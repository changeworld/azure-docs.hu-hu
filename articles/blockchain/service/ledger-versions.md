---
title: Azure Blockchain Service Ledger-verziók, javítás, & frissítés
description: Az Azure Blockchain szolgáltatás támogatott főkönyvek verzióinak áttekintése, beleértve a rendszerek javításával, a rendszer által felügyelt és a felhasználó által felügyelt frissítésekkel kapcsolatos házirendeket is.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325190"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Támogatott Azure Blockchain Service Ledger-verziók

Az Azure Blockchain szolgáltatás az Azure Blockchain Service-ben konzorciumként azonosított Ethereum-alapú [kvórumot](https://www.goquorum.com/developers) használ, amely az ismert résztvevők egy csoportján belüli privát tranzakciók feldolgozására lett tervezve.

Az Azure Blockchain szolgáltatás jelenleg támogatja a [kvórum Version 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) és [Tessera Transaction Manager](https://github.com/jpmorganchase/tessera)verzióját.

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése

A kvórumon belüli verziószámozás a fő, a másodlagos és a javítási kiadásokon keresztül történik. Ha például a kvórum verziója 2.0.1, a kiadás típusa a következőképpen lesz kategorizálva:

|Fő | Alverzió  | Javítás  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Az Azure Blockchain szolgáltatás a kvórumról elérhetővé tett 30 napon belül automatikusan frissíti a kvórum javítását a meglévő futó tagoknak.

## <a name="availability-of-new-ledger-versions"></a>Új Főkönyv-verziók rendelkezésre állása

Az Azure Blockchain szolgáltatás a kvórum főkönyvének legújabb fő és alverzióit a kvórum gyártójától 60 napon belül elérhetővé teszi. Egy új tag és konzorcium kiépítés esetén legfeljebb négy kisebb kiadást biztosítunk a konzorciumok számára. A verzióról a verzióra való verziófrissítés jelenleg nem támogatott. Ha például a 2. x verziót futtatja, a 3. x verzióra való frissítés jelenleg nem támogatott. Hasonlóképpen, ha az 2,2-es verziót futtatja, a 2,3-es verzióra való frissítés jelenleg nem támogatott.

## <a name="next-steps"></a>Következő lépések

[Korlátok az Azure Blockchain szolgáltatásban](limits.md)
