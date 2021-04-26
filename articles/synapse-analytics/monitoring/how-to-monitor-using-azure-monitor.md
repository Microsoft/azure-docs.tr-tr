---
title: Azure Izleyici kullanarak SYNAPSE Analytics 'i izleme
description: Azure Izleyici ölçümleri, uyarıları ve günlükleri kullanarak SYNAPSE Analytics çalışma alanınızı izlemeyi öğrenin
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: ba730adaa1e3b111586012ba1849c6dc84d6db00
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554000"
---
# <a name="use-azure-monitor-with-your-azure-synapse-analytics-workspace"></a>Azure Izleyici 'yi Azure SYNAPSE Analytics çalışma alanınız ile birlikte kullanma

Bulut uygulamaları karmaşıktır ve birçok hareketli parçaya sahiptir. İzleyiciler, uygulamalarınızın sağlıklı durumda kalmasını ve çalışmasını sağlamaya yardımcı olmak için veri sağlar. İzleyicilerin olası sorunları önlemenize ve geçmişteki sorunları gidermenize yardımcı olur. Uygulamalarınız hakkında derin Öngörüler elde etmek için izleme verilerini kullanabilirsiniz. Bu bilgi, uygulama performansını ve bakımlılığını iyileştirmenize yardımcı olur. Ayrıca, el ile müdahale gerektiren eylemleri otomatikleştirmenize de yardımcı olur.

Azure Izleyici, çoğu Azure hizmeti için temel düzeyde altyapı ölçümleri, uyarılar ve Günlükler sağlar. Azure tanılama günlükleri bir kaynak tarafından dağıtılır ve bu kaynağın çalışması hakkında zengin ve sık veriler sağlar. Azure SYNAPSE Analytics, Azure Izleyici 'de tanılama günlükleri yazabilir.

Daha fazla bilgi için bkz. [Azure izleyiciye genel bakış](../../azure-monitor/overview.md).

## <a name="metrics"></a>Ölçümler

Izleyici ile Azure iş yüklerinizin performansı ve sistem durumu hakkında görünürlük elde edebilirsiniz. En önemli Izleyici verileri türü, performans sayacı olarak da adlandırılan ölçümdür. Ölçümler çoğu Azure kaynağı tarafından yayınlanır. İzleyici, izleme ve sorun giderme amacıyla bu ölçümleri yapılandırmak ve kullanmak için çeşitli yollar sağlar.

Bu ölçümlere erişmek için [Azure izleyici veri platformundaki](../../azure-monitor/data-platform.md)yönergeleri doldurun.

### <a name="workspace-level-metrics"></a>Çalışma alanı düzeyi ölçümleri

Çalışma alanları tarafından yayılan bazı ölçümler aşağıda verilmiştir:

| **Ölçüm**                           | **Ölçüm kategorisi, görünen ad**                  | **Birim** | **Toplama türleri** | **Açıklama**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| Integrationactivityrunsended         | Tümleştirme, etkinlik çalıştırma ölçümü                     | Count    | Sum (varsayılan), sayı                | 1 dakikalık bir pencere içinde oluşan/sona erilmiş etkinlik çalıştırmalarının toplam sayısı. </br></br> Başarılı, başarısız veya Iptal edildi son durumuna göre filtrelemek için bu ölçümün sonuç boyutunu kullanın.|
| Integrationbir ardışık düzen eylemsizlik         | Tümleştirme, işlem hattı çalıştırmaları ölçümü                     | Count    | Sum (varsayılan), sayı                | 1 dakikalık bir pencere içinde oluşan/sona erilmiş işlem hattı çalıştırmalarının toplam sayısı. </br></br> Başarılı, başarısız veya Iptal edildi son durumuna göre filtrelemek için bu ölçümün sonuç boyutunu kullanın. |
| Integrationtriggerrunsended          | Tümleştirme, tetikleyici çalıştırma ölçümü                      | Count    | Sum (varsayılan), sayı                | 1 dakikalık bir pencere içinde oluşan/sona erilmiş tetikleyici çalıştırmalarının toplam sayısı. </br></br> Başarılı, başarısız veya Iptal edildi son durumuna göre filtrelemek için bu ölçümün sonuç boyutunu kullanın. |
| Builsqlpooldataprocessedbytes     | Yerleşik SQL havuzu, işlenen veri (bayt) | Bayt | Toplam (varsayılan) | Yerleşik sunucusuz SQL havuzu tarafından işlenen veri miktarı. |
| Builsqlpoollogindenemeler          | Yerleşik SQL havuzu, oturum açma girişimleri | Count | Toplam (varsayılan) | Yerleşik sunucusuz SQL havuzu için oturum açma denemesi sayısı. |
| Builsqlpooldatarequestsended      | Yerleşik SQL havuzu, sonlandırılan Istekler (bayt) | Count | Toplam (varsayılan) | Yerleşik sunucusuz SQL havuzu için sonlandırılan SQL isteklerinin sayısı. </br></br> Son duruma göre filtrelemek için bu ölçümün sonuç boyutunu kullanın. |

### <a name="dedicated-sql-pool-metrics"></a>Adanmış SQL havuzu ölçümleri

Adanmış SQL havuzları tarafından yayılan bazı ölçümler şunlardır:

| **Ölçüm**                           | **Görünen ad**                  | **Birim** | **Toplama türleri** | **Açıklama**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| DWULimit                            | DWU sınırı                       | Count   | Max (varsayılan), min, ort | SQL havuzunun yapılandırılmış boyutu |
| Kullanılan DWU                             | Kullanılan DWU                        | Count   | Max (varsayılan), min, ort | SQL havuzu genelinde kullanım üst düzey gösterimini temsil eder. DWU sınırına göre ölçülür * DWU yüzdesi |
| DWUUsedPercent                      | Kullanılan DWU yüzdesi             | Yüzde | Max (varsayılan), min, ort | SQL havuzu genelinde kullanım üst düzey gösterimini temsil eder. CPU yüzdesi ile veri GÇ yüzdesi arasındaki en yüksek değer alınarak ölçülür |
| ConnectionsBlockedByFirewall        | Güvenlik duvarı tarafından engellenen bağlantılar | Count   | Toplam (varsayılan)   | Güvenlik duvarı kuralları tarafından engellenen bağlantı sayısı. SQL havuzunuz için erişim denetim ilkelerini yeniden ziyaret edin ve sayı yüksekse bu bağlantıları izleyin |
| Uyartivecachehitpercent             | Uyarlamalı önbellek isabet yüzdesi   | Yüzde | Max (varsayılan), min, ort | İş yüklerinin Uyarlamalı önbelleğin kullanılmasıyla ne kadar iyi olduğunu ölçer. Ek kapasite için ölçekleneceğini veya önbelleği doldurma için iş yüklerini yeniden çalıştırmayı öğrenmek üzere bu ölçümü önbellek isabet yüzdesi ölçümüyle birlikte kullanın |
| Uyartivecacheusedpercent            | Uyarlamalı önbellek kullanılan yüzde  | Yüzde | Max (varsayılan), min, ort | İş yüklerinin Uyarlamalı önbelleğin kullanılmasıyla ne kadar iyi olduğunu ölçer. Bu ölçümü, ek kapasite için ölçekleneceğini veya önbelleğe almak üzere iş yüklerini yeniden çalıştırmayı belirtmek için kullanılan önbellek yüzdesi ölçümüyle kullanın |
| LocalTempDBUsedPercent              | Yerel tempdb kullanılan yüzde    | Yüzde | Max (varsayılan), min, ort | Tüm işlem düğümlerinde yerel tempdb kullanımı-değerler her beş dakikada bir dağıtılır |
| MemoryUsedPercent                   | Kullanılan bellek yüzdesi          | Yüzde | Max (varsayılan), min, ort | SQL havuzundaki tüm düğümlerde bellek kullanımı |
| Cpuyüzdesi                          | Kullanılan CPU yüzdesi             | Yüzde | Max (varsayılan), min, ort | SQL havuzundaki tüm düğümlerde CPU kullanımı |
| Bağlantılar                         | Bağlantılar                     | Count   | Toplam (varsayılan)  | SQL havuzunda Toplam oturum açma sayısı |
| ActiveQueries                      | Etkin sorgular                 | Count   | Toplam (varsayılan)   | Etkin sorgular. Bu ölçüyü filtrelenmemiş ve Böl, sistemde çalışan tüm etkin sorguları görüntüler |
| QueuedQueries                      | Kuyruğa alınmış sorgular                  | Count   | Toplam (varsayılan)   | En fazla eşzamanlılık sınırına ulaşıldıktan sonra sıraya alınan toplam istek sayısı |
| WLGActiveQueries                   | İş yükü grubu etkin sorguları   | Count   | Toplam (varsayılan)   | İş yükü grubu içindeki etkin sorgular. Bu ölçüyü filtrelenmemiş ve Böl, sistemde çalışan tüm etkin sorguları görüntüler |
| Wlgactivequerieszamanaşımları           | İş yükü grubu sorgu zaman aşımları   | Count   | Toplam (varsayılan)   | Zaman aşımına uğramış iş yükü grubu için sorgular. Bu ölçüm tarafından bildirilen sorgu zaman aşımları yalnızca sorgu yürütülmeye başladıktan sonra (kilitleme veya kaynak bekleme nedeniyle bekleme zamanı içermez) |
| WLGQueuedQueries                   | İş yükü grubu sıraya alınmış sorgular   | Count   | Toplam (varsayılan)   | En fazla eşzamanlılık sınırına ulaşıldıktan sonra sıraya alınan toplam istek sayısı |
| Wlgallocationbysystempersent        | Sistem iş yükü grubuna göre ayırma yüzdesi | Yüzde | Max (varsayılan), min, AVG, Sum | Tüm sisteme göre kaynakların yüzde tahsisi |
| WLGAllocationByEffectiveCapResourcePercent   | Maksimum kaynak yüzdesi bazında iş yükü grubu ayırması | Yüzde | Max (varsayılan), min, ort | Toplam iş yükü grubu başına düşen etkin sınır kaynağına göre kaynakların yüzde ayırmasını görüntüler. Bu ölçüm, iş yükü grubunun etkin kullanımını sağlar |
| WLGEffectiveCapResourcePercent      | Etkin uç kaynak yüzdesi  | Yüzde | Max (varsayılan), min, ort | İş yükü grubu için geçerli uç kaynak yüzdesi. Min_percentage_resource > 0 olan başka iş yükü grupları varsa effective_cap_percentage_resource orantılı bir şekilde düşürülemez |
| WLGEffectiveMinResourcePercent      | Geçerli Min kaynak yüzdesi  | Yüzde | Max (varsayılan), min, AVG, Sum | Etkin Min kaynak yüzdesi ayarı, hizmet düzeyini ve iş yükü grubu ayarlarını göz önünde bulundur, izin verildi. Etkin min_percentage_resource daha düşük hizmet düzeylerinde ayarlanabilir |

### <a name="apache-spark-pool-metrics"></a>Apache Spark havuzu ölçümleri

Apache Spark havuzlarından yayılan bazı ölçümler aşağıda verilmiştir:

| **Ölçüm**                           | **Ölçüm kategorisi, görünen ad**                  | **Birim** | **Toplama türleri** | **Açıklama**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| BigDataPoolApplicationsEnded  | Apache Spark uygulamalar sonlandırıldı  | Count | Toplam (varsayılan) | Sonlandırılan Apache Spark havuzu uygulama sayısı |
| BigDataPoolAllocatedCores     | Apache Spark havuzuna ayrılan sanal çekirdek sayısı                 | Count | Max (varsayılan), min, ort | Apache Spark havuzu için ayrılmış sanal çekirdekler |
| BigDataPoolAllocatedMemory    | Apache Spark havuzuna ayrılan bellek miktarı (GB)            | Count | Max (varsayılan), min, ort | Apache Spark havuzu için ayrılan bellek (GB) |
| BigDataPoolApplicationsActive | Etkin Apache Spark uygulamalar | Count | Max (varsayılan), min, ort | Etkin Apache Spark havuzu uygulaması sayısı |

## <a name="alerts"></a>Uyarılar

Azure Portal oturum açın ve   >  uyarı oluşturmak için **uyarıları** İzle ' yi seçin.

### <a name="create-alerts"></a>Uyarı oluşturma

1. Yeni bir uyarı oluşturmak için **+ Yeni uyarı kuralı** ' nı seçin.

1. Uyarının ne zaman tetiklenmesi gerektiğini belirtmek için **Uyarı koşulunu** tanımlayın.

    > [!NOTE]
    > **Kaynak türüne göre filtrele** aşağı açılan listesinden **Tümünü** seçtiğinizden emin olun.

1. Uyarının nasıl yapılandırılacağını daha fazla belirtmek için **uyarı ayrıntılarını** tanımlayın.

1. Uyarıları kimin alacağını (ve nasıl) belirlemek için **Eylem grubunu** tanımlayın.

## <a name="logs"></a>Günlükler

### <a name="workspace-level-logs"></a>Çalışma alanı düzeyi günlükleri

Azure SYNAPSE Analytics çalışma alanları tarafından yayılan Günlükler şunlardır:

| Log Analytics tablo adı       | Günlük kategorisi adı              | Açıklama          |
|--------------------------------|--------------------------------|----------------------|
| SynapseGatewayApiRequests      | GatewayApiRequests             | Azure SYNAPSE ağ geçidi API istekleri. |
| SynapseRbacOperations          | SynapseRbacOperations          | Azure SYNAPSE rol tabanlı erişim denetimi (SRTZB) işlemleri. |
| SynapseBuiltinSqlReqsEnded     | BuiltinSqlReqsEnded            | Azure SYNAPSE yerleşik sunucusuz SQL havuzu sona erdi istekleri. |
| SynapseIntegrationPipelineRuns | Integrationardışık düzen eylemsizlik        | Azure SYNAPSE Integration işlem hattı çalıştırmaları. |
| SynapseIntegrationActivityRuns | Integrationactivityçalıştırmaları        | Azure SYNAPSE tümleştirme etkinliği çalışır. |
| SynapseIntegrationTriggerRuns  | Integrationtriggerçalıştırmaları         | Azure SYNAPSE Tümleştirme tetikleyicisi çalışır. |

### <a name="dedicated-sql-pool-logs"></a>Adanmış SQL havuzu günlükleri

Adanmış SQL havuzları tarafından yayılan Günlükler şunlardır:

| Log Analytics tablo adı        | Günlük kategorisi adı             | Açıklama |
|----------------------|--------------------------------------|-------------|
| SynapseSqlPoolExecRequests  | ExecRequests | Azure SYNAPSE adanmış bir SQL havuzundaki SQL istekleri/sorguları hakkında bilgi.
| SynapseSqlPoolDmsWorkers    | Dmsçalışanları   | Azure SYNAPSE adanmış bir SQL havuzunda DMS adımlarını tamamlayan çalışanlar hakkında bilgiler.
| SynapseSqlPoolRequestSteps  | RequestSteps | Azure SYNAPSE adanmış bir SQL havuzunda verilen bir SQL isteği/sorgusu oluşturan istek adımları hakkında bilgiler.
| SynapseSqlPoolSqlRequests   | SqlRequests  | Azure SYNAPSE adanmış bir SQL havuzundaki SQL istekleri/sorguları adımlarının sorgu dağıtımları hakkında bilgiler.
| SynapseSqlPoolWaits         | Bekler        | Bir SQL isteği/sorgusunun bir Azure SYNAPSE adanmış SQL havuzunda yürütülmesi sırasında, kilitler ve iletim kuyruklarındaki bekleme durumları hakkında bilgi ile karşılaşıldı.

Bu Günlükler hakkında daha fazla bilgi için aşağıdaki bilgilere bakın:
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="apache-spark-pool-log"></a>Apache Spark havuz günlüğü

Apache Spark havuzları tarafından yayılan günlük aşağıda verilmiştir:

| Log Analytics tablo adı               | Günlük kategorisi adı              | Açıklama                 |
|-----------------------------|---------------------------------------|-----------------------------|
| SynapseBigDataPoolApplicationsEnded | BigDataPoolAppsEnded | Sonlandırılan Apache Spark uygulamalar hakkında bilgi |

### <a name="diagnostic-settings"></a>Tanılama ayarları

İşlem dışı kaynaklar için tanılama günlüklerini yapılandırmak üzere tanılama ayarlarını kullanın. Kaynak denetiminin ayarları aşağıdaki özelliklere sahiptir:

* Tanılama günlüklerinin nereye gönderileceğini belirtir. Azure depolama hesabı, Azure Olay Hub 'ı veya Izleme günlüklerini örnek olarak içerir.
* Hangi günlük kategorilerinin gönderileceğini belirtir.
* Her günlük kategorisinin bir depolama hesabında ne kadar süreyle tutulması gerektiğini belirtir.
* Sıfır gün saklama, günlüklerin sınırsız süreyle tutulacağı anlamına gelir. Aksi takdirde, değer 1 ile 2.147.483.647 arasında herhangi bir sayıda gün olabilir.
* Bekletme ilkeleri ayarlanır ancak günlükleri bir depolama hesabında depolamak devre dışı bırakılırsa, bekletme ilkelerinin etkisi yoktur. Örneğin, bu durum yalnızca Event Hubs veya Izleyici günlükleri seçeneklerinin seçildiği zaman gerçekleşebilir.
* Bekletme ilkeleri gün başına uygulanır. Günler arasındaki sınır, Eşgüdümlü Evrensel Saat (UTC) ile gece yarısı arasında gerçekleşir. Bir günün sonunda, bekletme ilkesinin ötesinde olan günlerdeki Günlükler silinir. Örneğin, bir güne ait bir bekletme ilkeniz varsa, bugünün başlangıcında dün olan Günlükler silinir.

Azure Izleyici Tanılama ayarları ile tanılama günlüklerini analiz için birden çok farklı hedefe yönlendirebilirsiniz.

* **Depolama hesabı**: Tanılama günlüklerinizi denetim veya el ile inceleme için bir depolama hesabına kaydedin. Bekletme süresini gün cinsinden belirtmek için tanılama ayarlarını kullanabilirsiniz.
* **Olay Hub 'ı**: günlükleri Azure Event Hubs Stream. Günlükler, Power BI gibi bir iş ortağı hizmeti/özel analiz çözümüne giriş haline gelir.
* **Log Analytics çalışma alanı**: günlükleri Log Analytics analiz edin. Log Analytics ile Azure SYNAPSE tümleştirmesi aşağıdaki senaryolarda faydalıdır:
  * Log Analytics için Azure SYNAPSE tarafından yayınlanan zengin bir ölçüm kümesine karmaşık sorgular yazmak istiyorsunuz. Bu sorgularda, Azure Izleyici aracılığıyla özel uyarılar oluşturabilirsiniz.
  * Çalışma alanları genelinde izlemek istiyorsunuz. Birden çok çalışma alanındaki verileri tek bir Log Analytics çalışma alanına yönlendirebilirsiniz.

Günlükleri yayan kaynağın aboneliğinde olmayan bir depolama hesabı veya Olay Hub 'ı ad alanı da kullanabilirsiniz. Ayarı yapılandıran kullanıcının her iki aboneliğe da uygun Azure rol tabanlı erişim denetimi (Azure RBAC) erişimi olmalıdır.

#### <a name="configure-diagnostic-settings"></a>Tanılama ayarlarını yapılandırma

Çalışma alanınız, adanmış SQL havuzu veya Apache Spark havuzunuz için Tanılama ayarları oluşturun veya ekleyin.

1. Portalda Izleyici ' ye gidin. **Ayarlar**  >  **Tanılama ayarları**' nı seçin.

1. Tanılama ayarı oluşturmak istediğiniz SYNAPSE çalışma alanını, adanmış SQL havuzunu veya Apache Spark havuzunu seçin.

1. Seçilen çalışma alanında hiçbir Tanılama ayarı yoksa, bir ayar oluşturmanız istenir. **Tanılamayı aç '** ı seçin.

   Çalışma alanında var olan Tanılama ayarları varsa, kaynakta zaten yapılandırılmış ayarların bir listesini görürsünüz. **Tanılama ayarı ekle**’yi seçin.

1. Ayarınızı bir ad verin, **Log Analytics gönder**' i seçin ve sonra **Log Analytics çalışma** alanından bir çalışma alanı seçin.

    > [!NOTE]
    > Bir Azure günlük tablosunda 500 ' den fazla sütun olabileceğinden _kaynağa özgü mod_' u **seçmeniz önerilir.** Daha fazla bilgi için bkz. [AzureDiagnostics logs Reference](/azure/azure-monitor/reference/tables/azurediagnostics).

1. **Kaydet**’i seçin.

Birkaç dakika sonra yeni ayar, çalışma alanınızın, adanmış SQL havuzunun veya Apache Spark havuzunuzun ayarları listenizde görünür. Tanılama günlükleri, yeni olay verileri oluşturulmasıyla hemen bu çalışma alanına akışla kaydedilir. Bir olay yayınlandığınızda ve Log Analytics göründüğünde 15 dakikaya kadar zaman çıkabilir.

## <a name="next-steps"></a>Sonraki adımlar

İşlem hattı çalıştırmalarını izleme hakkında daha fazla bilgi için bkz. [SYNAPSE Studio 'da işlem hattı çalıştırmaları](how-to-monitor-pipeline-runs.md) . 

Apache Spark uygulamalarını izleme hakkında daha fazla bilgi için, [SYNAPSE Studio 'Da izleme Apache Spark uygulamalar](apache-spark-applications.md) makalesine bakın.

SQL isteklerini izleme hakkında daha fazla bilgi için, [SYNAPSE Studio 'DA SQL Isteklerini izleme](how-to-monitor-sql-requests.md) makalesine bakın.
