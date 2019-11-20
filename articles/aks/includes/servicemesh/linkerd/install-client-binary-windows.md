---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 2a1249d134c23f47f939913fa1c9887d2a8e1f63
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72601168"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Linkerd linkerd istemci ikilisini indirin ve yükleyin

Windows üzerinde PowerShell tabanlı bir kabukta, Linkerd sürümünü şu şekilde indirmek için `Invoke-WebRequest` kullanın:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

@No__t_0 istemci ikili dosyası, istemci makinenizde çalışır ve Linkerd hizmet ağıyla etkileşime girebilmeniz için izin verir. Windows üzerinde PowerShell tabanlı bir kabuğa Linkerd `linkerd` istemci ikilisini yüklemek için aşağıdaki komutları kullanın. Bu komutlar, `linkerd` istemci ikilisini bir Linkerd klasörüne kopyalar ve ardından, `PATH` üzerinden (geçerli kabukta) ve kalıcı olarak (kabukta yeniden başlatmalar arasında) kullanılabilir hale getirir. Bu komutları çalıştırmak için yükseltilmiş (yönetici) ayrıcalıklarına ihtiyacınız yoktur ve kabuğunu yeniden başlatmanız gerekmez.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```