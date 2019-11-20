---
title: Çok kiracılı bir uygulamayla Azure Izleyici günlükleri
description: Azure Izleyici günlüklerini çok kiracılı bir Azure SQL veritabanı SaaS uygulamasıyla ayarlama ve kullanma
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 01/25/2019
ms.openlocfilehash: eca2dbe7589fdc7d59a84d21ecf59749d986ade9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826421"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-sql-database-saas-app"></a>Çok kiracılı bir SQL veritabanı SaaS uygulamasıyla Azure Izleyici günlüklerini ayarlama ve kullanma

Bu öğreticide, elastik havuzları ve veritabanlarını izlemek için [Azure izleyici günlüklerini](/azure/log-analytics/log-analytics-overview) ayarlayıp kullanacaksınız. Bu öğretici, [performans izleme ve yönetim öğreticisini](saas-dbpertenant-performance-monitoring.md)oluşturur. Azure Izleyici günlüklerinin, Azure portal belirtilen izleme ve uyarı sayısını artırmak için nasıl kullanılacağını gösterir. Azure Izleyici günlükleri binlerce elastik havuzun izlenmesini ve yüzlerce binlerce veritabanını izlemeyi destekler. Azure Izleyici günlükleri, birden çok Azure aboneliği arasında farklı uygulamaların ve Azure hizmetlerinin izlenmesini tümleştirebilen tek bir izleme çözümü sağlar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure Izleyici günlüklerini yükleyip yapılandırın.
> * Havuzları ve veritabanlarını izlemek için Azure Izleyici günlüklerini kullanın.

Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:

* Her kiracı için Wingtip bilet SaaS veritabanı uygulaması dağıtılır. Beş dakikadan kısa bir süre içinde dağıtmak için bkz. [Wingtip bilet SaaS veritabanı kiracı başına uygulama](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell’in yüklendiğinden. Daha fazla bilgi için bkz. [Azure PowerShell kullanmaya başlayın](https://docs.microsoft.com/powershell/azure/get-started-azureps).

SaaS senaryoları ve desenlerinin tartışılması ve bir izleme çözümünde gereksinimleri nasıl etkilediği hakkında bilgi için bkz. [performans izleme ve yönetim öğreticisi](saas-dbpertenant-performance-monitoring.md) .

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Azure Izleyici günlükleri ile veritabanı ve elastik havuz performansını izleme ve yönetme

Azure SQL veritabanı için, izleme ve uyarı, Azure portal veritabanları ve havuzlarda bulunur. Bu yerleşik izleme ve uyarı uygundur, ancak kaynağa özgüdür. Bu, büyük yüklemeleri izlemek veya kaynaklar ve abonelikler arasında Birleşik bir görünüm sağlamak için daha az uygun olduğunu gösterir.

Yüksek hacimli senaryolar için Azure Izleyici günlüklerini izleme ve uyarı için kullanabilirsiniz. Azure Izleyici, büyük olasılıkla çok sayıda hizmetten bir çalışma alanında toplanan tanılama günlükleri ve telemetri üzerinde analizler sağlayan ayrı bir Azure hizmetidir. Azure Izleyici günlükleri, işletimsel veri analizlerinin kullanılmasına izin veren yerleşik bir sorgu dili ve veri görselleştirme araçları sağlar. SQL Analytics çözümü, önceden tanımlanmış birkaç elastik havuz ve veritabanı izleme ve uyarı görünümü ve sorgusu sağlar. Azure Izleyici günlükleri özel bir görünüm Tasarımcısı da sağlar.

OMS çalışma alanları artık Log Analytics çalışma alanları olarak adlandırılır. Log Analytics çalışma alanları ve analiz çözümleri Azure portal açılır. Azure portal, daha yeni erişim noktasıdır, ancak bazı alanlardaki Operations Management Suite portalının arkasında yer alabilir.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Kiracılarınız üzerinde bir iş yükünü taklit ederek Performans Tanılama verileri oluşturun 

1. PowerShell ıSE 'de *..\\Wingtipbilet ssaas-MultiTenantDb-master\\öğrenme modüllerini\\performans izleme ve yönetim\\demo-PerformanceMonitoringAndManagement. ps1*. Bu öğreticide yük oluşturma senaryolarından birkaçını çalıştırmak isteyebileceğiniz için bu betiği açık tutun.
1. Daha önce yapmadıysanız, izleme bağlamını daha ilginç hale getirmek için bir Grup kiracı sağlayın. Bu işlem birkaç dakika sürer.

   a. **$DemoScenario = 1**ayarlayın, _kiracı grubu sağlayın_.

   b. Betiği çalıştırmak ve ek 17 kiracılar dağıtmak için F5 'e basın.

1. Şimdi, tüm kiracılarda benzetimli yük çalıştırmak için yük oluşturucuyu başlatın.

    a. **$DemoScenario = 2**olarak ayarlayın, _Normal yoğunluk yükü oluşturun (yaklaşık 30 DTU)_ .

    b. Betiği çalıştırmak için F5 tuşuna basın.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Her kiracı uygulama komut dosyası için Wingtip bilet SaaS veritabanı 'nı alın

Wingtip biletleri SaaS çok kiracılı veritabanı betikleri ve uygulama kaynak kodu [wingtipbilet ssaas-dbpertenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub deposunda mevcuttur. Wingtip bilet PowerShell betiklerini indirme ve engellemesini kaldırma adımları için bkz. [genel rehberlik](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Log Analytics çalışma alanını ve Azure SQL Analytics çözümünü yükleyip yapılandırın

Azure Izleyici, yapılandırılması gereken ayrı bir hizmettir. Azure Izleyici günlükleri bir Log Analytics çalışma alanında günlük verilerini, telemetri ve ölçümleri toplar. Azure 'daki diğer kaynaklarda olduğu gibi, bir Log Analytics çalışma alanı oluşturulmalıdır. Çalışma alanının, izlediği uygulamalarla aynı kaynak grubunda oluşturulması gerekmez. Bunun yapılması genellikle en mantıklı hale gelir. Wingtip bilet uygulaması için, çalışma alanının uygulamayla silindiğinden emin olmak için tek bir kaynak grubu kullanın.

1. PowerShell ıSE 'de *..\\Wingtipbilet ssaas-MultiTenantDb-master\\Learning modüllerini\\performans izleme ve yönetim\\Log Analytics\\demo-LogAnalytics. ps1*.
1. Betiği çalıştırmak için F5 tuşuna basın.

Artık Azure portal Azure Izleyici günlüklerini açabilirsiniz. Log Analytics çalışma alanında telemetri toplamak ve görünür hale getirmek birkaç dakika sürer. Sistemi tanılama verilerini toplama işlemi ne kadar uzun olursa, deneyim daha ilginç olur. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Havuzları ve veritabanlarını izlemek için Log Analytics çalışma alanını ve SQL Analytics çözümünü kullanma


Bu alıştırmada, veritabanları ve havuzlar için toplanan Telemetriyi görmek için Azure portal Log Analytics çalışma alanı açın.

1. [Azure portala](https://portal.azure.com) gidin. Log Analytics çalışma alanını açmak için **tüm hizmetler** ' i seçin. Sonra, Log Analytics için arama yapın.

   ![Log Analytics çalışma alanını aç](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. _Wtploganalytics-&lt;user&gt;_ adlı çalışma alanını seçin.

1. Azure portal Log Analytics çözümünü açmak için **Genel Bakış ' ı** seçin.

   ![Genel Bakış](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Çözümün etkin olması birkaç dakika sürebilir. 

1. Açmak için **Azure SQL Analytics** kutucuğunu seçin.

    ![Genel Bakış kutucuğu](media/saas-dbpertenant-log-analytics/overview.png)

1. Çözümdeki görünümler, alt kısımdaki kendi iç kaydırma çubuğu ile yana kaydırılır. Gerekirse sayfayı yenileyin.

1. Özet sayfasını araştırmak için, bir ayrıntıya gitme Gezgini açmak üzere kutucukları veya ayrı veritabanlarını seçin.

    ![Log Analytics panosu](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Zaman aralığını değiştirmek için filtre ayarını değiştirin. Bu öğretici için **Son 1 saat**' i seçin.

    ![Zaman filtresi](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Sorgu kullanımını ve bu veritabanına yönelik ölçümleri araştırmak için tek bir veritabanı seçin.

    ![Veritabanı Analizi](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Kullanım ölçümlerini görmek için analiz sayfasını sağa kaydırın.
 
     ![Veritabanı ölçümleri](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Analiz sayfasını sola kaydırın ve **kaynak bilgileri** listesinde sunucu kutucuğunu seçin.  

    ![Kaynak bilgi listesi](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Sunucu üzerindeki havuzları ve veritabanlarını gösteren bir sayfa açılır.

    ![Havuzlar ve veritabanları içeren sunucu](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Bir havuz seçin. Açılan havuz sayfasında, havuz ölçümlerini görmek için sağa kaydırın. 

    ![Havuz ölçümleri](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Log Analytics çalışma alanına döndüğünüzde, çalışma alanını açmak için **OMS portalı** ' nı seçin.

    ![Log Analytics çalışma alanı](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Log Analytics çalışma alanında, günlük ve ölçüm verilerini daha fazla inceleyebilirsiniz. 

Azure Izleyici günlüklerinde izleme ve uyarı verme, Azure portal her kaynak üzerinde tanımlanan uyarıdan farklı olarak çalışma alanındaki veriler üzerindeki sorguları temel alır. Uyarıları sorgulara dayandırarak, veritabanı başına bir tane tanımlamak yerine tüm veritabanlarına bakabilmeniz için tek bir uyarı tanımlayabilirsiniz. Sorgular yalnızca çalışma alanında bulunan verilerle sınırlıdır.

Azure Izleyici günlüklerini kullanarak uyarıları sorgulama ve ayarlama hakkında daha fazla bilgi için bkz. [Azure izleyici günlüklerinde uyarı kurallarıyla çalışma](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating).

Çalışma alanındaki veri hacmine bağlı olarak SQL veritabanı ücretleri için Azure Izleyici günlükleri. Bu öğreticide, günde 500 MB ile sınırlı olan ücretsiz bir çalışma alanı oluşturdunuz. Bu sınıra ulaşıldığında, veriler artık çalışma alanına eklenmez.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunları öğrendiniz:

> [!div class="checklist"]
> * Azure Izleyici günlüklerini yükleyip yapılandırın.
> * Havuzları ve veritabanlarını izlemek için Azure Izleyici günlüklerini kullanın.

[Kiracı Analizi öğreticisini](saas-dbpertenant-log-analytics.md)deneyin.

## <a name="additional-resources"></a>Ek kaynaklar

* [İlk Wingtip bilet SaaS veritabanı kiracı başına uygulama dağıtımı üzerinde derleme yapan ek öğreticiler](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure İzleyici günlükleri](../azure-monitor/insights/azure-sql.md)
