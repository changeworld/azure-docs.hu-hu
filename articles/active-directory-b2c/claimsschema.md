---
title: ClaimsSchema – Azure Active Directory B2C | Microsoft Docs
description: A Azure Active Directory B2C egyéni házirendjének ClaimsSchema elemének megadásához.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4c3b3318e941723ec333597c7e4b3e48710152d1
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78397797"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A **ClaimsSchema** elem határozza meg azokat a jogcímeket, amelyeket a szabályzat részeként lehet hivatkozni. A jogcím-séma az a hely, ahol deklarálja a jogcímeket. A jogcím lehet Utónév, vezetéknév, megjelenítendő név, telefonszám és más. A ClaimsSchema elem a **claimType** elemek listáját tartalmazza. A **claimType** elem tartalmazza az **ID** attribútumot, amely a jogcím neve.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>ClaimType

A **claimType** elem a következő attribútumot tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Azonosító | Igen | A jogcím típusához használt azonosító. Más elemek is használhatják ezt az azonosítót a szabályzatban. |

A **claimType** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | A különböző képernyőkön lévő felhasználók számára megjelenő cím. Az érték [honosítható](localization.md). |
| Adattípus | 1:1 | A jogcím típusa. |
| DefaultPartnerClaimTypes | 0:1 | A partner alapértelmezett jogcím-típusai, amelyeket egy adott protokollhoz kell használni. Az érték felülírható a **InputClaim** vagy a **OutputClaim** elemekben megadott **PartnerClaimType** . Ezzel az elemmel adhatja meg egy protokoll alapértelmezett nevét.  |
| Maszk | 0:1 | A jogcímek megjelenítésekor alkalmazható maszkolási karakterek nem kötelező karakterlánca. Például a 324-232-4343-as telefonszám a XXX-XXX-4343 lehet. |
| UserHelpText | 0:1 | A jogcím típusának leírása, amely hasznos lehet a felhasználók számára, hogy megértsék a célját. Az érték [honosítható](localization.md). |
| UserInputType | 0:1 | A felhasználó számára elérhető bemeneti vezérlő típusa, amikor manuálisan adja meg a jogcím-adatokat a jogcím típusához. Tekintse meg az ezen a lapon később definiált felhasználói beviteli típusokat. |
| AdminHelpText | 0:1 | A jogcím típusának leírása, amely hasznos lehet a rendszergazdák számára a cél megértéséhez. |
| Korlátozás | 0:1 | A jogcím korlátozásai, például reguláris kifejezés (regex) vagy elfogadható értékek listája. Az érték [honosítható](localization.md). |
PredicateValidationReference| 0:1 | Egy **PredicateValidationsInput** elemre mutató hivatkozás. A **PredicateValidationReference** elemek lehetővé teszik egy ellenőrzési folyamat elvégzését annak érdekében, hogy csak a megfelelően formázott adatok legyenek megadva. További információ: [predikátumok](predicates.md). |



### <a name="datatype"></a>Adattípus

Az **adattípus** elem a következő értékeket támogatja:

| Típus | Leírás |
| ------- | ----------- |
|logikai|A logikai (`true` vagy `false`) értéket jelöli.|
|dátum| Egy azonnali időpontot jelöl, amely általában nap dátumként van kifejezve. A dátum értéke az ISO 8601 konvenciót követi.|
|Dátum és idő|Egy azonnali időpontot jelöl, amely általában dátum és napszak szerint van megadva. A dátum értéke az ISO 8601 konvenciót követi.|
|duration|Az év, hónap, nap, óra, perc és másodperc időtartamot jelöli. A formátuma `PnYnMnDTnHnMnS`, ahol a `P` pozitív vagy `N` negatív értéket jelez. `nY` az évek száma, amelyet egy literál `Y`követ. `nMo` az a hónapok száma, amelyet egy literál `Mo`követ. `nD` az a napok száma, amelyet egy literál `D`követ. Példák: a `P21Y` 21 évet jelöl. `P1Y2Mo` egy évet és két hónapot jelöl. `P1Y2Mo5D` egy évet, két hónapot és öt napot jelöl.  `P1Y2M5DT8H5M620S` egy évet, két hónapot, öt napot, nyolc órát, öt percet és húsz másodpercet jelöl.  |
|Telefonszám|A telefonszámot jelöli. |
|int| A-2 147 483 648 és a 2 147 483 647 közötti számot jelöli|
|hosszú| A-9223372036854775808 és a 9 223 372 036 854 775 807 közötti számot jelöli |
|sztring| UTF-16 kód egységként jeleníti meg a szöveget.|
|StringCollection stb|`string`gyűjteményét jelöli.|
|userIdentity| Felhasználói identitást jelöl.|
|userIdentityCollection|`userIdentity`gyűjteményét jelöli.|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

A **DefaultPartnerClaimTypes** a következő elemet tartalmazhatja:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Protokoll | 1: n | A protokollok listája az alapértelmezett partneri jogcím típusának nevével. |

A **protokoll** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Name (Név) | Igen | A Azure AD B2C által támogatott érvényes protokoll neve. A lehetséges értékek a következők: OAuth1, OAuth2, egy SAML2, OpenIdConnect. |
| PartnerClaimType | Igen | A használni kívánt jogcím-típus neve. |

A következő példában, amikor az identitás-keretrendszer egy egy SAML2-identitás szolgáltatóval vagy függő entitással működik együtt, a **vezetéknév** `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`ra van leképezve, és a OpenIdConnect és a OAuth2 a jogcímek `family_name`re vannak leképezve.

```XML
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

Ennek eredményeképpen a Azure AD B2C által kiállított JWT-jogkivonat **a nevet a**claimType neve helyett a `family_name` bocsátja ki.

```JSON
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Maszk

A **maszk** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| `Type` | Igen | A jogcím maszkjának típusa Lehetséges értékek: `Simple` vagy `Regex`. A `Simple` érték azt jelzi, hogy egy egyszerű szöveges maszkot alkalmaz a rendszer egy karakterlánc-jogcím vezető részére. A `Regex` érték azt jelzi, hogy egy reguláris kifejezés lesz alkalmazva a karakterlánc-jogcímek egészére.  Ha meg van adva a `Regex` értéke, egy opcionális attribútumot is meg kell adni a használni kívánt reguláris kifejezéssel. |
| `Regex` | Nem | Ha **`Type`** `Regex`re van beállítva, adja meg a használni kívánt reguláris kifejezést.

A következő példa egy **telefonszám** jogcímet konfigurál a `Simple` maszkkal:

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Az Identity Experience Framework megjeleníti a telefonszámot az első hat számjegy elrejtésével:

![A böngészőben az XS által maszkolt első hat számjegytel megjelenített telefonszám-jogcím](./media/claimsschema/mask.png)

A következő példa egy **AlternateEmail** jogcímet konfigurál a `Regex` maszkkal:

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Az Identity Experience Framework csak az e-mail-cím és az e-mail tartománynév első betűjét jeleníti meg:

![A böngészőben megjelenített, csillagokkal maszkolt karaktereket tartalmazó e-mail-jogcím](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Korlátozás

A **korlátozási** elem a következő attribútumot is tartalmazhatja:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| MergeBehavior | Nem | Az enumerálási értékek ClaimType való egyesítésére szolgáló metódus ugyanazzal az azonosítóval rendelkező szülő házirendben. Ezt az attribútumot akkor használja, ha felülírja az alapházirendben megadott jogcímet. Lehetséges értékek: `Append`, `Prepend`vagy `ReplaceAll`. A `Append` érték olyan adatgyűjtemény, amelyet a fölérendelt házirendben megadott gyűjtemény végéhez kell hozzáfűzni. A `Prepend` érték olyan adatgyűjtemény, amelyet hozzá kell adni a szülő házirendben megadott gyűjtemény előtt. A `ReplaceAll` érték a szülő házirendben megadott adatgyűjtemény, amelyet figyelmen kívül kell hagyni. |

A **korlátozási** elem a következő elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Enumerálás | 1: n | A felhasználó felhasználói felületének elérhető beállításai, amelyek kiválaszthatják a jogcímek, például a legördülő lista értékét. |
| Mintázat | 1:1 | A használandó reguláris kifejezés. |

#### <a name="enumeration"></a>Enumerálás

A **számbavételi** elem a felhasználó számára elérhető beállításokat definiálja a felhasználói felületen lévő jogcímek kiválasztásához, például egy `CheckboxMultiSelect`, `DropdownSingleSelect`vagy `RadioSingleSelect`értékének megadásához. Azt is megteheti, hogy megadhatja és honosíthatja az elérhető beállításokat a [LocalizedCollections](localization.md#localizedcollections) elemmel. Ha meg szeretne keresni egy elemet egy jogcím- **enumerálási** gyűjteményből, használja a [GetMappedValueFromLocalizedCollection](string-transformations.md#getmappedvaluefromlocalizedcollection) jogcím-átalakítást.

A **számbavételi** elem a következő attribútumokat tartalmazza:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| Szöveg | Igen | Az ehhez a beállításhoz tartozó felhasználói felületen megjelenített megjelenítési karakterlánc. |
|Érték | Igen | A beállítás kiválasztásához társított jogcím értéke. |
| SelectByDefault | Nem | Azt jelzi, hogy ez a beállítás alapértelmezés szerint ki van-e választva a felhasználói felületen. Lehetséges értékek: true vagy FALSE. |

Az alábbi **példa egy legördülő lista** jogcímet konfigurál egy alapértelmezett értékkel `New York`:

```XML
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

A legördülő lista alapértelmezett értéke New York:

![A böngészőben megjelenített legördülő menü és az alapértelmezett érték megjelenítése](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Mintázat

A **minta** elem a következő attribútumokat tartalmazhatja:

| Attribútum | Kötelező | Leírás |
| --------- | -------- | ----------- |
| RegularExpression | Igen | Ahhoz, hogy az ilyen típusú jogcímek érvényesek legyenek, a reguláris kifejezésnek egyeznie kell. |
| HelpText | Nem | Hibaüzenet a felhasználók számára, ha a reguláris kifejezés-ellenőrzés sikertelen. |

Az alábbi példa egy **e-mail-** jogcímet konfigurál a reguláris kifejezéses beviteli ellenőrzéssel és a Súgó szöveggel:

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
    </Restriction>
 </ClaimType>
```

Az Identity Experience Framework az e-mail-cím jogcímet a bemeneti ellenőrzés e-mail-formátumával jeleníti meg:

![A regex-korlátozás által aktivált hibaüzenetet megjelenítő szövegmező](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

A Azure AD B2C számos felhasználói beviteli típust támogat, például a szövegmezőt, a jelszót és a legördülő listát, amely akkor használható, ha a jogcím-adatok manuális bevitele a jogcím típusához történik. A **UserInputType** akkor kell megadnia, amikor adatokat gyűjt a felhasználótól egy [önérvényesített technikai profil](self-asserted-technical-profile.md) és [megjelenítési vezérlő](display-controls.md)használatával.

A **UserInputType** elem elérhető felhasználói bemeneti típusok:

| UserInputType | Támogatott ClaimType | Leírás |
| --------- | -------- | ----------- |
|CheckboxMultiSelect| `string` |Többszörös kijelölés legördülő lista A jogcím értéke a kijelölt értékek vesszővel elválasztó karakterláncában jelenik meg. |
|DateTimeDropdown | `date`, `dateTime` |Legördülő menüből kiválaszthatja a napot, a hónapot és az évet. |
|DropdownSingleSelect |`string` |Egyetlen Select legördülő lista. A jogcím értéke a kijelölt érték.|
|EmailBox | `string` |E-mail-beviteli mező. |
|Bekezdés | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|Egy olyan mező, amely csak szöveget jelenít meg egy bekezdés címkéjében. |
|Jelszó | `string` |Jelszó szövegmezője|
|RadioSingleSelect |`string` | Választógombok gyűjteménye A jogcím értéke a kijelölt érték.|
|ReadOnly | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| Írásvédett szövegmező. |
|TextBox |`boolean`, `int`, `string` |Egy egysoros szövegmező. |


#### <a name="textbox"></a>TextBox

A **szövegmező** felhasználói beviteli típus egy egysoros szövegmező megadására szolgál.

![A jogcím típusa mezőben megadott tulajdonságokat ábrázoló szövegmező](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>EmailBox

Az **EmailBox** felhasználói bevitel típusa egy alapszintű e-mail-beviteli mező biztosítására szolgál.

![A jogcím típusa mezőben megadott tulajdonságokat mutató EmailBox](./media/claimsschema/emailbox.png)

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

#### <a name="password"></a>Jelszó

A **jelszó** felhasználói beviteli típusa a felhasználó által megadott jelszó rögzítésére szolgál.

![Jogcím típusának használata jelszóval](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DateTimeDropdown

A **DateTimeDropdown** felhasználói bevitel típusa legördülő listát biztosít egy nap, hónap és év kiválasztásához. A predikátumok és a PredicateValidations elemek segítségével szabályozhatja a minimális és a maximális dátumérték értékét. További információt a [predikátumok és a PredicateValidations](predicates.md) **konfigurálása** című témakörben talál.

![Jogcím típusának használata a datetimedropdown](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

A **RadioSingleSelect** felhasználói beviteli típus a választógombok egy gyűjteményének megadására szolgál, amely lehetővé teszi, hogy a felhasználó kiválasszon egy lehetőséget.

![Jogcím típusának használata a radiodsingleselect](./media/claimsschema/radiosingleselect.png)

```XML
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

A **DropdownSingleSelect** felhasználói bevitel típusa legördülő lista megadására szolgál, amely lehetővé teszi, hogy a felhasználó kiválasszon egy lehetőséget.

![Jogcím típusának használata a dropdownsingleselect](./media/claimsschema/dropdownsingleselect.png)

```XML
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

A **CheckboxMultiSelect** felhasználói beviteli típusa jelölőnégyzetek gyűjteményének megadására szolgál, amely lehetővé teszi a felhasználó számára több lehetőség kiválasztását.

![Jogcím típusának használata a checkboxmultiselect](./media/claimsschema/checkboxmultiselect.png)

```XML
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="readonly"></a>ReadOnly

A **readonly** felhasználói bevitel típusa írásvédett mező biztosítására szolgál a jogcím és az érték megjelenítéséhez.

![Jogcím típusának használata ReadOnly használatával](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Bekezdés

A **bekezdés** felhasználói beviteli típusa olyan mező megadására szolgál, amely csak egy bekezdés címkéjén jelenít meg szöveget.  Például &lt;p&gt;Text&lt;/p&gt;. Az önérvényesített technikai profil **`OutputClaim` felhasználói beviteli** típusa beállításnál a `Required` attribútumot `false` (alapértelmezett) értékre kell állítani.

![Jogcím típusának használata bekezdéssel](./media/claimsschema/paragraph.png)

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
