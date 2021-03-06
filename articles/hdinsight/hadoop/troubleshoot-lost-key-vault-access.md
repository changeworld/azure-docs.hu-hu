---
title: Az Azure HDInsight-fürtök lemezes titkosítása elveszti Key Vault hozzáférését
description: Hibaelhárítási lépések és lehetséges megoldások az Azure HDInsight-fürtökkel való interakció során felmerülő problémákhoz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.openlocfilehash: 2ae389be25cd8633a53a49cf000796c1510733a1
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965163"
---
# <a name="scenario-azure-hdinsight-clusters-with-disk-encryption-lose-key-vault-access"></a>Forgatókönyv: a lemez titkosításával rendelkező Azure HDInsight-fürtök elvesztik Key Vault hozzáférését

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

A Resource Health Center (RHC) riasztás, `The HDInsight cluster is unable to access the key for BYOK encryption at rest`, Bring Your Own Key (BYOK) fürtök esetében látható, ahol a fürtcsomópontok nem férnek hozzá az ügyfelekhez Key Vault (KV). Hasonló riasztások is megtekinthetők az Apache Ambari felhasználói felületén.

## <a name="cause"></a>Ok

A riasztás biztosítja, hogy a KV elérhető legyen a fürtcsomópontok között, így biztosítva a hálózati kapcsolat, a KV Health és a hozzáférési szabályzatot a felhasználóhoz rendelt felügyelt identitáshoz. Ez a riasztás csak a közelgő, a csomópontok újraindítására vonatkozó figyelmeztetést jeleníti meg, a fürt továbbra is működni fog, amíg a csomópontok újraindulnak.

Keresse meg az Apache Ambari felhasználói felületét, ahol további információkat talál a **lemezes titkosítási Key Vault állapotáról**. Ez a riasztás részletesen ismerteti az ellenőrzési hibák okát.

## <a name="resolution"></a>Felbontás

### <a name="kvaad-outage"></a>KV/HRE leállás

További részletekért tekintse meg a [Azure Key Vault rendelkezésre állási és redundancia](../../key-vault/key-vault-disaster-recovery-guidance.md) és az Azure status oldalát https://status.azure.com/

### <a name="kv-accidental-deletion"></a>KV véletlen törlés

* Állítsa vissza a Deleted Key on KV-ot az automatikus helyreállításhoz. További információ: [Recover Deleted Key](https://docs.microsoft.com/rest/api/keyvault/recoverdeletedkey).
* Érje el a KV-csapatot a véletlen törlésből való helyreállításhoz.

### <a name="kv-access-policy-changed"></a>A KV hozzáférési szabályzat megváltozott

Állítsa vissza a hozzáférési házirendeket a felhasználóhoz rendelt felügyelt identitáshoz, amely a KV-hoz való hozzáféréshez van rendelve a HDI-fürthöz.

### <a name="key-permitted-operations"></a>Kulcs engedélyezett műveletei

A KV-ban minden egyes kulcs esetében kiválaszthatja az engedélyezett műveletek készletét. Győződjön meg arról, hogy a BYOK kulcshoz engedélyezve vannak a becsomagolási és kicsomagolási műveletek.

### <a name="expired-key"></a>Lejárt kulcs

Ha a lejárati idő betelt, és a kulcs nem forog el, állítsa vissza a kulcsot a Backup HSM-ből, vagy forduljon a KV csapathoz, és törölje a lejárati dátumot.

### <a name="kv-firewall-blocking-access"></a>A KV tűzfal blokkolja a hozzáférést

Javítsa ki a KV-os tűzfal beállításait, hogy a BYOK-fürtcsomópontok hozzáférhessenek a KV-hoz.

### <a name="nsg-rules-on-virtual-network-blocking-access"></a>NSG szabályok a virtuális hálózat blokkolja a hozzáférést

Keresse meg a fürthöz csatolt virtuális hálózathoz társított NSG-szabályokat.

## <a name="mitigation-and-prevention-steps"></a>Enyhítő és megelőzési lépések

### <a name="kv-accidental-deletion"></a>KV véletlen törlés

* Key Vault konfigurálása [erőforrás-zárolási készlettel](../../azure-resource-manager/management/lock-resources.md).
* Biztonsági mentést készíthet a hardveres biztonsági modul kulcsaira.

### <a name="key-deletion"></a>Kulcs törlése

A kulcs törlése előtt törölni kell a fürtöt.

### <a name="kv-access-policy-changed"></a>A KV hozzáférési szabályzat megváltozott

A hozzáférési szabályzatok rendszeres naplózása és tesztelése.

### <a name="expired-key"></a>Lejárt kulcs

* A kulcsok biztonsági mentése a HSM-be.
* Lejárati készlet nélküli kulcsot használjon.
* Ha a lejáratot be kell állítani, a kulcsokat a lejárati dátum előtt kell elforgatni.

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiókot a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
