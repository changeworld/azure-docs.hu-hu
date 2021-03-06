---
title: Kiszolgáló újraindítása – Azure Portal – Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan lehet újraindítani egy Azure Database for MariaDB kiszolgálót az Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: fb4e56a3f40573a65b679ee026c22dfc5d6e5fa2
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769421"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Azure Database for MariaDB kiszolgáló újraindítása Azure Portal használatával
Ez a témakör azt ismerteti, hogyan lehet újraindítani egy Azure Database for MariaDB-kiszolgálót. Előfordulhat, hogy a kiszolgálót karbantartás miatt újra kell indítania, ami rövid kimaradást okoz, mivel a kiszolgáló végrehajtja a műveletet.

A kiszolgáló újraindítása le lesz tiltva, ha a szolgáltatás foglalt. Előfordulhat például, hogy a szolgáltatás feldolgoz egy korábban kért műveletet, például a skálázási virtuális mag.

Az újraindítás befejezéséhez szükséges idő a MariaDB helyreállítási folyamattól függ. Az újraindítási idő csökkentése érdekében javasoljuk, hogy csökkentse a kiszolgálón előforduló tevékenységek mennyiségét az újraindítás előtt.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for MariaDB-kiszolgáló](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Kiszolgáló újraindítása

A következő lépésekkel indítsa újra a MariaDB-kiszolgálót:

1. A Azure Portal válassza ki a Azure Database for MariaDB kiszolgálót.

2. A kiszolgáló **Áttekintés** lapjának eszköztárán kattintson az **Újraindítás**elemre.

   ![Azure Database for MariaDB – áttekintés – Újraindítás gomb](./media/howto-restart-server-portal/2-server.png)

3. A kiszolgáló újraindításának megerősítéséhez kattintson az **Igen** gombra.

   ![Azure Database for MariaDB – újraindítás megerősítése](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Figyelje meg, hogy a kiszolgáló állapota "újraindítás" értékűre változik.

   ![Azure Database for MariaDB – újraindítás állapota](./media/howto-restart-server-portal/4-restarting-status.png)

5. A kiszolgáló újraindításának megerősítése sikeres volt.

   ![Azure Database for MariaDB – sikeres újraindítás](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Következő lépések

[Gyors útmutató: Azure Database for MariaDB kiszolgáló létrehozása Azure Portal használatával](./quickstart-create-mariadb-server-database-using-azure-portal.md)