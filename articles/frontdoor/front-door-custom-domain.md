---
title: Oktatóanyag – egyéni tartomány hozzáadása az Azure-beli bejárati ajtó konfigurációjához
description: Ebből az oktatóanyagból megtudhatja, hogyan regisztrálható egyéni tartomány az Azure Front Door szolgáltatásban.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: fb9e369bbba72cd3a1dd7fcc864e2845e3a979e9
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184624"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>Oktatóanyag: Egyéni tartomány hozzáadása a Front Doorhoz
Ebből az oktatóanyagból elsajátíthatja, hogyan adhat hozzá egyéni tartományt a Front Doorhoz. Amikor az Azure Front Door Service-t használja az alkalmazások szállítására, és azt szeretné, hogy a saját tartományneve látható legyen a végfelhasználói kérelemben, akkor egy egyéni tartománynévre van szükség. A látható tartománynév hasznos lehet az ügyfelei számára, és a vállalati arculat szempontjából is.

A Front Door létrehozása után az alapértelmezett előtérbeli gazdagép, amely az `azurefd.net` altartománya, alapértelmezés szerint belekerül az URL-címbe a Front Door-tartalom háttéralkalmazásból való szállításához (például https:\//contoso.azurefd.net/activeusers.htm). A kényelmes használat érdekében az Azure Front Door lehetőséget ad arra, hogy egyéni tartományt társítson az alapértelmezett gazdagéphez. Ezzel a lehetőséggel a tartalom továbbításakor az egyéni tartomány neve lesz az URL-címben a Front Door tulajdonában lévő tartománynév helyett (például https:\//www.contoso.com/photo.png). 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - CNAME DNS-rekord létrehozása.
> - Az egyéni tartomány társítása a Front Doorral.
> - Az egyéni tartomány ellenőrzése.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elvégezhetné a jelen oktatóanyag lépéseit, először létre kell hoznia egy Front Doort. További információkért lásd a [Rövid útmutató: Front Door létrehozása](quickstart-create-front-door.md) szakaszt.

Ha nem rendelkezik egyéni tartománnyal, először vásároljon egyet egy tartományszolgáltatótól. Példákért lásd az [egyéni tartománynév vásárlásáról](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain) szóló részt.

Ha az Azure-t használja a [DNS-tartományai](https://docs.microsoft.com/azure/dns/dns-overview) üzemeltetésére, delegálnia kell a tartományszolgáltató tartománynévrendszerét (DNS-ét) egy Azure DNS-re. További információ: [Delegate a domain to Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) (Tartomány delegálása az Azure DNS-be). Egyéb esetben, ha tartományszolgáltatót használ a DNS-tartománya kezeléséhez, lépjen tovább a [CNAME DNS-rekord létrehozása](#create-a-cname-dns-record) szakaszra.


## <a name="create-a-cname-dns-record"></a>CNAME DNS-rekord létrehozása

Ahhoz, hogy egyéni tartományt lehessen használni a bejárati ajtóhoz, először létre kell hoznia egy kanonikus név (CNAME) rekordot a tartományi szolgáltatóval, hogy az a bejárati ajtó alapértelmezett előtér-gazdagépére mutasson (például contoso.azurefd.net). A CNAME rekord egy olyan DNS-rekordtípus, amellyel egy forrástartománynév leképezhető egy céltartománynévre. Az Azure Front Door Service esetében a forrástartománynév az Ön egyéni tartományneve, a céltartománynév pedig a Front Door alapértelmezett gazdagépneve. Miután a bejárati ajtó ellenőrizte a létrehozott CNAME-rekordot, a forrásként szolgáló egyéni tartományhoz (például a www\.contoso.com) irányuló forgalmat a rendszer átirányítja a megadott célhelyre az alapértelmezett előtér-gazdagéphez (például contoso.azurefd.net). 

Egy egyéni tartomány és annak altartománya egyszerre csak egy Front Doorral társítható. Azonban lehetséges egy egyéni tartomány több altartományának használata különböző Front Doorokhoz több CNAME rekord használatával. Emellett leképezhet egy különböző altartományokkal rendelkező egyéni tartományt is ugyanarra a Front Doorra.


## <a name="map-the-temporary-afdverify-sub-domain"></a>Az ideiglenes afdverify altartomány leképezése

Amikor meglévő, éles környezetben futó tartományt képez le, speciális szempontokat kell figyelembe vennie. Amikor regisztrálja az egyéni tartományát az Azure Portalon, rövid kimaradás fordulhat elő a tartományon. A webes forgalom megszakításának elkerüléséhez először képezze le az egyéni tartományt a Front Door alapértelmezett előtérbeli gazdagépére az Azure afdverify altartománnyal egy ideiglenes CNAME-leképezés létrehozásához. Ezzel a módszerrel a felhasználók megszakítás nélkül férhetnek hozzá a tartományhoz a DNS-hozzárendelés közben.

Egyéb esetben, ha először használja az egyéni tartományát, és nem fut rajta éles forgalom, a tartományt leképezheti közvetlenül a Front Doorra. Lépjen tovább [az állandó egyéni tartomány leképezését](#map-the-permanent-custom-domain) bemutató cikkre.

CNAME rekord létrehozása az afdverify altartománnyal:

1. Jelentkezzen be az egyéni tartomány tartományszolgáltatójának webhelyére.

2. Keresse meg a DNS-rekordok kezelésére szolgáló oldal helyét a szolgáltató dokumentációjának segítségével, vagy a webhely **Tartománynév**, **DNS** vagy **Névkiszolgáló kezelése** területeinek áttekintésével. 

3. Hozzon létre egy CNAME-rekordbejegyzést az egyéni tartományához, és töltse ki a mezőket a következő táblázatban látható módon (a mezők nevei eltérhetnek):

    | Forrás                    | Típus  | Cél                     |
    |---------------------------|-------|---------------------------------|
    | afdverify.www.contoso.com | CNAME | afdverify.contoso.azurefd.net |

    - Forrás: Adja meg az egyéni tartománya nevét az afdverify altartománnyal együtt, a következő formátumban: afdverify. _&lt;egyéni tartomány neve&gt;_ . Például: afdverify.www.contoso.com.

    - Típus: Írja be a *CNAME* szöveget.

    - Cél: Adja meg az alapértelmezett Front Door előtérbeli gazdagépet az afdverify altartománnyal együtt a következő formátumban: afdverify. _&lt;végpontnév&gt;_ .azurefd.net. Például: afdverify.contoso.azurefd.net.

4. Mentse a módosításokat.

Példaképp a GoDaddy tartományregisztráló eljárása a következő:

1. Jelentkezzen be, és válassza ki a használni kívánt egyéni tartományt.

2. A Tartományok szakaszban válassza az **Összes kezelése**, majd a **DNS** | **Zónák kezelése** lehetőséget.

3. A **Tartománynév** mezőben adja meg az egyéni tartomány nevét, majd válassza a **Keresés** lehetőséget.

4. A **DNS-kezelés** lapon válassza a **Hozzáadás**, lehetőséget, majd válassza a **CNAME** elemet a **Típus** listából.

5. Töltse ki a következő mezőket a CNAME bejegyzéshez:

    - Típus: Hagyja kijelölve a *CNAME* elemet.

    - Gazdagép: Adja meg az egyéni tartománya altartományát az afdverify altartomány nevével együtt. Például: afdverify.www.

    - Erre mutat: Adja meg az alapértelmezett előtérbeli Front Door-gazdagépet az afdverify altartománnyal együtt. Például: afdverify.contoso.azurefd.net. 

    - Élettartam: Hagyja kiválasztva az *1 óra* lehetőséget.

6. Kattintson a **Mentés** gombra.
 
    A CNAME bejegyzést a rendszer a DNS-rekordok táblázatához adja.


## <a name="associate-the-custom-domain-with-your-front-door"></a>Az egyéni tartomány társítása a Front Doorral

Miután regisztrálta az egyéni tartományát, hozzáadhatja azt a Front Doorhoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és navigáljon arra a Front Doorra, amely tartalmazza azt az előtérbeli gazdagépet, amelyet az egyéni tartományhoz kíván leképezni.
    
2. A **Front Door-tervező** lapon kattintson a „+” jelre egyéni tartomány hozzáadásához.
    
3. Adjon meg az **Egyéni tartomány** értékét. 

4. Az **Előtérbeli gazdagép** mezőben előre ki van töltve a CNAME rekordhoz céltartományként használt előtérbeli gazdagép neve, amely a Front Doorból van származtatva: *&lt;alapértelmezett gazdagépnév&gt;* .azurefd.net. A név nem módosítható.

5. Az **Egyéni gazdagépnév** mezőben adja meg az egyéni tartomány nevét az altartomány nevével együtt, amelyet a CNAME rekord forrástartományaként fog használni. Például: www\.contoso.com vagy cdn.contoso.com. Ne használja az afdverify altartománynevet.

6. Válassza a **Hozzáadás** lehetőséget.

   Az Azure ellenőrzi, hogy a megadott egyéni tartománynév esetében létezik-e a CNAME rekord. Ha a CNAME helyes, az egyéni tartomány érvényesítve lesz.

>[!WARNING]
> **Kötelező** gondoskodnia róla, hogy a Front Doorban az összes előtérbeli gazdagép (beleértve az egyéni tartományokat) rendelkezzen útválasztási szabállyal, hozzá társított alapértelmezett elérési úttal („/\*”). Vagyis minden egyes előtérbeli gazdagépnek rendelkeznie kell legalább egy olyan útválasztási szabállyal, amely az alapértelmezett útvonalat (\*) használja. Ha ez a feltétel nem teljesül, akkor a végfelhasználói forgalom útvonalválasztása nem lesz megfelelő.

## <a name="verify-the-custom-domain"></a>Az egyéni tartomány ellenőrzése

Az egyéni tartomány regisztrálása után ellenőrizze, hogy az egyéni tartomány az alapértelmezett előtérbeli Front Door-gazdagépre hivatkozik-e.
 
A böngészőjében navigáljon a fájl címére az egyéni tartomány használatával. Ha például az egyéni tartománya a robotics.contoso.com, a gyorsítótárazott fájl URL-címe a következő URL-címre fog hasonlítani: http:\//robotics.contoso.com/my-public-container/my-file.jpg. Ellenőrizze, hogy az eredmény ugyanaz-e, mint amikor a Front Doort közvetlenül a *&lt;Front Door gazdagépneve&gt;* .azurefd.net címen éri el.


## <a name="map-the-permanent-custom-domain"></a>Az állandó egyéni tartomány leképezése

Ha ellenőrizte, hogy az adfverify altartomány sikeresen le lett képezve a Front Doorra (vagy ha új egyéni tartományt használ, amely nem fut éles környezetben), akkor leképezheti az egyéni tartományt közvetlenül az alapértelmezett előtérbeli Front Door-gazdagépre.

CNAME rekord létrehozása az egyéni tartományhoz:

1. Jelentkezzen be az egyéni tartomány tartományszolgáltatójának webhelyére.

2. Keresse meg a DNS-rekordok kezelésére szolgáló oldal helyét a szolgáltató dokumentációjának segítségével, vagy a webhely **Tartománynév**, **DNS** vagy **Névkiszolgáló kezelése** területeinek áttekintésével. 

3. Hozzon létre egy CNAME-rekordbejegyzést az egyéni tartományához, és töltse ki a mezőket a következő táblázatban látható módon (a mezők nevei eltérhetnek):

    | Forrás          | Típus  | Cél           |
    |-----------------|-------|-----------------------|
    | < a www. contoso. com > | CNAME | contoso.azurefd.net |

   - Forrás: adja meg az egyéni tartománynevet (például: www\.contoso.com).

   - Típus: Írja be a *CNAME* szöveget.

   - Cél: Adja meg az alapértelmezett előtérbeli Front Door-gazdagépnevet. Ennek az alábbi formátumúnak kell lennie: _&lt;gazdagépnév&gt;_ .azurefd.net. Például: contoso.azurefd.net.

4. Mentse a módosításokat.

5. Ha korábban létrehozott egy ideiglenes afdverify altartományhoz tartozó CNAME rekordot, törölje azt. 

6. Ha éles környezetben most először használja az egyéni tartományt, kövesse [Az egyéni tartomány hozzárendelése a Front Doorhoz](#associate-the-custom-domain-with-your-front-door) és [Az egyéni tartomány ellenőrzése](#verify-the-custom-domain) szakaszok lépéseit.

Példaképp a GoDaddy tartományregisztráló eljárása a következő:

1. Jelentkezzen be, és válassza ki a használni kívánt egyéni tartományt.

2. A Tartományok szakaszban válassza az **Összes kezelése**, majd a **DNS** | **Zónák kezelése** lehetőséget.

3. A **Tartománynév** mezőben adja meg az egyéni tartomány nevét, majd válassza a **Keresés** lehetőséget.

4. A **DNS-kezelés** lapon válassza a **Hozzáadás**, lehetőséget, majd válassza a **CNAME** elemet a **Típus** listából.

5. Töltse ki a CNAME bejegyzés mezőit:

    - Típus: Hagyja kijelölve a *CNAME* elemet.

    - Gazdagép: Adja meg a használandó egyéni tartománya altartományát. Például: www vagy profile.

    - A következő helyre mutat: Adja meg a Front Door alapértelmezett gazdagépnevét. Például: contoso.azurefd.net. 

    - Élettartam: Hagyja kiválasztva az *1 óra* lehetőséget.

6. Kattintson a **Mentés** gombra.
 
    A CNAME bejegyzést a rendszer a DNS-rekordok táblázatához adja.

7. Ha rendelkezik adfverify CNAME rekorddal, kattintson a mellette található ceruza ikonra, majd válassza a kuka ikont.

8. A **Törlés** gombra kattintva törölje a CNAME rekordot.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben hozzáadott egy egyéni tartományt egy Front Doorhoz. Ha már nem szeretné egyéni tartománnyal társítani a Front Doort, az alábbi lépésekkel eltávolíthatja az egyéni tartományt:
 
1. A Front Door-tervezőben válassza ki azt az egyéni tartományt, amelyet el szeretne távolítani.

2. Kattintson a Törlés elemre a helyi menüben az egyéni tartománynál.  

   Az egyéni tartomány társítása a végponttal ekkor megszűnik.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - CNAME DNS-rekord létrehozása.
> - Az egyéni tartomány társítása a Front Doorral.
> - Az egyéni tartomány ellenőrzése.