---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 821746856cb37781c8f6a2e58659ce7db43e1479
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609395"
---
Bölgesel VNet tümleştirmesini kullanmak, uygulamanızın erişmesini sağlar:

* Uygulamanızla aynı bölgedeki bir VNet 'teki kaynaklar.
* Sanal ağlardaki VNet 'lerdeki kaynaklar, uygulamanızın tümleştirildiği VNet 'e tümleştirilir.
* Hizmet uç noktası güvenliği sağlanmış hizmetler.
* Azure ExpressRoute bağlantıları genelindeki kaynaklar.
* Tümleştirmiş olduğunuz VNet 'teki kaynaklar.
* Azure ExpressRoute bağlantılarını içeren, eşlenen bağlantılar arasındaki kaynaklar.
* Özel uç noktalar 

VNet ile VNet tümleştirmesini aynı bölgede kullandığınızda, aşağıdaki Azure ağ özelliklerini kullanabilirsiniz:

* **Ağ güvenlik grupları (NSG 'ler)**: Tümleştirme alt ağınıza yerleştirilmiş bir NSG ile giden trafiği engelleyebilirsiniz. Uygulama için gelen erişimi sağlamak üzere VNet tümleştirmesini kullanamadığından gelen kuralları uygulanmaz.
* **Yol tabloları (UDRs)**: istediğiniz yere giden trafik göndermek için tümleştirme alt ağına bir rota tablosu yerleştirebilirsiniz.

Varsayılan olarak, uygulamanız yalnızca sanal ağınıza RFC1918 trafiği yönlendirir. Tüm giden trafiğinizi VNet 'e yönlendirmek istiyorsanız, ayarı uygulamanıza eklemek için aşağıdaki adımları kullanın `WEBSITE_VNET_ROUTE_ALL` : 

1. Uygulama portalınızdaki **yapılandırma** Kullanıcı arabirimine gidin. **Yeni uygulama ayarı**'nı seçin.
1. `WEBSITE_VNET_ROUTE_ALL` **Ad** kutusuna girin ve `1` **değer** kutusuna girin.

   ![Uygulama ayarı sağla][4]

1. **Tamam**’ı seçin.
1. **Kaydet**’i seçin.

> [!NOTE]
> Tüm giden trafiğinizi sanal ağınıza yönlendirdiğinizde, tümleştirme alt ağınıza uygulanan NSG 'ler ve UDRs 'ye tabidir. `WEBSITE_VNET_ROUTE_ALL`Olarak ayarlandığında `1` , trafiği başka bir yerde yönlendiren yollar sağlamazsanız, giden trafik hala uygulama özelliklerinde listelenen adreslerden gönderilir.
> 
> Bölgesel VNet tümleştirmesi, 25 numaralı bağlantı noktasını kullanamaz.

Aynı bölgedeki sanal ağlar ile VNet tümleştirmesi kullanımıyla ilgili bazı sınırlamalar vardır:

* Genel eşleme bağlantıları genelindeki kaynaklara ulaşıamazsınız.
* Özelliği Premium v2 ve Premium v3 'deki tüm App Service ölçek birimlerinden kullanılabilir. Yalnızca daha yeni App Service ölçek birimlerinden standart olarak da kullanılabilir. Daha eski bir ölçek biriminiz varsa, özelliği yalnızca Premium v2 App Service planınızdan kullanabilirsiniz. Özelliği standart bir App Service planında kullanabilmeniz için uygulamanızı Premium v3 App Service planına göre oluşturun. Bu planlar yalnızca en yeni ölçek birimlerimiz üzerinde desteklenir. Bundan sonra isterseniz ölçeği azaltabilirsiniz.  
* Tümleştirme alt ağı yalnızca bir App Service planı tarafından kullanılabilir.
* Özelliği, bir App Service Ortamı olan yalıtılmış plan uygulamaları tarafından kullanılamaz.
* Özelliği, bir Azure Resource Manager VNet 'te bir/28 veya daha büyük olan kullanılmayan bir alt ağ gerektirir.
* Uygulama ve VNet aynı bölgede olmalıdır.
* Tümleşik bir uygulamayla VNet 'i silemezsiniz. VNet 'i silmeden önce tümleştirmeyi kaldırın.
* App Service planı başına yalnızca bir bölgesel VNet tümleştirmesi olabilir. Aynı App Service planındaki birden çok uygulama aynı VNet 'i kullanabilir.
* Bölgesel VNet tümleştirmesi kullanan bir uygulama varken, bir uygulamanın veya planın aboneliğini değiştiremezsiniz.
* Uygulamanız, yapılandırma değişiklikleri olmadan Azure DNS Özel Bölgeleri adresleri çözemez.

VNet tümleştirmesi ayrılmış bir alt ağa bağlıdır. Bir alt ağ sağladığınızda, Azure alt ağı başlangıçtan itibaren beş IP 'yi kaybeder. Her plan örneği için tümleştirme alt ağından bir adres kullanılır. Uygulamanızı dört örneğe ölçeklendirirseniz dört adres kullanılır. 

Boyutu ölçeği büyütme veya küçültme sırasında, kısa bir süre için gereken adres alanı iki katına çıkar. Bu, belirli bir alt ağ boyutu için gerçek ve kullanılabilir desteklenen örnekleri etkiler. Aşağıdaki tabloda, her iki CıDR bloğu için kullanılabilen en fazla adres ve bu da yatay ölçekte etkisi gösterilmektedir:

| CıDR blok boyutu | Kullanılabilir en fazla adresler | Maksimum yatay ölçek (örnekler)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| /28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| /26             | 59                      | 29                              |

<sup>*</sup>Herhangi bir noktada boyut veya SKU 'da ölçeği büyütme veya küçültme yapmanız gerektiğini varsayar. 

Alt ağ boyutu atamadan sonra değiştirilemediğinden, uygulamanızın ulaşabileceği ölçeğe uyum sağlayacak kadar büyük bir alt ağ kullanın. Alt ağ kapasitesine sahip herhangi bir sorunu önlemek için, 64 adresi ile bir/26 kullanmanız gerekir.  

Farklı bir plandaki uygulamalarınızın, başka bir plandaki uygulamalar tarafından zaten bağlı olan bir VNet 'e ulaşmasını istediğinizde, önceden var olan VNet tümleştirmesi tarafından kullanılandan farklı bir alt ağ seçin.

Özelliği, [özel kapsayıcılar](../articles/app-service/quickstart-custom-container.md)dahil olmak üzere hem Windows hem de Linux uygulamaları için tam olarak desteklenmektedir. Tüm davranışlar Windows Uygulamaları ve Linux uygulamaları arasında aynı şekilde davranır.

### <a name="service-endpoints"></a>Hizmet uç noktaları

Bölgesel VNet tümleştirmesi, hizmet uç noktaları ile güvenliği sağlanmış Azure hizmetlerine ulaşmanıza olanak sağlar. Hizmet uç noktası güvenli hizmetine erişmek için aşağıdakileri yapmanız gerekir:

1. Tümleştirme için belirli bir alt ağa bağlanmak üzere Web uygulamanızla bölgesel VNet tümleştirmesini yapılandırın.
1. Hedef hizmete gidin ve hizmet uç noktalarını tümleştirme alt ağına göre yapılandırın.

### <a name="network-security-groups"></a>Ağ güvenlik grupları

Ağ güvenlik gruplarını, bir sanal ağdaki kaynaklara gelen ve giden trafiği engellemek için kullanabilirsiniz. Bölgesel VNet tümleştirmesi kullanan bir uygulama, VNet veya Internet 'teki kaynaklara giden trafiği engellemek için bir [ağ güvenlik grubu][VNETnsg] kullanabilir. Ortak adreslere giden trafiği engellemek için uygulama ayarı `WEBSITE_VNET_ROUTE_ALL` olarak ayarlanmış olmalıdır `1` . VNet tümleştirmesi uygulamanızdan yalnızca giden trafiği etkilediği için NSG 'deki gelen kuralları uygulamanıza uygulanmaz.

Uygulamanıza gelen trafiği denetlemek için erişim kısıtlamaları özelliğini kullanın. Tümleştirme alt ağına uygulanan bir NSG, tümleştirme alt ağına uygulanan rotalara bakılmaksızın etkili olur. `WEBSITE_VNET_ROUTE_ALL`, Olarak ayarlanmışsa `1` ve tümleştirme alt ağınızda ortak adres trafiğini etkileyen bir yolunuz yoksa, tüm giden trafiğiniz hala tümleştirme alt ağına atanmış olan NSG 'lere tabidir. `WEBSITE_VNET_ROUTE_ALL`Ayarlanmadıysa, NSG 'ler yalnızca RFC1918 trafiğe uygulanır.

### <a name="routes"></a>Yollar

Uygulama, giden trafiği uygulamanızdaki her yere yönlendirmek için yol tabloları ' nı kullanabilirsiniz. Varsayılan olarak, yol tabloları yalnızca RFC1918 hedef trafiğinizi etkiler. `WEBSITE_VNET_ROUTE_ALL` `1` ' I ' a ayarladığınızda, tüm giden çağrılarınız etkilenir. Tümleştirme alt ağınızda ayarlanan yollar, gelen uygulama isteklerine olan yanıtları etkilemez. Ortak hedefler, güvenlik duvarı cihazları veya ağ geçitleri içerebilir.

Şirket içinde tüm giden trafiği yönlendirmek isterseniz, tüm giden trafiği ExpressRoute ağ geçidize göndermek için bir yol tablosu kullanabilirsiniz. Trafiği bir ağ geçidine yönlendirdiğinizde, dış ağdaki yolları, yanıtları geri gönderecek şekilde ayarladığınızdan emin olun.

Sınır Ağ Geçidi Protokolü (BGP) rotaları de uygulama trafiğinizi etkiler. ExpressRoute ağ geçidine benzer bir şekilde BGP yollarınız varsa, uygulamanızın giden trafiği etkilenir. Varsayılan olarak BGP yolları yalnızca RFC1918 hedef trafiğinizi etkiler. `WEBSITE_VNET_ROUTE_ALL`Olarak ayarlandığında `1` , tüm gıden trafik BGP rotalarınız tarafından etkilenebilir.

### <a name="azure-dns-private-zones"></a>Özel bölgeler Azure DNS 

Uygulamanız VNet 'iniz ile tümleştirdikten sonra, sanal ağınızın yapılandırıldığı aynı DNS sunucusunu kullanır. Varsayılan olarak, uygulamanız Azure DNS özel bölgelerle çalışmaz. Azure DNS özel bölgelerle çalışmak için aşağıdaki uygulama ayarlarını eklemeniz gerekir:

1. `WEBSITE_DNS_SERVER` değer ile `168.63.129.16`
1. `WEBSITE_VNET_ROUTE_ALL` değer ile `1`

Bu ayarlar, uygulamanızdaki tüm giden çağrılarınızı VNet 'e gönderir ve uygulamanızın Azure DNS bir özel bölgeye erişmesini sağlar. Bu ayarlarla, uygulamanız, çalışan düzeyindeki DNS özel bölgesini sorgulayarak Azure DNS kullanabilir.  

### <a name="private-endpoints"></a>Özel Uç Noktalar

[Özel uç noktalara][privateendpoints]çağrılar yapmak ISTIYORSANıZ, DNS aramalarınızın özel uç noktaya çözümlendiğinizden emin olmanız gerekir. Bu davranışı aşağıdaki yollarla zorunlu kılabilirsiniz: 

* Azure DNS özel bölgelerle tümleştirin. VNet 'iniz özel bir DNS sunucusuna sahip olmadığında, bu otomatik olarak yapılır.
* Uygulamanız tarafından kullanılan DNS sunucusunda özel uç noktasını yönetin. Bunu yapmak için özel uç nokta adresini bilmeniz ve sonra bir kayıt kullanarak bu adrese ulaşmaya çalıştığınız uç noktayı işaret etmeniz gerekir.
* Kendi DNS sunucunuzu Azure DNS özel bölgelere iletmek üzere yapılandırın.

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
