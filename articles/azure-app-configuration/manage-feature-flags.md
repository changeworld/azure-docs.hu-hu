---
title: 'Oktatóanyag: az Azure-alkalmazások konfigurációjának használata a funkció-jelzők kezelésére'
titleSuffix: Azure App Configuration
description: Ebből az oktatóanyagból megtudhatja, hogyan kezelheti a funkciók jelzőit az alkalmazástól függetlenül az Azure app Configuration használatával.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: ccab8014000f9f684249bf2c1f800f74c92e7369
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899367"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Oktatóanyag: funkció-jelzők kezelése az Azure-alkalmazás konfigurációjában

Az összes funkció jelzőjét az Azure-alkalmazások konfigurációjában tárolhatja, és egyetlen helyről felügyelheti őket. Az alkalmazás konfigurációja egy, a Feature **Manager** nevű portál felhasználói felülettel rendelkezik, amelyet kifejezetten a funkció-jelzők számára terveztek. Az alkalmazás konfigurációja is natív módon támogatja a .NET Core Feature-Flag adatsémát.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A szolgáltatási jelzők definiálása és kezelése az alkalmazás konfigurációjában.
> * Hozzáférési funkciók jelzői az alkalmazásból.

## <a name="create-feature-flags"></a>Szolgáltatás-jelzők létrehozása

Az alkalmazás-konfiguráció Azure Portaljában található Feature Manager egy felhasználói felületet biztosít az alkalmazásokban használt szolgáltatás-jelzők létrehozásához és kezeléséhez.

Új szolgáltatás jelző hozzáadása:

1. Válassza ki a Feature **Manager** >  **+ Hozzáadás** elemet a szolgáltatás-jelölő hozzáadásához.

    ![Szolgáltatás jelölői listája](./media/azure-app-configuration-feature-flags.png)

1. Adjon meg egy egyedi kulcsnévt a szolgáltatás jelzője számára. Ezt a nevet kell megadnia a kódban szereplő jelölőre való hivatkozáshoz.

1. Ha szeretné, adja meg a szolgáltatás jelölőjét.

1. A szolgáltatás jelzője kezdeti állapotának beállítása. Ez az állapot általában ki- *vagy* *bekapcsolva* . A *on* állapot módosul, *Ha szűrőt* ad hozzá a szolgáltatás jelölőhöz.

    ![Szolgáltatás jelölője – létrehozás](./media/azure-app-configuration-feature-flag-create.png)

1. Ha az állapot *be van kapcsolva*, válassza a **+ szűrő hozzáadása** lehetőséget az állapot minősítéséhez szükséges további feltételek megadásához. Adjon meg egy beépített vagy egyéni szűrő kulcsot, majd válassza a **+ paraméter hozzáadása** lehetőséget, ha egy vagy több paramétert szeretne hozzárendelni a szűrőhöz. A beépített szűrők a következők:

    | Jelmagyarázat | JSON-paraméterek |
    |---|---|
    | Microsoft. százalék | {"Érték": 0-100 százalék} |
    | Microsoft. TimeWindow | {"Start": UTC-idő, "End": UTC-idő} |

    ![Szolgáltatás jelölője szűrő](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Szolgáltatás jelző állapotának frissítése

A szolgáltatás jelölője állapotának módosítása:

1. Válassza a **szolgáltatás-kezelő**elemet.

1. A módosítani kívánt szolgáltatási jelzőtől jobbra válassza a három pontot ( **..** .), majd válassza a **Szerkesztés**lehetőséget.

1. Új állapot beállítása a szolgáltatás jelzője számára.

## <a name="access-feature-flags"></a>Hozzáférési funkciók jelzői

A Feature Manager által létrehozott funkció-jelzők tárolása és lekérése normál értékként történik. Ezeket egy speciális névtér-előtag `.appconfig.featureflag`. Az alapul szolgáló kulcs értékeit a Configuration Explorer használatával tekintheti meg. Az alkalmazás lekérheti ezeket az értékeket az alkalmazás-konfiguráció konfigurációs szolgáltatói, SDK-k, parancssori bővítmények és REST API-k használatával.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan kezelheti a funkciók jelzőit és állapotát az alkalmazás konfigurációjának használatával. Az alkalmazás-konfiguráció és a ASP.NET Core szolgáltatás-felügyeleti támogatásáról az alábbi cikkben talál további információt:

* [Szolgáltatás-jelzők használata ASP.NET Core alkalmazásban](./use-feature-flags-dotnet-core.md)
