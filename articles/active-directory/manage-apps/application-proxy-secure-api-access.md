---
title: Helyszíni API-k elérése az Azure AD Application Proxy
description: Azure Active Directory alkalmazásproxy lehetővé teszi a natív alkalmazások számára a helyszíni vagy felhőalapú virtuális gépeken üzemeltetett API-k és üzleti logika biztonságos elérését.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ecd5d8bae22d67f8d9f5b99d5c94eecf54a4a1f3
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166018"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Biztonságos hozzáférés a helyszíni API-khoz az Azure AD Application Proxy

Előfordulhat, hogy a helyszínen vagy a felhőben futó virtuális gépeken üzemeltetett üzleti logikai API-kkal rendelkezik. A natív Android-, iOS-, Mac-vagy Windows-alkalmazásoknak kommunikálnia kell az API-végpontokkal az adatfelhasználáshoz vagy a felhasználói beavatkozás biztosításához. Az Azure AD Application Proxy és az [Azure Active Directory Authentication Library (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) lehetővé teszi, hogy a natív alkalmazások biztonságosan hozzáférjenek a helyszíni API-khoz. Azure Active Directory Application Proxy gyorsabb és biztonságosabb megoldás, mint a tűzfal portjainak megnyitása, valamint a hitelesítés és engedélyezés szabályozása az alkalmazás rétegében. 

Ez a cikk végigvezeti egy Azure AD Application Proxy megoldás beállításán, amely a natív alkalmazások által elérhető webes API-szolgáltatások üzemeltetésére szolgál. 

## <a name="overview"></a>Áttekintés

Az alábbi ábrán a helyszíni API-k közzétételének hagyományos módja látható. Ehhez a megközelítéshez a 80-es és a 443-es bejövő portok megnyitása szükséges.

![Hagyományos API-hozzáférés](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Az alábbi ábra bemutatja, hogyan használható az Azure AD Application Proxy az API-k biztonságos közzétételére a bejövő portok megnyitása nélkül:

![Azure AD Application Proxy API-hozzáférés](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Az Azure AD Application Proxy a megoldás gerincét képezi, amely nyilvános végpontként működik az API-hozzáféréshez, és biztosítja a hitelesítést és az engedélyezést. Az API-kat a platformok széles köréből érheti el a [ADAL](/azure/active-directory/develop/active-directory-authentication-libraries) -kódtárak használatával. 

Mivel az Azure AD Application Proxy hitelesítés és engedélyezés az Azure AD-re épül, az Azure AD feltételes hozzáférés használatával biztosíthatja, hogy csak a megbízható eszközök férhessenek hozzá az Application proxyn keresztül közzétett API-khoz. Az Azure AD JOIN vagy az Azure AD hibrid csatlakoztatása asztali számítógépekhez és az Intune által felügyelt eszközökhöz. Kihasználhatja az Azure-Multi-Factor Authentication, valamint az [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection)gépi tanulásra képes biztonsági mentésével prémium szintű Azure Active Directory szolgáltatásokat is.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató követéséhez a következőkre lesz szüksége:

- Rendszergazdai hozzáférés egy Azure-címtárhoz egy olyan fiókkal, amely alkalmazásokat hozhat létre és regisztrálhat
- A mintául szolgáló webes API és a natív ügyfélalkalmazások [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Az API közzététele az alkalmazásproxy használatával

Ha az intraneten kívüli API-t az Application proxyn keresztül szeretné közzétenni, a webalkalmazások közzétételével megegyező mintát kell követnie. További információ: [oktatóanyag: helyszíni alkalmazás hozzáadása a táveléréshez az Application proxyn keresztül Azure Active Directory](application-proxy-add-on-premises-application.md).

A SecretAPI webes API közzététele az Application proxyn keresztül:

1. Hozza létre és tegye közzé a minta SecretAPI-projektet ASP.NET-webalkalmazásként a helyi számítógépen vagy intraneten. Győződjön meg arról, hogy a webalkalmazást helyileg éri el. 
   
1. A [Azure Portal](https://portal.azure.com)válassza a **Azure Active Directory**lehetőséget. Ezután válassza a **vállalati alkalmazások**lehetőséget.
   
1. A **vállalati alkalmazások – minden alkalmazás** lap tetején válassza az **új alkalmazás**lehetőséget.
   
1. Az **alkalmazás hozzáadása** lapon válassza **a helyszíni alkalmazások**lehetőséget. Megjelenik a **saját helyszíni alkalmazás hozzáadása** lap.
   
1. Ha nincs telepítve alkalmazásproxy-összekötő, a rendszer kérni fogja a telepítését. Válassza az **alkalmazásproxy-összekötő letöltése** lehetőséget az összekötő letöltéséhez és telepítéséhez. 
   
1. Miután telepítette az alkalmazásproxy-összekötőt, a **saját helyszíni alkalmazás hozzáadása** oldalon:
   
   1. A **név**mellett adja meg a *SecretAPI*.
      
   1. A **belső URL-cím**mellett adja meg azt az URL-címet, amelyet az API-hoz való hozzáféréshez használ az intraneten belülről.
      
   1. Győződjön meg arról, hogy az **előhitelesítés** **Azure Active Directoryra**van beállítva. 
      
   1. A lap tetején kattintson a **Hozzáadás** gombra, és várja meg, amíg létre nem jön az alkalmazás.
   
   ![API-alkalmazás hozzáadása](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. A **vállalati alkalmazások – minden alkalmazás** lapon válassza ki a **SecretAPI** alkalmazást. 
   
1. A **SecretAPI – áttekintés** oldalon válassza a **Tulajdonságok** lehetőséget a bal oldali navigációs sávon.
   
1. Nem szeretné, hogy az API-k elérhetők legyenek a végfelhasználók számára a **MyApps** panelen, ezért a **Tulajdonságok** lap alján a **nem** értékre állítva **látható a felhasználók** számára, majd válassza a **Mentés**lehetőséget.
   
   ![A felhasználók számára nem látható](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Közzétette a webes API-t az Azure AD Application Proxyon keresztül. Most adja hozzá azokat a felhasználókat, akik hozzáférhetnek az alkalmazáshoz. 

1. A **SecretAPI – áttekintés** oldalon válassza a **felhasználók és csoportok** lehetőséget a bal oldali navigációs sávon.
   
1. A **felhasználók és csoportok** lapon válassza a **felhasználó hozzáadása**elemet.  
   
1. A **hozzárendelés hozzáadása** lapon válassza a **felhasználók és csoportok**lehetőséget. 
   
1. A **felhasználók és csoportok** lapon keresse meg és válassza ki azokat a felhasználókat, akik hozzáférhetnek az alkalmazáshoz, beleértve legalább a sajátját is. Az összes felhasználó kijelölése után válassza a **kiválasztás**lehetőséget. 
   
   ![Felhasználó kiválasztása és kiosztása](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Vissza a **hozzárendelés hozzáadása** oldalon válassza a **hozzárendelés**lehetőséget. 

> [!NOTE]
> Az integrált Windows-hitelesítést használó API-k [további lépéseket](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd)is igényelhetnek.

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>A natív alkalmazás regisztrálása és az API-hoz való hozzáférés biztosítása

A natív alkalmazások olyan programok, amelyek egy adott platformon vagy eszközön való használatra lettek kifejlesztve. Ahhoz, hogy a natív alkalmazás csatlakozhasson és hozzáférhessen egy API-hoz, regisztrálnia kell az Azure AD-ben. A következő lépések bemutatják, hogyan regisztrálhat egy natív alkalmazást, és hogyan férhet hozzá az Application proxyn keresztül közzétett webes API-hoz.

A AppProxyNativeAppSample natív alkalmazás regisztrálása:

1. Az Azure Active Directory **áttekintése** lapon válassza a **Alkalmazásregisztrációk**lehetőséget, majd a **Alkalmazásregisztrációk** panel felső részén válassza az **új regisztráció**lehetőséget.
   
1. Az **alkalmazás regisztrálása** oldalon:
   
   1. A **név**mezőben adja meg a *AppProxyNativeAppSample*. 
      
   1. A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**. 
      
   1. Az **átirányítás URL-címe**területen válassza a legördülő menü **nyilvános ügyfél (mobil & asztal)** lehetőséget, majd írja be a *https:\//appproxynativeapp*értéket. 
      
   1. Válassza a **regisztráció**lehetőséget, és várja meg, amíg az alkalmazás regisztrálása sikeresen megtörtént. 
      
      ![Új alkalmazásregisztráció](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
Most regisztrálta a AppProxyNativeAppSample alkalmazást a Azure Active Directoryban. A natív alkalmazás hozzáférésének biztosítása a SecretAPI webes API-hoz:

1. A Azure Active Directory **áttekintés** > **alkalmazás-regisztrációk** lapon válassza ki a **AppProxyNativeAppSample** alkalmazást. 
   
1. A **AppProxyNativeAppSample** lapon válassza az **API-engedélyek** lehetőséget a bal oldali navigációs sávon. 
   
1. Az **API-engedélyek** lapon válassza az **engedély hozzáadása**elemet.
   
1. Az első **kérés API-engedélyek** lapon válassza a **saját szervezet által használt API** -k fület, majd keresse meg és válassza a **SecretAPI**lehetőséget. 
   
1. A következő **kérelmek API-engedélyei** lapon jelölje be a **user_impersonation**melletti jelölőnégyzetet, majd válassza az **engedélyek hozzáadása**elemet. 
   
    ![API kiválasztása](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Az API- **engedélyek** lapon megadhatja a **contoso rendszergazdai jóváhagyása** lehetőséget, így megakadályozhatja, hogy más felhasználók ne kelljen egyénileg beleegyezniük az alkalmazásba. 

## <a name="configure-the-native-app-code"></a>A natív alkalmazás kódjának konfigurálása

Az utolsó lépés a natív alkalmazás konfigurálása. A NativeClient-minta alkalmazás *Form1.cs* fájljának következő kódrészlete azt eredményezi, hogy a ADAL-függvénytár szerzi be a tokent az API-hívás igényléséhez, és csatolja a tulajdonosként az alkalmazás fejlécébe. 
   
   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
       
       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
       
       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");
   
       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```
   
Ha úgy szeretné konfigurálni a natív alkalmazást, hogy az Azure Active Directoryhoz kapcsolódjon, és meghívja az API app proxyt, frissítse a helyőrző értékeket a NativeClient-minta alkalmazás *app. config* fájljában az Azure ad-ből származó értékekkel: 

- Illessze be a **könyvtár (bérlő) azonosítóját** a `<add key="ida:Tenant" value="" />` mezőbe. Ezt az értéket (GUID) a bármelyik alkalmazás **Áttekintés** lapjáról tekintheti meg és másolhatja. 
  
- Illessze be a AppProxyNativeAppSample **alkalmazás (ügyfél) azonosítóját** a `<add key="ida:ClientId" value="" />` mezőbe. Ezt az értéket (GUID) a AppProxyNativeAppSample **– Áttekintés** oldalon találja és másolhatja.
  
- Illessze be a AppProxyNativeAppSample **átirányítási URI** -t a `<add key="ida:RedirectUri" value="" />` mezőbe. Ezt az értéket (URI) megkeresheti és átmásolhatja a AppProxyNativeAppSample- **hitelesítés** oldaláról. 
  
- Illessze be a SecretAPI- **alkalmazás azonosítójának URI-ját** a `<add key="todo:TodoListResourceId" value="" />` mezőbe. Ezt az értéket (URI) megkeresheti és átmásolhatja az SecretAPI **elérhetővé tenni egy API** -lapot.
  
- Illessze be a SecretAPI **kezdőlapjának URL-címét** a `<add key="todo:TodoListBaseAddress" value="" />` mezőbe. Ezt az értéket (URL-címet) megkeresheti és átmásolhatja a SecretAPI **branding** oldaláról.

A paraméterek konfigurálása után hozza létre és futtassa a natív alkalmazást. Amikor kiválasztja a **Bejelentkezés** gombot, az alkalmazás lehetővé teszi a bejelentkezést, majd egy sikeres képernyőt jelenít meg annak ellenőrzéséhez, hogy sikeresen csatlakozott-e a SecretAPI.

![Sikeres](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: helyi alkalmazás hozzáadása a távoli eléréshez az alkalmazásproxy használatával Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Gyors útmutató: ügyfélalkalmazás konfigurálása a webes API-k eléréséhez](../develop/quickstart-configure-app-access-web-apis.md)
- [Natív ügyfélalkalmazások engedélyezése a proxy alkalmazásokkal való kommunikációhoz](application-proxy-configure-native-client-application.md)
