---
title: Bir Azure Resource Manager şablonundaki sanal makineler | Microsoft Azure
description: Sanal makine kaynağının bir Azure Resource Manager şablonunda nasıl tanımlandığı hakkında daha fazla bilgi edinin.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: e1b513344b6ea16c25d829939e64cd5ca1063c87
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838901"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Azure Resource Manager şablonundaki sanal makineler

Bu makalede, sanal makinelere uygulanan Azure Resource Manager şablonun yönleri açıklanmaktadır. Bu makale, sanal makine oluşturmaya yönelik tüm şablonları tanımlamaz; Bunun için, depolama hesapları, ağ arabirimleri, genel IP adresleri ve sanal ağlar için kaynak tanımlarına ihtiyacınız vardır. Bu kaynakların nasıl birlikte tanımlanbileceğine ilişkin daha fazla bilgi için [Kaynak Yöneticisi şablonu izlenecek yol](../../azure-resource-manager/resource-manager-template-walkthrough.md)başlığına bakın.

Galerideki VM kaynağını içeren birçok [şablon](https://azure.microsoft.com/documentation/templates/?term=VM) vardır. Bir şablonda yer alan öğelerin hepsi burada açıklanmamaktadır.

 

Bu örnek, belirtilen sayıda VM oluşturmak için bir şablonun tipik bir kaynak bölümünü gösterir:

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
        "adminUsername": "[parameters('adminUsername')]", 
        "adminPassword": "[parameters('adminPassword')]" 
      }, 
      "storageProfile": { 
        "imageReference": { 
          "publisher": "MicrosoftWindowsServer", 
          "offer": "WindowsServer", 
          "sku": "2012-R2-Datacenter", 
          "version": "latest" 
        }, 
        "osDisk": { 
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>Bu örnek, daha önce oluşturulmuş bir depolama hesabını kullanır. Şablondan dağıtarak depolama hesabı oluşturabilirsiniz. Örnek ayrıca, şablonda tanımlanacak bir ağ arabirimine ve bağımlı kaynaklarına bağımlıdır. Bu kaynaklar örnekte gösterilmez.
>
>

## <a name="api-version"></a>API sürümü

Kaynakları bir şablon kullanarak dağıttığınızda, API 'nin kullanmak için bir sürümünü belirtmeniz gerekir. Örnekte bu apiVersion öğesi kullanılarak sanal makine kaynağı gösterilmektedir:

```
"apiVersion": "2016-04-30-preview",
```

Şablonunuzda belirttiğiniz API sürümü şablonda tanımlayabileceğiniz özellikleri etkiler. Genel olarak, şablon oluştururken en son API sürümünü seçmeniz gerekir. Mevcut şablonlar için, önceki bir API sürümünü kullanmaya devam etmek istediğinize karar verebilir veya yeni özelliklerden yararlanmak için şablonunuzu en son sürüm için güncelleştirebilirsiniz.

En son API sürümlerini almak için bu fırsatları kullanın:

- REST API- [tüm kaynak sağlayıcılarını listeleme](https://docs.microsoft.com/rest/api/resources/providers)
- PowerShell- [Get-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/get-azresourceprovider)
- Azure CLı- [az Provider Show](https://docs.microsoft.com/cli/azure/provider)


## <a name="parameters-and-variables"></a>Parametreler ve değişkenler

[Parametreler](../../resource-group-authoring-templates.md) , çalıştırdığınızda şablon için değer belirtmenizi kolaylaştırır. Bu parametreler bölümü örnekte kullanılır:

```        
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Örnek şablonu dağıttığınızda, her VM 'de yönetici hesabının adı ve parolası ve oluşturulacak sanal makine sayısı için değerler girersiniz. Parametre değerlerini şablonla yönetilen ayrı bir dosyada belirtme veya istendiğinde değer sağlama seçeneğiniz vardır.

[Değişkenler](../../resource-group-authoring-templates.md) , şablonda sürekli olarak kullanılan veya zaman içinde değiştirebileceğiniz değerleri ayarlamanıza olanak kolaylaştırır. Bu değişkenler bölümü örnekte kullanılır:

```
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

Örnek şablonu dağıttığınızda, değişken değerleri daha önce oluşturulan depolama hesabının adı ve tanımlayıcısı için kullanılır. Değişkenler, tanılama uzantısının ayarlarını sağlamak için de kullanılır. Şablonunuzda parametreleri ve değişkenleri nasıl yapılandırmak istediğinize karar vermenize yardımcı olmak üzere [Azure Resource Manager şablonları oluşturmak için en iyi uygulamaları](../../resource-manager-template-best-practices.md) kullanın.

## <a name="resource-loops"></a>Kaynak döngüleri

Uygulamanız için birden fazla sanal makineye ihtiyacınız olduğunda, bir şablon içinde bir kopyalama öğesi kullanabilirsiniz. Bu isteğe bağlı öğe, bir parametre olarak belirttiğiniz VM sayısını oluşturma döngülerine sahiptir:

```
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Ayrıca, örneğin, kaynak için bazı değerleri belirtirken döngü dizininin kullanıldığını unutmayın. Örneğin, üç örnek sayısı girdiyseniz, işletim sistemi disklerinin adları myOSDisk1, myOSDisk2 ve myOSDisk3 ' dir:

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>Bu örnek, sanal makineler için yönetilen diskleri kullanır.
>
>

Şablondaki bir kaynak için bir döngü oluşturmanın, diğer kaynakları oluştururken veya erişirken döngüyü kullanmanızı gerektirebilir. Örneğin, birden çok VM aynı ağ arabirimini kullanamaz. bu nedenle, şablonunuz üç VM oluşturma yoluyla döngüleriniz, üç ağ arabirimi oluşturma aracılığıyla da döngüye alınmalıdır. Bir sanal makineye bir ağ arabirimi atarken, bu işlemi tanımlamak için döngü dizini kullanılır:

```
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Bağımlılıklar

Kaynakların çoğu, doğru şekilde çalışmak için diğer kaynaklara bağımlıdır. Sanal makinelerin bir sanal ağla ilişkilendirilmesi ve bir ağ arabirimine ihtiyacı olması gerekir. [Bağımlıdson](../../resource-group-define-dependencies.md) öğesi, ağ arabiriminin VM 'ler oluşturulmadan önce kullanılabilir olduğundan emin olmak için kullanılır:

```
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Kaynak Yöneticisi, dağıtılmakta olan başka bir kaynağa bağımlı olmayan herhangi bir kaynak paralel olarak dağıtılır. Gereksiz bağımlılıkları belirterek dağıtımınızı yanlışlıkla yavaşlatabileceğinden, bağımlılıkları ayarlarken dikkatli olun. Bağımlılıklar birden çok kaynak arasında zincir oluşturabilir. Örneğin, ağ arabirimi genel IP adresine ve sanal ağ kaynaklarına bağlıdır.

Bağımlılığın gerekli olup olmadığını nasıl anlarsınız? Şablonda ayarladığınız değerlere bakın. Sanal makine kaynak tanımındaki bir öğe aynı şablonda dağıtılan başka bir kaynağı gösteriyorsa, bir bağımlılığa ihtiyacınız vardır. Örneğin, örnek sanal makineniz bir ağ profili tanımlar:

```
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Bu özelliği ayarlamak için, ağ arabiriminin mevcut olması gerekir. Bu nedenle, bir bağımlılığa ihtiyacınız vardır. Ayrıca, bir kaynak (alt öğe) başka bir kaynak (üst öğe) içinde tanımlandığında bir bağımlılık ayarlamanız gerekir. Örneğin, Tanılama ayarları ve özel Betik uzantıları, sanal makinenin alt kaynakları olarak tanımlanır. Sanal makine mevcut olana kadar oluşturuamazlar. Bu nedenle, her iki kaynak de sanal makineye bağımlı olarak işaretlenir.

## <a name="profiles"></a>Profiller

Bir sanal makine kaynağı tanımlarken çeşitli profil öğeleri kullanılır. Bazıları gereklidir ve bazıları isteğe bağlıdır. Örneğin, hardwareProfile, osProfile, storageProfile ve networkProfile öğeleri gereklidir, ancak diagnosticsProfile isteğe bağlıdır. Bu profiller gibi ayarları tanımlar:
   
- [boyutla](sizes.md)
- [ad](/azure/architecture/best-practices/resource-naming) ve kimlik bilgileri
- disk ve [işletim sistemi ayarları](cli-ps-findimage.md)
- [Ağ arabirimi](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- önyükleme tanılaması

## <a name="disks-and-images"></a>Diskler ve görüntüler
   
Azure 'da, VHD dosyaları [diskleri veya görüntüleri](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)temsil edebilir. Bir VHD dosyasındaki işletim sistemi belirli bir sanal makine olmak üzere özelleştirildiğinde, disk olarak adlandırılır. Bir VHD dosyasındaki işletim sistemi çok sayıda VM oluşturmak için genelleştirildiğinde, görüntü olarak adlandırılır.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Yeni sanal makineler ve bir platform görüntüsünden yeni diskler oluşturma

Bir VM oluşturduğunuzda, hangi işletim sisteminin kullanılması gerektiğine karar vermelisiniz. ImageReference öğesi, yeni bir sanal makinenin işletim sistemini tanımlamak için kullanılır. Örnek, bir Windows Server işletim sistemi için bir tanım gösterir:

```
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Bir Linux işletim sistemi oluşturmak istiyorsanız bu tanımı kullanabilirsiniz:

```
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

İşletim sistemi diski için yapılandırma ayarları osDisk öğesiyle atanır. Örnek, önbelleğe alma modu, **ReadWrite** olarak ayarlanan ve diskin bir [Platform görüntüsünden](cli-ps-findimage.md)oluşturulduğu yeni bir yönetilen disk tanımlar:

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Mevcut yönetilen disklerden yeni sanal makineler oluşturma

Mevcut disklerden sanal makineler oluşturmak istiyorsanız, ImageReference ve osProfile öğelerini kaldırın ve şu disk ayarlarını tanımlayın:

```
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Yönetilen görüntüden yeni sanal makineler oluşturma

Yönetilen görüntüden bir sanal makine oluşturmak istiyorsanız, ImageReference öğesini değiştirin ve bu disk ayarlarını tanımlayın:

```
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>Veri disklerini iliştirme

İsteğe bağlı olarak VM 'lere veri diskleri ekleyebilirsiniz. [Disk sayısı](sizes.md) , kullandığınız işletim sistemi diskinin boyutuna bağlıdır. VM 'lerin boyutu Standard_DS1_v2 olarak ayarlandığında, bunlara eklenebilecek en fazla veri diski sayısı ikdir. Örnekte, her VM 'ye bir yönetilen veri diski ekleniyor:

```
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>Uzantılar

[Uzantılar](extensions-features.md) ayrı bir kaynak olmakla birlikte, sanal makinelere yakın bir şekilde bağlanır. Uzantılar, VM 'nin alt kaynağı veya ayrı bir kaynak olarak eklenebilir. Örnek, VM 'lere eklenen [Tanılama uzantısını](extensions-diagnostics-template.md) gösterir:

```
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

Bu uzantı kaynağı, değer sağlamak için storageName değişkenini ve tanılama değişkenlerini kullanır. Bu uzantı tarafından toplanan verileri değiştirmek istiyorsanız, wadperfcounters değişkenine daha fazla performans sayacı ekleyebilirsiniz. Ayrıca, tanılama verilerini VM disklerinin depolandığı yerden farklı bir depolama hesabına koymaya de seçim yapabilirsiniz.

Bir VM 'ye yükleyebileceğiniz birçok uzantı vardır, ancak en iyi yöntem büyük olasılıkla [Özel Betik uzantısıdır](extensions-customscript.md). Örnekte, Start. ps1 adlı bir PowerShell betiği, ilk başladığında her bir VM üzerinde çalışır:

```
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

Start. ps1 betiği birçok yapılandırma görevini gerçekleştirebilir. Örneğin, örnekteki sanal makinelere eklenen veri diskleri başlatılmaz; bunları başlatmak için özel bir komut dosyası kullanabilirsiniz. Yapılacak birden çok başlangıç göreviniz varsa, Azure Storage 'daki diğer PowerShell betiklerini çağırmak için Start. ps1 dosyasını kullanabilirsiniz. Örnek PowerShell kullanır, ancak kullanmakta olduğunuz işletim sisteminde bulunan herhangi bir betik yöntemini kullanabilirsiniz.

Yüklü uzantıların durumunu portalda uzantılar ayarlarından görebilirsiniz:

![Uzantı durumunu al](./media/template-description/virtual-machines-show-extensions.png)

Ayrıca, **Get-Azvmexgerpowershell** komutunu, **VM Uzantısı** Azure CLI 'yı Al komutunu veya REST API **uzantısını al bilgilerini** kullanarak uzantı bilgileri alabilirsiniz.

## <a name="deployments"></a>Dağıtımlar

Bir şablon dağıttığınızda, Azure bir grup olarak dağıttığınız kaynakları izler ve bu dağıtılan gruba otomatik olarak bir ad atar. Dağıtımın adı, şablonun adı ile aynıdır.

Dağıtımdaki kaynakların durumunu merak ediyorsanız, Azure portal kaynak grubunu görüntüleyin:

![Dağıtım bilgilerini al](./media/template-description/virtual-machines-deployment-info.png)
    
Kaynak oluşturmak veya mevcut kaynakları güncelleştirmek için aynı şablonu kullanma sorunu değildir. Şablonları dağıtmak için komutlar kullandığınızda, hangi [modu](../../resource-group-template-deploy.md) kullanmak istediğinizi söylemeniz gerekir. Mod **tamamen** veya **artımlı**olarak ayarlanabilir. Varsayılan değer artımlı güncelleştirmeler gerçekleştirmemesidir. Kaynakları yanlışlıkla silebileceğinden, **Tüm** modunu kullanırken dikkatli olun. Modu, **tamamlanacak**şekilde ayarladığınızda, Kaynak Yöneticisi şablonda olmayan kaynak grubundaki tüm kaynakları siler.

## <a name="next-steps"></a>Sonraki Adımlar

- [Azure Resource Manager şablonları yazma](../../resource-group-authoring-templates.md)kullanarak kendi şablonunuzu oluşturun.
- Bir [Kaynak Yöneticisi şablonuyla Windows sanal makinesi oluşturma](ps-template.md)kullanarak oluşturduğunuz şablonu dağıtın.
- [Azure PowerShell modülü Ile Windows VM 'Leri oluşturma ve yönetme](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)konusunu Inceleyerek oluşturduğunuz VM 'leri yönetmeyi öğrenin.
- Şablonlarda kaynak türlerinin JSON sözdizimi ve özellikleri için bkz. [Azure Resource Manager şablonu başvurusu](/azure/templates/).
