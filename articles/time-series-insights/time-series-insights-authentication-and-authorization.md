---
title: API-hitelesítés és-engedélyezés – Azure Time Series Insights | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható a hitelesítés és engedélyezés egy olyan egyéni alkalmazáshoz, amely meghívja a Azure Time Series Insights API-t.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: ff5f7a80e2dcedb1795bae14ee9140c2842303a5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984570"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Hitelesítés és engedélyezés Azure Time Series Insights API-hoz

Ez a dokumentum azt ismerteti, hogyan regisztrálhat egy alkalmazást Azure Active Directory az új Azure Active Directory panel használatával. A Azure Active Directory regisztrált alkalmazások lehetővé teszik a felhasználók számára, hogy hitelesítsék magukat a Time Series Insights-környezethez társított Azure Time Series Insight API használatával.

> [!IMPORTANT]
> Azure Time Series Insights a következő hitelesítési könyvtárakat támogatja:
> * A legújabb [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * A [Azure Active Directory hitelesítési könyvtár (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Egyszerű szolgáltatásnév

Az alábbi szakaszok azt ismertetik, hogyan konfigurálható egy alkalmazás a Time Series Insights API-nak egy alkalmazás nevében való eléréséhez. Előfordulhat, hogy az alkalmazás a saját alkalmazás hitelesítő adataival lekérdezi vagy közzéteszi a Time Series Insights környezetben a Azure Active Directory használatával.

## <a name="summary-and-best-practices"></a>Összefoglalás és ajánlott eljárások

A Azure Active Directory alkalmazás regisztrációs folyamata három fő lépést foglal magában.

1. [Alkalmazás regisztrálása](#azure-active-directory-app-registration) Azure Active Directoryban.
1. Engedélyezze, hogy az alkalmazás [hozzáférjen a Time Series Insights-környezethez](#granting-data-access).
1. Az **alkalmazás-azonosító** és az **ügyfél titka** segítségével szerezzen be egy jogkivonatot az [ügyfélalkalmazás](#client-app-initialization)`https://api.timeseries.azure.com/`ból. A jogkivonat ezután felhasználható a Time Series Insights API meghívásához.

A **3. lépésben**az alkalmazás és a felhasználói hitelesítő adatok elkülönítése lehetővé teszi a következőket:

* Rendeljen engedélyeket az alkalmazás identitásához, amely nem azonos a saját engedélyeivel. Ezek az engedélyek jellemzően csak az alkalmazás által igényelt jogosultságokra korlátozódnak. Engedélyezheti például, hogy az alkalmazás csak egy adott Time Series Insights-környezetből olvassa be az adatokat.
* A felhasználó hitelesítési hitelesítő adatainak létrehozása az **ügyfél titkos** vagy biztonsági tanúsítványának használatával elkülönítheti az alkalmazás biztonságát. Ennek eredményeképpen az alkalmazás hitelesítő adatai nem függenek egy adott felhasználó hitelesítő adataitól. Ha a felhasználó szerepköre megváltozik, az alkalmazás nem feltétlenül igényel új hitelesítő adatokat vagy további konfigurálást. Ha a felhasználó megváltoztatja a jelszavát, az alkalmazáshoz való összes hozzáféréshez nincs szükség új hitelesítő adatokra vagy kulcsokra.
* Egy felügyelet nélküli parancsfájlt nem egy adott felhasználó hitelesítő adatai, hanem egy **titkos** vagy egy biztonsági tanúsítvány használatával futtathat.
* Jelszó helyett biztonsági tanúsítványt használjon a Azure Time Series Insights API-hoz való hozzáférés biztonságossá tételéhez.

> [!IMPORTANT]
> A Azure Time Series Insights biztonsági házirend konfigurálásakor kövesse az alábbi, a fenti forgatókönyvben ismertetett **problémáinak elkülönítésének** elvét.

> [!NOTE]
> * A cikk egy egybérlős alkalmazásra koncentrál, amelyben az alkalmazás csak egy szervezeten belül fut.
> * Általában egybérlős alkalmazásokat fog használni a szervezetében futó üzletági alkalmazásokhoz.

## <a name="detailed-setup"></a>Részletes beállítás

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory alkalmazás regisztrálása

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Adathozzáférés biztosítása

1. Az Time Series Insights-környezethez válassza az **adatelérési házirendek** elemet, majd válassza a **Hozzáadás**lehetőséget.

   [![új adatelérési házirend hozzáadása a Time Series Insights-környezethez](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. A **felhasználó kiválasztása** párbeszédpanelen illessze be az **alkalmazás nevét** vagy az **alkalmazás azonosítóját** a Azure Active Directory-alkalmazás regisztrációja szakaszba.

   [alkalmazás keresése ![a felhasználó kiválasztása párbeszédpanelen](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Válassza ki a szerepkört. Válassza ki az **olvasót** az adatlekérdezéshez vagy a **közreműködőhöz** az adatgyűjtés és a hivatkozási adatváltozások lekérdezéséhez. Kattintson az **OK** gombra.

   [a felhasználói szerepkör kiválasztása párbeszédpanel ![válassza az olvasó vagy közreműködő elemet](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Mentse a szabályzatot az **OK gombra**kattintva.

   > [!TIP]
   > A speciális adatelérési lehetőségekért olvassa el az [adathozzáférés megadását](./time-series-insights-data-access.md)ismertetőt.

### <a name="client-app-initialization"></a>Ügyfélalkalmazás inicializálása

* A fejlesztők a [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) vagy a [Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) használatával végezhetik el a hitelesítést Azure Time Series Insights.

* Például a ADAL használatával történő hitelesítéshez:

   1. A jogkivonat az alkalmazás nevében történő beszerzéséhez használja az **alkalmazás-azonosító** és az **ügyfél titkos** kulcsát (application Key) a Azure Active Directory-alkalmazás regisztrációja szakaszban.

   1. A C#-ben a következő kód beolvashatja a jogkivonatot az alkalmazás nevében. Teljes minta: [lekérdezési információk C# ](time-series-insights-query-data-csharp.md)olvasása a használatával.

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. Ezután a jogkivonat átadható a `Authorization` fejlécben, amikor az alkalmazás meghívja a Time Series Insights API-t.

* Azt is megteheti, hogy a fejlesztők a MSAL használatával hitelesítik magukat. További információért olvassa el a [Migrálás a MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) -ra és a [Azure Time Series Insights- C# környezettel kapcsolatos fontos információk kezelése](time-series-insights-manage-reference-data-csharp.md) című cikket. 

## <a name="common-headers-and-parameters"></a>Gyakori fejlécek és paraméterek

Ez a szakasz a gyakori HTTP-kérelmek fejléceit és paramétereit ismerteti a Time Series Insights GA és az előzetes verziójú API-k használatával végzett lekérdezések végrehajtásához. Az API-specifikus követelmények részletesebben szerepelnek a [Time Series Insights REST API](https://docs.microsoft.com/rest/api/time-series-insights/)dokumentációjában.

> [!TIP]
> A REST API-k használatáról, a HTTP-kérelmek elvégzéséről és a HTTP-válaszok kezeléséről az [Azure REST API dokumentációjában](https://docs.microsoft.com/rest/api/azure/) olvashat bővebben.

### <a name="authentication"></a>Hitelesítés

Ha hitelesített lekérdezéseket szeretne végrehajtani a [Time Series INSIGHTS REST API](https://docs.microsoft.com/rest/api/time-series-insights/)-kkal szemben, egy érvényes OAuth 2,0 tulajdonosi jogkivonatot kell átadni az [engedélyezési fejlécben](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) az Ön által választott Rest-ügyfél C#(Poster, JavaScript,) használatával. 

> [!TIP]
> Tekintse át az üzemeltetett Azure Time Series Insights [Client SDK minta vizualizációját](https://tsiclientsample.azurewebsites.net/) , amelyből megtudhatja, hogyan végezheti el a hitelesítést az Time Series Insights API-kkal programozott módon a [JavaScript ügyféloldali SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) -val a diagramok és diagramok használatával.

### <a name="http-headers"></a>HTTP-fejlécek

Az alábbiakban a szükséges kérések fejléceit mutatjuk be.

| Kötelező kérelem fejléce | Leírás |
| --- | --- |
| Engedélyezés | Time Series Insights használatával történő hitelesítéshez érvényes OAuth 2,0 tulajdonosi jogkivonatot kell átadni az **engedélyezési** fejlécben. | 

> [!IMPORTANT]
> A jogkivonatot pontosan ki kell adni a `https://api.timeseries.azure.com/` erőforrásnak (más néven a token célközönségének).
> * A [Poster](https://www.getpostman.com/) **AuthURL** ezért a következő lesz: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/` érvényes, de `https://api.timeseries.azure.com` nem.

Az opcionális kérések fejlécei alább olvashatók.

| Választható kérelemfejléc | Leírás |
| --- | --- |
| Content-Type | csak `application/json` támogatott. |
| x-MS-Client-Request-ID | Egy ügyfél-kérelem azonosítója. A szolgáltatás rögzíti ezt az értéket. Lehetővé teszi, hogy a szolgáltatás nyomkövetési műveletet végez a szolgáltatások között. |
| x-MS-Client-Session-ID | Ügyfél-munkamenet-azonosító. A szolgáltatás rögzíti ezt az értéket. Lehetővé teszi, hogy a szolgáltatás a kapcsolódó műveletek egy csoportját nyomon követhessék a szolgáltatások között. |
| x-MS-Client-Application-Name | Annak az alkalmazásnak a neve, amely a kérelmet generálta. A szolgáltatás rögzíti ezt az értéket. |

Nem kötelező, de az ajánlott válasz fejlécek az alábbiakban olvashatók.

| Válasz fejléce | Leírás |
| --- | --- |
| Content-Type | csak `application/json` támogatott. |
| x-MS-Request-ID | Kiszolgáló által generált kérelem azonosítója. Felhasználható arra, hogy felvegye a kapcsolatot a Microsofttal egy kérelem kivizsgálására. |
| x-MS-Property – nem található – viselkedés | A GA API opcionális válaszának fejléce. A lehetséges értékek a következők: `ThrowError` (alapértelmezett) vagy `UseNull`. |

### <a name="http-parameters"></a>HTTP-paraméterek

> [!TIP]
> A szükséges és választható lekérdezési információkkal kapcsolatos további információkért tekintse meg a [dokumentációt](https://docs.microsoft.com/rest/api/time-series-insights/).

A kötelező URL-lekérdezési karakterlánc paraméterei az API-verziótól függenek.

| Kiadás | Lehetséges API-verziók értékei |
| --- |  --- |
| Általános rendelkezésre állás | `api-version=2016-12-12`|
| Előzetes verzió | `api-version=2018-11-01-preview` |
| Előzetes verzió | `api-version=2018-08-15-preview` |

A nem kötelező URL-lekérdezési karakterlánc paraméterei közé tartozik a HTTP-kérelem végrehajtási időkorlátjának beállítása.

| Választható lekérdezési paraméter | Leírás | Verzió |
| --- |  --- | --- |
| `timeout=<timeout>` | Kiszolgálóoldali időtúllépés a HTTP-kérelmek végrehajtásához. Csak a [környezeti események beolvasása](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) és a [környezeti összesítések API-k beszerzése](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) esetén alkalmazható. Az időtúllépési értéknek ISO 8601 időtartam formátumúnak kell lennie, például `"PT20S"`, és a tartományba kell esnie `1-30 s`. Az alapértelmezett érték `30 s`. | FE |
| `storeType=<storeType>` | A meleg tárolást engedélyező előnézeti környezetekben a lekérdezés a `WarmStore`on vagy `ColdStore`is végrehajtható. A lekérdezésben szereplő paraméter határozza meg, hogy a lekérdezés melyik tárolóban legyen végrehajtva. Ha nincs meghatározva, a rendszer végrehajtja a lekérdezést a hűtőházi tárolón. A meleg tároló lekérdezéséhez a **storeType** -t `WarmStore`értékre kell állítani. Ha nincs meghatározva, a rendszer végrehajtja a lekérdezést a hűtőházi tárolón. | Előzetes verzió |

## <a name="next-steps"></a>Következő lépések

- A GA Time Series Insights API-t meghívó mintakód esetében olvassa el a [lekérdezési adatai C# ](./time-series-insights-query-data-csharp.md)a következő használatával:.

- Az előzetes verziójú Time Series Insights API-kódok mintáinak megtekintéséhez olvassa el a [lekérdezés-előnézeti adatai C# ](./time-series-insights-update-query-data-csharp.md)a következővel:

- Az API-referenciákkal kapcsolatos információkért olvassa el a [lekérdezési API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api) dokumentációját.

- Megtudhatja, hogyan [hozhat létre egyszerű szolgáltatásnevet](../active-directory/develop/howto-create-service-principal-portal.md).
