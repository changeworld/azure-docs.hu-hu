---
title: UserJourneys | Microsoft Docs
description: A Azure Active Directory B2C egyéni házirendjének UserJourneys elemének megadásához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d73a1a3ce23817d9d6f742a4a8c730afb58ee0c8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227006"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A felhasználói utazások olyan explicit elérési utakat határoznak meg, amelyeken keresztül a szabályzat lehetővé teszi, hogy a függő entitások egy felhasználó számára megfelelő jogcímeket szerezzenek. A felhasználó ezen elérési utakon keresztül kéri le a függő entitásnak benyújtandó jogcímeket. Más szóval a felhasználói útvonalak határozzák meg azt az üzleti logikát, amit a végfelhasználó a Azure AD B2C identitás-kezelési keretrendszere feldolgozza a kérést.

Ezek a felhasználói utazások olyan sablonokként tekinthetők meg, amelyek kielégítik a Közösség különböző függő feleinek alapvető igényét. A felhasználói utazások megkönnyítik a függő entitás definíciójának meghatározását egy házirendben. Egy házirend több felhasználói utat is meghatározhat. Minden felhasználói út a előkészítési lépések egyik sora.

A szabályzat által támogatott felhasználói útvonalak definiálásához a rendszer egy **UserJourneys** elemet ad hozzá a házirendfájl legfelső szintű eleméhez.

A **UserJourneys** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| UserJourney | 1: n | Egy felhasználói út, amely meghatározza a teljes felhasználói folyamathoz szükséges összes szerkezetet. |

A **UserJourney** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | Egy felhasználói utazás azonosítója, amely a szabályzat más elemeinek hivatkozására használható. A [függő entitás házirendjének](relyingparty.md) **DefaultUserJourney** eleme erre az attribútumra mutat. |

A **UserJourney** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1: n | Egy olyan előkészítési sorozatot, amelyet követni kell egy sikeres tranzakcióhoz. Minden felhasználói út a sorrendben végrehajtott előkészítési lépések rendezett listáját tartalmazza. Ha bármelyik lépés meghiúsul, a tranzakció meghiúsul. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

A felhasználói út olyan összehangoló sorozatot jelöl, amelyet egy sikeres tranzakcióhoz kell követni. Ha bármelyik lépés meghiúsul, a tranzakció meghiúsul. Ezek a kialakítási lépések az építőelemeket és a házirend fájljában engedélyezett jogcím-szolgáltatókat egyaránt felmutatják. A felhasználói élmény megjelenítéséhez vagy megjelenítéséhez felelős bármely előkészítési lépés a megfelelő tartalom-definíciós azonosítóra is hivatkozik.

A szerelési lépések feltételesen hajthatók végre a előkészítési lépés elemében meghatározott előfeltételek alapján. Megtekintheti például, hogy csak akkor végezzen előkészítési lépést, ha egy adott jogcím létezik, vagy ha egy jogcím a megadott értékkel egyenlő vagy sem.

A rendezési lépések rendezett listájának megadásához egy **OrchestrationSteps** elem kerül a szabályzat részeként. Ezt az elemet kötelező megadni.

A **OrchestrationSteps** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1: n | Egy rendezett előkészítési lépés. |

A **OrchestrationStep** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| `Order` | Igen | A folyamat lépéseinek sorrendje. |
| `Type` | Igen | A előkészítési lépés típusa. Lehetséges értékek: <ul><li>**ClaimsProviderSelection** – azt jelzi, hogy a előkészítési lépés különböző jogcím-szolgáltatókat mutat be a felhasználónak az egyik kiválasztásához.</li><li>**CombinedSignInAndSignUp** – azt jelzi, hogy a koordináló lépés a Kombinált közösségi szolgáltató bejelentkezési és helyi fiókjának regisztrációs lapját mutatja be.</li><li>**ClaimsExchange** – azt jelzi, hogy a koordináló lépés jogcímeket cserél a jogcím-szolgáltatóval.</li><li>**GetClaims** – azt jelzi, hogy a hangszerelési lépés beolvassa a bemeneti jogcímeket.</li><li>**SendClaims** – azt jelzi, hogy a koordináló lépés elküldi a jogcímeket a függő entitásnak a jogcímek kiállítói által kiállított jogkivonattal.</li></ul> |
| ContentDefinitionReferenceId | Nem | Az ehhez a előkészítési lépéshez társított [tartalom-definíció](contentdefinitions.md) azonosítója. Általában a Content definition Reference azonosító az önérvényesített technikai profilban van definiálva. Vannak azonban olyan esetek, amikor Azure AD B2Cnek technikai profil nélküli valamit kell megjelenítenie. Kétféle példa van – ha az előkészítési lépés típusa a következők egyike: `ClaimsProviderSelection` vagy `CombinedSignInAndSignUp`, Azure AD B2C az identitás-szolgáltató kijelölését a technikai profil nélkül kell megjelenítenie. |
| CpimIssuerTechnicalProfileReferenceId | Nem | Az előkészítési lépés típusa `SendClaims`. Ez a tulajdonság határozza meg a függő entitáshoz tartozó jogkivonatot kibocsátó jogcím-szolgáltató technikai profiljának azonosítóját.  Ha hiányzik, a rendszer nem hoz létre függő entitás tokenjét. |


A **OrchestrationStep** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Előfeltételei | 0: n | Azon előfeltételek listája, amelyeknek teljesülniük kell a előkészítési lépés végrehajtásához. |
| ClaimsProviderSelections | 0: n | A létrehozási lépéshez tartozó jogcím-szolgáltatói beállítások listája. |
| ClaimsExchanges | 0: n | A létrehozási lépéshez tartozó jogcím-csere listája. |

### <a name="preconditions"></a>Előfeltételei

Az **Előfeltételek** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Előfeltétel | 1: n | A használt technikai profiltól függően vagy átirányítja az ügyfelet a jogcím-szolgáltató kiválasztása alapján, vagy kezdeményezi a kiszolgálótól az Exchange-jogcímek meghívását. |


#### <a name="precondition"></a>Előfeltétel

Az **előfeltétel** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| `Type` | Igen | Az előfeltételként végrehajtandó ellenőrzés vagy lekérdezés típusa. Az érték lehet **ClaimsExist**, amely megadja, hogy a rendszer végrehajtja a műveleteket, ha a megadott jogcímek a felhasználó aktuális jogcímek készletében vannak, vagy **ClaimEquals**, amely meghatározza, hogy a rendszer végrehajtja a műveleteket, ha a megadott jogcím létezik, és annak értéke megegyezik a megadott értékkel. |
| `ExecuteActionsIf` | Igen | Használjon igaz vagy hamis tesztet annak eldöntéséhez, hogy az előfeltételben szereplő műveleteket kell-e végrehajtani. |

Az **előfeltétel** elemek a következő elemeket tartalmazzák:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Érték | 1: n | Egy ClaimTypeReferenceId, amelyről le kell kérdezni. Egy másik érték elem tartalmazza az ellenőrizendő értéket.</li></ul>|
| Művelet | 1:1 | Az a művelet, amelyet akkor kell végrehajtani, ha az előfeltétel-ellenőrzés egy előkészítési lépésen belül igaz. Ha a `Action` értéke `SkipThisOrchestrationStep`, akkor a társított `OrchestrationStep` nem hajtható végre. |

#### <a name="preconditions-examples"></a>Példák az előfeltételekre

A következő előfeltételek ellenőrzik, hogy a felhasználó objectId létezik-e. A felhasználói úton a felhasználó helyi fiókkal jelentkezhet be. Ha a objectId létezik, ugorja át ezt a hangszerelési lépést.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

A következő előfeltételek ellenőrzik, hogy a felhasználó bejelentkezett-e egy közösségi fiókkal. Kísérlet történt a felhasználói fiók megkeresésére a címtárban. Ha a felhasználó bejelentkezik, vagy helyi fiókkal jelentkezik be, ugorja át ezt a hangelőkészítési lépést.

```XML
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Az előfeltételek több előfeltétel ellenőrzését is megadhatják. A következő példa ellenőrzi, hogy létezik-e "objectId" vagy "e-mail". Ha az első feltétel igaz, a rendszer kihagyja a következő előkészítési lépést.

```XML
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>ClaimsProviderSelection

`ClaimsProviderSelection` vagy `CombinedSignInAndSignUp` típusú előkészítési lépés tartalmazhatja azokat a jogcím-szolgáltatókat, amelyekkel a felhasználó be tud jelentkezni. A `ClaimsProviderSelections` elemek elemeinek sorrendje vezérli a felhasználónak megjelenő identitás-szolgáltatók sorrendjét.

A **ClaimsProviderSelections** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1: n | A kiválasztható jogcím-szolgáltatók listáját jeleníti meg.|

A **ClaimsProviderSelections** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| DisplayOption| Nem | Egy olyan eset viselkedését szabályozza, amelyben egyetlen jogcím-szolgáltató választható ki. Lehetséges értékek: `DoNotShowSingleProvider` (alapértelmezett), a rendszer azonnal átirányítja a felhasználót az összevont identitás-szolgáltatóhoz. Vagy `ShowSingleProvider` Azure AD B2C a bejelentkezési oldalt jeleníti meg az egyetlen identitás-szolgáltató kijelölésével. Ennek az attribútumnak a használatához a [tartalom-definíciós verziónak](page-layout.md) `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` és újabbnak kell lennie.|

A **ClaimsProviderSelection** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | Nem | A jogcím-szolgáltató kiválasztásának következő megszervezési lépésében végrehajtott jogcímbarát Exchange azonosítója. Ezt az attribútumot vagy a ValidationClaimsExchangeId attribútumot meg kell adni, de nem mindkettőt. |
| ValidationClaimsExchangeId | Nem | A jogcímek cseréjének azonosítója, amelyet a rendszer az aktuális előkészítési lépésben hajt végre a jogcím-szolgáltató kijelölésének ellenőrzéséhez. Ezt az attribútumot vagy a TargetClaimsExchangeId attribútumot meg kell adni, de nem mindkettőt. |

### <a name="claimsproviderselection-example"></a>ClaimsProviderSelection példa

A következő előkészítési lépésben a felhasználó dönthet úgy, hogy bejelentkezik a Facebook, a LinkedIn, a Twitter, a Google vagy egy helyi fiók használatával. Ha a felhasználó kiválasztja az egyik közösségi identitás-szolgáltatót, a második előkészítési lépés a `TargetClaimsExchangeId` attribútumban megadott kiválasztott jogcím-Exchange-re van végrehajtva. A második előkészítési lépés átirányítja a felhasználót a közösségi identitás-szolgáltatóhoz a bejelentkezési folyamat befejezéséhez. Ha a felhasználó úgy dönt, hogy bejelentkezik a helyi fiókkal, Azure AD B2C marad ugyanazon a előkészítési lépésben (ugyanazon a regisztrációs oldalon vagy bejelentkezési oldalon), és kihagyja a második előkészítési lépést.

```XML
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
    <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
    </ClaimsProviderSelections>
    <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange"
                    TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
    </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

A **ClaimsExchanges** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1: n | A használt technikai profiltól függően vagy átirányítja az ügyfelet a kiválasztott ClaimsProviderSelection megfelelően, vagy kezdeményezi a kiszolgálótól az Exchange-jogcímek meghívását. |

A **ClaimsExchange** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A jogcím Exchange-lépés azonosítója. Az azonosító a jogcím-szolgáltató jogcímek kiválasztási lépésében való hivatkozására szolgál a szabályzatban. |
| TechnicalProfileReferenceId | Igen | A végrehajtandó műszaki profil azonosítója. |
