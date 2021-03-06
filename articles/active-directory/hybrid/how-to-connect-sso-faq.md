---
title: 'Azure AD Connect: zökkenőmentes egyszeri bejelentkezés – gyakori kérdések | Microsoft Docs'
description: Válaszok az Azure Active Directory zökkenőmentes egyszeri bejelentkezéssel kapcsolatos gyakori kérdésekre.
services: active-directory
keywords: Mi az Azure AD Connect, az Azure AD, az egyszeri bejelentkezéshez szükséges összetevők telepítése Active Directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7241c8dfbedb24f95c29ea9e1c3f763218a5668d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025677"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory zökkenőmentes egyszeri bejelentkezés: gyakori kérdések

Ebben a cikkben a zökkenőmentes egyszeri bejelentkezéssel (a zökkenőmentes SSO-val) kapcsolatos gyakori kérdéseket tárgyaljuk Azure Active Directory. Az új tartalom ellenőrzésének visszatartása.

**K: milyen bejelentkezési módszerek használhatók a zökkenőmentes egyszeri bejelentkezéshez**

A zökkenőmentes egyszeri bejelentkezést a jelszó- [kivonatoló szinkronizálással](how-to-connect-password-hash-synchronization.md) vagy az [átmenő hitelesítési](how-to-connect-pta.md) bejelentkezési módszerekkel kombinálhatja. Ez a funkció azonban nem használható Active Directory összevonási szolgáltatások (AD FS) (ADFS) használatával.

**K: a zökkenőmentes egyszeri bejelentkezés ingyenes funkció?**

A zökkenőmentes egyszeri bejelentkezés ingyenes szolgáltatás, és nincs szüksége az Azure AD fizetős kiadásaira a használatához.

**K: a [Microsoft Azure Germany felhőben](https://www.microsoft.de/cloud-deutschland) és a [Microsoft Azure Government felhőben](https://azure.microsoft.com/features/gov/)is elérhető a zökkenőmentes egyszeri bejelentkezés?**

Nem. A zökkenőmentes egyszeri bejelentkezés csak az Azure AD világméretű példányában érhető el.

**K: milyen alkalmazások kihasználják a zökkenőmentes egyszeri bejelentkezés `domain_hint` vagy `login_hint` paraméterét?**

Az alábbiakban felsoroljuk azokat az alkalmazásokat, amelyek elküldhetik ezeket a paramétereket az Azure AD-be, így a felhasználók csendes bejelentkezési élményt biztosítanak a zökkenőmentes egyszeri bejelentkezés (azaz nem szükséges, hogy a felhasználók be tudják írni a felhasználóneveket vagy a jelszavukat):

| Alkalmazásnév | Használandó alkalmazás-URL-cím |
| -- | -- |
| Hozzáférési panel | https:\//myapps.microsoft.com/contoso.com |
| Webes Outlook | https:\//outlook.office365.com/contoso.com |
| Office 365 portálok | https:\//portal.office.com?domain_hint=contoso.com, https:\//www.office.com?domain_hint=contoso.com |

Emellett a felhasználók csendes bejelentkezési élményt kapnak, ha egy alkalmazás a beérkező kéréseket az Azure AD-ba bérlőként beállított végpontokra küldi, vagyis https:\//login.microsoftonline.com/contoso.com/<. > vagy https:\//login.microsoftonline.com/<tenant_ID>/<.. > – Az Azure AD közös végpontja helyett – vagyis https:\//login.microsoftonline.com/common/<... >. Az alábbi lista az ilyen típusú bejelentkezési kérelmeket elvégező alkalmazások nem teljes listáját tartalmazza.

| Alkalmazásnév | Használandó alkalmazás-URL-cím |
| -- | -- |
| SharePoint Online | https:\//contoso.sharepoint.com |
| Azure Portal | https:\//portal.azure.com/contoso.com |

A fenti táblázatokban cserélje le a "contoso.com" kifejezést a tartománynevére, hogy a bérlőnek megfelelő alkalmazás URL-címei legyenek.

Ha azt szeretné, hogy más alkalmazások is csendes bejelentkezési élményt használjanak, tudassa velünk a visszajelzések szakaszban.

**K: a zökkenőmentes egyszeri bejelentkezés támogatása a Felhasználónév helyett a `Alternate ID` `userPrincipalName`?**

Igen. A zökkenőmentes egyszeri bejelentkezés támogatja a `Alternate ID` felhasználónevet, ha Azure AD Connect konfigurálja az [itt](how-to-connect-install-custom.md)látható módon. Nem minden Office 365-alkalmazás támogatja a `Alternate ID`. Tekintse meg az adott alkalmazás dokumentációját a támogatási nyilatkozathoz.

**K: mi a különbség az [Azure ad JOIN](../active-directory-azureadjoin-overview.md) és a zökkenőmentes SSO által nyújtott egyszeri bejelentkezés?**

Az [Azure ad JOIN](../active-directory-azureadjoin-overview.md) egyszeri bejelentkezést biztosít a felhasználók számára, ha az eszközük regisztrálva van az Azure ad-ben. Ezeket az eszközöket nem feltétlenül kell tartományhoz csatlakoztatni. Az egyszeri bejelentkezés az *elsődleges frissítési tokenekkel* vagy a *PRT*-vel van megadva, és nem Kerberos. A Windows 10-es eszközökön a felhasználói élmény a legoptimálisabb. Az egyszeri bejelentkezés automatikusan megtörténik a Microsoft Edge böngészőben. Emellett a Chrome-ban is működik egy böngésző-bővítmény használatával.

Az Azure AD Joint és a zökkenőmentes egyszeri bejelentkezést is használhatja a bérlőn. Ez a két funkció kiegészíti egymást. Ha mindkét funkció be van kapcsolva, az Azure AD JOIN szolgáltatásból származó egyszeri bejelentkezés elsőbbséget élvez a zökkenőmentes egyszeri bejelentkezéssel szemben.

**K: nem Windows 10 rendszerű eszközöket kívánok regisztrálni az Azure AD-vel, AD FS használata nélkül. Használhatom a zökkenőmentes egyszeri bejelentkezést?**

Igen, ehhez a forgatókönyvhöz a [munkahelyi csatlakoztatási ügyfél 2,1-](https://www.microsoft.com/download/details.aspx?id=53554)es vagy újabb verziójára van szükség.

**K: Hogyan lehet átvenni a `AZUREADSSOACC` számítógépfiók Kerberos-visszafejtési kulcsát?**

Fontos, hogy a helyszíni AD-erdőben létrehozott `AZUREADSSOACC` számítógépfiók (amely az Azure AD-t jelképező) Kerberos-visszafejtési kulcsát gyakran átadja.

>[!IMPORTANT]
>Javasoljuk, hogy legalább 30 naponként átadja a Kerberos-visszafejtési kulcsot.

Kövesse az alábbi lépéseket azon a helyszíni kiszolgálón, amelyen a Azure AD Connect futtatja:

   **1. lépés. Azon AD-erdők listájának beolvasása, amelyeken engedélyezve van a zökkenőmentes egyszeri bejelentkezés**

   1. Először töltse le és telepítse az [Azure ad PowerShellt](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
   3. Importálja a zökkenőmentes SSO PowerShell-modult a következő parancs használatával: `Import-Module .\AzureADSSO.psd1`.
   4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja meg a `New-AzureADSSOAuthenticationContext`. Ennek a parancsnak meg kell adnia egy előugró ablakban, hogy megadja a bérlő globális rendszergazdai hitelesítő adatait.
   5. Hívja meg `Get-AzureADSSOStatus | ConvertFrom-Json`. Ez a parancs az AD-erdők listáját tartalmazza (tekintse meg a "tartományok" listát), amelyen a funkció engedélyezve van.

   **2. lépés. Frissítse a Kerberos-visszafejtési kulcsot minden olyan AD-erdőben, amelyen be lett állítva**

   1. Hívja meg `$creds = Get-Credential`. Ha a rendszer kéri, adja meg a kívánt Active Directory-erdő tartományi rendszergazdai hitelesítő adatait.

   > [!NOTE]
   >A tartományi rendszergazdai hitelesítő adatok felhasználónevet a SAM-fiók nevének formátumában kell megadni (contoso\johndoe vagy contoso. com\johndoe). A Felhasználónév tartomány részeként a tartományi rendszergazda tartományvezérlőjét kell megkeresni a DNS használatával.

   >[!NOTE]
   >A használt tartományi rendszergazdai fiók nem lehet a Protected Users csoport tagja. Ha igen, a művelet sikertelen lesz.

   2. Hívja meg `Update-AzureADSSOForest -OnPremCredentials $creds`. Ez a parancs frissíti az adott AD-erdőben található `AZUREADSSOACC` számítógépfiók Kerberos-visszafejtési kulcsát, és frissíti azt az Azure AD-ben.
   3. Ismételje meg a fenti lépéseket minden olyan AD-erdőnél, amelyre beállította a szolgáltatást.

   >[!IMPORTANT]
   >Győződjön meg arról, hogy _nem_ futtatja többször a `Update-AzureADSSOForest` parancsot. Ellenkező esetben a funkció addig leáll, amíg a felhasználó Kerberos-jegye lejár, és a helyszíni Active Directory újra kiadja.

**K: Hogyan lehet letiltani a zökkenőmentes egyszeri bejelentkezést?**

   **1. lépés. A szolgáltatás letiltása a bérlőn**

   **A. lehetőség: letiltás a Azure AD Connect használatával**
    
   1. Futtassa Azure AD Connect, válassza a **felhasználói bejelentkezés módosítása lapot** , és kattintson a **tovább**gombra.
   2. Törölje az **egyszeri bejelentkezés engedélyezése** lehetőséget. Folytassa a varázslót.

   A varázsló befejezése után a rendszer letiltja a zökkenőmentes egyszeri bejelentkezést a bérlőn. A képernyőn megjelenő üzenet azonban a következőképpen jelenik meg:

   "Az egyszeri bejelentkezés le van tiltva, de további manuális lépéseket is végre kell hajtani a tisztítás befejezéséhez. További információ "

   A tisztítási folyamat befejezéséhez hajtsa végre a 2. és a 3. lépést azon a helyszíni kiszolgálón, amelyen a Azure AD Connect fut.

   **B. lehetőség: letiltás a PowerShell használatával**

   Futtassa az alábbi lépéseket azon a helyszíni kiszolgálón, amelyen a Azure AD Connect futtatja:

   1. Először töltse le és telepítse az [Azure ad PowerShellt](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
   3. Importálja a zökkenőmentes SSO PowerShell-modult a következő parancs használatával: `Import-Module .\AzureADSSO.psd1`.
   4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja meg a `New-AzureADSSOAuthenticationContext`. Ennek a parancsnak meg kell adnia egy előugró ablakban, hogy megadja a bérlő globális rendszergazdai hitelesítő adatait.
   5. Hívja meg `Enable-AzureADSSO -Enable $false`.

   >[!IMPORTANT]
   >A zökkenőmentes egyszeri bejelentkezés letiltása a PowerShell használatával nem változtatja meg Azure AD Connect állapotát. A zökkenőmentes egyszeri bejelentkezés a **felhasználó bejelentkezési** oldalának módosítása lapon engedélyezettként jelenik meg.

   **2. lépés. Azon AD-erdők listájának beolvasása, amelyeken engedélyezve van a zökkenőmentes egyszeri bejelentkezés**

   Ha a Azure AD Connect használatával letiltotta a zökkenőmentes egyszeri bejelentkezést, kövesse az alábbi 1 – 4. feladatot. Ha letiltotta a zökkenőmentes egyszeri bejelentkezést a PowerShell használatával, ugorjon az alábbi 5. feladatra.

   1. Először töltse le és telepítse az [Azure ad PowerShellt](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Navigáljon a `%programfiles%\Microsoft Azure Active Directory Connect` mappához.
   3. Importálja a zökkenőmentes SSO PowerShell-modult a következő parancs használatával: `Import-Module .\AzureADSSO.psd1`.
   4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja meg a `New-AzureADSSOAuthenticationContext`. Ennek a parancsnak meg kell adnia egy előugró ablakban, hogy megadja a bérlő globális rendszergazdai hitelesítő adatait.
   5. Hívja meg `Get-AzureADSSOStatus | ConvertFrom-Json`. Ez a parancs az AD-erdők listáját tartalmazza (tekintse meg a "tartományok" listát), amelyen a funkció engedélyezve van.

   **3. lépés. Törölje kézzel a `AZUREADSSOACCT` számítógépfiókot a felsorolt AD-erdőkből.**

## <a name="next-steps"></a>Következő lépések

- Rövid [**útmutató – az**](how-to-connect-sso-quick-start.md) Azure ad zökkenőmentes egyszeri bejelentkezésének beszerzése és futtatása.
- [**Technikai**](how-to-connect-sso-how-it-works.md) részletes útmutató – a funkció működésének megismerése.
- [**Hibaelhárítás**](tshoot-connect-sso.md) – megismerheti a szolgáltatással kapcsolatos gyakori problémák megoldását.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új szolgáltatásokra vonatkozó kérelmek bejelentése.
