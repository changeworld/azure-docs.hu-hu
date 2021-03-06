---
title: JavaScript egyoldalas alkalmazás forgatókönyve – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazást a Microsoft Identity platform használatával (forgatókönyv áttekintése).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b430778bed811656b5c8aadc75ba3cf35917f737
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701875"
---
# <a name="scenario-single-page-application"></a>Forgatókönyv: egyoldalas alkalmazás

Ismerje meg az egyoldalas alkalmazások (SPA) létrehozásához szükséges tudnivalókat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Első lépések

Az első alkalmazást az alábbi JavaScript SPA-útmutató segítségével hozhatja létre:

> [!div class="nextstepaction"]
> [Rövid útmutató: egyoldalas alkalmazás](./quickstart-v2-javascript.md)

## <a name="overview"></a>Áttekintés

Számos modern webalkalmazás úgy van kialakítva, mint az ügyféloldali egyoldalas alkalmazások. A fejlesztők a JavaScript vagy a SPA-keretrendszer, például a szögletes, a Vue. js és a reakciós. js használatával írhatják őket. Ezek az alkalmazások egy webböngészőben futnak, és különböző hitelesítési jellemzőkkel rendelkeznek, mint a hagyományos kiszolgálóoldali webes alkalmazások. 

A Microsoft Identity platform lehetővé teszi, hogy az egyoldalas alkalmazások bejelentkezzenek a felhasználókba, és jogkivonatokat kérjenek a háttér-szolgáltatások vagy webes API-k elérésére a [OAuth 2,0 implicit folyamat](./v2-oauth2-implicit-grant-flow.md)használatával. Az implicit folyamat lehetővé teszi, hogy az alkalmazás azonosító jogkivonatokat kapjon a hitelesített felhasználó képviseletére, valamint a védett API-k meghívásához szükséges jogkivonatok elérésére is.

![Egyoldalas alkalmazások](./media/scenarios/spa-app.svg)

Ez a hitelesítési folyamat nem tartalmaz olyan alkalmazási helyzeteket, amelyek platformfüggetlen JavaScript-keretrendszereket használnak, például az Electron-et és a reakciós Natívt. További képességeket igényelnek a natív platformokkal való interakcióhoz.

## <a name="specifics"></a>Sajátosságai

Ha ezt a forgatókönyvet szeretné engedélyezni az alkalmazásához, a következőkre lesz szüksége:

* Az alkalmazás regisztrálása Azure Active Directory (Azure AD) szolgáltatással. Ez a regisztráció magában foglalja az implicit folyamat engedélyezését és egy átirányítási URI-t, amely a visszaadott tokeneket adja vissza.
* Alkalmazás konfigurációja a regisztrált alkalmazás tulajdonságaival, például az alkalmazás azonosítójával.
* A Microsoft Authentication Library (MSAL) használatával jelentkezzen be a hitelesítési folyamatba, és szerezzen be jogkivonatokat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-spa-app-registration.md)
