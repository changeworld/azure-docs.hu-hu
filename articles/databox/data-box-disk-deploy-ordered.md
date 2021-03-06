---
title: Oktatóanyag a Azure Data Box Disk rendeléséhez | Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan regisztrálhat az Azure Data Box Disk szolgáltatásra, és hogyan rendelheti meg azt az adatok Azure-ba történő importálásához.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: c1626542aba71f971ad96fe9f187ce9f1c1a99f3
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012941"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>Oktatóanyag: Azure Data Box Disk megrendelése

Az Azure Data Box Disk egy hibrid felhőalapú megoldás, amellyel gyorsan, könnyen és megbízhatóan importálhat helyszíni adatokat az Azure-ba. Ön letölti az adatokat a Microsoft által SSD-lemezekre, majd visszaküldi azokat a Microsoftnak. Az adatok ezt követően fel lesznek töltve az Azure-ba.

Ez az oktatóanyag leírja, hogyan rendelheti meg az Azure Data Box Disk szolgáltatást. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Data Box Disk megrendelése
> * A rendelés nyomon követése
> * A rendelés lemondása

## <a name="prerequisites"></a>Előfeltételek

A központi telepítése előtt végezze el a következő konfigurálási előfeltételeket a Data Box szolgáltatáshoz és Data Box Diskhoz.

### <a name="for-service"></a>A szolgáltatás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:
- Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
- Győződjön meg róla, hogy a Data Box szolgáltatáshoz használt előfizetés a következő típusok valamelyikébe tartozik:
    - Microsoft nagyvállalati szerződés (EA). További információk az [EA-előfizetésekről](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Felhőszolgáltató (CSP). További információk az [Azure CSP programjáról](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
- A Data Box-rendelések létrehozásához tulajdonosi vagy közreműködői jogosultsággal kell rendelkeznie az előfizetésen.

### <a name="for-device"></a>Az eszköz esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:
- Van olyan ügyfélszámítógép, amelyről másolatot készíthet az adatokról. Az ügyfélszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-disk-system-requirements.md#supported-operating-systems-for-clients) kell futnia;
    - Windows-ügyfél esetén további [szükséges szoftvereknek](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) telepítve kell lennie.  

## <a name="order-data-box-disk"></a>A Data Box Disk megrendelése

Jelentkezzen be ide:

- A Azure Portal ezen az URL- https://portal.azure.com címen: rendelés Data Box Disk.
- Vagy a Azure Government portál ezen az URL-címen https://portal.azure.us:. További részletekért lépjen a [kapcsolódás Azure Government a portál használatával](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

Data Box Disk megrendeléséhez hajtsa végre az alábbi lépéseket.

1. Az Azure Portal bal felső sarkában kattintson a **+ Erőforrás létrehozása** elemre, és keresse meg az *Azure Data Box* lehetőséget. Kattintson az **Azure Data Box** lehetőségre.
    
   ![Keresse meg az Azure Data Box lehetőséget 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Kattintson a **Create** (Létrehozás) gombra.

3. Ellenőrizze, hogy a Data Box szolgáltatás elérhető-e a régióban. Adja meg, vagy jelölje ki a következő információkat, majd kattintson az **Alkalmaz** elemre.

    ![Data Box Disk lehetőség kiválasztása](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Beállítás|Value|
    |---|---|
    |Subscription|Válassza ki azt az előfizetést, amely számára engedélyezve van a Data Box szolgáltatás.<br> Az előfizetés az Ön számlázási fiókjához lesz társítva. |
    |Átvitel típusa| Importálás az Azure-ba|
    |Forrásország | Válassza ki azt az országot/régiót, ahol az adatai jelenleg találhatók.|
    |Azure-beli célrégió|Válassza ki azt az Azure-régiót, ahova át szeretné vinni az adatokat.|

  
5.  Kattintson a **Data Box Disk** elemre. A megoldás maximális kapacitása megrendelésenként (5 lemez) 35 TB. Nagyobb adatmennyiséghez több megrendelést is létrehozhat.

     ![Data Box Disk lehetőség kiválasztása](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  A **Megrendelés** területen adja meg a **Megrendelés részleteit**. Adja meg vagy válassza ki a következő információkat.

    |Beállítás|Érték|
    |---|---|
    |Name (Név)|Adjon meg egy rövid nevet a megrendelés nyomon követéséhez.<br> A névnek 3-24 karakter hosszúságúnak kell lennie, és csak betűket, számokat és kötőjelet tartalmazhat. <br> A névnek betűvel vagy számmal kell kezdődnie és végződnie. |
    |Resource group| Használjon egy már létezőt, vagy hozzon létre újat. <br> Az erőforráscsoport az együtt kezelhető vagy üzembe helyezhető erőforrások logikai tárolója. |
    |Azure-beli célrégió| Válasszon ki egy régiót a tárfiókhoz.<br> Jelenleg csak az USA régióiban, Nyugat-és Észak-Európában, Kanadában és Ausztráliában működő tárfiókok támogatottak. |
    |Adatok becsült mérete (TB)| Adjon meg egy becsült mennyiséget TB-ban. <br>Az adatmennyiség alapján a Microsoft kiküldi Önnek a megfelelő számú 8 TB-os SSD-t (7 TB felhasználható kapacitás). <br>5 lemez maximális felhasználható kapacitása 35 TB. |
    |Lemezjelszó| Ha bejelölte az **Egyéni kulcs használata az Azure által létrehozott hozzáférési kulcs helyett** lehetőséget, adja meg a lemez hozzáférési kulcsát. <br> Adjon meg egy 12 – 32 karakterből álló alfanumerikus kulcsot, amely legalább egy numerikus és egy speciális karakterből áll. Az engedélyezett speciális karakterek a következők: `@?_+`. <br> Úgy is dönthet, hogy kihagyja ezt a lehetőséget, és az Azure által létrehozott hozzáférési kulcsot használja a lemezek feloldásához.|
    |Tárolási cél     | Válasszon a Storage-fiók vagy a felügyelt lemezek közül, vagy mindkettőt. <br> A megadott Azure-régió alapján válassza ki a Storage-fiókot egy meglévő Storage-fiók szűrt listájából. Data Box Disk csak 1 Storage-fiókkal lehet összekapcsolni. <br> Létrehozhat egy új **általános célú v1**-, **általános célú v2-** vagy **blob Storage-fiókot**is. <br>A virtuális hálózatokkal rendelkező Storage-fiókok támogatottak. Annak engedélyezéséhez, hogy Data Box szolgáltatás biztonságos Storage-fiókokkal működjön, engedélyezze a megbízható szolgáltatásokat a Storage-fiók hálózati tűzfalának beállításai között. További információ: [Azure Data Box hozzáadása megbízható szolgáltatásként](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).|

    Ha a Storage-fiókot tároló célhelyként használja, a következő képernyőkép jelenik meg:

    ![A Storage-fiók Data Box Disk sorrendje](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Ha Data Box Disk használatával felügyelt lemezeket hoz létre a helyszíni virtuális merevlemezekről, a következő információkat is meg kell adnia:

    |Beállítás  |Value  |
    |---------|---------|
    |Resource group     | Hozzon létre egy új erőforráscsoportot, ha felügyelt lemezeket kíván létrehozni a helyszíni virtuális merevlemezekről. Csak akkor használjon meglévő erőforráscsoportot, ha Data Box szolgáltatás által Data Box Disk megrendeléshez lett létrehozva. <br> Csak egy erőforráscsoport támogatott.|

    ![Felügyelt lemez Data Box Disk sorrendje](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    A felügyelt lemezekhez megadott Storage-fiók átmeneti tárolási fiókként van használatban. A Data Box szolgáltatás feltölti a virtuális merevlemezeket az átmeneti tárolási fiókba, majd átalakítja azokat a felügyelt lemezekre, és áthelyezi azokat az erőforráscsoportokba. További információ: az [adatok feltöltésének ellenőrzése az Azure](data-box-disk-deploy-upload-verify.md#verify-data-upload-to-azure)-ban.

13. Kattintson a **Tovább** gombra.

    ![A megrendelés részletei](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. A **Szállítási cím** lapon adja meg az utónevét és a vezetéknevét, a cége nevét és postai címét, valamint egy érvényes telefonszámot. Kattintson a **Cím ellenőrzése** elemre. A rendszer ellenőrzi, hogy a szolgáltatás elérhető-e a megadott szállítási címen. Ha a szolgáltatás elérhető a megadott szállítási címen, értesítést fog kapni erről. 

    ![Szállítási cím megadása](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. Az **Értesítési adatok** területen adja meg az e-mail-címeket. A szolgáltatás e-mail-értesítést küld a megrendelés állapotának minden változásáról a megadott e-mail-címekre. 

    Javasoljuk a csoportos e-mail-cím használatát, hogy az értesítéseket a rendszergazda távollétében is kézhez kaphassa.

16. Tekintse át a megrendeléshez kapcsolódó **Összefoglalót** a kapcsolattartási adatokról, az értesítésekről és az adatvédelmi feltételekről. Jelölje ki az adatvédelmi feltételek elfogadásához tartozó jelölőnégyzetet.

17. Kattintson a **Megrendelés** elemre. A megrendelés létrehozása néhány percet vesz igénybe.

 
## <a name="track-the-order"></a>A megrendelés nyomon követése

Miután elküldte a megrendelését, annak állapotát az Azure Portalon követheti nyomon. Keresse meg a megrendelést, és nyissa meg az **Áttekintés** területet az állapot megtekintéséhez. A portál a **Megrendelve** állapotot mutatja.

![Data Box Disk állapot megrendelve](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Ha a lemezek nem érhetők el, értesítést kap. Ha a lemezek elérhetők, a Microsoft azonosítja a lemezeket a szállításhoz, és előkészíti a csomagot. A lemezek előkészítése a következőket foglalja magába:

- A lemezek titkosítása a AES-128 BitLocker használatával.  
- A lemezek zárolása az illetéktelen hozzáférés megakadályozására.
- A folyamat során létrejön a lemezek zárolását feloldó hozzáférési kulcs.

A lemezek előkészítésének lezárultával a portál a **Feldolgozott** állapotot mutatja.

A Microsoft ezután előkészíti, majd lebonyolítja a szállítást egy regionális szolgáltatón keresztül. A szállítás megkezdésekor Ön megkapja a nyomkövetési azonosító számot. A portál a **Feladva** állapotot mutatja.

## <a name="cancel-the-order"></a>A rendelés lemondása

A rendelés lemondásához az Azure Portalon lépjen az **Áttekintés** területre, és kattintson a **Megszakítás** gombra a parancssávon.

Csak akkor mondhatja le a rendelést, ha a lemezek megrendelése után a szállításra való előkészítés még folyamatban van. Ha a megrendelés feldolgozása már megtörtént, nem mondhatja le a rendelést.

![Rendelés lemondása](media/data-box-disk-deploy-ordered/cancel-order1.png)

A lemondott rendelés törléséhez nyissa meg az **Áttekintés** területet, és kattintson a parancssávon a **Törlés** elemre.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * A Data Box Disk megrendelése
> * A megrendelés nyomon követése
> * A rendelés lemondása

A következő oktatóanyag a Data Box Disk beállítását mutatja be.

> [!div class="nextstepaction"]
> [Az Azure Data Box Disk beállítása](./data-box-disk-deploy-set-up.md)
