---
title: Statik grup üyeliğini dinamik olarak değiştirme-Azure AD | Microsoft Docs
description: Grupları otomatik olarak doldurmak için üyelik kuralları ve bir kural başvurusu oluşturma.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34451fcc4597f77464e5e9566613e21e9fecdbc6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027308"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Azure Active Directory statik grup üyeliğini dinamik olarak değiştirme

Azure Active Directory (Azure AD) Içinde, bir grubun üyeliğini statik (veya tam tersi) olarak değiştirebilirsiniz. Azure AD, sistemde aynı grup adını ve KIMLIĞINI tutar, böylece gruba yapılan tüm başvurular hala geçerlidir. Bunun yerine yeni bir grup oluşturursanız, bu başvuruları güncelleştirmeniz gerekir. Dinamik grup üyeliği, Kullanıcı ekleme ve kaldırma yönetim yükünü ortadan kaldırır. Bu makalede, Azure AD Yönetim Merkezi veya PowerShell cmdlet 'lerini kullanarak var olan grupları statik veya dinamik üyeliğe nasıl dönüştürebileceğiniz açıklanır.

> [!WARNING]
> Mevcut bir statik grubu dinamik bir grup olarak değiştirirken, var olan tüm üyeler gruptan kaldırılır ve sonra üyelik kuralı yeni üye eklemek üzere işlenir. Grup, uygulama veya kaynaklara erişimi denetlemek için kullanılıyorsa, üyelik kuralı tam olarak işlenene kadar özgün üyelerin erişimi kaybedebileceğini unutmayın.
>
> Gruptaki yeni üyeliğin beklendiğinden emin olmak için yeni üyelik kuralını önceden sınamanızı öneririz.

## <a name="change-the-membership-type-for-a-group"></a>Grubun üyelik türünü değiştirme

1. [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) , kiracınızda genel yönetici veya Kullanıcı Yöneticisi olan bir hesapla oturum açın.
2. **Grupları**seçin.
3. **Tüm gruplar** listesinden, değiştirmek istediğiniz grubu açın.
4. **Özellikler**’i seçin.
5. Grubun **Özellikler** sayfasında, istediğiniz üyelik türüne bağlı olarak atanan (statik), dinamik Kullanıcı veya dinamik cihazın **üyelik türünü** seçin. Dinamik üyelik için kural Oluşturucu 'yu kullanarak basit bir kural için seçenekleri seçebilir veya bir üyelik kuralı yazabilirsiniz. 

Aşağıdaki adımlar, bir grup Kullanıcı grubu için statik ve dinamik üyeliğe değiştirme örneğidir.

1. Seçtiğiniz grubun **Özellikler** sayfasında, **dinamik kullanıcının** **üyelik türünü** seçin ve ardından devam etmek için grup üyeliğinde yapılan değişiklikleri açıklayan iletişim kutusunda Evet ' i seçin. 
  
   ![Dinamik kullanıcının üyelik türünü seçin](./media/groups-change-type/select-group-to-convert.png)
  
2. **Dinamik sorgu Ekle**' yi seçin ve kuralı sağlayın.
  
   ![dinamik grup için kural girin](./media/groups-change-type/enter-rule.png)
  
3. Kuralı oluşturduktan sonra sayfanın alt kısmındaki **Sorgu Ekle** ' yi seçin.
4. Değişikliklerinizi kaydetmek için grubun **Özellikler** sayfasında **Kaydet** ' i seçin. Grubun **üyelik türü** , Grup listesinde hemen güncelleştirilir.

> [!TIP]
> Girdiğiniz üyelik kuralı yanlış ise grup dönüştürme başarısız olabilir. Portalın sağ üst köşesinde, kuralın neden sistem tarafından kabul edilmediğini belirten bir açıklama içeren bir bildirim görüntülenir. Kuralı geçerli hale getirmek üzere nasıl ayarlayabileceğinizi anlamak için dikkatle okuyun. Kural sözdizimi örnekleri ve bir üyelik kuralı için desteklenen özelliklerin, işleçlerin ve değerlerin tamamen listesi için, bkz. [Azure Active Directory gruplar Için dinamik üyelik kuralları](groups-dynamic-membership.md).

## <a name="change-membership-type-for-a-group-powershell"></a>Grubun üyelik türünü değiştirme (PowerShell)

> [!NOTE]
> Dinamik Grup özelliklerini değiştirmek için, [Azure AD PowerShell sürüm 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)' **nin önizleme sürümündeki** cmdlet 'leri kullanmanız gerekir. Önizlemeyi [PowerShell Galerisi](https://www.powershellgallery.com/packages/AzureADPreview)yükleyebilirsiniz.

Mevcut bir grupta üyelik yönetimini değiştirme işlevlerine bir örnek aşağıda verilmiştir. Bu örnekte, GroupTypes özelliğini doğru şekilde işlemek ve dinamik üyelikle ilgisi olmayan değerleri korumak için dikkatli olunmalıdır.

```powershell
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Bir grubu statik hale getirmek için:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Bir grubu dinamik hale getirmek için:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makaleler Azure Active Directory gruplar hakkında ek bilgiler sağlar.

* [Var olan grupları görme](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Yeni grup oluşturma ve üye ekleme](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Bir grubun ayarlarını yönetme](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Bir grubun üyeliklerini yönetme](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Bir gruptaki kullanıcılar için dinamik kuralları yönetme](groups-dynamic-membership.md)
