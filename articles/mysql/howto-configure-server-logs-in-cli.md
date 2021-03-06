---
title: Lassú lekérdezési naplók elérése – Azure CLI – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan érheti el a lassú lekérdezési naplókat Azure Database for MySQL az Azure CLI használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 44c35d6e997b4a9a6d3dfcf3e7eba5328b125fdf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770594"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Lassú lekérdezési naplók konfigurálása és elérése az Azure CLI használatával
Az Azure CLI-vel, az Azure parancssori segédprogrammal töltheti le az Azure Database for MySQL lassú lekérdezési naplókat.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- [Azure Database for MySQL kiszolgáló](quickstart-create-mysql-server-database-using-azure-cli.md)
- Az [Azure CLI](/cli/azure/install-azure-cli) vagy Azure Cloud Shell a böngészőben

## <a name="configure-logging"></a>Naplózás konfigurálása
A kiszolgálót úgy is beállíthatja, hogy a következő lépések végrehajtásával hozzáférhessen a MySQL lassú lekérdezési naplóhoz:
1. A lassú lekérdezési naplózás bekapcsolásához állítsa be a **lassú\_lekérdezés\_log** PARAMÉTERt a következőre:.
2. Módosítsa a többi paramétert, például a **hosszú\_lekérdezést\_időt** és a **naplót\_lassú\_rendszergazdai\_utasításait**.

Ha meg szeretné tudni, hogyan állíthatja be a paraméterek értékét az Azure CLI-n keresztül, tekintse meg a [kiszolgáló paramétereinek konfigurálása](howto-configure-server-parameters-using-cli.md)című témakört.

A következő CLI-parancs például bekapcsolja a lassú lekérdezési naplót, beállítja a hosszú lekérdezési időt 10 másodpercre, majd kikapcsolja a lassú rendszergazdai utasítás naplózását. Végül felsorolja az Áttekintés konfigurációs beállításait.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Azure Database for MySQL-kiszolgáló naplófájljainak listázása
A kiszolgáló rendelkezésre álló lassú lekérdezési naplófájljainak listázásához futtassa az az [MySQL Server-logs List](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) parancsot.

A kiszolgálói **mydemoserver.mysql.database.Azure.com** tartozó naplófájlokat az erőforráscsoport **myresourcegroup**lehet kilistázni. Ezután irányítsa a naplófájlok listáját a **log\_Files nevű szövegfájlba\_Listázás. txt**fájlban.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Naplók letöltése a kiszolgálóról
Az az [MySQL Server-logs Download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) paranccsal letöltheti a kiszolgáló egyes naplófájljait. 

A következő példa használatával letöltheti a kiszolgáló **mydemoserver.mysql.database.Azure.com** tartozó naplófájlt a helyi környezet **myresourcegroup** .
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Következő lépések
- További információ [a Azure Database for MySQL lassú lekérdezési naplóiról](concepts-server-logs.md).
