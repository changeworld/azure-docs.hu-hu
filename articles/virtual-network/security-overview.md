---
title: Azure hálózati biztonsági csoportok – áttekintés
titlesuffix: Azure Virtual Network
description: További tudnivalók a hálózati biztonsági csoportokról. A hálózati biztonsági csoportok segítséget nyújtanak az Azure-erőforrások közötti hálózati forgalom szűrésében.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 3837b2af31ddab3c35abf877a74f980bd34e933d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280221"
---
# <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)
<a name="network-security-groups"></a>

Hálózati biztonsági csoporttal szűrheti az Azure-beli virtuális hálózatban lévő és az Azure-erőforrások felé irányuló hálózati forgalmat. A hálózati biztonsági csoportok olyan biztonsági szabályokat tartalmaznak, amelyek engedélyezik vagy letiltják a különböző típusú Azure-erőforrások bejövő vagy kimenő hálózati forgalmát. A virtuális hálózatokban üzembe helyezhető és hálózati biztonsági csoportokkal használható Azure-erőforrásokkal kapcsolatos információkért tekintse meg az [Azure-szolgáltatások virtuális hálózati integrációját](virtual-network-for-azure-services.md) ismertető cikket. Az egyes szabályokhoz meghatározhatja a forrást és a célt, valamint a használni kívánt portot és protokollt.

A cikk a hálózati biztonsági csoportokkal kapcsolatos fogalmakat ismerteti, hogy segítséget nyújtson a hatékony használatban. Ha korábban még nem hozott létre hálózati biztonsági csoportot, ebben a rövid [oktatóanyagban](tutorial-filter-network-traffic.md) némi gyakorlatra tehet szert. Ha már ismeri a hálózati biztonsági csoportok működését, és kezelni szeretné őket, tekintse meg a [hálózati biztonsági csoportok kezelését](manage-network-security-group.md) bemutató témakört. Ha kommunikációs problémákat tapasztal, és hibaelhárítást végezne a hálózati biztonsági csoportokon, tekintse meg [a virtuális gépek hálózatiforgalom-szűrési problémáinak diagnosztizálását](diagnose-network-traffic-filter-problem.md) ismertető rövid útmutatót. Engedélyezheti a [hálózati biztonsági csoport folyamatának naplóit](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a hálózati adatforgalom és a hozzájuk tartozó hálózati biztonsági csoport erőforrásainak elemzéséhez.

## <a name="security-rules"></a>Biztonsági szabályok

A hálózati biztonsági csoportok nulla vagy tetszőleges számú szabályt tartalmazhatnak, az Azure-előfizetések [korlátain](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) belül. Az egyes szabályok az alábbi tulajdonságokat határozzák meg:

|Tulajdonság  |Magyarázat  |
|---------|---------|
|Name (Név)|Egy egyedi név a hálózati biztonsági csoporton belül.|
|Prioritás | Egy 100 és 4096 közötti szám. A szabályok feldolgozása prioritási sorrendben történik. Az alacsonyabb sorszámúak feldolgozása a magasabb sorszámúak előtt történik, mivel az alacsonyabb sorszámok magasabb prioritást jelölnek. Ha az adatforgalom megfelel valamelyik szabálynak, a feldolgozás leáll. Ennek eredményeképp az olyan alacsonyabb prioritású (magasabb számú) szabályokat, amelyek attribútumai megegyeznek a magasabb prioritású szabályokéival, a rendszer nem dolgozza fel.|
|Forrás vagy cél| Bármelyik vagy egy egyéni IP-cím, Classless Inter-Domain Routing- (CIDR-) blokk (például 10.0.0.0/24), [szolgáltatáscímke](service-tags-overview.md) vagy [alkalmazásbiztonsági csoport](#application-security-groups). Ha egy Azure-erőforrás címét adja meg, az erőforráshoz rendelt magánhálózati IP-címet adja meg. A hálózati biztonsági csoportok feldolgozása azután történik, hogy az Azure a bejövő forgalomhoz a nyilvános IP-címeket magánhálózati IP-címekre fordítja le, de még mielőtt, hogy a magánhálózati IP-címeket nyilvános IP-címekre fordítaná le a kimenő forgalomhoz. További tudnivalók az Azure-beli [IP-címekről](virtual-network-ip-addresses-overview-arm.md). Tartományok, szolgáltatáscímkék vagy alkalmazásbiztonsági csoportok megadásával kevesebb biztonsági szabályt kell majd létrehoznia. A több egyéni IP-cím vagy -tartomány megadásának lehetősége (szolgáltatáscímkékből és alkalmazásbiztonsági csoportokból nem adható meg több) az egyes szabályokban [kibővített biztonsági szabályok](#augmented-security-rules) néven érhető el. Kibővített biztonsági szabályok kizárólag a Resource Manager-alapú üzemi modellben létrehozott hálózati biztonsági csoportokban hozhatóak létre. A klasszikus üzemi modellben létrehozott hálózati biztonsági csoportokban nem adhat meg több IP-címet vagy -címtartományt. További információ az [Azure üzemi modellekről](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|Protokoll     | TCP, UDP, ICMP vagy any.|
|Irány| Megadja, hogy a szabály a bejövő vagy a kimenő adatforgalomra vonatkozik.|
|Porttartomány     |Megadhat egy egyéni portot vagy egy porttartományt is. Megadhatja például a 80-as portot vagy a 10000–10005 tartományt. Tartományok megadásával kevesebb biztonsági szabályt kell majd létrehoznia. Kibővített biztonsági szabályok kizárólag a Resource Manager-alapú üzemi modellben létrehozott hálózati biztonsági csoportokban hozhatóak létre. A klasszikus üzemi modellben létrehozott hálózati biztonsági csoportokban egyazon szabályban nem adhat meg több portot vagy porttartományt.   |
|Műveletek     | Engedélyezés vagy letiltás        |

A hálózati biztonsági csoportok biztonsági szabályait a rendszer prioritásuk szerint, a rekordokkal kapcsolatos 5 információ (forrás, forrásport, cél, célport és protokoll) alapján értékeli ki, hogy a forgalom engedélyezve vagy tiltva legyen. Egy folyamatrekord jön létre a meglévő kapcsolatokhoz. A kommunikáció a folyamatrekordok kapcsolati állapota alapján lesz engedélyezve vagy tiltva. A folyamatrekord teszi lehetővé a hálózati biztonsági csoport állapotalapú működését. Ha bármely címre meghatároz egy kimenő biztonsági szabályt a 80-as porton keresztül, nem szükséges biztonsági szabályt megadnia a bejövő forgalomra a válaszhoz. Ha a kommunikáció kívülről indul, csak egy bejövő biztonsági szabályt kell meghatároznia. Ennek az ellenkezője is igaz. Ha egy porton engedélyezett a bejövő forgalom, nem szükséges egy kimenő biztonsági szabályt is megadni ugyanazon a porton történő válaszadáshoz.
Előfordulhat, hogy a meglévő kapcsolatok nem szakadnak meg az adatfolyamot engedélyező biztonsági szabály eltávolításakor. Az adatfolyam megszakad, ha a kapcsolatokat leállítják, és legalább néhány percig nincs forgalom egyik irányban sem.

Az egy hálózati biztonsági csoporton belül létrehozható biztonsági szabályok száma korlátozott. További részletek: [Az Azure korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

### <a name="default-security-rules"></a>Alapértelmezett biztonsági szabályok

Az Azure a következő alapértelmezett szabályokat hozza létre a létrehozott hálózati biztonsági csoportokban:

#### <a name="inbound"></a>Bejövő

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioritás|Forrás|Forrásportok|Cél|Célportok|Protokoll|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Bármelyik|Engedélyezés|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioritás|Forrás|Forrásportok|Cél|Célportok|Protokoll|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Bármelyik|Engedélyezés|

##### <a name="denyallinbound"></a>DenyAllInbound

|Prioritás|Forrás|Forrásportok|Cél|Célportok|Protokoll|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Bármelyik|Megtagadás|

#### <a name="outbound"></a>Kimenő

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Bármelyik | Engedélyezés |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Bármelyik | Engedélyezés |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Bármelyik | Megtagadás |

A **Forrás** és a **Cél** oszlopban a *VirtualNetwork*, *AzureLoadBalancer* és *Internet* értékek [szolgáltatáscímkék](service-tags-overview.md), nem IP-címek. A protokoll oszlopban **minden** a TCP, UDP és ICMP protokollt foglalja magában. Szabály létrehozásakor megadhatja a TCP, UDP, ICMP vagy bármelyik lehetőséget. A *0.0.0.0/0* érték a **Forrás** és a **Cél** oszlopban az összes címet jelöli. Az olyan ügyfelek, mint a Azure Portal, az Azure CLI vagy a PowerShell a * vagy bármelyiket használhatja ehhez a kifejezéshez.
 
Az alapértelmezett szabályok nem távolíthatók el, azonban magasabb prioritású szabályok létrehozásával felülírhatók.

### <a name="augmented-security-rules"></a>Kibővített biztonsági szabályok

A kibővített biztonsági szabályok megkönnyítik a virtuális hálózatok biztonsági definícióinak megadását, így nagyobb és összetettebb hálózati biztonsági szabályok alakíthatók ki kevesebb szabállyal. Több portot, több konkrét IP-címet és -tartományt foglalhat egyetlen, könnyen érthető biztonsági szabályba. Kibővített szabályokat a szabályok forrás, cél és port mezőiben is használhat. A biztonsági szabály definíciójának egyszerűbb karbantartása érdekében kombinálhatja a kibővített biztonsági szabályokat [szolgáltatáscímkékkel](service-tags-overview.md) vagy [alkalmazásbiztonsági csoportokkal](#application-security-groups). A szabályokban megadható címek, tartományok és portok száma korlátozott. További részletek: [Az Azure korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

#### <a name="service-tags"></a>Szolgáltatáscímkék

A szolgáltatás címkéje egy adott Azure-szolgáltatás IP-címeinek egy csoportját jelöli. Ez segít a hálózati biztonsági szabályok gyakori frissítéseinek összetettségének minimalizálásában.

További információ: Azure- [szolgáltatás címkéi](service-tags-overview.md). A hálózati hozzáférés korlátozásához a Storage szolgáltatás címkével kapcsolatos példát a [hálózati hozzáférés korlátozása a Pásti-erőforrásokhoz](tutorial-restrict-network-access-to-resources.md)című témakörben talál.

#### <a name="application-security-groups"></a>Alkalmazásbiztonsági csoportok

Az alkalmazásbiztonsági csoportokkal az alkalmazás struktúrájának természetes bővítményeként konfigurálhatja a hálózati biztonságot, így csoportosíthatja a virtuális gépeket, és ezen csoportok alapján meghatározhatja a hálózati biztonsági szabályokat. A biztonsági szabályokat újrahasznosíthatja nagy léptékben is a konkrét IP-címek manuális karbantartása nélkül. További információ: [alkalmazás biztonsági csoportjai](application-security-groups.md).

## <a name="how-traffic-is-evaluated"></a>A forgalom kiértékelése

Az Azure-beli virtuális hálózatokban több Azure-szolgáltatásból is helyezhet üzembe erőforrásokat. A teljes listáért tekintse meg [a virtuális hálózatokban üzembe helyezhető szolgáltatásokat](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) ismertető témakört. A virtuális gépek mindegyik virtuális hálózatának [alhálózatához](virtual-network-manage-subnet.md#change-subnet-settings) és [hálózati adapteréhez](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) nulla vagy egy hálózati biztonsági csoport rendelhető hozzá. Egy adott hálózati biztonsági csoport tetszőleges számú alhálózathoz és hálózati adapterhez rendelhető.

A következő képen különböző forgatókönyvek láthatók a hálózati biztonsági csoportok üzembe helyezésére az internetről bejövő és az internetre kimenő hálózati forgalom engedélyezéséhez a 80-as TCP-porton keresztül:

![NSG-feldolgozás](./media/security-groups/nsg-interaction.png)

A fenti kép és a következő leírás mutatja be, hogy az Azure hogyan dolgozza fel a hálózati biztonsági csoportok bejövő és kimenő szabályait:

### <a name="inbound-traffic"></a>Bejövő forgalom

A bejövő forgalom esetében az Azure először a hálózati biztonsági csoport alhálózathoz rendelt szabályait dolgozza fel (ha van alhálózat), majd a hálózati biztonsági csoport hálózati adapterhez rendelt szabályait (ha van hálózati adapter).

- **VM1**: Az *NSG1* biztonsági szabályai lesznek feldolgozva, mivel ez a *Subnet1* alhálózathoz van rendelve, és a *VM1* a *Subnet1* alhálózatban található. Ha nem hozott létre egy szabályt, amely engedi a 80-as port bejövő forgalmát, a forgalmat az alapértelmezett [DenyAllInbound](#denyallinbound) biztonsági szabály letiltja, és azt az *NSG2* soha nem értékeli ki, mivel az *NSG2* a hálózati adapterhez van rendelve. Ha az *NSG1* rendelkezik olyan biztonsági szabállyal, amely engedélyezi a forgalmat a 80-as porton, a forgalmat ezután az *NSG2* feldolgozza. Ahhoz, hogy a 80-as porton engedélyezve legyen a virtuális gépre irányuló forgalom, az *NSG1* és az *NSG2* csoportnak egyaránt rendelkeznie kell olyan szabállyal, amely engedi az internetről beérkező forgalmat a 80-as porton.
- **VM2**: Az *NSG1* szabályai fel lesznek dolgozva, mivel a *VM2* is a *Subnet1* alhálózaton található. Mivel a *VM2* hálózati adapteréhez nincs hálózati biztonsági csoport rendelve, erre a gépre az *NSG1* által átengedett minden forgalom megérkezik, és az *NSG1* által letiltott minden forgalom blokkolva lesz. Ha egy alhálózathoz egy hálózati biztonsági csoport van rendelve, az adott alhálózaton az összes erőforrás számára le lesz tiltva vagy engedélyezve lesz a forgalom.
- **VM3**: Mivel a *Subnet2* alhálózathoz nincs hálózati biztonsági csoport rendelve, a forgalom engedélyezve lesz az alhálózaton, és az *NSG2* feldolgozza, mivel az *NSG2* hozzá van rendelve a *VM3* géphez csatlakoztatott hálózati adapterhez.
- **VM4**: A forgalom engedélyezve van a *VM4* gépen, mivel a *Subnet3* hálózathoz vagy a virtuális gép hálózati adapteréhez nincs hálózati biztonsági csoport rendelve. Ha egy alhálózathoz vagy hálózati adapterhez nincs hálózati biztonsági csoport rendelve, ezeken a teljes hálózati forgalom engedélyezve lesz.

### <a name="outbound-traffic"></a>Kimenő forgalom

A kimenő forgalom esetében az Azure először a hálózati biztonsági csoport hálózati adapterhez rendelt szabályait dolgozza fel (ha van hálózati adapter), majd a hálózati biztonsági csoport alhálózathoz rendelt szabályait (ha van alhálózat).

- **VM1**: Az *NSG2* biztonsági szabályai fel lesznek dolgozva. Ha nem hoz létre egy biztonsági szabályt, amely letiltja a 80-as port internetre irányuló kimenő forgalmát, a forgalmat az alapértelmezett [AllowInternetOutbound](#allowinternetoutbound) biztonsági szabály az *NSG1* és az *NSG2* csoporton egyaránt engedélyezi. Ha az *NSG2* rendelkezik olyan biztonsági szabállyal, amely letiltja a forgalmat a 80-as porton, a forgalom le lesz tiltva, és az *NSG1* soha nem értékeli ki. Ahhoz, hogy a 80-as porton le legyen tiltva a virtuális gépről érkező forgalom, legalább az egyik hálózati biztonsági csoportnak rendelkeznie kell olyan szabállyal, amely letiltja az internetre irányuló kimenő forgalmat a 80-as porton.
- **VM2**: A teljes forgalom áthaladhat a hálózati adapteren keresztül az alhálózatra, mivel a *VM2* géphez csatlakoztatott hálózati adapterhez nincs hálózati biztonsági csoport rendelve. Az *NSG1* szabályai fel lesznek dolgozva.
- **VM3**: Ha az *NSG2* rendelkezik olyan biztonsági szabállyal, amely letiltja a forgalmat a 80-as porton, a forgalom le lesz tiltva. Ha az *NSG2* rendelkezik olyan biztonsági szabállyal, amely engedélyezi a forgalmat a 80-as porton, a 80-as porton engedélyezve lesz az internetre irányuló kimenő forgalom, mivel a *Subnet2* hálózathoz nincs hálózati biztonsági csoport rendelve.
- **VM4**: A teljes hálózati forgalom engedélyezve van a *VM4* gépről, mivel a virtuális gép hálózati adapteréhez vagy a *Subnet3* alhálózathoz nincs hálózati biztonsági csoport rendelve.


### <a name="intra-subnet-traffic"></a>Alhálózaton belüli forgalom

Fontos megjegyezni, hogy az alhálózathoz társított NSG biztonsági szabályai befolyásolhatják a virtuális gépek közötti kapcsolatot. Ha például egy olyan szabályt ad hozzá a *NSG1* -hez, amely megtagadja az összes bejövő és kimenő forgalmat, a *VM1* és a *VM2* többé nem fog tudni kommunikálni egymással. Egy másik szabályt kifejezetten hozzá kell adni ahhoz, hogy ezt engedélyezzék. 



A hálózati adapterekhez rendelt összesített szabályokat könnyen megismerheti a hálózati adapter [érvényes biztonsági szabályainak](virtual-network-network-interface.md#view-effective-security-rules) megtekintésével. Emellett az Azure Network Watcher [IP-forgalom ellenőrzése](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szolgáltatásával is megállapíthatja, hogy a kommunikáció engedélyezett-e a hálózati adapterre/adapterről. Az IP-forgalom ellenőrzése megmutatja, hogy a kommunikáció engedélyezve vagy blokkolva van-e, valamint hogy melyik hálózati biztonsági szabály engedélyezi vagy blokkolja a forgalmat.

> [!NOTE]
> A hálózati biztonsági csoportok az alhálózatokhoz vagy a klasszikus üzemi modellben üzembe helyezett virtuális gépekhez és felhőalapú szolgáltatásokhoz, valamint a Resource Manager-alapú üzemi modellben lévő alhálózatokhoz vagy hálózati adapterekhez vannak társítva. Az Azure üzembehelyezési modellekkel kapcsolatos további információkért lásd: [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> Hacsak nincs erre kifejezett oka, javasoljuk, hogy az egyes hálózati biztonsági csoportokat egy alhálózathoz vagy egy hálózati adapterhez rendelje, mindkettőhöz ne. Mivel az alhálózathoz rendelt hálózati biztonsági csoport szabályai ütközhetnek a hálózati adapterhez rendelt hálózati biztonsági csoport szabályaival, nem várt kommunikációs problémák merülhetnek fel, amelyek hibaelhárítást igényelnek.

## <a name="azure-platform-considerations"></a>Tudnivalók az Azure platformhoz

- **A gazdagép csomópontjának virtuális IP-** címe: az alapszintű infrastruktúra-szolgáltatások, például a DHCP, a DNS, a IMDS és az állapotfigyelő szolgáltatás a virtualizált GAZDAGÉP IP-címeinek 168.63.129.16 és 169.254.169.254 keresztül érhető el. Ezek az IP-címek a Microsofthoz tartoznak, és az összes régióban használt virtualizált IP-címek erre a célra szolgálnak.
- **Licencelés (kulcskezelő szolgáltatás)** : A virtuális gépeken futó Windows-rendszerképeket licencelni kell. A licenceléshez el kell küldeni egy licencelési kérelmet a Kulcskezelő szolgáltatás ilyen kérelmeket kezelő kiszolgálóinak. A kérelmet az 1688-as kimenő porton küldi el a rendszer. Az [alapértelmezett 0.0.0.0/0 útvonalat](virtual-networks-udr-overview.md#default-route) használó konfigurációkban ez a platformszabály le van tiltva.
- **Virtuális gépek az elosztott terhelésű készletekben**: Az alkalmazott forrásport és címtartomány a forrásszámítógépről származik, nem a terheléselosztóról. A célport és a címtartomány nem a terheléselosztóhoz, hanem a célszámítógéphez tartozik.
- **Azure szolgáltatáspéldányok**: Több Azure szolgáltatás, például a HDInsight, az alkalmazásszolgáltatási környezetek és a virtuálisgép-méretezési csoportok példányai is virtuális hálózati alhálózatokon vannak üzembe helyezve. A virtuális hálózatokon üzembe helyezhető szolgáltatások teljes listájához lásd a [Virtuális hálózatok az Azure-szolgáltatásokhoz](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) című témakört. Mindenképp ismerkedjen meg behatóan az egyes szolgáltatások portkövetelményeivel, mielőtt egy hálózati biztonsági csoportot alkalmazna az erőforrást üzemeltető alhálózatra. Ha a valamely szolgáltatás által használt portokat letiltja, a szolgáltatás nem megfelelően fog működni.
- **Kimenő e-mailek küldése**: A Microsoft azt javasolja, hogy hitelesített SMTP-továbbítási szolgáltatásokkal (legtöbbször az 587-es TCP-porton, de gyakran más portokon keresztül) küldjön e-maileket az Azure Virtual Machines rendszeréből. Az SMTP-továbbítási szolgáltatások a feladói jellegzetességek biztosítására szakosodtak, így minimalizálják annak lehetőségét, hogy a külső e-mail-szolgáltatók visszautasítsák az üzeneteket. Ilyen SMTP-továbbítási szolgáltatás például, a teljesség igénye nélkül, az Exchange Online Protection és a SendGrid. Az SMTP-továbbítási szolgáltatások használata nincsen korlátozva az Azure-ban, függetlenül attól, hogy milyen előfizetése van. 

  Amennyiben 2017. november 15. előtt hozta létre Azure-előfizetését, az SMTP-továbbítási szolgáltatások használata mellett közvetlenül a 25-ös TCP-porton keresztül is küldhet e-maileket. Amennyiben 2017. november 15. után fizetett elő, nem biztos hogy küldhet e-maileket közvetlenül a 25-ös porton keresztül. A 25-ös porton keresztül folytatott kimenő kommunikáció viselkedése az előfizetés típusától függ, amely lehet:

     - **Nagyvállalati szerződés**: 25-ös porton keresztüli kimenő kommunikáció engedélyezve. Közvetlenül a virtuális gépekről küldhet kimenő e-maileket a külső e-mail-szolgáltatóknak, és az Azure platform korlátozásai nem érvényesülnek. 
     - **Használatalapú fizetés**: a 25-ös porton keresztüli kimenő kommunikáció minden erőforráson blokkolva van. Ha közvetlenül a virtuális gépéről szeretne e-mailt küldenie egy külső e-mail-szolgáltatónak (hitelesített SMTP-továbbítás használata nélkül), kérheti a korlátozás feloldását. A kérelmeket a Microsoft saját belátása szerint értékeli és hagyja jóvá, a visszaélések kiküszöbölésére szolgáló megfelelő ellenőrzések elvégzése után. Kérelem benyújtásához támogatási esetet kell nyitnia a *Technikai*, *Virtuális hálózati kapcsolat*, *Sikertelen e-mail-küldés (SMTP/25-ös port)* problématípus kiválasztásával. A támogatási esetben részletesen indokolja, hogy előfizetésének miért kell közvetlenül a levelezési szolgáltatónak e-mailt küldenie a hitelesített SMTP-továbbítás használata helyett. Amennyiben előfizetését felmentik a korlátozás alól, csak a mentesítés dátuma után létrehozott virtuális gépek képesek a 25-ös porton keresztüli kimenő kommunikációra.
     - **MSDN, Azure Pass, Azure in Open, Education, BizSpark és ingyenes próbaverzió**: a 25-ös porton keresztüli kimenő kommunikáció minden erőforráson blokkolva van. Nem küldhető kérelem a korlátozás feloldására, mert a kérelmek nem teljesíthetők. Ha mindenképp szeretne e-mailt küldeni a virtuális gépről, használjon SMTP-továbbítási szolgáltatást.
     - **Felhőszolgáltató**: Az Azure-erőforrásokat felhőszolgáltatón keresztül használó ügyfelek létrehozhatnak egy támogatási esetet a felhőszolgáltatónál, és kérhetik, hogy a szolgáltató hozzon létre a nevükben egy feloldási esetet, ha nem használható egy biztonságos SMTP-továbbító.

  Amennyiben az Azure engedélyezi az e-mailek küldését a 25-ös porton keresztül, a Microsoft nem tudja garantálni, hogy a levelező szolgáltatók elfogadják a virtuális gépről érkező bejövő e-maileket. Ha egy szolgáltató elutasítja az Ön virtuális gépéről érkező leveleket, vele együttműködésben oldja meg az üzenetküldési vagy levélszemétszűrési problémákat, vagy használjon hitelesített SMTP-továbbítási szolgáltatást.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [a hálózati biztonsági csoportok létrehozását](tutorial-filter-network-traffic.md).
