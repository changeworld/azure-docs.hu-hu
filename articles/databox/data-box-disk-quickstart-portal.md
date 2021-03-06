---
title: Rövid útmutató – Microsoft Azure Data Box Disk | Microsoft Docs
description: A rövid útmutató segítségével gyorsan üzembe helyezheti az Azure Data Box Disket az Azure Portalon.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: fcc7c6ff74e17db2066d97597849c985f5a961e9
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514068"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Gyorsútmutató: Az Azure Data Box Disk üzembe helyezése az Azure Portal használatával

A rövid útmutató az Azure Data Box Disk az Azure Portal használatával való üzembe helyezését írja le. A lépések bemutatják, hogyan hozhat gyorsan létre rendeléseket, hogyan kaphatja kézhez, csomagolhatja ki és csatlakoztathatja a meghajtókat, majd másolhatja rájuk az adatokat azok az Azure-ba való feltöltéséhez.

Részletes üzembehelyezési és nyomkövetési utasítások: [Oktatóanyag: Az Azure Data Box Disk megrendelése](data-box-disk-deploy-ordered.md) című rész lépéseit. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

::: zone-end

::: zone target="chromeless"

Ez az útmutató az Azure Data Box Disk Azure-beli használatának lépéseit mutatja be. Ez az útmutató segít megválaszolni az alábbi kérdéseket.

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:

- Ellenőrizze, hogy az előfizetésen engedélyezve van-e az Azure Data Box szolgáltatás. A szolgáltatás az előfizetésen való engedélyezéséhez [regisztráljon a szolgáltatásra](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs) webhelyen.

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **Előfeltételek áttekintése**: Ellenőrizze a lemezek és a kábelek számát, az operációs rendszert és az egyéb szoftvereket.
> - **Csatlakoztatás és zárolás feloldása**: Csatlakoztassa az eszközt, és az adatok másolásához oldja fel a lemez zárolását.
> - **Adatmásolás a lemezre és ellenőrzés** Másolja az adatokat a lemezen található, előre létrehozott mappákba.
> - **Lemezek visszaküldése**: Küldje vissza a lemezeket az Azure-adatközpontba, ahol feltöltik az adatokat a tárfiókjába.
> - **Az Azure-beli adatok ellenőrzése**: Az adatok forrás-adatkiszolgálóról történő törlése előtt ellenőrizze, hogy fel lettek-e töltve a tárfiókba.

::: zone-end


::: zone target="docs"

## <a name="order"></a>Rendelés

Ez a lépés nagyjából 5 percet vesz igénybe.

1. Hozzon létre egy új Azure Data Box-erőforrást az Azure Portalon. 
2. Válasszon ki egy előfizetést a szolgáltatáshoz, és az átvitel típusánál válassza az **Importálás** lehetőséget. Adja meg a **Forrásország** mezőben azt a helyet, ahol az adatok jelenleg találhatók, az **Azure-beli célrégió** mezőben pedig az adatátvitel célját.
3. Válassza a **Data Box Disk** lehetőséget. A megoldás maximális kapacitása 35 TB, nagyobb mennyiségű adat esetén több meghajtórendelést is létrehozhat.  
4. Adja meg a rendelés részleteit és a szállítási adatokat. Ha a szolgáltatás elérhető az Ön régiójában, adja meg az értesítési e-mail-címeket, tekintse át az összefoglalót, és hozza létre a rendelést.

A rendelés létrehozását követően megtörténik a meghajtók szállításra való előkészítése.

## <a name="unpack"></a>Kicsomagolás

Ez a lépés nagyjából 5 percet vesz igénybe.

A Data Box Disk-meghajtót egy UPS Express dobozban küldjük el Önnek. Nyissa ki a dobozt, és ellenőrizze, hogy tartalmazza-e a következőket:

- 1–5 buborékfóliába csomagolt USB-meghajtó.
- Meghajtónként egy csatlakozókábel.
- Fuvarlevélcímke a csomag visszaküldéséhez.

## <a name="connect-and-unlock"></a>Csatlakoztatás és a zárolás feloldása

Ez a lépés nagyjából 5 percet vesz igénybe.

1. A csomagban foglalt kábellel csatlakoztassa a meghajtót a Windows vagy Linux egy támogatott verzióját futtató számítógéphez. A támogatott operációsrendszer-verziókkal kapcsolatos további információkért lásd [az Azure Data Box Disk rendszerkövetelményeit](data-box-disk-system-requirements.md). 
2. A meghajtó zárolásának feloldása:

    1. Az Azure Portalon lépjen az **Általános > Eszköz adatai** menüpontra, és kérje le a hozzáférési kulcsot.
    2. Töltse le és csomagolja ki a megfelelő operációs rendszerhez tartozó Data Box Disk zárolását feloldó eszközt az adatok a meghajtókra való másolásához használt számítógépen. 
    3. Futtassa a Data Box Disk lemezzárolás-feloldó eszközt és adja meg a hozzáférési kulcsot. Új lemezek behelyezésekor futtassa újra a zárolást feloldó eszközt, és adja meg a hozzáférési kulcsot. **Ne használja a BitLocker párbeszédpanelt vagy a BitLocker kulcsot a lemez zárolásának feloldására.** További információ a lemezek zárolásának feloldásáról: [Lemez zárolásának feloldása Windows-ügyfélen](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) vagy [Lemez zárolásának feloldása Linux-ügyfélen](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).
    4. A meghajtóhoz rendelt betűjelet az eszköz mutatja. Jegyezze fel az egyes meghajtók betűjelét. Ezt majd a következő lépésekben fogjuk felhasználni.

## <a name="copy-data-and-validate"></a>Adatok másolása és ellenőrzés

A művelet végrehajtásának időtartama az adatok mennyiségétől függ.

1. A meghajtó a *PageBlob*, a *BlockBlob*, az *AzureFile*, a *ManagedDisk* és a *DataBoxDiskImport* mappát tartalmazza. A blokkblobokként importálandó adatokat húzással másolja a *BlockBlob* mappába. Hasonlóképpen húzza a VHD/VHDX és hasonló típusú adatokat a *PageBlob* mappába, valamint a megfelelő adatokat az *AzureFile* mappába. Másolja a felügyelt lemezként feltölteni kívánt VHD-kat egy, a *ManagedDisk* mappában található almappába.

    A rendszer a *BlockBlob* és a *PageBlob* mappa alatt található minden almappához létrehoz egy tárolót az Azure-tárfiókban. Létrejön egy fájlmegosztás egy, az *AzureFile* mappában található almappában.

    A *BlockBlob* és a *PageBlob* mappa alatt található összes fájl az Azure Storage-fiók alatti alapértelmezett `$root` tárolóba lesz átmásolva. Másolja a fájlokat egy, az *AzureFile* mappában található almappába. A közvetlenül az *AzureFile* mappába másolt fájlok másolása meghiúsul, és a fájlok blokkblobként lesznek feltöltve.

    > [!NOTE]
    > - Minden tároló, blob és fájl nevének követnie kell az [Azure elnevezési konvencióit](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Ha a szabályok nem teljesülnek, az adatok az Azure-ba való feltöltése meghiúsul.
    > - Győződjön meg arról, hogy a fájlok mérete nem haladja meg blokkblobok esetén a ~4,75 TiB-ot, lapblobok esetén a ~8 TiB-ot, az Azure Files esetén pedig az ~1 TiB-ot.

2. **(Nem kötelező)** Határozottan javasoljuk, hogy a másolás után legalább a *DataBoxDiskImport* mappában elérhető `DataBoxDiskValidation.cmd` parancsot futtassa, és válassza ki az 1. lehetőséget a fájlok ellenőrzéséhez. Emellett azt is javasoljuk, hogy ha az ideje megengedi, a 2. lehetőség használatával hozzon létre ellenőrzőösszegeket az ellenőrzéshez (az adatok mennyiségétől függően ez némi időt vehet igénybe). Ezekkel a lépésekkel minimálisra csökkenthető az Azure-ba történő adatfeltöltés során fellépő hibák előfordulásának lehetősége.
3. Biztonságosan távolítsa el a meghajtót.

## <a name="ship-to-azure"></a>Elküldés az Azure-nak

Ez a lépés kb. 5–7 percet vesz igénybe.

1. Helyezze vissza az összes meghajtót együtt az eredeti csomagba. Használja a csomagban lévő fuvarlevélcímkét. Ha a címke sérült vagy elveszett, töltse le a portálról. Lépjen az **Áttekintés** oldalra, és kattintson a **Levélcímke letöltése** parancsra a parancssávon.
2. Adja fel a lezárt csomagot a futárszolgálatnál.  

A Data Box Disk szolgáltatás egy e-mail-értesítést küld, és frissíti a rendelés állapotát az Azure Portalon.

## <a name="verify-your-data"></a>Az adatok ellenőrzése

A művelet végrehajtásának időtartama az adatok mennyiségétől függ.

1. A Data Box-meghajtók az Azure-adatközpont hálózatához való csatlakoztatásakor az adatok az Azure-ba való feltöltése automatikusan megkezdődik.
2. Az Azure Data Box szolgáltatás az Azure Portalon értesíti, ha az adatok másolása befejeződött.
    
    1. Ellenőrizze a hibákat a hibanaplókban, és tegye meg a szükséges intézkedéseket.
    2. Ellenőrizze, hogy az adatok jelen vannak-e a tárfiók(ok)ban, mielőtt törölné azokat a forrásról.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a lépés 2–3 percet vehet igénybe.

Az erőforrások eltávolításához visszavonhatja a Data Box-rendelést, és törölheti azt.

- A Data Box-rendelést a feldolgozása előtt bármikor visszavonhatja az Azure Portalon. A rendelést a teljesítése után már nem lehet visszavonni. A rendelés halad a maga útján, amíg el nem éri a teljesített állapotot.

    A rendelés visszavonásához lépjen az **Áttekintés** oldalra, és kattintson a **Megszakítás** parancsra a parancssávon.  

- A rendelés akkor törölhető, ha a **Teljesítve** vagy a **Megszakítva** állapot jelenik meg az Azure Portalon.

    A rendelés törléséhez lépjen az **Áttekintés** oldalra, és kattintson a **Törlés** parancsra a parancssávon.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy Azure Data Box Disk-meghajtót helyezett üzembe az adatok az Azure-ba való importálásához. Folytassa a következő cikkel, ha többet szeretne megtudni az Azure Data Box Disk kezeléséről:

> [!div class="nextstepaction"]
> [A Data Box Disk az Azure Portal használatával történő kezelése](data-box-portal-ui-admin.md)

::: zone-end
