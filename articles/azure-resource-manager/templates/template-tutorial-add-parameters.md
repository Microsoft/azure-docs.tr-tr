---
title: Öğretici-şablona parametreler ekleme
description: Yeniden kullanılabilir hale getirmek için Azure Resource Manager şablonunuza parametreler ekleyin (ARM şablonu).
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: e983f8499cbeaf400a8da6f48d7f6c8b75c4795a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97107071"
---
# <a name="tutorial-add-parameters-to-your-arm-template"></a>Öğretici: ARM şablonunuza parametreler ekleme

[Önceki öğreticide](template-tutorial-add-resource.md), şablona bir depolama hesabı eklemeyi ve bunu dağıtmayı öğrendiniz. Bu öğreticide, parametreler ekleyerek Azure Resource Manager şablonu (ARM şablonu) geliştirmeyi öğreneceksiniz. Bu öğreticinin tamamlanabilmesi yaklaşık **14 dakika** sürer.

## <a name="prerequisites"></a>Önkoşullar

[Kaynaklar hakkında öğreticiyi](template-tutorial-add-resource.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Kaynak Yöneticisi Araçları uzantısı ve Azure PowerShell ya da Azure CLı ile Visual Studio Code olması gerekir. Daha fazla bilgi için bkz. [şablon araçları](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Şablonu gözden geçir

Önceki öğreticinin sonunda, şablonunuz aşağıdaki JSON 'a sahipti:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Bu şablonla ilgili bir sorun olduğunu fark etmiş olabilirsiniz. Depolama hesabı adı sabit kodlanmıştır. Bu şablonu yalnızca her seferinde aynı depolama hesabını dağıtmak için kullanabilirsiniz. Bir depolama hesabını farklı bir adla dağıtmak için, dağıtımlarınızı otomatik hale getirmenin pratik bir yolu olmayan yeni bir şablon oluşturmanız gerekir.

## <a name="make-template-reusable"></a>Şablonu yeniden kullanılabilir yap

Şablonunuzu yeniden kullanılabilir hale getirmek için, depolama hesabı adında geçiş yapmak üzere kullanabileceğiniz bir parametre ekleyelim. Aşağıdaki örnekteki vurgulanmış JSON, şablonunuzda nelerin değiştiğini gösterir. `storageName`Parametresi bir dize olarak tanımlanır. Çok uzun olan adların önlenmesi için en fazla uzunluk 24 karakter olarak ayarlanır.

Tüm dosyayı kopyalayın ve şablonunuzu içeriğiyle değiştirin.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Şablon dağıtma

Şablonu dağıtalım. Aşağıdaki örnek, şablonu Azure CLı veya PowerShell ile dağıtır. Depolama hesabı adını dağıtım komutundaki değerlerden biri olarak sağladığınızdan emin olun. Depolama hesabı adı için, önceki öğreticide kullandığınız adı belirtin.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](template-tutorial-create-first-template.md#create-resource-group). Örnek, `templateFile` [ilk öğreticide](template-tutorial-create-first-template.md#deploy-template)gösterildiği gibi, değişkeni şablon dosyası yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Kaynak güncelleştirmelerini anlama

Önceki bölümde, daha önce oluşturduğunuz aynı ada sahip bir depolama hesabı dağıttınız. Kaynağın yeniden dağıtım tarafından nasıl etkilendiğini merak ediyor olabilirsiniz.

Kaynak zaten varsa ve özelliklerde bir değişiklik algılanmazsa herhangi bir işlem uygulanmaz. Kaynak zaten varsa ve bir özellik değiştiyse, kaynak güncelleştirilir. Kaynak yoksa, oluşturulur.

Güncelleştirmeleri işlemenin bu yolu, şablonunuz bir Azure çözümü için ihtiyaç duyduğunuz tüm kaynakları içerebileceği anlamına gelir. Şablonu güvenle yeniden gönderebilir ve kaynakların değiştirildiğini veya yalnızca gerektiğinde oluşturulduğunu bilirsiniz. Örneğin, depolama hesabınıza dosya eklediyseniz depolama hesabını bu dosyaları kaybetmeden yeniden dağıtabilirsiniz.

## <a name="customize-by-environment"></a>Ortama göre özelleştirme

Parametreler, belirli bir ortam için tasarlanmış değerler kullanarak dağıtımı özelleştirmenizi sağlar. Örneğin, geliştirme, test ve üretim için bir ortama dağıtıp dağıtsanız farklı değerleri geçirebilirsiniz.

Önceki şablon her zaman bir **Standard_LRS** depolama hesabı dağıttı. Ortama bağlı olarak farklı SKU 'Ları dağıtma esnekliği isteyebilirsiniz. Aşağıdaki örnek, SKU için bir parametre eklemek için yapılan değişiklikleri gösterir. Tüm dosyayı kopyalayın ve şablonunuzun üzerine yapıştırın.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

`storageSKU`Parametrenin varsayılan bir değeri vardır. Bu değer, dağıtım sırasında bir değer belirtilmediğinde kullanılır. Ayrıca, izin verilen değerlerin bir listesini içerir. Bu değerler, bir depolama hesabı oluşturmak için gereken değerlerle eşleşir. Şablon kullanıcılarının çalışmayan SKU 'Larda geçmesini istemezsiniz.

## <a name="redeploy-template"></a>Şablonu yeniden dağıtma

Yeniden dağıtım için hazırsınız. Varsayılan SKU **Standard_LRS** olarak ayarlandığı için, bu parametre için bir değer sağlamanız gerekmez.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Dağıtım başarısız olursa, `verbose` oluşturulan kaynaklarla ilgili bilgi almak için anahtarını kullanın. `debug`Hata ayıklama hakkında daha fazla bilgi edinmek için anahtarını kullanın.

Şablonunuzun esnekliğini görmek için yeniden dağıtmanıza izin verin. Bu kez, SKU parametresini **Standard_GRS** olarak ayarlayın. Farklı bir depolama hesabı oluşturmak için yeni bir ad geçirebilir ya da mevcut depolama hesabınızı güncelleştirmek için aynı adı kullanabilirsiniz. Her iki seçenek de çalışır.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Son olarak, bir test çalıştıralım ve izin verilen değerlerden biri olmayan bir SKU 'ya geçirdiğinizde ne olacağını görelim. Bu durumda, şablonunuzda bir kullanıcının **temel** olarak SKU 'lardan biri olduğu senaryoya test ederiz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
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

Parametreleri ekleyerek [ilk öğreticide](template-tutorial-create-first-template.md) oluşturulan şablonu geliştirdik. Sonraki öğreticide, Şablon işlevleri hakkında bilgi edineceksiniz.

> [!div class="nextstepaction"]
> [Şablon işlevleri ekleme](template-tutorial-add-functions.md)
