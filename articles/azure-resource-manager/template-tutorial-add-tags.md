---
title: Öğretici-şablondaki kaynaklara etiketler ekleme
description: Azure Resource Manager şablonunuzda dağıttığınız kaynaklara Etiketler ekleyin. Etiketler, kaynakları mantıksal olarak düzenlemenizi sağlar.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 663de1e341815eac039c49cee63f8a492b0cb7a9
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150179"
---
# <a name="tutorial-add-tags-in-your-resource-manager-template"></a>Öğretici: Kaynak Yöneticisi şablonunuzda etiketler ekleme

Bu öğreticide, şablonunuzda kaynaklara nasıl etiket ekleneceğini öğreneceksiniz. [Etiketler](resource-group-using-tags.md) , kaynaklarınızı mantıksal olarak düzenlemenize yardımcı olur. Etiket değerleri, maliyet raporlarında gösterilir. Bu öğreticinin tamamlandığı **8 dakika** sürer.

## <a name="prerequisites"></a>Önkoşullar

[Hızlı başlangıç şablonları hakkında öğreticiyi](template-tutorial-quickstart-template.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Kaynak Yöneticisi Araçları uzantısı ve Azure PowerShell ya da Azure CLı ile Visual Studio Code olması gerekir. Daha fazla bilgi için bkz. [şablon araçları](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Şablonunuzu gözden geçirin

Önceki şablonunuz bir depolama hesabı, App Service planı ve Web uygulaması dağıttı.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json)]

Bu kaynakları dağıttıktan sonra, maliyetleri izlemeniz ve bir kategoriye ait kaynakları bulmanız gerekebilir. Bu sorunları çözmeye yardımcı olmak için Etiketler ekleyebilirsiniz.

## <a name="add-tags"></a>Etiket ekleme

Kullanımları tanımlamanızı sağlayacak değerler eklemek için kaynakları etiketlerseniz. Örneğin, ortamı ve projeyi listelemek için Etiketler ekleyebilirsiniz. Bir maliyet merkezini veya kaynağa sahip olan takımı tanımlayan etiketler ekleyebilirsiniz. Kuruluşunuz için anlamlı olan tüm değerleri ekleyin.

Aşağıdaki örnek, şablonda yapılan değişiklikleri vurgular. Tüm dosyayı kopyalayın ve şablonunuzu içeriğiyle değiştirin.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json?range=1-118&highlight=46-52,64,78,100)]

## <a name="deploy-the-template"></a>Şablonu dağıtma

Şablonun dağıtılması ve sonuçlara bakmaları zaman alabilir.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](template-tutorial-create-first-template.md#create-resource-group). Örnek, **TemplateFile** değişkenini, [ilk öğreticide](template-tutorial-create-first-template.md#deploy-template)gösterildiği gibi şablon dosyası yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="verify-the-deployment"></a>Dağıtımı doğrulama

Kaynak grubunu Azure portal inceleyerek dağıtımı doğrulayabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol menüden **kaynak grupları**' nı seçin.
1. Dağıttığınız kaynak grubunu seçin.
1. Depolama hesabı kaynağı gibi kaynaklardan birini seçin. Artık etiketlere sahip olduğunu görürsünüz.

   ![Etiketleri göster](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçiş yapıyorsanız, kaynak grubunu silmeniz gerekmez.

Şimdi duruyorsa, kaynak grubunu silerek dağıttığınız kaynakları temizlemeniz gerekebilir.

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **Kaynak grubunu sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, kaynaklara Etiketler eklediniz. Sonraki öğreticide, değerleri şablona geçirmeyi kolaylaştırmak için parametre dosyalarını nasıl kullanacağınızı öğreneceksiniz.

> [!div class="nextstepaction"]
> [Parametre dosyası kullan](template-tutorial-use-parameter-file.md)
