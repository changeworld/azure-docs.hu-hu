---
title: 'CLI: alkalmazás összekötése egy Storage-fiókkal'
description: Ismerje meg, hogyan automatizálható az App Service-alkalmazás üzembe helyezése és kezelése az Azure CLI használatával. Ez a minta bemutatja, hogyan csatlakoztatható egy alkalmazás egy Storage-fiókhoz.
author: msangapu-msft
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c8fcdae21e1f9b8508649c52480fa4a007a3325b
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688440"
---
# <a name="connect-an-app-service-app-to-a-storage-account-using-cli"></a>App Service-alkalmazás összekötése egy Storage-fiókkal a parancssori felület használatával

Ez a példa egy Azure Storage-fiókot és egy App Service alkalmazást hoz létre. Ezután az alkalmazás beállításainak használatával összekapcsolja a Storage-fiókot az alkalmazással.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.


## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-storage/connect-to-storage.sh "Azure Storage")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy erőforráscsoport, App Service alkalmazás, a Storage-fiók és az összes kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Létrehoz egy App Service-csomagot. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Létrehoz egy App Service alkalmazást. |
| [`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) | Létrehoz egy tárfiókot. |
| [`az storage account show-connection-string`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-show-connection-string) | Lekéri egy tárfiók kapcsolati sztringjét. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) | Egy App Service alkalmazás alkalmazás-beállításainak létrehozása vagy frissítése. Az alkalmazásbeállítások környezeti változókként érhetők el az alkalmazás számára. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
