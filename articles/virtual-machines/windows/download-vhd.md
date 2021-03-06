---
title: Windows rendszerű virtuális merevlemez letöltése az Azure-ból
description: Töltse le a Windows VHD-t a Azure Portal használatával.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: d1c98fa4f3572c40279978d787b1719746478a06
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940453"
---
# <a name="download-a-windows-vhd-from-azure"></a>Windows rendszerű virtuális merevlemez letöltése az Azure-ból

Ebből a cikkből megtudhatja, hogyan tölthet le egy Windows rendszerű virtuális merevlemezt (VHD-fájlt) az Azure-ból a Azure Portal használatával.

## <a name="optional-generalize-the-vm"></a>Nem kötelező: a virtuális gép általánosítása

Ha a virtuális merevlemezt [képként](tutorial-custom-images.md) szeretné létrehozni más virtuális gépek létrehozásához, akkor a [sysprept](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) kell használnia az operációs rendszer általánosítása érdekében. 

Ha a virtuális merevlemezt képként szeretné használni más virtuális gépek létrehozásához, általánosítsa a virtuális gépet.

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. [Kapcsolódjon a virtuális géphez](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. A virtuális gépen nyissa meg rendszergazdaként a parancssorablakot.
4. Módosítsa a könyvtárat a *%WINDIR%\system32\sysprep* , és futtassa a Sysprep. exe fájlt.
5. A rendszer-előkészítő eszköz párbeszédpanelen jelölje be a rendszerindítási folyamat **(OOBE) megadása**jelölőnégyzetet, és győződjön meg arról, hogy az **általánosítás** van kiválasztva.
6. A leállítási beállítások területen válassza a **Leállítás**lehetőséget, majd kattintson **az OK**gombra. 


## <a name="stop-the-vm"></a>A virtuális gép leállítása

Egy virtuális merevlemez nem tölthető le az Azure-ból, ha egy futó virtuális géphez van csatlakoztatva. A virtuális merevlemez letöltéséhez le kell állítania a virtuális gépet. 

1. A Azure Portal központi menüjében kattintson az **Virtual Machines**elemre.
1. Válassza ki a virtuális gépet a listából.
1. A virtuális gép paneljén kattintson a **Leállítás**gombra.


## <a name="generate-download-url"></a>Letöltési URL-cím előállítása

A VHD-fájl letöltéséhez egy [közös hozzáférési aláírás (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL-címét kell létrehoznia. Az URL-cím létrehozásakor a rendszer lejárati időt rendel az URL-címhez.

1. A virtuális gép lapján kattintson a bal oldali menüben a **lemezek** elemre.
1. Válassza ki a virtuális gép operációsrendszer-lemezét.
1. A lemez lapján válassza a bal oldali menü **lemez exportálása** elemét.
1. Az URL-cím alapértelmezett lejárati ideje *3600* másodperc. Növelje a **36000** -et a Windows operációsrendszer-lemezek esetében.
1. Kattintson az **URL-cím előállítása**gombra.

> [!NOTE]
> A lejárati idő megnő az alapértelmezetttől, hogy elegendő időt biztosítson a nagyméretű VHD-fájl letöltésére a Windows Server operációs rendszer számára. A Windows Server operációs rendszert tartalmazó VHD-fájl várhatóan több órányi letöltést is igénybe vehet a kapcsolódástól függően. Ha egy adatlemezre letölt egy VHD-t, az alapértelmezett idő elegendő. 
> 
> 

## <a name="download-vhd"></a>VHD letöltése

1. A létrehozott URL-cím alatt kattintson a VHD-fájl letöltése elemre.
1. Előfordulhat, hogy a letöltés indításához a böngészőben a **Mentés** gombra kell kattintania. A VHD-fájl alapértelmezett neve *ABCD*.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [tölthet fel egy VHD-fájlt az Azure-](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ba. 
- [Felügyelt lemezek létrehozása a nem felügyelt lemezekről egy Storage-fiókban](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Azure-lemezek kezelése a PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)-lel.

