---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C bir özel ilkenin ContentDefinitions öğesini belirtin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62bae22b6a4bb06b1e97c18e52ad614fd2439902
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489330"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Kendi kendini onaylanan teknik profillerinin](self-asserted-technical-profile.md)görünümünü özelleştirebilirsiniz. Azure Active Directory B2C (Azure AD B2C), kodu müşterinizin tarayıcısında çalıştırır ve çıkış noktaları arası kaynak paylaşımı (CORS) adlı modern bir yaklaşım kullanır.

Kullanıcı arabirimini özelleştirmek için, özelleştirilmiş HTML içeriğiyle **ContentDefinition** ÖĞESINDE bir URL belirtirsiniz. Kendi kendine onaylanan teknik profilde veya **Orchestrationstep** bu içerik tanımı tanımlayıcısını işaret edersiniz. İçerik tanımı, yüklenecek yerelleştirilmiş kaynakların listesini belirten bir **Localizedresourcesreferences** öğesi içerebilir. Azure AD B2C, Kullanıcı arabirimi öğelerini URL 'nizden yüklenen HTML içeriğiyle birleştirir ve ardından sayfayı kullanıcıya görüntüler.

**ContentDefinitions** öğesi, Kullanıcı yolculuğunda kullanılabilen HTML5 şablonlarının URL 'lerini içerir. HTML5 sayfa URI 'SI, belirtilen kullanıcı arabirimi adımı için kullanılır. Örneğin, oturum açma veya kaydolma, parola sıfırlama veya hata sayfaları. HTML5 dosyası için LoadUri 'yi geçersiz kılarak görünümü değiştirebilirsiniz. Gereksinimlerinize göre yeni içerik tanımları oluşturabilirsiniz. Bu öğe, [Yerelleştirme](localization.md) öğesinde belirtilen yerelleştirme tanımlayıcısına bir yerelleştirilmiş kaynaklar başvurusu içerebilir.

Aşağıdaki örnekte, yerelleştirilmiş kaynakların içerik tanımı tanımlayıcısı ve tanımı gösterilmektedir:

```xml
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

**Localaccountsignupwithlogonemail** kendi kendini onaylanan teknik profilinin meta verileri, şu şekilde ayarlanan içerik tanımı tanımlayıcı **Contentdefinitionreferenceıd** değerini içerir`api.localaccountsignup`

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>ContentDefinition

**ContentDefinition** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Id | Yes | Bir içerik tanımı için tanımlayıcı. Değer, bu sayfanın ilerleyen kısımlarında bulunan **İçerik tanımı kimlikleri** bölümünde belirtilmiştir. |

**ContentDefinition** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | İçerik tanımının HTML5 sayfasının URL 'sini içeren bir dize. |
| RecoveryUri | 1:1 | İçerik tanımıyla ilgili bir hata görüntülemek için HTML sayfasının URL 'sini içeren bir dize. Şu anda kullanılmıyor, değer olmalıdır `~/common/default_page_error.html` . |
| DataUri | 1:1 | Adım için çağrılacak Kullanıcı deneyimini sağlayan bir HTML dosyasının göreli URL 'sini içeren bir dize. |
| Meta veri | 0:1 | İçerik tanımı tarafından kullanılan meta verileri içeren bir anahtar/değer çiftleri koleksiyonu. |
| LocalizedResourcesReferences | 0:1 | Yerelleştirilmiş kaynaklar koleksiyonu başvuruları. Bir kullanıcı arabirimi ve talepler özniteliği yerelleştirmesini özelleştirmek için bu öğeyi kullanın. |

### <a name="datauri"></a>DataUri

**Datauri** öğesi, sayfa tanımlayıcısını belirtmek için kullanılır. Azure AD B2C, Kullanıcı arabirimi öğelerini ve istemci tarafı JavaScript 'i yüklemek ve başlatmak için sayfa tanımlayıcısını kullanır. Değerin biçimi `urn:com:microsoft:aad:b2c:elements:page-name:version` . Aşağıdaki tabloda kullanabileceğiniz sayfa tanımlayıcıları listelenmektedir.

| Sayfa tanımlayıcısı | Description |
| ----- | ----------- |
| `globalexception` | Bir özel durum veya hata ile karşılaşıldığında bir hata sayfası görüntüler. |
| `providerselection`, `idpselection` | Kullanıcıların oturum açma sırasında aralarından seçim yapabileceğiniz kimlik sağlayıcılarını listeler.  |
| `unifiedssp` | Bir e-posta adresini veya Kullanıcı adını temel alan bir yerel hesapla oturum açmak için bir form görüntüler. Bu değer ayrıca "Oturumumu Açık bırak" ve "parolanızı unuttum mı" değerlerini de sağlar. ücretsiz bir hesap oluşturabilirsiniz. |
| `unifiedssd` | Bir e-posta adresini veya Kullanıcı adını temel alan bir yerel hesapla oturum açmak için bir form görüntüler. |
| `multifactor` | Kaydolma veya oturum açma sırasında metin veya ses kullanarak telefon numaralarını doğrular. |
| `selfasserted` | Kullanıcıdan veri toplamak için bir form görüntüler. Örneğin, kullanıcıların profillerini oluşturmalarına veya güncelleştirmesine olanak sağlar. |

### <a name="select-a-page-layout"></a>Sayfa düzeni seçin

İle sayfa türü arasına ekleyerek [JavaScript istemci tarafı kodunu](javascript-and-page-layout.md) etkinleştirebilirsiniz `contract` `elements` . Örneğin, `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Öğesinin [Sürüm](page-layout.md) bölümü, `DataUri` ilkenizde kullanıcı ARABIRIMI öğeleri IÇIN HTML, CSS ve JavaScript içeren içerik paketini belirtir. JavaScript istemci tarafı kodunu etkinleştirmek istiyorsanız, JavaScript 'i temel alan öğelerin sabit olması gerekir. Sabit olmadıkları takdirde, herhangi bir değişiklik Kullanıcı sayfalarınızda beklenmeyen davranışlara neden olabilir. Bu sorunları engellemek için, bir sayfa düzeni kullanımını zorunlu tutun ve bir sayfa düzeni sürümü belirtin. Bunun yapılması, JavaScript 'i temel alan tüm içerik tanımlarının sabit olmasını sağlar. JavaScript 'i etkinleştirmeyi amaçlamadığınız halde, sayfalarınız için sayfa düzeni sürümünü de belirtmeniz gerekir.

Aşağıdaki örnek, sürümünün **Dataurı** 'sini göstermektedir `selfasserted` `1.2.0` :

```xml
<ContentDefinition Id="api.localaccountpasswordreset">
<LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
<RecoveryUri>~/common/default_page_error.html</RecoveryUri>
<DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
<Metadata>
    <Item Key="DisplayName">Local account change password page</Item>
</Metadata>
</ContentDefinition>
```

#### <a name="migrating-to-page-layout"></a>Sayfa düzenine geçiriliyor

Değerin biçimi şu kelimeyi içermelidir: `contract` _urn: com: Microsoft: AAD: B2C: Elements:**anlaşma**:p Age-Name: Version_. Özel ilkeleriniz içinde eski bir **veri URI** değeri kullanan bir sayfa düzeni belirtmek için, yeni biçime geçiş yapmak üzere aşağıdaki tabloyu kullanın.

| Eski DataUri değeri | Yeni DataUri değeri |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |

Aşağıdaki örnekte, içerik tanımı tanımlayıcıları ve en son sayfa sürümüne karşılık gelen **veri URI 'si** gösterilmektedir: 

```xml
<!-- 
<BuildingBlocks> -->
  <ContentDefinitions>
    <ContentDefinition Id="api.error">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections.signup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted.profileupdate">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.phonefactor">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.2</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!-- 
</BuildingBlocks> -->
```

### <a name="metadata"></a>Meta veri

**Meta veri** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| Öğe | 0: n | İçerik tanımıyla ilgili meta veriler. |

**Meta veri** öğesinin **Item** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Anahtar | Yes | Meta veri anahtarı.  |

#### <a name="metadata-keys"></a>Meta veri anahtarları

İçerik tanımı aşağıdaki meta veri öğelerini destekler:

| Anahtar | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| DisplayName | No | İçerik tanımının adını içeren bir dize. |

### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

**Localizedresourcesreferences** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1: n | İçerik tanımı için yerelleştirilmiş kaynak başvurularının listesi. |

**Localizedresourcesreference** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Dil | Yes | Dilleri tanımlamak için RFC 5646-etiketleri başına ilke için desteklenen bir dil içeren bir dize. |
| Localizedresourcesreferenceıd | Yes | **Localizedresources** öğesinin tanımlayıcısı. |

Aşağıdaki örnekte, Ingilizce, Fransızca ve Ispanyolca için yerelleştirmeye yönelik bir başvuruya sahip kaydolma veya oturum açma içerik tanımı gösterilmektedir:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

İçerik tanımlarınıza yerelleştirme desteğinin nasıl ekleneceğini öğrenmek için bkz. [Yerelleştirme](localization.md).

## <a name="content-definition-ids"></a>İçerik tanımı kimlikleri

**ContentDefinition** öğesinin ID özniteliği, içerik tanımıyla ilgili sayfa türünü belirtir. Öğesi, özel bir HTML5/CSS şablonunun uygulanacağı bağlamı tanımlar. Aşağıdaki tabloda, kimlik deneyimi çerçevesi tarafından tanınan içerik tanımı kimlikleri ve bunlarla ilgili sayfa türleri açıklanmaktadır. Rastgele bir KIMLIK ile kendi içerik tanımlarınızı oluşturabilirsiniz.

| ID | Varsayılan şablon | Description |
| -- | ---------------- | ----------- |
| **api. Error** | [Exception. cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Hata sayfası** -bir özel durum veya hata ile karşılaşıldığında hata sayfası görüntüler. |
| **api. ıdpseçimlerin** | [ıdpselector. cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Kimlik sağlayıcısı seçim sayfası** -kullanıcıların oturum açma sırasında seçebileceği kimlik sağlayıcılarını listeler. Seçenekler genellikle kurumsal kimlik sağlayıcılardır, Facebook ve Google + gibi sosyal kimlik sağlayıcılarıdır veya yerel hesaplardır. |
| **api. ıdpseçimlerin. Signup** | [ıdpselector. cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Kaydolma Için kimlik sağlayıcısı seçimi** -kullanıcıların kaydolma sırasında aralarından seçim yapabileceğiniz kimlik sağlayıcılarını listeler. Seçenekler genellikle kurumsal kimlik sağlayıcılardır, Facebook ve Google + gibi sosyal kimlik sağlayıcılarıdır veya yerel hesaplardır. |
| **api. localaccountpasswordreset** | [selfassırted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Parola unuttum sayfası** -parola sıfırlama işlemini başlatmak için kullanıcıların tamamlaması gereken bir form görüntüler. |
| **api. localaccountsignın** | [selfassırted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Yerel hesap oturum açma sayfası** -e-posta adresini veya Kullanıcı adını temel alan bir yerel hesapla oturum açmak için bir form görüntüler. Form bir metin girişi kutusu ve parola giriş kutusu içerebilir. |
| **api. localaccountsignup** | [selfassırted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Yerel hesap kaydolma sayfası** -e-posta adresini veya Kullanıcı adını temel alan bir yerel hesaba kaydolmak için bir form görüntüler. Form, metin girişi kutusu, parola girişi kutusu, radyo düğmesi, tek seçim açılan kutuları ve çoklu seçim onay kutuları gibi çeşitli giriş denetimleri içerebilir. |
| **api. phonefactor** | [çok faktörlü-1.0.0. cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication sayfası** -kaydolma veya oturum açma sırasında telefon numaralarını metin veya ses kullanarak doğrular. |
| **api. selfasted** | [selfassırted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Sosyal hesap kaydolma sayfası** -kullanıcıların, sosyal kimlik sağlayıcısından mevcut bir hesabı kullanarak kaydolduklarında tamamlaması gereken bir form görüntüler. Bu sayfa, parola girişi alanları hariç, önceki sosyal hesap kaydolma sayfasına benzer. |
| **api. selfasserted. profileUpdate** | [updateprofile. cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Profil güncelleştirme sayfası** -kullanıcıların, profillerini güncelleştirmek için erişebileceği bir form görüntüler. Bu sayfa, parola girişi alanları hariç sosyal hesap kaydolma sayfasına benzerdir. |
| **api. signuporsignın** | [Birleşik. cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Birleşik kaydolma veya oturum açma sayfası** -Kullanıcı kayıt ve oturum açma sürecini işler. Kullanıcılar, kurumsal kimlik sağlayıcılarını, Facebook veya Google + gibi sosyal kimlik sağlayıcılarını veya yerel hesapları kullanabilir. |

## <a name="next-steps"></a>Sonraki adımlar

İçerik tanımlarını kullanarak Kullanıcı arabirimini özelleştirmenin bir örneği için bkz.:

[Özel bir ilke kullanarak uygulamanızın kullanıcı arabirimini özelleştirme](customize-ui-with-html.md)
