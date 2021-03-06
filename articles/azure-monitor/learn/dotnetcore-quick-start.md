---
title: Gyors útmutató ASP.NET Core – Azure Monitor Application Insights
description: Útmutatást nyújt egy ASP.NET Core webalkalmazás gyors beállításához Azure Monitor-alapú figyeléshez Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: ef46b86186d1f5e26360de891b3a090ab0ece66b
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78894812"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Az ASP.NET Core-webalkalmazás monitorozásának indítása

Az Azure Application Insights segítségével egyszerűen monitorozhatja webalkalmazása rendelkezésre állását, teljesítményét és használatát. Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat. 

Ez a rövid útmutató végigvezeti a Application Insights SDK meglévő ASP.NET Core webalkalmazáshoz való hozzáadásának lépésein. Ha szeretné megtudni, hogyan konfigurálhatja a Application Insights a Visual Studio-előfizetések nélkül ez a [cikk](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

## <a name="prerequisites"></a>Előfeltételek

A gyorsútmutató elvégzéséhez:

- [Telepítse a Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) -et a következő munkaterhelésekkel:
  - ASP.NET és webfejlesztés
  - Azure-fejlesztés
- [A .NET Core 2.0 SDK telepítése](https://dotnet.microsoft.com/download)
- Szüksége lesz egy Azure-előfizetésre és egy meglévő .NET Core-webalkalmazásra.

Ha nem rendelkezik ASP.NET Core webalkalmazással, a lépésenkénti útmutatót követve [létrehozhat egy ASP.net Core alkalmazást, és hozzáadhatja Application Insights.](../../azure-monitor/app/asp-net-core.md)

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

Az Application Insights bármely, az internethez csatlakozó alkalmazásról képes telemetriaadatokat gyűjteni, függetlenül attól, hogy az a helyszínen vagy a felhőben fut-e. Az adatok megjelenítéséhez hajtsa végre az alábbi lépéseket.

1. Válassza az **Erőforrás létrehozása** > **Fejlesztői eszközök** > **Application Insights** elemet.

   > [!NOTE]
   >Ha első alkalommal hoz létre egy Application Insights-erőforrást, további információt az [Application Insights erőforrás létrehozása](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) doc webhelyén olvashat.

    Megjelenik egy konfigurációs mező. Az adatbeviteli mezők kitöltéséhez használja az alábbi táblát.

   | Beállítások        |  Érték           | Leírás  |
   | ------------- |:-------------|:-----|
   | **Name (Név)**      | Globálisan egyedi érték | A figyelt alkalmazást azonosító név |
   | **Erőforráscsoport**     | myResourceGroup      | Az új erőforráscsoport neve az alkalmazás-elemzési adatforrások üzemeltetéséhez. Létrehozhat egy új erőforráscsoportot, vagy használhat egy meglévőt is. |
   | **Hely** | USA keleti régiója | Válasszon egy Önhöz vagy az alkalmazást futtató gazdagéphez közeli helyet. |

2. Kattintson a  **Create** (Létrehozás) gombra.



## <a name="configure-app-insights-sdk"></a>Az App Insights SDK konfigurálása

1. Nyissa meg az ASP.NET Core-webalkalmazás **projektet** a Visual Studióban, majd kattintson a jobb gombbal az alkalmazás nevére a **Megoldáskezelőben**, és válassza a **Hozzáadás** > **Application Insights Telemetria** elemet.

    ![Application Insights Telemetria hozzáadása](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Kattintson az **első lépések** gombra

3. Válassza ki a fiókját és az előfizetést > Válassza ki a Azure Portal létrehozott **meglévő erőforrást** > kattintson a **regisztráció**elemre.

4. Válassza a **projekt** > **NuGet-csomagok kezelése** > **csomag forrása: NUGET.org** > **frissítse** a Application Insights SDK-csomagokat a legújabb stabil kiadásra.

5. Az alkalmazás indításához válassza a **Hibakeresés** > **Indítás hibakeresés nélkül** (Ctrl+F5) elemet

    ![Az Application Insights áttekintése menü](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> 3–5 percet is igénybe vehet, mire az adatok elkezdenek megjelenni a portálon. Ha az alkalmazás egy alacsony forgalmú tesztalkalmazás, vegye figyelembe, hogy a legtöbb metrika rögzítése csak akkor történik, ha aktív kérések és műveletek vannak folyamatban.

## <a name="start-monitoring-in-the-azure-portal"></a>Monitorozás indítása az Azure Portalon

1. Nyissa meg újra a Azure Portal Application Insights **Áttekintés** lapját a **Kezdőlap** lehetőség kiválasztásával, és a legutóbbi erőforrások területen válassza ki a korábban létrehozott erőforrást a jelenleg futó alkalmazás részleteinek megtekintéséhez.

   ![Az Application Insights áttekintése menü](./media/dotnetcore-quick-start/4overview.png)

2. Kattintson az **Alkalmazástérkép** elemre az alkalmazás-összetevők függőségi viszonyait mutató vizuális elrendezés megjelenítéséhez. Minden egyes összetevőnél megjelennek a KPI-k, például a terhelés, a teljesítmény, a hibák és a riasztások.

   ![Alkalmazástérkép](./media/dotnetcore-quick-start/5appmap.png)

3. Kattintson az **app Analytics** ikonra ![az alkalmazás-hozzárendelés ikon](./media/dotnetcore-quick-start/006.png) **Megtekintés az elemzésekben**. Megnyílik az **Application Insights Analytics**, amely egy részletes lekérdezési nyelvet biztosít az Application Insights által gyűjtött adatok elemzéséhez. Esetünkben most egy lekérdezés jön létre, amely a kérések számát egy diagramon jeleníti meg. A további adatok elemzéséhez írhat saját lekérdezéseket is.

   ![Az adott időtartamon belüli felhasználói kéréseket mutató elemzési diagram](./media/dotnetcore-quick-start/6analytics.png)

4. Térjen vissza az **Áttekintés** lapra, és vizsgálja meg a KPI-irányítópultokat.  Ezen az irányítópulton az alkalmazás állapotával kapcsolatos statisztikák jelennek meg, köztük a bejövő kérések száma, az egyes kérések időtartama, valamint az esetleges hibák. 

   ![Az Állapotáttekintési időegyenes diagramjai](./media/dotnetcore-quick-start/7kpidashboards.png)

5. A bal oldali kattintson a **metrikák**elemre. Az erőforrás állapotának és kihasználtságának vizsgálatához használja a metrikák Explorert. Az **Új diagram hozzáadása** gombra kattintva további egyéni nézeteket adhat hozzá, a **Szerkesztés** gombra kattintva pedig módosíthatja a meglévő diagramok típusát, magasságát, színpalettáját, csoportosításait és metrikáit. Létrehozhat például egy olyan diagramot, amely megjeleníti a böngésző átlagos betöltési idejét úgy, hogy kiveszi a "böngésző oldal betöltési ideje" lehetőséget a metrikák legördülő menüből és az "AVG" összesítésből való kiválasztásával. Ha többet szeretne megtudni az Azure Metrikaböngésző-ról, látogasson el [az azure Metrikaböngésző](../../azure-monitor/platform/metrics-getting-started.md)használatába.

     ![Metrikák lap: az átlagos böngészőbeli betöltési idő diagramja](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Videó

- Külső lépésenkénti útmutató a [Application Insights konfigurálásához a .net Core és a Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) használatával.
- Külső lépésenkénti videó arról, hogyan [konfigurálható a Application Insights a .net Core és a Visual Studio Code](https://youtu.be/ygGt84GDync) használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha végzett a teszteléssel, törölheti az erőforráscsoportot és az összes kapcsolódó erőforrást. Ehhez kövesse az alábbi lépéseket.

> [!NOTE]
> Ha meglévő erőforráscsoportot használt, az alábbi utasítások nem fognak működni, és csak törölni kell az egyéni Application Insights erőforrást. Ne feledje, hogy bármikor törli az erőforráscsoportot az összes olyan underyling-erőforrást, amely tagja a csoportnak.

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a **myResourceGroup** elemre.
2. Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a **myResourceGroup** szöveget a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Futásidejű kivételek észlelése és diagnosztizálása](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
