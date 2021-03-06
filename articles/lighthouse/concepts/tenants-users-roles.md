---
title: Bérlők, szerepkörök és felhasználók az Azure Lighthouse-forgatókönyvekben
description: Megismerheti Azure Active Directory bérlők, a felhasználók és a szerepkörök fogalmait, valamint azt, hogy miként használhatók az Azure Lighthouse-forgatókönyvekben.
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 344e104201a83b3589dae6dbd3b02e49e4575e00
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156335"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Bérlők, szerepkörök és felhasználók az Azure Lighthouse-forgatókönyvekben

Az Azure-beli [delegált erőforrás-kezeléshez szükséges ügyfelek bevezetéséhez](azure-delegated-resource-management.md)fontos tisztában lennie azzal, hogyan működnek a Azure Active Directory (Azure ad) bérlők, a felhasználók és a szerepkörök, valamint hogyan használhatók az Azure Lighthouse-forgatókönyvekben.

A *bérlő* az Azure ad dedikált és megbízható példánya. Az egyes bérlők általában egyetlen szervezetnek felelnek meg. Az Azure-beli delegált erőforrás-kezelés lehetővé teszi az erőforrások logikai kivetítését az egyik bérlőről egy másik bérlőre. Ez lehetővé teszi a bérlők felügyeletét (például egy szolgáltatóhoz tartozót) a delegált erőforrások elérésére az ügyfél bérlője számára, vagy lehetővé teszi, hogy [több Bérlővel rendelkező vállalatok központosítsák a felügyeleti műveleteiket](enterprise.md).

Ahhoz, hogy ez a logikai leképezés elérhető legyen, előfizetést (vagy egy vagy több, előfizetésen belüli erőforráscsoportot) kell előkészíteni az ügyfél bérlője *számára az* Azure-beli delegált erőforrás-kezeléshez. Ez a [bevezetési folyamat Azure Resource Manager-sablonokkal](../how-to/onboard-customer.md) vagy [nyilvános vagy privát ajánlat Azure Marketplace-en való közzétételével](../how-to/publish-managed-services-offers.md)végezhető el.

Bármelyik bevezetési módszert választja, meg kell adnia az *engedélyeket*. Az egyes engedélyek egy felhasználói fiókot határoznak meg a bérlők kezelése szolgáltatásban, amely hozzáfér a delegált erőforrásokhoz, valamint egy beépített szerepkört, amely megadja, hogy az egyes felhasználók milyen engedélyeket kapnak ezekhez az erőforrásokhoz.

## <a name="role-support-for-azure-delegated-resource-management"></a>Szerepkör-támogatás az Azure-beli delegált erőforrás-kezeléshez

Az engedélyezés meghatározásakor minden felhasználói fiókhoz hozzá kell rendelni a [szerepköralapú hozzáférés-vezérlés (RBAC) beépített szerepköreinek](../../role-based-access-control/built-in-roles.md)egyikét. Az egyéni szerepkörök és a [klasszikus előfizetés-rendszergazdai szerepkörök](../../role-based-access-control/classic-administrators.md) nem támogatottak.

Az Azure-beli delegált erőforrás-kezelés jelenleg az összes [beépített szerepkört](../../role-based-access-control/built-in-roles.md) támogatja, a következő kivételekkel:

- A [tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkör nem támogatott.
- A [DataActions](../../role-based-access-control/role-definitions.md#dataactions) engedéllyel rendelkező beépített szerepkörök nem támogatottak.
- A [felhasználói hozzáférés rendszergazdai](../../role-based-access-control/built-in-roles.md#user-access-administrator) beépített szerepköre támogatott, de csak azzal a korlátozott céllal, [hogy szerepköröket rendeljen hozzá egy felügyelt identitáshoz az ügyfél bérlője](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)számára. Ehhez a szerepkörhöz általában nem érvényesek más engedélyek. Ha megad egy felhasználót a szerepkörhöz, meg kell adnia azokat a beépített szerepkör (eke) t, amelyeket a felhasználó a felügyelt identitásokhoz hozzárendelhet.

> [!NOTE]
> Miután hozzáadta a megfelelő új beépített szerepkört az Azure-hoz, hozzá lehet rendelni [egy ügyfelet Azure Resource Manager-sablonok használatával](../how-to/onboard-customer.md). A [felügyelt szolgáltatásokra vonatkozó ajánlat közzétételekor](../how-to/publish-managed-services-offers.md)előfordulhat, hogy az újonnan hozzáadott szerepkör Cloud Partner Portal elérhetővé válik.

## <a name="best-practices-for-defining-users-and-roles"></a>Ajánlott eljárások felhasználók és szerepkörök definiálásához

Az engedélyek létrehozásakor javasoljuk a következő ajánlott eljárásokat:

- A legtöbb esetben egy Azure AD-felhasználói csoporthoz vagy egyszerű szolgáltatáshoz kell engedélyeket rendelni, nem pedig egyéni felhasználói fiókokhoz. Ez lehetővé teszi az egyes felhasználók hozzáférésének hozzáadását vagy eltávolítását anélkül, hogy a hozzáférési követelmények változásakor frissítenie és újból közzé kellene tennie a tervet.
- Ügyeljen arra, hogy kövesse a legalacsonyabb jogosultsági szint elvét, hogy a felhasználók csak a feladataik elvégzéséhez szükséges engedélyekkel rendelkezzenek, ami segít csökkenteni a véletlen hibák esélyét. További információ: [ajánlott biztonsági eljárások](../concepts/recommended-security-practices.md).
- Vegyen fel egy felhasználót a [felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörével](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) , így szükség esetén később is [eltávolíthatja a delegáláshoz való hozzáférést](../how-to/onboard-customer.md#remove-access-to-a-delegation) . Ha ez a szerepkör nincs hozzárendelve, a delegált erőforrásokat csak egy felhasználó távolíthatja el az ügyfél bérlője számára.
- Győződjön meg arról, hogy minden olyan felhasználónak, akinek meg kell [tekintenie a saját ügyfelek lapot a Azure Portal](../how-to/view-manage-customers.md) rendelkezik az [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkörrel (vagy egy másik beépített szerepkörrel, amely olvasói hozzáféréssel rendelkezik).

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [Az Azure-beli delegált erőforrás-kezelés ajánlott biztonsági eljárásait](recommended-security-practices.md).
- Az ügyfeleket az Azure-beli delegált erőforrás-kezeléshez [Azure Resource Manager sablonok használatával](../how-to/onboard-customer.md) vagy [egy magán-vagy nyilvános felügyelt szolgáltatás Azure Marketplace-re való közzétételével](../how-to/publish-managed-services-offers.md)teheti közzé.
