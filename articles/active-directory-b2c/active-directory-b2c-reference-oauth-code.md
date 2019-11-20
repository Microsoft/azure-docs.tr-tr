---
title: Yetkilendirme kodu akışı-Azure Active Directory B2C | Microsoft Docs
description: Azure AD B2C ve OpenID Connect kimlik doğrulama protokolünü kullanarak Web uygulamaları oluşturmayı öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d738bfb8bcd11c8da4c39d873c7f298b8c49af98
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167191"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 'de OAuth 2,0 yetkilendirme kodu akışı

Web API 'Leri gibi korumalı kaynaklara erişim kazanmak için bir cihazda yüklü olan uygulamalarda OAuth 2,0 yetkilendirme kodu yetkisini kullanabilirsiniz. OAuth 2,0 Azure Active Directory B2C (Azure AD B2C) uygulamasını kullanarak, mobil ve Masaüstü uygulamalarınıza kaydolma, oturum açma ve diğer kimlik yönetimi görevlerini ekleyebilirsiniz. Bu makale, dilden bağımsızdır. Makalesinde, herhangi bir açık kaynak kitaplığı kullanmadan HTTP iletilerinin nasıl gönderileceğini ve alınacağını anladık.

OAuth 2,0 yetkilendirme kodu akışı, [oauth 2,0 belirtiminin 4,1 bölümünde](https://tools.ietf.org/html/rfc6749)açıklanmaktadır. Web uygulamaları ve yerel olarak yüklenen uygulamalar dahil olmak üzere çoğu [uygulama türünde](active-directory-b2c-apps.md)kimlik doğrulama ve yetkilendirme için kullanabilirsiniz. OAuth 2,0 yetkilendirme kodu akışını, bir [yetkilendirme sunucusu](active-directory-b2c-reference-protocols.md)tarafından güvenliği sağlanmış olan kaynaklara erişmek için kullanılabilecek, uygulamalarınıza yönelik erişim belirteçlerini ve yenileme belirteçlerini güvenli bir şekilde almak için kullanabilirsiniz.  Yenileme belirteci, erişim belirtecinin süresi dolduktan sonra, genellikle bir saatten sonra istemcinin yeni erişim (ve yenileme) belirteçleri almasına izin verir.

Bu makale, **genel istemciler** OAuth 2,0 yetkilendirme kodu akışına odaklanır. Ortak istemci, gizli bir parolanın bütünlüğünü güvenli bir şekilde korumak için güvenilir olmayan tüm istemci uygulamasıdır. Bu, mobil uygulamaları, masaüstü uygulamaları ve temelde bir cihazda çalışan ve erişim belirteçleri alması gereken tüm uygulamaları içerir.

> [!NOTE]
> Azure AD B2C kullanarak bir Web uygulamasına kimlik yönetimi eklemek için, OAuth 2,0 yerine [OpenID Connect](active-directory-b2c-reference-oidc.md) kullanın.

Azure AD B2C, basit kimlik doğrulaması ve yetkilendirmeden daha fazlasını yapmak için standart OAuth 2,0 akışlarını genişletir. [Kullanıcı akışını](active-directory-b2c-reference-policies.md)tanıtır. Kullanıcı akışları ile, uygulamanıza kaydolma, oturum açma ve profil yönetimi gibi kullanıcı deneyimleri eklemek için OAuth 2,0 kullanabilirsiniz. OAuth 2,0 protokolünü kullanan kimlik sağlayıcıları [Amazon](active-directory-b2c-setup-amzn-app.md), [Azure Active Directory](active-directory-b2c-setup-oidc-azure-active-directory.md), [Facebook](active-directory-b2c-setup-fb-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md)ve [LinkedIn](active-directory-b2c-setup-li-app.md)' i içerir.

Bu makaledeki HTTP isteklerini denemek için:

1. Değiştirin `{tenant}` Azure AD B2C kiracınızın adı.
1. `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`, daha önce Azure AD B2C kiracınıza kaydettiğiniz uygulamanın uygulama KIMLIĞIYLE değiştirin.
1. `{policy}`, kiracınızda oluşturduğunuz bir ilkenin adıyla değiştirin, örneğin `b2c_1_sign_in`.

## <a name="1-get-an-authorization-code"></a>1. bir yetkilendirme kodu alın
Yetkilendirme kodu akışı, kullanıcıyı `/authorize` uç noktasına yönlendiren istemciyle başlar. Bu, akışın kullanıcının işlem yapacağı etkileşimli kısmıdır. Bu istekte istemci, `scope` parametresinde kullanıcıdan almaları gereken izinleri gösterir. Aşağıdaki üç örnek (okunabilirlik için satır sonları ile) her biri farklı bir Kullanıcı akışı kullanır.


```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
```


| Parametre | Gerekli mi? | Açıklama |
| --- | --- | --- |
|Kiracı| Gerekli | Azure AD B2C kiracınızın adı|
| ilkesinin | Gerekli | Çalıştırılacak Kullanıcı akışı. Azure AD B2C kiracınızda oluşturduğunuz Kullanıcı akışının adını belirtin. Örneğin: `b2c_1_sign_in`, `b2c_1_sign_up`veya `b2c_1_edit_profile`. |
| client_id |Gerekli |[Azure Portal](https://portal.azure.com)uygulamanıza atanan uygulama kimliği. |
| response_type |Gerekli |Yetkilendirme kodu akışı için `code` içermesi gereken yanıt türü. |
| redirect_uri |Gerekli |Uygulamanızın kimlik doğrulama yanıtlarının gönderildiği ve alındığı, uygulamanızın yeniden yönlendirme URI 'SI. Portalın, URL kodlamalı olması dışında, portalda kaydettiğiniz yeniden yönlendirme URI 'lerinden biriyle tam olarak eşleşmesi gerekir. |
| scope |Gerekli |Kapsamların boşlukla ayrılmış listesi. Tek bir kapsam değeri, İstenen izinlerin her ikisi de Azure Active Directory (Azure AD) gösterir. İstemci KIMLIĞINI kapsam olarak kullanmak, uygulamanızın aynı istemci KIMLIĞIYLE temsil edilen kendi hizmetinize veya Web API 'niz için kullanılabilecek bir erişim belirteci gerektiğini gösterir.  `offline_access` kapsamı, uygulamanızın kaynaklara uzun süreli erişim için yenileme belirteci gerektiğini gösterir. Ayrıca, Azure AD B2C bir KIMLIK belirteci istemek için `openid` kapsamını da kullanabilirsiniz. |
| response_mode |Önerilen |Elde edilen yetkilendirme kodunu uygulamanıza geri göndermek için kullandığınız yöntem. `query`, `form_post`veya `fragment`olabilir. |
| durum |Önerilen |İstekte bulunan, kullanmak istediğiniz herhangi bir içerik dizesi olabilecek bir değer. Genellikle, siteler arası istek sahteciliği saldırıları engellemek için rastgele oluşturulmuş benzersiz bir değer kullanılır. Durum Ayrıca, kimlik doğrulama isteği gerçekleştirilmeden önce kullanıcının uygulamasındaki durumu hakkında bilgi kodlamak için de kullanılır. Örneğin, kullanıcının açık olduğu sayfa veya yürütülmekte olan Kullanıcı akışı. |
| isteme |İsteğe bağlı |Gerekli Kullanıcı etkileşimi türü. Şu anda geçerli olan tek değer `login`, böylece Kullanıcı bu istek üzerine kimlik bilgilerini girmeye zorlar. Çoklu oturum açma etkili olmayacaktır. |

Bu noktada kullanıcıdan Kullanıcı akışının iş akışını tamamlaması istenir. Bu, kullanıcının Kullanıcı adını ve parolasını girmesini, sosyal kimlikle oturum açmasını, dizin için kaydolmasını veya başka birçok adımı de içerebilir. Kullanıcı eylemleri, Kullanıcı akışının nasıl tanımlandığına bağlıdır.

Kullanıcı Kullanıcı akışını tamamladıktan sonra, Azure AD `redirect_uri`için kullandığınız değerde uygulamanıza bir yanıt döndürür. `response_mode` parametresinde belirtilen yöntemini kullanır. Yanıt, yürütülen Kullanıcı akışından bağımsız olarak her kullanıcı eylemi senaryosu için tam olarak aynıdır.

`response_mode=query` kullanan başarılı bir yanıt şuna benzer:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametre | Açıklama |
| --- | --- |
| code |Uygulamanın istediği yetkilendirme kodu. Uygulama, bir hedef kaynak için erişim belirteci istemek üzere yetkilendirme kodunu kullanabilir. Yetkilendirme kodları çok kısa süreli. Genellikle, yaklaşık 10 dakika sonra süresi dolacak. |
| durum |Yukarıdaki bölümde bulunan tablodaki tam açıklamaya bakın. İsteğe bir `state` parametresi dahil edilir, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta `state` değerlerin özdeş olduğunu doğrulamalıdır. |

Ayrıca, uygulamanın uygun şekilde işleyebilmesi için yeniden yönlendirme URI 'sine de hata yanıtları gönderilebilir:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametre | Açıklama |
| --- | --- |
| error |Oluşan hata türlerini sınıflandırmak için kullanabileceğiniz bir hata kodu dizesi. Ayrıca, hatalara yanıt vermek için dizesini de kullanabilirsiniz. |
| error_description |Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |
| durum |Yukarıdaki tabloda bulunan tam açıklamaya bakın. İsteğe bir `state` parametresi dahil edilir, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta `state` değerlerin özdeş olduğunu doğrulamalıdır. |

## <a name="2-get-a-token"></a>2. bir belirteç alın
Artık bir yetkilendirme kodu edindiniz, `/token` uç noktasına bir POST isteği göndererek hedeflenen kaynağa belirteç için `code` kullanabilirsiniz. Azure AD B2C, istek içinde kapsamlarını belirterek diğer API 'ler için her zamanki gibi [erişim belirteçleri isteyebilirsiniz](active-directory-b2c-access-tokens.md#request-a-token) .

Ayrıca, uygulamanın istemci KIMLIĞINI istenen kapsam olarak kullanma kuralına göre uygulamanızın kendi arka uç Web API 'SI için bir erişim belirteci isteyebilirsiniz (Bu, "hedef kitle" olarak bu istemci KIMLIĞINE sahip bir erişim belirtecine neden olur):

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parametre | Gerekli mi? | Açıklama |
| --- | --- | --- |
|Kiracı| Gerekli | Azure AD B2C kiracınızın adı|
|ilkesinin| Gerekli| Yetkilendirme kodunu almak için kullanılan Kullanıcı akışı. Bu istekte farklı bir Kullanıcı akışı kullanamazsınız. |
| client_id |Gerekli |[Azure Portal](https://portal.azure.com)uygulamanıza atanan uygulama kimliği.|
| client_secret | Evet, Web Apps | [Azure Portal](https://portal.azure.com/)oluşturulan uygulama gizli dizisi. İstemci gizli dizileri, istemcinin güvenli bir şekilde istemci gizli depolayabileceği Web uygulaması senaryolarında Bu akışta kullanılır. Yerel uygulama (genel istemci) senaryolarında, istemci gizli dizileri güvenli bir şekilde saklanamaz ve bu nedenle bu çağrıda kullanılmaz. Bir istemci gizli anahtarı kullanırsanız, lütfen düzenli aralıklarla değiştirin. |
| grant_type |Gerekli |İzin türü. Yetkilendirme kodu akışı için, izin türü `authorization_code`olmalıdır. |
| scope |Önerilen |Kapsamların boşlukla ayrılmış listesi. Tek bir kapsam değeri, Azure AD 'ye, İstenen izinlerin her ikisi de belirtir. İstemci KIMLIĞINI kapsam olarak kullanmak, uygulamanızın aynı istemci KIMLIĞIYLE temsil edilen kendi hizmetinize veya Web API 'niz için kullanılabilecek bir erişim belirteci gerektiğini gösterir.  `offline_access` kapsamı, uygulamanızın kaynaklara uzun süreli erişim için yenileme belirteci gerektiğini gösterir.  Ayrıca, Azure AD B2C bir KIMLIK belirteci istemek için `openid` kapsamını da kullanabilirsiniz. |
| code |Gerekli |Akışın ilk baındaki elde ettiğiniz yetkilendirme kodu. |
| redirect_uri |Gerekli |Yetkilendirme kodunu aldığınız uygulamanın yeniden yönlendirme URI 'SI. |

Başarılı bir belirteç yanıtı şuna benzer:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametre | Açıklama |
| --- | --- |
| not_before |Belirtecin geçerli kabul edildiği zaman (dönem süresi). |
| token_type |Belirteç türü değeri. Azure AD 'nin desteklediği tek tür taşıyıcı. |
| access_token |İstediğiniz imzalı JSON Web Token (JWT). |
| scope |Belirtecin geçerli olduğu kapsamlar. Ayrıca, daha sonra kullanmak üzere belirteçleri önbelleğe almak için kapsamları kullanabilirsiniz. |
| expires_in |Belirtecin geçerli olduğu sürenin uzunluğu (saniye cinsinden). |
| refresh_token |Bir OAuth 2,0 yenileme belirteci. Uygulama, geçerli belirtecin süresi dolduktan sonra ek belirteçler almak için bu belirteci kullanabilir. Yenileme belirteçleri uzun süreli. Bunları, uzun süreli kaynaklara erişimi sürdürmek için kullanabilirsiniz. Daha fazla bilgi için [Azure AD B2C belirteç başvurusuna](active-directory-b2c-reference-tokens.md)bakın. |

Hata yanıtları şuna benzer:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametre | Açıklama |
| --- | --- |
| error |Oluşan hata türlerini sınıflandırmak için kullanabileceğiniz bir hata kodu dizesi. Ayrıca, hatalara yanıt vermek için dizesini de kullanabilirsiniz. |
| error_description |Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |

## <a name="3-use-the-token"></a>3. belirteci kullanın
Bir erişim belirtecini başarıyla edindiniz, artık `Authorization` üst bilgisine ekleyerek arka uç Web API 'lerinize yönelik isteklerindeki belirteçleri kullanabilirsiniz:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. belirteci yenileyin
Erişim belirteçleri ve KIMLIK belirteçleri kısa ömürlü. Süreleri dolduktan sonra, kaynaklara erişmeye devam etmek için bunları yenilemeniz gerekir. Bunu yapmak için `/token` uç noktasına başka bir POST isteği gönderin. Bu kez, `code`yerine `refresh_token` sağlayın:

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametre | Gerekli mi? | Açıklama |
| --- | --- | --- |
|Kiracı| Gerekli | Azure AD B2C kiracınızın adı|
|ilkesinin |Gerekli |Özgün yenileme belirtecini almak için kullanılan Kullanıcı akışı. Bu istekte farklı bir Kullanıcı akışı kullanamazsınız. |
| client_id |Gerekli |[Azure Portal](https://portal.azure.com)uygulamanıza atanan uygulama kimliği. |
| client_secret | Evet, Web Apps | [Azure Portal](https://portal.azure.com/)oluşturulan uygulama gizli dizisi. İstemci gizli dizileri, istemcinin güvenli bir şekilde istemci gizli depolayabileceği Web uygulaması senaryolarında Bu akışta kullanılır. Yerel uygulama (genel istemci) senaryolarında, istemci gizli dizileri güvenli bir şekilde saklanamaz ve bu nedenle bu çağrıda kullanılmaz. Bir istemci gizli anahtarı kullanırsanız, lütfen düzenli aralıklarla değiştirin. |
| grant_type |Gerekli |İzin türü. Yetkilendirme kodu akışının bu bacağı için, izin türü `refresh_token`olmalıdır. |
| scope |Önerilen |Kapsamların boşlukla ayrılmış listesi. Tek bir kapsam değeri, Azure AD 'ye, İstenen izinlerin her ikisi de belirtir. İstemci KIMLIĞINI kapsam olarak kullanmak, uygulamanızın aynı istemci KIMLIĞIYLE temsil edilen kendi hizmetinize veya Web API 'niz için kullanılabilecek bir erişim belirteci gerektiğini gösterir.  `offline_access` kapsamı, uygulamanızın kaynaklara uzun süreli erişim için yenileme belirtecine sahip olacağını belirtir.  Ayrıca, Azure AD B2C bir KIMLIK belirteci istemek için `openid` kapsamını da kullanabilirsiniz. |
| redirect_uri |İsteğe bağlı |Yetkilendirme kodunu aldığınız uygulamanın yeniden yönlendirme URI 'SI. |
| refresh_token |Gerekli |Akışın ikinci Bada aldığınız orijinal yenileme belirteci. |

Başarılı bir belirteç yanıtı şuna benzer:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametre | Açıklama |
| --- | --- |
| not_before |Belirtecin geçerli kabul edildiği zaman (dönem süresi). |
| token_type |Belirteç türü değeri. Azure AD 'nin desteklediği tek tür taşıyıcı. |
| access_token |İstediğiniz imzalı JWT. |
| scope |Belirtecin geçerli olduğu kapsamlar. Ayrıca kapsamları, daha sonra kullanmak üzere belirteçleri önbelleğe almak için de kullanabilirsiniz. |
| expires_in |Belirtecin geçerli olduğu sürenin uzunluğu (saniye cinsinden). |
| refresh_token |Bir OAuth 2,0 yenileme belirteci. Uygulama, geçerli belirtecin süresi dolduktan sonra ek belirteçler almak için bu belirteci kullanabilir. Belirteçleri yenileme uzun süreli olduğundan, uzun süre boyunca kaynaklara erişimi sürdürmek için kullanılabilir. Daha fazla bilgi için [Azure AD B2C belirteç başvurusuna](active-directory-b2c-reference-tokens.md)bakın. |

Hata yanıtları şuna benzer:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametre | Açıklama |
| --- | --- |
| error |Oluşan hata türlerini sınıflandırmakta kullanabileceğiniz bir hata kodu dizesi. Ayrıca, hatalara yanıt vermek için dizesini de kullanabilirsiniz. |
| error_description |Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Kendi Azure AD B2C dizininizi kullanın
Bu istekleri kendiniz denemek için aşağıdaki adımları izleyin. Bu makalede kullandığımız örnek değerleri kendi değerlerinizle değiştirin.

1. [Azure AD B2C bir dizin oluşturun](active-directory-b2c-get-started.md). İsteklerde dizininizin adını kullanın.
2. Uygulama KIMLIĞI ve yeniden yönlendirme URI 'SI elde etmek için [bir uygulama oluşturun](active-directory-b2c-app-registration.md) . Uygulamanıza yerel bir istemci ekleyin.
3. Kullanıcı akış adlarınızı almak için [Kullanıcı akışlarınızı oluşturun](active-directory-b2c-reference-policies.md) .
