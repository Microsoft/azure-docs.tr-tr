---
title: Azure AD Domain Services eşleşmeyen Dizin hatalarını giderme | Microsoft Docs
description: Eşleşmeyen bir dizin hatasının ne anlama geldiğini ve Azure AD Domain Services nasıl çözümleneceğini öğrenin
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 8b1c3184ada743fddb78e1a3d0ce8d67f1f1a94f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71693340"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>Mevcut Azure AD Domain Services yönetilen etki alanları için eşleşmeyen Dizin hatalarını çözümle

Azure Active Directory Domain Services (Azure AD DS) yönetilen etki alanında eşleşmeyen bir kiracı hatası görünüyorsa, yönetilen etki alanını çözümlenene kadar yönetemezsiniz. Temel alınan Azure sanal ağı farklı bir Azure AD dizinine taşınırsa bu hata oluşur.

Bu makalede hatanın neden oluştuğu ve nasıl çözümleneceği açıklanmaktadır.

## <a name="what-causes-this-error"></a>Bu hataya neden olur?

Azure AD DS yönetilen etki alanı ve sanal ağ iki farklı Azure AD kiracılarına ait olduğunda, eşleşmeyen bir dizin hatası oluşur. Örneğin, contoso Azure AD kiracısında çalışan *contoso.com* adlı bir Azure AD DS yönetilen etki alanınız olabilir. Ancak, yönetilen etki alanı için Azure sanal ağı fabrikam Azure AD kiracısının bir parçasıdır.

Azure, kaynaklarla erişimi sınırlandırmak için rol tabanlı erişim denetimi (RBAC) kullanır. Azure AD kiracısında Azure AD DS etkinleştirdiğinizde, kimlik bilgisi karmaları yönetilen etki alanıyla eşitlenir. Bu işlem, Azure AD dizini için bir kiracı yöneticisi olmanızı ve kimlik bilgilerine erişimin denetlenmelidir. Azure sanal ağına kaynak dağıtmak ve trafiği denetlemek için, Azure AD DS dağıttığınız sanal ağ üzerinde yönetici ayrıcalıklarına sahip olmanız gerekir.

RBAC 'in, Azure AD DS 'nin kullandığı tüm kaynaklara tutarlı ve güvenli şekilde çalışması için, yönetilen etki alanı ve sanal ağ aynı Azure AD kiracısına ait olmalıdır.

Kaynak Yöneticisi ortamında aşağıdaki kurallar geçerlidir:

- Azure AD dizininde birden çok Azure aboneliği olabilir.
- Bir Azure aboneliğinde, sanal ağlar gibi birden fazla kaynak olabilir.
- Azure AD dizini için tek bir Azure AD Domain Services yönetilen etki alanı etkinleştirilir.
- Azure AD Domain Services yönetilen bir etki alanı, aynı Azure AD kiracısındaki Azure aboneliklerinden herhangi birine ait olan bir sanal ağ üzerinde etkinleştirilebilir.

### <a name="valid-configuration"></a>Geçerli yapılandırma

Aşağıdaki örnek dağıtım senaryosunda contoso Azure AD DS yönetilen etki alanı contoso Azure AD kiracısında etkinleştirilmiştir. Yönetilen etki alanı, contoso Azure AD kiracının sahip olduğu bir Azure aboneliğine ait olan bir sanal ağda dağıtılır. Hem yönetilen etki alanı hem de sanal ağ aynı Azure AD kiracısına aittir. Bu örnek yapılandırma geçerli ve tam olarak desteklenmektedir.

![Aynı Azure AD kiracısının yönetilen etki alanı ve sanal ağ bölümüyle geçerli Azure AD DS kiracı yapılandırması](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>Eşleşmeyen kiracı yapılandırması

Bu örnek dağıtım senaryosunda contoso Azure AD DS yönetilen etki alanı, contoso Azure AD kiracısında etkinleştirilmiştir. Ancak, yönetilen etki alanı fabrikam Azure AD kiracının sahip olduğu bir Azure aboneliğine ait olan bir sanal ağda dağıtılır. Yönetilen etki alanı ve sanal ağ iki farklı Azure AD kiracılarına aittir. Bu örnek yapılandırma eşleşmeyen bir kiracıya sahip ve desteklenmiyor. Sanal ağ, yönetilen etki alanı ile aynı Azure AD kiracısına taşınmalıdır.

![Eşleşmeyen kiracı yapılandırması](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>Eşleşmeyen kiracı hatasını çözümle

Aşağıdaki iki seçenek eşleşmeyen Dizin hatasını çözer:

* [Azure AD DS yönetilen etki alanını](delete-aadds.md) mevcut Azure AD dizininden silin. Kullanmak istediğiniz sanal ağ ile aynı Azure AD dizininde [azure AD DS yönetilen bir etki alanı oluşturun](tutorial-create-instance.md) . Hazırlandığınızda, daha önce silinmiş etki alanına katılmış tüm makinelere yeniden oluşturulmuş yönetilen etki alanına katın.
* Sanal ağı içeren [Azure aboneliğini](../billing/billing-subscription-transfer.md) Azure AD DS yönetilen etki alanı Ile aynı Azure AD dizinine taşıyın.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD DS sorunlarını giderme hakkında daha fazla bilgi için bkz. [sorun giderme kılavuzu](troubleshoot.md).
