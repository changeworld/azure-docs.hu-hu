---
title: Felhasználók, csoportok, licencelés és szerepkörök áttekintése – Azure AD | Microsoft Docs
description: A felhasználók és a hozzárendelt licencek, a rendszergazdai szerepkörök és a csoporttagságok közti kapcsolatok az Azure Active Directoryban
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.reviewer: vincesm
ms.date: 11/08/2019
ms.topic: overview
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3cc2de5a2f297e8133011905ff2961b44476d6b
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046337"
---
# <a name="users-groups-licensing-and-roles-for-large-organizations"></a>Nagyobb szervezetek felhasználói, csoportjai, licenckezelése és szerepkörei

A cikk az Azure AD-rendszergazdák számára mutatja be a felhasználókra vonatkozó felső szintű [identitáskezelési](/azure/active-directory/fundamentals/identity-fundamentals?context=azure/active-directory/users-groups-roles/context/ugr-context) feladatok közti kapcsolatot a csoportok, a licencek, az üzembe helyezett vállalati alkalmazások és a rendszergazdai szerepkörök tekintetében. A szervezet növekedtével az Azure AD-csoportok és rendszergazdai szerepkörök használatával a következőket teheti:

* Licencek hozzárendelése egyének helyett csoportokhoz
* Engedélyek delegálása az Azure AD felügyeleti tevékenységeinek korlátozottabb jogosultságokkal rendelkező szerepkörökre való elosztásához
* Vállalati alkalmazások hozzáférésének hozzárendelése csoportokhoz

## <a name="assign-users-to-groups"></a>Felhasználók hozzárendelése csoportokhoz

A csoportok segítségével az Azure AD-ben licenceket rendelhet hozzá egyszerre sok felhasználóhoz, illetve felhasználói hozzáféréseket rendelhet hozzá a telepített vállalati alkalmazásokhoz. A csoportok segítségével az összes rendszergazdai szerepkört hozzárendelheti, kivéve az Azure AD globális rendszergazdáját, vagy megadhat hozzáférést a külső erőforrásokhoz, például SaaS-alkalmazásokhoz vagy SharePoint-webhelyekhez.

A nagyobb rugalmasság és a csoporttagság-felügyeleti tevékenységek csökkentése érdekében [dinamikus csoportokat](groups-create-rule.md) is használhat az Azure AD-ben a csoporttagság automatikus kiterjesztésére és szűkítésére. Minden egyedi felhasználóhoz, amely egy vagy több dinamikus csoport tagja, egy Azure AD Premium P1-licencre lesz szükség.

## <a name="assign-licenses-to-groups"></a>Licencek hozzárendelése csoportokhoz

Ha a licenceket külön rendeli hozzá vagy törli a felhasználókról, az sok időt és nagy figyelmet vehet igénybe. Ha a [licenceket inkább csoportokhoz rendeli](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context), a nagy volumenű licenc-hozzárendelési tevékenységeket egyszerűbbé teheti.

Amikor az Azure AD-ben a felhasználó egy licencelt csoport tagja lesz, automatikusan megkapja a megfelelő licenceket. Amikor a felhasználók elhagyják a csoportot, az Azure AD törli a hozzárendelt licenceket. Az Azure AD-csoportok nélkül PowerShell-szkripteket kellene írnia vagy a Graph API-t használnia a felhasználói licencek tömeges hozzáadásához vagy eltávolításához a szervezethez csatlakozó vagy azt elhagyó felhasználók számára.

Ha nem áll elég licenc rendelkezésre, vagy valamilyen hiba lép fel, például egyszerre hozzá nem rendelhető szolgáltatáscsomagok esetén, a csoport licencelési problémáinak állapotát az Azure Portalon tekintheti meg.

>[!NOTE]
>A csoportalapú licencelési funkció jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzióban a funkció bármilyen fizetős Azure Active Directory- (Azure AD-) licenccsomaggal vagy próbaverzióval elérhető.

## <a name="delegate-administrator-roles"></a>Rendszergazdai szerepkörök delegálása

Számos nagyméretű szervezet szeretne a felhasználó számára megfelelő lehetőségeket adni a munkavégzéshez anélkül, hogy a nagy hatalmú globális rendszergazdai szerepkört osztaná ki például az olyan felhasználók számára, akiknek alkalmazásokat kell tudnia regisztrálni. Íme egy példa az új Azure AD-rendszergazdai szerepkörökre, amelyekkel az alkalmazásfelügyeleti feladatok finomabb tagoltsággal oszthatók ki:

 Szerepkörnév | Engedélyek összegzése
 --------- | -------------------
 **Alkalmazás-rendszergazda** | Hozzáadhatja és felügyelheti a vállalati alkalmazásokat és az alkalmazásregisztrációkat, és konfigurálhatja a proxyalkalmazás-beállításokat. Az alkalmazás-rendszergazdák megtekinthetik a feltételes hozzáférési házirendeket és eszközöket, de nem kezelhetik azokat.
 **Felhőalkalmazás-rendszergazda** | Hozzáadhatja és felügyelheti a vállalati alkalmazásokat és a vállalati alkalmazásregisztrációkat. Ez a szerepkör az alkalmazás-rendszergazda összes engedélyével rendelkezik, azzal a különbséggel, hogy nem tudja kezelni az alkalmazásproxy-beállításokat.
**Alkalmazásfejlesztő** | Hozzáadhatja és frissítheti az alkalmazásregisztrációkat, de nem felügyelheti a vállalati alkalmazásokat és nem konfigurálhatja az alkalmazásproxykat.

Folyamatosan jelennek meg új Azure AD-rendszergazdai szerepkörök. A jelenleg elérhető szerepköröket az Azure Portalon vagy a [rendszergazdai szerepkörök engedélyeinek referenciájában](directory-assign-admin-roles.md) tekintheti meg.

## <a name="assign-app-access"></a>Alkalmazás-hozzáférés hozzárendelése

Az Azure AD használatával csoportszintű hozzáférést biztosíthat [az Azure AD-bérlőn üzembe helyezett vállalati alkalmazásokhoz](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context). A csoportszintű alkalmazás-hozzárendelést a dinamikus csoportokkal kombinálva automatizálható a felhasználók alkalmazás-hozzáféréseinek hozzárendelése a szervezet növekedésével. A vállalati alkalmazások hozzáféréseinek kiosztásához Azure Active Directory Premium P1 vagy P2 szintű licenc szükséges.

Az Azure AD emellett lehetővé teszi az alkalmazások és a hozzáféréssel felruházott csoportok közötti adatáramlás finomhangolását. A [Vállalati alkalmazásokban](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) az alkalmazást megnyitva, majd a **Kiépítés** lehetőséget választva:

* Beállíthatja az automatikus kiépítést az olyan alkalmazások esetében, amelyek támogatják azt
* Megadhatja a hitelesítő adatokat az alkalmazás felhasználókezelési API-ja számára
* Beállíthatja a leképezéseket, amelyek azt szabályozzák, hogy melyik felhasználói attribútumok legyenek továbbítva az Azure AD és az alkalmazás között a felhasználói fiókok kiépítése vagy frissítése során
* Elindíthatja vagy leállíthatja az Azure AD kiépítési szolgáltatást valamely alkalmazásra, törölheti a kiépítési gyorsítótárat, vagy újraindíthatja a szolgáltatást
* Megtekintheti a **kiépítési tevékenységekre vonatkozó jelentést**, amely naplózza az Azure AD és az alkalmazás között létrehozott, frissített és törölt összes felhasználót és csoportot, valamint a **kiépítési hibákat tartalmazó jelentést**, amely a részletes hibaüzeneteket tartalmazza

## <a name="next-steps"></a>Következő lépések

Amennyiben még csak most ismerkedik az Azure AD rendszergazdai feladataival, az alapvető információkért tekintse meg [az Azure Active Directory alapjait](https://docs.microsoft.com/azure/active-directory/fundamentals/index) bemutató cikket.

Vagy mindjárt hozzá is láthat [a csoportok létrehozásának](/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context), [a licencek hozzárendelésének](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context), [az alkalmazás-hozzáférések hozzárendelésének](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context) vagy [a rendszergazdai szerepkörök hozzárendelésének](directory-assign-admin-roles.md).
