---
title: Kiszolgálói paraméterek konfigurálása – Azure CLI – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni a szolgáltatás paramétereit Azure Database for MySQL az Azure CLI parancssori segédprogram használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 0250810d25b0abb5bf675d8c91f3c0678d895c37
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893161"
---
# <a name="customize-server-parameters-by-using-azure-cli"></a>Kiszolgálói paraméterek testreszabása az Azure CLI használatával
Az Azure CLI, az Azure parancssori segédprogram használatával listázhatja, megjelenítheti és frissítheti az Azure Database for MySQL-kiszolgáló konfigurációs paramétereit. A motor konfigurációjának egy részhalmaza a kiszolgáló szintjén érhető el, és módosítható. 

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Egy Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) parancssori segédprogram, vagy használja a Azure Cloud shellt a böngészőben.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Azure Database for MySQL kiszolgáló kiszolgáló-konfigurációs paramétereinek listázása
A kiszolgálók és azok értékei összes módosítható paraméterének listázásához futtassa az az [MySQL Server Configuration List](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) parancsot.

A kiszolgálói **mydemoserver.mysql.database.Azure.com** tartozó kiszolgálói konfigurációs paramétereket a **myresourcegroup**erőforráscsoport alatt listázhatja.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Az egyes felsorolt paraméterek definícióját a MySQL-referenciával foglalkozó szakaszban találja a [kiszolgálói rendszerváltozók](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)című részben.

## <a name="show-server-configuration-parameter-details"></a>Kiszolgáló konfigurációs paramétereinek megjelenítése – részletek
A kiszolgálók egy adott konfigurációs paraméterének részleteinek megjelenítéséhez futtassa az az [MySQL Server Configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) parancsot.

Ez a példa a **lassú\_lekérdezés** részleteit mutatja be\_a log Server konfigurációs paraméterét a kiszolgáló **mydemoserver.mysql.database.Azure.com** az erőforráscsoport **myresourcegroup területen.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Kiszolgáló-konfigurációs paraméter értékének módosítása
Egy bizonyos kiszolgáló-konfigurációs paraméter értékét is módosíthatja, amely frissíti a MySQL-kiszolgáló motorjának alapjául szolgáló konfigurációs értéket. A konfiguráció frissítéséhez használja az az [MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) parancsot. 

Ha frissíteni szeretné a **lassú\_lekérdezést\_a log** Server konfigurációs paraméterét a kiszolgáló **mydemoserver.mysql.database.Azure.com** az erőforráscsoport **myresourcegroup területen.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Ha alaphelyzetbe kívánja állítani egy konfigurációs paraméter értékét, hagyja ki a választható `--value` paramétert, és a szolgáltatás alkalmazza az alapértelmezett értéket. A fenti példában a következőképpen fog kinézni:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Ez a kód visszaállítja a **lassú\_lekérdezési\_naplózási** konfigurációját az **alapértelmezett értékre**. 

## <a name="working-with-the-time-zone-parameter"></a>Az időzóna-paraméter használata

### <a name="populating-the-time-zone-tables"></a>Az időzóna-táblák feltöltése

A kiszolgálón található időzóna-táblákat úgy töltheti fel, hogy meghívja a `az_load_timezone` tárolt eljárást egy olyan eszközről, mint a MySQL parancssori vagy a MySQL Workbench.

> [!NOTE]
> Ha a MySQL Workbench `az_load_timezone` parancsát futtatja, előfordulhat, hogy először a `SET SQL_SAFE_UPDATES=0;`használatával kell kikapcsolnia a biztonságos frissítési módot.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Indítsa újra a kiszolgálót az időzóna-táblák megfelelő kitöltésének biztosításához. A kiszolgáló újraindításához használja a [Azure Portal](howto-restart-server-portal.md) vagy a [parancssori](howto-restart-server-cli.md)felületet.

A rendelkezésre álló időzóna-értékek megtekintéséhez futtassa a következő parancsot:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>A globális szintű időzóna beállítása

A globális szintű időzónát az az [MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) paranccsal lehet beállítani.

A következő parancs frissíti az **idő\_zóna** kiszolgálójának konfigurációs paraméterét a kiszolgáló **mydemoserver.mysql.database.Azure.com** az **USA/csendes-óceáni**csoport **myresourcegroup** .

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>A munkamenet-szint időzónájának beállítása

A munkamenet-szint időzónája beállítható úgy, hogy a `SET time_zone` parancsot egy olyan eszközről futtatja, mint a MySQL parancssori vagy a MySQL Workbench. Az alábbi példa az időzónát az **USA/csendes-óceáni** időzónára állítja be.  

```sql
SET time_zone = 'US/Pacific';
```

Tekintse meg a MySQL dokumentációját a [dátum-és Időfüggvényekhez](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Következő lépések

- [Kiszolgáló paramétereinek konfigurálása Azure Portal](howto-server-parameters.md)