---
title: Azure-előfizetés számlázási tulajdonjogának beszerzése a Microsoft-partnerszerződéshez (MPA)
description: Megtudhatja, hogyan kérhet az Azure-előfizetésekre vonatkozó számlázási tulajdonjogot a többi felhasználótól.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: f8f2db3e81c498757bfc39bf70999ce1e70c09da
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117180"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>Azure-előfizetés számlázási tulajdonjogának beszerzése az MPA-fiókjához

A felügyelt szolgáltatások és az Azure-használat egyetlen, összevont számlájának biztosítása érdekében a Cloud Solution Provider (CSP) átveheti az Azure-előfizetések számlázási tulajdonjogát a közvetlen Nagyvállalati Szerződéssel (EA) rendelkező ügyfelektől.

Ez a szolgáltatás csak [Azure-beli szakértői MSP](https://partner.microsoft.com/membership/azure-expert-msp) minősítésű közvetlen felhőszolgáltatói (Cloud Solution Provider) számlázási partnerek számára elérhető. A Microsoft szabályozása és szabályzatai hatálya alá tartozik, és bizonyos ügyfelek esetén felülvizsgálatot és jóváhagyást is igényelhet.

A számlázási tulajdonjog kéréséhez rendelkeznie kell a **globális rendszergazda** vagy a **rendszergazdai ügynök** szerepkörrel. További információ: [Partnerközpont – Felhasználói szerepkörök és engedélyek hozzárendelése](https://docs.microsoft.com/partner-center/permissions-overview)

Ez a cikk a Microsoft-partnerszerződések számlázási fiókjaira vonatkozik. Ezek a fiókok a felhőszolgáltatói partnerek (CSP-k) számára készülnek az ügyfelek számláinak az új kereskedelmi felületen való kezeléséhez. Az új felület csak olyan partnerek számára érhető el, akik legalább egy olyan ügyféllel rendelkeznek, aki elfogadta a Microsoft-ügyfélszerződést (MCA), illetve rendelkezik Azure-csomaggal. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-partnerszerződéshez](#check-access-to-a-microsoft-partner-agreement).

## <a name="prerequisites"></a>Előfeltételek

1. [Viszonteladói kapcsolat](https://docs.microsoft.com/partner-center/request-a-relationship-with-a-customer) létesítése az ügyféllel. A [CSP regionális hitelesítés áttekintésének](https://docs.microsoft.com/partner-center/regional-authorization-overview) ellenőrzése annak megerősítésére, hogy az ügyfél és a partner bérlője is ugyanabban a hitelesített régióban van.  

2. [Annak megerősítése, hogy az ügyfél elfogadta a Microsoft-ügyfélszerződést](https://docs.microsoft.com/partner-center/confirm-customer-agreement)

3. [Azure-csomag](https://docs.microsoft.com/partner-center/purchase-azure-plan) beállítása az ügyfélnek. Ha az ügyfél több viszonteladótól is vásárol, Önnek mindegyik ügyfél-viszonteladó pároshoz be kell állítania az Azure-csomagot.

## <a name="request-billing-ownership"></a>Számlázási tulajdonjog kérése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) a CSP rendszergazdai ügynök hitelesítő adataival.

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről a költségkezelés + számlázás keresőkifejezéssel](./media/mpa-request-ownership/search-cmb.png)

3. Válassza az **Ügyfelek** lehetőséget a bal oldalon, majd válasszon ki egy ügyfelet a listából.

   ![Képernyőkép az ügyfelek kiválasztásáról](./media/mpa-request-ownership/mpa-select-customers.png)        

4. Válassza a bal alsó részen az **Átadási kérelmek** lehetőséget, majd az **Új kérelem hozzáadása** elemet.

   ![Képernyőkép az átadási kérelmek kiválasztásáról](./media/mpa-request-ownership/mpa-select-transfer-requests.png)

5. Adja meg az ügyfél szervezete azon felhasználójának e-mail-címét, aki az átadási kérelmet fogadja. A felhasználónak egy Nagyvállalati Szerződéssel rendelkező fiók tulajdonosának kell lennie. Válassza az **Átadási kérelem küldése** lehetőséget.

   ![Képernyőkép egy átadási kérelem küldéséről](./media/mpa-request-ownership/mpa-send-transfer-requests.png)

6. A felhasználó egy e-mailt kap, amely felszólítja az átadási kérelem áttekintéséra.

   ![Képernyőkép egy átadási kérelem áttekintésére felszólító e-mailről](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)

7. Az átadási kérelem jóváhagyásához a felhasználó kiválasztja az e-mailben szereplő hivatkozást, és követi az útmutatást.

    ![Képernyőkép egy átadási kérelem áttekintésére felszólító e-mailről](./media/mpa-request-ownership/mpa-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Az átadási kérelem állapotának ellenőrzése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről a költségkezelés + számlázás keresőkifejezéssel](./media/mpa-request-ownership/billing-search-cost-management-billing.png)

3. Válassza az **Ügyfelek** lehetőséget a bal oldalon.

   ![Képernyőkép az ügyfelek kiválasztásáról](./media/mpa-request-ownership/mpa-select-customers.png)

4. Válassza ki a listából azt az ügyfelet, akire az elküldött átadási kérelem vonatkozik.

5. Válassza a bal alsó részen az **Átadási kérelmek** lehetőséget. Az Átadási kérelmek lapon az alábbi információk jelennek meg:

    ![Képernyőkép az átadási kérelmek listájáról](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)

   |Oszlop|Meghatározás|
   |---------|---------|
   |Kérelem dátuma|Az átadási kérelem elküldésének dátuma|
   |Címzett|A felhasználó e-mail-címe, ahová a számlázási tulajdonjog átadásának kérése el lett küldve|
   |Lejárat dátuma|A kérelem lejáratának dátuma|
   |status|Az átadási kérelem állapota|

    Az átadási kérelem állapota a következők egyike lehet:

   |status|Meghatározás|
   |---------|---------|
   |Folyamatban|A felhasználó nem fogadta el az átadási kérelmet|
   |Feldolgozás|A felhasználó jóváhagyta az átadási kérelmet. A felhasználó által kiválasztott előfizetések számlázása átkerül az Ön fiókjába|
   |Befejezve| A felhasználó által kiválasztott előfizetések számlázása átkerült az Ön fiókjába|
   |Befejeződött, hibákkal|A kérelem teljesítve lett, de néhány, a felhasználó által kiválasztott előfizetés átadási nem sikerült|
   |Lejárt|A felhasználó nem fogadta el időben a kérelmet, így a kérelem lejárt|
   |Megszakítva|Valaki, akinek az átadási kérelemhez hozzáférése volt, megszakította a kérelmet|
   |Elutasítva|A felhasználó elutasította az átadási kérelmet|

6. Válasszon ki egy átadási kérelmet a részletei megtekintéséhez. Az átadási részleteinek lapján az alábbi információk jelennek meg: ![Képernyőkép az átadott kérelmek listájáról](./media/mpa-request-ownership/mpa-transfer-completed.png)

   |Oszlop  |Meghatározás|
   |---------|---------|
   |Átadási kérelem azonosítója|Az átadási kérelem egyedi azonosítója. Ha támogatási kérést nyújt be, ossza meg az azonosítót az Azure-támogatással, hogy a támogatási kérésre gyorsabban kapjon választ|
   |Átadási kérelem dátuma|Az átadási kérelem elküldésének dátuma|
   |Átadási kérelem feladója|Az átadási kérelmet elküldő felhasználó e-mail-címe|
   |Átadási kérelem lejárata| Az átadási kérelem lejáratának dátuma|
   |Címzett e-mail-címe|A felhasználó e-mail-címe, ahová a számlázási tulajdonjog átadásának kérése el lett küldve|
   |A címzettnek elküldött átadási hivatkozás|A felhasználónak küldött URL, hogy tekintse át az átadási kérelmet|

## <a name="supported-subscription-types"></a>Támogatott előfizetési típusok

Az alább felsorolt előfizetés-típusok számlázási tulajdonjogát lehet kérelmezni.

- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)\*
- [Microsoft nagyvállalati szerződés](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* A Dev/Test-előfizetést először nagyvállalati (EA-) ajánlattá kell átalakítani egy támogatási jegy létrehozásával. Az Enterprise Dev/Test-előfizetésre az átvitel után használatalapú fizetési díjszabás vonatkozik. A Nagyvállalati Szerződéssel rendelkező ügyfél nagyvállalati Dev/Test-ajánlata részeként kínált kedvezmények nem lesznek elérhetők a CSP-partner számára.

## <a name="additional-information"></a>További információ

A következő szakasz további információkkal szolgál az előfizetések átadásáról.

### <a name="no-service-downtime"></a>Nincs szolgáltatáskiesés

Az előfizetésben lévő Azure-szolgáltatások megszakítás nélkül futnak tovább. Csak a felhasználó által az átadásra kiválasztott Azure-előfizetések számlázási kapcsolatát állítjuk át.

### <a name="disabled-subscriptions"></a>Letiltott előfizetések

Letiltott előfizetéseket nem lehet átadni. Csak az aktív állapotú előfizetések számlázási tulajdonjoga adható át.

### <a name="azure-resources-transfer"></a>Azure-erőforrások átadása

Az előfizetésben található összes erőforrás, például virtuális gép, lemez és webhely átadódik az új fiókba.

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace-termékek átadása

A felhőszolgáltatók (CSP-k) által kezelt előfizetésekhez elérhető Azure Marketplace-termékek is át lesznek adva a hozzájuk tartozó előfizetésekkel együtt. A CSP-k számára nem engedélyezett Azure Marketplace-termékekkel rendelkező előfizetések nem adhatók át.

### <a name="azure-reservations-transfer"></a>Azure-foglalások átadása

Az Azure-foglalásokat a rendszer nem helyezi át automatikusan. Megtarthatja a foglalását, vagy [lemondhatja és újra megvásárolhatja a foglalást](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations) a CSP-ben. 

### <a name="access-to-azure-services"></a>Hozzáférés az Azure-szolgáltatásokhoz

A meglévő felhasználók, csoportok vagy szolgáltatásnevek hozzáféréseire, amelyek az [Azure RBAC (szerepköralapú hozzáférés-vezérlés)](../../role-based-access-control/overview.md) segítségével lettek hozzárendelve, az átadás nincs hatással. A partner nem kap új RBAC-hozzáférést az előfizetésekhez.  

A partnereknek együtt kell működniük az ügyféllel, hogy hozzáférjenek az előfizetésekhez.  A partnereknek rendelkezniük kell [Megbízott felügyeleti – AOBO](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) vagy [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/concepts/cloud-solution-provider)-hozzáféréssel a támogatási jegyek eléréséhez.

### <a name="azure-support-plan"></a>Azure támogatási csomagot

Az Azure-támogatás nem adható át az előfizetésekkel együtt. Ha a felhasználó az összes Azure-előfizetését átadja, akkor kérje meg, hogy mondja le a támogatási csomagját. Az átadás után a CSP-partner felel a támogatásért. Az ügyfélnek a CSP-partnerrel kell együttműködnie bármilyen támogatási kérés esetén.  

### <a name="charges-for-transferred-subscription"></a>Átadott előfizetések díjai

Az előfizetések eredeti számlázási tulajdonosa felelős minden olyan költségért, amelyet az átadás befejezésének időpontjáig jelentettek. Ön a felelős az átadás időpontjától kezdve jelentett díjakért. Előfordulhat, hogy egy díj kiszabása az átadás előtt történt, de csak az átadást követően lett jelentve. Az ilyen díjak megjelennek a számláján.

### <a name="cancel-a-transfer-request"></a>Átadási kérelem megszakítása

Az átadási kérelem megszakítható, ha még nem lett jóváhagyva vagy elutasítva. A megszakításhoz lépjen az [átadás részleteinek oldalára](#check-the-transfer-request-status), és válassza ki az oldal alján a Lemondás lehetőséget.

### <a name="software-as-a-service-saas-transfer"></a>Szolgáltatott szoftver (SaaS) átadása

A SaaS-termékek nem adhatók át az előfizetésekkel együtt. Kérje meg a felhasználót, hogy az SaaS-termékek számlázási tulajdonjogának átadásához [lépjen kapcsolatba az Azure ügyfélszolgálatával](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). A számlázási tulajdonjoggal együtt a felhasználó az erőforrásai tulajdonjogát is átadhatja. Az erőforrás-tulajdonjoga lehetővé teszi felügyeleti műveletek elvégzését, például a termék adatainak törlését és megtekintését. A felhasználónak az SaaS-termékben erőforrás-tulajdonosnak kell lennie ahhoz, hogy átadhassa az erőforrás tulajdonjogát.

### <a name="additional-approval-for-certain-customers"></a>További jóváhagyás bizonyos ügyfelek esetében

Néhány ügyfél átadási kérelme esetében a Microsoft által elvégzett további felülvizsgálati folyamatra lehet szükség az ügyfél aktuális vállalati regisztrációs struktúrájának jellegéből adódóan. A partner értesítést kap az ilyen követelményekről, amikor meghívást küld az ügyfeleknek. A partnerek a partnerfejlesztési vezetőjükkel és az ügyfél fiókkezelési csapatával együttműködve végzik el a felülvizsgálati folyamatot.

### <a name="azure-subscription-directory"></a>Azure-előfizetés címtára
Az átadott Azure-előfizetések címtárának meg kell egyeznie a CSP-kapcsolat létesítése során kiválasztott ügyfél címtárával.

Ha a két címtár nem egyezik, az előfizetések átadása nem sikerült. Létesítenie kell egy új viszonteladói CSP-kapcsolatot az ügyféllel az Azure-előfizetések címtárának kiválasztásával, vagy módosítania kell az Azure-előfizetések címtárát, hogy az megfeleljen az ügyfél CSP-kapcsolati címtárának. További információ: [Meglévő előfizetés társítása az Azure AD-címtárral](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory#to-associate-an-existing-subscription-to-your-azure-ad-directory).


## <a name="check-access-to-a-microsoft-partner-agreement"></a>Microsoft-partnerszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések

- Az Azure-előfizetések számlázási tulajdonjoga átadódik az Önnek. Az ezen előfizetésekre vonatkozó díjak alakulását nyomon követheti az [Azure Portalon](https://portal.azure.com).

- Működjön együtt az ügyféllel, hogy hozzáférjen az átvitt Azure-előfizetésekhez. [Azure-erőforrásokhoz való hozzáférés kezelése RBAC használatával](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
