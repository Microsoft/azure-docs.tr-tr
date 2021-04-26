---
title: 'Azure ön kapısı: kural kümesi'
description: Bu makalede, Azure ön kapısının Standart/Premium kuralları kümesi özelliğine genel bakış sunulmaktadır.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: e3e5333b339101676582cec03dbb960148d59b56
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067563"
---
# <a name="what-is-a-rule-set-for-azure-front-door-standardpremium-preview"></a>Azure ön kapısının Standart/Premium (Önizleme) için ayarlanan kural nedir?

> [!Note]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Burada](../front-door-overview.md)görüntüleyin.

Kural kümesi, kuralların birleşimini tek bir küme içine gruplandıran özelleştirilmiş bir kural altyapısıdır. Bir kural kümesini birden çok rotasıyla ilişkilendirebilirsiniz. Kural kümesi, isteklerin kenarda nasıl işleneceğini ve Azure ön kapısının bu istekleri nasıl işlediğini özelleştirmenize olanak sağlar.

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="common-supported-scenarios"></a>Yaygın olarak desteklenen senaryolar

* Çıkış noktaları arası kaynak paylaşımı (CORS) senaryoları için HTTP Strict-Transport-Security (HSTS), X-XSS-koruma, Content-Security-Policy, X-Frame-Options ve Access-Control-Allow-Origin üstbilgileri gibi tarayıcı tabanlı güvenlik açıklarını önlemeye yönelik güvenlik üst bilgileri uygulama. Güvenlik tabanlı öznitelikler, tanımlama bilgileriyle de tanımlanabilir.

* İstekleri istemci cihaz türüne bağlı olarak uygulamanızın mobil veya masaüstü sürümlerine yönlendirin.

* Yeniden yönlendirme yeteneklerini kullanarak, yeni ana bilgisayar adları, yollar, sorgu dizeleri veya protokollerine yönlendirmek için, istemciye 301, 302, 307 ve 308 geri yönlendirilmesi döndürün.

* Yönlendirmenize ait önbelleğe alma yapılandırmasını gelen isteklere göre dinamik olarak değiştirin.

* İstek URL 'SI yolunu yeniden yazın ve isteği yapılandırılan kaynak grubunuzda uygun kaynağa iletir.

* Hassas bilgileri gizlemek veya üst bilgiler aracılığıyla önemli bilgileri yakalamak için istek/yanıt üst bilgisi ekleyin, değiştirin veya kaldırın.

* İstek/yanıt üstbilgilerini veya URL yeniden yazma yollarını/Sorgu dizelerini dinamik olarak değiştirmek için sunucu değişkenlerini destekler, örneğin, yeni bir sayfa yüklenirken veya bir form gönderildiğinde. Sunucu değişkeni şu anda yalnızca **[kural kümesi eylemlerinde](concept-rule-set-actions.md)** destekleniyor.

## <a name="architecture"></a>Mimari

Kural kümesi, istekleri kenarda işler. Azure ön kapı Standart/Premium uç noktanıza bir istek ulaştığında, önce WAF yürütülür ve ardından rotada yapılandırılan ayarlar gelir. Bu ayarlar, rotayla ilişkili kural kümesini içerir. Kural kümeleri, rotada üstten alta doğru işlenir. Aynı kural kümesi içindeki kurallar için de geçerlidir. Her kuraldaki tüm eylemlerin yürütülmesi için, bir kural içindeki tüm eşleşme koşullarının karşılanması gerekir. Bir istek, kural kümesi yapılandırmanızda herhangi bir koşula uymazsa, yalnızca rotadaki yapılandırmalar yürütülür.

**Kalan kuralları değerlendirmeyi durdur** Işaretliyse, yol ile ilişkili kalan tüm kural kümeleri yürütülmez.  

### <a name="example"></a>Örnek

Aşağıdaki diyagramda, önce WAF ilkeleri yürütülür. Bir kural kümesi, yanıt üst bilgisini ekleyecek şekilde yapılandırılmış. Ardından, eşleşme koşulu karşılanıyorsa üst bilgi önbellek denetiminin maksimum yaşını değiştirir.

:::image type="content" source="../media/concept-rule-set/front-door-rule-set-architecture-1.png" alt-text="Kural kümesi mimarisini gösteren diyagram." lightbox="../media/concept-rule-set/front-door-rule-set-architecture-1-expanded.png":::

## <a name="terminology"></a>Terminoloji

Azure ön kapı kural kümesi ile, her biri bir kural kümesinden oluşan kural kümesi yapılandırması ' nın bir bileşimini oluşturabilirsiniz. Aşağıda, kural kümesini yapılandırırken kullanabileceğiniz bazı yararlı terimlerin satırları aşağıda verilmiştir.

Daha fazla kota sınırı için [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../../azure-resource-manager/management/azure-subscription-service-limits.md)' a bakın.

* *Kural kümesi*: bir veya birden çok [rotasıyla](concept-route.md)ilişkili bir kurallar kümesi.

* *Kural kümesi kuralı*: en fazla 10 eşleşme koşulu ve 5 eylemden oluşan bir kural. Kurallar bir kural kümesi için yereldir ve kural kümeleri arasında kullanılmak üzere verilemez. Kullanıcılar aynı kuralı birden çok kural kümesinde oluşturabilir.

* *Koşulu Eşleştir*: gelen isteklerinizi ayrıştırmak için kullanılabilecek çok sayıda eşleşme koşulu vardır. Bir kural, en fazla 10 eşleşme koşulu içerebilir. Eşleşme koşulları bir **ve** işleciyle değerlendirilir. *Normal ifade koşullarda desteklenir*. Eşleşen koşulların tam bir listesi, [kural kümesi eşleşme koşullarında](concept-rule-set-match-conditions.md)bulunabilir.

* *Eylem*: Eylemler, eşleştirmeleri eşleşen koşullara göre gelen istekleri nasıl işleyenleri belirler. Önbelleğe alma davranışlarını değiştirebilir, istek üst bilgilerini/yanıt üstbilgilerini değiştirebilir, URL yeniden yazma ve URL yeniden yönlendirme işlemleri yapabilirsiniz. *Sunucu değişkenleri, eylemde desteklenir*. Bir kural, en fazla 10 eşleşme koşulu içerebilir. Eylemlerin tam listesi, [kural kümesi eylemleri](concept-rule-set-actions.md)bulunabilir.

## <a name="arm-template-support"></a>ARM şablon desteği

Kural kümeleri, Azure Resource Manager şablonlar kullanılarak yapılandırılabilir. [Bkz. örnek bir şablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set). Yöntemi, [eşleştirme koşulları](concept-rule-set-match-conditions.md) ve [eylemleri](concept-rule-set-actions.md)için belge örneklerine dahil edilen JSON veya bıcep kod parçacıklarını kullanarak özelleştirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Ön kapı Standart/Premium oluşturmayı](create-front-door-portal.md)öğrenin.
* İlk [kural kümesini](how-to-configure-rule-set.md)nasıl yapılandıracağınızı öğrenin.
