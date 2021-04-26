---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: 1c7dbe9bb6cf28f4dee2c4e9aa036410fadbbddf
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080607"
---
## <a name="download-and-install-the-osm-client-binary"></a>OSD istemci ikilisini indirme ve yükleme

Bash tabanlı bir kabukta, `curl` OSI sürümünü indirmek için kullanın ve ardından aşağıdaki gibi ile ayıklayın `tar` :

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.2

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-darwin-amd64.tar.gz" | tar -vxzf -
```

`osm`İstemci ikilisi istemci makinenizde çalışır ve AKS kümenizdeki OSD 'yi yönetmenizi sağlar. `osm`Bash tabanlı bir kabuğa OSE istemci ikilisini yüklemek için aşağıdaki komutları kullanın. Bu komutlar, `osm` istemci ikilisini içindeki standart Kullanıcı programı konumuna kopyalar `PATH` .

```bash
sudo mv ./darwin-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

`osm`Aşağıdaki komutla, istemci kitaplığının yolunuza ve sürüm numarasına doğru şekilde eklendiğini doğrulayabilirsiniz.

```
osm version
```
