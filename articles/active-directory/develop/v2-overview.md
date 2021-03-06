---
title: Microsoft Identity platform (v 2.0) – áttekintés – Azure
description: Ismerje meg a Microsoft Identity platform (v 2.0) végpontját és platformját.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f6953fb5d016c802db260c55bc4970c3f029ab1a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240827"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Microsoft Identity platform (v 2.0) – áttekintés

A Microsoft identitásplatformja az Azure Active Directory (Azure AD) fejlesztői platform fejlődésének eredménye. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat hozzanak létre, amelyek az összes Microsoft-identitást bejelentkeznek, és a Microsoft API-k, például Microsoft Graph vagy a fejlesztők által készített API-k hívását kérik. A Microsoft Identity platform a következőkből áll:

- **OAuth 2,0 és OpenID Connect standard-kompatibilis hitelesítési szolgáltatás** , amely lehetővé teszi a fejlesztők számára bármely Microsoft-identitás hitelesítését, beleértve a következőket:
  - Munkahelyi vagy iskolai fiókok (az Azure AD-n keresztül kiépítve)
  - Személyes Microsoft-fiókok (például Skype, Xbox és Outlook.com)
  - Közösségi vagy helyi fiókok (Azure AD B2Con keresztül)
- **Nyílt forráskódú kódtárak**: Microsoft Authentication librarys (MSAL) és más szabványoknak megfelelő kódtárak támogatása
- **Application Management Portal**: a Azure Portal beépített regisztrációs és konfigurációs élmény, valamint az összes többi Azure-felügyeleti lehetőség.
- Az **Application CONFIGURATION API és a PowerShell**: lehetővé teszi az alkalmazások programozott konfigurálását az Microsoft Graph API és a PowerShell segítségével, így automatizálhatja a DevOps feladatait.
- **Fejlesztői tartalom**: elméleti és hivatkozási dokumentáció, gyors üzembe helyezési minták, mintakód-minták, oktatóanyagok és útmutatók.

A fejlesztők számára a Microsoft Identity platform zökkenőmentesen integrálható az identitások és a biztonság terén, például a jelszó nélküli hitelesítéssel, a lépésenkénti hitelesítéssel és a feltételes hozzáféréssel.  Nincs szükség ilyen funkciók megvalósítására: a Microsoft Identity platformmal integrált alkalmazások natív módon kihasználhatják ezeket az innovációkat.

A Microsoft Identity platformmal egyszer is írhat kódot, és elérheti a felhasználókat. Egyszerre létrehozhat egy alkalmazást, és több platformon is dolgozhat, vagy olyan alkalmazást hozhat létre, amely ügyfélként, valamint erőforrás-alkalmazásként (API) működik.

## <a name="getting-started"></a>Bevezetés

Nem kell, hogy az identitások használata nehéz legyen. 

Tekintse meg a [Microsoft Identity platform videóját](identity-videos.md) , ahol megismerheti az alapvető tudnivalókat. 

Válasszon egy olyan [forgatókönyvet](authentication-flows-app-scenarios.md) , amely Önre vonatkozik – az egyes forgatókönyvek elérési útja egy rövid útmutató és egy áttekintő oldal, amellyel percek alatt elvégezheti a működést:

- [Egyoldalas alkalmazás létrehozása](scenario-spa-overview.md)
- [Felhasználók számára bejelentkező Webalkalmazás létrehozása](scenario-web-app-sign-user-overview.md)
- [Webes API-kat meghívó Webalkalmazás létrehozása](scenario-web-app-call-api-overview.md)
- [Védett webes API létrehozása](scenario-protected-web-api-overview.md)
- [Webes API-kat meghívó webes API létrehozása](scenario-web-api-call-api-overview.md)
- [Asztali alkalmazás készítése](scenario-desktop-overview.md)
- [Daemon-alkalmazás létrehozása](scenario-daemon-overview.md)
- [Mobile-alkalmazás létrehozása](scenario-mobile-overview.md)

Az alábbi táblázat a gyakori hitelesítési alkalmazási forgatókönyveket ismerteti – a Microsoft Identity platform és az alkalmazás integrálására szolgáló hivatkozásként használja.

[Alkalmazás-forgatókönyvek ![a Microsoft Identity platformon](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az alapvető hitelesítési fogalmakról, javasoljuk, hogy kezdje a következő témakörökkel:

- [Hitelesítési folyamatok és alkalmazási helyzetek](authentication-flows-app-scenarios.md)
- [Hitelesítési alapok](authentication-scenarios.md)
- [Alkalmazás-és egyszerű szolgáltatások](app-objects-and-service-principals.md)
- [Közönség](v2-supported-account-types.md)
- [Engedélyek és beleegyezik](v2-permissions-and-consent.md)
- [Azonosító jogkivonatok](id-tokens.md) és [hozzáférési jogkivonatok](access-tokens.md)

Hozzon létre egy olyan adatgazdag alkalmazást, amely meghívja az [Microsoft Graph](https://docs.microsoft.com/graph/overview).

Ha készen áll az alkalmazás **éles környezetbe**való elindítására, tekintse át az alábbi ajánlott eljárásokat:

- [Engedélyezze a naplózást](msal-logging.md) az alkalmazásban.
- Telemetria engedélyezése az alkalmazásban.
- [Proxyk engedélyezése és a http-ügyfelek testreszabása](msal-net-provide-httpclient.md).
- Tesztelje az integrációt a [Microsoft Identity platform Integration ellenőrzőlista](identity-platform-integration-checklist.md)követésével.

## <a name="learn-more"></a>További információk

Ha olyan ügyfél-alkalmazást szeretne létrehozni, amely a közösségi és a helyi identitásokban jelentkezik be, tekintse meg a [Azure ad B2C áttekintést](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).
