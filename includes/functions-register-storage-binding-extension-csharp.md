---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201947"
---
::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Kötési bővítmények regisztrálása

A HTTP-és időzítő-eseményindítók kivételével a kötések kiterjesztési csomagként vannak implementálva. Futtassa a következő [DotNet-csomag hozzáadása](/dotnet/core/tools/dotnet-add-package) parancsot a terminál ablakban a tárolási bővítmény csomagjának a projekthez való hozzáadásához.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Most hozzáadhatja a tárolási kimeneti kötést a projekthez.  
::: zone-end  