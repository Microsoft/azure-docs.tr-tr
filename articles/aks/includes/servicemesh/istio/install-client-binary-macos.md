---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: d812baa15380b7824deaed081d2857fbc6e0b1b6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170885"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Istio istioctl istemci ikilisini indirme ve yükleme

MacOS üzerinde Bash tabanlı bir kabukta, Istio sürümünü indirmek için `curl` kullanın ve ardından `tar` ile aşağıdaki şekilde ayıklayın:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

İstemci ikili dosyası `istioctl` istemci makinenizde çalışır ve bu hizmet ağıyla etkileşim kurmanıza olanak tanır. MacOS `istioctl` istemci ikilisini MacOS 'da Bash tabanlı bir kabuğa yüklemek için aşağıdaki komutları kullanın. Bu komutlar `istioctl` istemci ikilisini `PATH`standart Kullanıcı programı konumuna kopyalar.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Istio `istioctl` istemci ikilisi için komut satırı tamamlamayı tercih ediyorsanız, bunu aşağıdaki şekilde ayarlayın:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```