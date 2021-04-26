---
title: Azure Active Directory B2C içindeki kullanıcı profili öznitelikleri
description: Azure AD B2C Directory Kullanıcı profili tarafından desteklenen Kullanıcı kaynak türü öznitelikleri hakkında bilgi edinin. Yerleşik öznitelikler, Uzantılar ve özniteliklerin Microsoft Graph eşleme hakkında bilgi edinin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7dfad71d05a882e3a3941a96e12489adb5fb3234
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500538"
---
# <a name="user-profile-attributes"></a>Kullanıcı profili öznitelikleri

Azure Active Directory (Azure AD) B2C Dizin Kullanıcı profiliniz, verilen ad, soyadı, şehir, posta kodu ve telefon numarası gibi yerleşik bir öznitelik kümesi ile gelir. Bir dış veri deposu gerektirmeden kullanıcı profilini kendi uygulama verilerinize genişletebilirsiniz. 

Azure AD B2C Kullanıcı profilleriyle kullanılabilen özniteliklerin çoğu, Microsoft Graph tarafından da desteklenir. Bu makalede, Kullanıcı profili özniteliklerinin desteklenen Azure AD B2C açıklanmaktadır. Ayrıca, Microsoft Graph tarafından desteklenmeyen özniteliklerin yanı sıra Azure AD B2C ile kullanılmamalıdır Microsoft Graph özniteliklerini de not edin.

> [!IMPORTANT]
> Hesap kimlik bilgileri, kamu kimlik numaraları, kart sahibi verileri, finansal hesap verileri, sağlık bilgileri ve hassas arka plan bilgileri gibi hassas kişisel verileri depolamak için yerleşik veya uzantı öznitelikleri kullanmamalısınız.

Ayrıca, dış sistemlerle tümleştirebilirsiniz. Örneğin, kimlik doğrulaması için Azure AD B2C kullanabilir, ancak bir dış müşteri ilişki yönetimi (CRM) veya müşteri bağlılık programı veritabanını, müşteri verilerinin yetkili kaynağı olarak temsil edebilirsiniz. Daha fazla bilgi için bkz. [uzak profil](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) çözümü.

## <a name="azure-ad-user-resource-type"></a>Azure AD Kullanıcı kaynak türü

Aşağıdaki tabloda, Azure AD B2C Directory Kullanıcı profili tarafından desteklenen [Kullanıcı kaynak türü](/graph/api/resources/user) öznitelikleri listelenmiştir. Her öznitelikle ilgili aşağıdaki bilgileri verir:

- Azure AD B2C tarafından kullanılan öznitelik adı (Eğer farklıysa, parantez içinde Microsoft Graph adı)
- Öznitelik veri türü
- Öznitelik açıklaması
- Azure portal öznitelik varsa
- Öznitelik bir Kullanıcı akışında kullanılıyorsa
- Öznitelik, özel bir ilkede [Azure AD teknik profilinde](active-directory-technical-profile.md) ve bu bölümde ( &lt; ınputclaim &gt; , &lt; outputclaim &gt; veya &lt; PersistedClaims &gt; ) kullanılabilir

|Ad     |Tür     |Description|Azure portalı|Kullanıcı akışları|Özel ilke|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boole|Kullanıcı hesabının etkin mi yoksa devre dışı mı olduğunu: hesap etkinse **doğru** , aksi takdirde **yanlış** olur.|Yes|Hayır|Kalıcı, çıkış|
|ageGroup        |Dize|Kullanıcının yaş grubu. Olası değerler: null, tanımsız, küçük, Yetişkin, NotAdult.|Yes|Hayır|Kalıcı, çıkış|
|Alternativesecurityıd ([kimlikler](#identities-attribute))|Dize|Dış kimlik sağlayıcısından tek bir kullanıcı kimliği.|Hayır|Hayır|Giriş, kalıcı, çıkış|
|Alternativesecurityıds ([kimlikler](#identities-attribute))|Alternatif Securityıd koleksiyonu|Dış kimlik sağlayıcılarından Kullanıcı kimlikleri koleksiyonu.|Hayır|Hayır|Kalıcı, çıkış|
|city            |Dize|Kullanıcının bulunduğu şehir. Maksimum uzunluk 128.|Yes|Yes|Kalıcı, çıkış|
|consentProvidedForMinor|Dize|İkincil için onay sağlanmış olup olmadığı. İzin verilen değerler: null, verildi, reddedildi veya notRequired.|Yes|Hayır|Kalıcı, çıkış|
|ülke         |Dize|Kullanıcının bulunduğu ülke/bölge. Örnek: "US" veya "UK". Maksimum uzunluk 128.|Yes|Yes|Kalıcı, çıkış|
|Saati|DateTime|Kullanıcı nesnesinin oluşturulduğu tarih. Salt okunurdur.|Hayır|Hayır|Kalıcı, çıkış|
|creationType    |Dize|Kullanıcı hesabı bir Azure Active Directory B2C kiracısı için yerel hesap olarak oluşturulduysa, değer LocalAccount veya namebir. Salt okunurdur.|Hayır|Hayır|Kalıcı, çıkış|
|Tarih Ofdoğum     |Tarih|Doğum tarihi.|Hayır|Hayır|Kalıcı, çıkış|
|bölüm      |Dize|Kullanıcının çalıştığı departmanın adı. Maksimum uzunluk 64.|Yes|Hayır|Kalıcı, çıkış|
|displayName     |Dize|Kullanıcının görünen adı. Maksimum uzunluk 256.|Yes|Yes|Kalıcı, çıkış|
|facsimileTelephoneNumber<sup>1</sup>|Dize|Kullanıcının iş Faks makinesinin telefon numarası.|Yes|Hayır|Kalıcı, çıkış|
|givenName       |Dize|Kullanıcının verilen adı (ilk adı). Maksimum uzunluk 64.|Yes|Yes|Kalıcı, çıkış|
|jobTitle        |Dize|Kullanıcının iş unvanı. Maksimum uzunluk 128.|Yes|Yes|Kalıcı, çıkış|
|ImmutableID     |Dize|Genellikle şirket içi Active Directory geçirilen kullanıcılar için kullanılan bir tanımlayıcı.|Hayır|Hayır|Kalıcı, çıkış|
|legalAgeGroupClassification|Dize|Yasal yaş grubu sınıflandırması. Bir salt okunurdur ve ageGroup ve consentProvidedForMinor özellikleri temel alınarak hesaplanır. İzin verilen değerler: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult ve yetişkin.|Yes|Hayır|Kalıcı, çıkış|
|legalCountry<sup>1</sup>  |Dize|Yasal amaçlar için ülke/bölge.|Hayır|Hayır|Kalıcı, çıkış|
|posta            |Dize|Kullanıcının SMTP adresi (örneğin, " bob@contoso.com "). Salt okunur.|Hayır|Hayır|Kalıcı, çıkış|
|mailNickName    |Dize|Kullanıcının posta diğer adı. Maksimum uzunluk 64.|Hayır|Hayır|Kalıcı, çıkış|
|Mobil (mobilePhone) |Dize|Kullanıcının birincil cep telefonu numarası. Maksimum uzunluk 64.|Yes|Hayır|Kalıcı, çıkış|
|NetID           |Dize|Ağ KIMLIĞI.|Hayır|Hayır|Kalıcı, çıkış|
|objectId        |Dize|Kullanıcının benzersiz tanımlayıcısı olan bir genel benzersiz tanımlayıcı (GUID). Örnek: 12345678-9ABC-def0-1234-56789abcde. Salt okuma, sabit.|Salt okunur|Yes|Giriş, kalıcı, çıkış|
|Diğer postalar      |Dize koleksiyonu|Kullanıcı için diğer e-posta adreslerinin listesi. Örnek: [" bob@contoso.com ", " Robert@fabrikam.com "].|Evet (alternatif e-posta)|No|Kalıcı, çıkış|
|password        |Dize|Kullanıcı oluşturma sırasında yerel hesabın parolası.|Hayır|Hayır|Kalıcı|
|passwordPolicies     |Dize|Parolanın ilkesi. Bu, virgülle ayrılmış farklı ilke adından oluşan bir dizedir. Örneğin, "Disablepasswordexpiasyon, DisableStrongPassword".|Hayır|Hayır|Kalıcı, çıkış|
|physicalDeliveryOfficeName (officeLocation)|Dize|Kullanıcının iş yerinde Office konumu. Maksimum uzunluk 128.|Yes|Hayır|Kalıcı, çıkış|
|postalCode      |Dize|Kullanıcının posta adresi için posta kodu. Posta kodu kullanıcının ülkesine/bölgesine özeldir. America Birleşik Devletler, bu öznitelik ZIP kodunu içerir. Maksimum Uzunluk 40.|Yes|Hayır|Kalıcı, çıkış|
|preferredLanguage    |Dize|Kullanıcı için tercih edilen dil. Tercih edilen dil biçimi, RFC 4646 ' i temel alır. Ad, dille ilişkili ISO 639 2 harfli küçük harfli bir kültür kodu ve ülke veya bölge ile ilişkili bir ISO 3166 2-Letter büyük harfli alt kültür kodu birleşimidir. Örnek: "en-US" veya "ES-ES".|Hayır|Hayır|Kalıcı, çıkış|
|refreshTokensValidFromDateTime (Signınsessionsvalidfromdatetime)|DateTime|Bu saatten önce verilen yenileme belirteçleri geçersiz ve uygulamalar, yeni bir erişim belirteci almak için geçersiz yenileme belirteci kullanırken bir hata alır. Bu durumda, uygulamanın yetkilendirme uç noktasına istek yaparak yeni bir yenileme belirteci edinmesi gerekir. Salt okunur.|Hayır|Hayır|Çıktı|
|Signınnames ([kimlikler](#identities-attribute)) |Dize|Dizindeki herhangi bir türdeki yerel hesap kullanıcısının benzersiz oturum açma adı. Yerel hesap türünü belirtmeden oturum açma değeri olan bir kullanıcıyı almak için bu özniteliği kullanın.|Hayır|Hayır|Giriş|
|Signınnames. userName ([kimlikler](#identities-attribute)) |Dize|Dizindeki yerel hesap kullanıcısının benzersiz Kullanıcı adı. Bu özniteliği, belirli bir oturum açma Kullanıcı adı ile Kullanıcı oluşturmak veya almak için kullanın. Bunu Patch işlemi sırasında PersistedClaims içinde belirtmek, diğer oturum adı türlerini kaldırır. Yeni bir Signınnames türü eklemek istiyorsanız, mevcut Signınnames 'leri de kalıcı hale getirmeniz gerekir.|Hayır|Hayır|Giriş, kalıcı, çıkış|
|Signınnames. phoneNumber ([kimlikler](#identities-attribute)) |Dize|Dizindeki yerel hesap kullanıcısının benzersiz telefon numarası. Bu özniteliği, belirli bir oturum açma telefon numarası olan bir kullanıcı oluşturmak veya almak için kullanın. Bu özniteliğin, düzeltme eki sırasında tek başına PersistedClaims içinde belirtilmesi, diğer Signınnames türlerini kaldırır. Yeni bir Signınnames türü eklemek istiyorsanız, mevcut Signınnames 'leri de kalıcı hale getirmeniz gerekir.|Hayır|Hayır|Giriş, kalıcı, çıkış|
|Signınnames. Emaadresi ([kimlikler](#identities-attribute))|Dize|Dizindeki yerel hesap kullanıcısının benzersiz e-posta adresi. Belirli bir oturum açma e-posta adresine sahip bir kullanıcı oluşturmak veya almak için bunu kullanın. Bu özniteliğin, düzeltme eki sırasında tek başına PersistedClaims içinde belirtilmesi, diğer Signınnames türlerini kaldırır. Yeni bir Signınnames türü eklemek istiyorsanız, mevcut Signınnames 'leri de kalıcı hale getirmeniz gerekir.|Hayır|Hayır|Giriş, kalıcı, çıkış|
|state           |Dize|Kullanıcının adresindeki eyalet veya bölge. Maksimum uzunluk 128.|Yes|Yes|Kalıcı, çıkış|
|streetAddress   |Dize|Kullanıcının iş yeri için cadde adresi. Maksimum uzunluk 1024.|Yes|Yes|Kalıcı, çıkış|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|Dize|Kullanıcının çok faktörlü kimlik doğrulaması için kullandığı ikincil telefon numarası.|Yes|Hayır|Kalıcı, çıkış|
|Strongauthenticationemaadresi<sup>1</sup>|Dize|Kullanıcının SMTP adresi. Örnek: " bob@contoso.com " Bu öznitelik, kullanıcı e-posta adresini depolamak için Kullanıcı adı ilkesi ile oturum açma için kullanılır. Daha sonra bir parola sıfırlama akışında kullanılan e-posta adresi.|Yes|Hayır|Kalıcı, çıkış|
|strongAuthenticationPhoneNumber<sup>2</sup>|Dize|Kullanıcının çok faktörlü kimlik doğrulaması için kullanılan birincil telefon numarası.|Yes|Hayır|Kalıcı, çıkış|
|surname         |Dize|Kullanıcının Soyadı (aile adı veya soyadı). Maksimum uzunluk 64.|Yes|Yes|Kalıcı, çıkış|
|telephoneNumber (ilk departman girişi)|Dize|Kullanıcının iş yeri için birincil telefon numarası.|Yes|Hayır|Kalıcı, çıkış|
|userPrincipalName    |Dize|Kullanıcının kullanıcı asıl adı (UPN). UPN, Internet standart RFC 822 ' i temel alan Kullanıcı için Internet stili bir oturum açma adıdır. Etki alanının, kiracının doğrulanmış etki alanları koleksiyonunda mevcut olması gerekir. Bir hesap oluşturulduğunda bu özellik gereklidir. Değişmez.|Hayır|Hayır|Giriş, kalıcı, çıkış|
|usageLocation   |Dize|Ülkelerde/bölgelerde hizmetlerin kullanılabilirliğini kontrol etmek için yasal gereksinim nedeniyle lisanslar atanacak kullanıcılar için gereklidir. Null yapılabilir değil. İki harfli ülke/bölge kodu (ISO standart 3166). Örnekler: "US", "JP" ve "GB".|Yes|Hayır|Kalıcı, çıkış|
|userType        |Dize|Dizininizdeki Kullanıcı türlerini sınıflandırmak için kullanılabilen bir dize değeri. Değer üye olmalıdır. Salt okunur.|Salt okunur|No|Kalıcı, çıkış|
|userState (externalUserState)<sup>3</sup>|Dize|Yalnızca Azure AD B2B hesabı için, davetin Pendingkabulünü veya kabul edilip edilmediğini belirtir.|Hayır|Hayır|Kalıcı, çıkış|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|UserState özelliği için en son değişikliğin zaman damgasını gösterir.|Hayır|Hayır|Kalıcı, çıkış|

<sup>1 </sup> Microsoft Graph tarafından desteklenmiyor<br><sup>2 </sup> Daha fazla bilgi için bkz. [MFA telefon numarası özniteliği](#mfa-phone-number-attribute)<br><sup>3 </sup> Azure AD B2C ile kullanılmamalıdır

## <a name="display-name-attribute"></a>Görünen ad özniteliği

, `displayName` Kullanıcı için Azure Portal Kullanıcı yönetiminde görüntülenecek addır ve erişim belirteci Azure AD B2C uygulamaya geri döner. Bu özellik gereklidir.

## <a name="identities-attribute"></a>Kimlikler özniteliği

Bir tüketici, iş ortağı veya vatandaşlık olabilecek bir müşteri hesabı şu kimlik türleriyle ilişkilendirilebilir:

- **Yerel** kimlik-Kullanıcı adı ve parola Azure AD B2C dizininde yerel olarak depolanır. Genellikle bu kimliklere "yerel hesaplar" olarak başvurduk.
- **Federasyon** kimliği- *sosyal* veya *Kurumsal* hesaplar olarak da bilinen kullanıcı KIMLIĞI, Facebook, Microsoft, ADFS veya Salesforce gibi bir federal kimlik sağlayıcısı tarafından yönetilir.

Müşteri hesabı olan bir Kullanıcı birden çok kimlik ile oturum açabilir. Örneğin, Kullanıcı adı, e-posta, çalışan KIMLIĞI, kamu KIMLIĞI ve diğerleri. Tek bir hesabın aynı parolayla hem yerel hem de sosyal birden çok kimliği olabilir. 

Microsoft Graph API 'sinde, hem yerel hem de Federasyon kimlikleri, `identities` [Objectıdentity](/graph/api/resources/objectidentity)türünde olan User özniteliğinde depolanır. `identities`Koleksiyon, bir kullanıcı hesabında oturum açmak için kullanılan bir kimlik kümesini temsil eder. Bu koleksiyon, kullanıcının Kullanıcı hesabında ilişkili kimliklerinden herhangi biriyle oturum açmasını sağlar. Kimlikler özniteliği en fazla on [Objectıdentity](/graph/api/resources/objectidentity) nesnesi içerebilir. Her nesne aşağıdaki özellikleri içerir:

| Ad   | Tür |Description|
|:---------------|:--------|:----------|
|Signıntype|string| Dizininizdeki Kullanıcı oturum açma türlerini belirtir. Yerel hesap için:,,,,  `emailAddress` `emailAddress1` `emailAddress2` `emailAddress3`  `userName` veya istediğiniz diğer herhangi bir tür. Sosyal hesabın olarak ayarlanması gerekir  `federated` .|
|yayınlayan|string|Kimliğin verenini belirtir. Yerel hesaplar için ( **Signıntype** değil `federated` ), bu özellik yerel B2C kiracısı varsayılan etki alanı adıdır (örneğin,) `contoso.onmicrosoft.com` . Sosyal kimlik (burada **Signıntype** ) için  `federated` değer verenin adıdır, örneğin `facebook.com`|
|ıssueratandıd|string|Kullanıcıya veren tarafından atanan benzersiz tanımlayıcıyı belirtir. **Issuer** ve **ıssueratanmadı** birleşimi kiracınız dahilinde benzersiz olmalıdır. Yerel hesap için, **Signıntype** veya olarak ayarlandığında `emailAddress` `userName` , Kullanıcı için oturum açma adını temsil eder.<br>**Signıntype** şu şekilde ayarlandığında: <ul><li>`emailAddress` (veya `emailAddress` benzer şekilde başlıyor `emailAddress1` ) **ıssueratandıd** geçerli bir e-posta adresi olmalıdır</li><li>`userName`(veya başka bir değer), **ıssueratandıd** [bir e-posta adresinin geçerli bir yerel parçası](https://tools.ietf.org/html/rfc3696#section-3) olmalıdır</li><li>`federated`, **ıssueratanmadı** Federal hesap benzersiz tanımlayıcısını temsil ediyor</li></ul>|

Aşağıdaki **kimlikler** özniteliği, oturum açma adı ile yerel hesap kimliği, oturum açma olarak bir e-posta adresi ve sosyal kimlik ile birlikte. 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

Federal kimlikler için, kimlik sağlayıcısına bağlı olarak, **ıssueratandıd** , uygulama veya geliştirme hesabı başına belirli bir kullanıcı için benzersiz bir değerdir. Azure AD B2C ilkesini, daha önce sosyal sağlayıcı veya aynı geliştirme hesabı içindeki başka bir uygulama tarafından atanan aynı uygulama KIMLIĞIYLE yapılandırın. 

## <a name="password-profile-property"></a>Parola profili özelliği

Yerel bir kimlik için **Passwordprofile** özniteliği gereklidir ve kullanıcının parolasını içerir. `forceChangePasswordNextSignIn`Özniteliği, bir kullanıcının bir sonraki oturum açma sırasında parolayı sıfırlaması gerekip gerekmediğini gösterir. Zorunlu parola sıfırlamayı işlemek için [zorlanan parola sıfırlama akışı ayarlayın](force-password-reset.md).

Bir federal (sosyal) kimlik için **Passwordprofile** özniteliği gerekli değildir.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>Parola İlkesi özniteliği

Azure AD B2C parola ilkesi (yerel hesaplar için) Azure Active Directory [güçlü parola gücü](../active-directory/authentication/concept-sspr-policy.md) ilkesini temel alır. Azure AD B2C kaydolma veya oturum açma ve parola sıfırlama ilkeleri için bu güçlü parola gücü gerekir ve parolaların süreleri dolmaz.

Kullanıcı geçişi senaryolarında, geçirmek istediğiniz hesapların Azure AD B2C tarafından zorlanan [güçlü parola kuvvetinden](../active-directory/authentication/concept-sspr-policy.md) daha zayıf olması halinde güçlü parola gereksinimini devre dışı bırakabilirsiniz. Varsayılan parola ilkesini değiştirmek için `passwordPolicies` özniteliğini olarak ayarlayın `DisableStrongPassword` . Örneğin, kullanıcı oluştur isteğini aşağıdaki şekilde değiştirebilirsiniz:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>MFA telefon numarası özniteliği

Multi-Factor Authentication (MFA) için bir telefon kullanırken, mobil telefon kullanıcı kimliğini doğrulamak için kullanılır. Telefon numarasını program aracılığıyla, [güncelleştirmeyi](/graph/api/b2cauthenticationmethodspolicy-update), [almayı](/graph/api/b2cauthenticationmethodspolicy-get)veya [silmeyi](/graph/api/phoneauthenticationmethod-delete) yeni BIR telefon numarası [eklemek](/graph/api/authentication-post-phonemethods) için, MS Graph API [Phone kimlik doğrulama yöntemi](/graph/api/resources/phoneauthenticationmethod)kullanın.

[Özel ilkeler](custom-policy-overview.md)Azure AD B2C, telefon numarası `strongAuthenticationPhoneNumber` talep türü aracılığıyla kullanılabilir.

## <a name="extension-attributes"></a>Uzantı öznitelikleri

Müşterilere yönelik her uygulamanın, toplanacak bilgiler için benzersiz gereksinimleri vardır. Azure AD B2C kiracınız, belirtilen ad, soyadı ve posta kodu gibi özelliklerde depolanan yerleşik bir bilgi kümesiyle gelir. Azure AD B2C, her müşteri hesabında depolanan özellikler kümesini genişletebilirsiniz. Daha fazla bilgi için bkz. [Kullanıcı özniteliklerini ekleme ve Kullanıcı girişini özelleştirme Azure Active Directory B2C](configure-user-input.md)

Uzantı öznitelikleri, dizindeki Kullanıcı nesnelerinin [şemasını genişletir](/graph/extensibility-overview#schema-extensions) . Uzantı öznitelikleri, bir kullanıcı için veri içerse de, yalnızca bir uygulama nesnesine kaydedilebilir. Extension özniteliği adlı uygulamaya iliştirilir `b2c-extensions-app` . Bu uygulamayı, Kullanıcı verilerini depolamak için Azure AD B2C tarafından kullanıldığından değiştirmeyin. Bu uygulamayı, Azure Active Directory Uygulama kayıtları altında bulabilirsiniz.

> [!NOTE]
> - En fazla 100 uzantı özniteliği herhangi bir kullanıcı hesabına yazılabilir.
> - B2C-Extensions-App uygulaması silinirse, bu uzantı öznitelikleri içerdikleri tüm verilerle birlikte tüm kullanıcılardan kaldırılır.
> - Bir uzantı özniteliği uygulama tarafından silinirse, tüm kullanıcı hesaplarından kaldırılır ve değerler silinir.

Graph API uzantı öznitelikleri, yöntemi kullanılarak adlandırılır `extension_ApplicationClientID_AttributeName` ; burada, `ApplicationClientID` uygulamanın uygulamanın **(istemci) kimliğidir** `b2c-extensions-app` (   >  Azure Portal **tüm uygulamalarda** uygulama kayıtları bulunur). Uzantı öznitelik adında temsil edilen **uygulama (istemci) kimliğinin** hiçbir tire içerdiğini unutmayın. Örnek:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Aşağıdaki veri türleri bir şema uzantısında öznitelik tanımlarken desteklenir:

|Tür |Açıklamalar  |
|--------------|---------|
|Boole    | Olası değerler: **true** veya **false**. |
|DateTime   | ISO 8601 biçiminde belirtilmelidir. UTC olarak saklanacaktır.   |
|Tamsayı    | 32 bitlik değer.               |
|Dize     | en fazla 256 karakter.     |

## <a name="next-steps"></a>Sonraki adımlar

Uzantı öznitelikleri hakkında daha fazla bilgi edinin:

- [Şema uzantıları](/graph/extensibility-overview#schema-extensions)
- [Özel öznitelikleri tanımlama](user-flow-custom-attributes.md)
