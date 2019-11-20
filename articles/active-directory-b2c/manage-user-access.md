---
title: Azure Active Directory B2C | Kullanıcı erişimini yönetme Microsoft Docs
description: Minors 'i belirlemeyi, Doğum tarihini ve ülke/bölge verilerini toplamayı ve Azure AD B2C kullanarak uygulamanızdaki kullanım koşullarını almayı öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 98d3fa50f405658b33f879ed8e7b95667cddcedf
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064122"
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

- **Uygulamaya imzasız BIR JSON belirteci gönder**: Azure AD B2C, uygulamayı kullanıcının küçük olduğunu ve kullanıcının ebeveyn onayı durumunu sağladığını bildirir. Uygulama daha sonra iş kuralları uygulanarak devam eder. JSON belirteci, uygulama ile başarılı bir kimlik doğrulamasını tamamlamaz. Uygulamanın **kimliği,** **e-posta**, **AGEGROUP**ve **consentProvidedForMinor**içerebilen JSON belirtecine dahil edilen talebe göre kimliği doğrulanmamış kullanıcıyı işlemesi gerekir.

- **Kullanıcıyı engelle**: Bir Kullanıcı küçük ise ve ebeveyn onayı sağlanmadıysa, Azure AD B2C kullanıcıyı engellendikleri konusunda bilgilendirir. Hiçbir belirteç verilmez, erişim engellenir ve Kullanıcı hesabı bir kayıt yolculuğu sırasında oluşturulmaz. Bu bildirimi uygulamak için, kullanıcıyı bilgilendirmek ve uygun seçenekleri sunmak üzere uygun bir HTML/CSS içerik sayfası sağlarsınız. Yeni kayıtlar için uygulama için başka bir eylem gerekmez.

## <a name="get-parental-consent"></a>Ebeveyn onayını al

Uygulama yönetmeliklerinize bağlı olarak, ebeveyn onayı yetişkin olarak doğrulanan bir kullanıcı tarafından verilmesi gerekebilir. Azure AD B2C, bir kişinin yaşını doğrulamak ve daha sonra doğrulanmış bir yetişkin 'ın küçük bir ebeveyn onayı sağlamasına izin vermek için bir deneyim sağlamaz. Bu deneyim, uygulama veya başka bir hizmet sağlayıcısı tarafından sağlanmalıdır.

Aşağıda, ebeveyn onayını toplamak için bir Kullanıcı akışı örneği verilmiştir:

1. Bir [Azure Active Directory Graph API](/previous-versions/azure/ad/graph/api/api-catalog) işlemi, kullanıcıyı küçük olarak tanımlar ve Kullanıcı verilerini, IMZASıZ bir JSON belirteci biçiminde uygulamaya döndürür.

2. Uygulama JSON belirtecini işler ve küçük bir ekran gösterir ve bu, ebeveyn onayı 'nın gerekli olduğunu ve bir üst çevrimiçi onay isteğini ister.

3. Azure AD B2C, kullanıcının normal olarak oturum açabileceği ve **Magalagegroupclassification = "minorWithParentalConsent"** olarak ayarlanan uygulamaya bir belirteç verdiği bir oturum açma yolculuğunu gösterir. Uygulama, üst öğenin e-posta adresini toplar ve üst öğenin bir yetişkin olduğunu doğrular. Bunu yapmak için, Ulusal KIMLIK ofisi, lisans doğrulama veya kredi kartı kanıtı gibi güvenilir bir kaynak kullanır. Doğrulama başarılı olursa, uygulama Azure AD B2C Kullanıcı akışını kullanarak oturum açmak için küçük bir değer ister. İzin reddedilirse (örneğin, Eğer **ılgalagegroupclassification = "minorWithoutParentalConsent"** ), Azure AD B2C onay işlemini yeniden başlatmak için uygulamaya bir JSON belirteci (oturum değil) döndürür. Küçük veya yetişkin 'in kayıt sırasında ikincil e-posta adresine veya yetişkin 'in e-posta adresine bir kayıt kodu göndererek küçük veya yetişkin 'in bir alt hesaba yeniden erişmesini sağlamak için Kullanıcı akışının özelleştirilmesi isteğe bağlı olarak özelleştirilebilir.

4. Uygulama, onayı iptal etmek için küçük bir seçenek sunar.

5. İkincil veya yetişkin iptal edildiğinde, **consentProvidedForMinor** değiştirmek IÇIN Azure AD Graph API **kullanılabilir.** Alternatif olarak, uygulama, onayı iptal edilmiş bir ikincil silme işlemi de seçebilir. Kullanıcı akışının, kimliği doğrulanmış alt öğe (veya ikincil hesabı kullanan üst) onayı iptal edebilmesi için, bu, isteğe bağlı olarak özelleştirilebilir. Azure AD B2C kayıtları **reddedildi**olarak **consentProvidedForMinor** .

**Ligalagegroupclassification**, **ConsentProvidedForMinor**ve **agegroup**hakkında daha fazla bilgi için bkz. [Kullanıcı kaynak türü](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user). Özel öznitelikler hakkında daha fazla bilgi için bkz. [Tüketicileriniz hakkında bilgi toplamak için özel öznitelikler kullanma](active-directory-b2c-reference-custom-attr.md). Azure AD Graph API kullanarak genişletilmiş öznitelikleri adresleyerek, *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*gibi özniteliğin uzun sürümünü kullanmanız gerekir: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Doğum tarihi ve ülke/bölge verileri toplama

Uygulamalar, kayıt sırasında tüm kullanıcılardan Doğum tarihini (DOB) ve ülke/bölge bilgilerini toplamak için Azure AD B2C kullanabilir. Bu bilgiler henüz yoksa, uygulama bir sonraki kimlik doğrulaması (oturum açma) yolculuğu sırasında kullanıcıdan istekte bulunabilir. Kullanıcılar, kendi DOB ve ülke/bölge bilgilerini sağlamadan devam edemez. Azure AD B2C, kişinin söz konusu ülkenin/bölgenin mevzuata standartlarına göre küçük olarak kabul edilip edilmeyeceğini belirlemede bu bilgileri kullanır.

Özelleştirilmiş bir Kullanıcı akışı, DOB ve ülke/bölge bilgilerini toplayabilir ve Azure AD B2C talep dönüşümünü kullanarak, **Agegrubunu** belirleyebilir ve sonucu kalıcı hale getirebilirsiniz (ya da doğrudan DOB ve ülke/bölge bilgilerini doğrudan kalıcı olarak).

Aşağıdaki adımlarda, kullanıcının Doğum tarihinden sonra **Agegrubunu** hesaplamak için kullanılan mantık gösterilmektedir:

1. Ülkeyi, listedeki ülke kodu ile bulmayı deneyin. Ülke bulunamazsa, **varsayılana**geri dönün.

2. Ülke öğesinde **MinorConsent** düğümü varsa:

    a. Kullanıcının yetişkin olarak kabul edilmesi için doğmuş olması gereken tarihi hesaplayın. Örneğin, geçerli tarih 14 Mart 2015 ve **MinorConsent** 18 ise, Doğum tarihi 14 Mart 2000 ' den sonra olmamalıdır.

    b. En küçük Doğum tarihini gerçek Doğum tarihiyle karşılaştırın. En küçük Doğum tarihi kullanıcının Doğum tarihinden önce ise, hesaplama yaş grubu hesaplaması olarak **İkincil** döndürür.

3. Ülke öğesinde **MinorNoConsentRequired** düğümü varsa, **MinorNoConsentRequired**' den değeri kullanarak 2A ve 2B adımlarını yineleyin. 2B çıkışı, en az Doğum tarihi kullanıcının Doğum tarihinden önce ise, **MinorNoConsentRequired** döndürür.

4. Hiçbir hesaplama true döndürürse, hesaplama **yetişkin**döndürür.

Bir uygulama, başka yöntemlerle güvenilir bir şekilde DOB veya ülke/bölge verileri toplanmışsa, bu bilgileri kullanarak uygulama kullanıcı kaydını güncelleştirmek için Graph API kullanabilir. Örneğin:

- Bir kullanıcının yetişkin olduğu bilindiğinde, bir **yetişme**değeri olan **agegroup** adlı dizin özniteliğini güncelleştirin.
- Bir kullanıcının küçük olduğu bilindiğinde, dizin özniteliği **Agegroup** değerini bir **Minor** değeri ile güncelleştirin ve **consentProvidedForMinor**olarak ayarlayın.

DOB verilerini toplama hakkında daha fazla bilgi için bkz. [Azure AD B2C yaş geçişi kullanma](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Kullanım koşullarını yakala sözleşmesi

Uygulamanızı geliştirirken, kullanıcıların, Kullanıcı dizininden yalnızca küçük ve yalnızca ikincil, katılım olmadan uygulamalarında kullanım koşullarını kabul ettiğini yakalarsınız. Bununla birlikte, bir kullanıcının kullanım koşulları kabulünü toplamak, kabul verilmezse erişimi kısıtlamak ve en son kabul ve Tarih tarihinin tarihine bağlı olarak kullanım koşullarında gelecekteki değişikliklerin kabul edilmesini zorlamak için bir Azure AD B2C Kullanıcı akışı kullanmak mümkündür.  Kullanım Koşullarının en son sürümü.

**Kullanım koşulları** , "verileri üçüncü taraflarla paylaşma izni" de içerebilir. Yerel yönetmeliklere ve iş kurallarına bağlı olarak, bir kullanıcının her iki koşulun de kabul edilmesini toplayabilir veya kullanıcının diğerini değil bir koşulu kabul etmesine izin verebilirsiniz.

Aşağıdaki adımlar kullanım koşullarını nasıl yönetebileceğinizi anlatmaktadır:

1. Graph API ve genişletilmiş öznitelikleri kullanarak kullanım koşullarının ve kabul tarihinin kabul edildiğini kaydedin. Hem yerleşik hem de özel Kullanıcı akışlarını kullanarak bunu yapabilirsiniz. **Extension_termsOfUseConsentDateTime** ve **extension_termsOfUseConsentVersion** özniteliklerini oluşturmanızı ve kullanmanızı öneririz.

2. "Kullanım koşulları 'nı kabul et" etiketli gerekli onay kutusunu oluşturun ve kayıt sırasında sonucu kaydedin. Hem yerleşik hem de özel Kullanıcı akışlarını kullanarak bunu yapabilirsiniz.

3. Azure AD B2C kullanım koşullarını ve kullanıcının kabulünü depolar. Graph API, yanıtı kaydetmek için kullanılan uzantı özniteliğini okuyarak herhangi bir kullanıcının durumunu sorgulamak için kullanabilirsiniz (örneğin, **Termsofusetestupdatedatetime**' ı okuyun). Hem yerleşik hem de özel Kullanıcı akışlarını kullanarak bunu yapabilirsiniz.

4. Kabul tarihini, kullanım koşullarının en son sürümünün tarihine göre karşılaştırarak, güncelleştirilmiş kullanım koşullarının kabul edilmesi gerekir. Yalnızca özel bir Kullanıcı akışı kullanarak tarihleri karşılaştırabilirsiniz. Genişletilmiş **extension_termsOfUseConsentDateTime**özniteliğini kullanın ve değeri **Termsofusetextupdatedatetime**talebiyle karşılaştırın. Kabul eski ise, otomatik olarak onaylanan bir ekran görüntüleyerek yeni bir kabul zorlayın. Aksi takdirde, ilke mantığını kullanarak erişimi engelleyin.

5. Kabulünün sürüm numarasını kabul edilen en son sürüm numarasıyla karşılaştırarak, güncelleştirilmiş kullanım koşullarının kabul edilmesini gerektir. Yalnızca özel bir Kullanıcı akışı kullanarak sürüm numaralarını karşılaştırabilirsiniz. Genişletilmiş **extension_termsOfUseConsentDateTime**özniteliğini kullanın ve değeri **extension_termsOfUseConsentVersion**talebiyle karşılaştırın. Kabul eski ise, otomatik olarak onaylanan bir ekran görüntüleyerek yeni bir kabul zorlayın. Aksi takdirde, ilke mantığını kullanarak erişimi engelleyin.

Kullanım Koşulları kabul etme koşullarını aşağıdaki senaryolar altında yakalayabilirsiniz:

- Yeni bir Kullanıcı kaydolduktan sonra. Kullanım koşulları görüntülenir ve kabul sonucu depolanır.
- Kullanıcı, en son veya etkin kullanım koşullarını önceden kabul etmiş olan oturum açma işlemi. Kullanım koşulları görüntülenmez.
- Kullanıcı, en son veya etkin kullanım koşullarını henüz kabul etmediyse oturum açmış. Kullanım koşulları görüntülenir ve kabul sonucu depolanır.
- Kullanıcı kullanım koşullarının daha eski bir sürümünü zaten kabul etmiş olan ve artık en son sürüme güncelleştirilmiş olan oturum açmış. Kullanım koşulları görüntülenir ve kabul sonucu depolanır.

Aşağıdaki görüntüde önerilen Kullanıcı akışı gösterilmektedir:

![Önerilen kabul Kullanıcı akışını gösteren akış grafiği diyagramı](./media/manage-user-access/user-flow.png)

Aşağıda, bir talepteki tarih saat temelli kullanım koşulları izni örneği verilmiştir:

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
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2098-01-30T23:03:45" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Aşağıda, bir talepteki sürüm tabanlı kullanım koşulları onayı örneği verilmiştir:

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

- Kullanıcı verilerini silme ve dışa aktarma hakkında bilgi edinmek için bkz. [Kullanıcı verilerini yönetme](manage-user-data.md).
