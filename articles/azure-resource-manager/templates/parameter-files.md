---
title: Parametre dosyası oluşturma
description: Azure Resource Manager şablonunun dağıtımı sırasında değerleri geçirmek için parametre dosyası oluşturma
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: ddeaed94396aa662b795ae5701aa367ba13d869b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531209"
---
# <a name="create-resource-manager-parameter-file"></a>Kaynak Yöneticisi parametre dosyası oluştur

Komut dosyanıza satır içi değer olarak parametre geçirmek yerine, parametre değerlerini içeren bir JSON dosyası kullanabilirsiniz. Bu makalede, bir JSON şablonuyla veya Bıcep dosyasıyla birlikte kullandığınız bir parametre dosyasının nasıl oluşturulacağı gösterilmektedir.

## <a name="parameter-file"></a>Parametre dosyası

Bir parametre dosyası aşağıdaki biçimi kullanır:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Parametre dosyasının parametre değerlerini düz metin olarak depoladığını unutmayın. Bu yaklaşım, bir kaynak SKU 'SU gibi hassas olmayan değerler için geçerlidir. Düz metin parolalar gibi hassas değerler için çalışmaz. Hassas değer içeren bir parametre geçirmeniz gerekiyorsa, değeri bir anahtar kasasında saklayın. Ardından parametre dosyanızdaki anahtar kasasına başvurun. Hassas değer dağıtım sırasında güvenli bir şekilde alınır.

Aşağıdaki parametre dosyası, bir anahtar kasasında depolanan bir düz metin değeri ve hassas bir değer içerir.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

Anahtar kasasından değerleri kullanma hakkında daha fazla bilgi için bkz. [dağıtım sırasında güvenli parametre değeri geçirmek için Azure Key Vault kullanma](key-vault-parameter.md).

## <a name="define-parameter-values"></a>Parametre değerlerini tanımla

Parametre adlarının ve değerlerinin nasıl tanımlanacağını belirlemek için JSON veya Bıcep şablonunuzu açın. Şablonun parametreler bölümüne bakın. Aşağıdaki örneklerde JSON ve Bıcep şablonlarından parametreler gösterilmektedir.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageAccountType string = 'Standard_LRS'
```

---

Parametre dosyasında, dikkat edilecek ilk ayrıntı her parametrenin adıdır. Parametre dosyanızdaki parametre adları, şablonunuzda parametre adlarıyla eşleşmelidir.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Parametre türüne dikkat edin. Parametre dosyanızdaki parametre türleri şablonunuzda aynı türleri kullanmalıdır. Bu örnekte her iki parametre türü de dizelerdir.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Varsayılan değere sahip parametreler için şablonu kontrol edin. Bir parametre varsayılan değere sahipse, parametre dosyasında bir değer sağlayabilirsiniz, ancak bu gerekli değildir. Parametre dosyası değeri, şablonun varsayılan değerini geçersiz kılar.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Şablonun izin verilen değerlerini ve en fazla uzunluk gibi kısıtlamaları kontrol edin. Bu değerler, bir parametre için sağlayabilmeniz için değer aralığını belirtir. Bu örnekte, `storagePrefix` en fazla 11 karakter uzunluğunda olabilir ve `storageAccountType` izin verilen bir değer belirtmeniz gerekir.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

> [!NOTE]
> Parametre dosyanız yalnızca şablonda tanımlanmış parametreler için değerler içerebilir. Parametre dosyanız, şablonun parametreleriyle eşleşmeyen ek parametreler içeriyorsa, bir hata alırsınız.

## <a name="parameter-type-formats"></a>Parametre türü biçimleri

Aşağıdaki örnek farklı parametre türlerinin biçimlerini gösterir: String, Integer, Boolean, Array ve Object.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
  }
}
```

## <a name="deploy-template-with-parameter-file"></a>Parametre dosyası ile şablon dağıtma

Azure CLı 'dan, `@` ve parametre dosya adını kullanarak yerel bir parametre dosyası geçirirsiniz. Örneğin, `@storage.parameters.json`.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Daha fazla bilgi için bkz. [ARM şablonları ve Azure CLI ile kaynak dağıtma](./deploy-cli.md#parameters). _. Bıcep_ dosyalarını dağıtmak IÇIN Azure clı sürüm 2,20 veya üzeri bir sürüme ihtiyacınız vardır.

Azure PowerShell, parametresini kullanarak yerel bir parametre dosyası geçirirsiniz `TemplateParameterFile` .

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile C:\MyTemplates\storage.json `
  -TemplateParameterFile C:\MyTemplates\storage.parameters.json
```

Daha fazla bilgi için bkz. [ARM şablonlarıyla kaynakları dağıtma ve Azure PowerShell](./deploy-powershell.md#pass-parameter-values). _. Bıcep_ dosyalarını dağıtmak için, sürüm 5.6.0 veya üzeri Azure PowerShell gerekir.

> [!NOTE]
> Portalda özel şablon dikey penceresinde bir parametre dosyası kullanmak mümkün değildir.

> [!TIP]
> [Visual Studio 'Da Azure Kaynak grubu projesi](create-visual-studio-deployment-project.md)kullanıyorsanız, parametre dosyasının **Yapı eylemi** **içerik** olarak ayarlanmış olduğundan emin olun.

## <a name="file-name"></a>Dosya adı

Parametre dosyası için genel adlandırma kuralı, _parametreleri_ şablon adına dahil etmek içindir. Örneğin, şablonunuz _azuredeploy.js_ olarak adlandırılmışsa, parametre dosyanız _üzerindeazuredeploy.parameters.js_ olarak adlandırılır. Bu adlandırma kuralı, şablon ve parametreler arasındaki bağlantıyı görmenizi sağlar.

Farklı ortamlara dağıtmak için birden fazla parametre dosyası oluşturursunuz. Parametre dosyalarını adlandırma sırasında geliştirme ve üretim gibi kullanımlarını belirleyebilirsiniz. Örneğin, kaynak dağıtmak için _üzerindeazuredeploy.parameters-dev.js_ ve _azuredeploy.parameters-prod.js_ kullanın.

## <a name="parameter-precedence"></a>Parametre önceliği

Aynı dağıtım işleminde satır içi parametreleri ve yerel bir parametre dosyasını kullanabilirsiniz. Örneğin, yerel parametre dosyasında bazı değerler belirtebilir ve dağıtım sırasında satır içi diğer değerleri ekleyebilirsiniz. Hem yerel parametre dosyasında hem de satır içi bir parametre için değerler sağlarsanız, satır içi değer önceliklidir.

Dosyaya URI sağlayarak bir dış parametre dosyası kullanmak mümkündür. Dış parametre dosyası kullandığınızda, diğer değerleri satır içi veya yerel bir dosyadan geçiremezsiniz. Tüm satır içi parametreler yok sayılır. Dış dosyadaki tüm parametre değerlerini belirtin.

## <a name="parameter-name-conflicts"></a>Parametre adı çakışmaları

Şablonunuz, PowerShell komutundaki parametrelerden biriyle aynı ada sahip bir parametre içeriyorsa, PowerShell şablondan parametreyi sonek ile gösterir `FromTemplate` . Örneğin, şablonunuzda adlı bir parametre `ResourceGroupName` `ResourceGroupName` [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet 'inin parametresiyle çakışıyor. İçin bir değer sağlamanız istenir `ResourceGroupNameFromTemplate` . Bu karışıklığın önüne geçmek için, dağıtım komutları için kullanılmayan parametre adlarını kullanın.

## <a name="next-steps"></a>Sonraki adımlar

- Bir şablonda parametrelerin nasıl tanımlanacağı hakkında daha fazla bilgi için bkz. [ARM şablonlarındaki parametreler](template-parameters.md).
- Anahtar kasasından değerleri kullanma hakkında daha fazla bilgi için bkz. [dağıtım sırasında güvenli parametre değeri geçirmek için Azure Key Vault kullanma](key-vault-parameter.md).
