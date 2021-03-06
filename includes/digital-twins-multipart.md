---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
ms.custom: include file
ms.openlocfilehash: 0e7cb7e4aaa9862a2b4af51593c29793ea54dd14
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2020
ms.locfileid: "77111246"
---
> [!NOTE]
> A többrészes kérelmek általában három darabot igényelnek:
> * Egy **Content-Type** fejléc:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Egy **Content-hajlam**:
>   * `form-data; name="metadata"`
> * A feltölteni kívánt fájl tartalma
>
> A **tartalom típusa** és a **tartalomra való hajlam** a használati forgatókönyvtől függően változhat.

A többrészes kérelmek programozott módon (átmenő C#), Rest-ügyféllel vagy más eszközzel (például [Poster](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request)) hozhatók létre. A REST-ügyfél eszközei eltérő szintű támogatást nyújthatnak az összetett többrészes kérelmekhez. A konfigurációs beállítások az eszköztől az eszköztől némileg eltérőek lehetnek. Ellenőrizze, hogy melyik eszköz megfelel az igényeinek.

> [!IMPORTANT]
> Az Azure digitális Twins felügyeleti API-khoz küldött többrészes kérelmek általában két részből állnak:
> * A blob metaadatainak (például a hozzá tartozó MIME-típus), amelyet a **tartalom típusa** és/vagy a **tartalom-elrendezés** deklarál
> * A feltölteni kívánt fájl strukturálatlan tartalmát tartalmazó blob tartalma
>
> A **javítási** kérelmek esetében a két rész egyikének sem kell lennie. Mindkettő szükséges a **post** vagy a Create műveletekhez.

A foglaltság gyors üzembe helyezési [forráskódja](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) olyan teljes C# példákat tartalmaz, amelyek bemutatják, hogyan lehet többrészes kérelmeket készíteni az Azure digitális Twins felügyeleti API-