---
title: Engedélyezés – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: a Threat Modeling Toolban elérhető fenyegetések enyhítése
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 75bbce0f1e9787e55880ccac80dacb5457e1f2c0
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728386"
---
# <a name="security-frame-authorization--mitigations"></a>Biztonsági keret: Engedélyezés | Enyhítését 
| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Számítógép-megbízhatósági kapcsolat határa** | <ul><li>[Győződjön meg arról, hogy a megfelelő ACL-ek úgy vannak konfigurálva, hogy korlátozzák az eszközön tárolt adathozzáférés korlátozását](#acl-restricted-access)</li><li>[Győződjön meg arról, hogy a bizalmas felhasználóspecifikus alkalmazás tartalma a felhasználói profil címtárában van tárolva](#sensitive-directory)</li><li>[Győződjön meg arról, hogy a telepített alkalmazások legalább jogosultsággal futnak](#deployed-privileges)</li></ul> |
| **Webalkalmazás** | <ul><li>[Szekvenciális lépések sorrendjének érvényesítése az üzleti logika folyamatainak feldolgozásakor](#sequential-logic)</li><li>[A ráta korlátozására szolgáló mechanizmus megvalósítása a számbavétel megakadályozásához](#rate-enumeration)</li><li>[Győződjön meg arról, hogy a megfelelő engedélyezés van érvényben, és a legalacsonyabb jogosultsági szint elvét követik](#principle-least-privilege)</li><li>[Az üzleti logika és az erőforrás-hozzáférés engedélyezési döntései nem alapulhatnak a bejövő kérelmek paramétereinek alapján](#logic-request-parameters)</li><li>[Győződjön meg arról, hogy a tartalom és az erőforrások nem enumerable, és nem érhetők el kényszerített böngészésen keresztül](#enumerable-browsing)</li></ul> |
| **Adatbázis** | <ul><li>[Győződjön meg arról, hogy az adatbázis-kiszolgálóhoz való kapcsolódáshoz a legkevésbé privilegizált fiókok használhatók](#privileged-server)</li><li>[A sor szintű biztonsági RLS megvalósítása annak megakadályozása érdekében, hogy a bérlők hozzáférjenek egymás adatokhoz](#rls-tenants)</li><li>[A sysadmin szerepkörnek csak érvényes szükséges felhasználókkal kell rendelkeznie](#sysadmin-users)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Kapcsolódás a Cloud Gatewayhez a legkevésbé privilegizált jogkivonatok használatával](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Az eszközök jogkivonatának létrehozásához használjon csak küldési engedélyek SAS-kulcsát](#sendonly-sas)</li><li>[Ne használjon olyan hozzáférési jogkivonatokat, amelyek közvetlen hozzáférést biztosítanak az Event hub-hoz](#access-tokens-hub)</li><li>[Kapcsolódás az Event hubhoz a minimálisan szükséges engedélyekkel rendelkező SAS-kulcsok használatával](#sas-minimum-permissions)</li></ul> |
| **Azure Document DB** | <ul><li>[Erőforrás-tokenek használata a Azure Cosmos DBhoz való kapcsolódáshoz, amikor lehetséges](#resource-docdb)</li></ul> |
| **Azure-beli megbízhatósági kapcsolat határa** | <ul><li>[Részletes hozzáférés-kezelés engedélyezése az Azure-előfizetéshez az RBAC használatával](#grained-rbac)</li></ul> |
| **Service Fabric megbízhatósági kapcsolat határa** | <ul><li>[Az ügyfél hozzáférésének korlátozása a RBAC használatával](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Biztonsági modellezés végrehajtása és a mező szintű biztonság használata, ha szükséges](#modeling-field)</li></ul> |
| **Dynamics CRM-portál** | <ul><li>[A portál-fiókok biztonsági modellezésének elvégzése szem előtt tartva, hogy a portál biztonsági modellje különbözik a többi CRM-től](#portal-security)</li></ul> |
| **Azure Storage** | <ul><li>[Részletes engedélyek megadása az Azure-beli entitások egy tartományában Table Storage](#permission-entities)</li><li>[Szerepköralapú Access Control (RBAC) engedélyezése az Azure Storage-fiókhoz a Azure Resource Manager használatával](#rbac-azure-manager)</li></ul> |
| **Mobil ügyfél** | <ul><li>[Implicit jailbreak vagy gyökeresedési észlelés megvalósítása](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Gyenge osztály-hivatkozás a WCF-ben](#weak-class-wcf)</li><li>[WCF – engedélyezési vezérlő implementálása](#wcf-authz)</li></ul> |
| **Webes API** | <ul><li>[Megfelelő engedélyezési mechanizmus implementálása a ASP.NET web API-ban](#authz-aspnet)</li></ul> |
| **IoT-eszköz** | <ul><li>[Engedélyezési ellenőrzések végrehajtása az eszközön, ha a különböző jogosultsági szinteket igénylő műveleteket támogat](#device-permission)</li></ul> |
| **IoT-mező átjárója** | <ul><li>[Ha olyan műveleteket támogat, amelyek különböző jogosultsági szinteket igényelnek, hajtsa végre az engedélyezési ellenőrzéseket a helyszíni átjárón.](#field-permission)</li></ul> |

## <a id="acl-restricted-access"></a>Győződjön meg arról, hogy a megfelelő ACL-ek úgy vannak konfigurálva, hogy korlátozzák az eszközön tárolt adathozzáférés korlátozását

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Számítógép-megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Győződjön meg arról, hogy a megfelelő ACL-ek úgy vannak konfigurálva, hogy korlátozzák az eszközön tárolt adathozzáférés korlátozását|

## <a id="sensitive-directory"></a>Győződjön meg arról, hogy a bizalmas felhasználóspecifikus alkalmazás tartalma a felhasználói profil címtárában van tárolva

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Számítógép-megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Győződjön meg arról, hogy a bizalmas felhasználóspecifikus alkalmazás tartalma a felhasználói profil címtárában van tárolva. Ezzel megakadályozható, hogy a gép több felhasználója hozzáférjen egymáshoz.|

## <a id="deployed-privileges"></a>Győződjön meg arról, hogy a telepített alkalmazások legalább jogosultsággal futnak

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Számítógép-megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Győződjön meg arról, hogy a telepített alkalmazás legalább egy jogosultsággal fut. |

## <a id="sequential-logic"></a>Szekvenciális lépések sorrendjének érvényesítése az üzleti logika folyamatainak feldolgozásakor

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Annak ellenőrzéséhez, hogy az adott fázist egy valódi felhasználó futtatta-e, az alkalmazás csak szekvenciális lépési sorrendben dolgozza fel az üzleti logikát, a reális emberi idő alatt feldolgozva, és nem dolgozza fel a lépéseket, kihagyott lépések , feldolgozott lépések egy másik felhasználótól vagy túl gyorsan elküldött tranzakciók.|

## <a id="rate-enumeration"></a>A ráta korlátozására szolgáló mechanizmus megvalósítása a számbavétel megakadályozásához

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Ügyeljen arra, hogy a bizalmas azonosítók véletlenszerűek legyenek. A CAPTCHA-vezérlő implementálása névtelen lapokon. Győződjön meg arról, hogy a hiba és a kivétel nem fedi fel az adott adathalmazt|

## <a id="principle-least-privilege"></a>Győződjön meg arról, hogy a megfelelő engedélyezés van érvényben, és a legalacsonyabb jogosultsági szint elvét követik

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | <p>Az elv azt jelenti, hogy a felhasználói fiók csak azokat a jogosultságokat adja meg, amelyek elengedhetetlenek a felhasználók működéséhez. Például egy biztonsági mentési felhasználónak nem kell szoftvert telepítenie: így a biztonsági mentési felhasználó csak a biztonsági mentési és a biztonsági mentési kapcsolódó alkalmazások futtatásához rendelkezik jogosultságokkal. A rendszer letiltja az egyéb jogosultságokat, például az új szoftverek telepítését. Az elv arra a személyes számítógép-felhasználóra is vonatkozik, aki általában normál felhasználói fiókban dolgozik, és egy privilegizált, jelszóval védett fiókot (azaz a rendszeradminisztrátort) nyit meg, és csak akkor, ha a helyzet feltétlenül igényli. </p><p>Ez az elv a webes alkalmazásokra is alkalmazható. Ahelyett, hogy a szerepköralapú hitelesítési módszerektől függően munkameneteket használ, inkább egy adatbázis-alapú hitelesítési rendszer használatával szeretne jogosultságokat rendelni a felhasználókhoz. Továbbra is munkameneteket használunk annak megállapításához, hogy a felhasználó bejelentkezett-e, csak most, hogy a felhasználót egy adott szerepkörhöz rendeli hozzá, jogosultságokkal igazoljuk, hogy a rendszeren milyen műveleteket kell végrehajtania. Ennek a módszernek a nagy részét képezi, ha egy felhasználóhoz kevesebb jogosultságot kell rendelni, a módosításokat a rendszer menet közben alkalmazza, mivel a hozzárendelés nem függ attól, hogy a munkamenet melyik esetben járt le először.</p>|

## <a id="logic-request-parameters"></a>Az üzleti logika és az erőforrás-hozzáférés engedélyezési döntései nem alapulhatnak a bejövő kérelmek paramétereinek alapján

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Ha ellenőrzi, hogy a felhasználó korlátozott-e bizonyos adatellenőrzésre, a hozzáférési korlátozásokat kiszolgálóoldali feldolgozással kell feldolgozni. A felhasználóazonosító-t a bejelentkezési munkamenet változóján belül kell tárolni, és a felhasználói adatoknak az adatbázisból való beolvasására kell használni. |

### <a name="example"></a>Példa
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
A lehetséges támadók mostantól nem változtathatják meg és nem módosíthatják az alkalmazás műveletét, mivel az adatok lekérésének azonosítóját a kiszolgáló kezeli.

## <a id="enumerable-browsing"></a>Győződjön meg arról, hogy a tartalom és az erőforrások nem enumerable, és nem érhetők el kényszerített böngészésen keresztül

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | <p>A bizalmas statikus és konfigurációs fájlokat nem szabad a webes gyökérben tárolni. Ahhoz, hogy a tartalom ne legyen nyilvános, a megfelelő hozzáférés-vezérlést kell alkalmazni, vagy magát a tartalmat kell eltávolítani.</p><p>Az erőteljes böngészést általában a találgatásos támadásokkal kombinálva gyűjtjük össze az adatokat úgy, hogy a lehető legtöbb URL-címet próbálják elérni a kiszolgálók könyvtárainak és fájljainak enumerálásához. A támadók a gyakran létező fájlok összes változatát megvizsgálják. A fájlok keresése például a psswd. txt, a Password. htm, a Password. dat és más változatok fájljait is magában foglalja.</p><p>Ennek enyhítése érdekében fel kell venni a találgatásos támadások észlelésére vonatkozó képességeket.</p>|

## <a id="privileged-server"></a>Győződjön meg arról, hogy az adatbázis-kiszolgálóhoz való kapcsolódáshoz a legkevésbé privilegizált fiókok használhatók

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [SQL Database engedélyek hierarchiája](https://msdn.microsoft.com/library/ms191465), [SQL Database-biztonságos elemek migrálására](https://msdn.microsoft.com/library/ms190401) |
| **Lépések** | Az adatbázishoz való kapcsolódáshoz a legkevésbé Kiemelt jogosultságú fiókokat kell használni. Az alkalmazás bejelentkezését korlátozni kell az adatbázisban, és csak a kiválasztott tárolt eljárásokat kell végrehajtania. Az alkalmazás bejelentkezésének nincs közvetlen táblához való hozzáférése. |

## <a id="rls-tenants"></a>A sor szintű biztonsági RLS megvalósítása annak megakadályozása érdekében, hogy a bérlők hozzáférjenek egymás adatokhoz

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Sql Azure, OnPrem |
| **Attribútumok**              | SQL-verzió – V12, SQL-verzió – MsSQL2016 |
| **Hivatkozik**              | [SQL Server sor szintű biztonság (RLS)](https://msdn.microsoft.com/library/azure/dn765131.aspx) |
| **Lépések** | <p>A sorszintű biztonság lehetővé teszi az ügyfelek számára, hogy szabályozzák egy adatbázistábla soraihoz való hozzáférést a lekérdezést végrehajtó felhasználó jellemzői alapján (például csoporttagság vagy végrehajtási környezet).</p><p>A soros szintű biztonság (RLS) leegyszerűsíti az alkalmazás biztonságának megtervezését és kódolását. Az RLS használatával korlátozásokat érvényesíthet az adatsorokhoz való hozzáférésre. Biztosítható például, hogy a munkavállalók csak a szervezeti egységükre vonatkozó adatsorokhoz férjenek hozzá, vagy egy ügyfél adathozzáférése korlátozható a vállalatával kapcsolatos adatokra.</p><p>A hozzáférés-korlátozási logika az adatbázis-szinten található, nem pedig egy másik alkalmazási szinten lévő adatoktól. Az adatbázisrendszer minden alkalommal alkalmazza a hozzáférési korlátozásokat, amikor az adathozzáférés bármely szintről megkísérelhető. Így a biztonsági rendszerek megbízhatóbbak és robusztusak a biztonsági rendszerek felületének csökkentésével.</p><p>|

Vegye figyelembe, hogy az RLS mint beépített adatbázis-szolgáltatás csak a 2016-es és az Azure SQL Database-t futtató SQL Serverre vonatkozik. Ha a beépített RLS funkció nincs implementálva, gondoskodni kell arról, hogy az adathozzáférés korlátozott legyen a nézetek és eljárások használatával

## <a id="sysadmin-users"></a>A sysadmin szerepkörnek csak érvényes szükséges felhasználókkal kell rendelkeznie

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [SQL Database engedélyek hierarchiája](https://msdn.microsoft.com/library/ms191465), [SQL Database-biztonságos elemek migrálására](https://msdn.microsoft.com/library/ms190401) |
| **Lépések** | A SysAdmin (rendszergazda) rögzített kiszolgálói szerepkör tagjainak nagyon korlátozottnak kell lenniük, és soha nem tartalmazhatják az alkalmazások által használt fiókokat.  Tekintse át a szerepkörbe tartozó felhasználók listáját, és távolítsa el a szükségtelen fiókokat|

## <a id="cloud-least-privileged"></a>Kapcsolódás a Cloud Gatewayhez a legkevésbé privilegizált jogkivonatok használatával

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Átjáró választása – Azure IoT Hub |
| **Hivatkozik**              | [IOT hub Access Control](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#Security) |
| **Lépések** | Adja meg a legalacsonyabb jogosultsági szintű engedélyeket a felhőalapú átjáróhoz (IoT Hub) csatlakozó különböző összetevőkhöz. Tipikus példa: az eszközök kezelése/kiépítési összetevő registryread/írást használ, az Event Processor (ASA) pedig a szolgáltatás-csatlakoztatást használja. Egyedi eszközök csatlakoztatása az eszköz hitelesítő adataival|

## <a id="sendonly-sas"></a>Az eszközök jogkivonatának létrehozásához használjon csak küldési engedélyek SAS-kulcsát

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure-eseményközpont | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Event Hubs hitelesítési és biztonsági modell áttekintése](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépések** | A rendszer egy SAS-kulcsot használ az egyes eszközök jogkivonatok létrehozásához. Csak küldési engedélyekkel rendelkező SAS-kulcs használata az adott közzétevőhöz tartozó eszköz jogkivonatának létrehozásakor|

## <a id="access-tokens-hub"></a>Ne használjon olyan hozzáférési jogkivonatokat, amelyek közvetlen hozzáférést biztosítanak az Event hub-hoz

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure-eseményközpont | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Event Hubs hitelesítési és biztonsági modell áttekintése](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépések** | Az Event hub közvetlen hozzáférését biztosító jogkivonatot nem szabad megadni az eszköz számára. Ha egy olyan eszközhöz, amely csak a közzétevő számára biztosít hozzáférést, akkor a legkevésbé privilegizált token használatával azonosítható és feketelistára kerül, ha a támadó vagy sérült eszköznek bizonyult.|

## <a id="sas-minimum-permissions"></a>Kapcsolódás az Event hubhoz a minimálisan szükséges engedélyekkel rendelkező SAS-kulcsok használatával

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure-eseményközpont | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Event Hubs hitelesítési és biztonsági modell áttekintése](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Lépések** | Adja meg a legalacsonyabb jogosultsági szintű engedélyeket az Event hub-hoz csatlakozó különböző háttérbeli alkalmazásokhoz. Külön SAS-kulcsok generálása minden háttérbeli alkalmazáshoz, és csak a szükséges engedélyek megadása – küldés, fogadás vagy kezelés.|

## <a id="resource-docdb"></a>Erőforrás-tokenek használata a Cosmos DBhoz való kapcsolódáshoz, amikor lehetséges

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Document DB | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Az erőforrás-token egy Azure Cosmos DB engedélyezési erőforráshoz van társítva, és rögzíti az adatbázis felhasználója és a felhasználó által adott Azure Cosmos DB alkalmazás-erőforráshoz (például gyűjtemény, dokumentum) való kapcsolatot. Mindig használjon erőforrás-jogkivonatot a Azure Cosmos DB eléréséhez, ha az ügyfél nem lehet megbízható a Master vagy a csak olvasható kulcsok használatával – például egy olyan végfelhasználói alkalmazáshoz, mint például a mobil-vagy asztali ügyfél. Használjon főkulcsot vagy csak olvasható kulcsokat a háttérbeli alkalmazásokból, amelyek biztonságos módon tárolhatják ezeket a kulcsokat.|

## <a id="grained-rbac"></a>Részletes hozzáférés-kezelés engedélyezése az Azure-előfizetéshez az RBAC használatával

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure-beli megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)  |
| **Lépések** | Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. Az RBAC használata lehetővé teszi, hogy csak olyan mértékű hozzáférést biztosítson, ami a felhasználóknak a feladataik elvégzéséhez szükséges.|

## <a id="cluster-rbac"></a>Az ügyfél hozzáférésének korlátozása a RBAC használatával

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Service Fabric megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Környezet – Azure |
| **Hivatkozik**              | [Szerepköralapú hozzáférés-vezérlés Service Fabric ügyfelek számára](https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security-roles/) |
| **Lépések** | <p>Az Azure Service Fabric két különböző hozzáférés-vezérlési típust támogat a Service Fabric-fürthöz csatlakozó ügyfelekhez: rendszergazda és felhasználó. A hozzáférés-vezérlés lehetővé teszi, hogy a fürt rendszergazdája a különböző felhasználói csoportok esetében korlátozza a hozzáférést bizonyos fürt műveleteihez, így a fürt biztonságosabbá válik.</p><p>A rendszergazdák teljes hozzáféréssel rendelkeznek a felügyeleti képességekhez (beleértve az írási/olvasási képességeket is). A felhasználók alapértelmezés szerint csak olvasási jogosultságot biztosítanak a felügyeleti képességekhez (például a lekérdezési képességekhez), valamint az alkalmazások és szolgáltatások feloldásának lehetőségét.</p><p>A fürt létrehozásakor a két ügyfél-szerepkört (rendszergazdát és ügyfelet) kell megadnia, ha külön tanúsítványokat biztosít mindegyikhez.</p>|

## <a id="modeling-field"></a>Biztonsági modellezés végrehajtása és a mező szintű biztonság használata, ha szükséges

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Biztonsági modellezés végrehajtása és a mező szintű biztonság használata, ha szükséges|

## <a id="portal-security"></a>A portál-fiókok biztonsági modellezésének elvégzése szem előtt tartva, hogy a portál biztonsági modellje különbözik a többi CRM-től

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM-portál | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | A portál-fiókok biztonsági modellezésének elvégzése szem előtt tartva, hogy a portál biztonsági modellje különbözik a többi CRM-től|

## <a id="permission-entities"></a>Részletes engedélyek megadása az Azure-beli entitások egy tartományában Table Storage

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | StorageType – tábla |
| **Hivatkozik**              | [Az Azure Storage-fiókban lévő objektumokhoz való hozzáférés delegálása SAS használatával](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_data-plane-security) |
| **Lépések** | Bizonyos üzleti forgatókönyvekben előfordulhat, hogy az Azure Table Storage a különböző felek számára kiszolgált bizalmas adatok tárolására is szükség lehet. Ilyenek például a különböző országokhoz/régiókhoz tartozó bizalmas adatok. Ilyen esetekben a SAS-aláírások a partíció és a sor kulcsainak megadásával állíthatók össze, így a felhasználók hozzáférhetnek egy adott országhoz/régióhoz tartozó adatokhoz.| 

## <a id="rbac-azure-manager"></a>Szerepköralapú Access Control (RBAC) engedélyezése az Azure Storage-fiókhoz a Azure Resource Manager használatával

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [A Storage-fiók biztonságossá tétele szerepköralapú Access Control (RBAC) használatával](https://azure.microsoft.com/documentation/articles/storage-security-guide/#management-plane-security) |
| **Lépések** | <p>Új Storage-fiók létrehozásakor ki kell választania a klasszikus vagy Azure Resource Manager telepítési modellt. Az Azure-beli erőforrások létrehozásának klasszikus modellje csak az előfizetéshez való teljes vagy semmis hozzáférést teszi lehetővé, viszont a Storage-fiókot.</p><p>A Azure Resource Manager modellel a Storage-fiókot egy erőforráscsoporthoz helyezheti el, és a Azure Active Directory használatával szabályozhatja az adott Storage-fiók felügyeleti síkjával való hozzáférést. Megadhatja például, hogy bizonyos felhasználók hozzáférjenek a Storage-fiók kulcsaihoz, míg más felhasználók megtekinthetik a Storage-fiók adatait, de nem férhetnek hozzá a Storage-fiók kulcsaihoz.</p>|

## <a id="rooting-detection"></a>Implicit jailbreak vagy gyökeresedési észlelés megvalósítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Mobil ügyfél | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | <p>Az alkalmazásnak meg kell őriznie a saját konfigurációját és a felhasználói adatvédelmet abban az esetben, ha a telefon gyökeres vagy a jail sérült. A gyökeresedés vagy a jail Breaking a jogosulatlan hozzáférést vonja maga után, amelyet a normál felhasználók nem tesznek elérhetővé a saját telefonoknál. Az alkalmazás indításakor az alkalmazásnak implicit észlelési logikával kell rendelkeznie annak észleléséhez, hogy a telefon feltört-e.</p><p>Az észlelési logika egyszerűen elérheti azokat a fájlokat, amelyek általában csak a legfelső szintű felhasználók férhetnek hozzá, például a következő módon:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>Ha az alkalmazás hozzáférhet bármelyik fájlhoz, az azt jelzi, hogy az alkalmazás root felhasználóként fut.</p>|

## <a id="weak-class-wcf"></a>Gyenge osztály-hivatkozás a WCF-ben

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [megerősítő Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Lépések** | <p>A rendszer gyenge osztályú referenciát használ, ami lehetővé teheti, hogy egy támadó jogosulatlan kódot hajtson végre. A program olyan felhasználó által definiált osztályra hivatkozik, amely nem egyedi módon van azonosítva. Ha a .NET betölti ezt a gyengén azonosított osztályt, a CLR-beli típus a megadott sorrendben a következő helyeken keresi a osztályt:</p><ol><li>Ha a típus szerelvénye ismert, a betöltő a konfigurációs fájl átirányítási helyein, a GAC-ban, a jelenlegi szerelvényben a konfigurációs adatokat és az alkalmazás alapkönyvtárát keresi.</li><li>Ha a szerelvény ismeretlen, a betöltő megkeresi az aktuális szerelvényt, a mscorlib és a TypeResolve-eseménykezelő által visszaadott helyet.</li><li>Ez a CLR-beli keresési sorrend módosítható a hookokkal, például a típus továbbítási mechanizmusával és a alkalmazástartomány. TypeResolve eseménysel.</li></ol><p>Ha egy támadó kihasználja a CLR keresési sorrendjét úgy, hogy létrehoz egy azonos nevű alternatív osztályt, és azt egy másik helyen helyezi el, amelyet a CLR betölt, akkor a CLR véletlenül nem fogja végrehajtani a támadó által megadott kódot.</p>|

### <a name="example"></a>Példa
Az alábbi WCF konfigurációs fájl elemearrautasítjaaWCF-t,hogyadjonhozzáegyegyéniviselkedésiosztálytegyadottWCF-bővítményhez.`<behaviorExtensions/>`
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
A teljesen minősített (erős) nevek egyedileg azonosítanak egy típust, és növelik a rendszer biztonságát. A Machine. config és az app. config fájlban található típusok regisztrálása esetén használjon teljesen minősített szerelvény-neveket.

### <a name="example"></a>Példa
Az alábbi WCF konfigurációs fájl elemearrautasítjaaWCF-t,hogyegyadottWCF-bővítményhezerősenhivatkozottegyéniviselkedésiosztálytadjonhozzá.`<behaviorExtensions/>`
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a id="wcf-authz"></a>WCF – engedélyezési vezérlő implementálása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [megerősítő Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Lépések** | <p>Ez a szolgáltatás nem használ engedélyezési vezérlőt. Amikor egy ügyfél egy adott WCF szolgáltatást hív meg, a WCF különböző engedélyezési sémákat biztosít, amelyek azt ellenőrzik, hogy a hívó jogosult-e a szolgáltatási módszer végrehajtására a kiszolgálón. Ha az engedélyezési vezérlők nincsenek engedélyezve a WCF-szolgáltatásokhoz, a hitelesített felhasználó elérheti a jogosultságok eszkalációját.</p>|

### <a name="example"></a>Példa
A következő konfiguráció arra utasítja a WCF-t, hogy ne vizsgálja meg az ügyfél engedélyezési szintjét a szolgáltatás végrehajtásakor:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Egy szolgáltatás-engedélyezési séma segítségével ellenőrizze, hogy a szolgáltatási módszer hívója jogosult-e erre. A WCF két módot biztosít, és lehetővé teszi egy egyéni engedélyezési séma definícióját. A UseWindowsGroups mód Windows-szerepköröket és-felhasználókat használ, a UseAspNetRoles mód pedig egy ASP.NET szerepkör-szolgáltatót használ (például SQL Server) a hitelesítéshez.

### <a name="example"></a>Példa
A következő konfiguráció arra utasítja a WCF-t, hogy a szolgáltatás hozzáadása előtt ellenőrizze, hogy az ügyfél a rendszergazdák csoport tagja-e:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
A szolgáltatás ezután a következőként van deklarálva:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a id="authz-aspnet"></a>Megfelelő engedélyezési mechanizmus implementálása a ASP.NET web API-ban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános, MVC5 |
| **Attribútumok**              | N/A, identitás-szolgáltató – ADFS, identitás-szolgáltató – Azure AD |
| **Hivatkozik**              | [Hitelesítés és engedélyezés a ASP.NET web API-ban](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **Lépések** | <p>Az alkalmazás felhasználói szerepkör-információi az Azure AD-ből vagy ADFS-jogcímekből származhatnak, ha az alkalmazás identitás-szolgáltatóként hivatkozik rájuk, vagy maga az alkalmazás is megadhatja azt. Ezekben az esetekben az egyéni engedélyezési implementációnak ellenőriznie kell a felhasználói szerepkör információit.</p><p>Az alkalmazás felhasználói szerepkör-információi az Azure AD-ből vagy ADFS-jogcímekből származhatnak, ha az alkalmazás identitás-szolgáltatóként hivatkozik rájuk, vagy maga az alkalmazás is megadhatja azt. Ezekben az esetekben az egyéni engedélyezési implementációnak ellenőriznie kell a felhasználói szerepkör információit.</p>

### <a name="example"></a>Példa
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
Az összes védeni kívánt vezérlőt és műveleti módszert a fenti attribútummal kell megdíszíteni.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a id="device-permission"></a>Engedélyezési ellenőrzések végrehajtása az eszközön, ha a különböző jogosultsági szinteket igénylő műveleteket támogat

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | <p>Az eszköznek engedélyeznie kell a hívót annak ellenőrzéséhez, hogy a hívó rendelkezik-e a kért művelet végrehajtásához szükséges engedélyekkel. Például tegyük fel, hogy az eszköz egy olyan intelligens ajtózár, amely a felhőből figyelhető, valamint olyan funkciókat biztosít, mint például az ajtó távoli zárolása.</p><p>Az intelligens ajtó zárolása csak akkor teszi lehetővé a funkció zárolását, ha valaki fizikailag az ajtó közelében van egy kártyával. Ebben az esetben a távoli parancs és a vezérlő megvalósítását úgy kell végrehajtani, hogy az ne biztosítson semmilyen funkciót az ajtó zárolásának feloldásához, mert a Felhőbeli átjáró nem rendelkezik jogosultsággal az ajtó feloldására szolgáló parancs küldésére.</p>|

## <a id="field-permission"></a>Ha olyan műveleteket támogat, amelyek különböző jogosultsági szinteket igényelnek, hajtsa végre az engedélyezési ellenőrzéseket a helyszíni átjárón.

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-mező átjárója | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | A helyszíni átjárónak engedélyeznie kell a hívónak, hogy ellenőrizze, hogy a hívó rendelkezik-e a kért művelet végrehajtásához szükséges engedélyekkel. Ilyen például, ha egy rendszergazdai felhasználói felület/API számára eltérő engedélyekkel kell rendelkeznie ahhoz, hogy az átjáróhoz csatlakozó v/s eszközöket konfigurálja.|
