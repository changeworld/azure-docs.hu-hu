---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 155c88ec4766391f70701b17038b915c399d8b0c
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2020
ms.locfileid: "77372112"
---
## <a name="sign-in-to-luis-portal"></a>Bejelentkezés a LUIS portálra

A LUIS új felhasználójának a következő eljárást kell követnie:

1. Jelentkezzen be a [Luis Portalra (előzetes verzió)](https://preview.luis.ai), válassza ki az országát, és fogadja el a használati feltételeket. Ha ehelyett a **saját alkalmazások** jelennek meg, a Luis-erőforrás már létezik, és az alkalmazás létrehozásához ugorjon előre.

1. Válassza az **Azure-erőforrás létrehozása** lehetőséget, majd válassza az **authoring-erőforrás létrehozása lehetőséget az alkalmazások migrálása érdekében.**

    ![Language Understanding szerzői erőforrás típusának kiválasztása](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Adja meg az erőforrás részleteit.

    ![Szerzői erőforrás létrehozása](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    **Új authoring-erőforrás létrehozásakor**adja meg a következő információkat:

    * **Erőforrás neve** – a kiválasztott egyéni név, amelyet a szerzői műveletek és előrejelzési végpontok lekérdezéséhez használt URL-cím részeként használhat.
    * **Bérlő** – az a bérlő, amelyhez az Azure-előfizetés társítva van.
    * **Előfizetés neve** – az erőforráshoz számlázandó előfizetés.
    * **Erőforráscsoport** – a kiválasztott vagy létrehozott egyéni erőforráscsoport-név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáféréshez és a felügyelethez.
    * **Hely** – a hely kiválasztása az **erőforráscsoport** kiválasztása alapján történik.
    * **Árképzési szint** – az árképzési szint meghatározza a másodpercenkénti és havi maximális tranzakciót.

1. Megjelenik a létrehozandó erőforrás összegzése. Kattintson a **Tovább** gombra.

    ![Szerzői erőforrás létrehozása](../media/sign-in/sign-in-confirm-key-selection.png)

1. Erősítse meg a **Folytatás**lehetőséget.

    ![Szerzői erőforrás létrehozása](../media/sign-in/sign-in-confirm-continue.png)