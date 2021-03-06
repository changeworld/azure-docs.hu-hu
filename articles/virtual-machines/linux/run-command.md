---
title: Rendszerhéj-parancsfájlok futtatása Linux rendszerű virtuális gépen az Azure-ban
description: Ez a témakör azt ismerteti, hogyan futtathat parancsfájlokat egy Azure-beli linuxos virtuális gépen a parancs futtatása funkció használatával
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 21787854590d3ca0be2cbd6e9d167de33482c787
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597877"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Rendszerhéj-parancsfájlok futtatása Linux rendszerű virtuális gépen a Futtatás parancs használatával

A futtatási parancs funkció a virtuális gép (VM) ügynököt használja a rendszerhéj-parancsfájlok Azure-beli linuxos virtuális gépen való futtatásához. Ezeket a parancsfájlokat használhatja az általános gépekhez vagy az alkalmazások kezeléséhez. Segítségükkel gyorsan diagnosztizálhatja és elháríthatja a virtuális gépek hozzáférési és hálózati problémáit, és visszaállíthatja a virtuális gépet jó állapotba.

## <a name="benefits"></a>Előnyök

Több módon is elérheti a virtuális gépeket. A Run parancs a virtuálisgép-ügynök használatával távolról is futtathat parancsfájlokat a virtuális gépeken. A Futtatás parancsot a Linux rendszerű virtuális gépekhez készült Azure Portalon, [Rest APIon](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)vagy [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) -n keresztül használhatja.

Ez a képesség minden olyan helyzetben hasznos, ahol parancsfájlt szeretne futtatni egy virtuális gépen belül. Ez az egyik lehetőség az olyan virtuális gépek hibáinak elhárítására és szervizelésére, amelyek nem rendelkeznek az RDP-vagy SSH-porttal, mert nem megfelelő hálózati vagy rendszergazdai konfigurációval rendelkeznek.

## <a name="restrictions"></a>Korlátozások

A futtatási parancs használatakor a következő korlátozások érvényesek:

* A kimenet az utolsó 4 096 bájtra korlátozódik.
* A parancsfájl futtatásának minimális ideje körülbelül 20 másodperc.
* A parancsfájlok alapértelmezés szerint a Linuxon emelt szintű felhasználóként futnak.
* Egyszerre csak egy parancsfájlt futtathat.
* Az adatokat kérő parancsfájlok (interaktív mód) nem támogatottak.
* Futó parancsfájlt nem lehet megszakítani.
* A parancsfájlok futtatásának maximális ideje 90 perc. Ezt követően a szkript időtúllépést eredményez.
* A parancsfájl eredményeinek visszaküldéséhez a virtuális gépről kimenő kapcsolat szükséges.

> [!NOTE]
> Ahhoz, hogy megfelelően működjön, futtassa a következő parancsot az Azure nyilvános IP-címeihez: kapcsolat (443-es port). Ha a bővítmény nem fér hozzá ezekhez a végpontokhoz, előfordulhat, hogy a parancsfájlok sikeresen futnak, de nem adják vissza az eredményeket. Ha blokkolja a forgalmat a virtuális gépen, a `AzureCloud` címke használatával engedélyezheti az Azure-beli nyilvános IP-címekre [irányuló](../../virtual-network/security-overview.md#service-tags) adatforgalmat.

## <a name="available-commands"></a>Elérhető parancsok

Ez a táblázat a Linux rendszerű virtuális gépekhez elérhető parancsok listáját jeleníti meg. A **RunShellScript** parancs használatával bármilyen egyéni parancsfájlt futtathat, amelyet szeretne. Ha az Azure CLI vagy a PowerShell használatával futtat egy parancsot, a `--command-id` vagy `-CommandId` paraméterhez megadott értéknek az alábbi felsorolt értékek egyikének kell lennie. Ha olyan értéket ad meg, amely nem elérhető parancs, a következő hibaüzenet jelenik meg:

```error
The entity was not found in this Azure location
```

|**Name (Név)**|**Leírás**|
|---|---|
|**RunShellScript**|Linux rendszerű rendszerhéj-parancsfájlt futtat.|
|**ifconfig**| Az összes hálózati adapter konfigurációjának beolvasása.|

## <a name="azure-cli"></a>Azure parancssori felület (CLI)

Az alábbi példa az az [VM Run-Command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) paranccsal futtat egy rendszerhéj-parancsfájlt egy Azure Linux rendszerű virtuális gépen.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Ha másik felhasználóként szeretné futtatni a parancsokat, írja be a `sudo -u`t a felhasználói fiók megadásához.

## <a name="azure-portal"></a>Azure Portal

Lépjen a [Azure Portal](https://portal.azure.com) egy virtuális gépre, és válassza a **Futtatás parancsot** a **műveletek**területen. Ekkor megjelenik a virtuális gépen futtatandó elérhető parancsok listája.

![Parancsok listája](./media/run-command/run-command-list.png)

Válassza ki a futtatandó parancsot. Egyes parancsok opcionális vagy kötelező bemeneti paramétereket tartalmazhatnak. Ezen parancsok esetében a paraméterek szövegmezőként jelennek meg a bemeneti értékek megadásához. Minden parancsnál megtekintheti a Futtatás alatt álló parancsfájlt a **megtekintési parancsfájl**kibontásával. A **RunShellScript** eltér a többi parancstól, mert lehetővé teszi a saját egyéni parancsfájl megadását.

> [!NOTE]
> A beépített parancsok nem szerkeszthetők.

A parancs kiválasztása után válassza a **Futtatás** parancsot a szkript futtatásához. A szkript befejeződése után visszaadja a kimenetet és az esetleges hibákat a kimeneti ablakban. Az alábbi képernyőfelvételen az **ifconfig** parancs futtatásának kimenete látható.

![Parancsfájl kimenetének futtatása](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

Az alábbi példa a [meghívó-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) parancsmag használatával futtat PowerShell-parancsfájlt egy Azure-beli virtuális gépen. A parancsmag elvárja, hogy a `-ScriptPath` paraméterben hivatkozott parancsfájl helyi legyen a parancsmag futtatásának helyétől.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>A futtatási parancshoz való hozzáférés korlátozása

A futtatási parancsok listázása vagy egy parancs részleteinek megjelenítéséhez az `Microsoft.Compute/locations/runCommands/read` engedélyre van szükség az előfizetés szintjén. Ez az engedély a beépített [olvasói](../../role-based-access-control/built-in-roles.md#reader) szerepkörhöz és a magasabb szintekhez tartozik.

A parancs futtatásához a `Microsoft.Compute/virtualMachines/runCommand/action` engedélyre van szükség az előfizetés szintjén. Ez az engedély a [virtuális gép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepköre és a magasabb szinten van.

Használhatja a [beépített szerepkörök](../../role-based-access-control/built-in-roles.md) egyikét, vagy létrehozhat egy [Egyéni szerepkört](../../role-based-access-control/custom-roles.md) a futtatási parancs használatához.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a parancsfájlok és parancsok távoli virtuális gépen való futtatásának egyéb módjairól, olvassa el a [parancsfájlok futtatása a linuxos virtuális gépen](run-scripts-in-vm.md)című témakört.
