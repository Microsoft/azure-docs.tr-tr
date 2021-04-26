---
title: Azure Active Directory yetkilendirme yönetimiyle dış erişimi yönetme
description: Azure Active Directory yetkilendirme yönetimi, genel dış erişim güvenlik planınızın bir parçası olarak nasıl kullanılır.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89744b63a555cc02d35815b4066ce572b7f77e38
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531901"
---
# <a name="manage-external-access-with-entitlement-management"></a>Yetkilendirme yönetimiyle dış erişimi yönetme 


[Yetkilendirme Yönetimi](../governance/entitlement-management-overview.md) , kuruluşların erişim isteği iş akışlarını, erişim atamalarını, İncelemeleri ve süre sonunu otomatikleştirerek kimlik ve erişim yaşam döngüsünü ölçekteki yönetmesine olanak tanıyan bir kimlik idare özelliğidir. Yetkilendirme Yönetimi, temsilcili yönetici olmayan kuruluşların, diğer kuruluşlardaki dış kullanıcıların erişim isteyebileceğini [erişim paketleri](../governance/entitlement-management-overview.md) oluşturmasına izin verir. Bir ve çok aşamalı onay iş akışları istekleri değerlendirmek ve yinelenen incelemelerle kullanıcılara zaman sınırlı erişim [sağlamak](../governance/what-is-provisioning.md) için yapılandırılabilir. Yetkilendirme Yönetimi, dış hesapların ilke tabanlı sağlanması ve sağlanması için izin verebilir.

## <a name="key-concepts-for-enabling-entitlement-management"></a>Yetkilendirme yönetimini etkinleştirmeye yönelik temel kavramlar

Aşağıdaki temel kavramlar, yetkilendirme yönetiminin anlaşılması açısından önemlidir.

### <a name="access-packages"></a>Erişim paketleri

[Erişim paketi](../governance/entitlement-management-overview.md) , yetkilendirme yönetiminin temelidir. Erişim paketleri, bir kullanıcının bir projede işbirliği yapması veya başka görevler yapması için ihtiyacı olan ilkeyle yönetilen kaynakların gruplanmalarıdır. Örneğin, bir erişim paketi şunları içerebilir:

* belirli SharePoint sitelerine erişim.

* Salesforce gibi özel şirket içi ve SaaS uygulamalarınız dahil kurumsal uygulamalar.

* Microsoft ekipleri kanalları.

* Grupları Microsoft 365. 

### <a name="catalogs"></a>Kataloglar

Erişim paketleri [kataloglarda](../governance/entitlement-management-catalog-create.md)bulunur. İlgili kaynakları gruplandırmak ve paketlere erişmek ve bunları yönetme yeteneğini devretmek istediğinizde bir katalog oluşturursunuz. Önce bir kataloğa kaynak eklersiniz ve ardından bu kaynakları, paketlere erişmek için ekleyebilirsiniz. Örneğin, bir "Finans" kataloğu oluşturmak ve yönetimini finans ekibinin bir üyesine [devretmek](../governance/entitlement-management-delegate.md) isteyebilirsiniz. Bu kişi daha sonra [kaynak ekleyebilir](../governance/entitlement-management-catalog-create.md), erişim paketleri oluşturabilir ve bu paketlere yönelik erişim onayını yönetebilir.

Aşağıdaki diyagramda, bir dış kullanıcının süre sonu olan bir erişim paketine erişim hakkı veren tipik bir idare yaşam döngüsü gösterilmektedir.

![Dış Kullanıcı idare döngüsünün diyagramı.](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>Self Servis dış erişim

Dış kullanıcıların erişim istemesine olanak tanımak için [Azure AD erişim portalından](../governance/entitlement-management-request-access.md) erişim paketlerini Surface 'a bırakabilirsiniz. İlkeler kimin bir erişim paketi isteyeceğini tespit edebilir. Erişim paketini isteme izni olan kişileri belirtirsiniz:

* Belirli [bağlı kuruluşlar](../governance/entitlement-management-organization.md)

* Tüm yapılandırılmış bağlı kuruluşlar

* Herhangi bir kuruluştan tüm kullanıcılar

* Kiracınızda zaten bulunan üye veya Konuk kullanıcılar

### <a name="approvals"></a>Onaylar   
Erişim paketleri erişim için zorunlu onay içerebilir. **Dış kullanıcılar Için her zaman onay işlemini uygulayın**. Onaylar tek veya çok aşamalı onay olabilir. Onaylar ilkelere göre belirlenir. Hem iç hem de dış kullanıcıların aynı pakete erişmesi gerekiyorsa, büyük olasılıkla bağlı kuruluşların farklı kategorileri ve iç kullanıcılar için farklı erişim ilkeleri ayarlayabilirsiniz.

### <a name="expiration"></a>Bitiş tarihi  
Erişim paketleri bir süre sonu tarihi içerebilir. Süre sonu belirli bir güne ayarlanabilir veya kullanıcıya erişim için belirli bir gün sayısı verebilir. Erişim paketinin süresi dolarsa ve Kullanıcı başka bir erişime sahip değilse, kullanıcıyı temsil eden B2B Konuk Kullanıcı nesnesi silinebilir veya oturum açması engellenir. Dış kullanıcılara yönelik erişim paketlerinde süre sonu zorlamanıza zorlanmasını öneririz. Tüm erişim paketlerinin süre sonları yok. Olmayanlar için, erişim gözden geçirmeleri gerçekleştirdiğinizden emin olun.

### <a name="access-reviews"></a>Erişim gözden geçirmeleri

Erişim paketleri, paket sahibini veya bir designee 'nin kullanıcıların erişimi için devam eden gereksinimi atmaya yönelik bir test gerektiren düzenli [erişim İncelemeleri](../governance/manage-guest-access-with-access-reviews.md)gerektirebilir. 

İncelemenizi ayarlamadan önce aşağıdakileri belirleyin.

* Sağlayan

   * Devam eden erişim ölçütleri nelerdir?

   * Belirtilen gözden geçirenler kim?

* Zamanlanan incelemeler ne sıklıkla oluşur?

   * Yerleşik Seçenekler aylık, üç aylık, iki haftada bir veya yıllık olarak bulunur. 

   * Dış erişimi destekleyen paketler için üç aylık veya daha sık önerilir. 

 

> [!IMPORTANT]
> Erişim paketlerine erişim gözden geçirmeleri yalnızca yetkilendirme yönetimi aracılığıyla verilen erişimi gözden geçirir. Bu nedenle, Yetkilendirme Yönetimi dışındaki dış kullanıcılara sunulan erişimi gözden geçirmek için diğer süreçler ayarlamanız gerekir.

Erişim gözden geçirmeleri hakkında daha fazla bilgi için bkz. [Azure AD erişim gözden geçirmeleri dağıtımı planlama](../governance/deploy-access-reviews.md).

## <a name="using-automation-in-entitlement-management"></a>Yetkilendirme yönetiminde Otomasyonu kullanma

[Microsoft Graph kullanarak yetkilendirme yönetimi işlevleri](/graph/tutorial-access-package-api)gerçekleştirebilirsiniz.

* [Erişim paketlerini Yönet](/graph/api/resources/accesspackage?view=graph-rest-beta&preserve-view=true)

* [Erişim incelemelerini yönetme](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)

* [Bağlı kuruluşları yönetme](/graph/api/resources/connectedorganization?view=graph-rest-beta&preserve-view=true)

* [Yetkilendirme Yönetimi ayarlarını yönetme](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta&preserve-view=true)

## <a name="recommendations"></a>Öneriler 

Yetkilendirme yönetimiyle dış erişimi yönetmek için yöntemler öneririz.

**Bir veya daha fazla iş ortağı olan projeler için, bu iş ortağının kullanıcılarına kaynak erişimi eklemek ve sağlamak üzere [erişim paketleri oluşturup kullanın](../governance/entitlement-management-access-package-create.md)**. 

* Dizininizde zaten B2B kullanıcıları varsa, bunları uygun erişim paketlerine de doğrudan atayabilirsiniz.

* [Azure Portal](../governance/entitlement-management-access-package-assignments.md)veya [Microsoft Graph](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta&preserve-view=true)aracılığıyla erişim atayabilirsiniz.

**Erişim paketleri sona erdiğinde, dizininizdeki kullanıcıları kaldırmak Için kimlik idare ayarlarınızı kullanın**.

![Dış kullanıcıların yaşam döngüsünü yönetme yapılandırması ekran görüntüsü.](media/secure-external-access/6-manage-external-lifecycle.png)

Bu ayarlar yalnızca, yetkilendirme yönetimi aracılığıyla eklendi olan kullanıcılar için geçerlidir.

**[Katalogların ve erişim paketlerinin yönetim yetkisini Iş sahiplerine devretmek](../governance/entitlement-management-delegate.md) , kimin erişmesi gerektiği hakkında daha fazla bilgi sahibi**.

![Katalog yapılandırma ekran görüntüsü.](media/secure-external-access/6-catalog-management.png)

**Dış kullanıcıların erişimine sahip olduğu [erişim paketlerinin sona erme süresini zorlar](../governance/entitlement-management-access-package-lifecycle-policy.md) .**


![Erişim paketi süre sonunu yapılandırma ekran görüntüsü.](media/secure-external-access/6-access-package-expiration.png)

* Proje tabanlı bir erişim paketinin bitiş tarihini biliyorsanız, belirli bir tarihi ayarlamak için tarihi kullanın. 

* Aksi takdirde, çok yıllık bir katılım olmadığı bilinmediği takdirde, süresinin artık 365 gün olması önerilir.

* Kullanıcıların erişimi genişlemesine izin verin.

* Uzantıya izin vermek için onay gerektir.

**Konukların uygunsuz erişimini önlemek için [paketlere yönelik erişim gözden geçirmeleri uygulayın](../governance/manage-guest-access-with-access-reviews.md) .**

![Yeni erişim paketi oluşturma ekranının ekran görüntüsü.](media/secure-external-access/6-new-access-package.png)

* İncelemeleri üç ayda bir uygulayın.

* Uyumluluğa duyarlı projeler için, gözden geçirenleri, dış kullanıcılara kendi kendini gözden geçirmek yerine belirli gözden geçirenler olacak şekilde ayarlayın. Paket yöneticilerine erişen kullanıcılar, gözden geçirenler için başlamak için iyi bir yerdir. 

* Daha az hassas projeler için, kullanıcıların kendi kendine gözden geçirmelerine sahip olması, artık ev kuruluşlarıyla olmayan kullanıcılardan erişimi kaldırmak için kuruluştaki yükü azaltır.

Daha fazla bilgi için bkz. [Azure AD yetkilendirme yönetiminde dış kullanıcılar için erişimi yönetme](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>Sonraki adımlar

Kaynaklara dışarıdan erişimi güvenli hale getirmek için aşağıdaki makalelere bakın. Eylemleri listelenen sırayla yapmanızı öneririz.

1. [Dış erişim için güvenlik durunuzu belirleme](1-secure-access-posture.md)

2. [Geçerli durumlarınızı bulma](2-secure-access-current-state.md)

3. [İdare planı oluşturma](3-secure-access-plan.md)

4. [Güvenlik için grupları kullanma](4-secure-access-groups.md)

5. [Azure AD B2B 'ye geçiş](5-secure-access-b2b.md)

6. [Yetkilendirme yönetimiyle güvenli erişim](6-secure-access-entitlement-managment.md) (burada bulabilirsiniz.)

7. [Koşullu erişim ilkeleriyle güvenli erişim](7-secure-access-conditional-access.md)

8. [Duyarlılık etiketleriyle güvenli erişim](8-secure-access-sensitivity-labels.md)

9. [Microsoft ekipleri, OneDrive ve SharePoint 'e güvenli erişim](9-secure-access-teams-sharepoint.md)

 

