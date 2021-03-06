---
title: 'Gyors útmutató: Tudásbázis közzététele, REST, Java – QnA Maker'
description: Ez a Java REST-alapú rövid útmutató közzéteszi a tudásbázist, és létrehoz egy végpontot, amely hívható az alkalmazásban vagy a csevegési robotban.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 149d7963f29bf041cda75fffaac533e0a62ee7a6
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851684"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Rövid útmutató: Tudásbázis közzététele a QnA Makerben a Java használatával

A REST-alapú rövid útmutató végigvezeti programozott módon közzététele (KB). A Publishing leküldi a Tudásbázis legújabb verzióját egy dedikált Azure Cognitive Search indexre, és létrehoz egy végpontot, amely meghívható az alkalmazásban vagy a csevegési robotban.

Ebben a rövid útmutatóban QnA Maker API-kat hívunk meg:
* [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – ehhez az API-hoz nem kell megadni információkat a kérés törzsében.

## <a name="prerequisites"></a>Előfeltételek

* [JDK SE](https://aka.ms/azure-jdks) (Java fejlesztői készlet, Standard Edition)
* Ez a példa az Apache [http-ügyfelet](https://hc.apache.org/httpcomponents-client-ga/) használja a http-összetevőkből. Az alábbi Apache HTTP-ügyfélkönyvtárak hozzáadása a projekthez kell:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * Commons-naplózás – 1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. Ha le szeretné kérni a kulcsot és a végpontot (amely tartalmazza az erőforrás nevét), válassza az erőforráshoz tartozó **Gyorsindítás** lehetőséget a Azure Portal.
* QnA Maker Tudásbázis-azonosító a `kbid` lekérdezési karakterlánc paraméterben az alább látható módon található.

    ![QnA Maker tudásbázis-azonosító](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Ha még nem rendelkezik tudásbázissal, létrehozhat egy minta tudásbázist ehhez a rövid útmutatóhoz: [Új tudásbázis létrehozása](create-new-kb-csharp.md).

> [!NOTE]
> A teljes megoldás fájl (ok) az [ **Azure-Samples/kognitív-Services-qnamaker-Java** GitHub-tárházból](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base)érhetők el.

## <a name="create-a-java-file"></a>Hozzon létre egy Java-fájlt

Nyissa meg a VSCode, és hozzon létre egy `PublishKB.java`nevű új fájlt.

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

`PublishKB.java`tetején a osztály felett adja hozzá a következő sorokat a szükséges függőségek a projekthez való hozzáadásához:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Metoda main PublishKB osztály létrehozása

A függőségek után adja hozzá a következő osztályok:

```Go
public class PublishKB {

    public static void main(String[] args)
    {
    }
}
```

## <a name="add-required-constants"></a>Szükséges konstansok hozzáadása

A **Main** metódusban adja hozzá a szükséges állandókat a QnA Maker eléréséhez. Cserélje le az értékeket a saját.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Adja hozzá a POST-kérés Tudásbázis közzététele

A szükséges állandókat után adja hozzá a következő kódra, amely egy HTTPS-kérést küld a QnA Maker API Tudásbázis közzététele, és megkapja a választ:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

Az API-hívás egy 204-es állapotot küld vissza a sikeres közzététel nyugtázására. A válasz törzsében nem található tartalom. A kód adja hozzá a 204-es válaszok tartalmát.

Bármely egyéb válasz esetében a rendszer a választ változtatás nélkül adja vissza.

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Hozhat létre, és a program futtatása a parancssorból. A kérelem automatikusan elküldi a QnA Maker API, majd a konzolablakban nyomtatási.

1. A fájl létrehozása:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Futtassa a fájlt:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

Miután közzétette a tudásbázist, szüksége lesz a [végpont URL-címére a válasz létrehozásához](./get-answer-from-knowledge-base-java.md).

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)