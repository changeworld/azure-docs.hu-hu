---
title: Regisztrálás az Office 365-be Azure-fiókkal | Microsoft Docs
description: Tudnivalók Office 365-előfizetés létrehozásáról egy Azure-fiók használatával
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: banders
ms.openlocfilehash: b38800a990056d7de0b6978573ddc19c80503a1d
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199925"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Regisztrálás Office 365-előfizetésre Azure-fiókkal
Ha Ön Azure-előfizető, akkor Azure-fiókjával regisztrálhat Office 365-előfizetésre. Ha Ön egy Azure-előfizetéssel rendelkező szervezet tagja, akkor a meglévő Azure Active Directory (Azure AD) felhasználói számára létrehozhat Office 365-előfizetéseket. Olyan fiókkal regisztráljon az Office 365-be, amely globális rendszergazdai vagy számlázási rendszergazdai engedélyekkel rendelkezik az Azure Active Directory-bérlőben. További információkért lásd [az Azure AD-ben a fiókengedélyek ellenőrzését](#RoleInAzureAD) és [az Azure Active Directoryban a rendszergazdai szerepkörök hozzárendelését](../../active-directory/users-groups-roles/directory-assign-admin-roles.md) ismertető cikket.

Ha már Office 365-fiókkal és Azure-előfizetéssel is rendelkezik, [hozzárendelhet egy Office 365-bérlőt az Azure-előfizetéshez](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Office 365-előfizetés beszerzése az Azure-fiók használatával

1. Lépjen az [Office 365 termékoldalára](https://products.office.com/business), és válasszon ki egy csomagot.
2. Az oldal jobb felső sarkában kattintson a **Bejelentkezés** lehetőségre.

    ![képernyőkép az Office 365-próbaverzió oldaláról](./media/azure-account-for-office-365-subscription/12-office-365-trial-page.png)
3. Jelentkezzen be Azure-fiókja hitelesítő adataival. Ha a szervezet számára hoz létre előfizetést, használjon egy olyan Azure-fiókot, amely a globális rendszergazdai vagy számlázási rendszergazdai címtárszerepkörrel rendelkezik az Azure Active Directory-bérlőben.

    ![Képernyőkép az Office 365-bejelentkezésről](./media/azure-account-for-office-365-subscription/13-office-365-sign-in.png)
4. Kattintson a **Kipróbálás** lehetőségre.

    ![Képernyőkép az Office 365 megrendelésének megerősítéséről.](./media/azure-account-for-office-365-subscription/14-office-365-confirm-your-order.png)
5. A rendelés visszaigazolási oldalán kattintson a **Folytatás** lehetőségre.

    ![Az Office 365 rendelési visszaigazolásának képernyőképe](./media/azure-account-for-office-365-subscription/15-office-365-order-receipt.png)

Ezzel készen is van.
Ha létrehozta az Office 365-előfizetést a szervezet számára, az alábbi lépésekkel ellenőrizheti, hogy az Azure AD-felhasználók már megtalálhatók-e az Office 365-ben.

1. Nyissa meg a Microsoft 365 Felügyeleti központot.
2. Bontsa ki a **FELHASZNÁLÓK** területet, majd kattintson az **Aktív felhasználók** lehetőségre.

    ![Képernyőkép a Microsoft 365 Felügyeleti központ felhasználókat megjelenítő területéről](./media/azure-account-for-office-365-subscription/16-microsoft-365-admin-center-users.png)

A regisztráció után az Office 365-előfizetés ugyanahhoz az Azure Active Directory-példányhoz lesz hozzáadva, amelyhez az Azure-előfizetés tartozik. További információkért lásd [az Azure- és az Office 365-előfizetésekkel kapcsolatos további tudnivalókat](office-365-account-for-azure-subscription.md#more-about-subs) és [az Azure-előfizetések az Azure Active Directoryhoz társítását](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) ismertető cikket.

## <a id="RoleInAzureAD"></a>Fiókengedélyek ellenőrzése az Azure AD-ben
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **Minden szolgáltatás** elemre, majd keresse meg az **Active Directoryt**.

    ![Képernyőkép az Active Directoryról az Azure Portalon](./media/azure-account-for-office-365-subscription/billing-more-services-active-directory.png)
3. Kattintson a **Felhasználók és csoportok** > **Minden felhasználó** lehetőségre.
4. Válassza ki a felhasználónevet.

    ![Képernyőkép az Azure Active Directory-felhasználókról](./media/azure-account-for-office-365-subscription/billing-users-groups.png)

5. Kattintson a **Címtárszerepkör** lehetőségre.

    ![Képernyőkép az Azure Portalon található címtárszerepkörről](./media/azure-account-for-office-365-subscription/billing-user-directory-role.png)
6.  Az Office 365-előfizetés meglévő Azure Active Directory-felhasználók számára történő létrehozásához a **Globális rendszergazda** vagy a **Korlátozott rendszergazda** > **Számlázási adminisztrátor** szerepkörre van szükség.

    ![Képernyőkép az Azure Portalon található Számlázási adminisztrátor címtárszerepkörről](./media/azure-account-for-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
