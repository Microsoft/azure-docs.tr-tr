---
title: Azure Active Directory B2C bir özel ilkede doğrulama teknik profili tanımlama | Microsoft Docs
description: Azure Active Directory B2C bir özel ilkede Azure Active Directory teknik profili tanımlayın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ad15342e6d35a5c6101beb1ddc09d4ce1f2089d5
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167568"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C özel ilkesinde doğrulama teknik profili tanımlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Bir doğrulama teknik profili, [Azure Active Directory](active-directory-technical-profile.md) veya [REST API](restful-technical-profile.md)gibi herhangi bir protokolün sıradan bir teknik profilidir. Doğrulama teknik profili, aşağıdaki verilerle çıkış taleplerini döndürür veya bir HTTP 409 hata iletisi (çakışma yanıtı durum kodu) döndürür:

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Bir doğrulama teknik profilinden döndürülen talepler, talep paketine geri eklenir. Bu talepleri bir sonraki doğrulama teknik profillerinde kullanabilirsiniz.

Doğrulama teknik profilleri, **Validationteknisyen** öğesinde göründükleri sırada yürütülür. Doğrulama teknik profili bir hata harekete geçirirse veya başarılı olursa, sonraki doğrulama teknik profillerinin yürütülmesine devam edip etmeyeceğini doğrulama teknik profilinde yapılandırabilirsiniz.

Doğrulama teknik profili, **Validationteknisyen** öğesinde tanımlanan önkoşullara göre koşullu bir şekilde yürütülebilir. Örneğin, belirli taleplerin varolup olmadığını veya bir talebin belirtilen değere eşit olup olmadığını kontrol edebilirsiniz.

Kendi kendine onaylanan bir teknik profil, çıkış taleplerinin bazılarını veya tümünü doğrulamak için kullanılmak üzere bir doğrulama teknik profili tanımlayabilir. Başvurulan teknik profilin tüm giriş talepleri, başvuran doğrulama teknik profilinin çıkış talepleri ' nde görünmelidir.

> [!NOTE]
> Yalnızca kendi kendine onaylanan teknik profiller, doğrulama teknik profillerini kullanabilir. Kendi kendini onaylanan teknik profillerdeki çıkış taleplerini doğrulamanız gerekiyorsa, Kullanıcı yolculuğunda, doğrulama aşamasında teknik profile uyum sağlamak için ek bir düzenleme adımı kullanmayı göz önünde bulundurun.    

## <a name="validationtechnicalprofiles"></a>Validationcealprofiles

**Validation, Alprofiles** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Açıklama |
| ------- | ----------- | ----------- |
| Validationteknisyen Alprofıle | 1: n | Başvuru yapan teknik profilin bazı veya tüm çıkış taleplerini doğrulamak için kullanılacak teknik bir profil. |

**Validation, Alprofile** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| ReferenceId | Yes | İlkede veya üst ilkede önceden tanımlanmış bir teknik profil tanıtıcısı. |
|ContinueOnError|Hayır| Bu doğrulama teknik profili bir hata harekete geçirirse sonraki doğrulama teknik profillerinin doğrulanmasının gerekip gerekmediğini belirtir. Olası değerler: `true` veya `false` (varsayılan, daha fazla doğrulama profili işleme durdurulur ve bir hata döndürülür). |
|Devam onSuccess | Hayır | Bu doğrulama teknik profili başarılı olursa sonraki doğrulama profillerinin doğrulanmasının gerekip gerekmediğini belirtir. Olası değerler: `true` veya `false`. Varsayılan değer `true`, diğer doğrulama profillerinin işlenmesinin devam edemeyeceği anlamına gelir. |

**Validation, Alprofile** öğesi aşağıdaki öğeyi içerir:

| Öğe | Öğeleri | Açıklama |
| ------- | ----------- | ----------- |
| Üstbilgisinde | 0:1 | Doğrulama teknik profilinin yürütülmesi için karşılanması gereken önkoşulların bir listesi. |

**Önkoşul** öğesi aşağıdaki özniteliği içerir:

| Öznitelik | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| `Type` | Yes | Önkoşul için gerçekleştirilecek denetim veya sorgunun türü. `ClaimsExist`, belirtilen talepler kullanıcının geçerli talep kümesinde varsa eylemlerin gerçekleştirilmesini sağlamak için `ClaimEquals` veya belirtilen talep varsa ve değeri belirtilen değere eşitse, eylemlerin gerçekleştirilmesi gerektiğini sağlamak üzere belirtilmiştir. |
| `ExecuteActionsIf` | Yes | Test true veya false olduğunda önkoşuldaki eylemlerin gerçekleştirilip gerçekleştirilmeyeceğini gösterir. |

**Önkoşul** öğesi aşağıdaki öğeleri içerir:

| Öğe | Öğeleri | Açıklama |
| ------- | ----------- | ----------- |
| Değer | 1: n | Denetim tarafından kullanılan veriler. Bu denetim türü `ClaimsExist`, bu alan, sorgulanacak bir ClaimTypeReferenceId belirtir. Denetim türü `ClaimEquals`ise bu alan, sorgulanacak bir ClaimTypeReferenceId belirtir. Başka bir değer öğesi, denetlenecek değeri içerdiğinde.|
| Eylem | 1:1 | Bir düzenleme adımı içindeki önkoşul denetimi doğru ise, alınması gereken eylem. **Eylemin** değeri `SkipThisValidationTechnicalProfile`olarak ayarlanır. İlişkili doğrulama teknik profilinin yürütülmemelidir. |

### <a name="example"></a>Örnek

Aşağıdaki örnek, bu doğrulama teknik profillerini kullanır:

1. İlk doğrulama teknik profili, Kullanıcı kimlik bilgilerini denetler ve geçersiz Kullanıcı adı veya hatalı parola gibi bir hata oluşursa devam etmez.
2. UserType talebi yoksa ve userType değeri `Partner`ise, sonraki doğrulama teknik profili yürütülmez. Doğrulama teknik profili, iç müşteri veritabanından kullanıcı profilini okumaya çalışır ve REST API hizmet kullanılamıyor ya da herhangi bir iç hata gibi bir hata oluşursa devam eder.
3. Son doğrulama teknik profili, userType talebi yoksa veya userType değeri `Customer`ise yürütülmez. Doğrulama teknik profili, iç iş ortağı veritabanından kullanıcı profilini okumaya çalışır ve REST API hizmet kullanılamıyor ya da herhangi bir iç hata gibi bir hata oluşursa devam eder.

```XML
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
