---
title: A biztonsági szabályzatok használata | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a biztonsági házirendek a Azure Security Centerban.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 45a27be400753b56c42310a340334feba8a420c5
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906857"
---
# <a name="working-with-security-policies"></a>Biztonsági szabályzatok használata

Ez a cikk ismerteti a biztonsági házirendek konfigurálásának módját, valamint azt, hogy miként lehet megtekinteni őket a Security Centerban. 

## <a name="introduction-to-security-policies"></a>A biztonsági szabályzatok bemutatása

A biztonsági szabályzat határozza meg a számítási feladatok kívánt konfigurációját, és segít biztosítani, hogy megfeleljen a vállalat vagy a szabályozók biztonsági követelményeinek.

A Azure Security Center a kiválasztott szabályzatok alapján hozza meg a biztonsági javaslatait. Security Center házirendek a Azure Policyban létrehozott házirend-kezdeményezéseken alapulnak. A [Azure Policy](../governance/policy/overview.md) segítségével kezelheti a házirendeket, és házirendeket állíthat be a felügyeleti csoportok és több előfizetés között.

Security Center a következő lehetőségeket kínálja a biztonsági házirendek használatához:

* **A beépített alapértelmezett házirend megtekintése és szerkesztése – a** Security Center engedélyezésekor az "ASC default" nevű beépített kezdeményezés automatikusan hozzá van rendelve az összes Security Center regisztrált előfizetéshez (ingyenes vagy standard szint). A kezdeményezés testreszabásához engedélyezheti vagy letilthatja az egyes házirendeket. Tekintse meg a [beépített biztonsági szabályzatok](security-center-policy-definitions.md) listáját, amelyből megismerheti a rendelkezésre álló lehetőségeket.

* **Saját egyéni szabályzatok hozzáadása** – Ha testre szeretné szabni az előfizetésre alkalmazott biztonsági kezdeményezéseket, akkor a Security Centeron belül megteheti. Ezután javaslatokat fog kapni, ha a gépek nem követik a létrehozott házirendeket. Az egyéni házirendek létrehozásával és hozzárendelésével kapcsolatos utasításokért lásd: [egyéni biztonsági házirendek használata](custom-security-policies.md).

* **Szabályozási megfelelőségi szabályzatok hozzáadása** – a Security Center szabályozási megfelelőségi irányítópultja egy adott szabvány vagy szabályozás (például Azure CIS, NIST SP 800-53 R4, Swift CSP) kontextusában jeleníti meg a környezeten belüli összes értékelés állapotát. CSCF-v2020). További információt a [Jogszabályi megfelelőség javítása](security-center-compliance-dashboard.md) című témakörben talál.


## <a name="managing-your-security-policies"></a>Biztonsági szabályzatok kezelése

Biztonsági szabályzatok megtekintése a Security Centerben:

1. A **Security Center** irányítópulton válassza a **biztonsági házirend**elemet.

    ![A Szabályzatkezelés panel](./media/security-center-policies/security-center-policy-mgt.png)

   A **házirend kezelése** képernyőn láthatja a felügyeleti csoportok, előfizetések és munkaterületek számát, valamint a felügyeleti csoport struktúráját.

1. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelynek a szabályzatait meg szeretné tekinteni.

1. Megjelenik az adott előfizetés vagy felügyeleti csoport biztonsági szabályzat lapja. Megjeleníti az elérhető és hozzárendelt házirendeket.

   ![házirend képernyő](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Ha az alapértelmezett szabályzat mellett "MG örökölt" címkével rendelkezik, az azt jelenti, hogy a szabályzat hozzá van rendelve egy felügyeleti csoporthoz, és a megtekintett előfizetés örökli.


1. Válasszon az ezen az oldalon elérhető lehetőségek közül:

    1. Az iparági szabályzatokkal való munkavégzéshez kattintson a **további szabványok hozzáadása**lehetőségre. További információ: [frissítés a dinamikus megfelelőségi csomagokra](update-regulatory-compliance-packages.md).

    1. Egyéni kezdeményezések hozzárendeléséhez és kezeléséhez kattintson az **Egyéni kezdeményezések hozzáadása**lehetőségre. További információ: [egyéni biztonsági házirendek használata](custom-security-policies.md).

    1. Az alapértelmezett házirend megtekintéséhez és szerkesztéséhez kattintson a **hatályos szabályzat megtekintése** elemre, és folytassa az alább leírtak szerint. 

       ![házirend képernyő](./media/security-center-policies/policy-screen.png)
       
       Ez a **biztonsági házirend** képernyő a kiválasztott előfizetéshez vagy felügyeleti csoporthoz hozzárendelt szabályzatok által végrehajtott műveletet tükrözi.
       
       * A felül található hivatkozásokkal nyisson meg egy szabályzat- **hozzárendelést** , amely az előfizetésre vagy a felügyeleti csoportra vonatkozik. Ezekkel a hivatkozásokkal hozzáférhet a hozzárendeléshez, és szerkesztheti vagy letilthatja a szabályzatot. Ha például úgy látja, hogy egy adott házirend-hozzárendelés ténylegesen megtagadja az Endpoint Protectiont, akkor a hivatkozásra kattintva szerkesztheti vagy tilthatja le a házirendet.
       
       * A szabályzatok listájában megtekintheti az előfizetéséhez vagy a felügyeleti csoportjához tartozó házirend hatályos alkalmazását. A rendszer figyelembe veszi a hatókörre vonatkozó egyes szabályzatok beállításait, és a szabályzat által végrehajtott műveletek összesített eredményét jeleníti meg. Ha például a házirend egyetlen hozzárendelésében le van tiltva, de egy másikban a AuditIfNotExist értékre van állítva, akkor a kumulatív hatás a AuditIfNotExist alkalmazza. Az aktívabb hatás mindig elsőbbséget élvez.
       
       * A házirendek hatása a következő lehet: Hozzáfűzés, naplózás, AuditIfNotExists, megtagadás, DeployIfNotExists, letiltva. További információ a hatások alkalmazásáról: a [szabályzatok hatásainak megismerése](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > A hozzárendelt házirendek megtekintésekor több hozzárendelést láthat, és megtekintheti, hogy az egyes hozzárendelések hogyan legyenek konfigurálva.


## <a name="who-can-edit-security-policies"></a>Kik módosíthatják a biztonsági házirendeket?

A biztonsági szabályzatokat a Azure Policy-portálon, REST API vagy a Windows PowerShell használatával szerkesztheti.

A Security Center szerepköralapú Access Control (RBAC) használ, amely beépített szerepköröket biztosít a felhasználókhoz, csoportokhoz és szolgáltatásokhoz az Azure-ban. Security Center megnyitásakor a felhasználók csak azokat az adatokat látják, amelyekhez hozzáféréssel rendelkeznek. Ez azt jelenti, hogy a felhasználók a *tulajdonos*, *közreműködő*vagy *olvasó* szerepkört rendelik hozzá az erőforrás előfizetéséhez. Továbbá a szerepkörökhöz két konkrét Security Center szerepkör tartozik:

- **Biztonsági olvasó**: megtekintheti a Security Centerra vonatkozó jogosultságokat, amelyekben javaslatok, riasztások, szabályzatok és állapotok szerepelnek, de nem módosíthatók.
- **Biztonsági rendszergazda**: ugyanazok a megtekintési jogosultságok, mint a *biztonsági olvasó*, továbbá frissíthetik a biztonsági házirendet, és elérhetik a javaslatokat és a riasztásokat.


## <a name="disable-security-policies"></a>Biztonsági házirendek letiltása
Ha az alapértelmezett biztonsági házirend olyan javaslatot hoz létre, amely nem releváns a környezetében, leállíthatja a javaslatot küldő szabályzat-definíció letiltásával.
A javaslatokkal kapcsolatos további információkért lásd: [biztonsági javaslatok kezelése](security-center-recommendations.md).

1. A Security Center a **házirend & megfelelősége** szakaszban kattintson a **biztonsági szabályzat**elemre.

   ![házirendek kezelése](./media/tutorial-security-policy/policy-management.png)

2. Kattintson arra az előfizetésre vagy felügyeleti csoportra, amelyhez le szeretné tiltani a javaslatot.

   > [!NOTE]
   > Ne feledje, hogy egy felügyeleti csoport alkalmazza a szabályzatokat az előfizetésekre. Ezért ha letilt egy előfizetési szabályzatot, és az előfizetés egy olyan felügyeleti csoporthoz tartozik, amely továbbra is ugyanazt a házirendet használja, akkor továbbra is megkapja a házirendre vonatkozó javaslatokat. A szabályzat továbbra is a felügyeleti szintjéről lesz alkalmazva, és a javaslatok továbbra is létrejönnek.

1. Kattintson a **hatályos szabályzat megtekintése**elemre.

   ![házirend letiltása](./media/tutorial-security-policy/view-effective-policy.png)

1. Kattintson a hozzárendelt házirendre.

   ![házirend letiltása](./media/tutorial-security-policy/security-policy.png)

1. A **Parameters (paraméterek** ) szakaszban keresse meg a letiltani kívánt javaslatot meghívó házirendet, és a legördülő listából válassza a **Letiltva** lehetőséget.

   ![házirend letiltása](./media/tutorial-security-policy/disable-policy.png)

1. Kattintson a **Save** (Mentés) gombra.

   > [!NOTE]
   > A házirend-letiltási módosítások érvénybe léptetéséhez akár 12 órát is igénybe vehet.



## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta a biztonsági szabályzatokat. A kapcsolódó információk a következő cikkekben találhatók:

* A házirendek PowerShell használatával történő beállításával kapcsolatos útmutatásért lásd [: rövid útmutató: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához a Azure PowerShell modul használatával](../governance/policy/assign-policy-powershell.md)

* A biztonsági szabályzatok Azure Policyban való szerkesztésével kapcsolatos útmutatásért lásd: [házirendek létrehozása és kezelése a megfelelőség érvényesítéséhez](../governance/policy/tutorials/create-and-manage.md).

* A szabályzat előfizetésekre vagy felügyeleti csoportokra Azure Policy használatával történő beállításával kapcsolatos utasításokért lásd: [Mi az Azure Policy?](../governance/policy/overview.md)