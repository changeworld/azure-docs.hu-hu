---
title: Az Azure IoT Hub Device Provisioning beállítása Azure Resource Manager sablonnal
description: Azure rövid útmutató – az Azure IoT Hub Device Provisioning Service (DPS) beállítása sablon használatával
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 482401b75cadf44e2cef03cced8dd216d0980524
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969581"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Rövid útmutató: a IoT Hub Device Provisioning Service beállítása Azure Resource Manager sablonnal

Az [Azure Resource Managerrel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) programozott módon üzembe helyezheti az eszközök regisztrációjához szükséges Azure felhőbeli erőforrásokat. Ezek a lépések bemutatják, hogyan hozhat létre egy IoT hubot és egy új IoT Hub Device Provisioning Service, és hogyan kapcsolhatja össze a két szolgáltatást egy Azure Resource Manager sablon használatával. Ez a rövid útmutató az [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) használatával hajtja végre az erőforráscsoport létrehozásához és a sablon üzembe helyezéséhez szükséges programozási lépéseket, azonban a [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal), a [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), a .net, a Ruby vagy más programozási nyelvek használatával egyszerűen elvégezheti ezeket a lépéseket, és üzembe helyezheti a sablont. 


## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
- Ehhez a rövid útmutatóhoz helyileg kell futtatnia az Azure CLI-t. Az Azure CLI 2.0-s vagy újabb verzióját kell telepíteni. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet, olvassa el [az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli) ismertető témakört.


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Bejelentkezés az Azure-ba és erőforráscsoport létrehozása

Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

1. A parancssorban futtassa a [login parancsot][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Kövesse az utasításokat a kóddal történő hitelesítéshez, és jelentkezzen be az Azure-fiókjába webböngészőből.

2. Ha több Azure-előfizetéssel rendelkezik, az Azure-ba történő bejelentkezéssel hozzáfér a hitelesítő adatokhoz tartozó összes Azure-fiókhoz. Használja az alábbi parancsot a használni kívánt [Azure-fiókok listázásához][lnk-az-account-command] :
    
    ```azurecli
    az account list 
    ```

    Az alábbi parancs segítségével válassza ki azt az előfizetést, amelyet az IoT Hub létrehozásához szükséges parancsok futtatásához kíván használni. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Amikor Azure felhőbeli erőforrásokat, például IoT Hubokat és regisztrációs szolgáltatásokat hoz létre, azokat egy erőforráscsoportban kell létrehozni. Használjon egy meglévő erőforráscsoportot, vagy futtassa a következő [parancsot egy erőforráscsoport létrehozásához][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > Az előző példában az erőforráscsoport az USA nyugati régiójában jön létre. Az `az account list-locations -o table` parancs futtatásával megtekintheti az elérhető helyek listáját.
    >
    >

## <a name="create-a-resource-manager-template"></a>Resource Manager-sablon létrehozása

JSON-sablon használatával létrehozhat egy regisztrációs szolgáltatást és egy ahhoz kapcsolt IoT Hubot az erőforráscsoportban. Az Azure Resource Manager-sablonok segítségével módosíthatja a meglévő regisztrációs szolgáltatásokat vagy IoT Hubot.

1. Szövegszerkesztőben hozzon létre egy **template.json** nevű Azure Resource Manager-sablont a következő váztartalommal. 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": []
   }
   ```

2. Cserélje le a **parameters** szakaszt a következő tartalomra. A parameters (paraméterek) szakasz azokat a paramétereket határozza meg, amelyek értékeit egy másik fájlból lehet átadni. Ez a szakasz a létrehozandó IoT hub és kiépítési szolgáltatás nevét határozza meg. Meghatározza továbbá az IoT hub és a kiépítési szolgáltatás helyét is. Az értékek az IoT-hubokat és a kiépítési szolgáltatásokat támogató Azure-régiókra korlátozódnak. A Device Provisioning Service által támogatott helyek listáját az `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` parancs segítségével kérheti le, vagy az [Azure állapotlapján](https://azure.microsoft.com/status/) kereshet a „Device Provisioning Service” kifejezésre.

   ```json
    "parameters": {
        "iotHubName": {
            "type": "string"
        },
        "provisioningServiceName": {
            "type": "string"
        },
        "hubLocation": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "westus",
                "westeurope",
                "northeurope",
                "southeastasia",
                "eastasia"
            ]
        }
    },

   ```

3. Cserélje le a **variables** szakaszt a következő tartalomra. Ez a szakasz az IoT Hub kapcsolati sztringjének későbbi létrehozásához szükséges értékeket határozza meg, amely a regisztrációs szolgáltatás és az IoT Hub összekapcsolásához szükséges. 
 
   ```json
    "variables": {        
        "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
        "iotHubKeyName": "iothubowner",
        "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
    },

   ```

4. IoT Hub létrehozásához adja hozzá a következő sorokat a **resources** gyűjteményéhez. A JSON meghatározza az IoT hub létrehozásához szükséges minimális tulajdonságokat. A **név** és a **hely** értékei más fájlból származó paraméterekként lesznek átadva. Ha többet szeretne megtudni a sablonban található IoT hub által megadható tulajdonságokról, tekintse meg a [Microsoft. Devices/IotHubs sablon-referenciát](https://docs.microsoft.com/azure/templates/microsoft.devices/iothubs).

   ```json
        {
            "apiVersion": "2017-07-01",
            "type": "Microsoft.Devices/IotHubs",
            "name": "[parameters('iotHubName')]",
            "location": "[parameters('hubLocation')]",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "tags": {
            },
            "properties": {
            }            
        },

   ``` 

5. A regisztrációs szolgáltatás létrehozásához adja hozzá a következő sorokat a **resources** gyűjteményben az IoT Hub specifikációja után. A kiépítési szolgáltatás **nevét** és **helyét** paraméterként adja át a rendszer. A **iotHubs** -gyűjtemény meghatározza a kiépítési szolgáltatáshoz kapcsolni kívánt IoT hubokat. Meg kell adnia legalább az összekapcsolni kívánt IoT Hubok **connectionString** és **location** tulajdonságát. Ezenkívül az egyes IoT Hubokon beállíthat olyan tulajdonságokat, mint az **allocationWeight** és az **applyAllocationPolicy**, a regisztrációs szolgáltatáson pedig olyan tulajdonságokat, mint az **allocationPolicy** vagy az **authorizationPolicies**. További tudnivalókért lásd a [Microsoft.Devices/provisioningServices sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.devices/provisioningservices).

   A **dependsOn** tulajdonsággal biztosítható, hogy a Resource Manager a regisztrációs szolgáltatás létrehozása előtt hozza létre az IoT Hubot. A sablonhoz az IoT Hub kapcsolati sztringjében meg kell adni a regisztrációs szolgáltatással való kapcsolatot, ezért elsőként a hubot és annak kulcsait kell létrehozni. A sablon olyan függvényeket használ, mint a **concat** és a **listkeys műveletének beolvasása** , hogy létrehozza a kapcsolódási karakterláncot a paraméteres változókból. További tudnivalókért tekintse meg [az Azure Resource Manager-sablonok függvényeiről](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) szóló cikket.

   ```json
        {
            "type": "Microsoft.Devices/provisioningServices",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "name": "[parameters('provisioningServiceName')]",
            "apiVersion": "2017-11-15",
            "location": "[parameters('hubLocation')]",
            "tags": {},
            "properties": {
                "iotHubs": [
                    {
                        "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                        "location": "[parameters('hubLocation')]",
                        "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                    }
                ]
            },
            "dependsOn": ["[parameters('iotHubName')]"]
        }

   ```

6. Mentse a sablonfájlt. Az elkészült sablonnak a következőhöz kell hasonlítania:

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "iotHubName": {
               "type": "string"
           },
           "provisioningServiceName": {
               "type": "string"
           },
           "hubLocation": {
               "type": "string",
               "allowedValues": [
                   "eastus",
                   "westus",
                   "westeurope",
                   "northeurope",
                   "southeastasia",
                   "eastasia"
               ]
           }
       },
       "variables": {        
           "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
           "iotHubKeyName": "iothubowner",
           "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
       },
       "resources": [
           {
               "apiVersion": "2017-07-01",
               "type": "Microsoft.Devices/IotHubs",
               "name": "[parameters('iotHubName')]",
               "location": "[parameters('hubLocation')]",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "tags": {
               },
               "properties": {
               }            
           },
           {
               "type": "Microsoft.Devices/provisioningServices",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "name": "[parameters('provisioningServiceName')]",
               "apiVersion": "2017-11-15",
               "location": "[parameters('hubLocation')]",
               "tags": {},
               "properties": {
                   "iotHubs": [
                       {
                           "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                           "location": "[parameters('hubLocation')]",
                           "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                       }
                   ]
               },
               "dependsOn": ["[parameters('iotHubName')]"]
           }
       ]
   }
   ```

## <a name="create-a-resource-manager-parameter-file"></a>Resource Manager-paraméterfájl létrehozása

Az utolsó lépésben megadott sablon paraméterek használatával adja meg az IoT hub nevét, a kiépítési szolgáltatás nevét, valamint a létrehozandó helyet (Azure Region). Ezeket a paramétereket egy külön fájlból kell átadnia a sablonba. Ez lehetővé teszi, hogy ugyanazt a sablont több telepítéshez is felhasználhassa. A paraméterfájl létrehozásához kövesse az alábbi lépéseket:

1. Szövegszerkesztőben hozzon létre egy **parameters.json** nevű Azure Resource Manager-paraméterfájlt a következő váztartalommal: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Adja hozzá az **iotHubName** értéket a paraméterek szakaszához.  Az IoT hub nevének globálisan egyedinek kell lennie az Azure-ban, ezért érdemes lehet egyedi előtagot vagy utótagot hozzáadni a példában szereplő névhez, vagy teljesen új nevet választani. Győződjön meg arról, hogy a neve megfelel az IoT hub megfelelő elnevezési konvencióinak: 3-50 karakter hosszúnak kell lennie, és csak kis-és nagybetűket (alfanumerikus karaktereket vagy kötőjeleket) tartalmazhat ("-"). 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Adja hozzá a **provisioningServiceName** értéket a paraméterek szakaszához. Emellett a kiépítési szolgáltatás globálisan egyedi nevét is ki kell választania. Győződjön meg arról, hogy az egy IoT Hub Device Provisioning Service megfelelő elnevezési konvenciókat használ: 3-64 karakter hosszúnak kell lennie, és csak kis-és nagybetűket, alfanumerikus karaktereket és kötőjeleket (-) tartalmazhat.

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
    }

   ```

4. Adja hozzá a **hubLocation** értéket a paraméterek szakaszához. Ez az érték határozza meg az IoT Hub és a regisztrációs szolgáltatás helyét. Az értéknek egyeznie kell a sablonfájl paraméterdefinícióiban szereplő **allowedValues** gyűjteményben megadott helyek egyikével. Ebben a gyűjteményben az értékek azon Azure-helyekre korlátozódnak, amelyek támogatják az IoT Hubok és a regisztrációs szolgáltatások használatát. Az eszközök kiépítési szolgáltatásának támogatott helyeinek listáját futtathatja `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table`, vagy az [Azure állapot](https://azure.microsoft.com/status/) lapjára kattintva megkeresheti a "Device kiépítési szolgáltatás" kifejezést.

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
        "hubLocation": {
            "value": "westus"
        }
    }

   ```

5. Mentse a fájlt. 


> [!IMPORTANT]
> Az IoT Hub és a regisztrációs szolgáltatás is DNS-végpontként nyilvánosan észlelhető lesz, ezért ügyeljen arra, hogy az elnevezésükkor ne adjon meg bizalmas adatokat.
>

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A következő Azure CLI-parancsokkal helyezheti üzembe a sablonokat és ellenőrizheti az üzembe helyezést.

1. A sablon üzembe helyezéséhez navigáljon a sablon és a paraméter fájljait tartalmazó mappához, és futtassa a következő [parancsot egy központi telepítés elindításához](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   A művelet végrehajtása több percet is igénybe vehet. Ha elkészült, keresse meg a **provisioningState** tulajdonságot, amely a kimenetben "sikeres" értékre mutat. 

   ![Regisztrációs kimenet](./media/quick-setup-auto-provision-rm/output.png) 


2. Az üzembe helyezés ellenőrzéséhez futtassa az alábbi [parancsot az erőforrások megjelenítéséhez](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list), és a kimenetben keresse meg az új regisztrációs szolgáltatást és az IoT Hubot:

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a folytatást, az Azure CLI-vel [törölhet egy adott erőforrást][lnk-az-resource-command], például egy IoT hubot vagy egy kiépítési szolgáltatást, vagy törölhet egy erőforráscsoportot és annak összes erőforrását.

A regisztrációs szolgáltatás törléséhez futtassa a következő parancsot:

```azurecli
az iot hub delete --name {your provisioning service name} --resource-group {your resource group name}
```
IoT Hub törléséhez futtassa a következő parancsot:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Erőforráscsoport és az ahhoz tartozó összes erőforrás törléséhez futtassa a következő parancsot:

```azurecli
az group delete --name {your resource group name}
```

A Azure Portal, a PowerShell vagy a REST API-k használatával is törölhet erőforráscsoportokat és egyedi erőforrásokat, valamint a Azure Resource Managerhoz vagy IoT Hub Device Provisioning Servicehoz közzétett támogatott Platform SDK-kat is.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy IoT hubot és egy eszköz kiépítési szolgáltatási példányát, és összekapcsolta a két erőforrást. Ha szeretné megtudni, hogyan lehet szimulált eszközt kiépíteni a telepítővel, folytassa a szimulált eszköz létrehozására szolgáló rövid útmutatóval.

> [!div class="nextstepaction"]
> [Szimulált eszköz létrehozásának rövid útmutatója](./quick-create-simulated-device.md)


<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
