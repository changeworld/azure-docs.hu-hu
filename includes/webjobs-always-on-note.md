---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424155"
---
> [!NOTE]
> A webalkalmazások 20 perc inaktivitás után időtúllépést okozhatnak. Csak a tényleges webalkalmazásnak küldött kérések állítják vissza az időzítőt. Ha megtekinti az alkalmazás konfigurációját a Azure Portalban, vagy kéréseket tesz a`https://<app_name>.scm.azurewebsites.net`speciális eszközök helyének (), ne állítsa alaphelyzetbe az időzítőt. Ha az alkalmazás folyamatos vagy ütemezett (időzítő trigger) webjobs-feladatokat futtat, engedélyezze a **mindig be** lehetőséget a webjobs megbízható működésének biztosításához. Ez a funkció csak az alapszintű, a standard és a prémium [szintű díjszabásban](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)érhető el.
