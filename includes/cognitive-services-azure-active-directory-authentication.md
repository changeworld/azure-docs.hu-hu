---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 8754504655cdd08c9bf9f89311cb6c5d1057f0e6
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262267"
---
## <a name="authenticate-with-azure-active-directory"></a>Hitelesítés az Azure Active Directoryval

> [!IMPORTANT]
> 1. Jelenleg **csak** a Computer Vision API, a Face API, a Text Analytics API, a teljes olvasó, az űrlap-felismerő, a anomália-detektor és az összes Bing-szolgáltatás Bing Custom Search támogatja a hitelesítést a Azure Active Directory (HRE) használatával.
> 2. Az HRE-hitelesítést mindig az Azure-erőforrás egyéni altartománynevével együtt kell használni. A [regionális végpontok](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) nem TÁMOGATJÁK a HRE-hitelesítést.

Az előző szakaszokban bemutatjuk, hogyan végezheti el a hitelesítést az Azure Cognitive Services az egy vagy több szolgáltatást használó előfizetési kulccsal. Habár ezek a kulcsok gyors és egyszerű elérési utat biztosítanak a fejlesztés megkezdéséhez, azok a bonyolultabb forgatókönyvek, amelyek szerepköralapú hozzáférés-vezérlést igényelnek. Nézzük meg, mi szükséges a hitelesítéshez Azure Active Directory (HRE) használatával.

A következő fejezetekben a Azure Cloud Shell vagy az Azure CLI használatával hozhat létre altartományokat, szerepköröket rendelhet hozzá, és beszerezhet egy tulajdonosi jogkivonatot az Azure-Cognitive Services meghívásához. Ha elakad, a rendszer a Azure Cloud Shell/Azure CLI-ben minden parancshoz elérhető összes lehetőséget tartalmazó hivatkozásokat tartalmaz.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Erőforrás létrehozása egyéni altartománnyal

Első lépésként hozzon létre egy egyéni altartományt. Ha olyan meglévő Cognitive Services-erőforrást szeretne használni, amely nem rendelkezik egyéni altartomány nevével, kövesse a [Cognitive Services egyéni altartományok](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) című témakör utasításait az erőforráshoz tartozó egyéni altartományok engedélyezéséhez.

1. Először nyissa meg a Azure Cloud Shell. Ezután [válasszon ki egy előfizetést](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0):

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Ezután [hozzon létre egy Cognitive Services erőforrást](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) egy egyéni altartománnyal. Az altartomány nevének globálisan egyedinek kell lennie, és nem tartalmazhat speciális karaktereket (például: ".", "!", ",").

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Ha a művelet sikeres, a **végpontnak** az erőforrás egyedi altartománynevét kell megjelenítenie.


### <a name="assign-a-role-to-a-service-principal"></a>Szerepkör kiosztása egy egyszerű szolgáltatáshoz

Most, hogy rendelkezik az erőforráshoz tartozó egyéni altartománnyal, hozzá kell rendelnie egy szerepkört egy egyszerű szolgáltatáshoz.

> [!NOTE]
> Ne feledje, hogy a HRE szerepkör-hozzárendelések akár öt percet is igénybe vehetnek.

1. Először regisztráljon egy [HRE alkalmazást](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   A következő lépésben szüksége lesz a **ApplicationId** .

2. Ezután [létre kell hoznia egy szolgáltatásnevet](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) a HRE alkalmazáshoz.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Ha a Azure Portalban regisztrál egy alkalmazást, ez a lépés az Ön számára lesz elvégezve.

3. Az utolsó lépés a ["Cognitive Services user" szerepkör társítása](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) az egyszerű szolgáltatáshoz (hatóköre az erőforrás). Szerepkör hozzárendelésével a szolgáltatás egyszerű hozzáférést biztosít ehhez az erőforráshoz. Az előfizetés több erőforrásához is biztosíthatja ugyanazt a szolgáltatást.
   >[!NOTE]
   > Az egyszerű szolgáltatásnév ObjectId van használatban, nem az alkalmazás ObjectId.
   > A ACCOUNT_ID a létrehozott Cognitive Services fiók Azure-erőforrás-azonosítója lesz. Az Azure Resource id-t a Azure Portal erőforrás "tulajdonságok" területén találja.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Mintakérelem

Ebben a példában egy jelszót használunk az egyszerű szolgáltatás hitelesítéséhez. A rendszer a megadott jogkivonatot használja a Computer Vision API meghívásához.

1. A **TenantId**beszerzése:
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Token beszerzése:
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Hívja meg a Computer Vision API:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Másik lehetőségként az egyszerű szolgáltatás hitelesítése tanúsítvánnyal végezhető el. Az egyszerű szolgáltatásnév mellett a felhasználói rendszerbiztonsági tag is támogatott azáltal, hogy egy másik HRE-alkalmazásban delegált engedélyekkel rendelkezik. Ebben az esetben a jelszavak vagy tanúsítványok helyett a felhasználókat a rendszer a token beszerzése során a kétfaktoros hitelesítésre kéri.
