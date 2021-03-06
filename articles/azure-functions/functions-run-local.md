---
title: Azure Functions Core Tools használata
description: Megtudhatja, hogyan teheti meg az Azure functions szolgáltatást a parancssorból vagy a terminálból a helyi számítógépen, mielőtt futtatja őket a Azure Functionson.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: 19691a654162ee3855cb257fd42e29d2e1fc0157
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276659"
---
# <a name="work-with-azure-functions-core-tools"></a>Azure Functions Core Tools használata

Azure Functions Core Tools lehetővé teszi a függvények fejlesztését és tesztelését a helyi számítógépen a parancssorból vagy a terminálból. A helyi függvények csatlakozhatnak az élő Azure-szolgáltatásokhoz, és a függvények a teljes functions Runtime használatával hibakeresést végezhetnek a helyi számítógépen. Az Azure-előfizetésben is üzembe helyezhet egy Function alkalmazást.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

A functions a helyi számítógépen való fejlesztése és az Azure-ba való közzétételük az alapeszközök használatával az alábbi alapvető lépéseket követi:

> [!div class="checklist"]
> * [Telepítse az alapvető eszközöket és a függőségeket.](#v2)
> * [Function app-projekt létrehozása nyelvspecifikus sablonból.](#create-a-local-functions-project)
> * [Trigger-és kötési bővítmények regisztrálása.](#register-extensions)
> * [Tároló és egyéb kapcsolatok definiálása.](#local-settings-file)
> * [Hozzon létre egy függvényt egy triggerből és egy nyelvspecifikus sablonból.](#create-func)
> * [Futtassa helyileg a függvényt.](#start)
> * [Tegye közzé a projektet az Azure-ban.](#publish)

## <a name="core-tools-versions"></a>Alapvető eszközök verziói

A Azure Functions Core Tools három verziója létezik. A használt verzió a helyi fejlesztési környezettől, a [választott nyelvtől](supported-languages.md)és a szükséges támogatási szinttől függ:

+ **1. x verzió**: a Azure functions futtatókörnyezet 1. x verzióját támogatja. Az eszközök ezen verziója csak Windows rendszerű számítógépeken támogatott, és egy NPM- [csomagból](https://www.npmjs.com/package/azure-functions-core-tools)van telepítve.

+ [**2. x/3. x verzió**](#v2): [a Azure functions futtatókörnyezet 2. x vagy 3. x verzióját](functions-versions.md)támogatja. Ezek a verziók támogatják a Windows, a [MacOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)és a [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2) [rendszert](/azure/azure-functions/functions-run-local?tabs=windows#v2), és platform-specifikus csomagkezelő vagy NPM használatával telepíthetők.

Ha másként nincs jelezve, a cikkben szereplő példák a 3. x verzióra vonatkoznak.

## <a name="install-the-azure-functions-core-tools"></a>Az Azure Functions Core Tools telepítése

[Azure functions Core Tools] tartalmaz egy olyan verziót, amely a helyi fejlesztési számítógépen futtatható Azure functions futtatókörnyezetet is felhasználja. Emellett parancsokat is biztosít a függvények létrehozásához, az Azure-hoz való kapcsolódáshoz és a functions-projektek üzembe helyezéséhez.

>[!IMPORTANT]
>Az Azure [CLI](/cli/azure/install-azure-cli) -t helyileg kell telepíteni ahhoz, hogy közzé lehessen tenni az Azure-ban Azure functions Core Tools.  

### <a name="v2"></a>2. x és 3. x verzió

Az eszközök 2. x/3. x verziója a .NET Core-ra épülő Azure Functions futtatókörnyezetet használja. Ez a verzió a .NET Core összes platformján támogatott, beleértve a Windows, a [MacOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)és a [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2) [rendszert](/azure/azure-functions/functions-run-local?tabs=windows#v2)is. 

> [!IMPORTANT]
> A .NET Core SDK telepítési követelményeit kihagyhatja a [bővítmények]használatával.

# <a name="windows"></a>[Windows](#tab/windows)

A következő lépések a NPM segítségével telepítik a Windows rendszerhez tartozó alapvető eszközöket. A [csokit](https://chocolatey.org/)is használhatja. További információ: [alapvető eszközök – fontos](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows)információk.

1. Telepítse a [Node.js]-t, amely tartalmazza a NPM.
    - Az eszközök 2. x verziója esetében csak a Node. js 8,5-es és újabb verziói támogatottak.
    - Az eszközök 3. x verziójában csak a Node. js 10 és újabb verziók támogatottak.

1. Telepítse a Core Tools csomagot:

    ##### <a name="v2x"></a>v2. x

    ```cmd
    npm install -g azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3. x

    ```cmd
    npm install -g azure-functions-core-tools@3
    ```

   A NPM letöltése és telepítése eltarthat néhány percig.

1. Ha nem tervezi a [bővítmények]használatát, telepítse a [Windowshoz készült .net Core 2. x SDK](https://www.microsoft.com/net/download/windows)-t.

# <a name="macos"></a>[macOS](#tab/macos)

A következő lépések a Homebrew-t használják a fő eszközök macOS rendszeren való telepítéséhez.

1. Telepítse a [Homebrew](https://brew.sh/)-t, ha még nincs telepítve.

1. Telepítse a Core Tools csomagot:

    ##### <a name="v2x"></a>v2. x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3. x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```

# <a name="linux"></a>[Linux](#tab/linux)

Az alábbi [lépések segítségével telepítheti az alapvető](https://wiki.debian.org/Apt) eszközöket az Ubuntu/Debian Linux-disztribúción. Más Linux-disztribúciók esetében tekintse meg az [alapvető eszközök readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux)című témakört.

1. Telepítse a Microsoft Package repository GPG kulcsot a csomag integritásának ellenőrzéséhez:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Az APT-frissítés végrehajtása előtt állítsa be a .NET-fejlesztői források listáját.

   A következő parancs futtatásával állíthatja be az APT-forrásokhoz tartozó Ubuntu-listát:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

   A Debian APT-forráslista beállításához futtassa a következő parancsot:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Az alábbi listában keresse meg a megfelelő Linux-verziókhoz tartozó `/etc/apt/sources.list.d/dotnetdev.list` fájlt:

    | Linux-disztribúció | Verzió |
    | --------------- | ----------- |
    | Debian 9 | `stretch` |
    | Debian 8 | `jessie` |
    | Ubuntu 18,10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17,04    | `zesty`     |
    | Ubuntu 16.04/Linux mint 18    | `xenial`  |

1. Az APT-forrás frissítésének elindítása:

    ```bash
    sudo apt-get update
    ```

1. Telepítse a Core Tools csomagot:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. Ha nem tervezi a [bővítmények]használatát, telepítse [a .net Core 2. x SDK-t a Linux rendszerhez](https://www.microsoft.com/net/download/linux).

---

## <a name="create-a-local-functions-project"></a>Egy helyi Functions-projekt létrehozása

A functions projekt könyvtára tartalmazza a [Host. JSON](functions-host-json.md) és a [Local. Settings. JSON](#local-settings-file)fájlt, valamint az egyes függvények kódját tartalmazó almappákat. Ez a könyvtár egyenértékű egy Azure-beli Function alkalmazással. A functions mappa struktúrájával kapcsolatos további tudnivalókért tekintse meg a [Azure functions fejlesztői útmutató](functions-reference.md#folder-structure)című témakört.

A 2. x verzióhoz az inicializáláskor ki kell választania a projekt alapértelmezett nyelvét. A 2. x verzióban minden funkció hozzáadva az alapértelmezett nyelvi sablonokat. Az 1. x verzióban az egyes függvények létrehozásakor minden alkalommal meg kell adnia a nyelvet.

A terminál ablakban vagy egy parancssorban futtassa a következő parancsot a projekt és a helyi git-tárház létrehozásához:

```
func init MyFunctionProj
```

Amikor megadja a projekt nevét, létrejön egy új, a névvel ellátott mappa, amely inicializálva van. Ellenkező esetben az aktuális mappa inicializálása megtörtént.  
A 2. x verzióban a parancs futtatásakor ki kell választania egy futtatókörnyezetet a projekthez. 

<pre>
Select a worker runtime:
dotnet
node
python 
powershell
</pre>

A fel/le nyílbillentyűk használatával válassza ki a nyelvet, majd nyomja le az ENTER billentyűt. Ha JavaScript-vagy írógéppel-függvények fejlesztését tervezi, válassza a **csomópont**lehetőséget, majd válassza ki a nyelvet. Az írógéppel [néhány további követelményt](functions-reference-node.md#typescript)is tartalmaz. 

A kimenet a következő példához hasonlít egy JavaScript-projekthez:

<pre>
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
</pre>

a `func init` a következő beállításokat támogatja, amelyek csak 2. x verziójúak, hacsak másként nincs jelezve:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--csharp`**<br/> **`--dotnet`** | Egy [ C# Class Library-(. cs) projekt](functions-dotnet-class-library.md)inicializálása. |
| **`--csx`** | Egy [ C# parancsfájl-(. CSX) projekt](functions-reference-csharp.md)inicializálása. A következő parancsokban meg kell adnia `--csx`. |
| **`--docker`** | Hozzon létre egy Docker egy tárolóhoz a kiválasztott `--worker-runtime`alapuló alaprendszerkép használatával. Akkor használja ezt a beállítást, ha egyéni Linux-tárolóba szeretne közzétenni. |
| **`--docker-only`** |  Egy meglévő projekthez hozzáadja a Docker. Ha nincs megadva, vagy a local. Settings. JSON fájlban van beállítva, a rendszer felszólítja a feldolgozóra. Akkor használja ezt a beállítást, ha egy meglévő projektet szeretne közzétenni egy egyéni Linux-tárolón. |
| **`--force`** | A projekt inicializálása akkor is, ha vannak meglévő fájlok a projektben. Ez a beállítás felülírja a meglévő fájlokat ugyanazzal a névvel. A Project mappában található egyéb fájlok nem érintettek. |
| **`--java`**  | Egy Java- [projekt](functions-reference-java.md)inicializálása. |
| **`--javascript`**<br/>**`--node`**  | Egy JavaScript- [projekt](functions-reference-node.md)inicializálása. |
| **`--no-source-control`**<br/>**`-n`** | Megakadályozza a git-tárház alapértelmezett létrehozását az 1. x verzióban. A 2. x verzióban a git-tárház alapértelmezés szerint nincs létrehozva. |
| **`--powershell`**  | Egy PowerShell- [projekt](functions-reference-powershell.md)inicializálása. |
| **`--python`**  | Egy Python- [projekt](functions-reference-python.md)inicializálása. |
| **`--source-control`** | Meghatározza, hogy a git-tárház létrejött-e. Alapértelmezés szerint a tárház nem jön létre. Amikor `true`, létrejön egy adattár. |
| **`--typescript`**  | Egy géppel rendelkező [projekt](functions-reference-node.md#typescript)inicializálása. |
| **`--worker-runtime`** | Beállítja a projekt nyelvi futtatókörnyezetét. A támogatott értékek a következők: `csharp`, `dotnet`, `java`, `javascript`,`node` (JavaScript), `powershell`, `python`és `typescript`. Ha nincs beállítva, a rendszer arra kéri, hogy válassza ki a futtatókörnyezetet az inicializálás során. |

> [!IMPORTANT]
> Alapértelmezés szerint a Core Tools 2. x verziója a .net futtatókörnyezethez [ C# (.](functions-dotnet-class-library.md) csproj) hoz létre Function app-projekteket. A C# Visual Studióval vagy a Visual Studio Code-hoz használható projektek a tesztelés során és az Azure-ba való közzététel során fordíthatók le. Ha ehelyett az 1. x verzióban és a portálon létrehozott ugyanazon C# parancsfájl-(. CSX) fájlokat szeretné létrehozni és használni, akkor a függvények létrehozásakor és telepítésekor a `--csx` paramétert is meg kell adnia.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Alapértelmezés szerint ezek a beállítások nem települnek át automatikusan, ha a projekt közzé van téve az Azure-ban. A [közzétételkor](#publish) használja a `--publish-local-settings` kapcsolót, és győződjön meg arról, hogy ezek a beállítások bekerülnek az Azure-beli Function alkalmazásba. Vegye figyelembe, hogy a **ConnectionStrings** lévő értékek soha nem lesznek közzétéve.

A Function app Settings értékeit környezeti változókként is beolvashatja a kódban. További információkért tekintse meg az alábbi nyelvspecifikus témakörök környezeti változók című szakaszát:

* [C#előfordított](functions-dotnet-class-library.md#environment-variables)
* [C#parancsfájl (. CSX)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

Ha nincs beállítva érvényes tárolási kapcsolódási karakterlánc a [`AzureWebJobsStorage`hoz] , és az emulátor nincs használatban, a következő hibaüzenet jelenik meg:

> A AzureWebJobsStorage hiányzó értéke a local. Settings. JSON fájlban. Ez a HTTP-n kívüli összes eseményindító esetében kötelező. Futtathatja a "funkció Azure functionapp Fetch-app-Settings \<functionAppName\>" parancsot, vagy megadhat egy kapcsolatok karakterláncot a local. Settings. JSON fájlban.

### <a name="get-your-storage-connection-strings"></a>A tárolási kapcsolatok karakterláncának beolvasása

Még ha a fejlesztési Microsoft Azure Storage Emulator is használja, érdemes lehet egy tényleges tárolási kapcsolatban is tesztelni. Feltételezve, hogy már [létrehozott egy Storage-fiókot](../storage/common/storage-create-storage-account.md), a következő módokon szerezhet be érvényes tárolási kapcsolatok karakterláncot:

- A [Azure Portalra]keresse meg és válassza ki a **Storage-fiókok**lehetőséget. 
  ![válassza a Storage-fiókok lehetőséget Azure Portal](./media/functions-run-local/select-storage-accounts.png)
  
  Válassza ki a Storage-fiókját, válassza a **hozzáférési kulcsok** lehetőséget a **Beállítások**területen, majd másolja a **kapcsolati karakterlánc** egyik értékét.
  ![a Azure Portal a kapcsolatok karakterláncának másolása](./media/functions-run-local/copy-storage-connection-portal.png)

- Az Azure-fiókhoz való kapcsolódáshoz használjon [Azure Storage Explorer](https://storageexplorer.com/) . Az **Explorerben**bontsa ki az előfizetést, bontsa ki a **Storage-fiókok**elemet, válassza ki a Storage-fiókját, és másolja az elsődleges vagy másodlagos kapcsolatok karakterláncát.

  ![A Storage Explorerból származó kapcsolatok karakterláncának másolása](./media/functions-run-local/storage-explorer.png)

+ Az alábbi parancsok egyikével töltheti le a kapcsolódási karakterláncot az Azure-ból a Core Tools használatával:

  + Egy meglévő Function alkalmazás összes beállításának letöltése:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Egy adott Storage-fiókhoz tartozó kapcsolatok karakterláncának beolvasása:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Ha még nem jelentkezett be az Azure-ba, a rendszer erre kéri.

## <a name="create-func"></a>Függvény létrehozása

Hozzon létre egy függvényt, futtassa a következő parancsot:

```
func new
```

A 2. x verzióban a `func new` futtatásakor a rendszer a Function app alapértelmezett nyelvén kéri a sablon kiválasztását, és a függvény nevét is kiválaszthatja. Az 1. x verzióban a rendszer azt is kéri, hogy válassza ki a nyelvet.

<pre>
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
</pre>

A függvény kódja a megadott nevű almappában jön létre, ahogy az a következő üzenetsor-trigger kimenetében látható:

<pre>
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
</pre>

Ezeket a beállításokat a paranccsal is megadhatja a következő argumentumok használatával:

| Argumentum     | Leírás                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (2. x verzió) Ugyanazokat C# a szkripteket (. CSX) hozza létre, amelyek az 1. x verzióban és a portálon használatosak. |
| **`--language`** , **`-l`**| A sablon programozási nyelve, például C#, F#, vagy JavaScript. Ez a beállítás az 1. x verzióban szükséges. A 2. x verzióban ne használja ezt a kapcsolót, vagy válasszon olyan nyelvet, amely megfelel a munkavégző futtatókörnyezetnek. |
| **`--name`** , **`-n`** | A függvény neve. |
| **`--template`** , **`-t`** | Az `func templates list` parancs használatával megtekintheti az elérhető sablonok teljes listáját az egyes támogatott nyelvekhez.   |

Ha például JavaScript HTTP-triggert szeretne létrehozni egyetlen parancsban, futtassa a következőt:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

Ha üzenetsor által aktivált függvényt szeretne létrehozni egyetlen parancsban, futtassa a következőt:

```
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Függvények helyi futtatása

Functions-projekt futtatásához futtassa a functions gazdagépet. A gazdagép lehetővé teszi az eseményindítók használatát a projektben lévő összes függvénynél. A Start parancs a projekt nyelvétől függően változhat.

# <a name="c"></a>[C\#](#tab/csharp)

```
func start --build
```
# <a name="javascript"></a>[JavaScript](#tab/node)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
Ezt a parancsot [virtuális környezetben kell futtatni](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv).

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> A functions futtatókörnyezet 1. x verziójának a `host` parancsot kell megadnia, ahogy az alábbi példában is látható:
>
> ```
> func host start
> ```

`func start` a következő lehetőségeket támogatja:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | A Futtatás előtt ne hozzon létre aktuális projektet. Csak a DotNet-projektekhez. Az alapértelmezett érték false (hamis). Az 1. x verzió esetében nem támogatott. |
| **`--cert`** | A titkos kulcsot tartalmazó. pfx-fájl elérési útja. Csak `--useHttps`használatos. Az 1. x verzió esetében nem támogatott. |
| **`--cors-credentials`** | A több forrásból származó hitelesített kérelmek (azaz a cookie-k és a hitelesítési fejléc) engedélyezése az 1. x verzió esetében nem támogatott. |
| **`--cors`** | A CORS-eredetek vesszővel tagolt listája, szóközök nélkül. |
| **`--language-worker`** | A nyelv feldolgozójának konfigurálásához szükséges argumentumok. Engedélyezheti például a nyelvi feldolgozó hibakeresését a [hibakeresési port és egyéb szükséges argumentumok](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers)megadásával. Az 1. x verzió esetében nem támogatott. |
| **`--nodeDebugPort`** , **`-n`** | A Node. js hibakereső által használandó port. Default (alapértelmezett): A Launch. JSON vagy A 5858 érték. Csak 1. x verzió. |
| **`--password`** | A jelszó vagy egy olyan fájl, amely egy. pfx fájl jelszavát tartalmazza. Csak `--cert`használatos. Az 1. x verzió esetében nem támogatott. |
| **`--port`** , **`-p`** | A figyelni kívánt helyi port. Alapértelmezett érték: 7071. |
| **`--pause-on-error`** | A folyamat bezárása előtt szüneteltesse a további adatokat. Csak akkor használható, ha egy integrált fejlesztői környezetből (IDE) indít alapszintű eszközöket.|
| **`--script-root`** , **`--prefix`** | A futtatni vagy telepíteni kívánt Function alkalmazás gyökeréhez tartozó elérési út megadására szolgál. Ez olyan lefordított projektek esetében használatos, amelyek a projektfájlok almappában hozhatók elő. Ha például létrehoz egy C# Class Library-projektet, a Host. JSON, a local. Settings. JSON és a function. JSON fájlok egy olyan *gyökérkönyvtárban* jönnek létre, amelynek elérési útja például a `MyProject/bin/Debug/netstandard2.0`. Ebben az esetben az előtagot `--script-root MyProject/bin/Debug/netstandard2.0`ként kell beállítani. Ez a Function alkalmazás gyökere, ha az Azure-ban fut. |
| **`--timeout`** , **`-t`** | A függvények gazdagépének időtúllépése másodpercben. Alapértelmezett: 20 másodperc.|
| **`--useHttps`** | `https://localhost:{port}` kötése a `http://localhost:{port}`helyett. Alapértelmezés szerint ez a beállítás megbízható tanúsítványt hoz létre a számítógépen.|

A functions-gazdagép indításakor a HTTP-triggert függvények URL-címét adja meg:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>Helyileg futtatva az engedélyezés nem kényszeríti ki a HTTP-végpontokat. Ez azt jelenti, hogy az összes helyi HTTP-kérelem `authLevel = "anonymous"`-ként van kezelve. További információkért lásd a http- [kötést ismertető cikket](functions-bindings-http-webhook-trigger.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Tesztelési adat átadása egy függvénynek

A függvények helyi teszteléséhez [indítsa el a functions gazdagépet](#start) , és hívja meg a végpontokat a helyi kiszolgálón a HTTP-kérelmek használatával. A hívott végpont a függvény típusától függ.

>[!NOTE]
> A jelen témakörben szereplő példák a cURL eszközzel küldenek HTTP-kéréseket a terminálról vagy egy parancssorból. Az Ön által választott eszköz használatával HTTP-kéréseket küldhet a helyi kiszolgálónak. A cURL eszköz alapértelmezés szerint a Linux-alapú rendszereken és a Windows 10-es Build 17063-es és újabb verzióiban érhető el. A régebbi Windows rendszeren először le kell töltenie és telepítenie kell a [curl eszközt](https://curl.haxx.se/).

A tesztelési funkciókkal kapcsolatos általánosabb információkért lásd: [stratégiák a kód teszteléséhez Azure functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>A HTTP és a webhook által aktivált függvények

A következő végpontot hívja meg helyileg a HTTP és a webhook által aktivált függvények futtatásához:

    http://localhost:{port}/api/{function_name}

Győződjön meg arról, hogy ugyanazt a kiszolgálónevet és portot használja, amelyet a functions-gazdagép figyel. Ez a függvény gazdagépének indításakor generált kimenetben jelenik meg. Ezt az URL-címet bármely, az trigger által támogatott HTTP-módszerrel meghívhatja.

A következő cURL-parancs elindítja a `MyHttpTrigger` rövid útmutató függvényt egy GET kérelemből a lekérdezési karakterláncban átadott _Name_ paraméterrel.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

A következő példa ugyanazt a függvényt _hívja meg a_ kérelem törzsében a post kérelem átadásakor:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

A lekérdezési karakterláncban található adatok beolvasása egy böngészőből is elvégezhető. Minden más HTTP-módszer esetében a cURL, a Hegedűs, a Poster vagy egy hasonló HTTP-tesztelési eszközt kell használnia.

#### <a name="non-http-triggered-functions"></a>Nem HTTP által aktivált függvények

A HTTP-triggerek, a webhookok és a Event Grid-eseményindítók kivételével a függvények helyi teszteléséhez az adminisztrációs végpont meghívásával végezheti el a funkciókat. Ha ezt a végpontot egy HTTP POST-kérelemmel hívja meg a helyi kiszolgálón, a függvényt indítja el. 

Event Grid aktivált függvények helyi teszteléséhez tekintse meg [a local Testing with Viewer Web App](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app)című témakört.

Szükség esetén a POST kérelem törzsében is elvégezheti a tesztelési célú adatellenőrzést. Ez a funkció hasonló a Azure Portal **teszt** lapjához.

A nem HTTP-függvények elindításához a következő rendszergazdai végpontot kell meghívni:

    http://localhost:{port}/admin/functions/{function_name}

Ahhoz, hogy egy függvény rendszergazdai végpontján át lehessen adni a tesztelési feladatokat, meg kell adnia a POST kérelem üzenet törzsében lévő összes adatát. Az üzenet törzsének a következő JSON-formátummal kell rendelkeznie:

```JSON
{
    "input": "<trigger_input>"
}
```

A `<trigger_input>` érték a függvény által várt formátumú értékeket tartalmaz. A következő cURL-példa egy `QueueTriggerJS` függvény BEJEGYZÉSe. Ebben az esetben a bemenet egy karakterlánc, amely egyenértékű a várólistában várható üzenettel.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

#### <a name="using-the-func-run-command-version-1x-only"></a>Az `func run` parancs használata (csak 1. x verzió)

>[!IMPORTANT]
> Az `func run` parancs csak az eszközök 1. x verziójában támogatott. További információ: [Azure functions futtatókörnyezet verzióinak megcélzása](set-runtime-version.md).

Az 1. x verzióban a függvényeket közvetlenül a `func run <FunctionName>` használatával is meghívhatja, és a függvény bemeneti adatokat is megadhat. Ez a parancs hasonló egy függvény futtatásához a Azure Portal **teszt** lapján.

`func run` a következő lehetőségeket támogatja:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--content`** , **`-c`** | Beágyazott tartalom. |
| **`--debug`** , **`-d`** | A függvény futtatása előtt csatoljon egy hibakeresőt a gazdagéphez.|
| **`--timeout`** , **`-t`** | Várakozási idő (másodpercben), amíg a helyi függvények gazdagépe nem áll készen.|
| **`--file`** , **`-f`** | A tartalomként használandó fájlnév.|
| **`--no-interactive`** | A nem kéri a bevitelt. Automatizálási forgatókönyvek esetén hasznos.|

Ha például egy HTTP-triggert használó függvényt szeretne meghívni, és tartalmat kell átadnia, futtassa a következő parancsot:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Közzététel az Azure-ban

A Azure Functions Core Tools kétféle telepítési típust támogat: a Function-projektfájlok közvetlen üzembe helyezése a Function alkalmazásban a [zip üzembe helyezésével](functions-deployment-technologies.md#zip-deploy) és az [Egyéni Docker-tároló üzembe helyezésével](functions-deployment-technologies.md#docker-container). Már létre kell hoznia [egy Function alkalmazást az Azure-előfizetésében](functions-cli-samples.md#create), amelyre telepíteni fogja a kódot. A fordítást igénylő projekteket úgy kell felépíteni, hogy a bináris fájlok üzembe helyezhetők legyenek.

>[!IMPORTANT]
>Az Azure [CLI](/cli/azure/install-azure-cli) -t helyileg kell telepíteni ahhoz, hogy közzé lehessen tenni az Azure-ban az alapvető eszközökről.  

A Project mappa olyan nyelvspecifikus fájlokat és címtárakat tartalmazhat, amelyeket nem lehet közzétenni. A kizárt elemek a legfelső szintű projekt mappában található. funcignore fájlban szerepelnek.     

### <a name="project-file-deployment"></a>Projektfájlok központi telepítése

Ha a helyi kódot egy Azure-beli Function alkalmazásban szeretné közzétenni, használja a `publish` parancsot:

```
func azure functionapp publish <FunctionAppName>
```

Ez a parancs egy meglévő Function alkalmazásba tesz közzé az Azure-ban. Hibaüzenet jelenik meg, ha olyan `<FunctionAppName>` tesz közzé, amely nem létezik az előfizetésben. Ha szeretné megtudni, hogyan hozhat létre egy Function alkalmazást a parancssorból vagy a terminál ablakból az Azure CLI használatával, tekintse meg [a Függvényalkalmazás létrehozása kiszolgáló nélküli végrehajtáshoz](./scripts/functions-cli-create-serverless.md)című témakört. Alapértelmezés szerint ez a parancs [távoli buildet](functions-deployment-technologies.md#remote-build) használ, és üzembe helyezi az alkalmazást [a központi telepítési csomagból való futtatásra](run-functions-from-deployment-package.md). Az ajánlott telepítési mód letiltásához használja a `--nozip` kapcsolót.

>[!IMPORTANT]
> Ha a Azure Portalban hoz létre egy Function alkalmazást, a függvény alapértelmezés szerint a Function Runtime 2. x verzióját használja. Ha azt szeretné, hogy a Function app a futtatókörnyezet 1. x verzióját használja, kövesse az [1. x verzió futtatásának](functions-versions.md#creating-1x-apps)utasításait.
> Egy meglévő függvényt tartalmazó Function alkalmazás futásidejű verziója nem módosítható.

A következő közzétételi beállítások mindkét verzióra érvényesek: 1. x és 2. x.

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  A beállítások közzététele a local. Settings. JSON fájlban az Azure-ba, ha a beállítás már létezik, a rendszer megkéri a felülírásra. Ha a Microsoft Azure Storage Emulator használja, először módosítsa az alkalmazás beállításait egy [tényleges tárolási kapcsolatban](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | `--publish-local-settings -i` használatakor a rendszer letiltja az Alkalmazásbeállítások felülírását.|

A következő közzétételi beállítások csak a 2. x verzióban támogatottak:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`** , **`-o`** |  Csak a beállítások közzététele és a tartalom kihagyása. Az alapértelmezett érték a prompt. |
|**`--list-ignored-files`** | A közzététel során figyelmen kívül hagyott fájlok listáját jeleníti meg, amely a. funcignore fájlon alapul. |
| **`--list-included-files`** | A közzétett fájlok listáját jeleníti meg, amely a. funcignore fájlon alapul. |
| **`--nozip`** | Kikapcsolja az alapértelmezett `Run-From-Package` módot. |
| **`--build-native-deps`** | Kihagyja a generálás. Wheels mappát a Python-függvények alkalmazásainak közzétételekor. |
| **`--build`** , **`-b`** | Build műveletet hajt végre Linux-Function alkalmazás telepítésekor. Elfogadva: `remote` és `local`. |
| **`--additional-packages`** | A natív függőségek kiépítésekor telepítendő csomagok listája. Például: `python3-dev libevent-dev`. |
| **`--force`** | Bizonyos helyzetekben figyelmen kívül hagyhatja a közzététel előtti ellenőrzést. |
| **`--csx`** | C# Parancsfájl-(. CSX) projekt közzététele. |
| **`--no-build`** | Ne hozzon létre .NET-osztálybeli függvénytár-függvényeket. |
| **`--dotnet-cli-params`** | A lefordított C# (. csproj) függvények közzétételekor a Core Tools a "DotNet Build-output bin/publish" metódust hívja meg. Az erre átadott paraméterek a parancssorhoz lesznek hozzáfűzve. |

### <a name="deploy-custom-container"></a>Egyéni tároló üzembe helyezése

Azure Functions lehetővé teszi a Function projekt üzembe helyezését egy [Egyéni Docker-tárolóban](functions-deployment-technologies.md#docker-container). További információkért lásd: [függvény létrehozása Linuxon egyéni rendszerkép használatával](functions-create-function-linux-custom-image.md). Az egyéni tárolók Docker kell rendelkezniük. Ha Docker szeretne létrehozni egy alkalmazást, használja az--Docker kapcsolót `func init`on.

```
func deploy
```

A következő egyéni tároló üzembe helyezési lehetőségei érhetők el:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Annak a Docker-beállításjegyzéknek a neve, amelyre az aktuális felhasználó bejelentkezett. |
| **`--platform`** | Üzemeltetési platform a Function alkalmazáshoz. Az érvényes beállítások a következők `kubernetes` |
| **`--name`** | Function alkalmazás neve. |
| **`--max`**  | Igény szerint beállíthatja a alkalmazásban telepítendő Function app-példányok maximális számát. |
| **`--min`**  | Igény szerint beállíthatja az alkalmazásban telepítendő Function app-példányok minimális számát. |
| **`--config`** | Egy opcionális telepítési konfigurációs fájl beállítása. |

## <a name="monitoring-functions"></a>Figyelési függvények

A függvények végrehajtásának ajánlott figyelése az Azure Application Insights integrálásával történik. A végrehajtási naplókat a helyi számítógépre is továbbíthatja. További információért lásd: [Azure functions figyelése](functions-monitoring.md).

### <a name="application-insights-integration"></a>Application Insights integráció

Application Insights integrációt engedélyezni kell, amikor létrehozza a Function alkalmazást az Azure-ban. Ha a Function alkalmazás valamilyen okból nem kapcsolódik Application Insights-példányhoz, egyszerűen elvégezheti ezt az integrációt a Azure Portal. 

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Folyamatos átviteli naplók engedélyezése

Megtekintheti a függvények által a helyi számítógépen lévő parancssori munkamenetben létrehozott naplófájlok streamjét. 

#### <a name="native-streaming-logs"></a>Natív adatfolyam-naplók

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Az ilyen típusú folyamatos átviteli naplókhoz a Application Insights integrációjának engedélyezése szükséges a Function alkalmazáshoz.   


## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan fejlesztheti, tesztelheti és teheti közzé Azure Functions a Azure Functions Core Tools [Microsoft Learning modul](https://docs.microsoft.com/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) használatával Azure functions Core Tools [nyílt forráskódú, és a githubon üzemeltethető](https://github.com/azure/azure-functions-cli).  
Egy hiba vagy szolgáltatás kérésének megkereséséhez [Nyisson meg egy GitHub-problémát](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portalra]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
[bővítmények]: functions-bindings-register.md#extension-bundles
