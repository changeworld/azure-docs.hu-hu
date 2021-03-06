---
title: Webalkalmazás-elemzés beállítása az ASP.NET-hez az Azure Application Insights segítségével | Microsoft Docs
description: Konfigurálhatja a helyszíni vagy az Azure-ban üzemeltetett ASP.NET-webhely teljesítmény-, rendelkezésre állási és felhasználói viselkedési elemzési eszközeit.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 0843d6c04bf6fc9bab07207072990fb3fb8f1844
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665918"
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Az Application Insights beállítása az ASP.NET-webhelyhez

Ez az eljárás beállítja, hogy az ASP.NET webapp telemetriát küldjön az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) szolgáltatásnak. Az eljárás a saját helyszíni IIS-kiszolgálón vagy a felhőben futtatott ASP.NET-alkalmazásokon használható. Diagramokat és hatékony lekérdezési nyelvet biztosít, amelyek révén jobban megismerheti az alkalmazás működését, és hogy a felhasználók minként használják, valamint automatikus riasztásokat kaphat a meghibásodásokkal és a teljesítményproblémákkal kapcsolatban. Számos fejlesztő már így is rendkívül hasznosnak tartja ezeket a funkciókat, azonban a telemetria szükség esetén bővíthető és testre is szabható.

A telepítés mindössze néhány kattintással végrehajtható a Visual Studióban. A szolgáltatás díjmentesen is használható, ha a telemetria mennyiségét korlátozza. Ez a funkció lehetővé teszi a kísérletezést és hibakeresést, illetve a nem sok felhasználóval rendelkező helyek figyelését. Ha úgy dönt, hogy belevág, és az éles webhelyét is figyelni fogja, a későbbiekben könnyen emelhető a korlát.

## <a name="prerequisites"></a>Előfeltételek
Ha hozzá kívánja adni az Application Insights megoldást ASP.NET-webhelyéhez, tegye a következőket:

- Telepítse a [Windowshoz készült Visual Studio 2019](https://www.visualstudio.com/downloads/) alkalmazást a következő számítási feladatokkal:
    - ASP.NET és webes fejlesztés (ne törölje a választható összetevőket)
    - Azure-fejlesztés

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="ide"></a> 1. lépés: Az Application Insights SDK hozzáadása

> [!IMPORTANT]
> Az ebben a példában szereplő Képernyőképek a Visual Studio 2017 15.9.9 és újabb verzióján alapulnak. A Application Insights hozzáadásának élménye a Visual Studio és a ASP.NET-sablon típusának különböző verzióiban is változhat. Előfordulhat, hogy a régebbi verziók helyettesítő szöveggel rendelkeznek, például "configure Application Insights".

Kattintson a jobb gombbal a webalkalmazás nevére a Megoldáskezelő, majd válassza a > **hozzáadása** lehetőséget **Application Insights telemetria**

![A Solution Explorer (Megoldáskezelő) képernyőképe, a kiemelt Configure Application Insights (Application Insights konfigurálása) elemmel](./media/asp-net/add-telemetry-new.png)

(Az Application Insights SDK verziójától függően a rendszer felkérheti, hogy frissítsen az SDK legújabb kiadására. Ebben az esetben válassza az **Update SDK** (SDK frissítése) lehetőséget.)

![Képernyőkép: Elérhető a Microsoft Application Insights SDK egy új verziója. Az SDK frissítési lehetőség kiemelve](./media/asp-net/0002-update-sdk.png)

Az Application Insights konfigurációs képernyőjén:

Válassza az első **lépések**lehetőséget.

![Képernyőkép az alkalmazásregisztrációs szakaszról az Application Insights oldalon](./media/asp-net/00004-start-free.png)

Ha szeretné beállítani az erőforráscsoportot vagy az adatok tárolásának helyét, kattintson a **Configure settings** (Beállítások konfigurálása) lehetőségre. Az erőforráscsoportok az adatokhoz való hozzáférés szabályozásához használhatóak. Ha például több alkalmazása is van, amelyek egy adott rendszer részét képezik, azok Application Insights-adatait ugyanabba az erőforráscsoportba helyezheti.

 Kattintson a **Register** (Regisztrálás) elemre.

![Képernyőkép az alkalmazásregisztrációs szakaszról az Application Insights oldalon](./media/asp-net/00005-register-ed.png)

 Válassza a **projekt** > **NuGet-csomagok kezelése** > **csomag forrása: nuget.org** > ellenőrizze, hogy rendelkezik-e az Application Insights SDK legújabb stabil kiadásával.

 A telemetria az [Azure Portalra](https://portal.azure.com) lesz küldve a hibakeresés során és az alkalmazás közzététele után is.
> [!NOTE]
> Ha a hibakeresés során nem szeretne telemetriát küldeni a portálra, adja hozzá az Application Insights SDK-t az alkalmazáshoz, de ne konfiguráljon erőforrást a portálon. A telemetria a hibakeresés során a Visual Studióban lesz megtekinthető. Később visszatérhet erre a konfigurációs oldalra, vagy megvárhatja az alkalmazás üzembe helyezését, és [bekapcsolhatja a telemetriát a futtatás során](../../azure-monitor/app/monitor-performance-live-website-now.md).

## <a name="run"></a> 2. lépés: Az alkalmazás futtatása
Futtassa az alkalmazást az F5 billentyűvel. Nyisson meg több lapot, hogy létrejöjjön valamennyi telemetria.

A Visual Studióban láthatja a naplózott események számát.

![A Visual Studio képernyőképe. Megjelenik az Application Insights gomb a hibakeresés alatt.](./media/asp-net/00006-Events.png)

## <a name="step-3-see-your-telemetry"></a>3\. lépés: A telemetria megtekintése
A telemetriát a Visual Studióban vagy az Application Insights webportálon tekintheti meg. A telemetria keresése a Visual Studióban segíti az alkalmazás hibakeresését. A teljesítményt és a használatot a webes portálon figyelheti, amikor a rendszer élesben működik. 

### <a name="see-your-telemetry-in-visual-studio"></a>Telemetria megtekintése a Visual Studióban

A Visual Studióban az Application Insights-adatok megtekintéséhez tegye a következőket.  Válassza a **Solution Explorer** > **Connected Services** (Csatlakoztatott szolgáltatások) lehetőséget > kattintson a jobb gombbal az **Application Insights** elemre, majd kattintson a **Search Live Telemetry** (Élő telemetria keresése) gombra.

A Visual Studio Application Insights keresőablakában az alkalmazásából származó adatok között látni fogja az alkalmazás kiszolgálói oldalán létrejött telemetriai adatokat. Kísérletezzen a szűrőkkel, és kattintson valamely eseményre további részletek megtekintéséhez.

![A Data from debug session (Hibakeresési munkamenetből származó adatok) nézet képernyőképe az Application Insights ablakban.](./media/asp-net/55.png)

> [!Tip]
> Ha nem jelennek meg adatok, ellenőrizze, hogy megfelelő-e az időtartomány, majd kattintson a Search (Keresés) ikonra.

[További tudnivalók az Application Insights-eszközökről a Visual Studióban](../../azure-monitor/app/visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Telemetria megtekintése a webportálon

A telemetriát az Application Insights webportálon is megtekintheti (ha a telepítéskor nem csak az SDK-t telepítette). A portálon a Visual Studiónál több diagram, elemzőeszköz és összetevőket megjelenítő nézet található. A portál riasztásokat is biztosít.

Nyissa meg az Application Insights-erőforrást. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) és keresse meg ott, vagy válassza a **Solution Explorer** > **Connected Services** (Csatlakoztatott szolgáltatások) lehetőséget > kattintson a jobb gombbal az **Application Insights** > **Open Application Insights Portal** (Application Insights-portál megnyitása) elemre, amely odavezeti.

A portál az alkalmazásából származó telemetriai adatok nézetével nyílik meg.

![Az Application Insights áttekintési oldalának képernyőképe](./media/asp-net/007.png)

A portálon az egyik csempére vagy diagramra kattintva további részleteket tekinthet meg.

## <a name="step-4-publish-your-app"></a>4\. lépés: Az alkalmazás közzététele
Tegye közzé alkalmazását az IIS-kiszolgálón vagy az Azure-on. Az [Élő mérőszámok streammel](../../azure-monitor/app/metrics-explorer.md#live-metrics-stream) ellenőrizheti, hogy minden rendben működik-e.

A telemetria a Application Insights portálon épül fel, ahol nyomon követheti a metrikákat, és megkeresheti a telemetria. A hatékony [Kusto lekérdezési nyelvet](/azure/kusto/query/) használhatja a használat és a teljesítmény elemzéséhez, vagy adott események megtalálásához is.

Folytathatja a telemetria elemzését a [Visual Studióban](../../azure-monitor/app/visual-studio.md) olyan eszközökkel, mint például a diagnosztikai keresés és a [trendek](../../azure-monitor/app/visual-studio-trends.md).

> [!NOTE]
> Ha az alkalmazása elég telemetriát küld a [szabályozási korlát](../../azure-monitor/app/pricing.md#limits-summary) eléréséhez, az automatikus [mintavételezés](../../azure-monitor/app/sampling.md) bekapcsol. A mintavételezés csökkenti az alkalmazásból küldött telemetria mennyiségét, míg a korrelatív adatokat diagnosztikai célból megőrzi.
>
>

## <a name="land"></a>Készen is van.

Gratulálunk! Telepítette az Application Insights csomagot az alkalmazásba, és konfigurálta, hogy telemetriát küldjön az Application Insights szolgáltatásba az Azure-ban.

Az alkalmazás telemetriáját fogadó Azure-erőforrást egy *kialakítási kulcs* azonosítja. A kulcs az ApplicationInsights.config fájlban található.


## <a name="upgrade-to-future-sdk-versions"></a>Frissítés a jövőbeli SDK-verziókra
Ha frissíteni szeretne egy [új SDK-kiadásra](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases), nyissa meg a **NuGet-csomagkezelőt**, és szűréssel keresse meg a telepített csomagokat. Jelölje ki a **Microsoft.ApplicationInsights.Web** lehetőséget, és válassza a **Frissítés** elemet.

Ha az ApplicationInsights.config fájlt testreszabta, mentse el egy példányát a frissítés előtt. Ezután egyesítse a módosításait az új verzióval.

## <a name="video"></a>Videó

* Külső lépésenkénti videó arról, hogyan [konfigurálható a Application Insights egy .net-alkalmazásból a semmiből](https://www.youtube.com/watch?v=blnGAVgMAfA).

## <a name="next-steps"></a>Következő lépések

További témaköröket is elolvashat, ha a következők érdeklik:

* [Webalkalmazás beállítása futási időben](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)

### <a name="more-telemetry"></a>További telemetria

* **[Böngésző és oldalbetöltési adatok](../../azure-monitor/app/javascript.md)** – Kódrészlet beszúrása a weboldalakra.
* **[Részletesebb függőség- és kivételfigyelés](../../azure-monitor/app/monitor-performance-live-website-now.md)** – Állapotfigyelő telepítése a kiszolgálón.
* **[Kódoljon egyéni eseményeket](../../azure-monitor/app/api-custom-events-metrics.md)** a felhasználói műveletek számlálásához, időzítéséhez és méréséhez.
* **[Naplóadatok lekérése](../../azure-monitor/app/asp-net-trace-logs.md)** – Naplóadatok összevetése a telemetriával.

### <a name="analysis"></a>Elemzés

* **[Az Application Insights használata a Visual Studióban](../../azure-monitor/app/visual-studio.md)**<br/>A telemetriával végzett hibakereséssel, diagnosztikai kereséssel és a kódig való részletezés lefúrással kapcsolatos információkat tartalmaz.
* **[Elemzés](../../azure-monitor/log-query/get-started-portal.md)** – Erőteljes lekérdezési nyelv.

### <a name="alerts"></a>Riasztások

* [Rendelkezésre állási tesztek](../../azure-monitor/app/monitor-web-app-availability.md): Hozzon létre teszteket, hogy megbizonyosodjon róla, oldala látható a weben.
* [Intelligens diagnosztika](../../azure-monitor/app/proactive-diagnostics.md): Ezek a tesztek automatikusan futnak, a beállításukhoz semmit sem kell tennie. Értesítést kap, ha az alkalmazásában szokatlanul magas a meghiúsult kérelmek száma.
* [Metrikai riasztások](../../azure-monitor/app/alerts.md): riasztások beállítása, amely figyelmezteti, ha egy metrika átlépi a küszöbértéket. Az alkalmazás kódjába beépített egyedi metrikákhoz is állíthat be riasztásokat.

### <a name="automation"></a>Automatizálás

* [Application Insights-erőforrások létrehozásának automatizálása](../../azure-monitor/app/powershell.md)
