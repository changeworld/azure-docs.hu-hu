---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: a028a0b5d79b2c79f1da336f033d3e8cac21a2e2
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474176"
---
A megosztott képkatalógus egy olyan szolgáltatás, amely segít felépíteni a felügyelt lemezképekre épülő struktúrát és szervezetet. A megosztott képtárak a következőket biztosítják:

- A lemezképek felügyelt globális replikálása.
- A könnyebb felügyelet érdekében a rendszerképek verziószámozása és csoportosítása.
- A Availability Zones-t támogató régiókban található, a zóna redundáns tárolási (ZRS) fiókjaival rendelkező, nagyon elérhető rendszerképek. A ZRS nagyobb rugalmasságot biztosít a zónabeli hibákkal szemben.
- Megosztás az előfizetések között, és akár Active Directory (AD) bérlők között a RBAC használatával.
- Az üzembe helyezések skálázása minden egyes régióban képreplikákkal.

Megosztott képkatalógus használatával a képeket megoszthatja a szervezeten belüli különböző felhasználókkal, egyszerű szolgáltatásokkal vagy AD-csoportokkal. A megosztott lemezképek több régióba is replikálhatók, így az üzemelő példányok gyorsabban méretezhetők.

A felügyelt lemezképek egy teljes virtuális gép (beleértve a csatlakoztatott adatlemezeket is) vagy csak az operációsrendszer-lemez másolata, attól függően, hogyan hozza létre a lemezképet. Amikor létrehoz egy virtuális gépet a lemezképből, a lemezképben található virtuális merevlemezek egy másolatát használják az új virtuális gép lemezeinek létrehozásához. A felügyelt rendszerkép a tárolóban marad, és újra felhasználható új virtuális gépek létrehozásához.

Ha nagy számú felügyelt lemezképet kell fenntartania, és a vállalaton belül elérhetővé szeretné tenni őket, a megosztott képtárat tárházként használhatja, amely megkönnyíti a képek megosztását. 

A megosztott képkatalógus funkció több erőforrástípust tartalmaz:

| Erőforrás | Leírás|
|----------|------------|
| **Felügyelt rendszerkép** | Olyan alapszintű rendszerkép, amely önmagában vagy rendszerkép- **verzió** létrehozásához használható képgyűjteményben. A felügyelt lemezképek [általánosított](#generalized-and-specialized-images) virtuális gépekről jönnek létre. A felügyelt rendszerkép olyan speciális VHD-típus, amellyel több virtuális gép hozható létre, és most már használható a megosztott rendszerkép-verziók létrehozásához is. |
| **Pillanatkép** | Egy virtuális merevlemez másolata, amely alkalmas a **rendszerkép verziójának**elkészítésére. A pillanatképek olyan [speciális](#generalized-and-specialized-images) virtuális gépekből is készíthetők (amelyek nem lettek általánosítva), majd önállóan vagy adatlemez-pillanatképekkel is használhatók a speciális rendszerkép-verziók létrehozásához.
| **Képtár** | Az Azure Marketplace-hez hasonlóan a képkatalógus **is a lemezképek** kezeléséhez és megosztásához használható tárház, de Ön szabályozhatja, hogy ki férhet hozzá. |
| **Rendszerkép definíciója** | A lemezképek a katalógusban vannak definiálva, és a rendszerképekkel és a szervezeten belüli használattal kapcsolatos követelményekkel kapcsolatos információkat hordoznak. Olyan információkat is tartalmazhat, mint például a rendszerkép általánosított vagy specializált, az operációs rendszer, a minimális és a maximális memória követelményei, valamint a kibocsátási megjegyzések. Ez egy adott típusú rendszerkép definíciója. |
| **Rendszerkép verziója** | A **rendszerkép verziója** az, amit a virtuális gép létrehozásához használ gyűjtemény létrehozásakor. A környezethez szükség lehet a rendszerkép több verziójára. A felügyelt rendszerképekhez hasonlóan, amikor **rendszerkép-verziót** használ egy virtuális gép létrehozásához, a rendszerkép verziója a virtuális gép új lemezének létrehozására szolgál. A rendszerkép verziója többször is használható. |

<br>

![Ábra, amely bemutatja, hogyan lehet egy rendszerkép több verziója is a galériában](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Rendszerkép-definíciók

A rendszerkép-definíciók egy adott rendszerkép verzióihoz tartozó logikai Csoportosítások. A rendszerkép definíciója információt tartalmaz arról, hogy a rendszerkép hogyan lett létrehozva, melyik operációs rendszer, és milyen információkkal szolgál a rendszerkép használatáról. A rendszerkép definíciója olyan, mint egy csomag, amely az adott rendszerkép létrehozásával kapcsolatos összes részletre vonatkozik. Nem telepít virtuális gépet lemezkép-definícióból, hanem a definícióból létrehozott rendszerkép-verzióból.

Az egyes képdefiníciók három paramétert használnak a **Publisherben**, az **ajánlatban** és az **SKU**-ban. Ezek egy adott rendszerkép-definíció megtalálására szolgálnak. Rendelkezhet egy vagy két, de nem mindhárom értékkel rendelkező képverzióval is.  Íme például három képdefiníció és értékeik:

|Rendszerkép-definíció|Közzétevő|Ajánlat|SKU|
|---|---|---|---|
|myImage1|Contoso|Pénzügy|Backend|
|myImage2|Contoso|Pénzügy|Frontend|
|myImage3|Tesztelés|Pénzügy|Frontend|

Mindhárom ilyen egyedi értéket tartalmaz. A formátum hasonló ahhoz, ahogyan jelenleg az [Azure Marketplace-lemezképekhez](../articles/virtual-machines/windows/cli-ps-findimage.md) tartozó közzétevőt, ajánlatot és SKU-t megadhatja Azure PowerShell a Piactéri lemezkép legújabb verziójának beszerzéséhez. Minden rendszerkép-definíciónak egyedi készlettel kell rendelkeznie ezeknek az értékeknek.

A következő más paraméterek is megadhatók a képdefinícióban, így könnyebben nyomon követheti az erőforrásokat:

* Operációs rendszer állapota – [általánosított vagy specializált](#generalized-and-specialized-images)operációsrendszer-állapotot állíthat be.
* Operációs rendszer – lehet Windows vagy Linux.
* Leírás – a Leírás használatával részletesebb információkat adhat meg arról, hogy miért létezik a rendszerkép definíciója. Előfordulhat például, hogy rendelkezik egy rendszerkép-definícióval az előtér-kiszolgálóhoz, amelyen az alkalmazás előre telepítve van.
* Végfelhasználói licencszerződés – a rendszerkép-definícióra vonatkozó végfelhasználói licencszerződésre mutathat.
* Adatvédelmi nyilatkozat és kibocsátási megjegyzések – a kibocsátási megjegyzések és az adatvédelmi nyilatkozatok tárolása az Azure Storage-ban, valamint egy URI megadása a rendszerkép definíciójának részeként való hozzáféréshez.
* Élettartam dátuma – a rendszerkép definíciójának élettartama után az Automation használatával törölheti a régi képdefiníciókat.
* Címke – címkéket adhat hozzá a rendszerkép definíciójának létrehozásakor. További információ a címkékkel kapcsolatban: [címkék használata az erőforrások rendszerezéséhez](../articles/azure-resource-manager/management/tag-resources.md)
* Minimális és maximális vCPU és memória-javaslatok – ha a rendszerképnek van vCPU-és memória-javaslata, csatolhatja ezeket az információkat a rendszerkép-definícióhoz.
* Nem engedélyezett lemezek típusai – a virtuális gép tárolási igényeivel kapcsolatos információkat adhat meg. Ha például a rendszerkép nem szabványos HDD-lemezekhez van kiválasztva, akkor azokat a letiltási listához adja.

## <a name="generalized-and-specialized-images"></a>Általánosított és speciális rendszerképek

A megosztott rendszerkép-katalógusban két operációsrendszer-állapot támogatott. Általában a lemezképek megkövetelik, hogy a rendszerkép létrehozásához használt virtuális gép általánosítva legyen a lemezkép készítése előtt. Az általánosítás egy olyan folyamat, amely eltávolítja a gép és a felhasználó által megadott adatokat a virtuális gépről. Windows rendszeren a Sysprep is használatban van. A Linux rendszerben [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` vagy `-deprovision+user` paramétereket is használhat.

A speciális virtuális gépek nem a gép specifikus információinak és fiókjainak eltávolítására szolgáló folyamattal rendelkeznek. Emellett a speciális lemezképből létrehozott virtuális gépekhez nincs `osProfile` társítva. Ez azt jelenti, hogy a speciális lemezképek bizonyos korlátozásokkal rendelkeznek.

- A virtuális gépre való bejelentkezéshez használható fiókok az adott virtuális gépről létrehozott speciális rendszerkép használatával létrehozott virtuális gépeken is használhatók.
- A virtuális gépek neve annak a virtuális gépnek a **számítógépnevét** fogja tartalmazni, amelyet a rendszerkép készített. Az ütközések elkerülése érdekében módosítsa a számítógép nevét.
- A `osProfile`, hogy a virtuális gép hogyan adja át a bizalmas adatokat a `secrets`használatával. Ez problémákat okozhat a kulcstartó, a WinRM és a `osProfile``secrets`t használó egyéb funkciók használatával. Bizonyos esetekben a felügyelt szolgáltatásbeli identitások (MSI) használatával is megkerülheti ezeket a korlátozásokat.

> [!IMPORTANT]
> A speciális lemezképek jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Ismert előzetes verzió korlátai** A virtuális gépeket csak speciális rendszerképekből lehet létrehozni a portál vagy az API használatával. A nem támogatja a CLI-t vagy a PowerShellt az előzetes verzióhoz.


## <a name="regional-support"></a>Regionális támogatás

A forrás régiói az alábbi táblázatban láthatók. Az összes nyilvános régió lehet célcsoport, de az ausztráliai közép-és ausztráliai Közép-Ausztrália 2-es verzióra való replikáláshoz az előfizetés engedélyezési listának kell lennie. A engedélyezési listához lépjen a következőre: https://azure.microsoft.com/global-infrastructure/australia/contact/


| Forrásoldali régiók        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Ausztrália középső régiója     | Kelet-Kína        | Dél-India        | Nyugat-Európa        |
| Ausztrália 2. középső régiója   | Kelet-Kína 2      | Délkelet-Ázsia     | Az Egyesült Királyság déli régiója           |
| Kelet-Ausztrália        | Észak-Kína       | Kelet-Japán         | Az Egyesült Királyság nyugati régiója            |
| Délkelet-Ausztrália   | Észak-Kína 2     | Nyugat-Japán         | US DoD – Középső régió     |
| Dél-Brazília          | Kelet-Ázsia         | Dél-Korea középső régiója      | US DoD – Kelet        |
| Közép-Kanada        | USA keleti régiója           | Dél-Korea déli régiója        | USA-beli államigazgatás – Arizona     |
| Kelet-Kanada           | USA 2. keleti régiója         | USA északi középső régiója   | USA-beli államigazgatás – Texas       |
| Közép-India         | USA 2. keleti – EUAP    | Észak-Európa       | USA-beli államigazgatás – Virginia    |
| USA középső régiója            | Közép-Franciaország    | USA déli középső régiója   | Nyugat-India         |
| USA középső – EUAP       | Dél-Franciaország      | USA nyugati középső régiója    | USA nyugati régiója            |
|                       |                   |                    | USA nyugati régiója, 2.          |



## <a name="limits"></a>Korlátok 

Az erőforrások megosztott képtárakkal való üzembe helyezéséhez korlátok, előfizetések vonatkoznak:
- 100 megosztott képtárak, előfizetések száma régiónként
- 1 000 képdefiníciók, előfizetések régiónként
- 10 000 képverzió, előfizetések száma régiónként
- A lemezképhez csatolt minden lemeznek 1 TB-nál kisebbnek vagy azzal egyenlőnek kell lennie

További információ: az [erőforrás-használat korlátainak korlátozása](https://docs.microsoft.com/azure/networking/check-usage-against-limits) példákkal a jelenlegi használat ellenõrzéséhez.
 
## <a name="scaling"></a>Méretezés
A megosztott képkatalógus segítségével megadhatja, hogy az Azure hány replikát őrizzen meg a képeken. Ez segítséget nyújt a több virtuális gépre kiterjedő üzembe helyezési forgatókönyvekben, mivel a virtuális gépek központi telepítése különböző replikák számára lehetséges, ami csökkenti annak a valószínűségét, hogy egy replika túlterhelése miatt szabályozható a példány-létrehozási feldolgozás.

A megosztott képtárat mostantól egy virtuálisgép-méretezési csoportba helyezheti üzembe egy 1 000-es virtuálisgép-példányon (a 600-ból felügyelt lemezképekkel). A képreplikák jobb teljesítményt, megbízhatóságot és konzisztenciát biztosítanak a központi telepítéshez.  Az egyes célcsoportok között különböző replikákat állíthat be, a régió skálázási igényei alapján. Mivel minden replika a rendszerkép egy részletes másolata, ez segít az üzembe helyezések lineárisan történő méretezésében minden további replikával. Habár a két rendszerkép vagy régió nem egyezik, a következő általános útmutatást láthatjuk, hogyan használhat replikákat egy régióban:

- Nem virtuálisgép-méretezési csoport (VMSS) esetén – minden olyan 20 virtuális gép esetében, amelyet egyszerre hoz létre, javasoljuk, hogy tartsa meg az egyik replikát. Ha például 120 virtuális gépet hoz létre egyidejűleg ugyanazon rendszerkép használatával egy régióban, javasoljuk, hogy legalább 6 replikát őrizzen meg a rendszerképből. 
- A virtuálisgép-méretezési csoport (VMSS) üzembe helyezése esetén – a legfeljebb 600 példánnyal rendelkező méretezési csoportokhoz legalább egy replikát érdemes megőrizni. Ha például egyszerre 5 méretezési csoportot hoz létre, és mindegyik 600 virtuálisgép-példánnyal ugyanazt a rendszerképet használja egyetlen régióban, javasoljuk, hogy legalább 5 replikát őrizzen meg a rendszerképből. 

Mindig azt javasoljuk, hogy a replikák számát a képméret, a tartalom és az operációs rendszer típusa miatt ne felépítse.

![A képek méretezését bemutató ábra](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Képek nagyfokú elérhetővé tétele

Az [Azure Zone redundáns tároló (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) rugalmasságot biztosít a rendelkezésre állási zóna meghibásodása esetén a régióban. A megosztott képkatalógus általános elérhetősége mellett dönthet úgy, hogy a rendszerképeket a ZRS-fiókokban tárolja a Availability Zones régiókban. 

Kiválaszthatja az egyes célcsoportok fiókjának típusát is. Az alapértelmezett Storage-fióktípus Standard_LRS, de az Availability Zones-vel rendelkező régiók esetében Standard_ZRS is választhat. A ZRS regionális elérhetőségét [itt](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)tekintheti meg.

![ZRS mutató ábra](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replikáció
A megosztott képkatalógus lehetővé teszi a képek más Azure-régiókba történő automatikus replikálását is. Az egyes megosztott rendszerkép-verziók különböző régiókba replikálhatók, attól függően, hogy mi értelme van a szervezetnek. Az egyik példa az, hogy mindig replikálja a legújabb rendszerképet több régióban, míg az összes régebbi verzió csak 1 régióban érhető el. Ez a megosztott rendszerkép-verziók tárolási költségeinek megtakarítását is lehetővé teheti. 

Az a régió, amelyet a megosztott rendszerkép replikál, a rendszer a létrehozási idő után frissítheti. A különböző régiókba való replikáláshoz szükséges idő a másolt adatok mennyiségétől és a verzió által replikált régiók számától függ. Bizonyos esetekben ez néhány órát is igénybe vehet. A replikáció során régiónként is megtekintheti a replikálás állapotát. Ha a lemezkép replikálása egy régióban fejeződött be, akkor az adott lemezkép verziójának használatával üzembe helyezhet egy virtuális gépet vagy méretezési készletet a régióban.

![A képek replikálásának módját bemutató ábra](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Access

Mivel a megosztott képtára, a képdefiníció és a rendszerkép verziója minden erőforrás, a beépített natív Azure RBAC-vezérlőkkel is megoszthatók. A RBAC használatával ezeket az erőforrásokat megoszthatja más felhasználókkal, egyszerű szolgáltatásokkal és csoportokkal is. Akár a bérlőn kívüli személyekhez is megoszthatja a hozzáférést. Miután egy felhasználó hozzáfér a megosztott lemezkép verziójához, üzembe helyezhet egy virtuális gépet vagy egy virtuálisgép-méretezési készletet.  Itt látható a megosztási mátrix, amely segít megérteni, hogy a felhasználó milyen módon férhet hozzá:

| Felhasználóval megosztva     | Megosztott rendszerkép-katalógus | Rendszerkép-definíció | Rendszerképverzió |
|----------------------|----------------------|--------------|----------------------|
| Megosztott rendszerkép-katalógus | Igen                  | Igen          | Igen                  |
| Rendszerkép-definíció     | Nem                   | Igen          | Igen                  |

A legjobb megoldás érdekében javasoljuk, hogy ossza meg a gyűjteményt a katalógus szintjén. Az egyes rendszerkép-verziók megosztását nem ajánlott. További információ a RBAC: az [Azure-erőforrásokhoz való hozzáférés kezelése a RBAC használatával](../articles/role-based-access-control/role-assignments-portal.md).

A képek a több-bérlős alkalmazások regisztrálásával is megoszthatók, akár a bérlők között is. A képek bérlők közötti megosztásával kapcsolatos további információkért lásd: katalógus virtuálisgép- [rendszerképeinek megosztása az Azure-bérlők között](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Számlázás
A megosztott rendszerkép-katalógus szolgáltatás használata nem díjköteles. A következő erőforrásokért kell fizetnie:
- A megosztott rendszerkép-verziók tárolásának tárolási költségei. A díj a rendszerkép-verzió replikáinak számától és azon régiók számától függ, amelyre a verzió replikálódik. Ha például két lemezkép van, és mindkettő 3 régióba van replikálva, akkor a méretük alapján 6 felügyelt lemez után kell fizetnie. További információ: [Managed Disks díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/).
- A hálózati kimenő forgalom díja a forrás régióból a replikált régiókba való első lemezkép-verzió replikálásához. A későbbi replikákat a régión belül kezeljük, így nincs további díj. 

## <a name="updating-resources"></a>Erőforrások frissítése

A létrehozás után módosításokat végezhet a Képtár erőforrásaiban. Ezek a következőkre korlátozódnak:
 
Megosztott képgyűjtemény:
- Leírás

Rendszerkép definíciója:
- Ajánlott vCPU
- Ajánlott memória
- Leírás
- Élettartam vége

Rendszerkép verziója:
- Regionális replika száma
- Célcsoportok
- Legutóbbi kizárás
- Élettartam vége

## <a name="sdk-support"></a>SDK-támogatás

A következő SDK-k támogatják a megosztott képtárak létrehozását:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Sablonok

Létrehozhat megosztott képkatalógus-erőforrást sablonok használatával. Több Azure Gyorsindítás-sablon is elérhető: 

- [Megosztott Képtár létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Rendszerkép-definíció létrehozása megosztott rendszerkép-gyűjteményben](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Rendszerkép-verzió létrehozása megosztott rendszerkép-gyűjteményben](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása rendszerkép-verzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Gyakori kérdések 

* [Hogyan lehet listázni az összes megosztott képgyűjtemény erőforrását az előfizetések között?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Áthelyezhetem a meglévő rendszerképet a megosztott képgalériába?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Létrehozhatok lemezkép-verziót egy speciális lemezről?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Áthelyezhetem a megosztott rendszerkép-katalógus erőforrását egy másik előfizetésbe a létrehozása után?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Replikálható a képverziók a felhők között, például az Azure China 21Vianet vagy az Azure Germany vagy a Azure Government Cloud?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Replikálható a rendszerképem verziója az előfizetések között?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Megoszthatom a képverziókat az Azure AD-bérlők között?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Mennyi ideig tart a képverziók replikálása a célcsoportok között?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Mi a különbség a forrásoldali régió és a célcsoport között?](#what-is-the-difference-between-source-region-and-target-region)
* [Hogyan megadnia a forrás régiót a rendszerkép verziójának létrehozásakor?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Hogyan az egyes régiókban létrehozandó rendszerkép-verziók replikáinak számát?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Létrehozhatom a megosztott képtárat egy másik helyen, mint a rendszerkép-definíció és a rendszerkép verziója?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Milyen díjak vonatkoznak a megosztott képtárat használó szolgáltatásokra?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Milyen API-verziót érdemes használni a megosztott rendszerkép-katalógus és a rendszerkép-definíció és a rendszerkép-verzió létrehozásához?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Milyen API-verziót használhatok a rendszerkép verziójának használatával létrehozott megosztott virtuális gépek vagy virtuálisgép-méretezések létrehozásához?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Hogyan lehet listázni az összes megosztott képgyűjtemény erőforrását az előfizetések között?

Az alábbi lépéseket követve listázhatja az összes megosztott képkatalógus-erőforrást az előfizetések között, amelyekhez hozzáféréssel rendelkezik a Azure Portalban:

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
1. Nyissa meg az **összes erőforrást**.
1. Válassza ki az összes olyan előfizetést, amelyben az összes erőforrást listázni szeretné.
1. Keressen a **privát**katalógus típusú erőforrásokat.
 
   A képdefiníciók és a képfájlok megjelenítéséhez válassza a **rejtett típusok megjelenítése**lehetőséget is.
 
   Ha az összes olyan előfizetésben szeretné listázni az összes megosztott képkatalógus-erőforrást, amelyre jogosult, használja a következő parancsot az Azure CLI-ben:

   ```bash
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
   ```

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Áthelyezhetem a meglévő rendszerképet a megosztott képgalériába?
 
Igen. A lemezképek típusain alapuló 3 forgatókönyv van.

 1\. forgatókönyv: Ha a SIG-vel megegyező előfizetésben felügyelt rendszerkép található, akkor a rendszerkép definícióját és a rendszerkép verzióját is létrehozhatja.

 2\. forgatókönyv: Ha nem felügyelt képpel rendelkezik a SIG-vel megegyező előfizetésben, létrehozhat egy felügyelt rendszerképet, majd létrehozhatja a rendszerkép definícióját és a rendszerkép verzióját. 

 3\. forgatókönyv: Ha a helyi fájlrendszerben virtuális merevlemezt használ, fel kell töltenie a virtuális merevlemezt egy felügyelt rendszerképbe, majd a rendszerkép-definíciót és a rendszerkép verzióját is létrehozhatja belőle.

- Ha a VHD egy Windows rendszerű virtuális gép, tekintse meg [a virtuális merevlemez feltöltése](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)című témakört.
- Ha a virtuális merevlemez Linux rendszerű virtuális gépen van, lásd: [virtuális merevlemez feltöltése](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Létrehozhatok lemezkép-verziót egy speciális lemezről?

Igen, a speciális lemezek támogatása előzetes verzióban érhető el. A portálon ([Windows](../articles/virtual-machines/linux/shared-images-portal.md) vagy [Linux](../articles/virtual-machines/linux/shared-images-portal.md)) és az API-val csak egy speciális RENDSZERKÉPBŐL hozhat létre virtuális gépet. Az előzetes verzióhoz nem érhető el PowerShell-támogatás.

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Áthelyezhetem a megosztott rendszerkép-katalógus erőforrását egy másik előfizetésbe a létrehozása után?

Nem, a megosztott rendszerkép-katalógus erőforrását nem lehet másik előfizetésbe áthelyezni. A katalógusban lévő rendszerképeket azonban szükség szerint más régiókban is replikálhatja.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Replikálható a képverziók a felhők között, például az Azure China 21Vianet vagy az Azure Germany vagy a Azure Government Cloud?

Nem, a képverziókat nem lehet a felhők között replikálni.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Replikálható a rendszerképem verziója az előfizetések között?

Nem, a képverziókat replikálhatja egy előfizetésben lévő régiók között, és más előfizetésekben is használhatja a RBAC-on keresztül.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Megoszthatom a képverziókat az Azure AD-bérlők között? 

Igen, a RBAC használatával a bérlők között oszthat meg személyeket. Ha azonban a méretezést szeretné megosztani, tekintse meg a "katalógus-rendszerképek megosztása Azure-bérlők között" részt a [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) vagy a [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md)használatával.

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Mennyi ideig tart a képverziók replikálása a célcsoportok között?

A lemezkép verziójának replikálási ideje teljes mértékben a rendszerkép méretétől és a replikált régiók számától függ. Javasoljuk azonban, hogy a képet kis méretűre kell állítani, a forrás-és a célcsoportok pedig a legjobb eredmények elérése érdekében. A replikáció állapotát a-ReplicationStatus jelzővel is megtekintheti.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Mi a különbség a forrásoldali régió és a célcsoport között?

A forrástartomány az a régió, amelyben a rendszer létrehozza a rendszerkép verzióját, és a célcsoportok azok a régiók, amelyekben a rendszerkép verziójának másolatát tárolja a rendszer. Minden egyes rendszerkép-verzióhoz csak egy forrásoldali régió tartozhat. Továbbá győződjön meg arról, hogy a forrás régió helyét az egyik célként megadott régióként adja meg, amikor létrehozza a rendszerkép verzióját.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Hogyan megadnia a forrás régiót a rendszerkép verziójának létrehozásakor?

A rendszerkép verziójának létrehozásakor a PowerShellben a **--Location** címkét használhatja a parancssori felületen, és megadhatja **a forrás** régiót. Győződjön meg arról, hogy az alaprendszerképként használt felügyelt rendszerkép ugyanazon a helyen található, mint a rendszerkép verziójának létrehozásához használni kívánt hely. Továbbá győződjön meg arról, hogy a forrás régió helyét az egyik célként megadott régióként adja meg, amikor létrehozza a rendszerkép verzióját.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Hogyan az egyes régiókban létrehozandó rendszerkép-verziók replikáinak számát?

Kétféle módon adhatja meg az egyes régiókban létrehozandó rendszerkép-verziók replikáinak számát:
 
1. A regionális replikák száma, amely meghatározza, hogy régiónként hány replikát kíván létrehozni. 
2. A közös replikák száma, amely az alapértelmezett régiónként szám, ha nincs megadva a területi replika száma. 

A területi replika számának megadásához adja meg a helyet az adott régióban létrehozni kívánt replikák számával együtt: "dél-közép-Egyesült Államok = 2". 

Ha a területi replikák száma nincs megadva az egyes helyekhez, akkor a replikák alapértelmezett száma lesz a megadott közös replika. 

A közös replikák számának a CLI-ben való megadásához használja a **--replika-Count** argumentumot a `az sig image-version create` parancsban.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Létrehozhatom a megosztott képtárat egy másik helyen, mint a rendszerkép-definíció és a rendszerkép verziója?

Igen, ez lehetséges. Javasoljuk azonban, hogy az erőforráscsoportot, a közös képtárat, a képdefiníciót és a rendszerkép verziószámát ugyanazon a helyen tartsa.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Milyen díjak vonatkoznak a megosztott képtárat használó szolgáltatásokra?

A megosztott képkatalógus szolgáltatás használatához nem számítunk fel díjat, kivéve a rendszerkép-verziók és a hálózati kimenő forgalom tárolási díját a forrás régióról a célként megadott régiókba történő replikáláshoz.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Milyen API-verziót érdemes használni a megosztott rendszerkép-katalógus és a rendszerkép-definíció és a rendszerkép-verzió létrehozásához?

A megosztott képtárakkal, képdefiníciókkal és képverziókkal való együttműködéshez javasoljuk, hogy használja a 2018-06-01-es API-verziót. A zóna redundáns tárterületének (ZRS) 2019-03-01-es vagy újabb verziójúnak kell lennie.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Milyen API-verziót használhatok a rendszerkép verziójának használatával létrehozott megosztott virtuális gépek vagy virtuálisgép-méretezések létrehozásához?

A virtuális gépek és a virtuálisgép-méretezési csoport rendszerképeket használó üzembe helyezése esetén javasoljuk, hogy a 2018-04-01-es vagy újabb API-verziót használja.
