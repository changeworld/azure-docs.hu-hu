---
title: Hibaelhárítási útmutató – Azure Event Hubs | Microsoft Docs
description: Ez a cikk az Azure Event Hubs üzenetkezelési kivételek és a javasolt műveletek listáját tartalmazza.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: de5b95bd10bf72f60ba5d63c4b3a799556fcce33
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76309778"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Az Azure Event Hubs hibaelhárítási útmutatója
Ez a cikk a Event Hubs .NET-keretrendszer API-jai által generált .NET-kivételeket, valamint a hibaelhárítással kapcsolatos egyéb tippeket tartalmaz. 

## <a name="event-hubs-messaging-exceptions---net"></a>Üzenetkezelési kivételek Event Hubs – .NET
Ez a szakasz a .NET-keretrendszer API-jai által generált .NET-kivételeket sorolja fel. 

### <a name="exception-categories"></a>Kivételek kategóriái

A Event Hubs .NET API-k olyan kivételeket hoznak elő, amelyek a következő kategóriákba sorolhatók, valamint a hozzájuk tartozó műveletekkel.

1. Felhasználói kódolási hiba: [System. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System. OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System. Runtime. szerializálás. SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Általános művelet: a folytatás előtt próbálja meg kijavítani a kódot.
2. Telepítési/konfigurációs hiba: [Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft. Azure. EventHubs. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Általános művelet: Ellenőrizze a konfigurációt, és szükség esetén módosítsa.
3. Átmeneti kivételek: [Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. ServerBusyException](#serverbusyexception), [Microsoft. Azure. EventHubs. ServerBusyException](#serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Általános művelet: Próbálja megismételni a műveletet, vagy értesítse a felhasználókat.
4. Egyéb kivételek: [System. Transactions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System. timeoutexception osztályról](#timeoutexception), [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Általános művelet: a kivétel típusára jellemző; Tekintse át a következő szakaszban található táblázatot. 

### <a name="exception-types"></a>Kivételek típusai
Az alábbi táblázat az üzenetkezelési kivételek típusait, valamint azok okait és megjegyzéseit sorolja fel.

| Kivétel típusa | Leírás/ok/példák | Javasolt művelet | Megjegyzés automatikus/azonnali újrapróbálkozás |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [Timeoutexception osztályról](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |A kiszolgáló a megadott időn belül nem válaszolt a kért műveletre, amelyet a [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings)vezérel. Lehet, hogy a kiszolgáló végrehajtotta a kért műveletet. Ez a kivétel hálózati vagy más infrastrukturális késések miatt fordulhat elő. |Ellenőrizze a rendszerállapotot a konzisztencia érdekében, és szükség esetén próbálkozzon újra.<br /> Lásd: [timeoutexception osztályról](#timeoutexception). | Előfordulhat, hogy az Újrapróbálkozás bizonyos esetekben segíthet. adja hozzá az újrapróbálkozási logikát a kódhoz. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A kért felhasználói művelet nem engedélyezett a kiszolgálón vagy a szolgáltatáson belül. A részletekért tekintse meg a kivételt jelző üzenetet. A [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) például ezt a kivételt hozza létre, ha az üzenet [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) módban érkezett. | Keresse meg a kódot és a dokumentációt. Győződjön meg arról, hogy a kért művelet érvényes. | Az újrapróbálkozás nem segít. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Kísérlet történt egy olyan objektum műveletének meghívására, amely már be van zárva, megszakadt vagy el lett távolítva. Ritka esetekben a környezeti tranzakció már el van távolítva. | Ellenőrizze a kódot, és győződjön meg róla, hogy nem hív meg műveleteket egy eldobott objektumon. | Az újrapróbálkozás nem segít. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | A [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objektum nem tudott jogkivonatot beszerezni, a jogkivonat érvénytelen, vagy a jogkivonat nem tartalmazza a művelet végrehajtásához szükséges jogcímeket. | Győződjön meg arról, hogy a jogkivonat-szolgáltató a megfelelő értékekkel lett létrehozva. Vizsgálja meg a Access Control Service konfigurációját. | Előfordulhat, hogy az Újrapróbálkozás bizonyos esetekben segíthet. adja hozzá az újrapróbálkozási logikát a kódhoz. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | A metódushoz megadott egy vagy több argumentum érvénytelen. A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy [létrehozáshoz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) megadott URI-azonosító szegmens (eke) t tartalmaz. A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) vagy- [létrehozáshoz](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) megadott URI-séma érvénytelen. A tulajdonság értéke nagyobb, mint 32 KB. | Ellenőrizze a hívó kódját, és ellenőrizze, hogy helyesek-e az argumentumok. | Az újrapróbálkozás nem segít. |
| [Microsoft. ServiceBus. Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft. Azure. EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | A művelethez társított entitás nem létezik, vagy törölték. | Győződjön meg arról, hogy az entitás létezik. | Az újrapróbálkozás nem segít. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Az ügyfél nem tud kapcsolatot létesíteni az Event hub szolgáltatással. |Győződjön meg arról, hogy a megadott állomásnév helyes, és a gazdagép elérhető. | Az újrapróbálkozás akkor lehet hasznos, ha akadozó kapcsolódási problémák léptek fel. |
| [Microsoft. ServiceBus. Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft. Azure. EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | A szolgáltatás jelenleg nem tudja feldolgozni a kérelmet. | Az ügyfél hosszabb ideig is megvárhat, majd próbálja megismételni a műveletet. <br /> Lásd: [ServerBusyException](#serverbusyexception). | Előfordulhat, hogy az ügyfél bizonyos intervallum után újra próbálkozik. Ha az újrapróbálkozások eltérő kivételt eredményeznek, akkor ellenőrizze, hogy az újrapróbálkozási viselkedést az adott kivétel okozza. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Általános üzenetküldési kivétel, amely a következő esetekben fordulhat elő: kísérlet történt egy [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) létrehozására egy másik típusú entitáshoz tartozó névvel vagy elérési úttal (például egy témakör). 1 MB-nál nagyobb üzenet küldésére történt kísérlet. A kiszolgáló vagy szolgáltatás hibát észlelt a kérelem feldolgozása során. A részletekért tekintse meg a kivételt jelző üzenetet. Ez a kivétel általában átmeneti kivétel. | Ellenőrizze a kódot, és győződjön meg arról, hogy csak szerializálható objektumok használatosak az üzenet törzséhez (vagy használjon egyéni szerializáló). Keresse meg a tulajdonságok támogatott értékeit, és csak a támogatott típusokat használja. Keresse meg a [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) tulajdonságot. Ha az értéke **igaz**, megismételheti a műveletet. | Az újrapróbálkozási viselkedés nincs meghatározva, és lehet, hogy nem segít. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Kísérlet történt olyan entitás létrehozására, amelynek a neve már használja egy másik entitás által az adott szolgáltatási névtérben. | Törölje a meglévő entitást, vagy válasszon másik nevet a létrehozandó entitás számára. | Az újrapróbálkozás nem segít. |
| [Quotaexceededexception osztályról](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Az üzenetküldési entitás elérte a maximálisan engedélyezett méretet. Ez a kivétel akkor fordulhat elő, ha a fogadók maximális száma (amely 5) már meg van nyitva felhasználónkénti csoport szintjén. | Hozzon létre helyet az entitásban az entitásból vagy annak alvárólistából érkező üzenetek fogadásával. <br /> Lásd: [quotaexceededexception osztályról](#quotaexceededexception) | Az újrapróbálkozás akkor lehet hasznos, ha az üzenetek időközben el lettek távolítva. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Futásidejű művelet kérése letiltott entitáson. |Aktiválja az entitást. | Az újrapróbálkozás segíthet abban az esetben, ha az entitást ideiglenesen aktiválták. |
| [Microsoft. ServiceBus. Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft. Azure. EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Az üzenet tartalma meghaladja az 1 MB-os korlátot. Ez az 1 MB-os korlát az összes üzenet, amely tartalmazhatja a rendszer tulajdonságait és a .NET-terhelést. | Csökkentse az üzenet adattartalmát, majd próbálja megismételni a műveletet. |Az újrapróbálkozás nem segít. |

### <a name="quotaexceededexception"></a>Quotaexceededexception osztályról
A [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) kivétel azt jelzi, hogy túllépte az adott entitáshoz tartozó kvótát.

Ez a kivétel akkor fordulhat elő, ha a fogadók maximális száma (5) már meg van nyitva felhasználónkénti csoport szintjén.

#### <a name="event-hubs"></a>Azure Event Hubs-eseményközpontok
Az Event Hubs legfeljebb 20 fogyasztói csoportot tartalmaz. Ha további kísérletet szeretne létrehozni, [quotaexceededexception osztályról](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception)kap. 

### <a name="timeoutexception"></a>Timeoutexception osztályról
A [timeoutexception osztályról](https://msdn.microsoft.com/library/system.timeoutexception.aspx) azt jelzi, hogy a felhasználó által kezdeményezett művelet a művelet időkorlátja alatt hosszabb időt vesz igénybe. 

Event Hubs esetében az időtúllépés a kapcsolati sztring részeként vagy a [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder)-n keresztül van megadva. Maga a hibaüzenet is változhat, de mindig az aktuális művelethez megadott időtúllépési értéket tartalmazza. 

#### <a name="common-causes"></a>Gyakori okok
Ennek a hibának két gyakori oka van: helytelen konfiguráció vagy átmeneti szolgáltatási hiba.

1. **Helytelen konfiguráció** Lehet, hogy a művelet időtúllépése túl kicsi a működési feltételnél. Az ügyfél-SDK-ban a művelet időtúllépésének alapértelmezett értéke 60 másodperc. Ellenőrizze, hogy a kód értéke túl kicsi-e. A hálózat és a CPU-használat feltétele befolyásolhatja az adott művelet befejezéséhez szükséges időt, így a művelet időtúllépése nem állítható be kis értékre.
2. **Átmeneti szolgáltatás hibája** Időnként előfordulhat, hogy a Event Hubs szolgáltatás késéseket tapasztal a feldolgozási kérelmekben; például a nagy forgalmú időszakokban. Ilyen esetekben egy késleltetés után újra megpróbálkozhat a művelettel, amíg a művelet nem sikerült. Ha ugyanez a művelet több kísérlet után is meghiúsul, látogasson el az [Azure szolgáltatás állapota webhelyre](https://azure.microsoft.com/status/) , és ellenőrizze, hogy vannak-e ismert szolgáltatások.

### <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft. ServiceBus. Messaging. ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) vagy A [Microsoft. Azure. EventHubs. ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) azt jelzi, hogy egy kiszolgáló túlterhelt. Ehhez a kivételhez két megfelelő hibakód van.

#### <a name="error-code-50002"></a>50002-es hibakód

Ez a hiba két okból fordulhat elő:

1. A terhelés nem egyenletesen oszlik el az Event hub összes partícióján, és egy partíció a helyi átviteli egységre vonatkozó korlátozást eredményez.
    
    Megoldás: a partíció-terjesztési stratégia felülvizsgálata vagy a [EventHubClient. Send (eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) kipróbálása segíthet.

2. A Event Hubs névtérnek nincs elegendő átviteli egysége (a [Azure Portal](https://portal.azure.com) Event Hubs névtér ablakában ellenőrizheti a **mérőszámok** képernyőt a megerősítéshez). A portál összesített (1 perces) adatokat jelenít meg, de az átviteli sebességet valós időben mérjük, így csak becslést lehet készíteni.

    Megoldás: a névtérben található átviteli egységek növelése segíthet. Ezt a műveletet a portálon, a Event Hubs névtér **méretezési** ablakában végezheti el. Vagy használhatja az [automatikus kinyílást](event-hubs-auto-inflate.md)is.

#### <a name="error-code-50001"></a>50001-es hibakód

Ennek a hibának ritkán kell történnie. Ez akkor történik meg, amikor a névtér kódját futtató tárolója alacsony a CPU-ban – nem több, mint néhány másodperc, mielőtt megkezdődik a Event Hubs Load Balancer.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>A GetRuntimeInformation metódus hívásának korlátozása
Az Azure Event Hubs másodpercenként legfeljebb 50 hívást támogat a GetRuntimeInfo másodpercenként. A korlát elérésekor a következőhöz hasonló kivétel jelenhet meg:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Kapcsolati, tanúsítvány-vagy időtúllépési problémák
A következő lépések segítséget nyújthatnak a kapcsolat/tanúsítvány/időtúllépési problémák hibaelhárításához a *. servicebus.windows.net alatti összes szolgáltatáshoz. 

- Tallózással keresse meg vagy a [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/`. Segít ellenőrizni, hogy rendelkezik-e IP-szűréssel, illetve virtuális hálózati vagy tanúsítványlánc-problémákkal (a Java SDK használatakor leggyakrabban).

    Példa a sikeres üzenetre:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Egy példa a hiba hibaüzenetére:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- A következő parancs futtatásával ellenőrizze, hogy a tűzfal blokkolja-e a portokat. A használt portok a következők: 443 (HTTPS), 5671 (AMQP) és 9093 (Kafka). A használt könyvtártól függően más portok is használatban vannak. Itt látható a minta parancs, amely azt vizsgálja, hogy a 5671-es port blokkolva van-e.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Linux rendszeren:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Időnkénti kapcsolódási problémák esetén futtassa az alábbi parancsot, és ellenőrizze, hogy vannak-e eldobott csomagok. Ezzel a paranccsal a szolgáltatással 1 másodpercenként 25 különböző TCP-kapcsolatot kell létrehozni. Ezt követően megtekintheti, hogy a sikeres és sikertelen volt-e a TCP-kapcsolatok késése. Az `psping` eszközt [innen](/sysinternals/downloads/psping)töltheti le.

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Ha más eszközöket (például `tnc`, `ping`stb.) használ, egyenértékű parancsokat is használhat. 
- Szerezze be a hálózati nyomkövetést, ha az előző lépések nem segítenek és nem elemzik olyan eszközökkel, mint például a [Wireshark](https://www.wireshark.org/). Ha szükséges, forduljon a [Microsoft ügyfélszolgálatahoz](https://support.microsoft.com/) . 

## <a name="next-steps"></a>Következő lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hub létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
