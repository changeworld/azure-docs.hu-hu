---
title: Oldal tartalmának módosítása a fejlesztői portálon API Management
titleSuffix: Azure API Management
description: Megtudhatja, hogyan szerkesztheti az oldaltartalmakat a fejlesztői portálon az Azure API Managementben.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: vlvinogr
editor: ''
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: vlvinogr
ms.openlocfilehash: ebf2cbd430339378a09d10d91ad61327d24842e4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430631"
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Oldalak tartalmának és elrendezésének módosítása a fejlesztői portálon az Azure API Managementben
A fejlesztői portál három alapvető módon szabható testre az Azure API Managementben:

* [Szerkesztheti a statikus lapok és a lapelrendezés elemek tartalmát][modify-content-layout] (ez az útmutató ismerteti)
* [Az oldal elemeihez használt stílusok frissítése a fejlesztői portálon][customize-styles]
* A [portál által létrehozott oldalakhoz használt sablonok módosítása][portal-templates] (például API-dokumentumok, termékek, felhasználói hitelesítés stb.)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="page-structure"> </a>A fejlesztői portál oldalainak szerkezete

A fejlesztői portál egy tartalomkezelő rendszeren alapul. Az egyes oldalak elrendezése kis oldalelemek készleteire, más néven widgetekre épül:

![A fejlesztői portál oldalszerkezete][api-management-customization-widget-structure]

Az összes widget szerkeszthető.
* Az egyes oldalak fő tartalmai a „Tartalmak” widgetben találhatók. Egy oldal tartalmának szerkesztésekor ennek a widgetnek a tartalmát szerkeszti.
* Az összes oldalelrendezési elem a többi widgetben található. A widgeteken végrehajtott módosítások az összes oldalra érvényesek. Ezeket „elrendezési widgeteknek” nevezzük.

A napi szintű oldalszerkesztés során általában csak a Tartalom widget szerkesztésére kerül sor, amely minden egyes laphoz különböző tartalommal rendelkezik.

## <a name="modify-layout-widget"> </a>Elrendezési widget tartalmának módosítása

A fejlesztői portál elérhető az Azure Portalról.

1. Kattintson a **Fejlesztői portál** elemre az API Management-példány eszköztárán.
2. A widgetek tartalmának szerkesztéséhez kattintson a két ecsetet ábrázoló ikonra a bal oldalon található **Fejlesztői** portál menüben.
3. A fejléc tartalmának módosításához görgessen a bal oldali lista **Fejléc** szakaszához.

    A widgetek a mezőkön belülről szerkeszthetők.
4. Ha készen áll a módosítások közzétételére, kattintson a lap alján található **Közzététel** gombra.

Most már a fejlesztői portál mindegyik oldalán az új fejléc fog megjelenni.

## <a name="next-steps"> </a>További lépések
* [Az oldal elemeihez használt stílusok frissítése a fejlesztői portálon][customize-styles]
* A [portál által létrehozott oldalakhoz használt sablonok módosítása][portal-templates] (például API-dokumentumok, termékek, felhasználói hitelesítés stb.)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
