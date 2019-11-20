---
title: Şablon dağıtımı-if (Önizleme)
description: Azure Resource Manager şablonu dağıtılmadan önce kaynaklarınızda hangi değişikliklerin gerçekleşecektir belirleme.
author: mumian
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: jgao
ms.openlocfilehash: 117215e7c41ad7f354c9e76f764e9af1f50b74c1
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149234"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Kaynak Yöneticisi şablonu dağıtımı durum işlemi (Önizleme)

Bir şablonu dağıtımdan önce, gerçekleşecek değişiklikleri önizlemek isteyebilirsiniz. Azure Resource Manager, şablonu dağıtırsanız kaynakların ne şekilde değişdiklerinizi görmenizi sağlamak için ne yapılır işlemini sağlar. Bu işlem, mevcut kaynaklarda hiçbir değişiklik yapmaz. Bunun yerine, belirtilen şablon dağıtılırsa değişiklikleri tahmin eder.

> [!NOTE]
> Bu işlem şu anda önizleme aşamasındadır. Bunu kullanmak için, [Önizleme için kaydolmanız](https://aka.ms/armtemplatepreviews)gerekir. Önizleme sürümü olarak, sonuçlar bazen hiçbir değişiklik gerçekleşmediği zaman bir kaynağın değiştirileceği gösterebilir. Bu sorunları azaltmak için çalışıyoruz, ancak yardımımız için ihtiyacımız var. Lütfen bu sorunları [https://aka.ms/armwhatifissues](https://aka.ms/armwhatifissues)bildirin.

`New-AzDeploymentWhatIf` PowerShell komutuyla ve [dağıtım What If](/rest/api/resources/deployments/whatif) Rest işlemiyle ne yapılır işlemini kullanabilirsiniz.

PowerShell 'de, çıktı şöyle görünür:

![Kaynak Yöneticisi şablonu dağıtımı ne yapılır işlemi fullresourcepayload ve değişiklik türleri](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

## <a name="change-types"></a>Değişiklik türleri

Ne yapılır işlemi altı farklı değişiklik türünü listeler:

- **Oluştur**: kaynak şu anda mevcut değil, ancak şablonda tanımlandı. Kaynak oluşturulacaktır.

- **Sil**: Bu değişiklik türü yalnızca dağıtım için [tamamlanmış mod](deployment-modes.md) kullanılırken geçerlidir. Kaynak var, ancak şablonda tanımlı değil. Bu, tüm modda kaynak silinir. Yalnızca [tamamlanmış mod silme işlemini destekleyen](complete-mode-deletion.md) kaynaklar bu değişiklik türüne dahil edilir.

- **Yoksay**: kaynak var, ancak şablonda tanımlı değil. Kaynak dağıtılmaz veya değiştirilmez.

- **NOCHANGE**: kaynak vardır ve şablonda tanımlanmıştır. Kaynak yeniden dağıtılacak, ancak kaynağın özellikleri değişmeyecek. Bu değişiklik türü, [RESULTFORMAT](#result-format) `FullResourcePayloads`, varsayılan değer olan olarak ayarlandığında döndürülür.

- **Değiştir**: kaynak vardır ve şablonda tanımlanmıştır. Kaynak yeniden dağıtılacak ve kaynağın özellikleri değişecektir. Bu değişiklik türü, [RESULTFORMAT](#result-format) `FullResourcePayloads`, varsayılan değer olan olarak ayarlandığında döndürülür.

- **Dağıtım**: kaynak vardır ve şablonda tanımlanmıştır. Kaynak yeniden dağıtılacak. Kaynağın özellikleri değişebilir veya değişmeyebilir. İşlem, herhangi bir özellik değişmeyeceği için yeterli bilgi olmadığında bu değişiklik türünü döndürür. Bu durumu yalnızca [RESULTFORMAT](#result-format) `ResourceIdOnly`olarak ayarlandığında görürsünüz.

## <a name="deployment-scope"></a>Dağıtım kapsamı

Dağıtımlar için ne yapılır işlemini abonelik veya kaynak grubu düzeyinde kullanabilirsiniz. Dağıtım kapsamını `-ScopeType` parametresiyle ayarlarsınız. Kabul edilen değerler `Subscription` ve `ResourceGroup`. Bu makalede kaynak grubu dağıtımları gösterilmektedir.

Abonelik düzeyi dağıtımları hakkında bilgi edinmek için bkz. [abonelik düzeyinde kaynak grupları ve kaynaklar oluşturma](deploy-to-subscription.md#).

## <a name="result-format"></a>Sonuç biçimi

Tahmin edilen değişiklikler hakkında döndürülen ayrıntı düzeyini kontrol edebilirsiniz. Değişiklik yapılacak kaynakların bir listesini ve değiştirecek özelliklerle ilgili ayrıntıları almak için `ResultFormat` parametresini `FullResourcePayloads` olarak ayarlayın. Değiştirecek kaynakların bir listesini almak için `ResultFormat` parametresini `ResourceIdOnly` olarak ayarlayın. Varsayılan değer `FullResourcePayloads`.  

Aşağıdaki ekran görüntülerinde iki farklı çıkış biçimi gösterilmektedir:

- Tam kaynak yükleri

    ![Kaynak Yöneticisi şablonu dağıtımı ne yapılır işlemi fullresourceyüklerini çıkışı](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-fullresourcepayload.png)

- Yalnızca kaynak KIMLIĞI

    ![Kaynak Yöneticisi şablonu dağıtımı ne yapılır işlemi resourceıdonly çıkışı](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-resourceidonly.png)

## <a name="run-what-if-operation"></a>Durum işlemini çalıştır

### <a name="set-up-environment"></a>Ortamı ayarlama

Nasıl çalıştığını görmek için bazı testler çalıştıralım. İlk olarak, [bir depolama hesabı oluşturan Azure hızlı başlangıç şablonlarından](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json)bir şablon dağıtın. Varsayılan depolama hesabı türü `Standard_LRS`. Bu depolama hesabını, değişikliklerin ne yapılır-if tarafından raporlanacağı test etmek için kullanacaksınız.

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

### <a name="test-modification"></a>Test değişikliği

Dağıtım tamamlandıktan sonra, durum işlemini test etmeye hazırsınız demektir. Durum komutunu çalıştırın ancak depolama hesabı türünü `Standard_GRS`olarak değiştirin.

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" `
  -storageAccountType Standard_GRS
```

Ne olursa? çıktısı şuna benzerdir:

![Kaynak Yöneticisi şablonu dağıtımı ne yapılır işlemi çıkışı](./media/template-deploy-what-if/resource-manager-deployment-whatif-output.png)

Çıktının en üstünde, değişikliklerin türünü belirtmek için renklerin tanımlandığını görürsünüz.

Çıktının en altında, SKU adı (depolama hesabı türü) **Standard_LRS** **Standard_GRS**olarak değiştirilir.

### <a name="test-deletion"></a>Test silme

[Dağıtım modunun](deployment-modes.md)kullanımını destekleyen durum işlemi. Tamamlanmış moda ayarlandığında şablonda olmayan kaynaklar silinir. Aşağıdaki örnek, tamamlanmış bir [kaynağı olmayan bir şablon dağıtmıştır](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) .

```azurepowershell-interactive
New-AzDeploymentWhatIf `
  -ScopeType ResourceGroup `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Şablonda kaynak tanımlanmadığı ve dağıtım modu Tamam olarak ayarlandığından, depolama hesabı silinir.

![Kaynak Yöneticisi şablonu dağıtımı ne yapılır işlemi çıkışı dağıtım modu tamamlanmıştır](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Nelerin gerçek değişiklik yapmaz unutmayın. Depolama hesabı, kaynak grubunuzda hala mevcut.

## <a name="next-steps"></a>Sonraki adımlar

- Önizleme sürümünden yanlış sonuçlar olduğunu fark ederseniz, lütfen [https://aka.ms/armwhatifissues](https://aka.ms/armwhatifissues)sorunları bildirin.
- Şablonları Azure PowerShell dağıtmak için bkz. [Kaynak Yöneticisi şablonları ve Azure PowerShell ile kaynak dağıtma](resource-group-template-deploy.md).
- Şablonları REST ile dağıtmak için bkz. [Kaynak Yöneticisi şablonlarıyla kaynak dağıtma ve REST API Kaynak Yöneticisi](resource-group-template-deploy-rest.md).
- Hata aldığınızda başarılı bir dağıtıma geri dönmek için, [başarılı bir dağıtımda hata durumunda geri alma](rollback-on-error.md)konusuna bakın.
