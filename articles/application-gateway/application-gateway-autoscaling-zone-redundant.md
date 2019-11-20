---
title: Otomatik ölçeklendirme ve Alanlar arası yedekli Application Gateway v2
description: Bu makalede, otomatik ölçeklendirme ve bölgesel olarak yedekli özellikler içeren Azure Uygulama Standard_v2 ve WAF_v2 SKU açıklanır.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/09/2019
ms.author: victorh
ms.openlocfilehash: 8f3a732d5d6128ff38f81f715113e87710b11c47
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847265"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Otomatik ölçeklendirme ve Alanlar arası yedekli Application Gateway v2 

Application Gateway ve Web uygulaması güvenlik duvarı (WAF), bir Standard_v2 ve WAF_v2 SKU 'SU altında da mevcuttur. V2 SKU 'SU performans iyileştirmeleri sunar ve otomatik ölçeklendirme, bölge artıklığı ve statik VIP 'ler için destek gibi kritik yeni özellikler için destek ekler. Standart ve WAF SKU 'SU altındaki mevcut özellikler, yeni v2 SKU 'sunda, [karşılaştırma](#differences-with-v1-sku) bölümünde listelenen birkaç özel durum ile devam eder.

Yeni v2 SKU 'SU aşağıdaki geliştirmeleri içerir:

- **Otomatik ölçeklendirme**: otomatik ölçeklendirme SKU 'su altındaki Application Gateway veya WAF dağıtımları, değişen trafik yükü desenlerine göre ölçeği değiştirebilir veya azaltabilirsiniz. Otomatik ölçeklendirme ayrıca sağlama sırasında dağıtım boyutu veya örnek sayısı seçme gereksinimini de ortadan kaldırır. Bu SKU, doğru esneklik sunar. Standard_v2 ve WAF_v2 SKU 'sunda Application Gateway, her ikisi de sabit kapasitede (otomatik ölçeklendirme devre dışı) ve otomatik ölçeklendirme etkinleştirilmiş modda işleyebilir. Sabit kapasite modu, tutarlı ve öngörülebilir iş yüklerine sahip senaryolar için faydalıdır. Otomatik ölçeklendirme modu, uygulama trafiğinden varyansı gösteren uygulamalarda faydalıdır.
- **Bölge artıklığı**: bir Application Gateway veya WAF dağıtımı birden fazla kullanılabilirlik alanları yayılabilir, her Traffic Manager bölgede ayrı Application Gateway örneklerinin sağlanması gereksinimini ortadan kaldırabilir. Application Gateway örneklerinin dağıtıldığı tek bir bölge veya birden çok bölge seçebilirsiniz. Bu, bölge hatasına daha dayanıklı hale gelir. Uygulamalar için arka uç havuzu, kullanılabilirlik alanları arasında benzer şekilde dağıtılabilir.

  Bölge yedekliliği yalnızca Azure bölgelerinin kullanılabildiği durumlarda kullanılabilir. Diğer bölgelerde, diğer tüm özellikler desteklenir. Daha fazla bilgi için bkz. [Azure 'da kullanılabilirlik alanları nedir?](../availability-zones/az-overview.md#services-support-by-region)
- **STATIK VIP**: Application Gateway v2 SKU 'SU statik VIP türünü özel olarak destekler. Bu, bir yeniden başlatma işleminden sonra bile, uygulama ağ geçidi ile ilişkili VIP 'nin dağıtımın yaşam döngüsü açısından değişmemesini sağlar.  V1 'de statik bir VIP yok, bu nedenle uygulama ağ geçidi aracılığıyla uygulama hizmetleri 'ne etki alanı adı yönlendirmesi için IP adresi yerine Application Gateway URL 'sini kullanmanız gerekir.
- **Üstbilgi yeniden yazma**: Application Gateway, v2 SKU 'SU ile http isteği ve yanıt üst bilgilerini eklemenize, kaldırmanıza veya güncelleştirmenize olanak tanır. Daha fazla bilgi için bkz. [http üst bilgilerini Application Gateway yeniden yazma](rewrite-http-headers.md)
- **Key Vault Tümleştirme (Önizleme)** : Application Gateway v2, https etkin dinleyicilerine eklenen sunucu sertifikaları için Key Vault (genel önizlemede) tümleştirmeyi destekler. Daha fazla bilgi için bkz. [Key Vault sertifikalarla SSL sonlandırması](key-vault-certs.md).
- **Azure Kubernetes hizmeti (Önizleme)** : Application Gateway v2 giriş denetleyicisi, Azure Application Gateway aks kümesi olarak bilinen bir Azure Kubernetes hizmeti (aks) için giriş olarak kullanılmasına izin verir. Daha fazla bilgi için [Belgeler sayfasına](https://azure.github.io/application-gateway-kubernetes-ingress/)bakın.
- **Performans iyileştirmeleri**: v2 SKU 'su standart/WAF SKU 'su ile karşılaştırıldığında daha iyi SSL yük boşaltma performansı sunar.
- **Daha hızlı dağıtım ve güncelleştirme zamanı** V2 SKU 'SU, standart/WAF SKU 'suna kıyasla daha hızlı dağıtım ve güncelleştirme süresi sağlar. Bu, WAF yapılandırma değişikliklerini de içerir.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Desteklenen bölgeler

Standard_v2 ve WAF_v2 SKU 'SU şu bölgelerde kullanılabilir: Orta Kuzey ABD, Orta Güney ABD, Batı ABD, Batı ABD 2, Doğu ABD, Doğu ABD 2, Orta ABD, Kuzey Avrupa, Batı Avrupa, Güneydoğu Asya, Fransa Orta, UK Batı, Japonya Doğu, Japonya Batı, Avustralya Doğu , Avustralya Güneydoğu, Brezilya Güney, Kanada Orta, Kanada Doğu, Doğu Asya, Kore Orta, Kore Güney, Güney Hindistan, UK Güney, Orta Hindistan, Batı Hindistan, Güney Hindistan.

## <a name="pricing"></a>Fiyatlandırma

V2 SKU 'SU ile, fiyatlandırma modeli tüketim ile çalıştırılır ve artık örnek sayılarına veya boyutlara eklenmez. V2 SKU fiyatlandırması iki bileşene sahiptir:

- **Sabit fiyat** -bu, bir Standard_v2 veya WAF_v2 ağ geçidi sağlamak için saatlik (veya kısmi saat) fiyatıdır. Lütfen 0 ek minimum örnek, her zaman sabit fiyata dahil edilen hizmetin yüksek kullanılabilirliğe sahip olduğundan emin olun.
- **Kapasite birim fiyatı** -bu, sabit maliyete ek olarak ücretlendirilen tüketim tabanlı bir maliyettir. Kapasite birimi ücretlendirmesi ayrıca saatlik olarak veya kısmen saatlik olarak hesaplanır. Kapasite biriminde üç boyut bulunur: işlem birimi, kalıcı bağlantılar ve aktarım hızı. İşlem birimi, kullanılan işlemci kapasitesini ölçer. İşlem birimini etkileyen faktörler, TLS bağlantısı/sn, URL yeniden yazma hesaplamaları ve WAF kural işleme. Kalıcı bağlantı, belirli bir fatura aralığındaki uygulama ağ geçidine kurulan TCP bağlantılarının bir ölçümüdür. Aktarım hızı, belirli bir fatura aralığında sistem tarafından işlenen ortalama megabit/sn 'dir.  Faturalandırma, ayrılmış örnek sayısının üzerinde her şey için bir kapasite birimi düzeyinde yapılır.

Her kapasite birimi en çok: 1 işlem birimi veya 2500 kalıcı bağlantı ya da 2,22 Mbps aktarım hızı ' ten oluşur.

İşlem birimi Kılavuzu:

- **Standard_v2** -her işlem BIRIMI, RSA 2048 BIT anahtar TLS sertifikası ile saniyede yaklaşık 50 bağlantı kapasitesine sahiptir.
- **WAF_v2** -her işlem birimi,% 70-30 oranında 70 trafik karması için saniyede yaklaşık 10 eşzamanlı isteği destekleyebilir. WAF performansı şu anda yanıt boyutundan etkilenmez.

> [!NOTE]
> Her örnek şu anda yaklaşık 10 kapasite birimini destekleyebilir.
> Bir işlem biriminin işleyebileceği isteklerin sayısı, TLS sertifikası anahtar boyutu, anahtar değişim algoritması, üst bilgi yeniden oluşturma gibi çeşitli ölçütlere ve WAF gelen istek boyutu durumuna bağlıdır. İşlem birimi başına istek hızını belirlemede uygulama testleri gerçekleştirmenizi öneririz. Hem Kapasite birimi hem de işlem birimi, faturalandırma başlamadan önce bir ölçüm olarak kullanılabilir hale getirilir.

Aşağıdaki tabloda örnek fiyatlar gösterilmektedir ve yalnızca çizim amaçlıdır.

**ABD Doğu fiyatlandırma**:

|              SKU adı                             | Sabit fiyat ($/hr)  | Kapasite birim fiyatı ($/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0,20             | 0,0080                          |
| WAF_v2                                            |    0,36             | 0,0144                          |

Daha fazla fiyatlandırma bilgisi için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/application-gateway/). Faturalandırma, 1 Temmuz 2019 ' de başlayacak şekilde zamanlandı.

**Örnek 1**

Bir Application Gateway Standard_v2, el ile ölçekleme modunda, beş örnek sabit kapasiteye sahip otomatik ölçeklendirme olmadan sağlanır.

Sabit fiyat = (saat) * $0,20 = $148,8 <br>
Kapasite birimleri = 744 (saat) * her örnek için 10 Kapasite birimi * 5 örnek * Kapasite birimi başına $0,008 saat = $297,6

Toplam Fiyat = $148,8 + $297,6 = $446,4

**Örnek 2**

Bir Application Gateway standard_v2, sıfır minimum örnek ve bu süre boyunca 25 yeni SSL bağlantısı/sn, ortalama 8,88 Mbps veri aktarımı alır. Bağlantıların kısa süreli olduğu varsayıldığında, fiyatınızın şöyle olması gerekir:

Sabit fiyat = (saat) * $0,20 = $148,8

Kapasite birim fiyatı = ünle (saatler) * en fazla (25/50 bağlantı için işlem birimi/sn, 8.88/2.22 Kapasite birimi üretilen iş birimi) * $0,008 = 744 * 4 * 0,008 = $23,81

Toplam Fiyat = $148.8 + 23.81 = $172,61

Gördüğünüz gibi, tüm örnek için değil yalnızca dört Kapasite birimi için faturalandırılırsınız. 

> [!NOTE]
> Max işlevi, bir değer çiftindeki en büyük değeri döndürür.


**Örnek 3**

Bir Application Gateway standard_v2, en az beş örnek içeren bir ayda sağlanır. Hiçbir trafik ve bağlantının kısa süreli olduğunu varsayarsak, fiyatınızın şöyle olması gerekir:

Sabit fiyat = (saat) * $0,20 = $148,8

Kapasite birim fiyatı = ünle (saatler) * en fazla (0/50 bağlantı için işlem birimi/sn, aktarım için 0/2.22 Kapasite birimi) * $0,008 = 744 * 50 * 0,008 = $297,60

Toplam Fiyat = $148.80 + 297.60 = $446,4

Bu durumda, hiçbir trafik olmasa bile beş örnek tümüyle faturalandırılırsınız.

**Örnek 4**

Bir Application Gateway standard_v2 en az beş örnek içeren bir ayda sağlanır, ancak bu kez ortalama 125 Mbps veri aktarımı ve saniye başına 25 SSL bağlantısı vardır. Hiçbir trafik ve bağlantının kısa süreli olduğunu varsayarsak, fiyatınızın şöyle olması gerekir:

Sabit fiyat = (saat) * $0,20 = $148,8

Kapasite birim fiyatı = ünle (saatler) * en fazla (25/50 bağlantı için işlem birimi/sn, üretilen iş için 125/2.22 Kapasite birimi) * $0,008 = 744 * 57 * 0,008 = $339,26

Toplam Fiyat = $148.80 + 339.26 = $488,06

Bu durumda, tam beş örnek ve yedi Kapasite birimi (örneğin 7/10) için faturalandırılırsınız.  

**Örnek 5**

Bir ay için Application Gateway WAF_v2 sağlandı. Bu süre boyunca 25 yeni SSL bağlantısı/sn, ortalama 8,88 Mbps veri aktarımı alır ve saniye başına 80 istek olur. Bağlantıların kısa süreli olduğu varsayıldığında ve uygulama için işlem birimi hesaplaması işlem birimi başına 10 RPS 'yi destekliyorsa, fiyatlarınız şöyle olacaktır:

Sabit fiyat = (saat) * $0,36 = $267,84

Kapasite birim fiyatı = ünl (saatler) * en fazla (işlem birimi en fazla (bağlantı için 25/50/sn, 80/10 WAF RPS), 8.88/2.22 Kapasite birimi üretilen iş birimi) * $0,0144 = 744 * 8 * 0,0144 = $85,71

Toplam Fiyat = $267,84 + $85,71 = $353,55

> [!NOTE]
> Max işlevi, bir değer çiftindeki en büyük değeri döndürür.

## <a name="scaling-application-gateway-and-waf-v2"></a>Application Gateway ve WAF v2 ölçeklendiriliyor

Application Gateway ve WAF, iki modda ölçeklenebilen şekilde yapılandırılabilir:

- **Otomatik ölçeklendirme** -otomatik ölçeklendirme etkinken, Application Gateway ve WAF v2 SKU 'larının uygulama trafiği gereksinimlerine göre ölçeği artırma veya azaltma. Bu mod, uygulamanız için daha iyi esneklik sunar ve uygulama ağ geçidi boyutunu veya örnek sayısını tahmin etme gereksinimini ortadan kaldırır. Bu mod ayrıca, ağ geçidinin beklenen maksimum trafik yükü için en yüksek sağlanan kapasiteye çalışmasına gerek duymadan maliyeti kaydetmenizi sağlar. En az ve isteğe bağlı olarak en büyük örnek sayısını belirtmeniz gerekir. En düşük kapasite, Application Gateway ve WAF v2 'nin, trafik yokluğunda bile belirtilen minimum örnek sayısının altına düşmemesini sağlar. Her örnek 10 ek ayrılmış Kapasite birimi olarak sayılır. Sıfır, ayrılmış kapasite olmadığını belirtir ve doğası halinde tamamen otomatik ölçeklendirin. Lütfen sıfır ek minimum örnek, her zaman sabit fiyata dahil edilen hizmetin yüksek kullanılabilirliğe sahip olduğundan emin olun. İsteğe bağlı olarak, Application Gateway belirtilen örnek sayısını aşmamasını sağlayan en büyük örnek sayısını belirtebilirsiniz. Ağ Geçidi tarafından hizmet verilen trafik miktarı için faturalandırılmaya devam edeceksiniz. Örnek sayıları 0 ile 125 arasında değişebilir. En fazla örnek sayısı için varsayılan değer, belirtilmemişse 20 ' dir.
- **El ile** -ağ geçidinin otomatik ölçeklendirme Meyeceği el ile modunu seçebilirsiniz. Bu modda, Application Gateway veya WAF 'nin işleyebileceğinden daha fazla trafik varsa, bu durum trafik kaybına neden olabilir. El ile moduyla, örnek sayısını belirtmek zorunludur. Örnek sayısı 1 ile 125 arasında örnek farklılık gösterebilir.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>V1 SKU 'SU ve v2 SKU 'SU arasındaki Özellik Karşılaştırması

Aşağıdaki tabloda, her SKU ile kullanılabilen özellikler karşılaştırılır.

|                                                   | V1 SKU 'SU   | v2 SKU 'SU   |
| ------------------------------------------------- | -------- | -------- |
| Otomatik ölçeklendirme                                       |          | &#x2713; |
| Bölge artıklığı                                   |          | &#x2713; |
| Statik VIP                                        |          | &#x2713; |
| Azure Kubernetes hizmeti (AKS) giriş denetleyicisi |          | &#x2713; |
| Azure Anahtar Kasası tümleştirme                       |          | &#x2713; |
| HTTP (S) üstbilgilerini yeniden yaz                           |          | &#x2713; |
| URL tabanlı yönlendirme                                 | &#x2713; | &#x2713; |
| Birden çok site barındırma                             | &#x2713; | &#x2713; |
| Trafiği yeniden yönlendirme                               | &#x2713; | &#x2713; |
| Web Uygulaması Güvenlik Duvarı (WAF)                    | &#x2713; | &#x2713; |
| WAF özel kuralları                                  |          | &#x2713; |
| Güvenli Yuva Katmanı (SSL) sonlandırma            | &#x2713; | &#x2713; |
| Uçtan uca SSL şifrelemesi                         | &#x2713; | &#x2713; |
| Oturum benzeşimi                                  | &#x2713; | &#x2713; |
| Özel hata sayfaları                                | &#x2713; | &#x2713; |
| WebSocket desteği                                 | &#x2713; | &#x2713; |
| HTTP/2 desteği                                    | &#x2713; | &#x2713; |
| Bağlantı boşaltma                               | &#x2713; | &#x2713; |

> [!NOTE]
> Otomatik ölçeklendirme v2 SKU 'SU artık arka uç havuzundaki tüm kaynakların sistem durumunu otomatik olarak izlemek için [varsayılan sistem durumu araştırmalarını](application-gateway-probe-overview.md#default-health-probe) destekler ve sağlıksız olarak kabul edilen arka uç üyelerini vurgulayacaktır. Varsayılan sistem durumu araştırması, özel araştırma yapılandırması olmayan arka uçlara otomatik olarak yapılandırılır. Daha fazla bilgi edinmek için bkz. [Application Gateway 'de sistem durumu araştırmaları](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>V1 SKU 'SU farkları

|Fark|Ayrıntılar|
|--|--|
|Kimlik doğrulama sertifikası|Desteklenmiyor.<br>Daha fazla bilgi için bkz. [Application Gateway ile uçtan uca SSL 'ye genel bakış](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Aynı alt ağda Standard_v2 ve standart Application Gateway karıştırma|Desteklenmiyor|
|Application Gateway alt ağında Kullanıcı tanımlı yol (UDR)|Desteklenmiyor|
|Gelen bağlantı noktası aralığı için NSG| Standard_v2 SKU için-65200-65535<br>Standart SKU için-65503-65534 arası.<br>Daha fazla bilgi için bkz. [SSS](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Azure tanılama 'da performans günlükleri|Desteklenmiyor.<br>Azure ölçümleri kullanılmalıdır.|
|Faturalandırma|Faturalama 1 Temmuz 2019 tarihinde başlayacak şekilde zamanlandı.|
|FIPS modu|Bunlar şu anda desteklenmiyor.|
|Yalnızca ıLB modu|Bu şu anda desteklenmiyor. Ortak ve ıLB modu birlikte desteklenir.|
|Netizleyici tümleştirmesi|Desteklenmiyor.|
|Azure Güvenlik Merkezi tümleştirmesi|Henüz kullanılamıyor.

## <a name="migrate-from-v1-to-v2"></a>v1'den v2'ye geçiş

PowerShell Galerisi 'nde, v1 Application Gateway/WAF 'den v2 otomatik ölçeklendirme SKU 'suna geçiş yapmanıza yardımcı olması için bir Azure PowerShell betiği kullanılabilir. Bu betik, yapılandırmayı v1 ağ Geçidinizden kopyalamanıza yardımcı olur. Trafik geçişi hala sizin sorumluluğunuzdadır. Daha fazla bilgi için bkz. [v1 'den v2 'ye Azure Application Gateway geçirme](migrate-v1-v2.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı başlangıç: Azure Application Gateway ile doğrudan web trafiği-Azure portal](quick-create-portal.md)
- [Azure PowerShell kullanarak, ayrılmış bir sanal IP adresine sahip bir otomatik ölçeklendirme, bölge yedekli uygulama ağ geçidi oluşturma](tutorial-autoscale-ps.md)
- [Application Gateway](overview.md)hakkında daha fazla bilgi edinin.
- [Azure Güvenlik Duvarı](../firewall/overview.md)hakkında daha fazla bilgi edinin.
