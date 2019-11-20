---
title: Koşullar ve Predicatedoðrulamadoğrulamaları-Azure Active Directory B2C | Microsoft Docs
description: Sosyal hesap, Azure Active Directory B2C Identity Experience Framework şeması için bir dönüşüm örnekleri talep ediyor.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a1f08589ae28b3e19d2a4fdb3e3862e127a810cc
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73099714"
---
# <a name="predicates-and-predicatevalidations"></a>Koşullar ve Predicatedoğrulamaları

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Koşullar** ve **predicatevalidation** öğeleri, Azure Active Directory B2C (Azure AD B2C) kiracısına yalnızca düzgün biçimlendirilmiş verilerin girildiğinden emin olmak için bir doğrulama işlemi gerçekleştirmenizi sağlar.

Aşağıdaki diyagramda öğeler arasındaki ilişki gösterilmektedir:

![Koşullar ve koşul doğrulamaları ilişkisini gösteren diyagram](./media/predicates/predicates.png)

## <a name="predicates"></a>Koşullar

**Koşul** öğesi, bir talep türünün değerini denetlemek için temel bir doğrulama tanımlar ve `true` veya `false`döndürür. Doğrulama, belirtilen bir **Yöntem** öğesi ve yöntemiyle ilgili bir dizi **parametre** öğesi kullanılarak yapılır. Örneğin bir koşul, bir dize talep değerinin uzunluğunun, belirtilen minimum ve maksimum parametre aralığı içinde olup olmadığını veya bir dize talep değerinin bir karakter kümesi içerip içermediğini denetleyebilir. **Userhelptext** öğesi, Denetim başarısız olursa kullanıcılar için bir hata mesajı sağlar. **Userhelptext** öğesinin değeri [dil özelleştirmesi](localization.md)kullanılarak yerelleştirilebilecek.

**Doðrulama** öğesi, [buildingblocks](buildingblocks.md) öğesi Içinde **claimsschema** öğesinin hemen ardından gelmelidir.

**Koşullar** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Açıklama |
| ------- | ----------- | ----------- |
| Koşulunda | 1: n | Koşulların listesi. |

**Koşul** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gereklidir | Açıklama |
| --------- | -------- | ----------- |
| Kimlik | Yes | Koşul için kullanılan bir tanımlayıcı. Diğer öğeler ilkede bu tanımlayıcıyı kullanabilir. |
| Yöntem | Yes | Doğrulama için kullanılacak yöntem türü. Olası değerler: **ılengthrange**, **matchesregex**, **ıncludescharacters**veya **ıdadterange**. **Ilengthrange** değeri, bir dize talep değerinin uzunluğunun, belirtilen minimum ve maksimum parametre aralığı içinde olup olmadığını denetler. **Matchesregex** değeri, bir dize talep değerinin bir normal ifadeyle eşleşip eşleşmediğini denetler. **Includescharacters** değeri, bir dize talep değerinin bir karakter kümesi içerip içermediğini denetler. **Isdadterange** değeri, bir tarih talep değerinin belirtilen en düşük ve en fazla parametre aralığı arasında olup olmadığını denetler. |

**Koşul** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Açıklama |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 | Denetim başarısız olursa kullanıcılara yönelik bir hata iletisi. Bu dize, [dil özelleştirmesi](localization.md) kullanılarak yerelleştirilebilecek |
| Parametreler | 1:1 | Dize doğrulamanın Yöntem türü için parametreler. |

**Parameters** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Açıklama |
| ------- | ----------- | ----------- |
| Parametre | 1: n | Dize doğrulamanın Yöntem türü için parametreler. |

**Parameter** öğesi aşağıdaki öznitelikleri içerir:

| Öğe | Öğeleri | Açıklama |
| ------- | ----------- | ----------- |
| Kimlik | 1:1 | Parametrenin tanımlayıcısı. |

Aşağıdaki örnek, `Minimum` ve `Maximum` dizenin uzunluk aralığını belirten `IsLengthRange` bir yöntemi gösterir:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

Aşağıdaki örnek, bir normal ifadeyi belirten `RegularExpression` parametresi ile bir `MatchesRegex` yöntemi gösterir:

```XML
<Predicate Id="PIN" Method="MatchesRegex">
  <UserHelpText>The password must be numbers only.</UserHelpText>
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

Aşağıdaki örnek, karakter kümesini belirten parametre `CharacterSet` sahip `IncludesCharacters` bir yöntemi gösterir:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters">
  <UserHelpText>a lowercase letter</UserHelpText>
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

Aşağıdaki örnek, `yyyy-MM-dd` ve `Today`biçimindeki tarih aralığını belirten `Minimum` ve `Maximum` parametrelere sahip bir `IsDateRange` yöntemi gösterir.

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>Predicatedoğrulamaları

Koşullar bir talep türüne karşı denetlenecek doğrulamayı tanımlalarken, **Predicatedoğrulamaları** , bir talep türüne uygulanabilen bir kullanıcı girişi doğrulaması oluşturmak için bir koşul kümesi grubu belirler. Her **Predicatevalidation** öğesi, bir **koşula**Işaret eden bir tahmine **Atereference** öğesi kümesi içeren bir **predicategroup** öğeleri kümesi içerir. Doğrulamanın başarılı olması için, talebin değeri, **Predicategroup** 'un tüm koşullarından herhangi bir koşulun tüm **Testlertereference** öğeleri kümesiyle tüm testlerini iletmelidir.

**Predicatedoğrulamaları** öğesi, [buildingblocks](buildingblocks.md) öğesi içindeki **koşulların** öğesinin hemen ardından gelmelidir.

```XML
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

| Öğe | Öğeleri | Açıklama |
| ------- | ----------- | ----------- |
| PredicateValidation | 1: n | Koşul doğrulama listesi. |

**Predicatevalidation** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gereklidir | Açıklama |
| --------- | -------- | ----------- |
| Kimlik | Yes | Koşul doğrulama için kullanılan bir tanımlayıcı. **ClaimType** öğesi bu tanımlayıcıyı ilkede kullanabilir. |

**Predicatevalidation** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Açıklama |
| ------- | ----------- | ----------- |
| PredicateGroups | 1: n | Koşul gruplarının listesi. |

**Predicategroups** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Açıklama |
| ------- | ----------- | ----------- |
| PredicateGroup | 1: n | Koşulların listesi. |

**Predicategroup** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gereklidir | Açıklama |
| --------- | -------- | ----------- |
| Kimlik | Yes | Koşul grubu için kullanılan bir tanımlayıcı.  |

**Predicategroup** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Açıklama |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 |  Kullanıcıların hangi değeri yazdıklarından haberdar olmaları için yararlı olabilecek koşulun açıklaması. |
| Predicatereferde | 1: n | Koşul başvurularının listesi. |

**Predicatereferde** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gereklidir | Açıklama |
| --------- | -------- | ----------- |
| MatchAtLeast | Hayır | Değerin en azından, girişin kabul edileceği birçok koşul tanımına uyması gerektiğini belirtir. |

**Predicatereferde** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Açıklama |
| ------- | ----------- | ----------- |
| PredicateReference | 1: n | Bir koşula başvuru. |

**Predicatereference** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gereklidir | Açıklama |
| --------- | -------- | ----------- |
| Kimlik | Yes | Koşul doğrulama için kullanılan bir tanımlayıcı.  |


## <a name="configure-password-complexity"></a>Parola karmaşıklığını yapılandırma

**Koşullar** ve **Predicatevalidationsinput** sayesinde, bir kullanıcı tarafından bir hesap oluştururken sunulan parolaların karmaşıklık gereksinimlerini kontrol edebilirsiniz. Varsayılan olarak, Azure AD B2C güçlü parolalar kullanır. Azure AD B2C Ayrıca, müşterilerin kullanabileceği parolaların karmaşıklığını denetlemek için yapılandırma seçeneklerini destekler. Bu koşul öğelerini kullanarak parola karmaşıklığı tanımlayabilirsiniz:

- `IsLengthRange` yöntemi kullanılarak **IsLengthBetween8And64** , parolanın 8 ila 64 karakter arasında olması gerektiğini doğrular.
- `IncludesCharacters` yöntemi kullanılarak **küçük harfli** , parolanın küçük harfli bir harf içerdiğini doğrular.
- **Büyük harfli** `IncludesCharacters` yöntemi kullanılarak parolanın büyük harfli bir harf içerdiğini doğrular.
- `IncludesCharacters` yöntemi kullanan **sayı** parolanın bir basamak içerdiğini doğrular.
- `IncludesCharacters` yöntemi kullanılarak **sembol** , parolanın çeşitli sembol karakterlerinden birini içerdiğini doğrular.
- `MatchesRegex` yöntemi kullanılarak **PIN** , parolanın yalnızca sayı içerdiğini doğrular.
- `MatchesRegex` yöntemi kullanılarak **Allowebaadcharacters** , parolanın yalnızca geçersiz karakterinin sağlandığını doğrular.
- `MatchesRegex` yöntemi kullanılarak **Disallowedwhitespace** , parolanın boşluk karakteriyle başlamayacağını veya bitmediğini doğrular.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
    <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters">
    <UserHelpText>a lowercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters">
    <UserHelpText>an uppercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters">
    <UserHelpText>a digit</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters">
    <UserHelpText>a symbol</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex">
    <UserHelpText>The password must be numbers only.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex">
    <UserHelpText>An invalid character was provided.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex">
    <UserHelpText>The password must not begin or end with a whitespace character.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Temel doğrulamaları tanımladıktan sonra, bunları birlikte birleştirebilir ve ilkenizde kullanabileceğiniz bir dizi parola ilkesi oluşturabilirsiniz:

- **Simplepassword** , DisallowedWhitespace, Allowedadadcharacters ve IsLengthBetween8And64 doğrular
- **Strongpassword** , DisallowedWhitespace, Allowebaadcharacters, IsLengthBetween8And64 doğrular. Son grup `CharacterClasses`, `MatchAtLeast` 3 olarak ayarlanan ek bir koşul kümesi çalıştırır. Kullanıcı parolası 8 ila 16 karakter arasında olmalıdır ve şu karakterlerden üçünü içermelidir: küçük harf, büyük harf, sayı veya simge.
- **Custompassword** yalnızca DisallowedWhitespace, Alloweeradcharacters ' i doğrular. Bu nedenle, karakterler geçerli olduğu sürece Kullanıcı herhangi bir uzunlukta bir parola sağlayabilir.

```XML
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

```XML
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

**Koşullar** ve **predicatedoğrulamaları** öğeleriyle, `DateTimeDropdown`kullanarak **userınputtype** ' ın en düşük ve en yüksek tarih değerlerini kontrol edebilirsiniz. Bunu yapmak için, `IsDateRange` yöntemiyle bir **koşul** oluşturun ve en düşük ve en yüksek parametreleri sağlayın.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange">
    <UserHelpText>The date must be between 01-01-1980 and today.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

`DateRange` koşulu için bir başvuruya sahip bir **Predicatevalidation** ekleyin.

```XML
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

Talep türünde, **Predicatevalidationreference** öğesini ekleyin ve tanımlayıcıyı `CustomDateRange`olarak belirtin.

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```
