---
title: 'Példa: valós idejű videó elemzése – Face'
titleSuffix: Azure Cognitive Services
description: A Face szolgáltatással közel valós idejű elemzést végezhet az élő videó streamből készített kereteken.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ab3f596000216e8555bb84d0d47aff9a6e969eeb
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169904"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Példa: Videók valós idejű elemzése

Ez az útmutató azt ismerteti, hogyan végezhet közel valós idejű elemzést egy élő videó-adatfolyam képkockáin. Az ilyen rendszerek alapvető összetevői:

- A képkockák beszerzése a videóforrásból
- Az elemezni kívánt képkockák kiválasztása
- A képkockák elküldése az API-ra
- Az API-hívás által visszaadott elemzési eredmények feldolgozása

A kódminták C# nyelven íródtak, a kód a GitHubon a következő címen található: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>A módszer

A videostreamek közel valós idejű elemzése többféleképpen oldható meg. Itt most három módszert mutatunk be, növekvő kifinomultsági szinten.

### <a name="a-simple-approach"></a>Az egyszerű módszer

A közel valós idejű elemzési rendszerek legegyszerűbb kialakítása végtelen hurok, amelyben minden iteráció egy keretet vár, elemzi azt, majd felhasználja az eredményt:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Ha az elemzést egy kisméretű ügyféloldali algoritmus végzi, ez a módszer megfelelő. Ha azonban az elemzés a felhőben történik, az érintett késés azt jelenti, hogy egy API-hívás több másodpercig is eltarthat. Ebben az időszakban nem vesszük fel a képeket, és a szál lényegében semmit sem csinál. A maximális képkocka-sebességet így az API-hívások késése korlátozza.

### <a name="parallelizing-api-calls"></a>Az API-hívások párhuzamosítása

Bár a kisméretű ügyféloldali algoritmusokhoz megfelelő egy egyszerű egyszálas hurok, a felhőalapú API-hívásokhoz a felmerülő késés miatt nem a legmegfelelőbb. A megoldás erre a problémára az, hogy a hosszú futású API-hívásokat párhuzamosan hajtjuk végre a képkockák rögzítésével. A C# nyelvben ezt feladatalapú párhuzamosítással érhetjük el. Például a következőképp:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () => 
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

A kód mindegyik elemzést külön feladatban indítja el, amelyek a háttérben futnak, miközben újabb képkockákat rögzíthetünk. Ezzel a módszerrel elkerülhető a fő szál blokkolása, miközben egy API-hívás visszaadására vár, de elvesztettük néhány garanciát, amelyet az egyszerű verzió biztosít. Több API-hívás is előfordulhat párhuzamosan, és előfordulhat, hogy az eredmények nem megfelelő sorrendben történnek vissza. Ez azt is okozhatja, hogy több szál is megadhatja a ConsumeResult () függvényt egyszerre, ami veszélyes lehet, ha a függvény nem a szál biztonságos. Végezetül pedig ez az egyszerű kód nem követi nyomon a létrehozott feladatokat, így a kivételek csendesen eltűnnek. Ezért az utolsó lépés egy olyan "fogyasztói" szál hozzáadása, amely nyomon követheti az elemzési feladatokat, megnöveli a kivételeket, leállíthatja a hosszan futó feladatokat, és gondoskodhat arról, hogy az eredmények megfelelő sorrendben legyenek felhasználva.

### <a name="a-producer-consumer-design"></a>A létrehozó-feldolgozó kialakítás

A végső "előállító-fogyasztó" alapú rendszerünkben van egy előállító szál, amely a korábbi végtelen ciklusunkhoz hasonlít. Ahelyett azonban, hogy az elemzési eredményeket azonnal feldolgozná, amint elérhetők, a feldolgozó egyszerűen egy üzenetsorba küldi a feladatokat, ahol nyomon követheti őket.

```csharp
// Queue that will contain the API call tasks. 
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();
     
// Producer thread. 
while (true)
{
    // Grab a frame. 
    Frame f = GrabFrame();
 
    // Decide whether to analyze the frame. 
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread. 
        var analysisTask = Task.Run(async () => 
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }
        
        // Push the task onto the queue. 
        taskQueue.Add(analysisTask);
    }
}
```

Olyan felhasználói szálat is használhatunk, amely elvégzi a várólistán lévő feladatok elvégzését, megvárja a befejezést, és vagy megjeleníti az eredményt, vagy megnöveli a dobott kivételt. Az üzenetsor használatával biztosíthatjuk, hogy az eredmények egyesével, a megfelelő sorrendben lesznek feldolgozva, a rendszer maximális képkocka-sebességének korlátozása nélkül.

```csharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
    var output = await analysisTask;
     
    // Consume the exception or result. 
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>A megoldás megvalósítása

### <a name="getting-started"></a>Első lépések

Ahhoz, hogy az alkalmazás a lehető leggyorsabban elérhető legyen, a fent ismertetett rendszer rugalmas megvalósítását fogja használni. A kódot a következő helyen éri el: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

A könyvtár tartalmazza a FrameGrabber osztályt, amely a fentiekben ismertetett gyártó-fogyasztói rendszer megvalósításával dolgozza fel a képkockákat egy webkamera használatával. A felhasználó megadhatja az API-hívás pontos formáját, és az osztály az eseményeket használva lehetővé teszi, hogy a hívó kód megismerje az új keret beszerzését vagy egy új elemzési eredmény elérhetővé tételét.

Az egyes lehetőségek bemutatására a kódtárat használó két mintaalkalmazás érhető el. Az első egy egyszerű konzolos alkalmazás, és az alább látható egyszerűsített verzió. A rendszer az alapértelmezett webkamera kereteit is megragadja, és a Face szolgáltatásba küldi azokat.

```csharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face Client. Insert your Face API key here.
            private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

A második mintaalkalmazás egy kicsit érdekesebb, és ebben kiválasztható, hogy melyik API-t hívja meg a videóképeken. A bal oldalon az alkalmazás az élő videó előnézetét jeleníti meg, a jobb oldalon pedig a legújabb API-eredményéket jeleníti meg a megfelelő képkockára vetítve.

A legtöbb módban látható késés lesz a bal oldali élő videó és a jobb oldali szemléltetett elemzés között. Ez a késés az API-kérés feldolgozásának időtartamával egyezik. Az egyik kivétel a "EmotionsWithClientFaceDetect" mód, amely a OpenCV használatával helyileg végzi az észlelést az ügyfélszámítógépen, mielőtt beküldi a képeket a Cognitive Servicesba. Így azonnal megjelenítheti az észlelt arcot, majd frissítheti az érzelmeket, ha az API-hívást visszaadja. Ez egy példa a "hibrid" megközelítésre, ahol az ügyfél elvégezheti az egyszerű feldolgozást, és a Cognitive Services API-k szükség esetén fejlettebb elemzéssel bővítheti ezt.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>A kódbázisba való integráció

A minta használatához hajtsa végre az alábbi lépéseket:

1. A Vision API-k API-kulcsainak beszerzése az [előfizetésekből](https://azure.microsoft.com/try/cognitive-services/). A videoképkockák elemzéséhez megfelelő API-k:
    - [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview)

2. Klónozza a [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub-adattárat

3. Nyissa meg a mintát a Visual Studio 2015-ben, és hozza létre és futtassa a minta alkalmazásokat:
    - A BasicConsoleSample esetében a Face kulcs nem kódolható közvetlenül a [BasicConsoleSample/program. cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs)-ban.
    - A LiveCameraSample esetében a kulcsokat az alkalmazás Settings (Beállítások) panelén kell megadni. A kulcsokat a rendszer felhasználói adatként a munkamenetek közt is megőrzi.
        

Ha készen áll az integrálásra, **a saját projektjeiből hivatkozhat a VideoFrameAnalyzer könyvtárra.** 

## <a name="summary"></a>Összefoglalás

Ebben az útmutatóban megtanulta, hogyan futtathat közel valós idejű elemzést az élő videó streameken az arc, a Computer Vision és az Emotion API-k használatával, és hogyan használhatja a minta kódját a kezdéshez. Az [Azure Cognitive Services regisztrációs oldalán](https://azure.microsoft.com/try/cognitive-services/)megkezdheti az alkalmazás felépítése ingyenes API-kulcsokkal. 

Nyugodtan visszajelzést és javaslatokat küldhet a GitHub- [tárházban](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) , vagy szélesebb körű API-visszajelzéseket a [UserVoice webhelyén](https://cognitive.uservoice.com/).

## <a name="related-topics"></a>Kapcsolódó témakörök
- [Hogyan lehet arcokat azonosítani a képen](HowtoIdentifyFacesinImage.md)
- [Hogyan lehet arcokat megtalálni a képen](HowtoDetectFacesinImage.md)
