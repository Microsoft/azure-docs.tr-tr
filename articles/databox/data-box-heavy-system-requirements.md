---
title: Microsoft Azure veri kutusu ağır sistem gereksinimleri | Microsoft Docs
description: Yazılım ve ağ gereksinimleri, Azure veri kutusu ağır hakkında bilgi edinin
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839773"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure veri kutusu ağır sistem gereksinimleri

Bu makalede, Azure veri kutusu ağır cihazınız için ve cihaz için bağlanan istemciler için en önemli sistem gereksinimlerini açıklar. Önce veri kutusu ağır dağıtın ve ardından geri gerekirse dağıtım ve sonraki işlemi sırasında başvurduğu bilgileri dikkatlice gözden öneririz.

Sistem gereksinimleri şunlardır:

* **Veri kutusu ağır bağlanma ana bilgisayarları için yazılım gereksinimleri** -desteklenen platformlar, yerel web kullanıcı Arabirimi için tarayıcılar, SMB istemcileri ve Data Box için bağlanabilir konaklar için tüm ek gereksinimleri açıklanmaktadır.
* **Veri kutusu ağır için ağ gereksinimleri** -veri kutusu ağır cihazın en uygun işlemi için ağ gereksinimleri hakkında bilgi sağlar.

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

Veri merkezinizin yüksek hızlı ağı olmalıdır. Hızlı kopya hızları için iki 40 GbE bağlantı paralel (düğüm başına bir tane) yararlanılabilir. 40-GbE kullanılabilir değilse, en az iki 10 GbE bağlantılar (düğüm başına bir tane) sahip olmasını öneririz.

### <a name="port-requirements"></a>Bağlantı noktası gereksinimleri

Aşağıdaki tablo için SMB veya NFS trafiğine izin vermek için güvenlik duvarını açılması gereken bağlantı noktalarını listeler. Bu tabloda *içinde* veya *gelen* hangi gelen istemci istekleri erişimden Cihazınızı yönü belirtir. *Çıkış* veya *giden* hangi veri kutusu ağır Cihazınızı gönderir dışarıdan, veri dağıtımı dışında yön ifade eder: Örneğin, Internet'e giden.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Box'ınızı dağıtın](data-box-deploy-ordered.md)
