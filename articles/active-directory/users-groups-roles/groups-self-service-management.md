---
title: Önkiszolgáló csoport felügyeletének beállítása – Azure Active Directory | Microsoft Docs
description: Biztonsági vagy Office 365-csoportok létrehozása és kezelése az Azure Active Directoryban, valamint biztonsági és Office 365-csoporttagság igénylése
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e52c37e293941a767621cf56ef75f8cc83b1925
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298003"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Önkiszolgáló csoport felügyeletének beállítása Azure Active Directory 

Lehetővé teheti a felhasználók számára, hogy saját biztonsági csoportokat vagy Office 365-csoportokat hozzanak létre és kezeljenek Azure Active Directoryban (Azure AD). A csoport tulajdonosa jóváhagyhatja vagy megtagadhatja a tagsági kérelmeket, és delegálhatja a csoporttagság vezérlését. Az önkiszolgáló csoport felügyeleti funkciói nem érhetők el levelezésre képes biztonsági csoportokhoz vagy terjesztési listához.

## <a name="self-service-group-membership-defaults"></a>Önkiszolgáló csoporttagság alapértelmezett értékei

Ha a biztonsági csoportokat a Azure Portal vagy az Azure AD PowerShell használatával hozza létre, akkor csak a csoport tulajdonosai frissíthetik a tagságot. Az önkiszolgáló szolgáltatással létrehozott biztonsági csoportok a [hozzáférési panelen](https://account.activedirectory.windowsazure.com/r#/joinGroups) és az összes Office 365-csoport csatlakozhat az összes felhasználóhoz, akár tulajdonos által jóváhagyott, akár automatikusan jóváhagyva. A hozzáférési panelen módosíthatja a tagsági beállításokat a csoport létrehozásakor.

Létrehozott csoportok | Biztonsági csoport alapértelmezett viselkedése | Office 365-csoport alapértelmezett viselkedése
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Csak a tulajdonosok adhatnak hozzá tagokat<br>Látható, de nem érhető el a hozzáférési panelen való csatlakozáshoz | Megnyitás az összes felhasználóhoz való csatlakozáshoz
[Azure Portalra](https://portal.azure.com) | Csak a tulajdonosok adhatnak hozzá tagokat<br>Látható, de nem érhető el a hozzáférési panelen való csatlakozáshoz<br>A tulajdonos nincs automatikusan hozzárendelve a csoport létrehozásakor | Megnyitás az összes felhasználóhoz való csatlakozáshoz
[Hozzáférési panel](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Megnyitás az összes felhasználóhoz való csatlakozáshoz<br>A tagsági beállítások megváltoztathatók a csoport létrehozásakor | Megnyitás az összes felhasználóhoz való csatlakozáshoz<br>A tagsági beállítások megváltoztathatók a csoport létrehozásakor

## <a name="self-service-group-management-scenarios"></a>Önkiszolgáló csoportos felügyeleti forgatókönyvek

* **Delegált csoportkezelés** – Jellemző példa rá egy olyan rendszergazda, aki a vállalata által használt SaaS-alkalmazás hozzáférésének kezelését végzi. E hozzáférési jogosultságok kezelése nehézkessé válik, így a rendszergazda új csoport létrehozására kéri fel a vállalat tulajdonosát. A rendszergazda hozzárendeli a hozzáférést az alkalmazáshoz az új csoporthoz, és hozzáadja a csoportot az alkalmazáshoz már hozzáférő összes személyhez. A vállalat tulajdonosa ezt követően további felhasználókat vehet fel a csoportba, akik automatikusan hozzáférést kapnak az alkalmazáshoz. A vállalat tulajdonosának nem kell a rendszergazdára várnia a felhasználók hozzáférésének kezeléséhez. Ha a rendszergazda ugyanezt az engedélyt egy másik üzleti csoportban lévő felettesnek adja meg, akkor a felhasználó a saját csoporttagok számára is kezelheti a hozzáférést. Sem az üzleti tulajdonos, sem a felettes nem tekintheti meg és nem kezelheti egymás csoportjának tagságait. A rendszergazda továbbra is megtekintheti az alkalmazáshoz hozzáféréssel rendelkező összes felhasználó listáját, és blokkolhatja is a hozzáférést, ha szükséges.
* **Önkiszolgáló csoportkezelés** – Jellemző példa rá két olyan felhasználó, akik egyaránt rendelkeznek egymástól függetlenül üzembe helyezett SharePoint Online-webhelyekkel. Szeretnének egymás csapatának hozzáférést adni a webhelyükhöz. Ennek megvalósítása érdekében létrehozhatnak egy csoportot az Azure AD-ben, majd a SharePoint Online felületén mindketten kiválaszthatják ezt a csoportot, így biztosítva hozzáférést a webhelyeikhez. Ha valaki hozzáférést igényel, azt a hozzáférési panelen igényelheti, és ha kérését jóváhagyják, automatikusan hozzáférést kap mindkét SharePoint Online-webhelyhez. Ezt követően egyikük dönthet úgy, hogy a webhelyhez hozzáféréssel rendelkező összes felhasználó számára egy adott SaaS-alkalmazáshoz is hozzáférést ad. A SaaS-alkalmazás rendszergazdája adhat hozzáférési jogot a SharePoint Online webhelyhez tartozó alkalmazáshoz. Ettől kezdve az elfogadott kérések mindkét SharePoint Online-webhelyhez és az SaaS-alkalmazáshoz is hozzáférést biztosítanak.

## <a name="make-a-group-available-for-user-self-service"></a>Csoport elérhetővé tétele önkiszolgáló felhasználói tevékenységhez

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
1. Válassza a **csoportok**lehetőséget, majd válassza az **általános** beállítások lehetőséget.
1. **A tulajdonosok beállítása a hozzáférési panelen a csoporttagság-kérelmeket** **Igen**értékre állíthatja.
1. **A hozzáférési panelen a csoportok hozzáférésének korlátozása a** **nem**értékre.
1. Ha beállítja, hogy **a felhasználók létrehozhatnak biztonsági csoportokat az Azure-portálokon** , vagy a **felhasználók Office 365-csoportokat hozhatnak létre az Azure** Portalon

    - **Igen**: az Azure ad-szervezetben lévő összes felhasználó létrehozhat új biztonsági csoportokat, és tagokat vehet fel ezekbe a csoportokba. Ezek az új csoportok szintén valamennyi felhasználó számára láthatóvá válnak a hozzáférési panelen. Ha a csoport házirend-beállítása lehetővé teszi, más felhasználók is létrehozhatnak kérelmeket a csoportokhoz való csatlakozáshoz
    - **Nem**: a felhasználók nem hozhatnak létre csoportokat, és nem módosíthatják azokat a meglévő csoportokat, amelyeknek tulajdonosai. Ugyanakkor továbbra is kezelhetik az ilyen csoportok tagságát, és elfogadhatják más felhasználók csoportfelvételi kéréseit.

Olyan tulajdonosokat is használhat, **akik csoport tulajdonosaként rendelnek hozzá tagokat az Azure** Portalon és a **tulajdonosokat, akik a tagokat az Azure** Portalon szeretnék hozzárendelni, így részletesebb hozzáférés-vezérlést biztosíthatnak a felhasználók önkiszolgáló csoportjának felügyeletéhez.

Ha a felhasználók létrehozhatnak csoportokat, a szervezet minden felhasználója létrehozhat új csoportokat, majd az alapértelmezett tulajdonosként hozzáadhat tagokat ezekhez a csoportokhoz. Nem adhat meg olyan személyeket, akik létrehozhatják saját csoportjaikat. Csak olyan személyeket adhat meg, akik egy csoport tulajdonosának egy másik csoport tagjává teszik.

## <a name="next-steps"></a>További lépések

E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforrások hozzáférésének kezelése Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](groups-settings-cmdlets.md)
* [Alkalmazáskezelés az Azure Active Directory használatával](../manage-apps/what-is-application-management.md)
* [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
