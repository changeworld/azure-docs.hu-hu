---
title: 'Azure AD Connect: felhőalapú hitelesítés szakaszos bevezetéssel | Microsoft Docs'
description: Ez a cikk azt ismerteti, hogyan lehet áttelepíteni az összevont hitelesítésről a Felhőbeli hitelesítésre egy előkészített bevezetéssel.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3044ebdd716eb85dc63d3a77089912d0d51d8b6
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915233"
---
# <a name="migrate-to-cloud-authentication-by-using-staged-rollout-preview"></a>Áttelepítés a felhőalapú hitelesítésbe szakaszos bevezetés használatával (előzetes verzió)

A szakaszos bevezetési megközelítés használatával áttelepítheti az összevont hitelesítést a felhőalapú hitelesítésbe. Ez a cikk bemutatja, hogyan hajthatja végre a kapcsolót. Mielőtt elkezdené a szakaszos bevezetést, érdemes figyelembe vennie a következményeket, ha az alábbi feltételek közül egy vagy több teljesül:
    
-  Jelenleg helyszíni Multi-Factor Authentication-kiszolgálót használ. 
-  Intelligens kártyákat használ a hitelesítéshez. 
-  Az aktuális kiszolgáló a csak összevonási szolgáltatásokat kínálja.

A szolgáltatás kipróbálása előtt javasoljuk, hogy tekintse át az útmutatót a megfelelő hitelesítési módszer kiválasztásához. További információ: a módszerek összehasonlítása a [Azure Active Directory Hybrid Identity megoldás megfelelő hitelesítési módszerének kiválasztása](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods)című táblázatban.

A funkció áttekintéséhez tekintse meg ezt a "Azure Active Directory: mi a szakaszos bevezetés?" című témakört. videóinak

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Előfeltételek

-   Van egy Azure Active Directory (Azure AD) bérlő összevont tartománnyal.

-   Úgy döntött, hogy két lehetőség közül választhat:
    - **A** - *Password hash-szinkronizálás (Sync)*  + *zökkenőmentes egyszeri bejelentkezés (SSO)*
    - **B. lehetőség** - *átmenő hitelesítés* *zökkenőmentes SSO* - + 
    
    Habár a *zökkenőmentes egyszeri bejelentkezés* nem kötelező, javasoljuk, hogy a tartományhoz csatlakoztatott számítógépeket futtató felhasználók számára is csendes bejelentkezési élményt biztosítson a vállalati hálózaton belül.

-   A Felhőbeli hitelesítésre áttelepített felhasználókhoz szükséges összes bérlői arculatot és feltételes hozzáférési szabályzatot konfigurálta.

-   Ha az Azure Multi-Factor Authentication használatát tervezi, javasoljuk, hogy az [önkiszolgáló jelszó-visszaállítás (SSPR) esetében konvergens regisztrációt használjon, és multi-Factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md) , hogy a felhasználók egyszer regisztrálják a hitelesítési módszereiket.

-   A szakaszos bevezetési funkció használatához globális rendszergazdának kell lennie a bérlőn.

-   A *zökkenőmentes egyszeri bejelentkezés* engedélyezéséhez egy adott Active Directory erdőben tartományi rendszergazdának kell lennie.

## <a name="supported-scenarios"></a>Támogatott esetek

A szakaszos bevezetéshez a következő forgatókönyvek támogatottak. A szolgáltatás csak a következőhöz használható:

- Azok a felhasználók, akik az Azure AD-hez Azure AD Connect használatával lettek kiépítve. Ez nem vonatkozik a csak felhőalapú felhasználókra.

- Felhasználói bejelentkezési forgalom a böngészőkben és a *modern hitelesítési* ügyfeleken. Az örökölt hitelesítést használó alkalmazások vagy felhőalapú szolgáltatások visszaesik az összevont hitelesítési folyamatokra. Előfordulhat például, hogy a modern hitelesítéssel rendelkező Exchange Online vagy az Outlook 2010, amely nem támogatja a modern hitelesítést.

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A következő forgatókönyvek nem támogatottak a szakaszos bevezetésnél:

- Bizonyos alkalmazások a hitelesítés során elküldik a "domain_hint" lekérdezési paramétert az Azure AD-nek. Ezek a folyamatok folytatódnak, és az előkészített bevezetésre engedélyezett felhasználók továbbra is a hitelesítéshez használják az összevonást.

<!-- -->

- A rendszergazdák biztonsági csoportok használatával tudják kideríteni a Felhőbeli hitelesítést. Ha a helyszíni Active Directory biztonsági csoportok használatakor szeretné elkerülni a szinkronizálás késését, javasoljuk, hogy használjon Felhőbeli biztonsági csoportokat. A következő feltételek érvényesek:

    - Szolgáltatásként legfeljebb 10 csoportot használhat. Ez azt is megteheti, hogy 10 csoportot használ a *jelszó kivonatának szinkronizálásához*, az *átmenő hitelesítéshez*és a *zökkenőmentes egyszeri bejelentkezéshez*.
    - Beágyazott csoportok *nem támogatottak*. Ez a hatókör a nyilvános előzetes verzióra is érvényes.
    - A szakaszos bevezetéshez *nem használhatók* dinamikus csoportok.
    - A csoporton belüli kapcsolattartási objektumok nem lesznek hozzáadva a csoporthoz.

- Azure AD Connect vagy PowerShell használatával továbbra is a végső átváltás kell összevontról felhőbe történő hitelesítésre. A előkészített bevezetések nem váltják át a tartományokat összevontról felügyelt állapotba.

- Amikor először ad hozzá egy biztonsági csoportot az előkészített bevezetéshez, a rendszer 200-re korlátozza a felhasználóktól, hogy elkerülje az UX időtúllépését. A csoport hozzáadása után további felhasználókat is hozzáadhat közvetlenül hozzá, ha szükséges.

## <a name="get-started-with-staged-rollout"></a>Ismerkedés a lépcsőzetes bevezetéssel

Ha tesztelni szeretné a *jelszó-kivonatolási szinkronizálást* a szakaszos bevezetéssel, kövesse a következő szakaszban ismertetett lépéseket.

További információ a használni kívánt PowerShell-parancsmagokról: [Azure AD 2,0 előzetes](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)verzió.

## <a name="pre-work-for-password-hash-sync"></a>A jelszó-kivonat szinkronizálásának előzetes munkája

1. Engedélyezze a *jelszó-kivonatok szinkronizálását* a Azure ad Connect [választható funkciók](how-to-connect-install-custom.md#optional-features) lapján. 

   ![Képernyőkép a "választható szolgáltatások" lapról Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Győződjön meg arról, hogy a *jelszó kivonatának teljes szinkronizálási* ciklusa fut, így minden felhasználó jelszavas kivonata szinkronizálva lett az Azure ad-vel. A *jelszó-kivonatolási szinkronizálás*állapotának megtekintéséhez használja a PowerShell-diagnosztikát a [jelszó-kivonat szinkronizálásának Azure ad Connect szinkronizálással való hibakereséséhez](tshoot-connect-password-hash-synchronization.md).

   ![Képernyőkép a AADConnect hibaelhárítási naplójáról](./media/how-to-connect-staged-rollout/sr2.png)

Ha lépcsőzetes *bevezetéssel* kívánja tesztelni az átmenő hitelesítési bejelentkezést, akkor a következő szakaszban leírt lépéseket követve engedélyezze azt.

## <a name="pre-work-for-pass-through-authentication"></a>Az átmenő hitelesítés előzetes munkája

1. Azonosítson egy Windows Server 2012 R2 vagy újabb rendszert futtató kiszolgálót, ahol az *átmenő hitelesítési* ügynököt futtatni szeretné. 

   *Ne válassza a* Azure ad Connect-kiszolgálót. Győződjön meg arról, hogy a kiszolgáló tartományhoz van csatlakoztatva, a kijelölt felhasználók hitelesítését Active Directory, és képes kommunikálni az Azure AD-vel a kimenő portok és URL-címek használatával. További információkért tekintse [meg az Azure ad zökkenőmentes egyszeri bejelentkezésének](how-to-connect-sso-quick-start.md)"1. lépés: az Előfeltételek ellenőrzése" című szakaszát.

1. [Töltse le a Azure ad Connect hitelesítési ügynököt](https://aka.ms/getauthagent), és telepítse a kiszolgálóra. 

1. A [magas rendelkezésre állás](how-to-connect-sso-quick-start.md)engedélyezéséhez telepítsen további hitelesítési ügynököket más kiszolgálókra.

1. Győződjön meg arról, hogy megfelelően konfigurálta az [intelligens zárolás beállításait](../authentication/howto-password-smart-lockout.md) . Így biztosíthatja, hogy a felhasználók helyszíni Active Directory fiókjai ne legyenek kizárva a rossz szereplőkkel.

Javasoljuk, hogy engedélyezze a *zökkenőmentes egyszeri bejelentkezést* a bejelentkezési módszertől függetlenül (*jelszó-kivonatolási szinkronizálás* vagy *átmenő hitelesítés*), amelyet a szakaszos bevezetéshez választott ki. A *zökkenőmentes egyszeri bejelentkezés*engedélyezéséhez kövesse a következő szakaszban leírt útmutatást.

## <a name="pre-work-for-seamless-sso"></a>A zökkenőmentes egyszeri bejelentkezéshez szükséges előzetes munka

A PowerShell használatával engedélyezze a *zökkenőmentes egyszeri bejelentkezést* a Active Directory erdőkön. Ha egynél több Active Directory erdővel rendelkezik, minden egyes erdőhöz engedélyezze azt. A  *zökkenőmentes egyszeri bejelentkezés* csak olyan felhasználók számára aktiválódik, akik a szakaszos bevezetésre vannak kiválasztva. Nem érinti a meglévő összevonási beállításokat.

Az alábbi lépésekkel engedélyezheti a *zökkenőmentes egyszeri bejelentkezést* :

1. Jelentkezzen be Azure AD Connect kiszolgálóra.

2. Nyissa meg a *(z)% ProgramFiles%\\Microsoft Azure Active Directory Connect* mappát.

3. Importálja a *zökkenőmentes SSO* PowerShell-modult a következő parancs futtatásával: 

   `Import-Module .\AzureADSSO.psd1`

4. Futtassa a PowerShellt rendszergazdaként. A PowerShellben hívja meg a `New-AzureADSSOAuthenticationContext`. Ez a parancs egy olyan ablaktáblát nyit meg, amelyen megadhatja a bérlő globális rendszergazdai hitelesítő adatait.

5. Hívja meg `Get-AzureADSSOStatus | ConvertFrom-Json`. Ez a parancs Active Directory erdők listáját jeleníti meg (lásd a "tartományok" listáját), amelyen a funkció engedélyezve van. Alapértelmezés szerint a a bérlő szintjén hamis értékre van állítva.

   ![Példa a Windows PowerShell kimenetére](./media/how-to-connect-staged-rollout/sr3.png)

6. Hívja meg `$creds = Get-Credential`. A parancssorba írja be a kívánt Active Directory erdő tartományi rendszergazdai hitelesítő adatait.

7. Hívja meg `Enable-AzureADSSOForest -OnPremCredentials $creds`. Ez a parancs létrehoz egy AZUREADSSOACC számítógépfiókot a helyszíni tartományvezérlőről a *zökkenőmentes egyszeri bejelentkezéshez*szükséges Active Directory erdőhöz.

8. A *zökkenőmentes egyszeri bejelentkezéshez* az intranet zónában URL-címek szükségesek. Ha ezeket az URL-címeket Csoportházirendek használatával szeretné telepíteni, tekintse [meg a rövid útmutató: Azure ad zökkenőmentes egyszeri bejelentkezés](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)című témakört.

9. A teljes bemutatóhoz letöltheti a *zökkenőmentes egyszeri bejelentkezéshez*használható [üzembe helyezési terveket](https://aka.ms/SeamlessSSODPDownload) is.

## <a name="enable-staged-rollout"></a>Szakaszos bevezetés engedélyezése

Egy adott szolgáltatás (*átmenő hitelesítés*, *jelszó-kivonatoló szinkronizálás*vagy *zökkenőmentes SSO*) egy csoporton belüli kiválasztásához kövesse a következő szakaszokban ismertetett utasításokat.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Egy adott szolgáltatás lépcsőzetes bevezetésének engedélyezése a bérlőn

A következő lehetőségek közül választhat:

- **A** - *Password hash-szinkronizálás* + *zökkenőmentes egyszeri bejelentkezés*
- **B. lehetőség** - *átmenő hitelesítés* *zökkenőmentes SSO* - + 
- **Nem támogatott** - *jelszó-kivonatolási szinkronizálás* + *átmenő hitelesítés* + *zökkenőmentes SSO*

Tegye a következőket:

1. Az előnézet UX eléréséhez jelentkezzen be az [Azure ad-portálra](https://aka.ms/stagedrolloutux).

2. Jelölje be a **felügyelt felhasználói bejelentkezés (előzetes verzió) hivatkozásának engedélyezése szakaszos** bevezetést.

   Ha például engedélyezni szeretné az *a kapcsolót*, a **jelszó-kivonatolási szinkronizálást** és a **zökkenőmentes egyszeri bejelentkezéses** vezérlőket csúsztassa a **be**értékre, ahogy az az alábbi képeken látható.

   ![Az Azure AD Connect lap](./media/how-to-connect-staged-rollout/sr4.png)

   !["A szakaszos bevezetési funkciók engedélyezése (előzetes verzió)" oldal](./media/how-to-connect-staged-rollout/sr5.png)

3. Adja hozzá a csoportokat a szolgáltatáshoz az *átmenő hitelesítés* és a *zökkenőmentes egyszeri bejelentkezés*engedélyezéséhez. Az UX-időtúllépés elkerüléséhez győződjön meg arról, hogy a biztonsági csoportok kezdetben legfeljebb 200 tagot tartalmaznak.

   ![A "csoportok kezelése jelszó-kivonat szinkronizálásához (előzetes verzió)" oldal](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >A csoport tagjai automatikusan engedélyezve vannak az előkészített bevezetéshez. A beágyazott és a dinamikus csoportok nem támogatottak a szakaszos bevezetéshez.

## <a name="auditing"></a>Naplózás

Engedélyezte a naplózási eseményeket a szakaszos bevezetéshez végrehajtott különféle műveletekhez:

- Naplózási esemény, ha engedélyez egy előkészített bevezetést a *jelszó-kivonatolási szinkronizáláshoz*, az *átmenő hitelesítéshez*vagy a *zökkenőmentes egyszeri bejelentkezéshez*.

  >[!NOTE]
  >A rendszer naplózza a naplózási eseményt, ha a *zökkenőmentes egyszeri bejelentkezés* be van kapcsolva a szakaszos bevezetés használatával.

  ![A "bevezetési szabályzat létrehozása a szolgáltatáshoz" ablaktábla – tevékenység lap](./media/how-to-connect-staged-rollout/sr7.png)

  ![A "bevezetési szabályzat létrehozása a szolgáltatáshoz" panel – módosított tulajdonságok lap](./media/how-to-connect-staged-rollout/sr8.png)

- Naplózási esemény, ha egy csoport hozzá lett adva a *jelszó kivonatának szinkronizálásához*, az *átmenő hitelesítéshez*vagy a *zökkenőmentes egyszeri bejelentkezéshez*.

  >[!NOTE]
  >A rendszer naplózza a naplózási eseményt, amikor a rendszer hozzáad egy csoportot a *jelszó-kivonat szinkronizálásához* a szakaszos bevezetéshez.

  ![A "Csoport hozzáadása a szolgáltatások bevezetéséhez" ablaktábla – tevékenység lap](./media/how-to-connect-staged-rollout/sr9.png)

  ![A "Csoport hozzáadása a szolgáltatáshoz" panel – módosított tulajdonságok lap](./media/how-to-connect-staged-rollout/sr10.png)

- Naplózási esemény, ha a csoportba felvett felhasználó engedélyezve van az előkészített bevezetéshez.

  ![A "felhasználó hozzáadása a szolgáltatások bevezetéséhez" ablaktábla – tevékenység lap](media/how-to-connect-staged-rollout/sr11.png)

  ![A "felhasználó hozzáadása a szolgáltatások bevezetéséhez" ablaktábla – cél (ok) lap](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Ellenőrzés

A *jelszó-kivonatoló szinkronizálással* vagy *átmenő hitelesítéssel* (Felhasználónév és jelszó bejelentkezéssel) való bejelentkezés teszteléséhez tegye a következőket:

1. Az extraneten nyissa meg az [alkalmazások lapot](https://myapps.microsoft.com) egy privát böngésző-munkamenetben, majd adja meg az előkészített bevezetéshez kiválasztott felhasználói fiók USERPRINCIPALNAME (UPN).

   Azok a felhasználók, akik már megcélozták az előkészített bevezetést, nem lesznek átirányítva az összevont bejelentkezési oldalra. Ehelyett a rendszer felszólítja, hogy jelentkezzen be az Azure AD bérlői márkás bejelentkezési oldalára.

1. Győződjön meg arról, hogy a bejelentkezés sikeresen megtörtént az [Azure ad bejelentkezési tevékenység jelentésében](../reports-monitoring/concept-sign-ins.md) a userPrincipalName való szűréssel.

A bejelentkezés *zökkenőmentes egyszeri bejelentkezéssel*történő tesztelése:

1. Az intraneten nyissa meg az [alkalmazások lapot](https://myapps.microsoft.com) egy privát böngésző-munkamenetben, majd adja meg az előkészített bevezetéshez kiválasztott felhasználói fiók USERPRINCIPALNAME (UPN).

   Azok a felhasználók, akik a *zökkenőmentes SSO* szakaszos kiépítését célozták meg, "próbálnak bejelentkezni..." üzenet, mielőtt a rendszer némán bejelentkezett.

1. Győződjön meg arról, hogy a bejelentkezés sikeresen megtörtént az [Azure ad bejelentkezési tevékenység jelentésében](../reports-monitoring/concept-sign-ins.md) a userPrincipalName való szűréssel.

   Ha nyomon szeretné követni azokat a felhasználói bejelentkezéseket, amelyek továbbra is Active Directory összevonási szolgáltatások (AD FS) (AD FS) időpontban történnek a kiválasztott bevezetési felhasználók számára, kövesse az [AD FS hibaelhárítás: események és naplózás](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)című témakör utasításait. A gyártói dokumentációban tájékozódhat arról, hogyan lehet ezt a külső összevonási szolgáltatóknál megnézni.

## <a name="remove-a-user-from-staged-rollout"></a>Felhasználó eltávolítása az előkészített bevezetésből

Ha eltávolít egy felhasználót a csoportból, azzal letiltja az adott felhasználó szakaszos bevezetését. Az előkészített bevezetési funkció letiltásához csúsztassa vissza a vezérlőt **ki**.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: használhatom ezt a képességet éles környezetben?**

Válasz: igen, használhatja ezt a funkciót az üzemi bérlőben, de javasoljuk, hogy először próbálja ki a tesztelési bérlőben.

**K: használhatja ezt a szolgáltatást állandó "közös létezés" fenntartására, ahol egyes felhasználók összevont hitelesítést használnak, mások pedig Felhőbeli hitelesítést használnak?**

A: nem, ez a funkció az összevontról a felhőbe való Migrálás fázisokban való áttelepítésére, majd a Felhőbeli hitelesítésre való áttérésre lett tervezve. Nem ajánlott állandó vegyes állapotot használni, mivel ez a módszer váratlan hitelesítési folyamatokhoz vezethet.

**K: használhatom a PowerShellt a szakaszos bevezetéshez?**

V: Igen. Ha szeretné megtudni, hogyan használhatja a PowerShellt a szakaszos bevezetéshez, tekintse meg az [Azure ad előzetes](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)verzióját.

## <a name="next-steps"></a>Következő lépések
- [Azure AD 2,0 előzetes verzió](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
