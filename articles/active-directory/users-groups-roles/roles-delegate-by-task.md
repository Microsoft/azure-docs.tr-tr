---
title: Yönetici görevine göre rol temsilcisi-Azure Active Directory | Microsoft Docs
description: Azure Active Directory kimlik görevleri için temsilci seçme rolleri
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 05/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ccd98553b06ef3fcd2241c8223889c0a86e331
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024665"
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

## <a name="b2c"></a>B2C

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Azure AD B2C dizinleri oluşturma | Konuk olmayan tüm kullanıcılar ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
B2C uygulamaları oluşturma | Genel Yönetici | 
Kurumsal uygulamalar oluşturma | Bulut uygulaması Yöneticisi | Uygulama Yöneticisi
B2C ilkeleri oluşturun, okuyun, güncelleştirin ve silin | Genel Yönetici | 
Kimlik sağlayıcıları oluşturun, okuyun, güncelleştirin ve silin | Genel Yönetici | 
Parola sıfırlama Kullanıcı akışlarını oluşturma, okuma, güncelleştirme ve silme | Genel Yönetici | 
Profil düzenlemesi Kullanıcı akışlarını oluşturma, okuma, güncelleştirme ve silme | Genel Yönetici | 
Oturum açma Kullanıcı akışlarını oluşturma, okuma, güncelleştirme ve silme | Genel Yönetici | 
Kaydolma Kullanıcı akışı oluşturun, okuyun, güncelleştirin ve silin |Genel Yönetici | 
Kullanıcı özniteliklerini oluşturma, okuma, güncelleştirme ve silme | Genel Yönetici | 
Kullanıcı oluşturun, okuyun, güncelleştirin ve silin | Genel yönetici ([belgelere bakın](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs))
Tüm yapılandırmaları Oku | Genel Yönetici | 
B2C denetim günlüklerini oku | Genel yönetici ([belgelere bakın](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

> [!NOTE]
> Azure AD B2C Genel Yöneticiler Azure AD Genel yöneticileriyle aynı izinlere sahip değildir. Azure AD B2C genel yönetici ayrıcalıklarına sahipseniz, bir Azure AD dizini değil, bir Azure AD B2C dizininde olduğunuzdan emin olun.

## <a name="company-branding"></a>Şirket markası

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Şirket markası yapılandırma | Genel Yönetici | 
Tüm yapılandırmaları Oku | Dizin okuyucuları | Varsayılan Kullanıcı rolü ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Şirket özellikleri

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Şirket özelliklerini yapılandırma | Genel Yönetici | 

## <a name="connect"></a>Bağlan

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Geçiş kimlik doğrulaması | Genel Yönetici | 
Tüm yapılandırmaları Oku | Genel Yönetici | 
Sorunsuz çoklu oturum açma | Genel Yönetici | 

## <a name="connect-health"></a>Connect Health

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Hizmet ekleme veya silme | Sahip ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Eşitleme hatasına düzeltmeleri Uygula | Katkıda bulunan ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Sahip
Bildirimleri yapılandırma | Katkıda bulunan ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Sahip
Ayarları yapılandırma | Sahip ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Eşitleme bildirimlerini yapılandırma | Katkıda bulunan ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Sahip
ADFS güvenlik raporlarını oku | Güvenlik okuyucusu | Katkıda bulunan, sahip
Tüm yapılandırmaları Oku | Okuyucu ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Katkıda bulunan, sahip
Okuma Eşitleme hataları | Okuyucu ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Katkıda bulunan, sahip
Eşitleme hizmetlerini oku | Okuyucu ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Katkıda bulunan, sahip
Ölçümleri ve uyarıları görüntüleme | Okuyucu ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Katkıda bulunan, sahip
Ölçümleri ve uyarıları görüntüleme | Okuyucu ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Katkıda bulunan, sahip
Eşitleme hizmeti ölçümlerini ve uyarılarını görüntüleme | Okuyucu ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Katkıda bulunan, sahip


## <a name="custom-domain-names"></a>Özel etki alanı adları

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Etki alanlarını yönetme | Genel Yönetici | 
Tüm yapılandırmaları Oku | Dizin okuyucuları | Varsayılan Kullanıcı rolü ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Etki Alanı Hizmetleri

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Azure AD Domain Services örneği oluştur | Genel Yönetici | 
Tüm Azure AD Domain Services görevlerini gerçekleştirin | Azure AD DC Administrators grubu ([belgelere bakın](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain)) | 
Tüm yapılandırmaları Oku | Azure aboneliğinde AD DS hizmeti içeren okuyucu | 

## <a name="devices"></a>Cihazlar

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Cihazı devre dışı bırak | Bulut Cihaz Yöneticisi | 
Cihazı Etkinleştir | Bulut Cihaz Yöneticisi | 
Temel yapılandırmayı oku | Varsayılan Kullanıcı rolü ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
BitLocker anahtarlarını oku | Güvenlik okuyucusu | Parola Yöneticisi, Güvenlik Yöneticisi

## <a name="enterprise-applications"></a>Kurumsal uygulamalar

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Temsilci atanan izinlerin onayı | Bulut uygulaması Yöneticisi | Uygulama Yöneticisi
Uygulama izinlerine onay Microsoft Graph veya Azure AD Graph dahil değildir | Bulut uygulaması Yöneticisi | Uygulama Yöneticisi
Microsoft Graph veya Azure AD grafiğine uygulama izinleri onayı | Genel Yönetici | 
Kendi verilerine erişen uygulamalara onay | Varsayılan Kullanıcı rolü ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Kurumsal uygulama oluştur | Bulut uygulaması Yöneticisi | Uygulama Yöneticisi
Uygulama proxy 'Sini yönetme | Uygulama Yöneticisi | 
Kullanıcı ayarlarını yönetme | Genel Yönetici | 
Bir grubun veya uygulamanın okuma erişimi incelemesi | Güvenlik okuyucusu | Güvenlik Yöneticisi, Kullanıcı Yöneticisi
Tüm yapılandırmaları Oku | Varsayılan Kullanıcı rolü ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Kurumsal uygulama atamalarını güncelleştirme | Kurumsal uygulama sahibi ([belgelere bakın](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Bulut uygulaması Yöneticisi, uygulama Yöneticisi
Kurumsal uygulama sahiplerini güncelleştirme | Kurumsal uygulama sahibi ([belgelere bakın](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Bulut uygulaması Yöneticisi, uygulama Yöneticisi
Kurumsal uygulama özelliklerini güncelleştir | Kurumsal uygulama sahibi ([belgelere bakın](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Bulut uygulaması Yöneticisi, uygulama Yöneticisi
Kurumsal uygulama sağlamayı Güncelleştir | Kurumsal uygulama sahibi ([belgelere bakın](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Bulut uygulaması Yöneticisi, uygulama Yöneticisi
Kurumsal uygulama Self Servis güncelleştirme | Kurumsal uygulama sahibi ([belgelere bakın](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Bulut uygulaması Yöneticisi, uygulama Yöneticisi
Çoklu oturum açma özelliklerini güncelleştir | Kurumsal uygulama sahibi ([belgelere bakın](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Bulut uygulaması Yöneticisi, uygulama Yöneticisi

## <a name="entitlement-management"></a>Yetkilendirme yönetimi
Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Kataloğa kaynak ekleme | Kullanıcı Yöneticisi | Yetkilendirme Yönetimi sayesinde bu görevi Katalog sahibine devredebilir ([bkz. belgeler](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners))
Kataloğa SharePoint Online siteleri ekleme | Genel yönetici


## <a name="groups"></a>Gruplar

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Lisans ata | Kullanıcı Yöneticisi | 
Grup Oluştur | Kullanıcı Yöneticisi | 
Bir grup veya uygulama için erişim gözden geçirmesi oluşturma, güncelleştirme veya silme | Kullanıcı Yöneticisi | 
Grup süre sonunu yönetme | Kullanıcı Yöneticisi | 
Grup ayarlarını yönetme | Genel Yönetici | 
Tüm yapılandırmayı oku (gizli üyelik hariç) | Dizin okuyucuları | Varsayılan Kullanıcı rolü ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Gizli üyeliği oku | Grup üyesi | Grup sahibi, parola Yöneticisi, Exchange Yöneticisi, SharePoint Yöneticisi, takımlar Yöneticisi, Kullanıcı Yöneticisi
Gizli üyelikle grupların üyeliğini oku | Yardım Masası Yöneticisi | Kullanıcı Yöneticisi, takımlar Yöneticisi
Lisansı iptal et | Lisans Yöneticisi | Kullanıcı Yöneticisi
Grup üyeliğini Güncelleştir | Grup sahibi ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Kullanıcı Yöneticisi
Grup sahiplerini güncelleştirme | Grup sahibi ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Kullanıcı Yöneticisi
Güncelleştirme grubu özellikleri | Grup sahibi ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Kullanıcı Yöneticisi

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
Tüm yapılandırmaları Oku | Güvenlik okuyucusu | 
Tüm risk algılamalarını okuyun | Güvenlik okuyucusu | 
Güvenlik açıklarını okuyun | Güvenlik okuyucusu | 

## <a name="licenses"></a>Lisanslarının

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Lisans ata | Lisans Yöneticisi | Kullanıcı Yöneticisi
Tüm yapılandırmaları Oku | Dizin okuyucuları | Varsayılan Kullanıcı rolü ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Lisansı iptal et | Lisans Yöneticisi | Kullanıcı Yöneticisi
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
MFA 'yı devre dışı bırak | Genel Yönetici | 
MFA’yı etkinleştirme | Genel Yönetici | 
MFA hizmeti ayarlarını yönetme | Genel Yönetici | 
Seçilen kullanıcıların iletişim yöntemlerini yeniden sağlamasını gerektir | Kimlik doğrulama Yöneticisi | 
Tüm hatırlanan cihazlarda Multi-Factor Authentication 'ı geri yükleme  | Kimlik doğrulama Yöneticisi | 

## <a name="mfa-server"></a>MFA Sunucusu

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Kullanıcıları engelle/engelini kaldır | Genel Yönetici | 
Hesap kilitlemeyi yapılandırma | Genel Yönetici | 
Önbelleğe alma kurallarını yapılandırma | Genel Yönetici | 
Sahtekarlık uyarısını yapılandırma | Genel Yönetici
Bildirimleri yapılandırma | Genel Yönetici | 
Bir kerelik geçişi yapılandırma | Genel Yönetici | 
Telefon araması ayarlarını yapılandırma | Genel Yönetici | 
Sağlayıcıları yapılandırma | Genel Yönetici | 
Sunucu ayarlarını yapılandırma | Genel Yönetici | 
Etkinlik raporunu oku | Genel Yönetici | 
Tüm yapılandırmaları Oku | Genel Yönetici | 
Sunucu durumunu oku | Genel Yönetici |  

## <a name="organizational-relationships"></a>Kuruluş ilişkileri

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Kimlik sağlayıcılarını Yönet | Genel Yönetici | 
Ayarları yönetme | Genel Yönetici | 
Kullanım koşullarını yönetme | Genel Yönetici | 
Tüm yapılandırmaları Oku | Genel Yönetici | 

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

## <a name="privileged-identity-management"></a>Ayrıcalıklı kimlik yönetimi

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Rollere kullanıcı atama | Ayrıcalıklı rol yöneticisi | 
Rol ayarlarını yapılandırma | Ayrıcalıklı rol yöneticisi | 
Denetim etkinliğini görüntüle | Güvenlik okuyucusu | 
Rol üyeliklerini görüntüleme | Güvenlik okuyucusu | 

## <a name="roles-and-administrators"></a>Roller ve Yöneticiler

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Rol atamalarını yönetme | Ayrıcalıklı rol yöneticisi | 
Azure AD rolünün okuma erişimi incelemesi  | Güvenlik okuyucusu | Güvenlik Yöneticisi, ayrıcalıklı rol yöneticisi
Tüm yapılandırmaları Oku | Varsayılan Kullanıcı rolü ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Güvenlik-kimlik doğrulama yöntemleri

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Kimlik doğrulama yöntemlerini yapılandırma | Genel Yönetici | 
Tüm yapılandırmaları Oku | Genel Yönetici | 

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
Tüm yapılandırmaları Oku | Güvenlik okuyucusu | 
Riskli oturum açma işlemlerini okuyun | Güvenlik okuyucusu | 

## <a name="security---users-flagged-for-risk"></a>Güvenlik-risk için işaretlenen kullanıcılar

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Tüm olayları kapatabilirsiniz. | Güvenlik Yöneticisi | 
Tüm yapılandırmaları Oku | Güvenlik okuyucusu | 
Risk için işaretlenen kullanıcıları okuyun | Güvenlik okuyucusu | 

## <a name="users"></a>Kullanıcılar

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Dizin rolüne kullanıcı ekleme | Ayrıcalıklı rol yöneticisi | 
Gruba Kullanıcı Ekle | Kullanıcı Yöneticisi | 
Lisans ata | Lisans Yöneticisi | Kullanıcı Yöneticisi
Konuk kullanıcı oluştur | Konuk davetci | Kullanıcı Yöneticisi
Kullanıcı oluştur | Kullanıcı Yöneticisi | 
Kullanıcıları Sil | Kullanıcı Yöneticisi | 
Sınırlı yöneticilerin yenileme belirteçlerini geçersiz kılar (bkz. belgeler) | Kullanıcı Yöneticisi | 
Yönetici olmayanlar için yenileme belirteçlerini geçersiz kıl (belgelere bakın) | Parola Yöneticisi | Kullanıcı Yöneticisi
Ayrıcalıklı yöneticilerin yenileme belirteçlerini geçersiz kıl (belgelere bakın) | Genel Yönetici | 
Temel yapılandırmayı oku | Varsayılan Kullanıcı rolü ([bkz. belgeler](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Sınırlı yöneticiler için parolayı sıfırlama (bkz. belgeler) | Kullanıcı Yöneticisi | 
Yönetici olmayanlar parolasını sıfırlama (belgelere bakın) | Parola Yöneticisi | Kullanıcı Yöneticisi
Ayrıcalıklı yöneticilerin parolasını sıfırlama | Genel Yönetici | 
Lisansı iptal et | Lisans Yöneticisi | Kullanıcı Yöneticisi
Kullanıcı asıl adı dışındaki tüm özellikleri Güncelleştir | Kullanıcı Yöneticisi | 
Sınırlı yöneticiler için Kullanıcı asıl adını güncelleştirme (bkz. belgeler) | Kullanıcı Yöneticisi | 
Ayrıcalıklı Yöneticiler üzerinde Kullanıcı asıl adı özelliğini güncelleştir (belgelere bakın) | Genel Yönetici | 
Kullanıcı ayarlarını Güncelleştir | Genel Yönetici | 


## <a name="support"></a>Destek

Görev | En az ayrıcalıklı rol | Ek roller
---- | --------------------- | ----------------
Destek bileti gönder | Hizmet Yöneticisi | Uygulama Yöneticisi, Azure Information Protection Yöneticisi, faturalandırma Yöneticisi, bulut uygulama Yöneticisi, uyumluluk Yöneticisi, Dynamics 365 Yöneticisi, masaüstü Analizi Yöneticisi, Exchange Yöneticisi, parola Yönetici, Intune Yöneticisi, Skype Kurumsal Yöneticisi, Power BI Yöneticisi, ayrıcalıklı kimlik doğrulama Yöneticisi, SharePoint Yöneticisi, takımlar Iletişim Yöneticisi, takımlar Yöneticisi, Kullanıcı Yöneticisi, Çalışma alanı analizi Yöneticisi

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD yönetici rolleri atama veya kaldırma](directory-manage-roles-portal.md)
* [Azure AD yönetici rolleri başvurusu](directory-assign-admin-roles.md)
