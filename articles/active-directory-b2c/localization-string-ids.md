---
title: Yerelleştirme dize kimlikleri-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C bir özel ilkede API. signuporsignıd KIMLIĞINE sahip bir içerik tanımının kimliklerini belirtin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b60b447d8302b89813ca462c3220603ef926eb26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631192"
---
# <a name="localization-string-ids"></a>Yerelleştirme dizesi kimlikleri

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Yerelleştirme** öğesi, Kullanıcı yolculukları için ilkedeki birden çok yerel ayarı veya dili desteklemenize olanak sağlar. Bu makale, ilkenizde kullanabileceğiniz yerelleştirme kimliklerinin listesini sağlar. UI yerelleştirmesini öğrenmek için bkz. [Yerelleştirme](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Kaydolma veya oturum açma sayfası öğeleri

KIMLIĞI `api.signuporsignin` ve [kendini onaylanan teknik profiliyle](self-asserted-technical-profile.md)bir içerik tanımı için aşağıdaki kimlikler kullanılır.

| ID | Varsayılan değer | Sayfa düzeni sürümü |
| -- | ------------- | ------ |
| **forgotpassword_link** | Parolanızı mı unuttunuz? | `All` |
| **createaccount_intro** | Hesabınız yok mu? | `All` |
| **button_signin** | Oturum açın | `All` |
| **social_intro** | Sosyal hesabınızla oturum açın | `All` |
| **remember_me** |Oturumumu Açık tut. | `All` |
| **unknown_error** | Oturumunuzu açmada sorun yaşıyoruz. Lütfen daha sonra yeniden deneyin. | `All` |
| **divider_title** | VEYA | `All` |
| **local_intro_email** | Mevcut hesabınızla oturum açın | `< 2.0.0` |
| **logonIdentifier_email** | E-posta Adresi | `< 2.0.0` |
| **requiredField_email** | Lütfen e-postanızı girin | `< 2.0.0` |
| **invalid_email** | Lütfen geçerli bir e-posta adresi girin | `< 2.0.0` |
| **email_pattern** | ^ [a-zA-Z0-9.! # $% & ' ' \* +/=? ^ \_ \` { \| } ~-] + @ [a-Za-z0-9-] + (?: \\ . [ a-zA-Z0-9-] +) \* $ |`< 2.0.0` |
| **local_intro_username** | Kullanıcı adınızla oturum açın | `< 2.0.0` |
| **logonIdentifier_username** | Kullanıcı adı | `< 2.0.0` |
| **requiredField_username** | Lütfen Kullanıcı adınızı girin | `< 2.0.0` |
| **parola** | Parola | `< 2.0.0` |
| **requiredField_password** | Lütfen parolanızı girin | `< 2.0.0` |
| **createaccount_link** | Hemen kaydolun | `< 2.0.0` |
| **cancel_message** | Kullanıcı parolasını unutuldu | `< 2.0.0` |
| **invalid_password** | Girdiğiniz parola beklenen biçimde değil. | `< 2.0.0` |
| **createaccount_one_link** | Hemen kaydolun | `>= 2.0.0` |
| **createaccount_two_links** | Veya ile kaydolun {0}{1} | `>= 2.0.0` |
| **createaccount_three_links** | {0}, Veya ile kaydolun {1}{2} | `>= 2.0.0` |
| **local_intro_generic** | İle oturum açın {0} | `>= 2.1.0` |
| **requiredField_generic** | Lütfen parolanızı girin {0} | `>= 2.1.0` |
| **invalid_generic** | Lütfen geçerli bir {0} | `>= 2.1.1` |
| **başlığıyla** | Oturum açın | `>= 2.1.1` |


> [!NOTE]
> * Gibi yer tutucular {0} , değeri ile otomatik olarak doldurulur `DisplayName` `ClaimType` . 
> * Yerelleştirmeye yönelik bilgi edinmek için `ClaimType` bkz. [kaydolma veya oturum açma örneği](#signupsigninexample).

Aşağıdaki örnek, kayıt veya oturum açma sayfasında bazı Kullanıcı arabirimi öğelerinin kullanımını gösterir:

:::image type="content" source="./media/localization-string-ids/localization-susi-2.png" alt-text="Kaydolma veya oturum açma sayfası U X öğelerini gösteren ekran görüntüsü.":::

### <a name="sign-up-or-sign-in-identity-providers"></a>Kaydolma veya oturum açma kimlik sağlayıcıları

Kimlik sağlayıcılarının KIMLIĞI, Kullanıcı yolculuğu  **Claimsexchange** öğesinde yapılandırılır. Kimlik sağlayıcısının başlığını yerelleştirmek için, **ElementType** olarak ayarlanır `ClaimsProvider` , **strıngıd** , kimliğine ayarlanır `ClaimsExchange` .

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Aşağıdaki örnek, Facebook kimlik sağlayıcısını Arapça olarak yerelleştirir:

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Kaydolma veya oturum açma hata iletileri

| ID | Varsayılan değer |
| -- | ------------- |
| **Usermessageifınvalidpassword** | Parolanız yanlış. |
| **UserMessageIfPasswordExpired**| Parolanızın süresi doldu.|
| **UserMessageIfClaimsPrincipalDoesNotExist** | Hesabınızı bulamıyoruz. |
| **UserMessageIfOldPasswordUsed** | Eski bir parola kullandınız gibi görünüyor. |
| **DefaultMessage** | Geçersiz Kullanıcı adı veya parola. |
| **UserMessageIfUserAccountDisabled** | Hesabınız kilitlendi. Dosyanın kilidini açmak için destek sorumlunuza başvurun, sonra yeniden deneyin. |
| **Usermessageifuseraccountkilitlendi** | Hesabınız yetkisiz kullanımı engellemek için geçici olarak kilitlidir. Daha sonra tekrar deneyin. |
| **Aadrequestskısıtlanıyor** | Şu anda çok fazla istek var. Lütfen bir süre bekleyip yeniden deneyin. |

<a name="signupsigninexample"></a>
### <a name="sign-up-or-sign-in-example"></a>Kaydolma veya oturum açma örneği

```xml
<LocalizedResources Id="api.signuporsignin.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="heading">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="social_intro">Sign in with your social account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_generic">Sign in with your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_password">Please enter your password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_generic">Please enter your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_generic">Please enter a valid {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_one_link">Sign up now</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_two_links">Sign up with {0} or {1}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_three_links">Sign up with {0}, {1}, or {2}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">Forgot your password?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_signin">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="divider_title">OR</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="unknown_error">We are having trouble signing you in. Please try again later.</LocalizedString>
    <!-- Uncomment the remember_me only if the keep me signed in is activated. 
    <LocalizedString ElementType="UxElement" StringId="remember_me">Keep me signed in</LocalizedString> -->
    <LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfPasswordExpired">Your password has expired.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">Looks like you used an old password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">Invalid username or password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Kaydolma ve kendiliğinden onaylanan sayfalar Kullanıcı arabirimi öğeleri

Aşağıdakiler, `api.localaccountsignup` ve gibi, ile başlayan bir içerik TANıMıNıN kimliği veya `api.selfasserted` , ve `api.selfasserted.profileupdate` `api.localaccountpasswordreset` [kendi kendini onaylanan teknik profili](self-asserted-technical-profile.md)olan bir içerik tanımı için olan kimliklerdir.

| ID | Varsayılan değer |
| -- | ------------- |
| **ver_sent** | Doğrulama kodu gönderildi: |
| **ver_but_default** | Varsayılan |
| **cancel_message** | Kullanıcı kendi kendine onaylanan bilgileri girmeyi iptal etti |
| **preloader_alt** | Lütfen bekleyin |
| **ver_but_send** | Doğrulama kodu gönder |
| **alert_yes** | Yes |
| **error_fieldIncorrect** | Bir veya daha fazla alan yanlış doldurulmuş. Lütfen girişlerinizi denetleyin ve yeniden deneyin. |
| **yıl** | Yıl |
| **verifying_blurb** | Bilgilerinizi işlerken lütfen bekleyin. |
| **button_cancel** | İptal |
| **ver_fail_no_retry** | Çok fazla hatalı deneme yaptınız. Lütfen daha sonra yeniden deneyin. |
| **başından** | Ay |
| **ver_success_msg** | E-posta adresi doğrulandı. Artık devam edebilirsiniz. |
| **aylar** | Ocak, Şubat, Mart, Nisan, Mayıs, Haziran, Temmuz, Ağustos, Eylül, Ekim, Kasım, Aralık |
| **ver_fail_server** | E-posta adresinizi doğrularken sorun yaşıyoruz. Lütfen geçerli bir e-posta adresi girin ve yeniden deneyin. |
| **error_requiredFieldMissing** | Gerekli bir alan eksik. Lütfen tüm gerekli alanları doldurun ve yeniden deneyin. |
| **initial_intro** | Lütfen aşağıdaki ayrıntıları sağlayın. |
| **ver_but_resend** | Yeni kod gönder |
| **button_continue** | Oluştur |
| **error_passwordEntryMismatch** | Parola girişi alanları eşleşmiyor. Lütfen her iki alana da aynı parolayı girip yeniden deneyin. |
| **ver_incorrect_format** | Hatalı biçim. |
| **ver_but_edit** | E-postayı değiştir |
| **ver_but_verify** | Kodu doğrula |
| **alert_no** | No |
| **ver_info_msg** | Doğrulama kodu gelen kutunuza gönderildi. Lütfen aşağıdaki giriş kutusuna kopyalayın. |
| **günündeki** | Gün |
| **ver_fail_throttled** | Bu e-posta adresini doğrulamak için çok fazla istek vardı. Lütfen bekleyin, sonra yeniden deneyin. |
| **helplink_text** | Bu nedir? |
| **ver_fail_retry** | Bu kod yanlış. Lütfen tekrar deneyin. |
| **alert_title** | Ayrıntılarınızı girmeyi iptal edin |
| **required_field** | Bu bilgiler gereklidir. |
| **alert_message** | Ayrıntılarınızı girmeyi iptal etmek istediğinizden emin misiniz? |
| **ver_intro_msg** | Doğrulama gereklidir. Lütfen gönder düğmesine tıklayın. |
| **ver_input** | Doğrulama kodu |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Kaydolma ve kendiliğinden onaylanan sayfalar hata iletileri

| ID | Varsayılan değer |
| -- | ------------- |
| **Usermessageifclaimsprincıpalalreadyexists** | Belirtilen KIMLIĞE sahip bir kullanıcı zaten var. Lütfen farklı bir tane seçin. |
| **Usermessageifclaimnotdoğrulandı** | Talep doğrulanmadı: {0} |
| **UserMessageIfIncorrectPattern** | İçin yanlış desenler: {0} |
| **UserMessageIfMissingRequiredElement** | Gerekli öğe eksik: {0} |
| **UserMessageIfValidationError** | Doğrulamada hata: {0} |
| **Usermessageifınvalidınput** | {0} geçersiz giriş içeriyor. |
| **Servicekısıtlanıyor** | Şu anda çok fazla istek var. Lütfen bir süre bekleyip yeniden deneyin. |

Aşağıdaki örnek, kaydolma sayfasında bazı Kullanıcı arabirimi öğelerinin kullanımını gösterir:

![Kullanıcı arabirimi öğesi adları etiketli kaydolma sayfası](./media/localization-string-ids/localization-sign-up.png)

Aşağıdaki örnek, Kullanıcı doğrulama kodu gönder düğmesine tıkladıktan sonra kaydolma sayfasındaki bazı Kullanıcı arabirimi öğelerinin kullanımını gösterir:

![Kaydolma sayfası eposta doğrulaması UX öğeleri](./media/localization-string-ids/localization-email-verification.png)

## <a name="sign-up-and-self-asserted-pages-example"></a>Kaydolma ve otomatik olarak onaylanan sayfalar örneği

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Email address that can be used to contact you.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">Enter new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">Confirm New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">Confirm new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">Display Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">Your display name.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">Surname</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">Your surname (also known as family name or last name).</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">Given Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">Your given name (also known as first name).</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">A required field is missing. Please fill out all required fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="helplink_text">What is this?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please provide the following details.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="preloader_alt">Please wait</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="required_field">This information is required.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_edit">Change e-mail</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_resend">Send new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_send">Send verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_verify">Verify code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">That code is incorrect. Please try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_server">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_info_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_input">Verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_success_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">Claim not verified: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">A user with the specified ID already exists. Please choose a different one.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">Incorrect pattern for: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">{0} has invalid input.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">Missing required element: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">Error in validation by: {0}</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="phone-factor-authentication-page-user-interface-elements"></a>Telefon faktörü kimlik doğrulama sayfası kullanıcı arabirimi öğeleri

Aşağıda KIMLIĞI `api.phonefactor` ve [Telefon faktörü teknik profiliyle](phone-factor-technical-profile.md)bir içerik tanımının kimlikleri verilmiştir.

| ID | Varsayılan değer |
| -- | ------------- |
| **button_verify** | Beni ara |
| **country_code_label** | Ülke Kodu |
| **cancel_message** | Kullanıcı Multi-Factor Authentication 'ı iptal etti |
| **text_button_send_second_code** | Yeni kod gönder |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Sizin için kayıt üzerinde aşağıdaki sayıyı sunuyoruz. Kimliğinizi doğrulamak için SMS veya Phone aracılığıyla kod gönderebiliriz. |
| **intro_mixed_p** | Sizin için kayıt için aşağıdaki numaralara ihtiyacımız var. Kimliğinizi doğrulamak için SMS aracılığıyla telefon ve kod gönderebileceğimiz bir sayı seçin. |
| **button_verify_code** | Kodu doğrula |
| **requiredField_code** | Lütfen aldığınız doğrulama kodunu girin |
| **invalid_code** | Lütfen aldığınız 6 basamaklı kodu girin |
| **button_cancel** | İptal |
| **local_number_input_placeholder_text** | Telefon numarası |
| **button_retry** | Yeniden Dene |
| **alternative_text** | Telefonum yok |
| **intro_phone_p** | Sizin için kayıt için aşağıdaki numaralara ihtiyacımız var. Kimliğinizi doğrulamak için telefon yapabilmemiz gereken bir sayı seçin. |
| **intro_phone** | Sizin için kayıt üzerinde aşağıdaki sayıyı sunuyoruz. Kimliğinizi doğrulamak için telefon göndereceğiz. |
| **enter_code_text_intro** | Doğrulama kodunuzu aşağıya girin veya  |
| **intro_entry_phone** | Kimliğinizi doğrulamak için telefon yapabilmemiz gereken bir sayı girin. |
| **intro_entry_sms** | Kimliğinizi doğrulamak için SMS aracılığıyla kod gönderebileceğimiz bir sayı girin. |
| **button_send_code** | Kodu gönder |
| **invalid_number** | Lütfen geçerli bir telefon numarası girin |
| **intro_sms** | Sizin için kayıt üzerinde aşağıdaki sayıyı sunuyoruz. Kimliğinizi doğrulamak için SMS aracılığıyla bir kod göndereceğiz. |
| **intro_entry_mixed** | Kimliğinizi doğrulamak için SMS veya Phone aracılığıyla kod gönderebileceğimiz bir sayı girin. |
| **number_pattern** | ^\\+ (?: [0-9] [ \\ x20-]?) {6,14} [0-9] $ |
| **intro_sms_p** |Sizin için kayıt için aşağıdaki numaralara ihtiyacımız var. Kimliğinizi doğrulamak için SMS aracılığıyla kod gönderebileceğimiz bir sayı seçin. |
| **requiredField_countryCode** | Lütfen ülke kodunuzu seçin |
| **requiredField_number** | Lütfen telefon numaranızı girin |
| **country_code_input_placeholder_text** |Ülke veya bölge |
| **number_label** | Telefon Numarası |
| **error_tryagain** | Verdiğiniz telefon numarası meşgul veya kullanılamıyor. Lütfen numarayı denetleyip yeniden deneyin. |
| **error_incorrect_code** | Girdiğiniz doğrulama kodu kayıtlarımızla eşleşmiyor. Lütfen yeniden deneyin veya yeni bir kod isteyin. |
| **countryList** | [Ülkeler listesini](#phone-factor-authentication-page-example)görüntüleyin. |
| **error_448** | Verdiğiniz telefon numarasına ulaşılamıyor. |
| **error_449** | Kullanıcı yeniden deneme girişimlerinin sayısını aştı. |
| **verification_code_input_placeholder_text** | Doğrulama kodu |

Aşağıdaki örnek, MFA kayıt sayfasında bazı Kullanıcı arabirimi öğelerinin kullanımını gösterir:

![Telefon faktörü kimlik doğrulama kaydı UX öğeleri](./media/localization-string-ids/localization-mfa1.png)

Aşağıdaki örnek, MFA doğrulama sayfasında bazı Kullanıcı arabirimi öğelerinin kullanımını gösterir:

![Telefon faktörü kimlik doğrulaması doğrulama UX öğeleri](./media/localization-string-ids/localization-mfa2.png)

## <a name="phone-factor-authentication-page-example"></a>Telefon faktörü kimlik doğrulama sayfası örneği

```xml
<LocalizedResources Id="api.phonefactor.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="button_verify">Call Me</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_label">Country Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has canceled multi-factor authentication</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="text_button_send_second_code">Send a new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="code_pattern">\d{6}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed">We have the following number on record for you. We can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed_p">We have the following numbers on record for you. Choose a number that we can phone or send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_code">Please enter the verification code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_code">Please enter the 6-digit code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_number_input_placeholder_text">Phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_retry">Retry</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="alternative_text">I don't have my phone</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone_p">We have the following numbers on record for you. Choose a number that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone">We have the following number on record for you. We will phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="enter_code_text_intro">Enter your verification code below, or</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_phone">Enter a number below that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_sms">Enter a number below that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_number">Please enter a valid phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms">We have the following number on record for you. We will send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_mixed">Enter a number below that we can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_pattern">^\+(?:[0-9][\x20-]?){6,14}[0-9]$</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms_p">We have the following numbers on record for you. Choose a number that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_countryCode">Please select your country code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_number">Please enter your phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_input_placeholder_text">Country or region</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_label">Phone Number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_tryagain">The phone number you provided is busy or unavailable. Please check the number and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_incorrect_code">The verification code you have entered does not match our records. Please try again, or request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="countryList">{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_448">The phone number you provided is unreachable.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_449">User has exceeded the number of retry attempts.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_code_input_placeholder_text">Verification code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="strongAuthenticationPhoneNumber" StringId="DisplayName">Phone Number</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>

```

## <a name="verification-display-control-user-interface-elements"></a>Doğrulama görüntüleme denetimi kullanıcı arabirimi öğeleri

Aşağıda, [sayfa düzeni sürüm](page-layout.md) 2.1.0 veya üzeri bir [doğrulama görüntüleme denetiminin](display-control-verification.md) kimlikleri verilmiştir.

| ID | Varsayılan değer |
| -- | ------------- |
|intro_msg <sup>*</sup>| Doğrulama gereklidir. Lütfen gönder düğmesine tıklayın.|
|success_send_code_msg | Doğrulama kodu gönderildi. Lütfen aşağıdaki giriş kutusuna kopyalayın.|
|failure_send_code_msg | E-posta adresinizi doğrularken sorun yaşıyoruz. Lütfen geçerli bir e-posta adresi girin ve yeniden deneyin.|
|success_verify_code_msg | E-posta adresi doğrulandı. Artık devam edebilirsiniz.|
|failure_verify_code_msg | E-posta adresinizi doğrularken sorun yaşıyoruz. Lütfen tekrar deneyin.|
|but_send_code | Doğrulama kodu gönder|
|but_verify_code | Kodu doğrula|
|but_send_new_code | Yeni kod gönder|
|but_change_claims | E-postayı değiştir|

Not: `intro_msg` öğe gizlenir ve kendiliğinden onaylanan sayfada gösterilmez. Görünmesini sağlamak için, Geçişli Stil Sayfaları [HTML customiztion](customize-ui-with-html.md) kullanın. Örnek:
    
```css
.verificationInfoText div{display: block!important}
```

### <a name="verification-display-control-example"></a>Doğrulama görüntüleme denetimi örneği

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
   <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="verification-display-control-user-interface-elements-deprecated"></a>Doğrulama görüntüleme denetimi kullanıcı arabirimi öğeleri (kullanım dışı)

Aşağıda, [sayfa düzeni sürüm](page-layout.md) 2.0.0 ile [doğrulama görüntüleme denetiminin](display-control-verification.md) kimlikleri verilmiştir.

| ID | Varsayılan değer |
| -- | ------------- |
|verification_control_but_change_claims |Değiştir |
|verification_control_fail_send_code |Kod gönderilemedi, lütfen daha sonra yeniden deneyin. |
|verification_control_fail_verify_code |Kod doğrulanamadı, lütfen daha sonra yeniden deneyin. |
|verification_control_but_send_code |Kodu gönder |
|verification_control_but_send_new_code |Yeni kod gönder |
|verification_control_but_verify_code |Kodu doğrula |
|verification_control_code_sent| Doğrulama kodu gönderildi. Lütfen aşağıdaki giriş kutusuna kopyalayın. |

### <a name="verification-display-control-example-deprecated"></a>Doğrulama görüntüleme denetimi örneği (kullanım dışı)

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>Yeniden hizmet hata iletileri

Aşağıda, [Restvıservice teknik profili](restful-technical-profile.md) hata iletilerinin kimlikleri verilmiştir:

| ID | Varsayılan değer |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | Yeniden hizmet uç noktası bağlantısı kurulamadı. Yeniden deneme hizmeti URL 'SI: {0} |
|Usermessageifbir ıtopen | {0} Yeniden deneme hizmeti URL 'SI: {1} |
|UserMessageIfDnsResolutionFailed | Yeniden deneme hizmeti uç noktasının ana bilgisayar adı çözümlenemedi. Yeniden deneme hizmeti URL 'SI: {0} |
|UserMessageIfRequestTimeout | Zaman aşımı sınırı saniyesi içindeki hizmet uç noktası için bağlantı kurulamadı {0} . Yeniden deneme hizmeti URL 'SI: {1} |


### <a name="restful-service-example"></a>Yeniden takip eden hizmet örneği

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-mfa-error-messages"></a>Azure AD MFA hata iletileri

Aşağıda, bir [Azure AD MFA teknik profili](multi-factor-auth-technical-profile.md) hata iletisinin kimlikleri verilmiştir:

| ID | Varsayılan değer |
| -- | ------------- |
|Usermessageif, Dntsendsms | SMS telefona gönderilemiyor, lütfen başka bir telefon numarası deneyin. |
|Usermessageifınvalidformat | Telefon numaranız geçerli bir biçimde değil, lütfen düzeltip yeniden deneyin.|
|Usermessageifmaxallowedcoderetr'e ulaşıldı | Yanlış kod çok fazla girildi, lütfen daha sonra yeniden deneyin.|
|UserMessageIfServerError | MFA hizmeti kullanılamıyor, lütfen daha sonra yeniden deneyin.|
|Usermessageifkısıtlanıyor | İsteğiniz kısıtlandı, lütfen daha sonra yeniden deneyin.|
|Usermessageifyanlışlıkla Gcodegirildi|Yanlış kod girildi, lütfen yeniden deneyin.|

### <a name="azure-ad-mfa-example"></a>Azure AD MFA örneği

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>Azure AD SSPR

[Azure AD SSPR teknik profili](aad-sspr-technical-profile.md) hata Iletileri için kimlikler şunlardır:

| ID | Varsayılan değer |
| -- | ------------- |
|Usermessageifrequesgeerer | Kodun süresi doldu.|
|Usermessageifınternalerror | E-posta hizmeti bir iç hatayla karşılaştı, lütfen daha sonra yeniden deneyin.|
|Usermessageifkısıtlanıyor | Çok fazla istek gönderdiniz, lütfen daha sonra yeniden deneyin.|
|Usermessageifdoğrulamaları Icationfailednoretry | En fazla doğrulama denemesi sayısını aştınız.|
|UserMessageIfVerificationFailedRetryAllowed | Doğrulama başarısız oldu, lütfen yeniden deneyin.|


### <a name="azure-ad-sspr-example"></a>Azure AD SSPR örneği

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>Bir kerelik parola hata iletileri

[Bir kerelik parola teknik profili](one-time-password-technical-profile.md) hata Iletileri için kimlikler aşağıda verilmiştir

| ID | Varsayılan değer |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |Bir kerelik parola belirtilen doğrulama, en fazla deneme sayısını aştı |
|Usermessageifsessionffnotexist |Bir kerelik parola doğrulama oturumunun süresi doldu |
|UserMessageIfSessionConflict |Bir kerelik parola doğrulama oturumunun çakışması |
|Usermessageifınvalidcode |Doğrulama için girilen bir kerelik parola yanlış |
|UserMessageIfVerificationFailedRetryAllowed |Bu kod yanlış. Lütfen tekrar deneyin. | 

### <a name="one-time-password-example"></a>Bir kerelik parola örneği

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>Talep dönüştürmeleri hata iletileri

Talep dönüştürmeleri hata iletileri için kimlikler şunlardır:

| ID | Talepleri dönüştürme | Varsayılan değer |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | "Inputclaim" talep türü için Boole talep değeri karşılaştırması başarısız oldu.| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | Talep değeri karşılaştırması başarısız oldu: belirtilen sol işlenen, sağ işlenenden büyük.|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[Assertstringclaimsareeşittir](string-transformations.md#assertstringclaimsareequal) | Talep değeri karşılaştırması, StringComparison "OrdinalIgnoreCase" ile başarısız oldu.|

### <a name="claims-transformations-example"></a>Talep dönüştürmeleri örneği

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="next-steps"></a>Sonraki adımlar

Yerelleştirme örnekleri için aşağıdaki makalelere bakın:

- [Azure Active Directory B2C özel ilkeyle dil özelleştirmesi](language-customization.md)
- [Azure Active Directory B2C Kullanıcı akışları ile dil özelleştirmesi](language-customization.md)
