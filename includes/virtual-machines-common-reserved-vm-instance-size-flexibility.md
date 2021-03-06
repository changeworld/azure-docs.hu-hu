---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02-18-2020
ms.openlocfilehash: aebe8f05bd1a3607da6d1153c87490891fc6b299
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471455"
---
A fenntartott VM-példányok vásárlásakor optimalizálhatja a példány méretének rugalmasságát vagy a kapacitás prioritását. A fenntartott VM-példányok optimalizálási beállításának beállításával és módosításával kapcsolatos további információkért lásd [a fenntartott VM-példányok optimalizálási beállításának módosítása](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)című témakört.

Egy fenntartott virtuálisgép-példány, amely a példányok méretének rugalmasságára van optimalizálva, a megvásárolt foglalás a virtuális gépek (VM-EK) méretére is vonatkozhat ugyanabban a példány-méret rugalmassági csoportban. Ha például egy, a DSv2-sorozatban felsorolt virtuálisgép-mérethez (például Standard_DS5_v2) foglal le egy foglalást, akkor a foglalási kedvezmény a következő négy méretre is vonatkozhat, amely az adott példány méretének rugalmassági csoportjában szerepel:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

A foglalási kedvezmény azonban nem vonatkozik azokra a virtuális gépekre, amelyek a különböző példányszámú rugalmassági csoportokban vannak felsorolva, például a DSv2 Series nagy memóriában található SKU-ban: Standard_DS11_v2, Standard_DS12_v2 stb.

A példányok méretének rugalmassága csoportban a foglalási kedvezmény által érintett virtuális gépek száma a foglalás megvásárlásakor kiválasztott virtuálisgép-mérettől függ. Emellett a futtatott virtuális gépek méretétől is függ. A Ratio (arány) oszlop összehasonlítja az adott példány méretének rugalmassági csoportjának minden virtuálisgép-méretéhez tartozó relatív lábnyomot. A Ratio érték használatával kiszámíthatja, hogy a foglalási kedvezmény Hogyan vonatkozik a futtatott virtuális gépekre.

## <a name="examples"></a>Példák

Az alábbi példák a DSv2 sorozatú táblában lévő méreteket és arányokat használják.

A fenntartott VM-példányok olyan mérettel rendelkeznek, Standard_DS4_v2, ahol az arány vagy a relatív lábnyom a sorozat többi méretéhez képest 8.

- 1\. forgatókönyv: nyolc Standard_DS1_v2 méretű virtuális gép futtatása 1-es aránnyal. A foglalási kedvezmény mind a nyolc virtuális gép esetében érvényes.
- 2\. forgatókönyv: két Standard_DS2_v2 méretű virtuális gépet futtasson 2. arányban. Futtasson egy Standard_DS3_v2 méretű virtuális gépet is, amelynek a aránya 4. A teljes helyigény 2 + 2 + 4 = 8. Így a foglalási kedvezmény mindhárom virtuális gépre vonatkozik.
- 3\. forgatókönyv: egy Standard_DS5_v2 futtatása 16-os aránnyal. A foglalási kedvezmény a virtuális gép számítási díja felére vonatkozik.

A következő fejezetek azt mutatják be, hogy az azonos méretű adatsorozat-csoport milyen méreteket tartalmaz, ha olyan fenntartott VM-példányt vásárol, amely a példány méretének rugalmasságára optimalizált

## <a name="instance-size-flexibility-ratio-for-vms"></a>Példányok méretének rugalmassági aránya a virtuális gépeknél 

Az alábbi CSV a példányok méretének rugalmassági csoportjai, a ArmSkuName és az arányok.  

[Példány méretének rugalmassági aránya](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

Megtartjuk a fájl URL-címét és a séma által rögzített sémát, hogy programozott módon használhassa ezt a fájlt. Az adatközpont hamarosan elérhető lesz az API-n keresztül is.
