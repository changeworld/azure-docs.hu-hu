---
title: Gyakori kérdések (GYIK) – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Language Understanding (LUIS) kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: a2472064720af0a25568a2f173b971898b1f2e25
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219708"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Language Understanding gyakori kérdések (GYIK)

Ez a cikk a Language Understanding (LUIS) kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

## <a name="whats-new"></a>Újdonságok

[További](whats-new.md) információ a Language Understanding újdonságáról (Luis).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Tartalomkészítés

### <a name="what-are-the-luis-best-practices"></a>Mik azok a LUIS-ajánlott eljárások?
Kezdje a [szerzői ciklussal](luis-concept-app-iteration.md), majd olvassa el az [ajánlott eljárásokat](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Mi az a legjobb módja, és kezdje el létrehozni egy alkalmazást az intelligens HANGFELISMERÉSI?

Az alkalmazás kiépítésének legjobb módja egy [növekményes folyamat](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Mi az jó megoldás a modell a leképezések az alkalmazásom? Kell-e létre pontosabb vagy több általános leképezések?

Válassza ki, amelyek nem átfedésben lévő, de nem így meghatározott, hogy lehetővé teszi, hogy nehéz megkülönböztetni a hasonló leképezések LUIS kell tehát általános leképezések. Discriminative adott szándékot létrehozása az ajánlott eljárást a LUIS modellezési egyik.

### <a name="is-it-important-to-train-the-none-intent"></a>Fontos a nincs szándék betanításához?

Igen, érdemes betanítani a **nincs** szándékot több hosszúságú kimondott szöveg, mivel további címkéket ad hozzá más szándékokhoz. A megfelelő arány 1 vagy 2 címke, amelyet a rendszer az egyik szándékhoz hozzáadott 10 címkéhez **sem** ad hozzá. Ez az arány felgyorsíthatók a LUIS discriminative hatékonyságát.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Hogyan javíthatja a kimondott szöveg helyesírási hibákat?

Lásd a [Bing Spell Check API v7](luis-tutorial-bing-spellcheck.md) oktatóanyagot. A LUIS határoz meg a Bing Spell ellenőrzés API 7-es érvénybe lépteti.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Hogyan szerkeszthetem a LUIS-alkalmazásokon programozott módon?
Ha programozott módon szeretné szerkeszteni a LUIS alkalmazást, használja az [authoring API](https://go.microsoft.com/fwlink/?linkid=2092087)-t. A szerzői API meghívásával kapcsolatos példákért tekintse meg a [Luis authoring API meghívása](./get-started-get-model-rest-apis.md) és [a Luis-alkalmazás programozott módon történő létrehozásával](./luis-tutorial-node-import-utterances-csv.md) foglalkozó témakört. A szerzői API használatához a létrehozási [kulcsot](luis-concept-keys.md#azure-resources-for-luis) kell használnia a végponti kulcs helyett. Programozott szerzői lehetővé teszi, hogy legfeljebb 1 000 000 hívást, havi és öt tranzakció / másodperc. A LUIS használatával használt kulcsokról további információt a [kulcsok kezelése](./luis-concept-keys.md)című témakörben talál.

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Hol van a minta-szolgáltatás, amely a megadott reguláris kifejezéssel egyező?
Az előző **minta funkció** jelenleg elavult, **[mintázatok](luis-concept-patterns.md)** helyett.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Hogyan használhatom egy entitás is, a helyes adatokat?
Tekintse meg az [entitások](luis-concept-entity-types.md) és [az adatkiemelés](luis-concept-data-extraction.md)témakört.

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Tartalmaznia kell egy példa utterance (kifejezés) változata írásjelek?
Adja hozzá a különböző változatokat példaként hosszúságú kimondott szöveg a szándékhoz, vagy adja hozzá a példa kizáró mintázatát a [szintaxissal, hogy figyelmen kívül hagyja](luis-concept-patterns.md#pattern-syntax) a központozást.

### <a name="does-luis-currently-support-cortana"></a>A LUIS jelenleg támogatja a Cortana?

A Cortana előre elkészített alkalmazásokat is elavult 2017-ben. Már nem támogatottak.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Hogyan ruházhatom át tulajdonjogát a LUIS-alkalmazások?
LUIS-alkalmazásokon át egy másik Azure-előfizetést, a LUIS alkalmazás exportálása, és importálja egy új fiók használatával. Frissítse a LUIS alkalmazás azonosítója, amely meghívja ezt az ügyfélalkalmazásban. Az új alkalmazás adhat vissza eltérő LUIS pontszámokat az eredeti alkalmazásból.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Egy előre összeépített entitás egy példaként való kiírással van megjelölve az egyéni entitás helyett. Hogyan javítsa ezt? 

A LUIS-portálon megcímkézheti a pontos entitáshoz tartozó szöveget, amelyet szeretne kinyerni. Ha a LUIS-portál nem jeleníti meg a megfelelő entitások előrejelzését, előfordulhat, hogy további hosszúságú kimondott szöveg kell hozzáadnia, és fel kell címkéznie az entitást a szövegen belül, vagy hozzá kell adnia egy leírót (például egy funkciót). 

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Megpróbáltam importálni egy alkalmazást vagy verziót, de hiba történt? 

További információ a [verzió importálásával kapcsolatos hibákról](luis-how-to-manage-versions.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Együttműködés és közreműködés

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Hogyan az Azure Active Directory (Azure AD) vagy a szerepköralapú hozzáférés-vezérlés (RBAC) használatával biztosítson közreműködőket a LUIS számára?

A közreműködők hozzáférésének megismeréséhez tekintse meg [Azure Active Directory erőforrásokat](luis-how-to-collaborate.md#azure-active-directory-resources) és [Azure Active Directory bérlői felhasználót](luis-how-to-collaborate.md#azure-active-directory-tenant-user) . 

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Végpont

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>HTTP 403-es hiba-állapotkód érkezett. Hogyan javíthatom?

A 403-es és a 429-es hibakód akkor jelenik meg, ha az árképzési szinten a másodpercenkénti tranzakciók száma vagy a havi tranzakció. Növelje az árképzési szintet, vagy használjon Language Understanding [tárolókat](luis-container-howto.md).

Ha az összes ingyenes 1000-végpontot lekérdezi, vagy túllépi a díjszabási csomag havi tranzakciós kvótáját, a rendszer HTTP 403 hibakódot kap. 

Ennek a hibának a kijavításához [módosítania kell az árképzési szintet](luis-how-to-azure-subscription.md#change-pricing-tier) egy magasabb szintű csomagra, vagy [létre kell hoznia egy új erőforrást](get-started-portal-deploy-app.md#create-the-endpoint-resource) , és [hozzá kell rendelnie az alkalmazáshoz](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

A hiba megoldásai a következők:

* A [Azure Portal](https://portal.azure.com)a Language Understanding erőforráson az **Erőforrás-kezelés – > díjszabási**szinten módosítsa az árképzési szintet magasabb TPS szintjére. Ha az erőforrás már hozzá van rendelve a Language Understanding alkalmazáshoz, semmit nem kell tennie a Language Understanding portálon.
*  Ha a használat meghaladja a legmagasabb szintű díjszabást, vegyen fel további Language Understanding erőforrásokat egy terheléselosztó elé. A Kubernetes vagy Docker-összeállítással rendelkező [Language Understanding-tároló](luis-container-howto.md) segíthet ennek elvégzésében.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>HTTP 429-es hiba-állapotkód érkezett. Hogyan javíthatom?

A 403-es és a 429-es hibakód akkor jelenik meg, ha az árképzési szinten a másodpercenkénti tranzakciók száma vagy a havi tranzakció. Növelje az árképzési szintet, vagy használjon Language Understanding [tárolókat](luis-container-howto.md).

Ezt az állapotkódot akkor adja vissza a rendszer, ha a másodpercenkénti tranzakciók száma meghaladja a díjszabási szintet.  

A megoldások a következők:

* [Megnövelheti az árképzési szintet](luis-how-to-azure-subscription.md#change-pricing-tier), ha nem a legmagasabb szintű szinten van.
* Ha a használat meghaladja a legmagasabb szintű díjszabást, vegyen fel további Language Understanding erőforrásokat egy terheléselosztó elé. A Kubernetes vagy Docker-összeállítással rendelkező [Language Understanding-tároló](luis-container-howto.md) segíthet ennek elvégzésében.
* Az ügyfélalkalmazás kérelmeit megadhatja az [újrapróbálkozási szabályzattal](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) , amelyet Ön saját maga is végrehajthat, amikor megkapja ezt az állapotkódot. 

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Végpont a lekérdezés váratlan eredményt adott vissza. Mit tegyek?

Váratlan lekérdezési előrejelzési eredményeket a közzétett modell állapotának alapulnak. A modell kijavítani lehet, hogy módosítania kell a modellt, a betanítást és a közzétételt. 

A modell javítása az [aktív tanulással](luis-how-to-review-endpoint-utterances.md)kezdődik.

A nem determinisztikus-képzések eltávolításához frissítse az [Application Version Settings API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) -t az összes betanítási érték használatához.

További tippekért tekintse át az [ajánlott eljárásokat](luis-concept-best-practices.md) . 

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Miért nem LUIS szóközöket a lekérdezésbe felvenni kívánt körül vagy közepén szavak?
LUIS [tokenizes](luis-glossary.md#token) a [kultúrán](luis-language-support.md#tokenization)alapuló Kimondás. Az eredeti érték és a jogkivonat-érték is elérhető az [kinyeréshez](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Hogyan létrehozása és hozzárendelése egy LUIS végponti kulcs?
[Hozza létre a végponti kulcsot](luis-how-to-azure-subscription.md) az Azure-ban a [szolgáltatási](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) szinthez. [Rendelje hozzá a kulcsot](luis-how-to-azure-subscription.md) az **[Azure-erőforrások](luis-how-to-azure-subscription.md)** lapon. Nincs a művelet nem megfelelő API-t. Ezt követően módosítania kell a HTTP-kérést a végpontra [az új Endpoint kulcs használatához](luis-concept-keys.md).

### <a name="how-do-i-interpret-luis-scores"></a>Mi a LUIS pontszámok?
A rendszer függetlenül annak értéke a legmagasabb pontozási leképezést kell használnia. Ha például 0.5-ös (kevesebb mint 50 %) alatti nem feltétlenül jelenti, hogy a LUIS alacsony megbízhatósági rendelkezik. A további betanítási [adatmennyiséggel növelheti a](luis-concept-prediction-score.md) legvalószínűbb szándékot.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Miért nem látom, hogy a végpont a találatok saját alkalmazás-irányítópult?
Az alkalmazás-irányítópult a teljes végpont a találatok rendszeres időközönként frissülnek, de a metrikák az Azure Portalon, a LUIS végponti kulcs társított gyakran frissülnek.

Ha nem látja a végpontok frissített találatait az irányítópulton, jelentkezzen be a Azure Portalba, és keresse meg a LUIS Endpoint kulcshoz társított erőforrást, és nyissa meg a **metrikákat** a **hívások teljes** metrikájának kiválasztásához. A végpont kulcs egynél több LUIS alkalmazás használata esetén az Azure Portalon a metrika azt használó összes LUIS-alkalmazások hívásait összesített számát jeleníti meg.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Van egy PowerShell-parancs a végponti kvóta eléréséhez?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A végponti kvóta megtekintéséhez használhatja a PowerShell-parancsot:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
``` 

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>A LUIS-alkalmazásokon tegnap dolgozott, de még ma érkeznek meg hozzám a 403-as hibák. Az alkalmazás nem módosítható. Hogyan javíthatom?
Az alábbi [útmutatást](#how-do-i-create-and-assign-a-luis-endpoint-key) követve hozzon létre egy Luis-végponti kulcsot, és rendelje hozzá az alkalmazáshoz. Ezután az [új Endpoint kulcs használatához](luis-concept-keys.md)módosítania kell az ügyfélalkalmazás http-kérelmét a végpontra. Ha egy másik régióban hozott létre egy új erőforrást, módosítsa a HTTP-ügyfél kérelmének régióját is.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Hogyan védhetem meg a LUIS végpontomhoz?
Lásd: [a végpont biztonságossá tétele](luis-concept-keys.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>A LUIS korlátokon belül működik

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Mi az a szándékok és entitások, LUIS-alkalmazások által támogatott maximális számát?
Lásd a [határok](luis-boundaries.md) referenciát.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Szeretnék a LUIS alkalmazás felépítése a több mint leképezések maximális számát. Mit tegyek?

Lásd: [ajánlott eljárások a szándékokhoz](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Szeretnék az entitások maximális száma meghaladja a LUIS-alkalmazás létrehozása. Mit tegyek?

Lásd: [ajánlott eljárások az entitásokhoz](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Mik azok a számát és méretét, a kifejezés korlátait felsorolja?
A [kifejezések listájának](./luis-concept-feature.md)maximális hosszát lásd: a [határok](luis-boundaries.md) hivatkozása.

### <a name="what-are-the-limits-on-example-utterances"></a>Mik azok a példa utterances korlátait?
Lásd a [határok](luis-boundaries.md) referenciát.

## <a name="testing-and-training"></a>Tesztelés és képzés

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>A köteg bizonyos, a modellek az alkalmazásom panel tesztelése hibák láthatók. Hogyan kezelheti a probléma?

A hibákat jelzik, hogy van néhány eltérés van, a címkék és a modellek által létrehozott javaslatok között. A probléma megoldása érdekében hajthatja végre a következő feladatok közül:
* A LUIS közötti leképezések megkülönböztetés javítása érdekében adjon hozzá további címkéket.
* Ismerje meg, gyorsabb LUIS érdekében adja hozzá a kifejezéslista funkciók, amelyek bemutatják a tartomány-specifikus szókincsből eredőket.

Lásd a [Batch-tesztelési](luis-tutorial-batch-testing.md) oktatóanyagot.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Amikor az alkalmazás van exportálva, majd újra be egy új alkalmazást (egy új alkalmazás azonosítója), a LUIS-előrejelzési eredmények eltérőek. Miért jelentkezik?

Tekintse meg [az azonos alkalmazás példányai között megjelenő előrejelzési különbségeket](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Néhány utterances nyissa meg a nem megfelelő leképezés után az alkalmazás végrehajtott módosításokat. A probléma úgy tűnik, hogy véletlenszerűen eltűnnek. Hogyan javíthatom? 

Tekintse meg [a vonatot az összes adattal](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Alkalmazások közzététele

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Mi az a bérlő Azonosítóját, a "Hozzáadás egy billentyűt az alkalmazás a" ablakban?
Az Azure-ban a bérlő az ügyfél vagy a szervezet, amely a társított szolgáltatás jelöl. A **címtár-azonosító** mezőben keresse meg a bérlő azonosítóját a Azure Portal **Azure Active Directory** >  > **tulajdonságainak** **kezelése** lehetőség kiválasztásával.

![Az Azure Portalon a bérlő azonosítója](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Miért vannak-e további végpont kulcsok kiosztott, mint az alkalmazás hozzárendelve?
Minden LUIS alkalmazás a szerzői műveletek/alapszintű kulcs rendelkezik a végpont listájában a kényelem. Ezt a kulcsot csak néhány végpont találatok lehetővé teszi, így kipróbálhatja a LUIS.  

Ha az alkalmazás korábban létezett, előtt a LUIS általánosan elérhető (GA), automatikusan hozzárendelve LUIS végpont kulcsok az előfizetésében. Ez megtörtént, a végleges verzió áttelepítés egyszerűbbé. A Azure Portalban található új LUIS Endpoint kulcsok _nem_ lesznek automatikusan kiosztva a Luis-hoz.

## <a name="key-management"></a>Kulcskezelés

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Hogyan tudja, milyen kulcsra van szükségem, hol kapok, és mit csinálok? 

További információ a szerzői műveletek és az előrejelzési futtatókörnyezet kulcsa közötti különbségekről: [szerzői és lekérdezési előrejelzési végpont kulcsai a Luis-ben](luis-concept-keys.md) . 

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Hiba történt a kvóta lejártakor. Hogyan javíthatom? 

További információért lásd: a [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) -es és a [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) -es http-állapotkód javítása.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>További végponti lekérdezéseket kell kezelnie. Hogyan csinálni? 

További információért lásd: a [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) -es és a [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) -es http-állapotkód javítása.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Létrehoztam egy szerzői kulcsot, de nem jelenik meg a LUIS-portálon. Mi történt?

A szerzői kulcsok a [szerzői műveletek elvégzése](luis-migration-authoring.md)után érhetők el a Luis-portálon.  

## <a name="app-management"></a>Alkalmazáskezelés

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Hogyan töltse le a felhasználó utterances naplózása?
Alapértelmezés szerint a LUIS-alkalmazás a felhasználók naplózza a kimondott szöveg. A felhasználók által a LUIS-alkalmazásba küldött hosszúságú kimondott szöveg letöltéséhez lépjen a **saját alkalmazások**elemre, és válassza ki az alkalmazást. A környezetfüggő eszköztáron válassza a **végponti naplók exportálása**lehetőséget. A napló formátuma vesszővel tagolt (CSV) fájlként.

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Hogyan tilthatom az utterances naplózását?
A felhasználói hosszúságú kimondott szöveg naplózását kikapcsolhatja az ügyfélalkalmazás által a LUIS lekérdezéséhez használt végponti URL-cím `log=false` beállításával. A naplózás kikapcsolása azonban letiltja a LUIS-alkalmazás hosszúságú kimondott szöveg, vagy javíthatja az [aktív tanuláson](luis-concept-review-endpoint-utterances.md#what-is-active-learning)alapuló teljesítményt. Ha az adatvédelemre vonatkozó probléma miatt `log=false` beállítani, akkor nem töltheti le a felhasználói hosszúságú kimondott szöveg származó adatokat a LUIS-ból, vagy az alkalmazás fejlesztéséhez használja ezeket a hosszúságú kimondott szöveg.

A naplózás nem utterances csak tárolására.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Miért nem szeretné az összes naplózott saját végpont utterances?
Ha a napló előrejelzési elemzésre használ, ne rögzítsen rajta teszt utterances a naplóban tárolt.

## <a name="data-management"></a>Adatkezelés

### <a name="can-i-delete-data-from-luis"></a>Törölhetem-e adatokat a LUIS?

* A LUIS oktatási használt példa kimondott szöveg mindig törölheti. Ha töröl egy példa utterance (kifejezés) a LUIS-alkalmazás, törlődik a LUIS webszolgáltatás és az exportálás nem érhető el.
* A hosszúságú kimondott szöveg törölheti a felhasználói hosszúságú kimondott szöveg listájáról, amelyet a LUIS az **Endpoint hosszúságú kimondott szöveg áttekintése** lapon javasol. Beszédmódok törlése a listáról a továbbiakban nem javasolt, de nem törli azokat a naplókat.
* Ha töröl egy fiókot, az összes alkalmazás törlődnek, azokhoz példa kimondott szöveg és a naplókat. Az adatok végleges törlés előtt 60 napig őrződnek a kiszolgálókon.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Hogyan kezeli a Microsoft LUIS küldött adatokat?

Az adatvédelmi [központ](https://www.microsoft.com/trustcenter) ismerteti a kötelezettségvállalásokat, valamint az adatkezelési és-hozzáférési lehetőségeket az Azure-szolgáltatásokban.

## <a name="language-and-translation-support"></a>Nyelvi és fordítási támogatás

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Szükségem van egy alkalmazása egy nyelven, és hozzon létre egy párhuzamos alkalmazást egy másik nyelven szeretné. Mi az a legegyszerűbb módja, ehhez?
1. Az alkalmazás exportálása.
2. Az exportált alkalmazásnak, hogy a célként megadott nyelv a JSON-fájlban címkézett megcímkézzen fordítja le.
3. Szüksége lehet a nevet a szándékok és entitások, vagy hagyja őket, mivel ezek.
4. Végül importálja a LUIS-alkalmazásokon rendelkezik a célként megadott nyelven az alkalmazást.

## <a name="app-notification"></a>Alkalmazásban megjelenő értesítésre

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Miért jelenik meg egy e-mail arról tájékoztatja, szinte kvótájából vagyok?
A szerzői műveletek/alapszintű kulcs csak akkor engedélyezett, 1000 végpont havonta kérdezi le. Hozzon létre egy LUIS végponti kulcs (ingyenes vagy fizetős) és a kulcs végpont lekérdezések létrehozásakor. Ha egy robot vagy egy másik ügyfélalkalmazás végpont lekérdezések végez, a LUIS végponti kulcs van módosítani szeretné.

## <a name="bots"></a>Robotok

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>A LUIS-bot nem működik. Mit tegyek?

Az első probléma az, hogy elkülöníti a problémát, ha a probléma LUIS-hez kapcsolódik, vagy a LUIS middleware-n kívül történik. 

#### <a name="resolve-issue-in-luis"></a>Probléma megoldása a LUIS-ben
A Luis- [végponttal](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)azonos Kimondás a Luis-nek. Ha hibaüzenetet kap, oldja meg a problémát a LUIS-ben, amíg a hibát már nem adja vissza. Gyakori hibák a következők:

* `Out of call volume quota. Quota will be replenished in <time>.` – ez a probléma azt jelzi, hogy egy szerzői kulcsból egy [végponti kulcsra](luis-how-to-azure-subscription.md) kell váltania, vagy módosítania kell a [szolgáltatási szinteket](luis-how-to-azure-subscription.md#change-pricing-tier). 

#### <a name="resolve-issue-in-azure-bot-service"></a>Probléma megoldása Azure Bot Service

Ha a Azure Bot Service használja, és a probléma az, hogy a **webes csevegésben a teszt** visszaadja a `Sorry, my bot code is having an issue`, ellenőrizze a naplókat:

1. A robot Azure Portal a robot **kezelése** szakaszban válassza a **Létrehozás**lehetőséget.
1. Nyissa meg az online Kódszerkesztő alkalmazást. 
1. A felső, kék navigációs sávban válassza ki a robot nevét (a második elem jobbra).
1. Az eredményül kapott legördülő listában válassza a **kudu-konzol megnyitása**lehetőséget.
1. Válassza ki a **LogFiles**elemet, majd válassza az **alkalmazás**lehetőséget. Tekintse át az összes naplófájlt. Ha nem látja a hibát az alkalmazás mappájában, tekintse át az összes naplófájlt a **LogFiles (naplófájlok**) területen. 
1. Ne felejtse el újraépíteni a projektet, ha lefordított nyelvet használ C#, például:.

> [!Tip] 
> A konzol emellett csomagokat is telepíthet. 

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Probléma megoldása a helyi gépen a bot Framework-mel való hibakeresés során. 

Ha többet szeretne megtudni a robot helyi hibakereséséről, olvassa el [a robot hibakeresése](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0)című témakört.

## <a name="integrating-luis"></a>A LUIS integrálása

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Ahol létrejött a LUIS-alkalmazásom az Azure web app bot előfizetés során?
Ha kijelöl egy LUIS-sablont, majd kiválasztja a **kiválasztás** gombot a sablon ablaktáblán, a bal oldali ablaktábla a sablon típusát is megváltoztatja, és megkérdezi, hogy milyen régióban hozza létre a Luis-sablont. A web app bot folyamat LUIS előfizetés azonban nem létrehozása.

![A LUIS sablon web app bot régió](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Milyen a LUIS-régiók támogatják a Bot Framework speech betanítási művelet?
A [beszédfelismerési](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) alapszolgáltatások csak a Central (US) példányban található Luis-alkalmazásokhoz támogatottak.

## <a name="api-programming-strategies"></a>API programozási stratégiák

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Hogyan programozott módon beszerezhet egy erőforrás LUIS régióját? 

A LUIS-minta használatával programozott módon [keresheti meg a régiót](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) C# vagy a Node. js-t. 

## <a name="luis-service"></a>LUIS szolgáltatás

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Language Understanding (LUIS) érhető el a helyszínen vagy a privát felhőben?

Igen, használhatja a LUIS- [tárolót](luis-container-howto.md) ezekben a forgatókönyvekben, ha rendelkezik a szükséges kapcsolattal a méréshez. 

## <a name="migrating-to-the-next-version"></a>Migrálás a következő verzióra

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Hogyan Migrálás az előnézet V3 API-ra? 

Lásd: [API v2 – v3 áttelepítési útmutató Luis-alkalmazásokhoz](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Build 2019 konferencia hirdetményei

A Build 2019 konferencián a következő funkciók jelentek meg:

* [A V3 API áttelepítési útmutatójának előzetes verziója](luis-migration-api-v3.md)
* [Továbbfejlesztett elemzési irányítópult](luis-how-to-use-dashboard.md)
* [Továbbfejlesztett előre összeépített tartományok](luis-reference-prebuilt-domains.md) 
* [Dinamikus lista entitásai](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Külső entitások](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Videók:

* [Az Azure társalgási AI használata az üzlet méretezésére a következő generáció számára](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Következő lépések

A LUIS kapcsolatos további információkért lásd a következőket:
* [A LUIS-mel kapcsolatos kérdések Stack Overflow](https://stackoverflow.com/questions/tagged/luis)
* [MSDN Language Understanding intelligens szolgáltatások (LUIS) Fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
