---
title: Ubuntu-alapú virtuális gép csatlakoztatása Azure AD Domain Serviceshoz | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat és csatlakoztathat egy Ubuntu Linux virtuális gépet egy Azure AD Domain Services felügyelt tartományhoz.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: bc5371ccbd3ba66117d5c613090b70ce7f07d51e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298841"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Ubuntu Linux virtuális gép csatlakoztatása Azure AD Domain Services felügyelt tartományhoz

Annak érdekében, hogy a felhasználók egyetlen hitelesítő adat használatával jelentkezzenek be a virtuális gépekre az Azure-ban, csatlakoztathatja a virtuális gépeket egy Azure Active Directory Domain Services (AD DS) felügyelt tartományhoz. Amikor egy virtuális gépet csatlakoztat egy Azure AD DS felügyelt tartományhoz, a tartomány felhasználói fiókjai és hitelesítő adatai használhatók a kiszolgálók bejelentkezéséhez és kezeléséhez. Az Azure AD DS felügyelt tartományból származó csoporttagságok a virtuális gépen található fájlokhoz vagy szolgáltatásokhoz való hozzáférés szabályozására is vonatkoznak.

Ez a cikk bemutatja, hogyan csatlakozhat egy Ubuntu Linux virtuális géphez egy Azure AD DS felügyelt tartományhoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, az első oktatóanyag [egy Azure Active Directory Domain Services példányt hoz létre és konfigurál][create-azure-ad-ds-instance].
* Egy olyan felhasználói fiók, amely az Azure AD DS felügyelt tartomány részét képezi.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Ubuntu Linux virtuális gép létrehozása és kapcsolódás

Ha rendelkezik egy meglévő Ubuntu Linux virtuális géppel az Azure-ban, csatlakozzon az SSH-val, majd folytassa a következő lépéssel a [virtuális gép konfigurálásának megkezdéséhez](#configure-the-hosts-file).

Ha létre kell hoznia egy Ubuntu Linux virtuális gépet, vagy létre szeretne hozni egy tesztelési virtuális gépet, amely a jelen cikkben használható, a következő módszerek egyikét használhatja:

* [Azure Portalra](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

A virtuális gép létrehozásakor ügyeljen arra, hogy a virtuális gép képes legyen kommunikálni az Azure AD DS felügyelt tartománnyal:

* Telepítse a virtuális gépet ugyanabba vagy egy olyan virtuális hálózatba, amelyben engedélyezte a Azure AD Domain Services.
* Telepítse a virtuális gépet egy másik alhálózatra, mint a Azure AD Domain Services példánya.

A virtuális gép üzembe helyezését követően hajtsa végre a lépéseket, hogy SSH-kapcsolaton keresztül csatlakozzon a virtuális géphez.

## <a name="configure-the-hosts-file"></a>A Hosts fájl konfigurálása

Győződjön meg arról, hogy a virtuális gép állomásneve helyesen van konfigurálva a felügyelt tartományhoz, szerkessze a */etc/hosts* fájlt, és állítsa be a hostname:

```console
sudo vi /etc/hosts
```

A *gazdagépek* fájlban frissítse a *localhost* -címeket. A következő példában:

* a *aaddscontoso.com* az Azure AD DS felügyelt tartományának DNS-tartományneve.
* az *Ubuntu* a felügyelt tartományhoz csatlakozó Ubuntu-alapú virtuális gép állomásneve.

Frissítse ezeket a neveket a saját értékeivel:

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

Ha elkészült, mentse és zárja be a *hosts* fájlt a szerkesztő `:wq` parancsának használatával.

## <a name="install-required-packages"></a>Szükséges csomagok telepítése

A virtuális gépnek szüksége van néhány további csomagra a virtuális gép Azure AD DS felügyelt tartományhoz való csatlakoztatásához. A csomagok telepítéséhez és konfigurálásához frissítse és telepítse a tartományhoz való csatlakozáshoz használt eszközöket `apt-get`

A Kerberos telepítésekor a *krb5* csomag minden nagybetűvel kéri a tartománynevet. Ha például az Azure AD DS felügyelt tartományának neve *aaddscontoso.com*, adja meg a *AADDSCONTOSO.com* tartományt. A telepítés a */etc/krb5.conf állományt* konfigurációs fájlban írja be a `[realm]` és `[domain_realm]` szakaszt. Győződjön meg arról, hogy a tartomány minden nagybetűvel rendelkezik:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>A Network Time Protocol (NTP) konfigurálása

Ahhoz, hogy a tartományi kommunikáció megfelelően működjön, az Ubuntu-alapú virtuális gép dátumának és időpontjának szinkronizálnia kell az Azure AD DS felügyelt tartománnyal. Adja hozzá az Azure AD DS felügyelt tartomány NTP-állomásnevét a */etc/ntp.conf* -fájlhoz.

1. Nyissa meg az *NTP. conf* fájlt egy szerkesztővel:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. Az *NTP. conf* fájlban hozzon létre egy sort az Azure AD DS felügyelt tartomány DNS-nevének hozzáadásához. A következő példában egy *aaddscontoso.com* bejegyzést adnak hozzá. Saját DNS-név használata:

    ```console
    server aaddscontoso.com
    ```

    Ha elkészült, mentse és zárja be az *NTP. conf* fájlt a szerkesztő `:wq` parancsának használatával.

1. A következő lépések szükségesek annak biztosításához, hogy a virtuális gép szinkronizálva legyen az Azure AD DS felügyelt tartományával:

    * Az NTP-kiszolgáló leállítása
    * A dátum és idő frissítése a felügyelt tartományból
    * Az NTP szolgáltatás elindítása

    Futtassa a következő parancsokat a lépések végrehajtásához. Használja a saját DNS-nevét a `ntpdate` paranccsal:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>Virtuális gép csatlakoztatása a felügyelt tartományhoz

Most, hogy a szükséges csomagok telepítve vannak a virtuális gépen, és az NTP konfigurálva van, csatlakoztassa a virtuális gépet az Azure AD DS felügyelt tartományhoz.

1. Az `realm discover` parancs használatával keresse fel az Azure AD DS felügyelt tartományt. A következő példa felfedi a *AADDSCONTOSO.com*tartományát. Adja meg saját Azure AD DS felügyelt tartománynevét az összes nagybetűvel:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Ha az `realm discover` parancs nem találja az Azure AD DS felügyelt tartományát, tekintse át a következő hibaelhárítási lépéseket:

    * Győződjön meg arról, hogy a tartomány elérhető a virtuális gépről. Próbálja meg `ping aaddscontoso.com` a pozitív válasz visszaadása.
    * Győződjön meg arról, hogy a virtuális gép üzembe helyezése ugyanarra a virtuális gépre történik, ahol az Azure AD DS felügyelt tartomány elérhető.
    * Győződjön meg arról, hogy a virtuális hálózat DNS-kiszolgálójának beállításai frissítve lettek, hogy az Azure AD DS felügyelt tartományának tartományvezérlőjére mutasson.

1. Most inicializálja a Kerberost a `kinit` parancs használatával. Olyan felhasználót kell megadnia, amely az Azure AD DS felügyelt tartomány részét képezi. Ha szükséges, [vegyen fel egy felhasználói fiókot egy csoportba az Azure ad-ben](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Ismét az Azure AD DS felügyelt tartománynevet minden nagybetűvel meg kell adni. A következő példában az `contosoadmin@aaddscontoso.com` nevű fiók a Kerberos inicializálására szolgál. Adja meg saját felhasználói fiókját, amely az Azure AD DS felügyelt tartomány része:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Végül csatlakoztassa a gépet az Azure AD DS felügyelt tartományhoz a `realm join` parancs használatával. Ugyanazt a felhasználói fiókot használja, mint amely az előző `kinit` parancsban megadott Azure AD DS felügyelt tartomány része, például `contosoadmin@AADDSCONTOSO.COM`:

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

Néhány percet vesz igénybe, hogy csatlakozzon a virtuális géphez az Azure AD DS felügyelt tartományhoz. A következő példa kimenete azt mutatja, hogy a virtuális gép sikeresen csatlakozott az Azure AD DS felügyelt tartományhoz:

```output
Successfully enrolled machine in realm
```

Ha a virtuális gép nem tudja sikeresen befejezni a tartományhoz való csatlakozás folyamatát, győződjön meg arról, hogy a virtuális gép hálózati biztonsági csoportja engedélyezi a kimenő Kerberos-forgalmat a 464-as TCP + UDP-porton az Azure AD DS felügyelt tartományának virtuális hálózati alhálózatán.

## <a name="update-the-sssd-configuration"></a>A SSSD konfigurációjának frissítése

Az előző lépésben telepített csomagok egyike a System Security Services Daemon (SSSD) volt. Amikor egy felhasználó tartományi hitelesítő adatokkal próbál bejelentkezni egy virtuális gépre, a SSSD továbbítja a kérést egy hitelesítési szolgáltatónak. Ebben az esetben a SSSD az Azure AD DS használatával hitelesíti a kérést.

1. Nyissa meg a *sssd. conf* fájlt egy szerkesztővel:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Tegye megjegyzésbe az *use_fully_qualified_names* sorát a következők szerint:

    ```console
    # use_fully_qualified_names = True
    ```

    Ha elkészült, mentse és zárja be a *sssd. conf* fájlt a szerkesztő `:wq` parancsával.

1. A módosítás alkalmazásához indítsa újra a SSSD szolgáltatást:

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>A felhasználói fiók és a csoport beállításainak konfigurálása

Ha a virtuális gép az Azure AD DS felügyelt tartományhoz van csatlakoztatva, és a hitelesítésre van konfigurálva, néhány felhasználói konfigurációs lehetőség is befejeződik. A konfigurációs változások közé tartozik a jelszó-alapú hitelesítés engedélyezése, valamint a helyi virtuális gépen lévő otthoni könyvtárak automatikus létrehozása, amikor a tartományi felhasználók először jelentkeznek be.

### <a name="allow-password-authentication-for-ssh"></a>Jelszó-hitelesítés engedélyezése SSH-hoz

Alapértelmezés szerint a felhasználók csak az SSH nyilvános kulcs-alapú hitelesítés használatával jelentkezhetnek be egy virtuális gépre. A jelszó-alapú hitelesítés meghiúsul. Amikor csatlakoztatja a virtuális gépet egy Azure AD DS felügyelt tartományhoz, a tartományi fiókoknak jelszó alapú hitelesítést kell használniuk. Frissítse az SSH-konfigurációt a jelszó alapú hitelesítés engedélyezéséhez az alábbiak szerint.

1. Nyissa meg a *sshd_conf* fájlt egy szerkesztővel:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. A *PasswordAuthentication* vonalának frissítése *Igen*értékre:

    ```console
    PasswordAuthentication yes
    ```

    Ha elkészült, mentse és zárja be a *sshd_conf* fájlt a szerkesztő `:wq` parancsával.

1. A módosítások alkalmazásához és a felhasználók jelszóval való bejelentkezéséhez indítsa újra az SSH-szolgáltatást:

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Az automatikus kezdőkönyvtár létrehozásának konfigurálása

Ha engedélyezni szeretné a kezdőkönyvtár automatikus létrehozását, amikor a felhasználó először jelentkezik be, hajtsa végre a következő lépéseket:

1. Nyissa meg a */etc/pam.d/Common-Session* fájlt egy szerkesztőben:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Adja hozzá a következő sort a fájlban a sor `session optional pam_sss.so`alatt:

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    Ha elkészült, mentse és zárja be a *Common-Session* fájlt a szerkesztő `:wq` parancsának használatával.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>A "HRE DC Administrators" csoport sudo-jogosultságának megadása

Ha a *HRE tartományvezérlő rendszergazdák* csoportjának tagjai számára rendszergazdai jogosultságokat kíván adni az Ubuntu virtuális gépen, vegyen fel egy bejegyzést a */etc/sudoers*. A Hozzáadás után a *HRE tartományvezérlő rendszergazdák* csoportjának tagjai az Ubuntu virtuális gépen a `sudo` parancsot használhatják.

1. Nyissa meg a *sudoers* fájlt a szerkesztéshez:

    ```console
    sudo visudo
    ```

1. Adja hozzá a következő bejegyzést a */etc/sudoers* fájl végéhez:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    Ha elkészült, mentse és zárja be a szerkesztőt a `Ctrl-X` parancs használatával.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Bejelentkezés a virtuális gépre tartományi fiók használatával

Annak ellenőrzéséhez, hogy a virtuális gép sikeresen csatlakozott-e az Azure AD DS felügyelt tartományhoz, indítson el egy új SSH-kapcsolódást egy tartományi felhasználói fiók használatával. Győződjön meg arról, hogy a kezdőkönyvtár létrejött, és a rendszer a tartományból származó csoporttagság alkalmazását alkalmazza.

1. Hozzon létre egy új SSH-kapcsolatokat a konzolon. Használjon olyan tartományi fiókot, amely a felügyelt tartományhoz tartozik a `ssh -l` parancs használatával, például `contosoadmin@aaddscontoso.com`, majd adja meg a virtuális gép (például *Ubuntu.aaddscontoso.com*) címeit. Ha a Azure Cloud Shell használja, a belső DNS-név helyett használja a virtuális gép nyilvános IP-címét.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
    ```

1. Ha sikeresen csatlakozott a virtuális géphez, ellenőrizze, hogy helyesen lett-e inicializálva a kezdőkönyvtár:

    ```console
    pwd
    ```

    A */Home* könyvtárban kell lennie a saját címtárával, amely megfelel a felhasználói fióknak.

1. Most győződjön meg arról, hogy a csoporttagságok megfelelően vannak feloldva:

    ```console
    id
    ```

    Az Azure AD DS felügyelt tartományból kell megjelennie a csoporttagságok.

1. Ha a *HRE DC-rendszergazdák* csoport tagjaként jelentkezett be a virtuális gépre, ellenőrizze, hogy megfelelően tudja-e használni a `sudo` parancsot:

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>Következő lépések

Ha problémái adódnak a virtuális gép Azure AD DS felügyelt tartományhoz való csatlakoztatásával vagy egy tartományi fiókkal való bejelentkezéssel kapcsolatban, olvassa el a [tartományhoz való csatlakozással kapcsolatos problémák elhárítása](join-windows-vm.md#troubleshoot-domain-join-issues)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
