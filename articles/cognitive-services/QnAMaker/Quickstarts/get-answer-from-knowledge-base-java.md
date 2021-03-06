---
title: 'Gyors útmutató: Válasz kérése a Tudásbázisból – REST, Java – QnA Maker'
description: Ez a Java REST-alapú rövid útmutató végigvezeti egy adott Tudásbázisból származó válasz beszerzésének lépésein.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 67f09b6d1e284cdf35825a2e584b372bd2adf70a
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851739"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Gyors útmutató: válaszok egy tudásbázisbeli kérdésre Java használatával

Ez a rövid útmutató végigvezeti a közzétett QnA Maker Tudásbázisból származó válasz programozott módon történő beszerzésének lépésein. A Tudásbázis az [adatforrásokból](../Concepts/knowledge-base.md) , például a GYIK-ből származó kérdéseket és válaszokat tartalmaz. A rendszer elküldi a [kérdést](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) a QnA Maker szolgáltatásnak. A [Válasz](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) tartalmazza a legfontosabb előre jelzett választ.

[Hivatkozási dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [minta](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/get-answer/GetAnswer.java)

## <a name="prerequisites"></a>Előfeltételek

* [JDK SE](https://aka.ms/azure-jdks) (Java fejlesztői készlet, Standard Edition)
* Ez a példa az Apache [http-ügyfelet](https://hc.apache.org/httpcomponents-client-ga/) használja a http-összetevőkből. Az alábbi Apache HTTP-ügyfélkönyvtárak hozzáadása a projekthez kell:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * Commons-naplózás – 1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Rendelkeznie kell [QnA Maker-szolgáltatással](../How-To/set-up-qnamaker-service-azure.md) is. A kulcs lekéréséhez válassza a **kulcsok** elemet az **Erőforrás-kezelés** területen az QnA Maker erőforráshoz tartozó Azure-irányítópulton.
* **Közzétételi** oldal beállításai Ha nem rendelkezik közzétett tudásbázissal, hozzon létre egy üres tudásbázist, majd importáljon egy tudásbázist a **Beállítások** lapon, majd tegye közzé. [Ezt az alapszintű tudásbázist](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)töltheti le és használhatja.

    A közzétételi oldal beállításai közé tartozik az útvonal értéke, a gazdagép értéke és a EndpointKey érték.

    ![Közzétételi beállítások](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-java-file"></a>Java-fájl létrehozása

Nyissa meg a VSCode, és hozzon létre egy `GetAnswer.java` nevű új fájlt, és adja hozzá a következő osztályt:

```Java
public class GetAnswer {

    public static void main(String[] args)
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

Ez a rövid útmutató a HTTP-kérelmek Apache-osztályait használja. A GetAnswer osztály felett, a `GetAnswer.java` fájl felső részén adja hozzá a szükséges függőségeket a projekthez:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>A szükséges konstansok hozzáadása

A `GetAnswer.java` osztály tetején adja hozzá a szükséges állandókat a QnA Maker eléréséhez. Ezek az értékek a **közzétételi** lapon jelennek meg, miután közzétette a tudásbázist.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>Kérdés küldésére szolgáló POST-kérelem hozzáadása

A következő kód egy HTTPS-kérést küld a QnA Maker APInak, hogy elküldje a kérdést a Tudásbázisnak, és fogadja a választ:

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

A `Authorization` fejléc értéke tartalmazza a karakterláncot `EndpointKey`.

További információ a [kérelemről](../how-to/metadata-generateanswer-usage.md#generateanswer-request) és a [válaszról](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>A program létrehozása és futtatása

Hozhat létre, és a program futtatása a parancssorból. A kérelem automatikusan elküldi a QnA Maker API, majd a konzolablakban nyomtatási.

1. A fájl létrehozása:

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Futtassa a fájlt:

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)