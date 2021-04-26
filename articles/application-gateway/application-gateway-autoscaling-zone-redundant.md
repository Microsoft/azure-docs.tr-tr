---
title: Otomatik ölçeklendirme ve Alanlar arası yedekli Application Gateway v2
description: Bu makalede, otomatik ölçeklendirme ve bölgesel olarak yedekli özellikler içeren Azure Uygulama Standard_v2 ve WAF_v2 SKU açıklanır.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: victorh
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: fad6e27c4ee7e8c10237cb3face5cfab9329b2ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98059730"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Otomatik ölçeklendirme ve Alanlar arası yedekli Application Gateway v2 

Application Gateway, bir Standard_v2 SKU 'SU altında bulunabilir. Web uygulaması güvenlik duvarı (WAF), bir WAF_v2 SKU 'SU altında bulunabilir. V2 SKU 'SU performans iyileştirmeleri sunar ve otomatik ölçeklendirme, bölge artıklığı ve statik VIP 'ler için destek gibi kritik yeni özellikler için destek ekler. Standart ve WAF SKU 'SU altındaki mevcut özellikler, yeni v2 SKU 'sunda, [karşılaştırma](#differences-from-v1-sku) bölümünde listelenen birkaç özel durum ile devam eder.

Yeni v2 SKU 'SU aşağıdaki geliştirmeleri içerir:

- **Otomatik ölçeklendirme**: otomatik ölçeklendirme SKU 'su altındaki Application Gateway veya WAF dağıtımları, değişen trafik yükü desenlerine göre ölçeklenebilir veya değiştirilebilir. Otomatik ölçeklendirme ayrıca sağlama sırasında dağıtım boyutu veya örnek sayısı seçme gereksinimini de ortadan kaldırır. Bu SKU, doğru esneklik sunar. Standard_v2 ve WAF_v2 SKU 'sunda Application Gateway, her ikisi de sabit kapasitede (otomatik ölçeklendirme devre dışı) ve otomatik ölçeklendirme etkinleştirilmiş modda işleyebilir. Sabit kapasite modu, tutarlı ve öngörülebilir iş yüklerine sahip senaryolar için faydalıdır. Otomatik ölçeklendirme modu, uygulama trafiğinden varyansı gösteren uygulamalarda faydalıdır.
- **Bölge artıklığı**: bir Application Gateway veya WAF dağıtımı birden fazla kullanılabilirlik alanları yayılabilir, her Traffic Manager bölgede ayrı Application Gateway örneklerinin sağlanması gereksinimini ortadan kaldırabilir. Application Gateway örneklerinin dağıtıldığı tek bir bölge veya birden çok bölge seçebilirsiniz. Bu, bölge hatasına daha dayanıklı hale gelir. Uygulamalar için arka uç havuzu, kullanılabilirlik alanları arasında benzer şekilde dağıtılabilir.

  Bölge yedekliliği yalnızca Azure bölgelerinin kullanılabildiği durumlarda kullanılabilir. Diğer bölgelerde, diğer tüm özellikler desteklenir. Daha fazla bilgi için bkz. [Azure 'Da bölgeler ve kullanılabilirlik alanları](../availability-zones/az-overview.md)
- **STATIK VIP**: Application Gateway v2 SKU 'SU statik VIP türünü özel olarak destekler. Bu, bir yeniden başlatma işleminden sonra bile, uygulama ağ geçidi ile ilişkili VIP 'nin dağıtımın yaşam döngüsü açısından değişmemesini sağlar.  V1 'de statik bir VIP yok, bu nedenle uygulama ağ geçidi aracılığıyla uygulama hizmetleri 'ne etki alanı adı yönlendirmesi için IP adresi yerine Application Gateway URL 'sini kullanmanız gerekir.
- **Üstbilgi yeniden yazma**: Application Gateway, v2 SKU 'SU ile http isteği ve yanıt üst bilgilerini eklemenize, kaldırmanıza veya güncelleştirmenize olanak tanır. Daha fazla bilgi için bkz. [http üst bilgilerini Application Gateway yeniden yazma](rewrite-http-headers.md)
- **Key Vault tümleştirme**: Application Gateway v2, https etkin dinleyicilerine eklenen sunucu sertifikaları için Key Vault tümleştirme desteği sağlar. Daha fazla bilgi için bkz. [Key Vault sertifikalarıyla TLS sonlandırma](key-vault-certs.md).
- **Azure Kubernetes hizmeti giriş denetleyicisi**: Application Gateway v2 giriş denetleyicisi, Azure Application Gateway aks kümesi olarak bilinen bir Azure Kubernetes hizmeti (aks) için giriş olarak kullanılmasına izin verir. Daha fazla bilgi için bkz. [Application Gateway giriş denetleyicisi nedir?](ingress-controller-overview.md).
- **Performans iyileştirmeleri**: v2 SKU 'Su, standart/WAF SKU 'su ile karşılaştırıldığında daha ıyı bir TLS yük boşaltma performansı sunar.
- **Daha hızlı dağıtım ve güncelleştirme zamanı** V2 SKU 'SU, standart/WAF SKU 'suna kıyasla daha hızlı dağıtım ve güncelleştirme süresi sağlar. Bu, WAF yapılandırma değişikliklerini de içerir.

![Otomatik ölçeklendirme bölgesi diyagramı.](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Desteklenen bölgeler

Standard_v2 ve WAF_v2 SKU 'SU şu bölgelerde kullanılabilir: Orta Kuzey ABD, Orta Güney ABD, Batı ABD, Batı ABD 2, Doğu ABD, Doğu ABD 2, Orta ABD, Kuzey Avrupa, Batı Avrupa, Güneydoğu Asya, Fransa Orta, UK Batı, Japonya Doğu, Japonya Batı, Avustralya Doğu, Avustralya Güneydoğu, Brezilya Güney, Kanada Orta, Kanada Doğu, Doğu Asya, Kore orta, Kore Güney , UK Güney, Orta Hindistan, Batı Hindistan Güney Hindistan.

## <a name="pricing"></a>Fiyatlandırma

V2 SKU 'SU ile, fiyatlandırma modeli tüketim ile çalıştırılır ve artık örnek sayılarına veya boyutlara eklenmez. V2 SKU fiyatlandırması iki bileşene sahiptir:

- **Sabit fiyat** -bu, bir Standard_v2 veya WAF_v2 ağ geçidi sağlamak için saatlik (veya kısmi saat) fiyatıdır. Lütfen 0 ek minimum örnek, her zaman sabit fiyata dahil edilen hizmetin yüksek kullanılabilirliğe sahip olduğundan emin olun.
- **Kapasite birim fiyatı** -bu, sabit maliyete ek olarak ücretlendirilen tüketim tabanlı bir maliyettir. Kapasite Birimi ücreti de saatlik veya kısmi olarak hesaplanır. Kapasite birimi işlem biriminin, kalıcı bağlantıların ve aktarım hızının üç boyutu vardır. İşlem birimi, tüketilen işlemci kapasitesinin bir ölçüsüdür. İşlem birimini etkileyen faktörler, TLS bağlantısı/sn, URL Yeniden yazma hesaplamaları ve WAF kural işlemedir. Kalıcı bağlantı, belirli bir fatura aralığındaki uygulama ağ geçidine kurulan TCP bağlantılarının bir ölçümüdür. Aktarım hızı, belirli bir fatura aralığında sistem tarafından işlenen ortalama megabit/sn 'dir.  Faturalandırma, ayrılmış örnek sayısının üzerinde her şey için bir kapasite birimi düzeyinde yapılır.

Her kapasite birimi, en fazla: 1 işlem birimi, 2500 kalıcı bağlantı ve 2,22 Mbps aktarım hızı ' ten oluşur.

Daha fazla bilgi edinmek için bkz. [fiyatlandırmayı anlama](understanding-pricing.md).

## <a name="scaling-application-gateway-and-waf-v2"></a>Application Gateway ve WAF v2 ölçeklendiriliyor

Application Gateway ve WAF, iki modda ölçeklenebilen şekilde yapılandırılabilir:

- **Otomatik ölçeklendirme** -otomatik ölçeklendirme etkinken, Application Gateway ve WAF v2 SKU 'larının uygulama trafiği gereksinimlerine göre ölçeği artırma veya azaltma. Bu mod, uygulamanız için daha iyi esneklik sunar ve uygulama ağ geçidi boyutunu veya örnek sayısını tahmin etme gereksinimini ortadan kaldırır. Bu mod ayrıca, ağ geçidinin beklenen maksimum trafik yükü için en yüksek sağlanan kapasiteye çalışmasına gerek duymadan maliyeti kaydetmenizi sağlar. En az ve isteğe bağlı olarak en büyük örnek sayısını belirtmeniz gerekir. En düşük kapasite, Application Gateway ve WAF v2 'nin, trafik yokluğunda bile belirtilen minimum örnek sayısının altına düşmemesini sağlar. Her örnek, yaklaşık olarak 10 ek ayrılmış Kapasite birimi ile eşdeğerdir. Sıfır, ayrılmış kapasite olmadığını belirtir ve doğası halinde tamamen otomatik ölçeklendirin. İsteğe bağlı olarak, Application Gateway belirtilen örnek sayısını aşmamasını sağlayan en büyük örnek sayısını belirtebilirsiniz. Yalnızca ağ geçidi tarafından hizmet verilen trafik miktarı üzerinden faturalandırılırsınız. Örnek sayıları 0 ile 125 arasında değişebilir. En fazla örnek sayısı için varsayılan değer, belirtilmemişse 20 ' dir.
- **El ile** -ağ geçidinin otomatik ölçeklendirme Meyeceği el ile modunu seçebilirsiniz. Bu modda, Application Gateway veya WAF 'nin işleyebileceğinden daha fazla trafik varsa, bu durum trafik kaybına neden olabilir. El ile moduyla, örnek sayısını belirtmek zorunludur. Örnek sayısı 1 ile 125 arasında örnek farklılık gösterebilir.

## <a name="autoscaling-and-high-availability"></a>Otomatik ölçeklendirme ve yüksek kullanılabilirlik

Azure uygulama ağ geçitleri her zaman yüksek oranda kullanılabilir bir biçimde dağıtılır. Hizmet, yapılandırılmış olarak oluşturulan (otomatik ölçeklendirme devre dışıysa) veya uygulama yükü (otomatik ölçeklendirme etkinse) için gerekli olan birden çok örnek üzerinden yapılır. Kullanıcının perspektifinden, tek tek örneklere ilişkin görünürlüğe sahip olmasa da, yalnızca Application Gateway hizmetine bir bütün olarak sahip olmanız gerektiğini unutmayın. Belirli bir örnekte sorun varsa ve çalışır şekilde karşılaşırsanız Azure Application Gateway, saydam olarak yeni bir örnek oluşturur.

Otomatik ölçeklendirmeyi sıfır minimum örneklerle yapılandırsanız bile hizmetin, her zaman sabit fiyata dahil olan yüksek oranda kullanılabilir olacağını lütfen unutmayın.

Ancak, yeni bir örnek oluşturmak biraz zaman alabilir (altı veya yedi dakika içinde). Bu nedenle, bu kapalı kalma süresiyle kullanmak istemiyorsanız, kullanılabilirlik alanı desteğiyle en az 2 örnek sayısını yapılandırabilirsiniz. Bu şekilde, normal koşullarda Azure Application Gateway içinde en az iki örneğe sahip olursunuz. bu nedenle, bunlardan biri başka bir örnek oluşturulurken trafikle ilgili bir sorun varsa diğer bir sorunla karşılaşırsanız. Bir Azure Application Gateway örneğinin, genellikle en düşük örnek otomatik ölçeklendirme ayarınızı 2 ' den yüksek bir değere yapılandırmak isteyebileceğiniz trafik miktarına bağlı olarak, 10 Kapasite birimi etrafında destekleyebileceğini unutmayın.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>V1 SKU 'SU ve v2 SKU 'SU arasındaki Özellik Karşılaştırması

Aşağıdaki tabloda, her SKU ile kullanılabilen özellikler karşılaştırılır.

| Özellik                                           | V1 SKU 'SU   | v2 SKU 'SU   |
| ------------------------------------------------- | -------- | -------- |
| Otomatik ölçeklendirme                                       |          | &#x2713; |
| Bölge yedekliliği                                   |          | &#x2713; |
| Statik VIP                                        |          | &#x2713; |
| Azure Kubernetes hizmeti (AKS) giriş denetleyicisi |          | &#x2713; |
| Azure Anahtar Kasası tümleştirme                       |          | &#x2713; |
| HTTP (S) üstbilgilerini yeniden yaz                           |          | &#x2713; |
| URL tabanlı yönlendirme                                 | &#x2713; | &#x2713; |
| Birden çok site barındırma                             | &#x2713; | &#x2713; |
| Trafiği yeniden yönlendirme                               | &#x2713; | &#x2713; |
| Web Uygulaması Güvenlik Duvarı (WAF)                    | &#x2713; | &#x2713; |
| WAF özel kuralları                                  |          | &#x2713; |
| Aktarım Katmanı Güvenliği (TLS)/Güvenli Yuva Katmanı (SSL) sonlandırma            | &#x2713; | &#x2713; |
| Uçtan uca TLS şifrelemesi                         | &#x2713; | &#x2713; |
| Oturum benzeşimi                                  | &#x2713; | &#x2713; |
| Özel hata sayfaları                                | &#x2713; | &#x2713; |
| WebSocket desteği                                 | &#x2713; | &#x2713; |
| HTTP/2 desteği                                    | &#x2713; | &#x2713; |
| Bağlantı boşaltma                               | &#x2713; | &#x2713; |

> [!NOTE]
> Otomatik ölçeklendirme v2 SKU 'SU artık arka uç havuzundaki tüm kaynakların sistem durumunu otomatik olarak izlemek için [varsayılan sistem durumu araştırmalarını](application-gateway-probe-overview.md#default-health-probe) destekler ve sağlıksız olarak kabul edilen arka uç üyelerini vurgulayacaktır. Varsayılan sistem durumu araştırması, özel araştırma yapılandırması olmayan arka uçlara otomatik olarak yapılandırılır. Daha fazla bilgi edinmek için bkz. [Application Gateway 'de sistem durumu araştırmaları](application-gateway-probe-overview.md).

## <a name="differences-from-v1-sku"></a>V1 SKU 'SU farklılıkları

Bu bölümde v1 SKU 'sundan farklı v2 SKU 'sunun özellikleri ve sınırlamaları açıklanmaktadır.

|Fark|Ayrıntılar|
|--|--|
|Kimlik doğrulama sertifikası|Desteklenmez.<br>Daha fazla bilgi için bkz. [Application Gateway ile uçtan uca TLS 'ye genel bakış](ssl-overview.md#end-to-end-tls-with-the-v2-sku).|
|Aynı alt ağda Standard_v2 ve standart Application Gateway karıştırma|Desteklenmez|
|Application Gateway alt ağda User-Defined Route (UDR)|Desteklenir (belirli senaryolar). Önizleme aşamasında.<br> Desteklenen senaryolar hakkında daha fazla bilgi için bkz. [Application Gateway yapılandırmasına genel bakış](configuration-infrastructure.md#supported-user-defined-routes).|
|Gelen bağlantı noktası aralığı için NSG| Standard_v2 SKU için-65200-65535<br>Standart SKU için-65503-65534 arası.<br>Daha fazla bilgi için bkz. [SSS](application-gateway-faq.yml#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Azure tanılama 'da performans günlükleri|Desteklenmez.<br>Azure ölçümleri kullanılmalıdır.|
|Faturalandırma|Faturalama 1 Temmuz 2019 tarihinde başlayacak şekilde zamanlandı.|
|FIPS modu|Bunlar şu anda desteklenmiyor.|
|Yalnızca ıLB modu|Bu şu anda desteklenmiyor. Ortak ve ıLB modu birlikte desteklenir.|
|NET izleyici tümleştirmesi|Desteklenmez.|
|Azure Güvenlik Merkezi tümleştirmesi|Henüz kullanılamıyor.

## <a name="migrate-from-v1-to-v2"></a>v1'den v2'ye geçiş

PowerShell Galerisi 'nde, v1 Application Gateway/WAF 'den v2 otomatik ölçeklendirme SKU 'suna geçiş yapmanıza yardımcı olması için bir Azure PowerShell betiği kullanılabilir. Bu betik, yapılandırmayı v1 ağ Geçidinizden kopyalamanıza yardımcı olur. Trafik geçişi hala sizin sorumluluğunuzdadır. Daha fazla bilgi için bkz. [v1 'den v2 'ye Azure Application Gateway geçirme](migrate-v1-v2.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç: Azure Application Gateway ile doğrudan web trafiği-Azure portal](quick-create-portal.md)
- [Azure PowerShell kullanarak, ayrılmış bir sanal IP adresine sahip bir otomatik ölçeklendirme, bölge yedekli uygulama ağ geçidi oluşturma](tutorial-autoscale-ps.md)
- [Application Gateway](overview.md)hakkında daha fazla bilgi edinin.
- [Azure Güvenlik Duvarı](../firewall/overview.md)hakkında daha fazla bilgi edinin.
