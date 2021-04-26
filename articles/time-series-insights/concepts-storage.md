---
title: Depolamaya genel bakış-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 'de veri depolama hakkında bilgi edinin.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: cd26df1de86ee4bdb33050d0bc4769663707733e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725035"
---
# <a name="data-storage"></a>Veri Depolama

Bu makalede, Azure Time Series Insights Gen2 ' deki veri depolama alanı açıklanmaktadır. Normal ve soğuk, veri kullanılabilirliği ve en iyi uygulamaları içerir.

## <a name="provisioning"></a>Sağlama

Bir Azure Time Series Insights Gen2 ortamı oluşturduğunuzda, aşağıdaki seçeneklere sahip olursunuz:

* Soğuk veri depolama:
  * Ortamınız için seçtiğiniz abonelikte ve bölgede yeni bir Azure depolama kaynağı oluşturun.
  * Önceden var olan bir Azure depolama hesabı ekleyin. Bu seçenek yalnızca bir Azure Resource Manager [şablonundan](/azure/templates/microsoft.timeseriesinsights/allversions)dağıtarak kullanılabilir ve Azure Portal görünmez.
* Sıcak veri depolama:
  * Bir sıcak mağaza isteğe bağlıdır ve sağlama sırasında veya sonrasında etkinleştirilebilir veya devre dışı bırakılabilir. Daha sonraki bir zamanda yarı depolamayı etkinleştirmeye karar verirseniz ve soğuk deponuzda zaten veri varsa, beklenen davranışı anlamak için aşağıdaki [bölümü gözden](concepts-storage.md#warm-store-behavior) geçirin. Yarı depolama veri saklama süresi 7 ile 31 gün arasında yapılandırılabilir ve bu da gerektiğinde ayarlanabilir.

Bir olay alındığı zaman, hem ısınma deposunda hem de soğuk depoda dizinlenir.

[![Depolamaya genel bakış](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> Soğuk depo verilerinin bulunduğu Azure Blob depolama hesabının sahibi olarak hesaptaki tüm verilere tam erişiminiz vardır. Bu erişim yazma ve silme izinleri içerir. Veri kaybına neden olabileceğinden, Gen2 yazmaları Azure Time Series Insights verileri düzenleme veya silme.

## <a name="data-availability"></a>Veri kullanılabilirliği

En iyi sorgu performansı için Gen2 bölümler ve dizinler verileri Azure Time Series Insights. Veriler, Dizin oluşturulduktan sonra hem normal (etkinse) hem de soğuk depodan sorgu için kullanılabilir hale gelir. Alınan veri miktarı ve bölüm başına aktarım hızı, kullanılabilirliği etkileyebilir. En iyi performansa yönelik olay kaynağı [verimlilik sınırlamalarını](./concepts-streaming-ingress-throughput-limits.md) ve [en iyi uygulamaları](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) gözden geçirin. Ayrıca, ortamınızda veri işleme sorunları yaşıyorsa bildirim almak için bir gecikme [uyarısı](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) da yapılandırabilirsiniz.

> [!IMPORTANT]
> Veriler kullanılabilir hale gelmeden önce 60 saniyeye kadar bir süre yaşayabilirsiniz. 60 saniyenin ötesinde önemli gecikme yaşınızı yaşıyorsanız lütfen Azure portal bir destek bileti gönderebilirsiniz.

## <a name="warm-store"></a>Sıcak mağaza

Isınma deponuzdaki veriler yalnızca [zaman serisi sorgu API 'leri](./concepts-query-overview.md), [Azure Time Series Insights TSI Explorer](./concepts-ux-panels.md)veya [Power BI Bağlayıcısı](./how-to-connect-power-bi.md)aracılığıyla kullanılabilir. Sıcak mağaza sorguları ücretsizdir ve kota yoktur, ancak 30 eşzamanlı istek [sınırı](/rest/api/time-series-insights/reference-api-limits#query-apis---limits) vardır.

### <a name="warm-store-behavior"></a>Sıcak mağaza davranışı

* Etkinleştirildiğinde, ortamınızda akan tüm veriler, etkinlik zaman damgasına bakılmaksızın ısınma deponuza yönlendirilir. Akış alma işlem hattının neredeyse gerçek zamanlı akış için oluşturulup oluşturulmadığını ve geçmiş olayların [desteklenmediğini](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion)unutmayın.
* Saklama dönemi, olay zaman damgasında değil, etkinliğin ısınma deposunda dizine alındığı zamana göre hesaplanır. Bu, olay zaman damgası ileride olsa bile, saklama süresi dolduktan sonra, verilerin artık ısınma deposunda kullanılamadığı anlamına gelir.
  * Örnek: 10 günlük hava durumu tahminlerine sahip bir olay, 7 günlük saklama süresiyle yapılandırılmış bir sıcak depolama kapsayıcısında alınır ve dizinlenir. Yedi gün sonra, tahmine daha fazla yarı mağaza 'da erişilemez, ancak soğuk bir şekilde sorgulanabilir.
* En yeni verileri soğuk depolamada dizinli olan mevcut bir ortamda yarı depolamayı etkinleştirirseniz, ısınma mağazalarınızın bu verilerle doldurulmadığını unutmayın.
* Yalnızca yarı depolamayı etkinleştirdiyseniz ve en son verilerinizi gezgin 'de görüntülerken sorunlarla karşılaşıyorsanız, geçici olarak yarı mağaza sorgularını kapalı bırakabilirsiniz:

   [![Sıcak sorguları devre dışı bırak](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>Soğuk depo

Bu bölümde, Azure Time Series Insights Gen2 ile ilgili Azure depolama ayrıntıları açıklanmaktadır.

Azure Blob depolama alanının kapsamlı bir açıklaması için, [depolama Blobları giriş](../storage/blobs/storage-blobs-introduction.md)' i okuyun.

### <a name="your-cold-storage-account"></a>Soğuk depolama hesabınız

Azure Time Series Insights Gen2, Azure Depolama hesabınızdaki her bir olayın en fazla iki kopyasını tutar. Bir kopya alma zamanına göre sıralanmış olayları depolar, her zaman bir zaman sıralı dizideki olaylara erişime izin verir. Zaman içinde, Azure Time Series Insights Gen2, performans sorguları için iyileştirmek üzere verilerin yeniden bölümlendirilmiş bir kopyasını da oluşturur.

Tüm verileriniz, Azure depolama hesabınızda süresiz olarak depolanır.

> [!WARNING]
> Time Series Insights tarafından kullanılan bir hub veya olay kaynağına yönelik genel Internet erişimini kısıtlamayın veya gerekli bağlantı bozulur.

#### <a name="writing-and-editing-blobs"></a>Blob yazma ve düzenleniyor

Sorgu performansı ve veri kullanılabilirliği sağlamak için, Azure Time Series Insights Gen2 tarafından oluşturulan blob 'ları düzenlemeyin veya silmeyin.

#### <a name="accessing-cold-store-data"></a>Soğuk mağaza verilerine erişme

[Azure Time Series Insights Gezgini](./concepts-ux-panels.md) ve [zaman serisi sorgu API 'lerinde](./concepts-query-overview.md)verilerinize erişmenin yanı sıra, verilerinize doğrudan soğuk deposunda depolanan Parquet dosyalarından de erişmek isteyebilirsiniz. Örneğin, bir Jupyter not defterindeki verileri okuyabilir, dönüştürebilir ve temizleyebilir, ardından Azure Machine Learning modelinizi aynı Spark iş akışında eğitebilmeniz için bunu kullanabilirsiniz.

Verilere doğrudan Azure Storage hesabınızdan erişmek için, Azure Time Series Insights Gen2 verilerinizi depolamak için kullanılan hesaba okuma erişiminizin olması gerekir. Daha sonra seçili verileri, aşağıda açıklanan Parquet dosyasının oluşturulma zamanına göre, `PT=Time` [Parquet dosya biçimi](#parquet-file-format-and-folder-structure) bölümünde bulabilirsiniz.  Depolama Hesabınıza yönelik okuma erişiminin etkinleştirilmesi hakkında daha fazla bilgi için bkz. [depolama hesabı kaynaklarınıza erişimi yönetme](../storage/blobs/anonymous-read-access-configure.md).

#### <a name="data-deletion"></a>Veri silme

Azure Time Series Insights Gen2 dosyalarınızı silmeyin. Yalnızca Azure Time Series Insights Gen2 içinden ilgili verileri yönetin.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet dosya biçimi ve klasör yapısı

Parquet, verimli depolama ve performans için tasarlanan açık kaynaklı bir sütunlu dosya biçimidir. Azure Time Series Insights Gen2, ölçekte zaman serisi KIMLIK tabanlı sorgu performansını etkinleştirmek için Parquet kullanır.

Parquet dosya türü hakkında daha fazla bilgi için, [Parquet belgelerini](https://parquet.apache.org/documentation/latest/)okuyun.

Azure Time Series Insights Gen2, verilerinizin kopyalarını aşağıdaki gibi depolar:

* Birincisi, ilk kopya alma zamanına göre bölümlendirilir ve verileri kabaca gelişme sırasıyla depolar. Bu veriler şu klasörde bulunur `PT=Time` :

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* İkinci, yeniden bölümlenmiş kopya zaman serisi kimliklerine göre gruplandırılır ve `PT=TsId` klasöründe bulunur:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

Klasördeki blob adlarındaki zaman damgası, `PT=Time` olayların zaman damgasına değil, Azure Time Series Insights Gen2 için varış zamanına karşılık gelir.

`PT=TsId`Klasördeki veriler, zaman içinde sorgu için iyileştirilebilir ve statik değildir. Yeniden bölümleme sırasında bazı olaylar birden fazla blobda bulunabilir. Bu klasördeki Blobların adlandırılmasının sağlanması garanti edilmez.

Genel olarak, Parquet dosyaları aracılığıyla verilere doğrudan erişmeniz gerekiyorsa `PT=Time` klasörünü kullanın.  Gelecekteki işlevler, klasöre verimli erişim sağlayacak `PT=TsId` .

> [!NOTE]
>
> * `<YYYY>` dört basamaklı bir yıl gösterimine eşlenir.
> * `<MM>` iki basamaklı bir ay gösterimiyle eşlenir.
> * `<YYYYMMDDHHMMSSfff>` dört basamaklı yıl ( `YYYY` ), iki basamaklı ay (), iki basamaklı gün (), iki basamaklı `MM` `DD` saat ( `HH` ), iki basamaklı dakika ( `MM` ), iki basamaklı saniye ( `SS` ) ve üç basamaklı milisaniyelik ( `fff` ) ile zaman damgası gösterimine eşler.

Azure Time Series Insights Gen2 olayları, aşağıdaki gibi, Parquet dosya içeriklerine eşlenir:

* Her olay tek bir satırla eşlenir.
* Her satır, bir olay zaman damgasıyla **zaman damgası** sütunu içerir. Zaman damgası özelliği hiçbir zaman null değildir. Olay kaynağında zaman damgası özelliği belirtilmemişse, varsayılan olarak **olay sıraya alınan zaman** . Depolanan zaman damgası her zaman UTC olarak kullanılır.
* Her satır, Azure Time Series Insights Gen2 ortamı oluşturulduğunda tanımlanan zaman serisi KIMLIĞI (TSıD) sütununu içerir. TSıD Özellik adı, soneki içerir `_string` .
* Telemetri verileri olarak gönderilen diğer tüm özellikler, `_bool` özellik türüne bağlı olarak, (Boolean), `_datetime` (zaman damgası), `_long` (Long), ( `_double` Double), `_string` (dize) veya `dynamic` (dinamik) ile biten sütun adlarıyla eşlenir.  Daha fazla bilgi için [desteklenen veri türleri](./concepts-supported-data-types.md)hakkında bilgi edinin.
* Bu eşleme şeması, **V = 1** olarak başvurulan ve aynı ada sahip temel klasörde depolanan dosya biçiminin ilk sürümü için geçerlidir. Bu özellik geliştikçe, bu eşleme şeması değişebilir ve başvuru adı artar.

## <a name="next-steps"></a>Sonraki adımlar

* [Veri modelleme](./concepts-model-overview.md)hakkında bilgi edinin.

* [Azure Time Series Insights Gen2 ortamınızı](./how-to-plan-your-environment.md)planlayın.
