---
title: Felhasználói bejelentkezések letiltása egy vállalati alkalmazáshoz az Azure AD-ben
description: Vállalati alkalmazások letiltása, hogy a felhasználók ne jelentkezzenek be Azure Active Directory
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
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10553898376c4b9236ee62718fffccd45b12d70b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274089"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Vállalati alkalmazásokhoz tartozó felhasználói bejelentkezések letiltása Azure Active Directory

A vállalati alkalmazások egyszerűen letilthatók, így egyetlen felhasználó sem tud bejelentkezni Azure Active Directory (Azure AD) szolgáltatásba. A vállalati alkalmazás felügyeletéhez szükséges engedélyek szükségesek. Emellett globális rendszergazdai jogosultsággal kell rendelkeznie a címtárhoz.

## <a name="how-do-i-disable-user-sign-ins"></a>Hogyan letiltani a felhasználói bejelentkezéseket?

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
1. Válassza a **minden szolgáltatás**lehetőséget, írja be **Azure Active Directory** a szövegmezőbe, majd válassza az **ENTER billentyűt**.
1. A **Azure Active Directory** -  ***könyvtárnév*** ablaktáblán (azaz a kezelt CÍMTÁRhoz tartozó Azure ad-ablaktáblán) válassza a **vállalati alkalmazások**lehetőséget.
1. A **vállalati alkalmazások – minden alkalmazás** panelen láthatja a felügyelhető alkalmazások listáját. Válasszon ki egy alkalmazást.
1. A ***AppName*** panelen (azaz a cím alatt a kiválasztott alkalmazás nevét tartalmazó ablaktáblán) válassza a **Tulajdonságok**lehetőséget.
1. A ***appname*** - **Tulajdonságok** ablaktáblán válassza a **nem** lehetőséget, **hogy a felhasználók bejelentkezzenek?** .
1. Kattintson a **Save (Mentés** ) parancsra.

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Lista nélküli alkalmazás letiltása az Azure AD PowerShell használatával

Ha ismeri egy olyan alkalmazás AppId, amely nem jelenik meg a vállalati alkalmazások listáján (például azért, mert törölte az alkalmazást vagy a szolgáltatásnevet még nem hozták létre a Microsoft által előre felhatalmazott alkalmazás miatt), manuálisan is létrehozhatja a szolgáltatáshoz tartozó szolgáltatásnevet az alkalmazást, majd tiltsa le a [AzureAD PowerShell-parancsmag](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0)használatával.

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>További lépések

* [Összes saját csoport megjelenítése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](assign-user-or-group-access-portal.md)
* [Felhasználó vagy csoport hozzárendelésének eltávolítása vállalati alkalmazásból](remove-user-or-group-access-portal.md)
* [Vállalati alkalmazás nevének vagy emblémájának módosítása](change-name-or-logo-portal.md)
