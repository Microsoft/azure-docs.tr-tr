---
title: Öğretici-Azure Resource Manager bicep dosyasına modül ekleme
description: Ham kaynak bildiriminin karmaşık ayrıntılarını kapsüllemek için modüller kullanın.
author: mumian
ms.date: 03/25/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8c7ab1038cbe62d6f15faf56796193df12b38546
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568775"
---
# <a name="tutorial-add-modules-to-azure-resource-manager-bicep-file"></a>Öğretici: Azure Resource Manager bicep dosyasına modüller ekleme

[Önceki öğreticide](bicep-tutorial-use-parameter-file.md), Bıcep dosyanızı dağıtmak için bir parametre dosyası kullanmayı öğrendiniz. Bu öğreticide, ham kaynak bildiriminin karmaşık ayrıntılarını kapsüllemek için Bıcep modüllerini nasıl kullanacağınızı öğreneceksiniz. Modüller, çözümünüz içinde paylaşılabilir ve yeniden kullanılabilir.  Yaklaşık **12 dakika** sürer.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

[Parametre dosyası hakkında öğreticiyi](bicep-tutorial-use-parameter-file.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Bıcep uzantılı Visual Studio Code ve Azure PowerShell ya da Azure CLı ile aynı olmalıdır. Daha fazla bilgi için bkz. [Bıcep araçları](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Bıcep dosyasını gözden geçirme

Önceki öğreticinin sonunda, Bıcep dosyanız aşağıdaki içeriğe sahiptir:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Bu bicep dosyası iyi şekilde çalışmaktadır. Ancak daha büyük çözümler için, bu modülleri paylaşıp yeniden kullanabilmeniz için Bıcep dosyanızı birçok ilgili modüle bölmek istersiniz. Geçerli bicep dosyası bir depolama hesabı, bir App Service planı ve bir Web sitesi dağıtır.  Depolama hesabını bir modüle ayıralim.

## <a name="create-bicep-module"></a>Bıcep modülü oluşturma

Her bicep dosyası bir modül olarak tüketilebilir, bu nedenle bir modül tanımlamak için belirli bir sözdizimi yoktur. Aşağıdaki içeriğe sahip bir _Storage. bıcep_ dosyası oluşturun:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/storage.bicep":::

Bu modül depolama hesabı kaynağını ve ilgili parametreleri ve değişkenleri içerir. _Location_ parametresi ve _resourcetags_ parametreleri için değerler kaldırılmıştır. Bu değerler, ana Bıcep dosyasından geçirilir.

## <a name="consume-bicep-module"></a>Bıcep modülünü kullanma

Mevcut _azuredeploy. bıcep_ içindeki depolama hesabı kaynak tanımını aşağıdaki bicep içeriğiyle değiştirin:

```bicep
module stg './storage.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: storagePrefix
    location: location
    resourceTags: resourceTags
  }
}
```

- **module**: anahtar sözcüğü.
- **simgesel ad** (STG): Bu modül için bir tanıtıcıdır.
- **modül dosyası**: Bu örnekteki modülün yolu, göreli bir yol (./Storage.exe) kullanılarak belirtilir. Bıcep içindeki tüm yollar, tutarlı derleme platformlar arası sağlamak için eğik çizgi (/) dizin ayırıcısı kullanılarak belirtilmelidir. Windows ters eğik çizgi ( \) karakter desteklenmiyor.

Depolama uç noktasını almak için, _azuredeploy. bıcep_ içindeki çıktıyı şu bıcep 'ye güncelleştirin:

```bicep
output storageEndpoint object = stg.outputs.storageEndpoint
```

Tamamlanmış azuredeploy. bıcep aşağıdaki içeriğe sahiptir:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/azuredeploy.bicep":::

## <a name="deploy-template"></a>Şablon dağıtma

Şablonu dağıtmak için Azure CLı veya Azure PowerShell kullanın.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](bicep-tutorial-create-first-bicep.md#create-resource-group). Örnek, `bicepFile` [ilk öğreticide](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)gösterildiği gibi, değişkeni bıcep dosyasının yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Bu dağıtım cmdlet 'ini çalıştırmak için Azure PowerShell [en son sürümüne](/powershell/azure/install-az-ps) sahip olmanız gerekir.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addmodule `
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
  --name addmodule \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
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

Tebrikler, Bıcep dosyalarını Azure 'a dağıtmaya yönelik bu girişi tamamladınız. Geri bildirim bölümünde herhangi bir yorum ve öneri varsa bize bilgi verin. Teşekkürler!

Sonraki öğretici serisi, şablonları dağıtma hakkında daha fazla ayrıntıya gider.

> [!div class="nextstepaction"]
> [Yerel şablonu dağıtma](./deployment-tutorial-local-template.md)
