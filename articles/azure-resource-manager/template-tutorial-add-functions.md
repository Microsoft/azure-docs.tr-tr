---
title: Öğretici-şablon işlevleri ekleme
description: Değerler oluşturmak için Azure Resource Manager şablonunuza şablon işlevleri ekleyin.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fe571c2a0088375feff8351f49a476669461b6aa
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150258"
---
# <a name="tutorial-add-template-functions-to-your-resource-manager-template"></a>Öğretici: Kaynak Yöneticisi şablonunuza şablon işlevleri ekleme

Bu öğreticide, şablonunuza [Şablon işlevleri](resource-group-template-functions.md) eklemeyi öğreneceksiniz. Değerleri dinamik olarak oluşturmak için işlevlerini kullanırsınız. Sistem tarafından sağlanmış bu şablon işlevlerine ek olarak, [Kullanıcı tanımlı işlevler](./template-user-defined-functions.md)de oluşturabilirsiniz. Bu öğreticinin tamamlana **7 dakika** sürer.

## <a name="prerequisites"></a>Önkoşullar

[Parametreler hakkında öğreticiyi](template-tutorial-add-parameters.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Kaynak Yöneticisi Araçları uzantısı ve Azure PowerShell ya da Azure CLı ile Visual Studio Code olması gerekir. Daha fazla bilgi için bkz. [şablon araçları](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Şablonunuzu gözden geçirin

Önceki öğreticinin sonunda, şablonunuz aşağıdaki JSON 'a sahipti:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json)]

Depolama hesabının konumu **Doğu ABD**için sabit olarak kodlanmıştır. Ancak, depolama hesabını diğer bölgelere dağıtmanız gerekebilir. Daha fazla esneklik olmayan şablonunuzda bir sorunla karşılaşıyoruz. Konum için bir parametre ekleyebilirsiniz, ancak varsayılan değeri yalnızca sabit kodlanmış bir değerden daha anlamlı olursa harika olur.

## <a name="use-function"></a>Use işlevi

Bu serinin önceki öğreticisini tamamladıysanız, zaten bir işlev kullandınız. **"[Parameters (' storageName ')]"** eklediğinizde [Parameters](resource-group-template-functions-deployment.md#parameters) işlevini kullandınız. Köşeli ayraçlar, köşeli ayracın içindeki sözdiziminin bir [şablon ifadesi](template-expressions.md)olduğunu gösterir. Kaynak Yöneticisi, sözdizimini değişmez değer olarak kabul etmek yerine, sözdizimini çözer.

İşlevler, dağıtım sırasında değerleri dinamik olarak alarak şablonunuz için esneklik ekler. Bu öğreticide, dağıtım için kullanmakta olduğunuz kaynak grubunun konumunu almak üzere bir işlev kullanırsınız.

Aşağıdaki örnek, **konum**adlı bir parametre ekleme değişikliklerini vurgular.  Parametre varsayılan değeri [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) işlevini çağırır. Bu işlev, dağıtım için kullanılan kaynak grubuyla ilgili bilgileri içeren bir nesne döndürür. Nesnedeki özelliklerden biri bir konum özelliğidir. Varsayılan değeri kullandığınızda, depolama hesabı konumu kaynak grubuyla aynı konuma sahiptir. Kaynak grubu içindeki kaynakların aynı konumu paylaşması gerekmez. Gerektiğinde farklı bir konum da sağlayabilirsiniz.

Tüm dosyayı kopyalayın ve şablonunuzu içeriğiyle değiştirin.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json?range=1-44&highlight=24-27,34)]

## <a name="deploy-template"></a>Şablon dağıtma

Önceki öğreticilerde, Doğu ABD bir depolama hesabı oluşturdunuz, ancak kaynak grubunuz Orta ABD oluşturulmuş. Bu öğreticide, depolama hesabınız kaynak grubuyla aynı bölgede oluşturulur. Konum için varsayılan değeri kullanın, bu nedenle bu parametre değerini belirtmeniz gerekmez. Farklı bir konumda depolama hesabı oluşturmakta olduğunuz için depolama hesabı için yeni bir ad sağlamalısınız. Örneğin, **store1**yerine ön ek olarak **store2** kullanın.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](template-tutorial-create-first-template.md#create-resource-group). Örnek, **TemplateFile** değişkenini, [ilk öğreticide](template-tutorial-create-first-template.md#deploy-template)gösterildiği gibi şablon dosyası yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

## <a name="verify-the-deployment"></a>Dağıtımı doğrulama

Kaynak grubunu Azure portal inceleyerek dağıtımı doğrulayabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol menüden **kaynak grupları**' nı seçin.
1. Dağıttığınız kaynak grubunu seçin.
1. Bir depolama hesabı kaynağının dağıtıldığını ve kaynak grubuyla aynı konuma sahip olduğunu görürsünüz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçiş yapıyorsanız, kaynak grubunu silmeniz gerekmez.

Şimdi duruyorsa, kaynak grubunu silerek dağıttığınız kaynakları temizlemeniz gerekebilir.

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **Kaynak grubunu sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir parametre için varsayılan değeri tanımlarken bir işlev kullandınız. Bu öğretici serisinde, işlevleri kullanmaya devam edersiniz. Serinin sonuna kadar, şablonun her bölümüne işlevler ekleyeceksiniz.

> [!div class="nextstepaction"]
> [Değişken Ekle](template-tutorial-add-variables.md)
