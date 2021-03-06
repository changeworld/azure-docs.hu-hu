---
title: Komplex veszélyforrások elleni védelem Azure Cosmos DB
description: Ismerje meg, hogyan biztosítja a Azure Cosmos DB az inaktív adatok titkosítását és megvalósítását.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: bcc1c6ffe7cdec4aed325a67969235ae993a5109
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614835"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>A Azure Cosmos DB komplex veszélyforrások elleni védelme (előzetes verzió)

A Azure Cosmos DB komplex veszélyforrások elleni védelme további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel Azure Cosmos DB fiókok eléréséhez vagy kihasználásához. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelésére, akár biztonsági szakértő nélkül, akár a központi biztonsági figyelő rendszerekkel való integrációhoz.

A biztonsági riasztások akkor lépnek életbe, ha a tevékenységben anomáliák vannak. Ezek a biztonsági riasztások integrálva vannak [Azure Security Centerekkel](https://azure.microsoft.com/services/security-center/), és e-mailben is elküldjük az előfizetés-rendszergazdáknak, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálásával és szervizelésével kapcsolatos ajánlásokkal együtt.

> [!NOTE]
>
> * A Azure Cosmos DB komplex veszélyforrások elleni védelme jelenleg csak az SQL API esetében érhető el.
> * A Azure Cosmos DB komplex veszélyforrások elleni védelme jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

A biztonsági riasztások teljes körű vizsgálatához javasolt a [diagnosztikai naplózás engedélyezése Azure Cosmos DBban](https://docs.microsoft.com/azure/cosmos-db/logging), amely maga az adatbázison naplózza a műveleteket, beleértve a szifiliszi műveleteket az összes dokumentumon, tárolón és adatbázison.

## <a name="threat-types"></a>Fenyegetéstípusok

A Azure Cosmos DB komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához. Jelenleg a következő riasztásokat indítja el:

- **Hozzáférés szokatlan helyekről**: Ez a riasztás akkor aktiválódik, ha módosul a hozzáférési minta egy Azure Cosmos-fiókhoz, ahol valaki egy szokatlan földrajzi helyről kapcsolódott a Azure Cosmos db végponthoz. Bizonyos esetekben a riasztás jogszerű műveletet észlel, ami egy új alkalmazás vagy fejlesztői karbantartási művelet. Más esetekben a riasztás rosszindulatú műveletet észlel egy korábbi alkalmazotttól, külső támadótól stb.

- **Szokatlan adatok kinyerése**: Ez a riasztás akkor aktiválódik, ha az ügyfél szokatlan adatmennyiséget nyer ki egy Azure Cosmos db fiókból. Ez lehet annak a tünete, hogy egyes adatok kiszűrése a fiókban tárolt adatok külső adattárba történő átviteléhez.

## <a name="set-up-advanced-threat-protection"></a>Komplex veszélyforrások elleni védelem beállítása

### <a name="set-up-atp-using-the-portal"></a>ATP beállítása a portál használatával

1. Indítsa el a Azure Portalt a [https://portal.azure.com](https://portal.azure.com/).

2. A Azure Cosmos DB fiók **Beállítások** menüjében válassza a **fokozott biztonság**lehetőséget.

    ![ATP beállítása](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. A **speciális biztonsági** beállítások panelen:

    * Kattintson a komplex **veszélyforrások elleni védelem** lehetőségre a **beállításához.**
    * Kattintson a **Save (Mentés** ) gombra az új vagy frissített összetett veszélyforrások elleni védelmi szabályzat mentéséhez.   

### <a name="set-up-atp-using-rest-api"></a>ATP beállítása REST API használatával

A REST API-parancsokkal létrehozhat, frissíthet vagy beszerezhet egy adott Azure Cosmos DB fiók komplex veszélyforrások elleni védelmének beállítását.

* [Komplex veszélyforrások elleni védelem – létrehozás](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Komplex veszélyforrások elleni védelem – Get](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>ATP beállítása Azure PowerShell használatával

Használja a következő PowerShell-parancsmagokat:

* [Komplex veszélyforrások elleni védelem engedélyezése](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Komplex veszélyforrások elleni védelem](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [A komplex veszélyforrások elleni védelem letiltása](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sablonok használata

Azure Resource Manager-sablonnal beállíthatja, hogy a rendszer engedélyezve legyen a komplex veszélyforrások elleni védelem Cosmos DB.
További információ: CosmosDB- [fiók létrehozása komplex veszélyforrások elleni védelemmel](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="using-azure-policy"></a>Azure Policy használata

A Cosmos DB a komplex veszélyforrások elleni védelem engedélyezéséhez használjon Azure Policy.

1. Indítsa el az Azure **Policy-fogalommeghatározások** lapot, és keressen rá a Cosmos db házirend komplex **veszélyforrások elleni védelmének üzembe helyezése** lehetőségre.

    ![Keresési szabályzat](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Kattintson a komplex **veszélyforrások elleni védelem telepítése CosmosDB** házirendre, majd kattintson a **hozzárendelés**elemre.

    ![Előfizetés vagy csoport kiválasztása](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. A **hatókör** mezőben kattintson a három pontra, válasszon ki egy Azure-előfizetést vagy erőforráscsoportot, majd kattintson a **kiválasztás**elemre.

    ![Házirend-definíciók lap](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Adja meg a többi paramétert, majd kattintson a **hozzárendelés**elemre.

## <a name="manage-atp-security-alerts"></a>ATP biztonsági riasztások kezelése

Ha Azure Cosmos DB tevékenység anomália történik, a rendszer egy biztonsági riasztást indít el a gyanús biztonsági eseménnyel kapcsolatos információkkal. 

 Azure Security Center az aktuális [biztonsági riasztásokat](../security-center/security-center-alerts-overview.md)áttekintheti és kezelheti.  Kattintson egy adott riasztásra [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) a lehetséges okok és az ajánlott műveletek megtekintéséhez és a lehetséges fenyegetések enyhítéséhez. Az alábbi képen egy példa látható a Security Centerban megadott riasztási adatokra.

 ![Fenyegetés részletei](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

A rendszer a riasztás részleteivel és a javasolt műveletekkel kapcsolatos e-mail-értesítést is küld. Az alábbi képen egy riasztási e-mail látható.

 ![Riasztás részletei](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>ATP-riasztások Cosmos DB

 A Azure Cosmos DB-fiókok figyelése során generált riasztások listájának megtekintéséhez tekintse meg a Azure Security Center dokumentációjának [Cosmos db riasztások](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos) című szakaszát.

## <a name="next-steps"></a>Következő lépések

* További információ a [diagnosztikai naplózásról Azure Cosmos db](cosmosdb-monitor-resource-logs.md)
* További információ a [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
