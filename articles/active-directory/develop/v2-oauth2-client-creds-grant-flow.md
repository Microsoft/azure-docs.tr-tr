---
title: Microsoft Identity platform kullanarak güvenli kaynaklara kullanıcı etkileşimi olmadan erişin | Mavisi
description: OAuth 2,0 kimlik doğrulama protokolünün Microsoft Identity platform uygulamasını kullanarak Web uygulamaları oluşturun.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3bb18f11de92680d296d747fc34e16c3264c369
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193274"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft Identity platformu ve OAuth 2,0 istemci kimlik bilgileri akışı

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Bir uygulamanın kimliğini kullanarak Web 'de barındırılan kaynaklara erişmek için, bazen *iki adet aralıklı OAuth*olarak adlandırılan RFC 6749 ' de belirtilen [OAuth 2,0 istemci kimlik bilgileri verme](https://tools.ietf.org/html/rfc6749#section-4.4) ' yi kullanabilirsiniz. Bu tür bir izin, genellikle bir kullanıcıyla etkileşimde bulunmadan, arka planda çalışması gereken sunucu-sunucu etkileşimleri için kullanılır. Bu tür uygulamalar genellikle *Daemon 'ları* veya *hizmet hesapları*olarak adlandırılır.

OAuth 2,0 istemci kimlik bilgileri verme akışı, bir Web hizmetinin (gizli istemci) başka bir Web hizmetini çağırırken kimlik doğrulaması yapmak yerine kendi kimlik bilgilerini kullanmasına izin verir. Bu senaryoda, istemci genellikle bir orta katman Web hizmeti, bir Daemon hizmeti veya bir Web sitesidir. Daha yüksek bir güvence düzeyi için, Microsoft Identity platformu, çağıran hizmetin kimlik bilgileri olarak bir sertifika (paylaşılan gizlilik yerine) kullanmasına de olanak tanır.

> [!NOTE]
> Microsoft Identity platform uç noktası tüm Azure AD senaryolarını ve özelliklerini desteklemez. Microsoft Identity platform uç noktasını kullanmanız gerekip gerekmediğini öğrenmek için [Microsoft Identity platform sınırlamaları](active-directory-v2-limitations.md)hakkında bilgi edinin.

Daha tipik *üç değerli OAuth*'da, bir istemci uygulamasına belirli bir kullanıcı adına bir kaynağa erişim izni verilir. İzin, genellikle izin işlemi sırasında kullanıcıdan uygulamaya devredilir. [](v2-permissions-and-consent.md) Ancak, istemci kimlik bilgileri (*iki taraflı OAuth*) akışında, izinler doğrudan uygulamanın kendisine verilir. Uygulama bir kaynağa belirteç sunduğunda, kaynak uygulamanın kendisinin Kullanıcı değil bir eylem gerçekleştirmek için yetkilendirmeye sahip olmasını zorunlu kılar.

## <a name="protocol-diagram"></a>Protokol diyagramı

Tüm istemci kimlik bilgileri akışı aşağıdaki diyagrama benzer şekilde görünür. Bu makalenin ilerleyen kısımlarında bulunan adımların her birini açıklıyoruz.

![İstemci kimlik bilgileri akışını gösteren diyagram](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Doğrudan yetkilendirmeyi al

Bir uygulama, genellikle bir kaynağa iki şekilde erişmek için doğrudan yetkilendirme alır:

* [Kaynak üzerinde bir erişim denetim listesi (ACL) üzerinden](#access-control-lists)
* [Azure AD 'de uygulama izni ataması aracılığıyla](#application-permissions)

Bu iki yöntem, Azure AD 'de en yaygın bir deyişle, istemci kimlik bilgileri akışını gerçekleştiren istemciler ve kaynaklar için önerilir. Bir kaynak, istemcilerini başka yollarla yetkilendirmeyi de seçebilir. Her kaynak sunucusu, uygulaması için en mantıklı hale getiren yöntemi seçebilir.

### <a name="access-control-lists"></a>Erişim denetimi listeleri

Bir kaynak sağlayıcısı, bildiği ve belirli bir erişim düzeyi veren uygulama (istemci) kimliklerinin listesini temel alan bir Yetkilendirme denetimini uygulayabilir. Kaynak Microsoft Identity platform uç noktasından bir belirteç aldığında, belirtecin kodunu çözebilir ve istemci uygulama kimliğini `appid` ve `iss` taleplerinden ayıklayabilir. Ardından uygulamayı, tuttuğu bir erişim denetim listesi (ACL) ile karşılaştırır. ACL 'nin ayrıntı düzeyi ve yöntemi kaynaklar arasında önemli ölçüde farklılık gösterebilir.

Yaygın kullanım durumu, bir Web uygulaması veya Web API 'SI için testler çalıştırmak üzere bir ACL kullanmaktır. Web API 'SI, belirli bir istemciye yönelik tam izinlerin yalnızca bir alt kümesini verebilir. API üzerinde uçtan uca testler çalıştırmak için, Microsoft Identity platform uç noktasından belirteçleri alan ve ardından bunları API 'ye gönderen bir test istemcisi oluşturun. Ardından API, API 'nin tüm işlevlerine tam erişim için test istemcisinin uygulama KIMLIĞI ACL 'sini denetler. Bu tür bir ACL kullanırsanız, yalnızca arayanın `appid` değerini değil, belirtecin `iss` değerinin güvenilir olduğunu da doğruladığınızdan emin olun.

Bu tür bir yetkilendirme, kişisel Microsoft hesapları olan tüketici kullanıcılarının sahip olduğu verilere erişmesi gereken Daemon 'ları ve hizmet hesapları için ortaktır. Kuruluşlara ait veriler için, uygulama izinleri aracılığıyla gerekli yetkilendirmeyi almanızı öneririz.

### <a name="application-permissions"></a>Uygulama izinleri

ACL 'Leri kullanmak yerine, uygulama izinleri kümesini kullanıma sunmak için API 'Leri kullanabilirsiniz. Uygulama izni bir kuruluşun yöneticisi tarafından uygulamaya verilir ve yalnızca söz konusu kuruluşa ve çalışanlarına ait olan verilere erişmek için kullanılabilir. Örneğin, Microsoft Graph aşağıdakileri yapmak için çeşitli uygulama izinleri sunar:

* Tüm posta kutularındaki postaları okuma
* Tüm posta kutularındaki postaları Okuma ve yazma
* Herhangi bir kullanıcı adına posta gönderme
* Dizin verilerini okuma

Uygulama izinleri hakkında daha fazla bilgi için [Microsoft Graph](https://developer.microsoft.com/graph)adresine gidin.

Uygulamanızda uygulama izinlerini kullanmak için, sonraki bölümlerde ele alınan adımları izleyin.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Uygulama kayıt portalında izinleri isteyin

1. Yeni [uygulama kayıtları (Önizleme) deneyimi](quickstart-register-app.md)aracılığıyla bir uygulamayı kaydedin ve oluşturun.
2. Uygulama kayıtları (Önizleme) deneyiminde uygulamanıza gidin. Bir belirteç istemek için en az bir gizli anahtar gerektiğinden, **sertifikalar & gizlilikler** bölümüne gidin ve **Yeni bir istemci gizli anahtarı**ekleyin.
3. **API izinleri** bölümünü bulun ve ardından uygulamanızın gerektirdiği **uygulama izinlerini** ekleyin.
4. Uygulama kaydını **kaydedin** .

#### <a name="recommended-sign-the-user-into-your-app"></a>Önerilen: Kullanıcıyı uygulamanızda imzalama

Genellikle, uygulama izinleri kullanan bir uygulama oluşturduğunuzda, uygulama, yöneticinin uygulamanın izinlerini onayladığı bir sayfa veya görünüm gerektirir. Bu sayfa, uygulamanın oturum açma akışının bir parçası, uygulamanın ayarlarının bir parçası olabilir veya adanmış bir "Connect" akışı olabilir. Çoğu durumda, uygulamanın bu "Bağlan" görünümünü yalnızca bir kullanıcı iş veya okul Microsoft hesabı oturum açtıktan sonra göstermesini mantıklı hale getirir.

Kullanıcıyı uygulamanıza imzalarsanız, kullanıcının uygulama izinlerini onaylamasını istemek için önce kullanıcının sahip olduğu kuruluşu belirleyebilirsiniz. Kesinlikle gerekli olmasa da, kullanıcılarınız için daha sezgisel bir deneyim oluşturmanıza yardımcı olabilir. Kullanıcı oturumu açmak için [Microsoft Identity platform protokol](active-directory-v2-protocols.md)öğreticilerimizi izleyin.

#### <a name="request-the-permissions-from-a-directory-admin"></a>Dizin yöneticisinden izinleri isteme

Kuruluşun yöneticisinden izin istemek için hazırsanız, kullanıcıyı Microsoft Identity Platform *Yöneticisi onay uç noktasına*yönlendirebilirsiniz.

> [!TIP]
> Bu isteği Postman 'da yürütmeyi deneyin! (En iyi sonuçları elde etmek için kendi uygulama KIMLIĞINIZI kullanın; öğretici uygulaması yararlı izinler istemez.) [![Bu isteği Postman 'da çalıştırmayı deneyin](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser.
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametre | Koşul | Açıklama |
| --- | --- | --- |
| `tenant` | Gerekli | İzin istemek istediğiniz dizin kiracısı. Bu, GUID veya kolay ad biçiminde olabilir. Kullanıcının hangi kiracıya ait olduğunu bilmiyorsanız ve herhangi bir kiracı ile oturum açmalarına izin vermek istiyorsanız kullanın `common`. |
| `client_id` | Gerekli | [Azure Portal – uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyiminin uygulamanıza atandığı **uygulama (istemci) kimliği** . |
| `redirect_uri` | Gerekli | Uygulamanızın işlenmesi için yanıtın gönderilmesini istediğiniz yeniden yönlendirme URI 'SI. Portalın, URL kodlamalı olması ve ek yol segmentlerine sahip olması dışında, portalda kaydettiğiniz yeniden yönlendirme URI 'lerinden biriyle tam olarak eşleşmesi gerekir. |
| `state` | Önerilen | İsteğin belirteç yanıtında de döndürülen bir değeri. İstediğiniz herhangi bir içerik dizesi olabilir. Durum, kullanıcının uygulamadaki durumu hakkında bilgi kodlamak için kullanılır; Örneğin, bulunan sayfa veya görünüm gibi kimlik doğrulama isteği gerçekleştirilmeden önce. |

Bu noktada, Azure AD yalnızca kiracı yöneticisinin isteği tamamlamada oturum açmasını zorunlu kılar. Yönetici, uygulama kayıt portalı 'nda uygulamanız için istediğiniz tüm doğrudan uygulama izinlerini onaylaması istenecektir.

##### <a name="successful-response"></a>Başarılı yanıt

Yönetici, uygulamanız için izinleri onayladığında, başarılı yanıt şöyle görünür:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametre | Açıklama |
| --- | --- |
| `tenant` | Uygulamanıza, istenen izinleri (GUID biçiminde) vermiş olan dizin kiracısı. |
| `state` | İstekte, belirteç yanıtında döndürülen bir değer. İstediğiniz herhangi bir içerik dizesi olabilir. Durum, kullanıcının uygulamadaki durumu hakkında bilgi kodlamak için kullanılır; Örneğin, bulunan sayfa veya görünüm gibi kimlik doğrulama isteği gerçekleştirilmeden önce. |
| `admin_consent` | **True**olarak ayarlayın. |

##### <a name="error-response"></a>Hata yanıtı

Yönetici, uygulamanız için izinleri onaylamadıysanız, başarısız yanıt şöyle görünür:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametre | Açıklama |
| --- | --- |
| `error` | Hata türlerini sınıflandırmak için kullanabileceğiniz ve hatalara yanıt vermek için kullanabileceğiniz bir hata kodu dizesi. |
| `error_description` | Bir hatanın temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |

Uygulama sağlama uç noktasından başarılı bir yanıt aldıktan sonra, uygulamanız istediği doğrudan uygulama izinlerini elde etti. Artık istediğiniz kaynak için bir belirteç isteyebilirsiniz.

## <a name="get-a-token"></a>Belirteç al

Uygulamanız için gerekli yetkilendirmeyi elde ettikten sonra API 'Ler için erişim belirteçleri alma ' ya ilerleyin. İstemci kimlik bilgileri verme işlemini kullanarak bir belirteç almak için `/token` Microsoft Identity platform uç noktasına bir post isteği gönderin:

> [!TIP]
> Bu isteği Postman 'da yürütmeyi deneyin! (En iyi sonuçları elde etmek için kendi uygulama KIMLIĞINIZI kullanın; öğretici uygulaması yararlı izinler istemez.) [![Bu isteği Postman 'da çalıştırmayı deneyin](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>İlk durum: Paylaşılan gizli dizi ile belirteç isteğine erişim

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
// Replace {tenant} with your tenant! 
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token'
```

| Parametre | Koşul | Açıklama |
| --- | --- | --- |
| `tenant` | Gerekli | Dizin, GUID veya etki alanı adı biçiminde işlem yapmak için uygulama planlarını kiralıyor. |
| `client_id` | Gerekli | Uygulamanıza atanan uygulama KIMLIĞI. Bu bilgileri, uygulamanızı kaydettiğiniz portalda bulabilirsiniz. |
| `scope` | Gerekli | Bu istekteki `scope` parametre için geçirilen değer, `.default` son ek ile yapıştırılmış, istediğiniz kaynağın kaynak tanımlayıcısı (uygulama kimliği URI 'si) olmalıdır. Microsoft Graph örnek için, değeri `https://graph.microsoft.com/.default`. <br/>Bu değer, Microsoft Identity platform uç noktasına, uygulamanız için yapılandırdığınız tüm doğrudan uygulama izinlerinin bir belirteç vermesini söyler ve uç noktanın, kullanmak istediğiniz kaynakla ilişkili olanlar için bir belirteç vermesi gerekir. `/.default` Kapsam hakkında daha fazla bilgi edinmek için bkz. [onay belgeleri](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Gerekli | Uygulama kayıt portalında uygulamanız için oluşturduğunuz istemci gizli anahtarı. İstemci parolası gönderilmeden önce URL kodlamalı olmalıdır. |
| `grant_type` | Gerekli | Ayarlanmalıdır `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>İkinci durum: Bir sertifikayla erişim belirteci isteği

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parametre | Koşul | Açıklama |
| --- | --- | --- |
| `tenant` | Gerekli | Dizin, GUID veya etki alanı adı biçiminde işlem yapmak için uygulama planlarını kiralıyor. |
| `client_id` | Gerekli |Uygulamanıza atanan uygulama (istemci) KIMLIĞI. |
| `scope` | Gerekli | Bu istekteki `scope` parametre için geçirilen değer, `.default` son ek ile yapıştırılmış, istediğiniz kaynağın kaynak tanımlayıcısı (uygulama kimliği URI 'si) olmalıdır. Microsoft Graph örnek için, değeri `https://graph.microsoft.com/.default`. <br/>Bu değer, uygulamanız için yapılandırdığınız tüm doğrudan uygulama izinlerinin Microsoft Identity platform uç noktasını bilgilendirir ve kullanmak istediğiniz kaynakla ilişkili olanlar için bir belirteç yayınlaması gerekir. `/.default` Kapsam hakkında daha fazla bilgi edinmek için bkz. [onay belgeleri](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Gerekli | Değerin olarak `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`ayarlanması gerekir. |
| `client_assertion` | Gerekli | Uygulamanız için kimlik bilgileri olarak kaydettiğiniz sertifikayı oluşturmanız ve oturum açmanız için gereken bir onaylama (JSON Web belirteci). Sertifikanızı ve onaylama biçiminizi nasıl kaydedeceğinizi öğrenmek için [sertifika kimlik bilgileri](active-directory-certificate-credentials.md) hakkında bilgi edinin.|
| `grant_type` | Gerekli | Ayarlanmalıdır `client_credentials`. |

Client_secret parametresi iki parametre ile değiştirilmeleri dışında, parametrelerin paylaşılan gizliliğe göre neredeyse aynı olduğuna dikkat edin: bir client_assertion_type ve client_assertion.

### <a name="successful-response"></a>Başarılı yanıt

Başarılı bir yanıt şöyle görünür:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parametre | Açıklama |
| --- | --- |
| `access_token` | İstenen erişim belirteci. Uygulama, bir Web API 'SI gibi güvenli kaynağın kimliğini doğrulamak için bu belirteci kullanabilir. |
| `token_type` | Belirteç türü değerini gösterir. Microsoft Identity platformunun desteklediği `bearer`tek tür. |
| `expires_in` | Bir erişim belirtecinin geçerli olduğu süre (saniye cinsinden). |

### <a name="error-response"></a>Hata yanıtı

Bir hata yanıtı şuna benzer:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametre | Açıklama |
| --- | --- |
| `error` | Oluşan hata türlerini sınıflandırmak ve hatalara tepki vermek için kullanabileceğiniz bir hata kodu dizesi. |
| `error_description` | Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |
| `error_codes` | Tanılamalarda yardımcı olabilecek STS 'ye özgü hata kodlarının listesi. |
| `timestamp` | Hatanın gerçekleştiği zaman. |
| `trace_id` | Tanılamalarla yardım eden istek için benzersiz bir tanımlayıcı. |
| `correlation_id` | Bileşenler genelinde tanılamalarla ilgili olarak size yardımcı olacak istek için benzersiz bir tanımlayıcı. |

## <a name="use-a-token"></a>Belirteç kullanma

Bir belirteç edindiniz, şimdi kaynağa istek yapmak için belirteci kullanın. Belirtecin süresi sona erdiğinde, yeni bir erişim belirteci almak `/token` için isteği uç noktaya yineleyin.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbG...." 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Kod örnekleri ve diğer belgeler

Microsoft kimlik doğrulama kitaplığı 'nda [istemci kimlik bilgilerine genel bakış belgelerini](https://aka.ms/msal-net-client-credentials) okuyun

| Örnek | Platform |Açıklama |
|--------|----------|------------|
|[Active-Directory-dotnetcore-Daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2,1 konsolu | Kullanıcı adına değil, uygulamanın kimliğini kullanarak Microsoft Graph sorgulayan bir kiracının kullanıcılarını görüntüleyen basit bir .NET Core uygulaması. Örnek ayrıca kimlik doğrulaması için sertifikaları kullanan çeşitlemesi gösterir. |
|[Active-Directory-DotNet-Daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Bir kullanıcı adına değil, uygulamanın kimliğini kullanarak Microsoft Graph verileri eşitlenen bir Web uygulaması. |
