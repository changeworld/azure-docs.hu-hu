---
title: Fürthöz tartozó rendszerbiztonsági tag hozzáadása az Azure Adatkezelőhoz a Python használatával
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá fürtöket az Azure Adatkezelőhoz a Python használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 637efdfe31d1f2eb0eaa5dd532dd9e9e67de5ce2
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965137"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-python"></a>Fürthöz tartozó rendszerbiztonsági tag hozzáadása az Azure Adatkezelőhoz a Python használatával

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Azure Resource Manager-sablon](cluster-principal-resource-manager.md)

Az Azure Data Explorer egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Ebben a cikkben a Python használatával adja hozzá a fürtöket az Azure Adatkezelőhoz.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Hozzon létre egy fürtöt](create-cluster-database-python.md).

## <a name="install-python-package"></a>Python-csomag telepítése

Az Azure Adatkezelő (Kusto) Python-csomagjának telepítéséhez nyisson meg egy parancssort, amely a Python elérési útját is tartalmazni fogja. Futtassa ezt a parancsot:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Fürthöz tartozó rendszerbiztonsági tag hozzáadása

Az alábbi példa azt szemlélteti, hogyan adhat hozzá programozott módon egy fürtöt.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import ClusterPrincipalAssignment
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster that is created as part of the Prerequisites
cluster_name = "mykustocluster"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.cluster_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, principal_assignment_name= principal_assignment_name, parameters=ClusterPrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Beállítás** | **Ajánlott érték** | **Mező leírása**|
|---|---|---|
| tenant_id | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | A bérlő azonosítója. Más néven címtár-azonosító.|
| subscription_id | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | Az erőforrás-létrehozáshoz használt előfizetés-azonosító.|
| client_id | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | Annak az alkalmazásnak az ügyfél-azonosítója, amely hozzáférhet a bérlő erőforrásaihoz.|
| client_secret | *XXXXXXXXXXXXXX* | Az alkalmazás ügyfél-titka, amely hozzáférhet a bérlő erőforrásaihoz. |
| resource_group_name | *testrg* | A fürtöt tartalmazó erőforráscsoport neve.|
| cluster_name | *mykustocluster* | A fürt neve.|
| principal_assignment_name | *clusterPrincipalAssignment1* | A fürt elsődleges erőforrásának neve.|
| principal_id | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | A résztvevő azonosítója, amely lehet felhasználói e-mail-cím, alkalmazás-azonosító vagy biztonsági csoport neve.|
| szerepkör | *AllDatabasesAdmin* | A fürt elsődleges szerepköre, amely lehet "AllDatabasesAdmin' vagy" AllDatabasesViewer ".|
| tenant_id_for_principal | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | A rendszerbiztonsági tag bérlői azonosítója.|
| principal_type | *Alkalmazás* | A rendszerbiztonsági tag típusa, amely lehet "user", "app" vagy "Group"|

## <a name="next-steps"></a>Következő lépések

* [Adatbázis-rendszerbiztonsági tag hozzáadása](database-principal-python.md)
