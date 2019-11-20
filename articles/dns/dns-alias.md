---
title: Azure DNS diğer ad kayıtlarına genel bakış
description: Microsoft Azure DNS 'de diğer ad kayıtlarına yönelik desteğe genel bakış.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: victorh
ms.openlocfilehash: 9a3cdb846921c2d73dd2cca5d679663c1ba9e192
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946893"
---
# <a name="azure-dns-alias-records-overview"></a>Azure DNS diğer ad kayıtlarına genel bakış

Azure DNS diğer ad kayıtları, DNS kayıt kümesindeki niteliklerdir. Bunlar, DNS bölgenizin içinden diğer Azure kaynaklarına başvurabilir. Örneğin, bir kayıt yerine bir Azure genel IP adresine başvuran bir diğer ad kayıt kümesi oluşturabilirsiniz. Diğer ad kayıt kümesi, dinamik olarak bir Azure genel IP adresi hizmeti örneğine işaret eder. Sonuç olarak, diğer ad kayıt kümesi DNS çözümlemesi sırasında kendisini sorunsuz bir şekilde günceller.

Bir Azure DNS bölgesindeki aşağıdaki kayıt türleri için bir diğer ad kayıt kümesi desteklenir: 

- A
- AAAA
- CNAME

> [!NOTE]
> Bir [Azure Traffic Manager profilini](../traffic-manager/quickstart-create-traffic-manager-profile.md) işaret etmek için A veya aaaa kayıt türleri için bir diğer ad kaydı kullanmayı düşünüyorsanız, Traffic Manager profilinin yalnızca [dış uç noktalara](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)sahip olduğundan emin olmanız gerekir. Traffic Manager dış uç noktalar için IPv4 veya IPv6 adresi sağlamanız gerekir. Uç noktalarda tam etki alanı adlarını (FQDN) kullanamazsınız. İdeal olarak, statik IP adresleri kullanın.

## <a name="capabilities"></a>Özellikler

- **DNS A/AAAA kayıt kümesinden ortak bir IP kaynağını işaret edin.** A/AAAA kayıt kümesi oluşturabilir ve bunu bir genel IP kaynağını işaret etmek üzere bir diğer ad kaydı kümesi (Standart veya temel) yapabilirsiniz. Genel IP adresi değişirse veya silinirse DNS kayıt kümesi otomatik olarak değişir. Hatalı IP adreslerine işaret eden, salyaze DNS kayıtları kaçınılmaz.

   Kaynak başına 20 diğer ad kaydı kümesi geçerli bir sınırı vardır.

- **DNS A/AAAA/CNAME kayıt kümesinden bir Traffic Manager profile işaret edin.** A/AAAA veya CNAME kayıt kümesi oluşturabilir ve diğer ad kayıtlarını kullanarak bir Traffic Manager profiline işaret edebilirsiniz. Bir bölge tepesinde için geleneksel CNAME kayıtları desteklenmediği için, trafiği bir bölge tepesinde yönlendirmeniz gerektiğinde bu durum özellikle yararlıdır. Örneğin, Traffic Manager profilinizin myprofile.trafficmanager.net olduğunu ve iş DNS bölgenizin contoso.com olduğunu varsayalım. Contoso.com (Zone tepesinde) için A/AAAA türünde bir diğer ad kayıt kümesi oluşturabilir ve myprofile.trafficmanager.net ' ye işaret edebilirsiniz.
- **Azure Content Delivery Network (CDN) uç noktasını Işaret edin**. Bu, Azure depolama ve Azure CDN kullanarak statik Web siteleri oluşturduğunuzda yararlı olur.
- **Aynı bölge içindeki başka bir DNS kayıt kümesine işaret edin.** Diğer ad kayıtları aynı türdeki diğer kayıt kümelerine başvurabilir. Örneğin, bir DNS CNAME kayıt kümesi başka bir CNAME kayıt kümesine diğer ad olabilir. Bu düzenleme, bazı kayıt kümelerinin diğer adlar ve diğer ad olmayan adlar olmasını istiyorsanız yararlıdır.

## <a name="scenarios"></a>Senaryolar

Diğer ad kayıtları için birkaç yaygın senaryo vardır.

### <a name="prevent-dangling-dns-records"></a>Tehlikeden DNS kayıtlarını önleme

Geleneksel DNS kayıtlarıyla ilgili yaygın bir sorun, kayıt kayıtlardır. Örneğin, IP adreslerinde yapılan değişiklikleri yansıtacak şekilde güncelleştirilmemiş DNS kayıtları. Sorun özellikle bir/AAAA veya CNAME kayıt türleriyle oluşur.

Geleneksel bir DNS bölgesi kaydıyla, hedef IP veya CNAME artık yoksa, onunla ilişkilendirilen DNS kaydı el ile güncelleştirilmeleri gerekir. Bazı kuruluşlarda, işlem sorunları veya rollerin ve ilişkili izin düzeylerinin ayrımı nedeniyle el ile güncelleştirme zamanında gerçekleşmeyebilir. Örneğin bir rol, bir uygulamaya ait bir CNAME veya IP adresini silme yetkisine sahip olabilir. Ancak, bu hedeflere işaret eden DNS kaydını güncelleştirmek için yeterli yetkisi yok. DNS kaydını güncelleştirmede bir gecikme, kullanıcılar için bir kesinti oluşmasına neden olabilir.

Diğer ad kayıtları, bir Azure kaynağı ile bir DNS kaydının yaşam döngüsünü sıkı bir şekilde uzatarak tehlikeden başvuruların oluşmasını önler. Örneğin, bir genel IP adresini veya bir Traffic Manager profilini işaret etmek için bir diğer ad kaydı olarak nitelenen bir DNS kaydını göz önünde bulundurun. Bu temel alınan kaynakları silerseniz, DNS diğer ad kaydı boş bir kayıt kümesi haline gelir. Artık silinen kaynağa başvurmuyor.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>DNS kaydını Güncelleştir-uygulama IP adresleri değiştiğinde otomatik olarak ayarla

Bu senaryo öncekiyle benzerdir. Belki de bir uygulama taşınmış veya temeldeki sanal makine yeniden başlatıldı. Daha sonra bir diğer ad kaydı, temel alınan genel IP kaynağı için IP adresi değiştiğinde otomatik olarak güncelleştirilir. Bu, kullanıcıları eski genel IP adresine atanmış başka bir uygulamaya yönlendiren olası güvenlik risklerini önler.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Tepesinde bölgesindeki yük dengeli uygulamaları barındırın

DNS protokolü, CNAME kayıtlarının tepesinde bölgesinde atanmasını engeller. Örneğin, etki alanınız contoso.com ise somelabel.contoso.com için CNAME kayıtları oluşturabilirsiniz; ancak contoso.com kendisi için CNAME oluşturamazsınız.
Bu kısıtlama, [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)arkasındaki yük dengeli uygulamalara sahip olan uygulama sahipleri için bir sorun gösterir. Bir Traffic Manager profilinin kullanılması için CNAME kaydı oluşturulması gerektiğinden, bölge tepesinde profil Traffic Manager profiline işaret etmek mümkün değildir.

Bu sorun diğer ad kayıtları kullanılarak çözülebilir. CNAME kayıtlarının aksine, diğer ad kayıtları bölge tepesinde kayıtlarını dış uç noktalara sahip bir Traffic Manager profiline işaret etmek için tepesinde ve uygulama sahipleri tarafından kullanılabilir. Uygulama sahipleri, DNS bölgesi içindeki diğer etki alanı için kullanılan Traffic Manager profile işaret noktasıdır.

Örneğin, contoso.com ve www\.contoso.com aynı Traffic Manager profilini işaret edebilir. Azure Traffic Manager profilleriyle diğer ad kayıtlarını kullanma hakkında daha fazla bilgi edinmek için sonraki adımlar bölümüne bakın.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Azure CDN uç noktalarına nokta dilimi tepesinde

Traffic Manager bir profilde olduğu gibi, DNS bölgenizi Azure CDN tepesinde uç noktalarına göstermek için diğer ad kayıtlarını da kullanabilirsiniz. Bu, Azure depolama ve Azure CDN kullanarak statik Web siteleri oluşturduğunuzda yararlı olur. Daha sonra Web sitesine "www" önlemeden DNS adınızı gönderebilirsiniz.

Örneğin, statik Web siteniz www.contoso.com olarak adlandırılmışsa, kullanıcılarınız www 'i DNS adına eklemek zorunda kalmadan contoso.com kullanarak sitenize erişebilirler.

Daha önce açıklandığı gibi, CNAME kayıtları tepesinde bölgesinde desteklenmez. Bu nedenle, contoso.com 'i CDN uç noktanıza göstermek için bir CNAME kaydı kullanamazsınız. Bunun yerine, bölge tepesinde doğrudan bir CDN uç noktasına göstermek için bir diğer ad kaydı kullanabilirsiniz.

> [!NOTE]
> Akamai 'dan Azure CDN için bir bölge tepesinde, CDN uç noktalarına işaret ediyor, şu anda desteklenmiyor.

## <a name="next-steps"></a>Sonraki adımlar

Diğer ad kayıtları hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Öğretici: Azure genel IP adresine başvurmak için bir diğer ad kaydı yapılandırma](tutorial-alias-pip.md)
- [Öğretici: Traffic Manager ile tepesinde etki alanı adlarını desteklemek için bir diğer ad kaydı yapılandırma](tutorial-alias-tm.md)
- [DNS SSS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
