---
title: Hibák elhárítása Azure Active Directory jelentési API-ban | Microsoft Docs
description: A Azure Active Directory jelentési API-k meghívása során felmerülő hibák megoldását teszi lehetővé.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d1fb4f49e4f9ad41f971d869873200e6180b5cd
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399293"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Hibák elhárítása Azure Active Directory jelentési API-ban

Ez a cikk azokat a gyakori hibaüzeneteket sorolja fel, amelyekkel a Microsoft Graph API-val és a megoldásuk lépéseivel férhet hozzá a tevékenységek jelentéseihez.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 HTTP belső kiszolgálóhiba a Microsoft Graph v2-végpont elérésekor

Jelenleg nem támogatjuk a Microsoft Graph v2-végpontot – ügyeljen arra, hogy a Microsoft Graph v1-végpont használatával hozzáférhessen a tevékenység naplóihoz.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Hiba: egyik bérlő sem a B2C, sem a bérlő nem rendelkezik prémium szintű licenccel

A bejelentkezési jelentések eléréséhez Azure Active Directory Premium 1 (P1) licenc szükséges. Ha ez a hibaüzenet jelenik meg a bejelentkezések elérésekor, győződjön meg arról, hogy a bérlője Azure AD P1 licenccel rendelkezik.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Hiba: a felhasználó nem szerepel az engedélyezett szerepkörökben 

Ha ez a hibaüzenet akkor jelenik meg, amikor az API-val megpróbál hozzáférni a naplókhoz vagy a bejelentkezésekhez, győződjön meg arról, hogy a fiókja a Azure Active Directory-bérlő **biztonsági olvasójának** vagy a **jelentéskészítő olvasó** szerepkörének része. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Hiba: az alkalmazásból hiányzik a "HRE olvasása" engedély. 

Kövesse az előfeltételekben ismertetett lépéseket a [Azure Active Directory jelentési API eléréséhez](howto-configure-prerequisites-for-reporting-api.md) , hogy az alkalmazás a megfelelő engedélyekkel legyen fut. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Hiba: az alkalmazás hiányzik Microsoft Graph API "a naplózási napló összes adatszolgáltatásának olvasása" engedély

Kövesse az előfeltételekben ismertetett lépéseket a [Azure Active Directory jelentési API eléréséhez](howto-configure-prerequisites-for-reporting-api.md) , hogy az alkalmazás a megfelelő engedélyekkel legyen fut. 

## <a name="next-steps"></a>További lépések

[Használja a naplózási API-referenciát](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
[használja a bejelentkezési tevékenység jelentésének API-referenciáját](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
