---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320487"
---
Ha HTTP-proxyt kell konfigurálnia a kimenő kérelmek végrehajtásához, használja a következő két argumentumot:

| Name (Név) | Adattípus | Leírás |
|--|--|--|
|HTTP_PROXY|sztring|A használandó proxy, például:`http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|Karakterlánc|A proxyn való hitelesítéshez szükséges hitelesítő adatok, például Felhasználónév: jelszó.|
|`<proxy-user>`|sztring|A proxy felhasználója.|
|`<proxy-password>`|sztring|A proxyhoz tartozó `<proxy-user>` jelszó.|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
