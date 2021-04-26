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
ms.date: 09/17/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5aa8f675e3fd36fbebfecf42db0f02b0f0f00115
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995990"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory portalındaki denetim etkinliği raporları 

Azure Active Directory (Azure AD) raporlarında, ortamınızın nasıl çalıştığını öğrenmek için gereken bilgileri alabilirsiniz.



Raporlama mimarisi aşağıdaki bileşenlerden oluşur:

- **Etkinlik** 
    - **Oturum açma** işlemleri – [oturum açma işlemleri raporu](concept-sign-ins.md) , yönetilen uygulamaların kullanımı ve Kullanıcı oturum açma etkinliklerinin kullanımı hakkında bilgi sağlar.
    - **Denetim günlükleri** -Azure AD içindeki çeşitli özelliklerle yapılan tüm değişiklikler için Günlükler aracılığıyla izlenebilirlik sağlar. Azure AD içindeki herhangi bir kaynakta yapılan kullanıcı, uygulama, grup, rol ve ilkeleri ekleme veya kaldırma işlemleri gibi değişiklikler, denetim günlüklerine örnek gösterilebilir.
    - **Sağlama günlükleri**  -  [Sağlama günlükleri](./concept-provisioning-logs.md) , müşterilerin, ServiceNow 'da bir grup oluşturma veya Workday 'den içeri aktarılan bir kullanıcı gibi sağlama hizmeti tarafından etkinlikleri izlemelerine olanak tanır. 
- **Güvenlik** 
    - **Riskli oturum** açma işlemleri- [riskli oturum](../identity-protection/overview-identity-protection.md) açma, bir kullanıcı hesabının meşru sahibi olmayan birisi tarafından gerçekleştirilmiş olabilecek oturum açma girişimine yönelik bir göstergedir. 
    - **Risk için Işaretlenen kullanıcılar** - [riskli bir Kullanıcı](../identity-protection/overview-identity-protection.md) , tehlikeye girmiş olabilecek bir kullanıcı hesabı göstergesidir.

Bu makale, denetim raporuna genel bir bakış sağlar.
 
## <a name="who-can-access-the-data"></a>Verilere kimler erişebilir?

* **Güvenlik Yöneticisi**, **güvenlik okuyucu**, **rapor okuyucu** , **Genel okuyucu** veya **genel yönetici** rolleri içindeki kullanıcılar

## <a name="audit-logs"></a>Denetim günlükleri

Azure AD denetim günlükleri, uyumluluk için sistem etkinliklerinin kayıtlarını sağlar. Denetim raporuna erişmek için **Azure Active Directory** **izleme** bölümünde **Denetim günlükleri** ' ni seçin. 



Denetim günlüklerinin aşağıdakileri gösteren bir varsayılan liste görünümü vardır:

- Olayın tarihi ve saati
- oluşumu günlüğe tutan hizmet
- etkinliğin kategorisi ve adı (*ne*) 
- etkinliğin durumu (başarı veya başarısızlık)
- Hedef
- Bir etkinliğin başlatıcısı/aktörü (kim)

![Denetim günlükleri](./media/concept-audit-logs/listview.png "Denetim günlükleri")

Araç çubuğunda **Sütunlar**’a tıklayarak liste görünümünü özelleştirebilirsiniz.

![Denetim sütunları](./media/concept-audit-logs/columns.png "Denetim sütunları")

Bu sayede ek alanları görüntüleyebilir ya da zaten görüntülenen alanları kaldırabilirsiniz.

![Alanları kaldır](./media/concept-audit-logs/columnselect.png "Alanları kaldır")

Daha ayrıntılı bilgi edinmek için liste görünümünde bir öğe seçin.

![öğe seçin](./media/concept-audit-logs/details.png "Öğe seçin")


## <a name="filtering-audit-logs"></a>Denetim günlüklerini filtreleme

Denetim verilerini aşağıdaki alanlarda filtreleyebilirsiniz:

- Hizmet
- Category
- Etkinlik
- Durum
- Hedef
- Başlatan (Aktör)
- Tarih aralığı

![Filtre nesnesi](./media/concept-audit-logs/filter.png "Filtre nesnesi")

**Hizmet** filtresi, aşağıdaki hizmetlerin açılan listesinden seçim yapmanıza olanak sağlar:

- Tümü
- AAD yönetim UX
- Erişim Gözden Geçirmeleri
- Hesap Sağlama
- Uygulama Ara Sunucusu
- Kimlik Doğrulaması Yöntemleri
- B2C
- Koşullu Erişim
- Çekirdek Dizin
- Yetkilendirme Yönetimi
- Karma kimlik doğrulaması
- Kimlik Koruması
- Davetli Kullanıcılar
- MIM Hizmeti
- MyApps
- PIM
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
- KerberosDomain
- KeyManagement
- Etiketle
- Diğer
- PermissionGrantPolicy
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

**Hedef** filtre, ad veya Kullanıcı asıl adı (UPN) ile başlayarak belirli bir hedef için arama yapmanıza olanak tanır. Hedef adı ve UPN, büyük/küçük harfe duyarlıdır. 

**Başlatan** filtresi, bir aktör adının veya bir evrensel asıl ADıNıN (UPN) ile nasıl başlayacağını tanımlamanızı sağlar. Ad ve UPN, büyük/küçük harfe duyarlıdır.

**Tarih aralığı** filtresi, döndürülen veriler için bir zaman çerçevesi tanımlamanıza olanak sağlar.  
Olası değerler şunlardır:

- 7 gün
- 24 saat
- Özel

Özel bir zaman çerçevesi seçerken başlangıç ve bitiş zamanını yapılandırabilirsiniz.

Filtre uygulanmış verileri, 250.000 kayda kadar, **İndir** düğmesini seçerek de indirmeyi seçebilirsiniz. Günlükleri CSV veya JSON biçiminde indirebilirsiniz. İndirebileceğiniz kayıt sayısı [Azure Active Directory rapor bekletme ilkeleri](reference-reports-data-retention.md)tarafından sınırlandırılır.

![Verileri indirme](./media/concept-audit-logs/download.png "Verileri indirme")

## <a name="audit-logs-shortcuts"></a>Denetim günlükleri kısayolları

Azure portalı, **Azure Active Directory**’ye ek olarak verileri denetlemeniz için fazladan iki giriş noktası sağlar:

- Kullanıcılar ve gruplar
- Kurumsal uygulamalar

### <a name="users-and-groups-audit-logs"></a>Kullanıcı ve gruplara yönelik denetim günlükleri

Kullanıcı ve grup tabanlı denetim raporları ile aşağıdakiler gibi soruların yanıtlarını alabilirsiniz:

- Kullanıcılara hangi tür güncelleştirmeler uygulandı?

- Kaç adet kullanıcı değiştirildi?

- Kaç adet parola değiştirildi?

- Bir yönetici bir dizinde neler yaptı?

- Eklenmiş olan gruplar hangileridir?

- Üyelik değişiklikleri olan gruplar var mı?

- Grubun sahipleri değişti mi?

- Bir grup veya kullanıcıya hangi lisanslar atanmış?

Yalnızca kullanıcılarla ilgili denetim verilerini gözden geçirmek istiyorsanız, **Kullanıcılar** sekmesinin **izleme** bölümünde **Denetim günlükleri** altında filtrelenmiş bir görünüm bulabilirsiniz. Bu giriş noktasındaki **Kullanıcı yönetimi** , önceden seçilmiş kategori olarak var.

![Kullanıcı](./media/concept-audit-logs/users.png "Kullanıcı")

Yalnızca gruplarla ilgili denetim verilerini gözden geçirmek istiyorsanız, **gruplar** sekmesinin **izleme** bölümünde **Denetim günlükleri** altında filtrelenmiş bir görünüm bulabilirsiniz. Bu giriş noktası, önceden seçilmiş kategori olarak **Groupmanagement** 'a sahiptir.

![Grupları filtrele](./media/concept-audit-logs/groups.png "Grupları filtrele")

### <a name="enterprise-applications-audit-logs"></a>Kurumsal uygulamaların denetim günlükleri

Uygulama tabanlı denetim raporları ile aşağıdakiler gibi soruların yanıtlarını alabilirsiniz:

* Eklenen veya güncellenen uygulamalar nelerdir?
* Hangi uygulamalar kaldırıldı?
* Bir uygulama için bir hizmet sorumlusu değiştirildi mi?
* Uygulamaların adları değiştirildi mi?
* Belirli bir uygulama için kim onay verdi?

Uygulamalarınızla ilgili denetim verilerini gözden geçirmek istiyorsanız, **Kurumsal uygulamalar** dikey penceresinin **etkinlik** bölümündeki **Denetim günlükleri** altında filtrelenmiş bir görünüm bulabilirsiniz. Bu giriş noktasında, **uygulama türü** olarak önceden seçilmiş **Kurumsal uygulamalar** vardır.

![Kurumsal uygulamalar](./media/concept-audit-logs/enterpriseapplications.png "Kurumsal uygulamalar")

## <a name="microsoft-365-activity-logs"></a>Etkinlik günlüklerini Microsoft 365

[Microsoft 365 Yönetim merkezinden](/office365/admin/admin-overview/about-the-admin-center)Microsoft 365 etkinlik günlüklerini görüntüleyebilirsiniz. Microsoft 365 etkinliği ve Azure AD etkinlik günlükleri birçok dizin kaynağını paylaşsa da, yalnızca Microsoft 365 Yönetim Merkezi Microsoft 365 etkinlik günlüklerinin tam görünümünü sağlar. 

Ayrıca, [Office 365 Yönetim API 'lerini](/office/office-365-management-api/office-365-management-apis-overview)kullanarak Microsoft 365 etkinlik günlüklerine programlı bir şekilde erişebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD denetim etkinliği başvurusu](reference-audit-activities.md)
- [Azure AD raporları bekletme başvurusu](reference-reports-data-retention.md)
- [Azure AD günlüğü gecikme süreleri başvurusu](reference-reports-latencies.md)