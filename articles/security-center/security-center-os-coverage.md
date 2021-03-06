---
title: A Azure Security Center által támogatott platformok | Microsoft Docs
description: Ez a dokumentum a Azure Security Center által támogatott platformok listáját tartalmazza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: e13149ba802f0f8b9a565e0aabd86ae05167f18b
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208817"
---
# <a name="supported-platforms"></a>Támogatott platformok 

## Virtuális gépek/kiszolgálók<a name="vm-server"></a>

A Security Center különböző típusú hibrid környezetekben támogatja a virtuális gépeket/kiszolgálókat:

* Csak Azure
* Az Azure és a helyszíni
* Azure és más felhők
* Azure, egyéb felhők és helyszíni

Azure-előfizetésben aktivált Azure-környezet esetén a Azure Security Center automatikusan felderíti az előfizetésen belül üzembe helyezett IaaS-erőforrásokat.

> [!NOTE]
> A biztonsági funkciók teljes készletének fogadásához rendelkeznie kell a [log Analytics ügynökkel](../azure-monitor/platform/agents-overview.md#log-analytics-agent), amelyet a Azure Security Center használ, a telepítés és [megfelelően konfigurálva, hogy az adatküldés Azure Security Center](security-center-enable-data-collection.md#manual-agent).

A következő részekben azok a támogatott kiszolgálói operációs rendszerek találhatók, amelyeken a Azure Security Center által használt [log Analytics-ügynök](../azure-monitor/platform/agents-overview.md#log-analytics-agent)futtatható.

### Windows Server operációs rendszerek<a name="os-windows"></a>

|Operációs rendszer|Támogatja Azure Security Center|A Microsoft Defender ATP-integráció támogatása|
|:---|:-:|:-:|
|A Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

A fent felsorolt Windows operációs rendszerek támogatott szolgáltatásaival kapcsolatos további tudnivalókért lásd: [virtuális gépek/kiszolgálók által támogatott szolgáltatások](security-center-services.md#vm-server-features).

### Windows operációs rendszerek<a name="os-windows (non-server)"></a>

A Azure Security Center az Azure-szolgáltatásokkal együttműködve figyeli és biztosítja a Windows-alapú virtuális gépeket.

### Linux operációs rendszerek<a name="os-linux"></a>

64 bites

* CentOS 6 és 7
* Amazon Linux 2017,09
* Oracle Linux 6 és Oracle Linux 7
* Red Hat Enterprise Linux 6. és 7. kiszolgáló
* Debian GNU/Linux 8 és 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS és 18,04 LTS
* SUSE Linux Enterprise Server 12

32 bites
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 és 9
* Ubuntu Linux 14,04 LTS és 16,04 LTS

> [!NOTE]
> Mivel a támogatott Linux operációs rendszerek listája folyamatosan változik, ha szeretné, kattintson [ide](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) , hogy megtekintse a támogatott verziók legfrissebb listáját abban az esetben, ha a témakör legutóbbi közzététele óta módosult.

A Linux operációs rendszerek támogatott szolgáltatásaival kapcsolatos további tudnivalókért lásd: [virtuális gépek/kiszolgálók által támogatott szolgáltatások](security-center-services.md#vm-server-features).

### Felügyelt virtuális gépek szolgáltatásai<a name="virtual-machine"></a>

A virtuális gépeket egy ügyfél-előfizetésben is létrehozzák az Azure által felügyelt szolgáltatások, például az Azure Kubernetes (ak), a Azure Databricks és sok más szolgáltatás részeként is. Ezeket a virtuális gépeket Azure Security Center is felderíti, és a log Analytics-ügynököt a fent felsorolt támogatott [Windows/Linux operációs rendszerek](#os-windows)szerint lehet telepíteni és konfigurálni.

### Cloud Services<a name="cloud-services"></a>

A Cloud Service-ben futó virtuális gépek is támogatottak. Csak az üzemi tárolóhelyeken futó Cloud Services-alapú webes és feldolgozói szerepköröket figyeli a rendszer. További információ a Cloud Services szolgáltatásról: [Az Azure Cloud Services áttekintése](../cloud-services/cloud-services-choose-me.md).

A Azure Stackban található Virtual Machines védelme is támogatott. A Security Center Azure Stack-nal való integrálásával kapcsolatos további információkért tekintse meg [a Azure stack virtuális gépek Security Centerba való](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)bevezetését ismertető témakört.

## <a name="next-steps"></a>További lépések

- Ismerje meg [, hogyan gyűjt Security Center adatokat és a log Analytics ügynököt](security-center-enable-data-collection.md).
- Megtudhatja [, hogyan kezeli és védi a Security Center az információkat](security-center-data-security.md).
- Megtudhatja, hogyan [tervezheti meg és értelmezheti a Azure Security Center elfogadásához szükséges tervezési szempontokat](security-center-planning-and-operations-guide.md).
- Ismerje meg [a különböző felhőalapú környezetekhez elérhető szolgáltatásokat](security-center-services.md).
- További információ a [Windows és Linux rendszerű gépek veszélyforrások elleni védelméről Azure Security Centerban](threat-protection.md#windows-machines).
