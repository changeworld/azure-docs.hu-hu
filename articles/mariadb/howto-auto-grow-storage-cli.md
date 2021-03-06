---
title: Tárterület automatikus növekedése – Azure CLI – Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan engedélyezheti az automatikus növekedés tárolását a Azure Database for MariaDB Azure CLI használatával.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 4c9677f31128076a80ec168151e74247bdc8bc51
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74771848"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Azure Database for MariaDB tárterület automatikus növelése az Azure CLI használatával
Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MariaDB-kiszolgáló tárterületét úgy, hogy az a munkaterhelés befolyásolása nélkül is növekszik.

A [tárolási korlátot elérő](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit)kiszolgáló csak olvasható értékre van állítva. Ha a Storage automatikus növekedése engedélyezve van, akkor a 100 GB-nál kevesebb kiosztott tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5 GB-kal nő, amint az ingyenes tárterület a kiépített tárterület nagyobb 1 GB-os vagy 10%-ában kisebb. A 100 GB-nál több kiosztott tárterülettel rendelkező kiszolgálók esetében a kiosztott tárterület mérete 5%-kal nő, ha a szabad tárterület mérete a kiosztott tárterület méretének 5%-a alá esik. Az [itt](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) megadott maximális tárolási korlátozások érvényesek.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for MariaDB-kiszolgáló](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ehhez az útmutatóhoz az Azure CLI 2,0-es vagy újabb verzióját kell használnia. A verzió megerősítéséhez az Azure CLI parancssorában írja be a következőt: `az --version`. A telepítéshez vagy a frissítéshez lásd: az [Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="enable-mariadb-server-storage-auto-grow"></a>A MariaDB-kiszolgáló tárterületének automatikus növekedésének engedélyezése

A kiszolgáló automatikus növekedésének engedélyezése egy meglévő kiszolgálón a következő paranccsal:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

A kiszolgáló automatikus növekedésének engedélyezése a tárolóban új kiszolgáló létrehozásakor a következő paranccsal:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Következő lépések

Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-metric.md).