---
title: Azure 'da Role-Based Access Control kullanma API Management | Microsoft Docs
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ef71591d6d5a26aa737db4e7cb547c8b2c39d92a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812183"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Azure API Management'te Rol Tabanlı Erişim Denetimini kullanma

Azure API Management, API Management Hizmetleri ve varlıkları (örneğin, API 'Ler ve ilkeler) için ayrıntılı erişim yönetimini sağlamak üzere Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanır. Bu makale, API Management yerleşik ve özel rollere genel bir bakış sunar. Azure portal erişim yönetimi hakkında daha fazla bilgi için, bkz. [Azure Portal erişim yönetimiyle çalışmaya başlama](../role-based-access-control/overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Yerleşik roller

API Management Şu anda üç yerleşik rol sağlar ve yakın gelecekte iki rol daha ekleyebilirler. Bu roller abonelik, kaynak grubu ve bireysel API Management örneği gibi farklı kapsamlara atanabilir. Örneğin, "API Management hizmet okuyucusu" rolünü kaynak grubu düzeyindeki bir kullanıcıya atarsanız, kullanıcının kaynak grubundaki tüm API Management örneklerine okuma erişimi vardır. 

Aşağıdaki tabloda yerleşik rollerin kısa açıklamaları verilmiştir. Azure portal veya Azure [PowerShell](../role-based-access-control/role-assignments-powershell.md), [azure CLI](../role-based-access-control/role-assignments-cli.md)ve [REST API](../role-based-access-control/role-assignments-rest.md)gibi diğer araçları kullanarak bu rolleri atayabilirsiniz. Yerleşik roller atama hakkında ayrıntılar için bkz. Azure [abonelik kaynaklarınıza erişimi yönetmek Için Azure rolleri atama](../role-based-access-control/role-assignments-portal.md).

| Rol          | Okuma erişimi<sup>[1]</sup> | Yazma erişimi<sup>[2]</sup> | Hizmet oluşturma, silme, ölçeklendirme, VPN ve özel etki alanı yapılandırması | Eski yayımcı portalına erişim | Description
| ------------- | ---- | ---- | ---- | ---- | ---- 
| API Management hizmet Katılımcısı | ✓ | ✓ | ✓ | ✓ | Süper Kullanıcı. , API Management Hizmetleri ve varlıklara (örneğin, API 'Ler ve ilkeler) tam CRUD erişimi vardır. , Eski yayımcı portalına erişebilir. |
| API Management hizmeti okuyucu | ✓ | | || API Management hizmetlere ve varlıklara salt okuma erişimi vardır. |
| API Management Service Işleci | ✓ | | ✓ | | API Management hizmetlerini yönetebilir, ancak varlıkları yönetemez.|
| API Management hizmeti Düzenleyicisi<sup>*</sup> | ✓ | ✓ | |  | API Management varlıklarını yönetebilir, ancak hizmetleri yönetemez.|
| API Management Içerik Yöneticisi<sup>*</sup> | ✓ | | | ✓ | Geliştirici portalını yönetebilir. Hizmetlere ve varlıklara salt okuma erişimi.|

<sup>[1] API Management Hizmetleri ve varlıklarına yönelik okuma erişimi (örneğin, API 'Ler ve ilkeler).</sup>

<sup>[2] aşağıdaki işlemler hariç API Management Hizmetleri ve varlıklara yazma erişimi: örnek oluşturma, silme ve ölçekleme; VPN yapılandırması; ve özel etki alanı kurulumu.</sup>

<sup>\* Mevcut yayımcı portalından tüm yönetici kullanıcı arabirimini Azure portal geçirdikten sonra hizmet Düzenleyicisi rolü kullanılabilir olacaktır. Içerik Yöneticisi rolü, yayımcı portalının yalnızca geliştirici portalını yönetme ile ilgili işlevselliği içermesi için yeniden düzenlenmiş olduktan sonra kullanılabilir olacaktır.</sup>  

## <a name="custom-roles"></a>Özel roller

Yerleşik rollerin hiçbiri özel ihtiyaçlarınızı karşılamıyorsa, API Management varlıkları için daha ayrıntılı erişim yönetimi sağlamak üzere özel roller oluşturulabilir. Örneğin, bir API Management hizmetine salt okuma erişimi olan, ancak yalnızca belirli bir API 'ye yazma erişimi olan özel bir rol oluşturabilirsiniz. Özel roller hakkında daha fazla bilgi edinmek için bkz. [Azure RBAC 'de özel roller](../role-based-access-control/custom-roles.md). 

> [!NOTE]
> Azure portal bir API Management örneğini görebilmek için, bir özel rol ```Microsoft.ApiManagement/service/read``` eylemi içermelidir.

Özel bir rol oluşturduğunuzda, yerleşik rollerden biriyle başlamak daha kolay olur. **Eylemler**, **NotActions** veya **astifblescopes** eklemek için öznitelikleri düzenleyin ve ardından değişiklikleri yeni bir rol olarak kaydedin. Aşağıdaki örnek, "API Management hizmet okuyucu" rolüyle başlar ve "Hesaplayıcı API Düzenleyicisi" adlı özel bir rol oluşturur. Özel rolü belirli bir API 'ye atayabilirsiniz. Sonuç olarak, bu rolün yalnızca bu API 'ye erişimi vardır. 

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

Azure 'da Role-Based Access Control hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:
  * [Azure portalında erişim yönetimi ile çalışmaya başlama](../role-based-access-control/overview.md)
  * [Azure abonelik kaynaklarınıza erişimi yönetmek için Azure rolleri atama](../role-based-access-control/role-assignments-portal.md)
  * [Azure RBAC 'de özel roller](../role-based-access-control/custom-roles.md)
  * [Azure Resource Manager kaynak sağlayıcısı işlemleri](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
