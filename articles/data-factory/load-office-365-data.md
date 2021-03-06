---
title: Adatok betöltése az Office 365-ből Azure Data Factory használatával
description: Adatok másolása az Office 365-ből az Azure Data Factory használatával
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: cd2b530375beb3e8fa1f79e004f4f1ac7fd4d0bb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443911"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Adatok betöltése az Office 365-ből Azure Data Factory használatával

Ebből a cikkből megtudhatja, hogyan használhatja a Data Factory _betöltési adatait az Office 365-ből az Azure Blob Storage-ba_. A hasonló lépéseket követve másolhatja az Adatmásolást Azure Data Lake Gen1 vagy Gen2. Tekintse meg az [office 365-összekötőt](connector-office-365.md) az Office 365-ből általában az adatok másolásáról szóló cikket.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza az **erőforrás létrehozása** > **Analytics** > **Data Factory**: 
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Az **új adat-előállító** lapon adja meg az alábbi képen látható mezők értékét:
      
   ![Új adat-előállító lap](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Név**: adjon meg egy globálisan egyedi nevet az Azure-beli adatgyár számára. Ha "a *LoadFromOffice365Demo* nem érhető el" hibaüzenet jelenik meg, adjon meg egy másik nevet az adatelőállítónak. Használhatja például a _**sajátneve**_ **LoadFromOffice365Demo**nevet. Próbálkozzon újra az adatelőállító létrehozásával. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
    * **Előfizetés**: válassza ki azt az Azure-előfizetést, amelyben létre kívánja hozni az adatelőállítót. 
    * **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **új létrehozása** lehetőséget, és adja meg az erőforráscsoport nevét. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
    * **Verzió**: válassza a **v2**elemet.
    * **Hely**: válassza ki az adatelőállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. A Refactory által használt adattárak más helyszíneken és régiókban is lehetnek. Ezek az adattárak a következők: Azure Data Lake Store, Azure Storage, Azure SQL Database stb.

3. Kattintson a **Létrehozás** gombra.
4. A létrehozás befejezése után nyissa meg az adatait a gyárban. Megjelenik a **Data Factory** kezdőlapja, ahogy az a következő képen látható:
   
   ![Data factory kezdőlap](./media/load-office-365-data/data-factory-home-page.png)

5. Az Adatintegráció alkalmazás külön lapon való elindításához kattintson a **Létrehozás és figyelés** csempére.

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Az "első lépések" lapon válassza a **folyamat létrehozása**lehetőséget.
 
    ![Folyamat létrehozása](./media/load-office-365-data/create-pipeline-entry.png)

2. A folyamat **Általános lapján** adja meg a folyamat **nevét** a "CopyPipeline" értékkel.

3. A tevékenységek eszköz panelen > a & átalakítási kategória áthelyezése > húzza a **másolási tevékenységet** az eszközkészletből a folyamat tervező felületére. Adja meg a "CopyFromOffice365ToBlob" nevet a tevékenység neveként.

### <a name="configure-source"></a>Forrás konfigurálása

1. Nyissa meg a folyamat > **forrás lapot**, és kattintson az **+ új** elemre a forrás-adatkészlet létrehozásához. 

2. Az új adatkészlet ablakban válassza az **Office 365**lehetőséget, majd kattintson a **Folytatás**gombra.
 
3. Most már a másolási tevékenység konfigurációja lapon van. az Office 365-adatkészlet melletti **Szerkesztés** gombra kattintva folytathatja az adatkonfigurációt.

    ![A config Office 365 adatkészlet általános](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Ekkor megjelenik egy új lap, amely az Office 365-adatkészlethez van megnyitva. A Tulajdonságok ablak alján található **Általános lapon** adja meg a "SourceOffice365Dataset" nevet.
 
5. Lépjen a Tulajdonságok ablak **kapcsolatok lapjára** . A társított szolgáltatás szövegmező mellett kattintson az **+ új**elemre.

6. Az új társított szolgáltatás ablakban írja be a "Office365LinkedService" nevet a név mezőbe, írja be az egyszerű szolgáltatásnév és szolgáltatásnév kulcsát, majd tesztelje a kapcsolódást, és válassza a **Létrehozás** lehetőséget a társított szolgáltatás telepítéséhez.

    ![Új Office 365 társított szolgáltatás](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. A társított szolgáltatás létrehozása után a rendszer visszalépteti az adatkészlet beállításaihoz. A **táblázat**mellett kattintson a lefelé mutató nyílra az elérhető Office 365-adatkészletek listájának kibontásához, majd válassza a BasicDataSet_v0 lehetőséget. Message_v0 "a legördülő listából:

    ![Config Office 365 adatkészlet táblázata](./media/load-office-365-data/edit-dataset.png)

8. Most lépjen vissza a **folyamat** > **forrás lapjára** , hogy továbbra is konfigurálja az Office 365-kinyerés további tulajdonságait.  A felhasználói hatókör és a felhasználói hatókör szűrője választható predikátumokat határozhat meg az Office 365-ből kinyerni kívánt adatok korlátozásához. A beállítások konfigurálásával kapcsolatban lásd az [Office 365-adatkészlet tulajdonságai](https://docs.microsoft.com/azure/data-factory/connector-office-365#dataset-properties) című szakaszt.

9. Ki kell választania a Dátumszűrő egyikét, és meg kell adnia a kezdési és a befejezési időpontot.

10. Kattintson a **séma importálása** lapra az üzenet-adatkészlet sémájának importálásához.

    ![A config Office 365 adatkészlet sémája](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Fogadó konfigurálása

1. Nyissa meg a folyamat > fogadó **lapot**, és válassza az **+ új** lehetőséget a fogadó adatkészlet létrehozásához.
 
2. Az új adatkészlet ablakban figyelje meg, hogy csak a támogatott célhelyek vannak kiválasztva az Office 365-ból való másoláskor. Válassza az **Azure Blob Storage**lehetőséget, válassza a bináris formátum lehetőséget, majd kattintson a **Folytatás**gombra.  Ebben az oktatóanyagban az Office 365-es verzióját egy Azure-Blob Storage másolja.

3. Az adatkonfiguráció folytatásához kattintson az Azure Blob Storage adatkészlet melletti **Szerkesztés** gombra.

4. A Tulajdonságok ablak **Általános lapján** , a név mezőben adja meg a "OutputBlobDataset" értéket.

5. Lépjen a Tulajdonságok ablak **kapcsolatok lapjára** . A társított szolgáltatás szövege mező mellett válassza az **+ új**lehetőséget.

6. Az új társított szolgáltatás ablakban adja meg a "AzureStorageLinkedService" nevet, válassza a "szolgáltatásnév" lehetőséget a hitelesítési módszerek legördülő listából, majd töltse ki a szolgáltatási végpontot, a bérlőt, a szolgáltatás egyszerű AZONOSÍTÓját és a szolgáltatásnév kulcsát, majd válassza a mentés lehetőséget. a társított szolgáltatás üzembe helyezése.  Az Azure Blob Storage szolgáltatás egyszerű hitelesítésének beállításával kapcsolatban [itt talál](connector-azure-blob-storage.md#service-principal-authentication) további információt.

    ![Új blob társított szolgáltatás](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>A folyamat érvényesítése

A folyamat érvényesítéséhez válassza az **Érvényesítés** elemet az eszköztáron.

A folyamathoz társított JSON-kódot a jobb felső sarokban található kód elemre kattintva is megtekintheti.

## <a name="publish-the-pipeline"></a>A folyamat közzététele

A felső eszköztáron válassza az **összes közzététele**lehetőséget. Ez a művelet közzéteszi a létrehozott entitásokat (adatkészleteket és folyamatokat) a Data Factoryben.

![Módosítások közzététele](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>A folyamat manuális aktiválása

Válassza az **aktiválás hozzáadása** lehetőséget az eszköztáron, majd válassza az **aktiválás most**lehetőséget. A folyamat futtatása lapon válassza a **Befejezés**lehetőséget. 

## <a name="monitor-the-pipeline"></a>A folyamat figyelése

Lépjen a bal oldali **Figyelés** lapra. Itt láthat egy manuális eseményindító által aktivált folyamatfuttatást. A **Műveletek** oszlopban található hivatkozások használatával megtekintheti a tevékenységek részleteit, és újra futtathatja a folyamatot.

![Folyamat figyelése](./media/load-office-365-data/pipeline-status.png) 

A folyamat futtatásához társított tevékenységek megtekintéséhez válassza a műveletek oszlop **Megtekintés tevékenység** futtatása hivatkozását. Ebben a példában csak egy tevékenység található, ezért csak egy bejegyzést lát a listán. A másolási művelet részleteinek megtekintéséhez válassza a műveletek oszlop **részletek** hivatkozását (szemüveg ikon).

![Tevékenység figyelése](./media/load-office-365-data/activity-status.png) 

Ha ez az első alkalom, amikor adatokat kér ehhez a környezethez (amely az adattábla egy kombinációja, amelybe a rendszer betölti az adatokat, és hogy melyik felhasználói identitás teszi az adatelérési kérelmet), a másolási tevékenység állapota **folyamatban**, és csak akkor jelenik meg, ha a műveletek területen a részletek hivatkozásra kattint, és az állapot **RequesetingConsent**lesz látható.  Az adathozzáférés-jóváhagyó csoport tagjának jóvá kell hagynia a kérést a Privileged Access Management az adatgyűjtés folytatásához.

_Engedély kérése:_ 
![tevékenység-végrehajtás részletei – kérelem belefoglalása](./media/load-office-365-data/activity-details-request-consent.png) 

_Állapot kibontási adatokként:_

![Tevékenység-végrehajtás részletei – adatok kinyerése](./media/load-office-365-data/activity-details-extract-data.png) 

Miután megadta a beleegyezett adatgyűjtést, a rendszer folytatja az adatfeldolgozást, és egy kis idő elteltével a folyamat futása sikeresnek fog megjelenni.

![Folyamat figyelése – sikeres](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Most lépjen a cél Azure Blob Storagera, és ellenőrizze, hogy az Office 365-es Adatkivonatok bináris formátumban lettek kibontva.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megismerheti a Azure SQL Data Warehouse támogatását: 

> [!div class="nextstepaction"]
>[Office 365-összekötő](connector-office-365.md)