---
title: Geliştirdiğiniz istemci uygulamalarında kimlik doğrulama ve yetkilendirme esnekliği artırma
titleSuffix: Microsoft identity platform
description: Microsoft Identity platformunu kullanarak istemci uygulamasında kimlik doğrulama ve yetkilendirme esnekliğini artırma Kılavuzu
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: bc3b041e44fad66a4edc6ff34c0e534dc423de86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226599"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-client-applications-you-develop"></a>Geliştirdiğiniz istemci uygulamalarında kimlik doğrulama ve yetkilendirme esnekliği artırma

Bu bölümde, kullanıcıların oturum açması ve bu kullanıcılar adına eylemler gerçekleştirmek için Microsoft Identity platformu ve Azure Active Directory kullanan istemci uygulamalarına esnekliği oluşturmaya yönelik yönergeler verilmektedir.

## <a name="use-the-microsoft-authentication-library-msal"></a>Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanma

[Microsoft kimlik doğrulama kitaplığı (msal)](../develop/msal-overview.md) , [Microsoft Identity platformunun](../develop/index.yml)önemli bir parçasıdır. Belirteçleri alma, yönetme, önbelleğe alma ve yenileme işlemlerini basitleştirir ve yönetir ve esnekliği için en iyi yöntemleri kullanır. MSAL, geliştiricilerin uygulama ayrıntıları konusunda endişelenmenize gerek kalmadan güvenli bir çözümü etkinleştirmek üzere tasarlanmıştır.

MSAL önbellek belirteçleri ve sessiz belirteç alma modelini kullanır. Ayrıca, Windows UWP, iOS ve Android gibi güvenli depolamayı yerel olarak sağlayan platformlarda belirteç önbelleğini otomatik olarak serileştirir. Geliştiriciler [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization), [msal.net](../develop/msal-net-token-cache-serialization.md), [msal for Java](../develop/msal-java-token-cache-serialization.md)ve [Python için msal](../develop/msal-python-token-cache-serialization.md)kullanırken serileştirme davranışını özelleştirebilir.

![Microsoft kimliği 'ni çağırmak için MSAL kullanan ve uygulamanın bulunduğu cihaz görüntüsü](media/resilience-client-app/resilience-with-microsoft-authentication-library.png)

MSAL kullanıldığında, belirteç önbelleğe alma, yenileme ve sessiz alma otomatik olarak desteklenir. Modern kimlik doğrulaması için gereken belirteçleri elde etmek için basit desenleri kullanabilirsiniz. Birçok dili destekliyoruz ve [örnekler](../develop/sample-v2-code.md) sayfamızda diliniz ve senaryonuz ile eşleşen bir örnek bulabilirsiniz.

## <a name="c"></a>[C#](#tab/csharp)

```csharp
try
{
    result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
    result = await app.AcquireToken(scopes).WithClaims(ex.Claims).ExecuteAsync()
}
```

## <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
return myMSALObj.acquireTokenSilent(request).catch(error => {
    console.warn("silent token acquisition fails. acquiring token using redirect");
    if (error instanceof msal.InteractionRequiredAuthError) {
        // fallback to interaction when silent call fails
        return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
            console.log(tokenResponse);

            return tokenResponse;
        }).catch(error => {
            console.error(error);
        });
    } else {
        console.warn(error);
    }
});
```

---

MSAL bazı durumlarda belirteçleri önceden yenilemeyi sağlayabilir. Microsoft kimliği uzun süreli bir belirteç aldığında, belirteci yenilemek için en iyi süre için istemciye bilgi gönderebilir (" \_ içinde Yenile"). MSAL, belirteci bu bilgilere göre proaktif olarak yeniler. Uygulama, eski belirteç geçerli olduğu sürece çalışmaya devam eder, ancak başka bir başarılı belirteç alımı yapmak için daha uzun bir zaman çerçevesi olacaktır.

### <a name="stay-up-to-date"></a>Gelişmeleri takip edin

Geliştiricilerin en son MSAL sürümüne güncelleştirme için bir işlemi olması gerekir. Kimlik doğrulaması, uygulama güvenlerinizin bir parçasıdır ve yeni MSAL sürümlerinde bulunan güvenlik geliştirmeleri ile uygulamanızın güncel kalması gerekir. Bu işlem, sürekli geliştirme kapsamındaki kitaplıklar için genellikle iyi bir uygulamadır ve bunun yapılması, App esnekliği 'e göre en güncel koda sahip olmanızı sağlar. Microsoft kimliği, uygulamaların daha dayanıklı olması için yenilik yapın olmaya devam ettiğinden, en son MSAL kullanan uygulamalar bu yeniliklere en iyi şekilde hazırlanacaktır.

[En son MSAL.js sürümünü ve sürüm notlarını denetleyin](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[En son MSAL .NET sürümünü ve sürüm notlarını denetleyin](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases)

[En son MSAL Python sürümünü ve sürüm notlarını denetleyin](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases)

[En son MSAL Java sürümünü ve sürüm notlarını denetleyin](https://github.com/AzureAD/microsoft-authentication-library-for-java/releases)

[En son MSAL iOS ve macOS sürümünü ve sürüm notlarını denetleyin](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)

[En son MSAL Android sürümünü ve sürüm notlarını denetleyin](https://github.com/AzureAD/microsoft-authentication-library-for-android/releases)

[En son MSAL angular sürümünü ve sürüm notlarını denetleyin](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[En son Microsoft. Identity. Web sürümünü ve sürüm notlarını denetleyin](https://github.com/AzureAD/microsoft-identity-web/releases)

## <a name="use-resilient-patterns-for-token-handling"></a>Belirteç işleme için esnek desenler kullanma

MSAL kullanmıyorsanız, bu esnek desenleri belirteç işleme için kullanabilirsiniz. Bu en iyi uygulamalar MSAL kitaplığı tarafından otomatik olarak uygulanır. 

Genel olarak, modern kimlik doğrulaması kullanan bir uygulama, kullanıcının kimliğini doğrulayan veya uygulamayı korunan API 'Leri çağırmak üzere yetkilendirecek belirteçleri almak için bir uç nokta çağırır. MSAL, kimlik doğrulamanın ayrıntılarını işleyecek ve bu işlemin esnekliği ' i geliştirmek için çeşitli desenler uygulayan bir işlemdir. MSAL dışında bir kitaplık kullanmayı seçerseniz en iyi yöntemleri uygulamak için bu bölümdeki kılavuzu kullanın. MSAL kullanıyorsanız, MSAL bunları otomatik olarak uyguladığından bu en iyi uygulamaları ücretsiz olarak alırsınız.

### <a name="cache-tokens"></a>Önbellek belirteçleri

Uygulamalar Microsoft kimliğinden alınan belirteçleri düzgün bir şekilde önbelleğe almalıdır. Uygulamanız belirteçleri aldığında, belirteçleri içeren HTTP yanıtı, uygulamayı ne kadar süreyle önbelleğe alacağını ve belirteci yeniden kullanmayı söyleyen bir "expires_in" özelliği de içerir. Uygulamaların, belirtecin kullanım süresini belirleyebilmek için "expires_in" özelliğini kullanması önemlidir. Uygulamanın bir API erişim belirtecinin kodunu çözme girişimi hiçbir şekilde denenmelidir.

![Microsoft Identity çağrısı yapan bir uygulama, ancak çağrı, uygulamayı çalıştıran cihazdaki bir belirteç önbelleğinden geçer](media/resilience-client-app/token-cache.png)

Önbelleğe alınmış belirtecin kullanılması, uygulamanız ve Microsoft kimliğiniz arasında gereksiz trafiği engeller ve belirteç alma çağrılarının sayısını azaltarak uygulamanızın belirteç alma hatalarıyla daha az savunmasız olmasını sağlar. Önbelleğe alınmış belirteçler, uygulamanın belirteçleri daha az almak için engel olması gerektiğinden uygulamanızın performansını da artırır. Kullanıcılarınız, söz konusu belirtecin yaşam süresinin uzunluğu için uygulamanızda oturum açmış durumda kalabilir.

### <a name="serialize-and-persist-tokens"></a>Serileştirme ve kalıcı belirteçler

Uygulamalar, uygulama örnekleri arasındaki belirteçleri kalıcı hale getirmek için belirteç önbelleklerini güvenli bir şekilde serileştirmelidir. Belirteçler, geçerli yaşam sürelerinin içinde oldukları sürece yeniden kullanılabilir. [Belirteçleri yenileme](../develop/v2-oauth2-auth-code-flow.md#refresh-the-access-token)ve artan [erişim belirteçleri](../develop/access-tokens.md)pek çok saat için verilir. Bu geçerli saat, uygulamanızı birçok kez Başlatan bir kullanıcıya yayılabilir. Uygulamanız başlatıldığında, kullanılabilecek geçerli bir erişim veya yenileme belirteci olup olmadığını kontrol etmelidir. Bu, Microsoft kimliğine yönelik gereksiz çağrıların önlediği şekilde uygulamanın esnekliği ve performansını artırır.

![Microsoft Identity çağrısı yapan bir uygulama, ancak çağrı bir belirteç önbelleğinden ve uygulamayı çalıştıran cihazdaki belirteç depoından geçer](media/resilience-client-app/token-store.png)

Kalıcı belirteç depolama erişimi denetimli ve sahip olan kullanıcı veya işlem kimliğiyle şifrelenmiş olmalıdır. Mobil, Windows ve Mac gibi platformlarda geliştirici, kimlik bilgilerini depolamak için yerleşik özelliklerden yararlanmalıdır.

### <a name="acquire-tokens-silently"></a>Belirteçleri sessizce al

Bir kullanıcının kimliğini doğrulama veya bir API 'YI çağırmak için yetkilendirme alma işlemi, Microsoft kimliği 'nde birden çok adım gerektirebilir. Örneğin, Kullanıcı ilk kez oturum açtığında, kimlik bilgilerini girmesi ve bir kısa mesaj aracılığıyla çok faktörlü kimlik doğrulaması gerçekleştirmesi gerekebilir. Her adım, bu hizmeti sağlayan kaynağa bir bağımlılık ekler. Kullanıcı etkileşimi istenmeden önce bu ek adımların önüne geçmek için Kullanıcı için en iyi deneyim ve en az bağımlılıklara sahip bir belirteci sessizce almayı denemaktır.

![Bir kullanıcının kimlik doğrulama veya yetkilendirme işlemini tamamlaması için çalıştırılması gerekebilecek Microsoft kimliği içindeki çeşitli hizmetleri gösteren diyagram](media/resilience-client-app/external-dependencies.png)

Belirteç alma, uygulamanın belirteç önbelleğinden geçerli bir belirteç kullanarak sessizce başlar. Kullanılabilir geçerli bir belirteç yoksa, uygulama bir yenileme belirteci, varsa, belirteç uç noktası kullanarak bir belirteç edinmeyi denemelidir. Bu seçeneklerden hiçbiri yoksa, uygulamanın "Prompt = none" parametresini kullanarak bir belirteç edinmesi gerekir. Bu, yetkilendirme uç noktasını kullanır, ancak kullanıcı için herhangi bir kullanıcı ARABIRIMI göstermez. Microsoft kimliği, kullanıcıyla etkileşime girmeden uygulamaya belirteç sağlayabiliyorsanız, bu, olur. Bu yöntemlerin hiçbiri bir belirteç ile sonuçlanmazsa, kullanıcının etkileşimli olarak yeniden kimlik doğrulaması yapması gerekecektir.

> [!NOTE]
> Genel olarak, uygulamalar hiçbir etkileşim gerekmediği zaman bile kullanıcı etkileşimini zorlamalarına yönelik "Login" ve "onay" gibi istemler kullanmaktan kaçınmalıdır.

## <a name="handle-service-responses-properly"></a>Hizmet yanıtlarını doğru şekilde işleyin

Uygulamalar tüm hata yanıtlarını ele almalıdır, ancak esnekliği etkileyebilecek bazı yanıtlar vardır. Uygulamanız bir HTTP 429 yanıt kodu alırsa çok fazla Istek varsa, Microsoft kimliği isteklerinizi azaltmadır. Uygulamanız çok fazla istek yapmaya devam ederse, uygulamanızın belirteçleri almasına karşı kısıtlanmaya devam eder. Uygulamanız, Retry-After Response alanındaki süre geçtikten sonra saniye cinsinden bir belirteci almayı denememelidir. 429 yanıtının alınması genellikle uygulamanın önbelleğe alınmadığını ve belirteçleri doğru şekilde yeniden desteklemediğinin göstergesidir. Geliştiriciler, belirteçlerin nasıl önbelleğe alınacağını ve uygulamada nasıl yeniden kullanıldığını incelemelidir.

Bir uygulama bir HTTP 5xx yanıt kodu aldığında, uygulamanın hızlı bir yeniden deneme döngüsü girmemelidir. Mevcut olduğunda, uygulama, 429 yanıtı için yaptığı gibi aynı Retry-After işlemesini dikkate almalıdır. Yanıt tarafından hiçbir Retry-After üstbilgisi sağlanmazsa, yanıttan sonra en az 5 saniye önce yeniden deneme ile bir üstel geri dönme işlemi uygulanması önerilir.

İstek zaman aşımına uğramadan uygulamalar hemen yeniden denenmemelidir. Yanıt alındıktan sonra ilk yeniden denemeden önce en az 5 saniye sonra bir üstel geri dönme işlemi uygulayın.

## <a name="evaluate-options-for-retrieving-authorization-related-information"></a>Yetkilendirmeyle ilgili bilgileri alma seçeneklerini değerlendirin

Birçok uygulama ve API 'de, yetkilendirme kararları almak için Kullanıcı hakkında belirli bilgiler gerekir. Uygulamanın bu bilgileri alması için birkaç yol vardır. Her yöntemin avantajları ve dezavantajları vardır. Geliştiriciler, uygulamaları üzerinde esnekliği için hangi stratejinin en iyi olduğunu tespit etmelidir.

### <a name="tokens"></a>Belirteçler

Kimlik (ID) belirteçleri ve erişim belirteçleri, konu hakkında bilgi sağlayan standart talepler içerir. Bunlar [Microsoft Identity platform kimlik belirteçleri](../develop/id-tokens.md) ve [Microsoft Identity platform erişim belirteçlerinde](../develop/access-tokens.md)belgelenmiştir. Uygulamanızın ihtiyaç duyacağı bilgiler zaten belirteçte ise, bu verileri almak için en verimli yöntem, bilgileri ayrı olarak almak için ek bir ağ çağrısının fazla olduğunu kaydedecek şekilde belirteç taleplerini kullanmaktır. Daha az ağ çağrısı, uygulamanın daha yüksek genel esnekliği anlamına gelir.

> [!NOTE]
> Bazı uygulamalar, kimlik doğrulaması yapan kullanıcı hakkında talepler almak için UserInfo uç noktasını çağırır. Uygulamanızın alabileceği KIMLIK belirtecinde bulunan bilgiler, UserInfo uç noktasından elde alabileceği bilgilerin bir üst kümesidir. Uygulamanızın, UserInfo uç noktasını çağırmak yerine Kullanıcı hakkında bilgi almak için KIMLIK belirtecini kullanması gerekir.

Bir uygulama geliştiricisi, standart belirteç taleplerini [isteğe bağlı taleplerle](../develop/active-directory-optional-claims.md)artırabilir. Genel bir isteğe bağlı talep [gruplar](../develop/active-directory-optional-claims.md#configuring-groups-optional-claims)olur. Grup talepleri eklemenin birkaç yolu vardır. "Uygulama grubu" seçeneği yalnızca uygulamaya atanan grupları içerir. "Tümü" veya "güvenlik grupları" seçenekleri, aynı Kiracıdaki tüm uygulamalardan grupları içerir ve bu da belirtece birçok grup ekleyebilirler. Belirteçteki grupları isteyerek elde etmek önemlidir. bu durum, belirteç blobuna neden olur ve hatta grupların tam listesini almak için ek çağrılar gerektirir.

Belirtecinizdeki grupları kullanmak yerine uygulama rollerini kullanabilir ve dahil edebilirsiniz. Geliştiriciler, uygulamaları ve API 'Leri için, portalı veya API 'Leri kullanarak kendi dizininden yönetebileceği [uygulama rollerini](../develop/howto-add-app-roles-in-azure-ad-apps.md) tanımlayabilir. BT uzmanları, ne içerik ve işlevlere kimlerin erişebileceğini denetlemek için farklı kullanıcılara ve gruplara roller atayabilir. Uygulama veya API için bir belirteç verildiğinde, kullanıcıya atanan roller, belirteçte roller talebinde kullanılabilir olacaktır. Bu bilgilerin doğrudan bir belirteçte alınması, ek API çağrılarını kaydedebilir.

Son olarak, BT yöneticileri bir Kiracıdaki belirli bilgileri temel alarak talepler de ekleyebilirler. Örneğin, bir kuruluş, kuruluşa özgü bir kullanıcı KIMLIĞI olan bir uzantıya sahip olabilir.

Her durumda, dizinden doğrudan bir belirtece bilgi eklemek verimli olabilir ve uygulamanın sahip olduğu bağımlılıkların sayısını azaltarak uygulamalar esnekliği artırabilir. Öte yandan, bir belirteci elde etme esnekliği hiçbir sorunu gidermez. Yalnızca uygulamanızın ana senaryoları için isteğe bağlı talepler eklemeniz gerekir. Uygulama yalnızca yönetici işlevselliği için bilgi gerektiriyorsa, uygulamanın bu bilgileri yalnızca gerektiğinde alması en iyisidir.

### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph, bir kuruluştaki üretkenlik, kimlik ve güvenlik düzenlerini açıklayan Microsoft 365 verilerine erişmek için birleştirilmiş bir API uç noktası sağlar. Microsoft Graph kullanan uygulamalar, yetkilendirme kararları için Microsoft 365 üzerinde herhangi bir bilgiyi kullanabilir.

Uygulamalar, tüm Microsoft 365 erişmek için yalnızca tek bir belirteç gerektirir. Bu, birden çok belirtece gerek duyulan Microsoft Exchange veya Microsoft SharePoint gibi Microsoft 365 bileşenlere özgü eski API 'Lerin kullanılmasıyla daha dayanıklıdır.

Microsoft Graph API 'Leri kullanırken [Microsoft Graph SDK 'sını](/graph/sdks/sdks-overview)kullanmanızı öneririz. Microsoft Graph SDK 'Ları, Microsoft Graph erişen yüksek kaliteli, verimli ve dayanıklı uygulamalar oluşturmayı basitleştirmek üzere tasarlanmıştır.

Yetkilendirme kararları için, geliştiriciler bazı Microsoft Graph çağrılarına alternatif olarak bir belirteçte kullanılabilir talepleri ne zaman kullanacağınızı göz önünde bulundurmalıdır. Yukarıda belirtildiği gibi, geliştiriciler belirteçlerinde gruplar, uygulama rolleri ve isteğe bağlı talepler isteyebilir. Esnekliği açısından, yetkilendirme için Microsoft Graph kullanmak, Microsoft Identity kullanan ek ağ çağrılarının yanı sıra kendisini Microsoft Graph Microsoft Graph. Bununla birlikte, uygulamanız zaten veri katmanı olarak Microsoft Graph kullanıyorsa, yetkilendirme için grafiğe bağlı olarak daha fazla risk uygulanmaz.

## <a name="use-broker-authentication-on-mobile-devices"></a>Mobil cihazlarda aracı kimlik doğrulamasını kullanma

Mobil cihazlarda, Microsoft Authenticator gibi bir kimlik doğrulama Aracısı kullanılması esnekliği iyileştirir. Aracı, Sistem tarayıcısı veya katıştırılmış Web görünümü gibi diğer seçeneklerle kullanılabilir olan avantajlardan faydalar ekler. Kimlik doğrulama Aracısı, Kullanıcı ve cihaz hakkında talepler içeren bir [birincil yenileme belirtecini](../devices/concept-primary-refresh-token.md) (prt) kullanabilir ve cihazdaki diğer uygulamalara erişmek için kimlik doğrulama belirteçleri almak üzere kullanılabilir. Bir bir uygulamaya erişim istemek için bir PRT kullanıldığında, cihazı ve MFA talepleri Azure AD tarafından güvenilirdir. Bu, cihazın kimliğini doğrulamak için ek adımları önleyerek esnekliği artırır. Kullanıcılar, aynı cihazda birden çok MFA istemiyle karşılaşmaz, bu nedenle dış hizmetlerde bağımlılıkları azaltarak ve Kullanıcı deneyimini geliştirerek esnekliği artırır.

![Microsoft Identity çağrısı yapan bir uygulama, ancak çağrı bir belirteç önbelleğinden ve uygulamayı çalıştıran cihazdaki bir kimlik doğrulama aracısına göre geçer](media/resilience-client-app/authentication-broker.png)

Aracı kimlik doğrulaması MSAL tarafından otomatik olarak desteklenir. Aşağıdaki sayfalarda aracılı kimlik doğrulamasını kullanma hakkında daha fazla bilgi edinebilirsiniz:

- [MacOS ve iOS 'ta SSO 'yu yapılandırma](../develop/single-sign-on-macos-ios.md#sso-through-authentication-broker-on-ios)
- [MSAL kullanarak Android 'de uygulamalar arası SSO 'yu etkinleştirme](../develop/msal-android-single-sign-on.md)

## <a name="adopt-continuous-access-evaluation"></a>Sürekli erişim değerlendirmesini benimseyin

[Sürekli erişim değerlendirmesi (CAE)](../conditional-access/concept-continuous-access-evaluation.md) , uzun süreli belirteçlerle uygulama güvenliğini ve esnekliği artırabileceğiniz son bir geliştirmede yapılır. CAE, OpenID Foundation 'ın paylaşılan sinyalleri ve olaylar çalışma grubunda geliştirilen bir endüstri standardıdır. CAE ile, bir erişim belirteci, kısa bir belirteç ömrüne güvenmek yerine [kritik olaylara](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) ve [ilke değerlendirmesine](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview)bağlı olarak iptal edilebilir. Bazı kaynak API 'Lerinde, risk ve ilke gerçek zamanlı olarak değerlendirildiğinden CAE, belirteç ömrünü en fazla 28 saate kadar artırabilir. Kaynak API 'Leri ve uygulamalar CAE 'yi benimseyen, Microsoft kimliği, iptal edilen ve uzun süreler için geçerli olan erişim belirteçleri verebilir. Bu uzun süreli belirteçler, MSAL tarafından proaktif olarak yenilenir.

CAE, daha erken aşamalardayken, uygulama (API 'Ler) uygulamayı benimseyen kullandığında [CAE 'nin avantajlarından faydalanabilecek istemci uygulamaları geliştirmek](../develop/app-resilience-continuous-access-evaluation.md) mümkündür. Daha fazla kaynak Kae benimsedıkça, uygulamanız bu kaynaklar için de CAE etkin belirteçleri elde edebilir. Microsoft Graph API ve [Microsoft Graph SDK 'ları](/graph/sdks/sdks-overview), CAE kapasitesini erken 2021 önizleme edecektir. CAE ile Microsoft Graph genel önizlemesine katılmak isterseniz, ilginizi çekdiğinizi bize bildirmek için şunları yapabilirsiniz: [https://aka.ms/GraphCAEPreview](https://aka.ms/GraphCAEPreview) .

Kaynak API 'Leri geliştirirseniz, [paylaşılan sinyalleri ve olayları WG](https://openid.net/wg/sse/)'ye katılmayı öneririz. Microsoft kimliği ve kaynak sağlayıcıları arasında güvenlik olaylarının paylaşılmasını etkinleştirmek için bu grupla çalışıyoruz.

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulamalarınızda sürekli erişim değerlendirmesi etkin API 'Leri kullanma](../develop/app-resilience-continuous-access-evaluation.md)
- [Daemon uygulamalarına esnekliği derleme](resilience-daemon-app.md)
- [Kimlik ve erişim yönetimi altyapınızda esnekliği oluşturma](resilience-in-infrastructure.md)
- [CıHAR sistemlerinizde derleme esnekliği](resilience-b2c.md)
