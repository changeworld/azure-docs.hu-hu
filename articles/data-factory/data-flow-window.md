---
title: Az adatfolyam-ablak átalakításának leképezése
description: Azure Data Factory leképezési adatfolyam ablakának átalakítása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: fa34def67d91332a00bf0ee92b365957a47f9616
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931486"
---
# <a name="azure-data-factory-window-transformation"></a>Azure Data Factory ablak átalakítása



Az ablak transzformációja az oszlopok ablakos összesítéseit fogja meghatározni az adatfolyamokban. A Kifejezésszerkesztő különböző típusú összesítéseket határozhat meg, amelyek az adat-vagy időablakokon alapulnak (SQL OVER záradék), például az ólom, a LAG, a NTILE, a CUMEDIST, a RANK stb.). A rendszer létrehoz egy új mezőt a kimenetben, amely tartalmazza ezeket az összesítéseket. A választható csoportosítási mezőket is felveheti.

![Ablak beállításai](media/data-flow/windows1.png "Windows 1")

## <a name="over"></a>felett
Állítsa be az oszlopok adatparticionálását az ablak átalakításához. Az SQL-ekvivalens az SQL-ben az over záradékban található ```Partition By```. Ha létre szeretne hozni egy számítást, vagy létre szeretne hozni egy kifejezést a particionáláshoz, akkor az oszlop neve fölé húzva, majd a "számított oszlop" lehetőséget kell választania.

![Ablak beállításai](media/data-flow/windows4.png "Windows 4")

## <a name="sort"></a>Rendezés
Az over záradék egy másik része a ```Order By```beállítására szolgál. Ezzel az adatrendezési sorrendet fogja beállítani. Ebben az oszlopban is létrehozhat egy kifejezést a rendezéshez.

![Ablak beállításai](media/data-flow/windows5.png "Windows 5")

## <a name="range-by"></a>Tartomány:
Ezután állítsa az ablakkeret keret nélküliként vagy korlátként. A nem kötött ablakkeret beállításához állítsa a csúszkát úgy, hogy mindkét végén fel legyen kötve. Ha a nem kötött és az aktuális sor közötti beállítást választja, akkor a kezdő és a záró értékeket kell megadnia. Mindkét érték pozitív egész szám lesz. Az adatokból relatív számokat vagy értékeket is használhat.

Az ablak csúszka két beállított értékkel rendelkezik: az aktuális sor előtti értékek és az aktuális sor utáni értékek. A kezdő és a záró eltolás megegyezik a csúszkán található két választóval.

![Ablak beállításai](media/data-flow/windows6.png "Windows 6")

## <a name="window-columns"></a>Ablak oszlopai
Végül a Kifejezésszerkesztő segítségével definiálhatja azokat az összesítéseket, amelyeket használni szeretne az Adatablakok, például a RANK, a COUNT, a MIN, a MAX, a SŰRŰ rang, az ólom, a késés stb. alapján.

![Ablak beállításai](media/data-flow/windows7.png "Windows 7")

Az ADF-adatáramlás kifejezésének nyelvén a Kifejezésszerkesztő használatával elérhető összesítési és elemzési függvények teljes listája itt található: https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>Következő lépések

Ha egy egyszerű csoportosítási összesítést keres, használja az [összesített transzformációt](data-flow-aggregate.md)
