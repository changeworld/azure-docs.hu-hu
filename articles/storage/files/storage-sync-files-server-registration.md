---
title: Regisztrált kiszolgálók kezelése a Azure File Syncrel | Microsoft Docs
description: Megtudhatja, hogyan regisztrálhat és törölheti a Windows Servert egy Azure File Sync Storage Sync szolgáltatással.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2656716560b981481273c3032fc0c7b1a06be8a2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255092"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Regisztrált kiszolgálók kezelése Azure File Sync
Az Azure File Sync lehetővé teszi a vállalat Azure Files szolgáltatásban tárolt fájlmegosztásainak központosítását anélkül, hogy fel kellene adnia a helyi fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását. Ez a Windows-kiszolgálók Azure-fájlmegosztás gyors gyorsítótárba alakításával végezhető el. A Windows Server rendszeren elérhető bármely protokollt használhatja a fájlok helyi eléréséhez (pl. SMB, NFS vagy FTPS), és annyi gyorsítótára lehet világszerte, amennyire csak szüksége van.

A következő cikk bemutatja, hogyan regisztrálhat és kezelhet egy kiszolgálót a Storage Sync szolgáltatással. A Azure File Sync végpontok üzembe helyezésével kapcsolatos információkért tekintse meg a [Azure file Sync üzembe helyezését](storage-sync-files-deployment-guide.md) ismertető témakört.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Kiszolgáló regisztrálása a Storage Sync szolgáltatással
A Azure File Sync-kiszolgáló regisztrálása megbízhatósági kapcsolatot létesít a Windows Server és az Azure között. Ezzel a kapcsolattal *kiszolgáló-végpontok* hozhatók létre a kiszolgálón, amelyek az Azure-fájlmegosztás (más néven *Felhőbeli végpont*) által szinkronizált adott mappákat jelölik. 

### <a name="prerequisites"></a>Előfeltételek
Ha Storage Sync szolgáltatással szeretne regisztrálni egy kiszolgálót, először elő kell készítenie a kiszolgálót a szükséges előfeltételekkel:

* A kiszolgálónak a Windows Server támogatott verzióját kell futtatnia. További információ: [Azure file Sync rendszerkövetelmények és együttműködési képesség](storage-sync-files-planning.md#windows-file-server-considerations).
* Győződjön meg arról, hogy a Storage Sync szolgáltatás telepítve van. A Storage Sync szolgáltatás telepítésével kapcsolatos további információkért lásd: [a Azure file Sync üzembe helyezése](storage-sync-files-deployment-guide.md).
* Ellenőrizze, hogy a kiszolgáló csatlakozik-e az internethez, és hogy az Azure elérhető-e.
* Tiltsa le az IE fokozott biztonsági beállításait a rendszergazdák számára a Kiszolgálókezelő felhasználói felületén.
    
    ![A Kiszolgálókezelő felhasználói felülete az Internet Explorer fokozott biztonsági beállításainak kiemelésével](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Győződjön meg arról, hogy a Azure PowerShell modul telepítve van a kiszolgálón. Ha a kiszolgáló egy feladatátvevő fürt tagja, a fürt minden csomópontjának az az modulra lesz szüksége. Az az modul telepítésével kapcsolatos további információkért tekintse meg a [telepítési és konfigurálási Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

    > [!Note]  
    > Javasoljuk, hogy az az PowerShell modul legújabb verzióját használja a kiszolgálók regisztrálásához/regisztrációjának megszüntetéséhez. Ha az az csomag már telepítve van ezen a kiszolgálón (és a PowerShell-verzió a kiszolgálón 5. * vagy nagyobb), akkor a `Update-Module` parancsmaggal frissítheti a csomagot. 
* Ha a környezetben hálózati proxykiszolgálót használ, konfigurálja a proxy beállításait a kiszolgálón a szinkronizálási ügynök számára.
    1. A proxy IP-címének és portszámának meghatározása
    2. Szerkessze ezt a két fájlt:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Adja hozzá az 1. ábrán szereplő sorokat a fenti két fájl/System.ServiceModel alatt, a 127.0.0.1:8888 a megfelelő IP-címhez (a 127.0.0.1 lecserélése) és a helyes portszámhoz (a 8888 helyére):
    4. A WinHTTP-proxybeállítások beállítása parancssor használatával:
        * A proxy megjelenítése: netsh WinHTTP show proxy
        * A proxy beállítása: netsh WinHTTP set proxy 127.0.0.1:8888
        * A proxy alaphelyzetbe állítása: netsh WinHTTP reset proxy
        * Ha ez a beállítás az ügynök telepítése után, indítsa újra a szinkronizálási ügynököt: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Kiszolgáló regisztrálása a Storage Sync szolgáltatással
Ahhoz, hogy egy kiszolgáló egy Azure File Sync *szinkronizálási csoportban* *kiszolgálói végpontként* is használható legyen, regisztrálni kell a *Storage Sync szolgáltatásban*. A kiszolgálót egyszerre csak egy Storage Sync szolgáltatásban lehet regisztrálni.

#### <a name="install-the-azure-file-sync-agent"></a>Az Azure File Sync-ügynök telepítése
1. [Töltse le a Azure file Sync ügynököt](https://go.microsoft.com/fwlink/?linkid=858257).
2. Indítsa el a Azure File Sync Agent telepítőjét.
    
    ![A Azure File Sync ügynök telepítőjének első panelje](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Ügyeljen arra, hogy a Microsoft Update használatával engedélyezze a frissítéseket a Azure File Sync-ügynöknek. Fontos, hogy a kiszolgálói csomag kritikus fontosságú biztonsági javításait és funkcióit Microsoft Update-on keresztül szállítsa a rendszer.

    ![Győződjön meg arról, Microsoft Update engedélyezve van a Azure File Sync ügynök telepítőjének Microsoft Update paneljén](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Ha a kiszolgáló korábban nem lett regisztrálva, a kiszolgáló regisztrációjának felhasználói felülete azonnal megjelenik a telepítés befejezése után.

> [!Important]  
> Ha a kiszolgáló egy feladatátvevő fürt tagja, akkor a Azure File Sync ügynököt a fürt minden csomópontjára telepíteni kell.

#### <a name="register-the-server-using-the-server-registration-ui"></a>A kiszolgáló regisztrálása a kiszolgáló regisztrációjának felhasználói felületén
> [!Important]  
> A Cloud Solution Provider (CSP) előfizetések nem használhatják a kiszolgáló regisztrációjának felhasználói felületét. Ehelyett használja a PowerShellt (ez a szakasz alatt).

1. Ha a kiszolgáló regisztrációjának felhasználói felülete nem indult el azonnal a Azure File Sync ügynök telepítésének befejezése után, akkor a `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`futtatásával manuálisan is elindítható.
2. Az Azure-előfizetés eléréséhez kattintson a *Bejelentkezés* elemre. 

    ![A kiszolgáló regisztrációjának felhasználói felületének megnyitása párbeszédpanel](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Válassza ki a megfelelő előfizetést, erőforráscsoportot és Storage Sync szolgáltatást a párbeszédpanelről.

    ![Storage Sync szolgáltatás adatai](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Előzetes verzióban még egy bejelentkezés szükséges a folyamat befejezéséhez. 

    ![Bejelentkezési párbeszédpanel](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Ha a kiszolgáló egy feladatátvevő fürt tagja, akkor minden kiszolgálónak futtatnia kell a kiszolgáló regisztrációját. Amikor az Azure Portalon tekinti meg a regisztrált kiszolgálókat, Azure File Sync automatikusan felismeri az egyes csomópontokat ugyanazon feladatátvevő fürt tagjaként, és megfelelően csoportosítja őket.

#### <a name="register-the-server-with-powershell"></a>A kiszolgáló regisztrálása a PowerShell-lel
A kiszolgáló regisztrációját a PowerShell használatával is végrehajthatja. Ez az egyetlen támogatott módszer a felhőalapú megoldás-szolgáltatói (CSP) előfizetések kiszolgálói regisztrálásához:

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>A kiszolgáló regisztrációjának törlése a Storage Sync szolgáltatással
A kiszolgálók Storage Sync szolgáltatással való regisztrációjának megszüntetéséhez több lépés szükséges. Vessünk egy pillantást a kiszolgálók regisztrációjának megszüntetésére.

> [!Warning]  
> Ne kísérelje meg a szinkronizálással, a Felhőbeli rétegekkel vagy a Azure File Sync bármely más aspektusával kapcsolatos hibák elhárítását azáltal, hogy megszünteti a kiszolgáló regisztrációját és regisztrálását, illetve a kiszolgáló-végpontok eltávolítását és újbóli létrehozását, hacsak a Microsoft mérnöke kifejezetten nem utasította. Egy-kiszolgáló regisztrációjának törlése és a kiszolgálói végpontok eltávolítása egy romboló művelet, és a köteteken található, a kiszolgálói végpontokkal rendelkező kötetek rétegű fájljai nem lesznek "újracsatlakoztatva" az Azure-fájlmegosztás azon helyeihez, amelyek a regisztrált kiszolgáló és a kiszolgálói végpontok a rendszer újból létrehozta, és szinkronizálási hibákat eredményez. Azt is vegye figyelembe, hogy a kiszolgálói végpontok névterén kívül található, lépcsőzetesen használt fájlok véglegesen elvesznek. A többszintes fájlok a kiszolgálói végpontokon belül létezhetnek, még akkor is, ha a felhő-rétegek soha nem voltak engedélyezve.

#### <a name="optional-recall-all-tiered-data"></a>Választható Az összes rétegű adatbázis visszahívása
Ha szeretné, hogy a jelenleg a Azure File Sync eltávolítását követően rendelkezésre álló fájlok elérhetők legyenek (például éles környezetben, nem teszt, környezet), a kiszolgálói végpontokat tartalmazó minden köteten emlékeztesse az összes fájlt. Tiltsa le a Felhőbeli adatrétegeket az összes kiszolgálói végponton, majd futtassa a következő PowerShell-parancsmagot:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Ha a kiszolgálói végpontot futtató helyi köteten nincs elég szabad hely az összes rétegű adat felidézéséhez, akkor a `Invoke-StorageSyncFileRecall` parancsmag sikertelen lesz.  

#### <a name="remove-the-server-from-all-sync-groups"></a>A kiszolgáló eltávolítása minden szinkronizálási csoportból
Mielőtt törli a kiszolgáló regisztrációját a Storage Sync szolgáltatásban, el kell távolítani a kiszolgálón található összes kiszolgálói végpontot. Ezt a Azure Portal keresztül teheti meg:

1. Navigáljon a Storage Sync szolgáltatáshoz, ahol a kiszolgáló regisztrálva van.
2. Távolítsa el a kiszolgálóhoz tartozó összes kiszolgálói végpontot a Storage Sync szolgáltatás minden egyes szinkronizálási csoportjához. Ezt úgy érheti el, ha a jobb gombbal a megfelelő kiszolgálói végpontra kattint a szinkronizálási csoport ablaktáblán.

    ![Kiszolgálói végpont eltávolítása egy szinkronizálási csoportból](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Ez egy egyszerű PowerShell-parancsfájllal is elvégezhető:

```powershell
Connect-AzAccount

$storageSyncServiceName = "<your-storage-sync-service>"
$resourceGroup = "<your-resource-group>"

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $storageSyncServiceName | ForEach-Object { 
    $syncGroup = $_; 
    Get-AzStorageSyncServerEndpoint -ParentObject $syncGroup | Where-Object { $_.ServerEndpointName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzStorageSyncServerEndpoint -InputObject $_ 
    } 
}
```

#### <a name="unregister-the-server"></a>Kiszolgáló regisztrációjának törlése
Most, hogy minden adatok visszahívása megtörtént, és a kiszolgáló el lett távolítva az összes szinkronizálási csoportból, a kiszolgáló regisztrációja törölhető. 

1. A Azure Portal navigáljon a Storage Sync szolgáltatás *regisztrált kiszolgálók* szakaszához.
2. Kattintson a jobb gombbal a regisztrálni kívánt kiszolgálóra, majd kattintson a "kiszolgáló regisztrálása" elemre.

    ![Kiszolgáló regisztrációjának törlése](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Annak biztosítása, Azure File Sync jó szomszéd az adatközpontban 
Mivel Azure File Sync csak ritkán fut az adatközpontban, érdemes korlátozni Azure File Sync hálózati és tárolási használatát.

> [!Important]  
> A határértékek túl alacsonyra állítása hatással van Azure File Sync szinkronizálás és a visszahívás teljesítményére.

### <a name="set-azure-file-sync-network-limits"></a>Azure File Sync hálózati korlátok megadása
A Azure File Sync hálózati kihasználtságát a `StorageSyncNetworkLimit` parancsmagok segítségével szabályozhatja.

> [!Note]  
> A hálózati korlátok nem érvényesek egy többrétegű fájl elérésekor vagy a Meghívási StorageSyncFileRecall parancsmag használatakor.

Létrehozhat például egy új szabályozási korlátot annak biztosításához, hogy a Azure File Sync ne használjon 10 Mbps-nál több, mint 9 – 5 PM-t (17:00H) a munkahét során: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

A korlátot a következő parancsmaggal érheti el:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

A hálózati korlátok eltávolításához használja a `Remove-StorageSyncNetworkLimit`. A következő parancs például eltávolítja az összes hálózati korlátot:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>A Windows Server Storage QoS használata 
Ha a Azure File Sync egy Windows Server virtualizálási gazdagépen futó virtuális gépen fut, a tárolási QoS (tárolási szolgáltatásminőség) segítségével szabályozhatja a tároló i/o-felhasználását. A tárolási QoS-házirend beállítható maximális értékként (vagy korlátként, például a fenti StorageSyncNetwork-korlát érvényesítése) vagy minimum (vagy foglalás). Ha a maximális értéket állítja be, a Azure File Sync a rendelkezésre álló tárolási sávszélességet használja, ha más munkaterhelések nem használják azt. További információ: [tárolási szolgáltatásminőség](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Lásd még
- [Az Azure File Sync üzembe helyezésének megtervezése](storage-sync-files-planning.md)
- [Az Azure File Sync üzembe helyezése](storage-sync-files-deployment-guide.md)
- [Az Azure File Sync monitorozása](storage-sync-files-monitoring.md)
- [Az Azure File Sync hibaelhárítása](storage-sync-files-troubleshoot.md)
