---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: pgibson
ms.openlocfilehash: 8ac70027f7483fbf0131c31a5ba3631ed1d4ff9a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080604"
---
## <a name="download-and-install-the-osm-client-binary"></a>OSD istemci ikilisini indirme ve yükleme

Windows üzerinde PowerShell tabanlı bir kabukta, `Invoke-WebRequest` Istio sürümünü indirmek için kullanın ve ardından aşağıdaki gibi ile ayıklayın `Expand-Archive` :

```powershell
# Specify the OSM version that will be leveraged throughout these instructions
$OSM_VERSION="v0.8.2"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-windows-amd64.zip&quot; -OutFile &quot;osm-$OSM_VERSION.zip"
Expand-Archive -Path "osm-$OSM_VERSION.zip" -DestinationPath .
```

`osm`İstemci ikilisi istemci makinenizde çalışır ve AKS kümenizdeki OSA denetleyicisini yönetmenizi sağlar. `osm`Windows üzerinde PowerShell tabanlı bir kabuğa OSD istemci ikilisini yüklemek için aşağıdaki komutları kullanın. Bu komutlar, `osm` istemci ikilisini BIR OSI klasörüne kopyalar ve sonra (geçerli kabukta) ve kalıcı olarak (kabukta yeniden başlatmalar arasında), ile birlikte kullanılabilir hale getirir `PATH` . Bu komutları çalıştırmak için yükseltilmiş (yönetici) ayrıcalıklarına ihtiyacınız yoktur ve kabuğunu yeniden başlatmanız gerekmez.

```powershell
# Copy osm.exe to C:\OSM
New-Item -ItemType Directory -Force -Path "C:\OSM"
Move-Item -Path .\windows-amd64\osm.exe -Destination "C:\OSM\"

# Add C:\OSM to PATH.
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH&quot;, &quot;User&quot;) + &quot;;C:\OSM\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\OSM\"
```
