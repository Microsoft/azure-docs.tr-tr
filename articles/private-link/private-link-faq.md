---
title: Azure Özel Bağlantı hakkında sık sorulan sorular (FAQ)
description: Azure özel bağlantısı hakkında bilgi edinin.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: d06e90a691389b99d8f439364203b921f49b2305
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496482"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure Özel Bağlantı hakkında sık sorulan sorular (FAQ)

## <a name="private-link"></a>Özel Bağlantı

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Azure özel uç noktası ve Azure özel bağlantı hizmeti nedir?

- **[Azure özel uç](private-endpoint-overview.md)** noktası: Azure özel uç noktası, Azure özel bağlantısı tarafından desteklenen bir hizmete özel ve güvenli bir şekilde bağlanan bir ağ arabirimidir. Özel uç noktaları, özel bağlantıyı destekleyen bir Azure PaaS hizmetine veya kendi özel bağlantı hizmetinize bağlanmak için kullanabilirsiniz.
- **[Azure özel bağlantı hizmeti](private-link-service-overview.md)**: Azure özel bağlantı hizmeti, bir hizmet sağlayıcısı tarafından oluşturulan bir hizmettir. Şu anda bir özel bağlantı hizmeti bir Standart Load Balancer ön uç IP yapılandırmasına bağlanabilir. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Özel bağlantı kullanılırken trafik nasıl gönderiliyor?
Trafik, Microsoft omurgası kullanılarak özel olarak gönderilir. İnternet 'te geçiş yapmaz. Azure özel bağlantısı müşteri verilerini depolamaz.
 
### <a name="what-is-the-difference-between-service-endpoints-and-private-endpoints"></a>Hizmet uç noktaları ve özel uç noktalar arasındaki fark nedir?
- Özel uç noktalar, belirli bir hizmetin arkasındaki belirli kaynaklara ağ erişimi sağlayarak parçalı segmentasyon sağlar. Trafik, genel uç noktaları kullanmadan Şirket içinden hizmet kaynağına ulaşabilir.
- Hizmet uç noktası, genel olarak yönlendirilebilir bir IP adresi kalır.  Özel uç nokta, Özel uç noktanın yapılandırıldığı sanal ağın adres alanındaki özel bir IP 'dir.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Özel bağlantı hizmeti ve özel uç nokta arasındaki ilişki nedir?
Birden çok özel bağlantı kaynak türü özel uç nokta aracılığıyla erişimi destekler. Kaynaklara Azure PaaS hizmetleri ve kendi özel bağlantı hizmetiniz dahildir. Tek-çok ilişkisi. 

Özel bir bağlantı hizmeti, birden çok özel uç noktasından bağlantı alır. Özel bir uç nokta, bir özel bağlantı hizmetine bağlanır.    

### <a name="do-i-need-to-disable-network-policies-for-private-link"></a>Özel bağlantı için ağ ilkelerini devre dışı bırakmem gerekiyor
Evet. Hem özel uç nokta hem de özel bağlantı hizmetinin düzgün çalışması için ağ ilkelerini devre dışı bırakmalıdır. Her ikisi de birbirinden bağımsız özelliklere sahiptir.

## <a name="private-endpoint"></a>Özel Uç Nokta 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Aynı VNet 'te birden fazla özel uç nokta oluşturabilir miyim? Farklı hizmetlere bağlanabilir mi? 
Evet. Aynı VNet veya alt ağda birden fazla özel uç noktaya sahip olabilirsiniz. Farklı hizmetlere bağlanabilirler.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Özel uç noktalar için ayrılmış bir alt ağa ihtiyacım var mı? 
Hayır. Özel uç noktalar için adanmış bir alt ağ gerekmez. Hizmetinizin dağıtıldığı VNet 'ten herhangi bir alt ağdan özel bir uç nokta IP 'si seçebilirsiniz.  
 
### <a name="can-a-private-endpoint-connect-to-private-link-services-across-azure-active-directory-tenants"></a>Özel bir uç nokta, Azure Active Directory kiracılar genelinde özel bağlantı hizmetlerine bağlanabilir mi? 
Evet. Özel uç noktalar, özel bağlantı hizmetlerine veya Azure PaaS Azure Active Directory kiracılar arasında bağlanabilir. Kiracılar genelinde bağlanan özel uç noktalar el ile istek onayı gerektirir. 
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Özel uç nokta Azure bölgeleri arasında Azure PaaS kaynaklarına bağlanabilir mi?
Evet. Özel uç noktalar Azure bölgeleri arasında Azure PaaS kaynaklarına bağlanabilir.

### <a name="can-i-modify-my-private-endpoint-network-interface-nic-"></a>Özel uç nokta ağ Arabirimmi (NIC) değiştirebilir miyim?
Özel bir uç nokta oluşturulduğunda, salt okunurdur bir NIC atanır. Bu değiştirilemez ve özel uç noktanın yaşam döngüsü için kalır.

### <a name="how-do-i-achieve-availability-while-using-private-endpoints-in-case-of-regional-failures-"></a>Bölgesel hatalarda özel uç noktaları kullanırken Nasıl yaparım? kullanılabilirlik elde edilsin mi?

Özel uç noktalar% 99,99 SLA [[Azure özel bağlantısı Için SLA]](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/)olan yüksek oranda kullanılabilir kaynaklardır. Ancak, bölgesel kaynaklar olduklarından, tüm Azure bölge kesintisi kullanılabilirliği etkileyebilir. Bölgesel hatalarda kullanılabilirlik sağlamak için, aynı hedef kaynağa bağlanmış birden çok PEs farklı bölgelerde dağıtılabilir. Bu şekilde, bir bölge, hedef kaynağa erişmek için farklı bir bölgedeki PE aracılığıyla kurtarma senaryolarınız için trafiği yönlendirmeye devam edebilir. Hedef hizmet tarafında bölgesel hataların nasıl işlendiği hakkında bilgi için lütfen yük devretme ve kurtarma hakkındaki hizmet belgelerini gözden geçirin. Özel bağlantı trafiği, hedef uç noktası için Azure DNS çözümlemesini izler. 


## <a name="private-link-service"></a>Özel Bağlantı Hizmeti
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Özel bir bağlantı hizmeti oluşturmak için önkoşulların önkoşulları nelerdir? 
Hizmet arka uçları bir sanal ağda ve bir Standart Load Balancer arkasında olmalıdır.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Özel bağlantı hizmetinizi nasıl ölçeklendirebilirim? 
Özel bağlantı hizmetinizi birkaç farklı şekilde ölçeklendirebilirsiniz: 
- Standart Load Balancer arkasındaki havuza arka uç VM 'Leri ekleyin 
- Özel bağlantı hizmetine bir IP ekleyin. Özel bağlantı hizmeti başına en fazla 8 IP 'ye izin veririz.  
- Standart Load Balancer yeni özel bağlantı hizmeti ekleyin. Yük dengeleyici başına en fazla sekiz özel bağlantı hizmetine izin veriyoruz.   

### <a name="what-is-natnetwork-address-translation-ip-configuration-used-in-private-link-service-how-can-i-scale-in-terms-of-available-ports-and-connections"></a>Özel bağlantı hizmetinde NAT (ağ adresi çevirisi) IP yapılandırması kullanılır? Kullanılabilir bağlantı noktaları ve bağlantılar açısından nasıl ölçeklendirebilirim? 

NAT IP yapılandırması, hedef taraftaki (hizmet sağlayıcı tarafı) özel bağlantı trafiğine kaynak NAT sağlayarak kaynak (tüketici tarafı) ve hedef (hizmet sağlayıcı) adres alanı arasında bir IP çakışması olmamasını sağlar. NAT IP adresi, hizmetiniz tarafından gönderilen tüm paketler için hizmetinize ve hedef IP 'niz tarafından alınan tüm paketlerin kaynak IP 'si olarak görünür.  NAT IP 'si, bir hizmet sağlayıcısının sanal ağındaki herhangi bir alt ağdan seçilebilir. 

Her NAT IP, Standart Load Balancer arka plandaki sanal makine başına 64K TCP bağlantısı (64K bağlantı noktası) sağlar. Daha fazla bağlantı ölçeklendirmek ve eklemek için yeni NAT IP 'Leri ekleyebilir veya Standart Load Balancer arkasına daha fazla VM ekleyebilirsiniz. Bunun yapılması, bağlantı noktası kullanılabilirliğini ölçeklendirecektir ve daha fazla bağlantı sağlar. Bağlantı, Standart Load Balancer arkasındaki NAT IP 'Leri ve VM 'Ler arasında dağıtılır.

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Hizmetmi birden çok özel uç noktaya bağlanabilir miyim?
Evet. Bir özel bağlantı hizmeti birden çok özel uç noktasından bağlantı alabilir. Ancak, bir özel uç nokta yalnızca bir özel bağlantı hizmetine bağlanabilir.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Özel bağlantı hizmetimin görünürlüğünü nasıl denetlerim?
Özel bağlantı hizmetindeki görünürlük yapılandırmasını kullanarak pozlamayı denetleyebilirsiniz. Görünürlük üç ayarı destekler:

- **Hiçbiri** -yalnızca Azure RBAC erişimi olan abonelikler hizmeti bulabilir. 
- **Kısıtlayıcı** -yalnızca onaylanmış ve Azure RBAC erişimi olan abonelikler hizmeti bulabilir. 
- **Tümü** -herkes hizmeti bulabilir. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Temel yük dengeleyiciye sahip bir özel bağlantı hizmeti oluşturabilir miyim? 
Hayır. Temel yük dengeleyici üzerinden özel bağlantı hizmeti desteklenmez.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Özel bağlantı hizmeti için ayrılmış bir alt ağ mı gerekiyor? 
Hayır. Özel bağlantı hizmeti adanmış bir alt ağ gerektirmez. Sanal ağınız için hizmetinizin dağıtıldığı herhangi bir alt ağ seçebilirsiniz.   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Azure özel bağlantısını kullanan bir hizmet sağlayıcısıyım. Tüm müşterilerimin benzersiz IP alanına sahip olduğundan ve IP alanım ile çakışmadığından emin olmam gerekiyor mu? 
Hayır. Azure özel bağlantısı, sizin için bu işlevselliği sağlar. Müşterinizin adres alanı ile çakışmayan adres alanı olması gerekmez. 

##  <a name="next-steps"></a>Sonraki adımlar

- [Azure özel bağlantısı](private-link-overview.md) hakkında bilgi edinin
