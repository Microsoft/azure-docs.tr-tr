---
title: Azure MFA NPS uzantısı için hata kodları sorunlarını giderme-Azure Active Directory
description: Azure Multi-Factor Authentication için NPS uzantısıyla ilgili sorunları çözme konusunda yardım alın
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0d04db6e9ccedc1e67ed0cdfd914ab42ebea0b1
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "67536941"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication için NPS uzantısından hata iletilerini çözümleyin

Azure Multi-Factor Authentication için NPS uzantısıyla ilgili hatalarla karşılaşırsanız, bir çözüme daha hızlı ulaşmak için bu makaleyi kullanın. NPS uzantısı günlükleri, NPS uzantısının yüklendiği sunucudaki **özel görünümler** > **sunucu rolleri** > **Ağ İlkesi ve erişim Hizmetleri** altında Olay Görüntüleyicisi bulunur.

## <a name="troubleshooting-steps-for-common-errors"></a>Sık karşılaşılan hatalar için sorun giderme adımları

| Hata kodu | Sorun giderme adımları |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Desteğe başvurun](#contact-microsoft-support)ve günlüklerin toplanması için adımlar listesinden bahsedin. , Kiracı kimliği ve Kullanıcı asıl adı (UPN) dahil olmak üzere hatadan önce ne olduğu hakkında daha fazla bilgi sağlayın. |
| **CLIENT_CERT_INSTALL_ERROR** | İstemci sertifikasının nasıl yüklendiği veya kiracınızla ilişkili olduğu bir sorun olabilir. İstemci sertifikası sorunlarını araştırmak için [MFA NPS uzantısının sorunlarını giderme](howto-mfa-nps-extension.md#troubleshooting) konusundaki yönergeleri izleyin. |
| **ESTS_TOKEN_ERROR** | İstemci sertifikası ve ADAL belirteci sorunlarını araştırmak için [MFA NPS uzantısının sorunlarını giderme](howto-mfa-nps-extension.md#troubleshooting) konusundaki yönergeleri izleyin. |
| **HTTPS_COMMUNICATION_ERROR** | NPS sunucusu, Azure MFA 'dan yanıt alamıyor. Güvenlik duvarlarınızın ve giden trafik için açık bir şekilde açık olduğunu doğrulayın https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | NPS uzantısını çalıştıran sunucuda, ve ' https://adnotifications.windowsazure.com https://login.microsoftonline.com/ a ulaşabildiğinizi doğrulayın. Bu siteler yüklenmezseniz, bu sunucu üzerinde bağlantı sorunlarını giderin. |
| **Azure MFA için NPS uzantısı:** <br> Azure MFA için NPS uzantısı yalnızca AccessAccept durumundaki RADIUS istekleri için Ikincil kimlik doğrulaması gerçekleştirir. Yanıt durumu Accessred olan Kullanıcı Kullanıcı adı için istek alındı, istek yoksayılıyor. | Bu hata genellikle AD 'deki bir kimlik doğrulaması hatasını yansıtır veya NPS sunucusunun Azure AD 'den yanıt alamıyor olduğunu gösterir. Güvenlik duvarlarınızın, 80 ve 443 bağlantı noktalarını https://adnotifications.windowsazure.com https://login.microsoftonline.com kullanarak ve giden trafik için açık bir şekilde açık olduğunu doğrulayın. Ağ erişim Izinlerinin arama sekmesinde, ayarın "NPS ağ Ilkesi aracılığıyla erişimi denetle" olarak ayarlandığını denetlemek de önemlidir. Bu hata, kullanıcıya bir lisans atanmamışsa da tetiklenebilir. |
| **REGISTRY_CONFIG_ERROR** | Uygulama için kayıt defterinde bir anahtar eksik, bu durum [PowerShell betiğinin](howto-mfa-nps-extension.md#install-the-nps-extension) yükleme sonrasında çalıştırılmamasının nedeni olabilir. Hata iletisi eksik anahtarı içermelidir. HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. altındaki anahtara sahip olduğunuzdan emin olun. |
| **REQUEST_FORMAT_ERROR** <br> Radius Isteğinde zorunlu Radius Username\ıdentifier özniteliği eksik. NPS 'nin RADIUS isteklerini aldığını doğrulama | Bu hata genellikle bir yükleme sorununu yansıtır. NPS uzantısının, RADIUS isteklerini alabilen NPS sunucularına yüklenmesi gerekir. RDG ve RRAS gibi hizmetler için bağımlılıklar olarak yüklenen NPS sunucuları RADIUS istekleri almaz. NPS uzantısı, kimlik doğrulama isteğinden ayrıntıları okuyamadığından bu tür yüklemeler ve hatalar üzerine yüklendiğinde çalışmaz. |
| **REQUEST_MISSING_CODE** | NPS ve NAS sunucuları arasındaki parola şifreleme protokolünün, kullanmakta olduğunuz ikincil kimlik doğrulama yöntemini desteklediğinden emin olun. **Pap** , BULUTTA Azure MFA 'nın tüm kimlik doğrulama yöntemlerini destekler: telefon araması, tek yönlü SMS mesajı, mobil uygulama bildirimi ve mobil uygulama doğrulama kodu. **CHAPv2** ve **EAP** desteği telefon araması ve mobil uygulama bildirimi. |
| **USERNAME_CANONICALIZATION_ERROR** | Kullanıcının şirket içi Active Directory örneğinde mevcut olduğunu ve NPS hizmetinin dizine erişim izinleri olduğunu doğrulayın. Ormanlar arası güvenler kullanıyorsanız, daha fazla yardım için [desteğe başvurun](#contact-microsoft-support) . |

### <a name="alternate-login-id-errors"></a>Alternatif oturum açma KIMLIĞI hataları

| Hata kodu | Hata iletisi | Sorun giderme adımları |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Hata: userObjectSid arama başarısız oldu | Kullanıcının şirket içi Active Directory örneğinde mevcut olduğunu doğrulayın. Ormanlar arası güvenler kullanıyorsanız, daha fazla yardım için [desteğe başvurun](#contact-microsoft-support) . |
| **ALTERNATE_LOGIN_ID_ERROR** | Hata: Alternatif LoginID araması başarısız oldu | LDAP_ALTERNATE_LOGINID_ATTRIBUTE öğesinin [geçerli bir Active Directory özniteliği](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx)olarak ayarlandığını doğrulayın. <br><br> LDAP_FORCE_GLOBAL_CATALOG true olarak ayarlanmışsa veya LDAP_LOOKUP_FORESTS boş olmayan bir değerle yapılandırılmışsa, bir genel katalog yapılandırdığınızdan ve AlternateLoginID özniteliğinin buna eklendiğini doğrulayın. <br><br> LDAP_LOOKUP_FORESTS boş olmayan bir değerle yapılandırıldıysa değerin doğru olduğundan emin olun. Birden fazla orman adı varsa, adların boşluk değil, noktalı virgülle ayrılması gerekir. <br><br> Bu adımlar sorunu gidermezse, daha fazla yardım için [desteğe başvurun](#contact-microsoft-support) . |
| **ALTERNATE_LOGIN_ID_ERROR** | Hata: Alternatif LoginID değeri boş | AlternateLoginID özniteliğinin Kullanıcı için yapılandırıldığını doğrulayın. |

## <a name="errors-your-users-may-encounter"></a>Kullanıcılarınızın karşılaşabilecekleri hatalar

| Hata kodu | Hata iletisi | Sorun giderme adımları |
| ---------- | ------------- | --------------------- |
| **Accessreddedildi** | Çağıran kiracının Kullanıcı için kimlik doğrulaması yapmak üzere erişim izinleri yok | Kiracı etki alanının ve Kullanıcı asıl adı (UPN) etki alanının aynı olup olmadığını kontrol edin. Örneğin, contoso kiracısında kimlik user@contoso.com doğrulamaya çalışıyor olduğundan emin olun. UPN, Azure 'da kiracı için geçerli bir kullanıcıyı temsil eder. |
| **AuthenticationMethodNotConfigured** | Belirtilen kimlik doğrulama yöntemi kullanıcı için yapılandırılmadı | Kullanıcının, [iki adımlı doğrulama için ayarlarınızı yönetme](../user-help/multi-factor-authentication-end-user-manage-settings.md)konusundaki yönergelere göre doğrulama yöntemlerini eklemesini veya doğrulamasını sağlayabilirsiniz. |
| **AuthenticationMethodNotSupported** | Belirtilen kimlik doğrulama yöntemi desteklenmiyor. | Bu hatayı içeren tüm günlüklerinizi toplayın ve [desteğe başvurun](#contact-microsoft-support). Destek ekibiyle iletişime geçerek, hatayı tetikleyen Kullanıcı adını ve ikincil doğrulama yöntemini belirtin. |
| **Becaccessreddedildi** | MSODS BEC çağrısı erişim engellendi döndürdü, büyük olasılıkla Kullanıcı adı kiracıda tanımlı değil | Kullanıcı şirket içinde Active Directory vardır ancak AD Connect tarafından Azure AD 'ye eşitlenmez. Ya da, kiracı için Kullanıcı eksik. Kullanıcıyı Azure AD 'ye ekleyin ve [iki adımlı doğrulama için ayarlarınızı yönetme](../user-help/multi-factor-authentication-end-user-manage-settings.md)konusundaki yönergelere göre doğrulama yöntemlerini eklemesini isteyin. |
| **Invalidformat** veya **Strongauthenticationserviceınvalidparameter** | Telefon numarası tanınmayan bir biçimde | Kullanıcının kendi doğrulama telefon numaralarını düzeltmesini sağlayabilirsiniz. |
| **Invalidsession** | Belirtilen oturum geçersiz veya zaman aşımına uğradı | Oturumun tamamlaması üç dakikadan uzun sürdü. Kullanıcının doğrulama kodunu girdiğini veya kimlik doğrulama isteğini başlatma sırasında geçen üç dakika içinde uygulama bildirimine yanıt verdiğini doğrulayın. Bu sorunu çözmezse, istemci, NAS sunucusu, NPS sunucusu ve Azure MFA uç noktası arasında ağ gecikme süresi olmadığından emin olun.  |
| **Nodefaultauthenticationmethodısconfigured** | Kullanıcı için varsayılan kimlik doğrulama yöntemi yapılandırılmadı | Kullanıcının, [iki adımlı doğrulama için ayarlarınızı yönetme](../user-help/multi-factor-authentication-end-user-manage-settings.md)konusundaki yönergelere göre doğrulama yöntemlerini eklemesini veya doğrulamasını sağlayabilirsiniz. Kullanıcının varsayılan bir kimlik doğrulama yöntemi seçtiğinizden ve hesabı için bu yöntemi yapılandırdığınıza emin olun. |
| **OathCodePinIncorrect** | Yanlış kod ve PIN girildi. | Bu hata NPS uzantısında beklenmiyor. Kullanıcı bu sorunla karşılaşırsa, sorun giderme Yardım için [desteğe başvurun](#contact-microsoft-support) . |
| **Redakpatanotfound** | Belirtilen kimlik doğrulama yöntemi için düzeltme verileri yapılandırılmadı. | Kullanıcının farklı bir doğrulama yöntemi denemesini veya [iki adımlı doğrulama için ayarlarınızı yönetme](../user-help/multi-factor-authentication-end-user-manage-settings.md)konusundaki yönergelere göre yeni bir doğrulama yöntemi eklemesini sağlayabilirsiniz. Doğrulama yönteminin doğru ayarlandığını doğruladıktan sonra Kullanıcı bu hatayı görmeyi devam ederse [desteğe başvurun](#contact-microsoft-support). |
| **Smsauthfailedyanlışlıkla Gcodepıngirildi** | Yanlış kod ve PIN girildi. (OneWaySMS) | Bu hata NPS uzantısında beklenmiyor. Kullanıcı bu sorunla karşılaşırsa, sorun giderme Yardım için [desteğe başvurun](#contact-microsoft-support) . |
| **TenantIsBlocked** | Kiracı engellendi | Azure portal Azure AD Özellikleri sayfasından Dizin KIMLIĞIYLE [desteğe başvurun](#contact-microsoft-support) . |
| **UserNotFound** | Belirtilen kullanıcı bulunamadı | Kiracı artık Azure AD 'de etkin olarak görünmez. Aboneliğinizin etkin olduğunu ve gerekli birinci taraf uygulamalarınız olduğunu denetleyin. Ayrıca sertifika konusunun kiracının beklenen şekilde olduğundan ve sertifikanın hala geçerli ve hizmet sorumlusu altına kaydedildiğinden emin olun. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Kullanıcılarınızın hata olmayan bir şekilde karşılaşabilecekleri iletiler

Bazen, kimlik doğrulama istekleri başarısız olduğundan kullanıcılarınız Multi-Factor Authentication 'dan iletiler alabilir. Bunlar yapılandırma ürününde hatalar değildir, ancak bir kimlik doğrulama isteğinin neden reddedildiğini belirten kasıtlı bir uyarılardır.

| Hata kodu | Hata iletisi | Önerilen adımlar | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Yanlış kod Entered\om kodu yanlış | Kullanıcı yanlış koda girdi. Yeni bir kod isteyerek veya yeniden oturum açarak yeniden deneyin. | 
| **Smsauthfailedmaxallowedcoderetr'e ulaşıldı** | İzin verilen maksimum kod yeniden denemesi ulaştı | Kullanıcı doğrulama sınaması çok fazla kez başarısız oldu. Ayarlarınıza bağlı olarak, şimdi bir yönetici tarafından engellenmemiş olmaları gerekebilir.  |
| **Smsauthfailedyanlışlıkla Gcodegirildi** | Yanlış kod girildi/kısa mesaj OTP yanlış | Kullanıcı yanlış koda girdi. Yeni bir kod isteyerek veya yeniden oturum açarak yeniden deneyin. |

## <a name="errors-that-require-support"></a>Destek gerektiren hatalar

Bu hatalardan biriyle karşılaşırsanız, tanılama yardımı için [desteğe başvurmanız](#contact-microsoft-support) önerilir. Bu hataları gidermek için standart bir adım kümesi yoktur. Destek ekibiyle iletişime geçerek, hataya yol gösteren adımlar ve kiracı bilgileriniz hakkında mümkün olduğunca fazla bilgi eklediğinizden emin olun.

| Hata kodu | Hata iletisi |
| ---------- | ------------- |
| **Geçersiz parametre** | İstek null olmamalıdır |
| **Geçersiz parametre** | ObjectID, ReplicationScope için null veya boş olmamalıdır:{0} |
| **Geçersiz parametre** | CompanyName \{0} \ ' un uzunluğu izin verilen en fazla uzunluktan daha uzun{1} |
| **Geçersiz parametre** | UserPrincipalName null veya boş olmamalıdır |
| **Geçersiz parametre** | Belirtilen Tenantıd doğru biçimde değil |
| **Geçersiz parametre** | SessionID null veya boş olmamalıdır |
| **Geçersiz parametre** | İstek veya MSODS 'deki herhangi bir yazım verileri çözümlenemedi. Yazım verileri bilinmiyor |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Sonraki adımlar

### <a name="troubleshoot-user-accounts"></a>Kullanıcı hesaplarında sorun giderme

Kullanıcılarınız [iki adımlı doğrulamayla ilgili sorun](../user-help/multi-factor-authentication-end-user-troubleshoot.md)yaşıyorsanız, sorunları kendi kendine tanılamaya yardımcı olur.

### <a name="contact-microsoft-support"></a>Microsoft desteğine başvurun

Ek yardıma ihtiyacınız varsa [Azure Multi-Factor Authentication sunucusu desteği](https://support.microsoft.com/oas/default.aspx?prid=14947)aracılığıyla bir destek uzmanına başvurun. Bizimle iletişim kurarken, sorun hakkında mümkün olduğunca fazla bilgi dahil edebiliyorsanız bu yararlı olur. Sağlayabileceğiniz bilgiler, hatayı gördüğünüz sayfa, belirli hata kodu, belirli oturum KIMLIĞI, hatayı seçen kullanıcının KIMLIĞI ve hata ayıklama günlükleri içerir.

Destek Tanılama için hata ayıklama günlüklerini toplamak üzere NPS sunucusunda aşağıdaki adımları kullanın:

1. Kayıt Defteri Düzenleyicisi 'ni açın ve HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa set **VERBOSE_LOG** 'e gidin
2. Bir yönetici komut istemi açın ve şu komutları çalıştırın:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Sorunu yeniden oluşturun

4. Bu komutlarla izlemeyi durdurun:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Kayıt Defteri Düzenleyicisi 'ni açın ve HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa set **VERBOSE_LOG** 'e gidin
6. C:\NPS klasörünün içeriğini ZIP ve daraltılmış dosyayı destek örneğine iliştirin.
