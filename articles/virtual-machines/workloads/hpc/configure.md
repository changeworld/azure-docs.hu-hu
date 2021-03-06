---
title: Nagy teljesítményű feldolgozás – az Azure Virtual Machines |} A Microsoft Docs
description: Tudnivalók a nagy teljesítményű feldolgozás, az Azure-ban.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707827"
---
# <a name="optimization-for-linux"></a>Optimalizálás Linuxra

Ez a cikk bemutatja néhány kulcsfontosságú technikák optimalizálhatja az operációs rendszer lemezképe. Tudjon meg többet [InfiniBand engedélyezése](enable-infiniband.md) és optimalizálása az operációsrendszer-lemezképeket.

## <a name="update-lis"></a>LIS frissítése

Üzembe (például egy régebbi operációs rendszer például a CentOS vagy RHEL 7.4 vagy 7.5) egyéni rendszerkép használatával, frissítse a virtuális gép LIS.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Memória felszabadítása

Hatékonyságnövelés által automatikusan igényelni a memória a távoli memória-hozzáférés elkerülése érdekében.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Ez a virtuális gép újraindítása után is fennállnak legyen:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Tűzfal- és SELinux letiltása

Tiltsa le tűzfal- és SELinux.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Cpupower letiltása

Tiltsa le a cpupower.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [InfiniBand engedélyezése](enable-infiniband.md) és optimalizálása operációsrendszer-lemezképeket.

* Tudjon meg többet [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) az Azure-ban.
