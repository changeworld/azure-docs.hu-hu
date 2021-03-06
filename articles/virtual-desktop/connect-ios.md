---
title: Kapcsolódás a Windows rendszerű virtuális asztalhoz iOS-Azure-ból
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz az iOS-ügyfél használatával.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3bf9ea6c68e4cbbe721705639e6c6416c0e93c9d
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128251"
---
# <a name="connect-with-the-ios-client"></a>Kapcsolódás az iOS-ügyfélhez

> A következőkre vonatkozik: iOS 13,0 vagy újabb. IPhone-, iPad-és iPod Touch-kompatibilis.

A Windows rendszerű virtuális asztali erőforrásokat a letölthető ügyféllel is elérheti iOS-eszközéről. Ebből az útmutatóból megtudhatja, hogyan állíthatja be az iOS-ügyfelet.

## <a name="install-the-ios-client"></a>Az iOS-ügyfél telepítése

Első lépésként [töltse le](https://aka.ms/rdios) és telepítse az ügyfelet iOS-eszközén.

## <a name="subscribe-to-a-feed"></a>Előfizetés hírcsatornára

Fizessen elő a rendszergazda által biztosított hírcsatornára az iOS-eszközön elérhető felügyelt erőforrások listájának lekéréséhez.

Előfizetés a hírcsatornára:

1. A Csatlakozáskezelőben koppintson a **+** elemre, majd koppintson a **munkaterület hozzáadása**lehetőségre.
2. Írja be a hírcsatorna URL-címét a **hírcsatorna URL-címe** mezőbe. A hírcsatorna URL-címe lehet URL vagy e-mail-cím.
   - Ha URL-címet használ, használja a rendszergazdát. Az URL-cím általában <https://rdweb.wvd.microsoft.com>.
   - E-mail-cím használatához adja meg az e-mail-címét. Ez azt jelzi, hogy az ügyfél megkeresi az e-mail-címéhez tartozó URL-címet, ha a rendszergazda konfigurálta a kiszolgálót.
3. Koppintson a **tovább**gombra.
4. Ha a rendszer kéri, adja meg a hitelesítő adatait.
   - A **Felhasználónév mezőben**adja meg a felhasználónevet, amely jogosult az erőforrások elérésére.
   - A **Password (jelszó**) mezőben adja meg a felhasználónévhez tartozó jelszót.
   - Az is előfordulhat, hogy további tényezőket kell megadnia, ha a rendszergazda úgy konfigurálta a hitelesítést.
5. Koppintson a **Mentés**gombra.

Ezután a Csatlakozáskezelő megjeleníti a távoli erőforrásokat.

Miután előfizetett egy hírcsatornára, a hírcsatorna tartalma automatikusan frissülni fog. A rendszergazda által végrehajtott módosítások alapján hozzáadhatók, módosíthatók vagy eltávolíthatók az erőforrások.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az iOS-ügyfél használatáról, tekintse meg az iOS-ügyfél dokumentációjának használatának [első lépéseit](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) .
