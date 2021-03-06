---
title: Az Azure Access Panel bővítmény hibaelhárítása az Internet Explorer |} A Microsoft Docs
description: Hogyan lehet az Internet Explorer-bővítmény a saját alkalmazások portál telepítése a csoportházirenddel.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723919"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Az Internet Explorer a hozzáférési Panel bővítmény hibaelhárítása

Ez a cikk a következő problémák elhárításához nyújt segítséget:

* Most már nem fér hozzá az alkalmazások a saját alkalmazások portál használatával az Internet Explorer használatával.
* Annak ellenére, hogy már telepítette a szoftvert a "Szoftver telepítése" üzenetet látja.

Ha Ön rendszergazda, lásd: [a hozzáférési Panel bővítmény telepítése csoportházirend használatával az Internet Explorer](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>A diagnosztikai eszköz futtatása

Felderítheti a hozzáférési Panel bővítmény telepítési problémáinak letöltésével és futtatásával a hozzáférési Panel diagnosztikai eszköz. 

Töltse le és telepítse a diagnosztikai eszköz:

1. [Válassza ki az erre a hivatkozásra kattintva töltse le a diagnosztikai eszköz.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Nyissa meg a fájlt, és csomagolja ki a tartalmát a számítógépre.
1. Futtassa az eszközt, kattintson a jobb gombbal a nevű fájl *AccessPanelExtensionDiagnosticTool.js* válassza **nyissa meg a** > **Microsoft Windows alapú parancsfájlfuttató** .

    ![Nyissa meg a > a Microsoft Windows Script Host alapján](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Tekintse át a diagnosztikai eredményeket, és válassza ki **Igen** megoldhatja a problémákat. A **eredmények ellenőrzése** párbeszédpanel jelenik meg az információkat arról, hogy mi a teendő, ha a bővítmény nem működik.  
1. Olvassa el az üzenetet, és válassza ki **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Ellenőrizze, hogy engedélyezve van-e a hozzáférési Panel bővítmény

Annak ellenőrzése, hogy engedélyezte-e a hozzáférési Panel bővítmény, az Internet Explorerben:

1. Az Internet Explorerben válassza ki a **fogaskerék ikont** ablakban, majd válassza a jobb felső sarkában **Internetbeállítások**.
1. Nyissa meg a **programok** lapot, és válasszon **bővítmények kezelése**.
1. Válassza ki **hozzáférési Panel bővítmény** a a **Microsoft Corporation** szakaszt, és válassza **engedélyezése**.
1. Mentse a módosításokat, zárja be az összes windows Internet Explorer böngészőben van nyitva. A módosítás akkor lépnek érvénybe, amikor legközelebb megnyitja az Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Bővítmények engedélyezése az InPrivate-böngészés

Bővítmények az InPrivate-böngészés engedélyezése:

1. Az Internet Explorerben válassza ki a **fogaskerék ikont** ablakban, majd válassza a jobb felső sarkában **Internetbeállítások**.
1. Nyissa meg a **adatvédelmi** lapon és ellenőrizze, hogy a **eszköztárak és kiterjesztések letiltása, ha az InPrivate-böngészés indítása** jelölőnégyzet nincs bejelölve.
1. Mentse a módosításokat, zárja be az összes windows Internet Explorer böngészőben van nyitva. A módosítás akkor lépnek érvénybe, amikor legközelebb megnyitja az Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Távolítsa el a hozzáférési Panel bővítményt

A hozzáférési Panel bővítmény eltávolítása a számítógépről:

1. A Vezérlőpulton keressen *eltávolítása*.
1. A keresési eredmények között, válassza ki a **program eltávolítása**.

    ![Válassza az Eltávolítás egy beállítás a Vezérlőpultból](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Válassza ki a listából **hozzáférési Panel bővítmény** válassza **Eltávolítás**.

    ![Távolítsa el a hozzáférési Panel bővítményt](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Ezután próbálkozzon az ismételt használatával ellenőrizheti, ha a probléma megoldódott-bővítményének telepítése.

Ha a kiterjesztés eltávolítása problémákat tapasztal, akkor is használatával távolíthatja el a [Microsoft javítása,](https://go.microsoft.com/?linkid=9779673) eszközt.

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](what-is-single-sign-on.md)
* [A hozzáférési Panel bővítmény telepítése csoportházirend használatával az Internet Explorer](deploy-access-panel-browser-extension.md)
