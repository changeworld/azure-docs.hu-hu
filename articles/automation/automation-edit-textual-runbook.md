---
title: Szöveges runbookok szerkesztése Azure Automationban
description: Ez a cikk különböző eljárásokat tartalmaz a PowerShell és a PowerShell munkafolyamat-runbookok használatáról a Azure Automation a szöveges szerkesztő használatával.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 72aefb8de57e27718b14dba6a6d82deb8b63466f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367278"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Szöveges runbookok szerkesztése Azure Automationban

A Azure Automationban található szöveges szerkesztő használható a PowerShell- [runbookok](automation-runbook-types.md#powershell-runbooks) és a [PowerShell-munkafolyamat runbookok](automation-runbook-types.md#powershell-workflow-runbooks)szerkesztésére. Ez a szerkesztő a más kód-szerkesztők, például az IntelliSense jellemző funkcióit tartalmazza. Emellett további speciális funkciókkal is rendelkezik, amelyek segítséget nyújtanak a runbookok közös erőforrásainak elérésében. 

A szöveges szerkesztő tartalmaz egy funkciót, amely a parancsmagok, az eszközök és a gyermek runbookok kódját szúrja be egy runbook. A kód beírása helyett választhat az elérhető erőforrások listájából, és a szerkesztő beszúrja a megfelelő kódot a runbook.

Azure Automation minden runbook két verziója, piszkozata és közzététele van. Szerkessze a runbook Piszkozat verzióját, és tegye közzé azt, hogy végrehajtható legyen. A közzétett verzió nem szerkeszthető. További információ: [Runbook közzététele](manage-runbooks.md#publish-a-runbook).

Ez a cikk részletesen ismerteti a különböző függvények a szerkesztővel való elvégzésének lépéseit. Ezek a [grafikus runbookok](automation-runbook-types.md#graphical-runbooks)nem alkalmazhatók. Ezekkel a runbookok kapcsolatban lásd: [grafikus létrehozás Azure Automationban](automation-graphical-authoring-intro.md).

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="editing-a-runbook-with-the-azure-portal"></a>Runbook szerkesztése a Azure Portal

A következő eljárással megnyithatja a runbook szerkesztésre a szöveges szerkesztőben.

1. A Azure Portal válassza ki az Automation-fiókját.
2. A **folyamat automatizálása**területen válassza a **runbookok** lehetőséget a runbookok listájának megnyitásához.
3. Válassza ki a szerkeszteni kívánt runbook, majd kattintson a **Szerkesztés**gombra.
4. Szerkessze a runbook.
5. A Szerkesztés befejezéséhez kattintson a **Mentés** gombra.
6. Kattintson a **Közzététel** elemre, ha közzé szeretné tenni a runbook legújabb vázlatos verzióját.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Parancsmag beszúrása egy runbook

1. A szöveges szerkesztő vászonján vigye a kurzort arra a helyre, ahová a parancsmagot helyezni kívánja.
2. Bontsa ki a **parancsmagok** csomópontot a könyvtár vezérlőben.
3. Bontsa ki a használni kívánt parancsmagot tartalmazó modult.
4. Kattintson a jobb gombbal a parancsmag nevére a beszúráshoz, majd válassza a **Hozzáadás a vászonhoz**lehetőséget. Ha a parancsmag egynél több paraméterrel van beállítva, a szerkesztő hozzáadja az alapértelmezett készletet. A parancsmag kibontásával másik paramétert is kijelölhet.
5. Vegye figyelembe, hogy a parancsmag kódja a teljes paraméterekkel együtt van beszúrva.
6. Adja meg a megfelelő értéket a szögletes zárójelek (< >) körüli érték helyett a szükséges paraméterekhez. Távolítsa el a szükségtelen paramétereket.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Kód beszúrása egy runbook egy gyermek runbook

1. A szöveges szerkesztő vászonján vigye a kurzort arra a helyre, ahová a [gyermek runbook](automation-child-runbooks.md)kódot kívánja helyezni.
2. Bontsa ki a **runbookok** csomópontot a könyvtár vezérlőben.
3. Kattintson a jobb gombbal a beszúrandó runbook, és válassza a **Hozzáadás a vászonhoz**lehetőséget.
4. A gyermek runbook kódja minden runbook-paraméterhez be van beszúrva.
5. Cserélje le a helyőrzőket az egyes paraméterek megfelelő értékeivel.

### <a name="insert-an-asset-into-a-runbook"></a>Eszköz beszúrása egy runbook

1. A szöveges szerkesztő vászon vezérlőjében vigye a kurzort arra a helyre, ahová a gyermek runbook kódot kívánja helyezni.
2. Bontsa ki az **eszközök** csomópontot a könyvtár vezérlőben.
3. Bontsa ki a kívánt eszköz típusának csomópontját.
4. Kattintson a jobb gombbal a beszúrandó eszköz nevére, majd válassza a **Hozzáadás a vászonhoz**lehetőséget. [Változó eszközök](automation-variables.md)esetén válassza a **"változó beolvasása" lehetőséget a vászonhoz** , vagy **adja hozzá a "változó beállítása" beállítást a vászonhoz**, attól függően, hogy szeretné-e lekérni vagy beállítani a változót.
5. Vegye figyelembe, hogy az eszköz kódját a rendszer beszúrja a runbook.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Azure Automation runbook szerkesztése a Windows PowerShell használatával

Ha a Windows PowerShell segítségével szeretne szerkeszteni egy runbook, használja a választott szerkesztőt, és mentse a runbook egy **. ps1** fájlba. A runbook tartalmának lekéréséhez az [export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) parancsmagot használhatja. Az [import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) parancsmag használatával lecserélheti a meglévő Piszkozat runbook a módosítottra.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Runbook tartalmának beolvasása a Windows PowerShell használatával

Az alábbi Példaparancsok szemléltetik egy runbook parancsprogramjának beolvasását, és mentse azt egy parancsfájl. Ebben a példában a rendszer lekéri a vázlatként megjelölt verziót. Lehetősége van a runbook közzétett verziójának lekérésére is, bár ez a verzió nem módosítható.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Runbook tartalmának módosítása a Windows PowerShell használatával

Az alábbi példaparancsok szemléltetik egy Runbook létező tartalmának felülírását egy parancsprogram tartalmával. Ez ugyanaz a mintavételi eljárás, mint [egy Runbook Windows PowerShell-lel való importálása parancsfájlból](manage-runbooks.md#import-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Kapcsolódó cikkek

* [Runbookok kezelése Azure Automation](manage-runbooks.md)
* [A PowerShell-munkafolyamat megismerése](automation-powershell-workflow.md)
* [Grafikus szerzői műveletek Azure Automation](automation-graphical-authoring-intro.md)
* [Tanúsítványok](automation-certificates.md)
* [Kapcsolatok](automation-connections.md)
* [Hitelesítő adatok](automation-credentials.md)
* [Ütemezések](automation-schedules.md)
* [Változók](automation-variables.md)

