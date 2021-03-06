---
title: Alkalmazás elrejtése a felhasználói felületről az Azure AD-ben
description: Alkalmazások elrejtése a felhasználói felületről Azure Active Directory hozzáférési panelek vagy Office 365-indítók esetében.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: be29f51771e24c67a8cd99a81e6a69be830dacb8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970635"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Alkalmazások elrejtése a Azure Active Directoryban lévő végfelhasználók számára

Útmutató a végfelhasználók MyApps paneljén vagy az Office 365-Indítóban található alkalmazások elrejtéséhez. Ha egy alkalmazás rejtve van, a felhasználók továbbra is rendelkeznek engedéllyel az alkalmazáshoz. 

## <a name="prerequisites"></a>Előfeltételek

Alkalmazás-rendszergazdai jogosultságok szükségesek az alkalmazások elrejtéséhez a MyApps panelen és az Office 365-Indítóban.

Az Office 365 összes alkalmazásának elrejtéséhez globális rendszergazdai jogosultságok szükségesek.


## <a name="hide-an-application-from-the-end-user"></a>Alkalmazás elrejtése a végfelhasználótól
A következő lépésekkel rejtheti el az alkalmazást a MyApps és az Office 365 Application Launcher használatával.

1.  Jelentkezzen be a [Azure Portalra](https://portal.azure.com) globális rendszergazdaként a címtárában.
2.  Válassza az **Azure Active Directory** elemet.
3.  Válassza a **vállalati alkalmazások**lehetőséget. A **vállalati alkalmazások – minden alkalmazás** panel nyílik meg.
4.  Az **alkalmazás típusa**területen válassza a **vállalati alkalmazások**lehetőséget, ha még nincs kiválasztva.
5.  Keresse meg az elrejteni kívánt alkalmazást, majd kattintson az alkalmazásra.  Megnyílik az alkalmazás áttekintése.
6.  Kattintson a **Tulajdonságok** elemre. 
7.  A **látható felhasználók számára?** kérdés, kattintson a **nem**gombra.
8.  Kattintson a **Save** (Mentés) gombra.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Alkalmazások elrejtése az Azure AD PowerShell használatával

Ha el szeretné rejteni egy alkalmazást a MyApps panelen, manuálisan is hozzáadhatja az HideApp címkét az alkalmazás egyszerű szolgáltatásához. Futtassa az alábbi [AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) -parancsokat, hogy az alkalmazás **látható legyen a felhasználók számára?** tulajdonság értéke **nem**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags.Add("HideApp")
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Office 365-alkalmazások elrejtése a MyApps panelről

A következő lépésekkel elrejtheti az Office 365 összes alkalmazását a MyApps panelről. Az alkalmazások továbbra is láthatók az Office 365-portálon.

1.  Jelentkezzen be a [Azure Portalra](https://portal.azure.com) globális rendszergazdaként a címtárában.
2.  Válassza az **Azure Active Directory** elemet.
3.  Válassza a **Felhasználói beállítások** elemet.
4.  A **vállalati alkalmazások**területen kattintson **a kezelés a végfelhasználók általi indítás és az alkalmazások megtekintése** elemre.
5.  A **felhasználók csak az office 365-alkalmazásokat látják az office 365-portálon**, kattintson az **Igen**gombra.
6.  Kattintson a **Save** (Mentés) gombra.

## <a name="next-steps"></a>Következő lépések
* [Összes saját csoport megjelenítése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](assign-user-or-group-access-portal.md)
* [Felhasználó vagy csoport hozzárendelésének eltávolítása vállalati alkalmazásból](remove-user-or-group-access-portal.md)
* [Vállalati alkalmazás nevének vagy emblémájának módosítása](change-name-or-logo-portal.md)

