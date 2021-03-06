---
title: Egyéni tartománynevek hozzáadása és ellenőrzése – Azure Active Directory | Microsoft Docs
description: Felügyeleti fogalmak és útmutatók a tartománynevek Azure Active Directoryban való kezeléséhez
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2395aa5984de2a9fe41e4778d16aba69bfef5192
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77559233"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Egyéni tartománynevek kezelése a Azure Active Directoryban

A tartománynév fontos részét képezi az azonosítónak számos címtár-erőforráshoz: ez egy felhasználó felhasználónevének vagy e-mail-címének része, egy csoport címének egy része, és esetenként egy alkalmazáshoz tartozó alkalmazás-azonosító URI része. Azure Active Directory (Azure AD) egyik erőforrása tartalmazhat olyan tartománynevet, amely az erőforrást tartalmazó könyvtár tulajdonában van. Csak globális rendszergazda kezelhet tartományokat az Azure AD-ben.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Az Azure AD-címtár elsődleges tartománynevének beállítása

A címtár létrehozásakor a kezdeti tartománynév (például "contoso.onmicrosoft.com") egyben az elsődleges tartománynév is. Új felhasználó létrehozásakor az elsődleges tartomány az új felhasználó alapértelmezett tartományneve. Az elsődleges tartománynév beállítása egyszerűbbé teszi a rendszergazda számára, hogy új felhasználókat hozzon létre a portálon. Az elsődleges tartomány nevének módosítása:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza az **Azure Active Directory** elemet.
3. Válassza az **Egyéni tartománynevek** elemet.
  
   ![A felhasználó-felügyeleti lap megnyitása](./media/domains-manage/add-custom-domain.png)
4. Válassza ki annak a tartománynak a nevét, amelyet elsődleges tartományként szeretne használni.
5. Válassza az **elsődlegesvé tétele** parancsot. Ha a rendszer kéri, erősítse meg a választását.
  
   ![Tartománynév megadása az elsődlegesnek](./media/domains-manage/make-primary-domain.png)

A címtár elsődleges tartománynevét úgy módosíthatja, hogy bármely ellenőrzött egyéni tartomány legyen, amely nem összevont. A címtár elsődleges tartományának módosítása nem módosítja a meglévő felhasználók felhasználónevét.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Egyéni tartománynevek hozzáadása az Azure AD-bérlőhöz

Legfeljebb 900 felügyelt tartománynevet adhat hozzá. Ha az összes tartományt a helyszíni Active Directory segítségével konfigurálja az összevonáshoz, az egyes címtárakban akár 450 tartománynevet is hozzáadhat.

## <a name="add-subdomains-of-a-custom-domain"></a>Egyéni tartomány altartományának hozzáadása

Ha egy harmadik szintű tartománynevet (például "europe.contoso.com") szeretne hozzáadni a címtárhoz, először fel kell vennie és ellenőriznie kell a második szintű tartományt, például contoso.com. Az altartományt az Azure AD automatikusan ellenőrzi. Ha szeretné látni, hogy a hozzáadott altartomány ellenőrzése megtörtént, frissítse a tartomány listáját a böngészőben.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Mi a teendő, ha megváltoztatja a DNS-regisztrálót az egyéni tartománynévhez

Ha megváltoztatja a DNS-regisztrálókat, az Azure AD-ben nincsenek további konfigurációs feladatok. A tartománynevet továbbra is használhatja megszakítás nélkül az Azure AD-vel. Ha az egyéni tartománynevet az Office 365, az Intune vagy más, egyéni tartományneveket használó szolgáltatásokkal használja az Azure AD-ben, tekintse meg a szolgáltatások dokumentációját.

## <a name="delete-a-custom-domain-name"></a>Egyéni tartománynév törlése

Törölheti az egyéni tartománynevet az Azure AD-ből, ha a szervezet már nem használja ezt a tartománynevet, vagy ha ezt a tartománynevet egy másik Azure AD-vel kell használnia.

Egyéni tartománynév törléséhez először gondoskodnia kell arról, hogy a címtárban lévő erőforrások ne legyenek a tartománynévre támaszkodva. A tartománynevet nem lehet törölni a címtárból, ha:

* Minden felhasználó rendelkezik egy olyan felhasználónévvel, e-mail-címmel vagy proxy címmel, amely tartalmazza a tartománynevet.
* Bármely csoport rendelkezik egy e-mail-címmel vagy proxy címmel, amely tartalmazza a tartománynevet.
* Az Azure AD-ban található bármely alkalmazáshoz tartozik egy, a tartománynevet tartalmazó alkalmazás-azonosító URI.

Az Egyéni tartománynév törlése előtt módosítania vagy törölnie kell az összes ilyen erőforrást az Azure AD-címtárban.

### <a name="forcedelete-option"></a>ForceDelete beállítás

A **ForceDelete** az [Azure ad felügyeleti központban](https://aad.portal.azure.com) vagy [Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)-val is felhasználhatja. Ezek a beállítások egy aszinkron műveletet használnak, és az egyéni tartománynévtől (például "user@contoso.com") származó összes hivatkozást frissítik a kezdeti alapértelmezett tartománynévre, például a "user@contoso.onmicrosoft.com" névre. 

A **ForceDelete** meghívásához a Azure Portalban meg kell győződnie arról, hogy a tartománynévnek kevesebb, mint 1000 hivatkozása van, és az Exchange [felügyeleti központban](https://outlook.office365.com/ecp/)frissíteni vagy törölni kell a kiépítési szolgáltatást. Ez magában foglalja az Exchange-levelezésre képes biztonsági csoportokat és az elosztott listát; További információ: [levelezésre képes biztonsági csoportok eltávolítása](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups). Emellett a **ForceDelete** művelet nem fog sikerülni, ha a következők egyike igaz:

* Az Office 365 tartományi előfizetési szolgáltatásokon keresztül vásárolt egy tartományt
* Ön egy másik ügyfél bérlője nevében felügyelt partner

A **ForceDelete** művelet részeként a következő műveletek végezhetők el:

* Átnevezi a felhasználók UPN-, EmailAddress-és ProxyAddress, amelyek az egyéni tartománynévre mutató hivatkozásokat tartalmaznak a kezdeti alapértelmezett tartomány nevére.
* Átnevezi a csoportok EmailAddress értékeit az egyéni tartománynévre mutató hivatkozásokkal a kezdeti alapértelmezett tartománynévre.
* Átnevezi az egyéni tartománynévre mutató hivatkozásokat tartalmazó identifierUris a kezdeti alapértelmezett tartománynévre.

A rendszer a következő hibaüzenetet adja vissza:

* Az átnevezni kívánt objektumok száma nagyobb, mint 1000
* A átnevezni kívánt alkalmazások közül egy több-bérlős alkalmazás

### <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Miért nem sikerül a tartomány törlése olyan hibával, amely azt jelzi, hogy az Exchange-főkiszolgáló ezen a tartománynévn van?** <br>
**A:** Napjainkban bizonyos csoportok, például a levelezésre képes biztonsági csoportok és az elosztott listák az Exchange-ben vannak kiépítve, és manuálisan kell tisztítani az [Exchange felügyeleti központban (EAC)](https://outlook.office365.com/ecp/). Előfordulhat, hogy a ProxyAddresses az egyéni tartománynévre támaszkodik, és manuálisan kell frissítenie egy másik tartománynévre. 

**K: rendszergazdai\@contoso.com vagyok bejelentkezve, de nem tudom törölni a "contoso.com" nevű tartománynevet?**<br>
**A:** A törölni kívánt egyéni tartománynév nem hivatkozhat a felhasználói fiók nevében. Győződjön meg arról, hogy a globális rendszergazdai fiók a kezdeti alapértelmezett tartománynevet (. onmicrosoft.com) használja, például admin@contoso.onmicrosoft.com. Jelentkezzen be egy másik globális rendszergazdai fiókkal, például admin@contoso.onmicrosoft.com vagy egy másik egyéni tartománynévvel, például a "fabrikam.com" névvel, ahol a fiók admin@fabrikam.com.

**K: rákattintok a tartomány törlése gombra, és megtekinthető a törlési művelet `In Progress` állapota. Mennyi ideig tart? Mi történik, ha nem sikerül?**<br>
**A:** A tartomány törlése művelet egy aszinkron háttérbeli feladat, amely átnevezi az összes hivatkozást a tartománynévre. Egy-két percen belül elvégezhető. Ha a tartomány törlése sikertelen, győződjön meg arról, hogy nincs:

* A tartománynévre konfigurált alkalmazások a appIdentifierURI
* Minden olyan levelezési csoport, amely az egyéni tartomány nevére hivatkozik
* Több mint 1000 hivatkozás a tartománynévre

Ha úgy látja, hogy a feltételek bármelyike nem teljesült, manuálisan törölje a hivatkozásokat, és próbálja meg újból törölni a tartományt.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>Tartománynevek kezelése a PowerShell vagy a Microsoft Graph API használatával

Azure Active Directory a tartománynevek legtöbb felügyeleti feladata a Microsoft PowerShell használatával vagy programozott módon, a Microsoft Graph API használatával is elvégezhető.

* [Tartománynevek kezelése a PowerShell használatával az Azure AD-ben](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#domains)
* [Tartományi erőforrás típusa](https://docs.microsoft.com/graph/api/resources/domain?view=graph-rest-1.0)

## <a name="next-steps"></a>További lépések

* [Egyéni tartománynevek hozzáadása](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Exchange-levelezésre képes biztonsági csoportok eltávolítása az Exchange felügyeleti központban az Azure AD-beli egyéni tartománynévben](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [ForceDelete egy egyéni tartománynevet Microsoft Graph API-val](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
