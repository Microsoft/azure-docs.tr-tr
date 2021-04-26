---
title: Azure VM 'lerde performansı izleme-Azure Application Insights
description: Azure VM ve Azure sanal makine ölçek kümeleri için uygulama performansı izleme. Grafik yükleme ve yanıt süresi, bağımlılık bilgileri ve performans üzerinde Uyarılar ayarlama.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 0951d1d622f59de4780735fad78ac73649ea2369
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711493"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Azure sanal makineler ve Azure sanal makine ölçek kümelerinde Azure Izleyici Application Insights aracısını dağıtma

[Azure sanal makinelerinde](https://azure.microsoft.com/services/virtual-machines/) ve [Azure sanal makine ölçek kümelerinde](../../virtual-machine-scale-sets/index.yml) çalışan .NET veya Java tabanlı Web uygulamalarınız için izlemeyi etkinleştirmek artık hiç olmadığı kadar kolay. Kodunuzda değişiklik yapmadan Application Insights kullanmanın avantajlarından yararlanın.

Bu makale, Application Insights Aracısı kullanarak Application Insights izlemeyi etkinleştirme konusunda size kılavuzluk eder ve büyük ölçekli dağıtımlar için işlemi otomatikleştirmek üzere ön kılavuz sağlar.
> [!IMPORTANT]
> Azure VM 'lerde ve VMSS üzerinde çalışan **Java** tabanlı uygulamalar, genel kullanıma sunulan **[Application Insights Java 3,0 aracısıyla](./java-in-process-agent.md)** izlenir.

> [!IMPORTANT]
> **Azure VM 'lerinde ve VMSS** 'de çalışan ASP.NET uygulamaları için Azure Application Insights Aracısı Şu anda genel önizlemededir. **Şirket içinde** çalışan ASP.NET uygulamalarınızı izlemek için, genel olarak kullanılabilen ve tam olarak desteklenen [Şirket Içi sunucular Için Azure Application Insights aracısını](./status-monitor-v2-overview.md)kullanın.
> Azure VM 'Leri ve VMSS 'nin önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve bunu üretim iş yükleri için önermiyoruz. Bazı özellikler desteklenmeyebilir ve bazıları kısıtlı özelliklere sahip olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Application Insights'ı etkinleştirme

Azure sanal makineleri ve Azure sanal makine ölçek kümeleri barındırılan uygulamalar için uygulama izlemeyi etkinleştirmenin iki yolu vardır:

### <a name="auto-instrumentation-via-application-insights-agent"></a>Application Insights Aracısı aracılığıyla otomatik izleme

* Bu yöntem etkinleştirilmesi en kolayıdır ve gelişmiş yapılandırma gerekmez. Genellikle "çalışma zamanı" izleme olarak adlandırılır.

* Azure sanal makineleri ve Azure sanal makine ölçek kümeleri için, bu izleme düzeyini en az etkinleştirmenize önerilir. Bu tarihten sonra, özel senaryonuza bağlı olarak, el ile izleme gerekip gerekmediğini değerlendirebilirsiniz.

> [!NOTE]
> Otomatik izleme şu anda yalnızca .NET IIS tarafından barındırılan uygulamalar ve Java için kullanılabilir. Bir Azure sanal makinelerinde ve sanal makine ölçek kümelerinde barındırılan ASP.NET Core, Node.js ve Python uygulamalarını işaretlemek için bir SDK kullanın.


#### <a name="net"></a>.NET

  * Application Insights Aracısı .NET SDK 'Sı ile aynı bağımlılık sinyallerini otomatik olarak toplar. Daha fazla bilgi için bkz. [bağımlılık otomatik koleksiyonu](./auto-collect-dependencies.md#net) .
        
#### <a name="java"></a>Java
  * Java için **[Application Insights java 3,0 Aracısı](./java-in-process-agent.md)** önerilen yaklaşımdır. En popüler kitaplıklar ve çerçeveler ve Günlükler ve bağımlılıklar, çok sayıda [ek yapılandırma](./java-standalone-config.md) ile [otomatik olarak toplanır](./java-in-process-agent.md#auto-collected-requests-dependencies-logs-and-metrics)

### <a name="code-based-via-sdk"></a>SDK aracılığıyla kod tabanlı
    
#### <a name="net"></a>.NET
  * .NET uygulamaları için bu yaklaşım çok daha özelleştirilebilirdir, ancak [APPLICATION INSIGHTS SDK NuGet paketlerine bağımlılık eklemeyi](./asp-net.md)gerektirir. Bu yöntem, ayrıca paketlerin en son sürümüne yönelik güncelleştirmeleri yönetmeniz anlamına gelir.

  * Aracı tabanlı izleme ile varsayılan olarak yakalanmayan olayları/bağımlılıkları izlemek için özel API çağrıları yapmanız gerekiyorsa, bu yöntemi kullanmanız gerekir. Daha fazla bilgi edinmek için [özel olaylar ve ölçümler makalesine yönelik API](./api-custom-events-metrics.md) 'ye göz atın.

    > [!NOTE]
    > Yalnızca .NET uygulamaları için-hem aracı tabanlı izleme hem de el ile SDK tabanlı izleme algılanırsa yalnızca el ile izleme ayarları kabul edilir. Bu, yinelenen verilerin gönderilmesini önlemektir. Bu konuda daha fazla bilgi edinmek için aşağıdaki [sorun giderme bölümüne](#troubleshooting) bakın.

#### <a name="net-core"></a>.NET Core
.NET Core uygulamalarını izlemek için [SDK](./asp-net-core.md) 'yı kullanın 

#### <a name="java"></a>Java 

Java uygulamaları için ek özel telemetri gerekiyorsa, bkz. [kullanılabilir](./java-in-process-agent.md#send-custom-telemetry-from-your-application), [Özel boyutlar](./java-standalone-config.md#custom-dimensions)ekleme veya [telemetri işlemcileri](./java-standalone-telemetry-processors.md)kullanma. 

#### <a name="nodejs"></a>Node.js

Node.js uygulamanızı işaretlemek için [SDK 'yı](./nodejs.md)kullanın.

#### <a name="python"></a>Python

Python uygulamalarını izlemek için [SDK](./opencensus-python.md)'yı kullanın.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>PowerShell kullanarak Azure sanal makinelerinde .NET uygulamaları için Application Insights aracısını yönetme

> [!NOTE]
> Application Insights aracısını yüklemeden önce bir bağlantı dizesine ihtiyacınız vardır. [Yeni bir Application Insights kaynağı oluşturun](./create-new-resource.md) veya var olan bir Application Insights kaynağından bağlantı dizesini kopyalayın.

> [!NOTE]
> PowerShell 'de yeni misiniz? [Başlarken kılavuzuna](/powershell/azure/get-started-azureps)göz atın.

Application Insights aracısını Azure sanal makineleri için bir uzantı olarak yükler veya güncelleştirin
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> Bir PowerShell döngüsü kullanarak, Application Insights aracısını birden çok sanal makine arasında genişleme olarak yükleyebilirsiniz veya güncelleyebilirsiniz.

Application Insights Agent uzantısını Azure sanal makinesinden kaldır
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Azure sanal makinesi için sorgu Application Insights aracısı uzantı durumu
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Azure sanal makinesi için yüklü uzantıların listesini al
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
Ayrıca, portaldaki [Azure sanal makine dikey](../../virtual-machines/extensions/overview.md) penceresinde yüklü uzantıları da görüntüleyebilirsiniz.

> [!NOTE]
> Application Insights Aracısı uzantısını dağıtmak için kullandığınız bağlantı dizesiyle ilişkili Application Insights kaynak içindeki Canlı Ölçüm Akışı tıklayarak yüklemeyi doğrulayın. Birden çok sanal makineden veri gönderiyorsanız, sunucu adı altında hedef Azure sanal makinelerini seçin. Verilerin akışa başlaması bir dakika kadar sürebilir.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>PowerShell kullanarak Azure sanal makine ölçek kümelerinde .NET uygulamaları için Application Insights aracısını yönetme

Application Insights aracısını Azure sanal makine ölçek kümesi için bir uzantı olarak yükler veya güncelleştirin
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Azure sanal makine ölçek kümelerinden uygulama izleme uzantısını kaldır
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Azure sanal makine ölçek kümeleri için sorgu uygulaması izleme uzantısı durumu
```powershell
# Not supported by extensions framework
```

Azure sanal makine ölçek kümeleri için yüklü uzantıların listesini al
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Sorun giderme

Azure sanal makinelerinde ve sanal makine ölçek kümelerinde çalışan .NET uygulamaları için Application Insights Izleme Aracısı uzantısı için sorun giderme ipuçları bulabilirsiniz.

> [!NOTE]
> .NET Core, Node.js ve Python uygulamaları, el ile SDK tabanlı araçlar aracılığıyla yalnızca Azure sanal makinelerinde ve Azure sanal makine ölçek kümelerinde desteklenir ve bu nedenle aşağıdaki adımlar bu senaryolara uygulanmaz.

Uzantı yürütme çıktısı, aşağıdaki dizinlerde bulunan dosyalara kaydedilir:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Sonraki adımlar
* Bir [Azure sanal makine ölçek kümesine uygulama dağıtmayı](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)öğrenin.
* Uç noktanız kapalıysa, [kullanılabilirlik Web testlerini](monitor-web-app-availability.md) uyarılmak için ayarlayın.