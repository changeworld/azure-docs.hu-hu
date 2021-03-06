---
title: SSL-kapcsolat – Azure Database for MySQL
description: Információk a Azure Database for MySQL és a társított alkalmazások az SSL-kapcsolatok megfelelő használatához való konfigurálásához
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 7bc3a238ca2ff2861ec6fc65033738e741574321
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370848"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>SSL-kapcsolat a Azure Database for MySQL

A Azure Database for MySQL a SSL (SSL) használatával támogatja az adatbázis-kiszolgáló és az ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

## <a name="ssl-default-settings"></a>Alapértelmezett SSL-beállítások

Alapértelmezés szerint az adatbázis-szolgáltatást úgy kell konfigurálni, hogy a MySQL-hez való csatlakozáskor SSL-kapcsolatokat igényeljen.  Javasoljuk, hogy ha lehetséges, ne tiltsa le az SSL-beállítást.

Amikor új Azure Database for MySQL kiszolgálót épít ki a Azure Portal és a parancssori felületen, az SSL-kapcsolatok kényszerítése alapértelmezés szerint engedélyezve van. 

A különböző programozási nyelvekhez tartozó kapcsolatok karakterláncai a Azure Portal láthatók. Ezek a kapcsolati karakterláncok tartalmazzák az adatbázishoz való kapcsolódáshoz szükséges SSL-paramétereket. A Azure Portal válassza ki a kiszolgálót. A **Beállítások** fejléc alatt válassza ki a **kapcsolatok karakterláncait**. Az SSL-paraméter az összekötőtől függően változik, például: "SSL = true" vagy "sslmode = require" vagy "sslmode = Required" és egyéb variációk.

Ha szeretné megtudni, hogyan engedélyezheti vagy tilthatja le az SSL-kapcsolatokat az alkalmazások fejlesztésekor, tekintse meg az [SSL konfigurálását](howto-configure-ssl.md)ismertető témakört.

## <a name="tls-connectivity-in-azure-database-for-mysql"></a>TLS-kapcsolat a Azure Database for MySQLban

Azure Database for MySQL támogatja a titkosítást az adatbázis-kiszolgálóhoz Transport Layer Security (TLS) használatával csatlakozó ügyfelek számára. A TLS egy iparági szabványnak megfelelő protokoll, amely gondoskodik az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti biztonságos hálózati kapcsolatokról, ami lehetővé teszi a megfelelőségi követelmények betartását.

### <a name="tls-settings"></a>TLS-beállítások

Az ügyfelek mostantól képesek kikényszeríteni a TLS-verziót az Azure Database for MySQLhoz csatlakozó ügyfél számára. A TLS beállítás használatához használja a TLS- **verzió minimális verziója** beállítást. Ehhez a beállításhoz a következő értékek engedélyezettek:

|  Minimális TLS-beállítás             | A TLS verziója támogatott                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (alapértelmezett) | Nincs szükség TLS-re                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 és újabb |
| TLS1_1                           | TLS 1,1, TLS 1,2 és újabb          |
| TLS1_2                           | TLS 1,2-es és újabb verzió           |


Ha például ezt a TLS-beállítást a TLS 1,0 értékre állítja be, akkor a kiszolgáló engedélyezi a TLS 1,0, 1,1 és 1.2 + protokollt használó ügyfelek kapcsolódását. Azt is megteheti, hogy a 1,2 értékre állítja azt, hogy csak a TLS 1,2-t használó ügyfelek kapcsolatainak engedélyezése, valamint a TLS 1,0 és a TLS 1,1 használatával létesített összes kapcsolat el lesz utasítva.

> [!Note] 
> Azure Database for MySQL a TLS alapértelmezés szerint le van tiltva az összes új kiszolgálón.
>
> Jelenleg a Azure Database for MySQL által támogatott TLS-verziók a TLS 1,0, 1,1 és 1,2.

Ha meg szeretné tudni, hogyan állíthatja be a TLS-beállítást a Azure Database for MySQLhoz, tekintse meg a [TLS-beállítás konfigurálását](howto-tls-configurations.md)ismertető témakört.

## <a name="next-steps"></a>Következő lépések

[Azure Database for MySQLhoz tartozó kapcsolatok kódtárai](concepts-connection-libraries.md)
