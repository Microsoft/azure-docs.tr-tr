---
title: Azure sanal makinesine izleme & tanılaması ekleme
description: Azure tanılama Uzantısı ile yeni bir Windows sanal makinesi oluşturmak için bir Azure Resource Manager şablonu kullanın.
services: virtual-machines-windows
documentationcenter: ''
author: sbtron
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2490c3de60e0deac6a1a4ddc5abc95cb46e240b2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073837"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Windows VM ve Azure Resource Manager şablonlarıyla izleme ve tanılama kullanma
Azure Tanılama uzantısı, Windows tabanlı bir Azure sanal makinesinde izleme ve tanılama özellikleri sağlar. Uzantıyı Azure Resource Manager şablonun bir parçası olarak ekleyerek sanal makinede bu özellikleri etkinleştirebilirsiniz. Bir sanal makine şablonunun parçası olarak herhangi bir uzantı ekleme hakkında daha fazla bilgi için bkz. [VM uzantılarına sahip Azure Resource Manager şablonları yazma](../windows/template-description.md#extensions) . Bu makalede, Azure Tanılama uzantısını bir Windows sanal makine şablonuna nasıl ekleyebileceğiniz açıklanır.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Azure Tanılama uzantısını VM kaynak tanımına ekleyin
Windows sanal makinesinde tanılama uzantısını etkinleştirmek için uzantıyı Kaynak Yöneticisi şablonuna bir VM kaynağı olarak eklemeniz gerekir.

Basit Kaynak Yöneticisi tabanlı bir sanal makine için uzantı yapılandırmasını sanal makinenin *kaynaklar* dizisine ekleyin: 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "tags": {
            "displayName": "AzureDiagnostics"
        },
        "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

Diğer bir yaygın kural, uzantı yapılandırmasını, sanal makinenin kaynaklar düğümü altında tanımlamak yerine şablonun kök kaynakları düğümüne eklemektir. Bu yaklaşımda, uzantı ile sanal makine arasında *ad* ve *tür* değerleriyle açık bir şekilde hiyerarşik ilişki belirtmeniz gerekir. Örneğin: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

Uzantı her zaman sanal makineyle ilişkilendirilir; doğrudan sanal makinenin kaynak düğümü altında doğrudan tanımlayabilir veya temel düzeyde tanımlayabilir ve hiyerarşik adlandırma kuralını kullanarak sanal makineyle ilişkilendirebilirsiniz.

Sanal Makine Ölçek Kümeleri için, uzantı yapılandırması *Virtualmachineprofile*'In *extensionprofile* özelliğinde belirtilmiştir.

**Microsoft. Azure. Diagnostics** değeri ve **ıaasdiagnostics** değeri ile *type* özelliği olan *Publisher* özelliği Azure tanılama uzantısını benzersiz şekilde tanımlar.

*Ad* özelliğinin değeri, kaynak grubundaki uzantıya başvurmak için kullanılabilir. Bunu özellikle **Microsoft. Insights. VMDiagnosticsSettings** olarak ayarlamak, izleme grafiklerinin Azure Portal doğru görünmesini sağlamak için Azure Portal tarafından kolayca tanımlanmasını sağlar.

*Typehandlerversion* , kullanmak istediğiniz uzantının sürümünü belirtir. *Oto Upgrademinorversion* alt sürümünün **true** olarak ayarlanması, mevcut uzantının en son ikincil sürümünü almanızı sağlar. Her zaman yeni özellikler ve hata düzeltmeleriyle birlikte kullanılabilir en son tanılama uzantısını kullanmak için her zaman her zaman **true** *olarak ayarlamanız önerilir* . 

*Settings* öğesi, uzantıya (bazen genel yapılandırma olarak adlandırılır) ayarlanabilir ve geri okuyabilen yapılandırma özelliklerini içerir. *Xmlcfg* özelliği, tanılama Aracısı tarafından toplanan tanılama günlükleri, performans sayaçları ve XML tabanlı yapılandırmayı içerir. XML şemasının kendisi hakkında daha fazla bilgi için bkz. [Tanılama yapılandırma şeması](https://msdn.microsoft.com/library/azure/dn782207.aspx) . Ortak bir uygulama, gerçek xml yapılandırmasını Azure Resource Manager şablonunda bir değişken olarak depolayıp, sonra *xmlcfg*için değer ayarlamak üzere bunları birleştirir ve Base64 olarak kodlayayöneliktir. XML 'i değişkenlerde nasıl depolayacağınızı öğrenmek için [Tanılama yapılandırma değişkenleri](#diagnostics-configuration-variables) bölümüne bakın. *Storageaccount* özelliği, tanılama verilerinin aktarıldığı depolama hesabının adını belirtir. 

*Protectedsettings* içindeki Özellikler (bazen özel yapılandırma olarak adlandırılır) ayarlanabilir, ancak ayarlandıktan sonra geri okunamaz. *Protectedsettings* 'in salt yazılır olması, tanılama verilerinin yazıldığı depolama hesabı anahtarı gibi gizli dizileri depolamanın yararlı olmasını sağlar.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Tanılama depolama hesabını parametre olarak belirtme
Yukarıdaki tanılama uzantısı JSON parçacığı, tanılama verilerinin depolandığı tanılama depolama hesabını belirtmek için iki parametrenin *Existingdiagnosticsstorageaccountname* ve *Existingdiagnosticsstorageresourcegroup* olduğunu varsayar. Tanılama depolama hesabının bir parametre olarak belirtilmesi, farklı ortamlarda tanılama depolama hesabını değiştirmeyi kolaylaştırır. Örneğin, test için farklı bir tanılama depolama hesabı ve Üretim dağıtımı.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
    }
}
```

Sanal makine için kaynak grubundan farklı bir kaynak grubunda bir tanılama depolama hesabı belirtmek en iyi uygulamadır. Bir kaynak grubu, kendi yaşam süresine sahip bir dağıtım birimi olarak kabul edilebilir, yeni yapılandırma güncelleştirmeleri kendisine yaptığı için bir sanal makine dağıtılabilir ve yeniden dağıtılabilir, ancak tanılama verilerini aynı depolama hesabında depolamaya devam etmek isteyebilirsiniz. Bu sanal makine dağıtımları. Depolama hesabının farklı bir kaynakta olması, depolama hesabının çeşitli sanal makine dağıtımlarındaki verileri kabul etmesine olanak sağlayarak çeşitli sürümlerde sorunları gidermeyi kolaylaştırır.

> [!NOTE]
> Visual Studio 'dan bir Windows sanal makine şablonu oluşturursanız, varsayılan depolama hesabı, sanal makine VHD 'sinin karşıya yüklendiği aynı depolama hesabını kullanacak şekilde ayarlanmış olabilir. Bu, VM 'nin ilk kurulumunu basitleştirecek. Şablonu bir parametre olarak geçirilebilecek farklı bir depolama hesabı kullanmak için yeniden çarpanlara koyun. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Tanılama yapılandırma değişkenleri
Önceki tanılama uzantısı JSON parçacığı, tanılama depolaması için depolama hesabı anahtarını almayı basitleştirmek üzere bir *AccountID* değişkeni tanımlar:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

Tanılama uzantısının *xmlcfg* özelliği, birlikte birleştirilmiş birden çok değişken kullanılarak tanımlanır. Bu değişkenlerin değerleri XML biçiminde olduğundan, JSON değişkenlerini ayarlarken doğru bir şekilde atlanmaları gerekir.

Aşağıdaki örnekte, bazı Windows olay günlükleri ve tanılama altyapı günlükleri ile birlikte standart sistem düzeyi performans sayaçlarını toplayan tanılama yapılandırması XML açıklanmaktadır. Yapılandırmanın doğrudan şablonunuzun değişkenler bölümüne yapıştırılabilmesi için, bu, atmıştır ve doğru biçimlendirilmiştir. Yapılandırma XML dosyası için daha okunabilir bir örnek için [Tanılama yapılandırması şemasına](https://msdn.microsoft.com/library/azure/dn782207.aspx) bakın.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

Yukarıdaki yapılandırmadaki ölçüm tanımı XML düğümü, *PerformanceCounter* düğümündeki XML 'de daha önce tanımlanan performans sayaçlarının nasıl toplandığını ve depolandığını tanımladığından önemli bir yapılandırma öğesidir. 

> [!IMPORTANT]
> Bu ölçümler, Azure portal izleme grafiklerini ve uyarılarını sürücü halinde izler.  Azure portal VM izleme verilerini görmek istiyorsanız, *RESOURCEID* ve **metrictoplamasını** içeren **ölçümler** düğümünün VM 'niz için tanılama yapılandırmasına dahil olması gerekir. 
> 
> 

Aşağıdaki örnek, ölçüm tanımlarının XML 'sini göstermektedir: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

*RESOURCEID* özniteliği, aboneliğinizdeki sanal makineyi benzersiz şekilde tanımlar. Şablon, dağıttığınız abonelik ve kaynak grubuna göre bu değerleri otomatik olarak güncelleyen abonelik () ve resourceGroup () işlevlerini kullandığınızdan emin olun.

Bir döngüde birden çok sanal makine oluşturuyorsanız, her bir sanal MAKINEYI doğru şekilde ayırt etmek için *RESOURCEID* değerini bir copyındex () işleviyle doldurmanız gerekir. *Xmlcfg* değeri bunu aşağıdaki gibi destekleyecek şekilde yapılandırılabilir:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

*PT1M* ve *PT1H* ' nin metrictoplamasını değeri, sırasıyla bir dakika ve bir toplama üzerinde bir toplama olduğunu işaret eder.

## <a name="wadmetrics-tables-in-storage"></a>Depolama alanındaki Wadölçümler tabloları
Yukarıdaki ölçüm yapılandırması, tanılama depolama hesabınızda aşağıdaki adlandırma kurallarıyla tablo oluşturur:

* **Wadölçümler**: tüm wadmetrik tabloları için Standart önek
* **PT1H** veya **PT1M**: tablonun 1 saat veya 1 dakikalık toplam verileri içerdiğini belirtir
* **P10D**: tablonun veri toplamaya başladığı zaman 10 günlük verileri içereceği anlamına gelir
* **V2S**: dize sabiti
* **YYYYMMDD**: tablonun veri toplamaya başladığı tarih

Örnek: *WADMetricsPT1HP10DV2S20151108* 11-kas-2015 tarihinden itibaren 10 gün boyunca toplanan ölçüm verilerini içerir    

Her Wadölçümler tablosu şu sütunları içerir:

* **Partitionkey**: bölüm anahtarı, VM kaynağını benzersiz şekilde tanımlamak için *RESOURCEID* değeri temel alınarak oluşturulur. Örneğin, `002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **Rowkey**: `<Descending time tick>:<Performance Counter Name>`biçimini izler. Azalan zaman değeri hesaplaması, en fazla zaman aralığı toplama döneminin başlangıcının süresinin çıkarılmasıyla elde edilir. Örneğin, 10-Kas-2015 ve 00:00Saat UTC 'de örnek dönem başlatılırsa, hesaplama şöyle olacaktır: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. Kullanılabilir bellek baytları performans sayacı için satır anahtarı şöyle görünür: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: performans sayacının adıdır. Bu, XML yapılandırmasında tanımlanan *sayaç belirticisiyle* eşleşir.
* **Maksimum**: toplama dönemi boyunca performans sayacının maksimum değeri.
* **Minimum**: toplama dönemi boyunca performans sayacının minimum değeri.
* **Toplam**: toplama dönemi boyunca raporlanan performans sayacının tüm değerlerinin toplamı.
* **Sayı**: performans sayacı için raporlanan toplam değer sayısı.
* **Average**: toplama dönemi boyunca performans sayacının ortalama (Toplam/sayı) değeri.

## <a name="next-steps"></a>Sonraki Adımlar
* Tanılama uzantısına sahip bir Windows sanal makinesinin tam örnek şablonu için bkz. [201-VM-Monitoring-Diagnostics-Extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* [Azure PowerShell](../windows/ps-template.md) veya [Azure komut satırı](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kullanarak Azure Resource Manager şablonu dağıtma
* [Azure Resource Manager şablonları yazma](../../resource-group-authoring-templates.md) hakkında daha fazla bilgi edinin
