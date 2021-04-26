---
title: Azure Data Factory 'da işlem hattı düzenleme ve Tetikleyicileri sorunlarını giderme
description: Azure Data Factory içinde işlem hattı tetikleme sorunlarını gidermek için farklı yöntemler kullanın.
author: ssabat
ms.service: data-factory
ms.date: 04/01/2021
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 49205025e26f7c0eb609638e70a58c9c0c14748e
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385420"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Azure Data Factory 'da işlem hattı düzenleme ve Tetikleyicileri sorunlarını giderme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory'de işlem hattı çalıştırması, bir işlem hattı yürütme örneğini tanımlar. Örneğin, 8:00, 9:00 ve 10:00 ' de çalışan bir işlem hattınızı varsayalım. Bu durumda, üç ayrı işlem hattı çalıştırması vardır. Her işlem hattı çalıştırması benzersiz bir işlem hattı çalıştırma kimliğine sahiptir. Çalıştırma KIMLIĞI, belirli bir işlem hattının çalıştırılacağını tanımlayan bir genel benzersiz tanımlayıcıdır (GUID).

İşlem hattı çalıştırmaları örneği genelde bağımsız değişkenlerin işlem hatlarında tanımladığınız parametrelere iletilmesiyle oluşturulur. Bir işlem hattını el ile veya bir tetikleyici kullanarak çalıştırabilirsiniz. Ayrıntılar için bkz. işlem [hattı yürütme ve tetikleyiciler Azure Data Factory](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Yaygın sorunlar, nedenler ve çözümler

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Azure Işlevleri uygulama işlem hattı, Özel uç nokta bağlantısı ile ilgili bir hata oluşturur
 
Data Factory ve özel bir uç noktada çalışan bir Azure işlev uygulamanız var. İşlev uygulamasıyla etkileşime giden bir işlem hattı çalıştırmaya çalışıyorsunuz. Üç farklı yöntem denediniz, ancak biri "Hatalı Istek" hatası döndürüyor ve diğer iki yöntem "103 hatası yasak" döndürüyor.

**Neden**

Data Factory Şu anda işlev uygulamaları için özel bir uç nokta bağlayıcısını desteklemez. Azure Işlevleri yalnızca özel bir bağlantıdan gelen bağlantılara izin verecek şekilde yapılandırıldığından çağrıları reddeder.

**Çözünürlük**

Bir **Privatelinkservice** uç noktası oluşturun ve Işlev uygulamanızın DNS ' i belirtin.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>İşlem hattı çalıştırması iptal edildi ancak izleyici hala ilerleme durumunu gösteriyor

**Neden**

İşlem hattı çalıştırmasını iptal ettiğinizde, ardışık düzen izlemesi genellikle ilerleme durumunu gösterir. Bu, bir tarayıcı önbelleği sorunu nedeniyle oluşur. Ayrıca, doğru izleme filtrelerine sahip olmayabilirsiniz.

**Çözünürlük**

Tarayıcıyı yenileyin ve doğru izleme filtrelerini uygulayın.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>İşlem hattını kopyalarken bir "Delimitedtextmorecolumnsıısdefined" hatası görürsünüz
 
 **Neden**
 
Kopyaladığınız bir klasör, değişken sayısı, farklı sınırlayıcılar, quote char ayarları veya bazı veri sorunları gibi farklı şemalarla dosya içeriyorsa Data Factory işlem hattı bu hatayı oluşturabilir:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Çözünürlük**

Kopyalama etkinliği oluştururken **Ikili kopya** seçeneğini belirleyin. Bu şekilde, toplu kopyalar veya verilerinizi bir Data Lake 'ten diğerine geçirmek için Data Factory şemayı okumak için dosyaları açmaz. Bunun yerine, Data Factory her dosyayı ikili olarak değerlendirir ve diğer konuma kopyalar.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime-for-data-flow"></a>Veri akışı için tümleştirme çalışma zamanının kapasite sınırına ulaştığınızda işlem hattı çalıştırması başarısız olur

**Sorun**

Hata iletisi:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**Neden**

Tümleştirme çalışma zamanının kapasite sınırına ulaştınız. Aynı tümleştirme çalışma zamanını aynı anda kullanarak büyük miktarda veri akışı çalıştırıyor olabilirsiniz. Ayrıntılar için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2) .

**Çözünürlük**
 
- İşlem hatlarınızı farklı tetikleyici saatlerinde çalıştırın.
- Yeni bir tümleştirme çalışma zamanı oluşturun ve işlem hatlarınızı birden çok tümleştirme çalışma zamanına ayırın.

### <a name="a-pipeline-run-error-while-invoking-rest-api-in-a-web-activity"></a>Web etkinliğinde REST API çağrılırken işlem hattı çalıştırma hatası

**Sorun**

Hata iletisi:

`
Operation on target Cancel failed: {“error”:{“code”:”AuthorizationFailed”,”message”:”The client ‘<client>’ with object id ‘<object>’ does not have authorization to perform action ‘Microsoft.DataFactory/factories/pipelineruns/cancel/action’ over scope ‘/subscriptions/<subscription>/resourceGroups/<resource group>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelineruns/<pipeline run id>’ or the scope is invalid. If access was recently granted, please refresh your credentials.”}}
`

**Neden**

İşlem hatları, yalnızca Azure Data Factory üyesine katkıda bulunan rolü atanırsa ADF REST API yöntemlerini çağırmak için Web etkinliğini kullanabilir. Önce Azure Data Factory yönetilen kimliği, katkıda bulunan güvenlik rolüne Ekle ' ye yapılandırmalısınız. 

**Çözünürlük**

Azure Data Factory REST API bir Web etkinliğinin ayarlar sekmesinde kullanılmadan önce, güvenliğin yapılandırılması gerekir. Azure Data Factory işlem hatları, yalnızca Azure Data Factory yönetilen kimliğe *katkıda*  bulunan rolü atanırsa ADF REST API yöntemlerini çağırmak için Web etkinliğini kullanabilir. Azure portal açarak ve Sol menüdeki **tüm kaynaklar** bağlantısına tıklayarak başlayın. *Rol ataması Ekle* kutusunda **Ekle** düğmesine tıklayarak, KATKıDA bulunan rolü olan ADF yönetilen kimliği eklemek için **Azure Data Factory** ' ı seçin.


### <a name="how-to-check-and-branch-on-activity-level-success-and-failure-in-pipelines"></a>İşlem hatlarında etkinlik düzeyindeki başarıyı ve başarısızlığı denetleme ve dallandırma

**Neden**

Azure Data Factory düzenleme koşullu mantığa izin verir ve kullanıcıların önceki bir etkinliğin sonucuna göre farklı yollar almasına olanak tanır. Dört koşullu yol sağlar: **başarılı** olduğunda (varsayılan geçiş), **hatadan** sonra, **tamamlandığında ve atlamada**.  

Azure Data Factory tüm yaprak düzeyindeki etkinliklerin sonucunu değerlendirir. İşlem hattı sonuçları yalnızca tümü başarılı olursa başarılı olur. Bir yaprak etkinlik atlandıysa bunun yerine üst etkinliğini değerlendiririz. 

**Çözünürlük**

* İşlem [hattı hatalarının ve hatalarının nasıl işleneceğini](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)izleyerek etkinlik düzeyi denetimleri uygulayın.
* İşlem hatlarını [fabrika tarafından sorgu](/rest/api/datafactory/pipelineruns/querybyfactory)izleyen düzenli aralıklarla izlemek için Azure Logic Apps kullanın.
* [Görsel olarak Izleme işlem hattı](./monitor-visually.md)

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>Düzenli aralıklarla işlem hattı başarısızlıklarını izleme

**Neden**

Başarısız Data Factory işlem hatlarını dakikalar içinde izlemeniz, 5 dakika söylemeniz gerekebilir. Uç noktasını kullanarak bir veri fabrikasında işlem hattı çalıştırmalarını sorgulayabilir ve filtreleyebilirsiniz. 

**Çözünürlük**
* Bir Azure mantıksal uygulamasını, [fabrika tarafından sorgu](/rest/api/datafactory/pipelineruns/querybyfactory)bölümünde açıklandığı gibi, 5 dakikada bir tüm başarısız işlem hatlarını sorgulamak için ayarlayabilirsiniz. Daha sonra, olayları bilet sistemine bildirebilirsiniz.
* [Görsel olarak Izleme işlem hattı](./monitor-visually.md)

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>Paralellik artışının derecesi daha yüksek verimlilik elde etmez

**Neden** 

*Foreach* cinsinden paralellik derecesi, gerçekte paralellik derecesini belirtir. Aynı anda belirli sayıda yürütme garantisi vermeyiz, ancak bu parametre ayarlanmış olan değerin üzerine hiçbir zaman geçeceğimizi garanti eder. Kaynaklarınızın ve havuzlar için eşzamanlı erişimi denetlerken yararlanılabilir olması için bunu bir sınır olarak görmeniz gerekir.

*Foreach* hakkında bilinen olgular
 * Foreach, varsayılan değer 20 ve Max 50 olan Batch Count (n) adlı bir özelliğe sahiptir.
 * Toplu iş sayısı n, n kuyruk oluşturmak için kullanılır. 
 * Her sıra sırayla çalışır, ancak paralel olarak çalışan birkaç kuyruğa sahip olabilirsiniz.
 * Kuyruklar önceden oluşturulur. Bu, çalışma zamanı sırasında kuyrukların yeniden dengelenmesi gerekmediği anlamına gelir.
 * Her zaman, kuyruk başına en çok bir öğe işlem yapmış olursunuz. Bu, belirli bir zamanda en fazla n öğe işlenen anlamına gelir.
 * Foreach toplam işlem süresi, en uzun kuyruğun işleme zamanına eşittir. Bu, Foreach etkinliğinin kuyrukların nasıl oluşturulduğuna bağlı olduğu anlamına gelir.
 
**Çözünürlük**

 * *Her biri* paralel olarak çalıştırılan her bir Için *SetVariable* etkinliğini kullanmamalısınız.
 * Kuyrukların oluşturulduğu şekilde göz önünde bulundurarak, müşteri, her *foreach* 'in benzer işleme süresine sahip olduğu *foreach* ' i ayarlayarak foreach performansını iyileştirebilir. 
 * Bu, uzun çalıştırmanın ardışık olarak değil paralel olarak işlenmesini güvence altına alacak.

 ### <a name="pipeline-status-is-queued-or-stuck-for-a-long-time"></a>İşlem hattı durumu kuyruğa alındı veya uzun bir süre takıldı
 
 **Neden**
 
 Bu durum eşzamanlılık sınırlarını, hizmet kesintilerini, ağ başarısızlıklarını vb. vurmaları gibi çeşitli nedenlerden kaynaklanabilir.
 
 **Çözünürlük**
 
* Eşzamanlılık sınırı: işlem hattınızda bir eşzamanlılık ilkesi varsa, devam eden eski bir işlem hattı çalıştırması olmadığını doğrulayın. Azure Data Factory izin verilen en fazla ardışık düzen eşzamanlılık 10 işlem hatdır. 
* Sınırları izleme: ADF yazma tuvaline gidin, işlem hattınızı seçin ve kendisine atanmış bir eşzamanlılık özelliği olup olmadığını belirleyin. Varsa, İzleme görünümüne gidin ve son 45 gün içinde devam eden hiçbir şey olmadığından emin olun. Devam eden bir sorun varsa iptal edebilir ve yeni işlem hattı çalışmasının başlaması gerekir.
* Geçici sorunlar: çalıştırmalarınızın geçici bir ağ sorunundan, kimlik bilgisi hatalarından, hizmet kesintilerine göre etkilenmesi mümkündür.  Bu durumda Azure Data Factory, tüm çalıştırmaları izleyen ve bir sorun olduğunu fark edildiğinde Başlatan bir iç kurtarma işlemine sahiptir. Bu işlem bir saat sonra gerçekleşir, bu nedenle çalıştırma bir saatten uzun bir süre boyunca takılırsa bir destek talebi oluşturun.
 
### <a name="longer-start-up-times-for-activities-in-adf-copy-and-data-flow"></a>ADF kopyalama ve veri akışı etkinliklerinin daha uzun süre başlangıç zamanı

**Neden**

Bu durum, veri akışı veya iyileştirilmiş KııR için yaşam süresi özelliğini uygulamadıysanız oluşabilir.

**Çözünürlük**

* Her kopyalama etkinliğinin başlaması 2 dakika kadar sürüyorsa ve bu sorun birincil olarak bir sanal ağ birleştirmede ( Azure IR yerine) oluşuyorsa, bu bir kopyalama performansı sorunu olabilir. Sorun giderme adımlarını gözden geçirmek için, [kopyalama performansı geliştirmesi](./copy-activity-performance-troubleshooting.md) ' ne gidin.
* Veri akışı etkinlikleri için küme başlangıç süresini azaltmak üzere yaşam süresi özelliğini kullanabilirsiniz. Lütfen [veri akışı Integration Runtime](./control-flow-execute-data-flow-activity.md#data-flow-integration-runtime) gözden geçirin.

 ### <a name="hitting-capacity-issues-in-shirself-hosted-integration-runtime"></a>Shar (Şirket içinde barındırılan Integration Runtime) içindeki kapasite sorunlarını vurun
 
 **Neden**
 
Bu durum, iş yükünüze göre kır ölçeğini ölçeklendirmezseniz meydana gelebilir.

**Çözünürlük**

* SHıR 'den bir kapasite sorunuyla karşılaşırsanız, etkinlikleri dengelemek için düğümü yükseltmek üzere VM 'yi yükseltin. Şirket içinde barındırılan bir IR genel hatası veya hatası, kendinden konak IR yükseltmesi veya şirket içinde barındırılan IR bağlantı sorunları hakkında bir hata iletisi alırsanız, [Şirket içinde barındırılan tümleştirme çalışma zamanına sorun giderme](./self-hosted-integration-runtime-troubleshoot-guide.md) bölümüne gidin.

### <a name="error-messages-due-to-long-queues-for-adf-copy-and-data-flow"></a>ADF kopyalama ve veri akışı için uzun kuyruklardan kaynaklanan hata iletileri

**Neden**

Uzun kuyruk ile ilgili hata iletileri çeşitli nedenlerle görünebilir. 

**Çözünürlük**
* Bağlayıcılar aracılığıyla herhangi bir kaynaktan veya hedefle bir hata iletisi alırsanız, uzun bir kuyruk üretebilir, [bağlayıcı sorun giderme kılavuzu](./connector-troubleshoot-guide.md) ' na gidin.
* Veri akışı eşleme hakkında uzun bir sıra oluşturabilen bir hata iletisi alırsanız, [veri akışları sorun giderme kılavuzu](./data-flow-troubleshoot-guide.md) ' na gidin.
* Databricks, özel etkinlikler veya HDI gibi diğer etkinliklerle ilgili, uzun bir sıra oluşturabilen bir hata iletisi alırsanız, [etkinlik sorun giderme kılavuzu](./data-factory-troubleshoot-guide.md) ' na gidin.
* Uzun bir sıra oluşturabilen SSIS paketlerini çalıştırmaya yönelik bir hata iletisi alırsanız, [Azure-SSIS paketi yürütme sorun giderme kılavuzu](./ssis-integration-runtime-ssis-activity-faq.md) ve [Integration Runtime yönetimi sorun giderme kılavuzu](./ssis-integration-runtime-management-troubleshoot.md) ' na gidin.


## <a name="next-steps"></a>Sonraki adımlar

Daha fazla sorun giderme Yardım için şu kaynakları deneyin:

*  [Data Factory blogu](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory Özellik istekleri](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videoları](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Soru sayfası Microsoft Q&](/answers/topics/azure-data-factory.html)
*  [Data Factory hakkında Twitter bilgileri](https://twitter.com/hashtag/DataFactory)
