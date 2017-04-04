---
title: "Başlarken: Azure AD Parola yönetimi | Microsoft Docs"
description: "Kullanıcıların kendi parolalarını sıfırlamasına, parola sıfırlama önkoşullarını öğrenmesine ve Active Directory&quot;deki şirket içi parolaları yönetmek için parola geri yazma özelliğini etkinleştirmesine olanak tanıyın."
services: active-directory
keywords: "Active directory parola yönetimi, parola yönetimi, Azure AD parolasını sıfırlama"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: c2c46637ccccd01c1c3056d6a25ef605cfd68f2d
ms.lasthandoff: 03/28/2017


---
# <a name="getting-started-with-password-management"></a>Parola Yönetimine Başlarken
> [!IMPORTANT]
> **Oturum açmada sorun yaşadığınız için mi buradasınız?** Sorun yaşıyorsanız bkz. [kendi parolanızı değiştirme ve sıfırlama](active-directory-passwords-update-your-own-password.md#reset-your-password).
>
>

Kullanıcıların kendi bulut Azure Active Directory veya şirket içi Active Directory parolalarını yönetmesine olanak tanımak için birkaç basit adımı tamamlamak yeterlidir. Birkaç basit önkoşulu yerine getirdiğinizi doğrulamanızın ardından, kuruluşunuzun tamamı için parola değiştirme ve sıfırlama işlemleri çok kısa süre içinde etkinleştirilir. Bu makalede aşağıdaki kavramlarla ilgili olarak size yol gösterilecektir:

* [**Başlamadan önce okumanız için müşterilerimizden önemli ipuçları**](#top-tips-from-our-customers-to-read-before-you-begin)
 * [**ÖNEMLİ İPUCU: BELGELERDE GEZİNME** - Yanıtları bulmak için içindekiler bölümümüzü ve tarayıcınızın bulma özelliğini kullanın](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
 * [**İpucu 1: LİSANSLAMA** - Lisanslama gereksinimlerini anladığınızdan emin olun](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
 * [**İpucu 2: TEST** - Yönetici değil bir son kullanıcı ile test edin ve küçük bir kullanıcı grubuyla pilot uygulaması yapın](#tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
 * [**İpucu 3: DAĞITIM** - Kullanıcılarınızın kaydetmek zorunda kalmaması için verilerini önceden doldurun](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
 * [**İpucu 4: DAĞITIM** - Geçici parola iletme gereksinimini ortadan kaldırmak için parola sıfırlama özelliğini kullanın](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
 * [**İpucu 5: GERİ YAZMA** - Parola geri yazma sorunlarını gidermek için AAD Connect makinenizdeki uygulama olay günlüğüne bakın](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
 * [**İpucu 6: GERİ YAZMA** - Parola geri yazma için doğru izinleri, güvenlik duvarı kurallarını ve bağlantı ayarlarını etkinleştirdiğinizden emin olun](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
 * [**İpucu 7: RAPORLAMA** - Azure AD SSPR Denetim Günlükleri ile kimlerin kaydolduğunu veya parola sıfırladığını görün](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
 * [**İpucu 8: SORUN GİDERME** - Birçok sorunu çözmek için sorun giderme kılavuzumuzu ve SSS bölümünü okuyun](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
 * [**İpucu 9: SORUN GİDERME** - Hala yardıma ihtiyacınız varsa, size yardımcı olabilmemiz için yeterli bilgi sağlayın](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)
* [**Kullanıcılara Azure Active Directory parolalarını sıfırlama olanağı tanıma**](#enable-users-to-reset-their-azure-ad-passwords)
 * [Self servis parola sıfırlama önkoşulları](#prerequisites)
 * [1. Adım: Parola sıfırlama ilkesini yapılandırma](#step-1-configure-password-reset-policy)
 * [2. Adım: Test kullanıcınız için iletişim verileri ekleme](#step-2-add-contact-data-for-your-test-user)
 * [3. Adım: Kullanıcı olarak parolanızı sıfırlama](#step-3-reset-your-azure-ad-password-as-a-user)
* [**Kullanıcılara şirket içi Azure Active Directory parolalarını sıfırlama veya değiştirme olanağı tanıma**](#enable-users-to-reset-or-change-their-ad-passwords)
 * [Parola Geri Yazma önkoşulları](#writeback-prerequisites)
 * [1. Adım: Azure AD Connect'in en son sürümünü indirme](#step-1-download-the-latest-version-of-azure-ad-connect)
 * [2. Adım: Kullanıcı arabirimi veya PowerShell ile Azure AD Connect'te Parola Geri Yazma'yı etkinleştirme ve doğrulama](#step-2-enable-password-writeback-in-azure-ad-connect)
 * [3. Adım: Güvenlik duvarınızı yapılandırma](#step-3-configure-your-firewall)
 * [4. Adım: Uygun izinleri ayarlama](#step-4-set-up-the-appropriate-active-directory-permissions)
 * [5. Adım: Kullanıcı olarak AD parolanızı sıfırlama ve doğrulama](#step-5-reset-your-ad-password-as-a-user)

## <a name="top-tips-from-our-customers-to-read-before-you-begin"></a>Başlamadan önce okumanız için müşterilerimizden önemli ipuçları
Kuruluşlarında parola yönetimi dağıtan müşteriler için faydalı gördüğümüz önemli ipuçlarından bazıları aşağıda verilmiştir.

* [**ÖNEMLİ İPUCU: BELGELERDE GEZİNME** - Yanıtları bulmak için içindekiler bölümümüzü ve tarayıcınızın bulma özelliğini kullanın](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
* [**İpucu 1: LİSANSLAMA** - Lisanslama gereksinimlerini anladığınızdan emin olun](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
* [**İpucu 2: TEST** - Yönetici değil bir son kullanıcı ile test edin ve küçük bir kullanıcı grubuyla pilot uygulaması yapın](#tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
* [**İpucu 3: DAĞITIM** - Kullanıcılarınızın kaydetmek zorunda kalmaması için verilerini önceden doldurun](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
* [**İpucu 4: DAĞITIM** - Geçici parola iletme gereksinimini ortadan kaldırmak için parola sıfırlama özelliğini kullanın](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
* [**İpucu 5: GERİ YAZMA** - Parola geri yazma sorunlarını gidermek için AAD Connect makinenizdeki uygulama olay günlüğüne bakın](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
* [**İpucu 6: GERİ YAZMA** - Parola geri yazma için doğru izinleri, güvenlik duvarı kurallarını ve bağlantı ayarlarını etkinleştirdiğinizden emin olun](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
* [**İpucu 7: RAPORLAMA** - Azure AD SSPR Denetim Günlükleri ile kimlerin kaydolduğunu veya parola sıfırladığını görün](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
* [**İpucu 8: SORUN GİDERME** - Birçok sorunu çözmek için sorun giderme kılavuzumuzu ve SSS bölümünü okuyun](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
* [**İpucu 9: SORUN GİDERME** - Hala yardıma ihtiyacınız varsa, size yardımcı olabilmemiz için yeterli bilgi sağlayın](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)

### <a name="top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers"></a>ÖNEMLİ İPUCU: BELGELERDE GEZİNME - Yanıtları bulmak için içindekiler bölümümüzü ve tarayıcınızın bulma özelliğini kullanın
Belgelerimizden herhangi birini kullanıyorsanız, içindekiler bölümünde yöneticilerin bilgi alması için ilgi çekici yerlerin tümüne ait hızlı bağlantılar verilmiştir. 

Aşağıdaki içindekiler bölümüne bakın: 
* [Azure AD Parola Sıfırlama: Belgelerin İçindekiler](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)

### <a name="tip-1-licensing---make-sure-you-understand-the-licensing-requirements"></a>İpucu 1: LİSANSLAMA - Lisanslama gereksinimlerini anladığınızdan emin olun
Azure AD Parola Sıfırlama işlevinin çalışması için kuruluşunuza en az bir lisans atanmış olmalıdır. Parola sıfırlama deneyiminde kullanıcı başına lisans kuralı uygulanmaz, ancak bir kullanıcıya atanmış lisans olmadan özelliği kullanırsanız, Microsoft lisans sözleşmenize uymadığınız kabul edilir ve bu kullanıcılara lisans atamanız gerekir.

Parola sıfırlama için hangi lisansların gerekli olduğunu anlamanıza yardımcı olabilecek bazı belgeler aşağıda verilmiştir.
* [Genel parola sıfırlama lisans bilgileri](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-customize#what-customization-options-are-available)
* [Özellik başına parola sıfırlama lisans bilgileri](https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability)
* [Parola geri yazma için desteklenen senaryolar](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#scenarios-supported-for-password-writeback)

### <a name="tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users"></a>İpucu 2: TEST - Yönetici değil bir son kullanıcı ile test edin ve küçük bir kullanıcı grubuyla pilot uygulaması yapın
Yönetici ile test ettiğinizde, aşağıda tanımlanan yönetici parola sıfırlama ilkesi uygulanır.  Bu durum, son kullanıcılarınız için yapılandırdığınız ilkenin beklenen sonuçlarını GÖRMEYECEĞİNİZ anlamına gelir.

Yöneticiye ait YALNIZCA UX içinde yapılandırılmış ilkeler, yöneticiler için değil, son kullanıcılar için geçerlidir. Microsoft, kuruluşunuzun güvenliğini korumak amacıyla yöneticiler için güçlü varsayılan parola sıfırlama ilkeleri uygular; bu ilkeler, son kullanıcılarınız için ayarladığınız ilkelerden farklı olabilir.

#### <a name="administrator-password-reset-policy"></a>Yönetici parolası sıfırlama ilkesi
* **Uygulandığı yer** - herhangi bir yönetici rolü (Genel Yönetici, Yardım Masası Yöneticisi, Parola Yöneticisi vb.)
* **Bir ağ geçidi ilkesi...**
 * ...bir deneme sürümü başlatıldıktan sonraki 30 gün için **VEYA**
 * ...bir gösterim etki alanı mevcut olmadığında **VE** Azure AD Connect kimlikleri eşitlemediğinde geçerlidir
 * **_Gereksinimler_**: Kimlik Doğrulama E-postası, Alternatif E-posta, Kimlik Doğrulama Telefonu, Cep Telefonu veya İş Telefonu değerlerinden herhangi **birinin** mevcut olması
* **İki ağ geçidi ilkesi...** 
 * ...deneme sürümünün ilk 30 günü geçtikten sonra **VEYA**
 * ...bir gösterim etki alanı mevcut olduğunda **VEYA** 
 * ...şirket içi ortamınızdaki kimlikleri eşitlemek üzere Azure AD Connect’i etkinleştirdiğinizde geçerlidir
 * _**Gereksinimler**_: Kimlik Doğrulama E-postası, Alternatif E-posta, Kimlik Doğrulama Telefonu, Cep Telefonu veya İş Telefonu değerlerinden herhangi **ikisinin** mevcut olması

### <a name="tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register"></a>İpucu 3: DAĞITIM - Kullanıcılarınızın kaydetmek zorunda kalmaması için verilerini önceden doldurun
Birçok insan, kullanıcılarınızın özelliği kullanmak için parola sıfırlamaya kaydolmak zorunda olmadığının farkında değil.  Kullanıcılarınız için önceden telefon veya e-posta özelliklerini ayarlayarak, **kullanıcılarınızın herhangi bir şey yapmasına gerek kalmadan** parola sıfırlamayı kuruluş geneline anında yayabilirsiniz!

Bir API, PowerShell veya Azure AD Connect ile bu işlemi yapma hakkında bilgi için aşağıdaki belgeleri okuyun:
* [Kullanıcıların kaydolmasına gerek olmadan parola sıfırlamayı dağıtma](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [Parola sıfırlamada hangi veriler kullanılır?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords"></a>İpucu 4: DAĞITIM - Geçici parola iletme gereksinimini ortadan kaldırmak için parola sıfırlama özelliğini kullanın
Bu ipucu, İpucu 3’ün bir etiketidir. Kullanıcılarınızı parola sıfırlama için önceden yapılandırdıktan sonra, bir çalışanın şirketinize yeni katıldığı bir senaryo düşünün. Çalışana geçici parola iletmek yerine, parolasını sıfırlamak için [Azure AD Parola Sıfırlama Portalı](https://passwordreset.microsoftonline.com)’na gitmesini isteyebilirsiniz.

Kullanıcı, [Windows 10 Azure AD Etki Alanına Katılmış cihaz](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) kullanıyorsa, bu işlemi Windows 10 İlk Kez oturum açma ekranından bile yapabilir. Böylece parmağınızı oynatmadan yepyeni bir bilgisayara erişmesini sağlayabilirsiniz :).

Bir API, PowerShell veya Azure AD Connect ile bu işlemi yapma hakkında bilgi için aşağıdaki belgeleri okuyun. Bu verileri önceden doldurduktan sonra, hesaplarına hemen giriş yapabilmeleri için kullanıcılarınızı parola sıfırlamaya göndermeniz yeterlidir:
* [Kullanıcıların kaydolmasına gerek olmadan parola sıfırlamayı dağıtma](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [Parola sıfırlamada hangi veriler kullanılır?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback"></a>İpucu 5: GERİ YAZMA - Parola geri yazma sorunlarını gidermek için AAD Connect makinenizdeki uygulama olay günlüğüne bakın
Azure AD Connect Uygulama Olay günlüğü, parola geri yazma hizmetinde gerçekleşen olayların birçoğunu gerçek zamanlı olarak açıklayan zengin günlük bilgileri içerir. Bu günlüğe erişmek için aşağıdaki adımları izleyin:

1. **Azure AD Connect** makinenizde oturum açın
2. **Başlat**’a basıp **"Olay Görüntüleyicisi"** yazarak **Windows Olay Görüntüleyicisi**’ni açın
3. **Uygulama** olay günlüğünü açın
4. Hangi sorunun gerçekleşebileceği hakkında daha fazla bilgi için şu kaynaklardaki olaylarını arayın: **PasswordResetService** veya **ADSync**

Bu günlükte görünebilen olayların tam listesi ile birlikte parola geri yazma ile ilgili daha fazla sorun giderme yönergesi için bkz:
* [Sorun giderme: parola geri yazma](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [Geri yazma olay günlüğü hata kodları](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [Sorun giderme: parola geri yazma bağlantısı](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [Geri yazma dağıtımı - Adım 3: Güvenlik duvarınızı yapılandırma](#step-3-configure-your-firewall)
* [Geri yazma dağıtımı - Adım 4: Uygun izinleri ayarlama](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback"></a>İpucu 6: GERİ YAZMA - Parola geri yazma için doğru izinleri, güvenlik duvarı kurallarını ve bağlantı ayarlarını etkinleştirdiğinizden emin olun
Geri yazma işleminin doğru çalışması için şunları sağlamanız gerekir:

1. Kullanıcıların AD’de kendi parola ve hesap kilidi açma bayraklarını değiştirme hakkına sahip olması için, parola geri yazma özelliği kullanılarak kullanıcılara doğru **Active Directory izinlerinin** verilmesi
2. Parola geri yazma hizmetinin giden bağlantı kullanarak dış dünya ile güvenli bir iletişim kurmasına olanak tanımak üzere doğru **güvenlik duvarı bağlantı noktalarının** açılması
3. Service Bus gibi temel parola sıfırlama hizmeti URL’leri için uygun **güvenlik duvarı özel durumlarının** oluşturulması
4. **Proxy ve güvenlik duvarınızın boşta giden bağlantıları sonlandırmaması**, 10 dakika veya daha uzun bir süre önerilir

Sorun giderme yönergeleri ile parola geri yazmaya ilişkin izin ve güvenlik duvarı kurallarını yapılandırma yönergelerinin tam listesi için bkz:
* [Sorun giderme: parola geri yazma](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [Geri yazma olay günlüğü hata kodları](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [Sorun giderme: parola geri yazma bağlantısı](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [Geri yazma dağıtımı - Adım 3: Güvenlik duvarınızı yapılandırma](#step-3-configure-your-firewall)
* [Geri yazma dağıtımı - Adım 4: Uygun izinleri ayarlama](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs"></a>İpucu 7: RAPORLAMA - Azure AD SSPR Denetim Günlükleri ile kimlerin kaydolduğunu veya parola sıfırladığını görün 
Parola sıfırlama dağıtılıp çalışır duruma getirildikten sonra, çalışmasını görmek ve hala kaydolması gereken kişileri, sıfırlama sırasında kullanıcılarınızın karşılaştığı yaygın sorunları ve özellik için yatırım getirinizi analiz etmek sonraki mantıksal adımdır.

Azure AD Parola Sıfırlama Denetim Günlüklerini kullanarak, bunu ve daha fazlasını Azure portalı, PowerBI, Azure AD Raporlama Olayları API’si veya PowerShell üzerinden yapabilirsiniz.  Bu raporlama özelliklerini kullanma hakkında daha fazla bilgi için bkz:
* [Parola yönetim raporlarına genel bakış](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#overview-of-password-management-reports)
* [Azure portalında parola yönetim raporlarını görüntüleme](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-view-password-management-reports)
* [Azure portalında Self Servis Parola Yönetimi etkinlik türleri](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#self-service-password-management-activity-types-in-the-new-azure-portal)
* [Azure AD Raporlar ve Olaylar API’sinden parola yönetimi olaylarını alma](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api)
* [PowerShell ile parola sıfırlama kayıt olaylarını hızlıca indirme](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-download-password-reset-registration-events-quickly-with-powershell)

### <a name="tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues"></a>İpucu 8: SORUN GİDERME - Birçok sorunu çözmek için sorun giderme kılavuzumuzu ve SSS bölümünü okuyun
Parola sıfırlama işlemiyle ilgili zengin sorun giderme yönergeleri ve SSS bölümünün olduğunu biliyor muydunuz? Bir sorunuz olursa, yanıtını aşağıdaki bağlantılardan bulma olasılığınız yüksektir.

Buna ek olarak, [Azure portalı](https://portal.azure.com) içindeki **"Destek ve Sorun Giderme"** dikey penceresini kullanarak, **Azure Active Directory** -> **Kullanıcılar ve Gruplar** -> **Parola Sıfırlama** -> **Destek ve Sorun Giderme** altında bulunan parola yönetimi yönetim kullanıcısı deneyiminden zengin sorun giderme içerikleri alabilirsiniz.

Parola sıfırlama sorun giderme yönergeleri ve SSS bağlantıları:
* [Parola Yönetimi Sorun Giderme](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot)
* [Parola Yönetimi Hakkında SSS](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-faq)

### <a name="tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you"></a>İpucu 9: SORUN GİDERME - Hala yardıma ihtiyacınız varsa, size yardımcı olabilmemiz için yeterli bilgi sağlayın
Hala sorun gidermeyle ilgili yardım gerekiyorsa, yardımcı olmaya hazırız. Bir destek talebi açabilir veya doğrudan bize ulaşması için hesap yönetimi ekibinizle temas kurabilirsiniz. Fikrinizi öğrenmek isteriz!

Ancak, size hızlıca yardım edebilmemiz için bize ulaşmadan önce lütfen **aşağıda istenen tüm bilgileri topladığınızdan emin olun**!
* [Yardıma ihtiyacınız olduğunda eklenecek bilgiler](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#information-to-include-when-you-need-help)

#### <a name="ways-to-provide-password-reset-feedback"></a>Parola sıfırlama geri bildirimi sağlamanın yolları
* [Özellik istekleri veya sorun giderme - Azure AD MSDN forumlarında yazın](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)
* [Özellik istekleri veya sorun giderme - StackOverflow üzerinde yazın](http://stackoverflow.com/questions/tagged/azure-active-directory)
* [Özellik istekleri veya sorun giderme - Tweet atın@azuread!](https://twitter.com/azuread)
* [Yalnızca özellik istekleri - UserVoice üzerinde not bırakın](https://feedback.azure.com/forums/169401-azure-active-directory)

## <a name="enable-users-to-reset-their-azure-ad-passwords"></a>Kullanıcıların Azure AD parolalarını sıfırlamalarına olanak tanıma
Bu bölümde, AAD bulut dizininiz için self servis parola sıfırlamayı etkinleştirme, kullanıcıları self servis parola sıfırlama özelliğine kaydetme ve ardından son olarak, test amaçlı self servis parola sıfırlama işlemini kullanıcı olarak gerçekleştirme işlemlerinde size yol gösterilir.

* [Self servis parola sıfırlama önkoşulları](#prerequisites)
* [1. Adım: Parola sıfırlama ilkesini yapılandırma](#step-1-configure-password-reset-policy)
* [2. Adım: Test kullanıcınız için iletişim verileri ekleme](#step-2-add-contact-data-for-your-test-user)
* [3. Adım: Kullanıcı olarak parolanızı sıfırlama](#step-3-reset-your-azure-ad-password-as-a-user)

### <a name="prerequisites"></a>Önkoşullar
Self servis parola sıfırlamayı etkinleştirebilmek ve kullanabilmek için ilk olarak aşağıdaki önkoşulları yerine getirmeniz gerekir:

* Bir AAD kiracısı oluşturun. Daha fazla bilgi için bkz. [Azure AD ile Çalışmaya Başlama](https://azure.microsoft.com/trial/get-started-active-directory/)
* Bir Azure aboneliği edinin. Daha fazla bilgi için bkz. [Azure AD kiracısı nedir?](active-directory-administer.md#what-is-an-azure-ad-tenant).
* AAD kiracınızı Azure aboneliğinizle ilişkilendirin. Daha fazla bilgi için bkz. [Azure aboneliklerinin Azure AD ile ilişkisi](https://msdn.microsoft.com/library/azure/dn629581.aspx).
* Azure AD Premium veya Basic sürümüne yükseltin ya da ücretli O365 lisansı kullanın. Daha fazla bilgi için bkz. [Azure Active Directory Sürümleri](https://azure.microsoft.com/pricing/details/active-directory/).

  > [!NOTE]
  > Bulut kullanıcıları için self servis parola sıfırlama özelliğini etkinleştirmek için Azure AD Premium, Azure AD Basic veya O365 ücretli lisansına yükseltmeniz gerekir.  Şirket içi kullanıcılarınız için self servis parola sıfırlama özelliğini etkinleştirmek için Azure AD Premium sürümüne yükseltmeniz gerekir. Daha fazla bilgi için bkz. [Azure Active Directory Sürümleri](https://azure.microsoft.com/pricing/details/active-directory/). Bu bilgiler, Azure AD Premium veya Basic sürümüne kaydolmaya, lisans planınızı ve Azure AD erişiminizi etkinleştirmeye, yönetici ve kullanıcı hesaplarına erişim atamaya yönelik ayrıntılı yönergeler içerir.
  >
  >
* AAD dizininizde en az bir yönetici hesabı ve bir kullanıcı hesabı oluşturun.
* Oluşturduğunuz yönetici ve kullanıcı hesabına bir AAD Premium, Basic veya O365 ücretli lisansı atayın.

### <a name="step-1-configure-password-reset-policy"></a>1. Adım: Parola sıfırlama ilkesini yapılandırma
Kullanıcı parolası sıfırlama ilkesini yapılandırmak için aşağıdaki adımları tamamlayın:

1. Tercih ettiğiniz tarayıcıyı açın ve [Klasik Azure portalına](https://manage.windowsazure.com) gidin.
2. [Klasik Azure portalında](https://manage.windowsazure.com), sol taraftaki gezinti çubuğunda **Active Directory uzantısını** bulun.

   ![Azure AD'de Parola Yönetimi][001]
3. **Directory (Dizin)** sekmesinde, içinde kullanıcı parolası sıfırlama ilkesini yapılandırmak istediğiniz dizine (ör. Wingtip Toys) tıklayın.

    ![][002]
4. **Configure (Yapılandır)** sekmesine tıklayın.

   ![][003]

5. **Configure (Yapılandır)** sekmesinde, aşağı kaydırarak **user password reset policy (kullanıcı parolası sıfırlama ilkesi)** bölümüne gidin.  Burada, belirli bir dizin için kullanıcı parolası sıfırlama ilkesinin tüm özelliklerini yapılandırabilirsiniz. *Yapılandır sekmesini görmüyorsanız Azure Active Directory Premium veya Temel sürümüne kaydolduğunuzdan ve bu özelliği yapılandıran yönetici hesabına __lisans atadığınızdan__ emin olun.*  

   > [!NOTE]
   > **Belirlediğiniz ilke yöneticiler için değil, yalnızca kuruluşunuzdaki son kullanıcılar için geçerlidir**. Güvenlikle ilgili nedenlerle, yöneticilere yönelik parola sıfırlama ilkesini Microsoft denetler. Mevcut yönetici ilkesi iki aşama gerektirir: Cep Telefonu ve E-posta Adresi.

   ![][004]
6. Kullanıcı parolası sıfırlama ilkesini yapılandırmak üzere **users enabled for password reset (kullanıcılar parola sıfırlayabilir)** seçeneğini **yes (evet)** ayarına kaydırın.  Bu işlem, bu özelliğin dizininizde çalışma biçimini yapılandırmanızı sağlayan birkaç ek denetimi görünür hale getirir.  Parola sıfırlama özelliğini uygun gördüğünüz şekilde özelleştirebilirsiniz.  Parola sıfırlama ilkesi denetimlerinden her birinin işlevi hakkında daha fazla bilgi edinmek istiyorsanız lütfen bkz. [Özelleştirme: Azure AD Parola Yönetimi](active-directory-passwords-customize.md).

   ![][005]
7. Kiracınız için kullanıcı parolası sıfırlama ilkesini istediğiniz gibi yapılandırdıktan sonra, ekranın alt kısmındaki **Save (Kaydet)** düğmesine tıklayın.

   > [!NOTE]
   > İşlevin en karmaşık durumda nasıl çalıştığını görebilmeniz için iki aşamalı kullanıcı parolası sıfırlama ilkesi önerilir.
   >
   >

   ![][006]

   > [!NOTE]
   > **Belirlediğiniz ilke yöneticiler için değil, yalnızca kuruluşunuzdaki son kullanıcılar için geçerlidir**. Güvenlikle ilgili nedenlerle, yöneticilere yönelik parola sıfırlama ilkesini Microsoft denetler. Mevcut yönetici ilkesi iki aşama gerektirir: Cep Telefonu ve E-posta Adresi.
   >
   >

### <a name="step-2-add-contact-data-for-your-test-user"></a>2. Adım: Test kullanıcınız için iletişim verileri ekleme
Kuruluşunuzdaki kullanıcılar için parola sıfırlamaya yönelik olarak kullanılacak verileri belirtmek üzere kullanabileceğiniz birkaç seçenek mevcuttur.

* [Klasik Azure portalında](https://manage.windowsazure.com) veya [Office 365 Yönetici Portalı](https://portal.microsoftonline.com)'nda kullanıcıları düzenleme
* Şirket içi Active Directory etki alanındaki kullanıcı özelliklerini Azure AD ile eşitlemek için AAD Connect'i kullanma
* Kullanıcı özelliklerini düzenlemek için Windows PowerShell'i kullanma
* Kullanıcıları [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) adresindeki kayıt portalına yönlendirerek kullanıcıların kendi verilerini kaydetmelerine olanak tanıma
* **Require users to register when signing in to the access panel (Kullanıcıların erişim panelinde oturum açarken kaydolmasını gerekli kıl)** SSPR yapılandırma seçeneğini **Yes (Evet)** olarak ayarlayarak, kullanıcıların [http://myapps.microsoft.com](http://myapps.microsoft.com) adresinden Erişim Paneli'nde oturum açtığında parola sıfırlama özelliğine kaydolmasını gerekli kılma.

Parola sıfırlama için hangi verilerin kullanıldığı ve bu verilere yönelik biçimlendirme gereksinimleri ile ilgili daha fazla bilgi edinmek istiyorsanız lütfen bkz. [Parola sıfırlama hangi verileri kullanır?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

#### <a name="to-add-user-contact-data-via-the-user-registration-portal"></a>Kullanıcı Kayıt Portalı üzerinden kullanıcı kişi verilerini eklemek için
1. Parola sıfırlama kayıt portalının kullanılması için kuruluşunuzdaki kullanıcılara bu sayfanın bağlantısını sağlamanız ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) veya kullanıcıların otomatik olarak kaydolmasını gerektiren seçeneği açmanız gerekir.  Kullanıcılar bu bağlantıya tıkladığında kuruluş hesaplarıyla oturum açmaları istenir.  Bunu yaptıktan sonra kullanıcılar şu sayfayı görür:

   ![][007]
2. Burada, kullanıcılar cep telefonu numaralarını, alternatif e-posta adreslerini veya güvenlik sorularını sağlayabilir ve doğrulayabilir.  Cep telefonu numarası doğrulama işlemi aşağıdaki gibidir.

   ![][008]
3. Bir kullanıcı bu bilgileri belirttikten sonra sayfa, bilgilerin geçerli olduğunu belirtecek şekilde güncelleştirilir (aşağıda karartılan kısımda olduğu gibi).  Kullanıcı, **Son** veya **İptal** düğmesine tıkladığında Erişim Paneli'ne yönlendirilir.

   ![][009]
4. Kullanıcı bu bilgilerin her ikisini de doğruladıktan sonra profili, sağladığı verilerle güncelleştirilir.  Bu örnekte, **Ofis Telefonu** numarası el ile belirtilmiştir; bu nedenle kullanıcı, parolasını sıfırlamak için iletişim yöntemi olarak bunu kullanabilir.

   ![][010]

### <a name="step-3-reset-your-azure-ad-password-as-a-user"></a>3. Adım: Kullanıcı olarak Azure AD parolanızı sıfırlama
Artık bir kullanıcı sıfırlama ilkesi yapılandırdığınıza ve kullanıcınız için kişi ayrıntılarını belirttiğinize göre bu kullanıcı, self servis parola sıfırlama işlemi gerçekleştirebilir.

#### <a name="to-perform-a-self-service-password-reset"></a>Self servis parola sıfırlama işlemini gerçekleştirmek için
1. [**portal.microsoftonline.com**](http://portal.microsoftonline.com) gibi bir siteye giderseniz aşağıdakine benzer bir oturum açma ekranı görürsünüz.  Parola sıfırlama kullanıcı arabirimini test etmek için **Hesabınıza erişemiyor musunuz?** bağlantısına tıklayın.

   ![][011]
2. **Hesabınıza erişemiyor musunuz?** bağlantısına tıkladıktan sonra, parolasını sıfırlamak istediğiniz **kullanıcı kimliğini** isteyen yeni bir sayfaya yönlendirilirsiniz.  Test **kullanıcı kimliğinizi** buraya girin, captcha doğrulamasından geçin ve **sonraki** seçeneğine tıklayın.

   ![][012]
3. Kullanıcı bu örnekte bir **ofis telefonu**, **cep telefonu** ve **alternatif e-posta** belirttiğinden, kullanıcıya ilk testten geçmesi için bu seçeneklerin tümünün sunulduğunu göreceksiniz.

   ![][013]
4. Bu durumda, ilk olarak **ofis telefonunu** **aramayı** tercih edin.  Telefon tabanlı bir yöntem seçildiğinde kullanıcılardan parolalarını sıfırlayabilmeleri için ilk olarak **telefon numaralarını doğrulamaları** istenir.  Bunun amacı, kötü amaçlı kişilerin kuruluşunuzdaki kullanıcıların telefon numaralarını istenmeyen şekilde kullanmasını engellemektir.

   ![][014]
5. Kullanıcı telefon numarasını doğruladıktan sonra ara seçeneğine tıklandığında bir değer değiştirici görünür ve kullanıcının telefonu çalar.  Kullanıcı, aramanızı açtığında hesabını doğrulamak için **kullanıcının "#" tuşuna basması gerektiğini** belirten bir ileti oynatılır.  Bu tuşa basılması kullanıcının birinci testi geçtiğini otomatik olarak doğrular ve kullanıcı arabirimini ikinci doğrulama adımına geçirir.

   ![][015]
6. İlk testi başarıyla geçmenizin ardından, kullanıcı arabirimi bunu kullanıcının sahip olduğu seçeneklerin listesinden kaldıracak şekilde otomatik olarak güncelleştirilir.  Bu durumda, ilk olarak **Ofis Telefonu**’nuzu kullandığınızdan, ikinci doğrulama adımı için sınama olarak kullanabileceğiniz geriye kalan geçerli seçenekler yalnızca **Cep Telefonu** ve **Alternatif E-posta** olur.  **Alternatif e-posta adresime gönder** seçeneğine tıklayın.  Bu işlemin ardından, e-posta seçeneğine basılması kayıtlı alternatif e-posta adresine e-posta gönderir.

   ![][016]
7. Aşağıda, kullanıcıların göreceği e-postaya örnek verilmiştir. Kiracı markasına dikkat edin:

   ![][017]
8. E-posta geldikten sonra sayfa güncelleştirilir ve e-postanın içerdiği doğrulama kodunu aşağıda gösterilen giriş kutusuna girmeniz mümkün olur.  İlgili kod girildikten sonra, sonraki düğmesi yanar ve ikinci doğrulama adımından geçebilirsiniz.

   ![][018]
9. Kuruluş ilkesinin gereksinimlerini karşılamanızın ardından yeni bir parola seçmenize izin verilir.  Parola, AAD "güçlü" parola gereksinimlerini karşılaması temelinde doğrulanır (bkz. [Azure AD'de parola ilkesi](https://msdn.microsoft.com/library/azure/jj943764.aspx)) ve girilen parolanın bu ilkeyi karşılayıp karşılamadığının kullanıcı için belirtilmesine yönelik bir güç doğrulayıcı görünür.

   ![][019]
10. Kuruluş ilkesine uygun eşleşen parolalar sağladığınızda, parolanız sıfırlanır ve yeni parolanızla hemen oturum açabilirsiniz.

    ![][020]

## <a name="enable-users-to-reset-or-change-their-ad-passwords"></a>Kullanıcıların AD parolalarını sıfırlamasına veya değiştirmesine olanak tanıma
Bu bölümde, parolaların şirket içi Active Directory'ye geri yazılması amacıyla parola sıfırlama özelliğini yapılandırma konusunda size yol gösterilmektedir.

* [Parola Geri Yazma önkoşulları](#writeback-prerequisites)
* [1. Adım: Azure AD Connect'in en son sürümünü indirme](#step-1-download-the-latest-version-of-azure-ad-connect)
* [2. Adım: Kullanıcı arabirimi veya PowerShell ile Azure AD Connect'te Parola Geri Yazma'yı etkinleştirme ve doğrulama](#step-2-enable-password-writeback-in-azure-ad-connect)
* [3. Adım: Güvenlik duvarınızı yapılandırma](#step-3-configure-your-firewall)
* [4. Adım: Uygun izinleri ayarlama](#step-4-set-up-the-appropriate-active-directory-permissions)
* [5. Adım: Kullanıcı olarak AD parolanızı sıfırlama ve doğrulama](#step-5-reset-your-ad-password-as-a-user)

### <a name="writeback-prerequisites"></a>Geri Yazma önkoşulları
Parola Geri Yazma özelliğini etkinleştirebilmek ve kullanabilmek için ilk olarak aşağıdaki önkoşulları yerine getirmeniz gerekir:

* Azure AD Premium'un etkin olduğu bir Azure AD kiracısına sahip olmanız gerekir.  Daha fazla bilgi için bkz. [Azure Active Directory Sürümleri](active-directory-editions.md).
* Parola geri yazma özelliğinin çalışması için Azure AD Connect’i Birincil Etki Alanı Denetleyicisi Öykünücüsüne bağlamanız gerekir.  Gerekirse, Azure AD Connect’i Birincil Etki Alanı Denetleyicisi kullanacak şekilde yapılandırmak için Active Directory eşitleme bağlayıcısının **özelliklerine** sağ tıklayın, ardından **dizin bölümlerini yapılandır**’ı seçin. Burada **etki alanı denetleyicisi bağlantı ayarları** bölümünü arayın ve **yalnızca tercih edilen etki alanı denetleyicilerini kullan** başlıklı kutuyu işaretleyin.  Not: tercih edilen DC bir PDC öykünücüsü değilse, Azure AD Connect parola geri yazma için PDC’ye yine de ulaşır.
* Parola sıfırlama, kiracınızda yapılandırılmış ve etkinleştirilmiştir.  Daha fazla bilgi için bkz. [Kullanıcıların Azure AD parolalarını sıfırlamasına olanak tanıma](#enable-users-to-reset-their-azure-ad-passwords)
* Bu özelliği test etmek için kullanabileceğiniz, Azure AD Premium lisansına sahip olan en az bir yönetici hesabına ve bir test kullanıcı hesabına sahip olmanız gerekir.  Daha fazla bilgi için bkz. [Azure Active Directory Sürümleri](active-directory-editions.md).

  > [!NOTE]
  > Parola Geri Yazma özelliğini etkinleştirmek için kullandığınız yönetici hesabının bir bulut yönetici hesabı (Azure AD'de oluşturulmuş) olduğundan ve birleştirilmiş bir hesap (şirket içi AD'de oluşturulmuş ve Azure AD ile eşitlenmiş) olmadığından emin olun.
  >
  >
* En son hizmet paketleri yüklü şekilde Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 veya Windows Server 2012 R2'yi çalıştıran tek veya çok ormanlı AD şirket içi dağıtımına sahip olmanız gerekir.

  > [!NOTE]
  > Windows Server 2008 veya 2008 R2'nin daha eski bir sürümünü çalıştırıyorsanız bu özelliği kullanmaya devam edebilirsiniz ancak bulutta yerel AD parola ilkenizi zorunlu kılabilmeniz için ilk olarak [KB 2386717'yi indirip yüklemeniz](https://support.microsoft.com/kb/2386717) gerekir.
  >
  >
* Azure AD Connect aracı yüklü olmalı ve AD ortamınızı bulut ile eşitlemeye yönelik olarak hazırlamış olmanız gerekir.  Daha fazla bilgi için bkz. [Şirket içi kimlik altyapınızı bulutta kullanma](connect/active-directory-aadconnect.md).

  > [!NOTE]
  > Parola geri yazma özelliğini test etmeden önce ilk olarak Azure AD Connect'te hem AD'den hem de Azure AD'den tam içeri aktarma işlemini tamamladığınızdan emin olun.
  >
  >
* Azure AD Eşitleme veya Azure AD Connect kullanıyorsanız giden **TCP 443** (ve bazı durumlarda **TCP 9350-9354**) açık olmalıdır.  Daha fazla bilgi için bkz. [3. Adım: Güvenlik duvarınızı yapılandırma](#step-3-configure-your-firewall). Bu senaryo için DirSync kullanımı artık desteklenmemektedir.  DirSync kullanmaya devam ediyorsanız parola geri yazma özelliğini dağıtmadan önce lütfen Azure AD Connect'in en son sürümüne yükseltme yapın.

  > [!NOTE]
  > Mümkün olan en iyi deneyimin ve en yeni özelliklerin sağlanması için, Azure AD Eşitleme veya DirSync araçlarını kullanan tüm kişilerin Azure AD Connect'in en son sürümüne yükseltme yapmasını öneririz.
  >
  >

### <a name="step-1-download-the-latest-version-of-azure-ad-connect"></a>1. Adım: Azure AD Connect'in en son sürümünü indirme
Parola Geri Yazma özelliği Azure AD Connect'in sürümlerinde veya sürüm numarası **1.0.0419.0911** veya üzeri olan Azure AD Eşitleme aracında kullanılabilir.  Otomatik hesap kilidi açma özelliğine sahip Parola Geri Yazma, Azure AD Connect'in sürümlerinde veya sürüm numarası **1.0.0485.0222** ya da üzeri olan Azure AD Eşitleme aracında kullanılabilir. Eski bir sürümü çalıştırıyorsanız lütfen devam etmeden önce en azından bu sürüme yükseltme yapın. [Azure AD Connect'in en son sürümünü indirmek için buraya tıklayın](connect/active-directory-aadconnect.md#install-azure-ad-connect).

#### <a name="to-check-the-version-of-azure-ad-sync"></a>Azure AD Eşitleme'nin sürümünü denetleme
1. **%ProgramFiles%\Azure Active Directory Sync\** konumuna gidin.
2. **ConfigWizard.exe** yürütülebilir dosyasını bulun.
3. Yürütülebilir dosyaya sağ tıklayıp bağlam menüsünden **Özellikler** seçeneğini belirleyin.
4. **Ayrıntılar** sekmesine tıklayın.
5. **Dosya sürümü** alanını bulun.

   ![][021]

Bu sürüm numarası **1.0.0419.0911** değerinden büyük veya bu değere eşitse ya da Azure AD Connect'i yüklüyorsanız [2. Adım: Kullanıcı arabirimi veya Powershell ile Azure AD Connect'te Parola Geri Yazma'yı etkinleştirme ve doğrulama](#step-2-enable-password-writeback-in-azure-ad-connect) adımına geçebilirsiniz.

> [!NOTE]
> Azure AD Connect aracını ilk kez yüklüyorsanız ortamınızı dizin eşitleme için hazırlamak üzere birkaç en iyi uygulamayı izlemeniz önerilir.  Azure AD Connect aracını yüklemeden önce [Office 365 Yönetici Portalı](https://portal.microsoftonline.com)'nda veya [Klasik Azure portalında](https://manage.windowsazure.com) dizin eşitlemesini etkinleştirmeniz gerekir.  Daha fazla bilgi için bkz. [Azure AD Connect'i Yönetme](active-directory-aadconnect-whats-next.md).
>
>

### <a name="step-2-enable-password-writeback-in-azure-ad-connect"></a>2. Adım: Azure AD Connect'te parola geri yazmayı etkinleştirme
Azure AD Connect aracını indirdiğinize göre, artık Parola Geri Yazma özelliğini etkinleştirmek için hazırsınız.  Bunu iki yoldan birini kullanarak yapabilirsiniz.  Parola Geri Yazma özelliğini Azure AD Connect kurulum sihirbazının isteğe bağlı özellikler ekranında veya Windows PowerShell üzerinden etkinleştirebilirsiniz.

#### <a name="to-enable-password-writeback-in-the-configuration-wizard"></a>Yapılandırma sihirbazında Parola Geri Yazma özelliğini etkinleştirmek için
1. **Dizin Eşitlemesi bilgisayarınızda**, **Azure AD Connect** yapılandırma sihirbazını açın.
2. **İsteğe bağlı özellikler** yapılandırma ekranına erişene kadar adımlara tıklayarak geçin.
3. **Parola geri yazma** seçeneğini işaretleyin.

   ![][022]
4. Sihirbazı tamamlayın; son sayfada değişiklikler özetlenir ve Parola Geri Yazma yapılandırma değişikliği bulunur.

> [!NOTE]
> Bu sihirbazı yeniden çalıştırıp özelliğin seçimini kaldırarak veya [Klasik Azure portalında](https://manage.windowsazure.com) dizininizin **Yapılandır** sekmesinin **Kullanıcı Parolası Sıfırlama İlkesi** bölümünde **Şirket İçi Dizine Parolaları Geri Yaz** ayarını **Hayır** olarak belirleyerek Parola Geri Yazma özelliğini istediğiniz zaman devre dışı bırakabilirsiniz.  Parola sıfırlama deneyiminizi özelleştirme hakkında daha fazla bilgi için bkz. [Özelleştirin: Azure AD Parola Yönetimi](active-directory-passwords-customize.md).
>
>

#### <a name="to-enable-password-writeback-using-windows-powershell"></a>Parola Geri Yazma özelliğini Windows PowerShell kullanarak etkinleştirmek için
1. **Dizin Eşitlemesi bilgisayarınızda** yeni bir **yükseltilmiş Windows PowerShell penceresi** açın.
2. Modül zaten yüklü değilse Azure AD Connect cmdlet'lerini geçerli oturumunuza yüklemek için `import-module ADSync` komutunu yazın.
3. `Get-ADSyncConnector` cmdlet'ini çalıştırarak ve sonuçları `$aadConnectorName = Get-ADSyncConnector|where-object {$_.name -like "*AAD"}` gibi bir `$aadConnectorName` içinde depolayarak Azure AD Bağlayıcıları'nın listesini alın
4. Aşağıdaki cmdlet'i çalıştırarak geçerli bağlayıcı için geri yazmanın geçerli durumunu almak üzere: `Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name`
5. Parola Geri Yazma'yı cmdlet'i çalıştırarak etkinleştirme: `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name –Enable $true`

> [!NOTE]
> Kimlik bilgilerinin istenmesi halinde, AzureADCredential için belirttiğiniz kullandığınız yönetici hesabının bir **bulut yönetici hesabı (Azure AD'de oluşturulmuş)** olduğundan ve birleştirilmiş bir hesap (şirket içi AD'de oluşturulmuş ve Azure AD ile eşitlenmiş) olmadığından emin olun.
>
> [!NOTE]
> Yukarıdaki yönergeleri tekrarlayarak, ancak bu defa adım içinde `$false` değerini geçirerek veya [Klasik Azure portalında](https://manage.windowsazure.com) dizininizin **Yapılandır** sekmesinin **Kullanıcı Parolası Sıfırlama İlkesi** bölümünde **Şirket İçi Dizine Parolaları Geri Yaz** ayarını **Hayır** olarak belirleyerek PowerShell ile Parola Geri Yazma özelliğini devre dışı bırakabilirsiniz.
>
>

#### <a name="verify-that-the-configuration-was-successful"></a>Yapılandırmanın başarılı olduğunu doğrulama
Yapılandırma başarılı olduğunda, Windows PowerShell penceresinde Parola sıfırlama geri yazma işlemi etkinleştirildi iletisini görür veya yapılandırma kullanıcı arabiriminde bir başarı iletisi görürsünüz.

Ayrıca Olay Görüntüleyicisi'ni açarak, uygulama olay günlüğüne giderek ve **PasswordResetService** kaynağına ait **31005 - OnboardingEventSuccess** olayını arayarak hizmetin doğru şekilde yüklendiğini doğrulayabilirsiniz.

  ![][023]

### <a name="step-3-configure-your-firewall"></a>3. Adım: Güvenlik duvarınızı yapılandırma
Parola Geri Yazma işlevini etkinleştirdikten sonra, Azure AD Connect uygulamasını çalıştıran bilgisayarın, parola geri yazma isteklerini almak için Microsoft bulut hizmetlerine erişebildiğinden emin olun. Bu adımda ağ cihazlarınızdaki (proxy sunucuları, güvenlik duvarları vs.) bağlantı kurallarını, belirli ağ bağlantı noktaları üzerinden belirli [Microsoft URL'lerine ve IP adreslerine](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US) yönelik giden bağlantılara izin verecek şekilde güncelleştirmeniz gerekir. Bu değişiklikler Azure AD Connect aracının sürümüne göre değişiklik gösterebilir. Daha fazla bağlam için [parola geri yazma özelliğinin çalışma şekli](active-directory-passwords-learn-more.md#how-password-writeback-works) ve [parola geri yazma güvenlik modeli](active-directory-passwords-learn-more.md#password-writeback-security-model) hakkında daha fazla bilgi edinebilirsiniz.

#### <a name="why-do-i-need-to-do-this"></a>Bunu neden yapmam gerekiyor?

Parola Geri Yazma özelliğinin düzgün çalışması için, Azure AD Connect’i çalıştıran makinenin Parola Sıfırlama Hizmeti ve Azure Service Bus ile konuşabilmesi gerekir.

Azure AD Connect aracı **1.1.443.0** ve üzeri için:

- Azure AD Connect aracının en son sürümünün aşağıdakilere **giden HTTPS** erişimi gerekir:
    - *passwordreset.microsoftonline.com*
    - *servicebus.windows.net*

Azure AD Connect aracı sürüm **1.0.8667.0** ila **1.1.380.0** için:

- **1. Seçenek:** 443 numaralı bağlantı noktasından (URL veya IP adresi kullanan) tüm giden HTTPS bağlantılara izin verin.
    - Bunu kullanmanız gereken durum:
        - Azure Veri Merkezi IP aralıkları değiştiğinde güncelleştirilmesi gerekmeyen kolay bir yapılandırma için bu seçeneği kullanın.
    - Gerekli adımlar:
        - 443 numaralı bağlantı noktasından URL veya IP adresi kullanan tüm giden HTTPS bağlantılarına izin verin.
<br><br>
- **2. Seçenek:** Belirli IP aralıklarına veya URL'lere yönelik giden HTTPS bağlantılarına izin verin.
    - Bunu kullanmanız gereken durum:
        - Kısıtlı bir ağ ortamındaysanız veya giden bağlantılara izin vermek istemiyorsanız bu seçeneği kullanın.
        - Bu yapılandırmada parola geri yazma işlevinin çalışmaya devam etmesi için ağ cihazlarının, Microsoft Azure Veri Merkezi IP Aralıkları listesine göre her hafta güncelleştirilmesini sağlamanız gerekir. Bu IP aralıkları her Çarşamba (Pasifik Saati) güncelleştirilen ve takip eden Pazartesi (Pasifik Saati) devreye alınan bir XML dosyası olarak mevcuttur.
    - Gerekli adımlar:
        - *.servicebus.windows.net adresine giden tüm HTTPS bağlantılarına izin verin.
        - Microsoft Azure Veri Merkezi IP Aralıkları listesindeki tüm IP adreslerine yönelik giden HTTPS bağlantılarına izin verin ve bu yapılandırmayı her hafta güncelleştirin. Listeyi [buradan](https://www.microsoft.com/download/details.aspx?id=41653) indirebilirsiniz.

> [!NOTE]
> Parola Geri Yazma işlevini yukarıdaki talimatları uygulayarak yapılandırmanıza ve Azure AD Connect olay günlüğünde hiç hata görmemenize rağmen test sırasında bağlantı hataları alıyorsanız, ortamınızdaki ağ gereçlerinden biri belirli IP adreslerine yönelik HTTPS bağlantıları kurulmasını engelliyor olabilir. Örneğin, *https://*.servicebus.windows.net* adresine yapılan bağlantılara izin verilmiş ancak ilgili aralıktaki belirli bir IP adresi engellenmiş olabilir. Bu sorunu çözmek için ağ ortamınızı, 443 numaralı bağlantı noktası üzerinden tüm URL veya IP adreslerine yönelik giden HTTPS bağlantılarına izin verecek şekilde yapılandırmanız (yukarıdaki 1. Seçenek) veya ağ ekibinizle birlikte çalışarak, belirli IP adreslerine yönelik HTTPS bağlantılarına izin vermeniz (yukarıdaki 2. Seçenek) gerekir.

**Eski sürümler için:**

- 443, 9350-9354 ve 5671 numaralı bağlantı noktaları üzerinden giden TCP bağlantılarına izin verin
- *https://ssprsbprodncu-sb.accesscontrol.windows.net/* adresine giden bağlantılara izin verin

> [!NOTE]
> Azure AD Connect'in 1.0.8667.0 öncesi bir sürümünü kullanıyorsanız Microsoft, yapılandırmayı daha kolay hale getirmek için birkaç geri yazma ağ geliştirmesi de içeren [Azure AD Connect'in son sürümüne](https://www.microsoft.com/download/details.aspx?id=47594) yükseltmenizi önerir.

Ağ cihazları yapılandırıldıktan sonra Azure AD Connect aracını çalıştıran bilgisayarı yeniden başlatın.

#### <a name="idle-connections-on-azure-ad-connect-114430-and-up"></a>Azure AD Connect’te boşta bağlantılar (1.1.443.0 ve üstü)
Azure AD Connect aracı, bağlantıların etkin kalmasını sağlamak için ServiceBus Uç noktalarına düzenli aralıklarla ping/canlı tutma gönderir. Aracın çok fazla bağlantının sonlandırıldığını algılaması halinde, uç noktasına ping sıklığını otomatik olarak artırır. En düşük 'ping aralıkları' 60 saniyede 1 ping şeklindedir, ancak **proxy/güvenlik duvarlarının, boşta bağlantıların en az 2-3 dakika boyunca devam etmesine izin vermesini önemle tavsiye ediyoruz.** \*Eski sürümler için, 4 dakika veya daha fazlasını öneririz.

### <a name="step-4-set-up-the-appropriate-active-directory-permissions"></a>4. Adım: İlgili Active Directory izinlerini ayarlama
Parolaları sıfırlanacak olan kullanıcıları içeren her bir orman için, yapılandırma sihirbazında söz konusu orman için belirtilen hesap X ise X hesabına `lockoutTime` üzerinde **Parola Sıfırlama**, **Parola Değiştirme**, **Yazma İzinleri**, `pwdLastSet` üzerinde **Yazma İzinleri** ve bu ormandaki her bir etki alanının kök nesnesi VEYA SSPR kapsamında olmasını istediğiniz kullanıcı OU’ları üzerinde genişletilmiş haklar verilmelidir.  Etki alanı kökü kabul edilebilir olmadığında sıfırlama izinlerinizin kapsamına yalnızca belirli bir kullanıcı nesneleri kümesini almak istiyorsanız ikinci seçeneği kullanabilirsiniz. Hak, tüm kullanıcı nesneleri tarafından devralınmış olarak işaretlenmelidir.  

Yukarıdakilerin hangi hesaba başvuruda bulunduğundan emin değilseniz Azure Active Directory Connect yapılandırma kullanıcı arabirimini açın ve **Çözümünüzü İnceleme** seçeneğine tıklayın.  Aşağıdaki ekran görüntüsünde, izin eklemeniz gereken hesabın kırmızıyla altı çizilmiştir.

**<font color="red">Aksi halde parola geri yazma düzgün şekilde çalışmayacağından, sisteminizdeki tüm ormanlarda yer alan her bir etki alanı için bu izni ayarladığınızdan emin olun.</font>**

  ![][032]

  Bu izinlerin ayarlanması, her bir ormana yönelik MA hizmet hesabının bu orman içindeki kullanıcı hesapları adına parolaları yönetmesine olanak tanır. Bu izinleri atamazsanız geri yazma doğru yapılandırılmış gibi görünse dahi kullanıcılar buluttan şirket içi parolalarını yönetme girişiminde bulunduğunda hatalarla karşılaşır. Bunun **Active Directory Kullanıcıları ve Bilgisayarları** yönetim eklentisi kullanılarak nasıl yapılacağına dair ayrıntılı adımlar burada verilmiştir:

> [!NOTE]
> Bu izinlerin dizininizdeki tüm nesneler için çoğaltılması bir saate kadar sürebilir.
>
>

#### <a name="to-set-up-the-right-permissions-for-writeback-to-occur"></a>Geri yazmanın gerçekleşmesini sağlamak üzere doğru izinleri ayarlamak için
1. İlgili etki alanı yöneticisi izinlerine sahip bir hesapla **Active Directory Kullanıcıları ve Bilgisayarları**'nı açın.
2. **Görünüm Menüsü** seçeneğinde **Gelişmiş Özellikler**'in açık olduğundan emin olun.
3. Sol panelde, etki alanının kökünü temsil eden nesneye sağ tıklayın.
4. **Güvenlik** sekmesine tıklayın.
5. Ardından **Gelişmiş**'e tıklayın.

   ![][024]
6. **İzinler** sekmesinde **Ekle**'ye tıklayın.

   ![][025]
7. İzin vermek istediğiniz hesabı seçin (bu, orman için eşitleme ayarlanırken belirtilen hesapla aynıdır).
8. Üstteki açılan menüde **Descendent User objects (Alt Kullanıcı nesneleri)** seçeneğini belirleyin.
9. Görünen **İzin Girdisi** iletişim kutusunda `lockoutTime` üzerinde **Parola Sıfırlama**, **Parola Değiştirme**, **Yazma İzinleri** ve `pwdLastSet` üzerinde **Yazma İzinleri** kutusunu işaretleyin.

   ![][026]
   ![][027]
   ![][028]
10. Ardından tüm açık iletişim kutularında **Uygula/Tamam** seçeneğine tıklayın.

### <a name="step-5-reset-your-ad-password-as-a-user"></a>5. Adım: Kullanıcı olarak AD parolanızı sıfırlama
Parola Geri Yazma etkinleştirildikten sonra, hesabı bulut kiracınızın hesabına eşitlenmiş bir kullanıcının parolasını sıfırlayarak bu özelliğin çalışıp çalışmadığını test edebilirsiniz.

#### <a name="to-verify-password-writeback-is-working-properly"></a>Parola Geri Yazma'nın doğru şekilde çalıştığını doğrulamak için
1. [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) adresine veya herhangi bir kuruluş kimliği oturum açma ekranına gidin ve **Hesabınıza erişemiyor musunuz?** bağlantısına tıklayın.

   ![][029]
2. Bu durumda, parolasını sıfırlamak istediğiniz kullanıcı kimliğini soran yeni bir sayfa görmeniz gerekir. Test kullanıcı kimliğinizi girin ve parola sıfırlama işlem akışında ilerleyin.
3. Parolanızı sıfırladıktan sonra aşağıdakine benzer bir ekran görürsünüz. Bu, şirket içi ve/veya bulut dizinlerinizde parolanızı başarıyla sıfırladığınız anlamına gelir.

   ![][030]
4. İşlemin başarılı olduğunu doğrulamak veya mevcut hataları tanılamak için **Dizin Eşitlemesi bilgisayarınıza** gidin, **Olay Görüntüleyicisi**'ni açın, **uygulama olay günlüğüne** gidin ve test kullanıcınız için **PasswordResetService** kaynağına ait **31002 - PasswordResetSuccess** olayını arayın.

   ![][031]



## <a name="next-steps"></a>Sonraki adımlar
Aşağıda, tüm Azure AD Parola Sıfırlama belge sayfalarının bağlantıları verilmiştir:

* **Oturum açmada sorun yaşadığınız için mi buradasınız?** Sorun yaşıyorsanız bkz. [kendi parolanızı değiştirme ve sıfırlama](active-directory-passwords-update-your-own-password.md#reset-your-password).
* [**Nasıl çalışır?**](active-directory-passwords-how-it-works.md) - Hizmetin altı farklı bileşeni ve işlevleri hakkında bilgi edinin
* [**Özelleştirin**](active-directory-passwords-customize.md) - Hizmetin genel görünümünü ve hareketlerini kuruluşunuzun ihtiyaçlarına göre nasıl özelleştireceğinizi öğrenin
* [**En iyi uygulamalar**](active-directory-passwords-best-practices.md) - Kuruluşunuzdaki parolaları nasıl hızlı bir şekilde dağıtacağınızı ve etkili bir şekilde yöneteceğinizi öğrenin
* [**Görüş alın**](active-directory-passwords-get-insights.md) - Tümleşik raporlama özelliklerimiz hakkında bilgi edinin
* [**SSS**](active-directory-passwords-faq.md) - Sık sorulan soruların yanıtlarını alın
* [**Sorunları giderin**](active-directory-passwords-troubleshoot.md) - Hizmetle ilgili sorunların hızlı bir şekilde nasıl giderileceğini öğrenin
* [**Daha fazla bilgi edinin**](active-directory-passwords-learn-more.md) - Hizmetin çalışma biçimine ilişkin teknik bilgileri ayrıntılı olarak inceleyin

[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"

