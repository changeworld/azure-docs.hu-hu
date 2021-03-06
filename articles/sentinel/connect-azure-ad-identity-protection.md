---
title: Azure AD Identity Protection-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással
description: Megtudhatja, hogyan csatlakoztatható Azure AD Identity Protection-adatkapcsolat az Azure Sentinelhez.
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 11/17/2019
ms.author: yelevin
ms.openlocfilehash: 7d42ff28ddd2d883feb25139096d781efe64d50f
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588569"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Adatok összekapcsolásának Azure AD Identity Protection



A naplók a [Azure ad Identity Protectionból](https://docs.microsoft.com/azure/information-protection/reports-aip) az Azure sentinelbe továbbítva továbbítják a riasztásokat az Azure Sentinel szolgáltatásba az irányítópultok megtekintéséhez, egyéni riasztások létrehozásához és a vizsgálat javításához. A Azure Active Directory Identity Protection összevont nézetet biztosít a veszélyeztetett felhasználók, a kockázati észlelések és a sebezhetőségek számára, és lehetővé teszi a kockázatok azonnali javítását, és szabályzatok beállítását a jövőbeli események automatikus szervizeléséhez. A szolgáltatás a Microsoft által a fogyasztói identitások védelmét szolgáló tapasztalatra épül, és egy nap alatt több mint 13 000 000 000-es bejelentkezésből származó, hatalmas pontosságot nyer. 


## <a name="prerequisites"></a>Előfeltételek

- Rendelkeznie kell egy [prémium szintű Azure Active Directory P1 vagy P2 licenccel](https://azure.microsoft.com/pricing/details/active-directory/)
- Globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel rendelkező felhasználó


## <a name="connect-to-azure-ad-identity-protection"></a>Kapcsolódás Azure AD Identity Protectionhoz

Ha már rendelkezik Azure AD Identity Protection, győződjön meg arról, hogy az engedélyezve van a [hálózaton](../active-directory/identity-protection/overview-identity-protection.md).
Ha Azure AD Identity Protection üzembe helyezése és az adatolvasás, a riasztási adatátviteli szolgáltatás könnyen továbbítható az Azure Sentinelbe.


1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd kattintson a **Azure ad Identity Protection** csempére.

2. A **Kapcsolódás** lehetőségre kattintva megkezdheti a streaming Azure ad Identity Protection események beküldését az Azure sentinelbe.


6. Ha a Log Analytics vonatkozó sémát szeretné használni a Azure AD Identity Protection riasztásokhoz, keresse meg a **SecurityAlert**.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakozhat Azure AD Identity Protection az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
