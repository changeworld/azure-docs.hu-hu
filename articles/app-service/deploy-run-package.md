---
title: Alkalmazás futtatása ZIP-csomagból
description: Az alkalmazás ZIP-csomagjának üzembe helyezése az atomenergia-szolgáltatással. Javíthatja az alkalmazás működésének kiszámíthatóságát és megbízhatóságát a ZIP-telepítési folyamat során.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 316ada7700a5cf45ee90f515336039702bab48c0
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920722"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Az alkalmazás futtatása Azure App Service közvetlenül egy ZIP-csomagból

[Azure app Service](overview.md)az alkalmazásokat közvetlenül egy üzembe HELYEZÉSi zip-csomagfájl használatával futtathatja. Ez a cikk bemutatja, hogyan engedélyezheti ezt a funkciót az alkalmazásban.

A App Service összes többi üzembe helyezési módszere közös: a fájlok üzembe helyezése az alkalmazás *D:\home\site\wwwroot* (vagy Linux-alkalmazások */Home/site/wwwroot* ) történik. Mivel az alkalmazás futása közben ugyanazt a könyvtárat használja, lehetséges, hogy az üzembe helyezés sikertelen a zárolási ütközések miatt, és az alkalmazás nem kiszámíthatatlanul viselkedik, mert néhány fájl még nincs frissítve.

Ezzel szemben, ha közvetlenül egy csomagból futtatja, a csomagban lévő fájlok nem másolódnak át a *wwwroot* könyvtárba. Ehelyett maga a ZIP-csomag közvetlenül a csak olvasható *wwwroot* -címtárral lesz csatlakoztatva. A csomagok közvetlen futtatásának számos előnye van:

- Kiküszöböli a zárolási ütközéseket az üzembe helyezés és a futtatókörnyezet között.
- Gondoskodik arról, hogy csak a teljes körűen telepített alkalmazások futnak.
- Üzembe helyezhető egy éles alkalmazásban (újraindítással).
- Javítja Azure Resource Manager üzemelő példányok teljesítményét.
- Csökkentheti a hideg kezdési időpontokat, különösen a JavaScript-függvények esetében nagyméretű NPM-csomagokkal.

> [!NOTE]
> Jelenleg csak a ZIP-csomagok fájljai támogatottak.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Futtatás engedélyezése a csomagból

A `WEBSITE_RUN_FROM_PACKAGE` Alkalmazásbeállítások lehetővé teszik a futtatást a csomagból. A beállításhoz futtassa a következő parancsot az Azure CLI-vel.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` lehetővé teszi az alkalmazás futtatását egy helyi csomagból az alkalmazásba. [Távoli csomagból is futtatható](#run-from-external-url-instead).

## <a name="run-the-package"></a>A csomag futtatása

A App Service-csomag futtatásának legegyszerűbb módja az Azure CLI az [WebApp Deployment Source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) parancs. Például:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Mivel a `WEBSITE_RUN_FROM_PACKAGE` alkalmazás beállítása be van állítva, ez a parancs nem bontja ki a csomag tartalmát az alkalmazás *D:\home\site\wwwroot* könyvtárába. Ehelyett feltölti a ZIP-fájlt a *D:\home\data\SitePackages*, és létrehoz egy *PackageName. txt* fájlt ugyanabban a címtárban, amely tartalmazza a FUTTATÁSkor betölteni kívánt zip-csomag nevét. Ha a ZIP-csomagot más módon (például [FTP](deploy-ftp.md)) tölti fel, akkor manuálisan kell létrehoznia a *D:\home\data\SitePackages* könyvtárat és a *PackageName. txt* fájlt.

A parancs az alkalmazást is újraindítja. Mivel `WEBSITE_RUN_FROM_PACKAGE` be van állítva, App Service a feltöltött csomagot írásvédett *wwwroot* -címtárként csatlakoztatja, és közvetlenül az adott csatlakoztatott könyvtárból futtatja az alkalmazást.

## <a name="run-from-external-url-instead"></a>Futtatás külső URL-címről

A csomagokat külső URL-címről, például az Azure Blob Storage is futtathatja. A [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) használatával tölthet fel csomagokat a blob Storage-fiókjába. Egy [megosztott hozzáférési aláírással (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) rendelkező privát tárolót kell használnia, amely lehetővé teszi, hogy a app Service futtatókörnyezet biztonságosan hozzáférjen a csomaghoz. 

Miután feltöltötte a fájlt a blob Storage-ba, és rendelkezik egy SAS URL-címmel a fájlhoz, állítsa be a `WEBSITE_RUN_FROM_PACKAGE` alkalmazás beállítását az URL-címre. Az alábbi példa az Azure CLI-t használja:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Ha a blob Storage-hoz azonos nevű frissített csomagot tesz közzé, újra kell indítania az alkalmazást, hogy a frissített csomag betöltődik a App Serviceba.

### <a name="use-key-vault-references"></a>Key Vault referenciák használata

A további biztonság érdekében Key Vault hivatkozásokat is használhat a külső URL-címmel együtt. Ez megtartja az URL-címek titkosítását, és lehetővé teszi, hogy kihasználja Key Vault a titkos felügyelet és a forgás számára. Az Azure Blob Storage használata ajánlott, így egyszerűen elforgathatja a társított SAS-kulcsot. Az Azure Blob Storage inaktív állapotban van, így az alkalmazás adatai biztonságban maradnak, amikor nincs telepítve App Service.

1. Hozzon létre egy Azure Key Vault.

    ```azurecli
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus
    ```

1. Adja hozzá a külső URL-címet Key Vault-titokként.

    ```azurecli
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<insert-your-URL>"
    ```

1. Hozza létre a `WEBSITE_RUN_FROM_PACKAGE` alkalmazás beállítását, és állítsa be az értéket Key Vault hivatkozásként a külső URL-címre.

    ```azurecli
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"
    ```

További információt a következő cikkekben talál.

- [App Service Key Vault referenciái](app-service-key-vault-references.md)
- [Azure Storage-titkosítás a REST-adatokhoz](../storage/common/storage-service-encryption.md)

## <a name="troubleshooting"></a>Hibakeresés

- A közvetlenül a csomagból való futtatás `wwwroot` írásvédett. Az alkalmazás hibaüzenetet küld, ha fájlokat próbál írni ebbe a könyvtárba.
- A TAR és a GZIP formátum nem támogatott.
- Ez a funkció nem kompatibilis a [helyi gyorsítótárral](overview-local-cache.md).
- A jobb hidegindító teljesítmény érdekében használja a helyi zip-beállítást (`WEBSITE_RUN_FROM_PACKAGE`= 1).

## <a name="more-resources"></a>További segédanyagok

- [Azure App Service folyamatos üzembe helyezése](deploy-continuous-deployment.md)
- [Kód üzembe helyezése ZIP-vagy WAR-fájllal](deploy-zip.md)
