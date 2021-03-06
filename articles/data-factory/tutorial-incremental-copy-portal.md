---
title: Táblázat növekményes másolása Azure Portal használatával
description: Az oktatóanyag során egy Azure Data Factory-folyamatot hoz majd létre, amely adatokat másol be növekményesen egy Azure SQL-adatbázisból egy Azure Blob Storage-tárolóba.
services: data-factory
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 01/11/2018
ms.openlocfilehash: a908b44f596e6ca1ecd2960110a35661448c2e1a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982652"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage-using-the-azure-portal"></a>Adatok növekményes betöltése az Azure SQL Database-ből az Azure Blob Storage-ba a Azure Portal használatával

Az oktatóanyag során egy Azure adat-előállítót hoz majd létre egy olyan folyamattal, amely módosított adatokat tölt be egy Azure SQL-adatbázisban lévő táblából egy Azure Blob Storage-tárolóba.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Az adatraktár előkészítése a küszöbértékek tárolására.
> * Adat-előállító létrehozása
> * Társított szolgáltatások létrehozása.
> * Forrás-, fogadó- és küszöbadatkészletek létrehozása.
> * Folyamat létrehozása.
> * A folyamat futtatása.
> * A folyamat futásának monitorozása.
> * Eredmények áttekintése
> * További adatok hozzáadása a forráshoz.
> * A folyamat újbóli futtatása.
> * A folyamat második futtatásának monitorozása.
> * A második futtatás eredményeinek ellenőrzése.


## <a name="overview"></a>Áttekintés
Itt látható a megoldás összefoglaló jellegű ábrája:

![Adatok növekményes betöltése](media/tutorial-Incremental-copy-portal/incrementally-load.png)

Az alábbiak a megoldás kialakításának leglényegesebb lépései:

1. **A küszöb oszlopának kiválasztása**.
    Jelölje ki az adatforrás egyik oszlopát, amelynek alapján az új és a frissített rekordok minden egyes futtatáskor leválogathatók. Normális esetben az ebben a kiválasztott oszlopban (például: last_modify_time vagy ID) lévő adatok a sorok létrehozásával vagy frissítésével folyamatosan növekednek. Az ebben az oszlopban lévő legnagyobb érték szolgál a küszöbként.

2. **Egy adatraktár előkészítése a küszöbértékek tárolására**. Ebben az oktatóanyagban a küszöbértékeket egy SQL-adatbázisban tároljuk.

3. **Egy folyamat létrehozása a következő munkafolyamattal**:

    Ebben a megoldásban a folyamat a következő tevékenységeket tartalmazza:

    * Két keresési tevékenység létrehozása. Az első keresési tevékenység az utolsó küszöbértéket kéri le. A második keresési tevékenység az új küszöbértéket kéri le. Ezeket a küszöbértékeket a rendszer átadja a másolási tevékenységnek.
    * Egy másolási tevékenység létrehozása, amely a küszöbértéket tartalmazó oszlopban a régi küszöbértéknél magasabb, az új küszöbértéknél alacsonyabb értékkel rendelkező sorokat másolja át a forrásadattárból. Ezután a módosított adatokat a forrásadattárból új fájlként egy Blob Storage-tárolóba másolja.
    * Egy StoredProcedure tevékenység létrehozása, amely frissíti a küszöbértékeket a folyamat következő futtatásához.


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek
* **Azure SQL Database** Ezt az adatbázist használjuk forrásadattárként. Ha még nem rendelkezik SQL-adatbázissal, a létrehozás folyamatáért lásd az [Azure SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikket.
* **Azure Storage**. A blobtárolót használjuk majd fogadóadattárként. Ha még nem rendelkezik tárfiókkal, tekintse meg a [tárfiók létrehozásának](../storage/common/storage-account-create.md) lépéseit ismertető cikket. Hozzon létre egy tárolót adftutorial néven. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Adatforrástábla létrehozása az SQL-adatbázisban
1. Nyissa meg az SQL Server Management Studiót. A **Kiszolgálókezelőben** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés** elemet.

2. Futtassa a következő SQL-parancsot az SQL-adatbázison egy tábla `data_source_table` néven, adatforrástárként történő létrehozásához:

    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    Az oktatóanyagban a LastModifytime oszlopot használjuk küszöboszlopként. Az adatforrástár adatai az alábbi táblázatban láthatók:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Egy másik tábla létrehozása az SQL-adatbázisban a felső küszöbértékek tárolására
1. Futtassa a következő SQL-parancsot az SQL-adatbázison egy `watermarktable` nevű, a küszöbértékek tárolására szolgáló tábla létrehozásához:  

    ```sql
    create table watermarktable
    (

    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Állítsa be a felső küszöb alapértelmezett értékét a forrásadattár táblanevével. Ebben az oktatóanyagban a tábla neve a következő: data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Tekintse át a következő tábla adatait: `watermarktable`.

    ```sql
    Select * from watermarktable
    ```
    Kimenet:

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Tárolt eljárás létrehozása az SQL-adatbázisban

Az alábbi parancs futtatásával hozzon létre egy tárolt eljárást az SQL-adatbázisban:

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime
WHERE [TableName] = @TableName

END
```

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. A bal oldali menüben válassza az **erőforrás létrehozása** > **Analytics** > **Data Factory**:

   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Az **Új data factory** lapon, a **Név** mezőben adja meg a következőt: **ADFIncCopyTutorialDF**.

   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha egy piros felkiáltójelet lát a következő hibaüzenettel, változtassa meg az adat-előállító nevét (például a következőre: sajátneveADFIncCopyTutorialDF), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

       `Data factory name "ADFIncCopyTutorialDF" is not available`
4. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
5. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:

      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot.
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
        Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
6. A **Verzió** résznél válassza a **V2** értéket.
7. Válassza ki a Data Factory **helyét**. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.
8. Kattintson a **Create** (Létrehozás) gombra.      
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.

   ![Data factory kezdőlap](./media/doc-common-process/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére.

## <a name="create-a-pipeline"></a>Folyamat létrehozása
Az oktatóanyag során egy olyan folyamatot fogunk létrehozni, amelyben két keresési, egy másolási és egy StoredProcedure (tárolt eljárás) tevékenység van összefűzve.

1. A Data Factory felhasználói felületének **első lépéseket ismertető** oldalán kattintson a **Folyamat létrehozása** csempére.

   ![A Data Factory felhasználói felületének első lépéseket ismertető oldala](./media/doc-common-process/get-started-page.png)    
3. A folyamat **tulajdonságait** tartalmazó ablak **általános** lapján adja meg az **IncrementalCopyPipeline** nevet.

4. Adja meg az első keresési tevékenységet a régi küszöbérték lekéréséhez. A **Tevékenységek** eszközkészletben bontsa ki az **Általános** elemet, és húzza a **Keresés** tevékenységet a folyamat tervezőfelületére. Változtassa a tevékenység nevét a következőre: **LookupOldWaterMarkActivity**.

   ![Első keresési tevékenység – név](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. Váltson a **Beállítások** lapra, és a **Forrásadatkészlet** elemnél kattintson az **+ Új** gombra. Ebben a lépésben egy olyan adatkészletet hoz létre, amely a **küszöbértékek táblájában** található adatokat jelöli. Ez a tábla tartalmazza az előző másolási művelet során használt régi küszöbértéket.

6. Az **új adatkészlet** ablakban válassza a **Azure SQL Database**lehetőséget, majd kattintson a **Folytatás**gombra. Ekkor megjelenik egy új ablak, amely megnyílik az adatkészlethez.

7. Az adatkészlet **tulajdonságok beállítása** ablakában adja meg a **WatermarkDataset** **nevet**.

8. A **társított szolgáltatás**esetében válassza az **új**lehetőséget, majd hajtsa végre a következő lépéseket:

    1. A **Név** mezőbe írja az **AzureSqlDatabaseLinkedService** nevet.
    2. A **kiszolgáló nevénél** válassza ki az Azure SQL Servert.
    3. Válassza ki az **adatbázis nevét** a legördülő listából.
    4. Adja meg **felhasználónevét** & **jelszavát**.
    5. Az Azure SQL-adatbázis kapcsolatának teszteléséhez kattintson a **Kapcsolat tesztelése** elemre.
    6. Kattintson a **Befejezés** gombra.
    7. Ellenőrizze, hogy a **AzureSqlDatabaseLinkedService** van-e kiválasztva a **társított szolgáltatáshoz**.

        ![Új társított szolgáltatás ablak](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
    8. Válassza a **Finish** (Befejezés) elemet.
9. A **kapcsolatok** lapon válassza a **[dbo] lehetőséget. [ watermarktable]** a **táblához**. A táblában található adatok előnézetének megtekintéséhez kattintson az **Adatok előnézete** elemre.

    ![Küszöbérték-adatkészlet – kapcsolat beállításai](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
10. A folyamatszerkesztőt úgy érheti el, ha a fenti folyamat fülre vagy a bal oldali fanézetben a folyamat nevére kattint. A **keresési** tevékenység tulajdonságainak lapján ellenőrizze, hogy a **WatermarkDataset** lehetőség van-e kiválasztva a **Forrásadatkészlet** mezőnél.

11. A **tevékenységek** eszközkészletében bontsa ki az **Általános** elemet, húzzon egy másik **keresési** tevékenységet a folyamat tervezőfelületére, és a tulajdonságok ablakának **Általános** lapján állítsa a nevet a következőre: **LookupNewWaterMarkActivity**. Ez a keresési tevékenység a célhelyre átmásolandó forrásadatokat tartalmazó táblából kap új küszöbértéket.

12. A második **keresési** tevékenység tulajdonságainak ablakában váltson a **Beállítások** lapra, majd kattintson az **Új** gombra. Így létrehoz egy, az új küszöbértéket (LastModifyTime maximális értéke) tartalmazó táblára mutató adatkészletet.

13. Az **új adatkészlet** ablakban válassza a **Azure SQL Database**lehetőséget, majd kattintson a **Folytatás**gombra.
14. A **készlet tulajdonságai** ablakban adja meg a **SourceDataset** **nevet**. A **Társított szolgáltatás** elemnél válassza az **AzureSqlDatabaseLinkedService** lehetőséget.
15. A Tábla elemhez válassza a **[dbo].[data_source_table]** lehetőséget. Az oktatóanyag során később megadunk egy olyan lekérdezést, amely az adatkészletre vonatkozik. A lekérdezés elsőbbséget élvez az ebben a lépésben megadott táblával szemben.
16. Válassza a **Finish** (Befejezés) elemet.
17. A folyamatszerkesztőt úgy érheti el, ha a fenti folyamat fülre vagy a bal oldali fanézetben a folyamat nevére kattint. A **keresési** tevékenység tulajdonságainak lapján ellenőrizze, hogy a **SourceDataset** lehetőség van-e kiválasztva a **Forrásadatkészlet** mezőnél.
18. A **Lekérdezés használata** mezőben válassza a **Lekérdezés** lehetőséget, majd írja be a következő lekérdezést: Ön csak a **LastModifyTime** érték maximális értékét választja ki a **data_source_table** táblából. Győződjön meg arról, hogy csak az **első sort**jelölte be.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![Második keresési tevékenység – lekérdezés](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. A **tevékenységek** eszközkészletben bontsa ki az **Áthelyezés & átalakítás**elemet, majd húzza a **Másolás** tevékenységet a tevékenységek eszközkészletből, és állítsa a nevet **IncrementalCopyActivity**értékre.

20. A keresési tevékenységhez csatolt **zöld gombot** a másolási tevékenységhez húzva **kapcsolja mindkét keresési tevékenységet a másolási tevékenységhez**. Amikor a másolási tevékenység szegélyének színe kékre vált, engedje el az egér gombját.

    ![Keresési tevékenységek hozzákapcsolása egy másolási tevékenységhez](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. Válassza ki a **másolási tevékenységet**, és győződjön meg arról, hogy a **tulajdonságok** ablakában megjelennek a tevékenység tulajdonságai.

22. Váltson a **Forrás** lapra a **tulajdonságok** ablakában, és hajtsa végre a következő lépéseket:

    1. Válassza ki a **SourceDataset** elemet a **Forrásadatkészlet** mezőnél.
    2. A **Lekérdezés használata** mezőnél válassza a **Lekérdezés** lehetőséget.
    3. Írja be a következő SQL-lekérdezést a **Lekérdezés** mezőbe.

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```

        ![Másolási tevékenység – forrás](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. Váltson a **Fogadó** lapra, és kattintson az **+ Új** lehetőségre a **Fogadó adatkészlet** mezőben.

24. Ebben az oktatóanyagban a fogadó adattár típusa Azure Blob Storage. Ezért válassza az **Azure Blob Storage**lehetőséget, majd kattintson a **Continue (folytatás** ) gombra az **új adatkészlet** ablakban.
25. A **formátum kiválasztása** ablakban válassza ki az adatai formátumának típusát, majd kattintson a **Folytatás**gombra.
25. A **készlet tulajdonságai** ablakban adja meg a **SinkDataset** **nevet**. A **társított szolgáltatás**esetében válassza az **+ új**lehetőséget. Ebben a lépésben létrehoz egy kapcsolatot (társított szolgáltatást) az **Azure Blob-tárolóhoz**.
26. Az **új társított szolgáltatás (Azure Blob Storage)** ablakban végezze el a következő lépéseket:

    1. A **Név** mezőbe írja be az **AzureStorageLinkedService** nevet.
    2. A **Storage-fiók neve** elemnél válassza ki saját Azure Storage-fiókját.
    3. Tesztelje a kapcsolatokat, majd kattintson a **Befejezés**gombra.

27. A **készlet tulajdonságai** ablakban ellenőrizze, hogy a **AzureStorageLinkedService** van-e kiválasztva a **társított szolgáltatáshoz**. Ezután kattintson a **Befejezés** gombra.
28. Nyissa meg a SinkDataset **Kapcsolódás** lapját, és hajtsa végre a következő lépéseket:
    1. A **fájl elérési útja** mezőbe írja be a következőt: **adftutorial/incrementalcopy**. Az **adftutorial** a blobtároló, az **incrementalcopy** pedig a mappa neve. Ez a kódtöredék azt feltételezi, hogy a Blob Storage-ban rendelkezik egy adftutorial nevű blobtárolóval. Ha még nem létezik, hozza létre a tárolót, vagy állítsa be egy meglévő tároló nevét. Az Azure Data Factory automatikusan létrehozza az **incrementalcopy** kimeneti mappát, ha az még nem létezik. A **fájl elérési útjánál** a **Tallózás** gombot is használhatja a blobtárolóban található mappák megkereséséhez.
    2. A **fájl elérési útja** mező **fájljának** részeként válassza a **dinamikus tartalom hozzáadása [ALT + P]** lehetőséget, majd a megnyitott ablakban adja meg `@CONCAT('Incremental-', pipeline().RunId, '.txt')`. Ezután kattintson a **Befejezés** gombra. A fájl neve dinamikusan jön létre a kifejezés használatával. A folyamat minden futtatásához tartozik egy egyedi azonosító. A másolási tevékenység a futtatási azonosítót használja a fájlnév létrehozásához.

28. A **folyamatszerkesztőt** úgy érheti el, ha a fenti folyamat fülre vagy a bal oldali fanézetben a folyamat nevére kattint.
29. A **tevékenységek** eszközkészletében bontsa ki az **Általános** elemet, és húzza a **tárolt eljárási** tevékenységet a **tevékenységek** eszközkészletéből a folyamat tervezőfelületére. **Kapcsolja** a **másolási** tevékenység zöld (sikeres) kimenetét a **tárolt eljárási** tevékenységhez.

24. Válassza ki a **tárolt eljárási tevékenységet** a folyamattervezőben, és módosítsa a nevét a következőre: **StoredProceduretoWriteWatermarkActivity**.

25. Váltson az **SQL-fiók** lapra, és válassza a **AzureSqlDatabaseLinkedService** lehetőséget a **társított szolgáltatáshoz**.

26. Váltson a **Tárolt eljárás** lapra, és végezze el az alábbi lépéseket:

    1. A **tárolt eljárás neve**mezőben válassza a **usp_write_watermark**lehetőséget.
    2. A tárolt eljárás paraméterértékeinek megadásához kattintson a **Paraméter importálása** gombra, és adja meg az alábbi értékeket a paraméterekhez:

        | Név | Type (Típus) | Value (Díj) |
        | ---- | ---- | ----- |
        | LastModifiedtime | Dátum és idő | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | Sztring | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![Tárolt eljárási tevékenység – tárolt eljárás beállításai](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. A folyamat beállításainak érvényesítéséhez kattintson az **Érvényesítés** gombra az eszköztáron. Ellenőrizze, hogy nincs-e érvényesítési hiba. A **folyamatérvényesítési jelentés** ablakának bezárásához kattintson a >> gombra.   

28. Az entitásokat (társított szolgáltatásokat, adatkészleteket és folyamatokat) az **Összes közzététele** elem kiválasztásával teheti közzé az Azure Data Factory szolgáltatásban. Várjon, amíg megjelenik a sikeres közzétételt jelző üzenet.


## <a name="trigger-a-pipeline-run"></a>Folyamat futtatásának aktiválása
1. Kattintson az **trigger hozzáadása** elemre az eszköztáron, majd az **aktiválás most**elemre.

2. A **Folyamatfuttatás** ablakban kattintson a **Befejezés** gombra.

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. Váltson a bal oldali **Monitorozás** lapra. Láthatja a manuális eseményindítás által elindított folyamatfuttatás állapotát. A lista frissítéséhez kattintson a **Frissítés** gombra.

2. A folyamatfuttatáshoz társított tevékenységfuttatások megtekintéséhez kattintson az első hivatkozásra (**Tevékenységfuttatások megtekintése**) a **Műveletek** oszlopban. A fenti **Folyamatok** elemre kattintva visszaválthat az előző nézetre. A lista frissítéséhez kattintson a **Frissítés** gombra.


## <a name="review-the-results"></a>Az eredmények áttekintése
1. Kapcsolódjon Azure Storage-fiókjához az [Azure Storage Explorerrel](https://azure.microsoft.com/features/storage-explorer/) vagy valamilyen hozzá hasonló eszközzel. Ellenőrizze, hogy létrejött-e egy kimeneti fájl az **adftutorial** tároló **incrementalcopy** nevű mappájában.

    ![Első kimeneti fájl](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. Ha megnyitja a kimeneti fájlt, láthatja, hogy a **data_source_table** minden adata át lett másolva a blobfájlba.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. Ellenőrizze a `watermarktable` legutolsó értékét. Láthatja, hogy a küszöbérték frissült.

    ```sql
    Select * from watermarktable
    ```

    A kimenet itt látható:

    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 |

## <a name="add-more-data-to-source"></a>További adatok hozzáadása a forráshoz

Szúrjon be új adatokat az SQL-adatbázisba (forrásadattár).

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
```

Az SQL-adatbázis a következő frissített adatokat tartalmazza:

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>Még egy folyamatfuttatás aktiválása
1. Váltson a **Szerkesztés** lapra. kattintson a folyamatra a fanézetben, ha az nincs megnyitva a tervezőben.

2. Kattintson az **trigger hozzáadása** elemre az eszköztáron, majd az **aktiválás most**elemre.


## <a name="monitor-the-second-pipeline-run"></a>A folyamat második futtatásának monitorozása.

1. Váltson a bal oldali **Monitorozás** lapra. Láthatja a manuális eseményindítás által elindított folyamatfuttatás állapotát. A lista frissítéséhez kattintson a **Frissítés** gombra.

2. A folyamatfuttatáshoz társított tevékenységfuttatások megtekintéséhez kattintson az első hivatkozásra (**Tevékenységfuttatások megtekintése**) a **Műveletek** oszlopban. A fenti **Folyamatok** elemre kattintva visszaválthat az előző nézetre. A lista frissítéséhez kattintson a **Frissítés** gombra.


## <a name="verify-the-second-output"></a>A második kimenet ellenőrzése

1. A blobtárolóban azt láthatja, hogy egy újabb fájl jött létre. Ebben az oktatóanyagban az új fájl neve a következő: `Incremental-<GUID>.txt`. Nyissa meg ezt a fájlt, és láthatja, hogy két sornyi rekordot tartalmaz.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. Ellenőrizze a `watermarktable` legutolsó értékét. Láthatja, hogy a küszöbérték ismét frissült.

    ```sql
    Select * from watermarktable
    ```
    Példa a kimenetre:

    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |



## <a name="next-steps"></a>Következő lépések
Az oktatóanyagban az alábbi lépéseket hajtotta végre:

> [!div class="checklist"]
> * Az adatraktár előkészítése a küszöbértékek tárolására.
> * Adat-előállító létrehozása
> * Társított szolgáltatások létrehozása.
> * Forrás-, fogadó- és küszöbadatkészletek létrehozása.
> * Folyamat létrehozása.
> * A folyamat futtatása.
> * A folyamat futásának monitorozása.
> * Eredmények áttekintése
> * További adatok hozzáadása a forráshoz.
> * A folyamat újbóli futtatása.
> * A folyamat második futtatásának monitorozása.
> * A második futtatás eredményeinek ellenőrzése.

Ebben az oktatóanyagban a folyamat egy SQL-adatbázisban lévő egyetlen táblából másolt adatokat egy blobtárolóba. Folytassa a következő oktatóanyaggal, amelyben azzal ismerkedhet meg, hogyan másolhat adatokat egy helyszíni SQL Server adatbázis több táblájából egy SQL-adatbázisba.

> [!div class="nextstepaction"]
>[Adatok növekményes betöltése az SQL Server több táblájából az Azure SQL-adatbázisba](tutorial-incremental-copy-multiple-tables-portal.md)
