---
title: Azure Cosmos DB el ile ve otomatik ölçeklendirmeyi arasından seçme
description: İş yükünüz için standart (el ile) sağlanan aktarım hızı ve otomatik ölçeklendirme sağlanan aktarım hızını seçme hakkında bilgi edinin.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: dech
ms.openlocfilehash: d8a6471d53ad4b2428504f9c53cbec6bc1967c49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93089650"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>Standart (el ile) ve otomatik ölçeklendirme sağlanan aktarım hızını seçme 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB, iki türü veya sağlanan aktarım hızı tekliflerini destekler: Standart (el ile) ve otomatik ölçeklendirme. Her iki işleme türü, yüksek performans ve ölçek gerektiren görev açısından kritik iş yükleri için uygundur ve aktarım hızı, kullanılabilirlik, gecikme süresi ve tutarlılık üzerinde aynı Azure Cosmos DB SLA 'Lar tarafından desteklenir.

Bu makalede, iş yükünüz için standart (el ile) ve otomatik ölçeklendirme sağlanan aktarım hızı arasından seçim yapma açıklanmaktadır. 

## <a name="overview-of-provisioned-throughput-types"></a>Sağlanan aktarım hızı türlerine genel bakış
Standart (el ile) ve otomatik ölçeklendirme arasındaki farka girmeden önce, sağlanan aktarım hızının Azure Cosmos DB ' de nasıl çalıştığını anlamak önemlidir. 

Sağlanan aktarım hızını kullandığınızda, iş yükünüz için gereken saniye başına istek birimi (RU/s) cinsinden ölçülen aktarım hızını ayarlarsınız. Hizmet, verimlilik gereksinimlerini desteklemek için gereken kapasiteyi sağlar. Okuma, yazma ve sorgular gibi hizmette gerçekleştirilen veritabanı işlemleri, bazı istek birimleri (ru) kullanır. [İstek birimleri](request-units.md)hakkında daha fazla bilgi edinin.

Aşağıdaki tabloda standart (el ile) ve otomatik ölçeklendirme arasındaki üst düzey bir karşılaştırma gösterilmektedir.

|Description|Standart (el ile)|Otomatik Ölçeklendirme|
|-------------|------|-------|
|En uygun|Düzenli veya öngörülebilir trafiğe sahip iş yükleri|Değişken veya öngörülemeyen trafik içeren iş yükleri. Bkz. [Otomatik ölçeklendirme durumlarını kullanma](provision-throughput-autoscale.md#use-cases-of-autoscale).|
|Nasıl çalışır?|`T`El ile değiştirmediğiniz müddetçe zaman içinde statik olan bir dızı ru/sn sağlayabilirsiniz. Her İkincisi, en fazla `T` ru/sn aktarım hızı kullanabilirsiniz. <br/><br/>Örneğin, standart (el ile) 400 RU/s ayarlarsanız, verimlilik 400 RU/s ' de kalır.|Sistemin aşilmesini istemediğiniz en yüksek veya en büyük RU/sn 'yi ayarlayın `Tmax` . Sistem, aktarım hızını otomatik olarak ölçeklendirir `T` `0.1* Tmax <= T <= Tmax` . <br/><br/>Örneğin, otomatik ölçek, 4000 RU/sn 'nin en yüksek RU/sn 'yi ayarlarsanız, sistem 400-4000 RU/s arasında ölçeklendirecektir.|
|Kullanılması gereken durumlar|Üretilen iş kapasitenizi (RU/s) el ile yönetmek ve ölçeği kendiniz yönetmek istiyorsunuz.<br/><br/>Sağlanan RU/s 'nin yüksek, tutarlı bir kullanımı vardır. Ayın tüm saatlerinde, sağlanan RU/s 'yi ayarlarsanız `T` ve saatlerin %66 ' i veya daha uzun bir süre kullanırsanız, standart (el ile) sağlanan ru/s ile tasarruf edersiniz.<br/><br/>Bu, `T` Standart (el ile) ayarı ve otomatik ölçeklendirme ile aynı miktar arasındaki karşılaştırmayı temel alır `Tmax` . |Kullanım kapasitesini temel alarak, üretilen iş kapasitenizi (RU/s) ve ölçeği yönetmek Azure Cosmos DB istiyorsunuz.<br/><br/>Değişken veya tahmin edilmesi zor olan RU/s kullanımlarınız var. Bir ayın tüm saatlerinde, otomatik ölçeklendirme en fazla RU/sn ayarlarsanız `Tmax` ve `Tmax` saat veya daha az %66 için tam miktarı kullanırsanız, otomatik ölçeklendirme ile tasarruf edersiniz.<br/><br/>Bu, otomatik ölçeklendirmeyi ayarlama `Tmax` ve `T` Standart (el ile) aktarım hızı ile aynı miktarı arasındaki karşılaştırmayı temel alır.|
|Faturalama modeli|Faturalandırma, ne kadar RUs tüketildiğine bakılmaksızın, sağlanan RU/s için saat başına yapılır.<br/><br/>Örnek: <li>Sağlama 400 RU/s</li><li>Saat 1: istek yok</li><li>Saat 2:400 RU/sn istek yüzdesi</li><br/><br/>1 ve 2. her iki saat için de [Standart (el ile) ücretlerden](https://azure.microsoft.com/pricing/details/cosmos-db/)her iki saat IÇIN 400 ru/sn faturalandırılırsınız.|Faturalandırma, sistem saatte bir şekilde ölçeklendirilebilen en yüksek RU/sn için saatte bir gerçekleştirilir. <br/><br/>Örnek: <li>Otomatik ölçeklendirme sağla 4000 RU/sn (400-4000 RU/s arasında ölçekleme)</li><li>Saat 1: sistem en yüksek 3500 RU/sn değerine kadar ölçeği</li><li>Saat 2: sistem `Tmax` , kullanım olmaması nedeniyle en az 400 ru/sn (her zaman %10) olarak ölçeklendirildi</li><br/><br/>3500 RU/s için saat 1 ' de faturalandırılır ve [Otomatik ölçeklendirme sağlanan aktarım hızı ücretlendirirken](https://azure.microsoft.com/pricing/details/cosmos-db/)saat 2 ' de 400 ru/s olarak faturalandırılırsınız. Her RU/sn için otomatik ölçeklendirme oranı 1,5 * Standart (el ile) oranıdır.
|Sağlanan RU/s 'yi aşarsanız ne olur?|RU/s, sağlanmak üzere statik kalır. Saniye içinde sağlanan ru 'nın ötesine geçen her türlü istek, yeniden denemeden önce beklemek için bir süre öneren bir yanıt olarak hız sınırlı olacaktır. Gerekirse RU/s 'yi el ile artırabilir veya azaltabilirsiniz.| Sistem RU/s 'yi otomatik ölçek en yüksek RU/sn 'ye kadar ölçeklendirecektir. Saniye içinde otomatik ölçeklendirme en fazla RU/sn 'nin ötesine geçen her türlü istek, yeniden denemeden önce beklemek için bir süre öneren bir yanıt olarak hız sınırlı olacaktır.|

## <a name="understand-your-traffic-patterns"></a>Trafik desenlerinizi anlayın

### <a name="new-applications"></a>Yeni uygulamalar ###

Yeni bir uygulama oluşturuyorsanız ve trafik modelinizi henüz bilmiyorsanız, başlangıçta aşırı sağlamayı önlemek için RU/s (veya minimum RU/sn) giriş noktasında başlamak isteyebilirsiniz. Ya da yüksek ölçekli bir uygulamanız olması gerekmiyorsa, maliyeti iyileştirmek için en az RU/sn giriş noktası sağlamak isteyebilirsiniz. Düşük beklenen trafiğe sahip küçük uygulamalar için [sunucusuz](throughput-serverless.md) kapasite modunu da dikkate alabilirsiniz.

Standart (el ile) veya otomatik ölçeklendirme kullanmayı planladığınızdan, şunları göz önünde bulundurmanız gerekir:

400 RU/sn giriş noktasında standart (el ile) RU/s sağlarsanız, üretilen işi el ile değiştirmediğiniz müddetçe 400 RU/s üzerinde kullanamazsınız. Standart (el ile) sağlanan aktarım hızı ücreti saat başına 400 RU/sn için faturalandırılırsınız.

4000 RU/sn 'nin en fazla RU/sn giriş noktasında otomatik ölçeklendirme üretilen işi sağlarsanız, kaynak 400 ila 4000 RU/s arasında ölçeklendirecektir. Otomatik ölçeklendirme işleme oranı, RU/s başına standart (el ile) oranından 1,5 x, sistemin en az 400 RU/sn 'ye ölçeklendiğinden, faturanız el ile 400 RU/s 'yi sağladıysanız daha yüksek olacaktır. Ancak, otomatik ölçeklendirme ile, her zaman, uygulama trafiğiniz ani artışlar varsa, Kullanıcı eylemi gerekmeden 4000 RU/sn 'ye kadar kullanabilirsiniz. Genel olarak, 1,5 x otomatik ölçeklendirme oranıyla, her zaman en fazla RU/sn 'ye kadar kullanım avantajına sahip olmanız gerekir.

Verimlilik gereksinimlerinizi tahmin etmek için Azure Cosmos DB [Kapasite hesaplayıcısını](estimate-ru-with-capacity-planner.md) kullanın. 

### <a name="existing-applications"></a>Mevcut uygulamalar ###

Standart (el ile) sağlanan aktarım hızını kullanarak mevcut bir uygulamanız varsa, trafik deseninin otomatik ölçeklendirme için uygun olup olmadığını öğrenmek için [Azure izleyici ölçümlerini](../azure-monitor/insights/cosmosdb-insights-overview.md) kullanabilirsiniz. 

İlk olarak, veritabanınızın veya kapsayıcının [normalleştirilmiş istek birimi tüketim ölçüsünü](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) bulun. Normalleştirilmiş kullanım, şu anda standart (el ile) sağlanan aktarım hızını kullanmakta olduğunuz bir ölçüdür. Sayının %100 ' e yakın olması, sağlanan RU/s 'nizi tamamen kullanıyorsunuz. Ölçüm hakkında [daha fazla bilgi edinin](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) .

Ardından, normalleştirilmiş kullanımın zaman içinde nasıl değiştiğini saptayın. Her saat için en yüksek normalleştirilmiş kullanımı bulun. Ardından, ortalama normalleştirilmiş kullanımı her saat boyunca hesaplayın. Ortalama kullanımınızın %66'nın altında olduğunu görürseniz veritabanınızda veya kapsayıcınızda otomatik ölçeklendirmeyi etkinleştirmeyi göz önünde bulundurun. Buna karşılık ortalama kullanım %66'nın üzerindeyse standart (el ile) sağlanan işleme biriminde kalmanız önerilir.

> [!TIP]
> Hesabınız çok bölgeli yazmaları kullanacak şekilde yapılandırıldıysa ve birden fazla bölge içeriyorsa, 100 RU/sn başına ücret, hem el ile hem de otomatik ölçeklendirme için aynıdır. Yani, otomatik ölçeklendirmeyi etkinleştirme, kullanımın ne olursa olsun ek ücret alınmaz. Sonuç olarak, yalnızca uygulamanızın ölçeklendirilen RU/sn için ödeme yapma tasarruflarından yararlanmak amacıyla, birden fazla bölgeniz olduğunda otomatik ölçeklendirmeyi çok bölgeli bir şekilde kullanmak her zaman önerilir. Çok bölgeli yazmalar ve bir bölgeniz varsa, otomatik ölçeklendirmeyi maliyet tasarruflarına neden olup olmadığını anlamak için Ortalama kullanımı kullanın. 

#### <a name="examples"></a>Örnekler

İki farklı örnek iş yüküne göz atalım ve el ile veya otomatik ölçeklendirme işleme için uygun olup olmadığını analiz edelim. Genel yaklaşımı göstermek için, el ile ve otomatik ölçeklendirme kullanma arasındaki maliyet farkını belirlemede üç saatlik geçmişi analiz edeceğiz. Üretim iş yükleri için, RU/s kullanımının bir modelini oluşturmak üzere 7 ila 30 gün geçmiş (veya varsa daha uzun) kullanılması önerilir.

> [!NOTE]
> Bu belgede gösterilen tüm örnekler, ABD 'deki devlet dışı bir bölgede dağıtılan bir Azure Cosmos hesabının fiyatını temel alır. Fiyatlandırma ve hesaplama, kullandığınız bölgeye bağlı olarak farklılık gösterir, en son fiyatlandırma bilgileri için Azure Cosmos DB [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/) bakın.

Varsayımlar:
- Şu anda 30.000 RU/sn 'nin el ile üretilen iş hızına ihtiyacımız olduğunu varsayalım. 
- Bölgemiz tek bölgeli yazmalar ile yapılandırılır, tek bölge. Birden çok bölgemiz varsa, saatlik maliyetten bölge sayısını çarpacağız.
- Tek bölgeli yazma hesaplarında el ile ($0,008 ABD/saat başına 100 RU/sn başına ABD Doları) ve otomatik ölçeklendirme işleme $0,012 (saat başına ABD 100 doları) için genel fiyatlandırma ücretleri kullanın. Ayrıntılar için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/cosmos-db/) . 

#### <a name="example-1-variable-workload-autoscale-recommended"></a>Örnek 1: değişken iş yükü (otomatik ölçeklendirme önerilir)

İlk olarak, normalleştirilmiş RU tüketimine göz atacağız. Bu iş yükünün değişken trafiği vardır ve %6 ' dan %100 ' e kadar normalleştirilmiş RU tüketimine sahiptir. Tahmin edilmesi zor olan, az sayıda saat için %100 oranında zaman zaman ani artışlar vardır. 

:::image type="content" source="media/how-to-choose-offer/variable-workload_use_autoscale.png" alt-text="Değişken trafiği olan iş yükü-%6 ila %100 arasında normalleştirilmiş RU tüketimi, tüm saatler için":::

30.000 RU/s el ile üretilen iş üretimini ve otomatik ölçeklendirme en yüksek RU/sn ayarını 30.000 (3000-30.000 RU/s arasında ölçekler) için sağlama maliyetini karşılaştıralım. 

Şimdi geçmişi analiz edelim. Aşağıdaki tabloda açıklanan kullanım olduğunu varsayalım. Bu üç saat boyunca ortalama kullanım %39 ' dir. Normalleştirilmiş RU tüketiminin %66 ' den küçük bir ortalama olduğu için, otomatik ölçeklendirme kullanarak tasarruf ederiz. 

Saat 1 ' de, %6 kullanım olduğunda otomatik ölçeklendirmeyi, en az RU/sn 'nin saat başına en az %10 ' luk için faturalandırılyacağını unutmayın. Otomatik ölçeklendirme maliyeti belirli saatlerde el ile üretilen iş maliyetinden daha yüksek olabilir, ancak ortalama kullanım tüm saatlerde %66 ' den az olduğunda otomatik ölçeklendirme, genel olarak olacaktır.

|Süre  | Kullanım |Faturalandırılan otomatik ölçeklendirme RU/s  |Seçenek 1: El Ile 30.000 RU/sn  | Seçenek 2: otomatik ölçeklendirme 3000-30.000 RU/s |
|---------|---------|---------|---------|---------|
|Saat 1  | %6  |     3000  |  30.000 * 0,008/100 = $2,40        |   3000 * 0,012/100 = $0,36      |
|Saat 2  | %100  |     30.000    |  30.000 * 0,008/100 = $2,40       |  30.000 * 0,012/100 = $3,60      |
|Saat 3 |  üst  |     3300    |  30.000 * 0,008/100 = $2,40       |    3300 * 0,012/100 = $0,40     |
|**Toplam**   |  |        |  $7,20       |    $4,36 (%39 tasarruf)    |

#### <a name="example-2-steady-workload-manual-throughput-recommended"></a>Örnek 2: sürekli iş yükü (el ile üretilen işlem önerilir)

Bu iş yükünün %72 ile %100 arasında normalleştirilmiş RU tüketimine sahip sürekli trafiği vardır. Sağlanan 30.000 RU/sn ile, 21.600 ila 30.000 RU/s arasında tüketiyoruz demektir.

:::image type="content" source="media/how-to-choose-offer/steady_workload_use_manual_throughput.png" alt-text="Sürekli trafik içeren iş yükü-%72 ve %100 arasında normalleştirilmiş RU tüketimi tüm saatler için":::

30.000 RU/s el ile üretilen iş üretimini ve otomatik ölçeklendirme en yüksek RU/sn ayarını 30.000 (3000-30.000 RU/s arasında ölçekler) için sağlama maliyetini karşılaştıralım.

Tabloda açıklandığı gibi kullanım geçmişimiz olduğunu varsayalım. Bu üç saat boyunca ortalama kullanımımız %88 ' dir. Normalleştirilmiş RU tüketiminin %66 ' den büyük bir ortalamalar olduğundan, el ile üretilen iş kullanarak tasarruf ederiz.

Genel olarak, bir ayda tüm 730 saat boyunca ortalama kullanım %66 ' den büyükse, el ile üretilen iş kullanarak tasarruf ederiz. 

| Süre | Kullanım |Faturalandırılan otomatik ölçeklendirme RU/s  |Seçenek 1: El Ile 30.000 RU/sn  | Seçenek 2: otomatik ölçeklendirme 3000-30.000 RU/s |
|---------|---------|---------|---------|---------|
|Saat 1  | %72  |     21.600   |  30.000 * 0,008/100 = $2,40        |   21600 * 0,012/100 = $2,59      |
|Saat 2  | %93  |     28.000    |  30.000 * 0,008/100 = $2,40       |  28.000 * 0,012/100 = $3,36       |
|Saat 3 |  %100  |     30.000    |  30.000 * 0,008/100 = $2,40       |    30.000 * 0,012/100 = $3,60     |
|**Toplam**   |  |        |  $7,20       |    $9,55     |

> [!TIP]
> Standart (el ile) aktarım hızı sayesinde, otomatik ölçeklendirme 'ye geçtiğinizde kullanabileceğiniz gerçek RU/s 'yi tahmin etmek için normalleştirilmiş kullanım ölçümünü kullanabilirsiniz. Şu anda sağlanan standart (el ile) RU/s ile, zaman içinde normalleştirilmiş kullanımı çarpın. Örneğin, 5000 RU/s 'yi sağladıysanız ve normalleştirilmiş kullanım %90 ise, RU/s kullanımı 0,9 * 5000 = 4500 RU/s ' dir. Trafik deseninin değişken olduğunu, ancak siz veya sağlanmış olduğunu görürseniz, otomatik ölçeklendirmeyi etkinleştirmek ve sonra otomatik ölçeklendirme en büyük RU/s ayarını uygun şekilde değiştirmek isteyebilirsiniz.

#### <a name="how-to-calculate-average-utilization"></a>Ortalama kullanımı hesaplama
En yüksek RU/sn için bir saat içinde ölçeklendirilmiş olan faturaları otomatik ölçeklendirme. Zamana göre normalleştirilmiş RU tüketimini analiz edilirken, ortalamayı hesaplarken saat başına en yüksek kullanımı kullanmak önemlidir. 

Tüm saatlerde en yüksek kullanımın ortalamasını hesaplamak için:
1. Noramon RU tüketim ölçümünde **toplamayı** **en yüksek** olarak ayarlayın.
1. 1 saat **ayrıntı düzeyini** seçin.
1. **Grafik seçenekleri**' ne gidin.
1. Çubuk grafik seçeneğini belirleyin. 
1. **Paylaşma** bölümünde **Excel 'e indir** seçeneğini belirleyin. Oluşturulan elektronik tablodan, her saat içindeki ortalama kullanımı hesaplayın. 

:::image type="content" source="media/how-to-choose-offer/variable-workload-highest-util-by-hour.png" alt-text="Normalleştirilmiş RU tüketimini saate göre görmek için 1) zaman parçalı yapısını 1 saate kadar seçin; 2) grafik ayarlarını düzenleyin; 3) çubuk grafik seçeneğini belirleyin; 4) Share altında, her saat içinde ortalamayı hesaplamak için Excel 'e Indir seçeneğini belirleyin. ":::

## <a name="measure-and-monitor-your-usage"></a>Kullanımınızı ölçme ve izleme
Zaman içinde, üretilen iş türünü seçtikten sonra uygulamanızı izlemeniz ve gereken ayarlamaları yapmanız gerekir. 

Otomatik ölçeklendirme kullanırken, sağlanan otomatik ölçeklendirme en fazla RU/sn (**Otomatik ölçeklendirme en fazla aktarım hızı**) ve sistem şu anda ölçeklendirildi (**sağlanan aktarım hızı**) için Azure izleyici 'yi kullanın. Aşağıda, otomatik ölçeklendirme kullanan bir değişkene veya öngörülemeyen iş yüküne bir örnek verilmiştir. Herhangi bir trafik olmadığında, sistem RU/s 'yi en fazla RU/sn 'nin en az %10 ' u, bu örnekte sırasıyla 5000 RU/s ve 50.000 RU/s olacak şekilde ölçeklendirir. 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="Otomatik ölçeklendirme kullanan iş yükü örneği, otomatik ölçeklendirme en fazla RU/sn ve 50.000 RU/sn ve verimlilik, 5000-50.000 RU/s ile değişiyor":::

> [!NOTE]
> Standart (el ile) sağlanan aktarım hızını kullandığınızda, **sağlanan aktarım hızı** ölçümü, bir kullanıcının ayarlamış olduğu şeyleri gösterir. Otomatik ölçeklendirme üretilen işi kullandığınızda bu ölçüm, sistemin Şu anda ölçeklendirmekte olduğu RU/s 'yi ifade eder.

## <a name="next-steps"></a>Sonraki adımlar
* Yeni iş yüklerinin verimini tahmin etmek için [ru Hesaplayıcı](https://cosmos.azure.com/capacitycalculator/) kullanın.
* Mevcut iş yüklerinizi izlemek için [Azure izleyici](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) 'yi kullanın.
* [Azure Cosmos veritabanı veya kapsayıcısı üzerinde otomatik ölçeklendirme işleme sağlamayı](how-to-provision-autoscale-throughput.md)öğrenin.
* [Otomatik ÖLÇEKLENDIRME SSS](autoscale-faq.md)'sini inceleyin.