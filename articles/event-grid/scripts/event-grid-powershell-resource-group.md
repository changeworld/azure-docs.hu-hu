---
title: Azure PowerShell-példaszkript – Feliratkozás erőforráscsoportra | Microsoft Docs
description: Ez a cikk egy minta Azure PowerShell parancsfájlt tartalmaz, amely bemutatja, hogyan fizethet elő az erőforráscsoport Event Grid eseményeire.
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e285f628d1ec3afce55dc8fe4efa50a361dab5ed
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720806"
---
# <a name="subscribe-to-events-for-a-resource-group-with-powershell"></a>Feliratkozás egy erőforráscsoport eseményeire a PowerShell-lel

Ez a szkript létrehoz egy Event Grid-előfizetést egy erőforráscsoport eseményeihez.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Az előnézeti minta parancsfájlhoz a Event Grid modul szükséges. A telepítéséhez futtassa `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Példaszkript – stabil

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-resource-group/subscribe-to-resource-group.ps1 "Subscribe to resource group")]

## <a name="sample-script---preview-module"></a>Minta parancsfájl – előzetes verziójú modul

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-resource-group-preview/subscribe-to-resource-group-preview.ps1 "Subscribe to resource group")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja az esemény-előfizetés létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Új – AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Event Grid-előfizetés létrehozása. |

## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* A PowerShell-lel kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/get-started-azureps).
