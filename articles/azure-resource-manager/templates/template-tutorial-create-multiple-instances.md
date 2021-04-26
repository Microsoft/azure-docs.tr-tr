---
title: Birden çok kaynak örneği oluşturma
description: Birden çok Azure Kaynak örneği oluşturmak için Azure Resource Manager şablonu (ARM şablonu) oluşturmayı öğrenin.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: e669e27547633639a88674ffee499fb1d84facdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673962"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>Öğretici: ARM şablonlarıyla birden çok kaynak örneği oluşturma

Azure kaynağının birden çok örneğini oluşturmak için Azure Resource Manager şablonunuzda (ARM şablonu) yineleme yapmayı öğrenin. Bu öğreticide, bir şablonu değiştirerek üç depolama hesabı örneği oluşturacaksınız.

![Azure Resource Manager birden çok örnek diyagramı oluşturuyor](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Hızlı başlangıç şablonunu açma
> * Şablonu düzenleme
> * Şablonu dağıtma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

Kaynak kopyasını içeren Microsoft Learn bir modül için bkz. [GELIŞMIŞ ARM şablon özelliklerini kullanarak karmaşık bulut dağıtımlarını yönetme](/learn/modules/manage-deployments-advanced-arm-template-features/).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

* Visual Studio Code ve Resource Manager Araçları uzantısı. Bkz. [hızlı başlangıç: VISUAL STUDIO Code ARM şablonları oluşturma](quickstart-create-templates-use-visual-studio-code.md).

## <a name="open-a-quickstart-template"></a>Hızlı başlangıç şablonunu açma

[Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/) , ARM şablonları için bir depodur. Sıfırdan bir şablon oluşturmak yerine örnek bir şablon bulabilir ve bunu özelleştirebilirsiniz. Bu hızlı başlangıçta kullanılan şablon [Standart depolama hesabı oluşturma](https://azure.microsoft.com/resources/templates/101-storage-account-create/) olarak adlandırılır. Şablon, Azure Depolama hesabı kaynağını tanımlar.

1. Visual Studio Code **Dosya**  >  **Aç dosya**' yı seçin.
1. **Dosya adı**’na şu URL’yi yapıştırın:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Dosyayı açmak için **Aç**’ı seçin.
1. `Microsoft.Storage/storageAccounts`Şablonda tanımlı bir kaynak vardır. Şablonu, [şablon başvurusu](/azure/templates/Microsoft.Storage/storageAccounts) ile karşılaştırın. Özelleştirme yapmadan önce şablon hakkında bazı temel bilgileri almak faydalı olur.
1. Dosyayı   >  yerel bilgisayarınızaazuredeploy.jsolarak kaydetmek için dosya **farklı kaydet** _'_ i seçin.

## <a name="edit-the-template"></a>Şablonu düzenleme

Var olan şablon, temel şifrelenmemiş depolama hesabı oluşturur. Şablonu özelleştirerek üç depolama hesabı oluşturacaksınız.

Visual Studio Code’dan aşağıdaki dört değişikliği yapın:

![Azure Resource Manager birden çok örnek oluşturur](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Depolama hesabı kaynak tanımına bir `copy` öğesi ekleyin. `copy`Öğesinde, bu döngü için yineleme ve değişken sayısını belirtirsiniz. Sayı değeri pozitif bir tamsayı olmalıdır ve 800’ü aşamaz.
2. `copyIndex()` İşlevi, döngüde geçerli yinelemeyi döndürür. Dizini ad ön eki olarak kullanırsınız. `copyIndex()` sıfır tabanlıdır. Dizin değerini kaydırmak için, işlevinde bir değer geçirebilirsiniz `copyIndex()` . Örneğin, `copyIndex(1)`.
3. `variables`Artık kullanılmadığından öğeyi silin.
4. Öğeyi silin `outputs` . Artık gerekli değildir.

Tamamlanan şablon aşağıdaki gibi görünür:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ]
}
```

Birden çok örnek oluşturma hakkında daha fazla bilgi için bkz. [ARM şablonlarında kaynak yinelemesi](./copy-resources.md)

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. [Azure Cloud Shell](https://shell.azure.com) oturum açın

1. Sol üst köşedeki **PowerShell** veya **Bash** (CLI için) öğesini seçerek tercih ettiğiniz ortamı seçin. Geçiş yaptığınızda kabuğun yeniden başlatılması gerekir.

    ![Dosyayı karşıya yükleme Cloud Shell Azure portal](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. **Dosyaları karşıya yükle/indir**'i seçin ve sonra da **Karşıya Yükle**'yi seçin. Önceki ekran görüntüsüne bakın. Önceki bölümde kaydettiğiniz dosyayı seçin. Dosyayı karşıya yükledikten sonra, `ls` `cat` dosyanın başarıyla karşıya yüklendiğini doğrulamak için komutunu ve komutunu kullanabilirsiniz.

1. Cloud Shell aşağıdaki komutları çalıştırın. PowerShell kodunu veya CLI kodunu gösteren sekmeyi seçin.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

Başarılı bir şablon dağıtımı sonrasında, belirtilen kaynak grubunda oluşturulan üç depolama hesabını görüntüleyebilirsiniz. Depolama hesabı adlarını, şablondaki ad tanımıyla karşılaştırın.

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"

Get-AzStorageAccount -ResourceGroupName $resourceGroupName
Write-Host "Press [ENTER] to continue ..."
```

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.  Kaynak grubunda toplam üç kaynak görürsünüz.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, birden çok depolama hesabı örneği oluşturmayı öğrendiniz. Bir sonraki öğreticide, birden fazla kaynağa ve birden çok kaynak türüne sahip bir şablon geliştireceksiniz. Bazı kaynakların bağımlı kaynakları vardır.

> [!div class="nextstepaction"]
> [Bağımlı kaynaklar oluşturma](./template-tutorial-create-templates-with-dependent-resources.md)
