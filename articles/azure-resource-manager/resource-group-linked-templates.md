---
title: Dağıtım için şablonları bağlama
description: Modüler şablon çözüm oluşturmak için bir Azure Resource Manager şablonunda bağlı şablonların kullanmayı açıklar. Parametre değerlerini geçirmek için bir parametre dosyası ve dinamik olarak oluşturulan URL'leri belirtin gösterilmektedir.
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 985520963ba9fab1a7f71e1af76bba4390c751ad
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149716"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Bağlı, şablonları Azure kaynakları dağıtılırken iç içe kullanma

Çözümünüzü dağıtmak için tek bir şablondan ya da bir ana şablon ile ilgili birçok şablonu kullanabilirsiniz. İlgili şablonlar, ana şablondan ya da ana şablon içinde iç içe yerleştirilmiş şablonlardan bağlantılı ayrı dosyalar olabilir.

Küçük ila orta çözümleri, tek bir şablon anlamak ve sürdürmek daha kolay olur. Tüm kaynaklar ve tek bir dosyada değerleri görebilirsiniz. Bağlantılı şablonlar, gelişmiş senaryolar için çözümü hedeflenen bileşenlere bölebilir. Bu şablonları diğer senaryolar için kolayca yeniden kullanabilirsiniz.

Bağlı şablonlar kullanırken, dağıtım sırasında parametre değerleri alan bir ana şablon oluşturun. Ana Şablon bağlantılı tüm şablonları içerir ve gerektiğinde bu şablonlara değerleri geçirir.

Bir öğretici için bkz. [Öğreticisi: bağlı bir Azure Resource Manager şablonları oluşturma](./resource-manager-tutorial-create-linked-templates.md).

> [!NOTE]
> Bağlantılı veya iç içe şablonlar için yalnızca [artımlı](deployment-modes.md) Dağıtım modunu kullanabilirsiniz.
>

## <a name="deployments-resource"></a>Dağıtımlar kaynağı

Başka bir şablona bağlamak için ekleme bir **dağıtımları** ana şablon kaynağı.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
        "mode": "Incremental",
        <nested-template-or-external-template>
    }
  }
]
```

Dağıtım kaynağı için sağladığınız özellikleri, dış bir şablona bağlama veya iç içe bir ana şablon satır içi şablonunda göre değişir.

## <a name="nested-template"></a>İç içe geçmiş şablon

Ana şablon içinde şablonun içine yerleştirmek için kullanmanız **şablon** özelliği ve şablon söz dizimi belirtin.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "nestedTemplate",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[variables('storageName')]",
            "location": "West US",
            "kind": "StorageV2",
            "sku": {
                "name": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> İç içe geçmiş şablonlar için parametreleri veya iç içe geçmiş şablon içinde tanımlanan değişkenler kullanamazsınız. Parametreler ve değişkenler ana kullanabilirsiniz. Önceki örnekte `[variables('storageName')]` ana şablonu, iç içe geçmiş şablon değil bir değer alır. Bu kısıtlama, dış şablonları için geçerli değildir.
>
> İç içe yerleştirilmiş bir şablonda tanımlı iki kaynak ve bir kaynak diğerine bağlı olduğunda, bağımlılığın değeri yalnızca bağımlı kaynağın adıdır:
> ```json
> "dependsOn": [
>   "[variables('storageAccountName')]"
> ],
> ```
>
> İç içe yerleştirilmiş şablonda dağıttığınız bir kaynağın iç içe yerleştirilmiş bir şablonun çıktılar bölümünde `reference` işlevini kullanamazsınız. Dönüş değerleri dağıtılan kaynağın içinde iç içe geçmiş bir şablon için iç içe geçmiş şablon bağlantılı şablona dönüştürebilirsiniz.

İç içe geçmiş şablon için gerekli [aynı özellikleri](resource-group-authoring-templates.md) standart şablon olarak.

## <a name="external-template"></a>Dış şablon

Bir dış şablona bağlantı sağlamak için **Templatelink** özelliğini kullanın. Yerel bir dosya ya da yalnızca yerel ağınızda kullanılabilir olan dosya belirtemezsiniz. Ya da içeren bir URI değeri yalnızca sağlayabilir **http** veya **https**. Kaynak Yöneticisi şablona erişebilmelidir.

Bir seçenek bağlı şablonunuzu bir depolama hesabında yerleştirin ve bu öğe için bir URI kullanın oluşturmaktır.

Dış şablonunuz için parametreleri bir dış dosyada veya satır içi olarak sağlayabilirsiniz.

### <a name="external-parameters"></a>Dış parametreler

Bir dış parametre dosyası sağlarken, **Parameterslink** özelliğini kullanın:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion":"1.0.0.0"
      },
      "parametersLink": {
        "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion":"1.0.0.0"
      }
    }
  }
]
```

Sağlamaları gerekmez `contentVersion` şablonu veya parametreler özellik. Bir içerik sürümü değeri sağlamıyorsa şablonunun geçerli sürümünü dağıtılır. İçerik sürümü için bir değer belirtirseniz, bağlı şablonun sürümünde eşleşmelidir; Aksi takdirde, dağıtım, bir hata ile başarısız olur.

### <a name="inline-parameters"></a>Satır içi parametreler

Veya, parametre satır içi sağlayabilirsiniz. Satır içi parametre hem de bir bağlantı için bir parametre dosyası kullanamazsınız. Dağıtım bir hata ile başarısız olduğunda her ikisi de `parametersLink` ve `parameters` belirtilir.

Ana şablondan bağlantılı şablona bir değer geçirmek için, **Parameters** özelliğini kullanın.

```json
"resources": [
  {
     "type": "Microsoft.Resources/deployments",
     "apiVersion": "2018-05-01",
     "name": "linkedTemplate",
     "properties": {
       "mode": "Incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parameters": {
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
        }
     }
  }
]
```

## <a name="using-copy"></a>Kopyayı kullanma

İç içe geçmiş şablonla bir kaynağın birden çok örneğini oluşturmak için, **Microsoft. resources/dağıtımlar** kaynağı düzeyinde kopyalama öğesini ekleyin.

Aşağıdaki örnek şablon, kopyalamanın iç içe geçmiş bir şablonla nasıl kullanılacağını göstermektedir.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2018-05-01",
    "name": "[concat('nestedTemplate', copyIndex())]",
    // yes, copy works here
    "copy":{
      "name": "storagecopy",
      "count": 2
    },
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "kind": "StorageV2",
            "sku": {
              "name": "Standard_LRS"
            }
            // no, copy doesn't work here
            //"copy":{
            //  "name": "storagecopy",
            //  "count": 2
            //}
          }
        ]
      }
    }
  }
]
```

## <a name="using-variables-to-link-templates"></a>Şablonları bağlamak için değişkenleri kullanma

Önceki örneklerde şablon bağlantılara sabit kodlanmış URL'si değerleri gösterdi. Bu yaklaşım için basit bir şablon çalışabilir ancak büyük bir dizi modüler şablonu ile çalışırken de çalışmıyor. Bunun yerine, ana şablon için temel URL saklayan statik bir değişken oluşturun ve ardından dinamik olarak URL'ler için bu temel URL bağlı şablonlardan oluşturma. Bu yaklaşımın avantajı, kolayca taşıyabilir veya yalnızca ana şablondaki statik değişkeni değiştirmeniz gerekir çünkü şablon çatal içindir. Ana Şablon doğru bir URI'leri ayrıştırılmış şablon boyunca geçirir.

Aşağıdaki örnek, bağlı şablonların iki URL'lerini oluşturmak için temel URL kullanmayı gösterir (**sharedTemplateUrl** ve **vmTemplate**).

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Ayrıca [deployment()](resource-group-template-functions-deployment.md#deployment) temel URL için geçerli şablonu alıp, URL'yi diğer şablonlar için aynı konumda almak için kullanın. Bu yaklaşım, şablonu konumu değişikliklerinizi veya şablon dosyasında URL'leri Sabit kodlama kaçınmak istiyorsanız kullanışlıdır. TemplateLink özelliğindeki yalnızca bir URL ile bir uzak şablonuna bağlarken döndürülür. Yerel bir şablonu kullanıyorsanız, bu özellik kullanılabilir değil.

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="get-values-from-linked-template"></a>Bağlı şablondan değerleri alma

Bağlantılı bir şablondan bir çıkış değeri almak için özellik değeri gibi bir söz dizimi ile Al: `"[reference('deploymentName').outputs.propertyName.value]"`.

Bir çıkış özelliği bağlı şablonundan alınırken, özellik adı bir tire içeremez.

Aşağıdaki örnekler, bağlı bir şablona başvurmak ve bir çıkış değeri almak nasıl ekleyebileceğiniz gösterilmektedir. Bağlantılı şablon, basit bir ileti döndürür.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "greetingMessage": {
            "value": "Hello World",
            "type" : "string"
        }
    }
}
```

Ana Şablon dağıtır bağlı şablonun ve döndürülen değer alır. Ada göre dağıtım kaynağına başvurduğundan ve bağlantılı şablon tarafından döndürülen özelliğin adını kullanan dikkat edin.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
                    "contentVersion": "1.0.0.0"
                }
            }
        }
    ],
    "outputs": {
        "messageFromLinkedTemplate": {
            "type": "string",
            "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
        }
    }
}
```

Gibi diğer kaynak türlerini, bağlı şablonun ve diğer kaynaklar arasındaki bağımlılıkları da ayarlayabilirsiniz. Diğer kaynaklar bağlantılı şablondan bir çıkış değeri gerektirdiğinde, bağlantılı şablonun uygulamadan önce dağıtıldığından emin olun. Ya da diğer kaynaklara bağlı şablonun kullanır, bağlı şablonun önce dağıtılan diğer kaynakları emin olun.

Aşağıdaki örnek, bir genel IP adresi dağıtır ve kaynak Kimliğini döndüren bir şablon gösterir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
            "location": "eastus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "resourceID": {
            "type": "string",
            "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
        }
    }
}
```

Önceki şablondan genel IP adresini yük dengeleyici dağıtırken kullanmak için şablona bağladığınız ve bağımlılık dağıtım kaynağı ekleyin. Genel IP adresini yük dengeleyici üzerindeki bağlantılı şablondan çıkış değerine ayarlanır.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_name": {
            "defaultValue": "mylb",
            "type": "string"
        },
        "publicIPAddresses_name": {
            "defaultValue": "myip",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2018-11-01",
            "name": "[parameters('loadBalancers_name')]",
            "location": "eastus",
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundNatRules": [],
                "inboundNatPools": []
            },
            "dependsOn": [
                "linkedTemplate"
            ]
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "linkedTemplate",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters":{
                    "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
                }
            }
        }
    ]
}
```

## <a name="linked-and-nested-templates-in-deployment-history"></a>Dağıtım geçmişi bağlantılı ve iç içe şablonlar

Kaynak Yöneticisi her şablon dağıtım geçmişini de ayrı bir dağıtım olarak işler. Bu nedenle, ana şablon bağlantılı veya iç içe üç şablon ile dağıtım geçmişini görünür:

![Dağıtım geçmişi](./media/resource-group-linked-templates/deployment-history.png)

Dağıtımdan sonra çıkış değerleri almak için bu ayrı girişleri geçmişinde kullanabilirsiniz. Aşağıdaki şablonu bir ortak IP adresi oluşturur ve IP adresini verir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[parameters('publicIPAddresses_name')]",
            "location": "southcentralus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Static",
                "idleTimeoutInMinutes": 4,
                "dnsSettings": {
                    "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "returnedIPAddress": {
            "type": "string",
            "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
        }
    }
}
```

Önceki şablonda aşağıdaki şablon bağlantılar. Üç genel IP adresi oluşturur.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "copy": {
                "count": 3,
                "name": "ip-loop"
            },
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            }
        }
    ]
}
```

Dağıtımdan sonra aşağıdaki PowerShell betiğini çıkış değerleri alabilir:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Veya, bir Bash Kabuğu'nda Azure CLI betiği:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Bir dış şablonu güvenliğini sağlama

Bağlı şablonun dışarıdan kullanılabilir olsa da, genel kullanıma sunuldu olması gerekmez. Yalnızca depolama hesabı sahibi tarafından erişilebilir bir özel depolama hesabına şablonunuza ekleyebilirsiniz. Ardından, dağıtım sırasında erişim sağlamak için paylaşılan erişim imzası (SAS) belirteci oluşturun. URI için bağlı şablonun SAS belirtecini ekleyin. SAS belirteci dahil olmak üzere bağlı şablon URI'si, belirteci güvenli bir dize olarak geçirilen olsa bile, dağıtım işlemleri günlüğe kaydedilir. Etkilenme sınırlamak için bir belirteç sona erme tarihi ayarlayın.

Parametre dosyasını bir SAS belirteci üzerinden erişim için sınırlı olabilir.

Şu anda [Azure Storage güvenlik duvarının](../storage/common/storage-network-security.md)arkasındaki bir depolama hesabındaki bir şablona bağlayamazsınız.

Aşağıdaki örnek, bir şablona bağlanırken bir SAS belirteci geçirilecek gösterilmektedir:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

PowerShell'de, bir belirteç almak için kapsayıcı ve aşağıdaki komutları kullanarak şablonları dağıtabilirsiniz. Dikkat **containerSasToken** parametre şablonunda tanımlanır. **New-AzResourceGroupDeployment** komutunda bir parametre değildir.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

Azure CLI bir Bash kabuğunda için bir belirteç almak için kapsayıcı ve şablonları aşağıdaki kodla dağıtın:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name storagecontosotemplates \
    --query connectionString)
token=$(az storage container generate-sas \
    --name templates \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name parent.json \
    --output tsv \
    --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Örnek şablonları

Aşağıdaki örnekler, bağlı şablonların'ın yaygın kullanımları gösterir.

|Ana şablon  |Bağlantılı şablon |Açıklama  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[Bağlantılı şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Bağlantılı şablondan dizeyi döndürür. |
|[Genel IP adresine sahip yük dengeleyici](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[Bağlantılı şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Bağlantılı şablondan genel IP adresini getirir ve yük dengeleyici bu değeri ayarlar. |
|[Birden çok IP adresi](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [Bağlantılı şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Bağlantılı şablonunda birden fazla genel IP adresi oluşturur.  |

## <a name="next-steps"></a>Sonraki adımlar

* Bir öğreticiyi incelemek için bkz: [Öğreticisi: bağlı bir Azure Resource Manager şablonları oluşturma](./resource-manager-tutorial-create-linked-templates.md).
* Kaynaklarınız için dağıtım sırasını tanımlamaya hakkında bilgi edinmek için [Azure Resource Manager şablonlarında bağımlılık tanımlama](resource-group-define-dependencies.md).
* Bir kaynak tanımladığınız ancak pek çok örneğini oluşturma konusunda bilgi almak için bkz: [Azure Resource Manager'da kaynakları birden çok örneğini oluşturma](resource-group-create-multiple.md).
* Bir depolama hesabında bir şablon oluşturma ve bir SAS belirteci oluşturma adımları için bkz [kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](resource-group-template-deploy.md) veya [kaynakları Resource Manager şablonları ile dağıtma ve Azure CLI](resource-group-template-deploy-cli.md).
