---
title: ContentDefinitions-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C bir özel ilkenin ContentDefinitions öğesini belirtin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f8acf499d4d82c49096e4e5beff8209d0970b421
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064327"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Kendi kendini onaylanan teknik profillerinin](self-asserted-technical-profile.md)görünümünü özelleştirebilirsiniz. Azure Active Directory B2C (Azure AD B2C), kodu müşterinizin tarayıcısında çalıştırır ve çıkış noktaları arası kaynak paylaşımı (CORS) adlı modern bir yaklaşım kullanır.

Kullanıcı arabirimini özelleştirmek için, özelleştirilmiş HTML içeriğiyle **ContentDefinition** ÖĞESINDE bir URL belirtirsiniz. Kendi kendine onaylanan teknik profilde veya **Orchestrationstep**bu içerik tanımı tanımlayıcısını işaret edersiniz. İçerik tanımı, yüklenecek yerelleştirilmiş kaynakların listesini belirten bir **Localizedresourcesreferences** öğesi içerebilir. Azure AD B2C, Kullanıcı arabirimi öğelerini URL 'nizden yüklenen HTML içeriğiyle birleştirir ve ardından sayfayı kullanıcıya görüntüler.

**ContentDefinitions** öğesi, Kullanıcı yolculuğunda kullanılabilen HTML5 şablonlarının URL 'lerini içerir. HTML5 sayfa URI 'SI, belirtilen kullanıcı arabirimi adımı için kullanılır. Örneğin, oturum açma veya kaydolma, parola sıfırlama veya hata sayfaları. HTML5 dosyası için LoadUri 'yi geçersiz kılarak görünümü değiştirebilirsiniz. Gereksinimlerinize göre yeni içerik tanımları oluşturabilirsiniz. Bu öğe, [Yerelleştirme](localization.md) öğesinde belirtilen yerelleştirme tanımlayıcısına bir yerelleştirilmiş kaynaklar başvurusu içerebilir.

Aşağıdaki örnekte, yerelleştirilmiş kaynakların içerik tanımı tanımlayıcısı ve tanımı gösterilmektedir:

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LoalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

**Localaccountsignupwithlogonemail** kendi kendini onaylanan teknik profilinin meta verileri, şu şekilde ayarlanan içerik tanımı tanımlayıcı **Contentdefinitionreferenceıd** değerini içerir`api.localaccountsignup`

```XML
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
| Id | Evet | Bir içerik tanımı için tanımlayıcı. Değer, bu sayfanın ilerleyen kısımlarında bulunan **İçerik tanımı kimlikleri** bölümünde belirtilmiştir. |

**ContentDefinition** öğesi aşağıdaki öğeleri içerir:

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | İçerik tanımının HTML5 sayfasının URL 'sini içeren bir dize. |
| RecoveryUri | 0:1 | İçerik tanımıyla ilgili bir hata görüntülemek için HTML sayfasının URL 'sini içeren bir dize. |
| dataUri | 1:1 | Adım için çağrılacak Kullanıcı deneyimini sağlayan bir HTML dosyasının göreli URL 'sini içeren bir dize. |
| Meta Veriler | 1:1 | İçerik tanımı tarafından kullanılan meta verileri içeren bir anahtar/değer çiftleri koleksiyonu. |
| LocalizedResourcesReferences | 0:1 | Yerelleştirilmiş kaynaklar koleksiyonu başvuruları. Bir kullanıcı arabirimi ve talepler özniteliği yerelleştirmesini özelleştirmek için bu öğeyi kullanın. |

### <a name="datauri"></a>dataUri

**Datauri** öğesi, sayfa tanımlayıcısını belirtmek için kullanılır. Azure AD B2C, Kullanıcı arabirimi öğelerini ve istemci tarafı JavaScript 'i yüklemek ve başlatmak için sayfa tanımlayıcısını kullanır. Değerin `urn:com:microsoft:aad:b2c:elements:page-name:version`biçimi.  Aşağıdaki tabloda kullanabileceğiniz sayfa tanımlayıcılarının listesi yer almaktadır.

| Value |   Açıklama |
| ----- | ----------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | Bir özel durum veya hata ile karşılaşıldığında bir hata sayfası görüntüler. |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | Kullanıcıların oturum açma sırasında aralarından seçim yapabileceğiniz kimlik sağlayıcılarını listeler. |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | Bir e-posta adresini veya Kullanıcı adını temel alan bir yerel hesapla oturum açmak için bir form görüntüler. Bu değer ayrıca "Oturumumu Açık bırak" ve "parolanızı unuttum mı" değerlerini de sağlar. bağlantı. |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | Bir e-posta adresini veya Kullanıcı adını temel alan bir yerel hesapla oturum açmak için bir form görüntüler. |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | Kaydolma veya oturum açma sırasında metin veya ses kullanarak telefon numaralarını doğrular. |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | Kullanıcıların profillerini oluşturmalarına veya güncelleştirmesine olanak sağlayan bir form görüntüler. |


### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

**Localizedresourcesreferences** öğesi aşağıdaki öğeleri içerir:

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1: n | İçerik tanımı için yerelleştirilmiş kaynak başvurularının listesi. |

**Localizedresourcesreferences** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Dil | Evet | Dilleri tanımlamak için RFC 5646-etiketleri başına ilke için desteklenen bir dil içeren bir dize. |
| Localizedresourcesreferenceıd | Evet | **Localizedresources** öğesinin tanımlayıcısı. |

Aşağıdaki örnekte bir kaydolma veya oturum açma içerik tanımı gösterilmektedir:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
</ContentDefinition>
```

Aşağıdaki örnekte, Ingilizce, Fransızca ve Ispanyolca için yerelleştirmeye yönelik bir başvuruya sahip kaydolma veya oturum açma içerik tanımı gösterilmektedir:

```XML
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

| ID | Varsayılan şablon | Açıklama |
| -- | ---------------- | ----------- |
| **api.error** | [Exception. cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Hata sayfası** -bir özel durum veya hata ile karşılaşıldığında hata sayfası görüntüler. |
| **api. ıdpseçimlerin** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Kimlik sağlayıcısı seçim sayfası** -kullanıcıların oturum açma sırasında seçebileceği kimlik sağlayıcılarını listeler. Seçenekler genellikle kurumsal kimlik sağlayıcılardır, Facebook ve Google + gibi sosyal kimlik sağlayıcılarıdır veya yerel hesaplardır. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Kaydolma Için kimlik sağlayıcısı seçimi** -kullanıcıların kaydolma sırasında aralarından seçim yapabileceğiniz kimlik sağlayıcılarını listeler. Seçenekler genellikle kurumsal kimlik sağlayıcılardır, Facebook ve Google + gibi sosyal kimlik sağlayıcılarıdır veya yerel hesaplardır. |
| **api.localaccountpasswordreset** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Parola unuttum sayfası** -parola sıfırlama işlemini başlatmak için kullanıcıların tamamlaması gereken bir form görüntüler. |
| **api.localaccountsignin** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Yerel hesap oturum açma sayfası** -e-posta adresini veya Kullanıcı adını temel alan bir yerel hesapla oturum açmak için bir form görüntüler. Form bir metin girişi kutusu ve parola giriş kutusu içerebilir. |
| **api.localaccountsignup** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Yerel hesap kaydolma sayfası** -e-posta adresini veya Kullanıcı adını temel alan bir yerel hesaba kaydolmak için bir form görüntüler. Form, metin girişi kutusu, parola girişi kutusu, radyo düğmesi, tek seçim açılan kutuları ve çoklu seçim onay kutuları gibi çeşitli giriş denetimleri içerebilir. |
| **api. phonefactor** | [çok faktörlü-1.0.0. cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication sayfası** -kaydolma veya oturum açma sırasında telefon numaralarını metin veya ses kullanarak doğrular. |
| **api.selfasserted** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Sosyal hesap kaydolma sayfası** -kullanıcıların, sosyal kimlik sağlayıcısından mevcut bir hesabı kullanarak kaydolduklarında tamamlaması gereken bir form görüntüler. Bu sayfa, parola girişi alanları hariç, önceki sosyal hesap kaydolma sayfasına benzer. |
| **api.selfasserted.profileupdate** | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Profil güncelleştirme sayfası** -kullanıcıların, profillerini güncelleştirmek için erişebileceği bir form görüntüler. Bu sayfa, parola girişi alanları hariç sosyal hesap kaydolma sayfasına benzerdir. |
| **api.signuporsignin** | [Birleşik. html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Birleşik kaydolma veya oturum açma sayfası** -Kullanıcı kayıt ve oturum açma sürecini işler. Kullanıcılar, kurumsal kimlik sağlayıcılarını, Facebook veya Google + gibi sosyal kimlik sağlayıcılarını veya yerel hesapları kullanabilir. |

