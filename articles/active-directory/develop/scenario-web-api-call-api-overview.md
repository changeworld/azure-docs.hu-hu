---
title: Webes API-kat meghívó webes API létrehozása – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan hozhat létre olyan webes API-t, amely meghívja az alárendelt webes API-kat (áttekintés).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 467ff2f789cc83bc5651d831838da0b5c922c839
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701739"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Forgatókönyv: webes API-kat meghívó webes API

Ismerje meg, hogy mit kell tudnia a webes API-kat meghívó webes API-k létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

Ez a forgatókönyv, amelyben a védett webes API meghívja a webes API-kat, a "webes API-k védelme" forgatókönyvre épül. Ha többet szeretne megtudni erről az alapvető forgatókönyvről, tekintse meg a [forgatókönyv: védett webes API](scenario-protected-web-api-overview.md)című témakört.

## <a name="overview"></a>Áttekintés

- A webes, asztali, mobil vagy egyoldalas alkalmazás ügyfélprogram (amely nem szerepel a csatolt ábrán) meghívja a védett webes API-t, és egy JSON Web Token (JWT) tulajdonosi jogkivonatot biztosít az "engedélyezési" HTTP-fejlécben.
- A védett webes API érvényesíti a jogkivonatot, és a Microsoft Authentication Library (MSAL) `AcquireTokenOnBehalfOf` metódust használja a Azure Active Directory (Azure AD) egy másik jogkivonatának igényléséhez, hogy a védett webes API meghívhat egy második webes API-t, vagy egy alárendelt webes API-t a felhasználó nevében.
- A védett webes API a `AcquireTokenSilent`később is meghívhatja, hogy az ugyanazon felhasználó nevében más alsóbb rétegbeli API-kra is igényeljen jogkivonatokat. `AcquireTokenSilent` szükség esetén frissíti a tokent.

![Webes API-t hívó webes API diagramja](media/scenarios/web-api.svg)

## <a name="specifics"></a>Sajátosságai

Az API-engedélyekhez kapcsolódó alkalmazás-regisztrációs rész klasszikus. Az alkalmazás konfigurációja magában foglalja a OAuth 2,0-as verziójának használatát a JWT tulajdonosi jogkivonatának az alsóbb rétegbeli API-hoz való cseréjéhez. Ezt a tokent a rendszer hozzáadja a jogkivonat-gyorsítótárhoz, ahol elérhető a webes API vezérlői között, és a tokent az alsóbb rétegbeli API-k meghívásához csendesen is beszerezheti.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-web-api-call-api-app-registration.md)
