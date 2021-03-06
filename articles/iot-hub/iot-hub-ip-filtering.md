---
title: Azure IoT Hub IP-kapcsolatok szűrőinek | Microsoft Docs
description: Az IP-szűrés használata az adott IP-címek és az Azure IoT hub közötti kapcsolatok blokkolására. Letilthatja az egyes vagy IP-címtartományok kapcsolatait.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2017
ms.author: robinsh
ms.openlocfilehash: a6bd8a766f3205358a65ef2fd0816643e4261cab
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414284"
---
# <a name="use-ip-filters"></a>IP-szűrők használata

A biztonság az Azure IoT Hub-alapú IoT-megoldások fontos aspektusa. Időnként explicit módon meg kell adnia azokat az IP-címeket, amelyekről az eszközök a biztonsági konfiguráció részeként csatlakozni tudnak. Az *IP-szűrési* funkció lehetővé teszi, hogy szabályokat konfiguráljon a megadott IPv4-címekről érkező forgalom elutasítására vagy fogadására.

## <a name="when-to-use"></a>A következő esetekben használja

A IoT Hub végpontok bizonyos IP-címekhez való letiltásához két konkrét használati eset van:

* Az IoT hub csak a megadott IP-címtartományból érkező forgalmat fogadja, és minden mást visszautasít. Például az IoT hub és az [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) használatával privát kapcsolatokat hozhat létre az IoT hub és a helyszíni infrastruktúra között.

* El kell utasítania azokat az IP-címekről érkező forgalmat, amelyeket az IoT hub rendszergazdája gyanúsnak talált.

## <a name="how-filter-rules-are-applied"></a>Szűrési szabályok alkalmazása

Az IP-szűrési szabályok a IoT Hub szolgáltatási szinten lesznek alkalmazva. Ezért az IP-szűrési szabályok az eszközök és a háttérbeli alkalmazások összes kapcsolatára érvényesek bármely támogatott protokoll használatával.

Minden olyan IP-címről érkező kapcsolódási kísérlet, amely megfelel az IoT hub elutasító IP-szabályának, a rendszer jogosulatlanul 401 állapotkódot és leírást kap. A válaszüzenet nem említi az IP-szabályt.

## <a name="default-setting"></a>Alapértelmezett beállítás

Alapértelmezés szerint a IoT hub-portálon található **IP-szűrő** rács üres. Ez az alapértelmezett beállítás azt jelenti, hogy a hub bármely IP-címről fogad kapcsolatokat. Ez az alapértelmezett beállítás megegyezik egy szabályt, amely elfogadja a 0.0.0.0/0 IP-címtartományt.

![Alapértelmezett IP-szűrési beállítások IoT Hub](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>IP-szűrési szabály hozzáadása vagy szerkesztése

IP-szűrési szabály hozzáadásához válassza az **+ IP-szűrési szabály hozzáadása**lehetőséget.

![IP-szűrési szabály hozzáadása egy IoT hubhoz](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

Az **IP-szűrési szabály hozzáadása**lehetőség kiválasztását követően töltse ki a mezőket.

![Az IP-szűrési szabály hozzáadása lehetőség kiválasztását követően](./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png)

* Adja meg az IP-szűrési szabály **nevét** . Ennek egyedi, kis-és nagybetűket nem megkülönböztető, alfanumerikus sztringnek kell lennie legfeljebb 128 karakter hosszú lehet. Csak az ASCII 7 bites alfanumerikus karaktereket és `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` a rendszer fogadja el.

* Adjon meg egyetlen IPv4-címet vagy IP-címet a CIDR-jelölésben. Például a CIDR 192.168.100.0/22 jelölése 1024 a 192.168.100.0 és a 192.168.103.255 közötti IPv4-címeket jelöli.

* Válassza az **Engedélyezés** vagy a **Letiltás** lehetőséget az IP-szűrési szabály **művelete** elemnél.

A mezők kitöltése után kattintson a **Mentés** gombra a szabály mentéséhez. Megjelenik egy riasztás, amely értesíti, hogy a frissítés folyamatban van.

![Értesítés IP-szűrési szabály mentéséről](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

A **Hozzáadás** lehetőség le van tiltva, amikor eléri a legfeljebb 10 IP-szűrési szabályt.

Meglévő szabály szerkesztéséhez válassza ki a módosítani kívánt adatait, végezze el a módosítást, majd válassza a **Mentés** lehetőséget a Szerkesztés mentéséhez.

> [!NOTE]
> Az IP-címek elutasítása megakadályozhatja, hogy más Azure-szolgáltatások (például a Azure Stream Analytics, az Azure Virtual Machines vagy a portálon lévő Device Explorer) az IoT hub használatával legyenek kommunikálva.

> [!WARNING]
> Ha Azure Stream Analytics (ASA) használatával olvas be üzeneteket egy olyan IoT-hubhoz, amelyen engedélyezve van az IP-szűrés, használja a IoT Hub Event hub-kompatibilis nevét és végpontját az ASA-kapcsolati karakterláncban.

## <a name="delete-an-ip-filter-rule"></a>IP-szűrési szabály törlése

Ha törölni szeretne egy IP-szűrési szabályt, válassza a Kuka ikont az adott sorban, majd válassza a **Mentés**lehetőséget. A szabály el lett távolítva, és a módosítás mentve lesz.

![IoT Hub IP-szűrési szabály törlése](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>IP-szűrők beolvasása és frissítése az Azure CLI-vel

A IoT Hub IP-szűrőinek lekérhető és frissíthető az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)-n keresztül.

A IoT Hub aktuális IP-szűrőinek lekéréséhez futtassa a következőt:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Ez egy JSON-objektumot ad vissza, amelyben a meglévő IP-szűrők a `properties.ipFilterRules` kulcs alatt vannak felsorolva:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Ha új IP-szűrőt szeretne hozzáadni a IoT Hubhoz, futtassa a következőt:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

A IoT Hub meglévő IP-szűrőjének eltávolításához futtassa a következőt:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Vegye figyelembe `<ipFilterIndexToRemove>` , hogy az IP-szűrők sorrendjét meg kell egyeznie `properties.ipFilterRules`a IoT hub.

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>IP-szűrők beolvasása és frissítése Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A IoT Hub IP-szűrőinek lekérhető és beállítható [Azure PowerShell](/powershell/azure/overview).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>IP-szűrési szabályok frissítése a REST használatával

A IoT Hub IP-szűrőjét az Azure erőforrás-szolgáltató REST-végpontjának használatával is lekérheti és módosíthatja. Lásd `properties.ipFilterRules` : a [createorupdate metódusban](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>IP-szűrési szabály értékelése

Az IP-szűrési szabályok a sorrendben lesznek alkalmazva, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletet.

Ha például a 192.168.100.0/22-es tartományba szeretne címeket fogadni, és minden mást visszautasít, a rács első szabályának el kell fogadnia a 192.168.100.0/22 címtartományt. A következő szabályt kell utasítania összes címet a tartomány 0.0.0.0/0 használatával.

Az IP-szűrési szabályok sorrendjét megváltoztathatja a rácson úgy, hogy a sorok elején lévő három függőleges pontra kattint, és a drag and drop parancsot használja.

Az új IP-szűrési szabály sorrendjének mentéséhez kattintson a **Mentés**gombra.

![IoT Hub IP-szűrési szabályok sorrendjének módosítása](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>További lépések

A IoT Hub képességeinek további megismeréséhez lásd:

* [Műveletek figyelése](iot-hub-operations-monitoring.md)
* [IoT Hub metrikák](iot-hub-metrics.md)
