---
title: Azure AD Domain Services eşleşmeyen Dizin hatalarını giderme | Microsoft Docs
description: Eşleşmeyen bir dizin hatasının ne anlama geldiğini ve Azure AD Domain Services nasıl çözümleneceğini öğrenin
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: ee8174114f1b892210e8ee9173ce0eb1d09c7e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619309"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-active-directory-domain-services-managed-domains"></a>Mevcut Azure Active Directory Domain Services yönetilen etki alanları için eşleşmeyen Dizin hatalarını çözümle

Azure Active Directory Domain Services (Azure AD DS) yönetilen etki alanında eşleşmeyen bir kiracı hatası görünüyorsa, yönetilen etki alanını çözümlenene kadar yönetemezsiniz. Temel alınan Azure sanal ağı farklı bir Azure AD dizinine taşınırsa bu hata oluşur.

Bu makalede hatanın neden oluştuğu ve nasıl çözümleneceği açıklanmaktadır.

## <a name="what-causes-this-error"></a>Bu hataya neden olur?

Azure AD DS yönetilen etki alanı ve sanal ağ iki farklı Azure AD kiracılarına ait olduğunda, eşleşmeyen bir dizin hatası oluşur. Örneğin, contoso Azure AD kiracısında çalışan *aaddscontoso.com* adlı bir yönetilen etki alanınız olabilir. Ancak, yönetilen etki alanı için Azure sanal ağı fabrikam Azure AD kiracısının bir parçasıdır.

Azure rol tabanlı erişim denetimi (Azure RBAC), kaynaklara erişimi sınırlandırmak için kullanılır. Azure AD kiracısında Azure AD DS etkinleştirdiğinizde, kimlik bilgisi karmaları yönetilen etki alanıyla eşitlenir. Bu işlem, Azure AD dizini için bir kiracı yöneticisi olmanızı ve kimlik bilgilerine erişimin denetlenmelidir.

Bir Azure sanal ağına kaynak dağıtmak ve trafiği denetlemek için, yönetilen etki alanını dağıttığınız sanal ağ üzerinde yönetici ayrıcalıklarına sahip olmanız gerekir.

Azure RBAC 'nin, Azure AD DS 'nin kullandığı tüm kaynaklara tutarlı ve güvenli şekilde çalışması için, yönetilen etki alanı ve sanal ağ aynı Azure AD kiracısına ait olmalıdır.

Dağıtımlar için aşağıdaki kurallar geçerlidir:

- Azure AD dizininde birden çok Azure aboneliği olabilir.
- Bir Azure aboneliğinde, sanal ağlar gibi birden fazla kaynak olabilir.
- Azure AD dizini için tek bir yönetilen etki alanı etkinleştirilir.
- Yönetilen bir etki alanı, aynı Azure AD kiracısındaki Azure aboneliklerinden herhangi birine ait olan bir sanal ağ üzerinde etkinleştirilebilir.

### <a name="valid-configuration"></a>Geçerli yapılandırma

Aşağıdaki örnek dağıtım senaryosunda, contoso Managed etki alanı contoso Azure AD kiracısında etkinleştirilir. Yönetilen etki alanı, contoso Azure AD kiracının sahip olduğu bir Azure aboneliğine ait olan bir sanal ağda dağıtılır.

Hem yönetilen etki alanı hem de sanal ağ aynı Azure AD kiracısına aittir. Bu örnek yapılandırma geçerli ve tam olarak desteklenmektedir.

![Aynı Azure AD kiracısının yönetilen etki alanı ve sanal ağ bölümüyle geçerli Azure AD DS kiracı yapılandırması](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Eşleşmeyen kiracı yapılandırması

Bu örnek dağıtım senaryosunda, contoso Managed etki alanı contoso Azure AD kiracısında etkinleştirilir. Ancak, yönetilen etki alanı fabrikam Azure AD kiracının sahip olduğu bir Azure aboneliğine ait olan bir sanal ağda dağıtılır.

Yönetilen etki alanı ve sanal ağ iki farklı Azure AD kiracılarına aittir. Bu örnek yapılandırma eşleşmeyen bir kiracıya sahip ve desteklenmiyor. Sanal ağ, yönetilen etki alanı ile aynı Azure AD kiracısına taşınmalıdır.

![Eşleşmeyen kiracı yapılandırması](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Eşleşmeyen kiracı hatasını çözümle

Aşağıdaki iki seçenek eşleşmeyen Dizin hatasını çözer:

* İlk olarak, [yönetilen etki alanını](delete-aadds.md) mevcut Azure AD dizininden silin. Ardından, kullanmak istediğiniz sanal ağ ile aynı Azure AD dizininde [bir değiştirme yönetilen etki alanı oluşturun](tutorial-create-instance.md) . Hazırlandığınızda, daha önce silinmiş etki alanına katılmış tüm makinelere yeniden oluşturulmuş yönetilen etki alanına katın.
* Sanal ağı içeren [Azure aboneliğini](../cost-management-billing/manage/billing-subscription-transfer.md) , yönetilen etki alanı Ile aynı Azure AD dizinine taşıyın.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD DS sorunlarını giderme hakkında daha fazla bilgi için bkz. [sorun giderme kılavuzu](troubleshoot.md).
