---
title: Azure özel bağlantı hakkında sık sorulan sorular (SSS)
description: Azure özel bağlantısı hakkında bilgi edinin.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 32e570e098c9a3f95dfc5393348cccb1ebe1fb60
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104580"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure özel bağlantı hakkında sık sorulan sorular (SSS)

## <a name="private-link"></a>Özel Bağlantı

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Azure özel uç noktası ve Azure özel bağlantı hizmeti nedir?

- **[Azure özel uç noktası](private-endpoint-overview.md)** : Azure özel uç noktası, Azure özel bağlantısı tarafından desteklenen bir hizmete özel ve güvenli bir şekilde bağlanan bir ağ arabirimidir. Özel uç noktaları, özel bağlantıyı destekleyen bir Azure PaaS hizmetine veya kendi özel bağlantı hizmetinize bağlanmak için kullanabilirsiniz.
- **[Azure özel bağlantı hizmeti](private-link-service-overview.md)** : Azure özel bağlantı hizmeti, bir hizmet sağlayıcısı tarafından oluşturulan bir hizmettir. Şu anda bir özel bağlantı hizmeti bir Standart Load Balancer ön uç IP yapılandırmasına bağlanabilir. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Özel bağlantı kullanılırken trafik nasıl gönderiliyor?
Trafik, Microsoft omurgası kullanılarak özel olarak gönderilir. İnternet 'te geçiş yapmaz.  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>Hizmet uç noktaları ve özel uç noktalar arasındaki fark nedir?
- Özel uç noktalar kullanılırken, belirli bir hizmetin arkasındaki belirli kaynaklara ağ erişimi verilir ve bu da trafik, genel uç noktaları kullanmadan şirket içi hizmet kaynağına erişebilir.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Özel bağlantı hizmeti ve özel uç nokta arasındaki ilişki nedir?
Özel uç nokta, Azure PaaS hizmetleri ve kendi özel bağlantı hizmetiniz dahil olmak üzere birden çok özel bağlantı kaynak türüne erişim sağlar. Bire çok ilişkisi vardır. Bir özel bağlantı hizmeti birden çok özel uç noktasından bağlantı alabilir. Öte yandan, bir özel uç nokta yalnızca bir özel bağlantı hizmetine bağlanabilir.    

## <a name="private-endpoint"></a>Özel Uç Nokta 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Aynı VNet 'te birden fazla özel uç nokta oluşturabilir miyim? Farklı hizmetlere bağlanabilir mi? 
Evet. Aynı VNet veya alt ağda birden fazla özel uç noktaya sahip olabilirsiniz. Farklı hizmetlere bağlanabilirler.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Özel uç noktalar için ayrılmış bir alt ağa ihtiyacım var mı? 
Hayır. Özel uç noktalar için adanmış bir alt ağ gerekmez. Hizmetinizin dağıtıldığı VNet 'ten herhangi bir alt ağdan özel bir uç nokta IP 'si seçebilirsiniz.  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>Özel uç nokta, Azure Active Directory kiracılar genelinde özel bağlantı hizmetine bağlanabilir mi? 
Evet. Özel uç noktalar, AD kiracılarında özel bağlantı hizmetlerine veya Azure PaaS 'ye bağlanabilir.  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Özel uç nokta Azure bölgeleri arasında Azure PaaS kaynaklarına bağlanabilir mi?
Evet. Özel uç noktalar Azure bölgeleri arasında Azure PaaS kaynaklarına bağlanabilir.

## <a name="private-link-service"></a>Özel Bağlantı Hizmeti
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Özel bir bağlantı hizmeti oluşturmak için önkoşulların önkoşulları nelerdir? 
Hizmet arka uçları bir sanal ağda ve bir Standart Load Balancer arkasında olmalıdır.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Özel bağlantı hizmetinizi nasıl ölçeklendirebilirim? 
Özel bağlantı hizmetinizi birkaç farklı şekilde ölçeklendirebilirsiniz: 
- Standart Load Balancer arkasındaki havuza arka uç VM 'Leri ekleyin 
- Özel bağlantı hizmetine bir IP ekleyin. Özel bağlantı hizmeti başına en fazla 8 IP 'ye izin veririz.  
- Standart Load Balancer yeni özel bağlantı hizmeti ekleyin. Yük dengeleyici başına en fazla sekiz özel bağlantı hizmetine izin veriyoruz.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Hizmetmi birden çok özel uç noktaya bağlanabilir miyim?
Evet. Bir özel bağlantı hizmeti birden çok özel uç noktasından bağlantı alabilir. Ancak, bir özel uç nokta yalnızca bir özel bağlantı hizmetine bağlanabilir.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Özel bağlantı hizmetimin görünürlüğünü nasıl denetlerim?
Özel bağlantı hizmetindeki görünürlük yapılandırmasını kullanarak pozlamayı denetleyebilirsiniz. Görünürlük üç ayarı destekler:

- **Hiçbiri** -yalnızca RBAC erişimi olan abonelikler hizmeti bulabilir. 
- **Kısıtlayıcı** -yalnızca, beyaz listeye eklenen ve RBAC erişimi olan abonelikler hizmeti bulabilir. 
- **Tümü** -herkes hizmeti bulabilir. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Temel Load Balancer bir özel bağlantı hizmeti oluşturabilir miyim? 
Hayır. Temel bir Load Balancer özel bağlantı hizmeti desteklenmez.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Özel bağlantı hizmeti için ayrılmış bir alt ağ mı gerekiyor? 
Hayır. Özel bağlantı hizmeti adanmış bir alt ağ gerektirmez. Sanal ağınız için hizmetinizin dağıtıldığı herhangi bir alt ağ seçebilirsiniz.   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Azure özel bağlantısını kullanan bir hizmet sağlayıcım. Tüm müşterilerimin benzersiz IP alanına sahip olduğundan ve IP alanım ile çakışmadığından emin olmam gerekiyor mu? 
Hayır. Azure özel bağlantısı, sizin için bu işlevselliği sağlar. Bu nedenle, müşterinizin adres alanı ile çakışmayan adres alanı olması gerekmez. 

##  <a name="next-steps"></a>Sonraki adımlar

- [Azure özel bağlantısı](private-link-overview.md) hakkında bilgi edinin
