---
title: SendGrid ile özel e-posta doğrulama
titleSuffix: Azure AD B2C
description: Azure AD B2C özellikli uygulamalarınızı kullanmak üzere kaydolduklarında müşterilerinize gönderilen doğrulama e-postasını özelleştirmek için SendGrid ile tümleştirmeyi öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d63e7916423038e53c375b2be4114582cf4d6152
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725773"
---
# <a name="custom-email-verification-with-sendgrid"></a>SendGrid ile özel e-posta doğrulama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Uygulamalarınızı kullanmak üzere kaydolan kullanıcılara özelleştirilmiş e-posta göndermek için Azure Active Directory B2C (Azure AD B2C) içinde özel e-posta kullanın. Üçüncü taraf e-posta sağlayıcısı SendGrid 'i kullanarak kendi e-posta şablonunuzu ve *Kimden:* adres ve konu ' ı kullanabilir, yerelleştirme ve özel bir kerelik parola (OTP) ayarlarını da destekleyebilirsiniz.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Özel e-posta doğrulaması için [SendGrid](https://sendgrid.com), [Mailjet](https://Mailjet.com)veya [mini](https://sparkpost.com)posta, özel bir REST API veya herhangi bir HTTP tabanlı e-posta sağlayıcısı (kendinizğiniz dahil) gibi bir üçüncü taraf e-posta sağlayıcısının kullanılması gerekir. Bu makalede, SendGrid kullanan bir çözümün kurulması açıklanmaktadır.

## <a name="create-a-sendgrid-account"></a>SendGrid hesabı oluşturma

Henüz bir tane yoksa, bir SendGrid hesabı ayarlayarak başlayın (Azure müşterileri her ay 25.000 ücretsiz e-postaların kilidini açabilir). Kurulum yönergeleri için, [Azure Ile SendGrid kullanarak e-posta gönderme](../sendgrid-dotnet-how-to-send-email.md)konusunun [SendGrid hesabı oluşturma](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) bölümüne bakın.

[SendGrid API anahtarı oluşturduğunuz](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key)bölümünü tamamladığınızdan emin olun. API anahtarını sonraki bir adımda kullanmak üzere kaydedin.

> [!IMPORTANT]
> SendGrid, müşterilere Paylaşılan IP ve [ayrılmış IP adreslerinden](https://sendgrid.com/docs/ui/account-and-settings/dedicated-ip-addresses/)e-posta gönderme yeteneği sunar. Ayrılmış IP adreslerini kullanırken, bir IP adresi ısınma ile kendi saygınlığını doğru bir şekilde oluşturmanız gerekir. Daha fazla bilgi için bkz. [IP adresi hazırlama](https://sendgrid.com/docs/ui/sending-email/warming-up-an-ip-address/).

## <a name="create-azure-ad-b2c-policy-key"></a>Azure AD B2C İlkesi anahtarı oluştur

Sonra, ilkenizin başvurmak için SendGrid API anahtarını bir Azure AD B2C ilke anahtarında depolayın.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve Azure AD B2C dizininizi seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. Genel Bakış sayfasında **kimlik deneyimi çerçevesi**' ni seçin.
1. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
1. **Seçenekler** Için **el ile**' yi seçin.
1. İlke anahtarı için bir **ad** girin. Örneğin, `SendGridSecret`. Ön ek, `B2C_1A_` anahtarınızın adına otomatik olarak eklenir.
1. **Gizli**, daha önce kaydettiğiniz SENDGRID API anahtarını girin.
1. **Anahtar kullanımı** için **imza**' yı seçin.
1. **Oluştur**’u seçin.

## <a name="create-sendgrid-template"></a>SendGrid şablonu oluşturma

Bir Azure AD B2C ilke anahtarında depolanan SendGrid hesabı oluşturulup SendGrid API anahtarı ile bir SendGrid [dinamik işlem şablonu](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)oluşturun.

1. SendGrid sitesinde, [işlem şablonları](https://sendgrid.com/dynamic_templates) sayfasını açın ve **Şablon Oluştur ' u** seçin.
1. Gibi benzersiz bir şablon adı girip `Verification email` **Kaydet**' i seçin.
1. Yeni şablonunuzu düzenlemeyle başlamak için **Sürüm Ekle**' yi seçin.
1. **Kod düzenleyicisini** seçin ve ardından **devam edin**.
1. HTML düzenleyicisinde, aşağıdaki HTML şablonunu yapıştırın veya kendi kendinize kullanın. `{{otp}}`Ve `{{email}}` parametreleri, tek seferlik parola değeri ve kullanıcı e-posta adresi ile dinamik olarak değişir.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Soldaki **ayarları** genişletin ve **e-posta konusu** için girin `{{subject}}` .
1. **Şablonu Kaydet**' i seçin.
1. Geri okunu seçerek **Işlem şablonları** sayfasına dönün.
1. Daha sonraki bir adımda kullanmak üzere oluşturduğunuz şablonun **kimliğini** kaydedin. Örneğin, `d-989077fbba9746e89f3f6411f596fb96`. [Talep dönüşümünü eklediğinizde](#add-the-claims-transformation)bu kimliği belirtirsiniz.

## <a name="add-azure-ad-b2c-claim-types"></a>Azure AD B2C talep türü Ekle

İlkenizde aşağıdaki talep türlerini `<ClaimsSchema>` içinde öğesine ekleyin `<BuildingBlocks>` .

Bu talep türleri, bir kerelik parola (OTP) kodu kullanarak e-posta adresi oluşturmak ve doğrulamak için gereklidir.

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="Otp">
      <DisplayName>Secondary One-time password</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="emailRequestBody">
      <DisplayName>SendGrid request body</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="VerificationCode">
      <DisplayName>Secondary Verification Code</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your email verification code</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

## <a name="add-the-claims-transformation"></a>Talep dönüşümünü ekleme

Daha sonra, SendGrid 'e gönderilen isteğin gövdesi olacak bir JSON dize talebinin çıktısını almak için bir talep dönüştürmesi gerekir.

JSON nesnesinin yapısı, InputParameters 'ın nokta gösteriminde ve ınputclaim 'nin dönüştürme Tionclaimtypes öğesindeki kimlikler tarafından tanımlanır. Nokta gösterimindeki sayılar dizileri kapsıyor. Değerler Inputclaim değerlerinin ve InputParameters ' "Value" özelliklerinden gelir. JSON talep dönüştürmeleri hakkında daha fazla bilgi için bkz. [JSON talep dönüştürmeleri](json-transformations.md).

Aşağıdaki talep dönüşümünü `<ClaimsTransformations>` içindeki öğesine ekleyin `<BuildingBlocks>` . Talep dönüştürme XML için aşağıdaki güncelleştirmeleri yapın:

* `template_id`InputParameter değerini, daha önce [SendGrid şablonunda](#create-sendgrid-template)oluşturduğunuz SENDGRID işlem şablonunun kimliğiyle güncelleştirin.
* `from.email`Adres değerini güncelleştirin. Doğrulama e-postalarından istenmeyen posta olarak işaretlenmesini önlemeye yardımcı olması için geçerli bir e-posta adresi kullanın.
* `personalizations.0.dynamic_template_data.subject`Konu satırı giriş parametresinin değerini kuruluşunuza uygun bir konu satırıyla güncelleştirin.

```xml
<!-- 
<BuildingBlocks>
  <ClaimsTransformations> -->
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your SendGrid template. -->
        <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
        <!-- Update with a subject line appropriate for your organization. -->
        <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
  <!--
  </ClaimsTransformations>
</BuildingBlocks> -->
```

## <a name="add-datauri-content-definition"></a>DataUri içerik tanımı Ekle

İçindeki talep dönüştürmelerinin altında `<BuildingBlocks>` , sürüm 2.1.2 'yi veri URI 'sine başvurmak için aşağıdaki [ContentDefinition](contentdefinitions.md) öğesini ekleyin:

```xml
<!--
<BuildingBlocks> -->
  <ContentDefinitions>
   <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!--
</BuildingBlocks> -->
```

## <a name="create-a-displaycontrol"></a>DisplayControl oluştur

Bir doğrulama görüntüleme denetimi, e-posta adresini kullanıcıya gönderilen doğrulama koduyla doğrulamak için kullanılır.

Bu örnek görüntü denetimi şu şekilde yapılandırılır:

1. `email`Kullanıcıdan adres talebi türünü toplayın.
1. Kullanıcının `verificationCode` talep türünü kullanıcıya gönderilen kodla vermesini bekleyin.
1. `email`Bu görüntü denetimine yönelik bir başvuruya sahip kendi kendine onaylanan teknik Profile geri dönün.
1. Eylemi kullanarak `SendCode` OTP kodu oluşturun ve kullanıcıya OTP kodunu içeren bir e-posta gönderin.

![Doğrulama kodu e-posta eylemi gönder](media/custom-email-sendgrid/display-control-verification-email-action-01.png)

İçerik tanımları altında, hala içinde `<BuildingBlocks>` , Ilkenize [Denetim](display-control-verification.md) türünde aşağıdaki [DisplayControl](display-controls.md) ' ı ekleyin.

```xml
<!--
<BuildingBlocks> -->
  <DisplayControls>
    <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
      <DisplayClaims>
        <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" />
      </OutputClaims>
      <Actions>
        <Action Id="SendCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
          </ValidationClaimsExchange>
        </Action>
        <Action Id="VerifyCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
          </ValidationClaimsExchange>
        </Action>
      </Actions>
    </DisplayControl>
  </DisplayControls>
<!--
</BuildingBlocks> -->
```

## <a name="add-otp-technical-profiles"></a>OTP teknik profilleri ekleme

`GenerateOtp`Teknik profil, e-posta adresi için bir kod oluşturur. `VerifyOtp`Teknik profil, e-posta adresiyle ilişkili kodu doğrular. Biçimin yapılandırmasını ve tek seferlik parolanın kullanım süresini değiştirebilirsiniz. OTP teknik profilleri hakkında daha fazla bilgi için bkz. [tek seferlik parola teknik profili tanımlama](one-time-password-technical-profile.md).

> [!NOTE]
> Web. TPEngine. Providers. OneTimePasswordProtocolProvider protokolü tarafından oluşturulan OTP kodları tarayıcı oturumuna bağlanır. Bu, bir kullanıcının ilgili oturumları için geçerli olan farklı tarayıcı oturumlarında benzersiz OTP kodları oluşturabileceği anlamına gelir. Bunun aksine, yerleşik Kullanıcı akışı tarafından oluşturulan bir OTP kodu tarayıcı oturumundan bağımsızdır, bu nedenle bir Kullanıcı yeni bir tarayıcı oturumunda yeni bir OTP kodu oluşturursa, önceki OTP kodunun yerini alır.

Aşağıdaki teknik profilleri `<ClaimsProviders>` öğesine ekleyin.

```xml
<!--
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>One time password technical profiles</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="GenerateOtp">
        <DisplayName>Generate one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">GenerateCode</Item>
          <Item Key="CodeExpirationInSeconds">1200</Item>
          <Item Key="CodeLength">6</Item>
          <Item Key="CharacterSet">0-9</Item>
          <Item Key="ReuseSameCode">true</Item>
          <Item Key="NumRetryAttempts">5</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
        </OutputClaims>
      </TechnicalProfile>

      <TechnicalProfile Id="VerifyOtp">
        <DisplayName>Verify one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">VerifyCode</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
          <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
        </InputClaims>
      </TechnicalProfile>
     </TechnicalProfiles>
  </ClaimsProvider>
<!--
</ClaimsProviders> -->
```

## <a name="add-a-rest-api-technical-profile"></a>REST API teknik profili ekleme

Bu REST API teknik profili, e-posta içeriğini (SendGrid biçimini kullanarak) oluşturur. Daha fazla teknik profil hakkında daha fazla bilgi için bkz. [Restuz teknik profili tanımlama](restful-technical-profile.md).

OTP teknik profillerinde olduğu gibi, aşağıdaki teknik profilleri `<ClaimsProviders>` öğesine ekleyin.

```xml
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendOtp">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>DisplayControl öğesine bir başvuru oluşturun

Son adımda, oluşturduğunuz DisplayControl öğesine bir başvuru ekleyin. Mevcut `LocalAccountSignUpWithLogonEmail` ve `LocalAccountDiscoveryUsingEmailAddress` kendi kendini onaylanan teknik profillerinizi aşağıdakiler ile değiştirin. Azure AD B2C ilkesi 'nin önceki bir sürümünü kullandıysanız. Bu teknik profiller `DisplayClaims` , DisplayControl başvurusu ile birlikte kullanılır.

Daha fazla bilgi için bkz. [kendi kendine onaylanan teknik profil](restful-technical-profile.md) ve [DisplayControl](display-controls.md).

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="LocalAccountDiscoveryUsingEmailAddress">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
      </DisplayClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>Seçim E-postanızı yerelleştirin

E-postayı yerelleştirmek için, SendGrid 'e veya e-posta sağlayıcınıza yerelleştirilmiş dizeler göndermeniz gerekir. Örneğin e-posta konusunu, gövdesini, kod iletinizi veya e-postanın imzasını yerelleştirebilirsiniz. Bunu yapmak için, yerelleştirilmiş dizeleri talep türlerine kopyalamak üzere [Getlocalizedstringstrans,](string-transformations.md) talepler dönüşümünü kullanabilirsiniz. `GenerateEmailRequestBody`JSON yükünü üreten talep dönüştürmesi, yerelleştirilmiş dizeleri içeren giriş taleplerini kullanır.

1. İlkenizde şu dize taleplerini tanımlayın: Subject, Message, Codepote ve Signature.
1. Yerelleştirilmiş dize değerlerini 1. adımdan gelen taleplere koymak için bir [Getlocalizedstringstransbir](string-transformations.md) talep dönüştürmesi tanımlayın.
1. `GenerateEmailRequestBody`Talep dönüşümünü, giriş taleplerini AŞAĞıDAKI XML kod parçacığıyla kullanılacak şekilde değiştirin.
1. SendGrid şablonunuzu, Azure AD B2C tarafından Yerelleştirilecek tüm dizelerin yerine dinamik parametreleri kullanacak şekilde güncelleştirin.

    ```xml
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. Aşağıdaki [Yerelleştirme](localization.md) öğesini ekleyin.

    ```xml
    <!--
    <BuildingBlocks> -->
      <Localization Enabled="true">
        <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
          <SupportedLanguage>en</SupportedLanguage>
          <SupportedLanguage>es</SupportedLanguage>
        </SupportedLanguages>
        <LocalizedResources Id="api.custom-email.en">
          <LocalizedStrings>
            <!--Email template parameters-->
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
        <LocalizedResources Id="api.custom-email.es">
          <LocalizedStrings>
            <!--Email template parameters-->
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
      </Localization>
    <!--
    </BuildingBlocks> -->
    ```

1. [ContentDefinitions](contentdefinitions.md) öğesini güncelleştirerek localizedresources öğelerine başvurular ekleyin.

    ```XML
    <!--
    <BuildingBlocks> -->
      <ContentDefinitions>
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
        <ContentDefinition Id="api.localaccountpasswordreset">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
      </ContentDefinitions>
    <!--
    </BuildingBlocks> -->
    ```

1. Son olarak, aşağıdaki giriş talep dönüşümünü `LocalAccountSignUpWithLogonEmail` ve `LocalAccountDiscoveryUsingEmailAddress` Teknik profillerine ekleyin.

    ```XML
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```
    
## <a name="optional-localize-the-ui"></a>Seçim Kullanıcı arabirimini yerelleştirin

Yerelleştirme öğesi, Kullanıcı yolculukları için ilkedeki birden çok yerel ayarı veya dili desteketmenize olanak tanır. İlkelerde yerelleştirme desteği, hem [doğrulama görüntüleme denetimi kullanıcı arabirimi öğeleri](localization-string-ids.md#verification-display-control-user-interface-elements)hem de [bir kerelik parola hata iletileri](localization-string-ids.md#one-time-password-error-messages)için dile özgü dizeler sağlamanıza olanak tanır. Aşağıdaki LocalizedString öğesini LocalizedResources öğesine ekleyin. 

```XML
<LocalizedResources Id="api.custom-email.en">
  <LocalizedStrings>
    ...
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
    <!-- Claims-->
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="DisplayName">Verification Code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="UserHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="AdminHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Eamil</LocalizedString>
    <!-- Email validation error messages-->
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">The verification has failed, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Yerelleştirilmiş dizeleri ekledikten sonra, LocalAccountSignUpWithLogonEmail ve Localaccountdiscoveryusingemapostadresi Technical Profiles ' nden OTP doğrulama hatası iletileri meta verilerini kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

GitHub 'da özel bir e-posta doğrulama ilkesi örneğini bulabilirsiniz:

- [Özel e-posta doğrulama-DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Özel bir REST API veya herhangi bir HTTP tabanlı SMTP e-posta sağlayıcısı kullanma hakkında bilgi için, bkz. [Azure AD B2C bir özel ilkede, bir RESTAN teknik profili tanımlama](restful-technical-profile.md).

::: zone-end
