---
title: Öğretici-şablon işlevleri ekleme
description: Değer oluşturmak için Azure Resource Manager şablonunuza (ARM şablonu) şablon işlevleri ekleyin.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 52b5bd0650b3a069adc3ef7f101c48a4674deaab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97107116"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>Öğretici: ARM şablonunuza şablon işlevleri ekleme

Bu öğreticide, Azure Resource Manager şablonunuza [Şablon işlevleri](template-functions.md) eklemeyi ÖĞRENECEKSINIZ (ARM şablonu). Değerleri dinamik olarak oluşturmak için işlevlerini kullanırsınız. Sistem tarafından sağlanmış bu şablon işlevlerine ek olarak, [Kullanıcı tanımlı işlevler](./template-user-defined-functions.md)de oluşturabilirsiniz. Bu öğreticinin tamamlana **7 dakika** sürer.

## <a name="prerequisites"></a>Önkoşullar

[Parametreler hakkında öğreticiyi](template-tutorial-add-parameters.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Kaynak Yöneticisi Araçları uzantısı ve Azure PowerShell ya da Azure CLı ile Visual Studio Code olması gerekir. Daha fazla bilgi için bkz. [şablon araçları](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Şablonu gözden geçir

Önceki öğreticinin sonunda, şablonunuz aşağıdaki JSON 'a sahipti:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

Depolama hesabının konumu **Doğu ABD** için sabit olarak kodlanmıştır. Ancak, depolama hesabını diğer bölgelere dağıtmanız gerekebilir. Daha fazla esneklik olmayan şablonunuzda bir sorunla karşılaşıyoruz. Konum için bir parametre ekleyebilirsiniz, ancak varsayılan değeri yalnızca sabit kodlanmış bir değerden daha anlamlı olursa harika olur.

## <a name="use-function"></a>Use işlevi

Bu serinin önceki öğreticisini tamamladıysanız, zaten bir işlev kullandınız. Eklediğinizde `"[parameters('storageName')]"` [Parameters](template-functions-deployment.md#parameters) işlevini kullandınız. Köşeli ayraçlar, köşeli ayracın içindeki sözdiziminin bir [şablon ifadesi](template-expressions.md)olduğunu gösterir. Kaynak Yöneticisi, sözdizimini değişmez değer olarak kabul etmek yerine, sözdizimini çözer.

İşlevler, dağıtım sırasında değerleri dinamik olarak alarak şablonunuz için esneklik ekler. Bu öğreticide, dağıtım için kullanmakta olduğunuz kaynak grubunun konumunu almak üzere bir işlev kullanırsınız.

Aşağıdaki örnek, adlı bir parametre ekleme değişikliklerini vurgular `location` . Parametre varsayılan değeri [resourceGroup](template-functions-resource.md#resourcegroup) işlevini çağırır. Bu işlev, dağıtım için kullanılan kaynak grubuyla ilgili bilgileri içeren bir nesne döndürür. Nesnedeki özelliklerden biri bir konum özelliğidir. Varsayılan değeri kullandığınızda, depolama hesabı konumu kaynak grubuyla aynı konuma sahiptir. Kaynak grubu içindeki kaynakların aynı konumu paylaşması gerekmez. Gerektiğinde farklı bir konum da sağlayabilirsiniz.

Tüm dosyayı kopyalayın ve şablonunuzu içeriğiyle değiştirin.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>Şablon dağıtma

Önceki öğreticilerde, Doğu ABD bir depolama hesabı oluşturdunuz, ancak kaynak grubunuz Orta ABD oluşturulmuş. Bu öğreticide, depolama hesabınız kaynak grubuyla aynı bölgede oluşturulur. Konum için varsayılan değeri kullanın, bu nedenle bu parametre değerini belirtmeniz gerekmez. Farklı bir konumda depolama hesabı oluşturmakta olduğunuz için depolama hesabı için yeni bir ad sağlamalısınız. Örneğin, **store1** yerine ön ek olarak **store2** kullanın.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](template-tutorial-create-first-template.md#create-resource-group). Örnek, `templateFile` [ilk öğreticide](template-tutorial-create-first-template.md#deploy-template)gösterildiği gibi, değişkeni şablon dosyası yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
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

Bu öğreticide, bir parametre için varsayılan değeri tanımlarken bir işlev kullandınız. Bu öğretici serisinde, işlevleri kullanmaya devam edersiniz. Serinin sonuna kadar, şablonun her bölümüne işlevler ekleyeceksiniz.

> [!div class="nextstepaction"]
> [Değişkenleri ekleme](template-tutorial-add-variables.md)
