---
title: SSH kulcspár létrehozásának részletes lépései
description: Megtudhatja, hogyan hozhat létre és kezelhet egy nyilvános és titkos SSH-kulcspárt a Linux rendszerű virtuális gépek számára az Azure-ban.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c34a88c39104d3af2c5747d1cd6d3dea6929379a
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969541"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Részletes lépések: SSH-kulcsok létrehozása és kezelése az Azure-beli linuxos virtuális gépek hitelesítéséhez 
A Secure Shell (SSH) kulcspár használatával létrehozhat egy Linux rendszerű virtuális gépet az Azure-ban, amely alapértelmezés szerint SSH-kulcsokat használ a hitelesítéshez, így nincs szükség a bejelentkezéshez szükséges jelszavakra. A Azure Portal, az Azure CLI, a Resource Manager-sablonok vagy más eszközök segítségével létrehozott virtuális gépek tartalmazhatják a nyilvános SSH-kulcsot az üzembe helyezés részeként, amely beállítja az SSH-kulcsos hitelesítést az SSH-kapcsolatokhoz. 

Ez a cikk részletes hátteret és lépéseket tartalmaz az SSH RSA nyilvános titkos kulcspár létrehozásához és kezeléséhez az SSH-ügyfélkapcsolatokhoz. Ha gyors parancsokat szeretne, tekintse meg a Linux rendszerű [virtuális gépekhez készült nyilvános SSH-kulcspár létrehozása az Azure-ban](mac-create-ssh-keys.md)című témakört.

Az SSH-kulcsok Windows-számítógépen való létrehozásával és használatával kapcsolatos további lehetőségekért lásd: [az ssh-kulcsok használata az Azure-ban Windowson](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Titkos kulcs jelszava
A titkos SSH-kulcsnak nagyon biztonságos jelszóval kell rendelkeznie a védelme érdekében. Ez a jelszó csak a titkos SSH-kulcsfájl elérésére szolgál, és *nem* a felhasználói fiók jelszava. Ha jelszót ad hozzá az SSH-kulcshoz, az titkosítja a titkos kulcsot a 128 bites AES használatával, hogy a titkos kulcs használhatatlan legyen a jelszó visszafejtése nélkül. Ha egy támadó ellopta a titkos kulcsot, és a kulcs nem rendelkezik hozzáférési kóddal, a titkos kulcs használatával bejelentkezhet bármely olyan kiszolgálóra, amely rendelkezik a megfelelő nyilvános kulccsal. Ha a titkos kulcsot jelszó védi, azt nem használhatja a támadó, így további biztonsági réteget biztosíthat az Azure-beli infrastruktúra számára.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Az SSH-kulcsok használata és a használat előnyei

Amikor létrehoz egy Azure-beli virtuális gépet a nyilvános kulcs megadásával, az Azure a nyilvános kulcsot (a `.pub` formátumban) a virtuális gép `~/.ssh/authorized_keys` mappájába másolja. A `~/.ssh/authorized_keys` SSH-kulcsainak használatával megtámadhatja az ügyfelet, hogy az megfeleljen a megfelelő titkos kulcsnak egy SSH-kapcsolatban. Az SSH-kulcsokat használó Azure Linux rendszerű virtuális gépeken az Azure úgy konfigurálja az SSHD-kiszolgálót, hogy ne engedélyezze a jelszavas bejelentkezést, csak az SSH-kulcsokat. Ezért az SSH-kulcsokkal rendelkező Azure Linux rendszerű virtuális gépek létrehozásával biztonságosabbá teheti a virtuális gépek üzembe helyezését, és megtakaríthatja a szokásos üzembe helyezés utáni konfigurációs lépést, amely letiltja a jelszavakat a `sshd_config` fájlban.

Ha nem kíván SSH-kulcsokat használni, beállíthatja a linuxos virtuális gépet a jelszó-hitelesítés használatára. Ha a virtuális gép nem érhető el az interneten, akkor a jelszavak használata elegendő lehet. Azonban továbbra is szükség van az egyes Linux-alapú virtuális gépek jelszavainak kezelésére, valamint az egészséges jelszóházirendek és eljárások fenntartására, például a jelszó minimális hosszára és a rendszeres frissítésekre. Az SSH-kulcsok használata csökkenti az egyes hitelesítő adatok több virtuális gépen történő kezelésének bonyolultságát.

## <a name="generate-keys-with-ssh-keygen"></a>Kulcsok generálása ssh-keygen használatával

A kulcsok létrehozásához egy előnyben részesített parancs `ssh-keygen`, amely a Azure Cloud Shell, macOS vagy Linux rendszerű gazdagépen, a [Linux Windows alrendszerén](https://docs.microsoft.com/windows/wsl/about)és más eszközökön elérhető OpenSSH segédprogramokkal érhető el. `ssh-keygen` kérdéseket tesz fel, majd egy titkos kulcsot és egy megfelelő nyilvános kulcsot ír. 

Az SSH-kulcsokat alapértelmezés szerint a `~/.ssh` könyvtár tárolja.  Ha Ön nem rendelkezik `~/.ssh` könyvtárral, akkor az `ssh-keygen` parancs létrehozza azt a megfelelő engedélyekkel.

### <a name="basic-example"></a>Alapszintű példa

A következő `ssh-keygen` parancs alapértelmezés szerint 2048 bites SSH RSA nyilvános és titkos kulcs fájlokat hoz létre a `~/.ssh` könyvtárban. Ha egy SSH-kulcspár létezik az aktuális helyen, a rendszer felülírja ezeket a fájlokat.

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

### <a name="detailed-example"></a>Részletes példa
Az alábbi példa további parancssori lehetőségeket mutat be egy SSH RSA kulcspár létrehozásához. Ha egy SSH-kulcspár létezik az aktuális helyen, a rendszer felülírja ezeket a fájlokat. 

```bash
ssh-keygen \
    -m PEM \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**A parancs ismertetése**

`ssh-keygen` = a kulcsok létrehozásához használt program,

`-m PEM` = a kulcs formázása PEMként

`-t rsa` = a létrehozandó kulcs típusa, ebben az esetben RSA formátumban

`-b 4096` = a kulcsban található bitek száma, ebben az esetben 4096

`-C "azureuser@myserver"` = a nyilvános kulcsfájl végéhez fűzött megjegyzés az egyszerű azonosítás érdekében. A rendszer általában egy e-mail-címet használ a megjegyzésként, de a legmegfelelőbb megoldást használja az infrastruktúra számára.

`-f ~/.ssh/mykeys/myprivatekey` = a titkos kulcsfájl fájlnevét, ha úgy dönt, hogy nem használja az alapértelmezett nevet. A `.pub`-vel hozzáfűzött megfelelő nyilvánoskulcs-fájl ugyanabban a címtárban jön létre. A könyvtárnak léteznie kell.

`-N mypassphrase` = a titkos kulcs fájljának eléréséhez használt további jelszó. 

### <a name="example-of-ssh-keygen"></a>Ssh-keygen – példa

```bash
ssh-keygen -t -m PEM rsa -b 4096 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:vFfHHrpSGQBd/oNdvNiX0sG9Vh+wROlZBktNZw9AUjA azureuser@myserver
The key's randomart image is:
+---[RSA 4096]----+
|        .oE=*B*+ |
|          o+o.*++|
|           .oo++*|
|       .    .B+.O|
|        S   o=BO.|
|         . .o++o |
|        . ... .  |
|         ..  .   |
|           ..    |
+----[SHA256]-----+
```

#### <a name="saved-key-files"></a>Kulcsfájl mentve

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

A cikkben használt kulcspár neve. `id_rsa` nevű kulcspár az alapértelmezett; Előfordulhat, hogy egyes eszközök megvárhatják az `id_rsa` titkos kulcs fájlnevét, így egy jó ötlet. A `~/.ssh/` könyvtár az SSH-kulcspárok és az SSH konfigurációs fájl alapértelmezett helye. Ha nincs megadva a teljes elérési útvonal, az `ssh-keygen` az aktuális munkakönyvtárban hozza létre a kulcsokat, és nem az alapértelmezett `~/.ssh` könyvtárban.

#### <a name="list-of-the-ssh-directory"></a>A `~/.ssh` könyvtárának listája

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Kulcs jelszava

`Enter passphrase (empty for no passphrase):`

*Javasoljuk* , hogy adjon hozzá egy hozzáférési kódot a titkos kulcshoz. A kulcsfájl védelmét biztosító hozzáférési kód nélkül a fájllal bárki bejelentkezhet bármely olyan kiszolgálóra, amely rendelkezik a megfelelő nyilvános kulccsal. A jelszó hozzáadása nagyobb védelmet nyújt arra az esetre, ha valaki hozzáfér a titkos kulcsfájl eléréséhez, így időt biztosít a kulcsok módosítására.

## <a name="generate-keys-automatically-during-deployment"></a>Kulcsok automatikus generálása az üzembe helyezés során

Ha az [Azure CLI](/cli/azure) -t használja a virtuális gép létrehozásához, a nyilvános és a titkos kulcs fájljait is létrehozhatja az az [VM create](/cli/azure/vm) paranccsal a `--generate-ssh-keys` kapcsolóval. A kulcsokat a ~/.ssh könyvtárban tárolja a rendszer. Vegye figyelembe, hogy ez a parancs nem írja felül a kulcsokat, ha azok már léteznek az adott helyen.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Nyilvános SSH-kulcs megadása virtuális gép telepítésekor

SSH-kulcsokat használó Linux rendszerű virtuális gép létrehozásához adja meg az SSH nyilvános kulcsát, amikor a Azure Portal, a CLI, a Resource Manager-sablonok vagy más módszerek használatával hozza létre a virtuális gépet. A portál használata esetén magát a nyilvános kulcsot kell megadnia. Ha az [Azure CLI](/cli/azure) használatával hozza létre a virtuális gépet egy meglévő nyilvános kulccsal, adja meg a nyilvános kulcs értékét vagy helyét úgy, hogy az az [VM Create](/cli/azure/vm) parancsot futtatja a `--ssh-key-value` kapcsolóval. 

Ha nem ismeri a nyilvános SSH-kulcs formátumát, a következőképpen tekintheti meg a nyilvános kulcsot `cat` futtatásával: az `~/.ssh/id_rsa.pub` a saját nyilvános kulcsú fájljának helyére való lecserélésével:

```bash
cat ~/.ssh/id_rsa.pub
```

A kimenet a következőhöz hasonló (itt kitakarva):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Ha a nyilvános kulcs fájljának tartalmát a Azure Portal vagy egy Resource Manager-sablonba másolja és beilleszti, ügyeljen arra, hogy ne másoljon további szóközöket, vagy hozzon be további sortöréseket. Ha például a macOS-t használja, a nyilvános kulcs fájlját (alapértelmezés szerint `~/.ssh/id_rsa.pub`) áthelyezheti a **pbcopy** a tartalom másolásához (más linuxos programok is megegyeznek, mint például a `xclip`).

Ha egy többsoros formátumú nyilvános kulcsot szeretne használni, lévő RFC4716 formázott kulcsot hozhat létre egy PEM-tárolóban a korábban létrehozott nyilvános kulcsból.

RFC4716 formátumú kulcs meglévő nyilvános SSH-kulcsból történő létrehozása:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH-t a virtuális géphez SSH-ügyféllel
Az Azure-beli virtuális gépen üzembe helyezett nyilvános kulccsal és a helyi rendszeren lévő titkos kulccsal, SSH-val a virtuális géphez a virtuális gép IP-címét vagy DNS-nevét használva. Cserélje le az *azureuser* és a *myvm.westus.cloudapp.Azure.com* parancsot a következő parancsra a rendszergazdai felhasználónévvel és a teljes TARTOMÁNYNÉVVEL (vagy IP-címmel):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha a kulcspár létrehozásakor jelszót adott meg, írja be a jelszót, amikor a rendszer a bejelentkezési folyamat során kéri. (A rendszer hozzáadja a kiszolgálót a `~/.ssh/known_hosts` mappájához, és nem kér ismételt csatlakozást, amíg a nyilvános kulcs Azure-beli virtuális gépén nem változik, vagy amíg a kiszolgálónevet nem törlik a `~/.ssh/known_hosts` helyről.)

Ha a virtuális gép az igény szerinti hozzáférési szabályzatot használja, a virtuális géphez való kapcsolódáshoz a hozzáférést kell kérnie. Az igény szerinti szabályzattal kapcsolatos további információkért lásd: [virtuális gépek hozzáférésének kezelése az igény szerinti házirend használatával](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>A titkos kulcs jelszavainak tárolása az ssh-agent használatával

Ha nem szeretné beírni a titkos kulcsfájl jelszavát minden SSH-bejelentkezéskor, a `ssh-agent` segítségével gyorsítótárazhatja a titkos kulcsfájl jelszavát. Ha Mac számítógépet használ, a macOS kulcstartó biztonságosan tárolja a titkos kulcs jelszavát, amikor meghívja a `ssh-agent`.

Ellenőrizze és használja `ssh-agent` és `ssh-add`, hogy tájékoztassa az SSH-rendszert a kulcsokról, hogy ne kelljen interaktívan használnia a jelszót.

```bash
eval "$(ssh-agent -s)"
```

Ezután adja hozzá a titkos kulcsot az `ssh-agent` ügynökhöz az `ssh-add` paranccsal.

```bash
ssh-add ~/.ssh/id_rsa
```

A titkos kulcs jelszava jelenleg `ssh-agent`ban van tárolva.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>A kulcs másolása egy meglévő virtuális gépre az SSH-Copy-ID használatával
Ha már létrehozott egy virtuális gépet, a következőhöz hasonló paranccsal telepítheti az új nyilvános SSH-kulcsot a Linux rendszerű virtuális gépre:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>SSH konfigurációs fájl létrehozása és konfigurálása

Létrehozhat és konfigurálhat egy SSH-konfigurációs fájlt (`~/.ssh/config`) a bejelentkezések felgyorsításához és az SSH-ügyfél viselkedésének optimalizálásához. 

Az alábbi példa egy egyszerű konfigurációt mutat be, amely segítségével gyorsan bejelentkezhet felhasználóként egy adott virtuális gépre az alapértelmezett SSH titkos kulccsal. 

### <a name="create-the-file"></a>A fájl létrehozása

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>A fájl szerkesztése az új SSH-konfiguráció hozzáadásához

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Konfigurációs példa

Adja meg a gazdagép virtuális gépe számára megfelelő konfigurációs beállításokat.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

További gazdagépekhez további konfigurációkat adhat hozzá, amelyek lehetővé teszik, hogy mindegyik a saját dedikált kulcspárt használja. További speciális konfigurációs beállítások: [SSH-konfigurációs fájl](https://www.ssh.com/ssh/config/) .

Most, hogy rendelkezik egy SSH-kulcspár és egy konfigurált SSH konfigurációs fájllal, gyorsan és biztonságosan tud bejelentkezni a linuxos virtuális gépre. A következő parancs futtatásakor az SSH megkeresi és betölti az SSH konfigurációs fájl `Host myvm` blokkjának beállításait.

```bash
ssh myvm
```

Amikor először jelentkezik be egy kiszolgálóra SSH-kulccsal, a parancs felszólítja a kulcshoz tartozó jelszó megadására.

## <a name="next-steps"></a>Következő lépések

Ezután létre kell hoznia az Azure Linux virtuális gépeket az új nyilvános SSH-kulcs használatával. Azok az Azure-beli virtuális gépek, amelyek nyilvános SSH-kulccsal lettek létrehozva, mint a bejelentkezés, jobban biztonságosak, mint az alapértelmezett bejelentkezési módszerrel létrehozott virtuális gépek, a jelszavak.

* [Linuxos virtuális gép létrehozása a Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux rendszerű virtuális gép létrehozása az Azure CLI-vel](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linuxos virtuális gép létrehozása Azure-sablon használatával](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
