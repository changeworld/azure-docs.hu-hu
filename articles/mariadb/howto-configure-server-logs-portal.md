---
title: Lassú lekérdezési naplók elérése – Azure Portal-Azure Database for MariaDB
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni és elérni a lassú lekérdezési naplókat Azure Database for MariaDB a Azure Portalból.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 69a01ec021ecbade235a693b1be502353420fde0
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767466"
---
# <a name="configure-and-access-slow-query-logs-from-the-azure-portal"></a>Lassú lekérdezési naplók konfigurálása és elérése a Azure Portal

Beállíthatja, listázhatja és letöltheti az [Azure Database for MariaDB lassú lekérdezési naplókat](concepts-server-logs.md) a Azure Portalból.

## <a name="prerequisites"></a>Előfeltételek
A cikkben ismertetett lépések végrehajtásához Azure Database for MariaDB- [kiszolgáló](quickstart-create-mariadb-server-database-using-azure-portal.md)szükséges.

## <a name="configure-logging"></a>Naplózás konfigurálása
Konfigurálja a lassú lekérdezési naplóhoz való hozzáférést. 

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Válassza ki a Azure Database for MariaDB-kiszolgálót.

3. Az oldalsáv **figyelés** szakaszában válassza a **kiszolgálói naplók**lehetőséget. 
   a kiszolgálói naplók beállításainak ![képernyőképe](./media/howto-configure-server-logs-portal/1-select-server-logs-configure.png)

4. A kiszolgáló paramétereinek megtekintéséhez **kattintson ide a naplók engedélyezéséhez és a naplózási paraméterek konfigurálásához**.

5. Módosítsa a módosítani kívánt paramétereket, beleértve **a** **slow_query_log** bekapcsolását. Az ebben a munkamenetben végrehajtott módosítások a lila színnel vannak kiemelve. 

   A paraméterek módosítása után válassza a **Mentés**lehetőséget. Vagy elvetheti a módosításokat.

   ![A kiszolgálói paraméterek beállításainak képernyőképe](./media/howto-configure-server-logs-portal/3-save-discard.png)

A **kiszolgáló paraméterei** lapon a lap bezárásával visszatérhet a naplók listájához.

## <a name="view-list-and-download-logs"></a>A lista és a letöltési naplók megtekintése
A naplózás megkezdése után megtekintheti az elérhető lassú lekérdezési naplók listáját, és letöltheti az egyes naplófájlokat. 

1. Nyissa meg az Azure Portalt.

2. Válassza ki a Azure Database for MariaDB-kiszolgálót.

3. Az oldalsáv **figyelés** szakaszában válassza a **kiszolgálói naplók**lehetőséget. A lap megjeleníti a naplófájlok listáját.

   ![Képernyőkép a kiszolgálói naplók lapról, a Kiemelt naplók listájával](./media/howto-configure-server-logs-portal/4-server-logs-list.png)

   > [!TIP]
   > A napló elnevezési konvenciója **MySQL-Slow-< a kiszolgáló nevét >-yyyymmddhh. log**. A fájlnévben használt dátum és idő a napló kiadásának időpontja. A naplófájlokat 24 óránként vagy 7,5 GB-ban forgatják el, attól függően, hogy melyik következik be először.

4. Ha szükséges, a keresőmező használatával gyorsan leszűkítheti egy adott naplóra a dátum és idő alapján. A keresés a napló nevében található.

5. Az egyes naplófájlok letöltéséhez kattintson az egyes naplófájlok melletti lefelé mutató nyíl ikonra a táblázat sorában.

   ![Képernyőkép a kiszolgálói naplók lapról, a lefelé mutató nyíl ikon kiemelve](./media/howto-configure-server-logs-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>Diagnosztikai naplók beállítása

1. Az oldalsáv **figyelés** szakaszában válassza a **diagnosztikai beállítások** > **diagnosztikai beállítás hozzáadása**elemet.

   ![A diagnosztikai beállítások beállításainak képernyőképe](./media/howto-configure-server-logs-portal/add-diagnostic-setting.png)

1. Adja meg a diagnosztikai beállítások nevét.

1. Itt adhatja meg, hogy mely adatnyelők küldje el a lassú lekérdezési naplókat (Storage-fiók, Event hub vagy Log Analytics munkaterület).

1. Válassza a **MySqlSlowLogs** lehetőséget a napló típusaként.
a diagnosztikai beállítások konfigurációs beállításainak ![képernyőképe](./media/howto-configure-server-logs-portal/configure-diagnostic-setting.png)

1. Miután konfigurálta az adatnyelőket a lassú lekérdezések naplóinak a csatornába való konfigurálásához, válassza a **Mentés**lehetőséget.
![képernyőkép a diagnosztikai beállítások konfigurációs beállításairól, a kijelöltek mentése](./media/howto-configure-server-logs-portal/save-diagnostic-setting.png)

1. A lassú lekérdezési naplók eléréséhez vizsgálja meg őket a konfigurált adattárolók között. A naplók megjelenése akár 10 percet is igénybe vehet.

## <a name="next-steps"></a>Következő lépések
- A lassú lekérdezési naplók programozott módon történő letöltésének megismeréséhez lásd: [a lassú lekérdezési naplók elérése a CLI-ben](howto-configure-server-logs-cli.md) .
- További információ a Azure Database for MariaDB [lassú lekérdezési naplóiról](concepts-server-logs.md) .
- A paraméter-definíciókkal és a naplózással kapcsolatos további információkért tekintse meg a [naplók](https://mariadb.com/kb/en/library/slow-query-log-overview/)MariaDB dokumentációját.