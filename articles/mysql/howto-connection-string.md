---
title: Kapcsolatok karakterláncai – Azure Database for MySQL
description: Ez a dokumentum felsorolja a jelenleg támogatott kapcsolati karakterláncokat, amelyekkel az alkalmazások csatlakozhatnakC#Azure Database for MySQLhoz, beleértve a ADO.net (), a JDBC, a Node. js, az ODBC, a PHP, a Python és a Ruby nyelveket.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: bee98accd8ac404eb223975571b082dae754571a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770492"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Alkalmazások összekapcsolásának Azure Database for MySQL
Ez a témakör felsorolja a Azure Database for MySQL által támogatott, a sablonokkal és példákkal együtt támogatott kapcsolatok karakterlánc-típusokat. Előfordulhat, hogy a kapcsolatok karakterláncában különböző paraméterek és beállítások vannak.

- A tanúsítvány beszerzéséhez lásd: [az SSL konfigurálása](./howto-configure-ssl.md).
- {your_host} = \<servername >. mysql. database. Azure. com
- {your_user} @ {servername} = userID-formátum a hitelesítéshez.  Ha csak a felhasználóazonosító-t használja, a hitelesítés sikertelen lesz.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

Ebben a példában a kiszolgáló neve `mydemoserver`, az adatbázis neve `wpdb`, a Felhasználónév `WPAdmin`, és a jelszó `mypassword!2`. Ennek eredményeképpen a kapcsolatok karakterláncának a következőképpen kell szerepelnie:

```ado.net
Server= "mydemoserver.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>A kapcsolati sztring részleteinek beolvasása a Azure Portal
A [Azure Portal](https://portal.azure.com)nyissa meg a Azure Database for MySQL-kiszolgálót, majd kattintson a **kapcsolódási karakterláncok** lehetőségre a példányhoz tartozó karakterláncok listájának lekéréséhez: ![a Azure Portal kapcsolódási karakterláncok paneljét](./media/howto-connection-strings/connection-strings-on-portal.png)

A karakterlánc olyan adatokat tartalmaz, mint például az illesztőprogram, a kiszolgáló és más adatbázis-kapcsolati paraméterek. Módosítsa ezeket a példákat saját paraméterek használatára, például az adatbázis nevére, jelszavára stb. Ezt a karakterláncot használhatja a kód és az alkalmazások kiszolgálóhoz való kapcsolódáshoz.

## <a name="next-steps"></a>Következő lépések
- További információ a kapcsolatok könyvtárairól: [fogalmak – kapcsolatok kódtárai](./concepts-connection-libraries.md).
