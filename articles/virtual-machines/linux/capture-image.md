---
title: Linux rendszerű virtuális gép lemezképének rögzítése az Azure CLI-vel
description: Az Azure CLI használatával rögzítheti az Azure-beli virtuális gép lemezképét, amelyet tömeges üzembe helyezéshez használhat.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 77f6244651551763f5460432655d66267775a256
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250399"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Virtuális gép vagy VHD rendszerképének létrehozása

Ha egy virtuális gép (VM) több példányát szeretné létrehozni az Azure-ban való használatra, rögzítsen egy rendszerképet a virtuális gépről vagy az operációs rendszer VHD-ről. A rendszerkép központi telepítéshez való létrehozásához el kell távolítania a személyes fiókadatok adatait. A következő lépésekben kiépít egy meglévő virtuális gépet, felszabadítja azt, és létrehoz egy rendszerképet. Ezt a rendszerképet használhatja arra, hogy virtuális gépeket hozzon létre az előfizetésében található bármely erőforráscsoporthoz.

Ha meglévő linuxos virtuális gépe másolatát szeretné létrehozni a biztonsági mentéshez vagy a hibakereséshez, vagy egy speciális linuxos virtuális merevlemezt szeretne feltölteni egy helyszíni virtuális gépről, tekintse meg a [Linux rendszerű virtuális gép feltöltése és létrehozása az egyéni](upload-vhd.md)lemezképből című témakört.  

Az **Azure VM rendszerkép-készítő (nyilvános előzetes)** szolgáltatásával egyéni rendszerképet hozhat létre, nem kell megtanulnia semmilyen eszközt, vagy a telepítő folyamatokat kell beállítania, egyszerűen biztosítva a rendszerkép-konfigurációt, és a rendszerkép-szerkesztő létrehozza a rendszerképet. További információ: [első lépések az Azure VM rendszerkép-készítővel](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

Rendszerkép létrehozása előtt a következő elemekre lesz szüksége:

* A felügyelt lemezeket használó Resource Manager-alapú üzemi modellben létrehozott Azure-beli virtuális gép. Ha még nem hozott létre Linux rendszerű virtuális gépet, akkor használhatja a [portált](quick-create-portal.md), az [Azure CLI](quick-create-cli.md)-t vagy a [Resource Manager-sablonokat](create-ssh-secured-vm-from-template.md). Szükség szerint konfigurálja a virtuális gépet. Ilyen például az [adatlemezek hozzáadása](add-disk.md), a frissítések alkalmazása és az alkalmazások telepítése. 

* A legújabb [Azure CLI](/cli/azure/install-az-cli2) -t telepítette, és be kell jelentkeznie egy Azure-fiókba az [az login](/cli/azure/reference-index#az-login)paranccsal.

## <a name="prefer-a-tutorial-instead"></a>Inkább egy oktatóanyagot szeretne?

A cikk egyszerűsített verziójához, valamint az Azure-beli virtuális gépek teszteléséhez, értékeléséhez vagy megismeréséhez tekintse meg [Az Azure-beli virtuális gép egyéni rendszerképének létrehozása a parancssori felület használatával](tutorial-custom-images.md)című cikket.  Ellenkező esetben folytassa a teljes kép beolvasásával.


## <a name="step-1-deprovision-the-vm"></a>1\. lépés: a virtuális gép kiépítése
Először távolítsa el a virtuális gépet az Azure virtuálisgép-ügynök használatával a gép-specifikus fájlok és az adatfájlok törléséhez. Használja a `waagent` parancsot a forrás linuxos virtuális gépen található `-deprovision+user` paraméterrel. További információk: [Azure Linux-ügynök – felhasználói útmutató](../extensions/agent-linux.md).

1. Kapcsolódjon Linux rendszerű virtuális géphez egy SSH-ügyféllel.
2. Az SSH ablakban adja meg a következő parancsot:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Csak olyan virtuális gépen futtassa ezt a parancsot, amelyet lemezképként fog rögzíteni. Ez a parancs nem garantálja, hogy a rendszer törli a képet az összes bizalmas adatról, vagy újraelosztásra alkalmas. A `+user` paraméter az utolsó kiosztott felhasználói fiókot is eltávolítja. Ha a felhasználói fiók hitelesítő adatait szeretné megőrizni a virtuális gépen, csak `-deprovision`használjon.
 
3. A folytatáshoz adja meg az **y** értéket. A megerősítési lépés elkerüléséhez hozzáadhatja a `-force` paramétert.
4. A parancs befejezése után a **Kilépés** gombra kattintva zárja be az SSH-ügyfelet.  Ezen a ponton továbbra is fut a virtuális gép.

## <a name="step-2-create-vm-image"></a>2\. lépés: virtuális gép rendszerképének létrehozása
Használja az Azure CLI-t a virtuális gép általánosított való megjelölésére és a lemezkép rögzítésére. Az alábbi példákban cserélje le a példában szereplő paraméterek nevét a saját értékeire. A paraméterek nevei például a következők: *myResourceGroup*, *myVnet*és *myVM*.

1. Szabadítsa fel a virtuális gépet, amelyet az [az VM felszabadításával](/cli/azure/vm)kiépített. Az alábbi példa felszabadítja a *myVM* nevű virtuális gépet a *myResourceGroup*nevű erőforráscsoporthoz.  
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```
    
    Várjon, amíg a virtuális gép teljesen fel nem szabadítja a szolgáltatást, mielőtt továbblépne. Ez eltarthat néhány percig.  A lefoglalás során a virtuális gép le van állítva.

2. A virtuális gépet az [az VM generalize](/cli/azure/vm)paranccsal általánosítva jelölheti meg. A következő példa a *myVM* nevű virtuális gépet az *myResourceGroup* nevű erőforráscsoport szerint jelöli meg általánosított néven.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

    Egy általánosított virtuális gép már nem indítható újra.

3. Hozzon létre egy rendszerképet a VM-erőforrásról az [az rendszerkép Create](/cli/azure/image#az-image-create)paranccsal. A következő példa létrehoz egy *myImage* nevű rendszerképet a *myResourceGroup* nevű ERŐFORRÁSCSOPORTHOZ a *myVM*nevű VM-erőforrás használatával.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > A rendszerkép ugyanabban az erőforráscsoporthoz jön létre, mint a forrás virtuális gép. Az előfizetéshez tartozó bármelyik erőforráscsoporthoz létrehozhat virtuális gépeket ebből a rendszerképből. Felügyeleti szempontból előfordulhat, hogy létre kell hoznia egy adott erőforráscsoportot a virtuális gép erőforrásaihoz és a lemezképekhez.
   >
   > Ha a rendszerképet a zóna rugalmas tárterületén szeretné tárolni, létre kell hoznia egy olyan régióban, amely támogatja a [rendelkezésre állási zónákat](../../availability-zones/az-overview.md) , és tartalmazza a `--zone-resilient true` paramétert.
   
Ez a parancs visszaadja a virtuális gép rendszerképét leíró JSON-t. Mentse ezt a kimenetet későbbi hivatkozásként.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>3\. lépés: virtuális gép létrehozása a rögzített lemezképből
Hozzon létre egy virtuális gépet az az [VM Create](/cli/azure/vm)paranccsal létrehozott rendszerkép használatával. A következő példában létrehozunk egy *myVMDeployed* nevű virtuális gépet a *myImage*nevű rendszerképből.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>A virtuális gép létrehozása egy másik erőforráscsoporthoz 

Létrehozhat virtuális gépeket az előfizetésében található bármelyik erőforráscsoporthoz tartozó rendszerképből. Ha egy virtuális gépet a rendszerképtől eltérő erőforráscsoport alapján szeretne létrehozni, adja meg a rendszerkép teljes erőforrás-AZONOSÍTÓját. Az [az Image List](/cli/azure/image#az-image-list) használatával megtekintheti a képek listáját. A kimenet a következő példához hasonló.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

Az alábbi példa az [az VM Create](/cli/azure/vm#az-vm-create) paranccsal hoz létre egy virtuális gépet a forrás rendszerképtől eltérő erőforráscsoporthoz a rendszerkép-erőforrás azonosítójának megadásával.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>4\. lépés: a központi telepítés ellenőrzése

Az SSH-t a létrehozott virtuális gépre, hogy ellenőrizze a telepítést, és kezdje el használni az új virtuális gépet. Ha SSH-n keresztül szeretne csatlakozni, keresse meg a virtuális gép IP-címét vagy teljes tartománynevét az [az VM show](/cli/azure/vm#az-vm-show)paranccsal.

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Következő lépések
Több virtuális GÉPET is létrehozhat a forrás virtuálisgép-rendszerképből. A rendszerkép módosítása: 

- Hozzon létre egy virtuális gépet a rendszerképből.
- Módosítsa a frissítéseket és a konfigurációt.
- A rendszerkép megszüntetéséhez, felszabadításához, általánosításához és létrehozásához kövesse a lépéseket.
- Ezt az új rendszerképet használja a jövőbeli üzembe helyezésekhez. Törölheti az eredeti rendszerképet is.

A virtuális gépek parancssori felülettel való kezelésével kapcsolatos további információkért lásd az [Azure CLI](/cli/azure)-t.
