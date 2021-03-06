---
title: REST-alapú titkosítás az ügyfél által felügyelt kulcsokkal
titleSuffix: Azure Cognitive Search
description: Az indexeken és az Azure-ban található szinonimák használatával kiegészítheti a kiszolgálóoldali titkosítást, Cognitive Search a Azure Key Vaultban létrehozott és felügyelt kulcsokkal.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: cb17fe24339ad618229b3456ece15c206f79bdb7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899947"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Az Azure Cognitive Search-ban található, az ügyfél által felügyelt kulcsokat használó tartalom titkosítása Azure Key Vault

Alapértelmezés szerint az Azure Cognitive Search a [szolgáltatás által felügyelt kulcsokkal](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models)titkosítja az indexelt tartalmakat. Az alapértelmezett titkosítás további titkosítási réteggel kiegészíthető a Azure Key Vaultban létrehozott és felügyelt kulcsok használatával. Ez a cikk végigvezeti a lépéseken.

A kiszolgálóoldali titkosítást a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)integrációja támogatja. Létrehozhatja saját titkosítási kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-jait a titkosítási kulcsok létrehozásához. A Azure Key Vault használatával is naplózhatja a kulcshasználat. 

Az ügyfél által felügyelt kulcsokkal történő titkosítás az index vagy a szinonimák leképezési szintjén van konfigurálva, ha ezek az objektumok létre lettek hozva, és nem a keresési szolgáltatás szintjén. A már létező tartalmak nem titkosíthatók. 

A kulcsoknak nem kell ugyanabban a Key Vault lennie. Egyetlen keresési szolgáltatás több titkosított indexet vagy szinonima-leképezést is képes tárolni, amelyek titkosítása a saját ügyfelek által felügyelt, különböző Kulcstartókban tárolt titkosítási kulcsokkal történik.  Az indexek és a szinonimák is használhatók ugyanabban a szolgáltatásban, amely nem titkosított az ügyfél által felügyelt kulcsokkal. 

> [!IMPORTANT] 
> Ez a funkció a [REST API 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) -es és a [.net SDK 8,0-es verziójában](search-dotnet-sdk-migration-version-9.md)érhető el. Jelenleg nem támogatott az ügyfél által felügyelt titkosítási kulcsok konfigurálása a Azure Portalban. A Search szolgáltatást január 2019 után kell létrehozni, és nem lehet ingyenes (megosztott) szolgáltatás.

## <a name="prerequisites"></a>Előfeltételek

Ebben a példában a következő szolgáltatásokat használjuk. 

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. 

+ [Hozzon létre egy Azure Key Vault erőforrást](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) , vagy keressen egy meglévő tárat az előfizetése alatt.

+ A konfigurációs feladatokhoz [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) vagy [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) van használatban.

+ A [Poster](search-get-started-postman.md), a [Azure PowerShell](search-create-index-rest-api.md) és az [Azure Cognitive Search SDK](https://aka.ms/search-sdk-preview) a REST API meghívására is használható. Jelenleg nem érhető el portál-támogatás az ügyfél által felügyelt titkosításhoz.

>[!Note]
> Az ügyfél által felügyelt kulcsok funkcióval történő titkosítás jellegéből adódóan az Azure Cognitive Search nem fogja tudni lekérni az adatait, ha az Azure Key Vault-kulcsot törli. Ha meg szeretné akadályozni az adatvesztést a véletlen Key Vault a kulcsok törlése miatt, a használat előtt engedélyeznie **kell** a helyreállítható törlést és a védelem kiürítését Key Vault. További információ: [Azure Key Vault Soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="1---enable-key-recovery"></a>1 – kulcshelyreállítás engedélyezése

A Azure Key Vault erőforrás létrehozása után a következő PowerShell-vagy Azure CLI-parancsok végrehajtásával engedélyezze a védelem **törlését** és **kiürítését** a kiválasztott kulcstartóban:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 – új kulcs létrehozása

Ha meglévő kulcsot használ az Azure Cognitive Search-tartalom titkosításához, hagyja ki ezt a lépést.

1. [Jelentkezzen be Azure Portalba](https://portal.azure.com) , és navigáljon a Key Vault irányítópultra.

1. Válassza a bal oldali navigációs ablaktáblán a **kulcsok** beállítást, majd kattintson a **+ Létrehozás/importálás**elemre.

1. A **kulcs létrehozása** ablaktáblán a **Beállítások**listájából válassza ki a kulcs létrehozásához használni kívánt módszert. **Létrehozhat egy új** kulcsot, **feltöltheti** a meglévő kulcsot, vagy használhatja a **visszaállítás biztonsági mentést** a kulcsok biztonsági másolatának kiválasztásához.

1. Adja meg a kulcs **nevét** , és ha kívánja, válassza ki a többi fontos tulajdonságot.

1. A telepítés elindításához kattintson a **Létrehozás** gombra.

Jegyezze fel a kulcs azonosítóját – ez a **kulcs értékének URI-ja**, a **kulcs neve**és a **kulcs verziószáma**. Ezeket a titkosított indexek megadásához kell megadni az Azure Cognitive Searchban.
 
![Új Key Vault-kulcs létrehozása](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Új Key Vault-kulcs létrehozása")

## <a name="3---create-a-service-identity"></a>3 – szolgáltatás identitásának létrehozása

Ha identitást rendel a keresési szolgáltatáshoz, Key Vault hozzáférési engedélyeket adhat meg a keresési szolgáltatáshoz. A keresési szolgáltatás a személyazonosságát fogja használni az Azure Key vaultban való hitelesítéshez.

Az Azure Cognitive Search az identitás hozzárendelésének két módját támogatja: felügyelt identitást vagy külsőleg felügyelt Azure Active Directory alkalmazást. 

Ha lehetséges, használjon felügyelt identitást. Ez a legegyszerűbb módja annak, hogy identitást rendeljen a keresési szolgáltatáshoz, és a legtöbb esetben működjön. Ha az indexekhez és a szinonimához több kulcsot használ, vagy ha a megoldás olyan elosztott architektúrában van, amely nem jogosult az identitás-alapú hitelesítésre, használja a cikk végén ismertetett, [külsőleg felügyelt Azure Active Directory megközelítést](#aad-app) .

 Általánosságban elmondható, hogy egy felügyelt identitás lehetővé teszi a keresési szolgáltatás hitelesítését Azure Key Vault a hitelesítő adatok kódban való tárolása nélkül. Az ilyen típusú felügyelt identitás életciklusa a keresési szolgáltatás életciklusához van kötve, amely csak egyetlen felügyelt identitással rendelkezhet. [További információ a felügyelt identitásokról](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Felügyelt identitás létrehozásához [Jelentkezzen be a toAzure-portálra](https://portal.azure.com) , és nyissa meg a keresési szolgáltatás irányítópultját. 

1. A bal oldali navigációs ablaktáblán kattintson az **Identity (identitás** ) elemre, módosítsa az állapotát **a be**értékre, majd kattintson a **Mentés**gombra.

![Felügyelt identitás engedélyezése](./media/search-enable-msi/enable-identity-portal.png "A Mangal Identity engedélyezése")

## <a name="4---grant-key-access-permissions"></a>4 – kulcsokra vonatkozó hozzáférési engedélyek megadása

Ha engedélyezni szeretné, hogy a keresési szolgáltatás használhassa a Key Vault kulcsot, bizonyos hozzáférési engedélyeket kell adnia a keresési szolgáltatásnak.

A hozzáférési engedélyeket bármikor visszavonhatja. A visszavonás után a Key vaultot használó keresési szolgáltatás indexe vagy szinonimája használhatatlan lesz. A Key Vault-hozzáférési engedélyek későbbi visszaállításakor a rendszer visszaállítja a index\synonym-leképezési hozzáférést. További információt a [kulcstartó biztonságos elérését](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)ismertető témakörben talál.

1. [Jelentkezzen be Azure Portalba](https://portal.azure.com) , és nyissa meg a Key Vault áttekintés lapját. 

1. Válassza ki a **hozzáférési házirendek** beállítást a bal oldali navigációs ablaktáblán, és kattintson az **+ új hozzáadása**lehetőségre.

   ![Új Key Vault hozzáférési szabályzat hozzáadása](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Új Key Vault hozzáférési szabályzat hozzáadása")

1. Kattintson a **rendszerbiztonsági tag kiválasztása** elemre, és válassza ki az Azure Cognitive Search szolgáltatást. A név vagy a felügyelt identitás engedélyezése után megjelenő objektumazonosító alapján is megkeresheti.

   ![A Key Vault hozzáférési szabályzatának kiválasztása](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "A Key Vault hozzáférési szabályzatának kiválasztása")

1. Kattintson a **legfontosabb engedélyek** elemre, és válassza a *beolvasás*, a *kulcs* és a *betakarás kulcsa*lehetőséget. A szükséges engedélyek gyors kiválasztásához használhatja a *Azure Data Lake Storage vagy az Azure Storage* -sablont.

   Az Azure Cognitive Search a következő [hozzáférési engedélyekkel](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)kell megadni:

   * *Get* -lehetővé teszi, hogy a keresési szolgáltatás lekérje a kulcs nyilvános részeit egy Key Vault
   * *Wrap Key* – lehetővé teszi, hogy a keresési szolgáltatás a kulcs használatával megvédje a belső titkosítási kulcsot
   * *Kicsomagolási kulcs* – lehetővé teszi, hogy a keresési szolgáltatás a kulcsot használja a belső titkosítási kulcs kicsomagolásához.

   ![Válassza ki a Key Vault hozzáférési szabályzatának kulcsának engedélyeit](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Válassza ki a Key Vault hozzáférési szabályzatának kulcsának engedélyeit")

1. Kattintson **az OK** gombra, és **mentse** a hozzáférési szabályzat módosításait.

> [!Important]
> Az Azure Cognitive Search titkosított tartalma úgy van konfigurálva, hogy egy meghatározott Azure Key Vault-kulcsot használjon egy adott **verzióval**. Ha megváltoztatja a kulcsot vagy a verziót, az indexet vagy a szinonima-térképet frissíteni kell az új key\version használatára az előző key\version. törlése **előtt** . Ha ezt nem teszi meg, az index vagy a szinonimák leképezése használhatatlan lesz, a kulcs elérésének elvesztése után nem fogja tudni visszafejteni a tartalmat.   

## <a name="5---encrypt-content"></a>5 – tartalom titkosítása

Az ügyfél által felügyelt kulccsal titkosított index vagy szinonima leképezés létrehozása még nem lehetséges a Azure Portal használatával. Az Azure Cognitive Search REST API használatával hozzon létre egy ilyen indexet vagy szinonima-térképet.

Az index és a szinonimák leképezése is támogatja a kulcs megadásához használt új felső szintű **encryptionKey** tulajdonságot. 

A Key Vault **URI-ja**, a **kulcs neve** és a Key Vault kulcsának **verziószáma** alapján létrehozhatunk egy **encryptionKey** -definíciót:

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> Ezek a kulcstartó-részletek egyike sem minősül titkosnak, és könnyen lekérhető, ha megkeresi a megfelelő Azure Key Vault kulcsot tartalmazó lapot Azure Portal.

Ha HRE alkalmazást használ a Key Vault hitelesítéshez felügyelt identitás használata helyett, adja hozzá a HRE-alkalmazás **hozzáférési hitelesítő adatait** a titkosítási kulcshoz: 
```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

## <a name="example-index-encryption"></a>Példa: index encryption
Az új indexnek a REST API használatával történő létrehozásának részletei a [create index (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)helyen találhatók, ahol az egyetlen különbség a titkosítási kulcs részleteinek megadása az index definíciójának részeként: 

```json
{
 "name": "hotels",  
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
 "encryptionKey": {
   "keyVaultUri": "https://demokeyvault.vault.azure.net",
   "keyVaultKeyName": "myEncryptionKey",
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
Most már elküldheti az index-létrehozási kérelmet, majd megkezdheti a normál index használatát.

## <a name="example-synonym-map-encryption"></a>Példa: szinonimák leképezésének titkosítása

Az új szinonimák leképezésének a REST API használatával történő létrehozásának részleteit a [szinonimák leképezése (Azure Cognitive Search REST API) létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)című rész tartalmazza, ahol az egyetlen különbség a titkosítási kulcs részleteinek megadása a szinonimák leképezése definíciójának részeként: 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
Most már elküldheti a szinonima-hozzárendelési kérést, majd normál módon megkezdheti a használatát.

>[!Important] 
> Habár a **encryptionKey** nem vehetők fel a meglévő Azure Cognitive Search indexekhez vagy szinonimái térképekhez, a három kulcstartó részleteinek (például a kulcs verziójának frissítése) különböző értékeinek megadásával lehet frissíteni. Új Key Vault kulcsra vagy új kulcs verzióra való váltáskor a kulcsot használó összes Azure Cognitive Search indexet vagy szinonima-térképet először frissíteni kell az új key\version használatára az előző key\version. törlése **előtt** . Ha ezt nem teszi meg, az index vagy a szinonimák leképezése használhatatlan lesz, mivel a kulcs elérésének elvesztése után nem tudja visszafejteni a tartalmat.   
> A Key Vault-hozzáférési engedélyek későbbi visszaállításával visszaállíthatja a tartalom-hozzáférést.

## <a name="aad-app"></a>Speciális: külsőleg felügyelt Azure Active Directory alkalmazás használata

Ha egy felügyelt identitás nem lehetséges, létrehozhat egy Azure Active Directory alkalmazást az Azure Cognitive Search szolgáltatáshoz tartozó rendszerbiztonsági tag használatával. A felügyelt identitások nem életképesek az alábbi feltételek teljesülése esetén:

* A keresési szolgáltatás hozzáférési engedélyeit nem lehet közvetlenül a kulcstartóhoz adni (például ha a keresési szolgáltatás más Active Directory bérlő, mint a Azure Key Vault).

* Egyetlen keresési szolgáltatás szükséges több titkosított indexes\synonym-Térkép üzemeltetéséhez, amelyek mindegyike egy másik Kulcstartóból származó másik kulcsot használ, ahol minden kulcstartónak **más identitást** kell használnia a hitelesítéshez. Ha más identitást használ a különböző kulcstartók kezelésére, érdemes lehet a fenti felügyelt identitást használni.  

Az ilyen topológiák befogadásához az Azure Cognitive Search támogatja Azure Active Directory (HRE) alkalmazások használatát a keresési szolgáltatás és a Key Vault közötti hitelesítéshez.    
HRE-alkalmazás létrehozása a portálon:

1. [Egy Azure Active Directory-alkalmazás létrehozása](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Szerezze be az alkalmazás azonosítóját és a hitelesítési kulcsot](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) , mivel ezek a titkosított indexek létrehozásához szükségesek lesznek. A megadható értékeknek tartalmaznia kell az **alkalmazás azonosítóját** és a **hitelesítési kulcsot**.

>[!Important]
> Ha úgy dönt, hogy felügyelt identitás helyett HRE-alkalmazást használ, vegye figyelembe, hogy az Azure Cognitive Search nem rendelkezik jogosultsággal a HRE-alkalmazásnak az Ön nevében történő felügyeletére, és a HRE-alkalmazás kezelése (például rendszeres időközönként) az alkalmazás hitelesítési kulcsának elforgatása.
> Egy HRE-alkalmazás vagy annak hitelesítési kulcsának módosításakor az adott alkalmazást használó Azure Cognitive Search indexet vagy szinonima-térképet először frissíteni kell az új alkalmazás-ID\key használatára az előző alkalmazás vagy az engedélyezési kulcs törlése **előtt** , és a Key Vault hozzáférésének visszavonása előtt.
> Ha ezt nem teszi meg, az index vagy a szinonimák leképezése használhatatlan lesz, mivel a kulcs elérésének elvesztése után nem tudja visszafejteni a tartalmat.   

## <a name="next-steps"></a>Következő lépések

Ha nem ismeri az Azure biztonsági architektúráját, tekintse át az [Azure biztonsági dokumentációját](https://docs.microsoft.com/azure/security/), és különösen a következő cikket:

> [!div class="nextstepaction"]
> [Inaktív adatok titkosítása](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
