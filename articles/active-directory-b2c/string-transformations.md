---
title: Azure Active Directory B2C | kimlik deneyimi çerçevesi şemasına yönelik dize talep dönüştürme örnekleri | Microsoft Docs
description: Azure Active Directory B2C Identity Experience Framework şeması için dize talep dönüştürme örnekleri.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 77f0b196777ae0f2ff0b870eac0a01b11854190b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936802"
---
# <a name="string-claims-transformations"></a>Dize talep dönüştürmeleri

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Bu makalede, Azure Active Directory B2C (Azure AD B2C) öğesinde kimlik deneyimi çerçevesi şemasının dize talep dönüştürmelerinin kullanılmasına yönelik örnekler sağlanmaktadır. Daha fazla bilgi için bkz. [Claimstransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>Assertstringclaimsareeşittir

İki talebi karşılaştırın ve belirtilen karşılaştırma inputClaim1, inputClaim2 ve stringComparison öğesine göre eşit değilse bir özel durum oluşturun.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | inputClaim1 | dize | Karşılaştırılacak ilk talep türü. |
| Inputclaim | inputClaim2 | dize | Karşılaştırılan ikinci talebin türü. |
| InputParameter | stringComparison | dize | dize karşılaştırma, değerlerden biri: Ordinal, OrdinalIgnoreCase. |

**Assertstringclaimsareeþitclaim** dönüşümü, her zaman [kendi kendine onaylanan bir teknik profille](self-asserted-technical-profile.md)çağrılan bir [doğrulama teknik profilinden](validation-technical-profile.md) yürütülür. **Usermessageifclaimstransformationstringsarenotequal** kendiliğinden onaylanan teknik profil meta verileri, kullanıcıya sunulan hata iletisini denetler.

![Assertstringclaimsareeşittir yürütme](./media/string-transformations/assert-execution.png)

Bu talep dönüşümünü, iki ClaimTypes değerinin aynı değere sahip olduğundan emin olmak için kullanabilirsiniz. Aksi takdirde, bir hata iletisi oluşturulur. Aşağıdaki örnek, **Strongauthenticationemapostaadresi** ClaimType 'ın **e-posta** ClaimType değerine eşit olduğunu denetler. Aksi takdirde bir hata iletisi oluşturulur.

```XML
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


**Oturum açma etkileşimli** doğrulama teknik profili **Assertemaılandstrongauthenticationemaıladdressareeþitclaim** dönüşümünü çağırır.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Otomatik olarak onaylanan teknik profil, doğrulama **oturum açma-etkileşimsiz** teknik profilini çağırır.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
    - Giriş parametreleri:
  - **StringComparison**: OrdinalIgnoreCase
- Sonuç: hata oluştu

## <a name="changecase"></a>Değişiklik durumu

, İşlecine bağlı olarak, belirtilen talebin durumunu küçük veya büyük harf olarak değiştirir.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | inputClaim1 | dize | Değiştirilen ClaimType. |
| InputParameter | toCase | dize | Şu değerlerden biri: `LOWER` veya `UPPER`. |
| outputClaim | outputClaim | dize | Bu talep dönüştürmesinin ardından üretilen ClaimType çağırılır. |

Herhangi bir dize ClaimType 'ı küçük veya büyük harfle değiştirmek için bu talep dönüşümünü kullanın.

```XML
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
  - **e-posta**: SomeOne@contoso.com
- Giriş parametreleri:
    - **Tocase**: daha düşük
- Çıkış talepleri:
  - **e-posta**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

İlkede belirtilen giriş parametresinden bir dize talebi oluşturur.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | dize | Ayarlanacak dize |
| outputClaim | createdClaim | dize | Bu talep dönüştürmesinin ardından üretilen ClaimType, giriş parametresinde belirtilen değerle çağırılır. |

Bir dize ClaimType değeri ayarlamak için bu talep dönüşümünü kullanın.

```XML
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş parametresi:
    - **değer**: contoso hizmet koşulları...
- Çıkış talepleri:
    - **Createdclaim**: TOS ClaimType "contoso hizmet koşulları..." içerir deeri.

## <a name="compareclaims"></a>CompareClaims

Tek bir dize talebinin diğerine eşit olup olmadığını belirleme. Sonuç, `true` veya `false` değerli yeni bir Boole ClaimType değeridir.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | inputClaim1 | dize | Karşılaştırılacak ilk talep türü. |
| Inputclaim | inputClaim2 | dize | Karşılaştırılacak ikinci talep türü. |
| InputParameter | operator | dize | Olası değerler: `EQUAL` veya `NOT EQUAL`. |
| InputParameter | ignoreCase | Boole değeri | Bu karşılaştırmanın karşılaştırılan dizelerin durumunu yoksayıp saymayacağını belirtir. |
| outputClaim | outputClaim | Boole değeri | Bu talep dönüştürmesinin ardından üretilen ClaimType çağırılır. |

Bir talebin başka bir talebe eşit olup olmadığını denetlemek için bu talep dönüşümünü kullanın. Örneğin, aşağıdaki talep dönüştürmesi, **e-posta** talebinin değerinin **doğrulanmış. email** talebine eşit olup olmadığını denetler.

```XML
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Giriş parametreleri:
    - **işleç**: eşit değil
    - **IgnoreCase**: true
- Çıkış talepleri:
    - **Outputclaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Bir talep değerinin giriş parametresi değerine eşit olup olmadığını belirler.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | inputClaim1 | dize | Karşılaştırılan talep türü. |
| InputParameter | operator | dize | Olası değerler: `EQUAL` veya `NOT EQUAL`. |
| InputParameter | compareTo | dize | dize karşılaştırma, değerlerden biri: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | Boole değeri | Bu karşılaştırmanın karşılaştırılan dizelerin durumunu yoksayıp saymayacağını belirtir. |
| outputClaim | outputClaim | Boole değeri | Bu talep dönüştürmesinin ardından üretilen ClaimType çağırılır. |

Bu talep dönüşümünü, bir talebin belirttiğiniz değere eşit olup olmadığını denetlemek için kullanabilirsiniz. Örneğin, aşağıdaki talep dönüştürmesi **Termsofuseconsentversion** talebinin değerinin `v1` ' e eşit olup olmadığını denetler.

```XML
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek
- Giriş talepleri:
    - **inputClaim1**: v1
- Giriş parametreleri:
    - **CompareTo**: v1
    - **işleç**: eşittir
    - **IgnoreCase**: true
- Çıkış talepleri:
    - **Outputclaim**: true

## <a name="createrandomstring"></a>CreateRandomString

Rastgele sayı oluşturucuyu kullanarak rastgele bir dize oluşturur. Rastgele sayı Oluşturucu `integer` türünde ise, isteğe bağlı olarak bir çekirdek parametresi ve en yüksek sayı belirtilebilir. İsteğe bağlı bir dize biçim parametresi, çıktının kullanılarak biçimlendirilmesini sağlar ve isteğe bağlı bir Base64 parametresi çıktının Base64 kodlamalı Rasgelegeneratortype [GUID, Integer] outputClaim (dize) olup olmadığını belirtir.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | Rasgelegeneratortype | dize | Oluşturulacak rastgele değeri, `GUID` (genel benzersiz KIMLIK) veya `INTEGER` (bir sayı) belirtir. |
| InputParameter | stringFormat | dize | Seçim Rastgele değeri biçimlendirin. |
| InputParameter | biçiminde | Boole değeri | Seçim Rastgele değeri Base64 olarak dönüştürür. Dize biçimi uygulanmışsa dize biçiminden sonraki değer Base64 olarak kodlanır. |
| InputParameter | maximumNumber | int | Seçim Yalnızca `INTEGER` randomGeneratorType. En yüksek sayıyı belirtin. |
| InputParameter | çekirdek  | int | Seçim Yalnızca `INTEGER` randomGeneratorType. Rastgele değer için çekirdek belirtin. Note: aynı çekirdek aynı rastgele sayı dizisini verir. |
| outputClaim | outputClaim | dize | Bu talep dönüşümünde oluşturulacak ClaimTypes. Rastgele değer. |

Aşağıdaki örnek, genel benzersiz bir KIMLIK üretir. Bu talep dönüştürmesi, rastgele UPN (Kullanıcı asıl adı) oluşturmak için kullanılır.

```XML
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Örnek

- Giriş parametreleri:
    - **Randomgeneratortype**: GUID
- Çıkış talepleri:
    - **Outputclaim**: bc8bedd2-aaa3-411E-bdee-2f1810b73dfc

Aşağıdaki örnek 0 ile 1000 arasında bir tamsayı rastgele değeri üretir. Değer, OTP_ {Random Value} olarak biçimlendirilir.

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş parametreleri:
    - **Randomgeneratortype**: tamsayı
    - **Maximumnumber**: 1000
    - **StringFormat**: OTP_ @ no__t-1
    - **Base64**: yanlış
- Çıkış talepleri:
    - **Outputclaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Bir talebi, belirtilen biçim dizesine göre biçimlendirin. Bu dönüşüm C# `String.Format` yöntemini kullanır.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | Inputclaim |dize |@No__t-0 parametresi dize biçiminde davranan ClaimType. |
| InputParameter | stringFormat | dize | @No__t-0 parametresi dahil dize biçimi. |
| outputClaim | outputClaim | dize | Bu talep dönüştürmesinin ardından üretilen ClaimType çağırılır. |

@No__t-0 olan bir dizeyi biçimlendirmek için bu talep dönüşümünü kullanın. Aşağıdaki örnek bir **userPrincipalName**oluşturur. @No__t-0 gibi tüm sosyal kimlik sağlayıcısı teknik profillerinin bir **userPrincipalName**oluşturmak Için **createuserprincipalname** çağrısı.

```XML
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
    - **ınputclaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Giriş parametreleri:
    - **StringFormat**: cpim_ @ no__t-1 @ {RelyingPartyTenantId}
- Çıkış talepleri:
  - **Outputclaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>Formatstringmultipleclaim

Belirtilen biçim dizesine göre iki talebi biçimlendirin. Bu dönüşüm C# **String. Format** yöntemini kullanır.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | Inputclaim |dize | @No__t-0 parametresi dize biçiminde davranan ClaimType. |
| Inputclaim | Inputclaim | dize | @No__t-0 parametresi dize biçiminde davranan ClaimType. |
| InputParameter | stringFormat | dize | @No__t-0 ve {1} parametreleri de dahil olmak üzere dize biçimi. |
| outputClaim | outputClaim | dize | Bu talep dönüştürmesinin ardından üretilen ClaimType çağırılır. |

İki parametreli dize biçimlendirmek için bu talep dönüşümünü kullanın, {0} ve {1}. Aşağıdaki örnek, belirtilen biçimde bir **DisplayName** oluşturuyor:

```XML
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
    - **inputClaim1**: ali
    - **inputClaim2**: Fernando
- Giriş parametreleri:
    - **StringFormat**: {0} {1}
- Çıkış talepleri:
    - **Outputclaim**: ali Fernando

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Talep **kısıtlama** koleksiyonundan bir öğe aranıyor.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | Mapfromclaım | dize | **Kısıtlama** koleksiyonuyla **restrictionValueClaim** taleplerde aranacak metni içeren talep.  |
| outputClaim | restrictionValueClaim | dize | **Kısıtlama** toplamayı içeren talep. Talep dönüştürme çağrıldıktan sonra, bu talebin değeri seçili öğenin değerini içerir. |

Aşağıdaki örnek hata anahtarına bağlı olarak hata iletisi açıklamasını arar. **Responsemsg** talebi, son kullanıcıya sunmak veya bağlı olan tarafa gönderilmek üzere bir hata iletileri koleksiyonu içerir.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
Talep dönüştürmesi öğenin metnini arar ve değerini döndürür. Kısıtlama `<LocalizedCollection>` kullanılarak yerelleştirilmiştir, talep dönüştürmesi yerelleştirilmiş değeri döndürür.

```XML
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
    - **Mapfromclaım**: B2C_V1_90001
- Çıkış talepleri:
    - **restrictionValueClaim**: bir küçük olduğunuz için oturum açmanız yapılamıyor.

## <a name="lookupvalue"></a>LookupValue

Bir değer listesinden, başka bir talebin değerine göre bir talep değeri arama.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | ınputparameterıd | dize | Arama değerini içeren talep |
| InputParameter | |dize | InputParameters koleksiyonu. |
| InputParameter | errorOnFailedLookup | Boole değeri | Eşleşen arama olmadığında bir hatanın döndürülüp döndürülmeyeceğini denetleme. |
| outputClaim | ınputparameterıd | dize | Bu talep dönüşümünde oluşturulacak ClaimTypes. Eşleşen kimliğin değeri. |

Aşağıdaki örnek, InputParameters koleksiyonlarından birinde etki alanı adını arar. Talep dönüştürmesi, Tanımlayıcıdaki etki alanı adını arar ve değerini (bir uygulama KIMLIĞI) döndürür.

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
    - **ınputparameterıd**: test.com
- Giriş parametreleri:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **Microsoft.com**: 0213308f-17cb-4398-b97e-01dav7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **Erroronfailedlookup**: false
- Çıkış talepleri:
    - **Outputclaim**: c7026f88-4299-4cdb-965d-3f166464b8a9

## <a name="nullclaim"></a>NullClaim

Belirli bir talebin değerini temizleyin.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| outputClaim | claim_to_null | dize | Değerini NULL olarak talep edin. |

Talep özelliği çantasından gereksiz verileri kaldırmak için bu talep dönüşümünü kullanın. Bu nedenle, oturum tanımlama bilgisi daha küçük olacaktır. Aşağıdaki örnek `TermsOfService` talep türünün değerini kaldırır.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Giriş talepleri:
    - **Outputclaim**: contoso uygulamasına hoş geldiniz. Bu Web sitesine gözatıp kullanmaya devam ederseniz, kabul etmiş ve aşağıdaki hüküm ve koşullara göre bağlanmaları gerekir...
- Çıkış talepleri:
    - **Outputclaim**: null

## <a name="parsedomain"></a>ParseDomain

Bir e-posta adresinin etki alanı kısmını alır.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | EmailAddress | dize | E-posta adresini içeren ClaimType. |
| outputClaim | etki alanı | dize | Bu talep dönüştürme işleminden sonra üretilen ClaimType, etki alanı. |

Kullanıcının @ simgesinden sonra etki alanı adını ayrıştırmak için bu talep dönüşümünü kullanın. Bu, denetim verilerinden kişisel olarak tanımlanabilen bilgilerin (PII) kaldırılması için yararlı olabilir. Aşağıdaki talep dönüşümünde, bir **e-posta** talebinde etki alanı adının nasıl ayrıştırılacağını gösterilmektedir.

```XML
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
  - **Emadresi**: joe@outlook.com
- Çıkış talepleri:
    - **etki alanı**: Outlook.com

## <a name="setclaimsifstringsareequal"></a>Setclaimsıfstringsareeşittir

Bir dize talebi ve `matchTo` giriş parametresinin eşit olduğunu ve `stringMatchMsg` ve `stringMatchMsgCode` giriş parametrelerinde bulunan değer ile birlikte çıkış taleplerini, `true` veya `false` olarak ayarlanacak bir sonucu Il.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | Inputclaim | dize | Karşılaştırılacak talep türü. |
| InputParameter | matchTo | dize | @No__t-0 ile Karşılaştırılacak dize. |
| InputParameter | stringComparison | dize | Olası değerler: `Ordinal` veya `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | dize | Dizeler eşitse ayarlanacak ilk değer. |
| InputParameter | stringMatchMsgCode | dize | Dizeler eşitse ayarlanacak ikinci değer. |
| outputClaim | outputClaim1 | dize | Dizeler eşitse, bu çıkış talebi `stringMatchMsg` giriş parametresinin değerini içerir. |
| outputClaim | outputClaim2 | dize | Dizeler eşitse, bu çıkış talebi `stringMatchMsgCode` giriş parametresinin değerini içerir. |
| outputClaim | stringCompareResultClaim | Boole değeri | Karşılaştırma sonuçlarına göre `true` veya `false` olarak ayarlanacak karşılaştırma sonucu çıkış talep türü. |

Bu talep dönüşümünü, bir talebin belirttiğiniz değere eşit olup olmadığını denetlemek için kullanabilirsiniz. Örneğin, aşağıdaki talep dönüştürmesi **Termsofuseconsentversion** talebinin değerinin `v1` ' e eşit olup olmadığını denetler. Yanıt Evet ise, değeri `v2` olarak değiştirin.

```XML
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Örnek

- Giriş talepleri:
    - **ınputclaim**: v1
- Giriş parametreleri:
    - **eşleşme**: v1
    - **StringComparison**: OrdinalIgnoreCase
    - **Stringmatchmsg**: B2C_V1_90005
    - **Stringmatchmsgcode**: TOS v2 'ye yükseltildi
- Çıkış talepleri:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: TOS v2 'ye yükseltilir
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>Setclaimsıfstringsmatch

Bir dize talebi ve `matchTo` giriş parametresinin eşit olup olmadığını denetler ve `outputClaimIfMatched` giriş parametresinde bulunan değer ile birlikte çıkış taleplerini, karşılaştırma sonucuna göre `true` veya `false` olarak ayarlanacak şekilde karşılaştırın.

| Öğe | Dönüştürme Tionclaimtype | Veri Türü | Notlar |
| ---- | ----------------------- | --------- | ----- |
| Inputclaim | claimToMatch | dize | Karşılaştırılacak talep türü. |
| InputParameter | matchTo | dize | Inputclaim ile Karşılaştırılacak dize. |
| InputParameter | stringComparison | dize | Olası değerler: `Ordinal` veya `OrdinalIgnoreCase`. |
| InputParameter | Outputclaimifeşleşti | dize | Dizeler eşitse ayarlanacak değer. |
| outputClaim | outputClaim | dize | Dizeler eşitse, bu çıkış talebi `outputClaimIfMatched` giriş parametresinin değerini içerir. Ya da dizeler eşleşmiyorsa null. |
| outputClaim | stringCompareResultClaim | Boole değeri | Karşılaştırma sonuçlarına göre `true` veya `false` olarak ayarlanacak karşılaştırma sonucu çıkış talep türü. |

Örneğin, aşağıdaki talep dönüştürmesi, **Agegroup** talebinin değerinin `Minor` ' e eşit olup olmadığını denetler. Yanıt Evet ise, değeri `B2C_V1_90001` ' a döndürün.

```XML
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Örnek

- Giriş talepleri:
    - **claimToMatch**: küçük
- Giriş parametreleri:
    - **eşleşme**: küçük
    - **StringComparison**: OrdinalIgnoreCase
    - **Outputclaimifeşleşti**: B2C_V1_90001
- Çıkış talepleri:
    - **isMinorResponseCode**: B2C_V1_90001
    - **ısminor**: doğru

