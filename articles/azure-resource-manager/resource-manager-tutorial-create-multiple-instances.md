---
title: Birden çok kaynak örneği oluşturma
description: Birden çok Azure kaynağı örneği oluşturmak için bir Azure Resource Manager şablonu oluşturmayı öğrenin.
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 73d8abcbc6e902855592f4bf3ca0373b8582130d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150442"
---
# <a name="tutorial-create-multiple-resource-instances-with-resource-manager-templates"></a>Öğretici: Resource Manager şablonlarıyla birden çok kaynak örneği oluşturma

Bir Azure Kaynağı’nın birden çok örneğini oluşturmak için Azure Resource Manager şablonunuzda yineleme yapmayı öğrenin. Bu öğreticide, bir şablonu değiştirerek üç depolama hesabı örneği oluşturacaksınız.

![Azure Resource Manager birden çok örnek diyagramı oluşturuyor](./media/resource-manager-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Hızlı başlangıç şablonunu açma
> * Şablonu düzenleme
> * Şablonu dağıtma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

* [Resource Manager Araçları uzantısı](https://code.visualstudio.com/) içeren [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Hızlı başlangıç şablonunu açma

[Azure Hızlı Başlangıç Şablonları](https://azure.microsoft.com/resources/templates/), Resource Manager şablonları için bir depolama alanıdır. Sıfırdan bir şablon oluşturmak yerine örnek bir şablon bulabilir ve bunu özelleştirebilirsiniz. Bu hızlı başlangıçta kullanılan şablon [Standart depolama hesabı oluşturma](https://azure.microsoft.com/resources/templates/101-storage-account-create/) olarak adlandırılır. Şablon, Azure Depolama hesabı kaynağını tanımlar.

1. Visual Studio Code’dan **Dosya**>**Dosya Aç**’ı seçin.
2. **Dosya adı**’na şu URL’yi yapıştırın:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Dosyayı açmak için **Aç**’ı seçin.
4. Şablonda tanımlı bir 'Microsoft.Storage/storageAccounts' kaynağı bulunur. Şablonu, [şablon başvurusu](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts) ile karşılaştırın. Şablonu özelleştirmeden önce temel noktaları kavramak faydalı olacaktır.
5. Dosyayı yerel bilgisayarınıza **azuredeploy.json** olarak kaydetmek için >Dosya**Farklı Kaydet**’i seçin.

## <a name="edit-the-template"></a>Şablonu düzenleme

Var olan şablon, temel şifrelenmemiş depolama hesabı oluşturur. Şablonu özelleştirerek üç depolama hesabı oluşturacaksınız.  

Visual Studio Code’dan aşağıdaki dört değişikliği yapın:

![Azure Resource Manager birden çok örnek oluşturur](./media/resource-manager-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Depolama hesabı kaynak tanımına bir `copy` öğesi ekleyin. Kopyalama öğesinde, bu döngü için yineleme sayısı ve bir değişken belirtin. Sayı değeri pozitif bir tamsayı olmalıdır ve 800’ü aşamaz.
2. `copyIndex()` İşlevi, döngüde geçerli yinelemeyi döndürür. Dizini ad ön eki olarak kullanırsınız. `copyIndex()` sıfır tabanlıdır. Dizin değerini kaydırmak için copyIndex () işlevine bir değer geçirebilirsiniz. Örneğin, *copyIndex(1)* .
3. Artık kullanılmadığı için **değişkenler** öğesini silin.
4. **Çıktılar** öğesini silin. Artık buna ihtiyacınız yoktur.

Tamamlanan şablon aşağıdaki gibi görünür:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

Birden çok örnek oluşturma hakkında daha fazla bilgi için, bkz. [Azure Resource Manager Şablonları’nda bir kaynağın veya özelliğin birden çok örneğini dağıtma](./resource-group-create-multiple.md)

## <a name="deploy-the-template"></a>Şablonu dağıtma

Dağıtım yordamı için Visual Studio Code hızlı başlangıçta [Şablonu dağıtma](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) bölümüne bakın.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Üç depolama hesabının tümünü listelemek için --ad parametresini atlayın:

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Get-AzStorageAccount -ResourceGroupName $resourceGroupName
```

---

Depolama hesabı adlarını, şablondaki ad tanımıyla karşılaştırın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.  Kaynak grubundaki toplam altı kaynak görüyor olmalısınız.
4. Üstteki menüden **Kaynak grubunu sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, birden çok depolama hesabı örneği oluşturmayı öğrendiniz.  Bir sonraki öğreticide, birden fazla kaynağa ve birden çok kaynak türüne sahip bir şablon geliştireceksiniz. Bazı kaynakların bağımlı kaynakları vardır.

> [!div class="nextstepaction"]
> [Bağımlı kaynaklar oluşturma](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
