---
title: 'Azure AD Connect: Első lépések a gyorsbeállításokkal |} A Microsoft Docs'
description: Ismerje meg, hogyan töltheti le, telepítheti és futtathatja az Azure AD Connect telepítővarázslóját.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.assetid: b6ce45fd-554d-4f4d-95d1-47996d561c9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24143f8c94a294da90be84bacfe633db0cd24f85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60244524"
---
# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>Első lépések az Azure AD Connecttel a gyorsbeállítások használatával
Az Azure AD Connect **Express Settings** (Gyorsbeállítások) akkor használható, ha egyerdős topológiával rendelkezik, és a hitelesítéshez [jelszókivonat-szinkronizálást](how-to-connect-password-hash-synchronization.md) alkalmaz. Az **Express Settings** (Gyorsbeállítások) az alapértelmezett beállítás, és ez használatos a leggyakoribb üzembe helyezési forgatókönyvhöz. Csak pár rövid kattintásnyira van attól, hogy kiterjessze helyszíni címtárát a felhőre.

Az Azure AD Connect telepítése előtt győződjön meg arról, hogy [töltse le az Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) és befejeződött a témakörben leírt lépéseket [az Azure AD Connect: Hardverkövetelmények és előfeltételek](how-to-connect-install-prerequisites.md).

Ha a gyorsbeállítások nem felelnek meg a topológiának, lásd az egyéb forgatókönyvek [vonatkozó dokumentációját](#related-documentation).

## <a name="express-installation-of-azure-ad-connect"></a>Az Azure AD Connect gyorstelepítése
Ezeket a lépéseket működés közben a [Videók](#videos) szakaszban tekintheti meg.

1. Jelentkezzen be helyi rendszergazdaként a kiszolgálóra, amelyen az Azure AD Connectet telepíteni kívánja. Ezt azon a kiszolgálón érdemes megtennie, amelyiket szinkronizálási kiszolgálónak szánja.
2. Keresse meg az **AzureADConnect.msi** fájlt, és kattintson rá duplán.
3. Az üdvözlőképernyőn jelölje be a licencfeltételek elfogadását jelző mezőt, és kattintson a **Continue** (Folytatás) gombra.  
4. Az Express settings (Gyorsbeállítások) képernyőn kattintson a **Use express settings** (Gyorsbeállítások használata) lehetőségre.  
   ![Üdvözli az Azure AD Connect](./media/how-to-connect-install-express/express.png)
5. A Connect to Azure AD (Csatlakozás az Azure AD szolgáltatáshoz) képernyőn adja meg az Azure AD egy globális rendszergazdai fiókjának felhasználónevét és jelszavát. Kattintson a **tovább**.  
   ![Csatlakozás az Azure AD-hez](./media/how-to-connect-install-express/connectaad.png)  
   Ha hibaüzenetet kap, és problémák adódnak a kapcsolódással, tekintse meg a [Troubleshoot connectivity problems](tshoot-connect-connectivity.md) (Kapcsolati problémák elhárítása) szakaszt.
6. A Connect to AD DS (Csatlakozás az AD DS szolgáltatáshoz) képernyőn adja meg egy vállalati rendszergazdai fiók felhasználónevét és jelszavát. A tartományrészt megadhatja NetBios- vagy FQDN-formátumban, vagyis FABRIKAM\rendszergazda vagy fabrikam.com\rendszergazda alakban. Kattintson a **tovább**.  
   ![Csatlakozás az AD DS szolgáltatáshoz](./media/how-to-connect-install-express/connectad.png)
7. Az [**Azure AD bejelentkezés konfigurálása**](plan-connect-user-signin.md#azure-ad-sign-in-configuration) lap csak akkor jelenik meg, ha nem végezte el [a tartományok ellenőrzését](../active-directory-domains-add-azure-portal.md) az [előfeltételek](how-to-connect-install-prerequisites.md) között.
   ![Nem ellenőrzött tartományok](./media/how-to-connect-install-express/unverifieddomain.png)  
   Ha ez a lap megjelenik, tekintse át az összes **Not Added** (Hozzá nem adott) és **Not Verified** (Nem ellenőrzött) megjelöléssel rendelkező tartományt. Bizonyosodjon meg róla, hogy az Ön által használt tartományok ellenőrizve lettek az Azure AD szolgáltatásban. Miután ellenőrizte a tartományokat, kattintson a Frissítés szimbólumra.
8. A Ready to configure (Konfigurálásra kész) oldalon kattintson az **Install** (Telepítés) lehetőségre.
   * Másik lehetőségként a Ready to configure (Konfigurálásra kész) oldalon törölheti a **Start the synchronization process as soon as configuration completes** (Szinkronizálási folyamat indítása a konfiguráció befejeztével) beállítás bejelölését. A jelölőnégyzet jelölését akkor érdemes törölnie, ha további konfigurálást, például [szűrést](how-to-connect-sync-configure-filtering.md) kíván végezni. Ha törli a beállítást, a varázsló konfigurálja a szinkronizálást, az ütemezőt azonban letiltva hagyja. Az ütemező nem fut, amíg manuálisan nem engedélyezi [a telepítővarázsló ismételt futtatásával](how-to-connect-installation-wizard.md).
   * A **Start the synchronization process as soon as configuration completes** (Szinkronizálási folyamat indítása a konfiguráció befejeztével) beállítás bejelölésével azonnal elindítja az összes felhasználó, csoport és kapcsolat teljes szinkronizálását az Azure AD-be.
   * Ha az Exchange működik a helyszíni Active Directory szolgáltatásban, akkor lehetősége van engedélyezni az [**Exchange hibrid telepítést**](https://technet.microsoft.com/library/jj200581.aspx) is. Ezt a beállítást akkor engedélyezze, ha Exchange postaládákat egy időben kíván üzemeltetni a felhőben és a helyszíni rendszeren.
     ![Készen áll az Azure AD Connect konfigurálására](./media/how-to-connect-install-express/readytoconfigure.png)
9. A telepítés befejezése után kattintson az **Exit** (Kilépés) gombra.
10. Miután a telepítés befejeződött, jelentkezzen ki, majd ismét jelentkezzen be, mielőtt a Synchronization Service Managert (Szinkronizálási szolgáltatás kezelőjét) vagy a Synchronization Rule Editort (Szinkronizálási szabályok szerkesztőjét) használná.

## <a name="videos"></a>Videók
A gyorstelepítés használatát bemutató videót itt tekintheti meg:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Express-Settings/player]
>
>

## <a name="next-steps"></a>További lépések
Miután az Azure AD Connect telepítése megtörtént, [ellenőrizheti a telepítést, és hozzárendelheti a licenceket](how-to-connect-post-installation.md).

További információkért ezekről a szolgáltatásokról, amelyek a telepítéssel engedélyezett szolgáltatásokkal: [Automatikus frissítés](how-to-connect-install-automatic-upgrade.md), [véletlen törlések megakadályozása](how-to-connect-sync-feature-prevent-accidental-deletes.md), és [az Azure AD Connect Health](how-to-connect-health-sync.md).

Ismerje meg részletesebben a következő általános témaköröket: [az ütemező és a szinkronizálási események indítása](how-to-connect-sync-feature-scheduler.md).

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).

## <a name="related-documentation"></a>Kapcsolódó dokumentáció

| Témakör | Hivatkozás |
| --- | --- |
| Az Azure AD Connect áttekintése | [A helyszíni címtárak integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
| Telepítés testreszabott beállítások használatával | [Az Azure AD Connect testreszabott telepítése](how-to-connect-install-custom.md) |
| Frissítés a DirSync szolgáltatásról | [Frissítés az Azure AD szinkronizáló eszközéről (DirSync)](how-to-dirsync-upgrade-get-started.md)|
| Telepítési fiókok | [További információk az Azure AD Connect hitelesítő adataival és engedélyeivel kapcsolatban](reference-connect-accounts-permissions.md) |
