---
title: Az Azure Blockchain Service (ABS) figyelése
description: Az Azure Blockchain szolgáltatás monitorozása Azure Monitor
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6f2a91a8ffce67d3c4008a7587f2787f6446c341
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293249"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Az Azure Blockchain szolgáltatás monitorozása Azure Monitor  

Mivel az ügyfelek üzemi szintű blockchain forgatókönyveket futtatnak az Azure Blockchain Service-ben (ABS), kritikus fontosságú lesz a rendelkezésre állási, teljesítmény-és üzemeltetési erőforrások figyelése. Ez a cikk az Azure Blockchain szolgáltatás által létrehozott figyelési információkat ismerteti, valamint azt, hogy miként használhatók a Azure Monitor különböző funkciói és integrációi a termelési környezetek kezeléséhez.  

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?

Az Azure Blockchain szolgáltatás a Azure Monitor használatával hoz létre monitorozási adatait, amely az Azure-ban teljes verem-figyelési szolgáltatás, amely az Azure-erőforrások figyeléséhez biztosít teljes körű szolgáltatásokat. További információ a Azure Monitorről: az [Azure-erőforrások figyelése Azure monitorokkal](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource).
 

Az alábbi részekben az Azure Blockchain szolgáltatásból gyűjtött adatok leírásával, valamint az adatok gyűjtésének konfigurálására és az Azure-eszközökkel történő elemzésére vonatkozó példákat találhat.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Az Azure Blockchain szolgáltatásból gyűjtött adatok figyelése  

Az Azure Blockchain szolgáltatás ugyanolyan típusú figyelési adatokat gyűjt, mint más Azure-erőforrásokat, amelyek az Azure-erőforrások [adatainak monitorozása](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) című témakörben olvashatók. Az Azure Blockchain szolgáltatás által létrehozott naplók és mérőszámok részletes ismertetését lásd: az [Azure Blockchain Service-adatok figyelése](#monitor-azure-blockchain-service-data-reference) .

Az egyes Azure Blockchain-szolgáltatások összes erőforrásának Azure Portal áttekintő lapja a tranzakciók rövid áttekintését tartalmazza, beleértve a kezelt és a feldolgozott blokkokat is. Az adatok némelyikét automatikusan gyűjti a rendszer, és elemzés céljából elérhetővé válik az Azure Blockchain szolgáltatás-tag erőforrásának létrehozása után, míg további beállításokkal is engedélyezheti a további adatgyűjtést.

## <a name="diagnostic-settings"></a>Diagnosztikai beállítások  

A platform metrikáit és a tevékenység naplóját a rendszer automatikusan gyűjti, de diagnosztikai beállítást kell létrehoznia az erőforrás-naplók összegyűjtéséhez vagy a Azure Monitoron kívüli továbbításához. A diagnosztikai beállításoknak a Azure Portal, a CLI vagy a PowerShell használatával történő létrehozásával kapcsolatos részletes folyamatért lásd: [diagnosztikai beállítás létrehozása a platform-naplók és-metrikák összegyűjtéséhez az Azure-ban](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) .

Diagnosztikai beállítás létrehozásakor meg kell adnia, hogy a rendszer milyen típusú naplókat gyűjtsön. Az Azure Blockchain Service kategóriái az alábbiakban láthatók.

**Blockchain** – válassza ki a kategóriát, ha figyelni szeretné a ngnix-proxy naplóit. Az összes ügyfél-tranzakciós adat naplózási és hibakeresési célból elérhető.  

**Blockchain** – válassza ki a kategóriát a felügyelt szolgáltatás által üzemeltetett Blockchain-alkalmazás naplóinak beolvasásához. Például egy ABS-kvórum tag esetében ezek a naplók a Kvórumból származó naplók.  

**Metrikai kérelmek**: Jelölje be a metrikai adatok Azure Cosmos DBból a célhelyekre való gyűjtésének lehetőségét a diagnosztikai beállításban, amelyet az Azure-metrikák automatikusan gyűjtenek. A metrikai adatok összegyűjtése az erőforrás-naplókkal mindkét típusú adat elemzéséhez, valamint a Azure Monitoron kívüli metrikai adatok küldéséhez.

## <a name="analyze-metric-data"></a>Metrikai adatok elemzése  

Az Azure Blockchain Service mérőszámait a metrikák Explorerrel elemezheti, ha az ABS Resource (figyelés) szakaszban a mérőszámok lapra navigál. Az eszköz használatával kapcsolatos részletekért tekintse meg az [Azure Metrikaböngésző használatának első lépéseit](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) ismertető témakört. Az Azure Blockchain szolgáltatás teljes mérőszámai az Azure Blockchain Service standard mérőszámokban találhatók.

A **csomópont** -dimenziót szűrő hozzáadásakor vagy a metrikák felosztásakor is használhatja, ami alapvetően metrikus értékeket biztosít a tranzakciós csomópontok és az ABS tag Érvényesítő csomópontjai számára.

## <a name="analyze-log-data"></a>Naplóadatok elemzése

Íme néhány lekérdezés, amely megadható a log keresési sávban az Azure Blockchain-szolgáltatás tagjainak figyeléséhez. Ezek a lekérdezések használata a [új nyelv](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Az alábbi lekérdezéssel kérdezheti le a hibák feltételeit a Blockchain alkalmazás naplófájljaiban:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

A Blockchain-proxy naplófájljaiban található hibák lekéréséhez használja az alábbi lekérdezést.  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Az Azure-naplókban elérhető időszűrők használatával szűrheti a lekérdezést egy adott időtartományra vonatkozóan.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Az Azure Blockchain szolgáltatás adatreferenciájának figyelése  

Ez a cikk az Azure Blockchain szolgáltatás teljesítményének és rendelkezésre állásának elemzéséhez gyűjtött napló-és metrikai adatokról nyújt referenciát.  

### <a name="resource-logs"></a>Erőforrásnaplók

Minden erőforrás-napló egy legfelső szintű közös sémát használ a blockchain szolgáltatáshoz tartozó néhány egyedi tulajdonsággal. Tekintse meg a cikk [legfelső szintű erőforrás-naplók sémáját](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema), az Azure Blockchain szolgáltatásra jellemző tulajdonságok részleteit alább találja.  

A következő táblázat az Azure Blockchain-proxy naplófájljainak tulajdonságait sorolja fel Azure Monitor-naplókba vagy Azure Storage-ba gyűjtve.  


| Tulajdonság neve  | Leírás |
|:---|:---|
| time | Dátuma és időpontja (UTC), ha a művelet történt. |
| resourceID  | Az Azure Blockchain Service-erőforrás, amely számára engedélyezve vannak a naplók.  |
| category  |Az Azure Blockchain Service esetében a lehetséges értékek a következők: **Proxylogs** és **Applicationlogs**. |
| operationName  | Az esemény által jelzett művelet neve.   |
| Naplózási szint  | Alapértelmezés szerint az Azure Blockchain szolgáltatás lehetővé teszi az **információs** naplózási szintet.   |
| NodeLocation  | Az az Azure-régió, ahol a blockchain-tag telepítve van.  |
| BlockchainNodeName  | Az Azure Blockchain-szolgáltatás azon csomópontjának neve, amelyen a műveletet végzi.   |
| EthMethod  | A metódust, amelyet az alapul szolgáló blockchain protokoll hív meg kvórumként, eth_sendTransactions, eth_getBlockByNumber stb.  |
| Ügynök  | A felhasználó nevében eljáró felhasználói ügynök, például böngésző Mozilla, Edge stb. Az értékek például a következők: "Mozilla/5.0 (Linux x64) Node. js/8.16.0 V8/6.2.414.77"  |
| Kód   | HTTP-hibakódok. A 4XX és a 5XX általában a hibákra vonatkoznak.  |
| NodeHost  | A csomópont DNS-neve.   |
| RequestMethodName | A HTTP-metódus neve, a lehetséges értékek itt a létrehozási tag, a meglévő tag részleteinek beolvasása, törlés a tag törlésére, a frissítés a tagok frissítése érdekében.   |
| BlockchainMemberName  | A felhasználó által megadott Azure Blockchain-szolgáltatási tag neve.  |
| Konzorcium | A konzorcium neve, amelyet a felhasználó adott meg.   |
| távoli  | Annak az ügyfélnek az IP-címe, amelyen a kérés érkezik.  |
| RequestSize  | A kérelem mérete bájtban megadva.  |
| RequestTime  | A kérelem időtartama ezredmásodpercben.|




Az alábbi táblázat az Azure Blockchain-alkalmazások naplóihoz tartozó tulajdonságokat sorolja fel.


| Tulajdonság neve  | Leírás |
|:---|:---|
| time | Dátuma és időpontja (UTC), ha a művelet történt. |
| resourceID  | Az Azure Blockchain Service-erőforrás, amely számára engedélyezve vannak a naplók.|
| category  |Az Azure Blockchain Service esetében a lehetséges érték a **Proxylogs** és a **Applicationlogs**.  |
| operationName  | Az esemény által jelzett művelet neve.   |
| Naplózási szint  | Alapértelmezés szerint az Azure Blockchain szolgáltatás lehetővé teszi az **információs** naplózási szintet.   |
| NodeLocation  | Az az Azure-régió, ahol a blockchain-tag telepítve van.  |
| BlockchainNodeName  | Az Azure Blockchain-szolgáltatás azon csomópontjának neve, amelyen a műveletet végzi.   |
| BlockchainMessage    | Ez a mező tartalmazza a Blockchain, amely az adategyszerű naplók. Az ABS-kvórum esetében ez kvórum naplókat tartalmaz. Információkkal szolgál arról, hogy milyen típusú naplóbejegyzés van a tájékoztatás, a hiba, a figyelmeztetés és egy olyan karakterlánc, amely további információkat nyújt a végrehajtott műveletről.   |
| TenantID    | Az Azure Blockchain szolgáltatás régió-specifikus bérlője. A mező formátuma https://westlake-rp-prod.<region>. cloudapp.azure.com, ahol a régió a központilag telepített tag Azure-régióját adja meg.       |
| SourceSystem   | A rendszer feltölti a naplókat, ebben az esetben ez az **Azure**.    |



### <a name="metrics"></a>Mérőszámok

Az alábbi táblázatok az Azure Blockchain szolgáltatáshoz gyűjtött platform-mérőszámokat ismertetik. Az összes mérőszámot a névtér **Azure Blockchain szolgáltatásának** standard mérőszámai tárolják.

Az összes Azure Monitor támogatott mérőszám (beleértve az Azure Blockchain Service) listáját az [Azure monitor támogatott metrikák](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)című részben tekintheti meg.

### <a name="blockchain-metrics"></a>Blockchain metrikák

A következő táblázat az Azure Blockchain-szolgáltatás Blockchain-erőforrásához összegyűjtött mérőszámok listáját tartalmazza.


| metrika neve | Unit (Egység)  |  Összesítés típusa| Leírás   |
|---|---|---|---|
| Függőben lévő tranzakciók   | Count  |  Average | A bányászra váró tranzakciók száma.   |
| Feldolgozott blokkok   | Count  | Összeg  |  Az egyes időintervallumokban feldolgozott blokkok száma. Jelenleg a blokk mérete 5 másodperc, ezért egy percen belül minden egyes csomópont 5 perc alatt feldolgozza 12 blokkot és 60 blokkot.   |
|Feldolgozott tranzakciók    | Count  | Összeg  | Egy blokkban feldolgozott tranzakciók száma.    |
|Várólistán lévő tranzakciók    |  Count | Average  | Azon tranzakciók száma, amelyeket nem lehet azonnal kibányászni. Ennek oka az lehet, hogy a megrendelésük megérkezett, és a jövőben az előző tranzakció megérkezésére vár. Vagy két tranzakció is lehet ugyanazzal a számmal (egyszer használatos), és ugyanaz a gáz érték, ezért a másodikat nem lehet kibányászni.   |

### <a name="connection-metrics"></a>Kapcsolati metrika  

A következő táblázat felsorolja az Azure Blockchain-szolgáltatási tag erőforrásához összegyűjtött különböző kapcsolatok mérőszámait. Ezek az NGINX proxy metrikái.


| metrika neve | Unit (Egység)  |  Összesítés típusa| Leírás |
|---|---|---|---|
| Elfogadott kapcsolatok   | Count  |  Összeg | Az elfogadott ügyfélkapcsolatok teljes száma.   |
| Aktív kapcsolatok  | Count  | Average  |  Az aktív ügyfélkapcsolatok aktuális száma, beleértve a várakozási kapcsolatokat.    |
|Kezelt kapcsolatok    | Count  | Összeg  | A kezelt kapcsolatok teljes száma. Általában a paraméter értéke megegyezik az elfogadott kapcsolatok értékével, kivéve, ha egyes erőforrás-korlátokat nem sikerült elérni.     |
|Kezelt kérelmek     |  Count | Összeg  | Az ügyfelek kéréseinek teljes száma.  |


### <a name="performance-metrics"></a>Teljesítmény-metrikák

A következő táblázat felsorolja az Azure Blockchain-tag erőforrásának egyes csomópontjain összegyűjtött teljesítménymutatókat.  


| metrika neve | Unit (Egység)  |  Összesítés típusa| Leírás   |
|---|---|---|---|
| CPU-használat százaléka   | Százalék  |  Max | A CPU-használat százalékos aránya.     |
| IO olvasási bájtok   | Kilobájtban   | Összeg  |  Az IO olvasási bájtjainak összege az blockchain-tag erőforrásának összes csomópontján.      |
|IO írási bájtjai     | Kilobájtban   | Összeg  | Az i/o-érték bájtban kifejezett összege a blockchain-tag erőforrás összes csomópontján.     |
|Memória korlátja       |  Gigabájt   | Average    | A blockchain folyamat számára elérhető maximális memória a csomóponton. |
|Memóriahasználat     | Gigabájt  |  Average | Az összes csomópont átlagosan felhasznált memória mennyisége.  |
| Memóriahasználat százaléka     | Százalék   | Average  |  Az összes csomópontban átlagosan felhasznált memória százalékos aránya.       |
|Tárterület-használat      | Gigabájt   | Average  | Az összes csomóponton átlagosan használt GB tárterület.       |


## <a name="next-steps"></a>További lépések

További információ a [Blockchain Data Manager](https://docs.microsoft.com/azure/blockchain/service/data-manager) a Blockchain-információk Azure Event Gridba való rögzítéséhez és átalakításához.
