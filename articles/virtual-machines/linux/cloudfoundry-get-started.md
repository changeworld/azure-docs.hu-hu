---
title: Első lépések Cloud Foundry a Microsoft Azure
description: OSS vagy Pivotal Cloud Foundry futtatása Microsoft Azure
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: ba0b4a636d8f785f1c169e4066c9cf6d25ba81db
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970051"
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry az Azure-ban

A Cloud Foundry egy olyan nyílt forráskódú „szolgáltatásként nyújtott platform” (PaaS), amely különböző nyelveken és keretrendszerekben fejlesztett, 12 faktoros alkalmazások létrehozását, telepítését és üzemeltetését teszi lehetővé. Ez a dokumentum a Cloud Foundry Azure-on való futtatásához és az első lépésekhez szükséges beállításokat ismerteti.

## <a name="cloud-foundry-offerings"></a>Ajánlatok Cloud Foundry

Az Azure-ban az Cloud Foundry két formája érhető el: nyílt forráskódú Cloud Foundry (OSS CF) és Pivotal Cloud Foundry (PCF). Az OSS CF a Cloud Foundry Foundation által felügyelt Cloud Foundry teljesen [nyílt forráskódú](https://github.com/cloudfoundry) verziója. A Pivotal Cloud Foundry a Cloud Foundry vállalati disztribúciója a Pivotal Software Inc-ből. Megvizsgáljuk a két ajánlat közötti különbségeket.

### <a name="open-source-cloud-foundry"></a>Nyílt forráskódú Cloud Foundry

Az OSS-Cloud Foundry az Azure-ban központilag telepítheti a BOSH-igazgató üzembe helyezésével, majd Cloud Foundry üzembe helyezésével a [githubon található utasítások](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)használatával. Ha többet szeretne megtudni az OSS CF használatáról, tekintse meg az Cloud Foundry Foundation által biztosított [dokumentációt](https://docs.cloudfoundry.org/) .

A Microsoft a következő közösségi csatornákon keresztül biztosítja az OSS CF legjobb munkamennyiség-támogatását:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slack"></a>Bosh-Azure – CPI-csatorna [Cloud Foundry slacken](https://slack.cloudfoundry.org/)
- [CF-Bosh levelezési lista](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- A [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) és a [Service Broker](https://github.com/Azure/meta-azure-service-broker/issues) GitHub-problémái

>[!NOTE]
> Az Azure-erőforrások, például a Cloud Foundry futtatott virtuális gépek támogatásának szintje az Azure-támogatási szerződésen alapul. A legjobb közösségi támogatás csak a Cloud Foundry-specifikus összetevőkre vonatkozik.

### <a name="pivotal-cloud-foundry"></a>Pivotal Cloud Foundry

A Pivotal Cloud Foundry az OSS-eloszlással megegyező alapplatformot tartalmaz, valamint egy szabadalmaztatott felügyeleti eszközt és nagyvállalati támogatást. A PCF Azure-on való futtatásához a Pivotal licencet kell beszerezni. Az Azure piactéren elérhető PCF-ajánlat 90 napos próbaverziós licencet tartalmaz.

Az eszközök olyan [pivotal Operations Manager](https://docs.pivotal.io/pivotalcf/customizing/), egy webalkalmazás, amely leegyszerűsíti a Cloud Foundry Foundation és a [Pivotal apps Manager](https://docs.pivotal.io/pivotalcf/console/)üzembe helyezését és kezelését, valamint a felhasználók és alkalmazások kezelésére szolgáló webes alkalmazást.

A fenti OSS CF-hez tartozó támogatási csatornák mellett a PCF-licenc arra jogosítja fel a kapcsolatot, hogy forduljon a Pivotal támogatási szolgálatához. A Microsoft és a Pivotal is engedélyezte a támogatási munkafolyamatokat, amelyek lehetővé teszik, hogy bármelyik fél számára segítséget nyújtson, és a kérdéses helytől függően megfelelő módon irányítsa a kérést.

## <a name="azure-service-broker"></a>Azure Service Broker

Cloud Foundry a ["tizenkét faktoros alkalmazás"](https://12factor.net/) módszerét javasolja, amely az állapot nélküli alkalmazások folyamatainak és az állapot-nyilvántartó szolgáltatások tiszta elkülönítését segíti elő. A [Service Broker](https://docs.cloudfoundry.org/services/api.html) egységes módszert kínál a szolgáltatások alkalmazásokba való kiépítésére és a hozzájuk kötődő biztonsági mentésre. Az [Azure Service Broker](https://github.com/Azure/meta-azure-service-broker) ezen a csatornán keresztül biztosítja a legfontosabb Azure-szolgáltatásokat, beleértve az Azure Storage és az Azure SQL szolgáltatást is.

Ha Pivotal Cloud Foundry használ, a Service Broker a csuklós hálózat [csempéje is elérhető](https://docs.pivotal.io/azure-sb/installing.html) .

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

### <a name="azure-devops-services-plugin"></a>Azure DevOps Services beépülő modul

Cloud Foundry kiválóan alkalmas a szoftverfejlesztés gyors fejlesztésére, beleértve a folyamatos integráció (CI) és a folyamatos teljesítés (CD) használatát. Ha az Azure DevOps Services használatával felügyeli a projekteket, és szeretné beállítani a CI/CD-t célzó Cloud Foundry, akkor használhatja az [Azure DevOps Services Cloud Foundry Build bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). A beépülő modul megkönnyíti az üzembe helyezések konfigurálását és automatizálását Cloud Foundry, akár az Azure-ban, akár más környezetben fut.

## <a name="next-steps"></a>Következő lépések

- [A Pivotal Cloud Foundry üzembe helyezése az Azure piactéren](https://azure.microsoft.com/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Alkalmazás üzembe helyezése az Azure-ban Cloud Foundry](./cloudfoundry-deploy-your-first-app.md)
