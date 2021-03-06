---
title: Beágyazott sablon környezetek üzembe helyezése Azure DevTest Labs
description: Megtudhatja, hogyan helyezhet üzembe beágyazott Azure Resource Manager-sablonokat a környezetek Azure DevTest Labs való biztosításához.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e83bc4e77a44f20d55fa3b56bc81aefd1d25bb03
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76168821"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>Beágyazott Azure Resource Manager-sablonok üzembe helyezése tesztelési környezetekhez
A beágyazott üzembe helyezés lehetővé teszi, hogy más Azure Resource Manager sablonokat hajtson végre egy fő Resource Manager-sablonon belülről. Lehetővé teszi az üzemelő példányok kiépítését a célzott és a célhoz tartozó sablonokba. A tesztelés, az újbóli használat és az olvashatóság szempontjából biztosít előnyöket. Az [Azure-erőforrások üzembe helyezése során csatolt sablonokat használó](../azure-resource-manager/templates/linked-templates.md) cikk jó áttekintést nyújt a megoldásról több mintakód használatával. Ez a cikk a Azure DevTest Labsra vonatkozó példát mutat be. 

## <a name="key-parameters"></a>Kulcs paraméterei
Habár a saját Resource Manager-sablonokat a semmiből is létrehozhatja, javasoljuk, hogy az [Azure erőforráscsoport-projektet](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) használja a Visual Studióban, amely megkönnyíti a sablonok fejlesztését és hibakeresését. Ha beágyazott üzembe helyezési erőforrást ad hozzá a azuredeploy. JSON fájlhoz, a Visual Studio több elemet is felvesz a sablon rugalmasabbá tétele érdekében. Ezek az elemek tartalmazzák a másodlagos sablonnal és paraméterekkel rendelkező almappát, a fő sablonban szereplő változók nevét, valamint az új fájlok tárolási helyének két paraméterét. A **_artifactsLocation** és **_ArtifactsLocationSasToken** a DevTest Labs által használt legfontosabb paraméterek. 

Ha nem tudja, hogyan működik együtt a DevTest Labs a környezetekkel, tekintse meg a [több virtuális gépre kiterjedő környezetek és a Azure Resource Manager-sablonok használatával kapcsolatos további információk](devtest-lab-create-environment-from-arm.md)című témakört. A sablonokat a DevTest Labs-laborhoz csatolt adattár tárolja. Amikor új környezetet hoz létre ezekkel a sablonokkal, a rendszer áthelyezi a fájlokat egy Azure Storage-tárolóba a laborban. A beágyazott fájlok azonosításához és másolásához a DevTest Labs azonosítja a _artifactsLocation és a _artifactsLocationSasToken paramétereket, és az almappákat a Storage-tárolóba másolja. Ezt követően automatikusan beszúrja a hely és a közös hozzáférésű aláírás (SaS) tokent a paraméterekbe. 

## <a name="nested-deployment-example"></a>Beágyazott telepítési példa
Az alábbi egyszerű példa egy beágyazott üzembe helyezésre:

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

A sablont tartalmazó adattár mappája `nestedtemplates` a **NestOne. JSON** és a **NestOne. Parameters. JSON**fájlokkal. A **azuredeploy. JSON**fájlban a sablon URI-ja az összetevők helye, a beágyazott sablon mappája, a beágyazott sablonfájl neve alapján készült. Hasonlóképpen, a paraméterek URI-ja az összetevők helye, a beágyazott sablon mappája és a beágyazott sablonhoz tartozó paraméter fájl használatával készült. 

Itt látható a Visual Studióban megjelenő projekt struktúrájának képe: 

![Projekt szerkezete a Visual Studióban](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

Az elsődleges mappában további mappák is hozzáadhatók, de nem lehetnek mélyebbek, mint egyetlen szinten. 

## <a name="next-steps"></a>Következő lépések
A környezetekkel kapcsolatos részletekért tekintse meg a következő cikkeket: 

- [Több virtuális gépes környezet és PaaS-erőforrás létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md)
- [Nyilvános környezetek konfigurálása és használata Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Környezet összekötése a tesztkörnyezet virtuális hálózatával Azure DevTest Labs](connect-environment-lab-virtual-network.md)