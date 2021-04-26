---
title: Microsoft Identity platform kapsamları, izinler & izin
description: Kapsamlar, izinler ve onay dahil olmak üzere Microsoft Identity platform uç noktasındaki yetkilendirme hakkında bilgi edinin.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur, marsma
ms.custom: aaddev, fasttrack-edit, contperf-fy21q1, identityplatformtop40
ms.openlocfilehash: 2a975a0aba06ecfd010fe328ef6c8cda75290f2b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515592"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform"></a>Microsoft Identity platformunda izinler ve onay

Microsoft Identity platformu ile tümleştirilen uygulamalar, kullanıcılara ve yöneticilere verilere nasıl erişilebileceği üzerinde denetim sağlayan bir yetkilendirme modeli izler. Yetkilendirme modelinin uygulanması Microsoft Identity platformunda güncelleştirildi. Uygulamanın Microsoft Identity platformu ile nasıl etkileşim kurması gerektiğini değiştirir. Bu makalede kapsamlar, izinler ve onay dahil olmak üzere bu yetkilendirme modelinin temel kavramları ele alınmaktadır.

## <a name="scopes-and-permissions"></a>Kapsamlar ve izinler

Microsoft Identity platformu, [OAuth 2,0](active-directory-v2-protocols.md) yetkilendirme protokolünü uygular. OAuth 2,0, bir üçüncü taraf uygulamanın bir kullanıcı adına Web 'de barındırılan kaynaklara erişebileceği bir yöntemdir. Microsoft Identity platformu ile tümleştirilen Web 'de barındırılan herhangi bir kaynağın bir kaynak tanımlayıcısı veya *uygulama kimliği URI 'si* vardır. 

Aşağıda, Microsoft 'un web 'de barındırılan kaynakların bazı örnekleri verilmiştir:

* Microsoft Graph: `https://graph.microsoft.com`
* Microsoft 365 mail API 'SI: `https://outlook.office.com`
* Azure Key Vault: `https://vault.azure.net`

Aynı değer, Microsoft Identity platformu ile tümleştirilmiş olan tüm üçüncü taraf kaynakları için de geçerlidir. Bu kaynakların herhangi biri aynı zamanda söz konusu kaynağın işlevselliğini daha küçük parçalara bölmek için kullanılabilecek bir izinler kümesi tanımlayabilir. Örnek olarak [Microsoft Graph](https://graph.microsoft.com) , diğerleri arasında aşağıdaki görevleri yapmak için tanımlı izinlere sahiptir:

* Kullanıcının takvimini oku
* Kullanıcının takvimine yazma
* Kullanıcı olarak posta gönder

Bu izin tanımları türleri nedeniyle, kaynağın verileri üzerinde ayrıntılı denetimi ve API işlevinin sunulma şeklini vardır. Üçüncü taraf bir uygulama, kullanıcıların ve yöneticilerin bu izinleri talep edebilir ve uygulamanın verilere erişebilmeleri veya Kullanıcı adına işlem yapması için isteği onaylaması gerekir. 

Bir kaynağın işlevselliği küçük izin kümelerine öbekli olduğunda, üçüncü taraf uygulamalar yalnızca kendi işlevlerini gerçekleştirmeleri için ihtiyaç duydukları izinleri istemek üzere oluşturulabilir. Kullanıcılar ve Yöneticiler, uygulamanın hangi verileri erişebileceği hakkında bilgi alabilir. Ayrıca, uygulamanın kötü amaçlı bir amaç ile davranmadığından daha emin olabilirler. Geliştiriciler, yalnızca uygulamalarının çalışması için ihtiyaç duydukları izinleri isteyen en az ayrıcalık ilkesine göre her zaman bilmelidir.

OAuth 2,0 ' de, bu izin kümesi türleri *kapsamlar* olarak adlandırılır. Bunlar da genellikle *izinler* olarak adlandırılır. Microsoft Identity platformunda bir izin dize değeri olarak temsil edilir. Bir uygulama, sorgu parametresinde izni belirterek ihtiyaç duyacağı izinleri ister `scope` . Kimlik platformu, iyi tanımlanmış birkaç [OpenID Connect kapsamını](#openid-connect-scopes) ve kaynak tabanlı izinleri destekler (her izin, izin değeri kaynağın tanımlayıcısına veya uygulama kimliği URI 'sine eklenerek belirtilir). Örneğin, izin dizesi `https://graph.microsoft.com/Calendars.Read` Microsoft Graph kullanıcı takvimlerini okumak için izin istemek üzere kullanılır.

Bu izinleri en yaygın olarak, Microsoft Identity platform yetkilendirme uç noktası isteklerindeki kapsamları belirterek ister. Ancak, bazı yüksek ayrıcalıklı izinler yalnızca yönetici onayı üzerinden verilebilir. [Yönetici onay uç noktası](#admin-restricted-permissions)kullanılarak talep edilebilir veya verilebilir. Daha fazla bilgi edinmek için okumaya devam edin.

## <a name="permission-types"></a>İzin türleri

Microsoft Identity platformu iki tür izni destekler: *temsilci izinleri* ve *Uygulama izinleri*.

* **Temsilci izinleri** , oturum açmış bir Kullanıcı bulunan uygulamalar tarafından kullanılır. Bu uygulamalar için, Kullanıcı ya da yönetici, uygulamanın istediği izinleri onaylar. Uygulama, hedef kaynağa çağrılar yaptığında oturum açmış kullanıcı olarak görev yapmak için izin verildi. 

    Bazı Temsilcili izinler, yönetici olmayanlar tarafından yapılabilir. Ancak bazı yüksek ayrıcalıklı izinler [yönetici onayı](#admin-restricted-permissions)gerektirir. Hangi Yönetici rollerinin temsilci izinleri onaylamasına izin verebileceğini öğrenmek için [Azure Active Directory (Azure AD) Içinde yönetici rolü izinleri](../roles/permissions-reference.md)bölümüne bakın.

* **Uygulama izinleri** , oturum açmış bir kullanıcı olmadan çalışan uygulamalar tarafından kullanılır; Örneğin, arka plan hizmetleri veya Daemon 'ları olarak çalışan uygulamalar. Yalnızca [bir yönetici, uygulama izinlerini kabul edebilir](#requesting-consent-for-an-entire-tenant) .

_Etkili izinler_ , uygulamanızın hedef kaynağa istek yaptığında sahip olduğu izinlerdir. Uygulamanızın verdiği temsilci izinleri ve uygulama izinleri arasındaki farkın ve hedef kaynağa çağrı yaptığında uygulamanızın verdiği etkin izinlerin anlaşılması önemlidir.

- Temsilci izinleri için uygulamanızın _etkili izinleri_ , uygulamaya atanan izinlerin en az ayrıcalıklı kesişimine (izin olarak) ve şu anda oturum açmış kullanıcının ayrıcalıklarına sahiptir. Uygulamanızın ayrıcalıkları hiçbir zaman oturum açmış kullanıcının ayrıcalıklarından fazla olamaz. 

   Kuruluşlar içinde, oturum açmış kullanıcının ayrıcalıkları ilke tarafından veya bir veya daha fazla yönetici rolünde üyelikle belirlenebilir. Hangi Yönetici rollerinin temsilci izinleri onaylamasına izin verebileceğini öğrenmek için bkz. [Azure AD 'de yönetici rolü izinleri](../roles/permissions-reference.md).

   Örneğin, uygulamanıza _User. ReadWrite. All_ temsilcisi izni verildiğini varsayalım. Adından da anlaşıldığı gibi bu izin uygulamanıza kuruluştaki her kullanıcının profilini okuma ve güncelleştirme izni verir. Oturum açmış kullanıcı genel yöneticidir, uygulamanız kuruluştaki her kullanıcının profilini güncelleştirebilir. Ancak, oturum açmış kullanıcının yönetici rolü yoksa, uygulamanız yalnızca oturum açan kullanıcının profilini güncelleştirebilir. Adına işlem yapması gereken Kullanıcı bu ayrıcalıklara sahip olmadığından kuruluştaki diğer kullanıcıların profillerini güncelleştiremez.

- Uygulama izinleri için uygulamanızın _etkili izinleri_ , izin tarafından ima edilen ayrıcalıkların tam düzeyidir. Örneğin, _User. ReadWrite_ olan bir uygulama. tüm uygulama izinleri, kuruluştaki her kullanıcının profilini güncelleştirebilir.

## <a name="openid-connect-scopes"></a>OpenID Connect kapsamları

OpenID Connect 'in Microsoft Identity platform uygulamasının Microsoft Graph,,, ve üzerinde de barındırılan bazı iyi tanımlanmış kapsamları vardır `openid` `email` `profile` `offline_access` . `address`Ve `phone` OpenID Connect kapsamları desteklenmez.

OpenID Connect kapsamlarını ve bir belirteci istemeniz durumunda, [UserInfo uç noktasını](userinfo.md)çağırmak için bir belirteç alırsınız.

### <a name="openid"></a>OpenID

Bir uygulama [OpenID Connect](active-directory-v2-protocols.md)kullanarak oturum açarsa, kapsam istemesi gerekir `openid` . `openid`Kapsam, **oturum açma** izniniz olarak iş hesabı onayı sayfasında görünür. Kişisel Microsoft hesabı izni sayfasında, **profilinizi görüntüleme ve Microsoft hesabı izniniz kullanılarak uygulamalara ve hizmetlere bağlanma** gibi görünür. 

Bu izni kullanarak bir uygulama, talep biçiminde kullanıcı için benzersiz bir tanımlayıcı alabilir `sub` . Bu izin, uygulamanın UserInfo uç noktasına erişimini de sağlar. `openid`Kapsam, kimlik belirteçleri almak Için Microsoft Identity platform belirteci uç noktasında kullanılabilir. Uygulama bu belirteçleri kimlik doğrulaması için kullanabilir.

### <a name="email"></a>e-posta

`email`Kapsam `openid` ve diğer kapsamlarla birlikte kullanılabilir. Uygulama, kullanıcının birincil e-posta adresine talep biçiminde erişim sağlar `email` . 

`email`Talep, yalnızca bir e-posta adresi kullanıcı hesabıyla ilişkiliyse, her zaman durum olmayan bir belirtece dahil edilir. Uygulamanız `email` kapsamı kullanıyorsa, uygulamanın belirteçte talep bulunmayan bir servis talebini işleyebilmesi gerekir `email` .

### <a name="profile"></a>profil

`profile`Kapsam, `openid` kapsam ve diğer tüm kapsamlar ile birlikte kullanılabilir. Uygulamanın kullanıcı hakkındaki büyük miktarda bilgiye erişmesini sağlar. Erişebileceği bilgiler, kullanıcının verilen adı, soyadı, tercih edilen Kullanıcı adı ve nesne KIMLIĞINI içerir, ancak bunlarla sınırlı değildir. 

`profile`Belirli bir kullanıcının parametresinde bulunan taleplerin tüm listesi için `id_tokens` , [ `id_tokens` başvuruya](id-tokens.md)bakın.

### <a name="offline_access"></a>offline_access

[ `offline_access` Kapsam](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) , uygulamanızın kullanıcı adına uzun bir süre boyunca kaynaklara erişmesini sağlar. Onay sayfasında bu kapsam, izin **erişimi verdiğiniz verilere erişim sağlama** olarak görünür. 

Kullanıcı kapsamı onayladığında `offline_access` , uygulamanız Microsoft Identity platform belirteci uç noktasından yenileme belirteçleri alabilir. Yenileme belirteçleri uzun süreli. Uygulamanız, eski kullanım süreleri dolana kadar yeni erişim belirteçleri alabilir.

> [!NOTE]
> Bu izin şu anda, yenileme belirteci sağlamayan akışlar için bile tüm onay sayfalarında görünür ( [örtük akış](v2-oauth2-implicit-grant-flow.md)gibi). Bu kurulum, bir istemcinin örtük akış içinde başlayabileceği ve sonra yenileme belirtecinin beklendiği kod akışına taşıyabildiği senaryolara yöneliktir.

Microsoft Identity platformunda (v 2.0 uç noktasında yapılan istekler), `offline_access` yenileme belirteçleri almak için uygulamanızın kapsamı açıkça istemesi gerekir. Bu nedenle, [OAuth 2,0 yetkilendirme kodu akışında](active-directory-v2-protocols.md)bir yetkilendirme kodu kullandığınızda uç noktadan yalnızca bir erişim belirteci alacaksınız `/token` . 

Erişim belirteci kısa bir süre için geçerlidir. Genellikle bir saat içinde sona erer. Bu noktada, uygulamanızın `/authorize` Yeni bir yetkilendirme kodu almak için kullanıcıyı uç noktaya yeniden yönlendirmesi gerekir. Bu yeniden yönlendirme sırasında, uygulamanın türüne bağlı olarak, kullanıcının kimlik bilgilerini yeniden girmesi veya izinleri yeniden onaylaması gerekebilir.

Yenileme belirteçleri alma ve kullanma hakkında daha fazla bilgi için bkz. [Microsoft Identity platform protokol başvurusu](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Bireysel kullanıcı izni isteme

Bir [OpenID Connect veya OAuth 2,0](active-directory-v2-protocols.md) yetkilendirme isteğinde bir uygulama, sorgu parametresini kullanarak ihtiyaç duyacağı izinleri isteyebilir `scope` . Örneğin, bir Kullanıcı bir uygulamada oturum açtığında, uygulama aşağıdaki örnek gibi bir istek gönderir. (Okunabilirliği için satır sonları eklenir.)

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope`Parametresi, uygulamanın istediği atanan izinlerin boşlukla ayrılmış bir listesidir. Her izin, izin değeri kaynağın tanımlayıcısına eklenerek belirtilir (uygulama KIMLIĞI URI 'SI). İstek örneğinde, uygulamanın kullanıcının takvimini okumak ve Kullanıcı olarak e-posta göndermek için izin gerekir.

Kullanıcı kimlik bilgilerini girdikten sonra, Microsoft Identity platformu eşleşen bir *Kullanıcı izni* kaydını denetler. Kullanıcı geçmişte istenen izinlerden herhangi birine onay vermezse ve yönetici tüm kuruluş adına bu izinlere ayrılmamışsa, Microsoft Identity platformu kullanıcıdan istenen izinleri vermesini ister. bu

Şu anda, `offline_access` ("erişim vermiş olduğunuz verilere erişimi koru") izni ve `user.read` ("profilinizi oturum açma ve profilinizi okuma") izni, bir uygulamaya ilk onaylamada otomatik olarak eklenir.  Bu izinler genellikle uygun uygulama işlevleri için gereklidir. Bu `offline_access` izin, uygulamanın yerel uygulamalar ve Web uygulamaları için kritik olan belirteçleri yenileme erişimi sağlar. `user.read`İzin, talebe erişim sağlar `sub` . İstemci veya uygulamanın zaman içinde kullanıcıyı doğru şekilde tanımlamasına ve ilkel Kullanıcı bilgilerine erişmesine izin verir.

![İş hesabı onayını gösteren örnek ekran görüntüsü.](./media/v2-permissions-and-consent/work_account_consent.png)

Kullanıcı izin isteğini onayladığında, onay kaydedilir. Kullanıcı daha sonra uygulamada oturum açtıklarında tekrar onay almak zorunda değildir.

## <a name="requesting-consent-for-an-entire-tenant"></a>Tüm kiracı için izin isteme

Bir kuruluş bir uygulama için lisans veya abonelik satın aldığında, kuruluş genellikle uygulamayı kuruluşun tüm üyeleri tarafından kullanılmak üzere ayarlamak ister. Bu işlemin bir parçası olarak, bir yönetici, uygulamanın Kiracıdaki herhangi bir kullanıcı adına işlem yapması için izin verebilir. Yönetici Kiracı genelinde onay veriyorsa, kuruluşun kullanıcıları uygulama için bir onay sayfası görmez.

Bir Kiracıdaki tüm kullanıcılar için temsilci izinleri için izin istemek üzere, uygulamanız yönetici onay uç noktasını kullanabilir.

Ayrıca, uygulamalar uygulama izinleri istemek için yönetici onay uç noktasını kullanmalıdır.

## <a name="admin-restricted-permissions"></a>Yönetici kısıtlı izinler

Microsoft kaynaklarında bazı yüksek ayrıcalıklı izinler *yönetici tarafından kısıtlanmış* olarak ayarlanabilir. Bu tür izinlerin bazı örnekleri aşağıda verilmiştir:

* Kullanarak tüm kullanıcıların tam profillerini okuyun `User.Read.All`
* Kullanarak bir kuruluşun dizinine veri yazma `Directory.ReadWrite.All`
* Kullanarak bir kuruluşun dizinindeki tüm grupları okuma `Groups.Read.All`

Bir tüketici kullanıcısı bu tür verilere erişim izni verebilir, ancak kurumsal kullanıcılar aynı hassas şirket verileri kümesine erişim izni veremez. Uygulamanız bir kuruluş kullanıcısının bu izinlerinden birine erişim isterse, kullanıcı uygulamanızın izinlerini kabul etmek için yetkilendirilmediğini bildiren bir hata iletisi alır.

Uygulamanız Yönetici kısıtlı izinler için kapsamlar gerektiriyorsa, kuruluşun yöneticisi kuruluşun kullanıcıları adına bu kapsamları kabul etmelidir. Kullanıcılara, izin veremediği izinler için izin isteyen kullanıcılara yönelik bir istem görüntülenmesini önlemek için, uygulamanız yönetici onay uç noktasını kullanabilir. Yönetici onay uç noktası sonraki bölümde ele alınmıştır.

Uygulama yüksek ayrıcalıklı temsilci izinleri isterse ve bir yönetici bu izinleri yönetici onay uç noktası üzerinden veriyorsa, Kiracıdaki tüm kullanıcılar için izin verilir.

Uygulama, uygulama izinlerini isterse ve bir yönetici bu izinleri yönetici onay uç noktası üzerinden veriyorsa, bu izin belirli bir kullanıcı adına yapılmaz. Bunun yerine, istemci uygulamasına *doğrudan* izinler verilir. Bu tür izinler yalnızca Daemon Hizmetleri ve arka planda çalışan diğer etkileşimsiz uygulamalar tarafından kullanılır.

## <a name="using-the-admin-consent-endpoint"></a>Yönetici onay uç noktasını kullanma

Yönetici onayı vermek için yönetici onay uç noktasını kullandıktan sonra işiniz tamamlanmıştır. Kullanıcıların başka bir işlem yapması gerekmez. Yönetici izni verildiğinde, kullanıcılar tipik bir kimlik doğrulama akışı aracılığıyla erişim belirteci alabilir. Elde edilen erişim belirtecinin, onaylanan izinleri vardır.

Genel yönetici uygulamanızı kullandığında ve yetkilendirme uç noktasına yönlendirildiğine göre, Microsoft Identity platformu kullanıcının rolünü algılar. Genel yöneticinin istediğiniz izinler için tüm kiracının adına onay vermesini isteyip istemediğini sorar. Bunun yerine, bir yöneticinin tüm kiracı adına izin vermesini sağlamak için bir özel yönetici onay uç noktası kullanabilirsiniz. Bu uç nokta, uygulama izinleri istemek için de gereklidir. Uygulama izinleri yetkilendirme uç noktası kullanılarak istenemez.

Bu adımları izlerseniz, uygulamanız, yönetici tarafından kısıtlanmış kapsamlar dahil olmak üzere bir Kiracıdaki tüm kullanıcılar için izinler isteyebilir. Bu işlem yüksek ayrıcalıkdır. İşlemi yalnızca senaryonuz için gerekliyse kullanın.

Adımları uygulayan bir kod örneğini görmek için GitHub 'da [yönetici kısıtlamalı kapsamlar örneğine](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2) bakın.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Uygulama kayıt portalında izinleri isteyin

Uygulama kayıt portalında, uygulamalar, gereken izinleri ve hem temsilci izinleri hem de uygulama izinlerini listeleyebilir. Bu kurulum, `/.default` kapsamın ve Azure Portal **yönetici izin verme** seçeneğinin kullanımına izin verir.  

Genel olarak, belirli bir uygulama için izinlerin statik olarak tanımlanması gerekir. Bunlar, uygulamanın dinamik veya artımlı olarak isteyeceğini belirten izinlerin bir üst kümesi olmalıdır.

> [!NOTE]
>Uygulama izinleri yalnızca kullanımı aracılığıyla istenebilir [`/.default`](#the-default-scope) . Bu nedenle, uygulamanız uygulama izinlerine ihtiyaç duyuyorsa, bunların uygulama kayıt portalında listelendiğinden emin olun.

Bir uygulama için statik olarak istenen izinlerin listesini yapılandırmak için:

1. <a href="https://go.microsoft.com/fwlink/?linkid=2083908" target="_blank">Azure portal uygulama kayıtları</a> hızlı başlangıç deneyiminde uygulamanıza gidin.
1. Bir uygulama seçin veya henüz yapmadıysanız bir uygulama [oluşturun](quickstart-register-app.md) .
1. Uygulamanın **genel bakış** sayfasında, **Yönet** altında, **API izinleri**  >  **izin Ekle**' yi seçin.
1. Kullanılabilir API 'Ler listesinden **Microsoft Graph** seçin. Ardından, uygulamanızın gerektirdiği izinleri ekleyin.
1. **Izin Ekle**' yi seçin.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Önerilir: Kullanıcı uygulamanızda oturum açın

Genellikle, yönetici onay uç noktasını kullanan bir uygulama oluşturduğunuzda, uygulamanın, yöneticinin uygulamanın izinlerini onaylayabileceği bir sayfa veya görünüm gerekir. Bu sayfa şu olabilir:

* Uygulamanın kaydolma akışının bir parçası.
* Uygulamanın ayarlarının bir parçası.
* Adanmış bir "Connect" akışı. 

Çoğu durumda, uygulamanın bu "Bağlan" görünümünü yalnızca bir kullanıcı iş Microsoft hesabı veya okul Microsoft hesabı oturum açtıktan sonra göstermesini mantıklı hale getirir.

Kullanıcı uygulamanızda oturum açtığınızda, gerekli izinleri onaylamasını istemek için yöneticinin ait olduğu kuruluşu belirleyebilirsiniz. Bu adım kesinlikle gerekli olmasa da, kurumsal kullanıcılarınız için daha sezgisel bir deneyim oluşturmanıza yardımcı olabilir. 

Kullanıcı oturumu açmak için [Microsoft Identity platform protokol öğreticilerini](active-directory-v2-protocols.md)izleyin.

### <a name="request-the-permissions-from-a-directory-admin"></a>Dizin yöneticisinden izinleri isteme

Kuruluşunuzun yöneticisinden izin istemek için hazırsanız, kullanıcıyı Microsoft Identity Platform Yöneticisi onay uç noktasına yönlendirebilirsiniz.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Parametre        | Koşul        | Açıklama                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Gerekli | İzin istemek istediğiniz dizin kiracısı. Bu, bir GUID veya kolay ad biçiminde sağlanıyor olabilir. Ya da örnekte görüldüğü gibi, kuruluşlara genel olarak başvurulabilir. Kişisel hesaplar kiracı bağlamı haricinde yönetici onayı sağlayamadığı için "ortak" kullanmayın. Kiracıların yönetiminde kişisel hesaplarla en iyi uyumluluğu sağlamak için, mümkün olduğunda kiracı KIMLIĞINI kullanın. |
| `client_id` | Gerekli | [Azure Portal – uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyiminin uygulamanıza atandığı uygulama (ISTEMCI) kimliği. |
| `redirect_uri` | Gerekli |Uygulamanızın işlenmesi için yanıtın gönderilmesini istediğiniz yeniden yönlendirme URI 'SI. Uygulama kayıt portalı 'nda kaydettiğiniz yeniden yönlendirme URI 'lerinden biriyle tam olarak eşleşmesi gerekir. |
| `state` | Önerilen | İsteğin belirteç yanıtında de döndürülecek bir değer. İstediğiniz herhangi bir içerik dizesi olabilir. Kullanıcının uygulamadaki durumuyla ilgili bilgileri, uygulamanın bulunduğu sayfa veya görünüm gibi kimlik doğrulama isteği olmadan önce kodlamak için bu durumu kullanın. |
|`scope`        | Gerekli        | Uygulama tarafından istenen izin kümesini tanımlar. Kapsamlar statik (kullanılarak [`/.default`](#the-default-scope) ) ya da dinamik olabilir.  Bu küme, OpenID Connect kapsamlarını ( `openid` , `profile` ,) içerebilir `email` . Uygulama izinlerine ihtiyacınız varsa, `/.default` statik olarak yapılandırılmış izin listesini istemek için kullanmanız gerekir.  |


Bu noktada, Azure AD 'nin isteği tamamlaması için bir kiracı yöneticisinin oturum açması gerekir. Yöneticinin, parametresinde istediğiniz tüm izinleri onaylaması istenir `scope` .  Statik ( `/.default` ) değeri kullandıysanız, bu, uygulama için gerekli izinlerde bulunan tüm kapsamlar için v 1.0 Yönetici onay uç noktası ve istek onayı gibi çalışır.

#### <a name="successful-response"></a>Başarılı yanıt

Yönetici, uygulamanız için izinleri onayladığında, başarılı yanıt şöyle görünür:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametre | Açıklama |
| --- | --- |
| `tenant` | Uygulamanıza istenen izinleri (GUID biçiminde) veren dizin kiracısı. |
| `state` | İstekte bulunan ve belirteç yanıtında de döndürülen bir değer. İstediğiniz herhangi bir içerik dizesi olabilir. Durum, kullanıcının uygulamadaki durumu hakkında bilgi kodlamak için kullanılır; Örneğin, bulunan sayfa veya görünüm gibi kimlik doğrulama isteği gerçekleştirilmeden önce. |
| `admin_consent` | , Olarak ayarlanır `True` . |

#### <a name="error-response"></a>Hata yanıtı

Yönetici, uygulamanız için izinleri onaylamazsa, başarısız yanıt şöyle görünür:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametre | Açıklama |
| --- | --- |
| `error` | Oluşan hata türlerini sınıflandırmak için kullanılabilen bir hata kodu dizesi. Hatalara tepki vermek için de kullanılabilir. |
| `error_description` | Bir geliştiricinin hatanın kök nedenini belirlemesine yardımcı olabilecek belirli bir hata iletisi. |

Yönetici onay uç noktasından başarılı bir yanıt aldıktan sonra, uygulamanız istediği izinleri kazandı. Ardından, istediğiniz kaynak için bir belirteç isteyebilirsiniz.

## <a name="using-permissions"></a>İzinleri kullanma

Kullanıcı uygulamanız için izinleri onayladıktan sonra uygulamanız, uygulamanın belirli bir kapasitede bir kaynağa erişme iznini temsil eden erişim belirteçleri alabilir. Erişim belirteci, yalnızca tek bir kaynak için kullanılabilir. Ancak erişim belirtecinin içinde kodlanan, uygulamanızın o kaynak için verilen her izindir. Uygulamanız, bir erişim belirteci almak için Microsoft Identity platform belirteç uç noktasına aşağıdaki gibi bir istek yapabilir:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

HTTP isteklerinde elde edilen erişim belirtecini kaynağa gönderebilirsiniz. Bu, uygulamanızın belirli bir görevi yapmak için uygun izinlere sahip olduğunu güvenilir bir şekilde gösterir.

OAuth 2,0 protokolü ve erişim belirteçleri alma hakkında daha fazla bilgi için bkz. [Microsoft Identity platform uç nokta protokol başvurusu](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>/.Exe varsayılan kapsamı

`/.default`Uygulamalarınızı v 1.0 uç noktasından Microsoft Identity platform uç noktasına geçirmeye yardımcı olması için kapsamı kullanabilirsiniz. `/.default`Kapsam, uygulama kaydında yapılandırılan izinlerin statik listesine başvuran her uygulama için yerleşiktir. 

Bir `scope` değeri, `https://graph.microsoft.com/.default` `resource=https://graph.microsoft.com` v 1.0 uç noktasındaki işlev ile aynıdır. `https://graph.microsoft.com/.default`Uygulamanız, isteği içinde belirterek, uygulama kayıt portalında uygulama için seçtiğiniz her Microsoft Graph izni için kapsamlar içeren bir erişim belirteci istiyor. Kapsam, kaynak URI 'SI ve kullanılarak oluşturulur `/.default` . Bu nedenle Kaynak URI 'SI ise, `https://contosoApp.com` istenen kapsam olur `https://contosoApp.com/.default` .  Belirteci doğru bir şekilde istemek için ikinci bir eğik çizgi eklemeniz gereken durumlarda, [sonundaki eğik çizgiler hakkında bölümüne](#trailing-slash-and-default)bakın.

`/.default`Kapsam herhangi bir OAuth 2,0 akışında kullanılabilir. Ancak, [Şirket adına](v2-oauth2-on-behalf-of-flow.md) ve [istemci kimlik bilgileri akışında](v2-oauth2-client-creds-grant-flow.md)gereklidir. Ayrıca, uygulama izinleri istemek için v2 yönetici onay uç noktasını kullandığınızda da ihtiyacınız vardır.

İstemciler statik ( `/.default` ) onayı ve dinamik onayı tek bir istekte birleştiremez. `scope=https://graph.microsoft.com/.default+mail.read`Bu nedenle, kapsam türlerini birleştirilemediğinden bir hatayla sonuçlanır.

### <a name="default-and-consent"></a>/.exe varsayılan ve onay

`/.default`Kapsam, v 1.0 uç nokta davranışını de tetikler `prompt=consent` . Kaynak ne olursa olsun, uygulamanın kaydolduğuna yönelik tüm izinler için onay ister. İsteğin bir parçası olarak dahil edildiyse `/.default` kapsam, istenen kaynak için Kapsamları içeren bir belirteç döndürür.

### <a name="default-when-the-user-has-already-given-consent"></a>/.exe Kullanıcı zaten izin vermiş olduğunda varsayılan

`/.default`Kapsam işlevsel olarak `resource` -merkezli v 1.0 uç noktası davranışıyla aynıdır. V 1.0 uç noktasının izin davranışını da taşır. Diğer bir deyişle, `/.default` yalnızca kullanıcı istemciyle kaynak arasında izin vermediyseniz bir onay istemi tetikler. 

Böyle bir onay varsa, döndürülen belirteç kullanıcının bu kaynak için verdiği tüm kapsamları içerir. Ancak, izin verilmezse veya `prompt=consent` parametre sağlanmışsa, istemci uygulamanın kayıtlı olduğu tüm kapsamlar için bir onay istemi gösterilir.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Örnek 1: Kullanıcı veya kiracı yöneticisi izin verdi

Bu örnekte, Kullanıcı veya kiracı yöneticisi `mail.read` `user.read` istemci için ve Microsoft Graph izinleri verdi. 

İstemci isterse, `scope=https://graph.microsoft.com/.default` istemci uygulamanın Microsoft Graph için kayıtlı izinlerinin içeriğinden bağımsız olarak hiçbir onay istemi gösterilmez. Döndürülen belirteç, kapsamları ve içerir `mail.read` `user.read` .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Örnek 2: kullanıcıya istemci ve kaynak arasında izin verilmemiş

Bu örnekte, kullanıcı istemciyle Microsoft Graph arasında izin vermedi. İstemci, izinler ve için kayıtlı `user.read` `contacts.read` . Ayrıca Azure Key Vault kapsamı için de kaydedilir `https://vault.azure.net/user_impersonation` . 

İstemci için bir belirteç istediğinde `scope=https://graph.microsoft.com/.default` , Kullanıcı `user.read` kapsam, `contacts.read` kapsam ve Key Vault kapsamları için bir onay sayfası görür `user_impersonation` . Döndürülen belirteç yalnızca `user.read` ve `contacts.read` kapsamlarını içerir. Yalnızca Microsoft Graph için kullanılabilir.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-more-scopes"></a>Örnek 3: Kullanıcı onaylanmıştır ve istemci daha fazla kapsam ister

Bu örnekte, Kullanıcı istemcisi için zaten bir daha onayladı `mail.read` . İstemci kapsama kaydoldu `contacts.read` . 

İstemci kullanarak bir belirteç istediğinde `scope=https://graph.microsoft.com/.default` ve aracılığıyla onay istediğinde `prompt=consent` , Kullanıcı tüm (ve yalnızca) uygulamanın kayıtlı olduğu izinleri bir onay sayfası görür. `contacts.read`Kapsam onay sayfasıdır ancak değil `mail.read` . Döndürülen belirteç Microsoft Graph içindir. Ve içerir `mail.read` `contacts.read` .

### <a name="using-the-default-scope-with-the-client"></a>İstemcisiyle//varsayılan kapsamını kullanma

Bazı durumlarda, bir istemci kendi kapsamını talep edebilir `/.default` . Aşağıdaki örnekte bu senaryo gösterilmektedir.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //Code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Bu kod örneği, önceki onay açıklamaları ve senaryoya uygulanacaksa tüm kayıtlı izinler için bir onay sayfası oluşturur `/.default` . Daha sonra kod `id_token` , erişim belirteci yerine bir döndürür.  

Bu davranış, Azure AD kimlik doğrulama kitaplığı 'ndan (ADAL) Microsoft kimlik doğrulama kitaplığı 'na (MSAL) taşınan bazı eski istemcileri de karşılar. Bu kurulum, Microsoft Identity platformunu hedefleyen yeni istemciler *tarafından kullanılmamalıdır.*

### <a name="client-credentials-grant-flow-and-default"></a>İstemci kimlik bilgileri verme akışı ve/.exe varsayılan  

Başka bir kullanımı `/.default` , bir Web API 'sini çağırmak için [istemci kimlik bilgileri](v2-oauth2-client-creds-grant-flow.md) verme akışını kullanan bir Daemon uygulaması gibi etkileşimli olmayan bir uygulamada uygulama izinleri (veya *rolleri*) isteğidir.

Bir Web API 'SI için uygulama izinleri (roller) oluşturmak için, bkz. [uygulamanıza uygulama rolleri ekleme](howto-add-app-roles-in-azure-ad-apps.md).

İstemci uygulamanızdaki istemci kimlik bilgileri istekleri içermesi *gerekir* `scope={resource}/.default` . Burada, `{resource}` uygulamanızın çağırmak amaçladığı Web API 'sidir. Bağımsız uygulama izinleri (roller) kullanarak istemci kimlik bilgileri isteği *verme desteklenmez.* Bu Web API 'SI için verilen tüm uygulama izinleri (roller), döndürülen erişim belirtecine dahildir.

Uygulama için yönetici onayı verme dahil olmak üzere, tanımladığınız uygulama izinlerine erişim vermek için bkz. [bir Web API 'sine erişmek için istemci uygulaması yapılandırma](quickstart-configure-app-access-web-apis.md).

### <a name="trailing-slash-and-default"></a>Sondaki eğik çizgi ve//varsayılan

Bazı kaynak URI 'Lerinde, örneğin aksine, izleyen eğik çizgi vardır `https://contoso.com/` `https://contoso.com` . Sondaki eğik çizgi, belirteç doğrulamayla ilgili sorunlara neden olabilir. Sorunlar öncelikle Azure Resource Manager () için bir belirteç istendiğinde oluşur `https://management.azure.com/` . Bu durumda, kaynak URI 'sindeki bir eğik çizgi, belirteç istendiğinde eğik çizginin mevcut olması gerektiği anlamına gelir.  Bu nedenle `https://management.azure.com/` , ve kullanmak için bir belirteç istediğinizde `/.default` , `https://management.azure.com//.default` (çift eğik çizgi!) istemeniz gerekir. Genel olarak, belirtecin verildiğini ve belirtecin kabul etmesi gereken API tarafından reddedildiğini doğruladıktan sonra ikinci bir eğik çizgi eklemeyi ve yeniden denemeyi düşünün. 

## <a name="troubleshooting-permissions-and-consent"></a>İzinler ve onay sorunlarını giderme

Sorun giderme adımları için bkz. [bir uygulamaya izin uygularken beklenmeyen hata](../manage-apps/application-sign-in-unexpected-user-consent-error.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Microsoft Identity platformunda KIMLIK belirteçleri](id-tokens.md)
* [Microsoft Identity platformunda belirteçleri erişim](access-tokens.md)
