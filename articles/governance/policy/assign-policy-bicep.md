---
title: 'Hızlı başlangıç: Bıcep (Önizleme) dosyası ile yeni ilke ataması'
description: Bu hızlı başlangıçta, uyumlu olmayan kaynakları belirlemek üzere bir ilke ataması oluşturmak için bir Bıcep (Önizleme) dosyası kullanırsınız.
ms.date: 04/01/2021
ms.topic: quickstart
ms.custom: subject-bicepqs
ms.openlocfilehash: 17460f9a06d7225d50933608645ea8aea2f5e8f6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223989"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-bicep-file"></a>Hızlı başlangıç: bir Bıcep dosyası kullanarak uyumlu olmayan kaynakları belirlemek için bir ilke ataması oluşturma

Azure’da uyumluluğu anlamanın ilk adımı, kaynaklarınızın durumunu belirlemektir.
Bu hızlı başlangıç, yönetilen diskler kullanmayan sanal makineleri belirlemek üzere bir ilke ataması oluşturmak için bir Azure Resource Manager şablonuna derlenen bir [bicep (Önizleme)](https://github.com/Azure/bicep) dosyası kullanma işleminde size kılavuzluk edecek. Bu işlemin sonunda, yönetilen disk kullanmayan sanal makineleri başarılı bir şekilde belirlemiş olacaksınız. Bu sanal makineler, ilke ataması ile _uyumsuzdur_.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portal açılır.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure 'a Azure Ilkesi atamak için ARM şablonunu dağıtmaya yönelik düğme." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.
- Bıcep sürümü `0.3` veya üzeri yüklendi. Henüz Bıcep CLı yoksa veya güncelleştirmeniz gerekiyorsa bkz. [Bıcep 'yi (Önizleme) yüklemek](../../azure-resource-manager/templates/bicep-install.md).

## <a name="review-the-bicep-file"></a>Bıcep dosyasını gözden geçirme

Bu hızlı başlangıçta, bir ilke ataması oluşturup _yönetilen diskleri kullanmayan VM 'Leri denetle_ () adlı yerleşik bir ilke tanımı atayabilirsiniz `06a78e20-9358-41c9-923c-fb736d382a4d` . Kullanılabilir yerleşik ilkelerin kısmi listesi için bkz. [Azure ilke örnekleri](./samples/index.md).

Aşağıdaki bicep dosyasını şöyle oluşturun `assignment.bicep` :

```bicep
param policyAssignmentName string = 'audit-vm-manageddisks'
param policyDefinitionID string = '/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d'

resource assignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
    name: policyAssignmentName
    properties: {
        scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
        policyDefinitionId: policyDefinitionID
    }
}

output assignmentId string = assignment.id
```

Dosyada tanımlanan kaynak:

- [Microsoft. Authorization/Poliyasatamaları](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>Şablonu dağıtma

> [!NOTE]
> Azure Ilke hizmeti ücretsizdir. Daha fazla bilgi için bkz. [Azure Ilkesine genel bakış](./overview.md).

Bıcep CLı yüklendikten ve Dosya oluşturulduktan sonra, Bıcep dosyasını ile dağıtabilirsiniz:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name PolicyDeployment `
  -ResourceGroupName PolicyGroup `
  -TemplateFile assignment.bicep
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name PolicyDeployment \
  --resource-group PolicyGroup \
  --template-file assignment.bicep
```

---

Bazı ek kaynaklar:

- Daha fazla örnek şablonu bulmak için bkz. [Azure hızlı başlangıç şablonu](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Şablon başvurusunu görmek için [Azure şablon başvurusu](/azure/templates/microsoft.authorization/allversions)' na gidin.
- ARM şablonları geliştirmeyi öğrenmek için [Azure Resource Manager belgelerine](../../azure-resource-manager/management/overview.md)bakın.
- Abonelik düzeyinde dağıtım hakkında bilgi edinmek için bkz. [abonelik düzeyinde kaynak grupları ve kaynaklar oluşturma](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

Sayfanın sol tarafındaki **Uyumluluk** ' i seçin. Ardından, oluşturduğunuz _yönetilen diskler ilke atamasını kullanmayan VM 'Leri denetle_ seçeneğini bulun.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Ilke uyumluluğu sayfasındaki uyumluluk ayrıntılarının ekran görüntüsü." border="false":::

Bu yeni atamayla uyumlu olmayan mevcut kaynaklar varsa **uyumlu olmayan kaynaklar** altında görünürler.

Daha fazla bilgi için bkz. [Uyumluluk nasıl çalıştığını öğrenin](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Oluşturulan atamayı kaldırmak için şu adımları izleyin:

1. Azure İlkesi sayfasının sol tarafından **Uyumluluk**’u (veya **Atamalar**’ı) seçin ve oluşturduğunuz _Yönetilen disk kullanmayan VM'leri denetle_ ilke atamasını bulun.

1. Yönetilen diskler ilkesi atamasını _kullanmayan VM 'Leri denetle_ ' ye sağ tıklayın ve **atamayı Sil**' i seçin.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Uyumluluk sayfasından bir atamayı silmek için bağlam menüsünü kullanmanın ekran görüntüsü." border="false":::

1. Dosyayı silin `assignment.bicep` .

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir kapsama yerleşik bir ilke tanımı atamış ve uyumluluk raporunu değerlendirdiniz. İlke tanımı, kapsamdaki tüm kaynakların uyumlu olduğunu doğrular ve hangilerinin olduğunu tanımlar.

Yeni kaynakların uyumlu olduğunu doğrulamak üzere ilke atama hakkında daha fazla bilgi edinmek için öğreticiye geçin:

> [!div class="nextstepaction"]
> [İlke oluşturma ve yönetme](./tutorials/create-and-manage.md)