---
title: Egyesült Királyság hivatalos & Egyesült Királysági NHS-tervezetének mintája
description: Az Egyesült Királyság hivatalos és az Egyesült Királysági NHS-tervezetek mintáinak szabályozása. Mindegyik vezérlő egy vagy több olyan Azure-szabályzatra van leképezve, amely segítséget nyújt az értékeléshez.
ms.date: 12/04/2019
ms.topic: sample
ms.openlocfilehash: 5bef590013a9ef06b791e58dc6c82e74dffe1a17
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851366"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Az Egyesült Királyság hivatalos és az Egyesült Királysági NHS-tervezetek mintáinak szabályozása

A következő cikk azt ismerteti, hogyan jelennek meg az Egyesült Királyság hivatalos és egyesült királysági NHS-tervezetei az Egyesült Királyság hivatalos és egyesült királysági NHS-vezérlőinek. További információ a vezérlőelemekről: [Egyesült Királyság hivatalos](https://www.gov.uk/government/publications/government-security-classifications).

Az alábbi hozzárendelések az **Egyesült Királyság hivatalos** és **Egyesült Királysági NHS** -vezérlőinek. A jobb oldali navigációs sávon közvetlenül egy adott vezérlőelem-megfeleltetésre ugorhat. A leképezett vezérlők számos [Azure Policy](../../../policy/overview.md) kezdeményezéssel valósulnak meg. A teljes kezdeményezés áttekintéséhez nyissa meg a **szabályzatot** a Azure Portalban, és válassza a **definíciók** lapot. Ezután keresse meg és válassza ki a **\[előzetes verzióját\] a naplózási Egyesült Királyság hivatalos és az Egyesült királyságbeli NHS-vezérlőket, és telepítsen speciális virtuálisgép-bővítményeket a naplózási követelmények** beépített házirend-kezdeményezés támogatására

> [!IMPORTANT]
> Az alábbi vezérlők egy vagy több [Azure Policy](../../../policy/overview.md) -definícióhoz vannak társítva. Ezek a szabályzatok segítséget nyújthatnak a vezérlő [megfelelőségének értékelésében](../../../policy/how-to/get-compliance-data.md) ; azonban gyakran nem 1:1 vagy teljes egyezés van egy vezérlő és egy vagy több szabályzat között. Ennek megfelelően a Azure Policy **megfelel** a saját szabályzatoknak; Ez nem teszi lehetővé, hogy teljes mértékben megfeleljen a vezérlők összes követelményének. Emellett a megfelelőségi szabvány olyan vezérlőket is tartalmaz, amelyek jelenleg nincsenek Azure Policy definíciók által tárgyalva. Ezért a Azure Policy megfelelősége csak a teljes megfelelőségi állapotának részleges áttekintése. A megfelelőségi tervhez tartozó vezérlők és Azure Policy definíciói közötti társítások idővel változhatnak. A módosítási előzmények megtekintéséhez tekintse meg a [GitHub-követési előzményeket](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 adatátviteli védelem

A terv segítségével biztosítható, hogy az Azure-szolgáltatásokkal való adatátvitel biztonságos legyen, ha olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek naplózzák a nem biztonságos kapcsolatokat a Storage-fiókokhoz és Redis cache.

- Csak a Redis Cache biztonságos kapcsolatai lehetnek engedélyezve
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt
- A nem biztonságos kommunikációs protokollokat használó Windows-webkiszolgálók naplózási eredményeinek megjelenítése
- A biztonságos kommunikációs protokollokat nem használó Windows-webkiszolgálók naplózásához szükséges előfeltételek központi telepítése
- A legújabb TLS-verziót kell használni az API-alkalmazásban
- A legújabb TLS-verziót kell használni a webalkalmazásban
- A legújabb TLS-verziót kell használni a függvényalkalmazás

## <a name="23-data-at-rest-protection"></a>2,3 inaktív adatok védelme

Ez a terv segítséget nyújt a házirendnek a titkosítási vezérlők használatára való érvényesítéséhez, ha olyan [Azure Policy](../../../policy/overview.md) -definíciókat rendel hozzá, amelyek kikényszerítik az adott titkosítási vezérlőket, és naplózzák a gyenge titkosítási beállítások használatát.
Annak megismerése, hogy az Azure-erőforrások nem optimális titkosítási konfigurációval rendelkezzenek-e, segítheti a javítási műveleteket, hogy az erőforrások konfigurálása az adatvédelmi szabályzatnak megfelelően történjen. Pontosabban, a tervhez hozzárendelt szabályzatok titkosítást igényelnek a Lake Storage-fiókokhoz; transzparens adattitkosítás megkövetelése SQL-adatbázisokban; a Storage-fiókokon, az SQL-adatbázisokon, a virtuálisgép-lemezeken és az Automation-fiók változójában a hiányzó titkosítás naplózása; nem biztonságos kapcsolatok naplózása a Storage-fiókokhoz és a Redis Cache; gyenge virtuális gép jelszavas titkosításának naplózása; és a titkosítatlan Service Fabric kommunikáció naplózása.

- A virtuális gépeken alkalmazni kell a lemeztitkosítást
- Az Automation-fiók változóit titkosítani kell
- Service Fabric-fürtökön a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre kell beállítani
- Az SQL-adatbázisokon engedélyezni kell transzparens adattitkosítás
- Az SQL DB transzparens adattitkosításának üzembe helyezése
- Titkosítás megkövetelése Data Lake Store fiókokon
- Engedélyezett helyszínek (az "Egyesült Királyság déli régiója" és az "Egyesült Királyság nyugati régiója" között rögzített)
- Engedélyezett telephelyek az erőforráscsoportok számára (az "Egyesült Királyság déli régiója" és az "Egyesült Királyság nyugati régiója" között rögzített kód)

## <a name="52-vulnerability-management"></a>5,2 biztonsági rések kezelése

Ez a terv segítséget nyújt az információs rendszer biztonsági réseinak kezeléséhez olyan [Azure Policy](../../../policy/overview.md) definíciók hozzárendelésével, amelyek figyelik a hiányzó Endpoint Protectiont, a hiányzó rendszerfrissítéseket, az operációs rendszer sebezhetőségeit, az SQL biztonsági réseket és a virtuális Ezek az információk valós idejű információkat biztosítanak az üzembe helyezett erőforrások biztonsági állapotáról, és segíthetnek a Szervizelési műveletek rangsorolásában.

- Az Endpoint Protection hiányának monitorozása az Azure Security Centerben
- A rendszerfrissítéseknek telepítve kell lennie a gépeken
- A virtuálisgép-méretezési csoportokhoz telepíteni kell a rendszerfrissítéseket
- A gépek biztonsági konfigurációjának biztonsági réseit el kell hárítani
- Az SQL-adatbázisok biztonsági réseit szervizelni kell
- A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni
- A sebezhetőségi felmérést alkalmazni ajánlott az SQL-kiszolgálókon
- A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.
- A virtuálisgép-méretezési csoportokban található biztonsági beállítások biztonsági réseit el kell hárítani
- A speciális adatbiztonságot engedélyezni kell az SQL felügyelt példányain
- A speciális adatbiztonságot alkalmazni ajánlott az SQL-kiszolgálókon

## <a name="53-protective-monitoring"></a>5,3 védelmi monitorozás

Ez a tervezet segítséget nyújt az információs rendszer eszközeinek védelméhez olyan [Azure Policy](../../../policy/overview.md) -definíciók kiosztásával, amelyek védelmet nyújtanak a korlátlan hozzáférés, a listázási tevékenységek és a fenyegetések ellen.

- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken
- Virtuális gépek naplózása vész-helyreállítás nélkül konfigurálva
- A DDoS Protection standardot engedélyezni kell
- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell beállítani az SQL felügyelt példány speciális adatbiztonsági beállításainál
- Az összetett veszélyforrások elleni védelem típusait "all" értékre kell állítani az SQL Server speciális adatbiztonsági beállításaiban
- Veszélyforrások észlelésének üzembe helyezése SQL-kiszolgálókon
- A Windows Serverhez készült alapértelmezett Microsoft IaaSAntimalware-bővítmény telepítése

## <a name="9-secure-user-management"></a>9 biztonságos felhasználói felügyelet 

Az Azure szerepköralapú hozzáférés-vezérlést (RBAC) valósít meg, amellyel felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv segít a hozzáférési jogosultságok korlátozásában és szabályozásában [Azure Policy](../../../policy/overview.md) definíciók kiosztásával, hogy a külső fiókokat a tulajdonossal és/vagy az olvasási/írási engedélyekkel és a tulajdonosi, olvasási és/vagy írási engedélyekkel rendelkező fiókokkal naplózza, amelyeken nincs engedélyezve a többtényezős hitelesítés

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-nak engedélyezve kell lennie az előfizetéséhez tartozó írási engedélyekkel rendelkező fiókoknak
- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

## <a name="10-identity-and-authentication"></a>10 identitás és hitelesítés

Ez a terv segít a hozzáférési jogosultságok korlátozásában és szabályozásában [Azure Policy](../../../policy/overview.md) definíciók kiosztásával, hogy a külső fiókokat a tulajdonossal és/vagy az olvasási/írási engedélyekkel és a tulajdonosi, olvasási és/vagy írási engedélyekkel rendelkező fiókokkal naplózza, amelyeken nincs engedélyezve a többtényezős hitelesítés

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-nak engedélyezve kell lennie az előfizetéséhez tartozó írási engedélyekkel rendelkező fiókoknak
- Az MFA-t engedélyezni kell az előfizetésre vonatkozó olvasási engedéllyel rendelkező fiókokon
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Az olvasási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a terv Azure Policy definíciókat rendel hozzá az SQL-kiszolgálók és a Service Fabric Azure Active Directory-hitelesítésének naplózásához. A Azure Active Directory hitelesítés használata lehetővé teszi az egyszerűbb engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központosított Identitáskezelés kezelését.

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni
- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez

Ez a terv Azure Policy definíciókat is hozzárendeli a naplózási fiókokhoz, amelyeket érdemes áttekinteni, beleértve az értékcsökkenéssel rendelkező fiókokat és a külső fiókokat is. Ha szükséges, a fiókokat le lehet tiltani a bejelentkezés (vagy Eltávolítás) alól, amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogokat. Ez a terv két Azure Policy-definíciót rendel hozzá az olyan leértékelt fiókokhoz, amelyeket el kell tekinteni az eltávolításhoz.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a terv egy Azure Policy-definíciót is hozzárendel, amely a Linux rendszerű virtuális gép jelszavas engedélyeinek naplózására vonatkozó engedélyeket naplózza, ha helytelenül vannak beállítva. Ez a kialakítás lehetővé teszi a korrekciós műveletek elvégzését, hogy a hitelesítő adatok ne legyenek biztonságban.

- \[előzetes verzió\]: olyan Linux rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem rendelkeznek a passwd fájl engedélyeivel 0644 értékre állítva

Ez a terv segít az erős jelszavak betartatásában olyan Azure Policy-definíciók hozzárendelésével, amelyek a minimális szilárdságot és egyéb jelszavakat nem érvényesítő Windows-virtuális gépeket naplózzák. A jelszó erősségét sértő virtuális gépek ismerete segít az összes virtuálisgép-felhasználói fiók jelszavának megfelelő javítási műveletek elvégzésében.

- \[előzetes verzió\]: a jelszó bonyolultsága beállítással nem rendelkező Windows rendszerű virtuális gépek naplózásához szükséges előfeltételek központi telepítése
- \[előzetes verzió\]: telepítse az előfeltételeket a maximális jelszóval nem rendelkező Windows rendszerű virtuális gépek naplózására 70 nap
- \[előzetes verzió\]: telepítse az előfeltételeket olyan Windows rendszerű virtuális gépek naplózására, amelyek nem rendelkeznek legalább 1 napos jelszóval
- \[előzetes verzió\]: telepítse az előfeltételeket a Windows rendszerű virtuális gépek naplózására, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre.
- \[előzetes verzió\]: telepítse az előfeltételeket a Windows rendszerű virtuális gépek naplózására, amelyek lehetővé teszik az előző 24 jelszó újbóli használatát.
- \[előzetes verzió\]: olyan Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyeken nincs engedélyezve a jelszó bonyolultsága beállítás
- \[előzetes verzió\]: olyan Windowsos virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem rendelkeznek maximális jelszóval (70 nap)
- \[előzetes verzió\]: olyan Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem rendelkeznek legalább 1 napos jelszóval
- \[előzetes verzió\]: a Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek nem korlátozzák a jelszó minimális hosszát 14 karakterre.
- \[előzetes verzió\]: a Windows rendszerű virtuális gépek naplózási eredményeinek megjelenítése, amelyek lehetővé teszik az előző 24 jelszó újbóli használatát.

A terv az Azure-erőforrásokhoz való hozzáférés szabályozását is lehetővé teszi Azure Policy definíciók hozzárendelésével. Ezek a házirendek olyan erőforrástípusok és konfigurációk használatát naplózzák, amelyek lehetővé tehetik az erőforrásokhoz való hozzáférést. A szabályzatok megsértése miatti erőforrások megismerése segíthet az Azure-erőforrások elérését engedélyező, a jogosult felhasználókra korlátozódó kijavítási műveletek elvégzésében.

- \[előzetes verzió\]: követelmények telepítése a jelszavak nélküli fiókokkal rendelkező linuxos virtuális gépek naplózására
- \[előzetes verzió\]: követelmények telepítése a jelszavak nélküli fiókok távoli kapcsolatait engedélyező Linux rendszerű virtuális gépek naplózására
- \[előzetes verzió\]: Linux rendszerű virtuális gépek naplózása jelszavak nélkül
- \[előzetes verzió\]: a jelszavak nélküli fiókok távoli kapcsolatait engedélyező Linux rendszerű virtuális gépek naplózása
- A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra
- A virtuális gépeket migrálni kell az új Azure Resource Manager-erőforrásokra
- Felügyelt lemezeket nem használó virtuális gépek naplózása

## <a name="11-external-interface-protection"></a>11 külső felület védelme

Ha több mint 25 házirendet használ a megfelelő biztonságos felhasználói felügyelethez, ez a terv a nem korlátozott tárolási fiókokat figyelő [Azure Policy](../../../policy/overview.md) -definíció hozzárendelésével segíti a szolgáltatási felületek jogosulatlan hozzáférés elleni védelmét. A korlátlan hozzáféréssel rendelkező Storage-fiókok nem kívánt hozzáférést biztosíthatnak az információs rendszeren belül található információkhoz. Ez a terv egy olyan szabályzatot is hozzárendel, amely lehetővé teszi az adaptív alkalmazások vezérlését a virtuális gépeken.

- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- Az adaptív alkalmazások vezérlőit engedélyezni kell a virtuális gépeken
- A IaaS lévő webalkalmazások NSG-szabályait meg kell erősíteni
- Korlátozni kell az internet felé irányuló végponton keresztüli hozzáférést
- Az internetre irányuló virtuális gépek hálózati biztonsági csoportjának szabályait meg kell szigorítani
- Az Endpoint Protection megoldást telepíteni kell a virtuálisgép-méretezési csoportokban
- Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken
- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- A távoli hibakeresést ki kell kapcsolni függvényalkalmazás
- A távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz
- A távoli hibakeresést ki kell kapcsolni az API-alkalmazáshoz
- A webalkalmazás elérése csak HTTPS protokollon keresztül történhet
- függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el
- Az API-alkalmazás csak HTTPS protokollon keresztül érhető el

## <a name="12-secure-service-administration"></a>12 biztonságos szolgáltatás felügyelete

Az Azure szerepköralapú hozzáférés-vezérlést (RBAC) valósít meg, amellyel felügyelheti, hogy ki férhet hozzá az Azure-beli erőforrásokhoz. A Azure Portal használatával áttekintheti, hogy ki férhet hozzá az Azure-erőforrásokhoz és azok engedélyeihez. Ez a terv segít az emelt szintű hozzáférési jogosultságok korlátozásában és szabályozásában azáltal, hogy öt [Azure Policy](../../../policy/overview.md) definíciót rendel hozzá a tulajdonossal és/vagy írási engedélyekkel rendelkező külső fiókokhoz, valamint a tulajdonossal és/vagy olyan írási engedélyekkel, amelyeken nincs engedélyezve a többtényezős hitelesítés.

A felhőalapú szolgáltatás felügyeletéhez használt rendszerek magas jogosultsági szintű hozzáférést kapnak a szolgáltatáshoz. A kompromisszum jelentős hatással lenne, beleértve a biztonsági ellenőrzés megkerülését és a nagy adatmennyiségek ellopását és kezelését. A szolgáltató rendszergazdája által az operatív szolgáltatás kezeléséhez használt módszereket úgy kell kialakítani, hogy enyhítse a szolgáltatás biztonságát veszélyeztető kiaknázás kockázatát. Ha ez az elv nem valósul meg, a támadók megkerülhetik a biztonsági ellenőrzéseket, vagy nagy mennyiségű adatmennyiséget ellopnak vagy kezelhetnek.

- Az MFA-t engedélyezni kell az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókokon
- Az MFA-nak engedélyezve kell lennie az előfizetéséhez tartozó írási engedélyekkel rendelkező fiókoknak
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a terv Azure Policy definíciókat rendel hozzá az SQL-kiszolgálók és a Service Fabric Azure Active Directory-hitelesítésének naplózásához. A Azure Active Directory hitelesítés használata lehetővé teszi az egyszerűbb engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központosított Identitáskezelés kezelését.

- Az SQL-kiszolgálókhoz Azure Active Directory rendszergazdának kell kiépíteni
- Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez

Ez a terv Azure Policy-definíciókat is hozzárendeli a naplózási fiókokhoz, amelyeket érdemes áttekinteni, beleértve az értékcsökkenéssel rendelkező fiókokat és a emelt szintű engedélyekkel rendelkező külső fiókokat is. Ha szükséges, a fiókokat le lehet tiltani a bejelentkezés (vagy Eltávolítás) alól, amely azonnal eltávolítja az Azure-erőforrásokhoz való hozzáférési jogokat. Ez a terv két Azure Policy-definíciót rendel hozzá az olyan leértékelt fiókokhoz, amelyeket el kell tekinteni az eltávolításhoz.

- Az elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből
- Írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Ez a terv egy Azure Policy-definíciót is hozzárendel, amely a Linux rendszerű virtuális gép jelszavas engedélyeinek naplózására vonatkozó engedélyeket naplózza, ha helytelenül vannak beállítva. Ez a kialakítás lehetővé teszi a korrekciós műveletek elvégzését, hogy a hitelesítő adatok ne legyenek biztonságban.

- \[előzetes verzió\]: a Linux rendszerű virtuális gép/etc/passwd-engedélyeinek naplózása a 0644 értékre van állítva.

## <a name="13-audit-information-for-users"></a>13 naplózási információ a felhasználók számára

Ez a terv segítséget nyújt a rendszeresemények naplózásához az Azure-erőforrások naplózási beállításait naplózó [Azure Policy](../../../policy/overview.md) -definíciók hozzárendelésével. A hozzárendelt szabályzat azt is naplózza, hogy a virtuális gépek nem küldenek naplókat egy adott log Analytics-munkaterületre.

- A naplózást engedélyezni kell a speciális adatbiztonsági beállításokon SQL Server
- Diagnosztikai beállítás naplózása
- \[előzetes verzió\]: Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken
- \[előzetes verzió\]: Log Analytics ügynök központi telepítése Windows rendszerű virtuális gépekre
- A Network Watcher üzembe helyezése virtuális hálózatok létrehozásakor

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az Egyesült Királyság hivatalos és egyesült királysági NHS-tervezetének vezérlési leképezését, az alábbi cikkekben megismerheti az áttekintést és a minta üzembe helyezésének módját:

> [!div class="nextstepaction"]
> [Egyesült Királyság hivatalos és egyesült királysági NHS-tervezetei – áttekintés](./index.md)
> [Egyesült Királyság hivatalos és egyesült királysági NHS-tervezetei – lépések üzembe helyezése](./deploy.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
