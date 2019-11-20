---
title: Yerelleştirme-Azure Active Directory B2C
description: Azure Active Directory B2C bir özel ilkenin yerelleştirme öğesini belirtin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ec9b4e7ce761d524d047f4d12cab9e5b782e6032
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70033459"
---
# <a name="localization"></a>Yerelleştirme

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Yerelleştirme** öğesi, Kullanıcı yolculukları için ilkedeki birden çok yerel ayarı veya dili desteketmenize olanak tanır. İlkelerde yerelleştirme desteği şunları yapmanıza olanak sağlar:

- Bir ilkede desteklenen dillerin açık listesini ayarlayın ve varsayılan bir dil seçin.
- Dile özgü dizeler ve koleksiyonlar sağlayın.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

**Yerelleştirme** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Enabled | Hayır | Olası değerler: `true` veya `false`. |

**Yerelleştirme** Öğesı aşağıdaki XML öğelerini içerir

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | Desteklenen dillerin listesi. |
| LocalizedResources | 0: n | Yerelleştirilmiş kaynakların listesi. |

## <a name="supportedlanguages"></a>SupportedLanguages

**Supportedlanguages** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| DefaultLanguage | Evet | Yerelleştirilmiş kaynaklar için varsayılan olarak kullanılacak dil. |
| MergeBehavior | Hayır | Aynı tanımlayıcıya sahip bir üst ilkede bulunan tüm ClaimType ile birlikte birleştirilmiş değerlerin sabit listesi değerleri. Temel ilkede belirtilen bir talebin üzerine yazdığınızda bu özniteliği kullanın. Olası değerler: `Append`, `Prepend`, veya `ReplaceAll`. `Append` Değer, var olan veri koleksiyonunun üst ilkede belirtilen koleksiyonun sonuna eklenmesi gerektiğini belirtir. `Prepend` Değer, var olan veri koleksiyonunun üst ilkede belirtilen koleksiyondan önce eklenmesi gerektiğini belirtir. `ReplaceAll` Değer, üst ilkede tanımlanan veri koleksiyonunun, bunun yerine geçerli ilkede tanımlanan veriler kullanılarak yoksayılacağını belirtir. |

### <a name="supportedlanguages"></a>SupportedLanguages

**Supportedlanguages** öğesi aşağıdaki öğeleri içerir:

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Dilleri tanımlamak için RFC 5646-Tags başına bir dil etiketine uyan içeriği görüntüler. |

## <a name="localizedresources"></a>LocalizedResources

**Localizedresources** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Id | Evet | Yerelleştirilmiş kaynakları benzersiz şekilde tanımlamak için kullanılan bir tanımlayıcı. |

**Localizedresources** öğesi aşağıdaki öğeleri içerir:

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Çeşitli kültürlerde tüm koleksiyonları tanımlar. Bir koleksiyon çeşitli kültürler için farklı sayıda öğe ve farklı dize içerebilir. Koleksiyon örnekleri, talep türlerinde görünen numaralandırmaları içerir. Örneğin, bir ülke/bölge listesi kullanıcıya bir açılan listede gösterilir. |
| LocalizedStrings | 0: n | Çeşitli kültürlerde koleksiyonlarda görünen dizeler hariç tüm dizeleri tanımlar. |

### <a name="localizedcollections"></a>LocalizedCollections

**Localizedcollections** öğesi aşağıdaki öğeleri içerir:

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1: n | Desteklenen dillerin listesi. |

#### <a name="localizedcollection"></a>LocalizedCollection

**Localizedcollection** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ElementType | Evet | İlke dosyasında bir ClaimType öğesine veya bir kullanıcı arabirimi öğesine başvurur. |
| ElementID | Evet | **ElementType** bir ClaimType olarak ayarlandıysa kullanılan ClaimsSchema bölümünde zaten tanımlanmış olan bir talep türüne başvuru içeren bir dize. |
| TargetCollection | Evet | Hedef koleksiyon. |

**Localizedcollection** öğesi aşağıdaki öğeleri içerir:

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| Öğe | 0: n | Kullanıcının, açılan menüdeki bir değer gibi kullanıcı arabirimindeki bir talep için seçim yapmak üzere kullanılabilir bir seçenek tanımlar. |

**Öğe** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Text | Evet | Bu seçenek için Kullanıcı arabiriminde kullanıcıya gösterilmesi gereken kullanıcı dostu görüntüleme dizesi. |
| Value | Evet | Bu seçeneği belirleyerek ilişkili dize talep değeri. |
| SelectByDefault | Hayır | Bu seçeneğin Kullanıcı arabiriminde varsayılan olarak seçilmesinin gerekip gerekmediğini gösterir. Olası değerler: TRUE veya False. |

Aşağıdaki örnek, **Localizedcollections** öğesinin kullanımını gösterir. Biri Ingilizce ve diğeri Ispanyolca için olmak üzere iki **Localizedcollection** öğesi içerir. Her ikisi de , `Gender` bir öğenin kısıtlama koleksiyonunu İngilizce ve İspanyolca için bir öğe listesiyle ayarlayın.

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

**Localizedstrings** öğesi aşağıdaki öğeleri içerir:

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | Yerelleştirilmiş bir dize. |

**LocalizedString** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ElementType | Evet | İlkede bir talep türü öğesine veya bir kullanıcı arabirimi öğesine başvuru. Olası değerler: `ClaimType`, `UxElement`, `ErrorMessage` ,veya.`Predicate` `ClaimType` Değer, strıngıd içinde belirtildiği gibi talep özniteliklerinden birini yerelleştirmek için kullanılır. `UxElement` Değer, strıngıd içinde belirtilen kullanıcı arabirimi öğelerinden birini yerelleştirmek için kullanılır. `ErrorMessage` Değer, strıngıd içinde belirtilen sistem hatası iletilerinden birini yerelleştirmek için kullanılır. Değer, strıngıd içinde belirtildiği gibi, koşul hata iletilerinden birini yerelleştirmek için kullanılır. [](predicates.md) `Predicate` Değer, strıngıd içinde belirtilen tahmine atevalidation grubu hata iletilerinden birini yerelleştirmek için kullanılır. [](predicates.md) `InputValidation` |
| ElementID | Evet | **ElementType** `ClaimType`, `Predicate`veya olarakayarlandıysa,buöğeclaimsschemabölümündezatentanımlanmışolanbirtaleptürünebaşvuruiçerir.`InputValidation` |
| StringID | Evet | **ElementType** olarak `ClaimType`ayarlandıysa, bu öğe bir talep türü özniteliğine başvuru içerir. Olası değerler: `DisplayName`, `AdminHelpText`, veya `PatternHelpText`. `DisplayName` Değer, talep görünen adını ayarlamak için kullanılır. `AdminHelpText` Değer, talep kullanıcısının yardım metni adını ayarlamak için kullanılır. `PatternHelpText` Değer, talep deseninin yardım metnini ayarlamak için kullanılır. **ElementType** olarak `UxElement`ayarlandıysa, bu öğe bir kullanıcı arabirimi öğesinin özniteliğine bir başvuru içerir. **ElementType** olarak `ErrorMessage`ayarlandıysa, bu öğe bir hata iletisinin tanımlayıcısını belirtir. `UxElement` Tanımlayıcıların tüm listesi için bkz. [Yerelleştirme dize kimlikleri](localization-string-ids.md) .|


Aşağıdaki örnekte, yerelleştirilmiş kaydolma sayfası gösterilmektedir. İlk üç **LocalizedString** değeri talep özniteliğini ayarlar. Üçüncü, devam düğmesinin değerini değiştirir. Son bir hata iletisini değiştirir.

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Aşağıdaki örnek, kimliğine `IsLengthBetween8And64`sahip **koşulun** **userhelptext** değerini yerelleştirilmiş olarak gösterir. Ve `StrongPassword`tahmine ategroup 'un kimliği ile tahmine `CharacterClasses` **atevalidation** kimliği ile yerelleştirilmiş bir **userhelptext** .

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>
```

## <a name="set-up-localization"></a>Yerelleştirmeyi ayarlama

Bu makalede, Kullanıcı bağlantısı için ilkedeki birden çok yerel ayarı veya dili nasıl destekleyeceği gösterilmektedir. Yerelleştirme üç adım gerektirir: desteklenen dillerin açık listesini ayarlayın, dile özgü dizeler ve koleksiyonlar sağlayın ve sayfa için ContentDefinition 'ı düzenleyin.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Desteklenen dillerin açık listesini ayarlama

**Buildingblocks** öğesi altında, desteklenen dillerin listesine sahip **Yerelleştirme** öğesini ekleyin. Aşağıdaki örnek, hem Ingilizce (varsayılan) hem de Ispanyolca için yerelleştirme desteğinin nasıl tanımlanacağını göstermektedir:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

### <a name="provide-language-specific-strings-and-collections"></a>Dile özgü dizeler ve koleksiyonlar sağlama

**Supportedlanguages** öğesinin kapandıktan sonra **Yerelleştirme** öğesinin içine **localizedresources** öğeleri ekleyin. Her sayfa (içerik tanımı) ve desteklemek istediğiniz herhangi bir dil için **Localizedresources** öğeleri eklersiniz. Birleşik kaydolma veya oturum açma sayfası, Ingilizce, Ispanyolca ve Fransa için kaydolma ve Multi-Factor Authentication (MFA) sayfalarını özelleştirmek için aşağıdaki **Localizedresources** öğelerini eklersiniz.

- Birleşik kaydolma veya oturum açma sayfası, Ingilizce`<LocalizedResources Id="api.signuporsignin.en">`
- Birleşik kaydolma veya oturum açma sayfası, Ispanyolca`<LocalizedResources Id="api.signuporsignin.es">`
- Birleşik kaydolma veya oturum açma sayfası, Fransa`<LocalizedResources Id="api.signuporsignin.fr">`
- Kaydolma, Ingilizce`<LocalizedResources Id="api.localaccountsignup.en">`
- Kaydolma, Ispanyolca`<LocalizedResources Id="api.localaccountsignup.es">`
- Kaydolma, Fransa`<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA, Ingilizce`<LocalizedResources Id="api.phonefactor.en">`
- MFA, Ispanyolca`<LocalizedResources Id="api.phonefactor.es">`
- MFA, Fransa`<LocalizedResources Id="api.phonefactor.fr">`

Her **Localizedresources** öğesi, birden çok LocalizedString öğesine ve **localizedcollections** öğelerine sahip gerekli **Localizedstrings** öğelerinin tümünü içerir. birden çok **localizedcollection** ög.  Aşağıdaki örnek, kayıt sayfası Ingilizce yerelleştirmesini ekler:

Not: Bu örnek `Gender` , ve `City` talep türlerine bir başvuru yapar. Bu örneği kullanmak için, bu talepleri tanımladığınızdan emin olun. Daha fazla bilgi için bkz. [Claimsschema](claimsschema.md).

```XML
<LocalizedResources Id="api.localaccountsignup.en">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="New York" Value="NY" />
      <Item Text="Paris" Value="Paris" />
      <Item Text="London" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Ispanyolca için kayıt sayfası yerelleştirilmesi.

```XML
<LocalizedResources Id="api.localaccountsignup.es">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="Nueva York" Value="NY" />
      <Item Text="París" Value="Paris" />
      <Item Text="Londres" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

### <a name="edit-the-contentdefinition-for-the-page"></a>Sayfa için ContentDefinition 'ı düzenleme

Yerelleştirilmesi istediğiniz her sayfa için, **ContentDefinition**'da aranacak dil kodlarını belirtin.

Aşağıdaki örnekte, kayıt sayfasına Ingilizce (en) ve Ispanyolca (es) özel dizeler eklenir. Her bir **Localizedresourcesreference** Için **Localizedresourcesreferenceıd** , kendi yerel ayarlarıyla aynıdır, ancak tanımlayıcı olarak herhangi bir dize kullanabilirsiniz. Her dil ve sayfa birleşimi için, daha önce oluşturduğunuz ilgili **Localizedresources** üzerine gelin.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

Aşağıdaki örnek, son XML 'i göstermektedir:

```XML
<BuildingBlocks>
  <ContentDefinitions>
    <ContentDefinition Id="api.localaccountsignup">
      <!-- Other content definitions elements... -->
      <LocalizedResourcesReferences MergeBehavior="Prepend">
         <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
         <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
      </LocalizedResourcesReferences>
    </ContentDefinition>
    <!-- More content definitions... -->
  </ContentDefinitions>

  <Localization Enabled="true">

    <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
      <!-- More supported language... -->
    </SupportedLanguages>

    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Female" Value="F" />
          <Item Text="Male" Value="M" />
          <!-- More items... -->
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="New York" Value="NY" />
          <Item Text="Paris" Value="Paris" />
          <Item Text="London" Value="London" />
        </LocalizedCollection>
        <!-- More localized collections... -->
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
       <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
        <!-- More localized strings... -->
      </LocalizedStrings>
    </LocalizedResources>

    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
       <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Femenino" Value="F" />
          <Item Text="Masculino" Value="M" />
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="Nueva York" Value="NY" />
          <Item Text="París" Value="Paris" />
          <Item Text="Londres" Value="London" />
        </LocalizedCollection>
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
    <!-- More localized resources... -->
  </Localization>
</BuildingBlocks>
```
