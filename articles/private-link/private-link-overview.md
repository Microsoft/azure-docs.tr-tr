---
title: Azure Özel Bağlantı nedir?
description: Azure özel bağlantı özelliklerine, mimarisine ve uygulamasına genel bakış. Azure özel uç noktaları ve Azure özel bağlantı hizmeti 'nin nasıl çalıştığını ve nasıl kullanılacağını öğrenin.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: overview
ms.date: 03/15/2021
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 19a4c6697c161a78bfb1425009bd0b142dece876
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057244"
---
# <a name="what-is-azure-private-link"></a>Azure Özel Bağlantı nedir? 
Azure özel bağlantısı, Azure PaaS hizmetlerine (örneğin, Azure depolama ve SQL veritabanı) ve Azure 'da barındırılan, müşteriye ait/iş ortağı hizmetlerine sanal ağınızdaki özel bir [uç nokta](private-endpoint-overview.md) üzerinden erişmenizi sağlar.

Sanal ağınız ve hizmet arasındaki trafik, Microsoft omurga ağını de dolaşır. Hizmetinizi genel İnternet 'e sunma artık gerekli değildir. Kendi [özel bağlantı hizmetinizi](private-link-service-overview.md) sanal ağınızda oluşturabilir ve müşterilerinize iletebilirsiniz. Azure özel bağlantısını kullanarak kurulum ve tüketim, Azure PaaS, müşteriye ait ve paylaşılan iş ortağı hizmetleri arasında tutarlıdır.

> [!IMPORTANT]
> Azure özel bağlantısı artık genel kullanıma sunulmuştur. Hem özel uç nokta hem de özel bağlantı hizmeti (Standart yük dengeleyicinin arkasındaki hizmet) genel kullanıma sunulmuştur. Farklı zamanlamalarda Azure özel bağlantısına farklı Azure PaaS da eklenecektir. Özel bağlantı için bkz. özel bağlantı [kullanılabilirliği](availability.md) . Bilinen sınırlamalar için bkz. [Özel uç nokta](private-endpoint-overview.md#limitations) ve [özel bağlantı hizmeti](private-link-service-overview.md#limitations). 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Azure özel bağlantı merkezi Azure portal" border="false":::

## <a name="key-benefits"></a>Önemli avantajlar
Azure özel bağlantısı aşağıdaki avantajları sağlar:  
- **Azure platformunda özel olarak erişim Hizmetleri**: Sanal ağınızı, kaynak veya hedef üzerinde genel bir IP adresi olmadan Azure 'daki hizmetlere bağlayın. Hizmet sağlayıcıları kendi sanal ağındaki hizmetlerini işleyebilir ve tüketiciler kendi yerel sanal ağında bu hizmetlere erişebilir. Özel bağlantı platformu, Azure omurga ağı üzerinden tüketici ve hizmetler arasındaki bağlantıyı işleymeyecektir. 
 
- Şirket **içi ve eşlenmiş ağlar**: Azure 'Da, ExpressRoute özel EŞLEMESI, VPN tünelleri ve eşlenmiş sanal ağlar üzerinden şirket içinden çalışan hizmetlere özel uç noktalar kullanarak erişin. ExpressRoute Microsoft eşlemesi 'ni yapılandırmaya veya internet 'e ulaşmaya gerek yoktur. Özel bağlantı, iş yüklerini Azure 'a geçirmek için güvenli bir yol sağlar.
 
- **Veri sızıntılarına karşı koruma**: özel bir uç nokta, tüm hizmet yerine bir PaaS kaynağı örneğine eşlenir. Tüketiciler yalnızca belirli bir kaynağa bağlanabilir. Hizmette başka bir kaynağa erişim engellenir. Bu mekanizma, veri sızıntısı risklerine karşı koruma sağlar. 
 
- **Küresel erişim**: diğer bölgelerde çalışan hizmetlere özel olarak bağlanın. Tüketicinin sanal ağı A bölgesinde olabilir ve B bölgesinde özel bağlantı arkasındaki hizmetlere bağlanabilir.  
 
- **Kendi hizmetlerinizi genişletin**: hizmetinizi Azure 'daki tüketicilere özel olarak işlemek için aynı deneyimi ve işlevselliği etkinleştirin. Hizmetinizi standart bir Azure Load Balancer arkasına yerleştirerek özel bağlantı için etkinleştirebilirsiniz. Tüketici daha sonra kendi sanal ağında özel bir uç nokta kullanarak doğrudan hizmetinize bağlanabilir. Bağlantı isteklerini bir onay çağrı akışı kullanarak yönetebilirsiniz. Azure özel bağlantısı, farklı Azure Active Directory kiracılarına ait tüketiciler ve hizmetler için geçerlidir. 

> [!NOTE]
> Azure özel bağlantısı, Azure sanal ağ ile birlikte [Azure kullanılabilirlik alanları](../availability-zones/az-overview.md) yayılmış ve bu nedenle bölge dayanıklı. Özel bir uç nokta kullanarak Azure kaynağı için yüksek kullanılabilirlik sağlamak üzere kaynağın bölge dayanıklı olduğundan emin olun.

## <a name="availability"></a>Kullanılabilirlik 

Özel bağlantıyı destekleyen Azure hizmetleri hakkında bilgi için bkz. [Azure özel bağlantı kullanılabilirliği](availability.md).

En güncel bildirimler için, [Azure özel bağlantı güncelleştirmeleri sayfasına](https://azure.microsoft.com/updates/?product=private-link)bakın.

## <a name="logging-and-monitoring"></a>Günlüğe kaydetme ve izleme

Azure özel bağlantısı, Azure Izleyici ile tümleştirilmesine sahiptir. Bu bileşim şunları sağlar:

 - Günlükleri bir depolama hesabına arşivleme.
 - Olay Hub 'ınıza olay akışı.
 - Azure İzleyici ile günlüğe kaydetme.

Azure Izleyici 'de aşağıdaki bilgilere erişebilirsiniz: 
- **Özel uç nokta**: 
    - Özel uç nokta tarafından işlenen veriler (ın/OUT)
 
- **Özel bağlantı hizmeti**:
    - Özel bağlantı hizmeti tarafından işlenen veriler (ın/OUT)
    - NAT bağlantı noktası kullanılabilirliği  
 
## <a name="pricing"></a>Fiyatlandırma   
Fiyatlandırma ayrıntıları için bkz. [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>SSS  
SSS için bkz. [Azure özel bağlantı SSS](private-link-faq.md).
 
## <a name="limits"></a>Sınırlar  
Sınırlar için bkz. [Azure özel bağlantı limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>Hizmet Düzeyi Sözleşmesi
SLA için bkz. [Azure özel bağlantısı Için SLA](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç: Azure portal kullanarak özel uç nokta oluşturma](create-private-endpoint-portal.md)
- [Hızlı başlangıç: Azure portal kullanarak özel bir bağlantı hizmeti oluşturma](create-private-link-service-portal.md)
