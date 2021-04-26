---
title: Veren hizmeti iletişim örnekleri (Önizleme)-Azure Active Directory doğrulanabilir kimlik bilgileri
description: Kimlik sağlayıcısı ve veren hizmeti arasındaki iletişimin ayrıntıları
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 6aa502e1ed0e49192220174d5a8573690035a4a3
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739143"
---
# <a name="issuer-service-communication-examples-preview"></a>Veren hizmeti iletişim örnekleri (Önizleme)

Azure AD doğrulanabilir kimlik bilgisi hizmeti, kuruluşunuzun OpenID uyumlu kimlik sağlayıcısı tarafından oluşturulan bir KIMLIK belirtecinden talepleri alarak doğrulanabilir kimlik bilgileri verebilir. Bu makalede kimlik doğrulayıcı ile iletişim kurabilmesi ve veren hizmete geçirilecek doğru KIMLIK belirtecini alabilmesi için kimlik sağlayıcınızı nasıl ayarlayabileceğiniz açıklanır. 

> [!IMPORTANT]
> Azure Active Directory doğrulanabilir kimlik bilgileri şu anda genel önizlemede.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Doğrulanabilir bir kimlik bilgisi vermek için, kullanıcıdan girişi toplamak ve bu bilgileri veren hizmetine göndermek üzere sözleşmeyi indirmek yoluyla kimlik doğrulayıcı yapılır. KIMLIK belirteci kullanmanız gerekiyorsa, kimlik sağlayıcınızı kimlik doğrulayıcısının bir kullanıcının OpenID Connect protokolünü kullanarak oturum açmasını sağlamak için ayarlamanız gerekir. Elde edilen KIMLIK belirtecindeki talepler, doğrulanabilir kimlik bilgilerinizin içeriğini doldurmak için kullanılır. Kimlik Doğrulayıcı, OpenID Connect yetkilendirme kodu akışını kullanarak kullanıcının kimliğini doğrular. OpenID sağlayıcınızın aşağıdaki OpenID Connect özelliklerini desteklemesi gerekir: 

| Özellik | Açıklama |
| ------- | ----------- |
| Verme türü | Yetkilendirme kodu verme türünü desteklemelidir. |
| Belirteç biçimi | Şifrelenmemiş kompakt JWTs 'ler üretmelidir. |
| İmza algoritması | RS 256 kullanılarak imzalanmış JWTs 'leri üretmelidir. |
| Yapılandırma belgesi | OpenID Connect yapılandırma belgesi ve ile desteklenmesi gerekir `jwks_uri` . | 
| İstemci kaydı | , Bir değeri kullanılarak genel istemci kaydını desteklemelidir `redirect_uri` `vcclient://openid/` . | 
| PKCE | Güvenlik nedenleriyle önerilir, ancak gerekli değildir. |

Kimlik sağlayıcınızda gönderilen HTTP isteklerinin örnekleri aşağıda verilmiştir. Kimlik sağlayıcınız, OpenID Connect kimlik doğrulama standardına uygun olarak bu istekleri kabul etmeli ve bunlara yanıt vermelidir.

## <a name="client-registration"></a>İstemci kaydı

Doğrulanabilir bir kimlik bilgisi almak için, kullanıcılarınızın Microsoft Authenticator uygulamasından ıDP 'de oturum açması gerekir. 

Bu alışverişi etkinleştirmek için kimlik sağlayıcınızla bir uygulama kaydedin. Azure AD kullanıyorsanız, yönergeleri [buradan](../develop/quickstart-register-app.md)bulabilirsiniz. Kayıt sırasında aşağıdaki değerleri kullanın.

| Ayar | Değer |
| ------- | ----- |
| Uygulama adı | `<Issuer Name> Verifiable Credential Service` |
| Yeniden Yönlendirme URI'si | `vcclient://openid/ ` |


Kimlik sağlayıcınızda bir uygulamayı kaydettikten sonra, istemci KIMLIĞINI kaydedin. Bunu, aşağıdaki bölümde kullanacaksınız. Ayrıca, OıDC uyumlu kimlik sağlayıcısı için iyi bilinen uç noktaya URL 'YI de yazmanız gerekir. Veren hizmeti, Kimliği Doğrulayıcı tarafından gönderildiğinde kimlik belirtecini doğrulamak için gereken ortak anahtarları indirmek üzere bu uç noktayı kullanır.

Yapılandırılan yeniden yönlendirme URI 'SI, kimlik doğrulayıcı tarafından, oturum açma işlemi tamamlandığında ve KIMLIK belirtecini alabileceği bilmesini sağlamak için kullanılır. 

## <a name="authorization-request"></a>Yetkilendirme isteği

Kimlik sağlayıcınızda gönderilen yetkilendirme isteği aşağıdaki biçimi kullanır.

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| Parametre | Değer |
| ------- | ----------- |
| `client_id` | Uygulama kayıt işlemi sırasında alınan istemci KIMLIĞI. |
| `redirect_uri` | Kullanılmalıdır `vcclient://openid/` . |
| `response_mode` | Desteği gerekir `query` . |
| `response_type` | Desteği gerekir `code` . |
| `scope` | Desteği gerekir `openid` . |
| `state` | OpenID Connect standardına göre istemciye döndürülmelidir. |
| `nonce` | OpenID Connect standardına göre KIMLIK belirtecinde bir talep olarak döndürülmelidir. |

Bir yetkilendirme isteği aldığında, kimlik sağlayıcınız kullanıcının kimliğini doğrular ve çok faktörlü kimlik doğrulaması gibi, oturum açma işleminin tamamlanması için gereken tüm adımları almalıdır.

Oturum açma sürecini gereksinimlerinizi karşılayacak şekilde özelleştirebilirsiniz. Kullanıcılardan ek bilgiler sağlamasını, hizmet koşullarını kabul etmesini, kimlik bilgileri için ödeme yapmayı ve daha fazlasını yapmasını isteyebilirsiniz. Tüm adımlar tamamlandıktan sonra, aşağıda gösterildiği gibi yeniden yönlendirme URI 'sine yönlendirerek yetkilendirme isteğine yanıt verin. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| Parametre | Değer |
| ------- | ----------- |
| `code` |  Kimlik sağlayıcınız tarafından döndürülen yetkilendirme kodu. |
| `state` | OpenID Connect standardına göre istemciye döndürülmelidir. |

## <a name="token-request"></a>Belirteç isteği

Kimlik sağlayıcınızda gönderilen belirteç isteği aşağıdaki biçimde olacaktır.

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| Parametre | Değer |
| ------- | ----------- |
| `client_id` | Uygulama kayıt işlemi sırasında alınan istemci KIMLIĞI. |
| `redirect_uri` | Kullanılmalıdır `vcclient://openid/` . |
| `scope` | Desteği gerekir `openid` . |
| `grant_type` | Desteği gerekir `authorization_code` . |
| `code` | Kimlik sağlayıcınız tarafından döndürülen yetkilendirme kodu. |

Belirteç isteği alındığında, kimlik sağlayıcınız bir KIMLIK belirteciyle yanıt vermelidir.

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
"id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
    yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
    NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
    fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
    AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
    Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
    NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
    QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
    K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
    XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
}
```

KIMLIK belirtecinin JWT Compact serileştirme biçimini kullanması ve şifrelenmemelidir. KIMLIK belirteci aşağıdaki talepleri içermelidir.

| İste | Değer |
| ------- | ----------- |
| `kid` | OpenID sağlayıcıdaki bir girdiye karşılık gelen ID belirtecini imzalamak için kullanılan anahtarın anahtar tanımlayıcısı `jwks_uri` . |
| `aud` | Uygulama kayıt işlemi sırasında alınan istemci KIMLIĞI. |
| `iss` | `issuer`OpenID Connect yapılandırma belgenizdeki değer olmalıdır. |
| `exp` | KIMLIK belirtecinin süre sonu saatini içermelidir. |
| `iat` | , KIMLIK belirtecinin verildiği saati içermelidir. |
| `nonce` | Yetkilendirme isteğine dahil edilen değer. |
| Ek talepler | KIMLIK belirteci, verilecek olan doğrulanabilir kimlik bilgisine dahil edilecek ek talepler içermelidir. Bu bölüm, Kullanıcı hakkında, adı gibi tüm öznitelikleri eklemeniz gereken yerdir. |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory doğrulanabilir kimlik bilgilerinizi özelleştirme](credential-design.md)
