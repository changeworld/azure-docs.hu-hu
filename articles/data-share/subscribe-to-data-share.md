---
title: 'Oktatóanyag: & fogadása az Azure-adatmegosztásról'
description: Oktatóanyag – az Azure-adatmegosztás használatával elfogadhatja és fogadhatja az adatfogadást
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 5b7d9cd7e7d438cf2beac76d5d8bcc78d377a8f4
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083103"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Oktatóanyag: az Azure-adatmegosztás használatával fogadja el és fogadja az adatfogadást  

Ebből az oktatóanyagból megtudhatja, hogyan fogadhatja el az adatmegosztási meghívásokat az Azure-adatmegosztás használatával. Megtudhatja, hogyan fogadhatja Önnel az Önnel megosztott adatmennyiséget, valamint hogyan engedélyezheti a rendszeres frissítési időközt annak biztosítása érdekében, hogy mindig a legfrissebb pillanatképet kapjon az Önnel megosztott adatokról. 

> [!div class="checklist"]
> * Azure-beli adatmegosztási meghívás elfogadása
> * Azure-beli adatmegosztási fiók létrehozása
> * Adja meg az adatai célhelyét
> * Előfizetés létrehozása az adatmegosztásra ütemezett frissítéshez

## <a name="prerequisites"></a>Előfeltételek
Az adatmegosztási Meghívások elfogadása előtt számos Azure-erőforrást kell kiépíteni, amelyek alább láthatók. 

Az adatmegosztási Meghívások elfogadása előtt győződjön meg arról, hogy az összes előfeltétel befejeződik. 

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .
* Egy adatmegosztás meghívása: Microsoft Azure egy meghívót az "Azure adatmegosztási meghívása **<yourdataprovider@domain.com>** " című témakörben.
* Regisztrálja a [Microsoft. DataShare erőforrás-szolgáltatót](concepts-roles-permissions.md#resource-provider-registration) az Azure-előfizetésben, amelyben létre fog hozni egy adatmegosztási erőforrást és az Azure-előfizetést, ahol a cél Azure-adattárolók találhatók.

### <a name="receive-data-into-a-storage-account"></a>Adatfogadás egy Storage-fiókba: 

* Azure Storage-fiók: Ha még nem rendelkezik ilyennel, létrehozhat egy [Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)is. 
* A Storage-fiókba való írásra vonatkozó engedély, amely megtalálható a *Microsoft. Storage/storageAccounts/Write*szolgáltatásban. Ez az engedély a közreműködő szerepkörben található. 
* Jogosultság a szerepkör-hozzárendelés hozzáadásához a Storage-fiókhoz, amely megtalálható a *Microsoft. Authorization/szerepkör-hozzárendelésekben/írásban*. Ez az engedély létezik a tulajdonosi szerepkörben.  

### <a name="receive-data-into-a-sql-based-source"></a>Adatfogadás SQL-alapú forrásba:

* A *Microsoft. SQL/Servers/Databases/Write*adatbázisban található SQL Server-adatbázisba való írásra vonatkozó engedély. Ez az engedély a közreműködő szerepkörben található. 
* Az adatmegosztási erőforrás felügyelt identitására vonatkozó engedély a Azure SQL Database vagy Azure SQL Data Warehouse eléréséhez. Ezt a következő lépések végrehajtásával teheti meg: 
    1. Állítsa be saját magát az SQL Server Azure Active Directory-rendszergazdájaként.
    1. Kapcsolódjon a Azure SQL Database/adattárházhoz Azure Active Directory használatával.
    1. A lekérdezés-szerkesztő (előzetes verzió) használatával hajtsa végre a következő parancsfájlt, hogy az adatmegosztás felügyelt identitását "db_datareader, db_datawriter, db_ddladmin" értékkel adja hozzá. Active Directory használatával kell kapcsolódnia, nem SQL Server a hitelesítéshez. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Vegye figyelembe, hogy a *< share_acc_name >* az adatmegosztási erőforrás neve. Ha még nem hozott létre adatmegosztási erőforrást, később is visszatérhet ehhez az előfeltételhöz.         

* Ügyfél IP-SQL Server tűzfal-hozzáférés. Ezt a következő lépések végrehajtásával teheti meg: 
    1. A Azure Portal található SQL Serverben navigáljon a *tűzfalak és a virtuális hálózatok* területére.
    1. Kattintson a **be** kapcsolóra az Azure-szolgáltatásokhoz való hozzáférés engedélyezéséhez.
    1. Kattintson az **+ ügyfél IP-** címének hozzáadása elemre, majd a **Mentés**gombra. Az ügyfél IP-címének módosítása változhat. Előfordulhat, hogy a következő alkalommal meg kell ismételni a folyamatot, amikor az adatok beérkeznek egy SQL-célhelyre Azure Portal. Hozzáadhat IP-címtartományt is. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Az Azure Adatkezelő-fürtbe történő adatfogadás: 

* Azure Adatkezelő-fürt ugyanabban az Azure-adatközpontban, mint az adatszolgáltató Adatkezelő-fürtje: Ha még nem rendelkezik ilyennel, létrehozhat egy [azure adatkezelő-fürtöt](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal). Ha nem ismeri az adatszolgáltató fürtje Azure-adatközpontját, később a folyamat során is létrehozhatja a fürtöt.
* Engedély az Azure Adatkezelő-fürtbe való írásra, amely megtalálható a *Microsoft. Kusto/fürtök/írás*szolgáltatásban. Ez az engedély a közreműködő szerepkörben található. 
* Jogosultság a szerepkör-hozzárendelés hozzáadásához az Azure Adatkezelő-fürthöz, amely megtalálható a *Microsoft. Authorization/szerepkör-hozzárendelésekben/írásban*. Ez az engedély létezik a tulajdonosi szerepkörben. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

## <a name="open-invitation"></a>Meghívás megnyitása

1. Az adatszolgáltatótól érkező meghívót a Beérkezett üzenetek között találja. A meghívó Microsoft Azure származik, és az **Azure-beli adatmegosztási meghívót <yourdataprovider@domain.com>** . Jegyezze fel a megosztás nevét annak biztosításához, hogy a megfelelő megosztást fogadja el, ha több meghívót is használ. 

1. Az Azure-beli meghívót a meghívás **megtekintése** lehetőségre kattintva tekintheti meg. Ekkor a kapott megosztások nézetre kerül.

   ![Meghívók](./media/invitations.png "Meghívások listája") 

1. Válassza ki a megtekinteni kívánt megosztást. 

## <a name="accept-invitation"></a>Meghívás elfogadása
1. Győződjön meg arról, hogy az összes mezőt felülvizsgálják, beleértve a használati **feltételeket**is. Ha elfogadja a használati feltételeket, a jelölőnégyzet bejelölésével jelezheti, hogy elfogadja. 

   ![Használati feltételek](./media/terms-of-use.png "Használati feltételek") 

1. A *cél adatmegosztási fiók*területen válassza ki azt az előfizetést és erőforráscsoportot, amelybe az adatmegosztást telepíteni fogja. 

   Az **adatmegosztási fiók** mezőben válassza az **új létrehozása** lehetőséget, ha nem rendelkezik meglévő adatmegosztási fiókkal. Ellenkező esetben válasszon ki egy meglévő adatmegosztási fiókot, amelyet el szeretne fogadni az adatmegosztásban. 

   A **Beérkezett megosztás neve** mezőben hagyhatja az alapértelmezett által megadott értéket, vagy megadhat egy új nevet a fogadott megosztáshoz. 

   ![Célként megadott adatmegosztási fiók](./media/target-data-share.png "Célként megadott adatmegosztási fiók") 

1. Ha jóváhagyta a használati feltételeket, és megadta a megosztás helyét, válassza az *elfogadás és konfigurálás*lehetőséget. A rendszer létrehoz egy megosztási előfizetést.

   A pillanatkép-alapú megosztáshoz a következő képernyő azt kéri, hogy válasszon ki egy cél Storage-fiókot az adatok másolásához. 

   ![Elfogadás beállításai](./media/accept-options.png "Elfogadás beállításai") 

   Ha azt szeretné, hogy a meghívót most is elfogadja, de később konfigurálja a célként megadott adattárt, válassza az *elfogadás és konfigurálás később*lehetőséget. A tárterület későbbi konfigurálásának folytatásához tekintse meg az [adatkészlet-hozzárendelések konfigurálása](how-to-configure-mapping.md) lapot, amely részletesen ismerteti az adatmegosztási konfiguráció folytatásának lépéseit. 

   A helyszíni megosztáshoz tekintse meg az [adatkészlet-hozzárendelések konfigurálása](how-to-configure-mapping.md) lapot, amely részletesen ismerteti az adatmegosztási konfiguráció folytatásának lépéseit. 

   Ha nem szeretné elfogadni a meghívót, válassza az *elutasítás*lehetőséget. 

## <a name="configure-storage"></a>A tárolás konfigurálása
1. A tároló *beállításai*területen válassza ki azt az előfizetést, erőforráscsoportot és Storage-fiókot, amelyet az adataihoz szeretne fogadni. 

   ![Cél tárolási beállításai](./media/target-storage-settings.png "Cél tárterülete") 

1. Az adatok rendszeres frissítésének engedélyezéséhez győződjön meg arról, hogy engedélyezi a pillanatkép-beállításokat. Vegye figyelembe, hogy ha az adatszolgáltató belefoglalta az adatmegosztásba, csak egy pillanatkép-beállításra vonatkozó ütemterv jelenik meg. 

   ![Pillanatkép-beállítások](./media/snapshot-settings.png "Pillanatkép-beállítások") 

1. Kattintson a *Mentés* gombra. 

> [!IMPORTANT]
> Ha SQL-alapú adatokat kap, és ezeket az adatokat SQL-alapú forrásként szeretné fogadni, látogasson el az [adatkészlet-megfeleltetés konfigurálása](how-to-configure-mapping.md) útmutatóval, amelyből megtudhatja, hogyan konfigurálhat egy SQL Server az adatkészlet célhelyének megadásához. 

## <a name="trigger-a-snapshot"></a>Pillanatkép indítása
Ezek a lépések csak a pillanatkép-alapú megosztásra vonatkoznak.

1. A kapott megosztások – > Részletek lapon elindíthat egy pillanatképet a **Pillanatkép indítása**lehetőség kiválasztásával. Itt aktiválhatja az adatok teljes vagy növekményes pillanatképét. Ha az első alkalommal fogadja az adatait az adatszolgáltatótól, válassza a teljes másolás lehetőséget. 

   ![Pillanatkép indítása](./media/trigger-snapshot.png "Pillanatkép indítása") 

1. Ha a legutóbbi futtatási állapot *sikeres*, nyissa meg a cél adattárt a kapott adatértékek megtekintéséhez. Válassza az **adatkészletek**lehetőséget, majd kattintson a hivatkozásra a cél elérési úton. 

   ![Fogyasztói adatkészletek](./media/consumer-datasets.png "Fogyasztói adatkészlet megfeleltetése") 

## <a name="view-history"></a>Előzmények megtekintése
A pillanatképek előzményeinek megtekintéséhez navigáljon a fogadott megosztások – > előzmények elemre. Itt megtalálhatja az elmúlt 60 napban létrehozott összes pillanatkép előzményeit. 

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtanulta, hogyan fogadhat és fogadhat Azure-adatmegosztást. Ha többet szeretne megtudni az Azure-beli adatmegosztási fogalmakról, folytassa a [fogalmakkal: az Azure adatmegosztási terminológiáját](terminology.md).