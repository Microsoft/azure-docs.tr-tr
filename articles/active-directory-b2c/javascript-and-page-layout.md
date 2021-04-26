---
title: JavaScript ve sayfa düzeni sürümleri
titleSuffix: Azure AD B2C
description: JavaScript 'ı etkinleştirme ve Azure Active Directory B2C sayfa düzeni sürümlerini kullanma hakkında bilgi edinin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d234e57f7c11b0d9f2cd212bde93a8b8e478ef41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781373"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C içindeki JavaScript ve sayfa düzeni sürümleri

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

Azure AD B2C, Kullanıcı akışlarınızda ve özel ilkelerindeki Kullanıcı arabirimi öğeleri için HTML, CSS ve JavaScript içeren bir paketlenmiş içerik kümesi sağlar. Uygulamalarınız için JavaScript 'ı etkinleştirmek üzere:

::: zone pivot="b2c-user-flow"

* [Sayfa düzeni](page-layout.md) seçin
* Azure portal kullanarak Kullanıcı akışında etkinleştirin
* İsteklerinizi [b2clogin.com](b2clogin.md) kullanma

::: zone-end

::: zone pivot="b2c-custom-policy"

* [Sayfa düzeni](page-layout.md) seçin
* [Özel ilkenize](custom-policy-overview.md) bir öğe ekleyin
* İsteklerinizi [b2clogin.com](b2clogin.md) kullanma

::: zone-end

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="select-a-page-layout-version"></a>Sayfa düzeni sürümü seçin

JavaScript istemci tarafı kodunu etkinleştirmek istiyorsanız, JavaScript 'i temel alan öğelerin sabit olması gerekir. Sabit olmadıkları takdirde, herhangi bir değişiklik Kullanıcı sayfalarınızda beklenmeyen davranışlara neden olabilir. Bu sorunları engellemek için, bir sayfa düzeni kullanımını zorunlu tutun ve JavaScript 'i temel alan içerik tanımlarının sabit olduğundan emin olmak için bir sayfa düzeni sürümü belirtin. JavaScript 'i etkinleştirmeyi amaçlamadığınız halde, sayfalarınız için bir sayfa düzeni sürümü belirtebilirsiniz.

::: zone pivot="b2c-user-flow"

Kullanıcı akış sayfalarınız için bir sayfa düzeni sürümü belirtmek için: 

1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
1. İlkeyi (örneğin, "B2C_1_SignupSignin") seçerek açın.
1. **Sayfa düzenlerini** seçin. Bir **Düzen adı** seçin ve ardından **sayfa düzeni sürümünü (Önizleme)** seçin.

Farklı sayfa düzeni sürümleri hakkında daha fazla bilgi için bkz. [sayfa düzeni sürümü değişiklik günlüğü](page-layout.md).

![Portalda sayfa düzeni sürüm açılan listesini gösteren sayfa düzeni ayarları](media/javascript-and-page-layout/page-layout-version.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Uygulamanızın Kullanıcı arabirimi öğeleri için bir [sayfa düzeni](contentdefinitions.md#select-a-page-layout) seçin.

[](contentdefinitions.md#migrating-to-page-layout) `contract` Özel ilkenizde *Tüm* içerik tanımlarının sayfa sürümüyle birlikte bir sayfa düzeni sürümü tanımlayın. Değerin biçimi şu kelimeyi içermelidir: `contract` _urn: com: Microsoft: AAD: B2C: Elements:**anlaşma**:p Age-Name: Version_. Sayfa sürümüne sahip [sayfa düzenine geçiş](contentdefinitions.md#migrating-to-page-layout) yapmayı öğrenin.

Aşağıdaki örnekte, sayfa sözleşmesiyle birlikte içerik tanımı tanımlayıcıları ve karşılık gelen **veri URI 'si** gösterilmektedir: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end

## <a name="enable-javascript"></a>JavaScript'i etkinleştirme

::: zone pivot="b2c-user-flow"

Kullanıcı akışı **özelliklerinde** JavaScript 'i etkinleştirebilirsiniz. JavaScript 'in etkinleştirilmesi, sayfa düzeninin kullanımını da zorunlu kılar. Daha sonra, sonraki bölümde açıklandığı gibi Kullanıcı akışının sayfa düzeni sürümünü ayarlayabilirsiniz.

![JavaScript ayarlarını etkinleştir seçeneği vurgulanmış şekilde Kullanıcı akışı özellikleri sayfası](media/javascript-and-page-layout/javascript-settings.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

**Scriptexecution** öğesini [RelyingParty](relyingparty.md) öğesine ekleyerek betik yürütmeyi etkinleştirirsiniz.

1. Özel ilke dosyanızı açın. Örneğin, *SignUpOrSignin.xml*.
1. **Scriptexecution** öğesini **RelyingParty** öğesine ekleyin:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
1. Dosyayı kaydedin ve karşıya yükleyin.

::: zone-end

## <a name="guidelines-for-using-javascript"></a>JavaScript kullanımı için yönergeler

JavaScript kullanarak uygulamanızın arabirimini özelleştirirken aşağıdaki yönergeleri izleyin:

- HTML öğelerine bir tıklama olayı bağlamayın `<a>` .
- Azure AD B2C koduna veya açıklamalara bağımlılık almaz.
- Azure AD B2C HTML öğelerinin sırasını veya hiyerarşisini değiştirmeyin. Kullanıcı arabirimi öğelerinin sırasını denetlemek için bir Azure AD B2C İlkesi kullanın.
- Şu noktalara sahip herhangi bir yeniden kullanılabilir hizmeti çağırabilirsiniz:
    - İstemci tarafı HTTP çağrılarına izin vermek için, Restınservıce CORS 'nizi ayarlamanız gerekebilir.
    - Yeniden yaptığınız hizmetin güvende olduğundan ve yalnızca HTTPS protokolünü kullandığından emin olun.
    - Azure AD B2C uç noktaları çağırmak için JavaScript 'ı doğrudan kullanmayın.
- JavaScript kodunuzu ekleyebilir veya dış JavaScript dosyalarına bağlanabilirsiniz. Bir dış JavaScript dosyası kullanırken göreli URL değil, mutlak URL 'yi kullandığınızdan emin olun.
- JavaScript çerçeveleri:
    - Azure AD B2C [, jQuery 'in belirli bir sürümünü](page-layout.md#jquery-version)kullanır. JQuery 'in başka bir sürümünü eklemeyin. Aynı sayfada birden fazla sürüm kullanılması sorunlara neden olur.
    - RequireJS kullanmak desteklenmez.
    - Çoğu JavaScript çerçevesi Azure AD B2C tarafından desteklenmez.
- Azure AD B2C ayarları `window.SETTINGS` , `window.CONTENT` geçerli kullanıcı arabirimi dili gibi nesneler çağırarak okunabilir. Bu nesnelerin değerini değiştirmeyin.
- Azure AD B2C hata iletisini özelleştirmek için, bir ilkede yerelleştirme kullanın.
- Bir ilke kullanılarak herhangi bir şey elde edilebildiği takdirde, genellikle önerilen yoldur.

## <a name="javascript-samples"></a>JavaScript örnekleri

### <a name="show-or-hide-a-password"></a>Parolayı göster veya gizle

Müşterilerinizin kaydolma başarısı ile müşterilerinizin, parola olarak girdiklerinizi görmesine yardımcı olmanın yaygın bir yolu. Bu seçenek, kullanıcıların gerektiğinde parolalarını kolayca görmesini ve bunlara yönelik düzeltmeler yapmasını sağlayarak kaydolmalarına yardımcı olur. Parola türünde herhangi bir alanda, **parolayı göster** etiketi olan bir onay kutusu vardır.  Bu, kullanıcının parolayı düz metin olarak görmesini sağlar. Bu kod parçacığını, otomatik olarak onaylanan bir sayfanın kayıt veya oturum açma şablonuna ekleyin:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Kullanım koşulları ekleme

Aşağıdaki kodu sayfanıza bir **kullanım koşulları** onay kutusu eklemek istediğiniz yere ekleyin. Bu onay kutusu genellikle yerel hesap kaydolma ve sosyal hesap kaydolma sayfalarınızda gereklidir.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

Kodda, `termsOfUseUrl` kullanım koşullarınızın anlaşmasıyla bağlantı ile değiştirin. Dizininiz için, **termsofuse** adlı yeni bir kullanıcı özniteliği oluşturun ve ardından Kullanıcı özniteliği olarak **termsofuse** ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Active Directory B2C ' de uygulamanızın kullanıcı arabirimini özelleştirme](customize-ui-with-html.md)bölümünde uygulamalarınızın Kullanıcı arabirimini nasıl özelleştirebileceğinizi hakkında daha fazla bilgi edinin.
