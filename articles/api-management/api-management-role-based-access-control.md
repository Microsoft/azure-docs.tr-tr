---
title: Azure API Management rol tabanlı Access Control kullanma | Microsoft Docs
description: Yerleşik rolleri kullanmayı ve Azure API Management özel roller oluşturmayı öğrenin
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: e33d981429f0e79accbe47ea0edea5f3c7a2157b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072204"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Azure 'da rol tabanlı Access Control kullanma API Management

Azure API Management, API Management Hizmetleri ve varlıkları için ayrıntılı erişim yönetimini (örneğin, API 'Ler ve ilkeler) etkinleştirmek üzere Azure rol tabanlı Access Control (RBAC) kullanır. Bu makale, API Management yerleşik ve özel rollere genel bir bakış sunar. Azure portal erişim yönetimi hakkında daha fazla bilgi için, bkz. [Azure Portal erişim yönetimiyle çalışmaya başlama](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Yerleşik roller

API Management Şu anda üç yerleşik rol sağlar ve yakın gelecekte iki rol daha ekleyebilirler. Bu roller abonelik, kaynak grubu ve bireysel API Management örneği gibi farklı kapsamlara atanabilir. Örneğin, "Azure API Management hizmet okuyucusu" rolünü kaynak grubu düzeyindeki bir kullanıcıya atarsanız, kullanıcının kaynak grubundaki tüm API Management örneklerine okuma erişimi vardır. 

Aşağıdaki tabloda yerleşik rollerin kısa açıklamaları verilmiştir. Azure portal veya Azure [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)ve [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)gibi diğer araçları kullanarak bu rolleri atayabilirsiniz. Yerleşik roller atama hakkında daha fazla bilgi için bkz. [Azure abonelik kaynaklarınıza erişimi yönetmek için rol atamalarını kullanma](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

| Role          | Okuma erişimi<sup>[1]</sup> | Yazma erişimi<sup>[2]</sup> | Hizmet oluşturma, silme, ölçeklendirme, VPN ve özel etki alanı yapılandırması | Eski yayımcı portalına erişim | Açıklama
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Azure API Management hizmeti Katılımcısı | ✓ | ✓ | ✓ | ✓ | Süper Kullanıcı. , API Management Hizmetleri ve varlıklara (örneğin, API 'Ler ve ilkeler) tam CRUD erişimi vardır. , Eski yayımcı portalına erişebilir. |
| Azure API Management hizmet okuyucusu | ✓ | | || API Management hizmetlere ve varlıklara salt okuma erişimi vardır. |
| Azure API Management Service Işleci | ✓ | | ✓ | | API Management hizmetlerini yönetebilir, ancak varlıkları yönetemez.|
| Azure API Management hizmeti Düzenleyicisi<sup>*</sup> | ✓ | ✓ | |  | API Management varlıklarını yönetebilir, ancak hizmetleri yönetemez.|
| Azure API Management Içerik Yöneticisi<sup>*</sup> | ✓ | | | ✓ | Geliştirici portalını yönetebilir. Hizmetlere ve varlıklara salt okuma erişimi.|

<sup>[1] API Management Hizmetleri ve varlıklarına yönelik okuma erişimi (örneğin, API 'Ler ve ilkeler).</sup>

<sup>[2] aşağıdaki işlemler hariç API Management Hizmetleri ve varlıklara yazma erişimi: örnek oluşturma, silme ve ölçekleme; VPN yapılandırması; ve özel etki alanı kurulumu.</sup>

<sup>\*Mevcut yayımcı portalından tüm yönetici kullanıcı arabirimini Azure portal geçirdikten sonra hizmet Düzenleyicisi rolü kullanılabilir olacaktır. Içerik Yöneticisi rolü, yayımcı portalının yalnızca geliştirici portalını yönetme ile ilgili işlevselliği içermesi için yeniden düzenlenmiş olduktan sonra kullanılabilir olacaktır.</sup>  

## <a name="custom-roles"></a>Özel roller

Yerleşik rollerin hiçbiri özel ihtiyaçlarınızı karşılamıyorsa, API Management varlıkları için daha ayrıntılı erişim yönetimi sağlamak üzere özel roller oluşturulabilir. Örneğin, bir API Management hizmetine salt okuma erişimi olan, ancak yalnızca belirli bir API 'ye yazma erişimi olan özel bir rol oluşturabilirsiniz. Özel roller hakkında daha fazla bilgi edinmek için bkz. [Azure RBAC 'de özel roller](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

> [!NOTE]
> Azure Portal bir API Management örneğini görebilmek için, bir özel rol ```Microsoft.ApiManagement/service/read``` eylemi içermelidir.

Özel bir rol oluşturduğunuzda, yerleşik rollerden biriyle başlamak daha kolay olur. **Eylemler**, **NotActions**veya **astifblescopes**eklemek için öznitelikleri düzenleyin ve ardından değişiklikleri yeni bir rol olarak kaydedin. Aşağıdaki örnek, "Azure API Management hizmet okuyucu" rolüyle başlar ve "Hesaplayıcı API Düzenleyicisi" adlı özel bir rol oluşturur. Özel rolü belirli bir API 'ye atayabilirsiniz. Sonuç olarak, bu rolün yalnızca bu API 'ye erişimi vardır. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

[Azure Resource Manager kaynak sağlayıcısı işlemler](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) makalesi, API Management düzeyinde verilebilirler izin listesini içerir.

## <a name="video"></a>Video


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Sonraki adımlar

Azure 'da rol tabanlı Access Control hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
  * [Azure portalında erişim yönetimi ile çalışmaya başlama](../role-based-access-control/overview.md)
  * [Azure abonelik kaynaklarınıza erişimi yönetmek için rol atamalarını kullanın](../role-based-access-control/role-assignments-portal.md)
  * [Azure RBAC 'de özel roller](../role-based-access-control/custom-roles.md)
  * [Azure Resource Manager kaynak sağlayıcısı işlemleri](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)