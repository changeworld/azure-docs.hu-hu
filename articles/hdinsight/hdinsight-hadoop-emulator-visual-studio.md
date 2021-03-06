---
title: Data Lake Tools for Visual Studio & Hortonworks – Azure HDInsight
description: Megtudhatja, hogyan használhatja a Visual studióhoz készült Azure Data Lake-eszközöket a helyi virtuális gépen futó Hortonworks-homokozóval. Ezekkel az eszközökkel létrehozhat és futtathat kaptár-és Pig-feladatokat a homokozóban, és megtekintheti a feladatok kimenetét és előzményeit.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: e128aaf6e1726b7a1341fefc6df3cdafd3beb880
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500156"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>A Visual studióhoz készült Azure Data Lake Tools használata a Hortonworks sandbox használatával

A Azure Data Lake az általános Apache Hadoop fürtökkel való munkavégzéshez használható eszközöket tartalmaz. Ez a dokumentum ismerteti azokat a lépéseket, amelyek szükségesek ahhoz, hogy a Data Lake-eszközöket a helyi virtuális gépen futó Hortonworks-sandbox használatával használhassa.

A Hortonworks sandbox használatával helyileg dolgozhat a Hadoop a fejlesztői környezetben. A megoldás kifejlesztése és a nagy léptékű üzembe helyezése után a HDInsight-fürtre léphet.

## <a name="prerequisites"></a>Előfeltételek

* A Hortonworks-homokozó, amely egy virtuális gépen fut a fejlesztési környezetben. Ezt a dokumentumot az Oracle VirtualBox-ban futó homokozóban írták és tesztelték. A homokozó beállításával kapcsolatos információkért tekintse meg az [első lépések a Hortonworks homokozóban](hadoop/apache-hadoop-emulator-get-started.md) című témakört. dokumentum.

* Visual Studio.

* Az [Azure SDK for .net](https://azure.microsoft.com/downloads/) 2.7.1-es vagy újabb verziója.

* A [Visual studióhoz készült Azure Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>A homokozó jelszavainak konfigurálása

Győződjön meg arról, hogy a Hortonworks-homokozó fut. Ezután kövesse az [első lépések a Hortonworks homokozóban](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) című dokumentum lépéseit. Ezekkel a lépésekkel konfigurálhatja az SSH `root` fiók és az Apache Ambari `admin` fiók jelszavát. Ezeket a jelszavakat akkor használja a rendszer, amikor a Visual studióból csatlakozik a Sandboxhoz.

## <a name="connect-the-tools-to-the-sandbox"></a>Az eszközök összekötése a homokozóval

1. Nyissa meg a Visual studiót, válassza a **nézet**, majd a **Server Explorer**lehetőséget.

2. A **Server Explorerben**kattintson a jobb gombbal a **HDInsight** bejegyzésre, majd válassza a **Kapcsolódás a HDInsight emulatorhoz**lehetőséget.

    ![A Server Explorer és a HDInsight-emulátorhoz való kapcsolódás Kiemelt](./media/hdinsight-hadoop-emulator-visual-studio/connect-hdinsight-emulator.png)

3. A **Kapcsolódás a HDInsight-emulátorhoz** párbeszédpanelen adja meg a Ambari konfigurált jelszót.

    ![Képernyőkép a párbeszédpanelről, a ambari Password szövegmező kiemelve](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    A folytatáshoz kattintson a **Tovább** gombra.

4. A **jelszó** mező használatával adja meg a `root`-fiókhoz konfigurált jelszót. Hagyja meg a többi mezőt az alapértelmezett értéken.

    ![Képernyőkép a párbeszédpanelről, a gyökér jelszava szövegmező kiemelve](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password1.png)

    A folytatáshoz kattintson a **Tovább** gombra.

5. Várjon, amíg a szolgáltatások érvényesítése befejeződik. Bizonyos esetekben előfordulhat, hogy az érvényesítés meghiúsul, és felszólítja a konfiguráció frissítésére. Ha az érvényesítés sikertelen, válassza a **frissítés**lehetőséget, és várjon, amíg a szolgáltatás be nem fejeződik a konfiguráció és az ellenőrzés.

    ![Képernyőkép a párbeszédpanelről, a frissítés gomb kiemelve](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update-window.png)

    > [!NOTE]  
    > A frissítési folyamat a Ambari használatával módosítja a Hortonworks-homokozó konfigurációját a Visual studióhoz készült Data Lake Tools által várt módon.

6. Az érvényesítés befejezése után a **Befejezés** gombra kattintva fejezze be a konfigurálást.
    ![képernyőkép a párbeszédpanelről, a Befejezés gombbal kiemelve](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect-dialog.png)

     >[!NOTE]  
     > A fejlesztési környezet sebességétől és a virtuális géphez lefoglalt memória mennyiségétől függően több percet is igénybe vehet, hogy konfigurálja és érvényesítse a szolgáltatásokat.

Az alábbi lépések elvégzése után már rendelkezik egy **HDInsight helyi fürttel** a Server Explorerben a **HDInsight** szakaszban.

## <a name="write-an-apache-hive-query"></a>Apache Hive lekérdezés írása

A kaptár egy SQL-szerű lekérdezési nyelvet (HiveQL) biztosít a strukturált adatkezeléshez. A következő lépésekkel megtudhatja, hogyan futtathat igény szerinti lekérdezéseket a helyi fürtön.

1. A **Server Explorerben**kattintson a jobb gombbal a korábban hozzáadott helyi fürt bejegyzésére, majd válassza a **kaptár-lekérdezés írása**lehetőséget.

    ![A Server Explorer képernyőképe, a kaptár lekérdezésének megírásával](./media/hdinsight-hadoop-emulator-visual-studio/write-apache-hive-query.png)

    Megnyílik egy új lekérdezési ablak. Itt gyorsan írhat és küldhet el egy lekérdezést a helyi fürthöz.

2. Az új lekérdezési ablakban adja meg a következő parancsot:

        select count(*) from sample_08;

    A lekérdezés futtatásához válassza a **Submit (Küldés** ) lehetőséget az ablak tetején. Hagyja meg a többi értéket (**köteg** és kiszolgáló neve) az alapértelmezett értékeken.

    ![Képernyőfelvétel a lekérdezési ablakról a beküldés gomb kiemelésével](./media/hdinsight-hadoop-emulator-visual-studio/query-window-submit-hive.png)

    A **speciális**lehetőségre kattintva a **Submit (Küldés** ) melletti legördülő menüt is használhatja. A speciális beállítások lehetővé teszik további beállítások megadását a feladatok elküldésekor.

    ![A parancsfájl elküldése párbeszédpanel-struktúra képernyőképe](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-hive.png)

3. A lekérdezés elküldése után a feladatok állapota jelenik meg. A feladatok állapota a Hadoop által feldolgozott feladattal kapcsolatos információkat jeleníti meg. A **feladatok** állapota a feladatok állapotát adja meg. Az állapot rendszeresen frissül, vagy a frissítés ikon használatával manuálisan is frissítheti az állapotot.

    ![Képernyőkép a feladat nézetről párbeszédpanel, a feladat állapota kiemelve](./media/hdinsight-hadoop-emulator-visual-studio/job-view-dialog-box1.png)

    A **feladatok állapotának** **befejezését**követően egy irányított aciklikus gráf (Dag) jelenik meg. Ez az ábra a TEZ által a kaptár-lekérdezés feldolgozásakor meghatározott végrehajtási útvonalat ismerteti. A TEZ az alapértelmezett végrehajtási motor a kaptár számára a helyi fürtön.

    > [!NOTE]  
    > A Linux-alapú HDInsight-fürtök használatakor az Apache TEZ is az alapértelmezett. A Windows-alapú HDInsight nem ez az alapértelmezett érték. Ha ott szeretné használni, adja hozzá a `set hive.execution.engine = tez;` sort a kaptár-lekérdezés elejéhez.

    A kimenet megtekintéséhez használja a **feladatok kimeneti** hivatkozását. Ebben az esetben ez 823, a sorok száma a sample_08 táblában. A feladatra vonatkozó diagnosztikai információkat a **Projektnapló** és a **fonalas naplók letöltése** hivatkozásra kattintva tekintheti meg.

4. A kaptár-feladatokat interaktív módon is futtathatja, ha a **Batch** mezőt **interaktívra**módosítja. Ezután válassza a **végrehajtás**lehetőséget.

    ![Képernyőkép az interaktív és a végrehajtás gombok kiemeléséről](./media/hdinsight-hadoop-emulator-visual-studio/hdi-interactive-query.png)

    Az interaktív lekérdezés a **HiveServer2 kimeneti** ablakba való feldolgozás során generált kimeneti naplót adatfolyamként továbbítja.

    > [!NOTE]  
    > A feladatok befejeződése után az információ megegyezik a **feladatütemezés** hivatkozásával.

    ![Képernyőkép a HiveServer2 kimenetéről](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output-box.png)

## <a name="create-a-hive-project"></a>Struktúra-projekt létrehozása

Létrehozhat egy olyan projektet is, amely több kaptár-parancsfájlt is tartalmaz. Ha kapcsolódó parancsfájlokkal rendelkezik, vagy ha parancsfájlokat szeretne tárolni egy verziókövető rendszeren, használjon egy projektet.

1. A Visual Studióban válassza a **fájl**, **új**, majd **projekt**lehetőséget.

2. A projektek listájában bontsa ki a **sablonok**, majd a **Azure Data Lake**elemet, majd válassza a **kaptár (HDInsight)** elemet. A sablonok listájában válassza a **struktúra minta**lehetőséget. Adja meg a nevet és a helyet, majd kattintson **az OK gombra**.

    ![Új projekt ablak, Azure Data Lake, kaptár minta és OK](./media/hdinsight-hadoop-emulator-visual-studio/new-apache-hive-project.png)

A **kaptár minta** projekt két parancsfájlt tartalmaz: a **WebLogAnalysis. HQL** és a **SensorDataAnalysis. HQL**. Ezeket a szkripteket az ablak felső részén található **Küldés** gomb használatával küldheti el.

## <a name="create-an-apache-pig-project"></a>Apache Pig-projekt létrehozása

Míg a kaptár egy SQL-szerű nyelvet biztosít a strukturált adatokkal való munkavégzéshez, a Pig az adatokon végzett átalakítások elvégzésével működik. A Pig egy nyelvet (Pig Latin) biztosít, amely lehetővé teszi átalakítások folyamatának fejlesztését. Ha a sertést a helyi fürthöz szeretné használni, kövesse az alábbi lépéseket:

1. Nyissa meg a Visual studiót, és válassza a **fájl**, **új**, majd **projekt**lehetőséget. A projektek listájából bontsa ki a **sablonok**, majd a **Azure Data Lake**, majd a **Pig (HDInsight)** elemet. A sablonok listájában válassza a **Pig-alkalmazás**lehetőséget. Adja meg a nevet, a helyet, majd kattintson **az OK gombra**.

    ![Képernyőfelvétel az új projekt ablakról, a Azure Data Lake, a Pig, a Pig alkalmazás és az OK kiemelésével](./media/hdinsight-hadoop-emulator-visual-studio/new-apche-pig-project.png)

2. Adja meg a következő szöveget a projekttel létrehozott **script. Pig** fájl tartalmában.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Míg a Pig más nyelvet használ, mint a kaptár, a feladatok futtatásának módja mindkét nyelv között konzisztens, a **Submit (Küldés** ) gombra kattintva. Ha a **Küldés** melletti legördülő menüre kattint, megjelenik a Pig speciális beküldési párbeszédpanelje.

    ![A szkript elküldése párbeszédpanel-Pig képernyőképe](./media/hdinsight-hadoop-emulator-visual-studio/advanced-apache-pig1.png)

3. A feladatok állapota és kimenete is megjelenik, ugyanaz, mint a kaptár lekérdezése.

    ![Képernyőkép egy befejezett Pig-feladatokról](./media/hdinsight-hadoop-emulator-visual-studio/completed-apache-pig.png)

## <a name="view-jobs"></a>Feladatok megtekintése

Data Lake eszközök segítségével egyszerűen megtekintheti a Hadoop-on futtatott feladatokkal kapcsolatos információkat. A következő lépésekkel tekintheti meg a helyi fürtön futtatott feladatokat.

1. A **Server Explorerben**kattintson a jobb gombbal a helyi fürtre, majd válassza a **feladatok megtekintése**lehetőséget. Megjelenik a fürtnek elküldött feladatok listája.

    ![A Server Explorer képernyőképe a Kiemelt feladatok megjelenítésével](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-view-jobs.png)

2. A feladatok listájában válassza az egyik elemet a feladat részleteinek megtekintéséhez.

    ![Képernyőfelvétel a feladat böngészőről, a Kiemelt feladatok egyikével](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-job-details.png)

    A megjelenő információk hasonlóak ahhoz, amit a struktúra vagy a Pig lekérdezés futtatása után lát, beleértve a kimeneti és a naplózási információk megtekintésére szolgáló hivatkozásokat is.

3. A feladatot itt is módosíthatja, majd újból elküldheti.

## <a name="view-hive-databases"></a>Struktúra-adatbázisok megtekintése

1. A **Server Explorerben**bontsa ki a **HDInsight helyi fürt** bejegyzést, majd bontsa ki a **kaptár-adatbázisok**csomópontot. Megjelennek a helyi fürtön lévő **alapértelmezett** és **xademo** adatbázisok. Az adatbázisok kibontása az adatbázisban található táblákat jeleníti meg.

    ![Képernyőfelvétel a Server Explorerről, az adatbázisok kibontásával](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases-view.png)

2. A táblázat kibontása megjeleníti a tábla oszlopait. Az adatmegjelenítéshez kattintson a jobb gombbal a táblázatra, majd válassza a **felső 100-sorok megtekintése**lehetőséget.

    ![Server Explorer, a táblázat kibontva és a felső 100-sorok megjelenítése kiválasztva](./media/hdinsight-hadoop-emulator-visual-studio/hdi-view-top-100-rows.png)

### <a name="database-and-table-properties"></a>Adatbázis és tábla tulajdonságai

Megtekintheti egy adatbázis vagy tábla tulajdonságait. A **Tulajdonságok** lehetőség kiválasztásával a Tulajdonságok ablakban a kijelölt elemmel kapcsolatos részletek jelennek meg. Tekintse meg például a következő képernyőképen látható információkat:

![Képernyőkép a Tulajdonságok ablakról](./media/hdinsight-hadoop-emulator-visual-studio/hdi-properties-window.png)

### <a name="create-a-table"></a>Tábla létrehozása

Tábla létrehozásához kattintson a jobb gombbal egy adatbázisra, majd válassza a **tábla létrehozása**lehetőséget.

![A Server Explorer képernyőképe, a CREATE TABLE kiemelve](./media/hdinsight-hadoop-emulator-visual-studio/server-explorer-create-table.png)

Ezután létrehozhatja a táblázatot egy űrlap használatával. A következő képernyőkép alján láthatja a tábla létrehozásához használt nyers HiveQL.

![A táblázat létrehozásához használt űrlap képernyőképe](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form-box.png)

## <a name="next-steps"></a>További lépések

* [A Hortonworks-Homokozóhoz tartozó kötelek megismerése](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Apache Hadoop oktatóanyag – első lépések a HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
