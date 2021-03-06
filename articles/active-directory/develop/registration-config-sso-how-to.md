---
title: Az alkalmazás egyszeri bejelentkezésének konfigurálása | Microsoft Docs
description: Az egyszeri bejelentkezés konfigurálása egy olyan egyéni alkalmazáshoz, amelyet Ön fejleszt és regisztrál az Azure AD-ben.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: bb77f376e22428e9259ff3efc84cf6f1cb3491fe
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702641"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Egyszeri bejelentkezés konfigurálása alkalmazáshoz

Az összevont egyszeri bejelentkezés (SSO) engedélyezése az alkalmazásban automatikusan engedélyezve van, ha az egyesítő az OpenID Connect, az SAML 2,0 vagy a WS-fed használatával végzi az Azure AD-t. Ha a végfelhasználóknak be kell jelentkezniük annak ellenére, hogy már rendelkezik egy meglévő munkamenettel az Azure AD-vel, előfordulhat, hogy az alkalmazás hibásan van konfigurálva.

* Ha ADAL/MSAL használ, győződjön meg arról, hogy a **PromptBehavior** beállítása **automatikus** , és nem **mindig**.

* Ha Mobile-alkalmazást készít, további konfigurációkra lehet szükség a közvetített vagy nem felügyelt egyszeri bejelentkezés engedélyezéséhez.

Android esetén lásd: a [Cross app SSO engedélyezése Androidon](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

IOS esetén lásd: a [Cross app SSO engedélyezése iOS-ben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Következő lépések

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[A Cross app SSO engedélyezése Androidon](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[A Cross app SSO engedélyezése iOS-ben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Alkalmazások integrálása a AzureAD-be](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[A AzureAD 2.0-val konvergált alkalmazások beleegyezése és engedélyezése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
