---
title: Rövid útmutató – blob létrehozása a Azure Portal
titleSuffix: Azure Storage
description: Ebben a rövid útmutatóban az Azure Portal használatával kezelheti az objektumtárat (blobtárat). Majd az Azure Portal segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 1e5ccc9d427755ea4274e836e81e3e324fffa39d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241051"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Rövid útmutató: blobok feltöltése, letöltése és listázása az Azure Portal használatával

Ez a rövid útmutató azt ismerteti, hogyan használható az [Azure Portal](https://portal.azure.com/) egy Azure Storage-tároló létrehozására, valamint blokkblobok fel- és letöltésére a tárolóban.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>Tároló létrehozása

Az Azure Portalon a következő lépések végrehajtásával hozhat létre egy tárolót:

1. Az Azure Portalon lépjen az új tárfiókjára.
2. A Storage-fiók bal oldali menüjében görgessen a **blob Service** szakaszra, majd válassza a **tárolók**lehetőséget.
3. Válassza a **+ Tároló** gombot.
4. Adja meg az új tároló nevét. A tároló neve csak kisbetűket tartalmazhat, betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat. A tárolók és a blobok elnevezésével kapcsolatos további információkért tekintse meg a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával foglalkozó cikket](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).
5. Adja meg a tároló nyilvános hozzáférési szintjét. Az alapértelmezett szint: **Privát (nincs névtelen hozzáférés)** .
6. A tároló létrehozásához válassza az **OK** gombot.

    ![A tárolók Azure Portalon történő létrehozását bemutató képernyőkép](media/storage-quickstart-blobs-portal/create-container.png)

## <a name="upload-a-block-blob"></a>Blokkblob feltöltése

A blokkblobok blobokká összefűzött adatblokkokból állnak. A Blob Storage legtöbb használati forgatókönyvében blokkblobokat kell használni. A blokkblobok ideális megoldást kínálnak szöveges és bináris adatok (például fájlok, képek és videók) felhőben történő tárolására. Ez a rövid útmutató a blokkblobok használatát mutatja be. 

Az Azure Portalon a következő lépések végrehajtásával tölthet fel egy blokkblobot az Azure Portalon lévő új tárolóba:

1. Az Azure Portalon lépjen az előző szakaszban létrehozott tárolóhoz.
2. Válassza ki a tárolót a benne lévő blobok listájának megtekintéséhez. Mivel ez a tároló új, még nem tartalmaz blobokat.
3. Kattintson a **feltöltés** gombra a feltöltés panel megnyitásához.
4. Tallózás a helyi fájlrendszerben a blokkos blobként feltöltendő fájl kereséséhez
     
    ![A blobok helyi meghajtóról történő feltöltését bemutató képernyőkép](media/storage-quickstart-blobs-portal/upload-blob.png)

5. A speciális szakasz kibontásával más beállításokat is meghatározhat, például a hitelesítési típust, a hozzáférési szintet vagy a virtuális mappa elérési útját.
6. A feltöltés végrehajtásához kattintson a **feltöltés** gombra.
7. Ezzel a módszerrel tetszőleges számú blobot tölthet fel. Látni fogja, hogy az új blobok már fel vannak sorolva a tárolóban.

## <a name="download-a-block-blob"></a>Blokkblob letöltése

A blokkblobok letölthetők a böngészőben történő megjelenítéshez vagy a helyi fájlrendszerben történő mentéshez. A blokkblobok letöltéséhez kövesse az alábbi lépéseket:

1. Lépjen az előző szakaszban feltöltött blobok listájához. 
2. Kattintson a jobb gombbal a letölteni kívánt blobra, majd válassza a **Letöltés** elemet. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen rövid útmutatóban létrehozott erőforrások eltávolításához törölje a tárolót. Ekkor a rendszer törli az adott tárolóban lévő összes blobot is.

A tároló törlése:

1. Az Azure Portalon lépjen a tárfiókban tárolóinak listájához.
2. Válassza ki a törölni kívánt tárolót.
3. Válassza a **Továbbiak** gombot ( **...** ), majd a **Törlés** elemet.
4. Erősítse meg, hogy törölni kívánja a tárolót.

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között az Azure Portal használatával. Ha bővebb információra van szüksége a Blob Storage használatával kapcsolatban, lépjen tovább a Blob Storage használati útmutatójára.

> [!div class="nextstepaction"]
> [Blob Storage-műveletek használati útmutatója](storage-dotnet-how-to-use-blobs.md)

