---
title: Megfelelőség a Azure Policy használatával
description: Beépített szabályzatok kiosztása Azure Policyban az Azure Container-nyilvántartások megfelelőségének naplózásához
ms.topic: article
ms.date: 02/26/2020
ms.openlocfilehash: 012cd013de1c60fddcfb28e4bca96d761ada41ab
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330736"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Az Azure Container-nyilvántartások megfelelőségének naplózása Azure Policy használatával

[Azure Policy](../governance/policy/overview.md) a szabályzatok létrehozásához, hozzárendeléséhez és kezeléséhez használt Azure-szolgáltatás. A szabályzatok különböző szabályokat és hatásokat kényszerítenek ki az erőforrásokon, hogy azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek.

Ez a cikk a Azure Container Registry beépített szabályzatait (előzetes verzió) ismerteti. Ezekkel a házirendekkel naplózhatja az új és a meglévő beállításjegyzékeket a megfelelőség érdekében.

A Azure Policy használatához nem számítunk fel díjat.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="built-in-policy-definitions"></a>Beépített szabályzat-definíciók

A következő beépített szabályzat-definíciók a Azure Container Registryra vonatkoznak:

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/azure-policy-samples-policies-container-registry.md)]

Lásd még a beépített hálózati házirend-definíció: [[előzetes verzió] Container Registry a virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78).

## <a name="assign-policies"></a>Szabályzatok hozzárendelése

* Rendeljen szabályzatokat a [Azure Portal](../governance/policy/assign-policy-portal.md), az [Azure CLI](../governance/policy/assign-policy-azurecli.md), egy [Resource Manager-sablon](../governance/policy/assign-policy-template.md)vagy a Azure Policy SDK-k használatával.
* Hatókör: szabályzat-hozzárendelés egy erőforráscsoport, egy előfizetés vagy egy [Azure felügyeleti csoport](../governance/management-groups/overview.md)számára. A tároló beállításjegyzék-házirendjének hozzárendelései a hatókörön belüli meglévő és új tároló-nyilvántartásokra vonatkoznak.
* Bármikor engedélyezheti vagy letilthatja a [szabályzatok kényszerítését](../governance/policy/concepts/assignment-structure.md#enforcement-mode) .

> [!NOTE]
> A szabályzatok hozzárendelése vagy frissítése után időbe telik, amíg a hozzárendelés a meghatározott hatókörben lévő erőforrásokra lesz alkalmazva. Tekintse meg a [szabályzat-próbaverziós eseményindítókkal](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)kapcsolatos információkat.

## <a name="review-policy-compliance"></a>Szabályzat megfelelőségének áttekintése

A szabályzat-hozzárendelések által generált megfelelőségi információk a Azure Portal, az Azure parancssori eszközei vagy a Azure Policy SDK-k használatával érhetők el. Részletekért lásd: [Azure-erőforrások megfelelőségi adatainak beolvasása](../governance/policy/how-to/get-compliance-data.md).

Ha egy erőforrás nem megfelelő, számos lehetséges oka lehet. Az ok megállapításához vagy a felelős változás megkereséséhez tekintse meg a [nem megfelelőség megállapítása](../governance/policy/how-to/determine-non-compliance.md)című témakört.

### <a name="policy-compliance-in-the-portal"></a>Szabályzat megfelelősége a portálon:

1. Válassza a **minden szolgáltatás**lehetőséget, és keresse meg a **házirendet**.
1. Válassza a **megfelelőség**lehetőséget.
1. A szűrők használatával korlátozhatja a megfelelőségi állapotokat, vagy megkeresheti a szabályzatokat ![a portálon](./media/container-registry-azure-policy/azure-policy-compliance.png).
1. Válasszon ki egy házirendet az összesített megfelelőségi adatok és események áttekintéséhez. Ha szükséges, válasszon ki egy adott beállításjegyzéket az erőforrás-megfelelőséghez.

### <a name="policy-compliance-in-the-azure-cli"></a>Szabályzatok megfelelősége az Azure CLI-ben

Az Azure CLI-vel is lekérheti a megfelelőségi adatgyűjtést. Például használja az az [Policy-hozzárendelési lista](/cli/azure/policy/assignment#az-policy-assignment-list) parancsot a CLI-ben az alkalmazott Azure Container Registry szabályzatok házirend-azonosítóinak beszerzéséhez:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Példa a kimenetre:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
[Preview]: Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Ezután futtassa az az [Policy State List](/cli/azure/policy/state#az-policy-state-list) parancsot az adott házirend-azonosítóhoz tartozó összes erőforrás JSON-formátumú megfelelőségi állapotának visszaküldéséhez:

```azurecli
az policy state list \
  --resource <policyID>
```

Vagy futtassa az [az Policy State List](/cli/azure/policy/state#az-policy-state-list) parancsot egy adott beállításjegyzék-erőforrás JSON-formátumú megfelelőségi állapotának visszaküldéséhez, például *myregistry*:

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Következő lépések

* További információ a Azure Policy [definíciói](../governance/policy/concepts/definition-structure.md) és [hatásairól](../governance/policy/concepts/effects.md)

* [Egyéni szabályzat-definíció](../governance/policy/tutorials/create-custom-policy-definition.md) létrehozása

* További információ az Azure [irányítási képességeiről](../governance/index.yml)


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/