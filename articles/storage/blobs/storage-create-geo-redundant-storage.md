---
title: Oktatóanyag – kiválóan elérhető alkalmazás létrehozása blob Storage-val
titleSuffix: Azure Storage
description: Az olvasási hozzáférésű geo-redundáns tárolással elérhetővé teheti az alkalmazás adatait.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: 0eabd918b5f8f52049792ceb28ef8055945d6475
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162174"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Oktatóanyag: kiválóan elérhető alkalmazás létrehozása blob Storage-val

Ez az oktatóanyag egy sorozat első része. Itt megtudhatja, hogyan teheti elérhetővé az alkalmazás adatait az Azure-ban.

Az oktatóanyag elvégzése után egy olyan konzol-alkalmazás lesz, amely feltölti és lekéri egy blobot egy [olvasási hozzáférésű, Geo-redundáns](../common/storage-redundancy.md) (ra-GRS) Storage-fiókból.

Az RA-GRS úgy működik, hogy az elsődleges régióból egy másodlagos régióba replikálja a tranzakciókat. A replikációs folyamat garantálja a másodlagos régió adatainak végső konzisztenciáját. Az alkalmazás az [áramkör-megszakító](/azure/architecture/patterns/circuit-breaker) mintája alapján határozza meg, hogy melyik végponthoz kell csatlakoznia, a végpontok közötti automatikus váltás a hibák és a helyreállítások szimulálása.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Tárfiók létrehozása
> * A kapcsolati sztring beállítása
> * A konzolalkalmazás futtatása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

* Telepítse a [Visual Studio 2019](https://www.visualstudio.com/downloads/) -et az **Azure-fejlesztési** számítási feladattal.

  ![Azure-fejlesztés (a Web és felhőszolgáltatások alatt)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="pythontabpython"></a>[Python](#tab/python)

* Telepítse a [Pythont](https://www.python.org/downloads/).
* A [Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python) letöltése és telepítése

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

* Telepítse a [Node. js](https://nodejs.org)-t.

---

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A Storage-fiók egy egyedi névteret biztosít az Azure Storage-adatobjektumok tárolásához és eléréséhez.

Kövesse az alábbi lépéseket egy írásvédett georedundáns tárfiók létrehozásához:

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Válassza a **tárterület** lehetőséget az **új** lapon.
3. Válassza ki a **Storage-fiók elemet – blob, fájl, tábla, üzenetsor** a **Kiemelt**területen.
4. Töltse ki a tárfiók űrlapját a következő adatokkal az alábbi képen látható módon, és kattintson a **Létrehozás** elemre:

   | Beállítás       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Name (Név)** | mystorageaccount | A tárfiók egyedi neve |
   | **Üzemi modell** | Resource Manager  | A Resource Manager a legújabb funkciókat kínálja.|
   | **Fióktípus** | StorageV2 | A fiókok típusaival kapcsolatos információkért lásd [a tárfiókok típusait](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Teljesítmény** | Standard | A példaforgatókönyvhöz a standard teljesítmény elegendő. |
   | **Replikáció**| Írásvédett georedundáns tárolás (RA-GRS) | Ez szükséges a minta működéséhez. |
   |**Előfizetés** | az Ön előfizetése |Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.azure.com/Subscriptions) ismertető cikket. |
   |**ResourceGroup** | myResourceGroup |Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
   |**Hely** | USA keleti régiója | Válassza ki a helyet. |

![tárfiók létrehozása](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>A minta letöltése

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[Töltse le a mintaprojektet](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip), és bontsa ki (csomagolja ki) a storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip fájlt. A [git](https://git-scm.com/) használatával is letöltheti az alkalmazás egy másolatát a fejlesztői környezetbe. A mintaprojekt tartalmaz egy konzolalkalmazást.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="pythontabpython"></a>[Python](#tab/python)

[Töltse le a mintaprojektet](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip), és bontsa ki (csomagolja ki) a storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip fájlt. A [git](https://git-scm.com/) használatával is letöltheti az alkalmazás egy másolatát a fejlesztői környezetbe. A mintaprojekt tartalmaz egy egyszerű Python-alkalmazást.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

[Töltse le a minta projektet](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs) , és bontsa ki a fájlt. A [git](https://git-scm.com/) használatával is letöltheti az alkalmazás egy másolatát a fejlesztői környezetbe. A minta projekt egy alapszintű Node. js-alkalmazást tartalmaz.

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>A minta konfigurálása

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Az alkalmazásban meg kell adnia a tárfiókjához tartozó kapcsolati sztringet. Ezt a kapcsolati sztringet tárolhatja egy környezeti változóban az alkalmazást futtató helyi gépen. A környezeti változó létrehozásához kövesse az alábbi példák egyikét az operációs rendszerének megfelelően.

Az Azure Portalon lépjen a tárfiókra. Válassza a **Hozzáférési kulcsok** lehetőséget a tárfiók **Beállítások** területén. Másolja ki az elsődleges vagy a másodlagos kulcs **kapcsolati sztringjét**. Futtassa a következő parancsok egyikét az operációs rendszer alapján, és cserélje le \<yourconnectionstring kifejezést\> a tényleges kapcsolatban álló karakterlánccal. A parancs egy környezeti változót ment a helyi számítógépen. A Windows rendszerben a környezeti változó nem érhető el, amíg újra nem tölti be a használni kívánt **parancssort** vagy rendszerhéjat.

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Az alkalmazásban meg kell adnia a Storage-fiók hitelesítő adatait. Ezeket az információkat az alkalmazást futtató helyi gépen található környezeti változókban tárolhatja. A környezeti változók létrehozásához kövesse az alábbi példák egyikét az operációs rendszertől függően.

Az Azure Portalon lépjen a tárfiókra. Válassza a **Hozzáférési kulcsok** lehetőséget a tárfiók **Beállítások** területén. Illessze be a **Storage-fiók nevét** és a **kulcs** értékeit a következő parancsokra, és cserélje le a \<youraccountname\> és \<youraccountkey\> helyőrzőket. Ez a parancs menti a környezeti változókat a helyi gépre. A Windows rendszerben a környezeti változó nem érhető el, amíg újra nem tölti be a használni kívánt **parancssort** vagy rendszerhéjat.

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

A minta futtatásához hozzá kell adnia a Storage-fiók hitelesítő adatait a `.env.example` fájlhoz, majd át kell neveznie a `.env`ra.

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

Ezeket az információkat a Azure Portal megkeresheti a Storage-fiókjához való navigálással, és a **Beállítások** szakaszban található **hozzáférési kulcsok lehetőség** kiválasztásával.

Telepítse a szükséges függőségeket. Ehhez nyisson meg egy parancssort, lépjen a minta mappájába, majd írja be a `npm install`.

---

## <a name="run-the-console-application"></a>A konzolalkalmazás futtatása

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

A Visual Studióban nyomja le az **F5** billentyűt, vagy kattintson a **Start** gombra az alkalmazás hibakeresésének megkezdéséhez. A Visual Studio automatikusan visszaállítja a hiányzó NuGet-csomagokat, ha vannak konfigurálva. További információért látogasson el a [csomagok telepítése és újratelepítése csomag-visszaállítással](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) című témakörre.

Megnyílik a konzolablak, és az alkalmazás futni kezd. Az alkalmazás feltölti a **HelloWorld.png** képet a megoldásból a tárfiókra. Az alkalmazás ellenőrzi, hogy a kép replikálása valóban megtörtént-e a másodlagos RA-GRS-végpontra. Ezután elkezdi letölteni a képet legfeljebb 999 alkalommal. Minden olvasást egy **P** vagy egy **S**jelöl. Ahol a **P** az elsődleges végpontot és az **S-t** jelöli, a másodlagos végpontot.

![Futó konzolalkalmazás](media/storage-create-geo-redundant-storage/figure3.png)

A mintakód a `RunCircuitBreakerAsync` fájlban található `Program.cs` művelettel letölt egy képet a tárfiókból a [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) metódus segítségével. A letöltés előtt meg kell határozni egy [OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext) környezetet. A műveleti környezet határozza meg az eseménykezelőket, amelyek a letöltés sikeres befejezésekor vagy a sikertelen letöltés utáni újrapróbálkozásokkal aktiválódnak.

# <a name="pythontabpython"></a>[Python](#tab/python)

Az alkalmazás terminálon vagy parancssorban való futtatásához lépjen a **circuitbreaker.py** könyvtárra, majd írja be a `python circuitbreaker.py` parancsot. Az alkalmazás feltölti a **HelloWorld.png** képet a megoldásból a tárfiókra. Az alkalmazás ellenőrzi, hogy a kép replikálása valóban megtörtént-e a másodlagos RA-GRS-végpontra. Ezután elkezdi letölteni a képet legfeljebb 999 alkalommal. Minden olvasást egy **P** vagy egy **S**jelöl. Ahol a **P** az elsődleges végpontot és az **S-t** jelöli, a másodlagos végpontot.

![Futó konzolalkalmazás](media/storage-create-geo-redundant-storage/figure3.png)

A mintakód a `run_circuit_breaker` fájlban található `circuitbreaker.py` metódussal letölt egy képet a tárfiókból a [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html) metódus segítségével.

A Storage-objektum újrapróbálkozási függvénye lineáris újrapróbálkozási szabályzatra van beállítva. Az újrapróbálkozási függvény határozza meg, hogy egy kérelmet újra kell-e próbálni, valamint megadja, hogy hány másodpercnyi várakozás után történjen az újrapróbálkozás. A **retry\_to\_secondary** paramétert állítsa true (igaz) értékre, ha a kérelmet a másodlagos végponton kell újra megkísérelni, amennyiben az elsődleges végpontra irányuló első kérelem sikertelen lenne. A mintaalkalmazásban az egyéni újrapróbálkozási szabályzat a Storage-objektum `retry_callback` függvényében van definiálva.

A letöltés előtt a szolgáltatás objektum [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) és [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) függvény van definiálva. Ezek a függvények határozzák meg az eseménykezelőket, amelyek a letöltés sikeres befejezésekor vagy a sikertelen letöltés utáni újrapróbálkozásokkal aktiválódnak.

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

A minta futtatásához nyisson meg egy parancssort, lépjen a minta mappájába, majd írja be a `node index.js`.

A minta létrehoz egy tárolót a blob Storage-fiókban, feltölti a **HelloWorld. png** -t a tárolóba, majd ismételten ellenőrzi, hogy a tároló és a rendszerkép replikálva lett-e a másodlagos régióba. A replikálást követően a rendszer felszólítja, hogy a letöltéshez vagy a kilépéshez adja meg a **D** vagy a **Q** értéket (majd írja be). A kimenetnek az alábbi példához hasonlóan kell kinéznie:

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

### <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Újrapróbálkozási eseménykezelő

A rendszer akkor hívja meg az `OperationContextRetrying` eseménykezelőt, ha a kép letöltése meghiúsult, és újrapróbálkozásra van beállítva. Ha a rendszer elérte a próbálkozások alkalmazásban definiált maximális számát, a kérelem [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) paramétere `SecondaryOnly` értékre változik. Ez a beállítás kényszeríti az alkalmazást, hogy a másodlagos végpontról kísérelje meg letölteni a képet. Ezzel a konfigurációval csökkenthető a kép lekérési ideje, mivel a rendszer nem próbálja vég nélkül elérni az elsődleges végpontot.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Befejezett kérelem eseménykezelő

A rendszer akkor hívja meg az `OperationContextRequestCompleted` eseménykezelőt, ha a kép letöltése sikerült. Ha az alkalmazás a másodlagos végpontot használja, továbbra is ezt a végpontot használja majd legfeljebb 20 alkalommal. A 20. alkalom után az alkalmazás visszaállítja a [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) paramétert `PrimaryThenSecondary` értékre, és az elsődleges végponton próbálkozik újra. Ha a kérelem sikeres, az alkalmazás továbbra is az elsődleges végpontról végzi a beolvasást.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

### <a name="pythontabpython"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>Újrapróbálkozási eseménykezelő

A rendszer akkor hívja meg az `retry_callback` eseménykezelőt, ha a kép letöltése meghiúsult, és újrapróbálkozásra van beállítva. Ha a rendszer elérte a próbálkozások alkalmazásban definiált maximális számát, a kérelem [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) paramétere `SECONDARY` értékre változik. Ez a beállítás kényszeríti az alkalmazást, hogy a másodlagos végpontról kísérelje meg letölteni a képet. Ezzel a konfigurációval csökkenthető a kép lekérési ideje, mivel a rendszer nem próbálja vég nélkül elérni az elsődleges végpontot.

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Befejezett kérelem eseménykezelő

A rendszer akkor hívja meg az `response_callback` eseménykezelőt, ha a kép letöltése sikerült. Ha az alkalmazás a másodlagos végpontot használja, továbbra is ezt a végpontot használja majd legfeljebb 20 alkalommal. A 20. alkalom után az alkalmazás visszaállítja a [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) paramétert `PRIMARY` értékre, és az elsődleges végponton próbálkozik újra. Ha a kérelem sikeres, az alkalmazás továbbra is az elsődleges végpontról végzi a beolvasást.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

### <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

A Node. js v10 SDK-val a visszahívási kezelők nem szükségesek. A minta Ehelyett egy újrapróbálkozási lehetőségekkel és egy másodlagos végponttal konfigurált folyamatot hoz létre. Ez lehetővé teszi, hogy az alkalmazás automatikusan átváltson a másodlagos folyamatra, ha nem sikerül elérnie az adatait az elsődleges folyamaton keresztül.

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>Következő lépések

A sorozat első részében megtanulta, hogyan lehet az alkalmazást az RA-GRS Storage-fiókokkal nagykörben elérhetővé teszi.

Folytassa a sorozat második részével, ha szeretné megismerni, hogyan szimulálhat hibákat és kényszerítheti az alkalmazást, hogy a másodlagos RA-GRS-végpontot használja.

> [!div class="nextstepaction"]
> [Az elsődleges régióból beolvasott hiba szimulálása](storage-simulate-failure-ragrs-account-app.md)
