---
title: Azure Izleyicisine genel bakış | Microsoft Docs
description: Azure hizmetleriniz ve uygulamalarınız için eksiksiz bir izleme stratejisine katkıda bulunan Microsoft hizmetleri ve işlevlerine genel bakış.
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 11/17/2019
ms.openlocfilehash: afe6e82c3cc9773fbf0c17992fd6894d199d177f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937252"
---
# <a name="azure-monitor-overview"></a>Azure İzleyici'ye genel bakış

Azure Izleyici, uygulamalarınızın ve hizmetlerinizin kullanılabilirliğini ve performansını en üst düzeye çıkarmanıza yardımcı olur. Bulut ve şirket içi ortamlarınızdaki telemetri toplama, çözümleme ve üzerinde işlem yapmaya yönelik kapsamlı bir çözüm sunar. Bu bilgiler, uygulamalarınızın nasıl çalıştığını ve bunları etkileyen sorunları ve bağımlı oldukları kaynakları nasıl tanımladığınızı anlamanıza yardımcı olur.

Azure Izleyici ile neler yapabileceğinize ilişkin birkaç örnek şunlardır:

- [Application Insights](app/app-insights-overview.md)ile uygulamalarda ve bağımlılıklarda sorunları algılayın ve tanılayın.
- Altyapı sorunlarını [VM öngörüleri](vm/vminsights-overview.md) ve [kapsayıcı öngörüleri](containers/container-insights-overview.md)ile ilişkilendirin.
- Sorun giderme ve ayrıntılı tanılama için [Log Analytics](logs/log-query-overview.md) izleme verilerinize ulaşın.
- [Akıllı uyarılarla](alerts/alerts-smartgroups-overview.md) ve [Otomatik eylemlerle](alerts/alerts-action-rules.md)birlikte işlem sırasında işlemler desteklenir.
- Azure [panoları](visualize/tutorial-logs-dashboards.md) ve [çalışma kitapları](visualize/workbooks-overview.md)ile görselleştirme oluşturun.
- [Azure Izleyici ölçümlerini](./essentials/data-platform-metrics.md)kullanarak [izlenen kaynaklardan](./monitor-reference.md) veri toplayın.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Genel Bakış

Aşağıdaki diyagramda Azure İzleyici'nin üst düzey bir görünümü sunulmaktadır. Diyagramın merkezinde, Azure Izleyici tarafından kullanılan iki temel veri türü olan ölçümler ve Günlükler için veri depoları bulunur. Sol tarafta, bu [veri depolarını](data-platform.md)dolduran [izleme verilerinin kaynakları](agents/data-sources.md) bulunur. Sağ tarafta, Azure Izleyici 'nin bu toplanan verilerle gerçekleştirdiği farklı işlevler bulunur. Bu işlem, analiz, uyarı ve dış sistemlere akış gibi eylemleri içerir.

![Azure İzleyici'ye genel bakış](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>İzleme veri platformu

Azure Izleyici tarafından toplanan tüm veriler, iki temel tür, [ölçüm ve günlüklerden](data-platform.md)birine uyar. [Ölçümler](essentials/data-platform-metrics.md) , belirli bir noktadaki sistemin bazı yönlerini tanımlayan sayısal değerlerdir. Bunlar hafif ve neredeyse gerçek zamanlı senaryolar desteklenebilecek bir şekilde kullanılabilir. [Günlükler](logs/data-platform-logs.md) , her tür için farklı özellik kümelerine sahip kayıtlarla düzenlenmiş farklı türlerde veriler içerir. Olaylar ve izlemeler gibi telemetri, tüm analiz için birleştirilebilmesi için performans verilerinin yanı sıra Günlükler olarak da depolanır.

Birçok Azure kaynağı için, Azure Izleyici tarafından toplanan verileri Azure portal genel bakış sayfasında görürsünüz. Örneğin, herhangi bir sanal makineye göz atın ve performans ölçümlerini görüntüleyen birkaç grafik görürsünüz. Azure portal, verileri [Ölçüm Gezgini](essentials/metrics-charts.md) 'nde açmak için herhangi bir grafiğe tıklayın. Bu, zaman içinde birden çok ölçümün değerlerini grafik oluşturmanızı sağlar.  Grafikleri etkileşimli olarak görüntüleyebilir veya diğer görselleştirmelerle görüntülemek için bunları bir panoya sabitleyebilirsiniz.

![Diyagramda, Görselleştirmelerde kullanılacak Ölçüm Gezgini ölçüm verileri gösterilir.](media/overview/metrics.png)

Azure Izleyici tarafından toplanan günlük verileri, toplanan verileri hızlı bir şekilde almak, birleştirmek ve analiz etmek için [sorgularla](logs/log-query-overview.md) analiz edilebilir.  Azure portal [Log Analytics](./logs/log-query-overview.md) kullanarak sorgular oluşturabilir ve test edebilirsiniz. Daha sonra, farklı araçları kullanarak verileri doğrudan çözümleyebilir veya [görselleştirmeler](visualizations.md) veya [Uyarı kuralları](alerts/alerts-overview.md)ile kullanmak üzere sorgular kaydedebilirsiniz.

Azure Izleyici, basit günlük sorguları için uygun olan [kusto sorgu dilinin](/azure/kusto/query/) bir sürümünü kullanır, ayrıca toplamalar, birleşimler ve akıllı analiz gibi gelişmiş işlevleri de içerir. [Birden çok ders](logs/get-started-queries.md)kullanarak sorgu dilini hızlıca öğrenebilirsiniz.  [SQL](/azure/data-explorer/kusto/query/sqlcheatsheet) ve [Splunk](/azure/data-explorer/kusto/query/splunk-cheat-sheet)’u önceden bilen kullanıcılara belirli yönergeler sağlanır.

![Diyagramda, analiz için Log Analytics günlük verilerinin akışı gösterilmektedir.](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Azure İzleyici hangi verileri toplar?

Azure Izleyici, [çeşitli kaynaklardan](monitor-reference.md)veri toplayabilir. Bu, uygulamanızdaki herhangi bir işletim sistemi ve hizmeti, platformun kendisine kadar olan bu aralıkları kullanır. Azure İzleyici aşağıdaki katmanların her birinden veri toplar:

- **Uygulama izleme verileri**: platformundan bağımsız olarak, yazdığınız kodun performansı ve işlevleri hakkındaki veriler.
- **Konuk işletim sistemi izleme verileri**: uygulamanızın üzerinde çalıştığı işletim sistemiyle ilgili veriler. Bu bileşen Azure'da, başka bir bulutta veya şirket içi ortamda çalışıyor olabilir. 
- **Azure kaynağı izleme verileri**: Bir Azure kaynağının çalışması hakkında veriler.
- **Azure aboneliği izleme verileri**: bir Azure aboneliğinin işlemi ve yönetimiyle ilgili veriler ve Azure 'un sistem durumu ve işlemi hakkındaki veriler. 
- **Azure kiracı izleme verileri**: Azure Active Directory gibi kiracı düzeyinde Azure hizmetlerinin çalışması hakkındaki veriler.

Bir Azure aboneliği oluşturup sanal makineler ve web uygulamaları gibi kaynaklar eklemeye başladığınızda Azure İzleyici veri toplamaya başlar.  Kaynaklar oluşturulduğunda veya değiştirildiğinde [etkinlik günlükleri](essentials/platform-logs-overview.md) kaydı. [Ölçümler](essentials/data-platform-metrics.md) , kaynağın nasıl çalıştığını ve tükettiği kaynakları bildirir. 

Topladığınızı verileri, kaynakların iç işlemine genişletmek için [tanılamayı etkinleştirin](essentials/platform-logs-overview.md) .  Konuk işletim sistemlerinden telemetri toplamak için işlem kaynaklarına [bir aracı ekleyin](agents/agents-overview.md) . 

Sayfa görünümleri, uygulama istekleri ve özel durumlar dahil olmak üzere ayrıntılı bilgi toplamak için [Application Insights](app/app-insights-overview.md) uygulamanız için izlemeyi etkinleştirin. Kullanıcı trafiğinin benzetimini yapmak için bir [Kullanılabilirlik testi](app/monitor-web-app-availability.md) yapılandırarak uygulamanızın kullanılabilirliğini daha da doğrulayın.

### <a name="custom-sources"></a>Özel kaynaklar

Azure Izleyici, [veri TOPLAYıCı API](logs/data-collector-api.md)'sini kullanarak HERHANGI bir rest istemcisinden günlük verileri toplayabilir. Bu, özel izleme senaryoları oluşturmanıza ve izlemeyi diğer kaynaklardan açığa çıkaran kaynaklara genişletmenize olanak tanır.

## <a name="insights"></a>Insights
İzleme verileri yalnızca, bilgi işlem ortamınızın işlemine ilişkin görünürlüğünüzü artırabilmesini sağlar. [Öngörüler](monitor-reference.md#insights-and-core-solutions) , belirli Azure hizmetleri için özelleştirilmiş bir izleme deneyimi sağlar. En az yapılandırma gerektirir ve kritik kaynakların işlemine ilişkin görünürlüğünüzü artırabilir.

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) , Web uygulamalarınızın bulutta veya şirket içinde barındırıldığından kullanılabilirlik, performans ve kullanımını izler. Uygulamanızın işlemlerinde derin Öngörüler sunmak için Azure Izleyici 'de güçlü veri analizi platformunu kullanır. Bir kullanıcının bunları rapor etmesini beklemeden hataları tanılamanıza olanak sağlar. Application Insights, çeşitli geliştirme araçlarına bağlantı noktaları içerir ve DevOps işlemlerinizi desteklemek için Visual Studio ile tümleşir.

![App Insights](media/overview/app-insights.png)

### <a name="container-insights"></a>Kapsayıcı öngörüleri
[Kapsayıcı öngörüleri](containers/container-insights-overview.md) , Azure Kubernetes Service (aks) üzerinde barındırılan yönetilen Kubernetes kümelerine dağıtılan kapsayıcı iş yüklerinin performansını izler. Bu, ölçüm API 'SI aracılığıyla Kubernetes 'te bulunan denetleyicilerden, düğümlerden ve kapsayıcılardan ölçümler toplayarak performans görünürlüğü sağlar. Kapsayıcı günlükleri de toplanır.  Kubernetes kümelerinden izlemeyi etkinleştirdikten sonra, bu ölçümler ve Günlükler, Linux için Log Analytics aracısının kapsayıcılı bir sürümü aracılığıyla sizin için otomatik olarak toplanır.

![Kapsayıcı durumu](media/overview/container-insights.png)

### <a name="vm-insights"></a>VM öngörüleri
[VM öngörüleri](vm/vminsights-overview.md) , Azure sanal makinelerinizi (VM) ölçeklendirerek izler. Windows ve Linux sanal makinelerinizin performansını ve sistem durumunu analiz eder ve dış süreçlerdeki farklı süreçlerini ve birbirine bağlı bağımlılıkları tanımlar. Çözüm, şirket içinde veya başka bir bulut sağlayıcısında barındırılan VM 'Ler için performans ve uygulama bağımlılıklarını izleme desteği içerir.  


![VM öngörüleri](media/overview/vm-insights.png)


## <a name="responding-to-critical-situations"></a>Kritik durumlara yanıt verme
İzleme verilerini etkileşimli olarak çözümlemenize izin vermenin yanı sıra, etkili bir izleme çözümü, topladığı verilerde tanımlanan kritik koşullara uygun bir şekilde yanıt verebilmelidir. Bu, bir sorunu araştırmaktan sorumlu bir yöneticiye bir metin veya posta gönderiyor olabilir. Ya da bir hata koşulunu düzeltmeyi deneyen otomatikleştirilmiş bir işlem başlatabilirsiniz.


### <a name="alerts"></a>Uyarılar
[Azure izleyici 'Deki uyarılar](alerts/alerts-overview.md) , kritik koşullar ve potansiyel olarak düzeltici eylem gerçekleştirmeye yönelik olarak size bir bildirim gönderir. Ölçümlere dayalı uyarı kuralları, sayısal değerlere göre neredeyse gerçek zamanlı uyarılar sağlar. Günlükleri temel alan kurallar, birden fazla kaynaktaki veriler arasında karmaşık mantık sağlar.

Azure Izleyici 'deki uyarı kuralları, birden çok kural arasında paylaşılabilen benzersiz alıcı kümeleri ve Eylemler içeren [eylem gruplarını](alerts/action-groups.md)kullanır. Gereksinimlerinize bağlı olarak, eylem grupları, uyarıları dış eylemleri başlatacak veya ıTSM araçlarınızla tümleştirilebilen Web kancaları kullanarak bu tür eylemleri gerçekleştirebilir.

![Ekran görüntüsü, Azure Izleyici 'deki uyarıları önem derecesi, toplam uyarı ve diğer bilgilerle gösterir.](media/overview/alerts.png)

### <a name="autoscale"></a>Otomatik Ölçeklendirme
Otomatik ölçeklendirme, uygulamanızdaki yükü işlemek için çalışan kaynakların doğru miktarına sahip olmasını sağlar. Yük arttıkça kaynakların ne zaman otomatik olarak ekleneceğini öğrenmek için Azure Izleyici tarafından toplanan ölçümleri kullanan kurallar oluşturun. Boşta duran kaynakları kaldırarak tasarruf edin. Minimum ve maksimum örnek sayısını ve kaynakların ne zaman artırması veya azaltılacağı mantığı belirtirsiniz.

![Diyagramda, Işlemci zamanı 80 > etiketli ve en az sayıda sunucu, geçerli kapasite olarak üç sunucu ve en fazla beş olarak işaretlenmiş bir satırdaki çeşitli sunucularla otomatik ölçeklendirme gösterilmektedir.](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>İzleme verileri görselleştiriliyor
Grafikler ve tablolar gibi [görselleştirmeler](visualizations.md) , izleme verilerini özetlemeye ve farklı kitlelere sunmaya yönelik etkili araçlardır. Azure Izleyici, izleme verilerini görselleştirmeye yönelik kendi özelliklerine sahiptir ve farklı kitlelere yayımlamak için diğer Azure hizmetlerinden yararlanır.

### <a name="dashboards"></a>Panolar
[Azure panoları](../azure-portal/azure-portal-dashboards.md) , farklı türlerde verileri [Azure Portal](https://portal.azure.com)tek bir bölmede birleştirmenizi sağlar. İsterseniz panoyu diğer Azure kullanıcılarıyla paylaşabilirsiniz. Herhangi bir günlük sorgusunun veya ölçüm grafiğinin çıkışını bir Azure panosuna ekleyin. Örneğin, bir ölçüm grafiği, etkinlik günlüklerinin bir tablosu, Application Insights bir kullanım grafiği ve günlük sorgusunun çıkışı gösteren kutucukları birleştiren bir pano oluşturabilirsiniz.

![Ekran görüntüsünde, diğer özelleştirilebilir bilgilerle birlikte uygulama ve güvenlik kutucukları içeren bir Azure panosu gösterilir.](media/overview/dashboard.png)

### <a name="workbooks"></a>Çalışma Kitapları
[Çalışma kitapları](visualize/workbooks-overview.md) , veri analizi ve Azure Portal zengin görsel raporların oluşturulması için esnek bir tuval sağlar. Azure 'da birden çok veri kaynağına dokunmanıza ve bunları Birleşik etkileşimli deneyimler halinde birleştirmeye olanak tanır. Öngörülerle birlikte sunulan çalışma kitaplarını kullanın veya önceden tanımlanmış şablonlardan kendinizinkini oluşturun.


![Çalışma kitapları örneği](media/overview/workbooks.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) , çeşitli veri kaynakları üzerinde etkileşimli görselleştirmeler sağlayan bir iş analizi hizmetidir. Bu, verilerin kuruluşunuzun içindeki ve dışındaki diğer kullanıcılara açık hale getirilmesi için etkili bir araçtır. Bu ek görselleştirmelerin avantajlarından yararlanmak için [Azure izleyici 'den günlük verilerini otomatik olarak içeri aktarmak](./visualize/powerbi.md) üzere Power BI yapılandırabilirsiniz.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Verileri tümleştirme ve dışarı aktarma
Genellikle Azure Izleyicisini diğer sistemlerle tümleştirme ve izleme verilerinizi kullanan özel çözümler oluşturma gerekliliği vardır. Diğer Azure Hizmetleri, bu tümleştirmeyi sağlamak için Azure Izleyici ile birlikte çalışır.

### <a name="event-hub"></a>Olay Hub'ı
[Azure Event Hubs](../event-hubs/index.yml) , bir akış platformu ve olay alma hizmetidir. Herhangi bir gerçek zamanlı analiz sağlayıcısı veya toplu işlem/depolama bağdaştırıcısı kullanarak verileri dönüştürebilir ve saklayabilir. [Azure izleyici verilerini](essentials/stream-monitoring-data-event-hubs.md) Iş ortağı SIEM ve izleme araçlarına akışa almak için Event Hubs kullanın.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) , farklı sistemler ve hizmetlerle tümleştirilen iş akışlarını kullanarak görevleri ve iş süreçlerini otomatikleştirmenize olanak tanıyan bir hizmettir. Azure Izleyici 'de ölçümleri ve günlükleri okuma ve yazma işlemleri için etkinlik vardır. Bu, diğer birçok sistemlerle tümleştirerek iş akışları oluşturmanıza olanak tanır.


### <a name="api"></a>API
Oluşturulan uyarılara erişmenin yanı sıra Azure Izleyici 'ye ve bunlara yönelik ölçümleri ve günlükleri okuyup yazmak için birden çok API kullanılabilir. Ayrıca, uyarıları yapılandırabilir ve alabilirsiniz. Bu, Azure Izleyici ile tümleştirilen özel çözümler oluşturmak için aslında sınırsız olanaklar sunar.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdakiler hakkında daha fazla bilgi edinin:

* Azure Izleyici tarafından toplanan veriler için [ölçümler ve Günlükler](./data-platform.md#metrics) .
* Uygulamanızın farklı bileşenlerinin telemetri gönderme yöntemi için [veri kaynakları](agents/data-sources.md) .
* Toplanan verileri çözümlemek için [günlük sorguları](logs/log-query-overview.md) .
* Bulut uygulamalarını ve hizmetlerini izlemeye yönelik [en iyi uygulamalar](/azure/architecture/best-practices/monitoring) .