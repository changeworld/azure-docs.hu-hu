---
title: Több ág a leképezési adatfolyamban
description: Adatfolyamok replikálása a leképezési adatfolyamban több ág
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 71fb9f1ba9952be0e6b3910dd1079aa6d3c0482d
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834516"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Új ág létrehozása a leképezési adatfolyamban

Egy új ág hozzáadásával több művelet és átalakítás végezhető el ugyanazon adatfolyamon. Az új ág hozzáadása akkor lehet hasznos, ha ugyanazt a forrást szeretné használni több mosogatóhoz, vagy az önkiszolgáló adategyesítéshez.

Az átalakítási listáról más átalakításokhoz hasonló új ág is felvehető. Az **új ág** csak akkor érhető el műveletként, ha már van egy meglévő átalakítás, amely azt követi, hogy milyen átalakítást végez az elágazásban.

![Új ág hozzáadása](media/data-flow/new-branch2.png "Új ág hozzáadása")

Az alábbi példában az adatfolyam beolvassa a taxi-adatforgalmat. A napi és a szállító által összesített kimenet megadása kötelező. Ahelyett, hogy két különálló adatfolyamatot hozna létre ugyanabból a forrásból, új ágat adhat hozzá. Így az összesítések ugyanúgy hajthatók végre, mint az adatfolyamatok részeként. 

![Új ág hozzáadása](media/data-flow/new-branch.png "Új ág hozzáadása")
