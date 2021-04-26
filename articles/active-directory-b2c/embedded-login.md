---
title: Özel ilkeyle uygulamanıza Azure Active Directory B2C Kullanıcı arabirimi ekleme
titleSuffix: Azure AD B2C
description: Özel ilkeyle uygulamanıza Azure Active Directory B2C Kullanıcı arabirimi eklemeyi öğrenin
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: dc4450fb1e21211b43bc178d94cf4bdfe6da58e1
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256795"
---
# <a name="embedded-sign-in-experience"></a>Ekli oturum açma deneyimi

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Daha basit bir oturum açma deneyimi için, kullanıcıların ayrı bir oturum açma sayfasına veya bir açılır pencere oluşturmaya yönelik olarak yeniden yönlendirilmekten kaçınabilirsiniz. Satır içi çerçeve öğesini kullanarak `<iframe>` , Azure AD B2C oturum açma kullanıcı arabirimini doğrudan Web uygulamanıza ekleyebilirsiniz.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="web-application-embedded-sign-in"></a>Web uygulaması Embedded oturum açma

`<iframe>`BIR HTML5 Web sayfasına bir belge katıştırmak için satır içi çerçeve öğesi kullanılır. Aşağıdaki örnekte gösterildiği gibi, Azure AD B2C oturum açma kullanıcı arabirimini doğrudan Web uygulamanıza eklemek için iframe öğesini kullanabilirsiniz:

![Vurgulama DıV deneyimiyle oturum açma](media/embedded-login/login-hovering.png)

İframe kullanırken şunları göz önünde bulundurun:

- Yerleşik oturum açma yalnızca yerel hesapları destekler. Çoğu sosyal kimlik sağlayıcısı (örneğin, Google ve Facebook), oturum açma sayfalarının satır içi çerçevelerde işlenmesini engeller.
- Bir iframe içindeki Azure AD B2C oturum tanımlama bilgileri üçüncü taraf tanımlama bilgileri olarak kabul edildiğinden, bazı tarayıcılar (ör. ınbilito modunda Safari veya Chrome) Bu tanımlama bilgilerini engeller ya da temizler, böylece istenmeyen bir kullanıcı deneyimi elde edilir. Bu sorunu engellemek için, uygulama etki alanı adınızın ve Azure AD B2C etki alanının *aynı kaynağa* sahip olduğundan emin olun. Aynı kaynağı kullanmak için, Azure AD B2C kiracı için [özel etki alanlarını etkinleştirin](custom-domain.md) ve sonra Web uygulamanızı aynı kaynaktan yapılandırın. Örneğin, ' ' üzerinde barındırılan bir uygulama https://app.contoso.com , ' ' üzerinde çalışan Azure AD B2C aynı kaynağa sahip https://login.contoso.com .

## <a name="prerequisites"></a>Önkoşullar

* [Active Directory B2C içindeki özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)adımlarını izleyin.
* İlkeleriniz için [özel etki alanlarını etkinleştirin](custom-domain.md) .

## <a name="configure-your-policy"></a>İlkenizi yapılandırma

Azure AD B2C Kullanıcı arayüzün iframe 'e katıştırılması için, `Content-Security-Policy` `X-Frame-Options` Azure AD B2C http yanıt üst bilgilerine bir içerik güvenlik ilkesi ve çerçeve seçenekleri eklenmelidir. Bu üstbilgiler Azure AD B2C Kullanıcı arabiriminin uygulama etki alanı adınızın altında çalışmasına izin verir.

[RelyingParty](relyingparty.md) öğesinin içine bir **IConnectionPoint** öğesi ekleyin.  **User, Neyıdavranışlar** öğesi **Defaultuseryolculuney** izlemelidir. **Kullanıcıbağlantısı Neydavranışlar** öğesi şu örnekteki gibi görünmelidir:

```xml
<!--
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" /> -->
  <UserJourneyBehaviors> 
    <JourneyFraming Enabled="true" Sources="https://somesite.com https://anothersite.com" /> 
  </UserJourneyBehaviors>
<!--
</RelyingParty> -->
```

**Sources** özniteliği, Web uygulamanızın URI 'sini içerir. URI 'Ler arasına boşluk ekleyin. Her URI aşağıdaki gereksinimlere uymalıdır:

- URI 'nin güvenilir ve uygulamanıza ait olması gerekir.
- URI, https düzenini kullanmalıdır.  
- Web uygulamasının tam URI 'SI belirtilmelidir. Joker karakterler desteklenmez.

Ayrıca, uygulama sayfalarınızda sırasıyla Content-Security-Policy ve X-Frame-Options üstbilgilerini ayarlayarak kendi etki alanı adınızın iframe 'e gömülmesini de engellemeniz önerilir. Bu, eski tarayıcılardaki iframe 'leri iç içe ekleme ile ilgili güvenlik konularını azaltır.

## <a name="adjust-policy-user-interface"></a>İlke Kullanıcı arabirimini ayarla

[Kullanıcı arabirimi özelleştirmesi](customize-ui.md)Azure AD B2C, KULLANıCıLARA sunulan HTML ve CSS içeriği üzerinde neredeyse tam denetime sahip olursunuz. İçerik tanımlarını kullanarak HTML sayfasını özelleştirmeye yönelik adımları izleyin. Azure AD B2C Kullanıcı arabirimini iframe boyutuna sığdırmak için, arka plan ve ek boşluklar olmadan temiz HTML sayfası sağlayın.  

Aşağıdaki CSS kodu, Azure AD B2C HTML öğelerini gizler ve panelin boyutunu iframe 'i dolduracak şekilde ayarlar.

```css
div.social, div.divider {
    display: none;
}

div.api_container{
    padding-top:0;
}

.panel {
    width: 100%!important
}
```

Bazı durumlarda, uygulamanız için Azure AD B2C sayfanın hangi sırada gösterilmekte olduğunu bildirmek isteyebilirsiniz. Örneğin, bir Kullanıcı kaydolma seçeneğini seçtiğinde, bir sosyal hesapla oturum açma veya iframe boyutunu ayarlama bağlantılarını gizleyerek uygulamanın yanıt vermesini isteyebilirsiniz.

Geçerli Azure AD B2C sayfasında uygulamanızı bilgilendirmek için, [JavaScript için ilkenizi etkinleştirin](./javascript-and-page-layout.md)ve sonra HTML5 Post iletileri kullanın. Aşağıdaki JavaScript kodu, ile uygulamaya bir gönderi iletisi gönderir `signUp` :

```javascript
window.parent.postMessage("signUp", '*');
```

## <a name="configure-a-web-application"></a>Web uygulaması yapılandırma

Bir Kullanıcı oturum açma düğmesini seçtiğinde, [Web uygulaması](code-samples.md#web-apps-and-apis) , kullanıcıyı Azure AD B2C oturum açma deneyimine alan bir yetkilendirme isteği oluşturur. Oturum açma işlemi tamamlandıktan sonra, Azure AD B2C uygulamanızın içindeki yapılandırılmış yeniden yönlendirme URI 'sine bir KIMLIK belirteci veya yetkilendirme kodu döndürür.

Eklenmiş oturum açma özelliğini desteklemek için, iframe **src** özelliği, `/account/SignUpSignIn` Yetkilendirme isteğini oluşturan ve kullanıcıyı Azure AD B2C ilkeye yönlendiren bir oturum açma denetleyicisine işaret eder.

```html
<iframe id="loginframe" frameborder="0" src="/account/SignUpSignIn"></iframe>
``` 

KIMLIK belirteci uygulama tarafından alınıp doğrulandıktan sonra, yetkilendirme akışı tamamlanır ve uygulama kullanıcıyı tanır ve güvenir. Yetkilendirme akışı iframe içinde gerçekleştiğinden, ana sayfayı yeniden yüklemeniz gerekir. Sayfa yeniden yüklendikten sonra, oturum açma düğmesi "Oturumu Kapat" olarak değişir ve Kullanıcı adı Kullanıcı arabiriminde görüntülenir.  

Aşağıdaki örnek, oturum açma yeniden yönlendirme URI 'sinin ana sayfayı nasıl yenileyebileceğinizi gösteren bir örnektir:

```javascript
window.top.location.reload();
```

### <a name="add-sign-in-with-social-accounts-to-a-web-app"></a>Web uygulamasına sosyal hesaplar ile oturum açma ekleme

Sosyal kimlik sağlayıcıları, oturum açma sayfalarının satır içi çerçevelerde işlenmesini engeller. Sosyal hesaplar için ayrı bir ilke kullanabilir veya hem yerel hem de sosyal hesaplar ile oturum açma ve kaydolma için tek bir ilke kullanabilirsiniz. Ardından `domain_hint` sorgu dizesi parametresini kullanabilirsiniz. Etki alanı ipucu parametresi, kullanıcıyı doğrudan sosyal kimlik sağlayıcısının oturum açma sayfasına götürür.

Uygulamanızda, sosyal Hesap düğmelerini kullanarak oturum açın. Kullanıcı sosyal hesap düğmelerinden birine tıkladığında, denetimin ilke adını değiştirmesi veya etki alanı ipucu parametresini ayarlaması gerekir.

<!-- TBD: add a diagram -->

Yeniden yönlendirme URI 'SI iframe tarafından kullanılan yeniden yönlendirme URI 'SI olabilir. Sayfa yeniden yükleme işlemini atlayabilirsiniz.

## <a name="configure-a-single-page-application"></a>Tek sayfalı bir uygulama yapılandırma

Tek sayfalı bir uygulama için, iframe 'e yüklenen ikinci bir "oturum açma" HTML sayfası da gerekir. Bu oturum açma sayfası, yetkilendirme kodunu üreten ve belirteci döndüren kimlik doğrulama kitaplığı kodunu barındırır.

Tek sayfalı uygulama erişim belirtecine ihtiyaç duyduğunda, JavaScript kodunu kullanarak iframe ve nesneyi içeren nesneden erişim belirtecini elde edin.

> [!NOTE]
> İframe 'de MSAL 2,0 çalıştırmak şu anda desteklenmiyor.

Aşağıdaki kod, ana sayfada çalışan ve iframe 'nin JavaScript kodunu çağıran bir örnektir:

```javascript
function getToken()
{
  var token = document.getElementById("loginframe").contentWindow.getToken("adB2CSignInSignUp");

  if (token === "LoginIsRequired")
    document.getElementById("tokenTextarea").value = "Please login!!!"
  else
    document.getElementById("tokenTextarea").value = token.access_token;
}

function logOut()
{
  document.getElementById("loginframe").contentWindow.policyLogout("adB2CSignInSignUp", "B2C_1A_SignUpOrSignIn");
}
```

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki ilgili makalelere bakın:

- [Kullanıcı arabirimini özelleştirme](customize-ui.md)
- [RelyingParty](relyingparty.md) öğesi başvurusu
- [JavaScript için ilkenizi etkinleştirme](./javascript-and-page-layout.md)
- [Kod örnekleri](code-samples.md)

::: zone-end
