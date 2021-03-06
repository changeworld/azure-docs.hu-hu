---
title: Azure Key Vault biztonság | Microsoft Docs
description: Azure Key Vault, kulcsok és titkos kódok hozzáférési engedélyeinek kezelése. Lefedi a Key Vault hitelesítési és engedélyezési modelljét, valamint a kulcstartó biztonságossá tételét.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 3cc5cb68f430ac8e5070b9c8c4a1aa0c28639311
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270900"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault biztonság

A Azure Key Vault használatakor meg kell adnia a titkosítási kulcsokat és a titkokat, például a tanúsítványokat, a kapcsolódási karakterláncokat és a jelszavakat a felhőben. Mivel bizalmas és üzleti szempontból kritikus fontosságú adatokat tárol, lépéseket kell tennie, hogy maximalizálja a tárolók és a bennük tárolt adatok biztonságát. Ez a cikk a Azure Key Vault biztonságának tervezésekor megfontolandó fogalmakat ismerteti.

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

Amikor kulcstartót hoz létre egy Azure-előfizetésben, az automatikusan társítva lesz az előfizetés Azure AD-bérlője számára. Az Azure AD-nek hitelesítenie kell azokat a személyeket, akik tartalmat próbálnak kezelni vagy beolvasni a tárból.

- A hitelesítés létrehozza a hívó identitását.
- Az engedélyezés meghatározza a hívó által végrehajtható műveleteket. A Key Vaultban történő engedélyezés a [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC) és a Azure Key Vault hozzáférési házirendek kombinációját használja.

### <a name="access-model-overview"></a>Hozzáférési modell áttekintése

A tárolók elérése két felületen vagy síkon történik. Ezek a síkok a felügyeleti sík és az adatsík.

- A *felügyeleti síkon* a Key Vault felügyeli, és a tárolók létrehozásához és törléséhez használt felület. A Key Vault tulajdonságait is beolvashatja, és kezelheti a hozzáférési házirendeket.
- Az *adatsíkok* lehetővé teszik a kulcstartóban tárolt adatmennyiség használatát. Kulcsok, titkos kódok és tanúsítványok hozzáadására, törlésére és módosítására is lehetőség van.

Ha a kulcstartót bármelyik síkon szeretné elérni, az összes hívót (felhasználót vagy alkalmazást) hitelesíteni és engedélyezni kell. Mindkét síkon Azure Active Directoryt (Azure AD) használ a hitelesítéshez. Az engedélyezéshez a felügyeleti sík szerepköralapú hozzáférés-vezérlést (RBAC) használ, és az adatsík egy Key Vault hozzáférési házirendet használ.

A mindkét síkon történő hitelesítéshez használt egyetlen mechanizmus modellje számos előnnyel jár:

- A szervezetek központilag vezérelhetik a hozzáférést a szervezet összes kulcstartója számára.
- Ha a felhasználó elhagyja, azonnal elveszíti a hozzáférést a szervezet összes kulcstartójának.
- A szervezetek az Azure AD lehetőségeit használva szabhatják testre a hitelesítést, például a többtényezős hitelesítés engedélyezésével a további biztonság érdekében.

### <a name="managing-administrative-access-to-key-vault"></a>A Key Vault rendszergazdai hozzáférésének kezelése

Amikor létrehoz egy kulcstartót egy erőforráscsoporthoz, az Azure AD használatával kezelheti a hozzáférést. A felhasználók vagy csoportok számára engedélyezheti az erőforráscsoport kulcstárolóinak kezelését. A megfelelő RBAC-szerepkörök hozzárendelésével megadhatja a hozzáférést egy adott hatóköri szinten. Ahhoz, hogy hozzáférést biztosítson egy felhasználónak a kulcstartók kezeléséhez, egy előre definiált `key vault Contributor` szerepkört kell hozzárendelni a felhasználóhoz egy adott hatókörben. A következő hatókörökhöz rendelhet hozzá RBAC-szerepköröket:

- **Előfizetés**: az előfizetés szintjén hozzárendelt RBAC-szerepkör az adott előfizetésen belüli összes erőforráscsoport és erőforrás esetében érvényes.
- **Erőforráscsoport**: az erőforráscsoport szintjén HOZZÁRENDELt RBAC-szerepkör az adott erőforráscsoport összes erőforrására vonatkozik.
- **Adott**erőforrás: egy adott erőforráshoz hozzárendelt RBAC-szerepkör az adott erőforrásra vonatkozik. Ebben az esetben az erőforrás egy adott kulcstartó.

Számos előre definiált szerepkör létezik. Ha egy előre meghatározott szerepkör nem felel meg az igényeinek, megadhatja saját szerepkörét. További információt a [RBAC: beépített szerepkörök](../role-based-access-control/built-in-roles.md)című témakörben talál.

> [!IMPORTANT]
> Ha egy felhasználó `Contributor` engedélyekkel rendelkezik egy kulcstartó felügyeleti síkon, a felhasználó egy Key Vault hozzáférési házirend beállításával saját maga is megadhatja az adatsíkon való hozzáférést. Szigorúan szabályozhatja, hogy ki `Contributor` szerepkör-hozzáférést a kulcstartóhoz. Győződjön meg arról, hogy csak a jogosult személyek férhetnek hozzá és kezelhetik a kulcstartókat, kulcsokat, titkos kulcsokat és tanúsítványokat.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Key Vault-adatelérés szabályozása

Key Vault hozzáférési házirendek külön engedélyeket biztosítanak a kulcsokhoz, titkokhoz vagy tanúsítványokhoz. A felhasználók csak a kulcsokhoz és a titkokhoz való hozzáférést adhatnak meg. A kulcsok, titkos kódok és tanúsítványok hozzáférési engedélyei a tároló szintjén kezelhetők.

> [!IMPORTANT]
> Key Vault hozzáférési házirendek nem támogatják a szemcsés, az objektum szintű engedélyeket, például egy adott kulcsot, titkos kulcsot vagy tanúsítványt. Ha a felhasználó engedélyt kap a kulcsok létrehozására és törlésére, akkor ezeket a műveleteket a kulcstartó összes kulcsán végrehajthatja.

A Key Vault hozzáférési házirendjeinek beállításához használja a [Azure Portal](https://portal.azure.com/), az [Azure CLI](../cli-install-nodejs.md), a [Azure POWERSHELL](/powershell/azureps-cmdlets-docs)vagy a [Key Vault felügyeleti REST API-kat](https://msdn.microsoft.com/library/azure/mt620024.aspx).

Az adatsíkok hozzáférését a [Azure Key Vault virtuális hálózati szolgáltatásbeli végpontjának](key-vault-overview-vnet-service-endpoints.md)használatával korlátozhatja. A [tűzfalat és a virtuális hálózati szabályokat](key-vault-network-security.md) egy további biztonsági réteghez is konfigurálhatja.

## <a name="network-access"></a>Hálózati hozzáférés

A tárolók expozíciójának csökkentése érdekében megadhatja, hogy mely IP-címek férhessenek hozzájuk. A Azure Key Vault Virtual Network szolgáltatásbeli végpontok lehetővé teszik, hogy korlátozza a hozzáférést egy adott virtuális hálózathoz. A végpontok azt is lehetővé teszik, hogy korlátozza a hozzáférést az IPv4 (Internet Protocol Version 4)-címtartományok listájához. A Key vaulthoz a forrásokon kívülről csatlakozó felhasználók hozzáférése megtagadva.

A tűzfalszabályok érvénybe léptetése után a felhasználók csak akkor tudják olvasni az Key Vault adatait, ha a kérésük engedélyezett virtuális hálózatokról vagy IPv4-címtartományok származnak. Ez a Azure Portal Key Vault elérésére is vonatkozik. Bár a felhasználók megkereshetik a kulcstartót a Azure Portalból, előfordulhat, hogy nem tudják listázni a kulcsokat, titkokat vagy tanúsítványokat, ha az ügyfélszámítógépük nem szerepel az engedélyezési listán. Ez hatással van a más Azure-szolgáltatások Key Vault választóra is. Előfordulhat, hogy a felhasználók megtekinthetik a kulcstárolók listáját, de nem listázják a kulcsokat, ha a tűzfalszabályok megakadályozzák az ügyfélszoftvert.

További információ a Azure Key Vault hálózati címek áttekintése [virtuális hálózati szolgáltatási végpontokról Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Figyelés

Key Vault naplózás a tárolón végrehajtott tevékenységekre vonatkozó adatokat takarít meg. Naplók Key Vault:

- Minden hitelesített REST API kérelem, beleértve a sikertelen kérelmeket is
  - Maga a kulcstartó műveletei. Ezek a műveletek magukban foglalják a létrehozási, törlési és hozzáférési szabályzatok beállítását, valamint a Key Vault-attribútumok, például címkék frissítését
  - A Key vaultban lévő kulcsokkal és titkos kulcsokkal kapcsolatos műveletek, beleértve a következőket:
    - A kulcsok vagy titkos kódok létrehozása, módosítása vagy törlése.
    - A kulcsok aláírása, ellenőrzése, titkosítása, visszafejtése, becsomagolása és kicsomagolása, a titkok beolvasása és a kulcsok és titkok listázása (és azok verziói).
- A 401-es választ eredményező, nem hitelesített kérelmek. Ilyenek például azok a kérelmek, amelyek nem rendelkeznek olyan tulajdonosi jogkivonattal, amely nem formázott vagy lejárt, vagy érvénytelen tokent tartalmaz.

A naplózási adatok a Key Vault műveletét követő 10 percen belül érhetők el. A naplókat a Storage-fiókban kezelheti.

- Az Azure szabványos hozzáférés-vezérlési módszereivel korlátozhatja a naplókhoz való hozzáférést, így megvédheti azokat.
- Törölje azokat a naplókat, amelyeket nem kíván megőrizni a tárfiókban.

A Storage-fiókok biztonságos kezelésével kapcsolatos javaslatért tekintse meg az [Azure Storage biztonsági útmutatóját](../storage/blobs/security-recommendations.md) .

## <a name="next-steps"></a>További lépések

- [Virtuális hálózati szolgáltatás végpontjai Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
- [RBAC: beépített szerepkörök](../role-based-access-control/built-in-roles.md)
- [virtuális hálózati szolgáltatás végpontjai Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
