---
title: Azure Resource Manager şablonlarıyla disk dağıtma
description: Azure VM 'Leri için Azure Resource Manager şablonlarda yönetilen ve yönetilmeyen disklerin nasıl kullanılacağına ilişkin ayrıntılar.
documentationcenter: ''
author: jboeshart
manager: ''
ms.service: virtual-machines
ms.topic: how-to
ms.workload: storage
ms.date: 06/01/2017
ms.author: jaboes
ms.subservice: disks
ms.openlocfilehash: 7c66a8b8483673a9d8fbdc9922b9cc377781bab3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91976680"
---
# <a name="using-disks-in-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarda diskler kullanma

Bu belge, sanal makineler sağlamak için Azure Resource Manager şablonları kullanırken yönetilen ve yönetilmeyen diskler arasındaki farklılıkları gösterir. Örnekler, yönetilmeyen diskleri kullanan mevcut şablonları yönetilen disklere güncelleştirmenize yardımcı olur. Başvuru için, bir kılavuz olarak [101-VM-Simple-Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) şablonunu kullanıyoruz. Hem [yönetilen diskleri](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) hem de doğrudan karşılaştırmak istiyorsanız [yönetilmeyen diskleri](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) kullanarak bir önceki sürümü kullanarak şablonu görebilirsiniz.

## <a name="unmanaged-disks-template-formatting"></a>Yönetilmeyen diskler şablon biçimlendirmesi

Başlamak için, yönetilmeyen disklerin nasıl dağıtıldığına göz atalım. Yönetilmeyen diskler oluştururken, VHD dosyalarını tutmak için bir depolama hesabı gerekir. Yeni bir depolama hesabı oluşturabilir veya zaten var olan bir depolama hesabı kullanabilirsiniz. Bu makalede, yeni bir depolama hesabının nasıl oluşturulacağı gösterilmektedir. Kaynak bloğunda aşağıda gösterildiği gibi bir depolama hesabı kaynağı oluşturun.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

Sanal makine nesnesi içinde, sanal makineden önce oluşturulduğundan emin olmak için depolama hesabına bir bağımlılık ekleyin. Bölümünde, `storageProfile` depolama hesabına başvuran ve işletim sistemi diski ve veri diskleri için gerekli olan VHD konumunun tam URI 'sini belirtin.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Yönetilen diskler şablon biçimlendirmesi

Azure yönetilen diskler ile disk, üst düzey bir kaynak haline gelir ve artık Kullanıcı tarafından oluşturulacak bir depolama hesabı gerektirmez. Yönetilen diskler ilk olarak `2016-04-30-preview` API sürümünde kullanıma sunulmuştur, sonraki tüm API sürümlerinde kullanılabilir ve artık varsayılan disk türüdür. Aşağıdaki bölümler, varsayılan ayarları ve disklerinizi daha fazla özelleştirmeye ilişkin ayrıntıları gösterir.

> [!NOTE]
> Ve arasında önemli değişiklikler olduğundan daha sonra API sürümünün kullanılması önerilir `2016-04-30-preview` `2016-04-30-preview` `2017-03-30` .
>
>

### <a name="default-managed-disk-settings"></a>Varsayılan yönetilen disk ayarları

Yönetilen disklerle bir VM oluşturmak için artık depolama hesabı kaynağını oluşturmanız gerekmez. Aşağıdaki şablon örneğine başvurarak, önceki yönetilmeyen disk örneklerinden bazı farklılıklar göz önünde bulunur:

- , `apiVersion` Yönetilen diskleri destekleyen bir sürümdür.
- `osDisk` ve `dataDisks` artık VHD için belirli BIR URI 'ye başvurmaz.
- Ek özellikler belirtmeden dağıtım yaparken, disk, sanal makinenin boyutuna bağlı olarak bir depolama türü kullanır. Örneğin, Premium depolamayı destekleyen bir VM boyutu kullanıyorsanız (Standard_D2s_v3 gibi adında "s" ile boyutlar), Premium diskler varsayılan olarak yapılandırılır. Bunu, bir depolama türü belirtmek için diskin SKU ayarını kullanarak değiştirebilirsiniz.
- Disk için bir ad belirtilmemişse, `<VMName>_OsDisk_1_<randomstring>` işletim sistemi diski ve her bir veri diski için biçimini alır `<VMName>_disk<#>_<randomstring>` .
  - Bir VM özel görüntüden oluşturulduysa, depolama hesabı türü ve disk adı için varsayılan ayarlar, özel görüntü kaynağında tanımlanan disk özelliklerinden alınır. Bunlar şablonda bunlar için değerler belirtilerek geçersiz kılınabilir.
- Azure disk şifrelemesi varsayılan olarak devre dışıdır.
- Varsayılan olarak, disk önbelleğe alma, işletim sistemi diski için okuma/yazma ve veri diskleri için yok.
- Aşağıdaki örnekte, hala bir depolama hesabı bağımlılığı vardır, ancak bu yalnızca tanılama depolaması içindir ve disk depolaması için gerekli değildir.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Üst düzey yönetilen disk kaynağı kullanma

Sanal makine nesnesinde disk yapılandırmasını belirtmeye alternatif olarak, üst düzey bir disk kaynağı oluşturabilir ve bunu sanal makine oluşturmanın bir parçası olarak iliştirebilirsiniz. Örneğin, bir disk kaynağını bir veri diski olarak kullanmak için aşağıdaki gibi oluşturabilirsiniz.

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

VM nesnesi içinde, eklenecek disk nesnesine başvurun. Özellikte oluşturulan yönetilen diskin kaynak KIMLIĞINI belirtmek, `managedDisk` sanal makine oluşturulurken diskin ekine izin verir. `apiVersion`VM kaynağı için olarak ayarlanır `2017-03-30` . VM oluşturmadan önce başarıyla oluşturulduğundan emin olmak için disk kaynağına bir bağımlılık eklenir. 

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Yönetilen diskleri kullanarak VM 'lerle yönetilen kullanılabilirlik kümeleri oluşturma

Yönetilen diskleri kullanarak VM 'lerle yönetilen kullanılabilirlik kümeleri oluşturmak için, `sku` nesneyi kullanılabilirlik kümesi kaynağına ekleyin ve `name` özelliğini olarak ayarlayın `Aligned` . Bu özellik, tek hata noktalarından kaçınmak için her VM için disklerin birbirinden yeterince yalıtılmış olmasını sağlar. Ayrıca, `apiVersion` kullanılabilirlik kümesi kaynağının için olarak ayarlandığını unutmayın `2018-10-01` .

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>Standart SSD diskler

Standart SSD disk oluşturmak için Kaynak Yöneticisi şablonunda gereken parametreler şunlardır:

* Microsoft. COMPUTE için *Apiversion* , `2018-04-01` (veya üzeri) olarak ayarlanmalıdır
* *Manageddisk. storageAccountType* olarak belirtin`StandardSSD_LRS`

Aşağıdaki örnek, Standart SSD diskleri kullanan bir VM için *Properties. storageProfile. osDisk* bölümünü gösterir:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Şablon ile Standart SSD disk oluşturma hakkında ayrıntılı bir örnek için, bkz. [Standart SSD Veri disklerine sahip bir Windows GÖRÜNTÜSÜNDEN VM oluşturma](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>Ek senaryolar ve özelleştirmeler

REST API belirtimleriyle ilgili tam bilgileri bulmak için lütfen [yönetilen disk oluşturma REST API belgelerini](/rest/api/manageddisks/disks/disks-create-or-update)gözden geçirin. Ek senaryolar ve ayrıca, şablon dağıtımları aracılığıyla API 'ye gönderilebileceğiniz varsayılan ve kabul edilebilir değerler bulacaksınız. 

## <a name="next-steps"></a>Sonraki adımlar

* Yönetilen diskler kullanan tam şablonlar için aşağıdaki Azure hızlı başlangıç deposu bağlantılarını ziyaret edin.
    * [Yönetilen disk ile Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Yönetilen disk ile Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* Yönetilen diskler hakkında daha fazla bilgi edinmek için [Azure yönetilen diskler genel bakış](managed-disks-overview.md) belgesini ziyaret edin.
* [Microsoft. COMPUTE/virtualMachines şablon başvuru](/azure/templates/microsoft.compute/virtualmachines) belgesini ziyaret ederek, sanal makine kaynakları için şablon başvuru belgelerini gözden geçirin.
* [Microsoft. COMPUTE/Disks şablon başvuru](/azure/templates/microsoft.compute/disks) belgesini ziyaret ederek disk kaynakları için şablon başvuru belgelerini gözden geçirin.
* Azure sanal makine ölçek kümelerinde yönetilen diskleri kullanma hakkında daha fazla bilgi için [Ölçek kümeleri ile veri disklerini kullanma](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md) belgesini ziyaret edin.