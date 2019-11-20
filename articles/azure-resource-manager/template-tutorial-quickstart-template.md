---
title: Öğretici-hızlı başlangıç şablonlarını kullanma
description: Şablon geliştirmeyi tamamlamaya yönelik Azure hızlı başlangıç şablonlarını nasıl kullanacağınızı öğrenin.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e38dfcd524a1b8fec8934a3a118147e358614a7b
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143745"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Öğretici: Azure hızlı başlangıç şablonlarını kullanma

[Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/) , topluluk tarafından katkıda bulunulan şablonların bir deposudur. Şablon geliştirmede örnek şablonları kullanabilirsiniz. Bu öğreticide bir Web sitesi kaynak tanımı bulur ve kendi şablonunuza eklersiniz. Yaklaşık **12 dakika** sürer.

## <a name="prerequisites"></a>Önkoşullar

[İçe aktarılmış şablonlar hakkında öğreticiyi](template-tutorial-export-template.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Kaynak Yöneticisi Araçları uzantısı ve Azure PowerShell ya da Azure CLı ile Visual Studio Code olması gerekir. Daha fazla bilgi için bkz. [şablon araçları](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Şablonunuzu gözden geçirin

Önceki öğreticinin sonunda, şablonunuz aşağıdaki JSON 'a sahipti:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json)]

Bu şablon, depolama hesapları ve App Service planlarını dağıtmak için geçerlidir, ancak buna bir Web sitesi eklemek isteyebilirsiniz. Önceden oluşturulmuş şablonları kullanarak bir kaynağı dağıtmak için gereken JSON 'ı hızlıca bulabilirsiniz.

## <a name="find-a-template"></a>Şablon bulma

1. [Azure hızlı başlangıç şablonlarını](https://azure.microsoft.com/resources/templates/) aç
1. **Ara**' ya **Linux Web uygulaması dağıtma**yazın.
1. **Temel bir Linux Web uygulaması dağıtma**başlığı ile birini seçin. Bulmakta sorun yaşıyorsanız, bu [doğrudan bağlantı](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/)' ya tıklayın.
1. **GitHub 'Da Araştır '** ı seçin.
1. **Azuredeploy. JSON**öğesini seçin.
1. Şablonu gözden geçirin. Özellikle `Microsoft.Web/sites` kaynağını arayın.

    ![Kaynak Yöneticisi şablonu Hızlı Başlangıç Web sitesi](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-the-existing-template"></a>Mevcut şablonu gözden geçir

Hızlı başlangıç şablonunu mevcut şablonla birleştirin:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json?range=1-108&highlight=32-45,49,85-100)]

WebApp adının Azure genelinde benzersiz olması gerekir. Bir yinelenen ad olmaması için, **webappportalname** değişkeni **"webappportalname": "[Concat (Parameters (' webappname '), '-WebApp ')]"** yerine **"webappportalname": "[Concat (Parametreler (' Webappname '), benzersiz dize (resourceGroup (). ID)]"** olarak güncelleştirilmiştir.

Kaynak tanımını `Microsoft.Web/sites` tanımından ayırmak için `Microsoft.Web/serverfarms` tanımının sonuna virgül ekleyin.

Bu yeni kaynakta dikkat edilmesi için birkaç önemli özellik vardır.

Burada, App Service planına ayarlanmış olan **Bağımlıdson** adlı bir öğe olduğunu fark edeceksiniz. Bu ayar, Web uygulaması oluşturulmadan önce App Service planının mevcut olması gerektiği için gereklidir. **Bağımlıdson** öğesi, kaynakları dağıtım için nasıl sıralacağını Kaynak Yöneticisi söyler.

**Serverfarmid** özelliği [RESOURCEID](resource-group-template-functions-resource.md#resourceid) işlevini kullanır. Bu işlev, bir kaynağın benzersiz tanımlayıcısını alır. Bu durumda, App Service planının benzersiz tanımlayıcısını alır. Web uygulaması, belirli bir App Service planıyla ilişkilidir.

## <a name="deploy-the-template"></a>Şablonu dağıtma

Bir şablonu dağıtmak için Azure CLı veya Azure PowerShell kullanın.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](template-tutorial-create-first-template.md#create-resource-group). Örnek, **TemplateFile** değişkenini, [ilk öğreticide](template-tutorial-create-first-template.md#deploy-template)gösterildiği gibi şablon dosyası yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçiş yapıyorsanız, kaynak grubunu silmeniz gerekmez.

Şimdi duruyorsa, kaynak grubunu silerek dağıttığınız kaynakları temizlemeniz gerekebilir.

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **Kaynak grubunu sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Şablon geliştirme için hızlı başlangıç şablonu kullanmayı öğrendiniz. Sonraki öğreticide, kaynaklara Etiketler eklersiniz.

> [!div class="nextstepaction"]
> [Etiket ekle](template-tutorial-add-tags.md)
