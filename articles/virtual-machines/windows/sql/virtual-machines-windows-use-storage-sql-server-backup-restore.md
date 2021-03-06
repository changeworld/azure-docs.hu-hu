---
title: Az Azure Storage használata SQL Server biztonsági mentéshez és visszaállításhoz | Microsoft Docs
description: Ismerje meg, hogyan készíthet biztonsági mentést SQL Server az Azure Storage-ba. Ismerteti az SQL-adatbázisok Azure Storage-ba történő biztonsági mentésének előnyeit.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: cb19dc7262721e672bd3f54b32db9188dad7fee0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101894"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Az Azure Storage használata SQL Server biztonsági mentéshez és visszaállításhoz
## <a name="overview"></a>Áttekintés
A SQL Server 2012 SP1 CU2 kezdve a SQL Server biztonsági mentések közvetlenül az Azure Blob Storage szolgáltatásba is írhatók. Ezzel a funkcióval biztonsági mentést készíthet az Azure Blob service egy helyszíni SQL Server-adatbázissal vagy egy Azure-beli virtuális gép SQL Server adatbázisával. A felhőbe történő biztonsági mentés a rendelkezésre állás előnyeit, a korlátlan, földrajzilag replikált helyszíni tárterületet, valamint az adatok felhőbe történő áttelepítését is biztosítja. A biztonsági mentési vagy VISSZAÁLLÍTÁSi utasítások a Transact-SQL vagy a SMO használatával adhatók ki.

A SQL Server 2016 új képességeket vezet be; a [fájl-pillanatkép biztonsági mentés](https://msdn.microsoft.com/library/mt169363.aspx) használatával szinte azonnali biztonsági mentéseket és hihetetlenül gyors visszaállításokat végezhet.

Ez a témakör azt ismerteti, hogy miért dönthet úgy, hogy az Azure Storage-t használja az SQL-alapú biztonsági mentésekhez, majd leírja az érintett összetevőket. A cikk végén található források segítségével elérheti a bemutatókat és a további információkat, amelyekkel megkezdheti a szolgáltatás használatát a SQL Server biztonsági másolatokkal.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Az Azure Blob Service használatának előnyei SQL Server biztonsági mentésekhez
A SQL Server biztonsági mentésekor több kihívás is van. Ezek a kihívások közé tartoznak a tárolók kezelése, a tárolási hibák kockázata, a helyszíni tároláshoz való hozzáférés és a hardverkonfiguráció. A kihívások nagy része a SQL Server biztonsági mentések esetén az Azure Blob Store szolgáltatással foglalkozik. Vegye figyelembe a következő előnyöket:

* **Egyszerű használat**: A biztonsági másolatok Azure-blobokban való tárolása kényelmes, rugalmas és könnyen elérhető külső helyszínen is lehetséges. Ha a SQL Server biztonsági mentések számára a helyszíni tárterületet szeretné létrehozni, egyszerűen módosíthatja a meglévő parancsfájlokat/feladatokat, hogy a **biztonsági mentés URL-** szintaxisát használja. A telephelyen kívüli tárterületnek általában elég messzinek kell lennie az éles adatbázis helyétől, így elkerülhető egyetlen olyan katasztrófa, amely hatással lehet a helyszínen kívüli és az üzemi adatbázis helyeire is. Az [Azure-Blobok földrajzi replikálásának](../../../storage/common/storage-redundancy.md)kiválasztásával extra védelmi réteggel rendelkezik, amely hatással lehet az egész régióra.
* **Biztonsági mentési Archívum**: Az Azure Blob Storage szolgáltatás jobb alternatíva a gyakran használt szalagos lehetőség a biztonsági másolatok archiválásához. A szalagos tároláshoz fizikai szállításra lehet szükség a telephelyen kívüli létesítményre, valamint az adathordozók elleni védelemre vonatkozó mértékeket. A biztonsági mentések tárolása az Azure-ban Blob Storage egy azonnali, magasan elérhető és tartós archiválási lehetőséget biztosít.
* **Felügyelt hardver**: Az Azure-szolgáltatások nem rendelkeznek a hardveres felügyelettel. Az Azure-szolgáltatások kezelik a hardvert, és földrajzi replikálást biztosítanak a redundancia és a hardveres hibák elleni védelem érdekében.
* **Korlátlan tárterület**: Az Azure-Blobok közvetlen biztonsági mentésének engedélyezésével gyakorlatilag korlátlan tárhelyet érhet el. Azt is megteheti, hogy egy Azure-beli virtuálisgép-lemezre való biztonsági mentés a számítógép méretétől függően korlátozott. A biztonsági mentések esetében korlátozva van egy Azure-beli virtuális géphez csatolható lemezek száma. Ez a korlát 16 lemez egy extra nagy példányhoz, és kevesebb a kisebb példányok esetében.
* **Biztonsági mentés elérhetősége**: Az Azure-blobokban tárolt biztonsági másolatok bárhonnan és bármikor elérhetők, és könnyen elérhetők az Azure-beli virtuális gépeken futó helyszíni SQL Server vagy egy másik SQL Server számára, anélkül, hogy az adatbázis csatolását/leválasztását vagy letöltését kellene elvégeznie. és csatolja a VHD-t.
* **Költségek**: Csak a használt szolgáltatásért kell fizetnie. Költséghatékony lehet a helyszíni és a biztonsági mentési archiválási lehetőséggel. További információkért tekintse meg az [Azure díjszabási kalkulátor](https://go.microsoft.com/fwlink/?LinkId=277060 "díjszabását"), valamint az [Azure](https://go.microsoft.com/fwlink/?LinkId=277059 "") díjszabási cikkének díjszabását.
* **Tárolási Pillanatképek**: Ha az adatbázisfájlok egy Azure-blobban tárolódnak, és SQL Server 2016-et használ, a [fájl-pillanatkép biztonsági mentés](https://msdn.microsoft.com/library/mt169363.aspx) használatával szinte azonnali biztonsági mentéseket és hihetetlenül gyors visszaállításokat végezhet.

További részletek: [SQL Server biztonsági mentés és visszaállítás az Azure Blob Storage szolgáltatással](https://go.microsoft.com/fwlink/?LinkId=271617).

Az alábbi két szakaszban bemutatjuk az Azure Blob Storage szolgáltatást, beleértve a szükséges SQL Server összetevőket is. Fontos megérteni az összetevőket és azok interakcióját, hogy sikeresen használják a biztonsági mentést és a visszaállítást az Azure Blob Storage szolgáltatásból.

## <a name="azure-blob-storage-service-components"></a>Azure Blob Storage Service-összetevők
A következő Azure-összetevők az Azure Blob Storage szolgáltatásba történő biztonsági mentéskor használatosak.

| Összetevő | Leírás |
| --- | --- |
| **Tárfiók** |A Storage-fiók az összes tárolási szolgáltatás kiindulási pontja. Azure Blob Storage szolgáltatás eléréséhez először hozzon létre egy Azure Storage-fiókot. Az Azure Blob Storage szolgáltatással kapcsolatos további információkért lásd: [az azure blob Storage szolgáltatás használata](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Tároló** |A tároló Blobok egy csoportját biztosítja, és korlátlan számú blob tárolására képes. Ha SQL Server biztonsági másolatot szeretne készíteni egy Azure-Blob servicera, legalább a legfelső szintű tárolót kell létrehoznia. |
| **Blob** |Bármilyen típusú és méretű fájl. A Blobok a következő URL-formátummal érhetők el: **https://[Storage account]. blob. Core. Windows. net/[Container]/[blob]** . További információ a lapok Blobokról: a [blokk-és Blobok ismertetése](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Összetevők SQL Server
Az alábbi SQL Server összetevőket használja a rendszer az Azure Blob Storage szolgáltatásba történő biztonsági mentéshez.

| Összetevő | Leírás |
| --- | --- |
| **URL-cím** |Az URL-cím egy Uniform Resource Identifier (URI) értéket ad meg egy egyedi biztonságimásolat-fájlhoz. Az URL-cím a SQL Server biztonságimásolat-fájl helyének és nevének megadására szolgál. Az URL-címnek tényleges blobra kell mutatnia, nem csak egy tárolóra. Ha a blob nem létezik, a rendszer létrehozza. Ha meg van adva egy meglévő blob, a biztonsági mentés sikertelen lesz, kivéve, ha meg van adva a > formátum beállítással. A következő példa a biztonsági mentési parancsban megadott URL-címre mutat be: **http [s]://[storageaccount]. blob. Core. Windows. net/[Container]/[filename. bak]** . A HTTPS használata ajánlott, de nem kötelező. |
| **Hitelesítő adatok** |Az Azure Blob Storage szolgáltatáshoz való kapcsolódáshoz és hitelesítéshez szükséges információk tárolása hitelesítő adatként történik.  Ahhoz, hogy a SQL Server a biztonsági mentéseket egy Azure-Blobba vagy annak visszaállítására lehessen írni, létre kell hoznia egy SQL Server hitelesítő adatot. További információ: [SQL Server hitelesítő adat](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> Az SQL Server 2016 frissítve lett a blokkos Blobok támogatásához. Olvassa el [az oktatóanyagot: További részletek a Microsoft Azure Blob Storage szolgáltatással SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx) adatbázisokkal.
> 
> 

## <a name="next-steps"></a>További lépések
1. Ha még nem rendelkezik Azure-fiókkal, hozzon létre egyet. Ha kiértékeli az Azure-t, vegye figyelembe az [ingyenes próbaverziót](https://azure.microsoft.com/free/).
2. Ezután folytassa a következő oktatóanyagok egyikét, amely végigvezeti a Storage-fiók létrehozásán és a visszaállításon.
   
   * **SQL Server 2014**: [Oktatóanyag SQL Server 2014 biztonsági mentést és visszaállítást Microsoft Azure](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx)blob Storage szolgáltatáshoz.
   * **SQL Server 2016**: [Oktatóanyag A Microsoft Azure Blob Storage szolgáltatás használata SQL Server 2016-adatbázisokkal](https://msdn.microsoft.com/library/dn466438.aspx)
3. Tekintse át a [SQL Server Backup és Restore Microsoft Azure Blob Storage Service szolgáltatással](https://msdn.microsoft.com/library/jj919148.aspx)kezdődő további dokumentációt.

Ha bármilyen problémája van, tekintse át a [SQL Server biztonsági mentés az URL-címekkel kapcsolatos ajánlott eljárásokat és hibaelhárítást](https://msdn.microsoft.com/library/jj919149.aspx)ismertető témakört.

Más SQL Server biztonsági mentési és visszaállítási lehetőségekért lásd: [SQL Server Azure-beli biztonsági mentése és visszaállítása Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

