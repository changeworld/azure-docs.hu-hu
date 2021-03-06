---
title: Hibaelhárítás – az Azure IoT Edge |} A Microsoft Docs
description: Ebben a cikkben megismerheti standard diagnosztikai képességek az Azure IoT Edge, például összetevő-állapot és a naplók beolvasása, és a leggyakoribb hibák elhárításához
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 98d75f75a985fca3448becab216ad6570d948468
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284823"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Az Azure IoT Edge gyakori problémái és azok megoldásai

Ha a környezetében az Azure IoT Edge futtatásakor problémákat tapasztal, ezt a cikket útmutatóként használhatja a hibaelhárításhoz és a megoldáshoz.

## <a name="run-the-iotedge-check-command"></a>A iotedge "pipa" parancs futtatása

Az első lépés a IoT Edge hibaelhárításakor a `check` parancs használata, amely a konfigurációs és kapcsolati tesztek gyűjteményét futtatja a gyakori problémákhoz. A `check` parancs a [kiadási 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) és újabb verziókban érhető el.

A `check` parancsot az alábbiak szerint futtathatja, vagy belefoglalhatja a `--help` jelzőt a lehetőségek teljes listájának megjelenítéséhez:

* Linux:

  ```bash
  sudo iotedge check
  ```

* Windows rendszeren:

  ```powershell
  iotedge check
  ```

Az eszköz által futtatott ellenőrzések típusai a következőképpen sorolhatók be:

* Konfigurációs ellenőrzések: megvizsgálja azokat az adatokat, amelyek megakadályozzák, hogy az Edge-eszközök csatlakozzanak a felhőhöz, beleértve a *config. YAML* és a Container motorral kapcsolatos problémákat is.
* Kapcsolat ellenőrzése: ellenőrzi, hogy a IoT Edge futtatókörnyezet hozzáférhet-e a gazdagép-eszköz portjaihoz, és hogy az összes IoT Edge-összetevő csatlakozni tud-e a IoT Hubhoz.
* Üzemi készültségi ellenőrzések: az ajánlott éles környezetek, például az eszközök HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa és a modul naplófájljai konfigurációjának állapotát keresi.

A diagnosztikai ellenőrzések teljes listáját a következő témakörben talál: [beépített hibaelhárítási funkció](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="standard-diagnostic-steps"></a>Általános diagnosztikai lépések

Ha problémába ütközik, további információt talál a IoT Edge eszköz állapotáról, ha áttekinti a tároló naplóit, valamint az eszközre és az eszközről továbbított üzeneteket. Az ebben a szakaszban szereplő parancsokkal és eszközökkel további információt gyűjthet.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>A IoT Edge Security Manager és a naplók állapotának ellenõrzése

Linux:

* Az IoT Edge-biztonságkezelő állapotának megtekintése:

   ```bash
   sudo systemctl status iotedge
   ```

* A naplók az IoT Edge-biztonságkezelő megtekintéséhez:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Továbbiak megtekintése részletes naplók az IoT Edge-biztonságkezelő:

  * Szerkessze a iotedge démon beállításokat:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Frissítse a következő sorokat:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Az IoT Edge biztonsági démon újraindításához:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

Windows rendszeren:

* Az IoT Edge-biztonságkezelő állapotának megtekintése:

   ```powershell
   Get-Service iotedge
   ```

* A naplók az IoT Edge-biztonságkezelő megtekintéséhez:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Ha az IoT Edge biztonsági kezelője nem fut, ellenőrizze a yaml-konfigurációs fájl

> [!WARNING]
> A YAML-fájlok nem tartalmazhatnak behúzást tartalmazó lapokat. Használja helyette a 2 szóközöket. A legfelső szintű elemeknek nincsenek kezdő szóközök.

Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows rendszeren:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Ellenőrizze a tároló naplóinak problémák

Miután az IoT Edge biztonsági démon fut, tekintse meg a naplókat a tárolók a hibák észlelése. Indítsa el a telepített tárolókat, majd tekintse meg a IoT Edge futtatókörnyezetet alkotó tárolókat: edgeAgent és edgeHub. A IoT Edge-ügynök naplói általában az egyes tárolók életciklusára vonatkozó információkat biztosítanak. Az IoT Edge hub naplói az üzenetküldéssel és az útválasztással kapcsolatos információkat biztosítanak.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Az IoT Edge hub-on keresztül haladó üzenetek megtekintése

Megtekintheti az IoT Edge hub-on keresztül megjelenő üzeneteket, és bepillantást nyerhet a részletes naplókból a futásidejű tárolóból. Ha be szeretné kapcsolni a részletes naplókat ezeken a tárolókban, állítsa be `RuntimeLogLevel` a YAML konfigurációs fájljába. A fájl megnyitásához:

Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows rendszeren:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Alapértelmezés szerint a `agent` elem az alábbi példához hasonlóan fog kinézni:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

`env: {}` cseréje a (z):

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML-fájlok nem lehetnek identation lapokon. Használja helyette a 2 szóközöket. A legfelső szintű elemek nem rendelkezhetnek kezdő szóközökkel.

Mentse a fájlt, és indítsa újra a IoT Edge-kezelő.

Az IoT Hub és az IoT Edge-eszközök között küldött üzeneteket is ellenőrizheti. A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)-hoz készült Azure IoT hub-bővítmény használatával tekintheti meg ezeket az üzeneteket. További információ: [praktikus eszköz az Azure IoT való fejlesztés során](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Indítsa újra a tárolók

Miután megvizsgálta a naplók és üzenetek információit, próbálja meg újraindítani a tárolók:

```cmd
iotedge restart <container name>
```

Indítsa újra az IoT Edge-futtatókörnyezet tárolóiból:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Indítsa újra az IoT Edge-biztonságkezelő

Ha a probléma továbbra is átlátni, próbálja meg újraindítani az IoT Edge-biztonságkezelő.

Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

Windows rendszeren:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge ügynök egy perc múlva leáll

A edgeAgent modul egy percen keresztül elindul, és sikeresen fut, majd leáll. A naplók azt jelzik, hogy a IoT Edge ügynök megpróbál csatlakozni a AMQP-on keresztüli IoT Hub, majd a AMQP-en keresztül próbál csatlakozni a WebSocket használatával. Ha ez nem sikerül, a IoT Edge ügynök kilép.

Példa edgeAgent-naplókra:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Alapvető ok**

A gazdagép hálózatán a hálózati konfiguráció megakadályozza, hogy a IoT Edge-ügynök elérje a hálózatot. Az ügynök először megpróbál AMQP-n keresztül csatlakozni (az 5671-es porton). Ha a kapcsolódás sikertelen, a websockettel (a 443-as porton).

Az IoT Edge-futtatókörnyezet minden modulon beállít egy-egy hálózatot a kommunikációhoz. Linux rendszeren ez a hálózat egy hídhálózat. Windows rendszeren NAT-ot használ. Ez a probléma gyakoribb a NAT-hálózatot használó Windows-tárolókat igénybe vevő windowsos eszközökön.

**Resolution** (Osztás)

Győződjön meg arról, hogy elérhető egy útvonal az internethez az ehhez a híd-/NAT-hálózathoz rendelt IP-címek esetén. Néha a gazdagépen lévő VPN-konfiguráció felülbírálja az IoT Edge-hálózatot.

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hub nem indul el

A edgeHub modul nem indul el, és a következő üzenetet nyomtatja ki a naplókba:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Alapvető ok**

A gazdagépen egy másik folyamat foglalja le a 443-as portot. Az IoT Edge hub a 5671-es és 443-es portokat a Gateway-forgatókönyvekben való használatra. Ez a portleképezés sikertelen, ha egy másik folyamat már lefoglalta a portot.

**Resolution** (Osztás)

Keresse meg, és állítsa le a 443-as portot használó folyamatot. Ez a folyamat általában a webkiszolgáló.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge ügynök nem fér hozzá a modul rendszerképéhez (403)

Egy tároló nem fut, és a edgeAgent-naplók 403 hibát jeleznek.

**Alapvető ok**

A IoT Edge ügynöknek nincs engedélye a modul rendszerképének elérésére.

**Resolution** (Osztás)

Győződjön meg arról, hogy a tárolójegyzék hitelesítő adatainak helyesen vannak megadva a manifest nasazení

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge biztonsági démon meghiúsul, és a egy érvénytelen állomásnév

A parancs `sudo journalctl -u iotedge` sikertelen, és kiírja a következő üzenetet:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Alapvető ok**

Az IoT Edge-futtatókörnyezet csak támogatja, amelyek 64 karakternél rövidebb gazdanévvel. Fizikai gépek általában nem rendelkezik hosszú állomásnevek, de a probléma gyakoribb a virtuális gépen. Az automatikusan létrehozott gazdanevek Windows virtuális gépek az Azure-ban üzemeltetett, általában hosszú.

**Resolution** (Osztás)

Ha ezt a hibát látja, feloldhatja konfigurálásával a virtuális gép DNS-nevét, és beállítja a DNS-nevét a setup parancs az állomásnevet.

1. Az Azure Portalon lépjen a virtuális gép áttekintés oldalán.
2. Válassza a **Konfigurálás** a DNS-név alatt lehetőséget. Ha a virtuális géphez már tartozik egy DNS-név konfigurálva, nem kell egy új konfigurálása.

   ![Konfigurálja a virtuális gép DNS-neve](./media/troubleshoot/configure-dns.png)

3. Adjon meg egy értéket a **DNS-név címkéhez** , majd válassza a **Mentés**lehetőséget.
4. Másolja az új DNS-nevet, amelynek formátuma **\<DNSnamelabel\>.\<jelenlegi\>. cloudapp.Azure.com**.
5. A virtuális gépen belül a következő parancs használatával állítsa be az IoT Edge-futtatókörnyezet, a DNS-névvel:

   * Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * Windows rendszeren:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Erőforrás stabilitási problémák korlátozott eszközök

Felmerülhet korlátozott eszközökön, például a Raspberry Pi-októl stabilitását, különösen akkor, ha az átjáróként használt. Memória kivételeket az edge hub modul kívül tünetei, alsóbb rétegbeli eszközök nem csatlakoznak, vagy az eszköz nem a telemetriai üzeneteket küld a néhány óra múlva.

**Alapvető ok**

A IoT Edge futtatókörnyezet részét képező IoT Edge hub alapértelmezés szerint a teljesítményre van optimalizálva, és nagy mennyiségű memóriát próbál lefoglalni. Az optimalizálás nem ideális korlátozott peremhálózati eszközökre, és stabilitását problémákat okozhat.

**Resolution** (Osztás)

Az IoT Edge hub esetében állítsa be a **OptimizeForPerformance** környezeti változót **hamis**értékre. A környezeti változók két módon állíthatók be:

Az Azure Portalon:

A IoT Hub válassza ki a IoT Edge eszközt, majd az eszköz adatai lapon válassza a **modulok beállítása** > **futtatókörnyezet beállításai**lehetőséget. Hozzon létre egy környezeti változót a *OptimizeForPerformance* nevű Edge hub-modulhoz, amely *hamis*értékre van beállítva.

![OptimizeForPerformance "false" értékűre.](./media/troubleshoot/optimizeforperformance-false.png)

**VAGY**

A manifest nasazení:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Nem olvasható be az IoT Edge démon naplóit a Windows

Ha a Windows `Get-WinEvent` használatakor EventLogException kap, ellenőrizze a beállításjegyzék bejegyzéseit.

**Alapvető ok**

A `Get-WinEvent` PowerShell-parancs egy olyan beállításjegyzék-bejegyzésre támaszkodik, amely egy adott `ProviderName`által naplókat keres.

**Resolution** (Osztás)

Állítsa be az IoT Edge-démon egy beállításjegyzék-bejegyzést. Hozzon létre egy **iotedge. reg** fájlt a következő tartalommal, és importálja a Windows beállításjegyzékbe úgy, hogy duplán rákattint rá, vagy használja a `reg import iotedge.reg` parancsot:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>IoT Edge-modul egy üzenet küldéséhez az edgeHub 404-es hibaüzenettel meghiúsul

Egy egyéni IoT Edge modul nem tud üzenetet küldeni a edgeHub egy 404 `Module not found` hibával. Az IoT Edge-démon a naplókat az alábbi üzenetet jelenít meg:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Alapvető ok**

Az IoT Edge-démon érvényesíti a folyamat azonosítóját összes modult a biztonsági okokból edgeHub csatlakozik. Ellenőrzi, hogy egy modul által küldött összes üzenet származik-e a fő a modul Folyamatazonosítója. Egy üzenet egy másik folyamat azonosítója, mint az eredetileg létrehozott a modul által küldött, ha azt az üzenetet a 404-es hibaüzenettel elutasítja.

**Resolution** (Osztás)

A verzió 1.0.7 kezdve az összes modul-folyamat jogosult a kapcsolódásra. Ha a 1.0.7-re való frissítés nem lehetséges, végezze el a következő lépéseket. További információ: a [1.0.7 kiadásának changelog](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Győződjön meg arról, hogy az azonos Folyamatazonosító mindig használják az egyéni IoT Edge-modul üzeneteket küldeni a edgeHub. Tegyük fel például, hogy `ENTRYPOINT` a Docker-fájlban `CMD` parancs helyett, mivel a `CMD` egy folyamat AZONOSÍTÓját fogja eredményezni a modulhoz, és egy másik folyamat-azonosítót a fő programot futtató bash-parancshoz, míg a `ENTRYPOINT` egyetlen folyamat AZONOSÍTÓját fogja eredményezni.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Üzemelő IoT Edge-példány konfigurációs szabályokat tűzfal- és portbeállítások

Azure IoT Edge lehetővé teszi a helyszíni kiszolgálóról az Azure-felhőbe való kommunikációt a támogatott IoT Hub protokollok használatával: [kommunikációs protokoll kiválasztása](../iot-hub/iot-hub-devguide-protocols.md). A fokozott biztonság érdekében a kommunikációs csatornák között az Azure IoT Edge és az Azure IoT Hub mindig konfigurálni kell a kimenő. Ez a konfiguráció a szolgáltatások által [támogatott kommunikációs minta](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)alapján történik, ami lekicsinyíti a támadási felületet egy rosszindulatú entitás számára a felderítéshez. Bejövő kommunikáció csak akkor szükséges, ahol az Azure IoT Hub kell üzenetek leküldése az Azure IoT Edge-eszköz bizonyos forgatókönyvek esetén. Felhőből az eszközre irányuló üzenetek biztonságos TLS-csatorna használatával véd, és további segítségével biztosítható X.509-tanúsítványokat és a TPM-eszköz modulok. A Azure IoT Edge Security Manager szabályozza, hogyan lehet létrehozni a kommunikációt: [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

IoT Edge biztosít továbbfejlesztett konfigurálása az Azure IoT Edge-futtatókörnyezet, és üzembe helyezett modulokat, az továbbra is függ az alapul szolgáló machine és a hálózati konfiguráció. Ezért fontos, hogy a megfelelő hálózati és tűzfalszabályok a Felhőbeli kommunikáció biztonságos peremén legyenek beállítva. A következő táblázat használható a konfigurációs tűzfalszabályok azon alapuló kiszolgálókon való konfigurálásához, amelyeken a Azure IoT Edge Runtime fut:

|Protokoll|Port|bejövő|Kimenő|Útmutatás|
|--|--|--|--|--|
|MQTT|8883|TILTOTT (alapértelmezett)|TILTOTT (alapértelmezett)|<ul> <li>Konfigurálja a kimenő (kimenő) kell nyílt, amikor az MQTT protokoll használatával.<li>az MQTT 1883 IoT Edge által nem támogatott. <li>Bejövő (bejövő) kapcsolatok le kell tiltani.</ul>|
|AMQP|5671|TILTOTT (alapértelmezett)|NYÍLT (alapértelmezett)|<ul> <li>Alapértelmezett kommunikációs protokollt az IoT Edge-hez. <li> Nyissa meg kell, ha más támogatott protokollok nincs konfigurálva az Azure IoT Edge vagy az AMQP protokoll kívánt kell konfigurálni.<li>az AMQP 5672 IoT Edge által nem támogatott.<li>Tiltsa le ezt a portot, ha az Azure IoT Edge használja egy másik IoT Hub protokoll támogatott.<li>Bejövő (bejövő) kapcsolatok le kell tiltani.</ul></ul>|
|HTTPS|443|TILTOTT (alapértelmezett)|NYÍLT (alapértelmezett)|<ul> <li>Konfigurálja a kimenő (kimenő) kell nyissa meg a 443-as kiépítése az IoT Edge. Ez a konfiguráció manuális parancsprogramokkal vagy az Azure IoT Device Provisioning Service (DPS) szükség. <li>Bejövő (bejövő) kapcsolatot kell lennie a nyílt csak az adott forgatókönyveket: <ul> <li>  Ha a levéleszközök is küldhet kéréseket metódus, amely transzparens átjáró. Ebben az esetben 443-as portot nem kell megnyitni a külső hálózatokhoz IoTHub csatlakozni, vagy adja meg az IoTHub-szolgáltatások az Azure IoT Edge segítségével. Így a bejövő szabály csak megnyitásához a belső hálózatról bejövő (bejövő) korlátozott lehet. <li> -Ügyfél (C2D) eszközök esetén.</ul><li>IoT Edge által nem támogatott a 80-as HTTP-hez.<li>Ha nem HTTP-protokoll (például AMQP és MQTT) nem konfigurálható a vállalat; az üzenetek küldhetők a websockets protokoll. 443-as portot ebben az esetben WebSocket-kommunikációhoz fogja használni.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Az Edge Agent modul folyamatosan jelentést készít az "üres konfigurációs fájlról", és egyetlen modul sem indul el az eszközön.

Az eszköz nem rendelkezik a telepítésben definiált modulok indításával. Csak a edgeAgent fut, de folyamatosan jelentést készít az "üres konfigurációs fájlról...".

**Alapvető ok**

Alapértelmezés szerint a IoT Edge elindítják a modulokat a saját elkülönített tároló hálózatában. Előfordulhat, hogy az eszköz problémát észlelt a DNS-névfeloldás ezen a magánhálózaton belül.

**Resolution** (Osztás)

**1. lehetőség: DNS-kiszolgáló beállítása a tároló motorjának beállításaiban**

Adja meg a környezethez tartozó DNS-kiszolgálót a tároló motorjának beállításaiban, amelyek a motor által indított összes tároló modulra érvényesek lesznek. Hozzon létre egy `daemon.json` nevű fájlt a használni kívánt DNS-kiszolgáló megadásával. Például:

```json
{
    "dns": ["1.1.1.1"]
}
```

A fenti példa egy nyilvánosan elérhető DNS-szolgáltatásra állítja be a DNS-kiszolgálót. Ha a peremhálózati eszköz nem fér hozzá ehhez az IP-címhez a környezetében, cserélje le az elérhető DNS-kiszolgáló címére.

`daemon.json` elhelyezése a platform megfelelő helyén:

| Platform | Hely |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows-gazdagép Windows-tárolókkal | `C:\ProgramData\iotedge-moby\config` |

Ha a hely már tartalmaz `daemon.json` fájlt, adja hozzá a **DNS-** kulcsot, és mentse a fájlt.

A frissítések érvénybe léptetéséhez indítsa újra a tároló motorját.

| Platform | Parancs |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (rendszergazdai PowerShell) | `Restart-Service iotedge-moby -Force` |

**2. lehetőség: a DNS-kiszolgáló beállítása IoT Edge üzembe helyezés modulban**

A DNS-kiszolgáló minden modul *createOptions* beállítható a IoT Edge üzemelő példányban. Például:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Ügyeljen arra, hogy ezt a konfigurációt a *edgeAgent* és a *edgeHub* modulhoz is állítsa be.

## <a name="next-steps"></a>Következő lépések

Úgy gondolja, hogy hibát talált az IoT Edge platformon? [Küldjön el egy problémát](https://github.com/Azure/iotedge/issues) , hogy tovább javítsuk.

Ha további kérdései vannak, hozzon létre egy [support Request](https://portal.azure.com/#create/Microsoft.Support) segítséget.
