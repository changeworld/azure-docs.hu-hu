---
title: A Storage-fiók replikálási módjának módosítása
titleSuffix: Azure Storage
description: Megtudhatja, hogyan replikálhatja a meglévő Storage-fiókokban lévő információkat.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 14ad6dbf139b34f501e0b0ea8c16d8570b2ace5b
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212569"
---
# <a name="change-how-a-storage-account-is-replicated"></a>A Storage-fiók replikálási módjának módosítása

Az Azure Storage mindig több példányban tárolja az adatait, így védve van a tervezett és nem tervezett eseményektől, beleértve az átmeneti hardver meghibásodását, a hálózati vagy áramkimaradást, valamint a súlyos természeti katasztrófákat. A redundancia biztosítja, hogy a Storage-fiókja a meghibásodások előtt is teljesítse az [Azure Storage szolgáltatói szerződését (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) .

Az Azure Storage a következő típusú replikációkat kínálja:

- Helyileg redundáns tárolás (LRS)
- Zónaredundáns tárolás (ZRS)
- Geo-redundáns tárolás (GRS) vagy olvasási hozzáférés geo-redundáns tárolás (RA-GRS)
- Geo-Zone-redundáns tárolás (GZRS) vagy olvasási hozzáférés geo-Zone-redundáns tárolás (RA-GZRS) (előzetes verzió)

Az egyes lehetőségek áttekintését az [Azure Storage redundancia](storage-redundancy.md)című témakörben tekintheti meg.

## <a name="switch-between-types-of-replication"></a>Váltás a replikálási típusok között

A Storage-fiókokat az egyik típusú replikációból bármilyen más típusra válthat, de egyes forgatókönyvek egyszerűbbek, mint mások. Ha a másodlagos régióhoz földrajzi replikálást vagy olvasási hozzáférést szeretne hozzáadni vagy eltávolítani, akkor a Azure Portal, a PowerShell vagy az Azure CLI használatával frissítheti a replikálási beállítást. Ha azonban módosítani szeretné az adatok replikálásának módját az elsődleges régióban, a LRS-ról ZRS-re való áthelyezéssel vagy fordítva, akkor manuális áttelepítést kell végeznie.

Az alábbi táblázat áttekintést nyújt az egyes replikálási típusokról a másikra való váltásról:

| Váltás | ... LRS | ... GRS/RA-GRS | ... ZRS | ... GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... LRS</b> | N/A | Módosítsa az<sup>1</sup> . replikációs beállítást a Azure Portal, a PowerShell vagy a CLI használatával | Manuális áttelepítés végrehajtása <br /><br />Élő áttelepítés kérése | Manuális áttelepítés végrehajtása <br /><br /> VAGY <br /><br /> Először váltson a GRS/RA-GRS, majd igényeljen élő áttelepítést<sup>1</sup> |
| <b>... GRS/RA-GRS</b> | A replikálási beállítás módosításához használja a Azure Portal, a PowerShell vagy a CLI-t. | N/A | Manuális áttelepítés végrehajtása <br /><br /> VAGY <br /><br /> Először váltson a LRS, majd kérjen élő áttelepítést | Manuális áttelepítés végrehajtása <br /><br /> Élő áttelepítés kérése |
| <b>... ZRS</b> | Manuális áttelepítés végrehajtása | Manuális áttelepítés végrehajtása | N/A | Módosítsa az<sup>1</sup> . replikációs beállítást a Azure Portal, a PowerShell vagy a CLI használatával |
| <b>... GZRS/RA-GZRS</b> | Manuális áttelepítés végrehajtása | Manuális áttelepítés végrehajtása | A replikálási beállítás módosításához használja a Azure Portal, a PowerShell vagy a CLI-t. | N/A |

<sup>1</sup> egyszeri kimenő díj.

## <a name="change-the-replication-setting"></a>Replikációs beállítás módosítása

A Azure Portal, a PowerShell vagy az Azure CLI használatával módosíthatja a Storage-fiókok replikálási beállításait, ha nem módosítja az elsődleges régióban lévő adatreplikálás módját. Ha az elsődleges régióban lévő LRS-ről kíván áttelepítést végezni az elsődleges régióban lévő ZRS, vagy fordítva, akkor [manuális áttelepítést](#perform-a-manual-migration-to-zrs) vagy [élő áttelepítést](#request-a-live-migration-to-zrs)kell végrehajtania.

A Storage-fiók replikálásának módosítása nem eredményezi az alkalmazásokhoz való időkorlátot.

# <a name="portaltabportal"></a>[Portal](#tab/portal)

Ha módosítani szeretné a Storage-fiókjához tartozó redundancia beállítást a Azure Portalban, kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a tárfiókot.
1. Válassza ki a **konfigurációs** beállítást.
1. Frissítse a **replikálási** beállítást.

![A portál replikációs lehetőségének módosítását bemutató képernyőkép](media/redundancy-migration/change-replication-option.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Ha módosítani szeretné a Storage-fiók redundancia beállítását a PowerShell-lel, hívja meg a [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) parancsot, és adja meg a `-SkuName` paramétert:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha módosítani szeretné a Storage-fiókjához tartozó redundancia beállítást az Azure CLI-vel, hívja meg az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update) parancsot, és adja meg a `--sku` paramétert:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs"></a>Manuális áttelepítés ZRS

Ha módosítani szeretné a Storage-fiókban lévő adatok replikálásának módját az elsődleges régióban, akkor a LRS-ről ZRS-re való áttéréssel vagy fordítva, akkor dönthet úgy, hogy manuális áttelepítést hajt végre. A manuális migrálás az élő migrálásnál nagyobb rugalmasságot biztosít. A manuális áttelepítés időzítését szabályozhatja, ezért akkor használja ezt a beállítást, ha az áttelepítésnek egy adott dátummal kell végrehajtania.

Ha manuális áttelepítést végez a LRS-ből az elsődleges régióban lévő ZRS, vagy fordítva, a célként megadott Storage-fiók földrajzilag redundáns lehet, és a másodlagos régió olvasási hozzáférésére is konfigurálható. Egy lépésben például áttelepítheti egy LRS-fiókot egy GZRS vagy RA-GZRS-fiókba.

A manuális áttelepítés az alkalmazás leállását eredményezheti. Ha az alkalmazás magas rendelkezésre állást igényel, a Microsoft élő migrálási lehetőséget is biztosít. Az élő migrálás egy állásidő nélküli, helyszíni migrálás.

Manuális áttelepítés esetén a meglévő Storage-fiókból származó adatok másolása egy új, az elsődleges régióban ZRS használó Storage-fiókba. Manuális áttelepítés végrehajtásához a következő lehetőségek közül választhat:

- Az Adatmásolás egy meglévő eszközzel, például a AzCopy, az egyik Azure Storage ügyféloldali kódtár vagy egy megbízható, harmadik féltől származó eszköz használatával.
- Ha már ismeri a Hadoop-t vagy a HDInsight-t, csatolhatja a forrás-és a cél Storage-fiók fiókját is a fürthöz. Ezután integrálással az adatmásolási folyamatot egy olyan eszközzel, mint a DistCp.

## <a name="request-a-live-migration-to-zrs"></a>Élő áttelepítés kérése a ZRS-be

Ha a Storage-fiókot a LRS vagy a GRS-ből kell áttelepítenie az elsődleges régióban az alkalmazás leállása nélkül, akkor a Microsofttól élő áttelepítést igényelhet. Az élő áttelepítés során hozzáférhet a Storage-fiókban tárolt adataihoz, és a tartósság és a rendelkezésre állás elvesztése nélkül is elérhető. Az Azure Storage SLA az áttelepítési folyamat során megmarad. Az élő áttelepítéshez nem tartozik adatvesztés. A szolgáltatás-végpontok, a hozzáférési kulcsok, a közös hozzáférésű aláírások és az egyéb Fiókbeállítások az áttelepítés után változatlanok maradnak.

A ZRS csak az általános célú v2-fiókokat támogatja, ezért mindenképpen frissítse a Storage-fiókját, mielőtt beküld egy élő áttelepítésre vonatkozó kérést a ZRS-be. További információ: [frissítés általános célú v2 Storage-fiókra](storage-account-upgrade.md). A Storage-fióknak tartalmaznia kell az áttelepíteni kívánt, élő áttelepítéssel kapcsolatos adatátvitelt.

Az élő áttelepítés csak LRS vagy GRS replikálást használó Storage-fiókok esetén támogatott. Ha a fiókja RA-GRS-t használ, előbb módosítania kell a fiók replikálási típusát a LRS vagy a GRS értékre a továbblépés előtt. Ez a közbenső lépés eltávolítja az RA-GRS által az áttelepítés előtt megadott másodlagos írásvédett végpontot.

Bár a Microsoft azonnal kezeli az élő migrálási kérelmet, a migrálás befejezésének időpontjára nem vállal garanciát. Ha egy adott időpontig szükséges az adatok ZRS-re való migrálása, a Microsoft a manuális migrálás végrehajtását javasolja. Minél több adat található a fiókban, általában annál tovább tart az adatok migrálása.

Manuális áttelepítést kell végrehajtania, ha:

- Az adatait olyan ZRS szeretné áttelepíteni, amely a forrásoldali fióktól eltérő régióban található.
- A Storage-fiók egy prémium szintű oldal blobja vagy egy blob-fiók blokkolása.
- Az adatok áttelepíthetők a ZRS-ből LRS, GRS vagy RA-GRS.
- A Storage-fiók az archiválási szinten található adatokkal is rendelkezik.

Az [Azure-támogatási portálon](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)keresztül is kérhet élő áttelepítést. A portálon válassza ki azt a Storage-fiókot, amelyet ZRS szeretne alakítani.

1. **Új támogatási kérelem** kiválasztása
2. A fiókadatok alapján végezze el az **alapvető** tudnivalókat. A **szolgáltatás** szakaszban válassza a **Storage-fiókok kezelése** elemet, majd a ZRS konvertálni kívánt erőforrást.
3. Kattintson a **Tovább** gombra.
4. A **probléma** szakasz a következő értékeket határozza meg:
    - **Súlyosság**: hagyja meg az alapértelmezett értéket.
    - **Probléma típusa**: válassza **Az adatáttelepítés**lehetőséget.
    - **Kategória**: válassza **az áttelepítés a ZRS**lehetőséget.
    - **Title**: adjon meg egy leíró címet, például **ZRS fiók áttelepítése**.
    - **Részletek**: írja be a **részletek mezőbe a** további részleteket, például a (z) \_\_ régióban lévő [LRS, GRS] webhelyről szeretné áttelepíteni a ZRS.
5. Kattintson a **Tovább** gombra.
6. A kapcsolattartási **adatok** panelen ellenőrizze, hogy helyesek-e a kapcsolattartási adatok.
7. Kattintson a **Létrehozás** gombra.

A támogatási személy felveszi Önnel a kapcsolatot, és segítséget nyújt a szükséges segítségért.

> [!NOTE]
> A prémium szintű fájlmegosztás jelenleg nem támogatja az élő áttelepítést. Az adatok manuális másolása és áthelyezése jelenleg nem támogatott.
>
> A GZRS-tárolási fiókok jelenleg nem támogatják az archiválási szintet. További részletekért tekintse meg az [Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési szintet](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) .
>
> A felügyelt lemezek csak a LRS számára érhetők el, és nem telepíthetők át a ZRS. A standard szintű SSD-meghajtókon pillanatképeket és lemezképeket tárolhat szabványos HDD-tárolón, és [választhat a LRS és a ZRS lehetőségek közül](https://azure.microsoft.com/pricing/details/managed-disks/). További információ a rendelkezésre állási csoportokkal való integrációról: [Bevezetés az Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)szolgáltatásba.

## <a name="switch-from-zrs-classic"></a>Váltás a klasszikus ZRS

> [!IMPORTANT]
> A Microsoft a ZRS klasszikus fiókjait a 2021. március 31-én fogja érvényteleníteni és áttelepíteni. További részletek a klasszikus ügyfelek ZRS az elavultság előtt.
>
> Miután az ZRS általánosan elérhetővé válik egy adott régióban, az ügyfelek többé nem tudnak ZRS klasszikus fiókokat létrehozni az adott régióban lévő Azure Portal. Ha a klasszikus ZRS elavult, a Microsoft PowerShell és az Azure CLI használatával hozhat létre klasszikus ZRS-fiókokat. További információ a ZRS rendelkezésre állásáról: [Azure Storage redundancia](storage-redundancy.md).

A klasszikus ZRS aszinkron módon replikálja az adatközpontokat egy-két régión belül. Előfordulhat, hogy a replikált adatértékek nem állnak rendelkezésre, kivéve, ha a Microsoft feladatátvételt kezdeményez a másodlagosnak. A klasszikus ZRS-fiók nem alakítható át LRS, GRS vagy RA-GRS típusúra vagy onnan. A klasszikus ZRS-fiókok nem támogatják a metrikákat és a naplózást is.

A klasszikus ZRS csak az általános célú v1-(GPv1-) Storage-fiókokban lévő **blokkos Blobok** esetén érhető el. További információ a tárfiókokról: [Az Azure-tárfiókok áttekintése](storage-account-overview.md).

Ha manuálisan szeretné áttelepíteni a ZRS-fiók adatait egy LRS, GRS, RA-GRS vagy klasszikus ZRS-fiókból, használja a következő eszközök egyikét: AzCopy, Azure Storage Explorer, PowerShell vagy Azure CLI. Saját áttelepítési megoldást is létrehozhat az Azure Storage ügyféloldali kódtárainak egyikével.

A ZRS klasszikus Storage-fiókját a Azure Portal, a PowerShell vagy az Azure CLI használatával is frissítheti a ZRS elérhető régiókban.

# <a name="portaltabportal"></a>[Portal](#tab/portal)

A Azure Portal ZRS való frissítéséhez navigáljon a fiók **konfigurációs** beállításaihoz, és válassza a **frissítés**lehetőséget:

![A ZRS klasszikus frissítése ZRS a portálon](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Ha a PowerShell használatával szeretne frissíteni a ZRS, hívja meg a következő parancsot:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

A ZRS az Azure CLI használatával történő frissítéséhez hívja meg a következő parancsot:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Az adatreplikálás módjának megváltoztatásához kapcsolódó költségek

Az adatok replikálásának megváltoztatásához kapcsolódó költségek a konverziós útvonaltól függenek. A legdrágább, Azure Storage-beli redundancia-ajánlatok megrendelése az LRS, a ZRS, a GRS, az RA-GRS, a GZRS és az RA-GZRS.

Ha például *a LRS-ből bármilyen* más típusú replikációra kerül, további díjakat számítunk fel, mert összetettebb redundancia-szintre vált. A GRS-RA vagy RA-GRS-re *való* Migrálás esetén a kimenő sávszélességért fizetendő díj várható, mert az elsődleges régiójában lévő adatai replikálódnak a távoli másodlagos régióba. Ez a díj egy egyszeri költség a kezdeti beállításnál. Az adatok másolása után nincsenek további áttelepítési díjak. A sávszélességgel kapcsolatos további információkért lásd az [Azure Storage díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/storage/blobs/).

Ha a Storage-fiókot a GRS-ből a LRS-be telepíti át, akkor nem jár további díjszabással, de a replikált adatok törlődnek a másodlagos helyről.

> [!IMPORTANT]
> Ha a Storage-fiókját az RA-GRS-ről a GRS vagy a LRS-re telepíti át, akkor ez a fiók az RA-GRS-ként lesz kiszámlázva az RA-tól számított további 30 nap után.

## <a name="see-also"></a>Lásd még

- [Azure Storage-redundancia](storage-redundancy.md)
- [A Storage-fiók utolsó szinkronizálási ideje tulajdonságának megtekintése](last-sync-time-get.md)
- [A magasan elérhető alkalmazások tervezése olvasási hozzáférésű geo-redundáns tárolással](storage-designing-ha-apps-with-ragrs.md)
