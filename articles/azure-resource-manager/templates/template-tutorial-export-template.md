---
title: Öğretici-Azure portal şablonu dışarı aktarma
description: Şablon geliştirmeyi tamamlamaya yönelik olarak, aktarılmış bir şablonu nasıl kullanacağınızı öğrenin.
author: mumian
ms.date: 09/09/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ba1797da5a78eeebd25f5df1b6e37eb92470f584
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97106929"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Öğretici: Azure portal dışarıya aktarılmış şablon kullanma

Bu öğretici serisinde, Azure depolama hesabı dağıtmak için bir şablon oluşturdunuz. Sonraki iki öğreticilerde, bir *App Service planı* ve bir *Web sitesi* eklersiniz. Sıfırdan şablon oluşturmak yerine, Azure portal şablonları dışarı aktarmayı ve [Azure hızlı başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/)örnek şablonları kullanmayı öğreneceksiniz. Bu şablonları kullanım için özelleştirirsiniz. Bu öğretici, şablonları dışarı aktarmaya ve şablonunuzun sonucunu özelleştirmeye odaklanır. Yaklaşık **14 dakika** sürer.

## <a name="prerequisites"></a>Önkoşullar

[Çıktılar hakkında öğreticiyi](template-tutorial-add-outputs.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Kaynak Yöneticisi Araçları uzantısı ve Azure PowerShell ya da Azure CLı ile Visual Studio Code olması gerekir. Daha fazla bilgi için bkz. [şablon araçları](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Şablonu gözden geçir

Önceki öğreticinin sonunda, şablonunuz aşağıdaki JSON 'a sahipti:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

Bu şablon, depolama hesaplarının dağıtımı için iyi bir sonuç verir, ancak buna daha fazla kaynak eklemek isteyebilirsiniz. Var olan bir kaynaktan bir şablonu dışarı aktarmak için bu kaynağın JSON 'sini hızlıca alabilirsiniz.

## <a name="create-app-service-plan"></a>App Service planı oluşturma

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Kaynak oluştur**’u seçin.
1. **Markette ara**' te **App Service planı** girin ve **App Service planı**' nı seçin.  **App Service planı seçme (klasik)**
1. **Oluştur**’u seçin.
1. Şunları girin:

    - **Abonelik**: Azure aboneliğinizi seçin.
    - **Kaynak grubu**: **Yeni oluştur** ' u seçin ve ardından bir ad belirtin. Bu öğretici serisinde kullandığınızdan farklı bir kaynak grubu adı belirtin.
    - **Ad**: App Service planı için bir ad girin.
    - **Işletim sistemi**: **Linux**' u seçin.
    - **Bölge**: bir Azure konumu seçin. Örneğin **Orta ABD**.
    - **Fiyatlandırma katmanı**: maliyetleri kaydetmek için SKU 'Yu **temel B1** (geliştirme/test altında) olarak değiştirin.

    ![Kaynak Yöneticisi şablonu dışarı aktarma şablonu portalı](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. **Gözden geçir ve Oluştur '** u seçin.
1. **Oluştur**’u seçin. Kaynağı oluşturmak birkaç dakika sürer.

## <a name="export-template"></a>Şablonu dışarı aktarma

1. **Kaynağa git**’i seçin.

    ![Kaynağa git](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. **Şablonu dışarı aktar**' ı seçin.

    ![Kaynak Yöneticisi şablonu dışarı aktarma şablonu](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   Şablonu dışarı aktar özelliği, bir kaynağın geçerli durumunu alır ve bunu dağıtmak için bir şablon oluşturur. Bir şablonu dışarı aktarmak, bir kaynağı dağıtmanız için ihtiyacınız olan JSON 'ı hızlı bir şekilde almanın yararlı bir yolu olabilir.

1. `Microsoft.Web/serverfarms`İçe aktarılmış şablondaki tanımına ve parametre tanımına bakın. Bu bölümleri kopyalamanız gerekmez. Bu içe aktarılmış şablonu, bu kaynağı şablonunuza eklemek istediğiniz bir örnek olarak kullanabilirsiniz.

    ![Kaynak Yöneticisi şablonu dışarı aktarma şablonu dışarı aktarma şablonu](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Genellikle, içe aktarılmış şablon, bir şablon oluştururken kullanmak isteyebileceğiniz daha ayrıntılıdır. Örneğin, verdiğiniz şablondaki SKU nesnesinin beş özelliği vardır. Bu şablon işe yarar, ancak yalnızca `name` özelliğini kullanabilirsiniz. Verdiğiniz şablonla başlayabilir ve sonra gereksinimlerinize uyacak şekilde değiştirebilirsiniz.

## <a name="revise-existing-template"></a>Mevcut şablonu gözden geçir

İçe aktarılmış şablon size ihtiyacınız olan JSON 'ın çoğunu verir, ancak şablonunuz için özelleştirmeniz gerekir. Şablonlarınız ve aktarılmış şablon arasındaki parametrelerde ve değişkenlerde farklara dikkat edin. Kuşkusuz, dışarı aktarma işlemi şablonunuzda zaten tanımladığınız parametreleri ve değişkenleri bilmez.

Aşağıdaki örnek, şablonlarınızın eklemelerini vurgular. Bu, içe aktarılmış kodu ve bazı değişiklikleri içerir. İlk olarak, parametre adını adlandırma kuralınızın eşleşmesi için değiştirir. İkincisi, App Service planının konumu için konum parametresini kullanır. Üçüncü olarak, varsayılan değerin iyi olduğu bazı özellikleri kaldırır.

Tüm dosyayı kopyalayın ve şablonunuzu içeriğiyle değiştirin.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json" range="1-77" highlight="28-31,50-69":::

## <a name="deploy-template"></a>Şablon dağıtma

Bir şablonu dağıtmak için Azure CLı veya Azure PowerShell kullanın.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](template-tutorial-create-first-template.md#create-resource-group). Örnek, `templateFile` [ilk öğreticide](template-tutorial-create-first-template.md#deploy-template)gösterildiği gibi, değişkeni şablon dosyası yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Dağıtım başarısız olursa, `verbose` oluşturulan kaynaklarla ilgili bilgi almak için anahtarını kullanın. `debug`Hata ayıklama hakkında daha fazla bilgi edinmek için anahtarını kullanın.

## <a name="verify-deployment"></a>Dağıtımı doğrulama

Kaynak grubunu Azure portal inceleyerek dağıtımı doğrulayabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol menüden **kaynak grupları**' nı seçin.
1. Dağıttığınız kaynak grubunu seçin.
1. Kaynak grubu, bir depolama hesabı ve bir App Service planı içerir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçiş yapıyorsanız, kaynak grubunu silmeniz gerekmez.

Şimdi duruyorsa, kaynak grubunu silerek dağıttığınız kaynakları temizlemeniz gerekebilir.

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Azure portal bir şablonu nasıl dışarı aktarıp şablon geliştirmede dışarı aktarılmış şablonu nasıl kullanacağınızı öğrendiniz. Şablon geliştirmeyi basitleştirmek için Azure hızlı başlangıç şablonlarını da kullanabilirsiniz.

> [!div class="nextstepaction"]
> [Azure hızlı başlangıç şablonlarını kullanma](template-tutorial-quickstart-template.md)
