---
title: Azure Data Box Gateway biztonság | Microsoft Docs
description: Ismerteti azokat a biztonsági és adatvédelmi funkciókat, amelyek védik a Azure Data Box Gateway virtuális eszközét, szolgáltatását és adatait a helyszínen és a felhőben.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 89f981fcda8f40daff49ebdf796b896d90ce1754
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900596"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Biztonság és adatvédelem Azure Data Box Gateway

A biztonság jelentős jelentőséggel bír az új technológiák bevezetésekor, különösen akkor, ha a technológiát bizalmas vagy tulajdonosi adatokkal használják. Azure Data Box Gateway segítségével biztosíthatja, hogy csak a felhatalmazott entitások tudják megtekinteni, módosítani vagy törölni az adatait.

Ez a cikk azokat a Azure Data Box Gateway biztonsági funkciókat ismerteti, amelyek segítenek a megoldás egyes összetevőinek és a bennük tárolt adatainak a védelmében.

A Data Box Gateway megoldás négy fő összetevőből áll, amelyek egymással együttműködnek:

- **Az Azure-ban üzemeltetett Data Box Gateway szolgáltatás**. Az eszköz megrendelésének létrehozásához használt felügyeleti erőforrás, konfigurálja az eszközt, majd kövesse nyomon a befejezési sorrendet.
- **Data Box Gateway eszköz**. Az Ön által megadott rendszer hypervisorában kiépített virtuális eszköz. A virtuális eszköz használatával importálhatja a helyszíni adatait az Azure-ba.
- **Az eszközhöz csatlakoztatott ügyfelek/gazdagépek**. Az infrastruktúra azon ügyfelei, amelyek a Data Box Gateway eszközhöz csatlakoznak, és a védeni kívánt adatait tartalmazzák.
- **Felhőbeli tárolás**. A hely az Azure Cloud platformon, ahol az adatgyűjtés történik. Ez a hely általában a létrehozott Data Box Gateway erőforráshoz csatolt Storage-fiók.


## <a name="data-box-gateway-service-protection"></a>Data Box Gateway szolgáltatás védelme

A Data Box Gateway szolgáltatás az Azure-ban üzemeltetett felügyeleti szolgáltatás. A szolgáltatás az eszköz konfigurálására és kezelésére szolgál.

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Data Box Gateway eszköz védelme

A Data Box Gateway eszköz egy olyan virtuális eszköz, amely egy Ön által megadott helyszíni rendszer hypervisorában van kiépítve. Az eszköz segít az Azure-ba való adatküldésben. Az eszköz:

- Aktiválási kulcsra van szükség a Data Box Edge/Data Box Gateway szolgáltatás eléréséhez.
- Az eszköz jelszavai mindig védelmet biztosítanak.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Az eszköz védetté aktiválása az aktiválási kulcs használatával

Csak egy engedélyezett Data Box Gateway eszköz csatlakozhat az Azure-előfizetésében létrehozott Data Box Gateway szolgáltatáshoz. Az eszköz engedélyezéséhez aktiválnia kell egy aktiválási kulcsot az eszköz Data Box Gateway szolgáltatással való aktiválásához.

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

További információ: aktiválási [kulcs](data-box-gateway-deploy-prep.md#get-the-activation-key)beszerzése.

### <a name="protect-the-device-via-password"></a>Az eszköz jelszavas védelemmel való ellátása

A jelszavak biztosítják, hogy csak a jogosult felhasználók férjenek hozzá az adataihoz. Data Box Gateway az eszközök zárolt állapotban vannak.

A következőket teheti:

- Kapcsolódjon az eszköz helyi webes FELÜLETéhez egy böngészőben, majd adjon meg egy jelszót az eszközre való bejelentkezéshez.
- Távolról kapcsolódhat az eszköz PowerShell-felületéhez HTTP-n keresztül. A távfelügyelet alapértelmezés szerint be van kapcsolva. Ezután megadhatja az eszköz jelszavát az eszközre való bejelentkezéshez. További információ: [távoli kapcsolódás a Data Box Gateway eszközhöz](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- A [jelszó](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)megváltoztatásához használja a helyi webes felhasználói felületet. Ha megváltoztatja a jelszót, ügyeljen arra, hogy tájékoztassa az összes távelérési felhasználót, hogy ne legyen probléma a bejelentkezéskor.


## <a name="protect-your-data"></a>Az adatok védelme

Ez a szakasz azokat a Data Box Gateway biztonsági szolgáltatásokat ismerteti, amelyek a továbbítást és a tárolt információkat védik.

### <a name="protect-data-at-rest"></a>Adatok védelme nyugalmi állapotban

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>A repülésben tárolt adatvédelem

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Az adatvédelem a Storage-fiókok használatával

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Forgassa el és szinkronizálja rendszeresen a [Storage-fiók kulcsait](data-box-gateway-manage-shares.md#sync-storage-keys) , hogy megvédje a Storage-fiókját a jogosulatlan felhasználóktól.

## <a name="manage-personal-information"></a>Személyes adatok kezelése

A Data Box Gateway szolgáltatás a következő helyzetekben gyűjt személyes adatokat:

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

A megosztásokat elérő vagy törölhető felhasználók listájának megtekintéséhez kövesse a [megosztások kezelése](data-box-gateway-manage-shares.md)a Data Box Gatewayban című témakör lépéseit.

További információkért tekintse át a Microsoft adatvédelmi szabályzatát a [megbízhatósági központban](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>További lépések

[A Data Box Gateway eszköz üzembe helyezése](data-box-gateway-deploy-prep.md)
