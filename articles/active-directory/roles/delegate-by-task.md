---
title: Yönetici görevine göre rol temsilcisi-Azure Active Directory | Microsoft Docs
description: Azure Active Directory kimlik görevleri için temsilci seçme rolleri
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70222f493e73c1b46dcb7dfdaedfa3ad2c30714a
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060269"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Azure Active Directory 'de yönetici görevine göre yönetici rolleri

Bu makalede, Azure Active Directory (Azure AD) ' de en az ayrıcalıklı rol atayarak bir kullanıcının yönetici izinlerini kısıtlamak için gereken bilgileri bulabilirsiniz. Özellik alanına göre düzenlenmiş yönetici görevleri ve her görevi gerçekleştirmek için gereken en az ayrıcalıklı rol, görevi gerçekleştirebilecek genel olmayan ek yönetici rolleriyle birlikte bulunur.

## <a name="application-proxy"></a>Uygulama proxy’si

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Uygulama proxy 'si uygulamasını yapılandırma | Uygulama Yöneticisi | 
Bağlayıcı grubu özelliklerini yapılandırma | Uygulama Yöneticisi | 
Tüm kullanıcılar için devre dışı bırakıldığında uygulama kaydı oluştur | Uygulama geliştiricisi | Bulut uygulaması Yöneticisi, uygulama Yöneticisi
Bağlayıcı grubu oluştur | Uygulama Yöneticisi | 
Bağlayıcı grubunu sil | Uygulama Yöneticisi | 
Uygulama ara sunucusunu devre dışı bırakma | Uygulama Yöneticisi | 
Bağlayıcı hizmetini indir | Uygulama Yöneticisi | 
Tüm yapılandırmaları Oku | Uygulama Yöneticisi | 

## <a name="external-identitiesb2c"></a>Dış kimlikler/B2C

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Azure AD B2C dizinleri oluşturma | Konuk olmayan tüm kullanıcılar ([bkz. belgeler](../fundamentals/users-default-permissions.md)) | 
B2C uygulamaları oluşturma | Genel Yönetici | 
Kurumsal uygulamalar oluşturma | Bulut Uygulaması Yöneticisi | Uygulama Yöneticisi
B2C ilkeleri oluşturun, okuyun, güncelleştirin ve silin | B2C ıEF Ilke Yöneticisi | 
Kimlik sağlayıcıları oluşturun, okuyun, güncelleştirin ve silin | Dış kimlik sağlayıcısı Yöneticisi | 
Parola sıfırlama Kullanıcı akışlarını oluşturma, okuma, güncelleştirme ve silme | Dış KIMLIK Kullanıcı akış Yöneticisi | 
Profil düzenlemesi Kullanıcı akışlarını oluşturma, okuma, güncelleştirme ve silme | Dış KIMLIK Kullanıcı akış Yöneticisi | 
Oturum açma Kullanıcı akışlarını oluşturma, okuma, güncelleştirme ve silme | Dış KIMLIK Kullanıcı akış Yöneticisi | 
Kaydolma Kullanıcı akışı oluşturun, okuyun, güncelleştirin ve silin |Dış KIMLIK Kullanıcı akış Yöneticisi | 
Kullanıcı özniteliklerini oluşturma, okuma, güncelleştirme ve silme | Dış KIMLIK Kullanıcı akışı öznitelik Yöneticisi | 
Kullanıcı oluşturun, okuyun, güncelleştirin ve silin | Kullanıcı Yöneticisi
Tüm yapılandırmaları Oku | Genel okuyucu | 
B2C denetim günlüklerini oku | Genel okuyucu ([bkz. belgeler](../../active-directory-b2c/faq.md)) | 

> [!NOTE]
> Azure AD B2C küresel okuyucular Azure AD Genel yöneticileriyle aynı izinlere sahip değildir. Azure AD B2C genel yönetici ayrıcalıklarına sahipseniz, bir Azure AD dizini değil, bir Azure AD B2C dizininde olduğunuzdan emin olun.

## <a name="company-branding"></a>Şirket markası

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Şirket markası yapılandırma | Genel Yönetici | 
Tüm yapılandırmaları Oku | Dizin okuyucuları | Varsayılan Kullanıcı rolü ([bkz. belgeler](../fundamentals/users-default-permissions.md))

## <a name="company-properties"></a>Şirket özellikleri

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Şirket özelliklerini yapılandırma | Genel Yönetici | 

## <a name="connect"></a>Bağlanma

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Geçiş kimlik doğrulaması | Genel Yönetici  | 
Tüm yapılandırmaları Oku | Genel okuyucu | Genel Yönetici  |
Kesintisiz çoklu oturum açma | Genel Yönetici  | 

## <a name="cloud-provisioning"></a>Bulut sağlama

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Geçiş kimlik doğrulaması | Karma kimlik yöneticisi  | 
Tüm yapılandırmaları Oku | Genel okuyucu | Karma kimlik yöneticisi  |
Kesintisiz çoklu oturum açma | Karma kimlik yöneticisi  | 

## <a name="connect-health"></a>Connect Health

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Hizmet ekleme veya silme | Sahip ([bkz. belgeler](../hybrid/how-to-connect-health-operations.md)) | 
Eşitleme hatasına düzeltmeleri Uygula | Katkıda bulunan ([bkz. belgeler](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Sahip
Bildirimleri yapılandırma | Katkıda bulunan ([bkz. belgeler](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Sahip
Ayarları yapılandırma | Sahip ([bkz. belgeler](../hybrid/how-to-connect-health-operations.md)) | 
Eşitleme bildirimlerini yapılandırma | Katkıda bulunan ([bkz. belgeler](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Sahip
ADFS güvenlik raporlarını oku | Güvenlik Okuyucusu | Katkıda bulunan, sahip
Tüm yapılandırmaları Oku | Okuyucu ([bkz. belgeler](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Katkıda bulunan, sahip
Okuma Eşitleme hataları | Okuyucu ([bkz. belgeler](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Katkıda bulunan, sahip
Eşitleme hizmetlerini oku | Okuyucu ([bkz. belgeler](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Katkıda bulunan, sahip
Ölçümleri ve uyarıları görüntüleme | Okuyucu ([bkz. belgeler](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Katkıda bulunan, sahip
Ölçümleri ve uyarıları görüntüleme | Okuyucu ([bkz. belgeler](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Katkıda bulunan, sahip
Eşitleme hizmeti ölçümlerini ve uyarılarını görüntüleme | Okuyucu ([bkz. belgeler](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Katkıda bulunan, sahip

## <a name="custom-domain-names"></a>Özel etki alanı adları

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Etki alanlarını yönetme | Etki alanı adı yöneticisi | 
Tüm yapılandırmaları Oku | Dizin okuyucuları | Varsayılan Kullanıcı rolü ([bkz. belgeler](../fundamentals/users-default-permissions.md))

## <a name="domain-services"></a>Etki Alanı Hizmetleri

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Azure AD Domain Services örneği oluştur | Genel Yönetici | 
Tüm Azure AD Domain Services görevlerini gerçekleştirin | Azure AD DC Administrators grubu ([belgelere bakın](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
Tüm yapılandırmaları Oku | Azure aboneliğinde AD DS hizmeti içeren okuyucu | 

## <a name="devices"></a>Cihazlar

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Cihazı devre dışı bırak | Bulut Cihaz Yöneticisi | 
Cihazı Etkinleştir | Bulut Cihaz Yöneticisi | 
Temel yapılandırmayı oku | Varsayılan Kullanıcı rolü ([bkz. belgeler](../fundamentals/users-default-permissions.md)) | 
BitLocker anahtarlarını oku | Güvenlik Okuyucusu | Parola Yöneticisi, Güvenlik Yöneticisi

## <a name="enterprise-applications"></a>Kurumsal uygulamalar

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Temsilci atanan izinlerin onayı | Bulut uygulaması Yöneticisi | Uygulama Yöneticisi
Uygulama izinlerinin onayı Microsoft Graph dahil değildir | Bulut uygulaması Yöneticisi | Uygulama Yöneticisi
Microsoft Graph için uygulama izinlerine onay | Ayrıcalıklı rol yöneticisi | 
Kendi verilerine erişen uygulamalara onay | Varsayılan Kullanıcı rolü ([bkz. belgeler](../fundamentals/users-default-permissions.md)) | 
Kurumsal uygulama oluştur | Bulut uygulaması Yöneticisi | Uygulama Yöneticisi
Uygulama proxy 'Sini yönetme | Uygulama Yöneticisi | 
Kullanıcı ayarlarını yönetme | Genel Yönetici | 
Bir grubun veya uygulamanın okuma erişimi incelemesi | Güvenlik Okuyucusu | Güvenlik Yöneticisi, Kullanıcı Yöneticisi
Tüm yapılandırmaları Oku | Varsayılan Kullanıcı rolü ([bkz. belgeler](../fundamentals/users-default-permissions.md)) | 
Kurumsal uygulama atamalarını güncelleştirme | Kurumsal uygulama sahibi ([belgelere bakın](../fundamentals/users-default-permissions.md)) | Bulut uygulaması Yöneticisi, uygulama Yöneticisi
Kurumsal uygulama sahiplerini güncelleştirme | Kurumsal uygulama sahibi ([belgelere bakın](../fundamentals/users-default-permissions.md)) | Bulut uygulaması Yöneticisi, uygulama Yöneticisi
Kurumsal uygulama özelliklerini güncelleştir | Kurumsal uygulama sahibi ([belgelere bakın](../fundamentals/users-default-permissions.md)) | Bulut uygulaması Yöneticisi, uygulama Yöneticisi
Kurumsal uygulama sağlamayı Güncelleştir | Kurumsal uygulama sahibi ([belgelere bakın](../fundamentals/users-default-permissions.md)) | Bulut uygulaması Yöneticisi, uygulama Yöneticisi
Kurumsal uygulama Self Servis güncelleştirme | Kurumsal uygulama sahibi ([belgelere bakın](../fundamentals/users-default-permissions.md)) | Bulut uygulaması Yöneticisi, uygulama Yöneticisi
Çoklu oturum açma özelliklerini güncelleştir | Kurumsal uygulama sahibi ([belgelere bakın](../fundamentals/users-default-permissions.md)) | Bulut uygulaması Yöneticisi, uygulama Yöneticisi

## <a name="entitlement-management"></a>Yetkilendirme yönetimi
Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Kataloğa kaynak ekleme | Kullanıcı yöneticisi | Yetkilendirme Yönetimi sayesinde bu görevi Katalog sahibine devredebilir ([bkz. belgeler](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners))
Kataloğa SharePoint Online siteleri ekleme | Genel yönetici


## <a name="groups"></a>Gruplar

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Lisans atama | Kullanıcı yöneticisi | 
Grup oluşturma | Grup Yöneticisi | Kullanıcı yöneticisi
Bir grup veya uygulama için erişim gözden geçirmesi oluşturma, güncelleştirme veya silme | Kullanıcı yöneticisi | 
Grup süre sonunu yönetme | Kullanıcı yöneticisi | 
Grup ayarlarını yönetme | Grup Yöneticisi | Kullanıcı Yöneticisi | 
Tüm yapılandırmayı oku (gizli üyelik hariç) | Dizin okuyucuları | Varsayılan Kullanıcı rolü ([bkz. belgeler](../fundamentals/users-default-permissions.md))
Gizli üyeliği oku | Grup üyesi | Grup sahibi, parola Yöneticisi, Exchange Yöneticisi, SharePoint Yöneticisi, takımlar Yöneticisi, Kullanıcı Yöneticisi
Gizli üyelikle grupların üyeliğini oku | Yardım Masası Yöneticisi | Kullanıcı Yöneticisi, takımlar Yöneticisi
Lisansı iptal et | Lisans yöneticisi | Kullanıcı yöneticisi
Grup üyeliğini Güncelleştir | Grup sahibi ([bkz. belgeler](../fundamentals/users-default-permissions.md)) | Kullanıcı yöneticisi
Grup sahiplerini güncelleştirme | Grup sahibi ([bkz. belgeler](../fundamentals/users-default-permissions.md)) | Kullanıcı yöneticisi
Güncelleştirme grubu özellikleri | Grup sahibi ([bkz. belgeler](../fundamentals/users-default-permissions.md)) | Kullanıcı yöneticisi
Grubu silme | Grup Yöneticisi | Kullanıcı yöneticisi

## <a name="identity-protection"></a>Kimlik Koruması

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Uyarı bildirimlerini yapılandırma| Güvenlik Yöneticisi | 
MFA ilkesini yapılandırma ve etkinleştirme veya devre dışı bırakma| Güvenlik Yöneticisi | 
Oturum açma risk ilkesini yapılandırma ve etkinleştirme veya devre dışı bırakma| Güvenlik Yöneticisi | 
Kullanıcı risk ilkesini yapılandırma ve etkinleştirme veya devre dışı bırakma | Güvenlik Yöneticisi | 
Haftalık özetleri yapılandırma | Güvenlik Yöneticisi| 
Tüm risk algılamalarını kapat | Güvenlik Yöneticisi | 
Güvenlik açığı 'nı onarın veya kapatın | Güvenlik Yöneticisi | 
Tüm yapılandırmaları Oku | Güvenlik Okuyucusu | 
Tüm risk algılamalarını okuyun | Güvenlik Okuyucusu | 
Güvenlik açıklarını okuyun | Güvenlik Okuyucusu | 

## <a name="licenses"></a>Lisanslar

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Lisans atama | Lisans yöneticisi | Kullanıcı yöneticisi
Tüm yapılandırmaları Oku | Dizin okuyucuları | Varsayılan Kullanıcı rolü ([bkz. belgeler](../fundamentals/users-default-permissions.md))
Lisansı iptal et | Lisans yöneticisi | Kullanıcı yöneticisi
Abonelik deneyin veya satın alın | Faturalama yöneticisi | 


## <a name="monitoring---audit-logs"></a>İzleme-denetim günlükleri

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Denetim günlüklerini oku | Rapor okuyucu | Güvenlik okuyucusu, Güvenlik Yöneticisi

## <a name="monitoring---sign-ins"></a>İzleme-oturum açma işlemleri

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Oturum açma günlüklerini oku | Rapor okuyucu | Güvenlik okuyucusu, Güvenlik Yöneticisi

## <a name="multi-factor-authentication"></a>Multi-factor authentication

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Seçilen kullanıcılar tarafından oluşturulan mevcut tüm uygulama parolalarını Sil | Genel Yönetici | 
MFA 'yı devre dışı bırak | Kimlik doğrulama Yöneticisi (PowerShell aracılığıyla) | Ayrıcalıklı kimlik doğrulama Yöneticisi (PowerShell aracılığıyla)
MFA’yı etkinleştirme | Kimlik doğrulama Yöneticisi (PowerShell aracılığıyla) | Ayrıcalıklı kimlik doğrulama Yöneticisi (PowerShell aracılığıyla) 
MFA hizmeti ayarlarını yönetme | Kimlik doğrulama Ilkesi Yöneticisi | 
Seçilen kullanıcıların iletişim yöntemlerini yeniden sağlamasını gerektir | Kimlik doğrulama Yöneticisi | 
Tüm hatırlanan cihazlarda Multi-Factor Authentication 'ı geri yükleme  | Kimlik doğrulama Yöneticisi | 

## <a name="mfa-server"></a>MFA Sunucusu

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Kullanıcıları engelle/engelini kaldır | Kimlik doğrulama Ilkesi Yöneticisi | 
Hesap kilitlemeyi yapılandırma | Kimlik doğrulama Ilkesi Yöneticisi | 
Önbelleğe alma kurallarını yapılandırma | Kimlik doğrulama Ilkesi Yöneticisi | 
Sahtekarlık uyarısını yapılandırma | Kimlik doğrulama Ilkesi Yöneticisi
Bildirimleri yapılandırma | Kimlik doğrulama Ilkesi Yöneticisi | 
Bir kerelik geçişi yapılandırma | Kimlik doğrulama Ilkesi Yöneticisi | 
Telefon araması ayarlarını yapılandırma | Kimlik doğrulama Ilkesi Yöneticisi | 
Sağlayıcıları yapılandırma | Kimlik doğrulama Ilkesi Yöneticisi | 
Sunucu ayarlarını yapılandırma | Kimlik doğrulama Ilkesi Yöneticisi | 
Etkinlik raporunu oku | Genel okuyucu | 
Tüm yapılandırmaları Oku | Genel okuyucu | 
Sunucu durumunu oku | Genel okuyucu |  

## <a name="organizational-relationships"></a>Kuruluş ilişkileri

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Kimlik sağlayıcılarını Yönet | Dış kimlik sağlayıcısı Yöneticisi | 
Ayarları yönetme | Genel Yönetici | 
Kullanım koşullarını yönetme | Genel Yönetici | 
Tüm yapılandırmaları Oku | Genel okuyucu | 

## <a name="password-reset"></a>Parola sıfırlama

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Kimlik doğrulama yöntemlerini yapılandırma | Genel Yönetici |
Özelleştirmeyi Yapılandır | Genel Yönetici |
Bildirimi Yapılandır | Genel Yönetici |
Şirket içi tümleştirmeyi yapılandırma | Genel Yönetici |
Parola sıfırlama özelliklerini yapılandırma | Kullanıcı Yöneticisi | Genel Yönetici
Kayıt yapılandırma | Genel Yönetici |
Tüm yapılandırmaları Oku | Güvenlik Yöneticisi | Kullanıcı Yöneticisi |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Rollere kullanıcı atama | Ayrıcalıklı rol yöneticisi | 
Rol ayarlarını yapılandırma | Ayrıcalıklı rol yöneticisi | 
Denetim etkinliğini görüntüle | Güvenlik okuyucusu | 
Rol üyeliklerini görüntüleme | Güvenlik okuyucusu | 

## <a name="roles-and-administrators"></a>Roller ve yöneticiler

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Rol atamalarını yönetme | Ayrıcalıklı rol yöneticisi | 
Azure AD rolünün okuma erişimi incelemesi  | Güvenlik Okuyucusu | Güvenlik Yöneticisi, ayrıcalıklı rol yöneticisi
Tüm yapılandırmaları Oku | Varsayılan Kullanıcı rolü ([bkz. belgeler](../fundamentals/users-default-permissions.md)) | 

## <a name="security---authentication-methods"></a>Güvenlik-kimlik doğrulama yöntemleri

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Kimlik doğrulama yöntemlerini yapılandırma | Genel Yönetici | 
Parola korumasını yapılandırma | Güvenlik yöneticisi
Akıllı kilitlemeyi yapılandırma | Güvenlik yöneticisi
Tüm yapılandırmaları Oku | Genel okuyucu | 

## <a name="security---conditional-access"></a>Güvenlik-koşullu erişim

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
MFA güvenilir IP adreslerini yapılandırma | Koşullu Erişim Yöneticisi | 
Özel denetimler oluşturma | Koşullu Erişim Yöneticisi | Güvenlik yöneticisi
Adlandırılmış konumlar oluştur | Koşullu Erişim Yöneticisi | Güvenlik yöneticisi
İlkeleri oluşturma | Koşullu Erişim Yöneticisi | Güvenlik yöneticisi
Kullanım koşulları oluşturma | Koşullu Erişim Yöneticisi | Güvenlik yöneticisi
VPN bağlantısı sertifikası oluştur | Koşullu Erişim Yöneticisi | Güvenlik yöneticisi
Klasik ilkeyi Sil | Koşullu Erişim Yöneticisi | Güvenlik yöneticisi
Kullanım koşullarını Sil | Koşullu Erişim Yöneticisi | Güvenlik yöneticisi
VPN bağlantısı sertifikasını Sil | Koşullu Erişim Yöneticisi | Güvenlik yöneticisi
Klasik ilkeyi devre dışı bırak | Koşullu Erişim Yöneticisi | Güvenlik yöneticisi
Özel denetimleri yönetme | Koşullu Erişim Yöneticisi | Güvenlik yöneticisi
Adlandırılmış konumları Yönet | Koşullu Erişim Yöneticisi | Güvenlik yöneticisi
Kullanım koşullarını yönetme | Koşullu Erişim Yöneticisi | Güvenlik yöneticisi
Tüm yapılandırmaları Oku | Güvenlik okuyucusu | Güvenlik yöneticisi
Adlandırılmış konumları Oku | Güvenlik okuyucusu | Koşullu Erişim Yöneticisi, Güvenlik Yöneticisi

## <a name="security---identity-security-score"></a>Güvenlik kimliği güvenlik puanı

Görev | En az ayrıcalıklı rol | Ek roller | 
---- | --------------------- | ----------------
Tüm yapılandırmaları Oku | Güvenlik okuyucusu | Güvenlik yöneticisi
Güvenlik Puanını oku | Güvenlik okuyucusu | Güvenlik yöneticisi
Olay durumunu güncelleştir | Güvenlik yöneticisi | 

## <a name="security---risky-sign-ins"></a>Güvenlik riskli oturum açma işlemleri

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Tüm yapılandırmaları Oku | Güvenlik Okuyucusu | 
Riskli oturum açma işlemlerini okuyun | Güvenlik Okuyucusu | 

## <a name="security---users-flagged-for-risk"></a>Güvenlik-risk için işaretlenen kullanıcılar

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Tüm olayları kapatabilirsiniz. | Güvenlik Yöneticisi | 
Tüm yapılandırmaları Oku | Güvenlik Okuyucusu | 
Risk için işaretlenen kullanıcıları okuyun | Güvenlik Okuyucusu | 

## <a name="users"></a>Kullanıcılar

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Dizin rolüne kullanıcı ekleme | Ayrıcalıklı rol yöneticisi | 
Gruba Kullanıcı Ekle | Kullanıcı yöneticisi | 
Lisans atama | Lisans yöneticisi | Kullanıcı yöneticisi
Konuk kullanıcı oluştur | Konuk davetci | Kullanıcı yöneticisi
Kullanıcı oluştur | Kullanıcı yöneticisi | 
Kullanıcıları silme | Kullanıcı yöneticisi | 
Sınırlı yöneticilerin yenileme belirteçlerini geçersiz kılar (bkz. belgeler) | Kullanıcı yöneticisi | 
Yönetici olmayanlar için yenileme belirteçlerini geçersiz kıl (belgelere bakın) | Parola yöneticisi | Kullanıcı yöneticisi
Ayrıcalıklı yöneticilerin yenileme belirteçlerini geçersiz kıl (belgelere bakın) | Ayrıcalıklı kimlik doğrulama Yöneticisi | 
Temel yapılandırmayı oku | Varsayılan Kullanıcı rolü ([bkz. belgeler](../fundamentals/users-default-permissions.md) | 
Sınırlı yöneticiler için parolayı sıfırlama (bkz. belgeler) | Kullanıcı yöneticisi | 
Yönetici olmayanlar parolasını sıfırlama (belgelere bakın) | Parola yöneticisi | Kullanıcı yöneticisi
Ayrıcalıklı yöneticilerin parolasını sıfırlama | Ayrıcalıklı kimlik doğrulama Yöneticisi | 
Lisansı iptal et | Lisans yöneticisi | Kullanıcı yöneticisi
Kullanıcı asıl adı dışındaki tüm özellikleri Güncelleştir | Kullanıcı yöneticisi | 
Sınırlı yöneticiler için Kullanıcı asıl adını güncelleştirme (bkz. belgeler) | Kullanıcı yöneticisi | 
Ayrıcalıklı Yöneticiler üzerinde Kullanıcı asıl adı özelliğini güncelleştir (belgelere bakın) | Genel Yönetici | 
Kullanıcı ayarlarını Güncelleştir | Genel Yönetici | 
Kimlik doğrulama yöntemlerini güncelleştirme | Kimlik doğrulama Yöneticisi | Ayrıcalıklı kimlik doğrulama Yöneticisi, genel yönetici


## <a name="support"></a>Destek

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Destek bileti gönder | Hizmet Yöneticisi | Uygulama Yöneticisi, Azure Information Protection yönetici, faturalandırma Yöneticisi, bulut uygulama Yöneticisi, uyumluluk Yöneticisi, Dynamics 365 Yöneticisi, masaüstü Analizi Yöneticisi, Exchange Yöneticisi, parola Yöneticisi, Intune Yöneticisi, Skype Kurumsal Yöneticisi, Power BI Yöneticisi, ayrıcalıklı kimlik doğrulama Yöneticisi, SharePoint Yöneticisi, takımlar Iletişim Yöneticisi, takımlar Yöneticisi, Kullanıcı Yöneticisi, çalışma alanı analizi Yöneticisi

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD yönetici rolleri atama veya kaldırma](manage-roles-portal.md)
* [Azure AD yönetici rolleri başvurusu](permissions-reference.md)
