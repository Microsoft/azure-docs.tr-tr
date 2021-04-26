---
title: Azure Active Directory yönetilen kimliklerin güvenliğini sağlama
description: Yönetilen kimliklerin güvenliğini bulma, değerlendirme ve artırmanın açıklaması.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b08231f254ea47fc3c9d65de42966301bd3378f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640048"
---
# <a name="securing-managed-identities"></a>Yönetilen kimliklerin güvenliğini sağlama

Geliştiriciler genellikle farklı hizmetler arasındaki iletişimin güvenliğini sağlamak için kullanılan gizli dizi ve kimlik bilgilerinin yönetimine göre yapılır. Yönetilen kimlikler, Azure kaynakları için kimlikler sağlamak üzere oluşturulan güvenli Azure Active Directory (Azure AD) kimlikleridir.

## <a name="benefits-of-using-managed-identities-for-azure-resources"></a>Azure kaynakları için Yönetilen kimlikler kullanmanın avantajları

Yönetilen kimlikleri kullanmanın avantajları aşağıda verilmiştir:

* Kimlik bilgilerini yönetmeniz gerekmez. Yönetilen kimlikler ile, kimlik bilgileri tamamen yönetilir, döndürülür ve Azure tarafından korunur. Kimlikler otomatik olarak sağlanır ve Azure kaynaklarıyla silinir. Yönetilen kimlikler, Azure kaynaklarının Azure AD kimlik doğrulamasını destekleyen tüm hizmetlerle iletişim kurmasını sağlar.

* Hiç kimse (herhangi bir genel yönetici dahil) kimlik bilgilerine erişime sahip olmadığından, örneğin, koda dahil edilmeleri gibi, yanlışlıkla sızamazlar.

## <a name="when-to-use-managed-identities"></a>Yönetilen kimlikler ne zaman kullanılır?

Yönetilen kimlikler, Azure AD kimlik doğrulamasını destekleyen hizmetler arasındaki iletişimler için en iyi şekilde kullanılır. 

Kaynak sistem bir hedef hizmete erişim istiyor. Herhangi bir Azure kaynağı bir kaynak sistem olabilir. Örneğin, bir Azure VM, Azure Işlev örneği ve Azure App Services örnekleri yönetilen kimlikleri destekler.

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

### <a name="how-authentication-and-authorization-work"></a>Kimlik doğrulama ve yetkilendirme çalışması

Yönetilen kimlikler sayesinde kaynak sistem, kaynak sahibinin kimlik bilgilerini yönetmesi gerekmeden Azure AD 'den bir belirteç alabilir. Azure kimlik bilgilerini yönetir. Kaynak sistem tarafından edinilen belirteç, kimlik doğrulaması için hedef sisteme sunulur. 

Hedef sistemin erişim izni vermeden önce kaynak sistemi kimlik doğrulaması (tanımlaması) ve yetkilendirilmesi gerekir. Hedef hizmet Azure AD tabanlı kimlik doğrulamasını desteklediğinde, Azure AD tarafından verilen bir erişim belirtecini kabul eder. 

Azure 'da bir denetim düzlemi ve bir veri düzlemi vardır. Denetim düzlemine kaynak oluşturursunuz ve bu verilere erişmeniz gerekir. Örneğin, denetim düzleminde bir Cosmos veritabanı oluşturursunuz, ancak bunu veri düzlemine sorgulayın.

Hedef sistem, kimlik doğrulaması için belirteci kabul ettikten sonra Denetim düzlemi ve veri düzlemi için yetkilendirme için farklı mekanizmaları destekleyebilir.

Azure 'un tüm denetim düzlemi işlemleri [Azure Resource Manager](../../azure-resource-manager/management/overview.md) tarafından yönetilir ve [azure rol tabanlı Access Control](../../role-based-access-control/overview.md)kullanılır. Veri düzleinde, her hedef sistemin kendi yetkilendirme mekanizması vardır. Azure depolama, veri düzleminde Azure RBAC 'yi destekler. Örneğin, Azure App Services kullanan uygulamalar Azure depolama 'dan verileri okuyabilir ve Azure Kubernetes hizmetini kullanan uygulamalar Azure Key Vault depolanan gizli dizileri okuyabilir.

Denetim ve veri düzlemleri hakkında daha fazla bilgi için bkz. [Denetim düzlemi ve veri düzlemi işlemleri-Azure Resource Manager](../../azure-resource-manager/management/control-plane-and-data-plane.md).

Tüm Azure hizmetleri sonunda yönetilen kimlikleri destekleyecektir. Daha fazla bilgi için bkz. [Azure kaynakları için yönetilen kimlikleri destekleyen hizmetler](../managed-identities-azure-resources/services-support-managed-identities.md).

##  

## <a name="types-of-managed-identities"></a>Yönetilen kimlik türleri

İki tür yönetilen kimlik (sistem tarafından atanan ve Kullanıcı tarafından atanan) vardır.

Sistem tarafından atanan yönetilen kimlik aşağıdaki özelliklere sahiptir:

* Azure kaynağıyla 1:1 ilişkisi vardır. Örneğin, her VM ile ilişkili benzersiz bir yönetilen kimlik vardır.

* Bunlar Azure kaynaklarının yaşam döngüsüne bağlıdır. Kaynak silindiğinde, onunla ilişkili yönetilen kimlik otomatik olarak silinir ve yalnız bırakılmış hesaplarla ilişkili riski ortadan kaldırır. 

Kullanıcı tarafından atanan Yönetilen kimlikler aşağıdaki özelliklere sahiptir:

* Bu kimliklerin yaşam döngüsü bir Azure kaynağının bağımsızdır ve yaşam döngüsünü yönetmeniz gerekir. Azure kaynağı silindiğinde, atanan kullanıcı tarafından atanan yönetilen kimlik sizin için otomatik olarak silinmez.

* Tek bir kullanıcı tarafından atanan yönetilen kimlik, sıfır veya daha fazla Azure kaynağına atanabilir.

* Bunlar, önceden oluşturulup bir kaynağa atanabilir.

## <a name="find-managed-identity-service-principals-in-azure-ad"></a>Azure AD 'de yönetilen kimlik hizmeti sorumlularını bulma

Yönetilen kimlikleri bulmak için çeşitli yollar vardır:

* Azure portal kurumsal uygulamalar sayfasını kullanma

* Microsoft Graph kullanma

### <a name="using-the-azure-portal"></a>Azure portalını kullanma

1. Azure Active Directory Kurumsal uygulamalar ' ı seçin.

2. "Yönetilen kimlikler" için filtreyi seçin 

   ![Uygulama türü açılan kutusu vurgulaması "Yönetilen kimlikler" içeren tüm uygulamalar ekranının görüntüsü.](./media/securing-service-accounts/service-accounts-managed-identities.png)

 

### <a name="using-microsoft-graph"></a>Microsoft Graph kullanma

Microsoft Graph için aşağıdaki GET isteğiyle kiracınızdaki tüm yönetilen kimliklerin bir listesini alabilirsiniz:

`https://graph.microsoft.com/v1.0/servicePrincipals?$filter=(servicePrincipalType eq 'ManagedIdentity') `

Bu istekleri filtreleyebilirsiniz. Daha fazla bilgi edinmek için bkz. [hizmet sorumlusu](/graph/api/serviceprincipal-get)için grafik belgeleri.

## <a name="assess-the-security-of-managed-identities"></a>Yönetilen kimliklerin güvenliğini değerlendirin 

Yönetilen kimliklerin güvenliğini aşağıdaki yollarla değerlendirebilirsiniz:

* Ayrıcalıkları inceleyin ve en az ayrıcalıklı modelin seçili olduğundan emin olun. Yönetilen kimliklerinize atanan izinleri almak için aşağıdaki PowerShell cmdlet 'ini kullanın.

   ` Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

 
* Yönetilen kimliğin bir Yöneticiler grubu gibi herhangi bir ayrıcalıklı grubun parçası olmadığından emin olun.  
Bunu, son derece ayrıcalıklı gruplarınızın üyelerini PowerShell ile numaralandırarak yapabilirsiniz.

   `Get-AzureADGroupMember -ObjectId <String> [-All <Boolean>] [-Top <Int32>] [<CommonParameters>]`

* [Yönetilen kimliğin hangi kaynaklara eriştiğini öğrendiğinizden emin olun](../../role-based-access-control/role-assignments-list-powershell.md).

## <a name="move-to-managed-identities"></a>Yönetilen kimliklere taşı

Hizmet sorumlusu veya bir Azure AD Kullanıcı hesabı kullanıyorsanız, kimlik bilgilerini koruma, döndürme ve yönetme ihtiyacını ortadan kaldırmak için bunun yerine yönetilen bir kimlik kullanıp kullanbileceğinizi değerlendirin. 

## <a name="next-steps"></a>Sonraki adımlar

**Yönetilen kimlikler oluşturma hakkında bilgi için bkz.** 

[Kullanıcı tarafından atanan yönetilen kimlik oluşturun](../managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

[Kaynak oluşturma sırasında bir sistem tarafından atanmış yönetilen kimliği etkinleştirme](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

[Mevcut bir kaynak üzerinde sistem tarafından atanmış yönetilen kimliği etkinleştir](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Hizmet hesapları hakkında daha fazla bilgi için bkz.**

[Azure Active Directory hizmet hesaplarına giriş](service-accounts-introduction-azure.md)

[Hizmet sorumlularının güvenliğini sağlama](service-accounts-principal.md)

[Azure hizmet hesaplarını yöneten](service-accounts-governing-azure.md)

[Şirket içi hizmet hesaplarına giriş](service-accounts-on-premises.md)

 

 

