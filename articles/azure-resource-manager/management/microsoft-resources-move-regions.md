---
title: Microsoft. resources içindeki kaynaklar için bölgeleri taşıma
description: Microsoft. Resources ad alanındaki kaynakların yeni bölgelere nasıl taşınacağını gösterir.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 898e5efef22f76dc07395fcfcad413ef4582dafd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725881"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Microsoft. resources kaynaklarını yeni bölgeye taşıma

Var olan bir kaynağı yeni bir bölgeye taşımanız gerekebilir. Bu makalede, Microsoft. Resources ad alanındaki iki kaynak türünün (Templatespec ve deploymentScripts) nasıl taşınacağı gösterilmektedir.

## <a name="move-template-specs-to-new-region"></a>Şablon özelliklerini yeni bölgeye taşı

Bir bölgede bir [şablon](../templates/template-specs.md) belirtimine sahipseniz ve yeni bölgeye taşımak istiyorsanız, şablon belirtimini dışarı aktarabilir ve yeniden dağıtabilirsiniz.

1. Var olan bir şablon belirtimini dışarı aktarmak için komutunu kullanın. Parametre değerleri için, dışarı aktarmak istediğiniz şablon belirtimi ile eşleşen değerleri sağlayın.

   Azure PowerShell için şunu kullanın:

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   Azure CLI için şunu kullanın:

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. Yeni bir şablon belirtimi oluşturmak için, aktarılmış şablon belirtimini kullanın. Aşağıdaki örneklerde `westus` Yeni bölge gösterilmektedir, ancak istediğiniz bölgeyi sağlayabilirsiniz.

   Azure PowerShell için şunu kullanın:

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   Azure CLI için şunu kullanın:

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>Dağıtım betiklerini yeni bölgeye taşı

1. Yeni bir bölgeye taşımak istediğiniz dağıtım betiğini içeren kaynak grubunu seçin.

1. [Şablonu dışarı aktarın](../templates/export-template-portal.md). Dışarı aktarırken, dağıtım betiğini ve diğer gerekli kaynakları seçin.

1. İçe aktarılmış şablonda, aşağıdaki özellikleri silin:

   * Değerine
   * principalId
   * clientId

1. İçe aktarılmış şablonda dağıtım betiğinin bölgesi için sabit kodlanmış bir değer vardır.

   ```json
   "location": "westus2",
   ```

   Şablonu, konumu ayarlamaya yönelik bir parametreye izin verecek şekilde değiştirin. Daha fazla bilgi için bkz. [ARM şablonunda kaynak konumunu ayarlama](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [İçe aktarılmış şablonu dağıtın](../templates/deploy-powershell.md) ve dağıtım betiği için yeni bir bölge belirtin.

## <a name="next-steps"></a>Sonraki adımlar

* Kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma hakkında bilgi edinmek için bkz. [kaynakları yeni bir kaynak grubuna veya aboneliğe taşıma](move-resource-group-and-subscription.md).
* Kaynakları yeni bir bölgeye taşıma hakkında bilgi edinmek için bkz. [kaynakları bölgeler arasında taşıma](move-resources-overview.md#move-resources-across-regions).
