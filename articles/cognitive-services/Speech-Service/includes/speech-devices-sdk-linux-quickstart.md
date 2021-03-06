---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: dapine
ms.openlocfilehash: 81c77b2f6ae0c4f8497716c168a937657ceb57dd
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383856"
---
Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Linux Speech Devices SDK-t egy beszédfelismerésre alkalmas termék létrehozásához vagy [beszélgetéses átírási](../conversation-transcription-service.md) eszközként való használatához. Jelenleg csak az [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) támogatott.

Az alkalmazás a Speech SDK csomaggal, valamint az Eclipse Java IDE (v4) a 64 bites Linux rendszeren (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8) készült. és 64 bites Java 8 futtatókörnyezetben (JRE) fut.

Ehhez az útmutatóhoz egy Speech Service-erőforrással rendelkező [Azure Cognitive Services](../get-started.md) -fiókra van szükség. Ha nincs fiókja, használhatja az ingyenes [próbaidőszakot](https://azure.microsoft.com/try/cognitive-services/) egy előfizetői azonosító beszerzéséhez.

A [minta alkalmazás](https://aka.ms/sdsdk-download-JRE) forráskódját a Speech Devices SDK tartalmazza. [A githubon is elérhető](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Operációs rendszer: 64-bites Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* Csak [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) vagy [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) esetén.
* Egy Azure-előfizetési kulcs a beszédfelismerési szolgáltatáshoz. [Szerezze be az egyiket ingyenesen](../get-started.md).
* Töltse le a Javához készült [Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) legújabb verzióját, és bontsa ki a. zip fájlt a munkakönyvtárba.
   > [!NOTE]
   > Ez a rövid útmutató azt feltételezi, hogy az alkalmazást a rendszer kinyeri a/home/wcaltest/JRE-Sample-Release

Az Eclipse elindítása előtt győződjön meg róla, hogy ezek a függőségek telepítve vannak.

* On Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* Debian 9 rendszeren:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

RHEL/CentOS 8 rendszeren:

  ```sh
  sudo yum update
  sudo yum install alsa-lib openssl
  ```

> [!NOTE]
> A RHEL/CentOS 8 rendszeren kövesse az [OpenSSL Linux rendszerhez való konfigurálásának](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)utasításait.

A beszélgetés átírása jelenleg csak az "en-US" és a "zh-CN" esetében érhető el az "CentralUS" és a "eastasia" régióban. A beszélgetés átírásának használatához az egyik régióban kell lennie egy beszédfelismerési kulcsnak.

Ha azt tervezi, hogy használja a leképezéseket, szüksége lesz egy [Language Understanding Service (Luis)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) előfizetésre. Ha többet szeretne megtudni a LUIS és a szándék felismeréséről, olvassa el a [beszédfelismerési szándékok C#FELISMERÉSe a Luis ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)használatával című témakört. Ehhez az alkalmazáshoz egy [minta Luis-modell](https://aka.ms/sdsdk-luis) érhető el.

## <a name="create-and-configure-the-project"></a>A projekt létrehozása és konfigurálása

1. Indítsa el az Eclipse-et.

1. Az **Eclipse ide-indító** **munkaterület** mezőjébe írja be az új munkaterület-könyvtár nevét. Ezután válassza a **Launch** (Indítás) lehetőséget.

   ![Az Eclipse Launcher képernyőképe](../media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Az Eclipse IDE főablaka hamarosan megjelenik. Ha megnyílt, zárja be az üdvözlőképernyőt.

1. Az Eclipse menüsávban hozzon létre egy új projektet a **fájl** > **új** > **Java-projekt**lehetőség kiválasztásával. Ha nem érhető el, válassza a **projekt** , majd a **Java-projekt**lehetőséget.

1. Elindul az **új Java-projekt** varázsló. **Tallózással keresse** meg a minta projekt helyét. Válassza a **Finish** (Befejezés) elemet.

   ![A New Java Project varázsló képernyőképe](../media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. A **Package Explorerben**kattintson a jobb gombbal a projektre. A helyi menüben kattintson a **Configure** > **Convert to Maven Project** (Konfigurálás, Konvertálás Maven-projektté) parancsra. Válassza a **Finish** (Befejezés) elemet.

   ![A Package Explorer képernyőképe](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Nyissa meg a Pom. xml fájlt, és szerkessze.

    A fájl végén, a záró címke `</project>`előtt hozzon létre `repositories` és `dependencies` elemeket az itt látható módon, és győződjön meg arról, hogy a `version` megfelel a jelenlegi verziójának:
    ```xml    
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.10.0</version>
        </dependency>
    </dependencies>
   ```

1. A **Package Explorerben**kattintson a jobb gombbal a projektre. Válassza a **Tulajdonságok**, majd a **Futtatás/hibakeresés beállítások** > **új... lehetőséget.** > **Java-alkalmazást**. 

1. Megjelenik a **konfiguráció szerkesztése** ablak. A **név** mezőbe írja be a **Main**értéket, és a **fő osztály** **keresése** elemre kattintva keresse meg és válassza ki a következőt: **com. microsoft. cognitiveservices. Speech. Samples. FunctionsList**.

   ![Képernyőkép – indítási konfiguráció szerkesztése](../media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. A cél architektúrához tartozó hangbináris fájlok másolása **Linux-ARM** vagy **Linux-x64 rendszerről**a Java-projekt helyére, például **/Home/wcaltest/JRE-Sample-Release**

1. Emellett a **konfiguráció szerkesztése** ablakban válassza ki a **környezet** lapot és az **új**elemet. Megjelenik az **új környezeti változó** ablak. A **név** mezőbe írja be a **LD_LIBRARY_PATH** értéket, és az **érték** mezőbe írja be a *. so fájlokat tartalmazó mappát (például **/Home/wcaltest/JRE-Sample-Release** ).

1. `kws.table` és `participants.properties` másolása a Project mappa **céljába/osztályaiba**


## <a name="configure-the-sample-application"></a>A mintaalkalmazás konfigurálása

1. Adja hozzá a beszédfelismerési előfizetéshez tartozó kulcsot a forráskódhoz. Ha szeretné kipróbálni a szándék felismerését, adja hozzá a [Language Understanding szolgáltatás](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) előfizetési kulcsát és az alkalmazás azonosítóját is.

   A Speech és a LUIS esetében az adatai bekerülnek a `FunctionsList.java`ba:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Ha a beszélgetés átírását használja, a beszédfelismerési kulcsra és a régióra vonatkozó információkra is szükség van `Cts.java`ban:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Az alapértelmezett kulcsszó (kulcsszó) a "Computer". Kipróbálhatja a többi megadott kulcsszót is, például a "Machine" vagy a "Assistant" kifejezést. Ezen alternatív kulcsszavak erőforrásai a Speech Devices SDK-ban, a Kulcsszóválasztó mappában találhatók. A `/home/wcaltest/JRE-Sample-Release/keyword/Computer` például a "számítógép" kulcsszóhoz használt fájlokat tartalmazza.

   > [!TIP]
   > [Egyéni kulcsszó is létrehozható](../speech-devices-sdk-create-kws.md).

    Új kulcsszó használatához frissítse a következő sort a `FunctionsList.java`ban, és másolja a kulcsszót az alkalmazásba. Ha például a "Machine" kulcsszót szeretné használni a Kulcsszóválasztó csomag `machine.zip`:

   * Másolja a zip-csomagból a `kws.table` fájlt a Project Folder **cél/osztályok**mappájába.

   * A `FunctionsList.java` frissítése a kulcsszó nevével:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>A minta alkalmazás futtatása az Eclipse-ből

1. Az Eclipse menüsávban **futtassa** > **Run** parancsot. 

1. A Speech Devices SDK-val példa alkalmazás elindul, és megjeleníti a következő beállításokat:

   ![Beszéd Devices SDK-val példa mintaalkalmazás és beállítások](../media/speech-devices-sdk/java-sample-app-linux.png)

1. Próbálja ki az új **beszélgetés átiratának** bemutatóját. A **munkamenet** - > **indításának**megkezdése. Alapértelmezés szerint mindenki a vendég. Ha azonban a résztvevő hangaláírásai vannak, akkor azokat `participants.properties`ba helyezheti a projekt mappájában **célként vagy osztályokban**. A hangaláírás létrehozásához tekintse meg a beszélgetések átírása [(SDK) című témakört](../how-to-use-conversation-transcription-service.md).

   ![Bemutató beszélgetés átirata alkalmazás](../media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Önálló alkalmazás létrehozása és futtatása

1. A **Package Explorerben**kattintson a jobb gombbal a projektre. Válassza az **Exportálás**lehetőséget. 
1. Megjelenik az **Exportálás** ablak. Bontsa ki a **javát** , és válassza a **futtatható jar-fájl** lehetőséget, majd kattintson a **tovább**gombra.

   ![Az exportálási ablak képernyőképe](../media/speech-devices-sdk/eclipse-export-linux.png) 

1. Megjelenik a **FUTTATHATÓ jar-fájl exportálása** ablak. Válassza ki az alkalmazás **exportálási célját** , majd kattintson a **Befejezés gombra**.
 
   ![Képernyőfelvétel a futtatható JAR-fájl exportálásáról](../media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Helyezze el `kws.table` és `participants.properties` a fenti célmappában, mivel az alkalmazásnak szüksége van ezekre a fájlokra.

1. Állítsa a LD_LIBRARY_LIBt a *. so fájlokat tartalmazó mappára.

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Az önálló alkalmazás futtatása

     ```bash
     java -jar SpeechDemo.jar
     ```
