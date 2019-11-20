---
title: Internet Çözümleyicisi SSS | Microsoft Docs
description: Azure Internet çözümleyici hakkında SSS.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 61cb6003549530f381b9cbbed74c1cb62c91431c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512905"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Azure Internet Analyzer hakkında SSS (Önizleme)

Bu, Azure Internet Analyzer için SSS-ek sorularınız varsa, [geri bildirim forumuna](https://aka.ms/internetAnalyzerFeedbackForum) gidin ve sorunuzu gönderin. Bir soru sık sorulduğunda, bu makaleye hızla ve kolayca bulabilmeleri için ekleyeceğiz.

## <a name="how-do-i-participate-in-the-preview"></a>Önizleme programına nasıl katılabilirim?

Önizleme sürümü belirli müşterilerin kullanımına sunuldu. Önizlemeye katılmakla ilgileniyorsanız lütfen aşağıdakileri yapın:

1. [Azure portalında](https://ms.portal.azure.com) oturum açın.
2. **Abonelikler** sayfasına gidin.
3. Internet Analyzer 'ı ile kullanmayı planladığınız Azure aboneliğine tıklayın.
4. Abonelik için **kaynak sağlayıcıları** ayarlarına gidin.
5. **Microsoft. Network** ' i arayın ve **Kaydet** (veya **yeniden kaydet**) düğmesine tıklayın.
![erişim isteği](./media/ia-faq/request-preview-access.png)

6. E-posta adresinizi ve erişim isteğini yapmak için kullanılan Azure abonelik KIMLIĞINI bize ekleyerek [onay isteyin](https://aka.ms/internetAnalyzerContact) .
7. İsteğiniz onaylandıktan sonra bir e-posta onayı alırsınız ve yeni izin verilen Azure aboneliğinden Internet çözümleyici kaynaklarını oluşturabilir/güncelleştirebilir/değiştirebilirsiniz.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>Bir testi çalıştırmak için istemciyi gömmem gerekir mi?

Evet, kullanıcılarınıza özgü ölçümleri toplamak için Internet çözümleyici istemcisinin uygulamanıza yüklenmesi gerekir. [İstemcisini yüklemeyi öğrenin.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Önizleme aşamasına katıldığım için bana fatura kesilir mi?
Hayır, Azure Internet Analyzer önizleme sürümünde ücretsiz olarak kullanılabilir. Önizleme sırasında hizmet düzeyi anlaşması yok.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Internet Çözümleyicisi hangi senaryolarda ele almak için tasarlanmıştır?

Internet Çözümleyicisi, Kullanıcı popülasyona göre ağ performans öngörüleri sağlamak üzere tasarlanmıştır. Kullanıcılarınız için en iyi performans kararlarını elde etmenize yardımcı olmak üzere, Internet çözümleyici, farklı Kullanıcı popülasyonu kullanarak iki Internet uç noktası performansını karşılaştırır. Internet Çözümleyicisi çok sayıda soruyu yanıtlayabilirken, en yaygın olarak karşılaşılan bazı konular şunlardır:

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

## <a name="which-tests-can-i-run-in-preview"></a>Önizlemede hangi testleri çalıştırabilirim?

Internet Çözümleyicisi 'nde oluşturduğunuz her test, uç nokta A ve uç nokta B olmak üzere iki uç noktadan oluşur. Aşağıdaki kombinasyonlardan herhangi biri, testler olarak çalıştırılabilir:  
* Önceden yapılandırılmış iki uç nokta,
* Bir özel ve önceden yapılandırılmış bir uç nokta veya
* İki [Özel uç](internet-analyzer-custom-endpoint.md) nokta (bir özel uç nokta Azure 'da bulunmalıdır).

Önizleme sırasında aşağıdaki önceden yapılandırılmış uç noktalar kullanılabilir:
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

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Internet Çözümleyicisi, Azure tarafından sunulan diğer izleme hizmetlerinden farklı midir?

Internet Çözümleyicisi, son kullanıcılarınızın performansını anlamanıza yardımcı olur ve performanslarını geliştirmek için kararlar almanıza yardımcı olur. Diğer Azure izleme araçları, Azure hizmetlerinize yönelik Öngörüler sağlarken, Internet Çözümleyicisi kullanıcılarınız için uçtan uca Internet performansını ölçmeye odaklanır.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinmek için bkz. [Internet Analyzer 'A genel bakış](internet-analyzer-overview.md).
