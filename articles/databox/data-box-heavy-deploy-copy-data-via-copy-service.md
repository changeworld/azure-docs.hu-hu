---
title: 'Oktatóanyag: az Adatmásolás Azure Data Box Heavy adatmásolási szolgáltatáson keresztül'
description: Ebből az oktatóanyagból megtudhatja, hogyan másolhat Adatmásolást a Azure Data Box Heavy eszközre az adatmásolási szolgáltatás segítségével.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 67547db53d2b9ce05838335ffcb5d789b77ecbbe
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560220"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-heavy-preview"></a>Oktatóanyag: az adatmásolási szolgáltatás használata az Adatmásolás Azure Data Box Heavyba (előzetes verzió)

Ez az oktatóanyag azt ismerteti, hogyan lehet adatot befogadni az adatmásolási szolgáltatás köztes gazdagép nélküli használatával. Az adatmásolási szolgáltatás helyileg fut Azure Data Box Heavyon, csatlakozik a hálózatra csatlakoztatott Storage-eszközhöz (NAS) az SMB-n keresztül, és átmásolja az adatait a Data Box Heavyba.

Az adatmásolási szolgáltatás használata:

- Olyan NAS-környezetekben, ahol előfordulhat, hogy a közbenső gazdagépek nem érhetők el.
- Az adatok betöltéséhez és feltöltéséhez szükséges heteket tartalmazó kisméretű fájlok. Az adatmásolási szolgáltatás jelentősen javítja a kis méretű fájlok betöltését és feltöltési idejét.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adatok másolása a Data Box Heavyre

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Elvégezte az oktatóanyagot: [Azure Data Box Heavy beállítása](data-box-heavy-deploy-set-up.md).
2. Megkapta a Data Box Heavyt, és a portálon a megrendelés **Kézbesítve** állapotú.
3. Rendelkezik a forrás NAS-eszköz hitelesítő adataival, amelyeket az adatmásoláshoz fog csatlakozni.
4. Nagy sebességű hálózathoz csatlakozik. A legnagyobb másolási sebesség érdekében két 40 GbE sebességű kapcsolat (csomópontonként egy) használható egyidejűleg. Ha nem rendelkezik 40 GbE sebességű kapcsolattal, javasoljuk, hogy legalább két 10 GbE sebességű kapcsolattal (csomópontonként eggyel) rendelkezzen. 

## <a name="copy-data-to-data-box-heavy"></a>Adatok másolása a Data Box Heavyre

Miután csatlakozott a NAS-eszközhöz, a következő lépés az adatai másolása. Az adatok másolásának megkezdése előtt tekintse át a következőket:

- Az adatok másolása közben ellenőrizze, hogy az adatok mérete megfelel-e az [Azure Storage-ban és a Data Box Heavy korlátokban](data-box-heavy-limits.md)leírt méretkorlát-korlátoknak.
- Ha az Data Box Heavy által feltöltött adatok párhuzamosan fel vannak töltve más alkalmazások által Data Box Heavyn kívül, a feltöltési feladatok és az adatok sérülése is eredményezhet.
- Ha az adatmásolási szolgáltatás az Adatmásolás során módosul, az adatvesztés vagy az adatsérülés tapasztalható.

Az adatok adatmásolási szolgáltatással történő másolásához létre kell hoznia egy feladatot:

1. A Data Box Heavy eszköz helyi webes FELÜLETén nyissa meg a **kezelés** > az **Adatmásolás**című részt.
2. Az **Adatmásolás** lapon válassza a **Létrehozás**lehetőséget.

    ![Válassza a létrehozás lehetőséget az "Adatmásolás" oldalon](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. A **feladatok és indítás konfigurálása** párbeszédpanelen adja meg a következő mezőket:
    
    |Mező                          |Érték    |
    |-------------------------------|---------|
    |**Feladatok neve**                       |A feladatokhoz 230 karakternél rövidebb egyedi név. Ezek a karakterek nem engedélyezettek a feladattípusban: \<, \>, \|, \?, \*, \\, \:, \/és \\\.         |
    |**Forrás helye**                |Adja meg az adatforrás SMB-elérési útját a következő formátumban: `\\<ServerIPAddress>\<ShareName>` vagy `\\<ServerName>\<ShareName>`.        |
    |**Felhasználónév**                       |A Felhasználónév `\\<DomainName><UserName>` formátumban az adatforrás eléréséhez. Ha egy helyi rendszergazda csatlakozik, akkor explicit biztonsági engedélyekre van szükségük. Kattintson a jobb gombbal a mappára, válassza a **Tulajdonságok** lehetőséget, majd válassza a **Biztonság**elemet. Ehhez hozzá kell adnia a helyi rendszergazdát a **Biztonság** lapon.       |
    |**Jelszó**                       |Az adatforrás eléréséhez használt jelszó.           |
    |**Cél Storage-fiók**    |Válassza ki a cél Storage-fiókot, hogy az adatok a listáról legyenek feltöltve.         |
    |**Cél típusa**       |Válassza ki a cél tárolási típust a listából: **blob letiltása**, **oldal blobja**vagy **Azure Files**.        |
    |**Cél tároló/megosztás**    |Adja meg annak a tárolónak vagy megosztásnak a nevét, amelyhez fel kívánja tölteni az adatait a célhely Storage-fiókjába. A név lehet egy megosztás neve vagy egy tároló neve. Például használhatja a következőket: `myshare` vagy `mycontainer`. A nevet `sharename\directory_name` vagy `containername\virtual_directory_name`formátumban is megadhatja.        |
    |**Fájlokra vonatkozó megfelelő minta másolása**    | A fájlnév-megfeleltetési mintát a következő két módon adhatja meg:<ul><li>**Helyettesítő kifejezések használata:** Helyettesítő karakteres kifejezésekben csak `*` és `?` támogatott. A kifejezés `*.vhd` például a `.vhd` kiterjesztésű összes fájlra illeszkedik. Hasonlóképpen, a `*.dl?` az összes olyan fájlra megfelel, amely vagy a kiterjesztés `.dl`, vagy amely a `.dl`vel kezdődik, például `.dll`. Hasonlóképpen `*foo` az összes olyan fájlra illeszkedik, amelynek a neve `foo`.<br>A mezőbe közvetlenül is beírhatja a helyettesítő karaktert. Alapértelmezés szerint a mezőben megadott értéket helyettesítő kifejezésként kezeli a rendszer.</li><li>**Reguláris kifejezések használata:** A POSIX-alapú reguláris kifejezések támogatottak. Például a reguláris kifejezés `.*\.vhd` az összes `.vhd` kiterjesztésű fájlhoz meg fog egyezni. Reguláris kifejezések esetén a `<pattern>` közvetlenül `regex(<pattern>)`ként adja meg. További információ a reguláris kifejezésekről: [reguláris kifejezés nyelve – gyors hivatkozás](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Fájl optimalizálása**              |Ha ez a funkció engedélyezve van, az 1 MB-nál kisebb fájlok a betöltés során lesznek csomagolva. Ez a csomagolás felgyorsítja a kis méretű fájlok adatmásolási feladatait. Emellett jelentős időt takaríthat meg, ha a fájlok száma messze meghaladja a címtárak számát.        |
 
4. Válassza az **Indítás**lehetőséget. A rendszer érvényesíti a bemeneteket, és ha az érvényesítés sikeres, akkor elindul a feladatok. Eltarthat néhány percig, amíg a feladatok elindulnak.

    ![Feladatok indítása a "feladatok és indítás konfigurálása" párbeszédpanelről](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. A rendszer létrehozza a megadott beállításokkal rendelkező feladatot. Feladatokhoz szüneteltetheti, folytathatja, megszakíthatja vagy újraindíthatja a feladatokat. Jelölje be a feladatok neve melletti jelölőnégyzetet, majd válassza ki a megfelelő gombot.

    ![Feladatok kezelése az "adatok másolása" oldalon](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Szüneteltetheti a feladatokat, ha az a NAS-eszköz erőforrásait a maximális idő alatt befolyásolja:

        ![Feladat szüneteltetése az "adatok másolása" oldalon](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        A feladatot később is folytathatja a munkaidőn kívüli órákban:

        ![Feladatok folytatása az "adatok másolása" oldalon](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Bármikor megszakíthatja a feladatokat:

        ![Feladat megszakítása az "adatok másolása" oldalon](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Egy feladat megszakításakor megerősítésre van szükség:

        ![A feladatok megszakításának megerősítése](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Ha úgy dönt, hogy megszakítja a feladatot, a már másolt adatok nem törlődnek. Ha törölni szeretné a Data Box eszközre másolt összes adatfájlt, állítsa alaphelyzetbe az eszközt.

        ![Eszköz alaphelyzetbe állítása](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Ha lemond vagy szüneteltet egy feladatot, előfordulhat, hogy a nagyméretű fájlok csak részben másolhatók. Ezeket a részben másolt fájlokat a rendszer az Azure-ba feltöltötte. Egy feladat megszakítása vagy felfüggesztése esetén győződjön meg arról, hogy a fájlok megfelelően lettek másolva. A fájlok ellenőrzéséhez tekintse meg az SMB-megosztásokat, vagy töltse le az AJ-fájlt.

    - Ha egy átmeneti hiba miatt nem sikerült végrehajtani a feladatot, például hálózati hibát észlelt, újraindíthatja a feladatokat. A feladatokat azonban nem lehet újraindítani, ha elérte a terminál állapotát, például **sikeres** vagy **hibákkal fejeződött**be. A feladatok meghibásodását a fájl-elnevezési vagy a fájlméretbeli problémák okozhatják. A rendszer naplózza ezeket a hibákat, de a befejezése után a feladatot nem lehet újraindítani.

        ![Sikertelen feladatok újraindítása](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Ha hibát tapasztal, és nem tudja újraindítani a feladatot, töltse le a naplókat, és keresse meg a hibát a naplófájlokban. A probléma javítása után hozzon létre egy új feladatot a fájlok másolásához. [A fájlokat SMB-kapcsolaton keresztül is másolhatja](data-box-deploy-copy-data.md).
    
    - Ebben a kiadásban nem törölhet feladatot.
    
    - Korlátlan számú feladatot hozhat létre, de egyszerre legfeljebb 10 feladatot futtathat egyszerre.
    - Ha a **fájl optimalizálása** be van kapcsolva, a kis méretű fájlok betöltése a másolási teljesítmény javítása érdekében történik. Ezekben az esetekben egy csomagolt fájl jelenik meg (a fájl neveként GUID azonosítóval fog rendelkezni). Ne törölje ezt a fájlt. A feltöltés során a rendszer kicsomagolja.

6. Amíg a művelet folyamatban van, az **adatok másolása** oldalon:

    - Az **állapot** oszlopban megtekintheti a másolási feladatok állapotát. Az állapot a következőket teheti:
        - **Fut**
        - **Sikertelen**
        - **Sikerült**
        - **Felfüggesztése**
        - **Szünetel**
        - **Érvénytelenítés**
        - **Visszavont**
        - **Hibákkal fejeződött be**
    - A **Files (fájlok** ) oszlopban láthatja a másolandó fájlok számát és teljes méretét.
    - A **feldolgozott** oszlopban láthatja a feldolgozott fájlok számát és teljes méretét.
    - A feladathoz tartozó **részletek** oszlopban válassza a **nézet** lehetőséget a feladatok részleteinek megtekintéséhez.
    - Ha bármilyen hiba fordul elő a másolási folyamat során, ahogy az a **# errors (hibák** ) oszlopban látható, lépjen a **hibanapló** oszlopra, és töltse le a hibaelhárításhoz szükséges hibákat.

Várja meg, amíg a másolási feladatok befejeződik. Mivel néhány hiba csak a **Kapcsolódás és a másolás** lapon van naplózva, győződjön meg arról, hogy a másolási feladatokhoz nem történt hiba, mielőtt a következő lépésre lép.

![Nincs hiba a "kapcsolat és másolás" oldalon](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

Az adatok integritásának biztosítása érdekében egy ellenőrzőösszeget számítunk fel az adatok másolása során. A másolás befejezése után válassza az **irányítópult megtekintése** lehetőséget, hogy ellenőrizze a felhasznált területet és a szabad területet az eszközön.
    
![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

> [!IMPORTANT]
> Ismételje meg ugyanezeket az utasításokat, hogy az Adatmásolás a Data Box Heavy második csomópontjára kerüljön.

A másolási feladatok befejezése után kiválaszthatja **szállításra való előkészítés**.

>[!NOTE]
> A **szállításra való előkészítés** nem futtatható, amíg a másolási feladatok folyamatban vannak.

## <a name="next-steps"></a>További lépések

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan szállíthatja vissza Data Box Heavy eszközét a Microsoftnak.

> [!div class="nextstepaction"]
> [Azure Data Box Heavy eszköz szállítása a Microsoftnak](./data-box-heavy-deploy-picked-up.md)

