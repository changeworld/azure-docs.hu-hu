---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966352"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Ha az adattár az alábbi módszerek egyikével van konfigurálva, akkor az adattárhoz való kapcsolódáshoz létre [](../articles/data-factory/create-self-hosted-integration-runtime.md) kell hoznia egy helyi Integration Runtimet:

- Az adattár egy helyszíni hálózaton belül, az Azure Virtual Network belül vagy az Amazon Virtual Private Cloud-on belül található.
- Az adattár egy felügyelt felhőalapú adatszolgáltatás, ahol a hozzáférés a tűzfalszabályok által meghatározott IP-címekre korlátozódik.
