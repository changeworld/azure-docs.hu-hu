---
title: Runbook kimenete és üzenetei Azure Automation
description: Ismerteti, hogyan lehet kimeneti és hibaüzeneteket létrehozni és beolvasni a runbookok Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 457b2d2211ea1ba5fa36cec4b7e9a214f5bcad77
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367091"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Runbook kimenete és üzenetei Azure Automation

A legtöbb Azure Automation runbookok valamilyen típusú kimenettel rendelkezik. Ez a kimenet hibaüzenetet kaphat a felhasználónak vagy egy másik runbook használni kívánt összetett objektumnak. A Windows PowerShell [több streamet](/powershell/module/microsoft.powershell.core/about/about_redirection) biztosít a kimenetek parancsfájlokból vagy munkafolyamatokból való küldéséhez. A Azure Automation az egyes streamek esetében különbözőképpen működik. Ha runbook hoz létre, kövesse az ajánlott eljárásokat a streamek használatához.

Az alábbi táblázat röviden leírja, hogy az egyes streamek milyen viselkedéssel rendelkeznek Azure Portal a közzétett runbookok és a [runbook tesztelése](automation-testing-runbook.md)során. A kimeneti adatfolyam a runbookok közötti kommunikáció fő adatfolyama. A többi stream üzenet-adatfolyamként van besorolva, amely a felhasználó felé irányuló információk közlésére szolgál. 

| Stream | Leírás | Közzétett | Tesztelés |
|:--- |:--- |:--- |:--- |
| Hiba |A felhasználónak szóló hibaüzenet. A kivételtől eltérően a runbook alapértelmezés szerint egy hibaüzenet után is folytatódik. |A feladatok előzményeibe írva |Megjelenítés a test output (kimenet) panelen |
| Hibakeresés |Egy interaktív felhasználó számára készült üzenetek. Nem használható a runbookok. |Nem írt a feladatok előzményeire |Nem jelenik meg a test output (kimenet) ablaktáblán |
| Kimenet |Másik runbookok számára készült objektum. |A feladatok előzményeibe írva |Megjelenítés a test output (kimenet) panelen |
| Állapot |A rekordok automatikusan generált előtt és után a runbook egyes tevékenységeinek. A runbook nem próbálja meg saját folyamatokat létrehozni, mivel azok interaktív felhasználók számára készültek. |Csak akkor íródik a feladatok előzményeibe, ha a runbook be van kapcsolva. |Nem jelenik meg a test output (kimenet) ablaktáblán |
| Részletes |Általános vagy hibakeresési adatokat tartalmazó üzenetek. |Csak akkor íródott a feladatok előzményeire, ha a részletes naplózás be van kapcsolva a runbook |Csak akkor jelenik meg a test output (tesztelés) ablaktáblán, ha `VerbosePreference` változó a folytatás értékre van állítva a runbook |
| Figyelmeztetés |A felhasználónak szóló figyelmeztető üzenetet. |A feladatok előzményeibe írva |Megjelenítés a test output (kimenet) panelen |

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="output-stream"></a>Kimeneti adatfolyam

A kimeneti adatfolyam a parancsfájlok vagy a munkafolyamat megfelelő futtatásakor létrehozott objektumok kimenetéhez használatos. Azure Automation elsődlegesen ezt az adatfolyamot használja azon objektumok számára, amelyeket az [aktuális runbook](automation-child-runbooks.md)meghívó fölérendelt runbookok használ. Amikor egy szülő [meghívja a runbook](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution), a gyermek visszaadja az adatokat a kimeneti adatfolyamból a szülőnek. 

A runbook a kimeneti adatfolyam használatával közli az általános információkat az ügyféllel, ha azt más runbook nem hívja meg. Az ajánlott eljárás szerint azonban a [részletes streamet](#verbose-stream) általában a runbookok kell használnia az általános információk a felhasználóhoz való továbbításához.

Írja be a runbook az adatokat a kimeneti adatfolyamba [írási kimenet](https://technet.microsoft.com/library/hh849921.aspx)használatával. Azt is megteheti, hogy az objektumot a parancsfájl saját sorába helyezi.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>Függvény kimenetének feldolgozása

Amikor egy runbook függvény a kimeneti adatfolyamba ír, a kimenet vissza lesz adva a runbook. Ha a runbook a kimenetet egy változóhoz rendeli, a kimenet nem íródik a kimeneti adatfolyamba. A függvényen belüli bármely más streambe való írás a runbook tartozó megfelelő streambe ír. Vegye figyelembe a következő PowerShell-munkafolyamat-runbook.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

A runbook-feladatokhoz tartozó kimeneti adatfolyam a következő:

```output
Output inside of function
Output outside of function
```

A runbook feladatsor részletes streamje a következő:

```output
Verbose outside of function
Verbose inside of function
```

Miután közzétette a runbook, és megkezdte a megkezdését, be kell kapcsolni a részletes naplózást a runbook-beállításokban a részletes adatfolyam kimenetének lekéréséhez.

### <a name="declaring-output-data-type"></a>A kimeneti adattípus deklarálása

A következő példák a kimeneti adattípusokra mutatnak:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Kimenet adattípusának deklarálása egy munkafolyamatban

A munkafolyamat a kimenet adattípusát adja meg a [OutputType attribútum](https://technet.microsoft.com/library/hh847785.aspx)használatával. Ennek az attribútumnak nincs hatása a futtatókörnyezetben, de a runbook várt kimenetének tervezési ideje szerint jelzi. Mivel a runbookok eszközkészlete továbbra is fejlődik, a kimeneti adattípusok bejelentésének fontossága a tervezési időszakban nő. Ezért ajánlott ezt a deklarációt minden Ön által létrehozott runbookok belefoglalni.

Az alábbi példában a runbook kimenete egy karakterlánc-objektum, és határozza meg, a kimeneti típus tartalmazza. Ha a runbook kimenete egy tömb bizonyos típusú, majd meg kell továbbra is megadni, nem pedig egy tömb típusú.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Kimeneti adattípus deklarálása grafikus runbook

Ha egy grafikus vagy grafikus PowerShell-munkafolyamat runbook szeretné deklarálni a kimeneti típust, válassza a **bemeneti és kimeneti** menü lehetőséget, és adja meg a kimeneti típust. Ajánlott a teljes .NET-osztály nevét használni, hogy a típus könnyen azonosítható legyen, ha egy szülő runbook hivatkozik rá. A teljes név használata az osztály összes tulajdonságát elérhetővé teszi a runbook lévő adatbuszba, és növeli a rugalmasságot, ha a tulajdonságok a feltételes logika, a naplózás és a más runbook tevékenységek értékeiként használatosak.<br> ![Runbook bemeneti és kimeneti beállítása](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Miután megadott egy értéket a bemeneti és kimeneti Tulajdonságok ablaktáblán a **kimenet típusa** mezőben, ügyeljen rá, hogy a vezérlőn kívülre kattintson, hogy az felismerje a bejegyzést.

Az alábbi példa két grafikus runbookok mutat be a bemeneti és kimeneti funkció bemutatására. A moduláris runbook kialakítási modell alkalmazása esetén egy runbook kell lennie, mint az Azure-ban a futtató fiókkal történő hitelesítést kezelő Runbook-sablon hitelesítése. A második runbook, amely általában alapvető logikát végez egy adott forgatókönyv automatizálásához, ebben az esetben a hitelesítő Runbook sablont hajtja végre. Megjeleníti az eredményeket a test output (kimenet) ablaktáblán. Normális körülmények között ez a runbook a gyermek runbook kimenetét kihasználó erőforráson végezhető el.

Itt látható a AuthenticateTo alapszintű logikája **– Az Azure** runbook.<br> ![hitelesítő Runbook-sablon például](media/automation-runbook-output-and-messages/runbook-authentication-template.png).

A runbook tartalmazza a `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`kimeneti típust, amely a hitelesítési profil tulajdonságait adja vissza.<br> ![Runbook kimeneti típusának példája](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Habár ez a runbook egyszerű, itt egy konfigurációs elem is meghívja Önt. Az utolsó tevékenység végrehajtja a `Write-Output` parancsmagot a profilok egy változóba való írásához a `Inputobject` paraméterhez tartozó PowerShell-kifejezés használatával. Ez a paraméter szükséges a `Write-Output`hoz.

A példában a **test-ChildOutputType**nevű második runbook egyszerűen két tevékenységet határoz meg.<br> ![példa gyermek kimeneti típus Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

Az első tevékenység meghívja a **AuthenticateTo-Azure-** runbook. A második tevékenység a `Write-Verbose` parancsmagot futtatja a **tevékenység kimenete**értékre beállított **adatforrással** . Emellett a **mező elérési útja** a **Context. Subscription. SubscriptionName**, a **AuthenticateTo-Azure** runbook környezeti kimenete.<br> ![Write-verbose parancsmag paraméter-adatforrás](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Az eredményül kapott kimenet az előfizetés neve.<br> ![Test-ChildOutputType Runbook eredményei](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>Üzenet-adatfolyamok

A kimeneti adatfolyamtól eltérően az üzenetek a felhasználó felé továbbítják az adatokat. Több üzenet-adatfolyam is létezik különböző típusú adatokhoz, és a Azure Automation az egyes streameket különbözőképpen kezeli.

### <a name="warning-and-error-streams"></a>Figyelmeztetési és hiba-adatfolyamok

A figyelmeztetési és hiba-adatfolyamok naplózása a runbook előforduló hibákat naplózza. A Azure Automation a runbook végrehajtásakor beírja ezeket a streameket a feladatok előzményeibe. Az Automation a runbook tesztelésekor a Azure Portal teszt kimenete ablaktábláján lévő adatfolyamokat is tartalmazza. 

Alapértelmezés szerint a runbook figyelmeztetés vagy hiba után folytatja a végrehajtást. Megadhatja, hogy a runbook figyelmeztessen egy figyelmeztetésre vagy hibára, ha a runbook az üzenet létrehozása előtt beállít egy [preferencia változót](#preference-variables) . Ha például egy kivétel miatt a runbook felfüggeszti a hibát, állítsa a `ErrorActionPreference` változót leállításra.

Figyelmeztetés vagy hibaüzenet létrehozása a [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) vagy a [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) parancsmag használatával. A tevékenységek a figyelmeztetési és a hiba-adatfolyamokra is írhatnak.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>Hibakeresési adatfolyam

Azure Automation a hibakeresési üzenet streamjét használja az interaktív felhasználók számára. Nem használható a runbookok-ben.

### <a name="verbose-stream"></a>Részletes adatfolyam

A részletes üzenetküldési adatfolyam a runbook művelet általános információit támogatja. Mivel a hibakeresési adatfolyam nem érhető el egy runbook, a runbook részletes üzeneteket kell használnia a hibakeresési adatokhoz. 

Alapértelmezés szerint a feladatok előzményei nem tárolnak részletes üzeneteket a közzétett runbookok, a teljesítménnyel kapcsolatos okokból. A részletes üzenetek tárolásához használja a Azure Portal **Konfigurálás** lapot a részletes **rekordok naplózása** beállítással a közzétett runbookok konfigurálásához a részletes üzenetek naplózásához. Kapcsolja be ezt a beállítást csak hibakeresési egy runbook vagy hibák elhárítása. A legtöbb esetben érdemes megtartani a részletes rekordok naplózásának alapértelmezett beállítását.

[Runbook tesztelésekor](automation-testing-runbook.md)a részletes üzenetek akkor sem jelennek meg, ha a runbook a részletes rekordok naplózására van konfigurálva. A [runbook tesztelésekor](automation-testing-runbook.md)a részletes üzenetek megjelenítéséhez be kell állítania a `VerbosePreference` változót a folytatáshoz. Ha ez a változó be van állítva, a részletes üzenetek a Azure Portal teszt kimenet paneljén jelennek meg.

A következő kód egy részletes üzenetet hoz létre a [Write-verbose](https://technet.microsoft.com/library/hh849951.aspx) parancsmag használatával.

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>Haladási rekordok

A Azure Portal configure ( **Konfigurálás** ) lapján konfigurálhatja a runbook naplózási folyamatát. Az alapértelmezett beállítás szerint a rendszer nem naplózza a rekordokat a teljesítmény maximalizálása érdekében. A legtöbb esetben érdemes megtartani az alapértelmezett beállítást. Kapcsolja be ezt a beállítást csak hibakeresési egy runbook vagy hibák elhárítása. 

Ha engedélyezi a folyamat naplózását, a runbook az egyes tevékenységek futtatása előtt és után rekordokat ír a feladatok előzményeihez. A runbook tesztelése nem jeleníti meg az előrehaladási üzeneteket még akkor sem, ha a runbook naplózása folyamatban van.

>[!NOTE]
>A [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) parancsmag nem érvényes egy runbook, mert ez a parancsmag interaktív felhasználóval való használatra készült.

## <a name="preference-variables"></a>Preferenciaváltozók

A runbookok bizonyos Windows PowerShell-beállításokat állíthat [be a különböző](https://technet.microsoft.com/library/hh847796.aspx) kimeneti adatfolyamoknak küldött adatválaszok szabályozására. A következő táblázat felsorolja a runbookok használható preferencia-változókat az alapértelmezett és érvényes értékekkel. Ha a Windows PowerShellben a Azure Automationon kívülre használja, további értékek érhetők el.

| Változó | Alapértelmezett érték | Érvényes értékek |
|:--- |:--- |:--- |
| `WarningPreference` |Folytatás |Leállítás<br>Folytatás<br>Folytatáscsendben |
| `ErrorActionPreference` |Folytatás |Leállítás<br>Folytatás<br>Folytatáscsendben |
| `VerbosePreference` |Folytatáscsendben |Leállítás<br>Folytatás<br>Folytatáscsendben |

A következő táblázat felsorolja a runbookok-ben érvényes preferencia-változók értékeinek viselkedését.

| Érték | Viselkedés |
|:--- |:--- |
| Folytatás |Naplózza az üzenetet, és folytatja a runbook futtatását. |
| Folytatáscsendben |Továbbra is fennáll, az üzenet naplózása nélkül a runbook futtatását. Ennek az értéknek az a következménye, hogy figyelmen kívül hagyja az üzenetet. |
| Leállítás |Naplózza az üzenetet, és felfüggeszti a runbook futtatását. |

## <a name="runbook-output"></a>Runbook-kimenet és üzenetek beolvasása

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Runbook-kimenet és-üzenetek lekérése Azure Portal

Egy runbook-feladat részleteit a Azure Portal **feladatok** lapján tekintheti meg a runbook. A feladatok összegzése a bemeneti paramétereket és a [kimeneti adatfolyamot](#output-stream)jeleníti meg, valamint a feladattal kapcsolatos általános információkat és a bekövetkezett kivételeket. A feladatok előzményei a kimeneti adatfolyamból, valamint a [Figyelmeztetési és a hiba-adatfolyamokból](#warning-and-error-streams)származó üzeneteket tartalmaznak. Emellett a [részletes adatfolyam](#verbose-stream) -és [folyamatjelző rekordokból](#progress-records) származó üzeneteket is tartalmaz, ha a runbook a részletes és a folyamatjelző rekordok naplózására van konfigurálva.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Runbook-kimenet és-üzenetek lekérése a Windows PowerShellben

A Windows PowerShellben a runbook kimenetét és üzeneteit a [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) parancsmag használatával kérheti le. Ehhez a parancsmaghoz szükség van a feladatokhoz tartozó AZONOSÍTÓra, és egy `Stream` nevű paraméterrel kell megadnia a lekérdezni kívánt adatfolyamot. A paraméter értékének megadásával lekérheti a feladatokhoz tartozó összes adatfolyamot.

A következő példában elindul a runbook, és megvárja, amíg az befejeződik. Miután a runbook befejezte a végrehajtást, a parancsfájl gyűjti a runbook kimeneti adatfolyamot a feladatokból.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Runbook-kimenet és-üzenetek lekérése grafikus runbookok

Grafikus runbookok esetében a kimenet és az üzenetek további naplózása tevékenység szintű nyomkövetés formájában érhető el. A nyomkövetésnek két szintje van: alapszintű és részletes. Az alapszintű nyomkövetés megjeleníti a runbook minden tevékenységének kezdési és befejezési időpontját, valamint a tevékenység újrapróbálkozásával kapcsolatos információkat. Ilyenek például a kísérletek száma és a tevékenység kezdési időpontja. A részletes nyomkövetés magában foglalja az alapszintű nyomkövetési funkciókat, valamint a bemeneti és kimeneti adatok naplózását az egyes tevékenységekhez. 

A tevékenység szintű nyomkövetés jelenleg a részletes adatfolyam használatával ír rekordokat. Ezért a nyomkövetés engedélyezésekor engedélyeznie kell a részletes naplózást. A nyomkövetést engedélyező grafikus runbookok esetében nem kell naplózni a folyamatjelző rekordokat. Az alapszintű nyomkövetés ugyanazt a célt szolgálja, és több információt nyújt.

![Grafikus szerzői feladatok adatfolyam-nézete](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

A grafikus runbookok részletes naplózást és nyomkövetést engedélyező rendszerképből sokkal több információ érhető el az üzemi **feladatok adatfolyamok** nézetében. Ez a további információ elengedhetetlen lehet a runbook kapcsolatos éles problémák elhárításához. 

Ha azonban nem kívánja, hogy ezek az információk nyomon kövessék a runbook állapotát a hibaelhárításhoz, érdemes lehet a nyomkövetést általános gyakorlatként megtartani. A nyomkövetési rekordok különösen sokek lehetnek. A grafikus runbook nyomon követésével az alapszintű vagy részletes nyomkövetési konfigurációtól függően akár két-négy rekordot is megadhat tevékenységekhez.

**A tevékenység szintű nyomkövetés engedélyezése:**

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A **folyamat-automatizálás** szakaszban válassza a **runbookok** lehetőséget a runbookok listájának megnyitásához.
3. A Runbookok lapon válasszon egy grafikus runbook a runbookok listájából.
4. A **Beállítások**területen kattintson a **naplózás és nyomkövetés**elemre.
5. A naplózás és nyomkövetés lapon a **részletes**naplózás engedélyezéséhez kattintson **a bekapcsolás** elemre.
6. A **tevékenység szintű nyomkövetés**területen módosítsa a nyomkövetési szintet **alapszintű vagy** **részletesre**a szükséges nyomkövetési szint alapján.<br>

   ![Grafikus szerzői műveletek naplózása és nyomkövetése lap](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Runbook-kimenet és-üzenetek lekérése Microsoft Azure figyelő naplóiban

A Azure Automation a runbook feladatok állapotát és a feladatok streamjét a Log Analytics munkaterületre küldheti. Azure Monitor támogatja a következő naplók használatát:

* Betekintést nyerhet az Automation-feladatokba.
* A runbook-feladatok állapota alapján indítson el egy e-mailt vagy riasztást, például sikertelen vagy felfüggesztett.
* Speciális lekérdezések írása a feladatok adatfolyamai között.
* A feladatok összekapcsolása az Automation-fiókok között.
* A feladatok előzményeinek megjelenítése.

További információ a Azure Monitor naplókkal való integráció konfigurálásáról a feladatok adatainak gyűjtéséhez, összekapcsolásához és működéséhez: a feladatok [állapotának és a feladatok adatfolyamának továbbítása az automatizálásból a Azure monitor naplókba](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Következő lépések

* A runbook végrehajtásával, a runbook-feladatok figyelésével és egyéb technikai részletekkel kapcsolatos további tudnivalókért tekintse meg [a runbook-feladatok nyomon követése](automation-runbook-execution.md)című témakört.
* A gyermek-runbookok kialakításával és használatával kapcsolatos tudnivalókat lásd: [gyermek runbookok Azure Automation](automation-child-runbooks.md).
* A PowerShell-lel kapcsolatos további információk, beleértve a nyelvi referenciákat és a tanulási modulokat, lásd: [PowerShell-dokumentumok](/powershell/scripting/overview).
