---
title: Kapcsolódás a mezőhöz
description: A Azure Logic Apps segítségével automatizálhatja a fájlok létrehozásával és kezelésével kapcsolatos feladatokat és munkafolyamatokat
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: c7f97ff33742eb545cbfbd7521ba135584851e5e
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666771"
---
# <a name="create-and-manage-files-in-box-by-using-azure-logic-apps"></a>Fájlok létrehozása és kezelése a box-ban Azure Logic Apps használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet fájlokat a dobozban egy logikai alkalmazásban a Box Connector használatával. Így olyan logikai alkalmazásokat hozhat létre, amelyek automatizálják a feladatokat és a munkafolyamatokat a fájlok és egyéb műveletek kezeléséhez, például:

* Hozza létre üzleti folyamatát a listából kapott adatok alapján.

* Automatizált feladatok és munkafolyamatok elindítása fájl létrehozásakor vagy frissítésekor.

* Futtasson egy fájlt átmásoló műveletet, vagy törölje a fájlt.

  Ha ezek a műveletek választ kapnak, a kimenet más műveletekhez is elérhetővé válik. 
  Ha például egy fájl módosítva van a box-ban, a fájlt e-mailben is elküldheti az Office 365 használatával.

## <a name="prerequisites"></a>Előfeltételek

* [Box-fiók](https://www.box.com/home)

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Az a logikai alkalmazás, amelyhez el szeretné érni a Box-fiókját. A logikai alkalmazás Box triggerrel való indításához [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részleteket, például az eseményindítókat, a műveleteket és a korlátozásokat az összekötő OpenAPI (korábban hencegő) fájljában leírtak szerint tekintse [meg az összekötő hivatkozási oldalát](/connectors/box/).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése