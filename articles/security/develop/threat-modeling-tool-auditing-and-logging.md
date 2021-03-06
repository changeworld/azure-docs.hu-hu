---
title: Naplózás és naplózás – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
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
ms.openlocfilehash: c9d20b3259cf4ea7af263d5e31145ad372db0c77
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728414"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Biztonsági keret: Naplózás és naplózás | Enyhítését 

| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Bizalmas entitások azonosítása a megoldásban és a változások naplózásának megvalósítása](#sensitive-entities)</li></ul> |
| **Webalkalmazás** | <ul><li>[Győződjön meg arról, hogy a naplózás és a naplózás érvényesítve van az alkalmazásban](#auditing)</li><li>[Győződjön meg arról, hogy a naplók elforgatása és elkülönítése folyamatban van](#log-rotation)</li><li>[Győződjön meg arról, hogy az alkalmazás nem naplózza a bizalmas felhasználói adatokat](#log-sensitive-data)</li><li>[Győződjön meg arról, hogy a naplózási és a naplófájlok korlátozott hozzáféréssel rendelkeznek](#log-restricted-access)</li><li>[Ellenőrizze, hogy a rendszer naplózza-e a felhasználói felügyeleti eseményeket](#user-management)</li><li>[Győződjön meg arról, hogy a rendszer beépített védelmet használ a visszaélések ellen](#inbuilt-defenses)</li><li>[Diagnosztikai naplózás engedélyezése webalkalmazásokhoz Azure App Service](#diagnostics-logging)</li></ul> |
| **Adatbázis** | <ul><li>[Győződjön meg arról, hogy a Bejelentkezés naplózása engedélyezve van SQL Server](#identify-sensitive-entities)</li><li>[Veszélyforrások észlelésének engedélyezése az Azure SQL-ben](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Az Azure Storage hozzáférésének naplózása Azure Storage Analytics használatával](#analytics)</li></ul> |
| **WCF** | <ul><li>[Megfelelő naplózás implementálása](#sufficient-logging)</li><li>[Megfelelő naplózási hibák kezelésének megvalósítása](#audit-failure-handling)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy a naplózás és a naplózás érvényesítve van a webes API-ban](#logging-web-api)</li></ul> |
| **IoT-mező átjárója** | <ul><li>[Győződjön meg arról, hogy a megfelelő naplózás és naplózás érvényesítve van a Field Gateway-ben](#logging-field-gateway)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Győződjön meg arról, hogy a felhőalapú átjárón a megfelelő naplózás és naplózás kényszerítve van](#logging-cloud-gateway)</li></ul> |

## <a id="sensitive-entities"></a>Bizalmas entitások azonosítása a megoldásban és a változások naplózásának megvalósítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Dynamics CRM | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések**                   | Azonosítsa a bizalmas adatokat tartalmazó megoldás entitásait, és hajtsa végre a változások naplózását ezeken az entitásokon és mezőkön |

## <a id="auditing"></a>Győződjön meg arról, hogy a naplózás és a naplózás érvényesítve van az alkalmazásban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések**                   | Az összes összetevő naplózásának és naplózásának engedélyezése. A naplóknak rögzíteniük kell a felhasználói környezetet. Azonosítsa az összes fontos eseményt, és naplózza ezeket az eseményeket. Központosított naplózás implementálása |

## <a id="log-rotation"></a>Győződjön meg arról, hogy a naplók elforgatása és elkülönítése folyamatban van

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések**                   | <p>A napló elforgatása a rendszerfelügyeletben használt automatizált folyamat, amelyben a rendszer archiválja a elavult naplófájlokat. A nagyméretű alkalmazásokat futtató kiszolgálók gyakran naplózzák az összes kérelmet: a tömeges naplók szem előtt tartásával korlátozható a naplók teljes mérete, miközben továbbra is engedélyezi a legutóbbi események elemzését. </p><p>A napló elkülönítése alapvetően azt jelenti, hogy a naplófájlokat egy másik partíción kell tárolnia, ahol az operációs rendszer/alkalmazás fut, hogy elhárítsa a szolgáltatásmegtagadási támadásokat vagy az alkalmazás teljesítményét</p>|

## <a id="log-sensitive-data"></a>Győződjön meg arról, hogy az alkalmazás nem naplózza a bizalmas felhasználói adatokat

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések**                   | <p>Győződjön meg arról, hogy nem naplózza a felhasználó által a webhelynek küldött bizalmas adatokat. Keressen szándékos naplózást, valamint a tervezési problémák által okozott mellékhatásokat. A bizalmas adatokra például a következők tartoznak:</p><ul><li>Felhasználó hitelesítő adatai</li><li>Társadalombiztosítási szám vagy egyéb azonosítási információ</li><li>Hitelkártya-számok vagy egyéb pénzügyi információk</li><li>Állapotadatok</li><li>Titkos kulcsok vagy egyéb adatok, amelyek a titkosított adatok visszafejtésére használhatók</li><li>Rendszer-vagy alkalmazás-információk, amelyek az alkalmazás hatékonyabb támadásához használhatók</li></ul>|

## <a id="log-restricted-access"></a>Győződjön meg arról, hogy a naplózási és a naplófájlok korlátozott hozzáféréssel rendelkeznek

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések**                   | <p>Győződjön meg arról, hogy a naplófájlokhoz való hozzáférési jogosultságok megfelelőek-e. Az alkalmazásadatok csak írási hozzáféréssel rendelkeznek, és a támogatási személyzetnek szükség esetén csak olvasási hozzáféréssel kell rendelkeznie.</p><p>A rendszergazdák fiókjai csak a teljes hozzáféréssel rendelkező fiókok. Ellenőrizze, hogy a Windows ACL a naplófájlokban legyen-e megfelelően korlátozva:</p><ul><li>Az alkalmazás fiókjai csak írási hozzáféréssel rendelkezhetnek</li><li>A kezelőknek és a támogatási személyzetnek szükség esetén csak olvasási hozzáféréssel kell rendelkeznie</li><li>A rendszergazdák csak a teljes hozzáféréssel rendelkező fiókok.</li></ul>|

## <a id="user-management"></a>Ellenőrizze, hogy a rendszer naplózza-e a felhasználói felügyeleti eseményeket

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések**                   | <p>Győződjön meg arról, hogy az alkalmazás figyeli a felhasználói felügyeleti eseményeket, például a sikeres és sikertelen felhasználói bejelentkezéseket, a jelszó alaphelyzetbe állítását, a jelszó módosításait, a fiók zárolását, a felhasználói regisztrációt Ez segít a vélhetően gyanús viselkedés észlelésében és reagálásában. Lehetővé teszi az operatív adatok összegyűjtését is; például nyomon követheti, hogy ki fér hozzá az alkalmazáshoz</p>|

## <a id="inbuilt-defenses"></a>Győződjön meg arról, hogy a rendszer beépített védelmet használ a visszaélések ellen

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések**                   | <p>A vezérlőket olyan helyen kell elhelyezni, amely biztonsági kivételt okoz az alkalmazások helytelen használata esetén. Ha például a bemeneti ellenőrzés van érvényben, és egy támadó olyan kártékony kódot próbál beszúrni, amely nem felel meg a regexnek, akkor biztonsági kivételt okozhat, ami a rendszer-visszaélésre utalhat.</p><p>Azt javasoljuk például, hogy a biztonsági kivételeket naplózza, és a következő problémákra tett lépéseket:</p><ul><li>Bemenet-ellenőrzés</li><li>CSRF megsértések</li><li>Találgatásos kényszerítés (a kérelmek felhasználónkénti száma felhasználónként)</li><li>Fájlfeltöltés megsértése</li><ul>|

## <a id="diagnostics-logging"></a>Diagnosztikai naplózás engedélyezése webalkalmazásokhoz Azure App Service

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | EnvironmentType – Azure |
| **Hivatkozik**              | –  |
| **Lépések** | <p>Az Azure a beépített diagnosztika révén segít az Azure App Service-ben működtetett webalkalmazások hibakeresésében. Az API-alkalmazásokra és a mobil alkalmazásokra is vonatkozik. App Service Web Apps diagnosztikai funkciókat biztosít a webkiszolgálóról és a webalkalmazásból származó adatok naplózásához.</p><p>Ezek logikailag elkülönítve vannak a webkiszolgáló-diagnosztika és az Application Diagnostics szolgáltatásban</p>|

## <a id="identify-sensitive-entities"></a>Győződjön meg arról, hogy a Bejelentkezés naplózása engedélyezve van SQL Server

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Bejelentkezési naplózás konfigurálása](https://msdn.microsoft.com/library/ms175850.aspx) |
| **Lépések** | <p>Az adatbázis-kiszolgáló bejelentkezési naplózását engedélyezni kell a jelszó-találgatásos támadások észleléséhez/megerősítéséhez. Fontos a sikertelen bejelentkezési kísérletek rögzítése. A sikeres és sikertelen bejelentkezési kísérletek rögzítése további előnyt nyújt a kriminalisztikai vizsgálatok során</p>|

## <a id="threat-detection"></a>Veszélyforrások észlelésének engedélyezése az Azure SQL-ben

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | SQL Azure |
| **Attribútumok**              | SQL-verzió – V12 |
| **Hivatkozik**              | [Ismerkedés a SQL Database fenyegetések észlelésével](https://azure.microsoft.com/documentation/articles/sql-database-threat-detection-get-started/)|
| **Lépések** |<p>A veszélyforrások észlelése rendellenes adatbázis-tevékenységeket észlel, ami az adatbázis potenciális biztonsági fenyegetéseket jelez. Egy új biztonsági réteget biztosít, amely lehetővé teszi az ügyfeleknek, hogy észlelje és reagáljon az esetleges fenyegetésekre a rendellenes tevékenységekre vonatkozó biztonsági riasztások révén.</p><p>A felhasználók megtekinthetik a gyanús eseményeket a Azure SQL Database naplózás használatával annak megállapításához, hogy a rendszer megpróbál-e hozzáférni az adatbázisban lévő adatokhoz, vagy illetéktelenül kihasználja őket.</p><p>A fenyegetések észlelése megkönnyíti a potenciális fenyegetések kezelését az adatbázisba anélkül, hogy biztonsági szakértőnek kellene lennie, vagy speciális biztonsági monitorozási rendszereket kellene kezelnie</p>|

## <a id="analytics"></a>Az Azure Storage hozzáférésének naplózása Azure Storage Analytics használatával

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | – |
| **Hivatkozik**              | [Storage Analytics használata az engedélyezési típus figyeléséhez](https://azure.microsoft.com/documentation/articles/storage-security-guide/#storage-analytics) |
| **Lépések** | <p>Minden egyes Storage-fiók esetében engedélyezhető Azure Storage Analytics a metrikák naplózásának és tárolásának elvégzéséhez. A Storage Analytics-naplók olyan fontos információkat biztosítanak, mint például a felhasználók által a tárolóhoz való hozzáféréshez használt hitelesítési módszer.</p><p>Ez akkor lehet hasznos, ha szorosan védelmet biztosít a tárolóhoz való hozzáféréshez. Blob Storage például beállíthatja az összes tárolót a magánjellegűre, és egy SAS-szolgáltatást alkalmazhat az alkalmazásokban. Ezután rendszeresen megtekintheti a naplókat, és megtekintheti, hogy a Blobok elérhetők-e a Storage-fiók kulcsainak használatával, ami a biztonság megsértését jelezheti, vagy ha a Blobok nyilvánosak, de nem.</p>|

## <a id="sufficient-logging"></a>Megfelelő naplózás implementálása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | .NET-keretrendszer |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [megerősítő Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Lépések** | <p>A biztonsági incidensek utáni megfelelő naplózási nyomvonal hiánya akadályozhatja a kriminalisztikai erőfeszítéseket. A Windows Communication Foundation (WCF) lehetőséget biztosít a sikeres és/vagy sikertelen hitelesítési kísérletek naplózására.</p><p>A sikertelen hitelesítési kísérletek naplózása figyelmeztetheti a rendszergazdákat a lehetséges találgatásos támadásokra. Hasonlóképpen, a sikeres hitelesítési események naplózása hasznos naplózási nyomvonalat biztosít a megbízható fiókok biztonsága esetén. A WCF szolgáltatás biztonsági naplózási funkciójának engedélyezése |

### <a name="example"></a>Példa
Az alábbi példa egy olyan konfigurációt mutat be, amelyen engedélyezve van a naplózás
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a id="audit-failure-handling"></a>Megfelelő naplózási hibák kezelésének megvalósítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | .NET-keretrendszer |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [megerősítő Királyság](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Lépések** | <p>A fejlesztett megoldás úgy van konfigurálva, hogy ne állítson elő kivételt, ha nem sikerül írni a naplóba. Ha a WCF úgy van konfigurálva, hogy ne dobjon kivételt, amikor nem tud írni egy naplóba, a program nem értesíti a hibát, és a kritikus biztonsági események naplózása nem történik meg.</p>|

### <a name="example"></a>Példa
Az alábbi WCF konfigurációs fájl elemearrautasítjaaWCF-t,hogyneértesítseazalkalmazást,amikoraWCFnemtudírniegynaplóba.`<behavior/>`
```
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Konfigurálja a WCF-t úgy, hogy értesítse a programot, amikor nem tud írni egy naplóba. A programnak alternatív értesítési sémával kell rendelkeznie, amely arra figyelmezteti a szervezetet, hogy a naplózási nyomvonalak nincsenek karbantartva. 

## <a id="logging-web-api"></a>Győződjön meg arról, hogy a naplózás és a naplózás érvényesítve van a webes API-ban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | A webes API-k naplózásának és naplózásának engedélyezése. A naplóknak rögzíteniük kell a felhasználói környezetet. Azonosítsa az összes fontos eseményt, és naplózza ezeket az eseményeket. Központosított naplózás implementálása |

## <a id="logging-field-gateway"></a>Győződjön meg arról, hogy a megfelelő naplózás és naplózás érvényesítve van a Field Gateway-ben

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-mező átjárója | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | <p>Ha több eszköz csatlakozik egy mező-átjáróhoz, győződjön meg arról, hogy az egyes eszközökhöz tartozó csatlakozási kísérletek és hitelesítési állapot (sikeres vagy sikertelen) naplózása és karbantartása megtörtént a helyszíni átjárón.</p><p>Olyan esetekben is, ahol a Field Gateway az egyes eszközök IoT Hub hitelesítő adatait tartja karban, győződjön meg arról, hogy a rendszer a naplózást a hitelesítő adatok lekérése után hajtja végre. Dolgozzon ki egy folyamatot, amely rendszeres időközönként feltölti a naplókat az Azure IoT Hub/Storage-ba a hosszú távú megőrzés érdekében.</p> |

## <a id="logging-cloud-gateway"></a>Győződjön meg arról, hogy a felhőalapú átjárón a megfelelő naplózás és naplózás kényszerítve van

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [A IoT Hub Operations monitoring bemutatása](https://azure.microsoft.com/documentation/articles/iot-hub-operations-monitoring/) |
| **Lépések** | <p>A IoT Hub műveletek monitorozásán keresztül összegyűjtött naplózási adatok gyűjtésének és tárolásának tervezése. Engedélyezze a következő figyelési kategóriákat:</p><ul><li>Eszköz-identitási műveletek</li><li>Eszközről a felhőbe irányuló kommunikáció</li><li>A felhőből az eszközre irányuló kommunikáció</li><li>Kapcsolatok</li><li>Fájlfeltöltések</li></ul>|