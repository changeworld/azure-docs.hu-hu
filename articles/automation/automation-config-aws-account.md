---
title: Hitelesítés konfigurálása Amazon Web Services
description: Ez a cikk ismerteti, hogyan lehet létrehozni és megerősíteni egy AWS hitelesítést az Azure Automation forgatókönyveihez, amelyek az AWS-erőforrásokat kezelik.
keywords: aws-hitelesítés, aws konfigurálása
services: automation
ms.subservice: process-automation
ms.date: 04/17/2018
ms.topic: conceptual
ms.openlocfilehash: 596dc334a412b3e0839d7661a23af771e5cd7394
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75366940"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Forgatókönyvek hitelesítése az Amazon webszolgáltatásokkal

Az általános feladatoknak az Amazon webszolgáltatások (AWS) erőforrásaival történő automatizálása az Automation forgatókönyvekkel lehetséges az Azure szolgáltatásban. Sok feladatot automatizálhat az AWS-ben az Automation forgatókönyvek használatával, ugyanúgy, mint az Azure erőforrásaival. Mindössze két dologra van szükség:

* Egy AWS-előfizetésre és a hitelesítő adatokra. Konkréten az AWS-hozzáférési kulcsára és a titkos kulcsára. További információkért tekintse át az [AWS hitelesítő adatok használatával](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html) foglalkozó cikket.
* Egy Azure-előfizetésre és egy Automation-fiókra.

Az AWS használatával történő hitelesítéshez meg kell határozni az AWS hitelesítő adatokat az Azure Automation által futtatott forgatókönyvek hitelesítéséhez. Ha már létrehozott egy Automation-fiókot, és azt szeretné használni az AWS-sel való hitelesítéshez, kövesse az alábbi szakasz lépéseit: Ha az AWS-erőforrásokra vonatkozó runbookok fiókot szeretne hozzárendelni, először hozzon létre egy új [Automation-fiókot](automation-offering-get-started.md) (hagyja ki az egyszerű szolgáltatás létrehozását), és kövesse az alábbi lépéseket:

## <a name="configure-automation-account"></a>Automation-fiók konfigurálása

Ahhoz, hogy az Azure Automation és az AWS kommunikáljon egymással, először le kell kérnie AWS hitelesítő adatait, és objektumként kell tárolnia őket az Azure Automationben. Végezze el az [AWS-fiók elérési kulcsának kezelése](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) című dokumentumban leírt lépéseket egy elérési kulcs létrehozásához, és másolja át az **Elérési kulcs azonosítóját** és a **Titkos elérési kulcsot** (vagy le is töltheti kulcsfájlját, hogy egy másik, biztonságos helyen tárolja azt).

Miután létrehozta és átmásolta AWS biztonsági kulcsait, létre kell hoznia egy hitelesítőadat-objektumot egy Azure Automation-fiókhoz, hogy biztonságosan tudja tárolni őket, és hivatkozni tudjon rájuk a runbookokkal. Kövesse a következő szakaszban ismertetett lépéseket: **új hitelesítő adatok létrehozása** a [hitelesítő adatokban Azure Automation](shared-resources/credentials.md#to-create-a-new-credential-asset-with-the-azure-portal) cikkben, és adja meg az alábbi adatokat:

1. A **Név** mezőbe írja be az **AWScred** nevet, vagy egy megfelelő értéket, amely követi az elnevezési szabványait.
2. A **Felhasználónév** mezőbe írja be az **Elérési azonosítóját**, a **Jelszó** és **Jelszó megerősítése** mezőkbe pedig a **Titkos elérési kulcsát**.

## <a name="next-steps"></a>Következő lépések

* Az AWS feladatainak automatizálására szolgáló runbookok létrehozására vonatkozó további információkért tekintse át a [virtuális gépek telepítésének Amazon webszolgáltatásokban történő automatizálásával](automation-scenario-aws-deployment.md) foglalkozó cikket.
