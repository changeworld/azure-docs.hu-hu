---
title: A Cloud Service életciklusával kapcsolatos események kezelése | Microsoft Docs
description: Ismerje meg, hogyan használható a Cloud Service-szerepkör életciklus-módszerei a .NET-ben
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 0a9c32affc50a6d357d4160e00486c896d762e3f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75385813"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Webes és feldolgozói szerepkörök életciklusának testreszabása a .NET-ben
Feldolgozói szerepkör létrehozásakor ki kell bővíteni a [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) osztályt, amely lehetővé teszi a felülbírálását, amely lehetővé teszi, hogy válaszoljon az életciklus eseményeire. A webes szerepkörök esetében ez az osztály nem kötelező, ezért azt kell használnia, hogy válaszoljon az életciklus eseményeire.

## <a name="extend-the-roleentrypoint-class"></a>A RoleEntryPoint osztály kiterjesztése
A [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) osztály az Azure által a webes vagy feldolgozói szerepkör **indításakor**, **futtatásakor**vagy **leállításakor** meghívott metódusokat tartalmazza. Ezeket a metódusokat felülbírálhatja a szerepkör inicializálásának, a szerepkör leállítási sorrendjének vagy a szerepkör végrehajtási szálának kezeléséhez. 

A **RoleEntryPoint**kiterjesztésekor vegye figyelembe a módszerek következő viselkedését:

* A [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) és a [OnStop](/previous-versions/azure/reference/ee772844(v=azure.100)) metódus logikai értéket ad vissza, így a metódusokból **hamis** értéket adhat vissza.
  
   Ha a kód **hamis**értéket ad vissza, a szerepkör-folyamat hirtelen leáll, és nem futtatja a leállítási sorozatot. Általánosságban érdemes elkerülni a **Hamis értéket** a **OnStart** metódusból.
* A **RoleEntryPoint** metódus túlterhelésén belüli, nem kezelt kivételeket kezeletlen kivételként kezeli a rendszer.
  
   Ha az életciklus egyik módszerén belül kivétel fordul elő, az Azure fel fogja emelni a [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) eseményt, majd a folyamat leáll. Miután a szerepköre offline állapotba került, a rendszer újraindítja az Azure-ban. Kezeletlen kivétel esetén a [leállítási](/previous-versions/azure/reference/ee758136(v=azure.100)) esemény nem jön létre, és a **OnStop** metódus nem lett meghívva.

Ha a szerepkör nem indul el, vagy az inicializálás, a foglalt és a leállítási állapotok között újrahasznosítást végez, a kód a szerepkör újraindításakor a rendszer nem kezelt kivételt is tartalmazhat az életciklus-események egyikén belül. Ebben az esetben a [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) esemény segítségével határozza meg a kivétel okát, és megfelelően kezeli azt. A szerepkör a [Run](/previous-versions/azure/reference/ee772746(v=azure.100)) metódusból is visszatérhet, ami a szerepkör újraindítását eredményezi. További információ a központi telepítési állapotokról: a [szerepkörök újrahasznosítását okozó gyakori problémák](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Ha a **Microsoft Visual studióhoz készült Azure-eszközöket** használja az alkalmazás fejlesztéséhez, a szerepkör-projekt sablonjai automatikusan kiterjesztik a **RoleEntryPoint** osztályt a *WebRole.cs* és a *WorkerRole.cs* -fájlokba.
> 
> 

## <a name="onstart-method"></a>OnStart metódus
A **OnStart** metódus hívása akkor történik meg, ha a szerepkör-példányt az Azure online állapotba hozza. A OnStart-kód végrehajtása közben a szerepkör-példány **elfoglaltként** van megjelölve, és a terheléselosztó nem irányítja át a külső forgalmat. Felülbírálhatja ezt a metódust az inicializálási műveletek elvégzéséhez, például az eseménykezelők megvalósításához és a [Azure Diagnostics](cloud-services-how-to-monitor.md)elindításához.

Ha a OnStart **igaz**értéket ad vissza, a példány inicializálása sikeresen megtörtént, és az Azure meghívja a **RoleEntryPoint. Run** metódust. Ha a OnStart **hamis**értéket ad vissza, a szerepkör azonnal leáll, és nem hajt végre semmilyen tervezett leállítási sorozatot.

A következő mintakód bemutatja, hogyan bírálhatja felül a **OnStart** metódust. Ezzel a módszerrel konfigurálhatja és elindíthatja a diagnosztikai figyelőt, amikor a szerepkör-példány elindul, és beállítja a naplózási adatok átvitelét egy Storage-fiókba:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>OnStop metódus
Az **OnStop** metódus hívása azt követően történik meg, hogy a szerepkör-példány offline állapotba került az Azure-ban, és még a folyamat bezárása előtt. Felülbírálhatja ezt a metódust a szerepkör-példányhoz szükséges kód meghívásához a tiszta leállítás érdekében.

> [!IMPORTANT]
> A **OnStop** metódusban futó kód csak akkor fejeződik be, ha a felhasználó által kezdeményezett leállítástól eltérő okok miatt meghívásra kerül. Ennyi idő elteltével a rendszer leállítja a folyamatot, ezért győződjön meg arról, hogy a **OnStop** metódusban található kód gyorsan vagy nem a befejezésig fut. A **OnStop** metódus hívása a **leállítási** esemény után történik.
> 
> 

## <a name="run-method"></a>Futtatási metódus
A **Run** metódus felülbírálható a szerepkör-példányhoz tartozó hosszú ideig futó szál megvalósításához.

A **futtatási** metódus felülbírálása nem kötelező; az alapértelmezett implementáció egy olyan szálat indít el, amely örökre alvó állapotba kerül. Ha felülbírálja a **futtatási** módszert, a kódnak határozatlan ideig kell blokkolnia. Ha a **Run** metódus visszatér, a rendszer automatikusan újrahasznosítja a szerepkört. más szóval az Azure kiemeli a **leállítási** eseményt, és meghívja a **OnStop** metódust, hogy a leállítási folyamatokat a rendszer offline állapotba helyezése előtt végrehajtsa.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>A ASP.NET életciklus-módszereinek megvalósítása webes szerepkörhöz
A **RoleEntryPoint** osztály által biztosított ASP.net-életciklusi módszerek mellett a webes szerepkörök inicializálási és leállítási folyamatait is kezelheti. Ez a kompatibilitási célokra hasznos lehet, ha meglévő ASP.NET-alkalmazást PORTOL az Azure-ba. A ASP.NET életciklusának metódusait a rendszer a **RoleEntryPoint** metódusok alapján hívja meg. A **RoleEntryPoint. OnStart** metódus befejeződése után az **alkalmazás\_indítási** metódusát kell meghívni. Az **alkalmazás\_End** metódust a **RoleEntryPoint. OnStop** metódus meghívása előtt hívja meg a rendszer.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [hozhat létre Cloud Service-csomagot](cloud-services-model-and-package.md).




