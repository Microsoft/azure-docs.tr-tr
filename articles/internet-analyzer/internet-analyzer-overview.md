---
title: Azure Internet Çözümleyicisi | Microsoft Docs
description: Azure Internet çözümleyici hakkında bilgi edinin
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: overview
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 3d9d1240e31d40a3d9c75d56314acc1721d90b99
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501949"
---
# <a name="what-is-internet-analyzer-preview"></a>Internet Çözümleyicisi nedir? (Önizleme)

Internet Çözümleyicisi, ağ altyapısı değişikliklerinin müşterilerinizin performansını nasıl etkilediğini test etmek için bir istemci tarafı ölçüm platformudur. Şirket içinden Azure 'a geçiş yaparken veya yeni bir Azure hizmetini değerlendirirken, Internet çözümleyici, kullanıcılarınızın verilerini ve Microsoft 'un zengin analizine, Azure ile ağ mimarinizi daha iyi anlamak ve en iyi hale getirmenize olanak tanır; geçiremezsiniz.

Internet Çözümleyicisi, son kullanıcılarınızın seçtiğiniz ağ hedefleri kümesine yönelik gecikme süresini ölçmek için Web uygulamanızda Embedded küçük bir JavaScript istemcisi kullanır, _uç noktaları_çağırıyoruz. Internet Çözümleyicisi, altyapı ve müşteri gereksinimleriniz geliştikçe çeşitli senaryoları değerlendirmenizi sağlayan birden çok yan yana test ayarlamanıza olanak sağlar. Internet Çözümleyicisi, son kullanıcılarınız için güvenilir performans kararları vermek için hem kolaylık hem de esneklik sağlayan özel ve önceden yapılandırılmış uç noktalar sağlar. 


> [!IMPORTANT]
> Bu genel önizleme bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılmamalıdır. Belirli özellikler desteklenmiyor olabilir, kısıtlı yeteneklere sahip olabilir veya tüm Azure konumlarında mevcut olmayabilir. Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="quick--customizable-tests"></a>Hızlı & özelleştirilebilir testler

Internet çözümleyici, bulut geçişi, yeni veya ek bir Azure bölgesine dağıtma veya Azure [ön kapısı](https://azure.microsoft.com/services/frontdoor/) ve Microsoft Azure CDN gibi yeni uygulama ve içerik teslim platformlarını test etmeye yönelik performansla ilgili soruları ele alınmaktadır [ ](https://azure.microsoft.com/services/cdn/). 

Internet Çözümleyicisi 'nde oluşturduğunuz her bir test, uç nokta A ve uç nokta B olmak üzere iki uç noktadan oluşur. uç nokta B 'nin performansı, uç nokta A 'ya göre çözümlenir. 

Kendi özel uç noktanızı yapılandırabilir ya da önceden yapılandırılmış çeşitli Azure uç noktalarından seçim yapabilirsiniz. Özel uç noktalar şirket içi iş yüklerini, diğer bulut sağlayıcılarındaki örneklerinizi veya özel Azure yapılandırmalarınızı değerlendirmek için kullanılmalıdır. Testler iki özel uç noktasından oluşabilir; Ancak, en az bir özel uç noktanın Azure 'da barındırılması gerekir. Önceden yapılandırılmış Azure uç noktaları, Azure ön kapısı, Azure Traffic Manager ve Azure CDN gibi popüler Azure ağ platformlarının performansını değerlendirmek için hızlı ve kolay bir yoldur. 

Önizleme sırasında, aşağıdaki önceden yapılandırılmış uç noktalar kullanılabilir: 

* **Azure bölgeleri**
    * Güney Brezilya
    * Orta Hindistan
    * Orta ABD
    * Doğu Asya
    * Doğu ABD
    * Japonya Batı
    * Kuzey Avrupa
    * Güney Afrika Kuzey
    * Güneydoğu Asya 
    * BAE Kuzey
    * Birleşik Krallık Batı  
    * Batı Avrupa
    * Batı ABD 
    * Batı ABD 2
* **Birden çok Azure bölge kombinasyonu** 
    * Doğu ABD, Brezilya Güney 
    * Doğu ABD, Doğu Asya 
    * Batı Avrupa, Brezilya Güney
    * Batı Avrupa, Güneydoğu Asya
    * Batı Avrupa, BAE Kuzey
    * Batı ABD, Doğu ABD 
    * Batı ABD, Batı Avrupa
    * Batı ABD, BAE Kuzey
    * Batı Avrupa, BAE Kuzey, Güneydoğu Asya
    * Batı ABD, Batı Avrupa, Doğu Asya
    * Batı ABD, Kuzey Avrupa, Güneydoğu Asya, BAE Kuzey Güney Afrika Kuzey 
* **Azure + Azure ön kapısı** -yukarıda listelenen tek veya birden çok Azure bölge birleşimine dağıtıldı
* **Microsoft 'Tan Azure + Azure CDN** , yukarıda listelenen tek bir Azure bölge birleşimine dağıtılır
* **Azure + azure Traffic Manager** , yukarıda listelenen birden çok Azure bölge birleşimine dağıtıldı

## <a name="suggested-test-scenarios"></a>Önerilen test senaryoları 

Müşterilerinizin en iyi performans kararlarını almanıza yardımcı olmak için, Internet çözümleyici, son kullanıcılarınızın özel popülasyonunun iki uç noktasını değerlendirmenize olanak tanır. 

Internet Çözümleyicisi çok sayıda soruyu yanıtlayabilirken, en yaygın olarak karşılaşılan bazı konular şunlardır: 
* Buluta geçiş performansı etkisi nedir? 
    * *Önerilen test: özel (Şirket içi altyapınızda) ve Azure (önceden yapılandırılmış herhangi bir uç nokta) karşılaştırması*
* Verileri bir veri merkezinde Edge 'e ve veri merkezine yerleştirmenin bir değeri nedir? 
    *  *Önerilen test: Azure ile Azure ön kapısı, Azure ve Microsoft 'tan Azure CDN karşılaştırması*
* Azure ön kapısının performans avantajı nedir?
    *  *Önerilen test: özel/Azure/CDN ile Azure ön kapısı karşılaştırması*
* Microsoft 'tan Azure CDN performans avantajı nedir? 
    *  *Önerilen test: özel/Azure/AFD ile Microsoft 'tan Azure CDN karşılaştırması*
* Microsoft Stack 'ten nasıl Azure CDN? 
    *  *Önerilen test: özel (diğer CDN uç noktası) ve Microsoft 'tan Azure CDN karşılaştırması*
* Her bölgede Son Kullanıcı popülasyonunuz için en iyi bulut nedir? 
    *  *Önerilen test: özel (diğer bulut hizmeti) ve Azure (önceden yapılandırılmış herhangi bir uç nokta) karşılaştırması*

## <a name="how-it-works"></a>Nasıl çalışır

Internet çözümleyici 'yi kullanmak için Microsoft Azure portal bir Internet çözümleyici kaynağı ayarlayın ve küçük JavaScript istemcisini uygulamanıza kurun. İstemci, HTTPS üzerinden tek piksellik bir görüntü indirerek son kullanıcılarınızın gecikme süresini seçtiğiniz uç noktalara ölçer. Gecikme ölçümleri toplandıktan sonra istemci, ölçüm verilerini Internet Analyzer 'a gönderir.

Bir Kullanıcı Web uygulamasını ziyaret ettiğinde, JavaScript istemcisi yapılandırılan tüm testlerde ölçmek için iki uç nokta seçer. Her uç nokta için istemci _soğuk_ ve _sıcak_ ölçüm gerçekleştirir. _Soğuk_ ölçüm, Kullanıcı ve uç nokta ARASıNDAKI, DNS ÇÖZÜMLEMESI, TCP bağlantısı el SıKıŞMASı ve SSL/TLS anlaşması gibi saf ağ gecikmesi yanında ek gecikme süresi doğurur. _Normal_ ölçüm, _soğuk_ ölçüm tamamlandıktan hemen sonra ve modern tarayıcıların kalıcı TCP bağlantısı yönetiminden yararlanarak uçtan uca gecikme süresinin doğru bir şekilde ölçülmesine neden olur. Kullanıcının tarayıcısı tarafından desteklenerek, doğru ölçüm zamanlaması için W3C kaynak zamanlama API 'SI kullanılır. Şu anda, analiz için yalnızca ısınma gecikmesi ölçümleri kullanılır.

![architecture](./media/ia-overview/architecture.png)


## <a name="scorecards"></a>Lere 

Bir test başladıktan sonra telemetri verileri, Internet çözümleyici kaynağında, karne sekmesinde görünür. Bu veriler her zaman toplanır. Gördüğünüz verilerin hangi görünümünü değiştirmek için aşağıdaki filtreleri kullanın: 

* **Test:** Sonuçlarını görüntülemek istediğiniz testi seçin. Test verileri, çoğu durumda, 24 saat içinde çözümlemeyi tamamlayacak kadar veri olduğunda görüntülenir. 
* **Zaman aralığı & bitiş tarihi:** Internet Çözümleyicisi her gün üç karne oluşturuyor: her bir karne, farklı bir toplama süresi dönemi (önceki 24 saat (gün), önceki yedi gün önce (hafta) ve 30 gün önce (ay) yansıtır. Görmek istediğiniz zaman dilimini seçmek için "bitiş tarihi" filtresini kullanın. 
* **Ülke:** Bir ülkede bulunan son kullanıcılara özgü verileri görüntülemek için bu filtreyi kullanın. Genel filtre tüm coğrafi cihazlarda verileri gösterir.  

Karneler hakkında daha fazla bilgi, [karneyi yorumlama](internet-analyzer-scorecard.md) sayfasında bulunabilir. 


## <a name="next-steps"></a>Sonraki adımlar

* [Ilk Internet çözümleyici kaynağını oluşturmayı](internet-analyzer-create-test-portal.md)öğrenin.
* [Internet ÇÖZÜMLEYICISI SSS](internet-analyzer-faq.md)makalesini okuyun. 
