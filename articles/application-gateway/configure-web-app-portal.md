---
title: Több-bérlős alkalmazások forgalmának kezelése a portál használatával
titleSuffix: Azure Application Gateway
description: Ez a cikk útmutatást nyújt ahhoz, hogyan konfigurálhatja az Azure app Service Web Apps-t a háttér-készlet tagjaiként egy meglévő vagy egy új Application Gateway-átjárón.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075162"
---
# <a name="configure-app-service-with-application-gateway"></a>App Service konfigurálása Application Gateway

Mivel az App Service egy több-bérlős szolgáltatás, amely a központi telepítés helyett a gazdagép fejlécét használja a bejövő kérelemben a megfelelő app Service-végpontra irányuló kérés feloldásához. Az alkalmazás DNS-neve általában az App Service-hez kapcsolódó Application Gateway-hez társított DNS-név, amely eltér a háttér-alkalmazás szolgáltatás tartománynevétől. Ezért az Application Gateway által fogadott eredeti kérelemben szereplő állomásfejléc nem egyezik meg a háttér-szolgáltatás állomásneve nevével. Emiatt, ha az Application Gateway-ből a háttérbe irányuló kérelemben szereplő állomásfejléc nem változik a háttérrendszer állomásneve, a több-bérlős háttérrendszer nem tudja feloldani a kérést a megfelelő végpontra.

A Application Gateway egy `Pick host name from backend address` nevű kapcsolót biztosít, amely felülbírálja a kérelemben szereplő állomásfejléc-fejlécet, ha a kérést a Application Gateway a háttérbe irányítja át. Ez a funkció lehetővé teszi a több-bérlős back-végpontok támogatását, például az Azure app Service és az API Management használatát. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> - Háttérbeli készlet létrehozása és App Service hozzáadása
> - HTTP-beállítások és egyéni mintavétel létrehozása a "pick hostname" kapcsolók engedélyezésével

## <a name="prerequisites"></a>Előfeltételek

- Application Gateway: Ha nem rendelkezik meglévő Application Gateway-átjáróval, tekintse meg [az Application Gateway létrehozása](https://docs.microsoft.com/azure/application-gateway/quick-create-portal) című témakört.
- App Service: Ha nem rendelkezik meglévő app Service-szolgáltatással, tekintse meg az [app Service dokumentációját](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>App Service hozzáadása háttér-készletként

1. A Azure Portal nyissa meg az Application Gateway konfigurációs nézetét.

2. A **háttér-készletek**területen kattintson a **Hozzáadás** elemre egy új háttér-készlet létrehozásához.

3. Adjon meg egy megfelelő nevet a háttér-készletnek. 

4. A **célok**területen kattintson a legördülő listára, és válassza a **app Services** lehetőséget.

5. Megjelenik egy legördülő lista, amely közvetlenül a **célok** legördülő listájában jelenik meg, amely tartalmazni fogja a app Services listáját. Ebből a legördülő listából válassza ki a háttérbeli készlet tagjaként hozzáadni kívánt App Service, majd kattintson a Hozzáadás gombra.

   ![App Service-háttérrendszer](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > A legördülő lista csak azokat az alkalmazás-szolgáltatásokat tölti fel, amelyek ugyanabban az előfizetésben vannak, mint a Application Gateway. Ha olyan app Service-t szeretne használni, amely egy másik előfizetésben található, mint a Application Gateway, **app Services** akkor a **célok** legördülő menüben válassza az **IP-cím vagy állomásnév** lehetőséget, és adja meg a hostname (példa. azurewebsites.net) az App Service-ben.

## <a name="create-http-settings-for-app-service"></a>Az App Service HTTP-beállításainak létrehozása

1. A **http-beállítások**területen kattintson a **Hozzáadás** ELEMre egy új http-beállítás létrehozásához.

2. Adja meg a HTTP-beállítás nevét, és a követelménynek megfelelően engedélyezheti vagy letilthatja a cookie-alapú affinitást.

3. Használati esetként válassza a protokollt HTTP-ként vagy HTTPS-ként. 

   > [!NOTE]
   > Ha a HTTPS lehetőséget választja, nem kell feltöltenie semmilyen hitelesítési tanúsítványt vagy megbízható főtanúsítványt az App Service-háttér engedélyezési listájára, mivel az App Service egy megbízható Azure-szolgáltatás.

4. Jelölje be a jelölőnégyzetet a **app Service használatához** . Vegye figyelembe, hogy a kapcsolók `Create a probe with pick host name from backend address` és `Pick host name from backend address` automatikusan engedélyezve lesznek.`Pick host name from backend address` felülírja a kérelemben szereplő állomásfejléc-fejlécet, ha a kérést a Application Gateway a háttérbe irányítja a rendszer.  

   `Create a probe with pick host name from backend address` automatikusan létrehoz egy állapot-mintavételt, és hozzárendeli azt ehhez a HTTP-beállításhoz. Ehhez a HTTP-beállításhoz nem kell más állapot-mintavételt létrehoznia. Azt is megteheti, hogy új, <HTTP Setting name><Unique GUID> nevű mintavétel lett hozzáadva az állapot-mintavételek listájában, és már rendelkezik a kapcsoló `Pick host name from backend http settings enabled`.

   Ha már rendelkezik egy vagy több olyan HTTP-beállítással, amelyet az App Service használ, és ha ezek a HTTP-beállítások ugyanazt a protokollt használják, mint amit a létrehozott, akkor a `Create a probe with pick host name from backend address` kapcsoló helyett a legördülő listából választhatja ki az egyik egyéni mintavételt. Ennek az az oka, hogy mivel már létezik egy HTTP-beállítás az App Service-szel, ezért létezik olyan állapot-mintavétel is, amelynek `Pick host name from backend http settings enabled` a kapcsolója. Válassza ki az egyéni mintavételt a legördülő listából.

5. A HTTP-beállítás létrehozásához kattintson **az OK** gombra.

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Szabály létrehozása a figyelő, a háttér-készlet és a HTTP-beállítás összekötéséhez

1. A **szabályok**területen kattintson az **alapszintű** elemre egy új alapszabály létrehozásához.

2. Adjon meg egy megfelelő nevet, és válassza ki azt a figyelőt, amely elfogadja az App Service-be érkező kéréseket.

3. A **háttérbeli készlet** legördülő menüben válassza ki a fent létrehozott háttér-készletet.

4. A **http-beállítások** legördülő menüben válassza ki a fent létrehozott http-beállítást.

5. A szabály mentéséhez kattintson **az OK** gombra.

   ![Szabály](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>További konfiguráció az App Service relatív elérési útjára történő átirányítás esetén

Amikor az App Service átirányítási választ küld az ügyfélnek, hogy átirányítsa a relatív elérési útjára (például egy átirányítást a contoso.azurewebsites.net/path1-ből a contoso.azurewebsites.net/path2-be), ugyanazt a gazdagépet használja a válaszának Location fejlécében, mint az Application gatewaytől kapott kérelemben szereplőnek. Így az ügyfél közvetlenül a contoso.azurewebsites.net/path2 küldi a kérést az Application Gateway (contoso.com/path2) helyett. Az Application Gateway megkerülése nem kívánatos.

Ha használja a használati esetet, vannak olyan helyzetek, amikor az App Service-nek át kell küldenie egy átirányítási választ az ügyfélnek, a [további lépéseket végrehajtva írja felül a hely fejlécét](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration).

## <a name="restrict-access"></a>Hozzáférés korlátozása

A példákban telepített webalkalmazások nyilvános IP-címeket használnak, amelyek közvetlenül az internetről érhetők el. Ez segít a hibaelhárításban az új funkciók megismerése és új dolgok kipróbálása során. Ha azonban éles környezetben szeretne üzembe helyezni egy szolgáltatást, további korlátozásokat is fel szeretne venni.

A webalkalmazásokhoz való hozzáférés korlátozásának egyik módja a [Azure app Service statikus IP-korlátozások](../app-service/app-service-ip-restrictions.md)használata. Például korlátozhatja a webalkalmazást úgy, hogy az csak az Application gatewaytől kapjon forgalmat. Az App Service IP-korlátozási funkciója segítségével az Application Gateway VIP-t csak az elérési címmel listázhatja.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az App Service-ről és az Application Gateway további több-bérlős támogatásáról, tekintse meg a [több-bérlős szolgáltatás támogatása az Application gatewayrel](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)című témakört.
