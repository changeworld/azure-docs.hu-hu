---
title: Metrikák és naplók figyelése az Azure-beli előtérben szolgáltatásban | Microsoft Docs
description: Ez a cikk azokat a különböző mérőszámokat és hozzáférési naplókat ismerteti, amelyekkel az Azure bejárati szolgáltatása támogatja
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: e379e67fb733c968a755afd245d079239f559c89
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751401"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Metrikák és naplók figyelése az Azure bejárati ajtó szolgáltatásban

Az Azure bejárati ajtó szolgáltatásának használatával a következő módokon figyelheti az erőforrásokat:

- **Metrikák**. Az Azure bejárati ajtajának jelenleg hét mérőszáma van a teljesítményszámlálók megtekintéséhez.
- **Naplók**. A tevékenység-és diagnosztikai naplók lehetővé teszik a teljesítmény, hozzáférés és egyéb adatok mentését, illetve az erőforrásokból való felhasználását figyelés céljából.

### <a name="metrics"></a>Metrikák

A metrikák bizonyos Azure-erőforrások, amelyek lehetővé teszik a teljesítményszámlálók megtekintését a portálon. A következő elérhetők a bejárati ajtó metrikái:

| Metrika | Metrika megjelenítendő neve | Unit (Egység) | Dimenziók | Leírás |
| --- | --- | --- | --- | --- |
| RequestCount | Kérelmek száma | Mennyiség | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | A bejárati ajtó által kiszolgált ügyfél-kérelmek száma.  |
| RequestSize | Kérelem mérete | Bájt | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Az ügyfelektől a befelé irányuló kérelmekként küldött bájtok száma. |
| ResponseSize | Válasz mérete | Bájt | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | A bejárati ajtótól az ügyfeleknek küldött válaszként küldött bájtok száma. |
| TotalLatency | Teljes késés | Ezredmásodperc | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Az ügyféltől érkező kérelemből kiszámított idő, amíg az ügyfél el nem fogadta az utolsó válasz bájtját a bejárati ajtótól. |
| BackendRequestCount | Háttérbeli kérelmek száma | Mennyiség | HttpStatus</br>HttpStatusGroup</br>Háttérszolgáltatás | A bejárati ajtóról a háttérbe küldött kérések száma. |
| BackendRequestLatency | Háttérbeli kérelmek késése | Ezredmásodperc | Háttérszolgáltatás | A kérelemnek a háttérbe való beérkezése után kiszámított idő, amíg be nem érkezett az utolsó válasz bájtja a háttérből. |
| BackendHealthPercentage | Háttér állapotának százalékos aránya | Százalék | Háttérszolgáltatás</br>Háttérkészletek | A sikeres állapot-mintavételek százalékos aránya a bejárati ajtóról a háttérbe. |
| WebApplicationFirewallRequestCount | Webalkalmazási tűzfalra vonatkozó kérelmek száma | Mennyiség | PolicyName</br>RuleName</br>Műveletek | Az alkalmazási rétegben a bejárati ajtó biztonságával feldolgozott ügyfélalkalmazások száma |

## <a name="activity-log"></a>Tevékenységnaplók

A Tevékenységnaplók a bejárati szolgáltatáson végzett műveletekről nyújtanak információt. Azt is meghatározzák, hogy mi, ki és mikor történik a bejárati szolgáltatáson elvégzett bármilyen írási művelet (Put, post vagy DELETE).

>[!NOTE]
>A Tevékenységnaplók nem tartalmaznak olvasási (Get) műveleteket. Emellett nem tartalmazzák a Azure Portal vagy az eredeti felügyeleti API használatával végrehajtott műveleteket.

A hozzáférési tevékenység naplói a bejárati ajtó szolgáltatásban vagy az Azure-erőforrások összes naplójában Azure Monitor. A tevékenységnaplók megtekintése:

1. Válassza ki az előtérben lévő példányt.
2. Válassza a **műveletnapló**lehetőséget.

    ![Tevékenységnapló](./media/front-door-diagnostics/activity-log.png)

3. Válasszon egy szűrési hatókört, majd kattintson az **alkalmaz**gombra.

## <a name="diagnostic-logging"></a>Diagnosztikai naplók
A diagnosztikai naplók részletes információkat biztosítanak a naplózáshoz és a hibaelhárításhoz fontos műveletekről és hibákról. A diagnosztikai naplók eltérnek a tevékenység naplóitól.

A Tevékenységnaplók betekintést nyújtanak az Azure-erőforrásokon végzett műveletekre. A diagnosztikai naplók betekintést nyújtanak az erőforrás által végrehajtott műveletekre. További információ: [Azure monitor diagnosztikai naplók](../azure-monitor/platform/platform-logs-overview.md).

![Diagnosztikai naplók](./media/front-door-diagnostics/diagnostic-log.png)

Diagnosztikai naplók konfigurálása az előtérben lévő szolgáltatáshoz:

1. Válassza ki az Azure bejárati ajtó szolgáltatását.

2. Válassza a **diagnosztikai beállítások**lehetőséget.

3. Kattintson **a diagnosztika bekapcsolása**elemre. Archiválja a diagnosztikai naplókat, valamint mérőszámokat egy Storage-fiókba, továbbíthatja őket egy Event hubhoz, vagy elküldheti azokat Azure Monitor naplókba.

A bejárati ajtó szolgáltatás jelenleg a diagnosztikai naplókat (óránként kötegelt feldolgozást) biztosítja. A diagnosztikai naplók egyedi API-kérelmeket biztosítanak minden egyes bejegyzéshez a következő sémával:

| Tulajdonság  | Leírás |
| ------------- | ------------- |
| ClientIp (Ügyfél IP-címe) | Annak az ügyfélnek az IP-címe, amely a kérelmet elvégezte. |
| ClientPort | Az ügyfél IP-portja, amely a kérést elvégezte. |
| HttpMethod | A kérelem által használt HTTP-metódus. |
| HttpStatusCode | A proxy által visszaadott HTTP-állapotkód. |
| HttpStatusDetails | Az eredményül kapott állapot a kérelemben. A karakterlánc értékének jelentése az állapot-hivatkozási táblában található. |
| HttpVersion | A kérelem vagy a kapcsolatok típusa. |
| RequestBytes | A HTTP-kérelem üzenetének mérete bájtban, beleértve a kérések fejléceit és a kérelem törzsét. |
| RequestUri | A fogadott kérelem URI-ja. |
| ResponseBytes | A háttér-kiszolgáló által válaszként küldött bájtok.  |
| RoutingRuleName | Annak az útválasztási szabálynak a neve, amelyhez a kérelem illeszkedik. |
| SecurityProtocol | A kérelem által használt TLS/SSL protokoll verziója vagy Null, ha nincs titkosítás. |
| Eltelt idő | A művelet végrehajtásának időtartama (ezredmásodpercben). |
| UserAgent | Az ügyfél által használt böngésző típusa |
| TrackingReference | A bejárati ajtó által kiszolgált kérést azonosító egyedi hivatkozási sztring, amely az ügyfélnek X-Azure-ref fejlécként is elküldve. Egy adott kérelem hozzáférési naplóiban található adatok kereséséhez szükséges. |

## <a name="next-steps"></a>Következő lépések

- [Bejárati ajtó profiljának létrehozása](quickstart-create-front-door.md)
- [Az előtérben működik](front-door-routing-architecture.md)
