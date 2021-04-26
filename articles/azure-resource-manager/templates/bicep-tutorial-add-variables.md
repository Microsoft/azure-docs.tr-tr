---
title: Öğretici-Azure Resource Manager bicep dosyasına değişken ekleme
description: Sözdizimini basitleştirmek için Bıcep dosyanıza değişkenler ekleyin.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: da2755c1f2c0f9fa891fe1a99b1fed21f64492c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632484"
---
# <a name="tutorial-add-variables-to-azure-resource-manager-bicep-file"></a>Öğretici: Azure Resource Manager bicep dosyasına değişken ekleme

Bu öğreticide, Bıcep dosyanıza nasıl değişken ekleneceğini öğreneceksiniz. Değişkenler bir kez yazmanızı ve Bıcep dosyası boyunca yeniden kullanılmasını sağlayarak Bıcep dosyalarınızı basitleştirir. Bu öğreticinin tamamlana **7 dakika** sürer.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

[İşlevlerle ilgili öğreticiyi](bicep-tutorial-add-functions.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Bıcep uzantılı Visual Studio Code ve Azure PowerShell ya da Azure CLı ile aynı olmalıdır. Daha fazla bilgi için bkz. [Bıcep araçları](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Bıcep dosyasını gözden geçirme

Önceki öğreticinin sonunda, Bıcep dosyanız aşağıdaki içeriğe sahiptir:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep":::

Benzersiz bir ad sağlamanız gerektiğinden, depolama hesabı adı için parametre kullanımı zor. Bu serideki önceki öğreticileri tamamladıysanız, büyük olasılıkla benzersiz bir ad tahmin etmeniz çok yorsunuzdur. Depolama hesabı için benzersiz bir ad oluşturan bir değişken ekleyerek bu sorunu çözebilirsiniz.

## <a name="use-variable"></a>Değişken kullan

Aşağıdaki örnek, Bıcep dosyanıza benzersiz bir depolama hesabı adı oluşturan bir değişken ekleme değişikliklerini gösterir. Tüm dosyayı kopyalayın ve Bıcep dosyanızı içeriğiyle değiştirin.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep" range="1-31" highlight="1-3,19,22":::

Adında bir değişken içerdiğine dikkat edin `uniqueStorageName` . Bu değişken, bir dize değeri oluşturmak için üç işlevi kullanır.

[ResourceGroup](template-functions-resource.md#resourcegroup) işlevi hakkında bilgi sahibisiniz. Bu durumda, `id` `location` önceki öğreticide gösterildiği gibi özelliği yerine özelliği alırsınız. `id`Özelliği, ABONELIK kimliği ve kaynak grubu adı dahil olmak üzere kaynak grubunun tam tanımlayıcısını döndürür.

[Uniquestring](template-functions-string.md#uniquestring) işlevi bir 13 karakterlik karma değeri oluşturur. Döndürülen değer geçirdiğiniz parametrelere göre belirlenir. Bu öğreticide, karma değer girişi olarak kaynak grubu KIMLIĞINI kullanırsınız. Bu, bu Bıcep dosyasını farklı kaynak gruplarına dağıtabileceğiniz ve farklı bir benzersiz dize değeri alabileceğiniz anlamına gelir. Ancak, aynı kaynak grubuna dağıtırsanız aynı değeri alırsınız.

Bıcep, [dize ilişkilendirme](https://en.wikipedia.org/wiki/String_interpolation#) söz dizimini destekler. Bu değişken için, parametresinden dize ve dizeden dize alır `uniqueString` ve bunları tek bir dize olarak birleştirir.

`storagePrefix`Parametresi, depolama hesaplarını belirlemenize yardımcı olan bir ön ek geçirmenize olanak sağlar. Depolama hesaplarının uzun bir kaynak listesinden dağıtımdan sonra tanımlanmasını kolaylaştıran kendi adlandırma kuralınızın oluşturulmasını sağlayabilirsiniz.

Son olarak, depolama adının bir parametre yerine artık değişkene ayarlı olduğuna dikkat edin.

## <a name="deploy-bicep-file"></a>Bıcep dosyasını dağıtma

Bıcep dosyasını dağıtalım. Yalnızca depolama adı için ön ek sağlamanız gerektiğinden, bu bicep dosyasını dağıtmak önceki Bıcep dosyalarından daha kolay.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](bicep-tutorial-create-first-bicep.md#create-resource-group). Örnek, `bicepFile` [ilk öğreticide](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)gösterildiği gibi, değişkeni bıcep dosyasının yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bu dağıtım cmdlet 'ini çalıştırmak için Azure PowerShell [en son sürümüne](/powershell/azure/install-az-ps) sahip olmanız gerekir.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.

```azurecli
az deployment group create \
  --name addnamevariable \
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
1. Bir depolama hesabı kaynağının dağıtıldığını görürsünüz. Depolama hesabının adı, **Depo** ve rastgele karakterlerden oluşan bir dizedir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçiş yapıyorsanız, kaynak grubunu silmeniz gerekmez.

Şimdi duruyorsa, kaynak grubunu silerek dağıttığınız kaynakları temizlemeniz gerekebilir.

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, depolama hesabı için benzersiz bir ad oluşturan bir değişken eklediniz. Sonraki öğreticide, dağıtılan depolama hesabından bir değer döndürürler.

> [!div class="nextstepaction"]
> [Çıkış Ekle](bicep-tutorial-add-outputs.md)
