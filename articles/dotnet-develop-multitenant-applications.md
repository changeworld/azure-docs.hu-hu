---
title: Több-bérlős webalkalmazás mintája | Microsoft Docs
description: A több-bérlős webalkalmazások Azure-ban való megvalósítását ismertető építészeti áttekintéseket és tervezési mintákat talál.
services: ''
documentationcenter: .net
author: wadepickett
manager: wpickett
editor: ''
ms.assetid: 4f0281d2-1555-42b0-a99d-1222fade0b0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2015
ms.author: wpickett
ms.openlocfilehash: d3e267eab056589ed38c436620dd0db185291da1
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425901"
---
# <a name="multitenant-applications-in-azure"></a>Több-bérlős alkalmazások az Azure-ban
A több-bérlős alkalmazás olyan megosztott erőforrás, amely lehetővé teszi a "felhasználók számára a különböző bérlők számára", hogy megtekintsék az alkalmazást, mintha a tulajdonosa lenne. Egy tipikus forgatókönyv, amely egy több-bérlős alkalmazáshoz rendeli, a különböző bérlők által az alkalmazás összes felhasználója testreszabhatja a felhasználói élményt, de egyébként ugyanazok az alapvető üzleti követelmények. Többek között a nagyméretű több-bérlős alkalmazások például az Office 365, a Outlook.com és a visualstudio.com.

Az alkalmazás-szolgáltató szemszögéből a bérlős előnyei többnyire a működési és a költséghatékonysági előnyökkel kapcsolatosak. Az alkalmazás egyik verziója képes kielégíteni számos bérlő vagy ügyfél igényeit, így lehetővé teszi a rendszerfelügyeleti feladatok, például a figyelés, a teljesítmény finomhangolása, a szoftveres karbantartás és az adatok biztonsági mentéseinak összevonását.

A következő lista felsorolja a szolgáltatók szempontjából legjelentősebb célokat és követelményeket.

* **Kiépítés**: az alkalmazáshoz új bérlőket kell kiépíteni.  A nagy számú Bérlővel rendelkező több-bérlős alkalmazások esetében általában az önkiszolgáló kiépítés engedélyezésével kell automatizálni ezt a folyamatot.
* **Karbantartás**: képesnek kell lennie az alkalmazás frissítésére és más karbantartási feladatok végrehajtására, miközben több bérlő használja azt.
* **Figyelés**: a problémák azonosításához és a hibaelhárításhoz mindig képesnek kell lennie az alkalmazás figyelésére. Ez magában foglalja annak figyelését, hogy az egyes bérlők hogyan használják az alkalmazást.

A megfelelően megvalósított több-bérlős alkalmazás a következő előnyöket biztosítja a felhasználók számára.

* **Elkülönítés**: az egyes bérlők tevékenységei nem érintik az alkalmazás más bérlők általi használatát. A bérlők nem férhetnek hozzá egymáshoz. A bérlő úgy tűnik, mintha kizárólagos módon használják az alkalmazást.
* **Rendelkezésre állás**: az egyes bérlők azt szeretnék, hogy az alkalmazás folyamatosan elérhető legyen, például az SLA-ban meghatározott garanciákkal. A többi bérlő tevékenysége nem érintheti az alkalmazás rendelkezésre állását.
* **Méretezhetőség**: az alkalmazás az egyes bérlők igényei szerint méretezhető. Más bérlők jelenléte és műveletei nem érinthetik az alkalmazás teljesítményét.
* **Költségek**: a költségek alacsonyabbak, mint a dedikált, egybérlős alkalmazások futtatása, mivel a többszörös kiszolgálás lehetővé teszi az erőforrások megosztását.
* **Testreszabhatóság**. Az alkalmazás egyéni bérlők számára történő testreszabása különböző módokon, például funkciók hozzáadásával vagy eltávolításával, színek és logók módosításával, vagy akár saját kód vagy parancsfájl hozzáadásával.

Röviden, míg számos szempontot figyelembe kell vennie, hogy nagy mértékben skálázható szolgáltatást biztosítson, számos olyan célt és követelményt is igénybe vehetik, amelyek számos több-bérlős alkalmazás esetében közösek. Előfordulhat, hogy egyes helyzetekben nem relevánsak, és az egyes célok és követelmények fontossága eltérő lesz az egyes forgatókönyvekben. A több-bérlős alkalmazás szolgáltatója olyan célokat és követelményeket is tartalmaz, mint a bérlői célok és követelmények, a jövedelmezőség, a számlázás, a több szolgáltatási szint, a kiépítés, a karbantartási monitorozás és az automatizálás.

További információ a több-bérlős alkalmazások tervezési szempontjairól: [több-bérlős alkalmazás üzemeltetése az Azure-][Hosting a Multi-Tenant Application on Azure]ban. A több bérlős szoftverszolgáltatás (SaaS) típusú adatbázis-alkalmazások általános adatarchitektúra-mintázataival kapcsolatos információk: [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Az Azure számos funkciót kínál, amelyek lehetővé teszik a több-bérlős rendszer tervezésekor felmerülő problémák megoldását.

**Elkülönítés**

* Szegmensek webhelyeinek bérlői az SSL-kommunikációval vagy anélkül történő gazdagép-fejlécek
* Szegmens webhely bérlői lekérdezési paraméterek alapján
* Webszolgáltatások a feldolgozói szerepkörökben
  * Olyan feldolgozói szerepkörök, amelyek jellemzően egy alkalmazás hátterén lévő adatfeldolgozást dolgozzák fel.
  * Olyan webes szerepkörök, amelyek jellemzően az alkalmazások előtérben működnek.

**Storage**

Adatkezelés, például Azure SQL Database vagy Azure Storage-szolgáltatások, mint például a Table service, amely nagy mennyiségű strukturálatlan adat tárolására és a Blob servicere kínál szolgáltatásokat, amelyek nagy mennyiségű strukturálatlan szöveg tárolására szolgálnak. bináris adatok, például videó, hang és képek.

* Több-bérlős adatok biztonságossá tétele SQL Database bérlői SQL Server bejelentkezések esetében.
* Ha Azure-táblákat használ az alkalmazás-erőforrásokhoz a tároló szintű hozzáférési szabályzat megadásával, lehetősége van az engedélyek módosítására anélkül, hogy új URL-címet kellene kiadnia a közös hozzáférési aláírásokkal védett erőforrásokhoz.
* Az Azure Queues for Application Resources Azure Queues szolgáltatást általában a bérlők nevében történő feldolgozásra használják, de a kiépítés vagy a felügyelethez szükséges munka elosztására is felhasználhatók.
* Service Bus várólisták olyan alkalmazás-erőforrásokhoz, amelyek egy megosztott szolgáltatáshoz küldenek munkát, egyetlen várólistát is használhat, amelyben minden bérlői feladónak csak engedélyekkel kell rendelkeznie (az ACS-ből kiállított jogcímekből származtatva) a várólistára való leküldéshez, miközben csak a szolgáltatás fogadói rendelkeznek a több bérlőtől érkező adatok várólistából való lekérésének engedélyezése.

**Kapcsolatok és biztonsági szolgáltatások**

* Azure Service Bus, az alkalmazások közötti üzenetküldési infrastruktúra, amely lehetővé teszi, hogy az üzeneteket egy lazán összekapcsolt módon cserélje le a jobb méretezés és rugalmasság érdekében.

**Hálózati szolgáltatások**

Az Azure számos olyan hálózati szolgáltatást biztosít, amelyek támogatják a hitelesítést, és javítják az üzemeltetett alkalmazások kezelhetőségét. Ezek a szolgáltatások többek között a következők:

* Az Azure Virtual Network lehetővé teszi virtuális magánhálózatok (VPN-ek) kiépítését és kezelését az Azure-ban, valamint a helyszíni informatikai infrastruktúrával való biztonságos összekapcsolást.
* Virtual Network Traffic Manager lehetővé teszi a bejövő forgalom terheléselosztását több üzemeltetett Azure-szolgáltatás között, függetlenül attól, hogy azok ugyanabban az adatközpontban vagy a világ különböző pontjain futnak.
* Azure Active Directory (Azure AD) egy modern, REST-alapú szolgáltatás, amely Identitáskezelés és hozzáférés-vezérlési képességeket biztosít a felhőalapú alkalmazásokhoz. Az Azure AD for Application-erőforrások használatával egyszerűen hitelesítheti és engedélyezheti a felhasználók számára, hogy hozzáférjenek a webalkalmazásokhoz és szolgáltatásokhoz, miközben lehetővé teszi a hitelesítés és az engedélyezés funkcióinak kiszámítását a kódból.
* A Azure Service Bus biztonságos üzenetkezelési és adatáramlási képességet biztosít az elosztott és hibrid alkalmazások számára, például az Azure által üzemeltetett alkalmazások és a helyszíni alkalmazások és szolgáltatások közötti kommunikációt anélkül, hogy bonyolult tűzfalat és biztonságot kellene igénybe helyeznie infrastruktúrák. A Service Bus Relay használata az alkalmazás erőforrásaihoz a végpontként elérhetővé tett szolgáltatásokhoz való hozzáféréshez (például a rendszeren kívül, például a helyszínen), vagy lehet, hogy kifejezetten a bérlő számára kiépített szolgáltatások tartoznak hozzá (mivel bizalmas, bérlő-specifikus adatok áthaladnak egymás között).

**Erőforrások kiépítés**

Az Azure számos lehetőséget kínál új bérlők kiépítésére az alkalmazás számára. A nagy számú Bérlővel rendelkező több-bérlős alkalmazások esetében általában az önkiszolgáló kiépítés engedélyezésével kell automatizálni ezt a folyamatot.

* A feldolgozói szerepkörök lehetővé teszik a bérlői erőforrások kiépítését és kiépítését (például egy új bérlő regisztrálása vagy megszakítása), a mérési használat mérőszámait, valamint egy adott ütemtervet követő, illetve a fő teljesítmény küszöbértékének átlépését eredményező skála kezelését. mutatók. Ugyanez a szerepkör felhasználható a frissítések leküldésére és a megoldás verziófrissítésére is.
* Az Azure-Blobok használhatók számítási vagy előzetesen inicializált tárolási erőforrások kiépítésére az új bérlők számára, miközben hozzáférési szabályzatokat biztosítanak a számítási szolgáltatás csomagjainak, a VHD-lemezképeknek és más erőforrásoknak a biztosításához.
* A bérlők SQL Database erőforrásainak kiépítési lehetőségei a következők:
  
  * DDL parancsfájlokban vagy beágyazott erőforrásként a szerelvényeken belül.
  * SQL Server 2008 R2 DAC-csomagokat programozott módon helyeztek üzembe.
  * Másolás egy fő hivatkozási adatbázisból.
  * Adatbázisok importálásával és exportálásával új adatbázisok hozhatók létre egy fájlból.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: https://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716
