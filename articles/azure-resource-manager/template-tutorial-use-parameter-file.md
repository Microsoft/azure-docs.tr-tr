---
title: Öğretici-şablon dağıtmak için parametre dosyası kullanma
description: Azure Resource Manager şablonunuzu dağıtmak için kullanılacak değerleri içeren parametre dosyalarını kullanın.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7ebf8a3eed81c8f5233f7212df7e245a27f7fd16
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149170"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-resource-manager-template"></a>Öğretici: Kaynak Yöneticisi şablonunuzu dağıtmak için parametre dosyalarını kullanma

Bu öğreticide, dağıtım sırasında geçirdiğiniz değerleri depolamak için [parametre dosyalarını](resource-manager-parameter-files.md) nasıl kullanacağınızı öğreneceksiniz. Önceki öğreticilerde, dağıtım komutunuz ile satır içi parametreleri kullandınız. Bu yaklaşım şablonunuzun test edilmesine çalıştı, ancak dağıtımları otomatikleştirmede ortamınız için bir değer kümesini geçirmek daha kolay olabilir. Parametre dosyaları belirli bir ortam için parametre değerlerini paketlemeyi kolaylaştırır. Bu öğreticide geliştirme ve üretim ortamları için parametre dosyaları oluşturacaksınız. Yaklaşık **12 dakika** sürer.

## <a name="prerequisites"></a>Önkoşullar

[Etiketler hakkında öğreticiyi](template-tutorial-add-tags.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Kaynak Yöneticisi Araçları uzantısı ve Azure PowerShell ya da Azure CLı ile Visual Studio Code olması gerekir. Daha fazla bilgi için bkz. [şablon araçları](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Şablonunuzu gözden geçirin

Şablonunuzda dağıtım sırasında sağlayabilmeniz için birçok parametre vardır. Önceki öğreticinin sonunda şablonunuz şöyle aranır:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json)]

Bu şablon iyi sonuç verir, ancak artık şablon için geçirdiğiniz parametreleri kolayca yönetmek istiyorsunuz.

## <a name="add-parameter-files"></a>Parametre dosyaları Ekle

Parametre dosyaları, şablonunuza benzer bir yapıya sahip JSON dosyalarıdır. Dosyasında, dağıtım sırasında geçirmek istediğiniz parametre değerlerini sağlarsınız.

VS Code ' de, aşağıdaki içerikle yeni bir dosya oluşturun. Dosyayı **azuredeploy. Parameters. dev. JSON**adıyla kaydedin.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json)]

Bu dosya, geliştirme ortamı için parametre dosyasıdır. Depolama hesabı için Standard_LRS kullandığına, kaynakları **dev** ön ekine göre isimettiğini ve **ortam** etiketini **dev**olarak ayarladığına dikkat edin.

Yine, aşağıdaki içerikle yeni bir dosya oluşturun. Dosyayı **azuredeploy. Parameters. prod. JSON**adıyla kaydedin.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json)]

Bu dosya, üretim ortamı için parametre dosyasıdır. Depolama hesabı için Standard_GRS kullandığına, kaynakları **contoso** ön ekine göre isimettiğini ve **ortam** etiketini **Üretim**olarak ayarladığına dikkat edin. Gerçek bir üretim ortamında, ücretsiz olarak aynı SKU ile bir App Service kullanmak isteyeceksiniz, ancak bu öğretici için bu SKU 'YU kullanmaya devam edeceğiz.

## <a name="deploy-the-template"></a>Şablonu dağıtma

Şablonu dağıtmak için Azure CLı veya Azure PowerShell kullanın.

Şablonunuzun son testi olarak iki yeni kaynak grubu oluşturalım. Bir geliştirici ortamı ve bir üretim ortamı için bir tane.

İlk olarak geliştirme ortamına dağıtırsınız.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az group deployment create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.dev.json
```

---

Şimdi üretim ortamına dağıtacağız.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroupProd \
  --location "West US"
az group deployment create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.prod.json
```

---

## <a name="verify-the-deployment"></a>Dağıtımı doğrulama

Kaynak gruplarını Azure portal inceleyerek dağıtımı doğrulayabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol menüden **kaynak grupları**' nı seçin.
1. Bu öğreticide dağıttığınız iki yeni kaynak grubunu görürsünüz.
1. Kaynak grubu ' nu seçin ve dağıtılan kaynakları görüntüleyin. Bu ortam için parametre dosyanızda belirttiğiniz değerlerle eşleştiğine dikkat edin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin. Bu seriyi tamamladıysanız, silinecek üç kaynak grubunuz vardır-myResourceGroup, myResourceGroupDev ve myResourceGroupProd.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **Kaynak grubunu sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Tebrikler, Azure 'a şablon dağıtmaya yönelik bu girişi tamamladınız. Geri bildirim bölümünde herhangi bir yorum ve öneri varsa bize bilgi verin. Teşekkür ederiz!

Şablonlar hakkında daha gelişmiş kavramlara geçmek için hazırsınız. Sonraki öğreticide, dağıtılacak kaynakları tanımlamaya yardımcı olması için şablon başvuru belgelerinin kullanılmasıyla ilgili daha fazla ayrıntıya gidersiniz.

> [!div class="nextstepaction"]
> [Şablon başvurusunda yararlanma](resource-manager-tutorial-create-encrypted-storage-accounts.md)
