---
title: A felügyelt Identity VM-bővítmény használatának leállítása – Azure AD
description: Lépésenkénti útmutató a virtuálisgép-bővítmény használatának leállításához és az Azure Instance Metadata Service (IMDS) használatának megkezdéséhez a hitelesítéshez.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: markvi
ms.openlocfilehash: 3440713c287967655678e1cde2c000a6ed28b900
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183953"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>A virtuális gépi felügyelt identitások bővítmény használatának leállítása és az Azure-Instance Metadata Service használatának megkezdése

## <a name="virtual-machine-extension-for-managed-identities"></a>Virtuálisgép-bővítmény a felügyelt identitásokhoz

A felügyelt identitások virtuálisgép-bővítménye a felügyelt identitáshoz tartozó jogkivonatok igénylésére szolgál a virtuális gépen. A munkafolyamat a következő lépésekből áll:

1. Először is az erőforráson belüli munkaterhelés meghívja a helyi végpontot `http://localhost/oauth2/token` egy hozzáférési jogkivonat igényléséhez.
2. A virtuálisgép-bővítmény ezután a felügyelt identitás hitelesítő adatait használja, hogy hozzáférési jogkivonatot kérjen az Azure AD-től. 
3. A rendszer visszaadja a hozzáférési jogkivonatot a hívónak, és az Azure AD-hitelesítést támogató szolgáltatásokkal (például Azure Key Vault vagy Azure Storage) való hitelesítésre használható.

A következő szakaszban ismertetett számos korlátozás miatt a felügyelt identitás virtuálisgép-bővítménye elavulttá vált a megfelelő végpontnak az Azure Instance Metadata Serviceban (IMDS) való használata mellett.

### <a name="provision-the-extension"></a>A bővítmény kiépítése 

Ha egy virtuális gépet vagy virtuálisgép-méretezési készletet úgy konfigurál, hogy felügyelt identitással rendelkezzen, dönthet úgy, hogy az Azure-erőforrások virtuálisgép-bővítményének felügyelt identitásait kiépíti a [set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) parancsmag `-Type` paraméterének használatával. A virtuális gép típusától függően `ManagedIdentityExtensionForWindows` vagy `ManagedIdentityExtensionForLinux`is átadható, és a `-Name` paraméterrel adhatja meg a nevet. A `-Settings` paraméter határozza meg az OAuth jogkivonat-végpont által a jogkivonat-beszerzéshez használt portot:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

A virtuálisgép-bővítmény kiépítéséhez használhatja a Azure Resource Manager telepítési sablont is. Ehhez adja hozzá a következő JSON-t a `resources` szakaszhoz a sablonhoz (használja az `ManagedIdentityExtensionForLinux`t a Linux-verzióhoz a név és a típus elemnél).

    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
    
Ha virtuálisgép-méretezési csoportokkal dolgozik, az [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) parancsmaggal kiépítheti a felügyelt identitásokat az Azure-erőforrások virtuálisgép-méretezési csoport bővítményének használatával is. A virtuálisgép-méretezési csoport típusától függően `ManagedIdentityExtensionForWindows` vagy `ManagedIdentityExtensionForLinux`is átadható, és a `-Name` paraméterrel adhatja meg a nevet. A `-Settings` paraméter határozza meg az OAuth jogkivonat-végpont által a jogkivonat-beszerzéshez használt portot:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
A virtuálisgép-méretezési csoport bővítménynek a Azure Resource Manager telepítési sablonnal való kiépítéséhez adja hozzá a következő JSON-t a `extensionpProfile` szakaszhoz a sablonhoz (használja a `ManagedIdentityExtensionForLinux` nevet a Linux-verzióhoz, majd írja be az elemeket).

    ```json
    "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
    ```

A virtuálisgép-bővítmény kiépítés sikertelen lehet a DNS-keresési hibák miatt. Ha ez történik, indítsa újra a virtuális gépet, és próbálkozzon újra. 

### <a name="remove-the-extension"></a>A bővítmény eltávolítása 
A bővítmény eltávolításához használja a `-n ManagedIdentityExtensionForWindows` vagy `-n ManagedIdentityExtensionForLinux` kapcsolót (a virtuális gép típusától függően) az [az VM Extension delete](https://docs.microsoft.com/cli/azure/vm/)vagy az [vmss Extension delete](https://docs.microsoft.com/cli/azure/vmss) paranccsal a virtuálisgép-méretezési csoportokhoz az Azure CLI használatával vagy a PowerShell `Remove-AzVMExtension`ával:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Token beszerzése a virtuálisgép-bővítmény használatával

Az alábbi példa az Azure-erőforrások virtuálisgép-bővítmény végpontjának felügyelt identitásait használó minta-kérelem:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elem | Leírás |
| ------- | ----------- |
| `GET` | A HTTP-művelet, amely azt jelzi, hogy a végpontról kívánja beolvasni az adatait. Ebben az esetben egy OAuth hozzáférési jogkivonat. | 
| `http://localhost:50342/oauth2/token` | Az Azure-erőforrások végpontjának felügyelt identitásai, ahol a 50342 az alapértelmezett port, és konfigurálható. |
| `resource` | Egy lekérdezési karakterlánc paraméter, amely a cél erőforrás alkalmazás-azonosító URI azonosítóját jelzi. Emellett megjelenik a kiállított jogkivonat `aud` (célközönség) jogcímében is. Ez a példa jogkivonatot kér a Azure Resource Managerhoz való hozzáféréshez, amely a https://management.azure.com/alkalmazás-azonosító URI-ja. |
| `Metadata` | Egy HTTP-kérelem fejlécének mezője, amelyet az Azure-erőforrások felügyelt identitásai igényelnek a kiszolgálóoldali kérelmek hamisításának (SSRF) támadása ellen. Ezt az értéket a "true" értékre kell beállítani, az összes kisbetű esetében.|
| `object_id` | Választható Egy lekérdezési karakterlánc paraméter, amely annak a felügyelt identitásnak a object_idét jelzi, amelyhez a tokent szeretné. Kötelező, ha a virtuális gépnek több felhasználó által hozzárendelt felügyelt identitása van.|
| `client_id` | Választható Egy lekérdezési karakterlánc paraméter, amely annak a felügyelt identitásnak a client_idét jelzi, amelyhez a tokent szeretné. Kötelező, ha a virtuális gépnek több felhasználó által hozzárendelt felügyelt identitása van.|


Példa a válaszra:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elem | Leírás |
| ------- | ----------- |
| `access_token` | A kért hozzáférési jogkivonat. A biztonságos REST API hívásakor a jogkivonat beágyazva van a `Authorization` kérelem fejléc mezőjébe "tulajdonos" tokenként, amely lehetővé teszi az API számára a hívó hitelesítését. | 
| `refresh_token` | Az Azure-erőforrások felügyelt identitásai nem használják. |
| `expires_in` | Azon másodpercek száma, ameddig a hozzáférési jogkivonat továbbra is érvényben marad, a lejárat időpontja előtt. A kiállítási idő a jogkivonat `iat` jogcímében található. |
| `expires_on` | A TimeSpan, amikor lejár a hozzáférési jogkivonat. A dátum az "1970-01-01T0:0: 0Z UTC" (a token `exp` jogcímnek felel meg) másodpercek száma. |
| `not_before` | A TimeSpan, ha a hozzáférési jogkivonat érvénybe lép, és el lehet fogadni. A dátum az "1970-01-01T0:0: 0Z UTC" (a token `nbf` jogcímnek felel meg) másodpercek száma. |
| `resource` | Az erőforráshoz a hozzáférési tokent kérték, amely megfelel a kérelem `resource` lekérdezési karakterlánc paraméterének. |
| `token_type` | A token típusa, amely egy "tulajdonos" hozzáférési jogkivonat, ami azt jelenti, hogy az erőforrás hozzáférést biztosíthat a jogkivonat tulajdonosához. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>A virtuálisgép-bővítmény hibáinak megoldása 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>A virtuális gép bővítmény újraindítása hiba után

Windows rendszeren és a Linux egyes verzióiban, ha a bővítmény leáll, a következő parancsmag használható a manuális újraindításhoz:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Az elemek magyarázata: 
- A Windows-bővítmény neve és típusa: `ManagedIdentityExtensionForWindows`
- A Linux-bővítmény neve és típusa: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Az "Automation-parancsfájl" sikertelen, amikor a séma exportálását kísérli meg az Azure-erőforrások bővítmény felügyelt identitásai számára

Ha az Azure-erőforrások felügyelt identitásai engedélyezve vannak egy virtuális gépen, a következő hibaüzenet jelenik meg, amikor a virtuális gép "Automation-parancsfájl" funkcióját vagy az erőforráscsoport használatát kísérli meg:

![Felügyelt identitások az Azure-erőforrások automatizálási parancsfájljának exportálási hibája](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Az Azure-erőforrások virtuálisgép-bővítményének felügyelt identitásai jelenleg nem támogatják a sémák erőforráscsoport-sablonba való exportálásának lehetőségét. Ennek eredményeképpen a generált sablon nem jeleníti meg a konfigurációs paramétereket az Azure-erőforrások felügyelt identitásának engedélyezéséhez az erőforráson. Ezeket a részeket manuálisan is hozzáadhatja a [felügyelt identitások konfigurálása az Azure-beli virtuális gépeken sablonok használatával](qs-configure-template-windows-vm.md)című részben ismertetett példákkal.

Ha a séma-exportálási funkció elérhetővé válik a felügyelt identitások számára az Azure-erőforrások virtuálisgép-bővítménye számára (amelyet a 2019-es januári elavulttá terveztek), akkor a rendszer a virtuálisgép- [bővítményeket tartalmazó erőforráscsoportok exportálását is tartalmazza](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>A virtuálisgép-bővítmény korlátai 

A virtuálisgép-bővítmény használatának számos jelentős korlátozása van. 

 * A legsúlyosabb korlátozás az a tény, hogy a jogkivonatok igényléséhez használt hitelesítő adatokat a virtuális gép tárolja. A virtuális gépet sikeresen sértő támadók exfiltrate a hitelesítő adatokat. 
 * Emellett a virtuálisgép-bővítményt továbbra is nem támogatja több Linux-disztribúció, és ez hatalmas fejlesztési költségeket tesz lehetővé a bővítmények módosításához, kiépítéséhez és teszteléséhez. Jelenleg csak a következő Linux-disztribúciók támogatottak: 
    * CoreOS stabil
    * CentOS 7,1 
    * Red Hat 7,2 
    * Ubuntu 15,04 
    * Ubuntu 16.04
 * A virtuális gépek felügyelt identitásokkal való üzembe helyezése teljesítménybeli hatással van, mivel a virtuálisgép-bővítményt is ki kell építeni. 
 * Végül a virtuálisgép-bővítmény csak a 32 felhasználó által hozzárendelt felügyelt identitások használatát támogatja virtuális gépenként. 

## <a name="azure-instance-metadata-service"></a>Azure-Instance Metadata Service

Az [Azure instance metadata Service (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) egy Rest-végpont, amely információt nyújt a virtuális gépek kezeléséhez és konfigurálásához használható virtuálisgép-példányok futtatásáról. A végpont egy jól ismert, nem irányítható IP-címen (`169.254.169.254`) érhető el, amelyet csak a virtuális gépről lehet elérni.

Az Azure IMDS használatának számos előnye van a jogkivonatok igényléséhez. 

1. A szolgáltatás kívül esik a virtuális gépen, ezért a felügyelt identitások által használt hitelesítő adatok már nem jelennek meg a virtuális gépen. Ehelyett az Azure-beli virtuális gép gazdagépén futnak és biztonságosak.   
2. Az Azure IaaS által támogatott összes Windows-és Linux-operációs rendszer felügyelt identitásokat használhat.
3. Az üzembe helyezés gyorsabb és egyszerűbb, mivel a virtuálisgép-bővítményt már nem kell kiépíteni.
4. Az IMDS-végponttal legfeljebb 1000 felhasználó által hozzárendelt felügyelt identitás lehet hozzárendelve egyetlen virtuális géphez.
5. A IMDS-t használó kérések esetében nincs jelentős változás a virtuálisgép-bővítményt használó kérelmek esetében, ezért meglehetősen egyszerű a port használata a virtuális gépek bővítményét jelenleg használó központi telepítések esetében.

Ezen okok miatt az Azure IMDS szolgáltatás a tokenek igénylésének defacto, miután a virtuálisgép-bővítmény elavulttá válik. 


## <a name="next-steps"></a>További lépések

* [Egy Azure-beli virtuális gépen található Azure-erőforrások felügyelt identitásának használata hozzáférési jogkivonat beszerzéséhez](how-to-use-vm-token.md)
* [Azure-Instance Metadata Service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
