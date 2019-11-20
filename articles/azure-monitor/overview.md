---
title: Azure Izleyicisine genel bakış | Microsoft Docs
description: Azure hizmetleriniz ve uygulamalarınız için eksiksiz bir izleme stratejisine katkıda bulunan Microsoft hizmetleri ve işlevlerine genel bakış.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: d02ea18422e20aa2fede4f25cdffa985607e7b5c
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597337"
---
# <a name="azure-monitor-overview"></a>Azure Izleyicisine genel bakış

Azure Izleyici, bulut ve şirket içi ortamlarınızdaki telemetri toplama, çözümleme ve üzerinde işlem yapmaya yönelik kapsamlı bir çözüm sunarak uygulamalarınızın ve hizmetlerinizin kullanılabilirliğini ve performansını en üst düzeye çıkarır. Uygulamalarınızın performansını anlamanıza ve uygulamalarla bağlı oldukları kaynakları etkileyen sorunları önceden tespit etmenize yardımcı olur.

Azure Izleyici ile neler yapabileceğinize ilişkin birkaç örnek şunlardır:

- [Application Insights](app/app-insights-overview.md)ile uygulamalarda ve bağımlılıklarda sorunları algılayın ve tanılayın.
- [VM'ler için Azure izleyici](insights/vminsights-overview.md) ve [kapsayıcılar için Azure izleyici](insights/container-insights-overview.md)ile altyapı sorunlarını ilişkilendirme.
- Sorun giderme ve ayrıntılı tanılama için [Log Analytics](log-query/log-query-overview.md) izleme verilerinize ulaşın.
- [Akıllı uyarılarla](platform/alerts-smartgroups-overview.md) ve [Otomatik eylemlerle](platform/alerts-action-rules.md)birlikte işlem sırasında işlemler desteklenir.
- Azure [panoları](learn/tutorial-logs-dashboards.md) ve [çalışma kitapları](app/usage-workbooks.md)ile görselleştirme oluşturun.

## <a name="overview"></a>Genel Bakış
Aşağıdaki diyagramda Azure Izleyici 'nin üst düzey bir görünümü sunulmaktadır. Diyagramın merkezinde, Azure Izleyici tarafından kullanılan iki temel veri türü olan ölçümler ve Günlükler için veri depoları bulunur. Sol tarafta, bu [veri depolarını](platform/data-platform.md)dolduran [izleme verilerinin kaynakları](platform/data-sources.md) bulunur. Sağ tarafta, Azure Izleyici 'nin bu toplanan verilerle (analiz, uyarı ve dış sistemlere akış gibi) gerçekleştirdiği farklı işlevler bulunur.


![Azure Izleyicisine genel bakış](media/overview/overview.png)


## <a name="monitoring-data-platform"></a>İzleme veri platformu
Azure Izleyici tarafından toplanan tüm veriler, iki temel tür, [ölçüm ve günlüklerden](platform/data-platform.md)birine uyar. [Ölçümler](platform/data-platform-metrics.md) , belirli bir noktadaki sistemin bazı yönlerini tanımlayan sayısal değerlerdir. Bunlar hafif ve neredeyse gerçek zamanlı senaryolar desteklenebilecek bir şekilde kullanılabilir. [Günlükler](platform/data-platform-logs.md) , her tür için farklı özellik kümelerine sahip kayıtlarla düzenlenmiş farklı türlerde veriler içerir. Olaylar ve izlemeler gibi telemetri, tüm analiz için birleştirilebilmesi için performans verilerinin yanı sıra Günlükler olarak da depolanır.

Birçok Azure kaynağı için, Azure Izleyici tarafından toplanan verileri Azure portal genel bakış sayfasında görürsünüz. Örneğin, herhangi bir sanal makineye göz atın ve performans ölçümlerini görüntüleyen birkaç grafik görürsünüz. Azure portal, verileri [Ölçüm Gezgini](platform/metrics-charts.md) 'nde açmak için herhangi bir grafiğe tıklayın. Bu, zaman içinde birden çok ölçümün değerlerini grafik oluşturmanızı sağlar.  Grafikleri etkileşimli olarak görüntüleyebilir veya diğer görselleştirmelerle görüntülemek için bunları bir panoya sabitleyebilirsiniz.

![Ölçümler](media/overview/metrics.png)

Azure Izleyici tarafından toplanan günlük verileri, toplanan verileri hızlı bir şekilde almak, birleştirmek ve analiz etmek için [sorgularla](log-query/log-query-overview.md) analiz edilebilir.  Azure portal [Log Analytics](log-query/portals.md) kullanarak sorgular oluşturup test edebilir ve ardından bu araçları kullanarak verileri doğrudan çözümleyebilir ya da [görselleştirmeler](visualizations.md) veya [Uyarı kurallarıyla](platform/alerts-overview.md)kullanılacak sorguları kaydedebilirsiniz.

Azure Izleyici, Azure Veri Gezgini tarafından kullanılan ve basit günlük sorguları için uygun olan, ayrıca toplamalar, birleşimler ve akıllı analiz gibi gelişmiş işlevleri de içeren [kusto sorgu dilinin](/azure/kusto/query/) bir sürümünü kullanır. [Birden çok ders](log-query/get-started-queries.md)kullanarak sorgu dilini hızlıca öğrenebilirsiniz.  [SQL](log-query/sql-cheatsheet.md) ve [Splunk](log-query/splunk-cheatsheet.md)’u önceden bilen kullanıcılara belirli yönergeler sağlanır.

![Günlükler](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Azure Izleyici hangi verileri toplar?
Azure Izleyici, çeşitli kaynaklardan veri toplayabilir. Uygulamalarınızın verilerini, uygulamanız için uygulamanızın, bağımlı olduğu herhangi bir işletim sistemi ve hizmeti platformun kendisine kadar olan katmanlarda izleyebilirsiniz. Azure Izleyici, aşağıdaki katmanlardan her birinden veri toplar:

- **Uygulama izleme verileri**: platformundan bağımsız olarak, yazdığınız kodun performansı ve işlevleri hakkındaki veriler.
- **Konuk işletim sistemi izleme verileri**: uygulamanızın üzerinde çalıştığı işletim sistemiyle ilgili veriler. Bu, Azure 'da, başka bir bulutta veya şirket içinde çalışıyor olabilir. 
- **Azure Kaynak izleme verileri**: bir Azure kaynağının işlemiyle ilgili veriler.
- **Azure aboneliği izleme verileri**: bir Azure aboneliğinin işlemi ve yönetimiyle ilgili veriler ve Azure 'un sistem durumu ve işlemi hakkındaki veriler. 
- **Azure kiracı izleme verileri**: Azure Active Directory gibi kiracı düzeyinde Azure hizmetlerinin çalışması hakkındaki veriler.

Bir Azure aboneliği oluşturur ve sanal makineler ve Web uygulamaları gibi kaynakları eklemeye başladığınızda Azure Izleyici veri toplamaya başlar.  Kaynaklar oluşturulduğunda veya değiştirildiğinde [etkinlik günlükleri](platform/activity-logs-overview.md) kaydı. [Ölçümler](platform/data-platform.md) , kaynağın nasıl çalıştığını ve tükettiği kaynakları bildirir. 

[Tanılama 'yı etkinleştirip](platform/resource-logs-overview.md) işlem kaynaklarına [bir aracı ekleyerek](platform/agent-windows.md) , topladığınız verileri kaynakların gerçek işlemine genişletin. Bu, kaynağın iç işlemi için telemetri toplar ve Windows ve Linux konuk işletim sisteminden günlükleri ve ölçümleri toplamak üzere farklı [veri kaynakları](platform/agent-data-sources.md) yapılandırmanıza olanak tanır. 

Uygulama [Hizmetleri uygulamanız](app/azure-web-apps.md) veya [VM ve sanal makine ölçek kümesi uygulamanız](app/azure-vm-vmss-apps.md)için izlemeyi etkinleştirerek, Application Insights sayfa görünümleri, uygulama istekleri ve dahil olmak üzere uygulamanız hakkında ayrıntılı bilgiler toplamasını etkinleştirin. larý. Kullanıcı trafiğinin benzetimini yapmak için bir [Kullanılabilirlik testi](app/monitor-web-app-availability.md) yapılandırarak uygulamanızın kullanılabilirliğini daha da doğrulayın.

### <a name="custom-sources"></a>Özel kaynaklar
Azure Izleyici, [veri TOPLAYıCı API](platform/data-collector-api.md)'sini kullanarak HERHANGI bir rest istemcisinden günlük verileri toplayabilir. Bu, özel izleme senaryoları oluşturmanıza ve izlemeyi diğer kaynaklardan açığa çıkaran kaynaklara genişletmenize olanak tanır.



## <a name="insights"></a>Bilgiler
İzleme verileri yalnızca, bilgi işlem ortamınızın işlemine ilişkin görünürlüğünüzü artırabilmesini sağlar. Azure Izleyici, uygulamalarınız ve bağımlı oldukları diğer kaynaklarla ilgili değerli Öngörüler sağlayan çeşitli özellikler ve araçlar içerir. [Kapsayıcılar için](insights/container-insights-overview.md) [Application Insights](app/app-insights-overview.md) ve Azure izleyici gibi [çözüm izleme çözümleri](insights/solutions.md) ve özellikleri, uygulamanızın ve belirli Azure hizmetlerinizin farklı yönlerine ayrıntılı öngörüler sağlar. 

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) , Web uygulamalarınızın bulutta veya şirket içinde barındırıldığından kullanılabilirlik, performans ve kullanımını izler. Azure Izleyici 'deki güçlü veri analizi platformundan yararlanarak uygulamanızın işlemlerinde derin öngörüler sağlar ve bir kullanıcının bunları raporlamasına gerek kalmadan hataları tanılamanıza olanak sağlar. Application Insights, çeşitli geliştirme araçlarına bağlantı noktaları içerir ve DevOps işlemlerinizi desteklemek için Visual Studio ile tümleşir.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Kapsayıcılar için Azure İzleyici
[Kapsayıcılar Için Azure izleyici](insights/container-insights-overview.md) , Azure Kubernetes Service (aks) üzerinde barındırılan, yönetilen Kubernetes kümelerine dağıtılan kapsayıcı iş yüklerinin performansını izlemek için tasarlanmış bir özelliktir. Ölçüm API 'SI aracılığıyla Kubernetes 'te bulunan denetleyicilerden, düğümlerden ve kapsayıcılardan bellek ve işlemci ölçümleri toplayarak performans görünürlüğü sağlar. Kapsayıcı günlükleri de toplanır.  Kubernetes kümelerinden izlemeyi etkinleştirdikten sonra, bu ölçümler ve Günlükler, Linux için Log Analytics aracısının kapsayıcılı bir sürümü aracılığıyla sizin için otomatik olarak toplanır.

![Kapsayıcı durumu](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>VM'ler için Azure İzleyici
[VM'ler için Azure izleyici](insights/vminsights-overview.md) , Windows ve Linux sanal makinelerinizin farklı işlemleri ve diğer kaynaklardaki ve dış bağlantılı bağımlılıklar dahil olmak üzere, Windows ve Linux VM 'lerinizin performansını ve sistem durumunu analiz ederek, Azure sanal makinelerinizi (VM) lerse. Çözüm, şirket içinde veya başka bir bulut sağlayıcısında barındırılan VM 'Ler için performans ve uygulama bağımlılıklarını izleme desteği içerir.  


![VM öngörüleri](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>İzleme çözümleri
Azure Izleyici 'de [izleme çözümleri](insights/solutions.md) , belirli bir uygulama veya hizmet için Öngörüler sağlayan paketlenmiş bir mantık kümesidir. Bunlar uygulama veya hizmete ilişkin izleme verilerini toplama mantığını, verileri çözümlemek için [sorguları](log-query/log-query-overview.md) ve görselleştirme için [görünümleri](../log-analytics/log-analytics-view-designer.md) içerir. Çeşitli Azure hizmetleri ve diğer uygulamalar için izleme sağlamak üzere Microsoft ve iş ortakları [tarafından sağlanan](insights/solutions-inventory.md) izleme çözümleri vardır.

![İzleme çözümleri](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Kritik durumlara yanıt verme
İzleme verilerini etkileşimli olarak çözümlemenize izin vermenin yanı sıra, etkili bir izleme çözümü, topladığı verilerde tanımlanan kritik koşullara uygun bir şekilde yanıt verebilmelidir. Bu, bir sorunu araştırmaktan sorumlu bir yöneticiye bir metin veya posta gönderiyor olabilir. Ya da bir hata koşulunu düzeltmeyi deneyen otomatikleştirilmiş bir işlem başlatabilirsiniz.


### <a name="alerts"></a>Uyarılar
[Azure izleyici 'Deki uyarılar](platform/alerts-overview.md) , kritik koşullar ve potansiyel olarak düzeltici eylem gerçekleştirmeye yönelik olarak size bir bildirim gönderir. Ölçümleri temel alan uyarı kuralları, sayısal değerlere göre neredeyse gerçek zamanlı uyarı sağlar ve Günlükler temelinde kurallar, birden fazla kaynaktaki veriler arasında karmaşık mantık sağlar.

Azure Izleyici 'deki uyarı kuralları, birden çok kural arasında paylaşılabilen benzersiz alıcı kümeleri ve Eylemler içeren [eylem gruplarını](platform/action-groups.md)kullanır. Gereksinimlerinize bağlı olarak, eylem grupları, uyarıları dış eylemleri başlatacak veya ıTSM araçlarınızla tümleştirilebilen Web kancaları kullanarak bu tür eylemleri gerçekleştirebilir.

![Uyarılar](media/overview/alerts.png)

### <a name="autoscale"></a>Otomatik Ölçeklendirme
Otomatik ölçeklendirme, uygulamanızdaki yükü işlemek için çalışan kaynakların doğru miktarına sahip olmasını sağlar. Yükün ne zaman otomatik olarak ekleneceğini ve ayrıca boşta duran kaynakları kaldırarak paradan tasarruf etmek üzere kaynakların ne zaman otomatik olarak ekleneceğini öğrenmek için Azure Izleyici tarafından toplanan ölçümleri kullanan kurallar oluşturmanıza olanak sağlar. Minimum ve maksimum örnek sayısını ve kaynakların ne zaman artırması veya azaltılacağı mantığı belirtirsiniz.

![Otomatik Ölçeklendirme](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>İzleme verileri görselleştiriliyor
Grafikler ve tablolar gibi [görselleştirmeler](visualizations.md) , izleme verilerini özetlemeye ve farklı kitlelere sunmaya yönelik etkili araçlardır. Azure Izleyici, izleme verilerini görselleştirmeye yönelik kendi özelliklerine sahiptir ve farklı kitlelere yayımlamak için diğer Azure hizmetlerinden yararlanır.

### <a name="dashboards"></a>Panolar
[Azure panoları](../azure-portal/azure-portal-dashboards.md) , hem ölçümler hem de Günlükler dahil olmak üzere farklı türlerdeki verileri [Azure Portal](https://portal.azure.com)tek bir bölmede birleştirmenizi sağlar. İsterseniz panoyu diğer Azure kullanıcılarıyla paylaşabilirsiniz. Azure Izleyici genelinde öğeler, herhangi bir günlük sorgusunun veya ölçüm grafiğinin çıktısının yanı sıra bir Azure panosuna eklenebilir. Örneğin, bir ölçüm grafiği, etkinlik günlüklerinin bir tablosu, Application Insights bir kullanım grafiği ve günlük sorgusunun çıkışı gösteren kutucukları birleştiren bir pano oluşturabilirsiniz.

![Pano](media/overview/dashboard.png)

### <a name="views"></a>Görünümler
[Görünümler](../log-analytics/log-analytics-view-designer.md) Azure izleyici 'de günlük verilerini görsel olarak sunar.  Her görünüm, kritik verileri özetleyen listelerin yanı sıra çubuk ve çizgi grafikler gibi görselleştirmelerin birleşimine kadar ayrıntıya eklenen tek bir kutucuk içerir.  İzleme çözümleri, belirli bir uygulama için verileri özetleyen görünümler içerir ve herhangi bir günlük sorgusundan veri sunmak için kendi görünümlerinizi de oluşturabilirsiniz. Azure Izleyici 'deki diğer öğelere benzer şekilde, görünümler Azure panolarına eklenebilir.

![Görüntüle](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) , çeşitli veri kaynakları genelinde etkileşimli görselleştirmeler sağlayan ve kuruluşunuzun içindeki ve dışındaki başkalarının kullanımına yönelik etkili bir yol sunan bir iş analizi hizmetidir. Bu ek görselleştirmelerin avantajlarından yararlanmak için [Azure izleyici 'den günlük verilerini otomatik olarak içeri aktarmak](../log-analytics/log-analytics-powerbi.md) üzere Power BI yapılandırabilirsiniz.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Verileri tümleştirme ve dışarı aktarma
Genellikle Azure Izleyicisini diğer sistemlerle tümleştirme ve izleme verilerinizi kullanan özel çözümler oluşturma gerekliliği vardır. Diğer Azure Hizmetleri, bu tümleştirmeyi sağlamak için Azure Izleyici ile birlikte çalışır.

### <a name="event-hub"></a>Olay Hub'ı
[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs) , herhangi bir gerçek zamanlı analiz sağlayıcısı veya toplu işlem/depolama bağdaştırıcısı kullanarak veri dönüştürebilen ve depolayabilen bir akış platformu ve olay alma hizmetidir. [Azure izleyici verilerini](platform/stream-monitoring-data-event-hubs.md) Iş ortağı SIEM ve izleme araçlarına akışa almak için Event Hubs kullanın.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) , farklı sistemler ve hizmetlerle tümleştirilen iş akışlarını kullanarak görevleri ve iş süreçlerini otomatikleştirmenize olanak tanıyan bir hizmettir. Azure Izleyici 'de ölçümleri ve günlükleri okuma ve yazma işlemleri, diğer birçok sistemlerle tümleştirerek iş akışları oluşturmanıza olanak tanıyan etkinlikleri kullanıma sunulmuştur.


### <a name="api"></a>eklentisi
Oluşturulan uyarılara erişmenin yanı sıra Azure Izleyici 'ye ve bunlara yönelik ölçümleri ve günlükleri okuyup yazmak için birden çok API kullanılabilir. Ayrıca, uyarıları yapılandırabilir ve alabilirsiniz. Bu, Azure Izleyici ile tümleştirilen özel çözümler oluşturmak için aslında sınırsız olanaklar sunar.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdakiler hakkında daha fazla bilgi edinin:

* Azure Izleyici tarafından toplanan veriler için [ölçümler ve Günlükler](platform/data-platform.md) .
* Uygulamanızın farklı bileşenlerinin telemetri gönderme yöntemi için [veri kaynakları](platform/data-sources.md) .
* Toplanan verileri çözümlemek için [günlük sorguları](log-query/log-query-overview.md) .
* Bulut uygulamalarını ve hizmetlerini izlemeye yönelik [en iyi uygulamalar](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) .
