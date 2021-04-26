---
title: Öğretici-Azure Resource Manager bicep geliştirme için hızlı başlangıç şablonları kullanma
description: Azure hızlı başlangıç şablonlarını kullanarak Bıcep geliştirmeyi nasıl tamamlayacağınızı öğrenin.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: cf655885e01fe6bca99a82c82d6bbbc4c1a080b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632446"
---
# <a name="tutorial-use-azure-quickstart-templates-for-azure-resource-manager-bicep-development"></a>Öğretici: Azure hızlı başlangıç şablonlarını kullanarak bicep geliştirme Azure Resource Manager

[Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/) , topluluk tarafından KATKıDA bulunulan JSON şablonlarının bir deposudur. Bıcep geliştirmede örnek şablonları kullanabilirsiniz. Bu öğreticide, bir Web sitesi kaynak tanımı bulur, Bıcep 'de derlemeyi kaldırır ve Bıcep dosyanıza ekleyin. Yaklaşık **12 dakika** sürer.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

[İçe aktarılmış şablonlar hakkında öğreticiyi](bicep-tutorial-export-template.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Bıcep uzantılı Visual Studio Code ve Azure PowerShell ya da Azure CLı ile aynı olmalıdır. Daha fazla bilgi için bkz. [Bıcep araçları](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Bıcep dosyasını gözden geçirme

Önceki öğreticinin sonunda, Bıcep dosyanız aşağıdaki içeriğe sahiptir:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep":::

Bu bicep dosyası, depolama hesapları ve App Service planlarını dağıtmaya çalışarak, buna bir Web sitesi eklemek isteyebilirsiniz. Önceden oluşturulmuş şablonları kullanarak bir kaynağı dağıtmak için gereken JSON 'ı hızlıca bulabilirsiniz. Azure hızlı başlangıç şablonları Bıcep örnekleri sunmadan önce, JSON şablonlarını Bıcep dosyalarına dönüştürmek için dönüştürme araçları 'nı kullanabilirsiniz.

## <a name="find-template"></a>Şablon bul

Şu anda Azure hızlı başlangıç şablonları yalnızca JSON şablonları sağlar. JSON şablonlarını Bıcep şablonlarına derlemek için kullanabileceğiniz araçlar vardır.

1. [Azure hızlı başlangıç şablonlarını](https://azure.microsoft.com/resources/templates/) aç
1. **Ara**' ya _Linux Web uygulaması dağıtma_ yazın.
1. **Temel bir Linux Web uygulaması dağıtan** başlıklı kutucuğu seçin. Bulmakta sorun yaşıyorsanız, bu [doğrudan bağlantı](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/)' ya tıklayın.
1. **GitHub 'Da Araştır '** ı seçin.
1. _azuredeploy.js_ seçin. Bu, kullanabileceğiniz şablondur.
1. **RAW**' ı seçin ve ardından URL 'nin bir kopyasını oluşturun.
1. **https://bicepdemo.z22.web.core.windows.net/**' A gidin, **derlemeyi kaldırma**' yı seçin ve ham şablon URL 'sini sağlayın.
1. Bıcep şablonunu gözden geçirin. Özellikle, `Microsoft.Web/sites` kaynağı arayın.

    ![Kaynak Yöneticisi şablonu Hızlı Başlangıç Web sitesi](./media/bicep-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

    JSON şablonları üzerinde çalıştıysanız, bu yeni kaynağa dikkat etmeniz için birkaç önemli bicep özelliği vardır.

    ARM JSON şablonlarında, _bağımlı DSON_ özelliğiyle Kaynak bağımlılıklarını el ile belirtmeniz gerekir. Web sitesi kaynağı, App Service planı kaynağına bağlıdır. Bıcep ile, sembolik adı kullanarak kaynağın herhangi bir özelliğine başvurdıysanız, Bağımlıdson özelliği otomatik olarak eklenir.

    Kaynak KIMLIĞINE, derlenen JSON şablonundaki RESOURCEID (...) işlevine çevrilecek olan App Service planının (appServicePlanName.id) sembolik adından kolayca başvurabilirsiniz.

## <a name="revise-existing-bicep-file"></a>Mevcut bicep dosyasını gözden geçir

Ayrıştırılmış hızlı başlangıç şablonunu mevcut Bıcep dosyasıyla birleştirin. Önceki öğreticide yaptığınız gibi, kaynak sembolik adını ve kaynak adını adlandırma kurallarınız ile eşleşecek şekilde güncelleştirin.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep" range="1-73" highlight="20-25,28,61-71":::

Web uygulaması adının Azure genelinde benzersiz olması gerekir. Yinelenen adlara sahip olmasını engellemek için, `webAppPortalName` değişkeni ' dan ' a `var webAppPortalName_var = '${webAppName}-webapp'` güncelleştirilmiştir `var webAppPortalName = '${webAppName}${uniqueString(resourceGroup().id)}'` .

## <a name="deploy-bicep-file"></a>Bıcep dosyasını dağıtma

Bir Bıcep şablonu dağıtmak için Azure CLı veya Azure PowerShell kullanın.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](bicep-tutorial-create-first-bicep.md#create-resource-group). Örnek, **Bıcepfile** değişkenini [ilk öğreticide](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)gösterildiği gibi bıcep dosyasının yoluna ayarlamış olduğunuzu varsayar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bu dağıtım cmdlet 'ini çalıştırmak için Azure PowerShell [en son sürümüne](/powershell/azure/install-az-ps) sahip olmanız gerekir.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
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
  --template-file $bicepFile \
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

Bicep geliştirme için hızlı başlangıç şablonu kullanmayı öğrendiniz. Sonraki öğreticide, kaynaklara Etiketler eklersiniz.

> [!div class="nextstepaction"]
> [Etiket ekleme](bicep-tutorial-add-tags.md)
