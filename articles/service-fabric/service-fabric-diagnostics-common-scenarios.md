---
title: Azure Service Fabric genel senaryoları tanılama | Microsoft Docs
description: Azure Service Fabric genel senaryolarıyla ilgili sorunları nasıl giderebileceğinizi öğrenin
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 265aea1b8873d812859b39175c732c3e7118cbb5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "60394257"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>Service Fabric ile yaygın senaryoları tanılayın

Bu makalede, kullanıcıların Service Fabric ile izleme ve tanılama alanında karşılaştığı yaygın senaryolar gösterilmektedir. Sunulan senaryolar, Service Fabric 'in tüm 3 katmanlarını kapsar: Uygulama, küme ve altyapı. Her çözüm, her bir senaryoyu tamamlamaya yönelik Application Insights ve Azure Izleyici günlüklerini, Azure izleme araçlarını kullanır. Her çözümdeki adımlarda, kullanıcılara Application Insights ve Azure Izleyici günlüklerinin Service Fabric bağlamında nasıl kullanılacağına ilişkin bir giriş sunulmaktadır.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>Önkoşullar ve öneriler

Bu makaledeki çözümler aşağıdaki araçları kullanacaktır. Bu ayarı ayarlamış ve yapılandırmış olmanız önerilir:

* [Service Fabric Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* [Kümenizde Azure Tanılama etkinleştirme](service-fabric-diagnostics-event-aggregation-wad.md)
* [Log Analytics çalışma alanı ayarlama](service-fabric-diagnostics-oms-setup.md)
* [Performans sayaçlarını izlemek için aracı Log Analytics](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>Uygulamamda işlenmeyen özel durumları nasıl görebilirim?

1. Uygulamanızın ile yapılandırıldığı Application Insights kaynağına gidin.
2. Sol üstteki *Ara* ' ya tıklayın. Sonra bir sonraki panelde filtre ' ye tıklayın.

    ![AI genel bakış](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. Çok sayıda olay türü görürsünüz (izlemeler, istekler, özel olaylar). Filtreniz olarak "özel durum" ı seçin.

    ![AI filtre listesi](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    Listede bir özel duruma tıkladığınızda, Service Fabric Application Insights SDK kullanıyorsanız hizmet bağlamı dahil daha fazla ayrıntıya bakabilirsiniz.

    ![AI özel durumu](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>Nasıl yaparım? Hizmetlerim 'de hangi HTTP çağrılarının kullanıldığını görüntüleyin?

1. Aynı Application Insights kaynağında, özel durumlar yerine "istekler" ile filtre uygulayabilir ve yapılan tüm istekleri görüntüleyebilirsiniz
2. Service Fabric Application Insights SDK 'sını kullanıyorsanız, hizmetlerinizin birine bağlı bir görsel temsilini ve başarılı ve başarısız isteklerin sayısını görebilirsiniz. Sol tarafta "uygulama haritası" na tıklayın

    ![AI uygulama Haritası dikey](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![penceresi AI uygulama Haritası](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    Uygulama haritası hakkında daha fazla bilgi için [uygulama Haritası belgelerini](../azure-monitor/app/app-map.md) ziyaret edin

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>Düğüm aşağı gittiğinde uyarı oluşturma Nasıl yaparım?

1. Düğüm olayları Service Fabric kümeniz tarafından izlenir. **Servicefabric (NameofResourceGroup)** adlı Service Fabric Analytics çözüm kaynağına gidin
2. "Özet" başlıklı dikey pencerenin alt kısmındaki grafiğe tıklayın

    ![Azure Izleyici günlükleri çözümü](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. Burada çeşitli ölçümleri görüntüleyen birçok grafik ve kutucuk vardır. Grafiklerinden birine tıkladığınızda günlük aramasına bu işlem uygulanır. Burada herhangi bir küme olayı veya performans sayacı için sorgulama yapabilirsiniz.
4. Aşağıdaki sorguyu girin. Bu olay kimlikleri [düğüm olayları başvurusunda](service-fabric-diagnostics-event-generation-operational.md#application-events) bulunur

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. En üstteki "yeni uyarı kuralı" na tıklayın ve artık bu sorguya bağlı olarak bir olay ulaştığında, seçtiğiniz iletişim yönteminde bir uyarı alırsınız.

    ![Azure Izleyici günlükleri yeni uyarısı](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>Uygulama yükseltme geri alma işlemlerinin nasıl uyarılendirilirim?

1. Aynı günlük araması penceresinde, önceki yükseltme geri alma için aşağıdaki sorguyu girin. Bu olay kimlikleri, [uygulama olayları başvurusu](service-fabric-diagnostics-event-generation-operational.md#application-events) altında bulunur

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. Üstteki "yeni uyarı kuralı" na tıklayın ve artık bu sorguya bağlı olarak bir olay geldiğinde bir uyarı alırsınız.

## <a name="how-do-i-see-container-metrics"></a>Nasıl yaparım? kapsayıcı ölçümlerini görmek mi istiyorsunuz?

Tüm grafiklerle aynı görünümde, kapsayıcılarınızın performansı için bazı kutucuklar görürsünüz. Bu kutucukların doldurulması için Log Analytics Aracısı ve [kapsayıcı izleme çözümüne](service-fabric-diagnostics-oms-containers.md) ihtiyacınız vardır.

![Log Analytics kapsayıcı ölçümleri](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>Kapsayıcılarınızın **içinden** Telemetriyi işaretlemek için [kapsayıcılar için Application Insights NuGet paketini](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios)eklemeniz gerekir.

## <a name="how-can-i-monitor-performance-counters"></a>Performans sayaçlarını nasıl izleyebilirim?

1. Log Analytics aracısını kümenize ekledikten sonra, izlemek istediğiniz belirli performans sayaçlarını eklemeniz gerekir. Portaldaki Log Analytics çalışma alanının sayfasına gidin; çözüm sayfasında çalışma alanı sekmesi sol menüde bulunur.

    ![Log Analytics çalışma alanı sekmesi](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. Çalışma alanının sayfasından sonra, aynı Sol menüdeki "Gelişmiş ayarlar" a tıklayın.

    ![Gelişmiş ayarları Log Analytics](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. Log Analytics Aracısı aracılığıyla düğümlerinizin belirli sayaçlarını toplamaya başlamak için veri > Windows performans sayaçları (Linux makineler için veri > Linux performans sayaçları) seçeneğine tıklayın. Eklenecek sayaçların biçimi örnekleri aşağıda verilmiştir

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     Hızlı başlangıçta, VotingData ve VotingWeb, kullanılan işlem adlarıdır. bu nedenle, bu sayaçların izlenmesi şu şekilde görünür.

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Performans sayaçlarını Log Analytics](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. Bu, altyapınızın iş yüklerinizi nasıl ele almakta olduğunu görmenizi ve kaynak kullanımına göre ilgili uyarıları ayarlamanızı sağlar. Örneğin, toplam Işlemci kullanımı% 90 veya% 5 ' in üzerinde olursa bir uyarı ayarlamak isteyebilirsiniz. Bu için kullanacağınız sayaç adı "% Işlemci zamanı". Bunu, aşağıdaki sorgu için bir uyarı kuralı oluşturarak yapabilirsiniz:

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>Nasıl yaparım? Reliable Services ve aktörlerimin performansı İzmi?

Uygulamalarınızda Reliable Services veya aktörlerin performansını izlemek için, Service Fabric aktör, aktör yöntemi, hizmet ve hizmet yöntemi sayaçlarını da toplamalısınız. Toplanacak güvenilir hizmet ve aktör performans sayacı örnekleri aşağıda verilmiştir

>[!NOTE]
>Service Fabric performans sayaçları Şu anda Log Analytics Aracısı tarafından toplanamaz, ancak [diğer tanılama çözümleriyle](service-fabric-diagnostics-partners.md) toplanabilir

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

Güvenilir [Hizmetler](service-fabric-reliable-serviceremoting-diagnostics.md) ve [aktörlerdeki](service-fabric-reliable-actors-diagnostics.md) performans sayaçlarının tam listesi için bu bağlantıları denetleyin

## <a name="next-steps"></a>Sonraki adımlar

* Performans veya kullanımlardaki değişiklikler hakkında bildirim almak için [AI 'Deki uyarıları ayarlama](../azure-monitor/app/alerts.md)
* [Application Insights akıllı algılama](../azure-monitor/app/proactive-diagnostics.md) , olası performans sorunları konusunda sizi uyarmak üzere AI 'ye gönderilen telemetrinin proaktif analizini yapar
* Algılama ve tanılama konusunda yardımcı olması için Azure Izleyici günlükleri [uyarısı](../log-analytics/log-analytics-alerts.md) hakkında daha fazla bilgi edinin.
* Azure Izleyici günlükleri, şirket içi kümeler için Azure Izleyici günlüklerine veri göndermek için kullanılabilen bir ağ geçidi (HTTP Iletme proxy) sunar. [Log Analytics ağ geçidini kullanarak Internet erişimi olmadan bilgisayarları Azure izleyici günlüklerine bağlama](../azure-monitor/platform/gateway.md) konusunda daha fazla bilgi edinin
* Azure Izleyici günlüklerinin bir parçası olarak sunulan [günlük araması ve sorgulama](../log-analytics/log-analytics-log-searches.md) özellikleriyle familiarized alın
* Azure Izleyici günlüklerine ve bu günlüklere yönelik daha ayrıntılı bir genel bakış edinin, [Azure izleyici günlüklerini](../operations-management-suite/operations-management-suite-overview.md) okuyun.
