---
title: Şablonlarda Azure Key Vault kullanma
description: Resource Manager şablonu dağıtımı sırasında parametre değerlerini güvenli bir şekilde geçirme amacıyla Azure Key Vault'u kullanmayı öğrenin
author: mumian
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 86625132e4ac4aa3ed2c42f1e94babcfbbf63a51
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149288"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-resource-manager-template-deployment"></a>Öğretici: Kaynak Yöneticisi şablonu dağıtımınızdaki Azure Key Vault tümleştirin

Azure Anahtar Kasası 'ndan gizli dizileri alma ve Azure Resource Manager dağıtırken gizli dizileri parametre olarak geçirme hakkında bilgi edinin. Yalnızca kendi Anahtar Kasası KIMLIĞINE başvurduğundan parametre değeri hiçbir şekilde gösterilmez. Daha fazla bilgi için bkz. [dağıtım sırasında güvenli parametre değeri geçirmek için Azure Key Vault kullanma](./resource-manager-keyvault-parameter.md).

[Kaynak dağıtım sırasını ayarla](./resource-manager-tutorial-create-templates-with-dependent-resources.md) öğreticisinde, bir sanal makıne (VM) oluşturursunuz. VM Yönetici Kullanıcı adı ve parolasını sağlamanız gerekir. Parolayı sağlamak yerine, parolayı bir Azure Anahtar Kasası 'nda ön depoya alabilir ve ardından, dağıtım sırasında bu parolayı anahtar kasasından almak için şablonu özelleştirebilirsiniz.

![Bir Kaynak Yöneticisi şablonunun anahtar kasası ile tümleştirilmesini gösteren diyagram](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Anahtar Kasası hazırlama
> * Hızlı başlangıç şablonunu açma
> * Parametre dosyasını düzenleme
> * Şablonu dağıtma
> * Dağıtımı doğrulama
> * Kaynakları temizleme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

* [Kaynak Yöneticisi Araçları uzantısıyla](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites) [Visual Studio Code](https://code.visualstudio.com/) .
* Güvenliği artırmak için, VM yönetici hesabı için oluşturulan bir parola kullanın. Parola oluşturmak için bir örnek aşağıda verilmiştir:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Oluşturulan parolanın VM parola gereksinimlerini karşıladığını doğrulayın. Her Azure hizmetinin parola gereksinimleri farklıdır. VM parola gereksinimleri için bkz. [VM oluştururken parola gereksinimleri nelerdir?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Anahtar Kasası hazırlama

Bu bölümde, bir Anahtar Kasası oluşturup buna bir gizli dizi ekleyerek, şablonunuzu dağıtırken gizli dizi elde edebilirsiniz. Anahtar Kasası oluşturmanın birçok yolu vardır. Bu öğreticide, bir [Kaynak Yöneticisi şablonu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json)dağıtmak için Azure PowerShell kullanırsınız. Bu şablon şunları yapar:

* `enabledForTemplateDeployment` özelliği etkinken bir Anahtar Kasası oluşturur. Şablon dağıtım işleminin anahtar kasasında tanımlanan gizli anahtarlara erişebilmesi için bu özelliğin *true* olması gerekir.
* Anahtar kasasına gizli dizi ekler. Gizli dizi VM yönetici parolasını depolar.

> [!NOTE]
> Sanal makine şablonunu dağıtan Kullanıcı olarak, anahtar kasasının sahibi veya katkıda bulunanı değilseniz, sahip veya katkıda bulunan, Anahtar Kasası için *Microsoft. Keykasası/Vaults/dağıtım/eylem* iznine erişim vermelidir. Daha fazla bilgi için bkz. [dağıtım sırasında güvenli bir parametre değeri geçirmek için Azure Key Vault kullanma](./resource-manager-keyvault-parameter.md).

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
```

> [!IMPORTANT]
> * Kaynak grubu adı proje adıdır, ancak buna **RG** eklenmiş olur. [Bu öğreticide oluşturduğunuz kaynakları temizlemeyi](#clean-up-resources)kolaylaştırmak için, bir [sonraki şablonu dağıtırken](#deploy-the-template)aynı proje adı ve kaynak grubu adını kullanın.
> * Gizli dizi için varsayılan ad **Vmadminpassword**' dır. Şablonda sabit kodlanmış.
> * Şablonu gizli dizi almak üzere etkinleştirmek için, Anahtar Kasası için "şablon dağıtımı için Azure Resource Manager erişimi etkinleştir" adlı bir erişim ilkesi etkinleştirmeniz gerekir. Bu ilke şablonda etkinleştirilmiştir. Erişim ilkesi hakkında daha fazla bilgi için bkz. [Anahtar Kasası ve gizli dizileri dağıtma](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

Şablonda *Keyvaultıd*adlı bir çıkış değeri bulunur. Sanal makineyi dağıtırken, daha sonra kullanmak üzere KIMLIK değerini yazın. Kaynak KIMLIĞI biçimi:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

KIMLIĞI kopyalayıp yapıştırdığınızda, birden çok satıra ayrılabilir. Satırları birleştirin ve ek boşlukları kırpın.

Dağıtımı doğrulamak için, parolayı şifresiz metin olarak almak için aynı kabuk bölmesinde aşağıdaki PowerShell komutunu çalıştırın. Komutu, önceki PowerShell betiğinden tanımlanan *$keyVaultName*değişkenini kullandığından, yalnızca aynı kabuk oturumunda çalışacaktır.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Artık bir anahtar kasası ve gizli anahtarı hazırladınız. Aşağıdaki bölümlerde, dağıtım sırasında gizli anahtarı almak için mevcut bir şablonu nasıl özelleştireceğiniz gösterilmektedir.

## <a name="open-a-quickstart-template"></a>Hızlı başlangıç şablonunu açma

Azure hızlı başlangıç şablonları Kaynak Yöneticisi şablonlar için bir depodur. Sıfırdan bir şablon oluşturmak yerine örnek bir şablon bulabilir ve bunu özelleştirebilirsiniz. Bu öğreticide kullanılan şablona [basit bir WINDOWS VM dağıtımı](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)denir.

1. Visual Studio Code **dosya** > **Dosya Aç**' ı seçin.

1. **Dosya adı** kutusuna aşağıdaki URL 'yi yapıştırın:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Dosyayı açmak için **Aç**’ı seçin. Senaryo, [öğretici: bağımlı kaynaklarla Azure Resource Manager şablonları oluşturma konusunda](./resource-manager-tutorial-create-templates-with-dependent-resources.md)kullanılan bir ile aynıdır.
   Şablon beş kaynağı tanımlar:

   * `Microsoft.Storage/storageAccounts`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   Özelleştirebilmeniz için önce şablon hakkında bazı temel bilgileri bilmeniz yararlı olur.

1. **Dosyayı** **farklı kaydet** > seçin ve sonra dosyanın bir kopyasını yerel bilgisayarınıza *azuredeploy. JSON*adıyla kaydedin.

1. Aşağıdaki URL 'yi açmak için 1-3 adımlarını yineleyin ve sonra dosyayı *azuredeploy. Parameters. JSON*olarak kaydedin.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Parametre dosyasını düzenleme

Şablon dosyasında değişiklik yapmanıza gerek yoktur.

1. Visual Studio Code, zaten açık değilse *azuredeploy. Parameters. JSON* öğesini açın.
1. `adminPassword` parametresini şu şekilde güncelleştirin:

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
    > **ID** değerini, önceki yordamda oluşturduğunuz anahtar kasasının kaynak kimliği ile değiştirin.

    ![Anahtar Kasası ve Kaynak Yöneticisi şablonu sanal makine dağıtım parametreleri dosyasını tümleştirin](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Aşağıdaki değerleri güncelleştirin:

    * **AdminUserName**: sanal makine yöneticisi hesabının adı.
    * **dnslabelprefix**: dnslabelprefix değerini adlandırın.

    Adların örnekleri için önceki resme bakın.

1. Değişiklikleri kaydedin.

## <a name="deploy-the-template"></a>Şablonu dağıtma

[Şablonu dağıtma](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)bölümündeki yönergeleri izleyin. Cloud Shell için hem *azuredeploy. JSON* hem de *azuredeploy. Parameters. JSON* dosyasını yükleyin ve ardından şablonu dağıtmak için aşağıdaki PowerShell betiğini kullanın:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

Şablonu dağıttığınızda, anahtar kasasında kullandığınız kaynak grubunu kullanın. İki yerine yalnızca bir kaynak grubunu silmeniz gerektiğinden bu yaklaşım, kaynakları temizlemeyi kolaylaştırır.

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

Sanal makineyi başarıyla dağıttıktan sonra, anahtar kasasında depolanan parolayı kullanarak oturum açma kimlik bilgilerini test edin.

1. [Azure portalı](https://portal.azure.com) açın.

1.  >  **\<*Yourresourcegroupname*>**  > **Simplewinvm**' de **kaynak grupları** ' nı seçin.
1. Üst kısımdaki **Bağlan** ' ı seçin.
1. **RDP dosyasını indir**' i seçin ve ardından anahtar kasasında depolanan parolayı kullanarak sanal makinede oturum açmak için yönergeleri izleyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure kaynaklarınıza artık ihtiyacınız kalmadığında, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure anahtar kasaınızdan bir gizli dizi almıştır. Daha sonra şablon dağıtımınızda gizli dizi kullandınız. Bağlı şablon oluşturmayı öğrenmek için bkz.:

> [!div class="nextstepaction"]
> [Bağlı şablonlar oluşturma](./resource-manager-tutorial-create-linked-templates.md)
