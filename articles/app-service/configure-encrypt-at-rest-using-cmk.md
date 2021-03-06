---
title: Az alkalmazás forrásának titkosítása nyugalmi állapotban
description: Az alkalmazásadatok titkosítása az Azure Storage-ban, és csomag fájlként történő üzembe helyezése.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: b1cfd99e2cc7c87675c72f679139a4299486cca6
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79374900"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Inaktív adatok titkosítása az ügyfél által felügyelt kulcsok használatával

A webalkalmazásban tárolt alkalmazásadatok titkosításához Azure Storage-fiókra és egy Azure Key Vaultra van szükség. Ezeket a szolgáltatásokat akkor használja a rendszer, amikor az alkalmazást egy központi telepítési csomagból futtatja.

  - [Az Azure Storage lehetővé teszi a titkosítást a nyugalmi](../storage/common/storage-service-encryption.md)állapotban. Használhatja a rendszerszintű kulcsokat vagy a saját, az ügyfél által felügyelt kulcsokat. Itt tárolja az alkalmazás adatait, ha nem fut az Azure-ban egy webalkalmazásban.
  - A [központi telepítési csomagból való futtatás](deploy-run-package.md) a app Service központi telepítési funkciója. Lehetővé teszi a webhely tartalmának Azure Storage-fiókból való üzembe helyezését egy közös hozzáférési aláírás (SAS) URL-cím használatával.
  - [Key Vault referenciák](app-service-key-vault-references.md) app Service biztonsági funkciója. Lehetővé teszi, hogy az alkalmazás beállításainak megfelelően importálja a titkokat futásidőben. Ezzel titkosíthatja az Azure Storage-fiók SAS URL-címét.

## <a name="set-up-encryption-at-rest"></a>Titkosítás beállítása nyugalmi állapotban

### <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Először [hozzon létre egy Azure Storage-fiókot](../storage/common/storage-account-create.md) , és [titkosítsa az ügyfél által felügyelt kulcsokkal](../storage/common/storage-service-encryption.md#customer-managed-keys-with-azure-key-vault). A Storage-fiók létrehozása után a [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) használatával töltse fel a csomagok fájljait.

Ezután a Storage Explorer használatával [állítson be sas](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer)-t. 

> [!NOTE]
> Mentse ezt az SAS URL-címet, amelyet később a központi telepítési csomag biztonságos elérésének engedélyezéséhez használhat futásidőben.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>A Futtatás beállítása csomagból a Storage-fiókból
  
Miután feltöltötte a fájlt a blob Storage-ba, és SAS URL-címmel rendelkezik a fájlhoz, állítsa a `WEBSITE_RUN_FROM_PACKAGE` alkalmazás beállítást a SAS URL-címre. Az alábbi példa az Azure CLI-t használja:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Az Alkalmazásbeállítás hozzáadása a webalkalmazás újraindítását eredményezi. Az alkalmazás újraindítása után tallózással keresse meg az alkalmazást, és ellenőrizze, hogy az alkalmazás megfelelően elindult-e a központi telepítési csomag használatával. Ha az alkalmazás nem indult el megfelelően, tekintse meg a [Futtatás a csomagban hibaelhárítási útmutatót](deploy-run-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Alkalmazás-beállítás titkosítása Key Vault hivatkozásokkal

Most lecserélheti a `WEBSITE_RUN_FROM_PACKAGE` Alkalmazásbeállítások értékét egy Key Vault hivatkozással az SAS-kódolt URL-címre. Így az SAS URL-címe titkosítva van Key Vaultban, ami egy extra biztonsági réteget biztosít.

1. Key Vault példány létrehozásához használja az alábbi [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) parancsot.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Kövesse [ezeket az utasításokat, hogy hozzáférést biztosítson az alkalmazáshoz](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) a Key vaulthoz:

1. A következő [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) parancs használatával adja hozzá a külső URL-címet a kulcstartó titkos kódjához:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  A következő [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) parancs használatával hozza létre a `WEBSITE_RUN_FROM_PACKAGE` Application beállítást az értékkel Key Vault hivatkozásként a külső URL-címre:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    A `<secret-version>` az előző `az keyvault secret set` parancs kimenetében jelenik meg.

Az Alkalmazásbeállítás frissítése azt eredményezi, hogy a webalkalmazás újraindul. Az alkalmazás újraindítása után keresse meg a következőt: Ellenőrizze, hogy megfelelően elindult-e a Key Vault hivatkozás használatával.

## <a name="how-to-rotate-the-access-token"></a>A hozzáférési jogkivonat elforgatása

Az ajánlott eljárás a Storage-fiók SAS-kulcsának rendszeres elforgatása. Annak biztosítása érdekében, hogy a webalkalmazás véletlenül ne legyen laza hozzáférés, az SAS URL-címét is frissítenie kell Key Vaultban.

1. Forgassa el az SAS-kulcsot úgy, hogy a Azure Portalban navigál a Storage-fiókjához. A **beállítások** > **hozzáférési kulcsok**területen kattintson az ikonra az SAS-kulcs elforgatásához.

1. Másolja az új SAS URL-címet, és a következő parancs használatával állítsa be a frissített SAS URL-címet a kulcstartóban:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Frissítse a Key Vault-hivatkozást az alkalmazás beállításaiban az új titkos verzióra:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    A `<secret-version>` az előző `az keyvault secret set` parancs kimenetében jelenik meg.

## <a name="how-to-revoke-the-web-apps-data-access"></a>A webalkalmazás adatelérésének visszavonása

A webalkalmazáshoz való hozzáférés két módszerrel vonható vissza a Storage-fiókhoz. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Az Azure Storage-fiók SAS-kulcsának elforgatása

Ha a rendszer elforgatja a Storage-fiók SAS-kulcsát, a webalkalmazás már nem fér hozzá a Storage-fiókhoz, de továbbra is a csomagfájl utolsó letöltött verziójával fog futni. A legutóbbi letöltött verzió törléséhez indítsa újra a webalkalmazást.

### <a name="remove-the-web-apps-access-to-key-vault"></a>A webalkalmazás hozzáférésének eltávolítása Key Vault

A webalkalmazás hozzáférését visszavonhatja a hely adateléréséhez, ha letiltja a webalkalmazás hozzáférését a Key Vaulthoz. Ehhez távolítsa el a webalkalmazás identitásához tartozó hozzáférési szabályzatot. Ez ugyanaz az identitás, amelyet korábban hozott létre a Key Vault-referenciák konfigurálása során.

## <a name="summary"></a>Összegzés

Az alkalmazás fájljai mostantól titkosítva vannak a Storage-fiókban. A webalkalmazás indításakor lekéri a SAS URL-címét a kulcstartóból. Végül a webalkalmazás betölti az alkalmazás fájljait a Storage-fiókból. 

Ha vissza kell vonnia a webalkalmazás hozzáférését a Storage-fiókjához, visszavonhatja a Key vaulthoz való hozzáférést, vagy elforgathatja a Storage-fiók kulcsait, ami érvényteleníti a SAS URL-címét.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>Díjköteles a webalkalmazás a központi telepítési csomagból való futtatása?

Csak az Azure Storage-fiókhoz társított költségek és a kimenő forgalomra vonatkozó költségek.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>Hogyan befolyásolja a rendszer a webes alkalmazást a központi telepítési csomagból?

- Ha az alkalmazást a központi telepítési csomagból futtatja, a `wwwroot/` csak olvasható lesz. Az alkalmazás hibaüzenetet kap, amikor megkísérli a címtárba írni.
- A TAR és a GZIP formátum nem támogatott.
- Ez a funkció nem kompatibilis a helyi gyorsítótárral.

## <a name="next-steps"></a>Következő lépések

- [App Service Key Vault referenciái](app-service-key-vault-references.md)
- [Azure Storage-titkosítás a REST-adatokhoz](../storage/common/storage-service-encryption.md)
