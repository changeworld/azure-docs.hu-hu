---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2bfdf1046c67ed1651f792191923bf4c533d0299
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77205747"
---
Ez a cikk az Azure Managed Disks és az Azure prémium SSD-lemezekkel kapcsolatos gyakori kérdésekre ad választ.

## <a name="managed-disks"></a>Felügyelt lemezek

**Mi az Azure Managed Disks?**

A Managed Disks egy olyan szolgáltatás, amely leegyszerűsíti az Azure IaaS virtuális gépek lemezes kezelését a Storage-fiókok kezelésével. További információ: [Managed Disks áttekintése](../articles/virtual-machines/windows/managed-disks-overview.md).

**Ha egy standard szintű felügyelt lemezt hoz létre egy 80 GB-os meglévő VHD-ről, Mennyibe kerül?**

A 80 GB-os virtuális merevlemezről létrehozott standard szintű felügyelt lemez a következő elérhető standard lemez méreteként lesz kezelve, amely egy S10-lemez. Az S10-lemez díjszabása alapján számítunk fel díjat. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Vannak a standard szintű felügyelt lemezekre vonatkozó tranzakciós költségek?**

Igen. Minden tranzakcióért díjat számítunk fel. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Standard szintű felügyelt lemez esetén a lemezre vagy a lemez kiosztott kapacitására vonatkozó adatok tényleges mérete után kell díjat fizetni?**

A díjat a lemez kiépített kapacitása alapján számítjuk fel. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Miben különbözik a prémium szintű felügyelt lemezek díjszabása a nem felügyelt lemezekkel?**

A prémium szintű Managed Disks díjszabása megegyezik a nem felügyelt prémium szintű lemezekkel.

**Módosíthatom a felügyelt lemezek tárolási fiókjának típusát (standard vagy prémium)?**

Igen. A felügyelt lemezek tárolási fiókjának típusát a Azure Portal, a PowerShell vagy az Azure CLI használatával módosíthatja.

**Használhatok egy Azure Storage-fiókban lévő VHD-fájlt egy másik előfizetéssel rendelkező felügyelt lemez létrehozásához?**

Igen.

**Használhatok egy Azure Storage-fiókban lévő VHD-fájlt egy felügyelt lemez egy másik régióban való létrehozásához?**

Nem.

**Vannak-e méretezési korlátozások a felügyelt lemezeket használó ügyfelek számára?**

Managed Disks megszünteti a Storage-fiókokhoz társított korlátokat. A maximális korlát azonban a 50 000 felügyelt lemez régiónként és egy előfizetéshez tartozó lemez típusa szerint.

**Készíthetek növekményes pillanatképet egy felügyelt lemezről?**

Nem. Az aktuális pillanatkép-képesség egy felügyelt lemez teljes másolatát teszi lehetővé.

**A rendelkezésre állási csoportba tartozó virtuális gépek felügyelt és nem felügyelt lemezek kombinációjából állnak?**

Nem. A rendelkezésre állási csoportba tartozó virtuális gépeknek az összes felügyelt lemezt vagy az összes nem felügyelt lemezt kell használniuk. A rendelkezésre állási csoport létrehozásakor kiválaszthatja, hogy milyen típusú lemezeket kíván használni.

**Managed Disks az alapértelmezett beállítás a Azure Portalban?**

Igen.

**Létrehozhatok egy üres felügyelt lemezt?**

Igen. Létrehozhat egy üres lemezt is. A felügyelt lemezek a virtuális gépektől függetlenül hozhatók létre, például anélkül, hogy egy virtuális géphez csatlakoztatná.

**Mi a támogatott tartalék tartományok száma egy Managed Disks használó rendelkezésre állási csoport számára?**

Attól függően, hogy a rendelkezésre állási csoport melyik régiót használja Managed Disks található, a támogatott tartalék tartományok száma 2 vagy 3.

**Hogyan állítható be a diagnosztika standard Storage-fiókja?**

A virtuálisgép-diagnosztika számára egyéni Storage-fiókot kell beállítania.

**Milyen szerepkör-alapú Access Control támogatás érhető el Managed Diskshoz?**

A Managed Disks három kulcsfontosságú alapértelmezett szerepkört támogat:

* Tulajdonos: mindent kezelhet, beleértve a hozzáférést is
* Közreműködő: mindent kezelhet, kivéve a hozzáférést
* Olvasó: mindent megtekinthet, de nem végezhet módosításokat

**Van mód arra, hogy egy felügyelt lemezt privát Storage-fiókba lehessen másolni vagy exportálni?**

Létrehozhatja a felügyelt lemez írásvédett megosztott hozzáférési aláírási (SAS) URI azonosítóját, és a használatával a tartalmat átmásolhatja egy privát Storage-fiókba vagy a helyszíni tárolóba. Az SAS URI-t a Azure Portal, Azure PowerShell, az Azure CLI vagy a [AzCopy](../articles/storage/common/storage-use-azcopy.md) használatával használhatja

**Készíthető másolat a felügyelt lemezről?**

Az ügyfelek pillanatképet készíthetnek a felügyelt lemezekről, majd a pillanatkép használatával létrehozhatnak egy másik felügyelt lemezt.

**A nem felügyelt lemezek továbbra is támogatottak?**

Igen, a nem felügyelt és a felügyelt lemezek is támogatottak. Javasoljuk, hogy felügyelt lemezeket használjon új munkaterhelésekhez, és telepítse át az aktuális számítási feladatokat a felügyelt lemezekre.

**Megkereshetem a nem felügyelt és felügyelt lemezeket ugyanazon a virtuális gépen?**

Nem.

**Ha 128 GB-os lemezt hozunk létre, majd a méretet a 130 gibibájtban értendők (GiB) értékre növeljük, akkor a következő lemez méretére kell fizetnem (256 GiB)?**

Igen.

**Létrehozhatok helyileg redundáns tárolást, Geo-redundáns tárolást és a zóna-redundáns tárolással felügyelt lemezeket?**

Az Azure Managed Disks jelenleg csak a helyileg redundáns tárolású felügyelt lemezeket támogatja.

**Csökkenthető vagy csökkenthető a felügyelt lemezek?**

Nem. Ez a funkció jelenleg nem támogatott.

**Lehet megszakítani a saját lemezem bérletét?**

Nem. Ez jelenleg nem támogatott bérletként, hogy megakadályozza a véletlen törlést a lemez használatakor.

**Megváltoztathatom a számítógépnév tulajdonságot, ha egy speciális (nem a rendszer-előkészítő eszközzel vagy általánosított) operációsrendszer-lemezzel lett létrehozva a virtuális gép kiépítéséhez?**

Nem. A számítógépnév tulajdonság nem frissíthető. Az új virtuális gép örökli azt a szülő virtuális gépről, amelyet az operációs rendszer lemezének létrehozásához használt. 

**Hol találhatok minta Azure Resource Manager sablonokat a felügyelt lemezekkel rendelkező virtuális gépek létrehozásához?**
* [Sablonok listája Managed Disks használatával](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**Amikor egy lemezt hoz létre egy blobból, van-e állandóan létező kapcsolat a forrás blobtal?**

Nem, az új lemez létrehozásakor ez a blob teljes önálló példánya, és a kettő között nincs kapcsolat. Ha szeretné, a lemez létrehozása után a forrás blobot a rendszer bármilyen módon törölheti anélkül, hogy az hatással lenne az újonnan létrehozott lemezre.

**Átnevezhetem a felügyelt vagy nem felügyelt lemezt a létrehozása után?**

Felügyelt lemezek esetében nem nevezhető át. A nem felügyelt lemezeket azonban akkor is átnevezheti, ha az jelenleg nem virtuális merevlemezhez vagy virtuális géphez van csatolva.

**Használhatok GPT particionálást egy Azure-lemezen?**

Az 1. generációs képek csak a GPT-particionálást használhatják adatlemezeken, nem operációsrendszer-lemezeken. Az operációsrendszer-lemezeknek az MBR-partíció stílusát kell használniuk.

A [2. generációs lemezképek](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) GPT particionálást használhatnak az operációsrendszer-lemezen és az adatlemezeken is.

**Milyen típusú lemezek támogatják a pillanatképeket?**

Prémium SSD, standard SSD és standard szintű HDD-támogatási Pillanatképek. Ebben a három lemez típusban a pillanatképek minden lemez mérete esetén támogatottak (beleértve a lemezeket akár 32 TiB méretig). Az ultra-lemezek nem támogatják a pillanatképeket.

**Mik azok az Azure Disk Reservations?**
A lemezes foglalás az a lehetőség, hogy egy évig előre megvásárolja a lemezes tárolást, ami csökkenti a teljes költségeket. Az Azure-lemezek foglalásával kapcsolatos részletekért tekintse meg a tárgyat ismertető cikket: a [foglalási kedvezményt az Azure diskre alkalmazza](../articles/cost-management-billing/reservations/understand-disk-reservations.md).

**Milyen lehetőségeket kínál az Azure Disk foglalás?** Az Azure Disk foglalás lehetőséget biztosít a prémium SSD-k megvásárlására a megadott SKU-P30 (1 TiB) akár P80 (32 TiB), egy éves időszakra. A lemezes foglalás megvásárlásához szükséges lemezek minimális száma nem korlátozott. Emellett dönthet úgy is, hogy egyszeri, előzetes fizetési vagy havi fizetéssel fizet. Prémium SSD Managed Disks esetében nem alkalmazható további tranzakciós díj. 

A foglalások lemezek formájában, nem kapacitással történnek. Más szóval, ha P80 (32 TiB) lemezt foglal le, egyetlen P80-lemezt kap, akkor az adott foglalást nem lehet két kisebb P70 (16 TiB) lemezre osztani. Természetesen a lehető legtöbb vagy kevés lemezt is lefoglalhatja, többek között két külön P70 (16 TiB) lemezt.

**Hogyan történik az Azure Disk-foglalás alkalmazása?**  
A lemezek foglalása a fenntartott virtuális gépek (VM) példányaihoz hasonló modellt követ. A különbség az, hogy a lemezek foglalása nem alkalmazható a különböző SKU-ra, míg a virtuálisgép-példányok is. A virtuálisgép-példányokkal kapcsolatos további információkért tekintse meg a [költségek mentése a Azure Reserved VM Instancesával](../articles/virtual-machines/linux/prepay-reserved-vm-instances.md) című témakört.    

**Használhatom az Azure-lemezek foglalásán keresztül vásárolt adattárolót több régióban?**     
Az Azure-lemezek foglalása egy adott régióra és SKU-ra (például az USA 2. keleti P30) van megvásárolva, ezért nem használható ezen szerkezeteken kívül. További Azure-lemezek foglalását is megvásárolhatja a lemezes tároláshoz más régiókban vagy SKU-ban. 

**Mi történik, ha az Azure-lemezek foglalása lejár?**    
Az e-mail-értesítéseket a lejárat előtt 30 nappal, a lejárati dátum után pedig újra megkapja. Ha a foglalás lejár, a telepített lemezek továbbra is futni fognak, és az utólagos elszámolású [díjszabással](https://azure.microsoft.com/pricing/details/managed-disks/)számolunk fel díjat.

### <a name="azure-shared-disks"></a>Azure-beli megosztott lemezek

**Támogatottak-e a megosztott lemezek szolgáltatás nem felügyelt lemezek vagy Blobok esetén?**

Nem, csak a prémium szintű SSD által felügyelt lemezek esetében támogatott.

**Mely régiók támogatják a megosztott lemezeket?**

Jelenleg csak az USA nyugati középső régiója.

**Használhatók-e a megosztott lemezek operációsrendszer-lemezként?**

Nem, a megosztott lemezek csak adatlemezek esetén támogatottak.

**Milyen méretű lemezek támogatják a megosztott lemezeket?**

Csak a P15 vagy nagyobb támogatást biztosító SSD-k támogatják a megosztott lemezeket.

**Ha van meglévő prémium SSD-je, Engedélyezem a megosztott lemezeket?**

Az API 2019-07-01-es vagy újabb verziójával létrehozott összes felügyelt lemez engedélyezheti a megosztott lemezeket. Ehhez le kell választania a lemezt az összes olyan virtuális gépről, amelyhez hozzá van csatolva. Ezután szerkessze a lemez `maxShares` tulajdonságát.

**Ha már nem szeretném használni a lemezt megosztott módban, hogyan lehet letiltani?**

Válassza le a lemezt az összes olyan virtuális gépről, amelyhez hozzá van csatolva. Ezután szerkessze a maxShare tulajdonságot a lemezen az 1 értékre.

**Át lehet méretezni egy megosztott lemezt?**

Igen.

**Engedélyezhető az írási gyorssegéd olyan lemezen, amelyen engedélyezve vannak a megosztott lemezek is?**

Nem.

**Engedélyezhető a gazdagép gyorsítótárazása olyan lemez esetében, amelyen engedélyezve van a megosztott lemez?**

Az egyetlen támogatott gazdagép-gyorsítótárazási beállítás a "None".

## <a name="ultra-disks"></a>Ultra-lemezek

**Mire kell beállítani az ultra Disk átviteli sebességét?**
Ha nem biztos abban, hogy mit kell beállítania a lemez átviteli sebességének beállításához, javasoljuk, hogy először a 16 KiB i/o-méretet adja meg, és az alkalmazás figyelése után állítsa be a teljesítményt. A képlet a következő: átviteli sebesség (MB/s) = IOPS * 16/1000.

**Úgy konfiguráltam a lemezt, hogy 40000 IOPS, de csak 12800 IOPS látok, miért nem látom a lemez teljesítményét?**
A lemez szabályozásán kívül van egy IO-szabályozás, amely a virtuális gép szintjén lesz kiszabva. Győződjön meg arról, hogy a használt virtuális gép mérete támogatja a lemezeken konfigurált szinteket. A virtuális gép által kiszabott IO-korlátokkal kapcsolatos részletekért lásd: a [Windows rendszerű virtuális gépek méretei az Azure-ban](../articles/virtual-machines/windows/sizes.md).

**Használhatok gyorsítótárazási szinteket Ultra lemezzel?**
Nem, az ultra-lemezek nem támogatják a más típusú lemezeken támogatott különböző gyorsítótárazási módszereket. Állítsa a lemezes gyorsítótárazást a none értékre.

**A meglévő virtuális géphez is csatlakoztatok egy ultra-lemezt?**
Előfordulhat, hogy a virtuális gépnek egy olyan régióban és rendelkezésre állási zónában kell lennie, amely támogatja az ultra-lemezeket. További részletekért lásd: [az ultra Disks szolgáltatás első lépései](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md) .

**Használhatok a virtuális gép operációsrendszer-lemezének ultravékony lemezét?**
Nem, az ultra-lemezek csak az adatlemezek, és csak 4K natív lemezként támogatottak.

**Átválthatok egy meglévő lemezt egy ultra lemezre?**
Nem, de az adatok áttelepíthetők egy meglévő lemezről egy ultra lemezre. Egy meglévő lemez Ultra-lemezre való áttelepítéséhez csatlakoztassa mindkét lemezt ugyanahhoz a virtuális géphez, és másolja a lemez adatait egyik lemezről a másikra, vagy használja ki a külső féltől származó megoldást az adatok áttelepítésére.

**Készíthetők Pillanatképek az ultra-lemezekhez?**
Nem, a pillanatképek még nem érhetők el.

**Azure Backup érhető el az ultra lemezekhez?**
Nem, Azure Backup támogatás még nem érhető el.

**A rendelkezésre állási csoportokban futó virtuális gépekhez is lehet Ultra-lemezt csatlakoztatni?**
Nem, ez még nem támogatott.

**Engedélyezhető-e a virtuális gépekhez Azure Site Recovery az ultra Disks használatával?**
Nem, Azure Site Recovery az ultra-lemezek esetében még nem támogatott.

## <a name="uploading-to-a-managed-disk"></a>Feltöltés felügyelt lemezre

**Tölthetek fel az adatok egy meglévő felügyelt lemezre?**

Nem, a feltöltés csak új, üres lemez **ReadyToUpload** állapotú létrehozásakor használható.

**Hogyan feltölt egy felügyelt lemezre?**

Hozzon létre egy felügyelt lemezt a [CreationData](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) [createOption](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) tulajdonságával "feltöltés" értékre, majd töltse fel az adatok között.

**Lehet lemezeket csatlakoztatni egy virtuális géphez, amíg feltöltési állapotban van?**

Nem.

**Készíthetek pillanatképet a feltöltött lemezekről a feltöltési állapotban?**

Nem.

## <a name="standard-ssd-disks"></a>standard SSD lemezek

**Mik azok az Azure standard SSD-lemezek?**
Standard SSD lemezek a szilárdtest adathordozó által támogatott szabványos lemezek, amelyek költséghatékony tárolási kapacitást biztosítanak az alacsonyabb IOPS szinteken konzisztens teljesítményt igénylő munkaterhelések esetén.

<a id="standard-ssds-azure-regions"></a>**Mik a standard SSD lemezek jelenleg támogatott régiói?**
Az összes Azure-régió már támogatja standard SSD lemezeket.

**A standard SSD-k használata esetén Azure Backup érhető el?**
Igen, Azure Backup már elérhető.

**Hogyan standard SSD lemezeket létrehozni?**
Standard SSD lemezeket a Azure Resource Manager sablonok, az SDK, a PowerShell vagy a parancssori felület használatával hozhat létre. Az alábbi paraméterek szükségesek a Resource Manager-sablonban standard SSD lemezek létrehozásához:

* a Microsoft *apiVersion* . a számítást `2018-04-01` (vagy újabb) értékre kell beállítani.
* *ManagedDisk. tárfióktípus* meghatározása `StandardSSD_LRS`ként

A következő példa egy standard SSD lemezeket használó virtuális gép *Properties. storageProfile. osDisk* szakaszát mutatja be:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Az standard SSD-lemezek sablonnal történő létrehozásával kapcsolatos példa: [virtuális gép létrehozása egy standard SSD Adatlemezekkel rendelkező Windows-lemezképből](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

**Átválthatom a meglévő lemezeket standard SSDre?**
Igen. Tekintse át az [Azure Managed Disks Storage standard és Premium közötti átalakítását, és fordítva](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) a Managed Disks átalakítására vonatkozó általános irányelveket. Továbbá a következő érték használatával frissítse a lemez típusát standard SSDra.
-AccountType StandardSSD_LRS

**Milyen előnyökkel jár a standard SSD lemezek használata a HDD helyett?**
Standard SSD lemezek jobb késést, következetességet, rendelkezésre állást és megbízhatóságot biztosítanak a HDD-lemezekhez képest. Az alkalmazás munkaterhelései sokkal simábban futnak a standard SSD miatt. Vegye figyelembe, prémium SSD lemezek a legtöbb IO-igényes éles számítási feladathoz ajánlott megoldás.

**Használhatom a standard SSD-ket nem felügyelt lemezként?**
Nem, a standard SSD lemezek csak Managed Disksként érhetők el.

**Standard SSD lemezek támogatják az "egypéldányos VM SLA" szolgáltatást?**
Nem, a standard SSD-k nem rendelkeznek egyetlen példányú VM SLA-val. Az Egypéldányos VM SLA-hoz prémium SSD lemezeket használjon.

## <a name="migrate-to-managed-disks"></a>Migrálás felügyelt lemezekre

**Van-e hatással az áttelepítés a Managed Disks teljesítményére?**

Az áttelepítés a lemez egyik tárolóhelyről a másikra történő áthelyezését foglalja magában. Ez az adatháttér-másolaton keresztül történik, amely több órát is igénybe vehet, és általában kevesebb, mint 24 óra, a lemezeken lévő adatmennyiségtől függően. Ez idő alatt az alkalmazás a szokásos olvasási késésnél magasabb szintű élményt nyújt, mivel egyes olvasási műveletek az eredeti helyre lesznek átirányítva, és hosszabb ideig tarthatnak. Ebben az időszakban nincs hatással az írási késésre.  

**Milyen módosításokra van szükség egy korábban létező Azure Backup szolgáltatás-konfigurációban a Managed Disks áttelepítése előtt/után?**

Nincs szükség módosításra.

**A virtuális gépek biztonsági mentései a Azure Backup szolgáltatáson keresztül jöttek létre, mielőtt az áttelepítés továbbra is működni fog?**

Igen, a biztonsági mentések zökkenőmentesen működnek.

**Milyen módosításokra van szükség a meglévő Azure-lemezek titkosítási konfigurációjában a Managed Disks áttelepítése előtt/után?**

Nincs szükség módosításra.

**Egy meglévő virtuálisgép-méretezési csoport automatikus áttelepítése nem felügyelt lemezekről Managed Disks támogatott?**

Nem. Létrehozhat egy új méretezési készletet Managed Disks használatával a régi méretezési csoportból a nem felügyelt lemezekkel rendelkező rendszerképpel.

**Létrehozhatok felügyelt lemezt egy blob-pillanatképből, amelyet a rendszer a Managed Disksba való Migrálás előtt hozott létre?**

Nem. Az oldal blob-pillanatképét oldal blobként is exportálhatja, majd létrehozhatja a felügyelt lemezt az exportált oldal blobból.

**Átadhatom a Azure Site Recovery által védett helyszíni gépeket Managed Disks-vel rendelkező virtuális géphez?**

Igen, választhatja a feladatátvételt egy virtuális gépre Managed Disks használatával.

**Van-e hatással az Azure-ban Azure Site Recovery által védett Azure-beli virtuális gépekre az Azure-ba történő áttelepítés?**

Nem. Azure Site Recovery Azure-ról Azure-ra, Managed Disks elérhető virtuális gépekre.

**Áttelepíthetem a virtuális gépeket olyan nem felügyelt lemezekkel, amelyek olyan tárolási fiókokon találhatók, amelyek korábban felügyelt lemezekre lettek titkosítva?**

Igen

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks és Storage Service Encryption

**A felügyelt lemez létrehozásakor alapértelmezés szerint engedélyezve van-e az Azure Storage Service Encryption?**

Igen.

**A rendszerindító kötetet alapértelmezés szerint titkosították a felügyelt lemezeken?**

Igen. Alapértelmezés szerint minden felügyelt lemez titkosítva van, beleértve az operációsrendszer-lemezt is.

**Kik kezelik a titkosítási kulcsokat?**

A Microsoft kezeli a titkosítási kulcsokat.

**Letilthatom Storage Service Encryption a felügyelt lemezeken?**

Nem.

**Storage Service Encryption csak bizonyos régiókban érhető el?**

Nem. Ez minden olyan régióban elérhető, ahol elérhetők a Managed Disks. Managed Disks minden nyilvános régióban és Németországban elérhető. Azonban Kínában is elérhető, csak a Microsoft által felügyelt kulcsokhoz, nem pedig az ügyfél által felügyelt kulcsokhoz.

**Hogyan állapítható meg, hogy a felügyelt lemez titkosított-e?**

Megtudhatja, hogy mikor jött létre felügyelt lemez a Azure Portal, az Azure CLI és a PowerShell használatával. Ha az idő a 2017. június 9. után van, akkor a lemez titkosítva lesz.

**Hogyan lehet titkosítani a 2017. június 10. előtt létrehozott meglévő lemezeket?**

2017. június 10-ig a meglévő felügyelt lemezekre írt új adatmennyiségek automatikusan titkosítva vannak. Azt is tervezzük, hogy a meglévőket titkosítani fogjuk, és a titkosítás aszinkron módon történik a háttérben. Ha most titkosítania kell a meglévő adatait, hozzon létre egy másolatot a lemezről. Az új lemezek titkosítva lesznek.

* [Felügyelt lemezek másolása az Azure CLI használatával](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [Felügyelt lemezek másolása a PowerShell használatával](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**A felügyelt Pillanatképek és lemezképek titkosítva vannak?**

Igen. A 2017. június 9. után létrehozott összes felügyelt pillanatkép és rendszerkép automatikusan titkosítva lesz. 

**Átalakíthatók-e a virtuális gépek olyan nem felügyelt lemezekkel, amelyek olyan Storage-fiókokon találhatók, amelyek korábban felügyelt lemezekre lettek titkosítva?**

Igen

**Egy felügyelt lemezről exportált virtuális merevlemez vagy egy pillanatkép is titkosítva lesz?**

Nem. Ha azonban egy titkosított, felügyelt lemezről vagy pillanatképről exportál egy VHD-t egy titkosított Storage-fiókba, akkor titkosítva van. 

## <a name="premium-disks-managed-and-unmanaged"></a>Prémium szintű lemezek: felügyelt és nem felügyelt

**Ha egy virtuális gép olyan méretű adatsorozatot használ, amely támogatja a prémium SSD lemezeket, például a DSv2, a prémium és a standard szintű adatlemezeket is csatlakoztatni kell?** 

Igen.

**A prémium és a standard szintű adatlemezek is csatolhatók olyan méretezési sorozathoz, amely nem támogatja a prémium SSD lemezeket, például a D, a Dv2, a G vagy az F sorozatot?**

Nem. Csak szabványos adatlemezeket lehet csatlakoztatni olyan virtuális gépekhez, amelyek nem használnak prémium SSD lemezeket támogató méretezési sorozatot.

**Mennyibe kerül a prémium adatlemez egy 80 GB-os meglévő VHD-ről?**

A 80 GB-os virtuális merevlemezről létrehozott prémium szintű adatlemezek a következő elérhető prémium lemez méretének megfelelően vannak kezelve, amely egy P10-lemez. A díjat a P10-lemez díjszabása szerint számítjuk fel.

**Vannak tranzakciós költségek a prémium SSD lemezek használatához?**

Az egyes lemezek méretének fix díja, amely a IOPS és az átviteli sebesség specifikus korlátaival van kiépítve. A többi költség a kimenő sávszélesség és a pillanatkép kapacitása, ha van ilyen. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/storage) olvasható.

**Milyen korlátozások vonatkoznak a IOPS és az átviteli sebességre?**

A DS-sorozatok gyorsítótárának és helyi SSD-tartalmának összesített korlátai a következők: 4 000 IOPS/mag és 33 MiB/mp. A GS-sorozat 5 000 IOPS/mag és 50 MiB másodpercenkénti számát kínálja.

**Támogatott a helyi SSD Managed Disks virtuális gép számára?**

A helyi SSD a Managed Disks virtuális géphez tartozó ideiglenes tároló. Ehhez az ideiglenes tárhelyhez nem jár extra díj. Azt javasoljuk, hogy ne használja ezt a helyi SSD-t az alkalmazásadatok tárolására, mert az nem marad meg az Azure Blob Storage-ban.

**Vannak-e a prémium szintű lemezeken található TRIM használatára vonatkozó következmények?**

Az Azure-lemezeken a prémium és a standard szintű lemezek esetében nincs hátránya a VÁGÁSnak.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>Új lemezek mérete: felügyelt és nem felügyelt

**Mely régiók támogatják a kitörési képességet a megfelelő prémium szintű SSD-lemez méretéhez?**

A burst képesség jelenleg az USA nyugati középső régiójában támogatott.

**Mely régiókban támogatott a 4/8/16 GiB által felügyelt lemez mérete (P1/P2/P3, E1/E2/E3)?**

Az új lemezes méretek jelenleg az USA nyugati középső régiójában támogatottak.

**Támogatottak-e a P1/P2/P3 méretű lemezek a nem felügyelt lemezekhez vagy a blobokhoz?**

Nem, csak a prémium szintű SSD által felügyelt lemezeken támogatott. 

**Támogatottak-e az E1/E2/E3 lemezes méretek a nem felügyelt lemezek vagy Blobok esetében?**

Nem, a standard SSD által felügyelt lemezek bármilyen méretűek, nem használhatók nem felügyelt lemezekkel vagy Blobokkal.

**Mi a legnagyobb felügyelt lemez mérete az operációs rendszer és az adatlemezek esetében?**

Az Azure által az operációsrendszer-lemezre támogatott partíció típusa a fő rendszertöltő rekord (MBR). Az MBR formátuma legfeljebb 2 TiB méretű lemez használatát támogatja. Az Azure által az operációsrendszer-lemez számára támogatott legnagyobb méret 2 TiB. Az Azure a felügyelt adatlemezek esetében akár 32 TiB-t is támogat.

**Mi az operációs rendszer és az adatlemezek esetében támogatott legnagyobb, nem felügyelt lemez mérete?**

Az Azure által az operációsrendszer-lemezre támogatott partíció típusa a fő rendszertöltő rekord (MBR). Az MBR formátuma legfeljebb 2 TiB méretű lemez használatát támogatja. Az Azure által az operációs rendszer nem felügyelt lemezéhez támogatott legnagyobb méret 2 TiB. Az Azure legfeljebb 4 TiB-t támogat a nem felügyelt lemezekhez.

**Mi az a legnagyobb oldal blob-méret, amelyet támogat?**

Az Azure által támogatott legnagyobb blob-méret 8 TiB (8 191 GiB). A Blobok maximális mérete, ha egy virtuális géphez csatolt adatként vagy operációsrendszer-lemezként van 4 TiB (4 095 GiB).

**Az 1 TiB-nál nagyobb lemezek létrehozásához, csatolásához, átméretezéséhez és feltöltéséhez az Azure Tools új verzióját kell használom?**

Nem kell frissítenie meglévő Azure-eszközeit az 1 TiB-nál nagyobb lemezek létrehozásához, csatolásához vagy átméretezéséhez. Ha a VHD-fájlt helyszíni blobként vagy nem felügyelt lemezként közvetlenül az Azure-ba szeretné feltölteni, akkor az alább felsorolt legújabb eszközkészleteket kell használnia. Legfeljebb 8 TiB-os VHD-feltöltést támogatunk.

|Azure-eszközök      | Támogatott verziók                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | Verziószám 4.1.0: június 2017 vagy újabb kiadás|
|Azure CLI v1     | Verziószám 0.10.13: május 2017 vagy újabb kiadás|
|Azure CLI v2     | Verziószám 2.0.12: július 2017 vagy újabb kiadás|
|AzCopy           | Verziószám 6.1.0: június 2017 vagy újabb kiadás|

**Támogatottak-e a P4 és a P6 a nem felügyelt lemezek vagy Blobok esetén?**

A P4 (32 GiB) és a P6 (64 GiB) lemez mérete nem támogatott a nem felügyelt lemezek és Blobok alapértelmezett lemezes szintjeiként. Explicit módon [be kell állítania a blob szintjét](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) a P4-re és a P6, hogy a lemez leképezve legyen ezekhez a rétegekhöz. Ha nem felügyelt lemezt vagy blobot telepít a lemez méretével vagy a tartalom hosszával, amely kisebb, mint 32 GiB, vagy a 32 GiB és a 64 GiB között a blob-csomag beállítása nélkül, továbbra is a P10 a 500 IOPS és a 100 MiB/s és a leképezett díjszabási csomaggal.

**Ha a meglévő prémium szintű felügyelt lemez kevesebb, mint 64 GiB lett létrehozva a kisméretű lemez engedélyezése előtt (2017. június 15.), hogyan történik a számlázás?**

A meglévő kisméretű, 64 GiB-nál kisebb prémium szintű lemezeket a P10 díjszabási szintjének megfelelően számítjuk fel.

**Hogyan válthatok be a kisméretű prémium lemezeken a 64 GiB-nál kisebb méretű, P10-ról P4-re vagy P6-re?**

Készíthet pillanatképet a kislemezekről, majd létrehozhat egy lemezt, amely automatikusan átváltja az árképzési szintet P4-re vagy P6-re a kiosztott méret alapján.

**Átméretezheti a meglévő Managed Diskseket a 4 tebibájt (TiB)-nál kevesebb méretről az újonnan bevezetett lemezekre akár 32 TiB-ra is?**

Igen.

**Melyek a Azure Backup és Azure Site Recovery szolgáltatás által támogatott legnagyobb méretű lemezek?**

Azure Backup által támogatott legnagyobb méretű lemez mérete 32 TiB (4 TiB titkosított lemezekhez). Azure Site Recovery által támogatott legnagyobb méretű lemez 8 TiB. A nagyobb lemezek támogatása a 32 TiB-ig még nem érhető el Azure Site Recoveryban.

**Az optimalizált lemezes IOPS és a sávszélesség eléréséhez az ajánlott virtuálisgép-méretek (> 4 TiB) standard SSD és standard HDD lemezek esetében**

A standard SSD lemez átviteli sebességének eléréséhez és a nagy méretű lemezek standard HDD (> 4 TiB) a 500 IOPS és a 60 MiB/s felett javasoljuk, hogy a következő virtuálisgép-méretek egyikével telepítsen egy új virtuális gépet a teljesítmény optimalizálása érdekében: B sorozat, DSv2 sorozat, Dsv3 sorozat, ESv3 sorozat, FS sorozat, Fsv2 sorozat, M sorozat, GS-sorozat, NCv2 sorozat, NCv3 sorozat vagy ls sorozatú virtuális gépek. Ha nagyméretű lemezeket csatlakoztat a meglévő virtuális gépekhez vagy olyan virtuális gépekhez, amelyek nem a fentiekben javasolt méreteket használják, alacsonyabb teljesítményt tapasztalhatnak.

**Hogyan frissíthetem a lemezeket (> 4 TiB), amelyek a nagyobb méretű lemezes méretek előzetes verziójában lettek telepítve, hogy a magasabb IOPS & sávszélességet a GA-ban?**

Leállíthatja és elindíthatja azt a virtuális gépet, amelyhez a lemez csatlakoztatva van, vagy leválasztja és újra csatlakoztatja a lemezt. A nagyobb méretű lemezek teljesítménybeli célpontjai a Premium SSD-k és a standard SSD-k esetében is megnövekedtek a GA-ban.

**Milyen régiókban támogatott a felügyelt lemezek mérete 8 TiB, 16 TiB és 32 TiB esetén?**

A 8 TiB, 16 TiB és 32 TiB Lemezes SKU minden régióban támogatott a globális Azure, a Microsoft Azure Government és az Azure China 21Vianet.

**Támogatjuk a gazdagépek gyorsítótárazásának engedélyezését az összes lemez méretén?**

Támogatjuk a gazdagépek írásvédett és olvasási/írási gyorsítótárazását a 4 TiB-nál kisebb méretű lemezeken. Ha a lemezek mérete meghaladja a 4 TiB-ot, a nincs beállításnál nem támogatott a gyorsítótárazás. Javasoljuk, hogy a kisebb méretű lemezek gyorsítótárazását olyankor is érdemes kihasználni, ahol a virtuális géphez gyorsítótárazott adatoknál nagyobb teljesítménybeli növekedést várhat.

## <a name="what-if-my-question-isnt-answered-here"></a>Mi a teendő, ha nem válaszolt a kérdésre?

Ha a kérdés nem szerepel a listán, tudassa velünk, és segítünk megtalálni a választ. A megjegyzésekben a cikk végén teheti közzé a kérdést. Az Azure Storage csapata és a jelen cikk más közösségi tagjainak részvételéhez használja az MSDN [Azure Storage fórumot](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).

A szolgáltatások igényléséhez küldje el kérelmeit és ötleteit az [Azure Storage visszajelzési fórumára](https://feedback.azure.com/forums/217298-storage).
