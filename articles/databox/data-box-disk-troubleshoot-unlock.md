---
title: Az Azure Data Box-lemezek hibaelhárítási lemez a videókban rejlő információk problémák |} A Microsoft Docs
description: Ez a cikk az Azure Data Box Disk szolgáltatásban jelentkező hibák elhárítását írja le.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 02cbf64261bbfbf50561e1b7466b46b27b688e0a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148282"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Az Azure Data Box-lemezek lemez a videókban rejlő információk hibáinak elhárítása

Ez a cikk a Microsoft Azure Data Box-lemezek vonatkozik, és bemutatja a munkafolyamatok a feloldás eszköz használatakor az esetleges problémák megoldásához használható. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>A Data Box Disk lemezzárolás-feloldó eszközének hibái


| Hibaüzenet/az eszköz viselkedése      | Javaslatok                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| A jelenlegi .NET-keretrendszer nem támogatott. A 4.5-ös és újabb verziók támogatottak.<br><br>Az eszköz hibaüzenettel bezárul.  | A .NET 4.5-ös verziója nincs telepítve. Telepítse a .NET 4.5-ös verzióját a Data Box Disk zárolásának feloldására szolgáló eszközt futtató számítógépre.                                                                            |
| Nem sikerült egyetlen kötetet sem feloldani vagy ellenőrizni. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával.  <br><br>Az eszköz nem tudott egyetlen zárolt meghajtót sem feloldani vagy ellenőrizni. | Az eszköz egyik zárolt meghajtót sem tudta feloldani a megadott hozzáférési kulccsal. A további lépésekhez kérjen segítséget a Microsoft ügyfélszolgálatától.                                                |
| Az alábbi kötetek lettek feloldva és ellenőrizve. <br>Meghajtó-betűjelek: E:<br>A következő hozzáférési kulcsokkal egyetlen kötetet sem sikerült feloldani: werwerqomnf, qwerwerqwdfda <br><br>Az eszköz felold egyes meghajtókat, és listázza a sikeres és sikertelen meghajtók betűjelét.| Részleges siker. Egyes meghajtókat nem sikerült feloldani a megadott hozzáférési kulccsal. A további lépésekhez kérjen segítséget a Microsoft ügyfélszolgálatától. |
| Az eszköz nem talált zárolt köteteket. Ellenőrizze, hogy a Microsofttól kapott lemez megfelelően csatlakoztatva és zárolt állapotban van-e.          | Az eszköz nem talált egyetlen zárolt meghajtót sem. A meghajtók már fel lettek oldva, vagy a rendszer nem észleli őket. Győződjön meg arról, hogy a meghajtók csatlakoztatva vannak és zároltak.                                                           |
| Végzetes hiba: Érvénytelen paraméter<br>Paraméter neve: invalid_arg<br>HASZNÁLAT:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>Példa: DataBoxDiskUnlock /PassKeys:passkey1; passkey2; passkey3<br>Példa: DataBoxDiskUnlock /SystemCheck<br>Példa: DataBoxDiskUnlock /Help<br><br>/ Hozzáférési kulcsok:       A hozzáférési kulcs lekérése Azure DataBox lemezrendelését. A hozzáférési kulccsal oldhatók fel a lemezek.<br>/Help:           Ez a beállítás a parancsmag használati és a példákat nyújt segítséget.<br>/SystemCheck:    Ez a beállítás ellenőrzi, hogy ha a rendszer megfelel-e az eszköz futtatására vonatkozó követelményeknek.<br><br>A kilépéshez nyomja le bármelyik billentyűt. | Érvénytelen paraméter lett megadva. Csak engedélyezett paraméterei a következők: /SystemCheck /PassKey és/help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Problémák a lemezek zárolásának feloldásához, egy Windows-ügyfél használata esetén

Ez a szakasz részletesen a Data Box-lemezek üzembe helyezése során szembesülnek, amikor a Windows-ügyfél használatával az adatok másolása leggyakoribb problémák.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Probléma: A BitLocker meghajtó nem zárolásának feloldása.
 
**OK** 

A BitLocker párbeszédpanelen használta a jelszavát, és meghajtók párbeszédpanelen keresztül a Bitlockert a lemez zárolásának feloldásához próbál zárolásának feloldásához. Ez nem működik.

**Felbontás**

A Data Box-lemezek zárolásának feloldásához meg kell a Data Box lemez zárolásának feloldásához eszközzel, és adja meg a jelszót az Azure Portalról. További információért ugorjon [oktatóanyag: Csomagolja ki, csatlakozzon, és az Azure Data Box-lemezek feloldásához](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Probléma: Nem zárolásának feloldásához, vagy ellenőrizze az egyes kötetek. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával.
 
**OK**

Előfordulhat, hogy tekintse meg a következő hiba történt a hibanapló, és nem, feloldhatja és ellenőrzése néhány kötet.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Ez azt jelzi, hogy valószínűleg hiányzik a Windows PowerShell megfelelő verziója a Windows-ügyfélen.

**Felbontás**

Telepíthet [Windows PowerShell-v 5.0](https://www.microsoft.com/download/details.aspx?id=54616) , majd próbálja megismételni a műveletet.
 
Ha még mindig nem tudja feloldani a kötetek nem ismeri, a naplók másolása a mappában, amely a Data Box lemez zárolásának feloldásához eszközzel rendelkezik, és [forduljon a Microsoft Support](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [érvényesítési problémák elhárítása](data-box-disk-troubleshoot.md).
