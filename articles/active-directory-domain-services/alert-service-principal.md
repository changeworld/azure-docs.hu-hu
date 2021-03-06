---
title: Egyszerű szolgáltatásokkal kapcsolatos riasztások feloldása Azure AD Domain Servicesban | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a Azure Active Directory Domain Services egyszerű szolgáltatásnév konfigurációs riasztásait
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: iainfou
ms.openlocfilehash: 175bfe63176b78c5aeafc7147c46dd5ab1110325
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257959"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Ismert problémák: Egyszerű szolgáltatások riasztásai a Azure Active Directory Domain Services

Az [egyszerű szolgáltatások](../active-directory/develop/app-objects-and-service-principals.md) olyan alkalmazások, amelyeket az Azure platform egy Azure AD DS felügyelt tartomány felügyeletére, frissítésére és karbantartására használ. Ha töröl egy egyszerű szolgáltatást, a rendszer hatással van az Azure AD DS felügyelt tartományában lévő funkciókra.

Ez a cikk segítséget nyújt a szolgáltatással kapcsolatos egyszerű konfigurációs riasztások hibaelhárításához és megoldásához.

## <a name="alert-aadds102-service-principal-not-found"></a>Riasztás AADDS102: Az egyszerű szolgáltatásnév nem található

### <a name="alert-message"></a>Riasztási üzenet

*Az Azure AD-címtárból törölte a Azure AD Domain Services megfelelő működéséhez szükséges egyszerű szolgáltatást. Ez a konfiguráció befolyásolja a Microsoft képességét a felügyelt tartomány figyelésére, kezelésére, javítására és szinkronizálására.*

Ha egy szükséges szolgáltatásnevet töröl, az Azure platform nem képes automatizált felügyeleti feladatok végrehajtására. Előfordulhat, hogy az Azure AD DS felügyelt tartománya nem alkalmazza megfelelően a frissítéseket, és nem készít biztonsági mentést.

### <a name="check-for-missing-service-principals"></a>Hiányzó egyszerű szolgáltatások keresése

Az alábbi lépések végrehajtásával ellenőrizheti, hogy melyik egyszerű szolgáltatásnév hiányzik, és hogy újra létre kell-e hozni.

1. A Azure Portal válassza a bal oldali navigációs menü **Azure Active Directory** elemét.
1. Válassza a **vállalati alkalmazások**lehetőséget. Válassza a *minden alkalmazás* lehetőséget az **alkalmazás típusa** legördülő menüben, majd válassza az **alkalmaz**lehetőséget.
1. Keresse meg az egyes alkalmazás-azonosítókat. Ha nem található meglévő alkalmazás, kövesse a *megoldás* lépéseit az egyszerű szolgáltatásnév létrehozásához, vagy regisztrálja újra a névteret.

    | Alkalmazásazonosító | Megoldás: |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Hiányzó egyszerű szolgáltatásnév újbóli létrehozása](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Regisztrálja újra a Microsoft. HRE névteret](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Regisztrálja újra a Microsoft. HRE névteret](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Regisztrálja újra a Microsoft. HRE névteret](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Hiányzó egyszerű szolgáltatásnév újbóli létrehozása

Ha az alkalmazás-azonosító *2565bd9d-DA50-47d4-8B85-4c97f669dc36* hiányzik az Azure ad-címtárból, az Azure ad PowerShell használatával hajtsa végre az alábbi lépéseket. További információ: az [Azure ad PowerShell telepítése](/powershell/azure/active-directory/install-adv2).

1. Telepítse az Azure AD PowerShell-modult, és importálja a következő módon:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Most hozza létre újra a szolgáltatásnevet a [New-azureadserviceprincipal parancsmagot][New-AzureAdServicePrincipal] parancsmag használatával:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

Az Azure AD DS felügyelt tartomány állapota két órán belül automatikusan frissül, és eltávolítja a riasztást.

### <a name="re-register-the-microsoft-aad-namespace"></a>Regisztrálja újra a Microsoft HRE-névteret

Ha az alkalmazás-azonosító *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-947a-dc74e5d09022*vagy *d87dcbc6-a371-462e-88e3-28ad15ec4e64* hiányzik az Azure ad-címtárból, hajtsa végre a következő lépéseket a regisztrálja újra a *Microsoft. HRE* erőforrás-szolgáltatót:

1. A Azure Portal keresse meg és válassza ki az **előfizetések**elemet.
1. Válassza ki az Azure AD DS felügyelt tartományához társított előfizetést.
1. A bal oldali navigációs sávon válassza az **erőforrás-szolgáltatók**lehetőséget.
1. Keressen rá a *Microsoft. HRE*kifejezésre, majd válassza az **újbóli regisztráció**lehetőséget.

Az Azure AD DS felügyelt tartomány állapota két órán belül automatikusan frissül, és eltávolítja a riasztást.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Riasztás AADDS105: A jelszó-szinkronizálási alkalmazás elavult

### <a name="alert-message"></a>Riasztási üzenet

*A "d87dcbc6-a371-462e-88e3-28ad15ec4e64" AZONOSÍTÓJÚ szolgáltatásnevet törölték, majd újból létrehozták. A szabadidő a felügyelt tartomány kiszolgálásához szükséges Azure AD Domain Services erőforrásokon inkonzisztens engedélyek mögött hagy. A felügyelt tartomány jelszavainak szinkronizálása hatással lehet.*

Az Azure AD DS automatikusan szinkronizálja a felhasználói fiókokat és a hitelesítő adatokat az Azure AD-ből. Ha probléma van a folyamathoz használt Azure AD-alkalmazással, a hitelesítő adatok szinkronizálása az Azure AD DS és az Azure AD között meghiúsul.

### <a name="resolution"></a>Megoldás:

A hitelesítő adatok szinkronizálásához használt Azure AD-alkalmazás újbóli létrehozásához használja az Azure AD PowerShellt az alábbi lépések végrehajtásához. További információ: az [Azure ad PowerShell telepítése](/powershell/azure/active-directory/install-adv2).

1. Telepítse az Azure AD PowerShell-modult, és importálja a következő módon:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Most törölje a régi alkalmazást és objektumot a következő PowerShell-parancsmagok használatával:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

Miután mindkét alkalmazást törölte, az Azure platform automatikusan újra létrehozza őket, és megkísérli a jelszó-szinkronizálást. Az Azure AD DS felügyelt tartomány állapota két órán belül automatikusan frissül, és eltávolítja a riasztást.

## <a name="next-steps"></a>További lépések

Ha továbbra is problémákba ütközik, [Nyisson meg egy Azure-támogatási kérést][azure-support] további hibaelhárítási segítségért.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
