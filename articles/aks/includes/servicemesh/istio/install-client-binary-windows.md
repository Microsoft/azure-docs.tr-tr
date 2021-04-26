---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: a02c17013a205ccc0da85536b491d467ef72fa48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91666735"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Istio istioctl istemci ikilisini indirme ve yükleme

Windows üzerinde PowerShell tabanlı bir kabukta, `Invoke-WebRequest` Istio sürümünü indirmek için kullanın ve ardından aşağıdaki gibi ile ayıklayın `Expand-Archive` :

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.7.3"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-win.zip" -OutFile "istioctl-$ISTIO_VERSION.zip"
Expand-Archive -Path "istioctl-$ISTIO_VERSION.zip" -DestinationPath .
```

`istioctl`İstemci ikilisi istemci makinenizde çalışır ve AKS kümenize Istio 'yu yüklemenize ve yönetmenize olanak sağlar. `istioctl`Windows üzerinde PowerShell tabanlı bir kabuğa Istio Client ikilisini yüklemek için aşağıdaki komutları kullanın. Bu komutlar, `istioctl` istemci ikilisini bir istio klasörüne kopyalar ve sonra (geçerli kabukta) ve kalıcı olarak (kabukta yeniden başlatmalar arasında) ile birlikte kullanılabilir hale getirir `PATH` . Bu komutları çalıştırmak için yükseltilmiş (yönetici) ayrıcalıklarına ihtiyacınız yoktur ve kabuğunu yeniden başlatmanız gerekmez.

```powershell
# Copy istioctl.exe to C:\Istio
New-Item -ItemType Directory -Force -Path "C:\Istio"
Move-Item -Path .\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```