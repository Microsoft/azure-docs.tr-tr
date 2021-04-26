---
title: Öğretici-hızlı başlangıç şablonlarını kullanma
description: Şablon geliştirmeyi tamamlamaya yönelik Azure hızlı başlangıç şablonlarını nasıl kullanacağınızı öğrenin.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 46b32ae7aeb971c9391a69e3ca3d01f669774248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97106912"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Öğretici: Azure hızlı başlangıç şablonlarını kullanma

[Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/) , topluluk tarafından katkıda bulunulan şablonların bir deposudur. Şablon geliştirmede örnek şablonları kullanabilirsiniz. Bu öğreticide bir Web sitesi kaynak tanımı bulur ve kendi şablonunuza eklersiniz. Yaklaşık **12 dakika** sürer.

## <a name="prerequisites"></a>Önkoşullar

[İçe aktarılmış şablonlar hakkında öğreticiyi](template-tutorial-export-template.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Kaynak Yöneticisi Araçları uzantısı ve Azure PowerShell ya da Azure CLı ile Visual Studio Code olması gerekir. Daha fazla bilgi için bkz. [şablon araçları](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Şablonu gözden geçir

Önceki öğreticinin sonunda, şablonunuz aşağıdaki JSON 'a sahipti:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Bu şablon, depolama hesapları ve App Service planlarını dağıtmak için geçerlidir, ancak buna bir Web sitesi eklemek isteyebilirsiniz. Önceden oluşturulmuş şablonları kullanarak bir kaynağı dağıtmak için gereken JSON 'ı hızlıca bulabilirsiniz.

## <a name="find-template"></a>Şablon bul

1. [Azure hızlı başlangıç şablonlarını](https://azure.microsoft.com/resources/templates/) aç
1. **Ara**' ya _Linux Web uygulaması dağıtma_ yazın.
1. **Temel bir Linux Web uygulaması dağıtan** başlıklı kutucuğu seçin. Bulmakta sorun yaşıyorsanız, bu [doğrudan bağlantı](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/)' ya tıklayın.
1. **GitHub 'Da Araştır '** ı seçin.
1. _azuredeploy.js_ seçin.
1. Şablonu gözden geçirin. Özellikle, `Microsoft.Web/sites` kaynağı arayın.

    ![Kaynak Yöneticisi şablonu Hızlı Başlangıç Web sitesi](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Mevcut şablonu gözden geçir

Hızlı başlangıç şablonunu mevcut şablonla birleştirin:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

Web uygulaması adının Azure genelinde benzersiz olması gerekir. Yinelenen adlara sahip olmasını engellemek için, `webAppPortalName` değişkeni ' dan ' a `"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"` güncelleştirilmiştir `"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"` .

Tanımın sonuna bir virgül ekleyerek `Microsoft.Web/serverfarms` kaynak tanımını `Microsoft.Web/sites` tanımdan ayırın.

Bu yeni kaynakta dikkat edilmesi için birkaç önemli özellik vardır.

App Service planı olarak ayarlanan adlı bir öğe olduğunu fark edeceksiniz `dependsOn` . Bu ayar, Web uygulaması oluşturulmadan önce App Service planının mevcut olması gerektiği için gereklidir. `dependsOn`Öğesi, kaynakları dağıtım için sıralamayı Kaynak Yöneticisi söyler.

`serverFarmId`Özelliği [RESOURCEID](template-functions-resource.md#resourceid) işlevini kullanır. Bu işlev, bir kaynağın benzersiz tanımlayıcısını alır. Bu durumda, App Service planının benzersiz tanımlayıcısını alır. Web uygulaması, belirli bir App Service planıyla ilişkilidir.

## <a name="deploy-template"></a>Şablon dağıtma

Bir şablonu dağıtmak için Azure CLı veya Azure PowerShell kullanın.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](template-tutorial-create-first-template.md#create-resource-group). Örnek, **TemplateFile** değişkenini, [ilk öğreticide](template-tutorial-create-first-template.md#deploy-template)gösterildiği gibi şablon dosyası yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.

```azurecli
az deployment group create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Dağıtım başarısız olursa, `verbose` oluşturulan kaynaklarla ilgili bilgi almak için anahtarını kullanın. `debug`Hata ayıklama hakkında daha fazla bilgi edinmek için anahtarını kullanın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçiş yapıyorsanız, kaynak grubunu silmeniz gerekmez.

Şimdi duruyorsa, kaynak grubunu silerek dağıttığınız kaynakları temizlemeniz gerekebilir.

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Şablon geliştirme için hızlı başlangıç şablonu kullanmayı öğrendiniz. Sonraki öğreticide, kaynaklara Etiketler eklersiniz.

> [!div class="nextstepaction"]
> [Etiket ekleme](template-tutorial-add-tags.md)
