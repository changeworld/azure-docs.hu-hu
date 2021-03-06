---
title: A Jupyter helyi telepítése és kapcsolódás a Sparkhoz az Azure HDInsight
description: Ismerje meg, hogyan telepítheti helyileg a Jupyter-jegyzetfüzetet a számítógépre, és hogyan csatlakoztatható egy Apache Spark-fürthöz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 225ee7028b9610a4974f9bee05da667d78d3355e
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903741"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Telepítse a Jupyter notebookot a számítógépre, és kapcsolódjon Apache Spark a HDInsight

Ebből a cikkből megtudhatja, hogyan telepítheti a Jupyter notebookot az egyéni PySpark (Python) és a Apache Spark (for Scala) kernelekhez a Spark Magic használatával, és hogyan csatlakoztatható a notebook egy HDInsight-fürthöz. Számos oka lehet annak, hogy a Jupyter a helyi számítógépre telepítse, és bizonyos kihívások is lehetnek. Erről további információt a cikk végén, a [Jupyter telepítése a számítógépre](#why-should-i-install-jupyter-on-my-computer) című szakaszban talál.

A Jupyter telepítése és a HDInsight szolgáltatáshoz való Apache Spark csatlakozás négy fő lépésből áll.

* A Spark-fürt konfigurálása.
* Telepítse a Jupyter notebookot.
* Telepítse a PySpark és a Spark-kerneleket a Spark magictal.
* A Spark Magic konfigurálása a HDInsight-beli Spark-fürt eléréséhez.

További információ az egyéni kernelekről és a Spark Magic Jupyter notebookok HDInsight-fürtökkel való használatáról: [Jupyter notebookok számára elérhető kernelek Apache Spark Linux-fürtökkel a HDInsight-on](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md). Ez a Jupyter-jegyzetfüzet HDInsight-fürthöz való csatlakoztatásának előfeltétele a jegyzetfüzet telepítése után.

* A Jupyter-notebookok és a HDInsighton futó Spark használatának ismerete.

## <a name="install-jupyter-notebook-on-your-computer"></a>A Jupyter notebook telepítése a számítógépre

A Jupyter-jegyzetfüzetek telepítése előtt telepítenie kell a Pythont. Az [anaconda-disztribúció](https://www.anaconda.com/download/) a Pythont és a Jupyter notebook is telepíti.

Töltse le a platformhoz tartozó [anaconda-telepítőt](https://www.anaconda.com/download/) , és futtassa a telepítőt. A telepítővarázsló futtatásakor győződjön meg arról, hogy a PATH változóhoz a anaconda hozzáadása lehetőséget választotta.  Lásd még: a [Jupyter telepítése a anaconda használatával](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>A Spark Magic telepítése

1. A Spark Magic telepítéséhez adja meg az alábbi parancsok egyikét. Lásd még: [sparkmagic dokumentáció](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Fürt verziója | Telepítési parancs |
    |---|---|
    |v 3.6 és v 3.5 |`pip install sparkmagic==0.13.1`|
    |v 3.4|`pip install sparkmagic==0.2.3`|

1. A következő parancs futtatásával győződjön meg arról, `ipywidgets` megfelelően van telepítve:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>A PySpark és a Spark kernelek telepítése

1. A következő parancs beírásával azonosíthatja a `sparkmagic` telepítését:

    ```cmd
    pip show sparkmagic
    ```

    Ezután módosítsa a munkakönyvtárat a fenti paranccsal azonosított helyre.

1. Az új munkakönyvtárból adja meg az alábbi parancsok valamelyikét a kívánt kernel (ek) telepítéséhez:

    |Rendszermag | Parancs |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Választható. Az alábbi parancs megadásával engedélyezheti a kiszolgáló bővítményét:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>A Spark Magic konfigurálása a HDInsight Spark-fürthöz való kapcsolódáshoz

Ebben a szakaszban azt a Spark-varázst konfigurálja, amelyet korábban telepített Apache Spark fürthöz való kapcsolódáshoz.

1. Indítsa el a Python rendszerhéjt a következő paranccsal:

    ```cmd
    python
    ```

2. A Jupyter konfigurációs adatait általában a felhasználók kezdőkönyvtár tárolja. Adja meg a következő parancsot a kezdőkönyvtár azonosításához, majd hozzon létre egy **. sparkmagic**nevű mappát.  A teljes elérési út kimarad.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. A mappában `.sparkmagic`hozzon létre egy **config. JSON** nevű fájlt, és adja hozzá a következő JSON-kódrészletet benne.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Végezze el a következő módosításokat a fájlon:

    |Sablon értéke | Új érték |
    |---|---|
    |USERNAME|A fürt bejelentkezési értéke `admin`.|
    |CLUSTERDNSNAME|Fürt neve|
    |{BASE64ENCODEDPASSWORD}|A tényleges jelszó Base64 kódolású jelszava.  Base64-jelszót a következő helyen hozhatja [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Tartsa meg a `sparkmagic 0.12.7` használatát (a v 3.5 és a v 3.6 fürtöket).  `sparkmagic 0.2.3` használata esetén (fürtök v 3.4-es verzió) cserélje le a következőt: `"should_heartbeat": true`.|

    A [minta config. JSON](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)fájlban egy teljes példa látható.

   > [!TIP]  
   > A szívverések elküldése annak biztosítására, hogy a munkamenetek nem szivárognak ki. Ha egy számítógép alvó állapotba kerül, vagy leáll, a szívverés nem kerül elküldésre, ami a munkamenet tisztítását eredményezi. Ha szeretné letiltani ezt a viselkedést, a v 3.4 fürtök esetében beállíthatja a Livy config `livy.server.interactive.heartbeat.timeout`, hogy `0` a Ambari felhasználói felületéről. Ha az v 3.5 fürtök esetében nem állítja be az 3,5-es konfigurációt, a rendszer nem törli a munkamenetet.

5. Indítsa el a Jupyter. Használja a következő parancsot a parancssorból.

    ```cmd
    jupyter notebook
    ```

6. Ellenőrizze, hogy használható-e a kernelekkel elérhető Spark Magic. Hajtsa végre a következő lépéseket.

    a. Hozzon létre új notebookot. A jobb oldali sarokban válassza az **új**lehetőséget. Ekkor meg kell jelennie az alapértelmezett kernel **Python 2** vagy **Python 3** és a telepített kerneleknek. A tényleges értékek a telepítési lehetőségektől függően eltérőek lehetnek.  Válassza a **PySpark**lehetőséget.

    ![Elérhető kernelek a Jupyter notebookon](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Kernelek a Jupyter notebookon")

    > [!IMPORTANT]  
    > Miután kiválasztotta az **új** rendszerhéj áttekintését a hibákhoz.  Ha megjelenik a hiba `TypeError: __init__() got an unexpected keyword argument 'io_loop'` előfordulhat, hogy a Tornado egyes verzióiban ismert probléma jelentkezik.  Ha igen, állítsa le a kernelt, majd a következő paranccsal minősítse le a tornádó telepítését: `pip install tornado==4.5.3`.

    b. Futtassa az alábbi kódrészletet.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Ha sikeresen beolvasta a kimenetet, a rendszer teszteli a HDInsight-fürthöz való kapcsolódást.

    Ha frissíteni szeretné a jegyzetfüzet konfigurációját egy másik fürthöz való kapcsolódáshoz, frissítse a config. JSON fájlt az új értékekkel, ahogy azt a fenti 3. lépés mutatja.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Miért érdemes telepíteni a Jupyter a számítógépre?

Számos oka lehet annak, hogy miért érdemes telepíteni a Jupyter a számítógépre, majd egy Apache Spark fürthöz csatlakozni a HDInsight-on.

* Bár a Jupyter notebookok már elérhetők a Spark-fürtön az Azure HDInsight, a Jupyter telepítése a számítógépen lehetővé teszi a jegyzetfüzetek helyi létrehozását, az alkalmazás tesztelését egy futó fürtön, majd feltölti a jegyzetfüzetek a fürthöz. A jegyzetfüzetek fürtbe való feltöltéséhez feltöltheti őket a vagy a fürtöt futtató Jupyter-jegyzetfüzettel, vagy a fürthöz társított Storage-fiókban mentheti a/HdiNotebooks mappába. A jegyzetfüzetek fürtön való tárolásával kapcsolatos további információkért lásd: [Hol vannak tárolva a Jupyter notebookok](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* A helyileg elérhető jegyzetfüzetek esetében az alkalmazásra vonatkozó követelmény alapján csatlakozhat különböző Spark-fürtökhöz.
* A GitHub segítségével implementálhatja a verziókövetés rendszerét, és vezérelheti a jegyzetfüzetek verziószámát. Olyan együttműködési környezetet is használhat, amelyben több felhasználó is dolgozhat ugyanazzal a jegyzetfüzettel.
* A jegyzetfüzetekkel helyileg is dolgozhat, anélkül, hogy fürtöt kellene létesítenie. Csak egy fürtre van szüksége a jegyzetfüzetek teszteléséhez, a jegyzetfüzetek és a fejlesztési környezetek manuális kezeléséhez nem.
* Könnyebben konfigurálhatja saját helyi fejlesztési környezetét, mint a Jupyter-telepítés konfigurálása a fürtön.  Kihasználhatja az összes helyileg telepített szoftver előnyeit anélkül, hogy egy vagy több távoli fürtöt kellene konfigurálnia.

> [!WARNING]  
> Ha a Jupyter telepítve van a helyi számítógépen, a több felhasználó ugyanazon a Spark-fürtön is futtathatja ugyanazt a jegyzetfüzetet. Ilyen esetben a rendszer több Livy-munkamenetet hoz létre. Ha problémát tapasztal, és azt szeretné, hogy a rendszer hibakeresést végezzen, az egy összetett feladat, amellyel nyomon követheti, hogy melyik Livy-munkamenet melyik felhasználóhoz tartozik.  

## <a name="next-steps"></a>Következő lépések

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)
* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learning használatával: a Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
