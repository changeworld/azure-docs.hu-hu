---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 72d87142f9b9c1f7bcb2b02281851bd1e29bc9c8
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262594"
---
Az Azure Managed Disks jelenleg négy lemez típust kínál, amelyek mindegyike konkrét felhasználói forgatókönyvek elérésére szolgál.

## <a name="disk-comparison"></a>Lemezek összehasonlítása

Az alábbi táblázat az ultra Disks, a prémium szintű SSD-meghajtók (SSD), a standard SSD és a standard merevlemez-meghajtók (HDD) összehasonlítását ismerteti a felügyelt lemezekhez, amelyek segítségével eldöntheti, hogy mi a használat.

|   | Ultravékony lemez   | prémium SSD   | standard SSD   | standard HDD   |
|---------|---------|---------|---------|---------|
|Lemez típusa   |SSD   |SSD   |SSD   |HDD   |
|Forgatókönyv   |I/o-igényes számítási feladatok, mint például a [SAP HANA](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md), a legfelső szintű adatbázisok (például SQL, Oracle) és az egyéb tranzakció-nagy számítási feladatok.   |Éles, teljesítményérzékeny számítási feladatok   |Webkiszolgálók, enyhén használt Nagyvállalati alkalmazások és fejlesztés/tesztelés   |Biztonsági mentés, nem kritikus, ritka hozzáférés   |
|Maximális lemez mérete   |65 536 gibibájtnak (GiB)    |32 767 GiB    |32 767 GiB   |32 767 GiB   |
|Maximális átviteli sebesség   |2 000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Maximális IOPS   |160 000    |20,000   |6,000   |2,000   |

## <a name="ultra-disk"></a>Ultravékony lemez

Az Azure Ultra Disks nagy teljesítményű, magas IOPS és konzisztens, alacsony késésű lemezes tárolást biztosít az Azure IaaS virtuális gépek számára. Az ultra-lemezek további előnyei közé tartozik a lemez teljesítményének dinamikus módosítása, valamint a számítási feladatok, anélkül, hogy újra kellene indítania a virtuális gépeket (VM). Az ultra-lemezek olyan adatigényes számítási feladatokhoz használhatók, mint a SAP HANA, a legfelső szintű adatbázisok és a tranzakció-nagy számítási feladatok. Az ultra lemezeket csak adatlemezként lehet használni. Javasoljuk, hogy a prémium SSD-ket operációsrendszer-lemezként használja.

### <a name="performance"></a>Teljesítmény

Ultra-lemez kiépítésekor egymástól függetlenül konfigurálhatja a lemez kapacitását és teljesítményét. Az ultra Disks számos rögzített méretben érhető el: 4 GiB-ig 64 TiB-ig, és egy rugalmas teljesítmény-konfigurációs modellt tartalmaz, amely lehetővé teszi a IOPS és az átviteli sebesség egymástól független konfigurálását.

Az ultra-lemezek főbb képességei a következők:

- Lemez kapacitása: az ultrakönnyű lemezek kapacitása 4 GiB-tól 64 TiB-ig terjed.
- Lemezes IOPS: az ultra Disks támogatja a 300 IOPS/GiB IOPS korlátait, legfeljebb 160 K IOPS. A kiépített IOPS eléréséhez győződjön meg arról, hogy a kiválasztott lemez IOPS kisebbek, mint a virtuális gép IOPS korlátja. A minimális garantált IOPS-érték 2 IOPS/GiB, a teljes alapkonfiguráció minimum 100 IOPS. Ha például 4 GiB Ultra-lemezzel rendelkezett, akkor nyolc IOPS helyett legalább 100 IOPS kell lennie.
- Lemez sebessége: az ultra Disks használatával az egyes kiépített IOPS 256 KiB/s-ra, a maximálisan 2000 MBps-ra vetítve (ahol MBps = 10 ^ 6 bájt/másodperc). Minden egyes kiépített IOPS esetében a minimális garantált átviteli sebesség 4KiB/s, a teljes alapértéknek legalább 1 MBps-nek kell lennie.
- Az ultra Disks támogatja a lemez teljesítmény-attribútumainak (IOPS és átviteli sebesség) beállítását futásidőben, anélkül, hogy leválasztja a lemezt a virtuális gépről. Ha a lemez teljesítményének átméretezési művelete egy lemezen van kiadva, akár egy óráig is eltarthat, amíg a változás ténylegesen érvénybe lép. Egy 24 órás időszakban legfeljebb négy teljesítmény-átméretezési művelet lehet. A teljesítmény-átméretezési művelet sikertelen a teljesítmény-sávszélességi kapacitás hiánya miatt.

### <a name="disk-size"></a>Lemezméret

|Lemez mérete (GiB)  |IOPS sapka  |Átviteli sebesség (MB/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9 600         |2,000         |
|64     |19 200         |2,000         |
|128     |38 400         |2,000         |
|256     |76 800         |2,000         |
|512     |80,000         |2,000         |
|1024 – 65536 (az ebben a tartományban lévő méretek 1 TiB-onként növekednek)     |160 000         |2,000         |

### <a name="ga-scope-and-limitations"></a>A GA hatóköre és korlátai

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]
