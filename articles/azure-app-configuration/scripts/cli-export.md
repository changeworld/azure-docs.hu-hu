---
title: Azure CLI-parancsfájl mintája – exportálás Azure-alkalmazás konfigurációs tárolójából
titleSuffix: Azure App Configuration
description: Az Azure CLI-szkript használata a konfiguráció exportálására az Azure-alkalmazások konfigurációjától
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 3e014504fc1e94c3b323a49b408fa992de00d14a
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523620"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Exportálás Azure app Configuration Store-ból

Ez a minta parancsfájl exportálja a kulcs-értékeket egy Azure-alkalmazás konfigurációs tárolójából.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja az alkalmazás-konfigurációs tárolóból való exportáláshoz. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az appconfig kV export](/cli/azure/appconfig/kv#az-appconfig-kv-export) | Exportálás az alkalmazás konfigurációs tárolójának erőforrásaiból. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

Az alkalmazások konfigurációjának további parancssori felületi mintái az [Azure app CONFIGURATION CLI-mintákban](../cli-samples.md)találhatók.
