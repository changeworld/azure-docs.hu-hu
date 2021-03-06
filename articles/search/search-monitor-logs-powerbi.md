---
title: Azure Cognitive Search-naplók és-metrikák megjelenítése Power BI
description: Azure Cognitive Search-naplók és-metrikák megjelenítése Power BI
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 618033e24236d26601fafaf7b0678ee83ec973e8
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650150"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Azure Cognitive Search-naplók és-metrikák megjelenítése Power BI
Az [azure Cognitive Search](https://docs.microsoft.com/azure/search/search-what-is-azure-search) lehetővé teszi, hogy egy Azure Storage-fiókban tárolja a keresési szolgáltatáshoz tartozó műveleti naplókat és szolgáltatási metrikákat. Ezen a lapon megtudhatja, hogyan jelenítheti meg ezt az információt egy Power BI sablon alkalmazáson keresztül. Az alkalmazás részletes információkat nyújt a keresési szolgáltatásról, többek között a kereséssel, az indexeléssel, a műveletekkel és a szolgáltatási metrikákkal kapcsolatban.

A Power BI template app **Azure Cognitive Search: a naplók és a metrikák elemzése** a [Power bi apps piactéren](https://appsource.microsoft.com/marketplace/apps).

## <a name="how-to-get-started-with-the-app"></a>Az alkalmazás első lépései
1. Diagnosztikai naplózás engedélyezése a keresési szolgáltatáshoz:
    1. Hozzon létre vagy azonosítson egy meglévő [Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) , ahol archiválhatja a naplókat
    1. Navigáljon az Azure Cognitive Search szolgáltatásra a Azure Portal
    1. A bal oldali oszlopban a figyelés szakaszban válassza a **diagnosztikai beállítások** elemet.

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. Válassza a **+ diagnosztikai beállítás hozzáadása** lehetőséget
    1. Keresse meg az **archívumot egy Storage-fiókban**, adja meg a Storage-fiók adatait, és keresse meg a **OperationLogs** és a **AllMetrics**

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. Kattintson a **Mentés** gombra

1. A naplózás engedélyezése után a keresési szolgáltatással megkezdheti a naplók és mérőszámok generálását. Akár egy óráig is eltarthat, amíg a tárolók megjelennek a blob Storage-ban ezekkel a naplókkal. Ekkor megjelenik egy elemzések – **naplók – operationlogs** tároló a forgalmi naplók és az elemzések – **mérőszámok – pt1m** tároló a metrikák számára.

1. Keresse meg az Azure Cognitive Search Power BI alkalmazást a [Power bi alkalmazások piactéren](https://appsource.microsoft.com/marketplace/apps) , és telepítse egy új munkaterületre vagy egy meglévő munkaterületre. Az alkalmazás neve **Azure Cognitive Search: a naplók és a metrikák elemzése**.

1. Az alkalmazás telepítése után válassza ki az alkalmazást az alkalmazások listájából Power BI.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. Válassza a **Kapcsolódás** lehetőséget az adatkapcsolathoz

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. Adja meg a naplókat és mérőszámokat tartalmazó Storage-fiók nevét. Alapértelmezés szerint az alkalmazás az utolsó 10 napot fogja megtekinteni, de ezt az értéket a **Days** paraméterrel lehet megváltoztatni.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. A hitelesítési módszerként válassza a **kulcs** lehetőséget, és adja meg a Storage-fiók kulcsát. Adatvédelmi szintként válassza a **magánjellegű** lehetőséget. A betöltési folyamat megkezdéséhez kattintson a Bejelentkezés gombra.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. Várjon, amíg az adatgyűjtés frissül. Ez eltarthat egy ideig, attól függően, hogy mennyi adattal rendelkezik. Láthatja, hogy az alábbi kijelző alapján a rendszer továbbra is frissíti-e az adott információt.

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. Az Adatfrissítés befejezését követően válassza az **Azure Cognitive Search jelentés** lehetőséget a jelentés megtekintéséhez.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. Győződjön meg arról, hogy a jelentés megnyitása után frissítse az oldalt, hogy az megnyíljon az adataival.

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>Az alkalmazás paramétereinek módosítása
Ha egy másik Storage-fiókból kívánja megjeleníteni az adatok megjelenítését, vagy módosítani szeretné a lekérdezési napok számát, kövesse az alábbi lépéseket a **napok** és a **StorageAccount** paramétereinek módosításához.

1. Navigáljon a Power BI alkalmazásaihoz, keresse meg az Azure Cognitive Search alkalmazást, és válassza az **alkalmazás szerkesztése** gombot a munkaterület megtekintéséhez.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. Válassza a **Beállítások** lehetőséget az adatkészlet beállításai közül.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. Az adatkészletek lapon módosítsa a paraméterek értékeit, majd válassza az **alkalmaz**lehetőséget. Ha probléma merül fel a kapcsolatban, frissítse az adatforrás hitelesítő adatait ugyanazon a lapon.

1. Lépjen vissza a munkaterületre, és válassza a **Frissítés most** lehetőséget az adatkészlet beállításai között.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. Nyissa meg a jelentést a frissített adatértékek megtekintéséhez. Előfordulhat, hogy frissítenie kell a jelentést a legfrissebb adatértékek megtekintéséhez.

## <a name="troubleshooting"></a>Hibakeresés
Ha úgy találja, hogy az adatai nem láthatók, kövesse az alábbi hibaelhárítási lépéseket:

1. Nyissa meg a jelentést, és frissítse az oldalt, és győződjön meg róla, hogy a legfrissebb adatnézeteket tekinti meg. A jelentésben lehetőség van az adatfrissítésre. Ezzel a beállítással hozzájuthat a legfrissebb értékekhez.

1. Győződjön meg arról, hogy a Storage-fiók neve és a megadott hozzáférési kulcs helyes. A Storage-fiók nevének meg kell egyeznie a keresési szolgáltatás naplóival konfigurált fiók nevével.

1. Győződjön meg arról, hogy a Storage-fiók tartalmazza a tárolók elemzéseit – **naplók – operationlogs** és megállapítások **– mérőszámok – pt1m** és minden tároló rendelkezik adatokkal. A naplók és a metrikák a mappák pár rétegén belül lesznek.

1. Ellenőrizze, hogy az adatkészlet továbbra is frissül-e. A frissítési állapot jelzője a fenti 8. lépésben látható. Ha továbbra is frissül, várjon, amíg a frissítés be nem fejeződik a jelentés megnyitásához és frissítéséhez.

## <a name="next-steps"></a>Következő lépések
[További információ az Azure Cognitive Search](https://docs.microsoft.com/azure/search/)

[Mi az Power BI?](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[A Power BI szolgáltatás tervezők alapfogalmai](https://docs.microsoft.com/power-bi/service-basic-concepts)