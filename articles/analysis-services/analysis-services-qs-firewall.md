---
title: Rövid útmutató – Azure Analysis Services kiszolgáló tűzfalának konfigurálása | Microsoft Docs
description: Ez a rövid útmutató segítséget nyújt a tűzfal konfigurálásához Azure Analysis Services-kiszolgálóhoz a Azure Portal használatával.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 68994f9b79af55b32527eed52bbc4e5866c89538
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79205157"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Rövid útmutató: Kiszolgálótűzfal konfigurálása – Portál

Ez a rövid útmutató segítséget nyújt az Azure Analysis Services-kiszolgáló tűzfalának konfigurálásában. A kiszolgáló és az adatok védelmének fontos része egy tűzfal engedélyezése és IP-címtartományok konfigurálása csak a kiszolgálóhoz hozzáférő számítógépek számára.

## <a name="prerequisites"></a>Előfeltételek

- Analysis Services-kiszolgáló az előfizetésben. További tudnivalókért lásd: [Rövid útmutató: Kiszolgáló létrehozása – Portál](analysis-services-create-server.md) vagy [Rövid útmutató: Kiszolgáló létrehozása – PowerShell](analysis-services-create-powershell.md)
- Egy vagy több IP-címtartomány ügyfélszámítógépekhez (ha szükséges).
- Előfordulhat, hogy a Power BI Premium Azure Analysis Serviceshoz, például az adatimportáláshoz (frissítéshez) és a többoldalas jelentésekhez való kapcsolódáskor jelenleg nem támogatottak, még akkor is, ha engedélyezve van a hozzáférés engedélyezése a Power BI. A Power BI Premium élő kapcsolat használatának leggyakoribb forgatókönyve támogatott. Az összes Power BI Pro-forgatókönyv támogatott.


## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra 

[Bejelentkezés a portálra](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Tűzfal konfigurálása

1. Kattintson a kiszolgálóra az Áttekintés oldal megnyitásához. 
2. A **BEÁLLÍTÁSOK** > **Tűzfal** > **Tűzfal engedélyezése** részben kattintson a **Be** lehetőségre.
3. A DirectQuery-hozzáférés a Power BI szolgáltatásból történő engedélyezéséhez a **Hozzáférés engedélyezése Power BI-ból** területen kattintson a **Be** lehetőségre.  
4. (Opcionális) Adjon meg egy vagy több IP-címtartományt. Adjon meg egy nevet, valamint egy kezdő és egy záró IP-címet mindegyik tartomány számára. A tűzfalszabály nevét 128 karakterre kell korlátozni, és csak nagybetűket, kisbetűket, számokat, aláhúzást és kötőjelet tartalmazhatnak. Szóközök és egyéb speciális karakterek használata nem engedélyezett.
5. Kattintson a **Save** (Mentés) gombra.

     ![Tűzfalbeállítások](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az IP-címtartományokat, vagy tiltsa le a tűzfalat.

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban megismerte, hogyan konfigurálhat tűzfalat a kiszolgálójához. Most, hogy tűfalas védelmet biztosított a kiszolgáló számára, hozzáadhat egy alapszintű minta adatmodellt a portálról. A mintamodell azért hasznos, mert segít megismerni a modell adatbázis-szerepkörök konfigurálását és az ügyfélkapcsolatok tesztelését. További tudnivalókért folytassa a mintamodell hozzáadását ismertető oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: Mintamodell hozzáadása a kiszolgálóhoz](analysis-services-create-sample-model.md)
