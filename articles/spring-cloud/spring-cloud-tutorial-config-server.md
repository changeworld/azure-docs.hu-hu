---
title: Oktatóanyag – a konfigurációs kiszolgáló példányának beállítása az Azure Spring Cloud-ban
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthat be egy Spring Cloud config Server-példányt az Azure Spring Cloud számára a Azure Portal
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/18/2019
ms.openlocfilehash: 5e0b5633a153583117cfe0d90ec5c0e7c5f2a147
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277525"
---
# <a name="tutorial-set-up-a-spring-cloud-config-server-instance-for-your-service"></a>Oktatóanyag: Spring Cloud config Server-példány beállítása a szolgáltatáshoz

Ez a cikk bemutatja, hogyan csatlakozhat egy Spring Cloud config Server-példányt az Azure Spring Cloud Service-hez.

A Spring Cloud config kiszolgáló-és ügyféloldali támogatást biztosít az elosztott rendszerekben található külső konfigurációhoz. A konfigurációs kiszolgáló példányával központi helyen kezelheti az alkalmazások külső tulajdonságait az összes környezetben. További információ: [Spring Cloud config Server Reference](https://spring.io/projects/spring-cloud-config).

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 
* Már üzembe lett helyezve, és futtatja az Azure Spring Cloud Service-t. Azure Spring Cloud-szolgáltatás beállításához és elindításához lásd [: gyors útmutató: Java Spring-alkalmazás elindítása az Azure CLI használatával](spring-cloud-quickstart-launch-app-cli.md).

## <a name="restriction"></a>Korlátozás

A konfigurációs kiszolgáló és a git háttérrendszer használatával bizonyos korlátozások vonatkoznak. Bizonyos tulajdonságok automatikusan bekerülnek az alkalmazási környezetbe a konfigurációs kiszolgáló és a szolgáltatás felderítésének eléréséhez. Ha ezeket a tulajdonságokat a konfigurációs kiszolgáló fájljaiból is konfigurálja, az ütközéseket és a váratlan viselkedést tapasztalhatja. A tulajdonságok a következők: 

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
```

> [!CAUTION]
> Azt javasoljuk, hogy ne _Helyezze el_ a fenti tulajdonságokat a konfigurációs kiszolgáló alkalmazás fájljaiban.

## <a name="create-your-config-server-files"></a>A konfigurációs kiszolgáló fájljainak létrehozása

Az Azure Spring Cloud támogatja az Azure DevOps, a GitHub, a GitLab és a bitbucket a konfigurációs kiszolgáló fájljainak tárolásához. Ha a tárháza elkészült, hozza létre a konfigurációs fájlokat az alábbi utasításokkal, és tárolja őket.

Emellett néhány konfigurálható tulajdonság csak bizonyos típusokhoz érhető el. A következő alszakaszok felsorolják az egyes adattár-típusok tulajdonságait.

### <a name="public-repository"></a>Nyilvános tárház

Ha nyilvános tárházat használ, a konfigurálható tulajdonságok korlátozottabbak.

A nyilvános git-tárház beállításához használt összes konfigurálható tulajdonság a következő táblázatban látható:

> [!NOTE]
> Kötőjel (-) használata a szavak elkülönítésére az egyetlen jelenleg támogatott elnevezési konvenció. Használhatja például az *alapértelmezett címkét*, de nem *defaultLabel*.

| Tulajdonság        | Szükséges | Szolgáltatás                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Igen    | A konfigurációs kiszolgálóként használt git-tárház URI-ja a *http://* , a *https://* , a *git@* és a *SSH://* karakterrel kezdődik. |
| `default-label` | Nem     | A git-tárház alapértelmezett címkéje az adattár *neve*, a *címke neve*vagy a tárház *véglegesítő azonosítója* lehet. |
| `search-paths`  | Nem     | A git-tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |

------

### <a name="private-repository-with-ssh-authentication"></a>Privát adattár SSH-hitelesítéssel

A privát git-tárház SSH-val történő beállításához használt összes konfigurálható tulajdonság a következő táblázatban látható:

> [!NOTE]
> Kötőjel (-) használata a szavak elkülönítésére az egyetlen jelenleg támogatott elnevezési konvenció. Használhatja például az *alapértelmezett címkét*, de nem *defaultLabel*.

| Tulajdonság                   | Szükséges | Szolgáltatás                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | Igen    | A konfigurációs kiszolgálóként használt git-tárház URI-JÁT *http://* , *https://* , *git@* vagy *SSH://* kell elindítani. |
| `default-label`            | Nem     | A git-tárház alapértelmezett címkéje az adattár *neve*, a *címke neve*vagy a tárház *véglegesítő azonosítója* lehet. |
| `search-paths`             | Nem     | A git-tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |
| `private-key`              | Nem     | A git-tárház eléréséhez _szükséges_ SSH titkos kulcs, ha az URI *git@* vagy *SSH://* -vel kezdődik. |
| `host-key`                 | Nem     | A git-tárház kiszolgálójának gazdagép kulcsa nem tartalmazhatja a `host-key-algorithm`által lefedett algoritmus-előtagot. |
| `host-key-algorithm`       | Nem     | A host key algoritmusnak *SSH-DSS*, *SSH-RSA*, *ECDSA-SHA2-nistp256*, *ECDSA-SHA2-nistp384*vagy *ECDSA-SHA2-nistp521*értékűnek kell lennie. Csak akkor *szükséges* , ha `host-key` létezik. |
| `strict-host-key-checking` | Nem     | Azt jelzi, hogy a konfigurációs kiszolgáló példánya nem indul el a magánhálózati `host-key`kihasználása esetén. *Igaznak* kell lennie (alapértelmezett érték) vagy *false (hamis*). |

-----

### <a name="private-repository-with-basic-authentication"></a>Egyéni adattár alapszintű hitelesítéssel

Az alapszintű hitelesítéssel rendelkező privát git-tárház beállításához használt összes konfigurálható tulajdonság alább látható.

> [!NOTE]
> Kötőjel (-) használata a szavak elkülönítésére az egyetlen jelenleg támogatott elnevezési konvenció. Használja például az *alapértelmezett-címkét*, ne *defaultLabel*.

| Tulajdonság        | Szükséges | Szolgáltatás                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | Igen    | A konfigurációs kiszolgálóként használt git-tárház URI-JÁT a *http://* , a *https://* , a *git@* vagy a *SSH://* kell elindítani. |
| `default-label` | Nem     | A git-tárház alapértelmezett címkéje az adattár *neve*, a *címke neve*vagy a tárház *véglegesítő azonosítója* lehet. |
| `search-paths`  | Nem     | A git-tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |
| `username`      | Nem     | A git-tárház kiszolgálójának eléréséhez használt Felhasználónév, amely akkor _szükséges_ , ha a git-tárház kiszolgálója támogatja a `Http Basic Authentication`. |
| `password`      | Nem     | A git-tárház kiszolgálójának eléréséhez használt jelszó, amely akkor _szükséges_ , ha a git-tárház kiszolgálója támogatja a `Http Basic Authentication`. |

> [!NOTE]
> Számos `Git` adattár-kiszolgáló támogatja a tokenek használatát a HTTP alapszintű hitelesítéshez használt jelszavak helyett. Egyes Tárházak, például a GitHub, lehetővé teszik a tokenek határozatlan ideig való megőrzését. Bizonyos git-tárház-kiszolgálók, például az Azure DevOps, néhány óra múlva lejárnak. A jogkivonatok lejáratát okozó adattárak nem használhatnak jogkivonat-alapú hitelesítést az Azure Spring Cloud használatával.

### <a name="git-repositories-with-pattern"></a>Git-adattárak mintázattal

Az alábbi listában a git-adattárak beállításához használt összes konfigurálható tulajdonság szerepel.

> [!NOTE]
> Kötőjel (-) használata a szavak elkülönítésére az egyetlen jelenleg támogatott elnevezési konvenció. Használja például az *alapértelmezett-címkét*, ne *defaultLabel*.

| Tulajdonság                           | Szükséges         | Szolgáltatás                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | Nem             | Egy adott névvel rendelkező git-tárház beállításait tartalmazó Térkép. |
| `repos."uri"`                      | Igen `repos` | A konfigurációs kiszolgálóként használt git-tárház URI-JÁT a *http://* , a *https://* , a *git@* vagy a *SSH://* kell elindítani. |
| `repos."name"`                     | Igen `repos` | A git-tárházon azonosítható név, amely csak akkor _szükséges_ , ha `repos` létezik. Például: *Team-A*, *Team-B*. |
| `repos."pattern"`                  | Nem             | Az alkalmazás nevének megfeleltetéséhez használt karakterláncok tömbje. Minden mintához használja a `{application}/{profile}` formátumot helyettesítő karakterekkel. |
| `repos."default-label"`            | Nem             | A git-tárház alapértelmezett címkéje az adattár *neve*, a *címke neve*vagy a tárház *véglegesítő azonosítója* lehet. |
| `repos."search-paths`"             | Nem             | A git-tárház alkönyvtáraiban való kereséshez használt karakterláncok tömbje. |
| `repos."username"`                 | Nem             | A git-tárház kiszolgálójának eléréséhez használt Felhasználónév, amely akkor _szükséges_ , ha a git-tárház kiszolgálója támogatja a `Http Basic Authentication`. |
| `repos."password"`                 | Nem             | A git-tárház kiszolgálójának eléréséhez használt jelszó, amely akkor _szükséges_ , ha a git-tárház kiszolgálója támogatja a `Http Basic Authentication`. |
| `repos."private-key"`              | Nem             | A git-tárház eléréséhez _szükséges_ SSH titkos kulcs, ha az URI *git@* vagy *SSH://* -vel kezdődik. |
| `repos."host-key"`                 | Nem             | A git-tárház kiszolgálójának gazdagép kulcsa nem tartalmazhatja a `host-key-algorithm`által lefedett algoritmus-előtagot. |
| `repos."host-key-algorithm"`       | Nem             | A host key algoritmusnak *SSH-DSS*, *SSH-RSA*, *ECDSA-SHA2-nistp256*, *ECDSA-SHA2-nistp384*vagy *ECDSA-SHA2-nistp521*értékűnek kell lennie. Csak akkor *szükséges* , ha `host-key` létezik. |
| `repos."strict-host-key-checking"` | Nem             | Azt jelzi, hogy a konfigurációs kiszolgáló példánya nem indul el a magánhálózati `host-key`kihasználása esetén. *Igaznak* kell lennie (alapértelmezett érték) vagy *false (hamis*). |

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>A konfigurációs kiszolgáló tárházának csatlakoztatása az Azure Spring Cloud-hoz

Most, hogy a konfigurációs fájlokat egy adattárba menti, hozzá kell csatlakoznia az Azure Spring Cloud-hoz.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Nyissa meg az Azure Spring Cloud **Áttekintés** oldalát.

1. Válassza ki a konfigurálni kívánt szolgáltatást.

1. A szolgáltatás lap bal oldali paneljének **Beállítások**területén válassza a **konfigurációs kiszolgáló** fület.

![A konfigurációs kiszolgáló ablaka](media/spring-cloud-tutorial-config-server/portal-config-server.png)

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Adja meg a tárház adatait közvetlenül a Azure Portal

#### <a name="default-repository"></a>Alapértelmezett adattár

* **Nyilvános adattár**: az **alapértelmezett adattár** szakasz **URI** mezőjébe illessze be a tárház URI-ját.  Állítsa be a **címkét** a **konfigurációra**. Győződjön meg arról, hogy a **hitelesítési** beállítás **nyilvános**, majd válassza az **alkalmaz** a befejezésre lehetőséget. 

* **Privát tárház**: az Azure Spring Cloud támogatja az alapszintű jelszó/jogkivonat-alapú hitelesítést és az SSH-t.

    * **Egyszerű hitelesítés**: az **alapértelmezett adattár** szakasz **URI** mezőjébe illessze be a tárház URI-ját, majd válassza a **hitelesítés** ("ceruza" ikon) gombot. A **hitelesítés szerkesztése** ablaktáblán a **Hitelesítés típusa** legördülő listában válassza a **http alapszintű**lehetőséget, majd adja meg felhasználónevét és jelszavát/jogkivonatát az Azure Spring Cloud elérésének biztosításához. Kattintson **az OK gombra**, majd válassza az **alkalmaz** lehetőséget a konfigurációs kiszolgálópéldány beállításának befejezéséhez.

    ![A hitelesítés szerkesztése panel](media/spring-cloud-tutorial-config-server/basic-auth.png)
    
    > [!CAUTION]
    > Egyes git-tárház-kiszolgálók, például a GitHub, *személyes jogkivonatot* vagy *hozzáférési tokent*(például jelszót) használnak az **alapszintű hitelesítéshez**. Ezt a tokent használhatja jelszóként az Azure Spring Cloud-ban, mert soha nem jár le. Más git-tárház-kiszolgálók esetében, például a bitbucket és az Azure DevOps esetében a *hozzáférési token* egy vagy két órán belül lejár. Ez azt jelenti, hogy a beállítás nem életképes, ha ezeket az adattár-kiszolgálókat használja az Azure Spring Cloud használatával.

    * **SSH**: az **alapértelmezett adattár** szakasz **URI** mezőjébe illessze be a tárház URI-ját, majd válassza a **hitelesítés** ("ceruza" ikon) gombot. A **hitelesítés szerkesztése** ablaktáblán a **Hitelesítés típusa** legördülő listában válassza az **SSH**lehetőséget, majd adja meg a **titkos kulcsot**. Szükség esetén megadhatja a **gazdagép kulcsát** és a **gazdagép kulcsának algoritmusát**is. Ügyeljen arra, hogy a nyilvános kulcsot a konfigurációs kiszolgáló adattárában is tartalmazza. Kattintson **az OK gombra**, majd válassza az **alkalmaz** lehetőséget a konfigurációs kiszolgálópéldány beállításának befejezéséhez.

    ![A hitelesítés szerkesztése panel](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="pattern-repository"></a>Minta adattár

Ha a szolgáltatás konfigurálásához egy opcionális **minta-tárházat** szeretne használni, adja meg az **URI** -t és a **hitelesítést** ugyanúgy, mint az **alapértelmezett tárházat**. Ügyeljen arra, hogy tartalmazza a minta **nevét** , majd kattintson az **alkalmaz** gombra a példányhoz való csatlakoztatáshoz. 

### <a name="enter-repository-information-into-a-yaml-file"></a>Adja meg a tárház adatait egy YAML-fájlba

Ha YAML-fájlt írt az adattár beállításaival, a fájlt közvetlenül a helyi gépről importálhatja az Azure Spring Cloud-ba. Az alapszintű hitelesítéssel rendelkező privát tárház egyszerű YAML-fájlja a következőképpen fog kinézni:

```yml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

Válassza a **Beállítások importálása** gombot, majd válassza ki a YAML fájlt a projekt könyvtárából. Válassza az **Importálás**lehetőséget, majd az **értesítések** közül egy `async` művelet jelenik meg. 1-2 perc elteltével a sikeres jelentésnek kell lennie.

![A konfigurációs kiszolgáló értesítései panel](media/spring-cloud-tutorial-config-server/local-yml-success.png)


A YAML-fájlból származó adatoknak meg kell jelennie a Azure Portalban. A befejezéshez kattintson az **alkalmaz** gombra. 


## <a name="delete-your-app-configuration"></a>Az alkalmazás konfigurációjának törlése

A konfigurációs fájl mentése után megjelenik az alkalmazás- **konfiguráció törlése** gomb a **konfiguráció** lapon. a gomb kiválasztásával teljesen törölni fogja a meglévő beállításokat. Válassza ki, ha a konfigurációs kiszolgáló példányát egy másik forráshoz szeretné kapcsolni, például a GitHubról az Azure DevOps-ra való áttérést.



## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan engedélyezheti és konfigurálhatja a Spring Cloud config Server-példányát. Az alkalmazások kezelésével kapcsolatos további tudnivalókért folytassa az alkalmazás manuális skálázásával foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
> [Oktatóanyag: alkalmazások méretezése az Azure Spring Cloud-ban](spring-cloud-tutorial-scale-manual.md)
