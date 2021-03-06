---
title: Válassza ki a Visual Studióval és az Azure-szolgáltatásokkal rendelkező ügyfélalkalmazások létrehozására szolgáló előtér-fejlesztési platformot
description: Ismerkedjen meg a támogatott natív és platformfüggetlen nyelvekkel az ügyfélalkalmazások létrehozásához.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: c55c9c36d84c8545f4192e1c9a65d8af0c580396
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453151"
---
# <a name="choose-mobile-development-frameworks"></a>Mobile Development keretrendszerek kiválasztása
A fejlesztők az ügyféloldali technológiák használatával saját maguk hozhatnak létre mobil alkalmazásokat a platformok közötti megközelítéshez meghatározott keretrendszerek és minták használatával. Döntési tényezők alapján a fejlesztők a következőket hozhatják létre:
- Natív egyplatformos alkalmazások olyan nyelvekkel, mint az Objective C és a Java
- Platformfüggetlen alkalmazások a Xamarin, a .NET és aC#
- Hibrid alkalmazások a Cordova és a hozzá tartozó változatok használatával

## <a name="native-platforms"></a>Natív platformok
A natív alkalmazások létrehozásához platform-specifikus programozási nyelvek, SDK-k, fejlesztési környezetek és az operációs rendszer gyártói által biztosított egyéb eszközök szükségesek.

### <a name="ios"></a>iOS
Az Apple hozta létre és fejlesztette ki, az iOS használatával alkalmazásokat hozhat létre az Apple-eszközökön, például az iPhone-on és az iPaden.

- **Programozási nyelvek**: Objective-C, Swift
- **Ide**: Xcode
- **SDK**: iOS SDK

### <a name="android"></a>Android
A Google és a világon a legnépszerűbb operációs rendszer által tervezett Android használatával olyan alkalmazások hozhatók létre, amelyek számos okostelefonon és tablettán futtathatók.

- **Programozási nyelv**: Java, Kotlin 
- **Ide**: Android Studio és androidos fejlesztői eszközök 
- **SDK**: Android SDK

### <a name="windows"></a>Windows
- **Programozási nyelv**:C#
- **Ide**: Visual Studio, Visual Studio Code
- **SDK**: Windows SDK

#### <a name="pros"></a>Előnyök
- Jó felhasználói élmény
- Rugalmas alkalmazások nagy teljesítményű és a natív kódtárakkal való kapcsolati képességgel
- Nagyon biztonságos alkalmazások

#### <a name="cons"></a>Hátrányok
- Az alkalmazás csak egy platformon fut
- További fejlesztői erőforrás-igényes és költséges alkalmazások készítése
- Alacsonyabb kódok újrafelhasználása

## <a name="cross-platforms-and-hybrid-applications"></a>Platformok közötti és hibrid alkalmazások
A platformfüggetlen alkalmazások lehetővé teszi, hogy a natív Mobile-alkalmazásokat egyszer, a kód megosztásával és iOS, Android és Windows rendszeren futtassa.

### <a name="xamarin"></a>Xamarin
A Microsoft tulajdonában álló [Xamarin](https://visualstudio.microsoft.com/xamarin/) robusztus, platformfüggetlen mobil alkalmazások létrehozására használható C#. A Xamarin rendelkezik egy, a számos platformon, például iOS-en, Androidon és Windowson működő futtatókörnyezettel és futtatókörnyezettel. Emellett olyan natív (nem értelmezett) alkalmazásokat is lefordít, amelyek nagy teljesítményt biztosítanak. A Xamarin egyesíti a natív platformok összes képességeit, és számos hatékony funkciót biztosít.

- **Programozási nyelv**:C#
- **Ide**: Visual Studio Windows vagy Mac rendszeren

### <a name="react-native"></a>React Native
A Facebook a 2015-ben megjelent, a [Native](https://facebook.github.io/react-native/) egy [nyílt forráskódú](https://github.com/facebook/react-native) JavaScript-keretrendszer, amely valós, natív módon teszi elérhetővé az iOS és az Android rendszerhez készült mobil alkalmazásokat. A felhasználói felületek létrehozásához a Facebook JavaScript-könyvtára reagál. A böngésző célzása helyett a mobil platformokat célozza meg. A natív reagálás a webes összetevők helyett natív összetevőket használ építőelemeként.
 
- **Programozási nyelv**: JavaScript
- **Ide**: Visual Studio Code

### <a name="unity"></a>Unity
 Az Unity egy olyan motor, amely a játékok létrehozására lett optimalizálva. A segítségével kiváló minőségű 2D-vagy 3D-alkalmazásokat készíthet a platformokhoz C# , például a Windows, az iOS, az Android és az Xbox rendszerekhez.

### <a name="cordova"></a>Cordova
A Cordova lehetővé teszi hibrid alkalmazások összeállítását az Apache Cordova vagy a Visual Studio Code segítségével a Cordova-hez készült Visual Studio Tools használatával. A hibrid megközelítéssel megoszthatja az összetevőket a webhelyekkel, és újrahasznosíthatja a webkiszolgáló-alapú alkalmazásokat a Cordova alapján üzemeltetett webalkalmazási módszerekkel.

#### <a name="pros"></a>Előnyök
- Nagyobb programkód-használhatóság a több platformon egy kód létrehozásával
- Több platformon is kiszolgálhatja a szélesebb közönséget
- Drámai csökkenés a fejlesztési időszakban
- Könnyen elindítható és frissíthető

#### <a name="cons"></a>Hátrányok
- Alacsonyabb teljesítmény
- Rugalmasság hiánya
- Minden platform tartalmaz egy egyedi szolgáltatást és funkciót, hogy a natív alkalmazás még kreatív legyen
- Megnövelt felhasználói felület tervezésének ideje
- Eszköz korlátozása
