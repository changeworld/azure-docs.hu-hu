---
title: Üzembe helyezési technológiák Azure Functions
description: Megtudhatja, milyen módokon telepíthet programkódot a Azure Functions.
author: georgewallace
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gwallace
ms.openlocfilehash: 43352117d149abbe41ba7bf49a1ffb68e46d2707
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277127"
---
# <a name="deployment-technologies-in-azure-functions"></a>Üzembe helyezési technológiák Azure Functions

Néhány különböző technológiával üzembe helyezheti Azure Functions-projekt kódját az Azure-ban. Ez a cikk részletes listát nyújt ezekről a technológiákról, leírja, hogy mely technológiák érhetők el, amelyek a függvények különböző típusairól szólnak, és ismerteti, hogy mi történik az egyes módszerek használatakor, és javaslatokat tesz a legjobb módszer használatára különféle helyzetekben . A Azure Functions üzembe helyezését támogató különféle eszközök a környezetük alapján a megfelelő technológiára vannak hangolva. Általánosságban elmondható, hogy a zip-telepítés a Azure Functions ajánlott központi telepítési technológiája.

## <a name="deployment-technology-availability"></a>Üzembe helyezési technológia rendelkezésre állása

Azure Functions támogatja a többplatformos helyi fejlesztést és üzemeltetést Windows és Linux rendszeren. Jelenleg három üzemeltetési csomag érhető el:

+ [Fogyasztás](functions-scale.md#consumption-plan)
+ [Prémium](functions-scale.md#premium-plan)
+ [Dedikált (App Service)](functions-scale.md#app-service-plan)

Minden csomag eltérő viselkedéssel rendelkezik. Nem minden központi telepítési technológia érhető el Azure Functions minden egyes ízét. A következő diagramon látható, hogy mely telepítési technológiák támogatottak az operációs rendszer és a üzemeltetési csomag minden kombinációja esetén:

| Üzembe helyezési technológia | Windows-felhasználás | Windows Premium | Dedikált Windows  | Linux-felhasználás | Linux Premium | Linux dedikált |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| Külső csomag URL-címe<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Zip-telepítés |✔|✔|✔|✔|✔|✔|
| Docker-tároló | | | | |✔|✔|
| Web Deploy |✔|✔|✔| | | |
| Verziókövetés |✔|✔|✔| |✔|✔|
| Helyi git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Cloud Sync<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Portál szerkesztése |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> a [manuális trigger-szinkronizálást](#trigger-syncing)igénylő üzembe helyezési technológia.  
<sup>2</sup> a portálon történő szerkesztés csak a http-és időzítő-eseményindítók esetében engedélyezett a Linux-függvények prémium és dedikált csomagok használatával.

## <a name="key-concepts"></a>Fő fogalmak

Néhány kulcsfontosságú fogalom fontos, hogy megértsük, hogyan működnek a központi telepítések Azure Functions.

### <a name="trigger-syncing"></a>Szinkronizálás elindítása

Az eseményindítók módosításakor a functions infrastruktúrájának tisztában kell lennie a változásokkal. A szinkronizálás számos üzembe helyezési technológia esetében automatikusan megtörténik. Bizonyos esetekben azonban manuálisan kell szinkronizálnia az eseményindítókat. Ha a frissítéseket egy külső csomag URL-címére, a helyi git-ra, a felhő-szinkronizálásra vagy az FTP-re hivatkozva telepíti, manuálisan kell szinkronizálnia az eseményindítókat. Az eseményindítók szinkronizálása háromféleképpen végezhető el:

* Indítsa újra a Function alkalmazást a Azure Portal
* HTTP POST-kérés küldése `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` a [főkulcs](functions-bindings-http-webhook-trigger.md#authorization-keys)használatával.
* HTTP POST-kérelem küldése a `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`nak. Cserélje le a helyőrzőket az előfizetés-AZONOSÍTÓra, az erőforráscsoport nevére és a függvény alkalmazásának nevére.

### <a name="remote-build"></a>Távoli Build

A Azure Functions automatikusan képes a zip-telepítések után kapott programkódra épülő buildek végrehajtására. Ezek a buildek némileg eltérően működnek attól függően, hogy az alkalmazás Windows vagy Linux rendszeren fut-e. A távoli buildek nem hajthatók végre, ha egy alkalmazás már a [csomag](run-functions-from-deployment-package.md) módból való futtatásra van beállítva. A távoli Build használatának megismeréséhez navigáljon a [zip üzembe helyezéshez](#zip-deploy).

> [!NOTE]
> Ha problémák merülnek fel a távoli buildtel kapcsolatban, annak oka az lehet, hogy az alkalmazást a szolgáltatás elérhetővé tétele előtt hozták létre (2019. augusztus 1.). Próbáljon meg új Function alkalmazást létrehozni, vagy futtasson `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` a Function alkalmazás frissítéséhez. A parancs végrehajtása két próbálkozást is igénybe vehet.

#### <a name="remote-build-on-windows"></a>Távoli Build Windows rendszeren

A Windowson futó összes Function apps egy kis felügyeleti alkalmazással, az SCM (vagy [kudu](https://github.com/projectkudu/kudu)) hellyel rendelkezik. Ez a hely kezeli a központi telepítés nagy részét, és felépíti a logikát a Azure Functions számára.

Ha egy alkalmazás Windows rendszerre van telepítve, a nyelvspecifikus parancsok, például a `dotnet restore` (C#) vagy a `npm install` (JavaScript) futnak.

#### <a name="remote-build-on-linux"></a>Távoli Build Linuxon

A Linuxon a távoli buildek engedélyezéséhez be kell állítani a következő [Alkalmazásbeállítások](functions-how-to-use-azure-function-app-settings.md#settings) :

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Alapértelmezés szerint a [Azure functions Core Tools](functions-run-local.md) és a [Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) -hoz készült Azure functions-bővítmény is távoli buildeket hajt végre a Linux rendszeren való üzembe helyezéskor. Emiatt mindkét eszköz automatikusan létrehozza ezeket a beállításokat az Azure-ban. 

Az alkalmazások Linux rendszeren való létrehozásakor [a központi telepítési csomagból futnak](run-functions-from-deployment-package.md). 

##### <a name="consumption-plan"></a>Használatalapú csomag

A használati tervben futó Linux-függvények alkalmazásai nem rendelkeznek SCM/kudu hellyel, ami korlátozza az üzembe helyezési lehetőségeket. Az alkalmazások a használati csomagban futó Linux rendszeren azonban támogatják a távoli buildeket.

##### <a name="dedicated-and-premium-plans"></a>Dedikált és prémium csomagok

A [dedikált (App Service)](functions-scale.md#app-service-plan) csomagban Linux rendszeren futó alkalmazások funkcióinak, valamint a [prémium csomagnak](functions-scale.md#premium-plan) korlátozott SCM/kudu-hely is van.

## <a name="deployment-technology-details"></a>Üzembe helyezési technológia részletei

A következő üzembe helyezési módszerek érhetők el Azure Functionsban.

### <a name="external-package-url"></a>Külső csomag URL-címe

Külső csomag URL-címével hivatkozhat a Function alkalmazást tartalmazó távoli csomag (. zip) fájlra. A fájl a megadott URL-címről töltődik le, és az alkalmazás [csomag](run-functions-from-deployment-package.md) módban fut.

>__Használat:__ `WEBSITE_RUN_FROM_PACKAGE` hozzáadása az alkalmazás beállításaihoz. A beállítás értékének URL-címnek kell lennie (a futtatni kívánt adott csomagfájl helye). A beállításokat a [portálon](functions-how-to-use-azure-function-app-settings.md#settings) vagy [Az Azure CLI használatával](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)is hozzáadhatja. 
>
>Ha az Azure Blob Storage-t használja, használjon egy [közös hozzáférési aláírással (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) rendelkező privát tárolót, amely lehetővé teszi a funkciók elérését a csomaghoz. Az alkalmazás újraindításakor a rendszer lekéri a tartalom egy példányát. A hivatkozásnak érvényesnek kell lennie az alkalmazás élettartamára.

>__Mikor érdemes használni:__ A külső csomag URL-címe az egyetlen támogatott üzembe helyezési módszer a Linux rendszeren futó Azure Functions a használati tervben, ha a felhasználó nem szeretné, hogy [távoli buildek](#remote-build) történjenek. Amikor frissíti a alkalmazáscsomag által hivatkozott csomagfájl adatait, [manuálisan kell szinkronizálnia az eseményindítókat](#trigger-syncing) , hogy tájékoztassa az Azure-t arról, hogy az alkalmazás megváltozott.

### <a name="zip-deploy"></a>Zip-telepítés

A zip-telepítés használatával leküldheti a Function alkalmazást az Azure-ba tartalmazó. zip-fájlt. Megadhatja, hogy az alkalmazás a [csomagból](run-functions-from-deployment-package.md)induljon el, vagy megadhatja, hogy a rendszer [távoli buildet](#remote-build) hajtson végre.

>__Használat:__ Üzembe helyezéséhez használja kedvenc ügyfélprogramját: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure)vagy a parancssorból a [Azure functions Core Tools](functions-run-local.md#project-file-deployment)használatával. Ezek az eszközök alapértelmezés szerint a zip-telepítést használják, és [a csomagból futnak](run-functions-from-deployment-package.md). A Core Tools és a Visual Studio Code bővítmény lehetővé teszi a [távoli buildek](#remote-build) telepítését a Linux rendszeren való üzembe helyezéskor. Ha a. zip-fájlt manuálisan szeretné telepíteni a Function alkalmazásba, kövesse az [üzembe helyezés a. zip fájlból vagy URL-címről](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)című témakör utasításait.

>Ha a zip-telepítés használatával végzi a telepítést, beállíthatja, hogy az alkalmazás [csomagból fusson](run-functions-from-deployment-package.md). A csomagból való futtatáshoz állítsa `1`értékre a `WEBSITE_RUN_FROM_PACKAGE` Alkalmazásbeállítások értékét. A zip-telepítést javasoljuk. Gyorsabb betöltési időt eredményez az alkalmazások számára, és ez az alapértelmezett a VS Code, a Visual Studio és az Azure CLI számára. 

>__Mikor érdemes használni:__ A zip-telepítés a Azure Functions ajánlott központi telepítési technológiája.

### <a name="docker-container"></a>Docker-tároló

Telepítheti a Function alkalmazást tartalmazó Linux-tároló lemezképét.

>__Használat:__ Hozzon létre egy Linux-függvény alkalmazást a prémium vagy a dedikált csomagban, és adja meg, melyik tárolót szeretné futtatni. Ezt két módon teheti meg:
>
>* Hozzon létre egy Linux-függvény alkalmazást egy Azure App Service csomaggal a Azure Portal. A **közzétételhez**válassza a **Docker-rendszerkép**lehetőséget, majd konfigurálja a tárolót. Adja meg azt a helyet, ahol a rendszerkép található.
>* Hozzon létre egy Linux-függvény alkalmazást egy App Service csomagon az Azure CLI használatával. További információ: [függvény létrehozása Linux rendszeren egyéni rendszerkép használatával](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function).
>
>Ha egy meglévő alkalmazást szeretne üzembe helyezni egy egyéni tárolóval, akkor a [Azure functions Core Toolsban](functions-run-local.md)használja a [`func deploy`](functions-run-local.md#publish) parancsot.

>__Mikor érdemes használni:__ Akkor használja a Docker-tároló lehetőséget, ha nagyobb mértékű vezérlésre van szüksége a Function alkalmazást futtató linuxos környezetben. Ez a központi telepítési mechanizmus csak a Linux rendszeren futó függvények esetében érhető el.

### <a name="web-deploy-msdeploy"></a>Web Deploy (MSDeploy)

A web Deploy csomagokat és üzembe helyezi a Windows-alkalmazásait bármely IIS-kiszolgálón, beleértve az Azure-ban Windowson futó Function-alkalmazásokat is.

>__Használat:__ [A Visual Studio Tools for Azure functions](functions-create-your-first-function-visual-studio.md)használata. Törölje a **Futtatás a csomagfájl alapján (ajánlott)** jelölőnégyzet jelölését.
>
>Letöltheti továbbá a [web Deploy 3,6](https://www.iis.net/downloads/microsoft/web-deploy) -et, és közvetlenül hívhatja `MSDeploy.exe`.

>__Mikor érdemes használni:__ A web Deploy támogatott, és nem tartalmaz problémát, de az előnyben részesített mechanizmus a [zip üzembe helyezése a csomaggal engedélyezett futtatással](#zip-deploy). További információt a [Visual Studio fejlesztői útmutatójában](functions-develop-vs.md#publish-to-azure)talál.

### <a name="source-control"></a>Verziókövetés

A verziókövetés használatával a Function alkalmazást egy git-tárházhoz is összekapcsolhatjuk. A tárházban lévő kód frissítése elindítja az üzembe helyezést. További információ: [kudu wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Használat:__ A portál functions területén használja a Deployment Center alkalmazást a verziókövetés közzétételének beállításához. További információ: [Azure functions folyamatos üzembe helyezése](functions-continuous-deployment.md).

>__Mikor érdemes használni:__ A verziókövetés használata az ajánlott eljárás olyan csapatok esetében, amelyek együttműködnek a funkció alkalmazásaiban. A verziókövetés jó üzembe helyezési lehetőség, amely kifinomultabb üzembe helyezési folyamatokat tesz lehetővé.

### <a name="local-git"></a>Helyi Git

A helyi gépen a git használatával leküldheti a kódot a helyi gépről Azure Functions.

>__Használat:__ Kövesse a [helyi git-telepítés](../app-service/deploy-local-git.md)utasításait Azure app Service.

>__Mikor érdemes használni:__ Általában azt javasoljuk, hogy használjon másik telepítési módszert. Amikor a helyi git-ből tesz közzé, [manuálisan kell szinkronizálnia az eseményindítókat](#trigger-syncing).

### <a name="cloud-sync"></a>Felhőbeli szinkronizálás

A Cloud Sync használatával szinkronizálhatja a Dropbox és a OneDrive tartalmát a Azure Functionsba.

>__Használat:__ Kövesse a [tartalom szinkronizálása egy felhőalapú mappából](../app-service/deploy-content-sync.md)című témakör utasításait.

>__Mikor érdemes használni:__ Általánosságban elmondható, hogy más üzembe helyezési módszereket is ajánlunk. A Cloud Sync használatával történő közzétételkor [manuálisan kell szinkronizálnia az eseményindítókat](#trigger-syncing).

### <a name="ftp"></a>FTP

Az FTP használatával közvetlenül átviheti a fájlokat a Azure Functionsba.

>__Használat:__ Kövesse a [tartalom üzembe helyezése FTP/s használatával](../app-service/deploy-ftp.md)című témakör utasításait.

>__Mikor érdemes használni:__ Általánosságban elmondható, hogy más üzembe helyezési módszereket is ajánlunk. Ha FTP használatával tesz közzé, [manuálisan kell szinkronizálnia az eseményindítókat](#trigger-syncing).

### <a name="portal-editing"></a>Portál szerkesztése

A portálon alapuló szerkesztőben közvetlenül szerkesztheti a Function alkalmazásban található fájlokat (lényegében a módosítások mentésekor minden alkalommal üzembe helyezheti őket).

>__Használat:__ Ahhoz, hogy szerkeszteni tudja a függvényeket a Azure Portalban, létre kell hoznia [a függvényeket a portálon](functions-create-first-azure-function.md). Az igazság egyetlen forrásainak megtartása érdekében bármely más üzembe helyezési módszer használatával a függvény csak olvasható, és nem teszi lehetővé a portál folyamatos szerkesztését. Ha vissza szeretne térni olyan állapotba, amelyben szerkesztheti a fájljait a Azure Portalban, manuálisan is visszakapcsolhatja a szerkesztési módot, hogy `Read/Write` és eltávolítsa a telepítéssel kapcsolatos összes beállítást (például `WEBSITE_RUN_FROM_PACKAGE`). 

>__Mikor érdemes használni:__ A portál jó módszer a Azure Functions megkezdésére. Az intenzívebb fejlesztési munka érdekében javasoljuk, hogy a következő ügyféleszközök egyikét használja:
>
>* [Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Azure Functions Core Tools (parancssor)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

A következő táblázat a portál szerkesztését támogató operációs rendszereket és nyelveket tartalmazza:

| | Windows-felhasználás | Windows Premium | Dedikált Windows | Linux-felhasználás | Linux Premium | Linux dedikált |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C#Parancsfájl |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (előzetes verzió) | | | | | | |
| PowerShell (előzetes verzió) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> A portál szerkesztése csak a HTTP-és időzítő-eseményindítók esetében engedélyezett a Linux-és prémium szintű és dedikált csomagok használatával.

## <a name="deployment-slots"></a>Üzembehelyezési pontok

Amikor üzembe helyezi a Function alkalmazást az Azure-ban, az üzembe helyezést külön üzembe helyezheti közvetlenül az éles környezet helyett. Az üzembe helyezési pontokkal kapcsolatos további információkért tekintse meg a részleteket a [Azure functions üzembe helyezési](../app-service/deploy-staging-slots.md) pontok dokumentációjában.

## <a name="next-steps"></a>Következő lépések

Olvassa el ezeket a cikkeket a Function apps üzembe helyezésével kapcsolatos további információkért: 

+ [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)
+ [Folyamatos kézbesítés az Azure DevOps használatával](functions-how-to-azure-devops.md)
+ [A Azure Functions zip-telepítései](deployment-zip-push.md)
+ [Azure Functions futtatása csomagfájl](run-functions-from-deployment-package.md)
+ [A Function alkalmazás erőforrás-telepítésének automatizálása Azure Functions](functions-infrastructure-as-code.md)
