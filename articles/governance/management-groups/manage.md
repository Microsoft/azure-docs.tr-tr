---
title: Yönetim gruplarınız ile çalışma-Azure Idare
description: Yönetim grubu hiyerarşinizi görüntülemeyi, bakımını yapmayı, güncelleştirmeyi ve silmeyi öğrenin.
ms.date: 01/15/2021
ms.topic: conceptual
ms.openlocfilehash: 09d11450ff63046ab458f930f64534d10863f451
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258359"
---
# <a name="manage-your-resources-with-management-groups"></a>Kaynakları yönetim gruplarıyla yönetme

Kuruluşunuzda birden fazla abonelik varsa bu abonelikler için verimli bir şekilde erişim, ilke ve uyumluluk yönetimi gerçekleştirmek isteyebilirsiniz. Azure yönetim grupları aboneliklerin üzerinde bir kapsam düzeyi sağlar. Abonelikleri "yönetim grupları" adlı kapsayıcılarla düzenler ve idare koşullarınızı bu yönetim gruplarına uygularsınız. Bir yönetim grubu içindeki aboneliklerin tümü otomatik olarak yönetim grubuna uygulanmış olan koşulları devralır.

Yönetim grupları, sahip olabileceğiniz abonelik türüne bakılmaksızın kurumsal düzeyde yönetimi büyük ölçekte sunar. Yönetim grupları hakkında daha fazla bilgi edinmek için bkz. [Azure Yönetim gruplarıyla kaynaklarınızı düzenleme](./overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

> [!IMPORTANT]
> Azure Resource Manager Kullanıcı belirteçleri ve yönetim grubu önbelleği, yenilemeye zorlanmadan önce 30 dakika sürer. Bir yönetim grubu veya aboneliğini taşıma gibi herhangi bir eylem gerçekleştirdikten sonra, bu işlemin görünmesi 30 dakika kadar sürebilir. Güncelleştirmeleri daha erken görmek için, tarayıcıyı yenileyerek, oturum açıp kapatarak veya yeni bir belirteç isteyerek belirtecinizi güncelleştirmeniz gerekir.  

## <a name="change-the-name-of-a-management-group"></a>Bir yönetim grubunun adını değiştirme

Portal, PowerShell veya Azure CLı kullanarak yönetim grubunun adını değiştirebilirsiniz.

### <a name="change-the-name-in-the-portal"></a>Portalda adı değiştirme

1. [Azure Portal](https://portal.azure.com)oturum açın.

1. **Tüm hizmetler**  >  **Yönetim gruplarını** seçin.

1. Yeniden adlandırmak istediğiniz yönetim grubunu seçin.

1. **Ayrıntılar**' ı seçin.

1. Sayfanın üst kısmındaki **grubu yeniden adlandır** seçeneğini belirleyin.

   :::image type="content" source="./media/detail_action_small.png" alt-text="Yönetim grubu sayfasında Eylem çubuğunun ve ' grubu yeniden adlandır ' düğmesinin ekran görüntüsü." border="false":::

1. Menü açıldığında, görüntülenmesini istediğiniz yeni adı girin.

   :::image type="content" source="./media/rename_context.png" alt-text="Grubu yeniden adlandır penceresinin ve bir yönetim grubunu yeniden adlandırma seçeneklerinin ekran görüntüsü." border="false":::

1. **Kaydet**’i seçin.

### <a name="change-the-name-in-powershell"></a>PowerShell 'de adı değiştirme

Görünen adı güncelleştirmek için **Update-AzManagementGroup** kullanın. Örneğin, bir yönetim grubu görünen adını "contoso IT" iken "contoso Group" olarak değiştirmek için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Azure CLı 'de adı değiştirme

Azure CLı için Update komutunu kullanın.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Yönetim grubunu silme

Bir yönetim grubunu silmek için aşağıdaki gereksinimlerin karşılanması gerekir:

1. Yönetim grubu altında alt yönetim grubu veya abonelik yok. Bir aboneliği veya yönetim grubunu başka bir yönetim grubuna taşımak için bkz. [hiyerarşideki Yönetim gruplarını ve abonelikleri taşıma](#moving-management-groups-and-subscriptions).

1. Yönetim grubu ("sahip", "katkıda bulunan" veya "yönetim grubu katılımcısı") üzerinde yazma izinlerine sahip olmanız gerekir. Sahip olduğunuz izinleri görmek için yönetim grubunu seçin ve ardından **IAM**' i seçin. Azure rolleri hakkında daha fazla bilgi için bkz.  
   [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="delete-in-the-portal"></a>Portalda Sil

1. [Azure Portal](https://portal.azure.com)oturum açın.

1. **Tüm hizmetler**  >  **Yönetim gruplarını** seçin.

1. Silmek istediğiniz yönetim grubunu seçin.

1. **Ayrıntılar**' ı seçin.

1. **Sil**’i seçin

   :::image type="content" source="./media/delete.png" alt-text="' Sil ' düğmesi vurgulanmış şekilde yönetim grubu sayfasının ekran görüntüsü." border="false":::

   > [!TIP]
   > Simge devre dışıysa, simgenin üzerinde fare seçicinizi, nedenini gösterir.

1. Yönetim grubunu silmek istediğinizi onaylayan bir pencere açılır.

   :::image type="content" source="./media/delete_confirm.png" alt-text="Bir yönetim grubunu silmek için ' Grup silme ' onay iletişim kutusunun ekran görüntüsü." border="false":::

1. **Evet**’i seçin.

### <a name="delete-in-powershell"></a>PowerShell 'de Sil

Yönetim gruplarını silmek için PowerShell içindeki **Remove-AzManagementGroup** komutunu kullanın.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Azure CLI ile silme

Azure CLı ile az Account Management-Group Delete komutunu kullanın.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Yönetim gruplarını görüntüle

Üzerinde doğrudan veya devralınmış bir Azure rolüne sahip olduğunuz herhangi bir yönetim grubunu görüntüleyebilirsiniz.  

### <a name="view-in-the-portal"></a>Portalda görüntüle

1. [Azure Portal](https://portal.azure.com)oturum açın.

1. **Tüm hizmetler**  >  **Yönetim gruplarını** seçin.

1. Yönetim grubu hiyerarşisi sayfası yüklenir. Bu sayfa, erişiminiz olan tüm yönetim gruplarını ve abonelikleri keşfedebileceğiniz yerdir. Grup adını seçtiğinizde hiyerarşide daha düşük bir düzeye gidersiniz. Gezinti, dosya Gezgini ile aynı şekilde çalışmaktadır.

1. Yönetim grubunun ayrıntılarını görmek için yönetim grubunun başlığının yanındaki **(Ayrıntılar)** bağlantısını seçin. Bu bağlantı kullanılamıyorsa, bu yönetim grubunu görüntüleme izniniz yok.

   :::image type="content" source="./media/main.png" alt-text="Alt yönetim grupları ve abonelikleri gösteren yönetim grupları sayfasının ekran görüntüsü." border="false":::

### <a name="view-in-powershell"></a>PowerShell 'de görüntüle

Tüm grupları almak için Get-AzManagementGroup komutunu kullanın. Yönetim grubu Al PowerShell komutlarını tam listesi için bkz. [az. resources](/powershell/module/az.resources/Get-AzManagementGroup) Modules.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Tek bir yönetim grubunun bilgileri için-GroupName parametresini kullanın

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Belirli bir yönetim grubunu ve altındaki hiyerarşinin tüm düzeylerini döndürmek için **-Expand** ve **-Recurse** parametreleri kullanın.  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Azure CLı 'de görüntüle

Tüm grupları almak için List komutunu kullanın.  

```azurecli-interactive
az account management-group list
```

Tek bir yönetim grubunun bilgileri için göster komutunu kullanın

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Belirli bir yönetim grubunu ve altındaki hiyerarşinin tüm düzeylerini döndürmek için **-Expand** ve **-Recurse** parametreleri kullanın.

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>Yönetim gruplarını ve abonelikleri taşıma   

Bir yönetim grubu oluşturmanın bir nedeni, abonelikleri birlikte paketlemenize neden olur. Yalnızca yönetim grupları ve abonelikler başka bir yönetim grubunun alt öğeleri haline getirilebilir. Bir yönetim grubuna taşınan bir abonelik, tüm Kullanıcı erişimini ve ilkelerini üst yönetim grubundan devralır

Bir yönetim grubu veya aboneliğini başka bir yönetim grubunun alt öğesi olacak şekilde taşırken, üç kuralın doğru olarak değerlendirililmesi gerekir.

Taşıma eylemini gerçekleştiriyorsanız şunlar gerekir: 

- Alt abonelik veya yönetim grubundaki yönetim grubu yazma ve rol atama yazma izinleri.
  - Yerleşik rol örneği **sahibi**
- Hedef üst yönetim grubunda yönetim grubu yazma erişimi.
  - Yerleşik rol örneği: **sahip**, **katkıda bulunan**, **Yönetim grubu katılımcısı**
- Yönetim grubu var olan üst yönetim grubu üzerinde yazma erişimi.
  - Yerleşik rol örneği: **sahip**, **katkıda bulunan**, **Yönetim grubu katılımcısı**

**Özel durum**: hedef veya var olan üst yönetim grubu kök yönetim grubinise, izin gereksinimleri geçerli değildir. Kök yönetim grubu tüm yeni yönetim grupları ve abonelikler için varsayılan giriş noktası olduğundan, bir öğeyi taşımak için üzerinde izinleriniz olması gerekmez.

Abonelikte sahip rolü geçerli yönetim grubundan devralınmışsa, taşıma hedefleriniz sınırlıdır. Aboneliği yalnızca sahip rolüne sahip olduğunuz başka bir yönetim grubuna taşıyabilirsiniz. Aboneliğin sahipliğini kaybedeceinizden, aboneliği yalnızca katılımcısı olduğunuz bir yönetim grubuna taşıyamazsınız. Aboneliğin sahip rolüne doğrudan atandıysanız, bunu katılımcı olduğunuz herhangi bir yönetim grubuna taşıyabilirsiniz.

Azure portal hangi izinlere sahip olduğunu görmek için, yönetim grubunu seçin ve ardından **IAM** öğesini seçin. Azure rolleri hakkında daha fazla bilgi edinmek için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="move-subscriptions"></a>Abonelikleri taşıma 

### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>Portalda bir yönetim grubuna mevcut bir abonelik ekleme

1. [Azure Portal](https://portal.azure.com)oturum açın.

1. **Tüm hizmetler**  >  **Yönetim gruplarını** seçin.

1. Üst öğe olmasını planladığınız yönetim grubunu seçin.

1. Sayfanın üst kısmında, **Abonelik Ekle**' yi seçin.

1. Listeden doğru KIMLIĞI olan aboneliği seçin.

   :::image type="content" source="./media/add_context_sub.png" alt-text="Bir yönetim grubuna eklemek üzere mevcut bir aboneliği seçmek için ' Abonelik Ekle ' seçeneklerinin ekran görüntüsü." border="false":::

1. "Kaydet" i seçin.

### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>Portalda bir yönetim grubundan abonelik kaldırma

1. [Azure Portal](https://portal.azure.com)oturum açın.

1. **Tüm hizmetler**  >  **Yönetim gruplarını** seçin.

1. Geçerli üst öğe olan planlama yaptığınız yönetim grubunu seçin.  

1. Taşımak istediğiniz listede abonelik için satırın sonundaki elipsi seçin.

   :::image type="content" source="./media/move_small.png" alt-text="' Taşı ' seçeneğini belirlemek için bir abonelik için alternatif menünün ekran görüntüsü." border="false":::

1. **Taşı**' yı seçin.

1. Açılan menüde, **ana yönetim grubunu** seçin.

   :::image type="content" source="./media/move_small_context.png" alt-text="Bir aboneliği farklı bir yönetim grubuna taşımak için ' Taşı ' penceresinin ve seçeneklerinin ekran görüntüsü." border="false":::

1. **Kaydet**’i seçin.

### <a name="move-subscriptions-in-powershell"></a>Abonelikleri PowerShell 'e taşıma

Bir aboneliği PowerShell 'de taşımak için New-AzManagementGroupSubscription komutunu kullanın.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

İle abonelik arasındaki bağlantıyı ve yönetim grubunu kaldırmak için Remove-AzManagementGroupSubscription komutunu kullanın.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Azure CLı 'de abonelikleri taşıma

Bir aboneliği CLı 'da taşımak için Add komutunu kullanın.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Aboneliği yönetim grubundan kaldırmak için abonelik kaldırma komutunu kullanın.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-arm-template"></a>ARM şablonunda abonelikleri taşıma

Bir aboneliği Azure Resource Manager şablonunda (ARM şablonu) taşımak için aşağıdaki şablonu kullanın.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMgId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the management group that you want to move the subscription to."
            }
        },
        "subscriptionId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the existing subscription to move."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "type": "Microsoft.Management/managementGroups/subscriptions",
            "apiVersion": "2020-05-01",
            "name": "[concat(parameters('targetMgId'), '/', parameters('subscriptionId'))]",
            "properties": {
            }
        }
    ],
    "outputs": {}
}
```

## <a name="move-management-groups"></a>Yönetim gruplarını taşıma 

### <a name="move-management-groups-in-the-portal"></a>Yönetim gruplarını portalda taşıma

1. [Azure Portal](https://portal.azure.com)oturum açın.

1. **Tüm hizmetler**  >  **Yönetim gruplarını** seçin.

1. Üst öğe olmasını planladığınız yönetim grubunu seçin.

1. Sayfanın üst kısmında **Yönetim grubu Ekle**' yi seçin.

1. Açılan menüde, yeni bir yönetim grubu istiyorsanız seçin veya var olan bir yönetim grubunu kullanın.

   - Yeni seçildiğinde yeni bir yönetim grubu oluşturulur.
   - Mevcut olanı seçtiğinizde, bu yönetim grubuna taşıyacağınız tüm yönetim gruplarının açılan listesi görüntülenir.  

   :::image type="content" source="./media/add_context_MG.png" alt-text="Yeni bir yönetim grubu oluşturmak için ' yönetim grubu Ekle ' seçeneklerinin ekran görüntüsü." border="false":::

1. **Kaydet**’i seçin.

### <a name="move-management-groups-in-powershell"></a>Yönetim gruplarını PowerShell 'de taşıma

Yönetim grubunu farklı bir grup altında taşımak için PowerShell 'deki Update-AzManagementGroup komutunu kullanın.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Azure CLı 'de Yönetim gruplarını taşıma

Bir yönetim grubunu Azure CLı ile taşımak için Update komutunu kullanın.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Etkinlik günlüklerini kullanarak yönetim gruplarını denetleme

Yönetim grupları [Azure Etkinlik Günlüğü](../../azure-monitor/essentials/platform-logs-overview.md)'nde desteklenir. Diğer Azure kaynaklarıyla aynı merkezi konumdaki bir yönetim grubunda gerçekleşen tüm olayları sorgulayabilirsiniz. Örneğin, belirli bir yönetim grubunda yapılan tüm Rol Atamalarını veya İlke Ataması değişikliklerini görebilirsiniz.

:::image type="content" source="./media/al-mg.png" alt-text="Seçili yönetim grubuyla ilgili etkinlik günlüklerinin ve işlemlerinin ekran görüntüsü." border="false":::

Azure portalının dışında Yönetim Gruplarını sorgulamak istediğinizde, yönetim gruplarının hedef kapsamı **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** gibi görünür.

## <a name="referencing-management-groups-from-other-resource-providers"></a>Diğer kaynak sağlayıcılardan yönetim gruplarına başvurma

Diğer kaynak sağlayıcılarının eylemlerdeki yönetim gruplarına başvururken, kapsam olarak aşağıdaki yolu kullanın. PowerShell, Azure CLı ve REST API 'Leri kullanılırken bu yol kullanılır.  

`/providers/Microsoft.Management/managementGroups/{yourMgID}`

Bu yolu kullanmanın bir örneği, PowerShell 'de bir yönetim grubuna yeni bir rol ataması atamasıdır:

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Bir yönetim grubunda ilke tanımı alınırken aynı kapsam yolu kullanılır.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2019-09-01
```

## <a name="next-steps"></a>Sonraki adımlar

Yönetim grupları hakkında daha fazla bilgi almak için bkz.:

- [Azure kaynaklarını düzenlemek için yönetim grupları oluşturma](./create-management-group-portal.md)
- [Yönetim gruplarınızı değiştirme, silme veya yönetme](./manage.md)
- [Azure PowerShell Kaynak Modülünde yönetim gruplarını gözden geçirme](/powershell/module/az.resources#resources)
- [REST API'de yönetim gruplarını gözden geçirme](/rest/api/managementgroups/managementgroups)
- [Azure CLI'de yönetim gruplarını gözden geçirme](/cli/azure/account/management-group)
