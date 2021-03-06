---
title: Kapcsolódás Windows rendszerű virtuálisgép-méretezési csoporthoz az Azure Bastion használatával | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat Azure-beli virtuálisgép-méretezési csoportokhoz az Azure Bastion használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4f513aaf113ef4bd6e75e5c4b31e0f0252d45f10
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988090"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Kapcsolódás virtuálisgép-méretezési csoporthoz az Azure Bastion használatával

Ez a cikk bemutatja, hogyan lehet biztonságosan és zökkenőmentesen RDP-t használni a Windows virtuálisgép-méretezési csoport példányaihoz az Azure-beli virtuális hálózaton az Azure Bastion használatával. Közvetlenül a Azure Portal csatlakozhat egy virtuálisgép-méretezési csoport példányaihoz. Az Azure Bastion használatával a virtuális gépeken nincs szükség ügyfélre, ügynökre és további szoftverekre sem. További információ az Azure Bastion-ről: [Áttekintés](bastion-overview.md).

## <a name="before-you-begin"></a>Előzetes teendők

Győződjön meg arról, hogy beállította a virtuálisgép-méretezési csoportba tartozó virtuális hálózathoz tartozó Azure Bastion-gazdagépet. További információ: [Azure Bastion-gazdagép létrehozása](bastion-create-host-portal.md). A megerősített szolgáltatás a virtuális hálózatban való üzembe helyezése és telepítése után a segítségével csatlakozhat egy virtuálisgép-méretezési csoport példányaihoz ebben a virtuális hálózaton. A Bastion azt feltételezi, hogy RDP-t használ egy Windows rendszerű virtuálisgép-méretezési csoporthoz való kapcsolódáshoz, és az SSH-t a linuxos virtuálisgép-méretezési csoporthoz való kapcsolódáshoz. További információ a Linux rendszerű virtuális gépekhez való kapcsolódásról: [Kapcsolódás virtuális géphez – Linux](bastion-connect-vm-ssh.md).

## <a name="rdp"></a>Kapcsolat RDP használatával

1. Nyissa meg az [Azure portált](https://portal.azure.com). Navigáljon ahhoz a virtuálisgép-méretezési csoporthoz, amelyhez csatlakozni szeretne.

   ![lépjen](./media/bastion-connect-vm-scale-set/1.png)
2. Keresse meg a virtuálisgép-méretezési csoport azon példányát, amelyhez csatlakozni szeretne, majd válassza a **Kapcsolódás**lehetőséget. RDP-kapcsolat használata esetén a virtuálisgép-méretezési csoportnak Windows virtuálisgép-méretezési csoportnak kell lennie.

   ![Virtuálisgép-méretezési csoport](./media/bastion-connect-vm-scale-set/2.png)
3. A **kapcsolat**kiválasztása után megjelenik egy oldalsó sáv, amely három lapot – RDP, SSH és Bastion – tartalmaz. Válassza ki az oldalsó sávban a **megerősített** lapot. Ha nem hozott létre a virtuális hálózatra vonatkozó kiépítést, a következő hivatkozásra kattintva állíthatja be a Bastion-t. A konfigurációs utasításokért lásd: a [Bastion konfigurálása](bastion-create-host-portal.md).

   ![Megerősített lap](./media/bastion-connect-vm-scale-set/3.png)
4. A megerősített lapon adja meg a virtuálisgép-méretezési csoport felhasználónevét és jelszavát, majd válassza a **kapcsolat**lehetőséget.

   ![csatlakozásra](./media/bastion-connect-vm-scale-set/4.png)
5. Az ehhez a virtuális géphez a Bastion-en keresztül létesített RDP-kapcsolat közvetlenül a Azure Portal (HTML5-n keresztül) lesz megnyitva a 443-es port és a megerősített szolgáltatás használatával.

## <a name="next-steps"></a>Következő lépések

Olvassa el a [megerősített GYIK](bastion-faq.md)-t.