---
title: Azure VM ve Azure sanal makine ölçek kümelerinde barındırılan uygulama performansını izleme | Microsoft Docs
description: Azure VM ve Azure sanal makine ölçek kümeleri için uygulama performansı izleme. Grafik yükleme ve yanıt süresi, bağımlılık bilgileri ve performans üzerinde Uyarılar ayarlama.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/26/2019
ms.openlocfilehash: 248dfb83c26d3f49fb492272ee3bd87d1e34fefa
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161481"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Azure sanal makineler ve Azure sanal makine ölçek kümelerinde Azure Izleyici Application Insights aracısını dağıtma

[Azure sanal makinelerinde](https://azure.microsoft.com/services/virtual-machines/) ve [Azure sanal makine ölçek kümelerinde](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) çalışan .NET tabanlı Web uygulamalarınızda izlemenin etkinleştirilmesi artık hiç olmadığı kadar kolay. Kodunuzda değişiklik yapmadan Application Insights kullanmanın avantajlarından yararlanın.

Bu makale, Application Insights Aracısı kullanarak Application Insights izlemeyi etkinleştirme konusunda size kılavuzluk eder ve büyük ölçekli dağıtımlar için işlemi otomatikleştirmek üzere ön kılavuz sağlar.

> [!IMPORTANT]
> .NET için Azure Application Insights Aracısı Şu anda genel önizlemededir.
> Bu önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve bunu üretim iş yükleri için önermiyoruz. Bazı özellikler desteklenmeyebilir ve bazıları kısıtlı özelliklere sahip olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Application Insights'ı etkinleştirme

Azure sanal makineleri ve Azure sanal makine ölçek kümeleri barındırılan uygulamalar için uygulama izlemeyi etkinleştirmenin iki yolu vardır:

* Application Insights Aracısı aracılığıyla **codeless**
    * Bu yöntem etkinleştirilmesi en kolayıdır ve gelişmiş yapılandırma gerekmez. Genellikle "çalışma zamanı" izleme olarak adlandırılır.

    * Azure sanal makineleri ve Azure sanal makine ölçek kümeleri için, bu izleme düzeyini en az etkinleştirmenize önerilir. Bu tarihten sonra, özel senaryonuza bağlı olarak, el ile izleme gerekip gerekmediğini değerlendirebilirsiniz.

    * Application Insights Aracısı .NET SDK 'Sı ile aynı bağımlılık sinyallerini otomatik olarak toplar. Daha fazla bilgi için bkz. [bağımlılık otomatik koleksiyonu](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net) .
        > [!NOTE]
        > Şu anda yalnızca .NET IIS tarafından barındırılan uygulamalar desteklenir. Bir Azure sanal makinelerinde ve sanal makine ölçek kümelerinde barındırılan ASP.NET Core, Java ve Node. js uygulamalarını işaretlemek için bir SDK kullanın.

* SDK aracılığıyla **kod tabanlı**

    * Bu yaklaşım çok daha özelleştirilebilir, ancak [APPLICATION INSIGHTS SDK NuGet paketlerine bağımlılık eklemeyi](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)gerektirir. Bu yöntem, ayrıca paketlerin en son sürümüne yönelik güncelleştirmeleri yönetmeniz anlamına gelir.

    * Aracı tabanlı izleme ile varsayılan olarak yakalanmayan olayları/bağımlılıkları izlemek için özel API çağrıları yapmanız gerekiyorsa, bu yöntemi kullanmanız gerekir. Daha fazla bilgi edinmek için [özel olaylar ve ölçümler makalesine yönelik API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) 'ye göz atın.

> [!NOTE]
> Hem aracı tabanlı izleme hem de el ile SDK tabanlı izleme algılanırsa yalnızca el ile izleme ayarları kabul edilir. Bu, yinelenen verilerin gönderilmesini önlemektir. Bu konuda daha fazla bilgi edinmek için aşağıdaki [sorun giderme bölümüne](#troubleshooting) bakın.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>PowerShell kullanarak Azure sanal makinelerinde .NET uygulamaları için Application Insights aracısını yönetme

> [!NOTE]
> Application Insights aracısını yüklemeden önce bir izleme anahtarına ihtiyacınız olacaktır. [Yeni bir Application Insights kaynağı oluşturun](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) veya mevcut bir Application Insights kaynağından izleme anahtarını kopyalayın.

> [!NOTE]
> PowerShell 'de yeni misiniz? [Başlarken kılavuzuna](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0)göz atın.

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
          "instrumentationSettings" : {
            "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
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
Ayrıca, portaldaki [Azure sanal makine dikey](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) penceresinde yüklü uzantıları da görüntüleyebilirsiniz.

> [!NOTE]
> Application Insights Aracısı uzantısını dağıtmak için kullandığınız izleme anahtarıyla ilişkili Application Insights kaynak içindeki Canlı Ölçüm Akışı tıklayarak yüklemeyi doğrulayın. Birden çok sanal makineden veri gönderiyorsanız, sunucu adı altında hedef Azure sanal makinelerini seçin. Verilerin akışa başlaması bir dakika kadar sürebilir.

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
          "instrumentationSettings"= @{
            "instrumentationKey"= "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"; # Application Insights Instrumentation Key, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
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
> .NET Core, Java ve Node. js uygulamaları yalnızca el ile SDK tabanlı araçlar aracılığıyla Azure sanal makinelerinde ve Azure sanal makine ölçek kümelerinde desteklenir ve bu nedenle aşağıdaki adımlar bu senaryolara uygulanmaz.

Uzantı yürütme çıktısı, aşağıdaki dizinlerde bulunan dosyalara kaydedilir:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Sonraki adımlar
* Bir [Azure sanal makine ölçek kümesine uygulama dağıtmayı](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)öğrenin.
* Uç noktanız kapalıysa, [kullanılabilirlik Web testlerini](monitor-web-app-availability.md) uyarılmak için ayarlayın.
