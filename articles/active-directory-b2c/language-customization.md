---
title: Azure Active Directory B2C dil özelleştirmesi
description: Azure Active Directory B2C Kullanıcı akışlarınızda dil deneyimini özelleştirme hakkında bilgi edinin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 418f0797343a64728c4e48084b09bd0e426cec62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686419"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C dil özelleştirmesi

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) dil özelleştirmesi, Kullanıcı akışlarınızın müşteri gereksinimlerinize uyacak şekilde farklı dillere uyum sağlamasına izin verir. Microsoft, [36 dil](#supported-languages)için Çeviriler sağlar, ancak herhangi bir dil için kendi çevirilerinizi de sağlayabilirsiniz. Deneyiminiz yalnızca tek bir dil için sağlanmış olsa bile, sayfalardaki tüm metinleri özelleştirebilirsiniz.

## <a name="how-language-customization-works"></a>Dil özelleştirmesi nasıl kullanılır?

Kullanıcı akışlarınızın hangi dillerde kullanılabilir olduğunu seçmek için dil özelleştirmesi ' nı kullanırsınız. Özellik etkinleştirildikten sonra, uygulamanızdan sorgu dizesi parametresini sağlayabilirsiniz `ui_locales` . Azure AD B2C ' a çağırdığınızda, sayfanız belirttiğiniz yerel ayara çevrilir. Bu tür bir yapılandırma, Kullanıcı akışlarınızdaki diller üzerinde tüm denetimleri denetlemenizi sağlar ve müşterinin tarayıcısının dil ayarlarını yoksayar.

Müşterinizin hangi dillerde gördüğü üzerinde bu denetim düzeyine ihtiyaç duymayabilir. Bir `ui_locales` parametre sağlamazsanız, müşterinin deneyimi tarayıcının ayarları tarafından belirlenir. Desteklenen bir dil olarak ekleyerek, Kullanıcı akışlarınızın hangi dillerde çevrildiğinden de denetleyebilirsiniz. Bir müşterinin tarayıcısı, desteklemek istemediğiniz bir dili gösterecek şekilde ayarlandıysa, desteklenen kültürler için varsayılan olarak seçtiğiniz dil bunun yerine gösterilir.

* **UI-yerel ayarlar belirtilen dil**: dil özelleştirmesini etkinleştirdikten sonra, Kullanıcı akışınız burada belirtilen dile çevrilir.
* **Tarayıcı tarafından istenen dil**: herhangi bir `ui_locales` parametre belirtilmemişse, *dil destekleniyorsa* Kullanıcı akışınız tarayıcı tarafından istenen dile çevrilir.
* **İlke varsayılan dili**: tarayıcı bir dil belirtmezse veya desteklenmeyen bir değer belirtiyorsa, Kullanıcı akışı, Kullanıcı akışı varsayılan diline çevrilir.

> [!NOTE]
> Özel Kullanıcı özniteliklerini kullanıyorsanız kendi çevirilerinizi sağlamanız gerekir. Daha fazla bilgi için bkz. [dizelerinizi özelleştirme](#customize-your-strings).

::: zone pivot="b2c-custom-policy"

Yerelleştirme üç adım gerektirir: 

1. Desteklenen dillerin açık listesini ayarlama
1. Dile özgü dizeler ve koleksiyonlar sağlama
1. Sayfanın [içerik tanımını](contentdefinitions.md) düzenleyin. 

::: zone-end 

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>Ui_locales için istenen dilleri destekleme

Dil özelleştirmenin genel kullanıma sunulmadan önce oluşturulan ilkelerin önce bu özelliği etkinleştirmesi gerekir. Sonrasında oluşturulan ilkeler ve Kullanıcı akışları, varsayılan olarak dil özelleştirmesi etkindir.

Bir Kullanıcı akışında dil özelleştirmesini etkinleştirdiğinizde, parametresini ekleyerek Kullanıcı akışının dilini kontrol edebilirsiniz `ui_locales` .

1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
1. Çeviriler için etkinleştirmek istediğiniz kullanıcı akışına tıklayın.
1. **Dilleri** seçin.
1. **Dil özelleştirmesini etkinleştir**' i seçin.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Kullanıcı akışındaki hangi dillerin etkinleştirildiğini seçin

Kullanıcı akışınız için parametresi olmadan tarayıcı tarafından istendiğinde çevrilecek bir dil kümesi etkinleştirin `ui_locales` .

1. Kullanıcı akışınızdan önceki yönergelerden dil özelleştirmesi etkinleştirildiğinden emin olun.
1. Kullanıcı akışının **Diller** sayfasında, desteklemek istediğiniz bir dil seçin.
1. Özellikler bölmesinde, **etkin** ' i **Evet** olarak değiştirin.
1. Özellikler bölmesinin en üstünde **Kaydet** ' i seçin.

>[!NOTE]
>Bir `ui_locales` parametre sağlanmazsa, sayfa yalnızca etkinleştirildiğinde müşterinin tarayıcı diline çevrilir.
>

## <a name="customize-your-strings"></a>Dizelerinizi özelleştirin

Dil özelleştirmesi, Kullanıcı akışındaki herhangi bir dizeyi özelleştirmenize olanak sağlar.

1. Kullanıcı akışınız, önceki yönergelerden dil özelleştirmesi etkinleştirilmiş olduğundan emin olun.
1. Kullanıcı akışının **Diller** sayfasında, özelleştirmek istediğiniz dili seçin.
1. **Sayfa düzeyi-kaynak dosyaları** altında, düzenlemek istediğiniz sayfayı seçin.
1. **Varsayılanları indir** ' i seçin (veya daha önce bu dili düzenlediyseniz, **geçersiz kılmaları indir** ).

Bu adımlar size Dizelerinizin düzenlenmesine başlamak için kullanabileceğiniz bir JSON dosyası sağlar.

### <a name="change-any-string-on-the-page"></a>Sayfadaki herhangi bir dizeyi değiştirme

1. JSON düzenleyicisinde önceki yönergelerden indirilen JSON dosyasını açın.
1. Değiştirmek istediğiniz öğeyi bulun. `StringId`Aradığınız dize için arama yapabilir veya `Value` değiştirmek istediğiniz özniteliği arayabilirsiniz.
1. Özniteliğini, `Value` görüntülenmesini istediğiniz şekilde güncelleştirin.
1. Değiştirmek istediğiniz her dize için `Override` olarak değiştirin `true` .
1. Dosyayı kaydedin ve değişikliklerinizi karşıya yükleyin. (Karşıya yükleme denetimini, JSON dosyasını indirdiğiniz konum ile aynı yerde bulabilirsiniz.)

> [!IMPORTANT]
> Bir dizeyi geçersiz kılmanız gerekirse değerini olarak ayarladığınızdan emin olun `Override` `true` . Değer değiştirilmemişse, giriş yok sayılır.

### <a name="change-extension-attributes"></a>Uzantı özniteliklerini Değiştir

Bir özel kullanıcı özniteliği için dizeyi değiştirmek isterseniz veya bir JSON öğesine eklemek istiyorsanız, şu biçimdedir:

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
  ]
}
```

`<ExtensionAttribute>`Özel Kullanıcı özniteme adıyla değiştirin.

`<ExtensionAttributeValue>`Görüntülenecek yeni dize ile değiştirin.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>LocalizedCollections kullanarak değer listesi sağlama

Yanıtlar için bir değer listesi kümesi sağlamak istiyorsanız, bir öznitelik oluşturmanız gerekir `LocalizedCollections` . `LocalizedCollections` , `Name` ve `Value` çiftleri dizisidir. Öğelerin sırası görüntülendikleri sıra olacaktır. Eklemek için `LocalizedCollections` aşağıdaki biçimi kullanın:

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
        {
          "Name":"<Response1>",
          "Value":"<Value1>"
        },
        {
          "Name":"<Response2>",
          "Value":"<Value2>"
        }
      ]
    }
  ]
}
```

* `ElementId` , bu `LocalizedCollections` özniteliğin yanıt olduğu Kullanıcı özniteliğidir.
* `Name` , kullanıcıya gösterilen değerdir.
* `Value` Bu seçenek belirlendiğinde talepte döndürülen değer.

### <a name="upload-your-changes"></a>Değişikliklerinizi karşıya yükleyin

1. JSON dosyanızdaki değişiklikleri tamamladıktan sonra B2C kiracınıza geri dönün.
1. **Kullanıcı akışları** ' nı seçin ve çeviriler için etkinleştirmek istediğiniz kullanıcı akışına tıklayın.
1. **Dilleri** seçin.
1. Çevirmek istediğiniz dili seçin.
1. Çevirileri sağlamak istediğiniz sayfayı seçin.
1. Klasör simgesini seçin ve karşıya yüklenecek JSON dosyasını seçin.

Değişiklikler Kullanıcı akışınıza otomatik olarak kaydedilir.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Dil özelleştirmesini kullanarak sayfa Kullanıcı arabirimini özelleştirme

[HTML içeriğinizi](customize-ui-with-html.md)yerelleştirmenin iki yolu vardır. Bir yol, [dil özelleştirmesini](language-customization.md)açmak için bir yoldur. Bu özelliğin etkinleştirilmesi, Azure AD B2C OpenID Connect parametresini uç noktanıza iletmesine izin verir `ui-locales` . İçerik sunucunuz bu parametreyi dile özgü özelleştirilmiş HTML sayfaları sağlamak için kullanabilir.

Alternatif olarak, kullanılan yerel ayara göre farklı yerlerden içerik çekebilirsiniz. CORS etkin uç noktanıza, belirli diller için içerik barındırmak üzere bir klasör yapısı ayarlayabilirsiniz. Joker karakter değerini kullanırsanız, doğru olanı çağıracaksınız `{Culture:RFC5646}` . Örneğin, bunun özel sayfa URI 'SI olduğunu varsayalım:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Sayfasını ' de yükleyebilirsiniz `fr` . Sayfa HTML ve CSS içeriğini çeker, buradan çekilir:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Özel Dil ekle

Ayrıca, Microsoft 'un şu anda çevirisi sağlamayan diller ekleyebilirsiniz. Kullanıcı akışındaki tüm dizeler için çevirileri sağlamanız gerekir. Dil ve yerel ayar kodları ISO 639-1 standardına göre sınırlandırılmıştır. Yerel ayar kodu biçimi, örneğin "ISO_639-1_code"-"CountryCode" olmalıdır `en-GB` . Yerel ayar KIMLIĞI biçimleri hakkında daha fazla bilgi için lütfen bkz. https://docs.microsoft.com/openspecs/office_standards/ms-oe376/6c085406-a698-4e12-9d4d-c3b0ee3dbc4a

1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
2. Özel diller eklemek istediğiniz kullanıcı akışına tıklayın ve sonra **Diller**' e tıklayın.
3. Sayfanın üst kısmından **Özel Dil ekle** ' yi seçin.
4. Açılan bağlam bölmesinde geçerli bir yerel ayar kodu girerek hangi dilde çeviri sağlayadığınızı belirler.
5. Her sayfa için, Ingilizce için bir geçersiz kılmalar kümesi indirebilir ve çeviriler üzerinde çalışma yapabilirsiniz.
6. JSON dosyaları ile işiniz bittiğinde, bunları her bir sayfa için karşıya yükleyebilirsiniz.
7. **Etkinleştir**' i seçtiğinizde Kullanıcı akışınız artık kullanıcılarınız için bu dili görüntüleyebilir.
8. Dili kaydedin.

>[!IMPORTANT]
>Kaydedebilmeniz için özel dilleri etkinleştirmeniz veya için geçersiz kılmaları yüklemeniz gerekir.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>Desteklenen dillerin listesini ayarlama

İlkenizin uzantıları dosyasını açın. Örneğin, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. [Buildingblocks](buildingblocks.md) öğesi için arama yapın. Öğe yoksa, ekleyin.
1. `Localization`Öğeyi desteklenen dillere ekleyin: İngilizce (varsayılan) ve İspanyolca.  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>Dile özgü Etiketler sağlama

Öğesinin [Localizedresources](localization.md#localizedresources) 'i `Localization` yerelleştirilmiş dizelerin listesini içerir. Yerelleştirilmiş kaynaklar öğesi yerelleştirilmiş kaynakları benzersiz şekilde tanımlamak için kullanılan bir tanımlayıcıya sahiptir. Bu tanımlayıcı, [İçerik tanımı](contentdefinitions.md) öğesinde daha sonra kullanılır.

İçerik tanımı ve desteklemek istediğiniz herhangi bir dil için yerelleştirilmiş kaynaklar öğelerini yapılandırırsınız. Ingilizce ve Ispanyolca Birleşik kaydolma veya oturum açma sayfalarını özelleştirmek için, `LocalizedResources` öğenin kapandıktan sonra aşağıdaki öğeleri eklersiniz `</SupportedLanguages>` .

> [!NOTE]
> Aşağıdaki örnekte, `#` her satırın başlangıcında kare simgesini ekledik ve bu sayede yerelleştirilmiş etiketleri ekranda kolayca bulabilirsiniz.

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>Yerelleştirme ile içerik tanımını düzenleme

BuildingBlocks öğesinin bir alt öğesi olarak kopyaladığınız ContentDefinitions öğesinin tüm içeriğini yapıştırın.

Aşağıdaki örnekte, kayıt veya oturum açma sayfasına ve yerel hesap kaydolma sayfasına Ingilizce (en) ve Ispanyolca (es) özel dizeler eklenir. Her bir **Localizedresourcesreference** Için **Localizedresourcesreferenceıd** , kendi yerel ayarlarıyla aynıdır, ancak tanımlayıcı olarak herhangi bir dize kullanabilirsiniz. Her dil ve sayfa birleşimi için, daha önce oluşturduğunuz ilgili **Localizedresources** üzerine gelin.

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

##  <a name="upload-and-test-your-updated-custom-policy"></a>Güncelleştirilmiş özel ilkenizi karşıya yükleme ve test etme

### <a name="upload-the-custom-policy"></a>Özel ilkeyi karşıya yükle

1. Uzantı dosyasını kaydedin.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Arama yapın ve **Azure AD B2C** seçin.
1. **İlkeler** altında **kimlik deneyimi çerçevesi**' ni seçin.
1. **Özel Ilkeyi karşıya yükle**' yi seçin.
1. Daha önce değiştirdiğiniz uzantıları dosyasını karşıya yükleyin.

### <a name="test-the-custom-policy-by-using-run-now"></a>**Şimdi Çalıştır** 'ı kullanarak özel ilkeyi test etme

1. Karşıya yüklediğiniz ilkeyi seçin ve **Şimdi Çalıştır**' ı seçin.
1. Yerelleştirilmiş kaydolma veya oturum açma sayfasını görebilmelisiniz.
1. Kaydolma bağlantısına tıklayın ve yerelleştirilmiş kaydolma sayfasını görmeniz gerekir.)
1. Tarayıcınızın varsayılan dilini Ispanyolca olarak değiştirin. Ya da sorgu dizesi parametresini `ui_locales` Yetkilendirme isteğine ekleyebilirsiniz. Örnek: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>Ek bilgiler

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>Geçersiz kılmalar olarak sayfa Kullanıcı arabirimi özelleştirme etiketleri

Dil özelleştirmesini etkinleştirdiğinizde, sayfa UI özelleştirmesi kullanan etiketlere yönelik önceki düzenlemeleriniz Ingilizce (en) için bir JSON dosyasında kalıcıdır. Dil özelleştirmesindeki dil kaynaklarını karşıya yükleyerek etiketlerinizi ve diğer dizelerinizi değiştirmeye devam edebilirsiniz.

::: zone-end

### <a name="up-to-date-translations"></a>Güncel Çeviriler

Microsoft, kullanım için en güncel çevirileri sağlamaya kararlıdır. Microsoft, çevirileri sürekli olarak geliştirir ve sizin için uyumluluk sağlar. Microsoft, genel terminolojilerin hatalarını ve değişikliklerini belirler ve Kullanıcı akışınızda sorunsuz bir şekilde çalışacak güncelleştirmeler yapar.

### <a name="support-for-right-to-left-languages"></a>Sağdan sola diller için destek

Microsoft şu anda sağdan sola diller için destek sağlamaz. Bu bunu, özel yerel ayarları kullanarak ve dizelerin görüntülenme biçimini değiştirmek için CSS kullanarak gerçekleştirebilirsiniz. Bu özelliğe ihtiyacınız varsa lütfen [Azure geri bildirimde](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)bu uygulamayı oylayın.

### <a name="social-identity-provider-translations"></a>Sosyal kimlik sağlayıcısı çevirileri

Microsoft, `ui_locales` sosyal oturumlar için OIDC parametresini sağlar. Ancak Facebook ve Google dahil bazı sosyal kimlik sağlayıcıları bunları dikkate almaz.

### <a name="browser-behavior"></a>Tarayıcı davranışı

Chrome ve Firefox, kendi set dilleri için istek. Desteklenen bir dilise, varsayılan değer olarak gösterilir. Microsoft Edge Şu anda bir dil isteyemez ve doğrudan varsayılan dile gider.

## <a name="supported-languages"></a>Desteklenen diller

Azure AD B2C aşağıdaki diller için destek içerir. Kullanıcı akış dilleri Azure AD B2C tarafından sağlanır. Multi-Factor Authentication (MFA) bildirim dilleri [Azure AD MFA](../active-directory/authentication/concept-mfa-howitworks.md)tarafından sağlanır.

| Dil              | Dil kodu | Kullanıcı akışları         | MFA bildirimleri  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arapça                | Ar            | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Bulgarca             | bg            | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Bangla                | milyar TL            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![No belirten X](./media/user-flow-language-customization/no.png) |
| Katalanca               | yetkilisini            | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Çekçe                 | 'ye            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Danca                | kapattığımda            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Almanca                | seçimini            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Yunanca                 | seri            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| İngilizce               | en            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Spanish               | es            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Estonya Dili              | lale            | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Baskça                | yapılan            | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Fince               | Fi            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Fransızca                | kesir            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Galiçya Dili              | g            | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Gucerat dili              | çubuğu            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![No belirten X](./media/user-flow-language-customization/no.png) |
| İbranice                | LIP            | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Hintçe                 | n            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Hırvatça              | sa            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Macarca             | Hu            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Endonezce            | kimlik            | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| İtalyanca               | içerdiği            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Japonca              | Sofya            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Kazakça                | kk            | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Kannada dili               | KN            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![No belirten X](./media/user-flow-language-customization/no.png) |
| Korece                | dili            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Litvanca            | lt            | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Letonca               | aramasını            | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Malayalam dili             | ml            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![No belirten X](./media/user-flow-language-customization/no.png) |
| Marathi               | Mr            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![No belirten X](./media/user-flow-language-customization/no.png) |
| Malayca                 | SWM            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Norveççe Bokmal      | NB            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![No belirten X](./media/user-flow-language-customization/no.png) |
| Felemenkçe                 | nl            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Norveççe             | hayır            | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Pencap dili               | VARS            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![No belirten X](./media/user-flow-language-customization/no.png) |
| Lehçe                | pl            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Portekizce - Brezilya   | pt-br         | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Portekizce - Portekiz | pt-pt         | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Rumence              | ro            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Rusça               | ru            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Slovakça                | sor            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Slovence             | SL            | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Sırpça (Kiril)    | SR-cryıl-CS    | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Sırpça (Latin)       | sr-Latn-CS    | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| İsveççe               | v            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Tamil dili                 | dolu            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![No belirten X](./media/user-flow-language-customization/no.png) |
| Telugu dili                | ot            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![No belirten X](./media/user-flow-language-customization/no.png) |
| Tayca                  | 11            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Türkçe               | tr            | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Ukraynaca             | tr            | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Vietnamca            | v            | ![No belirten X](./media/user-flow-language-customization/no.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Basitleştirilmiş Çince  | zh-Hans       | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |
| Geleneksel Çince | zh-Hant       | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) | ![Yeşil onay işareti.](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>Sonraki adımlar

::: zone pivot="b2c-user-flow"

[Azure Active Directory B2C ' de uygulamanızın kullanıcı arabirimini özelleştirme](customize-ui-with-html.md)bölümünde uygulamalarınızın Kullanıcı arabirimini nasıl özelleştirebileceğinizi hakkında daha fazla bilgi edinin.

::: zone-end 

::: zone pivot="b2c-custom-policy"

- IEF başvurusunda [Yerelleştirme](localization.md) öğesi hakkında daha fazla bilgi edinin.
- Azure AD B2C bulunan [Yerelleştirme dize kimliklerinin](localization-string-ids.md) listesine bakın.

::: zone-end 
