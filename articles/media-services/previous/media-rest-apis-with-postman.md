---
title: Poster konfigurálása Azure Media Services REST API hívásokhoz
description: Ez a cikk azt ismerteti, hogyan konfigurálható a Poster Media Services REST API-hívásokhoz.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 11c9c26e7c0f36e1e3dba732e90a6aef95e6ee14
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694990"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Poster konfigurálása Media Services v2 REST API hívásokhoz  

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-from-v2-to-v3.md)

Ez az oktatóanyag bemutatja, hogyan konfigurálhatja a **Poster** -t, hogy felhasználható legyen Azure Media Services (AMS) REST API-k meghívására. Az oktatóanyag bemutatja, hogyan importálhat környezeteket és gyűjteményeket a **Poster**-ba. A gyűjtemény a Azure Media Services (AMS) REST API-kat meghívó HTTP-kérések csoportosított definícióit tartalmazza. A környezeti fájl azokat a változókat tartalmazza, amelyeket a gyűjtemény használ.

Ez a környezet és gyűjtemény olyan cikkekben használatos, amelyek bemutatják, hogyan lehet különböző feladatokat elérni Azure Media Services REST API-kkal.

## <a name="prerequisites"></a>Előfeltételek

- Telepítse a [Postman](https://www.getpostman.com/) REST-ügyfelet, hogy végrehajtsa az AMS REST oktatóanyagok egy részében látható REST API-kat. 

    A **Postmant** használjuk, de bármely egyéb REST-eszköz is megfelelő. Egyéb alternatívák: **Visual Studio Code** REST beépülő modullal vagy **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>A környezet konfigurálása 

1. Hozzon létre egy. JSON fájlt, amely tartalmazza az AMS oktatóanyagokban használt környezeti változókat. Nevezze el a fájlt (például **AzureMediaServices. postman_environment. JSON**). Nyissa meg a fájlt, és illessze be a Poster-környezetet meghatározó kódot a [kód listájából](postman-environment.md). 
2. Nyissa meg a **Postmant**.
3. A képernyő jobb oldalán válassza a **Manage environment (Környezet felügyelete)** lehetőséget.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-create-env.png)
4. A **Manage environment (Környezet felügyelete)** párbeszédablakban kattintson az **Import (Importálás)** gombra.
5. Tallózással keresse meg és válassza ki a **AzureMediaServices. postman_environment. JSON** fájlt.
6. A **AzureMedia** -környezet hozzá van adva.
7. Zárja be a párbeszédpanelt.
8. Válassza ki a **AzureMedia** -környezetet.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>A gyűjtemény konfigurálása

1. Hozzon létre egy. JSON-fájlt, amely tartalmazza a **Poster** -gyűjteményt az összes olyan művelettel, amely szükséges a fájlok Media Servicesba való feltöltéséhez. Nevezze el a fájlt (például **AzureMediaServicesOperations. postman_collection. JSON**). Nyissa meg a fájlt, és illessze be a **Poster** [-gyűjteményt meghatározó kódot a](postman-collection.md)listában.
2. Kattintson az **Import (Importálás)** gombra a gyűjteményfájl importálásához.
3. Válassza ki a **AzureMediaServicesOperations. postman_collection. JSON** fájlt.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Következő lépések

Tekintse meg az [eszközök feltöltése](media-services-rest-upload-files.md) című cikket.  
