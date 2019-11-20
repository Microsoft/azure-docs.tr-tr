---
title: Azure Özel Bağlantı nedir?
description: Azure özel bağlantı kullanarak Azure PaaS hizmetlerine (örneğin, Azure depolama ve SQL veritabanı) ve Azure 'da barındırılan müşteri/iş ortağı hizmetlerine sanal ağınızdaki özel bir uç nokta üzerinden erişin.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: overview
ms.date: 11/04/2019
ms.author: kumud
ms.openlocfilehash: aa176cb5ffc67ebba826cf13a1a66608301af5e5
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73601524"
---
# <a name="what-is-azure-private-link-preview"></a>Azure Özel Bağlantı nedir? (Önizleme)
Azure özel bağlantısı, Azure PaaS hizmetlerine (örneğin, Azure depolama, Azure Cosmos DB ve SQL veritabanı) ve Azure 'da barındırılan müşteri/iş ortağı hizmetlerine sanal ağınızdaki [özel bir uç nokta](private-endpoint-overview.md) üzerinden erişmenizi sağlar. Sanal ağınız ve hizmet arasındaki trafik, Microsoft omurga ağı üzerinden geçer ve genel İnternet’ten etkilenme olasılığı ortadan kaldırılır. Ayrıca, kendi [özel bağlantı hizmetinizi](private-link-service-overview.md) sanal ağınızda (VNet) oluşturabilir ve müşterilerinize özel olarak iletebilirsiniz. Azure özel bağlantısı kullanılarak kurulum ve tüketim deneyimi Azure PaaS, müşteriye ait ve paylaşılan iş ortağı hizmetleri arasında tutarlıdır.

> [!IMPORTANT]
> Bu genel önizleme bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılmamalıdır. Belirli özellikler desteklenmiyor olabilir, kısıtlı yeteneklere sahip olabilir veya tüm Azure konumlarında mevcut olmayabilir. Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Bilinen sınırlamalar için bkz. [Özel uç nokta](private-endpoint-overview.md#limitations) ve [özel bağlantı hizmeti](private-link-service-overview.md#limitations).


![Özel uç noktaya genel bakış](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Önemli avantajlar
Azure özel bağlantısı aşağıdaki avantajları sağlar:  
- **Azure platformunda özel olarak erişim Hizmetleri**: Sanal ağınızı Azure 'da çalışan hizmetlere, kaynak veya hedef üzerinde genel bir IP adresi gerekmeden özel olarak bağlayın. Hizmet sağlayıcıları kendi sanal ağında hizmetlerini özel olarak işleyebilir ve tüketiciler bu hizmetlere kendi yerel sanal ağında özel olarak erişebilir. Özel bağlantı platformu, Azure omurga ağı üzerinden tüketici ve hizmetler arasındaki bağlantıyı işleymeyecektir. 
 
- Şirket **içi ve eşlenmiş ağlar**: Azure 'Da, ExpressRoute özel EŞLEMESI/VPN tünelleri (Şirket içi) ve eşlenmiş sanal ağlardan özel uç noktalar aracılığıyla Şirket içinden çalışan hizmetlere erişin. Hizmete ulaşmak için genel eşlemeyi ayarlamaya veya internet 'e geçiş yapmanıza gerek yoktur. Bu özellik, iş yüklerini Azure 'a geçirmek için güvenli bir yol sağlar.
 
- **Veri taşmasıyla Ilgili koruma**: Azure özel bağlantısı ile VNET 'teki özel uç nokta, hizmetin tamamına karşılık olarak müşterinin PaaS kaynağının belirli bir örneğine eşlenir. Özel uç nokta tüketicilerinin kullanılması, hizmette herhangi bir kaynağa değil yalnızca belirli bir kaynağa bağlanabilir. Bu, oluşturulan mekanizmaya yönelik veri kaybı risklerine karşı koruma sağlar. 
 
- **Küresel erişim**: diğer bölgelerde çalışan hizmetlere özel olarak bağlanın. Bu, tüketicinin sanal ağının A bölgesinde olabileceği ve B bölgesinde özel bağlantı arkasındaki hizmetlere bağlanabileceği anlamına gelir.  
 
- **Kendi hizmetlerinizi genişletin**: Azure 'daki tüketicilerinize özel olarak kendi hizmetinizi işlemek için aynı deneyim ve işlevlerden yararlanın. Hizmetinizi bir Standart Load Balancer arkasına yerleştirerek, özel bağlantı için etkinleştirebilirsiniz. Tüketici daha sonra kendi VNet 'inde özel bir uç nokta kullanarak doğrudan hizmetinize bağlanabilir. Bu bağlantı isteklerini, basit bir onay çağrı akışı kullanarak yönetebilirsiniz. Azure özel bağlantısı, farklı Active Directory kiracılarına ait tüketiciler ve hizmetler için de geçerlidir. 

## <a name="availability"></a>Kullanılabilirlik 
 Aşağıdaki tabloda özel bağlantı Hizmetleri ve bunların kullanılabildiği bölgeler listelenmektedir. 

|Senaryo  |Desteklenen hizmetler   |Kullanılabilen bölgeler | Durum   |
|---------|---------|---------|---------|
|Müşterinin sahip olduğu hizmetler için özel bağlantı|Standart Load Balancer arkasındaki özel bağlantı Hizmetleri | Tüm ortak bölgeler  |  Önizleme  |
|Azure PaaS hizmetleri için özel bağlantı   | Azure Storage        |  Tüm ortak bölgeler      | Önizleme         |
|  | Azure Data Lake Storage Gen2        |  Tüm ortak bölgeler      | Önizleme         |
|  |  Azure SQL Veritabanı         | Tüm ortak bölgeler      |   Önizleme      |
||Azure SQL Veri Ambarı| Tüm ortak bölgeler |Önizleme|
||Azure Cosmos DB| Orta Batı ABD, WestUS, Orta Kuzey ABD |Önizleme|

En güncel bildirimler için, [Azure sanal ağ güncelleştirmeleri sayfasını](https://azure.microsoft.com/updates/?product=virtual-network)inceleyin.

## <a name="logging-and-monitoring"></a>Günlüğe kaydetme ve izleme

Azure özel bağlantısı, günlükleri bir depolama hesabında arşivlemenize, Olay Hub 'ınıza olay akışına veya Azure Izleyici günlüklerine göndermenize olanak sağlayan Azure Izleyici ile tümleşiktir. Azure Izleyici 'de aşağıdaki bilgilere erişebilirsiniz: 
- **Özel uç nokta**: özel uç nokta tarafından işlenen VERILER (ın/out)
 
- **Özel bağlantı hizmeti**:
    - Özel bağlantı hizmeti tarafından işlenen veriler (ın/OUT)
    - NAT bağlantı noktası kullanılabilirliği  
 
## <a name="pricing"></a>Fiyatlandırma   
Fiyatlandırma ayrıntıları için bkz. [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>SSS  
SSS için bkz. [Azure özel bağlantı SSS](private-link-faq.md).
 
## <a name="limits"></a>Sınırlar  
Sınırlar için bkz. [Azure özel bağlantı limitleri](../azure-subscription-service-limits.md#private-link-limits).

## <a name="next-steps"></a>Sonraki adımlar
- [Portal kullanarak SQL veritabanı sunucusu için özel bir uç nokta oluşturma](create-private-endpoint-portal.md)
- [PowerShell kullanarak SQL veritabanı sunucusu için özel bir uç nokta oluşturma](create-private-endpoint-powershell.md)
- [CLı kullanarak SQL veritabanı sunucusu için özel bir uç nokta oluşturma](create-private-endpoint-cli.md)
- [Portalı kullanarak depolama hesabı için özel bir uç nokta oluşturma](create-private-endpoint-storage-portal.md)
- [Portal kullanarak Azure Cosmos hesabı için özel bir uç nokta oluşturma](../cosmos-db/how-to-configure-private-endpoints.md)
- [Azure PowerShell kullanarak kendi özel bağlantı hizmetinizi oluşturun](create-private-link-service-powershell.md)


 
