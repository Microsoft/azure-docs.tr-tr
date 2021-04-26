---
title: Şablonlarda koşul kullanma
description: Azure kaynaklarını koşullara bağlı olarak dağıtmayı öğrenin. Yeni bir kaynağın nasıl dağıtılacağını veya var olan bir kaynağı nasıl kullanacağınızı gösterir.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4affc2add2822702c1d5395f81efe01eeedf448b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98696032"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>Öğretici: ARM şablonlarında koşul kullanma

Azure Resource Manager şablonundaki koşullara göre Azure kaynaklarını dağıtmayı öğrenin (ARM şablonu).

[Kaynak dağıtım sırasını ayarlama](./template-tutorial-create-templates-with-dependent-resources.md) öğreticisinde bir sanal makine, bir sanal ağ ve bir depolama hesabı dahil olmak üzere ek birkaç bağımlı kaynak oluşturmuştunuz. Her defasında yeni depolama hesabı oluşturmak yerine kullanıcıların yeni depolama hesabı oluşturma veya var olan depolama hesabını kullanma arasında seçim yapmasını sağlayacaksınız. Bu hedefe ulaşmak için ek bir parametre tanımlamanız gerekir. Parametresinin değeri **Yeni** ise, yeni bir depolama hesabı oluşturulur. Aksi halde, belirtilen ada sahip mevcut bir depolama hesabı kullanılır.

![Kaynak Yöneticisi şablonu kullanım koşulu diyagramı](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Hızlı başlangıç şablonunu açma
> * Şablonu değiştirme
> * Şablonu dağıtma
> * Kaynakları temizleme

Bu öğretici yalnızca koşulların kullanıldığı temel bir senaryoyu ele alır. Daha fazla bilgi için bkz.

* [Şablon dosyası yapısı: koşul](conditional-resource-deployment.md).
* [BIR ARM şablonunda bir kaynağı koşullu olarak dağıtın](/azure/architecture/guide/azure-resource-manager/advanced-templates/conditional-deploy).
* [Şablon işlevi: Eğer](./template-functions-logical.md#if).
* [ARM şablonları için karşılaştırma işlevleri](./template-functions-comparison.md)

Koşulları içeren Microsoft Learn bir modül için bkz. [GELIŞMIŞ ARM şablon özelliklerini kullanarak karmaşık bulut dağıtımlarını yönetme](/learn/modules/manage-deployments-advanced-arm-template-features/).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

* Visual Studio Code ve Resource Manager Araçları uzantısı. Bkz. [hızlı başlangıç: VISUAL STUDIO Code ARM şablonları oluşturma](quickstart-create-templates-use-visual-studio-code.md).
* Güvenliği artırmak istiyorsanız sanal makine yönetici hesabı için oluşturulmuş bir parola kullanın. Parola oluşturma örneği aşağıda verilmiştir:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault şifreleme anahtarları ve diğer gizli dizileri korumak üzere tasarlanmıştır. Daha fazla bilgi için bkz. [öğretici: ARM şablon dağıtımında Azure Key Vault tümleştirme](./template-tutorial-use-key-vault.md). Ayrıca parolanızı üç ayda bir güncelleştirmenizi öneririz.

## <a name="open-a-quickstart-template"></a>Hızlı başlangıç şablonunu açma

Azure hızlı başlangıç şablonları, ARM şablonları için bir depodur. Sıfırdan bir şablon oluşturmak yerine örnek bir şablon bulabilir ve bunu özelleştirebilirsiniz. Bu öğreticide kullanılan şablonun adı: [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Basit bir Windows sanal makinesi dağıtma).

1. Visual Studio Code **Dosya**  >  **Aç dosya**' yı seçin.
1. **Dosya adı**’na şu URL’yi yapıştırın:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Dosyayı açmak için **Aç**’ı seçin.
1. Şablon tarafından tanımlanan altı kaynak vardır:

   * [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft. Network/Publicıpaddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft. Network/NetworkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

    Şablonu özelleştirmeden önce şablon başvurusunu gözden geçirmeniz faydalı olur.

1. Dosyanın   >  bir kopyasını yerel bilgisayarınıza _azuredeploy.js_ adı ile kaydetmek için dosya **farklı kaydet** ' i seçin.

## <a name="modify-the-template"></a>Şablonu değiştirme

Var olan şablonda iki değişiklik yapın:

* Depolama hesabı ad parametresi ekleyin. Kullanıcılar ya yeni bir depolama hesabı adını ya da var olan bir depolama hesabı adını belirleyebilir.
* Adlı yeni bir parametre ekleyin `newOrExisting` . Dağıtım, yeni bir depolama hesabı mı oluşturulacağını yoksa mevcut bir depolama hesabını mı kullanacağınızı öğrenmek için bu parametreyi kullanır.

Değişiklik yapmak için aşağıdaki adımları izleyin:

1. _azuredeploy.json_ dosyasını Visual Studio Code ile açın.
1. `variables('storageAccountName')`Tüm şablonda üç ile değiştirin `parameters('storageAccountName')` .
1. Aşağıdaki değişken tanımını kaldırın:

    ![Kaldırmanız gereken değişken tanımlarını vurgulayan ekran görüntüsü.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

1. Parametreler bölümünün başlangıcına aşağıdaki iki parametreyi ekleyin:

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

    Visual Studio Code şablonu biçimlendirmek için alt + SHIFT + F tuşlarına basın.

    Güncelleştirilmiş parametre tanımı şu şekilde görünür:

    ![Resource Manager kullanım koşulu](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

1. Depolama hesabı tanımının başına aşağıdaki satırı ekleyin.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    Koşul, parametresinin değerini denetler `newOrExisting` . Parametre değeri **new** ise dağıtım, depolama hesabını oluşturur.

    Güncelleştirilmiş depolama hesabı tanımı şu şekilde görünür:

    ![Güncelleştirilmiş depolama hesabı tanımını gösteren ekran görüntüsü.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
1. `storageUri`Sanal makine kaynak tanımının özelliğini aşağıdaki değerle güncelleştirin:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Farklı bir kaynak grubu altındaki var olan bir depolama hesabını kullandığınızda bu değişiklik gereklidir.

1. Değişiklikleri kaydedin.

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. [Azure Cloud Shell](https://shell.azure.com) oturum açın

1. Sol üst köşedeki **PowerShell** veya **Bash** (CLI için) öğesini seçerek tercih ettiğiniz ortamı seçin. Geçiş yaptığınızda kabuğun yeniden başlatılması gerekir.

    ![Dosyayı karşıya yükleme Cloud Shell Azure portal](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. **Dosyaları karşıya yükle/indir**'i seçin ve sonra da **Karşıya Yükle**'yi seçin. Önceki ekran görüntüsüne bakın. Önceki bölümde kaydettiğiniz dosyayı seçin. Dosyayı karşıya yükledikten sonra, `ls` `cat` dosyanın başarıyla karşıya yüklendiğini doğrulamak için komutunu ve komutunu kullanabilirsiniz.

1. Şablonu dağıtmak için aşağıdaki PowerShell betiğini çalıştırın.

    > [!IMPORTANT]
    > Depolama hesabı adının Azure’da benzersiz olması gerekir. Ad yalnızca küçük harf veya rakam içermelidir. Bu, 24 karakterden uzun olamaz. Depolama hesabı adı, **Mağaza** eklenmiş proje adıdır. Proje adının ve oluşturulan depolama hesabı adının depolama hesabı adı gereksinimlerini karşıladığından emin olun.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
    $newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
    $location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
    $vmAdmin = Read-Host -Prompt "Enter the admin username"
    $vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $vmAdmin `
        -adminPassword $vmPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -storageAccountName $storageAccountName `
        -newOrExisting $newOrExisting `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    > [!NOTE]
    > Dağıtım `newOrExisting` **Yeni** ise, ancak depolama hesabı adı belirtilen depolama hesabı zaten varsa, dağıtım başarısız olur.

Mevcut olarak ayarlanmış başka bir dağıtım yapmayı deneyin `newOrExisting` ve var olan bir depolama hesabı belirtin.  Depolama hesabını önceden oluşturmak için bkz. [Depolama hesabı oluşturma](../../storage/common/storage-account-create.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin. Kaynak grubunu silmek için **dene** ' yi seçerek Cloud Shell açın. PowerShell betiğini yapıştırmak için kabuk bölmesine sağ tıklayın ve ardından **Yapıştır**' ı seçin.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide kullanıcılara yeni depolama hesabı oluşturma ve var olan depolama hesabını kullanma arasında seçim yapma imkanı sunan bir şablon geliştirdiniz. Azure Key Vault'tan gizli dizi alma ve gizli dizileri şablon dağıtımı sırasında parola olarak kullanma hakkında bilgi için bkz.:

> [!div class="nextstepaction"]
> [Key Vault'u şablon dağıtımıyla tümleştirme](./template-tutorial-use-key-vault.md)
