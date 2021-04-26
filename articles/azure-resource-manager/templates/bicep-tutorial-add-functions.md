---
title: Öğretici-Azure Resource Manager bicep dosyalarına işlev ekleme
description: Değerleri oluşturmak için Bıcep dosyalarınıza işlevler ekleyin.
author: mumian
ms.date: 04/20/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: references_regions
ms.openlocfilehash: 43dec6ceb21a6604bc0034b3f14b79ffd2cbe263
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773810"
---
# <a name="tutorial-add-functions-to-azure-resource-manager-bicep-file"></a>Öğretici: Azure Resource Manager bicep dosyasına işlev ekleme

Bu öğreticide, Bıcep dosyanıza [Şablon işlevleri](template-functions.md) eklemeyi öğreneceksiniz. Değerleri dinamik olarak oluşturmak için işlevlerini kullanırsınız. Şu anda Bıcep Kullanıcı tanımlı işlevleri desteklemez. Bu öğreticinin tamamlana **7 dakika** sürer.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

[Parametreler hakkında öğreticiyi](bicep-tutorial-add-parameters.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Bıcep uzantılı Visual Studio Code ve Azure PowerShell ya da Azure CLı ile aynı olmalıdır. Daha fazla bilgi için bkz. [Bıcep araçları](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Bıcep dosyasını gözden geçirme

Önceki öğreticinin sonunda, Bıcep dosyanız aşağıdaki içeriğe sahiptir:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep":::

Depolama hesabının konumu **Doğu ABD** için sabit olarak kodlanmıştır. Ancak, depolama hesabını diğer bölgelere dağıtmanız gerekebilir. Bıcep dosyanız için bir sorunla karşılaşıyoruz. Konum için bir parametre ekleyebilirsiniz, ancak varsayılan değeri yalnızca sabit kodlanmış bir değerden daha anlamlı olursa harika olur.

## <a name="use-function"></a>Use işlevi

İşlevler, dağıtım sırasında değerleri dinamik olarak alarak Bıcep dosyanıza esneklik ekler. Bu öğreticide, dağıtım için kullanmakta olduğunuz kaynak grubunun konumunu almak üzere bir işlev kullanırsınız.

Aşağıdaki örnek, adlı bir parametre eklemek için değişiklikleri gösterir `location` . Parametre varsayılan değeri [resourceGroup](template-functions-resource.md#resourcegroup) işlevini çağırır. Bu işlev, dağıtım için kullanılan kaynak grubuyla ilgili bilgileri içeren bir nesne döndürür. Nesnedeki özelliklerden biri bir konum özelliğidir. Varsayılan değeri kullandığınızda, depolama hesabı konumu kaynak grubuyla aynı konuma sahiptir. Kaynak grubu içindeki kaynakların aynı konumu paylaşması gerekmez. Gerektiğinde farklı bir konum da sağlayabilirsiniz.

Tüm dosyayı kopyalayın ve Bıcep dosyanızı içeriğiyle değiştirin.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep" range="1-30" highlight="18,22":::

## <a name="deploy-bicep-file"></a>Bıcep dosyasını dağıtma

Önceki öğreticilerde, Doğu ABD bir depolama hesabı oluşturdunuz, ancak kaynak grubunuz Orta ABD oluşturulmuş. Bu öğreticide, depolama hesabınız kaynak grubuyla aynı bölgede oluşturulur. Konum için varsayılan değeri kullanın, bu nedenle bu parametre değerini belirtmeniz gerekmez. Farklı bir konumda depolama hesabı oluşturmakta olduğunuz için depolama hesabı için yeni bir ad sağlamalısınız. Örneğin, **store1** yerine ön ek olarak **store2** kullanın.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](bicep-tutorial-create-first-bicep.md#create-resource-group). Örnek, `bicepFile` [ilk öğreticide](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)gösterildiği gibi, değişkeni bıcep dosyasının yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bu dağıtım cmdlet 'ini çalıştırmak için Azure PowerShell [en son sürümüne](/powershell/azure/install-az-ps) sahip olmanız gerekir.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Dağıtım başarısız olursa, `verbose` oluşturulan kaynaklarla ilgili bilgi almak için anahtarını kullanın. `debug`Hata ayıklama hakkında daha fazla bilgi edinmek için anahtarını kullanın.

## <a name="verify-deployment"></a>Dağıtımı doğrulama

Kaynak grubunu Azure portal inceleyerek dağıtımı doğrulayabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol menüden **kaynak grupları**' nı seçin.
1. Dağıttığınız kaynak grubunu seçin.
1. Bir depolama hesabı kaynağının dağıtıldığını ve kaynak grubuyla aynı konuma sahip olduğunu görürsünüz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçiş yapıyorsanız, kaynak grubunu silmeniz gerekmez.

Şimdi duruyorsa, kaynak grubunu silerek dağıttığınız kaynakları temizlemeniz gerekebilir.

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir parametre için varsayılan değeri tanımlarken bir işlev kullandınız. Bu öğretici serisinde, işlevleri kullanmaya devam edersiniz. Serinin sonuna kadar, bicep dosyasının her bölümüne işlevler ekleyeceksiniz.

> [!div class="nextstepaction"]
> [Değişkenleri ekleme](bicep-tutorial-add-variables.md)
