---
title: Távoli helyszíni erőforrások kezelése PowerShell-függvények használatával
description: Megtudhatja, hogyan konfigurálhat Hibrid kapcsolatok a Azure Relayban egy PowerShell-függvény alkalmazás helyszíni erőforrásokhoz való összekapcsolásához, amelyet aztán a helyszíni erőforrás távoli kezelésére használhat.
author: eamono
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 36fc4c873dccfe9fa814bddccd829ed04207f095
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226937"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Hibrid környezetek kezelése a PowerShell-lel Azure Functions és App Service Hibrid kapcsolatok

A Azure App Service Hibrid kapcsolatok funkció lehetővé teszi a más hálózatokban lévő erőforrásokhoz való hozzáférést. Erről a képességről a [hibrid kapcsolatok](../app-service/app-service-hybrid-connections.md) dokumentációjában olvashat bővebben. Ez a cikk azt ismerteti, hogyan használhatja ezt a képességet a helyszíni kiszolgálókat célzó PowerShell-függvények futtatásához. Ezzel a kiszolgálóval a helyszíni környezet összes erőforrását egy Azure PowerShell függvényből lehet kezelni.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Helyszíni kiszolgáló konfigurálása a PowerShell táveléréshez

A következő parancsfájl lehetővé teszi a PowerShell távelérését, és létrehoz egy új tűzfalszabály és egy WinRM HTTPS-figyelőt. Tesztelési célból önaláírt tanúsítványt használ a rendszer. Éles környezetben javasoljuk, hogy aláírt tanúsítványt használjon.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>PowerShell-függvény alkalmazás létrehozása a portálon

A App Service Hibrid kapcsolatok funkció csak az alapszintű, a standard és az elkülönített díjszabási csomagokban érhető el. Ha a Function alkalmazást a PowerShell-lel hozza létre, hozzon létre vagy válasszon ki egyet az alábbi csomagok közül.

1. A [Azure Portal](https://portal.azure.com)válassza az **+ erőforrás létrehozása** lehetőséget a bal oldali menüben, majd válassza a **Function app**elemet.

1. A **üzemeltetési terv**területen válassza a **app Service terv**lehetőséget, majd válassza a **app Service terv/hely**lehetőséget.

1. Válassza az **új létrehozása**elemet, írja be a **app Servicei csomag** nevét, válasszon egy **helyet** az Ön közelében lévő [régióban](https://azure.microsoft.com/regions/) vagy a funkciókhoz hozzáférő egyéb szolgáltatások közelében, majd válassza az **árképzési szintet**.

1. Válassza ki az S1 standard csomagot, majd kattintson az **alkalmaz**gombra.

1. A terv létrehozásához kattintson **az OK gombra** , majd adja meg a fennmaradó **függvényalkalmazás** beállításokat a táblázatban megadott módon, közvetlenül a következő képernyőkép után:

    ![PowerShell Core Function-alkalmazás](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvényes karakterek: `a-z`, `0-9` és `-`.  | 
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. |
    | **Erőforráscsoport** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. Használhatja a javasolt értéket is. |
    | **OS** | Előnyben részesített operációs rendszer | Válassza a Windows lehetőséget. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válassza a PowerShell mag lehetőséget. |
    | **Tárolás** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A Storage-fiókok nevének 3 és 24 karakter közöttinek kell lennie, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat.
    | **Application Insights** | Alapértelmezett | Létrehoz egy Application Insights erőforrást ugyanahhoz az *alkalmazáshoz* a legközelebbi támogatott régióban. A beállítás kibontásával módosíthatja az **új erőforrás nevét** , vagy kiválaszthat egy másik **helyet** az [Azure földrajz](https://azure.microsoft.com/global-infrastructure/geographies/) régiójában, ahol az adatait tárolni szeretné. |

1. A Beállítások ellenőrzése után válassza a **Létrehozás**lehetőséget.

1. Válassza ki az **értesítés** ikont a portál jobb felső sarkában, és várja meg az "üzembe helyezés sikeres" üzenetet.

1. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget. Kiválaszthatja **a rögzítés az irányítópulton**lehetőséget is. A rögzítéssel egyszerűbbé válik a Function app-erőforráshoz való visszatérés az irányítópultról.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Hibrid kapcsolatok létrehozása a Function alkalmazáshoz

A hibrid kapcsolatok konfigurálása a Function alkalmazás hálózatkezelési szakasza alapján történik:

1. Válassza a **platform szolgáltatások** fület a Function alkalmazásban, majd válassza a **hálózatkezelés**lehetőséget. 
   a platform hálózatkezelésének ![az alkalmazások áttekintése](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Válassza **a hibrid kapcsolatok végpontok konfigurálása**lehetőséget.
   ![Hálózat](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Válassza a **hibrid kapcsolatok hozzáadása**lehetőséget.
   ![hibrid kapcsolatok](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Adja meg a hibrid kapcsolatok adatait, amint az a következő képernyőképen látható. Lehetősége van arra, hogy a **végpont gazdagépének** beállításai megegyezzenek a helyszíni kiszolgáló állomásneve, hogy a kiszolgáló később is megjegyezze a távoli parancsok futtatásakor. A port megegyezik a kiszolgálón korábban definiált alapértelmezett Windows távfelügyeleti szolgáltatás portjával.
  ![hibrid kapcsolatok hozzáadása](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Hibrid kapcsolatok neve**: ContosoHybridOnPremisesServer
    
    **Végponti gazdagép**: finance1
    
    **Végpont portja**: 5986
    
    **Servicebus-névtér**: új létrehozása
    
    **Hely**: válasszon ki egy elérhető helyet
    
    **Név**: contosopowershellhybrid

5. A hibrid kapcsolatok létrehozásához kattintson **az OK gombra** .

## <a name="download-and-install-the-hybrid-connection"></a>A hibrid kapcsolatok letöltése és telepítése

1. Válassza a **Csatlakozáskezelő letöltése** lehetőséget, hogy az. msi fájlt helyileg mentse a számítógépre.
![a telepítő letöltése](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Másolja az. msi fájlt a helyi számítógépről a helyszíni kiszolgálóra.
1. Futtassa a hibridkapcsolat-kezelő telepítőjét a szolgáltatás a helyszíni kiszolgálón való telepítéséhez.
![hibrid kapcsolatok telepítése](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. A portálon nyissa meg a hibrid kapcsolatokat, majd másolja az átjáró kapcsolódási karakterláncát a vágólapra.
![a hibrid kapcsolatok karakterláncának másolása](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Nyissa meg a hibridkapcsolat-kezelő felhasználói felületet a helyszíni kiszolgálón.
![a hibrid kapcsolatok felhasználói felületének megnyitása](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Válassza az **ENTER manuálisan** gombot, és illessze be a kapcsolatok karakterláncát a vágólapról.
![a kapcsolatok beillesztése](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Ha nem csatlakoztatottként jelenik meg, indítsa újra a hibridkapcsolat-kezelő a PowerShellből.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Alkalmazás-beállítás létrehozása rendszergazdai fiók jelszavához

1. Válassza a **platform szolgáltatások** fület a Function alkalmazásban.
1. Az **általános beállítások**területen válassza a **Konfigurálás**lehetőséget.
![a platform konfigurációjának kiválasztása](./media/functions-hybrid-powershell/select-configuration.png)  
1. Az **új Alkalmazásbeállítás** kibontásával új beállítást hozhat létre a jelszóhoz.
1. Nevezze el a beállítás _ContosoUserPassword_, és adja meg a jelszót.
1. Válassza az **OK** , majd a mentés lehetőséget a jelszó tárolásához a Function alkalmazásban.
![a jelszóhoz tartozó alkalmazás-beállítás hozzáadása](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Function http-trigger létrehozása teszteléshez

1. Hozzon létre egy új HTTP-trigger függvényt a Function alkalmazásból.
![új HTTP-trigger létrehozása](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Cserélje le a PowerShell-kódot a sablonból a következő kódra:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

3. A függvény teszteléséhez válassza a **Mentés** és **Futtatás** lehetőséget.
![tesztelje a Function alkalmazást](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>Más rendszerek helyszíni kezelése

A csatlakoztatott helyszíni kiszolgáló használatával kapcsolódhat a helyi környezetben lévő más kiszolgálókhoz és felügyeleti rendszerekhez. Így a PowerShell-függvények segítségével kezelheti az adatközpont-műveleteket az Azure-ban. A következő parancsfájl egy PowerShell-konfigurációs munkamenetet regisztrál, amely a megadott hitelesítő adatok alatt fut. A hitelesítő adatoknak a távoli kiszolgálók rendszergazdájának kell lenniük. Ezt a konfigurációt használhatja a helyi kiszolgálón vagy adatközpontban található többi végpont eléréséhez.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

Cserélje le a parancsfájlban az alábbi változókat a környezet megfelelő értékeire:
* $HybridEndpoint
* $RemoteServer

Az előző két forgatókönyvben a Azure Functions és Hibrid kapcsolatok PowerShell használatával csatlakozhat a helyszíni környezetekhez, és kezelheti azokat. Javasoljuk, hogy ismerkedjen meg a [functions](./functions-reference-powershell.md) [hibrid kapcsolatok](../app-service/app-service-hybrid-connections.md) és PowerShell szolgáltatásával.

Az Azure [Virtual Network](./functions-create-vnet.md) szolgáltatással a helyszíni környezethez is csatlakozhat Azure Functionson keresztül.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [További információ a PowerShell-függvények használatáról](functions-reference-powershell.md)
