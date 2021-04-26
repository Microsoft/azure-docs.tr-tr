---
title: Azure Active Directory B2C | Kullanıcı erişimini yönetme Microsoft Docs
description: Minors 'i belirlemeyi, Doğum tarihini ve ülke/bölge verilerini toplamayı ve Azure AD B2C kullanarak uygulamanızdaki kullanım koşullarını almayı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0ee26e7fe74d87f7b20f9a28b049b8043b376273
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518067"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 'de Kullanıcı erişimini yönetme

Bu makalede, Azure Active Directory B2C (Azure AD B2C) kullanılarak uygulamalarınıza yönelik Kullanıcı erişiminin nasıl yönetileceği açıklanır. Uygulamanızdaki erişim yönetimi şunları içerir:

- Sömürmeyi amaçlama 'i tanımlama ve uygulamanıza Kullanıcı erişimini denetleme.
- Uygulamalarınızı kullanmak için sömürmeyi amaçlama için ana izin isteme.
- Kullanıcılardan Doğum ve ülke/bölge verilerini toplama.
- Kullanım Koşulları Sözleşmesi yakalama ve erişim erişimi.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Düşük erişimi denetleme

Uygulamalar ve kuruluşlar, bu kitlede hedeflenen uygulamaları ve Hizmetleri kullanarak minörleri engellemeye karar verebilir. Alternatif olarak, uygulamalar ve kuruluşlar, azmaları kabul edip daha sonra ana onayı yönetmeyi ve daha sonra, iş kuralları tarafından dikte edilen ve yönetmelik tarafından izin verilen deneyimler için izin verilen deneyimler sunmaya karar verebilir.

Bir Kullanıcı küçük olarak tanımlanmışsa, Azure AD B2C Kullanıcı akışını üç seçenekten birine ayarlayabilirsiniz:

- **İmzalı BIR JWT id_token uygulamaya geri gönderin**: Kullanıcı dizine kaydedilir ve uygulamaya bir belirteç döndürülür. Uygulama daha sonra iş kuralları uygulanarak devam eder. Örneğin, uygulama bir ebeveyn onay işlemiyle devam edebilir. Bu yöntemi kullanmak için, uygulamadan **Agegroup** ve **consentProvidedForMinor** taleplerini almayı seçin.

- **Uygulamaya imzasız BIR JSON belirteci gönderin**: Azure AD B2C, uygulamayı kullanıcının küçük olduğunu ve kullanıcının ebeveyn onayı durumunu sağladığını bildirir. Uygulama daha sonra iş kuralları uygulanarak devam eder. JSON belirteci, uygulama ile başarılı bir kimlik doğrulamasını tamamlamaz. Uygulamanın kimliği, **e-posta**, **Agegroup** ve **consentProvidedForMinor** içerebilen JSON **belirtecine dahil edilen** talebe göre kimliği doğrulanmamış kullanıcıyı işlemesi gerekir.

- **Kullanıcıyı engelle**: bir Kullanıcı küçük ise ve ebeveyn onayı sağlanmadıysa Azure AD B2C, kullanıcıyı engellendikleri konusunda bilgilendirir. Hiçbir belirteç verilmez, erişim engellenir ve Kullanıcı hesabı bir kayıt yolculuğu sırasında oluşturulmaz. Bu bildirimi uygulamak için, kullanıcıyı bilgilendirmek ve uygun seçenekleri sunmak üzere uygun bir HTML/CSS içerik sayfası sağlarsınız. Yeni kayıtlar için uygulama için başka bir eylem gerekmez.

## <a name="get-parental-consent"></a>Ebeveyn onayını al

Uygulama yönetmeliklerinize bağlı olarak, ebeveyn onayı yetişkin olarak doğrulanan bir kullanıcı tarafından verilmesi gerekebilir. Azure AD B2C, bir kişinin yaşını doğrulamak ve daha sonra doğrulanmış bir yetişkin 'ın küçük bir ebeveyn onayı sağlamasına izin vermek için bir deneyim sağlamaz. Bu deneyim, uygulama veya başka bir hizmet sağlayıcısı tarafından sağlanmalıdır.

Aşağıda, ebeveyn onayını toplamak için bir Kullanıcı akışı örneği verilmiştir:

1. Bir [MICROSOFT Graph API](/graph/use-the-api) işlemi, kullanıcıyı küçük olarak tanımlar ve Kullanıcı verilerini, IMZASıZ bir JSON belirteci biçiminde uygulamaya döndürür.

2. Uygulama JSON belirtecini işler ve küçük bir ekran gösterir ve bu, ebeveyn onayı 'nın gerekli olduğunu ve bir üst çevrimiçi onay isteğini ister.

3. Azure AD B2C, kullanıcının normal olarak oturum açabileceği ve **Magalagegroupclassification = "minorWithParentalConsent"** olarak ayarlanan uygulamaya bir belirteç verdiği bir oturum açma yolculuğunu gösterir. Uygulama, üst öğenin e-posta adresini toplar ve üst öğenin bir yetişkin olduğunu doğrular. Bunu yapmak için, Ulusal KIMLIK ofisi, lisans doğrulama veya kredi kartı kanıtı gibi güvenilir bir kaynak kullanır. Doğrulama başarılı olursa, uygulama Azure AD B2C Kullanıcı akışını kullanarak oturum açmak için küçük bir değer ister. İzin reddedilirse (örneğin, Eğer **ılgalagegroupclassification = "minorWithoutParentalConsent"**), Azure AD B2C onay işlemini yeniden başlatmak için uygulamaya bir JSON belirteci (oturum değil) döndürür. Küçük veya yetişkin 'in kayıt sırasında ikincil e-posta adresine veya yetişkin 'in e-posta adresine bir kayıt kodu göndererek küçük veya yetişkin 'in bir alt hesaba yeniden erişmesini sağlamak için Kullanıcı akışının özelleştirilmesi isteğe bağlı olarak özelleştirilebilir.

4. Uygulama, onayı iptal etmek için küçük bir seçenek sunar.

5. Küçük ya da yetişkin iptal edildiğinde, **consentProvidedForMinor** **değiştirmek için Microsoft Graph** API 'si kullanılabilir. Alternatif olarak, uygulama, onayı iptal edilmiş bir ikincil silme işlemi de seçebilir. Kullanıcı akışının, kimliği doğrulanmış alt öğe (veya ikincil hesabı kullanan üst) onayı iptal edebilmesi için, bu, isteğe bağlı olarak özelleştirilebilir. Azure AD B2C kayıtları **reddedildi** olarak **consentProvidedForMinor** .

**Ligalagegroupclassification**, **ConsentProvidedForMinor** ve **agegroup** hakkında daha fazla bilgi için bkz. [Kullanıcı kaynak türü](/graph/api/resources/user). Özel öznitelikler hakkında daha fazla bilgi için bkz. [Tüketicileriniz hakkında bilgi toplamak için özel öznitelikler kullanma](user-flow-custom-attributes.md). Microsoft Graph API 'sini kullanarak genişletilmiş öznitelikleri adresleyerek, özniteliğin uzun sürümünü kullanmanız gerekir (örneğin, *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*).

## <a name="gather-date-of-birth-and-countryregion-data"></a>Doğum tarihi ve ülke/bölge verileri toplama

Uygulamalar, kayıt sırasında tüm kullanıcılardan Doğum tarihini (DOB) ve ülke/bölge bilgilerini toplamak için Azure AD B2C kullanabilir. Bu bilgiler henüz yoksa, uygulama bir sonraki kimlik doğrulaması (oturum açma) yolculuğu sırasında kullanıcıdan istekte bulunabilir. Kullanıcılar, kendi DOB ve ülke/bölge bilgilerini sağlamadan devam edemez. Azure AD B2C, kişinin söz konusu ülkenin/bölgenin mevzuata standartlarına göre küçük olarak kabul edilip edilmeyeceğini belirlemede bu bilgileri kullanır.

Özelleştirilmiş bir Kullanıcı akışı, DOB ve ülke/bölge bilgilerini toplayabilir ve Azure AD B2C talep dönüşümünü kullanarak, **Agegrubunu** belirleyebilir ve sonucu kalıcı hale getirebilirsiniz (ya da doğrudan DOB ve ülke/bölge bilgilerini doğrudan kalıcı olarak).

Aşağıdaki adımlarda, kullanıcının Doğum tarihinden sonra **Agegrubunu** hesaplamak için kullanılan mantık gösterilmektedir:

1. Ülke/bölge kodunu listedeki ülke/bölge kodu ile bulmayı deneyin. Ülke/bölge bulunamazsa, **varsayılana** geri dönün.

2. Ülke/bölge öğesinde **MinorConsent** düğümü varsa:

    a. Kullanıcının yetişkin olarak kabul edilmesi için doğmuş olması gereken tarihi hesaplayın. Örneğin, geçerli tarih 14 Mart 2015 ve **MinorConsent** 18 ise, Doğum tarihi 14 Mart 2000 ' den sonra olmamalıdır.

    b. En küçük Doğum tarihini gerçek Doğum tarihiyle karşılaştırın. En küçük Doğum tarihi kullanıcının Doğum tarihinden önce ise, hesaplama yaş grubu hesaplaması olarak **İkincil** döndürür.

3. Ülke/bölge öğesinde **MinorNoConsentRequired** düğümü varsa, **MinorNoConsentRequired**' deki değeri kullanarak 2A ve 2B adımlarını yineleyin. 2B çıkışı, en az Doğum tarihi kullanıcının Doğum tarihinden önce ise, **MinorNoConsentRequired** döndürür.

4. Hiçbir hesaplama true döndürürse, hesaplama **yetişkin** döndürür.

Bir uygulama, başka yöntemlerle güvenilir bir şekilde DOB veya ülke/bölge verileri toplanmışsa, bu bilgileri kullanarak uygulama kullanıcı kaydını güncelleştirmek için Graph API kullanabilir. Örnek:

- Bir kullanıcının yetişkin olduğu bilindiğinde, bir **yetişme** değeri olan **agegroup** adlı dizin özniteliğini güncelleştirin.
- Bir kullanıcının küçük olduğu bilindiğinde, dizin özniteliği **Agegroup** değerini bir **Minor** değeri ile güncelleştirin ve **consentProvidedForMinor** olarak ayarlayın.

## <a name="minor-calculation-rules"></a>Küçük hesaplama kuralları

Yaş aşımları iki yaş değeri içerir: birinin artık küçük olarak değerlendirilmediği yaş ve küçük bir ana izin olması gereken yaş. Aşağıdaki tabloda, düşük ve küçük bir onay gerektiren bir izin tanımlamak için kullanılan yaş kuralları listelenmektedir.

| Ülke/Bölge | Ülke/bölge adı | Küçük onay yaşı | Küçük yaş |
| -------------- | ------------------- | ----------------- | --------- |
| Varsayılan | Yok | Yok | 18 |
| AE | Birleşik Arap Emirlikleri | Yok | 21 |
| AT | Avusturya | 14 | 18 |
| BE | Belçika | 14 | 18 |
| BG | Bulgaristan | 16 | 18 |
| BH | Bahreyn | Yok | 21 |
| CM | Kamerun | Yok | 21 |
| CY | Kıbrıs | 16 | 18 |
| CZ | Çek Cumhuriyeti | 16 | 18 |
| DE | Almanya | 16 | 18 |
| DK | Danimarka | 16 | 18 |
| EE | Estonya | 16 | 18 |
| EG | Mısır | Yok | 21 |
| ES | İspanya | 13 | 18 |
| GS | Fransa | 16 | 18 |
| GB | Birleşik Krallık | 13 | 18 |
| GR | Yunanistan | 16 | 18 |
| HR | Hırvatistan | 16 | 18 |
| HU | Macaristan | 16 | 18 |
| IE | İrlanda | 13 | 18 |
| BT | İtalya | 16 | 18 |
| KR | Kore Cumhuriyeti | 14 | 18 |
| LT | Litvanya | 16 | 18 |
| LU | Lüksemburg | 16 | 18 |
| LV | Letonya | 16 | 18 |
| MT | Malta | 16 | 18 |
| NA | Namibya | Yok | 21 |
| NL | Hollanda | 16 | 18 |
| PL | Polonya | 13 | 18 |
| PT | Portekiz | 16 | 18 |
| RO | Romanya | 16 | 18 |
| SE | İsveç | 13 | 18 |
| SG | Singapur | Yok | 21 |
| SI | Slovenya | 16 | 18 |
| SK | Slovakya | 16 | 18 |
| TD | Çad | Yok | 21 |
| TH | Tayland | Yok | 20 |
| TW | Tayvan | Yok | 20 |
| ABD | Birleşik Devletler | 13 | 18 |



## <a name="capture-terms-of-use-agreement"></a>Kullanım koşullarını yakala sözleşmesi

Uygulamanızı geliştirirken, kullanıcıların, Kullanıcı dizininden yalnızca küçük ve yalnızca ikincil, katılım olmadan uygulamalarında kullanım koşullarını kabul ettiğini yakalarsınız. Bununla birlikte, bir kullanıcının kullanım koşulları 'nı kabul etmek için bir Azure AD B2C Kullanıcı akışı kullanmak, kabul verilmezse erişimi kısıtlamak ve en son kabul tarihi ve kullanım koşullarının en son sürümünün tarihi temelinde kullanım koşullarında gelecekteki değişikliklerin kabul edilmesini zorlamak mümkündür.

**Kullanım koşulları** , "verileri üçüncü taraflarla paylaşma izni" de içerebilir. Yerel yönetmeliklere ve iş kurallarına bağlı olarak, bir kullanıcının her iki koşulun de kabul edilmesini toplayabilir veya kullanıcının diğerini değil bir koşulu kabul etmesine izin verebilirsiniz.

Aşağıdaki adımlar kullanım koşullarını nasıl yönetebileceğinizi anlatmaktadır:

1. Graph API ve genişletilmiş öznitelikleri kullanarak kullanım koşullarının ve kabul tarihinin kabul edildiğini kaydedin. Hem yerleşik hem de özel Kullanıcı akışlarını kullanarak bunu yapabilirsiniz. **Extension_termsOfUseConsentDateTime** ve **extension_termsOfUseConsentVersion** özniteliklerini oluşturup kullanmanızı öneririz.

2. "Kullanım koşulları 'nı kabul et" etiketli gerekli onay kutusunu oluşturun ve kayıt sırasında sonucu kaydedin. Hem yerleşik hem de özel Kullanıcı akışlarını kullanarak bunu yapabilirsiniz.

3. Azure AD B2C kullanım koşullarını ve kullanıcının kabulünü depolar. Graph API, yanıtı kaydetmek için kullanılan uzantı özniteliğini okuyarak herhangi bir kullanıcının durumunu sorgulamak için kullanabilirsiniz (örneğin, **Termsofusetestupdatedatetime**' ı okuyun). Hem yerleşik hem de özel Kullanıcı akışlarını kullanarak bunu yapabilirsiniz.

4. Kabul tarihini, kullanım koşullarının en son sürümünün tarihine göre karşılaştırarak, güncelleştirilmiş kullanım koşullarının kabul edilmesi gerekir. Yalnızca özel bir Kullanıcı akışı kullanarak tarihleri karşılaştırabilirsiniz. **Extension_termsOfUseConsentDateTime** genişletilmiş özniteliği kullanın ve değeri **Termsofusetextupdatedatetime** talebiyle karşılaştırın. Kabul eski ise, otomatik olarak onaylanan bir ekran görüntüleyerek yeni bir kabul zorlayın. Aksi takdirde, ilke mantığını kullanarak erişimi engelleyin.

5. Kabulünün sürüm numarasını kabul edilen en son sürüm numarasıyla karşılaştırarak, güncelleştirilmiş kullanım koşullarının kabul edilmesini gerektir. Yalnızca özel bir Kullanıcı akışı kullanarak sürüm numaralarını karşılaştırabilirsiniz. Genişletilmiş öznitelik **extension_termsOfUseConsentDateTime** kullanın ve değeri **extension_termsOfUseConsentVersion** talebiyle karşılaştırın. Kabul eski ise, otomatik olarak onaylanan bir ekran görüntüleyerek yeni bir kabul zorlayın. Aksi takdirde, ilke mantığını kullanarak erişimi engelleyin.

Kullanım Koşulları kabul etme koşullarını aşağıdaki senaryolar altında yakalayabilirsiniz:

- Yeni bir Kullanıcı kaydolduktan sonra. Kullanım koşulları görüntülenir ve kabul sonucu depolanır.
- Kullanıcı, en son veya etkin kullanım koşullarını önceden kabul etmiş olan oturum açma işlemi. Kullanım koşulları görüntülenmez.
- Kullanıcı, en son veya etkin kullanım koşullarını henüz kabul etmediyse oturum açmış. Kullanım koşulları görüntülenir ve kabul sonucu depolanır.
- Kullanıcı kullanım koşullarının daha eski bir sürümünü zaten kabul etmiş olan ve artık en son sürüme güncelleştirilmiş olan oturum açmış. Kullanım koşulları görüntülenir ve kabul sonucu depolanır.

Aşağıdaki görüntüde önerilen Kullanıcı akışı gösterilmektedir:

![Önerilen kabul Kullanıcı akışını gösteren akış grafiği diyagramı](./media/manage-user-access/user-flow.png)

Aşağıda, bir talepteki Tarih temelli kullanım koşulları onayı örneği verilmiştir. `extension_termsOfUseConsentDateTime`Talep şundan eskiyse `2025-01-15T00:00:00` , `termsOfUseConsentRequired` Boole talebini denetleyerek ve kendinden onaylanan bir ekran görüntüleyerek yeni bir kabul zorlayın. 

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2025-01-15T00:00:00" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Aşağıda, bir talepteki sürüm tabanlı kullanım koşulları onayı örneği verilmiştir. `extension_termsOfUseConsentVersion`Talep değerine eşit değilse `V1` , `termsOfUseConsentRequired` Boole talebini denetleyerek ve kendinden onaylanan bir ekran görüntüleyerek yeni bir kabul zorlayın.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
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
</ClaimsTransformations>
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2C yaş geçişi 'Ni etkinleştirin](age-gating.md).
- Kullanıcı verilerini silme ve dışa aktarma hakkında bilgi edinmek için bkz. [Kullanıcı verilerini yönetme](manage-user-data.md).
- Bir kullanım koşulları istemi uygulayan örnek bir özel ilke için bkz. [BIR B2C ıEF özel ilkesi-kaydolun ve ' Kullanım Koşulları ' Istemiyle oturum açın](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou).