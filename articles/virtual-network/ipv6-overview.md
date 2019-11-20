---
title: Azure sanal ağ için IPv6 'ya genel bakış (Önizleme)
titlesuffix: Azure Virtual Network
description: Bir Azure sanal ağındaki IPv6 uç noktaları ve veri yollarının IPv6 açıklaması.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 001a6d5ef742874698cd7a67014179a2f8528fc6
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053456"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Azure sanal ağ için IPv6 nedir? (Önizleme)

Azure sanal ağ (VNet) için IPv6, Azure 'daki uygulamaları hem bir sanal ağ hem de Internet üzerinden IPv6 ve IPv4 bağlantısıyla barındırmanızı sağlar. Genel IPv4 adreslerinin tükenmesi nedeniyle, yeni taşınabilirlik ve Nesnelerin İnterneti (IoT) ağları genellikle IPv6 üzerinde oluşturulmuştur. Uzun süredir oluşturulan ISS ve mobil ağlar IPv6 'ya dönüştürüledir. Yalnızca IPv4 Hizmetleri, hem mevcut hem de gelişen pazarlarda kendilerini gerçek bir dezavantajda bulabilir. Çift yığın IPv4/IPv6 bağlantısı, Azure 'da barındırılan hizmetlerin, hem mevcut IPv4 hem de bu yeni IPv6 cihaz ve ağlarla birlikte erişilebilen, genel olarak kullanılabilen, çift yığılmış hizmetler ile bu teknoloji eksikliğini çapraz gezmesine olanak sağlar.

Azure 'un özgün IPv6 bağlantısı, Azure 'da barındırılan uygulamalar için çift yığın (IPv4/IPv6) Internet bağlantısı sağlamayı kolaylaştırır. Hem gelen hem de giden başlatılan bağlantılar için yük dengeli IPv6 bağlantısı olan VM 'lerin basit dağıtımına izin verir. Bu özellik hala kullanılabilir ve [burada](../load-balancer/load-balancer-ipv6-overview.md)daha fazla bilgi bulunabilir.
Azure sanal ağ için IPv6, tam IPv6 çözümü mimarilerinin Azure 'da dağıtılmasını sağlayan çok daha tam özellikli bir çözümdür.

> [!Important]
> Azure sanal ağ için IPv6 Şu anda genel önizleme aşamasındadır. Bu önizleme bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Aşağıdaki diyagramda, Azure 'da basit bir çift yığın (IPv4/IPv6) dağıtımı gösterilmektedir:

![IPv6 ağ dağıtım diyagramı](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Avantajlar

Azure VNET için IPv6 avantajları:

- Azure 'da barındırılan uygulamalarınızın, büyüyen mobil ve Nesnelerin İnterneti pazarlara ulaşma durumunu genişletmenize yardımcı olur.
- Çift yığılmış IPv4/IPv6 VM 'Leri, en yüksek hizmet dağıtımı esnekliği sağlar. Tek bir hizmet örneği, hem IPv4 hem de IPv6 özellikli Internet istemcileriyle bağlanabilir.
- Uzun süreli, kararlı Azure VM 'den Internet 'e IPv6 bağlantısı üzerinde oluşturulur.
- Internet 'e yönelik IPv6 bağlantısı yalnızca dağıtımınızda açıkça istekte bulunduğunda, varsayılan olarak güvenli hale gelir.

## <a name="capabilities"></a>Yetenekler

Azure VNet için IPv6 aşağıdaki özellikleri içerir:

- Azure müşterileri kendi kendi IPv6 sanal ağ adresi alanını, uygulamalarının gereksinimlerini karşılamak için tanımlayabilir veya şirket içi IP alanı ile sorunsuz bir şekilde tümleşebilir.
- İkili yığın (IPv4 ve IPv6) sanal ağları çift yığın alt ağları ile, uygulamaların sanal ağlarında veya Internet 'te hem IPv4 hem de IPv6 kaynaklarıyla bağlanmasını sağlar.
    > [!IMPORTANT]
    > IPv6 alt ağları tam/64 boyutunda olmalıdır.  Bu, gelecekteki uyumluluğun, bazı yönlendiricilerin yalnızca/64 IPv6 yollarını kabul edebileceği için alt ağın şirket içi bir ağa yönlendirilmesini etkinleştirmeye karar vermenize olanak sağlar.  
- Kaynaklarınızı ağ güvenlik grupları için IPv6 kurallarıyla koruyun.
    - Azure platformunun dağıtılmış hizmet reddi (DDoS) korumaları, Internet 'e yönelik genel IP 'lere genişletilir
- Kullanıcı tanımlı yollarla sanal ağınızdaki IPv6 trafiğinin yönlendirilmesini, özellikle de Uygulamanızı artırmak için ağ sanal gereçlerini kullanarak özelleştirin.
- Linux ve Windows Sanal Makineleri tüm Azure VNET için IPv6 'Yı kullanabilir
- [Standart IPv6 genel Load Balancer](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) , aşağıdakiler dahil olmak üzere dayanıklı, ölçeklenebilir uygulamalar oluşturmaya yönelik destek:
    - Hangi arka uç havuzu örneklerinin sistem durumu olduğunu ve bu nedenle yeni akışlar alabileceğini belirleyen isteğe bağlı IPv6 sistem durumu araştırması.
    - Bu özelliği özel gereksinimlerinize göre ölçeklendirmeye ve ayarlamaya yönelik giden bağlantı üzerinde tam bildirime dayalı denetim sağlayan isteğe bağlı giden kurallar.
    - Tek bir yük dengeleyicinin birden çok IPv6 genel IP adresi kullanmasını sağlayan isteğe bağlı birden çok ön uç yapılandırması-aynı ön uç Protokolü ve bağlantı noktası ön uç adresleri arasında yeniden kullanılabilir.
    - İsteğe bağlı IPv6 bağlantı noktaları, Yük Dengeleme kurallarının *kayan IP* özelliğini kullanarak arka uç örneklerinde yeniden kullanılabilir 
- Azure sanal ağları 'nda esnek çok katmanlı uygulamalar oluşturmak için [Standart ıPV6 iç Load Balancer](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) desteği.  
- Eski dağıtımlarla uyumluluk için temel IPv6 genel Load Balancer desteği
- [Ayrılmış IPv6 genel IP adresleri ve adres aralıkları](ipv6-public-ip-address-prefix.md) , şirketiniz ve müşterileriniz için Azure 'da barındırılan uygulamalarınızın daha kolay bir şekilde listelenmesini sağlayan kararlı, öngörülebilir IPv6 adresleri sağlar.
- Örnek Düzeyinde Ortak IP, IPv6 Internet bağlantısını doğrudan ayrı sanal makinelere sağlar.
- [IPv6 'yı yalnızca mevcut IPv4 dağıtımlarına ekleyin](ipv6-add-to-existing-vnet-powershell.md)-bu özellik, dağıtımları yeniden oluşturmaya gerek kalmadan, mevcut yalnızca IPv4 dağıtımlarına kolayca IPv6 bağlantısı eklemenizi sağlar.  Bu işlem sırasında IPv4 ağ trafiği etkilenmez, böylece uygulamanıza ve işletim sistemine bağlı olarak, canlı hizmetlere bile IPv6 ekleyemeyebilirsiniz.    
- Internet istemcilerinin, IPv6 (AAAA) kayıtları için Azure DNS desteği ile seçim protokolünü kullanarak çift yığın uygulamanıza sorunsuz bir şekilde erişmesini sağlayın. 
- IPv6 ile sanal makine ölçek kümeleri ile yükle otomatik olarak ölçeklenebilen çift yığın uygulamaları oluşturun.
- [Sanal ağ (VNet) eşleme](virtual-network-peering-overview.md) -hem bölgesel hem de genel eşleme-, çift yığın VNET 'leri birlikte kullanmanıza olanak sağlar. eşlenen ağlardaki VM 'lerde IPv4 ve IPv6 uç noktaları birbirleriyle iletişim kurabiliyor. Dağıtımlarınızı çift yığına taşısanız bile, yalnızca IPv4 sanal ağları ile çift yığına sahip olabilirsiniz. 
- IPv6 sorunlarını giderme ve tanılama, paket yakalama, NSG akış günlükleri, bağlantı sorunlarını giderme ve bağlantı izleme gibi yük dengeleyici ölçümleri/uyarı ve ağ Izleyicisi özellikleri ile kullanılabilir.   

## <a name="scope"></a>Kapsam
Azure sanal ağ için IPv6, müşterilerin Azure 'da ikili yığın (IPv4 + IPv6) uygulamaları barındırmalarını sağlayan bir temel özellik kümesidir.  Zaman içinde daha fazla Azure ağ özelliklerine IPv6 desteği eklemek ve sonuç olarak Azure PaaS hizmetlerinin çift yığın sürümlerini sunmak istiyorduk, ancak Azure PaaS hizmetlerine çift yığın sanal makinelerdeki IPv4 uç noktaları aracılığıyla erişilebilir.   

## <a name="limitations"></a>Sınırlamalar
Azure sanal ağ sürümü için geçerli IPv6 aşağıdaki sınırlamalara sahiptir:
- Azure sanal ağ için IPv6 (Önizleme) tüm genel Azure bölgelerinde mevcuttur, ancak yalnızca küresel Azure 'da henüz kamu bulutlarında değildir.
- Express Route ve VPN Gateway 'ler, IPv6 etkin olan VNET 'te doğrudan veya "UseRemoteGateway" ile birlikte kullanılamaz. 
- Azure platformu (AKS, vb.) kapsayıcılar için IPv6 iletişimini desteklemez.  

## <a name="pricing"></a>Fiyatlandırma

IPv6 Azure kaynakları ve bant genişliği, IPv4 ile aynı ücretler üzerinden ücretlendirilir. IPv6 için ek veya farklı ücretler yoktur. [Genel IP adresleri](https://azure.microsoft.com/pricing/details/ip-addresses/), [ağ bant genişliği](https://azure.microsoft.com/pricing/details/bandwidth/)veya [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/)fiyatlandırmasıyla ilgili ayrıntıları bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure PowerShell kullanarak bir IPv6 çift yığın uygulaması dağıtmayı](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)öğrenin.
- [Azure CLI kullanarak IPv6 ikili yığın uygulaması dağıtmayı](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)öğrenin.
- [Kaynak Yöneticisi şablonları kullanarak bir IPv6 çift yığın uygulaması dağıtmayı öğrenin (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
