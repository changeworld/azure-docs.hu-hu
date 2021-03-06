---
title: Eszköz csatlakoztatása az Azure IoT Centralban | Microsoft Docs
description: Ez a cikk bemutatja az Azure-beli eszközök csatlakoztatásával kapcsolatos főbb fogalmakat IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: e67a8f6b9cc175932b09e6f576148656dd9da9ba
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298818"
---
# <a name="get-connected-to-azure-iot-central"></a>Csatlakozás az Azure IoT Centralhoz

Ez a cikk a Microsoft Azure IoT Central eszközhöz való csatlakoztatásával kapcsolatos főbb fogalmakat ismerteti.

Az Azure IoT Central az [azure IoT hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) használatával kezeli az összes eszköz regisztrációját és kapcsolódását.

A DPS használata lehetővé teszi a következőket:

- IoT Central az eszközök nagy léptékű bevezetésének és csatlakoztatásának támogatásához.
- Az eszköz hitelesítő adatainak előállításához és az eszközök offline konfigurálásához az eszközök IoT Central felhasználói felületen keresztül történő regisztrálása nélkül.
- Megosztott hozzáférési aláírások (SAS) használatával csatlakozó eszközök.
- Az iparági szabványnak megfelelő X. 509 tanúsítványok használatával csatlakoztatható eszközök.
- A saját eszközök azonosítóinak használatával regisztrálhatja az eszközöket a IoT Centralban. A saját eszköz-azonosítók használatával egyszerűbbé válik a meglévő Back-Office rendszerekkel való integráció.
- Egyetlen, egységes módszer az eszközök IoT Centralhoz való csatlakoztatására.

Ez a cikk a következő használati eseteket ismerteti:

- [Egyetlen eszköz gyors csatlakoztatása SAS használatával](#connect-a-single-device)
- [Eszközök csatlakoztatása nagy méretekben SAS használatával](#connect-devices-at-scale-using-sas)
- Az [eszközök méretezése az X. 509 tanúsítvánnyal](#connect-devices-using-x509-certificates) . Ez az ajánlott módszer az éles környezetekben.
- [Csatlakoztatás az eszközök első regisztrációja nélkül](#connect-without-registering-devices)
- [Eszközök csatlakoztatása a IoT Plug and Play (előzetes verzió) funkcióival](#connect-devices-with-iot-plug-and-play-preview)

## <a name="connect-a-single-device"></a>Egyetlen eszköz csatlakoztatása

Ez a megközelítés akkor lehet hasznos, ha IoT Central vagy tesztelési eszközökkel kísérletezik. A IoT Central alkalmazás eszköz kapcsolati adataival csatlakoztathat egy eszközt a IoT Central alkalmazáshoz az eszköz kiépítési szolgáltatása (DPS) használatával. A következő nyelveken talál minta DPS-eszköz ügyféloldali kódját:

- [C\#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device)
- [Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/tree/master/provisioning/Samples/device)

## <a name="connect-devices-at-scale-using-sas"></a>Eszközök csatlakoztatása nagy méretekben SAS használatával

Az eszközök SAS-vel való IoT Centralhoz való csatlakoztatásához regisztrálnia kell, majd be kell állítania az eszközöket:

### <a name="register-devices-in-bulk"></a>Eszközök tömeges regisztrálása

Ha nagy számú eszközt szeretne regisztrálni a IoT Central alkalmazással, használjon egy CSV-fájlt az eszközök [azonosítóinak és az eszközök nevének importálásához](howto-manage-devices.md#import-devices).

Az importált eszközökhöz tartozó kapcsolódási adatok lekéréséhez [exportáljon egy CSV-fájlt a IoT Central alkalmazásból](howto-manage-devices.md#export-devices).

> [!NOTE]
> Ha szeretné megtudni, hogy az eszközök hogyan csatlakoztathatók a IoT Centralba való első regisztrációja nélkül, tekintse meg a [Kapcsolódás az eszközök első regisztrációja nélkül](#connect-without-registering-devices)című témakört.

### <a name="set-up-your-devices"></a>Az eszközök beállítása

Az eszköz kódjában található exportálási fájl kapcsolati adataival lehetővé teheti, hogy az eszközök csatlakozzanak a IoT, és adatokat küldjenek a IoT Central alkalmazásnak. További információ az eszközök csatlakoztatásáról: [következő lépések](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Eszközök csatlakoztatása X. 509 tanúsítványok használatával

Éles környezetben az X. 509 tanúsítványok használata az ajánlott eszköz hitelesítési mechanizmusa IoT Central számára. További információért lásd: [az eszközök hitelesítése X. 509 hitelesítésszolgáltatói tanúsítványokkal](../../iot-hub/iot-hub-x509ca-overview.md).

Az alábbi lépések azt ismertetik, hogyan csatlakoztathatók az eszközök az X. 509 tanúsítványokkal IoT Centralhoz:

1. A IoT Central alkalmazásban adja hozzá és ellenőrizze az eszköz tanúsítványának létrehozásához használt _köztes vagy gyökér X. 509 tanúsítványt_ :

    - Navigáljon az **adminisztráció > az eszközök közötti kapcsolatok > a tanúsítványok (x. 509)** elemre, és adja hozzá az x. 509 típusú gyökér-vagy köztes tanúsítványt, amelyet Ön használ a levél típusú tanúsítványok létrehozásához.

      ![Kapcsolati beállítások](media/concepts-get-connected/connection-settings.png)

      Ha biztonsági problémákba ütközik, vagy ha az elsődleges tanúsítvány lejár, a másodlagos tanúsítvány használatával csökkentheti az állásidőt. Az elsődleges tanúsítvány frissítésekor továbbra is kiépítheti az eszközöket a másodlagos tanúsítvány használatával.

    - A tanúsítvány tulajdonjogának ellenőrzése biztosítja, hogy a tanúsítvány feltöltője rendelkezik a tanúsítvány titkos kulcsával. A tanúsítvány ellenőrzése:
        - A kód létrehozásához kattintson az **ellenőrző kód** melletti gombra.
        - Hozzon létre egy X. 509 ellenőrző tanúsítványt az előző lépésben létrehozott ellenőrző kóddal. Mentse a tanúsítványt. cer fájlként.
        - Töltse fel az aláírt ellenőrző tanúsítványt, és válassza az **ellenőrzés**lehetőséget.

          ![Kapcsolati beállítások](media/concepts-get-connected/verify-cert.png)

1. Használjon CSV-fájlt a IoT Central alkalmazásban lévő _eszközök importálásához és regisztrálásához_ .

1. _Állítsa be az eszközöket._ Létrehozza a levél tanúsítványait a feltöltött főtanúsítvány használatával. Használja az **eszköz azonosítóját** a levél tanúsítványainak CNAME értékeként. Az eszköz AZONOSÍTÓjának az összes kisbetűsnek kell lennie. Ezután kiépítheti az eszközöket a kiépítési szolgáltatás adataival. Amikor egy eszköz először van bekapcsolva, lekéri a IoT Central alkalmazás kapcsolati adatait a DPS-ból.

### <a name="further-reference"></a>További tudnivalók

- Példa a RaspberryPi megvalósítására [.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Példa a C-beli eszköz-ügyfélre.](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Csak tesztelési célokra

Csak teszteléshez használhatja ezeket a segédprogramokat a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok és az eszközök tanúsítványainak létrehozásához.

- Ha fejlesztői készlet eszközt használ, a [parancssori eszköz](https://aka.ms/iotcentral-docs-dicetool) létrehoz egy hitelesítésszolgáltatói tanúsítványt, amelyet hozzáadhat a IoT Central alkalmazáshoz a tanúsítványok ellenőrzéséhez.

- A következő [parancssori eszköz](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) használatával:
  - Hozzon létre egy tanúsítványláncot. Kövesse a GitHub-cikkben a 2. lépést.
  - Mentse a tanúsítványokat. cer fájlként a IoT Central alkalmazásba való feltöltéshez.
  - Az ellenőrző tanúsítvány létrehozásához használja a IoT Central alkalmazásban található ellenőrző kódot. Kövesse a GitHub-cikk 3. lépését.
  - Hozzon létre Leaf-tanúsítványokat az eszközökhöz az eszköz azonosítói alapján az eszköz paraméterének megfelelően. Kövesse a 4. lépést a GitHub-cikkben.

## <a name="connect-without-registering-devices"></a>Csatlakoztatás eszközök regisztrálása nélkül

A fő forgatókönyv IoT Central lehetővé teszi, hogy az OEM-eszközök tömeges előállítsák azokat az eszközöket, amelyek regisztráció nélkül csatlakozhatnak egy IoT Central alkalmazáshoz. A gyártónak megfelelő hitelesítő adatokat kell kiállítania, és konfigurálnia kell az eszközöket a gyárban. Amikor egy eszköz első alkalommal bekapcsol, automatikusan csatlakozik egy IoT Central alkalmazáshoz. Az IoT Central operátornak jóvá kell hagynia az eszközt, mielőtt az adatokat tudja küldeni.

A következő ábra a folyamatot vázolja:

![Kapcsolati beállítások](media/concepts-get-connected/device-connection-flow1.png)

A következő lépések részletesen ismertetik ezt a folyamatot. A lépések kis mértékben eltérnek attól függően, hogy SAS vagy X. 509 tanúsítványokat használ az eszköz hitelesítéséhez:

1. Konfigurálja a kapcsolatok beállításait:

    - **X. 509 tanúsítványok:** [adja hozzá és ellenőrizze a gyökér/köztes tanúsítványt](#connect-devices-using-x509-certificates) , és használja az eszköz tanúsítványának létrehozásához a következő lépésben.
    - **Sas:** Másolja az elsődleges kulcsot. Ez a kulcs a IoT Central-alkalmazáshoz tartozó SAS-kulcs. A kulcs használatával az eszköz SAS-kulcsait a következő lépésben generálhatja.
    ![a kapcsolatok beállításai SAS](media/concepts-get-connected/connection-settings-sas.png)

1. Az eszköz hitelesítő adatainak előállítása
    - **Tanúsítványok X. 509:** Az eszközökhöz tartozó levél-tanúsítványok létrehozása a IoT Central alkalmazáshoz hozzáadott legfelső szintű vagy köztes tanúsítvány használatával. Győződjön meg arról, hogy a kisbetűs **eszköz azonosítóját** használja CNAME-ként a levél tanúsítványokban. Csak tesztelési célra használja ezt a [parancssori eszközt](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) az eszközök tanúsítványainak létrehozásához.
    - **Sas:** Az eszköz SAS-kulcsainak létrehozásához használja ezt a [parancssori eszközt](https://www.npmjs.com/package/dps-keygen) . Használja az előző lépésben a csoport **elsődleges kulcsát** . Az eszköz AZONOSÍTÓjának kisbetűnek kell lennie.

      A [Key Generator segédprogram](https://github.com/Azure/dps-keygen)telepítéséhez futtassa a következő parancsot:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      A következő parancs futtatásával hozhatja elő a kulcsokat a csoportos SAS elsődleges kulcsból:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Az eszközök beállításához a Flash minden eszközt a hatókör- **azonosító**, az **eszköz azonosítója**és az **X. 509 eszköz tanúsítványa** vagy **sas-kulcsa**alapján kell beállítani.

1. Ezután kapcsolja be az eszközt a IoT Central alkalmazáshoz való kapcsolódáshoz. Amikor bekapcsol egy eszközt, először csatlakozik a DPS-hez, hogy beolvassa a IoT Central regisztrációs adatait.

1. A csatlakoztatott eszköz kezdetben az **eszközök** lapon nem **társítottként** jelenik meg. Az eszköz kiépítési állapota **regisztrálva**van. **Telepítse át** az eszközt a megfelelő eszköz sablonba, és hagyja jóvá az eszközt a IoT Central alkalmazáshoz való kapcsolódáshoz. Az eszköz lekérheti a kapcsolódási karakterláncot a IoT Hubről, és megkezdheti az adatok küldését. Az eszköz üzembe helyezése befejeződött, és a létesítési állapot már **kiépítve**.

## <a name="individual-enrollment-based-device-connectivity"></a>Egyéni regisztráció-alapú eszközök kapcsolata

Azok az ügyfelek, akik a hitelesítő adatokkal rendelkező olyan eszközöket csatlakoztatnak, amelyek eszközönkénti egyéni regisztrációval rendelkeznek, a lehetőség. Az egyéni regisztráció egyetlen, a csatlakozáshoz használható eszköz bejegyzése. Az egyéni regisztrációk X. 509 levél-tanúsítványokat vagy SAS-jogkivonatokat (fizikai vagy virtuális TPM-ből) is használhatnak igazolási mechanizmusként. Az eszköz azonosítója (más néven regisztrációs azonosító) egy egyéni regisztrációban alfanumerikus, kisbetűs, és tartalmazhat kötőjeleket. További információ az egyéni regisztrációról [.](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)

> [!NOTE]
> Amikor egyéni regisztrációt hoz létre egy eszközhöz, az alkalmazásban elsőbbséget élvez az alapértelmezett csoportos regisztráción alapuló igazolásokkal (SAS, X509) szemben.

### <a name="creating-individual-enrollments"></a>Egyéni regisztrációk létrehozása
A IoT Central a következő igazolási mechanizmusokat támogatja

1. **Szimmetrikus kulcs igazolása:** A szimmetrikus kulcs igazolása egyszerű módszer egy eszköz kiépítési szolgáltatási példánnyal való hitelesítésére. Egyéni regisztráció szimmetrikus kulcsokkal való létrehozásához; Nyissa meg a csatlakozás párbeszédpanelt, válassza az egyéni regisztráció és a "SAS" mechanizmust, és adja meg az elsődleges és a másodlagos kulcsot. Az SAS-kulcsoknak Base64 kódolással kell rendelkezniük. Itt látható a kód mintáinak [hivatkozása](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) , amely segítséget nyújt az eszköz kódjának kiépítéséhez a szimmetrikus kulcsok és az egyéni regisztrációk használatával.
1. **X. 509 tanúsítványok:** Az X. 509 tanúsítványok, amelyeknek a címe azt sugallja, egy tanúsítványalapú igazolási mechanizmus, amely kiváló módot biztosít a termelés méretezésére. A szimmetrikus kulcsokkal rendelkező egyéni regisztráció létrehozásához válassza az egyéni regisztráció és a "X. 509" mechanizmust, és töltse fel az elsődleges és a másodlagos tanúsítványokat, és mentse a bejegyzést a regisztráció létrehozásához. Itt látható a kód mintáinak [hivatkozása](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) , amely segítséget nyújt az eszköz kódjának az X509 használatával történő kiépítéséhez. Az [Egyéni beléptetési](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment) bejegyzésekhez használt eszközök tanúsítványainak követelménye, hogy a tulajdonos nevét az egyéni regisztrációs bejegyzés eszköz-azonosítójának (más néven regisztrációs azonosítójának) kell beállítania.
1. **TPM-igazolás:** A TPM a platformmegbízhatósági modul számára készült, és a hardveres biztonsági modul (HSM) típusa, és az eszköz csatlakoztatásának egyik legbiztonságosabb módja.  Ez a cikk feltételezi, hogy diszkrét, belső vezérlőprogramot vagy integrált TPM-t használ. A szoftveresen emulált TPM a prototípus-készítéshez és a teszteléshez megfelelőek, de nem biztosítják ugyanazt a biztonsági szintet, mint a diszkrét, a belső vezérlőprogram vagy az integrált TPM. A szoftveres TPM nem ajánlott éles környezetben használni. A szimmetrikus kulcsokkal rendelkező egyéni regisztráció létrehozásához válassza az egyéni regisztráció és a "TPM" mechanizmust, és adja meg a regisztrációs kulcsokat a regisztráció létrehozásához. A TPM típusaival kapcsolatos további információkért tekintse meg az [itt](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation)található TPM-igazolást. Itt látható a kód mintáinak [hivatkozása](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/TpmSample) , amely segítséget nyújt az eszközök TPM használatával történő kiépítéséhez. TPM-alapú igazolás létrehozásához írja be a záradékot a jóváhagyó kulcsba, és mentse a következőt:.

## <a name="connect-devices-with-iot-plug-and-play-preview"></a>Eszközök csatlakoztatása a IoT Plug and Play (előzetes verzió)

A IoT Plug and Play (előzetes verzió) egyik kulcsfontosságú funkciója, a IoT Central segítségével automatikusan társíthatja az eszközöket az eszköz kapcsolataihoz. Az eszköz hitelesítő adataival együtt az eszközök most már elküldhetik a **CapabilityModelId** az eszköz regisztrációs hívásának részeként, IoT Central pedig felderíti és társítja az eszközt. A felderítési folyamat a következő sorrendet követi:

1. Társítja az eszköz sablonját, ha az IoT Central alkalmazásban már közzé van téve.
1. Beolvassa a közzétett és tanúsított képességi modellek nyilvános tárházát.

Alább látható az eszköz által a DPS regisztrációs hívás során elküldött további hasznos adatok formátuma

```javascript
'__iot:interfaces': {
              CapabilityModelId: <this is the URN for the capability model>
          }
```

> [!NOTE]
> Vegye figyelembe, hogy az automatikus jóváhagyás beállítást engedélyezni kell az eszközök automatikus csatlakoztatásához, a modell felderítéséhez és az adatok küldésének megkezdéséhez.

## <a name="device-status"></a>Eszköz állapota

Ha egy valós eszköz csatlakozik a IoT Central alkalmazáshoz, az eszköz állapota a következőképpen változik:

1. Az eszköz állapota először **regisztrálva**van. Ez az állapot azt jelenti, hogy az eszköz a IoT Centralban jön létre, és rendelkezik egy eszköz azonosítójával. Az eszköz regisztrálása az alábbiak szerint történik:
    - A rendszer új valódi eszközt ad hozzá az **eszközök** lapon.
    - Az **eszközök** lapon az **Importálás** használatával adhat hozzá eszközöket.

1. Az **Eszközállapot akkor változik, ha a** IoT Central alkalmazáshoz az érvényes hitelesítő adatokkal csatlakozó eszköz befejezi a kiépítési lépést. Ebben a lépésben az eszköz lekérdezi a IoT Hubból a kapcsolatok karakterláncát. Az eszköz most már csatlakozhat IoT Hubhoz, és megkezdheti az adatok küldését.

1. Az operátor letilthat egy eszközt. Ha egy eszköz le van tiltva, nem tud adatküldést küldeni a IoT Central alkalmazásnak. A letiltott eszközök állapota **Letiltva**. Az operátornak vissza kell állítania az eszközt az adatok küldésének folytatásához. Ha egy operátor feloldja egy eszköz zárolását, az állapot visszatér az előző értékre, **regisztrálva** vagy **kiépítve**.

1. Az eszköz állapota **jóváhagyásra vár**, ami azt jelenti, hogy az **automatikus jóváhagyás** lehetőség le van tiltva, és az IoT Centralhoz csatlakozó összes eszközt explicit módon jóvá kell hagyni egy operátor. Az **Eszközök lapon nem** regisztrált eszközök, de az érvényes hitelesítő adatokkal való kapcsolat az eszköz állapota **jóváhagyásra vár**. Az operátorok a **jóváhagyás** gomb használatával hagyhatják jóvá ezeket az eszközöket az **eszközök** lapról.

1. Az eszköz állapota nincs **társítva**, ami azt jelenti, hogy a IoT Centralhoz csatlakozó eszközökhöz nem tartozik sablon társítva. Ez általában a következő esetekben fordul elő:
    - A Devices ( **eszközök** ) lapon az **Importálás** használatával bővülnek az eszközök egy készlete az eszköz sablonjának megadása nélkül
    - Azok az eszközök, amelyek nem lettek kézzel regisztrálva az **eszközök** lapon érvényes hitelesítő adatokkal, de nem határozzák meg a sablon azonosítóját a regisztráció során.  
Az operátor a **Migrálás** gomb használatával rendelheti hozzá az eszközöket egy sablonhoz az **eszközök** lapról.

## <a name="best-practices"></a>Ajánlott eljárások 
1.  Ha a DPS használatával csatlakoztatja az eszközöket IoT Centralhoz, győződjön meg arról, hogy a (IoT Hub) eszköz kapcsolati karakterlánca nem marad meg vagy nem gyorsítótárazott. Az eszközök újbóli csatlakoztatásához lépjen a normál DPS-eszköz regisztrációs folyamatára a megfelelő eszköz kapcsolati karakterlánc beszerzéséhez. Ha a rendszer gyorsítótárazza a kapcsolódási sztringet, az eszközön futó szoftver elavult kapcsolódási karakterláncot tartalmaz abban az esetben, amikor a IoT Central frissítette az alapul szolgáló Azure-IoT Hub. 

## <a name="sdk-support"></a>SDK-támogatás

Az Azure-eszközök SDK-k az eszköz kódjának megvalósítására szolgáló legegyszerűbb megoldást nyújtanak. A következő eszköz-SDK-k érhetők el:

- [C-hez készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [Pythonhoz készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [Node. js-hez készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [Javához készült Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK a .NET-hez](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Az SDK szolgáltatásai és IoT Hub kapcsolat

A IoT Hub az összes eszköz kommunikációja a következő IoT Hub kapcsolódási lehetőségeket használja:

- [Eszközről a felhőbe irányuló üzenetkezelés](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Eszköz ikrek](../../iot-hub/iot-hub-devguide-device-twins.md)

Az alábbi táblázat összefoglalja, hogy az Azure IoT Central-eszköz funkciói hogyan képezhetők le IoT Hub funkciókra:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Mérték: telemetria | Eszközről a felhőbe irányuló üzenetkezelés |
| Eszköztulajdonságok | Eszköz kettős jelentett tulajdonságai |
| Beállítások | Az eszköz Twin kívánt és jelentett tulajdonságai |

Ha többet szeretne megtudni az eszköz SDK-k használatáról, tekintse meg a [DevDiv-készlet eszköz csatlakoztatása az Azure IoT Central-alkalmazáshoz](howto-connect-devkit.md) , például kód című témakört.

### <a name="protocols"></a>Protokollok

Az eszköz SDK-k a következő hálózati protokollokat támogatják az IoT hubhoz való csatlakozáshoz:

- MQTT
- AMQP
- HTTPS

További információ ezekről a különbségi protokollokról és az első kiválasztásáról: [kommunikációs protokoll kiválasztása](../../iot-hub/iot-hub-devguide-protocols.md).

Ha az eszköz nem tudja használni a támogatott protokollokat, az Azure IoT Edge használatával végezheti el a protokollok átalakítását. A IoT Edge támogatja a más, az Azure IoT Central alkalmazásban az Edge-be való kiszervezéshez szükséges egyéb hírszerzési forgatókönyveket.

## <a name="security"></a>Biztonság

Az eszközök és az Azure-IoT Central között kicserélt összes adatforgalom titkosítva van. IoT Hub minden olyan eszközről hitelesíti a kérelmet, amely az eszközre irányuló IoT Hub végpontokhoz csatlakozik. A hitelesítő adatok vezetéken keresztüli cseréjének elkerüléséhez az eszköz aláírt jogkivonatokat használ a hitelesítéshez. További információ: [IoT hub hozzáférésének szabályozása](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az eszköz kapcsolatát az Azure IoT Centralban, a következő lépéseket javasoljuk:

- [Fejlesztői készlet-eszköz előkészítése és csatlakoztatása](howto-connect-devkit.md)
- [C SDK: kiépítési eszköz ügyféloldali SDK-je](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
