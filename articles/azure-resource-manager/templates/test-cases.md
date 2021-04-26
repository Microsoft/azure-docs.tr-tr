---
title: Test araç seti için test çalışmaları
description: ARM şablonu test araç seti tarafından çalıştırılan testleri açıklar.
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7805d6dbdb8b93968a2792ed6dfaf2ac8fea9ae5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363402"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>ARM şablonu test araç seti için varsayılan test çalışmaları

Bu makalede, Azure Resource Manager şablonları (ARM şablonları) için [şablon test araç seti](test-toolkit.md) ile çalıştırılan varsayılan testler açıklanmaktadır. Testi geçiren veya başarısız olan örnekler sağlar. Her testin adını içerir. Belirli bir testi çalıştırmak için bkz. [test parametreleri](test-toolkit.md#test-parameters).

## <a name="use-correct-schema"></a>Doğru şemayı kullan

Test adı: **Deploymenttemplate şeması doğru**

Şablonunuzda geçerli bir şema değeri belirtmeniz gerekir.

Aşağıdaki örnek bu testi **geçirir** .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

Şablondaki şema özelliğinin aşağıdaki şemalardan birine ayarlanması gerekir:

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>Parametreler var olmalıdır

Test adı: **Parameters özelliği var olmalıdır**

Şablonunuz bir Parameters öğesine sahip olmalıdır. Parametreler, şablonlarınızı farklı ortamlarda yeniden kullanılabilir hale getirmek için gereklidir. Farklı ortamlara dağıtım yaparken değişen değerler için şablonunuza parametreler ekleyin.

Aşağıdaki örnek bu testi **geçirir** :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>Tanımlanan parametrelerin kullanılması gerekir

Test adı: **parametrelere başvurulmalıdır**

Şablonunuzda karışıklığı azaltmak için tanımlanmış ancak kullanılmayan parametreleri silin. Bu test, şablonda herhangi bir yerde kullanılmayan parametreleri bulur. Kullanılmayan parametreleri ortadan kaldırmak, gerekli olmayan değerler sağlamak zorunda olmadığınız için şablonunuzu dağıtmayı da kolaylaştırır.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>Güvenli Parametreler sabit kodlanmış varsayılana sahip olamaz

Test adı: **güvenli dize parametrelerinin varsayılan olamaz**

Şablonunuzda güvenli bir parametre için sabit kodlanmış bir varsayılan değer sağlamaın. Boş bir dize varsayılan değer için uygundur.

Parolalar gibi hassas değerler içeren parametrelerde **SecureString** veya **secureobject** türlerini kullanırsınız. Bir parametre güvenli bir tür kullandığında, parametrenin değeri, dağıtım geçmişinde günlüğe kaydedilmez veya depolanmaz. Bu eylem, kötü niyetli bir kullanıcının hassas değeri bulmasını engeller.

Ancak, varsayılan bir değer sağladığınızda, bu değer şablona veya dağıtım geçmişine erişebilen herkes tarafından bulunabilir.

Aşağıdaki örnekte bu test **başarısız olur** :

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

Sonraki örnekte bu test **geçirilir** :

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>Ortam URL 'Leri sabit kodlanamıyor

Test adı: **Deploymenttemplate, sabit kodlanmış URI içermemelidir**

Şablonunuzda kod ortamı URL 'Leri oluşturmayın. Bunun yerine, dağıtım sırasında bu URL 'Leri dinamik olarak almak için [Environment işlevini](template-functions-deployment.md#environment) kullanın. Engellenen URL konaklarının bir listesi için, bkz. [test çalışması](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1).

URL sabit kodlandığı için aşağıdaki örnek bu testi **başarısız oluyor** .

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

Test, [Concat](template-functions-string.md#concat) veya [URI](template-functions-string.md#uri)ile kullanıldığında de **başarısız olur** .

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

Aşağıdaki örnek bu testi **geçirir** .

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>Konum, parametresini kullanır

Test adı: **konum sabit kodlanmamalıdır**

Şablonlarınız konum adlı bir parametreye sahip olmalıdır. Şablonunuzda kaynakların konumunu ayarlamak için bu parametreyi kullanın. Ana şablonda ( _azuredeploy.js_ veya _mainTemplate.json_ olarak adlandırılır), bu parametre varsayılan kaynak grubu konumu olarak belirtilebilir. Bağlantılı veya iç içe şablonlarda, konum parametresinin varsayılan bir konumu olmamalıdır.

Şablonunuz için kullanılabilen bölgeler sınırlı olabilir. Kaynak konumunu sabit kodunuzda, kullanıcıların o bölgede bir kaynak oluşturması engellenebilir. Kaynak konumunu olarak ayarlamış olsanız bile kullanıcılar engellenebilir `"[resourceGroup().location]"` . Kaynak grubu, diğer kullanıcıların erişebileceği bir bölgede oluşturulmuş olabilir. Bu kullanıcıların şablonu kullanmalarını engelledi.

Kaynak grubu konumuna varsayılan olarak bir konum parametresi sağlayarak, kullanıcılar uygun olduğunda varsayılan değeri kullanabilir, ancak farklı bir konum da belirtebilir.

Kaynaktaki konum olarak ayarlandığı için aşağıdaki örnekte bu test **başarısız olur** `resourceGroup().location` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

Sonraki örnek bir konum parametresi kullanır, ancak location parametresi sabit kodlanmış bir konuma göre varsayılan olarak bu testi **başarısız olur** .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Bunun yerine, kaynak grubu konumuna varsayılan olarak bir parametre oluşturun, ancak kullanıcıların farklı bir değer sağlamasına izin verir. Aşağıdaki örnek, şablon ana şablon olarak kullanıldığında bu testi **geçirir** .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Ancak, yukarıdaki örnek bağlantılı bir şablon olarak kullanılıyorsa, test **başarısız olur**. Bağlı şablon olarak kullanıldığında, varsayılan değeri kaldırın.

## <a name="resources-should-have-location"></a>Kaynakların konumu olmalıdır

Test adı: **kaynakların konumu** olmalıdır

Bir kaynağın konumu bir [şablon ifadesi](template-expressions.md) veya olarak ayarlanmalıdır `global` . Şablon ifadesi genellikle önceki testte açıklanan konum parametresini kullanır.

Aşağıdaki örnek, konum bir ifade veya olmadığı için test **başarısız olur** `global` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Aşağıdaki örnek bu testi **geçirir** .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

Sonraki örnek bu testi de **geçirir** .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>VM boyutu parametresini kullanır

Test adı: **VM boyutu bir parametre** olmalıdır

Sanal makine boyutunu sabit bir şekilde kodmayın. Şablonunuzun kullanıcıları dağıtılan sanal makinenin boyutunu değiştirebilmeleri için bir parametre sağlayın.

Aşağıdaki örnek bu testte **başarısız olur** .

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

Bunun yerine bir parametre sağlayın.

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

Ardından, VM boyutunu bu parametreye ayarlayın.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>Min ve Max değerleri rakamlardan

Test adı: **Min ve Max değeri sayılardır**

Bir parametre için min ve Max değerlerini tanımlarsanız, bunları sayı olarak belirtin.

Aşağıdaki örnekte bu test **başarısız olur** :

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Bunun yerine, değerleri sayı olarak belirtin. Aşağıdaki örnek bu testi **geçirir** :

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

Ayrıca, bir Min veya Max değeri sağlarsanız, diğerini değil de bu uyarıyı alırsınız.

## <a name="artifacts-parameter-defined-correctly"></a>Yapıt parametresi doğru tanımlandı

Test adı: **yapıt parametresi**

Ve için parametreler dahil ettiğinizde `_artifactsLocation` `_artifactsLocationSasToken` , doğru Varsayılanları ve türleri kullanın. Bu testi iletmek için aşağıdaki koşulların karşılanması gerekir:

* bir parametre sağlarsanız, diğerini sağlamanız gerekir
* `_artifactsLocation`bir **dize** olmalıdır
* `_artifactsLocation` Ana şablonda bir varsayılan değere sahip olmalıdır
* `_artifactsLocation` iç içe yerleştirilmiş bir şablonda varsayılan değere sahip olamaz
* `_artifactsLocation``"[deployment().properties.templateLink.uri]"`varsayılan değeri için ya da ham depo URL 'si olmalıdır
* `_artifactsLocationSasToken`**secureString** olmalıdır
* `_artifactsLocationSasToken` Varsayılan değeri için yalnızca boş bir dize olabilir
* `_artifactsLocationSasToken` iç içe yerleştirilmiş bir şablonda varsayılan değere sahip olamaz

## <a name="declared-variables-must-be-used"></a>Tanımlanan değişkenler kullanılmalıdır

Test adı: **değişkenlere başvurulmalıdır**

Şablonunuzda karışıklığı azaltmak için tanımlanan ancak kullanılmayan tüm değişkenleri silin. Bu test, şablonda herhangi bir yerde kullanılmayan değişkenleri bulur.

## <a name="dynamic-variable-should-not-use-concat"></a>Dinamik değişken Concat kullanmamalıdır

Test adı: **dinamik değişken başvuruları Concat kullanmamalıdır**

Bazen başka bir değişkenin veya parametrenin değerine göre dinamik olarak bir değişken oluşturmanız gerekir. Değer ayarlanırken [Concat](template-functions-string.md#concat) işlevini kullanmayın. Bunun yerine, kullanılabilir seçenekleri içeren bir nesne kullanın ve dağıtım sırasında nesneden özelliklerden birini dinamik olarak alın.

Aşağıdaki örnek bu testi **geçirir** . **Currentımage** değişkeni dağıtım sırasında dinamik olarak ayarlanır.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>Son API sürümünü kullan

Test adı: **Apiversions son zamanlarda** olmalıdır

Her kaynak için API sürümü en son sürümü kullanmalıdır. Test, kullandığınız sürümü bu kaynak türü için kullanılabilir sürümlere göre değerlendirir.

## <a name="use-hardcoded-api-version"></a>Sabit kodlanmış API sürümünü kullanma

Test adı: **apiVersions sağlayıcıları Izin verilmiyor**

Bir kaynak türü için API sürümü hangi özelliklerin kullanılabilir olduğunu belirler. Şablonunuzda sabit kodlanmış bir API sürümü sağlayın. Dağıtım sırasında belirlenen bir API sürümünü alma. Hangi özelliklerin kullanılabildiğini bilemezsiniz.

Aşağıdaki örnek bu testte **başarısız olur** .

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

Aşağıdaki örnek bu testi **geçirir** .

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>Özellikler boş olamaz

Test adı: **şablon boşluk içermemelidir**

Kod özelliklerini boş bir değere gönderme. Boş değerler null ve boş dizeler, nesneler veya diziler içerir. Boş bir değere bir özellik ayarladıysanız, bu özelliği şablondan kaldırın. Ancak, dağıtım sırasında bir parametre gibi bir özelliği boş bir değere ayarlamanız normaldir.

## <a name="use-resource-id-functions"></a>Kaynak KIMLIĞI işlevlerini kullanma

Test adı: **kimlikler Resourceıds 'Den türetilmelidir**

Bir kaynak KIMLIĞI belirtirken, kaynak KIMLIĞI işlevlerinden birini kullanın. İzin verilen işlevler şunlardır:

* [resourceId](template-functions-resource.md#resourceid)
* [Subscriptionresourceıd](template-functions-resource.md#subscriptionresourceid)
* [Tenantresourceıd](template-functions-resource.md#tenantresourceid)
* [Extensionresourceıd](template-functions-resource.md#extensionresourceid)

Bir kaynak KIMLIĞI oluşturmak için Concat işlevini kullanmayın. Aşağıdaki örnek bu testte **başarısız olur** .

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

Sonraki örnek bu testi **geçirir** .

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>RESOURCEID işlevinde doğru parametreler vardır

Test adı: **Resourceıds içermemelidir**

Kaynak kimlikleri oluştururken, isteğe bağlı parametreler için gereksiz işlevler kullanmayın. Varsayılan olarak, [RESOURCEID](template-functions-resource.md#resourceid) işlevi geçerli aboneliği ve kaynak grubunu kullanır. Bu değerleri sağlamanız gerekmez.

Aşağıdaki örnek, geçerli abonelik KIMLIĞINI ve kaynak grubu adını sağlamanız gerekmeyen için bu testi **başarısız olur** .

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Sonraki örnek bu testi **geçirir** .

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Bu test için geçerlidir:

* [resourceId](template-functions-resource.md#resourceid)
* [Subscriptionresourceıd](template-functions-resource.md#subscriptionresourceid)
* [Tenantresourceıd](template-functions-resource.md#tenantresourceid)
* [Extensionresourceıd](template-functions-resource.md#extensionresourceid)
* [başvurunun](template-functions-resource.md#reference)
* [Listele](template-functions-resource.md#list)

Ve için, `reference` `list*` kaynak kimliği oluşturmak için kullandığınızda test **başarısız olur** `concat` .

## <a name="dependson-best-practices"></a>Bağımlıdson en iyi uygulamalar

Test adı: **Bağımlıdson En Iyi Yöntemler**

Dağıtım bağımlılıklarını ayarlarken, bir koşulu test etmek için [IF](template-functions-logical.md#if) işlevini kullanmayın. Bir kaynak [koşullu olarak dağıtılan](conditional-resource-deployment.md)bir kaynağa bağımlıysa, bağımlılığı herhangi bir kaynakta olduğu gibi ayarlayın. Koşullu bir kaynak dağıtıldığında Azure Resource Manager, gerekli bağımlılıklardan otomatik olarak kaldırır.

Aşağıdaki örnek bu testte **başarısız olur** .

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

Sonraki örnek bu testi **geçirir** .

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>İç içe veya bağlı dağıtımlar hata ayıklamayı kullanamıyor

Test adı: **dağıtım kaynaklarının hata ayıklama olmaması gerekir**

**Microsoft. resources/dağıtımlar** kaynak türü ile [iç içe veya bağlı bir şablon](linked-templates.md) tanımladığınızda, bu şablon için hata ayıklamayı etkinleştirebilirsiniz. Hata ayıklama, bu şablonu test etmeniz gerektiğinde, ancak şablonu üretimde kullanmaya hazırsanız açılmalıdır.

## <a name="admin-user-names-cant-be-literal-value"></a>Yönetici Kullanıcı adları değişmez değer olamaz

Test adı: **AdminUserName bir sabit değer** olmamalıdır

Yönetici Kullanıcı adı ayarlanırken, değişmez değer kullanmayın.

Aşağıdaki örnekte bu test **başarısız olur** :

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Bunun yerine, bir parametre kullanın. Aşağıdaki örnek bu testi **geçirir** :

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>En son VM görüntüsünü kullan

Test adı: **VM görüntülerinin en son sürümü kullanması gerekir**

Şablonunuz görüntü içeren bir sanal makine içeriyorsa, görüntünün en son sürümünü kullandığından emin olun.

## <a name="use-stable-vm-images"></a>Kararlı VM görüntülerini kullanma

Test adı: **sanal makineler önizleme olmamalıdır**

Sanal makineler önizleme görüntülerini kullanmamalıdır.

Aşağıdaki örnek bu testte **başarısız olur** .

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

Aşağıdaki örnek bu testi **geçirir** .

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Managedıdentity uzantısını kullanma

Test adı: **Managedıdentityextension kullanılmamalıdır**

Managedıdentity uzantısını bir sanal makineye uygulamayın. Uzantı 2019 ' de kullanımdan kaldırılmıştır ve artık kullanılmamalıdır.

## <a name="outputs-cant-include-secrets"></a>Çıkışlar gizli dizileri içeremez

Test adı: **çıktılar gizli dizileri içermemelidir**

Gizli dizileri açığa çıkarabilecek çıktılar bölümüne hiçbir değer eklemeyin. Bir şablonun çıktısı dağıtım geçmişinde depolanır, bu nedenle kötü amaçlı bir Kullanıcı bu bilgileri bulabilir.

Aşağıdaki örnek, çıkış değerinde güvenli bir parametre içerdiğinden test **başarısız olur** .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

Aşağıdaki örnek, çıktılarında bir [list *](template-functions-resource.md#list) işlevi kullandığından **başarısız olur** .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="use-protectedsettings-for-commandtoexecute-secrets"></a>CommandToExecute gizli dizileri için protectedSettings kullanma

Test adı: **Commandtoexecute, gizlilikler Için ProtectedSettings kullanmalıdır**

Özel bir betik uzantısında `protectedSettings` `commandToExecute` parola gibi gizli verileri içerdiğinde şifreli özelliğini kullanın. Gizli veri türü örnekleri, `secureString` `secureObject` , `list()` işlevleri veya betiklerdir.

Sanal makineler için özel Betik uzantısı hakkında daha fazla bilgi için bkz. [Windows](
/azure/virtual-machines/extensions/custom-script-windows), [Linux](/azure/virtual-machines/extensions/custom-script-linux)ve şema [Microsoft. COMPUTE virtualmachines/Extensions](/azure/templates/microsoft.compute/virtualmachines/extensions).

Bu örnekte, şifrelenen özelliği içerdiğinden, adlı bir parametreye sahip bir şablon `adminPassword` `secureString` , testi **geçirir** `protectedSettings` `commandToExecute` .

```json
"properties": [
  {
    "protectedSettings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

Şifrelenmemiş özelliği içeriyorsa test **başarısız olur** `settings` `commandToExecute` .

```json
"properties": [
  {
    "settings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

## <a name="next-steps"></a>Sonraki adımlar

* Test araç setini çalıştırma hakkında bilgi edinmek için bkz. [ARM şablonu test araç seti kullanma](test-toolkit.md).
* Test araç setini kullanarak kapsanan bir Microsoft Learn modülü için bkz. [değişiklik önizleme ve Azure kaynaklarını doğrulama, durum ve ARM şablonu test araç seti](/learn/modules/arm-template-test/).
