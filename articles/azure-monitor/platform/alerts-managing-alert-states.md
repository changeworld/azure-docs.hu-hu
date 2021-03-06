---
title: Riasztási és intelligenscsoport-állapotok kezelése
description: A riasztási és az intelligens csoportos példányok állapotának kezelése
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: a22d7b7b962b36f93ee804c64b3bc6b08ecceb0f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667516"
---
# <a name="manage-alert-and-smart-group-states"></a>Riasztási és intelligenscsoport-állapotok kezelése

A Azure Monitor riasztásai mostantól [riasztási állapottal és figyelési feltétellel](https://aka.ms/azure-alerts-overview) rendelkeznek, és Hasonlóképpen az intelligens csoportok [intelligens csoporttal](https://aka.ms/smart-groups)rendelkeznek. Az állapot változásai mostantól a megfelelő riasztáshoz vagy intelligens csoporthoz tartozó előzményekben vannak rögzítve. Ez a cikk végigvezeti az állapot módosításának folyamatán a riasztások és az intelligens csoportok esetében is.

## <a name="change-the-state-of-an-alert"></a>Riasztás állapotának módosítása

1. A riasztások állapotát a következő különböző módokon módosíthatja: 
    * A minden riasztás lapon jelölje be azon riasztások melletti jelölőnégyzetet, amelyeknek módosítani szeretné az állapotát, majd kattintson az állapot módosítása gombra.   
    ![Figyelés](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Egy adott riasztási példány riasztás részletei lapon kattintson az állapot módosítása lehetőségre.   
    ![Figyelés](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Egy adott riasztási példány riasztás részletei lapon az intelligens csoport ablaktáblán kattintson a kívánt riasztások melletti jelölőnégyzetre.    
    ![Figyelés](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Az intelligens csoport részletei lapon a tag riasztások listájában kattintson a riasztások elemre a kívánt állapot módosításához, és kattintson a Change intézménye ahová (állapot módosítása) elemre, majd kattintson az állapot módosítása gombra.   
    ![Figyelés](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Ha az állapot módosítása gombra kattint, megjelenik egy előugró ablak, amely lehetővé teszi, hogy kiválassza az állapotot (új/visszaigazolt/lezárt), és szükség esetén adjon meg egy megjegyzést.   
![Figyelés](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Ha ez megtörtént, az állapot változását a rendszer a megfelelő riasztás előzményeiben rögzíti. A megtekintéshez nyissa meg a megfelelő részletek lapot, és ellenőrizze az előzmények szakaszt.    
![Figyelés](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Intelligens csoport állapotának módosítása
1. Az intelligens csoportok állapotát a következő módokon módosíthatja:
    1. Az intelligens csoport listája lapon kattintson a jelölőnégyzetre azon intelligens csoportok mellett, amelyeken módosítani kívánja az állapotát, és kattintson az állapot módosítása gombra.  
    ![Figyelés](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Az intelligens csoport részletei lapon kattintson az állapot módosítása elemre.        
    ![Figyelés](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Ha az állapot módosítása gombra kattint, megjelenik egy előugró ablak, amely lehetővé teszi, hogy kiválassza az állapotot (új/visszaigazolt/lezárt), és szükség esetén adjon meg egy megjegyzést. 
![Monitorozás](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Az intelligens csoportok állapotának módosítása nem változtatja meg az egyes tagokra vonatkozó riasztások állapotát.

1. Ha ez megtörtént, az állapot változása a megfelelő intelligens csoport előzményeiben lesz rögzítve. A megtekintéshez nyissa meg a megfelelő részletek lapot, és ellenőrizze az előzmények szakaszt.     
![Figyelés](./media/alerts-managing-alert-states/state-sg-history.jpg)

