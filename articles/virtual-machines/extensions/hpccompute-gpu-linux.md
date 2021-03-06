---
title: NVIDIA GPU illesztőprogram-bővítmény – Azure Linux rendszerű virtuális gépek
description: Microsoft Azure bővítmény NVIDIA GPU-illesztőprogramok telepítéséhez Linux rendszeren futó N-sorozatú számítási virtuális gépeken.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/11/2019
ms.author: akjosh
ms.openlocfilehash: 6ea61acfc2db3c8f1f5c9c0ac8da8f19897d441e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250568"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>NVIDIA GPU illesztőprogram-bővítmény Linux rendszerhez

## <a name="overview"></a>Áttekintés

Ez a bővítmény az NVIDIA GPU-illesztőprogramokat telepíti a Linux N sorozatú virtuális gépeken. A virtuálisgép-családtól függően a bővítmény a CUDA vagy a GRID-illesztőprogramokat telepíti. Ha ezt a bővítményt használó NVIDIA-illesztőprogramokat telepít, elfogadja és elfogadja az [NVIDIA végfelhasználói licencszerződés](https://go.microsoft.com/fwlink/?linkid=874330)feltételeit. A telepítési folyamat során a virtuális gép újraindulhat az illesztőprogram telepítésének befejezéséhez.

Az illesztőprogramok manuális telepítésére és az aktuálisan támogatott verziókra vonatkozó utasítások [itt](
https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup)érhetők el.
A bővítmény az NVIDIA GPU [-illesztőprogramok Windows N sorozatú virtuális gépeken](hpccompute-gpu-windows.md)történő telepítéséhez is elérhető.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Ez a bővítmény a következő operációsrendszer-disztribúciókat támogatja, az adott operációsrendszer-verzióhoz tartozó illesztőprogram-támogatástól függően.

| Disztribúció | Verzió |
|---|---|
| Linux: Ubuntu | 16,04 LTS, 18,04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6 |

### <a name="internet-connectivity"></a>Internetkapcsolat

Az NVIDIA GPU-illesztőprogramok Microsoft Azure kiterjesztése megköveteli, hogy a célként megadott virtuális gép csatlakoztatva legyen az internethez, és hozzáférhessen.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a bővítmény sémáját jeleníti meg.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Tulajdonságok

| Name (Név) | Érték és példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| publisher | Microsoft.HpcCompute | sztring |
| type | NvidiaGpuDriverLinux | sztring |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Beállítások

Az összes beállítás megadása nem kötelező. Az alapértelmezett viselkedés nem frissíti a rendszermagot, ha az illesztőprogram telepítéséhez nem szükséges, telepítse a legújabb támogatott illesztőprogramot és a CUDA-eszközkészletet (a megfelelő módon).

| Name (Név) | Leírás | Alapértelmezett érték | Érvényes értékek | Adattípus |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | A kernel frissítése akkor is, ha az illesztőprogram telepítése nem szükséges | false | IGAZ, hamis | logikai |
| driverVersion | NV: GRID-illesztőprogram verziója<br> NC/ND: CUDA Toolkit verziója. A kiválasztott CUDA legújabb illesztőprogramjai automatikusan települnek. | Legújabb | RÁCS: "430,30", "418,70", "410,92", "410,71", "390,75", "390,57", "390,42"<br> CUDA: "10.0.130", "9.2.88", "9.1.85" | sztring |
| installCUDA | Telepítse a CUDA eszközkészletet. Csak az NC/ND sorozatú virtuális gépek esetében fontos. | true | IGAZ, hamis | logikai |


## <a name="deployment"></a>Környezet


### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon 

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. A sablonok ideálisak egy vagy több olyan virtuális gép üzembe helyezéséhez, amelyek a telepítés utáni konfigurációt igénylik.

Virtuálisgép-bővítmények JSON konfigurációjának a virtuális gép típusú erőforrást belülre, vagy elhelyezve, a legfelső szintű vagy a legfelső szintű Resource Managerből származó JSON-sablon. A JSON konfigurációs elhelyezését hatással van az erőforrás nevét, és írja be az értékét. További információ: [a gyermek erőforrások nevének és típusának beállítása](../../azure-resource-manager/resource-manager-template-child-resource.md). 

Az alábbi példa azt feltételezi, hogy a bővítmény a virtuális gép erőforrásán belül van beágyazva. A bővítmény erőforrásának beágyazásakor a rendszer a JSON-t a virtuális gép `"resources": []` objektumára helyezi.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

Az alábbi példa a fenti Azure Resource Manager és PowerShell-példákat tükrözi, és az egyéni beállításokat is hozzáadja példaként a nem alapértelmezett illesztőprogramok telepítéséhez. Pontosabban frissíti az operációs rendszer kernelét, és telepíti a CUDA Toolkit egy adott verziójának illesztőprogramját.

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
    "updateOS": true, `
    "driverVersion": "9.1.85", `
  }'
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmények állapotával kapcsolatos adatok beolvashatók a Azure Portalból, valamint a Azure PowerShell és az Azure CLI használatával. Egy adott virtuális gép bővítményeinek központi telepítési állapotának megtekintéséhez futtassa a következő parancsot.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Bővítmény végrehajtás kimenetének a rendszer naplózza a következő fájlt:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Kilépési kódok

| Kilépési kód | Jelentés | A művelet lehetséges |
| :---: | --- | --- |
| 0 | Sikeres művelet |
| 1 | A bővítmény helytelen használata | Végrehajtás kimeneti naplójának keresése |
| 10 | A Hyper-V és az Azure Linux-integrációs szolgáltatása nem érhető el vagy nincs telepítve | A lspci kimenetének ellenõrzése |
| 11 | Az NVIDIA GPU nem található a virtuális gép méretén | [Támogatott VM-méret és operációs rendszer](../linux/n-series-driver-setup.md) használata |
| 12 | A rendszerkép-ajánlat nem támogatott |
| 13 | A virtuális gép mérete nem támogatott | Az N sorozatú virtuális gépek használata az üzembe helyezéshez |
| 14 | Sikertelen művelet | Végrehajtás kimeneti naplójának keresése |


### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/community/)is. Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Következő lépések
További információ a bővítményekről: [virtuálisgép-bővítmények és-szolgáltatások Linux rendszerhez](features-linux.md).

Az N sorozatú virtuális gépekkel kapcsolatos további információkért lásd: GPU-ra [optimalizált virtuálisgép-méretek](../linux/sizes-gpu.md).
