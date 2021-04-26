---
title: Şablon dağıtımı
description: Azure Resource Manager şablonu dağıtılmadan önce kaynaklarınızda hangi değişikliklerin gerçekleşecektir belirleme.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: tomfitz
ms.openlocfilehash: 7e300f896bb11ed7c77738836f894cff41cc8bf3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781838"
---
# <a name="arm-template-deployment-what-if-operation"></a>ARM şablonu dağıtımı what-if işlemi

Azure Resource Manager şablonu (ARM şablonu) dağıtılmadan önce, gerçekleşen değişikliklerin önizlemesini yapabilirsiniz. Azure Resource Manager, şablonu dağıtırsanız kaynakların ne şekilde değişdiklerinizi görmenizi sağlamak için ne yapılır işlemini sağlar. what-if komutu var olan kaynaklarda herhangi bir değişiklik yapmaz. Bunun yerine belirtilen şablonun dağıtılması halinde gerçekleştirilecek değişikliklerle ilgili tahminde bulunur.

Azure PowerShell, Azure CLı veya REST API işlemleri ile durum işlemini kullanabilirsiniz. Kaynak grubu, abonelik, yönetim grubu ve kiracı düzeyinde dağıtımlar için ne desteklenir?

## <a name="install-azure-powershell-module"></a>Azure PowerShell modülünü Install

PowerShell 'de ne olduğunu kullanmak için **az Module sürüm 4,2 veya sonraki bir** sürümünü kullanmanız gerekir.

Modülünü yüklemek için şunu kullanın:

```powershell
Install-Module -Name Az -Force
```

Modül yükleme hakkında daha fazla bilgi için bkz. [yükleme Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="install-azure-cli-module"></a>Azure CLı modülünü yükler

Azure CLı 'de ne olduğunu kullanmak için Azure CLı 2.14.0 veya sonraki bir sürüme sahip olmanız gerekir. Gerekirse [Azure CLI'nin en son sürümünü yükleyin](/cli/azure/install-azure-cli).

## <a name="see-results"></a>Sonuçlara bakın

PowerShell veya Azure CLı 'de ne olursa kullanın, çıktı, farklı değişiklik türlerini görmenizi sağlayan renk kodlu sonuçlar içerir.

![Kaynak Yöneticisi şablonu dağıtımı ne yapılır işlemi fullresourcepayload ve değişiklik türleri](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Metin çıktısı:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

> [!NOTE]
> Ne yapılır işlemi [başvuru işlevini](template-functions-resource.md#reference)çözümleyemezse. Başvuru işlevini içeren bir şablon ifadesine her bir özellik ayarladığınızda, özelliğin değiştirileceği durum raporları görüntülenir. Bu davranış, özelliğin geçerli değerini (örneğin `true` veya `false` bir Boolean değeri için) çözümlenmemiş şablon ifadesiyle karşılaştırdığı için oluşur. Kuşkusuz, bu değerler eşleşmeyecektir. Şablonu dağıttığınızda, özellik yalnızca şablon ifadesi farklı bir değere çözümlenirse değişir.

## <a name="what-if-commands"></a>Durum komutları

### <a name="azure-powershell"></a>Azure PowerShell

Bir şablonu dağıtımdan önce değişiklikleri önizlemek için [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) veya [New-azsubscriptiondeployment](/powershell/module/az.resources/new-azdeployment)komutunu kullanın. `-Whatif`Dağıtım komutuna anahtar parametresini ekleyin.

* `New-AzResourceGroupDeployment -Whatif` kaynak grubu dağıtımları için
* `New-AzSubscriptionDeployment -Whatif``New-AzDeployment -Whatif`abonelik düzeyinde dağıtımlar için

`-Confirm`Değişiklikleri önizlemek ve dağıtıma devam etmek isteyip istemediğiniz sorulduğunda anahtar parametresini kullanabilirsiniz.

* `New-AzResourceGroupDeployment -Confirm` kaynak grubu dağıtımları için
* `New-AzSubscriptionDeployment -Confirm``New-AzDeployment -Confirm`abonelik düzeyinde dağıtımlar için

Yukarıdaki komutlar el ile inceleyebilmeniz için bir metin Özeti döndürür. Program aracılığıyla değişiklikler için inceleyebileceğiniz bir nesne almak için [Get-Azresourcegroupdeploymentwhatıresult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) veya [Get-azsubscriptiondeploymentwhatıresult](/powershell/module/az.resources/get-azdeploymentwhatifresult)' ı kullanın.

* `$results = Get-AzResourceGroupDeploymentWhatIfResult` kaynak grubu dağıtımları için
* `$results = Get-AzSubscriptionDeploymentWhatIfResult``$results = Get-AzDeploymentWhatIfResult`abonelik düzeyi dağıtımlar için veya

### <a name="azure-cli"></a>Azure CLI

Bir şablonu dağıtımdan önce değişiklikleri önizlemek için şunu kullanın:

* [az dağıtım grubu](/cli/azure/deployment/group#az_deployment_group_what_if) kaynak grubu dağıtımları için ne yapılır?
* abonelik düzeyi dağıtımlar için [az Deployment Sub](/cli/azure/deployment/sub#az_deployment_sub_what_if)
* Yönetim grubu dağıtımları için [az Deployment mg ne-if](/cli/azure/deployment/mg#az_deployment_mg_what_if)
* [az dağıtım kiracısı](/cli/azure/deployment/tenant#az_deployment_tenant_what_if) kiracı dağıtımları için ne yapılır?

`--confirm-with-what-if` `-c` Değişiklikleri önizlemek ve dağıtıma devam etmek isteyip istemediğiniz sorulduğunda anahtarı (veya kısa biçimini) kullanabilirsiniz. Bu anahtarı şu şekilde ekleyin:

* [az dağıtım grubu oluştur](/cli/azure/deployment/group#az_deployment_group_create)
* [az Deployment Sub Create](/cli/azure/deployment/sub#az_deployment_sub_create).
* [az Deployment mg Create](/cli/azure/deployment/mg#az_deployment_mg_create)
* [az Deployment Tenant Create](/cli/azure/deployment/tenant#az_deployment_tenant_create)

Örneğin, `az deployment group create --confirm-with-what-if` `-c` kaynak grubu dağıtımları için veya kullanın.

Yukarıdaki komutlar el ile inceleyebilmeniz için bir metin Özeti döndürür. Değişiklikler için programlı olarak inceleyebileceğiniz bir JSON nesnesi almak için `--no-pretty-print` anahtarını kullanın. Örneğin, `az deployment group what-if --no-pretty-print` kaynak grubu dağıtımları için kullanın.

Sonuçları renkler olmadan döndürmek istiyorsanız, [Azure CLI yapılandırma](/cli/azure/azure-cli-configuration) dosyanızı açın. **No_color** **Evet** olarak ayarlayın.

### <a name="azure-rest-api"></a>Azure REST API

REST API için şunu kullanın:

* [Dağıtımlar-](/rest/api/resources/deployments/whatif) kaynak grubu dağıtımları için What If
* [Dağıtımlar-abonelik dağıtımları Için Abonelik kapsamında What If](/rest/api/resources/deployments/whatifatsubscriptionscope)
* Dağıtımlar-yönetim grubu dağıtımları için [Yönetim grubu kapsamındaki What If](/rest/api/resources/deployments/whatifatmanagementgroupscope)
* [Dağıtımlar-](/rest/api/resources/deployments/whatifattenantscope) kiracı dağıtımlarında kiracı kapsamındaki What If.

## <a name="change-types"></a>Değişiklik türleri

Ne yapılır işlemi altı farklı değişiklik türünü listeler:

- **Oluştur**: kaynak şu anda mevcut değil, ancak şablonda tanımlandı. Kaynak oluşturulacaktır.

- **Sil**: Bu değişiklik türü yalnızca dağıtım için [tamamlanmış mod](deployment-modes.md) kullanılırken geçerlidir. Kaynak mevcuttur ancak şablonda tanımlanmamıştır. Tamamlama modunda kaynak oluşturulacaktır. Yalnızca [tamamlanmış mod silme işlemini destekleyen](complete-mode-deletion.md) kaynaklar bu değişiklik türüne dahil edilir.

- **Yoksay**: kaynak var, ancak şablonda tanımlı değil. Kaynak dağıtılmayacak veya değiştirilmeyecektir.

- **NOCHANGE**: kaynak vardır ve şablonda tanımlanmıştır. Kaynak yeniden dağıtılacaktır ancak kaynağın özellikleri değişmeyecektir. Bu değişiklik türü, varsayılan değer olan [RESULTFORMAT](#result-format) ayarlandığında döndürülür `FullResourcePayloads` .

- **Değiştir**: kaynak vardır ve şablonda tanımlanmıştır. Kaynak yeniden dağıtılacaktır ve kaynağın özellikleri değişecektir. Bu değişiklik türü, varsayılan değer olan [RESULTFORMAT](#result-format) ayarlandığında döndürülür `FullResourcePayloads` .

- **Dağıtım**: kaynak vardır ve şablonda tanımlanmıştır. Kaynak yeniden dağıtılacaktır. Kaynağın özellikleri değişebilir veya değişmeyebilir. İşlem, özelliklerin değiştirilip değiştirilmeyeceği konusunda karar vermek için yeterli bilgiye sahip olmadığında bu değişiklik türünü döndürür. Bu durumu yalnızca [RESULTFORMAT](#result-format) olarak ayarlandığında görürsünüz `ResourceIdOnly` .

## <a name="result-format"></a>Sonuç biçimi

Tahmin edilen değişiklikler hakkında döndürülen ayrıntı düzeyini kontrol edersiniz. İki seçeneğiniz vardır:

* **Fullresourceyükleri** -değiştirilecek kaynakların bir listesini ve değiştirilecek özelliklerle ilgili ayrıntıları döndürür
* **Resourceıdonly** -değiştirilecek kaynak listesini döndürür

Varsayılan değer **Fullresourceyükleri**' dir.

PowerShell dağıtım komutları için `-WhatIfResultFormat` parametresini kullanın. Programlı nesne komutlarında `ResultFormat` parametresini kullanın.

Azure CLı için `--result-format` parametresini kullanın.
 
Aşağıdaki sonuçlar iki farklı çıkış biçimini göstermektedir:

- Tam kaynak yükleri

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Yalnızca kaynak KIMLIĞI

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Durum işlemini çalıştır

### <a name="set-up-environment"></a>Ortamı ayarlama

Nasıl çalıştığını görmek için bazı testler çalıştıralım. İlk olarak, [sanal ağ oluşturan bir şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json)dağıtın. Bu sanal ağı, değişikliklerin ne yapılır-if tarafından raporlanacağı test etmek için kullanacaksınız.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Test değişikliği

Dağıtım tamamlandıktan sonra, durum işlemini test etmeye hazırsınız demektir. Bu sefer [, sanal ağı değiştiren bir şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json)dağıtırsınız. Özgün etiketlerden biri eksik, bir alt ağ kaldırıldı ve adres ön eki değişti.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

what-if komutunun çıkışı aşağıdakine benzer olacaktır:

![Kaynak Yöneticisi şablonu dağıtımı ne yapılır işlemi çıkışı](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Metin çıktısı:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Çıktının en üstünde, değişikliklerin türünü belirtmek için renklerin tanımlandığını görürsünüz.

Çıktının en altında, etiketin sahibinin silindiğini gösterir. Adres ön eki 10.0.0.0/16 olarak 10.0.0.0/15 olarak değiştirildi. Subnet001 adlı alt ağ silindi. Bu değişikliklerin dağıtılmadığını unutmayın. Şablonu dağıtırsanız gerçekleştirilecek değişikliklerin önizlemesini görürsünüz.

Silinmiş olarak listelenen özelliklerden bazıları aslında değişmeyecektir. Özellikler, şablonda olmadıkları sırada silinmiş olarak yanlış bildirilebilir, ancak dağıtım sırasında varsayılan değer olarak otomatik olarak ayarlanır. Bu sonuç, durum yanıtı içinde "gürültü" olarak değerlendirilir. Son dağıtılan kaynak, özellikler için ayarlanmış değerlere sahip olacaktır. Ne gibi işlemler söz konusu olduğunda, bu özellikler sonuçtan filtrelenmez.

## <a name="programmatically-evaluate-what-if-results"></a>Programlı sonuçları değerlendir

Şimdi, komutu bir değişkene ayarlayarak, bu sonuçları program aracılığıyla değerlendirelim.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Her bir değişikliğin özetini görebilirsiniz.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Silme işlemini onaylama

[Dağıtım modunun](deployment-modes.md)kullanımını destekleyen durum işlemi. Tamamlanmış moda ayarlandığında şablonda olmayan kaynaklar silinir. Aşağıdaki örnek, tamamlanmış bir [kaynağı olmayan bir şablon dağıtmıştır](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) .

Şablonu dağıtmadan önce gerçekleştirilecek değişiklikleri önizlemek için dağıtım komutuyla birlikte confirm anahtarı parametresini kullanın. Değişiklikler beklediğiniz gibi olduğunda, dağıtımın tamamlanmasını istediğinizi yanıtlayın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

Şablonda kaynak tanımlanmadığı ve dağıtım modu Tamam olarak ayarlandığından, sanal ağ silinir.

![Kaynak Yöneticisi şablonu dağıtımı ne yapılır işlemi çıkışı dağıtım modu tamamlanmıştır](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Metin çıktısı:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001&quot;
      location:        &quot;centralus&quot;
      name:            &quot;vnet-001&quot;
      tags.CostCenter: &quot;12345&quot;
      tags.Owner:      &quot;Team A&quot;
      type:            &quot;Microsoft.Network/virtualNetworks&quot;

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is &quot;Y"):
```

Beklenen değişiklikleri görürsünüz ve dağıtımın çalıştırılmasını istediğinizi doğrulayabilirsiniz.

## <a name="sdks"></a>SDK

Azure SDK 'Ları aracılığıyla ne yapılır işlemini kullanabilirsiniz.

* Python için, [ne yapılacağını](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-)kullanın.

* Java için [Deploymentwhatif sınıfını](/java/api/com.microsoft.azure.management.resources.deploymentwhatif)kullanın.

* .NET için [Deploymentwhatif sınıfını](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif)kullanın.

## <a name="next-steps"></a>Sonraki adımlar

- İşlem hattındaki durum işlemini kullanmak için, bkz. bir işlem hattındaki [What-If test ARM şablonları](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/).
- Durum işleminden yanlış sonuçlar fark ederseniz lütfen sorunları bildirin [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .
- Ne olursa kullanın bir Microsoft Learn modülü için, bkz. [değişiklik önizlemesi ve ARM şablonu test araç seti kullanarak Azure kaynaklarını doğrulama](/learn/modules/arm-template-test/).
