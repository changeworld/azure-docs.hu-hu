---
title: Frissítés a Azure Search .NET SDK 5-ös verziójára
titleSuffix: Azure Cognitive Search
description: Telepítse át a kódot a Azure Search .NET SDK 5-ös verziójának régebbi verzióiból. Ismerje meg, hogy mi az új, és milyen kód módosítása szükséges.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bb0cd191ba7e5939c55d11b484ed7a2c422f8c6d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793025"
---
# <a name="upgrade-to-azure-search-net-sdk-version-5"></a>Frissítés a Azure Search .NET SDK 5-ös verziójára

Ha a [Azure Search .net SDK](https://aka.ms/search-sdk)-hoz készült 4,0-es vagy régebbi verziót használja, ez a cikk segítséget nyújt az alkalmazás 5-ös verzióra való frissítéséhez.

Az SDK-val kapcsolatos általános áttekintést a példákat lásd: [Azure Search használata .NET-alkalmazásokból](search-howto-dotnet-sdk.md).

A Azure Search .NET SDK 5-ös verziója néhány változást tartalmaz a korábbi verziókból. Ezek többnyire kisebbek, ezért a kód módosítása csak minimális erőfeszítést igényelhet. Az új SDK-verzió használatára vonatkozó utasításokért lásd: a [verziófrissítés lépései](#UpgradeSteps) .

> [!NOTE]
> Ha a 2,0-es vagy régebbi verziót használja, először frissítsen a 3. verzióra, majd frissítsen az 5-ös verzióra. Útmutatásért lásd: [a Azure Search .net SDK 3-as verziójára való frissítés](search-dotnet-sdk-migration.md) .
>
> Az Azure Search Service-példány számos REST API verziót támogat, beleértve a legújabbat is. Továbbra is használhatja a verziót, ha már nem a legújabb, de javasoljuk, hogy a legújabb verzió használatára telepítse át a kódot. A REST API használatakor az API-verziót minden kérelemben meg kell adnia az API-Version paraméter használatával. A .NET SDK használatakor a használt SDK verziója meghatározza a REST API megfelelő verzióját. Ha régebbi SDK-t használ, továbbra is futtathatja ezt a kódot, még akkor sem, ha a szolgáltatás frissítve van egy újabb API-verzió támogatására.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Az 5-ös verzió újdonságai
A Azure Search .NET SDK 5-ös verziója a Azure Search REST API legújabb általánosan elérhető verzióját célozza meg, pontosabban 2017-11-11. Ez lehetővé teszi a Azure Search új funkcióinak használatát egy .NET-alkalmazásból, beleértve a következőket:

* [Szinonimák](search-synonyms.md).
* Mostantól programozott módon férhet hozzá a figyelmeztetésekhez az indexelő végrehajtási előzményeiben (további részletekért tekintse meg a [.net-hivatkozás](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) `IndexerExecutionResult` `Warning` tulajdonságát.
* A .NET Core 2 támogatása.
* Az új csomag szerkezete csak az SDK által igényelt részek használatát támogatja (részletekért lásd az [5. verzióban](#ListOfChanges) megjelenő változásokat).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései
Először frissítse a `Microsoft.Azure.Search` NuGet-hivatkozását a NuGet csomagkezelő konzol használatával, vagy kattintson a jobb gombbal a projekt hivatkozásaira, és válassza a "NuGet-csomagok kezelése..." lehetőséget. a Visual Studióban.

Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet. A kód szerkezetének módjától függően előfordulhat, hogy az Újraépítés sikeresen megtörtént. Ha igen, készen állsz!

Ha a Build sikertelen, a következőhöz hasonló fordítási hibaüzenetnek kell megjelennie:

    The name 'SuggesterSearchMode' does not exist in the current context

A következő lépés a Build-hiba kijavítása. A hiba okaival és megoldásával kapcsolatos további információkért lásd: az [5-ös verzióban](#ListOfChanges) megjelenő változások.

Vegye figyelembe, hogy a Azure Search .NET SDK csomagolásának változásai miatt az 5-ös verzió használatához újra kell építenie az alkalmazást. Ezek a változások részletesen szerepelnek az [5. verzióban történt változások](#ListOfChanges)során.

Előfordulhat, hogy az elavult metódusokkal vagy tulajdonságokkal kapcsolatos további felépítési figyelmeztetések jelennek meg. A figyelmeztetések tartalmazzák az elavult funkció helyett a használatra vonatkozó utasításokat is. Ha például az alkalmazás a `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` metódust használja, akkor a következő üzenet jelenik meg: "Ez a viselkedés mostantól alapértelmezés szerint engedélyezve van, ezért a metódus meghívása már nem szükséges."

A felépítési hibák vagy figyelmeztetések kijavítása után módosíthatja az alkalmazást, hogy igénybe vehesse az új funkciókat. Az SDK új funkciói részletesen ismertetik az [5-ös verzió újdonságait](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Az 5. verzióban feltört változások

### <a name="new-package-structure"></a>Új csomag szerkezete

Az 5-ös verzió legjelentősebb változása, hogy a `Microsoft.Azure.Search` szerelvény és annak tartalma négy különálló szerelvényre oszlik, amelyek most négy külön NuGet-csomagként vannak elosztva:

 - `Microsoft.Azure.Search`: ez egy olyan meta-csomag, amely az összes többi Azure Search-csomagot függőségként tartalmazza. Ha az SDK egy korábbi verziójáról frissít, egyszerűen frissítse ezt a csomagot, és az újbóli kiépítésnek elegendőnek kell lennie az új verzió használatának megkezdéséhez.
 - `Microsoft.Azure.Search.Data`: akkor használja ezt a csomagot, ha Azure Search használatával fejleszt egy .NET-alkalmazást, és csak az indexekben lévő dokumentumokat kell lekérdezni vagy frissítenie. Ha indexeket, szinonimákat vagy más szolgáltatási szintű erőforrásokat is létre kell hoznia vagy frissítenie, használja helyette a `Microsoft.Azure.Search` csomagot.
 - `Microsoft.Azure.Search.Service`: akkor használja ezt a csomagot, ha a .NET-ben fejleszti az automatizálást a Azure Search indexek, a szinonimák, az indexelő, az adatforrások vagy más szolgáltatási szintű erőforrások kezeléséhez. Ha csak az indexekben lévő dokumentumokat kell lekérdezni vagy frissítenie, használja helyette a `Microsoft.Azure.Search.Data` csomagot. Ha Azure Search összes funkciója szükséges, használja helyette a `Microsoft.Azure.Search` csomagot.
 - `Microsoft.Azure.Search.Common`: a Azure Search .NET-kódtárak által igényelt általános típusok. Ezt a csomagot nem szükséges közvetlenül az alkalmazásában használni; Csak függőségként használható.
 
Ez a változás technikailag feltört, mivel számos típust helyeztek át a szerelvények között. Ezért szükséges az alkalmazás újjáépítése az SDK 5-ös verziójára való frissítéshez.

Az 5-ös verzióban kis mennyiségű egyéb, az alkalmazás újraépítése melletti módosításra is szükség lehet.

### <a name="change-to-suggesters"></a>Váltás a javaslatokra 

A `Suggester` konstruktor már nem rendelkezik `SuggesterSearchMode``enum` paraméterrel. Ez a felsorolás csak egy értéket tartalmazott, ezért redundáns volt. Ha a felépítési hibákat látja, egyszerűen távolítsa el a `SuggesterSearchMode` paraméterre mutató hivatkozásokat.

### <a name="removed-obsolete-members"></a>Elavult tagok eltávolítva

Előfordulhat, hogy a korábbi verziókban elavultként megjelölt metódusokkal vagy tulajdonságokkal kapcsolatos fordítási hibákat talál, amelyeket később az 5. verzióban eltávolítottak. Ha ilyen hibák merülnek fel, az alábbi módon oldható meg:

- Ha a `IndexingParametersExtensions.IndexStorageMetadataOnly` metódust használta, használja a `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` helyet.
- Ha a `IndexingParametersExtensions.SkipContent` metódust használta, használja a `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` helyet.

### <a name="removed-preview-features"></a>Előzetes verziójú funkciók eltávolítva

Ha a 4,0-es verzióról frissít az 5. verzióra, vegye figyelembe, hogy a rendszer eltávolította a JSON-tömb és a CSV-elemző támogatását a blob indexek esetében, mivel ezek a funkciók még előzetes verzióban vannak. Pontosabban a `IndexingParametersExtensions` osztály következő módszerei lettek eltávolítva:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Ha az alkalmazás nem rendelkezik a funkciókhoz szükséges függőséggel, nem fogja tudni frissíteni a Azure Search .NET SDK 5-ös verziójára. Továbbra is használhatja a 4,0-es verziót – előzetes verzió. Ne feledje azonban, hogy az **előnézeti SDK-k éles alkalmazásokban való használatát nem javasoljuk**. Az előzetes verziójú funkciók csak értékelésre használhatók, és változhatnak.

## <a name="conclusion"></a>Összegzés
Ha további részletekre van szüksége a Azure Search .NET SDK használatával kapcsolatban, tekintse meg a [.net útmutató](search-howto-dotnet-sdk.md)című témakört.

Üdvözöljük az SDK-val kapcsolatos visszajelzéseit. Ha problémákba ütközik, kérjen segítséget a [stack overflow](https://stackoverflow.com/questions/tagged/azure-search). Ha hibát talál, a probléma az [Azure .net SDK GitHub-tárházában](https://github.com/Azure/azure-sdk-for-net/issues)is megadható. Ügyeljen arra, hogy a probléma címét "[Azure Search]" előtaggal adja meg.

Köszönjük Azure Search használatát!
