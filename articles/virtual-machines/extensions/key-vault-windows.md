---
title: Azure Key Vault virtuálisgép-bővítmény a Windowshoz
description: Telepítsen egy ügynököt, amely a virtuális gépek Key Vault titkainak automatikus frissítését végzi a virtuálisgép-bővítmény használatával.
services: virtual-machines-windows
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 22a4177d85cb9dbbaa9ed75e063306484c7b48a9
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298971"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>A Windows rendszerhez készült virtuálisgép-bővítmény Key Vault

A Key Vault virtuálisgép-bővítmény az Azure Key vaultban tárolt tanúsítványok automatikus frissítését teszi lehetővé. Pontosabban, a bővítmény figyeli a Key vaultban tárolt megfigyelt tanúsítványok listáját, és a módosítások észlelése, lekérése és a megfelelő tanúsítványok telepítése után. Ez a dokumentum a Windows rendszerhez készült Key Vault virtuálisgép-bővítmény támogatott platformait, konfigurációit és központi telepítési lehetőségeit részletezi. 

### <a name="operating-system"></a>Operációs rendszer

A Key Vault virtuálisgép-bővítmény a Windows alábbi verzióit támogatja:

- A Windows Server 2019
- Windows Server 2016
- Windows Server 2012

### <a name="supported-certificate-content-types"></a>Támogatott tanúsítvány-tartalomtípusok

- PKCS #12

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Key Vault virtuálisgép-bővítmény sémáját jeleníti meg. A bővítmény nem igényel védett beállításokat – az összes beállítás nyilvános információnak minősül. A bővítményhez meg kell adni a figyelt tanúsítványok listáját, a lekérdezés gyakoriságát és a célhely tanúsítványtárolóját. Ezek:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

> [!NOTE]
> A megfigyelt tanúsítványok URL-címeinek `https://myVaultName.vault.azure.net/secrets/myCertName`formátumúnak kell lenniük.
> 
> Ennek az az oka, hogy a `/secrets` útvonal a teljes tanúsítványt adja vissza, beleértve a titkos kulcsot is, míg a `/certificates` útvonal nem. A tanúsítványokkal kapcsolatos további információkért tekintse meg a következőt: [Key Vault tanúsítványok](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)

### <a name="property-values"></a>Tulajdonságok értékei

| Név | Érték és példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | dátum |
| publisher | Microsoft.Azure.KeyVault | Karakterlánc |
| típus | KeyVaultForWindows | Karakterlánc |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | Karakterlánc |
| certificateStoreName | MY | Karakterlánc |
| linkOnRenewal | false | Logikai érték |
| certificateStoreLocation  | LocalMachine | Karakterlánc |
| requiredInitialSync | true | Logikai érték |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | Karakterlánc-tömb


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. A sablonok ideálisak egy vagy több olyan virtuális gép üzembe helyezéséhez, amelyek a tanúsítványok telepítés utáni frissítését igénylik. A bővítmény az egyes virtuális gépekre vagy virtuálisgép-méretezési csoportokra is telepíthető. A séma és a konfiguráció a sablonok típusainál is gyakori. 

A virtuálisgép-bővítmények JSON-konfigurációját a sablon virtuálisgép-erőforrás szilánkján belül kell beágyazni, különösen `"resources": []` objektumot a virtuálisgép-sablonhoz, és a `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` objektum alatti virtuálisgép-méretezési csoport esetén.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Azure PowerShell üzemelő példány

A Azure PowerShell használatával telepítheti a Key Vault virtuálisgép-bővítményt egy meglévő virtuális gépre vagy virtuálisgép-méretezési csoportba. 

* A bővítmény üzembe helyezése egy virtuális gépen:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* A bővítmény üzembe helyezése virtuálisgép-méretezési csoporton:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az Azure CLI használatával telepítheti a Key Vault virtuálisgép-bővítményt egy meglévő virtuális gépre vagy virtuálisgép-méretezési csoportba. 
 
* A bővítmény üzembe helyezése egy virtuális gépen:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* A bővítmény üzembe helyezése virtuálisgép-méretezési csoporton:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Vegye figyelembe a következő korlátozásokat/követelményeket:
- Key Vault korlátozások:
  - A telepítés időpontjában léteznie kell 
  - Key Vault hozzáférési szabályzat a VM/VMSS identitáshoz van beállítva az MSI használatával


## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok beolvashatók a Azure Portalból és a Azure PowerShell használatával. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa az alábbi parancsot a Azure PowerShell használatával.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

Bővítmény végrehajtás kimenetének a rendszer naplózza a következő fájlt:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/forums/)is. Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
