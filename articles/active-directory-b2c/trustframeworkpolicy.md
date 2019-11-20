---
title: TrustFrameworkPolicy-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C özel bir ilkenin TrustFrameworkPolicy öğesini belirtin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 673807377914aabad5b90d1ac2ecc16623870d30
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063365"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Özel bir ilke, bir veya daha fazla XML biçimli dosya olarak temsil edilir ve hiyerarşik bir zincirde birbirini ifade eder. XML öğeleri, ilke için talepler şeması, talep dönüştürmeleri, içerik tanımları, talep sağlayıcılar, teknik profiller, Kullanıcı yolculuğu ve düzenleme adımları gibi öğeleri tanımlar. Her ilke dosyası, bir ilke dosyasının en üst düzey **TrustFrameworkPolicy** öğesi içinde tanımlanır.

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


**TrustFrameworkPolicy** öğesi aşağıdaki öznitelikleri içerir:

| Öznitelik | Gerekli | Açıklama |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Evet | İlkeyi yürütmek için kullanılacak şema sürümü. Değer şu şekilde olmalıdır`0.3.0.0` |
| Tenantobjectıd | Hayır | Azure Active Directory B2C (Azure AD B2C) kiracının benzersiz nesne tanımlayıcısı. |
| TenantId | Evet | Bu ilkenin ait olduğu kiracının benzersiz tanımlayıcısı. |
| `PolicyId` | Evet | İlke için benzersiz tanımlayıcı. Bu tanımlayıcının önüne *B2C_1A_* gelmelidir |
| PublicPolicyUri | Evet | İlke için kiracı KIMLIĞI ve ilke KIMLIĞI birleşimi olan URI. |
| DeploymentMode | Hayır | Olası değerler: `Production`, `Debugging`, veya `Development`. `Production` varsayılan değerdir. İlkenizde hata ayıklamak için bu özelliği kullanın. Daha fazla bilgi için bkz. [günlükleri toplama](active-directory-b2c-troubleshoot-custom.md). |
| Kullanıcıbağlantısı Neyırecorderendpoint | Hayır | **DeploymentMode** olarak `Development`ayarlandığında kullanılan uç nokta. Değer olmalıdır `urn:journeyrecorder:applicationinsights`. Daha fazla bilgi için bkz. [günlükleri toplama](active-directory-b2c-troubleshoot-custom.md). |


Aşağıdaki örnek, **TrustFrameworkPolicy** öğesinin nasıl kullanılacağını gösterir:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

## <a name="inheritance-model"></a>Devralma modeli

Bu ilke dosyası türleri genellikle Kullanıcı yolculuğunda kullanılır:

- Tanımlarının çoğunu içeren bir **temel** dosya. İlkeleriniz için sorun giderme ve uzun süreli bakımla yardımcı olmak üzere bu dosyada en az sayıda değişiklik yapmanız önerilir.
- Kiracınız için benzersiz yapılandırma değişikliklerini tutan bir **uzantı** dosyası. Bu ilke dosyası temel dosyadan türetilir. Yeni işlevsellik eklemek veya var olan işlevleri geçersiz kılmak için bu dosyayı kullanın. Örneğin, bu dosyayı yeni kimlik sağlayıcılarıyla federasyona eklemek için kullanın.
- Web, mobil veya masaüstü uygulamalarınız gibi bağlı olan taraf uygulaması tarafından doğrudan çağrılan tek bir görev odaklı dosya olan bir **bağlı olan taraf (RP)** dosyası. Kaydolma veya oturum açma, parola sıfırlama veya profil düzenleme gibi her benzersiz görev kendi RP ilke dosyası gerektirir. Bu ilke dosyası, uzantılar dosyasından türetilir.

Bağlı olan taraf uygulaması, belirli bir görevi yürütmek için RP ilke dosyasını çağırır. Örneğin, oturum açma akışını başlatmak için. Azure AD B2C ' deki kimlik deneyimi çerçevesi, ilk olarak tüm öğeleri temel dosyadan, ardından uzantı dosyasından ve son olarak geçerli ilkeyi birleştirmek için RP ilke dosyasından ekler. RP dosyasındaki aynı tür ve ad öğeleri, Uzantılardaki öğeleri geçersiz kılar ve uzantılar temeli geçersiz kılar. Aşağıdaki diyagramda, ilke dosyaları ve bağlı olan taraf uygulamaları arasındaki ilişki gösterilmektedir.

![Güven çerçevesi ilke devralma modelini gösteren diyagram](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Devralma modeli aşağıdaki gibidir:

- Üst ilke ve alt ilke aynı şemadır.
- Herhangi bir düzeydeki alt ilke üst ilkeden devralınabilir ve yeni öğeler ekleyerek genişletebilirler.
- Düzey sayısı için bir sınır yoktur.

Daha fazla bilgi için bkz. [özel ilkelerle çalışmaya başlama](active-directory-b2c-get-started-custom.md).

## <a name="base-policy"></a>Temel ilke

Bir ilkeyi başka bir ilkeden devralması için bir **Basepolicy** öğesi, Ilke dosyasının **TrustFrameworkPolicy** öğesi altında bildirilmelidir. **Basepolicy** öğesi, bu ilkenin türetildiği temel ilkeye bir başvurudur.

**Basepolicy** öğesi aşağıdaki öğeleri içerir:

| Öğe | Oluşumlar | Açıklama |
| ------- | ----------- | --------|
| TenantId | 1:1 | Azure AD B2C kiracınızın tanımlayıcısı. |
| `PolicyId` | 1:1 | Üst ilke tanımlayıcısı. |


Aşağıdaki örnek, temel bir ilkenin nasıl ekleneceğini gösterir. Bu **B2C_1A_TrustFrameworkExtensions** ilkesi **B2C_1A_TrustFrameworkBase** ilkesinden türetilir.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

## <a name="policy-execution"></a>İlke yürütme

Web, mobil veya masaüstü uygulaması gibi bir bağlı olan taraf uygulaması, [bağlı olan taraf (RP) ilkesini](relyingparty.md)çağırır. RP ilke dosyası, oturum açma, parola sıfırlama veya bir profili düzenlemeyle ilgili belirli bir görevi yürütür. RP ilkesi, bağlı olan taraf uygulamanın verilen belirtecin bir parçası olarak aldığı taleplerin listesini yapılandırır. Birden çok uygulama aynı ilkeyi kullanabilir. Tüm uygulamalar taleplerle aynı belirteci alır ve Kullanıcı aynı kullanıcı yolculuğuna gider. Tek bir uygulama, birden çok ilke kullanabilir.

RP ilke dosyasında, [Kullanıcı yolculuğuna](userjourneys.md)Işaret eden **defaultuseryolculuney** öğesini belirtirsiniz. Kullanıcı yolculuğu genellikle temel veya uzantılar ilkesinde tanımlanmıştır.

B2C_1A_signup_signin ilkesi:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase veya B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Kullanıcı yolculuğu, kullanıcının ne kadar ilerreceğini belirleyen iş mantığını tanımlar. Her Kullanıcı yolculuğu, kimlik doğrulaması ve bilgi toplama açısından sırayla bir dizi eylem gerçekleştiren bir düzenleme adımları kümesidir.

[Başlangıç paketindeki](active-directory-b2c-get-started-custom.md#custom-policy-starter-pack) **SocialAndLocalAccounts** Ilke dosyası Signuporsignın, profileedit, passwordreset Kullanıcı bir bağlantı içerir. E-posta adresini değiştirme veya sosyal hesabın bağlantısını kaldırma gibi diğer senaryolar için daha fazla kullanıcı bağlantısı ekleyebilirsiniz.

Düzenleme adımları [Teknik bir profil](technicalprofiles.md)çağırabilir. Teknik bir profil, farklı türlerde taraflar ile iletişim kurmak için yerleşik mekanizmaya sahip bir çerçeve sağlar. Örneğin, teknik bir profil, diğerleri arasında bu eylemleri gerçekleştirebilir:

- Kullanıcı deneyimini işleme.
- Kullanıcıların Facebook, Microsoft hesabı, Google, Salesforce veya başka herhangi bir kimlik sağlayıcısı gibi sosyal veya kurumsal bir hesapla oturum açmalarına izin verin.
- MFA için telefon doğrulamasını ayarlayın.
- Azure AD B2C bir kimlik deposundan veri okuma ve yazma.
- Özel bir yeniden deneme API hizmeti çağırın.

![İlke yürütme akışını gösteren diyagram](./media/trustframeworkpolicy/custom-policy-execution.png)

 **TrustFrameworkPolicy** öğesi aşağıdaki öğeleri içerir:

- Yukarıda belirtildiği gibi BasePolicy
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [Kullanıcı, neys](userjourneys.md)
- [RelyingParty](relyingparty.md)
