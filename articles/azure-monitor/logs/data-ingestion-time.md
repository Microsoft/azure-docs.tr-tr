---
title: Azure Izleyici 'de günlük verisi alma süresi | Microsoft Docs
description: Azure Izleyici 'de günlük verilerini toplama gecikmesini etkileyen farklı faktörleri açıklar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 49122421f04ee6eef8828ca305cfb235aceee3fb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105035702"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Azure İzleyici'de günlük verileri alım süresi
Azure Izleyici, her ay büyüyen bir hızda çok sayıda müşteriye hizmet veren binlerce müşteriyi sunan yüksek ölçekli bir veri hizmetidir. Genellikle günlük verilerinin toplandıktan sonra kullanılabilir hale gelmesi için geçen süre hakkında sık sorulan sorular vardır. Bu makalede, bu gecikmeyi etkileyen farklı faktörler açıklanmaktadır.

## <a name="typical-latency"></a>Tipik gecikme süresi
Gecikme süresi, izlenen sistemde verilerin oluşturulduğu zamanı ve Azure Izleyici 'de analize yönelik olarak sağlanan süreyi ifade eder. Günlük verilerinin alınması için tipik gecikme süresi 20 sn ile 3 dakika arasındadır. Bununla birlikte, belirli veriler için belirli gecikme süresi aşağıda açıklanan çeşitli faktörlere bağlı olarak değişir.


## <a name="factors-affecting-latency"></a>Gecikme süresini etkileyen faktörler
Belirli bir veri kümesinin toplam alım süresi, aşağıdaki üst düzey alanlara ayrılabilir. 

- Aracı saati-bir olayın keşflenme saati, toplanması ve sonra Azure Izleyici günlükleri giriş noktasına günlük kaydı olarak gönderme zamanı. Çoğu durumda, bu işlem bir aracı tarafından işlenir. Ağ tarafından ek gecikme ortaya çıkabilir.
- İşlem hattı süresi - Alım işlem hattının günlük kaydını işleme süresi. Bu, etkinliğin özelliklerini ayrıştırmayı ve büyük olasılıkla hesaplanmış bilgi eklemeyi içerir.
- Dizin oluşturma zamanı: Azure Izleyici büyük veri deposuna bir günlük kaydı almak için harcanan zaman.

Bu işlemde tanıtılan farklı gecikme süresi hakkındaki ayrıntılar aşağıda açıklanmıştır.

### <a name="agent-collection-latency"></a>Aracı toplama gecikme süresi
Aracılar ve yönetim çözümleri, bir sanal makineden veri toplamak için farklı stratejiler kullanır ve bu da gecikmeyi etkileyebilir. Bazı belirli örnekler şunlardır:

- Windows olayları, syslog olayları ve performans ölçümleri hemen toplanır. Linux performans sayaçları, 30 saniyelik aralıklarla yoklanır.
- IIS günlükleri ve özel Günlükler, zaman damgası değiştiğinde toplanır. IIS günlükleri için bu, [IIS 'de yapılandırılan rollover zamanlamasıyla](../agents/data-sources-iis-logs.md)etkilenir. 
- Active Directory çoğaltma çözümü değerlendirmesi beş günde bir gerçekleştirilir, ancak Active Directory Değerlendirmesi çözüm Active Directory altyapınızın haftalık değerlendirmesini yapar. Aracı bu günlükleri yalnızca değerlendirme tamamlandığında toplayacaktır.

### <a name="agent-upload-frequency"></a>Aracı karşıya yükleme sıklığı
Log Analytics aracısının hafif olduğundan emin olmak için, Aracı günlükleri arabelleğe alır ve düzenli aralıklarla Azure Izleyici 'ye yükler. Karşıya yükleme sıklığı, verilerin türüne bağlı olarak 30 saniye ile 2 dakika arasında değişir. Verilerin çoğu 1 dakika içinde karşıya yüklenir. 

### <a name="network"></a>Ağ
Ağ koşulları, Azure Izleyici günlükleri alma noktasına ulaşmak için bu verilerin gecikmesini olumsuz etkileyebilir.

### <a name="azure-activity-logs-resource-logs-and-metrics"></a>Azure etkinlik günlükleri, kaynak günlükleri ve ölçümleri
Azure verileri, işlenmek üzere Azure Izleyici günlükleri alma noktasında kullanılabilir hale gelecek süreyi ekler:

- Kaynak günlükleri genellikle Azure hizmetine bağlı olarak 30-90 saniye ekler. Bazı Azure Hizmetleri (özellikle, Azure SQL veritabanı ve Azure sanal ağı) Şu anda günlüklerini 5 dakikalık aralıklarla raporlayabilir. Bunun daha fazla geliştirilmesi için iş devam ediyor. Ortamınızdaki bu gecikmeyi incelemek için [aşağıdaki sorguya](#checking-ingestion-time) bakın
- Azure platformu ölçümleri, Azure Izleyici günlükleri alma noktasına aktarılması için 3 dakika sürer.
- Eski Tümleştirme kullanılıyorsa etkinlik günlüğü verileri ek 10-15 dakika sürebilir. Etkinlik günlüklerini Azure Izleyici günlüklerine almak için abonelik düzeyinde tanılama ayarlarını kullanmanızı öneririz. Bu, yaklaşık 30 saniye daha fazla gecikme süresi doğurur.

### <a name="management-solutions-collection"></a>Yönetim çözümleri koleksiyonu
Bazı çözümler, verileri bir aracıdan toplamaz ve ek gecikme sunan bir koleksiyon yöntemi kullanabilir. Bazı çözümler, neredeyse gerçek zamanlı bir süre toplamayı denemeden verileri düzenli aralıklarla toplar. Belirli örnekler şunlardır:

- Microsoft 365 çözümü, şu anda neredeyse gerçek zamanlı gecikme garantisi sağlamayan yönetim etkinliği API 'sini kullanarak etkinlik günlüklerini yoklar.
- Windows Analytics çözümleri (örneğin Güncelleştirme Uyumluluğu) verileri, çözüm tarafından günlük bir sıklıkta toplanır.

Koleksiyon sıklığını belirlemede her çözüm için belgelere bakın.

### <a name="pipeline-process-time"></a>Ardışık düzen-işlem süresi

Alım noktasında kullanılabilir olduktan sonra veriler, sorgulama için kullanılabilecek ek 30-60 saniye sürer.

Günlük kayıtları Azure Izleyici ardışık düzenine alındıktan sonra ( [_TimeReceived](./log-standard-columns.md#_timereceived) özelliğinde tanımlandığı gibi), kiracı yalıtımı sağlamak ve verilerin kaybolmamasını sağlamak için geçici depolamaya yazılır. Bu işlem genellikle 5-15 saniye ekler. Bazı yönetim çözümleri, verileri toplamak ve veri akışı sırasında Öngörüler türetmek için daha ağır algoritmalar uygular. Örneğin, ağ performansı Izleme, 3 dakikalık aralıklarla gelen verileri toplar, etkin olarak 3 dakikalık gecikme süresi ekler. Gecikme ekleyen başka bir işlem, özel günlükleri işleyen işlemdir. Bazı durumlarda bu işlem, aracıdan dosyalardan toplanan günlüklere birkaç dakika gecikme süresi ekleyebilir.

### <a name="new-custom-data-types-provisioning"></a>Yeni özel veri türleri sağlama
[Özel bir günlük](../agents/data-sources-custom-logs.md) veya [Veri Toplayıcı API](../logs/data-collector-api.md)'sinden yeni bir tür özel veri oluşturulduğunda, sistem ayrılmış bir depolama kapsayıcısı oluşturur. Ek süre gerektiren bu tek seferlik işlem yalnızca bu veri türüyle ilk kez karşılaşıldığında gerçekleştirilir.

### <a name="surge-protection"></a>Aşırı gerilim koruma
Azure Izleyici 'nin en üst önceliği, hiçbir müşteri verisinin kaybolmamasını sağlamaktır, bu nedenle sistemin veri dalgalanmalarına yönelik yerleşik koruması vardır. Bu, Immense yükünün altında bile olsa, sistemin çalışmaya devam etmesini sağlamak için arabellekleri içerir. Normal yük altında, bu denetimler bir dakikadan kısa bir süre içinde, ancak aşırı koşullarda ve hatalarda verilerin güvende olmasını sağlarken önemli bir zaman ekleyebilecekleri hatalara göre daha az zaman ekleyebilirler.

### <a name="indexing-time"></a>Dizin oluşturma süresi
Verilere anında erişim sağlamaktan farklı olarak analiz ve gelişmiş arama özellikleri sağlamaya yönelik her büyük veri platformu için yerleşik bir denge vardır. Azure Izleyici, milyarlarca kayıt üzerinde güçlü sorgular çalıştırmanızı ve birkaç saniye içinde sonuç almanızı sağlar. Bu, altyapı veri alımı sırasında verileri önemli ölçüde dönüştürtiğinden ve benzersiz kompakt yapılarda depoladığından, bu mümkün hale gelir. Bu yapıları oluşturmak için, sistem bu verileri yeterince uygun olana kadar arabelleğe alır. Günlük kaydı arama sonuçlarında görüntülenmeden önce bunun tamamlanması gerekir.

Bu işlem şu anda düşük miktarda veri hacmi olduğunda yaklaşık 5 dakika sürer, daha yüksek veri ücretlerinden daha az zaman alır. Bu, karşı sezgisel olarak görünse de, bu işlem yüksek hacimli üretim iş yükleri için gecikme iyileştirmesine izin verir.



## <a name="checking-ingestion-time"></a>Alma süresi denetleniyor
Alım süresi farklı koşullarda farklı kaynaklar için farklılık gösterebilir. Ortamınızdaki belirli davranışları belirlemek için günlük sorgularını kullanabilirsiniz. Aşağıdaki tabloda, bir kaydın oluşturulduğu ve Azure Izleyici 'ye gönderildiği gibi farklı zamanları nasıl belirleyebilmeniz gerektiğini belirtir.

| Adım | Özellik veya Işlev | Yorumlar |
|:---|:---|:---|
| Veri kaynağında oluşturulan kayıt | [TimeGenerated](./log-standard-columns.md#timegenerated-and-timestamp) <br>Veri kaynağı bu değeri ayarlanmamışsa, _TimeReceived ile aynı saate ayarlanır. |
| Azure Izleyici alma uç noktası tarafından alınan kayıt | [_TimeReceived](./log-standard-columns.md#_timereceived) | Bu alan, yığın işleme için en iyi duruma getirilmemiştir ve büyük veri kümelerini filtrelemek için kullanılmamalıdır. |
| Kayıt, çalışma alanında depolandı ve sorgular için kullanılabilir | [ingestion_time ()](/azure/kusto/query/ingestiontimefunction) | Yalnızca belirli bir zaman penceresinde giriş yapılan kayıtları filtrelemeye gerek varsa ingestion_time () kullanılması önerilir. Böyle bir durumda, aynı zamanda daha büyük bir aralığa sahip TimeGenerated filtresi eklemeniz önerilir. |

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

Önceki yüzdebirlik denetimleri, gecikme süresi içinde genel eğilimleri bulmak için iyi bir seçimdir. Gecikme süresi içinde kısa süreli bir ani artış belirlemek için maksimum () kullanımı `max()` daha etkili olabilir.

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
* Azure Izleyici için [hizmet düzeyi sözleşmesi (SLA)](https://azure.microsoft.com/en-us/support/legal/sla/monitor/v1_3/) makalesini okuyun.
