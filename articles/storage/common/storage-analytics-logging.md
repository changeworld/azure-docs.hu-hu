---
title: Azure Storage Analytics naplózás
description: Ismerje meg, hogyan naplózhatja az Azure Storage szolgáltatással kapcsolatos kérések részleteit.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 25c047dc9b2ce08ca39e69c6f106e41c5d9bd0dc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268417"
---
# <a name="azure-storage-analytics-logging"></a>Az Azure Storage Analytics naplózása

A Storage Analytics naplózza a tárolási szolgáltatásoknak elküldött sikeres és sikertelen kérelmek részletes információit. Ezekkel az információkkal monitorozhatók az egyes kérelmek és diagnosztizálhatók a tárolási szolgáltatások problémái. A kéréseket a rendszer a lehető legjobb módon naplózza.

 A Storage Analytics-naplózás alapértelmezés szerint nincs engedélyezve a Storage-fiókjához. Engedélyezheti a [Azure Portal](https://portal.azure.com/); További információ: [Storage-fiók figyelése a Azure Portalban](/azure/storage/storage-monitor-storage-account). Storage Analytics programozott módon is engedélyezheti a REST API vagy az ügyféloldali kódtár használatával. A [blob szolgáltatás](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)tulajdonságainak beolvasása, a [várólista-szolgáltatás tulajdonságainak](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)beolvasása és a [Table Service tulajdonságok beolvasása](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) műveletekkel engedélyezheti az egyes szolgáltatások Storage Analyticsét.

 A naplóbejegyzések csak akkor jönnek létre, ha a szolgáltatás-végpontra irányuló kérések történnek. Ha például egy Storage-fiók egy tevékenységgel rendelkezik a blob-végponton, de nem a táblázat-vagy várólista-végpontokon, akkor csak a Blob servicehoz tartozó naplók lesznek létrehozva.

> [!NOTE]
>  A Storage Analytics-naplózás jelenleg csak a Blob, a Queue és a Table szolgáltatás esetében érhető el. A prémium szintű tárfiókok azonban nem támogatottak.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="requests-logged-in-logging"></a>Naplózásban bejelentkezett kérelmek
### <a name="logging-authenticated-requests"></a>Hitelesített kérelmek naplózása

 A rendszer naplózza a következő típusú hitelesített kérelmeket:

- Sikeres kérések
- Sikertelen kérések, beleértve az időtúllépést, a szabályozást, a hálózatot, az engedélyezést és az egyéb hibákat
- Közös hozzáférésű aláírást (SAS) vagy OAuth (például sikertelen és sikeres kérelmeket) használó kérelmek
- Elemzési adatkérések

  A Storage Analytics saját maga által kezdeményezett kérelmeket (például a naplózás létrehozását vagy törlését) nem naplózza a rendszer. A naplózott adatok teljes listáját az [Storage Analytics naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) , valamint a [Storage Analytics naplózási formátum](/rest/api/storageservices/storage-analytics-log-format) témakörök ismertetik.

### <a name="logging-anonymous-requests"></a>Névtelen kérelmek naplózása

 A rendszer a következő típusú névtelen kérelmeket naplózza:

- Sikeres kérések
- Kiszolgálói hibák
- Időtúllépési hibák az ügyfél és a kiszolgáló esetében
- Sikertelen GET kérelmek a 304 hibakódmal (nincs módosítva)

  Az összes többi sikertelen névtelen kérelmet nem naplózza a rendszer. A naplózott adatok teljes listáját az [Storage Analytics naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) , valamint a [Storage Analytics naplózási formátum](/rest/api/storageservices/storage-analytics-log-format) témakörök ismertetik.

## <a name="how-logs-are-stored"></a>A naplók tárolása

A rendszer az összes naplót blokkoló blobokban tárolja `$logs`nevű tárolóban, amely automatikusan létrejön, amikor a Storage Analytics engedélyezve van egy Storage-fiókhoz. A `$logs` tároló a Storage-fiók blob-névterében található, például: `http://<accountname>.blob.core.windows.net/$logs`. Ez a tároló nem törölhető, ha Storage Analytics engedélyezve lett, de a tartalma törölhető. Ha a Storage-Browsing eszköz használatával közvetlenül a tárolóra navigál, a naplózási adatait tartalmazó Blobok jelennek meg.

> [!NOTE]
>  A `$logs` tároló nem jelenik meg, ha egy tároló-listaelemet hajt végre, például a tárolók listázása műveletet. Közvetlenül elérhetőnek kell lennie. Használhatja például a Blobok listázása műveletet a `$logs` tárolóban lévő Blobok eléréséhez.

Mivel a rendszer naplózza a kérelmeket, Storage Analytics a köztes eredményeket blokkként tölti fel. A Storage Analytics rendszeresen véglegesíti ezeket a blokkokat, és blobként elérhetővé teszi őket. Akár egy óráig is eltarthat, amíg a naplófájlok megjelennek a **$logs** tárolóban lévő blobokban, mert a tárolási szolgáltatás által használt gyakoriság a napló-írókat üríti. Ismétlődő rekordok létezhetnek ugyanabban az órában létrehozott naplókhoz. A **kérelemazonosító** és a **művelet** számának ellenőrzésével megállapíthatja, hogy egy rekord duplikált-e.

Ha nagy mennyiségű naplózási adata van, és minden órában több fájl található, akkor a blob metaadataival meghatározhatja, hogy a napló mely adatokat tartalmazza a blob metaadatainak mezőinek vizsgálatával. Ez azért is hasznos, mert időnként előfordulhat, hogy az adatok a naplófájlokban vannak írva: a blob metaadatai a blob nevénél pontosabban jelzik a blob tartalmát.

A legtöbb tárolási böngészési eszköz lehetővé teszi a Blobok metaadatainak megtekintését; ezeket az információkat a PowerShell vagy programozott módon is elolvashatja. A következő PowerShell-kódrészlet egy példa a naplók listájának szűrésére a név alapján egy idő megadásához, valamint a metaadatok alapján, hogy csak az **írási** műveleteket tartalmazó naplókat azonosítsák.  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

A Blobok programozott listázásával kapcsolatos információkért lásd: [blob-erőforrások enumerálása](https://msdn.microsoft.com/library/azure/hh452233.aspx) , [Tulajdonságok és metaadatok beállítása és lekérése blob-erőforrásokhoz](https://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Napló elnevezési konvenciói

 Minden napló a következő formátumban lesz megírva:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 A következő táblázat a napló neve egyes attribútumait ismerteti:

|Attribútum|Leírás|
|---------------|-----------------|
|`<service-name>`|A tárolási szolgáltatás neve. Például: `blob`, `table`vagy `queue`|
|`YYYY`|A naplófájl négyjegyű éve. Például:`2011`|
|`MM`|A napló két számjegyű hónapja. Például:`07`|
|`DD`|A napló két számjegyből álló napja. Például:`31`|
|`hh`|A két számjegyű óra, amely a naplók kezdő óráját jelzi 24 órás UTC formátumban. Például:`18`|
|`mm`|A naplók kezdő percét jelző kétszámjegyű szám. **Megjegyzés:**  Ez az érték nem támogatott a Storage Analytics aktuális verziójában, és az értéke mindig `00`lesz.|
|`<counter>`|Egy nulla-alapú számláló hat számjegytel, amely a tárolási szolgáltatáshoz egy órás időszakban generált naplók számát jelzi. Ez a számláló a következő időpontban indul: `000000`. Például:`000001`|

 A következő egy teljes minta-napló neve, amely a fenti példákat ötvözi:

 `blob/2011/07/31/1800/000001.log`

 A következő példa egy URI-t használ a fenti napló eléréséhez:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Tárolási kérelem naplózásakor az eredményül kapott napló neve a kért művelet befejezésének órájával összefügg. Ha például egy GetBlob-kérelem a 7/31/2011-es 6-os időpontban fejeződött be, a naplót a következő előtaggal kell megírni: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Naplózási metaadatok

 Minden naplózási blobot metaadatok tárolnak, amelyek segítségével azonosíthatók a blob által tartalmazott naplózási adatok. Az alábbi táblázat az egyes metaadatok attribútumait ismerteti:

|Attribútum|Leírás|
|---------------|-----------------|
|`LogType`|Leírja, hogy a napló tartalmazza-e az olvasási, írási és törlési műveletekkel kapcsolatos információkat. Ez az érték egy típust vagy mindhárom kombinációt tartalmazhat, vesszővel elválasztva.<br /><br /> 1\. példa: `write`<br /><br /> 2\. példa: `read,write`<br /><br /> 3\. példa: `read,write,delete`|
|`StartTime`|A napló bejegyzéseinek legkorábbi időpontja `YYYY-MM-DDThh:mm:ssZ`formában. Például:`2011-07-31T18:21:46Z`|
|`EndTime`|A napló bejegyzéseinek legkésőbbi időpontja `YYYY-MM-DDThh:mm:ssZ`formájában. Például:`2011-07-31T18:22:09Z`|
|`LogVersion`|A naplózási formátum verziója.|

 A következő lista a fenti példák alapján mutatja be a teljes minta metaadatokat:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Tárolók naplózásának engedélyezése

Azure Portal, PowerShell és Storage SDK-k használatával engedélyezheti a tárolók naplózását.

### <a name="enable-storage-logging-using-the-azure-portal"></a>A tárolók naplózásának engedélyezése a Azure Portal használatával  

A Azure Portal használja a **diagnosztikai beállítások (klasszikus)** panelt a tárolási naplózás vezérléséhez, amely a Storage-fiók **menü**paneljének **figyelés (klasszikus)** szakaszában érhető el.

Megadhatja a naplózni kívánt tárolási szolgáltatásokat, valamint a naplózott adatok megőrzési időtartamát (napokban).  

### <a name="enable-storage-logging-using-powershell"></a>Tárolók naplózásának engedélyezése a PowerShell használatával  

 A helyi gépen a PowerShell használatával konfigurálhatja a Storage-fiók tárolási naplózását, ha a **Get-AzureStorageServiceLoggingProperty** Azure PowerShell parancsmaggal szeretné lekérni az aktuális beállításokat, valamint a **set-AzureStorageServiceLoggingProperty** parancsmagot az aktuális beállítások módosításához.  

 A tárolási naplózást vezérlő parancsmagok olyan **LoggingOperations** paramétert használnak, amely a naplózandó kérelmek vesszővel tagolt listáját tartalmazó karakterlánc. A három lehetséges kérelem típusa: **olvasás**, **írás**és **Törlés**. A naplózás kikapcsolásához használja a **none** értéket a **LoggingOperations** paraméternél.  

 A következő parancs bekapcsolja az olvasási, írási és törlési kérelmek naplózását az alapértelmezett Storage-fiókban lévő Queue szolgáltatásban, a megőrzési érték pedig öt napig:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 A következő parancs kikapcsolja az alapértelmezett Storage-fiókban lévő Table szolgáltatás naplózását:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Az Azure PowerShell-parancsmagoknak az Azure-előfizetéssel való használatához való konfigurálásával és a használni kívánt alapértelmezett Storage-fiók kiválasztásával kapcsolatos információkért lásd: [Azure PowerShell telepítése és konfigurálása](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>A tárolási naplózás engedélyezése programozott módon  

 Amellett, hogy a Azure Portal vagy a Azure PowerShell parancsmagokat használja a tárolási naplózás vezérlésére, használhatja az egyik Azure Storage API-t is. Ha például .NET nyelvet használ, használhatja a Storage ügyféloldali kódtárat.  

 A **CloudBlobClient**, a **CloudQueueClient**és a **CloudTableClient** osztályok olyan metódusokkal rendelkeznek, mint például a **SetServiceProperties** és a **SetServicePropertiesAsync** , amelyek paraméterként egy **ServiceProperties** objektumot készítenek. A **ServiceProperties** objektum használatával konfigurálhatja a tárolási naplózást. Az alábbi C# kódrészlet például azt mutatja be, hogyan lehet módosítani a naplózási naplót és a várólista-naplózás megőrzési időtartamát:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 További információ a tárolási naplózás konfigurálásához használható .NET-nyelvről: a [Storage ügyféloldali kódtár referenciája](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 A tárolási naplózásnak a REST API használatával történő konfigurálásával kapcsolatos általános információkért lásd: [Storage Analytics engedélyezése és konfigurálása](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Storage-naplózási napló adatai letöltése

 A naplófájlok megtekintéséhez és elemzéséhez le kell töltenie azokat a blobokat, amelyek tartalmazzák a helyi számítógép számára érdeklik a napló adatait. Számos Storage-Browsing eszköz lehetővé teszi a Blobok letöltését a Storage-fiókból; a naplófájlok letöltéséhez használhatja az Azure Storage csapata által biztosított parancssori Azure Copy Tool [AzCopy](storage-use-azcopy-v10.md) is.  
 
>[!NOTE]
> A `$logs` tároló nincs integrálva a Event Gridba, így nem fog értesítést kapni a naplófájlok írásakor. 

 Győződjön meg arról, hogy letölti a kívánt naplózási adatnaplót, és hogy ne töltsön le ugyanazokat a naplókat többször:  

-   A naplózási adatokkal rendelkező Blobok dátum-és időelnevezési konvenciójának használatával nyomon követheti, hogy mely Blobok vannak már letöltve az elemzéshez, így elkerülhető, hogy az adatok többször is újra le legyenek töltve.  

-   A naplózási adatokat tartalmazó Blobok metaadatait használva azonosíthatja azt a megadott időszakot, ameddig a blob a naplófájlokban tárolja a letölteni kívánt blob azonosításához szükséges adatokat.  

A AzCopy megkezdéséhez tekintse meg a [AzCopy első lépéseivel](storage-use-azcopy-v10.md) foglalkozó témakört. 

Az alábbi példa bemutatja, hogyan töltheti le a üzenetsor-szolgáltatás naplófájljait a 09., 10:00 és 11. május 20-án, 2014-kor kezdődő órákig.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Ha többet szeretne megtudni az egyes fájlok letöltéséről, tekintse meg az [adott fájlok letöltése](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files)című témakört.

Ha letöltötte a napló adatait, megtekintheti a bejegyzéseket a fájlokban. Ezek a naplófájlok olyan tagolt szöveges formátumot használnak, amelyet számos napló-olvasási eszköz képes elemezni, beleértve a Microsoft Message Analyzert (további információért lásd: útmutató a [figyeléshez, a diagnosztizáláshoz és a hibaelhárításhoz Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). A különböző eszközök különböző lehetőségekkel rendelkeznek a naplófájlok tartalmának formázásához, szűréséhez, rendezéséhez, valamint az ad-kereséshez. További információ a tárolási naplózási naplófájl formátumáról és tartalmáról: [Storage Analytics naplózási formátum](/rest/api/storageservices/storage-analytics-log-format) és [Storage Analytics naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Következő lépések

* [Storage Analytics naplózási formátum](/rest/api/storageservices/storage-analytics-log-format)
* [Naplózott műveletek és állapotüzenetek Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Storage Analytics metrikák (klasszikus)](storage-analytics-metrics.md)
