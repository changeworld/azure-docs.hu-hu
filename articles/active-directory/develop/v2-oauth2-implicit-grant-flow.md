---
title: OAuth 2,0 implicit engedélyezési folyamat – Microsoft Identity platform | Azure
description: Egyoldalas alkalmazások biztonságossá tétele a Microsoft Identity platform implicit folyamatával.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 4f35162be513873637f9a87260410d891fdace10
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300468"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft Identity platform és implicit engedélyezési folyamat

A Microsoft Identity platform végpontja segítségével a Microsofttól személyes és munkahelyi vagy iskolai fiókkal is aláírhatja a felhasználókat a saját egyoldalas alkalmazásokba. Az egyoldalas és más JavaScript-alkalmazások, amelyek elsődlegesen egy böngészőben futnak, néhány érdekes kihívást jelentenek a hitelesítés során:

* Ezeknek az alkalmazásoknak a biztonsági jellemzői jelentősen eltérnek a hagyományos kiszolgáló-alapú webalkalmazások.
* Számos engedélyezési kiszolgáló és Identitáskezelő nem támogatja a CORS kérelmeket.
* A teljes oldal böngészője átirányítja az alkalmazást az alkalmazásból, így a felhasználói élmény különösen ininvazív lehet.

Ezekhez az alkalmazásokhoz (AngularJS, ember. js, reagálás. js stb.) a Microsoft Identity platform támogatja a OAuth 2,0 implicit engedélyezési folyamatot. Az implicit folyamat leírása a [OAuth 2,0 specifikációban](https://tools.ietf.org/html/rfc6749#section-4.2)található. Elsődleges előnye, hogy lehetővé teszi, hogy az alkalmazás a háttér-kiszolgáló hitelesítő adatainak cseréje nélkül beszerezze a jogkivonatokat a Microsoft Identity platformból. Ez lehetővé teszi az alkalmazás számára, hogy bejelentkezzen a felhasználóval, fenntartsa a munkamenetet, és más webes API-kra is lekérje az ügyfél JavaScript-kódjában található összes tokent. Néhány fontos biztonsági szempontot figyelembe kell venni, amikor az implicit folyamatot kifejezetten az [ügyfél](https://tools.ietf.org/html/rfc6749#section-10.3) -és a [felhasználói megszemélyesítést](https://tools.ietf.org/html/rfc6749#section-10.3)használja.

Ez a cikk azt ismerteti, hogyan lehet programozni közvetlenül az alkalmazás protokollját.  Ha lehetséges, javasoljuk, hogy a támogatott Microsoft hitelesítési kódtárakat (MSAL) használja a [jogkivonatok beszerzése és a biztonságos webes API-k hívása](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)helyett.  Tekintse meg az MSAL-t [használó példákat](sample-v2-code.md)is.

Ha azonban inkább nem szeretne használni egy könyvtárat az egyoldalas alkalmazásban, és saját maga is küldje a protokollok üzeneteit, kövesse az alábbi általános lépéseket.

> [!NOTE]
> Nem minden Azure Active Directory-(Azure AD-) forgatókönyvet és szolgáltatást támogat a Microsoft Identity platform végpontja. Annak megállapításához, hogy érdemes-e a Microsoft Identity platform-végpontot használni, olvassa el a [Microsoft Identity platform korlátozásait](active-directory-v2-limitations.md)ismertetőt.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>A OAuth2 implicit engedélyezésének megfelelő forgatókönyvei

A OAuth2-specifikáció kijelenti, hogy az implicit támogatás úgy lett kialakítva, hogy lehetővé tegye a felhasználói ügynök alkalmazásai számára – azaz a böngészőben futtatott JavaScript-alkalmazásokat. Az ilyen alkalmazások meghatározó jellemzője, hogy a JavaScript-kód a kiszolgálói erőforrások (általában a webes API-k) elérésére és ennek megfelelően az alkalmazás felhasználói élményének frissítésére szolgál. Gondoljon olyan alkalmazásokra, mint például a Gmail vagy az Outlook Web Access: Amikor kiválaszt egy üzenetet a Beérkezett üzenetek mappából, csak az üzenet vizualizáció paneljén változik az új kijelölés megjelenítése, míg a lap többi része változatlan marad. Ez a jellemző ellentétben áll a hagyományos átirányítási webalkalmazásokkal, ahol minden felhasználói interakció a teljes oldal visszaküldését és az új kiszolgáló válaszának teljes oldalát jeleníti meg.

Azok az alkalmazások, amelyek a JavaScript-alapú megközelítést használják szélsőségesként, egyoldalas alkalmazásoknak vagy Gyógyfürdőknek nevezzük. Az elképzelés az, hogy ezek az alkalmazások csak a kezdeti HTML-oldalt és a kapcsolódó JavaScriptet szolgálják ki, és a JavaScripten keresztül végrehajtott webes API-hívások által vezérelt minden további interakciót. Azonban a hibrid megközelítések, amelyekben az alkalmazás többnyire visszaküldéssel vezérelt, de alkalmanként JS-hívásokat hajt végre, nem ritka – az implicit flow használatáról szóló vitát is fontos.

Az átirányítási alkalmazások általában cookie-kon keresztül védik a kéréseiket, azonban ez a megközelítés nem működik jól a JavaScript-alkalmazásokhoz. A cookie-k csak a által létrehozott tartományon működnek, míg a JavaScript-hívások más tartományokra is irányíthatók. Valójában ez gyakran előfordul: a Microsoft Graph API-t, az Office API-t, az Azure API-t, a tartományon kívüli, az alkalmazás kiszolgálása helyétől eltérő alkalmazásokat. A JavaScript-alkalmazások egyre növekvő tendenciát mutatnak, ha nincs háttérrendszer, a harmadik féltől származó webes API-kra támaszkodva 100%-ot az üzleti funkció megvalósítására.

Jelenleg a webes API-hívások védelmének előnyben részesített módszere a OAuth2 tulajdonosi jogkivonat megközelítésének használata, amelyben minden hívást egy OAuth2 hozzáférési token kísér. A webes API megvizsgálja a bejövő hozzáférési jogkivonatot, és ha megtalálja a szükséges hatóköröket, akkor hozzáférést biztosít a kért művelethez. Az implicit folyamat kényelmes mechanizmust biztosít a JavaScript-alkalmazásokhoz a webes API-k hozzáférési jogkivonatának beszerzéséhez, ami számos előnyt kínál a cookie-kkal kapcsolatban:

* A tokeneket megbízhatóan lehet megszerezni a több eredetű hívás szükségessége nélkül – az átirányítási URI kötelező regisztrációja, amelynél a tokenek visszaállnak, a tokenek nem helyezhetők el.
* A JavaScript-alkalmazások a szükségesnél több hozzáférési jogkivonatot szerezhetnek be, az adott számú webes API-ra vonatkozóan – a tartományok korlátozása nélkül
* A HTML5-funkciók, például a munkamenet vagy a helyi tárterület teljes hozzáférést biztosítanak a jogkivonat-gyorsítótárazáshoz és az élettartam-kezeléshez, míg a cookie-k kezelése az alkalmazásba
* A hozzáférési jogkivonatok nem érzékenyek a helyek közötti kérelmek hamisítására (CSRF) irányuló támadásokra

Az implicit engedélyezési folyamat nem ad ki frissítési jogkivonatokat, főleg biztonsági okokból. A frissítési token nem a hozzáférési jogkivonatoknak megfelelően van leszűkítve, így sokkal nagyobb hatékonyságot biztosít, így sokkal nagyobb károkat okoz, ha kiszivárgott. Az implicit folyamat során a tokenek az URL-címben lesznek továbbítva, így az elfogás kockázata magasabb, mint az engedélyezési kód megadásakor.

Egy JavaScript-alkalmazás azonban egy másik mechanizmussal rendelkezik a hozzáférési tokenek megújításához, anélkül, hogy ismételten megkéri a felhasználót a hitelesítő adatok megadására. Az alkalmazás a rejtett iframe használatával új jogkivonat-kérelmeket hajthat végre az Azure AD engedélyezési végpontján: Ha a böngésző továbbra is aktív munkamenettel rendelkezik (olvasás: munkamenet-cookie) az Azure AD-tartományon, a hitelesítési kérelem a felhasználói beavatkozás szükségessége nélkül is sikeresen bekövetkezik.

Ez a modell lehetővé teszi, hogy a JavaScript-alkalmazás önállóan megújítsa a hozzáférési jogkivonatokat, és új API-k számára is újat szerezzen (ha a felhasználó korábban beleegyezett hozzájuk). Ezzel elkerülheti a nagy értékű összetevők, például a frissítési token beszerzésének, fenntartásának és védelmének további terheit. A csendes megújulást lehetővé teszi, hogy az Azure AD munkamenet-cookie-t az alkalmazáson kívül felügyeli a rendszer. Ennek a módszernek egy másik előnye, hogy egy felhasználó kijelentkezhet az Azure AD-ből az Azure AD-be bejelentkezett bármely alkalmazás használatával, amely a böngésző lapjain fut. Ez az Azure AD munkamenet-cookie törlését eredményezi, és a JavaScript alkalmazás automatikusan elveszíti a kijelentkezett felhasználó jogkivonatának megújítását.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Az implicit engedélyezési funkció megfelelő az alkalmazáshoz?

Az implicit támogatás nagyobb kockázatot jelent, mint a többi támogatás, és a figyelmet igényelő területek jól dokumentálva vannak (például: [hozzáférési jogkivonat használata az erőforrás tulajdonosának megszemélyesítéséhez az implicit flow-ban] [OAuth2-spec-implicit-visszaélés] és [OAuth 2,0 Threat Model és biztonsági megfontolások] [OAuth2-Threat-Model-and-Security-vonatkozásai]). A magasabb kockázati profilt azonban nagyrészt az a tény okozza, hogy az aktív kódot végrehajtó alkalmazásokat egy távoli erőforrás szolgáltatja egy böngészőnek. Ha gyógyfürdő-architektúrát tervez, nem rendelkezik háttér-összetevőkkel, vagy webes API-t szeretne meghívni JavaScripten keresztül, a jogkivonat-beszerzéshez használt implicit folyamat használata javasolt.

Ha az alkalmazás egy natív ügyfél, az implicit folyamat nem remekül illeszkedik. Az Azure AD-munkamenet cookie-nak a natív ügyfél kontextusában való hiánya megfosztja az alkalmazást a hosszú élettartamú munkamenet fenntartásának eszközeitől. Ez azt jelenti, hogy az alkalmazás többször is kéri a felhasználót, amikor hozzáférési jogkivonatokat kap az új erőforrásokhoz.

Ha olyan webalkalmazást fejleszt, amely hátteret tartalmaz, és egy API-t használ a háttér-kódjából, az implicit folyamat szintén nem megfelelő. A többi támogatás sokkal nagyobb teljesítményt nyújt. A OAuth2 ügyfél-hitelesítő adatok megadása például lehetővé teszi az alkalmazáshoz hozzárendelt engedélyeket tükröző jogkivonatok beszerzését, a felhasználói delegálásokkal szemben. Ez azt jelenti, hogy az ügyfélnek lehetősége van az erőforrásokhoz való programozott hozzáférés fenntartására, még akkor is, ha a felhasználó nem vesz aktívan részt egy munkamenetben, és így tovább. Nem csak ez, de az ilyen támogatások nagyobb biztonsági garanciákat biztosítanak. Például a hozzáférési tokenek soha nem haladnak át a felhasználói böngészőn keresztül, ezért a böngésző előzményeiben nem kerülnek mentésre, így tovább. Az ügyfélalkalmazás emellett erős hitelesítést is végezhet a jogkivonat kérésekor.

## <a name="protocol-diagram"></a>Protokoll diagramja

Az alábbi ábrán látható, hogy a teljes implicit bejelentkezési folyamat hogyan néz ki, valamint az egyes lépéseket követő szakaszok részletesebb leírását.

![Az implicit bejelentkezési folyamatot bemutató diagram](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Bejelentkezési kérelem küldése

Ahhoz, hogy először aláírja a felhasználót az alkalmazásba, elküldheti az [OpenID Connect](v2-protocols-oidc.md) hitelesítési kérését, és beszerezhet egy `id_token` a Microsoft Identity platform végpontból.

> [!IMPORTANT]
> Egy azonosító jogkivonat és/vagy hozzáférési jogkivonat sikeres igényléséhez az alkalmazásnak a [Azure Portal-Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapon való regisztrálásához engedélyezve kell lennie a megfelelő implicit engedélyezési folyamatnak, az **azonosító tokenek** **és a.** **hozzáférési tokenek** kiválasztásával. Ha nincs engedélyezve, `unsupported_response` hibát ad vissza: a (z) **"response_type" bemeneti paraméter megadott értéke nem engedélyezett ehhez az ügyfélhez. A várt érték a "code"**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Az implicit folyamat használatával történő bejelentkezés teszteléséhez kattintson a <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize..</a> elemre. A bejelentkezést követően a böngészőt át kell irányítani `https://localhost/myapp/` egy `id_token` a címsorban.
>

| Paraméter |  | Leírás |
| --- | --- | --- |
| `tenant` | szükséges |A kérelem elérési útjának `{tenant}` értéke használható annak szabályozására, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett értékek: `common`, `organizations`, `consumers`és bérlői azonosítók. További részletek: [protokoll alapjai](active-directory-v2-protocols.md#endpoints). |
| `client_id` | szükséges | Az alkalmazáshoz hozzárendelt [Azure Portal-Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldal alkalmazás-(ügyfél-) azonosítója. |
| `response_type` | szükséges |Tartalmaznia kell `id_token` az OpenID Connect bejelentkezéshez. A response_type `token`is tartalmazhat. A `token` használata lehetővé teszi az alkalmazás számára, hogy azonnal kapjon hozzáférési tokent az engedélyezés végponttól anélkül, hogy egy második kérést kellene létesítenie az engedélyezési végpont számára. Ha a `token` response_type használja, a `scope` paraméternek tartalmaznia kell egy hatókört, amely jelzi, hogy melyik erőforrást kell kibocsátania a jogkivonat számára (például user. Read on Microsoft Graph).  |
| `redirect_uri` | ajánlott |Az alkalmazás redirect_uri, ahol az alkalmazás elküldhet és fogadhat hitelesítési válaszokat. Pontosan meg kell egyeznie a portálon regisztrált redirect_urisével, kivéve, ha az URL-címet kódolni kell. |
| `scope` | szükséges |A [hatókörök](v2-permissions-and-consent.md)szóközzel tagolt listája. Az OpenID Connect (id_tokens) esetében tartalmaznia kell a hatókört `openid`, amely a hozzájárulási felhasználói felületen a "Bejelentkezés" engedélyre van lefordítva. Opcionálisan érdemes lehet a `email` és `profile` hatóköröket is felvenni a további felhasználói információkhoz való hozzáféréshez. A kérelemben más hatókörök is szerepelhetnek, ha hozzáférési jogkivonatot kér a különböző erőforrásokhoz való hozzáféréshez. |
| `response_mode` | választható |Meghatározza azt a módszert, amelyet az eredményül kapott jogkivonat az alkalmazásba való visszaküldéséhez kell használni. Az alapértelmezett érték csak egy hozzáférési jogkivonat lekérdezése, de töredék, ha a kérelem tartalmaz egy id_token. |
| `state` | ajánlott |A kérelemben szereplő érték, amelyet a rendszer a jogkivonat-válaszban is visszaad. Bármely kívánt tartalom sztringje lehet. A véletlenszerűen generált egyedi érték általában a [helyek közötti kérelmek hamisításának megelőzésére](https://tools.ietf.org/html/rfc6749#section-10.12)szolgál. Az állapot az alkalmazásban a felhasználó állapotára vonatkozó információk kódolására is használatos, mielőtt a hitelesítési kérelem bekövetkezett volna, például az oldal vagy a megtekintés. |
| `nonce` | szükséges |Az alkalmazás által generált kérelemben szereplő érték, amelyet a rendszer az eredményül kapott id_token tartalmaz. Az alkalmazás ezután ellenőrizheti ezt az értéket a jogkivonat-Visszajátszási támadások enyhítése érdekében. Az érték általában egy véletlenszerű, egyedi karakterlánc, amely a kérelem forrásának azonosítására szolgál. Csak id_token kérelmezése esetén szükséges. |
| `prompt` | választható |Megadja a szükséges felhasználói beavatkozás típusát. Ebben az esetben az egyetlen érvényes érték a következők egyike: "bejelentkezhessen," None "," select_account "és" beleegyező ". `prompt=login` kényszeríti a felhasználót, hogy adja meg a kéréshez tartozó hitelesítő adatokat, és zárja be az egyszeri bejelentkezést. `prompt=none` az ellentétes – biztosítja, hogy a felhasználó semmilyen interaktív kérés nélkül sem jelenik meg. Ha a kérést nem lehet csendes úton végrehajtani az egyszeri bejelentkezésen keresztül, a Microsoft Identity platform-végpont hibát ad vissza. `prompt=select_account` elküldi a felhasználót egy olyan fiók-választónak, ahol a munkamenetben megjegyzett összes fiók megjelenik. a `prompt=consent` a felhasználó bejelentkezése után aktiválja a OAuth jóváhagyása párbeszédpanelt, amely arra kéri a felhasználót, hogy adjon engedélyt az alkalmazásnak. |
| `login_hint`  |választható |A felhasználó számára a bejelentkezési oldal Felhasználónév/e-mail cím mezőjének előzetes kitöltésére is használható, ha a felhasználónevet az idő előtt ismeri. Az alkalmazások gyakran ezt a paramétert fogják használni az ismételt hitelesítés során, miután az `preferred_username` jogcím használatával már kibontották a felhasználónevet egy korábbi bejelentkezésből.|
| `domain_hint` | választható |Ha belefoglalja ezt a funkciót, a rendszer kihagyja az e-mailes felderítési folyamatot, amelyet a felhasználó a bejelentkezési oldalon áthalad, ami valamivel egyszerűbb felhasználói élményt nyújt. Ezt gyakran használják olyan üzletági alkalmazások esetében, amelyek egyetlen bérlőn működnek, és amelyek tartománynevet biztosítanak egy adott bérlőn belül.  Ezzel továbbítja a felhasználót az adott bérlőhöz tartozó összevonási szolgáltatóhoz.  Vegye figyelembe, hogy ez megakadályozza, hogy a vendégek bejelentkezzenek ebbe az alkalmazásba.  |

Ekkor a rendszer megkéri a felhasználót, hogy adja meg a hitelesítő adatait, és fejezze be a hitelesítést. A Microsoft Identity platform végpontja azt is biztosítja, hogy a felhasználó beleegyezett a `scope` lekérdezési paraméterben megadott engedélyekkel. Ha a felhasználó beleegyezett a fenti engedélyek **egyikére sem** , a felhasználó beleegyezést kér a szükséges engedélyekkel. További információ: [engedélyek, beleegyezikés és több-bérlős alkalmazások](v2-permissions-and-consent.md).

Miután a felhasználó elvégezte a hitelesítést és beleegyezik, a Microsoft Identity platform végpontja a jelzett `redirect_uri`válaszát adja vissza az alkalmazásnak a `response_mode` paraméterben megadott módszer használatával.

#### <a name="successful-response"></a>Sikeres válasz

`response_mode=fragment` és `response_type=id_token+token` használatával történő sikeres válasz a következőhöz hasonlóan néz ki (az olvashatóság érdekében sortörésekkel):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Paraméter | Leírás |
| --- | --- |
| `access_token` |`response_type` tartalmaz, ha a `token`. Az alkalmazás által kért hozzáférési jogkivonat. A hozzáférési tokent nem lehet dekódolni vagy más módon megvizsgálni, átlátszatlan karakterláncként kell kezelni. |
| `token_type` |`response_type` tartalmaz, ha a `token`. Mindig `Bearer`. |
| `expires_in`|`response_type` tartalmaz, ha a `token`. Azt jelzi, hogy a jogkivonat hány másodpercig érvényes, gyorsítótárazási célokra. |
| `scope` |`response_type` tartalmaz, ha a `token`. Azokat a hatókör (eke) t jelöli, amelyekhez a access_token érvényes lesz. Előfordulhat, hogy nem tartalmazza az összes kért hatókört, ha nem voltak érvényesek a felhasználóra (csak az Azure AD-hatókörök esetében, amikor a rendszer személyes fiókot használ a bejelentkezéshez). |
| `id_token` | Aláírt JSON Web Token (JWT). Az alkalmazás képes dekódolni a token szegmenseit, hogy adatokat kérjen a bejelentkezett felhasználótól. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti őket, de nem támaszkodhat rájuk az engedélyezési vagy biztonsági határokra. További információ a id_tokensről: [`id_token reference`](id-tokens.md). <br> **Megjegyzés:** Csak akkor van megadva, ha `openid` hatókört kérték. |
| `state` |Ha a kérelemben szerepel egy State paraméter, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő állapot értékek azonosak-e. |

#### <a name="error-response"></a>Hiba válasza

A rendszer hibaüzeneteket is küldhet a `redirect_uri`, hogy az alkalmazás megfelelően tudja kezelni őket:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Paraméter | Leírás |
| --- | --- |
| `error` |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| `error_description` |Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Hozzáférési tokenek csendes beolvasása a háttérben

Most, hogy aláírta a felhasználót egy egyoldalas alkalmazásba, a Microsoft Identity platform által védett webes API-k meghívásához (például a [Microsoft Graph](https://developer.microsoft.com/graph)) csendes módon kaphat hozzáférési jogkivonatokat. Akkor is használhatja ezt a módszert, ha a `token` response_type használatával már kapott jogkivonatot a további erőforrásokhoz anélkül, hogy át kellene irányítani a felhasználót, hogy újra be kellene jelentkeznie.

A normál OpenID Connect/OAuth folyamat során ehhez egy kérést kell benyújtani a Microsoft Identity platform `/token` végpontjának. A Microsoft Identity platform végpontja azonban nem támogatja a CORS-kérelmeket, így az AJAX-hívások lekérése és frissítése a jogkivonatok alól nem áll fenn. Ehelyett a rejtett iframe-ben lévő implicit folyamattal új jogkivonatokat kérhet le más webes API-khoz: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Az URL-cím lekérdezési paramétereinek részletes ismertetését lásd: [a bejelentkezési kérelem küldése](#send-the-sign-in-request).

> [!TIP]
> Próbálja meg másolni & az alábbi kérelem beillesztése egy böngésző lapra! (Ne felejtse el lecserélni a `login_hint` értékeket a felhasználó helyes értékével)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

A `prompt=none` paraméternek köszönhetően ez a kérés sikeres vagy sikertelen lesz, és visszatér az alkalmazáshoz. A rendszer sikeres választ küld az alkalmazásnak a jelzett `redirect_uri`a `response_mode` paraméterben megadott metódus használatával.

#### <a name="successful-response"></a>Sikeres válasz

Egy sikeres válasz `response_mode=fragment` a következőképpen néz ki:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Paraméter | Leírás |
| --- | --- |
| `access_token` |`response_type` tartalmaz, ha a `token`. Az alkalmazás által kért hozzáférési jogkivonat, ebben az esetben a Microsoft Graph. A hozzáférési tokent nem lehet dekódolni vagy más módon megvizsgálni, átlátszatlan karakterláncként kell kezelni. |
| `token_type` | Mindig `Bearer`. |
| `expires_in` | Azt jelzi, hogy a jogkivonat hány másodpercig érvényes, gyorsítótárazási célokra. |
| `scope` | Azokat a hatókör (eke) t jelöli, amelyekhez a access_token érvényes lesz. Előfordulhat, hogy nem tartalmazza az összes kért hatókört, ha nem voltak érvényesek a felhasználóra (csak az Azure AD-hatókörök esetében, amikor a rendszer személyes fiókot használ a bejelentkezéshez). |
| `id_token` | Aláírt JSON Web Token (JWT). `response_type` tartalmaz, ha a `id_token`. Az alkalmazás képes dekódolni a token szegmenseit, hogy adatokat kérjen a bejelentkezett felhasználótól. Az alkalmazás gyorsítótárazhatja az értékeket, és megjelenítheti őket, de nem támaszkodhat rájuk az engedélyezési vagy biztonsági határokra. További információ a id_tokensről:`id_token`- [hivatkozás](id-tokens.md). <br> **Megjegyzés:** Csak akkor van megadva, ha `openid` hatókört kérték. |
| `state` |Ha a kérelemben szerepel egy State paraméter, akkor a válaszban ugyanazt az értéket kell megjelennie. Az alkalmazásnak ellenőriznie kell, hogy a kérelemben és a válaszban szereplő állapot értékek azonosak-e. |

#### <a name="error-response"></a>Hiba válasza

A rendszer hibaüzeneteket is küldhet a `redirect_uri`, hogy az alkalmazás megfelelően tudja kezelni őket. `prompt=none`esetén a várt hiba a következő lesz:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Paraméter | Leírás |
| --- | --- |
| `error` |Hibakód-karakterlánc, amely a felmerülő hibák típusának besorolására használható, és felhasználható a hibákra való reagálásra. |
| `error_description` |Egy adott hibaüzenet, amely segítséget nyújt a fejlesztőknek a hitelesítési hiba kiváltó okának azonosításában. |

Ha ezt a hibát az IFRAME-kérelemben kapja, a felhasználónak interaktív módon be kell jelentkeznie egy új jogkivonat lekéréséhez. Ezt az esetet úgy is eldöntheti, hogy bármilyen módon ésszerű legyen az alkalmazáshoz.

## <a name="refreshing-tokens"></a>Tokenek frissítése

Az implicit támogatás nem biztosít frissítési jogkivonatokat. A `id_token`s és a `access_token`s is rövid idő után lejár, így az alkalmazásnak fel kell készülnie, hogy rendszeresen frissítse ezeket a jogkivonatokat. A token típusának frissítéséhez ugyanezen rejtett iframe-kérelmet is végrehajthatja a `prompt=none` paraméterrel az Identity platform működésének vezérléséhez. Ha új `id_token`szeretne kapni, akkor ügyeljen arra, hogy a `response_type` és a `scope=openid``id_token`, valamint egy `nonce` paramétert használjon.

## <a name="send-a-sign-out-request"></a>Kijelentkezési kérelem küldése

Az OpenID Connect `end_session_endpoint` lehetővé teszi, hogy az alkalmazás küldjön egy kérést a Microsoft Identity platform végpontjának a felhasználó munkamenetének befejezéséhez és a Microsoft Identity platform végpontja által beállított cookie-k törléséhez. Ha egy felhasználót teljes mértékben szeretne aláírni egy webalkalmazásból, az alkalmazásnak saját munkamenetét kell végződnie a felhasználóval (általában a jogkivonat-gyorsítótár törlésével vagy a cookie-k eldobásával), majd át kell irányítani a böngészőt a következőre:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Paraméter |  | Leírás |
| --- | --- | --- |
| `tenant` |szükséges |A kérelem elérési útjának `{tenant}` értéke használható annak szabályozására, hogy ki jelentkezhet be az alkalmazásba. Az engedélyezett értékek: `common`, `organizations`, `consumers`és bérlői azonosítók. További részletek: [protokoll alapjai](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | ajánlott | A felhasználó által a kijelentkezés befejeződése után visszaadott URL-cím. Ennek az értéknek meg kell egyeznie az alkalmazáshoz regisztrált átirányítási URI-k egyikével. Ha nem szerepel, a felhasználó egy általános üzenetet fog megjeleníteni a Microsoft Identity platform végpontján. |

## <a name="next-steps"></a>Következő lépések

* Ugorjon a [MSAL js-mintákra](sample-v2-code.md) az első lépések kódolásához.
