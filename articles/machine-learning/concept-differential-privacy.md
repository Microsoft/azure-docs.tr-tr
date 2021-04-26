---
title: Makine öğreniminde değişiklik gizliliği (Önizleme)
titleSuffix: Azure Machine Learning
description: Değişiklik gizliliğinizi ve veri gizliliğini koruyan farklı şekilde özel sistemleri nasıl uygulayabileceğinizi öğrenin.
author: luisquintanilla
ms.author: luquinta
ms.date: 01/21/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: responsible-ml
ms.openlocfilehash: de85c8184e05c9d662a2854e6138f3a61bfba8de
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062463"
---
# <a name="what-is-differential-privacy-in-machine-learning-preview"></a>Makine öğreniminde değişiklik gizliliği nedir (Önizleme)

Machine Learning 'de değişiklik gizliliği ve nasıl çalıştığı hakkında bilgi edinin.

Kuruluşun analizler için topladığı ve kullandığı veri miktarı arttıkça, gizlilik ve güvenlikle ilgili endişeler de vardır. Çözümlemeler için veri gerekir. Genellikle, modelleri eğitmek için kullanılan veriler daha doğru olur. Bu çözümlemeler için kişisel bilgiler kullanıldığında, verilerin kullanımı boyunca özel olarak kalması özellikle önemlidir.

## <a name="how-differential-privacy-works"></a>Değişiklik gizliliği nasıl kullanılır?

Değişiklik gizliliği, kişilerin verilerinin güvende ve özel olmasını sağlamaya yardımcı olan bir sistem ve uygulamalar kümesidir.

> [!div class="mx-imgBorder"]
> ![Değişiklik gizliliği makine öğrenimi işlemi](./media/concept-differential-privacy/differential-privacy-machine-learning.jpg)

Geleneksel senaryolarda, ham veriler dosya ve veritabanlarında depolanır. Kullanıcılar verileri çözümlerse genellikle ham verileri kullanır. Bu sorun, bir kişinin gizliliğine infringe olabileceğinden bir kaygıdır. Değişiklik gizliliği, kullanıcıların herhangi bir veri noktasını tanımlayamaması için verilere "gürültü" veya rasgelelik ekleyerek bu sorunla başa geçmeye çalışır. En azından, bu tür bir sistem, plausıble deniability sağlar. Bu nedenle, kişilerin gizliliği verilerin doğruluğu üzerinde sınırlı bir etkiye sahip olacak şekilde korunur.

Farklı şekilde özel sistemlerde, veriler **sorgu** olarak adlandırılan istekler aracılığıyla paylaşılır. Bir Kullanıcı veriler için bir sorgu gönderdiğinde, **Gizlilik mekanizmaları** olarak bilinen işlemler istenen verilere gürültü ekler. Gizlilik mekanizmaları ham veriler yerine *verilerin yaklaşık bir kısmını* döndürür. Bu Gizlilik-koruma sonucu bir **raporda** görüntülenir. Raporlar, hesaplanan gerçek veriler ve verilerin nasıl oluşturulduğuna ilişkin bir açıklama olmak üzere iki bölümden oluşur.

## <a name="differential-privacy-metrics"></a>Değişiklik gizlilik ölçümleri

Değişiklik gizliliği, kullanıcının sonunda hassas verileri açığa çıkarmak için sınırsız sayıda rapor üretmesine karşı korunmaya çalışır. **Epsilon** olarak bilinen bir değer, ne gürültülü ne de özel bir rapor olduğunu ölçer. Epsilon, gürültü veya gizliliğe ters bir ilişkiye sahiptir. Epsilon alt, verilerin daha gürültülü (ve özel) olması gerekir.

Epsilon değerleri negatif değil. Aşağıdaki değerler 1 tam plausible deniability sağlar. 1 ' den fazla şey gerçek verilerin açığa çıkması riski ile gelir. Farklı bir özel sistem uygularken, 0 ile 1 arasında Epsilon değeri olan raporlar oluşturmak istersiniz.

Daha doğrudan Epsilon ile bağıntılı bir değer **Delta**. Delta, raporun tam olarak özel olmadığı olasılığının bir ölçüsüdür. Delta ne kadar yüksekse, Epsilon daha yüksektir. Bu değerler bağıntılı olduğundan, Epsilon daha sık kullanılır.

## <a name="limit-queries-with-a-privacy-budget"></a>Sorguları bir gizlilik bütçesi ile sınırlayın

Birden çok sorguya izin verilen sistemlerde gizliliği sağlamak için, değişiklik gizliliği bir hız sınırı tanımlar. Bu sınır **Gizlilik bütçesi** olarak bilinir. Gizlilik bütçeleri, verilerin birden çok sorgu aracılığıyla yeniden oluşturulmasını önler. Gizlilik bütçelerine, yeniden tanımlama riskini sınırlamak için genellikle 1 ile 3 arasında bir Epsilon miktarı ayrılır. Raporlar oluşturulduğunda, gizlilik bütçeleri tek tek raporların Epsilon değerini ve tüm raporların toplamasını izler. Bir gizlilik bütçesi harcandıktan veya boşaldığında, kullanıcılar artık verilere erişemez. 

## <a name="reliability-of-data"></a>Verilerin güvenilirliği

Gizlilik Koruması hedef olmalıdır, ancak verilerin kullanılabilirliği ve güvenilirliği olduğunda bir zorunluluğunu getirir vardır. Veri Analizi 'nde doğruluk, örnekleme hataları tarafından sunulan belirsizlik ölçüsü olarak düşünülebilir. Bu belirsizlik, belirli sınırlar dahilinde düşecek şekilde eğilimlidir. Bunun yerine bir değişiklik gizliliği perspektifinden **doğruluk** , verilerin güvenilirliğini ölçer ve bu, gizlilik mekanizmalarının sunduğu belirsizlik tarafından etkilendi. Kısacası, daha yüksek bir gürültü veya gizlilik düzeyi, daha düşük bir Epsilon, doğruluk ve güvenilirliğe sahip verileri dönüştürür. 

## <a name="open-source-differential-privacy-libraries"></a>Açık kaynaklı değişiklik gizlilik kitaplıkları

SmartNoise, küresel farklı özel sistemler oluşturmak için farklı bileşenler içeren açık kaynaklı bir projem. SmartNoise, aşağıdaki üst düzey bileşenlerden oluşur:

- SmartNoise Çekirdek Kitaplığı
- Smartgürültü SDK Kitaplığı

### <a name="smartnoise-core"></a>Smartgürültü çekirdeği

Çekirdek kitaplık, farklı bir özel sistem uygulamak için aşağıdaki Gizlilik mekanizmalarını içerir:

|Bileşen  |Açıklama  |
|---------|---------|
|Analiz     | Rastgele hesaplamaların grafik açıklaması. |
|Doğrulayıcı     | Bir çözümlemenin farklı bir şekilde özel olması için gerekli koşulları denetlemeye ve türetmede kullanılabilecek bir araç kümesi içeren bir Rust kitaplığı.          |
|Çalışma Zamanı     | Çözümlemenin yürütüleceği ortam. Başvuru çalışma zamanı Rust dilinde yazılmıştır, ancak çalışma zamanları, veri gereksinimlerinize bağlı olarak SQL ve Spark gibi herhangi bir hesaplama çerçevesi kullanılarak yazılabilir.        |
|Bağlamalar     | Analizler oluşturmak için dil bağlamaları ve yardımcı kitaplıkları. Şu anda akıllı gürültü, Python bağlamaları sunuyor. |

### <a name="smartnoise-sdk"></a>Smartgürültü SDK 'Sı

Sistem kitaplığı, tablolu ve ilişkisel verilerle çalışmaya yönelik aşağıdaki araçları ve hizmetleri sağlar:

|Bileşen  |Açıklama  |
|---------|---------|
|Veri Erişimi     | SQL sorgularını karşılar ve işleyen ve rapor üreten kitaplık. Bu kitaplık Python 'da uygulanır ve aşağıdaki ODBC ve DBAPı veri kaynaklarını destekler:<ul><li>PostgreSQL</li><li>SQL Server</li><li>Spark</li><li>Preston</li><li>Pandas</li></ul>|
|Hizmet     | Paylaşılan veri kaynaklarına karşı istekleri veya sorguları sunan bir REST uç noktası sağlayan yürütme hizmeti. Hizmet, heterojen istekler olarak da bilinen farklı Delta ve Epsilon değerleri içeren isteklerde çalışan değişiklik gizlilik modüllerinin bulunmasına olanak tanımak üzere tasarlanmıştır. Bu başvuru, bağıntılı verilere ilişkin sorgulardan daha fazla etki için uygulama hesaplar. |
|İse     | Gizlilik ihlallerini, doğruluğu ve farkı denetleyen stochastik değerlendirici. Değerlendirici aşağıdaki testleri destekler: <ul><li>Gizlilik testi-bir raporun değişiklik gizliliği koşullarına uygun olup olmadığını belirler.</li><li>Doğruluk testi-raporların güvenilirliğini %95 Güvenirlik düzeyi verilen üst ve alt sınırların içinde olup olmadığını ölçer.</li><li>Yardımcı program testi-bir raporun güven sınırlarının, hala gizliliği en üst düzeye çıkarırken verilere yeterince yakın olup olmadığını belirler.</li><li>Sapma testi-dengesizolmamasını sağlamak için yinelenen sorgular için raporların dağıtımını ölçer</li></ul> |

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning ' de farklı [bir özel sistem oluşturma](how-to-differential-privacy.md) .

Smartgürültü bileşenleri hakkında daha fazla bilgi edinmek için, [Smartnoise Core](https://github.com/opendifferentialprivacy/smartnoise-core), [smartgürültü SDK](https://github.com/opendifferentialprivacy/smartnoise-sdk)ve [smartnoise örnekleri](https://github.com/opendifferentialprivacy/smartnoise-samples)için GitHub depolarına göz atın.