---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179723"
---
Python az alkalmazás elindítása során hibát észlel, ha csak egy egyszerű hibalap fogja visszaadni (például) "Az oldal nem lehet megjeleníteni, mert egy belső kiszolgálóhiba történt.").

Python-alkalmazáshibák rögzítésére:

1. Válassza ki az Azure Portalon, a web app alkalmazásban **beállítások**.
2. Az a **beállítások** lapon jelölje be **Alkalmazásbeállítások**.
3. A **Alkalmazásbeállítások**, adja meg a következő kulcs-érték párt:
    * Kulcs: WSGI_LOG
    * Érték: D:\home\site\wwwroot\logs.txt (adja meg a kívánt fájl nevét)

Meg kell jelennie a wwwroot mappában logs.txt fájlban.
