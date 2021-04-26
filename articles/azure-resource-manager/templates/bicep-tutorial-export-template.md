---
title: Öğretici-Bıcep geliştirme için Azure portal JSON şablonunu dışarı aktarma
description: Bıcep geliştirmeyi gerçekleştirmek için, dışarıya aktarılmış bir JSON şablonu kullanmayı öğrenin.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3bc7ed4ada4f7810e9864778c7f76a0573c9dc89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632568"
---
# <a name="tutorial-use-exported-json-template-from-the-azure-portal"></a>Öğretici: Azure portal aktarılmış JSON şablonunu kullanın

Bu öğretici serisinde, bir Azure depolama hesabı dağıtmak için bir Bıcep dosyası oluşturdunuz. Sonraki iki öğreticilerde, bir *App Service planı* ve bir *Web sitesi* eklersiniz. Sıfırdan şablon oluşturmak yerine, Azure portal JSON şablonlarını dışarı aktarmayı ve [Azure hızlı başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/)örnek şablonları kullanmayı öğreneceksiniz. Bu şablonları kullanım için özelleştirirsiniz. Bu öğretici, şablonları dışarı aktarmaya ve Bıcep dosyanız için sonucu özelleştirmeye odaklanmaktadır. Yaklaşık **14 dakika** sürer.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

[Çıktılar hakkında öğreticiyi](bicep-tutorial-add-outputs.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Bıcep uzantılı Visual Studio Code ve Azure PowerShell ya da Azure CLı ile aynı olmalıdır. Daha fazla bilgi için bkz. [Bıcep araçları](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Bıcep dosyasını gözden geçirme

Önceki öğreticinin sonunda, Bıcep dosyanız aşağıdaki içeriğe sahiptir:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep":::

Bu Bıcep dosyası depolama hesaplarının dağıtımı için iyi bir işlem sağlar, ancak buna daha fazla kaynak eklemek isteyebilirsiniz. Var olan bir kaynaktan JSON şablonunu dışarı aktarabilirsiniz. bu kaynak için JSON 'ı hızlıca alabilirsiniz. Ardından, Bıcep dosyanıza ekleyebilmeniz için önce JSON 'ı Bıcep 'ye dönüştürün.

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

    ![Kaynak Yöneticisi şablonu dışarı aktarma şablonu portalı](./media/bicep-tutorial-export-template/resource-manager-template-export.png)
1. **Gözden geçir ve Oluştur '** u seçin.
1. **Oluştur**’u seçin. Kaynağı oluşturmak birkaç dakika sürer.

## <a name="export-template"></a>Şablonu dışarı aktarma

Şu anda Azure portal yalnızca JSON şablonlarının dışarı verilmesini destekler. JSON şablonlarını Bıcep dosyalarına derlemek için kullanabileceğiniz araçlar vardır.

1. **Kaynağa git**’i seçin.

    ![Kaynağa git](./media/bicep-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. **Şablonu dışarı aktar**' ı seçin.

    ![Kaynak Yöneticisi şablonu dışarı aktarma şablonu](./media/bicep-tutorial-export-template/resource-manager-template-export-template.png)

   Şablonu dışarı aktar özelliği, bir kaynağın geçerli durumunu alır ve bunu dağıtmak için bir şablon oluşturur. Bir şablonu dışarı aktarmak, bir kaynağı dağıtmanız için ihtiyacınız olan JSON 'ı hızlı bir şekilde almanın yararlı bir yolu olabilir.

1. `Microsoft.Web/serverfarms`Tanım ve parametre tanımı, ihtiyacınız olan bölümlerdir.

    ![Kaynak Yöneticisi şablonu dışarı aktarma şablonu dışarı aktarma şablonu](./media/bicep-tutorial-export-template/resource-manager-template-exported-template.png)

    > [!IMPORTANT]
    > Genellikle, içe aktarılmış şablon, bir şablon oluştururken kullanmak isteyebileceğiniz daha ayrıntılıdır. Örneğin, verdiğiniz şablondaki SKU nesnesinin beş özelliği vardır. Bu şablon işe yarar, ancak yalnızca `name` özelliğini kullanabilirsiniz. Verdiğiniz şablonla başlayabilir ve sonra gereksinimlerinize uyacak şekilde değiştirebilirsiniz.

1. **İndir**'i seçin.  İndirilen ZIP dosyası, üzerinde bir **template.js** ve bir **parameters.js** içerir. Dosyaları sıkıştırmayı açın.
1. **https://bicepdemo.z22.web.core.windows.net/**' A gidin, **derlemeyi kaldırma**' yı seçin ve sonra **template.js** açın. Şablonu Bıcep ' de alırsınız.

## <a name="revise-existing-bicep-file"></a>Mevcut bicep dosyasını gözden geçir

Karmaşıkmış olan bir şekilde, ihtiyacınız olan bicep 'nin çoğunu sağlayan, ancak Bıcep dosyanız için özelleştirmeniz gerekir. Bicep dosyanız ile içe aktarılmış bicep dosyası arasındaki parametrelere ve değişkenlerde oluşan farklara dikkat edin. Kuşkusuz, dışa aktarma işlemi Bıcep dosyanızda zaten tanımlamış olduğunuz parametreleri ve değişkenleri bilmez.

Aşağıdaki örnek, Bıcep dosyanıza yapılan eklemeleri gösterir. Bu, içe aktarılmış kodu ve bazı değişiklikleri içerir. İlk olarak, parametre adını adlandırma kuralınızın eşleşmesi için değiştirir. İkincisi, App Service planının konumu için konum parametresini kullanır. Üçüncü olarak, varsayılan değerin iyi olduğu bazı özellikleri kaldırır.

Tüm dosyayı kopyalayın ve Bıcep dosyanızı içeriğiyle değiştirin.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep" range="1-53" highlight="18,34-51":::

## <a name="deploy-bicep-file"></a>Bıcep dosyasını dağıtma

Bir Bıcep dosyası dağıtmak için Azure CLı veya Azure PowerShell kullanın.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](bicep-tutorial-create-first-bicep.md#create-resource-group). Örnek, `bicepFile` [ilk öğreticide](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)gösterildiği gibi, değişkeni bıcep dosyasının yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bu dağıtım cmdlet 'ini çalıştırmak için Azure PowerShell [en son sürümüne](/powershell/azure/install-az-ps) sahip olmanız gerekir.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
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

Azure portal JSON şablonu dışarı aktarmayı, JSON şablonunu bir bicep dosyasına dönüştürmeyi ve Bıcep geliştirme için dışarı aktarılmış şablonu kullanmayı öğrendiniz. Ayrıca, bicep geliştirmeyi basitleştirmek için Azure hızlı başlangıç şablonlarını da kullanabilirsiniz.

> [!div class="nextstepaction"]
> [Azure hızlı başlangıç şablonlarını kullanma](bicep-tutorial-quickstart-template.md)
