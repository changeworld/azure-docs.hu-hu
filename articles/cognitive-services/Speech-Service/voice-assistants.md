---
title: Hangsegédek – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A hangsegédek funkcióinak, képességeinek és korlátozásainak áttekintése a Speech szoftverfejlesztői készlet (SDK) használatával.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79369896"
---
# <a name="what-is-a-voice-assistant"></a>Mi az a hangvezérelt asszisztens?

A beszédfelismerési szolgáltatást használó hangsegédek lehetővé teszik a fejlesztők számára, hogy természetes, emberi jellegű társalgási felületet hozzanak létre alkalmazásaikban és tapasztalataikban.

A hangsegéd szolgáltatás gyors, megbízható interakciót tesz lehetővé egy eszköz és egy asszisztens implementációja között, amely vagy (1) a bot Framework közvetlen vonalú beszédfelismerési csatornáját, vagy (2) az integrált egyéni parancsok (előzetes verzió) szolgáltatást használja a feladatok befejezéséhez.

Az alkalmazások a beszédfelismerési szoftverfejlesztői készlettel (SDK) csatlakoznak a Voice Assistant szolgáltatáshoz.

   ![A hangsegéd-előkészítési szolgáltatás folyamatának fogalmi diagramja](media/voice-assistants/overview.png "A hangsegéd folyamata")

## <a name="choosing-an-assistant-solution"></a>Asszisztensi megoldás kiválasztása

A hangsegéd létrehozásának első lépése, hogy eldöntse, mit kell tennie. A beszédfelismerési szolgáltatás több, egymást kiegészítő megoldást biztosít a Segéd interakcióinak kialakításához. Legyen szó akár a rugalmasságról és a sokoldalúságról, amit a bot Framework [közvetlen vonalas beszédének](direct-line-speech.md) csatornája biztosít, vagy az egyszerű forgatókönyvekhez [(előzetes verzió)](custom-commands.md) egyszerűen, a megfelelő eszközök kiválasztásával kezdheti meg az első lépéseket.

| Ha azt szeretné… | Ezt követően tekintse meg... | Például... |
|-------------------|------------------|----------------|
|Nyílt végű beszélgetés robusztus szakértelem-integrációval és teljes körű üzembe helyezési ellenőrzéssel | A bot Framework [közvetlen vonalas beszédének](direct-line-speech.md) csatornája | <ul><li>"Be kell menni a Seattle-be"</li><li>"Milyen típusú pizzát rendelhetek?"</li></ul>
|Parancs-és vezérlési vagy feladat-orientált beszélgetés egyszerűsített létrehozással és üzemeltetéssel | [Egyéni parancsok (előzetes verzió)](custom-commands.md) | <ul><li>"Az általános megvilágítás bekapcsolása"</li><li>"5 fokos meleg"</ul>

Javasoljuk, hogy a [közvetlen vonalas beszédfelismerést](direct-line-speech.md) a legjobb alapértelmezett beállításként válassza, ha még nem biztos benne, hogy mit szeretne kezelni. Integrálja az eszközöket és a szerzői támogatást, például a [virtuális asszisztensi megoldást és a vállalati sablont](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) , valamint a [QnA Maker szolgáltatást](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) , hogy közös mintákon építsen, és a meglévő tudásbázisokat használja.

Az [egyéni parancsok (előzetes verzió)](custom-commands.md) egy egyszerűsített szerzői és üzemeltetési élményt biztosítanak, amelyet kifejezetten a természetes nyelvi parancsok és a vezérlési forgatókönyvek számára alakítottak ki.

   ![Asszisztensi megoldások összehasonlítása](media/voice-assistants/assistant-solution-comparison.png "Asszisztensi megoldások összehasonlítása")

## <a name="core-features"></a>Alapvető funkciók

Akár [közvetlen vonalas beszédet](direct-line-speech.md) , akár [egyéni parancsokat (előzetes verzió)](custom-commands.md) választ a Segéd-interakciók létrehozásához, a testreszabási funkciók széles választékával testre szabhatja a Segédet a márka, a termék és a személyiség számára.

| Kategória | Szolgáltatások |
|----------|----------|
|[Egyéni kulcsszó](speech-devices-sdk-create-kws.md) | A felhasználók olyan egyéni kulcsszóval indíthatnak el beszélgetéseket, mint például a "Hey contoso". Az alkalmazás a Speech SDK-ban egy egyéni kulcsszavas motorral rendelkezik, amely konfigurálható egy egyéni kulcsszóval, [amelyet itt is](speech-devices-sdk-create-kws.md)létrehozhat. A hangsegédek kiszolgálóoldali ellenőrzés használatával növelhetik a kulcsszó aktiválásának pontosságát (az eszköz helyett).
|[Beszéd szövege](speech-to-text.md) | A hangsegédek valós idejű hanganyagot alakítanak át felismert szöveggé a beszédfelismerési szolgáltatásból származó [beszéd – szöveg](speech-to-text.md) használatával. Ez a szöveg a Segéd implementációjában és az ügyfélalkalmazásban is elérhető.
|[Szöveg – beszéd](text-to-speech.md) | A Segédtől érkező szöveges válaszokat a beszédfelismerési szolgáltatás [szövegről beszédre](text-to-speech.md) történő felhasználásával szintetizálja. Ezt a szintézist ezután az ügyfélalkalmazás elérhetővé válik egy hangadatfolyamként. A Microsoft lehetőséget nyújt saját, kiváló minőségű neurális TTS-hang létrehozására, amely hangvételt biztosít a márka számára. További információért [vegye fel velünk a kapcsolatot](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>Első lépések a hangsegédek használatába

Olyan gyors útmutatókat is kínálunk, amelyek kevesebb, mint 10 perc alatt futtatják a kódot. Ez a táblázat a hangsegéd-gyors üzembe helyezések listáját tartalmazza, nyelv szerint rendezve.

| Első lépések | Platform | API-referencia |
|------------|----------|---------------|
| C#, UWP | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Tallózás](https://aka.ms/csspeech/javaref) |
| Java | Android | [Tallózás](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Mintakód

Hangsegéd létrehozásához használható mintakód a GitHubon érhető el. Ezek a minták lefedik az ügyfélalkalmazás számára a Segédhez való csatlakozást számos népszerű programozási nyelven.

* [Hangsegéd-minták (SDK)](https://aka.ms/csspeech/samples)
* [Oktatóanyag: hang engedélyezése a Segéd számára a Speech SDK-valC#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Oktatóanyag

Egy oktatóanyag, amely bemutatja, hogyan [engedélyezheti a Segédet a SPEECH SDK és a Direct line Speech Channel használatával](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Testreszabás

A beszédfelismerési szolgáltatással létrehozott hangsegédek teljes körű testreszabási lehetőségeket használhatnak a [beszéd – szöveg](speech-to-text.md), a [szöveg-beszéd](text-to-speech.md)és az [Egyéni kulcsszavak kiválasztásához](speech-devices-sdk-create-kws.md).

> [!NOTE]
> A testreszabási lehetőségek nyelv/területi beállítás szerint változnak (lásd a [támogatott nyelveket](supported-languages.md)).

## <a name="reference-docs"></a>Dokumentációs dokumentumok

* [Beszéd SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Következő lépések

* [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](get-started.md)
* [A Speech SDK beszerzése](speech-sdk.md)
* [További információ az egyéni parancsokról (előzetes verzió)](custom-commands.md)
* [További információ a közvetlen vonalas beszédről](direct-line-speech.md)
