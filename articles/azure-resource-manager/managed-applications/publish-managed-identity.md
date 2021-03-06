---
title: Felügyelt alkalmazás felügyelt identitással
description: Felügyelt identitás konfigurálása a meglévő erőforrásokhoz való kapcsolódáshoz, az Azure-erőforrások kezeléséhez és az operatív identitás biztosításához a tevékenység naplójában.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: dbf75262440474c5cb50a6d733ac7cba212b5f3f
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651656"
---
# <a name="azure-managed-application-with-managed-identity"></a>Felügyelt identitással rendelkező Azure felügyelt alkalmazás

> [!NOTE]
> A felügyelt alkalmazások felügyelt identitásának támogatása jelenleg előzetes verzióban érhető el. A felügyelt identitás kihasználása érdekében használja a 2018-09-01-Preview API-verziót.

Megtudhatja, hogyan konfigurálhat felügyelt alkalmazást a felügyelt identitások tárolására. A felügyelt identitás használatával engedélyezhető, hogy az ügyfél a felügyelt alkalmazáshoz hozzáférést biztosítson a további meglévő erőforrásokhoz. Az identitást az Azure platform felügyeli, és nem igényli semmilyen titok kiépítését és elforgatását. A Azure Active Directory (HRE) felügyelt identitásával kapcsolatos további információkért lásd: [felügyelt identitások az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md).

Az alkalmazás két típusú identitást biztosíthat:

- A **rendszer által hozzárendelt identitás** az alkalmazáshoz van kötve, és törlődik, ha az alkalmazás törölve lett. Egy alkalmazásnak csak egy rendszerhez rendelt identitása lehet.
- A **felhasználó által hozzárendelt identitás** egy önálló Azure-erőforrás, amelyet az alkalmazáshoz rendelhet hozzá. Egy alkalmazáshoz több felhasználó által hozzárendelt identitás is tartozhat.

## <a name="how-to-use-managed-identity"></a>A felügyelt identitás használata

A felügyelt identitás számos forgatókönyvet tesz lehetővé a felügyelt alkalmazásokhoz. A megoldható gyakori forgatókönyvek a következők:

- Meglévő Azure-erőforrásokhoz kapcsolódó felügyelt alkalmazás üzembe helyezése. Egy példa egy Azure-beli virtuális gép (VM) üzembe helyezésére a felügyelt alkalmazásban, amely egy [meglévő hálózati adapterhez](../../virtual-network/virtual-network-network-interface-vm.md)van csatolva.
- A felügyelt alkalmazás és a közzétevő hozzáférésének biztosítása az Azure-erőforrásokhoz a **felügyelt erőforráscsoport**kívül.
- A felügyelt alkalmazások működési identitásának biztosítása a tevékenység naplója és egyéb szolgáltatásai számára az Azure-on belül.

## <a name="adding-managed-identity"></a>Felügyelt identitás hozzáadása

Felügyelt identitással rendelkező felügyelt alkalmazás létrehozásához további tulajdonságot kell beállítani az Azure-erőforráson. Az alábbi példa egy minta **Identity** tulajdonságot mutat be:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

A felügyelt alkalmazások két gyakori módon hozhatók létre az **Identity**: [CreateUIDefinition. JSON](./create-uidefinition-overview.md) és [Azure Resource Manager sablonokkal](../templates/template-syntax.md). Egyszerű, egyszeri létrehozási forgatókönyvek esetén a CreateUIDefinition-t a felügyelt identitás engedélyezésére kell használni, mivel ez gazdagabb élményt nyújt. Ha azonban olyan speciális vagy összetett rendszereket használ, amelyek automatizált vagy több felügyelt alkalmazás-telepítést igényelnek, a sablonok használhatók.

### <a name="using-createuidefinition"></a>A CreateUIDefinition használata

A felügyelt alkalmazások a [CreateUIDefinition. JSON](./create-uidefinition-overview.md)használatával konfigurálhatók felügyelt identitással. A [kimenetek szakaszban](./create-uidefinition-overview.md#outputs)a kulcs `managedIdentity` a felügyelt alkalmazás sablonjának Identity tulajdonságának felülbírálására használható. A mintául szolgáló minta lehetővé teszi a **rendszer által hozzárendelt** identitást a felügyelt alkalmazásban. A CreateUIDefinition elemek használatával összetettebb identitási objektumok hozhatók létre, amelyekkel megkérheti a fogyasztótól a bemeneteket. Ezek a bemenetek a **felhasználó által hozzárendelt identitással**rendelkező felügyelt alkalmazások létrehozására használhatók.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Mikor kell CreateUIDefinition használni a felügyelt identitáshoz

Az alábbiakban néhány javaslatot talál arra vonatkozóan, hogy mikor kell CreateUIDefinition használni a felügyelt identitásnak a felügyelt alkalmazásokban való engedélyezéséhez.

- A felügyelt alkalmazás létrehozása a Azure Portal vagy a piactéren megy keresztül.
- A felügyelt identitás összetett fogyasztói adatbevitelt igényel.
- A felügyelt identitásra a felügyelt alkalmazás létrehozásához van szükség.

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

Egy alapszintű CreateUIDefinition, amely lehetővé teszi a felügyelt alkalmazás SystemAssigned-identitását.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

Alapszintű CreateUIDefinition, amely a **felhasználó által hozzárendelt identitási** erőforrást bemenetként fogadja, és engedélyezi a felügyelt alkalmazás UserAssigned-identitását.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

A fenti CreateUIDefinition. JSON létrehoz egy felhasználói élményt, amely tartalmaz egy szövegmezőt a felhasználó számára a **felhasználó által hozzárendelt identitás** Azure erőforrás-azonosítójának megadásához. A generált élmény a következőképpen fog kinézni:

![A felhasználó által hozzárendelt identitás CreateUIDefinition mintája](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok használata

> [!NOTE]
> A piactéren felügyelt alkalmazás-sablonok automatikusan létrejönnek a Azure Portal-létrehozási élményben részt vevő ügyfelek számára.
> Ezekben az esetekben a CreateUIDefinition lévő `managedIdentity` kimeneti kulcsot kell használni az identitás engedélyezéséhez.

A felügyelt identitás Azure Resource Manager-sablonok használatával is engedélyezhető. A mintául szolgáló minta lehetővé teszi a **rendszer által hozzárendelt** identitást a felügyelt alkalmazásban. Az összetettebb identitási objektumok a bemenetek megadásához Azure Resource Manager sablon paraméterei segítségével hozhatók létre. Ezek a bemenetek a **felhasználó által hozzárendelt identitással**rendelkező felügyelt alkalmazások létrehozására használhatók.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Mikor kell Azure Resource Manager sablonokat használni a felügyelt identitáshoz

Az alábbiakban néhány javaslatot talál arra vonatkozóan, hogy mikor kell Azure Resource Manager sablonokat használni a felügyelt identitásnak a felügyelt alkalmazásokban való engedélyezéséhez.

- A felügyelt alkalmazások programozott módon is üzembe helyezhetők sablon alapján.
- A felügyelt identitáshoz egyéni szerepkör-hozzárendelésekre van szükség a felügyelt alkalmazás kiépítéséhez.
- A felügyelt alkalmazásnak nincs szüksége a Azure Portal és a piactér létrehozási folyamatára.

#### <a name="systemassigned-template"></a>SystemAssigned-sablon

Egy alapszintű Azure Resource Manager-sablon, amely egy felügyelt alkalmazást telepít a **rendszer által hozzárendelt** identitással.

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>UserAssigned-sablon

Egy alapszintű Azure Resource Manager-sablon, amely egy felügyelt alkalmazást telepít egy **felhasználó által hozzárendelt identitással**.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Hozzáférés biztosítása az Azure-erőforrásokhoz

Ha egy felügyelt alkalmazás identitást kap, hozzáférést biztosíthat a meglévő Azure-erőforrásokhoz. Ez a folyamat a Azure Portal hozzáférés-vezérlési (IAM) felületén végezhető el. A felügyelt alkalmazás vagy a **felhasználó által hozzárendelt identitás** neve kereshető a szerepkör-hozzárendelés hozzáadásához.

![Szerepkör-hozzárendelés hozzáadása a felügyelt alkalmazáshoz](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Meglévő Azure-erőforrások összekapcsolása

> [!NOTE]
> A felügyelt alkalmazás telepítése előtt [konfigurálni](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) kell egy **felhasználó által hozzárendelt identitást** . Emellett a felügyelt alkalmazások kapcsolódó erőforrás-telepítése csak a **Piactéri** típus esetében támogatott.

A felügyelt identitással olyan felügyelt alkalmazások is telepíthetők, amelyek a telepítés során a meglévő erőforrásokhoz való hozzáférést igénylik. Ha a felügyelt alkalmazást az ügyfél kiépíti, a **felhasználó által hozzárendelt identitások** hozzáadhatók további engedélyek biztosításához a **mainTemplate** telepítéséhez.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>A CreateUIDefinition létrehozása csatolt erőforrással

A felügyelt alkalmazás meglévő erőforrásokhoz való központi telepítésének összekapcsolásakor meg kell adni a meglévő Azure-erőforrást és egy **felhasználó által hozzárendelt identitást** is az adott erőforráshoz tartozó szerepkör-hozzárendeléssel.

 Egy minta CreateUIDefinition, amelyhez két bemenet szükséges: egy hálózati adapter erőforrás-azonosítója és egy felhasználó által hozzárendelt identitás erőforrás-azonosítója.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Ez a CreateUIDefinition. JSON létrehoz egy olyan felhasználói élményt, amely két mezővel rendelkezik. Az első mező lehetővé teszi, hogy a felhasználó beírja a felügyelt alkalmazás üzembe helyezéséhez kapcsolódó erőforráshoz tartozó Azure Resource ID-t. A második a **felhasználó által hozzárendelt identitás** Azure-erőforrás-azonosítójának megadása, amely hozzáfér a kapcsolódó Azure-erőforráshoz. A generált élmény a következőképpen fog kinézni:

![Minta CreateUIDefinition két bemenettel: egy hálózati adapter erőforrás-azonosítója és egy felhasználó által hozzárendelt identitás erőforrás-azonosítója](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>A mainTemplate létrehozása csatolt erőforrással

A CreateUIDefinition frissítése mellett a fő sablont is frissíteni kell, hogy fogadja az átadott erőforrás-azonosítót. A fő sablon frissíthető úgy, hogy új paraméter hozzáadásával fogadja el az új kimenetet. Mivel a `managedIdentity` kimenet felülbírálja a létrehozott felügyelt alkalmazás sablonjának értékét, a rendszer nem adja át a fősablonnak, és nem szerepelhet a parameters (paraméterek) szakaszban.

Egy minta fő sablon, amely a hálózati profilt a CreateUIDefinition által biztosított meglévő hálózati adapterre állítja be.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>A felügyelt alkalmazás összekapcsolása csatolt erőforrással

A felügyelt alkalmazáscsomag létrehozása után a felügyelt alkalmazás a Azure Portalon keresztül is felhasználható. A felhasználható használat előtt több előfeltétel lép fel.

- Létre kell hozni a szükséges csatolt Azure-erőforrások egy példányát.
- A **felhasználó által hozzárendelt identitást** létre kell hozni, és hozzá kell adni a társított erőforráshoz tartozó [szerepkör-hozzárendeléseket](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) .
- A meglévő csatolt erőforrás-azonosító és a **felhasználó által hozzárendelt** azonosító azonosító a CreateUIDefinition van megadva.

## <a name="accessing-the-managed-identity-token"></a>A felügyelt identitás jogkivonatának elérése

A felügyelt alkalmazás jogkivonata most már elérhető a `listTokens` API-n keresztül a közzétevő bérlőről. Egy példa a kérelemre a következőhöz hasonló lehet:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

Kérelem törzsének paraméterei:

Paraméter | Szükséges | Leírás
---|---|---
authorizationAudience | *nem* | A célként megadott erőforráshoz tartozó alkalmazás-azonosító URI-ja. A kiállított jogkivonat `aud` (célközönség) jogcíme is. Az alapértelmezett érték a "https://management.azure.com/"
userAssignedIdentities | *nem* | A felhasználó által hozzárendelt felügyelt identitások listája a jogkivonat lekéréséhez. Ha nincs megadva, `listTokens` visszaadja a tokent a rendszer által hozzárendelt felügyelt identitáshoz.


A minta válasza A következőképpen néz ki:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

A válasz a `value` tulajdonság alá tartozó jogkivonatok tömbjét fogja tartalmazni:

Paraméter | Leírás
---|---
access_token | A kért hozzáférési jogkivonat.
expires_in | Azon másodpercek száma, ameddig a hozzáférési jogkivonat érvényes lesz.
expires_on | A TimeSpan, amikor lejár a hozzáférési jogkivonat. Ez a szám az alapkorszakból másodpercben kifejezve jelenik meg.
not_before | A TimeSpan, amikor a hozzáférési jogkivonat érvénybe lép. Ez a szám az alapkorszakból másodpercben kifejezve jelenik meg.
authorizationAudience | A hozzáférési tokent kérő `aud` (célközönség). Ez ugyanaz, mint amit a `listTokens` kérelemben megadott.
resourceId | A kiállított jogkivonat Azure-erőforrás-azonosítója. Ez vagy a felügyelt alkalmazás azonosítója vagy a felhasználó által hozzárendelt azonosító.
token_type | A jogkivonat típusa.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Felügyelt alkalmazás konfigurálása egyéni szolgáltatóval](../custom-providers/overview.md)
