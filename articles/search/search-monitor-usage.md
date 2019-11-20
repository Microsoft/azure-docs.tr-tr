---
title: Kaynak kullanımını ve sorgu ölçümlerini izleme
titleSuffix: Azure Cognitive Search
description: Günlüğe kaydetmeyi etkinleştirin, sorgu etkinliği ölçümlerini, kaynak kullanımını ve Azure Bilişsel Arama hizmetinden diğer sistem verilerini alın.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c4b8b03394eee6dffb79b0e40a22dd49880dee88
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793497"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-cognitive-search"></a>Azure Bilişsel Arama kaynak tüketimini ve sorgu etkinliğini izleme

Azure Bilişsel Arama hizmetinizin Genel Bakış sayfasında, kaynak kullanımı ve sorgu ölçümleri hakkındaki sistem verilerini ve daha fazla dizin, Dizin Oluşturucu ve veri kaynağı oluşturmak için ne kadar kota kullanılabildiğini görüntüleyebilirsiniz. Ayrıca, Log Analytics 'i veya kalıcı veri toplama için kullanılan başka bir kaynağı yapılandırmak üzere portalını kullanabilirsiniz. 

Günlükleri ayarlama, kendi kendine tanılama ve işletimsel geçmişi koruma için kullanışlıdır. Dahili olarak, bir destek bileti verirseniz araştırma ve Analize yetecek kadar, günlük arka uçta kısa bir süre boyunca Günlükler bulunur. Denetim ve günlük bilgilerine erişim istiyorsanız, bu makalede açıklanan çözümlerden birini ayarlamanız gerekir.

Bu makalede, izleme seçenekleriniz, günlüğe kaydetme ve günlük depolamayı etkinleştirme ve günlük içeriğinin nasıl görüntüleneceği hakkında bilgi edinin.

## <a name="metrics-at-a-glance"></a>Bir bakışta ölçümler

Genel Bakış sayfasında yerleşik olarak bulunan ve kaynak tüketimine ve sorgu yürütme ölçümlerinde bulunan **kullanım** ve **izleme** bölümleri. Bu bilgiler, hizmeti kullanmaya başladığınızda yapılandırma gerekmeden kullanılabilir hale gelir. Bu sayfa birkaç dakikada bir yenilenir. [Üretim iş yükleri için hangi katmanın kullanılacağı](search-sku-tier.md)veya [etkin kopyaların ve bölümlerin sayısının ayarlanmayacağı](search-capacity-planning.md)hakkında kararlar alırsanız, bu ölçümler, kaynakların ne kadar hızlı tüketiğini göstererek bu kararlara yardımcı olabilir ve geçerli yapılandırmanın mevcut yükü ne kadar iyi işlediğini.

**Kullanım** sekmesi, geçerli [limitlere](search-limits-quotas-capacity.md)göre kaynak kullanılabilirliği gösterir. Aşağıdaki çizim, her bir ve 50 MB depolama alanının 3 nesnesine göre oluşan ücretsiz hizmet içindir. Temel veya standart bir hizmetin sınırları daha yüksektir ve bölüm sayılarını artırırsanız, en fazla depolama alanı orantılı bir şekilde değişir.

![Etkin sınırlara göre kullanım durumu](./media/search-monitor-usage/usage-tab.png
 "Etkin sınırlara göre kullanım durumu")

## <a name="queries-per-second-qps-and-other-metrics"></a>Saniyedeki sorgu sayısı (QPS) ve diğer ölçümler

**İzleme** sekmesi, dakikada toplanan arama *sorguları* (QPS) gibi ölçümler için hareketli ortalamaları gösterir. 
*Arama gecikmesi* , arama hizmetinin dakika başına toplanan arama sorgularını işlemesi için ihtiyaç duyduğu süredir. *Kısıtlanmış arama sorguları yüzdesi* (gösterilmez), her dakika toplanmış olan arama sorgularının yüzdesidir.

Bu sayılar yaklaşık değerlerdir ve sisteminizin istekleri ne kadar iyi bir şekilde sunmakta olduğu konusunda genel bir fikir sunacak şekilde tasarlanmıştır. Gerçek QPS, portalda raporlanan sayıdan daha yüksek veya daha düşük olabilir.

![Saniye başına sorgu etkinliği](./media/search-monitor-usage/monitoring-tab.png "Saniye başına sorgu etkinliği")

## <a name="activity-logs"></a>Etkinlik günlükleri

**Etkinlik günlüğü** Azure Resource Manager bilgi toplar. Etkinlik günlüğünde bulunan bilgilere örnek olarak bir hizmet oluşturma veya silme, kaynak grubunu güncelleştirme, ad kullanılabilirliğini denetleme veya bir isteği işlemek için hizmet erişim anahtarı alma sayılabilir. 

**Etkinlik günlüğüne** sol gezinti bölmesinden veya en üstteki pencere Komut çubuğundaki bildirimlerden veya **sorunları Tanıla ve çöz** sayfasından erişebilirsiniz.

Bir dizin oluşturma veya bir veri kaynağını silme gibi hizmet içi görevlerde, her istek için "yönetici anahtarı al" gibi genel bildirimler görürsünüz, ancak söz konusu eylemin kendisi uygulanmaz. Bu bilgi düzeyi için bir eklenti izleme çözümünü etkinleştirmeniz gerekir.

## <a name="add-on-monitoring-solutions"></a>Eklenti izleme çözümleri

Azure Bilişsel Arama, yönettiği nesnelerden daha fazla veri depolamaz, bu da günlük verilerinin dışarıdan depolanması gerektiği anlamına gelir. Günlük verilerini kalıcı hale getirmek istiyorsanız aşağıdaki kaynakları yapılandırabilirsiniz. 

Aşağıdaki tabloda, günlükleri depolama ve Application Insights aracılığıyla hizmet işlemlerinin ve sorgu iş yüklerinin derinlemesine izlenmesini ekleme seçenekleri karşılaştırılmaktadır.

| Kaynak | Kullanıldığı yerler |
|----------|----------|
| [Azure İzleyici günlükleri](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Günlüğe kaydedilen olaylar ve sorgu ölçümleri aşağıdaki şemalara göre yapılır. Olaylar Log Analytics çalışma alanına kaydedilir. Günlükte ayrıntılı bilgi döndürmek için sorguları bir çalışma alanına karşı çalıştırabilirsiniz. Daha fazla bilgi için bkz. [Azure izleyici günlükleri ile çalışmaya başlama](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Blob depolama](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Günlüğe kaydedilen olaylar ve sorgu ölçümleri aşağıdaki şemalara göre yapılır. Olaylar bir blob kapsayıcısına kaydedilir ve JSON dosyalarında depolanır. Dosya içeriğini görüntülemek için bir JSON düzenleyicisi kullanın.|
| [Olay Hub’ı](https://docs.microsoft.com/azure/event-hubs/) | Günlüğe kaydedilen olaylar ve sorgu ölçümleri, bu makalede belgelenen şemaları temel alır. Çok büyük Günlükler için bunu alternatif bir veri toplama hizmeti olarak seçin. |

Azure aboneliklerinizin kullanım ömrü boyunca ücretsiz olarak deneyebilmeniz için Azure Izleyici günlükleri ve BLOB depolama alanı ücretsiz bir hizmet olarak kullanılabilir. Application Insights, uygulama veri boyutu belirli limitlerin altında olduğu sürece, kaydolmak ve kullanmak ücretsizdir (Ayrıntılar için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/monitor/) bakın).

Sonraki bölümde, Azure Bilişsel Arama işlemleri tarafından oluşturulan günlük verilerini toplamak ve erişmek için Azure Blob Storage 'ı etkinleştirme ve kullanma adımlarında adım adım gösterilmektedir.

## <a name="enable-logging"></a>Günlü kaydını etkinleştir

Dizin oluşturma ve sorgu iş yükleri için günlüğe kaydetme varsayılan olarak kapalıdır ve hem günlük altyapısı hem de uzun vadeli dış depolama için eklenti çözümlerine bağımlıdır. Yalnızca Azure Bilişsel Arama kalıcı olan veriler oluşturduğu ve yönettiği nesnelerdir, bu nedenle günlüklerin başka bir yerde depolanması gerekir.

Bu bölümde, günlüğe kaydedilen olayları ve ölçüm verilerini depolamak için blob depolamayı nasıl kullanacağınızı öğreneceksiniz.

1. Henüz yoksa [bir depolama hesabı oluşturun](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) . Bu alıştırmada kullanılan tüm kaynakları silmek istiyorsanız, daha sonra temizlemeyi basitleştirmek için Azure Bilişsel Arama ile aynı kaynak grubuna yerleştirebilirsiniz.

   Depolama hesabınızın Azure Bilişsel Arama ile aynı bölgede bulunması gerekir.

2. Arama hizmeti genel bakış sayfasını açın. Sol gezinti bölmesinde **izleme** ' ye kaydırın ve **izlemeyi etkinleştir**' e tıklayın.

   ![İzlemeyi etkinleştir](./media/search-monitor-usage/enable-monitoring.png "İzlemeyi etkinleştirme")

3. Dışarı aktarmak istediğiniz verileri seçin: Günlükler, ölçümler veya her ikisi. Bir depolama hesabına kopyalayabilir, bunu bir olay hub 'ına gönderebilir veya Azure Izleyici günlüklerine aktarabilirsiniz.

   Blob depolamaya Arşiv için yalnızca depolama hesabının mevcut olması gerekir. Kapsayıcılar ve Bloblar, günlük verileri aktarıldığında gerekli olduğu gibi oluşturulur.

   ![BLOB depolama arşivini yapılandırma](./media/search-monitor-usage/configure-blob-storage-archive.png "BLOB depolama arşivini yapılandırma")

4. Profili kaydedin.

5. Nesneleri oluşturarak veya silerek (günlük olayları oluşturur) ve sorgular göndererek (ölçüm oluşturur) test günlüğü. 

Profil kaydedildikten sonra günlüğe kaydetme etkinleştirilir. Kapsayıcılar yalnızca günlüğe kaydedilecek veya ölçülecek bir etkinlik olduğunda oluşturulur. Veriler bir depolama hesabına kopyalandığında, veriler JSON olarak biçimlendirilir ve iki kapsayıcıya yerleştirilir:

* Öngörüler-Günlükler-operationlogs: arama trafiği günlükleri için
* Öngörüler-ölçümler-pt1m: ölçümler için

**Kapsayıcının blob depolamada görünmesi için bir saat sürer. Her kapsayıcı için bir blob, saat başına bir blob vardır.**

Dosyaları görüntülemek için [Visual Studio Code](#download-and-open-in-visual-studio-code) veya başka bir JSON düzenleyicisi kullanabilirsiniz. 

### <a name="example-path"></a>Örnek yol

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Günlük şeması
Arama hizmeti trafik günlüklerinizi içeren Bloblar, bu bölümde açıklandığı şekilde yapılandırılır. Her Blobun bir dizi günlük nesnesi içeren **kayıtlar** adlı bir kök nesnesi vardır. Her blob aynı saat boyunca gerçekleşen tüm işlemler için kayıtlar içerir.

| Adı | Tür | Örnek | Notlar |
| --- | --- | --- | --- |
| time |datetime |"2018-12-07T00:00:43.6872559 Z" |İşlemin zaman damgası |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>KAYNAKGRUPLARı/VARSAYıLAN/SAĞLAYıCıLAR/<br/> MICROSOFT. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |RESOURCEID |
| operationName |string |"Query. Search" |İşlemin adı |
| operationVersion |string |"2019-05-06" |Kullanılan api sürümü |
| category |string |"OperationLogs" |sabit |
| resultType |string |Başarılı |Olası değerler: başarılı veya başarısız |
| resultSignature |int |200 |HTTP sonuç kodu |
| Ort |int |50 |İşlem süresi (milisaniye) |
| properties |object |aşağıdaki tabloya bakın |İşleme özgü verileri içeren nesne |

**Özellikler şeması**

| Adı | Tür | Örnek | Notlar |
| --- | --- | --- | --- |
| Açıklama |string |"GET/Indexes (' content ')/docs" |İşlemin uç noktası |
| Sorgu |string |"? Search = AzureSearch & $count = true & api-Version = 2019-05-06" |Sorgu parametreleri |
| Belgeler |int |42 |İşlenen belge sayısı |
| indexName |string |"testındex" |İşlemle ilişkili dizinin adı |

## <a name="metrics-schema"></a>Ölçüm şeması

Ölçümler, sorgu istekleri için yakalanır.

| Adı | Tür | Örnek | Notlar |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>KAYNAKGRUPLARı/VARSAYıLAN/SAĞLAYıCıLAR/<br/>MICROSOFT. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |Kaynak KIMLIĞINIZ |
| metricName |string |Dönemlerinde |ölçümün adı |
| time |datetime |"2018-12-07T00:00:43.6872559 Z" |işlemin zaman damgası |
| Ortalama |int |64 |Ölçüm zaman aralığındaki ham örneklerin ortalama değeri |
| En düşük |int |37 |Ölçüm zaman aralığındaki ham örneklerin en küçük değeri |
| çok |int |78 |Ölçüm zaman aralığındaki ham örneklerin maksimum değeri |
| Toplamda |int |258 |Ölçüm zaman aralığındaki ham örneklerin toplam değeri |
| count |int |4 |Ölçümü oluşturmak için kullanılan ham örnek sayısı |
| zamandilimi |string |"PT1M" |ISO 8601 ' de ölçümün zaman dilimi |

Tüm ölçümler tek dakikalık aralıklarla raporlanır. Her ölçüm, dakikada minimum, maksimum ve ortalama değerleri kullanıma sunar.

SearchQueriesPerSecond ölçümü için en az, bu dakika içinde kayıtlı olan saniye başına arama sorgularının en düşük değeridir. Aynı değer en büyük değer için geçerlidir. Average, tüm dakikadaki toplamalar.
Bu senaryoyu bir dakika boyunca düşünün: en fazla SearchQueriesPerSecond için maksimum yük, ortalama yükün 58 saniye ve son olarak tek bir sorgu ile bir saniye, en düşük olan tek bir sorgudur.

Kısıtledsearchqueriespercentage, minimum, maksimum, Average ve Total için hepsi aynı değere sahiptir: bir dakika boyunca toplam arama sorgusu sayısından kısıtlanmış olan arama sorgularının yüzdesi.

## <a name="download-and-open-in-visual-studio-code"></a>Visual Studio Code indir ve aç

Günlük dosyasını görüntülemek için herhangi bir JSON düzenleyicisi kullanabilirsiniz. Hesabınız yoksa [Visual Studio Code](https://code.visualstudio.com/download)önerilir.

1. Azure portal, depolama hesabınızı açın. 

2. Sol gezinti bölmesinde **Bloblar**' a tıklayın. **Öngörüler-logs-operationlogs** ve **Insights-ölçümler-pt1m**görmeniz gerekir. Bu kapsayıcılar, günlük verileri blob depolamaya aktarıldığında Azure Bilişsel Arama tarafından oluşturulur.

3. . JSON dosyasına ulaşana kadar klasör hiyerarşisini aşağı tıklayın.  Dosyayı indirmek için bağlam menüsünü kullanın.

Dosya indirildikten sonra içeriği görüntülemek için bir JSON düzenleyicisinde açın.

## <a name="use-system-apis"></a>Sistem API 'Lerini kullanma
Hem Azure Bilişsel Arama REST API hem de .NET SDK, hizmet ölçümleri, dizin ve Dizin Oluşturucu bilgilerine ve belge sayılarına programlı erişim sağlar.

* [Hizmet Istatistiklerini al](/rest/api/searchservice/get-service-statistics)
* [Dizin Istatistiklerini al](/rest/api/searchservice/get-index-statistics)
* [Belge sayısı](/rest/api/searchservice/count-documents)
* [Dizin Oluşturucu durumunu al](/rest/api/searchservice/get-indexer-status)

PowerShell veya Azure CLı kullanımını etkinleştirmek için [buradaki](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)belgelere bakın.

## <a name="next-steps"></a>Sonraki adımlar

Hizmet yönetimi ve ayarlama Kılavuzu için [performans ve iyileştirme](search-performance-optimization.md) hakkında daha fazla bilgi için [Microsoft Azure üzerindeki arama hizmetinizi yönetin](search-manage.md) .
