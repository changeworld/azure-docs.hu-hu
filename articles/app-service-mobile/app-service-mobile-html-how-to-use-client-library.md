---
title: A JavaScript SDK használata
description: A v használata az Azure-Mobile Apps
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 029b01f3aacc928ebdae0e8fe90871437afccea5
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461521"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Az Azure-hoz készült JavaScript ügyféloldali kódtár használata Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan végezheti el az [Az Azure-hoz készült JavaScript SDK Mobile Apps]-t használó gyakori forgatókönyveket. Ha még nem ismeri az Azure Mobile Appst, először fejezze be az [azure Mobile Apps gyorskonfigurálás] egy háttérrendszer létrehozásához és egy tábla létrehozásához. Ebben az útmutatóban a mobil háttérrendszer használatára koncentrálunk a HTML/JavaScript webalkalmazásokban.

## <a name="supported-platforms"></a>Támogatott platformok
A böngésző támogatását a főbb böngészők aktuális és utolsó verzióira korlátozzák: a Google Chrome, a Microsoft Edge, a Microsoft Internet Explorer és a Mozilla Firefox.  Elvárjuk, hogy az SDK működjön a viszonylag modern böngészővel.

A csomag univerzális JavaScript-modulként van elosztva, így a globális, az AMD-és a CommonJS-formátumok is támogatottak.

## <a name="Setup"></a>Telepítés és előfeltételek
Ez az útmutató azt feltételezi, hogy létrehozott egy táblázatot tartalmazó hátteret. Ez az útmutató azt feltételezi, hogy a tábla ugyanazzal a sémával rendelkezik, mint az oktatóanyagokban szereplő táblák.

Az Azure Mobile Apps JavaScript SDK telepítése a `npm` parancs használatával végezhető el:

```
npm install azure-mobile-apps-client --save
```

A könyvtár ES2015-modulként is használható CommonJS-környezetekben, például a Browserify és a webpackben, valamint egy AMD-könyvtárként.  Például:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Az SDK előre összeállított verzióját úgy is használhatja, hogy közvetlenül a CDN-ből letölti a következőt:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Útmutató: felhasználók hitelesítése
Azure App Service támogatja az alkalmazások felhasználóinak hitelesítését és engedélyezését különféle külső identitás-szolgáltatók használatával: Facebook, Google, Microsoft-fiók és Twitter. A táblákra vonatkozó engedélyeket úgy állíthatja be, hogy az adott műveletekhez való hozzáférést csak a hitelesített felhasználókra korlátozza. A hitelesített felhasználók identitását is használhatja a kiszolgálói parancsfájlok engedélyezési szabályainak megvalósításához. További információ: Ismerkedés [Ismerkedés a hitelesítéssel] oktatóanyaggal.

A rendszer két hitelesítési folyamatot támogat: a kiszolgáló és az ügyfél folyamatát.  A kiszolgálói folyamat biztosítja a legegyszerűbb hitelesítési felületet, mivel a szolgáltató webes hitelesítési felületén alapul. Az ügyféloldali folyamat lehetővé teszi az eszközre jellemző képességekkel való mélyebb integrációt, például az egyszeri bejelentkezést, mivel a szolgáltatói SDK-k alapján működik.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Útmutató: Mobil App Service konfigurálása külső átirányítási URL-címekhez.
A JavaScript-alkalmazások számos típusa visszacsatolási képességet használ a OAuth felhasználói felületi folyamatainak kezeléséhez.  Ezek a képességek a következők:

* A szolgáltatás helyi futtatása
* Élő reload használata az ionos keretrendszerrel
* App Service hitelesítésre való átirányítása.

A helyileg futtatott alkalmazások problémát okozhatnak, mivel alapértelmezés szerint a App Service hitelesítés csak úgy van konfigurálva, hogy engedélyezze a hozzáférést a Mobile apps-háttérrel. A következő lépésekkel módosíthatja a App Service beállításait a hitelesítés engedélyezéséhez a kiszolgáló helyi futtatásakor:

1. Jelentkezzen be az [Azure Portalra]
2. Navigáljon a Mobile apps-háttérbe.
3. Válassza ki az **erőforrás-kezelőt** a **fejlesztői eszközök** menüben.
4. Kattintson az **Ugrás** gombra a Mobile apps-backend erőforrás-kezelőjének új lapon vagy ablakban való megnyitásához.
5. Bontsa ki a **config** > **authsettings elemre** csomópontot az alkalmazáshoz.
6. Kattintson a **Szerkesztés** gombra az erőforrás szerkesztésének engedélyezéséhez.
7. Keresse meg az **allowedExternalRedirectUrls** elemet, amelynek null értékűnek kell lennie. URL-címek hozzáadása tömbben:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Cserélje le a tömbben lévő URL-címeket a szolgáltatás URL-címeire, amely ebben a példában `http://localhost:3000` a helyi Node. js-minta szolgáltatáshoz. Az alkalmazás konfigurálásának módjától függően `http://localhost:4400` is használhat a lüktető szolgáltatáshoz vagy valamilyen más URL-címhez.
8. A lap tetején kattintson az **írás/írás**elemre, majd kattintson a **put** elemre a frissítések mentéséhez.

Ugyanezeket a visszacsatolási URL-címeket is hozzá kell adnia a CORS engedélyezési listához:

1. Váltson vissza a [Azure Portalra].
2. Navigáljon a Mobile apps-háttérbe.
3. Az **API** menüben kattintson a **CORS** elemre.
4. Adja meg az összes URL-címet az üres **engedélyezett Origins** szövegmezőben.  Létrejön egy új szövegmező.
5. Kattintson a **Mentés** gombra

A háttérbeli frissítések után az új visszacsatolási URL-címeket is használhatja az alkalmazásban.

<!-- URLs. -->
[Azure Mobile Apps gyorskonfigurálás]: app-service-mobile-cordova-get-started.md
[Ismerkedés a hitelesítéssel]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure Portalra]: https://portal.azure.com/
[Az Azure-hoz készült JavaScript SDK Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
