---
title: Ölçüm Danışmanı sık sorulan sorular
titleSuffix: Azure Cognitive Services
description: Ölçüm Danışmanı hizmeti hakkında sık sorulan sorular.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: mbullwin
ms.openlocfilehash: 0c4c296cb1454ed89eef102732533589b1c8ca0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420968"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Ölçüm Danışmanı sık sorulan sorular

### <a name="what-is-the-cost-of-my-instance"></a>Örneðimin maliyeti nedir?

Şu anda, önizleme sırasında örneğinizi kullanma maliyeti yoktur.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Kaynağı neden oluşturamıyorum? "Fiyatlandırma katmanı" kullanılamıyor ve "Bu abonelik için zaten 1 S0 oluşturdunuz" diyor musunuz?

:::image type="content" source="media/pricing.png" alt-text="Bir F0 kaynağı zaten varsa ileti":::

Genel Önizleme sırasında, bir abonelik kapsamında her bölge için yalnızca bir ölçüm Danışmanı örneği oluşturulabilir.

Aynı bölgede aynı abonelik kullanılarak oluşturulmuş bir örneğiniz zaten varsa, yeni bir örnek oluşturmak için farklı bir bölge veya farklı bir abonelik deneyebilirsiniz. Ayrıca, yeni bir örnek oluşturmak için var olan bir örneği silebilirsiniz.

Mevcut örneği zaten sildiyseniz ancak hatayı görmeye devam ederseniz, yeni bir örnek oluşturmadan önce lütfen kaynak silme sonrasında yaklaşık 20 dakika bekleyin.

## <a name="basic-concepts"></a>Temel kavramlar

### <a name="what-is-multi-dimensional-time-series-data"></a>Çok boyutlu zaman serisi verileri nedir?

Sözlükteki [çok boyutlu ölçüm](glossary.md#multi-dimensional-metric)  tanımına bakın.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Ölçüm Danışmanı 'nın anomali algılamayı başlatması için ne kadar veri gerekiyor?

En azından, bir veri noktası anomali algılamayı tetikleyebilir. Bununla birlikte en iyi doğruluk getirmez. Hizmet, veri akışı oluşturma sırasında "Fill-Gap" kuralı olarak belirttiğiniz değeri kullanarak önceki veri noktalarının bir penceresini varsayacaktır.

Üzerinde algılamayı yapmak istediğiniz zaman damgasından önce bazı veriler olmasını öneririz.
Verilerinizin ayrıntı düzeyi temelinde, önerilen veri miktarı aşağıda belirtildiği gibi değişir.

| Ayrıntı düzeyi | Algılama için önerilen veri miktarı |
| ----------- | ------------------------------------- |
| 5 dakikadan az | 4 gün veri |
| 5 dakika ila 1 gün | 28 gün veri |
| 1 günden fazla, 31 güne kadar | 4 yıllık veri |
| 31 günden fazla | 48 yıllık veri |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Ölçüm Danışmanı neden geçmiş verilerden bozukluklar algılamıyor?

Ölçüm Danışmanı canlı akış verilerini algılamak için tasarlanmıştır. Hizmetin geri bakması ve anomali algılama çalıştırması için geçmiş verilerin maksimum uzunluğuna yönelik bir sınırlama vardır. Bu, yalnızca belirli bir en erken zaman damgasına ait veri noktalarının anomali algılama sonuçlarına sahip olacağını gösterir. Bu en erken zaman damgası, verilerinizin ayrıntı düzeyine bağlıdır.

Verilerinizin ayrıntı düzeyi temelinde, anomali algılama sonuçlarına sahip olacak geçmiş verilerin uzunlukları aşağıda verilmiştir.

| Ayrıntı düzeyi | Anomali algılama için maksimum geçmiş verileri uzunluğu |
| ----------- | ------------------------------------- |
| 5 dakikadan az | Ekleme saati-13 saat |
| 1 saatten az 5 dakika | Ekleme zamanı-4 gün  |
| 1 günden daha az 1 saat | Ekleme saati-14 gün  |
| 1 gün | Ekleme saati-28 gün  |
| 1 günden fazla, 31 günden daha az | Ekleme saati-2 yıl  |
| 31 günden fazla | Ekleme saati-24 yıl   |

### <a name="more-concepts-and-technical-terms"></a>Daha fazla kavram ve teknik koşullar

Daha fazla bilgi için bkz. [Sözlük](glossary.md) .

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>Nasıl yaparım? verileri almak için geçerli bir sorgu yazmak mı istiyorsunuz?  

Ölçüm Danışmanı 'nın verilerinizi almak için tek bir zaman damgasında verilerinizin boyutlarını döndüren bir sorgu oluşturmanız gerekir. Ölçüm Danışmanı, her zaman damgasından verileri almak için bu sorguyu birden çok kez çalıştıracaktır. 

Sorgunun belirli bir zaman damgasında her bir boyut birleşimi için en çok bir kayıt döndürmesi gerektiğini unutmayın. Döndürülen tüm kayıtlar aynı zaman damgasına sahip olmalıdır. Sorgu tarafından döndürülen bir yinelenen kayıt olmamalıdır.

Örneğin, günlük ölçüm için aşağıdaki sorguyu oluşturduğunuzu varsayalım: 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

Zaman seriniz için doğru ayrıntı düzeyini kullandığınızdan emin olun. Saatlik ölçüm için şunu kullanabilirsiniz: 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

Bu sorguların yalnızca tek bir zaman damgasında veri döndürdüğüne ve ölçüm Danışmanı tarafından alınacak tüm boyut birleşimlerini içerdiğini unutmayın. 

:::image type="content" source="media/query-result.png" alt-text="Bir zaman damgasına sahip sorgu sonucu" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Nasıl yaparım?, anormal bir şekilde & ileri 'leri algılar mi?

Önceden tanımlanmış sabit eşikler varsa, [anomali algılama yapılandırmalarında](how-tos/configure-metrics.md#anomaly-detection-methods)"Sabit eşik" i el ile ayarlayabilirsiniz.
Hiçbir eşik yoksa AI tarafından desteklenen "akıllı algılama" kullanabilirsiniz. Ayrıntılar için lütfen [yapılandırmayı algılama](how-tos/configure-metrics.md#tune-the-detecting-configuration) bölümüne bakın.

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Nasıl yaparım?, düzenli (mevsimlik) desenlerle anormal olarak indiksel olarak algılama mi?

"Akıllı algılama" mevsimsel desenler dahil verilerinizin desenini öğreniyor. Daha sonra, düzenli desenlerle aynı şekilde uyumlu olmayan bu veri noktalarını algılar. Ayrıntılar için lütfen [yapılandırmayı algılama](how-tos/configure-metrics.md#tune-the-detecting-configuration) bölümüne bakın.

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Düz satırları anomali olarak Nasıl yaparım? mi?

Verileriniz normalde çok kararsız ve büyük bir ölçüde dalgalandığı ve çok kararlı ve hatta düz bir çizgi haline geldiğinde uyarılmak istiyorsanız, değişiklik çok küçük olduğunda bu tür veri noktalarını algılamak için "değişiklik eşiği" yapılandırılabilir.
Ayrıntılar için lütfen [anomali algılama yapılandırmalarına](how-tos/configure-metrics.md#anomaly-detection-methods) bakın.

### <a name="how-to-set-up-email-settings-and-enable-alerting-by-email"></a>E-posta ayarlarını nasıl ayarlayabilirim ve uyarı e-posta ile nasıl etkinleştirilir?

1.  Abonelik Yöneticisi veya kaynak grubu yönetici ayrıcalıklarına sahip bir kullanıcının, Azure portal oluşturulan ölçüm Danışmanı kaynağına gitilmesi ve **erişim denetimi (IAM)** sekmesini seçmeniz gerekir. 
2.  **Rol atamaları Ekle** ' yi seçin
3.  Bilişsel **Hizmetler ölçüm Danışmanı yöneticisinin** bir rolünü seçin, aşağıdaki görüntüde olduğu gibi hesabınızı seçin.
4.  **Kaydet** düğmesine tıklayın, ardından ölçüm Danışmanı kaynağı Yöneticisi olarak başarıyla eklendiniz. Yukarıdaki tüm eylemlerin abonelik Yöneticisi veya kaynak grubu yöneticisi tarafından gerçekleştirilmesi gerektiğini unutmayın. 

:::image type="content" source="media/access-control.png" alt-text="Rol ataması ekle seçili olan erişim denetimi (ıAM) menü sayfası ve ardından bilişsel hizmetler ölçüm Danışmanı yöneticisinin erişim rolüyle birlikte Seçili kullanıcıya erişim ata ' yı ve ardından Kullanıcı arama ve belirli bir erişim izinleri düzeyi ekleme adımlarını göstermek için seçilen kullanıcı ARABIRIMININ Kaydet düğmesi." lightbox="media/access-control.png":::


5.  Bu işlem, yayma izinlerinin bir dakika kadar sürebilir. Ardından, ölçüm Danışmanı çalışma alanınızı seçin ve sol gezinti panelinde **e-posta ayarı** seçeneğini belirleyin. Gerekli öğeleri, özellikle SMTP ile ilgili bilgileri olarak girin. 
6.  **Kaydet**' i seçin, ardından e-posta yapılandırması ile birlikte hazırsınız demektir. Yeni kancalar oluşturabilir ve neredeyse gerçek zamanlı uyarılar için ölçüm bozuklularına abone olabilirsiniz. 

## <a name="advanced-concepts"></a>Gelişmiş kavramlar

### <a name="how-does-metric-advisor-build-an-incident-tree-for-multi-dimensional-metrics"></a>Ölçüm Danışmanı, çok boyutlu ölçümler için bir olay ağacı nasıl oluşturur?

Ölçüm, boyutlara göre birden çok zaman serisine ayrılabilir. Örneğin, ölçüm `Response latency` takımın sahip olduğu tüm hizmetler için izlenir. `Service`Kategori, ölçümü zenginleştirmek için bir boyut olarak kullanılabilir, bu nedenle,, vb. olarak `Response latency` bölüneceğiz `Service1` `Service2` . Her hizmet birden fazla veri merkezinde farklı makinelere dağıtılarak, ölçüm ve ile daha fazla bölünebilir `Machine` `Data center` .

|Hizmet| Veri Merkezi| Makine  | 
|----|------|----------------   |
| S1 |  DC1 |   M1 |
| S1 |  DC1 |   M2 |
| S1 |  DC2 |   M3 |
| S1 |  DC2 |   M4 |
| S2 |  DC1 |   M1 |
| S2 |  DC1 |   M2 |
| S2 |  DC2 |   M5 |
| S2 |  DC2 |   M6 |
| ...|      |      |

Toplamın üzerinden başlayarak `Response latency` , ve ölçüsünün ayrıntılarına gidebilirsiniz `Service` `Data center` `Machine` . Ancak, hizmet sahiplerinin yolu kullanması daha mantıklı `Service`  ->  `Data center`  ->  `Machine` veya altyapı mühendislerinin yolu kullanması daha mantıklı hale gelir `Data Center`  ->  `Machine`  ->  `Service` . Hepsi, kullanıcılarınızın bireysel iş gereksinimlerine bağlıdır. 

Ölçüm Danışmanı 'nda, kullanıcılar hiyerarşik topolojinin bir düğümünden detaya geçmek veya bu düğümleri toplu yapmak istedikleri yolu belirtebilir. Daha kesin olarak, hiyerarşik topoloji ağaç yapısı yerine yönlendirilmiş bir Çevrimsiz grafiktir. Aşağıdaki gibi tüm olası boyut birleşimlerinin bulunduğu tam bir hiyerarşik topoloji vardır: 

:::image type="content" source="media/dimension-combinations-view.png" alt-text="1 ile 6 arasında değişen karşılık gelen sayılarla S, DC ve d etiketli birden çok boyutlu köşelerden ve kenarlarından oluşan hiyerarşik topoloji diyagramı" lightbox="media/dimension-combinations-view.png":::

Teorik olarak, boyutun `Service` `Ls` farklı değerleri varsa, boyutun farklı değerleri `Data center` vardır `Ldc` ve boyutun `Machine` ayrı değerleri vardır ve `Lm` `(Ls + 1) * (Ldc + 1) * (Lm + 1)` hiyerarşik topolojide boyut birleşimleri olabilir. 

Ancak genellikle tüm boyut birleşimleri geçerli değildir ve bu da karmaşıklığı önemli ölçüde azaltabilir. Şu anda kullanıcılar ölçüyü kendileri topladığımızda, boyut sayısını sınırlayamıyoruz. Ölçüm Danışmanı tarafından sunulan toplama işlevini kullanmanız gerekiyorsa, boyutların sayısı 6 ' dan fazla olmamalıdır. Ancak, bir ölçüm için boyutlara göre genişletilen zaman serisinin sayısını 10.000 ' den az olacak şekilde kısıtlarız.

Tanılama sayfasındaki **olay ağacı** Aracı, tüm topoloji yerine yalnızca bir anomali algılanan düğümleri gösterir. Bu, geçerli soruna odaklanmanız için size yardımcı olur. Ayrıca, ölçüm içindeki tüm anormallikleri göstermez ve bunun yerine katkılara göre en önemli bozukluklar görüntülenir. Bu şekilde, olağan dışı verilerin etkisini, kapsamını ve yayma yolunu hızla bulabiliriz. Odaklanmaları gereken anomali sayısını önemli ölçüde azaltır ve kullanıcıların önemli sorunlarını anlamalarına ve bulmalarına yardımcı olur. 
 
Örneğin, bir anomali oluştuğunda, anomali `Service = S2 | Data Center = DC2 | Machine = M5` 'in sapması, `Service= S2` aynı zamanda anomali 'i tespit eden üst düğümü etkiler, ancak anomali, tüm veri merkezini `DC2` ve üzerinde tüm hizmetleri etkilemez `M5` . Olay ağacı aşağıdaki ekran görüntüsünde olduğu gibi oluşturulur, en üst anomali, üzerine yakalanır `Service = S2` ve kök neden, iki yolda de analiz edilebilir `Service = S2 | Data Center = DC2 | Machine = M5` .

 :::image type="content" source="media/root-cause-paths.png" alt-text="5 ' te, S2 ile etiketlenmiş ortak bir düğüm ile birbirine bağlı iki farklı yol içeren köşeler etiketlendi. En üst anomali, Service = S2 üzerine yakalanır ve kök nedeni, her ikisi de Service = S2 öğesine yol açabilecek iki yol tarafından analiz edilebilir. Veri merkezi = DC2 | Makine = M5" lightbox="media/root-cause-paths.png":::

## <a name="next-steps"></a>Sonraki Adımlar
- [Ölçüm danışmanına genel bakış](overview.md)
- [Web portalını kullanma](quickstarts/web-portal.md)