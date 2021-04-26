---
title: Windows VM 'de tanılamayı etkinleştirmek için Azure PowerShell kullanma
description: Windows çalıştıran bir sanal makinede Azure Tanılama etkinleştirmek için PowerShell 'in nasıl kullanılacağını öğrenin
ms.topic: article
author: amjads1
ms.author: amjads
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.date: 12/15/2015
ms.openlocfilehash: e242a45e2c0f8247a1c23bf4d43a6e8b0a033d9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560106"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Windows çalıştıran bir sanal makinede Azure Tanılama etkinleştirmek için PowerShell 'i kullanma

Azure Tanılama, Azure 'da dağıtılan bir uygulamadaki tanılama verilerinin toplanmasını sağlayan bir özelliktir. Tanılama uzantısını, Windows çalıştıran bir Azure sanal makinesinden (VM) uygulama günlükleri veya performans sayaçları gibi tanılama verileri toplamak için kullanabilirsiniz. 

 

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Kaynak Yöneticisi dağıtım modelini kullanıyorsanız tanılama uzantısını etkinleştirin
Uzantı yapılandırmasını Kaynak Yöneticisi şablonuna ekleyerek, Azure Resource Manager dağıtım modeli aracılığıyla bir Windows VM oluştururken, tanılama uzantısını etkinleştirebilirsiniz. [Azure Resource Manager şablonunu kullanarak izleme ve tanılama Ile Windows sanal makinesi oluşturma](diagnostics-template.md)konusuna bakın.

Kaynak Yöneticisi dağıtım modeli aracılığıyla oluşturulmuş mevcut bir VM 'de tanılama uzantısını etkinleştirmek için, aşağıda gösterildiği gibi [set-Azvmdiagnosticsextenma](/powershell/module/az.compute/set-azvmdiagnosticsextension) PowerShell cmdlet 'ini kullanabilirsiniz.

```azurepowershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path
```


*$diagnosticsconfig _path* , aşağıdaki [örnekte](#sample-diagnostics-configuration) açıklandığı gibi, XML 'de tanılama yapılandırmasını içeren dosyanın yoludur.  

Tanılama yapılandırma dosyası depolama hesabı adına sahip bir **Storageaccount** öğesi belirtiyorsa, *set-azvmdiagnosticsextenıı* betiği, tanılama uzantısını otomatik olarak bu depolama hesabına gönderecek şekilde ayarlar. Bunun çalışması için, depolama hesabının VM ile aynı abonelikte olması gerekir.

Tanılama yapılandırmasında **Storageaccount** belirtilmemişse, cmdlet 'e *storageAccountName* parametresini geçirmeniz gerekir. *StorageAccountName* parametresi belirtilmişse, cmdlet, her zaman parametresinde belirtilen depolama hesabını kullanır ve bu, tanılama yapılandırma dosyasında belirtilen bir değer değildir.

Tanılama depolama hesabı VM 'den farklı bir abonelikte yer alıyorsa, cmdlet 'e *storageAccountName* ve *storageaccountkey* parametrelerini açıkça geçirmeniz gerekir. Tanılama depolama hesabı aynı abonelikte olduğunda *Storageaccountkey* parametresi gerekli değildir. cmdlet, tanılama uzantısını etkinleştirirken anahtar değerini otomatik olarak sorgulayabilir ve ayarlayabilir. Ancak, tanılama depolama hesabı farklı bir abonelikte ise, cmdlet anahtarı otomatik olarak alamaz ve anahtarı *Storageaccountkey* parametresi aracılığıyla açıkça belirtmeniz gerekir.  

```azurepowershell
Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

Tanılama uzantısı bir VM 'de etkinleştirildikten sonra, [Get-Azvmdiagnosticsextenma](/powershell/module/az.compute/get-azvmdiagnosticsextension) cmdlet 'ini kullanarak geçerli ayarları alabilirsiniz.

```azurepowershell
Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name
```

Cmdlet 'i, tanılama yapılandırmasını içeren *Publicsettings*' i döndürür. WadCfg ve xmlCfg olmak üzere iki tür yapılandırma desteklenir. WadCfg, JSON yapılandırması ve xmlCfg, Base64 kodlamalı bir biçimde XML yapılandırması. XML 'yi okumak için, bu dosyanın kodunu çözmelisiniz.

```azurepowershell
$publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
$encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
$xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
Write-Host $xmlconfig
```

[Remove-Azvmdiagnosticsextenma](/powershell/module/az.compute/remove-azvmdiagnosticsextension) CMDLET 'i VM 'den tanılama uzantısını kaldırmak için kullanılabilir.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Klasik dağıtım modelini kullanıyorsanız tanılama uzantısını etkinleştirin

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Klasik dağıtım modeli aracılığıyla oluşturduğunuz bir VM 'de bir tanılama uzantısını etkinleştirmek için [set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) cmdlet 'ini kullanabilirsiniz. Aşağıdaki örnek, tanılama uzantısı etkin olan klasik dağıtım modeli aracılığıyla yeni bir VM oluşturmayı gösterir.

```azurepowershell
$VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
$VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
$VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM
```

Klasik dağıtım modeli aracılığıyla oluşturulmuş mevcut bir VM 'de tanılama uzantısını etkinleştirmek için önce VM yapılandırmasını almak üzere [Get-AzureVM](/powershell/module/servicemanagement/azure.service/get-azurevm) cmdlet 'ini kullanın. Daha sonra [set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) cmdlet 'ini kullanarak tanılama uzantısını IÇERECEK şekilde VM yapılandırmasını güncelleştirin. Son olarak, [Update-AzureVM](/powershell/module/servicemanagement/azure.service/update-azurevm)kullanarak GÜNCELLEŞTIRILMIŞ yapılandırmayı VM 'ye uygulayın.

```azurepowershell
$VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
$VM_Update = Set-AzureVMDiagnosticsExtension  -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM
```

## <a name="sample-diagnostics-configuration"></a>Örnek tanılama yapılandırması
Aşağıdaki XML, yukarıdaki betiklerle tanılama genel yapılandırması için kullanılabilir. Bu örnek yapılandırma, Windows olay günlüklerindeki uygulama, güvenlik ve sistem kanallarındaki hatalar ve tanılama altyapısı günlüklerinden hata ile birlikte çeşitli performans sayaçlarını tanılama depolama hesabına aktaracaktır.

Yapılandırmanın aşağıdakileri içermesi için güncelleştirilmesi gerekir:

* **Ölçümler** öğesinin *RESOURCEID* ÖZNITELIĞININ VM 'nin kaynak kimliğiyle güncelleştirilmesi gerekir.
  
  * Kaynak KIMLIĞI şu model kullanılarak oluşturulabilir: "/Subscriptions/{*VM ile abonelik için ABONELIK kimliği*}/ResourceGroups/{VM 'nin *resourcegroup Name*}/Providers/Microsoft.COMPUTE/virtualMachines/{*VM Name*}".
  * Örneğin, VM 'nin çalıştığı aboneliğin abonelik KIMLIĞI **11111111-1111-1111-1111-111111111111** ise kaynak grubunun kaynak grubu adı **myresourcegroup** olur ve VM adı **Mywindowsvm** ise, *RESOURCEID* değeri şöyle olacaktır:
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Performans sayaçları ve ölçüm yapılandırmasına göre ölçümlerin nasıl oluşturulduğu hakkında daha fazla bilgi için, bkz. [depolamada Azure tanılama ölçümleri tablosu](diagnostics-template.md#wadmetrics-tables-in-storage).
* **Storageaccount** öğesinin, tanılama depolama hesabının adıyla güncelleştirilmesi gerekir.
  
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Sonraki adımlar
* Sorunları gidermeye yönelik Azure Tanılama özelliğini ve diğer teknikleri kullanma hakkında ek yönergeler için bkz. [Azure Cloud Services ve sanal makinelerde tanılamayı etkinleştirme](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Tanılama yapılandırması şeması](../../azure-monitor/agents/diagnostics-extension-versions.md) , tanılama uzantısı IÇIN çeşitli XML yapılandırması seçeneklerini açıklar.