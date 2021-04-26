---
title: Öğretici-şablona kaynak ekleme
description: İlk Azure Resource Manager şablonunuzu (ARM şablonu) oluşturma adımlarını açıklar. Şablon dosyası söz dizimi ve depolama hesabının nasıl dağıtılacağı hakkında bilgi edinirsiniz.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 49cee5c98c4099e214a732371269e935db353152
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97106980"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Öğretici: ARM şablonunuza kaynak ekleme

[Önceki öğreticide](template-tutorial-create-first-template.md)boş bir Azure Resource Manager şablonu (ARM şablonu) oluşturmayı ve bunu dağıtmayı öğrendiniz. Şimdi gerçek bir kaynak dağıtmaya hazırsınız demektir. Bu öğreticide bir depolama hesabı eklersiniz. Bu öğreticiyi tamamlamaya yaklaşık **9 dakika** sürer.

## <a name="prerequisites"></a>Önkoşullar

[Şablonlar hakkında giriş öğreticisini](template-tutorial-create-first-template.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Kaynak Yöneticisi Araçları uzantısı ve Azure PowerShell ya da Azure CLı ile Visual Studio Code olması gerekir. Daha fazla bilgi için bkz. [şablon araçları](template-tutorial-create-first-template.md#get-tools).

## <a name="add-resource"></a>Kaynak Ekle

Mevcut şablona bir depolama hesabı tanımı eklemek için, vurgulanan JSON ' a aşağıdaki örnekte bakın. Şablonun bölümlerini kopyalamaya çalışmak yerine, tüm dosyayı kopyalayın ve şablonunuzu içeriğiyle değiştirin.

`{provide-unique-name}`Ve küme ayraçları `{}` benzersiz bir depolama hesabı adıyla değiştirin.

> [!IMPORTANT]
> Depolama hesabı adının Azure’da benzersiz olması gerekir. Ad yalnızca küçük harf veya rakam içermelidir. Bu, 24 karakterden uzun olamaz. Ön ek olarak **store1** kullanma ve sonra adınızın baş harflerini ve bugünün tarihini ekleme gibi bir adlandırma modelini deneyebilirsiniz. Örneğin, kullandığınız ad **store1abc09092019** gibi görünebilir.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

Depolama hesabı için benzersiz bir ad tahmin etmek kolay değildir ve büyük dağıtımları otomatikleştirmede iyi çalışmaz. Bu öğretici serisinde daha sonra, benzersiz bir ad oluşturmayı kolaylaştıran şablon özelliklerini kullanacaksınız.

## <a name="resource-properties"></a>Kaynak özellikleri

Her kaynak türü için kullanılacak özellikleri bulmayı merak ediyor olabilirsiniz. Dağıtmak istediğiniz kaynak türlerini bulmak için [ARM şablonu başvurusunu](/azure/templates/) kullanabilirsiniz.

Dağıttığınız her kaynak en azından aşağıdaki üç özelliğe sahiptir:

- `type`: Kaynağın türü. Bu değer, kaynak sağlayıcının ad alanının ve gibi kaynak türünün bir birleşimidir `Microsoft.Storage/storageAccounts` .
- `apiVersion`: Kaynak oluşturmak için kullanılacak REST API sürümü. Her kaynak sağlayıcı kendi API sürümlerini yayımlar, bu nedenle bu değer türüne özeldir.
- `name`: Kaynağın adı.

Kaynakların çoğu ayrıca `location` , kaynağın dağıtıldığı bölgeyi ayarlayan bir özelliğine sahiptir.

Diğer özellikler kaynak türüne ve API sürümüne göre farklılık gösterir. API sürümü ve kullanılabilir özellikler arasındaki bağlantıyı anlamanız önemlidir. bu nedenle daha fazla ayrıntıya geçelim.

Bu öğreticide, şablona bir depolama hesabı eklediniz. Bu API sürümünü [Storageaccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts)' de görebilirsiniz. Tüm özellikleri şablonunuza eklemediğine dikkat edin. Özelliklerin birçoğu isteğe bağlıdır. `Microsoft.Storage`Kaynak sağlayıcısı yeni BIR API sürümünü serbest bırakabilir, ancak dağıttığınız sürümün değiştirmek zorunda değildir. Bu sürümü kullanmaya devam edebilir ve dağıtımınızın sonuçlarının tutarlı olacağını bilirsiniz.

[Storageaccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)gibi eskı bir API sürümünü görüntülediğinizde, daha küçük bir özellik kümesinin kullanılabildiğini görürsünüz.

Bir kaynağın API sürümünü değiştirmeye karar verirseniz, bu sürümün özelliklerini değerlendirdiğinizden emin olun ve şablonunuzu uygun şekilde ayarlayın.

## <a name="deploy-template"></a>Şablon dağıtma

Depolama hesabı oluşturmak için şablonu dağıtabilirsiniz. Farklı bir ad verin ve bu sayede geçmişi kolayca bulabilirsiniz.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](template-tutorial-create-first-template.md#create-resource-group). Örnek, `templateFile` [ilk öğreticide](template-tutorial-create-first-template.md#deploy-template)gösterildiği gibi, değişkeni şablon dosyası yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

> [!NOTE]
> Dağıtım başarısız olursa, `verbose` oluşturulan kaynaklarla ilgili bilgi almak için anahtarını kullanın. `debug`Hata ayıklama hakkında daha fazla bilgi edinmek için anahtarını kullanın.

Karşılaşabileceğiniz iki olası dağıtım başarısızlığı:

- `Error: Code=AccountNameInvalid; Message={provide-unique-name}` geçerli bir depolama hesabı adı değil. Depolama hesabı adı 3 ila 24 karakter uzunluğunda olmalı ve yalnızca rakam ve küçük harf kullanılmalıdır.

    Şablonda, öğesini `{provide-unique-name}` benzersiz bir depolama hesabı adıyla değiştirin. Bkz. [kaynak ekleme](#add-resource).

- `Error: Code=StorageAccountAlreadyTaken; Message=The storage account named store1abc09092019` zaten alınmış.

    Şablonda, farklı bir depolama hesabı adı deneyin.

Bu dağıtım, depolama hesabı oluşturulduğundan boş şablon dağıtımınızdan daha uzun sürer. Bir dakika sürebilir, ancak genellikle daha hızlıdır.

## <a name="verify-deployment"></a>Dağıtımı doğrulama

Kaynak grubunu Azure portal inceleyerek dağıtımı doğrulayabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol menüden **kaynak grupları**' nı seçin.
1. Dağıttığınız kaynak grubunu seçin.
1. Bir depolama hesabının dağıtıldığını görürsünüz.
1. Dağıtım etiketinin şimdi şöyle göründüğünü göreceksiniz: **dağıtımlar: 2 başarılı**.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçiş yapıyorsanız, kaynak grubunu silmeniz gerekmez.

Şimdi duruyorsa, kaynak grubunu silerek dağıttığınız kaynakları temizlemeniz gerekebilir.

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bir Azure depolama hesabı dağıtmak için basit bir şablon oluşturdunuz. Sonraki öğreticilerde, bir şablona parametreler, değişkenler, kaynaklar ve çıktılar eklemeyi öğreneceksiniz. Bu özellikler, çok daha karmaşık şablonlar için yapı taşlarıdır.

> [!div class="nextstepaction"]
> [Parametreler ekleme](template-tutorial-add-parameters.md)
