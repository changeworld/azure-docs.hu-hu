---
title: Tranzakciók és zárolási módok megbízható gyűjteményekben
description: Az Azure Service Fabric megbízható State Manager és megbízható gyűjtemények tranzakciója és zárolása.
ms.topic: conceptual
ms.date: 5/1/2017
ms.custom: sfrev
ms.openlocfilehash: 5f7b3a4d43d35f0d2965dd33c8f69143f4b3a8f7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938911"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Tranzakciók és zárolási módok az Azure-ban Service Fabric megbízható gyűjtemények

## <a name="transaction"></a>Tranzakció

A tranzakciók egyetlen logikai egységként végrehajtott műveletek sorozatából állnak. Az adatbázis-tranzakciók általános [sav](https://en.wikipedia.org/wiki/ACID) (*atomi*, *konzisztencia*, *elkülönítés*, *tartósság*) tulajdonságait mutatja be:

* **Atomi**: a tranzakciónak a Work atomi egységének kell lennie. Más szóval a rendszer minden adatmódosítást végez, vagy egyiket sem hajt végre.
* **Konzisztencia**: Ha elkészült, a tranzakciónak konzisztens állapotban kell hagynia az összes adategységet. A tranzakció végén minden belső adatstruktúrának helyesnek kell lennie.
* **Elkülönítés**: az egyidejű tranzakciók által végrehajtott módosításokat el kell különíteni a más párhuzamos tranzakciók által végrehajtott módosításokkal. Egy [ITransaction](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.itransaction?view=azure-dotnet) belüli művelethez használt elkülönítési szintet a műveletet végrehajtó [IReliableState](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestate?view=azure-dotnet) határozza meg.
* **Tartósság**: egy tranzakció befejeződése után a rendszer tartósan érvénybe helyezi a hatását. A módosítások rendszerhiba esetén is megmaradnak.

### <a name="isolation-levels"></a>Elkülönítési szintek

Az elkülönítési szint határozza meg, hogy a tranzakciót milyen mértékben kell elkülöníteni a más tranzakciók által végrehajtott módosításoktól.
A megbízható gyűjtemények két elkülönítési szintet támogatnak:

* **Ismételhető olvasás**: azt határozza meg, hogy az utasítások nem tudják olvasni azokat az adatokat, amelyeket más tranzakciók még nem véglegesítettek, és hogy más tranzakciók nem módosíthatják az aktuális tranzakció által beolvasott adatokat, amíg az aktuális tranzakció be nem fejeződik.
* **Pillanatkép**: azt adja meg, hogy a tranzakció bármely utasítása által beolvasott adatok a tranzakció elején található adatok tranzakciós szempontból konzisztens verziója.
  A tranzakció csak azokat az adatmódosításokat ismeri fel, amelyeket a tranzakció elindítása előtt véglegesítettek.
  A más tranzakciók által az aktuális tranzakció kezdete után végrehajtott adatmódosítások nem láthatók az aktuális tranzakcióban végrehajtó utasításokban.
  Ennek a hatása, mintha egy tranzakció utasításai beolvassák a tranzakció elején található véglegesített adatokat.
  A pillanatképek konzisztensek a megbízható gyűjtemények között.

A megbízható gyűjtemények automatikusan kiválasztják az adott olvasási művelethez használt elkülönítési szintet a tranzakció létrehozásakor a művelettől és a replika szerepköreitől függően.
A következő táblázat az elkülönítési szint alapértelmezett értékeit ábrázolja a megbízható szótárak és üzenetsor-műveletek esetében.

| Művelet \ szerepkör | Elsődleges | Másodlagos |
| --- |:--- |:--- |
| Egyetlen entitás olvasása |Ismételhető olvasás |Pillanatkép |
| Számbavétel, darabszám |Pillanatkép |Pillanatkép |

> [!NOTE]
> Az egyentitásos műveletekre vonatkozó gyakori példák a `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

A megbízható szótár és a megbízható üzenetsor is támogatja az *írások olvasását*.
Ez azt jelenti, hogy a tranzakción belüli bármilyen írás látható lesz a következő olvasási műveletben, amely ugyanahhoz a tranzakcióhoz tartozik.

## <a name="locks"></a>Zárolások

Megbízható gyűjteményekben az összes tranzakció szigorú kétfázisú zárolást valósít meg: egy tranzakció nem szabadítja fel a beszerzett zárolásokat, amíg a tranzakció megszakítással vagy véglegesítéssel leáll.

A megbízható szótár minden egyes entitási műveletnél sor szintű zárolást használ.
A megbízható üzenetsor a szigorú tranzakciós FIFO-tulajdonsághoz tartozó egyidejűségen kívülről kereskedik.
A megbízható üzenetsor olyan műveleti szintű zárolásokat használ, amelyek lehetővé teszik, hogy egy tranzakció `TryPeekAsync` és/vagy `TryDequeueAsync`, és egy tranzakciót egyszerre `EnqueueAsync`.
Vegye figyelembe, hogy a FIFO megőrzése érdekében, ha egy `TryPeekAsync` vagy `TryDequeueAsync` azt észleli, hogy a megbízható várólista üres, akkor a `EnqueueAsync`is zárolva lesznek.

Az írási műveletek mindig kizárólagos zárolást végeznek.
Olvasási műveletek esetén a zárolás néhány tényezőtől függ:

- A pillanatkép-elkülönítés használatával végzett olvasási művelet zárolás nélküli.
- Az összes ismételhető olvasási művelet alapértelmezés szerint a megosztott zárolásokat veszi igénybe.
- Az ismételhető olvasást támogató olvasási műveletek esetében azonban a felhasználó a megosztott zárolás helyett a frissítési zárolást is kérheti.
A frissítési zárolás egy aszimmetrikus zárolás, amely megakadályozza a holtpont gyakori formáját, amely akkor fordul elő, ha egy későbbi időpontban több tranzakció zárolja a lehetséges frissítéseket.

A zárolási kompatibilitási mátrix a következő táblázatban található:

| Kérelem \ megadott | None | Közös | Frissítés | Kizárólagos |
| --- |:--- |:--- |:--- |:--- |
| Közös |Nincs ütközés |Nincs ütközés |Ütközés |Ütközés |
| Frissítés |Nincs ütközés |Nincs ütközés |Ütközés |Ütközés |
| Kizárólagos |Nincs ütközés |Ütközés |Ütközés |Ütközés |

A megbízható gyűjtemények API-k időtúllépési argumentuma a holtpont észlelésére szolgál.
Például két tranzakció (T1 és T2) megkísérli olvasni és frissíteni a K1-et.
Lehetséges, hogy holtpontra válnak, mivel mindkettő a megosztott zárolással végződik.
Ebben az esetben az egyik vagy mindkét művelet időtúllépést eredményez. Ebben a forgatókönyvben egy frissítési zár megakadályozhatja az ilyen holtpontot.

## <a name="next-steps"></a>Következő lépések

* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Értesítések Reliable Services](service-fabric-reliable-services-notifications.md)
* [Biztonsági mentés és visszaállítás Reliable Services (vész-helyreállítás)](service-fabric-reliable-services-backup-restore.md)
* [Megbízható állapot-kezelő konfigurációja](service-fabric-reliable-services-configuration.md)
* [Fejlesztői referenciák megbízható gyűjteményekhez](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
