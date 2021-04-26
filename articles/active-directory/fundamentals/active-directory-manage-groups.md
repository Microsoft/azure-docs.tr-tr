---
title: Grupları kullanarak uygulama & kaynak erişimini yönetme-Azure AD
description: Kuruluşunuzun bulut tabanlı uygulamalarına, şirket içi uygulamalara ve Azure Active Directory grupları kullanarak kaynaklara erişimi yönetme hakkında bilgi edinin.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: ajburnle
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 735f838ad4532b140eddcb4ce1ced24fba9a81be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369123"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Azure Active Directory gruplarını kullanarak uygulama ve kaynak erişimini yönetme
Azure Active Directory (Azure AD), bulut tabanlı uygulamalarınıza, şirket içi uygulamalarınıza ve kaynaklarınıza erişimi yönetmek için grupları kullanmanıza olanak sağlar. Kaynaklarınız, Azure AD 'deki roller aracılığıyla nesneleri yönetme izinleri ya da hizmet olarak yazılım (SaaS) uygulamaları, Azure Hizmetleri, SharePoint siteleri ve şirket içi kaynaklar gibi kuruluş dışında, Azure AD kuruluşunun bir parçası olabilir.

>[!NOTE]
> Azure portal, üyelik ve Grup ayrıntılarını portalda yönetemeyeceğinizdeki bazı grupları görebilirsiniz:
>
> - Şirket içi Active Directory eşitlenen gruplar, yalnızca şirket içi Active Directory yönetilebilir.
> - Dağıtım listeleri ve posta etkin güvenlik grupları gibi diğer Grup türleri yalnızca Exchange Yönetim Merkezi veya Microsoft 365 Yönetim Merkezi 'nde yönetilir. Bu grupları yönetmek için Exchange Yönetim Merkezi veya Microsoft 365 Yönetim Merkezi ' nde oturum açmanız gerekir.

## <a name="how-access-management-in-azure-ad-works"></a>Azure AD 'de erişim yönetimi nasıl işe yarar?

Azure AD, tek bir kullanıcıya veya tüm Azure AD grubuna erişim hakları sağlayarak kuruluşunuzun kaynaklarına erişim sağlamanıza yardımcı olur. Grupların kullanılması kaynak sahibinin (veya Azure AD dizin sahibinin) hakları tek tek sağlamak zorunda kalmadan grubun tüm üyelerine erişim izinleri atayabilmesini sağlar. Kaynak veya dizin sahibi Ayrıca, üye listesi için bir Departman Yöneticisi veya Yardım Masası Yöneticisi gibi başka birine de yönetim hakları verebilir, bu da kişinin üye eklemesini ve kaldırmasına izin verir. Grup sahiplerini yönetme hakkında daha fazla bilgi için bkz. [Grup sahiplerini yönetme](active-directory-accessmanagement-managing-group-owners.md)

![Azure Active Directory erişim yönetimi diyagramı](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Erişim hakları atama yolları

Kullanıcılarınıza kaynak erişim hakları atamak için dört yol vardır:

- **Doğrudan atama.** Kaynak sahibi kullanıcıyı doğrudan kaynağa atar.

- **Grup ataması.** Kaynak sahibi, kaynağa bir Azure AD grubu atar ve bu, tüm grup üyelerine kaynak erişimini otomatik olarak verir. Grup üyeliği hem Grup sahibi hem de kaynak sahibi tarafından yönetilir ve bu, sahip tarafından gruba üye ekleme veya kaldırma izni verir. Grup üyeliğini ekleme veya kaldırma hakkında daha fazla bilgi için, bkz. [nasıl yapılır: başka bir gruptan grup ekleme veya kaldırma Azure Active Directory portalını kullanma](active-directory-groups-membership-azure-portal.md). 

- **Kural tabanlı atama.** Kaynak sahibi bir grup oluşturur ve belirli bir kaynağa hangi kullanıcıların atandığını tanımlamak için bir kural kullanır. Kural, bireysel kullanıcılara atanmış öznitelikleri temel alır. Kaynak sahibi kuralı yönetir, kaynağa erişim izni vermek için hangi özniteliklerin ve değerlerin gerekli olduğunu belirler. Daha fazla bilgi için bkz. [dinamik grup oluşturma ve durumu denetleme](../enterprise-users/groups-create-rule.md).

    Ayrıca, dinamik grupları oluşturma ve kullanma hakkında hızlı bir açıklama için bu kısa videoyu Izleyebilirsiniz:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Dış yetkili ataması.** Erişim, şirket içi dizin veya SaaS uygulaması gibi bir dış kaynaktan gelir. Bu durumda, kaynak sahibi kaynağa erişim sağlamak için bir grup atar ve ardından dış kaynak grup üyelerini yönetir.

   ![Erişim yönetimine genel bakış diyagramı](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Kullanıcılara atamadan gruplara katılabilirler mi?
Grup sahibi kullanıcıların, kendilerine atamak yerine kendi gruplarını bulmasına izin verebilir. Sahibi ayrıca grubu, onay gerektiren tüm kullanıcıları otomatik olarak kabul edecek şekilde ayarlayabilir.

Bir Kullanıcı bir gruba katılmayı istediğinde, istek grup sahibine iletilir. Gerekirse, sahibi isteği onaylayabilir ve kullanıcıya grup üyeliği bildirilir. Ancak, birden çok sahipleriniz varsa ve bunlardan biri onaylarsa, Kullanıcı bilgilendirilir ancak gruba eklenmez. Kullanıcılarınızın gruplara katılmasına izin isteme hakkında daha fazla bilgi ve yönergeler için bkz. [Azure AD ayarlama, kullanıcıların gruplara katılması isteyebilmeleri](../enterprise-users/groups-self-service-management.md) için

## <a name="next-steps"></a>Sonraki adımlar
Artık grupları kullanarak yönetime erişim için bir giriş bitolduğunuza göre, kaynak ve uygulamalarınızı yönetmeye başlayabilirsiniz.

- [Azure Active Directory kullanarak yeni bir grup oluşturma](active-directory-groups-create-azure-portal.md) veya [PowerShell cmdlet 'lerini kullanarak yeni bir grup oluşturma ve yönetme](../enterprise-users/groups-settings-v2-cmdlets.md)

- [Tümleşik bir SaaS uygulamasına erişim atamak için grupları kullanma](../enterprise-users/groups-saasapps.md)

- [Azure AD Connect kullanarak bir şirket içi grubu Azure ile eşitleme](../hybrid/whatis-hybrid-identity.md)
