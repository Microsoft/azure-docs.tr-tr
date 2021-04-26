---
title: Performans karşılaştırmaları
titleSuffix: Azure Cognitive Search
description: Çeşitli performans değerlendirmeleri aracılığıyla Azure Bilişsel Arama performansı hakkında bilgi edinin
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 9f4473d6c8a584bf60e5c8fe2d69d6a56a55e71d
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108445"
---
# <a name="azure-cognitive-search-performance-benchmarks"></a>Azure Bilişsel Arama performans değerlendirmeleri

Azure Bilişsel Arama performansı, arama hizmetinizin Boyutu ve gönderdiğiniz sorgu türleri gibi [çeşitli faktörlere](search-performance-tips.md) bağlıdır. İş yükünüz için gereken arama hizmeti boyutunu tahmin etmeye yardımcı olmak için, farklı arama hizmetleri ve yapılandırmalarının performansını belgelemek üzere çeşitli değerlendirmeler çalıştırdık. Bu kıyaslamalar, hizmetinizden belirli bir performans düzeyini garanti etmez ancak size, bekleneceğiniz performansa ilişkin bir fikir verebilir.

Farklı kullanım durumlarının bir aralığını kapsayacak şekilde, iki ana senaryo için kıyaslamalar çalıştırdık:

* **E-ticaret araması** -Bu kıyaslama gerçek bir e-ticaret senaryosuna öykünür ve İskandinav e-ticaret şirketi [cdi](https://cdon.com)'yi temel alır.
* **Belge arama** -bu senaryo, [semantik Scholar](http://s2-public-api-prod.us-west-2.elasticbeanstalk.com/corpus/download/)tam metin belgeleri üzerinde anahtar sözcük aramadan oluşur. Bu, tipik bir belge arama çözümüne öykünür.

Bu senaryolar farklı kullanım durumlarını yansıttığından her senaryo farklıdır, bu nedenle bireysel iş yükünüzün performans testini her zaman öneriyoruz. [JMeter kullanarak bir performans testi çözümü](https://github.com/Azure-Samples/azure-search-performance-testing) yayımladık, bu sayede kendi hizmetinize karşı benzer testleri çalıştırabilirsiniz.

## <a name="testing-methodology"></a>Test yöntemi

Azure Bilişsel Arama performansını kıyaslamaya yönelik testleri, farklı katmanda ve çoğaltma/bölüm birleşimlerinde iki farklı senaryo için çalıştırdık.

Bu kıyaslamaları oluşturmak için aşağıdaki metodolojiyi kullanılmıştır:

1. Test, `X` 180 saniye boyunca saniye başına sorgu (QPS) ile başlar. Bu genellikle 5 veya 10 QPS idi.
2. QPS daha sonra `X` başka bir 180 saniye boyunca artar ve çalışır
3. Her 180 saniyede bir test, `X` ortalama gecikme 1000 ms üzerinde arttırana veya sorguların %99 ' inden az olana kadar QPS tarafından arttı.

Aşağıdaki grafik testin sorgu yükünün neye benzediklerine ilişkin görsel bir örnek sağlar:

![Örnek test](./media/performance-benchmarks/example-test.png)

Her senaryo, önbelleğe alma işlemi tarafından aşırı çarpıtılmış olan testlerin olmaması için en az 10.000 benzersiz sorgu kullandı.

> [!IMPORTANT]
> Bu sınamalar yalnızca sorgu iş yüklerini içerir. Dizin oluşturma işlemlerinin yüksek volumne olmasını beklemeniz durumunda, tahmin ve performans testinizde bu işlemi gerçekleştirdiğinizden emin olun. Bu [öğreticide](tutorial-optimize-indexing-push-api.md), dizin oluşturma benzetimi için örnek kod bulunabilir.

### <a name="definitions"></a>Tanımlar

- **En fazla QPS** -aşağıdaki en fazla QPS numarası, azaltma işlemi yapılmadan başarılı bir şekilde tamamlanan ve 1000 ms altında ortalama gecikme süresi olan bir 99 testte elde edilen en yüksek QPS 'yi temel alır.

- **MAKSIMUM QPS yüzdesi** -belirli bir test için elde edilen en fazla QPS yüzdesi. Örneğin, belirli bir test en fazla 100 QPS 'ye eriştiğinde, en fazla QPS 'nin %20 ' si 20 QPS olur.

- **Gecikme süresi** -sunucunun bir sorgu için gecikmesi; Bu sayılar [gidiş dönüş gecikmesini (RTT)](https://en.wikipedia.org/wiki/Round-trip_delay)içermez. Aşağıdaki değerler milisaniye (MS) cinsindendir.

### <a name="disclaimer"></a>Disclaimer

Bu değerlendirmeleri çalıştırmak için kullandığımız kod [burada](https://github.com/Azure-Samples/azure-search-performance-testing/tree/main/other_tools)bulunabilir. [JMeter performans testi çözümüyle](https://github.com/Azure-Samples/azure-search-performance-testing) , aşağıdaki kıyaslamalara kıyasla biraz daha düşük bir QPS düzeyi gözlemlediğimiz konusunda dikkat edin. Farkların, testlerin stilindeki farklılıklara eklenebilir. Bu, performans testlerinizi mümkün olduğunca üretim iş yükünüze benzer şekilde yapmanın önemini konuşuyor.

Bu kıyaslamalar, hizmetinizden belirli bir performans düzeyini garanti etmez ancak senaryonuza bağlı olarak beklemeniz için size bir fikir sağlayabilir.

Sorularınız veya endişeleriniz varsa bizimle bize ulaşın azuresearch_contact@microsoft.com .

## <a name="benchmark-1-e-commerce-search"></a>Kıyaslama 1: E-ticaret arama

:::row:::
   :::column span="1":::
      ![CDON logosu](./media/performance-benchmarks/cdon-logo-160px2.png)
   :::column-end:::
   :::column span="3":::
      Bu kıyaslama, e-ticaret şirketi, [CDON](https://cdon.com), İskandinav bölgesinin en büyük çevrimiçi marketi ile Isveç, Finlandiya, Norveç ve Danimarka işlemlerinde oluşturulmuştur. CDON, 1.500 tüccarların ' ye kadar, 8.000.000 ' den fazla ürün içeren geniş aralıklı bir sınıflama sunmaktadır. 2020 ' de CDON, 120.000.000 ziyaretçi ve 2.000.000 etkin müşteriler içeriyordu. [Bu makalede](https://pulse.microsoft.com/transform/na/fa1-how-cdon-has-been-using-technology-to-become-the-leading-marketplace-in-the-nordics/)CDON 'ın Azure bilişsel arama kullanımı hakkında daha fazla bilgi edinebilirsiniz.
   :::column-end:::
:::row-end:::

Bu testleri çalıştırmak için, CDON 'ın üretim arama dizini 'nin bir anlık görüntüsünü ve [Web sitelerinden](https://cdon.com)binlerce benzersiz sorguyu kullandık.

### <a name="scenario-details"></a>Senaryo ayrıntıları

- **Belge sayısı**: 6.000.000 
- **Dizin boyutu**: 20 GB
- **Dizin şeması**: 250 alanları toplam, 25 aranabilir alan ve 200 çok yönlü/filtrelenebilir alanları olan geniş bir dizin
- **Sorgu türleri**: model, filtre, sıralama ve Puanlama profilleri dahil olmak üzere tam metin arama sorguları

### <a name="s1-performance"></a>S1 performansı

#### <a name="queries-per-second"></a>Sorgu/saniye

Aşağıdaki grafikte, bir hizmetin saniye başına (QPS) sorgu bakımından uzun bir süre işleyebileceği en yüksek sorgu yükleme işlemi gösterilmektedir.

![En yüksek sürdürülebilir QPS, e-ticaret S1](./media/performance-benchmarks/s1-ecom-qps.png)

#### <a name="query-latency"></a>Sorgu gecikmesi

Sorgu gecikmesi, hizmetin yüküne ve yüksek stres kapsamındaki hizmetlere göre değişiklik gösterir ve ortalama sorgu gecikmesi daha yüksektir. Aşağıdaki tabloda, üç farklı kullanım düzeyi için% 25th, 50th, 75th, 90th, 95 TH ve 11. sorgu gecikmesi yüzdebirlik değeri gösterilmektedir.

| Maksimum QPS yüzdesi  | Ortalama gecikme süresi | 25% | 75% | %90 | %95 | %99|
|---|---|---|---| --- | --- | --- | 
| %20  | 104 MS  | 35 MS  | 115 MS   | 177 MS | 257 MS | 738 MS |
| 50%  | 140 MS  | 47 MS  | 144 MS   | 241 MS | 400 MS | 1175 MS |
| %80  | 239 MS  | 77 MS  | 248 MS   | 466 MS | 763 MS | 1752 MS | 


### <a name="s2-performance"></a>S2 performansı

#### <a name="queries-per-second"></a>Sorgu/saniye

Aşağıdaki grafikte, bir hizmetin saniye başına (QPS) sorgu bakımından uzun bir süre işleyebileceği en yüksek sorgu yükleme işlemi gösterilmektedir.

![En yüksek sürdürülebilir SCOMMERCE S2](./media/performance-benchmarks/s2-ecom-qps.png)

#### <a name="query-latency"></a>Sorgu gecikmesi

Sorgu gecikmesi, hizmetin yüküne ve yüksek stres kapsamındaki hizmetlere göre değişiklik gösterir ve ortalama sorgu gecikmesi daha yüksektir. Aşağıdaki tabloda, üç farklı kullanım düzeyi için% 25th, 50th, 75th, 90th, 95 TH ve 11. sorgu gecikmesi yüzdebirlik değeri gösterilmektedir.

| Maksimum QPS yüzdesi  | Ortalama gecikme süresi | 25% | 75% | %90 | %95 | %99|
|---|---|---|---| --- | --- | --- | 
| %20  | 56 MS | 21 MS | 68 MS  | 106 MS  | 132 MS | 210 MS | 
| 50%  | 71 MS  | 26 MS  | 83 MS   | 132 MS | 177 MS | 329 MS |
| %80  | 140 MS  | 47 MS  | 153 MS   | 293 MS | 452 MS | 924 MS | 

### <a name="s3-performance"></a>S3 performansı

#### <a name="queries-per-second"></a>Sorgu/saniye

Aşağıdaki grafikte, bir hizmetin saniye başına (QPS) sorgu bakımından uzun bir süre işleyebileceği en yüksek sorgu yükleme işlemi gösterilmektedir.

![En yüksek sürdürülebilir QPS e-ticaret S3](./media/performance-benchmarks/s3-ecom-qps.png)

Bu durumda, ikinci bir bölüm eklemenin en fazla QPS 'yi önemli ölçüde artıracağını, ancak üçüncü bir bölümün eklenmesi, önemli bir veren marjinal dönüşler sağlar. Daha küçük bir geliştirme, büyük olasılıkla yalnızca iki bölümden oluşan tüm verilerin S3's etkin belleğe zaten çekilmesinden kaynaklanır.

#### <a name="query-latency"></a>Sorgu gecikmesi

Sorgu gecikmesi, hizmetin yüküne ve yüksek stres kapsamındaki hizmetlere göre değişiklik gösterir ve ortalama sorgu gecikmesi daha yüksektir. Aşağıdaki tabloda, üç farklı kullanım düzeyi için% 25th, 50th, 75th, 90th, 95 TH ve 11. sorgu gecikmesi yüzdebirlik değeri gösterilmektedir.

| Maksimum QPS yüzdesi  | Ortalama gecikme süresi | 25% | 75% | %90 | %95 | %99|
|---|---|---|---| --- | --- | --- |
| %20  | 50 MS  | 20 MS  | 64 MS   | 83 MS | 98 MS | 160 ms |
| 50%  | 62 MS  | 24 MS  | 80 MS   | 107 MS | 130 MS | 253 MS |
| %80  | 115 MS  | 38 MS  | 121 MS   | 218 MS | 352 MS | 828 MS |

## <a name="benchmark-2-document-search"></a>Kıyaslama 2: belge arama

### <a name="scenario-details"></a>Senaryo ayrıntıları

- **Belge sayısı**: 7.500.000
- **Dizin boyutu**: 22 GB
- **Dizin şeması**: 23 alan; 8 aranabilir, 10 filtrelenebilir/çok yönlü tablo
- **Sorgu türleri**: model ve isabet vurgulaması ile anahtar sözcük aramaları

### <a name="s1-performance"></a>S1 performansı

#### <a name="queries-per-second"></a>Sorgu/saniye

Aşağıdaki grafikte, bir hizmetin saniye başına (QPS) sorgu bakımından uzun bir süre işleyebileceği en yüksek sorgu yükleme işlemi gösterilmektedir.

![En yüksek sürdürülebilir QPS belge arama S1](./media/performance-benchmarks/s1-docsearch-qps.png)

#### <a name="query-latency"></a>Sorgu gecikmesi

Sorgu gecikmesi, hizmetin yüküne ve yüksek stres kapsamındaki hizmetlere göre değişiklik gösterir ve ortalama sorgu gecikmesi daha yüksektir. Aşağıdaki tabloda, üç farklı kullanım düzeyi için% 25th, 50th, 75th, 90th, 95 TH ve 11. sorgu gecikmesi yüzdebirlik değeri gösterilmektedir.

| Maksimum QPS yüzdesi  | Ortalama gecikme süresi | 25% | 75% | %90 | %95 | %99|
|---|---|---|---| --- | --- | --- |
| %20  | 67 MS  | 44 MS  | 77 MS   | 103 MS | 126 MS | 216 MS |
| 50%  | 93 MS  | 59 MS  | 110 MS   | 150 MS | 184 MS | 304 MS |
| %80  | 150 MS  | 96 MS  | 184 MS   | 248 MS | 297 MS | 424 MS |

### <a name="s2-performance"></a>S2 performansı

#### <a name="queries-per-second"></a>Sorgu/saniye

Aşağıdaki grafikte, bir hizmetin saniye başına (QPS) sorgu bakımından uzun bir süre işleyebileceği en yüksek sorgu yükleme işlemi gösterilmektedir.

![En yüksek sürdürülebilir QPS belge arama S2](./media/performance-benchmarks/s2-docsearch-qps.png)

#### <a name="query-latency"></a>Sorgu gecikmesi

Sorgu gecikmesi, hizmetin yüküne ve yüksek stres kapsamındaki hizmetlere göre değişiklik gösterir ve ortalama sorgu gecikmesi daha yüksektir. Aşağıdaki tabloda, üç farklı kullanım düzeyi için% 25th, 50th, 75th, 90th, 95 TH ve 11. sorgu gecikmesi yüzdebirlik değeri gösterilmektedir.

| Maksimum QPS yüzdesi  | Ortalama gecikme süresi | 25% | 75% | %90 | %95 | %99|
|---|---|---|---| --- | --- | --- |
| %20  | 45 MS  | 31 MS  | 55 MS   | 73 MS | 84 MS | 109 MS |
| 50%  | 63 MS  | 39 MS  | 81 MS   | 106 MS | 123 MS | 163 MS |
| %80  | 115 MS  | 73 MS  | 145 MS   | 191 MS | 224 MS | 291 MS |

## <a name="takeaways"></a>Paketler

Bu kıyaslamalar sayesinde, performans Azure Bilişsel Arama teklifleri hakkında bir fikir edinebilirsiniz. Farklı katmanlardaki hizmetler arasındaki farkı de görebilirsiniz.

Bu kıyaslamalardan bazı önemli yollar şunlardır:

* S2, genellikle sorgu biriminin S1 olarak en az dört kez idare edebilir
* S2, benzer sorgu birimlerindeki S1 'ten genellikle daha düşük gecikme süresine sahip olur
* Çoğaltmaları eklerken, bir hizmetin tanıtıcı genellikle doğrusal bir şekilde ölçeklendirilebileceği şekilde (örneğin, bir çoğaltmanın 10 QPS 'yi işleyebileceği durumlarda beş çoğaltma genellikle 50 QPS 'yi işleyebilir)
* Hizmetin yükün ne kadar yüksek olması, ortalama gecikme süresinin ne kadar yüksek olacağını

Ayrıca, performansın senaryolar arasında büyük ölçüde farklılık gösterebileceğini de görebilirsiniz. Tahmin ettiğiniz performansı almıyorsanız [daha iyi performans için ipuçlarına](search-performance-tips.md)göz atın.

## <a name="next-steps"></a>Sonraki adımlar

Performans kıyaslamalarıyla karşılaşdığınıza göre, performansı etkileyen Bilişsel Arama performansını ve temel faktörleri çözümleme hakkında daha fazla bilgi edinebilirsiniz.

> [!div class="nextstepaction"]
> [Performansı çözümleme](search-performance-analysis.md) 
>  [Daha iyi performans ipuçları](search-performance-tips.md)