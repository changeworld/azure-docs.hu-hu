---
title: Alsóbb rétegbeli eszközök csatlakoztatása – Azure IoT Edge | Microsoft Docs
description: Az alsóbb rétegbeli vagy a levélben lévő eszközök konfigurálása Azure IoT Edge Gateway-eszközökhöz való kapcsolódáshoz.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6ddda38d887cdfe30b449847e2f625ba17f33898
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510804"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Alsóbb rétegbeli eszköz csatlakoztatása Azure IoT Edge átjáróhoz

Ez a cikk útmutatást nyújt az alsóbb rétegbeli eszközök és a IoT Edge transzparens átjárók közötti megbízható kapcsolat létesítéséhez. Transzparens átjáró esetén egy vagy több eszköz átadhatja az üzeneteiket egyetlen átjárón keresztül, amely karbantartja a kapcsolatot IoT Hub. Egy alsóbb rétegbeli eszköz lehet bármely olyan alkalmazás vagy platform, amely rendelkezik az [Azure IoT hub](https://docs.microsoft.com/azure/iot-hub) Cloud Service szolgáltatással létrehozott identitással. Sok esetben ezek az alkalmazások az [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md)-t használják. Egy alsóbb rétegbeli eszköz is lehet a IoT Edge átjáró eszközön futó alkalmazás.

A sikeres transzparens átjáró-kapcsolatok létrehozásához három általános lépés szükséges. Ez a cikk a harmadik lépést ismerteti:

1. Az átjáró-eszköznek biztonságosan csatlakoznia kell az alárendelt eszközökhöz, kommunikációt kell fogadnia az alsóbb rétegbeli eszközökről, és továbbítania kell az üzeneteket a megfelelő célhelyre. További információ: [IoT Edge eszköz konfigurálása transzparens átjáróként való](how-to-create-transparent-gateway.md)használatra.
2. Az alsóbb rétegbeli eszköznek rendelkeznie kell egy eszköz identitásával, hogy a hitelesítés a IoT Hub használatával történjen, és tudnia kell kommunikálni az átjáró eszközén keresztül. További információ: [alsóbb rétegbeli eszköz hitelesítése az Azure IoT Hubban](how-to-authenticate-downstream-device.md).
3. **Az alsóbb rétegbeli eszköznek biztonságosan kell csatlakoznia az átjáró eszközéhez.**

Ez a cikk az alsóbb rétegbeli eszközök kapcsolataival kapcsolatos gyakori problémákat ismerteti, és végigvezeti az alsóbb rétegbeli eszközök beállításán:

* A Transport Layer Security (TLS) és a tanúsítvány alapjainak ismertetése.
* Annak ismertetése, hogyan működnek a TLS-kódtárak a különböző operációs rendszereken, és hogy az egyes operációs rendszerek hogyan tárgyalják a tanúsítványokat.
* Ismerkedjen meg az Azure IoT-mintákkal több nyelven, így könnyebben elsajátíthatja az első lépéseket.

Ebben a cikkben az *átjáró* és *IoT Edge átjáró* használati feltételei egy transzparens átjáróként konfigurált IoT Edge-eszközre hivatkoznak.

## <a name="prerequisites"></a>Előfeltételek

* A **Azure-IOT-test-only. root. ca. CERT. PEM** tanúsítványfájl lett létrehozva, amely [egy IoT Edge eszköz konfigurálása](how-to-create-transparent-gateway.md) , amely az alsóbb rétegbeli eszközön elérhető transzparens átjáróként működik. Az alsóbb rétegbeli eszköz ezt a tanúsítványt használja az átjáró-eszköz identitásának ellenőrzéséhez.
* Az átjáró-eszközre mutató módosított kapcsolódási sztringet az [alsóbb rétegbeli eszköz Azure IoT hubba való hitelesítésének leírását](how-to-authenticate-downstream-device.md)ismertető cikkben ismertetett módon kell megtekintenie.

## <a name="prepare-a-downstream-device"></a>Alsóbb rétegbeli eszköz előkészítése

Egy alsóbb rétegbeli eszköz lehet bármely olyan alkalmazás vagy platform, amely rendelkezik az [Azure IoT hub](https://docs.microsoft.com/azure/iot-hub) Cloud Service szolgáltatással létrehozott identitással. Sok esetben ezek az alkalmazások az [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md)-t használják. Egy alsóbb rétegbeli eszköz is lehet a IoT Edge átjáró eszközön futó alkalmazás. Azonban egy másik IoT Edge eszköz nem lehet egy IoT Edge átjárón.

>[!NOTE]
>A IoT Hubban regisztrált identitásokkal rendelkező eszközök [IoT a különböző](../iot-hub/iot-hub-devguide-module-twins.md) folyamatok, hardverek vagy függvények egyetlen eszközön való elkülönítéséhez használhatók. IoT Edge átjárók támogatják az alárendelt modulok kapcsolatait a szimmetrikus kulcsos hitelesítéssel, de X. 509 tanúsítványalapú hitelesítést nem.

Egy alsóbb rétegbeli eszköz IoT Edge átjáróhoz való csatlakoztatásához két dolog szükséges:

* Egy IoT Hub eszköz kapcsolati karakterlánccal konfigurált eszköz vagy alkalmazás, amely az átjáróhoz való kapcsolódáshoz szükséges adatokat fűzi hozzá.

    Ez a lépés [egy alsóbb rétegbeli eszköz Azure-IoT hub való hitelesítését](how-to-authenticate-downstream-device.md)ismerteti.

* Az eszköznek vagy alkalmazásnak meg kell bíznia az átjáró **legfelső szintű hitelesítésszolgáltatói** tanúsítványán, hogy ellenőrizze az ÁTJÁRÓ eszköz TLS-kapcsolatait.

    Ezt a lépést részletesen ismertetjük a cikk további részében. Ez a lépés kétféleképpen végezhető el: a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány telepítésével az operációs rendszer tanúsítványtárolójában vagy (bizonyos nyelveken) az Azure IoT SDK-kat használó alkalmazásokon belüli tanúsítványra hivatkozva.

## <a name="tls-and-certificate-fundamentals"></a>A TLS és a tanúsítvány alapjai

Az alsóbb rétegbeli eszközök IoT Edgehoz való biztonságos csatlakoztatásának kihívása ugyanúgy történik, mint az interneten keresztüli biztonságos ügyfél-/kiszolgáló-kommunikáció. Az ügyfél és a kiszolgáló biztonságos módon kommunikál az interneten keresztül a [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security)protokoll használatával. A TLS-t a standard [nyilvánoskulcs-infrastruktúra (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) által készített szerkezetek nevezik ki. A TLS meglehetősen érintett specifikáció, és a két végpont biztonságossá tételével kapcsolatos témakörök széles körét kezeli. Ez a szakasz az eszközök IoT Edge átjáróhoz való biztonságos csatlakoztatásához szükséges fogalmakat összegzi.

Amikor az ügyfél egy kiszolgálóhoz csatlakozik, a kiszolgáló a tanúsítványok láncát adja meg, amelyet a *kiszolgálói tanúsítvány láncának*nevezünk. A tanúsítványlánc általában egy legfelső szintű hitelesítésszolgáltató (CA) tanúsítványból, egy vagy több közbenső HITELESÍTÉSSZOLGÁLTATÓI tanúsítványból áll, és végül maga a kiszolgáló tanúsítványa. Az ügyfél megbízhatóságot létesít a kiszolgálóval a teljes kiszolgálói tanúsítvány láncának kriptográfiai ellenőrzésével. A kiszolgálói tanúsítvány láncának ezt az ügyfél-érvényesítését a *kiszolgálói lánc érvényesítésének*nevezzük. Az ügyfél kriptográfiailag megküzdi a szolgáltatást, hogy igazolja a tanúsítványhoz tartozó titkos kulcsot egy olyan folyamatban, amelynek a *birtokában bizonyítás*történt. A kiszolgálói lánc érvényesítésének és a birtokon tartás igazolásának kombinációját *kiszolgáló hitelesítésnek*nevezzük. A kiszolgálói tanúsítványlánc érvényesítéséhez az ügyfélnek szüksége van egy olyan legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány másolatára, amely a kiszolgáló tanúsítványának létrehozásához (vagy kiállításához) volt használva. Általában a webhelyekhez való kapcsolódáskor a böngésző előre konfigurálva van a gyakran használt HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokkal, így az ügyfél zökkenőmentes folyamattal rendelkezik.

Amikor egy eszköz csatlakozik az Azure IoT Hubhoz, az eszköz az ügyfél, a IoT Hub Cloud Service pedig a-kiszolgáló. A IoT Hub Cloud Service-t a **Baltimore CyberTrust root**nevű legfelső szintű hitelesítésszolgáltatói tanúsítvány támogatja, amely nyilvánosan elérhető és széles körben használatban van. Mivel a IoT Hub HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány már telepítve van a legtöbb eszközön, számos TLS-implementáció (OpenSSL, Schannel, LibreSSL) automatikusan használja azt a kiszolgálói tanúsítvány érvényesítése során. Előfordulhat, hogy egy olyan eszköz, amely sikeresen kapcsolódott IoT Hubhoz, problémákba ütközik egy IoT Edge-átjáróhoz való csatlakozáskor.

Amikor egy eszköz csatlakozik egy IoT Edge-átjáróhoz, az alárendelt eszköz az ügyfél, és az átjáró-eszköz a-kiszolgáló. Azure IoT Edge lehetővé teszi az operátorok (vagy felhasználók) számára, hogy az átjáró-tanúsítványok láncok számára is megjelenjenek. Az operátor dönthet úgy, hogy nyilvános HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt (például a Baltimore-t) használ, vagy önaláírt (vagy házon belüli) legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használ. A nyilvános HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok gyakran rendelkeznek a velük járó díjakkal, ezért általában éles környezetben használatosak. Az önaláírt HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok fejlesztési és tesztelési célokra ajánlottak. A bevezetésben felsorolt transzparens átjáró beállítási cikkei önaláírt legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat használnak.

Ha egy IoT Edge átjáróhoz önaláírt legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használ, azt az átjáróhoz csatlakozni próbáló összes alsóbb rétegbeli eszközre telepíteni kell, vagy meg kell adni.

![Átjáró tanúsítványának beállítása](./media/how-to-create-transparent-gateway/gateway-setup.png)

Ha többet szeretne megtudni a IoT Edge tanúsítványokról és néhány üzemi hatásról, tekintse meg a [IoT Edge tanúsítvány-használati adatok](iot-edge-certs.md)című témakört.

## <a name="provide-the-root-ca-certificate"></a>Adja meg a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt

Az átjáró-eszköz tanúsítványainak ellenőrzéséhez az alárendelt eszköznek a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány saját példányát kell használnia. Ha a IoT Edge git-tárházban megadott parancsfájlokat használta a tesztelési tanúsítványok létrehozásához, akkor a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány neve **Azure-IOT-test-only. root. ca. CERT. PEM**. Ha még nem tette meg a többi alsóbb rétegbeli eszköz előkészítésének lépéseit, helyezze át a tanúsítványfájl bármely könyvtárba az alsóbb rétegbeli eszközön. Használhat olyan szolgáltatásokat, mint a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) , vagy egy olyan függvény, mint például a [biztonságos másolási protokoll](https://www.ssh.com/ssh/scp/) a tanúsítványfájl áthelyezéséhez.

## <a name="install-certificates-in-the-os"></a>Tanúsítványok telepítése az operációs rendszeren

A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány telepítése az operációs rendszer tanúsítványtárolójában általában lehetővé teszi, hogy a legtöbb alkalmazás a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használja. Vannak kivételek, például olyan NodeJS alkalmazások, amelyek nem használják az operációs rendszer tanúsítványtárolóját, hanem a csomópont-futtatókörnyezet belső tanúsítványtárolóját használják. Ha nem tudja telepíteni a tanúsítványt az operációs rendszer szintjén, ugorjon a [tanúsítványok használata az Azure IoT SDK](#use-certificates-with-azure-iot-sdks)-k használatával című részre.

### <a name="ubuntu"></a>Ubuntu

A következő parancsok egy példa arra, hogyan lehet HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt telepíteni Ubuntu-gazdagépre. Ez a példa azt feltételezi, hogy a **Azure-IOT-test-only. root. ca. CERT. PEM** tanúsítványt használja az előfeltételek cikkeiből, és a tanúsítványt egy helyre másolta az alsóbb rétegbeli eszközön.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

A következő üzenetnek kell megjelennie: "tanúsítványok frissítése a/etc/SSL/certs...-ben 1 hozzáadva, 0 eltávolítva; kész. "

### <a name="windows"></a>Windows

A következő lépések egy példa arra, hogyan telepíthet HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt egy Windows rendszerű gazdagépre. Ez a példa azt feltételezi, hogy a **Azure-IOT-test-only. root. ca. CERT. PEM** tanúsítványt használja az előfeltételek cikkeiből, és a tanúsítványt egy helyre másolta az alsóbb rétegbeli eszközön.

A tanúsítványokat a PowerShell [importálási tanúsítványával](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) rendszergazdaként telepítheti:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

A tanúsítványokat a **certlm** segédprogrammal is telepítheti:

1. A Start menüben keresse meg és válassza a **számítógép-tanúsítványok kezelése**elemet. Megnyílik egy **certlm** nevű segédprogram.
2. Navigáljon a **tanúsítványok – helyi számítógép** > **megbízható legfelső szintű hitelesítésszolgáltatók**elemre.
3. Kattintson a jobb gombbal a **tanúsítványok** elemre, és válassza **az összes feladat** > **Importálás**lehetőséget. A tanúsítvány importálása varázsló elindul.
4. Kövesse az utasításokat a következő módon: utasítás és importálás tanúsítványfájl `<path>/azure-iot-test-only.root.ca.cert.pem`. Ha elkészült, a "sikeres Importálás" üzenetnek kell megjelennie.

A tanúsítványokat programozott módon is telepítheti a .NET API-kkal, ahogy azt a jelen cikk későbbi, a .NET-mintában is látható.

Az alkalmazások általában a [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) nevű Windows-adatkészletet használják a TLS-kapcsolat biztonságos csatlakoztatásához. A Schannel *megköveteli* , hogy a tanúsítványokat a Windows-tanúsítványtárolóba telepítse, mielőtt TLS-kapcsolatot hozna létre.

## <a name="use-certificates-with-azure-iot-sdks"></a>Tanúsítványok használata az Azure IoT SDK-k használatával

Ez a szakasz azt ismerteti, hogy az Azure IoT SDK-k hogyan csatlakozhatnak egy IoT Edge eszközhöz egyszerű példák használatával. Az összes minta célja, hogy összekapcsolja az eszköz ügyfelét, és telemetria üzeneteket küldjön az átjárónak, majd bezárja a kapcsolatot, és kilép.

Az alkalmazás szintű minták használata előtt két dolog áll készen:

* Az alsóbb rétegbeli eszköz IoT Hub kapcsolódási karakterlánca úgy lett módosítva, hogy az átjáró eszközre mutasson, és az alsóbb rétegbeli eszköz hitelesítéséhez szükséges összes tanúsítvány IoT Hub. További információ: [alsóbb rétegbeli eszköz hitelesítése az Azure IoT Hubban](how-to-authenticate-downstream-device.md).

* A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány teljes elérési útja, amelyet az alsóbb rétegbeli eszközön másolt és mentett.

    Például: `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>NodeJS

Ez a szakasz egy Azure IoT NodeJS-eszköz ügyfelének egy IoT Edge átjáróhoz való csatlakoztatására szolgáló minta alkalmazást tartalmaz. A NodeJS alkalmazások esetében az itt látható módon telepítenie kell a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt az alkalmazás szintjén. A NodeJS-alkalmazások nem használják a rendszertanúsítvány-tárolót.

1. A [Node. js-hez készült Azure IoT ESZKÖZOLDALI SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)-ból **edge_downstream_device. js** -hez tartozó minta beszerzése.
2. Ellenőrizze, hogy a **readme.MD** fájl áttekintésével rendelkezik-e a minta futtatásához szükséges összes előfeltételt.
3. A edge_downstream_device. js fájlban frissítse a **ConnectionString** és a **edge_ca_cert_path** változót.
4. A minta eszközön való futtatásával kapcsolatos útmutatásért tekintse meg az SDK dokumentációját.

A futtatott minta megértéséhez a következő kódrészlet az, hogy az ügyfél SDK hogyan olvassa be a tanúsítványfájl, és hogyan használja a biztonságos TLS-kapcsolat létrehozásához:

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Ez a szakasz egy Azure IoT .NET-eszköz ügyfelének egy IoT Edge átjáróhoz való csatlakoztatására szolgáló minta alkalmazást mutat be. A .NET-alkalmazások azonban automatikusan használhatják a rendszer tanúsítványtárolójában telepített tanúsítványokat a Linux-és Windows-gazdagépeken is.

1. Szerezze be a **EdgeDownstreamDevice** mintáját a [IoT Edge .net Samples mappából](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice).
2. Ellenőrizze, hogy a **readme.MD** fájl áttekintésével rendelkezik-e a minta futtatásához szükséges összes előfeltételt.
3. A **Properties/launchSettings. JSON** fájlban frissítse a **DEVICE_CONNECTION_STRING** és **CA_CERTIFICATE_PATH** változókat. Ha a megbízható tanúsítványtárolóban telepített tanúsítványt szeretné használni a gazda rendszeren, hagyja üresen ezt a változót.
4. A minta eszközön való futtatásával kapcsolatos útmutatásért tekintse meg az SDK dokumentációját.

Ha egy .NET-alkalmazás használatával szeretne programozott módon telepíteni egy megbízható tanúsítványt a tanúsítványtárolóban, tekintse meg a **InstallCACert ()** függvényt a **EdgeDownstreamDevice/program.cs** fájlban. Ez a művelet idempotens, így többször is futhat ugyanazzal az értékekkel, további hatás nélkül.

### <a name="c"></a>C#

Ez a szakasz egy Azure IoT C-eszköz ügyfelének egy IoT Edge átjáróhoz való csatlakoztatására szolgáló minta alkalmazást mutat be. A C SDK számos TLS-függvénytárral működhet, beleértve az OpenSSL-t, a WolfSSL-t és a Schannel-t. További információkért lásd az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)-t.

1. Szerezze be a **iotedge_downstream_device_sample** alkalmazást a [C minta Azure IoT Device SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)-ból.
2. Ellenőrizze, hogy a **readme.MD** fájl áttekintésével rendelkezik-e a minta futtatásához szükséges összes előfeltételt.
3. A iotedge_downstream_device_sample. c fájlban frissítse a **ConnectionString** és a **edge_ca_cert_path** változót.
4. A minta eszközön való futtatásával kapcsolatos útmutatásért tekintse meg az SDK dokumentációját.

A C Azure IoT eszközoldali SDK lehetőséget biztosít a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány regisztrálására az ügyfél beállításakor. Ez a művelet bárhol nem telepíti a tanúsítványt, hanem a memóriában lévő tanúsítvány karakterlánc-formátumát használja. A mentett tanúsítványt a rendszer a mögöttes TLS-verem számára a kapcsolatok létrehozásakor kapja meg.

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Windows-gazdagépeken, ha nem használ OpenSSL-t vagy egy másik TLS-függvénytárat, az SDK alapértelmezés szerint a Schannel protokollt használja. Ahhoz, hogy a Schannel működjön, a IoT Edge legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt a Windows tanúsítványtárolóba kell telepíteni, nem a `IoTHubDeviceClient_SetOption` művelettel.

### <a name="java"></a>Java

Ez a szakasz egy Azure IoT Java-eszköz ügyfelének egy IoT Edge átjáróhoz való csatlakoztatására szolgáló minta alkalmazást mutat be.

1. Az [Azure IoT ESZKÖZOLDALI SDK for Java-mintákhoz](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)tartozó **Send-Event** minta beszerzése.
2. Ellenőrizze, hogy a **readme.MD** fájl áttekintésével rendelkezik-e a minta futtatásához szükséges összes előfeltételt.
3. A minta eszközön való futtatásával kapcsolatos útmutatásért tekintse meg az SDK dokumentációját.

### <a name="python"></a>Python

Ez a szakasz egy Azure IoT Python-eszköz ügyfelének egy IoT Edge átjáróhoz való csatlakoztatására szolgáló minta alkalmazást mutat be.

1. Szerezze be a **send_message** mintáját a [Python-mintákhoz készült Azure IoT Device SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios)-val.
2. Győződjön meg arról, hogy egy IoT Edge tárolóban fut, vagy hibakeresési forgatókönyvben a `EdgeHubConnectionString` és a `EdgeModuleCACertificateFile` környezeti változók vannak beállítva.
3. A minta eszközön való futtatásával kapcsolatos útmutatásért tekintse meg az SDK dokumentációját.

## <a name="test-the-gateway-connection"></a>Az átjáró-kapcsolatok tesztelése

Ezzel a minta paranccsal ellenőrizheti, hogy az alsóbb rétegbeli eszköz tud-e csatlakozni az átjáró eszközéhez:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Ez a parancs a MQTTS (8883-es porton) keresztül ellenőrzi a kapcsolatokat. Ha más protokollt használ, módosítsa a parancsot a AMQPS (5671) vagy a HTTPS (433) számára szükséges módon.

A parancs kimenete hosszú lehet, beleértve a láncban lévő összes tanúsítvány adatait is. Ha a sikeres a kapcsolatok, egy sor jelenik meg, például `Verification: OK` vagy `Verify return code: 0 (ok)`.

![Átjáró-kapcsolatok ellenőrzése](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Az átjáró-kapcsolatok hibáinak megoldása

Ha a levélben lévő eszköz időnként csatlakozik az átjáró eszközéhez, próbálkozzon a következő lépésekkel a megoldáshoz.

1. Az átjáró gazdagépének állomásneve megegyezik a (z) IoT Edge config. YAML fájlban található állomásnév értékével az átjáró eszközén?
2. Feloldható az átjáró állomásneve egy IP-címhez? Az időszakos kapcsolatokat a DNS használatával vagy egy gazda-fájl bejegyzésének a levél eszközön való hozzáadásával oldhatja fel.
3. Nyitva vannak a kommunikációs portok a tűzfalon? A használt protokollon alapuló (MQTTS: 8883/AMQPS: 5671/HTTPS: 433) kommunikációnak az alsóbb rétegbeli eszköz és az átlátszó IoT Edge között kell lennie.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan bővítheti IoT Edge az [Offline képességeket](offline-capabilities.md) az alsóbb rétegbeli eszközökre.
