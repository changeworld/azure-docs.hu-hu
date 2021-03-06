---
title: Ismétlődő feladatok és munkafolyamatok ütemezése a Azure Logic Appsban
description: Áttekintés az ismétlődő automatizált feladatok, folyamatok és munkafolyamatok ütemezéséről Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0f6ec158cf6ab855191e6796be3abec7d37439a0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270562"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Ismétlődő automatizált feladatok, folyamatok és munkafolyamatok ütemezett és futtatása Azure Logic Apps

A Logic Apps használatával automatizált ismétlődő feladatokat és folyamatokat hozhat létre és futtathat ütemezett időközönként. Ha olyan logikai alkalmazás-munkafolyamatot hoz létre, amely egy beépített ismétlődési eseményindítóval vagy egy csúszó ablak eseményindítóval kezdődik, amely az ütemezések típusú eseményindítók, a feladatokat azonnal, később vagy ismétlődő időközönként is futtathatja. Az Azure-on belüli és kívüli szolgáltatásokat, például HTTP-vagy HTTPS-végpontokat hívhat meg, üzeneteket küldhet az Azure-szolgáltatásba, például az Azure Storage-ba és a Azure Service Busba, vagy beolvashatja a fájlmegosztást feltöltött fájlokat. Az ismétlődési eseményindítóval összetett ütemezéseket és speciális ismétlődéseket is beállíthat a futó feladatokhoz. Ha többet szeretne megtudni a beépített ütemezett eseményindítókkal és műveletekkel kapcsolatban, tekintse meg az eseményindítók és az [ütemezett műveletek](#schedule-actions) [időzítését](#schedule-triggers) ismertető témakört. 

> [!TIP]
> Ütemezheti és futtathatja az ismétlődő munkaterheléseket anélkül, hogy külön logikai alkalmazást hozna létre minden ütemezett feladathoz, és a [munkafolyamatok régiónként és előfizetésben](../logic-apps/logic-apps-limits-and-config.md#definition-limits)való futtatásával korlátozva van. Ehelyett használhatja az Azure rövid útmutató sablonja által létrehozott Logic app-mintát [: Logic apps Feladatütemező](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/).
>
> A Logic Apps Feladatütemező sablon létrehoz egy CreateTimerJob logikai alkalmazást, amely egy TimerJob logikai alkalmazást hív meg. Ezt követően a CreateTimerJob logikai alkalmazást API-ként hívhatja meg egy HTTP-kérelem elküldésével, és a kérelem bemenetként való átadásával. A CreateTimerJob logikai alkalmazás minden egyes hívása meghívja a TimerJob Logic alkalmazást, amely egy új TimerJob-példányt hoz létre, amely folyamatosan fut a megadott ütemezés alapján, vagy amíg a megadott korlátot nem teljesíti. Így tetszőleges számú TimerJob-példányt futtathat úgy, hogy a munkafolyamatok korlátai miatt nem kell aggódnia, mert a példányok nem egyéni logikai alkalmazások munkafolyamat-definíciói vagy erőforrásai.

Ez a lista néhány példát mutat be, amelyeket a beépített eseményindítók használatával futtathat:

* Belső adat beolvasása, például minden nap SQL tárolt eljárás futtatása.

* Külső adatok beolvasása, például a NOAA szolgáltatás 15 percenkénti lekéréses időjárási jelentései.

* Jelentésadatok küldése, például e-mailben az összes megrendelés összegzése az elmúlt héten megadott összegnél nagyobb mértékben.

* Adatok feldolgozása, például a ma feltöltött képek tömörítése hétköznapokon a munkaidőn kívül.

* Az adattisztítást, például a három hónapnál régebbi összes Tweet törlését.

* Az archivált adatok, például a leküldéses számlák egy backup szolgáltatásba, minden nap 1:00-kor, a következő kilenc hónapban.

A következő művelet futtatása előtt a beépített műveleteket is használhatja a munkafolyamat szüneteltetéséhez, például:

* Várjon, amíg egy hétköznapon el nem küldi az állapot-frissítést e-mailben.

* Késleltesse a munkafolyamatot addig, amíg egy HTTP-hívásnak nincs ideje befejezni az eredmény folytatása és beolvasása előtt.

Ez a cikk a beépített eseményindítók és műveletek ütemezett funkcióit ismerteti.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Eseményindítók ütemezett indítása

A logikai alkalmazás munkafolyamata az ismétlődési eseményindító vagy a csúszó ablak eseményindítójának használatával indítható el, amely nincs egyetlen adott szolgáltatáshoz vagy rendszerhez társítva, például Office 365 Outlook vagy SQL Server. Ezek az eseményindítók elindítják és futtatják a munkafolyamatot a megadott ismétlődés alapján, ahol kiválaszthatja az intervallumot és a gyakoriságot, például a másodpercek számát, a perceket és az időpontokat mindkét eseményindító esetében, illetve az ismétlődési eseményindítóhoz tartozó napok, hetek vagy hónapok számát. Megadhatja a kezdési dátumot és az időt, valamint az időzónát is. Minden alkalommal, amikor egy eseményindító tüzek, Logic Apps létrehoz és futtat egy új munkafolyamat-példányt a logikai alkalmazáshoz.

Ezek az eseményindítók közötti különbségek:

* **Ismétlődés**: a munkafolyamatot a megadott ütemterv alapján rendszeres időközönként futtatja. Ha az ismétlődések kimaradnak, az ismétlődési eseményindító nem dolgozza fel a kihagyott ismétlődéseket, de a következő ütemezett intervallummal újraindítja az ismétlődéseket. Megadhatja a kezdési dátumot és az időt, valamint az időzónát is. Ha a "nap" lehetőséget választja, megadhatja az óra napját és percét, például minden nap 2:30-kor. Ha a "hét" lehetőséget választja, akkor a hét napjait is kiválaszthatja, például a szerda és a szombat lehetőséget. További információ: [ismétlődő feladatok és munkafolyamatok létrehozása, ütemezése és futtatása az ismétlődési eseményindítóval](../connectors/connectors-native-recurrence.md).

* **Csúszó ablak**: rendszeres időközönként futtatja a munkafolyamatot, amely folytonos adattömbökben kezeli az adategységeket. Ha az ismétlődések kimaradnak, a csúszó ablak eseményindítója visszatér, és feldolgozza a kihagyott ismétlődéseket. Megadhatja a kezdési dátumot és időt, az időzónát és egy időtartamot, amellyel késleltetheti az egyes ismétlődéseket a munkafolyamatban. Ez az trigger nem rendelkezik a napok, hetek és hónapok megadására, a nap órájára, az óra percére és a hét napjaira. További információ: [ismétlődő feladatok és munkafolyamatok létrehozása, beosztása és futtatása a csúszó ablakos triggerrel](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Ütemezett műveletek

A Logic app-munkafolyamatban bármilyen művelet után a késleltetés és a késleltetés, amíg a műveletek a következő művelet futtatása előtt nem tudják a munkafolyamatot várni.

* **Késleltetés**: várjon a következő művelet futtatására a megadott számú időegységre (például másodperc, perc, óra, nap, hét vagy hónap). További információ: [a következő művelet késleltetése a munkafolyamatokban](../connectors/connectors-native-delay.md).

* **Késleltetés eddig**: várja meg a következő művelet futtatását a megadott dátumig és időpontig. További információ: [a következő művelet késleltetése a munkafolyamatokban](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>A kezdő dátum és idő mintái

<a name="start-time"></a>

Íme néhány példa, amely bemutatja, hogyan szabályozhatja az ismétlődést a kezdő dátummal és időponttal, valamint azt, hogy az Logic Apps szolgáltatás hogyan futtatja ezeket az ismétlődéseket:

| Kezdési idő | Ismétlődés ütemezés nélkül | Ismétlődés az ütemtervtel (csak ismétlődési eseményindító) |
|------------|-----------------------------|----------------------------------------------------|
| nEz egy | Azonnal futtatja az első munkafolyamatot. <p>A jövőbeli munkaterheléseket az utolsó futtatási idő alapján futtatja. | Azonnal futtatja az első munkafolyamatot. <p>A jövőbeli munkaterheléseket a megadott ütemterv alapján futtatja. |
| Kezdési idő a múltban | **Ismétlődési** eseményindító: a megadott kezdési idő alapján kiszámítja a futtatási időpontokat, és elveti a korábbi futtatási időpontokat. Futtatja az első számítási feladatot a következő későbbi futtatási időpontban. <p>A jövőbeli munkaterheléseket az utolsó futtatási idő számításai alapján futtatja. <p><p>**Csúszó ablak** trigger: a megadott kezdési időpont alapján kiszámítja a futtatási időpontokat, és kiértékeli a korábbi futtatási időpontokat. <p>A jövőbeli számítási feladatokat a megadott kezdési időpontból számított számítások alapján futtatja. <p><p>További magyarázatért tekintse meg a táblázat következő példáját. | A kezdési időpontból kiszámított ütemterv alapján az első számítási feladatot a kezdési időpontnál *hamarabb* futtatja. <p>A jövőbeli munkaterheléseket a megadott ütemterv alapján futtatja. <p>**Megjegyzés:** Ha egy ütemezett ismétlődést ad meg, de nem ad meg órákat vagy percet az ütemtervhez, akkor a jövőbeli futási idők az első futási idő órában vagy percben lesznek kiszámítva. |
| Kezdési idő jelenleg vagy a jövőben | Futtatja az első számítási feladatot a megadott kezdési időpontban. <p>A jövőbeli munkaterheléseket az utolsó futtatási idő számításai alapján futtatja. | A kezdési időpontból kiszámított ütemterv alapján az első számítási feladatot a kezdési időpontnál *hamarabb* futtatja. <p>A jövőbeli munkaterheléseket a megadott ütemterv alapján futtatja. <p>**Megjegyzés:** Ha egy ütemezett ismétlődést ad meg, de nem ad meg órákat vagy percet az ütemtervhez, akkor a jövőbeli futási idők az első futási idő órában vagy percben lesznek kiszámítva. |
||||

> [!IMPORTANT]
> Ha az ismétlődések nem határoznak meg speciális ütemezési beállításokat, a jövőbeli ismétlődések az utolsó futási időn alapulnak.
> Ezeknek az ismétlődéseknek az indítási időpontja a tárolási hívások során felmerülő tényezők, például a késés miatt eltérhet. A következő lehetőségek egyikének használatával győződjön meg arról, hogy a logikai alkalmazás nem hagyja ki az ismétlődést, különösen akkor, ha a gyakoriság napokban vagy már nem érhető el.
> 
> * Adja meg az ismétlődés kezdési idejét.
> 
> * Adja meg az órákat és a perceket, hogy mikor futtassa az ismétlődést a **ezen órákon** és a **percekben** megadott tulajdonságok alapján.
> 
> * Az ismétlődési eseményindító helyett használja a [csúszó ablak eseményindítóját](../connectors/connectors-native-sliding-window.md).

*Példa korábbi kezdési időre és ismétlődésre, de nincs ütemezése*

Tegyük fel, hogy az aktuális dátum és idő szeptember 8., 2017, 1:00 PM. Megadhatja a kezdési dátumot és az időpontot szeptember 7., 2017., 2:00 PM, amely a múltban, valamint egy ismétlődés, amely két naponként fut.

| Kezdési idő | Aktuális idő | Ismétlődés | Ütemezés |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09 –**07** , 2:00 PM) | 2017-09 –**08**T13:00:00Z <br>(2017-09 –**08** , 1:00 PM) | Két naponta | nEz egy |
|||||

Az ismétlődési eseményindító esetében a Logic Apps motor a kezdési idő alapján kiszámítja a futási időt, elveti a múltbeli futtatási időpontokat, a következő jövőbeli kezdési időt használja az első futtatáshoz, és kiszámítja a jövőbeli futtatásokat az utolsó futtatási idő alapján.

Így néz ki az ismétlődés:

| Kezdési idő | Első futtatás időpontja | Jövőbeli futtatási idők |
|------------|----------------|------------------|
| 2017-09 –**07** , 2:00 PM | 2017-09-**09** , 2:00 PM | 2017-09 –**11** , 2:00 PM </br>2017-09 –**13** , 2:00 PM </br>2017-09 –**15** , 2:00 PM </br>És így tovább... |
||||

Tehát attól függetlenül, hogy a múltban milyen messzire van szükség a kezdési időponthoz, például: 2017-09-**05** , 2:00 pm vagy 2017-09-2:00**01** , az első futtatás mindig a következő jövőbeli kezdési időpontot használja.

A csúszó ablakos trigger esetén a Logic Apps motor a kezdési idő alapján kiszámítja a futási időt, a korábbi futtatási időpontokat, az első futtatás kezdő időpontját, és a jövőbeli futtatásokat a kezdési időpont alapján számítja ki.

Így néz ki az ismétlődés:

| Kezdési idő | Első futtatás időpontja | Jövőbeli futtatási idők |
|------------|----------------|------------------|
| 2017-09 –**07** , 2:00 PM | 2017-09 –**07** , 2:00 PM | 2017-09-**09** , 2:00 PM </br>2017-09 –**11** , 2:00 PM </br>2017-09 –**13** , 2:00 PM </br>2017-09 –**15** , 2:00 PM </br>És így tovább... |
||||

Tehát attól függetlenül, hogy a múltban milyen messzire van szükség a kezdési időponthoz, például: 2017-09-**05** , 2:00 pm vagy 2017-09-2:00**01** , az első futtatáskor mindig a megadott kezdési időpontot használja.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Ismétlődések – példa

Az alábbi példa a beállításokat támogató eseményindítók különböző ismétlődéseit jeleníti meg:

| Eseményindító | Ismétlődés | Intervallum | Frequency | Kezdési idő | Ezekben a napokban | Ezen az órában | Ezen a perceken | Megjegyzés |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Megismétlődésének <br>Csúszóablak | Futtatás 15 percenként (nincs kezdő dátum és idő) | 15 | Perc | nEz egy | érhető | nEz egy | nEz egy | Ez az ütemezés azonnal elindul, majd az utolsó futási idő alapján kiszámítja a jövőbeli ismétlődéseket. |
| Megismétlődésének <br>Csúszóablak | Futtatás 15 percenként (kezdő dátummal és idővel) | 15 | Perc | *StartDate* T*kezdő időpont*– Z | érhető | nEz egy | nEz egy | Ez az ütemezés nem indul el *hamarabb* a megadott kezdési dátumnál és időpontnál, majd az utolsó futási idő alapján kiszámítja a jövőbeli ismétlődéseket. |
| Megismétlődésének <br>Csúszóablak | Minden órában fut, az óra (kezdő dátummal és idővel) | 1 | Óra | *StartDate* THH: 00:00Z | érhető | nEz egy | nEz egy | Ez az ütemterv nem indul el *hamarabb* a megadott kezdési dátumnál és időpontnál. A jövőbeli ismétlődések óránként futnak a "00" percben, amely a kezdési időpontból lesz kiszámítva. <p>Ha a gyakoriság értéke "Week" vagy "Month", akkor ez az ütemterv hetente vagy havonta egy nappal fut le. |
| Megismétlődésének <br>Csúszóablak | Futtatás óránként, naponta (nincs kezdő dátum és idő) | 1 | Óra | nEz egy | érhető | nEz egy | nEz egy | Ez az ütemezés azonnal elindul, és az utolsó futási idő alapján kiszámítja a jövőbeli ismétlődéseket. <p>Ha a gyakoriság értéke "Week" vagy "Month", akkor ez az ütemterv hetente vagy havonta egy nappal fut le. |
| Megismétlődésének <br>Csúszóablak | Futtatás óránként, minden nap (kezdő dátummal és idővel) | 1 | Óra | *StartDate* T*kezdő időpont*– Z | érhető | nEz egy | nEz egy | Ez az ütemezés nem indul el *hamarabb* a megadott kezdési dátumnál és időpontnál, majd az utolsó futási idő alapján kiszámítja a jövőbeli ismétlődéseket. <p>Ha a gyakoriság értéke "Week" vagy "Month", akkor ez az ütemterv hetente vagy havonta egy nappal fut le. |
| Megismétlődésének <br>Csúszóablak | Minden órában 15 percenként fut, óránként (kezdő dátummal és idővel) | 1 | Óra | *StartDate* T00:15:00Z | érhető | nEz egy | nEz egy | Ez az ütemterv nem indul el *hamarabb* a megadott kezdési dátumnál és időpontnál. A jövőbeli ismétlődések a kezdési időpontból kiszámított "15" perces jellel futnak, így: 00:15, 1:15, 2:15 AM és így tovább. |
| Ismétlődés | Minden órában 15 percenként fut, óránként (nincs kezdő dátum és idő) | 1 | Day | nEz egy | érhető | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Ez az ütemterv a következő időpontban fut: 00:15, 1:15, 2:15, és így tovább. Emellett ez az ütemterv az "Hour" gyakoriságának és a "15" perces kezdési időpontnak felel meg. |
| Ismétlődés | Futtassa 15 percenként a megadott percenkénti jelzéseket (nincs kezdő dátum és idő). | 1 | Day | nEz egy | érhető | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ez az ütemezés addig nem indul el, amíg a következő megadott 15 perces megjelölés be nem fejeződik. |
| Ismétlődés | A logikai alkalmazás mentésekor naponta, a percek alatt, *illetve* a percben is futtatható. | 1 | Day | nEz egy | érhető | 8 | nEz egy | Kezdési dátum és idő nélkül ez az ütemterv a logikai alkalmazás mentésekor (PUT művelet) történő mentés időpontja alapján fut le. |
| Ismétlődés | Napi Futtatás 8:00 ÓRAKOR (kezdő dátummal és idővel) | 1 | Day | *StartDate* T08:00:00Z | érhető | nEz egy | nEz egy | Ez az ütemterv nem indul el *hamarabb* a megadott kezdési dátumnál és időpontnál. A jövőbeli előfordulások naponta, 8:00 ÓRAKOR futnak. | 
| Ismétlődés | Napi Futtatás 8:30 ÓRAKOR (nincs kezdő dátum és idő) | 1 | Day | nEz egy | érhető | 8 | 30 | Ez az ütemterv minden nap 8:30 ÓRAKOR fut. |
| Ismétlődés | Napi Futtatás 8:30 órakor és 4:30 PM | 1 | Day | nEz egy | érhető | 8, 16 | 30 | |
| Ismétlődés | Napi Futtatás 8:30 ÓRAKOR, 8:45, 4:30 PM és 4:45 PM | 1 | Day | nEz egy | érhető | 8, 16 | 30, 45 | |
| Ismétlődés | Minden szombaton fut 5 ÓRAKOR (nincs kezdő dátum és idő) | 1 | Hét | nEz egy | Szombat | 17 | 00 | Ez az ütemterv minden szombaton 5:00 ÓRAKOR fut. |
| Ismétlődés | Minden szombaton fut 5 ÓRAKOR (kezdő dátummal és idővel) | 1 | Hét | *StartDate* KÖZLEMÉNNYEL17:00:00Z | Szombat | nEz egy | nEz egy | Ez az ütemterv nem *indul el a megadott* kezdési dátumnál és időpontnál (ebben az esetben a 2017. szeptember 9-én, 5:00 órakor). A jövőbeli ismétlődések minden szombaton 5:00 ÓRAKOR futnak. |
| Ismétlődés | Minden kedden, csütörtökön és a logikai alkalmazás *mentésekor a* percben is futtatható.| 1 | Hét | nEz egy | "Kedd", "csütörtök" | 17 | nEz egy | |
| Ismétlődés | Minden órában fut a munkaidőn belül | 1 | Hét | nEz egy | Válassza a szombat és a vasárnap kivételével az összes napot. | Válassza ki a nap azon óráját, amelyet szeretne. | Válassza ki a kívánt órányi percet. | Ha például a munkaidő 8:00 – 5:00 PM, válassza a "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" lehetőséget a nap órájában. <p>Ha a munkaidő 8:30 – 5:30 PM, válassza ki a nap előző óráját, valamint a "30" órát az óra percében. |
| Ismétlődés | Hetente egyszer fut a hétvégén | 1 | Hét | nEz egy | "Szombat", "vasárnap" | Válassza ki a nap azon óráját, amelyet szeretne. | Szükség szerint válassza ki az óra bármelyik percét. | Ez az ütemterv minden szombaton és vasárnap a megadott ütemterv szerint fut. |
| Ismétlődés | Futás 15 percenként, csak hétfőn | 2 | Hét | nEz egy | Hétfőtől | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ez az ütemterv 15 percenként minden más hétfőn fut. |
| Ismétlődés | Minden hónap futtatása | 1 | Month | *StartDate* T*kezdő időpont*– Z | érhető | érhető | érhető | Ez az ütemezés nem indul el *hamarabb* a megadott kezdési dátumnál és időpontnál, és kiszámítja a jövőbeli ismétlődéseket a kezdő dátumon és időpontban. Ha nem ad meg kezdési dátumot és időpontot, az ütemterv a létrehozás dátumát és időpontját használja. |
| Ismétlődés | Minden órában futtatható havonta egy napra | 1 | Month | {Lásd: Megjegyzés} | érhető | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {Lásd: Megjegyzés} | Ha nem ad meg kezdési dátumot és időpontot, az ütemterv a létrehozás dátumát és időpontját használja. Az ismétlődési ütemterv percének vezérléséhez határozza meg az óra percét, a kezdési időpontot, vagy használja a létrehozási időt. Ha például a kezdési vagy a létrehozási idő 8:25, akkor ez az ütemterv 8:25 ÓRAKOR, 9:25 AM, 10:25 és hasonló módon fut. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Futtatás csak egyszer

Ha a későbbiekben csak egyszer szeretné futtatni a logikai alkalmazást, használhatja a **Scheduler: Run Once Jobs** sablont. Miután létrehozta az új logikai alkalmazást, de a Logic Apps Designer megnyitása előtt, a **sablonok** szakaszban, a **Kategória** listáról válassza az **ütemterv**lehetőséget, majd válassza ki ezt a sablont:

![Válassza a "Scheduler: Futtatás a feladatok után" sablont](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Vagy ha a logikai alkalmazást a **http-kérelem fogadása** után is elindíthatja, és a kezdési időpontot az trigger paraméterének adja át. Az első művelethez használja a **késleltetés az ütemezésig** műveletet, és adja meg a következő művelet futásának időpontját.

## <a name="next-steps"></a>Következő lépések

* [Ismétlődő feladatok és munkafolyamatok létrehozása, ütemezése és futtatása az ismétlődési eseményindítóval](../connectors/connectors-native-recurrence.md)
* [Ismétlődő feladatok és munkafolyamatok létrehozása, beosztása és futtatása a csúszó ablakos triggerrel](../connectors/connectors-native-sliding-window.md)
* [Munkafolyamatok szüneteltetése késleltetési műveletekkel](../connectors/connectors-native-delay.md)
