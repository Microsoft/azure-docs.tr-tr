---
title: Azure Izleyici hakkında SSS | Microsoft Docs
description: Azure Izleyici hakkında sık sorulan soruların yanıtları.
services: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2020
ms.openlocfilehash: fa91644eab9d28ffb20de8ec8c0fe00488922b67
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563387"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure Izleyici sık sorulan sorular

Bu Microsoft SSS, Azure Izleyici hakkında sık sorulan sorulardan oluşan bir listesidir. Başka sorularınız varsa, [tartışma forumuna](/answers/questions/topics/single/24223.html) gidin ve sorularınızı gönderin. Bir soru sıkça sorulduğunda, hızlı ve kolay bir şekilde bulunabilmesi için bu makaleye ekleyeceğiz.


## <a name="general"></a>Genel

### <a name="what-is-azure-monitor"></a>Azure İzleyici nedir?
Azure [izleyici](overview.md) , Azure 'da, diğer bulut ortamlarında veya şirket içinde bulunan uygulamalar ve hizmetler için performans ve kullanılabilirlik izlemeyi sağlayan bir Azure hizmetidir. Azure Izleyici, birden çok kaynaktan alınan verileri, eğilimleri ve anormaller için çözümlenebileceği ortak bir veri platformuna toplar. Azure Izleyici 'de zengin özellikler, uygulamanızı etkileyebilecek kritik durumları hızla tanımanıza ve yanıt vermeye yardımcı olur.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Azure Izleyici, Log Analytics ve Application Insights arasındaki fark nedir?
Eylül 2018 ' de, Microsoft birleştirilmiş Azure Izleyici, Log Analytics ve Application Insights uygulamalarınızın ve bağlı oldukları bileşenlerin güçlü bir uçtan uca izlenmesini sağlamak için tek bir hizmete. Log Analytics ve Application Insights özellikleri değişmemiştir, ancak bazı özellikler yeni kapsamını daha iyi yansıtacak şekilde Azure Izleyici 'ye yeniden eklenmiştir. Log Analytics günlük veri altyapısı ve sorgu dili artık Azure Izleyici günlükleri olarak adlandırılır. Bkz. [Azure izleyici terminolojisi güncelleştirmeleri](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Azure Izleyici maliyeti nedir?
Ölçüm ve etkinlik günlüklerinin toplanması gibi otomatik olarak etkinleştirilen Azure Izleyici özellikleri ücretsiz olarak sunulmaktadır. Günlük sorguları ve uyarı gibi diğer özelliklerle ilişkili bir maliyet vardır. Ayrıntılı fiyatlandırma bilgileri için bkz. [Azure izleyici fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/monitor/) .

### <a name="how-do-i-enable-azure-monitor"></a>Azure Izleyiciyi etkinleştirmek Nasıl yaparım? mı?
Azure Izleyici, yeni bir Azure aboneliği oluşturduğunuz anda ve [etkinlik günlüğü](./essentials/platform-logs-overview.md) ile Platform [ölçümleri](essentials/data-platform-metrics.md) otomatik olarak toplandığında etkindir. Azure kaynaklarınızın çalışması hakkında daha ayrıntılı bilgi toplamak için [Tanılama ayarları](essentials/diagnostic-settings.md) oluşturun ve belirli hizmetler için toplanan veriler hakkında ek analizler sağlamak üzere [izleme çözümleri](insights/solutions.md) ve [Öngörüler](./monitor-reference.md) ekleyin. 

### <a name="how-do-i-access-azure-monitor"></a>Azure Izleyici erişimi mi Nasıl yaparım??
Tüm Azure Izleyici özelliklerine ve verilerine, Azure portal 'daki **izleyici** menüsünden erişin. Farklı Azure hizmetleri menüsünün **izleme** bölümü, belirli bir kaynağa filtrelenen verilerle aynı araçlara erişim sağlar. Azure Izleyici verilerine, CLı, PowerShell ve bir REST API kullanan çeşitli senaryolar için de erişilebilir.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Azure Izleyici 'nin şirket içi bir sürümü var mı?
Hayır. Azure izleyici, şirket içinde ve diğer bulutlardaki kaynakları izleyebilse de büyük miktarlarda veriyi işleyen ve depolayan ölçeklenebilir bir bulut hizmetidir.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Azure Izleyici, şirket içi kaynakları izleyebilir mi?
Evet, Azure Izleyici, Azure kaynaklarından izleme verilerinin toplanmasına ek olarak, diğer bulutlardaki ve Şirket içindeki sanal makinelerden ve uygulamalardan veri toplayabilir. Bkz. [Azure izleyici için izleme verileri kaynakları](agents/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Azure Izleyici System Center Operations Manager tümleştirsin mi?
Aracılardan Azure Izleyici günlüklerine veri toplamak için mevcut System Center Operations Manager yönetim grubunuzu Azure Izleyici 'ye bağlayabilirsiniz. Bu, aracılardan toplanan verileri analiz etmek için günlük sorgularını ve çözümü kullanmanıza olanak sağlar. Ayrıca, mevcut System Center Operations Manager aracılarını doğrudan Azure Izleyici 'ye veri gönderecek şekilde yapılandırabilirsiniz. Bkz. [Azure izleyici 'ye Operations Manager bağlama](agents/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Azure Izleyici hangi IP adreslerini kullanır?
Aracıların ve diğer dış kaynakların Azure Izleyici 'ye erişmesi için gereken IP adresleri ve bağlantı noktalarının listesi için [, Application Insights tarafından kullanılan IP adresleri ve Log Analytics](app/ip-addresses.md) bakın. 

## <a name="monitoring-data"></a>Verileri izleme

### <a name="where-does-azure-monitor-get-its-data"></a>Azure Izleyici verilerini nereden alır?
Azure Izleyici, Azure platformu ve kaynakları, özel uygulamalar ve sanal makinelerde çalışan aracılardan Günlükler ve ölçümler de dahil olmak üzere çeşitli kaynaklardan veri toplar. Azure Güvenlik Merkezi ve ağ Izleyicisi gibi diğer hizmetler, Azure Izleyici verileriyle çözümlenebilmesi için bir Log Analytics çalışma alanına veri toplar. Günlükler veya ölçümler için REST API kullanarak Azure Izleyici 'ye özel veriler de gönderebilirsiniz. Bkz. [Azure izleyici için izleme verileri kaynakları](agents/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Azure Izleyici hangi verileri toplamıştır? 
Azure Izleyici çeşitli kaynaklardaki verileri [Günlükler](logs/data-platform-logs.md) veya [ölçümler](essentials/data-platform-metrics.md)halinde toplar. Her bir veri türü kendi göreli avantajlarına sahiptir ve her biri Azure Izleyici 'de belirli bir özellik kümesini destekler. Her bir Azure aboneliği için tek bir ölçüm veritabanı bulunur, ancak gereksinimlerinize bağlı olarak günlükleri toplamak için birden çok Log Analytics çalışma alanı oluşturabilirsiniz. Bkz. [Azure izleyici veri platformu](data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Azure Izleyici 'de toplayadığım maksimum veri miktarı var mı?
Toplayacağınız Ölçüm verisi miktarına yönelik bir sınır yoktur, ancak bu veriler en fazla 93 gün boyunca depolanır. [Ölçüm bekletme](essentials/data-platform-metrics.md#retention-of-metrics)konusuna bakın. Toplayacağınız günlük verisi miktarı için bir sınır yoktur, ancak Log Analytics çalışma alanı için seçtiğiniz fiyatlandırma katmanından etkilenebilir. [Fiyatlandırma ayrıntılarına](https://azure.microsoft.com/pricing/details/monitor/)bakın.

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Azure Izleyici tarafından toplanan Nasıl yaparım? erişim verileri?
Öngörüler ve çözümler, Azure Izleyici 'de depolanan verilerle çalışmaya yönelik özel bir deneyim sağlar. Kusto sorgu dilinde (KQL) yazılmış bir günlük sorgusunu kullanarak doğrudan günlük verileriyle çalışabilirsiniz. Azure portal sorguları yazabilir ve çalıştırabilir ve Log Analytics kullanarak verileri etkileşimli bir şekilde çözümleyebilirsiniz. Azure portal ölçümleri Ölçüm Gezgini ile çözümleyin. Bkz. [Azure izleyici 'de günlük verilerini çözümleme](logs/log-query-overview.md) ve [Azure Ölçüm Gezgini kullanmaya](essentials/metrics-getting-started.md)başlama.

## <a name="solutions-and-insights"></a>Çözümler ve Öngörüler

### <a name="what-is-an-insight-in-azure-monitor"></a>Azure Izleyici 'de öngörü nedir?
Öngörüler, belirli Azure hizmetleri için özelleştirilmiş bir izleme deneyimi sağlar. Bunlar, Azure Izleyici 'deki diğer özelliklerle aynı ölçümleri ve günlükleri kullanır, ancak ek veri toplayabilir ve Azure portal benzersiz bir deneyim sağlayabilir. Bkz. [Azure izleyici 'de Öngörüler](./monitor-reference.md).

Azure portal içgörüleri görüntülemek için, **izleme** menüsündeki **Öngörüler** bölümüne veya hizmet menüsünün **izleme** bölümüne bakın.

### <a name="what-is-a-solution-in-azure-monitor"></a>Azure Izleyici 'de çözüm nedir?
İzleme çözümleri, belirli bir uygulamayı veya hizmeti Azure Izleyici özelliklerine göre izlemek için paketlenmiş mantık kümeleridir. Azure Izleyici 'de günlük verilerini toplar ve Azure portal ortak bir deneyim kullanarak analizine yönelik günlük sorguları ve görünümleri sağlar. Bkz. [Azure izleyici 'de çözümleri izleme](insights/solutions.md).

Azure portal çözümleri görüntülemek için **izleyici** menüsünün **Öngörüler** bölümünde **daha fazla** ' ya tıklayın. Çalışma alanına ek çözümler eklemek için **Ekle** ' ye tıklayın.

## <a name="logs"></a>Günlükler

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Azure Izleyici günlükleri ve Azure Veri Gezgini arasındaki fark nedir?
Azure Veri Gezgini, günlük ve telemetri verileri için hızlı ve üst düzeyde ölçeklenebilir veri keşfetme hizmetidir. Azure Izleyici günlükleri Azure Veri Gezgini üzerine kurulmuştur ve bazı küçük farklılıklar ile aynı kusto sorgu dilini (KQL) kullanır. Bkz. [Azure izleyici günlük sorgusu dil farkları](/azure/data-explorer/kusto/query/).

### <a name="how-do-i-retrieve-log-data"></a>Günlük verilerini almak Nasıl yaparım??
Tüm veriler, kusto sorgu dili (KQL) kullanılarak yazılmış bir günlük sorgusu kullanarak bir Log Analytics çalışma alanından alınır. Kendi sorgularınızı yazabilir veya belirli bir uygulama veya hizmete ait günlük sorgularını içeren çözüm ve Öngörüler kullanabilirsiniz. Bkz. [Azure izleyici 'de günlük sorgularına genel bakış](logs/log-query-overview.md).

### <a name="can-i-delete-data-from-a-log-analytics-workspace"></a>Log Analytics çalışma alanındaki verileri silebilir miyim?
Veriler, [bekletme dönemine](logs/manage-cost-storage.md#change-the-data-retention-period)göre çalışma alanından kaldırılır. Gizlilik veya uyumluluk nedenleriyle belirli verileri silebilirsiniz. Daha fazla bilgi için bkz. [özel verileri dışarı ve silme](logs/personal-data-mgmt.md#how-to-export-and-delete-private-data) .

### <a name="is-log-analytics-storage-immutable"></a>Log Analytics depolama alanı sabit mi?
Veritabanı depolamadaki veriler bir kez alındıktan sonra değiştirilemez, ancak [özel verileri silmek için *Temizleme* API 'si](./logs/personal-data-mgmt.md#delete)yoluyla silinebilirler. Veriler değiştirilemeyeceği halde, bazı sertifikalar verilerin sabit tutulmasını gerektirir ve depolamada değiştirilemez veya silinemez. Veri imlebilirlik, [Sabit depolama](../storage/blobs/storage-blob-immutability-policies-manage.md)olarak yapılandırılmış bir depolama hesabına [veri aktarma](./logs/logs-data-export.md) kullanılarak sağlanabilir.

### <a name="what-is-a-log-analytics-workspace"></a>Log Analytics çalışma alanı nedir?
Azure Izleyici tarafından toplanan tüm günlük verileri Log Analytics çalışma alanında depolanır. Çalışma alanı aslında çeşitli kaynaklardan günlük verilerinin toplandığı bir kapsayıcıdır. Tüm izleme verileriniz için tek bir Log Analytics çalışma alanınız olabilir veya birden çok çalışma alanı için gereksinimlere sahip olabilirsiniz. Bkz. [Azure Izleyici günlükleri dağıtımınızı tasarlama](logs/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Mevcut bir Log Analytics çalışma alanını başka bir Azure aboneliğine taşıyabilir miyim?
Çalışma alanını kaynak grupları veya abonelikler arasında, farklı bir bölgeye taşıyabilirsiniz. Bkz. [Log Analytics çalışma alanını farklı bir aboneliğe veya kaynak grubuna taşıma](logs/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Sorgu Gezginini neden göremiyorum ve düğmeleri Log Analytics Kaydet?

Sorgu [kapsamı](logs/scope.md) belirli bir kaynağa ayarlandığında, **sorgu Gezgini**, **Kaydet** ve **Yeni uyarı kuralı** düğmeleri kullanılamaz. Uyarı oluşturmak, bir sorguyu kaydetmek veya yüklemek için Log Analytics bir çalışma alanı kapsamında olmalıdır. Çalışma alanı bağlamında Log Analytics açmak için **Azure izleyici** menüsünden **Günlükler** ' i seçin. Son kullanılan çalışma alanı seçilir, ancak başka bir çalışma alanını seçebilirsiniz. Bkz. [Azure izleyici 'de günlük sorgusu kapsamı ve zaman aralığı Log Analytics](logs/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Neden "Bu sorguyu etkinleştirmek için bu aboneliğin ' Microsoft. Insights ' kaynak sağlayıcısını Kaydet" ' I bir VM 'den Log Analytics açarken bu sorguyu etkinleştirmek istiyor musunuz? 
Birçok kaynak sağlayıcısı otomatik olarak kaydedilir, ancak bazı kaynak sağlayıcılarını el ile kaydetmeniz gerekebilir. Kayıt kapsamı her zaman abonelik olur. Daha fazla bilgi için bkz. [Kaynak sağlayıcıları ve türleri](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Log Analytics bir VM 'den açarken neden erişim hatası mesajı alıyorum? 
VM günlüklerini görüntülemek için, VM günlüklerini depolayan çalışma alanları için okuma izni verilmesi gerekir. Bu durumlarda yöneticinizin Azure 'daki izinleri size vermesi gerekir.

## <a name="metrics"></a>Ölçümler

### <a name="why-are-metrics-from-the-guest-os-of-my-azure-virtual-machine-not-showing-up-in-metrics-explorer"></a>Azure sanal makinelerimin Konuk işletim sistemindeki ölçümler, Ölçüm Gezgini ' nde görünmüyor mu?
[Platform ölçümleri](essentials/monitor-azure-resource.md#monitoring-data) , Azure kaynakları için otomatik olarak toplanır. Bir sanal makinenin Konuk IŞLETIM sisteminden ölçümleri toplamak için bazı yapılandırmalar gerçekleştirmeniz gerekir. Windows VM için, tanılama uzantısını yükleyip Azure Izleyici havuzunu [Windows Azure tanılama uzantısı 'nı (WAD) yükleyip yapılandırma](agents/diagnostics-extension-windows-install.md)bölümünde açıklandığı gibi yapılandırın. Linux için telegraf aracısını, [etkileyen bir Linux VM için özel ölçümleri toplama bölümünde açıklandığı gibi yükleyerek, etkileyen bir sanal makine telegraf aracısıdır](essentials/collect-custom-metrics-linux-telegraf.md).

## <a name="alerts"></a>Uyarılar

### <a name="what-is-an-alert-in-azure-monitor"></a>Azure Izleyici 'de uyarı nedir?
Uyarılar, izleme verilerinizde önemli koşullar bulunduğunda size bir bildirim gönderir. Bunlar, sisteminizin kullanıcıları tarafından bildirilmeksizin sorunları tanımlamanızı ve adreslerinizi belirlemenizi sağlar. Birden çok uyarı türü vardır:

- Ölçüm-Ölçüm değeri bir eşiği aşıyor.
- Günlük sorgusu-günlük sorgusunun sonuçları tanımlanan ölçütlerle eşleşiyor.
- Etkinlik günlüğü-etkinlik günlüğü olayı tanımlı ölçütlerle eşleşiyor.
- Web testi-kullanılabilirlik testi eşleşme tanımlı ölçütlere ilişkin sonuçlar.


Bkz. [Microsoft Azure uyarılara genel bakış](alerts/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Eylem grubu nedir?
Eylem grubu, bir uyarı tarafından tetiklenebilecek bildirimlerin ve eylemlerin bir koleksiyonudur. Birden çok uyarı tek bir eylem grubu kullanarak, ortak bildirim ve eylem kümelerinden yararlanmanızı sağlar. Bkz. [Azure Portal eylem grupları oluşturma ve yönetme](alerts/action-groups.md).


### <a name="what-is-an-action-rule"></a>Eylem kuralı nedir?
Eylem kuralı, belirli ölçütlerle eşleşen bir uyarı kümesinin davranışını değiştirmenize olanak sağlar. Bu, bakım penceresi sırasında uyarı eylemlerini devre dışı bırak gibi gereksinimleri gerçekleştirmenize olanak tanır. Bir eylem grubunu doğrudan uyarı kurallarına uygulamak yerine bir uyarı kümesine de uygulayabilirsiniz. [Eylem kurallarına](alerts/alerts-action-rules.md)bakın.

## <a name="agents"></a>Aracılar

### <a name="does-azure-monitor-require-an-agent"></a>Azure Izleyici bir aracı gerektiriyor mu?
Bir aracı yalnızca işletim sisteminden ve sanal makinelerdeki iş yüklerinden veri toplamak için gereklidir. Sanal makineler, Azure 'da, başka bir bulut ortamında veya şirket içinde bulunabilir. Bkz. [Azure izleyici aracılarına genel bakış](agents/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Azure Izleyici aracıları arasındaki fark nedir?
Azure tanılama uzantısı, Azure sanal makinelerine yöneliktir ve Azure Izleyici ölçümleri, Azure depolama ve Azure Event Hubs verilerini toplar. Log Analytics Aracısı, Azure 'daki sanal makinelere, başka bir bulut ortamına veya şirket içinde yer alır ve Azure Izleyici günlüklerine veri toplar. Bağımlılık Aracısı Log Analytics aracısına ve toplanan işlem ayrıntılarını ve bağımlılıklarını gerektirir. Bkz. [Azure izleyici aracılarına genel bakış](agents/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Aracı Trafiğim ExpressRoute bağlantısını kullanıyor mu?
Azure Izleyici trafiği Microsoft eşlemesi ExpressRoute devresini kullanır. Farklı ExpressRoute trafiği türlerinin açıklaması için bkz. [ExpressRoute belgeleri](../expressroute/expressroute-faqs.md#supported-services) . 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Log Analytics aracısının Azure Izleyici ile iletişim kurabildiğini nasıl doğrulayabilirim?
Aracı bilgisayardaki Denetim Masası ' ndan **güvenlik & ayarları**, * * Microsoft Monitoring Agent ' nı seçin. **Azure Log Analytics (OMS)** sekmesinde, yeşil onay işareti simgesi aracının Azure izleyici ile iletişim kurabildiğini onaylar. Sarı bir uyarı simgesi, aracının sorun yaşadığını gösterir. **Microsoft Monitoring Agent** hizmetin durdurulduğu bir yaygın neden olur. Hizmeti yeniden başlatmak için hizmet denetimi Yöneticisi 'ni kullanın.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Log Analytics aracısının Azure Izleyici ile iletişim kurmasını Nasıl yaparım? mı?
Doğrudan Log Analytics bağlı aracılar için, Denetim Masası 'nı açın ve **güvenlik & ayarları** **Microsoft Monitoring Agent**' nı seçin. **Azure Log Analytics (OMS)** sekmesinde, listelenen tüm çalışma alanlarını kaldırın. System Center Operations Manager, bilgisayarı Log Analytics yönetilen bilgisayarlar listesinden kaldırın. Operations Manager, aracının yapılandırmasını artık Log Analytics raporlanmayacak şekilde güncelleştirir. 

### <a name="how-much-data-is-sent-per-agent"></a>Aracı başına ne kadar veri gönderildi?
Aracı başına gönderilen veri miktarı şunlara bağlıdır:

* Etkinleştirdiğiniz çözümler
* Toplanmakta olan günlük ve performans sayacı sayısı
* Günlüklerdeki verilerin hacmi

Ayrıntılar için bkz. [Azure Izleyici günlükleriyle kullanımı ve maliyetleri yönetme](logs/manage-cost-storage.md) .

Kablolu veri aracısını çalıştırabilecek bilgisayarlar için, ne kadar veri gönderildiğini görmek üzere aşağıdaki sorguyu kullanın:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Azure Izleyici 'ye veri gönderilirken Microsoft Yönetim Aracısı (MMA) tarafından ne kadar ağ bant genişliği kullanılıyor?
Bant genişliği, gönderilen veri miktarı üzerinde bir işlevdir. Veriler, ağ üzerinden gönderildiği için sıkıştırılır.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Log Analytics aracıdan veri toplama durdurulduğunda nasıl bildirim alabilirim?

Veri toplama durdurulduğunda bildirim almak için [Yeni bir günlük uyarısı oluşturma](alerts/alerts-metric.md) bölümünde açıklanan adımları kullanın. Uyarı kuralı için aşağıdaki ayarları kullanın:

- **Uyarı koşulunu tanımlayın**: Log Analytics çalışma alanınızı kaynak hedefi olarak belirtin.
- **Uyarı ölçütleri** 
   - **Sinyal adı**: *özel günlük araması*
   - **Arama sorgusu**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Uyarı mantığı**:  *sonuç sayısına* ve *şundan büyük* bir **koşula** göre **eşik değeri** *0*
   - **Temelinde değerlendirilen**: **süresi (dakika)** *30*, **Sıklık (dakika)** *10*
- **Uyarı ayrıntılarını tanımlama** 
   - **Ad**: *veri koleksiyonu durduruldu*
   - **Önem derecesi**: *Uyarı*

Mevcut veya yeni bir [eylem grubu](alerts/action-groups.md) belirtin, böylece günlük uyarısı ölçütlerle eşleştiğinde, 15 dakikadan uzun bir sinyal eksik olduğunda size bildirilir.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Azure Izleyici aracıları için güvenlik duvarı gereksinimleri nelerdir?
Güvenlik Duvarı gereksinimleriyle ilgili ayrıntılar için bkz. [ağ güvenlik duvarı gereksinimleri](agents/log-analytics-agent.md#network-requirements).


## <a name="visualizations"></a>Görsel öğeler

### <a name="why-cant-i-see-view-designer"></a>Neden görünüm tasarımcısını göremiyorum?

Görünüm Tasarımcısı yalnızca Log Analytics çalışma alanında katkıda bulunan izinlerle veya üzeri olarak atanmış kullanıcılar için kullanılabilir.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Yapılandırma sorunları
*Şunları ayarlarken sorun yaşıyorum:*

* [.NET uygulaması](app/asp-net-troubleshoot-no-data.md)
* [Zaten çalışan bir uygulamayı izleme](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure tanılama](agents/diagnostics-extension-to-application-insights.md)
* [Java web uygulaması](app/java-troubleshoot.md)

*Sunucuma veri aldım:*

* [Güvenlik Duvarı özel durumlarını ayarlama](app/ip-addresses.md)
* [ASP.NET sunucusu kurma](app/monitor-performance-live-website-now.md)
* [Java sunucusu kurma](app/java-agent.md)

*Kaç Application Insights kaynak dağıtmalıyım:*

* [Application Insights dağıtımınızı tasarlama: bir çok Application Insights kaynağı.](app/separate-resources.md)

### <a name="can-i-use-application-insights-with-"></a>Application Insights,... ile birlikte kullanabilir miyim?

* [Azure VM 'de veya Azure sanal makine ölçek kümesindeki bir IIS sunucusundaki Web uygulamaları](app/azure-vm-vmss-apps.md)
* [Bir IIS sunucusundaki Web Apps-şirket içi veya bir VM](app/asp-net.md)
* [Java web uygulamaları](app/java-get-started.md)
* [Node.js uygulamaları](app/nodejs.md)
* [Azure 'da Web Apps](app/azure-web-apps.md)
* [Azure üzerinde Cloud Services](app/cloudservices.md)
* [Docker 'da çalışan uygulama sunucuları](./azure-monitor-app-hub.yml)
* [Tek sayfalı web uygulamaları](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows masaüstü uygulaması](app/windows-desktop.md)
* [Diğer platformlar](app/platforms.md)

### <a name="is-it-free"></a>Ücretsizdir mi?

Evet, deneysel kullanım için. Temel fiyatlandırma planında, uygulamanız her ay ücretsiz olarak belirli bir veri indirimi gönderebilir. Ücretsiz kullanım, geliştirme ve az sayıda kullanıcı için bir uygulama yayımlama açısından yeterince büyük. Belirtilen miktarda verinin işlenmesini engellemek için bir Cap ayarlayabilirsiniz.

Daha büyük olan telemetri birimleri GB ile ücretlendirilir. [Ücretlerinizi nasıl sınırlayacağıyla](app/pricing.md)ilgili bazı ipuçları sunuyoruz.

Kurumsal plan her bir gün için her bir Web sunucusu düğümünün telemetri gönderdiği bir ücret doğurur. Büyük bir ölçekte sürekli dışarı aktarmayı kullanmak istiyorsanız uygundur.

[Fiyatlandırma planını okuyun](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Fiyatı nedir?

* Bir Application Insights kaynağındaki **kullanım ve tahmini maliyetler sayfasını** açın. Son kullanılan kullanım grafiği. İsterseniz bir veri hacmi üst sınırı belirleyebilirsiniz.
* Tüm kaynaklarda [faturanızı görmek Için Azure faturalama dikey penceresini](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) açın.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Projem 'de ne Application Insights Değiştir?
Ayrıntılar proje türüne bağlıdır. Bir Web uygulaması için:

* Bu dosyaları projenize ekler:
  * ApplicationInsights.config
  * ai.js
* Şu NuGet paketlerini yükleme:
  * *APPLICATION INSIGHTS API* -çekirdek API 'si
  * *Web uygulamaları için APPLICATION INSIGHTS API* -sunucudan telemetri göndermek için kullanılır
  * *JavaScript uygulamaları için APPLICATION INSIGHTS API* -istemciden telemetri göndermek için kullanılır
* Paketler şu derlemeleri içerir:
  * Microsoft. ApplicationInsights
  * Microsoft. ApplicationInsights. Platform
* İçine öğe ekler:
  * Web.config
  * packages.config
* (Yalnızca yeni projeler- [var olan bir projeye Application Insights eklerseniz][start]bunu el ile yapmanız gerekir.) , Application Insights kaynak KIMLIĞI ile başlatmak için istemci ve sunucu koduna kod parçacıkları ekler. Örneğin, bir MVC uygulamasında kod ana sayfa görünümlerine/paylaşılan/ \_ Layout. cshtml 'ye eklenir

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Nasıl yaparım? eski SDK sürümlerinden yükseltme yapılsın mı?
Uygulama türüne uygun SDK için [sürüm notlarına](app/release-notes.md) bakın.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Projem hangi Azure kaynağını veri gönderdiğini nasıl değiştirebilirim?
Çözüm Gezgini ' de sağ tıklayın `ApplicationInsights.config` ve **Application Insights Güncelleştir**' i seçin. Verileri Azure 'da var olan veya yeni bir kaynağa gönderebilirsiniz. Güncelleştirme Sihirbazı, sunucu SDK 'sının verilerinizi nereye göndereceğini belirleyen ApplicationInsights.config izleme anahtarını değiştirir. "Tümünü Güncelleştir" seçeneğinin işaretini kaldırmadığınız takdirde, Web sayfalarınızda göründüğü anahtarı da değiştirecek.

### <a name="do-new-azure-regions-require-the-use-of-connection-strings"></a>Yeni Azure bölgeleri bağlantı dizelerinin kullanılmasını gerektiriyor mu?

Yeni Azure bölgeleri, izleme anahtarları yerine bağlantı dizelerinin kullanılmasını **gerektirir** . [Bağlantı dizesi](./app/sdk-connection-string.md) , telemetri verilerinizi ilişkilendirmek istediğiniz kaynağı tanımlar. Ayrıca, kaynağınızın telemetri için hedef olarak kullanacağı uç noktaları değiştirmenize de olanak tanır. Bağlantı dizesini kopyalamanız ve uygulamanızın koduna veya bir ortam değişkenine eklemeniz gerekir.

### <a name="can-i-use-providersmicrosoftinsights-componentsapiversions0-in-my-azure-resource-manager-deployments"></a>`providers('Microsoft.Insights', 'components').apiVersions[0]`Azure Resource Manager dağıtımlarım içinde kullanabilir miyim?

API sürümünü doldurmak için bu yöntemi kullanmanızı önermiyoruz. En yeni sürüm, son değişiklikleri içerebilen önizleme sürümlerini temsil edebilir. Daha yeni önizleme olmayan sürümlerden bile, API sürümleri mevcut şablonlarla her zaman geriye doğru uyumlu değildir veya bazı durumlarda API sürümü tüm abonelikler için kullanılamayabilir.

### <a name="what-is-status-monitor"></a>Durum İzleyicisi nedir?

Web Apps 'te Application Insights yapılandırmaya yardımcı olması için IIS Web sunucunuzda kullanabileceğiniz bir masaüstü uygulamasıdır. Telemetri toplamaz: bir uygulamayı yapılandırmadığınızda bunu durdurabilirsiniz. 

[Daha fazla bilgi edinin](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Application Insights tarafından hangi telemetri toplanır?

Sunucu Web Apps 'ten:

* HTTP istekleri
* [Bağımlılıklar](app/asp-net-dependencies.md). Çağrıları: SQL veritabanları; Dış hizmetlere HTTP çağrıları; Azure Cosmos DB, tablo, BLOB depolama ve kuyruk. 
* [Özel durumlar](app/asp-net-exceptions.md) ve yığın izlemeleri.
* [Performans sayaçları](app/performance-counters.md) - [durum İzleyicisi](app/monitor-performance-live-website-now.md)kullanıyorsanız, [uygulama hizmetleri için Azure izleme](app/azure-web-apps.md), [VM veya sanal makine ölçek kümesi için Azure izleme](app/azure-vm-vmss-apps.md)veya [toplanan yazıcı Application Insights](app/java-collectd.md).
* Kodlarınızın [özel olayları ve ölçümleri](app/api-custom-events-metrics.md) .
* Uygun toplayıcıyı yapılandırırsanız [izleme günlükleri](app/asp-net-trace-logs.md) .

[İstemci Web sayfalarından](app/javascript.md):

* [Sayfa görüntüleme sayıları](app/usage-overview.md)
* [Ajax çağrıları](app/asp-net-dependencies.md) Çalışan bir betikten yapılan istekler.
* Sayfa görünümü yükleme verileri
* Kullanıcı ve oturum sayıları
* [Kimliği doğrulanmış kullanıcı kimlikleri](app/api-custom-events-metrics.md#authenticated-users)

Diğer kaynaklardan yapılandırırsanız:

* [Azure tanılama](agents/diagnostics-extension-to-application-insights.md)
* [Analiz 'e aktar](logs/data-collector-api.md)
* [Log Analytics](logs/data-collector-api.md)
* [Logstash](logs/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Bir Telemetriyi filtreleyebilir veya değiştirebilir miyim?

Evet, şu sunucuda yazabilirsiniz:

* Uygulamanıza gönderilmeden önce seçili telemetri öğelerine filtre uygulamak veya özellikleri eklemek için telemetri Işlemcisi.
* Telemetri başlatıcısı tüm telemetri öğelerine özellik eklemek için.

[ASP.net](app/api-filtering-sampling.md) veya [Java](app/java-filter-telemetry.md)hakkında daha fazla bilgi edinin.

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Şehir, ülke/bölge ve diğer coğrafi konum verileri nasıl hesaplanır?

[GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)kullanarak web istemcisinin IP adresini (IPv4 veya IPv6) arar.

* Tarayıcı telemetrisi: gönderenin IP adresini topladık.
* Sunucu telemetrisi: Application Insights modülü istemci IP adresini toplar. Ayarlanırsa, toplanmaz `X-Forwarded-For` .
* IP adresi ve coğrafi konum verilerinin nasıl toplandığı hakkında daha fazla bilgi edinmek için Application Insights bu [makaleye](./app/ip-collection.md)bakın.

Öğesini, `ClientIpHeaderTelemetryInitializer` IP adresini farklı bir üst bilgiden alacak şekilde yapılandırabilirsiniz. Bazı sistemlerde, örneğin, bir proxy, yük dengeleyici veya CDN ile taşınır `X-Originating-IP` . [Daha fazla bilgi edinin](https://apmtips.com/posts/2016-07-05-client-ip-address/).

İstek telemetrinizi bir haritada göstermek için [Power BI kullanabilirsiniz](app/export-power-bi.md ) .


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Veriler portalda ne kadar süreyle tutulur? Güvenli mi?
[Veri saklama ve gizliliğe][data]göz atın.

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Bir sunucu veya cihaz Azure bağlantısı kesildiğinde Application Insights telemetrisine ne olur?

Web SDK 'Sı dahil olmak üzere tüm SDK 'lerimiz "güvenilir aktarım" veya "güçlü aktarım" içerir. Sunucu veya cihaz Azure ile bağlantıyı kaybettiğinde, telemetri [yerel olarak dosya sistemi](./app/data-retention-privacy.md#does-the-sdk-create-temporary-local-storage) (sunucu SDK 'ları) veya HTML5 oturum depolama (Web SDK) üzerinde depolanır. SDK, bu Telemetriyi düzenli aralıklarla göndermek için alma hizmeti, "eski" (Günlükler için 48 saat, ölçümler için 30 dakika) olarak kabul eder. Eski telemetri atılacak. Örneğin, yerel depolama dolduğunda yeniden deneme gerçekleşmeyecektir.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Kişisel veriler telemetriye gönderilebilir mi?

Bu, kodunuzun bu verileri göndermesi durumunda mümkündür. Yığın izlemelerindeki değişkenler kişisel verileri içeriyorsa de bu durum oluşabilir. Geliştirme ekibiniz, kişisel verilerin düzgün şekilde işlenmesini sağlamak için risk değerlendirmeleri yürütmelidir. [Veri saklama ve gizlilik hakkında daha fazla bilgi edinin](app/data-retention-privacy.md).

Coğrafi konum öznitelikleri arandığında, istemci Web adresinin **Tüm** sekizliklerin her zaman 0 olarak ayarlanmış olması.

[Application Insights JavaScript SDK 'sı](app/javascript.md) varsayılan olarak otomatik tamamlamada herhangi bir kişisel veri içermez. Ancak, uygulamanızda kullanılan bazı kişisel veriler SDK tarafından alınabilir (örneğin, içindeki tam adlar `window.title` veya XHR URL sorgu parametrelerinde hesap kimlikleri). Özel kişisel veri maskeleme için bir [telemetri başlatıcısı](app/api-filtering-sampling.md#javascript-web-applications)ekleyin.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Izleme anahtarım, Web sayfası kaynağı 'nda görünür.

* Bu, izleme çözümlerinde yaygın bir uygulamadır.
* Verilerinizi çalmak için kullanılamaz.
* Verilerinizi eğmek veya uyarıları tetiklemek için kullanılabilir.
* Herhangi bir müşterinin bu soruna sahip olduğunu duymadı.

Şunları yapabilirsiniz:

* İstemci ve sunucu verileri için iki ayrı Izleme anahtarı (ayrı Application Insights kaynakları) kullanın. Veya
* Sunucunuzda çalışan bir ara sunucu yazın ve web istemcisinin bu proxy üzerinden veri göndermesini sağlayabilirsiniz.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Nasıl yaparım? bkz. tanılama aramasında veri gönderme?
POST verilerini otomatik olarak günlüğe kaydetme, ancak bir TrackTrace çağrısını kullanabilirsiniz: verileri ileti parametresine koy. Bu, dize özelliklerindeki sınırlardan daha uzun bir boyut sınırına sahiptir, ancak bunu filtreleyememiş olursunuz.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Tek veya birden çok Application Insights kaynak kullanmalı mıyım?

Tek bir iş sistemindeki tüm bileşenler veya roller için tek bir kaynak kullanın. Geliştirme, test ve sürüm sürümleri için ve bağımsız uygulamalar için ayrı kaynaklar kullanın.

* [Tartışmaya buraya bakın](app/separate-resources.md)
* [Örnek-çalışan ve Web rollerine sahip bulut hizmeti](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>İzleme anahtarı dinamik olarak değiştirilsin mi Nasıl yaparım??

* [Burada tartışma](app/separate-resources.md)
* [Örnek-çalışan ve Web rollerine sahip bulut hizmeti](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Kullanıcı ve oturum sayıları nelerdir?

* JavaScript SDK 'Sı, Web istemcisinde Kullanıcı tanımlama bilgisini, döndürülen kullanıcıları ve etkinlikleri gruplandırmak için bir oturum tanımlama bilgisini belirler.
* İstemci tarafı komut dosyası yoksa, [sunucuda tanımlama bilgilerini ayarlayabilirsiniz](https://apmtips.com/posts/2016-07-09-tracking-users-in-api-apps/).
* Bir gerçek Kullanıcı, sitenizi farklı tarayıcılarda kullanıyorsa ya da özel/veya farklı makinelere göz atmak veya farklı makineler kullanmak için birden çok kez sayılır.
* Makineler ve tarayıcılar arasında oturum açmış bir kullanıcıyı tanımlamak için, [Setadoğrulayıcısının Catedusercontext ()](app/api-custom-events-metrics.md#authenticated-users)çağrısı ekleyin.

### <a name="how-does-application-insights-generate-device-information-browser-os-language-model"></a>Application Insights cihaz bilgilerini (tarayıcı, işletim sistemi, dil, model) nasıl oluşturur?

Tarayıcı, isteğin HTTP üstbilgisindeki Kullanıcı aracısı dizesini geçirir ve Application Insights alma hizmeti, veri tablolarında ve deneyimlerinde gördüğünüz alanları oluşturmak için [UA ayrıştırıcısı](https://github.com/ua-parser/uap-core) kullanır. Sonuç olarak, Application Insights kullanıcılar bu alanları değiştiremez.

Kullanıcı veya kuruluş, tarayıcı ayarlarında Kullanıcı aracısının gönderilmesini devre dışı bırakırsa, bu veriler eksik veya yanlış olabilir. Ayrıca, [UA ayrıştırıcısı normal ifadeler](https://github.com/ua-parser/uap-core/blob/master/regexes.yaml) tüm cihaz bilgilerini içermeyebilir veya Application Insights en son güncelleştirmeleri benimsemiş olabilir.

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Application Insights her şeyi etkinleştirmem gerekir mi?
| Görmeniz gereken | Nasıl alınır? | Neden istediğiniz |
| --- | --- | --- |
| Kullanılabilirlik grafikleri |[Web testleri](app/monitor-web-app-availability.md) |Web uygulamanızın çalışıyor olduğunu öğrenin |
| Sunucu uygulaması performans: yanıt süreleri,... |[Projenize Application Insights ekleyin](app/asp-net.md) veya [AI durum İzleyicisi sunucuya](app/monitor-performance-live-website-now.md) (veya [bağımlılıklarını izlemek](app/api-custom-events-metrics.md#trackdependency)için kendi kodunuzu yazın) |Performans sorunlarını Algıla |
| Bağımlılık telemetrisi |[AI Durum İzleyicisi sunucuya yükler](app/monitor-performance-live-website-now.md) |Veritabanları veya diğer dış bileşenlerle ilgili sorunları tanılayın |
| Özel durumların yığın izlemelerini al |[Kodunuzda TrackException çağrıları ekleyin](app/asp-net-exceptions.md) (ancak bazıları otomatik olarak bildirilir) |Özel durumları Algıla ve Tanıla |
| Arama günlüğü izlemeleri |[Günlüğe kaydetme bağdaştırıcısı ekleme](app/asp-net-trace-logs.md) |Özel durumları ve performans sorunlarını tanılayın |
| İstemci kullanımı temelleri: sayfa görünümleri, oturumlar,... |[Web sayfalarındaki JavaScript başlatıcısı](app/javascript.md) |Kullanım analizi |
| İstemci özel ölçümleri |[Web sayfalarındaki çağrıları izleme](app/api-custom-events-metrics.md) |Kullanıcı deneyimini geliştirin |
| Sunucu özel ölçümleri |[Sunucudaki aramalar izleniyor](app/api-custom-events-metrics.md) |İş zekası |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Arama ve ölçüm grafiklerindeki sayımlar neden eşit değildir?

[Örnekleme](app/sampling.md) , gerçekten uygulamanızdan portala gönderilen telemetri öğelerinin (istek, özel olay vb.) sayısını azaltır. Arama bölümünde, gerçekten alınan öğe sayısını görürsünüz. Olayların sayısını görüntüleyen ölçüm grafiklerinde, gerçekleşen özgün olay sayısını görürsünüz. 

Aktarılan her öğe, `itemCount` öğenin kaç tane özgün olay temsil ettiğini gösteren bir özellik taşır. Örneklemede örnekleme gözlemlemek için bu sorguyu Analytics 'te çalıştırabilirsiniz:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```

### <a name="how-do-i-move-an-application-insights-resource-to-a-new-region"></a>Application Insights bir kaynağı yeni bir bölgeye taşımak Nasıl yaparım??

Mevcut Application Insights kaynaklarının bir bölgeden diğerine taşınması **Şu anda desteklenmiyor**. Topladığınız geçmiş verileri yeni bir bölgeye **geçirilemez** . Kısmi geçici çözüm şunlardır:

1. Yeni bölgede yepyeni bir Application Insights kaynak ([Klasik](app/create-new-resource.md) veya [çalışma alanı tabanlı](./app/create-workspace-resource.md)) oluşturun.
2. Yeni kaynaktaki özgün kaynağa özgü tüm benzersiz özelleştirmeleri yeniden oluşturun.
3. Uygulamanızı yeni bölge kaynak [izleme anahtarını](app/create-new-resource.md#copy-the-instrumentation-key) veya [bağlantı dizesini](app/sdk-connection-string.md)kullanacak şekilde değiştirin.  
4. Yeni Application Insights kaynağınız ile her şeyin beklendiği gibi çalışmaya devam etmekte olduğunu onaylamak için test edin. 
5. Bu noktada, **tüm geçmiş verilerin kaybolmasına** neden olacak özgün kaynağı silebilirsiniz. Veya veri saklama ayarlarının süresi boyunca geçmiş raporlama amacıyla özgün kaynağı koruyun.

Genellikle yeni bölgedeki kaynak için el ile yeniden oluşturulması veya güncellenmesi gereken benzersiz özelleştirmeler şunlardır:

- Özel panoları ve çalışma kitaplarını yeniden oluşturun. 
- Herhangi bir özel günlük/ölçüm uyarısı kapsamını yeniden oluşturun veya güncelleştirin. 
- Kullanılabilirlik uyarılarını yeniden oluşturun.
- Kullanıcılarınızın yeni kaynağa erişmesi için gerekli olan tüm özel Azure rol tabanlı erişim denetimi (Azure RBAC) ayarlarını yeniden oluşturun. 
- Alma örnekleme, veri saklama, günlük uç ve özel ölçümler ile ilgili ayarları çoğaltın. Bu ayarlar, **kullanım ve tahmini maliyetler** bölmesi aracılığıyla denetlenir.
- [Sürüm ek açıklamaları](./app/annotations.md), [canlı ölçümler güvenli denetim kanalı](app/live-stream.md#secure-the-control-channel) vb. gibi API anahtarlarına bağlı olan herhangi bir tümleştirme Yeni API anahtarları oluşturmanız ve ilişkili tümleştirmeyi güncelleştirmeniz gerekecektir. 
- Klasik kaynaklarda sürekli dışarı aktarmanın yeniden yapılandırılması gerekir.
- Çalışma alanı tabanlı kaynaklardaki tanılama ayarlarının yeniden yapılandırılması gerekir.

> [!NOTE]
> Yeni bir bölgede oluşturmakta olduğunuz kaynak klasik bir kaynağı değiştirirken, [Yeni bir çalışma alanı tabanlı kaynak oluşturma](app/create-workspace-resource.md) veya alternatif olarak [var olan kaynağınızı çalışma alanı temelinde geçirme](app/convert-classic-resource.md)avantajlarının araştırmalarını öneririz. 

### <a name="automation"></a>Otomasyon

#### <a name="configuring-application-insights"></a>Application Insights yapılandırma

Azure Kaynak İzleyicisi kullanarak [PowerShell betikleri yazabilirsiniz](app/powershell.md) :

* Application Insights kaynaklarını oluşturun ve güncelleştirin.
* Fiyatlandırma planını ayarlayın.
* İzleme anahtarını alın.
* Ölçüm uyarısı ekleyin.
* Bir kullanılabilirlik testi ekleyin.

Ölçüm Gezgini raporu ayarlayamazsınız veya sürekli dışarı aktarmayı ayarlayabilirsiniz.

#### <a name="querying-the-telemetry"></a>Telemetriyi sorgulama

[Analiz](./logs/log-query-overview.md) sorguları çalıştırmak için [REST API](https://dev.applicationinsights.io/) kullanın.

### <a name="how-can-i-set-an-alert-on-an-event"></a>Bir olayda nasıl uyarı ayarlayabilirim?

Azure uyarıları yalnızca ölçümlerde bulunur. Olaylarınız gerçekleştiğinde bir değer eşiğine geçen özel bir ölçüm oluşturun. Ardından, ölçüm üzerinde bir uyarı ayarlayın. Her iki yönde de ölçüm eşiğin her kesişilişinde bir bildirim alırsınız. ilk kesişene kadar, ilk değerin yüksek veya düşük olmasından bağımsız olarak bildirim almazsınız; birkaç dakika içinde her zaman bir gecikme vardır.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Bir Azure Web uygulaması ve Application Insights arasında veri aktarımı ücretleri var mı?

* Azure Web uygulamanız Application Insights koleksiyon uç noktasının bulunduğu bir veri merkezinde barındırılıyorsa, ücret alınmaz. 
* Ana bilgisayar veri merkezinizde koleksiyon uç noktası yoksa, uygulamanızın telemetrisi [Azure giden ücretlerine](https://azure.microsoft.com/pricing/details/bandwidth/)tabi olur.

Bu, Application Insights kaynağınızın barındırıldığı yere bağlı değildir. Yalnızca uç noktalarımızın dağıtımına bağlıdır.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Application Insights portalına telemetri gönderebilir miyim?

SDK 'larımızı kullanmanızı ve [SDK API](app/api-custom-events-metrics.md)'sini kullanmanızı öneririz. Çeşitli [platformlar](app/platforms.md)için SDK 'nın çeşitleri vardır. Bu SDK 'lar, arabelleğe alma, sıkıştırma, azaltma, yeniden deneme gibi işlemleri gerçekleştirir. Ancak, alma [şeması](https://github.com/microsoft/ApplicationInsights-dotnet/tree/master/BASE/Schema/PublicSchema) ve [uç nokta Protokolü](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) geneldir.

### <a name="can-i-monitor-an-intranet-web-server"></a>Bir intranet Web sunucusunu izleyebilir miyim?

Evet, ancak güvenlik duvarı özel durumları ya da ara sunucu yeniden yönlendirmeleri ile hizmetlerimize giden trafiğe izin vermeniz gerekir.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- Applicationıdprovider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Hizmetlerin ve IP adreslerinin tam listesini [burada](app/ip-addresses.md)gözden geçirin.

#### <a name="firewall-exception"></a>Güvenlik Duvarı özel durumu

Web sunucunuzun, uç noktalarımıza telemetri göndermesini sağlar. 

#### <a name="gateway-redirect"></a>Ağ Geçidi yönlendirmesi

Yapılandırmanızda bulunan uç noktaların üzerine yazarak intranetteki trafiği intranetinizdeki bir ağ geçidine yönlendirin. Bu "uç nokta" özellikleri, config uygulamanızda yoksa, bu sınıflar örnek ApplicationInsights.config aşağıda gösterilen varsayılan değerleri kullanır. 

Ağ geçidinizin trafiği bitiş noktasının temel adresine yönlendirmelidir. Yapılandırmanızda varsayılan değerleri ile değiştirin `http://<your.gateway.address>/<relative path>` .


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Varsayılan bitiş noktaları ile örnek ApplicationInsights.config:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> Applicationıdprovider, v 2.6.0 'dan başlayarak kullanılabilir.



#### <a name="proxy-passthrough"></a>Proxy geçişi

Proxy geçişi, bir makine düzeyi veya uygulama düzeyi proxy yapılandırılarak elde edilebilir.
Daha fazla bilgi için bkz. [defaultProxy](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)üzerinde DotNet makalesi.
 
 Örnek Web.config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Bir intranet sunucusunda kullanılabilirlik Web testlerini çalıştırabilir miyim?

[Web testleriniz](app/monitor-web-app-availability.md) , dünyanın dört bir yanında dağıtılan varlık noktalarında çalışır. İki çözüm vardır:

* Güvenlik Duvarı kapısı-sunucunuza, [Web testi aracılarının uzun ve değiştirilebilir listesinden](app/ip-addresses.md)Istek yapmasına izin verin.
* İntranetinizin içinden sunucunuza düzenli istek göndermek için kendi kodunuzu yazın. Bu amaçla Visual Studio Web testlerini çalıştırabilirsiniz. Sınayıcı, sonuçları TrackAvailability () API 'sini kullanarak Application Insights gönderebilir.

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Telemetri toplanması ne kadar sürer?

Çoğu Application Insights veri, 5 dakikalık bir gecikme süresine sahiptir. Bazı veriler daha uzun sürebilir; genellikle daha büyük günlük dosyaları. Daha fazla bilgi için bkz. [SLA Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md

### <a name="http-502-and-503-responses-are-not-always-captured-by-application-insights"></a>HTTP 502 ve 503 yanıtları her zaman Application Insights tarafından yakalanmaz

"502 hatalı Ağ Geçidi" ve "503 hizmeti kullanılamıyor" hataları Application Insights tarafından her zaman yakalanmaz. İzleme için yalnızca istemci tarafı JavaScript kullanılıyorsa, bu, hata yanıtının, izlenen JavaScript kod parçacığı ile HTML üstbilgisini içeren sayfadan önce döndürüldüğünden beklenen davranış olabilir. 

Sunucu tarafı izleme özelliği etkin olan bir sunucudan 502 veya 503 yanıtı gönderilmişse, hatalar Application Insights SDK tarafından toplanır. 

Ancak, bir uygulamanın Web sunucusunda sunucu tarafı izlemenin etkin olduğu durumlarda bile 502 veya 503 hatasının Application Insights tarafından yakalanmayacağını hala unutmayın. Birçok modern web sunucusu, istemcinin doğrudan iletişim kurmasına izin vermez, bunun yerine, istemci ve ön uç Web sunucuları arasında bilgileri geri ve ileri geçirmek için ters proxy 'ler gibi çözümler kullanmayı sağlar. 

Bu senaryoda, ters proxy katmanında bir sorun olması nedeniyle istemciye bir 502 veya 503 yanıtı döndürülebilir ve bu, Application Insights tarafından kullanıma hazır olarak yakalanmaz. Bu katmandaki sorunları tespit etmenize yardımcı olmak için, günlüğü ters proxy 'nizden Log Analytics iletmek ve 502/503 yanıtlarını denetlemek için özel bir kural oluşturmanız gerekebilir. 502 ve 503 hatalarının yaygın nedenleri hakkında daha fazla bilgi edinmek için, ["502 hatalı Ağ Geçidi" ve "503 Service unavailable" için Azure App Service sorun giderme makalesine](../app-service/troubleshoot-http-502-http-503.md)başvurun.     


## <a name="opentelemetry"></a>OpenTelemetry

### <a name="what-is-opentelemetry"></a>Opentelemetri nedir?

Observability için yeni bir açık kaynak standardı. Daha fazla bilgi edinin [https://opentelemetry.io/](https://opentelemetry.io/) .

### <a name="why-is-microsoft--azure-monitor-investing-in-opentelemetry"></a>Microsoft/Azure Izleyici neden Opentelemetri 'e yatırım yapıyor?

Müşterilerimize üç nedenden dolayı daha iyi hizmet sunduk:
   1. Daha fazla müşteri senaryosu için desteği etkinleştirin.
   2. Satıcı kilidi olmadan geçiş yapın.
   3. Müşteri saydamlığını ve katılımı artırın.

Ayrıca Microsoft 'un [Açık kaynaklı kaynağı benimseme](https://opensource.microsoft.com/)stratejisiyle de hizalanır.

### <a name="what-additional-value-does-opentelemetry-give-me"></a>Opentelemetri bana hangi ek değer veriyor?

Yukarıdaki nedenlerin yanı sıra, Opentelemetri daha verimli bir şekilde ölçeklenebilir ve diller arasında tutarlı tasarım/yapılandırma sağlar.

### <a name="how-can-i-test-out-opentelemetry"></a>Opentelemetriyi nasıl sınayabilirim?

Azure Izleyici Application Insights erken benimseyen Community ' ye katmak için kaydolun [https://aka.ms/AzMonOtel](https://aka.ms/AzMonOtel) .

### <a name="what-does-ga-mean-in-the-context-of-opentelemetry"></a>GA, Opentelemetri bağlamında ne anlama geliyor?

Opentelemetri topluluğu, genel kullanıma açık ( [GA) tanımlar](https://medium.com/opentelemetry/ga-planning-f0f6d7b5302). Bununla birlikte, Opentelemetriyi "GA", mevcut Application Insights SDK 'Ları ile özellik eşliği anlamına gelmez. Azure Izleyici, Opentelemetri SDK 'Ları özellik [ölçüsüne ulaşıncaya kadar ön toplanmış ölçümler](app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics), [canlı ölçümler](app/live-stream.md), [Uyarlamalı örnekleme](app/sampling.md#adaptive-sampling), [Profil Oluşturucu](app/profiler-overview.md)ve [anlık görüntü hata ayıklayıcısı](app/snapshot-debugger.md) gibi özellikler gerektiren müşteriler için geçerli Application Insights SDK 'lerimizi önermeye devam edecektir.

### <a name="can-i-use-preview-builds-in-production-environments"></a>Üretim ortamlarında önizleme derlemelerini kullanabilir miyim?

Önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

### <a name="whats-the-difference-between-opentelemetry-sdk-and-auto-instrumentation"></a>Opentelemetri SDK 'Sı ve otomatik izleme arasındaki fark nedir?

Opentelemetri belirtimi [SDK](https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/glossary.md#telemetry-sdk)'yi tanımlar. Kısacası, "SDK", uygulamanızın çeşitli bileşenleri genelinde telemetri verilerini toplayan dile özgü bir pakettir ve verileri bir dışarı aktarıcı aracılığıyla Azure Izleyici 'ye gönderir.

Otomatik izleme kavramı (bazen bytecode ekleme, cosilss veya aracı tabanlı olarak adlandırılır), kodunuzu değiştirmeden uygulamanızı işaretleme özelliğine başvurur. Örneğin, daha fazla bilgi için [Opentelemetri Java otomatik Izleme Benioku dosyasına](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/master/README.md) göz atın.

### <a name="whats-the-opentelemetry-collector"></a>Opentelemetri toplayıcısı nedir?

Opentelemetri toplayıcısı, [GitHub Benioku](https://github.com/open-telemetry/opentelemetry-collector#opentelemetry-collector)dosyasında açıklanmıştır. Şu anda Microsoft, Opentelemetri toplayıcısını kullanmaz ve Azure Izleyici Application Insights ' ye gönderen doğrudan dışarı aktarıcılar ' ne bağlıdır.

### <a name="whats-the-difference-between-opencensus-and-opentelemetry"></a>OpenCensus ve Opentelemetri arasındaki fark nedir?

[Opencensus](https://opencensus.io/) , [opentelemetri](https://opentelemetry.io/)için precurcursor. Microsoft, dünya için tek bir Observability standardı olan Opentelemetri oluşturmak için [Opentracing](https://opentracing.io/) ve OpenCensus 'i birlikte getirmeye yardımcı oldu. Azure Izleyici 'nin geçerli [üretimi-önerilen Python SDK 'sı](app/opencensus-python.md) , OpenCensus 'a dayalıdır, ancak sonunda tüm Azure Izleyicisinin SDK 'Ları opentelemetri temelinde olacaktır.


## <a name="container-insights"></a>Kapsayıcı öngörüleri

### <a name="what-does-other-processes-represent-under-the-node-view"></a>Düğüm görünümü altında *diğer süreçler* ne gösterir?

**Diğer süreçler** , düğümünüz üzerinde yüksek kaynak kullanımının kök nedenini açık bir şekilde anlamanıza yardımcı olmaya yöneliktir. Bu, Kapsayıcılı süreçler arasındaki kullanımı kapsayıcısız süreçler ile ayırt etmenizi sağlar.

Bu **diğer süreçler** nelerdir? 

Bunlar, düğümünüz üzerinde çalışan kapsayıcısız işlemlerdir.  

Bunu nasıl hesaplarız?

**Diğer süreçler**  =  *Cadvizörü*  -  'ten toplam kullanım *Kapsayıcılı Işlemden kullanım*

**Diğer süreçler** şunları içerir:

- Kendi kendine yönetilen veya yönetilen Kubernetes kapsayıcısız süreçler 

- Kapsayıcı çalışma zamanı işleme  

- Kubelet  

- Düğümünüz üzerinde çalışan sistem işlemi 

- Düğüm donanımında veya VM 'de çalışan, Kubernetes olmayan diğer iş yükleri 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>ContainerLog tablosunu sorgulıyorum, resim ve ad özelliği değerlerini doldurdum.

Aracı sürümü ciprod12042019 ve üzeri için, varsayılan olarak bu iki özellik her günlük satırı için doldurulmaz ve toplanan günlük verilerinde maliyeti en aza indirir. Bu özellikleri içeren tabloyu, değerlerini sorgulamak için iki seçenek vardır:

#### <a name="option-1"></a>1\. Seçenek 

Sonuçlara bu özellik değerlerini dahil etmek için diğer tabloları birleştirin.

Kapsayıcılarınızda, tablo ve ImageTag özelliklerini, ```ContainerInventory``` Containerıd özelliğine katılarak tablodaki resim ve ImageTag özelliklerini içerecek şekilde değiştirin. Name özelliğini (daha önce tabloda göründüğü gibi ```ContainerLog``` ) KubepodInventory tablosunun ContaineName alanındaki Containerıd özelliğine katılarak dahil edebilirsiniz. Önerilen seçenek budur.

Aşağıdaki örnek, bu alan değerlerinin birleşimlerle nasıl alınacağını anlatan örnek bir ayrıntılı sorgudur.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>2\. Seçenek

Her kapsayıcı günlük satırı için bu özellikler için koleksiyonu yeniden etkinleştirin.

İlk seçenek, söz konusu sorgu değişiklikleri nedeniyle uygun değilse, ```log_collection_settings.enrich_container_logs``` [veri toplama yapılandırma ayarlarında](containers/container-insights-agent-config.md)açıklandığı gibi aracı yapılandırma eşlemesindeki ayarı etkinleştirerek bu alanları toplamayı yeniden etkinleştirebilirsiniz.

> [!NOTE]
> Bu zenginleştirme işlemini gerçekleştirmek için kümedeki her düğümden API sunucusu çağrıları oluşturduğundan, ikinci seçenek 50 taneden fazla düğümü olan büyük kümeler için önerilmez. Bu seçenek ayrıca, toplanan her günlük satırı için veri boyutunu artırır.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Grafana ' de toplanan ölçümleri görüntüleyebilir miyim?

Kapsayıcı öngörüleri, Grafana panolar içinde Log Analytics çalışma alanınızda depolanan ölçümlerin görüntülenmesini destekler. Grafana 'in [Pano deposundan](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) indirebileceğiniz bir şablon sağladık ve özel Grafana panolarında görselleştirmek üzere izlenen kümelerinizdeki ek verileri sorgulama hakkında bilgi edinmenize yardımcı olur. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-container-insights"></a>AKS-Engine kümenizi kapsayıcı öngörüleri ile izleyebilir miyim?

Kapsayıcı öngörüleri, Azure 'da barındırılan AKS-Engine (eski adıyla ACS-Engine) kümeleri için dağıtılan kapsayıcı iş yüklerini izlemeyi destekler. Bu senaryoya yönelik izlemeyi etkinleştirmek için gereken adımlara ilişkin daha fazla bilgi ve bir genel bakış için bkz. [AKS-Engine Için kapsayıcı öngörülerini kullanma](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Neden Log Analytics çalışma alanım 'da veri görmüyorum?

Her gün belirli bir saatte Log Analytics çalışma alanında hiç veri göremiyorsanız, varsayılan 500 MB sınırına veya günlük toplanacak veri miktarını denetlemek için belirlenmiş günlük sınıra ulaşmış olabilirsiniz. Günlük sınıra ulaşıldığında veri toplama durdurulur ve ancak ertesi gün sürdürülür. Veri kullanımınızı gözden geçirmek ve tahmin edilen kullanım desenlerinize göre farklı bir fiyatlandırma katmanına güncelleştirmek için bkz. [günlük verisi kullanımı ve maliyeti](logs/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Containerınventory tablosunda belirtilen kapsayıcı durumları nelerdir?

Containerınventory tablosu, hem durdurulan hem de çalışan kapsayıcılar hakkında bilgiler içerir. Tablo, tüm kapsayıcılar için Docker 'ı sorgulayan (çalışıyor ve durdurulmuş) ve bu verileri Log Analytics çalışma alanına ileten aracının içindeki bir iş akışı tarafından doldurulur.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Nasıl yaparım? *eksik abonelik kayıt* hatası.

**Microsoft. operationsmanagement Için eksik abonelik kaydı** hatası alırsanız, çalışma alanının tanımlandığı abonelikte **Microsoft. operationsmanagement** kaynak sağlayıcısını kaydederek bu sorunu çözebilirsiniz. Bunun nasıl yapılacağını gösteren belgeler [burada](../azure-resource-manager/templates/error-register-resource-provider.md)bulunabilir.

### <a name="is-there-support-for-kubernetes-rbac-enabled-aks-clusters"></a>Kubernetes RBAC etkinleştirilmiş AKS kümeleri için destek var mı?

Kapsayıcı Izleme çözümü, Kubernetes RBAC 'yi desteklemez, ancak kapsayıcı öngörüleri ile desteklenir. Çözüm ayrıntıları sayfası, bu kümelerin verilerini gösteren dikey pencerelerde doğru bilgileri göstermez.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Nasıl yaparım?, KUIN-System ad alanındaki kapsayıcılar için günlük toplamayı Held aracılığıyla etkinleştirmek istiyor musunuz?

Kuto-System ad alanındaki kapsayıcılardan günlük koleksiyonu varsayılan olarak devre dışıdır. Omsagent üzerinde bir ortam değişkeni ayarlanarak günlük toplama etkinleştirilebilir. Daha fazla bilgi için bkz. [kapsayıcı öngörüleri](https://aka.ms/azuremonitor-containers-helm-chart) GitHub sayfası. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Omsagent 'ı yayınlanan en son sürüme güncelleştirmek Nasıl yaparım? mı?

Aracıyı yükseltmeyi öğrenmek için bkz. [Aracı yönetimi](containers/container-insights-manage-agent.md).

### <a name="why-are-log-lines-larger-than-16kb-split-into-multiple-records-in-log-analytics"></a>16 KB 'den büyük günlük satırları neden Log Analytics birden çok kayda bölünür?

Aracı, kapsayıcıların stdout ve stderr 'i yakalamak için [Docker JSON dosya günlüğü sürücüsünü](https://docs.docker.com/config/containers/logging/json-file/) kullanır. Bu günlüğe kaydetme sürücüsü, stdout veya stderr 'den bir dosyaya kopyalanırken [16KB 'den büyük](https://github.com/moby/moby/pull/22982) günlük satırlarını birden çok satıra böler.

### <a name="how-do-i-enable-multi-line-logging"></a>Çok satırlı günlüğe kaydetme etkinleştirilsin mi Nasıl yaparım??

Şu anda kapsayıcı öngörüleri çok satırlı günlüğü desteklemez, ancak kullanılabilir geçici çözümler vardır. Tüm hizmetleri JSON biçiminde yazacak şekilde yapılandırabilir ve ardından Docker/Moby, bunları tek bir satır olarak yazar.

Örneğin, bir örnek node.js uygulaması için aşağıdaki örnekte gösterildiği gibi, günlüğlerinizi bir JSON nesnesi olarak kaydırabilirsiniz:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Bu veriler, sorgulama yaptığınızda Günlükler için Azure Izleyici 'de aşağıdaki örnekteki gibi görünür:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Soruna ilişkin ayrıntılı bir bakış için aşağıdaki [GitHub bağlantısını](https://github.com/moby/moby/issues/22920)inceleyin.

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Canlı günlükleri etkinleştirdiğimde Azure AD hatalarını Nasıl yaparım? çözün misiniz? 

Şu hatayı görebilirsiniz: **istekte belirtilen yanıt URL 'si, uygulama için yapılandırılan yanıt URL 'leriyle eşleşmiyor: ' <uygulama kimliği \> '**. Çözümü çözecek çözüm, Container [Insights ile gerçek zamanlı olarak kapsayıcı verilerini görüntüleme](containers/container-insights-livedata-setup.md#configure-ad-integrated-authentication)makalesinde bulunabilir. 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Ekleme işleminden sonra kümeyi neden yükseltemiyorum?

Bir AKS kümesi için kapsayıcı öngörüleri etkinleştirdikten sonra, kümeyi yükseltmeye çalışırken kümenin verilerini gönderdiği Log Analytics çalışma alanını silersiniz. Bu sorunu geçici olarak çözmek için, izlemeyi devre dışı bırakmanız ve ardından aboneliğinizdeki farklı bir geçerli çalışma alanına başvuruda bulunan yeniden etkinleştirmeniz gerekir. Küme yükseltmesini yeniden gerçekleştirmeye çalıştığınızda, başarıyla işlemeli ve tamamlanmalıdır.  

### <a name="which-ports-and-domains-do-i-need-to-openallow-for-the-agent"></a>Aracı için hangi bağlantı noktalarına ve etki alanlarına açık/izin vermem gerekir?

Azure, Azure ABD kamu ve Azure Çin 21Vianet bulutları ile Kapsayıcılı aracı için gereken ara sunucu ve güvenlik duvarı yapılandırma bilgileri için [ağ güvenlik duvarı gereksinimlerine](containers/container-insights-onboard.md#network-firewall-requirements) bakın.


## <a name="vm-insights"></a>VM öngörüleri

### <a name="can-i-onboard-to-an-existing-workspace"></a>Mevcut bir çalışma alanına ekleyebilir miyim?
Sanal makineleriniz zaten bir Log Analytics çalışma alanına bağlı ise, bu çalışma alanını [desteklenen bölgelerden](vm/vminsights-configure-workspace.md#supported-regions)bırınde olan VM öngörülerine ekleme sırasında kullanmaya devam edebilirsiniz.


### <a name="can-i-onboard-to-a-new-workspace"></a>Yeni bir çalışma alanına ekleyebilir miyim? 
VM 'niz Şu anda mevcut bir Log Analytics çalışma alanına bağlı değilse, verilerinizi depolamak için yeni bir çalışma alanı oluşturmanız gerekir. Azure portal aracılığıyla VM öngörüleri için tek bir Azure VM yapılandırırsanız, yeni bir varsayılan çalışma alanı oluşturma otomatik olarak yapılır.

Betik tabanlı yöntemi kullanmayı seçerseniz, bu adımlar [Azure PowerShell veya Kaynak Yöneticisi şablonu kullanarak VM öngörülerini etkinleştirme](./vm/vminsights-enable-powershell.md) makalesinde ele alınmıştır. 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>VM 'im mevcut bir çalışma alanına zaten bildirildiğimde ne yapmam gerekiyor?
Sanal makinelerinizden zaten veri topluyorsanız, mevcut bir Log Analytics çalışma alanına verileri raporlamak için onu zaten yapılandırmış olabilirsiniz.  Bu çalışma alanı desteklenen bölgelerden birinde olduğu sürece, VM öngörülerini önceden var olan çalışma alanına etkinleştirebilirsiniz.  Zaten kullanmakta olduğunuz çalışma alanı desteklenen bölgelerden birinde değilse, şu anda VM öngörülerine yükleyemezsiniz.  Ek bölgeleri desteklemek için etkin bir şekilde çalışıyoruz.


### <a name="why-did-my-vm-fail-to-onboard"></a>Sanal makinem neden eklenemedi?
Azure portal bir Azure VM 'si eklenirken aşağıdaki adımlar oluşur:

* Bu seçenek işaretliyse, varsayılan bir Log Analytics çalışma alanı oluşturulur.
* Log Analytics Aracısı, gerekli olduğunu tespit ederseniz bir VM uzantısı kullanılarak Azure VM 'lerine yüklenir.  
* VM öngörüleri eşleme bağımlılığı Aracısı, gerekli olduğunu tespit ederseniz bir uzantı kullanılarak Azure VM 'lerine yüklenir. 

Ekleme işlemi sırasında, portalda size bir bildirim durumu döndürmek için yukarıdaki her bir durumu denetliyoruz. Çalışma alanının yapılandırması ve aracı yüklemesi genellikle 5 ila 10 dakika sürer. Portalda izleme verilerini görüntülemek için 5 ila 10 dakika sürer.  

Ekleme işlemini başlattınız ve VM 'nin eklendi olması gerektiğini belirten iletiler görürseniz, VM 'nin işlemi tamamlaması için 30 dakikaya kadar bekleyin. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>VM 'im için Performans grafiklerinde bir veya daha fazla veri görmüyorum
Performans Grafiklerimiz, *ınsightsölçümlerini* tablosunda depolanan verileri kullanacak şekilde güncelleştirilmiştir.  Bu grafiklerdeki verileri görmek için yeni VM öngörüleri çözümünü kullanmak üzere yükseltmeniz gerekir.  Daha fazla bilgi için lütfen [ga SSS bölümüne](vm/vminsights-ga-release-faq.md) bakın.

Disk tablosunda veya bazı performans grafiklerinde performans verilerini görmüyorsanız, performans sayaçlarınız çalışma alanında yapılandırılmamış olabilir. Çözümlemek için aşağıdaki [PowerShell betiğini](./vm/vminsights-enable-powershell.md)çalıştırın.


### <a name="how-is-vm-insights-map-feature-different-from-service-map"></a>VM öngörüleri eşleme özelliği Hizmet Eşlemesi 'den farklı?
VM öngörüleri eşleme özelliği Hizmet Eşlemesi tabanlıdır, ancak aşağıdaki farklılıklara sahiptir:

* Harita görünümüne VM dikey penceresinden ve Azure Izleyici altındaki VM öngörülerine erişilebilir.
* Haritadaki bağlantılar artık tıklatılabilir ve seçili bağlantı için yan bölmede bağlantı ölçüm verilerinin bir görünümünü görüntüler.
* Daha karmaşık haritaları daha iyi destekleyecek şekilde eşlemeler oluşturmak için kullanılan yeni bir API vardır.
* İzlenen VM 'Ler artık istemci grubu düğümüne dahildir ve halka grafik gruptaki izlenen ve izlenmeyen sanal makinelerin oranını gösterir.  Grup genişletildiğinde makine listesini filtrelemek için de kullanılabilir.
* İzlenen sanal makineler artık sunucu bağlantı noktası grubu düğümlerine dahildir ve halka grafik gruptaki izlenen ve izlenmeyen makinelerin oranını gösterir.  Grup genişletildiğinde makine listesini filtrelemek için de kullanılabilir.
* Harita stili, Application Insights 'tan uygulama haritası ile daha tutarlı olacak şekilde güncelleştirilmiştir.
* Yan paneller güncelleştirilmiştir ve Hizmet Eşlemesi-Güncelleştirme Yönetimi, Değişiklik İzleme, güvenlik ve hizmet masası 'nda desteklenen tam tümleştirme kümesine sahip değildir. 
* Eşlenecek grupları ve makineleri seçme seçeneği güncelleştirildi ve artık abonelikleri, kaynak gruplarını, Azure sanal makine ölçek kümelerini ve bulut hizmetlerini desteklemektedir.
* VM öngörüleri eşleme özelliğinde yeni Hizmet Eşlemesi makine grupları oluşturamazsınız.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Performans grafiklerim neden noktalı çizgiler gösteriyor?
Bu, birkaç nedenden dolayı oluşabilir.  Veri koleksiyonunda bir boşluk olduğu durumlarda, satırları noktalı olarak belirledik.  Performans sayaçları etkin için veri örnekleme sıklığını değiştirdiyseniz (varsayılan ayar, her 60 saniyede bir veri toplamaktır), grafik için bir dar zaman aralığı seçerseniz ve örnekleme sıklığınızdan grafik için kullanılan demet boyutundan daha az olduğunda (örneğin, örnekleme sıklığı 10 dakikada bir ve grafikteki her demet 5 dakika olduğunda) grafikte noktalı çizgileri görebilirsiniz.  Görüntülenecek daha geniş bir zaman aralığı seçilmesi, grafik çizgilerinin bu durumda nokta yerine düz çizgiler olarak görünmesine neden olmalıdır.

### <a name="are-groups-supported-with-vm-insights"></a>Gruplar VM öngörüleri ile destekleniyor mu?
Evet, bağımlılık aracısını yükledikten sonra abonelikleri, kaynak grubunu, sanal makine ölçek kümelerini ve bulut hizmetlerini temel alan grupları göstermek için VM 'lerden bilgi topladık.  Hizmet Eşlemesi kullanıyorsanız ve makine grupları oluşturduysanız, bunlar da görüntülenir.  Siz görüntülemekte olduğunuz çalışma alanı için oluşturduysanız, bilgisayar grupları gruplar filtresinde da görünür. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Nasıl yaparım? toplam Performans grafiklerinde 95. yüzdebirlik satırını neyin yönlendirdiğini görmek için ayrıntılara bakın.
Varsayılan olarak, liste, en düşük 5 ' lik değere sahip makineleri gösteren kullanılabilir bellek grafiği dışında, seçili ölçüm için en yüksek 95. yüzdebirlik değerini içeren VM 'Leri gösterecek şekilde sıralanır.  Grafiğe tıklandığında, uygun ölçüm seçili olan **Ilk N liste**  görünümü açılır.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Harita özelliği farklı VNET 'lerde ve alt ağlarda yinelenen IP 'Leri nasıl işler?
IP aralıklarını VM 'Ler veya alt ağlar ve sanal makine ölçek kümeleri arasında çoğaltdıysanız, VM Öngörüler Haritalarınızın yanlış bilgileri görüntülemesine neden olabilir. Bu bilinen bir sorundur ve bu deneyimi geliştirme seçeneklerini araştırıyoruz.

### <a name="does-map-feature-support-ipv6"></a>Harita özelliği IPv6 'Yı destekliyor mu?
Map özelliği şu anda yalnızca IPv4 'Ü destekliyor ve IPv6 desteğini araştırıyoruz. IPv6 içinde tünel oluşturulan IPv4 de destekliyoruz.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Bir kaynak grubu veya başka bir büyük grup için bir harita yüklediğimde haritanın görüntülemesi zordur
Büyük ve karmaşık yapılandırmaların işlenmesi amacıyla haritada geliştirmeler yaptığımız için, bir haritanın çok fazla düğüm, bağlantı ve düğüm kümesi olarak çalışan bir düğüm olabileceğini fark ettik.  Ölçeklenebilirliği artırmak için desteği geliştirmeye devam etmek için çalışıyoruz.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Performans sekmesindeki ağ grafiği neden Azure VM 'ye Genel Bakış sayfasındaki ağ grafiğinden farklı görünüyor?

Bir Azure VM 'ye ilişkin genel bakış sayfası, konağın Konuk VM 'deki etkinlik ölçüsünün bulunduğu grafikleri görüntüler.  Azure VM 'de ağ grafiğine genel bakış için yalnızca faturalandırılacak ağ trafiği görüntülenir.  Bu, sanal ağlar arası trafiği içermez.  VM öngörüleri için gösterilen veriler ve grafikler, Konuk VM 'deki verileri temel alır ve ağ grafiği, sanal ağlar dahil olmak üzere bu VM 'ye gelen ve giden tüm TCP/IP trafiğini görüntüler.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Yanıt süresi, VMConnection 'da depolanan ve bağlantı panelinde ve çalışma kitaplarında görüntülenmekte olan veriler için nasıl ölçülür?

Yanıt süresi yaklaşık bir değer. Uygulamanın kodunu işaretlemediğimiz için, bir isteğin ne zaman başladığını ve yanıtın ne zaman ulaştığında gerçekten haberdar ettik. Bunun yerine, bir bağlantı üzerinden gönderilen verileri gözlemliyoruz ve sonra bu bağlantıya geri dönüş yaptık. Aracımız, bu gönderme ve alma girişimlerini takip eder ve bunları eşleştirmeye çalışır: bir dizi Gönderen, sonrasında bir alma dizisi, istek/yanıt çifti olarak yorumlanır. Bu işlemler arasındaki zamanlama yanıt süresi olur. Ağ gecikmesi ve sunucu işleme süresi de dahil olur.

Bu yaklaşık, istek/yanıt tabanlı protokollerde iyi sonuç verir: tek bir istek bağlantı üzerinde geçer ve tek bir yanıt ulaşır. Bu, HTTP (S) için (ardışık düzen olmadan), ancak diğer protokollerin karşılanmadığı durumdur.

### <a name="are-there-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Log Analytics ücretsiz fiyatlandırma plansın varsa sınırlamalar var mı?
*Ücretsiz* fiyatlandırma katmanını kullanarak Azure izleyici 'yi bir Log Analytics çalışma alanıyla YAPıLANDıRDıYSANıZ, VM öngörüleri eşleme özelliği yalnızca çalışma alanına bağlı beş bağlı makineyi destekleyecektir. Ücretsiz bir çalışma alanına bağlı beş VM varsa, VM 'lerden birinin bağlantısını kesip daha sonra yeni bir VM 'yi bağladığınızda yeni VM izlenmiyor ve harita sayfasında yansıtılmaz.  

Bu koşulun altında, VM 'yi açıp, zaten VM 'ye yüklendikten sonra bile sol bölmedeki **Öngörüler** ' i seçtiğinizde **Şimdi dene** seçeneği istenir.  Ancak, bu VM, VM öngörülerine eklendi değilse, genellikle bu sanal makine için bir seçenek istenmez. 

## <a name="sql-insights-preview"></a>SQL Insights (Önizleme)

### <a name="what-versions-of-sql-server-are-supported"></a>Hangi SQL Server sürümleri desteklenir?
SQL Server 2012 ve daha yeni sürümleri destekliyoruz. Daha fazla ayrıntı için bkz. [Desteklenen sürümler](insights/sql-insights-overview.md#supported-versions) .

### <a name="what-sql-resource-types-are-supported"></a>Hangi SQL kaynak türleri destekleniyor?
- Azure SQL Veritabanı
- Azure SQL Yönetilen Örnek
- Azure sanal makinelerinde SQL Server ( [SQL sanal makine](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) sağlayıcısına kayıtlı sanal makinelerde çalışan SQL Server)
- Azure VM 'Leri ( [SQL sanal makine](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) sağlayıcısına kayıtlı olmayan sanal makinelerde çalışan SQL Server)

Daha ayrıntılı bilgi edinmek ve destek veya sınırlı destek içermeyen senaryolar hakkında ayrıntılar için bkz. [Desteklenen sürümler](insights/sql-insights-overview.md#supported-versions) .

### <a name="what-operating-systems-for-the-virtual-machine-running-sql-server-are-supported"></a>SQL Server çalıştıran sanal makine için hangi işletim sistemleri destekleniyor?
Azure sanal makinelerinde SQL Server için [Windows](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms) ve [Linux](../azure-sql/virtual-machines/linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create) belgeleri tarafından belirtilen tüm işletim sistemlerini destekliyoruz.

### <a name="what-operating-system-for-the-monitoring-virtual-machine-are-supported"></a>İzleme sanal makinesi için hangi işletim sistemi destekleniyor?
Ubuntu 18,04 Şu anda izleme sanal makinesi için desteklenen tek işletim sistemidir.

### <a name="where-will-the-monitoring-data-be-stored-in-log-analytics"></a>İzleme verileri Log Analytics içinde depolanacak mi?
Tüm izleme verileri, **ınsightsölçümlerini** tablosunda depolanır. **Kaynak** sütununun değeri vardır `solutions.azm.ms/telegraf/SqlInsights` . **Ad alanı** sütununda ile başlayan değerler vardır `sqlserver_` .

### <a name="how-often-is-data-collected"></a>Veriler ne sıklıkta toplanır? 
Veri toplama sıklığı özelleştirilebilir. Varsayılan sıklıklardan Ayrıntılar için bkz. [SQL Insights tarafından toplanan veriler](../insights/../azure-monitor/insights/sql-insights-overview.md#data-collected-by-sql-insights) ve frekansları özelleştirme yönergeleri için bkz. [SQL izleme profili oluşturma](../insights/../azure-monitor/insights/sql-insights-enable.md#create-sql-monitoring-profile) . 

## <a name="next-steps"></a>Sonraki adımlar
Sorunuz burada yanıtlanmazsa, ek sorular ve yanıtlar için aşağıdaki forumlara başvurabilirsiniz.

- [Log Analytics](/answers/topics/azure-monitor.html)
- [Uygulama Bilgileri](/answers/topics/azure-monitor.html)

Azure Izleyici hakkında genel geri bildirimde bulunmak için lütfen [geri bildirim forumunu](https://feedback.azure.com/forums/34192--general-feedback)ziyaret edin.
