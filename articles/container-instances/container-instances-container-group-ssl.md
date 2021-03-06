---
title: Az SSL engedélyezése az oldalkocsi-tárolóval
description: Hozzon létre egy SSL-vagy TLS-végpontot egy Azure Container Instanceson futó Container Group számára egy oldalkocsi-tárolóban való futtatásával
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 524e997cf6c7c464cc352048b1abf4be119d2f37
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77460552"
---
# <a name="enable-an-ssl-endpoint-in-a-sidecar-container"></a>SSL-végpont engedélyezése oldalkocsi-tárolóban

Ez a cikk bemutatja, hogyan hozhat létre egy [tároló csoportot](container-instances-container-groups.md) egy alkalmazás-tárolóval és egy SSL-szolgáltatót futtató oldalkocsi-tárolóval. Egy különálló SSL-végponttal rendelkező tároló csoport beállításával az alkalmazás kódjának módosítása nélkül engedélyezheti az SSL-kapcsolatokat az alkalmazáshoz.

Létrehozhat egy két tárolóból álló példa típusú tároló csoportot:
* Egy alkalmazás-tároló, amely egy egyszerű webalkalmazást futtat a nyilvános Microsoft [ACI-HelloWorld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) rendszerkép használatával. 
* A nyilvános [Nginx](https://hub.docker.com/_/nginx) -rendszerképet futtató oldalkocsi-tároló, amely SSL használatára van konfigurálva. 

Ebben a példában a Container Group csak a 443-es portot teszi elérhetővé az Nginx nyilvános IP-címével. Az Nginx a HTTPS-kérelmeket a Companion-webalkalmazáshoz irányítja, amely belsőleg figyeli a 80-es porton. A példát a más portokat figyelő tároló-alkalmazások esetében is módosíthatja. 

A [következő lépésekben](#next-steps) talál további lépéseket az SSL egy tároló csoportban való engedélyezéséhez.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A cikk végrehajtásához használhatja az Azure CLI Azure Cloud Shell vagy helyi telepítését is. Ha helyileg szeretné használni, a 2.0.55 vagy újabb verzió használata javasolt. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Az Nginx SSL-szolgáltatóként való beállításához SSL-tanúsítványra van szükség. Ez a cikk bemutatja, hogyan hozhat létre és állíthat be egy önaláírt SSL-tanúsítványt. Termelési forgatókönyvek esetén tanúsítványt kell beszereznie a hitelesítésszolgáltatótól.

Önaláírt SSL-tanúsítvány létrehozásához használja a Azure Cloud Shell és számos Linux-disztribúcióban elérhető [OpenSSL](https://www.openssl.org/) eszközt, vagy használjon egy hasonló ügyfélprogramot az operációs rendszerben.

Először hozzon létre egy tanúsítványkérelmet (. CSR fájlt) egy helyi munkakönyvtárban:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Az azonosítási adatok hozzáadásához kövesse az utasításokat. A köznapi név mezőben adja meg a tanúsítványhoz társított állomásnevet. Ha a rendszer jelszót kér, nyomja le az ENTER billentyűt gépelés nélkül, ha ki szeretné hagyni a jelszó hozzáadását.

A következő parancs futtatásával hozza létre az önaláírt tanúsítványt (. CRT-fájlt) a tanúsítványkérelem alapján. Például:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Ekkor három fájlnak kell megjelennie a könyvtárban: a tanúsítványkérelem (`ssl.csr`), a titkos kulcs (`ssl.key`) és az önaláírt tanúsítvány (`ssl.crt`). A későbbi lépésekben `ssl.key` és `ssl.crt`eket használ.

## <a name="configure-nginx-to-use-ssl"></a>Az Nginx konfigurálása SSL használatára

### <a name="create-nginx-configuration-file"></a>Nginx konfigurációs fájl létrehozása

Ebben a szakaszban egy konfigurációs fájlt hoz létre az Nginx használatára az SSL használatához. Először másolja az alábbi szöveget egy `nginx.conf`nevű új fájlba. A Azure Cloud Shell a Visual Studio Code használatával hozhatja létre a fájlt a munkakönyvtárában:

```console
code nginx.conf
```

A `location`ban ügyeljen arra, hogy a megfelelő portot adja meg az alkalmazáshoz `proxy_pass`. Ebben a példában az 80-es portot a `aci-helloworld` tárolóhoz állítjuk be.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64 – titkos kódok és konfigurációs fájl kódolása

Base64 – kódolja az Nginx konfigurációs fájlját, az SSL-tanúsítványt és az SSL-kulcsot. A következő szakaszban megadhatja a kódolt tartalmakat egy YAML-fájlban, amelyet a tároló csoport telepítéséhez használ.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Tároló csoportjának üzembe helyezése

Most telepítse a tároló csoportot egy [YAML-fájlban](container-instances-multi-container-yaml.md)lévő tároló-konfigurációk megadásával.

### <a name="create-yaml-file"></a>YAML-fájl létrehozása

Másolja az alábbi YAML egy `deploy-aci.yaml`nevű új fájlba. A Azure Cloud Shell a Visual Studio Code használatával hozhatja létre a fájlt a munkakönyvtárában:

```console
code deploy-aci.yaml
```

Adja meg az `secret`alatt jelzett Base64 kódolású fájlok tartalmát. Például `cat` minden Base64 kódolású fájlt, hogy megtekintse a tartalmát. Az üzembe helyezés során ezeket a fájlokat a rendszer hozzáadja a tároló csoportban található [titkos kötethez](container-instances-volume-secret.md) . Ebben a példában a titkos kötet az Nginx-tárolóhoz van csatlakoztatva.

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>A tároló csoport üzembe helyezése

Hozzon létre egy erőforráscsoportot az az [Group Create](/cli/azure/group#az-group-create) paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Telepítse a tároló csoportot az az [Container Create](/cli/azure/container#az-container-create) paranccsal, és adja át a YAML-fájlt argumentumként.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Központi telepítési állapot megtekintése

A központi telepítés állapotának megtekintéséhez használja a következőt az [Container show](/cli/azure/container#az-container-show) paranccsal:

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Sikeres telepítés esetén a kimenet a következőhöz hasonló:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>SSL-kapcsolat ellenőrzése

A böngésző segítségével navigáljon a Container Group nyilvános IP-címére. Az ebben a példában látható IP-cím `52.157.22.76`, így az URL-cím **https://52.157.22.76** . Az Nginx-kiszolgáló konfigurációja miatt a futó alkalmazás megtekintéséhez a HTTPS protokollt kell használnia. HTTP-kapcsolaton keresztüli sikertelen kapcsolódási kísérlet.

![Képernyőkép a böngészőről, ahol egy Azure-tárolópéldányban futó alkalmazás látható](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Mivel ez a példa önaláírt tanúsítványt használ, és nem egy hitelesítésszolgáltatótól, a böngésző biztonsági figyelmeztetést jelenít meg, amikor HTTPS-kapcsolaton keresztül csatlakozik a webhelyhez. Előfordulhat, hogy el kell fogadnia a figyelmeztetést, vagy módosítania kell a böngésző vagy a tanúsítvány beállításait a lapra való továbblépéshez. Ez várt működés.

>

## <a name="next-steps"></a>Következő lépések

Ez a cikk bemutatja, hogyan állíthat be egy Nginx-tárolót, amely lehetővé teszi az SSL-kapcsolatok használatát a Container csoportban futó webalkalmazások számára. Ezt a példát olyan alkalmazásokhoz igazíthatja, amelyek a 80-es porton kívül is figyelik a portokat. Az Nginx konfigurációs fájlját úgy is frissítheti, hogy automatikusan átirányítsa a kiszolgálói kapcsolatokat a 80-as porton (HTTP) a HTTPS használatára.

Habár ez a cikk a Nginx-et használja az oldalkocsiban, használhat egy másik SSL-szolgáltatót, például a [Caddy](https://caddyserver.com/)-t.

Ha egy Azure-beli [virtuális hálózatban](container-instances-vnet.md)helyezi üzembe a tároló csoportot, megtekintheti azokat a beállításokat, amelyek lehetővé teszik az SSL-végpontok használatát a háttérbeli tárolók példányaihoz, beleértve a következőket:

* [Azure Functions-proxyk](../azure-functions/functions-proxies.md)
* [Azure API Management](../api-management/api-management-key-concepts.md)
* [Azure Application Gateway](../application-gateway/overview.md) – tekintse meg a minta- [telepítési sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet).
