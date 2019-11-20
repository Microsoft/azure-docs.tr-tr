---
title: Uyumlu olmayan kaynakları düzeltme
description: Bu kılavuzda, Azure Ilkesindeki ilkelerle uyumlu olmayan kaynakların düzeltilme adımları gösterilmektedir.
ms.date: 09/09/2019
ms.topic: conceptual
ms.openlocfilehash: 53ca21e4b8a1f3e7973706acd10601593efc3448
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959494"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Azure İlkesi ile uyumlu olmayan kaynakları Düzelt

Bir **Deployifnotexists** veya **MODIFY** Policy ile uyumlu olmayan kaynaklar, **Düzeltme**aracılığıyla uyumlu bir duruma yerleştirilebilir. Düzeltme, Azure Ilkesini, mevcut kaynaklarınız üzerinde atanan ilkenin **Deployifnotexists** efektini veya etiket **işlemlerini** çalıştırmak üzere karşılaştırarak gerçekleştirilir. Bu makalede, Azure Ilkesini anlamak ve düzeltmeyi gerçekleştirmek için gereken adımlar gösterilir.

## <a name="how-remediation-security-works"></a>Düzeltme güvenliği nasıl çalışır

Azure Ilkesi, şablonu **Deployifnotexists** ilke tanımında çalıştırdığında, bu, [yönetilen bir kimlik](../../../active-directory/managed-identities-azure-resources/overview.md)kullanılarak yapılır.
Azure Ilkesi, her atama için yönetilen bir kimlik oluşturur, ancak yönetilen kimliğe hangi rollerin verilmek üzere ayrıntıları içermelidir. Yönetilen kimlik rolleri eksikse, ilke veya girişim ataması sırasında bu hata görüntülenir. Portal kullanılırken, Azure Ilkesi, atama başlatıldıktan sonra yönetilen kimliğe listelenen roller için otomatik olarak izin verir.

![Yönetilen kimlik - eksik rol](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> **Deployifnotexists** veya **MODIFY** tarafından değiştirilen bir kaynak ilke atamasının kapsamı dışındaysa veya şablon, ilke atamasının kapsamı dışında kaynaklardaki özelliklere erişirse, atamaya ait yönetilen kimliğe [el ile erişim izni](#manually-configure-the-managed-identity) verilmelidir veya düzeltme dağıtımı başarısız olur.

## <a name="configure-policy-definition"></a>İlke tanımı'nı yapılandırma

İlk adım, dahil edilen şablonunuzun içeriğini başarılı bir şekilde dağıtmak için, **dağıtım** ve ilke tanımında **yapılan ihtiyaçları karşılayan** rolleri tanımlamaktır. Altında **ayrıntıları** özelliği eklemek bir **roleDefinitionIds** özelliği. Bu özellik, ortamınızdaki eşleşen dizeler dizisidir. Tam bir örnek için, [Deployifnotexists örneğine](../concepts/effects.md#deployifnotexists-example) veya [değiştirme örneklerine](../concepts/effects.md#modify-examples)bakın.

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**Roledefinitionıds** özelliği, tam kaynak tanımlayıcıyı kullanır ve rolün kısa **roleName** özelliğini almaz. Ortamınızı 'Katkıda bulunan' rolü için kimliği almak için aşağıdaki kodu kullanın:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Yönetilen kimlik el ile yapılandırma

Portalı kullanarak bir atama oluştururken, Azure Ilkesi yönetilen kimliği oluşturur ve rol **Definitionıds**içinde tanımlanan rollere izin verir. Aşağıdaki durumlarda, yönetilen bir kimlik oluşturmak ve izinleri atamak için adımları el ile yapılması gerekir:

- (Örneğin, Azure PowerShell) SDK'sı kullanırken
- Atama kapsamı dışında bir kaynağa şablon tarafından değiştirildiğinde
- Atama kapsamı dışında bir kaynağa şablon tarafından ne zaman okuma

> [!NOTE]
> Şu anda bu özelliği destekleyen yalnızca SDK'ları şunlardır: Azure PowerShell ve .NET.

### <a name="create-managed-identity-with-powershell"></a>PowerShell ile yönetilen kimlik oluşturma

İlke ataması sırasında yönetilen bir kimlik oluşturmak için **konumu** tanımlanmalıdır ve **Assignıdentity** kullanılır. Aşağıdaki örnek, yerleşik ilke tanımı alır **dağıtmak, SQL veritabanı saydam veri şifrelemesi**, hedef kaynak grubu ayarlar ve ardından ataması oluşturulur.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

`$assignment` Değişkeni artık yönetilen kimlikle birlikte bir ilke ataması oluştururken, döndürülen değerlerin standart asıl kimliği içeriyor. Aracılığıyla erişilebilir `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>PowerShell ile rol verme tanımlanan

Gerekli rolleri verilmeden önce yeni bir yönetilen kimlik Azure Active Directory aracılığıyla çoğaltma tamamlamanız gerekir. Çoğaltma tamamlandıktan sonra aşağıdaki örnek, ilke tanımını **Roledefinitionıds** için `$policyDef` yineler ve yeni yönetilen kimliğe roller vermek için [New-azroleatama](/powershell/module/az.resources/new-azroleassignment) kullanır.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Portal üzerinden rolleri verme tanımlanan

Portalı kullanarak tanımlanmış rollere atamanın yönetilen kimlik vermek için iki yolu vardır **erişim denetimi (IAM)** veya ilke veya girişim ataması düzenleme ve tıklatarak **Kaydet**.

Rol atama için yönetilen kimlik eklemek için aşağıdaki adımları izleyin:

1. Azure portalında **Tüm hizmetler**’e tıkladıktan sonra **İlke**'yi arayıp seçerek Azure İlkesi hizmetini başlatın.

1. Azure İlkesi sayfasının sol tarafından **Atamalar**'ı seçin.

1. Yönetilen bir kimliğe sahip ataması bulun ve adına tıklayın.

1. Bulma **atama kimliği** düzenleme sayfası özelliği. Atama kimliği gibi bir şey olacaktır:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Yönetilen kimlik adı olduğundan ataması kaynak kimliği son bölümüdür `2802056bfc094dfb95d4d7a5` Bu örnekte. Atama kaynak kimliği. Bu bölümü kopyalayın

1. Kaynak veya el ile eklenen rol tanımı gereken kaynakları üst kapsayıcının (kaynak grubu, abonelik, yönetim grubu) gidin.

1. Tıklayın **erişim denetimi (IAM)** tıklayın ve bağlantı Kaynaklar sayfasında **+ rol ataması Ekle** erişim denetimi sayfanın üstünde.

1. Eşleşen uygun rolü seçin bir **roleDefinitionIds** ilke tanımından.
   Bırakın **erişim Ata** 'Azure AD kullanıcı, Grup veya uygulama' varsayılan olarak ayarla. İçinde **seçin** kutusuna yapıştırın veya önceden bulunan ataması kaynak kimliği bölümünü yazın. Arama tamamlandığında, kimliği'ni seçin ve aynı ada sahip nesneye tıklayın **Kaydet**.

## <a name="create-a-remediation-task"></a>Düzeltme görev oluşturma

### <a name="create-a-remediation-task-through-portal"></a>Portal aracılığıyla düzeltme görevi oluşturma

Değerlendirme sırasında, **Deployifnotexists** veya **değişiklik** efektlerine sahip ilke ataması uyumlu olmayan kaynaklar olup olmadığını belirler. Uyumlu olmayan kaynakları bulunduğunda ayrıntıları sağlanır **düzeltme** sayfası. Uyumlu olmayan kaynakları olan ilkeleri listesinde birlikte tetiklemeye yönelik seçeneği olan bir **düzeltme görev**. Bu seçenek, **Deployifnotexists** şablonundan veya **değiştirme** işlemlerinden bir dağıtım oluşturur.

Oluşturmak için bir **düzeltme görev**, şu adımları izleyin:

1. Azure portalında **Tüm hizmetler**’e tıkladıktan sonra **İlke**'yi arayıp seçerek Azure İlkesi hizmetini başlatın.

   ![Tüm hizmetlerde Ilke ara](../media/remediate-resources/search-policy.png)

1. Seçin **düzeltme** Azure İlkesi sayfasının sol tarafındaki.

   ![Ilke sayfasında düzeltme ' yi seçin](../media/remediate-resources/select-remediation.png)

1. Tüm **Deployifnotexists** ve uyumlu olmayan kaynaklarla ilke atamalarını **değiştirme** , sekme ve veri tablosunu düzeltme **ilkelerine** dahildir. Bir ilkeyle uyumlu olmayan kaynakları tıklayın. **Yeni bir düzeltme görev** sayfası açılır.

   > [!NOTE]
   > Açmak için alternatif bir yolu **düzeltme görev** sayfasıdır bulup ilkeden tıklayarak **Uyumluluk** sayfasında'a tıklayın **düzeltme Görevi Oluştur** düğmesi.

1. Üzerinde **yeni bir düzeltme görev** sayfasında, kaynakları kullanarak düzeltmek için filtre **kapsam** alt kaynakları burada ilkenin atandığı seçmek için üç nokta simgesini (aşağı ayrı kaynak dahil nesneler). Ayrıca, **konumları** daha da fazla filtrelemek için kaynakları açılır. Yalnızca kaynak tabloda listelenen düzeltilebilir.

   ![Düzelt-hangi kaynakların düzeltileceği seçin](../media/remediate-resources/select-resources.png)

1. Kaynakları tıklayarak filtrelendi sonra düzeltme görevi Başlat **düzelt**. İlke uyumluluk sayfası açılacak **düzeltme görevleri** görevleri ilerleme durumunu göstermek için sekmesinde.

   ![Düzeltme görevlerinin ilerlemesini düzelt](../media/remediate-resources/task-progress.png)

1. Tıklayarak **düzeltme görev** İlkesi uyumluluk sayfasından ilerleme durumu hakkında ayrıntılı bilgi edinmek için. Görev için kullanılan filtreleme düzeltilen kaynakların listesini birlikte gösterilir.

1. Gelen **düzeltme görev** sayfasında, ya da düzeltme görevin dağıtım görüntülemek için bir kaynak veya kaynak üzerinde sağ tıklayın. Satırın sonunda tıklayarak **ilgili olaylar** gibi bir hata iletisi ayrıntılarını görmek için.

   ![Düzeltme - kaynak görev bağlam menüsü](../media/remediate-resources/resource-task-context-menu.png)

Dağıtılan kaynakları aracılığıyla bir **düzeltme görev** eklenir **dağıtılan kaynakların** uyumluluk İlkesi sayfasının bir sekmesinde.

### <a name="create-a-remediation-task-through-azure-cli"></a>Azure CLı aracılığıyla bir düzeltme görevi oluşturma

Azure CLı ile bir **Düzeltme görevi** oluşturmak için `az policy remediation` komutlarını kullanın. `{subscriptionId}` abonelik KIMLIĞINIZ ile değiştirin ve `{myAssignmentId}` **Deployifnotexists** veya Ilke atama kimliğini **değiştirin** .

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Diğer düzeltme komutları ve örnekleri için, [az Policy düzeltme](/cli/azure/policy/remediation) komutlarına bakın.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Azure PowerShell aracılığıyla düzeltme görevi oluşturma

Azure PowerShell bir **Düzeltme görevi** oluşturmak için `Start-AzPolicyRemediation` komutlarını kullanın. `{subscriptionId}` abonelik KIMLIĞINIZ ile değiştirin ve `{myAssignmentId}` **Deployifnotexists** veya Ilke atama kimliğini **değiştirin** .

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Diğer düzeltme cmdlet 'leri ve örnekleri için bkz. [az. Policınsıghts](/powershell/module/az.policyinsights/#policy_insights) modülü.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ilke örneklerindeki](../samples/index.md)örnekleri gözden geçirin.
- [Azure İlkesi tanımı yapısını](../concepts/definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](../concepts/effects.md) konusunu gözden geçirin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](getting-compliance-data.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.