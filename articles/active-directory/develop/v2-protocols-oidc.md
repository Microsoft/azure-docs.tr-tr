---
title: Microsoft Identity platform ve OpenID Connect Protocol | Mavisi
description: OpenID Connect kimlik doğrulama protokolünün Microsoft Identity platform uygulamasını kullanarak Web uygulamaları oluşturun.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69aa2da29e18f99e75e09d8f21814b71cc95ef72
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852138"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft Identity platform ve OpenID Connect Protokolü

OpenID Connect, OAuth 2,0 üzerinde oluşturulmuş bir kimlik doğrulama protokolüdür ve bir kullanıcıyı bir Web uygulamasında güvenli bir şekilde oturum açmak için kullanabilirsiniz. OpenID Connect 'in Microsoft Identity platform uç noktasının uygulamasını kullandığınızda, Web tabanlı uygulamalarınıza oturum açma ve API erişimi ekleyebilirsiniz. Bu makalede, bu dilden bağımsız olarak nasıl yapılacağı ve herhangi bir Microsoft açık kaynak kitaplığı kullanmadan HTTP iletileri gönderme ve alma işlemlerinin nasıl yapılacağı açıklanır.

> [!NOTE]
> Microsoft Identity platform uç noktası, tüm Azure Active Directory (Azure AD) senaryolarını ve özelliklerini desteklemez. Microsoft Identity platform uç noktasını kullanmanız gerekip gerekmediğini öğrenmek için [Microsoft Identity platform sınırlamaları](active-directory-v2-limitations.md)hakkında bilgi edinin.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) , OAuth 2,0 *Yetkilendirme* protokolünü bir *kimlik doğrulama* protokolü olarak kullanılmak üzere genişletir. böylece, OAuth kullanarak çoklu oturum açma yapabilirsiniz. OpenID Connect, istemcinin kullanıcı kimliğini doğrulamasına izin veren bir güvenlik belirteci olan *kimlik belirteci*kavramını tanıtır. KIMLIK belirteci, kullanıcı hakkındaki temel profil bilgilerini de alır. OpenID Connect OAuth 2,0 ' i genişlettiğinden, uygulamalar bir [yetkilendirme sunucusu](active-directory-v2-protocols.md#the-basics)tarafından güvenliği sağlanmış olan kaynaklara erişmek için kullanılabilen *erişim belirteçlerini*güvenli bir şekilde alabilir. Microsoft Identity platform uç noktası Ayrıca, Azure AD 'ye kayıtlı üçüncü taraf uygulamaların, Web API 'Leri gibi güvenli kaynaklar için erişim belirteçleri vermesine olanak tanır. Erişim belirteçleri vermek üzere bir uygulama ayarlama hakkında daha fazla bilgi için bkz. [Microsoft Identity platform uç noktası ile uygulama kaydetme](quickstart-register-app.md). Sunucuda barındırılan ve bir tarayıcı aracılığıyla erişilebilen bir [Web uygulaması](v2-app-types.md#web-apps) oluşturuyorsanız OpenID Connect kullanmanız önerilir.

## <a name="protocol-diagram-sign-in"></a>Protokol diyagramı: Oturum açma

En temel oturum açma akışı, sonraki diyagramda gösterilen adımlara sahiptir. Her adım bu makalede ayrıntılı olarak açıklanmaktadır.

![OpenID Connect protokolü: Oturum açma](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>OpenID Connect meta veri belgesini getir

OpenID Connect, bir uygulamanın oturum açması için gereken bilgilerin çoğunu içeren bir meta veri belgesi tanımlar. Bu, kullanılacak URL 'Ler ve hizmetin ortak imzalama anahtarlarının konumu gibi bilgileri içerir. Microsoft Identity platform uç noktası için, bu, kullanmanız gereken OpenID Connect meta veri belgesidir:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP]
> Deneyin! Kiracılar`common` yapılandırmasını görmek için tıklayın [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) .

`{tenant}` Dört değerden birini alabilir:

| Value | Açıklama |
| --- | --- |
| `common` |Azure AD 'den hem kişisel Microsoft hesabı hem de iş veya okul hesabı olan kullanıcılar uygulamada oturum açabilir. |
| `organizations` |Yalnızca Azure AD 'den iş veya okul hesabı olan kullanıcılar uygulamada oturum açabilir. |
| `consumers` |Yalnızca kişisel Microsoft hesabı kullanıcılar uygulamada oturum açabilir. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` veya `contoso.onmicrosoft.com` | Yalnızca belirli bir Azure AD kiracısından gelen kullanıcılar (iş veya okul hesabı ile dizinde üye olup olmadıkları veya bir kişisel Microsoft hesabı olan dizinde konuklardır) uygulamada oturum açabilir. Azure AD kiracının kolay etki alanı adı ya da kiracının GUID tanımlayıcısı kullanılabilir. Ayrıca, `consumers` kiracının yerine tüketici kiracısını `9188040d-6c67-4c5b-b112-36a304b66dad`de kullanabilirsiniz.  |

Meta veriler basit bir JavaScript Nesne Gösterimi (JSON) belgesidir. Örnek için aşağıdaki kod parçacığına bakın. Kod parçacığının içeriği [OpenID Connect belirtiminde](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2)tamamen açıklanmıştır.

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/{tenant}\/discovery\/v2.0\/keys",

  ...

}
```

Uygulamanızda, [talep eşleme](active-directory-claims-mapping.md) özelliğini kullanmanın bir sonucu olarak özel İmzalama anahtarları varsa, uygulamanızın imzalama anahtarı bilgilerine `appid` `jwks_uri` işaret etmek için uygulama kimliğini içeren bir sorgu parametresi eklemeniz gerekir. Örneğin: `https://login.microsoftonline.com/{tenant}/.well-known/v2.0/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` `jwks_uri` biriçerir.`https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e`

Genellikle, bu meta veri belgesini bir OpenID Connect kitaplığı veya SDK 'Sı yapılandırmak için kullanacaksınız; Kitaplık, işini yapmak için meta verileri kullanır. Ancak, önceden oluşturulmuş bir OpenID Connect kitaplığı kullanmıyorsanız, Microsoft Identity platform uç noktasını kullanarak bir Web uygulamasında oturum açmak için bu makalenin geri kalanında bulunan adımları izleyebilirsiniz.

## <a name="send-the-sign-in-request"></a>Oturum açma isteğini gönder

Web uygulamanızın kimlik doğrulaması yapması gerektiğinde, kullanıcıyı `/authorize` uç noktaya yönlendirebilir. Bu istek, [OAuth 2,0 yetkilendirme kodu akışının](v2-oauth2-auth-code-flow.md)ilk bamasına benzer ve bu önemli farklılıklarla benzerdir:

* İstek, `openid` `scope` parametresine kapsamı içermelidir.
* `response_type` Parametrenin içermesi`id_token`gerekir.
* İstek, `nonce` parametresini içermelidir.

> [!IMPORTANT]
> /Authorization uç noktasından bir kimlik belirteci istemek için, [kayıt portalındaki](https://portal.azure.com) uygulama kaydının kimlik doğrulama sekmesinde etkin id_tokens ' `oauth2AllowIdTokenImplicitFlow` [in örtük izni olması gerekir (Bu bayrak, uygulama bildirimi](reference-app-manifest.md) `true`). Etkin değilse bir `unsupported_response` hata döndürülür: "' Response_type ' giriş parametresi için belirtilen değere bu istemci için izin verilmiyor. Beklenen değer ' Code ' "

Örneğin:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> Bu isteği yürütmek için aşağıdaki bağlantıya tıklayın. Oturum açtıktan sonra tarayıcınız `https://localhost/myapp/`, adres çubuğunda bir kimlik belirteci ile yeniden yönlendirilir. Bu isteğin (yalnızca tanıtım `response_mode=fragment` amacıyla) kullandığını unutmayın. Kullanmanızı `response_mode=form_post`öneririz.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| Parametre | Koşul | Açıklama |
| --- | --- | --- |
| `tenant` | Gerekli | Uygulamanın oturum açmasını denetlemek `{tenant}` için isteğin yolundaki değeri kullanabilirsiniz. İzin verilen değerler, `common`, `organizations`ve `consumers`kiracı tanımlayıcılarıdır. Daha fazla bilgi için bkz. [protokol temelleri](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Gerekli | [Azure Portal – uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) deneyiminin uygulamanıza atandığı **uygulama (istemci) kimliği** . |
| `response_type` | Gerekli | OpenID Connect oturum açma içiniçermelidir.`id_token` Ayrıca, `response_type` `code`gibi diğer değerleri de içerebilir. |
| `redirect_uri` | Önerilen | Uygulamanızın kimlik doğrulama yanıtlarının gönderilebileceği ve alınabileceği, uygulamanızın yeniden yönlendirme URI 'SI. Portalın, URL kodlamalı olması dışında, portala kaydettiğiniz yeniden yönlendirme URI 'lerinden biriyle tam olarak eşleşmesi gerekir. Mevcut değilse, uç nokta, kullanıcıyı öğesine geri göndermek için rastgele bir kayıtlı redirect_uri seçer. |
| `scope` | Gerekli | Kapsamların boşlukla ayrılmış listesi. OpenID Connect için, izin Kullanıcı arabirimindeki "oturum `openid`aç" iznine çeviren kapsamı içermelidir. Bu istekte izin istemek için diğer kapsamları da dahil edebilirsiniz. |
| `nonce` | Gerekli | Talep olarak elde edilen id_token değerine dahil edilecek, uygulama tarafından oluşturulan, isteğe dahil edilen bir değer. Uygulama, belirteç yeniden yürütme saldırılarını azaltmak için bu değeri doğrulayabilirler. Değer genellikle, isteğin kaynağını belirlemek için kullanılabilecek rastgele, benzersiz bir dizedir. |
| `response_mode` | Önerilen | Elde edilen yetkilendirme kodunu uygulamanıza geri göndermek için kullanılması gereken yöntemi belirtir. `form_post` veya `fragment` olabilir. Web uygulamaları için, belirteçlerin uygulamanıza `response_mode=form_post`en güvenli şekilde aktarılmasını sağlamak üzere kullanmanızı öneririz. |
| `state` | Önerilen | İstekte bulunan ve belirteç yanıtında de döndürülen bir değer. İstediğiniz herhangi bir içerik dizesi olabilir. Genellikle, [siteler arası istek sahteciliği saldırıları engellemek](https://tools.ietf.org/html/rfc6749#section-10.12)için rastgele oluşturulan benzersiz bir değer kullanılır. Durum Ayrıca, kullanıcının uygulamadaki durumuyla ilgili bilgileri, örneğin, Kullanıcı veya görüntüleme gibi kimlik doğrulama isteği yapılmadan önce kodlamak için de kullanılır. |
| `prompt` | İsteğe Bağlı | Gerekli kullanıcı etkileşiminin türünü gösterir. Şu anda yalnızca geçerli değerler, `login` `none`ve `consent`' dir. `prompt=login` Talep, kullanıcıyı bu istek üzerine kimlik bilgilerini girmeye zorlar ve çoklu oturum açma işlemini geçersiz kılar. `prompt=none` Talep, tersidir. Bu talep, kullanıcının ' de etkileşimli bir istem ile sunulmamasını sağlar. İstek çoklu oturum açma yoluyla sessizce tamamlanamayacak, Microsoft Identity platform uç noktası bir hata döndürür. `prompt=consent` Talep, Kullanıcı oturum açtıktan sonra OAuth onay iletişim kutusunu tetikler. İletişim kutusu kullanıcıdan uygulamaya izin vermesini ister. |
| `login_hint` | İsteğe Bağlı | Bu parametreyi, kullanıcının Kullanıcı adı ve e-posta adresi alanını daha önce biliyorsanız, Kullanıcı için oturum açma sayfasının Kullanıcı adı ve e-posta adresi alanını önceden doldurmanız için kullanabilirsiniz. Uygulamalar genellikle bu parametreyi, `preferred_username` talebi kullanarak daha önceki bir oturum açma işleminden zaten ayıklandıktan sonra yeniden kimlik doğrulama sırasında kullanır. |
| `domain_hint` | İsteğe Bağlı | Bir Federasyon dizinindeki kullanıcının bölgesi.  Bu, kullanıcının oturum açma sayfasında, biraz daha kolay bir kullanıcı deneyimi için gittiği e-posta tabanlı bulma işlemini atlar. AD FS gibi şirket içi bir dizin aracılığıyla federe olan kiracılar için, bu genellikle mevcut oturum açma oturumu nedeniyle sorunsuz bir oturum açma ile sonuçlanır. |

Bu noktada kullanıcıdan kimlik bilgilerini girmesi ve kimlik doğrulamasını tamamlaması istenir. Microsoft Identity platform uç noktası, kullanıcının `scope` sorgu parametresinde belirtilen izinlere onay verildiğini doğrular. Kullanıcı bu izinlerden birine onay vermediyse, Microsoft Identity platform uç noktası kullanıcıdan gerekli izinleri vermesini ister. [İzinler, onay ve çok kiracılı uygulamalar](v2-permissions-and-consent.md)hakkında daha fazla bilgi edinebilirsiniz.

Kullanıcı kimlik doğrulamasından ve onay verdikten sonra, `response_mode` parametrede belirtilen yöntemi kullanarak, belirtilen yeniden yönlendirme URI 'sindeki uygulamanıza bir yanıt döndürür.

### <a name="successful-response"></a>Başarılı yanıt

Kullanırken başarılı bir yanıt `response_mode=form_post` şu şekilde görünür:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parametre | Açıklama |
| --- | --- |
| `id_token` | Uygulamanın istediği KIMLIK belirteci. Kullanıcının kimliğini doğrulamak ve `id_token` kullanıcıyla oturum başlatmak için parametresini kullanabilirsiniz. Kimlik belirteçleri ve içerikleri hakkında daha fazla bilgi için bkz [ `id_tokens` . başvuru](id-tokens.md). |
| `state` | İsteğe bir `state` parametre dahil ise, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta durum değerlerinin özdeş olduğunu doğrulamalıdır. |

### <a name="error-response"></a>Hata yanıtı

Uygulamanın bunları işleyebilmesi için yeniden yönlendirme URI 'sine de hata yanıtları gönderilebilir. Bir hata yanıtı şuna benzer:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametre | Açıklama |
| --- | --- |
| `error` | Oluşan hata türlerini sınıflandırmak ve hatalara tepki vermek için kullanabileceğiniz bir hata kodu dizesi. |
| `error_description` | Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |

### <a name="error-codes-for-authorization-endpoint-errors"></a>Yetkilendirme uç noktası hataları için hata kodları

Aşağıdaki tabloda hata yanıtının `error` parametresinde döndürülebilecek hata kodları açıklanmaktadır:

| Hata kodu | Açıklama | İstemci eylemi |
| --- | --- | --- |
| `invalid_request` | Eksik, gerekli bir parametre gibi protokol hatası. |İsteği onarın ve yeniden gönderin. Bu, genellikle ilk sınama sırasında yakalanan bir geliştirme hatasıdır. |
| `unauthorized_client` | İstemci uygulaması bir yetkilendirme kodu isteğinde bulunamıyor. |Bu durum genellikle istemci uygulaması Azure AD 'de kayıtlı olmadığında veya kullanıcının Azure AD kiracısına eklenmediğinde oluşur. Uygulama kullanıcıya uygulamayı yüklemek ve Azure AD 'ye eklemek için yönergeler isteyebilir. |
| `access_denied` | Kaynak sahibi onay reddedildi. |İstemci uygulaması, kullanıcıya Kullanıcı tarafından bağlanmadığı takdirde devam edemediği konusunda bildirimde bulunabilir. |
| `unsupported_response_type` |Yetkilendirme sunucusu istekteki yanıt türünü desteklemiyor. |İsteği onarın ve yeniden gönderin. Bu, genellikle ilk sınama sırasında yakalanan bir geliştirme hatasıdır. |
| `server_error` | Sunucu beklenmeyen bir hatayla karşılaştı. |İsteği yeniden deneyin. Bu hatalar geçici koşullardan kaynaklanabilir. İstemci uygulaması, geçici bir hata nedeniyle yanıtı geciktirildiği kullanıcıya açıklanmayabilir. |
| `temporarily_unavailable` | Sunucu, isteği işlemek için geçici olarak çok meşgul. |İsteği yeniden deneyin. İstemci uygulaması, geçici bir durum nedeniyle yanıtı geciktirildiği kullanıcıya açıklanmayabilir. |
| `invalid_resource` | Hedef kaynak geçersiz, çünkü mevcut değil, Azure AD bu dosyayı bulamıyor veya doğru şekilde yapılandırılmamış. |Bu, varsa kaynağın kiracıda yapılandırılmadığı anlamına gelir. Uygulama kullanıcıya uygulamayı yükleme ve Azure AD 'ye ekleme yönergelerini isteyebilir. |

## <a name="validate-the-id-token"></a>KIMLIK belirtecini doğrulama

Yalnızca bir id_token almak, kullanıcının kimliğini doğrulamak için yeterli değildir; id_token's imzasını doğrulamanız ve uygulamanızın gereksinimlerine göre belirteçteki talepleri doğrulamanız gerekir. Microsoft Identity platform uç noktası belirteçleri imzalamak ve bunların geçerli olduğunu doğrulamak için [JSON Web belirteçleri (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) ve ortak anahtar şifrelemesi kullanır.

İstemci kodunda doğrulamayı `id_token` seçebilirsiniz, ancak bunu bir arka uç sunucusuna `id_token` göndermek ve doğrulama yapmak için sık kullanılan bir uygulamadır. İd_token imzasını doğruladıktan sonra, doğrulamanız gereken birkaç talep vardır. [Belirteçleri doğrulama](id-tokens.md#validating-an-id_token) ve [anahtar geçişi imzalama hakkındaki önemli bilgileri](active-directory-signing-key-rollover.md)içeren daha fazla bilgi için [ başvuruyabakın.`id_token` ](id-tokens.md) Belirteçleri ayrıştırma ve doğrulama için bir kitaplık kullanmanızı öneririz. çoğu dil ve platform için en az bir kullanılabilir.

Senaryonuza bağlı olarak ek talepler de doğrulamak isteyebilirsiniz. Bazı ortak doğrulamalar şunları içerir:

* Kullanıcı/kuruluşun uygulama için kaydolmasını sağlama.
* Kullanıcının uygun yetkilere/ayrıcalıklara sahip olmasını sağlama
* Çok faktörlü kimlik doğrulaması gibi belirli bir kimlik doğrulaması kuvvetinin gerçekleştiği doğrulanıyor.

İd_token öğesini doğrulandıktan sonra, Kullanıcı ile oturum başlatabilir ve uygulamanızdaki Kullanıcı hakkında bilgi edinmek için id_token 'teki talepleri kullanabilirsiniz. Bu bilgiler, görüntüleme, kayıtlar, kişiselleştirme vb. için kullanılabilir.

## <a name="send-a-sign-out-request"></a>Oturum kapatma isteği gönder

Kullanıcının uygulamanızdan oturumu kapatmak istediğinizde, uygulamanızın tanımlama bilgilerini temizlemek veya Kullanıcı oturumunu sonlandırmak yeterli değildir. Ayrıca oturumu kapatmak için kullanıcıyı Microsoft Identity platform uç noktasına yönlendirmeniz gerekir. Bunu yapmazsanız, Kullanıcı Microsoft Identity platform uç noktasıyla geçerli bir çoklu oturum açma oturumuna sahip olduklarından, kimlik bilgilerini tekrar girmeden uygulamanızı yeniden doğrular.

Kullanıcıyı OpenID Connect meta veri belgesinde `end_session_endpoint` listelenen öğesine yeniden yönlendirebilirsiniz:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parametre | Koşul | Açıklama |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | Önerilen | Başarıyla oturum kapatıldıktan sonra kullanıcının yeniden yönlendirildiği URL. Parametresi dahil edilmemişse, kullanıcıya Microsoft Identity platform uç noktası tarafından oluşturulan genel bir ileti gösterilir. Bu URL, uygulama kayıt portalı 'nda uygulamanız için kayıtlı olan yeniden yönlendirme URI 'lerinden biriyle aynı olmalıdır. |

## <a name="single-sign-out"></a>Çoklu oturum kapatma

Kullanıcıyı uygulamasına `end_session_endpoint`yönlendirirseniz, Microsoft Identity platform uç noktası kullanıcının oturumunu tarayıcıdan temizler. Ancak Kullanıcı, kimlik doğrulaması için Microsoft hesapları kullanan diğer uygulamalarda oturum açmış olabilir. Bu uygulamaların kullanıcı tarafından aynı anda oturum açmasını sağlamak için, Microsoft Identity platform uç noktası, kullanıcının şu anda oturum açmış olduğu tüm `LogoutUrl` uygulamalara kayıtlı bir http get isteği gönderir. Uygulamalar, kullanıcıyı tanıtan ve `200` yanıt döndüren tüm oturumları temizleyerek bu isteğe yanıt vermelidir. Uygulamanızda çoklu oturum açmayı desteklemek istiyorsanız, uygulamanızın kodunda böyle bir `LogoutUrl` uygulama uygulamanız gerekir. Uygulamasını uygulama kayıt portalından `LogoutUrl` ayarlayabilirsiniz.

## <a name="protocol-diagram-access-token-acquisition"></a>Protokol diyagramı: Erişim belirteci alımı

Birçok Web uygulamasının ' de kullanıcıyı imzalayıp Kullanıcı adına OAuth kullanarak bir Web hizmetine erişmesi gerekir. Bu senaryo, OAuth yetkilendirme kodu akışını kullanıyorsanız erişim belirteçlerini almak için kullanabileceğiniz bir yetkilendirme kodu elde ederken, Kullanıcı kimlik doğrulaması için OpenID Connect 'i birleştirir.

Tam OpenID Connect oturum açma ve belirteç alma akışı, sonraki diyagrama benzer şekilde görünür. Makalenin sonraki bölümlerinde her adımın ayrıntılı olarak anlatıldığı anlatılmaktadır.

![OpenID Connect protokolü: Belirteç alımı](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>Erişim belirteçleri al
Erişim belirteçleri almak için oturum açma isteğini değiştirin:

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> Bu isteği yürütmek için aşağıdaki bağlantıya tıklayın. Oturum açtıktan sonra, tarayıcınız bir kimlik belirteci ve adres `https://localhost/myapp/`çubuğundaki bir kodla yeniden yönlendirilir. Bu isteğin yalnızca tanıtım amacıyla `response_mode=fragment` kullandığını unutmayın. Kullanmanızı `response_mode=form_post`öneririz.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

İstekte izin kapsamları dahil ederek ve kullanarak `response_type=id_token code`, Microsoft Identity platform uç noktası kullanıcının `scope` sorgu parametresinde belirtilen izinlere onay almasını sağlar. Erişim belirteci için, uygulamanıza Exchange 'e bir yetkilendirme kodu döndürür.

### <a name="successful-response"></a>Başarılı yanıt

Kullanarak `response_mode=form_post` başarılı bir yanıt şöyle görünür:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parametre | Açıklama |
| --- | --- |
| `id_token` | Uygulamanın istediği KIMLIK belirteci. KIMLIK belirtecini, kullanıcının kimliğini doğrulamak ve kullanıcıyla oturum başlatmak için kullanabilirsiniz. Kimliğin kimlik belirteçleri ve içerikleri [ `id_tokens` ](id-tokens.md)hakkında daha fazla ayrıntı bulacaksınız. |
| `code` | Uygulamanın istediği yetkilendirme kodu. Uygulama, hedef kaynak için bir erişim belirteci istemek üzere yetkilendirme kodunu kullanabilir. Yetkilendirme kodu kısa ömürlü olur. Genellikle, bir yetkilendirme kodunun yaklaşık 10 dakika içinde süresi dolar. |
| `state` | İsteğe bir durum parametresi dahil edilir, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta durum değerlerinin özdeş olduğunu doğrulamalıdır. |

### <a name="error-response"></a>Hata yanıtı

Ayrıca, uygulamanın bunları uygun şekilde işleyebilmesi için yeniden yönlendirme URI 'sine de hata yanıtları gönderilebilir. Bir hata yanıtı şuna benzer:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parametre | Açıklama |
| --- | --- |
| `error` | Oluşan hata türlerini sınıflandırmak ve hatalara tepki vermek için kullanabileceğiniz bir hata kodu dizesi. |
| `error_description` | Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |

Olası hata kodlarının ve önerilen istemci yanıtlarının açıklaması için bkz. [Yetkilendirme uç noktası hataları Için hata kodları](#error-codes-for-authorization-endpoint-errors).

Bir yetkilendirme kodunuz ve bir KIMLIK belirteciniz varsa, Kullanıcı oturumunu açabilir ve adına erişim belirteçleri alabilirsiniz. Kullanıcıyı ' de imzalamak için KIMLIK belirtecini [tam olarak açıklandığı gibi](id-tokens.md#validating-an-id_token)doğrulamanız gerekir. Erişim belirteçlerini almak için, [OAuth kod akışı belgelerinde](v2-oauth2-auth-code-flow.md#request-an-access-token)açıklanan adımları izleyin.
