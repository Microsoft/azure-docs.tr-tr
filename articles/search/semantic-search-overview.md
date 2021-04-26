---
title: Anlamsal arama
titleSuffix: Azure Cognitive Search
description: Bilişsel Arama, arama sonuçlarının daha sezgisel olmasını sağlamak için Bing 'den derin öğrenme anlam arama modellerini nasıl kullandığını öğrenin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2021
ms.custom: references_regions
ms.openlocfilehash: 04300b8d148bb22bf585aa81481c475b347ad462
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222053"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Azure Bilişsel Arama 'de anlamsal arama

> [!IMPORTANT]
> Anlamsal arama genel önizlemede, önizleme REST API ve Portal aracılığıyla kullanılabilir. Önizleme özellikleri, olduğu gibi, [ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)altında sunulur ve genel kullanıma sunulmakta olan uygulamanın garantisi yoktur. Bu özellikler faturalandırılabilir. Daha fazla bilgi için bkz. [kullanılabilirlik ve fiyatlandırma](semantic-search-overview.md#availability-and-pricing).

Anlamsal arama, arama sonuçlarına semantik ilgi ve dil anlama ekleyen, sorguyla ilgili yetenekler koleksiyonudur. *Anlam derecelendirmesi* , bir sorgu verildiğinde daha mantıklı olan eşleşmeleri yükseltir, şartlar arasında bağlam ve relatedsliği arar. Dil anlama, içeriğinizin, eşleşen belgeyi özetleyen veya bir soruyu yanıtlayan, daha sonra daha üretken bir arama deneyimi için bir arama sonuçları sayfasında oluşturulabilen *açıklamalı alt yazılar* ve *yanıtlar* bulur.

Son derece önceden eğitilen modeller özetleme ve derecelendirme için kullanılır. Kullanıcıların aramanın beklediği hızlı performansı korumak için, semantik özetleme ve derecelendirme, [varsayılan benzerlik Puanlama algoritmasına](index-similarity-and-scoring.md#similarity-ranking-algorithms)göre puanlanmış olarak yalnızca ilk 50 sonuca uygulanır. Bu sonuçları belge Corpus olarak kullanarak, anlam derecelendirmesi bu sonuçları eşleşmenin anlam gücüne göre yeniden alır.

Temel alınan teknoloji Bing ve Microsoft Research ' dir ve Bilişsel Arama altyapısına eklenti özelliği olarak tümleştirilir. Araştırmayı yedekleyen araştırma ve AI yatırımları hakkında daha fazla bilgi için bkz. [Bing 'den AI 'Nin Azure bilişsel arama (Microsoft Research blogu) nasıl güçlemesinden](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

Aşağıdaki videoda, özelliklerine genel bir bakış sunulmaktadır.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="components-and-workflow"></a>Bileşenler ve iş akışı

Anlamsal arama duyarlık artırır ve aşağıdaki özellikleri ekleme ile geri çağırır:

| Özellik | Açıklama |
|---------|-------------|
| [Yazım denetimi](speller-how-to-add.md) | Sorgu terimleri arama altyapısına ulaşmadan önce yazım hatalarını düzeltir. |
| [Anlam derecelendirmesi](semantic-ranking.md) | Yeni bir ilgi Puanını hesaplamak için bağlam veya anlam anlamı kullanır. |
| [Anlamsal açıklamalı alt yazılar ve vurgular](semantic-how-to-query-request.md) | İçeriği en iyi şekilde özetleyen bir belgedeki cümleler ve tümcecikler, kolay tarama için anahtar yollarından önemli noktalar elde etimiyle. Sonucu özetleyen açıklamalı alt yazılar, tek tek içerik alanları sonuçlar sayfası için çok yoğun olduğunda faydalıdır. Vurgulanan metin, kullanıcıların bir eşleşmenin ilgili olarak neden kabul edileceğini hızla belirleyebilmeleri için en ilgili terimleri ve tümceleri yükseltir. |
| [Anlam yanıtları](semantic-answers.md) | Bir anlam sorgusundan isteğe bağlı ve ek bir alt yapı döndürüldü. Soru gibi görünen bir sorguya doğrudan yanıt sağlar. |

### <a name="order-of-operations"></a>İşlem sırası

Anlamsal arama bileşenleri, mevcut sorgu yürütme ardışık düzenini her iki yönde de genişletir. Yazım düzeltmesini etkinleştirirseniz, yazım [denetleyicisi](speller-how-to-add.md) , arama altyapısına ulaşmadan önce sorgu OnSet 'teki yazım hatalarını düzeltir.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Sorgu yürütmesindeki anlam bileşenleri" border="true":::

Sorgu yürütme her zamanki gibi devam eder, ters kullanılan dizinler üzerinde terim ayrıştırma, çözümleme ve tarama. Motor, dosyaları belirteç eşleştirmeyi kullanarak alır ve [varsayılan benzerlik Puanlama algoritmasını](index-similarity-and-scoring.md#similarity-ranking-algorithms)kullanarak sonuçları puan alır. Puanlar, sorgudaki sorgu terimleri ve eşleşen terimler arasındaki dilsel benzerlik derecesine göre hesaplanır. Bunları tanımladıysanız, bu aşamada Puanlama profilleri de uygulanır. Sonuçlar daha sonra anlamsal arama alt sistemine geçirilir.

Hazırlık adımında, ilk sonuç kümesinden döndürülen yapı belgesi, her belgeyi özetleyen bir busages bulmak için tümce ve paragraf düzeyinde çözümlenir. Anahtar sözcük arama 'nın aksine, bu adım, içeriği değerlendirmek için makine okuma ve kavrama özelliklerini kullanır. Bu içerik işleme aşamasında, bir anlamsal sorgu [açıklamalı alt yazılar](semantic-how-to-query-request.md) ve [yanıtlar](semantic-answers.md)döndürür. Anlamsal arama, bir sonucu en iyi şekilde özetleyen anahtar parolaları ayıklamak ve vurgulamak için dil gösterimini kullanır. Arama sorgusu bir soru ise ve yanıtlar istenirse, yanıt, arama sorgusunda gösterildiği gibi, soruyu en iyi şekilde cevapladığı bir metin metni de içerecektir. 

Her iki resim yazısı ve yanıt için, varolan metin formülde kullanılır. Anlam modelleri, kullanılabilir içerikten Yeni cümleler veya ifadeler oluşturmaz veya yeni ekibinizle 'e ulaşmak için mantık uygulamaz. Kısacası, sistem hiçbir daha önceden mevcut olmayan içeriği döndürmez.

Sonuçlar daha sonra sorgu koşullarının [kavramsal benzerliğine](semantic-ranking.md) göre yeniden puanlanır.

Sorgularda semantik özellikleri kullanmak için, [arama isteğinde](semantic-how-to-query-request.md)küçük değişiklikler yapmanız gerekir, ancak ek yapılandırma veya yeniden dizin oluşturma gerekli değildir.

## <a name="availability-and-pricing"></a>Kullanılabilirlik ve fiyatlandırma

Bir Standart katmanda (S1, S2, S3) oluşturulan ve bu bölgelerden birinde bulunan (S1, S2, S3) (Orta Kuzey ABD, Batı ABD, Batı ABD 2, Doğu ABD 2, Kuzey Avrupa, Batı Avrupa), kayıt [kaydı](https://aka.ms/SemanticSearchPreviewSignup)aracılığıyla anlamsal yetenekler kullanılabilir. 

Yazım denetimi aynı bölgelerde mevcuttur, ancak katman kısıtlamalarına sahip değildir. Katman ve bölge ölçütlerine uyan mevcut bir hizmetiniz varsa, yalnızca kaydolma gereklidir.

2 Mart 'ta önizleme başlangıcı ile geç Nisan arasında, yazım düzeltme ve anlam derecelendirmesi ücretsiz olarak sunulur. Nisan ayında, bu işlevselliği çalıştırmanın daha sonra faturalandırılabilir bir olay olacaktır. Beklenen maliyet 250.000 sorguları için yaklaşık USD $500/ay olur. [Bilişsel arama fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/search/) belgelenen ayrıntılı maliyet bilgilerini ve [tahmin etme ve maliyetleri yönetme](search-sku-manage-costs.md)' de bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Yeni bir sorgu türü, anlamsal aramanın ilgi derecesini ve yanıt yapılarını mümkün bir şekilde sunar.

Başlamak için [bir anlam sorgusu oluşturun](semantic-how-to-query-request.md) . Veya ilgili bilgiler için aşağıdaki makalelere göz atın.

+ [Sorgu koşullarına yazım denetimi Ekle](speller-how-to-add.md)
+ [Anlam yanıtı döndürme](semantic-answers.md)
+ [Anlam derecelendirmesi](semantic-ranking.md)
+ [Anlam aramasına giriş (blog gönderisi)](https://techcommunity.microsoft.com/t5/azure-ai/introducing-semantic-search-bringing-more-meaningful-results-to/ba-p/2175636)
+ [Anlamsal özellikleri kullanarak anlamlı Öngörüler bulma (AI video göster)](https://channel9.msdn.com/Shows/AI-Show/Find-meaningful-insights-using-semantic-capabilities-in-Azure-Cognitive-Search)