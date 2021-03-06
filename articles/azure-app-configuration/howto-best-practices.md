---
title: Az Azure app Configuration – ajánlott eljárások | Microsoft Docs
description: Útmutató az Azure-alkalmazások konfigurációjának legmegfelelőbb használatához
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 37f93099027f810e8089119536e089e07080d0bc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898634"
---
# <a name="azure-app-configuration-best-practices"></a>Az Azure app Configuration ajánlott eljárásai

Ez a cikk az Azure-alkalmazások konfigurációjának használatával kapcsolatos gyakori mintákat és ajánlott eljárásokat ismerteti.

## <a name="key-groupings"></a>Kulcsfontosságú csoportok

Az alkalmazás konfigurálása két lehetőséget biztosít a kulcsok rendszerezésére:

* Kulcs előtagjai
* Címkék

A kulcsok csoportosításához használhatja az egyiket vagy mindkét lehetőséget is.

A *kulcs előtagjai* a kulcsok kezdetének részei. A kulcsok készletét logikailag csoportosíthatja a nevükben szereplő előtag használatával. Az előtagok több olyan összetevőt is tartalmazhatnak, amelyek egy adott URL-címhez hasonlóan egy elválasztó, például `/`, egy névtér létrehozásához. Ilyen hierarchiák akkor hasznosak, ha sok alkalmazás, összetevő-szolgáltatás és környezet kulcsait tárolja egy alkalmazás-konfigurációs tárolóban.

Fontos szem előtt tartani, hogy a kulcsok az alkalmazás kódjára hivatkoznak a megfelelő beállítások értékeinek lekéréséhez. A kulcsok nem változnak, különben minden alkalommal módosítania kell a kódot.

A *címkék* a kulcsok attribútumai. Egy kulcs változatának létrehozásához használatosak. Kijelölhet például címkéket egy kulcs több verziójához. Egy verzió lehet iteráció, környezet vagy más környezetfüggő információ. Az alkalmazás egy másik címke megadásával kérheti a kulcs értékeinek egy teljesen eltérő készletét. Ennek eredményeképpen az összes fontos hivatkozás változatlan marad a kódban.

## <a name="key-value-compositions"></a>Kulcs-érték kompozíciók

Az alkalmazás konfigurációja a vele együtt tárolt összes kulcsot független entitásként kezeli. Az alkalmazás konfigurációja nem kísérli meg a kulcsok közötti kapcsolat következtetését, illetve a kulcs értékének öröklését a hierarchiájuk alapján. A kulcsok több készletét is összesítheti, ha az alkalmazás kódjában a megfelelő konfiguráció halmozásával párosított címkéket használ.

Nézzük meg egy példát. Tegyük fel, hogy van egy **Asset1**nevű beállítása, amelynek értéke változó lehet a fejlesztési környezettől függően. Hozzon létre egy "Asset1" nevű kulcsot egy üres címkével és egy "fejlesztés" nevű címkével. Az első címkében a **Asset1**alapértelmezett értékét helyezi el, és az utóbbiban egy adott értéket helyez el a "fejlesztés" értékre.

A kódban először le kell kérnie a kulcs értékeit címkék nélkül, majd a "fejlesztés" címkével megegyező számú kulcs-értéket kell lekérnie. Ha a második alkalommal kéri le az értékeket, a kulcsok korábbi értékei felül lesznek írva. A .NET Core konfigurációs rendszer lehetővé teszi, hogy az egymásra épülő konfigurációs adat több készletét "verem". Ha egy kulcs egynél több készletben található, az azt tartalmazó utolsó készletet használja. A modern programozási keretrendszer, például a .NET Core esetében ez a halmozási képesség ingyenesen elérhető, ha natív konfigurációs szolgáltatót használ az alkalmazás konfigurálásához. A következő kódrészlet azt mutatja be, hogyan valósítható meg a halmozás egy .NET Core-alkalmazásban:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>Alkalmazás-konfiguráció rendszerindítási szolgáltatása

Az alkalmazás-konfigurációs tároló eléréséhez használhatja a kapcsolati karakterláncát, amely a Azure Portalban érhető el. Mivel a kapcsolatok karakterlánca hitelesítő adatokat tartalmaz, azok titkos kulcsnak tekintendők. Ezeket a titkokat Azure Key Vault kell tárolni, és a kódnak hitelesítenie kell a Key Vault a lekéréséhez.

A jobb lehetőség az Azure Active Directory felügyelt identitások funkciójának használata. A felügyelt identitások esetében csak az alkalmazás-konfigurációs végpont URL-címére van szükség az alkalmazás-konfigurációs tároló eléréséhez. Az URL-címet beágyazhatja az alkalmazás kódjába (például a *appSettings. JSON* fájlban). A részletekért lásd: az [Azure által felügyelt identitások integrálása](howto-integrate-azure-managed-service-identity.md) .

## <a name="app-or-function-access-to-app-configuration"></a>Alkalmazás-konfigurációhoz való hozzáférés alkalmazása vagy funkciója

A következő módszerek bármelyikével biztosíthat hozzáférést a webalkalmazások vagy függvények alkalmazás-konfigurációjához:

* A Azure Portalon adja meg a kapcsolódási karakterláncot az alkalmazás konfigurációs tárolójához a App Service Alkalmazásbeállítások között.
* Tárolja a kapcsolódási karakterláncot az alkalmazás-konfigurációs tárolóban Key Vault és [hivatkozzon a app Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Az Azure által felügyelt identitások használatával érheti el az alkalmazás konfigurációs tárolóját. További információ: [integrálás az Azure felügyelt identitásokkal](howto-integrate-azure-managed-service-identity.md).
* Az alkalmazás konfigurációjának leküldéses konfigurációja App Servicera. Az alkalmazás konfigurációja olyan exportálási függvényt biztosít (Azure Portal és az Azure CLI-ben), amely közvetlenül a App Serviceba küld adatokat. Ezzel a módszerrel egyáltalán nem kell módosítania az alkalmazás kódját.

## <a name="next-steps"></a>Következő lépések

* [Kulcsok és értékek](./concept-key-value.md)
