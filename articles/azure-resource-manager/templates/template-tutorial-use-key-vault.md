---
title: Şablonlarda Azure Key Vault kullanma
description: Azure Resource Manager şablonu (ARM şablonu) dağıtımı sırasında güvenli parametre değerlerini geçirmek için Azure Key Vault nasıl kullanacağınızı öğrenin.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 388996dc0054192f6d9f3c87e11ca1d15e8a85e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703894"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>Öğretici: Azure Key Vault'u ARM şablonunuzun dağıtımıyla tümleştirme

Bir Azure anahtar kasasından gizli dizileri alma ve bir Azure Resource Manager şablonu dağıtırken (ARM şablonu) gizli dizileri parametre olarak geçirme hakkında bilgi edinin. Yalnızca kendi Anahtar Kasası KIMLIĞINE başvurduğundan parametre değeri hiçbir şekilde gösterilmez. Anahtar Kasası gizliliğine statik bir KIMLIK veya dinamik KIMLIK kullanarak başvurabilirsiniz. Bu öğretici statik bir KIMLIK kullanır. Statik KIMLIK yaklaşımıyla, şablon dosyasında değil, şablon parametre dosyasındaki anahtar kasasına başvurun. Her iki yaklaşım hakkında daha fazla bilgi için bkz. [dağıtım sırasında güvenli parametre değeri geçirmek için Azure Key Vault kullanma](./key-vault-parameter.md).

[Kaynak dağıtım sırasını ayarla](./template-tutorial-create-templates-with-dependent-resources.md) öğreticisinde, bir sanal makıne (VM) oluşturursunuz. VM Yönetici Kullanıcı adı ve parolasını sağlamanız gerekir. Parolayı sağlamak yerine, parolayı bir Azure Anahtar Kasası 'nda ön depoya alabilir ve ardından, dağıtım sırasında bu parolayı anahtar kasasından almak için şablonu özelleştirebilirsiniz.

![Bir Kaynak Yöneticisi şablonunun anahtar kasası ile tümleştirilmesini gösteren diyagram](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Anahtar Kasası hazırlama
> * Hızlı başlangıç şablonunu açma
> * Parametre dosyasını düzenleme
> * Şablonu dağıtma
> * Dağıtımı doğrulama
> * Kaynakları temizleme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

Anahtar kasasından güvenli bir değer kullanan bir Microsoft Learn modülü için bkz. [GELIŞMIŞ ARM şablon özelliklerini kullanarak karmaşık bulut dağıtımlarını yönetme](/learn/modules/manage-deployments-advanced-arm-template-features/).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

* Visual Studio Code ve Resource Manager Araçları uzantısı. Bkz. [hızlı başlangıç: VISUAL STUDIO Code ARM şablonları oluşturma](quickstart-create-templates-use-visual-studio-code.md).
* Güvenliği artırmak için, VM yönetici hesabı için oluşturulan bir parola kullanın. Parola oluşturmak için bir örnek aşağıda verilmiştir:

    ```console
    openssl rand -base64 32
    ```

    Oluşturulan parolanın VM parola gereksinimlerini karşıladığını doğrulayın. Her Azure hizmetinin parola gereksinimleri farklıdır. VM parola gereksinimleri için bkz. [VM oluştururken parola gereksinimleri nelerdir?](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Anahtar Kasası hazırlama

Bu bölümde, bir Anahtar Kasası oluşturup buna bir gizli dizi ekleyerek, şablonunuzu dağıtırken gizli dizi elde edebilirsiniz. Anahtar Kasası oluşturmanın birçok yolu vardır. Bu öğreticide, [ARM şablonunu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json)dağıtmak için Azure PowerShell kullanırsınız. Bu şablon iki şeyi yapar:

* Özelliği etkin olan bir Anahtar Kasası oluşturur `enabledForTemplateDeployment` . Şablon dağıtım işleminin anahtar kasasında tanımlanan gizli anahtarlara erişebilmesi için bu özelliğin *true* olması gerekir.
* Anahtar kasasına gizli dizi ekler. Gizli dizi VM yönetici parolasını depolar.

> [!NOTE]
> Sanal makine şablonunu dağıtan Kullanıcı olarak, anahtar kasasının sahibi veya katkıda bulunanı değilseniz, sahip veya katkıda bulunan, `Microsoft.KeyVault/vaults/deploy/action` Anahtar Kasası için izin erişimi vermelidir. Daha fazla bilgi için bkz. [dağıtım sırasında güvenli bir parametre değeri geçirmek için Azure Key Vault kullanma](./key-vault-parameter.md).

Aşağıdaki Azure PowerShell betiğini çalıştırmak için, Azure Cloud Shell açmak üzere **deneyin** ' i seçin. Betiği yapıştırmak için kabuk bölmesine sağ tıklayın ve ardından **Yapıştır**' ı seçin.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue

Write-Host "Press [ENTER] to continue ..."
```

> [!IMPORTANT]
> * Kaynak grubu adı proje adıdır, ancak buna **RG** eklenmiş olur. [Bu öğreticide oluşturduğunuz kaynakları temizlemeyi](#clean-up-resources)kolaylaştırmak için, bir [sonraki şablonu dağıtırken](#deploy-the-template)aynı proje adı ve kaynak grubu adını kullanın.
> * Gizli dizi için varsayılan ad **Vmadminpassword**' dır. Şablonda sabit kodlanmış.
> * Şablonu parolayı almak üzere etkinleştirmek için, Anahtar Kasası için **şablon dağıtımı Azure Resource Manager erişimi etkinleştir** adlı bir erişim ilkesi etkinleştirmeniz gerekir. Bu ilke şablonda etkinleştirilmiştir. Erişim ilkesi hakkında daha fazla bilgi için bkz. [Anahtar Kasası ve gizli dizileri dağıtma](./key-vault-parameter.md#deploy-key-vaults-and-secrets).

Şablon, adlı bir çıkış değerine sahiptir `keyVaultId` . Bu KIMLIĞI, daha sonra öğreticideki gizli değeri almak için gizli adıyla birlikte kullanacaksınız. Kaynak KIMLIĞI biçimi:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

KIMLIĞI kopyalayıp yapıştırdığınızda, birden çok satıra ayrılabilir. Satırları birleştirin ve ek boşlukları kırpın.

Dağıtımı doğrulamak için, parolayı şifresiz metin olarak almak için aynı kabuk bölmesinde aşağıdaki PowerShell komutunu çalıştırın. Komutu, `$keyVaultName` önceki PowerShell betiğinden tanımlanan değişkenini kullandığından, yalnızca aynı kabuk oturumunda çalışacaktır.

```azurepowershell
$secret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword"
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

Artık bir anahtar kasası ve gizli anahtarı hazırladınız. Aşağıdaki bölümlerde, dağıtım sırasında gizli anahtarı almak için mevcut bir şablonu nasıl özelleştireceğiniz gösterilmektedir.

## <a name="open-a-quickstart-template"></a>Hızlı başlangıç şablonunu açma

Azure hızlı başlangıç şablonları, ARM şablonları için bir depodur. Sıfırdan bir şablon oluşturmak yerine örnek bir şablon bulabilir ve bunu özelleştirebilirsiniz. Bu öğreticide kullanılan şablona [basit bir WINDOWS VM dağıtımı](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)denir.

1. Visual Studio Code **Dosya**  >  **Aç dosya**' yı seçin.

1. **Dosya adı** kutusuna aşağıdaki URL 'yi yapıştırın:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Dosyayı açmak için **Aç**’ı seçin. Senaryo, [öğreticide kullanılan ile aynıdır: bağımlı kaynaklarla ARM şablonları oluşturma](./template-tutorial-create-templates-with-dependent-resources.md).
   Şablon altı kaynak tanımlar:

   * [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft. Network/Publicıpaddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft. Network/NetworkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

   Özelleştirebilmeniz için önce şablon hakkında bazı temel bilgileri bilmeniz yararlı olur.

1. **Dosya**  >  **farklı kaydet**' i seçin ve sonra dosyanın bir kopyasını *azuredeploy.js* adı ile yerel bilgisayarınıza kaydedin.

1. Aşağıdaki URL 'yi açmak için 1-3 adımlarını yineleyin ve sonra dosyayı *azuredeploy.parameters.js* olarak kaydedin.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Parametre dosyasını düzenleme

Statik KIMLIK yöntemini kullanarak, şablon dosyasında herhangi bir değişiklik yapmanız gerekmez. Gizli değeri almak, şablon parametre dosyası yapılandırılarak yapılır.

1. Visual Studio Code, zaten açık değilse açık *azuredeploy.parameters.js* açın.
1. Parametreyi şu `adminPassword` şekilde güncelleştirin:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
                "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Değerini, `id` önceki yordamda oluşturduğunuz anahtar kasasının kaynak kimliği ile değiştirin. , `secretName` **Vmadminpassword** olarak kodlanır.  Bkz. [Anahtar Kasası hazırlama](#prepare-a-key-vault).

    ![Anahtar Kasası ve Kaynak Yöneticisi şablonu sanal makine dağıtım parametreleri dosyasını tümleştirin](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Aşağıdaki değerleri güncelleştirin:

    * `adminUsername`: Sanal makine yöneticisi hesabının adı.
    * `dnsLabelPrefix`: Değeri adlandırın `dnsLabelPrefix` .

    Adların örnekleri için önceki resme bakın.

1. Değişiklikleri kaydedin.

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. [Azure Cloud Shell](https://shell.azure.com) oturum açın

1. Sol üst köşedeki **PowerShell** veya **Bash** (CLI için) öğesini seçerek tercih ettiğiniz ortamı seçin.  Geçiş yaptığınızda kabuğun yeniden başlatılması gerekir.

    ![Dosyayı karşıya yükleme Cloud Shell Azure portal](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. **Dosyaları karşıya yükle/indir**'i seçin ve sonra da **Karşıya Yükle**'yi seçin. Hem *azuredeploy.js* hem de *azuredeploy.parameters.js* Cloud Shell üzerine yükleyin. Dosyayı karşıya yükledikten sonra, `ls` `cat` dosyanın başarıyla karşıya yüklendiğini doğrulamak için komutunu ve komutunu kullanabilirsiniz.

1. Şablonu dağıtmak için aşağıdaki PowerShell betiğini çalıştırın.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
    $location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$HOME/azuredeploy.json" `
        -TemplateParameterFile "$HOME/azuredeploy.parameters.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    Şablonu dağıttığınızda, anahtar kasasında kullandığınız kaynak grubunu kullanın. İki yerine yalnızca bir kaynak grubunu silmeniz gerektiğinden bu yaklaşım, kaynakları temizlemeyi kolaylaştırır.

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

Sanal makineyi başarıyla dağıttıktan sonra, anahtar kasasında depolanan parolayı kullanarak oturum açma kimlik bilgilerini test edin.

1. [Azure portalını](https://portal.azure.com) açın.

1. **Kaynak grupları**  >  **\<*YourResourceGroupName*>**  >  **simplewınvm**' yi seçin.
1. Üst kısımdaki **Bağlan** ' ı seçin.
1. **RDP dosyasını indir**' i seçin ve ardından anahtar kasasında depolanan parolayı kullanarak sanal makinede oturum açmak için yönergeleri izleyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure kaynaklarınıza artık ihtiyacınız kalmadığında, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure anahtar kasaınızdan bir gizli dizi almıştır. Daha sonra şablon dağıtımınızda gizli dizi kullandınız. Dağıtım sonrası görevleri gerçekleştirmek üzere sanal makine uzantılarını nasıl kullanacağınızı öğrenmek için bkz.:

> [!div class="nextstepaction"]
> [Sanal makine uzantılarını dağıtma](./template-tutorial-deploy-vm-extensions.md)
