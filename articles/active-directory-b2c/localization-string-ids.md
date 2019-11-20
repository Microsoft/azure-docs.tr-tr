---
title: Yerelleştirme dize kimlikleri-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C bir özel ilkede API. signuporsignıd kimliğine sahip bir içerik tanımının kimliklerini belirtin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: da1390de4e2eb0624032dc490416e7b6e5d61baa
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "67846845"
---
# <a name="localization-string-ids"></a>Yerelleştirme dizesi kimlikleri

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Yerelleştirme** öğesi, Kullanıcı yolculukları için ilkedeki birden çok yerel ayarı veya dili desteklemenize olanak sağlar. Bu makale, ilkenizde kullanabileceğiniz yerelleştirme kimliklerinin listesini sağlar. UI yerelleştirmesini öğrenmek için bkz. [Yerelleştirme](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Kaydolma veya oturum açma sayfası öğeleri

KIMLIĞI olan bir içerik tanımı için aşağıdaki kimlikler kullanılır `api.signuporsignin`.

| id | Varsayılan değer |
| -- | ------------- |
| **local_intro_email** | Mevcut hesabınızla oturum açın |
| **logonIdentifier_email** | E-posta Adresi |
| **requiredField_email** | Lütfen e-postanızı girin |
| **invalid_email** | Lütfen geçerli bir e-posta adresi girin |
| **email_pattern** | ^ [a-zA-Z0-9.! # $% & ' ' *+/=? ^ _\`{\|} ~-] + @ [a-Za-z0-9-] + (?:\\. [ a-zA-Z0-9-] +)* $ |
| **local_intro_username** | Kullanıcı adınızla oturum açın |
| **logonIdentifier_username** | Kullanıcı Adı |
| **requiredField_username** | Lütfen kullanıcı adınızı girin |
| **Parola** | istemcisiyle yönetilen bir cihaz için) |
| **requiredField_password** | Lütfen parolanızı girin |
| **invalid_password** | Girdiğiniz parola beklenen biçimde değil. |
| **forgotpassword_link** | Parolanızı mı unuttunuz? |
| **createaccount_intro** | Hesabınız yok mu? |
| **createaccount_link** | Şimdi kaydolun |
| **divider_title** | OR |
| **cancel_message** | Kullanıcı parolasını unuttu |
| **button_signin** | Oturum aç |
| **social_intro** | Sosyal hesabınızla oturum açın |
  **remember_me** |Oturumumu açık bırak|
| **unknown_error** | Oturumunuzu açmada sorun yaşıyoruz. Lütfen daha sonra tekrar deneyin. |

Aşağıdaki örnek, kayıt veya oturum açma sayfasında bazı Kullanıcı arabirimi öğelerinin kullanımını gösterir:

![Kaydolma veya oturum açma sayfası UX öğeleri](./media/localization-string-ids/localization-susi.png)

Kimlik sağlayıcılarının KIMLIĞI, Kullanıcı yolculuğu **Claimsexchange** öğesinde yapılandırılır. Kimlik sağlayıcısının başlığını yerelleştirmek için, **ElementType** olarak ayarlanır `ClaimsProvider`, **strıngıd** , `ClaimsExchange`kimliğine ayarlanır.

```XML
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

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>Kaydolma veya oturum açma hata iletileri

| id | Varsayılan değer |
| -- | ------------- |
| **Usermessageifınvalidpassword** | Yanlış parola girdiniz. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Hesabınızı bulamıyoruz. |
| **UserMessageIfOldPasswordUsed** | Eski bir parola kullandınız gibi görünüyor. |
| **DefaultMessage** | Geçersiz kullanıcı adı veya parola. |
| **UserMessageIfUserAccountDisabled** | Hesabınız kilitlendi. Dosyanın kilidini açmak için destek sorumlunuza başvurun, sonra yeniden deneyin. |
| **Usermessageifuseraccountkilitlendi** | Hesabınız yetkisiz kullanımı engellemek için geçici olarak kilitlidir. Daha sonra tekrar deneyin. |
| **Aadrequestskısıtlanıyor** | Şu anda çok fazla istek var. Lütfen bir süre bekleyip yeniden deneyin. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Kaydolma ve kendi kendine onaylanan sayfalar Kullanıcı arabirimi öğeleri

Aşağıda, `api.localaccountsignup` `api.selfasserted.profileupdate` ve `api.selfasserted` gibibiriçeriktanımınınkimliğiolanbiriçerik`api.localaccountpasswordreset`tanımının kimlikleri verilmiştir.

| id | Varsayılan değer |
| -- | ------------- |
| **ver_sent** | Doğrulama kodu şu adrese gönderildi: |
| **ver_but_default** | Varsayılan |
| **cancel_message** | Kullanıcı kendi kendine onaylanan bilgileri girmeyi iptal etti |
| **preloader_alt** | Lütfen bekleyin |
| **ver_but_send** | Doğrulama kodu gönder |
| **alert_yes** | Evet |
| **error_fieldIncorrect** | Bir veya daha fazla alan yanlış doldurulmuş. Lütfen girişlerinizi denetleyin ve yeniden deneyin. |
| **yıl** | Yıl |
| **verifying_blurb** | Bilgileriniz işlenirken lütfen bekleyin. |
| **button_cancel** | İptal |
| **ver_fail_no_retry** | Çok fazla hatalı deneme yaptınız. Lütfen daha sonra tekrar deneyin. |
| **başından** | Ay |
| **ver_success_msg** | E-posta adresi doğrulandı. Artık devam edebilirsiniz. |
| **months** | Ocak, Şubat, Mart, Nisan, Mayıs, Haziran, Temmuz, Ağustos, Eylül, Ekim, Kasım, Aralık |
| **ver_fail_server** | E-posta adresinizi doğrularken sorun yaşıyoruz. Lütfen geçerli bir e-posta adresi girin ve yeniden deneyin. |
| **error_requiredFieldMissing** | Gerekli bir alan eksik. Lütfen tüm gerekli alanları doldurun ve yeniden deneyin. |
| **initial_intro** | Lütfen aşağıdaki ayrıntıları sağlayın. |
| **ver_but_resend** | Yeni kod gönder |
| **button_continue** | Create |
| **error_passwordEntryMismatch** | Parola girişi alanları eşleşmiyor. Lütfen her iki alana da aynı parolayı girip yeniden deneyin. |
| **ver_incorrect_format** | Hatalı biçim. |
| **ver_but_edit** | E-postayı değiştir |
| **ver_but_verify** | Kodu doğrula |
| **alert_no** | Hayır |
| **ver_info_msg** | Doğrulama kodu gelen kutunuza gönderildi. Lütfen aşağıdaki giriş kutusuna kopyalayın. |
| **günündeki** | Gün |
| **ver_fail_throttled** | Bu e-posta adresini doğrulamak için çok fazla istek vardı. Lütfen bekleyin, sonra yeniden deneyin. |
| **helplink_text** | Bu nedir? |
| **ver_fail_retry** | Bu kod yanlış. Lütfen yeniden deneyin. |
| **alert_title** | Bilgilerinizi Girmeyi İptal Edin |
| **required_field** | Bu bilgi gereklidir. |
| **alert_message** | Bilgilerinizi girmeyi iptal etmek istediğinizden emin misiniz? |
| **ver_intro_msg** | Doğrulama gereklidir. Lütfen gönder düğmesine tıklayın. |
| **ver_input** | Doğrulama kodu |

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>Kaydolma ve kendi kendine onaylanan sayfalar hata iletileri

| id | Varsayılan değer |
| -- | ------------- |
| **Usermessageifclaimsprincıpalalreadyexists** | Belirtilen KIMLIĞE sahip bir kullanıcı zaten var. Lütfen farklı bir tane seçin. |
| **Usermessageifclaimnotdoğrulandı** | Talep doğrulanmadı:{0} |
| **UserMessageIfIncorrectPattern** | İçin yanlış desenler:{0} |
| **UserMessageIfMissingRequiredElement** | Gerekli öğe eksik:{0} |
| **UserMessageIfValidationError** | Doğrulamada hata:{0} |
| **Usermessageifınvalidınput** | {0}geçersiz giriş içeriyor. |
| **Servicekısıtlanıyor** | Şu anda çok fazla istek var. Lütfen bir süre bekleyip yeniden deneyin. |

Aşağıdaki örnek, kaydolma sayfasında bazı Kullanıcı arabirimi öğelerinin kullanımını gösterir:

![Kullanıcı arabirimi öğesi adları etiketli kaydolma sayfası](./media/localization-string-ids/localization-sign-up.png)

Aşağıdaki örnek, Kullanıcı doğrulama kodu gönder düğmesine tıkladıktan sonra kaydolma sayfasındaki bazı Kullanıcı arabirimi öğelerinin kullanımını gösterir:

![Kaydolma sayfası eposta doğrulaması UX öğeleri](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Telefon faktörü kimlik doğrulama sayfası kullanıcı arabirimi öğeleri

Aşağıda KIMLIĞI `api.phonefactor`olan bir içerik tanımının kimlikleri verilmiştir.

| id | Varsayılan değer |
| -- | ------------- |
| **button_verify** | Beni Arayın |
| **country_code_label** | Ülke kodu |
| **cancel_message** | Kullanıcı Multi-Factor Authentication 'ı iptal etti |
| **text_button_send_second_code** | Yeni bir kod gönder |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Sizin için kayıt üzerinde aşağıdaki sayıyı sunuyoruz. Kimliğinizi doğrulamak için SMS veya Phone aracılığıyla kod gönderebiliriz. |
| **intro_mixed_p** | Sizin için kayıt için aşağıdaki numaralara ihtiyacımız var. Kimliğinizi doğrulamak için SMS aracılığıyla telefon ve kod gönderebileceğimiz bir sayı seçin. |
| **button_verify_code** | Kodu Doğrula |
| **requiredField_code** | Lütfen aldığınız doğrulama kodunu girin |
| **invalid_code** | Lütfen aldığınız 6 basamaklı kodu girin |
| **button_cancel** | İptal |
| **local_number_input_placeholder_text** | Telefon numarası |
| **button_retry** | Yeniden Dene |
| **alternative_text** | Telefonum yanımda değil |
| **intro_phone_p** | Sizin için kayıt için aşağıdaki numaralara ihtiyacımız var. Kimliğinizi doğrulamak için telefon yapabilmemiz gereken bir sayı seçin. |
| **intro_phone** | Sizin için kayıt üzerinde aşağıdaki sayıyı sunuyoruz. Kimliğinizi doğrulamak için telefon göndereceğiz. |
| **enter_code_text_intro** | Doğrulama kodunuzu aşağıya girin veya  |
| **intro_entry_phone** | Aşağıya kimliğinizi doğrulamak için arayabileceğimiz bir numara girin. |
| **intro_entry_sms** | Aşağıya kimliğinizi doğrulamak için SMS aracılığıyla kod gönderebileceğimiz bir numara girin. |
| **button_send_code** | Kod Gönder |
| **invalid_number** | Lütfen geçerli bir telefon numarası girin |
| **intro_sms** | Sizin için kayıt üzerinde aşağıdaki sayıyı sunuyoruz. Kimliğinizi doğrulamak için SMS aracılığıyla bir kod göndereceğiz. |
| **intro_entry_mixed** | Aşağıya kimliğinizi doğrulamak için SMS aracılığıyla kod gönderebileceğimiz veya arayabileceğimiz bir numara girin. |
| **number_pattern** | ^\\+ (?: [0-9] [\\x20-]?) {6,14}[0-9] $ |
| **intro_sms_p** |Sizin için kayıt için aşağıdaki numaralara ihtiyacımız var. Kimliğinizi doğrulamak için SMS aracılığıyla kod gönderebileceğimiz bir sayı seçin. |
| **requiredField_countryCode** | Lütfen ülke kodunuzu seçin |
| **requiredField_number** | Lütfen telefon numaranızı girin |
| **country_code_input_placeholder_text** |Ülke veya bölge |
| **number_label** | Telefon numarası |
| **error_tryagain** | Verdiğiniz telefon numarası meşgul veya kullanılamıyor. Lütfen numarayı denetleyip yeniden deneyin. |
| **error_incorrect_code** | Girdiğiniz doğrulama kodu kayıtlarımızla eşleşmiyor. Lütfen yeniden deneyin veya yeni bir kod isteyin. |
| **countryList** | {\"Varsayılan\":ülke/\"bölge,AF\":\"Afganistan,AX\":,\"\"\"\"\" Adaları\",\"Al:\"Arnavutluk\",DZ\" : Cezayir,as\":\"\"\"\"\"\" Amerikan Samoa\",\"ad\":Andora\",\"Ao:\"Angola,AI\":\"\"\"\" \"Anguilla\", aq:\"Antarktika\",AG\":Antigua veBarbuda\",\"\" \"\"\"Ar\":Arjantin,har\": Ermenistan,AW:\"Aruba\"\"\"\"\"\"\" \",Au\":\"Avustralya,\"at:Avusturya,\"\"\"\"\"\" AZ\":\"Azerbaycan,\"BS\":Bahamalar\", BH:\"\"\"\"\" Bahreyn\",\"BD:\"Bangladeş\",BB\":Barbados,\"\"\"\"\" Şu\"kadar\":\"Belarus,\"beyaz:\"Belçika,\"BZ:\" \"\"\" Belize\",\"BJ:\"Benin\",BM\":Bermuda,\"\"\"\"\"BT\":Bhutan\",\"Bo:\"Bolivya,BQ:\"\"\"\"\"\" Bonaire\",\"ba\":Bosna\"-Hersek\",Siyah\"beyaz\":Botsvana\"<span class="notransla class=""></span class="notransla> Harici Adaları\",\"VI\":ABD\" Virgin Adaları\",\"UG\":\"Uganda,\"UA\":Ukrayna\",AE:\"\"\"\"\" Birleşik Arap Emirlikleri\",\"GB\":BirleşikKrallık\",ABD\":BirleşikDevletler\",uy\"\"\"\" \":Uruguay\",\"uz:\"Özbekistan, vu & lt:Vanuatu\",\"\"\"\"\"\" \"VA:\"Vatikanşehri\",ve\": Venezuela, vn:\"Vietnam\"\"\"\"\"\" \",WF\":Wallisve\"Futuna,YS:\"Yemen,\"ZM\"\"\"\"\"\":Zambiya\",\"ZW:Zimbabve\"}\"\"\" |
| **error_448** | Belirttiğiniz telefon numarasına ulaşılamıyor. |
| **error_449** | Kullanıcı yeniden deneme girişimi sayısını aştı. |
| **verification_code_input_placeholder_text** | Doğrulama kodu |

Aşağıdaki örnek, MFA kayıt sayfasında bazı Kullanıcı arabirimi öğelerinin kullanımını gösterir:

![Kaydolma sayfası eposta doğrulaması UX öğeleri](./media/localization-string-ids/localization-mfa1.png)

Aşağıdaki örnek, MFA doğrulama sayfasında bazı Kullanıcı arabirimi öğelerinin kullanımını gösterir:

![Kaydolma sayfası eposta doğrulaması UX öğeleri](./media/localization-string-ids/localization-mfa2.png)







