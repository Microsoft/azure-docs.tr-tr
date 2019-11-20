---
title: Azure AD Yetkilendirme Yönetimi 'nde bir erişim paketinin kaynak rollerini değiştirme-Azure Active Directory
description: Azure Active Directory yetkilendirme yönetimi 'nde var olan bir erişim paketinin kaynak rollerini değiştirmeyi öğrenin.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 102bbfbd1c02c93830f5c7fce89fe95d7fde54c5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174651"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD yetkilendirme yönetiminde bir erişim paketinin kaynak rollerini değiştirme

Bir erişim paketi Yöneticisi olarak, bir erişim paketindeki kaynakları, kullanıcının yeni kaynaklara erişimini sağlama veya önceki kaynaklardan erişimleri kaldırma konusunda endişelenmeden dilediğiniz zaman değiştirebilirsiniz. Bu makalede, var olan bir erişim paketinin kaynak rollerinin nasıl değiştirileceği açıklanır.

Bu videoda, erişim paketinin nasıl değiştirileceği hakkında bir genel bakış sunulmaktadır.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Kaynak kataloğunu denetle

Bir erişim paketine kaynak eklemeniz gerekiyorsa, ihtiyacınız olan kaynakların katalogda kullanılabilir olup olmadığını denetlemeniz gerekir. Bir erişim paketi Yöneticisi kullanıyorsanız, kendilerine sahip olsanız bile bir kataloğa kaynak ekleyemezsiniz. Katalogda bulunan kaynakları kullanmaya kısıtlıdır.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol menüde, **Katalog** ' a tıklayın ve ardından kataloğu açın.

1. Sol taraftaki menüde, bu katalogdaki kaynakların listesini görmek için **kaynaklar** ' a tıklayın.

    ![Bir katalogdaki kaynakların listesi](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Bir erişim paketi yöneticisidir ve kataloğa kaynak eklemeniz gerekiyorsa, Katalog sahibinden onları eklemesini isteyebilirsiniz.

## <a name="add-resource-roles"></a>Kaynak rolleri ekleme

Kaynak rolü, bir kaynakla ilişkili izinlerin koleksiyonudur. Kaynakları kullanıcıların istemesi için kullanılabilir hale getirmenin yolu, erişim paketinize kaynak rolleri eklemektir. Gruplar, takımlar, uygulamalar ve SharePoint siteleri için kaynak rolleri ekleyebilirsiniz.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. Sol taraftaki menüden **kaynak rolleri**' ne tıklayın.

1. **Kaynak rolleri ekle** ' ye tıklayarak pakete erişmek için kaynak rolleri Ekle sayfasını açın.

    ![Erişim paketi-kaynak rolleri ekleme](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. Bir grup, takım, uygulama veya SharePoint sitesi eklemek istediğinize bağlı olarak, aşağıdaki kaynak rolü bölümlerinden birindeki adımları gerçekleştirin.

## <a name="add-a-group-or-team-resource-role"></a>Grup veya takım kaynağı rolü ekleme

Bir erişim paketi atandığında, yetkilendirme yönetiminin kullanıcıları Microsoft ekipteki bir gruba veya takıma otomatik olarak eklemesini sağlayabilirsiniz. 

- Bir grup veya takım bir erişim paketinin parçası olduğunda ve bu erişim paketine bir Kullanıcı atandığında, zaten mevcut değilse Kullanıcı o gruba veya takıma eklenir.
- Bir kullanıcının erişim paketi atamasının süresi dolarsa, o anda aynı grup veya takımı içeren başka bir erişim paketine bir atamaya sahip olmadıkları takdirde, bunlar gruptan veya takımdan kaldırılır.

Herhangi bir [Azure AD güvenlik grubunu veya Office 365 grubunu](../fundamentals/active-directory-groups-create-azure-portal.md)seçebilirsiniz. Yöneticiler, kataloğa herhangi bir grup ekleyebilir; Katalog sahipleri, grubun sahibiyseniz, kataloğa herhangi bir grup ekleyebilir. Bir grup seçerken aşağıdaki Azure AD kısıtlamalarını aklınızda bulundurun:

- Konuk dahil bir Kullanıcı, bir grup veya takıma üye olarak eklendiğinde, bu grubun veya ekibin tüm diğer üyelerini görebilirler.
- Azure AD, Azure AD Connect kullanılarak Windows Server Active Directory eşitlenen veya Exchange Online 'da bir dağıtım grubu olarak oluşturulan bir grubun üyeliğini değiştiremiyor.  
- Dinamik grupların üyeliği üye ekleyerek veya kaldırarak güncelleştirilemez, bu nedenle dinamik grup üyelikleri yetkilendirme yönetimiyle kullanım için uygun değildir.

Daha fazla bilgi için bkz. grupları ve [Office 365 gruplarını ve Microsoft ekiplerini](https://docs.microsoft.com/microsoftteams/office-365-groups) [karşılaştırın](https://docs.microsoft.com/office365/admin/create-groups/compare-groups) .

1. **Pakete erişmek için kaynak rolleri ekle** sayfasında, **gruplar ve takımlar** ' a tıklayarak Grup Seç bölmesini açın.

1. Erişim paketine eklemek istediğiniz grupları ve takımları seçin.

    ![Erişim paketi-kaynak rolleri ekleme-Grup seçme](./media/entitlement-management-access-package-resources/group-select.png)

1. **Seç**'e tıklayın.

    Grubu veya takımı seçtiğinizde, **alt tür** sütunu aşağıdaki alt türlerinden birini listeler:

    |  |  |
    | --- | --- |
    | Güvenlik | Kaynaklara erişim vermek için kullanılır. |
    | Dağıtım | Bir grup kişiye bildirim göndermek için kullanılır. |
    | O365 | Ekipler etkin olmayan Office 365 grubu. Şirket içindeki ve dışındaki kullanıcılar arasında işbirliği için kullanılır. |
    | Takım | Ekipler etkin olan Office 365 grubu. Şirket içindeki ve dışındaki kullanıcılar arasında işbirliği için kullanılır. |

1. **Rol** listesinde **Owner** veya **member**' ı seçin.

    Genellikle üye rolünü seçersiniz. Sahip rolünü seçerseniz, kullanıcıların başka Üyeler veya sahipler eklemesine veya kaldırmasına izin verir.

    ![Erişim paketi-bir grup veya takım için kaynak rolü ekleme](./media/entitlement-management-access-package-resources/group-role.png)

1. **Ekle**'ye tıklayın.

    Erişim paketine mevcut atamaları olan tüm kullanıcılar, eklendiğinde bu grubun veya ekibin üyeleri otomatik olarak olur.

## <a name="add-an-application-resource-role"></a>Uygulama kaynağı rolü ekleme

Bir kullanıcıya erişim paketi atandığında, Azure AD 'nin hem SaaS uygulamaları hem de kuruluşunuzun Azure AD 'ye federe uygulamalar dahil olmak üzere bir Azure AD kurumsal uygulamasına erişimini otomatik olarak atamasını sağlayabilirsiniz. Federasyon çoklu oturum açma aracılığıyla Azure AD ile tümleştirilen uygulamalar için Azure AD, uygulamaya atanan kullanıcılar için Federasyon belirteçleri verir.

Uygulamalar birden çok role sahip olabilir. Bir erişim paketine bir uygulama eklerken, bu uygulamanın birden fazla rolü varsa, bu kullanıcılar için uygun rolü belirtmeniz gerekir. Uygulama geliştiriyorsanız, bu rollerin uygulamalarınıza nasıl eklendiği hakkında daha fazla bilgi için bkz. [Kurumsal uygulamalar IÇIN SAML belirtecinde verilen rol talebini yapılandırma](../develop/active-directory-enterprise-app-role-management.md).

Uygulama rolü bir erişim paketinin parçası olduktan sonra:

- Bir Kullanıcı bu erişim paketine atandığında, zaten mevcut değilse Kullanıcı söz konusu uygulama rolüne eklenir.
- Bir kullanıcının erişim paketi atamasının süresi dolarsa, bu uygulama rolünü içeren başka bir erişim paketine atanması gerekmedikçe erişimleri uygulamadan kaldırılır.

Bir uygulama seçerken bazı konular aşağıda verilmiştir:

- Uygulamalarda Ayrıca, rollerine de atanmış gruplar bulunabilir.  Bir erişim paketindeki uygulama rolü yerine bir grup eklemeyi seçebilirsiniz, ancak bundan sonra uygulama kullanıcıya erişim portalındaki erişim paketinin bir parçası olarak görünmez.

1. **Pakete erişmek için kaynak rolleri ekle** sayfasında **uygulamalar** ' a tıklayarak uygulama Seç bölmesini açın.

1. Erişim paketine eklemek istediğiniz uygulamaları seçin.

    ![Erişim paketi-kaynak rolleri ekleme-uygulama seçme](./media/entitlement-management-access-package-resources/application-select.png)

1. **Seç**'e tıklayın.

1. **Rol** listesinde bir uygulama rolü seçin.

    ![Erişim paketi-bir uygulama için kaynak rolü ekleme](./media/entitlement-management-access-package-resources/application-role.png)

1. **Ekle**'ye tıklayın.

    Erişim paketine mevcut atamalara sahip tüm kullanıcılara, eklendiğinde bu uygulamaya otomatik olarak erişim verilir.

## <a name="add-a-sharepoint-site-resource-role"></a>SharePoint site kaynağı rolü ekleme

Azure AD, kullanıcılara bir SharePoint Online sitesine veya SharePoint Online site koleksiyonuna erişim paketi atandığında otomatik olarak erişim atayabilir.

1. **Pakete erişmek için kaynak rolleri ekle** sayfasında **SharePoint siteleri** ' ne tıklayarak SharePoint Online siteleri Seç bölmesini açın.

1. Erişim paketine eklemek istediğiniz SharePoint Online sitelerini seçin.

    ![Erişim paketi-kaynak rolleri ekleme-SharePoint Online siteleri seçin](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. **Seç**'e tıklayın.

1. **Rol** listesinde bir SharePoint Online site rolü seçin.

    ![Erişim paketi-SharePoint Online sitesi için kaynak rolü ekleme](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. **Ekle**'ye tıklayın.

    Erişim paketine mevcut atamalara sahip olan tüm kullanıcılara, eklendiğinde bu SharePoint Online sitesine otomatik olarak erişim verilir.

## <a name="remove-resource-roles"></a>Kaynak rollerini kaldırma

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. Sol taraftaki menüden **kaynak rolleri**' ne tıklayın.

1. Kaynak rolleri listesinde, kaldırmak istediğiniz kaynak rolünü bulun.

1. Üç noktaya ( **...** ) ve ardından **kaynak rolünü kaldır**' a tıklayın.

    Erişim paketine mevcut atamaları olan tüm kullanıcılar, kaldırıldığında bu kaynak rolü için otomatik olarak erişimi iptal eder.

## <a name="when-changes-are-applied"></a>Değişiklikler uygulandığında

Yetkilendirme Yönetimi 'nde Azure AD, erişim paketinizdeki atama ve kaynaklara yönelik toplu değişiklikleri günde birkaç kez işleyecek. Bu nedenle, bir atama yaparsanız veya erişim paketinizin kaynak rollerini değiştirirseniz, bu değişikliğin Azure AD 'de yapılması ve bu değişiklikleri diğer Microsoft Online hizmetlerine veya bağlı SaaS uygulamasına yayılması için geçen süreyi 24 saate kadar sürebilir olmalıdır Yaptığınız değişiklik yalnızca birkaç nesneyi etkiliyorsa, değişikliğin Azure AD 'de uygulanması birkaç dakika sürer. Bu durumda, diğer Azure AD bileşenleri bu değişikliği algılayacak ve SaaS uygulamalarını güncelleştirmeyecektir. Değişiklik, binlerce nesneyi etkiliyorsa değişiklik daha uzun sürer. Örneğin, 2 uygulama ve 100 Kullanıcı Ataması olan bir erişim paketiniz varsa ve erişim paketine bir SharePoint site rolü eklemeye karar verirseniz, tüm kullanıcılar bu SharePoint sitesi rolünün parçası olana kadar bir gecikme olabilir. İlerlemeyi Azure AD denetim günlüğü, Azure AD sağlama günlüğü ve SharePoint site denetim günlükleri aracılığıyla izleyebilirsiniz.

Ekibin bir üyesini kaldırdığınızda, bunlar Office 365 grubundan da kaldırılır. Ekibin sohbet işlevlerinden kaldırma gecikebilir. Daha fazla bilgi için bkz. [Grup üyeliği](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory kullanarak temel Grup oluşturma ve üye ekleme](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Nasıl yapılır: kurumsal uygulamalar için SAML belirtecinde verilen rol talebini yapılandırma](../develop/active-directory-enterprise-app-role-management.md)
- [SharePoint Online 'a giriş](/sharepoint/introduction)