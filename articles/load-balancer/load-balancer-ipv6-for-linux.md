---
title: A DHCPv6 konfigurálása Linux rendszerű virtuális gépekhez
titleSuffix: Azure Load Balancer
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a DHCPv6-t Linux rendszerű virtuális gépekhez.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6-alapú, az azure load balancer, kettős verem, nyilvános IP-cím, natív ipv6, mobil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: 6ea215b6aa826231e940f88c3687bb65591303f2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225325"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>A DHCPv6 konfigurálása Linux rendszerű virtuális gépekhez


Az Azure piactéren elérhető Linuxos virtuálisgép rendszerképek némelyike nem rendelkezik a Dynamic Host Configuration Protocol version 6 (DHCPv6) alapértelmezés szerint konfigurálva. Az IPv6 támogatása érdekében a Linux operációs rendszer terjesztési használt DHCPv6 kell konfigurálni. A Linux-disztribúciókat DHCPv6 mivel használ a különböző csomagok többféle módon konfigurálható.

> [!NOTE]
> Legutóbbi SUSE Linux és a CoreOS rendszerképek az Azure piactéren elérhető előre konfigurált DHCPv6 törölték. Nincsenek további módosítások szükségesek, ezek a lemezképek használata esetén.

Ez a dokumentum ismerteti a DHCPv6 engedélyezése, hogy a Linux rendszerű virtuális gép IPv6-címet kéri le.

> [!WARNING]
> Nem megfelelően hálózati konfigurációs fájlok szerkesztésével, a virtuális géphez elveszíti a hálózati hozzáférést. Azt javasoljuk, hogy a konfigurációs módosítások nem termelő rendszerei tesztelése. A jelen cikkben lévő utasítások a Linux-rendszerképeket az Azure piactéren elérhető legújabb verzióiban teszteltük. Részletesebb útmutatásért tekintse meg a saját Linux-verzióhoz kapcsolódó dokumentációt.

## <a name="ubuntu"></a>Ubuntu

1. Szerkessze a */etc/DHCP/dhclient6.conf* fájlt, és adja hozzá a következő sort:

        timeout 10;

2. Szerkessze a hálózati konfigurációt a eth0 adapter a következő beállításokkal:

   * **Ubuntu 12,04 és 14,04**esetén szerkessze a */etc/network/interfaces.d/eth0.cfg* fájlt. 
   * Az **Ubuntu 16,04**-ben szerkessze a */etc/network/interfaces.d/50-Cloud-init.cfg* fájlt.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```
Az Ubuntu 17,10-es verziójától kezdve az alapértelmezett hálózati konfigurációs mechanizmus a [NETPLAN]( https://netplan.io).  A telepítés/létrehozás ideje alatt a NETPLAN beolvassa a hálózati konfigurációt a YAML konfigurációs fájljairól ezen a helyen:/{lib, etc, Run}/netplan/*. YAML.

Adja meg a *dhcp6: true* utasítást a konfigurációban található minden Ethernet-adapterhez.  Például:
  
        network:
          version: 2
          ethernets:
            eno1:
              dhcp6: true

A korai rendszerindítás során a "hálózati leképező" netplan úgy írja be a konfigurációt, hogy/Run az eszközök vezérlését a megadott hálózati démonnak a NETPLAN vonatkozó hivatkozási információkkal kapcsolatban: https://netplan.io/reference.
 
## <a name="debian"></a>Debian

1. Szerkessze a */etc/DHCP/dhclient6.conf* fájlt, és adja hozzá a következő sort:

        timeout 10;

2. Szerkessze a */etc/network/interfaces* fájlt, és adja hozzá a következő konfigurációt:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS és Oracle Linux

1. Szerkessze a */etc/sysconfig/network* fájlt, és adja hozzá a következő paramétert:

        NETWORKING_IPV6=yes

2. Szerkessze a */etc/sysconfig/network-scripts/ifcfg-eth0* fájlt, és adja hozzá a következő két paramétert:

        IPV6INIT=yes
        DHCPV6C=yes

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 és openSUSE 13

Legutóbbi SUSE Linux Enterprise Server (SLES) és openSUSE lemezképek az Azure-ban előre konfigurált DHCPv6 törölték. Nincsenek további módosítások szükségesek, ezek a lemezképek használata esetén. Ha egy virtuális Gépet egy régebbi vagy egyéni SUSE kép alapján, tegye a következőket:

1. Szükség esetén telepítse a `dhcp-client` csomagot:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Szerkessze a */etc/sysconfig/network/ifcfg-eth0* fájlt, és adja hozzá a következő paramétert:

        DHCLIENT6_MODE='managed'

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 rendszert, és azt openSUSE

Legutóbbi SLES és openSUSE-rendszerképeket az Azure-ban előre konfigurált DHCPv6 törölték. Nincsenek további módosítások szükségesek, ezek a lemezképek használata esetén. Ha egy virtuális Gépet egy régebbi vagy egyéni SUSE kép alapján, tegye a következőket:

1. Szerkessze a */etc/sysconfig/network/ifcfg-eth0* fájlt, és cserélje le a `#BOOTPROTO='dhcp4'` paramétert a következő értékre:

        BOOTPROTO='dhcp'

2. A */etc/sysconfig/network/ifcfg-eth0* -fájlhoz adja hozzá a következő paramétert:

        DHCLIENT6_MODE='managed'

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Legutóbbi CoreOS rendszerképek az Azure-ban előre konfigurált DHCPv6 törölték. Nincsenek további módosítások szükségesek, ezek a lemezképek használata esetén. Ha egy régebbi vagy egyéni CoreOS lemezképen alapuló virtuális Gépet, tegye a következőket:

1. Szerkessze a */etc/systemd/network/10_dhcp. Network* fájlt:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Újítsa meg az IPv6-cím:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
