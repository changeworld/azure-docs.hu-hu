---
title: Fájlok feltöltése Azure Media Services-fiókba az Azure StorSimple-ből | Microsoft Docs
description: Ez a cikk rövid áttekintést nyújt az Azure StorSimple Data Managerről. A cikk olyan oktatóanyagokra is hivatkozik, amelyekből megtudhatja, hogyan gyűjthet ki adatokat a StorSimple-ből, és töltheti fel azokat objektumként egy Azure Media Services-fiókba.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1dd09328-262b-43ef-8099-73241b49a925
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: c77b700cab4afd411c3a2df824ee8335cb394cda
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868310"
---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>Fájlok feltöltése Azure Media Services-fiókba az Azure StorSimple-ből  

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még [v3 a v2 migrálási útmutató](../latest/migrate-from-v2-to-v3.md)
>
> 
> Az Azure StorSimple Data Manager jelenleg privát előzetes verzióban érhető el. 
> 

## <a name="overview"></a>Áttekintés

A Media Services szolgáltatásban a digitális fájlok feltöltése egy adategységbe történik. Az Adategység tartalmazhat videót, hangot, képeket, miniatűröket, szövegsávokat és feliratfájlokat (valamint mindezen fájlok metaadatait). A fájlok feltöltése után a tartalom a felhőben lesz biztonságosan tárolva további feldolgozás és adatfolyam-továbbítás céljából.

Az [Azure StorSimple](https://docs.microsoft.com/azure/storsimple/) a felhőalapú tárolót a helyszíni tároló kiterjesztéseként használja, és automatikusan rétegzi az adatokat a helyszíni és a felhőalapú tárolók közt. A StorSimple-eszköz deduplikálja és tömöríti az adatokat, mielőtt a felhőbe küldené azokat, így nagyon hatékony eszköz a nagyméretű fájlok a felhőbe továbbításához. A [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md) szolgáltatás nyújtotta API-k segítségével az adatok kinyerhetők a StorSimple-ből, és megjeleníthetők AMS-objektumokként.

## <a name="get-started"></a>Bevezetés

1. [Hozzon létre egy Media Services-fiókot](media-services-portal-create-account.md), amelybe az objektumokat át szeretné vinni.
2. Regisztráljon a Data Manager előzetes verziójára a [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md) című cikkben leírtak szerint.
3. Hozzon létre egy StorSimple Data Manager-fiókot.
4. Hozzon létre egy adatátalakítási feladatot, amely a futtatásakor adatokat gyűjt egy StorSimple-eszközről, és továbbítja azokat objektumokként egy AMS-fiókba. 

    Amikor a feladat futni kezd, a rendszer létrehoz egy tárolási sort. A sorba az átalakított blobokkal kapcsolatos üzenetek kerülnek, amint a blobok elkészültek. A sor neve megegyezik a feladatdefiníció nevével. A sor használatával megállapíthatja, hogy mikor készül el egy adott objektum, és meghívhatja és futtathatja rajta a kívánt Media Services-műveletet. Például a sor használatával aktiválhat egy Azure Functions-függvényt, amely tartalmazza a szükséges Media Services-kódot.

## <a name="see-also"></a>Lásd még

[Feladatok aktiválása a .NET SDK-t használja a Data Managerben](../../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések

Most már kódolhatja a feltöltött adategységeket. További információ: [Encode Assets](media-services-portal-encode.md) (Adategységek kódolása).
