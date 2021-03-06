---
title: Kapcsolódás a Azure Media Services V3 API-hoz – Java
description: Ez a cikk azt ismerteti, hogyan csatlakozhat a Javához Azure Media Services V3 API-hoz.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: 6b0f21c3fa7a9c827f7201f4b899a33ea77eaf08
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888495"
---
# <a name="connect-to-media-services-v3-api---java"></a>Kapcsolódás a Media Services V3 API-hoz – Java

Ez a cikk bemutatja, hogyan csatlakozhat a Azure Media Services v3 Java SDK-hoz az egyszerű szolgáltatás bejelentkezési metódusának használatával.

Ebben a cikkben a Visual Studio Code-ot használjuk a minta alkalmazás fejlesztéséhez.

## <a name="prerequisites"></a>Előfeltételek

- A következő lépésekkel telepítheti a [Java-t a Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) használatával:

   - JDK
   - Apache Maven
   - Java kiterjesztési csomag
- Ügyeljen arra, hogy `JAVA_HOME` és `PATH` környezeti változókat állítson be.
- [Hozzon létre egy Media Services fiókot](create-account-cli-how-to.md). Ügyeljen arra, hogy jegyezze fel az erőforráscsoport nevét és a Media Services fiók nevét.
- Kövesse az API-k [elérését](access-api-cli-how-to.md) ismertető témakör lépéseit. Jegyezze fel az előfizetés-azonosítót, az alkalmazás AZONOSÍTÓját (ügyfél-azonosítót), a hitelesítő kulcsot (Secret) és a bérlő AZONOSÍTÓját, amelyre szüksége van egy későbbi lépésben.

Tekintse át a következőket is:

- [Java a Visual Studio Code-ban](https://code.visualstudio.com/docs/languages/java)
- [Java projektmenedzsment a VS Code-ban](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Tekintse át az [elnevezési konvenciókat](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Maven-projekt létrehozása

Nyisson meg egy parancssori eszközt, és `cd` egy olyan könyvtárba, amelyben létre kívánja hozni a projektet.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

A parancs futtatásakor a rendszer létrehozza a `pom.xml`, a `App.java`és az egyéb fájlokat. 

## <a name="add-dependencies"></a>Függőségek hozzáadása

1. A Visual Studio Code-ban nyissa meg azt a mappát, ahol a projekt
1. A `pom.xml` megkeresése és megnyitása
1. A szükséges függőségek hozzáadása

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Kapcsolódás a Java-ügyfélhez

1. Nyissa meg a `App.java` fájlt a `src\main\java\com\azure\ams` területen, és győződjön meg róla, hogy a csomag felül van:

    ```java
    package com.azure.ams;
    ```
1. A Package utasítás alatt adja hozzá a következő importálási utasításokat:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. A kérésekhez szükséges Active Directory hitelesítő adatok létrehozásához adja hozzá a következő kódot az App osztály fő metódusához, és állítsa be a [hozzáférési API](access-api-cli-how-to.md)-k által kapott értékeket:
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Futtassa az alkalmazást.

## <a name="see-also"></a>Lásd még:

- [Media Services fogalmak](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java-referencia](https://aka.ms/ams-v3-java-ref)
- [com. microsoft. Azure. Mediaservices. v2018_07_01: Azure-mgmt-Media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Következő lépések

Mostantól belefoglalhatja `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` és megkezdheti az entitások módosítását.

További példákat a [Java SDK-minták](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) tárházában talál.
