---
title: Runbook bemeneti paraméterei
description: A Runbook bemeneti paraméterei növelhetik a runbookok rugalmasságát azáltal, hogy lehetővé teszik az adatok átadását egy Runbook az indításakor. Ez a cikk különböző forgatókönyveket ismertet, amelyekben a bemeneti paraméterek a runbookok-ben használatosak.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 17be351d4af3d277242af70ea96e8735a5f68bc9
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78329085"
---
# <a name="runbook-input-parameters"></a>Runbook bemeneti paraméterei

A Runbook bemeneti paraméterei növelhetik a Runbook rugalmasságát azáltal, hogy lehetővé teszik az adatok átadását az indításkor. Ezek a paraméterek lehetővé teszik, hogy a runbook műveletek konkrét forgatókönyveket és környezeteket célozzanak. Ez a cikk a bemeneti paraméterek konfigurálását és használatát ismerteti a runbookok.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="configuring-input-parameters"></a>Bemeneti paraméterek konfigurálása

Beállíthatja a PowerShell, a PowerShell-munkafolyamat, a grafikus és a Python-runbookok bemeneti paramétereit. A runbook több, különböző adattípusú paraméterrel rendelkezhetnek, vagy nincsenek paraméterek. A bemeneti paraméterek kötelezőek vagy nem kötelezőek, és az opcionális paraméterek alapértelmezett értékeit is használhatja.

Az értékeket a runbook tartozó bemeneti paraméterekhez rendelheti hozzá az indításakor. A runbook elindíthatja a Azure Portal, egy webszolgáltatásból vagy a PowerShellből. Azt is megteheti, hogy az egyiket alárendelt runbook, amely egy másik runbook inline néven szerepel.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Bemeneti paraméterek konfigurálása a PowerShell-runbookok

A PowerShell és a PowerShell munkafolyamat-runbookok Azure Automation támogatja a következő tulajdonságokkal megadott bemeneti paramétereket. 

| **Tulajdonság** | **Leírás** |
|:--- |:--- |
| Típus |Kötelező. A paraméter értékének várt adattípusa. Bármely .NET-típus érvényes. |
| Name (Név) |Kötelező. A paraméter neve. Ennek a névnek egyedinek kell lennie a runbook belül, betűvel kell kezdődnie, és csak betűket, számokat vagy aláhúzás karaktereket tartalmazhat. |
| Kötelező |Választható. Logikai érték, amely azt határozza meg, hogy a paraméter igényel-e értéket. Ha ez **igaz**értékre van állítva, a runbook indításakor meg kell adni egy értéket. Ha ezt a beállítást **hamis**értékre állítja, nem kötelező megadni egy értéket. Ha nem ad meg értéket a **kötelező** tulajdonsághoz, a PowerShell alapértelmezés szerint nem kötelezővé teszi a bemeneti paramétert. |
| Alapértelmezett érték |Választható. A paraméterhez használt érték, ha a runbook indításakor nem adja át a bemeneti értéket. A runbook bármely paraméterhez beállíthat alapértelmezett értéket. |

A Windows PowerShell támogatja a fentiekben felsorolt bemeneti paraméterek több attribútumát, például az érvényesítést, az aliasokat és a paramétereket. A Azure Automation azonban jelenleg csak a felsorolt bemeneti paraméterek tulajdonságait támogatja.

Tegyük fel például, hogy egy PowerShell-munkafolyamat runbook egy paraméter-definíciót keresünk. Ennek a definíciónak a következő általános formája van, ahol több paramétert vesszővel elválasztva.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Most konfigurálja a bemeneti paramétereket egy olyan PowerShell-munkafolyamati runbook, amely a virtuális gépek részleteit, vagy egyetlen virtuális gépet vagy egy erőforráscsoport összes virtuális gépét kiírja. Ez a runbook két paraméterrel rendelkezik, ahogy az a következő képernyőképen látható: a virtuális gép neve (*VMName*) és az erőforráscsoport neve (*resourceGroupName*).

![Automation PowerShell-munkafolyamat](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

Ebben a paraméter-definícióban a bemeneti paraméterek karakterlánc típusú egyszerű paraméterek.

Vegye figyelembe, hogy a PowerShell és a PowerShell-munkafolyamat runbookok támogatja az összes egyszerű típust és összetett típust, például az **Object** vagy a **PSCredential** a bemeneti paraméterekhez. Ha a runbook objektum-bemeneti paraméterrel rendelkezik, a név-érték párokkal rendelkező PowerShell-szórótábla kell átadnia egy értékre. Tegyük fel például, hogy a következő paraméter szerepel egy runbook.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

Ebben az esetben a következő értéket adhatja át a paraméternek.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Ha nem adja át az értéket egy opcionális karakterlánc-paraméternek null alapértelmezett értékkel, akkor a paraméter értéke üres karakterlánc a **Null**helyett.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Bemeneti paraméterek konfigurálása grafikus runbookok

A grafikus runbook tartozó bemeneti paraméterek konfigurációjának szemléltetéséhez hozzon létre egy runbook, amely a virtuális gépek részleteit jeleníti meg, vagy egyetlen virtuális gépet vagy egy erőforráscsoport összes virtuális gépét. Részletekért tekintse meg [az első grafikus runbook](automation-first-runbook-graphical.md).

A grafikus runbook ezeket a főbb runbook tevékenységeket használják:

* Azure-beli futtató fiók konfigurációja az Azure-beli hitelesítéshez. 
* A [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) parancsmag definíciója a virtuális gép tulajdonságainak lekéréséhez.
* A [Write-output](/powershell/module/microsoft.powershell.utility/write-output) tevékenység használata a virtuális gépek nevének kinyomtatásához. 

A **Get-AzVM** tevékenység két bemenetet definiál, a virtuális gép nevét és az erőforráscsoport nevét. Mivel ezek a nevek a runbook elindulásakor különbözőek lehetnek, a bemeneti paramétereket hozzá kell adnia a runbook, hogy elfogadja ezeket a bemeneteket. Tekintse meg [Azure Automation grafikus szerzői](automation-graphical-authoring-intro.md)műveleteit.

A bemeneti paraméterek konfigurálásához kövesse az alábbi lépéseket.

1. A **runbookok** lapon válassza ki a grafikus runbook, majd kattintson a **Szerkesztés**gombra.
2. A grafikus szerkesztőben kattintson a **bemenet és kimenet** gombra, majd **adja hozzá a bemenetet** a Runbook bemeneti paraméter panel megnyitásához.

   ![Automation grafikus runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. A bemeneti és kimeneti vezérlő megjeleníti a runbook meghatározott bemeneti paraméterek listáját. Itt hozzáadhat egy új bemeneti paramétert, vagy szerkesztheti a meglévő bemeneti paraméterek konfigurációját. Ha új paramétert szeretne felvenni a runbook, kattintson a **bemenet hozzáadása** gombra a **runbook bemeneti paraméter** panel megnyitásához, ahol a paramétereket a [grafikus szerzői műveletek Azure Automationban](automation-graphical-authoring-intro.md)definiált tulajdonságok használatával konfigurálhatja.

    ![Új bemenet hozzáadása](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Hozzon létre két paramétert a **Get-AzVM** tevékenység által használandó alábbi tulajdonságokkal, majd kattintson **az OK**gombra.

   * 1\. paraméter:
        * **Név** -- **VMName**
        * **Típus** – sztring
        * **Kötelező** -- **nem**

   * 2\. paraméter:
        * **Név** -- **resourceGroupName**
        * **Típus** – sztring
        * **Kötelező** -- **nem**
        * **Alapértelmezett érték** -- **Egyéni**
        * Egyéni alapértelmezett érték – a virtuális gépeket tartalmazó erőforráscsoport neve

5. Megtekintheti a bemeneti és a kimeneti vezérlő paramétereit. 
6. Kattintson ismét **az OK gombra** , majd kattintson a **Mentés**gombra.
7. A runbook közzétételéhez kattintson a **Közzététel** gombra.

### <a name="configure-input-parameters-in-python-runbooks"></a>Bemeneti paraméterek konfigurálása a Python runbookok

A PowerShell-lel, a PowerShell-munkafolyamatokkal és a grafikus runbookok ellentétben a Python runbookok nem fogad el nevesített paramétereket. A runbook-szerkesztő az összes bemeneti paramétert argumentum-érték tömbként elemzi. A tömböt úgy érheti el, ha importálja a **sys** modult a Python-parancsfájlba, majd a **sys. argv** tömb használatával. Fontos megjegyezni, hogy a tömb első eleme `sys.argv[0]`, a parancsfájl neve. Ezért az első tényleges bemeneti paraméter a *sys. argv [1]* .

Egy Python-runbook található bemeneti paraméterek használatával kapcsolatos példát a [Azure Automation első Python-runbook](automation-first-runbook-textual-python2.md)talál.

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>Értékek kiosztása bemeneti paraméterekhez a runbookok-ben

Ez a szakasz számos módszert ismertet az értékek bemeneti paraméterekbe való átadásához a runbookok-ben. A paraméterek értékét az alábbiak szerint rendelheti hozzá:

* [Runbook indítása](#start-a-runbook-and-assign-parameters)
* [Runbook tesztelése](#test-a-runbook-and-assign-parameters)
* [Ütemterv csatolása a runbook](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Webhook létrehozása a runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Runbook elindítása és paraméterek kiosztása

A runbook többféleképpen is elindíthatók: a Azure Portalon, webhooktal, PowerShell-parancsmagokkal, a REST API vagy az SDK-val. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Közzétett runbook elindítása a Azure Portal és a paraméterek hozzárendelésével

Amikor [elindítja a runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) a Azure Portalban, megnyílik a **runbook indítása** panel, amelyen megadhatja a létrehozott paraméterek értékeit.

![A portál használatának megkezdése](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

A beviteli mező alatti címkében láthatja, hogy milyen tulajdonságokat állítottak be a paraméter attribútumainak definiálásához, például kötelező vagy opcionális, típus, alapértelmezett érték. A paraméter neve melletti Súgó buborék a paraméterek bemeneti értékeivel kapcsolatos döntések meghozatalához szükséges legfontosabb információkat is meghatározza. 

> [!NOTE]
> A karakterlánc-paraméterek támogatják a String típusú üres értékeket. A (z) **[EmptyString]** megadása a bemeneti paraméter mezőben üres karakterláncot ad át a paraméternek. Emellett a karakterlánc-paraméterek nem támogatják a null értéket. Ha nem adott meg értéket egy karakterlánc-paraméternek, a PowerShell null értékként értelmezi.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Közzétett runbook elindítása PowerShell-parancsmagok használatával és paraméterek kiosztása

* **Azure Resource Manager parancsmagok:** A [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
)használatával elindíthat egy olyan Automation-runbook, amely egy erőforráscsoporthoz lett létrehozva.

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Klasszikus Azure üzembehelyezési modell-parancsmagok:** A [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook)használatával elindíthat egy alapértelmezett erőforráscsoporthoz létrehozott Automation-runbook.
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Amikor PowerShell-parancsmagokkal indítja el a runbook, a rendszer egy alapértelmezett paramétert ( *MicrosoftApplicationManagementStartedBy*) hoz létre a **PowerShell**értékkel. Ezt a paramétert a feladatok részletei ablaktábláján tekintheti meg.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Runbook elindítása SDK használatával és paraméterek kiosztása

* **Azure Resource Manager metódus:** A runbook a programozási nyelv SDK használatával indítható el. Az alábbiakban egy C# kódrészletet talál az Automation-fiókban lévő runbook elindításához. A [GitHub-tárházban](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)található összes kód megtekinthető.  

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **Klasszikus Azure üzembe helyezési modell módszere:** A runbook a programozási nyelv SDK használatával indítható el. Az alábbiakban egy C# kódrészletet talál az Automation-fiókban lévő runbook elindításához. A [GitHub-tárházban](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)található összes kód megtekinthető.

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   A metódus elindításához hozzon létre egy szótárt, amely a runbook-paramétereket *VMName* és *resourceGroupName* , valamint azok értékeit tárolja. Ezután indítsa el a runbook. Alább látható C# a fentiekben megadott metódus meghívásához használt kódrészlet.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Runbook elindítása a REST API és a paraméterek hozzárendelésével

Létrehozhat és elindíthat egy runbook-feladatot a Azure Automation REST API a **put** metódussal a következő kérelem URI-val: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

A kérelem URI azonosítójában cserélje le a következő paramétereket:

* *subscriptionId*: az Azure-előfizetés azonosítója.  
* *resourceGroupName*: az Automation-fiókhoz tartozó erőforráscsoport neve.
* *automationAccountName*: a megadott felhőalapú szolgáltatáson belül futtatott Automation-fiók neve.  
* *jobName*: a feladatokhoz tartozó GUID. A PowerShell GUID azonosítói a `[GUID]::NewGuid().ToString()*`használatával hozhatók létre.

Ha paramétereket szeretne átadni a runbook-feladatoknak, használja a kérelem törzsét. A JSON-formátumban megadott alábbi információkat veszi figyelembe:

* Runbook neve: kötelező. Az elindítani kívánt runbook neve.  
* Runbook paraméterek: nem kötelező. A paraméterek listájának (név, érték) formátuma, ahol a név karakterlánc típusú, és az érték bármely érvényes JSON-érték lehet.

Ha a korábban a *VMName* és a *resourceGroupName* paraméterrel létrehozott **Get-AzureVMTextual** runbook szeretné elindítani, használja a következő JSON-formátumot a kérelem törzséhez.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

A rendszer a 201 HTTP-állapotkódot adja vissza, ha a feladatot sikeresen létrehozták. További információ a válasz fejlécekről és a válasz törzséről: [runbook-feladatok létrehozása a REST API használatával](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Runbook tesztelése és paraméterek kiosztása

Ha a tesztelési lehetőséggel [teszteli a runbook Piszkozat verzióját](automation-testing-runbook.md) , megnyílik a **test (teszt** ) oldal. Ezen a lapon konfigurálhatók a létrehozott paraméterek értékei.

![Paraméterek tesztelése és kiosztása](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Ütemterv összekapcsolása runbook és paraméterek kiosztása

Az [ütemtervet összekapcsolhatja](automation-schedules.md) a runbook, hogy a runbook egy adott időpontban induljon el. A bemeneti paramétereket az ütemterv létrehozásakor kell hozzárendelni, és a runbook ezeket az értékeket használja, amikor az ütemterv elindítja. Az ütemezés addig nem menthető, amíg meg nem történik az összes kötelező paraméterérték megadása.

![Paraméterek beosztása és kiosztása](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Webhook létrehozása runbook és paraméterek hozzárendeléséhez

Létrehozhat egy [webhookot](automation-webhooks.md) a runbook, és konfigurálhatja a runbook bemeneti paramétereit. A webhook nem menthető, amíg meg nem történik az összes kötelező paraméterérték megadása.

![Webhook létrehozása és paraméterek kiosztása](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Amikor webhook használatával hajt végre runbook, az előre definiált bemeneti paraméter *[WebhookData](automation-webhooks.md)* , valamint a megadott bemeneti paraméterekkel együtt. 

![WebhookData paraméter](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>JSON-objektum átadása runbook

Hasznos lehet olyan adattárolást tárolni, amelyet egy JSON-fájlban lévő runbook szeretne átadni. Létrehozhat például egy JSON-fájlt, amely tartalmazza az összes olyan paramétert, amelyet át szeretne adni egy runbook. Ehhez át kell alakítania a JSON-kódot egy karakterlánccá, majd át kell alakítania a karakterláncot egy PowerShell-objektumba a runbook való továbbítás előtt.

Ez a szakasz egy olyan példát használ, amelyben egy PowerShell [-parancsfájl elindítja a Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) -t, hogy elindítson egy PowerShell-runbook, és átadja a JSON-fájl tartalmát a runbook. A PowerShell-runbook egy Azure-beli virtuális gépet indít el a virtuális gép paramétereinek a JSON-objektumból való beolvasásával.

### <a name="create-the-json-file"></a>A JSON-fájl létrehozása

Írja be a következő kódot egy szövegfájlba, és mentse `test.json` valahova a helyi számítógépen.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>A runbook létrehozása

Hozzon létre egy **test-JSON** nevű új PowerShell-runbook Azure Automationban. Tekintse meg [az első PowerShell-runbook](automation-first-runbook-textual-powershell.md).

A JSON-adatok elfogadásához a runbook bemeneti paraméterként kell felvennie egy objektumot. A runbook ezután a JSON-fájlban definiált tulajdonságokat használhatják.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Mentse és tegye közzé ezt a runbook az Automation-fiókjában.

### <a name="call-the-runbook-from-powershell"></a>A runbook meghívása a PowerShellből

Most meghívhatja a runbook a helyi gépről Azure PowerShell használatával. 

1. Jelentkezzen be az Azure-ba az ábrán látható módon. Ezt követően a rendszer felszólítja az Azure-beli hitelesítő adatok megadására.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >PowerShell-runbookok esetében a **Add-AzAccount** és a **Add-AzureRMAccount** aliasok a **csatlakozási-AzAccount**. Vegye figyelembe, hogy ezek az aliasok nem érhetők el grafikus runbookok. A grafikus runbook saját maga is használhatják A **AzAccount** .

1. Szerezze be a mentett JSON-fájl tartalmát, és alakítsa át karakterlánccá. `JsonPath` az az elérési út, ahová a JSON-fájlt mentette.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. `$json` karakterlánc tartalmának konvertálása PowerShell-objektumra.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Hozzon létre egy szórótábla a **Start-AzAutomationRunbook**paraméterekhez. 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Figyelje meg, hogy a *Paraméterek* értékét a JSON-fájlból származó értékeket tartalmazó PowerShell-objektumra állítja be.
1. Indítsa el a runbook.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Következő lépések

* A runbook elindításának különböző módjaival kapcsolatos részletekért lásd: [Runbook elindítása](automation-starting-a-runbook.md).
* A szöveges runbook szerkesztéséhez tekintse meg a [szöveges Runbookok szerkesztését](automation-edit-textual-runbook.md)ismertető témakört.
* Grafikus runbook szerkesztéséhez tekintse meg [Azure Automation grafikus szerzői](automation-graphical-authoring-intro.md)műveleteit.
