---
title: Öğretici-Azure Resource Manager bicep dosyasını dağıtmak için parametre dosyası kullanma
description: Bıcep dosyanızı dağıtmak için kullanılacak değerleri içeren parametre dosyalarını kullanın.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ca3a73cde9549bfcdfd47bc4f1955904fac69d1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632365"
---
# <a name="tutorial-use-parameter-files-to-deploy-azure-resource-manager-bicep-file"></a>Öğretici: Azure Resource Manager bicep dosyasını dağıtmak için parametre dosyalarını kullanma

Bu öğreticide, dağıtım sırasında geçirdiğiniz değerleri depolamak için [parametre dosyalarını](parameter-files.md) nasıl kullanacağınızı öğreneceksiniz. Önceki öğreticilerde, dağıtım komutunuz ile satır içi parametreleri kullandınız. Bu yaklaşım Bıcep dosyanızı test etmeye çalıştı, ancak dağıtımları otomatikleştirmede ortamınız için bir değer kümesini geçirmek daha kolay olabilir. Parametre dosyaları belirli bir ortam için parametre değerlerini paketlemeyi kolaylaştırır. JSON şablonu dağıtırken aynı JSON parametre dosyasını kullanırsınız. Bu öğreticide geliştirme ve üretim ortamları için parametre dosyaları oluşturacaksınız. Yaklaşık **12 dakika** sürer.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

[Etiketler hakkında öğreticiyi](bicep-tutorial-add-tags.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Bıcep uzantılı Visual Studio Code ve Azure PowerShell ya da Azure CLı ile aynı olmalıdır. Daha fazla bilgi için bkz. [Bıcep araçları](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Bıcep dosyasını gözden geçirme

Bıcep dosyanızda dağıtım sırasında sağlayabilmeniz için birçok parametre vardır. Önceki öğreticinin sonunda, Bıcep dosyanız şu şekilde aranır:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Bu Bıcep dosyası iyi şekilde çalışmaktadır, ancak şimdi Bıcep dosyası için geçirdiğiniz parametreleri kolayca yönetmek istiyorsunuz.

## <a name="add-parameter-files"></a>Parametre dosyaları Ekle

Parametre dosyaları JSON şablonlarına benzer bir yapıya sahip JSON dosyalarıdır. Dosyasında, dağıtım sırasında geçirmek istediğiniz parametre değerlerini sağlarsınız.

Parametre dosyası içinde Bıcep dosyanızdaki parametreler için değerler sağlarsınız. Parametre dosyanızdaki her parametrenin adı, Bıcep dosyanızdaki bir parametrenin adıyla eşleşmelidir. Ad, büyük/küçük harfe duyarsızdır ancak eşleşen değerleri kolayca görmek için Bıcep dosyasındaki büyük küçük harf ile eşleştirmenizi öneririz.

Her parametre için bir değer sağlamanız gerekmez. Belirtilmeyen bir parametre varsayılan değere sahipse, bu değer dağıtım sırasında kullanılır. Bir parametre varsayılan değere sahip değilse ve parametre dosyasında belirtilmemişse, dağıtım sırasında bir değer sağlamanız istenir.

Parametre dosyanızda, Bıcep dosyasındaki bir parametre adıyla eşleşmeyen bir parametre adı belirtemezsiniz. Bilinmeyen parametreler sağlandığında bir hata alırsınız.

Visual Studio Code ' de, aşağıdaki içerikle yeni bir dosya oluşturun. Dosyayı _azuredeploy.parameters.dev.js_ adı ile kaydedin.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Bu dosya, geliştirme ortamı için parametre dosyasıdır. Depolama hesabı için **Standard_LRS** kullandığına, kaynakları **dev** ön ekine göre isimettiğini ve `Environment` etiketini **dev** olarak ayarladığına dikkat edin.

Yine, aşağıdaki içerikle yeni bir dosya oluşturun. Dosyayı _azuredeploy.parameters.prod.js_ adı ile kaydedin.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Bu dosya, üretim ortamı için parametre dosyasıdır. Depolama hesabı için **Standard_GRS** kullandığından, kaynakları **contoso** ön ekine göre adlandırır ve `Environment` etiketi **Üretim** olarak ayarlıyor olduğuna dikkat edin. Gerçek bir üretim ortamında, ücretsiz olarak aynı SKU ile bir App Service kullanmak isteyeceksiniz, ancak bu öğretici için bu SKU 'YU kullanmaya devam edeceğiz.

## <a name="deploy-bicep-file"></a>Bıcep dosyasını dağıtma

Bıcep dosyasını dağıtmak için Azure CLı veya Azure PowerShell kullanın.

Bu öğreticide, iki yeni kaynak grubu oluşturalım. Bir geliştirici ortamı ve bir üretim ortamı için bir tane.

Şablon ve parametre değişkenleri için,,, `{path-to-the-bicep-file}` `{path-to-azuredeploy.parameters.dev.json}` `{path-to-azuredeploy.parameters.prod.json}` ve küme ayraçları `{}` ile bıcep dosya ve parametre dosyası yollarıyla değiştirin.

İlk olarak geliştirme ortamına dağıtırsınız.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bu dağıtım cmdlet 'ini çalıştırmak için Azure PowerShell [en son sürümüne](/powershell/azure/install-az-ps) sahip olmanız gerekir.

```azurepowershell
$bicepFile = "{path-to-the-bicep-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $bicepFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.

```azurecli
bicepFile="{path-to-the-bicep-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $bicepFile \
  --parameters $devParameterFile
```

---

Şimdi üretim ortamına dağıtacağız.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> Dağıtım başarısız olursa, `verbose` oluşturulan kaynaklarla ilgili bilgi almak için anahtarını kullanın. `debug`Hata ayıklama hakkında daha fazla bilgi edinmek için anahtarını kullanın.

## <a name="verify-deployment"></a>Dağıtımı doğrulama

Kaynak gruplarını Azure portal inceleyerek dağıtımı doğrulayabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol menüden **kaynak grupları**' nı seçin.
1. Bu öğreticide dağıttığınız iki yeni kaynak grubunu görürsünüz.
1. Kaynak grubu ' nu seçin ve dağıtılan kaynakları görüntüleyin. Bu ortam için parametre dosyanızda belirttiğiniz değerlerle eşleştiğine dikkat edin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Bıcep dosyanızı dağıtmak için bir parametre dosyası kullanırsınız. Sonraki öğreticide, Bıcep dosyalarınızı nasıl modüle kullanacağınızı öğreneceksiniz.

> [!div class="nextstepaction"]
> [Modül ekle](./bicep-tutorial-add-modules.md)
