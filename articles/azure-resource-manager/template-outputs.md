---
title: Şablonlarda çıkış çıkışları
description: Azure Resource Manager şablonunda çıkış değerlerinin nasıl tanımlanacağını açıklar.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: b4c652d71436202b9b6e551f9c582e5c98508259
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149173"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Azure Resource Manager şablondaki çıktılar

Bu makalede, Azure Resource Manager şablonunuzda çıkış değerlerinin nasıl tanımlanacağı açıklanmaktadır. Dağıtılan kaynaklardan değer döndürihtiyacınız olduğunda çıktıları kullanırsınız.

## <a name="define-output-values"></a>Çıkış değerlerini tanımla

Aşağıdaki örnek, kaynak kimliği için bir genel IP adresi döndürülecek gösterilmektedir:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Koşullu çıkış

Çıktılar bölümünde, koşullu bir değer döndürebilirsiniz. Genellikle, bir kaynağı [koşullu olarak dağıttığınız](conditional-resource-deployment.md) durumunda çıkışların durumunu kullanırsınız. Aşağıdaki örnek, bir genel IP adresi için kaynak KIMLIĞININ, yeni bir birinin dağıtılıp dağıtıldığına göre nasıl koşullu olarak döndürülüp döndürülmeyeceğini göstermektedir:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Koşullu çıkışın basit bir örneği için bkz. [koşullu çıkış şablonu](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="linked-templates"></a>Bağlı şablonlar

Bağlı bir şablondan çıkış değerini almak için, üst şablondaki [başvuru](resource-group-template-functions-resource.md#reference) işlevini kullanın. Üst şablondaki sözdizimi şöyledir:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Bir çıkış özelliği bağlı şablonundan alınırken, özellik adı bir tire içeremez.

Aşağıdaki örnek, bir yük dengeleyicide, bağlantılı şablondan bir değer alarak IP adresinin nasıl ayarlanacağını gösterir.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Kullanamazsınız `reference` çıktılar bölümünü işlevinde bir [iç içe geçmiş şablon](resource-group-linked-templates.md#nested-template). Dönüş değerleri dağıtılan kaynağın içinde iç içe geçmiş bir şablon için iç içe geçmiş şablon bağlantılı şablona dönüştürebilirsiniz.

## <a name="get-output-values"></a>Çıkış değerlerini al

Dağıtım başarılı olduğunda, çıkış değerleri dağıtım sonuçlarında otomatik olarak döndürülür.

Dağıtım geçmişinden çıkış değerleri almak için, komut dosyası kullanabilirsiniz.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group deployment show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Örnek şablonları

Aşağıdaki örneklerde, çıkışları kullanmaya yönelik senaryolar gösterilmektedir.

|Şablon  |Açıklama  |
|---------|---------|
|[Değişkenleri kopyalayın](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Karmaşık değişkenler oluşturur ve bu değerleri çıkarır. Tüm kaynakları dağıtmaz. |
|[Genel IP adresi](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Genel bir IP adresi oluşturur ve kaynak kimliği çıkarır |
|[Yük Dengeleyici](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Yukarıdaki şablonu bağlar. Kaynak Kimliği, yük dengeleyici oluştururken çıktısında kullanır. |

## <a name="next-steps"></a>Sonraki adımlar

* Çıktılar için kullanılabilen özellikler hakkında bilgi edinmek için bkz. [Azure Resource Manager şablonlarının yapısını ve sözdizimini anlayın](resource-group-authoring-templates.md).
* Çıktılar oluşturma hakkında öneriler için bkz. [en iyi yöntemler-çıktılar](template-best-practices.md#outputs).
