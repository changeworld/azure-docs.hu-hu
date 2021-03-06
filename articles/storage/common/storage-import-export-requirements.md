---
title: Az Azure Import/Export szolgáltatás követelményei |} A Microsoft Docs
description: Ismerje meg az Azure Import/Export szolgáltatás szoftveres és hardveres követelményekkel.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 58997b20c01f33037a5e5e149caa59e1630373ff
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255313"
---
# <a name="azure-importexport-system-requirements"></a>Az Azure Import/Export rendszerre vonatkozó követelmények

Ez a cikk ismerteti az Azure Import/Export szolgáltatás a fontos követelmények. Azt javasoljuk, hogy mielőtt, tekintse át az információkat gondosan meg az Import/Export szolgáltatás használata, majd vissza, szükség esetén a művelet során.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A merevlemezek a WAImportExport eszközzel történő előkészítéséhez a **BitLocker meghajtótitkosítást támogató következő 64 bites operációs rendszer** támogatott.


|Platform |Verzió |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> A Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Windows-ügyfél szükséges szoftverek

|Platform |Verzió |
|---------|---------|
|.NET-keretrendszer    | 4.5.1       |
| BitLocker        |  \_          |


## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

Az Azure Import/Export szolgáltatás támogatja a következő típusú storage-fiókok:

- Standard általános célú v2 Storage-fiókok (a legtöbb esetben ajánlott)
- Blob Storage-fiókok
- Általános célú v1 Storage-fiókok (klasszikus vagy Azure Resource Manager üzemelő példányok),

A Storage-fiókokkal kapcsolatos további információkért lásd: az [Azure Storage-fiókok áttekintése](storage-account-overview.md).

Minden egyes feladat előfordulhat, hogy használható-adatok áthelyezésénél csak egy storage-fiókból. Más szóval egy egyetlen importálási/exportálási feladat terjedhetnek ki több tárfiókon keresztül. További információ az új Storage-fiókok létrehozásáról: [Storage-fiók létrehozása](storage-account-create.md).

> [!IMPORTANT]
> Az Azure import export szolgáltatás nem támogatja azokat a tárolási fiókokat, amelyeken engedélyezve van a [Virtual Network szolgáltatási végpontok](../../virtual-network/virtual-network-service-endpoints-overview.md) funkció. 

## <a name="supported-storage-types"></a>Támogatott tárolási típus

A tárolási típusok a következők közül az Azure Import/Export szolgáltatás használata támogatott.


|Feladat  |Storage szolgáltatás |Támogatott  |Nem támogatott  |
|---------|---------|---------|---------|
|Importálás     |  Azure Blob Storage <br><br> Az Azure File storage       | Támogatott Blokkblobok és lapblobok <br><br> Támogatott fájlok          |
|Exportálás     |   Azure Blob Storage       | A blokkblobok, lapblobok és hozzáfűző blobok támogatott         | Az Azure Files nem támogatott


## <a name="supported-hardware"></a>Támogatott hardveres

Az Azure Import/Export szolgáltatás az adatok másolása támogatott lemez szükséges.

### <a name="supported-disks"></a>Támogatott lemezek

Lemezek a következők közül az Import/Export szolgáltatás használata támogatott.


|Lemez típusa  |Méret  |Támogatott |
|---------|---------|---------|
|SSD    |   2,5"      |SATA III          |
|HDD     |  2,5"<br>3,5"       |SATA II, SATA III.         |

A következő típusú lemezek nem támogatottak:
- USBs.
- Külső HDD beépített USB-adapterrel.
- Külső HDD házán belüli lemezek.

Egy egyetlen importálási/exportálási feladat veheti fel:
- Legfeljebb 10 HDD és SSD-kkel.
- HDD és SSD és méretű vegyesen.

Nagy számú meghajtók oszlik el több feladat között, és ott nem hozható létre a feladatok száma korlátlan. Az importálási feladatokhoz csak az első adatmennyiség a meghajtón dolgoz fel. Az adatmennyiség NTFS fájlrendszerrel kell formázni.

Ha a merevlemez-meghajtók előkészítése, és a WAImportExport eszköz használata az adatok másolásának, külső USB-adaptereket is használhat. Legtöbb megoldásszolgáltatóknál USB 3.0-s vagy újabb adaptereket működnie kell.


## <a name="next-steps"></a>Következő lépések

* [A WAImportExport eszköz beállítása](storage-import-export-tool-how-to.md)
* [Adatátvitel az AzCopy parancssori segédprogrammal](storage-use-azcopy.md)
* [Azure importálási REST API minta](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
