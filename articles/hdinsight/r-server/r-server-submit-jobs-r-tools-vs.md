---
title: Feladatok elküldése az R Tools for Visual Studio alkalmazásból – Azure HDInsight
description: R-feladatok elküldése a helyi Visual Studio-gépről egy HDInsight-fürtre.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 73d1478ec2d6c90428f22a30ec82634df115d2f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435252"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Feladatok beküldése az R Tools for Visual Studio használatával

A Visual studióhoz készült [R Tools](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) a [Visual Studio 2017](https://www.visualstudio.com/downloads/)és a [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) vagy újabb verziójának közösségi (ingyenes), Professional és Enterprise kiadásának ingyenes, nyílt forráskódú bővítménye. A RTVS nem érhető el a [Visual Studio 2019](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019)-hez.

A RTVS olyan eszközöket kínál, mint például az [r interaktív ablak](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), az IntelliSense (kód befejezése), az r-könyvtárakon keresztüli [vizualizációk](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) , például a ggplot2 és a ggviz, az [r-kód hibakeresése](https://docs.microsoft.com/visualstudio/rtvs/debugging)stb.

## <a name="set-up-your-environment"></a>A környezet beállítása

1. [A Visual studióhoz készült R Tools](/visualstudio/rtvs/installing-r-tools-for-visual-studio)telepítése.

    ![A RTVS telepítése a Visual Studio 2017-ben](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Válassza ki az *adatelemzési és analitikai alkalmazások számítási feladatait* , majd válassza ki az **r nyelv támogatását**, az **r-fejlesztés futásidejű támogatását**, valamint a **Microsoft r-ügyfél** beállításait.

3. Nyilvános és titkos kulcsokat kell használnia az SSH-hitelesítéshez.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Telepítse a [ml Servert](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) a gépre. A ML Server a [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) és a `RxSpark` függvényt biztosítja.

5. Telepítse a [Putty](https://www.putty.org/) -t, hogy számítási környezetet biztosítson `RevoScaleR` függvények a helyi ügyfélről a HDInsight-fürthöz való futtatásához.

6. Lehetősége van arra, hogy az adatelemzési beállításokat a Visual Studio-környezetre alkalmazza, amely új elrendezést biztosít a munkaterület számára az R-eszközökhöz.
   1. A Visual Studio aktuális beállításainak mentéséhez használja az **eszközök > importálási és exportálási beállítások** parancsot, majd válassza a **kiválasztott környezeti Beállítások exportálása** lehetőséget, és adjon meg egy fájlnevet. A beállítások visszaállításához használja ugyanazt a parancsot, és válassza a **kiválasztott környezeti beállítások importálása**lehetőséget.

   2. Nyissa meg az **R Tools** menüelemet, majd válassza az **adatelemzési beállítások...** elemet.

       ![A Visual Studio adatelemzési beállításai](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Az 1. lépésben ismertetett módszer használatával az **adatelemzési beállítások** parancs megismétlése helyett mentheti és visszaállíthatja a személyre szabott adattudós-elrendezést.

## <a name="execute-local-r-methods"></a>Helyi R-metódusok végrehajtása

1. Hozza létre a HDInsight ML Services-fürtöt.
2. Telepítse a [RTVS bővítményt](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Töltse le a [minta zip-fájlt](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Nyissa meg `examples/Examples.sln` a megoldás indításához a Visual Studióban.
5. Nyissa meg a `1-Getting Started with R.R` fájlt a `A first look at R` megoldás mappájába.
6. A fájl elejétől kezdve nyomja le a CTRL + ENTER billentyűkombinációt az egyes sorok egyszerre történő elküldéséhez az R interaktív ablakba. Egyes sorok a csomagok telepítésekor eltarthat egy ideig.
    * Azt is megteheti, hogy kijelöli az R-fájl összes sorát (CTRL + A), majd végrehajtja az összeset (CTRL + ENTER), vagy kiválasztja az interaktív végrehajtás ikont az eszköztáron.

        ![Visual Studio – interaktív végrehajtás](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. A parancsfájlban szereplő összes sor futtatása után a következőhöz hasonló kimenetnek kell megjelennie:

    ![Visual Studio-munkaterület R-eszközei](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Feladatok elküldése egy HDInsight ML Services-fürtbe

Ha Microsoft ML Server/Microsoft R-ügyfelet használ a PuTTY-vel felszerelt Windows-számítógépről, létrehozhat egy számítási környezetet, amely elosztott `RevoScaleR` függvényeket futtat a helyi ügyfélről a HDInsight-fürtre. A számítási környezet létrehozásához használja a `RxSpark`t, adja meg a felhasználónevet, a Apache Hadoop fürt peremhálózati csomópontját, az SSH-kapcsolókat és így tovább.

1. A HDInsight-ben a ML-szolgáltatások peremhálózati csomópontjának címe `CLUSTERNAME-ed-ssh.azurehdinsight.net`, ahol a `CLUSTERNAME` a ML-szolgáltatások fürtjének neve.

1. Illessze be a következő kódot a Visual Studióban található R interaktív ablakba, és változtassa meg a telepítési változók értékeit a környezetnek megfelelően.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
          sshHostname = mySshHostname,
          sshSwitches = mySshSwitches,
          sshProfileScript = mySshProfileScript,
          consoleOutput = TRUE,
          hdfsShareDir = myHdfsShareDir,
          shareDir = myShareDir,
          sshClientDir = mySshClientDir
    )

    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```

   ![az Apache Spark beállítása a kontextusban](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. Hajtsa végre a következő parancsokat az R interaktív ablakban:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    A következőhöz hasonló kimenetnek kell megjelennie:

    ![sikeres Rx-parancs végrehajtása](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) a
1. Győződjön meg arról, hogy a `rxHadoopCopy` sikeresen átmásolta a `people.json` fájlt a példa adatmappából az újonnan létrehozott `/user/RevoShare/newUser` mappába:

    1. Az Azure HDInsight ML-szolgáltatások fürtjének paneljén válassza a bal oldali menüben a **Storage-fiókok** lehetőséget.

        ![Azure HDInsight-tár-fiókok](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Válassza ki a fürt alapértelmezett Storage-fiókját, jegyezze fel a tároló/könyvtár nevét.

    3. A Storage-fiók panel bal oldali menüjében válassza a **tárolók** lehetőséget.

        ![Azure HDInsight-tár tárolók](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Válassza ki a fürt tárolójának nevét, tallózással keresse meg a **felhasználói** mappát (Előfordulhat, hogy a lista alján a *továbbiak betöltése* lehetőségre kell kattintania), majd válassza a *RevoShare*, majd a **newUser**lehetőséget. A `people.json` fájlnak a `newUser` mappában kell megjelennie.

        ![HDInsight másolt mappa helye](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. Miután befejezte az aktuális Apache Spark környezet használatát, le kell állítania azt. Egyszerre nem futtathat több kontextust.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Következő lépések

* [Számítási környezeti beállítások a HDInsight ML-szolgáltatásaihoz](r-server-compute-contexts.md)
* A [scaleer és a sparker ötvözi](../hdinsight-hadoop-r-scaler-sparkr.md) a légitársaság repülési késési előrejelzéseit.
