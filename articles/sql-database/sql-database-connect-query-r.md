---
title: Adatbázis lekérdezése az R és a Machine Learning Services használatával (előzetes verzió)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Ebből a cikkből megtudhatja, hogyan használhatja az R-szkripteket Azure SQL Database Machine Learning Services egy Azure SQL Database-adatbázishoz való kapcsolódáshoz és a Transact-SQL-utasítások használatával történő lekérdezéséhez.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: 7103afc29e4021d950d9a3634b190f4439ecfe8d
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768518"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Rövid útmutató: az R és a Machine Learning Services használata Azure SQL Database-adatbázisok lekérdezéséhez (előzetes verzió)

Ebben a rövid útmutatóban az R és a Machine Learning Services használatával csatlakozik egy Azure SQL Database-adatbázishoz, és T-SQL-utasítások használatával kérdez le adatokat.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy [Azure SQL Database-adatbázis](sql-database-single-database-get-started.md)
- Az R-t engedélyező [Machine learning Services](sql-database-machine-learning-services-overview.md) . [Regisztráljon az előzetes](sql-database-machine-learning-services-overview.md#signup)verzióra.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> A cikkben található parancsfájlok az **Adventure Works** -adatbázis használatára íródnak.

> [!NOTE]
> A nyilvános előzetes verzióban a Microsoft bevezeti Önt és lehetővé teszi a gépi tanulást a meglévő vagy az új adatbázishoz, azonban a felügyelt példányok központi telepítésének lehetősége jelenleg nem támogatott.

Az R-vel Machine Learning Services az Azure SQL Database egyik funkciója, amely az adatbázisbeli R-parancsfájlok futtatására szolgál. További információ: [R-projekt](https://www.r-project.org/).

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Navigáljon az **SQL-adatbázisok** vagy az **SQL-felügyelt példányok** lapra.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egyetlen adatbázishoz vagy a felügyelt példányhoz tartozó **gazdagép** melletti teljes kiszolgálónévhez. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

## <a name="create-code-to-query-your-sql-database"></a>Kód létrehozása az SQL-adatbázis lekérdezéséhez

1. Nyissa meg az **SQL Server Management Studiót**, és csatlakozzon az SQL-adatbázishoz.

   Ha segítségre van szüksége a csatlakozáshoz, tekintse meg [Az Azure SQL Database-adatbázisok csatlakoztatásához és lekérdezéséhez SQL Server Management Studio használata](sql-database-connect-query-ssms.md)című témakört.

1. Adja át a teljes R-szkriptet a [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárásnak.

   A parancsfájl a `@script` argumentummal halad át. A `@script` argumentumban szereplő összes értéknek érvényes R-kódnak kell lennie.
   
   >[!IMPORTANT]
   >Az ebben a példában szereplő kód a minta AdventureWorksLT-adatait használja, amelyeket az adatbázis létrehozásakor választhat forrásként. Ha az adatbázis különböző adatokkal rendelkezik, a SELECT lekérdezésben használja a saját adatbázisában lévő táblákat. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Ha hibába ütközik, előfordulhat, hogy a Machine Learning Services (with R) nyilvános előzetes verziója nincs engedélyezve az SQL-adatbázishoz. Lásd a fenti [előfeltételeket](#prerequisites) .

## <a name="run-the-code"></a>A kód futtatása

1. Hajtsa végre az [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) tárolt eljárást.

1. Győződjön meg arról, hogy az **üzenetek** ablakban a 20 legfontosabb kategória/termék sorok lesznek visszaadva.

## <a name="next-steps"></a>Következő lépések

- [Az első Azure SQL-adatbázis megtervezése](sql-database-design-first-database.md)
- [Azure SQL Database Machine Learning Services (R)](sql-database-machine-learning-services-overview.md)
- [Egyszerű R-parancsfájlok létrehozása és futtatása Azure SQL Database Machine Learning Servicesban (előzetes verzió)](sql-database-quickstart-r-create-script.md)
- [Speciális R függvények írása a Azure SQL Database Machine Learning Services használatával (előzetes verzió)](sql-database-machine-learning-services-functions.md)
