---
title: Azure Active Directory | içindeki özel roller için uygulama onay izinleri | Microsoft Docs
description: Azure portal, PowerShell veya Graph API özel Azure AD rolleri için uygulama onayı izinlerini önizleyin.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: psignoret
ms.custom: it-pro
ms.openlocfilehash: f9c2c15bbfcf9a9271e629ef26c11ecc4cbaaa6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98740117"
---
# <a name="app-consent-permissions-for-custom-roles-in-azure-active-directory"></a>Azure Active Directory özel roller için uygulama izni izinleri

Bu makale, Azure Active Directory (Azure AD) içindeki özel rol tanımları için şu anda kullanılabilir olan uygulama onay izinlerini içerir. Bu makalede, uygulama onayı ve izinleriyle ilgili bazı yaygın senaryolar için gereken izinleri bulacaksınız.

## <a name="required-license-plan"></a>Gerekli lisans planı

Bu özelliğin kullanılması için Azure AD kuruluşunuz için Azure AD Premium P1 lisansı gerekir. Gereksinimlerinize uygun lisansı bulmak için bkz. [Ücretsiz, Temel ve Premium sürümlerinin genel olarak sağlanan özelliklerini karşılaştırma](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="app-consent-permissions"></a>Uygulama onay izinleri

Uygulama onay ilkelerini yönetmek için bu makalede listelenen izinleri ve uygulamalara izin verme iznini kullanın.

> [!NOTE]
> Azure AD Yönetici portalı, bu makalede listelenen izinlerin özel bir dizin rolü tanımına eklenmesini henüz desteklememektedir. Bu makalede listelenen izinlerle [özel bir dizin rolü oluşturmak Için Azure AD PowerShell kullanmanız](custom-create.md#create-a-role-using-powershell) gerekir.

### <a name="granting-delegated-permissions-to-apps-on-behalf-of-self-user-consent"></a>Kendi kendine adına uygulamalara temsilci izinleri verme (Kullanıcı onayı)

Kullanıcıların, uygulamalar adına (Kullanıcı izni), uygulama onay ilkesine bağlı olarak izin vermesini sağlamak için.

- Microsoft. Directory/Servicesorumlularını/managePermissionGrantsForSelf. numarasını

Burada `{id}` , bu iznin etkin olması için karşılanması gereken koşulları ayarlayacağı bir [uygulama onay ilkesinin](../manage-apps/manage-app-consent-policies.md) kimliği ile değiştirilmiştir.

Örneğin, kullanıcıların kendi adına izin vermesini sağlamak için, KIMLIĞI olan yerleşik uygulama onay ilkesine bağlı olarak `microsoft-user-default-low` , iznini kullanırsınız `...managePermissionGrantsForSelf.microsoft-user-default-low` .

### <a name="granting-permissions-to-apps-on-behalf-of-all-admin-consent"></a>Tüm uygulamalar adına uygulama izinleri verme (yönetici onayı)

Uygulamalara kiracı genelinde yönetici onayı atamak için, hem temsilci izinleri hem de uygulama izinleri (uygulama rolleri) için:

- Microsoft. Directory/Servicesorumlularını/managePermissionGrantsForAll. numarasını

Burada `{id}` , bu iznin kullanılabilir olması için karşılanması gereken koşulları ayarlayacağı bir [uygulama onay ilkesinin](../manage-apps/manage-app-consent-policies.md) kimliği ile değiştirilmiştir.

Örneğin, rol atanışları, uygulamalara kiracı genelinde yönetici onayı vererek, KIMLIK ile özel bir [uygulama onay ilkesine](../manage-apps/manage-app-consent-policies.md) bağlı olarak izin vermek için `low-risk-any-app` , iznini kullanırsınız `microsoft.directory/servicePrincipals/managePermissionGrantsForAll.low-risk-any-app` .

### <a name="managing-app-consent-policies"></a>Uygulama onay ilkelerini yönetme

[Uygulama onay ilkelerinin](../manage-apps/manage-app-consent-policies.md)oluşturulmasını, güncelleştirilmesini ve silinmesini devretmek.

- Microsoft. Directory/permissionGrantPolicies/oluşturma
- Microsoft. Directory/permissionGrantPolicies/standart/okuma
- Microsoft. Directory/permissionGrantPolicies/Basic/Update
- Microsoft. Directory/permissionGrantPolicies/Delete

## <a name="full-list-of-permissions"></a>İzinlerin tam listesi

İzin | Description
---------- | -----------
Microsoft. Directory/Servicesorumlularını/managePermissionGrantsForSelf. numarasını | Uygulama onay ilkesine tabi olmak üzere kendi kendine (Kullanıcı izni) adına uygulamalar için izin verme olanağı verir `{id}` .
Microsoft. Directory/Servicesorumlularını/managePermissionGrantsForAll. numarasını | Uygulama onay ilkesine bağlı olarak, her biri adına (kiracı genelinde yönetici onayı) uygulamalara izin vermek için izin verir `{id}` .
Microsoft. Directory/permissionGrantPolicies/standart/okuma | Uygulama onay ilkelerini okuma olanağı verir.
Microsoft. Directory/permissionGrantPolicies/Basic/Update | Mevcut uygulama izni ilkelerindeki temel özellikleri güncelleştirme olanağı verir.
Microsoft. Directory/permissionGrantPolicies/oluşturma | Uygulama onay ilkeleri oluşturma olanağı verir.
Microsoft. Directory/permissionGrantPolicies/Delete | Uygulama onay ilkelerini silme olanağı verir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal, Azure AD PowerShell ve Graph API kullanarak özel roller oluşturun](custom-create.md)
- [Özel bir rol için atamaları görüntüleme](../roles/view-assignments.md)
