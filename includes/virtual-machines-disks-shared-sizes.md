---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 34699ed89e79448d66343021dd624cb872d0172d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471693"
---
Egyelőre csak a prémium SSD-k engedélyezhetik a megosztott lemezeket. A funkciót támogató lemezek mérete P15 és nagyobb. A különböző méretű lemezek eltérő `maxShares` korláttal rendelkezhetnek, amelyet a `maxShares` érték beállításakor nem lehet meghaladni.

Minden lemez esetében megadhat egy `maxShares` értéket, amely a lemezt egyidejűleg megosztható csomópontok maximális számát jelöli. Ha például egy 2 csomópontos feladatátvevő fürtöt szeretne beállítani, akkor állítsa be `maxShares=2`. A maximális érték egy felső határ. A csomópontok csatlakozhatnak vagy elhagyhatják a fürtöt (a lemez csatlakoztatása vagy leválasztása), feltéve, hogy a csomópontok száma a megadott `maxShares` értéknél kisebb.

> [!NOTE]
> A `maxShares` érték csak akkor állítható be vagy szerkeszthető, ha a lemez le van választva az összes csomópontról.

A következő táblázat a `maxShares` által megengedett maximális értékeket mutatja a lemez méretével:

|Lemezek mérete  |maxShares korlátja  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

A IOPS és a sávszélesség korlátozásait nem érinti a `maxShares` értéke. Egy P15-lemez Max IOPS például 1100, hogy maxShares = 1 vagy maxShares > 1.