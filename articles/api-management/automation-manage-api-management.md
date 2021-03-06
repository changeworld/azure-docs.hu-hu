---
title: Azure-API Management kezelése Azure Automation használatával
description: Ismerje meg, hogyan használható az Azure Automation szolgáltatás az Azure-API Management felügyeletéhez.
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: a472e00f9ecab8a5ffa6b19e4fe9a5f8b5ee5b95
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072059"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Az Azure API Management kezelése Azure Automation használatával
Ez az útmutató bemutatja a Azure Automation szolgáltatást, valamint azt, hogy miként használható az Azure-API Management felügyeletének egyszerűsítésére.

## <a name="what-is-azure-automation"></a>Mi az Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) egy Azure-szolgáltatás, amely egyszerűbbé teszi a felhőalapú felügyeletet a folyamatok automatizálásával. A Azure Automation, a manuális, az ismétlődő, a hosszan futó és a hibákra hajlamos feladatok automatizálásával növelheti a szervezet számára a megbízhatóságot, a hatékonyságot és az időt.

A Azure Automation egy nagyon megbízható, jól elérhető munkafolyamat-végrehajtó motort biztosít, amely az igényeinek megfelelően méretezhető. Azure Automation a folyamatokat manuálisan, harmadik féltől származó rendszerek vagy ütemezett időközök alapján lehet kiváltani, hogy a feladatok a szükségesnél pontosan megtörténjenek.

Csökkentse az üzemeltetési terhelést, és szabadítson fel informatikai részleget, és DevOps a munkatársakat úgy, hogy az üzleti értéket a Felhőbeli felügyeleti feladatok automatikus futtatásához Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Hogyan segíthet Azure Automation az Azure API Management kezelése?
A API Management Azure Automation az [Azure API Management API Windows PowerShell-parancsmagjai](https://docs.microsoft.com/powershell/module/az.apimanagement)használatával kezelhetők. Azure Automationon belül a parancsmagok használatával számos API Management feladat elvégzéséhez PowerShell-munkafolyamat-parancsfájlok is írhatók. Ezeket a parancsmagokat Azure Automation is párosíthatja más Azure-szolgáltatások parancsmagokkal, így összetett feladatokat automatizálhat az Azure-szolgáltatások és a külső gyártók rendszerei között.

Íme néhány példa a API Management használatára a PowerShell használatával:

* [Azure PowerShell-minták az API Managementhez](https://docs.microsoft.com/azure/api-management/powershell-samples)

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Azure Automation alapjait, és hogy miként használható az Azure-API Management kezelésére, kövesse az alábbi hivatkozásokat, ahol további információkat talál.

* Tekintse meg a Azure Automation [első lépéseket ismertető oktatóanyagot](../automation/automation-first-runbook-graphical.md).

