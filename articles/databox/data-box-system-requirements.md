---
title: Data Box sistem gereksinimlerini Microsoft Azure | Microsoft Docs
description: Azure Data Box ve Data Box bağlanan istemciler için önemli sistem gereksinimleri hakkında bilgi edinin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 03/25/2021
ms.author: alkohli
ms.openlocfilehash: 7f999dbf7a4e0262e36181a98560931d32d3296b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612509"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box sistem gereksinimleri

Bu makalede, Microsoft Azure Data Box ve Data Box bağlanan istemciler için önemli sistem gereksinimleri açıklanmaktadır. Data Box dağıtmadan önce bilgileri dikkatlice incelemenizi ve dağıtım ve işlem sırasında ihtiyacınız olduğunda bu bilgilere başvurmanız önerilir.

Sistem gereksinimleri şunları içerir:

* **Yazılım gereksinimleri:** Data Box bağlanan konaklarda, yerel Web Kullanıcı arabirimi için desteklenen işletim sistemlerini, dosya aktarım protokollerini, depolama hesaplarını, depolama türlerini ve tarayıcıları açıklar.
* **Ağ gereksinimleri:** Data Box için, Data Box en iyi işlem için ağ bağlantıları ve bağlantı noktaları için gereksinimleri açıklar.


## <a name="software-requirements"></a>Yazılım gereksinimleri

Yazılım gereksinimleri, yerel Web Kullanıcı arabirimi için desteklenen işletim sistemlerini, dosya aktarım protokollerini, depolama hesaplarını, depolama türlerini ve tarayıcıları içerir.

### <a name="supported-operating-systems-for-clients"></a>İstemciler için desteklenen işletim sistemleri

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>İstemciler için desteklenen dosya aktarımı protokolleri

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> Data Box paylaşımlarına bağlantı, dışarı aktarma siparişleri için REST aracılığıyla desteklenmez.

### <a name="supported-storage-accounts"></a>Desteklenen depolama hesapları

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Desteklenen depolama türleri

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Desteklenen web tarayıcıları

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Ağ gereksinimleri

Veri merkezinizin yüksek hızlı ağı olmalıdır. En az 1 10-GbE bağlantınızın olmasını önemle öneririz. 10-GbE bağlantı kullanılamıyorsa, verileri kopyalamak için 1-GbE veri bağlantısı kullanabilirsiniz, ancak kopyalama hızları bundan etkilenir.

### <a name="port-requirements"></a>Bağlantı noktası gereksinimleri

Aşağıdaki tabloda SMB veya NFS trafiğine izin vermek için güvenlik duvarınızda açılması gereken bağlantı noktaları listelenmektedir. Bu tabloda, (*gelen* *), gelen* istemcinin cihazınıza erişim talep ettiği yöne başvurur. *Out* (veya *Outbound*), dağıtım ötesinde Data Box cihazınızın verileri dışarıdan gönderdiği yönü ifade eder. Örneğin, veriler Internet 'e giden bir durum olabilir.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Box dağıtın](data-box-deploy-ordered.md)
