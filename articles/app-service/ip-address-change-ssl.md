---
title: Az SSL IP-címek módosításának előkészítése
description: Ha az SSL IP-címe megváltozik, Ismerje meg, mi a teendő, hogy az alkalmazás továbbra is működni fog a módosítás után.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 34f35eb67cada6066e35227fcd6a0eaf425ac007
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672404"
---
# <a name="how-to-prepare-for-an-ssl-ip-address-change"></a>Az SSL IP-címek módosításának előkészítése

Ha értesítést kapott arról, hogy a Azure App Service alkalmazás SSL IP-címe változik, kövesse az ebben a cikkben található utasításokat a meglévő SSL IP-cím kiadásához és egy új hozzárendeléséhez.

## <a name="release-ssl-ip-addresses-and-assign-new-ones"></a>Az SSL IP-címeinek kiadása és az újak kiosztása

1.  Nyissa meg az [Azure Portal](https://portal.azure.com).

2.  A bal oldali navigációs menüben válassza a **app Services**lehetőséget.

3.  Válassza ki a App Service alkalmazást a listából.

4.  A **Beállítások** fejléc alatt kattintson az **SSL-beállítások** elemre a bal oldali navigációs sávon.

1. Az SSL-kötések szakaszban válassza ki az állomásnév rekordját. A megnyíló szerkesztőben válassza a **SNI SSL** lehetőséget az **SSL típusa** legördülő menüben, majd kattintson a **kötés hozzáadása**parancsra. Amikor megjelenik a művelet sikerességéről szóló üzenet, a meglévő IP-cím megjelent.

6.  Az **SSL-kötések** szakaszban válassza ki ugyanazt az állomásnév-rekordot a tanúsítvánnyal. A megnyíló szerkesztőben ezúttal válassza az **IP-alapú SSL** lehetőséget az **SSL típusa** legördülő menüben, majd kattintson a **kötés hozzáadása**parancsra. Amikor megjelenik a művelet sikerességéről szóló üzenet, új IP-címet szerzett.

7.  Ha egy rekord (a közvetlenül az IP-címére mutató DNS-rekord) be van állítva a tartományi regisztrációs portálon (harmadik fél DNS-szolgáltatója vagy Azure DNS), cserélje le a meglévő IP-címet az újonnan létrehozott-re. Az új IP-címet a következő szakaszban található utasítások alapján érheti el.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Az új SSL IP-cím megkeresése az Azure Portalon

1.  Várjon néhány percet, majd nyissa meg a [Azure Portal](https://portal.azure.com).

2.  A bal oldali navigációs menüben válassza a **app Services**lehetőséget.

3.  Válassza ki a App Service alkalmazást a listából.

4.  A **Beállítások** fejléc alatt kattintson a bal oldali navigációs menüben a **Tulajdonságok** elemre, és keresse meg a **virtuális IP-cím**feliratú szakaszt.

5. Másolja ki az IP-címet, és konfigurálja újra a tartományi rekordot vagy az IP-mechanizmust.

## <a name="next-steps"></a>Következő lépések

Ez a cikk azt ismerteti, hogyan lehet előkészíteni az Azure által kezdeményezett IP-címek változásait. A Azure App Service IP-címeivel kapcsolatos további információkért lásd: [SSL-és SSL-IP-címek Azure app Service](overview-inbound-outbound-ips.md).
