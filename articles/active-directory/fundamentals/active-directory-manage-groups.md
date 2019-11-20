---
title: Grupları - Azure Active Directory kullanarak uygulama ve kaynak erişimini yönetme | Microsoft Docs
description: Kuruluşunuzun bulut tabanlı uygulamalar, şirket içi uygulamalar ve Azure Active Directory grupları kullanarak kaynaklara erişimi yönetme hakkında bilgi edinin.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: ajburnle
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: acbc19c2148a2b62ff1aa28d705591430b4fad20
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561824"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Azure Active Directory grupları'nı kullanarak uygulama ve kaynak erişimini yönetme
Azure Active Directory (Azure AD) bulut tabanlı uygulamalarınızı şirket içi uygulamalar ve kuruluşunuzun grupları kullanarak kaynaklarınızı yönetmenize yardımcı olur. Kaynaklarınızı directory ya da dış dizin rolleri aracılığıyla nesneleri gibi yazılım olarak hizmet (SaaS) uygulamaları için Azure, SharePoint siteleri, hizmetleri ve şirket içi kaynakları yönetmek için izinleri gibi bir dizinin parçası olabilir.

>[!NOTE]
>Azure Active Directory'yi kullanmak için bir Azure hesabına ihtiyacınız vardır. Hesabınız yoksa [ücretsiz Azure hesabı için kaydolabilirsiniz](https://azure.microsoft.com/free/).

## <a name="how-does-access-management-in-azure-ad-work"></a>Nasıl erişim yönetimini Azure AD iş?
Tek bir kullanıcı için erişim haklarını sağlayarak kuruluşunuzun kaynaklarına erişmesini veya grup için tüm Azure AD'yi azure AD yardımcı olur. Grupları kullanarak, kaynak sahibi (veya Azure AD directory sahibi), tek tek hakları sağlamak zorunda kalmak yerine bu grubun tüm üyeleri için erişim izinleri kümesi atama olanak tanır. Kaynak veya dizin sahibi de yönetim hakları üye listesi için bir bölüm Yöneticisi'ni veya bir Yardım Masası Yöneticisi, bu kişinin, ekleme ve gerektiğinde üyeleri kaldırma gibi başka bir kişiye verebilirsiniz. Grup sahiplerini yönetme hakkında daha fazla bilgi için bkz. [Grup sahiplerini yönetme](active-directory-accessmanagement-managing-group-owners.md)

![Azure Active Directory erişim yönetimi diyagramı](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Erişim hakları atama yolları
Kullanıcıların erişim haklarını kaynak atamak için izleyebileceğiniz dört yol vardır:

- **Doğrudan atamayı.** Kaynak sahibi kullanıcı kaynağa doğrudan atar.

- **Grup ataması.** Kaynak sahibi Azure AD grubu için kaynak sağlayan otomatik olarak, tüm Grup üyeleri erişimi kaynağa atar. Grup üyeliği, Grup sahibi ve üye ekleme veya gruptan kaldırma ya da sahibi izin vererek kaynak sahibi tarafından yönetilir. Grup üyeliğini ekleme veya kaldırma hakkında daha fazla bilgi için bkz [. nasıl yapılır: Azure Active Directory portalını](active-directory-groups-membership-azure-portal.md)kullanarak başka bir gruptan Grup ekleyin veya kaldırın. 

- **Kural tabanlı atama.** Kaynak sahibi bir grup oluşturur ve hangi kullanıcıların belirli bir kaynağa atanmış tanımlamak için bir kural kullanır. Kural bireysel kullanıcılara atanan ve öznitelikleri temel alır. Kaynak sahibi, hangi öznitelikleri ve değerleri kaynak erişime izin vermek için gerekli olan belirleme kural yönetir. Daha fazla bilgi için [dinamik bir grup oluşturun ve durumunu denetlemek](../users-groups-roles/groups-create-rule.md).

    Oluşturma ve dinamik grupları kullanma hakkında hızlı bir açıklama bu kısa videoyu da izleyebilirsiniz:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Dış yetkilisi atama.** Bir şirket içi dizin veya bir SaaS uygulaması gibi bir dış kaynaktan erişim gelir. Bu durumda, kaynağa erişim sağlamak için bir grup kaynak sahibi atar ve ardından Grup üyelerinin dış kaynağı yönetir.

   ![Erişim yönetimine genel bakış diyagramı](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Kullanıcılar, gruplar atanmadan birleştirebilir miyim?
Grup sahibi katılma, atamak yerine kendi grupları bulmak izin verebilirsiniz. Katılan tüm kullanıcılar otomatik olarak kabul etme veya onay gerektirmesini sahibi grubu de ayarlayabilirsiniz.

Bir kullanıcı bir gruba katılma isteklerini sonra isteği Grup sahibine iletilir. Gerekliyse, sahibi, isteği onaylayabilir ve kullanıcının grup üyeliğini bildirilir. Ancak, birden fazla sahibe varsa ve bunlardan birinin disapproves kullanıcı bildirilir ancak grubuna eklenmez. Daha fazla bilgi ve kullanıcılarınızın bu gruplara katılmak için istek izin vermek hakkında yönergeler için bkz. [kullanıcılar bu gruplara katılmak için isteyebilir için Azure AD'yi ayarlarken ayarlayın](../users-groups-roles/groups-self-service-management.md)

## <a name="next-steps"></a>Sonraki adımlar
Biraz erişim yönetim gruplarını kullanarak bir giriş olduğuna göre kaynakları ve uygulamaları yönetmek başlatın.

- [Azure Active Directory kullanarak yeni bir grup oluşturmak](active-directory-groups-create-azure-portal.md) veya [oluşturma ve PowerShell cmdlet'lerini kullanarak yeni bir grubu yönetme](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Tümleşik bir SaaS uygulamasına erişim atamak için grupları kullanma](../users-groups-roles/groups-saasapps.md)

- [Azure AD Connect kullanarak Azure için bir şirket içi Grup eşitleme](../hybrid/whatis-hybrid-identity.md)
