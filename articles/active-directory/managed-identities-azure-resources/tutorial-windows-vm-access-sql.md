---
title: Oktatóanyag`:` felügyelt identitás használata az Azure SQL-Windows-Azure AD eléréséhez
description: Az oktatóanyag azt ismerteti, hogyan lehet hozzáférni az Azure SQL-hez egy Windows VM rendszer által hozzárendelt felügyelt identitásával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fc5596c6914b77b09db10528af891d7e6bd0159
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977864"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Oktatóanyag: Hozzáférés az Azure SQL-hez egy Windows VM rendszer által hozzárendelt felügyelt identitásával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az oktatóanyag bemutatja, hogyan használhat rendszer által hozzárendelt identitást egy Windows rendszerű virtuális gépen (VM) egy Azure SQL Server eléréséhez. A felügyeltszolgáltatás-identitások kezelését automatikusan az Azure végzi, és lehetővé teszi a hitelesítést az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy be kellene szúrnia a hitelesítő adatokat a kódba. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Azure SQL Server-hozzáférés engedélyezése a VM számára
> * Azure AD-hitelesítés engedélyezése az SQL-kiszolgáló számára
> * A virtuális gép rendszerhez hozzárendelt identitását képviselő tartalmazott felhasználó létrehozása az adatbázisban
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd egy Azure SQL Server-kiszolgáló lekérdezése a jogkivonattal

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="enable"></a>Engedélyezés

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]


## <a name="grant-access"></a>Hozzáférés biztosítása

A virtuális gép egy Azure SQL Server-adatbázishoz való hozzáférésének engedélyezéséhez használhat egy meglévő SQL-kiszolgálót, vagy létrehozhat egy újat. Ha új kiszolgálót és adatbázist szeretne létrehozni az Azure Portalon, kövesse ennek az [Azure SQL rövid útmutatónak](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) a lépéseit. Az [Azure SQL dokumentációjában](https://docs.microsoft.com/azure/sql-database/) olyan rövid útmutatók is vannak, amelyek az Azure CLI-t és az Azure PowerShellt használják.

Két lépés kell hozzá, hogy a VM hozzá tudjon férni egy adatbázishoz:

1. Engedélyezni kell az Azure AD-hitelesítést az SQL-kiszolgáló számára.
2. Létre kell hozni egy, a virtuális gép rendszerhez hozzárendelt identitását képviselő **tartalmazott felhasználót** az adatbázisban.

### <a name="enable-azure-ad-authentication"></a>Azure AD-hitelesítés engedélyezése

**[Az Azure ad-hitelesítés konfigurálása az SQL Serverhez](/azure/sql-database/sql-database-aad-authentication-configure):**

1.  Az Azure Portal bal oldali navigációs sávjában válassza az **SQL-kiszolgálók** elemet.
2.  Kattintson az Azure AD-hitelesítéshez engedélyezni kívánt SQL-kiszolgálóra.
3.  A panel **Beállítások** szakaszában kattintson az **Active Directory-rendszergazda** elemre.
4.  Kattintson a **Rendszergazda beállítása** elemre a parancssávon.
5.  Válassza ki azt az Azure AD felhasználói fiókot, amelyet a kiszolgáló rendszergazdájává szeretne tenni, és kattintson a **Kiválasztás** lehetőségre.
6.  Kattintson a **Mentés** gombra a parancssávon.

### <a name="create-contained-user"></a>Foglalt felhasználó létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre egy tárolt felhasználót a virtuális gép rendszerhez rendelt identitását képviselő adatbázisban. Ehhez a lépéshez [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) szükséges. Mielőtt hozzálátna, hasznos lehet áttekinteni az Azure AD-integráció hátterével foglalkozó következő cikkeket:

- [Univerzális hitelesítés az SQL Database és az SQL Data Warehouse használatával (SSMS-támogatás az MFA-hoz)](/azure/sql-database/sql-database-ssms-mfa-authentication)
- [Azure Active Directory-hitelesítés konfigurálása és kezelése az SQL Database vagy az SQL Data Warehouse használatával](/azure/sql-database/sql-database-aad-authentication-configure)

Az SQL-ADATBÁZIShoz egyedi HRE megjelenítendő nevek szükségesek. Ezzel a HRE-fiókokat, például a felhasználókat, a csoportokat és az egyszerű szolgáltatásokat (alkalmazásokat), valamint a felügyelt identitás számára engedélyezett virtuálisgép-neveket egyedi módon kell definiálni a HRE a megjelenítendő nevükkel kapcsolatban. Az SQL DB ellenőrzi a HRE megjelenítendő nevét az ilyen felhasználók T-SQL-létrehozása során, és ha az nem egyedi, a parancs sikertelenül kéri az adott fiók egyedi HRE-megjelenítési nevének megadását.

**Egy foglalt felhasználó létrehozása:**

1. Indítsa el az SQL Server Management Studiót.
2. A **Connect to Server** (Csatlakozás kiszolgálóhoz) párbeszédablakban írja be az SQL-kiszolgáló nevét a **Server name** (Kiszolgáló neve) mezőbe.
3. Az **Authentication** (Hitelesítés) mezőben válassza ki az **Active Directory - Universal with MFA support** (Active Directory – univerzális, MFA-támogatással) lehetőséget.
4. A **User name** (Felhasználónév) mezőbe írja be azon Azure AD-fiók nevét, amelyet a kiszolgáló rendszergazdájaként szeretne beállítani (például helen@woodgroveonline.com).
5. Kattintson a **Beállítások** gombra.
6. A **Connect to database** (Csatlakozás az adatbázishoz) mezőbe írja be a konfigurálni kívánt, nem rendszerszintű adatbázis nevét.
7. Kattintson a **Csatlakozás** gombra. Fejezze be a bejelentkezést.
8. Az **Object Explorerben** bontsa ki a **Databases** (Adatbázisok) mappát.
9. Kattintson a jobb gombbal egy felhasználói adatbázisra, majd kattintson a **New Query** (Új lekérdezés) menüpontra.
10. A lekérdezési ablakban írja be a következő sort, és kattintson az eszköztár **Execute** (Végrehajtás) gombjára:

    > [!NOTE]
    > Az alábbi parancsban a `VMName` a virtuális gép neve, amelyen az előfeltételek szakaszban a rendszerhez hozzárendelt identitást engedélyezte.
    
    ```
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```
    
    A parancsnak sikeresen futnia kell, és létre kell hoznia a virtuális gép rendszerhez hozzárendelt identitásának tartalmazott felhasználóját.
11. Törölje a lekérdezési ablakot, írja be a következő sort, és kattintson az eszköztár **Execute** (Végrehajtás) gombjára:

    > [!NOTE]
    > Az alábbi parancsban a `VMName` a virtuális gép neve, amelyen az előfeltételek szakaszban a rendszerhez hozzárendelt identitást engedélyezte.
    
    ```
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    A parancsnak sikeresen futnia kell, így a tartalmazott felhasználó képes lesz a teljes adatbázis olvasására.

A virtuális gépen futó kód most le tud kérni egy jogkivonatot a rendszer által hozzárendelt felügyelt identitással, amellyel hitelesíthet az SQL-kiszolgálón.

## <a name="access-data"></a>Adatok elérése

Ez a szakasz bemutatja, hogyan kérhet hozzáférési tokent a virtuális gép rendszer által hozzárendelt felügyelt identitásával, és hogyan hívhatja meg az Azure SQL-t. Az Azure SQL natív támogatást nyújt az Azure AD-hitelesítéshez, így közvetlenül is elfogadhatja az Azure-erőforrások felügyelt identitásaival beszerzett hozzáférési jogkivonatokat. Az SQL-lel létesített kapcsolat létrehozásához használja az **access token** metódust. Ez az Azure SQL és az Azure AD integrációjának része, és eltér attól a megoldástól, amikor a kapcsolati sztringen adja meg a hitelesítő adatokat.

A .NET-kód példa arra, hogy egy hozzáférési jogkivonat használatával nyitja meg az SQL-kapcsolatot. Ennek a kódnak a virtuális gépen kell futnia a VM rendszer által hozzárendelt felügyelt identitásához tartozó végpont eléréséhez. A hozzáférési jogkivonat metódusának használatához a **.NET-keretrendszer 4,6** -es vagy újabb verziójára vagy a **.net Core 2,2** -es vagy újabb verziójára van szükség. Cserélje le az AZURE-SQL-SERVERNAME és a DATABASE értékét a megfelelő értékre. Figyelje meg, hogy az Azure SQL erőforrás-azonosítója `https://database.windows.net/`.

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call managed identities for Azure resources endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream());
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

A PowerShell használatával is gyorsan tesztelhető a végpontok közötti beállítás anélkül, hogy alkalmazást kellene írnia és üzembe helyeznie a virtuális gépen.

1.  A portálon lépjen a **Virtuális gépek** lapra, lépjen a Windows VM-hez, és az **Áttekintés** területen kattintson a **Csatlakozás** elemre.
2.  A **Felhasználónév** és a **Jelszó** mezőbe azt a felhasználónevet és jelszót írja be, amelyet a Windows VM létrehozásakor adott meg.
3.  Most, hogy létrehozott egy **távoli asztali kapcsolatot** a virtuális géppel, nyissa meg a **PowerShellt** a távoli munkamenetben.
4.  A PowerShell `Invoke-WebRequest` parancsával küldjön kérést a helyi felügyelt identitás végpontjára, hogy lekérjen egy hozzáférési jogkivonatot az Azure SQL-hez.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    Konvertálja a választ JSON-objektumból PowerShell-objektummá. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Nyerje ki a hozzáférési jogkivonatot a válaszból.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5. Nyisson meg egy kapcsolatot az SQL-kiszolgálóval. Ne felejtse el lecserélni az AZURE-SQL-SERVERNAME és a DATABASE értékét.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Ezután hozzon létre és küldjön el egy lekérdezést a kiszolgálónak. Ne felejtse el lecserélni a TABLE értékét.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Vizsgálja meg a `$DataSet.Tables[0]` értékét, hogy áttekinthesse a lekérdezés eredményeit.


## <a name="disable"></a>Letiltás

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]


## <a name="next-steps"></a>Következő lépések

Az oktatóanyag bemutatta, hogyan használható a rendszer által hozzárendelt felügyelt identitás az Azure SQL Serverhez való hozzáférésre. További információ az Azure SQL Serverről:

> [!div class="nextstepaction"]
> [Az Azure SQL Database szolgáltatás](/azure/sql-database/sql-database-technical-overview)
