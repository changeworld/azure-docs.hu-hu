---
title: Az Azure HPC cache áttekintése
description: Leírja az Azure HPC cache-t, amely egy fájl-hozzáférési gyorsító megoldás a nagy teljesítményű számítástechnika számára
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 2a008d22de5df8d091e868153205697b4bb343ee
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241191"
---
# <a name="what-is-azure-hpc-cache"></a>Mi az az Azure HPC Cache?

Az Azure HPC cache nagy teljesítményű számítástechnikai (HPC) feladatokhoz biztosít hozzáférést az adatokhoz. Az Azure-ban tárolt fájlok gyorsítótárazásával az Azure HPC cache a Felhőbeli számítástechnika méretezhetőségét a meglévő munkafolyamatba helyezi. Ez a szolgáltatás még olyan munkafolyamatok esetében is használható, ahol az adatait WAN-kapcsolatokon keresztül tárolják, például a helyi Datacenter hálózati tároló (NAS) környezetében.

Az Azure HPC cache könnyen indítható és figyelhető a Azure Portal. A meglévő NFS-tárolók vagy új blob-tárolók az összesített névtér részévé válhatnak, ami egyszerűvé teszi az ügyfelek hozzáférését, még akkor is, ha megváltoztatja a háttérbeli tároló célját.

## <a name="use-cases"></a>Használati esetek

Az Azure HPC cache a következőhöz hasonló munkafolyamatok esetében növeli a termelékenységet:

* Olvasási – nagy mennyiségű fájl-hozzáférési munkafolyamat
* Az NFS-en keresztül elérhető tárolóban, az Azure blobban vagy mindkettőben tárolt adattárolás
* Akár 75 000 CPU-magokból álló számítási farmok

Az Azure HPC cache számos különböző munkafolyamathoz hozzáadható a legkülönbözőbb iparágakban. Minden olyan rendszer, amelyben nagy mennyiségű gépnek kell hozzáférni egy bizonyos méretű fájlhoz, és az alacsony késéssel jár, a szolgáltatás előnyeit élvezheti. Az alábbi fejezetek konkrét példákat biztosítanak.

### <a name="visual-effects-vfx-rendering"></a>Vizuális effektek (VFX) renderelése

A Media és a Entertainment szolgáltatásban az Azure HPC cache felgyorsíthatja az adathozzáférést az időkritikus megjelenítési projektekhez. A VFX renderelési munkafolyamatainak gyakran nagy számú számítási csomópont általi feldolgozást kell végezniük. Ezen munkafolyamatok esetében általában egy helyszíni NAS-környezet található. Az Azure HPC cache képes a felhőben tárolt fájlok gyorsítótárazására a késés csökkentése és az igény szerinti renderelés rugalmasságának növelése érdekében.

### <a name="life-sciences"></a>Élettudományok

Számos élettudományok munkafolyamata kihasználhatja a kibővíthető fájl gyorsítótárazását.

Egy Kutatóintézet, amely a genomikai elemzési munkafolyamatokat az Azure-ba szeretné irányítani, egyszerűen átválthatja őket az Azure HPC cache használatával. Mivel a gyorsítótár a POSIX-fájlok elérését biztosítja, nincs szükség ügyféloldali módosításra a meglévő ügyfél-munkafolyamat felhőben való futtatásához.

Az Azure HPC cache emellett kihasználható az olyan feladatok hatékonyságának javításához is, mint a másodlagos elemzés, a farmakológiai szimuláció vagy az AI-vezérelt rendszerképek elemzése.

### <a name="financial-services-analytics"></a>Pénzügyi szolgáltatások elemzése

Az Azure HPC cache üzembe helyezésével felgyorsíthatja a mennyiségi elemzési számításokat, a kockázatelemzési munkaterheléseket és a Monte Carlo szimulációkat, hogy a pénzügyi szolgáltatások vállalatai jobban betekintést nyerjenek a stratégiai döntések elvégzésére.

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az Azure HPC cache a következő Azure-régiókban érhető el:

* USA keleti régiója
* USA 2. keleti régiója
* Észak-Európa
* Nyugat-Európa
* Délkelet-Ázsia
* Sydney
* USA nyugati régiója, 2.
* Dél-Korea középső régiója

A legfrissebb rendelkezésre állási információkért tekintse meg az [Azure HPC cache-termék oldalát](https://azure.microsoft.com/services/hpc-cache) .

## <a name="service-availability"></a>Szolgáltatás rendelkezésre állása

Az Azure HPC cache használatával használni kívánt előfizetésekhez hozzáférést kell kérnie. Ez a korlátozás segít a szolgáltatás minőségének biztosításában az általánosan elérhető kezdeti hónapokban.

Hozzáférés kérése az [űrlap](https://aka.ms/onboard-hpc-cache)kitöltésével. Miután hozzáadta az előfizetést a hozzáférési listához, létrehozhat gyorsítótárat is.

## <a name="next-steps"></a>Következő lépések

* A képességeivel kapcsolatos további információkért olvassa el az [Azure HPC cache-termék oldalát](https://azure.microsoft.com/services/hpc-cache)
* További tudnivalók a termékek [előfeltételeiről](hpc-cache-prereqs.md)
* [Azure HPC-gyorsítótár létrehozása](hpc-cache-create.md) a Azure Portal
