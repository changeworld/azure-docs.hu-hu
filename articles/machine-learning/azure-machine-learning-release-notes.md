---
title: A kiadás újdonságai
titleSuffix: Azure Machine Learning
description: Ismerkedjen meg a Azure Machine Learning legújabb frissítéseivel, valamint a gépi tanulással és az adat-előkészítéssel kapcsolatos Python SDK-val.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 03/10/2020
ms.openlocfilehash: c12a6efd608625b93b1a084de3ceb790a8773eee
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129801"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning kibocsátási megjegyzések

Ebben a cikkben megismerheti Azure Machine Learning kiadásait.  A teljes SDK-hivatkozási tartalomért keresse fel a Azure Machine Learning [**fő SDK for Python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -referenciát tartalmazó oldalt.

Az ismert hibák és a megkerülő megoldások megismeréséhez tekintse meg [az ismert problémák listáját](resource-known-issues.md) .

## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK a Python v 1.1.5

+ **Szolgáltatás elavulása**
  + **Python 2,7**
    + A Python 2,7 támogatásának utolsó verziója

+ **Változtatások megszakítása**
  + **Szemantikai verziószámozás 2.0.0**
    + Az 1,1-es verziótól kezdődően az Azure ML Python SDK a szemantikai verziószámozási 2.0.0 alkalmazza. [További tudnivalók itt](https://semver.org/). Minden további verzió új számozási sémát és szemantikai verziószámozási szerződést fog követni. 

+ **Hibajavítások és javítások**
  + **Azure-CLI-ml**
    + Módosítsa a végpont CLI-parancsának nevét az "az ml Endpoint AK" értékről az "az ml Endpoint Realtime" értékre a konzisztencia érdekében.
    + a CLI-telepítési utasítások frissítése a stabil és a kísérleti ág parancssori felületéhez
    + Az Egypéldányos profilkészítés egy javaslat előállítására lett javítva, és elérhetővé tettük az alap SDK-ban.
  + **azureml-automl-Core**
    + Engedélyezve van a Batch Mode következtetés (több sor többszöri elkészítése) a automl ONNX-modellekhez
    + Javult az adathalmazok gyakoriságának észlelése, az adat hiánya vagy a szabálytalan adatpontokat tartalmazó információk
    + Hozzáadta az olyan adatpontok eltávolításának lehetőségét, amelyek nem felelnek meg a domináns gyakoriságnak.
    + Módosította a konstruktor bemenetét, hogy a megfelelő oszlopok imputálási beállításait alkalmazza.
    + A hiba naplózása javult.
  + **azureml-automl-futtatókörnyezet**
    + Kijavítottuk a hibát, amely akkor fordul elő, ha a betanítási készletben nem szereplő gabona szerepel a tesztelési készletben.
    + Az előrejelzési szolgáltatásra vonatkozó pontozás során eltávolította a y_query követelményt
    + Kijavítottuk az előrejelzéssel kapcsolatos problémát, ha az adathalmaz rövid, hosszú idejű hézagokat tartalmazó gabonát tartalmaz.
    + Kijavítottuk a problémát, ha az automatikus maximális horizont be van kapcsolva, és a Date oszlop karakterláncok formájában tartalmaz dátumokat. A rendszer a megfelelő átalakítást és hibaüzeneteket adta hozzá, amikor a dátumra váltás nem lehetséges
    + Natív NumPy és SciPy használata a FileCacheStore köztes adatközpont szerializálásához és deszerializálásához (a helyi AutoML futtatásához használatos)
    + Kijavítva a hiba, ahol a gyermek futása meghiúsult a futó állapotban.
    + Nagyobb sebesség a featurization.
    + A gyakoriság ellenőrzésének kijavítása a pontozás során – mostantól az előrejelzési feladatok nem igénylik a vonat és a tesztelési készlet szigorú gyakorisági egyenértékűségét.
    + Módosította a konstruktor bemenetét, hogy a megfelelő oszlopok imputálási beállításait alkalmazza.
    + A lag típusának kiválasztásával kapcsolatos rögzített hibák.
    + Az adatkészleteken kiváltott, nem besorolt hiba kijavítva, az egyetlen sorral rendelkező gabonákkal
    + Javítva lett a probléma a gyakoriság észlelésének lassúságával.
    + Kijavít egy hibát a AutoML kivételek kezelésére vonatkozóan, ami azt eredményezte, hogy a AttributeError nem helyettesíthetik a betanítási hibák valós okát.
  + **azureml – CLI – gyakori**
    + Az Egypéldányos profilkészítés egy javaslat előállítására lett javítva, és elérhetővé tettük az alap SDK-ban.
  + **azureml – megterhelés – Mir**
    + Funkciók beolvasása a MirWebservice osztályban a hozzáférési jogkivonat lekéréséhez
    + A MirWebservice jogkivonat-hitelesítés használata alapértelmezés szerint a MirWebservice. Run () hívása esetén, ha a hívás sikertelen
    + A Mir webszolgáltatások üzembe helyezéséhez a [Ds2v2, a A2v2 és a F16] helyett megfelelő SKU-ket [Standard_DS2_v2, Standard_F16, Standard_A2_v2] szükséges.
  + **azureml – egytörzsű folyamatok – lépések**
    + A nem kötelező paraméter side_inputs hozzáadva a ParallelRunStep-hez. Ezzel a paraméterrel lehet csatlakoztatni a mappát a tárolón. A jelenleg támogatott típusok a következők: DataReference és PipelineData.
    + A ParallelRunConfig átadott paraméterek felülírása a folyamat paramétereinek átadásával lehetséges. Az új folyamat paraméterei támogatottak aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count és aml_process_count_per_node már a korábbi kiadás részét képezik).
  + **azureml – mag**
    + A AzureML webszolgáltatások üzembe helyezése mostantól alapértelmezés szerint `INFO` a naplózáshoz. Ezt a `AZUREML_LOG_LEVEL` környezeti változó beállításával szabályozhatja a telepített szolgáltatásban.
    + A Python SDK a felderítési szolgáltatás használatával "API" végpontot használ a "folyamatok" helyett.
    + Csere az új útvonalakra az összes SDK-hívásban
    + A ModelManagementService irányuló hívások átirányításának módosítása egy új egyesített struktúrára
      + A munkaterület frissítési metódusa nyilvánosan elérhető.
      + Image_build_compute paraméter hozzáadva a munkaterület-frissítési metódusban, amely lehetővé teszi a felhasználó számára a rendszerkép-létrehozási számítások frissítését.
    +  Elavult üzeneteket adott hozzá a régi profilkészítési munkafolyamathoz. Rögzített profilkészítési CPU-és memória-korlátok
    + A RSection hozzáadása a környezet részeként az R-feladatok futtatásához
    +  Ha az adatkészlet forrása nem érhető el, vagy nem tartalmaz adatokat, a rendszer az ellenőrzéshez hozzáadta a hibát a `Dataset.mount`hoz.
    + `--grant-workspace-msi-access` hozzáadva további paraméterként az adattár CLI-hez az Azure Blob-tároló regisztrálásához, amely lehetővé teszi a VNet mögötti blob-tároló regisztrálását.
    + Az Egypéldányos profilkészítés egy javaslat előállítására lett javítva, és elérhetővé tettük az alap SDK-ban.
    + Kijavítva a problémát a aks.py _deploy
    + Ellenőrzi a feltöltés alatt álló modellek integritását a csendes tárolási hibák elkerülése érdekében.
    + A felhasználók mostantól meghatározhatnak egy értéket az Auth kulcshoz a webszolgáltatások kulcsainak újragenerálása során.
    + Kijavítva a hiba, ahol a nagybetűk nem használhatók adatkészlet bemeneti neveként
  + **azureml – alapértékek**
    + a `azureml-dataprep` mostantól `azureml-defaults`részeként települ. Az adatkészletek csatlakoztatásához már nem szükséges a adatelőkészítés [Fuse] manuális telepítése a számítási célokon.
  + **azureml – értelmezés**
    + Frissítve azureml – értelmezés a Közösség 0,6. *
    + Frissített azureml – az értelmezéstől függ a közösségi 0.5.0
    + azureml-stílusú kivételek hozzáadva a azureml-értelmezéshez
    + rögzített DeepScoringExplainer szerializálás kerasz-modellekhez
  + **azureml – mlflow**
    + Adja hozzá a szuverén felhők támogatását a azureml. mlflow
  + **azureml – folyamat – mag**
    + A pipeline batch pontozási jegyzetfüzet mostantól a ParallelRunStep-t használja
    + Kijavítva egy hiba, ahol a PythonScriptStep-eredmények helytelenül újra felhasználhatók az argumentumok listájának módosításakor
    + Lehetőség van az oszlopok típusának beállítására, amikor a parse_ * metódusok meghívásakor `PipelineOutputFileDataset`
  + **azureml – folyamat – lépések**
    + Áthelyezte a `AutoMLStep` a `azureml-pipeline-steps` csomagba. A `AutoMLStep` elavult a `azureml-train-automl-runtime`on belül.
    + Dokumentáció hozzáadva példa az adatkészlethez PythonScriptStep-bemenetként
  + **azureml – tensorboard**
    + Frissítve azureml-tensorboard a tensorflow 2,0 támogatásához
    + Helyes portszám megjelenítése, ha egy számítási példányon egyéni Tensorboard-portot használ
  + **azureml-Train-automl-Client**
    + Kijavított egy hibát, amelyben bizonyos csomagok a távoli futtatások helytelen verzióiban telepíthetők.
    + rögzített FeaturizationConfig-felülbírálási probléma, amely az egyéni featurization-konfigurációt szűri.
  + **azureml-Train-automl-Runtime**
    + Kijavítottuk a gyakoriság észlelésével kapcsolatos problémát a távoli futtatásokban.
    + Áthelyezte a `AutoMLStep` a `azureml-pipeline-steps`-csomagban. A `AutoMLStep` elavult a `azureml-train-automl-runtime`on belül.
  + **azureml-Train-Core**
    + A PyTorch 1,4-es verziójának támogatása a PyTorch-Kalkulátorban
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK for Python v 1.1.2 RC0 (előzetes verzió)

+ **Hibajavítások és javítások**
  + **azureml-automl-Core**
    + Engedélyezve van a Batch Mode következtetés (több sor többszöri elkészítése) a automl ONNX-modellekhez
    + Javult az adathalmazok gyakoriságának észlelése, az adat hiánya vagy a szabálytalan adatpontokat tartalmazó információk
    + Hozzáadta az olyan adatpontok eltávolításának lehetőségét, amelyek nem felelnek meg a domináns frequrncy.
  + **azureml-automl-futtatókörnyezet**
    + Kijavítottuk a hibát, amely akkor fordul elő, ha a betanítási készletben nem szereplő gabona szerepel a tesztelési készletben.
    + Az előrejelzési szolgáltatásra vonatkozó pontozás során eltávolította a y_query követelményt
  + **azureml – megterhelés – Mir**
    + Funkciók beolvasása a MirWebservice osztályban a hozzáférési jogkivonat lekéréséhez
  + **azureml – mag**
    + A AzureML webszolgáltatások üzembe helyezése mostantól alapértelmezés szerint `INFO` a naplózáshoz. Ezt a `AZUREML_LOG_LEVEL` környezeti változó beállításával szabályozhatja a telepített szolgáltatásban.
    + A munkaterületen regisztrált összes adatkészlet visszaküldéséhez javítsa a `Dataset.get_all` megismétlését.
    + Javítsa a hibaüzenetet, ha a rendszer érvénytelen típust ad át az adatkészlet-létrehozási API-k `path` argumentumának.
    + A Python SDK a felderítési szolgáltatás használatával "API" végpontot használ a "folyamatok" helyett.
    + Csere az új útvonalakra az összes SDK-hívásban
    + A ModelManagementService irányuló hívások átirányításának módosítása egy új egyesített struktúrára
      + A munkaterület frissítési metódusa nyilvánosan elérhető.
      + Image_build_compute paraméter hozzáadva a munkaterület-frissítési metódusban, amely lehetővé teszi a felhasználó számára a rendszerkép-létrehozási számítások frissítését.
    +  Elavult üzeneteket adott hozzá a régi profilkészítési munkafolyamathoz. Rögzített profilkészítési CPU-és memória-korlátok
  + **azureml – értelmezés**
    + azureml frissítése – értelmezés az értelmezéshez – Community 0,6. *
  + **azureml – mlflow**
    + Adja hozzá a szuverén felhők támogatását a azureml. mlflow
  + **azureml – folyamat – lépések**
    + Áthelyezte a `AutoMLStep` a `azureml-pipeline-steps package`ba. A `AutoMLStep` elavult a `azureml-train-automl-runtime`on belül.
  + **azureml-Train-automl-Client**
    + Kijavított egy hibát, amelyben bizonyos csomagok a távoli futtatások helytelen verzióiban telepíthetők.
  + **azureml-Train-automl-Runtime**
    + Kijavítottuk a gyakoriság észlelésével kapcsolatos problémát a távoli futtatásokban.
    + Áthelyezte a `AutoMLStep` a `azureml-pipeline-steps package`ba. A `AutoMLStep` elavult a `azureml-train-automl-runtime`on belül.
  + **azureml-Train-Core**
    + Áthelyezte a `AutoMLStep` a `azureml-pipeline-steps package`ba. A `AutoMLStep` elavult a `azureml-train-automl-runtime`on belül.

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK for Python v 1.1.1 RC0 (előzetes verzió)

+ **Hibajavítások és javítások**
  + **Azure-CLI-ml**
    + Az Egypéldányos profilkészítés egy javaslat előállítására lett javítva, és elérhetővé tettük az alap SDK-ban.
  + **azureml-automl-Core**
    + A hiba naplózása javult.
  + **azureml-automl-futtatókörnyezet**
    + Kijavítottuk az előrejelzéssel kapcsolatos problémát, ha az adathalmaz rövid, hosszú idejű hézagokat tartalmazó gabonát tartalmaz.
    + Kijavítottuk a problémát, ha az automatikus maximális horizont be van kapcsolva, és a Date oszlop karakterláncok formájában tartalmaz dátumokat. A megfelelő átalakítást és az értelmes hibát adta hozzá, ha a dátumra történő átalakítás nem lehetséges
    + Natív NumPy és SciPy használata a FileCacheStore köztes adatközpont szerializálásához és deszerializálásához (a helyi AutoML futtatásához használatos)
    + Kijavítva a hiba, ahol a gyermek futása meghiúsult a futó állapotban.
  + **azureml – CLI – gyakori**
    + Az Egypéldányos profilkészítés egy javaslat előállítására lett javítva, és elérhetővé tettük az alap SDK-ban.
  + **azureml – mag**
    + `--grant-workspace-msi-access` hozzáadva további paraméterként az adattár CLI-hez az Azure Blob-tároló regisztrálásához, amely lehetővé teszi a VNet mögötti blob-tároló regisztrálását.
    + Az Egypéldányos profilkészítés egy javaslat előállítására lett javítva, és elérhetővé tettük az alap SDK-ban.
    + Kijavítva a problémát a aks.py _deploy
    + Ellenőrzi a feltöltés alatt álló modellek integritását a csendes tárolási hibák elkerülése érdekében.
  + **azureml – értelmezés**
    + azureml-stílusú kivételek hozzáadva a azureml-értelmezéshez
    + rögzített DeepScoringExplainer szerializálás kerasz-modellekhez
  + **azureml – folyamat – mag**
    + A pipeline batch pontozási jegyzetfüzet mostantól a ParallelRunStep-t használja
  + **azureml – folyamat – lépések**
    + Áthelyezte a `AutoMLStep` a `azureml-pipeline-steps`-csomagban. A `AutoMLStep` elavult a `azureml-train-automl-runtime`on belül.
  + **azureml – egytörzsű folyamatok – lépések**
    + A nem kötelező paraméter side_inputs hozzáadva a ParallelRunStep-hez. Ezzel a paraméterrel lehet csatlakoztatni a mappát a tárolón. A jelenleg támogatott típusok a következők: DataReference és PipelineData.
  + **azureml – tensorboard**
    + Frissítve azureml-tensorboard a tensorflow 2,0 támogatásához
  + **azureml-Train-automl-Client**
    + rögzített FeaturizationConfig-felülbírálási probléma, amely az egyéni featurization-konfigurációt szűri.
  + **azureml-Train-automl-Runtime**
    + Áthelyezte a `AutoMLStep` a `azureml-pipeline-steps`-csomagban. A `AutoMLStep` elavult a `azureml-train-automl-runtime`on belül.
  + **azureml-Train-Core**
    + A PyTorch 1,4-es verziójának támogatása a PyTorch-Kalkulátorban
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK for Python v 1.1.0 RC0 (előzetes verzió)

+ **Változtatások megszakítása**
  + **Szemantikai verziószámozás 2.0.0**
    + Az 1,1-es verziótól kezdődően az Azure ML Python SDK a szemantikai verziószámozási 2.0.0 alkalmazza. [További tudnivalók itt](https://semver.org/). Minden további verzió új számozási sémát és szemantikai verziószámozási szerződést fog követni. 
  
+ **Hibajavítások és javítások**
  + **azureml-automl-futtatókörnyezet**
    + Nagyobb sebesség a featurization.
    + A gyakoriság ellenőrzésének kijavítása a pontozás során – mostantól az előrejelzési feladatokban nem szükséges szigorú gyakorisági egyenértékűséget megadni a vonat és a tesztelési készlet között.
  + **azureml – mag**
    + A felhasználók mostantól meghatározhatnak egy értéket az Auth kulcshoz a webszolgáltatások kulcsainak újragenerálása során.
  + **azureml – értelmezés**
    + Frissített azureml – az értelmezéstől függ a közösségi 0.5.0
  + **azureml – folyamat – mag**
    + Kijavítva egy hiba, ahol a PythonScriptStep-eredmények helytelenül újra felhasználhatók az argumentumok listájának módosításakor
  + **azureml – folyamat – lépések**
    + Dokumentáció hozzáadva példa az adatkészlethez PythonScriptStep-bemenetként
  + **azureml – egytörzsű folyamatok – lépések**
    + A ParallelRunConfig átadott paraméterek felülírása a folyamat paramétereinek átadásával lehetséges. Az új folyamat paraméterei támogatottak aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count és aml_process_count_per_node már a korábbi kiadás részét képezik).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK a Python v 1.0.85

+ **Új funkciók**
  + **azureml – mag**
    + Egy adott munkaterülethez és előfizetéshez tartozó AmlCompute-erőforrások aktuális alapvető használati és kvóta-korlátozásának beolvasása
  
  + **azureml – egytörzsű folyamatok – lépések**
    + Lehetővé teheti, hogy a felhasználó átadja a táblázatos adatkészletet közbenső eredményként az előző lépésből a parallelrunstep

+ **Hibajavítások és javítások**
  + **azureml-automl-futtatókörnyezet**
    + A rendszer eltávolította a kérelemben szereplő y_query oszlop követelményét a telepített előrejelző szolgáltatáshoz. 
    + A "y_query" el lett távolítva a Dominic Orange Juice notebook Service Request szakaszból.
    + Javítva lett a hiba, amely megakadályozza a telepített modellekre vonatkozó előrejelzéseket, és a dátum-és időoszlopokat tartalmazó adatkészleteken működik.
    + Az Matthews korrelációs együtthatója egy besorolási metrikaként lett hozzáadva mind a bináris, mind a többosztályos besoroláshoz.
  + **azureml – összetétel – értelmezés**
    + Eltávolított szöveges magyarázatok a azureml – a szöveg magyarázata átkerült a tolmácsolási szöveges tárházba, amely hamarosan elérhető lesz.
  + **azureml – mag**
    + Adatkészlet: a fájl adatkészlethez való használata már nem függ a Python env-be telepítendő NumPy és pandáktől.
    + Módosította a LocalWebservice. wait_for_deployment () parancsot a helyi Docker-tároló állapotának ellenőrzésére, mielőtt megpróbálja az állapot-végpont pingelését, nagy mértékben csökkenti a sikertelen központi telepítés jelentésének időtartamát.
    + A LocalWebservice. reload () metódusban használt belső tulajdonság inicializálásának kijavítása, ha a szolgáltatási objektum egy meglévő telepítésből jön létre a LocalWebservice () konstruktor használatával.
    + A pontosításhoz szerkesztett hibaüzenet.
    + Egy get_access_token () nevű új metódust adott hozzá a (z) AksWebservice, amely a hozzáférési tokent tartalmazó AksServiceAccessToken objektumot adja vissza, az időbélyeg és a token típusának lejárta után. 
    + A AksWebservice meglévő get_token () metódusa elavult, mivel az új metódus visszaadja a metódus által visszaadott összes információt.
    + Az az ml Service Get-Access-Token parancs módosított kimenete. Átnevezte a tokent a accessToken és a refreshBy értékre a refreshAfter. A expiryOn és a tokenType tulajdonság hozzáadva.
    + Rögzített get_active_runs
  + **azureml – magyarázat – modell**
    + frissítve a 0.33.0 és a tolmácsolás – Közösség 0,4. *
  + **azureml – értelmezés**
    + frissítve a 0.33.0 és a tolmácsolás – Közösség 0,4. *
  + **azureml-Train-automl-Runtime**
    + Az Matthews korrelációs együtthatója egy besorolási metrikaként lett hozzáadva mind a bináris, mind a többosztályos besoroláshoz.
    + Az előfeldolgozási jelző elavult a kódban, és lecserélve a következőre: featurization-featurization alapértelmezés szerint be van kapcsolva

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK a Python v 1.0.83

+ **Új funkciók**
  + Adatkészlet: vegyen fel két lehetőséget `on_error` és `out_of_range_datetime` a `to_pandas_dataframe`, ha az adatok hibás értékkel rendelkeznek, ahelyett, hogy `None`.
  + Munkaterület: a `hbi_workspace` jelzőt adta hozzá a bizalmas adatokkal rendelkező munkaterületekhez, amelyek lehetővé teszik a további titkosítást, és letiltja a speciális diagnosztikát a munkaterületeken. Emellett a saját kulcsok a társított Cosmos DB-példányhoz való bevezetését is támogatja a `cmk_keyvault` és `resource_cmk_uri` paraméterek megadásával a munkaterület létrehozásakor, amely létrehoz egy Cosmos DB példányt az előfizetésben a munkaterület kiépítése során. [Itt olvashat bővebben.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Hibajavítások és javítások**
  + **azureml-automl-futtatókörnyezet**
    + Javítva lett egy olyan regresszió, amely TypeError okozta a AutoML a 3.5.4 alatti Python-verziókban való futtatásakor.
  + **azureml – mag**
    + Kijavítva a hiba a `datastore.upload_files`ban, ahol a relatív elérési út, amely nem `./` indult el, nem használható.
    + Elavult üzenetek hozzáadása az összes rendszerkép osztály codepaths
    + Rögzített modellkezelés URL-felépítés a mooncake régióhoz.
    + Kijavítva a probléma, hogy az source_dir-t használó modellek nem tölthetők Azure Functionshoz.    
    + Lehetőség a [környezethez. build_local ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) a rendszerkép leküldése a AzureML Workspace-tároló beállításjegyzékbe
    + Frissítette az SDK-t, hogy az új jogkivonat-függvénytárat használja az Azure szinapszisban egy visszamenőleges kompatibilis módon.
  + **azureml – értelmezés**
    + Kijavítva a hiba, ha a rendszer nem adott vissza semmilyen magyarázatot a letöltéshez. A mostantól kivételt vet fel.
  + **azureml – folyamat – lépések**
    + A rendszer nem engedélyezte a `DatasetConsumptionConfig`s `Estimator``inputs` paraméterének átadását, ha az `Estimator` egy `EstimatorStep`lesz használatban.
  + **azureml – SDK**
    + AutoML-ügyfél hozzáadva a azureml-SDK csomaghoz, amely lehetővé teszi a távoli AutoML futtatását, hogy a teljes AutoML csomag telepítése nélkül legyen elküldve.
  + **azureml-Train-automl-Client**
    + A konzol kimenetének javított igazítása a automl futtatásához
    + Kijavítva a hiba, ahol a Panda helytelen verziója lehet a távoli amlcompute telepítve.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK a Python v 1.0.81

+ **Hibajavítások és javítások**
  + **azureml – összetétel – értelmezés**
    + a Shap-függőség elhalasztása a azureml értelmezéséhez
  + **azureml – mag**
    + A számítási cél mostantól paraméterként is megadható a megfelelő telepítési konfigurációs objektumokhoz. Ez kifejezetten annak a számítási célnak a neve, amelyet telepíteni kell, nem az SDK-objektumra.
    + CreatedBy-információ hozzáadva a modell-és szolgáltatás-objektumokhoz. <var>on keresztül érhető el. created_by
    + Rögzített ContainerImage. Run (), amely nem volt megfelelően beállítva a Docker-tároló HTTP-portjának beállítása.
    + `az ml dataset register` CLI-parancshoz `azureml-dataprep` választhatóvé tétele
    + Kijavított egy hibát, amelyben a `TabularDataset.to_pandas_dataframe` helytelenül térhet vissza egy másik olvasóhoz, és kinyomtat egy figyelmeztetést.
  + **azureml – magyarázat – modell**
    + a Shap-függőség elhalasztása a azureml értelmezéséhez
  + **azureml – folyamat – mag**
    + Új folyamat lépésének `NotebookRunnerStep`a helyi jegyzetfüzetnek a folyamat lépéseként való futtatásához.
    + Elavult get_all függvények lettek eltávolítva a PublishedPipelines, az ütemtervekhez és a PipelineEndpoints
  + **azureml-Train-automl-Client**
    + A rendszer elindította a data_script elavulttá tételét a AutoML bemenetként.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK a Python v 1.0.79

+ **Hibajavítások és javítások**
  + **azureml-automl-Core**
    + A naplózott featurizationConfig eltávolítva
      + Frissült a naplózás az "Auto"/"off"/"Customized" kifejezésre.
  + **azureml-automl-futtatókörnyezet**
    + A Panda-támogatás hozzáadva. Adatsorozat és pandák. Kategorikus az oszlop adattípusának észleléséhez. Korábban csak a NumPy. ndarray támogatott.
      + A kapcsolódó kód módosításainak hozzáadása a kategorikus dtype megfelelő kezeléséhez.
    + Az előrejelzési funkció felülete javult: a y_pred paraméter nem kötelező. – A docstrings javult.
  + **azureml – adathalmaz**
    + Kijavítva egy hiba, amelyben a címkézett adatkészletek nem csatlakoztathatók.
  + **azureml – mag**
    + A `Environment.from_existing_conda_environment(name, conda_environment_name)`hibajavítása. A felhasználó létrehozhat egy olyan környezet-példányt, amely a helyi környezet pontos replikája
    + Módosult az idősorozattal kapcsolatos adatkészletek alapértelmezett `include_boundary=True`i módszerei.
  + **azureml-Train-automl-Client**
    + Kijavítva a hiba, ha az érvényesítési eredmények nem lesznek kinyomtatva, ha a kimenet értéke hamis.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK a Python v 1.0.76

+ **Változtatások megszakítása**
  + Azureml – AutoML frissítési problémák
    + Frissítés a azureml-Train-automl > = 1.0.76 from azureml-automl < 1.0.76 részleges telepítéseket okozhat, ami miatt egyes automl-importálások sikertelenek lesznek. Ennek megoldásához futtassa a https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmdcímen található telepítési parancsfájlt. Vagy ha közvetlenül a pip-et használja, a következőket teheti:
      + "pip install--upgrade azureml-Train-automl"
      + "pip install--Mellőzés – telepített azureml-Train-automl-Client"
    + vagy eltávolíthatja a régi verziót a frissítés előtt
      + "pip eltávolítása azureml-Train-automl"
      + "pip install azureml-Train-automl"

+ **Hibajavítások és javítások**
  + **azureml-automl-futtatókörnyezet**
    + A AutoML mostantól az igaz és a hamis osztályokat is figyelembe veszik a bináris besorolási feladatok átlagos skaláris metrikáinak kiszámításakor.
    + Áthelyezte a Machine learning-és betanítási kódot a AzureML-AutoML-Core-ban egy új, AzureML-AutoML-Futtatókörnyezetű csomagba.
  + **azureml – adathalmaz**
    + Amikor meghívja a `to_pandas_dataframe`t egy címkézett adatkészleten a letöltési lehetőséggel, megadhatja, hogy felülírja-e a meglévő fájlokat.
    + Ha olyan `keep_columns` vagy `drop_columns` hív meg, amely egy idősor, felirat vagy képoszlop eldobását eredményezi, akkor a rendszer a megfelelő képességeket is elveti az adatkészlethez.
    + Az objektum észlelése feladat pytorch betöltő hibája javítva.
  + **azureml – összetétel – értelmezés**
    + A magyarázat-irányítópult widget eltávolítása a azureml-untribal-értelmezze, changed Package, hogy az újat hivatkozzon a interpret_community
    + A tolmácsolási Közösség frissített verziója 0.2.0
  + **azureml – mag**
    + `workspace.datasets`teljesítményének javítása.
    + Az Azure SQL Database adattár regisztrálása felhasználónévvel és jelszóval történő hitelesítéssel
    + Javítsa a RunConfigurations betöltését a relatív elérési utakról.
    + Ha olyan `keep_columns` vagy `drop_columns` hív meg, amely egy idősor-oszlop eldobását eredményezi, akkor a megfelelő képességek is el lesznek dobva az adatkészlethez.
  + **azureml – értelmezés**
    + A tolmácsolási Közösség frissített verziója 0.2.0
  + **azureml – folyamat – lépések**
    + Az Azure Machine learning-folyamat lépéseinek `runconfig_pipeline_params` dokumentált támogatott értékei.
  + **azureml – folyamat – mag**
    + CLI-beállítás hozzáadva a kimenet JSON formátumban való letöltéséhez a folyamat parancsaihoz.
  + **azureml-Train-automl**
    + Split AzureML – AutoML 2 csomagba, egy AzureML-Train-AutoML-Client és egy ML-es képzési csomag AzureML-Train-AutoML-Runtime
  + **azureml-Train-automl-Client**
    + Egy vékony ügyfelet vett fel a AutoML-kísérletek elküldéséhez anélkül, hogy a gépi tanulási függőségeket helyileg kellene telepítenie.
    + Az automatikusan észlelt késések, a gördülő ablakok méretei és a távoli futtatások maximális távlatai rögzített naplózása.
  + **azureml-Train-automl-Runtime**
    + Új AutoML-csomag hozzáadva a Machine learning és a futásidejű összetevők elkülönítéséhez az ügyfélről.
  + **azureml-a-a-Train-RL**
    + Megerősítő tanulási támogatás az SDK-ban.
    + AmlWindowsCompute-támogatás hozzáadva az RL SDK-ban.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK a Python v 1.0.74

  + **Előzetes verziójú funkciók**
    + **azureml – adathalmaz**
      + A azureml-adathalmazok importálása után meghívhatja `Dataset.Labeled.from_json_lines` helyett `._Labeled` egy címkézett adatkészlet létrehozásához.
      + Amikor meghívja a `to_pandas_dataframe`t egy címkézett adatkészleten a letöltési lehetőséggel, megadhatja, hogy felülírja-e a meglévő fájlokat.
      + Ha `keep_columns` vagy `drop_columns` hív meg, amely egy idősorozat, felirat vagy képoszlop eldobását eredményezi, akkor a rendszer a megfelelő képességeket is elveti az adatkészlethez.
      + A PyTorch betöltő a `dataset.to_torchvision()`hívásakor rögzített hibákat észlelt.

+ **Hibajavítások és javítások**
  + **Azure-CLI-ml**
    + Modell-profilkészítés hozzáadva az előnézeti CLI-hez.
    + Javítja az Azure Storage-ban fellépő változások változását, ami miatt a CLI AzureML sikertelen lesz.
    + Load Balancer típus hozzáadva az MLC-hoz az AK-típusoknál
  + **azureml-automl-Core**
    + Kijavítottuk a problémát az idősorozatok maximális horizontjának észlelésével, hiányzó értékekkel és több gabonával.
    + Kijavítottuk a hibák számát a kereszt-ellenőrzési felosztások létrehozásakor.
    + A szakasz helyébe egy Markdown formátumú üzenet jelenik meg, amely a kibocsátási megjegyzésekben szerepel: – az előrejelzési adatkészletekben a rövid szemek jobb kezelését.
    + Kijavítottuk a problémát néhány felhasználói információ maszkolásakor a naplózás során. – A hibák továbbfejlesztett naplózása az előrejelzési futtatások során.
    + A psutil hozzáadása Conda-függőségként az automatikusan létrehozott YML-telepítési fájlhoz.
  + **azureml – megterhelés – Mir**
    + Javítja az Azure Storage-ban fellépő változások változását, ami miatt a CLI AzureML sikertelen lesz.
  + **azureml – mag**
    + Javít egy olyan hibát, amely a Azure Functionson üzembe helyezett modelleket okozta a 500 létrehozásához.
    + Kijavított egy hibát, amelyben a amlignore-fájl nem lett alkalmazva a pillanatképekre.
    + Hozzáadott egy új API-amlcompute. get_active_runs, amely visszaad egy generátort a futó és a várólistán lévő futtatásokhoz egy adott amlcompute.
    + Load Balancer típust adtak hozzá az MLC-hoz az AK-típusoknál.
    + Append_prefix bool paramétert adtak hozzá a run.py-ben, és a artifacts_client-ben download_artifacts_from_prefix a download_files. Ez a jelző a forrás filepath szelektív összevonására szolgál, így csak a fájl-vagy mappanév kerül a output_directoryba
    + A deszerializálási probléma kijavítása az adatkészletek használatával `run_config.yml`.
    + Amikor meghívja `keep_columns` vagy `drop_columns`, amely egy idősorozat-oszlop eldobását eredményezi, a megfelelő képességek el lesznek dobva az adatkészlethez is.
  + **azureml – értelmezés**
    + Frissített értelmezés – közösségi verzió a 0.1.0.3
  + **azureml-Train-automl**
    + Kijavított egy problémát, amelyben a automl_step nem lehet kinyomtatni az érvényesítési problémákat.
    + A rögzített register_model sikeres lesz, még akkor is, ha a modell környezete hiányzik a függőségek helyileg.
    + Kijavított egy hibát, amelyben egyes távoli futtatások nem engedélyezettek a Docker-ben.
    + Adja hozzá a kivétel naplózását, amely a helyi futtatást okozza idő előtt.
  + **azureml-Train-Core**
    + Gondolja át, hogy resume_from fut-e az automatikus hiperparaméter hangolásának legjobb alárendelt futtatásainak kiszámításakor.
  + **azureml – folyamat – mag**
    + Rögzített paraméterek feldolgozása a folyamat argumentumának kialakításakor.
    + Hozzáadott folyamat leírása és a lépés típusa YAML paraméter.
    + Új YAML-formátum a folyamat lépéseihez és a régi formátumra vonatkozó elavult figyelmeztetés hozzáadása.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Webes felület

Az együttműködésen alapuló munkaterület kezdőlapja [https://ml.azure.com](https://ml.azure.com) bővült, és a Azure Machine learning Studio (előzetes verzió) néven lett feldolgozva.

A studióból betaníthatja, tesztelheti, üzembe helyezheti és kezelheti Azure Machine Learning adategységeket, például adatkészleteket, folyamatokat, modelleket, végpontokat és sok más eszközt.

A Studio alkalmazásban a következő webalapú szerzői eszközöket érheti el:

| Webalapú eszköz | Leírás | Kiadás |
|-|-|-|
| Notebook VM (előzetes verzió) | Teljes körűen felügyelt felhőalapú munkaállomás | Alapszintű & Enterprise |
| [Automatikus gépi tanulás](tutorial-first-experiment-automated-ml.md) (előzetes verzió) | Nincs programkód a gépi tanulási modellek fejlesztésének automatizálásához | Enterprise |
| [Designer](concept-designer.md) (előzetes verzió) | A korábban a tervezőnek nevezett gépi tanulási modellezési eszköz húzása és eltávolítása | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Azure Machine Learning Designer fejlesztései

+ Korábbi nevén vizuális felület 
+   11 új [modul](algorithm-module-reference/module-reference.md) , beleértve az ajánlókat, az osztályozók és az oktatási segédeszközöket, beleértve a szolgáltatások mérnöki, több ellenőrzési és Adatátalakítási funkcióját.

### <a name="r-sdk"></a>R SDK 
 
Az adatszakértők és a mesterséges intelligencia-fejlesztők a [Azure Machine learning SDK for R](tutorial-1st-r-experiment.md) használatával hozhatnak létre és futtathatnak gépi tanulási munkafolyamatokat Azure Machine learning.

Az R Azure Machine Learning SDK a `reticulate` csomagot használja a Python SDK-hoz való kötéshez. A Pythonhoz való közvetlen kötéssel az R SDK lehetővé teszi, hogy a Python SDK-ban megvalósított alapvető objektumokhoz és metódusokhoz hozzáférjen a kiválasztott R-környezetből.

Az SDK főbb képességei a következők:

+   Felhőbeli erőforrások kezelése a gépi tanulási kísérletek monitorozásához, naplózásához és rendszerezéséhez.
+   Modellek betanítása felhőalapú erőforrásokkal, beleértve a GPU-gyorsított Modelles képzést.
+   A modelleket webszolgáltatásként helyezheti üzembe Azure Container Instances (ACI) és az Azure Kubernetes Service (ak) szolgáltatásban.

A teljes dokumentációért tekintse meg a [csomag webhelyét](https://azure.github.io/azureml-sdk-for-r) .

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning integráció a Event Grid 

A Azure Machine Learning mostantól a Event Grid erőforrás-szolgáltatója, a Azure Portal vagy az Azure CLI használatával konfigurálhatja a gépi tanulási eseményeket. A felhasználók létrehozhatnak eseményeket a Futtatás befejezésére, a modell regisztrálására, a modell üzembe helyezésére és az adateltolódás észlelésére. Ezeket az eseményeket a Event Grid által támogatott eseménykezelők számára lehet átirányítani. További részletekért tekintse meg a Machine learning-esemény [sémáját](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning), a [fogalmakat](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration) és az [oktatóanyagokat](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid) ismertető cikket.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK a Python v 1.0.72

+ **Új funkciók**
  + A [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift) csomag segítségével hozzáadta az adatkészlet-figyelőket, ami lehetővé teszi, hogy az idősorozat-adatkészleteket az adateltolódásra vagy más statisztikai változásokra időben figyelje. A riasztások és események akkor indíthatók el, ha a rendszer a drift észlelését vagy az adatfeldolgozás egyéb feltételeit is kielégíti. A részletekért tekintse meg [a dokumentációt](https://aka.ms/datadrift) .
  + Két új kiadás bejelentése (más néven SKU szinonimaként) a Azure Machine Learningban. Ezzel a kiadással már létrehozhat egy alapszintű vagy egy vállalati Azure Machine Learning munkaterületet is. Minden meglévő munkaterület Alapértelmezésben az alapszintű kiadás lesz, és a munkaterületet bármikor frissítheti a Azure Portal vagy a studióba. Létrehozhat egy alapszintű vagy egy vállalati munkaterületet is a Azure Portal. További információért olvassa el [a dokumentációt](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) . Az SDK-ból a munkaterület kiadása a munkaterület objektum "SKU" tulajdonságának használatával határozható meg.
  + Továbbfejlesztettük a Azure Machine Learning számítást is – mostantól megtekintheti a fürtök metrikáit (például az összes csomópontot, a csomópontokat, a teljes kvótát) a Azure Monitorban, a diagnosztikai naplók megtekintése mellett a hibakereséshez. Emellett az aktuálisan futó vagy várólistán lévő futtatásokat is megtekintheti a fürtön, valamint a fürt különböző csomópontjainak IP-címeit. Ezeket a portálon vagy az SDK-ban vagy a CLI-ben a megfelelő függvények használatával tekintheti meg.

  + **Előzetes verziójú funkciók**
    + Az előzetes verzió támogatja a helyi SSD lemezek titkosítását Azure Machine Learning számításban. Ha ezt a funkciót szeretné használni, vegyen fel egy technikai támogatási jegyet az előfizetése engedélyezési listára.
    + Azure Machine Learning batch-következtetés nyilvános előzetes verziója. Azure Machine Learning batch-következtetések olyan nagy következtetési feladatokat céloznak meg, amelyek nem időérzékenyek. A Batch-következtetések költséghatékony következtetéseket biztosítanak, és az aszinkron alkalmazások esetében páratlan átviteli sebességgel rendelkeznek. Nagy adatátviteli sebességre van optimalizálva, és nagy adatgyűjtési feladatokhoz is felejtsd el.
    + [**azureml – adathalmaz**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + A címkézett adatkészlet funkcióinak engedélyezése
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Hibajavítások és javítások**
  + **Azure-CLI-ml**
    + A CLI mostantól támogatja a modell csomagolását.
    + Adatkészlet hozzáadva a CLI-hez. További információ: `az ml dataset --help`
    + Támogatott modellek (ONNX, scikit-Learn és TensorFlow) üzembe helyezésének és csomagolásának támogatása InferenceConfig-példány nélkül.
    + A szolgáltatás üzembe helyezésének (ACI és ak) felülírási jelzője hozzáadva az SDK-ban és a CLI-ben. Ha meg van jelölve, a felülírja a meglévő szolgáltatást, ha már létezik ilyen nevű szolgáltatás. Ha a szolgáltatás nem létezik, új szolgáltatást fog létrehozni.
    + A modellek regisztrálása két új keretrendszerrel, Onnx és Tensorflow lehetséges. -A modell regisztrációja fogadja a minta bemeneti adatokat, minta kimeneti adatokat és erőforrás-konfigurációt a modellhez.
  + **azureml-automl-Core**
    + Az iterációk betanítása csak akkor fut egy alárendelt folyamatban, ha a futásidejű megkötések be vannak állítva.
    + Az előrejelzési feladatokhoz hozzáadott egy Guardrail, amellyel ellenőrizhető, hogy egy adott max_horizon okozhat-e memóriát a megadott gépen. Ha ez megtörténik, egy Guardrail üzenet jelenik meg.
    + Többek között az összetett gyakoriságok támogatása, például 2 év és 1 hónap. – Új, érthető hibaüzenet, ha a gyakoriság nem határozható meg.
    + Azureml hozzáadása – alapértelmezés szerint automatikusan generált Conda env a modell telepítési hibájának megoldása érdekében
    + Azure Machine Learning folyamat közbenső adatfolyamatainak átalakítása táblázatos adatkészletbe, és a `AutoMLStep`használata.
    + Megvalósított oszlop céljának frissítése a folyamatos átvitelhez.
    + Megvalósított transzformátor-paraméterek frissítése az imputált és a HashOneHotEncoder a streaminghez.
    + Az aktuális adatméretet és a minimálisan szükséges adatméretet adta hozzá az érvényesítési hibaüzenetekhez.
    + A több ellenőrzés minimálisan szükséges adatméretének frissítése az egyes ellenőrzési sorokban legalább két minta biztosításához.
  + **azureml – CLI – gyakori**
    + A CLI mostantól támogatja a modell csomagolását.
    + A modellek regisztrálása két új keretrendszerrel, Onnx és Tensorflow lehetséges.
    + A modell regisztrációja fogadja a minta bemeneti adatokat, a modell kimeneti adatait és erőforrás-konfigurációját.
  + **azureml – gbdt**
    + a notebook kiadási csatornájának kijavítása
    + Figyelmeztetés hozzáadva a nem AmlCompute számítási célhoz, amelyet nem támogatunk
    + LightGMB-kalkulátor hozzáadva a azureml-gbdt csomaghoz
  + [**azureml – mag**](https://docs.microsoft.com/python/api/azureml-core)
    + A CLI mostantól támogatja a modell csomagolását.
    + Elavult figyelmeztetés hozzáadása az elavult adatkészlet API-khoz. Lásd: adathalmaz API-változási megjegyzése https://aka.ms/tabular-dataset.
    + Ha az adatkészlet regisztrálva van, módosítsa [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) a regisztrációs név és a verzió visszaküldéséhez.
    + Javítsa ki azt a hibát, amelyet a ScriptRunConfig az adatkészletben argumentummal nem lehet ismételten használni a kísérlet futtatásának elküldéséhez.
    + A Futtatás során lekért adatkészleteket a rendszer nyomon követi, és megtekintheti a Futtatás részletei lapon, vagy meghívja a [`run.get_details()`t](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) a Futtatás befejezése után.
    + Azure Machine Learning folyamat közbenső adatfolyamatainak átalakítása táblázatos adatkészletbe, és a [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep)használata.
    + Támogatott modellek (ONNX, scikit-Learn és TensorFlow) üzembe helyezésének és csomagolásának támogatása InferenceConfig-példány nélkül.
    + A szolgáltatás üzembe helyezésének (ACI és ak) felülírási jelzője hozzáadva az SDK-ban és a CLI-ben. Ha meg van jelölve, a felülírja a meglévő szolgáltatást, ha már létezik ilyen nevű szolgáltatás. Ha a szolgáltatás nem létezik, új szolgáltatást fog létrehozni.
    +  A modellek regisztrálása két új keretrendszerrel, Onnx és Tensorflow lehetséges. A modell regisztrációja fogadja a minta bemeneti adatokat, a modell kimeneti adatait és erőforrás-konfigurációját.
    + Új adattár lett hozzáadva a Azure Database for MySQLhoz. Új példa a Azure Database for MySQL DataTransferStep való használatára Azure Machine Learning folyamatokban.
    + Hozzáadott funkciók a címkék hozzáadásához és eltávolításához a-kísérletek hozzáadott funkcióival a címkék a futtatásokból való eltávolításához
    + A szolgáltatás üzembe helyezésének (ACI és ak) felülírási jelzője hozzáadva az SDK-ban és a CLI-ben. Ha meg van jelölve, a felülírja a meglévő szolgáltatást, ha már létezik ilyen nevű szolgáltatás. Ha a szolgáltatás nem létezik, új szolgáltatást fog létrehozni.
  + [**azureml – datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Áthelyezve a `azureml-contrib-datadrift`ból a `azureml-datadrift`ba
    + Az idősorozat-adatkészletek figyelésének támogatása a drift és más statisztikai intézkedések esetében
    + Az új metódusok `create_from_model()` és `create_from_dataset()` a [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) osztályhoz. A `create()` metódus elavulttá válik.
    + A Pythonban és a felhasználói felületen lévő vizualizációk módosításai a Azure Machine Learning Studióban.
    + A heti és havi monitorozási ütemezés támogatása a napi adatkészlet-figyelők mellett.
    + Az adatfigyelő metrikáinak backfill támogatása az adatkészlet-figyelők korábbi adatainak elemzéséhez.
    + Különböző hibajavítások
  + [**azureml – folyamat – mag**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + a azureml-adatelőkészítés már nincs szükség ahhoz, hogy beküldjön egy Azure Machine Learning folyamat futtatását a folyamat `yaml` fájlból.
  + [**azureml-Train-automl**](/python/api/azureml-train-automl-runtime/)
    + Azureml hozzáadása – alapértelmezés szerint automatikusan generált Conda env a modell telepítési hibájának megoldása érdekében
    + A AutoML Remote Training mostantól tartalmazza a azureml-alapértékeket, amelyek lehetővé teszik a betanítási env használatának újbóli használatát.
  + **azureml-Train-Core**
    + PyTorch 1,3 támogatás hozzáadva [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) kalkulátorban

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Vizuális felület (előzetes verzió)

+ A Azure Machine Learning Visual Interface (előzetes verzió) [Azure Machine learning folyamatokon](concept-ml-pipelines.md)való futtatásra lett kidolgozva. A vizuális felületen létrehozott folyamatok (korábbi néven kísérletek) mostantól teljes mértékben integrálva vannak az alapvető Azure Machine Learning felülettel.
  + Egységes felügyeleti élmény az SDK-eszközökkel
  + A Visual Interface-modellek,-folyamatok és-végpontok verziószámozása és nyomon követése
  + Újratervezett felhasználói felület
  + A Batch következtetési központi telepítése hozzáadva
  + Az Azure Kubernetes Service (ak) támogatása a következtetésekhez kapcsolódó számítási célokhoz
  + Új Python-Step folyamat-létrehozási munkafolyamat
  + A Visual authoring Tools új [kezdőlapja](https://ml.azure.com)

+ **Új modulok**
  + Matematikai művelet alkalmazása
  + SQL-transzformáció alkalmazása
  + Klip értékei
  + Az adatösszesítés
  + Importálás az SQL Database-ből

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK a Python v 1.0.69

+ **Hibajavítások és javítások**
  + **azureml-automl-Core**
    + A modell magyarázatait a legjobb futtatásra korlátozza, és nem kell minden futtatáskor számítási magyarázatot felvennie. Ez a viselkedés a helyi, a távoli és az ADB esetében is változhat.
    + Igény szerinti modellhez kapcsolódó magyarázatok támogatása a felhasználói felületen
    + A psutil hozzáadva `automl` függőségként, és a psutil Conda-függőségként szerepel a amlcompute-ben.
    + Az előrejelzési adatkészletek esetében a heurisztikus késéssel és a gördülő ablak méretével kapcsolatos probléma kijavítva egy sor, ami lineáris algebra-hibákhoz vezethet
      + Kinyomtatva az előrejelzési futtatások heurisztikus meghatározású paramétereinek kinyomtatása.
  + **azureml – datadrift**
    + A védelem a kimeneti metrikák létrehozásakor lett hozzáadva, ha az adatkészletek szintjének eltolódása nem az első szakaszban található.
  + **azureml – összetétel – értelmezés**
    + azureml – összevont – magyarázat – a modell csomagja át lett nevezve a azureml-retribal-értelmezze
  + **azureml – mag**
    + API hozzáadása az adatkészletek regisztrációjának megszüntetéséhez. `dataset.unregister_all_versions()`
    + azureml – a (z)-detörzsi-magyarázat-Model csomag át lett nevezve a azureml-retribal-értelmezze névre.
  + **[azureml – mag](https://docs.microsoft.com/python/api/azureml-core)**
    + API hozzáadása az adatkészletek regisztrációjának megszüntetéséhez. adatkészlet. [unregister_all_versions ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Az adatkészlet API-ját hozzáadta az adatváltozások időpontjához. `dataset.data_changed_time`.
    + Felhasználható `FileDataset` és `TabularDataset` bemenetként a `PythonScriptStep`, a `EstimatorStep`és a `HyperDriveStep` a folyamatokban
    + A `FileDataset.mount` teljesítménye a nagy mennyiségű fájllal rendelkező mappák esetében javult
    + A [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) és a [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) bemenetként való felhasználása a [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), a [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)és a [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) számára a Azure Machine learning folyamat során.
    + A FileDataset teljesítménye. a [Mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none-) szolgáltatás a nagy mennyiségű fájllal rendelkező mappák esetében javult
    + A Futtatás részleteiben szereplő ismert hibajelentések URL-címe hozzáadva.
    + Hiba történt a Futtatás közben. get_metrics, ahol a kérelmek sikertelenek lesznek, ha egy Futtatás túl sok gyermeket tartalmaz
    + Hiba történt a [Futtatás közben. get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) , ahol a kérelmek sikertelenek lesznek, ha egy Futtatás túl sok gyermeket tartalmaz
    + Támogatás hozzáadva az Arcadia-fürtön történő hitelesítéshez.
    + A kísérlet objektum létrehozása vagy létrehozása a kísérletet a Azure Machine Learning munkaterületen a futtatási előzmények nyomon követéséhez. A kísérlet azonosítója és az archivált idő a létrehozáskor a kísérlet objektumban van feltöltve. Példa: Experiment = Experiment (munkaterület, "New Experiment") experiment_id = experiment.id Archive () és reactivate () függvények, amelyek meghívhatók egy kísérletre, hogy elrejtsék és visszaállítsák a kísérletet az UX-ben, vagy alapértelmezés szerint visszaadott hívás a kísérletek listázásához. Ha egy új kísérlet ugyanazzal a névvel lett létrehozva, mint az archivált kísérlet, az újraaktiváláskor átnevezheti az archivált kísérletet egy új név átadásával. Egy adott névvel csak egy aktív kísérlet lehet. Például: experiment1 = Experiment (munkaterület, "Active Experiment") experiment1. Archive () # hozzon létre új aktív kísérletet ugyanazzal a névvel, mint az archivált. experiment2. = Kísérlet (munkaterület, "aktív kísérlet") experiment1. reactivate (new_name = "előző aktív kísérlet") a kísérlet során a statikus metódusok listája () is megteheti a nevet és a nézet típusa szűrőt. A nézet típusa értékek: "ACTIVE_ONLY", "ARCHIVED_ONLY" és "ALL" Példa: archived_experiments = Experiment. list (munkaterület, view_type = "ARCHIVED_ONLY") all_first_experiments = kísérlet. list (munkaterület, név = "első kísérlet", view_type = "ALL")
    + A környezet támogatása a modell üzembe helyezéséhez és a szolgáltatás frissítéséhez
  + **azureml – datadrift**
    + A DataDriftDector osztály show attribútuma nem támogatja többé a következő opcionális argumentumot: "with_details". A show attribútum csak az adateltolódási együtthatót és a szolgáltatás oszlopainak adateltolódási hozzájárulását mutatja.
    + A (z) "get_output" DataDriftDetector attribútum változásai:
      + A (z) start_time bemeneti paraméter nem kötelező, hanem end_time opcionális.
      + Ha a megadott start_time és/vagy end_time egy adott meghívásban meghatározott run_id adott meg, akkor az érték hiba miatt kivételt eredményez, mivel kölcsönösen kizárják egymást
      + A megadott start_time és/vagy end_time alapján csak az ütemezett futtatások eredményei lesznek visszaadva.
      + A következő paraméter elavult: "daily_latest_only".
    + Támogatja az adatkészlet-alapú adateltolódások kimenetének beolvasását.
  + **azureml – magyarázat – modell**
    + Átnevezi a AzureML-magyarázza-Model csomagot a AzureML-értelmezésre, így a régi csomagot a visszamenőleges kompatibilitás érdekében megtarthatja
    + kijavítva `automl` hiba a RAW-magyarázatokkal a besorolási feladatra vonatkozóan a regresszió helyett alapértelmezetten a ExplanationClient
    + `ScoringExplainer`-támogatás hozzáadása közvetlenül a `MimicWrapper` használatával
  + **azureml – folyamat – mag**
    + Nagyobb teljesítmény a nagy adatcsatornák létrehozásakor
  + **azureml-Train-Core**
    + TensorFlow 2,0-támogatás hozzáadva a TensorFlow kalkulátorhoz
  + **azureml-Train-automl**
    + A kísérlet [objektum létrehozása](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) vagy létrehozása a kísérletet a Azure Machine learning munkaterületen a futtatási előzmények nyomon követéséhez. A kísérlet azonosítója és az archivált idő a létrehozáskor a kísérlet objektumban van feltöltve. Példa:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        az [archiválás ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) és az [újraaktiválás ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) olyan függvények, amelyek meghívhatók egy kísérletre, hogy elrejtsék és visszaállítsák a kísérletet az UX-ben, vagy alapértelmezés szerint visszaadja azokat a kísérletek listájának hívásakor. Ha egy új kísérlet ugyanazzal a névvel lett létrehozva, mint az archivált kísérlet, az újraaktiváláskor átnevezheti az archivált kísérletet egy új név átadásával. Egy adott névvel csak egy aktív kísérlet lehet. Példa:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        A kísérlet során a statikus metódusok [listája ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) a név szűrőjét és a nézet típusa szűrőt is elvégezheti. A nézet típusa értékei a következők: "ACTIVE_ONLY", "ARCHIVED_ONLY" és "ALL". Példa:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + A környezet támogatása a modell üzembe helyezéséhez és a szolgáltatás frissítéséhez.
  + **[azureml – datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + A [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) osztály show attribútuma nem támogatja többé a következő opcionális argumentumot: "with_details". A show attribútum csak az adateltolódási együtthatót és a szolgáltatás oszlopainak adateltolódási hozzájárulását mutatja.
    + A DataDriftDetector függvény [get_output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) viselkedési változások:
      + A (z) start_time bemeneti paraméter nem kötelező, hanem end_time opcionális.
      + A megadott start_time és/vagy end_time egy adott meghíváshoz tartozó konkrét run_id megadása esetén a rendszer hibát eredményez, mivel ezek kölcsönösen kizárják egymást.
      + A megadott start_time és/vagy end_time alapján csak az ütemezett futtatások eredményei lesznek visszaadva.
      + A következő paraméter elavult: "daily_latest_only".
    + Támogatja az adatkészlet-alapú adateltolódások kimenetének beolvasását.
  + **[azureml – magyarázat – modell](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Átnevezi a AzureML-magyarázza-Model csomagot a AzureML-értelmezésre, így a régi csomag visszafelé kompatibilis marad.
    + rögzített AutoML hiba a RAW-magyarázatokkal a besorolási feladathoz a ExplanationClient-ből való letöltéskor alapértelmezetten nem regressziós.
    + A [MimicWrapper](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper) használatával közvetlenül létrehozandó [ScoringExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.scoring.scoring_explainer.scoringexplainer) -támogatás hozzáadása
  + **[azureml – folyamat – mag](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Nagyobb teljesítmény a nagy adatcsatornák létrehozásához.
  + **[azureml-Train-Core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + TensorFlow 2,0 támogatás hozzáadva a [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) kalkulátorhoz.
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + A szülő futtatása nem fog sikerülni, ha a telepítés iterációja meghiúsult, mivel a folyamat már gondoskodik róla.
    + Helyi Docker-és helyi Conda-támogatás hozzáadva a AutoML-kísérletekhez
    + Helyi Docker-és helyi Conda támogatása a AutoML-kísérleteknél.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Új webes felület (előzetes verzió) Azure Machine Learning munkaterületekhez

Frissült az [Új munkaterület-portál](https://ml.azure.com) Experiment (kísérlet) lapja, így az adatszakértők több módon is megfigyelheti a kísérleteket. A következő funkciók közül választhat:
+ Kísérletezzen a metaadatokkal, így egyszerűen szűrheti és rendezheti a kísérletek listáját
+ Egyszerűsített és elvégezhető kísérlet részleteinek oldalai, amelyek lehetővé teszik a futtatások megjelenítését és összehasonlítását
+ Új kialakítás a részletek oldalain a képzések megismeréséhez és figyeléséhez

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK a Python v 1.0.65

  + **Új funkciók**
    + Válogatott környezetek lettek hozzáadva. Ezek a környezetek előre konfigurálva vannak könyvtárakkal a gyakori gépi tanulási feladatokhoz, és a gyorsabb végrehajtás érdekében Docker-rendszerképekként előre felépítve és gyorsítótárazva lettek. Alapértelmezés szerint a munkaterület "AzureML" előtagú környezetének listájában jelennek meg.
    + Válogatott környezetek lettek hozzáadva. Ezek a környezetek előre konfigurálva vannak könyvtárakkal a gyakori gépi tanulási feladatokhoz, és a gyorsabb végrehajtás érdekében Docker-rendszerképekként előre felépítve és gyorsítótárazva lettek. Alapértelmezés szerint a [munkaterület](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)"AzureML" előtagú környezetének listájában jelennek meg.

  + **azureml-Train-automl**
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + A ONNX-átalakítás támogatása az ADB és a HDI esetében

+ **Előzetes verziójú funkciók**
  + **azureml-Train-automl**
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + Támogatott BERT és BiLSTM as text Képtulajdonság (csak előzetes verzió)
    + Az oszlop céljának és a transzformátor paramétereinek támogatott featurization testreszabása (csak előzetes verzió)
    + Támogatott nyers magyarázatok, ha a felhasználó a betanítás során engedélyezi a modell magyarázatát (csak előzetes verzió)
    + Próféta hozzáadva a `timeseries`-előrejelzéshez betanítható folyamatként (csak előzetes verzió)

  + **azureml – datadrift**
    + Csomagok áthelyezve a azureml-datadrift-azureml-datadrift; a `contrib` csomag el lesz távolítva egy későbbi kiadásban

+ **Hibajavítások és javítások**
  + **azureml-automl-Core**
    + Bevezette a FeaturizationConfig a AutoMLConfig és a AutoMLBaseSettings
    + Bevezette a FeaturizationConfig a [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) és a AutoMLBaseSettings
      + A Featurization oszlopának felülbírálása a megadott oszlop és szolgáltatás típusával
      + A transzformátor paramétereinek felülbírálása
    + Elavult üzenet hozzáadva a explain_model () és a retrieve_model_explanations () rendszerhez
    + Próféta hozzáadva betanítható folyamatként (csak előzetes verzió)
    + Elavult üzenet lett hozzáadva a explain_model () és a retrieve_model_explanations () rendszerhez.
    + Próféta hozzáadva betanítható folyamatként (csak előzetes verzió).
    + A cél késések automatikus észlelésének támogatása, a gördülő ablak mérete és a maximális horizont. Ha target_lags, target_rolling_window_size vagy max_horizon az "Auto" értékre van állítva, a rendszer a heurisztikus értékeket a betanítási adat alapján a megfelelő paraméter értékének becslésére fogja alkalmazni.
    + Rögzített előrejelzés abban az esetben, ha az adathalmaz egy gabona oszlopot tartalmaz, ez a gabona numerikus típusú, és a vonat és a tesztelési készlet közötti hézag van.
    + Kijavítva a duplikált indextel kapcsolatos hibaüzenet a távoli Futtatás előrejelzési feladatokban
    + Rögzített előrejelzés abban az esetben, ha az adathalmaz egy gabona oszlopot tartalmaz, a gabona numerikus típusú, és a vonat és a tesztelési készlet között hiányzik a különbség.
    + Kijavítva a duplikált indexre vonatkozó hibaüzenetet a távoli Futtatás előrejelzési feladatokban.
    + Egy Guardrail hozzáadva annak ellenőrzését, hogy az adatkészlet kiegyensúlyozatlan vagy sem. Ha igen, a rendszer egy Guardrail üzenetet ír a konzolra.
  + **azureml – mag**
    + Lehetőség van az SAS URL-címének lekérésére a Storage-ban a Model objektumon keresztül. Pl.: Model. get_sas_url ()
    + `run.get_details()['datasets']` bevezetése az elküldött futtatáshoz társított adatkészletek lekéréséhez
    + API-`Dataset.Tabular.from_json_lines_files` hozzáadása TabularDataset létrehozásához JSON-sorok fájljaiból. A TabularDataset JSON-sorok fájljaiban található táblázatos adatokat a dokumentációban találhatja meg https://aka.ms/azureml-data.
    + További virtuálisgép-méret mezők (operációsrendszer-lemez, GPU-k száma) lettek hozzáadva a supported_vmsizes () függvényhez
    + További mezők lettek hozzáadva a list_nodes () függvényhez a Futtatás, a privát és a nyilvános IP-cím, a port stb. megjelenítéséhez.
    + Új mező megadásának lehetősége a fürt üzembe helyezése során – remotelogin_port_public_access amely engedélyezhető vagy letiltható attól függően, hogy a fürt létrehozásakor nyitva vagy le szeretné-e hagyni az SSH-portot. Ha nem adja meg, a szolgáltatás intelligensen megnyitja vagy lezárta a portot attól függően, hogy egy VNet belül telepíti a fürtöt.
  + **azureml – magyarázat – modell**
  + **[azureml – mag](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Lehetőség van az SAS URL-címének lekérésére a Storage-ban a Model objektumon keresztül. Pl.: modell. [get_sas_url ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Futtassa a parancsot. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)["adatkészletek"] az elküldött futtatáshoz társított adatkészletek lekéréséhez
    + API-`Dataset.Tabular`hozzáadása. [from_json_lines_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) TabularDataset létrehozásához JSON-sorok fájljaiból. A TabularDataset JSON-sorok fájljaiban található táblázatos adatokat a dokumentációban találhatja meg https://aka.ms/azureml-data.
    + További virtuálisgép-méret mezők (operációsrendszer-lemez, GPU-k száma) lettek hozzáadva a [supported_vmsizes ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-) függvényhez
    + További mezők lettek hozzáadva a [list_nodes ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) függvényhez a Futtatás, a privát és a nyilvános IP-cím, a port stb. megjelenítéséhez.
    + Új mező megadásának lehetősége a fürt [üzembe helyezése során `--remotelogin_port_public_access` amely](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) beállítható úgy, hogy engedélyezve vagy Letiltva legyen attól függően, hogy a fürt létrehozásakor megnyitotta vagy lezárta az SSH-portot. Ha nem adja meg, a szolgáltatás intelligensen megnyitja vagy lezárta a portot attól függően, hogy egy VNet belül telepíti a fürtöt.
  + **[azureml – magyarázat – modell](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Továbbfejlesztett dokumentáció a besorolási forgatókönyv magyarázatának eredményeiről.
    + A kiértékelési példákban az előre jelzett y értékek feltöltésére is lehetőség van. További hasznos vizualizációk feloldása.
    + A magyarázatot megadó tulajdonság hozzáadva a MimicWrapper-hez, amely lehetővé teszi az alapul szolgáló MimicExplainer beszerzését.
  + **azureml – folyamat – mag**
    + Hozzáadott jegyzetfüzet a modul, a ModuleVersion és a ModuleStep leírásához
  + **azureml – folyamat – lépések**
    + A RScriptStep hozzáadva a pénzmosás-folyamaton keresztüli R-szkriptek támogatásához.
    + A AzureBatchStep olyan rögzített metaadat-paramétereket elemez, amely a következő hibaüzenetet okozta: "a SubscriptionId paraméterhez nincs megadva a hozzárendelés."
  + **azureml-Train-automl**
    + Támogatott training_data, validation_data, label_column_name, weight_column_name adatbevitel formátuma
    + Elavult üzenet hozzáadva a explain_model () és a retrieve_model_explanations () rendszerhez
  + **[azureml – folyamat – mag](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Hozzáadott egy [jegyzetfüzetet](https://aka.ms/pl-modulestep) a [modul](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)), a [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) és a [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)leírásához.
  + **[azureml – folyamat – lépések](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + A [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) hozzáadva a pénzmosás-folyamaton keresztüli R-szkriptek támogatásához.
    + A [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) -elemzésben rögzített metaadat-paraméterek a következő hibaüzenetet okozzák: "a SubscriptionId paraméter nem lett megadva.
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + Támogatott training_data, validation_data, label_column_name weight_column_name adatbevitel formátuma.
    + Elavult üzenet lett hozzáadva a [explain_model ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) és a [retrieve_model_explanations ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-)rendszerhez.


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK a Python v 1.0.62

+ **Új funkciók**
  + Bevezette a `timeseries`i tulajdonságot a TabularDataset. Ez a funkció lehetővé teszi, hogy a TabularDataset egyszerű időbélyeg-szűrést biztosítson, például egy adott időtartam vagy a legfrissebb adatmennyiség között. Ha többet szeretne megtudni a TabularDataset `timeseries`-tulajdonságáról, látogasson el az https://aka.ms/azureml-data dokumentációba, vagy https://aka.ms/azureml-tsd-notebook egy jegyzetfüzetre.
  + TabularDataset-és FileDataset-alapú betanítás. Egy példa notebookra https://aka.ms/dataset-tutorial tekintse meg a következőt:.

  + **azureml-Train-Core**
    + Hozzáadott `Nccl` és `Gloo` támogatás a PyTorch kalkulátorban

+ **Hibajavítások és javítások**
  + **azureml-automl-Core**
    + A (z) "lag_length" AutoML-beállítás és a LaggingTransformer elavult.
    + A bemeneti adatok helyes érvényesítésének javítása, ha adatfolyam formátumban vannak megadva
    + Módosította a fit_pipeline. a Graph-ot a gráf JSON létrehozásához és az összetevőkre való feltöltéséhez.
    + A diagramot a `userrun` `Cytoscape`használatával jeleníti meg.
  + **azureml – mag**
    + Tekintse át a kivételt az ADB-kódban, és végezze el a módosítást új hibakezelés esetén
    + Automatikus MSI-hitelesítés hozzáadva a notebook virtuális gépekhez.
    + Kijavítja a hibát, ha a hibás újrapróbálkozások miatt a sérült vagy üres modelleket fel lehet tölteni.
    + Kijavítva a hiba, ahol a `DataReference` neve megváltozik a `DataReference` mód változásakor (például `as_upload`, `as_download`vagy `as_mount`meghívásakor).
    + `FileDataset.mount` és `FileDataset.download`esetén `mount_point` és `target_path` megadása nem kötelező.
    + Kivétel az időbélyegző-oszlop nem található, ha az idősoros kapcsolódó API-t a rendszer az időbélyegző oszlop kiosztása nélkül hívja meg, vagy a hozzárendelt időbélyeg-oszlopok el lesznek dobva.
    + Az idősoros oszlopokat olyan oszlophoz kell rendelni, amelynek a típusa dátum, ellenkező esetben a rendszer kivételt vár.
    + A (z) "with_timestamp_columns" API-t hozzárendelő idő-sorozatszámok egyike sem lehet egy értékre vonatkozó apró/durva időbélyeg-oszlop neve, amely törli a korábban hozzárendelt időbélyeg-oszlopokat
    + A kivétel akkor jelenik meg, ha a durva vagy a részletes időbélyeg-oszlop el van dobva, és jelzi, hogy az eldobást követően a felhasználó megszakította az időbélyegző oszlopot a ledobások listájában, vagy meghívja a nem értékkel rendelkező with_time_stamp oszlopok
    + A kivétel akkor jelenik meg, ha a durva vagy a részletes időbélyeg-oszlop nem szerepel az oszlopok megtartása listán, és jelzi, hogy a felhasználó megtarthat egy olyan időbélyegzőt, amely tartalmazza az oszlopok megtartása vagy a hívása with_time_stamp Az időbélyeg-oszlopok kiadásának értéke.
    + Egy regisztrált modell méretének naplózása hozzáadva.
  + **azureml – magyarázat – modell**
    + Rögzített figyelmeztetés kinyomtatva a konzolra, ha a "csomagolás" Python-csomag nincs telepítve: "a lightgbm támogatott verziójának régebbi verziója esetén frissítsen a 2.2.1-nél nagyobb verzióra"
    + Rögzített letöltési modell magyarázata a globális magyarázatok számos funkcióval
    + A rögzített utánozó-magyarázat hiányzó inicializálási példákat tartalmaz a kimeneti magyarázaton
    + Rögzített megváltoztathatatlan hiba történt a tulajdonságok beállításakor, ha két különböző típusú modellt használ a magyarázó ügyféllel való feltöltéshez
    + Egy get_raw paramétert adott hozzá a magyarázathoz. magyarázza el (), hogy az egyik pontozási elmagyarázó a mesterséges és a nyers értékeket is visszaadja.
  + **azureml-Train-automl**
    + Nyilvános API-kat vezetett be a AutoML-ből, hogy támogassa a magyarázatokat `automl` magyarázza az SDK-t – újabb módszer a AutoML-magyarázatok támogatásához a AutoML-featurization leválasztásával és az SDK-val integrált, a azureml magyarázó SDK-k AutoML-modellekkel kapcsolatos,
    + Azureml eltávolítása – Alapértelmezések a távoli képzési környezetekben.
    + A FileCacheStore alapján módosította az alapértelmezett gyorsítótár-tároló helyét, hogy a AzureFileCacheStore egy AutoML Azure Databricks a kód elérési útján.
    + A bemeneti adatok helyes érvényesítésének javítása, ha adatfolyam formátumban vannak megadva
  + **azureml-Train-Core**
    + Source_directory_data_store elavulttá vált.
    + A azureml telepített csomag verzióinak felülbírálásának lehetősége.
    + Docker-támogatás hozzáadva az `environment_definition` paraméterben a becslések-ben.
    + Egyszerűsített elosztott tanítási paraméterek a becslések-ben.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Új webes felület (előzetes verzió) Azure Machine Learning munkaterületekhez
Az új webes felület lehetővé teszi az adatszakértők és adatmérnökök számára, hogy teljes körű gépi tanulási életciklust prepping, és megjelenítsék az adatok megjelenítését a modellek betanításához és üzembe helyezéséhez egyetlen helyen.

![Azure Machine Learning munkaterület felhasználói felülete (előzetes verzió)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Főbb funkciók:**

Az új Azure Machine Learning felület használatával mostantól a következőket teheti:
+ Jegyzetfüzetek kezelése vagy Jupyter
+ [Automatizált ML-kísérletek futtatása](tutorial-first-experiment-automated-ml.md)
+ [Adatkészletek létrehozása helyi fájlokból, adattárokból, & webfájlokból](how-to-create-register-datasets.md)
+ Ismerkedjen meg & adatkészletek előkészítésével a modell létrehozásához
+ A modellek adateltolódásának monitorozása
+ Legutóbbi erőforrások megtekintése az irányítópultról

A jelen kiadás időpontjában a következő böngészők támogatottak: Chrome, Firefox, Safari és Microsoft Edge preview.

**Ismert problémák:**

1. Frissítse a böngészőt, ha "hiba történt". Hiba történt az adathalmaz fájljainak betöltésekor, ha a központi telepítés folyamatban van.

1. Nem lehet törölni vagy átnevezni a fájlt jegyzetfüzetekben és fájlokban. A nyilvános előzetes verzió során a Jupyter felhasználói felületét vagy a terminált használhatja a notebook virtuális gépen a frissítési fájl műveleteinek elvégzéséhez. Mivel ez egy csatlakoztatott hálózati fájlrendszer, a notebook virtuális gépen végzett módosítások azonnal megjelennek a notebook munkaterületen.

1. SSH-t a notebook VM-be:
   1. Keresse meg a virtuális gép beállítása során létrehozott SSH-kulcsokat. Vagy keresse meg a kulcsokat a Azure Machine Learning munkaterületen > nyissa meg a számítási lapot, > keresse meg a jegyzetfüzet virtuális gépet a listában > nyissa meg a tulajdonságokat: másolja a kulcsokat a párbeszédpanelről.
   1. Importálja ezeket a nyilvános és titkos SSH-kulcsokat a helyi gépre.
   1. Az SSH-t a notebook VM-be használhatja.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK a Python v 1.0.60

+ **Új funkciók**
  + Bevezetett FileDataset, amely egy vagy több fájlra hivatkozik az adattárban vagy a nyilvános URL-címeken. A fájlok formátuma bármilyen lehet. A FileDataset lehetővé teszi a fájlok letöltését vagy csatlakoztatását a számítási feladatokhoz. Ha szeretne többet megtudni a FileDataset, látogasson el https://aka.ms/file-dataset.
  + Hozzáadott folyamat YAML-támogatás a PythonScript lépéshez, ADla lépés, Databricks lépés, DataTransferStep és AzureBatch lépés

+ **Hibajavítások és javítások**
  + **azureml-automl-Core**
    + A AutoArima mostantól az előzetes verzióra vonatkozó, javasolt folyamat.
    + Továbbfejlesztett hibajelentés az előrejelzéshez.
    + Az előrejelzési feladatokban az általános helyett egyéni kivételek használatával javította a naplózást.
    + Az ellenőrzési max_concurrent_iterations nem lehet kisebb, mint az ismétlések teljes száma.
    + A AutoML modellek mostantól AutoMLExceptions adnak vissza
    + Ez a kiadás javítja az automatizált gépi tanulás helyi futtatásának végrehajtási teljesítményét.
  + **azureml – mag**
    + Adja meg a DataSet. get_all (munkaterület) bevezetését, amely a regisztrációs nevük alapján `TabularDataset` és `FileDataset` objektumokból álló szótárt ad vissza.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + A `Dataset.Tabular.from_delimited_files` és a `Dataset.Tabular.from_parquet.files``parition_format`ként be kell vezetnie az argumentumot. Az egyes adatútvonalak partíciós adatait a rendszer a megadott formátum alapján oszlopokra bontja ki. a (z) "{column_name}" karakterlánc-oszlopot hoz létre, és a (z) {column_name: ÉÉÉÉ/HH/NN/HH/PP/SS} létrehoz datetime oszlopot, ahol az "éééé", a "hh", a "HH", a "mm" és az "SS A partition_format a fájl elérési útjának végéig kell kezdenie az első partíciós kulcs pozíciójában. Például a következő elérési út miatt: ".. /USA/2019/01/01/data.csv ", ahol a partíció ország és idő szerint van, partition_format ="/{Country}/{PartitionDate: éééé/hh/nn}/Value. csv "karakterláncot hoz létre az" ország "értékkel, amelynek értéke" USA ", a DateTime oszlop pedig" PartitionDate ", értéke" 2019-01-01 ".
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + A `Dataset.Tabular.from_delimited_files` és a `Dataset.Tabular.from_parquet.files``partition_format`ként be kell vezetnie az argumentumot. Az egyes adatútvonalak partíciós adatait a rendszer a megadott formátum alapján oszlopokra bontja ki. a (z) "{column_name}" karakterlánc-oszlopot hoz létre, és a (z) {column_name: ÉÉÉÉ/HH/NN/HH/PP/SS} létrehoz datetime oszlopot, ahol az "éééé", a "hh", a "HH", a "mm" és az "SS A partition_format a fájl elérési útjának végéig kell kezdenie az első partíciós kulcs pozíciójában. Például a következő elérési út miatt: ".. /USA/2019/01/01/data.csv ", ahol a partíció ország és idő szerint van, partition_format ="/{Country}/{PartitionDate: éééé/hh/nn}/Value. csv "karakterláncot hoz létre az" ország "értékkel, amelynek értéke" USA ", a DateTime oszlop pedig" PartitionDate ", értéke" 2019-01-01 ".
    + `to_csv_files` és `to_parquet_files` metódusok lettek hozzáadva a `TabularDataset`hoz. Ezek a módszerek lehetővé teszik a `TabularDataset` és a `FileDataset` közötti átalakítást, ha az adott formátumba konvertálja az adatfájlokat.
    + Automatikusan bejelentkezik az alaprendszerkép beállításjegyzékbe a Model. package () által generált Docker mentésekor.
    + a (z) "gpu_support" már nem szükséges; A pénzmosás mostantól automatikusan észleli és használja az NVIDIA Docker-bővítményt, ha elérhető. Egy későbbi kiadásban el lesz távolítva.
    + Támogatás hozzáadva a PipelineDrafts létrehozásához, frissítéséhez és használatához.
    + Ez a kiadás javítja az automatizált gépi tanulás helyi futtatásának végrehajtási teljesítményét.
    + A felhasználók a futtatási előzmények alapján tudják lekérdezni a metrikákat.
    + Az előrejelzési feladatokban az általános helyett egyéni kivételek használatával javította a naplózást.
  + **azureml – magyarázat – modell**
    + Feature_maps paraméter hozzáadva az új MimicWrapper, amely lehetővé teszi a felhasználók számára a nyers funkciók magyarázatának beolvasását.
    + Az adatkészlet-feltöltések mostantól alapértelmezés szerint ki vannak kapcsolva a magyarázat feltöltéséhez, és újra engedélyezhetők upload_datasets = true értékkel
    + A rendszer hozzáadta a "is_law" paramétert a magyarázatok listájához és a letöltési függvényekhez.
    + A a globális és a helyi magyarázó objektumokhoz is `get_raw_explanation(feature_maps)` metódust ad hozzá.
    + Hozzáadott verzió-ellenőrzési lightgbm a nyomtatott figyelmeztetéssel, ha az alábbi támogatott verzió
    + Optimalizált memória használata
    + A AutoML modellek mostantól AutoMLExceptions adnak vissza
  + **azureml – folyamat – mag**
    + A PipelineDrafts létrehozásához, frissítéséhez és használatához hozzáadva támogatott a megváltoztathatatlan folyamat-definíciók fenntartása és interaktív módon történő futtatása
  + **azureml-Train-automl**
    + A szolgáltatás a GPU-kompatibilis pytorch v 1.1.0, :::no-loc text="cuda"::: Toolkit 9,0, pytorch-Transformers adott verzióinak telepítéséhez szükséges, amely a BERT/XLNet távoli Python futásidejű környezetében való engedélyezéséhez szükséges.
  + **azureml-Train-Core**
    + Néhány hiperparaméter-hely definíciós hibájának korai meghibásodása közvetlenül az SDK-ban a kiszolgálóoldali helyett.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.14
+ **Hibajavítások és javítások**
  + A ADLS/ADLSGen2 írásának engedélyezése a nyers elérési út és a hitelesítő adatok használatával.
  + Kijavított egy olyan hibát, amely miatt a `include_path=True` nem működik `read_parquet`hoz.
  + Rögzített `to_pandas_dataframe()` hiba történt a következő kivétel miatt: "Érvénytelen tulajdonságérték: hostSecret".
  + Kijavítva egy hiba, ahol a DBFS Spark módban nem olvashatók a fájlok.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK a Python v 1.0.57
+ **Új funkciók**
  + Az engedélyezett `TabularDataset` a AutomatedML által felhasználható. Ha többet szeretne megtudni a `TabularDataset`ről, tekintse meg a https://aka.ms/azureml/howto/createdatasets.

+ **Hibajavítások és javítások**
  + **automl-Client-Core-nativeclient**
    + Kijavítva a hiba, amely akkor következik be, amikor a betanítási és/vagy ellenőrzési címkéket (y és y_valid) a pandák dataframe, de nem NumPy tömb formájában adták meg.
    + Frissített illesztőfelület `RawDataContext` létrehozásához, hogy csak az és a `AutoMLBaseSettings` objektumra legyen szükség.
    +  Annak engedélyezése, hogy a AutoML-felhasználók eldobják a betanítási sorozatot, amely nem elég hosszú az előrejelzés során. – Lehetővé teszi, hogy a AutoML-felhasználók eldobják azokat a teszt-készletből, amelyek nem szerepelnek az előrejelzés során a betanítási készletben.
  + **Azure-CLI-ml**
    + Most már frissítheti a Microsoft által generált és az ügyfél-tanúsítványhoz az AK-fürtökön üzembe helyezett pontozási végpont SSL-tanúsítványát is.
  + **azureml-automl-Core**
    + Javítva lett egy probléma a AutoML, ahol a hiányzó címkékkel rendelkező sorok nem lettek megfelelően eltávolítva.
    + Javított hibák naplózása a AutoML-ben; a rendszer mindig a naplófájlba írja a teljes hibaüzeneteket.
    + A AutoML frissítette a csomagjának rögzítését `azureml-defaults`, `azureml-explain-model`és `azureml-dataprep`belefoglalásához. A AutoML nem fog figyelmeztetni a csomagok eltérésére (kivéve `azureml-train-automl` csomagot).
    + Kijavított egy problémát a `timeseries`ban, ahol az önéletrajz felosztása nem egyenlő, ami miatt a Raktárhelyek kiszámítása sikertelen lesz.
    + Ha a többellenőrzéses képzéshez a teljes adatkészletre betanított modellek letöltése során nem sikerül a Ensemble-iteráció futtatása, a modell súlyozása és a szavazásban bekövetkező modellek között inkonzisztens volt. együttes.
    + Kijavítva a hiba, amely akkor következik be, amikor a betanítási és/vagy ellenőrzési címkéket (y és y_valid) a pandák dataframe, de nem NumPy tömb formájában adták meg.
    + Kijavítva az előrejelzési feladatokkal kapcsolatos problémát, ha a bemeneti táblák logikai oszlopaiban sem történt a hiba.
    + Annak engedélyezése, hogy a AutoML-felhasználók eldobják a betanítási sorozatot, amely nem elég hosszú az előrejelzés során. – Lehetővé teszi, hogy a AutoML-felhasználók eldobják azokat a teszt-készletből, amelyek nem szerepelnek az előrejelzés során a betanítási készletben.
  + **azureml – mag**
    + A blob_cache_timeout paraméter megrendelésével kapcsolatos probléma kijavítva.
    + A külső illesztési és átalakítási kivételek rendszerhibákhoz lettek adva.
    + Key Vault-titkok támogatása távoli futtatásokhoz. Vegyen fel egy azureml. Core. kulcstartó. kulcstartó osztályt a munkaterülethez tartozó kulcstartó hozzáadásához, beolvasásához és listázásához. A támogatott műveletek a következők:
      + azureml. Core. Workspace. Workspace. get_default_keyvault ()
      + azureml. Core. kulcstartó. kulcstartó. set_secret (név, érték)
      + azureml. Core. kulcstartó. kulcstartó. set_secrets (secrets_dict)
      + azureml. Core. kulcstartó. kulcstartó. get_secret (név)
      + azureml. Core. kulcstartó. kulcstartó. get_secrets (secrets_list)
      + azureml. Core. kulcstartó. kulcstartó. list_secrets ()
    + További módszerek az alapértelmezett kulcstartó beszerzéséhez és a titkos kulcsok lekéréséhez a távoli futtatás során:
      + azureml. Core. Workspace. Workspace. get_default_keyvault ()
      + azureml. Core. Run. Run. get_secret (név)
      + azureml. Core. Run. Run. get_secrets (secrets_list)
    + További felülbírálási paraméterek lettek hozzáadva a Submit-HyperDrive CLI-parancshoz.
    + Az API-hívások megbízhatóságának növelése a gyakori kérések könyvtárbeli kivételekre való ismételt próbálkozások kiterjesztése.
    + A futtatások küldésének támogatása egy elküldött futtatásból.
    + A FileWatcher-ben rögzített lejáró SAS-token probléma, amely miatt a fájlok a kezdeti jogkivonat lejárta után le lettek töltve.
    + Támogatott a HTTP CSV/TSV fájlok importálása a DataSet Python SDK-ban.
    + A munkaterület. Setup () metódus elavult. A felhasználók számára megjelenített figyelmeztető üzenet a Create () vagy a Get ()/from_config () függvényt javasolja.
    + Hozzáadott környezet. add_private_pip_wheel (), amely lehetővé teszi privát egyéni Python-`whl`csomagok feltöltését a munkaterületre, és biztonságos módon felhasználja azokat a környezet létrehozásához vagy megvalósulása érdekében.
    + Most már frissítheti a Microsoft által generált és az ügyfél-tanúsítványhoz az AK-fürtökön üzembe helyezett pontozási végpont SSL-tanúsítványát is.
  + **azureml – magyarázat – modell**
    + Paraméter hozzáadva a modell AZONOSÍTÓjának a feltöltéshez való hozzáadásához.
    + A rendszer hozzáadta `is_raw` címkézést a memóriában található magyarázatokhoz és a feltöltéshez.
    + Pytorch-támogatás és-tesztek hozzáadva a azureml-magyarázza-Model csomaghoz.
  + **azureml – opendatasets**
    + Az automatikus tesztelési környezet észlelése és naplózása.
    + Osztályok hozzáadásával az Egyesült államokbeli lakosságot a megye és a zip alapján szerezheti be.
  + **azureml – folyamat – mag**
    + A Label tulajdonság hozzáadva a bemeneti és a kimeneti port definícióhoz.
  + **azureml – telemetria**
    + Helytelen telemetria-konfiguráció lett kijavítva.
  + **azureml-Train-automl**
    + Kijavítva a hiba, ahol a telepítési hiba miatt nem sikerült bejelentkezni a "hibák" mezőbe a telepítési futtatáshoz, ezért a rendszer nem a szülő "errors" (hibák) alatt tárolja a hibát.
    + Javítva lett egy probléma a AutoML, ahol a hiányzó címkékkel rendelkező sorok nem lettek megfelelően eltávolítva.
    + Annak engedélyezése, hogy a AutoML-felhasználók eldobják a betanítási sorozatot, amely nem elég hosszú az előrejelzés során.
    + Annak engedélyezése, hogy a AutoML-felhasználók eldobják azokat a tesztelési készletből, amelyek nem szerepelnek az előrejelzés során a betanítási készletben.
    + A AutoMLStep mostantól áthalad `automl` konfiguráción keresztül, hogy elkerülje az új konfigurációs paraméterek módosításainak vagy kiegészítéseinek esetleges problémáit.
    + A AutoML-adatGuardrail már nyilvános előzetes verzióban érhető el. A felhasználó az Guardrail jelentést (besorolási/regressziós feladatok esetén) fogja látni a képzés után, és az SDK API-n keresztül is elérhetővé teheti.
  + **azureml-Train-Core**
    + A Torch 1,2-támogatás hozzáadva a PyTorch kalkulátorhoz.
  + **azureml – widgetek**
    + Továbbfejlesztett összetéveszthető mátrix-diagramok a besorolási képzéshez.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.12
+ **Új funkciók**
  + A sztringek listája mostantól a `read_*` metódusok bemenetként adható át.

+ **Hibajavítások és javítások**
  + A `read_parquet` teljesítménye jelentősen javult a Sparkban való futás során.
  + Kijavított egy problémát, amelyben a `column_type_builder` nem egyértelmű dátumformátum esetén egyetlen oszlop esetében meghiúsult.

### <a name="azure-portal"></a>Azure Portal
+ **Előzetes verzió funkció**
  + A log és a output file streaming mostantól elérhető a futtatási részletek oldalain. A fájlok valós időben továbbítják a frissítéseket, amikor bekapcsolják az előnézeti kapcsolót.
  + A kvóta megadásának lehetősége a munkaterület szintjén előzetes verzióban érhető el. A AmlCompute-kvóták az előfizetés szintjén vannak lefoglalva, de mostantól lehetővé tesszük, hogy az adott kvótát a munkaterületek között, és a méltányos megosztáshoz és irányításhoz ossza ki. A munkaterület bal oldali navigációs sávján kattintson a **használat + kvóták** panelre, és válassza a **kvóták konfigurálása** fület. vegye figyelembe, hogy a kvóták a munkaterület szintjén való beállítása, mivel ez egy több-munkaterület művelet.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK a Python v 1.0.55

+ **Új funkciók**
  + A jogkivonat-alapú hitelesítés mostantól támogatott az AK-on üzembe helyezett pontozási végponton kezdeményezett hívásokban. Továbbra is támogatni fogjuk az aktuális kulcs alapú hitelesítést, és a felhasználók egyszerre használhatják ezeket a hitelesítési mechanizmusokat.
  + Lehetőség a virtuális hálózat (VNet) mögött lévő blob Storage adattárként való regisztrálására.

+ **Hibajavítások és javítások**
  + **azureml-automl-Core**
    + Kijavít egy hibát, ahol a CV-felosztások érvényesítési mérete kicsi, és a regressziós és az előrejelzési eredményhez rossz előre jelzett és igaz diagramokat eredményez.
    + A távoli futtatások előrejelzési feladatainak naplózása továbbfejlesztett, mostantól a felhasználó átfogó hibaüzenetet kap, ha a Futtatás sikertelen volt.
    + A `Timeseries` rögzített hibái, ha az előfeldolgozás jelzője igaz.
    + Az előrejelzési adatellenőrzéssel kapcsolatos egyes hibaüzenetek további műveleteket végeznek.
    + Az adatkészletek ledobásával és/vagy lusta betöltésével a AutoML kevesebb memóriát használ, különösen a folyamatok között
  + **azureml – feltörzs – magyarázat – modell**
    + Model_task jelző hozzáadása a magyarázatokhoz, amely lehetővé teszi, hogy a felhasználó felülbírálja a modell típusának alapértelmezett automatikus következtetési logikáját
    + Widget-változások: automatikusan települ a `contrib`, nincs több `nbextension` telepítése/engedélyezése – támogatás magyarázata csak globális funkció fontosságával (pl. Permutative)
    + Irányítópult-változások: Box-mintaterületek és hegedű-mintaterületek az összefoglalás oldalának `beeswarm` teleken felül – sokkal gyorsabb, ha a "Top-k" csúszka módosítására szolgáló `beeswarm`-ábrázolás a "felülről
  + **azureml – mag**
    + A Model. package () metódussal olyan Docker-lemezképeket és Dockerfiles hozhat létre, amelyek a modelleket és a függőségeiket beágyazzák.
    + Frissített helyi webszolgáltatások a környezeti objektumokat tartalmazó InferenceConfigs fogadásához.
    + Fixed Model. Register () érvénytelen modelleket állít elő, ha "." (az aktuális könyvtár esetében) a model_path paraméterként lesz átadva.
    + Adja hozzá a Run. submit_child, a funkció-tükrözések kísérlet. Submit parancsot az elküldött gyermek futtatásának megadásakor.
    + A Model. register konfigurációs beállításainak támogatása a Run. register_modelban.
    + JAR-feladatok futtatása meglévő fürtön.
    + Most instance_pool_id és cluster_log_dbfs_path paraméterek támogatása.
    + Egy környezeti objektum használatának támogatása a modell webszolgáltatásba történő telepítésekor. A környezeti objektum most már megadható a InferenceConfig objektum részeként.
    + Új régiókhoz tartozó appinsifht-leképezés hozzáadása – CentralUS-westus-northcentralus
    + Dokumentáció hozzáadva az összes adattár osztály összes attribútumához.
    + Blob_cache_timeout paraméter hozzáadva a `Datastore.register_azure_blob_container`hoz.
    + Save_to_directory és load_from_directory metódusok lettek hozzáadva a következőhöz: azureml. Core. environment. environment.
    + Hozzáadta az "az ml-környezet letöltése" és az "az ml-környezet regisztrálása" parancsokat a CLI-hez.
    + Hozzáadott környezet. add_private_pip_wheel metódus.
  + **azureml – magyarázat – modell**
    + Az adatkészlet nyomon követése az adatkészlet szolgáltatás (előzetes verzió) használatával magyarázatokkal bővült.
    + Csökkent az alapértelmezett köteg mérete, amikor a globális magyarázatot a 10k-ról 100-re.
    + Model_task jelzőt adott hozzá a magyarázatokhoz, hogy a felhasználó felülbírálja a modell típusának alapértelmezett automatikus következtetési logikáját.
  + **azureml – mlflow**
    + Kijavítva a hiba a mlflow. azureml. build_imageban, ahol a rendszer figyelmen kívül hagyja a beágyazott könyvtárakat.
  + **azureml – folyamat – lépések**
    + A JAR-feladatok meglévő Azure Databricks-fürtön való futtatásának lehetősége hozzáadva.
    + A támogatási instance_pool_id és cluster_log_dbfs_path paraméterek hozzáadása a DatabricksStep lépéshez.
    + A DatabricksStep lépésben hozzáadta a folyamat paramétereinek támogatását.
  + **azureml-Train-automl**
    + `docstrings` hozzáadva az Ensemble-hez kapcsolódó fájlokhoz.
    + A dokumentumok frissítése a `max_cores_per_iteration` és `max_concurrent_iterations` megfelelőbb nyelvére
    + A távoli futtatások előrejelzési feladatainak naplózása továbbfejlesztett, mostantól a felhasználó átfogó hibaüzenetet kap, ha a Futtatás sikertelen volt.
    + Get_data eltávolítva a folyamatból `automlstep` jegyzetfüzetből.
    + A `automlstep``dataprep` támogatásának megkezdése.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.10

+ **Új funkciók**
  + Most már kérheti az adott oszlopokra vonatkozó bizonyos ellenőrök (például hisztogram, pontdiagram stb.) futtatását is.
  + Hozzáadott egy integrálással argumentumot a `append_columns`hoz. Ha az értéke igaz, a rendszer a memóriába tölti be az adatmennyiséget, a végrehajtás pedig párhuzamosan fut. Ha a False (hamis), a végrehajtás streaming, de egyszálas lesz.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK a Python v 1.0.53

+ **Új funkciók**
  + Az automatizált Machine Learning mostantól támogatja a távoli számítási cél ONNX modelljeit
  + A Azure Machine Learning mostantól lehetővé teszi az előző futtatási, ellenőrzőpont-vagy modell-fájlok betanításának folytatását.
    + Megtudhatja, hogyan [használhatja a becslések-t egy korábbi futtatásból származó képzés folytatásához](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Hibajavítások és javítások**
  + **automl-Client-Core-nativeclient**
    + Javítsa ki a hibát az oszlopok típusának elvesztése után az átalakítás után (hiba összekapcsolva);
    + Annak engedélyezése, hogy y_query olyan objektumtípus legyen, amely a No (s) értéket tartalmazza a BEGIN (#459519) értéknél.
  + **Azure-CLI-ml**
    + A CLI-parancsok "modell telepítése" és "szolgáltatás frissítése" mostantól elfogadják a paramétereket, a konfigurációs fájlokat vagy a kettő kombinációját. A paraméterek elsőbbséget élveznek a fájlok attribútumaival szemben.
    + A modell leírása mostantól a regisztráció után is frissíthető
  + **azureml-automl-Core**
    + NimbusML-függőség frissítése a 1.2.0 verziójára (aktuális legújabb).
    + Támogatás hozzáadása a NimbusML becslések & a AutoML-becslések belüli folyamatokhoz.
    + Az Ensemble kiválasztási eljárásában felmerült hiba, amely szükségtelenül bővült az eredményül kapott együttesnél, még akkor is, ha a pontszámok változatlanok maradnak.
    + Az előrejelzési feladatok elvégzése érdekében az egyes featurizations ismételt használatának engedélyezése. Ez felgyorsítja a telepítés futtatási idejét, nagyjából a költséges featurizations, például a késések és a folyamatos Windows-költségek n_cross_validationsának egy tényezője.
    + Probléma megoldása, ha az idő a Panda által támogatott időtartományon kívül esik. Most DataException, ha az idő kevesebb, mint PD. Timestamp. min vagy nagyobb, mint a PD. Timestamp. max
    + Az előrejelzés mostantól lehetővé teszi, hogy a vonatok és a tesztek különböző gyakorisággal legyenek igazítva. Például a "negyedéves kezdés januárban" és a "negyedévente kezdődően október" értékkel igazítható.
    + A "parameters" tulajdonság hozzá lett adva a TimeSeriesTransformer.
    + Távolítsa el a régi kivételi osztályokat.
    + Az előrejelzési feladatokban a `target_lags` paraméter mostantól egyetlen egész értéket, vagy egész számok listáját fogadja el. Ha az egész számot adta meg, csak egy késés lesz létrehozva. Ha egy lista van megadva, a rendszer a lemaradás egyedi értékeit veszi figyelembe. target_lags = [1, 2, 2, 4] egy, 2 és 4 időszakból álló késéseket fog létrehozni.
    + Javítsa ki a hibát az oszlopok típusának elvesztése után az átalakítás után (hiba összekapcsolva);
    + A `model.forecast(X, y_query)`ban engedélyezze a nem (ka) t tartalmazó objektumtípus y_query használatát a BEGIN (#459519) elemnél.
    + várt értékek hozzáadása `automl` kimenethez
  + **azureml – datadrift**
    +  Többek között a jegyzetfüzetek fejlesztése, beleértve a azureml-opendatasets való váltást, a azureml-opendatasets és a teljesítmény javítását az adatgazdagítás során
  + **azureml – feltörzs – magyarázat – modell**
    + Rögzített átalakítások argumentum a LIME-magyarázathoz a nyers funkció fontosságának megállapításához a azureml-rel-detribal-magyarázza-Model csomag
    + Szegmentálások hozzáadása a képmagyarázatban a AzureML-a-detribal-magyarázza-Model csomaghoz
    + SciPy ritka támogatásának hozzáadása a LimeExplainer
    + `batch_size` hozzáadásával elmagyarázhatja a magyarázatot, ha `include_local=False`, a kötegekben a globális magyarázatokat a DecisionTreeExplainableModel végrehajtásának javítása érdekében.
  + **azureml – featureengineering**
    + A (z) set_featurizer_timeseries_params hívásának javítása (): dict-értéktípus módosítása és null-beadás – jegyzetfüzet hozzáadása a `timeseries` Képtulajdonság
    + NimbusML-függőség frissítése a 1.2.0 verziójára (aktuális legújabb).
  + **azureml – mag**
    + A DBFS-adattárolók csatlakoztatásának lehetősége a AzureML CLI-ben
    + Kijavítva a hiba az adattár feltöltésekor, ha a `target_path` elindítva üres mappát hoz létre `/`
    + Kijavítva `deepcopy` probléma a ServicePrincipalAuthentication-ben.
    + Hozzáadta az "az ml-környezet megjelenítése" és az "az ml környezet listázása" parancsot a parancssori felülethez.
    + A környezetek mostantól támogatják a base_dockerfile megadását egy már létrehozott base_image alternatívájaként.
    + A nem használt RunConfiguration-beállítás auto_prepare_environment elavultként van megjelölve.
    + A modell leírása mostantól a regisztráció után is frissíthető
    + Hibajavítás: a modell-és rendszerkép-törlés mostantól további információkat nyújt a felsőbb rétegbeli objektumok lekéréséről, amelyek attól függenek, hogy a törlés a felsőbb rétegbeli függőség miatt meghiúsul-e.
    + Kijavítva a hiba, amely üres időtartamot nyomtat a környezetekhez tartozó munkaterületek létrehozásakor előforduló központi telepítések esetén.
    + A továbbfejlesztett munkaterület a hibák kivételeit hozza létre. A felhasználók nem látják a munkaterület létrehozását. Nem található... " az üzenet helyett a tényleges létrehozási hiba jelenik meg.
    + Adja hozzá a jogkivonat-hitelesítés támogatását az AK webszolgáltatásokban.
    + `get_token()` metódus hozzáadása `Webservice` objektumokhoz.
    + A CLI-támogatás hozzáadva a Machine learning-adatkészletek kezeléséhez.
    + `Datastore.register_azure_blob_container` most igény szerint `blob_cache_timeout` értéket (másodpercben), amely a blobfuse csatlakoztatási paramétereit konfigurálja a gyorsítótár lejáratának engedélyezéséhez az adattár számára. Az alapértelmezett érték nem időtúllépés, azaz a Blobok beolvasása esetén a helyi gyorsítótárban marad, amíg a művelet be nem fejeződik. A legtöbb feladat inkább ezt a beállítást fogja előnyben részesíteni, de egyes feladatokhoz több adatra van szükség egy nagy adatkészletből, mint amennyi a csomópontjaihoz fér. Ezekhez a feladatokhoz a paraméter finomhangolása segít a sikeres műveletben. Ügyeljen a paraméter finomhangolására: a túl alacsony érték beállítása gyenge teljesítményt eredményezhet, mivel a korszakban használt adat lejár, mielőtt ismét használni lehetne. Ez azt jelenti, hogy az összes olvasás a blob Storage-ból (azaz a hálózatból) történik, nem pedig a helyi gyorsítótárból, amely negatív hatással van a betanítási időpontokra.
    + A modell leírása mostantól a regisztráció után is megfelelően frissíthető
    + A modell-és képtörlés mostantól további információkat tartalmaz a rajtuk függő felsőbb rétegbeli objektumokról, ami miatt a törlés meghiúsul
    + A távoli futtatások erőforrás-kihasználtságának javítása a azureml. mlflow használatával.
  + **azureml – magyarázat – modell**
    + Rögzített átalakítások argumentum a LIME-magyarázathoz a nyers funkció fontosságának megállapításához a azureml-rel-detribal-magyarázza-Model csomag
    + SciPy ritka támogatásának hozzáadása a LimeExplainer
    + hozzáadott alakzat lineáris bemutató burkoló, valamint egy másik szint a táblázatos magyarázathoz a lineáris modellek elmagyarázása érdekében
    + az elmagyarázza a modell függvénytárában, rögzített hiba, ha include_local = false for ritka adatbevitel
    + várt értékek hozzáadása `automl` kimenethez
    + a rögzített permutációs funkció fontossága, ha az átalakítások argumentuma a nyers funkció fontosságának beolvasására van megadva
    + `batch_size` hozzáadásával elmagyarázhatja a magyarázatot, ha `include_local=False`, a kötegekben a globális magyarázatokat a DecisionTreeExplainableModel végrehajtásának javítása érdekében.
    + a modell-magyarázó könyvtár, a rögzített tábla-magyarázatok, ahol a Panda dataframe input szükséges az előrejelzéshez
    + Kijavítva a hiba, ahol a `explanation.expected_values` időnként lebegőpontos értéket adnak vissza, nem pedig egy lebegőpontos listát.
  + **azureml – mlflow**
    + A mlflow teljesítményének javítása. set_experiment (experiment_name)
    + Javítsa ki a InteractiveLoginAuthentication for mlflow tracking_uri
    + A távoli futtatások erőforrás-kihasználtságának javítása a azureml. mlflow használatával.
    + Javítsa a azureml-mlflow csomag dokumentációját
    + Javítási hiba, ahol a mlflow. log_artifacts ("my_dir") az összetevők mentését a "my_dir/< ereklyét-paths >" helyett "< összetevő-elérési utak >"
  + **azureml – opendatasets**
    + A `opendatasets` a régi verzióra (< 0.14.0) `pyarrow` PIN-kódot az újonnan bevezetett memória-probléma miatt.
    + Helyezze át a azureml-opendatasets-azureml-opendatasets.
    + Lehetővé teszi, hogy a nyitott adatkészlet-osztályok regisztrálva legyenek Azure Machine Learning munkaterületre, és zökkenőmentesen hasznosítsa a pénzmosás-adatkészlet képességeit.
    + Jelentősen javíthatja a NoaaIsdWeather-bővítési teljesítményt a nem SPARK verzióban.
  + **azureml – folyamat – lépések**
    + A DBFS adattár mostantól támogatja a DatabricksStep bemeneteit és kimeneteit.
    + Frissített dokumentáció a Azure Batch lépéshez a bemenetek/kimenetek tekintetében.
    + A AzureBatchStep-ben módosult *delete_batch_job_after_finish* alapértelmezett értéke *true (igaz*).
  + **azureml – telemetria**
    +  Helyezze át a azureml-opendatasets-azureml-opendatasets.
    + Lehetővé teszi, hogy a nyitott adatkészlet-osztályok regisztrálva legyenek Azure Machine Learning munkaterületre, és zökkenőmentesen hasznosítsa a pénzmosás-adatkészlet képességeit.
    + Jelentősen javíthatja a NoaaIsdWeather-bővítési teljesítményt a nem SPARK verzióban.
  + **azureml-Train-automl**
    + A get_output frissített dokumentációja, amely a tényleges visszatérési típust tükrözi, és további megjegyzéseket biztosít a kulcs tulajdonságainak lekéréséhez.
    + NimbusML-függőség frissítése a 1.2.0 verziójára (aktuális legújabb).
    + várt értékek hozzáadása `automl` kimenethez
  + **azureml-Train-Core**
    + A karakterláncok mostantól számítási célként vannak elfogadva az automatikus Hiperparaméter-hangoláshoz
    + A nem használt RunConfiguration-beállítás auto_prepare_environment elavultként van megjelölve.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.9

+ **Új funkciók**
  + A fájlok közvetlenül egy http-vagy HTTPS-URL-címről való olvasásának támogatása.

+ **Hibajavítások és javítások**
  + Továbbfejlesztett hibaüzenet, amikor egy távoli forrásból (amely jelenleg nem támogatott) származó parketta-adatkészletet próbál beolvasni.
  + Hiba javítva a parketta-fájlformátumba való íráskor a ADLS Gen 2 fájlban, és a ADLS Gen 2 tároló nevének frissítése az elérési úton.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Vizuális felület
+ **Előzetes verziójú funkciók**
  + Az "R-szkript végrehajtása" modul hozzáadva a Visual Interface-ben.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK a Python v 1.0.48

+ **Új funkciók**
  + **azureml – opendatasets**
    + **azureml – a opendatasets** már elérhető **azureml-opendatasets**néven. A régi csomag továbbra is működhet, de javasoljuk, hogy a **azureml-opendatasets** a szélesebb körű képességek és tökéletesítések érdekében használja.
    + Ez az új csomag lehetővé teszi, hogy a megnyitott adatkészleteket adatkészletként regisztrálja Azure Machine Learning munkaterületen, és kihasználja az adatkészlet által kínált funkciókat.
    + Emellett olyan meglévő képességeket is tartalmaz, mint például az Open-adatkészletek Panda/SPARK dataframes való használata, valamint egyes adathalmazok, például időjárási viszonyok elérésének helye.

+ **Előzetes verziójú funkciók**
    + A HyperDriveConfig mostantól elfogadhatja a folyamat objektumát paraméterként a hiperparaméter hangolásának támogatásához egy folyamat használatával.

+ **Hibajavítások és javítások**
  + **azureml-Train-automl**
    + Kijavítva a hiba az oszlopok típusának elvesztése után az átalakítás után.
    + Kijavítva a hiba, hogy a y_query olyan objektumtípus legyen, amely a none (ok) értéket tartalmazza az elején.
    + Kijavította a problémát a Ensemble kiválasztási eljárásában, amely szükségtelenül megnőtte az eredményül kapott Ensemble-t, még akkor is, ha a pontszámok állandó
    + Kijavítottuk a problémát a AutoMLStep whitelist_models és blacklist_models beállításaival.
    + Kijavítottuk a problémát, amely megakadályozta az előfeldolgozás használatát, ha az AutoML az Azure ML-folyamatok kontextusában használták volna.
  + **azureml – opendatasets**
    + Áthelyezett azureml-opendatasets-azureml-opendatasets.
    + Az engedélyezett nyitott adatkészlet-osztályok regisztrálva lesznek Azure Machine Learning munkaterületen, és zökkenőmentesen használhatják a pénzmosás-adatkészletek képességeit.
    + A NoaaIsdWeather továbbfejlesztett teljesítményének növelése a nem SPARK verzióban jelentős mértékben.
  + **azureml – magyarázat – modell**
    + Frissített online dokumentáció az értelmező objektumokhoz.
    + `batch_size` hozzáadásával elmagyarázhatja a magyarázatot, ha `include_local=False`, a kötegekben a globális magyarázatok továbbítása érdekében, hogy javítsa a DecisionTreeExplainableModel a modell-magyarázó könyvtárhoz.
    + Kijavítottuk a problémát, amelyben a `explanation.expected_values` időnként lebegőpontos értéket adnak vissza, nem pedig egy lebegőpontos lista.
    + A program hozzáadta a várt értékeket a `automl` kimenetéhez, amely leírja a modell függvénytárát.
    + A rögzített permutációs funkció fontossága, ha az átalakítások argumentuma a nyers funkció fontosságának beolvasására van megadva.
  + **azureml – mag**
    + Hozzá lett adva a DBFS-adattárolók csatolása az AzureML CLI-ben.
    + Az adattár feltöltésével kapcsolatos probléma kijavítva, ha a `target_path` a `/`megkezdett, üres mappát hoz létre.
    + Két adatkészlet engedélyezett összehasonlítása.
    + A modell és a rendszerkép törlése mostantól további információkat nyújt a felsőbb rétegbeli objektumok lekéréséről, amelyek attól függenek, hogy a törlés a felsőbb rétegbeli függőség miatt meghiúsul-e.
    + A auto_prepare_environment nem használt RunConfiguration-beállítás elavult.
  + **azureml – mlflow**
    + A azureml. mlflow-t használó távoli futtatások továbbfejlesztett Erőforrás-kihasználtsága.
    + Javítottuk a azureml-mlflow csomag dokumentációját.
    + Kijavítva a probléma, amelyben a mlflow. log_artifacts ("my_dir") a "my_dir/artifact-paths" alatt az "összetevő-útvonalak" helyett az összetevők mentését tenné.
  + **azureml – folyamat – mag**
    + Az összes folyamat lépéseihez hash_paths paraméter elavult, és a jövőben el lesz távolítva. A source_directory alapértelmezett tartalma kivonattal van elkészítve (kivéve a. amlignore vagy. gitignore fájlban felsorolt fájlokat)
    + Folyamatosan fejlesztheti a modul-és ModuleStep a számítási típusokra vonatkozó speciális modulok támogatásához, hogy felkészüljön a RunConfiguration-integrációra és egyéb változásokra a számítási típus meghatározott modul-használatának feloldásához a folyamatokban.
  + **azureml – folyamat – lépések**
    + AzureBatchStep: továbbfejlesztett dokumentáció a bemenetek/kimenetek tekintetében.
    + AzureBatchStep: módosult delete_batch_job_after_finish alapértelmezett értéke TRUE (igaz).
  + **azureml-Train-Core**
    + A karakterláncok mostantól számítási célként vannak elfogadva az automatikus Hiperparaméter-hangoláshoz.
    + A auto_prepare_environment nem használt RunConfiguration-beállítás elavult.
    + Az elavult paraméterek `conda_dependencies_file_path` és `pip_requirements_file_path` a `conda_dependencies_file` és a `pip_requirements_file` javára.
  + **azureml – opendatasets**
    + Jelentősen javíthatja a NoaaIsdWeather-bővítési teljesítményt a nem SPARK verzióban.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.8

+ **Új funkciók**
 + A adatfolyam objektumok mostantól megadhatók, és rekordokat hozhatnak létre. Lásd: `Dataflow.to_record_iterator`dokumentációja.
  + A adatfolyam objektumok mostantól megadhatók, és rekordokat hozhatnak létre. Lásd: `Dataflow.to_record_iterator`dokumentációja.

+ **Hibajavítások és javítások**
 + Növelte a Adatelőkészítés SDK megbízhatóságát.
 + A pandák DataFrames jobb kezelését a nem karakterlánc típusú oszlop indexekkel.
 + Az adatkészletek `to_pandas_dataframe` teljesítményének javítása.
 + Kijavítva egy olyan hibát, amelyben az adathalmazok Spark-végrehajtása nem sikerült, ha több csomópontos környezetben futnak.
  + Növelte a Adatelőkészítés SDK megbízhatóságát.
  + A pandák DataFrames jobb kezelését a nem karakterlánc típusú oszlop indexekkel.
  + Az adatkészletek `to_pandas_dataframe` teljesítményének javítása.
  + Kijavítva egy olyan hibát, amelyben az adathalmazok Spark-végrehajtása nem sikerült, ha több csomópontos környezetben futnak.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.7

Javítottunk egy olyan változást, amely javítja a teljesítményt, mivel a Azure Databrickst használó ügyfeleknél problémát okozott. Ha Azure Databricks problémába ütközött, az alábbi módszerek egyikével frissíthet a verzió 1.1.7:
1. Futtassa ezt a parancsfájlt a frissítéshez: `%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Hozza létre újra a fürtöt, amely telepíti a legújabb adat-előkészítési SDK-verziót.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK a Python v 1.0.45

+ **Új funkciók**
  + Döntési fa helyettesítő modell hozzáadása az azureml-magyarázza-Model csomagban található magyarázatok megjelenítéséhez
  + Lehetőség arra, hogy megadhatja a rendszerképekre telepítendő CUDA-verziót. A CUDA 9,0, 9,1 és 10,0 támogatása.
  + Az Azure ML betanítási alaplemezképekkel kapcsolatos információk mostantól elérhetők az [Azure ml containers GitHub-tárházban](https://github.com/Azure/AzureML-Containers) és a [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + CLI-támogatás hozzáadva a folyamat-ütemtervhez. További információért futtassa az "az ml pipeline-h" parancsot.
  + Egyéni Kubernetes névtér-paraméter lett hozzáadva az AK webszolgáltatások üzembe helyezési konfigurációjához és a CLI-hez.
  + Az összes folyamat lépéseinél elavult hash_paths paraméter
  + A Model. register mostantól támogatja a több egyedi fájl regisztrálását egyetlen modellként a `child_paths` paraméter használatával.

+ **Előzetes verziójú funkciók**
    + A pontozási magyarázatok mostantól opcionálisan menthetik a Conda és a pip-adatokat a megbízhatóbb szerializálás és deszerializálás céljából.
    + Hibajavítás az automatikus funkció-választóhoz.
    + A mlflow. azureml. build_image frissítve az új API-val, az új implementáció által közzétett javított hibákkal.

+ **Hibajavítások és javítások**
  + A rendszer eltávolította `paramiko` függőséget a azureml-Core-ból. Elavult figyelmeztetések jelennek meg az örökölt számítási cél csatolási módszereihez.
  + A futtatási teljesítmény javítása. create_children
  + A megmagyarázó bináris osztályozó használatával a rendszer kijavítja a valószínűségi sorrendet, ha a tanár valószínűsége az alakzat értékének méretezésére szolgál.
  + Továbbfejlesztett hibakezelés és üzenet az automatizált gépi tanuláshoz.
  + Kijavítottuk az automatikus gépi tanulásra vonatkozó iterációs időtúllépési problémát.
  + Továbbfejlesztettük az automatikus gépi tanulás idősorozat-átalakítási teljesítményét.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.6

+ **Új funkciók**
  + A Top Values (`SummaryFunction.TOPVALUES`) és az alsó értékek (`SummaryFunction.BOTTOMVALUES`) összegző függvényei is hozzáadhatók.

+ **Hibajavítások és javítások**
  + Jelentősen javította `read_pandas_dataframe`teljesítményét.
  + Kijavítva egy olyan hibát, amely `get_profile()`t okoz a bináris fájlokra mutató adatfolyam.
  + A telemetria-gyűjtemény programozott engedélyezését vagy letiltását lehetővé tévő `set_diagnostics_collection()`.
  + Módosította `get_profile()`viselkedését. A NaN értékeit a rendszer figyelmen kívül hagyja a min, a Mean, az STD és az Sum függvénynél, amely a pandák viselkedéséhez igazodik.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK a Python v 1.0.43

+ **Új funkciók**
  + A Azure Machine Learning mostantól biztosítja az első osztályú támogatást a népszerű gépi tanulási és adatelemzési keretrendszer Scikit – Learn. A [`SKLearn` kalkulátor](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)használatával a felhasználók egyszerűen betanítják és üzembe helyezhetik a Scikit-tanulási modelleket.
    + Ismerje meg, hogyan [futtathatja a hiperparaméter-hangolást a Scikit használatával – Ismerkedjen meg a HyperDrive segítségével](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + További támogatás a ModuleStep létrehozásához a folyamatokban, valamint a modul-és ModuleVersion osztályok használatával az újrafelhasználható számítási egységek kezeléséhez.
  + Az ACI-webszolgáltatások mostantól támogatják az állandó scoring_uri a frissítéseken keresztül. A scoring_uri IP-ről FQDN-re változik. A teljes tartománynévhez tartozó DNS-név címkéje konfigurálható úgy, hogy beállítja a dns_name_label deploy_configuration.
  + Automatikus gépi tanulás – új funkciók:
    + STL-Képtulajdonság az előrejelzéshez
    + A KMeans-fürtszolgáltatás engedélyezve van a szolgáltatások elsöprő számára
  + A AmlCompute-kvóták jóváhagyása csak gyorsabb volt! Most automatizáljuk a kvóta-kérelmek küszöbértéken belüli jóváhagyásának folyamatát. A kvóták működésével kapcsolatos további információkért tekintse meg a [kvóták kezelését](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)ismertető témakört.

+ **Előzetes verziójú funkciók**
    + Integráció a [MLflow](https://mlflow.org) 1.0.0 követésével azureml-MLflow-csomagon keresztül ([például jegyzetfüzetek](https://aka.ms/azureml-mlflow-examples)).
    + Jupyter-jegyzetfüzet küldése futtatásként. [API-referenciák dokumentációja](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Az [adatdrift-detektor](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) nyilvános előzetes verziója azureml-datadrift csomagon keresztül ([például jegyzetfüzetek](https://aka.ms/azureml-datadrift-example)). Az adateltolódás az egyik legfontosabb ok, ahol a modell pontossága az idő múlásával csökken. Ez akkor történik meg, ha az éles modellbe való kiszolgált adatok eltérnek a modell által betanított adatoktól. A pénzmosás-adatdrift detektor segítségével az ügyfél figyelheti az adateltolódást, és riasztást küld, ha a rendszer a drift észlelését észleli

+ **Változtatások megszakítása**

+ **Hibajavítások és javítások**
  + A RunConfiguration betöltési és a mentési funkció támogatja a teljes elérési út megadását a korábbi működéshez szükséges teljes biztonsági mentéssel.
  + A ServicePrincipalAuthentication-ben hozzáadott gyorsítótárazási funkció alapértelmezés szerint ki van kapcsolva.
  + Több mintaterület naplózásának engedélyezése ugyanazzal a metrikai névvel.
  + A Model osztály mostantól megfelelően importálható a azureml. Core (`from azureml.core import Model`) rendszerből.
  + A folyamat lépéseiben `hash_path` paraméter már elavult. Az új viselkedés a teljes source_directory kivonatolása, kivéve a. amlignore vagy. gitignore fájlban felsorolt fájlokat.
  + A folyamat csomagjaiban a különböző `get_all` és `get_all_*` metódusok elavultak `list` és `list_*`javára.
  + a azureml. Core. get_run már nem igénylik az osztályok importálását az eredeti futtatási típus visszaküldése előtt.
  + Kijavított egy hibát, amelyben a webszolgáltatások frissítéseinek meghívása nem váltott ki frissítést.
  + Az AK-webszolgáltatások pontozási időtúllépésének 5ms és 300000ms között kell lennie. A pontozási kérelmek maximális megengedett scoring_timeout_msa 1 perc és 5 perc között van.
  + A LocalWebservice objektumok mostantól `scoring_uri` és `swagger_uri` tulajdonságokkal rendelkeznek.
  + Áthelyezett kimenetek könyvtár létrehozása és kimenetek könyvtár feltöltése a felhasználói folyamatból. A futtatási előzmények SDK-t minden felhasználói folyamaton futtathatja. Ennek során meg kell oldania az elosztott képzések futtatásával kapcsolatos egyes szinkronizálási problémákat.
  + A felhasználói folyamat nevéből írt azureml-napló neve mostantól tartalmazza a folyamat nevét (csak elosztott képzés esetén) és a PID-t.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.5

+ **Hibajavítások és javítások**
  + A kétjegyű éves formátummal rendelkező, az érvényes évek tartománya frissült a Windows májusi kiadásának megfelelő értékekre. A tartomány 1930-2029 és 1950-2049 között lett módosítva.
  + Amikor beolvas egy fájlt, és beállítja a `handleQuotedLineBreaks=True`t, `\r` új sorként lesz kezelve.
  + Kijavított egy hibát, amely miatt a `read_pandas_dataframe` sikertelen volt bizonyos esetekben.
  + A `get_profile`jobb teljesítménye.
  + Javított hibaüzenetek.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning adat PREP SDK v 1.1.4

+ **Új funkciók**
  + Mostantól a következő kifejezés nyelvi függvények használatával kinyerheti és elemezheti a DateTime értékeket új oszlopokra.
    + `RegEx.extract_record()` a DateTime elemek új oszlopba való kibontása.
    + `create_datetime()` datetime objektumokat hoz létre a különböző datetime elemekről.
  + `get_profile()`meghívásakor láthatja, hogy a quantile oszlopai (EST.) szerint vannak megjelölve, hogy egyértelműen jelezze az értékek közelítését.
  + Mostantól a * * globbing is használhatja az Azure Blob Storage való olvasáskor.
    + például `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Hibajavítások**
  + Kijavított egy olyan hibát, amely a Parquet-fájlok távoli forrásból (Azure Blob) való olvasásával kapcsolatos.

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK a Python v 1.0.39
+ **Változások**
  + A konfigurációs auto_prepare_environment futtatása beállítás elavult, és az automatikus előkészítés válik az alapértelmezett értékre.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.3

+ **Új funkciók**
  + A PostgresSQL-adatbázisból való olvasás támogatása a read_postgresql meghívásával vagy adattár használatával.
    + Példák a útmutatókra:
      + [Adatfeldolgozási jegyzetfüzet](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Adattár-jegyzetfüzet](https://aka.ms/aml-data-prep-datastore-nb)

+ **Hibajavítások és javítások**
  + Az oszlop típusú konverzióval kapcsolatos hibák Javítva:
  + A most helyesen konvertál egy logikai vagy numerikus oszlopot egy logikai oszlopra.
  + Most nem sikerül, ha egy Date (dátum) típusú oszlopot kíván beállítani dátum típusként.
  + Továbbfejlesztett JoinType-típusok és kísérő dokumentáció. Két adatfolyamok való csatlakozáskor mostantól megadhatja az alábbi típusú csatlakozások egyikét:
    + NINCS, EGYEZÉS, BELSŐ, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Továbbfejlesztett adattípus, amely több dátumformátum felismerésére használható.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure Portal

A Azure Portal mostantól a következőket teheti:
+ Automatizált ML-kísérletek létrehozása és futtatása
+ Hozzon létre egy notebook virtuális gépet, hogy kipróbálja a minta Jupyter notebookokat vagy a sajátját.
+ Új szerzői műveletek szakasz (előzetes verzió) a Azure Machine Learning munkaterületen, amely magában foglalja az automatizált Machine Learning, a vizuális felületet és a szolgáltatott notebookos virtuális gépeket
    + Modell automatikus létrehozása automatizált gépi tanulás használatával
    + Drag and drop vizuális felület használata kísérletek futtatásához
    + Hozzon létre egy notebook virtuális gépet az adatelemzéshez, modellek létrehozásához és szolgáltatások telepítéséhez.
+ Élő diagram és metrika frissítése a jelentések futtatása és a részletek lapok futtatása
+ A naplófájlok, a kimenetek és a pillanatképek frissített fájlkezelője a futtatási részletek oldalain.
+ Új és továbbfejlesztett jelentés-létrehozási élmény a kísérletek lapon.
+ Lehetőség van a config. JSON fájl letöltésére a Azure Machine Learning munkaterület áttekintés lapján.
+ Azure Machine Learning munkaterület létrehozását a Azure Databricks munkaterületről.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK a Python v 1.0.33
+ **Új funkciók**
  + A _munkaterület. Create_ metódus most elfogadja az alapértelmezett FÜRTKONFIGURÁCIÓ a processzor-és GPU-fürtökhöz.
  + Ha a munkaterület létrehozása sikertelen, a függő erőforrások megtisztítása megtörténik.
  + Az alapértelmezett Azure Container Registry SKU alapértékre lett állítva.
  + A Azure Container Registry lustán jön létre, ha szükséges a futtatáshoz vagy a rendszerkép létrehozásához.
  + A betanítási környezetek támogatása.

### <a name="notebook-virtual-machine"></a>Notebook virtuális gép 

A notebookos virtuális gépek biztonságos, nagyvállalati használatra kész üzemeltetési környezetként használhatók Jupyter jegyzetfüzetekhez, ahol gépi tanulási kísérleteket végezhet, webvégpontként telepíthet modelleket, és elvégezheti az Azure Machine Learning SDK által támogatott összes egyéb műveletet a Python használatával. Számos képességet biztosít:
+ Gyorsan elindíthat [egy előre konfigurált notebookos virtuális gépet](tutorial-1st-experiment-sdk-setup.md) , amely az Azure Machine learning SDK és a kapcsolódó csomagok legújabb verzióját tartalmazza.
+ A hozzáférést bizonyított technológiák, például HTTPS, Azure Active Directory hitelesítés és engedélyezés biztosítja.
+ A notebookok és kódok megbízható Felhőbeli tárolása a Azure Machine Learning-munkaterület blob Storage-fiókban. A notebook virtuális gépet biztonságosan törölheti a munka elvesztése nélkül.
+ Előtelepített minta-jegyzetfüzetek Azure Machine Learning funkciók megismeréséhez és kipróbálásához.
+ Az Azure-beli virtuális gépek teljes körű testreszabási képességei, bármilyen virtuálisgép-típus, bármely csomag és bármely illesztőprogram. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>A Azure Machine Learning SDK for Python v 1.0.33 megjelent.

+ Általánosan elérhető az Azure ML Hardware Accelerated Models a [FPGA](how-to-deploy-fpga-web-service.md) .
  + Mostantól [a azureml-extra gyorsulás-models csomagot is használhatja](how-to-deploy-fpga-web-service.md) a következőhöz:
    + Egy támogatott Deep neurális hálózat (ResNet 50, ResNet 152, DenseNet-121, VGG-16 és SSD-VGG) súlyozásának betanítása
    + Az adatátviteli tanulás használata a támogatott DNN
    + A modell regisztrálása modellkezelés szolgáltatással és a modell tárolóba helyezése
    + A modell üzembe helyezése Azure-beli virtuális gépen egy Azure Kubernetes Service-(ak-) fürt FPGA
  + A tároló üzembe helyezése egy [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) kiszolgáló eszközön
  + Adja meg az adatait a gRPC-végponttal ezzel a [mintával](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatizált Machine Learning

+ A funkciók dinamikus hozzáadásával lehetővé teszi, hogy dinamikusan vegyen fel :::no-loc text="featurizers"::: a teljesítmény optimalizálása érdekében. Új :::no-loc text="featurizers":::: a munkabeágyazások, a bizonyítékok súlya, a célként megadott kódolások, a szöveges cél kódolása, a fürt távolsága
+ Intelligens önéletrajz a betanítási/érvényes osztások automatikus ML-ben való kezeléséhez
+ Néhány memória-optimalizálási módosítás és futásidejű teljesítmény javítása
+ Teljesítmény javítása a modellben – magyarázat
+ ONNX-modell átalakítása helyi futtatáshoz
+ Almintavételezési támogatás hozzáadva
+ Intelligens Leállítás, ha nincs megadva kilépési feltétel
+ Halmozott együttesek

+ Idősorozat-előrejelzés
  + Új prediktív előrejelzési függvény
  + Mostantól használhatja az idősorozat-adatelemzési időszakon belüli többit is
  + Az idősorozat-elmaradás konfigurálásához hozzáadott új funkciók
  + Új funkciókkal bővült a működés közbeni ablak összesített funkcióinak támogatása
  + Új üdülés észlelése és Képtulajdonság, ha az országkód definiálva van a kísérleti beállításokban

+ Azure Databricks
  + Az idősorozat-előrejelzés és a modell explainabilty/értelmező képességének engedélyezése
  + Mostantól megszakíthatja és folytathatja a (folytatás) automatizált ML-kísérletek elvégzését
  + Többprocesszoros feldolgozás támogatása

### <a name="mlops"></a>MLOps
+ **Helyi üzembe helyezési & hibakeresése pontozási tárolók esetén**<br/> Most már helyileg is üzembe helyezhet egy ML-modellt, és gyorsan megismételheti a pontozási fájlt és a függőségeket, hogy a várt módon viselkedjenek.

+ **Bevezetett InferenceConfig & Model. Deploy ()**<br/> A modell üzembe helyezése mostantól támogatja a forrás mappa megadását egy bejegyzési parancsfájllal, ugyanazokat a RunConfig.  Emellett a modell üzembe helyezését egyetlen parancsra is egyszerűsítettük.

+ **Git-hivatkozások követése**<br/> Az ügyfelek egy ideig alapszintű git-integrációs képességeket igényelnek, mivel ez segít a teljes körű naplózás fenntartásában. Az Azure ML-ben a git-hez kapcsolódó metaadatok (tárház, véglegesítés, tiszta állapot) tekintetében a főbb entitások nyomon követését valósították meg. Az SDK és a CLI automatikusan összegyűjti ezeket az információkat.

+ **Modell profilkészítés & érvényesítési szolgáltatás**<br/> Az ügyfelek gyakran kifogásolják a következtetési szolgáltatáshoz kapcsolódó számítási kapacitás mértékét. A Model profilkészítési szolgáltatás segítségével az ügyfél minta bemeneteket biztosíthat, és 16 különböző CPU-/memória-konfigurációban dolgozunk fel, hogy a telepítés optimális méretezése megtörténjen.

+ **Saját alaprendszerkép használata a következtetéshez**<br/> Egy másik gyakori panasz volt a nehézség a kísérletezéstől az ismételt megosztási függőségekre való áttérés során. Az új alaprendszerkép-megosztási képességgel most már felhasználhatja a kísérletezési alapképeket, a függőségeket és az összes, a következtetést. Ennek fel kell gyorsítania az üzemelő példányokat, és csökkentenie kell a belső és a külső hurok közötti hézagot.

+ **Továbbfejlesztett hencegő séma-létrehozási élmény**<br/> A korábbi hencegés-létrehozási módszer volt a hiba, és nem lehet automatizálni. Új, beágyazott módon hozhat létre a kisegítő sémákat bármely Python-függvényből a dekoratőrs használatával. Nyílt forráskódú ez a kód, és a séma generálási protokollja nem párosul az Azure ML platformmal.

+ **A Azure ML CLI általánosan elérhető (GA)**<br/> A modellek mostantól egyetlen CLI-paranccsal is üzembe helyezhetők. Általános vásárlói visszajelzésünk szerint a Jupyter-jegyzetfüzetből senki sem telepít ML modellt. A [**CLI-hivatkozás dokumentációja**](https://aka.ms/azmlcli) frissítve lett.


## <a name="2019-04-22"></a>2019-04-22

A Azure Machine Learning SDK for Python v 1.0.30 megjelent.

A [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) bevezette a közzétett folyamat új verziójának hozzáadását, miközben megtartja ugyanazt a végpontot.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.2

Megjegyzés: az adatprep Python SDK többé nem telepíti `numpy` és `pandas` csomagokat. Lásd: a [telepítési utasítások frissítése](https://github.com/Microsoft/AMLDataPrepDocs).

+ **Új funkciók**
  + Mostantól a pivot átalakítót is használhatja.
    + Útmutató: [pivot notebook](https://aka.ms/aml-data-prep-pivot-nb)
  + Mostantól reguláris kifejezéseket is használhat natív függvényekben.
    + Példák:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Most már használhatja a `to_upper` és a `to_lower` függvények kifejezést a kifejezés nyelvén.
  + Most már megtekintheti az egyes oszlopok egyedi értékeinek számát egy adatprofilban.
  + A gyakran használt olvasói lépések némelyike esetében a `infer_column_types` argumentumot is megadhatja. Ha `True`értékre van állítva, az adat-előkészítő megpróbálja felderíteni és automatikusan átalakítani az oszlopok típusait.
    + a `inference_arguments` már elavult.
  + Ezután meghívhatja `Dataflow.shape`.

+ **Hibajavítások és javítások**
  + `keep_columns` mostantól egy további opcionális argumentumot is elfogad `validate_column_exists`, amely ellenőrzi, hogy a `keep_columns` eredménye tartalmaz-e oszlopokat.
  + Az összes olvasó lépés (amely egy fájlból olvas) mostantól egy további opcionális argumentumot is elfogad `verify_exists`.
  + A Panda dataframe és az adatprofilok beolvasásának jobb teljesítménye.
  + Kijavítva egy olyan hibát, amelyben egy adatfolyam egyetlen lépésének szeletelése egyetlen indexszel meghiúsult.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + Most már újra elküldheti egy meglévő parancsfájl futtatását egy meglévő távoli számítási fürtön.
  + Mostantól egy közzétett folyamatot is futtathat új paraméterekkel a folyamatok lapon.
  + A Run details mostantól támogatja az új pillanatkép-fájl megjelenítőjét. Egy adott Futtatás elküldésekor megtekintheti a könyvtár pillanatképét. A Futtatás elindításához le is töltheti a beküldött jegyzetfüzetet.
  + Mostantól megszakíthatja a szülő futtatását a Azure Portalból.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK a Python v 1.0.23

+ **Új funkciók**
  + A Azure Machine Learning SDK mostantól támogatja a Python 3,7-es verzióit.
  + Azure Machine Learning DNN becslések már beépített, többverziós támogatást biztosít. Például `TensorFlow` a kalkulátor már elfogad egy `framework_version` paramétert, és a felhasználók megadhatják a következő verziót: "1,10" vagy "1,12". Az aktuális SDK-kiadás által támogatott verziók listáját a `get_supported_versions()` meghívása a kívánt Framework osztályban (például `TensorFlow.get_supported_versions()`).
  A legújabb SDK-kiadás által támogatott verziók listáját a [DNN kalkulátor dokumentációjában](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)találja.

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.1

+ **Új funkciók**
  + Read_ * átalakítók használatával több adattár-/DataPath-vagy DataReference-forrást is olvashat.
  + A következő műveleteket végezheti el az oszlopokon egy új oszlop létrehozásához: osztás, padló, adatmegosztás, teljesítmény, hossz.
  + Az adat-előkészítés mostantól az Azure ML diagnosztikai csomag része, és alapértelmezés szerint naplózza a diagnosztikai adatokat.
    + A kikapcsoláshoz állítsa a környezeti változót True (igaz) értékre: DISABLE_DPREP_LOGGER

+ **Hibajavítások és javítások**
  + Továbbfejlesztett kód dokumentáció a gyakran használt osztályokhoz és függvényekhez.
  + Kijavított egy olyan hibát auto_read_file, amely nem tudta beolvasni az Excel-fájlokat.
  + A mappa felülírásának lehetősége a read_pandas_dataframeban.
  + A dotnetcore2 függőségek telepítésének jobb teljesítménye, valamint a Fedora 27/28 és az Ubuntu 1804 támogatása.
  + Továbbfejlesztettük az Azure-Blobok olvasásának teljesítményét.
  + Az oszlop típusú észlelés mostantól támogatja a Long típusú oszlopokat.
  + Kijavítva egy hiba, amelyben néhány dátumérték időbélyegként jelenik meg a Python datetime-objektumok helyett.
  + Kijavítva egy olyan hibát, amelyben egyes típusok száma nem egész számként, hanem párosként jelenik meg.


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK a Python v 1.0.21

+ **Új funkciók**
  + A *azureml. Core. Run. create_children* metódus lehetővé teszi több gyermek kis késleltetésű létrehozását egyetlen hívással.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning adat-előkészítési SDK v 1.1.0

+ **Változtatások megszakítása**
  + Az adat-előkészítő csomag fogalma elavult, és már nem támogatott. Ahelyett, hogy egy csomagban több adatfolyamok is megmaradjon, külön adatfolyamok maradhat.
    + Útmutató: [adatfolyamok-jegyzetfüzet megnyitása és mentése](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Új funkciók**
  + Az adat-előkészítő mostantól felismerheti az adott szemantikai típusnak megfelelő oszlopokat, és ennek megfelelően feloszthatja őket. A jelenleg támogatott STypes a következők: e-mail-cím, földrajzi koordináták (szélesség & hosszúság), IPv4-és IPv6-címek, USA-beli telefonszámok és US zip-kódok.
    + Útmutató: [szemantikai típusok jegyzetfüzet](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Az adat-előkészítő mostantól a következő műveleteket támogatja két numerikus oszlopból származó eredő oszlop létrehozásához: kivonás, szorzás, osztás és maradék.
  + A adatfolyam `verify_has_data()` meghívásával ellenőrizze, hogy a adatfolyam-e a végrehajtott rekordok létrehozásakor.

+ **Hibajavítások és javítások**
  + Mostantól megadhatja, hogy hány raktárhelyet használjon a hisztogram a numerikus oszlopok profiljaihoz.
  + A `read_pandas_dataframe` átalakítás most megköveteli, hogy a DataFrame karakterlánc-vagy byte-Type típusú oszlopnevek legyenek.
  + Kijavított egy hibát a `fill_nulls` átalakítóban, ahol az értékek nem lettek megfelelően kitöltve, ha az oszlop hiányzik.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK a Python v 1.0.18

 + **Változások**
   + Az azureml-tensorboard csomag helyettesíti a azureml-tensorboard.
   + Ebben a kiadásban beállíthat egy felhasználói fiókot a felügyelt számítási fürtön (amlcompute), miközben létrehozta. Ezt a tulajdonságok a létesítési konfigurációban való átadásával teheti meg. További részleteket az [SDK-dokumentációban](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)talál.

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.17

+ **Új funkciók**
  + A mostantól támogatja két numerikus oszlop hozzáadását egy eredményül kapott oszlop létrehozásához a kifejezés nyelvének használatával.

+ **Hibajavítások és javítások**
  + Javítottuk a random_split dokumentációját és paraméterének ellenőrzését.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.16

+ **Hibajavítás**
  + Kijavított egy egyszerű szolgáltatás hitelesítési problémáját, amelyet egy API-változás okozott.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK a Python v 1.0.17

+ **Új funkciók**

  + A Azure Machine Learning mostantól első osztályú támogatást biztosít a népszerű DNN-keretrendszer láncolásához. [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) osztály használatával a felhasználók egyszerűen betanítják és üzembe helyezhetik a Láncos modelleket.
    + Ismerje meg, hogyan [futtathatja az elosztott képzést a ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Ismerje meg, hogyan [futtathat hiperparaméter-hangolást a chainer használatával a HyperDrive segítségével](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning folyamatokkal bővült az adattár-módosítások alapján a folyamat futtatásának lehetősége. A folyamat [ütemezett jegyzetfüzete](https://aka.ms/pl-schedule) frissült a szolgáltatás megjelenítéséhez.

+ **Hibajavítások és javítások**
  + Azure Machine Learning-folyamatokat is támogatunk, amelyekkel beállíthatja a source_directory_data_store tulajdonságot egy kívánt adattárra (például blob Storage-ra) a [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)megadott [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) . Alapértelmezés szerint az Azure file Store-t használja a háttérként szolgáló adattárként, amely szabályozási problémákba ütközik, amikor nagy számú lépést hajt végre egyszerre.

### <a name="azure-portal"></a>Azure Portal

+ **Új funkciók**
  + Új fogd és vidd táblázat szerkesztői élmény a jelentésekhez. A felhasználók a megfelelő oszlopokat a tábla azon területére húzhatja, ahol megjelenik a táblázat előnézete. Az oszlopok átrendezhető.
  + Új naplófájlok megjelenítője
  + A műveletek lapon található kísérletekre, számításokra, modellekre, lemezképekre és központi telepítésekre mutató hivatkozások

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.15

+ **Új funkciók**
  + Az adat-előkészítő mostantól támogatja a adatfolyam származó fájlok adatfolyamként való írását. A Emellett lehetővé teszi a fájlok stream-neveinek módosítását új fájlnevek létrehozásához.
    + Útmutató: [a file Streams notebook használata](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Hibajavítások és javítások**
  + Nagyobb teljesítmény a t-Digest nagy adatkészleteken.
  + Az adat-előkészítő mostantól támogatja az adatok DataPath való olvasását.
  + Egy gyors kódolás a logikai és a numerikus oszlopokon is működik.
  + Egyéb egyéb hibajavítások.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK a Python v 1.0.15

+ **Új funkciók**
  + Azure Machine Learning folyamatokat hozzáadott AzureBatchStep ([notebook](https://aka.ms/pl-azbatch)), HyperDriveStep (notebook) és időalapú ütemezési funkció ([notebook](https://aka.ms/pl-schedule)).
  +  A DataTranferStep frissítve lett az Azure SQL Server és az Azure Database for PostgreSQL ([notebook](https://aka.ms/pl-data-trans)) használatához.

+ **Változások**
  + A `PublishedPipeline.get`javára elavult `PublishedPipeline.get_published_pipeline`.
  + A `Schedule.get`javára elavult `Schedule.get_schedule`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.12

+ **Új funkciók**
  + Az adat-előkészítő mostantól támogatja az Azure SQL Database-adatbázisokból való olvasást az adattár használatával.

+ **Változások**
  + Javítottuk a nagy mennyiségű adathoz tartozó bizonyos műveletek memóriájának teljesítményét.
  + `read_pandas_dataframe()` most meg kell adni `temp_folder`ot.
  + A `ColumnProfile` `name` tulajdonsága elavult – használjon `column_name` helyet.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK a Python v 1.0.10

+ **Módosítások**:
  + Az Azure ML SDK-nak már nincs Azure-CLI-csomagja függőségként. Pontosabban, az Azure-CLI-Core és az Azure-CLI-profil függőségei el lettek távolítva a azureml-Core-ból. A felhasználók a következő változásokat érintik:
    + Ha az "az login", majd az azureml-SDK használatával végzi az SDK-t, a böngésző vagy az eszköz kódjának naplózása még egyszer megtörténik. Nem használja az "az login" által létrehozott hitelesítő adatok állapotát.
    + Az Azure CLI-hitelesítéshez, például az "az login" használata esetén használja az _azureml. Core. Authentication. AzureCliAuthentication_ osztályt. Azure CLI-hitelesítés esetén a _pip az Azure-CLI telepítését_ a Python-környezetben, ahol a azureml-SDK telepítve van.
    + Ha az automatizáláshoz az "az login" szolgáltatást használja, javasoljuk, hogy az _azureml. Core. Authentication. ServicePrincipalAuthentication_ osztályt használja, mivel a azureml-SDK nem fogja használni az Azure CLI által létrehozott hitelesítő adatokat.

+ **Hibajavítások**: Ez a kiadás főleg kisebb hibajavításokat tartalmaz

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.8

+ **Hibajavítások**
  + Javítottuk az adatprofilok beolvasásának teljesítményét.
  + A hibajelentéssel kapcsolatos rögzített kisebb hibák.

### <a name="azure-portal-new-features"></a>Az Azure portal: új funkciók
+ A jelentések új, húzással kapcsolatos felhasználói felülete. A felhasználók a megfelelő típusú oszlopokat vagy attribútumokat a diagram területére húzhatja, ahol a rendszer automatikusan kiválasztja a megfelelő diagramtípust a felhasználó számára az adatok típusától függően. A felhasználók módosíthatják a diagram típusát más alkalmazható típusokra, illetve további attribútumokat adhatnak hozzá.

    Támogatott diagramok típusai:
    - Vonalas diagram
    - Hisztogram
    - Halmozott sávdiagram
    - Box-ábra
    - Pontdiagram
    - Buborék ábrázolása
+ A portál mostantól dinamikusan generál jelentéseket a kísérletekhez. Amikor egy felhasználó elküld egy kísérletet, a rendszer automatikusan létrehoz egy jelentést a naplózott metrikákkal és gráfokkal, hogy az összehasonlítás lehetővé váljon a különböző futtatások között.

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK a Python v 1.0.8

+ **Hibajavítások**: Ez a kiadás főleg kisebb hibajavításokat tartalmaz

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.7

+ **Új funkciók**
  + Az adattár fejlesztése (dokumentálva az [adattár](https://aka.ms/aml-data-prep-datastore-nb)útmutatójában)
    + Lehetőség van az Azure-fájlmegosztás és a ADLS-adattárolók beolvasására és írására.
    + Az adattárolók használatakor az adatelőkészítés mostantól támogatja az egyszerű szolgáltatásnév hitelesítés használatát az interaktív hitelesítés helyett.
    + A wasb és a wasbs URL-címek támogatása.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.6

+ **Hibajavítások**
  + Rögzített hiba a nyilvánosan olvasható Azure Blob-tárolók beolvasásával a Sparkban

## <a name="2018-12-20"></a>2018-12-20

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK a Python v 1.0.6
+ **Hibajavítások**: Ez a kiadás főleg kisebb hibajavításokat tartalmaz

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning adat-előkészítési SDK v 1.0.4

+ **Új funkciók**
  + a `to_bool` függvény mostantól lehetővé teszi, hogy a nem egyező értékeket a program a hibák értékére konvertálja. Ez a `to_bool` és `set_column_types`új alapértelmezett eltérési viselkedése, míg a korábbi alapértelmezett viselkedés a nem egyező értékek hamisra konvertálása.
  + `to_pandas_dataframe`hívásakor új lehetőség van a null/hiányzó értékek értelmezésére a numerikus oszlopokban, mint a NaN.
  + Bizonyos kifejezések visszatérési típusának további ellenőrzése, hogy a konzisztencia és a hibák korának legyenek.
  + Most meghívhatja a `parse_json`t, hogy egy oszlop értékeit JSON-objektumokként elemezze, és kibővítse őket több oszlopba.

+ **Hibajavítások**
  + Kijavított egy hibát, amely összeomlott `set_column_types` a Python 3.5.2-ben.
  + Kijavítva egy hiba, amely összeomlott az adattárhoz való csatlakozáskor egy pénzmosás-rendszerkép használatával.

+ **Frissítések**
  * [Például jegyzetfüzetek](https://aka.ms/aml-data-prep-notebooks) az első lépések oktatóanyagok, esettanulmányok és útmutatók.

## <a name="2018-12-04-general-availability"></a>2018-12-04: általánosan elérhető

A Azure Machine Learning már általánosan elérhető.

### <a name="azure-machine-learning-compute"></a>Az Azure Machine Learning Compute
Ebben a kiadásban bejelentjük az új felügyelt számítási élményt a [Azure Machine learning számítási](how-to-set-up-training-targets.md#amlcompute)feladatokon keresztül. Ez a számítási cél a Azure Machine Learning Azure Batch AI számítását váltja fel.

Ez a számítási cél:
+ A modell betanítására és a Batch-következtetésre/pontozásra szolgál.
+ Egy-a több csomópontos számítás
+ A fürt kezelése és a feladatütemezés a felhasználó számára
+ Alapértelmezés szerint az autoskálázás
+ A processzor-és a GPU-erőforrások támogatása
+ Alacsony prioritású virtuális gépek használatát teszi lehetővé csökkentett áron

Az Azure Machine Learning Compute a Python, az Azure portal vagy a parancssori felület használatával hozható létre. A munkaterület a régióban kell létrehozni, és bármilyen egyéb munkaterülethez nem csatolható. Ez a számítási cél egy Docker-tárolót használ a futtatásához, és a függőségek csomagjaival replikálja ugyanazt a környezetet az összes csomóponton.

> [!Warning]
> Azt javasoljuk, hogy használata az Azure Machine Learning Compute új munkaterület létrehozása. Nincs távoli előfordulhat, hogy a felhasználók próbál létrehozni egy meglévő munkaterületet az Azure Machine Learning COMPUTE számítási hiba láthatják. A munkaterületen található meglévő számítási továbbra is változatlanul működik.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Az Azure Machine Learning SDK for Python v1.0.2
+ **Változtatások megszakítása**
  + Ettől a kiadástól kezdve megszüntetjük a virtuális gép létrehozása az Azure Machine Learning támogatása. Továbbra is csatolhat a virtuális gép meglévő felhő vagy egy távoli helyszíni kiszolgáló.
  + Emellett megszüntetjük BatchAI, mindegyike támogatnia kell az Azure Machine Learning Compute révén most támogatása.

+ **Új**
  + A machine learning-folyamatokat:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Frissített**
  + A machine learning-folyamatokat:
    + A [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) most elfogadja a runconfig
    + A [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) mostantól egy SQL-adatforrásból másolható
    + Az SDK-t létrehozni és frissíteni a közzétett folyamatok futó ütemezések funkció ütemezéséhez

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Az Azure Machine Learning adat-előkészítési SDK v0.5.2
+ **Változtatások megszakítása**
  * a `SummaryFunction.N` átnevezve `SummaryFunction.Count`re.

+ **Hibajavítások**
  * A legújabb pénzmosás-jogkivonatot használja az adattárolók távoli futtatáskor történő beolvasása és írása során. Korábban az AML futtatása Token frissül a Python, ha az Adatelőkészítés-modul nem frissül a frissített AML futtatása jogkivonat a.
  * További világosabb hibaüzenetek
  * a to_spark_dataframe () nem fog összeomlani, ha a Spark `Kryo` szerializálást használ
  * Értékek száma vizsgáló most már 1000-nél több egyedi értékeket jeleníti meg
  * Véletlenszerű Split már nem sikertelen lesz, ha az eredeti adatfolyam nem rendelkezik egy nevet

+ **További információ**
  * [Azure Machine Learning adat-előkészítési SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Docs és jegyzetfüzetek
+ Gépi Tanulási folyamatok
  + Új és frissített jegyzetfüzetek a folyamatok, a kötegek hatóköre és a stílus-átadási példákkal való ismerkedéshez: https://aka.ms/aml-pipeline-notebooks
  + Ismerje meg, hogyan [hozhatja létre első folyamatát](how-to-create-your-first-pipeline.md)
  + Útmutató a [Batch-előrejelzések folyamatokkal történő futtatásához](how-to-use-parallel-run-step.md)
+ Számítási cél Azure Machine Learning
  + A [minta jegyzetfüzetek](https://aka.ms/aml-notebooks) mostantól frissülnek az új felügyelt számítás használatára.
  + [További tudnivalók a számítási feladatokról](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Az Azure portal: új funkciók
+ [Azure Machine learning számítási](how-to-set-up-training-targets.md#amlcompute) típusok létrehozása és kezelése a portálon.
+ A kvóta használatának figyelése és a Azure Machine Learning számításhoz szükséges [kvóta igénylése](how-to-manage-quotas.md) .
+ Azure Machine Learning számítási fürt állapotának valós idejű megtekintése.
+ Virtuálishálózat-támogatást az Azure Machine Learning COMPUTE számítási és az Azure Kubernetes Service létrehozásakor lett hozzáadva.
+ Futtassa újra a közzétett folyamatokat a meglévő paraméterekkel.
+ Új [automatizált gépi tanulási diagramok](how-to-understand-automated-ml.md) a besorolási modellekhez (lift, nyereség, kalibrálás, funkció fontossági diagramja modell-magyarázattal) és regressziós modellekkel (maradványok és funkciók fontossági diagramja a modell magyarázatával).
+ A folyamatok tekinthetnek meg az Azure Portalon




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Az Azure Machine Learning SDK for Python v0.1.80

+ **Változtatások megszakítása**
  * a *azureml. Train. Widgets* névtér át lett helyezve a *azureml. Widgets*-re.
  * *azureml. Core. számítás. a AmlCompute* a következő osztályokat elavult: *azureml. Core. számítás. BatchAICompute* és *azureml. Core. számítás. DSVMCompute*. Az utóbbi osztály törlődni fog a következő kiadásokban. A AmlCompute osztály mostantól könnyebben definícióval rendelkezik egyszerűen egy vm_size és a max_nodes van szüksége, és automatikus méretezése a max_nodes 0-tól a fürthöz, amikor a rendszer elküld egy feladatot. A [mintául szolgáló jegyzetfüzetek](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) frissítve lettek ezzel az információval, és használati példákat kell megadni. Reméljük, mint például az egyszerűsítés és a egy későbbi kiadásban lesz több izgalmas funkciók számos!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Az Azure Machine Learning adat-előkészítési SDK v0.5.1

További információ az adat-előkészítő SDK-ról a [dokumentációs dokumentumok](https://aka.ms/data-prep-sdk)beolvasásával.
+ **Új funkciók**
   * Létrehozott egy új Adatelőkészítés CLI hajtsa végre az Adatelőkészítés-csomagok és az adatkészlet vagy adatfolyam adatok profiljának megtekintése
   * Újratervezett SetColumnType API használhatóság javítása érdekében
   * Átnevezett smart_read_file auto_read_file
   * Mostantól tartalmazza a Adatprofil döntés és értékek
   * A rétegzett mintavételi is minta
   * CSV-fájlokat tartalmazó zip-fájlokat is olvashat
   * Feloszthatja az adathalmazokat a Wise véletlenszerű felosztással (például test-Train készletekbe)
   * Az összes oszlop adattípusát lekérheti egy adatfolyam vagy egy adatprofilból a `.dtypes` meghívásával
   * Lekérheti a sorok darabszámát egy adatfolyam vagy egy adatprofilból a `.row_count` meghívásával

+ **Hibajavítások**
   * Mennyi ideig dupla átalakítás rögzített
   * Rögzített vyhodnocení után bármely oszlop hozzáadása
   * FuzzyGrouping, ahol azt szeretné nem csoportok egyes esetekben képes észlelni kijavítva
   * Több oszlopot rendezési sorrend figyelembe veszi a rögzített rendezési funkció
   * A rögzített és/vagy kifejezéseket a `pandas` kezeli.
   * Rögzített olvasásakor dbfs elérési útja
   * Hibaüzenetek a könnyebb érthetőség érdekében
   * Most már nem sikerül a távoli számítási cél beolvasása egy pénzmosás-token használatával
   * Most már nem meghiúsul, a Linux-dsvm-hez
   * Most már nem összeomlik, ha nem sztring típusú értékek a következők az olyan predikátumokban karakterlánc
   * Most már kezeli a helyességi feltétel hibákat, amikor adatfolyamot megfelelően kell-e sikertelen
   * Most már támogatja az Azure databricks szolgáltatásban dbutils csatlakoztatott tárolási helyek

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure Portal
A Azure Machine Learning Azure Portal a következő frissítésekkel rendelkezik:
  * Új **folyamatok** lap a közzétett folyamatokhoz.
  * Támogatás hozzáadva a csatolása egy meglévő HDInsight-fürt egy számítási célnak.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Az Azure Machine Learning SDK for Python v0.1.74

+ **Változtatások megszakítása**
  * \* A munkaterület. compute_targets, az adattárolók, a kísérletek, a képek, a modellek és a *webszolgáltatások* a metódusok helyett tulajdonságok. Például cserélje le a *munkaterület. compute_targets ()* *munkaterületre. compute_targets*.
  * A *Run. get_context* a *Run. get_submitted_run*elavult. Az utóbbi módszer a következő kiadásokban törlődni fog.
  * A *PipelineData* osztály most datastore_name helyett paraméterként egy adattár-objektumot vár. Hasonlóképpen, a *folyamat* default_datastore_name helyett a default_datastore is elfogadja.

+ **Új funkciók**
  * A Azure Machine Learning-folyamatok [minta notebookja](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) már MPI-lépéseket használ.
  * A Jupyter-notebookokhoz RunDetails widget megjelenítéséhez a vizualizációt, a folyamat frissül.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Az Azure Machine Learning adat-előkészítési SDK v0.4.0

+ **Új funkciók**
  * Adatprofil hozzáadott száma
  * Értékek száma és a hisztogram már elérhető
  * További. percentilisei az Adatprofil
  * A középérték érhető el összegzés
  * Mostantól támogatott Python 3.7.
  * Amikor menti egy adatfolyam, amely tartalmazza az adattárolók Adatelőkészítés csomaghoz, az adattár-információk maradnak az Adatelőkészítés-csomag részeként
  * Mostantól támogatott az adattárhoz való írás

+ **Hiba javítva**
  * a 64 bites, előjel nélküli egész túlcsordulások mostantól megfelelően kezelhetők Linux rendszeren
  * Egyszerű szöveges fájlok smart_read rögzített helytelen felirat
  * Karakterlánc oszloptípus most már megjelenik-e metrikák megtekintése
  * Száma már rögzített egyesével helyett egyetlen FieldType leképezve ValueKinds megjelenítése
  * Write_to_csv már nem sikertelen lesz, amikor az elérési út egy karakterláncként van megadva.
  * Cserélje le használatakor "keresése" üresen hagyja már nem meghiúsul

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Az Azure Machine Learning SDK for Python v0.1.68

+ **Új funkciók**
  * Több-bérlős támogatás új munkaterület létrehozásakor.

+ **Kijavított hibák**
  * A webszolgáltatás telepítésekor már nem kell rögzítenie a pynacl-függvénytár verzióját.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Az Azure Machine Learning adat-előkészítési SDK v0.3.0

+ **Új funkciók**
  * Hozzáadott metódus transform_partition_with_file(script_path), amely lehetővé teszi a felhasználók számára, hogy az elérési útját egy Python-fájlt, végrehajtására

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Az Azure Machine Learning SDK for Python v0.1.65
A [0.1.65 verzió](https://pypi.org/project/azureml-sdk/0.1.65) új funkciókat, több dokumentációt, hibajavítást és több [minta jegyzetfüzetet](https://aka.ms/aml-notebooks)tartalmaz.

Az ismert hibák és a megkerülő megoldások megismeréséhez tekintse meg [az ismert problémák listáját](resource-known-issues.md) .

+ **Változtatások megszakítása**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, Workspace.web_services visszatérési szótár, korábban visszaadott listában. Lásd: [azureml. Core. Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API-dokumentáció.

  * Automatizált Machine Learning Normalizált négyzetes hiba eltávolítja az elsődleges metrikákat.

+ **HyperDrive**
  * Különböző HyperDrive hibajavítások a Bayes, a javított teljesítménye, metrikák hívások beolvasása.
  * 1\.9 Tensorflow 1.10 verzióról
  * Docker-rendszerkép optimalizálást a hidegindítás.
  * A feladatok mostantól akkor is helyes állapotot jelentenek, ha a nullától eltérő hibakóddal kilépnek.
  * RunConfig attribútum érvényesítése, az SDK-t.
  * Futtatás HyperDrive objektum Futtatás szabályos hasonló Mégse támogatja: nincs szükség adja át a paramétereket.
  * Widget fejlesztései legördülő értékeit az elosztott futtatási és HyperDrive futtatások állapotának fenntartásához.
  * Paraméterkiszolgáló TensorBoard és a többi napló-fájloknak támogatniuk rögzíteni.
  * Intel(R) MPI szolgáltatás oldalán támogatja.
  * Bugfix a finomhangolással az elosztott futtatási javítás BatchAI az érvényesítés során.
  * Környezet Manager mostantól azonosítja az elsődleges példány.

+ **Azure Portali élmény**
  * Futtatás részletei log_table() és log_row() támogatottak.
  * Diagramok automatikus létrehozása a táblázatok és sorok számára 1, 2 vagy 3 numerikus oszlop és egy nem kötelezően megadandó oszlop esetében.

+ **Automatizált Machine Learning**
  * Továbbfejlesztett hiba- és dokumentáció
  * Rögzített futtatási tulajdonság lekérése teljesítménybeli problémák.
  * Rögzített futtatási a probléma továbbra is.
  * Rögzített :::no-loc text="ensembling"::: iterációs problémák.
  * Rögzített méretű betanítási módosítási hiba a MAC OS.
  * Egyszerűsítés makró átlagos Pull-kérelem/ROC-görbe egyéni ellenőrzési forgatókönyvben.
  * Eltávolítja a felesleges index logikát.
  * Szűrő távolítva get_output API-t.

+ **Folyamatok**
  * A metódus egy folyamatot, közvetlenül közzé anélkül, hogy az első futtatás végrehajtási Pipeline.publish() hozzá.
  * A rendszer hozzáadta a PipelineRun. get_pipeline_runs () metódust a közzétett folyamatból generált folyamat-futtatások beolvasásához.

+ **Projekt agyhullám**
  * FPGA-kban elérhető új AI modelleket frissített támogatása.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Az Azure Machine Learning adat-előkészítési SDK v0.2.0
A [0.2.0 verziója](https://pypi.org/project/azureml-dataprep/0.2.0/) a következő funkciókat és hibajavításokat tartalmazza:

+ **Új funkciók**
  * Egy gyakori kódolási támogatása
  * Ki osztóérték átalakító támogatása

+ **Hiba javítva:**
  * Bármely tornádó verziójával működik nincs szükség alacsonyabb szolgáltatásszintre váltásához az tornádó verzió
  * Értékek száma az összes értéket, nem csak az első három

## <a name="2018-09-public-preview-refresh"></a>2018-09 (nyilvános előzetes verzió frissítése)

Azure Machine Learning új, frissített kiadása: További információ erről a kiadásról: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Következő lépések

Olvassa el az [Azure Machine Learning](overview-what-is-azure-ml.md) áttekintését.
