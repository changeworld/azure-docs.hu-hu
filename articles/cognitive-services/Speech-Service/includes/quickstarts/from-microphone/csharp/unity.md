---
title: 'Rövid útmutató: beszéd felismerése mikrofonból C# , (Unity) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: be8497cb07bd3c761c59bfc55e773b5dcc937c50
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925936"
---
> [!NOTE]
> Az Unity Speech SDK támogatja a Windows Desktopot (x86 és x64) vagy Univerzális Windows-platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) és iOS (x64 Simulator, ARM32 és ARM64)

## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](../../../../quickstarts/setup-platform.md?tabs=unity)
> * Győződjön meg arról, hogy van hozzáférése egy mikrofonhoz a hangrögzítéshez

Ha már megtette ezt, remek. Folytasd a munkát.

## <a name="create-a-unity-project"></a>Unity-projekt létrehozása

1. Nyissa meg az egységet. Ha első alkalommal használja az egységet, megjelenik az **Unity Hub** *<version number>* ablak. (Az Unity hub-t közvetlenül is megnyithatja az ablak megnyitásához.)

   [![Unity hub ablak](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. Válassza az **Új** lehetőséget. Megjelenik az **új projekt létrehozása az Unity** *<version number>* ablakban.

   [új projekt létrehozása az Unity hub-ban ![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. A **Project Name (projekt neve**) mezőben adja meg a **csharp-Unity**értéket.
1. A **sablonokban**, ha a **3D** még nincs kiválasztva, jelölje ki.
1. A **hely**mezőben válasszon ki vagy hozzon létre egy mappát, amelybe menteni szeretné a projektet.
1. Kattintson a **Létrehozás** gombra.

Egy kis idő elteltével megjelenik az Unity Editor ablak.



## <a name="add-ui"></a>Felhasználói felület hozzáadása

Most vegyünk fel egy minimális felhasználói felületet a színtérbe. Ez a felhasználói felület egy gombból áll, amely elindítja a beszédfelismerést és egy szöveges mezőt az eredmény megjelenítéséhez. A [ **hierarchia** ablakban](https://docs.unity3d.com/Manual/Hierarchy.html)megjelenik egy minta jelenet, amely az új projekttel létrehozott egységet mutatja.

1. A **hierarchia** ablak felső részén válassza a **Létrehozás** > **felhasználói felület** > **gombra**.

   Ez a művelet három játék objektumot hoz létre, amelyek a **hierarchia** ablakban láthatók: egy **gomb** objektum, egy **vászon** objektum, amely tartalmazza a gombot, valamint egy **EventSystem** objektumot.

   [![Unity Editor-környezet](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [Navigáljon a **jelenet** ](https://docs.unity3d.com/Manual/SceneViewNavigation.html) nézetbe, hogy jól látható legyen a vászon és a gomb a [ **jelenet** nézetben](https://docs.unity3d.com/Manual/UsingTheSceneView.html).

1. A [ **felügyelő** ablakban](https://docs.unity3d.com/Manual/UsingTheInspector.html) (alapértelmezés szerint a jobb oldalon) állítsa a **POS X** és a **POS Y** tulajdonságokat **0-ra**, így a gomb középre van állítva a vászon közepén.

1. A **hierarchia** ablakban válassza a **Létrehozás** > **felhasználói felület** > **szöveg** lehetőséget egy **szöveges** objektum létrehozásához.

1. A **felügyelő** ablakban állítsa a **POS X** és a **pos Y** tulajdonságokat **0** és **120**értékre, majd állítsa a **szélesség** és a **magasság** tulajdonságokat **240** és **120**értékre. Ezek az értékek biztosítják, hogy a szöveg mező és a gomb ne legyen átfedésben.

Ha elkészült, a **jelenet** nézetnek a következő képernyőképhez hasonlóan kell kinéznie:

[![jelenet nézet az Unity Editorban](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>A mintakód hozzáadása

A következő lépésekkel adhatja hozzá a minta parancsfájl kódját az Unity projekthez:

1. Új C# parancsfájl hozzáadásához a [projekt ablakban](https://docs.unity3d.com/Manual/ProjectView.html)válassza a létrehozás >  **C# parancsfájl** **létrehozása** lehetőséget.

   [![projekt ablak az Unity Editorban](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. Nevezze el a szkriptet `HelloWorld`.

1. Az újonnan létrehozott szkript szerkesztéséhez kattintson duplán a `HelloWorld` elemre.

   > [!NOTE]
   > Ha úgy szeretné beállítani a Kódszerkesztő használatát, hogy az Unity a szerkesztéshez használni tudja, válassza a > **Beállítások** **szerkesztése** lehetőséget, majd lépjen a **külső eszközök** beállításaihoz. További információ: [Unity felhasználói kézikönyv](https://docs.unity3d.com/Manual/Preferences.html).

1. Cserélje le a meglévő parancsfájlt a következő kódra:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. Keresse meg és cserélje le a `YourSubscriptionKey` karakterláncot a beszédfelismerési szolgáltatás előfizetési kulcsával.

1. Keresse meg és cserélje le a `YourServiceRegion` karakterláncot az előfizetéséhez [tartozó régió](https://aka.ms/speech/sdkregion) - **azonosítóval** . Ha például az ingyenes próbaverziót használja, akkor a régió a `westus`.

1. Mentse a parancsfájl módosításait.

Most térjen vissza az Unity Editorhoz, és adja hozzá a parancsfájlt összetevőként az egyik játék-objektumhoz:

1. A **hierarchia** ablakban válassza ki a **vászon** objektumot.

1. Az **ellenőr** ablakban kattintson az **összetevő hozzáadása** gombra.

   [![Inspector ablak az Unity Editorban](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. A legördülő listában keresse meg a fent létrehozott `HelloWorld`-parancsfájlt, és adja hozzá. A **"Helló világ!" alkalmazás (szkript)** szakasz a **felügyelő** ablakban jelenik meg, amely két nem inicializált tulajdonságot, a **kimeneti szöveget** és a **kiindulási gombot**tartalmazza. Ezek az egység-összetevők tulajdonságai megegyeznek a `HelloWorld` osztály nyilvános tulajdonságaival.

1. Jelölje be a kiválasztó **gomb** tulajdonság objektumának kiválasztása (a tulajdonság jobb oldalán lévő kis kör ikon), és válassza ki a korábban létrehozott **gomb** objektumot.

1. Válassza ki a **kimeneti szöveg** tulajdonság objektum-választóját, és válassza ki a korábban létrehozott **szöveges** objektumot.

   > [!NOTE]
   > A gomb beágyazott szöveges objektummal is rendelkezik. Ügyeljen arra, hogy a szöveg kimenete ne legyen véletlenül kiválasztva (vagy nevezze át az egyik szöveges objektumot a **felügyelő** ablak **név** mezőjéből a félreértések elkerülése érdekében).

## <a name="run-the-application-in-the-unity-editor"></a>Az alkalmazás futtatása az Unity Editorban

Most már készen áll az alkalmazás futtatására az Unity Editoron belül.

1. Az Unity Editor eszköztárán (a menüsáv alatt) válassza a **Lejátszás** gombot (egy jobb oldali háromszög).

1. Nyissa meg a [ **játék** nézetet](https://docs.unity3d.com/Manual/GameView.html), és várja meg, amíg a **text** objektum a **beszédfelismerés felismeréséhez kattintson a gombra**. ( **Új szöveget** jelenít meg, ha az alkalmazás nem indult el, vagy nem áll készen a válaszadásra.)

1. Kattintson a gombra, és beszéljen egy angol kifejezéssel vagy mondattal a számítógép mikrofonjában. A beszéd a beszédfelismerési szolgáltatáshoz lett továbbítva, és szövegbe kerül, amely a **játék** nézetben jelenik meg.

   [![játék nézet az Unity Editorban](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. A hibakeresési üzenetekért tekintse meg a [ **konzol** ablakát](https://docs.unity3d.com/Manual/Console.html) . Ha a **konzolablak** nem jelenik meg, nyissa meg a menüsávot, és válassza az **ablak** > az **általános** > **konzol** lehetőséget a megjelenítéshez.

1. Ha elvégezte a beszédfelismerést, válassza a **Lejátszás** gombot az Unity Editor eszköztárán az alkalmazás leállításához.

## <a name="additional-options-to-run-this-application"></a>További lehetőségek az alkalmazás futtatásához

Ez az alkalmazás Android-alkalmazásként, Windows önálló alkalmazásként vagy UWP alkalmazásként is üzembe helyezhető.
További információkért tekintse meg a [minta tárházat](https://aka.ms/csspeech/samples). A `quickstart/csharp-unity` mappa ismerteti ezen további célok konfigurációját.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]
