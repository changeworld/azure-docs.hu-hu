---
title: Linuxos rendszerhéj-parancsfájlkezelési labor beállítása Azure Lab Services használatával | Microsoft Docs
description: Ismerje meg, hogyan állíthat be egy labort a rendszerhéj-parancsfájlok Linuxon való tanításához.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 100a485588c77f6977001dae984b30ebcb1de557
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443550"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Tesztkörnyezet beállítása a rendszerhéj-parancsfájlok Linuxon való tanításához
Ez a cikk bemutatja, hogyan állíthat be egy labort a rendszerhéj-parancsfájlok Linuxon való tanításához. A parancsfájlok hasznos részét képezik a rendszerfelügyeletnek, amely lehetővé teszi a rendszergazdák számára az ismétlődő feladatok elkerülését. Ebben a példában az osztály a hagyományos bash-parancsfájlokat és a továbbfejlesztett parancsfájlokat tartalmazza. A továbbfejlesztett parancsfájlok olyan parancsfájlok, amelyek a bash-parancsokat és a Ruby-t ötvözik. Ez a módszer lehetővé teszi a Ruby számára, hogy a Shell használatával kommunikáljon a környékre és bash-parancsokra. 

Az ilyen programozási osztályokat tartalmazó tanulók Linux rendszerű virtuális gépeket szereznek be a Linux alapjaihoz, és megismerhetik a bash rendszerhéj parancsfájljait is. A linuxos virtuális gépen engedélyezve van a távoli asztali hozzáférés, és telepítve van a [gedit](https://help.gnome.org/users/gedit/stable/) és a [Visual Studio Code](https://code.visualstudio.com/) szövegszerkesztő.

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja
A tesztkörnyezet beállításához Azure-előfizetésre van szükség a kezdéshez. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt. Ha már rendelkezik Azure-előfizetéssel, létrehozhat egy új Labor-fiókot Azure Lab Services vagy használhat egy meglévő Lab-fiókot is. A következő oktatóanyagban talál egy új Labor-fiókot: [oktatóanyag a labor-fiók beállításához](tutorial-setup-lab-account.md).

A labor-fiók létrehozása után engedélyezze a következő beállításokat a labor-fiókban: 

| Tesztkörnyezet-fiók beállítása | Utasítások |
| ----------- | ------------ |  
| Marketplace-rendszerképek | Engedélyezze az [Ubuntu Server 18,04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) -rendszerkép használatát a labor-fiókjában. További információ: a [piactéren elérhető rendszerképek megadása a labor-készítők](specify-marketplace-images.md)számára. | 

[Ezt az oktatóanyagot](tutorial-setup-classroom-lab.md) követve hozzon létre egy új labort, és alkalmazza a következő beállításokat:

| Tesztkörnyezet beállításai | Érték/utasítások | 
| ------------ | ------------------ |
| Virtuális gép (VM) mérete | Kicsi  |
| VM-rendszerkép | [Ubuntu Server 18,04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) |
| Távoli asztali kapcsolat engedélyezése | Engedélyezése. <p>A beállítás engedélyezése lehetővé teszi, hogy a tanárok és a diákok a távoli asztal (RDP) használatával csatlakozzanak a virtuális gépekhez. További információ: a [Távoli asztal engedélyezése a Linux rendszerű virtuális gépek számára a Azure Lab Services tesztkörnyezetben](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm). </p>|


## <a name="install-desktop-and-xrdp"></a>Az asztali és a xrdp telepítése
Az [ubuntu server 18,04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) -rendszerkép alapértelmezés szerint nem települ a távoli asztali kiszolgálóval. Az [Azure-beli Linux rendszerű virtuális géphez való kapcsolódáshoz a telepítés és konfigurálás távoli asztal](../../virtual-machines/linux/use-remote-desktop.md) utasításait követve telepítse a sablonhoz szükséges csomagokat a távoli asztal protokollon keresztül történő kapcsolódáshoz.

## <a name="install-ruby"></a>A Ruby telepítése
A Ruby egy nyílt forráskódú dinamikus nyelv, amely bash-parancsfájlok használatával kombinálható. Ez a szakasz bemutatja, hogyan használható a `apt-get` a [Ruby](https://www.ruby-lang.org/)legújabb verziójának telepítéséhez.

1. Telepítse a frissítéseket a következő parancsok futtatásával:

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  Telepítse a [rubyt](https://www.ruby-lang.org/).  A Ruby egy nyílt forráskódú dinamikus nyelv, amely bash-parancsfájlok használatával kombinálható. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>Fejlesztői eszközök telepítése
Ez a szakasz bemutatja, hogyan telepíthet néhány szövegszerkesztőt. A gedit a GNOME asztali környezet alapértelmezett szövegszerkesztője. Általános célú szövegszerkesztőként van kialakítva. A Visual Studio Code egy szövegszerkesztő, amely támogatja a hibakeresést és a verziókövetés integrálását.

> [!NOTE]
> Több különböző szövegszerkesztő is elérhető. A Visual Studio Code és a gedit csak két példát mutat be.

1. A [gedit](https://help.gnome.org/users/gedit/stable/)telepítése.

    ```bash
    sudo apt-get install gedit
    ```
1. Telepítse a [Visual Studio Code](https://code.visualstudio.com/)-ot.  A Visual Studio Code a Snap Store használatával telepíthető.  Alternatív telepítési lehetőségekért lásd: a [Visual Studio Code alternatív letöltése](https://code.visualstudio.com/#alt-downloads).

    ```bash
    sudo snap install vscode --classic 
    ```

    A sablon frissítve lett, és a tesztkörnyezet végrehajtásához szükséges programozási nyelv és fejlesztői eszközök is elérhetők. A sablon képe mostantól közzétehető a laborban. Kattintson a **Közzététel** gombra a sablon lapon a sablonnak a laborba való közzétételéhez.  

## <a name="cost"></a>Költségek 
Ha a labor költségeit szeretné megbecsülni, a következő példát használhatja:
 
Egy 25 tanulós osztály esetében, amely 20 órányi ütemezett időpontot és 10 órányi munkafeladatot vagy hozzárendelést használ, a labor ára a következő lesz: 

25 tanuló * (20 + 10) óra * 20 labor egység * 0,01 USD/óra = 150 USD

A díjszabással kapcsolatos további információkért tekintse meg a következő dokumentumot: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Összegzés
Ez a cikk végigvezeti a labornak a parancsfájlkezelési osztályokhoz való létrehozásának lépésein. Ez a cikk a Ruby Scripting Tools Linux rendszerű gépen való beállítására összpontosított. a telepítő más parancsfájl-osztályokhoz is használható, mint például a Python Linux rendszeren.

## <a name="next-steps"></a>Következő lépések
A következő lépések közösek a laborok beállításához:

- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail-regisztráció a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users). 





