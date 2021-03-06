---
title: Telemetria folyamatos exportálása a Application Insightsból | Microsoft Docs
description: A diagnosztikai és használati adatok exportálása a Microsoft Azure tárolóba, és onnan tölthető le.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 33158919980514b70c3b0e438691427a34eed834
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663913"
---
# <a name="export-telemetry-from-application-insights"></a>Telemetria exportálása a Application Insightsból
Szeretné megőrizni a telemetria a normál megőrzési időtartamnál hosszabb ideig? Vagy dolgozza fel valamilyen speciális módon? A folyamatos exportálás ideális ehhez. A Application Insights-portálon megjelenített események JSON formátumban exportálhatók Microsoft Azureba. Innen letöltheti az adatait, és bármilyen kódot írhat, amelyet fel kell dolgoznia.  

A folyamatos exportálás beállítása előtt bizonyos alternatívákat érdemes figyelembe venni:

* A metrikák vagy a Keresés lap tetején található exportálás gomb lehetővé teszi táblázatok és diagramok Excel-számolótáblába való átvitelét.

* Az [elemzés](../../azure-monitor/app/analytics.md) hatékony lekérdezési nyelvet biztosít a telemetria számára. Az eredmények exportálására is használható.
* Ha [Power BIban lévő adatait szeretné felfedezni](../../azure-monitor/app/export-power-bi.md ), folyamatos exportálás nélkül is megteheti.
* Az [adatelérési REST API](https://dev.applicationinsights.io/) lehetővé teszi a telemetria programozott módon való elérését.
* A telepítő [folyamatos exportálását a PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport)használatával is elérheti.

A folyamatos exportálás után a rendszer a szokásos [megőrzési időszakra](../../azure-monitor/app/data-retention-privacy.md)vonatkozóan a Application Insightsban is elérhetővé teszi az adatok tárolását

## <a name="continuous-export-advanced-storage-configuration"></a>Folyamatos exportálás speciális tárolási konfiguráció

A folyamatos exportálás nem **támogatja** a következő Azure Storage-funkciókat/-konfigurációkat:

* A [VNET/Azure Storage-tűzfalak](https://docs.microsoft.com/azure/storage/common/storage-network-security) használata az Azure Blob Storage szolgáltatással együtt.

* Nem módosítható [tároló](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) az Azure Blob Storage-hoz.

* [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

## <a name="setup"></a>Folyamatos exportálás létrehozása

1. Az alkalmazás Application Insights erőforrásában a bal oldali konfigurálás területen nyissa meg a folyamatos exportálás elemet, és válassza a **Hozzáadás**lehetőséget:

2. Válassza ki az exportálni kívánt telemetria-adattípusokat.

3. Hozzon létre vagy válasszon ki egy [Azure Storage-fiókot](../../storage/common/storage-introduction.md) , ahol tárolni kívánja az adatkészletet. A Storage díjszabási lehetőségeivel kapcsolatos további információkért látogasson el a [hivatalos díjszabási oldalra](https://azure.microsoft.com/pricing/details/storage/).

     Kattintson a Hozzáadás, az Exportálás célhelye, a Storage-fiók lehetőségre, majd hozzon létre egy új tárolót, vagy válasszon egy meglévő tárolót.

    > [!Warning]
    > Alapértelmezés szerint a tárolási hely ugyanarra a földrajzi régióra lesz beállítva, mint a Application Insights erőforrás. Ha egy másik régióban tárolja, díjköteles lehet.

4. Hozzon létre vagy válasszon ki egy tárolót a tárolóban.

Miután létrehozta az exportálást, elindul. Csak az Exportálás létrehozása után megjelenő adatkérések érkeznek meg.

A tárolóban lévő adatmennyiség körülbelül egy órával késleltethető.

### <a name="to-edit-continuous-export"></a>A folyamatos exportálás szerkesztése

Kattintson a folyamatos exportálás elemre, és válassza ki a szerkeszteni kívánt Storage-fiókot.

### <a name="to-stop-continuous-export"></a>A folyamatos exportálás leállítása

Az Exportálás leállításához kattintson a Letiltás gombra. Amikor az Engedélyezés gombra kattint, az Exportálás új adattal fog újraindulni. Az Exportálás letiltását követően nem fogja lekérni a portálon megjelenő adatkérést.

Az Exportálás végleges leállításához törölje azt. Így nem törli az adatait a tárolóból.

### <a name="cant-add-or-change-an-export"></a>Nem lehet hozzáadni vagy módosítani az exportálást?
* Az Exportálás hozzáadásához vagy módosításához tulajdonosi, közreműködő vagy Application Insights közreműködői hozzáférési jogosultság szükséges. [További információ a szerepkörökről][roles].

## <a name="analyze"></a>Milyen eseményekhez juthat?
Az exportált adatok az alkalmazásból kapott nyers telemetria, kivéve, ha az ügyfél IP-címéből kiszámított helyadatok hozzáadására kerül sor.

A [mintavétel](../../azure-monitor/app/sampling.md) által elvetett adatvesztés nem szerepel az exportált adatsorokban.

A rendszer nem tartalmazza a többi számított metrikát. Például nem exportáljuk az átlagos CPU-kihasználtságot, de exportáljuk a nyers telemetria, amelyből az átlagot számítjuk.

Az adatmennyiség magában foglalja az Ön által beállított [rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md) eredményeit is.

> [!NOTE]
> **Mintavételi.** Ha az alkalmazás sok adatokat küld, a mintavételi funkció működhet, és csak a generált telemetria egy részét küldheti el. [További tudnivalók a mintavételezésről.](../../azure-monitor/app/sampling.md)
>
>

## <a name="get"></a>Az adatgyűjtés ellenőrzése
A tárolót közvetlenül a portálon ellenőrizheti. Kattintson a bal szélső menü Kezdőlap elemére, ahol az "Azure-szolgáltatások" lehetőséget választja a **Storage-fiókok**elemre, majd válassza ki a Storage-fiók nevét, az Áttekintés lapon a szolgáltatások területen válassza a **Blobok** lehetőséget, végül válassza ki a tároló nevét.

Az Azure Storage a Visual Studióban való vizsgálatához nyissa meg a **nézet**, **Cloud Explorer**lehetőséget. (Ha nem rendelkezik a menüparancsokkal, telepítenie kell az Azure SDK-t: Nyissa meg az **új projekt** párbeszédpanelt, C#bontsa ki a Visual/Cloud elemet, és válassza a **beolvasás Microsoft Azure SDK a .net-hez**lehetőséget.)

A blob-tároló megnyitásakor egy tárolót fog látni a blob-fájlokkal. A Application Insights-erőforrás nevéből származtatott fájlok URI-ja, a kialakítási kulcs, a telemetria-típus/dátum/idő. (Az erőforrás neve mind kisbetűs, és a kialakítási kulcs kihagyja a kötőjeleket.)

![A blob-tároló vizsgálata megfelelő eszközzel](./media/export-telemetry/04-data.png)

A dátum és az idő UTC, és az, amikor a telemetria a tárolóban helyezték el – nem a generált idő. Tehát ha kódot ír az adatletöltéshez, az az adatátvitelt lineárisan áthelyezheti.

Az elérési út formája:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Ahol

* `blobCreationTimeUtc` az idő, amikor a blobot a belső átmeneti tárolóban hozták létre
* `blobDeliveryTimeUtc` az az idő, amikor a blobot a rendszer az Exportálás célhelyére másolja

## <a name="format"></a>Adatformátum
* Minden blob egy szövegfájl, amely több "\n"-tagolt sort tartalmaz. Tartalmazza a feldolgozott telemetria körülbelül fél percen belül.
* Az egyes sorok egy telemetria adatpontot jelölnek, például egy kérés vagy egy oldal nézetet.
* Minden sor egy formázatlan JSON-dokumentum. Ha szeretné megülni és bámulni, nyissa meg a Visual Studióban, és válassza a Szerkesztés, speciális, fájl formázása lehetőséget:

![A telemetria megtekintése megfelelő eszközzel](./media/export-telemetry/06-json.png)

Az időtartamok a kullancsokban vannak, ahol a 10 000-es osztásjelek = 1 ms. Ezek az értékek például 1 MS időpontot jelenítenek meg egy kérésnek a böngészőből való elküldéséhez, 3 MS a fogadáshoz, és 1,8 s a lap felfeldolgozásához a böngészőben:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Részletes adatmodell-referenciák a tulajdonságok típusaihoz és értékeihez.](export-data-model.md)

## <a name="processing-the-data"></a>Az adatfeldolgozás
Kis léptékben írhat egy kódot, amely lekéri az adatait, beolvashatja azt egy számolótáblába, és így tovább. Például:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

Nagyobb mintakód esetén lásd: [feldolgozói szerepkör használata][exportasa].

## <a name="delete"></a>Régi adatai törlése
Az Ön felelőssége, hogy kezelje a tárolókapacitást, és szükség esetén törölje a régi adatmennyiséget.

## <a name="if-you-regenerate-your-storage-key"></a>Ha újragenerálta a tárolási kulcsot...
Ha megváltoztatja a kulcsát a tárolóra, a folyamatos exportálás nem fog működni. Ekkor megjelenik egy értesítés az Azure-fiókjában.

Nyissa meg a folyamatos exportálás lapot, és szerkessze az exportálást. Szerkessze az Exportálás célhelyét, de csak hagyja ki ugyanazt a tárolót. A megerősítéshez kattintson az OK gombra.

A folyamatos exportálás újra fog indulni.

## <a name="export-samples"></a>Minták exportálása

* [Exportálás az SQL-be a Stream Analytics használatával][exportasa]
* [2. minta Stream Analytics](export-stream-analytics.md)

Nagyobb lépték esetén vegye fontolóra a [HDInsight](https://azure.microsoft.com/services/hdinsight/) Hadoop-fürtöket a felhőben. A HDInsight számos különböző technológiát biztosít a big data felügyeletéhez és elemzéséhez, és felhasználhatja az Application Insightsból exportált adatok feldolgozására.

## <a name="q--a"></a>Q & A
* *Azonban csak egy diagram egyszeri letöltésére van szükség.*  

    Igen, ezt megteheti. A lap tetején kattintson az **adatexportálás**elemre.
* *Egy exportálást állítottam be, de az áruházban nem találhatók adatkészletek.*

    Az Exportálás beállítása óta Application Insights kapott bármilyen telemetria az alkalmazástól? Csak az új adatgyűjtést fogja kapni.
* *Megpróbáltam beállítani az exportálást, de a hozzáférés megtagadva*

    Ha a fiók a szervezet tulajdonában van, akkor a tulajdonosok vagy közreműködők csoport tagjának kell lennie.
* *Exportálhatók közvetlenül a saját helyszíni áruházba?*

    Nem, sajnos. Az exportálási motor jelenleg csak az Azure Storage szolgáltatással működik.  
* *Van korlátozva az áruházban elhelyezett adatmennyiség?*

    Nem. Az adatmegőrzést addig tartjuk, amíg nem törli az exportálást. Ha elérjük a blob Storage külső korlátait, akkor leállnak, de ez elég nagy. Ezzel a beállítással szabályozhatja, hogy mekkora tárterületet használ.  
* *Hány blobot látok a tárolóban?*

  * Minden exportálásra kiválasztott adattípus esetében minden percben létrejön egy új blob (ha az elérhető).
  * Emellett a nagy forgalmú alkalmazások esetében további partíciós egységek is le vannak foglalva. Ebben az esetben minden egység percenként hoz létre egy blobot.
* *Újrageneráltam a kulcsot a tárhelyhez, vagy Megváltoztattam a tároló nevét, és most az exportálás nem működik.*

    Szerkessze az exportálást, és nyissa meg az Exportálás célhelye lapot. hagyja ki ugyanazt a tárolót, mint korábban, majd a megerősítéshez kattintson az OK gombra. Az Exportálás újra fog indulni. Ha a módosítás az elmúlt néhány napon belül volt, nem veszíti el az adatvesztést.
* *Szüneteltethető az Exportálás?*

    Igen. Kattintson a Letiltás lehetőségre.

## <a name="code-samples"></a>Kódminták

* [Stream Analytics minta](export-stream-analytics.md)
* [Exportálás az SQL-be a Stream Analytics használatával][exportasa]
* [Részletes adatmodell-referenciák a tulajdonságok típusaihoz és értékeihez.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
