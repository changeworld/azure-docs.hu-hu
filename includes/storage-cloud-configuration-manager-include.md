---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038162"
---
A [Microsoft Azure Configuration Manager könyvtár a .NET-hez](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)egy osztályt biztosít a konfigurációs fájlokból származó kapcsolati sztringek elemzéséhez. A [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) osztály elemzi a konfigurációs beállításokat. Az alkalmazás az asztali gépen, egy mobileszközön, egy Azure-beli virtuális gépen vagy egy Azure Cloud Service-ben futó ügyfélalkalmazások beállításait elemzi.

A `CloudConfigurationManager` csomagra való hivatkozáshoz adja hozzá a következő `using` irányelveket:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

A következő példa bemutatja, hogyan kérhető le egy kapcsolati sztring egy konfigurációs fájlból:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Az Azure Configuration Manager használata nem kötelező. Használhat olyan API-t is, mint például a .NET-keretrendszer [ConfigurationManager osztálya](/dotnet/api/system.configuration.configurationmanager).
