---
title: Oktatóanyag – alapszintű Active Directory helyszíni és Azure AD-környezetekben.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 356a05d4d92f17ceb66ff0208153ec3eac736757
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793897"
---
# <a name="tutorial-basic-active-directory-environment"></a>Oktatóanyag: alapszintű Active Directory környezet

Ez az oktatóanyag végigvezeti egy alapszintű Active Directory környezet létrehozásán. 

![Létrehozás](media/tutorial-single-forest/diagram1.png)

Az oktatóanyagban létrehozott környezettel tesztelheti a hibrid identitási forgatókönyvek különböző szempontjait, és néhány oktatóanyag előfeltételként is használható.  Ha már rendelkezik meglévő Active Directory-környezettel, ezt helyettesítőként használhatja.  Ezek az adatok olyan személyeknek szólnak, akik nem a semmiből indulnak.

Ez az oktatóanyag a következő elemekből áll
## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következő előfeltételek szükségesek
- [Hyper-V-](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) t futtató számítógép.  Ezt a [Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) vagy [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) rendszerű számítógépeken javasolt elvégezni.
- Egy [külső hálózati adapter](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) , amely lehetővé teszi, hogy a virtuális gép kommunikáljon az internettel.
- [Azure-előfizetés](https://azure.microsoft.com/free)
- A Windows Server 2016 másolata
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)

> [!NOTE]
> Ez az oktatóanyag PowerShell-parancsfájlokat használ, hogy a leggyorsabb idő alatt létre tudja hozni az oktatóanyag-környezetet.  Mindegyik parancsfájl a parancsfájlok elején deklarált változókat használ.  A változókat a környezetnek megfelelően módosítani lehet.
>
>A szkriptek a Azure AD Connect Cloud kiépítési ügynök telepítése előtt általános Active Directory környezetet hoznak létre.  Ezek minden oktatóanyaghoz relevánsak.
>
> Az oktatóanyagban használt PowerShell-parancsfájlok példányai [itt](https://github.com/billmath/tutorial-phs)érhetők el a githubon.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Az első teendő, hogy a hibrid identitás-környezet felépítésének és működtetésének megkezdéséhez hozzon létre egy virtuális gépet, amelyet a helyszíni Active Directory-kiszolgálóként fog használni.  Tegye a következőket:

1. Nyissa meg rendszergazdaként a PowerShell ISE-t.
2. Futtassa az alábbi szkriptet.

    ```powershell
    #Declare variables
    $VMName = 'DC1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\DC1\DC1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
    ```

## <a name="complete-the-operating-system-deployment"></a>Az operációs rendszer központi telepítésének befejezése
A virtuális gép létrehozásának befejezéséhez be kell fejeznie az operációs rendszer telepítését.

1. Hyper-V kezelője, kattintson duplán a virtuális gépre
2. Kattintson a Start gombra.
3. A rendszer felszólítja, hogy a CD-ről vagy DVD-ről történő rendszerindításhoz nyomja le bármelyik billentyűt. Ugorjon erre.
4. A Windows Server Start up képernyőn válassza ki a nyelvet, és kattintson a **tovább**gombra.
5. Kattintson a **Telepítés most**lehetőségre.
6. Adja meg a licenckulcs, és kattintson a **tovább**gombra.
7. Jelölje be a * * Elfogadom a licencfeltételeket, majd kattintson a **tovább**gombra.
8. Válassza az **Egyéni: csak a Windows telepítése (speciális) lehetőséget.**
9. Kattintson a **Tovább** gombra
10. Miután a telepítés befejeződött, indítsa újra a virtuális gépet, jelentkezzen be, és futtassa a Windows-frissítéseket, hogy a virtuális gép a legnaprakészebb legyen.  Telepítse a legújabb frissítéseket.

## <a name="install-active-directory-prerequisites"></a>Active Directory Előfeltételek telepítése
Most, hogy már rendelkezik virtuális géppel, a Active Directory telepítése előtt végre kell hajtania néhány dolgot.  Éppen ezért át kell neveznie a virtuális gépet, meg kell adnia egy statikus IP-címet és a DNS-adatokat, és telepítenie kell a Távoli kiszolgálófelügyelet eszközeit.   Tegye a következőket:

1. Nyissa meg rendszergazdaként a PowerShell ISE-t.
2. Futtassa az alábbi szkriptet.

    ```powershell
    #Declare variables
    $ipaddress = "10.0.1.117" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.117"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "DC1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    # Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```

## <a name="create-a-windows-server-ad-environment"></a>Windows Server AD-környezet létrehozása
Most, hogy létrehozta a virtuális gépet, és átnevezte, és statikus IP-címmel rendelkezik, megteheti a Active Directory tartományi szolgáltatások telepítését és konfigurálását.  Tegye a következőket:

1. Nyissa meg rendszergazdaként a PowerShell ISE-t.
2. Futtassa az alábbi szkriptet.

    ```powershell 
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "contoso.com"
    $DomaninNetBIOSName = "CONTOSO"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

## <a name="create-a-windows-server-ad-user"></a>Windows Server AD-felhasználó létrehozása
Most, hogy már rendelkezik Active Directory-környezettel, tesztelési fiókkal kell rendelkeznie.  Ez a fiók a helyszíni AD-környezetben jön létre, majd szinkronizálva lesz az Azure AD-vel.  Tegye a következőket:

1. Nyissa meg rendszergazdaként a PowerShell ISE-t.
2. Futtassa az alábbi szkriptet.

    ```powershell 
    # Filename:    4_CreateUser.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Allie"
    $Surname = "McCray"
    $Displayname = "Allie McCray"
    $Name = "amccray"
    $Password = "Pass1w0rd"
    $Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```


## <a name="create-an-azure-ad-tenant"></a>Azure AD-bérlő létrehozása
Most létre kell hoznia egy Azure AD-bérlőt, hogy szinkronizálni tudja a felhasználókat a felhőben.  Új Azure AD-bérlő létrehozásához tegye a következőket.

1. Keresse meg a [Azure Portal](https://portal.azure.com) , és jelentkezzen be egy Azure-előfizetéssel rendelkező fiókkal.
2. Válassza a **plusz ikont (+)** , és keressen rá **Azure Active Directory**.
3. A keresési eredmények között válassza a **Azure Active Directory** lehetőséget.
4. Kattintson a **Létrehozás** gombra.</br>
![Létrehozás](media/tutorial-single-forest/create1.png)</br>
5. Adja **meg a szervezet nevét** a **kezdeti tartománynévvel**együtt. Ezután kattintson a **Létrehozás** elemre. Ezzel létrehozza a könyvtárat.
6. Miután ez befejeződik, kattintson az **ide** hivatkozásra a címtár kezeléséhez.

## <a name="create-a-global-administrator-in-azure-ad"></a>Globális rendszergazda létrehozása az Azure AD-ben
Most, hogy rendelkezik egy Azure AD-Bérlővel, létre fog hozni egy globális rendszergazdai fiókot.  A globális rendszergazdai fiók létrehozásához tegye a következőket.

1.  A **Kezelés** alatt válassza a **Felhasználókat**.</br>
![Létrehozás](media/tutorial-single-forest/administrator1.png)</br>
2.  Válassza **a minden felhasználó** , majd az **+ új felhasználó**lehetőséget.
3.  Adja meg a felhasználó nevét és felhasználónevét. Ez lesz a bérlő globális rendszergazdája. A **címtárbeli szerepkört** a **globális rendszergazdára** is módosítani kívánja. Megjelenítheti az ideiglenes jelszót is. Ha elkészült, válassza a **Létrehozás**lehetőséget.</br>
![Létrehozás](media/tutorial-single-forest/administrator2.png)</br>
4. Ha ez befejeződik, nyisson meg egy új webböngészőt, és jelentkezzen be a myapps.microsoft.com-be az új globális rendszergazdai fiókkal és az ideiglenes jelszóval.
5. Módosítsa a globális rendszergazda jelszavát úgy, hogy megjegyezzen.

## <a name="optional--additional-server-and-forest"></a>Nem kötelező: további kiszolgáló és erdő
A következő egy választható szakasz, amely egy további kiszolgáló és vagy erdő létrehozásának lépéseit ismerteti.  Ez néhány fejlettebb oktatóanyagban használható, például a [próbaüzem Azure ad Connect a felhőbe való kiépítés](tutorial-pilot-aadc-aadccp.md)során.

Ha csak további kiszolgálóra van szüksége, akkor a- **create The Virtual Machine (virtuális gép létrehozása** ) lépés után leállíthatja a kiszolgálót, és csatlakoztathatja a kiszolgálót a fent létrehozott meglévő tartományhoz.  

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. Nyissa meg rendszergazdaként a PowerShell ISE-t.
2. Futtassa az alábbi szkriptet.

    ```powershell
    # Filename:    1_CreateVM_CP.ps1
    # Description: Creates a VM to be used in the tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. #This script is made available to you without any express, implied or statutory warranty, not even the implied warranty of merchantability or fitness for a particular purpose, or the warranty of title or non-infringement. The entire risk of the use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $VMName = 'CP1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\CP1\CP1.vhdx'
    $VHDSize = '64424509440'

    #Create New Virtual Machine
    New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

    #Set the memory to be non-dynamic
    Set-VMMemory $VMName -DynamicMemoryEnabled $false

    #Add DVD Drive to Virtual Machine
    Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

    #Mount Installation Media
    $DVDDrive = Get-VMDvdDrive -VMName $VMName

    #Configure Virtual Machine to Boot from DVD
    Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive
    ```

### <a name="complete-the-operating-system-deployment"></a>Az operációs rendszer központi telepítésének befejezése
A virtuális gép létrehozásának befejezéséhez be kell fejeznie az operációs rendszer telepítését.

1. Hyper-V kezelője, kattintson duplán a virtuális gépre
2. Kattintson a Start gombra.
3. A rendszer felszólítja, hogy a CD-ről vagy DVD-ről történő rendszerindításhoz nyomja le bármelyik billentyűt. Ugorjon erre.
4. A Windows Server Start up képernyőn válassza ki a nyelvet, és kattintson a **tovább**gombra.
5. Kattintson a **Telepítés most**lehetőségre.
6. Adja meg a licenckulcs, és kattintson a **tovább**gombra.
7. Jelölje be a * * Elfogadom a licencfeltételeket, majd kattintson a **tovább**gombra.
8. Válassza az **Egyéni: csak a Windows telepítése (speciális) lehetőséget.**
9. Kattintson a **Tovább** gombra
10. Miután a telepítés befejeződött, indítsa újra a virtuális gépet, jelentkezzen be, és futtassa a Windows-frissítéseket, hogy a virtuális gép a legnaprakészebb legyen.  Telepítse a legújabb frissítéseket.

### <a name="install-active-directory-prerequisites"></a>Active Directory Előfeltételek telepítése
Most, hogy már rendelkezik virtuális géppel, a Active Directory telepítése előtt végre kell hajtania néhány dolgot.  Éppen ezért át kell neveznie a virtuális gépet, meg kell adnia egy statikus IP-címet és a DNS-adatokat, és telepítenie kell a Távoli kiszolgálófelügyelet eszközeit.   Tegye a következőket:

1. Nyissa meg rendszergazdaként a PowerShell ISE-t.
2. Futtassa az alábbi szkriptet.

    ```powershell
    # Filename:    2_ADPrep_CP.ps1
    # Description: Prepares your environment for Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $ipaddress = "10.0.1.118" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.118"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "CP1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    #Set the DNS servers
    Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

    #Rename the computer 
    Rename-Computer -NewName $newname -force 

    #Install features 
    New-Item $featureLogPath -ItemType file -Force 
    Add-WindowsFeature $addsTools 
    Get-WindowsFeature | Where installed >>$featureLogPath 

    #Restart the computer 
    Restart-Computer
    ```
### <a name="create-a-windows-server-ad-environment"></a>Windows Server AD-környezet létrehozása
Most, hogy létrehozta a virtuális gépet, és átnevezte, és statikus IP-címmel rendelkezik, megteheti a Active Directory tartományi szolgáltatások telepítését és konfigurálását.  Tegye a következőket:

1. Nyissa meg rendszergazdaként a PowerShell ISE-t.
2. Futtassa az alábbi szkriptet.

    ```powershell
    # Filename:    3_InstallAD_CP.ps1
    # Description: Creates an on-premises AD envrionment.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "fabrikam.com"
    $DomaninNetBIOSName = "FABRIKAM"
    $ForestMode = "WinThreshold"
    $LogPath = "c:\windows\NTDS"
    $SysVolPath = "c:\windows\SYSVOL"
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $Password = "Pass1w0rd"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

    #Install AD DS, DNS and GPMC 
    start-job -Name addFeature -ScriptBlock { 
    Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
    Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
    Wait-Job -Name addFeature 
    Get-WindowsFeature | Where installed >>$featureLogPath

    #Create New AD Forest
    Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
    ```

### <a name="create-a-windows-server-ad-user"></a>Windows Server AD-felhasználó létrehozása
Most, hogy már rendelkezik Active Directory-környezettel, tesztelési fiókkal kell rendelkeznie.  Ez a fiók a helyszíni AD-környezetben jön létre, majd szinkronizálva lesz az Azure AD-vel.  Tegye a következőket:

1. Nyissa meg rendszergazdaként a PowerShell ISE-t.
2. Futtassa az alábbi szkriptet.

    ```powershell 
    # Filename:    4_CreateUser_CP.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Anna"
    $Surname = "Ringdal"
    $Displayname = "Anna Ringdal"
    $Name = "aringdal"
    $Password = "Pass1w0rd"
    $Identity = "CN=aringdal,CN=Users,DC=fabrikam,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```

## <a name="conclusion"></a>Összegzés
Most már rendelkezik egy olyan környezettel, amely használható a meglévő oktatóanyagokhoz, és tesztelheti a további funkciókat a felhőalapú kiépítés terén.

## <a name="next-steps"></a>Következő lépések 

- [Mi a kiépítés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud kiépítés?](what-is-cloud-provisioning.md)
