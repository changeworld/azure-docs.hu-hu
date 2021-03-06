---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 10fb9e8169b7f4159ccbf4a0ff36021f6033f811
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552417"
---
Az oktatóanyag teljesítéséhez a következők szükségesek:

**Azure CLI**: Az Azure CLI 2.0.29-es vagy újabb verziójának telepítve kell lennie a helyi számítógépen. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését][azure-cli-install] ismertető cikket.

**Docker**: Az oktatóanyag feltételezi, hogy rendelkezik a Docker fő fogalmaira, például a tárolókra, tárolórendszerképekre és az alapszintű `docker`-parancsokra vonatkozó alapvető ismeretekkel. A Docker és a Container alapjairól a [Docker áttekintésében][docker-get-started]talál további információt.

**Docker**: az oktatóanyag elvégzéséhez helyileg kell telepíteni a Docker-t. A Docker csomagokat biztosít a Docker-környezet konfigurálásához [MacOS][docker-mac], Windows és [Linux][docker-linux] [rendszereken][docker-windows].

> [!IMPORTANT]
> Mivel az Azure Cloud Shell nem tartalmazza a Docker-démont, ezért az oktatóanyag teljesítéséhez telepíteni *kell* a *helyi számítógépen* az Azure CLI-t és a Docker Engine-t. Ehhez az oktatóanyaghoz nem használható az Azure Cloud Shell.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
