---
title: Windows-rendszerképek használata az Azure-ban
description: A Visual Studio előfizetési előnyeinek használata a Windows 7, Windows 8 vagy Windows 10 Azure-ban való üzembe helyezéséhez fejlesztési és tesztelési helyzetekben
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 812e6d251943d4418666f221ad8b5d2b6e501736
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039504"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>A Windows-ügyfél használata az Azure-ban fejlesztési és tesztelési helyzetekben
A fejlesztői és tesztelési forgatókönyvekhez használhatja a Windows 7, Windows 8 vagy Windows 10 Enterprise (x64) rendszert az Azure-ban, amennyiben rendelkezik a megfelelő Visual Studio (korábbi MSDN) előfizetéssel. Ez a cikk a Windows 7, a Windows 8,1, a Windows 10 Enterprise és a következő Azure Gallery-lemezképek használatára vonatkozó támogathatósági követelményeket ismerteti.

![Rendszerkép részletei a Azure Portal](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> A Windows 10 Pro és a Windows 10 Pro N rendszerképek az Azure Galleryben című [témakörben tájékozódhat arról, hogyan helyezheti üzembe a Windows 10 rendszert az Azure-ban több-bérlős üzemeltetési jogosultságokkal](windows-desktop-multitenant-hosting-deployment.md)
>![Pro-lemezkép részleteit a Azure Portal](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Előfizetés támogathatósága
Az aktív Visual Studio-előfizetők (akik Visual Studio-előfizetési licencet szereztek be) a Windows-ügyfelet használhatják fejlesztési és tesztelési célokra. A Windows-ügyfél a saját hardveres és Azure-beli virtuális gépeken is használható, bármilyen típusú Azure-előfizetésben. A Windows-ügyfél nem helyezhető üzembe az Azure-ban, és nem használható a normál termelési használatra, vagy nem aktív Visual Studio-előfizetőknek.

Az Ön kényelme érdekében bizonyos Windows 10-es lemezképek az Azure Galleryben érhetők el, a [jogosult fejlesztési/tesztelési ajánlatokon](#eligible-offers)belül. A Visual Studio-előfizetők bármilyen típusú ajánlaton belül a 64 bites Windows 7, Windows 8 vagy Windows 10 rendszerű rendszerképek [megfelelő előkészítését és létrehozását](prepare-for-upload-vhd-image.md) is lehetővé teszi, majd [feltöltheti őket az Azure](upload-generalized-managed.md)-ba. A használat az aktív Visual Studio-előfizetők által a fejlesztéshez és teszteléshez is korlátozott.

## <a name="eligible-offers"></a>Jogosult ajánlatok
A következő táblázat a Windows 10 Azure-katalóguson keresztüli üzembe helyezésére jogosult ajánlat-azonosítókat ismerteti. A Windows 10-es lemezképek csak a következő ajánlatokban láthatók. Azok a Visual Studio-előfizetők, akiknek a Windows-ügyfelet egy másik ajánlati típusban kell futtatniuk, [megfelelően elő kell készíteniük és létre kell hozniuk](prepare-for-upload-vhd-image.md) egy 64 bites Windows 7, Windows 8 vagy Windows 10 rendszerképet, [majd fel kell tölteni az Azure](upload-generalized-managed.md)-ba.

| Offer Name | Csomag száma | Elérhető ügyféloldali rendszerképek |
|:--- |:---:|:---:|
| [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Visual Studio Enterprise- (MPN-) előfizetők](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Visual Studio Professional-előfizetők](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Visual Studio test Professional-előfizetők](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium MSDN-nel (juttatás)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Visual Studio Enterprise-előfizetők](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Visual Studio Enterprise-(BizSpark-) előfizetők](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Azure-előfizetés keresése
Ha nem ismeri az ajánlat AZONOSÍTÓját, az alábbi két módszer egyikével szerezheti be a Azure Portal:  

- Az *előfizetések* ablakban:

  ![Az ajánlat AZONOSÍTÓjának részletei a Azure Portal](./media/client-images/offer-id-azure-portal.png) 

- Vagy kattintson a **számlázás** lehetőségre, majd az előfizetés-azonosítóra. Az ajánlat azonosítója megjelenik a *Számlázási* ablakban.

Az ajánlat AZONOSÍTÓját az Azure-fiók portál [előfizetés lapján](https://account.windowsazure.com/Subscriptions) is megtekintheti:

![Az ajánlatok AZONOSÍTÓjának részletei az Azure-fiók portálján](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Következő lépések
Most már üzembe helyezheti a virtuális gépeket a [PowerShell](quick-create-powershell.md), a [Resource Manager-sablonok](ps-template.md)vagy a [Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)használatával.

