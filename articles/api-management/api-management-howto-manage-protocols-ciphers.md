---
title: Protokollok és titkosítások kezelése az Azure API Managementban | Microsoft Docs
description: Ismerje meg, hogyan kezelheti a protokollok (TLS, SSL) és a titkosítások (DES) használatát az Azure API Managementban.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: f7c7fdd06480ce3da70c86d38ab0685b9b3aaaf2
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072391"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Protokollok és titkosítások kezelése az Azure API Management

Az Azure API Management a TLS protokoll több verzióját is támogatja az ügyfél-és a háttérbeli, valamint a 3DES titkosításhoz.

Ez az útmutató bemutatja, hogyan kezelheti a protokollok és a titkosítási konfigurációk konfigurációját egy Azure API Management-példány esetében.

![Protokollok és titkosítások kezelése a APIM-ben](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához a következőket kell tennie:

* Egy API Management-példány

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>A TLS-protokollok és a 3DES titkosítás kezelése

1. Navigáljon a **API Management** -példányhoz a Azure Portal.
2. Válassza ki a **protokoll beállításait** a menüből.  
3. A kívánt protokollok vagy titkosítások engedélyezése vagy letiltása.
4. Kattintson a **Save** (Mentés) gombra. A módosítások egy órán belül lesznek alkalmazva.  

## <a name="next-steps"></a>További lépések

* További információ a [TLS-ről (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* További [videók](https://azure.microsoft.com/documentation/videos/index/?services=api-management) a API Managementról.