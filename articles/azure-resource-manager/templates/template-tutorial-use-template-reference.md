---
title: Şablon başvurusu kullanma
description: Şablon oluşturmak için Azure Resource Manager şablonu (ARM şablonu) başvurusunu kullanın.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: a44852fb2f491dd949b58217eca3e4f3e392cf17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97584145"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>Öğretici: ARM şablon başvurusunu kullanma

Şablon Şeması bilgilerini bulmayı ve bu bilgileri kullanarak Azure Resource Manager şablonları (ARM şablonları) oluşturmayı öğrenin.

Bu öğreticide, Azure hızlı başlangıç şablonlarından temel bir şablon kullanırsınız. Şablon başvuru belgelerini kullanarak şablonu özelleştirirsiniz.

![Kaynak Yöneticisi şablonu başvurusunu dağıt depolama hesabı](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Hızlı başlangıç şablonunu açma
> * Şablon biçimini anlama
> * Şablon başvurusunu bulma
> * Şablonu düzenleme
> * Şablonu dağıtma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

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
1. Dosyayı   >  yerel bilgisayarınızaazuredeploy.jsolarak kaydetmek için dosya **farklı kaydet** _'_ i seçin.

## <a name="understand-the-schema"></a>Şemayı anlama

1. Visual Studio Code, şablonu kök düzeyine daraltın. Aşağıdaki öğelere sahip çok basit bir yapı görürsünüz:

    ![Resource Manager şablonu basit yapısı](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * `$schema`: şablon dilinin sürümünü açıklayan JSON Şema dosyasının konumunu belirtin.
    * `contentVersion`: şablonunuzda önemli değişiklikleri belgelemek için bu öğe için herhangi bir değer belirtin.
    * `parameters`: dağıtım yürütüldüğünde kaynak dağıtımını özelleştirmek için belirtilen değerleri belirtin.
    * `variables`: şablon dil ifadelerini basitleştirmek için şablonda JSON parçaları olarak kullanılan değerleri belirtin.
    * `resources`: bir kaynak grubunda dağıtılan veya güncellenen kaynak türlerini belirtin.
    * `outputs`: dağıtımdan sonra döndürülen değerleri belirtin.

1. Öğesini genişletin `resources` . `Microsoft.Storage/storageAccounts`Tanımlı bir kaynak vardır. SKU adı bir parametre değeri kullanır. Parametresi çağrılır `storageAccountType` .

    ![Resource Manager şablonu depolama hesabı tanımı](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. `parameters`Nasıl tanımlandığını görmek için genişletin `storageAccountType` . Parametrenin dört izin verilen değeri vardır. İzin verilen diğer değerleri bulacak ve ardından parametre tanımını düzelteceksiniz.

    ![Kaynak Yöneticisi Şablon depolama hesabı kaynakları SKU 'ları](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>Şablon başvurusunu bulma

1. [Azure şablon başvurusuna](/azure/templates/)gidin.
1. **Başlığa göre filtrele** kutusuna **depolama hesapları**' nı girin ve **başvuru > depolama** alanındaki ilk **depolama hesaplarını** seçin.

    ![Resource Manager şablon başvurusu depolama hesabı](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Kaynak sağlayıcısı genellikle birkaç API sürümüne sahiptir:

    ![Kaynak Yöneticisi şablonu başvuru depolama hesabı sürümleri](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. Sol bölmedeki **depolama** alanındaki **tüm kaynaklar** ' ı seçin. Bu sayfa, depolama kaynak sağlayıcısının kaynak türlerini ve sürümlerini listeler. Şablonunuzda tanımlanan kaynak türleri için en son API sürümlerini kullanmanız önerilir.

    ![Kaynak Yöneticisi şablonu başvuru depolama hesabı türleri sürümleri](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Kaynak türünün en son sürümünü seçin `storageAccount` . Bu makale yazıldığında en son sürüm **2019-06-01** ' dir. Bu sürümün, şablonunuzda depolama hesabı kaynağı için kullanılan sürümle eşleştiğinden emin olun. API sürümünü güncelleştirirseniz, kaynak tanımının şablon başvurusuyla eşleştiğini doğrulayın.

1. Bu sayfa, storageAccount kaynak türünün ayrıntılarını listeler. Örneğin, **SKU nesnesi** için izin verilen değerleri listeler. Daha önce açtığınız hızlı başlangıç şablonunda listelenenlerden daha fazla SKU vardır. Hızlı başlangıç şablonunu tüm kullanılabilir depolama türlerini içerecek şekilde özelleştirebilirsiniz.

    ![Kaynak Yöneticisi şablonu başvuru depolama hesabı SKU 'ları](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>Şablonu düzenleme

Visual Studio Code, aşağıdaki ekran görüntüsünde gösterildiği gibi ek depolama hesabı türlerini ekleyin:

![Kaynak Yöneticisi şablonu depolama hesabı kaynakları](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. [Azure Cloud Shell](https://shell.azure.com) oturum açın

1. Sol üst köşedeki **PowerShell** veya **Bash** (CLI için) öğesini seçerek tercih ettiğiniz ortamı seçin.  Geçiş yaptığınızda kabuğun yeniden başlatılması gerekir.

    ![Dosyayı karşıya yükleme Cloud Shell Azure portal](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. **Dosyaları karşıya yükle/indir**'i seçin ve sonra da **Karşıya Yükle**'yi seçin. Önceki ekran görüntüsüne bakın. Önceki bölümde kaydettiğiniz dosyayı seçin. Dosyayı karşıya yükledikten sonra, `ls` `cat` dosyanın başarıyla karşıya yüklendiğini doğrulamak için komutunu ve komutunu kullanabilirsiniz.

1. Cloud Shell aşağıdaki komutları çalıştırın. PowerShell kodunu veya CLI kodunu gösteren sekmeyi seçin.

   Şablonu dağıttığınızda, `storageAccountType` parametresini yeni eklenen bir değere (örneğin, **Standard_RAGRS**) belirtin. **Standard_RAGRS** izin verilen bir değer olmadığı için özgün hızlı başlangıç şablonunu kullandıysanız dağıtım başarısız olur.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters storageAccountType='Standard_RAGRS'
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -storageAccountType "Standard_RAGRS"
    ```

    ---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
1. **Ada göre filtrele** alanına kaynak grubu adını girin.
1. Kaynak grubu adını seçin.  Kaynak grubundaki toplam altı kaynak görüyor olmalısınız.
1. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şablon başvurusunu kullanarak var olan bir şablonu özelleştirmeyi öğrendiniz. Birden çok depolama hesabı örneği oluşturmayı öğrenmek için, bkz:

> [!div class="nextstepaction"]
> [Birden çok örnek oluşturma](./template-tutorial-create-multiple-instances.md)
