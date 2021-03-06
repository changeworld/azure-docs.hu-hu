---
title: Webalkalmazási tűzfal (WAF) szabályzatok létrehozása a Application Gatewayhoz
description: Megtudhatja, hogyan hozhat létre webalkalmazási tűzfal-házirendeket a Application Gatewayhoz.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: 3e8cd2f1e594cd6a60296b2df135f275641df313
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086983"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>Webalkalmazási tűzfal házirendjeinek létrehozása a Application Gatewayhoz

A WAF szabályzatok a figyelőkkel való társítása lehetővé teszi, hogy a különböző szabályzatok által védett, egyetlen WAF mögötti helyek is védve legyenek. Ha például öt hely van a WAF mögött, öt különálló WAF-szabályzat közül választhat (egyet az egyes figyelőknél) a kizárások, az egyéni szabályok és a felügyelt szabályrendszerek egy adott helyhez való testreszabásához anélkül, hogy a másik négyet kellene végrehajtania. Ha egyetlen szabályzatot szeretne alkalmazni az összes webhelyre, egyszerűen társíthatja a szabályzatot a Application Gatewayhoz, nem pedig az egyes figyelőkhöz, hogy az alkalmazás globálisan legyen alkalmazva. A házirendek egy elérésiút-alapú útválasztási szabályra is alkalmazhatók. 

Tetszőleges számú szabályzatot készíthet. Miután létrehozta a szabályzatot, ahhoz társítania kell egy Application Gateway, hogy az érvénybe lépjen, de az Application Gateway és a figyelők tetszőleges kombinációjával társítható. 

Ha a Application Gateway szabályzatot alkalmaz, majd egy másik szabályzatot alkalmaz egy figyelőre az adott Application Gateway, akkor a figyelő házirendje érvénybe lép, csak azokhoz a figyelőkhöz, amelyekhez hozzá van rendelve. A Application Gateway szabályzat továbbra is érvényes minden olyan figyelőre, amely nem rendelkezik a hozzájuk rendelt házirenddel. 

   > [!NOTE]
   > A helyszíni és az URI-WAF szabályzatok nyilvános előzetes verzióban érhetők el. Ez azt jelenti, hogy ez a funkció a Microsoft kiegészítő használati feltételeinek hatálya alá tartozik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A webalkalmazási tűzfal minden új WAF-beállítása (egyéni szabályok, felügyelt rulset konfigurációk, kizárások stb.) egy WAF-szabályzaton belül él. Ha rendelkezik meglévő WAF, ezek a beállítások továbbra is előfordulhatnak a WAF-konfigurációban. Az új WAF-házirendre való áttérés lépéseiért lásd a jelen cikk későbbi, a [WAF-konfiguráció áttelepítésének WAF-házirendjére](#migrate) című témakörét. 

## <a name="create-a-policy"></a>Szabályzat létrehozása

Először hozzon létre egy alapszintű WAF szabályzatot egy felügyelt alapértelmezett szabálykészlet (DRS) használatával a Azure Portal segítségével.

1. A portál bal felső részén válassza az **erőforrás létrehozása**lehetőséget. Keresse meg a **WAF**, válassza a **webalkalmazási tűzfal**elemet, majd kattintson a **Létrehozás**gombra.
2. A **WAF házirend létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**:

   |Beállítás  |Érték  |
   |---------|---------|
   |Házirend a következőhöz:     |Regionális WAF (Application Gateway)|
   |Előfizetést     |Adja meg az előfizetés nevét|
   |Erőforráscsoport     |Válassza ki az erőforráscsoportot|
   |Házirend neve     |Adjon egyedi nevet a WAF-házirendnek.|
3. A **társítás** lapon adja meg a következő beállítások egyikét, majd válassza a **Hozzáadás**lehetőséget:

   |Beállítás  |Érték  |
   |---------|---------|
   |Application Gateway hozzárendelése     |Válassza ki a Application Gateway profil nevét.|
   |Figyelők hozzárendelése     |Válassza ki a Application Gateway figyelő nevét, majd válassza a **Hozzáadás**lehetőséget.|

   > [!NOTE]
   > Ha olyan házirendet rendel hozzá a Application Gatewayhoz (vagy figyelőhöz), amely már rendelkezik szabályzattal, a rendszer felülírja az eredeti szabályzatot, és felülírja az új házirendet.
4. Válassza a **felülvizsgálat + létrehozás**, majd a **Létrehozás**lehetőséget.

   ![WAF szabályzat alapjai](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>WAF-szabályok konfigurálása (nem kötelező)

WAF szabályzat létrehozásakor alapértelmezés szerint *észlelési* módban van. Észlelési módban a WAF nem blokkolja a kérelmeket. Ehelyett a rendszer naplózza a megfelelő WAF-szabályokat a WAF-naplókba. A WAF működés közbeni megtekintéséhez módosíthatja a mód beállításait a *megelőzés*lehetőségre. A megelőzési módban a kiválasztott CRS-szabályokban meghatározott egyezési szabályokat a rendszer letiltja és/vagy naplózza a WAF-naplókban.

## <a name="managed-rules"></a>Felügyelt szabályok

Az Azure által felügyelt OWASP-szabályok alapértelmezés szerint engedélyezve vannak. Ha le szeretne tiltani egy szabály csoportjának egy adott szabályát, bontsa ki a szabály csoporton belüli szabályokat, jelölje be a szabály száma előtt található jelölőnégyzetet, majd válassza a **Letiltás** lehetőséget a fenti lapon.

[![felügyelt szabályok](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>Egyéni szabályok

Egyéni szabály létrehozásához válassza az egyéni **szabály hozzáadása** lehetőséget az **Egyéni szabályok** lapon. Ekkor megnyílik az egyéni szabály konfigurációja lap. Az alábbi képernyőfelvételen egy olyan egyéni szabály látható, amely egy kérelem blokkolására van konfigurálva, ha a lekérdezési karakterlánc tartalmazza a szöveges *blockme*.

[![egyéni szabály szerkesztése](../media/create-waf-policy-ag/edit-custom-rule.png)](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate"></a>WAF-konfiguráció migrálása WAF-házirendre

Ha rendelkezik meglévő WAF, előfordulhat, hogy a portálon néhány változást észlelt. Először meg kell határoznia, hogy milyen típusú házirendet engedélyez a WAF. Három lehetséges állapot létezik:

- Nincs WAF szabályzat
- Csak egyéni szabályok házirend
- WAF szabályzat

Megtudhatja, hogy melyik állapotban van a WAF, ha megtekinti a portálon. Ha a WAF beállításai láthatók, és a Application Gateway nézetből módosíthatók, a WAF az 1. állapotú.

[![WAF-konfiguráció](../media/create-waf-policy-ag/waf-configure.png)](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

Ha a **webalkalmazási tűzfal** lehetőséget választja, és egy kapcsolódó házirendet jelenít meg, akkor a WAF 2. vagy 3. állapotú. Ha a házirendbe való navigálás után **csak** az egyéni szabályokat és a társított Application Gateway átjárókat jeleníti meg, akkor csak az egyéni szabályok érvényesek.

![WAF egyéni szabályok](../media/create-waf-policy-ag/waf-custom-rules.png)

Ha a házirend-beállításokat és a felügyelt szabályokat is megjeleníti, akkor ez egy teljes webalkalmazási tűzfal házirend. 

![WAF házirend-beállítások](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>Migrálás a WAF szabályzatba

Ha egyéni szabályok csak WAF szabályzattal rendelkeznek, akkor érdemes lehet áttérni az új WAF-házirendre. A jövőben a tűzfalszabályok támogatják a WAF házirend-beállításait, a felügyelt szabályrendszerek, a kivételeket és a letiltott szabálykészlet-csoportokat. A korábban a Application Gatewayon elvégzett összes WAF-konfiguráció mostantól a WAF-szabályzaton keresztül történik. 

Az egyéni szabály szerkesztése csak a WAF házirend le van tiltva. A WAF beállításainak, például a szabályok letiltásának, a kizárások hozzáadásának és más beállításoknak a szerkesztéséhez át kell térnie egy új, legfelső szintű tűzfalszabály-erőforrásra.

Ehhez hozzon létre egy *webalkalmazási tűzfal-házirendet* , és rendelje hozzá az Ön által választott Application Gateway (ok) hoz és figyelőhöz. Az új szabályzatnak pontosan **meg** kell egyeznie a jelenlegi WAF-konfigurációval, ami azt jelenti, hogy minden egyéni szabályt, kizárást, letiltott szabályt stb. át kell másolni a létrehozandó új szabályzatba. Ha rendelkezik a Application Gatewayhoz tartozó szabályzattal, akkor folytathatja a WAF-szabályok és-beállítások módosítását. Ezt Azure PowerShell is elvégezheti. További információ: WAF- [házirend hozzárendelése meglévő Application Gatewayhoz](associate-waf-policy-existing-gateway.md).

Igény szerint áttelepítési parancsfájlt is használhat egy WAF-házirendbe való áttelepítéshez. További információ: [webalkalmazási tűzfal házirendjeinek Áttelepítése Azure PowerShell használatával](migrate-policy.md).

## <a name="next-steps"></a>Következő lépések

További információ a [webalkalmazási tűzfalon keresztüli CRS-szabályok csoportjairól és szabályairól](application-gateway-crs-rulegroups-rules.md).
