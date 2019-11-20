---
title: Self Servis Grup yönetimini ayarlama-Azure Active Directory | Microsoft Docs
description: Azure Active Directory'de (Azure AD) güvenlik grupları veya Office 365 grupları oluşturma ve yönetin ve güvenlik grubu veya Office 365 grup üyelikleri isteme
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b52604d4ad20fed83c4649f046722ed45e766c4
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097710"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Azure Active Directory içinde Self Servis Grup yönetimini ayarlama 

Kullanıcıların Azure Active Directory (Azure AD) içinde kendi güvenlik gruplarını veya Office 365 gruplarını oluşturmasını ve yönetmesini sağlayabilirsiniz. Grubun sahibi üyelik isteklerini onaylayabilir veya reddedebilir ve grup üyeliği denetimini temsil edebilir. Self Servis Grup yönetimi özellikleri posta etkin güvenlik grupları veya dağıtım listeleri için kullanılamaz.

## <a name="self-service-group-membership-defaults"></a>Self Servis Grup üyeliği Varsayılanları

Azure portal veya Azure AD PowerShell kullanılarak güvenlik grupları oluşturulduğunda, yalnızca grubun sahipleri üyeliği güncelleştirebilir. [Erişim panelinde](https://account.activedirectory.windowsazure.com/r#/joinGroups) oluşturulan güvenlik grupları ve tüm Office 365 grupları, sahip tarafından onaylanmış veya otomatik olarak onaylanan tüm kullanıcılara katılabilir. Erişim panelinde, grubu oluştururken üyelik seçeneklerini değiştirebilirsiniz.

İçinde oluşturulan gruplar | Güvenlik grubu varsayılan davranışı | Office 365 grup varsayılan davranışı
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Üyeler yalnızca sahipler tarafından eklenebilir<br>Görünür ancak erişim paneli 'ne katılması için kullanılamaz | Tüm kullanıcılara katılması için aç
[Azure portal](https://portal.azure.com) | Üyeler yalnızca sahipler tarafından eklenebilir<br>Görünür ancak erişim paneli 'ne katılması için kullanılamaz<br>Grup oluşturma sırasında otomatik olarak sahip atanmadı | Tüm kullanıcılara katılması için aç
[Erişim paneli](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Tüm kullanıcılara katılması için aç<br>Grup oluşturulduğunda üyelik seçenekleri değiştirilebilir | Tüm kullanıcılara katılması için aç<br>Grup oluşturulduğunda üyelik seçenekleri değiştirilebilir

## <a name="self-service-group-management-scenarios"></a>Self Servis Grup yönetimi senaryoları

* **Temsilcili grup yönetimi** Şirketinin kullandığı SaaS uygulamasına erişimi yöneten bir yönetici, bu senaryoya örnek olarak verilebilir. Bu erişim haklarını yönetmek sıkıcı bir hal alabilir; bu durumda yönetici, işletme sahibinden yeni bir grup oluşturmasını ister. Yönetici, uygulama için yeni gruba erişim atar ve uygulamaya daha önce erişen tüm kişilerin grubuna ekler. Ardından işletme sahibi daha fazla kullanıcı ekleyebilir ve bu kullanıcılar uygulamaya otomatik olarak sağlanır. İşletme sahibinin, yöneticinin kullanıcılar için erişimi yönetmesini beklemesi gerekmez. Yönetici farklı bir iş grubundaki bir yöneticiye aynı izni veriyorsa, bu kişi kendi grup üyelerine erişimi de yönetebilir. Ne iş sahibi ne de yönetici, her birinin grup üyeliklerini görüntüleyebilir veya yönetebilir. Yönetici uygulamaya erişimi olan tüm kullanıcıları görebilir ve gerekirse erişim haklarını engelleyebilir.
* **Self servis grup yönetimi** SharePoint Online sitelerine sahip olan ve bu siteleri ayrı şekilde ayarlayan iki kullanıcı, bu senaryoya örnek olarak verilebilir. Bunlar birbirlerinin ekiplerine siteleri için erişim vermek ister. Bunun için Azure AD'de bir grup oluşturabilirler ve her biri SharePoint Online'da kendi sitelerine erişim sunmak üzere bu grubu seçerler. Birisi erişim istediğinde Erişim Paneli'nden ister ve onayın ardından otomatik olarak iki SharePoint Online sitesine de erişim sağlanır. Daha sonra bu kişilerden biri, siteye erişen herkesin belirli bir SaaS uygulamasına da erişmesi gerektiğine karar verir. SaaS uygulamasının yöneticisi uygulamanın erişim haklarını SharePoint Online sitesine ekleyebilir. Daha sonra onaylanan tüm istekler, iki SharePoint Online sitesine ve bu SaaS uygulamasına erişim sağlar.

## <a name="make-a-group-available-for-user-self-service"></a>Bir grubu kullanıcı self servisi için kullanıma sunma

1. Dizin için genel yönetici olan bir hesapla [Azure AD yönetim merkezinde](https://aad.portal.azure.com) oturum açın.
2. **Kullanıcılar ve gruplar**'ı ve ardından **Grup ayarları**'nı seçin.
3. **Self servis grup yönetimi etkin** ayarını **Evet** olarak belirleyin.
4. **Kullanıcılar güvenlik grupları oluşturabilir** veya **Kullanıcılar Office 365 grupları oluşturabilir** ayarını **Evet** olarak belirleyin.
   * Bu ayarlar etkinleştirildiğinde, dizininizdeki tüm kullanıcılara yeni güvenlik grupları oluşturma ve bu gruplara üye ekleme izni verilir. Bu yeni gruplar ayrıca diğer tüm kullanıcılar Erişim Paneli’nde gösterilir. Grup üzerindeki ilke ayarı izin veriyorsa diğer kullanıcılar bu gruplara katılmak için istekler oluşturabilir. 
   * Bu ayarlar devre dışı ise kullanıcılar grup oluşturamaz ve sahibi oldukları mevcut grupları değiştiremez. Ancak, bu grupların üyeliklerini yönetmeye ve diğer kullanıcıların gruplara katılma isteklerini onaylamaya devam edebilirler.

**Kullanıcılar güvenlik grupları oluşturabilir** ve **Kullanıcılar Office 365 grupları oluşturabilir** ayarlarını kullanarak kullanıcılarınız için self servis grup yönetimi ile ilgili daha ayrıntılı bir erişim denetimi elde edebilirsiniz. **Kullanıcılar grup oluşturabilir** seçeneği etkinleştirildiğinde, kiracınızdaki tüm kullanıcılara yeni grup oluşturma ve bu gruplara üye ekleme izni verilir. Kendi gruplarını oluşturabileceğiniz bireyler belirtemezsiniz. Yalnızca başka bir grup üyesini Grup sahibi yapmak için bireyler belirtebilirsiniz.

**Office 365 gruplarını** **Evet**olarak yönetebilen **güvenlik grupları ve kullanıcılar için self servis kullanabilecek kullanıcıları** ayarlayarak, kiracınızdaki tüm kullanıcıların yeni gruplar oluşturmasını sağlayabilirsiniz.

**Kullanıcılar güvenlik grupları oluşturabilir** ve **Kullanıcılar Office 365 grupları oluşturabilir** ayarlarını ayrıca üyeleri self servis hizmetini kullanacak tek bir grubu belirlemek için de kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalelerde Azure Active Directory ile ilgili ek bilgi sağlanmıştır.

* [Azure Active Directory grupları ile kaynaklara erişimi yönetme](../fundamentals/active-directory-manage-groups.md)
* [Grup ayarlarını yapılandırmak için Azure Active Directory cmdlet'leri](groups-settings-cmdlets.md)
* [Azure Active Directory’de Uygulama Yönetimi](../manage-apps/what-is-application-management.md)
* [Azure Active Directory nedir?](../fundamentals/active-directory-whatis.md)
* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](../hybrid/whatis-hybrid-identity.md)
