---
title: Data Box Heavy sistem gereksinimlerini Microsoft Azure | Microsoft Docs
description: Azure Data Box Heavy için yazılım ve ağ gereksinimleri hakkında bilgi edinin
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 03/25/2021
ms.author: alkohli
ms.openlocfilehash: d4fed8a79b9d74f44a511bd019531c7843965d0e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611943"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure Data Box Heavy sistem gereksinimleri

Bu makalede, Azure Data Box Heavy cihazınız ve cihaza bağlanan istemciler için önemli sistem gereksinimleri açıklanmaktadır. Data Box Heavy dağıtmadan önce bilgileri dikkatlice incelemenizi ve ardından dağıtım ve sonraki işlemler sırasında gerektiği şekilde geri başvurmalarını öneririz.

Sistem gereksinimleri şunları içerir:

* **Data Box Heavy bağlanan konaklar Için yazılım gereksinimleri** -desteklenen platformları, yerel Web Kullanıcı arabirimine yönelik TARAYıCıLARı, SMB istemcilerini ve Data Box bağlanabilecek konaklara yönelik ek gereksinimleri açıklar.
* **Data Box Heavy Için ağ gereksinimleri** -Data Box Heavy cihazının en iyi işlemi için ağ gereksinimleri hakkında bilgi sağlar.

## <a name="software-requirements"></a>Yazılım gereksinimleri

Yazılım gereksinimleri, desteklenen işletim sistemleri, yerel Web Kullanıcı arabirimi için desteklenen tarayıcılar ve SMB istemcileri hakkındaki bilgileri içerir.

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

Veri merkezinizin yüksek hızlı ağı olmalıdır. En hızlı kopyalama hızları için 2 40-GbE bağlantıları paralel olarak (düğüm başına bir tane) kullanılabilir. 40-GbE kullanılabilir değilse, en az 2 10-GbE bağlantınız olması önerilir (düğüm başına bir tane).

### <a name="port-requirements"></a>Bağlantı noktası gereksinimleri

Aşağıdaki tabloda SMB veya NFS trafiğine izin vermek için güvenlik duvarınızda açılması gereken bağlantı noktaları listelenmektedir. Bu tabloda, veya *gelen* *içinde* , gelen istemci, cihazınıza erişim talep ettiği yöne başvurur. *Out* veya *Outbound* , dağıtım ötesinde Data Box Heavy cihazınızın verileri dışarıdan gönderdiği yönü ifade eder. Örneğin, Internet 'e giden.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Box dağıtın](data-box-deploy-ordered.md)
