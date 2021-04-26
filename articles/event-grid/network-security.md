---
title: Azure Event Grid kaynakları için ağ güvenliği
description: Bu makalede, çıkış için hizmet etiketlerinin, giriş için IP Güvenlik Duvarı kurallarının ve Azure Event Grid giriş için özel uç noktaların nasıl kullanılacağı açıklanır.
author: VidyaKukke
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: vkukke
ms.openlocfilehash: d58e8b5ce9fb444fa501f897cca722613d9c51fe
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967583"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Azure Event Grid kaynakları için ağ güvenliği
Bu makalede, Azure Event Grid ile aşağıdaki güvenlik özelliklerinin nasıl kullanılacağı açıklanmaktadır: 

- Çıkış için hizmet etiketleri
- Giriş için IP güvenlik duvarı kuralları
- Giriş için özel uç noktalar


## <a name="service-tags"></a>Hizmet etiketleri
Hizmet etiketi, belirli bir Azure hizmetinden bir IP adresi önekleri grubunu temsil eder. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir ve ağ güvenlik kuralları için sık sık güncelleştirmelerin karmaşıklığını en aza indirir. Hizmet etiketleri hakkında daha fazla bilgi için bkz. [hizmet etiketlerine genel bakış](../virtual-network/service-tags-overview.md).

[Ağ güvenlik gruplarında](../virtual-network/network-security-groups-overview.md#security-rules) veya [Azure Güvenlik duvarında](../firewall/service-tags.md)ağ erişim denetimleri tanımlamak için hizmet etiketlerini kullanabilirsiniz. Güvenlik kuralları oluştururken belirli IP adreslerinin yerine hizmet etiketleri kullanın. Bir kuralın uygun *kaynak* veya *hedef* alanındaki hizmet etiketi adını (örneğin, **AzureEventGrid**) belirterek, karşılık gelen hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz.

| Hizmet etiketi | Amaç | Gelen veya giden trafiği kullanabilir miyim? | Bölgesel olabilir mi? | Azure Güvenlik Duvarı ile kullanılabilir mi? |
| --- | -------- |:---:|:---:|:---:|
| AzureEventGrid | Azure Event Grid. | Her ikisi | Hayır | Hayır |


## <a name="ip-firewall"></a>IP güvenlik duvarı 
Azure Event Grid konularda ve etki alanlarında yayımlama için IP tabanlı erişim denetimlerini destekler. IP tabanlı denetimlerle, yayımcıları bir konu veya etki alanıyla yalnızca onaylanmış bir makine ve bulut hizmetleri kümesiyle sınırlayabilirsiniz. Bu özellik Event Grid tarafından desteklenen [kimlik doğrulama mekanizmalarını](security-authentication.md) tamamlar.

Varsayılan olarak, konu ve etki alanına, istek geçerli kimlik doğrulama ve yetkilendirme ile geldiği sürece internet 'ten erişilebilir. IP güvenlik duvarı ile, [CIDR (sınıfsız Inter-Domain yönlendirme)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) gösteriminde yalnızca bir IP adresı veya IP adresi aralığı kümesiyle daha fazla sınırlandırabilirsiniz. Başka herhangi bir IP adresinden kaynaklanan yayımcılar reddedilir ve 403 (yasak) yanıtı alacaktır.

Konular ve etki alanları için IP güvenlik duvarını yapılandırmaya yönelik adım adım yönergeler için bkz. [IP güvenlik duvarını yapılandırma](configure-firewall.md).

## <a name="private-endpoints"></a>Özel uç noktalar
[Özel uç noktaları](../private-link/private-endpoint-overview.md) kullanarak doğrudan sanal ağınızdan, genel İnternet üzerinden geçmeden [özel bir bağlantı](../private-link/private-link-overview.md) üzerinden konulara ve etki alanlarına olay girişi sağlayabilirsiniz. Özel uç nokta, VNet 'iniz içindeki bir Azure hizmeti için özel bir ağ arabirimidir. Konu veya etki alanınız için özel bir uç nokta oluşturduğunuzda, sanal ağınızdaki istemciler ve Event Grid kaynağınız arasında güvenli bağlantı sağlar. Özel uç noktaya sanal Ağınızın IP adresi aralığından bir IP adresi atanır. Özel uç nokta ve Event Grid hizmeti arasındaki bağlantı güvenli bir özel bağlantı kullanır.

![Mimari diyagramı](./media/network-security/architecture-diagram.png)

Event Grid kaynağınız için özel uç noktalar kullanmak şunları yapmanıza olanak sağlar:

- Genel internet yerine Microsoft omurga ağı üzerinden bir VNet 'ten konu veya etki alanına güvenli erişim.
- VPN veya Express yollarını özel eşleme ile kullanarak VNet 'e bağlanan şirket içi ağlardan güvenli bir şekilde bağlanın.

VNet 'iniz içindeki bir konu veya etki alanı için özel bir uç nokta oluşturduğunuzda, kaynak sahibine onay için bir izin isteği gönderilir. Özel uç noktanın oluşturulmasını isteyen kullanıcı aynı zamanda kaynağın sahibiyseniz, bu onay isteği otomatik olarak onaylanır. Aksi takdirde, bağlantı onaylanana kadar **bekleme** durumunda olur. VNet 'teki uygulamalar, başka şekilde kullandıkları aynı bağlantı dizelerini ve yetkilendirme mekanizmalarını kullanarak, Özel uç nokta üzerinden Event Grid hizmetine sorunsuz bir şekilde bağlanabilir. Kaynak sahipleri, Azure portal kaynağın **Özel uç noktaları** sekmesinden izin isteklerini ve özel uç noktaları yönetebilir.

### <a name="connect-to-private-endpoints"></a>Özel uç noktalara bağlan
Özel uç nokta kullanılarak VNet 'teki yayımcılar, genel uç noktaya bağlanan istemciler için konu veya etki alanı için aynı bağlantı dizesini kullanmalıdır. DNS çözümlemesi, VNet 'ten gelen bağlantıları özel bir bağlantı üzerinden konuya veya etki alanına otomatik olarak yönlendirir. Event Grid, varsayılan olarak, Özel uç noktalar için gerekli güncelleştirmeyle VNet 'e bağlı [Özel BIR DNS bölgesi](../dns/private-dns-overview.md) oluşturur. Ancak, kendi DNS sunucunuzu kullanıyorsanız, DNS yapılandırmanızda ek değişiklikler yapmanız gerekebilir.

### <a name="dns-changes-for-private-endpoints"></a>Özel uç noktalar için DNS değişiklikleri
Özel bir uç nokta oluşturduğunuzda, kaynağın DNS CNAME kaydı, öneki olan bir alt etki alanındaki diğer ada güncelleştirilir `privatelink` . Varsayılan olarak, özel bağlantının alt etki alanına karşılık gelen özel bir DNS bölgesi oluşturulur. 

Konuyu veya etki alanı uç noktası URL 'sini özel uç noktayla VNet dışından çözdüğünde, hizmetin genel uç noktasına dönüşür. Özel uç noktasını barındıran **VNET dışından** çözümlendiğinde ' Topica ' için DNS kaynak kayıtları şu şekilde olur:

| Ad                                          | Tür      | Değer                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<Azure traffic manager profile\>

[IP güvenlik duvarını](#ip-firewall)kullanarak genel uç nokta aracılığıyla VNET dışındaki bir istemcinin erişimini reddedebilir veya kontrol edebilirsiniz. 

Özel uç noktasını barındıran VNet 'ten çözümlendiğinde, konu veya etki alanı uç noktası URL 'SI özel uç noktanın IP adresini çözümler. Özel uç noktasını barındıran **VNET 'in içinden** çözümlendiğinde ' Topica ' konusunun DNS kaynak kayıtları şu şekilde olur:

| Ad                                          | Tür      | Değer                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

Bu yaklaşım, Özel uç noktaları ve VNet dışındaki istemcileri barındıran VNet 'teki istemciler için aynı bağlantı dizesini kullanarak konuya veya etki alanına erişim sağlar.

Ağınızda özel bir DNS sunucusu kullanıyorsanız, istemciler konu veya etki alanı uç noktası için FQDN 'yi özel uç nokta IP adresine çözümleyebilir. DNS sunucunuzu özel bağlantı alt etki alanınızı, sanal ağın özel DNS bölgesine devretmek veya `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` Özel uç nokta IP adresi ile Için bir kayıt yapılandırmak üzere yapılandırın.

Önerilen DNS bölge adı `privatelink.eventgrid.azure.net` .

### <a name="private-endpoints-and-publishing"></a>Özel uç noktalar ve yayımlama

Aşağıdaki tabloda özel uç nokta bağlantısının çeşitli durumları ve yayımlamanın etkileri açıklanmaktadır:

| Bağlantı durumu   |  Başarıyla Yayımla (Evet/Hayır) |
| ------------------ | -------------------------------|
| Onaylandı           | Yes                            |
| Reddedildi           | No                             |
| Beklemede            | No                             |
| Bağlantı kesildi       | No                             |

Yayımlamanın başarılı olması için özel uç nokta bağlantı durumunun **onaylanması** gerekir. Bir bağlantı reddedilirse, Azure portal kullanılarak onaylanamaz. Tek olasılık, bağlantıyı silmek ve bunun yerine yeni bir tane oluşturmaktır.

## <a name="pricing-and-quotas"></a>Fiyatlandırma ve Kotalar
**Özel uç noktalar** , Event Grid hem temel hem de Premium katmanlarında kullanılabilir. Event Grid, konuya veya etki alanına göre 64 'e kadar özel uç nokta bağlantısının oluşturulmasına izin verir. 

**IP güvenlik duvarı** özelliği, Event Grid hem temel hem de Premium katmanlarında kullanılabilir. Her konu veya etki alanı için 16 adede kadar IP güvenlik duvarı kuralına izin veririz.

## <a name="next-steps"></a>Sonraki adımlar
Genel internet üzerinden erişimi yalnızca belirli bir IP adresi veya IP adresi aralığı kümesinden kısıtlamak için, Event Grid kaynağınız için IP güvenlik duvarını yapılandırabilirsiniz. Adım adım yönergeler için bkz. [IP güvenlik duvarını yapılandırma](configure-firewall.md).

Yalnızca seçili sanal ağlardan erişimi kısıtlamak için özel uç noktaları yapılandırabilirsiniz. Adım adım yönergeler için bkz. [Özel uç noktaları yapılandırma](configure-private-endpoints.md).

Ağ bağlantısı sorunlarını gidermek için bkz. [ağ bağlantısı sorunlarını giderme](troubleshoot-network-connectivity.md)
