---
title: Üzembehelyezési hibaelhárítási útmutató
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy miként lehet megkerülni, megoldani és elhárítani a Common Docker-telepítési hibákat az Azure Kubernetes szolgáltatással, és Azure Container Instances a Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: fab46f7d7ae74ad643ce3f122b27b0dc767f5a78
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399684"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Az Azure Kubernetes Service és a Azure Container Instances üzemelő példány hibaelhárítása Azure Machine Learning

Megtudhatja, hogyan használhatja a Docker-telepítési hibákat a Azure Container Instances (ACI) és az Azure Kubernetes szolgáltatással (ak) a Azure Machine Learning használatával.

Azure Machine Learning-modell telepítésekor a rendszer számos feladatot hajt végre.

Az ajánlott és a legmodernebb módszer a modell üzembe helyezéséhez a [Model. Deploy () API-](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) t használja egy [környezeti](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) objektum használatával bemeneti paraméterként. Ebben az esetben a szolgáltatás létrehoz egy alap Docker-rendszerképet az üzembe helyezési fázisban, és egy hívásban csatlakoztatja a szükséges modelleket. Az alapszintű üzembe helyezési feladatok a következők:

1. A munkaterület-modell beállításjegyzék regisztrálja a modellt.

2. Következtetési konfiguráció meghatározása:
    1. Hozzon létre egy [környezeti](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) objektumot a környezeti YAML fájlban megadott függőségek alapján, vagy használja a beszerzett környezetek egyikét.
    2. Hozzon létre egy következtetési konfigurációt (InferenceConfig-objektumot) a környezet és a pontozási parancsfájl alapján.

3. A modell üzembe helyezése az Azure Container instance (ACI) szolgáltatásban vagy az Azure Kubernetes szolgáltatásban (ak).

További információ a folyamatról a [modellkezelés](concept-model-management-and-deployment.md) bevezetésében.

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Ha még nem rendelkezik ilyennel, próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).
* A [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)-vel.
* A [Azure Machine learning CLI-bővítménye](reference-azure-machine-learning-cli.md).
* Helyi hibakereséshez rendelkeznie kell egy működő Docker-telepítéssel a helyi rendszeren.

    A Docker-telepítés ellenőrzéséhez használja a parancsot `docker run hello-world` egy terminálról vagy parancssorból. A Docker telepítésével vagy a Docker-hibák elhárításával kapcsolatos információkért tekintse meg a [Docker dokumentációját](https://docs.docker.com/).

## <a name="before-you-begin"></a>Előkészületek

Ha bármilyen problémát tapasztal,-e az első teendő a szolgáltatástelepítési feladat felosztania (előző ismertetett) az egyes lépéseket a probléma.

Feltételezve, hogy az új/ajánlott telepítési módszert használja a [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API-val egy [környezeti](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) objektummal bemeneti paraméterként, a kód három fő lépésből bontható:

1. Regisztrálja a modellt. Íme néhány mintakód:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Következtetési konfiguráció megadása a központi telepítéshez:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Telepítse a modellt az előző lépésben létrehozott következtetési konfiguráció használatával:

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

Miután rendelkezik, az üzembe helyezési folyamat az egyes tevékenységek lebontva, hogy megtekinthessük leggyakoribb hibák.

## <a name="debug-locally"></a>Helyi hibakeresés

Ha olyan problémák merülnek fel, amelyek a modell ACI-vagy AK-beli üzembe helyezésével kapcsolatosak, próbálja meg helyi webszolgáltatásként telepíteni. A helyi webszolgáltatás használatával egyszerűbbé válik a problémák elhárítása. A modellt tartalmazó Docker-rendszerkép le van töltve, és elindult a helyi rendszeren.

> [!WARNING]
> A helyi webszolgáltatások üzembe helyezése éles környezetben nem támogatott.

A helyileg történő üzembe helyezéshez módosítsa a kódot a `LocalWebservice.deploy_configuration()` használatára a telepítési konfiguráció létrehozásához. Ezután a `Model.deploy()` használatával telepítse a szolgáltatást. A következő példa egy modellt helyez üzembe a modell változójában helyi webszolgáltatásként:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Vegye figyelembe, hogy ha saját Conda-specifikációs YAML határoz meg, akkor a azureml alapértelmezett értékeit kell megadnia, amelyek a > = 1.0.45 verzióban pip-függőségként szerepelnek. Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat.

Ezen a ponton a megszokott módon dolgozhat a szolgáltatással. Az alábbi kód például a szolgáltatásnak küldött adatokat mutatja be:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

A Python-környezet testreszabásával kapcsolatos további információkért lásd: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md). 

### <a name="update-the-service"></a>A szolgáltatás frissítése

A helyi tesztelés során előfordulhat, hogy frissítenie kell a `score.py` fájlt a naplózás hozzáadásához, vagy megkísérelni a felderített problémák megoldását. A `score.py` fájl változásainak újratöltéséhez használja a `reload()`. A következő kód például újratölti a szolgáltatáshoz tartozó parancsfájlt, majd adatokat küld neki. A rendszer a frissített `score.py` fájl alapján szerzi be az adatgyűjtést:

> [!IMPORTANT]
> A `reload` metódus csak helyi központi telepítések esetén érhető el. További információ a központi telepítés más számítási célra való frissítéséről: a [modellek üzembe helyezésének](how-to-deploy-and-where.md#update)frissítése szakasz.

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> A parancsfájl a szolgáltatás által használt `InferenceConfig` objektum által megadott helyről lesz újratöltve.

A modell, a Conda-függőségek vagy a telepítési konfiguráció módosításához használja az [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)t. A következő példa frissíti a szolgáltatás által használt modellt:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>A szolgáltatás törlése

A szolgáltatás törléséhez használja a [delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)t.

### <a id="dockerlog"></a>A Docker-napló ellenőrzése

Kinyomtathatja a részletes Docker engine naplóüzenetek a szolgáltatás-objektumból. Megtekintheti az ACI-, AK-és helyi központi telepítések naplóját. Az alábbi példa bemutatja, hogyan lehet kinyomtatni a naplókat.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Szolgáltatás indítása sikertelen lesz.

A rendszerkép sikeres felépítése után a rendszer megkísérli a tároló indítását a telepítési konfiguráció alapján. A tároló indítási folyamatának részeként a rendszer meghívja a pontozási parancsfájl `init()` függvényét. Ha a `init()` függvény nem észlelt kivételeket tartalmaz, előfordulhat, hogy a **CrashLoopBackOff** hiba jelenik meg a hibaüzenetben.

A naplók ellenőrzéséhez használja a [Docker-napló vizsgálata](#dockerlog) szakaszban található információkat.

## <a name="function-fails-get_model_path"></a>Függvény futása: get_model_path()

Gyakran előfordul, hogy a pontozási parancsfájl `init()` függvényében a [Model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) függvényt a rendszer megkeresi egy modellel vagy egy, a tárolóban található, a modell fájljainak mappáját. Ha a modell fájlja vagy mappája nem található, a függvény sikertelen lesz. A legegyszerűbben úgy, hogy ez a hiba hibakeresési, hogy futtassa az alábbi a tároló shellben a Python-kód:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Ez a példa kiírja a helyi elérési utat (a `/var/azureml-app`hoz képest) abban a tárolóban, ahol a pontozási parancsfájl a modell fájljának vagy mappájának megkeresésére vár. Ezután már ellenőrizhető, ha a fájl vagy mappa valóban ahol azt kellene lennie.

Ha a naplózási szint HIBAKERESÉSét állítja be, akkor további információk is naplózhatók, ami hasznos lehet a hiba azonosításához.

## <a name="function-fails-runinput_data"></a>Függvény futása: run(input_data)

Ha a szolgáltatás sikeresen telepítve van, de összeomlik a pontozási végpontra történő adatküldés során, akkor a `run(input_data)` függvényben hozzáadhat egy Error befogási utasítást, hogy a részletes hibaüzenetet adja vissza helyette. Például:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Megjegyzés**: a `run(input_data)` hívással kapcsolatos hibaüzeneteket csak hibakeresési célra lehet elvégezni. Biztonsági okokból az éles környezetben nem adhat vissza hibaüzeneteket.

## <a name="http-status-code-502"></a>HTTP-állapotkód 502

Az 502-es állapotkód azt jelzi, hogy a szolgáltatás kivételt okozott vagy összeomlott a score.py fájl `run()` metódusában. A cikkben található információk segítségével a fájl hibakeresését folytathatja.

## <a name="http-status-code-503"></a>HTTP-állapotkód 503

Az Azure Kubernetes szolgáltatás központi telepítései támogatják az automatikus skálázást, amely lehetővé teszi a replikák hozzáadását a további terhelések támogatásához. Az automatikus skálázás azonban úgy van kialakítva, hogy kezelje a terhelés **fokozatos** változásait. Ha a kérések másodpercenként nagy számú tüskéket kapnak, az ügyfelek a 503-as HTTP-állapotkódot kapják meg.

Két olyan dolog van, amely segíthet megelőzni a 503-es állapotkódot:

* Módosítsa a kihasználtsági szintet, amelynél az automatikus skálázás új replikákat hoz létre.
    
    Alapértelmezés szerint az automatikus skálázási cél kihasználtsága 70%-ra van állítva, ami azt jelenti, hogy a szolgáltatás legfeljebb 30%-os terhelést képes kezelni a másodpercenkénti kérésekben (RPS). A kihasználtsági célt úgy állíthatja be, hogy a `autoscale_target_utilization` alacsonyabb értékre állítja be.

    > [!IMPORTANT]
    > Ez a változás nem eredményezi a replikák *gyorsabb*létrehozását. Ehelyett alacsonyabb kihasználtsági küszöbértékben jönnek létre. Ahelyett, hogy megvárná, amíg a szolgáltatás 70%-ot nem használ, az érték 30%-ra való módosítása a replikákat a 30%-os kihasználtság esetén hozza létre.
    
    Ha a webszolgáltatás már használja a jelenlegi maximális replikákat, és továbbra is a 503-es állapotkódot látja, növelje a `autoscale_max_replicas` értéket a replikák maximális számának növeléséhez.

* Módosítsa a replikák minimális számát. A minimális replikák növelése nagyobb készletet biztosít a bejövő tüskék kezeléséhez.

    A replikák minimális számának növeléséhez állítsa a `autoscale_min_replicas` értéket magasabb értékre. A szükséges replikák kiszámításához használja a következő kódot, és cserélje le az értékeket a projekthez tartozó értékekre:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Ha az új minimális replikánál nagyobb kérelmeket kap, akkor a 503s újra megjelenhet. Ha például a szolgáltatás felé irányuló forgalom növekszik, akkor előfordulhat, hogy növelnie kell a minimális replikákat.

A `autoscale_target_utilization`, `autoscale_max_replicas`és `autoscale_min_replicas` beállításával kapcsolatos további információkért tekintse meg a [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) -modul referenciáját.

## <a name="http-status-code-504"></a>HTTP-állapotkód 504

A 504 állapotkód azt jelzi, hogy a kérelem túllépte az időkorlátot. Az alapértelmezett időkorlát 1 perc.

A felesleges hívások eltávolításához módosítsa a score.py, vagy próbálja meg felgyorsítani a szolgáltatást. Ha ezek a műveletek nem orvosolják a problémát, a jelen cikkben található információk segítségével a score.py-fájl hibakeresését végezheti el. Előfordulhat, hogy a kód lefagyott állapotban vagy végtelen hurokban van.

## <a name="advanced-debugging"></a>Speciális hibakeresés

Bizonyos esetekben előfordulhat, hogy interaktívan kell hibakeresést végeznie a modell üzembe helyezésében található Python-kóddal. Ha például a bejegyzési parancsfájl meghibásodik, és az ok nem határozható meg további naplózással. A Visual Studio Code és a Python Tools for Visual Studio (PTVSD) használatával csatlakoztathatja a Docker-tárolón belül futó kódot.

> [!IMPORTANT]
> Ez a hibakeresési módszer nem működik, ha a `Model.deploy()` és `LocalWebservice.deploy_configuration` használatával helyileg helyezi üzembe a modellt. Ehelyett létre kell hoznia egy rendszerképet a [Model. package ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-) metódus használatával.

A helyi webszolgáltatás üzembe helyezéséhez a helyi rendszeren működő Docker-telepítés szükséges. A Docker használatával kapcsolatos további információkért lásd a [Docker dokumentációját](https://docs.docker.com/).

### <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

1. A Python Tools for Visual Studio (PTVSD) a helyi VS Code-fejlesztési környezetbe való telepítéséhez használja a következő parancsot:

    ```
    python -m pip install --upgrade ptvsd
    ```

    További információ a PTVSD és a VS Code használatával kapcsolatban: [távoli hibakeresés](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Ha a VS Code-t úgy szeretné konfigurálni, hogy kommunikáljon a Docker-lemezképpel, hozzon létre egy új hibakeresési konfigurációt:

    1. A VS Code-ból válassza a __hibakeresés__ menüt, majd válassza a __konfigurációk megnyitása__lehetőséget. Megnyílik egy __Launch. JSON__ nevű fájl.

    1. A __Launch. JSON__ fájlban keresse meg a `"configurations": [`tartalmazó sort, majd szúrja be a következő szöveget:

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Ha már vannak más bejegyzések a konfigurációk szakaszban, adjon hozzá egy vesszőt (,) a beszúrt kód után.

        Ez a szakasz a Docker-tárolóhoz csatlakozik a 5678-es porton keresztül.

    1. Mentse a __Launch. JSON__ fájlt.

### <a name="create-an-image-that-includes-ptvsd"></a>PTVSD tartalmazó rendszerkép létrehozása

1. Módosítsa az üzemelő példány Conda-környezetét, hogy az tartalmazza a PTVSD-t. Az alábbi példa a `pip_packages` paraméterrel való hozzáadását mutatja be:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. A PTVSD elindításához és a szolgáltatás indításakor való várakozáshoz adja hozzá a következőt a `score.py` fájl elejéhez:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Hozzon létre egy rendszerképet a környezeti definíció alapján, és kérje le a rendszerképet a helyi beállításjegyzékbe. A hibakeresés során érdemes lehet módosításokat végezni a rendszerképben anélkül, hogy újból létre kellene hoznia. Ha a Docker-rendszerképben szeretne egy szövegszerkesztőt (VIM) telepíteni, használja a `Environment.docker.base_image` és `Environment.docker.base_dockerfile` tulajdonságokat:

    > [!NOTE]
    > Ez a példa azt feltételezi, hogy `ws` a Azure Machine Learning munkaterületre mutat, és az `model` az üzembe helyezett modell. A `myenv.yml` fájl az 1. lépésben létrehozott Conda-függőségeket tartalmazza.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    A rendszerkép létrehozása és letöltése után a rendszerkép elérési útja (tartalmazza az adattárat, a nevet és a címkét is, amely ebben az esetben a kivonata is) a következőhöz hasonló üzenetben jelenik meg:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Ahhoz, hogy könnyebben működjön a rendszerképpel, a következő paranccsal adhat hozzá egy címkét. Cserélje le a `myimagepath` értéket az előző lépésben megadott Location értékre.

    ```bash
    docker tag myimagepath debug:1
    ```

    A többi lépésnél a teljes rendszerkép elérési útja helyett `debug:1` a helyi rendszerképet.

### <a name="debug-the-service"></a>A szolgáltatás hibakeresése

> [!TIP]
> Ha a `score.py` fájlban beállította a PTVSD-kapcsolat időtúllépését, az időkorlát lejárta előtt csatlakoztatnia kell a VS Code-ot a hibakeresési munkamenethez. Indítsa el a VS Code-ot, nyissa meg `score.py`helyi példányát, állítson be egy töréspontot, és készen áll arra, hogy az ebben a szakaszban ismertetett lépések használata előtt lépjen fel.
>
> A töréspontok hibakeresésével és beállításával kapcsolatos további információkért lásd: [hibakeresés](https://code.visualstudio.com/Docs/editor/debugging).

1. Ha egy Docker-tárolót a rendszerkép használatával szeretne elindítani, használja a következő parancsot:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Ha a VS Code-t a tárolón belül PTVSD szeretné csatolni, nyissa meg a VS Code-ot, és használja az F5 billentyűt, __vagy válassza a__ Ha a rendszer kéri, válassza ki a __Azure Machine learning: Docker hibakeresési__ konfigurációját. Azt is megteheti, hogy kijelöli a hibakeresés ikont az oldalsó sávban, a __Azure Machine learning: Docker debug__ bejegyzést a hibakeresés legördülő menüből, majd a zöld nyíl használatával csatlakoztathatja a hibakeresőt.

    ![A hibakeresés ikon, a hibakeresés elindítása gomb és a konfigurációs választó](./media/how-to-troubleshoot-deployment/start-debugging.png)

Ezen a ponton a VS Code a Docker-tárolón belül csatlakozik a PTVSD-hez, és a korábban megadott törésponton leáll. Most már megkezdheti a kód futtatását, megtekintheti a változókat stb.

A VS Code a Python hibakereséséhez való használatával kapcsolatos további információkért lásd [a Python-kód hibakeresését](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)ismertető témakört.

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>A tároló fájljainak módosítása

A rendszerképben lévő fájlok módosításához csatolhatja a futó tárolót, és végrehajthat egy bash-rendszerhéjat. Innen a Vim használatával szerkesztheti a fájlokat:

1. A futó tárolóhoz való kapcsolódáshoz és egy bash-rendszerhéj a tárolóban történő indításához használja a következő parancsot:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. A szolgáltatás által használt fájlok megkereséséhez használja a következő parancsot a tárolóban található bash-rendszerhéjból, ha az alapértelmezett könyvtár eltér a `/var/azureml-app`:

    ```bash
    cd /var/azureml-app
    ```

    Innen a Vim használatával szerkesztheti a `score.py` fájlt. A Vim használatával kapcsolatos további információkért lásd: [a Vim-szerkesztő használata](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. A tároló módosításai általában nem maradnak meg. A módosítások mentéséhez használja a következő parancsot, mielőtt kilép a fenti lépésben elindított rendszerhéjból (azaz egy másik rendszerhéjban):

    ```bash
    docker commit debug debug:2
    ```

    Ez a parancs létrehoz egy `debug:2` nevű új rendszerképet, amely tartalmazza a módosításokat.

    > [!TIP]
    > A módosítások érvénybe léptetéséhez le kell állítania az aktuális tárolót, és meg kell kezdenie az új verzió használatát.

1. Ügyeljen arra, hogy a tárolóban lévő fájlok módosításai szinkronban legyenek a VS Code által használt helyi fájlokkal. Ellenkező esetben a hibakereső felhasználói felülete nem a várt módon fog működni.

### <a name="stop-the-container"></a>A tároló leállítása

A tároló leállításához használja a következő parancsot:

```bash
docker stop debug
```

## <a name="next-steps"></a>Következő lépések

További információk az üzembe helyezésről:

* [Üzembe helyezés és hol](how-to-deploy-and-where.md)
* [Oktatóanyag: a betanítási & modellek üzembe helyezése](tutorial-train-models-with-aml.md)
