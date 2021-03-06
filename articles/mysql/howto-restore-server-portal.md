---
title: Biztonsági mentés és visszaállítás – Azure Portal – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan lehet visszaállítani egy kiszolgálót Azure Database for MySQL a Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: f8005bccf3a726a6022efdecb2eb5738669f3bc2
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74762624"
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-portal"></a>Kiszolgálók biztonsági mentése és visszaállítása Azure Database for MySQL a Azure Portal használatával

## <a name="backup-happens-automatically"></a>A biztonsági mentés automatikusan megtörténik
A visszaállítási funkciók engedélyezéséhez rendszeresen biztonsági mentést kell készíteni Azure Database for MySQL-kiszolgálókról. A szolgáltatás használatával visszaállíthatja a kiszolgálót és az összes adatbázisát egy korábbi időpontra, egy új kiszolgálón.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for MySQL-kiszolgáló és-adatbázis](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-backup-configuration"></a>Biztonsági mentési konfiguráció beállítása

A kiszolgáló létrehozásakor a **díjszabási** szinten ablakban beállíthatja, hogy a kiszolgálót helyileg redundáns biztonsági mentésekhez vagy földrajzilag redundáns biztonsági másolatokhoz konfigurálja.

> [!NOTE]
> A kiszolgáló létrehozása után a redundancia, földrajzilag redundáns vagy helyileg redundáns, nem állítható be.
>

Amikor a Azure Portalon keresztül hoz létre kiszolgálót, a **díjszabási rétegek** ablakban a **helyi redundáns** vagy **földrajzilag redundáns** biztonsági másolatokat választhatja a kiszolgáló számára. Ezen az ablakban is kiválaszthatja a **biztonsági mentés megőrzési időtartamát** – mennyi ideig (hány nap múlva) kívánja tárolni a kiszolgáló biztonsági másolatait.

   ![Díjszabási réteg – a biztonsági másolatok redundanciának kiválasztása](./media/howto-restore-server-portal/pricing-tier.png)

További információ ezekről az értékekről a létrehozás során: [Azure Database for MySQL Server](quickstart-create-mysql-server-database-using-azure-portal.md)gyors üzembe helyezése.

A biztonsági másolatok megőrzési időszaka a kiszolgálón a következő lépésekkel módosítható:
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki a Azure Database for MySQL-kiszolgálót. Ez a művelet megnyitja az **Áttekintés** lapot.
3. Válassza ki az **árképzési szintet** a menü **Beállítások**területén. A csúszka használatával megváltoztathatja a **biztonsági mentés megőrzési időtartamát** 7 és 35 nap között.
Az alábbi képernyőképen a 34 napra nőtt.
![biztonsági másolatok megőrzési időszaka nőtt](./media/howto-restore-server-portal/3-increase-backup-days.png)

4. A módosítás megerősítéséhez kattintson **az OK** gombra.

A biztonsági másolatok megőrzési időszaka azt szabályozza, hogy az adott időpontra visszamenőleges visszaállítás hogyan kérhető le, mert az elérhető biztonsági másolatokon alapul. Az időponthoz való visszaállítást a következő szakasz ismerteti részletesebben. 

## <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
Azure Database for MySQL lehetővé teszi, hogy visszaállítsa a kiszolgálót egy adott időpontra, és a-kiszolgáló egy új példányára. Ezt az új kiszolgálót használhatja az adatok helyreállításához, vagy az ügyfélalkalmazások erre az új kiszolgálóra mutatnak.

Ha például egy táblázatot véletlenül a mai napig dobott el, akkor a visszaállíthatja a déli idő előtt, és lekérheti a hiányzó táblát és adatforrást a kiszolgáló új példányáról. Az időponthoz való visszaállítás a kiszolgáló szintjén, nem pedig az adatbázis szintjén történik.

A következő lépésekkel állíthatja vissza a kiszolgálót egy időpontra:
1. A Azure Portal válassza ki a Azure Database for MySQL kiszolgálót. 

2. A kiszolgáló **Áttekintés** lapjának eszköztárán válassza a **visszaállítás**lehetőséget.

   ![Azure Database for MySQL – áttekintés – visszaállítás gomb](./media/howto-restore-server-portal/2-server.png)

3. Töltse ki a visszaállítási űrlapot a szükséges információkkal:

   ![Azure Database for MySQL – adatok visszaállítása](./media/howto-restore-server-portal/3-restore.png)
   - **Visszaállítási pont**: válassza ki azt az időpontot, amelyet vissza szeretne állítani.
   - **Célkiszolgáló**: adja meg az új kiszolgáló nevét.
   - **Hely**: nem választhatja ki a régiót. Alapértelmezés szerint ugyanaz, mint a forráskiszolgáló.
   - **Díjszabási**csomag: ezeket a paramétereket nem módosíthatja az időponthoz tartozó visszaállítás során. Ugyanaz, mint a forráskiszolgálóé. 

4. Kattintson **az OK** gombra, ha vissza szeretné állítani a kiszolgálót egy adott időpontra való visszaállításhoz. 

5. A visszaállítás befejeződése után keresse meg a létrehozott új kiszolgálót annak ellenőrzéséhez, hogy az Adathelyreállítás a várt módon történt-e.

Az időponthoz tartozó visszaállítás által létrehozott új kiszolgáló ugyanazzal a kiszolgáló-rendszergazdai bejelentkezési névvel és jelszóval rendelkezik, amely érvényes volt a meglévő kiszolgáló számára a kiválasztott időpontban. A jelszót az új kiszolgáló **áttekintő** oldaláról lehet módosítani.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található tűzfalszabályok vagy VNet szolgáltatás-végpontokkal. Ezeket a szabályokat külön kell beállítani az új kiszolgálóhoz.

## <a name="geo-restore"></a>Geo-visszaállítás
Ha a kiszolgálót földrajzilag redundáns biztonsági mentésre konfigurálta, akkor a rendszer létrehoz egy új kiszolgálót a meglévő kiszolgáló biztonsági másolatából. Ezt az új kiszolgálót bármely olyan régióban létre lehet hozni, amely Azure Database for MySQL elérhető.  

1. A portál bal felső sarkában válassza az **Erőforrás létrehozása** (+) gombot. Válassza a **Databases** > **Azure Database for MySQL** lehetőséget.

   ![A "Azure Database for MySQL" lehetőség](./media/howto-restore-server-portal/2_navigate-to-mysql.png)

2. Az űrlap **Select Source (forrás kiválasztása** ) legördülő menüben válassza a **biztonsági mentés**lehetőséget. Ez a művelet betölti azon kiszolgálók listáját, amelyeken engedélyezve van a földrajzi redundáns biztonsági másolatok. Válassza ki az egyik biztonsági mentést az új kiszolgáló forrásaként.
   ![válassza ki a forrást: a redundáns biztonsági másolatok biztonsági mentése és listázása](./media/howto-restore-server-portal/2-georestore.png)

   > [!NOTE]
   > A kiszolgálók első létrehozásakor előfordulhat, hogy a Geo-visszaállításhoz nem lesz azonnal elérhető. A szükséges metaadatok feltöltése néhány órát is igénybe vehet.
   >

3. Adja meg a többi űrlapot a beállításokkal. Bármelyik **helyet**kiválaszthatja. A hely kiválasztása után kiválaszthatja az **árképzési szintet**. Alapértelmezés szerint a rendszer megjeleníti a meglévő, visszaállításra szolgáló kiszolgáló paramétereit. **Az OK** gombra kattintva módosíthatja a beállítások öröklését. Vagy módosíthatja a **számítási generációt** (ha elérhető a választott régióban), a **virtuális mag**számát, a **biztonsági mentés megőrzési idejét**és a **biztonsági mentési redundancia beállítást**. Az **árképzési szint** (alapszintű, általános célú vagy memória optimalizálása) vagy a **tárolási** méret módosítása a visszaállítás során nem támogatott.

A földrajzi visszaállítás által létrehozott új kiszolgáló ugyanazzal a kiszolgáló-rendszergazdai bejelentkezési névvel és jelszóval rendelkezik, amely a visszaállítás kezdeményezésének időpontjában érvényes a meglévő kiszolgálóhoz. A jelszót az új kiszolgáló **áttekintő** oldaláról lehet megváltoztatni.

A visszaállítás során létrehozott új kiszolgáló nem rendelkezik az eredeti kiszolgálón található tűzfalszabályok vagy VNet szolgáltatás-végpontokkal. Ezeket a szabályokat külön kell beállítani az új kiszolgálóhoz.



## <a name="next-steps"></a>Következő lépések
- További információ a szolgáltatás [biztonsági mentéséről](concepts-backup.md)
- A [replikák](concepts-read-replicas.md) megismerése
- További információ az [üzletmenet-folytonossági](concepts-business-continuity.md) lehetőségekről
