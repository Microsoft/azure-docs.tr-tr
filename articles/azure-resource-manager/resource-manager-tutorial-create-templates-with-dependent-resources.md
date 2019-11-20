---
title: Bağımlı kaynaklarla şablon
description: Birden fazla kaynakla bir Azure Resource Manager şablonu oluşturmayı ve Azure portalı kullanarak dağıtmayı öğrenin
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3e7d397b097691b79f4f74dfd5aa9079af3a84f9
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149348"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Öğretici: Bağımlı kaynaklarla Azure Resource Manager şablonları oluşturma

Birden çok kaynağı dağıtmak ve dağıtım sırasını yapılandırmak için Azure Resource Manager şablonu oluşturmayı öğrenin. Şablonu oluşturduktan sonra Azure portaldan Cloud Shell kullanarak dağıtacaksınız.

Bu öğreticide bir depolama hesabı, bir sanal makine, bir sanal ağ ve ek birkaç bağımlı kaynak oluşturacaksınız. Kaynakların bazıları başka bir kaynak var olana kadar dağıtılamaz. Örneğin depolama hesabı ve ağ arabirimi oluşturulmadan sanal makineyi oluşturamazsınız. Bu ilişkiyi, kaynakların birini diğer kaynaklara bağımlı hale getirerek tanımlarsınız. Resource Manager, kaynaklar arasındaki bağımlılıkları değerlendirir ve bunları bağımlılık sırasına göre dağıtır. Resource Manager, birbirine bağımlı olmayan kaynakları paralel olarak dağıtır. Daha fazla bilgi için bkz. [Azure Resource Manager şablonlarındaki kaynakları dağıtma sırasını belirleme](./resource-group-define-dependencies.md).

![Resource Manager şablonuna bağımlı kaynaklar dağıtım sırası diyagramı](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Hızlı başlangıç şablonunu açma
> * Şablonu keşfetme
> * Şablonu dağıtma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

* [Visual Studio Code](https://code.visualstudio.com/) ve Resource Manager Araçları uzantısı.  Bkz. [Uzantıyı yükleme](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
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
4. **Dosya**>**Farklı Kaydet**'i seçerek dosyanın bir kopyasını yerel bilgisayarınıza **azuredeploy.json** adıyla kaydedin.

## <a name="explore-the-template"></a>Şablonu keşfetme

Bu bölümdeki şablonu inceledikten sonra şu soruları yanıtlamaya çalışın:

* Bu şablonda kaç adet Azure kaynağı tanımlanmıştır?
* Kaynaklardan biri bir Azure depolama hesabıdır.  Tanım, son öğreticide kullanılan tanıma benziyor mu?
* Bu şablonda tanımlanan kaynaklara ilişkin şablon başvurularını bulabilir misiniz?
* Kaynakların bağımlılıklarını bulabilir misiniz?

1. Visual Studio Code'da birinci düzeydeki öğeleri ve **resources** içindeki ikinci düzey öğeleri görene kadar öğeleri daraltın:

    ![Visual Studio Code Azure Resource Manager şablonları](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Şablonun tanımladığı beş kaynak vardır:

   * `Microsoft.Storage/storageAccounts`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Bkz. [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Şablonu özelleştirmeden önce temel noktaları kavramak faydalı olacaktır.

2. İlk kaynağı genişletin. Bir depolama hesabıdır. Kaynak tanımını [şablon başvurusu](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts) ile karşılaştırın.

    ![Visual Studio Code Azure Resource Manager şablonları depolama hesabı tanımı](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. İkinci kaynağı genişletin. Kaynak türü `Microsoft.Network/publicIPAddresses` şeklindedir. Kaynak tanımını [şablon başvurusu](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses) ile karşılaştırın.

    ![Visual Studio Code Azure Resource Manager şablonları genel IP adresi tanımı](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Dördüncü kaynağı genişletin. Kaynak türü `Microsoft.Network/networkInterfaces` şeklindedir:  

    ![Visual Studio Code Azure Resource Manager şablonları dependson](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    dependsOn öğesi, kaynaklardan birini diğer kaynaklardan birine veya daha fazlasına bağımlı olarak tanımlamanızı sağlar. Kaynak, iki farklı kaynağa bağımlıdır:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

5. Beşinci kaynağı genişletin. Bu kaynak bir sanal makinedir. İki farklı kaynağa bağımlıdır:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

Aşağıdaki diyagramda bu şablondaki kaynaklar ve bağımlılık bilgileri gösterilmiştir:

![Visual Studio Code Azure Resource Manager şablonları bağımlılık diyagramı](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Bağımlılıkların belirtilmesi, Resource Manager'ın çözümü verimli bir şekilde dağıtmasını sağlar. Depolama hesabı, genel IP adresi ve sanal ağ herhangi bir bağımlılığa sahip olmadığından paralel olarak dağıtılır. Genel IP adresi ve sanal ağ dağıtıldıktan sonra ağ arabirimi oluşturulur. Resource Manager, diğer tüm kaynaklar dağıtıldıktan sonra sanal makineyi dağıtır.

## <a name="deploy-the-template"></a>Şablonu dağıtma

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Şablonları dağıtmak için birçok yöntem vardır.  Bu öğreticide Azure portaldan Cloud Shell'i kullanacaksınız.

1. [Cloud Shell](https://shell.azure.com)'de oturum açın. 
2. Cloud Shell'in sol üst köşesinden **PowerShell**'i ve ardından **Onayla**'yı seçin.  Bu öğreticide PowerShell'i kullanacaksınız.
3. Cloud Shell'de **Dosya yükle**'yi seçin:

    ![Azure portal Cloud shell dosya karşıya yükleme](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Öğreticide daha önce kaydettiğiniz şablonu seçin. Varsayılan ad **azuredeploy.json** olur.  Aynı dosya adına sahip bir dosyanız varsa bildirim gösterilmeden eski dosyanın üzerine yazılır.

    İsteğe bağlı olarak, dosyaların başarıyla karşıya yüklendiğini doğrulamak için **ls $Home** komutunu ve **Cat $Home/azuredeploy.JSON** komutunu kullanabilirsiniz. 

5. Cloud Shell'de aşağıdaki PowerShell komutlarını çalıştırın. Güvenliği artırmak istiyorsanız sanal makine yönetici hesabı için oluşturulmuş bir parola kullanın. [Ön koşullara](#prerequisites) bakın.

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"
    ```

8. Yeni oluşturulan sanal makineyi listelemek için aşağıdaki PowerShell komutunu çalıştırın:

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    Get-AzVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    Sanal makine adı şablon içinde **SimpleWinVM** olarak kodlanmıştır ve değiştirilemez.

9. Sanal makinenin başarıyla oluşturulduğunu doğrulamak için RDP bağlantısı kurun.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.  Kaynak grubundaki toplam altı kaynak görüyor olmalısınız.
4. Üstteki menüden **Kaynak grubunu sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide sanal makine, sanal ağ ve bağımlı kaynaklar oluşturmak için bir şablon geliştirdiniz ve dağıttınız. Azure kaynaklarını koşullara bağlı olarak dağıtmayı öğrenin:

> [!div class="nextstepaction"]
> [Koşulları kullanma](./resource-manager-tutorial-use-conditions.md)
