---
title: Teljesítményszámlálók összegyűjtése és elemzése a Azure Monitorban | Microsoft Docs
description: A teljesítményszámlálókat a Azure Monitor gyűjti a Windows-és Linux-ügynökök teljesítményének elemzéséhez.  Ez a cikk bemutatja, hogyan konfigurálhatja a teljesítményszámlálók gyűjteményét Windows-és Linux-ügynökökhöz, a munkaterületen tárolt adatokat, valamint azt, hogyan elemezheti őket a Azure Portalban.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: d1a972a1d89066b961f2dcc28fba830e3a04ebc1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274761"
---
# <a name="windows-and-linux-performance-data-sources-in-azure-monitor"></a>Windows-és Linux-teljesítményű adatforrások a Azure Monitor
A Windows és Linux rendszerű teljesítményszámlálók betekintést nyújtanak a hardver-összetevők, operációs rendszerek és alkalmazások teljesítményére.  A Azure Monitor a teljesítményadatok a hosszú távú elemzéshez és jelentéskészítéshez való közel valós idejű (vizsgálja) elemzéshez is összegyűjthetők.

![Teljesítményszámlálók](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Teljesítményszámlálók konfigurálása
Teljesítményszámlálók konfigurálása a [Speciális beállítások adatok menüjében](agent-data-sources.md#configuring-data-sources).

Amikor először konfigurálja a Windows-vagy Linux-teljesítményszámlálókat egy új munkaterülethez, lehetősége van számos gyakori számláló gyors létrehozására.  Ezek mindegyike mellett egy jelölőnégyzet található.  Győződjön meg arról, hogy a kezdetben létrehozni kívánt számlálók be vannak jelölve, majd kattintson **a kijelölt teljesítményszámlálók hozzáadása**lehetőségre.

A Windows-teljesítményszámlálók esetében kiválaszthatja az egyes teljesítményszámlálók egy adott példányát. A Linux-teljesítményszámlálók esetében az egyes kiválasztott számlálók a szülő számláló összes alárendelt számlálóján érvényesek. A következő táblázat a Linux és a Windows teljesítményszámlálói számára elérhető általános példányokat mutatja be.

| Példány neve | Leírás |
| --- | --- |
| \_összesen |Összes példány összesen |
| \* |Minden példány |
| (/&#124;/var) |A (z):/vagy a/var nevű példányokra illeszkedik |

### <a name="windows-performance-counters"></a>Windows-teljesítményszámlálók

![Windows-teljesítményszámlálók konfigurálása](media/data-sources-performance-counters/configure-windows.png)

Kövesse ezt az eljárást egy új Windows-teljesítményszámláló hozzáadásához a gyűjtéshez.

1. Írja be a számláló nevét a Format *objektum (példány) \ számláló*szövegmezőbe.  A gépelés megkezdése után a rendszer a gyakori számlálók megfelelő listáját mutatja be.  Kiválaszthat egy számlálót a listából, vagy megadhatja a kívánt értéket.  A *object\counter*megadásával egy adott számlálóhoz tartozó összes példányt is visszaadhat.  

    SQL Server teljesítményszámlálók elnevezett példányokból való gyűjtésekor az összes elnevezett példány számlálója az *MSSQL $* értékkel kezdődik, amelyet a példány neve követ.  Ha például a log cache találati arány számlálóját szeretné összegyűjteni az SQL-példány INST2 tartozó adatbázis-teljesítmény objektumban található összes adatbázishoz, akkor a `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`t kell megadnia.

2. Kattintson **+** vagy nyomja le az **ENTER** billentyűt a számláló a listához való hozzáadásához.
3. Számláló hozzáadásakor a rendszer az alapértelmezett 10 másodpercet használja a **mintavételi intervallumhoz**.  Ez a érték legfeljebb 1800 másodperc (30 perc) lehet, ha csökkenteni szeretné az összegyűjtött teljesítményadatok tárolási követelményeit.
4. Ha elkészült a számlálók hozzáadásával, kattintson a képernyő felső részén található **Mentés** gombra a konfiguráció mentéséhez.

### <a name="linux-performance-counters"></a>Linux-teljesítményszámlálók

![Linux-teljesítményszámlálók konfigurálása](media/data-sources-performance-counters/configure-linux.png)

Kövesse ezt az eljárást egy új Linux-teljesítményszámláló hozzáadásához a gyűjtéshez.

1. Alapértelmezés szerint az összes konfigurációs módosítást automatikusan leküld az összes ügynököt.  Linux-ügynökök esetében a rendszer egy konfigurációs fájlt küld a Fluent-adatgyűjtőnek.  Ha ezt a fájlt manuálisan kívánja módosítani minden Linux-ügynökön, törölje a jelet az *alábbi konfiguráció alkalmazása a Linux rendszerű gépekre* lehetőségre, és kövesse az alábbi útmutatást.
2. Írja be a számláló nevét a Format *objektum (példány) \ számláló*szövegmezőbe.  A gépelés megkezdése után a rendszer a gyakori számlálók megfelelő listáját mutatja be.  Kiválaszthat egy számlálót a listából, vagy megadhatja a kívánt értéket.  
3. Kattintson **+** vagy nyomja le az **ENTER** billentyűt a számláló az objektumhoz tartozó egyéb számlálók listájához való hozzáadásához.
4. Egy objektum összes számlálója ugyanazt a **mintavételi időközt**használja.  Az alapértelmezett érték 10 másodperc.  Ezt magasabb értékre kell módosítani, amely legfeljebb 1800 másodperc (30 perc) lehet, ha csökkenteni szeretné az összegyűjtött teljesítményadatok tárolási követelményeit.
5. Ha elkészült a számlálók hozzáadásával, kattintson a képernyő felső részén található **Mentés** gombra a konfiguráció mentéséhez.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Linux-teljesítményszámlálók konfigurálása a konfigurációs fájlban
A Linux-teljesítményszámlálók a Azure Portal használatával történő konfigurálása helyett lehetősége van a konfigurációs fájlok szerkesztésére a Linux-ügynökön.  A gyűjteni kívánt teljesítmény-mérőszámokat a **/etc/opt/microsoft/omsagent/\<munkaterület-azonosító\>/conf/omsagent.conf**konfigurációja vezérli.

A gyűjteni kívánt teljesítmény-mérőszámok minden objektumát vagy kategóriáját egyetlen `<source>` elemként kell definiálni a konfigurációs fájlban. A szintaxis az alábbi mintát követi.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


Az ebben az elemben található paramétereket a következő táblázat ismerteti.

| Paraméterek | Leírás |
|:--|:--|
| objektum\_neve | A gyűjtemény objektumának neve. |
| példány\_regex |  Egy *reguláris kifejezés* , amely meghatározza, hogy mely példányokat kell gyűjteni. Az érték: `.*` az összes példányt megadja. Ha csak a \_összes példányához szeretné összegyűjteni a processzor metrikáit, megadhatja `_Total`. Ha csak a crond vagy sshd példányok feldolgozási metrikáit szeretné összegyűjteni, megadhatja a következőt: `(crond\|sshd)`. |
| számláló\_neve\_regexben | Egy *reguláris kifejezés* , amely meghatározza, hogy mely számlálókat (az objektumhoz) kell összegyűjteni. Az objektum összes számlálójának összegyűjtéséhez a következőt kell megadnia: `.*`. Ha csak a memória-objektum lapozófájl-számlálóit szeretné összegyűjteni, például megadhatja a következőket: `.+Swap.+` |
| interval | Az objektum számlálóinak gyűjtésének gyakorisága. |


A következő táblázat felsorolja a konfigurációs fájlban megadható objektumokat és számlálókat.  Bizonyos alkalmazásokhoz további számlálók érhetők el, amelyek a következő témakörben olvashatók: a [teljesítményszámlálók összegyűjtése Linux-alkalmazásokhoz a Azure monitorban](data-sources-linux-applications.md).

| Objektum neve | Számláló neve |
|:--|:--|
| Logikai lemez | Szabad inode%-ban |
| Logikai lemez | Szabad terület százalékos aránya |
| Logikai lemez | Felhasznált inode%-ban |
| Logikai lemez | Foglalt hely % |
| Logikai lemez | Lemezolvasás sebessége bájt/mp-ben |
| Logikai lemez | Lemezolvasások/mp |
| Logikai lemez | Átvitel/mp |
| Logikai lemez | Lemezírás sebessége bájt/mp-ben |
| Logikai lemez | Lemezírások/mp |
| Logikai lemez | Szabad hely MB-ban |
| Logikai lemez | Logikai lemez bájt/mp |
| Memória | Rendelkezésre álló memória%-ban |
| Memória | Rendelkezésre álló swap-terület (%) |
| Memória | Felhasznált memória (%) |
| Memória | Felhasznált swap-terület%-ban |
| Memória | Rendelkezésre álló memória |
| Memória | Rendelkezésre álló memória (MB) |
| Memória | Olvasott lap/mperc |
| Memória | Írt lap/mperc |
| Memória | Mozgatott lapok (lap/sec) |
| Memória | Felhasznált memória (MB) – lapozófájl |
| Memória | Felhasznált memória (MB) |
| Network (Hálózat) | Küldött bájtok száma összesen |
| Network (Hálózat) | Fogadott bájtok teljes száma |
| Network (Hálózat) | Bájtok összesen |
| Network (Hálózat) | Továbbított csomagok összesen |
| Network (Hálózat) | Fogadott csomagok összesen |
| Network (Hálózat) | Rx-hibák összesen |
| Network (Hálózat) | TX-hibák összesen |
| Network (Hálózat) | Ütközések összesen |
| Fizikai lemez | Átlagos írási idő (mp/olvasás) |
| Fizikai lemez | Átlagos műveleti idő (mp/átvitel) |
| Fizikai lemez | Átlagos írási idő (mp/írás) |
| Fizikai lemez | Fizikai lemez sebessége (bájt/s) |
| Process | PCT rendszerjogosultságú idő |
| Process | PCT felhasználói idő |
| Process | Felhasznált memória (kilobájt) |
| Process | Virtuális megosztott memória |
| Processzor | % DPC idő |
| Processzor | Üresjárati idő%-ban |
| Processzor | Megszakítási idő%-ban |
| Processzor | I/o várakozási idő%-ban |
| Processzor | % Nice idő |
| Processzor | %-Os privilegizált idő |
| Processzor | A processzor kihasználtsága (%) |
| Processzor | Felhasználói idő%-ban |
| Rendszer | Szabad fizikai memória |
| Rendszer | Szabad terület a Lapozófájlokban |
| Rendszer | Szabad virtuális memória |
| Rendszer | Folyamatok |
| Rendszer | Lapozófájlokban tárolt méret |
| Rendszer | Hasznos üzemidő |
| Rendszer | Felhasználók |


A teljesítmény-metrikák alapértelmezett konfigurációja a következő:

    <source>
      type oms_omi
      object_name "Physical Disk"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Logical Disk"
      instance_regex ".*
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Processor"
      instance_regex ".*
      counter_name_regex ".*"
      interval 30s
    </source>

    <source>
      type oms_omi
      object_name "Memory"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>

## <a name="data-collection"></a>Adatgyűjtés
Azure Monitor a megadott mintavételi intervallumban gyűjti az összes megadott teljesítményszámlálókat az összes olyan ügynökön, amelyen telepítve van a számláló.  Az adatokat a rendszer nem összesíti, és a nyers adatokat az előfizetés által megadott időtartamra vonatkozóan az összes naplózási lekérdezési nézetben elérhetővé válik.

## <a name="performance-record-properties"></a>Teljesítmény rekord tulajdonságai
A teljesítményadatokat a teljesítmény **típusa és a** következő táblázatban szereplő tulajdonságok szerepelnek.

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Az esemény gyűjtötte a program a számítógép. |
| CounterName |A teljesítményszámláló neve |
| CounterPath |A számláló teljes elérési útja az űrlapon \\\\\<számítógép >\\objektum (példány)\\számláló. |
| Kártyabirtokos számlájának megterhelését |A számláló numerikus értéke. |
| InstanceName |Az esemény példányának neve.  Üres, ha nincs példány. |
| ObjectName |A teljesítményobjektum neve |
| SourceSystem |Az ügynök típusa, amelyből az adatokat gyűjtötték. <br><br>OpsManager – Windows-ügynök, közvetlen kapcsolat vagy SCOM <br> Linux – minden Linux-ügynök  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |Az adatok mintavételezésének dátuma és időpontja. |

## <a name="sizing-estimates"></a>Méretezési becslések
 Egy adott számláló 10 másodperces időközönként történő gyűjtésének durva becslése napi 1 MB-onként történik.  Megbecsülheti egy adott számláló tárolási követelményeit a következő képlettel.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-queries-with-performance-records"></a>Lekérdezések naplózása a teljesítménnyel kapcsolatos rekordokkal
Az alábbi táblázat különböző példákat tartalmaz a teljesítményadatokat lekérő lekérdezések naplózására.

| Lekérdezés | Leírás |
|:--- |:--- |
| Teljesítmény |Minden teljesítményadatok |
| A &#124; Perf, ahol a Computer = = "Sajátgép" |Egy adott számítógépről származó összes teljesítményadatok |
| A &#124; Perf, ahol a CounterName = = "a lemez aktuális várólistájának hossza" |Egy adott számláló összes teljesítményadatokat |
| A &#124; Perf, ahol a ObjectName = = "Processor" és a CounterName = = "% Processor Time" és a példánynév &#124; = = "_Total" összefoglaló AVGCPU = AVG (kártyabirtokos számlájának megterhelését) a számítógépen |A CPU átlagos kihasználtsága az összes számítógépen |
| A &#124; Perf, ahol a CounterName = = "% &#124; Processor Time" összefoglalja a AggregatedValue = Max (kártyabirtokos számlájának megterhelését) t a számítógépen |Maximális CPU-kihasználtság az összes számítógépen |
| Perf &#124; , ahol a ObjectName = = "LogicalDisk" és a CounterName = = "aktuális lemez várólistájának hossza" és a Computer &#124; = = "MyComputerName" összefoglaló AggregatedValue = AVG (kártyabirtokos számlájának megterhelését) by példánynév |Az aktuális Lemezvezérlő-várólista átlagos hossza egy adott számítógép összes példánya között |
| A &#124; Perf, ahol a CounterName = = "Disk Transfers/mp" &#124; összefoglalja a AggregatedValue = percentilis (kártyabirtokos számlájának megterhelését, 95) a számítógépen |a 95. százalékos aránya/mp az összes számítógép között |
| A &#124; Perf, ahol CounterName = = "% Processor Time" és példánynév = = " &#124; _Total" összegzi a AggregatedValue = AVG (kártyabirtokos számlájának megterhelését) by bin (TimeGenerated, 1h), számítógép |A CPU-használat óránkénti átlaga az összes számítógépen |
| A &#124; Perf, ahol a Computer = = "Sajátgép" és a CounterName startswith_cs "%" és a példánynév &#124; = = "_Total" összefoglalja a AggregatedValue = percentilis (kártyabirtokos számlájának megterhelését, 70) raktárhely alapján (TimeGenerated, 1h), CounterName | Egy adott számítógép% százalékos számlálójának óránként 70 százalékos aránya |
| A &#124; Perf, ahol a CounterName = = "% Processor Time" és a példánynév = = "_Total" és a Computer &#124; = = "Sajátgép" összegzése ["min (kártyabirtokos számlájának megterhelését)"] = min (kártyabirtokos számlájának megterhelését), ["AVG (kártyabirtokos számlájának megterhelését)"] = AVG (kártyabirtokos számlájának megterhelését), ["percentile75 (kártyabirtokos számlájának megterhelését)"] = percentilis (kártyabirtokos számlájának megterhelését, 75), ["Max (kártyabirtokos számlájának megterhelését)"] = Max (kártyabirtokos számlájának megterhelését) by bin (TimeGenerated, 1h), számítógép |Egy adott számítógép óránkénti átlaga, minimális, maximális és 75 – percentilis CPU-használata |
| Perf &#124; , ahol ObjectName = = "MSSQL $ INST2: adatbázisok" és példánynév = = "Master" | Az adatbázis teljesítményobjektum a Master adatbázishoz tartozó összes teljesítményadatokat a megnevezett SQL Server példány INST2.  




## <a name="next-steps"></a>További lépések
* [Teljesítményszámlálók gyűjtése Linux-alkalmazásokból](data-sources-linux-applications.md) , beleértve a MySQL-t és az Apache HTTP-kiszolgálót.
* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../log-query/log-query-overview.md) .  
* Az összegyűjtött adatok [Power BIba](powerbi.md) való exportálása további vizualizációk és elemzések céljából.
