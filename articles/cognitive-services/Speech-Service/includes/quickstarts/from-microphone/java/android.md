---
title: 'Rövid útmutató: beszéd felismerése mikrofonból, Java (Android) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan ismerheti fel a beszédfelismerést Java nyelven Androidon a Speech SDK használatával
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: 6348d09351cf627624340083e2c419def38dfc01
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77445285"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md?tabs=android)
> * Győződjön meg arról, hogy van hozzáférése egy mikrofonhoz a hangrögzítéshez

## <a name="create-a-user-interface"></a>Felhasználói felület létrehozása

Most létrehozunk egy alapszintű felhasználói felületet az alkalmazáshoz. Kezdje el szerkeszteni a fő tevékenység (`activity_main.xml`) elrendezését. Kezdetben az elrendezés tartalmaz egy címsort az alkalmazás nevével, valamint egy TextView, amely tartalmazza a következő szöveget: ""Helló világ!"alkalmazás!".

* Válassza ki a TextView elemet. Módosítsa a jobb felső sarokban található ID (azonosító) attribútumot a következőre: `hello`.

* A `activity_main.xml` ablak bal felső részén található palettáról húzzon egy gombot a szöveg feletti üres helyre.

* A gomb attribútumai jobb oldalon láthatók. Az `onClick` attribútum értékét állítsa a következőre: `onSpeechButtonClicked`. Ezen a néven írni fogunk később egy metódust, amely kezelni fogja a gombeseményt. Módosítsa a jobb felső sarokban található ID (azonosító) attribútumot a következőre: `button`.

* A tervező felső részén található varázspálca ikonra kattintva kikövetkeztetheti az elrendezés korlátozásait.

  ![A varázspálca ikon képernyőképe](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

A felhasználói felület szöveg-és grafikus ábrázolásának ekkor a következőképpen kell kinéznie:

![Felhasználói felület](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Mintakód hozzáadása

1. Nyissa meg a `MainActivity.java` forrásfájlt. Cserélje le a fájl összes kódját a következőre:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Az `onCreate` metódus egy olyan kódot tartalmaz, amely mikrofon- és internet-engedélyeket kér, majd elindítja a natív platform kötését. A natív platform kötését csak egyszer kell elvégezni. Erre érdemes mihamarabb, már az alkalmazás inicializálása során sort keríteni.

   * Ahogy korábban már említettük, az `onSpeechButtonClicked` metódus kezeli azt, ha a gombra kattintanak. Egy gomb megnyomásával elindíthatja a beszédfelismerési szöveg átírását.

1. Ugyanabban a fájlban cserélje le a `YourSubscriptionKey` sztringet az előfizetői azonosítóra.

1. Cserélje le a `YourServiceRegion` karakterláncot az előfizetéséhez [tartozó régió](https://aka.ms/speech/sdkregion) - **azonosítóra** . Használja például az ingyenes próba-előfizetéshez `westus`.

## <a name="build-and-run-the-app"></a>Az alkalmazás létrehozása és futtatása

1. Csatlakoztassa az Android-eszközt a fejlesztői számítógéphez. Győződjön meg arról, hogy engedélyezve van a [fejlesztési mód és az USB-hibakeresés](https://developer.android.com/studio/debug/dev-options) az eszközön.

1. Az alkalmazás létrehozásához válassza a CTRL + F9 billentyűkombinációt, vagy válassza a **létrehozás** > a **projekt** létrehozása lehetőséget a menüsávon.

1. Az alkalmazás indításához válassza a SHIFT + F10 lehetőséget, vagy válassza a **futtatás** > **Futtatás az "app"** lehetőséget.

1. A megjelenő központi telepítési cél ablakban válassza ki az Android-eszközét.

   ![A Select Deployment Target (Üzembehelyezési cél kiválasztása) ablak képernyőképe](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

A beszédfelismerési szakasz elindításához kattintson az alkalmazásban a gombra. A következő 15 másodpercben kimondott (angol nyelvű) beszédet a rendszer elküldi a Speech Service-nek, amely írott szöveggé alakítja. Az eredmény az Android-alkalmazásban és az Android Studio logcat ablakában jelenik meg.

![Az Android-alkalmazás képernyőképe](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]

