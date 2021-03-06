---
title: Azure Cosmos DB SQL API-erőforrások kezelése a .net v4 SDK-val
description: Útmutató a konzolos alkalmazások .net v4 SDK használatával történő létrehozásához Azure Cosmos DB SQL API-fiók erőforrásainak kezeléséhez.
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/10/2020
ms.openlocfilehash: 6587b6d5ceb18bcc2374594aef91da91f792bb84
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585934"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Gyors útmutató: konzol-alkalmazás létrehozása a .net v4 SDK használatával Azure Cosmos DB SQL API-fiók erőforrásainak kezeléséhez.

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Ismerkedés a Azure Cosmos DB SQL API .NET-hez készült ügyféloldali kódtáraval. Kövesse a jelen dokumentum lépéseit a .NET v4 (Azure. Cosmos) csomag telepítéséhez, egy alkalmazás létrehozásához, és próbálja ki az alapszintű szifilisz-műveletekre vonatkozó példát a Azure Cosmos DBban tárolt adatokon. 

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. A Azure Cosmos DB a kulcs/érték, a dokumentum és a Graph-adatbázisok gyors létrehozásához és lekérdezéséhez használható. A .NET-hez készült Azure Cosmos DB SQL API ügyféloldali kódtára a következőre használható:

* Azure Cosmos-adatbázis és-tároló létrehozása
* Mintaadatok hozzáadása a tárolóhoz
* Adatok lekérdezése 
* Az adatbázis törlése

[Könyvtár forráskódja](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | [csomag (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/) , vagy ingyenes Azure-előfizetés nélkül is [kipróbálhat Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) 
* [Net Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). `dotnet --version`futtatásával ellenőrizheti, hogy melyik verzió érhető el a környezetben.

## <a name="setting-up"></a>Beállítás

Ez a szakasz végigvezeti egy Azure Cosmos-fiók létrehozásán és egy olyan projekt beállításán, amely Azure Cosmos DB SQL API-ügyfél-függvénytárat használ a .NET-hez az erőforrások kezeléséhez. A cikkben ismertetett mintakód létrehoz egy `FamilyDatabase` adatbázist és egy családtagot (az egyes családtagok egy elem) az adott adatbázison belül. Minden családtag olyan tulajdonságokkal rendelkezik, mint például a `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`. A `LastName` tulajdonságot a tároló partíciós kulcsaként használja a rendszer. 

### <a id="create-account"></a>Azure Cosmos-fiók létrehozása

Ha az [ingyenes kipróbálás Azure Cosmos db](https://azure.microsoft.com/try/cosmosdb/) lehetőséget használja az Azure Cosmos-fiók létrehozásához, létre kell hoznia egy **SQL API**típusú Azure Cosmos db fiókot. Már létrehoztak egy Azure Cosmos DB tesztelési fiókot. Nem kell explicit módon létrehoznia a fiókot, így kihagyhatja ezt a szakaszt, és átléphet a következő szakaszra.

Ha rendelkezik saját Azure-előfizetéssel, vagy ingyenes előfizetést hozott létre, explicit módon hozzon létre egy Azure Cosmos-fiókot. A következő kód egy Azure Cosmos-fiókot hoz létre a munkamenet konzisztenciájával. A fiók replikálása `South Central US` és `North Central US`.  

Az Azure Cosmos-fiók létrehozásához Azure Cloud Shell használható. A Azure Cloud Shell egy interaktív, hitelesített, böngészővel elérhető rendszerhéj az Azure-erőforrások kezeléséhez. Rugalmasságot biztosít a működésének legmegfelelőbb rendszerhéj-élmény kiválasztásában, vagy bash vagy PowerShell használatával. Ebben a rövid útmutatóban válassza a **bash** mód lehetőséget. Azure Cloud Shell is szükség van egy Storage-fiókra, ha a rendszer kéri, létrehozhat egyet.

Válassza az alábbi kód melletti **kipróbálás** gombot, válassza a **bash** mód lehetőséget, válassza a **Storage-fiók létrehozása** és a Cloud Shellra való bejelentkezés lehetőséget. Ezután másolja és illessze be a következő kódot az Azure Cloud shellbe, és futtassa. Az Azure Cosmos-fiók nevének globálisan egyedinek kell lennie, a parancs futtatása előtt győződjön meg arról, hogy a `mysqlapicosmosdb` értéket frissíti.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Az Azure Cosmos-fiók létrehozása eltarthat egy ideig, amint a művelet sikeres, láthatja a megerősítő kimenetet. A parancs sikeres befejeződése után jelentkezzen be a [Azure Portalba](https://portal.azure.com/) , és ellenőrizze, hogy létezik-e a megadott nevű Azure Cosmos-fiók. Az erőforrás létrehozása után a Azure Cloud Shell ablakot is lezárhatja. 

### <a id="create-dotnet-core-app"></a>Új .NET-alkalmazás létrehozása

Hozzon létre egy új .NET-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben. Nyissa meg a Windows-parancssort vagy egy terminál-ablakot a helyi számítógépről. A következő szakaszban lévő összes parancsot a parancssorból vagy a terminálból fogja futtatni.  Futtassa a következő DotNet új parancsot egy új, `todo`nevű alkalmazás létrehozásához. A--langVersion paraméter beállítja a LangVersion tulajdonságot a létrehozott projektfájl számára.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

   ```bash
   cd todo
   dotnet build
   ```

A Build várt kimenetének a következőhöz hasonlóan kell kinéznie:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a id="install-package"></a>A Azure Cosmos DB csomag telepítése

Miközben továbbra is az alkalmazás könyvtárában található, telepítse a .NET Core-hoz készült Azure Cosmos DB ügyféloldali kódtárat a DotNet-csomag hozzáadása paranccsal.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Az Azure Cosmos-fiók hitelesítő adatainak másolása a Azure Portal

A minta alkalmazásnak hitelesítenie kell magát az Azure Cosmos-fiókban. A hitelesítéshez át kell adni az Azure Cosmos-fiók hitelesítő adatait az alkalmazásnak. Az Azure Cosmos-fiók hitelesítő adatait a következő lépésekkel szerezheti be:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

1. Navigáljon az Azure Cosmos-fiókjához.

1. Nyissa meg a **kulcsok** ablaktáblát, és másolja a fiókjának **URI-JÁT** és **elsődleges kulcsát** . A következő lépésben hozzá kell adnia az URI és a kulcsok értékeit egy környezeti változóhoz.

## <a id="object-model"></a>Objektummodell

Az alkalmazás létrehozásának megkezdése előtt tekintsük át a Azure Cosmos DB erőforrásainak hierarchiáját, és az erőforrások létrehozásához és eléréséhez használt objektummodell. A Azure Cosmos DB erőforrásokat hoz létre a következő sorrendben:

* Azure Cosmos-fiók 
* Adatbázisok 
* Tárolók 
* Elemek

A különböző entitások hierarchiájának megismeréséhez tekintse meg az [adatbázisok, tárolók és elemek használata Azure Cosmos db](databases-containers-items.md) cikkben. A következő .NET-osztályokat fogja használni az alábbi erőforrásokkal való interakcióhoz:

* CosmosClient – ez az osztály a Azure Cosmos DB szolgáltatás ügyféloldali logikai ábrázolását biztosítja. Az ügyfél-objektum a kérések konfigurálására és végrehajtására szolgál a szolgáltatáson.
* Createdatabaseifnotexistasync metódusának – ez a metódus (ha nem létezik) vagy (ha már létezik) egy adatbázis-erőforrás aszinkron műveletként való létrehozása. 
* CreateContainerIfNotExistsAsync – ez a metódus (ha nem létezik) vagy (ha már létezik) a tároló aszinkron műveletként jön létre. A válaszban található állapotkód alapján megállapíthatja, hogy a tárolót újonnan hozták-e létre (201), vagy egy meglévő tárolót adott vissza (200). 
* CreateItemAsync – ez a metódus létrehoz egy tételt a tárolón belül.
* UpsertItemAsync – ez a metódus létrehoz egy tételt a tárolón belül, ha még nem létezik, vagy lecseréli az adott elemre, ha az már létezik. 
* GetItemQueryIterator – ez a metódus létrehoz egy lekérdezést az Azure Cosmos-adatbázis tárolói alá tartozó elemekhez egy paraméteres értékeket tartalmazó SQL-utasítás használatával. 
* DeleteAsync – törli a megadott adatbázist az Azure Cosmos-fiókból. `DeleteAsync` a metódus csak az adatbázist törli.

 ## <a id="code-examples"></a>Példák a kódokra

A cikkben ismertetett mintakód egy családi adatbázist hoz létre Azure Cosmos DBban. A család adatbázisa olyan családi adatokat tartalmaz, mint például a név, a lakcím, a hely, a társított szülők, a gyermekek és a háziállatok. Mielőtt feltölti az adatokat az Azure Cosmos-fiókjába, definiálja egy családi elem tulajdonságait. Hozzon létre egy új, `Family.cs` nevű osztályt a minta alkalmazás legfelső szintjén, és adja hozzá a következő kódot:

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Family.cs":::

### <a name="add-the-using-directives--define-the-client-object"></a>Adja hozzá a using direktíva & a Client objektum megadása

A projekt könyvtárában nyissa meg a szerkesztőben a `Program.cs` fájlt, és adja hozzá a következő használati irányelveket az alkalmazás tetején:

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="Usings":::


Adja hozzá a következő globális változókat a `Program` osztályban. Ezek közé tartozik a végpont és az engedélyezési kulcsok, az adatbázis neve és a létrehozandó tároló. Ügyeljen rá, hogy a végpont és az engedélyezési kulcsok értékét a környezetnek megfelelően cserélje le. 

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="Constants":::

Végül cserélje le a `Main` metódust:

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="Main":::

### <a name="create-a-database"></a>Adatbázis létrehozása 

Adja meg a `CreateDatabaseAsync` metódust a `program.cs` osztályban. Ez a metódus hozza létre a `FamilyDatabase`, ha még nem létezik.

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="CreateDatabaseAsync":::

### <a name="create-a-container"></a>Tároló létrehozása

Adja meg a `CreateContainerAsync` metódust a `Program` osztályban. Ez a metódus hozza létre a `FamilyContainer`, ha még nem létezik. 

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="CreateContainerAsync":::

### <a name="create-an-item"></a>Elemek létrehozása

Hozzon létre egy családi tételt a `AddItemsToContainerAsync` metódus hozzáadásával a következő kóddal. Egy tétel létrehozásához használhatja a `CreateItemAsync` vagy a `UpsertItemAsync` metódust:

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="AddItemsToContainerAsync":::

### <a name="query-the-items"></a>Elemek lekérdezése

Egy elem beszúrása után futtathat egy lekérdezést, amely az "Andersen" család részleteit kéri le. A következő kód bemutatja, hogyan hajthatja végre a lekérdezést közvetlenül az SQL-lekérdezés használatával. A "Anderson" család részleteit a következő SQL-lekérdezéssel érheti el: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Adja meg a `QueryItemsAsync` metódust a `Program` osztályban, és adja hozzá a következő kódot:

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="QueryItemsAsync":::

### <a name="replace-an-item"></a>Egy tétel cseréje 

Olvassa el a családi elemeket, majd frissítse azt úgy, hogy hozzáadja a `ReplaceFamilyItemAsync` metódust a következő kóddal.

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="ReplaceFamilyItemAsync":::

### <a name="delete-an-item"></a>Elemek törlése 

A `DeleteFamilyItemAsync` metódusnak a következő kóddal való hozzáadásával törölje a családi elemeket.

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="DeleteFamilyItemAsync":::

### <a name="delete-the-database"></a>Az adatbázis törlése 

Végül törölheti az `DeleteDatabaseAndCleanupAsync` metódust hozzáadó adatbázist a következő kóddal:

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="DeleteDatabaseAndCleanupAsync":::

Az összes szükséges metódus hozzáadását követően mentse a `Program` fájlt. 

## <a name="run-the-code"></a>A kód futtatása

Ezután hozza létre és futtassa az alkalmazást a Azure Cosmos DB erőforrások létrehozásához.

   ```bash
   dotnet run
   ```

Az alkalmazás futtatásakor a következő kimenet jön létre. A Azure Portalba is bejelentkezhet, és ellenőrizheti, hogy az erőforrások létrejöttek-e:

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

Az adatok létrejöttének ellenőrzéséhez jelentkezzen be a Azure Portalba, és tekintse meg az Azure Cosmos-fiókban szükséges elemeket. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja az Azure CLI-t vagy Azure PowerShell az Azure Cosmos-fiók és a hozzá tartozó erőforráscsoport eltávolításához. Az alábbi parancs bemutatja, hogyan törölheti az erőforráscsoportot az Azure CLI használatával:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos-fiókot, hogyan hozhat létre egy adatbázist és egy tárolót egy .NET Core-alkalmazás használatával. Mostantól a következő cikk utasításait követve importálhat további információkat az Azure Cosmos-fiókjába. 

> [!div class="nextstepaction"]
> [Adatok importálása az Azure Cosmos DB-be](import-data.md)
