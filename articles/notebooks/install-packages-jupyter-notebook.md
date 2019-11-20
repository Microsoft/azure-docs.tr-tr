---
title: Azure 'da bir Jupyter not defterine paket yükler
description: Azure üzerinde çalışan bir Jupyter F# Not Defteri içinden Python, R ve paketleri nasıl yükleyeceğiniz.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: e6582bb21cb6d383e298bdf68bdb2f565147c20a
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969931"
---
# <a name="install-packages-from-within-a-notebook"></a>Bir not defteri içinden paket yükler

[Not defteriniz için projeyi proje düzeyinde](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)yapılandırabilseniz de, paketleri doğrudan ayrı bir not defteri içinde yüklemek isteyebilirsiniz.

Not defterinden yüklenen paketler yalnızca geçerli sunucu oturumu için geçerlidir. Sunucu kapatıldıktan sonra paket yüklemeleri kalıcı olmaz.

## <a name="python"></a>Python

Python 'daki paketler, kod hücrelerinin içindeki komutlar kullanılarak PIP veya Conda ile yüklenebilir:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Komut çıktısı, gereksinimin zaten karşılandığını gösteriyorsa, Azure Notebooks paketi varsayılan olarak içerebilir. Paket ayrıca bir [Proje ortamı Kurulum adımı](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)ile yüklenebilir.

## <a name="r"></a>R

R 'deki paketler, bir kod hücresindeki `install.packages` işlevi kullanılarak CRAN veya GitHub 'dan yüklenebilir:

```r
install.packages("package_name")
```

Ayrıca, devtools kitaplığını kullanarak GitHub 'dan ön sürüm sürümlerini ve diğer geliştirme paketlerini de yükleyebilirsiniz:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

İçindeki F# paketler, kod hücrelerinin içinden paket bağımlılığı yöneticisini çağırarak, [NuGet.org](https://www.nuget.org) adresinden yüklenebilir. İlk olarak, paket yöneticisini yükleyin:

```fsharp
#load "Paket.fsx"
```

Ardından paketleri yükler:

```fsharp
Paket.Package
  [ "MathNet.Numerics"
    "MathNet.Numerics.FSharp"
  ]
```

Ardından, paket oluşturucuyu yükleyin:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Tüm ormalleri açın:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Sonraki adımlar

- [Nasıl yapılır: projeleri yapılandırma ve yönetme](configure-manage-azure-notebooks-projects.md)
- [Nasıl yapılır: bir slayt gösterisi sunma](present-jupyter-notebooks-slideshow.md)
