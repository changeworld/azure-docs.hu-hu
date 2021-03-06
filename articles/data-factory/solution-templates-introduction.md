---
title: A sablonok áttekintése
description: Megtudhatja, hogyan használható előre definiált sablon a Azure Data Factory gyors megkezdéséhez.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: f150234ecd3446858e8a6aa7d224eb3ad3d0efd3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927345"
---
# <a name="templates"></a>Sablonok

A sablonok előre meghatározott Azure Data Factory folyamatok, amelyek lehetővé teszik a Data Factory gyors megkezdését. A sablonok akkor hasznosak, ha új Data Factory, és gyors kezdésre van szükség. Ezek a sablonok csökkentik az adatintegrációs projektek kiépítésének fejlesztési idejét, ezáltal javítják a fejlesztői hatékonyságot.

## <a name="create-data-factory-pipelines-from-templates"></a>Data Factory folyamatok létrehozása sablonokból

A következő két módon teheti meg a Data Factory folyamat létrehozását a sablonból:

1.  Válassza a **folyamat létrehozása sablonból** lehetőséget az Áttekintés lapon a sablon-gyűjtemény megnyitásához.

    ![A sablon-gyűjtemény megnyitása az Áttekintés lapról](media/solution-templates-introduction/templates-intro-image1.png)

1.  A erőforrás-kezelő szerző lapján válassza a **+** , majd a **folyamat sablonból** lehetőséget a sablon-gyűjtemény megnyitásához.

    ![A sablon-gyűjtemény megnyitása a szerző lapról](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Sablon gyűjteménye

![A sablon gyűjteménye](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Data Factory sablonok

A Data Factory Azure Resource Manager sablonokat használ a következő adattároló-sablonok mentéséhez:. Megtekintheti az összes Resource Manager-sablont, valamint a Data Factory sablonokhoz használt jegyzékfájlt a GitHub-tárház [hivatalos Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/templates). A Microsoft által biztosított előre definiált sablonok közé tartoznak a következők, de nem korlátozódnak a következő elemekre:

-   Sablonok másolása:

    -   [Tömeges másolás az adatbázisból](solution-template-bulk-copy-with-control-table.md)
    
    -   [Új fájlok másolása a LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Több file-tároló másolása a fájl alapú tárolók között](solution-template-copy-files-multiple-containers.md)

    -   [Fájlok áthelyezése](solution-template-move-files.md)

    -   [Különbözeti másolat az adatbázisból](solution-template-delta-copy-with-control-table.md)

    -   Másolás \<forrás\>ról \<célhelyre\>

        -   [Amazon S3-ról Azure Data Lake Store Gen 2](solution-template-migration-s3-azure.md)

        -   A Google Big Queryből a 2. generációs Azure Data Lake Store

        -   HDF-ről Azure Data Lake Store Gen 2-re

        -   Netezza-ről Azure Data Lake Store Gen 1

        -   SQL Server a helyszínen Azure SQL Database

        -   SQL Server a helyszínen Azure SQL Data Warehouse

        -   A helyszíni Oracle-ből Azure SQL Data Warehouse

-   SSIS-sablonok

    -   SSIS-csomagok végrehajtásának ütemezett Azure-SSIS Integration Runtime

-   Sablonok átalakítása

    -   [ETL Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Saját sablonok

A folyamatokat sablonként is mentheti a folyamat lapon a **Mentés sablonként** lehetőség kiválasztásával.

![Folyamat mentése sablonként](media/solution-templates-introduction/templates-intro-image4.png)

Megtekintheti a sablonokként mentett folyamatokat a sablon-gyűjtemény **saját sablonok** szakaszában. Ezeket a erőforrás-kezelő **sablonok** szakaszában is megtekintheti.

![Saját sablonok](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> A saját sablonok funkció használatához engedélyeznie kell a GIT-integrációt. Az Azure DevOps GIT és a GitHub is támogatott.
