---
title: Az alapszintű nyilvános verzióról a standard Public-Azure Load Balancer
description: Ez a cikk bemutatja, hogyan frissítheti az Azure Public Load Balancert alapszintű SKU-ról standard SKU-ra
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: a4c8b029b199915cce9a417430e67675a03d327f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659951"
---
# <a name="upgrade-azure-public-load-balancer"></a>Azure nyilvános Load Balancer frissítése
Az [Azure standard Load Balancer](load-balancer-overview.md) számos funkciót és magas rendelkezésre állást kínál a zónák redundancia révén. További információ az Load Balancer SKU-ról: [összehasonlító táblázat](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

A frissítés három szakaszból áll:

1. A konfiguráció áttelepíteni
2. Virtuális gépek hozzáadása standard Load Balancer backend-készletéhez
3. Kimenő szabály létrehozása a terheléselosztó számára a kimenő kapcsolatok számára

Ez a cikk a konfiguráció áttelepítését ismerteti. A virtuális gépeknek a háttérbeli készletekbe való felvétele az adott környezettől függően változhat. Bizonyos magas szintű általános javaslatok azonban [megtalálhatók](#add-vms-to-backend-pools-of-standard-load-balancer).

## <a name="upgrade-overview"></a>Frissítés áttekintése

Olyan Azure PowerShell-parancsfájl érhető el, amely a következő műveleteket végzi el:

* Létrehoz egy szabványos SKU-Load Balancer az erőforráscsoport és a megadott helyen.
* Zökkenőmentesen másolja az alapszintű SKU Load Balancer konfigurációit az újonnan létrehozott standard Load Balancerra.

### <a name="caveatslimitations"></a>Caveats\Limitations

* A parancsfájl csak a nyilvános Load Balancer frissítését támogatja. A belső alapszintű Load Balancer verziófrissítéshez hozzon létre egy szabványos belső Load Balancer, ha a kimenő kapcsolat nem kívánatos, és hozzon létre egy szabványos belső Load Balancer és standard nyilvános Load Balancer, ha kimenő kapcsolatra van szükség.
* A standard Load Balancer új nyilvános címnek kell lennie. A meglévő alapszintű Load Balancerekhez kapcsolódó IP-címeket nem lehet zökkenőmentesen áthelyezni, mivel azok eltérő SKU-k használatával standard Load Balancer.
* Ha a standard Load Balancer egy másik régióban lett létrehozva, akkor a régi régióban meglévő virtuális gépeket nem lehet az újonnan létrehozott standard Load Balancerhoz rendelni. A korlátozás megkerüléséhez hozzon létre egy új virtuális gépet az új régióban.
* Ha a Load Balancer nem rendelkezik előtér-IP-konfigurációval vagy háttér-készlettel, valószínűleg a parancsfájl futtatásakor hiba lépett fel. Ügyeljen arra, hogy ne legyenek üresek.

## <a name="download-the-script"></a>A parancsfájl letöltése

Töltse le az áttelepítési parancsfájlt a [PowerShell-Galéria](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/1.0).
## <a name="use-the-script"></a>A szkript használata

A helyi PowerShell-környezet beállításaitól és beállításaitól függően két lehetőség közül választhat:

* Ha nem rendelkezik az Azure az modulok telepítésével, vagy ne feledje eltávolítani az Azure-t, akkor a legjobb lehetőség a szkript futtatására szolgáló `Install-Script` lehetőség használata.
* Ha meg kell őriznie az Azure az modulokat, a legjobb megoldás, ha letölti a szkriptet, és közvetlenül futtatja.

Annak megállapításához, hogy telepítve van-e az Azure az modulok, futtassa `Get-InstalledModule -Name az`. Ha a telepített modulok nem láthatók, akkor a `Install-Script` metódust használhatja.

### <a name="install-using-the-install-script-method"></a>Telepítés az install-script metódus használatával

Ha ezt a beállítást szeretné használni, az Azure az modulokat nem kell telepítenie a számítógépre. Ha telepítve vannak, a következő parancs hibát jelez. Távolítsa el az Azure az modulokat, vagy használja a másik lehetőséget a szkript manuális letöltéséhez és futtatásához.
  
Futtassa a szkriptet a következő paranccsal:

`Install-Script -Name AzurePublicLBUpgrade`

Ez a parancs telepíti a szükséges az modulokat is.  

### <a name="install-using-the-script-directly"></a>Telepítés közvetlenül a szkript használatával

Ha van néhány Azure az modulok telepítve, és nem távolítható el (vagy nem szeretné eltávolítani őket), manuálisan letöltheti a szkriptet a parancsfájl letöltése hivatkozás **manuális Letöltés** lapján. A parancsfájl nyers nupkg-fájlként van letöltve. Ha a parancsfájlt erről a nupkg-fájlról szeretné telepíteni, olvassa el a [manuális csomagok letöltése](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)című témakört.

A szkript futtatása:

1. `Connect-AzAccount` használata az Azure-hoz való kapcsolódáshoz.

1. A `Import-Module Az` használatával importálja az az modulokat.

1. Vizsgálja meg a szükséges paramétereket:

   * **oldRgName: [string]: kötelező** – ez a meglévő alapszintű Load Balancerhez tartozó erőforráscsoport, amelyet frissíteni kíván. A karakterlánc értékének megkereséséhez navigáljon Azure Portal, válassza ki az alapszintű Load Balancer forrását, és kattintson a terheléselosztó **áttekintésére** . Az erőforráscsoport ezen az oldalon található.
   * **oldLBName: [string]: kötelező** – ez a meglévő alapszintű, frissíteni kívánt alapbalancer neve. 
   * **newrgName: [string]: kötelező** – ez az az erőforráscsoport, amelyben a rendszer létrehozza a standard Load Balancer. Ez lehet egy új erőforráscsoport vagy egy meglévő. Ha kiválaszt egy meglévő erőforráscsoportot, vegye figyelembe, hogy a Load Balancer nevének egyedinek kell lennie az erőforráscsoport belül. 
   * **newlocation: [string]: kötelező** – ez az a hely, ahol a standard Load Balancer létre lesz hozva. Javasoljuk, hogy a kiválasztott alapszintű Load Balancer ugyanazon helyét örökli a standard Load Balancer a többi meglévő erőforrással való jobb együttműködés érdekében.
   * **newLBName: [string]: kötelező** – ez a létrehozandó standard Load Balancer neve.
1. Futtassa a szkriptet a megfelelő paraméterek használatával. A befejezéshez öt – hét percet is igénybe vehet.

    **Példa**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Virtuális gépek hozzáadása standard Load Balancer backend-készletéhez

Először ellenőrizze, hogy a parancsfájl sikeresen létrehozott-e egy új standard nyilvános Load Balancer az alapszintű nyilvános Load Balancer áttelepített pontos konfigurációval. Ezt a Azure Portal ellenőrizheti.

Ügyeljen arra, hogy kis mennyiségű forgalmat küldjön a standard Load Balancer manuális tesztként.
  
Íme néhány forgatókönyv, amely azt ismerteti, hogyan adhat hozzá virtuális gépeket az újonnan létrehozott Standard nyilvános Load Balancer backend-készletéhez, valamint a javaslataikat a következőkhöz:

* **Meglévő virtuális gépek áthelyezése a régi alapszintű nyilvános Load Balancer háttér-készletekből az újonnan létrehozott Standard nyilvános Load Balancer háttér-készletei**között.
    1. A rövid útmutatóban szereplő feladatok végrehajtásához jelentkezzen be a [Azure Portalba](https://portal.azure.com).
 
    1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza ki az **újonnan létrehozott Standard Load Balancer** az erőforrás-listából.
   
    1. A **Beállítások** alatt válassza ki a **Háttérkészletek** elemet.
   
    1. Válassza ki azt a háttér-készletet, amely megfelel az alapszintű Load Balancer háttér-készletének, válassza ki a következő értéket: 
      - **Virtuális gép**: a legördülő listából válassza ki a virtuális gépeket az alapszintű Load Balancer megfelelő háttérbeli készletéből.
    1. Kattintson a **Mentés** gombra.
    >[!NOTE]
    >A nyilvános IP-címmel rendelkező virtuális gépek esetében először a szabványos IP-címeket kell létrehoznia, ahol az IP-cím nem garantált. A virtuális gépeket az alapszintű IP-címekről társíthatja, és társíthatja őket az újonnan létrehozott szabványos IP-címekkel. Ezt követően az utasításokat követve hozzáadhat virtuális gépeket standard Load Balancer háttérbeli készletéből. 

* **Új virtuális gépek létrehozása az újonnan létrehozott Standard nyilvános Load Balancer háttér-készletekhez való hozzáadásához**.
    * További információ a virtuális gép létrehozásával és a standard Load Balancerhoz való hozzárendelésével kapcsolatban [itt](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines)található.

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Kimenő szabály létrehozása a kimenő kapcsolatok számára

Kövesse az [utasításokat](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-portal#create-outbound-rule-configuration) egy kimenő szabály létrehozásához, hogy
* Adja meg a kimenő NAT-t a semmiből.
* A meglévő kimenő NAT működésének skálázása és finomhangolása.

## <a name="common-questions"></a>Gyakori kérdések

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Vannak korlátozások a Azure PowerShell szkripttel a konfiguráció a v1-ről a v2-re való áttelepíteni?

Igen. Lásd a [figyelmeztetéseket és korlátozásokat](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>A Azure PowerShell szkript az alapszintű Load Balancerról az újonnan létrehozott standard Load Balancerra is átváltja a forgalmat?

Nem. A Azure PowerShell szkript csak a konfigurációt telepíti át. A tényleges forgalom áttelepítése az Ön felelőssége és a vezérlése.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Néhány probléma merült fel a szkript használatával. Hogyan Kérhetek segítséget?
  
E-mailt küldhet slbupgradesupport@microsoft.comnak, megnyithat egy támogatási esetet az Azure-támogatással, vagy mindkettőt elvégezheti.

## <a name="next-steps"></a>Következő lépések

[Tudnivalók a standard Load Balancer](load-balancer-overview.md)
