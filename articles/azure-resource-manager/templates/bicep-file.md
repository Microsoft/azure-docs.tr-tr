---
title: Bıcep dosya yapısı ve sözdizimi
description: Bildirim temelli söz dizimi kullanarak bir Bıcep dosyasının yapısını ve özelliklerini açıklar.
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: 1b8eddd388878be8f653f963ef967cf2c0af685f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537865"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Bıcep dosyalarının yapısını ve söz dizimini anlayın

Bu makalede bir Bıcep dosyasının yapısı açıklanır. Dosyanın farklı bölümlerini ve bu bölümlerde kullanılabilen özellikleri gösterir.

Bu makale, bicep dosyaları hakkında bazı benzerlik sahibi olan kullanıcılar için tasarlanmıştır. Şablonun yapısı hakkında ayrıntılı bilgi sağlar. Bicep dosyası oluşturma sürecinde size kılavuzluk eden adım adım bir öğretici için bkz. [öğretici: ilk Azure Resource Manager Bıcep dosyası oluşturma ve dağıtma](bicep-tutorial-create-first-bicep.md).

## <a name="template-format"></a>Şablon biçimi

Bir Bıcep dosyası aşağıdaki öğelere sahiptir. Öğeler herhangi bir sırada görünebilir.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

// conditional deployment
resource <resource-symbolic-name> '<resource-type>@<api-version>' = if (<condition-to-deploy>) {
  <resource-properties>
}

// iterative deployment
@<decorator>(<argument>)
resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
  <resource-properties>
}]

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// conditional deployment
module <module-symbolic-name> '<path-to-file>' = if (<condition-to-deploy>) {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// iterative deployment
module <module-symbolic-name> '<path-to-file>' = [for <item> in <collection>: {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}]

output <output-name> <output-data-type> = <output-value>
```

Aşağıdaki örnek, bu öğelerin bir uygulamasını gösterir.

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location

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

module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

## <a name="target-scope"></a>Hedef kapsam

Varsayılan olarak, hedef kapsam olarak ayarlanır `resourceGroup` . Kaynak grubu düzeyinde dağıtım yapıyorsanız, Bıcep dosyanızda hedef kapsamı ayarlamanız gerekmez.

İzin verilen değerler şunlardır:

* **resourceGroup** - [kaynak grubu dağıtımları](deploy-to-resource-group.md)için kullanılan varsayılan değer.
* **abonelik** - [abonelik dağıtımları](deploy-to-subscription.md)için kullanılır.
* **ManagementGroup** - [Yönetim grubu dağıtımları](deploy-to-management-group.md)için kullanılır.
* **kiracı** - [kiracı dağıtımları](deploy-to-tenant.md)için kullanılır.

## <a name="parameters"></a>Parametreler

Farklı dağıtımlar için farklılık göstermesi gereken değerler için parametreleri kullanın. Dağıtım sırasında hiçbir değer sağlanmazsa kullanılan parametresi için varsayılan bir değer tanımlayabilirsiniz.

Örneğin, bir kaynak için farklı boyutlar belirtmek üzere bir SKU parametresi ekleyebilirsiniz. Kaynak grubu konumunu alma gibi varsayılan değeri oluşturmak için şablon işlevlerini kullanabilirsiniz.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

Kullanılabilir veri türleri için bkz. [şablonlarda veri türleri](data-types.md).

Daha fazla bilgi için bkz. [şablonlarda parametreler](template-parameters.md).

## <a name="parameter-decorators"></a>Parametre Dekoratörleri

Her bir parametre için bir veya daha fazla dekoratlayıcısı ekleyebilirsiniz. Bu dekoratörler, parametresi için izin verilen değerleri tanımlar. Aşağıdaki örnek, Bıcep dosyası aracılığıyla dağıtılabilecek SKU 'Ları belirtir.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

Aşağıdaki tabloda, kullanılabilir dekoratörler ve bunların nasıl kullanılacağı açıklanmaktadır.

| Dekoratör | Uygula | Bağımsız Değişken | Açıklama |
| --------- | ---- | ----------- | ------- |
| izin verilen | tümü | array | Parametre için izin verilen değerler. Kullanıcının doğru değerler sağladığından emin olmak için bu dekoratörü kullanın. |
| açıklama | tümü | string | Parametresinin nasıl kullanılacağını açıklayan metin. Açıklama, portalda kullanıcılar için görüntülenir. |
| 'In | dizi, dize | int | Dize ve dizi parametreleri için uzunluk üst sınırı. Değer dahil değildir. |
| Değerini | int | int | Integer parametresinin en büyük değeri. Bu değer dahil değildir. |
| meta veriler | tümü | object | Parametreye uygulanacak özel özellikler. Açıklama dekoratörü ile eşdeğer bir açıklama özelliği içerebilir. |
| minLength | dizi, dize | int | Dize ve dizi parametreleri için minimum uzunluk. Değer dahil değildir. |
| minValue | int | int | Integer parametresinin en küçük değeri. Bu değer dahil değildir. |
| güvende | dize, nesne | yok | Parametreyi güvenli olarak işaretler. Güvenli bir parametre değeri dağıtım geçmişine kaydedilmez ve günlüğe kaydedilmez. Daha fazla bilgi için bkz. [güvenli dizeler ve nesneler](data-types.md#secure-strings-and-objects). |

## <a name="variables"></a>Değişkenler

Bir bicep dosyasında yinelenen karmaşık ifadeler için değişkenler kullanın. Örneğin, birkaç değeri birleştirerek oluşturulmuş bir kaynak adı için bir değişken ekleyebilirsiniz.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

Bir değişken için [veri türü](data-types.md) belirtmezsiniz. Bunun yerine, veri türü değerden algılanır.

Daha fazla bilgi için bkz. [şablonlarda değişkenler](template-variables.md).

## <a name="resource"></a>Kaynak

`resource`Dağıtılacak kaynağı tanımlamak için anahtar sözcüğünü kullanın. Kaynak bildirimidir, kaynak için bir sembolik ad içerir. Kaynaktan bir değer almanız gerekiyorsa bu sembolik adı Bıcep dosyasının diğer bölümlerinde kullanacaksınız.

Kaynak bildirimi, kaynak türünü ve API sürümünü de içerir.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
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
```

Kaynak bildirimindeki kaynak türü için özellikler dahil edersiniz. Bu özellikler her kaynak türü için benzersizdir.

Daha fazla bilgi için bkz. [şablonlarda kaynak bildirimi](resource-declaration.md).

[Bir kaynağı koşullu olarak dağıtmak](conditional-resource-deployment.md)için bir `if` ifade ekleyin.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
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
```

Kaynak türünün birden [fazla örneğini dağıtmak](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) için bir `for` ifade ekleyin. İfade, bir dizinin üyeleri üzerinde yineleyebilir.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  name: storageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}]
```

## <a name="modules"></a>Modül

Yeniden kullanmak istediğiniz kodu içeren diğer bicep dosyalarına bağlamak için modüller kullanın. Modül dağıtılacak bir veya daha fazla kaynak içeriyor. Bu kaynaklar, Bıcep dosyanızdaki diğer kaynaklarla birlikte dağıtılır.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

Simgesel ad, modüldeki başka bir yerde modüle başvuru yapmanızı sağlar. Örneğin, simgesel adı ve çıkış değerinin adını kullanarak bir modülden çıkış değeri alabilirsiniz.

Kaynaklar gibi, bir modülü koşullu olarak veya tekrarlayarak dağıtabilirsiniz. Söz dizimi, modüller için kaynak olarak aynıdır.

Daha fazla bilgi için bkz. [Bıcep modüllerini kullanma](bicep-modules.md).

## <a name="resource-and-module-decorators"></a>Kaynak ve modül Dekoratörleri

Bir kaynak veya modül tanımına dekoratör ekleyebilirsiniz. Yalnızca desteklenen dekoratör `batchSize(int)` . Yalnızca bir ifade kullanan bir kaynağa veya modül tanımına uygulayabilirsiniz `for` .

Varsayılan olarak, kaynaklar paralel olarak dağıtılır. Bitdikleri sırayı bilemezsiniz. Dekoratörü eklediğinizde `batchSize` , örnekleri hizmet dağıtırsınız. Paralel olarak dağıtılacak örneklerin sayısını belirtmek için tamsayı bağımsız değişkenini kullanın.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

Daha fazla bilgi için bkz. [seri veya paralel](copy-resources.md#serial-or-parallel).

## <a name="outputs"></a>Çıkışlar

Dağıtımdan değer döndürmek için çıktıları kullanın. Genellikle, başka bir işlem için bu değeri yeniden kullanmanız gerektiğinde, dağıtılan bir kaynaktan bir değer döndürüyordu.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Çıkış değeri için bir [veri türü](data-types.md) belirtin.

Daha fazla bilgi için bkz. [şablonlarda çıktılar](template-outputs.md).

## <a name="comments"></a>Yorumlar

`//`Tek satır açıklamaları veya `/* ... */` çok satırlı açıklamalar için kullanın

Aşağıdaki örnek, tek satırlık bir açıklama gösterir.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

Aşağıdaki örnek, çok satırlı bir açıklama gösterir.

```bicep
/*
  This template assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>Çok satırlı dizeler

Bir dizeyi birden çok satıra kesebilirsiniz. `'''`Çok satırlı dizeyi başlatmak ve sonlandırmak için üç tek tırnak karakteri kullanın.

Çok satırlı dize içindeki karakterler olduğu gibi işlenir. Kaçış karakterleri gereksizdir. `'''`Çok satırlı dizeye dahil etmeniz gerekir. Dize ilişkilendirme Şu anda desteklenmiyor.

Dizenizi doğrudan açtıktan sonra başlatabilir `'''` veya yeni bir satır ekleyebilirsiniz. Her iki durumda da, sonuçta elde edilen dize yeni bir satır içermez. Bicep dosyanızdaki satır sonlarına bağlı olarak, yeni satırlar veya olarak yorumlanır `\r\n` `\n` .

Aşağıdaki örnek, çok satırlı bir dizeyi gösterir.

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

Yukarıdaki örnek aşağıdaki JSON ile eşdeğerdir.

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bıcep 'ye giriş için bkz. [bicep nedir?](bicep-overview.md).
