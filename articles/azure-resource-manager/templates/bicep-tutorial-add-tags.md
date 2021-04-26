---
title: Öğretici-Azure Resource Manager Bıcep dosyasındaki kaynaklara etiket ekleme
description: Bicep dosyalarınıza dağıttığınız kaynaklara Etiketler ekleyin. Etiketler, kaynakları mantıksal olarak düzenlemenizi sağlar.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ea5e078eb692d002b3f86cd43663dd042d692611
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632616"
---
# <a name="tutorial-add-tags-in-azure-resource-manager-bicep-files"></a>Öğretici: Azure Resource Manager bicep dosyalarına etiket ekleme

Bu öğreticide, Bıcep dosyalarınızda kaynaklara nasıl etiket ekleneceğini öğreneceksiniz. [Etiketler](../management/tag-resources.md) , kaynaklarınızı mantıksal olarak düzenlemenize yardımcı olur. Etiket değerleri, maliyet raporlarında gösterilir. Bu öğreticinin tamamlandığı **8 dakika** sürer.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

[Hızlı başlangıç şablonları hakkında öğreticiyi](bicep-tutorial-quickstart-template.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Bıcep uzantılı Visual Studio Code ve Azure PowerShell ya da Azure CLı ile aynı olmalıdır. Daha fazla bilgi için bkz. [Bıcep araçları](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Bıcep dosyasını gözden geçirme

Önceki Bıcep dosyanız bir depolama hesabı, App Service planı ve Web uygulaması dağıttı.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep":::

Bu kaynakları dağıttıktan sonra, maliyetleri izlemeniz ve bir kategoriye ait kaynakları bulmanız gerekebilir. Bu sorunları çözmeye yardımcı olmak için Etiketler ekleyebilirsiniz.

## <a name="add-tags"></a>Etiket ekleme

Kaynakların kullanım amacını daha kolay belirlemek için değerler ekleyebilirsiniz. Örneğin, ortamı ve projeyi listelemek için Etiketler ekleyebilirsiniz. Bir maliyet merkezini veya kaynağa sahip olan takımı tanımlayan etiketler ekleyebilirsiniz. Kuruluşunuz için anlamlı olan tüm değerleri kullanabilirsiniz.

Aşağıdaki örnek, Bıcep dosyasındaki değişiklikleri gösterir. Tüm dosyayı kopyalayın ve Bıcep dosyanızı içeriğiyle değiştirin.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep" range="1-81" highlight="27-30,38,51,71":::

## <a name="deploy-bicep-file"></a>Bıcep dosyasını dağıtma

Bıcep dosyasının dağıtılması ve sonuçlara bakmaları zaman alabilir.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](bicep-tutorial-create-first-bicep.md#create-resource-group). Örnek, `bicepFile` [ilk öğreticide](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)gösterildiği gibi, değişkeni bıcep dosyasının yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bu dağıtım cmdlet 'ini çalıştırmak için Azure PowerShell [en son sürümüne](/powershell/azure/install-az-ps) sahip olmanız gerekir.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Dağıtım başarısız olursa, `verbose` oluşturulan kaynaklarla ilgili bilgi almak için anahtarını kullanın. `debug`Hata ayıklama hakkında daha fazla bilgi edinmek için anahtarını kullanın.

## <a name="verify-deployment"></a>Dağıtımı doğrulama

Kaynak grubunu Azure portal inceleyerek dağıtımı doğrulayabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol menüden **kaynak grupları**' nı seçin.
1. Dağıttığınız kaynak grubunu seçin.
1. Depolama hesabı kaynağı gibi kaynaklardan birini seçin. Artık etiketlere sahip olduğunu görürsünüz.

   ![Etiketleri göster](./media/bicep-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçiş yapıyorsanız, kaynak grubunu silmeniz gerekmez.

Şimdi duruyorsa, kaynak grubunu silerek dağıttığınız kaynakları temizlemeniz gerekebilir.

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, kaynaklara Etiketler eklediniz. Sonraki öğreticide, değerleri dağıtıma geçirmeyi kolaylaştırmak için parametre dosyalarını nasıl kullanacağınızı öğreneceksiniz.

> [!div class="nextstepaction"]
> [Parametre dosyası kullan](bicep-tutorial-use-parameter-file.md)
