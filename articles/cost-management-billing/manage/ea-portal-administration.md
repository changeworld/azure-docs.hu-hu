---
title: A nagyvállalati szerződéses Azure Portal adminisztrációja
description: Ez a cikk a rendszergazdák Azure EA Portalon elvégzendő gyakori feladatait ismerteti.
author: bandersmsft
ms.author: banders
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 79225d4dfe9e53da6936f8647c9f5a1dff0b4909
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301472"
---
# <a name="azure-ea-portal-administration"></a>A nagyvállalati szerződéses Azure Portal adminisztrációja

Ez a cikk a rendszergazdák Azure EA Portalon (https://ea.azure.com) ) elvégzendő gyakori feladatait ismerteti. Az Azure EA Portal egy online felügyeleti portál, amely segítséget nyújt az ügyfeleknek az Azure EA-szolgáltatások költségének kezelésében. Az Azure EA Portallal kapcsolatos bevezető információkért lásd: [Ismerkedés az Azure EA Portallal](ea-portal-get-started.md).

## <a name="add-a-new-enterprise-administrator"></a>Új vállalati rendszergazda hozzáadása

Az Azure EA-regisztrációk kezelésénél a vállalati rendszergazdák rendelkeznek a legtöbb jogosultsággal. Az első Azure EA-rendszergazdát a nagyvállalati szerződés megkötésekor hozták létre. Viszont bármikor hozzá lehet adni új rendszergazdákat, vagy el is lehet távolítani őket. Az új rendszergazdákat csak a meglévő rendszergazdák adhatják hozzá. A vállalati rendszergazdák hozzáadásával kapcsolatos további információkért lásd az [új vállalati rendszergazda létrehozását](ea-portal-get-started.md#create-another-enterprise-administrator) ismertető részt. A számlázási profil szerepköreivel és feladataival kapcsolatos további információkért lásd [a számlázási profil szerepköreinek és azok feladatainak ismertetését](understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="update-user-state-from-pending-to-active"></a>A felhasználói állapot frissítése Függőben értékről Aktív értékre

Amikor először hozzáadják az új fióktulajdonosokat (AO) egy Azure EA-regisztrációhoz, a _függőben_ állapot jelenik meg náluk. Amikor az új fióktulajdonosok megkapják az aktiváló e-mailt, be tudnak jelentkezni a fiókjuk aktiválásához. A fiókjuk aktiválásakor a fiók állapota _függőben_ értékről _aktív_ értékre módosul. A fióktulajdonosnak el kell olvasnia a figyelmeztető üzenetet, majd a **Folytatás** lehetőséget kell választania. A rendszer kérheti az új felhasználók vezeték- és utónevét egy Kereskedelmi fiók létrehozásához. Ha kéri, akkor meg kell adniuk a szükséges adatokat a folytatáshoz, majd megtörténik a fiók aktiválása.

## <a name="add-a-department-admin"></a>Részlegszintű rendszergazda hozzáadása

Ha az egyik Azure EA-rendszergazda létrehozott egy részleget, az Azure vállalati rendszergazda részlegszintű rendszergazdákat adhat hozzá, és hozzárendelheti őket egy-egy részleghez. A részlegszintű rendszergazdák új fiókokat hozhatnak létre. Az Azure EA-előfizetések létrehozásához új fiókokra van szükség.

A részlegszintű rendszergazdák hozzáadásával kapcsolatos további információkért tekintse meg az [Azure EA-részlegszintű rendszergazdák létrehozását](ea-portal-get-started.md#add-a-department-administrator) ismertető témakört.

## <a name="associate-an-account-to-a-department"></a>Fiókok részlegekhez történő hozzárendelésére

A vállalati rendszergazdák a részlegekhez társíthatják a regisztrációban meglévő fiókokat.

### <a name="to-associate-an-account-to-a-department"></a>Fiókok részlegekhez történő hozzárendelése

1. Jelentkezzen be az Azure EA Portalra vállalati rendszergazdaként.
1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a **Részleg** lehetőséget.
1. Vigye az egérmutatót a fiókot tartalmazó sor fölé, és válassza a jobbra található ceruza ikont.
1. Válassza ki a részleget a legördülő menüből.
1. Kattintson a **Mentés** gombra.

## <a name="department-spending-quotas"></a>Részleg költségkvótái

Az EA-ügyfelek megadhatják vagy módosíthatják a részlegek költségkvótáit a regisztráción belül. A költségkvóta összege az aktuális kötelezettségvállalási időszakra állítható be. Az aktuális kötelezettségvállalási időszak végén a rendszer meghosszabbítja a meglévő költségkvótát a következő kötelezettségvállalási időszakra, kivéve, ha az értékek frissülnek.

A részlegszintű rendszergazda megtekintheti a költségkvótát, de csak a vállalati rendszergazda frissítheti a kvóta összegét. A vállalati rendszergazda és a részlegszintű rendszergazda értesítést kap a kvóta 50, 75, 90 és 100 százalékának elérésekor.

### <a name="enterprise-administrator-to-set-the-quota"></a>A vállalati rendszergazda a következőképp állíthatja be a kvótát:

 1. Nyissa meg az Azure EA Portalt.
 1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
 1. Válassza a **Részleg** lapot.
 1. Válassza ki a részleget.
 1. Válassza a ceruza szimbólumot a Részleg adatai részben, vagy válassza a **+ Részleg hozzáadása** lehetőséget, ha egy új részleggel együtt szeretne költségkvótát hozzáadni.
 1. A Részleg adatai területen adja meg a költségkvóta összegét a regisztráció pénznemében a Költségkvóta $ mezőben (0-nál nagyobb értéket kell megadni).
    - Jelenleg a Részleg neve és a Költséghely is szerkeszthető.
 1. Kattintson a **Mentés** gombra.

A részleg költségkvótája mostantól megjelenik a Részleg fül részlegek listáját tartalmazó nézetében. A jelenlegi kötelezettségvállalási időszak végén az Azure EA Portal fenntartja a költségkvótákat a következő kötelezettségvállalási időszakra.

A részlegkvóta összege független az aktuális pénzügyi kötelezettségvállalástól, és a kvóta összege és a riasztások csak belső használatra szolgálnak. A részleg költségkvótái csak tájékoztató jellegűek, és nem kényszerítenek a költségkeretek betartására.

### <a name="department-administrator-to-view-the-quota"></a>A részlegszintű rendszergazda így tekintheti meg a kvótát:

1. Nyissa meg az Azure EA Portalt.
1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a **Részleg** lapot, és tekintse meg a részlegek listáját tartalmazó nézetet a költségkvótákkal.

Ha Ön közvetett ügyfél, a költségfunkciókat a csatornapartnerének kell engedélyeznie.

## <a name="enterprise-user-roles"></a>Vállalati felhasználók szerepkörei

Az Azure EA Portal segítséget nyújt az Azure EA-költségek és -használat felügyeletéhez. Az Azure EA Portalon három fő szerepkör van:

- EA-rendszergazda
- Részlegszintű rendszergazda
- Fióktulajdonos

Minden szerepkör különböző szintű hozzáféréssel és jogosultsággal rendelkezik.

A felhasználói szerepkörökkel kapcsolatos további információkért lásd: [Vállalati felhasználók szerepkörei](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Azure EA-fiók hozzáadása

Az Azure EA-fiók az Azure EA Portal szervezeti egysége. Ez a fiók az előfizetések felügyeletére, valamint jelentéskészítésre használatos. Az Azure-szolgáltatások eléréséhez és használatához létre kell hoznia vagy hozatnia egy fiókot.

Az Azure-fiókokkal kapcsolatos további információkért lásd: Fiók hozzáadása.

## <a name="enterprise-devtest-offer"></a>Enterprise Dev/Test ajánlat

Az Azure vállalati rendszergazdájaként engedélyezheti a szervezeten belüli fióktulajdonosok számára, hogy előfizetéseket hozzanak létre az EA Dev/Test ajánlat alapján. Ezt a fióktulajdonos Dev/Test jelölőnégyzetének bejelölésével teheti meg az Azure EA Portalon.

Ha bejelölte a Dev/Test jelölőnégyzetet, tudassa a fióktulajdonossal, hogy be tudja állítani a Dev/Test-előfizetőkből álló csapata számára szükséges EA Dev/Test-előfizetéseket.

Ez lehetővé teszi, hogy az aktív Visual Studio-előfizetők speciális fejlesztési és tesztelési díjszabás mellett futtassanak fejlesztési és tesztelési számítási feladatokat az Azure-ban. Hozzáférést biztosít a Dev/Test-képek teljes galériájához, beleértve a Windows 8.1 és a Windows 10 rendszert.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Az Enterprise Dev/Test ajánlat beállítása:

1. Jelentkezzen be vállalati rendszergazdaként.
1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a **Fiók** fület.
1. Válassza annak a fióknak a sorát, amelyhez engedélyezni szeretné a Dev/Test-hozzáférést.
1. Válassza a sortól jobbra található ceruza szimbólumot.
1. Jelölje be a Dev/Test jelölőnégyzetet.
1. Kattintson a **Mentés** gombra.

Ha egy felhasználót fióktulajdonosként adnak hozzá az Azure EA Portalon keresztül, akkor a fióktulajdonoshoz társított Azure-előfizetések, amelyek a használatalapú Dev/Test ajánlaton vagy a Visual Studio-előfizetőknek szóló havi kreditajánlatokon alapulnak, az EA Dev/Test ajánlatra lesznek konvertálva. A fióktulajdonoshoz társított egyéb ajánlattípusokon (például használatalapú ajánlattípusokon) alapuló előfizetések Microsoft Azure Enterprise-ajánlatokra lesznek konvertálva.

A Dev/Test ajánlat jelenleg nem érhető el az Azure Government-ügyfelek számára.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Vállalati fiók átvitele egy új regisztrációba

A fiókátvitellel egy fióktulajdonos áthelyezhető az egyik regisztrációból egy másikba. A fióktulajdonos alá tartozó összes kapcsolódó előfizetés átkerül a célregisztrációba. Akkor használjon fiókátvitelt, ha több aktív regisztráció van, és csak a kiválasztott fióktulajdonosokat szeretné áthelyezni.

Ez a szakasz csak tájékoztató célt szolgál, mert a műveletet vállalati rendszergazda nem végezheti el. Vállalati fiók új regisztrációba történő átviteléhez támogatási kérés leadása szükséges.

Amikor vállalati fiókokat visz át egy új regisztrációba, tartsa szem előtt a következőket:

- A rendszer csak a kérelemben megadott fiókokat viszi át. Ha az összes fiókot kiválasztotta, az átvitel mindegyikre vonatkozni fog.
- A forrásregisztráció megőrzi az állapotát (aktív vagy meghosszabbítva). Tovább folytathatja a használatát, amíg le nem jár.

### <a name="prerequisites"></a>Előfeltételek

A fiókátvitel kérésekor adja meg az alábbi adatokat:

- A célregisztráció számát, az átvinni kívánt fiók nevét és a fióktulajdonos e-mail-címét
- A forrásregisztrációhoz a regisztrációs számot és az átvinni kívánt fiókot

Egyéb szempontok, amelyeket érdemes észben tartani a fiókok átvitele előtt:

- A cél- és a forrásregisztráció esetében is szükség van egy EA-rendszergazda jóváhagyására
- Ha egy fiókátvitel nem felel meg az elvárásainak, vegye fontolóra a regisztráció átvitelét.
- A fiókátvitel az adott fiókokhoz tartozó összes szolgáltatást és előfizetést átviszi.
- Az átvitel befejezését követően az átvitt fiók inaktívként jelenik meg a forrásregisztráció alatt, és aktívként a célregisztráció alatt.
- A fiók a záró dátumot a forrásregisztráció hatályos átviteli dátumaként jelenít meg és a célregisztráció kezdő dátumaként.
- A fiók esetében a hatályos átviteli dátum előtti bármilyen használat a forrásregisztrációhoz tartozik.


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Vállalati regisztráció átvitele egy új regisztrációba

A regisztrációátvitel a következő esetben megfontolandó:

- A jelenlegi regisztráció kötelezettségvállalási időtartama véget ért.
- A regisztráció lejárt/meghosszabbított állapotban van, és egy új szerződés egyeztetése folyamatban van.
- Több regisztrációval rendelkezik, és egyetlen regisztráció keretében szeretné összevonni az összes fiókot és számlázást.

Ez a szakasz csak tájékoztató célt szolgál, mert a műveletet vállalati rendszergazda nem végezheti el. Egy vállalati regisztráció egy újba történő átviteléhez támogatási kérés leadása szükséges.

Ha egy teljes vállalati regisztráció átvitelét kéri egy regisztrációba, a következő műveletek mennek végbe:

- A rendszer az összes Azure-szolgáltatást, előfizetést, fiókot, részleget, valamint a teljes regisztrációs struktúrát átviszi az új célregisztrációba, az EA-részlegek rendszergazdáival együtt.
- A regisztráció állapota _Átvitt_ értékre módosul. Az átvitt regisztrációt csak a korábbi használatról szóló jelentések elkészítéséhez lehet elérni.
- Az átvitt regisztrációkhoz nem adhat hozzá szerepköröket vagy előfizetéseket. Az átvitt állapot megakadályozza a regisztráció további használatának felszámítását.
- A szerződés pénzügyi keretének fennmaradó egyenlege elveszik, a jövőbeli időszakokkal együtt.
-    Ha a regisztráció, amelyről az átvitelt végzi, rendelkezik megvásárolt fenntartott példányokkal, a fenntartott példány vételára továbbra is a forrásregisztrációban marad, azonban a fenntartott példány összes előnye az új regisztrációban lesz elérhető.
-    A Marketplace-en kifizetett egyszeri vételár és a régi regisztrációban felmerült fix havi díjak nem lesznek áthelyezve az új regisztrációba. A használatalapú Marketplace-díjak átvitele megtörténik.

### <a name="effective-transfer-date"></a>Átvitel hatálybalépési dátuma

Az átvitel hatályba lépésének napja a célregisztráció kezdődátuma vagy egy későbbi időpont lehet.

A forrásregisztráció használatának költsége a pénzügyi keretből lesz levonva, vagy túlhasználatként lesz kiszámlázva. Az átvitel dátuma utáni használat átkerül az új regisztrációba, és ennek megfelelően kell majd érte fizetni.

### <a name="prerequisites"></a>Előfeltételek

A regisztrációátvitel kérésekor adja meg az alábbi adatokat:

- A forrásregisztrációhoz a regisztrációs számot.
- A célregisztrációhoz az átvitel céljának regisztrációs számát.
- A regisztrációátvitel hatálybalépési dátuma lehet a célregisztráció kezdő dátuma vagy egy későbbi időpont. A kiválasztott dátum nem lehet hatással a már kiállított túlhasználati számlákban feltüntetett használatra.

Egyéb szempontok, amelyeket érdemes észben tartani a regisztrációk átvitele előtt:

- A cél- és a forrásregisztráció esetében is szükség van egy EA-rendszergazda jóváhagyására.
- Ha egy regisztrációátvitel nem felel meg az elvárásainak, vegye fontolóra a fiókátvitelt.
- A forrásregisztráció állapota átvitt lesz, és ezt a regisztrációt csak a korábbi használatról szóló jelentések elkészítéséhez lehet majd elérni.

### <a name="monetary-commitment"></a>Pénzügyi keret

A pénzügyi keretet nem lehet átvinni egyik regisztrációból a másikba. A pénzügyi keret egyenlegeit a szerződés ahhoz a regisztrációhoz köti, amelyhez megrendelték őket. A rendszer nem viszi át a pénzügyi keretet a fiók vagy a regisztráció átviteli folyamatának részeként.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>A fiókok és a regisztrációk átvitele nem érinti a szolgáltatásokat

A fiókok és a regisztrációk átvitele során nincs állásidő. Ha az összes szükséges információt megadja, a kérelem benyújtásának napján is el lehet végezni.

## <a name="change-account-owner"></a>Fiók tulajdonosának módosítása

Az Azure EA Portalon át lehet adni az előfizetéseket egyik fióktulajdonostól a másiknak. További információért lásd: [Fiók tulajdonosának módosítása](ea-portal-get-started.md#change-account-owner).

## <a name="subscription-transfer-effects"></a>Az előfizetés-áthelyezés hatásai

Ha az Azure-előfizetést ugyanazon Azure AD-bérlő egy másik fiókjába helyezi át, akkor az összes [szerepköralapú hozzáférés-vezérléssel (RBAC)](../../role-based-access-control/overview.md) rendelkező felhasználó, csoport és szolgáltatásnév megtartja az erőforrások kezeléséhez való hozzáférését.

Az előfizetéshez RBAC-hozzáféréssel rendelkező felhasználók megtekintése:

1. Az Azure Portalon nyissa meg az **Előfizetések** oldalt.
2. Válassza ki a megtekinteni kívánt előfizetést, majd válassza a **Hozzáférés-vezérlés (IAM)** elemet.
3. Válassza a **Szerepkör-hozzárendelések** lehetőséget. A szerepkör-hozzárendelések oldala felsorolja az összes felhasználót, aki RBAC-hozzáféréssel rendelkezik az előfizetéshez.

Ha az előfizetést egy másik Azure AD-bérlő fiókjába helyezi át, akkor az összes [RBAC-vel](../../role-based-access-control/overview.md) rendelkező felhasználó, csoport és szolgáltatásnév _elveszíti_ a hozzáférését az erőforrások kezeléséhez. Bár nincs RBAC-hozzáférés, mégis előfordulhat, hogy az előfizetést el lehet érni biztonsági mechanizmusokon keresztül, például:

- Felügyeleti tanúsítványok, amelyek rendszergazdai jogosultságokat biztosítanak a felhasználónak az előfizetés erőforrásaihoz. További információért lásd: [Felügyeleti tanúsítvány létrehozása és feltöltése az Azure szolgáltatáshoz](../../cloud-services/cloud-services-certs-create.md).
- A Storage-hoz hasonló szolgáltatások hozzáférési kulcsai. További információkat az [Azure Storage-fiókok áttekintésében](../../storage/common/storage-account-overview.md) találhat.
- Az olyan szolgáltatások távelérési hitelesítő adatai, mint az Azure-beli virtuális gépek.

Ha a címzettnek korlátoznia kell a hozzáférését az Azure-erőforrásokhoz, érdemes megfontolnia a szolgáltatáshoz társított titkok frissítését. A legtöbb erőforrást a következő lépésekkel lehet frissíteni:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A központi menüben válassza a **Minden erőforrás** elemet.
3. Válassza ki az erőforrást.
4. Az erőforrás oldalán válassza a **Beállítások** lehetőséget a meglévő titkos kulcsok megtekintéséhez és frissítéséhez.

## <a name="delete-subscription"></a>Előfizetés törlése

Egy olyan előfizetés törlése, amely esetében Ön a fióktulajdonos:

1. Jelentkezzen be az Azure Portalra a fiókjához tartozó hitelesítő adatokkal.
1. A központi menüben válassza az **Előfizetések** elemet.
1. Az előfizetések lapján, az oldal bal felső sarkában válassza ki azt az előfizetést, amelyet le szeretne mondani, és válassza az **Előfizetés megszüntetése** lehetőséget a megszüntetési lap megnyitásához.
1. Írja be az előfizetés nevét, válassza ki a megszüntetés okát, majd válassza az **Előfizetés megszüntetése** lehetőséget.

Csak a fiókadminisztrátorok szüntethetik meg az előfizetéseket.

További információ: [Mi történik az előfizetés lemondása után?](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription)

## <a name="delete-an-account"></a>Fiók eltávolítása

A fiókeltávolítást csak aktív előfizetésekkel nem rendelkező aktív fiókok esetében lehet elvégezni.

1. Az Enterprise Portalon válassza a **Kezelés** lehetőséget a bal oldali navigációs sávon.
1. Válassza a **Fiók** fület.
1. A fiókok táblázatából válassza ki a törölni kívánt fiókot.
1. Válassza a fiók sorának jobb oldalán található X szimbólumot.
1. Ha a fiók nem rendelkezik aktív előfizetéssel, válassza a fiók sora alatti **Igen** lehetőséget a fiók eltávolításának megerősítéséhez.

## <a name="update-notification-settings"></a>Frissítési értesítés beállításai

A rendszer automatikusan regisztrálja a vállalati rendszergazdákat, hogy megkapják a regisztrációjukkal kapcsolatos használati értesítéseket. A vállalati rendszergazdák módosíthatják az értesítések időközét, vagy teljesen ki is kapcsolhatják őket.

Az értesítési kapcsolattartók az Azure EA Portal **Értesítési kapcsolattartó** területén jelennek meg. Az értesítési kapcsolattartók kezelésével gondoskodhat arról, hogy a szervezet megfelelő tagjai kapják meg az Azure EA-értesítéseket.

Az aktuális értesítési beállítások megtekintése:

1. Az Azure EA Portalon lépjen a **Kezelés** > **Értesítési kapcsolattartó** elemre.
2. E-mail-cím – A vállalati rendszergazda Microsoft-fiókjához, illetve munkahelyi vagy iskolai fiókjához társított e-mail-cím, amelyre a rendszer az értesítéseket küldi.
3. Számlázatlan egyenlegről szóló értesítés gyakorisága – Az egyes vállalati rendszergazdáknak küldött értesítésekhez beállított gyakoriság.

Kapcsolattartó hozzáadása:

1. Válassza a **+Kapcsolattartó hozzáadása** lehetőséget.
2. Írja be az e-mail-címet, majd erősítse meg.
3. Kattintson a **Mentés** gombra.

Az új értesítési kapcsolattartó megjelenik az **Értesítendő fél** területen. Az értesítés gyakoriságának módosításához válassza ki az értesítendő felet, majd válassza a kiválasztott sor jobb oldalán található ceruza szimbólumot. Állítsa be a gyakorisághoz a **naponta**, **hetente**, **havonta** vagy a **nincs** értéket.

Figyelmen kívül hagyhatja a _közelgő fedezeti időszak záró dátumát_, valamint _letilthatja és megszüntetheti a közelgő dátummal_ kapcsolatos életciklus-értesítéseket. Az életciklus-értesítések letiltásával figyelmen kívül hagyja a fedezeti időszakról és a szerződés záró dátumáról szóló értesítéseket.

## <a name="manage-partner-administrators"></a>Partneradminisztrátorok kezelése

Az Azure EA Portalon mindegyik partneradminisztrátor jogosult felvenni vagy eltávolítani más partneradminisztrátorokat. A partneradminisztrátorok a közvetett regisztrációk partnerszervezeteihez vannak társítva, és nincsenek közvetlenül társítva a regisztrációkhoz.

### <a name="add-a-partner-administrator"></a>Partneradminisztrátorok hozzáadása

Ha meg szeretné tekinteni az összes olyan regisztrációt, amely ugyanahhoz a partnerszervezethez tartozik, mint az aktuális felhasználó, válassza a **Regisztráció** lapot, és jelölje be a kívánt regisztráció jelölőnégyzetét.

1. Jelentkezzen be partneradminisztrátorként.
1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a **Partner** lapot.
1. Válassza az **+ Adminisztrátor hozzáadása** lehetőséget, és adja meg az e-mail-címét, az értesítési kapcsolattartóját és az értesítési adatait.
1. Válassza a **Hozzáadás** lehetőséget.

### <a name="remove-a-partner-administrator"></a>Partneradminisztrátorok eltávolítása

Ha meg szeretné tekinteni az összes olyan regisztrációt, amely ugyanahhoz a partnerszervezethez tartozik, mint az aktuális felhasználó, válassza a **Regisztráció** lapot, és jelölje be a kívánt regisztráció jelölőnégyzetét.

1. Jelentkezzen be partneradminisztrátorként.
1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a **Partner** lapot.
1. A Rendszergazda területen válassza ki az eltávolítani kívánt rendszergazdához tartozó sort.
1. Válassza a jobb oldalon található X szimbólumot.
1. Erősítse meg a törlési szándékát.

## <a name="manage-partner-notifications"></a>A partnerértesítések kezelése

A partnerrendszergazdák beállíthatják, hogy milyen gyakran kapjanak a regisztrációikkal kapcsolatos használati értesítéseket. A rendszer minden héten automatikusan értesítéseket küld nekik a számlázatlan egyenlegükről. Módosíthatják az értesítések gyakoriságát havonta, hetente, vagy naponta értékre, vagy teljesen ki is kapcsolhatják őket.

Ha a felhasználó nem kap értesítést, a következő lépésekkel ellenőrizze, hogy helyesek-e a felhasználó értesítési beállításai.

1. Jelentkezzen be az Azure EA Portalra partnerrendszergazdaként.
2. Válassza a **Kezelés** lehetőséget, majd a **Partner** lapot.
3. Tekintse meg a rendszergazdák listáját a Rendszergazda területen.
4. Az értesítési beállítások szerkesztéséhez vigye a mutatót a megfelelő rendszergazda fölé, és válassza a ceruza szimbólumot.
5. Növelje igény szerint az értesítési gyakoriságot és az életciklus-értesítéseket.
6. Szükség esetén adjon hozzá egy kapcsolattartót, és válassza a **Hozzáadás** lehetőséget.
7. Kattintson a **Mentés** gombra.

![A Kapcsolattartó hozzáadása panelt mutató példa ](./media/ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="view-enrollments-for-partner-administrators"></a>Partneradminisztrátorok regisztrációinak megtekintése

A partneradminisztrátorok megtekinthetik az Azure EA Portalon elérhető közvetlen és közvetett regisztrációik listáját. A regisztrációk áttekintését tartalmazó mezők a regisztrációs számmal, a regisztráció nevével, egyenleggel és a kerettúllépés mennyiségével jelennek meg.

### <a name="view-a-list-of-enrollments"></a>A regisztrációk listájának megtekintése

1. Jelentkezzen be partneradminisztrátorként.
1. Válassza a **Kezelés** lehetőséget az oldal bal oldalán található navigációs sávon.
1. Válassza a **Regisztráció** fület.
1. Jelölje be a regisztrációhoz tartozó jelölőnégyzetet.

Az összes regisztráció nézete az oldal tetején továbbra is megtekinthető marad az egyes regisztrációkhoz tartozó jelölőnégyzetekkel. Emellett a regisztrációk között a lap bal oldalán lévő navigációs sávon az aktuális regisztrációs számot kiválasztva is válthat. Ekkor megjelenik egy felugró ablak, amely lehetővé teszi a regisztrációk keresését, vagy egy másik regisztráció kiválasztását a megfelelő jelölőnégyzet kiválasztásával.

## <a name="azure-sponsorship-offer"></a>Azure Sponsorship-ajánlat

Az Azure Sponsorship-ajánlat korlátozott számú szponzorált Microsoft Azure-fiókot takar. A Microsoft által kiválasztott, korlátozott számú ügyfél az e-mailben kapott meghívóval csatlakozhat hozzá. Ha Ön jogosult a Microsoft Azure Sponsorship-ajánlatára, akkor kapni fog egy e-mailes meghívót a fiókazonosítójára.

További információért hozzon létre egy [támogatási kérést a szponzorálás aktiválásához](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Váltás munkahelyi vagy iskolai fiókkal történő hitelesítésre

Az Azure Enterprise-felhasználók Microsoft-fiókról (MSA vagy Live ID), az Azure-ban Active Directoryt használó munkahelyi vagy iskolai fiókkal történő hitelesítési típusra válthatnak.

A kezdéshez:

1. Adja hozzá a munkahelyi vagy iskolai fiókot az Azure EA Portalhoz a szükséges szerepkörrel/szerepkörökkel.
1. Ha hibaüzenet jelenik meg, előfordulhat, hogy a fiók nem érvényes az Active Directoryban.  Az Azure egyszerű felhasználónevet (UPN) használ, amely nem mindig egyezik meg az e-mail-címmel.
1. Az Azure EA Portalon történő hitelesítéshez a munkahelyi vagy iskolai fiókot használhatja.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Microsoft-fiókokból származó előfizetések átváltása munkahelyi vagy iskolai fiókokra:

1. Jelentkezzen be a felügyeleti portálra azzal a Microsoft-fiókkal, amely az előfizetések tulajdonosa.
1. Az új fiókra való váltáshoz végezze el a fiók tulajdonjogának átruházását.
1. A Microsoft-fiók most már nem rendelkezik aktív előfizetésekkel, és törölhető.
1. A törölt fiókok korábbi számlázási okokból inaktív állapotban megtekinthetőek maradnak a portálon.  Kiszűrheti őket a nézetből, ha bejelöli a kizárólag aktív fiókok megjelenítésére vonatkozó jelölőnégyzetet.

## <a name="account-subscription-ownership-faq"></a>Gyakori kérdések a fiókhoz tartozó előfizetés tulajdonjogával kapcsolatban

Ez a dokumentum a fiókhoz tartozó előfizetés tulajdonjogával kapcsolatos gyakori kérdésekre ad választ.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Hány Azure-fióktulajdonos lehet előfizetésenként?

Előfizetésenként csak egy fióktulajdonos engedélyezett.  További szerepkörök a [portal.azure.com] webhely bal felső sarkában található előfizetési fülön adhatók hozzá a szerepköralapú hozzáférés vagy a (Hozzáférés-vezérlés (IAM)) használatával (https://portal.azure.com) ).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Szerepelhet egy Azure-fiók tulajdonosa több részlegen is?

Nem, egy fióktulajdonos csak egyetlen részleghez társítható. A szabályzat segít biztosítani a részleg költségeinek és kiadásainak pontos monitorozását és felosztását az Azure EA Portalon, az EA-regisztrációhoz igazodva.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Szerepelhet egy Azure-fiók tulajdonosa biztonsági csoportként?

Nem, az előfizetés tulajdonosának egyedi Microsoft-fiók- (MSA-) vagy Azure Active Directory- (AAD-) hitelesítéssel kell rendelkeznie. A szervezeten belüli utódláshoz érdemes lehet általános fiókokat létrehozni és az AAD használatával kezelni az előfizetéshez való hozzáférést.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Egy egyedi felhasználó lehet több előfizetés tulajdonosa?

Egy Azure-fiók tulajdonosa korlátlan számú előfizetést hozhat létre és kezelhet.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Hogyan lehet hozzáférni és megtekinteni a szervezet összes előfizetését?

Jelenleg ezt szabályzat alapján kell végezni, vagyis minden létrehozott előfizetés esetében a fiókot hozzá kell adni egy előfizetési szerepkörhöz szerepköralapú hozzáférés használatával.

### <a name="where-do-i-go-to-create-a-subscription"></a>Hol hozhatok létre előfizetést?

Nagyvállalati Azure-előfizetés létrehozása előtt a fiókját az EA-regisztráció rendszergazdájának hozzá kell adnia a fióktulajdonosi szerepkörhöz az Azure EA Portalon. Ezután be kell jelentkeznie az Azure EA Portalra, hogy megkapja a jogosultságát az EA-ajánlat típusú előfizetések létrehozásához. Javasoljuk, hogy az első EA-előfizetését az EA Portal előfizetési lapján található „+ Előfizetés hozzáadása” hivatkozással hozza létre.  Miután azonban a fiókja rendelkezik a megfelelő jogosultsággal, könnyebb lehet a portal.azure.com webhelyen, az oldal bal felső sarkában található előfizetési fülön létrehozni az előfizetéseket, ahol egyetlen lépésben létrehozhatja és átnevezheti az adott előfizetést.

### <a name="who-can-create-a-subscription"></a>Ki hozhat létre előfizetést?

Ha nagyvállalati Azure-előfizetést szeretne létrehozni, akkor fióktulajdonosi szerepkörrel kell rendelkeznie az [EA Portalon](https://ea.azure.com).

## <a name="next-steps"></a>További lépések

- Olvassa el, hogyan takaríthat meg pénzt a [virtuális gépek lefoglalásával](ea-portal-vm-reservations.md).
- Ha segítségre van szüksége az Azure EA Portallal kapcsolatos hibák elhárításához, olvassa el a következő részt: [A nagyvállalati szerződéses Azure Portal elérésével kapcsolatos hibák elhárítása](ea-portal-troubleshoot.md).
