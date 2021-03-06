---
title: Az Azure HDInsight kibocsátási megjegyzései
description: Az Azure HDInsight legújabb kibocsátási megjegyzései. A Hadoop, a Spark, a R Server, a kaptár és sok más fejlesztéssel kapcsolatos tippeket és információkat kaphat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: b83828c3c78913598c103730e11222969fe1fddb
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370168"
---
# <a name="release-notes"></a>Kibocsátási megjegyzések

Ez a cikk az Azure HDInsight **legújabb** kiadási frissítéseivel kapcsolatos információkat tartalmaz. A korábbi kiadásokkal kapcsolatos információkért lásd: [HDInsight kibocsátási megjegyzések archívuma](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Összegzés

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében az Azure-beli nyílt forráskódú elemzésekhez.

## <a name="release-date-01092020"></a>Kiadás dátuma: 01/09/2020

Ez a kiadás a 3,6-es és a 4,0-es HDInsight is érvényes. A HDInsight-kiadás több napon keresztül elérhetővé válik minden régióban. A kiadás dátuma itt jelzi az első régió kiadásának dátumát. Ha nem látja az alábbi módosításokat, várjon, amíg a kiadás több napon belül élő marad a régióban.

> [!IMPORTANT]  
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információ: [HDInsight verziószámozási cikk](hdinsight-component-versioning.md).

## <a name="new-features"></a>Új funkciók
### <a name="tls-12-enforcement"></a>A TLS 1.2 kényszerítése
A Transport Layer Security (TLS) és a SSL (SSL) olyan titkosítási protokollok, amelyek kommunikációs biztonságot biztosítanak a számítógép hálózatán. További információ a [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)-ről. A HDInsight TLS 1,2-t használ a nyilvános HTTPs-végpontokon, de a TLS 1,1 továbbra is támogatott a visszamenőleges kompatibilitás érdekében. 

Ebben a kiadásban az ügyfelek csak a nyilvános fürt végpontján keresztül csatlakozhatnak a TLS 1,2-es kapcsolatokhoz. Ennek támogatásához a **minSupportedTlsVersion** új tulajdonsága be van vezetve, és a fürt létrehozása során megadható. Ha a tulajdonság nincs beállítva, a fürt továbbra is támogatja a TLS 1,0, 1,1 és 1,2 protokollt, amely megegyezik a mai viselkedésével. Az ügyfelek a tulajdonság értékét "1,2" értékre állíthatja, ami azt jelenti, hogy a fürt csak a TLS 1,2-es vagy újabb verzióját támogatja. További információ: [virtuális hálózat megtervezése – Transport Layer Security](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#transport-layer-security).

### <a name="bring-your-own-key-for-disk-encryption"></a>Saját kulcs használata a lemezes titkosításhoz
A HDInsight összes felügyelt lemeze az Azure Storage Service Encryption (SSE) védelemmel van ellátva. A lemezeken tárolt adatforgalom alapértelmezés szerint a Microsoft által felügyelt kulcsokkal van titkosítva. Ettől a kiadástól kezdve a lemezes titkosításhoz Bring Your Own Key (BYOK) és a Azure Key Vault használatával felügyelheti. A BYOK-titkosítás egy egylépéses konfiguráció a fürt létrehozása során, többletköltség nélkül. Csak regisztrálja a HDInsight felügyelt identitásként Azure Key Vault és adja hozzá a titkosítási kulcsot a fürt létrehozásakor. További információ: [ügyfél által felügyelt kulcs lemezének titkosítása](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

## <a name="deprecation"></a>Elavulás
Nincs elavultság ehhez a kiadáshoz. A közelgő elavulás elkezdéséhez tekintse meg a [közelgő változásokat](#upcoming-changes).

## <a name="behavior-changes"></a>Viselkedési változások
Ebben a kiadásban nem változik a viselkedés. A közelgő változásokról a [közelgő változások](#upcoming-changes)című szakaszban olvashat.

## <a name="upcoming-changes"></a>Közelgő változások
A következő módosítások a közelgő kiadásokban fognak történni. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>A fő csomóponthoz legalább 4 magos virtuális gépnek kell tartoznia 
A HDInsight-fürtök magas rendelkezésre állásának és megbízhatóságának biztosítása érdekében legalább 4 magos virtuális gépnek kell lennie a fő csomópont számára. A 2020. április 1-től kezdődően az ügyfelek csak 4 magos vagy magasabb szintű virtuális gépet választhatnak az új HDInsight-fürtök fő csomópontja. A meglévő fürtök továbbra is a várt módon fognak futni. 

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark-fürt csomópontjának méretének változása 
A következő kiadásban az ESP Spark-fürt minimálisan megengedett csomópontjának mérete Standard_D13_V2re változik. Az A sorozatú virtuális gépek az ESP-fürtöket a viszonylag alacsony CPU-és memória-kapacitás miatt okozhatják. Az "a" sorozatú virtuális gépek elavulttá válik az új ESP-fürtök létrehozásához.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Áttérés Azure-beli virtuálisgép-méretezési csoportokra
A HDInsight mostantól Azure-beli virtuális gépeket használ a fürt kiépítéséhez. A következő kiadásban a HDInsight az Azure virtuálisgép-méretezési csoportokat fogja használni helyette. További információ az Azure-beli virtuálisgép-méretezési csoportokról.

### <a name="hbase-20-to-21"></a>HBase 2,0 – 2,1
A közelgő HDInsight 4,0-es kiadásban a HBase verziója a 2,0-es verzióról 2,1-ra lesz frissítve.

## <a name="bug-fixes"></a>Hibajavítások
A HDInsight továbbra is a fürt megbízhatóságának és teljesítményének növelését teszi elérhetővé. 

## <a name="component-version-change"></a>Összetevő verziójának módosítása
Ehhez a kiadáshoz nem módosult az összetevő verziószáma. A HDInsight 4,0 ad HDInsight 3,6 aktuális összetevő-verzióit itt találja.
