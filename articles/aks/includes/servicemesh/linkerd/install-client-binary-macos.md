---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593722"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Töltse le és telepítse a Linkerd Linkerd-ügyfél bináris fájlját

A MacOS rendszerű bash-alapú rendszerhéjban a következő módon töltheti le az Linkerd-kiadást a `curl` használatával:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

A `linkerd`-ügyfél bináris fájlja fut az ügyfélszámítógépen, és lehetővé teszi a Linkerd szolgáltatás hálójának kezelését. A következő parancsokkal telepítheti a Linkerd `linkerd`-ügyfél bináris fájlját egy bash-alapú rendszerhéjba MacOS rendszeren. Ezek a parancsok a `linkerd` ügyfél bináris fájljait a `PATH`normál felhasználói program mappájába másolják.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Ha a Linkerd parancssori kiegészítést szeretne végrehajtani `linkerd` ügyfél bináris fájlját, a következőképpen állítsa be:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
