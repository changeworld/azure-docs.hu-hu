---
title: Azure bejárati ajtó szolgáltatás – gyorsítótárazás | Microsoft Docs
description: Ez a cikk segít megérteni, hogy az Azure bejárati ajtó szolgáltatás hogyan figyeli a háttérrendszer állapotát
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 70ee0af0b39e80aa90d143303b3c522fbb3cc780
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839214"
---
# <a name="caching-with-azure-front-door-service"></a>Gyorsítótárazás az Azure bejárati ajtó szolgáltatásával
A következő dokumentum a bejárati ajtó működésének módját határozza meg az olyan útválasztási szabályokkal, amelyeken engedélyezve van a gyorsítótárazás.

## <a name="delivery-of-large-files"></a>Nagyméretű fájlok kézbesítése
Az Azure bejárati ajtó szolgáltatás nagyméretű fájlokat tesz elérhetővé a fájl mérete nélkül. A bejárati ajtó egy objektum-darabolás nevű technikát használ. Amikor nagyméretű fájlokat igényelnek, a Front Door lekéri a fájl kisebb darabjait a háttérrendszerről. Ha a rendszer teljes vagy bájttartományra vonatkozó fájlkérelmet kap, a Front Door-környezet 8 MB-os darabokban kéri le a fájlt a háttérrendszerről.

</br>Miután a rendszer beolvasta a tömböt az első ajtós környezetben, a gyorsítótárba kerül, és azonnal kiszolgálja a felhasználónak. A bejárati ajtó ezután előre lekéri a következő adatrészletet párhuzamosan. Ez az előzetes beolvasás biztosítja, hogy a tartalom a felhasználó előtt egy darabban maradjon, ami csökkenti a késést. Ez a folyamat addig folytatódik, amíg a teljes fájl le nem töltődik (ha szükséges), az összes bájtos tartomány elérhető (ha szükséges), vagy az ügyfél leállítja a csatlakozást.

</br>A byte-Range kérelemmel kapcsolatos további információkért olvassa el a következőt: [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
A bejárati ajtó gyorsítótárba helyezi a kapott adattömböket, így a teljes fájlt nem kell gyorsítótárazni a bejárati ajtó gyorsítótárában. A fájl-vagy byte-tartományokra vonatkozó további kérelmek a gyorsítótárból lesznek kézbesítve. Ha nem az összes adathalmaz gyorsítótárazva van, a rendszer a háttérbeli adattömböket kéri le. Ez az optimalizálás arra támaszkodik, hogy a háttérrendszer képes támogatni a bájtos tartományokra vonatkozó kérelmeket; Ha a háttérrendszer nem támogatja a bájtok közötti kérelmeket, ez az optimalizálás nem érvényes.

## <a name="file-compression"></a>Fájltömörítés
A bejárati ajtó dinamikusan tömörítheti a tartalmat a peremhálózat szélén, így kevesebb és gyorsabb választ kaphat az ügyfeleknek. Az összes fájl tömörítésre alkalmas. A fájlnak azonban olyan MIME-típusnak kell lennie, amely jogosult a tömörítési listára. Jelenleg a bejárati ajtó nem teszi lehetővé a lista módosítását. Az aktuális lista:</br>
- "alkalmazás/EOT"
- "alkalmazás/betűkészlet"
- "alkalmazás/betűkészlet – sfnt"
- "alkalmazás/JavaScript"
- "alkalmazás/JSON"
- "alkalmazás/OpenType"
- "alkalmazás/OTF"
- "Application/PKCS7 – MIME"
- "alkalmazás/TrueType"
- "Application/TTF",
- "Application/vnd. MS-fontobject"
- "Application/XHTML + XML"
- "Application/XML"
- "alkalmazás/XML + RSS"
- "application/x-font-OpenType"
- "application/x-font-TrueType"
- "application/x-font-TTF"
- "application/x-httpd-CGI"
- "application/x-mpegurl"
- "application/x-OpenType"
- "application/x-OTF"
- "application/x-Perl"
- "application/x-TTF"
- "alkalmazás/x-JavaScript"
- "font/EOT"
- "betűkészlet/TTF"
- "font/OTF"
- "betűkészlet/OpenType"
- "rendszerkép/SVG + XML"
- "text/CSS"
- "text/CSV"
- "text/html"
- "text/JavaScript"
- "text/js", "text/plain"
- "text/RichText"
- "szöveg/tabulátorral tagolt értékek"
- "text/XML"
- "text/x-script"
- "text/x-Component"
- "text/x-Java-Source"

Emellett a fájlnak 1 KB és 8 MB méretűnek kell lennie, beleértve a-t is.

Ezek a profilok a következő tömörítési kódolásokat támogatják:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Ha egy kérelem támogatja a gzip és a Brotli tömörítést, a Brotli-tömörítés elsőbbséget élvez.</br>
Ha egy adategységre vonatkozó kérelem meghatározza a tömörítést, és a kérés gyorsítótár-hiányt eredményez, a bejárati ajtó közvetlenül a POP-kiszolgálón végzi el az eszköz tömörítését. Ezt követően a tömörített fájlt a rendszer a gyorsítótárból kézbesíti. Az eredményül kapott tételt egy átvitel – Encoding: darabolásos érték adja vissza.

## <a name="query-string-behavior"></a>Lekérdezési karakterlánc viselkedése
A bejárati ajtó segítségével szabályozhatja, hogy a rendszer hogyan gyorsítótárazza a fájlokat a lekérdezési karakterláncot tartalmazó webes kérelmek esetében. Lekérdezési karakterláncot tartalmazó webes kérelem esetén a lekérdezési karakterlánc a kérelemnek a kérdőjel (?) utáni részét jelöli. A lekérdezési karakterláncok tartalmazhatnak egy vagy több kulcs-érték párokat, amelyekben a mező nevét és értékét egy egyenlőségjel (=) választja el egymástól. A kulcs-érték párokat egy jel (&) választja el egymástól. Például: `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Ha egy kérelem lekérdezési karakterláncában egynél több kulcs-érték pár szerepel, a rendelésük nem számít.
- **Lekérdezési karakterláncok figyelmen kívül hagyása**: alapértelmezett mód. Ebben a módban a bejárati ajtó továbbítja a lekérdezési karakterláncokat a kérelmezőtől az első kérelemben található háttérbe, és gyorsítótárazza az eszközt. Az eszköz bejárati környezetből kiszolgált összes további kérelme figyelmen kívül hagyja a lekérdezési karakterláncokat, amíg a gyorsítótárazott eszköz le nem jár.

- **Gyorsítótár – minden egyedi URL-cím**: ebben a módban minden egyedi URL-címmel rendelkező kérelem, beleértve a lekérdezési karakterláncot, a saját gyorsítótárral rendelkező egyedi objektumként lesz kezelve. Például a rendszer a háttérbeli `www.example.ashx?q=test1`re vonatkozó kérelemre adott válaszát gyorsítótárazza a bejárati ajtó környezetében, és visszaadja a későbbi gyorsítótárak esetében ugyanazzal a lekérdezési karakterlánccal. A `www.example.ashx?q=test2`ra vonatkozó kérelmet a rendszer külön eszközként gyorsítótárazza a saját élettartam beállítással.

## <a name="cache-purge"></a>Gyorsítótár kiürítése
A bejárati ajtó gyorsítótárba helyezi az eszközöket, amíg az eszköz élettartama (TTL) lejár. Miután az objektum ÉLETTARTAMa lejár, amikor egy ügyfél kéri az eszközt, az előtérben lévő környezet beolvassa az eszköz új, frissített példányát az ügyfél kérésének kiszolgálásához, és tárolja a gyorsítótár frissítését.
</br>Az ajánlott eljárás annak biztosítására, hogy a felhasználók mindig megkapják az adategységek legújabb példányát, hogy minden egyes frissítéshez a saját eszközeiket, és azokat új URL-ként tegye közzé. A bejárati ajtó azonnal lekéri az új eszközöket a következő ügyfelek kéréseire. Előfordulhat, hogy a gyorsítótárazott tartalmat törölni kívánja az összes peremhálózati csomópontról, és az összeset kényszeríti az új eszközök beolvasására. Ennek oka lehet a webalkalmazás frissítései, vagy a helytelen adatokat tartalmazó eszközök gyors frissítése.

</br>Válassza ki, hogy milyen eszközöket kíván kiüríteni a peremhálózati csomópontokból. Ha törölni kívánja az összes eszközt, kattintson az összes kiürítés jelölőnégyzetre. Ellenkező esetben írja be az elérési út szövegmezőben az összes törölni kívánt eszköz elérési útját. Az alábbi formátumok támogatottak az elérési úton.
1. **Egy URL-cím kiürítése**: az egyes adategységek kiürítése a teljes URL-cím megadásával, a fájlkiterjesztés használatával, például/Pictures/Strasbourg.png;
2. **Helyettesítő karakteres törlés**: a csillag (\*) helyettesítő karakterként is használható. Az összes mappa, almappa és fájl végleges törlése az elérési út vagy\* alatt lévő végpont alatt, illetve az összes almappa és fájl kiürítése egy adott mappában, a mappa és a\*(például/Pictures/\*) megadása után.
3. **Gyökértartomány kiürítése**: Ürítse ki a végpont gyökerét az elérési úton található "/" értékkel.

A bejárati ajtón a gyorsítótár kiürítése kis-és nagybetűk megkülönböztetése nélkül történik. Emellett a lekérdezési karakterláncokat is használják, ami azt jelenti, hogy az URL-cím ürítése törli az összes lekérdezési karakterlánc-változatot. 

## <a name="cache-expiration"></a>Gyorsítótár lejárata
A rendszer a következő fejlécek sorrendjét használja annak meghatározásához, hogy mennyi ideig tárolja a rendszer az elemeket a gyorsítótárban:</br>
1. Cache-Control: s-maxage =\<másodperc >
2. Cache-Control: Max-Age =\<másodperc >
3. Lejárat: \<http-Date >

Cache-Control Response fejlécek, amelyek azt jelzik, hogy a válasz nem lesz gyorsítótárazva, például a Cache-Control: Private, Cache-Control: no-cache és Cache-Control: No-Store tiszteletben. Ha azonban egy adott URL-címen több kérelem van folyamatban egy POP-on, akkor megoszthatják a választ. Ha nincs gyorsítótár-vezérlő, az alapértelmezett viselkedés az, hogy a AFD gyorsítótárazza az erőforrást X időtartamra, ahol az X véletlenszerűen 1 és 3 nap közötti értéket vesz fel.


## <a name="request-headers"></a>Kérések fejlécei

A következő kérések fejléceit a rendszer nem továbbítja a háttérbe gyorsítótárazás használatakor.
- Engedélyezés
- Content-Length
- Átvitel – kódolás

## <a name="next-steps"></a>További lépések

- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
