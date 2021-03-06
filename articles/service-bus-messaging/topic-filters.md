---
title: Azure Service Bus témakör szűrők | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan határozhatja meg, hogy az előfizetők hogyan határozhatják meg, hogy mely üzeneteket szeretnék a témakörből a szűrők megadásával.
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2020
ms.author: spelluru
ms.openlocfilehash: b8ffbb16763bfe6485ebf2ab770f4537ddbc8569
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774497"
---
# <a name="topic-filters-and-actions"></a>Témakörszűrők és -műveletek

Az előfizetők meghatározhatják, hogy mely üzeneteket szeretnék megkapni egy témakörön belül. Ezek az üzenetek egy vagy több elnevezett előfizetési szabály formájában vannak megadva. Minden szabály egy olyan feltételből áll, amely kijelöl egy adott üzenetet, valamint egy műveletet, amely a kijelölt üzenet megjegyzéseit tartalmazza. Az előfizetés megfelelő szabályfeltételenként létrehoz egy üzenetmásolatot, amelyek azután megfelelő szabályonként más és más jelöléssel láthatók el.

Minden újonnan létrehozott témakör-előfizetés kezdeti alapértelmezett előfizetési szabállyal rendelkezik. Ha nem ad meg explicit módon egy szűrési feltételt a szabályhoz, az alkalmazott szűrő a **valódi** szűrő, amely lehetővé teszi az összes üzenet kijelölését az előfizetésben. Az alapértelmezett szabályhoz nem tartozik társított megjegyzési művelet.

A Service Bus három szűrési feltételt támogat:

-   *Logikai szűrők* – a **TrueFilter** és a **FalseFilter** az összes érkező üzenetet (**igaz**) vagy egyetlen nem az elküldött üzenetet (**false**) sem okozhatja az előfizetés kiválasztásához.

-   *SQL-szűrők* – a **SqlFilter** egy SQL-szerű feltételes kifejezést tart fenn, amelyet a rendszer az elküldött üzenetek felhasználó által definiált tulajdonságai és a Rendszertulajdonságok alapján értékel ki a közvetítőn. Az összes rendszertulajdonságot előtaggal kell ellátni `sys.` a feltételes kifejezésben. A [szűrési feltételekhez tartozó SQL-Language részhalmaz](service-bus-messaging-sql-filter.md) a tulajdonságok (`EXISTS`) létezésére, valamint a null értékekre (`IS NULL`), a logikai nem/és/vagy, a rokon operátorokra, az egyszerű numerikus aritmetikara és az egyszerű szöveges mintázatra vonatkozó, `LIKE`.

-   *Korrelációs szűrők* – a **CorrelationFilter** olyan feltételekkel rendelkezik, amelyek egy vagy több érkező üzenet felhasználó-és rendszertulajdonságával egyeznek. Gyakori használat a **correlationId** tulajdonsággal való egyezés, de az alkalmazás dönthet úgy is, hogy a **ContentType**, a **label**, a **MessageID**, a **ReplyTo**, a **ReplyToSessionId**, **a** **munkamenet**-azonosító, a és a felhasználó által definiált tulajdonságokat is egyeztetni tudja. Ha egy tulajdonsághoz tartozó üzenet értéke megegyezik a korrelációs szűrőben megadott értékkel, akkor egyezés áll fenn. Karakterlánc-kifejezések esetében az összehasonlítás a kis-és nagybetűk megkülönböztetése. Több egyezési tulajdonság megadásakor a szűrő logikai és feltételként kombinálja őket, ami azt jelenti, hogy a szűrőnek egyeznie kell, minden feltételnek egyeznie kell.

Az összes szűrő kiértékeli az üzenet tulajdonságait. A szűrők nem tudják kiértékelni az üzenet törzsét.

Az összetett szűrési szabályok feldolgozási kapacitást igényelnek. Az SQL-szűrési szabályok használata különösen a teljes üzenet átviteli sebességét eredményezi az előfizetés, a témakör és a névtér szintjén. Amikor csak lehetséges, az alkalmazásoknak olyan korrelációs szűrőket kell választaniuk, mint az SQL-szerű szűrők, mivel ezek sokkal hatékonyabbá válnak a feldolgozás során, ezért kevesebb hatással vannak az átviteli sebességre.

## <a name="actions"></a>Műveletek

SQL-szűrési feltételekkel olyan műveletet határozhat meg, amely a tulajdonságok és azok értékeinek hozzáadásával, eltávolításával vagy cseréjével képes jegyzeteket adni az üzenethez. A művelet [egy olyan SQL-szerű kifejezést használ](service-bus-messaging-sql-filter.md) , amely lazán támaszkodik az SQL Update utasítás szintaxisára. A műveletet a rendszer az üzenet egyeztetése után hajtja végre, mielőtt az üzenet be lett jelölve az előfizetésben. Az üzenet tulajdonságainak módosításai magánjellegűek az előfizetésbe másolt üzenetben.

## <a name="usage-patterns"></a>Használati minták

Egy témakör legegyszerűbb használati forgatókönyve, hogy minden előfizetés lekéri a témakörbe küldött összes üzenet másolatát, amely lehetővé teszi a szórási mintát.

A szűrők és a műveletek lehetővé teszik a minták két további csoportjának használatát: particionálás és útválasztás.

A particionálás szűrők használatával továbbítja az üzeneteket számos meglévő témakör-előfizetésben kiszámítható és kölcsönösen kizárható módon. A particionálási mintát akkor kell használni, ha egy rendszer méretezése úgy történik, hogy számos különböző kontextust kezel a funkcionálisan azonos rekeszekben, amelyek mindegyike a teljes adat egy részhalmazát fogja tárolni. például az ügyfél profiljának adatai. A particionálással a közzétevő a particionálási modell ismerete nélkül küldi el az üzenetet egy témakörbe. Ekkor a rendszer áthelyezi az üzenetet a megfelelő előfizetésre, ahonnan a partíció üzenetkezelője lekérheti azt.

Az Útválasztás szűrőket használ az üzenetek különböző témakör-előfizetések közötti elosztására kiszámítható módon, de nem feltétlenül kizárólagos. Az [automatikus továbbítási](service-bus-auto-forwarding.md) szolgáltatással együtt a témakör szűrők segítségével összetett útválasztási diagramokat hozhat létre egy Service Bus névtérben az Azure-régión belüli üzenetek terjesztéséhez. Ha Azure Functions vagy Azure Logic Apps Azure Service Bus névterek közötti híddal, összetett globális topológiákat hozhat létre, amelyek közvetlen integrációt végeznek az üzletági alkalmazásokkal.

## <a name="next-steps"></a>Következő lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [SQLFilter szintaxis](service-bus-messaging-sql-filter.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)