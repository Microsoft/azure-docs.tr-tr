---
title: Yönetim gruplarınız ile çalışma-Azure Idare
description: Yönetim grubu hiyerarşinizi görüntülemeyi, bakımını yapmayı, güncelleştirmeyi ve silmeyi öğrenin.
ms.date: 05/22/2019
ms.topic: conceptual
ms.openlocfilehash: 90f4bacf462ed5f2590f51d15b6b660057c51738
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960235"
---
# <a name="manage-your-resources-with-management-groups"></a>Kaynakları yönetim gruplarıyla yönetme

Kuruluşunuzda birden fazla abonelik varsa bu abonelikler için verimli bir şekilde erişim, ilke ve uyumluluk yönetimi gerçekleştirmek isteyebilirsiniz. Azure yönetim grupları, aboneliklerin üzerinde bir kapsam düzeyi sunar. Abonelikleri "yönetim grupları" adlı kapsayıcılarla düzenler ve idare koşullarınızı bu yönetim gruplarına uygularsınız. Bir yönetim grubu içindeki aboneliklerin tümü otomatik olarak yönetim grubuna uygulanmış olan koşulları devralır.

Yönetim grupları, sahip olabileceğiniz abonelik türüne bakılmaksızın kurumsal düzeyde yönetimi büyük ölçekte sunar.  Yönetim grupları hakkında daha fazla bilgi edinmek için bkz. [Azure Yönetim gruplarıyla kaynaklarınızı düzenleme](overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>Bir yönetim grubunun adını değiştirme

Portal, PowerShell veya Azure CLı kullanarak yönetim grubunun adını değiştirebilirsiniz.

### <a name="change-the-name-in-the-portal"></a>Portalda adı değiştirme

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. **Tüm hizmetler** > **Yönetim grupları**' nı seçin.

1. Yeniden adlandırmak istediğiniz yönetim grubunu seçin.

1. **Ayrıntılar**' ı seçin.

1. Sayfanın üst kısmındaki **grubu yeniden adlandır** seçeneğini belirleyin.

   ![Yönetim grubu sayfasındaki Grup seçeneğini yeniden adlandır](./media/detail_action_small.png)

1. Menü açıldığında, görüntülenmesini istediğiniz yeni adı girin.

   ![Grup bölmesini, yönetim grubunu yeniden adlandırılacak şekilde yeniden adlandır](./media/rename_context.png)

1. **Kaydet**’i seçin.

### <a name="change-the-name-in-powershell"></a>PowerShell 'de adı değiştirme

Görünen adı güncelleştirmek için **Update-AzManagementGroup**kullanın. Örneğin, bir yönetim grubu görünen adını "contoso IT" iken "contoso Group" olarak değiştirmek için aşağıdaki komutu çalıştırın:

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

1. Yönetim grubu altında alt yönetim grubu veya abonelik yok.

   - Bir aboneliği bir yönetim grubundan dışına taşımak için bkz. [aboneliği başka bir yönetim grubuna taşıma](#move-subscriptions-in-the-hierarchy).

   - Bir yönetim grubunu başka bir yönetim grubuna taşımak için bkz. [Yönetim gruplarını hiyerarşide taşıma](#move-management-groups-in-the-hierarchy).

1. Yönetim grubu ("sahip", "katkıda bulunan" veya "yönetim grubu katılımcısı") üzerinde yazma izinleriniz vardır. Sahip olduğunuz izinleri görmek için yönetim grubunu seçin ve ardından **IAM**' i seçin. RBAC rolleri hakkında daha fazla bilgi edinmek için bkz. [RBAC ile erişimi ve Izinleri yönetme](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Portalda Sil

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. **Tüm hizmetler** > **Yönetim grupları**' nı seçin.

1. Silmek istediğiniz yönetim grubunu seçin.

1. **Ayrıntılar**' ı seçin.

1. **Sil** ' i seçin

    > [!TIP]
    > Simge devre dışıysa, simgenin üzerinde fare seçicinizi, nedenini gösterir.

   ![Grup silme seçeneği](./media/delete.png)

1. Yönetim grubunu silmek istediğinizi onaylayan bir pencere açılır.

   ![Grup onay penceresini Sil](./media/delete_confirm.png)

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

Üzerinde doğrudan veya devralınmış bir RBAC rolüne sahip olduğunuz herhangi bir yönetim grubunu görüntüleyebilirsiniz.  

### <a name="view-in-the-portal"></a>Portalda görüntüle

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. **Tüm hizmetler** > **Yönetim grupları**' nı seçin.

1. Yönetim grubu hiyerarşisi sayfası yüklenir. Bu sayfa, erişiminiz olan tüm yönetim gruplarını ve abonelikleri keşfedebileceğiniz yerdir. Grup adı seçildiğinde hiyerarşide bir düzey yer alır. Gezinti, dosya Gezgini ile aynı şekilde çalışmaktadır.

1. Yönetim grubunun ayrıntılarını görmek için yönetim grubunun başlığının yanındaki **(Ayrıntılar)** bağlantısını seçin. Bu bağlantı kullanılamıyorsa, bu yönetim grubunu görüntüleme izniniz yok.

   ![Ana](./media/main.png)

### <a name="view-in-powershell"></a>PowerShell 'de görüntüle

Tüm grupları almak için Get-AzManagementGroup komutunu kullanın.  Yönetim grubu Al PowerShell komutlarını tam listesi için bkz. [az. resources](/powershell/module/az.resources/Get-AzManagementGroup) Modules.  

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

## <a name="move-subscriptions-in-the-hierarchy"></a>Abonelikleri hiyerarşide taşıma

Bir yönetim grubu oluşturmanın bir nedeni, abonelikleri birlikte paketlemenize neden olur. Yalnızca yönetim grupları ve abonelikler başka bir yönetim grubunun alt öğeleri haline getirilebilir. Bir yönetim grubuna taşınan bir abonelik, tüm Kullanıcı erişimini ve ilkelerini üst yönetim grubundan devralır.

Aboneliği taşımak için aşağıdaki RBAC izinlerinin tümünün doğru olması gerekir:

- Alt abonelikte "sahip" rolü.
- Hedef üst yönetim grubundaki "sahip", "katkıda bulunan" veya "yönetim grubu katılımcısı" rolü.
- Mevcut üst yönetim grubunda "Owner", "katkıda bulunan" veya "yönetim grubu katılımcısı" rolü.

Hedef veya var olan üst yönetim grubu kök yönetim grubinise, izin gereksinimleri geçerli değildir. Kök yönetim grubu tüm yeni yönetim grupları ve abonelikler için varsayılan giriş noktası olduğundan, bir öğeyi taşımak için üzerinde izinleriniz olması gerekmez.

Abonelikte sahip rolü geçerli yönetim grubundan devralınmışsa, taşıma hedefleriniz sınırlıdır. Aboneliği yalnızca sahip rolüne sahip olduğunuz başka bir yönetim grubuna taşıyabilirsiniz. Aboneliğin sahipliğini kaybedeceinizden katılımcı olduğunuz bir yönetim grubuna taşıyamazsınız. Abonelik için (yönetim grubundan devralınmaz) sahip rolüne doğrudan atandıysanız, katılımcısı olduğunuz herhangi bir yönetim grubuna taşıyabilirsiniz.

Azure portal hangi izinlere sahip olduğunu görmek için, yönetim grubunu seçin ve ardından **IAM**öğesini seçin. RBAC rolleri hakkında daha fazla bilgi edinmek için bkz. [RBAC ile erişimi ve Izinleri yönetme](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Portalda abonelikleri taşıma

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Bir yönetim grubuna mevcut bir abonelik ekleme

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. **Tüm hizmetler** > **Yönetim grupları**' nı seçin.

1. Üst öğe olmasını planladığınız yönetim grubunu seçin.

1. Sayfanın üst kısmında, **Abonelik Ekle**' yi seçin.

1. Listeden doğru KIMLIĞI olan aboneliği seçin.

   ![Bir yönetim grubuna eklemek için kullanılabilir abonelikler](./media/add_context_sub.png)

1. "Kaydet" i seçin.

#### <a name="remove-a-subscription-from-a-management-group"></a>Bir yönetim grubundan abonelik kaldırma

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. **Tüm hizmetler** > **Yönetim grupları**' nı seçin.

1. Geçerli üst öğe olan planlama yaptığınız yönetim grubunu seçin.  

1. Taşımak istediğiniz listede abonelik için satırın sonundaki elipsi seçin.

   ![Bir yönetim grubunda taşıma seçeneği](./media/move_small.png)

1. **Taşı**' yı seçin.

1. Açılan menüde, **ana yönetim grubunu**seçin.

   ![Bölmeyi üst grubu değiştirmek için taşı](./media/move_small_context.png)

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

## <a name="move-management-groups-in-the-hierarchy"></a>Yönetim gruplarını hiyerarşide taşıma  

Bir üst yönetim grubunu taşıdığınızda, bu grubun altındaki hiyerarşi onunla birlikte taşınır. Yönetim gruplarını taşımanız gereken erişim için bkz. [Yönetim grubu erişimi](overview.md#management-group-access).

### <a name="move-management-groups-in-the-portal"></a>Yönetim gruplarını portalda taşıma

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. **Tüm hizmetler** > **Yönetim grupları**' nı seçin.

1. Üst öğe olmasını planladığınız yönetim grubunu seçin.

1. Sayfanın üst kısmında **Yönetim grubu Ekle**' yi seçin.

1. Açılan menüde, yeni bir yönetim grubu istiyorsanız seçin veya var olan bir yönetim grubunu kullanın.

   - Yeni seçildiğinde yeni bir yönetim grubu oluşturulur.
   - Mevcut olanı seçtiğinizde, bu yönetim grubuna taşıyacağınız tüm yönetim gruplarının açılan listesi görüntülenir.  

   ![Bir yönetim grubunu yeni veya mevcut bir gruba taşıma](./media/add_context_MG.png)

1. **Kaydet**’i seçin.

### <a name="move-management-groups-in-powershell"></a>Yönetim gruplarını PowerShell 'de taşıma

Yönetim grubunu farklı bir grup altında taşımak için PowerShell 'de Update-AzManagementGroup komutunu kullanın.

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

Yönetim grupları [Azure Etkinlik Günlüğü](../../azure-monitor/platform/activity-logs-overview.md)'nde desteklenir. Diğer Azure kaynaklarıyla aynı merkezi konumdaki bir yönetim grubunda gerçekleşen tüm olayları sorgulayabilirsiniz.  Örneğin, belirli bir yönetim grubunda yapılan tüm Rol Atamalarını veya İlke Ataması değişikliklerini görebilirsiniz.

![Yönetim gruplarıyla etkinlik günlükleri](media/al-mg.png)

Azure portalının dışında Yönetim Gruplarını sorgulamak istediğinizde, yönetim gruplarının hedef kapsamı **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** gibi görünür.

## <a name="referencing-management-groups-from-other-resource-providers"></a>Diğer kaynak sağlayıcılardan yönetim gruplarına başvurma

Diğer kaynak sağlayıcılarının eylemlerdeki yönetim gruplarına başvururken, kapsam olarak aşağıdaki yolu kullanın. PowerShell, Azure CLı ve REST API 'Leri kullanılırken bu yol kullanılır.  

>"/providers/Microsoft.Management/managementGroups/{yourMgID}"

Bu yolu kullanmanın bir örneği, PowerShell 'de bir yönetim grubuna yeni bir rol ataması atamasıdır

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Bir yönetim grubunda ilke tanımı alınırken aynı kapsam yolu kullanılır.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>Sonraki adımlar

Yönetim grupları hakkında daha fazla bilgi almak için bkz.:

- [Azure kaynaklarını düzenlemek için yönetim grupları oluşturma](create.md)
- [Yönetim gruplarınızı değiştirme, silme veya yönetme](manage.md)
- [Azure PowerShell Kaynak Modülünde yönetim gruplarını gözden geçirme](/powershell/module/az.resources#resources)
- [REST API'de yönetim gruplarını gözden geçirme](/rest/api/resources/managementgroups)
- [Azure CLI'de yönetim gruplarını gözden geçirme](/cli/azure/account/management-group)