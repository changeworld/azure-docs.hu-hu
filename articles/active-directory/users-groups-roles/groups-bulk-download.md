---
title: A csoportok listájának letöltése a Azure Active Directory portálon | Microsoft Docs
description: A csoport tulajdonságainak tömeges letöltése a Azure Active Directory Azure felügyeleti központban.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b08e807e179270b63ca81d3777c230c3e129c3a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517153"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Csoportok listájának tömeges letöltése (előzetes verzió) Azure Active Directory

A Azure Active Directory (Azure AD) portál használatával tömegesen letöltheti a szervezet összes csoportjának listáját egy vesszővel tagolt (CSV) fájlba.

## <a name="to-download-a-list-of-groups"></a>A csoportok listájának letöltése

1. Jelentkezzen be [a Azure Portalba](https://portal.azure.com) egy rendszergazdai fiókkal a szervezeten belül.
1. Az Azure AD-ben válassza a **csoportok**  >  a**csoportok letöltése**lehetőséget.
1. A **csoportok letöltése** lapon kattintson a **Start** gombra a csoportok listáját tartalmazó CSV-fájl fogadásához.

   ![A csoportok letöltése parancs a minden csoport oldalon található.](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Letöltés állapotának keresése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei (előzetes verzió)** lapon tekintheti meg.

   ![A tömeges műveletek eredményei lapon a tömeges kérelem állapota látható](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Szolgáltatási korlátok tömeges letöltése

A csoportok listájának letöltésére szolgáló összes tömeges tevékenység legfeljebb egy óráig futhat. Ez lehetővé teszi, hogy legalább 300 000 csoportból álló listát töltsön le.

## <a name="next-steps"></a>Következő lépések

- [Csoporttagok tömeges eltávolítása](groups-bulk-remove-members.md)
- [Csoport tagjainak letöltése](groups-bulk-download-members.md)
