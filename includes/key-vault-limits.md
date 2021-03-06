---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2d16febd4676ca7ba763eb7bc6dcecda4608ebb5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224338"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Legfontosabb tranzakciók (a maximálisan engedélyezett tranzakciók 10 másodpercen belül, tároló régiónként<sup>1</sup>):

|Kulcs típusa|HSM-kulcs<br>Kulcs létrehozása|HSM-kulcs<br>Minden egyéb tranzakció|Szoftver kulcsa<br>Kulcs létrehozása|Szoftver kulcsa<br>Minden egyéb tranzakció|
|:---|---:|---:|---:|---:|
|RSA 2 048 – bites|5|1,000|10|2,000|
|RSA 3 072 – bites|5|250|10|500|
|RSA 4 096 – bites|5|125|10|250|
|ECC P-256|5|1,000|10|2,000|
|ECC P-384|5|1,000|10|2,000|
|ECC P-521|5|1,000|10|2,000|
|ECC-SECP256K1|5|1,000|10|2,000|

> [!NOTE]
> Az előző táblázatban azt láthatjuk, hogy az RSA 2 048 bites szoftverek kulcsai esetében 10 másodpercenként 2 000 GET tranzakció engedélyezett. Az RSA 2 048 bites HSM-kulcsok esetében 10 másodpercenként 1 000 GET tranzakció engedélyezett.
>
> A szabályozási küszöbértékek súlyozottak, és a kényszerítés az összegen alapul. Például az előző táblázatban látható módon, amikor az RSA HSM-kulcsok beolvasási műveleteit hajtja végre, a rendszer nyolc alkalommal drágább a 4 096 bites kulcsok és a 2 048 bites kulcsok összehasonlítására. Ennek oka, hogy 1000/125 = 8.
>
> Egy adott 10 másodperces intervallumban az Azure Key Vault-ügyfél csak a következő műveletek *egyikét* hajthatja végre, mielőtt a rendszer beolvassa a `429` szabályozási HTTP-állapotkódot:
> - 2 000 RSA 2 048-bites szoftver – kulcs lekérése tranzakciók
> - 1 000 RSA 2 048-bit HSM – kulcs lekérése tranzakció
> - 125 RSA 4 096-bit HSM – kulcs lekérése tranzakció
> - 124 RSA 4 096-bites HSM-Key GET tranzakciók és 8 RSA 2 048-bites HSM – kulcs-lekérési tranzakciók

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Titkok, felügyelt Storage-fiókok kulcsai és a tár tranzakciói:
| Tranzakció típusa | A maximálisan engedélyezett tranzakciók 10 másodpercen belül, a tároló régiónként<sup>1</sup> |
| --- | --- |
| Minden tranzakció |2,000 |

További információ a korlátozásoknak a határértékek túllépése esetén történő kezeléséről: [Azure Key Vault szabályozási útmutató](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> az összes tranzakciótípus esetében az előfizetésre vonatkozó korlát a Key Vault-korlátnál ötször van. Például HSM – az egyes előfizetésekhez tartozó egyéb tranzakciók 10 másodpercen belül 5 000 tranzakcióra korlátozódnak.
