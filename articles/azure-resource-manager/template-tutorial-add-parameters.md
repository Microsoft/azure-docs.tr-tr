---
title: Öğretici-şablona parametreler ekleme
description: Yeniden kullanılabilir hale getirmek için Azure Resource Manager şablonunuza parametreler ekleyin.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7a4d8db57167bc82d13b4d46be1abc3518c340e4
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150241"
---
# <a name="tutorial-add-parameters-to-your-resource-manager-template"></a>Öğretici: Kaynak Yöneticisi şablonunuza parametreler ekleme

[Önceki öğreticide](template-tutorial-add-resource.md), şablona bir depolama hesabı eklemeyi ve bunu dağıtmayı öğrendiniz. Bu öğreticide, parametreleri ekleyerek şablonu geliştirmeyi öğrenirsiniz. Bu öğreticinin tamamlanabilmesi yaklaşık **14 dakika** sürer.

## <a name="prerequisites"></a>Önkoşullar

[Kaynaklar hakkında öğreticiyi](template-tutorial-add-resource.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Kaynak Yöneticisi Araçları uzantısı ve Azure PowerShell ya da Azure CLı ile Visual Studio Code olması gerekir. Daha fazla bilgi için bkz. [şablon araçları](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Şablonunuzu gözden geçirin

Önceki öğreticinin sonunda, şablonunuz aşağıdaki JSON 'a sahipti:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json)]

Bu şablonla ilgili bir sorun olduğunu fark etmiş olabilirsiniz. Depolama hesabı adı sabit kodlanmış. Bu şablonu yalnızca aynı depolama hesabını her seferinde dağıtmak için kullanabilirsiniz. Bir depolama hesabını farklı bir adla dağıtmak için, dağıtımlarınızı otomatik hale getirmenin pratik bir yolu olmayan yeni bir şablon oluşturmanız gerekir.

## <a name="make-your-template-reusable"></a>Şablonunuzu yeniden kullanılabilir hale getirin

Şablonunuzu yeniden kullanılabilir hale getirmek için, depolama hesabı adında geçiş yapmak üzere kullanabileceğiniz bir parametre ekleyelim. Aşağıdaki örnekteki vurgulanmış JSON, şablonunuzda nelerin değiştiğini gösterir. **Storagename** parametresi bir dize olarak tanımlanır. Çok uzun olan adların önlenmesi için en fazla uzunluk 24 karakter olarak ayarlanır.

Tüm dosyayı kopyalayın ve şablonunuzu içeriğiyle değiştirin.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json?range=1-26&highlight=4-10,15)]

## <a name="deploy-the-template"></a>Şablonu dağıtma

Şablonu dağıtalım. Aşağıdaki örnek, şablonu Azure CLı veya PowerShell ile dağıtır. Depolama hesabı adını dağıtım komutundaki değerlerden biri olarak sağladığınızdan emin olun. Depolama hesabı adı için, önceki öğreticide kullandığınız adı belirtin.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](template-tutorial-create-first-template.md#create-resource-group). Örnek, **TemplateFile** değişkenini, [ilk öğreticide](template-tutorial-create-first-template.md#deploy-template)gösterildiği gibi şablon dosyası yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Kaynak güncelleştirmelerini anlama

Önceki bölümde, daha önce oluşturduğunuz aynı ada sahip bir depolama hesabı dağıttınız. Kaynağın yeniden dağıtım tarafından nasıl etkilendiğini merak ediyor olabilirsiniz.

Kaynak zaten varsa ve özelliklerde hiçbir değişiklik algılanırsa hiçbir işlem yapılmaz. Kaynak zaten varsa ve bir özellik değiştiyse, kaynak güncellenir. Kaynak yoksa, oluşturulur.

Güncelleştirmeleri işlemenin bu yolu, şablonunuz bir Azure çözümü için ihtiyaç duyduğunuz tüm kaynakları içerebileceği anlamına gelir. Şablonu güvenle yeniden gönderebilir ve kaynakların değiştirildiğini veya yalnızca gerektiğinde oluşturulduğunu bilirsiniz. Örneğin, depolama hesabınıza dosya eklediyseniz depolama hesabını bu dosyaları kaybetmeden yeniden dağıtabilirsiniz.

## <a name="customize-by-environment"></a>Ortama göre özelleştirme

Parametreler, belirli bir ortam için tasarlanmış değerler kullanarak dağıtımı özelleştirmenizi sağlar. Örneğin, geliştirme, test ve üretim için bir ortama dağıtıp dağıtsanız farklı değerleri geçirebilirsiniz.

Önceki şablon her zaman bir Standard_LRS depolama hesabı dağıttı. Ortama bağlı olarak farklı SKU 'Ları dağıtma esnekliği isteyebilirsiniz. Aşağıdaki örnek, SKU için bir parametre eklemek için yapılan değişiklikleri gösterir. Tüm dosyayı kopyalayın ve şablonunuzun üzerine yapıştırın.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json?range=1-40&highlight=10-23,32)]

**Storagesku** parametresinin varsayılan bir değeri vardır. Bu değer, dağıtım sırasında bir değer belirtilmediğinde kullanılır. Ayrıca, izin verilen değerlerin bir listesini içerir. Bu değerler, bir depolama hesabı oluşturmak için gereken değerlerle eşleşir. Şablon kullanıcılarının çalışmayan SKU 'Larda geçmesini istemezsiniz.

## <a name="redeploy-the-template"></a>Şablonu yeniden dağıtın

Yeniden dağıtım için hazırsınız. Varsayılan SKU **Standard_LRS**olarak ayarlandığı için, bu parametre için bir değer sağlamanız gerekmez.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

Şablonunuzun esnekliğini görmek için yeniden dağıtmanıza izin verin. Bu kez, SKU parametresini **Standard_GRS**olarak ayarlayın. Farklı bir depolama hesabı oluşturmak için yeni bir ad geçirebilir ya da mevcut depolama hesabınızı güncelleştirmek için aynı adı kullanabilirsiniz. Her iki seçenek de çalışır.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usedefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name usedefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Son olarak, bir test çalıştıralım ve izin verilen değerlerden biri olmayan bir SKU 'ya geçirdiğinizde ne olacağını görelim. Bu durumda, şablonunuzda bir kullanıcının **temel** olarak SKU 'lardan biri olduğu senaryoya test ederiz.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
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

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **Kaynak grubunu sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Parametreleri ekleyerek [ilk öğreticide](template-tutorial-create-first-template.md) oluşturulan şablonu geliştirdik. Sonraki öğreticide, Şablon işlevleri hakkında bilgi edineceksiniz.

> [!div class="nextstepaction"]
> [Şablon işlevleri ekleme](template-tutorial-add-functions.md)
