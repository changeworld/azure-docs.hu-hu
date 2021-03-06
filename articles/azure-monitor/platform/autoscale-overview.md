---
title: Az Virtual Machines-, Cloud Services-és Web Apps-méretezési funkció áttekintése
description: Microsoft Azure az autoskálázás. A Virtual Machinesre, a virtuálisgép-méretezési csoportokra, a Cloud Servicesra és a Web Appsre vonatkozik.
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a60c03f1928b38c78a59edca4b5493307d7d19d3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364373"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Az Microsoft Azure Virtual Machines-, Cloud Services-és Web Apps-méretezési funkció áttekintése
Ez a cikk ismerteti, hogy mi Microsoft Azure az autoscale, annak előnyeit és a használatának első lépéseit.  

Azure Monitor az autoscale csak [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)és [API Management szolgáltatásokra](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)vonatkozik.

> [!NOTE]
> Az Azure két autoskálázási módszerrel rendelkezik. Az autoscale egy régebbi verziója Virtual Machines (rendelkezésre állási csoportokra) vonatkozik. Ez a funkció korlátozott támogatást nyújt, és javasoljuk, hogy gyorsabban és megbízhatóbban támogassa a virtuálisgép-méretezési csoportokra való áttérést. Ez a cikk a régebbi technológiák használatára mutató hivatkozást tartalmaz.  
>
>

## <a name="what-is-autoscale"></a>Mi az az autoscale?
Az automatikus méretezés lehetővé teszi, hogy az alkalmazás terhelésének kezeléséhez megfelelő mennyiségű erőforrást futtasson. Lehetővé teszi az erőforrások hozzáadását a terhelés növekedésének kezeléséhez, és pénzt takarít meg a tétlenül ülő erőforrások eltávolításával. Megadhatja a futtatandó példányok minimális és maximális számát, és a virtuális gépeket a szabályok egy halmaza alapján automatikusan hozzáadhatja vagy eltávolíthatja. A minimális értékkel gondoskodhat róla, hogy az alkalmazás mindig még a terhelés nélkül is fusson. Az összes lehetséges óradíjat maximálisan korlátozza. A két véglet közötti automatikus méretezést a létrehozott szabályok használatával végezheti el.

 ![Az autoscale magyarázata. Virtuális gépek hozzáadása és eltávolítása](./media/autoscale-overview/AutoscaleConcept.png)

Ha a szabályok teljesülnek, a rendszer egy vagy több autoskálázási műveletet indít el. Virtuális gépeket adhat hozzá és távolíthat el, illetve egyéb műveleteket is végrehajthat. A következő koncepcionális diagram ezt a folyamatot mutatja be.  

 ![Folyamatábra-méretezési diagram](./media/autoscale-overview/Autoscale_Overview_v4.png)

Az alábbi magyarázat az előző diagram darabjaira vonatkozik.   

## <a name="resource-metrics"></a>Erőforrás-metrikák
Az erőforrások metrikákat bocsátanak ki, ezeket a metrikákat később szabályok dolgozzák fel. A metrikák különböző módszerekkel származnak.
A virtuálisgép-méretezési csoportok telemetria-adatok használatával használják az Azure Diagnostics-ügynököket, míg a Web Apps és a Cloud Services telemetria közvetlenül az Azure-infrastruktúrából származnak. A leggyakrabban használt statisztikák közé tartoznak a CPU-használat, a memóriahasználat, a szálak száma, a várólista hossza és a lemezhasználat. A használható telemetria-adatok listáját itt tekintheti meg: az [autoscale Common mérőszámai](../../azure-monitor/platform/autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Egyéni metrikák
Saját egyéni metrikákat is használhat, amelyeket az alkalmazás (ok) okozhat. Ha úgy konfigurálta az alkalmazás (oka) t, hogy metrikákat küldjön a Application Insightsre, a mérőszámok kihasználása lehetővé teszi, hogy a méretezés vagy a nem.

## <a name="time"></a>Idő
Az ütemezett szabályok az UTC-alapúak. A szabályok beállításakor megfelelően be kell állítania az időzónát.  

## <a name="rules"></a>Szabályok
A diagramon csak egy autoskálázási szabály látható, de ezek közül több is lehet. A helyzethez szükség szerint összetett, átfedésben lévő szabályokat hozhat létre.  A szabályok típusai a következők:  

* **Metrika-alapú** – például akkor hajtsa végre ezt a műveletet, ha a CPU-használat 50% fölött van.
* **Időalapú** – például, ha egy adott időzónában szombaton indít egy webhookot.

A metrika-alapú szabályok mérik az alkalmazás terhelését, és a terhelés alapján felveszik vagy eltávolítják a virtuális gépeket. Az ütemezett szabályok lehetővé teszik a méretezést, ha a terhelésben időmintázatok jelennek meg, és egy lehetséges terhelési növekedés vagy csökkenés előtt szeretnék méretezni a méretezést.  

## <a name="actions-and-automation"></a>Műveletek és automatizálás
A szabályok egy vagy több típusú műveletet indíthatnak el.

* **Vertikálisan méretezhető virtuális** gépek
* **E-mail** – e-mail küldése az előfizetés-adminisztrátoroknak, a közös rendszergazdáknak és/vagy az Ön által megadott további e-mail-címnek
* **Automatizálás webhookok használatával** – webhookok hívása, amely több, az Azure-on belüli vagy kívüli összetett műveletet indíthat el. Az Azure-ban elindíthat egy Azure Automation runbook, egy Azure-függvényt vagy egy Azure logikai alkalmazást. Példa külső gyártótól származó URL-cím az Azure-on kívül olyan szolgáltatások, mint a Slack és a Twilio.

## <a name="autoscale-settings"></a>Automatikus méretezési beállítások
Az autoscale a következő terminológiát és struktúrát használja.

- Az autoskálázási **beállítással** megtudhatja, hogy a méretezés felfelé vagy lefelé történjen-e. Egy vagy több profilt tartalmaz, a cél erőforrással kapcsolatos információkat és az értesítési beállításokat.

  - Az **autoskálázási profil** a következőket ötvözi:

    - a **kapacitás beállítása**, amely megadja a példányok számának minimális, maximális és alapértelmezett értékét.
    - **szabályok összessége**, amelyek mindegyike egy triggert (időt vagy metrikát) és egy méretezési műveletet (fel vagy le) tartalmaz.
    - **Ismétlődés**, amely azt jelzi, hogy mikor kell a profilt az autoscale értékre állítani.

      Több profillal is rendelkezhet, amelyek lehetővé teszik a különböző átfedési követelmények ellátását. Különböző autoskálázási profilokat használhat a hét különböző időpontjaihoz vagy napjaihoz, például:.

  - Egy **értesítési beállítás** határozza meg, hogy milyen értesítések történnek, amikor egy autoskálázási esemény bekövetkezik, és megfelel az egyik autoskálázási beállítás profiljának feltételeinek. Az autoscale egy vagy több e-mail-címre tud értesítést küldeni, vagy hívásokat kezdeményez egy vagy több webhooknak.


![Azure-beli autoskálázási beállítás, profil és szabály szerkezete](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

A konfigurálható mezők és leírások teljes listája az [autoscale Rest APIban](https://msdn.microsoft.com/library/dn931928.aspx)érhető el.

Példák a kódokra:

* [Speciális automatikus méretezési konfiguráció a Resource Manager-sablonokkal VM Scale Sets](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md)  
* [REST API méretezése](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Vízszintes és függőleges méretezés
Az autoskálázás csak horizontálisan méretezhető, ami a virtuálisgép-példányok számának növekedése ("out") vagy a csökkenés ("in").  A horizontális megoldás rugalmasabb a Felhőbeli helyzetekben, mivel lehetővé teszi, hogy akár több ezer virtuális gépet futtasson a terhelés kezeléséhez.

Ezzel szemben a vertikális skálázás eltérő. Ugyanazokat a virtuális gépeket őrzi meg, de a virtuális gépeket ("felfelé") vagy annál kevesebbet ("Down") teszi hatékonyabbá. A teljesítményt a memória, a processzor sebessége, a lemezterület stb. méri.  A vertikális skálázás több korlátozást tartalmaz. Ez a nagyobb hardverek számától függ, ami gyorsan elér egy felső korlátot, és régiónként változhat. A vertikális skálázáshoz is általában szükség van egy virtuális gép leállítására és újraindítására.


## <a name="methods-of-access"></a>Hozzáférési módszerek
Beállíthatja az autoscale on

* [Azure Portal](../../azure-monitor/platform/autoscale-get-started.md)
* [PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)
* [Platformfüggetlen parancssori felület (CLI)](../../azure-monitor/platform/cli-samples.md#autoscale)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Az autoscale támogatott szolgáltatásai
| Szolgáltatás | Séma & docs |
| --- | --- |
| Webalkalmazások |[Méretezés Web Apps](../../azure-monitor/platform/autoscale-get-started.md) |
| Felhőszolgáltatások |[Felhőalapú szolgáltatás méretezése](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtual Machines: klasszikus |[A klasszikus virtuális gépek rendelkezésre állási csoportjainak méretezése](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Virtual Machines: Windows-méretezési csoportok |[Virtuálisgép-méretezési csoportok méretezése a Windowsban](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Virtual Machines: linuxos méretezési csoportok |[Virtuálisgép-méretezési csoportok skálázása Linux rendszeren](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Virtual Machines: Windows-példa |[Speciális automatikus méretezési konfiguráció a Resource Manager-sablonokkal VM Scale Sets](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md) |
| API Management-szolgáltatás|[Az Azure API Management-példány automatikus skálázása](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni az autoscale használatával kapcsolatban, használja az előzőleg felsorolt, vagy a következő erőforrásokra vonatkozó információkat:

* [Általános mérőszámok Azure Monitor](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Ajánlott eljárások az Azure Monitor-méretezéshez](../../azure-monitor/platform/autoscale-best-practices.md)
* [E-mailek és webhookok riasztási értesítéseinek küldése az autoscale műveletekkel](../../azure-monitor/platform/autoscale-webhook-email.md)
* [REST API méretezése](https://msdn.microsoft.com/library/dn931953.aspx)
* [Az Virtual Machine Scale Sets-méretezés hibaelhárítása](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

