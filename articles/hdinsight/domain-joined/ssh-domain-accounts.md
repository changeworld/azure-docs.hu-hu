---
title: Tartományi fiókok SSH-hozzáférésének kezelése az Azure HDInsight
description: Az Azure AD-fiókokhoz való SSH-hozzáférés kezelésének lépései a HDInsight-ben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472517"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Tartományi fiókok SSH-hozzáférésének kezelése az Azure HDInsight

A biztonságos fürtökön alapértelmezés szerint az [Azure AD DS](../../active-directory-domain-services/overview.md) összes tartományi felhasználója [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) -t használhat a fő és a peremhálózati csomópontok számára. Ezek a felhasználók nem tartoznak a sudoers csoportba, és nem kapják meg a rendszergazdai hozzáférést. A fürt létrehozása során létrehozott SSH-felhasználó rendszergazdai jogosultságokkal fog rendelkezni.

## <a name="manage-access"></a>Hozzáférés kezelése

Az egyes felhasználókhoz vagy csoportokhoz való SSH-hozzáférés módosításához frissítse `/etc/ssh/sshd_config` az egyes csomópontokon.

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Nyissa meg a `ssh_confi`g fájlt.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Módosítsa a `sshd_config` fájlt igény szerint. Ha bizonyos csoportokra korlátozza a felhasználókat, akkor a helyi fiókok nem tudnak SSH-t felvenni ebbe a csomópontba. A következő szintaxis csak egy példát mutat be:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Ezután mentse a módosításokat: **CTRL + X**, **Y**, **ENTER**.

1. Az sshd újraindítása.

    ```bash
    sudo systemctl restart sshd
    ```

1. Ismételje meg a fenti lépéseket az egyes csomópontokon.

## <a name="ssh-authentication-log"></a>SSH-hitelesítési napló

Az SSH-hitelesítési napló `/var/log/auth.log`ba íródik. Ha a helyi vagy tartományi fiókoknál az SSH-n keresztül jelentkeznek bejelentkezési hibák, a hibák hibakereséséhez át kell lépnie a naplóban. Gyakran előfordul, hogy a probléma egy adott felhasználói fiókhoz kapcsolódik, és általában az alapértelmezett SSH-felhasználó (helyi fiók) használatával próbálkozik más felhasználói fiókokkal vagy SSH-val, majd megpróbál egy kinit parancsot.

## <a name="ssh-debug-log"></a>SSH-hibakeresési napló

A részletes naplózás engedélyezéséhez újra kell indítania a `sshd`t a `-d` kapcsolóval. A `/usr/sbin/sshd -d`hoz hasonlóan a `sshd` is futtatható egyéni porton (például 2222), így nem kell leállítania a fő SSH démont. Az SSH-ügyféllel `-v` lehetőség is használható, hogy több naplót kapjon (a hibák ügyféloldali nézete).

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürtök kezelése Enterprise Security Package](./apache-domain-joined-manage.md)
* [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md).
