---
title: Azure Service Fabric kafes uygulamalarında izleme ve tanılama
description: Azure 'da Service Fabric ağı 'nda uygulama izleme ve tanılama hakkında bilgi edinin.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: d8859293b4853cbfa8c3b3dd0e7d1bfe4f75fc40
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766178"
---
# <a name="monitoring-and-diagnostics"></a>İzleme ve tanılama

> [!IMPORTANT]
> Azure Service Fabric ağı önizlemesi devre dışı bırakıldı. Yeni dağıtımlar Service Fabric kafes API 'SI aracılığıyla artık izin verilmeyecektir. Mevcut dağıtımlar için destek 28 Nisan 2021 ' den devam edecektir.
> 
> Ayrıntılar için bkz. [Azure Service Fabric kafes önizleme kullanımdan](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)kaldırma.

Azure Service Fabric Mesh, geliştiricilerin sanal makineleri, depolama alanını veya ağ bileşenlerini yönetmeden mikro hizmet uygulamaları dağıtmasını sağlayan tam olarak yönetilen bir hizmettir. Service Fabric ağ için izleme ve tanılama, üç temel tanılama verisi türüne kategorize edilir:

- Uygulama günlükleri-Bunlar, uygulamanızı nasıl kapatdığınıza (ör. Docker günlükleri) göre Kapsayıcılı uygulamalardaki Günlükler olarak tanımlanmıştır
- Platform olayları-şu anda kapsayıcı etkinleştirme, devre dışı bırakma ve sonlandırma dahil olmak üzere, ağ platformunun kapsayıcı işleminizi ilgilendiren olaylar.
- Kapsayıcı ölçümleri-kapsayıcılarınız için kaynak kullanımı ve performans ölçümleri (Docker istatistikleri)

Bu makalede, kullanılabilir en son önizleme sürümü için izleme ve tanılama seçenekleri açıklanmaktadır.

## <a name="application-logs"></a>Uygulama günlükleri

Her kapsayıcı için dağıtılan kapsayıcılarınızdaki Docker günlüklerinizi görüntüleyebilirsiniz. Service Fabric kafes uygulama modelinde, her kapsayıcı uygulamanızdaki bir kod paketidir. Bir kod paketiyle ilişkili günlükleri görmek için aşağıdaki komutu kullanın:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Çoğaltma adını almak için "az kafes Service-Replica" komutunu kullanabilirsiniz. Çoğaltma adları 0 ' dan tamsayıları artırmadır.

Bu, oylama uygulamasındaki VotingWeb. Code kapsayıcısından günlükleri görmek için şöyle görünür:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Kapsayıcı ölçümleri 

Ağ ortamı, kapsayıcılarınızın nasıl gerçekleştiğini gösteren bir dizi ölçüm sunar. Aşağıdaki ölçümler Azure portal ve Azure izleyici CLı aracılığıyla sunulmaktadır:

| Metric | Açıklama | Birimler|
|----|----|----|
| Cpukullanımı | Yüzde olarak ActualCpu/Ayrıtedcpu | % |
| Memoryutilileştirme | Yüzde olarak ActualMem/Ayrıtedbellek | % |
| Ayrılatedcpu | Azure Resource Manager şablona göre ayrılan CPU | Milicore |
| Ayrılan bellek | Azure Resource Manager şablonuna göre ayrılan bellek | MB |
| ActualCpu | CPU kullanımı | Milicore |
| ActualMemory | Bellek kullanımı | MB |
| ContainerStatus | 0-geçersiz: kapsayıcı durumu bilinmiyor <br> 1-bekliyor: kapsayıcı başlatılmaya zamanlandı <br> 2-başlatılıyor: kapsayıcı başlangıç sürecinde <br> 3-başlatıldı: kapsayıcı başarıyla başlatıldı <br> 4-durduruluyor: kapsayıcı durduruluyor <br> 5-durduruldu: kapsayıcı başarıyla durduruldu | Yok |
| ApplicationStatus | 0-bilinmiyor: durum alınabilir değil <br> 1-Ready: uygulama başarıyla çalışıyor <br> 2-yükseltiliyor: sürmekte olan bir yükseltme var <br> 3-oluşturma: uygulama oluşturuluyor <br> 4-silme: uygulama siliniyor <br> 5-başarısız: uygulama dağıtılamadı | Yok |
| ServiceStatus | 0-geçersiz: hizmetin Şu anda bir sistem durumu yok <br> 1-Tamam: hizmet sağlıklı  <br> 2-Uyarı: araştırma gerektiren yanlış bir sorun olabilir <br> 3-hata: araştırma gerektiren bir sorun oluştu <br> 4-bilinmiyor: durum alınabilir değil | Yok |
| Servicereperepstatus | 0-geçersiz: çoğaltmanın Şu anda bir sistem durumu yok <br> 1-Tamam: hizmet sağlıklı  <br> 2-Uyarı: araştırma gerektiren yanlış bir sorun olabilir <br> 3-hata: araştırma gerektiren bir sorun oluştu <br> 4-bilinmiyor: durum alınabilir değil | Yok | 
| RestartCount | Kapsayıcı yeniden başlatmaları sayısı | Yok |

> [!NOTE]
> ServiceStatus ve Servicereperepstatus değerleri, Service Fabric [HealthState](/dotnet/api/system.fabric.health.healthstate) ile aynıdır.

Her ölçüm farklı boyutlarda kullanılabilir ve bu sayede toplamaları farklı düzeylerde görebilirsiniz. Geçerli boyut listesi aşağıdaki gibidir:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> CodePackageName Dimension, Linux uygulamaları için kullanılamaz. 

Her boyut [Service Fabric uygulama modelinin](service-fabric-mesh-service-fabric-resources.md#applications-and-services) farklı bileşenlerine karşılık gelir

### <a name="azure-monitor-cli"></a>Azure Izleyici CLı

Komutların tam listesi [Azure IZLEYICI CLI belgelerinden](/cli/azure/monitor/metrics#az_monitor_metrics_list) edinilebilir ancak aşağıda birkaç faydalı örnek verilmiştir 

Her örnekte, kaynak KIMLIĞI bu düzene uyar

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Bir uygulamadaki kapsayıcıların CPU kullanımı

```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Her hizmet çoğaltması için bellek kullanımı
```azurecli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* 1 saatlik penceredeki her bir kapsayıcı için yeniden başlatmalar 
```azurecli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* 1 saatlik bir pencerede "VotingWeb" adlı hizmetler genelinde ortalama CPU kullanımı
```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Ölçüm gezgini

Ölçüm Gezgini, portaldaki, kafes uygulamanız için tüm ölçümleri görselleştirebileceğiniz bir dikey pencere. Bu dikey pencere, portalın ve Azure izleyici dikey penceresindeki uygulamanın sayfasında, Azure Izleyicisini destekleyen tüm Azure kaynaklarınızın ölçümlerini görüntülemek için kullanabileceğiniz Azure izleyici dikey penceresinde erişilebilir. 

![Ölçüm Gezgini](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Sonraki adımlar
* Service Fabric Mesh hakkında daha fazla bilgi edinmek için [Service Fabric Mesh’e genel bakış](service-fabric-mesh-overview.md) makalesini okuyun.
* Azure Izleyici ölçümleri komutları hakkında daha fazla bilgi edinmek için [Azure IZLEYICI CLI belgelerine](/cli/azure/monitor/metrics#az_monitor_metrics_list)göz atın.
