---
title: A Azure Stream Analytics időközbeni kezelési értelmezése
description: Megtudhatja, hogyan működik az idő a kezelés Azure Stream Analyticsban, mint például a legjobb kezdési idő kiválasztása, a késői és korai események kezelése, valamint az időkezelési mérőszámok.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 367b7c2e1ce1c8b3c0dbc02003218b76096b409d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354652"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>A Azure Stream Analytics időközbeni kezelési értelmezése

Ebben a cikkben azt mutatjuk be, hogyan lehet tervezési döntéseket hozni a gyakorlati idő kezelésére a Azure Stream Analytics szolgáltatásban. Az időigényes tervezési döntések szorosan kapcsolódnak az események rendezési tényezőinek.

## <a name="background-time-concepts"></a>Háttérbeli időbeli fogalmak

A vitafórum jobb megjelenítéséhez hozzon létre néhány háttér-fogalmat:

- **Esemény időpontja**: az az időpont, amikor az eredeti esemény történt. Például amikor egy mozgó autó az autópályán egy díjköteles standot közelít.

- **Feldolgozási idő**: az az idő, amikor az esemény eléri a feldolgozó rendszerét, és megfigyelhető. Ha például egy díjköteles stand-érzékelő látja az autót, és a számítógéprendszer eltarthat néhány másodpercig, hogy feldolgozza az adatfeldolgozást.

- **Vízjel**: egy esemény időjelölője, amely azt jelzi, hogy a rendszer milyen eseményeket ingressed a streaming processzornak. A vízjelek lehetővé teszik, hogy a rendszer egyértelmű előrehaladást jelezzen az események betöltéséhez. A streamek jellegéből adódóan a bejövő esemény adatai soha nem állnak le, így a vízjelek jelzik, hogy az adatfolyam egy bizonyos pontjára mutat.

   A vízjel fogalma fontos. A vízjelek lehetővé teszik a Stream Analytics annak meghatározását, hogy a rendszer mikor hozhat létre teljes, helyes és ismételhető eredményeket, amelyeket nem kell visszavonni. A feldolgozás a kiszámítható és megismételhető garantált módon valósítható meg. Ha például egy bizonyos hibakezelés feltételéhez újra kell számolni, a vízjelek biztonságos kezdési és befejezési pontok.

A témához tartozó további forrásokért lásd: Tyler Akidau blog-bejegyzéseinek [streaming 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) és [streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choosing-the-best-starting-time"></a>A legjobb kezdési idő kiválasztása

Stream Analytics két lehetőséget biztosít a felhasználóknak a kiválogatási esemény időpontjára:

1. **Érkezés időpontja**  

   Ha az esemény eléri a forrást, az érkezési idő a bemeneti forráshoz van rendelve. Az érkezési időt a **EventEnqueuedUtcTime** tulajdonsággal érheti el Event Hubs bemenetek, a **IoTHub. EnqueuedTime** tulajdonsága IoT hubhoz, és a **BlobProperties. LastModified** tulajdonság használata a blob-bevitelhez.

   Az érkezési idő használata az alapértelmezett viselkedés, és az adatarchiválási forgatókönyvekhez legjobban használható, ahol nincs szükség időbeli logikára.

2. **Alkalmazás időpontja** (más néven esemény időpontja)

   A rendszer az esemény létrehozásakor rendeli hozzá az alkalmazási időt, és az esemény hasznos adatának részét képezi. Az események alkalmazási idő szerinti feldolgozásához használja a Select lekérdezés **timestamp by** záradékát. Ha a **timestamp by** záradék hiányzik, az események az érkezési idő szerint lesznek feldolgozva.

   Fontos, hogy egy időbélyeget használjon a hasznos adatokhoz, amikor időbeli logikát vesznek fel. Így a rendszer a forrásrendszer vagy a hálózat késéseit is figyelembe veheti.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>A Azure Stream Analytics folyamatának időpontja

Az alkalmazás időpontjának használatakor az idő előrehaladtával a bejövő eseményektől függ. Az adatfolyam-feldolgozó rendszer számára nehéz megállapítani, hogy nincsenek-e események, vagy ha az események késleltetve vannak. Ezért Azure Stream Analytics heurisztikus vízjeleket generál a következő módokon az egyes bemeneti partíciók esetében:

1. Ha bármilyen bejövő esemény van, akkor a vízjel az a legnagyobb esemény, ameddig a rendszer eddig levonva a megrendelésen kívüli tolerancia ablak méretét.

2. Ha nincs beérkező esemény, a vízjel a jelenlegi becsült beérkezési idő (a jelenetek virtuális gép mögött eltelt idő, amely az eseményeket az utolsó alkalommal egy bemeneti eseménynek tekinti, valamint a bemeneti esemény érkezési idejét) mínusz a késői érkezési tolerancia ablak.

   Az érkezési időt csak a becsült érték alapján lehet megbecsülni, mert a valós idejű beérkezési idő a bemeneti esemény-közvetítőn, például a Event Hubs, nem pedig az eseményeket feldolgozó Azure Stream Analytics virtuális gép.

A kialakítás két további célt szolgál a vízjelek generálásán kívül:

1. A rendszer időben hozza létre az eredményeket a bejövő események vagy azok nélkül.

   Megadhatja, hogy milyen időben szeretné megjeleníteni a kimenet eredményét. A Azure Portal a Stream Analytics feladatokhoz tartozó **esemény rendezése** lapon beállíthatja a nem **megrendelési események** beállítást. Ha beállítja ezt a beállítást, vegye figyelembe az időkorlátot az esemény-adatfolyamon kívüli megrendelési események tűréshatárával.

   A késői beérkezési tolerancia ablak fontos a vízjelek létrehozásában, még a bejövő események hiányában is. Időnként előfordulhat, hogy egy olyan időszak van, amelyben nem érkeznek bejövő események, például ha egy esemény bemeneti adatfolyama ritka. Ezt a problémát súlyosbítja több partíció használata a bemeneti esemény-átvitelszervezőben.

   A késői beérkezési tolerancia nélküli adatfolyam-feldolgozási rendszerek késleltetett kimenetekkel rendelkezhetnek, ha a bemeneti adatok ritkaak, és több partíciót használnak.

2. A rendszerviselkedésnek ismételhetőnek kell lennie. Az ismételhetőség a streaming adatfeldolgozó rendszer fontos tulajdonsága.

   A vízjel az érkezési idő és az alkalmazás időpontjából származik. Mindkettő megmarad az Event brokerben, így ismételhető. Abban az esetben, ha az érkezési időt az események hiánya miatt kell megbecsülni, Azure Stream Analytics a naplók a Visszajátszási idő becsült megérkezési idejét a sikertelen helyreállítás érdekében.

Figyelje meg, hogy amikor az esemény időpontjában az **érkezési időt** választja, nem kell konfigurálnia a megrendelésen kívüli toleranciát és a késői érkezési toleranciát. Mivel az **érkezési idő** garantált, hogy a bemeneti monoton módon egyre növekszik, Azure stream Analytics egyszerűen figyelmen kívül hagyja a konfigurációkat.

## <a name="late-arriving-events"></a>Későn érkező események

A késői érkezési tolerancia ablakának definíciója szerint minden bejövő eseménynél Azure Stream Analytics összehasonlítja az **esemény időpontját** az **érkezési idővel**. Ha az esemény időpontja kívül esik a tolerancia ablakon, beállíthatja, hogy a rendszer eldobja az eseményt, vagy állítsa be az esemény időpontját a tűréshatáron belülre.

Vegye figyelembe, hogy a küszöbértékek generálása után a szolgáltatás képes lehet olyan eseményeket fogadni, amelyekben az esemény ideje alacsonyabb a vízjelnél. Beállíthatja, hogy a szolgáltatás **eldobja** ezeket az eseményeket, vagy **állítsa be** az esemény időpontját a vízjel értékre.

A beállítás részeként az esemény **System. timestamp** beállítása az új értékre van állítva, de maga az **esemény** időmezője nem módosul. Ez a beállítás az egyetlen olyan helyzet, amikor az esemény **System. időbélyega** eltérő lehet az esemény időmezőjében szereplő értéktől, és váratlan eredményeket eredményezhet.

## <a name="handling-time-variation-with-substreams"></a>Az idő variációja alstreamekkel

Az itt leírt heurisztikus vízjel-létrehozási mechanizmus jól működik a legtöbb esetben, amikor az idő többnyire szinkronizálva van a különböző esemény-küldők között. A valós életben azonban különösen sok IoT forgatókönyv esetén a rendszer kis mértékben szabályozza az esemény-küldők óráját. Az esemény-küldők a mezőben lévő összes típusú eszköz lehetnek, például a hardver és a szoftver különböző verzióiban.

Ahelyett, hogy a vízjel globálisan a bemeneti partíció összes eseményére felhasználja a küszöbértéket, Stream Analytics az alstreamek nevű másik mechanizmussal segíti Önt. A feladatban alstreameket is használhat, ha olyan feladatot tartalmazó lekérdezést ír, amely a [**timestamp by**](/stream-analytics-query/timestamp-by-azure-stream-analytics) záradékot és **a kulcsszót**használja. Az ALStream kijelöléséhez adja meg a kulcs oszlop nevét az **over** kulcsszó után, például egy `deviceid`, hogy a rendszer az adott oszlop időházirendjeit alkalmazza. Mindegyik ALStream saját független vízjelet kap. Ez a mechanizmus hasznos lehet az időigényes kimenet létrehozásához, amikor nagy órajel-eltéréseket vagy hálózati késéseket tapasztal az események küldői között.

Az alstreamek Azure Stream Analytics által biztosított egyedi megoldás, amelyet más adatfolyam-feldolgozási rendszerek nem kínálnak. A Stream Analytics a késői beérkezés tolerancia ablakát alkalmazza a bejövő eseményekre, amikor alstreamek vannak használatban. Az alapértelmezett beállítás (5 másodperc) valószínűleg túl kicsi az eltérő időbélyegzővel rendelkező eszközök esetében. Javasoljuk, hogy 5 percen belül kezdjen el, és végezze el a módosításokat az eszköz órájának döntési mintája alapján.

## <a name="early-arriving-events"></a>Korán érkező események

Előfordulhat, hogy észrevette egy másik, korai érkezési időszakra vonatkozó fogalmat, amely a késői érkezési tolerancia ablakának ellenkezőjére hasonlít. Ez az ablak 5 percen belül megoldódott, és egy másik célt szolgál a késői érkezéstől kezdve.

Mivel Azure Stream Analytics garantálja, hogy mindig a teljes eredményt hozza létre, csak a feladathoz tartozó első kimeneti **időpontot adhatja meg,** nem pedig a bemeneti időpontot. A befejezési idő megadása kötelező, hogy a teljes ablak feldolgozása megtörténjen, nem csupán az ablak közepétől.

Stream Analytics ezután a lekérdezési specifikációból származtatja a kezdési időt. Mivel azonban a bemeneti események közvetítőjét csak az érkezési idő indexeli, a rendszernek le kell fordítania a kezdési esemény időpontját az érkezési időre. A rendszer megkezdheti az események feldolgozását az adott pontról a bemeneti esemény-átvitelszervezőben. A korai érkezési időszakra vonatkozó korláttal a fordítás egyszerű. A kezdési esemény időpontja mínusz az 5 perces korai érkezési ablak. Ez a számítás azt is jelenti, hogy a rendszer minden olyan eseményt elveszít, amely 5 percnél nagyobb az érkezési időpontnál.

Ez a koncepció annak biztosítására szolgál, hogy a feldolgozás megismételhető legyen, függetlenül attól, hogy hol kezdi a kimenetet. Ilyen mechanizmus nélkül nem lehet garantálni az ismételhetőséget, hiszen számos más streaming-rendszer is igénybe veszik.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Az események rendezési idejére vonatkozó tűréshatárok mellékhatásai

Stream Analytics feladatok több **rendezési** lehetőséggel rendelkeznek. A két konfigurálható a Azure Portalban: a nem **megrendelési események** beállítás (a megrendelésen kívüli tűréshatár), valamint a **késői beállításban megjelenő események** (késői beérkezés tűréshatára). A **korai beérkezés** tűréshatára rögzített, és nem módosítható. Ezeket az időszabályzatokat a Stream Analytics az erős garanciák biztosítására használják. Ezek a beállítások azonban néha váratlan következményekkel járnak:

1. Véletlenül túl korán küldött események küldése.

   A korai eseményeket általában nem lehet kiadni. Lehetséges, hogy a rendszer a korai eseményeket küldi el a kimenetnek, ha a küldő órája túl gyorsan fut. A rendszer elveti az összes korán érkező eseményt, így azok nem jelennek meg a kimenetből.

2. A régi események küldése Event Hubsra, hogy Azure Stream Analytics dolgozza fel őket.

   Habár a régi események először ártalmatlannak tűnnek, a késői érkezési tolerancia alkalmazása miatt előfordulhat, hogy a régi eseményeket el lehet dobni. Ha az események túl régiek, a **System. timestamp** érték módosul az esemény betöltése során. Ennek a viselkedésnek köszönhetően a jelenleg Azure Stream Analytics a közel valós idejű esemény-feldolgozási forgatókönyvekhez alkalmasabb, az esemény-feldolgozási forgatókönyvek helyett. Megadhatja azokat az **eseményeket, amelyek késői időben érkeznek** a lehető legnagyobb értékre (20 nap), hogy bizonyos esetekben megkerülje ezt a viselkedést.

3. Úgy tűnik, hogy a kimenetek késleltetve lesznek.

   Az első vízjel a számított időpontban jön létre: a rendszer eddig megfigyelt **maximális eseménye** , mínusz a nem megengedett tűréshatárok ablakának mérete. Alapértelmezés szerint a rendszer az elutasítási tűréshatárt nulla (00 perc és 00 másodperc) értékre állítja be. Ha magasabb, nullától eltérő időértékre állítja azt, akkor a rendszer az első kiszámított küszöbérték miatt késlelteti a folyamatos átviteli feladatot (vagy nagyobb).

4. A bemenetek ritkák.

   Ha nincs bemenet egy adott partícióban, a rendszer a küszöbértéket az **érkezési idő** alapján számítja ki a késői érkezési tolerancia ablakban. Ennek eredményeképpen, ha a bemeneti események nem ritkák és gyérek, a kimenet késleltethető ennél az időtartamnál. A késő értékre **érkező alapértelmezett események** 5 másodperc. A bemeneti események egyszerre történő elküldésekor várhatóan némi késésnek kell megjelennie, például:. A késések rosszabbodnak, amikor olyan eseményeket állít be, amelyek nagy értékre **érkeznek a késő** ablakba.

5. A **System. timestamp** érték eltér az **esemény időpontja** mezőben megadott időponttól.

   Az előzőekben leírtaknak megfelelően a rendszer a megrendelésen kívüli tolerancia vagy a késői beérkezés tűréshatára alapján állítja be az esemény időpontját. Az esemény **System. timestamp** értéke módosítva van, de nem az **esemény időpontja** mező.

## <a name="metrics-to-observe"></a>A figyelni kívánt mérőszámok

A [stream Analytics feladatok mérőszámai](stream-analytics-monitoring.md)segítségével megfigyelheti az események rendezésének időbeli tűréshatárait. A következő metrikák szükségesek:

|Metrika  | Leírás  |
|---------|---------|
| **Megrendelésen kívüli események** | Azt jelzi, hogy hány esemény érkezett el a megadott sorrendben, és amelyek el lettek dobva, vagy egy beállított időbélyeg lett megadva. Ezt a metrikát közvetlenül érinti a Azure Portalban lévő feladathoz tartozó **esemény** - **megrendelési események** beállításának beállítása. |
| **Késői bemeneti események** | Azt jelzi, hogy hány esemény érkezik a forrástól. Ez a metrika olyan eseményeket tartalmaz, amelyek el lettek dobva, vagy az időbélyege módosult. Ezt a metrikát közvetlenül érinti a Azure Portalban a feladathoz tartozó **esemény rendezési** lapján a **késői beállításban megjelenő események** konfigurációja. |
| **Korai bemeneti események** | Azt jelzi, hogy a forrástól korán érkező események száma eldobott állapotú-e, vagy az időbélyegük ki lett igazítva, ha a korábbi 5 percen túl van. |
| **Vízjel késleltetése** | A folyamatos átviteli adatfeldolgozási feladatok késleltetését jelzi. További információt a következő szakaszban talál.|

## <a name="watermark-delay-details"></a>Vízjel késleltetésének részletei

A **vízjel késleltetése** mérőszám a feldolgozási csomópont falióra-időpontjára van kiszámítva, mínusz az eddig látott legnagyobb vízjel. További információ: [vízjel-késleltetési blogbejegyzés](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Több oka is lehet annak, hogy a metrika értéke nagyobb nullánál a normál működésnél:

1. A folyamatos feldolgozási folyamat késése. Általában ez a késés a névleges érték.

2. A rendszer késlelteti a nem meghatározott sorrendbe állítási toleranciát, mivel a rendszer a tűréshatárt a tolerancia ablak méretével csökkenti.

3. A késői beérkezés ablaka késleltetve vezetett be, mert a küszöbértéket a tolerancia ablak mérete csökkenti.

4. A metrikát generáló feldolgozási csomópont órájának torzítása.

Számos más erőforrás-korlátozás is előfordulhat, amely miatt a folyamatos átviteli folyamat lelassulhat. A vízjel késleltetésének mérőszáma a következő okból emelkedhet:

1. Nincs elég erőforrás a Stream Analytics a bemeneti események mennyiségének kezeléséhez. Az erőforrások vertikális felskálázásával kapcsolatban lásd: a [folyamatos átviteli egységek ismertetése és módosítása](stream-analytics-streaming-unit-consumption.md).

2. Nincs elegendő átviteli sebesség a bemeneti esemény-átvitelszervezőn belül, ezért szabályozva vannak. A lehetséges megoldásokért lásd: az [Azure Event Hubs átviteli egységek automatikus vertikális felskálázása](../event-hubs/event-hubs-auto-inflate.md).

3. A kimeneti mosogatók nincs elegendő kapacitással kiépítve, ezért szabályozva vannak. A lehetséges megoldások széles körben eltérnek a használt kimeneti szolgáltatás íze alapján.

## <a name="output-event-frequency"></a>Kimeneti események gyakorisága

A Azure Stream Analytics a vízjel előrehaladását használja az egyetlen triggerként a kimeneti események előállításához. Mivel a vízjel bemeneti adatokból származik, a rendszer a sikertelen helyreállítás és a felhasználó által kezdeményezett újrafeldolgozás során ismételhető.

[Ablakos összesítések](stream-analytics-window-functions.md)használatakor a szolgáltatás csak a Windows végén hozza létre a kimeneteket. Bizonyos esetekben előfordulhat, hogy a felhasználók a Windowsból generált részleges összesítéseket szeretnének látni. A részleges összesítések jelenleg nem támogatottak Azure Stream Analyticsban.

Más streaming-megoldásokban a kimeneti események különböző kiváltó pontokon, a külső körülményektől függően lehetnek lényegesek. Bizonyos megoldások esetében előfordulhat, hogy egy adott időablak kimeneti eseményei többször is létrehozhatók. Ahogy a bemeneti értékek finomítva vannak, az összesített eredmények pontosabbak lesznek. Az eseményeket az első alkalommal lehet meggondolni, és az idő múlásával módosították. Ha például egy adott eszköz kapcsolat nélküli állapotban van a hálózatról, a rendszer egy becsült értéket is felhasználhat. A későbbiekben Ugyanez az eszköz csatlakozik a hálózatra. Ezután a tényleges esemény adatai szerepelhetnek a bemeneti adatfolyamban. Az időablak feldolgozásának kimenete pontosabb kimenetet eredményez.

## <a name="illustrated-example-of-watermarks"></a>Példa a vízjelekre

Az alábbi ábrán bemutatjuk, hogyan haladnak a vízjelek a különböző körülmények között.

Ez a táblázat az alábbi táblázatban szereplő példákat mutatja be. Figyelje meg, hogy az esemény időpontja és az érkezési idő eltérő, néha nem.

| Esemény időpontja | Érkezés időpontja | DeviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | device3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | device3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | device3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | device3

Ebben az ábrán a következő tűréshatárok használatosak:

- A korai érkezéssel rendelkező Windows 5 perc
- A későn érkező ablak 5 perc
- Az átrendezési ablak 2 perc

1. Az alábbi eseményeken átmenő vízjelek illusztrációja:

   ![Azure Stream Analytics vízjel ábrája](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Az előző ábrán látható jelentős folyamatok:

   1. Az első esemény (device1) és a második esemény (device2) igazított időpontot tartalmaz, és módosítás nélkül dolgozza fel őket. A vízjel az egyes eseményeken halad.

   2. A harmadik esemény (device1) feldolgozása után az érkezés időpontja (12:11) megelőzi az esemény időpontját (12:17). Az esemény 6 perccel korábbi, így az esemény az 5 perces korai érkezési tolerancia miatt eldobásra kerül.

      A vízjel nem halad előre egy korai esemény esetében.

   3. A negyedik esemény (device3) és az ötödik esemény (device1) igazított időpontot tartalmaz, és módosítás nélkül dolgozza fel őket. A vízjel az egyes eseményeken halad.

   4. A hatodik esemény (device3) feldolgozása után az érkezési idő (12:17) és az esemény időpontja (12:12) a vízjel szintje alá esik. Az esemény időpontja a vízjelek szintjére van beállítva (12:17).

   5. A tizenkettedik esemény (device3) feldolgozásakor az érkezés időpontja (12:27) az esemény időpontját követő 6 perc (12:21). A rendszer a késői érkezési szabályzatot alkalmazza. Az esemény időpontját helyesbíti (12:22), amely meghaladja a vízjelet (12:21), ezért nincs szükség további módosításra.

2. Második ábrán a vízjel a korai beérkezési szabályzat nélkül halad:

   ![Azure Stream Analytics nincs korai házirend-vízjel illusztráció](media/stream-analytics-time-handling/watermark-graph-2.png)

   Ebben a példában nem alkalmazunk korán megérkezési szabályzatot. A leghamarabb megjelenő kiugró események jelentősen megemelik a vízjelet. Figyelje meg, hogy a harmadik esemény (a deviceId1 időpontjában 12:11) nem kerül eldobásra ebben a forgatókönyvben, és a vízjel 12:15-re van kiemelve. Ennek eredményeképpen a negyedik esemény időpontját 7 percre (12:08 – 12:15) korrigáljuk.

3. Az utolsó ábrán az alstreamek használatosak (a DeviceId felett). Több vízjel is nyomon követhető, egy adatfolyamként. Ennek eredményeképpen kevesebb esemény lett kiigazítva.

   ![Azure Stream Analytics alstreamek vízjelének illusztrációja](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Következő lépések

- [Azure Stream Analytics az események sorrendjét érintő megfontolások](stream-analytics-out-of-order-and-late-events.md)
- [Stream Analytics feladatok metrikái](stream-analytics-monitoring.md)
