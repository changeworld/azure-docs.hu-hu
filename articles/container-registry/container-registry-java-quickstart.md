---
title: Gyors útmutató – Java-tárolók lemezképének létrehozása és leküldése Azure Container Registry a Maven és a gém használatával
description: Hozzon létre egy tárolóban lévő Java-alkalmazást, és küldje el Azure Container Registry a Maven gém beépülő modullal.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 02/26/2020
ms.openlocfilehash: 62d63b24baab204cb029565b109ea2de768e1d80
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165445"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Gyors útmutató: Java-tárolók rendszerképének létrehozása és leküldése Azure Container Registry

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Java-alkalmazást, és hogyan küldheti el Azure Container Registry a Maven gém beépülő modullal. A Maven és a gém használatának egyik példája a fejlesztői eszközök használata az Azure Container registrytel való interakcióhoz.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés; ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial).
* Az [Azure parancssori felület (CLI)](/cli/azure/overview).
* Támogatott Java fejlesztői készlet (JDK). További információ az Azure-beli fejlesztéshez rendelkezésre álló JDK-król: <https://aka.ms/azure-jdks>.
* Apache [Maven](http://maven.apache.org) Build eszköz (3. vagy újabb verzió).
* Egy [Git](https://git-scm.com)-ügyfél.
* Egy [Docker](https://www.docker.com)-ügyfél.
* Az [ACR Docker hitelesítő adatainak segítője](https://github.com/Azure/acr-docker-credential-helper).

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>A Getting Started Docker-beli Spring Boot-webalkalmazás létrehozása

A következő lépések végigvezetik egy Spring Boot-webalkalmazás összeállításán és helyszíni tesztelésén.

1. A parancssorban a következő parancs használatával klónozott a [Spring boot a docker első lépések](https://github.com/spring-guides/gs-spring-boot-docker) Sample projektben.

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Váltsa a könyvtárat a befejezett projektre.

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. A Mavennel készítse el és futtassa a mintaalkalmazást.

   ```bash
   mvn package spring-boot:run
   ```

1. Tesztelje a webalkalmazást. Ehhez lépjen a `http://localhost:8080` címre, vagy a használja a `curl` parancsot:

   ```bash
   curl http://localhost:8080
   ```

A következő üzenetnek kell megjelennie: **Hello Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Azure Container Registry létrehozása az Azure CLI-vel

Ezután létre kell hoznia egy Azure-erőforráscsoportot és az ACR-t az alábbi lépések végrehajtásával:

1. Jelentkezzen be az Azure-fiókjába a következő parancs használatával:

   ```azurecli
   az login
   ```

1. Válassza ki a használni kívánt Azure-előfizetést:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Hozzon létre egy erőforráscsoportot az oktatóanyagban használt Azure-erőforrások számára. A következő parancsban mindenképp cserélje le a helyőrzőket a saját erőforrás nevére és egy olyan helyre, mint például a `eastus`.

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Hozzon létre egy privát Azure Container registryt az erőforráscsoporthoz az alábbi parancs használatával. Ügyeljen arra, hogy a helyőrzőket a tényleges értékekkel cserélje le. Az oktatóanyag a mintaalkalmazást Docker-lemezképként küldi le ennek a regisztrációs adatbázisnak a későbbi lépésekben.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Az alkalmazás leküldése a regisztrációs adatbázisnak a Jibbel

Végezetül frissítse a projekt konfigurációját, és a parancssor használatával hozza létre és telepítse a lemezképet.

1. Jelentkezzen be a Azure Container Registryba az Azure CLI-vel az alábbi parancs használatával. A helyőrzőt cserélje le a saját beállításjegyzék-nevére.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   A `az configure` parancs beállítja az alapértelmezett beállításjegyzék-nevet `az acr` parancsokkal.

1. Lépjen a Spring Boot-alkalmazás befejezett projektkönyvtárába (például „*C:\SpringBoot\gs-spring-boot-docker\complete*” vagy „ */users/robert/SpringBoot/gs-spring-boot-docker/complete*”), és nyissa meg a *pom.xml* fájlt egy szövegszerkesztővel.

1. Frissítse a `<properties>` gyűjteményt a *Pom. XML* fájlban a következő XML-fájllal. Cserélje le a helyőrzőt a beállításjegyzék nevére, és frissítse a `<jib-maven-plugin.version>` értéket a [gém-Maven-beépülő modul](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin)legújabb verziójával.

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <jib-maven-plugin.version>1.8.0</jib-maven-plugin.version>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Frissítse a `<plugins>` gyűjteményt a *Pom. XML* fájlban, hogy a `<plugin>` elem tartalmazza és a `jib-maven-plugin`bejegyzést a következő példában látható módon. Vegye figyelembe, hogy a Microsoft Container Registry (MCR): `mcr.microsoft.com/java/jdk:8-zulu-alpine`egy alaprendszerképét használjuk, amely az Azure-hoz hivatalosan támogatott JDK-t tartalmaz. A hivatalosan támogatott JDK rendelkező más MCR-alaplemezképek esetében lásd: [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE fej nélküli JRE](https://hub.docker.com/_/microsoft-java-jre-headless)és [Java SE JDK és Maven](https://hub.docker.com/_/microsoft-java-maven).

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Lépjen a Spring Boot-alkalmazás befejezett projektkönyvtárába, és futtassa a következő parancsot a lemezkép elkészítéséhez, majd a könyvtárba való leküldéséhez:

   ```bash
   mvn compile jib:build
   ```

> [!NOTE]
>
> Biztonsági okokból a `az acr login` által létrehozott hitelesítő adatok csak 1 órára érvényesek. Ha *401 jogosulatlan* hibaüzenetet kap, akkor újbóli hitelesítéshez futtathatja a `az acr login -n <your registry name>` parancsot.

## <a name="verify-your-container-image"></a>A tároló rendszerképének ellenőrzése

Gratulálunk! Most már telepítette az ACR-be az Azure által támogatott JDK-t. Most tesztelheti a rendszerképet úgy, hogy üzembe helyezi a Azure App Service, vagy meghúzza a parancsot a helyi parancsra (a helyőrző helyére):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker:v1
```

## <a name="next-steps"></a>Következő lépések

A Microsoft által támogatott Java alaplemezképek egyéb verzióihoz lásd:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE fej nélküli JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK és Maven](https://hub.docker.com/_/microsoft-java-maven)

Ha szeretne többet megtudni a Spring és az Azure szolgáltatásról, lépjen tovább a Spring on Azure dokumentációs központra.

> [!div class="nextstepaction"]
> [A Spring használata az Azure-ban](/azure/java/spring-framework)

### <a name="additional-resources"></a>További források

További információkért lásd a következőket:

* [Azure Java-fejlesztőknek](/azure/java)
* [Az Azure DevOps és a Java használata](/azure/devops/java)
* [A Spring Boot a Dockerben – első lépések](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Spring Boot-alkalmazás üzembe helyezése az Azure App Service-ben](/azure/java/spring-framework/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin)
* [Egyéni Docker-lemezkép használata az Azure Web App on Linux szolgáltatáshoz](/azure/app-service-web/app-service-linux-using-custom-docker-image)
