---
title: Öğretici-şablona değişken ekleme
description: Sözdizimini basitleştirmek için Azure Resource Manager şablonunuza değişken ekleyin.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 9af6b9028dbc5b01c3d0ec9dc41f145e37c31b0e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147968"
---
# <a name="tutorial-add-variables-to-your-resource-manager-template"></a>Öğretici: Kaynak Yöneticisi şablonunuza değişkenler ekleme

Bu öğreticide, şablonunuza nasıl değişken ekleneceğini öğrenirsiniz. Değişkenler, bir ifadeyi bir kez yazmanızı ve şablon boyunca yeniden kullanılmasını sağlayarak şablonlarınızı basitleştirir. Bu öğreticinin tamamlana **7 dakika** sürer.

## <a name="prerequisites"></a>Önkoşullar

[İşlevlerle ilgili öğreticiyi](template-tutorial-add-functions.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Kaynak Yöneticisi Araçları uzantısı ve Azure PowerShell ya da Azure CLı ile Visual Studio Code olması gerekir. Daha fazla bilgi için bkz. [şablon araçları](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Şablonunuzu gözden geçirin

Önceki öğreticinin sonunda, şablonunuz aşağıdaki JSON 'a sahipti:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json)]

Benzersiz bir ad sağlamanız gerektiğinden, depolama hesabı adı için parametre kullanımı zor. Bu serideki önceki öğreticileri tamamladıysanız, büyük olasılıkla benzersiz bir ad tahmin etmeniz çok yorsunuzdur. Depolama hesabı için benzersiz bir ad oluşturan bir değişken ekleyerek bu sorunu çözebilirsiniz.

## <a name="use-variable"></a>Değişken kullan

Aşağıdaki örnek, şablonunuza benzersiz bir depolama hesabı adı oluşturan bir değişken ekleme değişikliklerini vurgular. Tüm dosyayı kopyalayın ve şablonunuzu içeriğiyle değiştirin.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json?range=1-47&highlight=5-9,29-31,36)]

**Unisorgtoraygename**adlı bir değişken içerdiğine dikkat edin. Bu değişken bir dize değeri oluşturmak için dört işlevi kullanır.

[Parameters](resource-group-template-functions-deployment.md#parameters) işlevini zaten öğreniyoruz, bu yüzden bunu inceleyeceğiz.

Ayrıca, [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) işlevi hakkında bilgi sahibisiniz. Bu durumda, önceki öğreticide gösterildiği gibi **Location** özelliği yerine **ID** özelliğini alırsınız. **ID** özelliği, abonelik kimliği ve kaynak grubu adı dahil olmak üzere kaynak grubunun tam tanımlayıcısını döndürür.

[Uniquestring](resource-group-template-functions-string.md#uniquestring) işlevi bir 13 karakterlik karma değeri oluşturur. Döndürülen değer geçirdiğiniz parametrelere göre belirlenir. Bu öğreticide, karma değer girişi olarak kaynak grubu KIMLIĞINI kullanırsınız. Bu, bu şablonu farklı kaynak gruplarına dağıtabileceğiniz ve farklı bir benzersiz dize değeri alabileceğiniz anlamına gelir. Ancak, aynı kaynak grubuna dağıtırsanız aynı değeri alırsınız.

[Concat](resource-group-template-functions-string.md#concat) işlevi değerleri alır ve bunları birleştirir. Bu değişken için, dizeden ve uniqueString işlevindeki dizeden dize alır ve bunları tek bir dize olarak birleştirir.

**Storageprefix** parametresi, depolama hesaplarını belirlemenize yardımcı olan bir ön ek geçirmenize olanak sağlar. Depolama hesaplarının uzun bir kaynak listesinden dağıtımdan sonra tanımlanmasını kolaylaştıran kendi adlandırma kuralınızın oluşturulmasını sağlayabilirsiniz.

Son olarak, depolama adının bir parametre yerine artık değişkene ayarlı olduğuna dikkat edin.

## <a name="deploy-the-template"></a>Şablonu dağıtma

Şablonu dağıtalım. Yalnızca depolama adı için ön ek sağlamanız gerektiğinden, bu şablonu dağıtmak önceki şablonlardan daha kolaydır.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](template-tutorial-create-first-template.md#create-resource-group). Örnek, **TemplateFile** değişkenini, [ilk öğreticide](template-tutorial-create-first-template.md#deploy-template)gösterildiği gibi şablon dosyası yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-the-deployment"></a>Dağıtımı doğrulama

Kaynak grubunu Azure portal inceleyerek dağıtımı doğrulayabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol menüden **kaynak grupları**' nı seçin.
1. Dağıttığınız kaynak grubunu seçin.
1. Bir depolama hesabı kaynağının dağıtıldığını görürsünüz. Depolama hesabının adı, **Depo** ve rastgele karakterlerden oluşan bir dizedir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçiş yapıyorsanız, kaynak grubunu silmeniz gerekmez.

Şimdi duruyorsa, kaynak grubunu silerek dağıttığınız kaynakları temizlemeniz gerekebilir.

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **Kaynak grubunu sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, depolama hesabı için benzersiz bir ad oluşturan bir değişken eklediniz. Sonraki öğreticide, dağıtılan depolama hesabından bir değer döndürürler.

> [!div class="nextstepaction"]
> [Çıkış Ekle](template-tutorial-add-outputs.md)
