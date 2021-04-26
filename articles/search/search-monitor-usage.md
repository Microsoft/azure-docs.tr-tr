---
title: İşlemleri ve etkinlikleri izleme
titleSuffix: Azure Cognitive Search
description: Günlüğe kaydetmeyi etkinleştirin, sorgu etkinliği ölçümlerini, kaynak kullanımını ve Azure Bilişsel Arama hizmetinden diğer sistem verilerini alın.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: aa224a09317aafd49ae10c89ae0c50455ddd4602
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709931"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Azure Bilişsel Arama işlemlerini ve etkinliklerini izleme

Bu makalede, Azure Bilişsel Arama yönelik kavramların ve araçların izlenmesine ilişkin bir genel bakış sunulmaktadır. Bütünsel izleme için, Azure Izleyici gibi yerleşik işlevlerin ve eklenti hizmetlerin bir birleşimini kullanabilirsiniz.

Tamamen şunları izleyebilirsiniz:

* Hizmet: sistem durumu/kullanılabilirliği ve hizmet yapılandırmasındaki değişiklikler.
* Depolama: her bir içerik türü için, hizmet katmanı için izin verilen kotayla ilgili olarak, hem kullanılan hem de kullanılabilir sayılır.
* Sorgu etkinliği: birim, gecikme süresi ve daraltılmış ya da bırakılan sorgular. Günlüğe kaydedilen sorgu istekleri [Azure izleyicisini](#add-azure-monitor)gerektirir.
* Dizin oluşturma etkinliği: Azure Izleyici ile [tanılama günlüğü](#add-azure-monitor) gerektirir.

Arama hizmeti kullanıcı başına kimlik doğrulamasını desteklemez, bu nedenle günlüklerde kimlik bilgileri bulunamadı.

## <a name="built-in-monitoring"></a>Yerleşik izleme

Yerleşik izleme, bir arama hizmeti tarafından günlüğe kaydedilen etkinliklere başvurur. Tanılama dışında, bu izleme düzeyi için yapılandırma gerekmez.

Azure Bilişsel Arama, hizmet durumu ve sorgu ölçümlerinde raporlama için, portalda veya bu [REST API 'lerinde](#monitoring-apis)bulabileceğiniz, 30 günlük bir zamanlamaya göre dahili verileri saklar.

Aşağıdaki ekran görüntüsü Portalda izleme bilgilerini bulmanıza yardımcı olur. Hizmeti kullanmaya başladığınızda veriler kullanılabilir hale gelir. Portal sayfaları birkaç dakikada bir yenilenir.

* **İzleme** sekmesi, ana genel bakış sayfasında sorgu hacmi, gecikme süresi ve hizmetin basınç altında olup olmadığı gösterilir.
* Sol gezinti bölmesindeki **etkinlik günlüğü** Azure Resource Manager bağlıdır. Etkinlik günlüğü Kaynak Yöneticisi, hizmet kullanılabilirliği ve durumu, kapasiteye (çoğaltmalar ve bölümler) yapılan değişiklikler ve API tuşuyla ilgili etkinlikler üzerinde gerçekleştirilen eylemler hakkında rapor sağlar.
* **İzleme** ayarları, daha sonra yapılandırılabilir uyarılar, ölçüm görselleştirme ve tanılama günlükleri sağlar. İhtiyacınız olduğunda bunları oluşturun. Veriler toplandıktan ve depolanıyorsa, Öngörüler hakkındaki bilgileri sorgulayabilir veya görselleştirebilirsiniz.

  ![Arama hizmetinde Azure Izleyici tümleştirmesi](./media/search-monitor-usage/azure-monitor-search.png
 "Arama hizmetinde Azure Izleyici tümleştirmesi")

> [!NOTE]
> Portal sayfaları birkaç dakikada bir yenilendiğinden, bildirilen sayılar yaklaşık olarak, sisteminizin istekleri ne kadar iyi hizmet verdiğine ilişkin genel bir fikir vermek için tasarlanmıştır. Saniye başına sorgu (QPS) gibi gerçek ölçümler, sayfada gösterilen sayıdan daha yüksek veya daha düşük olabilir. Duyarlık bir gereksinimdir ise API 'Leri kullanmayı düşünün.

<a name="monitoring-apis"> </a>

### <a name="apis-useful-for-monitoring"></a>İzleme için yararlı API 'Ler

Portalda Izleme ve kullanım sekmelerinde bulunan bilgileri almak için aşağıdaki API 'Leri kullanabilirsiniz.

* [Hizmet Istatistiklerini al](/rest/api/searchservice/get-service-statistics)
* [Dizin Istatistiklerini al](/rest/api/searchservice/get-index-statistics)
* [Belge sayılarını al](/rest/api/searchservice/count-documents)
* [Dizin Oluşturucu durumunu al](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>Etkinlik günlükleri ve hizmet durumu

Portaldaki [**etkinlik günlüğü**](../azure-monitor/essentials/activity-log.md#view-the-activity-log) sayfası, Azure Resource Manager bilgileri ve hizmet durumunu değişikliklerle ilgili raporları toplar. Hizmet durumu ile ilgili kritik, hata ve uyarı koşulları için etkinlik günlüğü 'nü izleyebilirsiniz.

Ortak girişler, API anahtarlarına yönelik başvuruları, *yönetici anahtarı al* ve *sorgu anahtarları* al gibi genel bilgilendirme bildirimleri içerir. Bu etkinlikler, yönetici anahtarı (nesneleri oluştur veya Sil) veya sorgu anahtarı kullanılarak yapılan istekleri gösterir, ancak isteği kendisi göstermez. Bu gren hakkında bilgi edinmek için tanılama günlüğünü yapılandırmanız gerekir.

**Etkinlik günlüğüne** sol gezinti bölmesinden veya en üstteki pencere Komut çubuğundaki bildirimlerden veya **sorunları Tanıla ve çöz** sayfasından erişebilirsiniz.

### <a name="monitor-storage-in-the-usage-tab"></a>Kullanım sekmesinde depolamayı izleme

Portalda görsel izleme için **kullanım** sekmesi, hizmet katmanının uyguladığı geçerli [limitlere](search-limits-quotas-capacity.md) göre kaynak kullanılabilirliği gösterir. [Üretim iş yükleri için hangi katmanın kullanılacağı](search-sku-tier.md)veya [etkin çoğaltma ve bölüm sayısının ayarlanmayacağı](search-capacity-planning.md)hakkında kararlar alırsanız, bu ölçümler, kaynakların ne kadar hızlı bir şekilde tüketildiğini ve geçerli yapılandırmanın var olan yükü ne kadar iyi işlediğini göstererek bu kararlara yardımcı olabilir.

Aşağıdaki çizim, her bir ve 50 MB depolama alanının 3 nesnesine göre oluşan ücretsiz hizmet içindir. Temel veya standart bir hizmetin sınırları daha yüksektir ve bölüm sayılarını artırırsanız, en fazla depolama alanı orantılı bir şekilde değişir.

![Katman sınırlarına göre kullanım durumu](./media/search-monitor-usage/usage-tab.png
 "Katman sınırlarına göre kullanım durumu")

> [!NOTE]
> Depolama ile ilgili uyarılar Şu anda kullanılamıyor; depolama alanı tüketimi, Azure Izleyici 'de **AzureMetrics** tablosunda toplanmaz veya günlüğe kaydedilmez. Depolama uyarılarını almak için, kodunuzun depolama boyutunu denetlediği ve yanıtı işleyen kaynakla ilgili bildirimleri gösteren [bir özel çözüm](../azure-monitor/insights/solutions.md) oluşturmanız gerekir.

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>Azure Izleyici ile eklenti izleme

Azure Bilişsel Arama dahil olmak üzere birçok hizmet, ek uyarılar, ölçümler ve günlük Tanılama verileri için [Azure izleyici](../azure-monitor/index.yml) ile tümleşir. 

Veri toplama ve depolama üzerinde denetim istiyorsanız arama hizmeti için [Tanılama günlüğünü etkinleştirin](search-monitor-logs.md) . Azure Izleyici tarafından yakalanan günlüğe kaydedilen olaylar **AzureDiagnostics** tablosunda depolanır ve sorgular ve dizin oluşturma ile ilgili işletimsel verilerden oluşur.

Azure Izleyici çeşitli depolama seçenekleri sağlar ve tercih ettiğiniz verileri nasıl kullanacağınızı belirler:

* Power BI raporunda [günlük verilerini görselleştirmek](search-monitor-logs-powerbi.md) Istiyorsanız Azure Blob Depolama ' yı seçin.
* Verileri kusto sorguları aracılığıyla araştırmak istiyorsanız Log Analytics seçin.

Azure Izleyici kendi faturalandırma yapısına sahiptir ve bu bölümde başvurulan tanılama günlüklerinin ilişkili bir maliyeti vardır. Daha fazla bilgi için bkz. [Azure izleyici 'de kullanım ve tahmini maliyetler](../azure-monitor//usage-estimated-costs.md).

## <a name="monitor-user-access"></a>Kullanıcı erişimini izleme

Arama dizinleri daha büyük bir istemci uygulamasının bileşeni olduğundan, bir dizine Kullanıcı başına erişimi denetlemek veya izlemek için yerleşik bir yöntem yoktur. İsteklerin, yönetici ya da sorgu isteği sunan bir istemci uygulamasından geldiği varsayılır. Yönetici okuma yazma işlemleri, tüm hizmet genelinde nesneleri oluşturma, güncelleştirme, silme işlemlerini içerir. Salt okuma işlemleri, tek bir dizin kapsamına alınmış belgeler koleksiyonuna yönelik sorgulardır. 

Bu nedenle, etkinlik günlüklerinde göreceğiniz özellikler, yönetici anahtarları veya sorgu anahtarları kullanılarak yapılan çağrılara başvurudur. İlgili anahtar, istemci kodundan kaynaklanan isteklere dahildir. Hizmet, kimlik belirteçlerini veya kimliğe bürünme işlemini işleyecek şekilde donatılmış değildir.

Kullanıcı başına yetkilendirme için iş gereksinimleri mevcut olduğunda, öneri Azure Active Directory ile tümleştirilir. Kullanıcının görmemelidir belgelerinin [arama sonuçlarını kırpmak](search-security-trimming-for-azure-search-with-aad.md) için $Filter ve Kullanıcı kimliklerini kullanabilirsiniz. 

Bu bilgileri $filter parametresini içeren sorgu dizesinden ayrı olarak günlüğe kaydetme yolu yoktur. Rapor sorgu dizeleri hakkındaki ayrıntılar için bkz. [sorguları izleme](search-monitor-queries.md) .

## <a name="next-steps"></a>Sonraki adımlar

Azure Izleyici ile akıcı olma, Azure Bilişsel Arama gibi kaynaklar dahil olmak üzere tüm Azure hizmetleri hakkında daha fazla bakış için gereklidir. Azure Izleyici hakkında bilgi sahibi değilseniz kaynaklarla ilgili makaleleri gözden geçirme süresini göz atın. Öğreticilere ek olarak, aşağıdaki makale başlamak için iyi bir yerdir.

> [!div class="nextstepaction"]
> [Azure İzleyici ile Azure kaynaklarını izleme](../azure-monitor/essentials/monitor-azure-resource.md)