---
title: Tarayıcıda daha az cihazda oturum açmak için Microsoft Identity platform 'u kullanın | Mavisi
description: Cihaz yetkilendirme yetkisini kullanarak gömülü ve tarayıcı için daha az kimlik doğrulama akışları oluşturun.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90922a48f213ecd506f08f616fe8c28ab44776a2
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893889"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft Identity platformu ve OAuth 2,0 cihaz yetkilendirmesi verme akışı

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Microsoft Identity platformu, kullanıcıların akıllı TV, IoT cihazı veya yazıcı gibi giriş kısıtlı cihazlarda oturum açmasına olanak tanıyan [cihaz yetkilendirme yetkisini](https://tools.ietf.org/html/rfc8628)destekler.  Bu akışı etkinleştirmek için, cihazda kullanıcının oturum açmasını sağlamak üzere başka bir cihazdaki tarayıcıda bir Web sayfasını ziyaret ettiği bir cihaz vardır.  Kullanıcı oturum açtıktan sonra cihaz, erişim belirteçlerini alabilir ve belirteçleri gerektiği şekilde yenileyebilir.  

> [!NOTE]
> Microsoft Identity platform uç noktası, tüm Azure Active Directory senaryolarını ve özelliklerini desteklemez. Microsoft Identity platform uç noktasını kullanmanız gerekip gerekmediğini öğrenmek için [Microsoft Identity platform sınırlamaları](active-directory-v2-limitations.md)hakkında bilgi edinin.

## <a name="protocol-diagram"></a>Protokol diyagramı

Tüm cihaz kod akışı, sonraki diyagrama benzer şekilde görünür. Bu makalenin ilerleyen kısımlarında bulunan adımların her birini açıklıyoruz.

![Cihaz kod akışı](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Cihaz yetkilendirme isteği

İstemcinin, kimlik doğrulamasını başlatmak için kullanılan bir cihaz ve Kullanıcı kodu için kimlik doğrulama sunucusunu denetlemesi gerekir. İstemci bu isteği `/devicecode` uç noktasından toplar. Bu istekte, istemci ayrıca kullanıcıdan almaları gereken izinleri de içermelidir. Bu isteğin gönderildiği andan itibaren, kullanıcının oturum açması için yalnızca 15 dakikası vardır (`expires_in`için her zamanki değer), bu nedenle yalnızca Kullanıcı oturum açmaya hazırsanız bu isteği yapın.

> [!TIP]
> Bu isteği Postman 'da yürütmeyi deneyin!
> [![bu isteği Postman 'da çalıştırmayı deneyin](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parametre | Koşul | Açıklama |
| --- | --- | --- |
| `tenant` | Gereklidir | /Common,/tüketicileri veya/Organizations. olabilir.  Ayrıca, GUID veya kolay ad biçiminde izin istemek istediğiniz dizin kiracısı de olabilir.  |
| `client_id` | Gereklidir | [Azure Portal – uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyiminin uygulamanıza atandığı **uygulama (istemci) kimliği** . |
| `scope` | Önerilen | Kullanıcının onay vermesini istediğiniz [kapsamların](v2-permissions-and-consent.md) , boşlukla ayrılmış bir listesi.  |

### <a name="device-authorization-response"></a>Cihaz yetkilendirme yanıtı

Başarılı bir yanıt, kullanıcının oturum açmasını sağlamak için gerekli bilgileri içeren bir JSON nesnesi olacaktır.  

| Parametre | Biçimlendir | Açıklama |
| ---              | --- | --- |
|`device_code`     | Dize | İstemci ile yetkilendirme sunucusu arasındaki oturumu doğrulamak için kullanılan uzun bir dize. İstemci, yetkilendirme sunucusundan erişim belirtecini istemek için bu parametreyi kullanır. |
|`user_code`       | Dize | Bir ikincil cihazda oturumu tanımlamak için kullanılan kullanıcıya gösterilen kısa bir dize.|
|`verification_uri`| KULLANıLMAMıŞSA | Kullanıcının oturum açmak için `user_code` ile gitmesi gereken URI. |
|`expires_in`      | int | `device_code` ve `user_code` süresi dolmadan önce geçmesi gereken saniye sayısı. |
|`interval`        | int | İstemcinin yoklama istekleri arasında beklemesi gereken saniye sayısı. |
| `message`        | Dize | Kullanıcı yönergelerine sahip, insan tarafından okunabilen bir dize. Bu, uygun dil kültür kodunu doldurarak `?mkt=xx-XX`form isteğine bir **sorgu parametresi** eklenerek yerelleştirilebilir. |

> [!NOTE]
> `verification_uri_complete` yanıtı alanı şu anda dahil değil veya desteklenmiyor.  Bunun nedeni, [Standart](https://tools.ietf.org/html/rfc8628) bir cihaz kod akışı standardının isteğe bağlı bir parçası olarak `verification_uri_complete` olduğunu görtiğimiz için bahsettik.

## <a name="authenticating-the-user"></a>Kullanıcının kimliğini doğrulama

`user_code` ve `verification_uri`aldıktan sonra istemci bunları kullanıcıya görüntüler ve bunları mobil telefonlarını veya bılgısayar tarayıcısını kullanarak oturum açmasını ister.

Kullanıcı bir kişisel hesapla kimlik doğrulaması gerçekleştiriyorsa (sık karşılaşılan veya/tüketicileri), kimlik doğrulama durumunun cihaza aktarılması için yeniden oturum açması istenir.  Ayrıca, verilen izinlerin farkında olduklarından emin olmak için onay sağlamaları istenir.  Bu, kimlik doğrulaması için kullanılan iş veya okul hesapları için geçerlidir. 

Kullanıcı `verification_uri`kimlik doğrulaması yaparken, istemci, `device_code`kullanarak istenen belirtecin `/token` uç noktasını yoklamalıdır.

``` 
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Parametre | Gereklidir | Açıklama|
| -------- | -------- | ---------- |
| `tenant`  | Gereklidir | İlk istekte aynı kiracı veya kiracı diğer adı kullanıldı. | 
| `grant_type` | Gereklidir | `urn:ietf:params:oauth:grant-type:device_code` olmalıdır|
| `client_id`  | Gereklidir | İlk istekte kullanılan `client_id` eşleşmelidir. |
| `device_code`| Gereklidir | Cihaz yetkilendirme isteğinde döndürülen `device_code`.  |

### <a name="expected-errors"></a>Beklenen hatalar

Cihaz kod akışı bir yoklama protokolüdür, bu nedenle kullanıcının kimlik doğrulaması tamamlanmadan önce hata alması beklenir.  

| Hata | Açıklama | İstemci eylemi |
| ------ | ----------- | -------------|
| `authorization_pending` | Kullanıcı kimlik doğrulamasını tamamlamadı, ancak akışı iptal edilmedi. | İsteği en az `interval` saniye sonra tekrarlayın. |
| `authorization_declined` | Son Kullanıcı Yetkilendirme isteğini reddetti.| Yoklamayı durdurun ve kimliği doğrulanmamış bir duruma dönün.  |
| `bad_verification_code`| `/token` uç noktasına gönderilen `device_code` tanınmadı. | İstemcinin istekte doğru `device_code` gönderdiğini doğrulayın. |
| `expired_token` | En az `expires_in` saniye geçti ve bu `device_code`kimlik doğrulaması artık mümkün değil. | Yoklamayı durdurun ve kimliği doğrulanmamış bir duruma dönün. |   

### <a name="successful-authentication-response"></a>Başarılı kimlik doğrulama yanıtı

Başarılı bir belirteç yanıtı şöyle görünür:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parametre | Biçimlendir | Açıklama |
| --------- | ------ | ----------- |
| `token_type` | Dize| Always "taşıyıcı. |
| `scope` | Boşlukla ayrılmış dizeler | Erişim belirteci döndürülürse, bu, erişim belirtecinin geçerli olduğu kapsamları listeler. |
| `expires_in`| int | Dahil edilen erişim belirtecinin geçerli olması için geçmesi gereken saniye sayısı. |
| `access_token`| Donuk dize | İstenen [kapsamlar](v2-permissions-and-consent.md) için verildi.  |
| `id_token`   | JWT | Özgün `scope` parametresi `openid` kapsamını içeriyorsa verildi.  |
| `refresh_token` | Donuk dize | Özgün `scope` parametresi `offline_access`dahil edilmek için verilir.  |

Yenileme belirtecini, [OAuth kod akışı belgelerinde](v2-oauth2-auth-code-flow.md#refresh-the-access-token)belgelenen akışı kullanarak yeni erişim belirteçleri elde etmek ve belirteçleri yenilemek için kullanabilirsiniz.  
