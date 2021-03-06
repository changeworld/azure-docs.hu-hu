---
title: Azure Multi-Factor Authentication-verziók és-fogyasztási csomagok
description: Ismerje meg az Azure multi-Factor Authentication-ügyfelet, valamint az elérhető különböző módszereket és verziókat.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e74a7ab0c003aaf9d90211484b39f8322cd9c329
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648002"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication szolgáltatásai és licencei

A szervezet felhasználói fiókjainak védeleméhez többtényezős hitelesítést kell használni. Ez a funkció különösen fontos olyan fiókoknál, amelyek privilegizált hozzáféréssel rendelkeznek az erőforrásokhoz. Az Office 365 és a Azure Active Directory (Azure AD) rendszergazdák számára az alapszintű multi-Factor Authentication szolgáltatás további költségek nélkül elérhető. Ha szeretné frissíteni a rendszergazdák vagy a többtényezős hitelesítés használatát a felhasználók többi részén, több módon is megvásárolhatja az Azure Multi-Factor Authentication.

> [!IMPORTANT]
> Ez a cikk az Azure Multi-Factor Authentication által licencelt és használható különböző módszereket részletezi. A díjszabással és a számlázással kapcsolatos részletekért tekintse meg az [Azure multi-Factor Authentication díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication elérhető verziói

Az Azure Multi-Factor Authentication a szervezet igényeitől függően különböző módokon használhatók és licenccel is rendelkezhetnek. Lehetséges, hogy az Azure AD-t, az Office 365-et, az EMS-t vagy a jelenleg használt Microsoft 365-licencet az Azure Multi-Factor Authentication használatára jogosult. Az alábbi táblázat ismerteti az Azure-Multi-Factor Authentication beszerzésének különböző módjait, valamint az egyes funkciók és használati esetek leírását.

| Ha a felhasználó a | Képességek és használati esetek |
| --- | --- |
| EMS vagy Microsoft 365 E3 és E5 | Az EMS E3 vagy Microsoft 365 E3 (amely magában foglalja az EMS és az Office 365) prémium szintű Azure AD P1-et is tartalmaz. Az EMS E5 vagy Microsoft 365 E5 prémium szintű Azure AD P2-t tartalmazza. A következő szakaszokban ismertetett feltételes hozzáférési funkciók használatával többtényezős hitelesítést biztosíthat a felhasználóknak. |
| Prémium szintű Azure AD P1 | Az [Azure ad feltételes hozzáférés](../conditional-access/overview.md) használatával a felhasználók a többtényezős hitelesítésre való rákérdezéshez bizonyos esetekben vagy eseményeknél, az üzleti igényeknek megfelelően. |
| Prémium szintű Azure AD P2 | Biztosítja a legerősebb biztonsági helyzetet és a jobb felhasználói élményt. [Kockázatalapú feltételes hozzáférést biztosít](../conditional-access/howto-conditional-access-policy-risk.md) a prémium szintű Azure ad P1-funkciókhoz, amelyek alkalmazkodnak a felhasználói mintákhoz, és lekicsinyítik a többtényezős hitelesítési kéréseket. |
| Office 365 Business Premium, E3 vagy E5 | Az Azure Multi-Factor Authentication az összes felhasználó számára engedélyezve van vagy le van tiltva az összes bejelentkezési esemény esetében. Nincs lehetőség a többtényezős hitelesítés engedélyezésére a felhasználók egy részhalmaza számára, vagy csak bizonyos esetekben. A felügyelet az Office 365-portálon keresztül történik. A jobb felhasználói élmény érdekében frissítsen prémium szintű Azure AD P1-re vagy P2-re, és használja a feltételes hozzáférést. További információ: [az Office 365-erőforrások védelme többtényezős hitelesítéssel](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). |
| Ingyenes Azure AD | A [biztonsági alapértelmezett beállításokkal](../fundamentals/concept-fundamentals-security-defaults.md) engedélyezheti a többtényezős hitelesítést az összes felhasználó számára, valahányszor hitelesítési kérés történik. Nem rendelkezik az engedélyezett felhasználók vagy forgatókönyvek részletes szabályozásával, de ez további biztonsági lépést is biztosít.<br /> Még akkor is, ha a biztonsági alapértékek nem használják a többtényezős hitelesítést mindenki számára, az *Azure ad globális rendszergazdai* szerepkörrel rendelkező felhasználók úgy konfigurálhatók, hogy a többtényezős hitelesítést használják. Az ingyenes szint ezen funkciója biztosítja, hogy a kritikus rendszergazdai fiókok védelmét a multi-Factor Authentication védi. |

## <a name="feature-comparison-of-versions"></a>A verziók összehasonlítása

A következő táblázat felsorolja az Azure Multi-Factor Authentication különböző verzióiban elérhető funkciókat. Tervezze meg a felhasználói hitelesítés biztonságossá tételének szükségességét, majd határozza meg, hogy melyik megközelítés teljesíti ezeket a követelményeket. Például bár a ingyenes Azure AD biztosít az Azure Multi-Factor Authenticationt biztosító biztonsági alapértékeket, csak a mobil hitelesítő alkalmazás használható a hitelesítési kéréshez, nem pedig telefonhíváshoz vagy SMS-hez. Ez a megközelítés akkor lehet korlátozás, ha nem biztos benne, hogy a Mobile Authentication alkalmazás telepítve van a felhasználó személyes eszközén.

| Funkció | Ingyenes Azure AD – biztonsági alapértékek | Ingyenes Azure AD – Azure AD globális rendszergazdák | Office 365 Business Premium, E3 vagy E5 | prémium szintű Azure AD P1 vagy P2 |
| --- |:---:|:---:|:---:|:---:|
| Azure AD-bérlői rendszergazdai fiókok biztosítása MFA-val | ● | ● (Csak*Azure ad globális rendszergazdai* fiókok) | ● | ● |
| Mobile App második tényezőként | ● | ● | ● | ● |
| Telefonhívás második tényezőként | | ● | ● | ● |
| SMS második tényezőként | | ● | ● | ● |
| Az ellenőrzési módszerek rendszergazdai ellenőrzése | | ● | ● | ● |
| Csalási riasztás | | | | ● |
| MFA-jelentések | | | | ● |
| Egyéni üdvözlések a telefonhívásokhoz | | | | ● |
| Telefonos hívások egyéni hívóazonosító | | | | ● |
| Megbízható IP-címek | | | | ● |
| MFA megjegyzése megbízható eszközökön | | ● | ● | ● |
| MFA helyszíni alkalmazásokhoz | | | | ● |

> [!IMPORTANT]
> A 2019 márciusában a telefonhívási lehetőségek már nem érhetők el az Azure Multi-Factor Authentication és az Azure önkiszolgáló jelszó-visszaállítási felhasználói számára ingyenes Azure AD/próbaverziós bérlők esetében. Ez a változás nem érinti az SMS-üzeneteket. A telefonhívások továbbra is elérhetők a prémium szintű Azure AD P1 vagy P2 bérlők, illetve a vagy az Office 365 Business Premium, E3 vagy E5 rendszerű felhasználók számára.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Azure-Multi-Factor Authentication vásárlása és engedélyezése

Az Azure Multi-Factor Authentication használatához Regisztráljon vagy vásároljon egy jogosult Azure AD-szintet. Az Azure AD négy kiadásban érhető el: ingyenes, Office 365 alkalmazások kiadása (az Office 365 Business Premium E3 vagy E5 ügyfelek esetében), prémium P1 és prémium P2.

Az ingyenes kiadást egy Azure-előfizetés tartalmazza. Tekintse meg az [alábbi szakaszt](#azure-ad-free-tier) a biztonsági Alapértelmezések használatáról, illetve a fiókok védelméről az *Azure ad globális rendszergazdai* szerepkörrel.

A prémium szintű Azure AD kiadása a Microsoft képviselőjétől, a [nyílt mennyiségi licencelési programtól](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)és a [Cloud Solution Providers programtól](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409)érhető el. Az Azure és az Office 365-előfizetők prémium szintű Azure Active Directory P1 és P2 online is vásárolhatnak. [Jelentkezzen be](https://portal.office.com/Commerce/Catalog.aspx) a vásárláshoz.

> [!IMPORTANT]
> A fogyasztáson alapuló licencelés az új ügyfelek számára már nem érhető el, 2018. szeptember 1-től érvényes. A fogyasztáson alapuló modellt használó meglévő ügyfelek továbbra is használhatják az engedélyezett felhasználónként vagy a hitelesítési számlázással.

Miután megvásárolta a szükséges Azure AD-szintet, [tervezze meg és telepítse az azure multi-Factor Authenticationt](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>ingyenes Azure ADi szintű

Egy ingyenes Azure AD bérlő összes felhasználója használhatja az Azure multi-Factor Authenticationt a biztonsági Alapértelmezések használatával. Ezek a biztonsági alapértékek lehetővé teszik az Azure multi-Factor Authentication használatát minden felhasználó számára, valahányszor bejelentkeznek. A Mobile Authentication alkalmazás az egyetlen módszer, amelyet az Azure Multi-Factor Authentication használhat ingyenes Azure AD biztonsági Alapértelmezések használata esetén.

* [További információ az Azure AD biztonsági beállításairól](../fundamentals/concept-fundamentals-security-defaults.md)
* [A ingyenes Azure AD felhasználók biztonsági alapértelmezésének engedélyezése](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Ha nem szeretné engedélyezni az Azure-Multi-Factor Authentication az összes felhasználó és a bejelentkezési esemény számára, akkor csak az *Azure ad globális rendszergazdai* szerepkörrel rendelkező felhasználói fiókokat kell védelemmel ellátnia. Ez a megközelítés további hitelesítési kéréseket biztosít a kritikus rendszergazdai fiókokhoz. Az Azure Multi-Factor Authentication az alábbi módszerek egyikével engedélyezheti a használt fiók típusától függően:

* Ha Microsoft-fiókot használ, [regisztráljon a multi-Factor Authentication szolgáltatásra](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Ha nem Microsoft-fiókot használ, [kapcsolja be a többtényezős hitelesítést egy felhasználó vagy csoport számára az Azure ad-ben](howto-mfa-userstates.md).

## <a name="next-steps"></a>Következő lépések

A költségekkel kapcsolatos további információkért lásd: az [Azure multi-Factor Authentication díjszabása](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
