---
title: Günlük verilerini topla
titleSuffix: Azure Cognitive Search
description: Tanılama ayarını etkinleştirerek günlük verilerini toplayın ve çözümleyin ve sonra sonuçları araştırmak için kusto sorgu dilini kullanın.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: f0d85f056cfaaa58fcc72eb9c2182b3e1a78affb
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581617"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Azure Bilişsel Arama günlük verilerini toplayın ve çözümleyin

Tanılama veya işletimsel Günlükler, Azure Bilişsel Arama ayrıntılı işlemlerine ilişkin öngörüler sağlar ve hizmet durumu ve işlemlerini izlemek için yararlıdır. Microsoft, bir destek bileti verirseniz araştırma ve analize yönelik olarak, sistem bilgilerini kısa bir süre (yaklaşık 30 gün) için arka uca korur. Ancak, işletimsel veriler üzerinde sahiplik istiyorsanız, günlüğe kaydetme bilgilerinin nerede toplandığını belirtmek için bir tanılama ayarı yapılandırmanız gerekir.

Tanılama günlüğü, [Azure izleyici](../azure-monitor/index.yml)ile tümleştirme aracılığıyla etkinleştirilir. 

Tanılama günlük kaydını ayarlarken, bir depolama mekanizması belirtmeniz istenir. Aşağıdaki tabloda, verileri toplama ve kalıcı hale getirme seçenekleri numaralandırılır.

| Kaynak | Kullanıldığı yerler |
|----------|----------|
| [Log Analytics çalışma alanına gönderme](../azure-monitor/essentials/tutorial-resource-logs.md) | Olaylar ve ölçümler Log Analytics çalışma alanına gönderilir ve bu, ayrıntılı bilgi döndürmek için portalda sorgulanabilir. Giriş için bkz. [Azure izleyici günlükleri ile çalışmaya başlama](../azure-monitor/logs/log-analytics-tutorial.md) |
| [BLOB depolama ile arşivleme](../storage/blobs/storage-blobs-overview.md) | Olaylar ve ölçümler bir blob kapsayıcısına arşivlenir ve JSON dosyalarında depolanır. Günlükler, belirli bir olayı araştırmak için faydalı olan ancak açık uçlu araştırma için kullanışlı olan oldukça ayrıntılı olabilir (saat/dakika). Ham günlük dosyasını görüntülemek için bir JSON düzenleyicisi kullanın veya günlük verilerini toplamak ve görselleştirmek için Power BI.|
| [Olay Hub 'ına akış](../event-hubs/index.yml) | Olaylar ve ölçümler bir Azure Event Hubs hizmetine akışla kaydedilir. Çok büyük Günlükler için bunu alternatif bir veri toplama hizmeti olarak seçin. |

## <a name="prerequisites"></a>Önkoşullar

Tanılama günlüğünü yapılandırırken bir veya daha fazla seçim yapabilmeniz için kaynakları önceden oluşturun.

+ [Log Analytics çalışma alanı oluşturma](../azure-monitor/logs/quick-create-workspace.md)

+ [Depolama hesabı oluşturma](../storage/common/storage-account-create.md)

+ [Olay Hub'ı oluşturma](../event-hubs/event-hubs-create.md)

## <a name="enable-data-collection"></a>Veri toplamayı etkinleştirme

Tanılama ayarları, günlüğe kaydedilen olayların ve ölçümlerin nasıl toplandığını belirtir.

1. **İzleme** bölümünde **Tanılama ayarları**'nı seçin.

   ![Tanılama ayarları](./media/search-monitor-usage/diagnostic-settings.png "Tanılama ayarları")

1. **+ Tanılama ayarı Ekle** ' yi seçin

1. **Log Analytics** denetleyin, çalışma alanınızı seçin ve **Operationlogs** ve **allölçümler**' i seçin.

   ![Veri toplamayı yapılandırma](./media/search-monitor-usage/configure-storage.png "Veri toplamayı yapılandırma")

1. Ayarı kaydedin.

1. Günlüğe kaydetme etkinleştirildikten sonra, günlükleri ve ölçümleri oluşturmaya başlamak için arama hizmetinizi kullanın. Günlüğe kaydedilen olaylar ve ölçümler kullanılabilir hale gelmeden önce zaman alır.

Log Analytics için, verilerin kullanılabilir olması birkaç dakika sürer ve sonrasında verileri döndürmek için kusto sorguları çalıştırabilirsiniz. Daha fazla bilgi için bkz. [sorgu Isteklerini izleme](search-monitor-logs.md).

BLOB depolama için, kapsayıcının blob depolamada görünmesi için bir saat sürer. Her kapsayıcı için bir blob, saat başına bir blob vardır. Kapsayıcılar yalnızca günlüğe kaydedilecek veya ölçülecek bir etkinlik olduğunda oluşturulur. Veriler bir depolama hesabına kopyalandığında, veriler JSON olarak biçimlendirilir ve iki kapsayıcıya yerleştirilir:

+ Öngörüler-Günlükler-operationlogs: arama trafiği günlükleri için
+ Öngörüler-ölçümler-pt1m: ölçümler için

## <a name="query-log-information"></a>Sorgu günlüğü bilgileri

İki tablo Azure Bilişsel Arama yönelik Günlükler ve ölçümler içerir: **AzureDiagnostics** ve **AzureMetrics**.

1. **İzleme** altında **Günlükler**' i seçin.

1. Sorgu penceresinde **AzureMetrics** girin. Bu tabloda toplanan verilerle tanışın almak için bu basit sorguyu çalıştırın. Ölçümleri ve değerleri görüntülemek için tablo boyunca ilerleyin. En üstteki kayıt sayısına dikkat edin ve hizmetiniz bir süredir ölçüm topmışsa, yönetilebilir bir veri kümesini almak için zaman aralığını ayarlamak isteyebilirsiniz.

   ![AzureMetrics tablosu](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics tablosu")

1. Tablosal sonuç kümesi döndürmek için aşağıdaki sorguyu girin.

   ```kusto
   AzureMetrics
   | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Daha ilginç bilgiler çıkaran daha seçmeli bir sorgu ile, tüm sütunları bilgilendirme amacıyla döndürmek için **AzureDiagnostics** ile başlayan önceki adımları yineleyin.

   ```kusto
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![AzureDiagnostics tablosu](./media/search-monitor-usage/azurediagnostics-table.png "AzureDiagnostics tablosu")

## <a name="kusto-query-examples"></a>Kusto sorgu örnekleri

Tanılama günlük kaydını etkinleştirdiyseniz, hizmetinize ve ne zaman çalıştığı bir işlem listesi için **AzureDiagnostics** sorgulama yapabilirsiniz. Ayrıca, performans değişikliklerini araştırmak için etkinlik ile bağıntı de oluşturabilirsiniz.

#### <a name="example-list-operations"></a>Örnek: listeleme işlemleri 

İşlemlerin bir listesini ve her birinin sayımını döndürün.

```kusto
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Örnek: işlemler bağıntılı

Sorgu isteğini dizin oluşturma işlemleriyle ilişkilendirin ve işlemlerin çakıştığı görmek için veri noktalarını bir zaman grafiğinde işleme koyun.

```kusto
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

## <a name="logged-operations"></a>Günlüğe kaydedilen işlemler

Azure Izleyici tarafından yakalanan günlüğe kaydedilen olaylar, dizin oluşturma ve sorgularla ilgili olanları içerir. Log Analytics içindeki **AzureDiagnostics** tablosu sorgular ve dizin oluşturma ile ilgili işletimsel verileri toplar.

| OperationName | Açıklama |
|---------------|-------------|
| ServiceStats | Bu işlem, bir portala genel bakış sayfası yüklendiğinde veya yenilendiğinde, doğrudan veya örtük olarak çağrılan [hizmet Istatistiklerini almak](/rest/api/searchservice/get-service-statistics)için bir yordam çağrıdır. |
| Query. Search |  Bir dizinde yapılan sorgu istekleri bkz. günlüğe kaydedilen sorgular hakkında bilgi için [izleyici sorguları](search-monitor-queries.md) .|
| Dizin oluşturuluyor. Dizin  | Bu işlem, [belge eklemek, güncelleştirmek veya silmek](/rest/api/searchservice/addupdate-or-delete-documents)için yapılan bir çağrıdır. |
| dizinlerde. Örneğini | Bu, verileri Içeri aktarma Sihirbazı tarafından oluşturulan bir dizindir. |
| Dizin oluşturucular. oluştur | Veri alma Sihirbazı aracılığıyla açıkça veya örtük olarak bir Dizin Oluşturucu oluşturun. |
| Indexers. Get | Dizin Oluşturucu her çalıştırıldığında bir dizin oluşturucunun adını döndürür. |
| Dizin oluşturucular. durum | Dizin Oluşturucu her çalıştırıldığında bir dizin oluşturucunun durumunu döndürür. |
| DataSources. Get | Bir Dizin Oluşturucu her çalıştırıldığında veri kaynağının adını döndürür.|
| Indexes. Get | Bir Dizin Oluşturucu çalıştırıldığında bir dizinin adını döndürür. |

## <a name="log-schema"></a>Günlük şeması

Özel raporlar oluşturuyorsanız, Azure Bilişsel Arama günlük verilerini içeren veri yapıları aşağıdaki şemayla uyumlu değildir. BLOB depolama için, her Blobun bir dizi günlük nesnesi içeren **kayıtlar** adlı bir kök nesnesi vardır. Her blob aynı saat boyunca gerçekleşen tüm işlemler için kayıtlar içerir.

Aşağıdaki tablo, kaynak günlüğü ile ortak olan alanların kısmi bir listesidir.

| Ad | Tür | Örnek | Notlar |
| --- | --- | --- | --- |
| timeGenerated |datetime |"2018-12-07T00:00:43.6872559 Z" |İşlemin zaman damgası |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>KAYNAKGRUPLARı/VARSAYıLAN/SAĞLAYıCıLAR/<br/> MICROSOFT. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |RESOURCEID |
| operationName |string |"Query. Search" |İşlemin adı |
| operationVersion |string |"2020-06-30" |Kullanılan api sürümü |
| category |string |"OperationLogs" | sabiti |
| resultType |string |Başarılı |Olası değerler: başarılı veya başarısız |
| resultSignature |int |200 |HTTP sonuç kodu |
| Ort |int |50 |İşlem süresi (milisaniye) |
| properties |object |aşağıdaki tabloya bakın |İşleme özgü verileri içeren nesne |

### <a name="properties-schema"></a>Özellikler şeması

Aşağıdaki özellikler Azure Bilişsel Arama özgüdür.

| Ad | Tür | Örnek | Notlar |
| --- | --- | --- | --- |
| Description_s |string |"GET/Indexes (' content ')/docs" |İşlemin uç noktası |
| Documents_d |int |42 |İşlenen belge sayısı |
| IndexName_s |string |"test-Dizin" |İşlemle ilişkili dizinin adı |
| Query_s |string |"? Search = AzureSearch&$count = true&api-Version = 2020-06-30" |Sorgu parametreleri |

## <a name="metrics-schema"></a>Ölçüm şeması

Ölçümler, sorgu istekleri için yakalanır ve bir dakikalık aralıklarla ölçülür. Her ölçüm, dakikada minimum, maksimum ve ortalama değerleri kullanıma sunar. Daha fazla bilgi için bkz. [sorgu Isteklerini izleme](search-monitor-queries.md).

| Ad | Tür | Örnek | Notlar |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>KAYNAKGRUPLARı/VARSAYıLAN/SAĞLAYıCıLAR/<br/>MICROSOFT. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |Kaynak KIMLIĞINIZ |
| metricName |string |Dönemlerinde |ölçümün adı |
| time |datetime |"2018-12-07T00:00:43.6872559 Z" |işlemin zaman damgası |
| ortalama |int |64 |Ölçüm zaman aralığındaki ham örneklerin ortalama değeri, ölçüye göre saniye veya yüzde cinsinden birimler. |
| minimum |int |37 |Ölçüm zaman aralığı içindeki (saniye cinsinden) ham örneklerin en küçük değeri. |
| maksimum |int |78 |Ölçüm zaman aralığı (saniye cinsinden) içindeki ham örneklerin maksimum değeri.  |
| toplam |int |258 |Ölçüm zaman aralığı içindeki ham örneklerin toplam değeri, birim (saniye cinsinden).  |
| count |int |4 |Bir düğümden günlüğe bir dakika aralığı içinde oluşturulan ölçüm sayısı.  |
| zamandilimi |string |"PT1M" |ISO 8601 ' de ölçümün zaman dilimi. |

Sorguların milisaniye cinsinden yürütülmesi yaygındır. bu nedenle, yalnızca saniyeler olarak ölçülecek sorgular QPS gibi bir ölçümde görünür.

**Saniye başına arama sorgusu** için en az, bu dakika içinde kayıtlı olan saniye başına arama sorgularının en düşük değeridir. Aynı değer en büyük değer için geçerlidir. Average, tüm dakikadaki toplamalar. Örneğin, bir dakika içinde şu şekilde bir düzene sahip olabilirsiniz: en yüksek bir ikinci yük olan SearchQueriesPerSecond için maksimum, ortalama yükün 58 saniye ve son olarak tek bir sorgu ile bir saniye, en az bir sorgu ile.

**Kısıtlanmış arama sorguları yüzdesi**, minimum, maksimum, ortalama ve toplam için aynı değer vardır: bir dakika boyunca toplam arama sorgusu sayısından kısıtlanmış olan arama sorgularının yüzdesi.

## <a name="view-raw-log-files"></a>Ham günlük dosyalarını görüntüle

BLOB depolama, günlük dosyalarını arşivlemek için kullanılır. Günlük dosyasını görüntülemek için herhangi bir JSON düzenleyicisi kullanabilirsiniz. Hesabınız yoksa [Visual Studio Code](https://code.visualstudio.com/download)önerilir.

1. Azure portal, depolama hesabınızı açın. 

2. Sol gezinti bölmesinde **Bloblar**' a tıklayın. **Öngörüler-logs-operationlogs** ve **Insights-ölçümler-pt1m** görmeniz gerekir. Bu kapsayıcılar, günlük verileri blob depolamaya aktarıldığında Azure Bilişsel Arama tarafından oluşturulur.

3. . JSON dosyasına ulaşana kadar klasör hiyerarşisini aşağı tıklayın.  Dosyayı indirmek için bağlam menüsünü kullanın.

Dosya indirildikten sonra içeriği görüntülemek için bir JSON düzenleyicisinde açın.

## <a name="next-steps"></a>Sonraki adımlar

Daha önce yapmadıysanız, daha fazla bakış özelliği hakkında bilgi edinmek için arama hizmeti izlemenin temellerini gözden geçirin.

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama işlemler ve etkinlikleri izleme](search-monitor-usage.md)