---
title: Koşullar ve Predicatedoğrulamaları
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C içindeki özel ilkeleri kullanarak, hatalı oluşturulmuş verilerin Azure AD B2C kiracınıza eklenmesini engelleyin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 46f04c55b40d4f1bdbbf5fd55eb648d1d3294056
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97108425"
---
# <a name="predicates-and-predicatevalidations"></a>Koşullar ve Predicatedoğrulamaları

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Koşullar** ve **predicatevalidation** öğeleri, Azure Active Directory B2C (Azure AD B2C) kiracısına yalnızca düzgün biçimlendirilmiş verilerin girildiğinden emin olmak için bir doğrulama işlemi gerçekleştirmenizi sağlar.

Aşağıdaki diyagramda öğeler arasındaki ilişki gösterilmektedir:

![Koşullar ve koşul doğrulamaları ilişkisini gösteren diyagram](./media/predicates/predicates.png)

## <a name="predicates"></a>Koşullar

**Koşul** öğesi, bir talep türünün değerini denetlemek için temel bir doğrulama tanımlar ve `true` ya da döndürür `false` . Doğrulama, belirtilen bir **Yöntem** öğesi ve yöntemiyle ilgili bir dizi **parametre** öğesi kullanılarak yapılır. Örneğin bir koşul, bir dize talep değerinin uzunluğunun, belirtilen minimum ve maksimum parametre aralığı içinde olup olmadığını veya bir dize talep değerinin bir karakter kümesi içerip içermediğini denetleyebilir. **Userhelptext** öğesi, Denetim başarısız olursa kullanıcılar için bir hata mesajı sağlar. **Userhelptext** öğesinin değeri [dil özelleştirmesi](localization.md)kullanılarak yerelleştirilebilecek.

**Doðrulama** öğesi, [buildingblocks](buildingblocks.md) öğesi Içinde **claimsschema** öğesinin hemen ardından gelmelidir.

**Koşullar** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| Koşulunda | 1: n | Koşulların listesi. |

**Koşul** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Id | Yes | Koşul için kullanılan bir tanımlayıcı. Diğer öğeler ilkede bu tanımlayıcıyı kullanabilir. |
| Yöntem | Yes | Doğrulama için kullanılacak yöntem türü. Olası değerler: [ılengthrange](#islengthrange), [matchesregex](#matchesregex), [ıncludescharacters](#includescharacters)veya [ıdadterange](#isdaterange).  |
| HelpText | No | Denetim başarısız olursa kullanıcılara yönelik bir hata iletisi. Bu dize, [dil özelleştirmesi](localization.md) kullanılarak yerelleştirilebilecek |

**Koşul** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 | Kullanım dışı Denetim başarısız olursa kullanıcılara yönelik bir hata iletisi. |
| Parametreler | 1:1 | Dize doğrulamanın Yöntem türü için parametreler. |

**Parameters** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| Parametre | 1: n | Dize doğrulamanın Yöntem türü için parametreler. |

**Parameter** öğesi aşağıdaki öznitelikleri içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| Id | 1:1 | Parametrenin tanımlayıcısı. |

### <a name="predicate-methods"></a>Koşul yöntemleri

#### <a name="islengthrange"></a>Ilengthrange

Ilengthrange yöntemi, bir dize talep değerinin uzunluğunun, belirtilen minimum ve maksimum parametre aralığı içinde olup olmadığını denetler. Koşul öğesi aşağıdaki parametreleri destekler:

| Parametre | Gerekli | Açıklama |
| ------- | ----------- | ----------- |
| Maksimum | Yes | Girilebilecek en fazla karakter sayısı. |
| Minimum | Yes | Girilmesi gereken en az karakter sayısı. |


Aşağıdaki örnek, parametresine sahip bir ılengthrange yöntemi gösterir `Minimum` ve `Maximum` dizenin uzunluk aralığını belirtir:

```xml
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="matchesregex"></a>MatchesRegex

MatchesRegex yöntemi bir dize talep değerinin bir normal ifadeyle eşleşip eşleşmediğini denetler. Koşul öğesi aşağıdaki parametreleri destekler:

| Parametre | Gerekli | Açıklama |
| ------- | ----------- | ----------- |
| Cevap içerisinde RegularExpression | Yes | Eşleştirilecek normal ifade deseninin. |

Aşağıdaki örnek, bir `MatchesRegex` normal ifadeyi belirten parametresine sahip bir yöntemi gösterir `RegularExpression` :

```xml
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="includescharacters"></a>Includescharacters

Includescharacters yöntemi, bir dize talep değerinin bir karakter kümesi içerip içermediğini denetler. Koşul öğesi aşağıdaki parametreleri destekler:

| Parametre | Gerekli | Açıklama |
| ------- | ----------- | ----------- |
| CharacterSet | Yes | Girilebilecek karakter kümesi. Örneğin, küçük harfli karakterler  `a-z` , büyük harfler `A-Z` , rakamlar `0-9` veya gibi semboller listesi `@#$%^&amp;*\-_+=[]{}|\\:',?/~"();!` . |

Aşağıdaki örnek, `IncludesCharacters` parametresini `CharacterSet` belirten ve karakter kümesini belirten bir yöntemi gösterir:

```xml
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="isdaterange"></a>Isdavterange

Isdadterange yöntemi, bir tarih talep değerinin belirtilen en düşük ve en fazla parametre aralığı arasında olup olmadığını denetler. Koşul öğesi aşağıdaki parametreleri destekler:

| Parametre | Gerekli | Açıklama |
| ------- | ----------- | ----------- |
| Maksimum | Yes | Girilebilecek en büyük olası tarih. Tarih ve `yyyy-mm-dd` kural biçimi `Today` . |
| Minimum | Yes | Girilebilecek en küçük olası tarih. Tarih ve `yyyy-mm-dd` kural biçimi `Today` .|

Aşağıdaki örnek, `IsDateRange` `Minimum` `Maximum` ve biçimindeki tarih aralığını belirten ve parametrelerine sahip bir yöntemi gösterir `yyyy-mm-dd` `Today` .

```xml
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>Predicatedoğrulamaları

Koşullar bir talep türüne karşı denetlenecek doğrulamayı tanımlalarken, **Predicatedoğrulamaları** , bir talep türüne uygulanabilen bir kullanıcı girişi doğrulaması oluşturmak için bir koşul kümesi grubu belirler. Her **Predicatevalidation** öğesi, bir **koşula** Işaret eden bir tahmine **Atereference** öğesi kümesi içeren bir **predicategroup** öğeleri kümesi içerir. Doğrulamanın başarılı olması için, talebin değeri, **Predicategroup** 'un tüm koşullarından herhangi bir koşulun tüm **Testlertereference** öğeleri kümesiyle tüm testlerini iletmelidir.

**Predicatedoğrulamaları** öğesi, [buildingblocks](buildingblocks.md) öğesi içindeki **koşulların** öğesinin hemen ardından gelmelidir.

```xml
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

**Predicatedoğrulamaları** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| PredicateValidation | 1: n | Koşul doğrulama listesi. |

**Predicatevalidation** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Id | Yes | Koşul doğrulama için kullanılan bir tanımlayıcı. **ClaimType** öğesi bu tanımlayıcıyı ilkede kullanabilir. |

**Predicatevalidation** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| PredicateGroups | 1: n | Koşul gruplarının listesi. |

**Predicategroups** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| PredicateGroup | 1: n | Koşulların listesi. |

**Predicategroup** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Id | Yes | Koşul grubu için kullanılan bir tanımlayıcı.  |

**Predicategroup** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 |  Kullanıcıların hangi değeri yazdıklarından haberdar olmaları için yararlı olabilecek koşulun açıklaması. |
| Predicatereferde | 1: n | Koşul başvurularının listesi. |

**Predicatereferde** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| MatchAtLeast | No | Değerin en azından, girişin kabul edileceği birçok koşul tanımına uyması gerektiğini belirtir. Belirtilmemişse, değer tüm koşul tanımlarına uymalıdır. |

**Predicatereferde** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Description |
| ------- | ----------- | ----------- |
| PredicateReference | 1: n | Bir koşula başvuru. |

**Predicatereference** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Id | Yes | Koşul doğrulama için kullanılan bir tanımlayıcı.  |


## <a name="configure-password-complexity"></a>Parola karmaşıklığını yapılandırma

**Koşullar** ve **Predicatevalidationsinput** sayesinde, bir kullanıcı tarafından bir hesap oluştururken sunulan parolaların karmaşıklık gereksinimlerini kontrol edebilirsiniz. Varsayılan olarak, Azure AD B2C güçlü parolalar kullanır. Azure AD B2C Ayrıca, müşterilerin kullanabileceği parolaların karmaşıklığını denetlemek için yapılandırma seçeneklerini destekler. Bu koşul öğelerini kullanarak parola karmaşıklığı tanımlayabilirsiniz:

-  Yöntemi kullanarak IsLengthBetween8And64 `IsLengthRange` , parolanın 8 ila 64 karakter arasında olması gerektiğini doğrular.
- Yöntemi kullanılarak **küçük harfli** `IncludesCharacters` , parolanın küçük harfli bir harf içerdiğini doğrular.
- **Büyük harfli** `IncludesCharacters` yöntemi kullanarak parolanın büyük harfle içerdiğini doğrular.
-  `IncludesCharacters` Yöntemi kullanarak, parolanın bir sayı içerdiğini doğrular.
-  Yöntemi kullanılarak sembol `IncludesCharacters` , parolanın çeşitli sembol karakterlerinden birini içerdiğini doğrular.
-  Yöntemini kullanarak sabitleme `MatchesRegex` , parolanın yalnızca sayı içerdiğini doğrular.
- **Allowedadadcharacters** yöntemi kullanılarak `MatchesRegex` , parolanın yalnızca geçersiz karakterinin sağlandığını doğrular.
- Yöntemi kullanarak **Disallowedwhitespace** `MatchesRegex` , parolanın boşluk karakteriyle başlamayacağını veya bitmediğini doğrular.

```xml
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters" HelpText="a digit">
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters" HelpText="a symbol">
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex" HelpText="An invalid character was provided.">
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex" HelpText="The password must not begin or end with a whitespace character.">
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Temel doğrulamaları tanımladıktan sonra, bunları birlikte birleştirebilir ve ilkenizde kullanabileceğiniz bir dizi parola ilkesi oluşturabilirsiniz:

- **Simplepassword** , DisallowedWhitespace, Allowedadadcharacters ve IsLengthBetween8And64 doğrular
- **Strongpassword** , DisallowedWhitespace, Allowebaadcharacters, IsLengthBetween8And64 doğrular. Son grup `CharacterClasses` 3 ' e ayarlanmış ek bir koşullar kümesi çalıştırır `MatchAtLeast` . Kullanıcı parolası 8 ila 16 karakter arasında olmalıdır ve şu karakterlerden üçünü içermelidir: küçük harf, büyük harf, sayı veya simge.
- **Custompassword** yalnızca DisallowedWhitespace, Alloweeradcharacters ' i doğrular. Bu nedenle, karakterler geçerli olduğu sürece Kullanıcı herhangi bir uzunlukta bir parola sağlayabilir.

```xml
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Talep türü ' nde, **Predicatevalidationreference** öğesini ekleyin ve tanımlayıcıyı Simplepassword, strongpassword veya CustomPassword gibi koşul Doğrulamalardan biri olarak belirtin.

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

Aşağıdaki, Azure AD B2C hata iletisini görüntülediğinde öğelerin nasıl düzenlendiğini gösterir:

![Koşul ve PredicateGroup parola karmaşıklığı örnek diyagramı](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Bir tarih aralığı yapılandırma

**Doðrulama** ve **predicatedoğrulamaları** öğeleriyle, kullanarak **userınputtype** 'ın minimum ve maksimum tarih değerlerini kontrol edebilirsiniz `DateTimeDropdown` . Bunu yapmak için, yöntemiyle bir **koşul** oluşturun `IsDateRange` ve en düşük ve en yüksek parametreleri sağlayın.

```xml
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Koşul başvurusu ile bir **Predicatevalidation** ekleyin `DateRange` .

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Talep türünde, **Predicatevalidationreference** öğesini ekleyin ve tanımlayıcıyı olarak belirtin `CustomDateRange` .

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```

## <a name="next-steps"></a>Sonraki adımlar

- Azure Active Directory B2C koşul doğrulamaları kullanarak [, özel ilkeler kullanarak parola karmaşıklığını yapılandırma](password-complexity.md) hakkında bilgi edinin.