---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545208"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>A felhőalapú térbeli horgony munkamenetének konfigurálása

A következő lépésekkel gondoskodunk a felhőalapú térbeli horgony munkamenetének konfigurálásáról. Az első sorban beállítjuk az érzékelő szolgáltatóját a munkamenetben. Mostantól a munkamenet során létrehozott összes horgony érzékelő-olvasási művelettel lesz társítva. Ezután létrehozunk egy közeli eszközre vonatkozó keresési feltételeket, és inicializáljuk az alkalmazás követelményeinek megfelelően. Végezetül arra utasítja a munkamenetet, hogy a horgonyok megkeresése során az érzékelő adatait használja a közeli eszközre vonatkozó feltételekből származó figyelők létrehozásával.