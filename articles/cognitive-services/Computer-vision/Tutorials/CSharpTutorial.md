---
title: 'Minta: a képfeldolgozó alkalmazás megismeréseC#'
titleSuffix: Azure Cognitive Services
description: Ismerkedjen meg az Azure Cognitive Services Computer Vision APIt használó alapszintű Windows-alkalmazással. OCR végrehajtása, miniatűrök létrehozása és képek vizuális jellemzőinek használata.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: b492d8e3bdcf6d9a41df3eb79ef159985cc715cf
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76168884"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Minta: a képfeldolgozó alkalmazás megismeréseC#

Fedezzen fel egy alapszintű Windows-alkalmazást, amely a Computer Visiont használja az optikai karakterfelismerés (OCR) elvégzéséhez, intelligens vágású miniatűrök létrehozásához, valamint a vizuális funkciók (például arcok) észleléséhez, kategorizálásához, címkézéséhez és leírásához a képen. Az alábbi példában elküldheti egy kép URL-címét vagy egy helyileg tárolt fájlt. Ez a nyílt forráskódú példa sablonként használható a Windows saját alkalmazásának létrehozásához a Computer Vision API és Windows megjelenítési alaprendszer (WPF) használatával, amely a .NET-keretrendszer részét képezi.

> [!div class="checklist"]
> * Minta alkalmazás beszerzése a GitHubról
> * A minta alkalmazás megnyitása és felépítése a Visual Studióban
> * A minta alkalmazás futtatása és használata különböző forgatókönyvek elvégzéséhez
> * Ismerkedjen meg a minta alkalmazásban található különféle forgatókönyvekkel

## <a name="prerequisites"></a>Előfeltételek

A minta alkalmazás megismerése előtt győződjön meg arról, hogy teljesítette a következő előfeltételeket:

* A [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) vagy újabb verzióval kell rendelkeznie.
* Szüksége lesz egy Computer Vision-előfizetői azonosítóra. A [kipróbálási Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)ingyenes próbaverziós kulcsot is beszerezhet. Vagy kövesse a [Cognitive Services fiók létrehozása](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) az Computer Visionra való előfizetéshez és a kulcs beszerzéséhez című témakör utasításait. Jegyezze fel a szolgáltatás végpontjának URL-címét is.

## <a name="get-the-sample-app"></a>Mintaalkalmazás letöltése

A Computer Vision minta alkalmazás a GitHubon érhető el a `Microsoft/Cognitive-Vision-Windows` adattárból. Ez a tárház a `Microsoft/Cognitive-Common-Windows` adattárat git almodulként is tartalmazza. A tárházat rekurzív módon is megteheti, beleértve az almodult is, ha a parancssorból a `git clone --recurse-submodules` parancsot vagy a GitHub Desktopot használja.

Ha például a Computer Vision minta alkalmazás adattárát rekurzív módon szeretné klónozást futtatni a parancssorból, futtassa a következő parancsot:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Ezt a tárházat ne töltse le ZIP-fájlként. A git nem tartalmaz almodulokat a tárház ZIP-fájlként való letöltésekor.

### <a name="get-optional-sample-images"></a>Opcionális mintaképek beolvasása

Igény szerint használhatja az [arc](../../Face/Overview.md) minta alkalmazásban található minta lemezképeket is, amelyek a githubon érhetők el a `Microsoft/Cognitive-Face-Windows` adattárból. Ez a minta alkalmazás olyan mappát tartalmaz, `/Data`, amely több rendszerképet tartalmaz. Ezt a tárházat a Computer Vision minta alkalmazáshoz tartozó módszerekkel is rekurzív módon klónozott.

Ha például egy parancssorból szeretné rekurzív módon klónozott adattárat a Face minta alkalmazáshoz, futtassa a következő parancsot:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>A minta alkalmazás megnyitása és felépítése a Visual Studióban

Először a minta alkalmazást kell létrehoznia, hogy a Visual Studio fel tudja oldani a függőségeket, mielőtt futtatja vagy megtekinti a minta alkalmazást. A minta alkalmazás megnyitásához és létrehozásához hajtsa végre a következő lépéseket:

1. Nyissa meg a Visual Studio-megoldás fájlját `/Sample-WPF/VisionAPI-WPF-Samples.sln`a Visual Studióban.
1. Győződjön meg arról, hogy a Visual Studio-megoldás két projektet tartalmaz:  

   * SampleUserControlLibrary
   * VisionAPI-WPF – minták  

   Ha a SampleUserControlLibrary-projekt nem érhető el, győződjön meg arról, hogy a `Microsoft/Cognitive-Vision-Windows` adattár rekurzív klónozása megtörténik.
1. A Visual Studióban nyomja le a CTRL + SHIFT + B billentyűkombinációt, vagy válassza a **Létrehozás** elemet a menüszalag menüből, majd válassza a **megoldás létrehozása** lehetőséget a megoldás felépítéséhez.

## <a name="run-and-interact-with-the-sample-app"></a>A minta alkalmazás futtatása és használata

A minta alkalmazást futtatva megtekintheti, hogyan működik együtt Önnel és a Computer Vision ügyféloldali függvénytárával, amikor különböző feladatokat hajt végre, például miniatűröket vagy címkéző képeket hoz létre. A minta alkalmazás futtatásához és használatához hajtsa végre a következő lépéseket:

1. A Build befejezése után nyomja le az **F5** billentyűt, vagy a menüszalag menüjében válassza a **hibakeresés** lehetőséget, majd válassza a **hibakeresés elindítása** lehetőséget a minta alkalmazás futtatásához.
1. Amikor megjelenik a minta alkalmazás, válassza az **előfizetés-kulcskezelő** lehetőséget a navigációs ablaktáblán az előfizetés-kulcs kezelése lap megjelenítéséhez.
   ![az előfizetési kulcs kezelése lap](../Images/Vision_UI_Subscription.PNG)  
1. Adja meg az előfizetési kulcsot az **előfizetési kulcsban**.
1. Adja meg a végpont URL-címét a **végpontban**.  
   Ha például az Computer Vision ingyenes próbaverziójában használja az előfizetési kulcsot, adja meg a következő végpont URL-címét: `https://westcentralus.api.cognitive.microsoft.com`  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Ha nem szeretné megadni az előfizetési kulcsot és a végponti URL-címet a minta alkalmazás következő futtatásakor, válassza a **beállítás mentése** lehetőséget, hogy az előfizetési kulcs és a végpont URL-címét mentse a számítógépre. Ha törölni szeretné a korábban mentett előfizetési kulcsot és a végpont URL-címét, válassza a **beállítás törlése**lehetőséget.

   > [!NOTE]
   > A minta alkalmazás elkülönített tárterületet használ, és `System.IO.IsolatedStorage`az előfizetési kulcs és a végpont URL-címének tárolásához.

1. A navigációs ablaktáblán válassza **a forgatókönyv lehetőséget** , válassza ki a minta alkalmazáshoz jelenleg mellékelt forgatókönyvek egyikét:  

   | Alkalmazási helyzet | Leírás |
   |----------|-------------|
   |Rendszerkép elemzése | A [rendszerkép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) művelettel elemzi a helyi vagy a távoli rendszerképeket. Kiválaszthatja az elemzés vizuális funkcióit és nyelvét, és megtekintheti a képet és az eredményeket is.  |
   |Rendszerkép elemzése a tartományi modellel | A [tartománynév-specifikus modellek](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) művelettel sorolja fel azokat a tartományi modelleket, amelyekről kiválaszthatja a tartományt, valamint a [tartomány-specifikus tartalom felismerése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) műveletet egy helyi vagy távoli rendszerkép elemzéséhez a kiválasztott tartományi modell használatával. Megadhatja az elemzés nyelvét is. |
   |Rendszerkép leírása | A [képleírási](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) művelettel létrehoz egy helyi vagy távoli rendszerkép olvasható leírását. Megadhatja a Leírás nyelvét is. |
   |Címkék készítése | A [kép címkézése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) műveletet használja a helyi vagy távoli rendszerkép vizualizációs funkcióinak címkézéséhez. Megadhatja a címkékhez használt nyelvet is. |
   |Szövegfelismerés (OCR) | Az [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) művelettel azonosítja és Kinyeri a nyomtatott szöveget egy képből. Kiválaszthatja a használni kívánt nyelvet, vagy engedélyezheti Computer Vision a nyelv automatikus észlelését. |
   |Szövegfelismerés v2 (angol) | A [szövegfelismerés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) használja, és [lekérdezi a szövegfelismerés műveleti eredmény](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) műveleteit a nyomtatott vagy kézzel írt szöveg aszinkron felismeréséhez és kinyeréséhez. |
   |Miniatűr beolvasása | A [miniatűr beolvasása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) művelettel egy helyi vagy távoli rendszerkép miniatűrjét állítja elő. |

   Az alábbi képernyőfelvételen a képelemzési forgatókönyvhöz megadott oldal látható, a minta-rendszerkép elemzése után.
   ![képernyőkép a rendszerkép elemzése lapról](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>A minta alkalmazás megismerése

A Computer Vision minta alkalmazáshoz készült Visual Studio-megoldás két projektet tartalmaz:

* SampleUserControlLibrary  
  A SampleUserControlLibrary projekt több Cognitive Services-minta által megosztott funkciókat biztosít. A projekt a következőket tartalmazza:
  * SampleScenarios  
    Egy szabványosított bemutatót biztosító UserControl vezérlő, mint például a címsor, a navigációs ablaktábla és a tartalom panel, mintákhoz. A Computer Vision minta alkalmazás ezt a vezérlőt használja a MainWindow. XAML ablakban a forgatókönyvek és az egyes forgatókönyvekben megosztott hozzáférési információk megjelenítéséhez, például az előfizetési kulcs és a végpont URL-címének megadásához.
  * SubscriptionKeyPage  
    Egy olyan oldal, amely szabványosított elrendezést biztosít a minta alkalmazás előfizetési kulcsának és végponti URL-címének megadásához. A Computer Vision minta alkalmazás ezen a lapon kezelheti a forgatókönyv lapjai által használt előfizetési kulcsot és végpont URL-címét.
  * VideoResultControl  
    UserControl vezérlő, amely szabványosított bemutatót biztosít a videóval kapcsolatos információkhoz. A Computer Vision minta alkalmazás nem használja ezt a vezérlőt.
* VisionAPI-WPF – minták  
  A Computer Vision minta alkalmazás fő projektje, ez a projekt a Computer Vision érdekes funkcióit tartalmazza. A projekt a következőket tartalmazza:
  * AnalyzeInDomainPage. XAML  
    Az elemzés rendszerképének a tartományi modellel kapcsolatos forgatókönyvhöz tartozó lapja.
  * AnalyzeImage. XAML  
    A rendszerkép elemzése forgatókönyv forgatókönyv lapja.
  * DescribePage. XAML  
    A rendszerkép leírására szolgáló forgatókönyv lapja.
  * ImageScenarioPage.cs  
    A ImageScenarioPage osztály, amelyből a rendszer a minta alkalmazás összes forgatókönyv lapját származtatja. Ez az osztály kezeli a funkciókat, például a hitelesítő adatok megadását és a kimenet formázását, amelyet az összes forgatókönyv oldalai megosztanak.
  * MainWindow.xaml  
    A minta alkalmazás főablaka a SampleScenarios vezérlőelem használatával mutatja be a SubscriptionKeyPage és a forgatókönyvek lapjait.
  * OCRPage. XAML  
    A szövegfelismerés (OCR) forgatókönyvhöz tartozó forgatókönyv lapja.
  * RecognizeLanguage.cs  
    A RecognizeLanguage osztály, amely információt nyújt a minta alkalmazás különböző módszerei által támogatott nyelvekről.
  * TagsPage. XAML  
    A címkék előállítására szolgáló forgatókönyv lapja.
  * TextRecognitionPage. XAML  
    A szövegfelismerés v2 (angol) forgatókönyvhöz tartozó forgatókönyv lapja.
  * ThumbnailPage. XAML  
    A miniatűr beolvasása forgatókönyv forgatókönyv lapja.

### <a name="explore-the-sample-code"></a>A mintakód megismerése

A mintakód kulcsfontosságú részeit olyan megjegyzési blokkokkal együtt kell kialakítani, amelyek a `KEY SAMPLE CODE STARTS HERE` és az `KEY SAMPLE CODE ENDS HERE`-vel kezdődnek, így könnyebben megismerheti a minta alkalmazást. A mintakód ezen kulcsfontosságú részei tartalmazzák a legfontosabb tudnivalókat a Computer Vision API ügyféloldali kódtár különböző feladatok elvégzéséhez való használatának megismeréséhez. A Visual Studióban `KEY SAMPLE CODE STARTS HERE` kereshet úgy, hogy a Computer Vision minta alkalmazásban a kód legfontosabb részei között mozog. 

Például a AnalyzePage. XAML elemben látható és azt tartalmazó `UploadAndAnalyzeImageAsync` metódus azt mutatja be, hogyan használható az ügyféloldali kódtár egy helyi rendszerkép elemzéséhez a `ComputerVisionClient.AnalyzeImageInStreamAsync` metódus meghívásával.

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>Az ügyféloldali kódtár megismerése

Ez a minta alkalmazás a Computer Vision API ügyféloldali kódtárat használja, C# amely egy vékony ügyfél-burkoló az Azure Cognitive Services Computer Vision APIhoz. Az ügyféloldali kódtár a [Microsoft. Azure. CognitiveServices. vízió. ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) csomagban található NuGet érhető el. A Visual Studio alkalmazás létrehozásakor a megfelelő NuGet-csomagból beolvasta az ügyféloldali kódtárat. Az ügyféloldali kódtár forráskódját az `Microsoft/Cognitive-Vision-Windows` adattár `/ClientLibrary` mappájába is megtekintheti.

Az ügyféloldali függvénytár a `ComputerVisionClient` osztály, a `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` névtérben, míg a `ComputerVisionClient` osztály által a `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` névtérben való Computer Vision interakció során használt modelleket tartalmazza. A minta alkalmazásban található különféle XAML-forgatókönyvek oldalain a következő `using` irányelvek találhatók meg ezekhez a névterekhez:

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

A `ComputerVisionClient` osztály különböző módszereiről részletesebben is tájékozódhat a Computer Vision-minta alkalmazásban található forgatókönyvek megismerése során.

## <a name="explore-the-analyze-image-scenario"></a>A képelemzési forgatókönyv megismerése

Ezt a forgatókönyvet a AnalyzePage. XAML lap kezeli. Kiválaszthatja az elemzés vizuális funkcióit és nyelvét, és megtekintheti a képet és az eredményeket is. A forgatókönyv oldal ezt a következő módszerek egyikével hajtja végre a rendszerkép forrásától függően:

* UploadAndAnalyzeImageAsync  
  Ez a módszer helyi lemezképekhez használatos, amelyekben a lemezképet `Stream`ként kell kódolni, és a `ComputerVisionClient.AnalyzeImageInStreamAsync` metódus meghívásával el kell juttatni a Computer Visionnak.
* AnalyzeUrlAsync  
  Ez a módszer távoli lemezképekhez használatos, amelyekben a lemezkép URL-címét a rendszer a `ComputerVisionClient.AnalyzeImageAsync` metódus meghívásával elküldi Computer Vision.

A `UploadAndAnalyzeImageAsync` metódus új `ComputerVisionClient` példányt hoz létre a megadott előfizetési kulcs és végpont URL-cím használatával. Mivel a minta alkalmazás egy helyi rendszerképet elemez, el kell küldenie a rendszerkép tartalmát a Computer Visionba. Ekkor megnyílik a `imageFilePath`ban megadott helyi fájl `Stream`ként való olvasáshoz, majd beolvassa a forgatókönyv oldalon kiválasztott vizuális funkciókat és nyelveket. Meghívja a `ComputerVisionClient.AnalyzeImageInStreamAsync` metódust, átadja a fájl `Stream`ét, a vizualizáció funkcióit és a nyelvet, majd az eredményt `ImageAnalysis` példányként adja vissza. A `ImageScenarioPage` osztálytól örökölt metódusok a forgatókönyv lapon visszaadott eredményeket jelenítik meg.

A `AnalyzeUrlAsync` metódus új `ComputerVisionClient` példányt hoz létre a megadott előfizetési kulcs és végpont URL-cím használatával. Beolvassa a forgatókönyv oldalon kiválasztott vizuális funkciókat és nyelveket. Meghívja a `ComputerVisionClient.AnalyzeImageInStreamAsync` metódust, átadja a kép URL-címét, a vizualizáció funkcióit és a nyelvet, majd visszaküldi az eredményt `ImageAnalysis` példányként. A `ImageScenarioPage` osztálytól örökölt metódusok a forgatókönyv lapon visszaadott eredményeket jelenítik meg.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>A rendszerkép elemzése a tartományi modell forgatókönyvével

Ezt a forgatókönyvet a AnalyzeInDomainPage. XAML lap kezeli. Kiválaszthat egy tartományi modellt, például `celebrities` vagy `landmarks`, valamint a nyelvet, hogy végrehajtsa a rendszerkép tartomány-specifikus elemzését, és megtekintse a képet és az eredményeket is. A forgatókönyv oldal a következő metódusokat használja a rendszerkép forrásától függően:

* GetAvailableDomainModelsAsync  
  Ez a módszer beolvassa az elérhető tartományi modellek listáját a Computer Visionból, és az `ComputerVisionClient.ListModelsAsync` metódus használatával tölti fel az `_domainModelComboBox` kombinált lista vezérlőelemet az oldalon.
* UploadAndAnalyzeInDomainImageAsync  
  Ez a módszer helyi lemezképekhez használatos, amelyekben a lemezképet `Stream`ként kell kódolni, és a `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` metódus meghívásával el kell juttatni a Computer Visionnak.
* AnalyzeInDomainUrlAsync  
  Ez a módszer távoli lemezképekhez használatos, amelyekben a lemezkép URL-címét a rendszer a `ComputerVisionClient.AnalyzeImageByDomainAsync` metódus meghívásával elküldi Computer Vision.

A `UploadAndAnalyzeInDomainImageAsync` metódus új `ComputerVisionClient` példányt hoz létre a megadott előfizetési kulcs és végpont URL-cím használatával. Mivel a minta alkalmazás egy helyi rendszerképet elemez, el kell küldenie a rendszerkép tartalmát a Computer Visionba. Ekkor megnyílik a `imageFilePath`ban megadott helyi fájl `Stream`ként való olvasáshoz, majd lekéri a forgatókönyv lapon kiválasztott nyelvet. Meghívja a `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` metódust, átadja a fájl `Stream`ét, a tartományi modell nevét és a nyelvet, majd az eredményt `DomainModelResults` példányként adja vissza. A `ImageScenarioPage` osztálytól örökölt metódusok a forgatókönyv lapon visszaadott eredményeket jelenítik meg.

A `AnalyzeInDomainUrlAsync` metódus új `ComputerVisionClient` példányt hoz létre a megadott előfizetési kulcs és végpont URL-cím használatával. A forgatókönyv lapon kiválasztott nyelvet kapja meg. Meghívja a `ComputerVisionClient.AnalyzeImageByDomainAsync` metódust, átadja a kép URL-címét, a vizualizáció funkcióit és a nyelvet, majd visszaküldi az eredményt `DomainModelResults` példányként. A `ImageScenarioPage` osztálytól örökölt metódusok a forgatókönyv lapon visszaadott eredményeket jelenítik meg.

## <a name="explore-the-describe-image-scenario"></a>Fedezze fel a képleírási forgatókönyvet

Ezt a forgatókönyvet a DescribePage. XAML lap kezeli. Kiválaszthat egy nyelvet a rendszerkép egy ember által olvasható leírásának létrehozásához, és megtekintheti a képet és az eredményeket is. A forgatókönyv oldal a következő metódusokat használja a rendszerkép forrásától függően:

* UploadAndDescribeImageAsync  
  Ez a módszer helyi lemezképekhez használatos, amelyekben a lemezképet `Stream`ként kell kódolni, és a `ComputerVisionClient.DescribeImageInStreamAsync` metódus meghívásával el kell juttatni a Computer Visionnak.
* DescribeUrlAsync  
  Ez a módszer távoli lemezképekhez használatos, amelyekben a lemezkép URL-címét a rendszer a `ComputerVisionClient.DescribeImageAsync` metódus meghívásával elküldi Computer Vision.

A `UploadAndDescribeImageAsync` metódus új `ComputerVisionClient` példányt hoz létre a megadott előfizetési kulcs és végpont URL-cím használatával. Mivel a minta alkalmazás egy helyi rendszerképet elemez, el kell küldenie a rendszerkép tartalmát a Computer Visionba. Ekkor megnyílik a `imageFilePath`ban megadott helyi fájl `Stream`ként való olvasáshoz, majd lekéri a forgatókönyv lapon kiválasztott nyelvet. Meghívja a `ComputerVisionClient.DescribeImageInStreamAsync` metódust, átadja a fájl `Stream`ét, a jelöltek maximális számát (ebben az esetben 3) és a nyelvet, majd az eredményt `ImageDescription` példányként adja vissza. A `ImageScenarioPage` osztálytól örökölt metódusok a forgatókönyv lapon visszaadott eredményeket jelenítik meg.

A `DescribeUrlAsync` metódus új `ComputerVisionClient` példányt hoz létre a megadott előfizetési kulcs és végpont URL-cím használatával. A forgatókönyv lapon kiválasztott nyelvet kapja meg. Meghívja a `ComputerVisionClient.DescribeImageAsync` metódust, átadja a képurl-címet, a jelöltek maximális számát (ebben az esetben 3) és a nyelvet, majd visszaküldi az eredményt `ImageDescription` példányként. A `ImageScenarioPage` osztálytól örökölt metódusok a forgatókönyv lapon visszaadott eredményeket jelenítik meg.

## <a name="explore-the-generate-tags-scenario"></a>A címkék létrehozásának forgatókönyvének megismerése

Ezt a forgatókönyvet a TagsPage. XAML lap kezeli. Megadhat egy nyelvet a képek vizuális funkcióinak címkézéséhez, és megtekintheti a képet és az eredményeket is. A forgatókönyv oldal a következő metódusokat használja a rendszerkép forrásától függően:

* UploadAndGetTagsForImageAsync  
  Ez a módszer helyi lemezképekhez használatos, amelyekben a lemezképet `Stream`ként kell kódolni, és a `ComputerVisionClient.TagImageInStreamAsync` metódus meghívásával el kell juttatni a Computer Visionnak.
* GenerateTagsForUrlAsync  
  Ez a módszer távoli lemezképekhez használatos, amelyekben a lemezkép URL-címét a rendszer a `ComputerVisionClient.TagImageAsync` metódus meghívásával elküldi Computer Vision.

A `UploadAndGetTagsForImageAsync` metódus új `ComputerVisionClient` példányt hoz létre a megadott előfizetési kulcs és végpont URL-cím használatával. Mivel a minta alkalmazás egy helyi rendszerképet elemez, el kell küldenie a rendszerkép tartalmát a Computer Visionba. Ekkor megnyílik a `imageFilePath`ban megadott helyi fájl `Stream`ként való olvasáshoz, majd lekéri a forgatókönyv lapon kiválasztott nyelvet. Meghívja a `ComputerVisionClient.TagImageInStreamAsync` metódust, átadja az `Stream` a fájlhoz és a nyelvhez, majd az eredményt `TagResult` példányként adja vissza. A `ImageScenarioPage` osztálytól örökölt metódusok a forgatókönyv lapon visszaadott eredményeket jelenítik meg.

A `GenerateTagsForUrlAsync` metódus új `ComputerVisionClient` példányt hoz létre a megadott előfizetési kulcs és végpont URL-cím használatával. A forgatókönyv lapon kiválasztott nyelvet kapja meg. Meghívja a `ComputerVisionClient.TagImageAsync` metódust, átadja a képurl-címet és a nyelvet, majd az eredményt `TagResult` példányként adja vissza. A `ImageScenarioPage` osztálytól örökölt metódusok a forgatókönyv lapon visszaadott eredményeket jelenítik meg.

## <a name="explore-the-recognize-text-ocr-scenario"></a>A szövegfelismerés (OCR) forgatókönyv megismerése

Ezt a forgatókönyvet a OCRPage. XAML lap kezeli. Kiválaszthat egy nyelvet, amellyel felismerheti és kinyerheti a képekből a nyomtatott szöveget, és megtekintheti a képet és az eredményeket is. A forgatókönyv oldal a következő metódusokat használja a rendszerkép forrásától függően:

* UploadAndRecognizeImageAsync  
  Ez a módszer helyi lemezképekhez használatos, amelyekben a lemezképet `Stream`ként kell kódolni, és a `ComputerVisionClient.RecognizePrintedTextInStreamAsync` metódus meghívásával el kell juttatni a Computer Visionnak.
* RecognizeUrlAsync  
  Ez a módszer távoli lemezképekhez használatos, amelyekben a lemezkép URL-címét a rendszer a `ComputerVisionClient.RecognizePrintedTextAsync` metódus meghívásával elküldi Computer Vision.

A `UploadAndRecognizeImageAsync` metódus új `ComputerVisionClient` példányt hoz létre a megadott előfizetési kulcs és végpont URL-cím használatával. Mivel a minta alkalmazás egy helyi rendszerképet elemez, el kell küldenie a rendszerkép tartalmát a Computer Visionba. Ekkor megnyílik a `imageFilePath`ban megadott helyi fájl `Stream`ként való olvasáshoz, majd lekéri a forgatókönyv lapon kiválasztott nyelvet. Meghívja a `ComputerVisionClient.RecognizePrintedTextInStreamAsync` metódust, amely azt jelzi, hogy a rendszer nem észleli a tájolást, és átadja a fájlnak és a nyelvnek a `Stream`, majd `OcrResult` példányként visszaadja az eredményt. A `ImageScenarioPage` osztálytól örökölt metódusok a forgatókönyv lapon visszaadott eredményeket jelenítik meg.

A `RecognizeUrlAsync` metódus új `ComputerVisionClient` példányt hoz létre a megadott előfizetési kulcs és végpont URL-cím használatával. A forgatókönyv lapon kiválasztott nyelvet kapja meg. Meghívja a `ComputerVisionClient.RecognizePrintedTextAsync` metódust, amely azt jelzi, hogy a tájolás nem észlelhető, és a rendszer átadja a képurl-címet és a nyelvet, majd visszaküldi az eredményt `OcrResult` példányként. A `ImageScenarioPage` osztálytól örökölt metódusok a forgatókönyv lapon visszaadott eredményeket jelenítik meg.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Ismerkedés a szövegfelismerés v2 (angol) forgatókönyvvel

Ezt a forgatókönyvet a TextRecognitionPage. XAML lap kezeli. Kiválaszthatja a felismerési módot és a nyelvet, hogy aszinkron módon felismerje és kibontsa a nyomtatott vagy kézírásos szövegeket egy képből, és a képet és az eredményeket is megtekintheti. A forgatókönyv oldal a következő metódusokat használja a rendszerkép forrásától függően:

* UploadAndRecognizeImageAsync  
  Ez a módszer helyi lemezképekhez használatos, amelyekben a lemezképet `Stream`ként kell kódolni, és a `RecognizeAsync` metódus meghívásával, valamint a `ComputerVisionClient.RecognizeTextInStreamAsync` metódus paraméteres delegált átadásával kell elküldeni a Computer Visionnak.
* RecognizeUrlAsync  
  Ez a módszer távoli lemezképekhez használatos, amelyekben a lemezkép URL-címét a rendszer a `RecognizeAsync` metódus meghívásával, valamint a `ComputerVisionClient.RecognizeTextAsync` metódus paraméteres delegált átadásával küldi el Computer Vision.
* RecognizeAsync ez a módszer kezeli a `UploadAndRecognizeImageAsync` és `RecognizeUrlAsync` metódusok aszinkron hívását, valamint az eredmények lekérdezését az `ComputerVisionClient.GetTextOperationResultAsync` metódus meghívásával.

A Computer Vision minta alkalmazásban szereplő egyéb forgatókönyvekkel ellentétben ez a forgatókönyv aszinkron módon történik, hogy az egyik metódust a folyamat elindítására hívja meg, de egy másik metódust kell meghívni az állapot vizsgálatára és a folyamat eredményének visszaadására. Ebben a forgatókönyvben a logikai folyamat némileg eltér a többi forgatókönyvtől.

A `UploadAndRecognizeImageAsync` módszer megnyithatja a `imageFilePath`ban megadott helyi fájlt `Stream`ként való olvasáshoz, majd meghívja a `RecognizeAsync` metódust, amely átadja a következőt:

* Egy lambda kifejezés a `ComputerVisionClient.RecognizeTextInStreamAsync` metódus paraméteres aszinkron delegált számára, a fájl `Stream` és a felismerési mód paraméterként a `GetHeadersAsyncFunc`ban.
* Lambda kifejezés egy delegált számára a `Operation-Location` válasz fejlécének értékének beolvasásához `GetOperationUrlFunc`ban.

A `RecognizeUrlAsync` metódus meghívja a `RecognizeAsync` metódust, amely a következőket adja meg:

* A `ComputerVisionClient.RecognizeTextAsync` metódus paraméteres aszinkron delegálására szolgáló lambda kifejezés, a távoli rendszerkép URL-címével és a felismerési móddal, `GetHeadersAsyncFunc`ban.
* Lambda kifejezés egy delegált számára a `Operation-Location` válasz fejlécének értékének beolvasásához `GetOperationUrlFunc`ban.

Ha a `RecognizeAsync` metódus elkészült, a `UploadAndRecognizeImageAsync` és `RecognizeUrlAsync` metódusok is `TextOperationResult` példányként adják vissza az eredményt. A `ImageScenarioPage` osztálytól örökölt metódusok a forgatókönyv lapon visszaadott eredményeket jelenítik meg.

A `RecognizeAsync` metódus meghívja a paraméteres delegálást a `GetHeadersAsyncFunc` átadott `ComputerVisionClient.RecognizeTextInStreamAsync` vagy `ComputerVisionClient.RecognizeTextAsync` metódushoz, és megvárja a választ. A metódus ezután meghívja a `GetOperationUrlFunc` átadott delegált, hogy a válaszból beolvassa a `Operation-Location` válasz fejlécének értékét. Ez az érték a Computer Vision `GetHeadersAsyncFunc` átadott metódus eredményeinek lekéréséhez használt URL-cím.

A `RecognizeAsync` metódus ezután meghívja a `ComputerVisionClient.GetTextOperationResultAsync` metódust, átadja a `Operation-Location` válasz fejlécében lekért URL-címet, hogy lekérje az `GetHeadersAsyncFunc`által átadott metódus állapotát és eredményét. Ha az állapot nem jelzi, hogy a metódus befejeződött, sikeres vagy sikertelen volt, akkor a `RecognizeAsync` metódus többször is meghívja a `ComputerVisionClient.GetTextOperationResultAsync` 3-szor, a hívások között 3 másodpercet vár. A `RecognizeAsync` metódus visszaadja az eredményeket a meghívott metódusnak.

## <a name="explore-the-get-thumbnail-scenario"></a>A miniatűr beolvasása forgatókönyv megismerése

Ezt a forgatókönyvet a ThumbnailPage. XAML lap kezeli. Megadhatja, hogy az intelligens levágást kívánja-e használni, és megadja a kívánt magasságot és szélességet, a miniatűrt a rendszerképből, és a képet és az eredményeket is megtekintheti. A forgatókönyv oldal a következő metódusokat használja a rendszerkép forrásától függően:

* UploadAndThumbnailImageAsync  
  Ez a módszer helyi lemezképekhez használatos, amelyekben a lemezképet `Stream`ként kell kódolni, és a `ComputerVisionClient.GenerateThumbnailInStreamAsync` metódus meghívásával el kell juttatni a Computer Visionnak.
* ThumbnailUrlAsync  
  Ez a módszer távoli lemezképekhez használatos, amelyekben a lemezkép URL-címét a rendszer a `ComputerVisionClient.GenerateThumbnailAsync` metódus meghívásával elküldi Computer Vision.

A `UploadAndThumbnailImageAsync` metódus új `ComputerVisionClient` példányt hoz létre a megadott előfizetési kulcs és végpont URL-cím használatával. Mivel a minta alkalmazás egy helyi rendszerképet elemez, el kell küldenie a rendszerkép tartalmát a Computer Visionba. Ekkor megnyílik a `imageFilePath`ben megadott helyi fájl `Stream`ként való olvasáshoz. Meghívja a `ComputerVisionClient.GenerateThumbnailInStreamAsync` metódust, a szélességet, a magasságot, a fájl `Stream`ét, valamint azt, hogy az intelligens levágást használja-e, majd `Stream`ként adja vissza az eredményt. A `ImageScenarioPage` osztálytól örökölt metódusok a forgatókönyv lapon visszaadott eredményeket jelenítik meg.

A `RecognizeUrlAsync` metódus új `ComputerVisionClient` példányt hoz létre a megadott előfizetési kulcs és végpont URL-cím használatával. Meghívja a `ComputerVisionClient.GenerateThumbnailAsync` metódust, átadva a szélességet, a magasságot, a rendszerkép URL-címét, valamint azt, hogy az intelligens levágást használja-e, majd visszaadja az eredményt `Stream`ként. A `ImageScenarioPage` osztálytól örökölt metódusok a forgatókönyv lapon visszaadott eredményeket jelenítik meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje azt a mappát, amelybe a `Microsoft/Cognitive-Vision-Windows` adattárt klónozotta. Ha a minta lemezképek használatát választotta, törölje azt a mappát is, amelybe a `Microsoft/Cognitive-Face-Windows` tárházat klónozotta.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a Face Service szolgáltatással](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
