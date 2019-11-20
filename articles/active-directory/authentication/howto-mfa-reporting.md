---
title: Azure MFA için erişim ve kullanım raporları-Azure Active Directory
description: Bu, Azure Multi-Factor Authentication Feature-Reports 'un nasıl kullanılacağını açıklar.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb90fafb48be1d3389597c6188b0200743f90c3e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065970"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication raporları

Azure Multi-Factor Authentication, sizin ve kuruluşunuzun Azure portal aracılığıyla erişilebilen çeşitli raporlar sağlar. Aşağıdaki tabloda, kullanılabilir raporlar listelenmektedir:

| Rapor | Konum | Açıklama |
|:--- |:--- |:--- |
| Engellenen Kullanıcı Geçmişi | Azure AD > güvenliği > MFA > kullanıcıları engelle/engellemesini kaldır | Kullanıcıları engellemek veya engelini kaldırmak için isteklerin geçmişini gösterir. |
| Kullanım ve sahtekarlık uyarıları | Azure AD > oturum açma işlemleri | Genel kullanım, Kullanıcı Özeti ve kullanıcı ayrıntıları hakkında bilgi sağlar; Ayrıca, belirtilen tarih aralığı boyunca gönderilen sahtekarlık uyarılarının geçmişi. |
| Şirket içi bileşenler için kullanım | Azure AD > güvenliği > MFA > etkinlik raporu | NPS uzantısı, ADFS ve MFA sunucusu aracılığıyla MFA için genel kullanım hakkında bilgi sağlar. |
| Atlanan Kullanıcı Geçmişi | Azure AD > güvenliği > MFA > bir kerelik atlama | Bir kullanıcı için Multi-Factor Authentication atlama isteklerinin geçmişini sağlar. |
| Sunucu durumu | Azure AD > güvenliği > MFA > sunucu durumu | Hesabınızla ilişkili Multi-Factor Authentication sunucularının durumunu görüntüler. |

## <a name="view-mfa-reports"></a>MFA raporlarını görüntüleme

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol tarafta **Azure Active Directory** > **güvenlik** > **MFA**' yı seçin.
3. Görüntülemek istediğiniz raporu seçin.

   ![Azure portal MFA sunucu sunucusu durum raporu](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Azure AD oturum açma işlemleri raporu

[Azure Portal](https://portal.azure.com) **oturum açma etkinliği raporuyla** , ortamınızın nasıl çalıştığını öğrenmek için gereken bilgileri alabilirsiniz.

Oturum açma işlemleri raporu, çok faktörlü kimlik doğrulaması (MFA) kullanımı hakkında bilgi içeren, yönetilen uygulamaların kullanımı ve Kullanıcı oturum açma etkinlikleri hakkında bilgi sunabilir. MFA verileri MFA'nın kuruluşunuzdaki kullanımı hakkında öngörüler sunar. Aşağıdaki gibi sorulara yanıt bulmanızı sağlar:

- Oturum açma sırasında MFA kullanıldı mı?
- Kullanıcı MFA'yı nasıl tamamladı?
- Kullanıcının MFA'yı tamamlayamadığı durumlar oldu mu?
- Kaç kullanıcıdan MFA istendi?
- Kaç kullanıcı MFA isteğini tamamlayamadı?
- Son kullanıcıların karşılaştığı ortak MFA sorunları nelerdir?

Bu veriler [Azure Portal](https://portal.azure.com) ve [Raporlama API 'si](../reports-monitoring/concept-reporting-api.md)ile kullanılabilir.

![Azure portal Azure AD oturum açma işlemleri raporu](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Oturum açma raporu yapısı

MFA hakkındaki oturum açma etkinliği raporları aşağıdaki bilgilere erişmenizi sağlar:

**MFA gerekli:** Oturum açma işlemi için MFA'nın gerekli olup olmadığı. Kullanıcı başına MFA, koşullu erişim ya da başka nedenlerden dolayı MFA gerekli olabilir. Olası değerler **Evet** veya **Hayır**.

**MFA Sonucu:** MFA'nın tamamlanıp tamamlanmadığı hakkında daha fazla bilgi:

- MFA tamamlandıysa bu sütunda MFA'nın tamamlanma şekli hakkında daha fazla bilgi yer alır.
   - Azure Multi-Factor Authentication
      - bulutta tamamlandı
      - kiracıda yapılandırılmış ilkeler nedeniyle süresi doldu
      - kayıt istendi
      - belirteç talebiyle gerçekleştirildi
      - harici sağlayıcı tarafından sağlanan taleple gerçekleştirildi
      - güçlü kimlik doğrulamasıyla gerçekleştirildi
      - gerçekleştirilen akış Windows aracısı oturum açma akışı tarafı olduğundan atlandı
      - uygulama parolası nedeniyle atlandı
      - konum nedeniyle atlandı
      - kayıtlı cihaz nedeniyle atlandı
      - hatırlanan cihaz nedeniyle atlandı
      - başarıyla tamamlandı
   - Çok faktörlü kimlik doğrulaması için harici sağlayıcıya yönlendirildi

- MFA reddedildiyse bu sütunda reddedilme nedeni yer alır.
   - Azure Multi-Factor Authentication tarafından reddedildi;
      - kimlik doğrulaması devam ediyor
      - yinelenen kimlik doğrulaması girişimi
      - çok fazla kez yanlış kod girildi
      - geçersiz kimlik doğrulaması
      - geçersiz mobil uygulama doğrulama kodu
      - yanlış yapılandırma
      - telefon araması sesli mesaja düştü
      - telefon numarası biçimi geçersiz
      - hizmet hatası
      - kullanıcının telefonuna ulaşılamadı
      - cihaza mobil uygulama bildirimi gönderilemedi
      - mobil uygulama bildirimi gönderilemedi
      - kullanıcı kimlik doğrulamasını reddetti
      - kullanıcı mobil uygulama bildirimine yanıt vermedi
      - kullanıcının kayıtlı doğrulama yöntemi yok
      - kullanıcı hatalı kod girdi
      - kullanıcı hatalı PIN girdi
      - kullanıcı kimlik doğrulaması başarılı olmadan telefon aramasını sonlandırdı
      - kullanıcı engellendi
      - kullanıcı doğrulama kodunu hiç girmedi
      - kullanıcı bulunamadı
      - doğrulama kodu zaten bir kez kullanıldı

**MFA kimlik doğrulama yöntemi:** Kullanıcının MFA'yı tamamlaması için kullandığı kimlik doğrulama yöntemi. Olası değerler şunlardır:

- Kısa mesaj
- Mobil uygulama bildirimi
- Telefon araması (Kimlik doğrulama telefonu)
- Mobil uygulama doğrulama kodu
- Telefon araması (Ofis telefonu)
- Telefon araması (Alternatif kimlik doğrulama telefonu)

**MFA kimlik doğrulama ayrıntısı:** Telefon numarasının temizlenmiş sürümü. Örnek: +X XXXXXXXX64.

**Koşullu erişim** Aşağıdakiler dahil olmak üzere oturum açma girişimini etkileyen koşullu erişim ilkeleri hakkında bilgi edinin:

- İlke adı
- Atama denetimleri
- Oturum denetimleri
- Sonuç

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>MFA için kaydedilen kullanıcılarda PowerShell raporlaması

İlk olarak, [MSOnline v1 PowerShell modülünün](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) yüklü olduğundan emin olun.

Aşağıdaki PowerShell 'i kullanarak MFA için kaydolan kullanıcıları belirler.

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Aşağıdaki PowerShell kullanılarak MFA için kaydolmayan kullanıcıları belirler.

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>Etkinlik raporlarında olası sonuçlar

Multi-Factor Authentication etkinlik raporunun indirilen sürümünü kullanarak çok faktörlü kimlik doğrulaması sorunlarını gidermek için aşağıdaki tablo kullanılabilir. Bunlar doğrudan Azure portal görünmez.

| Çağrı sonucu | Açıklama | Geniş açıklama |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN girildi | Kullanıcı bir PIN girdi.  Kimlik doğrulaması başarılı olursa, doğru PIN 'ı girirler.  Kimlik doğrulaması reddedilirse, yanlış bir PIN girirler veya Kullanıcı standart moda ayarlanır. |
| SUCCESS_NO_PIN | Yalnızca # girildi | Kullanıcı PIN moduna ayarlanmışsa ve kimlik doğrulaması reddedilirse, bu, kullanıcının PIN 'ini girmediği ve yalnızca # olarak girdiği anlamına gelir.  Kullanıcı standart moda ayarlanmışsa ve kimlik doğrulaması başarılı olursa, Kullanıcı yalnızca standart modda yapılacak doğru şey olan # değeri olarak girilen bu anlamına gelir. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | Girişten sonra # tuşuna basılmadı | # Girilmediğinden Kullanıcı hiçbir DTMF almadı.  Girişin tamamlandığını belirten # girilmediği takdirde girilen diğer basamaklar gönderilmez. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Telefon girişi yok-zaman aşımına uğradı | Çağrı Yanıtlandı, ancak yanıt yoktu.  Bu genellikle çağrının sesli posta tarafından çekildiğini gösterir. |
| SUCCESS_PIN_EXPIRED | PIN 'in geçerliliği geçildi ve değiştirilmedi | Kullanıcının PIN 'inin kullanım zaman aşımına uğradı ve bunu değiştirmesi istenir, ancak PIN değişikliği başarıyla tamamlanamadı. |
| SUCCESS_USED_CACHE | Kullanılan önbellek | Yapılandırılmış önbellek zaman diliminde aynı Kullanıcı adı için önceki başarılı bir kimlik doğrulama işlemi olduğundan, kimlik doğrulama Multi-Factor Authentication çağrısı olmadan başarılı oldu. |
| SUCCESS_BYPASSED_AUTH | Atlanan kimlik doğrulaması | Kimlik doğrulaması, Kullanıcı için tek seferlik bir geçiş başlatıldı kullanılarak başarılı oldu.  Atlama hakkında daha fazla bilgi için Atlanan Kullanıcı Geçmişi raporuna bakın. |
| SUCCESS_USED_IP_BASED_CACHE | Kullanılan IP tabanlı önbellek | Yapılandırılan önbellek zaman diliminde aynı Kullanıcı adı, kimlik doğrulama türü, uygulama adı ve IP için bir önceki başarılı kimlik doğrulama işlemi başarısız olduğundan kimlik doğrulaması Multi-Factor Authentication çağrısı olmadan başarılı oldu. |
| SUCCESS_USED_APP_BASED_CACHE | Kullanılan uygulama tabanlı önbellek | Aynı Kullanıcı adı, kimlik doğrulama türü ve yapılandırılmış önbellek zaman çerçevesi içinde uygulama adı için önceki başarılı kimlik doğrulamasından bu yana kimlik doğrulaması Multi-Factor Authentication çağrısı olmadan başarılı oldu. |
| SUCCESS_INVALID_INPUT | Geçersiz telefon girişi | Telefondan gönderilen yanıt geçerli değil.  Bu, bir faks makinesinden veya modemden olabilir veya Kullanıcı PIN 'inin bir parçası olarak * girmiş olabilir. |
| SUCCESS_USER_BLOCKED | Kullanıcı engellendi | Kullanıcının telefon numarası engellendi.  Engellenen bir sayı, kimlik doğrulama araması sırasında veya Azure portal kullanan bir yönetici tarafından Kullanıcı tarafından başlatılabilir. <br> Note: Engellenen sayı ayrıca bir sahtekarlık uyarısında bir ürünüdür. |
| SUCCESS_SMS_AUTHENTICATED | Kısa mesaj kimliği doğrulandı | İki yönlü test iletisi için Kullanıcı, bir kerelik geçiş kodu (OTP) veya OTP + PIN ile doğru şekilde yanıt verdi. |
| SUCCESS_SMS_SENT | SMS mesajı gönderildi | Kısa mesaj için, tek seferlik geçiş kodu (OTP) içeren kısa mesaj başarıyla gönderildi.  Kullanıcı, kimlik doğrulamasını gerçekleştirmek için uygulamada OTP veya OTP + PIN 'ini girer. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Mobil uygulama kimliği doğrulandı | Kullanıcı, mobil uygulama aracılığıyla kimliği başarıyla doğrulandı. |
| SUCCESS_OATH_CODE_PENDING | OATH kodu bekliyor | Kullanıcıdan OATH kodu istendi, ancak yanıt vermedi. |
| SUCCESS_OATH_CODE_VERIFIED | OATH kodu doğrulandı | Kullanıcı istendiğinde geçerli bir OATH kodu girdiniz. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Geri dönüş OATH kodu doğrulandı | Kullanıcı, birincil Multi-Factor Authentication yöntemi kullanılarak kimlik doğrulamasını reddetti ve ardından geri dönüş için geçerli bir OATH kodu sağladı. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Geri dönüş güvenlik soruları yanıtlandı | Kullanıcı, birincil Multi-Factor Authentication yöntemi kullanılarak kimlik doğrulamasını reddetti ve sonra geri dönüş için güvenlik sorularını doğru bir şekilde yanıtladı. |
| FAILED_PHONE_BUSY | Kimlik doğrulama zaten devam ediyor | Multi-Factor Authentication, bu kullanıcı için bir kimlik doğrulamasını zaten işliyor.  Bu, genellikle aynı oturum açma sırasında birden fazla kimlik doğrulama isteği gönderen RADIUS istemcilerinden kaynaklanır. |
| CONFIG_ISSUE | Telefona ulaşılamıyor | Çağrı denendi, ancak yerleştirimedi ya da yanıtlanmadı.  Bu, meşgul sinyali, Hızlı meşgul sinyali (bağlantısı kesik), Üçlü tonlar (artık hizmette olmayan numara), çalan sırada zaman aşımına uğradığını içerir, vb. |
| FAILED_INVALID_PHONENUMBER | Geçersiz telefon numarası biçimi | Telefon numarası geçersiz bir biçime sahip.  Telefon numaraları sayısal olmalıdır ve ülke kodu + 1 (Birleşik Devletler & Kanada) için 10 basamak olmalıdır. |
| FAILED_USER_HUNGUP_ON_US | Kullanıcı telefonu kapattı | Kullanıcı telefonu yanıtladı, ancak herhangi bir düğmeye basmadan askıda kalmaz. |
| FAILED_INVALID_EXTENSION | Geçersiz uzantı | Uzantı geçersiz karakterler içeriyor.  Yalnızca rakamlar, virgüller, * ve # kullanılabilir.  Bir @ öneki de kullanılabilir. |
| FAILED_FRAUD_CODE_ENTERED | Sahtekarlık kodu girildi | Çağrı sırasında sahtekarlık raporlamak için tercih edilen Kullanıcı, reddedilen bir kimlik doğrulaması ve engellenen telefon numarası ile sonuçlanır.| 
| FAILED_SERVER_ERROR | Çağrı yerleştirilemiyor | Multi-Factor Authentication hizmeti çağrıyı yerleştiremedi. |
| FAILED_SMS_NOT_SENT | Kısa mesaj gönderilemedi | SMS mesajı gönderilemedi.  Kimlik doğrulaması reddedildi. |
| FAILED_SMS_OTP_INCORRECT | Kısa mesaj OTP yanlış | Kullanıcı, aldığı SMS iletisinden yanlış bir kerelik geçiş kodu (OTP) girmiştir.  Kimlik doğrulaması reddedildi. |
| FAILED_SMS_OTP_PIN_INCORRECT | Kısa mesaj OTP + PIN yanlış | Kullanıcı yanlış bir kerelik geçiş kodu (OTP) ve/veya yanlış Kullanıcı PIN kodu girdiniz.  Kimlik doğrulaması reddedildi. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Maksimum SMS Iletisi OTP denemesi aşıldı | Kullanıcı, en fazla bir kerelik geçiş kodu (OTP) denemesi sayısını aşmış. |
| FAILED_PHONE_APP_DENIED | Mobil uygulama reddedildi | Kullanıcı, Reddet düğmesine basarak mobil uygulamadaki kimlik doğrulamasını reddetti. |
| FAILED_PHONE_APP_INVALID_PIN | Mobil uygulama geçersiz PIN | Kullanıcı, mobil uygulamada kimlik doğrulanırken geçersiz bir PIN girdiniz. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Mobil uygulama PIN 'ı değiştirilmedi | Kullanıcı, mobil uygulamadaki gerekli PIN değişikliğini başarıyla tamamlayamadı. |
| FAILED_FRAUD_REPORTED | Sahtekarlık bildirildi | Kullanıcı, mobil uygulamada sahtekarlık bildirdi. |
| FAILED_PHONE_APP_NO_RESPONSE | Mobil uygulama yanıt yok | Kullanıcı mobil uygulama kimlik doğrulama isteğine yanıt vermedi. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Mobil uygulama tüm cihazlar engellendi | Bu kullanıcıya yönelik mobil uygulama cihazları artık bildirimlere yanıt vermiyor ve engellendi. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Mobil uygulama bildirimi başarısız oldu | Kullanıcının cihazında mobil uygulamaya bildirim gönderilmeye çalışılırken bir hata oluştu. |
| FAILED_PHONE_APP_INVALID_RESULT | Mobil uygulama geçersiz sonuç | Mobil uygulama geçersiz bir sonuç döndürdü. |
| FAILED_OATH_CODE_INCORRECT | OATH kodu yanlış | Kullanıcı yanlış bir OATH kodu girdiniz.  Kimlik doğrulaması reddedildi. |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH kodu + PIN yanlış | Kullanıcı yanlış bir OATH kodu ve/veya yanlış Kullanıcı PIN kodu girdiniz.  Kimlik doğrulaması reddedildi. |
| FAILED_OATH_CODE_DUPLICATE | Yinelenen OATH kodu | Kullanıcı daha önce kullanılan bir OATH kodu girdiniz.  Kimlik doğrulaması reddedildi. |
| FAILED_OATH_CODE_OLD | OATH kodu güncel değil | Kullanıcı daha önce kullanılan OATH kodundan önce gelen bir OATH kodu girdiniz.  Kimlik doğrulaması reddedildi. |
| FAILED_OATH_TOKEN_TIMEOUT | OATH kodu sonuç zaman aşımı | Kullanıcının OATH kodunu girmesi çok uzun sürdü ve Multi-Factor Authentication denemesi zaten zaman aşımına uğradı. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Güvenlik soruları sonuç zaman aşımı | Kullanıcının güvenlik sorularına yanıt girmesi çok uzun sürdü ve Multi-Factor Authentication denemesi zaten zaman aşımına uğradı. |
| FAILED_AUTH_RESULT_TIMEOUT | Kimlik doğrulama sonucu zaman aşımı | Kullanıcının Multi-Factor Authentication denemesini tamamlaması çok uzun sürdü. |
| FAILED_AUTHENTICATION_THROTTLED | Kimlik doğrulaması kısıtlandı | Multi-Factor Authentication denemesi, hizmet tarafından kısıtlandı. |

## <a name="next-steps"></a>Sonraki adımlar

* [SSPR ve MFA kullanımı ve öngörüleri raporlama](howto-authentication-methods-usage-insights.md)
* [Kullanıcılar için](../user-help/multi-factor-authentication-end-user.md)
* [Nereden dağıtılır](concept-mfa-whichversion.md)
