---
title: Azure Service Fabric Windows Azure Tanılama ile olay toplama | Microsoft Docs
description: Azure Service Fabric kümelerini izleme ve Tanılama için WAD kullanarak olayları toplama ve toplama hakkında bilgi edinin.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: 555a8a823526a51b045b4a0314ef7610bf728e5b
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242980"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Windows Azure Tanılama kullanarak olay toplama ve toplama
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Azure Service Fabric kümesi çalıştırırken, günlükleri merkezi konumdaki tüm düğümlerden toplamak iyi bir fikirdir. Günlüklerin merkezi bir konumda olması, kümenizdeki sorunları veya bu kümede çalışan uygulama ve hizmetlerde sorunları analiz etmenize ve gidermenize yardımcı olur.

Günlükleri karşıya yükleme ve toplamanın bir yolu, Azure depolama 'ya günlükleri yükleyen ve ayrıca Azure Application Insights veya Event Hubs günlükleri gönderme seçeneğine sahip olan Windows Azure Tanılama (WAD) uzantısını kullanmaktır. Ayrıca, olayları depolama alanından okumak ve [Azure izleyici günlükleri](../log-analytics/log-analytics-service-fabric.md) ya da başka bir günlük ayrıştırma çözümü gibi bir çözümleme platformu ürününe yerleştirmek için bir dış işlem de kullanabilirsiniz.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar
Bu makalede aşağıdaki araçlar kullanılır:

* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Resource Manager şablonu](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>Service Fabric platform olayları
Service Fabric, aşağıdaki kanalların bir depolama tablosuna veya başka bir yere izleme ve Tanılama verileri göndermek için uzantısıyla önceden yapılandırıldığı birkaç [hazır olmayan günlüğe kaydetme kanalı](service-fabric-diagnostics-event-generation-infra.md)sağlar:
  * [İşletimsel olaylar](service-fabric-diagnostics-event-generation-operational.md): Service Fabric platformun gerçekleştirdiği daha üst düzey işlemler. Örnek olarak uygulama ve hizmet oluşturma, düğüm durumu değişiklikleri ve yükseltme bilgileri sayılabilir. Bunlar, Windows için olay Izleme (ETW) günlükleri olarak yayınlanır
  * [Reliable Actors programlama modeli olayları](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services programlama modeli olayları](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Tanılama uzantısını Portal aracılığıyla dağıtma
Günlükleri toplamanın ilk adımı, Service Fabric kümesindeki sanal makine ölçek kümesi düğümlerinde tanılama uzantısını dağıtmaktır. Tanılama uzantısı her bir VM 'de günlükleri toplar ve bunları belirttiğiniz depolama hesabına yükler. Aşağıdaki adımlar, Azure portal ve Azure Resource Manager şablonları aracılığıyla yeni ve mevcut kümeler için bunu nasıl gerçekleştirebileceğiniz ana hatlarıyla gösterilmiştir.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Tanılama uzantısını Azure portal ile küme oluşturmanın bir parçası olarak dağıtma
Kümenizi oluştururken, küme yapılandırma adımında, isteğe bağlı ayarları genişletin ve tanılamaların **Açık** (varsayılan ayar) olarak ayarlandığından emin olun.

![Portalda küme oluşturma için Azure Tanılama ayarları](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Son adımda **Oluştur ' a tıklamadan önce** şablonu indirmenizi kesinlikle öneririz. Ayrıntılar için, [Azure Resource Manager şablonu kullanarak Service Fabric kümesi ayarlama](service-fabric-cluster-creation-via-arm.md)bölümüne bakın. Verilerin toplanacağı kanallar (yukarıda listelenen) üzerinde değişiklik yapabilmek için şablona ihtiyacınız vardır.

![Küme şablonu](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Azure Storage 'da olayları toplayarak, Azure izleyici günlüklerini Azure izleyici günlükleri portalında Öngörüler elde etmek ve sorgulamak için [ayarlayın](service-fabric-diagnostics-oms-setup.md) .

>[!NOTE]
>Şu anda tablolara gönderilen olayları filtrelemek veya eklemek için bir yol yoktur. Olayları tablodan kaldırmak için bir işlem gerçekleştirmezseniz tablo büyümeye devam eder (varsayılan sınır 50 GB 'tır). Bunun nasıl değiştirileceği hakkında yönergeler [Bu makalede aşağıda](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota)verilmiştir. Ayrıca, [izleme örneğinde](https://github.com/Azure-Samples/service-fabric-watchdog-service)çalışan bir veri temizleme hizmeti örneği de vardır ve günlükleri 30 veya 90 günlük zaman dilimi dışında depolamanız için iyi bir neden olmadıkça, kendiniz de bir tane yazmanız önerilir.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Tanılama uzantısını Azure Resource Manager aracılığıyla dağıtma

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Tanılama Uzantısı ile küme oluşturma
Kaynak Yöneticisi kullanarak bir küme oluşturmak için, tanılama yapılandırma JSON 'sini tam Kaynak Yöneticisi şablonuna eklemeniz gerekir. Kaynak Yöneticisi Şablon örneklerimizin bir parçası olarak, tanılama yapılandırması eklenmiş bir örnek beş VM kümesi Kaynak Yöneticisi şablonu sağlıyoruz. Azure örnekleri galerisinde şu konumda görebilirsiniz: [tanılama Kaynak Yöneticisi Şablon örneği Ile beş düğümlü küme](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Kaynak Yöneticisi şablonunda tanılama ayarını görmek için azuredeploy. json dosyasını açın ve **ıaasdiagnostics**' i arayın. Bu şablonu kullanarak bir küme oluşturmak için önceki bağlantıda bulunan **Azure 'A dağıt** düğmesini seçin.

Alternatif olarak, Kaynak Yöneticisi örneğini indirebilir, üzerinde değişiklik yapabilir ve bir Azure PowerShell penceresinde `New-AzResourceGroupDeployment` komutunu kullanarak değiştirilmiş şablonla bir küme oluşturabilirsiniz. Komutuna geçirdiğiniz parametreler için aşağıdaki koda bakın. PowerShell kullanarak bir kaynak grubu dağıtma hakkında ayrıntılı bilgi için, [Azure Resource Manager şablonuyla kaynak grubu dağıtma](../azure-resource-manager/resource-group-template-deploy.md)makalesine bakın.

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Tanılama uzantısını var olan bir kümeye ekle
Tanılamayı dağıtmayan mevcut bir kümeniz varsa, küme şablonu aracılığıyla ekleyebilir veya güncelleştirebilirsiniz. Mevcut kümeyi oluşturmak için kullanılan Kaynak Yöneticisi şablonunu değiştirin ya da daha önce açıklandığı gibi portaldan şablonu indirin. Aşağıdaki görevleri gerçekleştirerek Template. json dosyasını değiştirin:

Kaynaklar bölümüne ekleyerek şablona yeni bir depolama kaynağı ekleyin.

```json
{
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
    "name": "[parameters('applicationDiagnosticsStorageAccountType')]"
    "tier": "standard"
  },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Daha sonra, `supportLogStorageAccountName`arasında depolama hesabı tanımlarından hemen sonra parametreler bölümüne ekleyin. Yer tutucu metin *depolama hesabı adının yerine buraya* , istediğiniz depolama hesabının adını girin.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Ardından, Extensions dizisine aşağıdaki kodu ekleyerek Template. json dosyasının `VirtualMachineProfile` bölümünü güncelleştirin. Eklendiği yere bağlı olarak, başlangıca veya sonunda virgül eklediğinizden emin olun.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                },
                {
                    "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Template. json dosyasını açıklandığı şekilde değiştirdikten sonra Kaynak Yöneticisi şablonunu yeniden yayımlayın. Şablon aktarılıyorsa, Deploy. ps1 dosyasını çalıştırmak şablonu ikinci olarak çalıştırır. Dağıttıktan sonra, **Provisioningstate** 'in **başarılı**olduğundan emin olun.

> [!TIP]
> Kümelerinize kapsayıcılar dağıtacaksanız WAD 'yi **Wadcfg > DiagnosticMonitorConfiguration** bölümüne ekleyerek Docker istatistiklerini seçmesini etkinleştirin.
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>Depolama kotasını güncelleştirme

Uzantı tarafından doldurulan tablolar, kotaya ulaşılana kadar büyüdükçe, kota boyutunu azaltmasını isteyebilirsiniz. Varsayılan değer 50 GB 'tır ve `DiagnosticMonitorConfiguration` altındaki `overallQuotaInMB` alanı altındaki şablonda yapılandırılabilir.

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Günlük koleksiyonu yapılandırması
Ek kanallara ait Günlükler koleksiyon için de kullanılabilir. Azure 'da çalışan kümeler için şablonda yapabileceğiniz en yaygın yapılandırmaların bazıları aşağıda verilmiştir.

* İşletimsel kanal-temel: Service Fabric ve küme tarafından gerçekleştirilen, bir düğüm için olaylar, dağıtılan yeni bir uygulama veya yükseltme geri alma gibi işlemleri de içeren, varsayılan olarak etkinleştirilen üst düzey işlemler. Olayların listesi için [Işlemsel kanal olaylarına](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational)bakın.
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* İşletimsel kanal-ayrıntılı: Bu durum raporlarını ve yük dengeleme kararlarını, ayrıca temel işletimsel kanaldaki her şeyi içerir. Bu olaylar, [Reportpartitionhealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) veya [reportload](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx)gibi sistem durumu ya da yük Raporlama API 'leri kullanılarak sistem veya kodunuz tarafından oluşturulur. Visual Studio 'nun tanılamasında bu olayları görüntülemek için Olay Görüntüleyicisi ETW sağlayıcıları listesine "Microsoft-ServiceFabric: 4:0x4000000000000008" ekleyin.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Veri ve mesajlaşma kanalı-temel: ayrıntılı işlemsel kanal günlüklerine ek olarak, mesajlaşma 'da (Şu anda yalnızca bir Tara sunucu) ve veri yolunda oluşturulan kritik Günlükler ve olaylar. Bu olaylar, istek işleme hataları ve bu istekteki diğer kritik sorunların yanı sıra, işlenen istekleri de ister. **Bu, kapsamlı günlük kaydı için önerimiz**. Bu olayları Visual Studio 'nun tanılama Olay Görüntüleyicisi görüntülemek için, ETW sağlayıcıları listesine "Microsoft-ServiceFabric: 4:0x4000000000000010" ekleyin.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Veri & mesajlaşma kanalı-ayrıntılı: kümedeki önemli olmayan tüm günlükleri ve ayrıntılı işlem kanalını içeren ayrıntılı kanal. Tüm ters proxy olayları hakkında ayrıntılı sorun giderme için, [ters proxy tanılama Kılavuzu](service-fabric-reverse-proxy-diagnostics.md)' na bakın.  Visual Studio 'nun Tanılama olay görüntüleyicisinde bu olayları görüntülemek için ETW sağlayıcıları listesine "Microsoft-ServiceFabric: 4:0x4000000000000020" ekleyin.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Bu kanalın çok yüksek bir olay hacmi vardır ve bu ayrıntılı kanaldan olay toplamanın etkinleştirilmesi, hızlı bir şekilde oluşturulan birçok izleme ile sonuçlanır ve depolama kapasitesini tüketebilir. Bunu yalnızca kesinlikle gerekliyse açın.


**Temel Işlem kanalını** , en az gürültü miktarıyla kapsamlı günlük için önerimizi etkinleştirmek üzere, şablonunuzun `WadCfg` `EtwManifestProviderConfiguration` aşağıdaki gibi görünür:

```json
  "WadCfg": {
        "DiagnosticMonitorConfiguration": {
          "overallQuotaInMB": "50000",
          "EtwProviders": {
            "EtwEventSourceProviderConfiguration": [
              {
                "provider": "Microsoft-ServiceFabric-Actors",
                "scheduledTransferKeywordFilter": "1",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableActorEventTable"
                }
              },
              {
                "provider": "Microsoft-ServiceFabric-Services",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableServiceEventTable"
                }
              }
            ],
            "EtwManifestProviderConfiguration": [
              {
                "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricSystemEventTable"
                }
              },
              {
                "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                "eventDestination": "ServiceFabricSystemEventTable"
                }
              }
            ]
          }
        }
      },
```

## <a name="collect-from-new-eventsource-channels"></a>Yeni EventSource kanallarından topla

Dağıtmak üzere olduğunuz yeni bir uygulamayı temsil eden yeni EventSource kanallarından günlükleri toplamak üzere tanılamayı güncelleştirmek için, var olan bir küme için tanılamayı ayarlama konusunda daha önce açıklanan adımları gerçekleştirin.

`New-AzResourceGroupDeployment` PowerShell komutunu kullanarak yapılandırma güncelleştirmesini uygulamadan önce yeni EventSource kanallarının girdilerini eklemek üzere Template. JSON dosyasındaki `EtwEventSourceProviderConfiguration` bölümünü güncelleştirin. Olay kaynağının adı, Visual Studio tarafından oluşturulan ServiceEventSource.cs dosyasındaki kodunuzun bir parçası olarak tanımlanır.

Örneğin, olay kaynağınız My-EventSource olarak adlandırılmışsa, olayları MyDestinationTableName adlı bir tabloya yerleştirmek için aşağıdaki kodu ekleyin.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Performans sayaçlarını veya olay günlüklerini toplamak için, [bir Azure Resource Manager şablonu kullanarak izleme ve tanılama Ile Windows sanal makinesi oluşturma](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)bölümünde verilen örnekleri kullanarak kaynak yöneticisi şablonunu değiştirin. Sonra Kaynak Yöneticisi şablonunu yeniden yayımlayın.

## <a name="collect-performance-counters"></a>Performans sayaçlarını topla

Kümenizdeki performans ölçümlerini toplamak için, kümenizin Kaynak Yöneticisi şablonunda performans sayaçlarını "WadCfg > DiagnosticMonitorConfiguration" dizinine ekleyin. Belirli performans sayaçlarını toplamak için `WadCfg` değiştirme adımları için bkz. [WAD Ile performans izleme](service-fabric-diagnostics-perf-wad.md) . Toplamayı önerdiğimiz performans sayaçlarının listesi için başvuru [Service Fabric performans sayaçları](service-fabric-diagnostics-event-generation-perf.md) .
  
Aşağıdaki bölümde açıklandığı gibi bir Application Insights havuzu kullanıyorsanız ve bu ölçümlerin Application Insights gösterilmesini istiyorsanız, yukarıdaki gibi "Havuzlar" bölümüne havuz adını eklediğinizden emin olun. Bu işlem, Application Insights kaynağına ayrı ayrı yapılandırılmış performans sayaçlarını otomatik olarak gönderir.


## <a name="send-logs-to-application-insights"></a>Günlükleri Application Insights gönder

### <a name="configuring-application-insights-with-wad"></a>WAD ile Application Insights yapılandırma

>[!NOTE]
>Bu, şu anda yalnızca Windows kümeleri için geçerlidir.

WAD yapılandırmasına, Azure portal veya bir Azure Resource Manager şablonuyla bir Application Insights havuzu eklenerek elde edilen Azure Application Insights 'den verileri göndermenin iki temel yolu vardır.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Azure portal bir küme oluştururken Application Insights bir Izleme anahtarı ekleyin

![Bir AIKey ekleme](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Bir küme oluştururken, tanılama "açık" olarak ayarlandığında, Application Insights bir Izleme anahtarı girmek için isteğe bağlı bir alan görüntülenir. Application Insights anahtarınızı buraya yapıştırırsanız, Application Insights havuzu kümenizi dağıtmak için kullanılan Kaynak Yöneticisi şablonunda sizin için otomatik olarak yapılandırılır.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Application Insights havuzunu Kaynak Yöneticisi şablonuna ekleyin

Kaynak Yöneticisi şablonunun "WadCfg" bölümünde, aşağıdaki iki değişikliği dahil ederek bir "havuz" ekleyin:

1. `DiagnosticMonitorConfiguration` bildirme işlemi tamamlandıktan sonra havuz yapılandırmasını doğrudan ekleyin:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Aşağıdaki satırı `WadCfg` `DiagnosticMonitorConfiguration` ekleyerek havuzu `DiagnosticMonitorConfiguration` ekleyin (`EtwProviders` bildirilmeden önce):

    ```json
    "sinks": "applicationInsights"
    ```

Yukarıdaki kod parçacıklarında, havuzu anlatmak için "ApplicationInsights" adı kullanılmıştır. Bu bir gereksinim değildir ve havuz adı "Havuzlar" a dahil olduğu sürece, adı herhangi bir dizeye ayarlayabilirsiniz.

Şu anda, kümedeki Günlükler Application Insights ' günlük görüntüleyicisinde **izleme** olarak görünür. Platformdan gelen izlemelerin çoğu "bilgilendirme" düzeyine sahip olduğundan, havuz yapılandırmasını yalnızca "uyarı" veya "hata" türünde Günlükler gönderecek şekilde değiştirmeyi de düşünebilirsiniz. Bu, [Bu makalede](../azure-monitor/platform/diagnostics-extension-to-application-insights.md)gösterildiği gibi, havuzunuzu "Kanallar" eklenerek yapılabilir.

>[!NOTE]
>Portalda veya Kaynak Yöneticisi şablonunuzda yanlış bir Application Insights anahtarı kullanırsanız, anahtarı el ile değiştirmeniz ve kümeyi güncelleştirmeniz/yeniden dağıtmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Azure tanılama 'yı doğru bir şekilde yapılandırdıktan sonra, depolama tablolarınızdaki verileri ETW ve EventSource günlüklerinden görürsünüz. Kaynak Yöneticisi şablonunda doğrudan yapılandırılmayan Azure Izleyici günlüklerini, kibana veya herhangi bir veri analizi ve görselleştirme platformunu kullanmayı tercih ederseniz, bu depolama tablolarından alınan verilerde okumak üzere tercih ettiğiniz platformu ayarladığınızdan emin olun. Bunu Azure Izleyici günlükleri için yapmak nispeten daha basit olur ve [olay ve günlük Analizi](service-fabric-diagnostics-event-analysis-oms.md)bölümünde açıklanmaktadır. Application Insights, bu anlamda tanılama uzantısı yapılandırmasının bir parçası olarak yapılandırılandığından, bu anlamda özel bir durumun bir bittir. bu nedenle, AI kullanmayı seçerseniz [ilgili makaleye](service-fabric-diagnostics-event-analysis-appinsights.md) başvurun.

>[!NOTE]
>Şu anda tabloya gönderilen olayları filtrelemek veya eklemek için bir yol yoktur. Olayları tablodan kaldırmak için bir işlem gerçekleştirmezseniz tablo büyümeye devam edecektir. Şu anda, [izleme örneğinde](https://github.com/Azure-Samples/service-fabric-watchdog-service)çalışan bir veri temizleme hizmeti örneği vardır ve günlükleri 30 veya 90 günlük zaman dilimi dışında depolamanız için iyi bir neden olmadıkça, kendiniz de bir tane yazmanız önerilir.

* [Tanılama uzantısını kullanarak performans sayaçlarını veya günlükleri nasıl toplayacağınızı öğrenin](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Application Insights ile olay Analizi ve görselleştirme](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Azure Izleyici günlükleri ile olay Analizi ve görselleştirme](service-fabric-diagnostics-event-analysis-oms.md)
* [Application Insights ile olay Analizi ve görselleştirme](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Azure Izleyici günlükleri ile olay Analizi ve görselleştirme](service-fabric-diagnostics-event-analysis-oms.md)
