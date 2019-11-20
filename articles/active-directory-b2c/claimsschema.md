---
title: ClaimsSchema-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C bir özel ilkenin ClaimsSchema öğesini belirtin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1a26d6228fd2d0383f22d4f286cc84e263facfe6
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999097"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Claimsschema** öğesi, ilkenin bir parçası olarak başvurulabilen talep türlerini tanımlar. Talep şeması, taleplerinizi bildirdiğiniz yerdir. Bir talep adı, soyadı, görünen ad, telefon numarası ve daha fazlası olabilir. ClaimsSchema öğesi **ClaimType** öğelerinin listesini içerir. **ClaimType** öğesi, talep adı olan **ID** özniteliğini içerir.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>ClaimType

**ClaimType** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Id | Evet | Talep türü için kullanılan bir tanımlayıcı. Diğer öğeler ilkede bu tanımlayıcıyı kullanabilir. |

**ClaimType** öğesi aşağıdaki öğeleri içerir:

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| DisplayName | 0:1 | Çeşitli ekranlarda kullanıcılara görüntülenen başlık. Değer [yerelleştirilmiş](localization.md)olabilir. |
| DataType | 0:1 | Talebin türü. Boolean, Date, dateTime, int, Long, String, stringCollection, Alternativesecurityıdcollection veri türleri kullanılabilir. |
| DefaultPartnerClaimTypes | 0:1 | Belirtilen bir protokol için kullanılacak iş ortağı varsayılan talep türleri. Değerin **ınputclaim** veya **outputclaim** öğelerinde belirtilen **partnerclaimtype** 'da üzerine yazılabilir. Bir protokol için varsayılan adı belirtmek üzere bu öğeyi kullanın.  |
| Maske | 0:1 | Talep görüntülenirken uygulanabilecek bir maske karakterleri için isteğe bağlı bir dize. Örneğin, 324-232-4343 telefon numarası XXX-XXX-4343 olarak maskelenebilir. |
| UserHelpText | 0:1 | Kullanıcıların amacını anlaması için yararlı olabilecek talep türünün açıklaması. Değer [yerelleştirilmiş](localization.md)olabilir. |
| Userınputtype | 0:1 | Talep türü için talep verilerini el ile girerken Kullanıcı tarafından kullanılabilir olması gereken giriş denetiminin türü. Bu sayfada daha sonra tanımlanan Kullanıcı giriş türlerine bakın. |
| Kısıtlama | 0:1 | Bu talep için normal ifade (Regex) veya kabul edilebilir değerlerin bir listesi gibi değer kısıtlamaları. Değer [yerelleştirilmiş](localization.md)olabilir. |
PredicateValidationReference| 0:1 | Bir **Predicatevalidationsinput** öğesine başvuru. **Predicatevalidationreference** öğeleri yalnızca düzgün biçimlendirilmiş verilerin girildiğinden emin olmak için bir doğrulama işlemi gerçekleştirmenizi sağlar. Daha fazla bilgi için bkz. [koşullar](predicates.md). |

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

**Defaultpartnerclaimtypes** aşağıdaki öğeyi içerebilir:

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| Protocol | 0: n | Varsayılan iş ortağı talep türü adı olan protokollerin listesi. |

**Protokol** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Name | Evet | Azure AD B2C tarafından desteklenen geçerli bir protokol adı. Olası değerler şunlardır:  OAuth1, OAuth2, SAML2, Openıdconnect. |
| PartnerClaimType | Evet | Kullanılacak talep türü adı. |

Aşağıdaki örnekte, kimlik deneyimi çerçevesi bir SAML2 Identity provider veya bağlı olan taraf uygulamasıyla `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`etkileşime geçtiğinde, openıdconnect ve OAuth2 ile, `family_name` talebin eşlendiği .

```XML
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

Sonuç olarak, Azure AD B2C tarafından verilen JWT belirteci, ClaimType adının `family_name` **Soyadı**yerine yayar.

```JSON
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Maske

**Mask** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| `Type` | Evet | Talep maskesinin türü. Olası değerler: `Simple` veya `Regex`. Değer `Simple` , bir dize talebinin önde gelen kısmına bir basit metin maskesinin uygulanacağını gösterir. Değer `Regex` , normal bir ifadenin dize talebine tam olarak uygulandığını belirtir.  `Regex` Değer belirtilmişse, kullanılacak normal ifadeyle isteğe bağlı bir özniteliğin de tanımlanması gerekir. |
| `Regex` | Hayır | **`Type`** Olarak`Regex`ayarlanırsa, kullanılacak normal ifadeyi belirtin.

Aşağıdaki örnek, `Simple` maskeyle bir **PhoneNumber** talebi yapılandırır:

```XML
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Kimlik deneyimi çerçevesi, ilk altı basamağı gizlerken telefon numarasını işler:

![Bir tarayıcıda gösterilen telefon numarası talebi, XS tarafından maskelenen ilk altı basamakla](./media/claimsschema/mask.png)

Aşağıdaki örnek, `Regex` maskeyle bir **alternateemail** talebi yapılandırır:

```XML
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Kimlik deneyimi çerçevesi yalnızca e-posta adresinin ilk harfini ve e-posta etki alanı adını işler:

![Yıldız işaretiyle maskelenmiş karakterlerle tarayıcıda gösterilen e-posta talebi](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Kısıtlama

**Kısıtlama** öğesi aşağıdaki özniteliği içerebilir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| MergeBehavior | Hayır | Aynı tanımlayıcıya sahip bir üst ilkede bir ClaimType ile numaralandırma değerlerini birleştirmek için kullanılan yöntem. Temel ilkede belirtilen bir talebin üzerine yazdığınızda bu özniteliği kullanın. Olası değerler: `Append`, `Prepend`, veya `ReplaceAll`. `Append` Değer, üst ilkede belirtilen koleksiyonun sonuna eklenmesi gereken verilerin bir koleksiyonudur. `Prepend` Değer, üst ilkede belirtilen koleksiyondan önce eklenmesi gereken verilerin bir koleksiyonudur. `ReplaceAll` Değer, üst ilkede yok sayılacak belirtilen verilerin bir koleksiyonudur. |

**Kısıtlama** öğesi aşağıdaki öğeleri içerir:

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | ----------- |
| Listelenen | 1: n | Kullanıcının bir talep için seçmesini sağlamak üzere Kullanıcı arabirimindeki kullanılabilir seçenekler (örneğin, açılan menüdeki bir değer). |
| Desen | 1:1 | Kullanılacak normal ifade. |

### <a name="enumeration"></a>Listelenen

**Enumeration** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Text | Evet | Bu seçenek için Kullanıcı arabirimindeki kullanıcıya gösterilen görüntüleme dizesi. |
|Value | Evet | Bu seçeneği belirleyerek ilişkili talep değeri. |
| SelectByDefault | Hayır | Bu seçeneğin Kullanıcı arabiriminde varsayılan olarak seçilmesinin gerekip gerekmediğini gösterir. Olası değerler: TRUE veya False. |

Aşağıdaki örnek, varsayılan değer olarak `New York`ayarlanmış bir şehir açılan listesi talebi yapılandırır:

```XML
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

Yeni York olarak ayarlanan varsayılan değere sahip aşağı açılan şehir listesi:

![Tarayıcıda işlenmiş ve varsayılan değeri gösteren açılan denetim denetimi](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Desen

**Model** öğesi aşağıdaki öznitelikleri içerebilir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Cevap içerisinde RegularExpression | Evet | Bu tür taleplerin geçerli olması için aynı olması gereken normal ifade. |
| HelpText | Hayır | Bu talep için model veya normal ifade. |

Aşağıdaki örnek, normal ifade giriş doğrulaması ve yardım metni ile bir **e-posta** talebi yapılandırır:

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
    </Restriction>
 </ClaimType>
```

Kimlik deneyimi çerçevesi, e-posta adresi talebini e-posta biçiminde giriş doğrulamasına göre işler:

![Regex kısıtlaması tarafından tetiklenen hata iletisini gösteren TextBox](./media/claimsschema/pattern.png)

## <a name="userinputtype"></a>Userınputtype

Azure AD B2C, talep türü için talep verilerini el ile girerken kullanılabilecek bir TextBox, parola ve açılan liste gibi çeşitli kullanıcı giriş türlerini destekler. Kullanıcıdan [kendi kendine onaylanan bir teknik profil](self-asserted-technical-profile.md)kullanarak bilgi topladığınızda **userınputtype** belirtmeniz gerekir.

### <a name="textbox"></a>TextBox

**TextBox** Kullanıcı giriş türü, tek satırlık bir metin kutusu sağlamak için kullanılır.

![Talep türünde belirtilen özellikleri gösteren metin kutusu](./media/claimsschema/textbox.png)

```XML
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

### <a name="emailbox"></a>EmailBox

**Emailbox** Kullanıcı giriş türü, temel bir e-posta girişi alanı sağlamak için kullanılır.

![Talep türünde belirtilen özellikleri gösteren EmailBox](./media/claimsschema/emailbox.png)

```XML
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

### <a name="password"></a>istemcisiyle yönetilen bir cihaz için)

**Parola** Kullanıcı giriş türü, Kullanıcı tarafından girilen bir parolayı kaydetmek için kullanılır.

![Talep türünü parolayla kullanma](./media/claimsschema/password.png)

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

### <a name="datetimedropdown"></a>DateTimeDropdown

**Datetimeaçýlan** Kullanıcı giriş türü, bir gün, ay ve yıl seçmek üzere bir dizi açılır menü sağlamak için kullanılır. En düşük ve en yüksek tarih değerlerini denetlemek için, koşullar ve Predicatedoğrulamaları öğelerini kullanabilirsiniz. Daha fazla bilgi için, [koşullar ve Predicatedoðrulamaların](predicates.md) **Tarih aralığını yapılandırma** bölümüne bakın.

![Datetimedropdown ile talep türü kullanma](./media/claimsschema/datetimedropdown.png)

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

### <a name="radiosingleselect"></a>RadioSingleSelect

**RadioSingleSelect** Kullanıcı girişi türü, kullanıcının bir seçenek seçmesini sağlayan radyo düğmelerinin bir koleksiyonunu sağlamak için kullanılır.

![Radiodsingleselect ile talep türü kullanma](./media/claimsschema/radiosingleselect.png)

```XML
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

### <a name="dropdownsingleselect"></a>DropdownSingleSelect

Kullanıcı giriş **türü, kullanıcının** bir seçenek seçmesini sağlayan bir açılan kutu sağlamak için kullanılır.

![Talep türünü dropdownsingleselect ile kullanma](./media/claimsschema/dropdownsingleselect.png)

```XML
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

**Checkboxmultiselect** Kullanıcı giriş türü, kullanıcının birden çok seçenek seçmesine izin veren bir onay kutusu koleksiyonu sağlamak için kullanılır.

![Checkboxmultiselect ile talep türü kullanma](./media/claimsschema/checkboxmultiselect.png)

```XML
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

### <a name="readonly"></a>Özelliğinin

**Salt okunur** Kullanıcı giriş türü, talebi ve değeri göstermek için salt okunur bir alan sağlamak üzere kullanılır.

![Salt okunur ile talep türü kullanma](./media/claimsschema/readonly.png)

```XML
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


### <a name="paragraph"></a>Paragraf

**Paragraf** Kullanıcı giriş türü, yalnızca bir paragraf etiketinde metin gösteren bir alan sağlamak için kullanılır. Örneğin, &lt;p&gt;metni&lt;/p.&gt;

![Paragrafla birlikte talep türü kullanma](./media/claimsschema/paragraph.png)

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```

Bir **responsemsg** talebi, ya `CreateStringClaim` da talep dönüştürmesi içindeki **sabit listesi** değerlerinden birini `GetMappedValueFromLocalizedCollection` göstermek için kullanın. Daha fazla bilgi için bkz. [dize talep dönüştürmeleri](string-transformations.md)
