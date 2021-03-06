---
title: Az Azure AD-hitelesítés konfigurálása
description: Megtudhatja, hogyan konfigurálhatja Azure Active Directory hitelesítést identitás-szolgáltatóként a App Service alkalmazáshoz.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 717336e0ddfe99c96afda4861f4de1239ee949bf
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913208"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>App Service alkalmazás konfigurálása az Azure AD-bejelentkezés használatára

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja a Azure App Servicet a Azure Active Directory (Azure AD) hitelesítési szolgáltatóként való használatára.

Az alkalmazás és a hitelesítés beállításakor kövesse az alábbi ajánlott eljárásokat:

- Adja meg az egyes App Service alkalmazások saját engedélyeit és hozzájárulásukat.
- Konfigurálja az egyes App Service alkalmazásait a saját regisztrálásával.
- Kerülje a környezetek közötti engedélyek megosztását külön alkalmazás-regisztrációk használatával külön üzembe helyezési pontokhoz. Az új kód tesztelésekor ez a gyakorlat segít megakadályozni az éles alkalmazást érintő problémákat.

## <a name="express"> </a>Konfigurálás az expressz beállításokkal

> [!NOTE]
> Az **expressz** beállítás a kormányzati felhők esetében nem érhető el. 

1. A [Azure Portalra]keresse meg és válassza ki a **app Services**, majd válassza ki az alkalmazást.
2. A bal oldali navigációs sávon válassza a **hitelesítés/engedélyezés** > **bekapcsolva**lehetőséget.
3. Válassza ki **Azure Active Directory** > **Express**elemet.

   Ha inkább egy meglévő alkalmazás-regisztrációt szeretne választani:

   1. Válassza a **meglévő ad-alkalmazás kiválasztása**lehetőséget, majd kattintson a **Azure ad alkalmazás**elemre.
   2. Válasszon egy meglévő alkalmazás-regisztrációt, és kattintson **az OK**gombra.

3. Kattintson az **OK** gombra, hogy regisztrálja a app Service alkalmazást a Azure Active Directoryban. Létrejön egy új alkalmazás regisztrálása.
   
    ![Expressz beállítások a Azure Active Directoryban](./media/configure-authentication-provider-aad/express-settings.png)
   
4. Választható Alapértelmezés szerint a App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. Engedélyezni kell a felhasználókat az alkalmazás kódjában. Ha csak az Azure Active Directory által hitelesített felhasználók számára kívánja korlátozni az alkalmazás-hozzáférést, állítsa be **a végrehajtandó műveletet, ha a kérés nincs hitelesítve** a **Azure Active Directoryval való bejelentkezéshez**. Ha beállítja ezt a funkciót, az alkalmazásnak minden kérelmet hitelesítenie kell. Emellett átirányítja az összes nem hitelesített Azure Active Directory a hitelesítéshez.

    > [!CAUTION]
    > A hozzáférés ily módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos olyan alkalmazások esetében, amelyek nyilvánosan elérhető kezdőlaptal rendelkeznek, mint sok egyoldalas alkalmazásban. Ilyen alkalmazások esetén **engedélyezze a névtelen kérelmeket (nincs művelet)** előnyben részesített, ha az alkalmazás manuálisan indítja el a bejelentkezést. További információ: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow).
5. Kattintson a **Mentés** gombra.

## <a name="advanced"> </a>Konfigurálás speciális beállításokkal

Az Alkalmazásbeállítások manuálisan is konfigurálhatók, ha egy másik Azure AD-bérlőből származó alkalmazás-regisztrációt szeretne használni. Az egyéni konfiguráció elvégzéséhez:

1. Hozzon létre egy regisztrációt az Azure AD-ben.
2. Adja meg a App Service regisztrációs adatait.

### <a name="register"> </a>Alkalmazás-regisztráció létrehozása az Azure ad-ben a app Service-alkalmazáshoz

A App Service alkalmazás konfigurálásakor a következő információkra lesz szüksége:

- Ügyfél-azonosító
- Bérlőazonosító
- Ügyfél titka (nem kötelező)
- Alkalmazás-azonosító URI-ja

Hajtsa végre a következő lépéseket:

1. Jelentkezzen be a [Azure Portalra], keresse meg és válassza ki a **app Services**, majd válassza ki az alkalmazást. Jegyezze fel az alkalmazás **URL-címét**. Ezzel konfigurálja a Azure Active Directory alkalmazás regisztrációját.
1. Válassza a **Azure Active Directory** > **Alkalmazásregisztrációk** > **új regisztráció**lehetőséget.
1. Az **alkalmazás regisztrálása** lapon adja meg az alkalmazás regisztrációjának **nevét** .
1. Az **átirányítási URI**-ban válassza a **web** lehetőséget, és írja be `<app-url>/.auth/login/aad/callback`. Például: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Kattintson a **Létrehozás** gombra.
1. Az alkalmazás regisztrációjának létrehozása után másolja az **alkalmazás (ügyfél) azonosítóját** és a **címtár (bérlő) azonosítóját** később.
1. Válassza a **branding**elemet. A **Kezdőlap URL-címe**mezőben adja meg app Service alkalmazásának URL-címét, majd válassza a **Mentés**lehetőséget.
1. Válassza ki **az API** > - **készlet**közzététele lehetőséget. Illessze be a App Service alkalmazás URL-címét, majd válassza a **Mentés**lehetőséget.

   > [!NOTE]
   > Ez az érték az alkalmazás regisztrációs **azonosítójának URI** azonosítója. Ha a webalkalmazásnak hozzáférést kell biztosítania egy Felhőbeli API-hoz, akkor a felhő App Service erőforrásának konfigurálásakor a webalkalmazás **alkalmazás-azonosító URI-ja** szükséges. Ezt például akkor használhatja, ha azt szeretné, hogy a Cloud Service explicit módon engedélyezze a hozzáférést a webalkalmazáshoz.

1. Válassza a **Hatókör hozzáadása** lehetőséget.
   1. A **hatókör neve**mezőbe írja be a *user_impersonation*.
   1. A szövegmezőben adja meg a belefoglalni kívánt hatókör nevét és leírását, amelyet a felhasználók a beleegyező lapon láthatnak. Írja be például a következőt: *hozzáférés az alkalmazáshoz*. 
   1. Válassza a **hatókör hozzáadása**elemet.
1. Választható Az ügyfél titkos kulcsának létrehozásához válassza a **tanúsítványok & secrets** > **új ügyfél titkos** > **Hozzáadás**lehetőséget. Másolja a lapon látható ügyfél titkos kulcs értékét. Nem jelenik meg újra.
1. Választható Több **Válasz URL-cím**hozzáadásához válassza a **hitelesítés**lehetőséget.

### <a name="secrets"> </a>Azure Active Directory engedélyezése a app Service alkalmazásban

1. A [Azure Portalra]keresse meg és válassza ki a **app Services**, majd válassza ki az alkalmazást. 
1. A bal oldali ablaktábla **Beállítások**területén válassza a **hitelesítés/engedélyezés** > **bekapcsolva**lehetőséget.
1. Választható Alapértelmezés szerint a App Service hitelesítés lehetővé teszi a nem hitelesített hozzáférést az alkalmazáshoz. A felhasználói hitelesítés érvénybe léptetéséhez állítsa be a **műveletet, ha a kérelem nem hitelesítve van** a **Azure Active Directoryba való bejelentkezéshez**.
1. A **hitelesítésszolgáltatók**területen válassza a **Azure Active Directory**lehetőséget.
1. **Felügyeleti módban**válassza a **speciális** lehetőséget, és konfigurálja app Service hitelesítést az alábbi táblázatnak megfelelően:

    |Mező|Leírás|
    |-|-|
    |Ügyfél-azonosító| Használja az **alkalmazás regisztrációjának alkalmazás-(ügyfél-) azonosítóját** . |
    |Kiállító azonosítója| Használja a `https://login.microsoftonline.com/<tenant-id>`t, és cserélje le *\<bérlő-azonosító >* az alkalmazás regisztrációjának **könyvtár (bérlő) azonosítójával** . |
    |Ügyfél titka (nem kötelező)| Használja az alkalmazás regisztrációjában létrehozott ügyfél-titkos kulcsot.|
    |Engedélyezett jogkivonat-célközönségek| Ha ez egy Felhőbeli vagy kiszolgálóalkalmazás-alkalmazás, és engedélyezni szeretné a hitelesítési jogkivonatokat egy webalkalmazásból, adja hozzá a webalkalmazás **alkalmazás-azonosító URI-ját** itt. A konfigurált **ügyfél** -azonosító *mindig* implicit módon engedélyezett célközönségnek tekintendő. |

2. Válassza **az OK**, majd a **Mentés**lehetőséget.

Most már készen áll a Azure Active Directory használatára a App Service alkalmazásban való hitelesítéshez.

## <a name="configure-a-native-client-application"></a>Natív ügyfélalkalmazás konfigurálása

A natív ügyfelek regisztrálása lehetővé teszi a hitelesítés használatát egy ügyféloldali kódtár, például a **Active Directory-hitelesítési tár**használatával.

1. A [Azure Portalra]válassza a **Active Directory** > **Alkalmazásregisztrációk** > **új regisztráció**lehetőséget.
1. Az **alkalmazás regisztrálása** lapon adja meg az alkalmazás regisztrációjának **nevét** .
1. Az **átirányítási URI**-ban válassza a **nyilvános ügyfél (mobil & asztal)** lehetőséget, és írja be az URL-címet `<app-url>/.auth/login/aad/callback`. Például: `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Windows-alkalmazások esetén a [csomag biztonsági azonosítóját](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) használja inkább URI-ként.
1. Kattintson a **Létrehozás** gombra.
1. Az alkalmazás regisztrációjának létrehozása után másolja az **Application (ügyfél) azonosító**értékét.
1. Válassza az **API-engedélyek** lehetőséget > a **saját api**-k > **engedély hozzáadása** elemet.
1. Válassza ki a korábban létrehozott App Service alkalmazás regisztrációját. Ha nem látja az alkalmazás regisztrációját, győződjön meg arról, hogy felvette a **user_impersonation** hatókört az [alkalmazás regisztrációjának létrehozása az Azure ad-ben a app Service alkalmazáshoz](#register).
1. Válassza a **user_impersonation**lehetőséget, majd kattintson az **engedélyek hozzáadása**lehetőségre.

Ezzel konfigurált egy natív ügyfélalkalmazás, amely hozzáfér a App Service-alkalmazáshoz.

## <a name="related-content"> </a>További lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure Portalra]: https://portal.azure.com/
