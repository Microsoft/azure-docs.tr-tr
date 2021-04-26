---
title: Performans ipuçları
titleSuffix: Azure Cognitive Search
description: Arama hizmetinde performansı en üst düzeye çıkarmak için ipuçları ve en iyi uygulamalar hakkında bilgi edinin.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 28325a1bbda1b2d4a4bb060ae3e79057275ee42a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582346"
---
# <a name="tips-for-better-performance-in-azure-cognitive-search"></a>Azure Bilişsel Arama daha iyi performans ipuçları

Bu makale, performansı artırmak için genellikle önerilen ipuçları ve en iyi uygulamalardan oluşan bir koleksiyondur. Arama performansını etkileyen faktörlerin en büyük olasılıklı olduğunu bilmek, verimsizlikleri önlemenize ve arama hizmetinizin en iyi şekilde yararlanmasına yardımcı olabilir. Bazı anahtar faktörleri şunlardır:

+ Dizin oluşturma (şema ve boyut)
+ Sorgu türleri
+ Hizmet kapasitesi (katman ve çoğaltma ve bölüm sayısı)

## <a name="index-size-and-schema"></a>Dizin boyutu ve şema

Sorgular daha küçük dizinlerde daha hızlı çalışır. Bu kısmen taramak için daha az alan olan bir işlevdir, ancak sistemin gelecekteki sorgular için içeriği önbelleğe alma işlemi de buna neden olur. İlk sorgu sonrasında, bazı içerikler daha verimli bir şekilde arandığı bellekte kalır. Dizin boyutu zaman içinde büyümeye eğilimlebildiğinden, en iyi yöntem, içerik azaltma fırsatlarını aramak için hem şema hem de belgeler için düzenli aralıklarla dizin oluşturmayı yeniden ziyaret etmeniz olacaktır. Bununla birlikte, Dizin doğru boyutlandırıldığından, tek yapmanız gereken tek ayar, kapasiteyi [ekleyerek](search-capacity-planning.md#adjust-capacity) veya hizmet katmanını yükselterek kapasiteyi artırmaya yönelik olabilir. Bölüm ["Ipucu: Standart S2 katmanına yükseltme"]] (#tip--to-to-to-to-to-to-to-to-------------------

Şema karmaşıklığı Ayrıca dizin oluşturma ve sorgu performansını olumsuz yönde etkileyebilir. Sınırlamalar ve işleme gereksinimlerinde aşırı alan attributıon yapıları. [Karmaşık türler](search-howto-complex-data-types.md) dizin ve sorgu için daha uzun sürer. Sonraki birkaç bölüm her bir koşulu keşfedebilir.

### <a name="tip-be-selective-in-field-attribution"></a>İpucu: alan atışın seçmeli olması

Yöneticiler ve geliştiricinin bir arama dizini oluştururken yaptığı yaygın bir hata, yalnızca gerekli özellikleri seçmeye izin verecek şekilde alanların tüm kullanılabilir özelliklerini seçmektir. Örneğin, bir alanın tam metin aranabilir olması gerekmiyorsa, aranabilir özniteliği ayarlarken bu alanı atlayın.

:::image type="content" source="media/search-performance/perf-selective-field-attributes.png" alt-text="Seçmeli attributıon" border="true":::

Filtreler, modeller ve sıralama desteği, depolama gereksinimlerini dörtlü olabilir. Öneri araçları eklerseniz, depolama gereksinimleri daha da fazla devam edersiniz. Depolamadaki özniteliklerin etkisi hakkında bir çizim için bkz. [öznitelikler ve dizin boyutu](search-what-is-an-index.md#index-size).

Özet, daha fazla öznitelik içerir:

+ Alandaki içeriği işlemek için gereken ek iş nedeniyle dizin oluşturma performansının azalmasına ve sonra bunu ters arama dizinine depoladığınızda ("aranabilir" özniteliğini yalnızca aranabilir içerik içeren alanlarda ayarlayın).

+ Her sorgunun kapsaması gereken daha büyük bir yüzey oluşturur. Aranabilir olarak işaretlenen tüm alanlar tam metin aramasında taranır.

+ Ek depolama nedeniyle işlem maliyetlerini artırır. Filtreleme ve sıralama, orijinal (çözümlenmeyen) dizeleri depolamak için ek alan gerektirir. Filtre uygulanabilir veya sıralanabilir olmayan alanlar üzerinde filtrelenebilir ayarından kaçının.

+ Birçok durumda, atfı 'ın alanı, alanın yeteneklerini sınırlandırır. Örneğin, bir alan çok yönlü, filtrelenebilir ve aranabilir ise, bir alan içinde yalnızca 16 KB 'lık metin saklayabilir, ancak aranabilir bir alan 16 MB 'a kadar metin tutabilir.

> [!NOTE]
> Yalnızca gereksiz atısyon kaçınılmalıdır. Filtre ve modeller genellikle arama deneyimi için gereklidir ve filtrelerin kullanıldığı durumlarda sonuçları sıralamak için genellikle sıralama yapmanız gerekir (kendilerine ait filtreler sıralanmamış bir küme içinde döndürülür).

### <a name="tip-consider-alternatives-to-complex-types"></a>İpucu: karmaşık türlere alternatifleri göz önünde bulundurun

Karmaşık veri türleri, verilerin JSON belgelerinde bulunan üst-alt öğeleri gibi karmaşık bir iç içe yapısına sahip olduğu durumlarda faydalıdır. Karmaşık türlerin aşağı kısmı, karmaşık olmayan veri türlerine kıyasla içeriğin dizinlemek için gereken ek depolama gereksinimleridir. 

Bazı durumlarda, karmaşık bir veri yapısını bir koleksiyon gibi daha basit bir alan türüne eşleyerek bu avantajları önleyebilirsiniz. Alternatif olarak, bir alan hiyerarşisini tek tek kök düzeyindeki alanlara düzleştirmeyi tercih edebilirsiniz.

:::image type="content" source="media/search-performance/perf-flattened-field-hierarchy.png" alt-text="düzleştirilmiş alan yapısı" border="true":::

## <a name="types-of-queries"></a>Sorgu türleri

Göndereceğiniz sorgu türleri, performans için en önemli faktörlerden biridir ve sorgu iyileştirmesi performansı önemli ölçüde iyileştirebilir. Sorgu tasarlarken aşağıdaki noktaları göz önünde bulundurun:

+ **Aranabilir alan sayısı.** Her ek aranabilir alan, arama hizmeti tarafından ek iş gerektirir. Sorgu zamanında aranmakta olan alanları "searchFields" parametresini kullanarak sınırlayabilirsiniz. Yalnızca performansı geliştirmek için ilgilendiğiniz alanları belirtmek en iyisidir.

+ **Döndürülen veri miktarı.** Çok sayıda içerik almak sorguları daha yavaş yapabilir. Bir sorgu yapılandırırken, yalnızca sonuçlar sayfasını işlemek için gereken alanları döndürün ve ardından Kullanıcı bir eşleşme seçtikten sonra [Arama API](/rest/api/searchservice/lookup-document) 'sini kullanarak kalan alanları alın.

+ **Kısmi terim aramalarının kullanımı.** Ön ek arama, benzer arama ve normal ifade arama gibi [kısmi terim aramaları](search-query-partial-matching.md), sonuçların üretilmesi için tam dizin taramaları gerektirdiğinden tipik anahtar sözcük aramalarından daha pahalı bir işlemdir.

+ **Model sayısı.** Sorgulara model eklenmesi, her sorgu için toplamaları gerektirir. Genel olarak, yalnızca uygulamanızda işlemeyi planladığınız modelleri ekleyin.

+ **Yüksek kardinalite alanlarını sınırlayın.**  *Yüksek bir kardinalite alanı* , önemli sayıda benzersiz değere sahip çok yönlü bir tabloya veya filtrelenebilir alana başvurur ve sonuç olarak sonuçları hesaplarken önemli kaynakları tüketir. Örneğin, bir ürün KIMLIĞI veya açıklama alanını çok yönlü tablo olarak ayarlama ve filtrelenebilir, belgedeki değerlerin çoğu benzersiz olduğundan yüksek önem düzeyi olarak sayılır.

### <a name="tip-use-search-functions-instead-overloading-filter-criteria"></a>İpucu: arama işlevlerini kullanarak filtre ölçütlerini aşırı yükleme

Bir sorgu giderek daha fazla [karmaşık filtre ölçütü](search-query-odata-filter.md#filter-size-limitations)kullandığından, arama sorgusunun performansı düşmez. Bir kullanıcı kimliğine göre sonuçları kırpmaya yönelik filtrelerin kullanımını gösteren aşağıdaki örneği göz önünde bulundurun:

```json
$filter= userid eq 123 or userid eq 234 or userid eq 345 or userid eq 456 or userid eq 567
```

Bu durumda, filtre ifadeleri, her belgedeki tek bir alanın bir Kullanıcı kimliğinin olası birçok değerinden birine eşit olup olmadığını denetlemek için kullanılır. Bu kalıbı [güvenlik kırpması](search-security-trimming-for-azure-search.md) uygulayan uygulamalarda bulabilirsiniz (sorguyu veren kullanıcıyı temsil eden bir asıl kimlik listesine göre bir veya daha fazla asıl kimlik içeren alanı denetleme).

Çok sayıda değer içeren filtreleri yürütmeye yönelik daha etkili bir yol, bu örnekte gösterildiği gibi [ `search.in` işlevi](search-query-odata-search-in-function.md)kullanmaktır:

```json
search.in(userid, '123,234,345,456,567', ',')
```

### <a name="tip-add-partitions-for-slow-individual-queries"></a>İpucu: yavaş tek sorgular için bölüm ekleme

Sorgu performansı genel olarak yavaşanıyorsa, daha fazla çoğaltma eklemek sorunu genellikle çözer. Ancak, sorun tamamlanamayacak kadar uzun süren tek bir sorgu ise ne olur? Bu senaryoda çoğaltmalar eklemek yardım etmez, ancak ek bölümler de oluşabilir. Bir bölüm, verileri ek bilgi işlem kaynakları arasında böler. İki bölüm, verileri yarı bir şekilde ayırır, üçüncü bir bölüm onu üç ve diğerleri olarak böler. 

Bölüm eklemenin olumlu bir yan etkisi, daha yavaş sorguların bazen paralel bilgi işlem nedeniyle daha hızlı bir şekilde gerçekleştirilemesidir. Çok sayıda belgeyle eşleşen sorgular veya çok sayıda belge üzerinde sayı sağlayan modeller gibi düşük seçiciliği sorgularında paralelleştirme belirtiyoruz. Belgelerin yeniden dengeliğine veya belge numaralarını saymaya yönelik önemli bir hesaplama gerektiğinden, ek bölümler eklemek sorguların daha hızlı tamamlanmasını sağlar.  

Bölüm eklemek için [Azure Portal](search-create-service-portal.md), [POWERSHELL](search-manage-powershell.md), [Azure CLI](search-manage-azure-cli.md)veya bir Yönetim SDK 'sını kullanın.

## <a name="service-capacity"></a>Hizmet kapasitesi

Sorgular çok uzun sürmeye başladığında veya hizmet istekleri bırakmaya başladığında bir hizmet aşırı kullanılıyor. Bu durumda, hizmeti yükselterek veya kapasite ekleyerek sorunu ele alabilirsiniz.

Arama hizmetinizin katmanının ve kopyaların/bölümlerin sayısının performansı üzerinde büyük bir etkisi vardır. Her iki katman da performans üzerinde olumlu bir etkisi olan daha hızlı CPU ve daha fazla bellek sağlar.

### <a name="tip-upgrade-to-a-standard-s2-tier"></a>İpucu: Standart S2 katmanına yükseltme

Standart S1 arama katmanı genellikle müşterilerinin başlayacağı yerdir. S1 Hizmetleri için ortak bir düzende, dizinlerin zaman içinde büyümesi ve bu sayede daha fazla bölüm olması gerekir. Daha fazla bölüm daha yavaş yanıt sürelerine yol açacağından, sorgu yükünü işlemek için daha fazla çoğaltma eklenmiştir. Imagine de bir S1 hizmetini çalıştırmanın maliyeti artık ilk yapılandırmanın ötesinde ilerlemedi.

Bu juncture 'de, geçerli hizmetin bölümlerinin veya çoğaltmaların sayısını aşamalı olarak artırarak daha yüksek bir katmana geçiş yapmak yararlı olup olmadığı konusunda önemli bir sorudır. 

Aşağıdaki topolojiyi, kapasite seviyeleri üzerinde gerçekleştirilen bir hizmet örneği olarak göz önünde bulundurun:

+ Standart S1 katmanı
+ Dizin boyutu: 190 GB
+ Bölüm sayısı: 8 (S1 üzerinde bölüm boyutu bölüm başına 25 GB 'dir)
+ Çoğaltma sayısı: 2
+ Toplam arama birimi: 16 (8 Bölüm x 2 çoğaltma)
+ Kuramsal perakende fiyatı: ~ $4.000 ABD doları/ay ($250 USD x 16 arama birimi varsay)

Hizmet yöneticisinin hala daha yüksek gecikme oranları gördüğünü ve başka bir çoğaltma eklemeyi düşündüğünü varsayalım. Bu, çoğaltma sayısını 2 ' den 3 ' e değiştirecek ve sonuç olarak arama birimi sayısını 24 ' e ve $6.000 ABD doları/ay sonucunu elde eden bir fiyat olarak değiştirir.

Ancak, yönetici standart bir S2 katmanına taşımayı seçerse topoloji şöyle görünür:

+ Standart S2 katmanı
+ Dizin boyutu: 190 GB
+ Bölüm sayısı: 2 (S2 'de bölüm boyutu bölüm başına 100 GB)
+ Çoğaltma sayısı: 2
+ Toplam arama birimi: 4 (2 bölüm x 2 çoğaltma)
+ Kuramsal perakende fiyatı: ~ $4.000 ABD doları/ay ($1000 USD x 4 arama birimi)

Bu kuramsal senaryoda, daha düşük katmanlar üzerinde, ilk yerde daha yüksek bir katmanı kabul etmiş gibi, benzer maliyetlere yol açacak yapılandırmalara sahip olabilirsiniz. Ancak, daha yüksek katmanlar, dizin oluşturma işlemini daha hızlı hale getiren Premium Depolama ile gelir. Daha yüksek katmanların da daha fazla işlem gücü ve ek bellek de vardır. Aynı maliyetlerde aynı dizini destekleyen daha güçlü bir altyapıyı de kullanabilirsiniz.

Eklenen belleğin önemli bir avantajı, daha fazla dizinin önbelleğe alınabileceği, daha düşük arama gecikmesi ve saniye başına çok sayıda sorgu olabilir. Bu ek güçle, yöneticinin çoğaltma sayısını artırması ve S1 hizmetinde kalmasından daha az ödeme yapması gerekebilir.

## <a name="next-steps"></a>Sonraki adımlar

Hizmet performansıyla ilgili bu ek makaleleri gözden geçirin.

+ [Performansı analiz etme](search-performance-analysis.md)
+ [Bir hizmet katmanı seçin](search-sku-tier.md)
+ [Kapasite (çoğaltmalar ve bölümler) ekleme](search-capacity-planning.md#adjust-capacity)
