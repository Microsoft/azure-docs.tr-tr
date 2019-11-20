---
title: Azure Izleyici 'de günlük verisi alma süresi | Microsoft Docs
description: Azure Izleyici 'de günlük verilerini toplama gecikmesini etkileyen farklı faktörleri açıklar.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 8b40d89920208eaf15e01b3519b667a77baf8671
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932574"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Azure Izleyici 'de günlük verisi alma süresi
Azure Izleyici, her ay büyüyen bir hızda çok sayıda müşteriye hizmet veren binlerce müşteriyi sunan yüksek ölçekli bir veri hizmetidir. Genellikle günlük verilerinin toplandıktan sonra kullanılabilir hale gelmesi için geçen süre hakkında sık sorulan sorular vardır. Bu makalede, bu gecikmeyi etkileyen farklı faktörler açıklanmaktadır.

## <a name="typical-latency"></a>Tipik gecikme süresi
Gecikme süresi, izlenen sistemde verilerin oluşturulduğu zamanı ve Azure Izleyici 'de analize yönelik olarak sağlanan süreyi ifade eder. Günlük verilerinin alınması için tipik gecikme süresi 2 ila 5 dakikadır. Belirli veriler için belirli gecikme süresi, aşağıda açıklanan çeşitli faktörlere bağlı olarak değişir.


## <a name="factors-affecting-latency"></a>Gecikme süresini etkileyen faktörler
Belirli bir veri kümesinin toplam alım süresi, aşağıdaki üst düzey alanlara ayrılabilir. 

- Aracı saati-bir olayı bulma, toplama ve sonra Azure Izleyici alma noktasına bir günlük kaydı olarak gönderme süresi. Çoğu durumda, bu işlem bir aracı tarafından işlenir.
- İşlem hattı süresi - Alım işlem hattının günlük kaydını işleme süresi. Bu, etkinliğin özelliklerini ayrıştırmayı ve büyük olasılıkla hesaplanmış bilgi eklemeyi içerir.
- Dizin oluşturma zamanı: Azure Izleyici büyük veri deposuna bir günlük kaydı almak için harcanan zaman.

Bu işlemde tanıtılan farklı gecikme süresi hakkındaki ayrıntılar aşağıda açıklanmıştır.

### <a name="agent-collection-latency"></a>Aracı toplama gecikmesi
Aracılar ve yönetim çözümleri, bir sanal makineden veri toplamak için farklı stratejiler kullanır ve bu da gecikmeyi etkileyebilir. Bazı belirli örnekler şunlardır:

- Windows olayları, syslog olayları ve performans ölçümleri hemen toplanır. Linux performans sayaçları, 30 saniyelik aralıklarla yoklanır.
- IIS günlükleri ve özel Günlükler, zaman damgası değiştiğinde toplanır. IIS günlükleri için bu, [IIS 'de yapılandırılan rollover zamanlamasıyla](data-sources-iis-logs.md)etkilenir. 
- Active Directory çoğaltma çözümü değerlendirmesi beş günde bir gerçekleştirilir, ancak Active Directory Değerlendirmesi çözüm Active Directory altyapınızın haftalık değerlendirmesini yapar. Aracı bu günlükleri yalnızca değerlendirme tamamlandığında toplayacaktır.

### <a name="agent-upload-frequency"></a>Aracı karşıya yükleme sıklığı
Log Analytics aracısının hafif olduğundan emin olmak için, Aracı günlükleri arabelleğe alır ve düzenli aralıklarla Azure Izleyici 'ye yükler. Karşıya yükleme sıklığı, verilerin türüne bağlı olarak 30 saniye ile 2 dakika arasında değişir. Verilerin çoğu 1 dakika içinde karşıya yüklenir. Ağ koşulları, Azure Izleyici alma noktasına ulaşmak için bu verilerin gecikmesini olumsuz etkileyebilir.

### <a name="azure-activity-logs-diagnostic-logs-and-metrics"></a>Azure etkinlik günlükleri, tanılama günlükleri ve ölçümleri
Azure verileri, işleme için Log Analytics Alım noktasında kullanılabilecek ek süre ekler:

- Tanılama günlüklerinden alınan veriler, Azure hizmetine bağlı olarak 2-15 dakika sürer. Ortamınızdaki bu gecikmeyi incelemek için [aşağıdaki sorguya](#checking-ingestion-time) bakın
- Azure platformu ölçümlerinin Log Analytics alma noktasına gönderilmesi 3 dakika sürer.
- Etkinlik günlüğü verilerinin Log Analytics alma noktasına gönderilmesi yaklaşık 10-15 dakika sürer.

Alım noktasında kullanılabilir olduğunda, verilerin sorgulanmasını sağlamak için ek 2-5 dakika sürer.

### <a name="management-solutions-collection"></a>Yönetim çözümleri koleksiyonu
Bazı çözümler, verileri bir aracıdan toplamaz ve ek gecikme sunan bir koleksiyon yöntemi kullanabilir. Bazı çözümler, neredeyse gerçek zamanlı bir süre toplamayı denemeden verileri düzenli aralıklarla toplar. Belirli örnekler şunlardır:

- Office 365 çözümü, geçerli olarak neredeyse gerçek zamanlı gecikme garantisi sağlamayan Office 365 yönetim etkinliği API 'sini kullanarak etkinlik günlüklerini yoklar.
- Windows Analytics çözümleri (örneğin Güncelleştirme Uyumluluğu) verileri, çözüm tarafından günlük bir sıklıkta toplanır.

Koleksiyon sıklığını belirlemede her çözüm için belgelere bakın.

### <a name="pipeline-process-time"></a>Ardışık düzen-işlem süresi
Günlük kayıtları Azure Izleyici ardışık düzenine alındıktan sonra ( [_Timereceilıorlama](log-standard-properties.md#_timereceived) özelliğinde tanımlandığı gibi), kiracı yalıtımı sağlamak ve verilerin kaybolmamasını sağlamak için geçici depolamaya yazılır. Bu işlem genellikle 5-15 saniye ekler. Bazı yönetim çözümleri, verileri toplamak ve veri akışı sırasında Öngörüler türetmek için daha ağır algoritmalar uygular. Örneğin, ağ performansı Izleme, 3 dakikalık aralıklarla gelen verileri toplar, etkin olarak 3 dakikalık gecikme süresi ekler. Gecikme ekleyen başka bir işlem, özel günlükleri işleyen işlemdir. Bazı durumlarda bu işlem, aracıdan dosyalardan toplanan günlüklere birkaç dakika gecikme süresi ekleyebilir.

### <a name="new-custom-data-types-provisioning"></a>Yeni özel veri türleri sağlama
[Özel bir günlük](data-sources-custom-logs.md) veya [Veri Toplayıcı API](data-collector-api.md)'sinden yeni bir tür özel veri oluşturulduğunda, sistem ayrılmış bir depolama kapsayıcısı oluşturur. Bu, yalnızca bu veri türünün ilk görünümünde gerçekleşen tek seferlik bir ek yüktür.

### <a name="surge-protection"></a>Aşırı gerilim koruma
Azure Izleyici 'nin en üst önceliği, hiçbir müşteri verisinin kaybolmamasını sağlamaktır, bu nedenle sistemin veri dalgalanmalarına yönelik yerleşik koruması vardır. Bu, Immense yükünün altında bile olsa, sistemin çalışmaya devam etmesini sağlamak için arabellekleri içerir. Normal yük altında, bu denetimler bir dakikadan kısa bir süre içinde, ancak aşırı koşullarda ve hatalarda verilerin güvende olmasını sağlarken önemli bir zaman ekleyebilecekleri hatalara göre daha az zaman ekleyebilirler.

### <a name="indexing-time"></a>Dizin oluşturma süresi
Verilere anında erişim sağlamaktan farklı olarak analiz ve gelişmiş arama özellikleri sağlamaya yönelik her büyük veri platformu için yerleşik bir denge vardır. Azure Izleyici, milyarlarca kayıt üzerinde güçlü sorgular çalıştırmanızı ve birkaç saniye içinde sonuç almanızı sağlar. Bu, altyapı veri alımı sırasında verileri önemli ölçüde dönüştürtiğinden ve benzersiz kompakt yapılarda depoladığından, bu mümkün hale gelir. Bu yapıları oluşturmak için, sistem bu verileri yeterince uygun olana kadar arabelleğe alır. Günlük kaydı arama sonuçlarında görüntülenmeden önce bunun tamamlanması gerekir.

Bu işlem şu anda düşük miktarda veri hacmi olduğunda yaklaşık 5 dakika sürer, daha yüksek veri ücretlerinden daha az zaman alır. Bu, karşı sezgisel olarak görünse de, bu işlem yüksek hacimli üretim iş yükleri için gecikme iyileştirmesine izin verir.



## <a name="checking-ingestion-time"></a>Alma süresi denetleniyor
Alım süresi farklı koşullarda farklı kaynaklar için farklılık gösterebilir. Ortamınızdaki belirli davranışları belirlemek için günlük sorgularını kullanabilirsiniz. Aşağıdaki tabloda, bir kaydın oluşturulduğu ve Azure Izleyici 'ye gönderildiği gibi farklı zamanları nasıl belirleyebilmeniz gerektiğini belirtir.

| Adım | Özellik veya Işlev | Yorumlar |
|:---|:---|:---|
| Veri kaynağında oluşturulan kayıt | [TimeGenerated](log-standard-properties.md#timegenerated-and-timestamp) <br>Veri kaynağı bu değeri ayarlanmamışsa, _Timerectıı ile aynı saate ayarlanır. |
| Azure Izleyici alma uç noktası tarafından alınan kayıt | [_Timerecelmiş](log-standard-properties.md#_timereceived) | |
| Kayıt, çalışma alanında depolandı ve sorgular için kullanılabilir | [ingestion_time()](/azure/kusto/query/ingestiontimefunction) | |

### <a name="ingestion-latency-delays"></a>Alma gecikmesi gecikme gecikmeleri
[İngestion_time ()](/azure/kusto/query/ingestiontimefunction) Işlevinin sonucunu _TimeGenerated_ özelliği ile karşılaştırarak belirli bir kaydın gecikmesini ölçebilirsiniz. Bu veriler, alma gecikmesini nasıl davranacağını bulmak için çeşitli toplamalar ile birlikte kullanılabilir. Büyük miktarda veri için Öngörüler elde etmek üzere alma süresinin bazı yüzdelerini inceleyin. 

Örneğin, aşağıdaki sorgu, son 8 saat içinde hangi bilgisayarların en yüksek alım zamanına sahip olduğunu gösterir: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

Önceki yüzdebirlik denetimleri, gecikme süresi içinde genel eğilimleri bulmak için iyi bir seçimdir. Gecikme süresi içinde kısa süreli bir ani artış belirlemek için, en fazla (`max()`) kullanılması daha etkili olabilir.

Belirli bir bilgisayar için belirli bir süre içinde alım zamanında detaya gitmek isterseniz, bir grafikteki son günden verileri görselleştirerek aşağıdaki sorguyu kullanın: 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
Bilgisayar alma süresini, IP adreslerini temel alan bulunduğu ülkeye/bölgeye göre göstermek için aşağıdaki sorguyu kullanın: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Aracıdan kaynaklanan farklı veri türleri farklı alma gecikme süresine sahip olabilir, bu nedenle önceki sorgular diğer türlerle birlikte kullanılabilir. Çeşitli Azure hizmetlerinin giriş süresini incelemek için aşağıdaki sorguyu kullanın: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Yanıt vermeyi durduran kaynaklar 
Bazı durumlarda, bir kaynak veri göndermeyi durdurabilir. Bir kaynağın veri gönderip göndermediğini anlamak için, standart _TimeGenerated_ alanı tarafından tanımlanabilecek en son kayıt bölümüne bakın.  

Bir sinyal bir kez aracıdan bir kez gönderildiğinden, bir VM 'nin kullanılabilirliğini denetlemek için _sinyal_ tablosu ' nu kullanın. En son sinyal bildirmeyen etkin bilgisayarları listelemek için aşağıdaki sorguyu kullanın: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Sonraki adımlar
* Azure Izleyici için [hizmet düzeyi sözleşmesi (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) makalesini okuyun.

