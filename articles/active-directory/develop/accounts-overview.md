---
title: Microsoft Identity platform hesapları ve kiracı profilleri (Android) | Mavisi
description: Android için Microsoft Identity platform hesaplarına genel bakış
services: active-directory
documentationcenter: ''
author: shoatman
manager: nadima
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7beab6759524037f86c83429644c1bb1fffe4d07
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679847"
---
# <a name="accounts--tenant-profiles-android"></a>Hesap & kiracı profilleri (Android)

Bu makalede, `account` ' ın Microsoft Identity platformunda ne olduğuna ilişkin bir genel bakış sunulmaktadır.

Microsoft kimlik doğrulama kitaplığı (MSAL) API 'SI, *Kullanıcı* terimi *hesabını hesaba*koyar. Bir nedeni, bir kullanıcının (insan veya yazılım Aracısı) birden çok hesabı olabileceği ya da kullanabileceği bir nedendir. Bu hesaplar kullanıcının kendi kuruluşunda ve/veya kullanıcının üyesi olduğu diğer kuruluşlarda yer alabilir.

Microsoft Identity platformunda bir hesap aşağıdakilerden oluşur:

  - Benzersiz bir tanımlayıcı.
  - Hesabın sahipliğini/denetimini göstermek için kullanılan bir veya daha fazla kimlik bilgisi.
  - Öznitelikleri içeren bir veya daha fazla profil:
    - Resim, verilen ad, Aile adı, başlık, ofis konumu
- Hesap, bir yetki kaynağına veya kayıt sistemine sahiptir. Bu, hesabın oluşturulduğu ve bu hesapla ilişkili kimlik bilgilerinin depolandığı sistemdir. Microsoft Identity platformu gibi çok kiracılı sistemlerde kayıt sistemi, hesabın oluşturulduğu `tenant` ' dır. Bu kiracıya `home tenant` de denir.
- Microsoft Identity platform 'daki hesaplara aşağıdaki kayıt sistemleri sahiptir:
  - Azure Active Directory B2C dahil Azure Active Directory.
  - Microsoft hesabı (canlı).
- Microsoft Identity platformu dışındaki kayıt sistemlerinden gelen hesaplar, aşağıdakiler de dahil olmak üzere Microsoft Identity platformu dahilinde temsil edilir:
  - bağlı şirket içi dizinlerden kimlikler (Windows Server Active Directory)
  - LinkedIn, GitHub vb. kaynaklı dış kimlikler.
  Bu durumlarda, bir hesabın hem kaynak sistemi hem de Microsoft Identity platformu içindeki bir kayıt sistemi vardır.
- Microsoft Identity platformu, birden fazla kuruluşa (Azure Active Directory kiracılar) ait kaynaklara erişmek için bir hesabın kullanılmasına izin verir.
  - Bir kayıt sisteminden (AAD kiracı A) bir hesabın, başka bir kayıt sistemindeki (AAD kiracısı B) bir kaynağa erişimi olduğunu kaydetmek için, hesabın, kaynağın tanımlandığı kiracıda temsil edilebilmesi gerekir. Bu, sistem B 'deki sistem A 'dan hesabın yerel bir kaydı oluşturularak yapılır.
  - Hesabın temsili olan bu yerel kayıt, özgün hesaba bağlanır.
  - MSAL bu yerel kaydı `Tenant Profile` olarak gösterir.
  - Kiracı profili, Iş unvanı, ofis konumu, Iletişim bilgileri vb. gibi yerel bağlamla ilgili farklı özniteliklere sahip olabilir.
 
- Bir hesap bir veya daha fazla kiracıda mevcut olabileceğinden, hesap birden fazla profile sahip olabilir.

> [!NOTE]
> MSAL, Microsoft hesabı sistemine (canlı, MSA) Microsoft Identity platform içinde başka bir kiracı olarak davranır. Microsoft hesabı kiracının Kiracı kimliği: `9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Hesaba genel bakış Diyagramı

![Hesaba genel bakış Diyagramı](./media/accounts-overview/accounts-overview.png)

Yukarıdaki diyagramda:

- @No__t-0 hesabı şirket içi Windows Server Active Directory (kaynak şirket içi kayıt sisteminde) oluşturulur.
- @No__t-0 hesabı Microsoft hesabı kiracısında oluşturulur.
- `bob@contoso.com` ' ın aşağıdaki Azure Active Directory kiracılarında en az bir kaynağa erişimi vardır:
  - contoso.com (kaydın bulut sistemi-şirket içi kayıt sistemine bağlı)
  - fabrikam.com
  - woodgrovebank.com
  - Bu kiracıların her birinde `bob@contoso.com` için bir kiracı profili bulunur.
- `tom@live.com` ' ın aşağıdaki Microsoft kiracılarındaki kaynaklara erişimi vardır:
  - contoso.com
  - fabrikam.com
  - Bu kiracıların her birinde `tom@live.com` için bir kiracı profili bulunur.
- Diğer kiracılardaki Tom ve Bob hakkındaki bilgiler, kayıt sisteminde farklılık gösterebilir. Bunlar, Iş unvanı, ofis konumu vb. gibi özniteliklere göre farklılık gösterebilir. Gruplar ve/veya rollerinin üyeleri (Azure Active Directory kiracısı) olabilir. Bu bilgilere bob@contoso.com kiracı profili olarak değintik.

Diyagramda bob@contoso.com ve tom@live.com farklı Azure Active Directory kiracılarındaki kaynaklara erişebilir. Daha fazla bilgi için [Azure portal Azure ACTIVE DIRECTORY B2B işbirliği kullanıcıları ekleme](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)bölümüne bakın.

## <a name="accounts-and-single-sign-on-sso"></a>Hesaplar ve çoklu oturum açma (SSO)

MSAL belirteç önbelleği, hesap başına *tek bir yenileme belirteci* depolar. Bu yenileme belirteci, birden çok Microsoft kimlik platformu kiracısından sessizce erişim belirteçleri istemek için kullanılabilir. Bir aracı bir cihaza yüklendiğinde, hesap aracı tarafından yönetilir ve cihaz genelinde çoklu oturum açma mümkün olur.

> [!IMPORTANT]
> İşletmeden müşteriye (B2C) hesabı ve yenileme belirteci davranışı, Microsoft Identity platformunun geri kalanından farklıdır. Daha fazla bilgi için bkz. [B2C ilkeleri & hesapları](#b2c-policies--accounts).

## <a name="account-identifiers"></a>Hesap tanımlayıcıları

MSAL hesap KIMLIĞI, bir hesap nesne KIMLIĞI değil. Microsoft Identity platform içinde benzersizlik dışında herhangi bir şeyi iletmek için ayrıştırılıp/veya bir şekilde güvenilmez.

Azure AD kimlik doğrulama kitaplığı (ADAL) ile uyumluluk ve ADAL 'den MSAL 'e geçiş kolaylığı sağlamak için MSAL, MSAL önbelleğinde bulunan hesap için geçerli bir tanımlayıcı kullanarak hesapları arayabilir.  Örneğin, her tanımlayıcı geçerli olduğundan, aşağıdakiler tom@live.com için aynı hesap nesnesini alır:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Hesap hakkındaki taleplere erişme

Bir erişim belirteci istemenin yanı sıra, MSAL her bir kiracıdan bir KIMLIK belirteci de ister. Bunu her zaman aşağıdaki kapsamları isteyerek yapar:

- OpenID
- Profilinizi

KIMLIK belirteci, taleplerin bir listesini içerir. `Claims`, hesapla ilgili ad/değer çiftleridir ve isteği yapmak için kullanılır.

Daha önce belirtildiği gibi, bir hesabın mevcut olduğu her kiracı, hesap hakkında: iş unvanı, ofis konumu vb. dahil olmak üzere farklı bilgileri depolayabileceği, ancak bunlarla sınırlı olmamak üzere.

Bir hesap birden fazla kuruluşta üye veya Konuk olabileceğinden, MSAL, hesabın üyesi olduğu kiracıların bir listesini almak üzere bir hizmeti sorgulayamaz. Bunun yerine, MSAL, yapılmış olan belirteç isteklerinin bir sonucu olarak hesabın bulunduğu kiracıların bir listesini oluşturur.

Hesap nesnesi üzerinde sunulan talepler her zaman bir hesap için ' ana kiracı '/{Authority} ' deki taleplerdir. Bu hesap, ana kiracının bir belirtecini istemek için kullanılmıyorsa, MSAL hesap nesnesi aracılığıyla talep sağlayamaz.  Örneğin:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Hesap nesnesinden kullanılabilen taleplerin listesini görmek için [bir id_token içindeki talepler](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token) bölümüne bakın

> [!TIP]
> İd_token verilerinize ek talepler eklemek için, [nasıl yapılır: Azure AD uygulamanıza isteğe bağlı talepler sağlama](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) bölümündeki isteğe bağlı talep belgelerine bakın

### <a name="access-tenant-profile-claims"></a>Kiracı profili taleplerine erişin

Bir hesap hakkındaki taleplerde diğer kiracılarda göründükleri gibi erişmek için, önce hesap nesneniz `IMultiTenantAccount` ' a atamalısınız. Tüm hesaplar çok kiracılı olabilir, ancak MSAL aracılığıyla kullanılabilir kiracı profillerinin sayısı, geçerli hesabı kullanarak belirteçleri istediğiniz kiracılara göre belirlenir.  Örneğin:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>B2C ilkeleri & hesapları

Hesap için belirteçleri Yenile, B2C ilkeleri arasında paylaşılmaz. Sonuç olarak, belirteçler kullanan çoklu oturum açma mümkün değildir. Bunun anlamı çoklu oturum açma mümkün değildir. Çoklu oturum açma 'nın çoklu oturum açmayı etkinleştirmek için bir tanımlama bilgisinin kullanılabildiği etkileşimli bir deneyim kullanması gerektiği anlamına gelir.

Bu Ayrıca, MSAL durumunda farklı B2C ilkeleri kullanarak belirteçler elde ediyorsanız, bunların her biri kendi tanımlayıcısına sahip ayrı hesaplar olarak değerlendirilir. @No__t-0 ' ı kullanarak belirteç istemek için bir hesap kullanmak istiyorsanız, belirteç isteğiyle kullandığınız ilkeyle eşleşen hesaplar listesinden hesabı seçmeniz gerekir. Örneğin:

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```
