---
title: Microsoft Identity platform UserInfo uç noktası | Mavisi
titleSuffix: Microsoft identity platform
description: Microsoft Identity platformunda UserInfo uç noktası hakkında bilgi edinin.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3bd0621d607b121f19bc47c717343b8e2e39b04f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98752726"
---
# <a name="microsoft-identity-platform-userinfo-endpoint"></a>Microsoft Identity platform UserInfo uç noktası

UserInfo uç noktası, kimliği doğrulanmış kullanıcı hakkında talepler döndürecek şekilde tasarlanan [OpenID Connect Standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) 'ın (OIDC) bir parçasıdır.  Microsoft Identity platformu için, UserInfo uç noktası Microsoft Graph ( https://graph.microsoft.com/oidc/userinfo) . 

## <a name="find-the-well-known-configuration-endpoint"></a>. İyi bilinen yapılandırma uç noktasını bulun

Üzerinde OpenID Connect bulma belgesini kullanarak, UserInfo uç noktasını programlı bir şekilde keşfedebilirsiniz `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` . `userinfo_endpoint`Bu, alanında listelenir ve sağ uç noktayı işaret eden bulutlar arasında bu model kullanılabilir.  Uygulamanızda UserInfo uç noktasının sabit kodlanmasını önermeyiz. bu uç noktayı bunun yerine çalışma zamanında bulmak için OıDC bulma belgesini kullanın.

OpenID Connect belirtiminin bir parçası olarak, Kullanıcı hakkında bilgi almak için, UserInfo uç noktası genellikle [OIDC uyumlu kitaplıklar](https://openid.net/developers/certified/)  tarafından otomatik olarak çağırılır.  Bu tür bir uç nokta barındırmadan, Microsoft Identity platformu standartlara uygun değildir ve bazı kitaplıklar başarısız olur.  [OıDC standardında tanımlanan talepler listesinden](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims) , kullanılabilir ve için kabul edildiğinde talepler, konu talebi ve e-posta şeklinde bir ad üretir.  

## <a name="consider-use-an-id-token-instead"></a>Göz önünde bulundurun: bunun yerine bir KIMLIK belirteci kullanın

Uygulamanızın alabileceği KIMLIK belirtecinde bulunan bilgiler, UserInfo uç noktasından elde alabileceği bilgilerin bir üst kümesidir.  Bir KIMLIK belirtecini, UserInfo uç noktasını çağırmaya yönelik bir belirteç aldığınızda aynı anda alabilmeniz için, bu KIMLIK belirtecini, Kullanıcı hakkında bilgi edinmek için, UserInfo uç noktasını çağırmak yerine kullanmanızı öneririz.  KIMLIK belirtecinin kullanılması, uygulamanızın başlatılması sırasında yapılan bir veya iki ağ isteğini ortadan kaldıracak ve uygulamanızda gecikme süresini azaltır.

Kullanıcı hakkında daha fazla ayrıntı isterseniz, Office numarası veya iş unvanı gibi bilgileri almak için [Microsoft Graph `/user` API](/graph/api/user-get) 'sini çağırmanız gerekir.   KIMLIK ve erişim belirteçlerine ek kullanıcı bilgileri eklemek için [isteğe bağlı talepler](active-directory-optional-claims.md) de kullanabilirsiniz.

## <a name="calling-the-userinfo-endpoint"></a>UserInfo uç noktasını çağırma

UserInfo, Microsoft Graph için belirteç alırken alınan erişim belirtecini kullanarak diğer Microsoft Graph API 'leri gibi çağrılan standart bir OAuth taşıyıcı belirteç API 'sidir. Kullanıcı hakkında talepler içeren bir JSON yanıtı döndürür.

### <a name="permissions"></a>İzinler

UserInfo API 'sini çağırmak için aşağıdaki [OIDC izinlerini](v2-permissions-and-consent.md#openid-connect-scopes) kullanın. `openid` gereklidir ve `profile` ve `email` kapsamları yanıtta ek bilgilerin sağlandığından emin olur.

|İzin türü      | İzinler    |
|:--------------------|:---------------------------------------------------------|
|Temsilci (iş veya okul hesabı) | OpenID (gerekli), profil, e-posta |
|Temsilci (kişisel Microsoft hesabı) | OpenID (gerekli), profil, e-posta |
|Uygulama | Uygulanamaz |

> [!TIP]
> UserInfo uç noktasının yanı sıra [kimlik belirtecinin](id-tokens.md) bir belirtecini almak IÇIN bu URL 'yi tarayıcınızda kopyalayın ve istemci kimliği ve yenıden yönlendirme URI 'sini kendi ile değiştirin. Yalnızca OpenID veya Graph kapsamları için kapsam isteğinde bulunduğunu ve başka hiçbir şey olduğunu unutmayın.  Aynı belirteç isteğindeki iki farklı kaynak için izin isteyebileceğinizden bu gereklidir.
>
> `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=<yourClientID>&response_type=token+id_token&redirect_uri=<YourRedirectUri>&scope=user.read+openid+profile+email&response_mode=fragment&state=12345&nonce=678910`
>
> Sonraki bölümde bu erişim belirtecini kullanabilirsiniz.

Diğer Microsoft Graph belirteçlerle olduğu gibi, burada aldığınız belirteç bir JWT olmayabilir. Microsoft hesabı kullanıcı oturumu açtıysanız, şifreli bir belirteç biçimi olur. Bunun nedeni Microsoft Graph özel bir belirteç verme deseninin olması. Bu, UserInfo uç noktasını çağırmak için erişim belirtecini kullanma yeteneğinizi etkilemez.

### <a name="calling-the-api"></a>API çağırma

UserInfo API 'SI, OıDC belirtimine göre hem GET hem de POST 'u destekler.

```http
GET or POST /oidc/userinfo HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6Il…
```

### <a name="userinfo-response"></a>UserInfo yanıtı

```jsonc
{
    "sub": "OLu859SGc2Sr9ZsqbkG-QbeLgJlb41KcdiPoLYNpSFA",
    "name": "Mikah Ollenburg", // names all require the “profile” scope.
    "family_name": " Ollenburg",
    "given_name": "Mikah",
    "email": "mikoll@contoso.com" //requires the “email” scope.
}
```

Burada listelenen talepler, UserInfo uç noktasının döndürebir bütün taleplerdir.  Bunlar, uygulamanın uygulamaya verilen [kimlik belirtecinde](id-tokens.md) göreceği aynı değerlerdir.  

## <a name="notes-and-caveats-on-the-userinfo-endpoint"></a>UserInfo uç noktasındaki notlar ve uyarılar

* Bu UserInfo bitiş noktasını çağırmak isterseniz, v 2.0 uç noktasını kullanmanız gerekir.  V 1.0 uç noktasını kullanırsanız, login.microsoftonline.com üzerinde barındırılan v 1.0 UserInfo uç noktası için bir belirteç alırsınız.  Uyumluluk sağlamak için tüm OıDC uyumlu uygulamaların ve kitaplıkların v 2.0 uç noktasını kullanmasını öneririz.
* UserInfo uç noktasındaki yanıt özelleştirilemiyor.  Talepleri özelleştirmek istiyorsanız, belirteçlerde döndürülen bilgileri düzenlemek için lütfen [talep eşlemesini]( active-directory-claims-mapping.md) kullanın.
* UserInfo uç noktasındaki yanıt eklenemiyor.  Kullanıcıyla ilgili ek talepler almak isterseniz, belirteçlere yeni talepler eklemek için lütfen [isteğe bağlı talepler]( active-directory-optional-claims.md) kullanın.

## <a name="next-steps"></a>Sonraki Adımlar

* [KIMLIK belirteçlerinin içeriğini gözden geçirin](id-tokens.md)
* [İsteğe bağlı talepler kullanarak bir KIMLIK belirtecinin içeriğini özelleştirme](active-directory-optional-claims.md)
* [OAuth2 protokolünü kullanarak erişim belirteci ve KIMLIK belirteci isteme](v2-protocols-oidc.md)
