---
title: A klasszikus üzembe helyezési modell API-jai kiAzure Monitor a metrikák és az autoskálázás számára
description: A metrikák és a klasszikus API-k, más néven Azure Service Management (ASM) vagy RDFE üzembe helyezési modell kivonása
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 980f4e840f7778c6ea3025db9b37a3454267bac7
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663555"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>A klasszikus üzembe helyezési modell API-jai kiAzure Monitor a metrikák és az autoskálázás számára

Azure Monitor (korábbi Azure-elemzések, amikor az első kiadás) jelenleg a klasszikus virtuális gépekről és a klasszikus Cloud Servicesokból származó mérőszámok létrehozásához és kezeléséhez biztosít lehetőséget. A klasszikus üzembe helyezési modellen alapuló API-k eredeti készlete kimarad az összes Azure-beli nyilvános és privát felhőben **2019,** az összes régióban.   

Ugyanazokat a műveleteket a Azure Resource Manager-alapú API-k több mint egy évig támogatták. A Azure Portal az új REST API-kat használja az autoscale és a metrikák esetében is. A Resource Manager API-k alapján egy új SDK, PowerShell és CLI is elérhető. Partnereink számára az új Resource Manager-alapú REST API-k használata a Azure Monitorban.  

## <a name="who-is-not-affected"></a>Ki nem érintett

Ha a Azure Portalon, az [új Azure monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)-ban, a PowerShellben, a CLI-ben vagy a Resource Manager-sablonokban kezeli az autoskálázást, nincs szükség beavatkozásra.  

Ha mérőszámokat használ a Azure Portal vagy különböző [figyelési partneri szolgáltatásokon](../../azure-monitor/platform/partners.md)keresztül, nincs szükség beavatkozásra. A Microsoft együttműködik a figyelési partnerekkel az új API-kra való Migrálás során.

## <a name="who-is-affected"></a>Ki érintett

Ez a cikk Önre vonatkozik, ha a következő összetevőket használja:

- **Klasszikus Azure** -beli betekintő SDK – ha a [klasszikus Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)betekintő SDK-t használja, váltson az új Azure monitor SDK for [.net](https://github.com/azure/azure-libraries-for-net#download) vagy a [Java](https://github.com/azure/azure-libraries-for-java#download)használatára. Töltse le a [Azure monitor SDK NuGet csomagot](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Klasszikus automatikus méretezés** – ha a [klasszikus automatikus méretezési beállítások API-kat](https://msdn.microsoft.com/library/azure/mt348562.aspx) az egyéni eszközökkel vagy a [klasszikus Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)-beli elemzések SDK-val hívja meg, váltson a [Resource Manager Azure monitor REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings)használatára.

- **Klasszikus mérőszámok** – ha a [klasszikus REST API](https://msdn.microsoft.com/library/azure/dn510374.aspx) -kkal vagy a [klasszikus Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) -elemzéssel rendelkező, egyéni eszközökről származó mérőszámokat használ, váltson a [Resource Manager Azure monitor REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings)használatára. 

Ha nem biztos abban, hogy a kód vagy az egyéni eszközök meghívja a klasszikus API-kat, tekintse meg a következőket:

- Tekintse át a kódban vagy eszközben hivatkozott URI-t. A klasszikus API-k az URI https://management.core.windows.nethasználják. A Resource Manager-alapú API-k újabb URI-JÁT a https://management.azure.com/használatával kezdődik.

- Hasonlítsa össze a szerelvény nevét a gépen. A régebbi klasszikus szerelvény a következő címen érhető el: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/.

- Ha tanúsítványalapú hitelesítést használ a metrikák vagy az autoscale API-k eléréséhez, klasszikus végpontot és könyvtárat használ. Az újabb Resource Manager API-k Azure Active Directory hitelesítést igényelnek egy egyszerű szolgáltatásnév vagy egy egyszerű felhasználó használatával.

- Ha az alábbi hivatkozások bármelyikén a dokumentációban hivatkozott hívásokat használja, a régebbi klasszikus API-kat használja.

  - [Windows. Azure. Management. monitoring osztály könyvtára](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [Monitorozás (klasszikus) .NET](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations felület](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Miért érdemes váltania

Az autoscale és a metrikák összes meglévő funkciója továbbra is az új API-kon keresztül fog működni.  

Az újabb API-khoz való áttelepítéshez Resource Manager-alapú képességek tartoznak, mint például a konzisztens szerepköralapú Access Control (RBAC) támogatása az összes figyelési szolgáltatásban. A metrikák további funkcióit is megszerezheti: 

- méretek támogatása
- konzisztens 1 perces metrikai részletesség az összes szolgáltatáson belül 
- jobb lekérdezés
- nagyobb adatmegőrzés (93 napos metrikák és 30 nap) 

Az Azure-ban az összes többi szolgáltatáshoz hasonlóan a Resource Manager-alapú Azure Monitor API-k jobb teljesítményt, méretezhetőséget és megbízhatóságot biztosítanak. 

## <a name="what-happens-if-you-do-not-migrate"></a>Mi történik, ha nem telepíti át

### <a name="before-retirement"></a>Nyugdíjazás előtt

Nem lesz közvetlen hatással az Azure-szolgáltatásokra vagy azok munkaterhelésére.  

### <a name="after-retirement"></a>Nyugdíjazás után

A korábban felsorolt klasszikus API-kon érkező hívások sikertelenek lesznek, és az alábbihoz hasonló hibaüzeneteket adnak vissza:

Az autoscale esetében: *Ez az API elavult. A Azure Portal, a Azure Monitor SDK, a PowerShell, a CLI vagy a Resource Manager-sablonok segítségével kezelheti az autoskálázási beállításokat*.  

Metrikák esetén: *Ez az API elavult. A metrikák lekérdezéséhez használja a Azure Portalt, Azure Monitor SDK-t, a PowerShellt*és a CLI-t.

## <a name="email-notifications"></a>E-mail-értesítések

A rendszer kivonulási értesítést küldött az e-mail-címekre a következő fiókok szerepköreihez: 

- Fiók-és szolgáltatás-rendszergazdák
- Társrendszergazdák  

Ha bármilyen kérdése van, vegye fel velünk a kapcsolatot MonitorClassicAPIhelp@microsoft.comcímen.  

## <a name="references"></a>Referencia

- [Újabb REST API-k a Azure Monitorhoz](https://docs.microsoft.com/rest/api/monitor/) 
- [Újabb Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
