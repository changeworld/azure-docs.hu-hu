---
title: Ismerkedés az Azure AD-vel a Visual Studio WebApi projektjeiben
description: A Azure Active Directory használatának első lépései a WebApi-projektekben az Azure AD-vel való csatlakozás vagy a Visual Studio csatlakoztatott szolgáltatásaival való létrehozása után
author: ghogen
manager: jillfra
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 33894e237144628634c3b63393130eb0af5b9877
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159454"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Ismerkedés a Azure Active Directoryrel (WebApi-projektek)

> [!div class="op_single_selector"]
> - [Első lépések](vs-active-directory-webapi-getting-started.md)
> - [mi történt](vs-active-directory-webapi-what-happened.md)

Ez a cikk további útmutatást nyújt, miután hozzáadta Active Directoryt egy ASP.NET WebAPI projekthez a Visual Studio **project > Connected Services** parancsával. Ha még nem adta hozzá a szolgáltatást a projekthez, bármikor megteheti.

Megtudhatja [, mi történt a WebAPI-projekttel?](vs-active-directory-webapi-what-happened.md) a csatlakoztatott szolgáltatás hozzáadásakor a projekten végrehajtott módosítások.

## <a name="requiring-authentication-to-access-controllers"></a>Hitelesítés megkövetelése a hozzáférés-vezérléshez

A projekt összes vezérlőjét a `[Authorize]` attribútummal díszítették. Ez az attribútum megköveteli a felhasználó hitelesítését, mielőtt hozzáfér az ezen vezérlők által definiált API-khoz. Annak engedélyezéséhez, hogy a vezérlő névtelenül legyen elérhető, távolítsa el ezt az attribútumot a vezérlőből. Ha az engedélyeket részletesebben szeretné beállítani, alkalmazza az attribútumot minden olyan metódusra, amely az engedélyezést igényli a vezérlő osztályra való alkalmazása helyett.

## <a name="next-steps"></a>Következő lépések

- [Hitelesítési forgatókönyvek Azure Active Directory](authentication-scenarios.md)
- [Bejelentkezés felvétele a Microsofttal egy ASP.NET-webalkalmazásba](quickstart-v2-aspnet-webapp.md)
