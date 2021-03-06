---
title: Engedélyek a Azure Security Centerban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a Azure Security Center szerepköralapú hozzáférés-vezérléssel a felhasználók engedélyeinek kiosztásához és az egyes szerepkörökhöz engedélyezett műveletek azonosításához.
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 0e60e782fa65cd5868bebe081673f9a158e07799
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921317"
---
# <a name="permissions-in-azure-security-center"></a>Engedélyek Azure Security Center

Az Azure Security Center [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/role-assignments-portal.md) használ, amelynek [beépített szerepköreit](../role-based-access-control/built-in-roles.md) az Azure különböző csoportjaihoz, felhasználóihoz és szolgáltatásaihoz rendelheti.

A Security Center a biztonsági problémák és biztonsági rések azonosításához erőforrásán konfiguráció értékeli. A Security Centerben csak láthatja az előfizetés vagy az erőforráscsoport, amely egy erőforrás tartozik, a tulajdonos, közreműködő vagy olvasó a szerepkör hozzárendelése esetén egy erőforráshoz kapcsolódó információkat.

Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

* **Biztonsági olvasó**: az ehhez a szerepkörhöz tartozó felhasználó megtekinti a jogosultságokat a Security Center. A felhasználók megtekinthetik a javaslatokat, a riasztásokat, a biztonsági házirendeket és a biztonsági állapotokat, de nem módosíthatják azokat.
* **Biztonsági rendszergazda**: az ehhez a szerepkörhöz tartozó felhasználók ugyanazokkal a jogokkal rendelkeznek, mint a biztonsági olvasó, és frissíthetik a biztonsági házirendet, és elmulasztják a riasztások és javaslatok elutasítását.

> [!NOTE]
> A biztonsági szerepkörök, a biztonsági olvasó és a biztonsági rendszergazda csak Security Center rendelkezik hozzáféréssel. A biztonsági szerepkörök nem férnek hozzá az Azure más szolgáltatási területeihez, például a Storage, a Web & Mobile vagy a eszközök internetes hálózatahoz.
>
>

## <a name="roles-and-allowed-actions"></a>Szerepkörök és engedélyezett műveletek

A következő táblázat a szerepköröket és az engedélyezett műveleteket mutatja Security Centerban.

| Szerepkör | Biztonsági házirend szerkesztése | Erőforrásokra vonatkozó biztonsági javaslatok alkalmazása</br> (beleértve a ' Quick Fix! ') | Riasztások és javaslatok bezárása | Riasztások és javaslatok megtekintése |
|:--- |:---:|:---:|:---:|:---:|
| Előfizetés tulajdonosa | ✔ | ✔ | ✔ | ✔ |
| Előfizetés közreműködői | -- | ✔ | ✔ | ✔ |
| Erőforráscsoport tulajdonosa | -- | ✔ | -- | ✔ |
| Erőforráscsoport közreműködői | -- | ✔ | -- | ✔ |
| Olvasó | -- | -- | -- | ✔ |
| Biztonsági rendszergazda | ✔ | -- | ✔ | ✔ |
| Biztonsági olvasó | -- | -- | -- | ✔ |

> [!NOTE]
> Javasoljuk, hogy a felhasználókhoz azt a lehető legalacsonyabb szintű szerepkört rendelje, amellyel még el tudják végezni feladataikat. Például rendelje hozzá az olvasó szerepkört azokhoz a felhasználókhoz, akik csak az erőforrások biztonsági állapotával kapcsolatos információkat szeretnének megtekinteni, de nem végeznek műveleteket, például javaslatok alkalmazása vagy szerkesztési szabályzatok.
>
>

## <a name="next-steps"></a>Következő lépések
Ez a cikk azt ismerteti, hogyan használja a Security Center a RBAC-t a felhasználók engedélyeinek hozzárendeléséhez és az egyes szerepkörökhöz engedélyezett műveletek azonosításához. Most, hogy már ismeri az előfizetés biztonsági állapotának figyeléséhez szükséges szerepkör-hozzárendeléseket, a biztonsági szabályzatok szerkesztését és a javaslatok alkalmazását, Ismerje meg a következőket:

- [Biztonsági szabályzatok beállítása Security Center](tutorial-security-policy.md)
- [Biztonsági javaslatok kezelése Security Center](security-center-recommendations.md)
- [Azure-erőforrások biztonsági állapotának figyelése](security-center-monitoring.md)
- [A biztonsági riasztások kezelése és a riasztásokra való válaszadás a Security Centerben](security-center-managing-and-responding-alerts.md)
- [Partneri biztonsági megoldások figyelése](security-center-partner-solutions.md)
