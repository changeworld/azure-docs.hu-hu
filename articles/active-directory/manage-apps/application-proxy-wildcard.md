---
title: Helyettesítő karakteres alkalmazások az Azure AD Application Proxy
description: Ismerje meg, hogyan használható az Azure Active Directory application proxy helyettesítő karaktereket tartalmazó alkalmazások.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5a9e7be5f582051e03cba08733fcbfa697cc8f5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275045"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Az Azure Active Directory application proxy a helyettesítő karaktereket tartalmazó alkalmazások

Az Azure Active Directoryban (Azure AD), a helyszíni nagy számú konfigurálása az alkalmazások gyorsan Kezelhetetlen válhat, és konfigurációs hibák szükségtelen kockázatok vezet be, ha ezek közül számos ugyanazokat a beállításokat. Az [Azure ad Application proxy](application-proxy.md)a problémát a helyettesítő karakteres alkalmazások közzétételével kezelheti, és egyszerre több alkalmazást is közzétehet és kezelhet. Ez a megoldás, amely lehetővé teszi, hogy:

- A felügyelettel járó többletterhelést egyszerűsítése
- A potenciális konfigurációs hibák számának csökkentése
- A felhasználók biztonságosan hozzáférhessenek a további erőforrások

Ez a cikk a helyettesítő karaktert tartalmazó alkalmazás-közzététel konfigurálása a környezetében szükséges információkat biztosít.

## <a name="create-a-wildcard-application"></a>Helyettesítő karaktert tartalmazó alkalmazás létrehozása

Egy helyettesítő karakter (*) alkalmazást létrehozhatja úgy is, ha ugyanazt a konfigurációt az alkalmazások egy csoportja. A helyettesítő karaktereket tartalmazó alkalmazások esetében lehetséges alternatívák: alkalmazások megosztása a következő beállításokat:

- Azok a felhasználók csoportját
- Az egyszeri bejelentkezés módszer
- A hozzáférési protokoll (http, https)

Helyettesítő karaktereket is tartalmazó alkalmazások teheti közzé, ha mind a belső és külső URL-címeket a következő formátumban:

> http (s)://*.\<tartomány\>

Például: `http(s)://*.adventure-works.com`.

A belső és külső URL-címeket használhatja a különböző tartományokban, ajánlott eljárásként, amíg azok azonosnak kell lennie. Ha az alkalmazás közzététele hibaüzenet jelenik meg, ha egy helyettesítő karaktert tartalmazó URL-címek egyike nem rendelkezik.

Ha további alkalmazásokat különböző konfigurációs beállításokkal rendelkezik, közzé kell tennie az ilyen kivételek külön alkalmazásokként felülírja az alapértelmezett értékeket beállítani a helyettesítő karaktert. Helyettesítő karakter nélküli alkalmazások mindig elsőbbséget élveznek helyettesítő karaktereket tartalmazó alkalmazások. Konfigurációs szempontjából ezek a "csak" a szokásos alkalmazások.

Helyettesítő karakteres alkalmazás létrehozása ugyanazon [alkalmazás-közzétételi folyamaton](application-proxy-add-on-premises-application.md) alapul, amely minden más alkalmazás számára elérhető. Az egyetlen különbség, hogy tartalmazza-e helyettesítő karakterként az URL-címeket, és az egyszeri bejelentkezés konfigurálása.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként győződjön meg arról, hogy teljesítette ezeket a követelményeket.

### <a name="custom-domains"></a>Egyéni tartományok

Míg az [Egyéni tartományok](application-proxy-configure-custom-domain.md) nem kötelezőek az összes többi alkalmazáshoz, ezek a helyettesítő alkalmazások előfeltételei. Egyéni tartományok létrehozása szükséges, hogy:

1. Ellenőrzött tartomány létrehozása az Azure-ban.
1. Töltse fel az application proxy SSL-tanúsítvány PFX formátumban.

Érdemes lehet egy helyettesítő tanúsítványt használja az alkalmazás azt tervezi, hogy hozzon létre megfelelő. Másik lehetőségként használhatja egy tanúsítványt, amely csak megjeleníti az adott alkalmazásokat. Csak azokat az alkalmazásokat, a tanúsítvány szerepel ebben az esetben a helyettesítő karaktereket tartalmazó alkalmazásokká keresztül elérhető lesz.

Biztonsági okokból ez egy szigorú követelmény és helyettesítő karaktereket nem támogatjuk az alkalmazásokat, amelyek a külső URL-cím nem használható egy egyéni tartományt.

### <a name="dns-updates"></a>DNS-frissítések

Egyéni tartományok használatakor létre kell hoznia egy DNS-bejegyzést a külső URL-címhez (például `*.adventure-works.com`) tartozó CNAME-rekorddal, amely az alkalmazásproxy-végpont külső URL-címére mutat. Helyettesítő karakteres alkalmazások esetén a CNAME rekordnak a megfelelő külső URL-címekre kell mutatnia:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Annak ellenőrzéséhez, hogy helyesen konfigurálta-e a CNAME-t, használhatja az [nslookupt](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) az egyik cél végponton, például `expenses.adventure-works.com`.  A válasznak tartalmaznia kell a már említett aliast (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).

## <a name="considerations"></a>Megfontolandó szempontok

Az alábbiakban néhány megfontolandó szempontot érdemes figyelembe venni a helyettesítő karakteres alkalmazások esetében.

### <a name="accepted-formats"></a>Elfogadott formátumok

Helyettesítő karakteres alkalmazások esetén a **belső URL-címet** `http(s)://*.<domain>`ként kell formázni.

![A belső URL-cím esetében használja a http (s)://* formátumot.\<tartomány >](./media/application-proxy-wildcard/22.png)

**Külső URL-cím**konfigurálásakor a következő formátumot kell használnia: `https://*.<custom domain>`

![A külső URL-cím esetében használja a * https://formátumot.\<egyéni tartomány >](./media/application-proxy-wildcard/21.png)

Más esetekben a helyettesítő karaktert, több helyettesítő karaktereket vagy más regex karakterláncok nem támogatottak, és hibák okozzák.

### <a name="excluding-applications-from-the-wildcard"></a>A helyettesítő karaktereket tartalmazó alkalmazások kizárása

Egy alkalmazás kizárása a helyettesítő karaktereket tartalmazó alkalmazásokká szerint

- Az alkalmazás rendszeres kivétel alkalmazás közzététele
- A helyettesítő karakter, csak az adott alkalmazások a DNS-beállítások engedélyezése

Rendszeres alkalmazásként alkalmazás közzététele a kizárandó egy alkalmazás egy helyettesítő karaktert tartalmazó előnyben részesített módszer. Tegyen közzé a kizárt alkalmazást, mielőtt a helyettesítő karaktereket tartalmazó alkalmazások annak érdekében, hogy a kivételek az elejétől is érvényben vannak. A legpontosabb alkalmazás mindig elsőbbséget élvez – a `budgets.finance.adventure-works.com` közzétett alkalmazások elsőbbséget élveznek az alkalmazás `*.finance.adventure-works.com`ével szemben, ami elsőbbséget élvez az alkalmazás `*.adventure-works.com`.

A helyettesítő karaktert csak a megfelelő alkalmazások a DNS-management szolgáltatáson keresztül működik is korlátozhatja. Ajánlott eljárásként hozzon létre egy CNAME-bejegyzést, amely egy helyettesítő karaktert tartalmaz, és megfelel a konfigurált külső URL-cím formátuma. Adott alkalmazás URL-címek azonban a helyettesítő karakterek inkább mutat. `*.adventure-works.com`helyett például `hr.adventure-works.com`, `expenses.adventure-works.com` és `travel.adventure-works.com individually` `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`.

Ha ezt a lehetőséget választja, akkor a `AppId.domain`értékhez is szükség van egy másik CNAME-bejegyzésre, például `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`ra, amely ugyanarra a helyre mutat. A **AppID** a helyettesítő karakteres alkalmazás alkalmazás tulajdonságai lapján található:

![Keresse meg az alkalmazás AZONOSÍTÓját az alkalmazás tulajdonságlapján](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>A MyApps panel kezdőlap URL-Címének beállítása

A helyettesítő karakteres alkalmazás csak egy csempével jelenik meg a [MyApps panelen](https://myapps.microsoft.com). Alapértelmezés szerint ez a csempe eltűnik. A csempe megjelenítése, és rendelkezik az adott oldalon található felhasználók föld:

1. Kövesse a [Kezdőlap URL-címének beállításához](application-proxy-configure-custom-home-page.md)szükséges útmutatást.
1. Állítsa az **alkalmazás megjelenítése** **igaz** értékre az alkalmazás Tulajdonságok lapján.

### <a name="kerberos-constrained-delegation"></a>Kerberos által korlátozott delegálás

A Kerberos által [korlátozott delegálást (KCD) használó alkalmazások egyszeri bejelentkezéses módszere](application-proxy-configure-single-sign-on-with-kcd.md)esetén az egyszeri bejelentkezéses metódushoz megadott egyszerű szolgáltatásnév is helyettesítő karakternek kell lennie. Az egyszerű szolgáltatásnév például a következő lehet: `HTTP/*.adventure-works.com`. Továbbra is szükség van a háttér-kiszolgálókon konfigurált egyedi SPN-ek használatára (például `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).

## <a name="scenario-1-general-wildcard-application"></a>1\. forgatókönyv: Általános helyettesítő karaktereket tartalmazó alkalmazásokká

Ebben a forgatókönyvben három különböző alkalmazás közzétenni kívánt rendelkezik:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Mindhárom alkalmazás:

- Minden felhasználó által használt
- *Integrált Windows-hitelesítés* használata
- Az azonos tulajdonságokkal rendelkezik.

A helyettesítő karakteres alkalmazást közzéteheti az [alkalmazások közzététele az Azure ad Application proxy használatával](application-proxy-add-on-premises-application.md)című témakörben ismertetett lépésekkel. Ez a forgatókönyv feltételezi, hogy:

- A következő AZONOSÍTÓval rendelkező bérlő: `000aa000-11b1-2ccc-d333-4444eee4444e`
- Egy `adventure-works.com` nevű ellenőrzött tartomány konfigurálva lett.
- A rendszer létrehoz egy `*.adventure-works.com` `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`re mutató **CNAME** -bejegyzést.

A [dokumentált lépéseket](application-proxy-add-on-premises-application.md)követve hozzon létre egy új alkalmazásproxy-alkalmazást a bérlőben. Ebben a példában a helyettesítő karakter szerepel a következő mezőket:

- Belső URL-címe:

    ![Példa: helyettesítő karakter a belső URL-címben](./media/application-proxy-wildcard/42.png)

- Külső URL-címe:

    ![Példa: helyettesítő karakter a külső URL-címben](./media/application-proxy-wildcard/43.png)

- Belső alkalmazás egyszerű Szolgáltatásneve:

    ![Példa: helyettesítő karakter az SPN-konfigurációban](./media/application-proxy-wildcard/44.png)

A helyettesítő karakteres alkalmazás közzétételével most már elérheti a három alkalmazást a használt URL-címekre való navigálással (például `travel.adventure-works.com`).

A konfiguráció az alábbi struktúrával valósít meg:

![Megjeleníti a példa konfigurációjában megvalósított struktúrát](./media/application-proxy-wildcard/05.png)

| Szín | Leírás |
| ---   | ---         |
| Kék  | A Azure Portal explicit módon közzétett és látható alkalmazások. |
| Szürke  | Alkalmazások az alkalmazás-n keresztül elérhető is. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>2\. forgatókönyv: Általános helyettesítő karaktereket tartalmazó alkalmazásokká kivétel

Ebben a forgatókönyvben a három általános alkalmazás mellett egy másik alkalmazást is `finance.adventure-works.com`, amelyet csak pénzügyi részlegnek kell elérnie. Az aktuális alkalmazás struktúrával a pénzügyi alkalmazás elérhető-e a helyettesítő karaktereket tartalmazó alkalmazásokká keresztül, és amelyet minden alkalmazott lenne. Ennek módosításához, zárja ki az alkalmazás a helyettesítő karakteres külön alkalmazás korlátozóbb engedélyekkel pénzügyi konfigurálásával.

Győződjön meg arról, hogy létezik olyan CNAME-rekord, amely az alkalmazáshoz tartozó alkalmazásproxy lapon megadott alkalmazásspecifikus végpontra `finance.adventure-works.com` mutat. Ebben a forgatókönyvben a `finance.adventure-works.com` `https://finance-awcycles.msappproxy.net/`re mutat.

A [dokumentált lépéseket](application-proxy-add-on-premises-application.md)követve ehhez a forgatókönyvhöz a következő beállítások szükségesek:

- A **belső URL-címben**helyettesítő karakter helyett a **Pénzügy** értéket kell beállítania.

    ![Példa: a pénzügy beállítása helyettesítő karakter helyett a belső URL-címben](./media/application-proxy-wildcard/52.png)

- A **külső URL-címben**helyettesítő karakter helyett a **Pénzügy** értéket kell beállítania.

    ![Példa: a pénzügy beállítása helyettesítő karakter helyett a külső URL-címben](./media/application-proxy-wildcard/53.png)

- Belső alkalmazás SPN-ben a **Penzugy** helyett helyettesítő karaktert kell beállítania.

    ![Példa: a pénzügy beállítása helyettesítő karakter helyett a SPN-konfigurációban](./media/application-proxy-wildcard/54.png)

Ezzel a konfigurációval megvalósul az alábbi forgatókönyvet:

![A minta forgatókönyv által megvalósított konfiguráció megjelenítése](./media/application-proxy-wildcard/09.png)

Mivel `finance.adventure-works.com` a `*.adventure-works.com`nál pontosabb URL-cím, elsőbbséget élvez. A `finance.adventure-works.com` navigáló felhasználóknak a pénzügyi erőforrások alkalmazásban meg kell adni a felhasználói élményt. Ebben az esetben csak a pénzügyi alkalmazottak férhetnek hozzá `finance.adventure-works.com`hoz.

Ha több alkalmazást is közzétett a Finance szolgáltatásban, és `finance.adventure-works.com` ellenőrzött tartományként, akkor közzétehet egy másik helyettesítő helyettesítő alkalmazást `*.finance.adventure-works.com`. Mivel ez konkrétabb, mint az általános `*.adventure-works.com`, elsőbbséget élvez, ha egy felhasználó a pénzügyi tartományban található alkalmazáshoz fér hozzá.

## <a name="next-steps"></a>További lépések

- Az **Egyéni tartományokkal**kapcsolatos további tudnivalókért tekintse meg az [Egyéni tartományok használata az Azure ad Application proxy-ban](application-proxy-configure-custom-domain.md)című témakört.
- További információ az **alkalmazások közzétételéről**: [alkalmazások közzététele az Azure ad Application proxy használatával](application-proxy-add-on-premises-application.md)
