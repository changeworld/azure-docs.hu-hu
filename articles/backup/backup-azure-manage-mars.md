---
title: A MARS-ügynök biztonsági másolatainak kezelése és figyelése
description: Megtudhatja, hogyan kezelheti és figyelheti Microsoft Azure Recovery Services (MARS) ügynök biztonsági másolatait a Azure Backup szolgáltatás használatával.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: c11d73edd32c197aac2cec58eeb1cc20e5c6a339
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673251"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Microsoft Azure Recovery Services-(MARS-) ügynök biztonsági másolatainak kezelése a Azure Backup szolgáltatás használatával

Ez a cikk azt ismerteti, hogyan kezelhetők a Microsoft Azure Recovery Services ügynökkel biztonsági mentés alatt álló fájlok és mappák.

## <a name="modify-a-backup-policy"></a>Biztonsági mentési szabályzat módosítása

A biztonsági mentési szabályzat módosításakor hozzáadhat új elemeket, eltávolíthatja a meglévő elemeket a biztonsági másolatból, vagy kizárhatja a fájlok biztonsági mentését a kizárási beállítások használatával.

- **Elemek hozzáadása** ezt a lehetőséget csak új elemek biztonsági mentéshez való hozzáadásához használja. Meglévő elemek eltávolításához használja az **elemek eltávolítása** vagy a **kizárási beállítások** lehetőséget.  
- **Elemek eltávolítása** ezzel a beállítással távolíthatja el az elemeket a biztonsági mentésből.
  - A **kizárási beállítások** használatával **távolítsa el**a köteten belüli összes elemet az elemek eltávolítása helyett.
  - Egy kötet összes kijelölésének törlésével a rendszer a legutóbbi biztonsági mentés időpontjában megőrzési beállításokként megőrzi az elemek régi biztonsági másolatait, a módosítás hatóköre nélkül.
  - Ha újra kiválasztja ezeket az elemeket, a rendszer az első teljes biztonsági mentést és az új házirend-módosításokat nem alkalmazza a régi biztonsági másolatokra.
  - A teljes kötet kijelölésének megszüntetése megőrzi a korábbi biztonsági mentést az adatmegőrzési szabályzat módosításának hatóköre nélkül.
- **Kizárási beállítások** ezzel a beállítással kizárhat bizonyos elemeket a biztonsági mentésből.

### <a name="add-new-items-to-existing-policy"></a>Új elemek hozzáadása meglévő szabályzathoz

1. A **műveletek**területen kattintson a **biztonsági mentés időzítése**elemre.

    ![Windows Server biztonsági mentés ütemezése](./media/backup-configure-vault/schedule-first-backup.png)

2. A **házirend-elem kiválasztása** lapon válassza **a fájlok és mappák biztonsági mentésének módosítása** lehetőséget, majd kattintson a **tovább**gombra.

    ![Házirend-elemek kiválasztása](./media/backup-azure-manage-mars/select-policy-items.png)

3. A **biztonsági mentés módosítása vagy leállítása** lapon jelölje be a **módosítások végrehajtása a biztonsági másolatok vagy időpontok számára** lehetőséget, és kattintson a **tovább**gombra.

    ![Biztonsági mentés módosítása vagy időzítése](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Az elemek **kiválasztása a biztonsági mentéshez** lapon kattintson az **elemek hozzáadása** lehetőségre azon elemek hozzáadásához, amelyekről biztonsági másolatot szeretne készíteni.

    ![Biztonsági másolat hozzáadási elemeinek módosítása vagy ütemezett létrehozása](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Az **elemek kijelölése** ablakban válassza ki a hozzáadni kívánt legyek vagy mappák elemet, majd kattintson **az OK**gombra.

    ![Elemek kijelölése](./media/backup-azure-manage-mars/select-item.png)

6. Hajtsa végre a következő lépéseket, majd kattintson a **Befejezés** gombra a művelet befejezéséhez.

### <a name="add-exclusion-rules-to-existing-policy"></a>Kizárási szabályok hozzáadása a meglévő szabályzathoz

Kizárási szabályok hozzáadásával kihagyhatja azokat a fájlokat és mappákat, amelyeket nem szeretne biztonsági mentésre készíteni. Ezt az új szabályzat definiálásakor vagy egy meglévő szabályzat módosításakor teheti meg.

1. A Műveletek ablaktáblán kattintson az **ütemezett biztonsági mentés**elemre. A **biztonsági mentéshez válassza az elemek elemet** , majd kattintson a **kizárási beállítások**elemre.

    ![Elemek kijelölése](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. A **kizárási beállítások**területen kattintson a **kizárás hozzáadása**lehetőségre.

    ![Elemek kijelölése](./media/backup-azure-manage-mars/add-exclusion.png)

3. A **kizárni kívánt elemek kiválasztásával**tallózzon a fájlokhoz és mappákhoz, és válassza ki azokat az elemeket, amelyeket ki szeretne zárni, majd kattintson **az OK**gombra.

    ![Elemek kijelölése](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Alapértelmezés szerint a kijelölt mappákban lévő összes **almappa** ki van zárva. Ezt megváltoztathatja az **Igen** vagy a **nem**lehetőség kiválasztásával. A kizárni kívánt fájltípusokat a lent látható módon szerkesztheti és adhatja meg:

    ![Elemek kijelölése](./media/backup-azure-manage-mars/subfolders-type.png)

5. Hajtsa végre a következő lépéseket, majd kattintson a **Befejezés** gombra a művelet befejezéséhez.

### <a name="remove-items-from-existing-policy"></a>Elemek eltávolítása meglévő házirendből

1. A Műveletek ablaktáblán kattintson az **ütemezett biztonsági mentés**elemre. Lépjen a **biztonsági mentés elemre**. A listából válassza ki azokat a fájlokat és mappákat, amelyeket el szeretne távolítani a biztonsági mentési ütemtervből, majd kattintson az **elemek eltávolítása**elemre.

    ![Elemek kijelölése](./media/backup-azure-manage-mars/select-items-remove.png)

> [!NOTE]
> Körültekintően járjon el, ha teljesen eltávolítja a kötetet a szabályzatból.  Ha újra hozzá kell adnia, akkor a rendszer új kötetként kezeli. A következő ütemezett biztonsági mentés a növekményes biztonsági mentés helyett kezdeti biztonsági mentést (teljes biztonsági mentést) hajt végre. Ha a későbbiekben átmenetileg el kell távolítania és hozzá kell adnia az elemeket, akkor az **elemek eltávolítása** helyett a **kizárási beállítások** használata javasolt, hogy a teljes biztonsági mentés helyett a növekményes biztonsági mentést biztosítsa.

2. Hajtsa végre a következő lépéseket, majd kattintson a **Befejezés** gombra a művelet befejezéséhez.

## <a name="stop-protecting-files-and-folder-backup"></a>Fájlok és mappák biztonsági mentésének leállítása

A fájlok és mappák biztonsági mentése kétféleképpen állítható le:

- **A védelem leállítása és a biztonsági mentési adat megőrzése**.
  - Ezzel a beállítással leállíthatja az összes jövőbeli biztonsági mentési feladatot a védelemből.
  - Azure Backup szolgáltatás megőrzi azokat a helyreállítási pontokat, amelyek biztonsági mentése a megőrzési házirend alapján történt.
  - Visszaállíthatja a lejárt helyreállítási pontok biztonsági másolatait.
  - Ha úgy dönt, hogy folytatja a védelmet, használhatja a *biztonsági mentési ütemterv újbóli engedélyezése* lehetőséget. Ezt követően az adatok megmaradnak az új adatmegőrzési szabályzat alapján.
- **Állítsa le a védelmet, és törölje a biztonsági másolati fájlokat**.
  - Ezzel a beállítással leállíthatja az összes jövőbeli biztonsági mentési feladatot az adatok védelme és az összes helyreállítási pont törlése után.
  - A biztonsági mentési adatriasztások törléséről értesítő e-mailt fog kapni, amely üzenettel *törli a biztonsági mentési tétel adatait. Ezek az adatok 14 napig átmenetileg elérhetők lesznek, ami után véglegesen törlődik,* és a javasolt művelet *14 napon belül újra védetté teszi a biztonsági mentési tételt az adatok helyreállításához.*
  - A védelem folytatásához a törlési művelettől számított 14 napon belül meg kell oldania a védelmet.

### <a name="stop-protection-and-retain-backup-data"></a>A védelem leállítása és a biztonsági mentési adat megőrzése

1. Nyissa meg a MARS felügyeleti konzolját, lépjen a **műveletek ablaktáblára**, és **válassza a biztonsági mentés időzítése elemet**.

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-manage-mars/mars-actions.png)
1. A **házirend elemének kiválasztása** lapon válassza **a fájlok és mappák biztonsági mentésének ütemezett módosítása** lehetőséget, majd kattintson a **tovább**gombra.

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Az **ütemezett biztonsági mentés módosítása vagy leállítása** lapon válassza a **Leállítás ezzel a biztonsági mentési ütemezéssel lehetőséget, de a tárolt biztonsági mentéseket tartsa meg, amíg újra nem aktiválja az ütemezést**. Ezután válassza a **Tovább** lehetőséget.

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Az **ütemezett biztonsági mentés szüneteltetése** lapon tekintse át az információkat, majd kattintson a **Befejezés**gombra.

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. A **biztonsági mentési folyamat módosítása** alatt jelölje be az ütemezett biztonsági mentés szüneteltetése sikeres állapotban, majd kattintson a **Bezárás** gombra.

### <a name="stop-protection-and-delete-backup-data"></a>Védelem leállítása és biztonsági másolatok törlése

1. Nyissa meg a MARS felügyeleti konzolját, lépjen a **műveletek** ablaktáblára, és válassza a **biztonsági mentés időzítése**elemet.
2. Az **ütemezett biztonsági mentés módosítása vagy leállítása** lapon válassza a **Leállítás a biztonsági mentési ütemezés használatával lehetőséget, és törölje az összes tárolt biztonsági**mentést. Ezután válassza a **Tovább** lehetőséget.

    ![Ütemezett biztonsági mentés módosítása vagy leállítása.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Az **ütemezett biztonsági mentés leállítása** lapon válassza a **Befejezés**lehetőséget.

    ![Állítsa le az ütemezett biztonsági mentést.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. A rendszer felszólítja, hogy adjon meg egy biztonsági PIN-kódot (személyes azonosító számot), amelyet manuálisan kell előkészítenie. Ehhez először jelentkezzen be a Azure Portalba.
5. Lépjen a **Recovery Services** tároló > **Beállítások** > **Tulajdonságok menüpontra**.
6. A **biztonsági PIN-kód**területen válassza a **készítés**elemet. Másolja ezt a PIN-kódot. A PIN-kód csak öt percig érvényes.
7. A felügyeleti konzolon illessze be a PIN-kódot, majd kattintson **az OK gombra**.

    ![Biztonsági PIN-kód létrehozása.](./media/backup-azure-delete-vault/security-pin.png)

8. A **biztonsági mentési folyamat módosítása** lapon a következő üzenet jelenik meg: a *törölt biztonsági mentési adat 14 napig megmarad. Ez idő után véglegesen törlődik a biztonsági mentési adatvesztés.*  

    ![Törölje a biztonsági mentési infrastruktúrát.](./media/backup-azure-delete-vault/deleted-backup-data.png)

A helyszíni biztonsági mentési elemek törlését követően kövesse a portál következő lépéseit.

## <a name="re-enable-protection"></a>Védelem újbóli engedélyezése

Ha leállította a védelmet, miközben megtartja az adatvédelmet, és úgy döntött, hogy folytatja a védelmet, akkor a biztonsági mentési szabályzat módosításával újból engedélyezheti a biztonsági mentést.

1. A **műveletek** lapon válassza az **ütemezett biztonsági mentés**lehetőséget.
1. Válassza a **biztonsági mentési ütemterv újbóli engedélyezése lehetőséget. Emellett módosíthatja a biztonsági másolati elemeket vagy az időpontokat** , és kattintson a **tovább**gombra.<br>

    ![Törölje a biztonsági mentési infrastruktúrát.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. A **biztonsági mentés elemek kijelölése lapon**kattintson a **tovább**gombra.

    ![Törölje a biztonsági mentési infrastruktúrát.](./media/backup-azure-manage-mars/re-enable-next.png)
1. A **biztonsági mentés**időpontjának megadása lapon válassza ki a biztonsági mentés ütemtervét, és kattintson a **tovább**gombra.
1. A **megőrzési szabály kiválasztása**területen adja meg a megőrzési időtartamot, majd kattintson a **tovább**gombra.
1. Végül a **megerősítő** képernyőn tekintse át a szabályzat részleteit, és kattintson a **Befejezés**gombra.

## <a name="re-generate-passphrase"></a>Jelszó újbóli előállítása

A hitelesítő adatok titkosítására és visszafejtésére szolgálnak a helyszíni vagy helyi gép a MARS-ügynökkel vagy az Azure-ból történő biztonsági mentése vagy visszaállítása során. Ha elvesztette vagy elfelejtette a jelszót, akkor újra létrehozhatja a jelszót (ha a számítógép továbbra is regisztrálva van a Recovery Services-tárolóban, és a biztonsági mentés konfigurálva van), kövesse az alábbi lépéseket:

- A MARS-ügynök konzolján lépjen a **műveletek ablaktáblára** , > **módosítsa a tulajdonságok** > elemet. Ezután nyissa meg a **titkosítás lapot**.<br>
- Válassza a **jelszó módosítása** jelölőnégyzetet.<br>
- Adjon meg egy új jelszót, vagy kattintson a **jelszó létrehozása**lehetőségre.
- Az új jelszó mentéséhez kattintson a **Tallózás** gombra.

    ![Jelszó előállítása.](./media/backup-azure-manage-mars/passphrase.png)
- A módosítások alkalmazásához kattintson **az OK** gombra.  Ha a [biztonsági funkció](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) engedélyezve van a Recovery Services-tároló Azure Portalján, a rendszer kérni fogja a biztonsági PIN-kód megadását. A PIN-kód fogadásához kövesse az ebben a [cikkben](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations)ismertetett lépéseket.<br>
- Illessze be a biztonsági PIN-kódot a portálról, majd kattintson az **OK** gombra a módosítások alkalmazásához.<br>

    ![Jelszó előállítása.](./media/backup-azure-manage-mars/passphrase2.png)
- Győződjön meg arról, hogy a jelszó biztonságos módon mentve van egy másik helyen (a forrásoldali gépen kívül), lehetőleg a Azure Key Vault. Tartsa nyomon az összes hozzáférési kódot, ha több géppel is rendelkezik a MARS-ügynökökkel való biztonsági mentéssel.


## <a name="next-steps"></a>További lépések

- A támogatott forgatókönyvekkel és korlátozásokkal kapcsolatos információkért tekintse meg a [Mars-ügynök támogatási mátrixát](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent).
- További információ az [igény szerinti biztonsági mentési szabályzat megőrzési viselkedéséről](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
