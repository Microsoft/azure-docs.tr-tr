---
title: İşlemleri, olayları ve genel temel Load Balancer sayaçlarını izleme
titlesuffix: Azure Load Balancer
description: Uyarı olaylarını etkinleştirmeyi ve genel temel Load Balancer araştırma sistem durumu günlüğe kaydetmeyi öğrenin
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: allensu
ms.openlocfilehash: df35168d0fab0b01ff11c4105a1fcc5b16e21f30
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350732"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>Genel temel Load Balancer Azure Izleyici günlükleri

>[!IMPORTANT] 
>Azure Load Balancer iki farklı türü destekler: Temel ve Standart. Bu makalede Temel Yük Dengeleyici anlatılmaktadır. Standart Load Balancer hakkında daha fazla bilgi için bkz. Azure Izleyici 'de çok boyutlu ölçümler aracılığıyla telemetri sunan [Standart Load Balancer genel bakış](load-balancer-standard-overview.md) .

Temel yük dengeleyiciler yönetmek ve sorunlarını gidermek için Azure 'da farklı türlerde Günlükler kullanabilirsiniz. Bu günlüklerden bazılarına Portal üzerinden erişilebilir. Günlükler bir olay hub 'ına veya Log Analytics çalışma alanına akışla eklenebilir. Tüm Günlükler Azure Blob depolamadan ayıklanıp Excel ve Power BI gibi farklı araçlarla görüntülenebilir.  Aşağıdaki listeden farklı türlerdeki Günlükler hakkında daha fazla bilgi edinebilirsiniz.

* **Etkinlik günlükleri:** Azure aboneliğinize gönderilen tüm etkinlikleri ve bunların durumunu görüntülemek için [kaynak eylemlerini izlemek üzere etkinlik günlüklerini görüntüle](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) ' yi kullanabilirsiniz. Etkinlik günlükleri varsayılan olarak etkindir ve Azure portal görüntülenebilir.
* **Uyarı olay günlükleri:** Yük dengeleyici tarafından oluşturulan uyarıları görüntülemek için bu günlüğü kullanabilirsiniz. Yük dengeleyicinin durum her beş dakikada bir toplanır. Bu günlük yalnızca bir yük dengeleyici uyarı olayı ortaya çıktığında yazılır.
* **Durum araştırma günlükleri:** Bu günlüğü, sistem durumu araştırma hataları nedeniyle yük dengeleyiciden istek almamış arka uç havuzunuzdaki örnek sayısı gibi, sistem durumu araştırmanız tarafından algılanan sorunları görüntülemek için kullanabilirsiniz. Bu günlük, durum araştırma durumunda bir değişiklik olduğunda yazılır.

> [!IMPORTANT]
> Azure Izleyici günlükleri Şu anda yalnızca genel temel yük dengeleyiciler için geçerlidir. Günlükler yalnızca Kaynak Yöneticisi dağıtım modelinde dağıtılan kaynaklar için kullanılabilir. Klasik dağıtım modelinde kaynaklar için günlük kullanamazsınız. Dağıtım modelleri hakkında daha fazla bilgi için bkz. [Kaynak Yöneticisi dağıtımı ve klasik dağıtımı anlama](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="enable-logging"></a>Günlü kaydını etkinleştir

Etkinlik günlüğü tüm Kaynak Yöneticisi kaynakları için otomatik olarak etkinleştirilir. Bu Günlükler aracılığıyla kullanılabilir verileri toplamaya başlamak için olay ve durum araştırma günlüğünü etkinleştirin. Günlüğe kaydetmeyi etkinleştirmek için aşağıdaki adımları kullanın.

[Azure Portal](https://portal.azure.com) oturum açın. Zaten bir yük dengeleyiciniz yoksa devam etmeden önce [yük dengeleyici oluşturun](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) .

1. Portalda **kaynak grupları**' na tıklayın.
2. Yük dengeleyicinizin olduğu **\<resource-Group-name >** seçin.
3. Yük dengeleyiciyi seçin.
4. @No__t-1**tanılama ayarlarını** **izlemeyi**seçin.
5. **Tanılama ayarları** bölmesinde, **Tanılama ayarları**altında **+ Tanılama ayarı Ekle**' yi seçin.
6. **Tanılama ayarları** oluşturma bölmesinde **ad** alanına **mylbdiagnostics** ' i girin.
7. **Tanılama ayarları**için üç seçeneğiniz vardır.  Bunlardan birini, ikisini veya üçünü seçebilirsiniz ve her birini gereksinimleriniz için yapılandırabilirsiniz:
   * **Bir depolama hesabına Arşivle**
   * **Bir olay hub 'ına akış**
   * **Log Analytics gönder**

    ### <a name="archive-to-a-storage-account"></a>Bir depolama hesabına arşivle
    Bu işlem için zaten oluşturulmuş bir depolama hesabınız olması gerekir.  Depolama hesabı oluşturmak için bkz. [depolama hesabı oluşturma](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)

    1. **Depolama hesabında Arşivle**seçeneğinin yanındaki onay kutusunu işaretleyin.
    2. **Depolama hesabı seç** bölmesini açmak için **Yapılandır** ' ı seçin.
    3. Aşağı açılan kutuda depolama hesabınızın oluşturulduğu **aboneliği** seçin.
    4. Açılır kutuda depolama **hesabı** altında depolama hesabınızın adını seçin. 
    5. Tamam ' ı seçin.

    ### <a name="stream-to-an-event-hub"></a>Bir olay hub'ına akış yap
    Bu işlem için önceden oluşturulmuş bir olay hub 'ı gerekir.  Bir olay hub 'ı oluşturmak için, bkz. [Hızlı başlangıç: Azure portal @ no__t-0 kullanarak bir olay hub 'ı oluşturma

    1. **Olay Hub 'ına akış** ' nın yanındaki onay kutusunu seçin
    2. **Olay Hub 'ını Seç** bölmesini açmak için **Yapılandır** ' ı seçin.
    3. Aşağı açılan kutuda Olay Hub 'ınızın oluşturulduğu **aboneliği** seçin.
    4. Açılır kutuda **Olay Hub 'ı ad alanını seçin** .
    5. Açılır kutuda **Olay Hub 'ı ilke adı** ' nı seçin.
    6. Tamam ' ı seçin.

    ### <a name="send-to-log-analytics"></a>Log Analytics'e gönder
    Bu işlem için oluşturulmuş ve yapılandırılmış bir Log Analytics çalışma alanınız olması gerekir.  Log Analytics çalışma alanı oluşturmak için, bkz [. Azure portal Log Analytics çalışma alanı oluşturma](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

    1. **Log Analytics gönder**' in yanındaki onay kutusunu seçin.
    2. Log Analytics çalışma alanınızın açılır kutuda olduğu **aboneliği** seçin.
    3. Açılır kutudan **Log Analytics çalışma alanını** seçin.


8. **Tanılama ayarları** bölmesindeki **günlük** bölümünün altında her ikisinin yanındaki onay kutusunu işaretleyin:
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  **Tanılama ayarları** bölmesindeki **ölçüm** bölümünün altında, şunun yanındaki onay kutusunu işaretleyin:
   * **Allölçümleri**

11. Her şeyin doğru göründüğünü doğrulayın ve **Tanılama ayarları** oluştur bölmesinin en üstünde **Kaydet** ' e tıklayın.

## <a name="activity-log"></a>Etkinlik günlüğü

Etkinlik günlüğü varsayılan olarak oluşturulur. Günlükler, Azure 'un olay günlükleri deposunda 90 gün boyunca korunur. [Kaynaklardaki eylemleri izlemek için etkinlik günlüklerini görüntüle](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) makalesindeki bu Günlükler hakkında daha fazla bilgi edinin makalesini okuyun.

## <a name="archive-to-storage-account-logs"></a>Depolama hesabı günlüklerine Arşivle

### <a name="alert-event-log"></a>Uyarı olay günlüğü

Bu günlük yalnızca, yük dengeleyici temelinde etkinleştirildiyse oluşturulur. Olaylar JSON biçiminde günlüğe kaydedilir ve günlüğü etkinleştirdiğinizde belirttiğiniz depolama hesabında depolanır. Aşağıdaki örnek bir olaydır.

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

JSON çıktısı, yük dengeleyicinin bir uyarı oluşturduğu nedeni açıklayan *EventName* özelliğini gösterir. Bu durumda, oluşturulan uyarı kaynak IP NAT sınırları (SNAT) nedeniyle oluşan TCP bağlantı noktası tükenmesi nedeniyle oluştu.

### <a name="health-probe-log"></a>Durum araştırma günlüğü

Bu günlük yalnızca, yukarıda ayrıntılandırılan yük dengeleyici temelinde etkinleştirildiyse oluşturulur. Veriler, günlüğü etkinleştirdiğinizde belirttiğiniz depolama hesabında depolanır. ' Insights-logs-loadbalancerprobehealthstatus ' adlı bir kapsayıcı oluşturulur ve aşağıdaki veriler günlüğe kaydedilir:

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

JSON çıktısı, araştırma sistem durumu için temel bilgileri Özellikler alanında gösterir. *Dıaltdowncount* özelliği, başarısız araştırma yanıtları nedeniyle ağ trafiği almıyor arka uçtaki toplam örnek sayısını gösterir.

### <a name="view-and-analyze-the-audit-log"></a>Denetim günlüğünü görüntüleyin ve çözümleyin

Aşağıdaki yöntemlerden herhangi birini kullanarak denetim günlüğü verilerini görüntüleyebilir ve çözümleyebilirsiniz:

* **Azure Araçları:** Azure PowerShell, Azure komut satırı arabirimi (CLı), Azure REST API veya Azure portal aracılığıyla denetim günlüklerinden bilgi alın. Her yöntemin adım adım yönergeleri [Kaynak Yöneticisi makalesinde denetim işlemlerinde](../azure-resource-manager/resource-group-audit.md) ayrıntılı olarak açıklanmıştır.
* **Power BI:** Zaten bir [Power BI](https:// .microsoft.com/pricing) hesabınız yoksa ücretsiz olarak deneyebilirsiniz. [Power BI Için Azure denetim günlükleri içerik paketini](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)kullanarak verilerinizi önceden yapılandırılmış panolar ile çözümleyebilir veya görünümleri gereksinimlerinize uyacak şekilde özelleştirebilirsiniz.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>Sistem durumu araştırmasını ve olay günlüğünü görüntüleyin ve çözümleyin

Depolama hesabınıza bağlanın ve olay ve durum araştırma günlükleri için JSON günlüğü girdilerini alın. JSON dosyalarını indirdikten sonra Excel, Power BI veya başka bir veri görselleştirme aracında CSV 'ye dönüştürebilir ve bu verileri görüntüleyebilirsiniz.

> [!TIP]
> Visual Studio ve C# ile sabit ve değişken değerlerini değiştirme konusunda temel kavramlara hakimseniz GitHub'daki [günlük dönüştürücü araçlarını](https://github.com/Azure-Samples/networking-dotnet-log-converter) kullanabilirsiniz.

## <a name="stream-to-an-event-hub"></a>Bir olay hub'ına akış yap
Tanılama bilgileri bir olay hub 'ına akış yapıldığında, Azure Izleyici tümleştirmesiyle üçüncü taraf SıEM aracında Merkezi günlük analizi için kullanılabilir. Daha fazla bilgi için bkz. [Azure izleme verilerini bir olay hub 'ına akış](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Log Analytics'e gönder
Azure 'daki kaynakların, tanılama bilgileri, sorun giderme ve analizine yönelik bilgilere karşı, karmaşık sorguların çalıştırılabileceği bir Log Analytics çalışma alanına doğrudan gönderilmesini sağlayabilir.  Daha fazla bilgi için bkz. Azure [izleyici 'de Log Analytics çalışma alanında Azure Kaynak günlüklerini toplama](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)

## <a name="next-steps"></a>Sonraki adımlar

[Yük dengeleyici araştırmalarını anlama](load-balancer-custom-probe-overview.md)
