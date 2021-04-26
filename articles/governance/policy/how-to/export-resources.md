---
title: Azure İlkesi kaynaklarını dışarı aktarma
description: İlke tanımları ve ilke atamaları gibi Azure Ilke kaynaklarını GitHub 'a aktarmayı öğrenin.
ms.date: 03/31/2021
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8fd2a24d2c01b4214eda88054b45a409cd865a32
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096555"
---
# <a name="export-azure-policy-resources"></a>Azure İlkesi kaynaklarını dışarı aktarma

Bu makalede, mevcut Azure Ilke kaynaklarınızı dışarı aktarma hakkında bilgi verilmektedir. Kaynaklarınızın dışa aktarılması yararlı olur ve yedekleme için önerilir, ancak bulut İdaresi ile aynı zamanda önemli bir adımdır ve [kod olarak ilkenize](../concepts/policy-as-code.md)davranılmaktadır. Azure Ilke kaynakları [Azure Portal](#export-with-azure-portal), [azure CLI](#export-with-azure-cli), [Azure PowerShell](#export-with-azure-powershell)ve desteklenen SDK 'lardan her biri aracılığıyla aktarılabilir.

## <a name="export-with-azure-portal"></a>Azure portal ile dışarı aktar

Azure portal bir ilke tanımını dışarı aktarmak için şu adımları izleyin:

1. Azure portalında **Tüm hizmetler**’e tıkladıktan sonra **İlke**'yi arayıp seçerek Azure İlkesi hizmetini başlatın.

1. Azure Ilkesi sayfasının sol tarafındaki **tanımlar** ' ı seçin.

1. **Tanımları dışarı aktar** düğmesini veya bir ilke tanımının satırındaki üç noktayı seçip **dışarı aktarma tanımı**' nı seçin.

1. **GitHub Ile oturum aç** düğmesini seçin. Kaynağı dışarı aktarmak için Azure Ilkesini yetkilendirmek üzere GitHub ile henüz kimlik doğrulamasından ayrıldıysanız, açılan yeni pencerede [GitHub eylemi](https://github.com/features/actions) ihtiyaçlarına erişimi gözden geçirin ve dışarı aktarma işlemine devam etmek Için **Yetkilendir AzureGitHubActions** ' ı seçin. Tamamlandıktan sonra, yeni pencere kendi kendini kapatır.

1. **Temel bilgiler** sekmesinde, aşağıdaki seçenekleri ayarlayın ve ardından sayfanın alt kısmındaki **Ilkeler** sekmesini veya **Sonraki: ilkeler** düğmesini seçin.

   - **Depo filtresi**: GitHub eyleminin erişimine izin verdiğiniz tüm depoları görmek Için _depolarıma_ yalnızca sahip olduğunuz depoları veya _Tüm depolarımı_ görüntülemek için ayarlayın.
   - **Depo**: Azure ilke kaynaklarını dışarı aktarmak istediğiniz depoya ayarlayın.
   - **Dal**: depodaki dalı ayarlayın. Varsayılan dışında bir dal kullanmak, kaynak kodunuzda daha fazla birleştirmeden önce güncelleştirmelerinizi doğrulamak için iyi bir yoldur.
   - **Dizin**: Azure ilke kaynaklarının dışarı aktarılacağı _kök düzeyi klasörü_ . Bu dizin altındaki alt klasörler, hangi kaynakların verileceği temel alınarak oluşturulur.

1. **İlkeler** sekmesinde, üç noktayı seçip yönetim gruplarının, aboneliklerinin veya kaynak gruplarının bir birleşimini seçerek kapsamı arama olarak ayarlayın.
   
1. Hangi nesnelerin dışarı aktarılacağı kapsamı aramak için **ilke tanımlarını Ekle** düğmesini kullanın. Açılan yan pencerede, dışarı aktarılacak her bir nesneyi seçin. Seçimi arama kutusuna veya türe göre filtreleyin. Dışarı aktarılacak tüm nesneleri seçtikten sonra sayfanın alt kısmındaki **Ekle** düğmesini kullanın.

1. Seçili her nesne için, _yalnızca tanım_ veya tanım ve ilke tanımı için _atama (ler)_ gibi istenen dışarı aktarma seçeneklerini seçin. Ardından sayfanın alt kısmındaki **gözden geçir + dışarı aktar** sekmesini veya **Sonraki: gözden geçir + dışarı aktar** düğmesini seçin.

   > [!NOTE]
   > Seçenek _tanımı ve atamaları_ seçilirse, yalnızca ilke tanımı eklendiğinde filtre tarafından ayarlanan kapsamdaki ilke atamaları verilir.

1. **Gözden geçir ve dışarı aktar** sekmesinde, Ayrıntılar ' ı işaretleyin ve ardından sayfanın alt kısmındaki **dışarı aktar** düğmesini kullanın.

1. Seçilen kaynakların artık kaynak denetiminize aktarıldığına bakmak için GitHub deponuzu, dalınızı ve _kök düzeyi klasörünüzü_ denetleyin.

Azure Ilke kaynakları, seçilen GitHub deposu ve _kök düzeyi klasörü_ içinde aşağıdaki yapıya aktarılabilir:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Azure CLı ile dışarı aktarma

Azure Ilke tanımları, girişimleri ve atamaları, her biri [Azure CLI](/cli/azure/install-azure-cli)ile JSON olarak aktarılabilir. Bu komutların her biri, JSON 'ı hangi nesnenin alınacağını belirtmek için bir **ad** parametresi kullanır. **Name** özelliği genellikle bir _GUID_ 'dir ve nesnenin **DisplayName** değeri değildir.

- Tanım- [az Policy Definition Show](/cli/azure/policy/definition#az_policy_definition_show)
- Girişim- [az Policy set-Definition Show](/cli/azure/policy/set-definition#az_policy_set_definition_show)
- Atama- [az Policy atama Show](/cli/azure/policy/assignment#az_policy_assignment_show)

Aşağıda _Virtual, Inesstorage_ **adlı** BIR ilke tanımı için JSON alma örneği verilmiştir:

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Azure PowerShell ile dışarı aktar

Azure Ilke tanımları, girişimleri ve atamaları, her biri [Azure POWERSHELL](/powershell/azure/)JSON olarak aktarılabilir. Bu cmdlet 'lerin her biri, JSON 'ı hangi nesnenin alınacağını belirtmek için bir **ad** parametresi kullanır. **Name** özelliği genellikle bir _GUID_ 'dir ve nesnenin **DisplayName** değeri değildir.

- Tanım- [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- Girişim- [Get-AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- Atama- [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

Aşağıda _Virtual, Inesstorage_ **adlı** BIR ilke tanımı için JSON alma örneği verilmiştir:

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage' | ConvertTo-Json -Depth 10
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ilke örneklerindeki](../samples/index.md)örnekleri gözden geçirin.
- [Azure İlkesi tanımı yapısını](../concepts/definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](programmatically-create.md)anlayın.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.
