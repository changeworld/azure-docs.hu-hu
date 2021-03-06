---
title: Titkok használata a betanítási futtatásokban
titleSuffix: Azure Machine Learning
description: A titkok átadása biztonságos módon, a munkaterület Key Vault használatával
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d877794abf12b8b412cd1ecf4efd72fd1179d768
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942264"
---
# <a name="use-secrets-in-training-runs"></a>Titkok használata a betanítási futtatásokban
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan használhatók biztonságosan a Titkok a betanításban. A hitelesítési adatok, például a Felhasználónév és a jelszó titkos kódok. Ha például egy külső adatbázishoz csatlakozik a betanítási adatai lekérdezéséhez, a felhasználónevet és a jelszót át kell adnia a távoli futtatási környezetnek. Ha ezeket az értékeket a titkosítatlan szövegben lévő betanítási parancsfájlokba szeretné írni, nem biztonságos, mert a titkos kulcsot kiteszi. 

Ehelyett a Azure Machine Learning munkaterület egy [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)nevű társított erőforrással rendelkezik. Ennek a Key Vaultnak a használatával biztonságosan továbbíthatja a titkos kulcsokat a távoli futtatáshoz a Azure Machine Learning Python SDK API-jai segítségével.

A titkok használatának alapszintű folyamata a következő:
 1. A helyi számítógépen jelentkezzen be az Azure-ba, és kapcsolódjon a munkaterületéhez.
 2. Helyi számítógépen állítson be egy titkos kulcsot a munkaterületen Key Vault.
 3. Távoli Futtatás küldése.
 4. A távoli Futtatás alatt szerezze be a titkot Key Vault és használja.

## <a name="set-secrets"></a>Titkos kódok beállítása

A [Azure Machine learning a kulcstartó osztály a](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) titkok beállítására szolgáló metódusokat tartalmazza. A helyi Python-munkamenetben először szerezzen be egy hivatkozást a munkaterületre Key Vault, majd a [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) metódus használatával állítsa be a titkos kulcsot név és érték alapján. Ha a név már létezik, a __set_secret__ metódus frissíti a titkos értéket.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Ne helyezze el a titkos értéket a Python-kódban, mert nem biztonságos a fájlként való tárolása titkosítatlan fájlként. Ehelyett szerezze be a titkos értéket egy környezeti változóból, például az Azure DevOps Build Secret vagy az interaktív felhasználói bemenet alapján.

A titkos neveket a [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) metódussal listázhatja, és a Batch verziója,[set_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) is lehetővé teszi, hogy egyszerre több titkot is beállíthat.

## <a name="get-secrets"></a>Titkok beolvasása

A helyi kódban a[`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) metódussal kérheti le a titkos értéket név alapján.

A [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) elküldött futtatások esetén használja a [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) metódust a [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) osztállyal. Mivel az elküldött futtatások tisztában vannak a munkaterületével, ez a módszer a munkaterület-példányát, és közvetlenül a titkos értéket adja vissza.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Ügyeljen arra, hogy a titkos értéket ne tegye elérhetővé vagy nyomtassa ki.

Létezik egy batch-verzió is, [get_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) , amely egyszerre több titkot is elér.

## <a name="next-steps"></a>További lépések

 * [Példa jegyzetfüzetek megtekintése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Ismerkedjen meg a nagyvállalati biztonsággal Azure Machine Learning](concept-enterprise-security.md)
