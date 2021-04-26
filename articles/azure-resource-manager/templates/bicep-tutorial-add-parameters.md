---
title: Öğretici-Azure Resource Manager bicep dosyasına parametre ekleme
description: Yeniden kullanılabilir hale getirmek için Bıcep dosyanıza parametreler ekleyin.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 00df2ffc6272011127c5a1eb0c1e302011f8de5f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632790"
---
# <a name="tutorial-add-parameters-to-azure-resource-manager-bicep-file"></a>Öğretici: Azure Resource Manager bicep dosyasına parametre ekleme

[Önceki öğreticide](bicep-tutorial-create-first-bicep.md), bir depolama hesabını dağıtmayı öğrendiniz. Bu öğreticide, parametreler ekleyerek Bıcep dosyasını geliştirmeyi öğreneceksiniz. Bu öğreticinin tamamlanabilmesi yaklaşık **14 dakika** sürer.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

[Ilk bicep dosyasını oluştur](bicep-tutorial-create-first-bicep.md)' u tamamlamanızı öneririz, ancak bu gerekli değildir.

Bıcep uzantılı Visual Studio Code ve Azure PowerShell ya da Azure CLı ile aynı olmalıdır. Daha fazla bilgi için bkz. [Bıcep araçları](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Bıcep dosyasını gözden geçirme

Önceki öğreticinin sonunda Bıcep şöyle görünür:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.bicep":::

Bu bicep dosyası ile ilgili bir sorun olduğunu fark etmiş olabilirsiniz. Depolama hesabı adı sabit kodlanmıştır. Bu bicep dosyasını, her seferinde aynı depolama hesabını dağıtmak için kullanabilirsiniz. Bir depolama hesabını farklı bir adla dağıtmak için, dağıtımlarınızı otomatik hale getirmenin pratik bir yolu olmayan yeni bir Bıcep dosyası oluşturmanız gerekir.

## <a name="make-bicep-file-reusable"></a>Bıcep dosyasını yeniden kullanılabilir yap

Bicep dosyanızı yeniden kullanılabilir hale getirmek için, depolama hesabı adında geçiş yapmak üzere kullanabileceğiniz bir parametre ekleyelim. Aşağıdaki bicep dosyası dosyanızdaki nelerin değiştiğini gösterir. `storageName`Parametresi bir dize olarak tanımlanır. Çok uzun olan adların önlenmesi için en fazla uzunluk 24 karakter olarak ayarlanır.

Tüm dosyayı kopyalayın ve aşağıdaki içerikle değiştirin.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.bicep" range="1-15" highlight="1-3,6":::

Parametrelere, ARM JSON şablonunda [Parameters (' storageName ')] gereksinimi ile karşılaştırıldığında Bıcep içindeki adlarını kullanarak doğrudan başvurulabileceğini unutmayın.

## <a name="deploy-bicep-file"></a>Bıcep dosyasını dağıtma

Bıcep dosyasını dağıtalım. Aşağıdaki örnek, Bıcep dosyasını Azure CLı veya PowerShell ile dağıtır. Depolama hesabı adını dağıtım komutundaki değerlerden biri olarak sağladığınızdan emin olun. Depolama hesabı adı için, önceki öğreticide kullandığınız adı belirtin.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](bicep-tutorial-create-first-bicep.md#create-resource-group). Örnek, `bicepFile` [ilk öğreticide](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)gösterildiği gibi, değişkeni bıcep dosyasının yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bu dağıtım cmdlet 'ini çalıştırmak için Azure PowerShell [en son sürümüne](/powershell/azure/install-az-ps) sahip olmanız gerekir.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Kaynak güncelleştirmelerini anlama

Önceki bölümde, daha önce oluşturduğunuz aynı ada sahip bir depolama hesabı dağıttınız. Kaynağın yeniden dağıtım tarafından nasıl etkilendiğini merak ediyor olabilirsiniz.

Kaynak zaten varsa ve özelliklerde bir değişiklik algılanmazsa herhangi bir işlem uygulanmaz. Kaynak zaten varsa ve bir özellik değiştiyse, kaynak güncelleştirilir. Kaynak yoksa, oluşturulur.

Güncelleştirmeleri işlemenin bu yolu, Bıcep dosyanız bir Azure çözümü için ihtiyaç duyduğunuz tüm kaynakları içerebileceği anlamına gelir. Bıcep dosyasını güvenle yeniden gönderebilir ve kaynakların değiştirildiğini veya yalnızca gerektiğinde oluşturulduğunu bilirsiniz. Örneğin, depolama hesabınıza dosya eklediyseniz depolama hesabını bu dosyaları kaybetmeden yeniden dağıtabilirsiniz.

## <a name="customize-by-environment"></a>Ortama göre özelleştirme

Parametreler, belirli bir ortam için tasarlanmış değerler kullanarak dağıtımı özelleştirmenizi sağlar. Örneğin, geliştirme, test ve üretim için bir ortama dağıtıp dağıtsanız farklı değerleri geçirebilirsiniz.

Önceki bicep dosyası her zaman bir **Standard_LRS** depolama hesabı dağıttı. Ortama bağlı olarak farklı SKU 'Ları dağıtma esnekliği isteyebilirsiniz. Aşağıdaki örnek, SKU için bir parametre eklemek için yapılan değişiklikleri gösterir. Tüm dosyayı kopyalayın ve Bıcep dosyanızın üzerine yapıştırın.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep" range="1-27" highlight="5-15,21":::

`storageSKU`Parametrenin varsayılan bir değeri vardır. Bu değer, dağıtım sırasında bir değer belirtilmediğinde kullanılır. Ayrıca, izin verilen değerlerin bir listesini içerir. Bu değerler, bir depolama hesabı oluşturmak için gereken değerlerle eşleşir. Bicep dosyanızın kullanıcılarının çalışmayan SKU 'Larda geçmesini istemezsiniz.

## <a name="redeploy-bicep-file"></a>Bıcep dosyasını yeniden dağıtma

Yeniden dağıtım için hazırsınız. Varsayılan SKU **Standard_LRS** olarak ayarlandığı için, bu parametre için bir değer sağlamanız gerekmez.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Dağıtım başarısız olursa, `verbose` oluşturulan kaynaklarla ilgili bilgi almak için anahtarını kullanın. `debug`Hata ayıklama hakkında daha fazla bilgi edinmek için anahtarını kullanın.

Bıcep dosyanızın esnekliğini görmek için yeniden dağıtmanıza izin verin. Bu kez, SKU parametresini **Standard_GRS** olarak ayarlayın. Farklı bir depolama hesabı oluşturmak için yeni bir ad geçirebilir ya da mevcut depolama hesabınızı güncelleştirmek için aynı adı kullanabilirsiniz. Her iki seçenek de çalışır.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Son olarak, bir test çalıştıralım ve izin verilen değerlerden biri olmayan bir SKU 'ya geçirdiğinizde ne olacağını görelim. Bu durumda, Bıcep dosyanız için **temel** olan bir kullanıcının SKU 'lardan biri olduğu senaryoya test ederiz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

Komut, hangi değerlere izin verileceğini belirten bir hata iletisiyle hemen başarısız olur. Kaynak Yöneticisi, dağıtım başlamadan önce hatayı tanımlar.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçiş yapıyorsanız, kaynak grubunu silmeniz gerekmez.

Şimdi duruyorsa, kaynak grubunu silerek dağıttığınız kaynakları temizlemeniz gerekebilir.

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Parametreleri ekleyerek [ilk öğreticide](bicep-tutorial-create-first-bicep.md) oluşturulan Bıcep dosyasını geliştirdik. Sonraki öğreticide Bıcep işlevleri hakkında bilgi edineceksiniz.

> [!div class="nextstepaction"]
> [İşlev Ekle](bicep-tutorial-add-functions.md)
