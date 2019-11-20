---
title: Azure Active Directory kimlik doğrulaması ve yetkilendirme hata kodları | Microsoft Docs
description: Azure AD güvenlik belirteci hizmeti 'nden (STS) döndürülen AADSTS hata kodları hakkında bilgi edinin.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 261fe2142fc3bc45625b5d088a46ad92c34222db
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193176"
---
# <a name="authentication-and-authorization-error-codes"></a>Kimlik doğrulaması ve yetkilendirme hata kodları

Azure Active Directory (Azure AD) güvenlik belirteci hizmeti 'nden (STS) döndürülen AADSTS hata kodları hakkında bilgi mi arıyorsunuz? AADSTS hata açıklamalarını, düzeltmelerini ve önerilen bazı geçici çözümleri bulmak için bu belgeyi okuyun.

> [!NOTE]
> Bu bilgiler geçicidir ve değiştirilebilir. Sorunuz mu var? ya da aradığınızı bulamıyor musunuz? Bir GitHub sorunu oluşturun veya geliştiricilerin yardım ve destek alma hakkında daha fazla bilgi edinmek için [destek ve yardım seçenekleri](active-directory-develop-help-support.md) bölümüne bakın.
>
> Bu belge, geliştirici ve Yönetici Kılavuzu için sağlanır, ancak hiçbir şekilde istemci tarafından kullanılmamalıdır. Hata kodları, geliştiricilerin uygulamalarını oluştururken yardımcı olmaya yönelik daha ayrıntılı hata iletileri sağlamak için herhangi bir zamanda değişebilir. Metin veya hata kodu numaralarına bağımlılığı alan uygulamalar zaman içinde bozulur.

## <a name="lookup-current-error-code-information"></a>Geçerli hata kodu bilgilerini ara
Hata kodları ve mesajlar değiştirilebilir.  En güncel bilgiler için, aadsts hata açıklamalarını, [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) düzeltmeleri ve önerilen bazı geçici çözümleri bulmak için sayfaya göz atın.  

Döndürülen hata kodunun sayısal bölümünde arama yapın.  Örneğin, "AADSTS16000" hata kodunu aldıysanız, "16000" [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) için bir arama yapın.  Ayrıca, URL 'ye hata kodu numarasını ekleyerek doğrudan belirli bir hataya da bağlanabilirsiniz: [https://login.microsoftonline.com/error?code=16000](https://login.microsoftonline.com/error?code=16000).

## <a name="aadsts-error-codes"></a>AADSTS hata kodları

| Hata | Açıklama |
|---|---|
| AADSTS16000 | Selectkullanıcıhesabı-bu, Azure AD tarafından oluşturulan ve kullanıcının birden çok geçerli SSO oturumu arasından seçim yapmasına izin veren kullanıcı ARABIRIMI ile sonuçlanan bir kesintiye neden olur. Bu hata oldukça yaygındır ve belirtilirse uygulamaya `prompt=none` döndürülür. |
| AADSTS16001 | Useraccountselectiongeçersiz-Kullanıcı, oturumun seçim mantığı tarafından reddedildiği bir kutucuğa tıkladığı takdirde bu hatayı görürsünüz. Bu hata tetiklendiğinde, kullanıcının güncelleştirilmiş kutucuk/oturumlar listesinden seçerek veya başka bir hesap seçerek kurtarılmasına izin verir. Bu hata, bir kod hatası veya yarış durumu nedeniyle oluşabilir. |
| AADSTS16002 | Appsessionselectiongeçersiz-uygulama tarafından belirtilen SID gereksinimi karşılanmadı.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant-kullanıcının kiracıya açıkça eklenmediğini belirtir. |
| AADSTS17003 | CredentialKeyProvisioningFailed-Azure AD Kullanıcı anahtarını sağlayamıyor. |
| AADSTS20001 | Wsfedsignınresponseerror-federal kimlik sağlayıcınız ile ilgili bir sorun var. Bu sorunu çözmek için IDP’ye başvurun. |
| AADSTS20012 | Wsfedmessagegeçersiz-federal kimlik sağlayıcınız ile ilgili bir sorun var. Bu sorunu çözmek için IDP’ye başvurun. |
| AADSTS20033 | Fedmetadataınvalidtenantname-federal kimlik sağlayıcınızda bir sorun var. Bu sorunu çözmek için IDP’ye başvurun. |
| AADSTS40008 | OAuth2IdPUnretryableServerError-federal kimlik sağlayıcınızda bir sorun var. Bu sorunu çözmek için IDP’ye başvurun. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError-federal kimlik sağlayıcınızda bir sorun var. Bu sorunu çözmek için IDP’ye başvurun. |
| AADSTS40010 | OAuth2IdPRetryableServerError-federal kimlik sağlayıcınızda bir sorun var. Bu sorunu çözmek için IDP’ye başvurun. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError-federal kimlik sağlayıcınızda bir sorun var. Bu sorunu çözmek için IDP’ye başvurun. |
| AADSTS50000 | Tokenıssuanceerror-oturum açma hizmetiyle ilgili bir sorun var. Bu sorunu çözmek için bir [destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS50001 | Invalidresource-kaynak devre dışı veya yok. Erişmeye çalıştığınız kaynağın tam kaynak URL 'sini belirttiğinizden emin olmak için uygulamanızın kodunu kontrol edin.  |
| AADSTS50002 | NotAllowedTenant-kiracı üzerinde kısıtlanmış bir ara sunucu erişimi nedeniyle oturum açma başarısız oldu. Kendi kiracı ilkeniz varsa, bu sorunu giderecek şekilde kısıtlı kiracı ayarlarınızı değiştirebilirsiniz. |
| AADSTS50003 | Eksik bir imzalama anahtarı veya sertifikası nedeniyle MissingSigningKey-oturum açma başarısız oldu. Bu, uygulamada bir imzalama anahtarı yapılandırılmadığı için olabilir. [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured) sayfasında belirtilen çözümlere bakın. Sorun yaşamaya devam ederseniz uygulama sahibine veya bir uygulama yöneticisine başvurun. |
| AADSTS50005 | DevicePolicyError-Kullanıcı, şu anda koşullu erişim ilkesi aracılığıyla desteklenmeyen bir platformdan cihazda oturum açmaya çalıştı. |
| AADSTS50006 | Invalidsignature-geçersiz bir imza nedeniyle Imza doğrulaması başarısız oldu. |
| AADSTS50007 | PartnerEncryptionCertificateMissing-bu uygulama için iş ortağı şifreleme sertifikası bulunamadı. Bunu sağlamak için Microsoft ile [bir destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md) . |
| AADSTS50008 | Invalidsamltoken-bir belirteçte SAML onaylama işlemi eksik veya yanlış yapılandırılmış. Federasyon sağlayıcınıza başvurun. |
| AADSTS50010 | AudienceUriValidationFailed-belirteç izleyicileri yapılandırılmadığından uygulama için hedef kitle URI doğrulaması başarısız oldu. |
| AADSTS50011 | Invalidreplyto-yanıt adresi eksik, yanlış yapılandırılmış veya uygulama için yapılandırılan yanıt adresleriyle eşleşmiyor.  Bir çözüm olarak, bu eksik yanıt adresini Azure Active Directory uygulamasına eklemeyi veya bir kişiye Active Directory ' de Uygulamanızı yönetme izinlerine sahip olmasını sağlayın.|
| AADSTS50012 | AuthenticationFailed-aşağıdaki nedenlerden biri için kimlik doğrulama başarısız oldu:<ul><li>İmza sertifikasının konu adı yetkilendirilmemiş</li><li>Yetkili konu adı için eşleşen bir güvenilen yetkili ilkesi bulunamadı</li><li>Sertifika zinciri geçerli değil</li><li>İmza sertifikası geçerli değil</li><li>İlke kiracı üzerinde yapılandırılmadı</li><li>İmza sertifikasının parmak izi yetkilendirilmemiş</li><li>İstemci onaylama işlemi geçersiz bir imza içeriyor</li></ul> |
| AADSTS50013 | Farklı nedenlerden dolayı ınvalidassertion-assertion geçersiz; belirteç verenin geçerli zaman aralığı içindeki API sürümüyle eşleşmiyor-zaman aşımına uğradı-hatalı oluşturulmuş-bir birincil yenileme belirteci değildir. |
| AADSTS50014 | Guestuserınpendingstate-kullanıcının kullanım alanı bekleme durumunda. Konuk Kullanıcı hesabı henüz tam olarak oluşturulmamış. |
| AADSTS50015 | Viraluserligalageconsentrequiredstate-Kullanıcı Için geçerli yaş grubu onayı gerekir. |
| AADSTS50017 | CertificateValidationFailed-sertifika doğrulaması başarısız oldu, şu nedenlerden dolayı nedenler:<ul><li>Güvenilir sertifika listesinde verme sertifikası bulunamıyor</li><li>Beklenen CrlSegment bulunamıyor</li><li>Güvenilir sertifika listesinde verme sertifikası bulunamıyor</li><li>Delta CRL dağıtım noktası, karşılık gelen bir CRL dağıtım noktası olmadan yapılandırılmış</li><li>Zaman aşımı sorunu nedeniyle geçerli CRL kesimleri alınamıyor</li><li>CRL indirilemiyor</li></ul>Kiracı yöneticisine başvurun. |
| AADSTS50020 | Useryetkilendirilmemiş-kullanıcıların bu uç noktayı çağırma yetkisi yok. |
| AADSTS50027 | Invalidjwttoken-aşağıdaki nedenlerden dolayı geçersiz JWT belirteci:<ul><li>Bir kerelik anahtar talebi içermiyor, alt talep</li><li>konu tanımlayıcısı uyuşmazlığı</li><li>idToken taleplerinde yinelenen talep</li><li>beklenmeyen veren</li><li>beklenmeyen hedef kitle</li><li>geçerli zaman aralığı içinde değil </li><li>belirteç biçimi doğru değil</li><li>Verenin dış kimlik belirteci imza doğrulaması başarısız oldu.</li></ul> |
| AADSTS50029 | Geçersiz URI - etki alanı adı geçersiz karakterler içeriyor. Kiracı yöneticisine başvurun. |
| AADSTS50032 | WeakRsaKey-hatalı kullanıcının zayıf bir RSA anahtarını kullanma girişiminde olduğunu gösterir. |
| AADSTS50033 | RetryableError-veritabanı işlemleriyle ilgili olmayan geçici bir hatayı gösterir. |
| AADSTS50034 | UserAccountNotFound-bu uygulamada oturum açmak Için hesabın dizine eklenmesi gerekir. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt-bir yerleşik tanımlayıcı oluşturmak için gereken anahtar, ilke içinde eksik. Kiracı yöneticisine başvurun. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | Subjectmismatchesıssuer-konu, istemci onaylayıcısı içindeki veren talebi ile uyuşmuyor. Kiracı yöneticisine başvurun. |
| AADSTS50049 | Nosuchınstancefordiscovery-bilinmiyor veya geçersiz örnek. |
| AADSTS50050 | MalformedDiscoveryRequest-istek hatalı biçimlendirilmiş. |
| AADSTS50053 | Idskilitlendi-Kullanıcı, yanlış Kullanıcı KIMLIĞI veya parolasıyla çok fazla kez oturum açmaya çalıştığı için hesap kilitli. |
| AADSTS50055 | Invalidpasswordexpiredpassword-parolanın geçerliliği dolmuştur. |
| AADSTS50056 | Geçersiz veya null parola-bu kullanıcı için depoda parola yok. |
| AADSTS50057 | Kullanıcıdevre dışı-Kullanıcı hesabı devre dışı bırakıldı. Hesap bir yönetici tarafından devre dışı bırakıldı. |
| AADSTS50058 | Userınformationnotsaðlanan-bu, bir kullanıcının oturum açmadığı anlamına gelir. Bu, bir kullanıcı kimliği doğrulanmamış olduğunda ve henüz oturum açmamış olması beklenen yaygın bir hatadır.</br>Bu hata, kullanıcının daha önce oturum açmış olduğu bir SSO bağlamında teşvik edilir, bu, SSO oturumunun bulunamaması veya geçersiz olması anlamına gelir.</br>Prompt = none belirtilmişse bu hata uygulamaya döndürülür. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided-kiracı tanımlama bilgileri istekte bulunamadı veya belirtilen kimlik bilgileri tarafından kapsanıyor. Kullanıcı, sorunu çözmeye yardımcı olmak için kiracı yöneticisine bağlanabilir. |
| AADSTS50061 | Signoutınvalidrequest-oturum kapatma isteği geçersiz. |
| AADSTS50064 | CredentialAuthenticationError-Kullanıcı adı veya parola üzerinde kimlik bilgisi doğrulaması başarısız oldu. |
| AADSTS50068 | Signoutınitiatornotkatılımcı-SignOut başarısız oldu. SignOut 'i başlatan uygulama geçerli oturumda bir katılımcı değil. |
| AADSTS50070 | Signoutunknownsessionıdentifier-SignOut başarısız oldu. SignOut isteği, mevcut oturumunuzla eşleşmeyen bir ad tanımlayıcısı belirtti. |
| AADSTS50071 | Signoutmessagedoldu-oturum kapatma isteğinin süresi doldu. |
| AADSTS50072 | Userstrongauthkayıtlarını Mentrequiredınterrupt-kullanıcının İkinci faktör kimlik doğrulamasına (etkileşimli) kaydolması gerekir. |
| AADSTS50074 | Userstrongauthclientauthnrequiredınterrupt-güçlü kimlik doğrulaması gereklidir ve Kullanıcı MFA sınamasını geçirmedi. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired-yönetici tarafından yapılan bir yapılandırma değişikliği nedeniyle veya yeni bir konuma taşıdığınız için, kullanıcının kaynağa erişmek için Multi-Factor Authentication kullanması gerekir. Kaynak için yeni bir yetkilendirme isteğiyle yeniden deneyin. |
| AADSTS50079 | Userstrongauthkayıtlarına gerekli-yönetici tarafından yapılan bir yapılandırma değişikliği nedeniyle veya Kullanıcı yeni bir konuma taşındığı için, kullanıcının Multi-Factor Authentication kullanması gerekir. |
| AADSTS50085 | Yenileme belirteci için sosyal IDP oturum açma bilgileri gerekiyor. Kullanıcıdan kullanıcı adı-parola ile yeniden oturum açmasını isteyin |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError-hizmet geçici olarak kullanılamıyor. Yeniden deneyin. |
| AADSTS50089 | Akış belirtecinin süresi doldu - Kimlik Doğrulaması Başarısız Oldu. Kullanıcının Kullanıcı adı-parolasıyla yeniden oturum açmayı denemesini sağlayabilirsiniz. |
| AADSTS50097 | DeviceAuthenticationRequired-cihaz kimlik doğrulaması gerekiyor. |
| AADSTS50099 | Pkeyauthınvalidjwtunyetkilendirme-JWT imzası geçersiz. |
| AADSTS50105 | EntitlementGrantsNotFound-oturum açmış olan Kullanıcı, oturum açmış olan uygulama için bir role atanmaz. Kullanıcıyı uygulamaya atayın. Daha fazla bilgi için[https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role):. |
| AADSTS50107 | Invalidrealmuri-istenen Federasyon Bölgesi nesnesi yok. Kiracı yöneticisine başvurun. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat-JWT üstbilgisiyle sorun. Kiracı yöneticisine başvurun. |
| AADSTS50124 | Claimstransformationınvalidınputparameter-talep dönüştürmesi geçersiz giriş parametresi içeriyor. İlke güncelleştirmek için kiracı yöneticisine başvurun. |
| AADSTS50125 | Passwordresetregistrationrequiredınterrupt, parola sıfırlama veya parola kayıt girdisi nedeniyle oturum açma işlemi kesildi. |
| AADSTS50126 | Invalidusernameorpassword-geçersiz Kullanıcı adı veya parola nedeniyle kimlik bilgileri doğrulanırken hata oluştu. |
| AADSTS50127 | Brokerappnotinstall-bu içeriğe erişim kazanmak için kullanıcının bir aracı uygulaması yüklemesi gerekir. |
| AADSTS50128 | Geçersiz etki alanı adı-istekte bulunan veya belirtilen kimlik bilgileri tarafından kapsanan hiçbir kiracı tanımlama bilgisi bulunamadı. |
| AADSTS50129 | Cihazı kaydetmek için Deviceisnotworkplacekatılmış-Workplace JOIN gereklidir. |
| AADSTS50131 | ConditionalAccessFailed-hatalı Windows cihaz durumu, şüpheli etkinlik nedeniyle istek engellendi, erişim ilkesi veya güvenlik ilkesi kararları gibi çeşitli koşullu erişim hataları olduğunu gösterir. |
| AADSTS50132 | SsoArtifactInvalidOrExpired-parola süre sonu veya son parola değişikliği nedeniyle oturum geçerli değil. |
| AADSTS50133 | SsoArtifactRevoked-parola süre sonu veya son parola değişikliği nedeniyle oturum geçerli değil. |
| AADSTS50134 | Deviceflowauthorizeyanlışlıkla Gdatacenter-yanlış veri merkezi. OAuth 2,0 cihaz akışında bir uygulama tarafından başlatılan bir isteği yetkilendirmek için, yetkilendirme tarafı, orijinal isteğin bulunduğu veri merkezinde olmalıdır. |
| AADSTS50135 | Passwordchangecompromısedpassword-hesap riski nedeniyle parola değişikliği gerekiyor. |
| AADSTS50136 | RedirectMsaSessionToApp-tek MSA oturumu algılandı. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken-eksik bir dış yenileme belirteci nedeniyle oturum geçersiz. |
| AADSTS50140 | Kmsiınterrupt-Kullanıcı oturum açarken "Oturumumu Açık bırak" kesmesi nedeniyle bu hata oluştu. Daha fazla bilgi almak için Bağıntı Kimliği, İstek Kimliği ve Hata kodu ile [bir destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS50143 | Oturum uyumsuzluğu-Kullanıcı kiracısı farklı kaynak nedeniyle etki alanı ipucuyla eşleşmediğinden oturum geçersiz. Daha fazla ayrıntı edinmek için bağıntı KIMLIĞI, Istek KIMLIĞI ve hata kodu ile  [bir destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md) . |
| AADSTS50144 | Invalidpasswordexpiredonprempassword-kullanıcının Active Directory parolasının süresi doldu. Kullanıcı için yeni bir parola oluşturun veya kullanıcının parolasını sıfırlamak için self servis sıfırlama aracını kullanmasını sağlayabilirsiniz. |
| AADSTS50146 | MissingCustomSigningKey-bu uygulamanın uygulamaya özgü bir imzalama anahtarıyla yapılandırılması gerekir. Bu şekilde yapılandırılmamış veya anahtarın süresi dolmuş ya da anahtar henüz geçerli değil. |
| AADSTS50147 | MissingCodeChallenge-kod sınama parametresinin boyutu geçerli değil. |
| AADSTS50155 | DeviceAuthenticationFailed-bu kullanıcı için cihaz kimlik doğrulaması başarısız oldu. |
| AADSTS50158 | ExternalSecurityChallenge-dış güvenlik sınaması karşılanmadı. |
| AADSTS50161 | Invalidexternalsecuritybir Geconfiguration-dış sağlayıcı tarafından gönderilen talepler, dış sağlayıcıya istenen yeterli veya eksik talep değil. |
| AADSTS50166 | Externalclaimsproviderkısıtlanıyor-istek, talep sağlayıcısına gönderilemedi. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired-istemci, Windows 10 hesapları uzantısı aracılığıyla bir SSO belirteci elde etme yeteneğine sahip, ancak istekte belirteç bulunamadı veya sağlanan belirtecin geçerliliği bitti. |
| AADSTS50169 | Invalidrequestbadrealm-bölge, geçerli hizmet ad alanının yapılandırılmış bir bölgesi değil. |
| AADSTS50170 | MissingExternalClaimsProviderMapping-dış denetimler eşlemesi eksik. |
| AADSTS50177 | External, Genotsupportedforpassthroughusers-dış sınama, geçiş kullanıcıları için desteklenmez. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers-oturum denetimi, geçiş kullanıcıları için desteklenmez. |
| AADSTS50180 | WindowsIntegratedAuthMissing-tümleşik Windows kimlik doğrulaması gerekiyor. Sorunsuz SSO için kiracıyı etkinleştirin. |
| AADSTS50187 | Deviceınformationnotsaðlanan-hizmet, cihaz kimlik doğrulaması yapamadı. |
| AADSTS51000 | RequiredFeatureNotEnabled-özellik devre dışı bırakıldı. |
| AADSTS51001 | Domainhintmustbesun-Domain ipucu, şirket içi güvenlik tanımlayıcısı veya şirket içi UPN ile birlikte bulunmalıdır. |
| AADSTS51004 | UserAccountNotInDirectory-Kullanıcı hesabı dizinde yok. |
| AADSTS51005 | TemporaryRedirect-istenen bilgilerin konum üstbilgisinde belirtilen URI 'de bulunduğunu gösteren HTTP durum 307 ' e eşdeğerdir. Bu durumu aldığınızda, Yanıtla ilişkili konum başlığını izleyin. Özgün istek yöntemi gönderilirken, yeniden yönlendirilen istek POST yöntemini de kullanacaktır. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth-tümleşik Windows kimlik doğrulaması gerekir. Kullanıcı, tümleşik Windows kimlik doğrulaması talebi eksik olan bir oturum belirteci kullanarak oturum açtı. Kullanıcıyı yeniden oturum açmasını isteyin. |
| AADSTS52004 | Delegationyok Notexistforlinkedın-Kullanıcı LinkedIn kaynaklarına erişim onayı sağlamamıştır. |
| AADSTS53000 | Devicenotuyumlu-koşullu erişim ilkesi için uyumlu bir cihaz gerekir ve cihaz uyumlu değildir. Kullanıcı, cihazlarını Intune gibi onaylanmış bir MDM sağlayıcısına kaydetmelidir. |
| AADSTS53001 | Devicenotdomainkatılmış-koşullu erişim ilkesi, etki alanına katılmış bir cihaz gerektirir ve cihaz etki alanına katılmış değildir. Kullanıcının etki alanına katılmış bir cihaz kullanmasını sağlayabilirsiniz. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp-kullanılan uygulama, koşullu erişim için onaylanan bir uygulama değil. Kullanıcının erişim sağlamak için kullanılacak onaylanan uygulamalar listesinden uygulamalardan birini kullanması gerekir. |
| AADSTS53003 | BlockedByConditionalAccess-erişim koşullu erişim ilkeleri tarafından engellendi. Erişim ilkesi belirteç verilmesine izin vermiyor. |
| AADSTS53004 | Redakupblockedduetorisk-bu içeriğe erişmeden önce kullanıcının Multi-Factor Authentication kayıt işlemini tamamlaması gerekir. Kullanıcının çok faktörlü kimlik doğrulamasına kaydolması gerekir. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | Delegationsuz notexist-Kullanıcı veya yönetici, KIMLIĞI X olan uygulamayı kullanmaya Medi. bu kullanıcı ve kaynak için etkileşimli bir yetkilendirme isteği gönderin. |
| AADSTS65004 | Userdeclinedonay-Kullanıcı uygulamaya erişim iznini reddetti. Kullanıcıdan oturum açmayı yeniden denemesini ve uygulamaya izin vermesini isteyin|
| AADSTS65005 | Hatalı Configuredapplication-uygulama gerekli kaynak erişim listesi kaynak tarafından bulunabilecek uygulamaları içermiyor veya istemci uygulaması, gerekli kaynak erişim listesinde veya grafik hizmetinde belirtilmeyen kaynak erişimi istedi istek veya kaynak bulunamadı. Uygulama SAML destekliyorsa, uygulamayı yanlış tanımlayıcıyla (varlık) yapılandırmış olabilirsiniz. Aşağıdaki bağlantıyı kullanarak SAML için listelenen çözümü deneyin: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav) |
| AADSTS67003 | Actornotvalidserviceıdentity |
| AADSTS70000 | Invalidgrant-kimlik doğrulaması başarısız oldu. Yenileme belirteci geçerli değil. Hatanın nedeni aşağıdakilerden biri olabilir:<ul><li>Belirteç bağlama üst bilgisi boş</li><li>Belirteç bağlama karması eşleşmiyor</li></ul> |
| AADSTS70001 | UnauthorizedClient-uygulama devre dışı bırakıldı. |
| AADSTS70002 | Invalidclient-kimlik bilgileri doğrulanırken hata oluştu. Belirtilen client_secret, bu istemci için beklenen değerle eşleşmiyor. Client_secret düzeltip yeniden deneyin. Daha fazla bilgi için bkz. [erişim belirteci istemek için yetkilendirme kodunu kullanma](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType-uygulama, desteklenmeyen bir izin türü döndürdü. |
| AADSTS70004 | Invalidredirecturi-uygulama geçersiz bir yeniden yönlendirme URI 'SI döndürdü. İstemci tarafından belirtilen yeniden yönlendirme adresi, yapılandırılmış bir adresle veya OIDC onay listesindeki herhangi bir adresle eşleşmiyor. |
| AADSTS70005 | UnsupportedResponseType-uygulama, aşağıdaki nedenlerden dolayı desteklenmeyen bir yanıt türü döndürdü:<ul><li>' token ' yanıt türü uygulama için etkin değil</li><li>'id_token' yanıt türü 'OpenID' kapsamı gerektiriyor -kodlanmış wctx içinde desteklenmeyen bir OAuth parametre değeri içeriyor</li></ul> |
| AADSTS70007 | Unsupportedresponsemode-uygulama, belirteç istenirken desteklenmeyen bir değer `response_mode` döndürdü.  |
| AADSTS70008 | ExpiredOrRevokedGrant-yenileme belirtecinin süresi eylemsizlik nedeniyle doldu. Belirteç XXX 'de verildi ve belirli bir süre boyunca etkin değil. |
| AADSTS70011 | Invalidscope-uygulama tarafından istenen kapsam geçersiz. |
| AADSTS70012 | MsaServerError-MSA (tüketici) kullanıcısının kimliği doğrulanırken bir sunucu hatası oluştu. Yeniden deneyin. Başarısız olmaya devam ederse, [bir destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending-OAuth 2,0 cihaz akışı hatası. Yetkilendirme beklemede. Cihaz isteği yoklamayı yeniden deneyecek. |
| AADSTS70018 | Baddoğrulamaları ıationcode-Kullanıcı, cihaz kodu akışı için yanlış Kullanıcı koduna yazma nedeniyle geçersiz doğrulama kodu. Yetkilendirme onaylanmamış. |
| AADSTS70019 | Codesüre sonu-doğrulama kodu zaman aşımına uğradı. Kullanıcının oturum açma işlemini yeniden denemesini sağlayabilirsiniz. |
| AADSTS75001 | BindingSerializationError-SAML ileti bağlama sırasında bir hata oluştu. |
| AADSTS75003 | UnsupportedBindingError-uygulama desteklenmeyen bağlama ile ilgili bir hata döndürdü (SAML protokol yanıtı HTTP POST dışında bağlamalar aracılığıyla gönderilemez). |
| AADSTS75005 | Saml2MessageInvalid-Azure AD, SSO için uygulama tarafından gönderilen SAML isteğini desteklemez. |
| AADSTS75008 | RequestDeniedError-SAML isteğinde beklenmeyen bir hedef bulunduğundan, uygulamadan gelen istek reddedildi. |
| AADSTS75011 | Nomatchedauthncontexınoutputclaim-hizmet ile kimliği doğrulanmış kullanıcı tarafından istenen kimlik doğrulama yöntemiyle eşleşmeyen kimlik doğrulama yöntemi. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy-SAML2 kimlik doğrulama Isteğinde geçersiz Nameıdpolicy yok. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable-kimlik doğrulama Aracısı Active Directory bağlanamıyor. Aracı sunucularının, parolalarının doğrulanması gereken kullanıcılarla aynı AD ormanının üyesi olduğundan ve Active Directory bağlanabildiklerinden emin olun. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout-parola doğrulama isteği zaman aşımına uğradı. Active Directory kullanılabilir olduğundan ve aracılardan gelen isteklere yanıt verdiğinden emin olun. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException-kimlik doğrulama aracısından yanıt işlenirken bilinmeyen bir hata oluştu. İsteği yeniden deneyin. Başarısız olmaya devam ederse hata hakkında daha fazla bilgi edinmek için [bir destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md) . |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem-kimlik doğrulama Aracısı kullanıcının parolasını doğrulayamıyor. Daha fazla bilgi için aracı günlüklerine bakın ve Active Directory beklendiği gibi çalıştığını doğrulayın. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException-kimlik doğrulama Aracısı parolanın şifresini çözemedi. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours-kullanıcılar izin verilen saatlerin dışında oturum açmaya çalıştı (AD 'de belirtilir). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew-kimlik doğrulama denemesi, kimlik doğrulama Aracısı ve AD çalıştıran makine arasındaki zaman çarpıklığı nedeniyle tamamlanamadı. Zaman eşitleme sorunlarını giderin. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated-Kerberos kimlik doğrulaması girişimi başarısız oldu. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported-kimlik doğrulama paketi desteklenmiyor. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader-yetkilendirme üst bilgisi bulunamadı. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn-kiracı sorunsuz SSO için etkinleştirilmemiş. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat-kullanıcının Kerberos bileti doğrulanamıyor. |
| AADSTS81010 | Desktopssoauthtokengeçersiz-kullanıcının Kerberos anahtarının süresi sona erdiği veya geçersiz olduğu için sorunsuz SSO başarısız oldu. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed-kullanıcının Kerberos anahtarındaki bilgilere göre Kullanıcı nesnesi bulunamıyor. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn-Azure AD 'de oturum açmaya çalışan Kullanıcı, cihazda oturum açmış olan kullanıcıdan farklı. |
| AADSTS90002 | Invalidtenantname-kiracı adı veri deposunda bulunamadı. Doğru kiracı KIMLIĞINE sahip olduğunuzdan emin olun. |
| AADSTS90004 | Invalidrequestformat-istek düzgün biçimlendirilmedi. |
| AADSTS90005 | Invalidrequestwithmultiplerequirements-istek tamamlanamıyor. Tanımlayıcı ve oturum açma ipucu birlikte kullanılmadığından istek geçerli değil.  |
| AADSTS90006 | ExternalServerRetryableError-hizmet geçici olarak kullanılamıyor.|
| AADSTS90007 | Invalidsessionıd-hatalı istek. Geçirilen oturum KIMLIĞI ayrıştırılamıyor. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission-Kullanıcı veya yönetici uygulamayı kullanmaya yönelik değil. En azından, uygulamanın oturum açma ve kullanıcı profilini okuma izni belirtilerek Azure AD 'ye erişmesi gerekir. |
| AADSTS90009 | Tokenforitselfmissingidenticalappıdentifier-uygulama kendisi için bir belirteç istiyor. Bu senaryo yalnızca belirtilen kaynak GUID tabanlı uygulama KIMLIĞI kullanıyorsa desteklenir. |
| AADSTS90010 | NotSupported-algoritma oluşturulamıyor. |
| AADSTS90012 | RequestTimeout-istenen zaman aşımına uğradı. |
| AADSTS90013 | Invaliduserınput-kullanıcının girişi geçerli değil. |
| AADSTS90014 | MissingRequiredField-bu hata kodu, kimlik bilgilerinde beklenen bir alan bulunmadığı çeşitli durumlarda görünebilir. |
| AADSTS90015 | QueryStringTooLong-sorgu dizesi çok uzun. |
| AADSTS90016 | MissingRequiredClaim-erişim belirteci geçerli değil. Gerekli talep eksik. |
| AADSTS90019 | MissingTenantRealm-Azure AD, istekten kiracı tanımlayıcısını belirleyemedi. |
| AADSTS90022 | Authenticatedınvalidprincipalnameformat-asıl ad biçimi geçerli değil veya beklenen `name[/host][@realm]` biçimi karşılamıyor. Asıl ad gereklidir, ana bilgisayar ve bölge isteğe bağlıdır ve null olarak ayarlanabilir. |
| AADSTS90023 | Invalidrequest-kimlik doğrulama hizmeti isteği geçerli değil. |
| AADSTS9002313 | Invalidrequest-Istek hatalı oluşturulmuş veya geçersiz. -Burada sorun, belirli bir uç nokta isteğiyle ilgili bir sorun olduğu için verilmiştir. Bu sorunun önerisi, oluşan hatanın Fiddler izlemesini almak ve isteğin gerçekten doğru şekilde biçimlendirilip biçimlendirilmediğini görmek için. |
| AADSTS90024 | Requestdostgetexceedebir-geçici bir hata oluştu. Yeniden deneyin. |
| AADSTS90033 | MsodsServiceUnavailable-Microsoft çevrimiçi dizin hizmeti (MSODS) kullanılamıyor. |
| AADSTS90036 | MsodsServiceUnretryableFailure-MSODS tarafından barındırılan WCF hizmetinden beklenmedik, yeniden denenmeyen bir hata oluştu. Hata hakkında daha fazla bilgi almak için [bir destek bileti açın](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS90038 | Ülke Alcloudtenantredirection-belirtilen ' Y ' kiracısı, Ulusal ' X ' bulutuna aittir. Geçerli ' Z ' bulut örneği X ile federasyona eklemez. Bir bulut yeniden yönlendirme hatası döndürüldü. |
| AADSTS90043 | Ülke Alclouduuthcoderedirection-özellik devre dışı. |
| AADSTS90051 | Invalidülke Alcloudıd-Ulusal bulut tanımlayıcısı geçersiz bir bulut tanımlayıcısı içeriyor. |
| AADSTS90055 | Tenantkısıtlar Lingerror-çok fazla gelen istek var. Bu özel durum engellenen kiracılar için oluşturulur. |
| AADSTS90056 | Badresourcerequest-bir erişim belirtecinin kodunu kullanmak için, uygulamanın `/token` uç noktaya bir post isteği gönderilmesi gerekir. Ayrıca, bundan önce bir yetkilendirme kodu sağlamalı ve bunu `/token` uç noktaya gönder isteğinde göndermeniz gerekir. OAuth 2,0 yetkilendirme kodu akışına genel bakış için bu makaleye başvurun: [https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code). Kullanıcıyı bir authorization_code döndürecek `/authorize` uç noktaya yönlendirin. `/token` Uç noktaya istek göndererek, Kullanıcı erişim belirtecini alır. Azure portal oturum açın ve iki uç noktanın doğru şekilde yapılandırıldığını onaylamak için **Uygulama kayıtları > uç noktalarına** bakın. |
| AADSTS90072 | PassThroughUserMfaError-kullanıcının oturum açtığı kiracı üzerinde oturum açtığı dış hesap yok; Böylece Kullanıcı kiracının MFA gereksinimlerini karşılayamaz. Hesabın, önce kiracıda bir dış Kullanıcı olarak eklenmesi gerekir. Oturumu kapatın ve farklı bir Azure AD kullanıcı hesabıyla oturum açın. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid-hizmet bir WS-Federation iletisini işlemeye çalışırken hata oluştu. İleti geçerli değil. |
| AADSTS90082 | OrgIdWsFederationNotSupported-istek için seçilen kimlik doğrulama ilkesi Şu anda desteklenmiyor. |
| AADSTS90084 | Bu site için OrgIdWsFederationGuestNotAllowed-Guest hesaplarına izin verilmez. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed-şirket nesnesi henüz sağlanmadığından hizmet bir belirteç veremez. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired-Kullanıcı DA belirteci zaman aşımına uğradı. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed-URI 'den WS-Federation iletisi oluşturulurken bir hata oluştu. |
| AADSTS90090 | GraphRetryableError-hizmet geçici olarak kullanılamıyor. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | Graphuseryetkilendirilmemiş-Graph, istek için yasaklanmış bir hata koduyla döndürüldü. |
| AADSTS90094 | AdminConsentRequired-yönetici onayı gerekiyor. |
| AADSTS90100 | Invalidrequestparameter-parametre boş veya geçerli değil. |
| AADSTS901002 | AADSTS901002: ' Resource ' istek parametresi desteklenmiyor. |
| AADSTS90101 | Invalidemapostano-sağlanan veriler geçerli bir e-posta adresi değil. E-posta adresi biçiminde `someone@example.com`olmalıdır. |
| AADSTS90102 | Invaliduriparameter-değer geçerli bir mutlak URI olmalıdır. |
| AADSTS90107 | Invalidxml-istek geçerli değil. Verilerinizde geçersiz karakterler bulunmadığından emin olun.|
| AADSTS90114 | Invalidexpıryıdate-toplu belirtecin süre sonu zaman damgası, süresi dolmasının verilmemesine neden olur. |
| AADSTS90117 | Invalidrequestınput |
| AADSTS90119 | Invalidusercode-Kullanıcı kodu null veya boş.|
| AADSTS90120 | Invaliddeviceflowrequest-istek zaten yetkilendirildi veya reddedildi. |
| AADSTS90121 | Invalidemptyrequest-geçersiz boş istek.|
| AADSTS90123 | Identityprovideraccessreddedildi-kimlik veya talep verme sağlayıcısı isteği reddettiğinden belirteç verilemez. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported-kaynak, `/common` veya `/consumers` uç noktalar üzerinde desteklenmez. Bunun yerine `/organizations` veya kiracıya özgü uç noktasını kullanın. |
| AADSTS90125 | Debugmodekayıtlarını Tenantnotfound-Kullanıcı sistemde yok. Kullanıcı adını doğru girdiğinizden emin olun. |
| AADSTS90126 | Debugmodekayıtlarını Tenantnotçıkarılan-Kullanıcı türü bu uç noktada desteklenmiyor. Sistem Kullanıcı adından kullanıcının kiracısını çıkarsanamıyor. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported-uygulama `/common` veya `/consumers` uç noktalar üzerinde desteklenmez. Bunun yerine `/organizations` veya kiracıya özgü uç noktasını kullanın. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | Passwordchangeınvalidnewpasswordzayıf |
| AADSTS120003 | Passwordchangeınvalidnewpasswordcontainsmembername |
| AADSTS120004 | Passwordchangeonpremkarmaşıklık |
| AADSTS120005 | Passwordchangeonpremskscmonfail |
| AADSTS120008 | Passwordchangeasyncjobstatesonlandırılmış-yeniden denenmesiz bir hata oluştu.|
| AADSTS120011 | Passwordchangeasyncupnınencefailed başarısız oldu |
| AADSTS120012 | Passwordchangeneedstomıenonpred |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | Passwordchangepasswordparolasız Notcomplybelirsizlik Zypolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | Partnerservicessprınternalserviceerror |
| AADSTS130004 | NgcKeyNotFound-Kullanıcı sorumlusu, yapılandırılmış NGC KIMLIK anahtarına sahip değil. |
| AADSTS130005 | NgcInvalidSignature-NGC anahtar imzası doğrulandı.|
| AADSTS130006 | NgcTransportKeyNotFound-bu, cihazda NGC Aktarım anahtarı yapılandırılmadı. |
| AADSTS130007 | NgcDeviceIsDisabled-cihaz devre dışı bırakıldı. |
| AADSTS130008 | NgcDeviceIsNotFound-NGC anahtarı tarafından başvurulan cihaz bulunamadı. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | Invalidrequestnonce-Istek nonce sağlanmadı. |
| AADSTS140001 | Invalidsessionkey-oturum anahtarı geçerli değil.|
| AADSTS165900 | Invalidapirequest-geçersiz istek. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion-Chrome WebView sürümü desteklenmiyor. |
| AADSTS220501 | Invalidcrldownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource-kaynak yalnızca cihaz belirteçlerini kabul edecek şekilde yapılandırılmadı. |
| AADSTS240001 | BulkAADJTokenUnauthorized-Kullanıcı Azure AD 'ye cihaz kaydetme yetkisine sahip değil. |
| AADSTS240002 | Requiredclaimısmissing-id_token, Grant olarak `urn:ietf:params:oauth:grant-type:jwt-bearer` kullanılamaz.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy-kiracı yöneticisi, bu isteği engelleyen bir güvenlik ilkesi yapılandırmıştır. İsteğiniz ilke gereksinimlerini karşılayıp karşılamadığını öğrenmek için kiracı düzeyinde tanımlanan güvenlik ilkelerini kontrol edin. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest-uygulama dizinde/kiracıda bulunamadı. Bu durum, uygulama kiracının Yöneticisi tarafından yüklenmediyse veya kiracının kiracısındaki herhangi bir kullanıcı tarafından ' a onaylı olması durumunda gerçekleşebilir. Uygulamanın tanımlayıcı değerini yanlış yapılandırılmış veya kimlik doğrulama isteğinizi yanlış kiracıya göndermiş olabilirsiniz. |
| AADSTS700020 | Interactionrequired-erişim izni etkileşim gerektiriyor. |
| AADSTS700022 | Invalidmultipleresourcesscope-giriş parametresi kapsamı için girilen değer, birden fazla kaynak içerdiğinden geçerli değil. |
| AADSTS700023 | Invalidresourcelessscope-girilen parametre kapsamı için girilen değer, erişim belirteci isteğinde geçerli değildir. |
| AADSTS1000000 | UserNotBoundError-bağlama API 'SI, Azure AD kullanıcısının, henüz gerçekleşmemiş bir dış ıDP ile kimlik doğrulaması yapmasını gerektirir. |
| AADSTS1000002 | BindCompleteInterruptError-bağlama başarıyla tamamlandı, ancak kullanıcının bilgilendirilmesi gerekiyor. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled-uygulama devre dışı bırakıldı. |

## <a name="next-steps"></a>Sonraki adımlar

* Sorunuz mu var? ya da aradığınızı bulamıyor musunuz? Bir GitHub sorunu oluşturun veya geliştiricilerin yardım ve destek alma hakkında daha fazla bilgi edinmek için [destek ve yardım seçenekleri](active-directory-develop-help-support.md) bölümüne bakın.
