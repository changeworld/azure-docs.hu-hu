---
title: Egyéni Linux-tároló konfigurálása
description: Megtudhatja, hogyan konfigurálhat egyéni Linux-tárolókat a Azure App Serviceban. Ez a cikk a leggyakoribb konfigurációs feladatokat ismerteti.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280143"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Egyéni Linux-tároló konfigurálása Azure App Servicehoz

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy egyéni Linux-tárolót, hogy Azure App Serviceon fusson.

Ez az útmutató a Linux-alkalmazások App Service-ben történő tárolókra bontás kapcsolatos főbb fogalmakat és útmutatásokat tartalmazza. Ha még soha nem használta Azure App Servicet, először kövesse az [Egyéni tároló](quickstart-docker-go.md) rövid [útmutatóját és az oktatóanyagot](tutorial-custom-docker-image.md) . A [multi-Container app](quickstart-multi-container.md) rövid [útmutatója és oktatóanyaga](tutorial-multi-container-app.md)is rendelkezésre áll.

## <a name="configure-port-number"></a>Portszám konfigurálása

Az egyéni rendszerképben található webkiszolgáló a 80-től eltérő portot is használhat. Tájékoztassa az Azure-t arról, hogy az egyéni tároló milyen portot használ a `WEBSITES_PORT` alkalmazás-beállítás használatával. A [jelen oktatóanyagban lévő Python-mintához](https://github.com/Azure-Samples/docker-django-webapp-linux) tartozó GitHub-oldalon az látható, hogy a `WEBSITES_PORT` értékét _8000_-re kell állítani. A Cloud Shell [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancs futtatásával állíthatja be. Például:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Környezeti változók konfigurálása

Az egyéni tároló olyan környezeti változókat használhat, amelyeket külsőleg kell megadni. A Cloud Shell [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancs futtatásával adhatja át azokat. Például:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Ez a módszer egytárolós alkalmazások vagy többtárolós alkalmazások esetén is működik, ahol a környezeti változók a *Docker-compose. YML* fájlban vannak megadva.

## <a name="use-persistent-shared-storage"></a>Állandó megosztott tároló használata

Az alkalmazás fájlrendszerében a */Home* Directory használatával megtarthatja a fájlokat az újraindítások között, és megoszthatja azokat a példányok között. Az alkalmazásban lévő `/home` lehetővé teszi, hogy a tároló alkalmazás hozzáférjen az állandó tárterülethez.

Ha az állandó tárterület le van tiltva, akkor a `/home` könyvtárba való írások nem maradnak meg az alkalmazás újraindítása vagy több példány között. Az egyetlen kivétel a Docker és a tároló naplóinak tárolására szolgáló `/home/LogFiles` könyvtár. Ha az állandó tárterület engedélyezve van, a `/home` könyvtárba való összes írás megmarad, és a kibővített alkalmazás összes példánya elérhetővé válik.

Alapértelmezés szerint az állandó tárterület *engedélyezve* van, és a beállítás nem érhető el az alkalmazás beállításaiban. A letiltásához állítsa be a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` alkalmazás beállítását [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancs futtatásával a Cloud shell. Például:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> [Saját állandó tárterületet is beállíthat](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>SSH engedélyezése

Az SSH lehetővé teszi a tároló és az ügyfél közötti biztonságos kommunikációt. Ahhoz, hogy egy egyéni tároló támogassa az SSH-t, fel kell vennie azt a Docker.

> [!TIP]
> Az összes beépített Linux-tároló hozzá lett adva az SSH-utasításokhoz a rendszerkép-tárházban. A [Node. js 10,14 adattárral](https://github.com/Azure-App-Service/node/blob/master/10.14) a következő utasításokat követve megtekintheti, hogyan engedélyezhető ott.

- A [futtatási](https://docs.docker.com/engine/reference/builder/#run) utasítással telepítse az SSH-kiszolgálót, és állítsa be a rendszergazdai fiók jelszavát `"Docker!"`. Az [alpesi Linux](https://hub.docker.com/_/alpine)-alapú rendszerképekhez például a következő parancsokat kell megadnia:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Ez a konfiguráció nem engedélyezi a külső kapcsolatokat a tárolóval. Az SSH csak `https://<app-name>.scm.azurewebsites.net` és a közzétételi hitelesítő adatokkal hitelesített hitelesítéssel érhető el.

- Adja hozzá [ezt a sshd_config fájlt](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) a rendszerkép-tárházhoz, és a [másolási](https://docs.docker.com/engine/reference/builder/#copy) utasítás használatával másolja a fájlt a */etc/ssh/* könyvtárba. *Sshd_config* fájlokról további információt az [OpenBSD dokumentációjában](https://man.openbsd.org/sshd_config)talál.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Az *sshd_config* fájlnak tartalmaznia kell a következő elemeket:
    > - A `Ciphers` beállításnak tartalmaznia kell legalább egy elemet a következő listából: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - A `MACs` beállításnak tartalmaznia kell legalább egy elemet a következő listából: `hmac-sha1,hmac-sha1-96`.

- A következő [utasítás használatával](https://docs.docker.com/engine/reference/builder/#expose) nyissa meg a tárolóban az 2222-es portot. Bár a gyökér jelszava ismert, a 2222-es port nem érhető el az internetről. A szolgáltatás csak a privát virtuális hálózat híd hálózatán lévő tárolók számára érhető el.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- A tároló indítási parancsfájljában indítsa el az SSH-kiszolgálót.

    ```bash
    /usr/sbin/sshd
    ```

    Példaként tekintse meg, hogyan indítja el az alapértelmezett [Node. js 10,14-tároló](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) az SSH-kiszolgálót.

## <a name="access-diagnostic-logs"></a>Diagnosztikai naplók elérése

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Többtárolós alkalmazások konfigurálása

- [Állandó tároló használata a Docker-összeállításban](#use-persistent-storage-in-docker-compose)
- [Előzetes verzió korlátozásai](#preview-limitations)
- [Docker-összeállítás beállításai](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Állandó tároló használata a Docker-összeállításban

A többtárolós alkalmazások, például a WordPress esetében állandó tárterületre van szükség a megfelelő működéshez. Az engedélyezéshez a Docker-összeállítás konfigurációjának a tárolón *kívüli* tárolási helyre kell mutatnia. A tárolón belüli tárolóhelyek nem tartanak fenn módosításokat az alkalmazás újraindítása után.

Engedélyezze az állandó tárterületet a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` alkalmazás beállításának beállításával az az [WebApp config appSettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) paranccsal a Cloud Shellban.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

A *Docker-compose. YML* fájlban rendelje hozzá a `${WEBAPP_STORAGE_HOME}`hoz a `volumes` lehetőséget. 

A `WEBAPP_STORAGE_HOME` egy környezeti változó az App Service szolgáltatásban, amely az alkalmazás állandó tárolójára mutat. Például:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Előzetes verzió korlátozásai

A multi-Container jelenleg előzetes verzióban érhető el. A következő App Service platform-funkciók nem támogatottak:

- Hitelesítés/engedélyezés
- Felügyelt identitások

### <a name="docker-compose-options"></a>Docker-összeállítás beállításai

Az alábbi listában a támogatott és nem támogatott Docker-összeállítási beállítások láthatók:

#### <a name="supported-options"></a>Támogatott beállítások

- command
- entrypoint
- environment
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Nem támogatott beállítások

- build (nem engedélyezett)
- depends_on (figyelmen kívül hagyva)
- networks (figyelmen kívül hagyva)
- secrets (figyelmen kívül hagyva)
- 80 és 8080 közötti (figyelmen kívül hagyott) portok

> [!NOTE]
> A rendszer figyelmen kívül hagyja a nem kifejezetten kinevezett egyéb beállításokat a nyilvános előzetes verzióban.

## <a name="configure-vnet-integration"></a>VNet-integráció konfigurálása

Ha egyéni tárolót használ a VNet-integrációval, további tároló-konfigurációra lehet szükség. Lásd: [az alkalmazás integrálása Azure-Virtual Networkokkal](../web-sites-integrate-with-vnet.md).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: üzembe helyezés Private Container adattárból](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Multi-Container WordPress-alkalmazás](tutorial-multi-container-app.md)
