---
title: Uygulamalarınızda sürekli erişim değerlendirmesi etkin API 'Leri kullanma | Mavisi
titleSuffix: Microsoft identity platform
description: Sürekli erişim değerlendirmesi için destek ekleyerek uygulama güvenliğini ve esnekliği artırma, önemli olaylar ve ilke değerlendirmesi temelinde iptal edilebilir uzun süreli erişim belirteçleri sağlar.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2020
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: f6ce792b3db0100d7356884bbc6ee2696580df10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652067"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>Uygulamalarınızda sürekli erişim değerlendirmesi etkin API 'Leri kullanma

[Sürekli erişim değerlendirmesi](../conditional-access/concept-continuous-access-evaluation.md) (CAE), erişim belirteçlerinin yaşam süresine bağlı olarak belirteç süre sonu güvenmek yerine [kritik olaylara](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) ve [ilke değerlendirmesine](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview) bağlı olarak iptal etmesine olanak tanıyan bir endüstri standardıdır. Bazı kaynak API 'Lerinde, risk ve ilke gerçek zamanlı olarak değerlendirildiğinden, bu belirteç ömrünü 28 saate kadar artırabilir. Bu uzun süreli belirteçler, Microsoft kimlik doğrulama kitaplığı (MSAL) tarafından proaktif olarak yenilenecektir ve uygulamalarınızın dayanıklılığını artırır.

Bu makalede, uygulamalarınızda CAE özellikli API 'Lerin nasıl kullanılacağı gösterilmektedir.

## <a name="implementation-considerations"></a>Uygulama konuları

Sürekli erişim değerlendirmesi kullanmak için hem uygulamanız hem de kaynak API 'sinin erişimi CAE etkin olmalıdır. Ancak, kodunuzun etkin bir kaynağı kullanacak şekilde hazırlanması, etkin olmayan API 'Leri kullanmanıza engel olmaz.

Bir kaynak API 'SI, CAE uygular ve uygulamanız CAE 'nin işlemesini bildirirse, uygulamanız bu kaynak için CAE belirteçlerini alır. Bu nedenle, uygulamanızı daha önce bildirirseniz, uygulamanızın Microsoft Identity Access belirteçlerini kabul eden tüm kaynak API 'Leri için CAE talep sınamasını işlemesi gerekir. Bu API çağrılarında CAE yanıtlarını işlemeyin, uygulamanız bir API çağrısını yeniden denemeye devam edebilir, ancak belirtecin döndürülen ömrü, ancak CAE nedeniyle iptal edildi.

## <a name="the-code"></a>Kod

İlk adım, kaynak API 'sinden bir yanıtı işleyecek şekilde, CAE nedeniyle çağrıyı reddetmeden kod eklemektir. CAE ile, erişim belirteci iptal edildiğinde veya API kullanılan IP adresinde bir değişiklik algıladığında API 'Ler 401 durumunu ve bir WWW-Authenticate üst bilgisini döndürür. WWW-Authenticate üst bilgisi, uygulamanın yeni bir erişim belirteci almak için kullanabileceği bir talep zorluğu içerir.

Örnek:

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
  authorization_uri="https://login.windows.net/common/oauth2/authorize",
  error="insufficient_claims",
  claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

Uygulamanızın şunları denetlemesi gerekir:

- 401 durumunu döndüren API çağrısı
- Şunu içeren bir WWW-Authenticate üst bilgisi var:
  - "insufficient_claims" değerine sahip "Error" parametresi
  - "talepler" parametresi

Bu koşullar karşılandığında, uygulama talep sınamasını ayıklayabilir ve kodunu çözebilir.

```csharp
if (APIresponse.IsSuccessStatusCode)
{
    // ...
}
else
{
    if (APIresponse.StatusCode == System.Net.HttpStatusCode.Unauthorized
        && APIresponse.Headers.WwwAuthenticate.Any())
    {
        AuthenticationHeaderValue bearer = APIresponse.Headers.WwwAuthenticate.First
            (v => v.Scheme == "Bearer");
        IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
        var error = GetParameter(parameters, "error");

        if (null != error && "insufficient_claims" == error)
        {
            var claimChallengeParameter = GetParameter(parameters, "claims");
            if (null != claimChallengeParameter)
            {
                var claimChallengebase64Bytes = System.Convert.FromBase64String(claimChallengeParameter);
                var claimChallenge = System.Text.Encoding.UTF8.GetString(claimChallengebase64Bytes);
                var newAccessToken = await GetAccessTokenWithClaimChallenge(scopes, claimChallenge);
```

Daha sonra uygulamanız, kaynak için yeni bir erişim belirteci almak üzere talep sınamasını kullanır.

```csharp
try
{
    authResult = await _clientApp.AcquireTokenSilent(scopes, firstAccount)
        .WithClaims(claimChallenge)
        .ExecuteAsync()
        .ConfigureAwait(false);
}
catch (MsalUiRequiredException)
{
    try
    {
        authResult = await _clientApp.AcquireTokenInteractive(scopes)
            .WithClaims(claimChallenge)
            .WithAccount(firstAccount)
            .ExecuteAsync()
            .ConfigureAwait(false);
    }
    // ...
```

Uygulamanız bir CAE etkin kaynak tarafından döndürülen talep sınamasını işlemeye hazırsa, Microsoft kimliğine uygulamanızın izin VERMIŞ olduğunu söyleyebilirsiniz. Bunu MSAL uygulamanızda yapmak için, "CP1" Istemci yeteneklerini kullanarak genel Istemcinizi oluşturun.

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

Kullanıcı oturumlarını Iptal etmek için Azure portal kullanarak uygulamanızı bir Kullanıcı oturumu açarak test edebilirsiniz. Uygulama bir dahaki sefer CAE etkin API 'yi çağırdığında, kullanıcıdan yeniden kimlik doğrulaması istenir.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [sürekli erişim değerlendirmesi](../conditional-access/concept-continuous-access-evaluation.md).
