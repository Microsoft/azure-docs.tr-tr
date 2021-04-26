---
title: Oracle için BareMetal 'ın Ethernet yapılandırması
description: Oracle iş yükleri için BareMetal örneklerinde Ethernet arabirimlerinin yapılandırması hakkında bilgi edinin.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: c57cbc86d17090d6960a334c2790d80b43420aca
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588896"
---
# <a name="ethernet-configuration-of-baremetal-for-oracle"></a>Oracle için BareMetal 'ın Ethernet yapılandırması

Bu makalede, Oracle iş yükleri için BareMetal örneklerinde Ethernet arabirimlerinin yapılandırmasına bakacağız.

Oracle için sağlanan her BareMetal örneği, Ethernet arabirimi kümeleriyle önceden yapılandırılmış olarak gelir. Ethernet arabirimleri dört tür olarak kategorilere ayrılır:

- İstemci erişimi için veya tarafından kullanılır.
- Düğümden düğüme iletişim için kullanılır. Bu arabirim, istenen topolojiden bağımsız olarak tüm sunucularda yapılandırılır. Yalnızca genişleme senaryolarında kullanılır.
- Düğümden depolamaya bağlantı için kullanılır.
- Olağanüstü durum kurtarma (DR) kurulumu ve bölgeler arası bağlantı için küresel erişime bağlantı için kullanılır.

## <a name="architecture"></a>Mimari

Aşağıdaki diyagramda, BareMetal altyapı önceden yapılandırılmış Ethernet arabirimlerinin mimarisi gösterilmektedir. 

[![Oracle iş yükleri için önceden yapılandırılmış Ethernet arabirimlerinin mimarisini gösteren diyagram.](media/oracle-baremetal-ethernet/architecture-ethernet.png)](media/oracle-baremetal-ethernet/architecture-ethernet.png#lightbox)

Varsayılan yapılandırma bir BareMetal örneğine erişmek için Secure Shell (SSH) kullanabileceğiniz Azure sanal ağınızdan (VNET) bağlanarak, bir istemci IP arabirimi (eth1) ile birlikte gelir.

> [!NOTE]
> Farklı bir Azure VNET 'ten başka bir istemci arabirimi (eth10) için, bir hizmet isteği göndermek üzere Microsoft CSA ile iletişim kurun. Örneğin, geliştirme/test ve üretim/DR ortamlarını istiyorsanız.

| **NIC mantıksal arabirimi** | **RHEL işletim sistemi ile ad** | **Kullanım örneği** |
| --- | --- | --- |
| A | Net1. Tenant | İstemciden BareMetal örneği |
| C | NET2. Tenant | Düğümden depolamaya; depolama ortamının yönetimi için depolama denetleyicilerine koordine ve erişimi destekler. |
| B | net3. Tenant | Düğümden düğüme (özel bağlantı) |
| C | NET4. Tenant | Ayrılmış/Iscsı |
| C | Net5. Tenant | Ayrılmış/günlük yedeklemesi |
| C | NET6. Tenant | Düğümden storage_Data yedekleme (RMAN, anlık görüntü) |
| C | Net7. Tenant | Düğümden storage_dNFS-PRI; NetApp depolama dizisiyle bağlantı sağlar. |
| C | Net8. Tenant | Düğüm-storage_dNFS-sn; NetApp depolama dizisiyle bağlantı sağlar. |
| D | Net9. Tenant | Başka bir bölgedeki BMı 'ye erişmek için genel erişim kurulumu için DR bağlantısı. |
| A | \*Net10. Tenant | \* İstemciden BareMetal örneği
 |

Gerekirse, sizin için daha fazla ağ arabirim denetleyicisi (NIC) kartı tanımlayabilirsiniz. Ancak, *mevcut NIC 'lerin yapılandırması değiştirilemez.*

## <a name="usage-rules"></a>Kullanım kuralları

BareMetal örnekleri için, varsayılan olarak dört mantıksal NIC 'de dokuz atanan IP adresi olacaktır. Aşağıdaki kullanım kuralları geçerlidir:

- Ethernet "A", Microsoft 'a gönderdiğiniz sunucu IP havuzu adres aralığının dışında atanmış bir IP adresine sahip olmalıdır. Bu IP adresi, işletim sisteminin vs/hosts dizininde tutulmamalıdır.
- Ethernet "B", çeşitli örnekler arasında iletişim için özel olarak, etc/hosts dizininde tutulmalıdır. Bu IP adreslerini, düğüm içi yapılandırma için kullanılan IP adresleri olarak genişleme Oracle gerçek uygulama kümeleri (RAC) yapılandırmalarında saklayın.
- Ethernet "C", NFS depolamaya iletişim için kullanılan bir IP adresine sahip olmalıdır. Bu tür bir adres, etc/hosts dizininde tutulmamalıdır.
- Ethernet "D", yalnızca DR bölgesindeki BareMetal örneklere erişmek için genel erişim kurulumu için kullanılmalıdır.

## <a name="next-step"></a>Sonraki adım

Oracle mimarisi için BareMetal altyapısı hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Oracle için BareMetal altyapısının mimarisi](oracle-baremetal-architecture.md)
