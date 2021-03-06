---
title: Útmutató az Azure-alkalmazások konfigurációjának használatáról
description: Útmutató az Azure-alkalmazások konfigurálásához a Java Spring Apps használatával.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: 7b89696e60189a8ab2585f8511be32ddaa89e826
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79216780"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Rövid útmutató: Java Spring-alkalmazás létrehozása az Azure app Configuration szolgáltatással

Ebben a rövid útmutatóban beépíti az Azure-alkalmazások konfigurációját egy Java Spring-alkalmazásba, hogy központilag központosítsa az alkalmazás-beállítások tárolási és kezelési beállításait a kódból.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- A 8-as verzióval támogatott [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) .
- Az [Apache Maven](https://maven.apache.org/download.cgi) 3,0-es vagy újabb verziója.

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

1. Válassza a **Configuration Explorer** >  **+ Létrehozás** lehetőséget a következő kulcs-érték párok hozzáadásához:

    | Paraméter | Érték |
    |---|---|
    | /application/config.message | Üdvözöljük! |

    Most hagyja üresen a **címke** és a **tartalom típusát** .

## <a name="create-a-spring-boot-app"></a>Spring boot-alkalmazás létrehozása

A [Spring inicializáló](https://start.spring.io/) használatával hozzon létre egy új Spring boot-projektet.

1. Nyissa meg a következő címet: <https://start.spring.io/>.

1. Adja meg a következő beállításokat:

   - Hozzon létre egy **Maven** projektet **Javával**.
   - Olyan **Spring boot** -verziót válasszon, amely egyenlő vagy nagyobb, mint 2,0.
   - Adja meg az alkalmazáshoz tartozó **Group** (Csoport) és **Artifact** (Összetevő) neveket.
   - Adja hozzá a **rugó webes** függőségét.

1. Az előző beállítások megadása után válassza a **projekt létrehozása**lehetőséget. Ha a rendszer kéri, töltse le a projektet a helyi számítógépre.

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Miután kicsomagolta a fájlokat a helyi rendszeren, az egyszerű Spring boot-alkalmazás készen áll a szerkesztésre. Keresse meg a *Pom. XML* fájlt az alkalmazás gyökérkönyvtárában.

1. Nyissa meg a *Pom. XML* fájlt egy szövegszerkesztőben, és adja hozzá a Spring Cloud Azure config Starter-t a `<dependencies>`listájához:

    **Spring Cloud 1.1. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.2</version>
    </dependency>
    ```

    **Spring Cloud 1.2. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.2</version>
    </dependency>
    ```

1. Hozzon létre egy új, *MessageProperties. Java* nevű Java-fájlt az alkalmazás csomag könyvtárába. Adja hozzá a következő sorokat:

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. Hozzon létre egy új, *HelloController. Java* nevű Java-fájlt az alkalmazás csomag könyvtárába. Adja hozzá a következő sorokat:

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

1. Nyissa meg az alkalmazás fő Java-fájlját, és adja hozzá a `@EnableConfigurationProperties` a funkció engedélyezéséhez.

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Hozzon létre egy `bootstrap.properties` nevű új fájlt az alkalmazás erőforrások könyvtára alatt, és adja hozzá a következő sorokat a fájlhoz. Cserélje le a mintavételi értékeket az alkalmazás konfigurációs tárolójának megfelelő tulajdonságaira.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].name= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Állítson be egy **APP_CONFIGURATION_CONNECTION_STRING**nevű környezeti változót, és állítsa be az alkalmazás konfigurációs tárolójának hozzáférési kulcsára. A parancssorban futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

    ```CLI
        setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

    ```azurepowershell
        $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Ha macOS vagy Linux rendszert használ, futtassa a következő parancsot:

    ```console
        export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Készítse elő a Spring boot-alkalmazást a Maven használatával, és futtassa, például:

    ```CLI
    mvn clean package
    mvn spring-boot:run
    ```

2. Az alkalmazás futása után a *curl* használatával tesztelheti az alkalmazást, például:

      ```CLI
      curl -X GET http://localhost:8080/
      ```

    Megjelenik az alkalmazás konfigurációs tárolójában megadott üzenet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy új alkalmazás-konfigurációs tárolót, amelyet egy Java Spring-alkalmazással használt. További információ: [Spring on Azure](https://docs.microsoft.com/java/azure/spring-framework/). Ha szeretné megtudni, hogyan engedélyezheti a Java Spring-alkalmazást a konfigurációs beállítások dinamikus frissítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dinamikus konfiguráció engedélyezése](./enable-dynamic-configuration-java-spring-app.md)
