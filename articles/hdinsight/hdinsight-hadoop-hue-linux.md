---
title: Hue a Hadoop on HDInsight Linux-alapú fürtökön – Azure
description: Megtudhatja, hogyan telepítheti a Hue-t a HDInsight-fürtökre, és hogyan használhatja a bújtatást a kérések Színezetbe irányításához A Hue használatával böngészhet a tárolóban, és futtathatja a kaptár vagy a Pig-t.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 69acfd4f2edab9be1b1dcfbb52eafbd00aec712f
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934561"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>A Hue telepítése és használata a HDInsight Hadoop-fürtökön

Megtudhatja, hogyan telepítheti a Hue-t a HDInsight-fürtökre, és hogyan használhatja a bújtatást a kérések Színezetbe irányításához

## <a name="what-is-hue"></a>Mi az a Hue?

A Hue egy Apache Hadoop-fürttel való interakcióhoz használt webalkalmazások összessége. A Hue használatával megkeresheti a Hadoop-fürthöz társított tárolót (WASB, HDInsight-fürtök esetén), és futtathatja a kaptár-feladatokat és a Pig-parancsfájlokat, és így tovább. Az alábbi összetevők HDInsight Hadoop-fürtön található Hue-telepítésekkel érhetők el.

* Méhviasz-struktúra szerkesztője
* Apache Pig
* Metaadattár-kezelő
* Apache Oozie
* Fájlböngésző (amely a WASB alapértelmezett tárolóra beszél)
* Feladatok böngészője

> [!WARNING]  
> A HDInsight-fürthöz biztosított összetevők teljes mértékben támogatottak, és Microsoft ügyfélszolgálata az ezen összetevőkkel kapcsolatos problémák elkülönítésében és megoldásában is segítséget nyújt.
>
> Az egyéni összetevők kereskedelmileg ésszerű támogatást kapnak a probléma további megoldásához. Ez a probléma megoldásához vezethet, vagy megkérdezheti, hogy a nyílt forráskódú technológiákhoz elérhető csatornákat szeretne-e felvenni. Többek között számos közösségi webhely használható, például a [következőhöz: HDInsight MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight) [https://stackoverflow.com](https://stackoverflow.com). Emellett az Apache-projektek [https://apache.org on ](https://apache.org)is rendelkeznek projekt-webhelyekkel, például: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>A Hue telepítése parancsfájl-műveletek használatával

Használja az alábbi táblázatban szereplő információkat a parancsfájl-művelethez. Lásd: [HDInsight-fürtök testreszabása parancsfájl-műveletekkel](hdinsight-hadoop-customize-cluster-linux.md) a parancsfájl-műveletek használatával kapcsolatos konkrét utasításokhoz.

> [!NOTE]  
> A Hue HDInsight-fürtökön való telepítéséhez az ajánlott átjárócsomóponthoz mérete legalább a4 (8 mag, 14 GB memória).

|Tulajdonság |Value (Díj) |
|---|---|
|Parancsfájl típusa:|– Egyéni|
|Név|A Hue telepítése|
|Bash-parancsfájl URI-ja|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Csomópont típusa (i):|Fej|

## <a name="use-hue-with-hdinsight-clusters"></a>A Hue használata HDInsight-fürtökkel

Az SSH-bújtatás az egyetlen módszer a színárnyalat elérésére a fürtön, ha fut. Az SSH-n keresztüli bújtatás lehetővé teszi, hogy a forgalom közvetlenül annak a fürtnek a átjárócsomóponthoz menjen, ahol a Hue fut. Miután a fürt befejezte az üzembe helyezést, a következő lépésekkel használhatja a Hue szolgáltatást egy HDInsight-fürtön.

> [!NOTE]  
> Javasoljuk, hogy kövesse az alábbi utasításokat a Firefox böngésző használatával.

1. Az [SSH-bújtatás használata az Apache Ambari webes felhasználói felület, a erőforráskezelő, a JobHistory, a NameNode, a Oozie és az egyéb webes kezelőfelületek eléréséhez](hdinsight-linux-ambari-ssh-tunnel.md) használja az SSH-alagutat az ügyfél rendszeréről a HDInsight-fürtre, majd konfigurálja úgy a webböngészőt, hogy az SSH-alagutat proxyként használja.

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](./hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Csatlakozás után a következő paranccsal szerezheti be az elsődleges átjárócsomóponthoz teljes tartománynevét:

    ```bash
    hostname -f
    ```

    Ez a következőhöz hasonló nevet ad vissza:

        myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Ez az az elsődleges átjárócsomóponthoz állomásneve, ahol a Hue webhely található.

1. A böngészőben nyissa meg a Hue portált `http://HOSTNAME:8888`címen. Cserélje le az ÁLLOMÁSNÉV nevet az előző lépésben beszerzett névre.

   > [!NOTE]  
   > Amikor első alkalommal jelentkezik be, a rendszer felszólítja, hogy hozzon létre egy fiókot a Hue Portalra való bejelentkezéshez. Az itt megadott hitelesítő adatokat a rendszer a portálra korlátozza, és nem kapcsolódik a fürt kiépítésekor megadott rendszergazdai vagy SSH-felhasználói hitelesítő adatokhoz.

    ![HDInsight Hue Portal bejelentkezési ablak](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Hitelesítő adatok megadása a Hue Portalhoz")

### <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

1. A Hue Portalon válassza a **lekérdezési szerkesztők**lehetőséget, majd válassza a **kaptár** elemet a kaptár-szerkesztő megnyitásához.

    ![HDInsight Hue-portál a kaptár-szerkesztő használatával](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Hive használata")

2. Az **assziszt** lapon az **adatbázis**területen tekintse meg a **hivesampletable**. Ez egy minta tábla, amely a HDInsight összes Hadoop-fürtjével együtt kapható. Adjon meg egy minta lekérdezést a jobb oldali ablaktáblán, és tekintse meg az alábbi ablaktábla **eredmények** lapján látható kimenetet, ahogy az a képernyőfelvételen látható.

    ![HDInsight Hue-portál struktúrájának lekérdezése](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Struktúra-lekérdezés futtatása")

    A **diagram** lapon megtekintheti az eredmény vizuális ábrázolását is.

### <a name="browse-the-cluster-storage"></a>A fürt tárterületének tallózása

1. A Hue Portalon válassza a menüsáv jobb felső sarkában található **fájlkezelő** elemet.
2. Alapértelmezés szerint a fájlkezelő a **/User/myuser** könyvtárban nyílik meg. A fürthöz társított Azure Storage-tároló gyökeréhez való ugráshoz válassza a perjel közvetlenül az elérési úton lévő felhasználói könyvtárat.

    ![HDInsight Hue-portál fájljának böngészője](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "A fájl böngésző használata")

3. Kattintson a jobb gombbal egy fájlra vagy mappára az elérhető műveletek megtekintéséhez. A jobb oldali sarokban található **feltöltés** gomb használatával fájlokat tölthet fel az aktuális könyvtárba. Új fájlok vagy könyvtárak létrehozásához használja az **új** gombot.

> [!NOTE]  
> A Hue file Browser csak a HDInsight-fürthöz társított alapértelmezett tároló tartalmát jeleníti meg. A fürthöz esetlegesen hozzárendelt további Storage-fiókok vagy tárolók nem lesznek elérhetők a fájlkezelő használatával. A fürthöz társított további tárolók azonban mindig elérhetővé válnak a kaptár-feladatok számára. Ha például beírja a `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` parancsot a kaptár-szerkesztőben, a további tárolók tartalmát is megtekintheti. Ebben a parancsban a **newcontainer** nem a fürthöz társított alapértelmezett tároló.

## <a name="important-considerations"></a>Fontos szempontok

1. A Hue telepítéséhez használt parancsfájl csak a fürt elsődleges átjárócsomóponthoz telepíti.

1. A telepítés során a rendszer több Hadoop szolgáltatást (HDFS, FONALat, MR2, Oozie) indít el a konfiguráció frissítéséhez. Miután a parancsfájl befejezte a Hue telepítését, eltarthat egy ideig, amíg a többi Hadoop-szolgáltatás elindul. Ez hatással lehet a Hue teljesítményére. Az összes szolgáltatás elindítása után a Hue teljes mértékben működőképes lesz.

1. A Hue nem érti az Apache TEZ-feladatokat, ami a struktúra aktuális alapértelmezett értéke. Ha a MapReduce-t a kaptár-végrehajtó motorként szeretné használni, frissítse a parancsfájlt a következő parancs használatára a parancsfájlban:

        set hive.execution.engine=mr;

1. A Linux-fürtök esetében lehet olyan forgatókönyv, amelyben a szolgáltatások az elsődleges átjárócsomóponthoz futnak, miközben a Resource Manager a másodlagos gépen is fut. Egy ilyen forgatókönyv hibákat eredményezhet (alább látható), ha a Hue használatával megtekinti a fürtön futó feladatok részleteit. Megtekintheti azonban a feladatok részleteit a feladatok befejeződése után.

   ![Hue Portal – hiba minta üzenet](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Hue-portál hibája")

   Ezt egy ismert probléma okozza. Áthidaló megoldásként módosítsa a Ambari, hogy az Active Resource Manager is az elsődleges átjárócsomóponthoz fusson.

1. A Hue megérti a WebHDFS, miközben a HDInsight-fürtök az Azure Storage-t használják `wasbs://`használatával. Így a parancsfájl-művelettel használt egyéni szkript telepíti a WebWasb-t, amely egy WebHDFS-kompatibilis szolgáltatás, amely a WASB-re mutat. Tehát bár a Hue Portal a HDFS (például ha az egérmutatót a **fájlkezelőben**helyezi át), akkor a WASB kell értelmezni.

## <a name="next-steps"></a>Következő lépések

[Telepítse az R-t a HDInsight-fürtökön](hdinsight-hadoop-r-scripts-linux.md). A fürt testreszabásával telepítse az R-t a HDInsight Hadoop-fürtökön. Az R egy nyílt forráskódú nyelv és környezet statisztikai számítástechnikai szolgáltatásokhoz. Több száz beépített statisztikai funkciót és saját programozási nyelvet biztosít, amely ötvözi a funkcionális és az objektumorientált programozás szempontjait. Emellett kiterjedt grafikus képességeket is biztosít.
