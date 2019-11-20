---
title: Şema tanımlarını PowerShell ile içeri ve dışarı aktarma
description: Şema tanımlarınız kodu olarak nasıl çalışacağınızı öğrenin. Dışarı aktarma ve içeri aktarma komutlarını kullanarak bunları paylaşabilirsiniz, kaynak denetimi yapın ve yönetin.
ms.date: 09/03/2019
ms.topic: conceptual
ms.openlocfilehash: ca756ed093d5d423f6f83e5ca3953a8ecfce7d5a
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960379"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Şema tanımlarını PowerShell ile içeri ve dışarı aktarma

Azure şemaları, Azure portal aracılığıyla tam olarak yönetilebilir. Kuruluşlar, şemaları kullanımıyla ilerletikçe, şema tanımlarını yönetilen kod olarak düşünmeye başlamalıdır. Bu kavram genellikle kod olarak altyapı (IAC) olarak adlandırılır. Şema tanımlarınızın kod olarak davranılması, Azure portal sunduğu avantajlardan daha fazla avantaj sunar. Bu avantajlar şunlardır:

- Şema tanımlarını paylaşma
- Şema tanımlarınızı yedekleme
- Farklı kiracılarda veya aboneliklerde şema tanımlarını yeniden kullanma
- Şema tanımlarını kaynak denetimine yerleştirme
  - Test ortamlarında şema tanımlarının otomatik testi
  - Sürekli tümleştirme ve sürekli dağıtım (CI/CD) işlem hatları desteği

Nedenleriniz ne olursa olsun, şema tanımlarınızı kod olarak yönetme avantajlara sahiptir. Bu makalede, [az. Blueprint](https://powershellgallery.com/packages/Az.Blueprint/) modülünde `Import-AzBlueprintWithArtifact` ve `Export-AzBlueprintWithArtifact` komutlarının nasıl kullanılacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, Azure şemaları hakkında orta düzeyde çalışma bilgisi varsayılmaktadır. Henüz yapmadıysanız, aşağıdaki makalelerle çalışın:

- [Portalda bir şema oluşturma](../create-blueprint-portal.md)
- [Dağıtım aşamaları](../concepts/deployment-stages.md) ve [şema yaşam döngüsü](../concepts/lifecycle.md) hakkında bilgi edinin
- PowerShell ile şema tanımlarını ve atamalarını [oluşturma](../create-blueprint-powershell.md) ve [yönetme](./manage-assignments-ps.md)

Zaten yüklü değilse, PowerShell Galerisi **az** . Blueprint modülünü yüklemek ve doğrulamak için [az. Blueprint modülünü ekleme](./manage-assignments-ps.md#add-the-azblueprint-module) bölümündeki yönergeleri izleyin.

## <a name="folder-structure-of-a-blueprint-definition"></a>Şema tanımının klasör yapısı

Şemaları dışarı ve içeri aktarmaya geçmeden önce, şema tanımını oluşturan dosyaların nasıl yapılandırıldığını inceleyelim. Şema tanımının kendi klasöründe depolanması gerekir.

> [!IMPORTANT]
> `Import-AzBlueprintWithArtifact` cmdlet 'inin **Name** parametresine hiçbir değer geçirilmemişse, şema tanımının depolandığı klasörün adı kullanılır.

`blueprint.json`adlandırılması gereken şema tanımıyla birlikte, şema tanımının meydana gelen yapıtlardır. Her yapıt `artifacts`adlı alt klasörde olmalıdır.
Birlikte yerleştirin, şema tanımınızın yapısı klasörlerde JSON dosyaları olarak aşağıdaki gibi görünmelidir:

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>Şema tanımınızı dışarı aktarma

Şema tanımınızı dışa aktarma adımları basittir. Şema tanımını dışarı aktarmak, paylaşım, yedekleme veya kaynak denetimine yerleştirme için yararlı olabilir.

- **Blueprint** [gerekli]
  - Şema tanımını belirtir
  - Başvuru nesnesini almak için `Get-AzBlueprint` kullanın
- **OutputPath** [gerekli]
  - Şema tanımı JSON dosyalarının kaydedileceği yolu belirtir
  - Çıktı dosyaları, şema tanımının adını taşıyan bir alt klasördeyse
- **Sürüm** (isteğe bağlı)
  - **Blueprint** başvuru nesnesi birden fazla sürüme başvurular içeriyorsa, çıktının sürümünü belirtir.

1. `{subId}`gösterilen abonelikten dışarı aktarmak için şema tanımına bir başvuru alın:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Belirtilen şema tanımını dışarı aktarmak için `Export-AzBlueprintWithArtifact` cmdlet 'ini kullanın:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>Şema tanımınızı içeri aktarma

[Dışarı aktarılan bir şema tanımınız](#export-your-blueprint-definition) veya [gerekli klasör yapısında](#folder-structure-of-a-blueprint-definition)el ile oluşturulmuş bir şema Definition olduktan sonra, bu şema tanımını farklı bir yönetim grubuna veya aboneliğine aktarabilirsiniz.

Yerleşik şema tanımlarının örnekleri için bkz. [GitHub deposu Azure Blueprint](https://github.com/Azure/azure-blueprints/tree/master/samples/builtins).

- **Ad** [gerekli]
  - Yeni şema tanımının adını belirtir
- **Inputpath** [gerekli]
  - Şema tanımının oluşturulacağı yolu belirtir
  - [Gerekli klasör yapısıyla](#folder-structure-of-a-blueprint-definition) eşleşmesi gerekir
- **ManagementGroupId** (isteğe bağlı)
  - Geçerli bağlam varsayılanı değilse, şema tanımını kaydetmek için yönetim grubu kimliği
  - **ManagementGroupId** veya **SubscriptionID** belirtilmelidir
- **Abonelik kimliği** (isteğe bağlı)
  - Geçerli bağlam varsayılanı değilse, şema tanımının kaydedileceği abonelik kimliği
  - **ManagementGroupId** veya **SubscriptionID** belirtilmelidir

1. Belirtilen şema tanımını içeri aktarmak için `Import-AzBlueprintWithArtifact` cmdlet 'ini kullanın:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

Şema tanımı içeri aktarıldıktan sonra, [PowerShell ile atayın](./manage-assignments-ps.md#create-blueprint-assignments).

Gelişmiş şema tanımları oluşturma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Statik ve dinamik parametreleri](../concepts/parameters.md)kullanın.
- [Şema sıralama düzenini](../concepts/sequencing-order.md)özelleştirin.
- [Şema kaynak kilitleme](../concepts/resource-locking.md)ile dağıtımları koruyun.
- [Şemaları kod olarak yönetin](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Şema yaşam döngüsü](../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Genel sorun giderme](../troubleshoot/general.md) adımlarıyla şema atama sorunlarını giderin.