---
title: Oracle için BareMetal Altyapısı nedir?
description: Oracle iş yükleri için BareMetal altyapı teklifleri özellikleri hakkında bilgi edinin.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: efb7506a0fa98ca93390bfe7d3cb1401cdbb77e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559281"
---
# <a name="what-is-baremetal-infrastructure-for-oracle"></a>Oracle için BareMetal Altyapısı nedir?

Bu makale, Oracle iş yükleri için BareMetal altyapı teklifleri özelliklerine genel bir bakış sunar.

Oracle için BareMetal altyapısı, Oracle sertifikalı Birleşik Bilgi Işlem sistemine (UCS) ve FLexPod 'a dayalıdır. Esnek Pod platformu, önceden doğrulanan depolama, ağ ve sunucu teknolojilerini sunar. Bu BT, DirectNFS protokolünü kullanarak tümleştirme sağlayan NFS depolama alanı sunar. Karemetal sunucular, BareMetal örneklerinde hiper yönetici olmadan size ayrılmıştır. 

Bu örnekler, bir Oracle iş yükü gerektiren görev açısından kritik uygulamaların çalıştırılmasına yöneliktir. BareMetal örnekleri, Azure sanal makinelerinde (VM) çalışan uygulamalarınıza düşük gecikme süresi (0,35 MS) sağlar. BareMetal, paylaşılan depolama diski sağlar ve adanmış bir özel Interconnect ağıyla düğümden düğüme iletişim için gereken çoklu dönüştürmeyi destekler. 

Oracle için BareMetal altyapısının diğer özellikleri şunlardır:

- Oracle sertifikalı UCS blade-UCSB200-M5, UCSB460-M4, UCSB480-M5
- Özel sanal LAN (VLAN)-40 GB kullanan Oracle gerçek uygulama kümeleri (RAC) düğümden düğüme (çok noktaya yayın) iletişimi.
- Microsoft tarafından yönetilen donanım
  - Yedekli depolama, ağ, güç, yönetim
  - Infra, onarımlar ve değiştirme için izleme
  - Müşterinin etki alanı denetleyicisine Azure ExpressRoute ekler
  - Güvenli fiziksel ve ağ güvenliği, tüm Azure bulut hizmetlerine erişebilir

### <a name="supported-protocols"></a>Desteklenen protokoller

Aşağıdaki protokoller, Oracle iş yükünün BareMetal sunucuları içindeki farklı bağlama noktaları için kullanılır.

- İşletim sistemi bağlama – Iscsı
- Veri/günlük – NFSv3
- Backup/Arşivle – NFSv4

### <a name="licensing"></a>Lisanslama

- Kendi şirket içi işletim sisteminizi ve Oracle lisanslarınızı siz getirin.

### <a name="operating-system"></a>İşletim sistemi

Sunucular, işletim sistemi RHEL 7,6 ile önceden yüklenir.

## <a name="next-steps"></a>Sonraki adımlar

Oracle BareMetal iş yükleri için SKU 'Lar hakkında bilgi edinin.

> [!div class="nextstepaction"]
> [Oracle iş yükleri için BareMetal SKU 'Ları](oracle-baremetal-skus.md)
