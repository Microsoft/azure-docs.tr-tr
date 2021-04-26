---
title: Sanal Makine Ölçek Kümesi şablonları hakkında bilgi edinin
description: Birkaç basit adım aracılığıyla Azure sanal makine ölçek kümeleri için temel ölçek kümesi şablonu oluşturmayı öğrenin.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.date: 04/26/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: c759cf564654990149424acf5aa3efaea4b37be0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933449"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Sanal Makine Ölçek Kümesi şablonları hakkında bilgi edinin
[Azure Resource Manager şablonları](../azure-resource-manager/templates/overview.md#template-deployment-process), ilgili kaynak gruplarını dağıtmanın harika bir yoludur. Bu öğretici serisinde, temel bir ölçek kümesi şablonunun nasıl oluşturulduğu ve bu şablonun çeşitli senaryolara uyacak şekilde nasıl değiştirileceği gösterilmektedir. Tüm örnekler bu [GitHub deposundan](https://github.com/gatneil/mvss)gelir.

Bu şablonun basit olması amaçlanmıştır. Ölçek kümesi şablonlarının daha kapsamlı örnekleri için bkz. [Azure hızlı başlangıç şablonları GitHub deposu](https://github.com/Azure/azure-quickstart-templates) ve dizeyi içeren klasörleri arama `vmss` .

Şablon oluşturma konusunda zaten bilgi sahibiyseniz, bu şablonu nasıl değiştireceğiniz hakkında daha fazla bilgi için "sonraki adımlar" bölümüne geçebilirsiniz.

## <a name="define-schema-and-contentversion"></a>$schema ve contentVersion tanımlayın
İlk olarak, `$schema` `contentVersion` şablonda ve içinde tanımlayın. `$schema`Öğesi, şablon dilinin sürümünü tanımlar ve Visual Studio sözdizimi vurgulama ve benzer doğrulama özellikleri için kullanılır. `contentVersion`Öğesi Azure tarafından kullanılmıyor. Bunun yerine, şablon sürümünü izlemenize yardımcı olur.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>Parametreleri tanımlama
Sonra, iki parametre tanımlayın `adminUsername` ve `adminPassword` . Parametreler, dağıtım sırasında belirttiğiniz değerlerdir. `adminUsername`Parametresi yalnızca bir `string` türdür, ancak `adminPassword` bir gizli dizi olduğundan, bu türe `securestring` sahip olur. Daha sonra, bu parametreler ölçek kümesi yapılandırmasına geçirilir.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Değişkenleri tanımlama
Kaynak Yöneticisi şablonlar, daha sonra şablonda kullanılacak değişkenleri tanımlamanızı da sağlar. Örnek herhangi bir değişken kullanmaz, bu nedenle JSON nesnesi boş olur.

```json
  "variables": {},
```

## <a name="define-resources"></a>Kaynakları tanımlama
Bundan sonraki, şablonun kaynaklar bölümüdür. Burada, gerçekten ne dağıtmak istediğinizi tanımlarsınız. `parameters`Ve `variables` (JSON nesneleri olan) aksine, JSON `resources` nesnelerinin JSON listesidir.

```json
   "resources": [
```

Tüm kaynaklar `type` ,, `name` `apiVersion` ve özellikleri gerektirir `location` . Bu örneğin ilk kaynağında [Microsoft. Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks), Name `myVnet` ve apiversion türü vardır `2018-11-01` . (Bir kaynak türü için en son API sürümünü bulmak için, [Azure Resource Manager şablonu başvurusuna](/azure/templates/)bakın.)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>Konum belirt
Sanal ağın konumunu belirtmek için bir [Kaynak Yöneticisi Şablon işlevi](../azure-resource-manager/templates/template-functions.md)kullanın. Bu işlev tırnak içine alınmış ve köşeli ayraçlar şöyle olmalıdır: `"[<template-function>]"` . Bu durumda, `resourceGroup` işlevini kullanın. Bağımsız değişken içermez ve bu dağıtımın dağıtıldığı kaynak grubu hakkında meta veriler içeren bir JSON nesnesi döndürür. Kaynak grubu, dağıtım sırasında Kullanıcı tarafından ayarlanır. Bu değer, `.location` JSON nesnesinden konumunu almak için ile bu JSON nesnesine dizinlenir.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Sanal ağ özelliklerini belirtin
Her bir Kaynak Yöneticisi kaynağının, `properties` kaynağa özgü yapılandırmalara yönelik kendi bölümü vardır. Bu durumda, sanal ağın özel IP adresi aralığını kullanan bir alt ağa sahip olması gerektiğini belirtin `10.0.0.0/16` . Ölçek kümesi her zaman bir alt ağ içinde yer alır. Alt ağlara yayılamaz.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>Bağımlıdson listesi ekle
Gerekli `type` , `name` , `apiVersion` , ve özelliklerine ek olarak `location` , her kaynak isteğe bağlı dizeler listesine sahip olabilir `dependsOn` . Bu liste, bu kaynağı dağıtılmadan önce bu dağıtımın hangi diğer kaynakların bitmesini gerektiğini belirtir.

Bu durumda, listede bir önceki örnekteki sanal ağ olmak üzere yalnızca bir öğe vardır. Ölçek kümesi herhangi bir VM oluşturmadan önce ağın mevcut olması gerektiğinden bu bağımlılığı belirtirsiniz. Bu şekilde, ölçek kümesi bu VM 'Lere daha önce ağ özelliklerinde belirtilen IP adres aralığından özel IP adresleri verebilir. Bağımlıdson listesindeki her bir dizenin biçimi `<type>/<name>` . `type` `name` Daha önce sanal ağ kaynak tanımında kullanılan ve aynısını kullanın.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2019-03-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Ölçek kümesi özelliklerini belirtin
Ölçek kümelerinin ölçek kümesindeki VM 'Leri özelleştirmek için birçok özelliği vardır. Bu özelliklerin tam listesi için [şablon başvurusuna](/azure/templates/microsoft.compute/virtualmachinescalesets)bakın. Bu öğretici için, yalnızca birkaç yaygın kullanılan özellik ayarlanır.
### <a name="supply-vm-size-and-capacity"></a>VM boyut ve kapasitesini belirtin
Ölçek kümesinin oluşturulacak sanal makine boyutunu ("SKU adı") ve kaç tane sanal makine oluşturulacağını ("SKU kapasitesi") bilmeleri gerekir. Hangi VM boyutlarının kullanılabilir olduğunu görmek için, [VM boyutları belgelerine](../virtual-machines/sizes.md)bakın.

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Güncelleştirme türünü seçin
Ölçek kümesinin Ayrıca ölçek kümesinde güncelleştirmelerin nasıl işleneceğini bilmeleri gerekir. Şu anda üç seçenek vardır, `Manual` `Rolling` ve `Automatic` . İkisi arasındaki farklılıklar hakkında daha fazla bilgi için bkz. [Ölçek kümesini yükseltme](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)hakkındaki belgeler.

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>VM işletim sistemi seçin
Ölçek kümesinin VM 'Lere hangi işletim sisteminin yerleştirileceğini bilmeleri gerekir. Burada, tam düzeltme eki uygulanan Ubuntu 16,04-LTS görüntüsüne sahip VM 'Ler oluşturun.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>ComputerNamePrefix belirtin
Ölçek kümesi birden çok VM dağıtır. Her VM adını belirtmek yerine belirtin `computerNamePrefix` . Ölçek kümesi, her VM için bir dizin ekler ve bu nedenle VM adlarının biçimi vardır `<computerNamePrefix>_<auto-generated-index>` .

Aşağıdaki kod parçacığında, ölçek kümesindeki tüm VM 'Ler için Yönetici Kullanıcı adı ve parolasını ayarlamak üzere önceki parametreleri kullanın. Bu işlem, `parameters` şablon işlevini kullanır. Bu işlev, hangi parametrenin başvurabileceği ve bu parametrenin değerini çıkaran bir dizeyi alır.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>VM ağ yapılandırmasını belirtin
Son olarak, ölçek kümesindeki VM 'Ler için ağ yapılandırmasını belirtin. Bu durumda, yalnızca daha önce oluşturulan alt ağın KIMLIĞINI belirtmeniz gerekir. Bu, ölçek kümesine ağ arabirimlerini bu alt ağa yerleştirmesine söyler.

Şablon işlevini kullanarak alt ağı içeren sanal ağın KIMLIĞINI alabilirsiniz `resourceId` . Bu işlev, bir kaynağın türünü ve adını alır ve bu kaynağın tam nitelikli tanımlayıcısını döndürür. Bu KIMLIK şu biçimdedir: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Ancak, sanal ağın tanımlayıcısı yeterli değildir. Ölçek kümesi VM 'lerinin içinde olması gereken belirli bir alt ağı sağlayın. Bunu yapmak için, `/subnets/mySubnet` sanal AĞıN kimliğine bir araya geçin. Sonuç, alt ağın tam KIMLIĞIDIR. Bu birleştirme `concat` işlevini, bir dizi dizeyi alan ve birlikte birleştirmesini döndüren işlevle yapın.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]