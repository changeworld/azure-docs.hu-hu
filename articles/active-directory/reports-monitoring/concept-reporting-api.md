---
title: Ismerkedés az Azure AD Reporting API-val | Microsoft Docs
description: Ismerkedés a Azure Active Directory jelentési API-val
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56f240a5191dd483f89889f3ffe13b1819ca1e53
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399316"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Ismerkedés a Azure Active Directory Reporting API-val

Azure Active Directory számos [jelentést](overview-reports.md)biztosít, amelyek hasznos információkat tartalmaznak az olyan alkalmazásokhoz, mint például a Siem-rendszerek, az audit és az üzleti intelligencia-eszközök. 

Az Azure AD-jelentések Microsoft Graph API-jának használatával programozott hozzáférést nyerhet az adatokhoz REST-alapú API-k segítségével. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat.

Ez a cikk áttekintést nyújt a jelentéskészítési API-ról, beleértve az elérésének módjait.

Ha problémákba ütközik, tekintse meg a [Azure Active Directory támogatásának beszerzése](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Ha a jelentéskészítési API-t felhasználói beavatkozás nélkül vagy anélkül szeretné elérni, a következőket kell tennie:

1. Szerepkörök kiosztása (biztonsági olvasó, biztonsági rendszergazda, globális rendszergazda)
2. Egy alkalmazás regisztrálása
3. Engedélyek megadása
4. Konfigurációs beállítások összegyűjtése

Részletes útmutatást a [Azure Active Directory jelentési API elérésének előfeltételei](howto-configure-prerequisites-for-reporting-api.md)című témakörben talál. 

## <a name="api-endpoints"></a>API-végpontok 

A naplók Microsoft Graph API-végpontja `https://graph.microsoft.com/beta/auditLogs/directoryAudits`, és a bejelentkezések Microsoft Graph API-végpontja `https://graph.microsoft.com/beta/auditLogs/signIns`. További információkért tekintse meg a [naplózási API-referenciát](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) és a [bejelentkezési API-referenciát](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn).

Emellett az [Identity Protection kockázati észlelések API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) -val programozási hozzáférést nyerhet a biztonsági észlelésekhez Microsoft Graph használatával. További információ: Ismerkedés [a Azure Active Directory Identity Protection és Microsoft Graphával](../identity-protection/graph-get-started.md). 
  
## <a name="apis-with-microsoft-graph-explorer"></a>API-k Microsoft Graph Explorerrel

A bejelentkezési és a naplózási API-adatai a [Microsoft Graph Explorerrel](https://developer.microsoft.com/graph/graph-explorer) ellenőrizhetők. Ügyeljen arra, hogy jelentkezzen be a fiókjába a Graph Explorer felhasználói felületének mindkét bejelentkezési gombján, és állítsa be a **AuditLog. Read. All** és a **Directory. Read. All** engedélyeket a bérlőhöz az ábrán látható módon.   

![Graph Explorer](./media/concept-reporting-api/graph-explorer.png)

![Engedélyek módosítása felhasználói felület](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Tanúsítványok használata az Azure AD Reporting API eléréséhez 

Használja az Azure AD Reporting API-t a tanúsítványokkal, ha felhasználói beavatkozás nélkül szeretné lekérni a jelentéskészítési adatgyűjtést.

Részletes utasításokért lásd: [Az Azure ad Reporting API és a tanúsítványok használatával kapcsolatos információk lekérése](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Következő lépések

 * [A jelentéskészítési API elérésének előfeltételei](howto-configure-prerequisites-for-reporting-api.md) 
 * [Az Azure AD Reporting API és a tanúsítványok használatával kapott adatlekérdezés](tutorial-access-api-with-certificates.md)
 * [Hibák elhárítása az Azure AD Reporting API-ban](troubleshoot-graph-api.md)


