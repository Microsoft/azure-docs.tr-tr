---
title: Azure Active Directory portalındaki denetim etkinliği raporları | Microsoft Docs
description: Azure Active Directory portalındaki denetim etkinliği raporlarına giriş
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8883e3ade7e5d52f3a62346bf8be51ac36042f82
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008534"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory portalındaki denetim etkinliği raporları 

Azure Active Directory (Azure AD) raporlarında, ortamınızın nasıl çalıştığını öğrenmek için gereken bilgileri alabilirsiniz.

Raporlama mimarisi aşağıdaki bileşenlerden oluşur:

- **Etkinlik** 
    - **Oturum açma** işlemleri – [oturum açma işlemleri raporu](concept-sign-ins.md) , yönetilen uygulamaların kullanımı ve Kullanıcı oturum açma etkinliklerinin kullanımı hakkında bilgi sağlar.
    - **Denetim günlükleri** - Azure AD içindeki çeşitli özellikler tarafından yapılan tüm değişiklikler için günlükler aracılığıyla izlenebilirlik sağlar. Denetim günlüklerine örnek olarak, Azure AD 'de Kullanıcı, uygulama, Grup, rol ve ilke ekleme veya kaldırma gibi herhangi bir kaynak üzerinde yapılan değişiklikler bulunur.
- **Güvenlik** 
    - **Riskli oturum** açma işlemleri- [riskli oturum](concept-risky-sign-ins.md) açma, bir kullanıcı hesabının meşru sahibi olmayan birisi tarafından gerçekleştirilmiş olabilecek oturum açma girişimine yönelik bir göstergedir. 
    - **Risk için Işaretlenen kullanıcılar** - [riskli bir Kullanıcı](concept-user-at-risk.md) , tehlikeye girmiş olabilecek bir kullanıcı hesabı göstergesidir.

Bu makale, denetim raporuna genel bir bakış sağlar.
 
## <a name="who-can-access-the-data"></a>Verilere kimler erişebilir?

* **Güvenlik Yöneticisi**, **güvenlik okuyucu**, **rapor okuyucu** veya **genel yönetici** rollerinin kullanıcıları

## <a name="audit-logs"></a>Denetim günlükleri

Azure AD denetim günlükleri, uyumluluk için sistem etkinliklerinin kayıtlarını sağlar. Denetim raporuna erişmek için **Azure Active Directory** **etkinlik** bölümündeki **Denetim günlükleri** ' ni seçin. Denetim günlüklerinin bir saate kadar gecikme sürebileceğini unutmayın. bu nedenle, görevi tamamladıktan sonra Denetim etkinlik verilerinin portalda gösterilmesi uzun sürebilir.



Denetim günlüklerinin aşağıdakileri gösteren bir varsayılan liste görünümü vardır:

- Olayın tarihi ve saati
- oluşumu günlüğe tutan hizmet
- etkinliğin kategorisi ve adı (*ne*) 
- etkinliğin durumu (başarı veya başarısızlık)
- Hedef
- Etkinliğin başlatıcısı/aktör (kim)

![Denetim günlükleri](./media/concept-audit-logs/listview.png "Denetim günlükleri")

Araç çubuğunda **Sütunlar**’a tıklayarak liste görünümünü özelleştirebilirsiniz.

![Denetim günlükleri](./media/concept-audit-logs/columns.png "Denetim günlükleri")

Bu sayede ek alanları görüntüleyebilir ya da zaten görüntülenen alanları kaldırabilirsiniz.

![Denetim günlükleri](./media/concept-audit-logs/columnselect.png "Denetim günlükleri")

Daha ayrıntılı bilgi edinmek için liste görünümünde bir öğe seçin.

![Denetim günlükleri](./media/concept-audit-logs/details.png "Denetim günlükleri")


## <a name="filtering-audit-logs"></a>Denetim günlüklerini filtreleme

Denetim verilerini aşağıdaki alanlarda filtreleyebilirsiniz:

- Hizmet
- Kategori
- Etkinlik
- Durum
- Hedef
- Başlatan (Aktör)
- Tarih aralığı

![Denetim günlükleri](./media/concept-audit-logs/filter.png "Denetim günlükleri")

**Hizmet** filtresi, aşağıdaki hizmetlerin açılan listesinden seçim yapmanıza olanak sağlar:

- Tümü
- Erişim Gözden Geçirmeleri
- Hesap Sağlama 
- Uygulama SSO 'SU
- Kimlik Doğrulaması Yöntemleri
- B2C
- Koşullu Erişim
- Çekirdek Dizin
- Yetkilendirme Yönetimi
- Kimlik Koruması
- Davetli Kullanıcılar
- PıM
- Self Servis Grup Yönetimi
- Self Servis Parola Yönetimi
- Kullanım Koşulları

**Kategori** filtresi, aşağıdaki filtrelerden birini seçmenize olanak sağlar:

- Tümü
- AdministrativeUnit
- ApplicationManagement
- Kimlik Doğrulaması
- Yetkilendirme
- İletişim
- Cihaz
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Diğer
- İlke
- ResourceManagement
- RoleManagement
- Kullanıcı Yönetimi

**Etkinlik** filtresi, yaptığınız kategori ve etkinlik kaynak türü seçimine dayalıdır. Görmek istediğiniz belirli bir etkinliği ya da tüm etkinlikleri seçebilirsiniz. 

Graph API kullanarak tüm denetim etkinliklerinin listesini alabilirsiniz: `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

**Durum** filtresi, bir denetim işleminin durumuna göre filtrelemenize izin verir. Durum aşağıdakilerden biri olabilir:

- Tümü
- Başarılı
- Hata

**Hedef** filtre, belirli bir hedefin adına veya Kullanıcı asıl ADıNA (UPN) göre arama yapmanıza olanak tanır. Hedef adı ve UPN, büyük/küçük harfe duyarlıdır. 

**Başlatan** filtresi, bir aktörün adını ya da evrensel asıl adı (UPN) tanımlamanıza olanak sağlar. Ad ve UPN, büyük/küçük harfe duyarlıdır.

**Tarih aralığı** filtresi, döndürülen veriler için bir zaman çerçevesi tanımlamanıza olanak sağlar.  
Olası değerler şunlardır:

- 1 ay
- 7 gün
- 24 saat
- Özel

Özel bir zaman çerçevesi seçerken başlangıç ve bitiş zamanını yapılandırabilirsiniz.

Filtre uygulanmış verileri, 250.000 kayda kadar, **İndir** düğmesini seçerek de indirmeyi seçebilirsiniz. Günlükleri CSV veya JSON biçiminde indirebilirsiniz. İndirebileceğiniz kayıt sayısı [Azure Active Directory rapor bekletme ilkeleri](reference-reports-data-retention.md)tarafından sınırlandırılır.

![Denetim günlükleri](./media/concept-audit-logs/download.png "Denetim günlükleri")

## <a name="audit-logs-shortcuts"></a>Denetim günlükleri kısayolları

Azure portalı, **Azure Active Directory**’ye ek olarak verileri denetlemeniz için fazladan iki giriş noktası sağlar:

- Kullanıcılar ve gruplar
- Kurumsal uygulamalar

### <a name="users-and-groups-audit-logs"></a>Kullanıcı ve gruplara yönelik denetim günlükleri

Kullanıcı ve grup tabanlı denetim raporları ile aşağıdakiler gibi soruların yanıtlarını alabilirsiniz:

- Kullanıcılara hangi tür güncelleştirmeler uygulanmış?

- Kaç adet kullanıcı değiştirildi?

- Kaç adet parola değiştirildi?

- Bir yönetici bir dizinde neler yaptı?

- Eklenmiş olan gruplar hangileridir?

- Üyelik değişiklikleri olan gruplar var mı?

- Grubun sahipleri değişti mi?

- Bir grup veya kullanıcıya hangi lisanslar atanmış?

Yalnızca kullanıcılarla ilgili denetim verilerini gözden geçirmek istiyorsanız, **Kullanıcılar** sekmesinin **etkinlik** bölümündeki **Denetim günlükleri** altında filtrelenmiş bir görünüm bulabilirsiniz. Bu giriş noktasındaki **Kullanıcı yönetimi** , önceden seçilmiş kategori olarak var.

![Denetim günlükleri](./media/concept-audit-logs/users.png "Denetim günlükleri")

Yalnızca gruplarla ilgili denetim verilerini gözden geçirmek istiyorsanız, **gruplar** sekmesinin **etkinlik** bölümündeki **Denetim günlükleri** altında filtrelenmiş bir görünüm bulabilirsiniz. Bu giriş noktası, önceden seçilmiş kategori olarak **Groupmanagement** 'a sahiptir.

![Denetim günlükleri](./media/concept-audit-logs/groups.png "Denetim günlükleri")

### <a name="enterprise-applications-audit-logs"></a>Kurumsal uygulamaların denetim günlükleri

Uygulama tabanlı denetim raporları ile aşağıdakiler gibi soruların yanıtlarını alabilirsiniz:

* Eklenen veya güncellenen uygulamalar nelerdir?
* Hangi uygulamalar kaldırıldı?
* Bir uygulama için bir hizmet sorumlusu değiştirildi mi?
* Uygulamaların adları değiştirildi mi?
* Belirli bir uygulama için kim onay verdi?

Uygulamalarınızla ilgili denetim verilerini gözden geçirmek istiyorsanız, **Kurumsal uygulamalar** dikey penceresinin **etkinlik** bölümündeki **Denetim günlükleri** altında filtrelenmiş bir görünüm bulabilirsiniz. Bu giriş noktasında, **uygulama türü**olarak önceden seçilmiş **Kurumsal uygulamalar** vardır.

![Denetim günlükleri](./media/concept-audit-logs/enterpriseapplications.png "Denetim günlükleri")

## <a name="office-365-activity-logs"></a>Office 365 etkinlik günlükleri

[Microsoft 365 Yönetim merkezinden](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)Office 365 etkinlik günlüklerini görüntüleyebilirsiniz. Office 365 etkinliği ve Azure AD etkinlik günlükleri birçok dizin kaynağını paylaşsa da, yalnızca Microsoft 365 Yönetim Merkezi, Office 365 etkinlik günlüklerinin tam görünümünü sağlar. 

Office [365 Yönetim API 'lerini](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)kullanarak da Office 365 etkinlik günlüklerine programlı bir şekilde erişebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD denetim etkinliği başvurusu](reference-audit-activities.md)
- [Azure AD raporları bekletme başvurusu](reference-reports-data-retention.md)
- [Azure AD günlüğü gecikme süreleri başvurusu](reference-reports-latencies.md)
