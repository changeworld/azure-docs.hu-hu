---
title: Munkahelyi vagy iskolai fiók lezárása nem felügyelt Azure AD-címtárban
description: Munkahelyi vagy iskolai fiók lezárása nem felügyelt Azure Active Directoryban.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c101c0ef7932151e675c5c514ac558e6e0f94b2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73815722"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Munkahelyi vagy iskolai fiók lezárása nem felügyelt címtárban

Ha nem felügyelt Azure Active Directory (Azure AD) szervezet felhasználója, és már nem kell használnia az adott szervezettől származó alkalmazásokat, vagy nem kívánja fenntartani a társítást, bármikor lezárhatja a fiókját. A nem felügyelt címtár nem rendelkezik globális rendszergazdával. A nem felügyelt címtárban lévő felhasználók saját fiókjaikat a rendszergazdához való kapcsolatfelvétel nélkül is lezárják.

A nem felügyelt címtárban lévő felhasználók gyakran jönnek létre az önkiszolgáló regisztráció során. Ilyen lehet például egy olyan adatfeldolgozó, amely egy ingyenes szolgáltatásra regisztrál. Az önkiszolgáló regisztrációval kapcsolatos további információkért lásd: [Mi az a Azure Active Directory önkiszolgáló regisztrációja?](directory-self-service-signup.md)

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Előkészületek

A fiók lezárása előtt ellenőrizze a következő elemeket:

* Győződjön meg arról, hogy egy nem felügyelt Azure AD-címtár felhasználója. A fiók nem zárható be, ha egy felügyelt címtárhoz tartozik. Ha egy felügyelt címtárhoz tartozik, és be szeretné állítani a fiókját, forduljon a rendszergazdához. További információ arról, hogyan állapítható meg, hogy egy nem felügyelt címtárhoz tartozik-e: [a felhasználó törlése nem felügyelt bérlőről](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Mentse a megőrizni kívánt összes adatfájlt. További információ az exportálási kérelmek beküldéséről: a nem [felügyelt bérlők számára a rendszer által létrehozott naplók elérése és exportálása](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> A fiók bezárása visszafordíthatatlan. Ha bezárta a fiókját, az összes személyes adatait eltávolítja a rendszer. Többé nem fog hozzáférni fiókjához és a fiókjához tartozó adataihoz.

## <a name="close-your-account"></a>Fiók lezárása

Nem felügyelt munkahelyi vagy iskolai fiók bezárásához kövesse az alábbi lépéseket:

1. A [fiók bezárásához](https://go.microsoft.com/fwlink/?linkid=873123)jelentkezzen be a fiókjába.

1. **Az adatkérések**területen válassza a **fiók lezárása**lehetőséget.

    ![Adatkérések – fiók lezárása](./media/users-close-account/close-account.png)

1. Tekintse át a megerősítő üzenetet, majd válassza az **Igen**lehetőséget.

    ![Adatkérések – Bezárás megerősítése](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>További lépések

- [Mi a Azure Active Directory önkiszolgáló regisztrációja?](directory-self-service-signup.md)
- [A felhasználó törlése a nem felügyelt Bérlőből](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Rendszer által létrehozott naplók elérése és exportálása nem felügyelt bérlők számára](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
