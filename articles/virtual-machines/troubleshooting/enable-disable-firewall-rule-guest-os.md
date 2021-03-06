---
title: Tűzfalszabály engedélyezése vagy letiltása egy Azure-beli virtuális gépen futó vendég operációs rendszeren | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 782240c51833fc841af9f4260860db4c03897c03
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086449"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Tűzfalszabály engedélyezése vagy letiltása Azure-beli virtuális gép vendég operációs rendszeren

Ez a cikk egy olyan helyzet hibaelhárítását ismerteti, amelyben azt gyanítja, hogy a vendég operációs rendszer tűzfala a részleges forgalmat szűri a virtuális gépen (VM). Ez a következő okok miatt lehet hasznos:

*   Ha a tűzfal olyan módosítást hajtott végre, amely az RDP-kapcsolatok meghibásodását okozta, akkor az egyéni szkriptek bővítményének használatával elháríthatja a problémát.

*   Ha letiltja az összes tűzfal-profilt, az RDP-specifikus tűzfalszabályok beállításakor a rendszer nem támogatja a hibaelhárítást.

## <a name="solution"></a>Megoldás

A tűzfalszabályok konfigurálásának módja a szükséges virtuális géphez való hozzáférés szintjétől függ. Az alábbi példák RDP-szabályokat használnak. Ugyanezeket a metódusokat azonban más típusú forgalomra is alkalmazhatja, ha a megfelelő beállításkulcsot mutat.

### <a name="online-troubleshooting"></a>Online hibáinak elhárítása 

#### <a name="mitigation-1-custom-script-extension"></a>1\. enyhítés: Egyéni szkriptbővítmény

1.  Hozza létre a parancsfájlt a következő sablonnal.

    *   Szabály engedélyezése:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Szabály letiltásához:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Töltse fel ezt a szkriptet a Azure Portal az [egyéni parancsfájl-bővítmény](../extensions/custom-script-windows.md) funkció használatával. 

#### <a name="mitigation-2-remote-powershell"></a>2\. mérséklés: Távoli PowerShell

Ha a virtuális gép online állapotban van, és ugyanazon a virtuális hálózaton egy másik virtuális gépen is elérhető, a követést a másik virtuális gép használatával végezheti el.

1.  A hibaelhárítási virtuális gépen nyisson meg egy PowerShell-konzol ablakát.

2.  Szükség szerint futtassa az alábbi parancsokat.

    *   Szabály engedélyezése:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Szabály letiltásához:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>3\. enyhítés: PSTools parancsok

Ha a virtuális gép online állapotban van, és ugyanazon a virtuális hálózaton egy másik virtuális gépen is elérhető, a követést a másik virtuális gép használatával végezheti el.

1.  A hibaelhárítási virtuális gépen töltse le a [PsTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Nyisson meg egy CMD-példányt, és a belső IP-címén (DIP) keresztül férhet hozzá a virtuális géphez. 

    * Szabály engedélyezése:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Szabály letiltásához:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>4\. enyhítés: Távoli beállításjegyzék

Ha a virtuális gép online állapotban van, és ugyanazon a virtuális hálózaton található másik virtuális gépen is elérhető, akkor a másik virtuális GÉPEN is használhatja a [távoli beállításjegyzéket](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) .

1.  A hibaelhárítási virtuális gépen indítsa el a Beállításszerkesztőt (Regedit. exe), majd válassza a **file** > **Network Registry (fájl összekapcsolása**) lehetőséget.

2.  Nyissa meg a *célszámítógép*\SYSTEM ágat, majd adja meg a következő értékeket:

    * A szabály engedélyezéséhez nyissa meg a következő beállításértéket:
    
        *Célszámítógép*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-in-TCP
    
        Ezután módosítsa az **aktív = FALSE értéket** az **aktív = True** értékre a karakterláncban:

        **v 2.22 | Művelet = engedélyezés | Aktív = igaz | Dir = a | Protokoll = 6 | Profil = tartomány | Profil = Private | Profil = nyilvános | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = TermService | Név =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**
    
    * Egy szabály letiltásához nyissa meg a következő beállításértéket:
    
        *Célszámítógép*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-in-TCP

        Ezután módosítsa az **aktív = True** **értéket az aktív = false**értékre:
        
        **v 2.22 | Művelet = engedélyezés | Aktív = hamis | Dir = a | Protokoll = 6 | Profil = tartomány | Profil = Private | Profil = nyilvános | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = TermService | Név =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

3.  A módosítások alkalmazásához indítsa újra a virtuális gépet.

### <a name="offline-troubleshooting"></a>Offline hibaelhárítás 

Ha a virtuális gépet semmilyen módszerrel nem tudja elérni, az egyéni szkriptek bővítményének használata sikertelen lesz, és a rendszerlemezen keresztüli közvetlen munkavégzéssel OFFLINE módban kell működnie.

Mielőtt végrehajtaná ezeket a lépéseket, pillanatkép készítése a rendszerlemezt az érintett virtuális gép biztonsági mentéséhez. További információkért lásd: [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md).

1.  [A rendszer lemez csatolása egy helyreállítási virtuális Géphez](troubleshoot-recovery-disks-portal-windows.md).

2.  Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot.

3.  Győződjön meg arról, hogy a lemez megjelölt **Online** a Lemezkezelés konzol. Vegye figyelembe, hogy a csatlakoztatott rendszerlemezhez rendelt meghajtóbetűjel.

4.  A módosítások elvégzése előtt hozzon létre egy másolatot a \Windows\System32\Config mappából abban az esetben, ha a módosítások visszaállítására van szükség.

5.  A hibaelhárítási virtuális gépen indítsa el a Beállításszerkesztőt (Regedit. exe).

6.  Jelölje ki a **HKEY_LOCAL_MACHINE** kulcsot, majd válassza a **fájl** > **Load kaptár** elemet a menüből.

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Keresse meg, majd nyissa meg a \windows\system32\config\SYSTEM fájlt. 

    > [!Note]
    > A rendszer megkéri a nevet. Adja meg a **BROKENSYSTEM**, majd bontsa ki a **HKEY_LOCAL_MACHINE**elemet. Ekkor megjelenik egy **BROKENSYSTEM**nevű további kulcs. Ebben a hibaelhárításban a probléma-struktúrákat **BROKENSYSTEM**-ként csatlakoztatjuk.

8.  Hajtsa végre a következő módosításokat az BROKENSYSTEM ág esetében:

    1.  Győződjön meg arról, hogy a virtuális gép melyik **ControlSet** -kulcstól indul. A kulcs számát a HKLM\BROKENSYSTEM\Select\Current. fogja látni.

    2.  A szabály engedélyezéséhez nyissa meg a következő beállításértéket:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Ezután módosítsa az **aktív = FALSE értéket** **aktív = True**értékre.
        
        **v 2.22 | Művelet = engedélyezés | Aktív = igaz | Dir = a | Protokoll = 6 | Profil = tartomány | Profil = Private | Profil = nyilvános | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = TermService | Név =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

    3.  Egy szabály letiltásához nyissa meg a következő beállításkulcsot:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Ezután módosítsa az **aktív = True** **értéket aktív = false**értékre.
        
        **v 2.22 | Művelet = engedélyezés | Aktív = hamis | Dir = a | Protokoll = 6 | Profil = tartomány | Profil = Private | Profil = nyilvános | LPort = 3389 | App =%SystemRoot%\system32\svchost.exe | SVC = TermService | Név =\@FirewallAPI. dll,-28775 | Desc =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

9.  Jelölje ki a **BROKENSYSTEM**, majd a menüből válassza ki a **fájl** > **kitöltése struktúrát** .

10. [Válassza le a rendszer lemezt, és hozza létre újból a virtuális gép](troubleshoot-recovery-disks-portal-windows.md).

11. Ellenőrizze, hogy a probléma megoldódott.
