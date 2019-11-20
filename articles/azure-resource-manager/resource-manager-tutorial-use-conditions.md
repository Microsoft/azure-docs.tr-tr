---
title: Şablonlarda koşul kullanma
description: Azure kaynaklarını koşullara bağlı olarak dağıtmayı öğrenin. Yeni bir kaynağın nasıl dağıtılacağını veya var olan bir kaynağı nasıl kullanacağınızı gösterir.
author: mumian
ms.date: 05/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fe0a30ab3931be0473ea4f067d6a02797fec1e4d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150394"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Öğretici: Azure Resource Manager şablonlarında koşulları kullanma

Azure kaynaklarını koşullara bağlı olarak dağıtmayı öğrenin.

[Kaynak dağıtım sırasını ayarlama](./resource-manager-tutorial-create-templates-with-dependent-resources.md) öğreticisinde bir sanal makine, bir sanal ağ ve bir depolama hesabı dahil olmak üzere ek birkaç bağımlı kaynak oluşturmuştunuz. Her defasında yeni depolama hesabı oluşturmak yerine kullanıcıların yeni depolama hesabı oluşturma veya var olan depolama hesabını kullanma arasında seçim yapmasını sağlayacaksınız. Bu hedefe ulaşmak için ek bir parametre tanımlamanız gerekir. Parametrenin değeri "new" olduğunda yeni bir depolama hesabı oluşturulur. Aksi halde, belirtilen ada sahip mevcut bir depolama hesabı kullanılır.

![Kaynak Yöneticisi şablonu kullanım koşulu diyagramı](./media/resource-manager-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Hızlı başlangıç şablonunu açma
> * Şablonu değiştirme
> * Şablonu dağıtma
> * Kaynakları temizleme

Bu öğretici yalnızca koşulların kullanıldığı temel bir senaryoyu ele alır. Daha fazla bilgi için bkz.

* [Şablon dosyası yapısı: koşul](conditional-resource-deployment.md).
* [Azure Resource Manager şablonunda bir kaynağı koşullu olarak dağıtın](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Şablon işlevi: Eğer](./resource-group-template-functions-logical.md#if).
* [Azure Resource Manager şablonları için karşılaştırma işlevleri](./resource-group-template-functions-comparison.md)

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

* [Resource Manager Araçları uzantısı](https://code.visualstudio.com/) içeren [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Güvenliği artırmak istiyorsanız sanal makine yönetici hesabı için oluşturulmuş bir parola kullanın. Parola oluşturma örneği aşağıda verilmiştir:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault şifreleme anahtarları ve diğer gizli dizileri korumak üzere tasarlanmıştır. Daha fazla bilgi için bkz. [Öğretici: Azure Key Vault'u Resource Manager şablonu dağıtımıyla tümleştirme](./resource-manager-tutorial-use-key-vault.md). Ayrıca parolanızı üç ayda bir güncelleştirmenizi öneririz.

## <a name="open-a-quickstart-template"></a>Hızlı başlangıç şablonunu açma

Azure Hızlı Başlangıç Şablonları, Resource Manager şablonları için bir depolama alanıdır. Sıfırdan bir şablon oluşturmak yerine örnek bir şablon bulabilir ve bunu özelleştirebilirsiniz. Bu öğreticide kullanılan şablonun adı: [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Basit bir Windows sanal makinesi dağıtma).

1. Visual Studio Code’dan **Dosya**>**Dosya Aç**’ı seçin.
2. **Dosya adı**’na şu URL’yi yapıştırın:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Dosyayı açmak için **Aç**’ı seçin.
4. Şablonun tanımladığı beş kaynak vardır:

   * `Microsoft.Storage/storageAccounts`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Şablonu özelleştirmeden önce temel noktaları kavramak faydalı olacaktır.
5. **Dosya**>**Farklı Kaydet**'i seçerek dosyanın bir kopyasını yerel bilgisayarınıza **azuredeploy.json** adıyla kaydedin.

## <a name="modify-the-template"></a>Şablonu değiştirme

Var olan şablonda iki değişiklik yapın:

* Depolama hesabı ad parametresi ekleyin. Kullanıcılar ya yeni bir depolama hesabı adını ya da var olan bir depolama hesabı adını belirleyebilir.
* **newOrExisting** adlı yeni bir parametre ekleyin. Dağıtım, yeni bir depolama hesabı mı oluşturulacağını yoksa mevcut bir depolama hesabını mı kullanacağınızı öğrenmek için bu parametreyi kullanır.

Değişiklik yapmak için aşağıdaki adımları izleyin:

1. **azuredeploy.json** dosyasını Visual Studio Code ile açın.
2. Bu üç **değişkeni (' storageAccountName ')** tüm şablonda **Parametreler (' storageAccountName ')** ile değiştirin.
3. Aşağıdaki değişken tanımını kaldırın:

    ![Kaynak Yöneticisi şablonu kullanım koşulu diyagramı](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

4. Şablona aşağıdaki iki parametreyi ekleyin:

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    Güncelleştirilmiş parametre tanımı şu şekilde görünür:

    ![Resource Manager kullanım koşulu](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Depolama hesabı tanımının başına aşağıdaki satırı ekleyin.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    Koşul, **newOrExisting** adlı parametrenin değerini denetler. Parametre değeri **new** ise dağıtım, depolama hesabını oluşturur.

    Güncelleştirilmiş depolama hesabı tanımı şu şekilde görünür:

    ![Resource Manager kullanım koşulu](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Sanal makine kaynak tanımının **Storageuri** özelliğini aşağıdaki değerle güncelleştirin:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Farklı bir kaynak grubu altındaki var olan bir depolama hesabını kullandığınızda bu değişiklik gereklidir.

7. Değişiklikleri kaydedin.

## <a name="deploy-the-template"></a>Şablonu dağıtma

Cloud Shell 'i açmak için [şablonu dağıtma](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) bölümündeki yönergeleri izleyin ve düzeltilmiş şablonu karşıya yükleyin ve ardından şablonu dağıtmak Için aşağıdaki PowerShell betiğini çalıştırın.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin `
    -adminPassword $vmPassword `
    -dnsLabelPrefix $dnsLabelPrefix `
    -storageAccountName $storageAccountName `
    -newOrExisting $newOrExisting `
    -TemplateFile "$HOME/azuredeploy.json"
```

> [!NOTE]
> **newOrExisting** değeri **new** olduğunda ancak belirtilen depolama hesabı adına sahip olan bir depolama hesabı mevcut olduğunda dağıtım başarısız olur.

**Neworexıting** ile "var" olarak ayarlanmış başka bir dağıtım yapmayı deneyin ve var olan bir depolama hesabı belirtin. Depolama hesabını önceden oluşturmak için bkz. [Depolama hesabı oluşturma](../storage/common/storage-quickstart-create-account.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin. Kaynak grubunu silmek için, Cloud Shell 'i açmak için **dene** ' yi seçin. PowerShell betiğini yapıştırmak için kabuk bölmesine sağ tıklayın ve ardından **Yapıştır**' ı seçin.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the same resource group name you used in the last procedure"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide kullanıcılara yeni depolama hesabı oluşturma ve var olan depolama hesabını kullanma arasında seçim yapma imkanı sunan bir şablon geliştirdiniz. Azure Key Vault'tan gizli dizi alma ve gizli dizileri şablon dağıtımı sırasında parola olarak kullanma hakkında bilgi için bkz.:

> [!div class="nextstepaction"]
> [Key Vault'u şablon dağıtımıyla tümleştirme](./resource-manager-tutorial-use-key-vault.md)
