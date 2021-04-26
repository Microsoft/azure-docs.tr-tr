---
title: Öğretici-Azure Resource Manager bicep dosyasına çıktılar ekleme
description: Sözdizimini basitleştirmek için Bıcep dosyanıza çıktılar ekleyin.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 4b7d7a1414091c516dba2c474e1681ba357b55a1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594317"
---
# <a name="tutorial-add-outputs-to-azure-resource-manager-bicep-file"></a>Öğretici: Azure Resource Manager bicep dosyasına çıktılar ekleme

Bu öğreticide, dağıtımdan bir değer döndürmeyi öğrenirsiniz. Dağıtılan bir kaynaktan bir değere ihtiyacınız olduğunda çıktıları kullanırsınız. Bu öğreticinin tamamlana **7 dakika** sürer.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

[Değişkenler hakkında öğreticiyi](bicep-tutorial-add-variables.md)tamamlamanızı öneririz, ancak bu gerekli değildir.

Bıcep uzantılı Visual Studio Code ve Azure PowerShell ya da Azure CLı ile aynı olmalıdır. Daha fazla bilgi için bkz. [Bıcep araçları](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Bıcep dosyasını gözden geçirme

Önceki öğreticinin sonunda, Bıcep dosyanız aşağıdaki içeriğe sahiptir:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep":::

Bir depolama hesabı dağıtır, ancak depolama hesabı hakkında herhangi bir bilgi döndürmez. Daha sonra başvuru için kullanılabilir olmaları için yeni bir kaynaktaki özellikleri yakalamanız gerekebilir.

## <a name="add-outputs"></a>Çıkış Ekle

Dağıtımdan değer döndürmek için çıktıları kullanabilirsiniz. Örneğin, yeni depolama hesabınız için uç noktaları almak faydalı olabilir.

Aşağıdaki örnek, bir çıkış değeri eklemek için Bıcep dosyanızdaki değişikliği gösterir. Tüm dosyayı kopyalayın ve Bıcep dosyanızı içeriğiyle değiştirin.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep" range="1-33" highlight="33":::

Eklediğiniz çıktı değeri hakkında dikkat etmeniz için bazı önemli öğeler vardır.

Döndürülen değerin türü olarak ayarlanır `object` , yani bir şablon nesnesi döndürür.

`primaryEndpoints`Depolama hesabından özelliği almak için, depolama hesabı sembolik adını kullanırsınız. Visual Studio Code otomatik tamamlama özelliği, özelliklerinin tam bir listesini sunar:

   ![Visual Studio Code bicep sembolik ad nesne özellikleri](./media/bicep-tutorial-add-outputs/visual-studio-code-bicep-output-properties.png)

## <a name="deploy-bicep-file"></a>Bıcep dosyasını dağıtma

Bıcep dosyasını dağıtmaya ve döndürülen değere bakmaya hazırsınız.

Kaynak grubunu oluşturmadıysanız, bkz. [kaynak grubu oluşturma](bicep-tutorial-create-first-bicep.md#create-resource-group). Örnek, `bicepFile` [ilk öğreticide](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)gösterildiği gibi, değişkeni bıcep dosyasının yolu olarak ayarlamış olduğunuzu varsayar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

Dağıtım komutunun çıktısında, aşağıdaki örneğe benzer bir nesne görürsünüz ve yalnızca çıkış JSON biçimindedir:

```json
{
  "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
  "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
  "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
  "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
  "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
  "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> Dağıtım başarısız olursa, `verbose` oluşturulan kaynaklarla ilgili bilgi almak için anahtarını kullanın. `debug`Hata ayıklama hakkında daha fazla bilgi edinmek için anahtarını kullanın.

## <a name="review-your-work"></a>Çalışmanızı gözden geçirin

Son altı öğreticilerde bir çok şey yaptınız. Ne yaptığını gözden geçirmeniz biraz zaman atalım. Sağlaması kolay olan parametrelerle bir Bıcep dosyası oluşturdunuz. Bıcep dosyası, özelleştirmeye izin verdiğinden ve gerekli değerleri dinamik olarak oluşturduğundan farklı ortamlarda yeniden kullanılabilir. Ayrıca, betiğinizdeki kullanabileceğiniz depolama hesabı hakkındaki bilgileri de döndürür.

Şimdi, kaynak grubuna ve dağıtım geçmişine bakalım.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol menüden **kaynak grupları**' nı seçin.
1. Dağıttığınız kaynak grubunu seçin.
1. Yaptığınız adımlara bağlı olarak, kaynak grubunda en az bir ve belki birkaç depolama hesabınız olmalıdır.
1. Ayrıca, geçmişte listelenen birkaç başarılı dağıtıma da sahip olmanız gerekir. Bağlantıyı seçin.

   ![Dağıtımları seçin](./media/bicep-tutorial-add-outputs/select-deployments.png)

1. Tüm dağıtımlarınızın geçmişini görürsünüz. **Addoutputs** adlı dağıtımı seçin.

   ![Dağıtım geçmişini göster](./media/bicep-tutorial-add-outputs/show-history.png)

1. Girişleri gözden geçirebilirsiniz.

   ![Girişleri göster](./media/bicep-tutorial-add-outputs/show-inputs.png)

1. Çıkışları gözden geçirebilirsiniz.

   ![Çıkışları göster](./media/bicep-tutorial-add-outputs/show-outputs.png)

1. JSON şablonunu gözden geçirebilirsiniz.

   ![Şablonu göster](./media/bicep-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir sonraki öğreticiye geçiş yapıyorsanız, kaynak grubunu silmeniz gerekmez.

Şimdi duruyorsa, kaynak grubunu silerek dağıttığınız kaynakları temizlemeniz gerekebilir.

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Bıcep dosyasına bir dönüş değeri eklediniz. Sonraki öğreticide, bir JSON şablonunu dışarı aktarmayı ve bu dışarı aktarılmış şablonun parçalarını Bıcep dosyanızda kullanmayı öğreneceksiniz.

> [!div class="nextstepaction"]
> [Aktarılmış şablonu kullan](bicep-tutorial-export-template.md)
