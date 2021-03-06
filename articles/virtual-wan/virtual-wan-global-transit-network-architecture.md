---
title: 'Architektúra: globális átviteli hálózati architektúra'
titleSuffix: Azure Virtual WAN
description: Ismerje meg a Virtual WAN-hoz készült globális átviteli hálózati architektúrát
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 17d0e678008c76da32f20562aa795e83e49c80e4
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064971"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Globális átviteli hálózati architektúra és virtuális WAN

A modern vállalatok a felhőben és a helyszíni környezetekben a Hyper-elosztott alkalmazások, az adatközpontok és a felhasználók közötti mindennapos kapcsolatokat igénylik. A vállalatok a globális átviteli hálózati architektúrát a felhőalapú modern, globális vállalati IT-lábnyom megszilárdítására, összekapcsolására és szabályozására használják.

A globális átviteli hálózat architektúrája egy olyan klasszikus sugaras kapcsolati modellen alapul, ahol a felhőben üzemeltetett hálózat "hub" lehetővé teszi az olyan végpontok közötti tranzitív kapcsolódást, amelyek különböző típusú "küllők" között terjeszthetők.

Ebben a modellben a küllő a következő lehet:
* Virtuális hálózat (virtuális hálózatok)
* Fizikai ág helye
* Távoli felhasználó
* Internet

![hub és küllő](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**1. ábra: globális árutovábbítási hub és küllős hálózat**

Az 1. ábrán a globális átviteli hálózat logikai nézete látható, ahol a földrajzilag elosztott felhasználók, fizikai helyek és virtuális hálózatok a felhőben üzemeltetett hálózati elosztón keresztül kapcsolódnak egymáshoz. Ez az architektúra lehetővé teszi a logikai egyugrásos átviteli kapcsolatok használatát a hálózati végpontok között.

## <a name="globalnetworktransit"></a>Globális átviteli hálózat virtuális WAN-kapcsolattal

Az Azure Virtual WAN egy Microsoft által felügyelt felhőalapú hálózati szolgáltatás. A szolgáltatás által alkotott összes hálózati összetevőt a Microsoft üzemelteti és kezeli. A virtuális WAN-ról további információt a [virtuális WAN áttekintése](virtual-wan-about.md) című cikkben talál.

Az Azure Virtual WAN lehetővé teszi a globális átviteli hálózatok architektúráját azáltal, hogy a virtuális hálózatok, a fiókirodákban, az SaaS-és a Pásti-alkalmazásokban, valamint a felhasználókon elérhető, globálisan elosztott Felhőbeli számítási feladatok teljes körű és bármilyen módon elérhető kapcsolatát engedélyezi.

![Azure Virtual WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**2. ábra: globális tranzit hálózat és virtuális WAN**

Az Azure Virtual WAN architektúrában a virtuális WAN-hubok az Azure-régiókban vannak kiépítve, amelyekhez az ágakat, a virtuális hálózatok és a távoli felhasználókat is összekapcsolhatjuk. A fizikai fiókirodák prémium szintű ExpressRoute vagy helyek közötti VPN-kapcsolattal csatlakoznak a központhoz, a virtuális hálózatok a VNet-kapcsolatok segítségével csatlakoznak a központhoz, a távoli felhasználók pedig közvetlenül kapcsolódhatnak a hubhoz a felhasználó VPN-je (pont – hely VPN) használatával. A Virtual WAN támogatja a régiók közötti VNet kapcsolatot is, ahol az egyik régióban lévő VNet egy másik régióban lévő virtuális WAN-hubhoz is csatlakoztathatók.

Létrehozhat egy virtuális WAN-t úgy, hogy létrehoz egy virtuális WAN-központot a régiójában, amely a legnagyobb számú küllőt (ágakat, virtuális hálózatok, felhasználókat) és a más régiókban lévő küllőket csatlakoztatja a hubhoz. Ez jó megoldás, ha egy nagyvállalati lábnyom főleg egy régióban, néhány távoli küllővel.  
  
## <a name="hubtohub"></a>Központ – központ kapcsolat

A vállalati Felhőbeli lábnyom több Felhőbeli régióra is kiterjed, és az optimális (késés-Wise) a felhőnek a fizikai helyükhöz és a felhasználókhoz legközelebb eső régióból való elérésére szolgál. A globális átviteli hálózati architektúra egyik kulcsfontosságú alapelve a régiók közötti kapcsolat engedélyezése a Felhőbeli és a helyszíni hálózati végpontok között. Ez azt jelenti, hogy egy adott régióban a felhőhöz csatlakoztatott ágakból érkező forgalom egy másik régióban is elérheti az [Azure globális hálózata](https://azure.microsoft.com/global-infrastructure/global-network/)által engedélyezett, a csomópontok közötti kapcsolatot használó régiókat vagy VNet.

![régiók közötti](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**3. ábra: virtuális WAN régiók közötti kapcsolat**

Ha egy virtuális WAN-ban több hub is engedélyezve van, a hubok automatikusan csatlakoznak a hub – hub kapcsolatokon keresztül, így lehetővé téve a globális kapcsolódást a több régióban elosztott ágak és virtuális hálózatok között. 

Emellett a virtuális WAN összes részét képező hubok különböző regionális hozzáférési és biztonsági házirendekhez társíthatók. További információ: [biztonsági és házirend-vezérlés](#security) a cikk későbbi részében.

## <a name="anytoany"></a>Bármilyen kapcsolat

A globális átviteli hálózati architektúra a virtuális WAN-hubokon keresztül bármilyen kapcsolódást tesz lehetővé. Ez az architektúra kiküszöböli vagy csökkenti a küllők közötti teljes rácsvonal vagy részleges háló kapcsolat szükségességét, amelyek összetettebbek a létrehozáshoz és a karbantartáshoz. Emellett a hub-és küllős és a mesh hálózatok útválasztási vezérlése könnyebben konfigurálható és kezelhető.

Bármely – bármely kapcsolat (globális architektúra kontextusában) lehetővé teszi, hogy a vállalat globálisan elosztott felhasználókkal, ágakkal, adatközpontokkal, virtuális hálózatok és alkalmazásokkal kapcsolódjon egymáshoz az "árutovábbítási" központ (ok) n keresztül. Az Azure Virtual WAN globális árutovábbítási rendszerrel működik.

![bármilyen](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**4. ábra: virtuális WAN-forgalom elérési útjai**

Az Azure Virtual WAN a következő globális átviteli csatlakozási útvonalakat támogatja. A zárójelben lévő betűk a 4. ábrán láthatók.

* Ág – VNet (a)
* Ág – ág (b)
  * ExpressRoute Global Reach és virtuális WAN
* Távoli felhasználó – VNet (c)
* Távoli felhasználó – ág (d)
* VNet – VNet (e)
* Ág – központ – hub – ág (f)
* Ág – központ – hub – VNet (g)
* VNet – hub-VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Ág – VNet (a) és ág – VNet közötti régió (g)

A VNet az Azure Virtual WAN által támogatott elsődleges elérési út. Ez az elérési út lehetővé teszi, hogy az Azure virtuális hálózatok üzembe helyezett Azure IAAS Enterprise-munkaterhelésekhez csatlakozhasson ágakat. Az ágak a ExpressRoute vagy a helyek közötti VPN használatával csatlakoztathatók a virtuális WAN-hoz. A VNet-kapcsolatokon keresztül a virtuális WAN-központokhoz csatlakozó virtuális hálózatok áthaladó forgalom. A virtuális WAN esetében nem szükséges explicit [átjáró-átvitel](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) , mert a virtuális WAN automatikusan engedélyezi az átjárónak a fiókirodába való átvitelét. Tekintse meg a [virtuális WAN-partnerek](virtual-wan-configure-automation-providers.md) című cikket, amely bemutatja, hogyan CSATLAKOZTATHATÓ egy SD-WAN CPE a virtuális WAN-hoz.

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach és virtuális WAN

A ExpressRoute egy privát és rugalmas módszer a helyi hálózatok Microsoft Cloudhoz való összekapcsolására. A Virtual WAN támogatja az Express Route Circuit-kapcsolatokat. Ha egy fiókirodai helyet a Virtual WAN-hoz csatlakoztat, az Express Route 1) Premium Circuit 2) áramkört Global Reach engedélyezett helyen kell lennie.

A ExpressRoute Global Reach a ExpressRoute kiegészítő szolgáltatása. A Global Reach segítségével összekapcsolhatja a ExpressRoute-áramköröket, hogy magánhálózat legyen a helyszíni hálózatok között. Azok az ágak, amelyek az Azure Virtual WAN-hoz csatlakoznak a ExpressRoute használatával, az ExpressRoute Global Reach kell kommunikálni egymással.

Ebben a modellben minden olyan ág, amely a ExpressRoute használatával csatlakozik a virtuális WAN-hubhoz, a virtuális hálózatok a VNet útvonalon keresztül csatlakozhat. Az elágazási forgalom nem kerül át a központba, mert az ExpressRoute Global Reach az Azure WAN-on keresztüli optimális elérési utat tesz lehetővé.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Ág – ág (b) és ág – ág közötti régió (f)

Az ágak ExpressRoute áramkörök és/vagy helyek közötti VPN-kapcsolatok használatával csatlakoztathatók egy Azure-beli virtuális WAN-hubhoz. Az ágakat csatlakoztathatja a virtuális WAN-hubhoz, amely az ág legközelebb eső régiójában található.

Ez a beállítás lehetővé teszi, hogy a vállalatok az Azure gerincet használják az ágak összekapcsolásához. Bár ez a funkció elérhető, érdemes mérlegelni a fiókirodák Azure-beli virtuális WAN-kapcsolaton keresztül történő csatlakoztatásának előnyeit, valamint a privát WAN használatát.  

### <a name="remote-user-to-vnet-c"></a>Távoli felhasználó – VNet (c)

Engedélyezheti a közvetlen, biztonságos távoli hozzáférést az Azure-hoz pont – hely kapcsolat használatával egy távoli felhasználói ügyfélről egy virtuális WAN-ra. A vállalati távoli felhasználóknak már nem kell hajtű a felhőbe a vállalati VPN használatával.

### <a name="remote-user-to-branch-d"></a>Távoli felhasználó – ág (d)

A távoli felhasználó – ág elérési út lehetővé teszi, hogy a távoli felhasználók, akik pont – hely kapcsolattal csatlakoznak az Azure-hoz a helyszíni számítási feladatokhoz és alkalmazásokhoz a felhőn keresztül történő átvitelsel. Ez az elérési út biztosítja a távoli felhasználók számára a rugalmasságot az Azure-ban és a helyszínen üzembe helyezett munkaterhelések eléréséhez. A vállalatok engedélyezhetik a központi felhőalapú biztonságos távelérés szolgáltatást az Azure Virtual WAN-ban.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet – VNet tranzit (e) és VNet – VNet régió (h)

A VNet – VNet tranzit lehetővé teszi, hogy a virtuális hálózatok a több virtuális hálózatok-ben megvalósított többrétegű alkalmazások összekapcsolásához csatlakozhasson egymáshoz. A virtuális hálózatok egymással is összekapcsolhatók a VNet-közvetítéssel, és ez olyan esetekben lehet megfelelő, amikor az VWAN hub-on keresztül történő átvitel nem szükséges.

## <a name="security"></a>Biztonság és házirend-vezérlés

Az Azure Virtual WAN-hubok összekötik az összes hálózati végpontot a hibrid hálózaton, és lehetséges, hogy az összes átviteli hálózati forgalom megjelenik. A virtuális WAN-hubok biztonságos virtuális hubokba alakíthatók, ha a Azure Firewallt a VWAN-hubokon belül telepíti, hogy engedélyezze a felhőalapú biztonságot, a hozzáférést és a házirend-vezérlést. A virtuális WAN-hubokban található Azure-tűzfalak összehangolása a Azure Firewall Manager által végezhető el.

A [Azure Firewall Manager](https://go.microsoft.com/fwlink/?linkid=2107683) biztosítja a globális átviteli hálózatok biztonságának kezeléséhez és méretezéséhez szükséges képességeket. A Azure Firewall Manager lehetővé teszi, hogy központilag kezelhesse az útválasztást, a globális házirendek felügyeletét, a speciális internetes biztonsági szolgáltatásokat harmadik felekkel, valamint a Azure Firewall.

![biztonságos virtuális központ Azure Firewall](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**5. ábra: biztonságos virtuális központ Azure Firewall**

A virtuális WAN Azure Firewall a következő globálisan biztonságos tranzit-csatlakozási útvonalakat támogatja. A zárójelben lévő betűk az 5. ábrán láthatók.

* VNet – VNet biztonságos átvitel (e)
* VNet vagy harmadik féltől származó biztonsági szolgáltatás (i)
* Ág – Internet vagy harmadik féltől származó biztonsági szolgáltatás (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet – VNet biztonságos átvitel (e)

A VNet – VNet biztonságos továbbítás lehetővé teszi, hogy a virtuális hálózatok a virtuális WAN-központban lévő Azure Firewall keresztül kapcsolódjon egymáshoz.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet vagy harmadik féltől származó biztonsági szolgáltatás (i)

A VNet vagy harmadik féltől származó biztonságos tranzit lehetővé teszi, hogy a virtuális hálózatok a virtuális WAN-központban lévő Azure Firewall keresztül kapcsolódjon az internethez vagy egy támogatott harmadik féltől származó biztonsági szolgáltatáshoz.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Ág – Internet vagy harmadik féltől származó biztonsági szolgáltatás (j)
Az ág – Internet vagy harmadik féltől származó biztonságos továbbítás lehetővé teszi, hogy az ágak a virtuális WAN-központban Azure Firewall keresztül csatlakozhassanak az internethez vagy egy támogatott harmadik féltől származó biztonsági szolgáltatáshoz.

## <a name="next-steps"></a>Következő lépések

Hozzon létre egy virtuális WAN-kapcsolaton keresztüli kapcsolatokat, és telepítse a Azure Firewallt az VWAN hub (ok) ban.

* [Helyek közötti kapcsolatok virtuális WAN használatával](virtual-wan-site-to-site-portal.md)
* [ExpressRoute-kapcsolatok virtuális WAN használatával](virtual-wan-expressroute-portal.md)
* [Azure Firewall Manager az Azure FW üzembe helyezéséhez a VWAN-ben](https://go.microsoft.com/fwlink/?linkid=2107683)
