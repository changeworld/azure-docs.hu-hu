---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 74f5b22ccc822a188059b29d9c661a15cf8412bf
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593970"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Töltse le és telepítse a Istio istioctl-ügyfél bináris fájlját

A MacOS rendszerű bash-alapú rendszerhéjban `curl` használatával töltse le a Istio-kiadást, majd bontsa ki a `tar` a következő módon:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

A `istioctl`-ügyfél bináris fájlja fut az ügyfélszámítógépen, és lehetővé teszi a Istio szolgáltatás hálójának kezelését. A következő parancsokkal telepítheti a Istio `istioctl`-ügyfél bináris fájlját egy bash-alapú rendszerhéjba MacOS rendszeren. Ezek a parancsok a `istioctl` ügyfél bináris fájljait a `PATH`normál felhasználói program mappájába másolják.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Ha a Istio parancssori kiegészítést szeretne végrehajtani `istioctl` ügyfél bináris fájlját, a következőképpen állítsa be:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```