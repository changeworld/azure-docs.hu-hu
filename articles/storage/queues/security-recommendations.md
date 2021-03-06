---
title: Biztonsági javaslatok a várólista-tároláshoz
titleSuffix: Azure Storage
description: A várólista-tárolás biztonsági javaslatainak megismerése. Ennek az útmutatónak a megvalósítása a megosztott felelősségi modellben leírtak szerint biztosítja a biztonsági kötelezettségek teljesítését.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 1315e1f81ee32a544b623b7f3ffad61a7e7275d8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486286"
---
# <a name="security-recommendations-for-queue-storage"></a>Biztonsági javaslatok a várólista-tároláshoz

Ez a cikk biztonsági javaslatokat tartalmaz a várólista-tároláshoz. A javaslatok megvalósítása a közös felelősségi modellben leírtaknak megfelelően segíti a biztonsági kötelezettségek teljesítését. Ha többet szeretne megtudni arról, hogy a Microsoft hogyan teljesíti a szolgáltatói feladatokat, olvassa el a [megosztott felelősségek a felhőalapú számítástechnika](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf)terén című témakört.

A cikkben szereplő ajánlások némelyikét a Azure Security Center automatikusan nyomon követheti. A Azure Security Center az Azure-beli erőforrások védelmének első védelmi vonala. Azure Security Centerről a [Mi az Azure Security Center?](../../security-center/security-center-intro.md)című témakörben olvashat bővebben.

Azure Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát az esetleges biztonsági rések azonosítása érdekében. Ezután javaslatokat tesz a megoldására. Azure Security Center javaslatokkal kapcsolatos további információkért lásd: [biztonsági javaslatok a Azure Security Centerban](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Adatvédelem

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| A Azure Resource Manager telepítési modell használata | Új Storage-fiókok létrehozása a Azure Resource Manager üzembe helyezési modellel a fontos biztonsági fejlesztésekhez, beleértve a jobb hozzáférés-vezérlést (RBAC) és a naplózást, a Resource Manager-alapú üzembe helyezést és irányítást, a felügyelt identitásokhoz való hozzáférést, a hozzáférést a titkok Azure Key Vaultéhez és az Azure AD-alapú hitelesítéshez és az Azure Storage-adatokhoz és-erőforrásokhoz való hozzáférés engedélyezéséhez. Ha lehetséges, telepítse át a klasszikus üzemi modellt használó meglévő Storage-fiókokat a Azure Resource Manager használatára. További információ a Azure Resource Managerről: [Azure Resource Manager áttekintése](/azure/azure-resource-manager/resource-group-overview). | - |
| A **biztonságos átvitel szükséges** beállításának engedélyezése az összes Storage-fiókban | Ha engedélyezi a **biztonságos átvitel szükséges** beállítást, a Storage-fiókra irányuló összes kérést biztonságos kapcsolatokon keresztül kell végrehajtani. A HTTP-n keresztül végrehajtott kérelmek sikertelenek lesznek. További információ: [biztonságos átvitel megkövetelése az Azure Storage-ban](../common/storage-require-secure-transfer.md). | [Igen](../../security-center/security-center-sql-service-recommendations.md) |
| A komplex veszélyforrások elleni védelem engedélyezése az összes Storage-fiókhoz | Az Azure Storage komplex veszélyforrások elleni védelme egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kiaknázásához. A biztonsági riasztások Azure Security Center, ha a tevékenységben észlelt rendellenességek bekövetkeznek, és e-mailben is elküldik az előfizetési rendszergazdáknak, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálására és elhárítására vonatkozó javaslatokkal kapcsolatban. További információ: [Az Azure Storage komplex veszélyforrások elleni védelme](../common/storage-advanced-threat-protection.md). | [Igen](../../security-center/security-center-sql-service-recommendations.md) |
| Közös hozzáférésű aláírási (SAS-) tokenek korlátozása csak HTTPS-kapcsolatokra | HTTPS megkövetelése, ha az ügyfél SAS-jogkivonattal fér hozzá a várólista-adatforgalomhoz, segít csökkenteni a lehallgatás kockázatát. További információ: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../common/storage-sas-overview.md). | - |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| A Azure Active Directory (Azure AD) használata az üzenetsor-adatelérés engedélyezéséhez | Az Azure AD kiváló biztonságot és könnyű használatot biztosít a megosztott kulcson keresztül a várólista-tárolásra irányuló kérések engedélyezéséhez. További információ: az [Azure-blobok és-várólisták hozzáférésének engedélyezése Azure Active Directory használatával](../common/storage-auth-aad.md). | - |
| Ne feledje, hogy a legalacsonyabb jogosultsági szint a RBAC-on keresztüli Azure AD rendszerbiztonsági tag engedélyeinek hozzárendelésével | Ha egy szerepkört egy felhasználóhoz, csoporthoz vagy alkalmazáshoz rendel, a rendszerbiztonsági tag csak azokat az engedélyeket adja meg, amelyek szükségesek a feladataik elvégzéséhez. Az erőforrásokhoz való hozzáférés korlátozása segít megakadályozni az adataival való szándékos és rosszindulatú visszaéléseket. | - |
| A fiók-hozzáférési kulcsok biztonságossá tétele Azure Key Vault | A Microsoft azt javasolja, hogy az Azure AD használatával engedélyezze az Azure Storage-ba irányuló kéréseket. Ha azonban megosztott kulcsos hitelesítést kell használnia, akkor a fiók kulcsainak védelmét Azure Key Vault. A kulcsokat a Key vaultból is lekérheti futásidőben, az alkalmazással való mentés helyett. | - |
| A fiók kulcsai rendszeres újragenerálása | A fiókok kulcsainak rendszeres elforgatása csökkenti annak kockázatát, hogy az adatok rosszindulatú szereplőkkel legyenek kitéve. | - |
| Tartsa szem előtt a legalacsonyabb jogosultsági szintű rendszerbiztonsági tag számára az engedélyek SAS-hez való hozzárendelését. | SAS létrehozásakor csak azokat az engedélyeket kell megadnia, amelyekre az ügyfélnek szüksége van a funkció végrehajtásához. Az erőforrásokhoz való hozzáférés korlátozása segít megakadályozni az adataival való szándékos és rosszindulatú visszaéléseket. | - |
| Visszavonási tervvel kell rendelkeznie minden olyan SAS számára, amelyet az ügyfelek számára ad ki | Ha egy SAS biztonsága sérül, a lehető leghamarabb vissza kell vonnia az SAS-t. Ha vissza szeretne vonni egy felhasználói delegálási SAS-t, vonja vissza a felhasználói delegálási kulcsot, hogy gyorsan érvénytelenítse a kulcshoz társított összes aláírást. Egy tárolt hozzáférési szabályzattal társított szolgáltatási SAS visszavonásához törölheti a tárolt hozzáférési szabályzatot, átnevezheti a szabályzatot, vagy módosíthatja annak lejárati idejét egy múltbeli időpontra. További információ: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../common/storage-sas-overview.md).  | - |
| Ha egy szolgáltatás SAS-je nem egy tárolt hozzáférési szabályzathoz van társítva, akkor a lejárati időt állítsa egy órára vagy kevesebbre. | Nem lehet visszavonni egy olyan szolgáltatáshoz tartozó SAS-t, amely nincs hozzárendelve egy tárolt hozzáférési szabályzathoz. Emiatt a lejárati időt úgy kell korlátozni, hogy az SAS egy órán vagy kevesebb ideig érvényes legyen. | - |

## <a name="networking"></a>Hálózatkezelés

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| Tűzfalszabályok engedélyezése | A tűzfalszabályok konfigurálásával korlátozza a Storage-fiókhoz való hozzáférést a megadott IP-címekről vagy tartományokból származó kérelmekre, vagy egy Azure-Virtual Network (VNet) alhálózatok listájáról. A tűzfalszabályok konfigurálásával kapcsolatos további információkért lásd: [Azure file Sync proxy-és tűzfal-beállítások](../files/storage-sync-files-firewall-and-proxy.md). | - |
| Megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a Storage-fiókhoz | Ha bekapcsolja a tűzfalszabályok bekapcsolását a Storage-fiókhoz, az alapértelmezés szerint letiltja a bejövő adatkéréseket, kivéve, ha a kérelmek Azure-Virtual Network (VNet) vagy engedélyezett nyilvános IP-címeken belüli szolgáltatásból származnak. A letiltott kérések közé tartoznak a más Azure-szolgáltatások, a Azure Portal, a naplózási és a metrikai szolgáltatások, valamint így tovább. A többi Azure-szolgáltatástól érkező kéréseket engedélyezheti egy kivétel hozzáadásával, amely lehetővé teszi, hogy a megbízható Microsoft-szolgáltatások hozzáférjenek a Storage-fiókhoz. A megbízható Microsoft-szolgáltatások kivételének hozzáadásával kapcsolatos további információkért lásd: [Azure file Sync proxy-és tűzfalbeállítások](../files/storage-sync-files-firewall-and-proxy.md).| - |
| Privát végpontok használata | A privát végpontok magánhálózati IP-címet rendelnek hozzá az Azure-Virtual Network (VNet) a Storage-fiókhoz. A VNet és a Storage-fiók közötti összes forgalmat privát kapcsolaton keresztül biztosítja. A privát végpontokkal kapcsolatos további információkért lásd: [privát kapcsolódás a Storage-fiókhoz az Azure Private Endpoint használatával](../../private-link/create-private-endpoint-storage-portal.md). | - |
| Bizonyos hálózatokhoz való hálózati hozzáférés korlátozása | A hozzáférést igénylő ügyfelek hálózati hozzáférésének korlátozása csökkenti az erőforrások hálózati támadásoknak való kitettségét. | [Igen](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>Naplózás/figyelés

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| A kérések engedélyezésének nyomon követése | Az Azure Storage naplózásának engedélyezése az Azure Storage-ba irányuló kérelmek engedélyezésének nyomon követésére. A naplók azt jelzik, hogy egy kérelem névtelenül történt-e egy OAuth 2,0-token használatával, megosztott kulcs használatával vagy közös hozzáférésű aláírás (SAS) használatával. További információ: az [Azure Storage Analytics naplózása](../common/storage-analytics-logging.md). | - |

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security dokumentációja](https://docs.microsoft.com//azure/security/)
- [Biztonságos fejlesztői dokumentáció](https://docs.microsoft.com/azure/security/develop/).
