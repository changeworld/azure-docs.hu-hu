---
title: Bejelentkezési hitelesítő adatok elforgatása Azure Stream Analytics feladatokban
description: Ez a cikk azt ismerteti, hogyan lehet frissíteni a bemeneti és kimeneti adatnyelők hitelesítő adatait Azure Stream Analytics feladatokban.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 3ae639dd7c5a42fc6880240988f0fb2817b09f43
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425976"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Bejelentkezési hitelesítő adatok elforgatása egy Stream Analytics-feladathoz tartozó bemenetekhez és kimenetekhez

Amikor egy Stream Analytics-feladathoz tartozó bemeneti vagy kimeneti hitelesítő adatokat állít elő, a feladatot új hitelesítő adatokkal kell frissítenie. A hitelesítő adatok frissítése előtt le kell állítania a feladatot, nem lehet lecserélni a hitelesítő adatokat, amíg a feladatok futnak. A feladatok leállítása és újraindítása közötti késés csökkentése érdekében Stream Analytics támogatja a feladatok utolsó kimenetből való folytatását. Ez a témakör ismerteti a bejelentkezési hitelesítő adatok elforgatásának folyamatát, és új hitelesítő adatokkal indítja el a feladatot.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Új hitelesítő adatok újbóli előállítása és a feladatok frissítése az új hitelesítő adatokkal 

Ebben a szakaszban bemutatjuk a Blob Storage, Event Hubs, SQL Database és Table Storage hitelesítő adatainak újragenerálását. 

### <a name="blob-storagetable-storage"></a>BLOB Storage/Table Storage
1. Jelentkezzen be a Azure Portal > tallózással keresse meg a Stream Analytics feladathoz bemenetként vagy kimenetként használt Storage-fiókot.    
2. A beállítások szakaszban nyissa meg a **hozzáférési kulcsok**elemet. A két alapértelmezett kulcs (key1, key2) között válassza ki azt, amelyet a feladatokban nem használ, majd adja meg újból a következőket:  
   ![a Storage-fiók kulcsainak újragenerálása](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Másolja az újonnan létrehozott kulcsot.    
4. A Azure Portal tallózással keresse meg Stream Analytics feladatot > válassza a **Leállítás** lehetőséget, és várja meg, amíg a feladatok le nem állnak.    
5. Keresse meg azt a blob/Table Storage-bemenetet/-kimenetet, amelynek a hitelesítő adatait frissíteni kívánja.    
6. Keresse meg a **Storage-fiók kulcsa** mezőt, és illessze be az újonnan létrehozott kulcsot > kattintson a **Save (Mentés**) gombra.    
7. A módosítások mentésekor a rendszer automatikusan elindítja a kapcsolódási teszteket, és megtekintheti az értesítések lapról. Két értesítés van – az egyik megfelel a frissítés mentésének, és az egyéb megfelel a kapcsolódás tesztelésének:  
   ![Értesítések a kulcs szerkesztése után](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Folytassa a [feladatok elindításával az utolsó leállított idő](#start-your-job-from-the-last-stopped-time) szakaszból.

### <a name="event-hubs"></a>Event Hubs

1. Jelentkezzen be a Azure Portal > tallózással keresse meg a Stream Analytics feladathoz bemenetként vagy kimenetként használt Event hub-t.    
2. A beállítások szakaszban nyissa meg a megosztott hozzáférési házirendeket, és válassza ki a szükséges hozzáférési **szabályzatot** . Az **elsődleges kulcs** és a **másodlagos kulcs**között válassza ki azt, amelyet a feladatokban nem használ, majd generálja újra:  
   ![kulcsok újragenerálása Event Hubs](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Másolja az újonnan létrehozott kulcsot.    
4. A Azure Portal tallózással keresse meg Stream Analytics feladatot > válassza a **Leállítás** lehetőséget, és várja meg, amíg a feladatok le nem állnak.    
5. Keresse meg az Event hub azon bemenetét/kimenetét, amelynél frissíteni kívánja a hitelesítő adatokat.    
6. Keresse meg az **Event hub-házirend kulcsa** mezőt, és illessze be az újonnan létrehozott kulcsot > kattintson a **Save (Mentés**) gombra.    
7. A módosítások mentésekor a rendszer automatikusan elindítja a kapcsolódási teszteket.    
8. Folytassa a [feladatok elindításával az utolsó leállított idő](#start-your-job-from-the-last-stopped-time) szakaszból.

### <a name="sql-database"></a>SQL-adatbázis

Egy meglévő felhasználó bejelentkezési hitelesítő adatainak frissítéséhez csatlakoznia kell az SQL-adatbázishoz. A hitelesítő adatokat Azure Portal vagy egy ügyféloldali eszköz (például SQL Server Management Studio) használatával frissítheti. Ez a szakasz a hitelesítő adatok Azure Portal használatával történő frissítésének folyamatát mutatja be.

1. Jelentkezzen be a Azure Portal > tallózással keresse meg a Stream Analytics feladatokhoz kimenetként használt SQL-adatbázist.    
2. Az **adatkezelőből**jelentkezzen be/kapcsolódjon az adatbázishoz > válassza az engedélyezési típus lehetőséget az **SQL server-hitelesítés** > írja be a **bejelentkezési** és a **jelszó** részleteit > válassza az **OK**gombot.  
   ![az SQL Database hitelesítő adatainak ismételt előállítása](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. A lekérdezés lapon módosítsa az egyik felhasználó jelszavát a következő lekérdezés futtatásával (Ügyeljen arra, hogy a `<user_name>` a felhasználónevével és `<new_password>` az új jelszóval):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Jegyezze fel az új jelszót.    
5. A Azure Portal tallózással keresse meg Stream Analytics feladatot > válassza a **Leállítás** lehetőséget, és várja meg, amíg a feladatok le nem állnak.    
6. Keresse meg azt az SQL Database-kimenetet, amelynek a hitelesítő adatait el szeretné forgatni. Frissítse a jelszót, és mentse a módosításokat.    
7. A módosítások mentésekor a rendszer automatikusan elindítja a kapcsolódási teszteket.    
8. Folytassa a [feladatok elindításával az utolsó leállított idő](#start-your-job-from-the-last-stopped-time) szakaszból.

### <a name="power-bi"></a>Power BI
1. Jelentkezzen be a Azure Portal > tallózzon a Stream Analytics feladatokban > válassza a **Leállítás** lehetőséget, és várja meg, amíg a feladatok le nem állnak.    
2. Keresse meg azt a Power BI kimenetet, amelynek hitelesítő adatait meg szeretné újítani > kattintson a **megújítási engedélyre** (sikeres üzenet jelenik meg) > **mentse** a módosításokat.    
3. A módosítások mentésekor a rendszer automatikusan elindítja a kapcsolódási teszteket.    
4. Folytassa a [feladatok elindításával az utolsó leállított idő](#start-your-job-from-the-last-stopped-time) szakaszból.

## <a name="start-your-job-from-the-last-stopped-time"></a>Indítsa el a feladatot az utolsó leállított időpontból

1. Navigáljon a feladatok **Áttekintés** panelére > válassza a **Start (Kezdés** ) lehetőséget a feladatok elindításához.    
2. Válassza ki az **utolsó leállítva** > kattintson az **Indítás**gombra. Vegye figyelembe, hogy a "ha utolsó leállítva" beállítás csak akkor jelenik meg, ha korábban már futtatta a feladatot, és némi kimenet lett létrehozva. A feladatot a rendszer az utolsó kimeneti érték időpontja alapján újraindítja.
   ![indítsa el a Stream Analytics feladatot](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
