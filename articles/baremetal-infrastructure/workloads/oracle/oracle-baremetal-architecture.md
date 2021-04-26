---
title: Oracle için BareMetal altyapısının mimarisi
description: Oracle için çeşitli BareMetal altyapı yapılandırmalarının mimarisi hakkında bilgi edinin.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 1bdc32c14cfc8986c52a4ea916a6130ee29e6028
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559298"
---
# <a name="architecture-of-baremetal-infrastructure-for-oracle"></a>Oracle için BareMetal altyapısının mimarisi

Bu makalede, Oracle için BareMetal altyapısına yönelik mimari seçeneklere ve her birinin desteklediği özelliklere bakacağız.

## <a name="single-instance"></a>Tek örnek

Bu topoloji, BareMetal altyapısına geçiş için Oracle Data Guard ile Oracle Database tek bir örneğini destekler. Yüksek kullanılabilirlik ve bakım çalışması için bekleme düğümünün kullanılmasını destekler.

[![Oracle Data Guard ile tek bir Oracle Database örneğinin mimarisini gösteren diyagram.](media/oracle-baremetal-architecture/single-instance-architecture.png)](media/oracle-baremetal-architecture/single-instance-architecture.png#lightbox)

## <a name="oracle-real-application-clusters-rac-one-node"></a>Oracle gerçek uygulama kümeleri (RAC) bir düğüm

Bu topoloji, paylaşılan depolama ve KıLAVUZ kümesiyle bir RAC yapılandırmasını destekler. Veritabanı örnekleri yalnızca bir düğümde çalışır (etkin-Pasif yapılandırma).

Şu özellikler mevcuttur:

- Aktif-pasif Oracle RAC bir düğüm

    - Otomatik yük devretme

    - İkinci düğümde hızlı yeniden başlatma

- Oracle RAC ile gerçek zamanlı yük devretme ve ölçeklenebilirlik

- Sıfır kesinti durumunda bakım

[![Oracle RAC tek düğümlü etkin-Pasif yapılandırma mimarisini gösteren diyagram.](media/oracle-baremetal-architecture/one-node-rac-architecture.png)](media/oracle-baremetal-architecture/one-node-rac-architecture.png#lightbox)

## <a name="rac"></a>RAC

Bu topoloji, her veritabanı için aynı anda birden çok örnek (etkin-etkin yapılandırma) çalıştırıldığında paylaşılan depolama ve kılavuz kümesiyle bir Oracle RAC yapılandırmasını destekler.

- Performans, eklenen sunucuların çevrimiçi sağlanması aracılığıyla kolayca Ölçeklendirilecek. 
-  Kullanıcılar tüm sunucularda etkindir ve tüm sunucular aynı Oracle Database erişimi paylaşır. 
-  Tüm veritabanı bakımı türleri çevrimiçi veya en az ya da sıfır kapalı kalma süresine göre yapılır. 
- Oracle Active Data Guard (ADG) bekleme sistemleri, test sistemleri olarak çift amaçlı olarak kolayca sunabilir. 

Bu yapılandırma, üretim ortamına uygulanmadan önce üretim veritabanının tam kopyasının tüm değişikliklerini test etmenize olanak tanır.

> [!NOTE]
> Etkin Data Guard 'ı (zaman uyumlu modu) kullanmayı düşünüyorsanız, bu özelliğin desteklendiği bölgesel bölgeleri göz önünde bulundurmanız gerekir. Yalnızca coğrafi olarak dağıtılmış bölgeler için Data Guard 'ı zaman uyumsuz modla kullanmanızı öneririz.

[![Oracle RAC etkin-etkin yapılandırmasının mimarisini gösteren diyagram.](media/oracle-baremetal-architecture/rac-architecture.png)](media/oracle-baremetal-architecture/rac-architecture.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

Oracle iş yükleri için BareMetal örneklerinizi sağlama hakkında bilgi edinin.

> [!div class="nextstepaction"]
> [Oracle için BareMetal altyapısını sağlama](oracle-baremetal-provision.md)

