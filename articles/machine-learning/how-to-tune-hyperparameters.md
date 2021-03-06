---
title: A modell hiperparaméterek beállítása hangolása
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning használatával hatékonyan hangolhatja a Deep learning-és gépi tanulási modell hiperparaméterek beállítása. Megtudhatja, hogyan határozhatja meg a paraméterek keresési területét, hogyan adhat meg egy elsődleges metrikát az optimalizáláshoz, és korai megszakítást hajthat végre a rosszul futó futtatások.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 4ca9cac00b9d00dcdbbd27f2fe769de2983c13ae
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75536641"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>A modell hiperparaméterek beállítása hangolása Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning használatával hatékonyan hangolhatja be a modell hiperparaméterek beállítása.  A hiperparaméter hangolás a következő lépéseket tartalmazza:

* A paraméter keresési területének megadása
* Az optimalizáláshoz használandó elsődleges metrika meghatározása  
* A gyengén teljesítő futtatásokhoz tartozó korai befejezési feltételek megadása
* Erőforrások lefoglalása a hiperparaméter hangolásához
* Kísérlet elindítása a fenti konfigurációval
* A betanítási futtatások megjelenítése
* Válassza ki a modell legjobban teljesítő konfigurációját

## <a name="what-are-hyperparameters"></a>Mi a hiperparaméterek beállítása?

A hiperparaméterek beállítása olyan állítható paraméterek, amelyekkel a betanítási folyamatra vonatkozó modelleket lehet betanítani. Ha például mély neurális hálózatot szeretne betanítani, a modell betanítása előtt a hálózatban lévő rejtett rétegek számát és az egyes rétegek csomópontjainak számát kell meghatároznia. Ezek az értékek általában állandó maradnak a betanítási folyamat során.

Mélyreható tanulási/gépi tanulási forgatókönyvekben a modell teljesítménye nagymértékben függ a kiválasztott hiperparaméter-értékektől. A hiperparaméter-feltárás célja, hogy a különböző hiperparaméter konfigurációkon keressen olyan konfiguráció megtalálásához, amely a legjobb teljesítményt eredményezi. A hiperparaméter-felderítési folyamat általában aprólékosan manuális, mivel a keresési terület óriási, és az egyes konfigurációk kiértékelése költséges lehet.

Azure Machine Learning segítségével hatékony módon automatizálhatja a hiperparaméter-feltárást, így jelentős időt és erőforrásokat takaríthat meg. Megadhatja a hiperparaméter-értékek tartományát és a betanítási futtatások maximális számát. A rendszer ezután automatikusan több egyidejű futtatást indít el különböző paraméterekkel, és megkeresi a legjobb teljesítményt eredményező konfigurációt a választott mérőszám alapján. A gyengén teljesítő képzések futtatása automatikusan megszűnik, ami csökkenti a számítási erőforrások pazarlását. Ezek az erőforrások Ehelyett a más hiperparaméter-konfigurációk megismerésére szolgálnak.


## <a name="define-search-space"></a>Keresési terület meghatározása

A hiperparaméterek beállítása automatikus finomhangolása az egyes hiperparaméter meghatározott értékek tartományának feltárásával.

### <a name="types-of-hyperparameters"></a>Hiperparaméterek beállítása típusai

Minden hiperparaméter lehet különálló vagy folyamatos, és egy [paraméter-kifejezés](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py)által ismertetett értékek eloszlása.

#### <a name="discrete-hyperparameters"></a>Különálló hiperparaméterek beállítása 

A diszkrét hiperparaméterek beállítása a különálló értékek között `choice` vannak megadva. `choice` a következőket teheti:

* egy vagy több vesszővel tagolt érték
* egy `range` objektum
* tetszőleges `list` objektum


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Ebben az esetben a `batch_size` a [16, 32, 64, 128] érték valamelyikét veszi igénybe, és a `number_of_hidden_layers` a [1, 2, 3, 4] érték egyikére kerül.

A speciális diszkrét hiperparaméterek beállítása eloszlás használatával is megadható. A következő disztribúciók támogatottak:

* `quniform(low, high, q)` – olyan értéket ad vissza, mint a Round (egységes (alacsony, magas)/q) * q
* `qloguniform(low, high, q)` – egy olyan értéket ad vissza, mint a Round (exp (egységes (alacsony, magas))/q
* `qnormal(mu, sigma, q)` – olyan értéket ad vissza, mint a Round (normál (MU, Sigma)/q) * q
* `qlognormal(mu, sigma, q)` – olyan értéket ad vissza, mint a Round (exp (normál (MU, Sigma))/q) * q

#### <a name="continuous-hyperparameters"></a>Folyamatos hiperparaméterek beállítása 

A folyamatos hiperparaméterek beállítása eloszlása az értékek folytonos tartományában történik. A támogatott disztribúciók a következők:

* `uniform(low, high)` – az alacsony és a magas közötti egységesen elosztott értéket adja vissza
* `loguniform(low, high)` – az exp (egységes (alacsony, magas)) alapján rajzolt értéket adja vissza, hogy a visszatérési érték logaritmusa egységesen legyen elosztva.
* `normal(mu, sigma)` – egy olyan valós értéket ad vissza, amelyet általában a Mean és a standard szórás Sigma
* `lognormal(mu, sigma)` – az exp (normál (MU, Sigma)) szerint rajzolt értéket adja vissza, hogy a visszatérési érték logaritmusa szabályosan legyen elosztva.

Egy példa a paraméter területének meghatározására:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Ez a kód két paramétert definiáló keresési helyet határoz meg – `learning_rate` és `keep_probability`. a `learning_rate` normál eloszlása a 10-es értékkel, a szórás pedig 3. a `keep_probability` egy egységes eloszlású, 0,05-es minimális értékkel és 0,1-es maximális értékkel rendelkezik.

### <a name="sampling-the-hyperparameter-space"></a>A hiperparaméter-terület mintavételezése

Azt is megadhatja, hogy a mintavételi módszer hogyan használható a hiperparaméter terület definícióján keresztül. Azure Machine Learning támogatja a véletlenszerű mintavételezést, a rácsos mintavételezést és a Bayes mintavételezést.

#### <a name="picking-a-sampling-method"></a>Mintavételi módszer kiválogatása

* A rácsos mintavételezést akkor lehet használni, ha a hiperparaméter terület a diszkrét értékek közül választhat, és ha elegendő költségvetése van a megadott keresési terület összes értékének kimerítő kereséséhez. Emellett az egyik a gyengén teljesítő futtatások automatizált korai megszakítását is használhatja, ami csökkenti az erőforrások pazarlását.
* A véletlenszerű mintavételezés lehetővé teszi, hogy a hiperparaméter terület diszkrét és folytonos hiperparaméterek beállítása is tartalmazzon. A gyakorlatban jó eredményeket hoz létre a legtöbb esetben, és lehetővé teszi a gyengén teljesítő futtatások automatizált korai megszüntetését. Egyes felhasználók véletlenszerű mintavételezéssel végzik a kezdeti keresést, majd a iteratív pontosítják a keresési helyet az eredmények javítása érdekében.
* A Bayes mintavételezés a korábbi minták ismereteit használja a hiperparaméter értékek kiválasztásakor, és hatékonyan megpróbálja javítani a jelentett elsődleges metrikát. A Bayes-alapú mintavételezés akkor ajánlott, ha elegendő költségvetéssel rendelkezik a hiperparaméter-terület megismeréséhez – a legjobb eredmények a Bayes-as mintavételezéssel azt javasoljuk, hogy legfeljebb 20 alkalommal fusson a beállított hiperparaméterek beállítása száma. Vegye figyelembe, hogy a Bayes mintavételezés jelenleg nem támogatja a korai megszakítási szabályzatot.

#### <a name="random-sampling"></a>Véletlenszerű mintavétel

Véletlenszerű mintavételezés esetén a hiperparaméter értékek véletlenszerűen vannak kiválasztva a megadott keresési területről. A [véletlenszerű mintavételezés](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) lehetővé teszi, hogy a keresési terület diszkrét és folytonos hiperparaméterek beállítása is tartalmazzon.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Rács mintavételezése

A [rács mintavételezése](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) egyszerű rácsos keresést végez a definiált keresési terület minden lehetséges értékén. Csak `choice`használatával megadott hiperparaméterek beállítása használható. A következő terület például összesen hat mintát tartalmaz:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayes-féle mintavételezés

A [Bayes mintavételezés](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) a Bayes optimalizációs algoritmuson alapul, és intelligens döntéseket tesz a hiperparaméter értékeinek a következőre történő mintavételezéséhez. A mintát az előző minták végrehajtása alapján választja ki, így az új minta javítja a jelentett elsődleges metrikát.

A Bayes mintavételezés használatakor az egyidejű futtatások száma hatással van a hangolási folyamat hatékonyságára. Az egyidejű futtatások kisebb száma általában nagyobb mintavételezési konvergenciát eredményezhet, mivel a kisebb párhuzamosságok növelik a korábban befejezett futtatások előnyeit kihasználó futtatások számát.

A Bayes mintavételezés csak `choice`, `uniform`és `quniform` eloszlást támogat a keresési térben.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> A Bayes mintavételezés nem támogatja a korai megszakítási házirendet (lásd: [a korai megszakítási házirend meghatározása](#specify-early-termination-policy)). Ha a Bayes paraméter mintavételét használja, állítsa be `early_termination_policy = None`, vagy hagyja ki a `early_termination_policy` paramétert.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Elsődleges metrika meghatározása

Itt adhatja meg azt az [elsődleges metrikát](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) , amelyet a hiperparaméter-hangolási kísérlettel optimalizálni szeretne. Minden betanítási Futtatás kiértékelése az elsődleges metrika esetében történik. Nem megfelelően végrehajtott futtatások (ha az elsődleges metrika nem felel meg a korai megszakítási szabályzat által meghatározott feltételeknek), a rendszer leállítja. Az elsődleges metrika neve mellett megadhatja az optimalizálás célját is – függetlenül attól, hogy maximalizálni vagy csökkenteni szeretné az elsődleges metrikát.

* `primary_metric_name`: az optimalizálni kívánt elsődleges metrika neve. Az elsődleges metrika nevének pontosan meg kell egyeznie a betanítási parancsfájl által naplózott metrika nevével. Lásd: [a hiperparaméter hangolásának naplózási mérőszámai](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: lehet `PrimaryMetricGoal.MAXIMIZE` vagy `PrimaryMetricGoal.MINIMIZE`, és meghatározza, hogy az elsődleges metrika maximalizálva vagy Lekicsinyítve legyen a futtatások kiértékelése során. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

A futtatások optimalizálása a "pontosság" maximalizálása érdekében.  Ügyeljen rá, hogy a betanítási parancsfájlban naplózza ezt az értéket.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Hiperparaméter hangolásának naplózási mérőszámai

A modell betanítási parancsfájljának a modell betanítása során be kell jelentkeznie a megfelelő mérőszámokra. A hiperparaméter hangolás konfigurálásakor meg kell adnia a futtatási teljesítmény kiértékeléséhez használandó elsődleges metrikát. (Lásd: [elsődleges metrika meghatározása az optimalizáláshoz](#specify-primary-metric-to-optimize).)  A betanítási parancsfájlban be kell jelentkeznie ezt a metrikát, hogy elérhető legyen a hiperparaméter hangolási folyamat számára.

Naplózza ezt a metrikát a betanítási parancsfájlban az alábbi kódrészlettel:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

A betanítási parancsfájl kiszámítja a `val_accuracy`, és "pontosságként" naplózza, amelyet elsődleges metrikaként használ a rendszer. Minden alkalommal, amikor a metrika be van jelentkezve, a hiperparaméter tuning szolgáltatás fogadja. A modell fejlesztője határozza meg, hogy milyen gyakran jelentse ezt a metrikát.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Korai megszakítási szabályzat meghatározása

A gyengén teljesítő futtatások automatikus leállítása egy korai megszakítási házirenddel. A megszakítás csökkenti az erőforrások pazarlását, és ehelyett ezeket az erőforrásokat használja a többi paraméter-konfiguráció feltárásához.

Korai megszakítási szabályzat használatakor a következő paramétereket állíthatja be a szabályzatok alkalmazása esetén:

* `evaluation_interval`: a házirend alkalmazásának gyakorisága. Minden alkalommal, amikor a betanítási parancsfájl egy intervallumként naplózza az elsődleges metrikát. Így az 1. `evaluation_interval` minden alkalommal alkalmazza a szabályzatot, amikor a betanítási parancsfájl az elsődleges metrikát jelenti. A 2 `evaluation_interval` a szabályzatot minden más alkalommal alkalmazza, amikor a betanítási parancsfájl az elsődleges metrikát jelenti. Ha nincs megadva, `evaluation_interval` alapértelmezett értéke 1.
* `delay_evaluation`: az első szabályzat kiértékelését késlelteti a megadott számú intervallumra vonatkozóan. Ez egy opcionális paraméter, amely lehetővé teszi, hogy az összes konfiguráció a kezdeti minimális számú intervallumra fusson, elkerülve a képzések korai megszakítását. Ha meg van adva, a házirend a evaluation_interval minden olyan többszörösét alkalmazza, amely nagyobb vagy egyenlő, mint delay_evaluation.

Azure Machine Learning a következő korai megszakítási házirendeket támogatja.

### <a name="bandit-policy"></a>Bandita-szabályzat

A [Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) egy tartalékidő-tényező/Slack-mennyiség és a kiértékelési időköz alapján felmondási szabályzat. A házirend korán leáll minden olyan futtatásnál, amelynél az elsődleges metrika nem a megadott tartalékidő-faktor/Slack-értéken belül van, a legjobb teljesítményű képzések futtatására. A következő konfigurációs paramétereket veszi figyelembe:

* `slack_factor` vagy `slack_amount`: a tartalékidő a legjobban teljesítő képzések esetében engedélyezett. `slack_factor` megadja az engedélyezett tartalékidőt arányként. `slack_amount` a megengedett tartalékidőt abszolút értékként adja meg egy arány helyett.

    Tegyük fel például, hogy a rendszer a 10. intervallumban alkalmazza a Bandit-szabályzatot. Tegyük fel, hogy a legjobb teljesítményű Futtatás a 10-es intervallumban egy elsődleges metrika 0,8, amelynek célja az elsődleges metrika maximalizálása. Ha a házirendet a 0,2-es `slack_factor` értékkel adták meg, akkor minden olyan képzés fut, amelynek a 10. intervallumhoz tartozó legjobb mérőszáma kevesebb, mint 0,66 (0,8/(1 +`slack_factor`)). Ha helyette a szabályzatot a 0,2-es `slack_amount`, a betanítási folyamat, amelynek a 10. intervallumának legjobb mérőszáma kisebb, mint 0,6 (0,8-`slack_amount`), a rendszer leállítja.
* `evaluation_interval`: a házirend alkalmazásának gyakorisága (opcionális paraméter).
* `delay_evaluation`: az első szabályzat kiértékelését késlelteti a megadott számú intervallumra (opcionális paraméter).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Ebben a példában a korai megszakítási szabályzatot minden intervallumban alkalmazza a rendszer, amikor a mérőszámok jelentése megtörténik, az 5. értékelési intervallumtól kezdődően. Minden olyan Futtatás, amelynek a legjobb metrikája kisebb, mint (1/(1 + 0,1), vagy a legjobb teljesítményű Futtatás 91%-a leáll.

### <a name="median-stopping-policy"></a>Középérték leállítása házirend

A [középérték leállítása](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) a futtatások által jelentett elsődleges metrikák futtatási átlagán alapuló korai megszakítási házirend. Ez a szabályzat az összes betanítási Futtatás átlagát számítja ki, és leáll, amelynek teljesítménye rosszabb, mint a futó átlagok középértéke. Ez a szabályzat a következő konfigurációs paramétereket veszi figyelembe:
* `evaluation_interval`: a házirend alkalmazásának gyakorisága (opcionális paraméter).
* `delay_evaluation`: az első szabályzat kiértékelését késlelteti a megadott számú intervallumra (opcionális paraméter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Ebben a példában a korai megszakítási szabályzatot minden olyan intervallumban alkalmazni kell, amely az 5. értékelési intervallumtól kezdődően történik. A rendszer az 5. intervallumban leállítja a futtatást, ha a legjobb elsődleges metrikája rosszabb, mint a futó átlagok középértéke a 1:5-as időszakra az összes betanítási futtatás során.

### <a name="truncation-selection-policy"></a>Csonkítás kiválasztási szabályzata

A [csonkítás kiválasztása](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) megszakítja a legalacsonyabb végrehajtású futtatások adott százalékát az egyes értékelési intervallumokban. A futtatások összehasonlítása az elsődleges metrika teljesítményének és a legalacsonyabb X%-nak az alapján történik. A következő konfigurációs paramétereket veszi figyelembe:

* `truncation_percentage`: a legalacsonyabb végrehajtású futtatások százalékos aránya az egyes értékelési intervallumok leállításához. 1 és 99 közötti egész számot kell megadni.
* `evaluation_interval`: a házirend alkalmazásának gyakorisága (opcionális paraméter).
* `delay_evaluation`: az első szabályzat kiértékelését késlelteti a megadott számú intervallumra (opcionális paraméter).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Ebben a példában a korai megszakítási szabályzatot minden olyan intervallumban alkalmazni kell, amely az 5. értékelési intervallumtól kezdődően történik. A rendszer az 5. intervallumban leállítja a futtatást, ha az 5. intervallumbeli teljesítménye az 5. intervallumban az összes Futtatás legalacsonyabb 20%-ában van.

### <a name="no-termination-policy"></a>Nincs megszakítási szabályzat

Ha azt szeretné, hogy az összes betanítási Futtatás befejezésre fusson, állítsa a szabályzatot none értékre. Ennek hatására a rendszer nem alkalmazza a korai megszakítási szabályzatot.

```Python
policy=None
```

### <a name="default-policy"></a>Alapértelmezett házirend

Ha nincs megadva házirend, a hiperparaméter hangolási szolgáltatás lehetővé teszi, hogy az összes tanítás fusson a befejezésig.

### <a name="picking-an-early-termination-policy"></a>Korai megszakítási szabályzat kiválasztása

* Ha olyan konzervatív szabályzatot keres, amely megtakarítást biztosít az ígéretes feladatok megszakítása nélkül, használhat egy középértékes leállítási szabályzatot `evaluation_interval` 1 és `delay_evaluation` 5 használatával. Ezek a konzervatív beállítások, amelyek körülbelül 25%-35%-os megtakarítást biztosítanak az elsődleges metrika elvesztése nélkül (kiértékelési adatok alapján).
* Ha a korai felmondásban agresszívebb megtakarítást keres, akkor a Bandit-szabályzatot egy szigorúbb (kisebb) megengedett tartalékidő-vagy csonkolt kiválasztási szabályzattal is használhatja, amely nagyobb mennyiségű csonkítás százalékos arányt biztosít.

## <a name="allocate-resources"></a>Erőforrások lefoglalása

Az erőforrás-költségkeretet a hiperparaméter hangolási kísérlethez a teljes képzési futtatások maximális számának megadásával szabályozhatja.  Opcionálisan megadhatja a hiperparaméter-hangolási kísérlet maximális időtartamát.

* `max_total_runs`: a létrehozandó betanítási futtatások maximális száma. Felső határ – előfordulhat, hogy kevesebb fut, például ha a hiperparaméter terület véges, és kevesebb mintát tartalmaz. 1 és 1000 közötti számnak kell lennie.
* `max_duration_minutes`: a hiperparaméter-hangolási kísérlet maximális időtartama percben. A paraméter nem kötelező, és ha van, akkor a rendszer automatikusan megszakítja az ezen időtartam után futtatni kívánt futtatásokat.

>[!NOTE] 
>Ha a `max_total_runs` és a `max_duration_minutes` is meg van adva, a hiperparaméter hangolási kísérlet akkor leáll, amikor elérik az első két küszöbértéket.

Emellett adja meg a betanítási futtatások maximális számát, hogy az a hiperparaméter hangolási keresés során egyidejűleg fusson.

* `max_concurrent_runs`: a futtatott futtatások maximális száma egy adott pillanatban. Ha nincs megadva, az összes `max_total_runs` párhuzamosan fog elindulni. Ha meg van adva, egy 1 és 100 közötti számnak kell lennie.

>[!NOTE] 
>Az egyidejű futtatások száma a megadott számítási célra elérhető erőforrásokon van lefuttatva. Ezért győződjön meg arról, hogy a számítási cél rendelkezik a kívánt egyidejűséghez rendelkezésre álló erőforrásokkal.

Erőforrások lefoglalása a hiperparaméter hangoláshoz:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Ez a kód úgy konfigurálja a hiperparaméter hangolási kísérletet, hogy legfeljebb 20 teljes futtatást használjon, egyszerre négy konfigurációt futtatva.

## <a name="configure-experiment"></a>Kísérlet konfigurálása

[Konfigurálja a hiperparaméter hangolási](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) kísérletet a megadott hiperparaméter keresési területtel, a korai megszakítási házirenddel, az elsődleges metrikával és az erőforrás-elosztással a fenti fejezetekben. Továbbá adjon meg egy `estimator`, amely a mintául szolgáló hiperparaméterek beállítása lesz meghívva. A `estimator` ismerteti a futtatott képzési szkriptet, az erőforrások/feladatok (egy vagy több GPU) és a használni kívánt számítási célt. Mivel a hiperparaméter-hangolási kísérletre vonatkozó Egyidejűség a rendelkezésre álló erőforrásokon van leképezve, győződjön meg arról, hogy a `estimator`ben megadott számítási cél elegendő erőforrással rendelkezik a kívánt egyidejűséghez. (További információ a becslések: [modellek betanítása](how-to-train-ml-models.md).)

Konfigurálja a hiperparaméter hangolási kísérletet:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>Kísérlet elküldése

Miután meghatározta a hiperparaméter hangolási konfigurációját, [küldjön el egy kísérletet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name` a hiperparaméter-hangolási kísérlethez hozzárendelni kívánt név, és `workspace` az a munkaterület, amelyben létre kívánja hozni a kísérletet (a kísérletekről további információért lásd: [Hogyan működik a Azure Machine learning?](concept-azure-machine-learning-architecture.md))

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>A hiperparaméter-hangolási kísérlet meleg megkezdése (opcionális)

Gyakran előfordul, hogy a modell legjobb hiperparaméter-értékeinek megkeresése egy iterációs folyamat lehet, amely több hangolási futtatást igényel, amely a korábbi hiperparaméter-hangolási futtatások megismerését mutatja be. Az előző futtatások ismeretének újrafelhasználásával felgyorsítja a hiperparaméter hangolási folyamatát, így csökkentve a modell finomhangolásának költségeit, és javíthatja az eredményül kapott modell elsődleges metrikáját is. Ha a hiperparaméter-hangolási kísérletet a többhelyes mintavételezéssel indítják el, az előző futtatásból származó próbaverziókat az előzetes ismeretként fogjuk használni az új minták intelligens kiválasztásához az elsődleges metrika javítása érdekében. Emellett véletlenszerű vagy rácsos mintavételezés esetén a korai lemondási döntések kihasználják az előző futtatások metrikáit, hogy meghatározzák a gyengén teljesítő képzések futtatását. 

Azure Machine Learning lehetővé teszi, hogy melegen indítsa el a hiperparaméter hangolást úgy, hogy az akár 5 korábban befejezett vagy megszakított hiperparaméter-hangolási szülő futtatásával ismeri fel az ismereteket. Megadhatja a szülő futtatások listáját, melyeket szeretne melegen kezdeni a következő kódrészlet használatával:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Emellett előfordulhatnak olyan alkalmak, amikor a hiperparaméter-hangolási kísérlet egyes képzései megszakadnak a költségvetési megkötések vagy egyéb okok miatt. Mostantól lehetséges a legutóbbi ellenőrzőponton futó egyéni képzések folytatása (feltéve, hogy a betanítási szkript kezeli az ellenőrzőpontokat). Az egyéni képzések futtatásának folytatása ugyanazt a hiperparaméter-konfigurációt fogja használni, és csatolja az adott futtatáshoz használt kimeneti mappát. A betanítási parancsfájlnak el kell fogadnia a `resume-from` argumentumot, amely tartalmazza azokat az ellenőrzőpont-vagy modell-fájlokat, amelyekből folytatni szeretné a betanítást. A következő kódrészlettel folytathatja az egyéni képzések futtatását:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

A hiperparaméter hangolási kísérletet konfigurálhatja úgy, hogy az egy korábbi kísérletből induljon, vagy folytassa az egyes betanítási futtatásokat a választható paraméterekkel `resume_from` és `resume_child_runs` a konfigurációban:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          resume_from=warmstart_parents_to_resume_from, 
                          resume_child_runs=child_runs_to_resume,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>Kísérlet megjelenítése

A Azure Machine Learning SDK egy [Jegyzetfüzet-widgetet](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) biztosít, amely megjeleníti a betanítási folyamat előrehaladását. A következő kódrészlet megjeleníti az összes hiperparaméter finomhangolását egy helyen egy Jupyter-jegyzetfüzetben:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Ez a kód egy táblázatot jelenít meg, amely részletesen ismerteti az egyes hiperparaméter-konfigurációk betanítási futtatásait.

![hiperparaméter hangolási táblázat](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Az egyes futtatások teljesítményét a betanítási folyamatokban is megjelenítheti. 

![hiperparaméter-hangolási ábra](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Emellett vizuálisan azonosíthatja az egyes hiperparaméterek beállítása teljesítményének és értékének korrelációját egy párhuzamos koordinátákat tartalmazó ábra használatával. 

[![hiperparaméter hangolása párhuzamos koordinátákkal](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Az Azure web Portalon is megjelenítheti az összes hiperparaméter hangolási futtatást. A kísérleteknek a webes portálon való megtekintésével kapcsolatos további információkért lásd: [a kísérletek nyomon követése](how-to-track-experiments.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>A legjobb modell megkeresése

Miután az összes hiperparaméter-hangolási Futtatás befejeződött, [azonosítsa a legjobb teljesítményű konfigurációt](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true--include-resume-from-runs-true-----typing-union-azureml-core-run-run--nonetype-) és a megfelelő hiperparaméter-értékeket:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Minta notebook
Tekintse meg a következő mappában található hiperparaméter-* jegyzetfüzeteket:
* [használat – azureml/képzés – mélyreható tanulás](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Következő lépések
* [Kísérlet nyomon követése](how-to-track-experiments.md)
* [Betanított modell üzembe helyezése](how-to-deploy-and-where.md)
