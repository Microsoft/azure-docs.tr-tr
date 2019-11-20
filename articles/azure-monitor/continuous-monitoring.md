---
title: Azure Izleyici ile sürekli izleme | Microsoft Docs
description: İş akışlarınız genelinde sürekli izlemeyi etkinleştirmek için Azure Izleyici 'yi kullanmaya yönelik belirli adımları açıklar.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: 88934d469ddcca50d7dfc3f35b18ab332d4bd172
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596792"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Azure Izleyici ile sürekli izleme

Sürekli izleme, DevOps ve BT operasyon yaşam sürelerinizin her aşamasında izlemeyi birleştirmek için gereken işlem ve teknolojiyi ifade eder. Geliştirme 'dan üretime kadar taşırken uygulamanızın ve altyapınızın sistem durumunu, performansını ve güvenilirliğini sürekli olarak sağlamaya yardımcı olur. Sürekli izleme, kullanıcılarınıza sürekli değer sağlamak için daha hızlı ve güvenilir bir şekilde yazılım geliştirmenize ve sunmanıza yardımcı olan sürekli tümleştirme ve sürekli dağıtım (CI/CD) kavramlarını oluşturur.

[Azure izleyici](overview.md) , Azure 'da, bulutta ve şirket içinde uygulamalar ve altyapıda tam yığın Observability sağlayan Birleşik izleme çözümüdür. Geliştirme ve test sırasında [Visual Visual Studio Code Studio](https://visualstudio.microsoft.com/) ile sorunsuz bir şekilde çalışır ve dağıtım ve işlemler sırasında sürüm yönetimi ve iş öğesi yönetimi Için [Azure DevOps](/azure/devops/user-guide/index) ile tümleşir. Ayrıca, mevcut BT işlemlerinizde sorunları ve olayları izlemeye yardımcı olmak için, seçtiğiniz ıTSM ve SıEM araçları genelinde de tümleşir.

Bu makalede, iş akışlarınız genelinde sürekli izlemeyi etkinleştirmek için Azure Izleyici 'yi kullanmaya yönelik belirli adımlar açıklanır. Farklı özellikleri uygulamaya ilişkin ayrıntıları sağlayan diğer belgelere bağlantılar içerir.


## <a name="enable-monitoring-for-all-your-applications"></a>Tüm uygulamalarınız için izlemeyi etkinleştirin
Tüm ortamınız genelinde Observability kazanmak için tüm Web uygulamalarınızda ve hizmetlerinize yönelik izlemeyi etkinleştirmeniz gerekir. Bu, tüm bileşenlerin tamamında uçtan uca işlemleri ve bağlantıları kolayca görselleştirmenize olanak tanır.

- [Azure DevOps Projeleri](../devops-project/overview.md) , mevcut kodunuz ve git deponuzla ilgili Basitleştirilmiş bir deneyim sağlar veya Azure 'A sürekli TÜMLEŞTIRME (CI) ve sürekli teslım (CD) işlem hattı oluşturmak için örnek uygulamalardan birini tercih edebilirsiniz.
- [DevOps yayın işlem hattınızda sürekli izleme](../azure-monitor/app/continuous-monitoring.md) , izleme verilerine bağlı olarak dağıtımınızı geçit veya geri alma olanağı sağlar.
- [Durum İzleyicisi](../azure-monitor/app/monitor-performance-live-website-now.md) , kodunuzu değiştirmeye veya yeniden dağıtmaya gerek kalmadan Windows 'da canlı bir .NET uygulamasını Azure Application Insights ile denetlemenizi sağlar.
- Uygulamanıza yönelik koda erişiminiz varsa, [.net](../azure-monitor/learn/quick-monitor-portal.md), [Java](../azure-monitor/app/java-get-started.md), [Node. js](../azure-monitor/learn/nodejs-quick-start.md)veya başka bir programlama için Azure izleyici Application Insights SDK 'sını yükleyerek [Application Insights](../azure-monitor/app/app-insights-overview.md) ile tam izlemeyi etkinleştirin [ Diller](../azure-monitor/app/platforms.md). Bu, uygulamanız ve işletmeniz için uygun olan özel olayları, ölçümleri veya sayfa görünümlerini belirtmenize olanak tanır.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>Tüm altyapınız için izlemeyi etkinleştirin
Uygulamalar, temel aldığı altyapı olarak yalnızca güvenilir. Tüm altyapınızda izlemenin etkinleştirilmesini sağlamak, tam Observability elde etmenize yardımcı olur ve bir sorun ortaya çıkarsa olası bir kök nedenini bulmayı kolaylaştırır. Azure Izleyici, sanal makineler, kapsayıcılar, depolama ve ağ gibi kaynaklar da dahil olmak üzere tüm karma altyapınızın sistem durumunu ve performansını izlemenize yardımcı olur.

- Azure kaynaklarınızın çoğunun yapılandırma olmadan [Platform ölçümlerini, etkinlik günlüklerini ve tanılama günlüklerini](platform/data-sources.md) otomatik olarak alırsınız.
- [VM'ler için Azure izleyici](insights/vminsights-overview.md)olan VM 'ler için daha derin izlemeyi etkinleştirin.
-  [Kapsayıcılar Için Azure izleyici](insights/container-insights-overview.md)ile aks kümelerinin daha derin izlenmesini etkinleştirin.
- Ortamınızdaki farklı uygulamalar ve hizmetler için [izleme çözümleri](insights/solutions-inventory.md) ekleyin.


[Kod olarak altyapı](/azure/devops/learn/what-is-infrastructure-as-code) , DevOps ekiplerinin kaynak kodu için kullandığı sürüm oluşturma ile, açıklayıcı bir modelde altyapının yönetimidir. Ortamınıza güvenilirlik ve ölçeklenebilirlik ekler ve uygulamalarınızı yönetmek için kullanılan benzer süreçlerden yararlanmanızı sağlar.

-  Büyük bir kaynak kümesi üzerinde uyarıları izlemeyi ve yapılandırmayı etkinleştirmek için [Kaynak Yöneticisi şablonlarını](platform/template-workspace-configuration.md) kullanın.
- Kaynaklarınız üzerinde farklı kurallara zorlamak için [Azure ilkesini](../governance/policy/overview.md) kullanın. Bu, bu kaynakların kurumsal standartlarınız ve hizmet düzeyi Sözleşmelerinizle uyumlu kalmasını sağlar. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Azure kaynak gruplarındaki kaynakları birleştirme
Günümüzde Azure 'daki tipik bir uygulama, VM 'Ler ve uygulama hizmetleri veya Cloud Services, AKS kümelerinde veya Service Fabric barındırılan mikro hizmetler gibi birden çok kaynak içerir. Bu uygulamalar genellikle Event Hubs, depolama, SQL ve Service Bus gibi bağımlılıklardan yararlanır.

- Farklı uygulamalarınızı oluşturan tüm kaynaklarınız genelinde tam görünürlük sağlamak için Azure kaynak gruplarındaki kaynakları birleştirin. [Kaynak grupları Için Azure izleyici](../azure-monitor/insights/resource-group-insights.md) , tüm tam yığın uygulamanızın sistem durumunu ve performansını izlemek için basit bir yol sağlar ve herhangi bir araştırmalar veya hata ayıklama için ilgili bileşenlere detaya gitmeyi sağlar.

## <a name="ensure-quality-through-continuous-deployment"></a>Sürekli dağıtım ile kalite sağlayın
Sürekli tümleştirme/sürekli dağıtım, otomatik test sonuçlarına göre kod değişikliklerini uygulamanıza otomatik olarak tümleştirmenize ve dağıtmanıza olanak tanır. Dağıtım sürecini kolaylaştırır ve üretime geçmeden önce herhangi bir değişiklik kalitesini sağlar.


- Sürekli dağıtım uygulamak ve tüm işleminizi, CI/CD testleriniz temelinde üretim için kod işleme üzerinden otomatik hale getirmek için [Azure Pipelines](/azure/devops/pipelines) kullanın.
- İzlemeyi dağıtım öncesi veya dağıtım sonrası ile bütünleştirmek için [kalite kapıları](/azure/devops/pipelines/release/approvals/gates) kullanın. Bu, uygulamalarınızın dev 'den üretime taşınması ve altyapı ortamındaki veya ölçekteki herhangi bir farklılık KPI 'larınızı olumsuz şekilde etkilemediğinden, önemli sağlık/performans ölçümlerini (KPI 'ler) karşılamanızı sağlar.
- Geliştirme, test, Canary ve üretim gibi farklı dağıtım ortamlarınızla [ayrı izleme örnekleri saklayın](../azure-monitor/app/separate-resources.md) . Bu, toplanan verilerin ilişkili uygulamalar ve altyapı genelinde ilgili olmasını sağlar. Ortamların genelinde veri ilişkilendirilmesi gerekiyorsa, [Ölçüm Gezgini çoklu kaynak grafikleri](../azure-monitor/platform/metrics-charts.md) kullanabilir veya [Azure izleyici 'de çapraz kaynak sorguları](log-query/cross-workspace-query.md)oluşturabilirsiniz.


## <a name="create-actionable-alerts-with-actions"></a>Eylemlerle eylem yapılabilir uyarılar oluşturma
İzlemenin kritik bir yönü, yöneticilere geçerli ve tahmin edilen sorunları önceden bildirmeye yönelik olarak bildirimde bulunur. 

- Öngörülebilir hata durumlarını belirlemek için günlüklere ve ölçümlere göre [Azure izleyici 'de uyarılar](../azure-monitor/platform/alerts-overview.md) oluşturun. Tüm uyarıları, gerçek kritik koşulları temsil ettikleri ve hatalı pozitif sonuçları azaltmak için arama yapılabilecek anlamına gelen bir amaca sahip olmanız gerekir. Kendi statik eşiklerinizi tanımlamak yerine ölçüm verilerinde temelleri otomatik olarak hesaplamak için [dinamik eşikleri](platform/alerts-dynamic-thresholds.md) kullanın. 
- Yöneticilerinize bildirimde bulunmak için en etkili yöntemi kullanmak üzere uyarılara yönelik eylemleri tanımlayın. [Bildirim için kullanılabilir eylemler](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) SMS, e-posta, anında iletme bildirimleri veya sesli çağrılardır.
- [Web kancaları](platform/activity-log-alerts-webhook.md)aracılığıyla [ITSM aracınız](platform/itsmc-overview.md) veya diğer uyarı yönetim sistemlerine bağlanmak için daha gelişmiş eylemler kullanın.
- [Azure Otomasyonu runbook 'ları](../automation/automation-webhooks.md) veya Web kancaları kullanılarak bir uyarıdan başlatılabilen [Logic Apps](/connectors/custom-connectors/create-webhook-trigger) , uyarılarda tanımlanan durumları düzeltin. 
- Toplanan ölçümlere göre işlem kaynaklarınızı dinamik olarak artırmak ve azaltmak için [Otomatik ölçeklendirmeyi](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md) kullanın.

## <a name="prepare-dashboards-and-workbooks"></a>Panoları ve çalışma kitaplarını hazırlama
Geliştirme ve işlemlerinizin aynı telemetri ve araçların erişimine sahip olmasını sağlamak, bunların tüm ortamınızda desenleri görüntülemesine olanak tanır ve ortalama (MTTD) ve ortalama geri yükleme süresi (MTTR) süresini en aza indirir.

- Kuruluşunuzdaki farklı roller için ortak ölçümleri ve günlükleri temel alan [özel panolar](../azure-monitor/learn/tutorial-app-dashboards.md) hazırlayın. Panolar, tüm Azure kaynaklarından verileri birleştirebilir.
- Geliştirme ve işlemler arasında bilgi paylaşımını sağlamak için [çalışma kitaplarını](../azure-monitor/app/usage-workbooks.md) hazırlayın. Bunlar, ölçüm grafikleri ve günlük sorguları ile dinamik raporlar olarak hazırlanarak, geliştiriciler tarafından hazırlanan ve temel sorunları ele almak için müşterilerin destek veya işlemlerine yardımcı olan geliştiriciler tarafından hazırlanan sorun giderme kılavuzlarından de yararlanılabilir

## <a name="continuously-optimize"></a>Sürekli iyileştirme
 İzleme, popüler derleme ölçümü-öğrenme felseflarından biridir. Bu, KPI ve Kullanıcı davranışı ölçümlerinizi sürekli olarak izlemeye ve ardından bunları planlama yinelemeleri aracılığıyla iyileştirmek için daha fazla çaba öneriyor. Azure Izleyici, işinizle ilgili ölçümleri ve günlükleri toplamanıza ve sonraki dağıtımda gerekli şekilde yeni veri noktaları eklemenize yardımcı olur.

- [Son Kullanıcı davranışını ve katılımı izlemek](../azure-monitor/learn/tutorial-users.md)için Application Insights araçları kullanın.
- Önemli KPI 'lere sürücü üzerinde hangi alanların odaklanacağı önceliklendirmenize yardımcı olması için [etki analizini](../azure-monitor/app/usage-impact.md) kullanın.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure izleyici](overview.md)'nin fark bileşenleri hakkında bilgi edinin.
- Yayın ardışık düzenine [sürekli Izleme ekleyin](../azure-monitor/app/continuous-monitoring.md) .