---
title: Öğretici-şablon dağıtmak için parametre dosyası kullanma
description: Azure Resource Manager şablonunuzu dağıtmak için kullanılacak değerleri içeren parametre dosyalarını kullanın (ARM şablonu).
author: mumian
ms.date: 09/10/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: b6257161017afc9dab692c43fcc64e5d961a90ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97368435"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>Öğretici: ARM şablonunuzu dağıtmak için parametre dosyalarını kullanma

Bu öğreticide, dağıtım sırasında geçirdiğiniz değerleri depolamak için [parametre dosyalarını](parameter-files.md) nasıl kullanacağınızı öğreneceksiniz. Önceki öğreticilerde, dağıtım komutunuz ile satır içi parametreleri kullandınız. Bu yaklaşım Azure Resource Manager şablonunuzu (ARM şablonu) test etmeye çalıştı, ancak dağıtımları otomatikleştirmede ortamınız için bir değer kümesini geçirmek daha kolay olabilir. Parametre dosyaları belirli bir ortam için parametre değerlerini paketlemeyi kolaylaştırır. Bu öğreticide geliştirme ve üretim ortamları için parametre dosyaları oluşturacaksınız. Yaklaşık **12 dakika** sürer.

## <a name="prerequisites"></a>Önkoşullar

[Etiketler hakkında öğreticiyi](template-tutorial-add-tags.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Kaynak Yöneticisi Araçları uzantısı ve Azure PowerShell ya da Azure CLı ile Visual Studio Code olması gerekir. Daha fazla bilgi için bkz. [şablon araçları](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Şablonu gözden geçir

Şablonunuzda dağıtım sırasında sağlayabilmeniz için birçok parametre vardır. Önceki öğreticinin sonunda şablonunuz şöyle aranır:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Bu şablon iyi sonuç verir, ancak artık şablon için geçirdiğiniz parametreleri kolayca yönetmek istiyorsunuz.

## <a name="add-parameter-files"></a>Parametre dosyaları Ekle

Parametre dosyaları, şablonunuza benzer bir yapıya sahip JSON dosyalarıdır. Dosyasında, dağıtım sırasında geçirmek istediğiniz parametre değerlerini sağlarsınız.

Parametre dosyası içinde, şablonunuzda parametreler için değerler sağlarsınız. Parametre dosyanızdaki her parametrenin adı, şablonunuzda bir parametre adıyla eşleşmelidir. Ad, büyük/küçük harfe duyarsızdır ancak eşleşen değerleri kolayca görmek için şablondan büyük küçük harf eşleştirmenizi öneririz.

Her parametre için bir değer sağlamanız gerekmez. Belirtilmeyen bir parametre varsayılan değere sahipse, bu değer dağıtım sırasında kullanılır. Bir parametre varsayılan değere sahip değilse ve parametre dosyasında belirtilmemişse, dağıtım sırasında bir değer sağlamanız istenir.

Parametre dosyanızda, şablondaki bir parametre adıyla eşleşmeyen bir parametre adı belirtemezsiniz. Bilinmeyen parametreler sağlandığında bir hata alırsınız.

Visual Studio Code ' de, aşağıdaki içerikle yeni bir dosya oluşturun. Dosyayı _azuredeploy.parameters.dev.js_ adı ile kaydedin.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Bu dosya, geliştirme ortamı için parametre dosyasıdır. Depolama hesabı için **Standard_LRS** kullandığına, kaynakları **dev** ön ekine göre isimettiğini ve `Environment` etiketini **dev** olarak ayarladığına dikkat edin.

Yine, aşağıdaki içerikle yeni bir dosya oluşturun. Dosyayı _azuredeploy.parameters.prod.js_ adı ile kaydedin.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Bu dosya, üretim ortamı için parametre dosyasıdır. Depolama hesabı için **Standard_GRS** kullandığından, kaynakları **contoso** ön ekine göre adlandırır ve `Environment` etiketi **Üretim** olarak ayarlıyor olduğuna dikkat edin. Gerçek bir üretim ortamında, ücretsiz olarak aynı SKU ile bir App Service kullanmak isteyeceksiniz, ancak bu öğretici için bu SKU 'YU kullanmaya devam edeceğiz.

## <a name="deploy-template"></a>Şablon dağıtma

Şablonu dağıtmak için Azure CLı veya Azure PowerShell kullanın.

Şablonunuzun son testi olarak iki yeni kaynak grubu oluşturalım. Bir geliştirici ortamı ve bir üretim ortamı için bir tane.

Şablon ve parametre değişkenleri için,,, `{path-to-the-template-file}` `{path-to-azuredeploy.parameters.dev.json}` `{path-to-azuredeploy.parameters.prod.json}` ve küme ayraçları, `{}` şablon ve parametre dosyası yollarınız ile değiştirin.

İlk olarak geliştirme ortamına dağıtırsınız.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
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
2. **Ada göre filtrele** alanına kaynak grubu adını girin. Bu seriyi tamamladıysanız, silinecek üç kaynak grubunuz vardır- **Myresourcegroup**, **myresourcegroupdev** ve **myresourcegroupprod**.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Tebrikler, Azure 'a şablon dağıtmaya yönelik bu girişi tamamladınız. Geri bildirim bölümünde herhangi bir yorum ve öneri varsa bize bilgi verin. Teşekkürler!

Sonraki öğretici serisi, şablonları dağıtma hakkında daha fazla ayrıntıya gider.

> [!div class="nextstepaction"]
> [Yerel şablonu dağıtma](./deployment-tutorial-local-template.md)
