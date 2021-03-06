---
title: Azure Key Vault áttekintése – Azure Key Vault | Microsoft Docs
description: Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 4e2953b107b017d032e737e2878472166c677839
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194954"
---
# <a name="what-is-azure-key-vault"></a>Mi az Azure Key Vault?

A Azure Key Vault a következő problémák megoldásában nyújt segítséget:

- **Titkos kódok kezelése** – Az Azure Key Vaulttal biztonságosan tárolhatók a jogkivonatok, jelszavak, tanúsítványok, API-kulcsok és egyéb titkos kulcsok, valamint szigorúan szabályozható az ezekhez való hozzáférés
- **Kulcskezelés** – Az Azure Key Vault kulcskezelési megoldásként is használható. Az Azure Key Vaulttal egyszerűen létrehozhatja és vezérelheti az adatok titkosításához használt titkosítási kulcsokat. 
- A **tanúsítványkezelő** – Azure Key Vault egy olyan szolgáltatás is, amely lehetővé teszi a nyilvános és magánhálózati Transport Layer Security/SSL (TLS/SSL) tanúsítványok egyszerű üzembe helyezését, kezelését és telepítését az Azure-ral és a belső csatlakoztatott erőforrásokkal való használatra. 
- A **hardveres biztonsági modulok által támogatott titkos kulcsok tárolása** – a titkokat és a kulcsokat a szoftver vagy az FIPS 140-2 2. szintű ellenőrzött HSM védi.

## <a name="why-use-azure-key-vault"></a>Miért érdemes az Azure Key Vaultot használni?

### <a name="centralize-application-secrets"></a>Titkos alkalmazáskulcsok központosítása

A titkos alkalmazáskulcsok tárolásának központosítása az Azure Key Vaultban lehetővé teszi azok elosztásának irányítását. A Key Vault nagymértékben csökkenti a veszélyét annak, hogy a titkos kulcsok véletlenül kiszivárogjanak. A Key Vault használatával az alkalmazásfejlesztőknek többé nem kell az alkalmazásban tárolniuk a biztonsági információkat. Nem kell a biztonsági adatokat tárolni az alkalmazásokban, így nem kell ezt az információt a kód részévé tenni. Tegyük fel például, hogy egy alkalmazásnak egy adatbázishoz kell csatlakoznia. Ahelyett, hogy az alkalmazás kódjában tárolja a kapcsolatok karakterláncát, biztonságosan tárolhatja Key Vaultban.

Az alkalmazások az URI-k használatával biztonságosan érhetik el a szükséges információkat. Ezek az URI-k lehetővé teszik az alkalmazások számára a titkos kód adott verzióinak lekérését. Nem kell egyéni kódot írnia, hogy megvédje a Key Vaultban tárolt titkos adatokat.

### <a name="securely-store-secrets-and-keys"></a>A titkos kulcsok és a kulcsok biztonságos tárolása

A titkos kulcsok és a kulcsok számára az Azure biztosít védelmet az ipari szabványoknak megfelelő algoritmusok, kulcshosszok és hardveres biztonsági modulok (HSM) segítségével. A használt HSM-eket a FIPS 140-2 2. szintje szerint ellenőrizték.

A kulcstartóhoz való hozzáférés előtt a hívónak (felhasználónak vagy alkalmazásnak) megfelelő hitelesítésre és engedélyezésre van szüksége. A hitelesítés a hívó azonosítását szolgálja, az engedélyezés pedig meghatározza, milyen műveletek elvégzésére jogosult.

A hitelesítés az Azure Active Directory használatával történik. Az engedélyezés szerepköralapú hozzáférés-vezérléssel (RBAC) vagy Key Vault hozzáférési szabályzattal történhet. Az RBAC a tárolók kezeléséhez használható, a kulcstartó hozzáférési szabályzatát pedig a tárolóban található adatokhoz való hozzáféréskor használja a rendszer.

Az Azure Key Vault-kulcstartók szoftveresen vagy hardveresen lehetnek HSM-védettek. Az olyan esetekben, ahol nagyobb biztonságra van szükség, hardveres biztonsági modulokkal importálhat vagy hozhat létre a HSM határait mindig betartó kulcsokat. A Microsoft a nCipher hardveres biztonsági modulokat használja. A nCipher-eszközök segítségével áthelyezhet egy kulcsot a HSM-ből Azure Key Vaultba.

Végül pedig az Azure Key Vault kialakításából adódóan a Microsoft nem tekintheti meg és nem fejtheti vissza az adatokat.

### <a name="monitor-access-and-use"></a>A hozzáférés és használat monitorozása

Miután létrehozott néhány kulcstartót, monitorozhatja, hogyan és mikor férnek hozzá a kulcsokhoz és a titkos kulcsokhoz. A tevékenységek figyeléséhez engedélyezheti a naplózást a tárolók számára. Az Azure Key Vaultot beállíthatja az alábbiak elvégzésére:

- Archiválás tárfiókba
- Streamelés eseményközpontba
- Küldje el a naplókat Azure Monitor naplókba.

Engedélyekkel rendelkezik a naplók felett, és meg is védheti őket a hozzáférés korlátozásával, illetve törölheti azokat a naplókat, amelyekre már nincs szüksége.

### <a name="simplified-administration-of-application-secrets"></a>A titkos alkalmazáskulcsok egyszerűsített adminisztrációja

Értékes adatok tárolásakor el kell végeznie néhány lépést. A biztonsági információknak védettnek kell lenniük, meg kell tartaniuk egy életciklust, és a szolgáltatásnak erősen elérhetőnek kell lennie. Azure Key Vault leegyszerűsíti az alábbi követelmények teljesítésének folyamatát:

- A hardveres biztonsági modulok belső ismeretének szükségessége.
- Rendkívül gyorsan igazodik a vállalata használati igényeiben jelentkező növekedésekhez.
- A kulcstartója tartalmát egy régión belül és egy másodlagos régióba replikálja. Az adatreplikáció biztosítja a magas rendelkezésre állást, és elveszi a rendszergazda által a feladatátvétel elindításához szükséges bármilyen művelet szükségességét.
- Standard Azure felügyeleti lehetőségeket biztosít a Portal, az Azure CLI és a PowerShell segítségével.
- Automatizálja a nyilvános hitelesítésszolgáltatóktól vásárolt tanúsítványokon elvégzett egyes műveleteket, például a regisztrálást és a megújítást.

Továbbá az Azure Key Vault-kulcstartók lehetővé teszik a titkos alkalmazáskulcsok elkülönítését. Az alkalmazások csak azt a tárolót érhetik el, amelyhez hozzáféréssel rendelkeznek, és csak meghatározott műveletekre korlátozhatók. Alkalmazásonként egy Azure Key Vault-kulcstárolót hozhat létre, és a benne tárolt titkos kulcsok használatát csak egy adott alkalmazásra és fejlesztői csapatra korlátozhatja.

### <a name="integrate-with-other-azure-services"></a>Integráció más Azure-szolgáltatásokkal

Az Azure-beli Biztonságos tár Key Vault a következő forgatókönyvek egyszerűsítésére lett felhasználva:
-  [Azure Disk Encryption](../security/fundamentals/encryption-overview.md)
-  Az SQL Server és a Azure SQL Database [Always encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkciója
- [Azure app Service]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate). 

A Key Vault integrálható tárfiókokkal, eseményközpontokkal és a naplóelemzéssel.

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: Azure Key Vault-kulcstartó létrehozása a parancssori felülettel](quick-create-cli.md)
- [Azure-webalkalmazások konfigurálása a Key Vault titkos kulcsainak olvasásához](tutorial-web-application-keyvault.md)
