---
title: Microsoft Azure Data Box sistem gereksinimleri | Microsoft Docs
description: Azure Data Box'ınızın yazılım ve ağ gereksinimleri hakkında bilgi edinin
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839769"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box sistem gereksinimleri

Bu makalede Data Box için istemcilerin ve Microsoft Azure Data Box'ınızı için önemli sistem gereksinimlerini açıklar. Data Box'ınızı dağıtın ve ardından geri gerekirse dağıtım ve sonraki işlemi sırasında başvurduğu önce bilgileri dikkatle gözden geçirmeniz önerilir.

Sistem gereksinimleri şunlardır:

* **Data Box bağlanma ana bilgisayarları için yazılım gereksinimleri** -desteklenen platformlar, yerel web kullanıcı Arabirimi için tarayıcılar, SMB istemcileri ve Data Box için bağlanabilir konaklar için tüm ek gereksinimleri açıklanmaktadır.
* **Data Box için ağ gereksinimleri** -Data Box'ın en iyi işlemi için ağ gereksinimleri hakkında bilgi sağlar.


## <a name="software-requirements"></a>Yazılım gereksinimleri

Yazılım gereksinimleri, desteklenen işletim sistemleri, yerel web kullanıcı Arabirimi ile desteklenen tarayıcılar ve SMB istemcileri hakkında bilgi içerir.

### <a name="supported-operating-systems-for-clients"></a>İstemciler için desteklenen işletim sistemleri

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Linux istemcileri için desteklenen dosya sistemleri

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Desteklenen depolama hesapları

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Desteklenen depolama türleri

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Desteklenen web tarayıcıları

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Ağ gereksinimleri

Veri merkezinizin yüksek hızlı ağı olmalıdır. En az bir adet 10 GbE bağlantınızın olması önemle tavsiye edilir. 10 GbE bağlantı kullanılabilir değilse, 1 GbE veri bağlantısı, veri ancak hızı etkilenir kopyalama kopyalamak için kullanılabilir.

### <a name="port-requirements"></a>Bağlantı noktası gereksinimleri

Aşağıdaki tablo için SMB veya NFS trafiğine izin vermek için güvenlik duvarını açılması gereken bağlantı noktalarını listeler. Bu tabloda *içinde* veya *gelen* hangi gelen istemci istekleri erişimden Cihazınızı yönü belirtir. *Çıkış* veya *giden* , Data Box cihazınız gönderir dışarıdan, veri dağıtımı dışında yön ifade eder: Örneğin, Internet'e giden.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Box'ınızı dağıtın](data-box-deploy-ordered.md)
