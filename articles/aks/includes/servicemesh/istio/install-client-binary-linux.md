---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: 164844a9da09563f8fbefe7ec60aff7eb05ace2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91666745"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Istio istioctl istemci ikilisini indirme ve yükleme

Linux veya [Linux Için Windows alt sistemi][install-wsl]üzerinde Bash tabanlı bir kabukta, `curl` istio sürümünü indirmek için kullanın ve ardından `tar` aşağıdaki gibi ile ayıklayın:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.7.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-linux-amd64.tar.gz" | tar xz
```

`istioctl`İstemci ikilisi istemci makinenizde çalışır ve AKS kümenize Istio 'yu yüklemenize ve yönetmenize olanak sağlar. `istioctl`İstemci ikilisini Linux veya [Linux Için Windows alt sistemi][install-wsl]üzerinde Bash tabanlı bir kabuğa yüklemek için aşağıdaki komutları kullanın. Bu komutlar, `istioctl` istemci ikilisini içindeki standart Kullanıcı programı konumuna kopyalar `PATH` .

```bash
sudo mv ./istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Istio istemci ikilisi için komut satırı tamamlamayı isterseniz `istioctl` , bunu aşağıdaki şekilde ayarlayın:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: /windows/wsl/install-win10
