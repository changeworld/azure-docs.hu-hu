---
title: Bejelentkezve maradok Azure Active Directory B2C
description: Megtudhatja, hogyan állíthatja be a bejelentkezett lépést (KMSI) a Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a27487fa69888b02883c3d9a2151887f41afc45
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189378"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>A bejelentkezett (KMSI) Azure Active Directory B2C használatának engedélyezése

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Engedélyezheti a bejelentkezett (KMSI) funkciót azon webes és natív alkalmazások felhasználói számára, akik helyi fiókkal rendelkeznek a Azure Active Directory B2C (Azure AD B2C) címtárban. Ez a szolgáltatás hozzáférést biztosít az alkalmazásnak visszaadott felhasználóknak anélkül, hogy újra meg kellene adnia felhasználónevét és jelszavát. A rendszer visszavonja ezt a hozzáférést, amikor egy felhasználó kijelentkezik.

A felhasználók nem engedélyezhetik ezt a lehetőséget a nyilvános számítógépeken.

![Példa a bejelentkezési bejelentkezési oldalára, amely egy bejelentkezve marad jelölőnégyzetet jelenít meg](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Előfeltételek

- Azure AD B2C bérlő, amely a helyi fiók bejelentkezésének engedélyezésére van konfigurálva. A KMSI nem támogatott külső identitás-szolgáltatói fiókok esetében.
- Hajtsa végre az [Ismerkedés az egyéni szabályzatokkal](custom-policy-get-started.md)című témakör lépéseit.

## <a name="configure-the-page-identifier"></a>Az oldal azonosítójának konfigurálása

A KMSI engedélyezéséhez állítsa a Content definition `DataUri` elemet az [oldal azonosítójának](contentdefinitions.md#datauri) `unifiedssp` és az [oldal Version](page-layout.md) *1.1.0* vagy újabb verzióra.

1. Nyissa meg a szabályzat fájlkiterjesztés-fájlját. Például <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>  . Ez a kiterjesztési fájl az egyéni házirend alapszintű csomagban található egyik házirend-fájl, amelyet az előfeltételben kell megszereznie az [Egyéni szabályzatok használatának első lépéseiben](custom-policy-get-started.md).
1. Keresse meg a **BuildingBlocks** elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a **ContentDefinitions** elemet a szabályzat **BuildingBlocks** eleméhez.

    Az egyéni szabályzatnak a következő kódrészlethez hasonlóan kell kinéznie:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Mentse a bővítmények fájlt.



## <a name="configure-a-relying-party-file"></a>Függő entitás fájljának konfigurálása

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Nyissa meg az egyéni házirend-fájlt. Például: *SignUpOrSignin. XML*.
1. Ha még nem létezik, adjon hozzá egy `<UserJourneyBehaviors>` gyermek csomópontot a `<RelyingParty>` csomóponthoz. `<DefaultUserJourney ReferenceId="User journey Id" />`után azonnal el kell helyezni, például: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`.
1. Adja hozzá a következő csomópontot a `<UserJourneyBehaviors>` elem gyermekének.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** – azt jelzi, hogyan hosszabbítja meg a munkamenetet a `SessionExpiryInSeconds` és `KeepAliveInDays`ban megadott idő szerint. A `Rolling` érték (alapértelmezett) azt jelzi, hogy a munkamenet minden alkalommal ki van-e bővítve, amikor a felhasználó végrehajtja a hitelesítést. A `Absolute` érték azt jelzi, hogy a felhasználónak a megadott időszak után újra hitelesítenie kell magát.

    - **SessionExpiryInSeconds** – a munkamenet-cookie-k élettartama, ha a *bejelentkezett* üzenet nem engedélyezett, vagy ha a felhasználó nem jelöli be a *Bejelentkezés megtartása*beállítást. A munkamenet lejár `SessionExpiryInSeconds` után, vagy a böngésző bezárult.

    - **KeepAliveInDays** – a munkamenet-cookie-k élettartama, ha be van kapcsolva a *bejelentkezett* üzenet, és a felhasználó kiválasztja a *bejelentkezett lépést*.  `KeepAliveInDays` értéke elsőbbséget élvez a `SessionExpiryInSeconds` értékkel szemben, és a munkamenet lejárati idejét diktálja. Ha a felhasználó bezárja a böngészőt, és később újra megnyitja, akkor továbbra is csendesen jelentkezhet be, amíg a KeepAliveInDays időszakon belül van.

    További információ: [felhasználói út viselkedése](relyingparty.md#userjourneybehaviors).

Azt javasoljuk, hogy a SessionExpiryInSeconds értékét rövid időtartamra (1200 másodpercre) állítsa be, míg a KeepAliveInDays értéke viszonylag hosszú (30 nap) lehet, ahogy az alábbi példában látható:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

4. Mentse a módosításokat, majd töltse fel a fájlt.
5. A feltöltött egyéni szabályzat teszteléséhez a Azure Portal lépjen a szabályzat lapra, majd válassza a **Futtatás most**lehetőséget.

[Itt](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)megtekintheti a minta szabályzatot.
