---
title: A Microsoft-hitelesítés konfigurálása
description: Ismerje meg, hogyan konfigurálhatja a Microsoft-fiók hitelesítését identitás-szolgáltatóként a App Service-alkalmazáshoz.
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.topic: article
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 95c603d4a10eb0e4d0817e20755c0f9b36baa96f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842333"
---
# <a name="configure-your-app-service-app-to-use-microsoft-account-login"></a>A App Service alkalmazás konfigurálása a Microsoft-fiók bejelentkezési használatára

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Ebből a témakörből megtudhatja, hogyan konfigurálhatja a Azure App Servicet a HRE használatára a személyes Microsoft-fiók-bejelentkezések támogatásához.

> [!NOTE]
> A személyes Microsoft-fiókok és a szervezeti fiókok egyaránt a HRE identitás-szolgáltatót használják. Jelenleg nem lehet konfigurálni ezt az identitás-szolgáltatót mindkét típusú bejelentkezés támogatásához.

## <a name="register-microsoft-account"> </a>Alkalmazás regisztrálása a Microsoft-fiókkal

1. Lépjen [**Alkalmazásregisztrációk**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) a Azure Portal. Ha szükséges, jelentkezzen be a Microsoft-fiók.
1. Válassza az **új regisztráció**lehetőséget, majd adja meg az alkalmazás nevét.
1. A **támogatott fióktípus**területen válassza a **fiókok bármely szervezeti címtárban (bármely Azure ad-címtár – több-bérlő) és a személyes Microsoft-fiókok (például Skype, Xbox) lehetőséget.**
1. Az **átirányítási URI**-k területen válassza a **web**lehetőséget, majd írja be a `https://<app-domain-name>/.auth/login/aad/callback`. Cserélje le *\<app-domain-name >* az alkalmazás tartománynevére.  Például: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Ügyeljen arra, hogy az URL-címben a HTTPS-sémát használja.

1. Kattintson a **Register** (Regisztrálás) elemre.
1. Másolja az **alkalmazás (ügyfél) azonosítóját**. Erre később még szüksége lesz.
1. A bal oldali panelen válassza a **tanúsítványok & secrets** > **új ügyfél titka**lehetőséget. Adja meg a leírást, válassza ki az érvényesség időtartamát, és válassza a **Hozzáadás**lehetőséget.
1. Másolja a **tanúsítványok & titkok** lapon megjelenő értéket. Miután elhagyta a lapot, nem jelenik meg újra.

    > [!IMPORTANT]
    > Az ügyfél titkos értéke (jelszó) fontos biztonsági hitelesítő adat. Ne ossza meg senkivel a jelszót, vagy küldje el azt egy ügyfélalkalmazáson belül.

## <a name="secrets"> </a>Microsoft-fiókadatok hozzáadása a app Service-alkalmazáshoz

1. Nyissa meg az alkalmazást a [Azure Portal].
1. Válassza a **beállítások** > a **hitelesítés/engedélyezés**lehetőséget, és győződjön meg arról, hogy a **app Service hitelesítés** **be van kapcsolva**.
1. A **hitelesítésszolgáltatók**területen válassza a **Azure Active Directory**lehetőséget. Válassza a speciális **felügyeleti mód**lehetőséget. Illessze be a korábban beszerzett alkalmazás (ügyfél) AZONOSÍTÓját és az ügyfél titkos kulcsát. Használja **https://login.microsoftonline.com/9188040d-6c67-4c5b-b112-36a304b66dad/v2.0** a **kiállító URL-címe** mezőben.
1. Kattintson az **OK** gombra.

   App Service hitelesítést biztosít, de nem korlátozza a webhely tartalmához és API-khoz való jogosult hozzáférést. Engedélyezni kell a felhasználókat az alkalmazás kódjában.

1. Választható A Microsoft-fiók felhasználókhoz való hozzáférés korlátozásához állítsa be a **műveletet, ha a kérelem nem hitelesítve van** a **Azure Active Directoryval való bejelentkezéshez**. Ha beállítja ezt a funkciót, az alkalmazásnak minden kérelmet hitelesítenie kell. Emellett az összes nem hitelesített kérelmet is átirányítja a HRE használatára a hitelesítéshez. Vegye figyelembe, hogy mivel a **kiállítói URL-címet** a Microsoft-fiók bérlő használatára konfigurálta, a rendszer csak a személyes acccounts hitelesíti.

   > [!CAUTION]
   > A hozzáférés ily módon való korlátozása az alkalmazás összes hívására vonatkozik, ami nem kívánatos olyan alkalmazások esetében, amelyek nyilvánosan elérhető kezdőlaptal rendelkeznek, mint sok egyoldalas alkalmazásban. Ilyen alkalmazások esetén **engedélyezze a névtelen kérelmeket (nincs művelet)** előnyben részesített, hogy az alkalmazás manuálisan megkezdse a hitelesítést. További információ: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow).

1. Kattintson a **Mentés** gombra.

Most már készen áll a Microsoft-fiók használatára a hitelesítéshez az alkalmazásban.

## <a name="related-content"> </a>További lépések

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
