---
title: Dav4 és Dasv4 sorozat – Azure Virtual Machines
description: A Dav4 és a Dasv4 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 809843bddc4216df4dab44330172d36778248b1a
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78162179"
---
# <a name="dav4-and-dasv4-series"></a>Dav4 és Dasv4 sorozat

A Dav4 sorozat és a Dasv4 sorozat új méretek az AMD 2.35 GHz EPYC<sup>TM</sup> 7452 processzorát egy többszálas konfigurációban, amely akár 256 MB L3-os gyorsítótárat is kínál, amelyek 8 GB-ot tesznek elérhetővé az általános célú számítási feladatok futtatásához. A Dav4-sorozat és a Dasv4-sorozat ugyanazokkal a memória-és lemez-konfigurációval rendelkezik, mint a D & Dsv3 sorozat.

## <a name="dav4-series"></a>Dav4 sorozat

ACU: 230-260

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

Élő áttelepítés: támogatott

Memória megőrzésének frissítései: támogatott

A Dav4-sorozat méretei a 2.35 GHz-es AMD EPYC<sup>TM</sup> 7452 processzoron alapulnak, amely növelheti a 3.35 GHz-es maximális gyakoriságot. A Dav4-sorozat méretei vCPU, memória és ideiglenes tárterület kombinációját nyújtják a legtöbb éles számítási feladathoz. Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. A prémium SSD használatához használja a Dasv4-méreteket. A Dasv4-méretek díjszabása és számlázási mérőszámai megegyeznek a Dav4 sorozattal.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Hálózati adapterek maximális száma/várt hálózati sávszélesség (MBps) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 / 1000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 / 2000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000 / 187 / 93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000 / 375 / 187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000 / 750 / 375 |8 / 16000 |
| Standard_D48a_v4 <sup>**</sup> | 48 | 192| 1200 | 32 | | |
| Standard_D64a_v4 <sup>**</sup> | 64 | 256 | 1600 | 32 | | |
| Standard_D96a_v4 <sup>**</sup> | 96 | 384 | 2400 | 32 | | |

<sup>**</sup> Ezek a méretek előzetes verzióban érhetők el.  Ha szeretné kipróbálni ezeket a nagyobb méreteket, Regisztráljon itt: [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dasv4-series"></a>Dasv4 sorozat

ACU: 230-260

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

Élő áttelepítés: támogatott

Memória megőrzésének frissítései: támogatott

A Dasv4-sorozat méretei a 2.35 GHz-es AMD EPYC<sup>TM</sup> 7452 processzoron alapulnak, amely növelheti a 3.35 GHz-es maximális gyakoriságot, és prémium SSD-t használhat. A Dasv4-sorozat méretei vCPU, memória és ideiglenes tárterület kombinációját nyújtják a legtöbb éles számítási feladathoz.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek maximális száma/várt hálózati sávszélesség (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000 / 32 (50)|3200 / 48|2 / 1000 |
| Standard_D4as_v4|4|16|32|8|8000 / 64 (100)|6400/96|2 / 2000 |
| Standard_D8as_v4|8|32|64|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000/255 (400)|25600 / 384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000/510 (800)|51200 / 768|8 / 16000 |
| Standard_D48as_v4 <sup>**</sup>|48|192|384|32| | | 
| Standard_D64as_v4 <sup>**</sup>|64|256|512|32| | | 
| Standard_D96as_v4 <sup>**</sup>|96|384|768|32| | | 

<sup>**</sup> Ezek a méretek előzetes verzióban érhetők el.  Ha szeretné kipróbálni ezeket a nagyobb méreteket, Regisztráljon itt: [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
