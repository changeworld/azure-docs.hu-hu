---
title: További Azure Storage-fiókok hozzáadása a HDInsight-hez
description: Ismerje meg, hogyan adhat hozzá további Azure Storage-fiókokat egy meglévő HDInsight-fürthöz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 87eb04b7323186175195babf6a602fa12d25176f
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206707"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>További Storage-fiókok hozzáadása a HDInsight-hez

Megtudhatja, hogyan használhat parancsfájl-műveleteket további Azure Storage- *fiókok* HDInsight való hozzáadásához. A jelen dokumentum lépései egy meglévő HDInsight-fürthöz adhatnak hozzá Storage- *fiókot* . Ez a cikk a Storage- *fiókokra* vonatkozik (nem az alapértelmezett fürtlemez-fiókra), és nem a további tárterület, például a [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) és a [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> A jelen dokumentumban található információk további Storage-fiók (ok) egy fürthöz való hozzáadását ismertetik a létrehozása után. A Storage-fiókok fürt létrehozása során történő hozzáadásával kapcsolatos információkért lásd: [fürtök beállítása a HDInsight-ben Apache Hadoop, Apache Spark, Apache Kafka és sok más](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Előfeltételek

* Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* A Storage-fiók neve és kulcsa. Lásd: a [Storage-fiók elérési kulcsainak kezelése](../storage/common/storage-account-keys-manage.md).
* Ha a PowerShellt használja, szüksége lesz az az modulra.  Lásd: [Azure PowerShell áttekintése](https://docs.microsoft.com/powershell/azure/overview).

## <a name="how-it-works"></a>Működés

A feldolgozás során a parancsfájl a következő műveleteket hajtja végre:

* Ha a Storage-fiók már létezik a fürt Core-site. XML konfigurációjában, a parancsfájl kilép, és nem hajt végre további műveletet.

* Ellenőrzi, hogy a Storage-fiók létezik-e, és hogy a kulcs használatával érhető-e el.

* Titkosítja a kulcsot a fürt hitelesítő adatainak használatával.

* Hozzáadja a Storage-fiókot a Core-site. xml fájlhoz.

* Leállítja és újraindítja az [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop fonalat](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)és [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) szolgáltatásokat. A szolgáltatások leállítása és elindítása lehetővé teszi, hogy az új Storage-fiókot használják.

> [!WARNING]  
> A HDInsight-fürttől eltérő helyen lévő Storage-fiók használata nem támogatott.

## <a name="add-storage-account"></a>Tárfiók hozzáadása

A következő szempontok alapján alkalmazza a módosításokat a [parancsfájl-művelet](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) használatával:

|Tulajdonság | Érték |
|---|---|
|Bash-parancsfájl URI-ja|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Csomópont típusa (i)|Head|
|Paraméterek|`ACCOUNTNAME` `ACCOUNTKEY` `-p` (nem kötelező)|

* `ACCOUNTNAME` a HDInsight-fürthöz hozzáadandó Storage-fiók neve.
* a `ACCOUNTKEY` a `ACCOUNTNAME`elérési kulcsa.
* A(z) `-p` nem kötelező. Ha meg van adva, a kulcs nincs titkosítva, és a Core-site. xml fájlban tárolja egyszerű szövegként.

## <a name="verification"></a>Ellenőrzés

Ha a HDInsight-fürtöt a Azure Portal tekinti meg, akkor a __Tulajdonságok__ területen a __Storage-fiókok__ bejegyzés nem jeleníti meg a parancsfájl-művelettel hozzáadott tárolási fiókokat. Azure PowerShell és az Azure CLI nem jeleníti meg a további Storage-fiókot sem. A tárolási adatok nem jelennek meg, mert a parancsfájl csak a fürt `core-site.xml` konfigurációját módosítja. Ez az információ nem használatos a fürt adatainak Azure felügyeleti API-k használatával történő beolvasásakor.

A további tárterület ellenőrzéséhez használja az alábbi módszerek egyikét:

### <a name="powershell"></a>PowerShell

A parancsfájl visszaadja az adott fürthöz társított Storage-fiók nevét (ke) t. Cserélje le a `CLUSTERNAME`t a tényleges fürt nevére, majd futtassa a parancsfájlt.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. Egy webböngészőből navigáljon `https://CLUSTERNAME.azurehdinsight.net`, ahol a `CLUSTERNAME` a fürt neve.

1. Navigáljon a **HDFS** > **konfigurációk** > **Advanced** > **Egyéni Core-site**.

1. Figyelje meg, hogy milyen kulcsokkal kezdődik a `fs.azure.account.key`. A fiók neve a kulcs része lesz, ahogy az ebben a példában szereplő képen látható:

   ![ellenőrzés az Apache Ambari](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Storage-fiók eltávolítása

1. Egy webböngészőből navigáljon `https://CLUSTERNAME.azurehdinsight.net`, ahol a `CLUSTERNAME` a fürt neve.

1. Navigáljon a **HDFS** > **konfigurációk** > **Advanced** > **Egyéni Core-site**.

1. Távolítsa el a következő kulcsokat:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

Miután eltávolította ezeket a kulcsokat, és mentette a konfigurációt, újra kell indítania a Oozie, a fonalat, a MapReduce2, a HDFS és a kaptárt eggyel.

## <a name="known-issues"></a>Ismert problémák

### <a name="storage-firewall"></a>Tárolási tűzfal

Ha úgy dönt, hogy védi a Storage-fiókot a **tűzfalakkal és a virtuális hálózatokkal** kapcsolatos korlátozásokkal a **kiválasztott hálózatokon**, ügyeljen arra, hogy a kivételt engedélyezze a **megbízható Microsoft-szolgáltatások számára** ... így a HDInsight hozzáférhet a Storage-fiókhoz.

### <a name="unable-to-access-storage-after-changing-key"></a>Nem lehet hozzáférni a tárolóhoz a kulcs módosítása után

Ha megváltoztatja egy Storage-fiók kulcsát, a HDInsight már nem fér hozzá a Storage-fiókhoz. A HDInsight a kulcs gyorsítótárazott példányát használja a Core-site. xml fájlban a fürthöz. Ezt a gyorsítótárazott másolatot frissíteni kell, hogy az megfeleljen az új kulcsnak.

A parancsfájl-művelet ismételt futtatása __nem__ frissíti a kulcsot, mivel a parancsfájl ellenőrzi, hogy van-e már létező bejegyzés a Storage-fiókhoz. Ha egy bejegyzés már létezik, nem végez módosítást.

A probléma megkerülése:  
1. Távolítsa el a Storage-fiókot.
1. Adja hozzá a Storage-fiókot.

> [!IMPORTANT]  
> A fürthöz csatolt elsődleges Storage-fiók tárolási kulcsának elforgatása nem támogatott.

### <a name="poor-performance"></a>Gyenge teljesítmény

Ha a Storage-fiók a HDInsight-fürttől eltérő régióban található, a teljesítmény gyenge lehet. Az adatok egy másik régióban való elérése a regionális Azure-adatközponton és a nyilvános interneten kívüli hálózati forgalmat küld, amely a késést is képes bevezetni.

### <a name="additional-charges"></a>További díjak

Ha a Storage-fiók a HDInsight-fürttől eltérő régióban található, az Azure-számlázás további kimenő költségeire is figyelmeztetheti. Ha az adatközpont elhagyja a helyi adatközpontot, a kimenő forgalom díját is alkalmazza. Ez a díj akkor is alkalmazható, ha a forgalmat egy másik régióban lévő Azure-adatközpontra szánják.

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan adhat hozzá további Storage-fiókokat egy meglévő HDInsight-fürthöz. További információ a parancsfájl-műveletekről: [Linux-alapú HDInsight-fürtök testreszabása parancsfájl-művelet használatával](hdinsight-hadoop-customize-cluster-linux.md)
