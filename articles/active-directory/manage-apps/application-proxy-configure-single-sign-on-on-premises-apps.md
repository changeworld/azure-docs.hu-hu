---
title: SAML egyszeri bejelentkezés a helyi alkalmazásokhoz Azure AD alkalmazás proxyval
description: Ismerje meg, hogyan biztosíthat egyszeri bejelentkezést az SAML-hitelesítéssel védett helyszíni alkalmazásokhoz. Távoli hozzáférés biztosítása a helyszíni alkalmazásokhoz alkalmazásproxy használatával.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1046c11e064e69ed0ddb18c77bf5935ba60fb5aa
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461283"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>SAML egyszeri bejelentkezés a helyszíni alkalmazásokhoz alkalmazásproxy használatával

Az egyszeri bejelentkezést (SSO) az SAML-hitelesítéssel védett helyszíni alkalmazások számára is biztosíthatja, és az alkalmazás-proxyn keresztül távoli hozzáférést biztosíthat ezekhez az alkalmazásokhoz. Az SAML egyszeri bejelentkezéssel az Azure Active Directory (Azure AD) a felhasználó Azure AD-fiókjának használatával hitelesíti az alkalmazást. Azure ad-ben a bejelentkezési adatait az alkalmazás egy kapcsolat protokollon keresztül kommunikál. A felhasználókat az SAML-jogcímek által meghatározott szabályok alapján adott alkalmazási szerepkörökre is leképezheti. A SAML SSO mellett az alkalmazásproxy engedélyezésével a felhasználók külső hozzáféréssel fognak rendelkezni az alkalmazáshoz és a zökkenőmentes SSO-élményhez.

Az alkalmazásoknak képesnek kell lenniük a **Azure Active Directory**által kiadott SAML-tokenek felhasználására. Ez a konfiguráció nem vonatkozik a helyszíni identitás-szolgáltatót használó alkalmazásokra. Ezen forgatókönyvek esetében javasoljuk, hogy tekintse [át az alkalmazások Azure ad-ba való áttelepítésének erőforrásait](migration-resources.md).

Az SAML SSO és az Application proxy is együttműködik az SAML-jogkivonat titkosítási funkciójával. További információ: az [Azure ad SAML-jogkivonat titkosításának konfigurálása](howto-saml-token-encryption.md).

Az alábbi protokollok a szolgáltató által kezdeményezett (SP által kezdeményezett) folyamat és az identitás-szolgáltató által kezdeményezett (identitásszolgáltató által kezdeményezett) folyamat egyszeri bejelentkezési folyamatát írják le. Az alkalmazásproxy az SAML SSO-nal működik, ha gyorsítótárazza az SAML-kérelmet, és választ küld a helyi alkalmazásnak és a szolgáltatásnak.

  ![SAML SP-folyamat](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![SAML SP-folyamat](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Alkalmazás létrehozása és SAML SSO beállítása

1. A Azure Portal válassza a **Azure Active Directory > vállalati alkalmazások** lehetőséget, és válassza az **új alkalmazás**lehetőséget.

2. Adja meg az új alkalmazás megjelenítendő nevét, válassza a katalógusban **nem található bármely más alkalmazás integrálása**lehetőséget, majd válassza a **Létrehozás**lehetőséget.

3. Az alkalmazás **Áttekintés** lapján válassza az **egyszeri bejelentkezés**lehetőséget.

4. Válassza az **SAML** lehetőséget az egyszeri bejelentkezési módszerként.

5. Először állítsa be az SAML SSO-t, hogy működjön a vállalati hálózaton. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon nyissa meg az **alapszintű SAML-konfiguráció** fejlécét, és válassza ki annak **szerkesztési** ikonját (egy ceruza). Az alkalmazás SAML-alapú hitelesítésének konfigurálásához kövesse az [alapszintű SAML-konfiguráció megadása](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) című témakör lépéseit.

6. Adjon hozzá legalább egy felhasználót az alkalmazáshoz, és győződjön meg arról, hogy a teszt fiók hozzáfér az alkalmazáshoz. Ha csatlakozik a vállalati hálózathoz, a tesztelési fiók használatával ellenőrizheti, hogy van-e egyszeri bejelentkezés az alkalmazásba. 

   > [!NOTE]
   > Az alkalmazásproxy beállítása után visszatérhet és frissítheti az SAML **-Válasz URL-címét**.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>A helyszíni alkalmazás közzététele az Application proxyval

A helyszíni alkalmazásokhoz való egyszeri bejelentkezés biztosítása előtt engedélyeznie kell az alkalmazásproxy használatát, és telepítenie kell egy összekötőt. Tekintse meg az [Azure ad-beli Application proxyn keresztüli távelérés helyszíni alkalmazás hozzáadása](application-proxy-add-on-premises-application.md) a helyszíni környezet előkészítését, az összekötők telepítését és regisztrálását, valamint az összekötő tesztelését ismertető oktatóanyagot. Ezután kövesse az alábbi lépéseket az új alkalmazás alkalmazás-proxyval való közzétételéhez. Az alább nem említett egyéb beállításokért tekintse [meg a helyszíni alkalmazás hozzáadása az Azure ad-](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) hez című szakaszt az oktatóanyagban.

1. Ha az alkalmazás továbbra is meg van nyitva a Azure Portalban, válassza az **alkalmazásproxy**lehetőséget. Adja meg az alkalmazás **belső URL-címét** . Ha egyéni tartományt használ, fel kell töltenie az alkalmazás SSL-tanúsítványát is. 
   > [!NOTE]
   > Ajánlott eljárásként használjon egyéni tartományokat, amikor lehetséges az optimalizált felhasználói élmény. További információ az [Egyéni tartományok Azure ad Application proxy-beli használatáról](application-proxy-configure-custom-domain.md).

2. Válassza a **Azure Active Directory** lehetőséget az alkalmazás **előhitelesítési** módszere.

3. Másolja az alkalmazás **külső URL-címét** . Az SAML-konfiguráció befejezéséhez szüksége lesz erre az URL-címre.

4. A teszt fiók használatával próbálja meg megnyitni az alkalmazást a **külső URL-címmel** annak ellenőrzéséhez, hogy az alkalmazásproxy megfelelően van-e beállítva. Ha problémák merülnek fel, tekintse meg az [alkalmazásproxy-problémák és a hibaüzenetek hibaelhárítása](application-proxy-troubleshoot.md)című témakört.

## <a name="update-the-saml-configuration"></a>Az SAML-konfiguráció frissítése

1. Ha az alkalmazás továbbra is meg van nyitva a Azure Portalban, válassza az **egyszeri bejelentkezés**lehetőséget. 

2. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon nyissa meg az **alapszintű SAML-konfiguráció** fejlécét, és válassza ki annak **szerkesztési** ikonját (egy ceruza). Győződjön meg arról, hogy az alkalmazásproxy-ban konfigurált **külső URL** -cím fel van töltve az **azonosító**, a **Válasz URL-cím**és a **kijelentkezési URL-cím** mezőkbe. Ezek az URL-címek szükségesek ahhoz, hogy az alkalmazásproxy megfelelően működjön. 

3. Szerkessze a korábban konfigurált **Válasz URL-címet** , hogy a tartománya elérhető legyen az alkalmazásproxy számára. Ha például a **külső URL** -cím `https://contosotravel-f128.msappproxy.net`, és az eredeti **Válasz url-címe** `https://contosotravel.com/acs`volt, frissítenie kell az eredeti **válasz URL-címét** a `https://contosotravel-f128.msappproxy.net/acs`re. 

    ![Alapszintű SAML-konfigurációs adatok megadása](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Jelölje be a frissített **Válasz URL-címe** melletti jelölőnégyzetet az alapértelmezettként való megjelöléséhez.

   * Ha a szükséges **Válasz URL-cím** már szerepel a felsorolásban, akkor a **Válasz URL-címét** állítsa alapértelmezettként, és törölje a korábban konfigurált **Válasz URL-címét**.

   * Egy SP által kezdeményezett folyamat esetén győződjön meg arról, hogy a háttérbeli alkalmazás megadja a megfelelő **Válasz URL-címet** vagy az állítási fogyasztói szolgáltatás URL-címét a hitelesítési jogkivonat fogadásához.

    > [!NOTE]
    > Ha a háttérbeli alkalmazás a belső URL-címnek a **Válasz URL-címét** fogja használni, [Egyéni tartományokat](application-proxy-configure-custom-domain.md) kell használnia a belső és külső URL-címek egyeztetéséhez, vagy a saját alkalmazások biztonságos bejelentkezési bővítmény telepítését a felhasználói eszközökön. Ezt a bővítményt a rendszer automatikusan átirányítja a megfelelő alkalmazásproxy-szolgáltatásba. A bővítmény telepítéséhez tekintse meg az [alkalmazások biztonságos bejelentkezési bővítményét](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Az alkalmazás tesztelése

Ha végrehajtotta ezeket a lépéseket, az alkalmazás működik és kell lennie. Az alkalmazás tesztelése:

1. Nyisson meg egy böngészőt, és navigáljon arra a **külső URL-címre** , amelyet az alkalmazás közzétételekor hozott létre. 
1. Jelentkezzen be a teszt-fiókkal, amelyet az alkalmazáshoz rendelt. Képesnek kell lennie az alkalmazás betöltésére és az egyszeri bejelentkezésre az alkalmazásba.

## <a name="next-steps"></a>Következő lépések

- [Hogyan biztosítja az Azure AD Application Proxy az egyszeri bejelentkezést?](application-proxy-single-sign-on.md)
- [Alkalmazásproxy – problémamegoldás](application-proxy-troubleshoot.md)
