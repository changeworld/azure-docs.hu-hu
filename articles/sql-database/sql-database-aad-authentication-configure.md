---
title: Az Azure Active Directory-fiókon alapuló hitelesítés konfigurálása
description: Megtudhatja, hogyan csatlakozhat SQL Databasehoz, felügyelt példányhoz és SQL Data Warehousehoz Azure Active Directory hitelesítéssel – az Azure AD konfigurálása után.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 01/07/2020
ms.openlocfilehash: 7c439091cecca153779017358188e6c1388086a9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256652"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Azure Active Directory hitelesítés konfigurálása és kezelése SQL-sel

Ebből a cikkből megtudhatja, hogyan hozhat létre és tölthet fel Azure AD-t, majd hogyan használhatja az Azure AD-t az Azure [SQL Database](sql-database-technical-overview.md), a [felügyelt példány](sql-database-managed-instance.md)és a [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)használatával. Az áttekintést lásd: [Azure Active Directory hitelesítés](sql-database-aad-authentication.md).

> [!NOTE]
> Ez a cikk az Azure SQL Serverre vonatkozik, valamint az Azure SQL Serveren létrehozott SQL Database és SQL Data Warehouse adatbázisokra is. Az egyszerűség kedvéért a jelen témakörben az SQL Database és az SQL Data Warehouse megnevezése egyaránt SQL Database.

> [!IMPORTANT]  
> Az Azure-beli virtuális gépen futó SQL Serverhoz való csatlakozás Azure Active Directory-fiók használata esetén nem támogatott. Ehelyett használjon tartományi Active Directory fiókot.

## <a name="create-and-populate-an-azure-ad"></a>Azure AD létrehozása és feltöltése

Hozzon létre egy Azure AD-t, és töltse fel a felhasználókat és a csoportokat. Az Azure AD lehet a kezdeti Azure AD által felügyelt tartomány. Az Azure AD az Azure AD-vel összevont helyszíni Active Directory tartományi szolgáltatások is lehet.

További információk a következő témakörökben találhatók: [Helyszíni identitások integrálása az Azure Active Directoryval](../active-directory/hybrid/whatis-hybrid-identity.md), [Saját tartománynév hozzáadása az Azure AD-hez](../active-directory/active-directory-domains-add-azure-portal.md), [A Microsoft Azure mostantól támogatja a Windows Server Active Directoryval való összevonást](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Az Azure AD-címtár felügyelete](../active-directory/fundamentals/active-directory-administer.md), [Az Azure AD kezelése Windows PowerShell használatával](/powershell/azure/overview) és [Hibrid identitás – szükséges portok és protokollok](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Azure-előfizetés hozzárendelése vagy hozzáadása Azure Active Directoryhoz

1. Rendelje hozzá az Azure-előfizetést a Azure Active Directoryhoz, hogy a címtárban az adatbázist futtató Azure-előfizetés számára megbízható könyvtárat biztosít. Részletekért lásd: az [Azure-előfizetések az Azure ad-vel való társításának módja](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

2. A Azure Portal Directory kapcsolójának használatával váltson át a tartományhoz társított előfizetésre.

   > [!IMPORTANT]
   > Minden Azure-előfizetés bizalmi kapcsolattal rendelkezik egy Azure AD-példányhoz. Ez azt jelenti, hogy megbízik ebben a címtárban a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban a címtárban, de egy előfizetés csak egy címtárban bízhat meg. Ez az előfizetés és a címtár közötti bizalmi kapcsolat nem olyan, mint ami az előfizetés és az Azure összes többi erőforrása (webhelyek, adatbázisok stb.) között áll fenn, amelyek inkább az előfizetések gyermekerőforrásainak számítanak. Ha egy előfizetés lejár, akkor az előfizetéssel társított ilyen egyéb erőforrások hozzáférése is lejár. De a címtár az Azure-ban elérhető marad, és más előfizetéseket társíthat ezzel a címtárral, és folytathatja a címtár felhasználóinak kezelését. Az erőforrásokkal kapcsolatos további információkért lásd: [az erőforrás-hozzáférés ismertetése az Azure-ban](../active-directory/active-directory-b2b-admin-add-users.md). További információ erről a megbízható kapcsolatról: [Azure-előfizetés hozzárendelése vagy hozzáadása Azure Active Directoryhoz](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Azure AD-rendszergazda létrehozása az Azure SQL Serverhez

Minden Azure SQL Server (amely egy SQL Database vagy SQL Data Warehouse) a teljes Azure SQL Server-rendszergazdai fiókkal indul el. Létre kell hozni egy második SQL Server rendszergazdát, amely egy Azure AD-fiók. Ez a rendszerbiztonsági tag egy tárolt adatbázis-felhasználóként jön létre a Master adatbázisban. Rendszergazdákként a kiszolgáló-rendszergazdai fiókok minden felhasználói adatbázisban a **db_owner** szerepkör tagjai, és az egyes felhasználói adatbázisokat **dbo** -felhasználóként adja meg. A kiszolgálói rendszergazdai fiókokkal kapcsolatos további információkért lásd: [adatbázisok és bejelentkezések kezelése Azure SQL Databaseban](sql-database-manage-logins.md).

Ha Geo-replikációval Azure Active Directory használ, a Azure Active Directory rendszergazdát mind az elsődleges, mind a másodlagos kiszolgálókhoz be kell állítani. Ha a kiszolgáló nem rendelkezik Azure Active Directory-rendszergazdával, akkor Azure Active Directory bejelentkezések és a felhasználók "nem lehet csatlakozni" kiszolgálóhiba miatt.

> [!NOTE]
> Azok a felhasználók, akik nem egy Azure AD-fiókon (például az Azure SQL Server-rendszergazdai fiókon) alapulnak, nem hozhatnak létre Azure AD-alapú felhasználókat, mert nem rendelkeznek engedéllyel a javasolt adatbázis-felhasználók ellenőrzéséhez az Azure AD-vel.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Azure Active Directory-rendszergazda kiépítése a felügyelt példányhoz

> [!IMPORTANT]
> Csak akkor hajtsa végre ezeket a lépéseket, ha felügyelt példányt épít ki. Ezt a műveletet csak a globális/vállalati rendszergazda vagy az Azure AD Kiemelt szerepkörű rendszergazdája hajthatja végre. A következő lépések azt mutatják be, hogyan adhatók meg engedélyek a különböző jogosultságokkal rendelkező felhasználók számára a címtárban.

> [!NOTE]
> A GA előtt létrehozott Azure AD-rendszergazdák esetében, de továbbra is a GA utáni működést, a meglévő viselkedést nem változtatja meg. További részletekért tekintse meg az [új Azure ad-rendszergazdai funkciót a mi](#new-azure-ad-admin-functionality-for-mi) szakaszban.

A felügyelt példánynak engedélyre van szüksége az Azure AD olvasásához, hogy sikeresen hajtsa végre a felhasználókat, például a biztonsági csoporttagság vagy az új felhasználók létrehozása révén a felhasználók hitelesítését. Ahhoz, hogy működjön, engedélyeket kell adnia a felügyelt példánynak az Azure AD olvasásához. Ezt kétféleképpen teheti meg: a portálról és a PowerShellből. A következő lépések mindkét módszert követik.

1. A Azure Portal jobb felső sarokban válassza ki a kapcsolódást a lehetséges aktív könyvtárak listájának legördülő listához.

2. Válassza ki a megfelelő Active Directory alapértelmezett Azure AD-ként.

   Ez a lépés összekapcsolja a felügyelt példánnyal Active Directory társított előfizetést, és gondoskodik arról, hogy ugyanazt az előfizetést használja az Azure AD és a felügyelt példány esetében is.

3. Navigáljon a felügyelt példányhoz, és válassza ki az Azure AD-integrációhoz használni kívánt elemet.

   ![HRE](./media/sql-database-aad-authentication/aad.png)

4. Válassza ki a Active Directory felügyeleti oldal tetején található szalagcímet, és adja meg az engedélyt az aktuális felhasználónak. Ha globális/vállalati rendszergazdaként jelentkezett be az Azure AD-ben, azt a Azure Portal vagy a PowerShell használatával végezheti el az alábbi szkripttel.

    ![engedélyek megadása – portál](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

    # Get Azure AD role "Directory Users" and create if it doesn't exist
    $roleName = "Directory Readers"
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    if ($role -eq $null) {
        # Instantiate an instance of the role template
        $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
        Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
        $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    }

    # Get service principal for managed instance
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. A művelet sikeres befejeződése után a következő értesítés jelenik meg a jobb felső sarokban:

    ![sikeres](./media/sql-database-aad-authentication/success.png)

6. Most kiválaszthatja a felügyelt példányához tartozó Azure AD-rendszergazdát. Ehhez a Active Directory admin lapon válassza a **felügyeleti parancs beállítása** lehetőséget.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. A HRE-adminisztrátor lapon keressen rá egy felhasználóra, válassza ki a felhasználót vagy csoportot, hogy rendszergazda legyen, majd válassza a **kiválasztás**lehetőséget.

   A Active Directory felügyeleti oldal megjeleníti a Active Directory összes tagját és csoportját. A nem kiválasztható felhasználók vagy csoportok nem választhatók ki, mert nem támogatottak az Azure AD-rendszergazdák. Tekintse meg a támogatott rendszergazdák listáját az [Azure ad-funkciókban és korlátozásokban](sql-database-aad-authentication.md#azure-ad-features-and-limitations). A szerepköralapú hozzáférés-vezérlés (RBAC) csak a Azure Portalra vonatkozik, és nincs propagálva SQL Serverre.

    ![Azure Active Directory adminisztrátor hozzáadása](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. A Active Directory-rendszergazda lap tetején válassza a **Mentés**lehetőséget.

    ![Mentés](./media/sql-database-aad-authentication/save.png)

    A rendszergazda módosításának folyamata több percet is igénybe vehet. Ezután megjelenik az új rendszergazda a Active Directory admin mezőben.

Miután kiépített egy Azure AD-rendszergazdát a felügyelt példányhoz, megkezdheti az Azure AD Server-rendszerbiztonsági tag (bejelentkezések) létrehozását a <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">bejelentkezési szintaxis létrehozásával</a> . További információ: a [felügyelt példányok áttekintése](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Ha később el szeretné távolítani a rendszergazdát, a Active Directory-rendszergazda lap tetején válassza a **rendszergazda eltávolítása**lehetőséget, majd kattintson a **Mentés**gombra.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>Új Azure AD-rendszergazdai funkciók a MI számára

Az alábbi táblázat összefoglalja az Azure AD-beli nyilvános előzetes bejelentkezési adminisztrátor funkcióit, valamint az Azure AD-bejelentkezésekhez készült új funkciókat.

| Az Azure AD bejelentkezési rendszergazdája a nyilvános előzetes verzióban | Az Azure AD rendszergazdai GA funkciói a MI számára |
| --- | ---|
| Hasonló módon viselkedik, mint a SQL Database Azure AD-rendszergazdája, amely lehetővé teszi az Azure AD-hitelesítést, de az Azure AD-rendszergazda nem tud Azure AD-vagy SQL-bejelentkezéseket létrehozni a Master dB-ben a MI számára. | Az Azure AD-rendszergazda rendszergazdai jogosultságokkal rendelkezik, és létrehozhat HRE és SQL-bejelentkezéseket a Master dB-ben a MI számára. |
| Nem szerepel a sys. server_principals nézetben | Megtalálható a sys. server_principals nézetben |
| Lehetővé teszi, hogy az egyes Azure AD vendég felhasználók Azure AD-rendszergazdaként legyenek beállítva a MI számára. További információ: [Azure Active Directory B2B együttműködéssel rendelkező felhasználók hozzáadása a Azure Portal](../active-directory/b2b/add-users-administrator.md). | Az Azure AD-csoport létrehozásához a vendég felhasználók tagként való létrehozását igényli a csoport Azure AD-rendszergazdaként való beállításához. További információ: az [Azure ad Business to Business támogatása](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support). |

Az ajánlott eljárás a GA előtt létrehozott meglévő Azure AD-rendszergazdák számára, és továbbra is a GA utáni működés alaphelyzetbe állítása az Azure AD-Azure Portal rendszergazdának a "rendszergazda eltávolítása" és a "rendszergazda beállítása" lehetőséggel azonos Azure AD-felhasználó vagy-csoport esetén.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>Az Azure AD bejelentkezési GA-vel kapcsolatos ismert problémák

- Ha egy Azure AD-bejelentkezés létezik a MI számára készült Master adatbázisban, a T-SQL parancs `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`használatával jött létre, az nem állítható be Azure AD-rendszergazdaként a MI számára. Az Azure AD-bejelentkezés létrehozásához a Azure Portal, a PowerShell vagy a CLI-parancsok használatával hibát tapasztalhat a bejelentkezés Azure AD-rendszergazdaként való beállításakor.
  - A bejelentkezést a főadatbázisból a `DROP LOGIN [myaadaccount]`parancs használatával kell eldobni, mielőtt a fiókot Azure AD-rendszergazdaként lehetne létrehozni.
  - A `DROP LOGIN` sikeres végrehajtása után állítsa be az Azure AD-beli rendszergazdai fiókot a Azure Portal. 
  - Ha nem tudja beállítani az Azure AD-beli rendszergazdai fiókot, a bejelentkezéshez ellenőrizze a felügyelt példány Master adatbázisát. Használja a következő parancsot: `SELECT * FROM sys.server_principals`
  - Az Azure AD-rendszergazda beállítása a MI számára automatikusan létrehoz egy bejelentkezési azonosítót a főadatbázisban ehhez a fiókhoz. Az Azure AD-rendszergazda eltávolításával automatikusan elkerülheti a bejelentkezést a Master adatbázisból.

- Az egyéni Azure AD vendég felhasználói nem használhatók Azure AD-rendszergazdákként a MI számára. A vendég felhasználók számára az Azure AD-rendszergazdaként beállítani kívánt Azure AD-csoport részét kell képeznie. Jelenleg a Azure Portal panel nem szürke a vendég felhasználók számára egy másik Azure AD-hoz, így a felhasználók továbbra is elérhetik a rendszergazda beállításait. Ha a vendég felhasználókat Azure AD-rendszergazdaként menti, a telepítő sikertelen lesz.
  - Ha szeretné, hogy a vendég felhasználó egy Azure AD-rendszergazdát hozzon létre a MI számára, foglalja bele a vendég felhasználót egy Azure AD-csoportba, és állítsa be ezt a csoportot Azure AD-rendszergazdaként.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell a felügyelt SQL-példányhoz

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell-parancsmagok futtatásához Azure PowerShell kell telepítenie és futnia. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager (RM) modult, de a jövőbeli fejlesztés az az. SQL modulhoz kapcsolódik. A AzureRM modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor.  Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

Az Azure AD-rendszergazda kiépítéséhez hajtsa végre a következő Azure PowerShell parancsokat:

- Connect-AzAccount
- Select-AzSubscription

A felügyelt SQL-példányok Azure AD-rendszergazdájának kiépítéséhez és kezeléséhez használt parancsmagok:

| Parancsmag neve | Leírás |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Kiépít egy Azure AD-rendszergazdát az SQL által felügyelt példányhoz a jelenlegi előfizetésben. (Az aktuális előfizetésből kell származnia)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Eltávolít egy Azure AD-rendszergazdát az SQL által felügyelt példányhoz az aktuális előfizetésben. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |A jelenlegi előfizetésben található SQL felügyelt példányra vonatkozó Azure AD-rendszergazda adatait adja vissza.|

Az alábbi parancs egy ManagedInstance01 nevű felügyelt példányhoz tartozó Azure AD-rendszergazdával kapcsolatos információkat olvas be, amely egy ResourceGroup01 nevű erőforráscsoporthoz van társítva.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

A következő parancs egy Adattervezők nevű Azure AD-rendszergazdai csoportot helyez üzembe a ManagedInstance01 nevű felügyelt példányhoz. Ez a kiszolgáló az erőforráscsoport ResourceGroup01 van társítva.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

A következő parancs eltávolítja az Azure AD-rendszergazdát az erőforráscsoport ResourceGroup01 társított ManagedInstanceName01 nevű felügyelt példányhoz.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az SQL felügyelt példányához az alábbi CLI-parancsok meghívásával is kiépítheti az Azure AD-rendszergazdát:

| Parancs | Leírás |
| --- | --- |
|[az SQL mi ad-admin Create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | A felügyelt SQL-példányok Azure Active Directory rendszergazdájának kiosztása. (Az aktuális előfizetésből kell származnia) |
|[az SQL mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | A felügyelt SQL-példányok Azure Active Directory rendszergazdájának eltávolítása. |
|[az SQL mi ad-admin List](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Az SQL felügyelt példányhoz jelenleg konfigurált Azure Active Directory-rendszergazda adatait adja vissza. |
|[az SQL mi ad-Admin Update](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | A felügyelt SQL-példányok Active Directory-rendszergazdájának frissítése. |

További információ a CLI-parancsokról: [az SQL mi](/cli/azure/sql/mi).

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Azure Active Directory-rendszergazda kiépítése a Azure SQL Database-kiszolgálóhoz

> [!IMPORTANT]
> Csak akkor hajtsa végre ezeket a lépéseket, ha Azure SQL Database kiszolgálót vagy adattárházat épít ki.

Az alábbi két eljárás bemutatja, hogyan építhet ki Azure Active Directory rendszergazdát az Azure SQL Serverhez a Azure Portal és a PowerShell használatával.

### <a name="azure-portal"></a>Azure Portal

1. A [Azure Portal](https://portal.azure.com/)jobb felső sarokban válassza ki a kapcsolódást a lehetséges aktív könyvtárak listájának legördülő listához. Válassza ki a megfelelő Active Directory alapértelmezett Azure AD-ként. Ez a lépés összekapcsolja az előfizetéshez kapcsolódó Active Directory az Azure SQL Serverrel, és gondoskodik arról, hogy ugyanazt az előfizetést használja az Azure AD és a SQL Server esetében is. (Az Azure SQL Server Azure SQL Database vagy Azure SQL Data Warehouse is üzemeltethető.)

    ![Válasszon-ad][8]

2. Keresse meg és válassza ki az **SQL Servert**.

    ![SQL-kiszolgálók keresése és kiválasztása](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Ezen az oldalon az **SQL-kiszolgálók**kiválasztása előtt kiválaszthatja a név melletti **csillagot** a kategória *kedvencéhez* , és hozzáadhat **SQL-kiszolgálókat** a bal oldali navigációs sávon.

3. **SQL Server** lapon válassza a **Active Directory rendszergazda**lehetőséget.

4. A **Active Directory-rendszergazda** lapon válassza a **rendszergazda beállítása**lehetőséget.

    ![SQL Servers set Active Directory admin](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. A rendszergazda **hozzáadása** lapon keressen rá egy felhasználóra, válassza ki a felhasználót vagy csoportot, majd válassza a **kiválasztás**lehetőséget. (A Active Directory felügyeleti oldal megjeleníti a Active Directory összes tagját és csoportját. A nem kiválasztható felhasználók vagy csoportok nem választhatók ki, mert nem támogatottak az Azure AD-rendszergazdák. (Tekintse meg a támogatott rendszergazdák listáját az **Azure ad-szolgáltatások és-korlátozások** című szakaszban [Azure Active Directory hitelesítés használata SQL Database vagy SQL Data Warehouse használatával történő hitelesítéshez](sql-database-aad-authentication.md).) A szerepköralapú hozzáférés-vezérlés (RBAC) csak a portálra vonatkozik, és nincs propagálva a SQL Server.

    ![Azure Active Directory rendszergazda kiválasztása](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. A **Active Directory-rendszergazda** lap tetején válassza a **Mentés**lehetőséget.

    ![rendszergazda mentése](./media/sql-database-aad-authentication/save-admin.png)

A rendszergazda módosításának folyamata több percet is igénybe vehet. Ezután megjelenik az új rendszergazda a **Active Directory admin** mezőben.

   > [!NOTE]
   > Az Azure AD-rendszergazda beállításakor az új rendszergazda neve (felhasználó vagy csoport) nem lehet már jelen a virtuális főadatbázisban SQL Server hitelesítési felhasználóként. Ha van ilyen, az Azure AD-rendszergazda telepítése meghiúsul; a létrehozás visszavonása és annak jelzése, hogy a rendszergazda (név) már létezik. Mivel az ilyen SQL Server hitelesítési felhasználó nem része az Azure AD-nek, az Azure AD-hitelesítéssel való csatlakozáshoz szükséges bármilyen erőfeszítés sikertelen lesz.

Ha később el szeretné távolítani a rendszergazdát, a **Active Directory-rendszergazda** lap tetején válassza a **rendszergazda eltávolítása**lehetőséget, majd kattintson a **Mentés**gombra.

### <a name="powershell-for-azure-sql-database-and-azure-sql-data-warehouse"></a>PowerShell Azure SQL Database és Azure SQL Data Warehouse

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell-parancsmagok futtatásához Azure PowerShell kell telepítenie és futnia. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview). Az Azure AD-rendszergazda kiépítéséhez hajtsa végre a következő Azure PowerShell parancsokat:

- Connect-AzAccount
- Select-AzSubscription

Az Azure AD-rendszergazda létesítéséhez és kezeléséhez használt parancsmagok Azure SQL Database és Azure SQL Data Warehousehoz:

| Parancsmag neve | Leírás |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Kiépít egy Azure Active Directory rendszergazdát az Azure SQL Serverhez vagy Azure SQL Data Warehousehoz. (Az aktuális előfizetésből kell származnia) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Eltávolít egy Azure Active Directory rendszergazdát az Azure SQL Serverhez vagy Azure SQL Data Warehousehoz. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Az Azure SQL Serverhez vagy Azure SQL Data Warehousehoz jelenleg konfigurált Azure Active Directory-rendszergazda adatait adja vissza. |

Az egyes parancsokra vonatkozó további információk megtekintéséhez használja a Get-Help PowerShell-parancsot. Például: `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

A következő parancsfájl a **Group-23**nevű erőforráscsoport **DBA_Group** (objektumazonosító `40b79501-b343-44ed-9ce7-da4c8cc7353f`) nevű Azure ad-rendszergazdai csoportját is kiépíti a **demo_server** -kiszolgálóhoz:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

A **DisplayName** beviteli paraméter az Azure ad megjelenítendő nevét vagy az egyszerű felhasználónevet is elfogadja. Például ``DisplayName="John Smith"`` és ``DisplayName="johns@contoso.com"``. Az Azure AD-csoportok esetében csak az Azure AD megjelenítendő neve támogatott.

> [!NOTE]
> A Azure PowerShell parancs ```Set-AzSqlServerActiveDirectoryAdministrator``` nem akadályozza meg, hogy az Azure AD-rendszergazdák kiépítsék a nem támogatott felhasználók számára. Nem támogatott felhasználó is kiépíthető, de nem tud kapcsolódni egy adatbázishoz.

A következő példa a választható **ObjectId**használja:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> Az Azure AD- **ObjectId** akkor van szükség, ha a **DisplayName attribútum** nem egyedi. A **ObjectId** és a **DisplayName** értékek beolvasásához használja klasszikus Azure portál Active Directory szakaszát, és tekintse meg egy felhasználó vagy csoport tulajdonságait.

Az alábbi példa az Azure SQL Server aktuális Azure AD-rendszergazdájával kapcsolatos információkat adja vissza:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

Az alábbi példa egy Azure AD-rendszergazdát távolít el:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure AD-rendszergazdák üzembe helyezéséhez hívja a következő CLI-parancsokat:

| Parancs | Leírás |
| --- | --- |
|[az SQL Server ad-admin Create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Kiépít egy Azure Active Directory rendszergazdát az Azure SQL Serverhez vagy Azure SQL Data Warehousehoz. (Az aktuális előfizetésből kell származnia) |
|[az SQL Server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Eltávolít egy Azure Active Directory rendszergazdát az Azure SQL Serverhez vagy Azure SQL Data Warehousehoz. |
|[az SQL Server ad-admin List](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Az Azure SQL Serverhez vagy Azure SQL Data Warehousehoz jelenleg konfigurált Azure Active Directory-rendszergazda adatait adja vissza. |
|[az SQL Server ad-Admin Update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Egy Azure SQL Server-kiszolgáló vagy-Azure SQL Data Warehouse Active Directory-rendszergazdájának frissítése. |

További információ a CLI-parancsokról: [az SQL Server](/cli/azure/sql/server).

* * *

> [!NOTE]
> A REST API-k használatával kiépítheti Azure Active Directory-rendszergazdát is. További információ: [Service Management REST API referenciák és műveletek Azure SQL Database műveletekhez Azure SQL Database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Ügyfélszámítógépek konfigurálása

A következő szoftvereket kell telepítenie minden olyan ügyfélszámítógépen, amelyről az alkalmazások vagy a felhasználók az Azure AD-identitások használatával csatlakoznak Azure SQL Databasehoz vagy Azure SQL Data Warehousehoz:

- A .NET-keretrendszer 4,6-es vagy újabb verziója [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Azure Active Directory hitelesítési könyvtár a SQL Serverhoz (*ADAL. DLL*). Alább láthatók a letöltési hivatkozások, amelyek a ADAL tartalmazó legújabb SSMS, ODBC és OLE DB illesztőprogramot telepítik *. DLL* -függvénytár.
    1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [ODBC-illesztő, 17 SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [OLE DB 18. illesztőprogram SQL Server](https://www.microsoft.com/download/details.aspx?id=56730)

Ezeket a követelményeket az alábbiak szerint teljesítheti:

- A [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) vagy [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) legújabb verziójának telepítése megfelel a .NET-keretrendszer 4,6-es követelményének.
    - A SSMS a ADAL x86-os verzióját telepíti *. DLL*.
    - A SSDT a ADAL amd64 verzióját telepíti *. DLL*.
    - A [Visual Studio letöltések](https://www.visualstudio.com/downloads/download-visual-studio-vs) legújabb Visual Studio verziója megfelel a .NET-keretrendszer 4,6-es követelményének, de nem telepíti a ADAL szükséges amd64-es verzióját *. DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Tárolt adatbázis-felhasználók létrehozása az adatbázisban az Azure AD-identitásokhoz rendelve

> [!IMPORTANT]
> A felügyelt példány mostantól támogatja az Azure ad Server-rendszerbiztonsági tag (bejelentkezések) használatát, amely lehetővé teszi az Azure AD-felhasználók,-csoportok vagy-alkalmazások bejelentkezési adatainak létrehozását. Az Azure AD-kiszolgálói rendszerbiztonsági tag (Logins) lehetővé teszi a felügyelt példányok hitelesítését anélkül, hogy az adatbázis-felhasználók számára a tárolt adatbázis-felhasználóként kellene létrehozni. További információ: a [felügyelt példányok áttekintése](sql-database-managed-instance.md#azure-active-directory-integration). Az Azure AD Server-rendszerbiztonsági tag (Logins) létrehozásával kapcsolatos szintaxisért lásd: <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">create login (bejelentkezés létrehozása</a>).

Azure Active Directory hitelesítéshez az adatbázis-felhasználók a tárolt adatbázis-felhasználókként hozhatók létre. Egy Azure AD-identitáson alapuló adatbázis-felhasználó egy olyan adatbázis-felhasználó, amely nem rendelkezik bejelentkezési azonosítóval a főadatbázisban, és amely az adatbázishoz társított Azure AD-címtárban található identitáshoz rendelhető. Az Azure AD-identitás lehet egyéni felhasználói fiók vagy csoport is. További információ a tárolt adatbázis-felhasználókról: [tárolt adatbázis-felhasználók – az adatbázis hordozhatóvé tétele](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Az adatbázis felhasználói (a rendszergazdák kivételével) nem hozhatók létre a Azure Portal használatával. A RBAC szerepkörök nem lettek propagálva SQL Server, SQL Database vagy SQL Data Warehouse. Az Azure RBAC szerepkörei az Azure-erőforrások felügyeletére szolgálnak, és nem vonatkoznak az adatbázis engedélyeire. A **SQL Server közreműködő** szerepkör például nem biztosít hozzáférést a SQL Databasehoz vagy a SQL Data Warehousehoz való kapcsolódáshoz. A hozzáférési engedélyt közvetlenül az adatbázisban kell megadni a Transact-SQL-utasítások használatával.

> [!WARNING]
> A speciális karakterek, például a kettőspont `:` vagy a jel `&`, ha a T-SQL CREATE LOGINban felhasználónevek szerepelnek, és a felhasználói utasítások létrehozása nem támogatott.

Ha olyan Azure AD-alapú tárolt adatbázis-felhasználót szeretne létrehozni (az adatbázist birtokló kiszolgáló rendszergazdájától eltérő), akkor az adatbázishoz egy Azure AD-identitással kell csatlakoznia, amely legalább a **felhasználói engedély megváltoztatására** jogosult. Ezután használja a következő Transact-SQL szintaxist:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* lehet egy Azure ad-felhasználó egyszerű felhasználóneve vagy egy Azure ad-csoport megjelenített neve.

**Példák:** Az Azure AD összevont vagy felügyelt tartományi felhasználót képviselő tárolt adatbázis-felhasználó létrehozása:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Egy Azure AD-vagy összevont tartományi csoportot jelképező, tárolt adatbázis-felhasználó létrehozásához adja meg a biztonsági csoport megjelenítendő nevét:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Egy olyan tárolt adatbázis-felhasználó létrehozása, amely egy Azure AD-jogkivonattal csatlakozó alkalmazást jelképez:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Ehhez a parancshoz az SQL Access Azure AD-t (a "külső szolgáltatót") a bejelentkezett felhasználó nevében kell megadnia. Bizonyos esetekben előfordulhat, hogy az Azure AD egy kivételt ad vissza az SQL-nek. Ezekben az esetekben a felhasználó a 33134-es SQL-hibát fogja látni, amelynek tartalmaznia kell a HRE-specifikus hibaüzenetet. A legtöbb esetben a hiba azt jelenti, hogy a rendszer megtagadja a hozzáférést, vagy a felhasználónak regisztrálnia kell az MFA-ban az erőforrás eléréséhez, vagy az első féltől származó alkalmazások közötti hozzáférést előhitelesítéssel kell kezelni. Az első két esetben a problémát általában a felhasználó HRE-bérlője által megadott feltételes hozzáférési szabályzatok okozzák: megakadályozza, hogy a felhasználó hozzáférjen a külső szolgáltatóhoz. A HITELESÍTÉSSZOLGÁLTATÓI házirendek frissítésével engedélyezheti a (z) "00000002-0000-0000-C000-000000000000" alkalmazáshoz való hozzáférést (a HRE Graph API) a probléma megoldásához. Abban az esetben, ha a hiba azt mondja, hogy a rendszer az első féltől származó alkalmazások hozzáférését az előhitelesítésen keresztül kell kezelni, a probléma az, hogy a felhasználó egyszerű szolgáltatásként van bejelentkezve. A parancsnak sikeresnek kell lennie, ha egy felhasználó hajtja végre.

> [!TIP]
> Az Azure-előfizetéshez társított Azure Active Directorytól eltérő Azure Active Directory nem hozhat létre közvetlenül felhasználót. Azonban a társított Active Directoryban (más néven külső felhasználók) importált felhasználók a bérlő Active Directory Active Directory csoportjába is hozzáadhatók. Ha létrehoz egy tárolt adatbázis-felhasználót az AD-csoport számára, a külső Active Directory felhasználói hozzáférhetnek SQL Databasehoz.

A tárolt adatbázis-felhasználók Azure Active Directory identitások alapján történő létrehozásával kapcsolatos további információkért lásd: [felhasználó létrehozása (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Az Azure SQL Server Azure Active Directory-rendszergazdájának eltávolítása megakadályozza, hogy bármely Azure AD-hitelesítési felhasználó csatlakozzon a kiszolgálóhoz. Ha szükséges, a használhatatlan Azure AD-felhasználókat a SQL Database rendszergazdája manuálisan is elvégezheti.

> [!NOTE]
> Ha **lejárt a kapcsolat időkorlátja**, előfordulhat, hogy a kapcsolati karakterlánc `TransparentNetworkIPResolution` paraméterét false értékre kell állítania. További információ: [a .NET-keretrendszer 4.6.1-TransparentNetworkIPResolution kapcsolati időtúllépési hibája](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Amikor létrehoz egy adatbázis-felhasználót, a felhasználó megkapja a **kapcsolódási** engedélyt, és a **nyilvános** szerepkör tagjaként tud csatlakozni az adatbázishoz. Kezdetben csak a felhasználó számára elérhető engedélyek érvényesek a **nyilvános** szerepkörre, vagy bármely olyan Azure ad-csoportnak biztosított engedély, amely tagja a felhasználónak. Miután kiépített egy Azure AD-alapú tárolt adatbázis-felhasználót, a felhasználónak további engedélyeket is megadhat, ugyanúgy, ahogy bármely más típusú felhasználó számára engedélyezi. Általában engedélyeket ad az adatbázis szerepköreinek, és felhasználókat ad hozzá a szerepkörökhöz. További információ: adatbázismotor- [engedélyek alapjai](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). A speciális SQL Database szerepkörökkel kapcsolatos további információkért lásd: [adatbázisok és bejelentkezések kezelése Azure SQL Databaseban](sql-database-manage-logins.md).
A felügyelt tartományba külső felhasználóként importált összevont tartományi felhasználói fióknak a felügyelt tartományi identitást kell használnia.

> [!NOTE]
> Az Azure AD-felhasználók az E (EXTERNAL_USER) típusú adatbázis-metaadatokban vannak megjelölve, az X típusú csoportok esetében (EXTERNAL_GROUPS). További információ: [sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Kapcsolódás a felhasználói adatbázishoz vagy az adatraktárhoz a SSMS vagy a SSDT használatával  

Ha ellenőrizni szeretné, hogy az Azure AD-rendszergazda megfelelően van-e beállítva, kapcsolódjon a **Master** adatbázishoz az Azure ad rendszergazdai fiók használatával.
Egy Azure AD-alapú tárolt adatbázis-felhasználó (az adatbázist birtokló kiszolgáló rendszergazdája kivételével) kiépítéséhez kapcsolódjon az adatbázishoz egy olyan Azure AD-identitással, amely hozzáféréssel rendelkezik az adatbázishoz.

> [!IMPORTANT]
> Azure Active Directory hitelesítés támogatása [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) és a Visual Studio 2015 [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) érhető el. A SSMS augusztusi 2016-es kiadása a Active Directory univerzális hitelesítés támogatását is magában foglalja, amely lehetővé teszi, hogy a rendszergazdák telefonhívást, szöveges üzenetet, intelligens kártyákat PIN-kóddal vagy a mobil alkalmazások értesítésével megkövetelje Multi-Factor Authentication használatát.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Azure AD-identitás használata a SSMS vagy a SSDT használatával történő kapcsolódáshoz

A következő eljárások bemutatják, hogyan csatlakozhat egy Azure AD-identitással rendelkező SQL-adatbázishoz SQL Server Management Studio vagy SQL Server adatbázis-eszközök használatával.

### <a name="active-directory-integrated-authentication"></a>Integrált hitelesítés Active Directory

Ezt a módszert akkor használja, ha egy összevont tartomány Azure Active Directory hitelesítő adataival jelentkezett be a Windowsba.

1. Indítsa el Management Studio vagy az adateszközöket, és a **Kapcsolódás a kiszolgálóhoz** (vagy a **Kapcsolódás az adatbázis-kezelőhöz**) párbeszédpanel **hitelesítés** mezőjében válassza a **Active Directory integrált**elemet. Nincs szükség jelszóra, vagy megadható, mert a rendszer a meglévő hitelesítő adatait fogja megjeleníteni a kapcsolatban.

    ![AD integrált hitelesítés kiválasztása][11]

2. Válassza a **Beállítások** gombot, és a **kapcsolat tulajdonságai** lapon a **Kapcsolódás az adatbázishoz** mezőbe írja be annak a felhasználói adatbázisnak a nevét, amelyhez csatlakozni szeretne. (Az **Active Directory Domain Name vagy a bérlői azonosító**beállítás csak az **MFA-kapcsolatok** esetében támogatott, ellenkező esetben szürkén jelenik meg.)  

    ![Adja meg az adatbázis nevét][13]

## <a name="active-directory-password-authentication"></a>Jelszavas hitelesítés Active Directory

Ezt a módszert akkor használja, ha Azure ad-beli egyszerű névvel csatlakozik az Azure AD által felügyelt tartomány használatával. Azt is megteheti, hogy összevont fiókokat használ a tartományhoz való hozzáférés nélkül, például ha távolról dolgozik.

Ezzel a módszerrel a natív vagy összevont Azure AD-felhasználók számára az Azure AD-vel végezhet hitelesítést az SQL DB/DW használatával. Egy natív felhasználó az Azure AD-ben explicit módon jön létre, és felhasználónévvel és jelszóval van hitelesítve, míg egy összevont felhasználó egy olyan Windows-felhasználó, amelynek tartományát az Azure AD-vel összevonták. Az utóbbi módszer (felhasználói & jelszó használata) akkor használható, ha a felhasználó Windows-hitelesítő adatait szeretné használni, de a helyi számítógép nincs csatlakoztatva a tartományhoz (például egy távelérés használatával). Ebben az esetben a Windows-felhasználó megadhatja a tartományi fiókját és jelszavát, és az összevont hitelesítő adatok használatával hitelesítheti magát az SQL DB/DW-ben.

1. Indítsa el Management Studio vagy az adateszközöket, és a **Kapcsolódás a kiszolgálóhoz** (vagy a **Kapcsolódás az adatbázis-kezelőhöz**) párbeszédpanel **hitelesítés** mezőjében válassza a **Active Directory-Password**lehetőséget.

2. **A Felhasználónév mezőbe írja** be a Azure Active Directory felhasználónevét a **Felhasználónév\@domain.com**formátumban. A felhasználónévnek a Azure Active Directory vagy egy olyan fiókból kell származnia, amely a Azure Active Directory összevonása.

3. A **jelszó** mezőbe írja be a Azure Active Directory fiók vagy összevont tartományi fiók felhasználói jelszavát.

    ![AD jelszó-hitelesítés kiválasztása][12]

4. Válassza a **Beállítások** gombot, és a **kapcsolat tulajdonságai** lapon a **Kapcsolódás az adatbázishoz** mezőbe írja be annak a felhasználói adatbázisnak a nevét, amelyhez csatlakozni szeretne. (Lásd az előző beállításban található ábrát.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Azure AD-identitás használata az ügyfélalkalmazások közötti kapcsolódáshoz

A következő eljárások bemutatják, hogyan csatlakozhat egy Azure AD-identitással rendelkező SQL-adatbázishoz egy ügyfélalkalmazás használatával.

### <a name="active-directory-integrated-authentication"></a>Integrált hitelesítés Active Directory

Az integrált Windows-hitelesítés használatához a tartomány Active Directory összevontnak kell lennie Azure Active Directoryval. Az adatbázishoz csatlakozó ügyfélalkalmazás (vagy szolgáltatás) tartományhoz csatlakozó gépen kell futnia a felhasználó tartományi hitelesítő adatai alatt.

Ha integrált hitelesítést és Azure AD-identitást használó adatbázishoz szeretne csatlakozni, az adatbázis-kapcsolati karakterláncban lévő hitelesítési kulcsszót Active Directory integráltra kell beállítani. Az alábbi C# MINTAKÓD az ADO .NET-et használja.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

A kapcsolati karakterlánc kulcsszava `Integrated Security=True` nem támogatott Azure SQL Databasehoz való csatlakozáshoz. ODBC-kapcsolatok esetén el kell távolítania a szóközöket, és be kell állítania a hitelesítést a "ActiveDirectoryIntegrated" értékre.

### <a name="active-directory-password-authentication"></a>Jelszavas hitelesítés Active Directory

Ha integrált hitelesítést és Azure AD-identitást használó adatbázishoz szeretne csatlakozni, a hitelesítési kulcsszót Active Directory jelszóra kell beállítani. A kapcsolatok karakterláncának tartalmaznia kell a felhasználói azonosító/UID azonosítót, valamint a jelszó/PWD kulcsszavakat és értékeket. Az alábbi C# MINTAKÓD az ADO .NET-et használja.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

További információ az Azure AD-hitelesítési módszerekről az [Azure ad-hitelesítés GitHub-bemutatójában](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)elérhető bemutató kód minták használatával.

## <a name="azure-ad-token"></a>Azure AD-jogkivonat

Ez a hitelesítési módszer lehetővé teszi, hogy a középső rétegbeli szolgáltatások a Azure Active Directory (HRE) jogkivonat beszerzésével csatlakozzanak Azure SQL Databasehoz vagy Azure SQL Data Warehousehoz. Kifinomult forgatókönyveket, például tanúsítványalapú hitelesítést tesz lehetővé. Az Azure AD-jogkivonat-hitelesítés használatához négy alapvető lépést kell végrehajtania:

1. Regisztrálja alkalmazását Azure Active Directory és szerezze be a kód ügyfél-AZONOSÍTÓját.
2. Hozzon létre egy adatbázis-felhasználót, amely az alkalmazást jelképezi. (A 6. lépésben korábban fejeződött be.)
3. Hozzon létre egy tanúsítványt az ügyfélszámítógépen, amelyen az alkalmazás fut.
4. Adja hozzá a tanúsítványt az alkalmazás kulcsaként.

Példa a kapcsolatok karakterláncára:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

További információ: [SQL Server biztonsági blog](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). A tanúsítványok hozzáadásával kapcsolatos információkért lásd: a [tanúsítványalapú hitelesítés első lépései a Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

A következő utasítások az Sqlcmd 13,1-es verziójának használatával csatlakoznak, amely a [letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=53591)érhető el.

> [!NOTE]
> a `sqlcmd` a `-G` paranccsal nem működik a rendszeridentitásokkal, és felhasználói egyszerű bejelentkezést igényel.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>Az Azure AD-hitelesítés hibaelhárítása

Az Azure AD-hitelesítéssel kapcsolatos hibaelhárítási útmutató a következő blogban található: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Következő lépések

- A bejelentkezések, a felhasználók, az adatbázis-szerepkörök és a SQL Databaseban lévő engedélyek áttekintését lásd: [bejelentkezések, felhasználók, adatbázis-szerepkörök és felhasználói fiókok](sql-database-manage-logins.md).
- További információ az adatbázis résztvevőivel kapcsolatban: [Résztvevők](https://msdn.microsoft.com/library/ms181127.aspx).
- További információ az adatbázis-szerepkörökkel kapcsolatban: [Adatbázis-szerepkörök](https://msdn.microsoft.com/library/ms189121.aspx).
- További információ az SQL Database tűzfalszabályaival kapcsolatban: [SQL Database tűzfalszabályok](sql-database-firewall-configure.md).

<!--Image references-->
[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
