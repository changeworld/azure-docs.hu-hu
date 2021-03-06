---
title: A Azure AD Domain Services hálózati tervezése és kapcsolatai | Microsoft Docs
description: Ismerkedjen meg a virtuális hálózat kialakításával kapcsolatos szempontokkal és a Azure Active Directory Domain Services futtatásakor a kapcsolathoz használt erőforrásokkal.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: e00ec8448739ac30950877a2ae196aa78cde750c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264192"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>A virtuális hálózat kialakításával kapcsolatos szempontok és a Azure AD Domain Services konfigurációs beállításai

Mivel a Azure Active Directory Domain Services (AD DS) hitelesítési és felügyeleti szolgáltatásokat biztosít más alkalmazások és munkaterhelések számára, a hálózati kapcsolat kulcsfontosságú összetevő. A megfelelően konfigurált virtuális hálózati erőforrások nélkül az alkalmazások és a munkaterhelések nem tudnak kommunikálni az Azure AD DS által biztosított szolgáltatásokkal, és nem használhatják azokat. Tervezze meg a virtuális hálózat követelményeit, és győződjön meg arról, hogy az Azure AD DS szükség szerint képes kiszolgálni az alkalmazásokat és a számítási feladatokat.

Ez a cikk a tervezési szempontokat és követelményeket ismerteti egy Azure-beli virtuális hálózat számára az Azure AD DS támogatásához.

## <a name="azure-virtual-network-design"></a>Azure Virtual Network – kialakítás

A hálózati kapcsolat biztosításához, valamint az alkalmazások és szolgáltatások Azure-AD DS általi hitelesítésének engedélyezéséhez Azure-beli virtuális hálózatot és alhálózatot kell használnia. Ideális esetben az Azure AD DSt a saját virtuális hálózatába kell telepíteni. A felügyeleti virtuális gép vagy a könnyű alkalmazások számítási feladatainak üzemeltetéséhez külön alkalmazás-alhálózatot is hozzáadhat ugyanabban a virtuális hálózatban. Egy különálló virtuális hálózat, amely az Azure AD DS Virtual Network szolgáltatáshoz kapcsolódó nagyobb vagy összetett alkalmazás-munkaterhelések esetében általában a legmegfelelőbb kialakítás. Más formatervezési minták is érvényesek, ha megfelel a virtuális hálózat és az alhálózat következő részeiben ismertetett követelményeknek.

Az Azure AD DS virtuális hálózatának tervezésekor az alábbi szempontokat kell figyelembe venni:

* Az Azure AD DS-t a virtuális hálózattal megegyező Azure-régióba kell telepíteni.
    * Jelenleg csak egy Azure AD DS felügyelt tartomány helyezhető üzembe Azure AD-bérlőn. Az Azure AD DS felügyelt tartomány egyetlen régióban van üzembe helyezve. Győződjön meg arról, hogy létrehoz vagy kijelöl egy virtuális hálózatot egy olyan [régióban, amely támogatja az Azure AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)-t.
* Vegye figyelembe a többi Azure-régió és az alkalmazás számítási feladatait üzemeltető virtuális hálózatok közelségét.
    * A késés csökkentése érdekében az alapvető alkalmazásai közel vagy ugyanabban a régióban legyenek, mint az Azure AD DS felügyelt tartományának virtuális hálózati alhálózata. Virtuális hálózati vagy virtuális magánhálózati (VPN) kapcsolatokat is használhat az Azure Virtual Networks között. Ezeket a kapcsolatbeállításokat a következő szakaszban tárgyaljuk.
* A virtuális hálózat nem hivatkozhat az Azure AD DS által biztosított DNS-szolgáltatásokra.
    * Az Azure AD DS saját DNS-szolgáltatást biztosít. A virtuális hálózatot ezen DNS-szolgáltatási címek használatára kell konfigurálni. A további névterek névfeloldása feltételes továbbítók használatával végezhető el.
    * Az egyéni DNS-kiszolgáló beállításai nem használhatók más DNS-kiszolgálókról, például virtuális gépekről érkező lekérdezések lekérdezéséhez. A virtuális hálózatban lévő erőforrásoknak az Azure AD DS által biztosított DNS-szolgáltatást kell használniuk.

> [!IMPORTANT]
> Az Azure AD DS nem helyezhető át másik virtuális hálózatra, miután engedélyezte a szolgáltatást.

Egy Azure AD DS felügyelt tartomány egy Azure-beli virtuális hálózatban lévő alhálózathoz csatlakozik. Tervezze meg ezt az alhálózatot az Azure AD DS számára a következő szempontokkal:

* Az Azure AD DS a saját alhálózatán kell telepíteni. Ne használjon meglévő alhálózatot vagy átjáró-alhálózatot.
* Egy hálózati biztonsági csoport jön létre egy Azure AD DS felügyelt tartomány központi telepítése során. Ez a hálózati biztonsági csoport a megfelelő szolgáltatási kommunikációhoz szükséges szabályokat tartalmazza.
    * Ne hozzon létre vagy használjon meglévő hálózati biztonsági csoportot a saját egyéni szabályaival.
* Az Azure AD DS 3-5 IP-címet igényel. Győződjön meg arról, hogy az alhálózat IP-címtartomány megadhatja a címek számát.
    * Az elérhető IP-címek korlátozása megakadályozza Azure AD Domain Services két tartományvezérlő fenntartását.

Az alábbi ábrán egy olyan érvényes kialakítás szerepel, amelyben az Azure AD DS saját alhálózattal rendelkezik, és a külső kapcsolathoz tartozik egy átjáró-alhálózat, és az alkalmazás munkaterhelései a virtuális hálózatban lévő csatlakoztatott alhálózaton találhatók:

![Javasolt alhálózat-kialakítás](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Csatlakozás az Azure AD DS Virtual networkhez

Ahogy az előző szakaszban is látható, csak egyetlen virtuális hálózatban hozhat létre Azure AD Domain Services felügyelt tartományt az Azure-ban, és az Azure AD-bérlőn belül csak egy felügyelt tartomány hozható létre. Ezen architektúra alapján előfordulhat, hogy egy vagy több olyan virtuális hálózatot kell összekapcsolnia, amely az alkalmazás számítási feladatait üzemelteti az Azure AD DS Virtual Network szolgáltatásban.

A következő módszerek egyikével kapcsolódhat más Azure-beli virtuális hálózatokban üzemeltetett alkalmazás-munkaterhelésekhez:

* Társviszony létesítése virtuális hálózatok között
* Virtuális magánhálózat (VPN)

### <a name="virtual-network-peering"></a>Társviszony létesítése virtuális hálózatok között

A virtuális hálózat társítása egy olyan mechanizmus, amely két virtuális hálózatot csatlakoztat az adott régióban az Azure gerinc hálózatán keresztül. A globális virtuális hálózati társítás az Azure-régiók közötti virtuális hálózat összekapcsolására is képes. A két virtuális hálózat a társítást követően lehetővé teszi, hogy az erőforrások (például a virtuális gépek) közvetlenül a magánhálózati IP-címek használatával kommunikáljanak egymással. A virtuális hálózati kapcsolatok használata lehetővé teszi, hogy egy Azure AD DS felügyelt tartományt helyezzen üzembe más virtuális hálózatokban üzembe helyezett alkalmazás-munkaterhelésekkel.

![Virtuális hálózati kapcsolat a peering használatával](./media/active-directory-domain-services-design-guide/vnet-peering.png)

További információ: az [Azure Virtual Network peering áttekintése](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking-vpn"></a>Virtuális magánhálózat (VPN)

A virtuális hálózatokat ugyanúgy összekapcsolhatja egy másik virtuális hálózattal (VNet – VNet), ahogyan a virtuális hálózatot egy helyszíni helyhez is konfigurálhatja. Mindkét kapcsolat VPN-átjárót használ egy biztonságos alagút létrehozásához az IPsec/IKE használatával. Ez a kapcsolati modell lehetővé teszi az Azure-AD DS üzembe helyezését egy Azure-beli virtuális hálózatban, majd a helyszíni helyek vagy más felhők csatlakoztatását.

![Virtuális hálózati kapcsolat VPN Gateway használatával](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

A virtuális magánhálózat használatával kapcsolatos további információkért olvassa el a [VNet-VNET VPN Gateway-kapcsolat konfigurálása a Azure Portal használatával](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)című témakört.

## <a name="name-resolution-when-connecting-virtual-networks"></a>Névfeloldás virtuális hálózatok csatlakoztatásakor

A Azure AD Domain Services virtuális hálózathoz csatlakoztatott virtuális hálózatok általában saját DNS-beállításokkal rendelkeznek. A virtuális hálózatok csatlakoztatásakor nem konfigurálja automatikusan a névfeloldást a Connected Virtual Network számára az Azure AD DS felügyelt tartomány által biztosított szolgáltatások feloldásához. A csatlakoztatott virtuális hálózatokon a névfeloldást úgy kell konfigurálni, hogy engedélyezze az alkalmazás munkaterhelését a Azure AD Domain Services megkereséséhez.

A névfeloldást engedélyezheti feltételes DNS-továbbítók használatával a Connecting Virtual Networks szolgáltatást támogató DNS-kiszolgálón, vagy az Azure AD tartományi szolgáltatás virtuális hálózatával megegyező DNS IP-címek használatával.

## <a name="network-resources-used-by-azure-ad-ds"></a>Az Azure AD DS által használt hálózati erőforrások

Egy Azure AD DS felügyelt tartomány létrehoz néhány hálózati erőforrást az üzembe helyezés során. Ezek az erőforrások szükségesek az Azure AD DS felügyelt tartományának sikeres működéséhez és kezeléséhez, és nem kell manuálisan konfigurálni.

| Azure-erőforrás                          | Leírás |
|:----------------------------------------|:---|
| Hálózati csatolókártya                  | Az Azure AD DS üzemelteti a felügyelt tartományt két tartományvezérlőn (DCs), amely Azure-beli virtuális gépekként fut a Windows Serveren. Minden virtuális gépnek van egy virtuális hálózati adaptere, amely csatlakozik a virtuális hálózati alhálózathoz. |
| Dinamikus normál nyilvános IP-cím      | Az Azure AD DS szabványos SKU nyilvános IP-cím használatával kommunikál a szinkronizálási és a felügyeleti szolgáltatással. A nyilvános IP-címekről további információt az [IP-címek típusai és a kiosztási módszerek az Azure-ban](../virtual-network/virtual-network-ip-addresses-overview-arm.md)című témakörben talál. |
| Azure standard Load Balancer            | Az Azure AD DS standard SKU Load balancert használ a hálózati címfordításhoz (NAT) és a terheléselosztáshoz (ha biztonságos LDAP-használatot használ). További információ az Azure Load balancerről: [Mi az Azure Load Balancer?](../load-balancer/load-balancer-overview.md) |
| Hálózati címfordítási (NAT) szabályok | Az Azure AD DS három NAT-szabályt hoz létre és használ a terheléselosztó számára – egy szabályt a biztonságos HTTP-forgalomhoz, és két szabályt a biztonságos PowerShell táveléréshez. |
| Terheléselosztó szabályai                     | Ha az Azure AD DS felügyelt tartománya biztonságos LDAP-ra van konfigurálva a 636-as TCP-porton, akkor három szabály jön létre, és a terheléselosztó használatával terjeszti a forgalmat. |

> [!WARNING]
> Ne törölje az Azure AD DS által létrehozott hálózati erőforrások egyikét sem. Ha törli valamelyik hálózati erőforrást, akkor az Azure AD DS szolgáltatás leáll.

## <a name="network-security-groups-and-required-ports"></a>Hálózati biztonsági csoportok és szükséges portok

A [hálózati biztonsági csoport (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) olyan szabályokat tartalmaz, amelyek engedélyezik vagy megtagadják egy Azure-beli virtuális hálózat forgalmának hálózati forgalmát. A hálózati biztonsági csoport az Azure AD DS telepítésekor jön létre, amely olyan szabályokat tartalmaz, amelyek lehetővé teszik a szolgáltatás számára a hitelesítési és felügyeleti funkciókat. Ez az alapértelmezett hálózati biztonsági csoport ahhoz a virtuális hálózati alhálózathoz van társítva, amelyhez az Azure AD DS felügyelt tartománya telepítve van.

A következő hálózati biztonsági csoportokra vonatkozó szabályok szükségesek az Azure AD DS számára a hitelesítési és felügyeleti szolgáltatások biztosításához. Ne szerkessze vagy törölje ezeket a hálózati biztonsági csoportokra vonatkozó szabályokat azon virtuális hálózati alhálózaton, amelyhez az Azure AD DS felügyelt tartománya telepítve van.

| Portszám | Protokoll | Forrás                             | Cél | Műveletek | Kötelező | Cél |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Bármelyik         | Engedélyezés  | Igen      | Szinkronizálás az Azure AD-Bérlővel. |
| 3389        | TCP      | CorpNetSaw                         | Bármelyik         | Engedélyezés  | Igen      | A tartomány kezelése. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Bármelyik         | Engedélyezés  | Igen      | A tartomány kezelése. |
| 636         | TCP      | Bármelyik                                | Bármelyik         | Engedélyezés  | Nem       | Csak a biztonságos LDAP (LDAPs) konfigurálásakor engedélyezett. |

> [!WARNING]
> Ne szerkessze kézzel ezeket a hálózati erőforrásokat és konfigurációkat. Ha hibásan konfigurált hálózati biztonsági csoportot vagy egy felhasználó által megadott útválasztási táblázatot társít az Azure AD DS üzembe helyezéséhez használt alhálózathoz, akkor megzavarhatja a Microsoft képességét a tartomány kiszolgálására és felügyeletére. Az Azure AD-bérlő és az Azure AD DS felügyelt tartománya közötti szinkronizálás is megszakad.
>
> A hálózati biztonsági csoport számára a *AllowVnetInBound*, a *AllowAzureLoadBalancerInBound*, a *DenyAllInBound*, a *AllowVnetOutBound*, a *AllowInternetOutBound*és a *DenyAllOutBound* alapértelmezett szabályai is léteznek. Ne szerkessze vagy törölje ezeket az alapértelmezett szabályokat.
>
> Az Azure SLA nem vonatkozik azokra az üzemelő példányokra, amelyekben nem megfelelően konfigurált hálózati biztonsági csoport és/vagy felhasználó által definiált útválasztási táblák lettek alkalmazva, amelyek blokkolják az Azure AD DS a tartomány frissítését és felügyeletét.

### <a name="port-443---synchronization-with-azure-ad"></a>443-es port – szinkronizálás az Azure AD-vel

* Az Azure AD-bérlő Azure AD DS felügyelt tartományhoz való szinkronizálására használatos.
* A porthoz való hozzáférés nélkül az Azure AD DS felügyelt tartománya nem tud szinkronizálni az Azure AD-Bérlővel. Előfordulhat, hogy a felhasználók nem tudnak bejelentkezni a jelszavuk módosításaival, nem lesznek szinkronizálva az Azure AD DS felügyelt tartományával.
* A port IP-címekre való bejövő hozzáférése alapértelmezés szerint a **AzureActiveDirectoryDomainServices** szolgáltatás címkével van korlátozva.
* Ne korlátozza a kimenő hozzáférést ebből a portból.

### <a name="port-3389---management-using-remote-desktop"></a>3389-es port – felügyelet a távoli asztal használatával

* Távoli asztali kapcsolatokhoz használatos az Azure AD DS felügyelt tartományában lévő tartományvezérlőkön.
* Az alapértelmezett hálózati biztonsági csoport szabály a *CorpNetSaw* szolgáltatás címkéjét használja a forgalom további korlátozására.
    * Ez a szolgáltatási címke csak a biztonságos hozzáférési munkaállomásokat engedélyezi a Microsoft vállalati hálózaton a távoli asztal használatához az Azure AD DS felügyelt tartományhoz.
    * A hozzáférés csak üzleti indoklással engedélyezett, például felügyeleti vagy hibaelhárítási helyzetekben.
* Ez a szabály beállítható a *Megtagadás*értékre, és csak *akkor állítható be, ha* szükséges. A legtöbb felügyeleti és figyelési feladat a PowerShell távelérés használatával történik. Az RDP-t csak abban a ritka eseményben használják, amelyet a Microsoftnak a felügyelt tartományhoz való távoli kapcsolódásra kell használnia a speciális hibaelhárítás érdekében.

> [!NOTE]
> Ha megpróbálja szerkeszteni a hálózati biztonsági csoport szabályát, manuálisan nem választhatja ki a *CorpNetSaw* szolgáltatás címkéjét a portálról. A *CorpNetSaw* szolgáltatás címkét használó szabályok manuális konfigurálásához Azure PowerShell vagy az Azure CLI-t kell használnia.

### <a name="port-5986---management-using-powershell-remoting"></a>5986-es port – felügyelet a PowerShell távoli eljáráshívás használatával

* Felügyeleti feladatok végrehajtásához használatos a PowerShell távelérési szolgáltatásával az Azure AD DS felügyelt tartományában.
* A porthoz való hozzáférés nélkül az Azure AD DS felügyelt tartománya nem frissíthető, konfigurálható, készíthető biztonsági másolat vagy figyelhető.
* A Resource Manager-alapú virtuális hálózatot használó Azure AD DS felügyelt tartományok esetében a porthoz való bejövő hozzáférést a *AzureActiveDirectoryDomainServices* szolgáltatás címkéjére korlátozhatja.
    * Az örökölt Azure AD DS felügyelt tartományok klasszikus virtuális hálózattal való használata esetén a porthoz való bejövő hozzáférést a következő forrás IP-címekre korlátozhatja: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*és *104.40.87.209*.

    > [!NOTE]
    > A (z) 2017-es verziójában Azure AD Domain Services elérhetővé vált a Azure Resource Manager hálózaton lévő gazdagép számára. Azóta egy biztonságosabb szolgáltatást hoztunk létre a Azure Resource Manager modern képességeinek használatával. Mivel Azure Resource Manager központi telepítések teljes mértékben lecserélik a klasszikus üzemelő példányokat, az Azure-AD DS a klasszikus virtuális hálózati telepítések 2023. március 1-től megszűnnek.
    >
    > További információkért lásd a [hivatalos elavult közleményt](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/) .

## <a name="user-defined-routes"></a>Felhasználó által megadott útvonalak

A felhasználó által megadott útvonalak alapértelmezés szerint nem jönnek létre, és nem szükségesek az Azure AD DS megfelelő működéséhez. Ha útválasztási táblákat kell használnia, kerülje a *0.0.0.0* útvonal módosításait. Az útvonal módosításai megszakítják Azure AD Domain Services, és nem támogatott állapotban helyezi el a felügyelt tartományt.

A bejövő forgalmat a megfelelő Azure-szolgáltatási címkékben található IP-címekről is át kell irányítani a Azure AD Domain Services alhálózatra. A szolgáltatással kapcsolatos címkékkel és a hozzájuk kapcsolódó IP-címmel kapcsolatos további információkért lásd: [Azure IP-tartományok és szolgáltatás-címkék – nyilvános felhő](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Ezek az Azure-adatközpontok IP-tartományai értesítés nélkül megváltoztathatók. Győződjön meg arról, hogy rendelkezik olyan folyamatokkal, amelyekkel ellenőrizheti a legújabb IP-címeket.

## <a name="next-steps"></a>Következő lépések

További információ az Azure AD DS által használt hálózati erőforrásokról és a kapcsolatok lehetőségeiről:

* [Azure-beli virtuális hálózati társítás](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN Gateway-átjárók](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure hálózati biztonsági csoportok](../virtual-network/security-overview.md)
