---
title: Bıcep modülleri
description: Modülün nasıl tanımlanacağını ve kullanılacağını ve modül kapsamlarının nasıl kullanılacağını açıklar.
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: 6c325bbbe265e13241119761373985ca4552b158
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967889"
---
# <a name="use-bicep-modules-preview"></a>Bıcep modüllerini kullanma (Önizleme)

Bıcep, karmaşık bir çözümü modüllerle bozar etmenizi sağlar. Bir Bıcep modülü birlikte dağıtılacak bir veya daha fazla kaynak kümesidir. Modüller, ham kaynak bildiriminin, okunabilirliği artıran karmaşık ayrıntılarını soyutlar. Bu modülleri yeniden kullanabilir ve diğer kişilerle paylaşabilirsiniz. [Şablon Özellikleri](./template-specs.md)ile birlikte kullanıldığında, modülerlik ve kod yeniden kullanımı için bir yol oluşturur. Bıcep modülleri, dağıtım için [iç içe şablonlar](./linked-templates.md#nested-template) içeren tek bir ARM şablonuna transpiled. Bıcep 'de [_Bağımlıdson_](./template-syntax.md#resources) , otomatik olarak işlenir.

Öğretici için bkz. [öğretici: Bıcep modülleri ekleme](./bicep-tutorial-add-modules.md).

## <a name="define-modules"></a>Modülleri tanımlama

Her bicep dosyası bir modül olarak tüketilebilir. Modül, parametreleri ve çıkışları yalnızca diğer bicep dosyalarına sözleşme olarak kullanıma sunar. Her iki parametre ve çıkış isteğe bağlıdır.

Aşağıdaki bicep dosyası bir depolama hesabı oluşturmak veya bir modül olarak kullanılmak üzere doğrudan dağıtılabilir.  Sonraki bölümde, modüllerin nasıl kullanılacağı gösterilmektedir:

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

Çıkış, ana bicep dosyalarına değer geçirmek için kullanılır.

## <a name="consume-modules"></a>Modülleri kullanma

Modül kullanmak için _module_ anahtar sözcüğünü kullanın. Aşağıdaki bicep dosyası, başvurulan modül dosyasında tanımlanan kaynağı dağıtır:

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **module**: anahtar sözcüğü.
- **simgesel ad** (stgmodule): modülün tanımlayıcısı.
- **modül dosyası**: Bu örnekteki modülün yolu, göreli bir yol (./Storageaccount.exe) kullanılarak belirtilir. Bıcep içindeki tüm yollar, tutarlı derleme platformlar arası sağlamak için eğik çizgi (/) dizin ayırıcısı kullanılarak belirtilmelidir. Windows ters eğik çizgi ( \\ ) karakteri desteklenmiyor.
- Bir modül kullanılırken **_Name_** özelliği (storagedeploy) gereklidir. Bıcep, şablonu Il oluşturduğunda, bu alan modül için oluşturulan iç içe dağıtım kaynağının adı olarak kullanılır:

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```

Bir modülden çıkış değeri almak için, şu şekilde sözdizimi ile özellik değerini alın: `stgModule.outputs.storageEndpoint` WHERE `stgModule` modülün tanımlayıcısıdır.

## <a name="configure-module-scopes"></a>Modül kapsamlarını yapılandırma

Bir modül bildirirken, modülün dağıtılacağı kapsamı ayarlamak için bir _scope_ özelliği sağlayabilirsiniz:

```bicep
module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: resourceGroup('someOtherRg') // pass in a scope to a different resourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}
```

Modülün hedef kapsamı ve üst öğenin hedef kapsamı aynı olduğunda _scope_ özelliği atlanabilir. Scope özelliği sağlanmazsa, modül üst öğenin hedef kapsamında dağıtılır.

Aşağıdaki bicep dosyası, bir kaynak grubu oluşturmayı ve kaynak grubuna bir modül dağıtmayı gösterir:

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2020-01-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

## <a name="next-steps"></a>Sonraki adımlar

- Öğreticiye gitmek için bkz. [öğretici: Bıcep modülleri ekleme](./bicep-tutorial-add-modules.md).
