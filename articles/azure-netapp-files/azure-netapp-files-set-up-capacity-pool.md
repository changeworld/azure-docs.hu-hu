---
title: Kapacitáskészlet beállítása az Azure NetApp Files számára | Microsoft Docs
description: A cikk azt mutatja be, hogyan állíthat be kapacitáskészleteket, amelyekben aztán majd köteteket hozhat létre.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 8f50b2ad34c705c8d3831d8243f136c41d750dc0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60691098"
---
# <a name="set-up-a-capacity-pool"></a>Kapacitáskészlet beállítása

A beállított kapacitáskészletekben hozhat létre majd köteteket.  

## <a name="before-you-begin"></a>Előkészületek 

Már rendelkeznie kell egy létrehozott NetApp-fiókkal.   

[NetApp-fiók létrehozása](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Lépések 

1. A NetApp fiók felügyeleti paneljéről, és a navigációs ablaktáblán kattintson a **kapacitás készletek**.  
    
    ![Navigáljon a kapacitás-készlet](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Új kapacitáskészlet létrehozásához kattintson a **+ Készletek hozzáadása** elemre.   
    Ekkor megjelenik az Új kapacitáskészlet ablak.

3. Adja meg az alábbi, az új kapacitáskészletre vonatkozó információkat:  
   * **Name (Név)**  
     Adja meg a kapacitáskészlet nevét.  
     A kapacitáskészlet nevének a NetApp-fiókban egyedinek kell lennie.

   * **Szolgáltatásszint**   
     Ez a mező a kapacitáskészlet célteljesítményét mutatja.  
     Adja meg a szolgáltatási szint a kapacitás-készlet: [**Prémium szintű** ](azure-netapp-files-service-levels.md#Premium) vagy [ **Standard**](azure-netapp-files-service-levels.md#Standard).

   * **Méret**     
     Adja meg a megvásárolni kívánt kapacitáskészlet-méretet.        
     A kapacitáskészletek minimális mérete 4 TiB. A létrehozott kapacitáskészlet méretének a 4 TiB egy szorzatának kell lennie.   
      
     ![Új kapacitáskészlet](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Kattintson az **OK** gombra.

## <a name="next-steps"></a>További lépések 

- [Szolgáltatási szintek a NetApp Azure-fájlok](azure-netapp-files-service-levels.md)
- Tekintse meg a [Azure NetApp fájlok díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/storage/netapp/) az ár különböző szolgáltatási szintek
- [Az Azure Files-NetApp alhálózat delegálása](azure-netapp-files-delegate-subnet.md)
