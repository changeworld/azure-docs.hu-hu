---
title: 'Gyors útmutató: kiszolgáló létrehozása – az postgres up-Azure Database for PostgreSQL-Single Server'
description: Rövid útmutató Azure Database for PostgreSQL – egyetlen kiszolgáló létrehozásához az Azure CLI (parancssori felület)-up paranccsal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: fe15c02286223ec0829b31664811b7f589cf16aa
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774832"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Gyors útmutató: Azure CLI-parancs használata az postgres up (előzetes verzió), Azure Database for PostgreSQL-egyetlen kiszolgáló létrehozása

> [!IMPORTANT]
> Az az [postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI-parancs előzetes verzióban érhető el.

A PostgreSQL-hez készült Azure Database felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure Database for PostgreSQL-kiszolgálót az Azure CLI használatával az az [postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) paranccsal. A-kiszolgáló létrehozása mellett a `az postgres up` parancs létrehoz egy minta-adatbázist, egy gyökérszintű felhasználót az adatbázisban, megnyitja a tűzfalat az Azure-szolgáltatásokhoz, és létrehozza az alapértelmezett tűzfalszabályok az ügyfélszámítógépen. Ezek az alapértelmezett beállítások segítenek a fejlesztési folyamat felgyorsításában.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verzióját kell futtatnia helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az az [login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) paranccsal be kell jelentkeznie a fiókjába. Jegyezze fel az **ID** tulajdonságot a parancs kimenetében a megfelelő előfizetés neveként.

```azurecli
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account) paranccsal. **Az előfizetés-azonosító tulajdonságot** az előfizetés-azonosító helyőrzőbe írja be az előfizetéshez tartozó **bejelentkezési** kimenetből.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

A parancsok használatához telepítse a [db-up](/cli/azure/ext/db-up) bővítményt. Ha a rendszer hibát ad vissza, győződjön meg róla, hogy telepítette az Azure CLI legújabb verzióját. Lásd: az [Azure CLI telepítése](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Hozzon létre egy Azure Database for PostgreSQL-kiszolgálót a következő parancs használatával:

```azurecli
az postgres up
```

A kiszolgáló a következő alapértelmezett értékekkel jön létre (kivéve, ha manuálisan felülbírálja őket):

**Beállítás** | **Alapértelmezett érték** | **Leírás**
---|---|---
server-name | Rendszer által generált | Egy egyedi név, amely az Azure Database for PostgreSQL-kiszolgálót azonosítja.
resource-group | Rendszer által generált | Egy új Azure-erőforráscsoport.
sku-name | GP_Gen5_2 | A termékváltozat neve. A {tarifacsomag}\_{számítási generáció}\_{virtuális magok} mintát követi rövidített módon. Az alapértelmezett érték egy általános célú Gen5-kiszolgáló 2 virtuális mag. A szintekkel kapcsolatos további információkért tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/postgresql/) oldalunkat.
backup-retention | 7 | A biztonsági másolat megőrzésének ideje. A mértékegysége a nap.
geo-redundant-backup | Letiltva | Azt adja meg, hogy a georedundáns biztonsági mentést engedélyezni kell-e ehhez a kiszolgálóhoz.
location | westus2 | A kiszolgáló Azure-helye.
ssl-enforcement | Letiltva | Azt adja meg, hogy engedélyezni kell-e az ssl-t ehhez a kiszolgálóhoz.
storage-size | 5120 | A kiszolgáló tárkapacitása (megabájtban megadva).
version | 10 | A PostgreSQL főverziója.
admin-user | Rendszer által generált | A rendszergazda felhasználóneve.
admin-password | Rendszer által generált | A rendszergazda felhasználó jelszava.

> [!NOTE]
> További információ a `az postgres up` parancsról és a további paraméterekről: az [Azure CLI dokumentációja](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up).

A kiszolgáló létrehozása után a következő beállításokkal rendelkezik:

- Létrejön egy "devbox" nevű tűzfalszabály. Az Azure CLI megpróbálja felderíteni a gép IP-címét, és az IP-címet a `az postgres up` parancsot futtatja és engedélyezte.
- "Az Azure-szolgáltatásokhoz való hozzáférés engedélyezése" beállítás be értékre van állítva. Ezzel a beállítással konfigurálható a kiszolgáló tűzfala, hogy fogadja az összes Azure-erőforrás kapcsolatait, beleértve az előfizetésben nem szereplő erőforrásokat is.
- A rendszer létrehoz egy "sampledb" nevű üres adatbázist.
- Létrejön egy "root" nevű új felhasználó, amely jogosultsággal rendelkezik a "sampledb" létrehozásához.

> [!NOTE]
> A Azure Database for PostgreSQL a 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Az IT-részleg az 5432-as portot nyitja meg a kiszolgálóhoz való csatlakozáshoz.

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

Az `az postgres up` parancs befejezése után a rendszer a népszerű programozási nyelvekhez tartozó kapcsolódási karakterláncok listáját adja vissza Önnek. Ezek a csatlakozási karakterláncok előre konfigurálva vannak az újonnan létrehozott Azure Database for PostgreSQL-kiszolgáló adott attribútumaival.

A kapcsolati karakterláncok ismételt listázásához használja az az [postgres show-kapcsolat-string](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) parancsot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő parancs használatával törölje a gyors útmutatóban létrehozott összes erőforrást. Ez a parancs törli a Azure Database for PostgreSQL kiszolgálót és az erőforráscsoportot.

```azurecli
az postgres down --delete-group
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja az [az postgres Down](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down) parancsot.

```azurecli
az postgres down
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)
