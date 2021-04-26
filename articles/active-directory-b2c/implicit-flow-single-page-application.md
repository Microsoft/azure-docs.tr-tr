---
title: Örtük akış kullanan tek sayfalı oturum açma
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C ile OAuth 2,0 örtük akışını kullanarak tek sayfalı oturum açmayı nasıl ekleyeceğinizi öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fe31e1bf095d15cfdd7945288486cb866ace8246
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94840619"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 'de OAuth 2,0 örtük akışını kullanarak tek sayfalı oturum açma

Birçok modern uygulamanın, birincil olarak JavaScript 'te yazılmış tek sayfalı bir uygulama ön ucu vardır. Uygulama genellikle tepki, angular veya Vue.js gibi bir çerçeve kullanılarak yazılır. Birincil olarak bir tarayıcıda çalışan tek sayfalı uygulamalar ve diğer JavaScript uygulamaları, kimlik doğrulaması için bazı ek güçlüklere sahiptir:

- Bu uygulamaların güvenlik özellikleri geleneksel sunucu tabanlı Web uygulamalarından farklıdır.
- Birçok yetkilendirme sunucusu ve kimlik sağlayıcısı, çıkış noktaları arası kaynak paylaşımı (CORS) isteklerini desteklemez.
- Tam sayfa tarayıcı yeniden yönlendirmeleri, uygulamanın dışında yeniden yönlendirilir ve Kullanıcı deneyimine sahip olabilir.

Tek sayfalı uygulamaları desteklemeye yönelik önerilen yöntem, [OAuth 2,0 yetkilendirme kodu akışsıdır (PKI CE ile)](./authorization-code-flow.md).

[MSAL.js 1. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)gibi bazı çerçeveler yalnızca örtük izin akışını destekler. Bu durumlarda Azure Active Directory B2C (Azure AD B2C), OAuth 2,0 yetkilendirmesi dolaylı verme akışını destekler. Thee akışı, [OAuth 2,0 belirtiminin 4,2 bölümünde](https://tools.ietf.org/html/rfc6749)açıklanmaktadır. Örtük akışta, uygulama herhangi bir sunucudan sunucuya Exchange olmadan belirteçleri doğrudan Azure Active Directory (Azure AD) yetkilendirme uç noktasından alır. Tüm kimlik doğrulama mantığı ve oturum işleme, tam olarak JavaScript istemcisinde veya bir sayfa yeniden yönlendirme ya da bir açılır kutu ile yapılır.

Azure AD B2C, standart OAuth 2,0 örtük akışını basit kimlik doğrulamasından ve yetkilendirmeye genişletir. Azure AD B2C, [ilke parametresini](user-flow-overview.md)tanıtır. İlke parametresiyle, uygulamanıza kaydolma, oturum açma ve profil yönetimi Kullanıcı akışları gibi ilkeler eklemek için OAuth 2,0 kullanabilirsiniz. Bu makaledeki örnek HTTP isteklerinde, **{Tenant}. onmicrosoft. com** örnek olarak kullanılır. `{tenant}`Varsa kiracınızın adıyla değiştirin ve ayrıca bir Kullanıcı akışı oluşturduysanız.

Örtük oturum açma akışı aşağıdaki şekilde görünür. Her adım makalede daha sonra ayrıntılı olarak açıklanmaktadır.

![OpenID Connect örtük akışını gösteren kulvar stili diyagram](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Kimlik doğrulama isteklerini gönder

Web uygulamanızın kullanıcı kimliğini doğrulaması ve bir Kullanıcı akışı çalıştırması gerektiğinde, kullanıcıyı `/authorize` uç noktaya yönlendirebilir. Kullanıcı, Kullanıcı akışına bağlı olarak eylem gerçekleştirir.

Bu istekte istemci, `scope` parametresindeki kullanıcıdan ve Kullanıcı akışının çalışması için gereken izinleri gösterir. İsteğin nasıl çalıştığına ilişkin bir fikir almak için, isteği bir tarayıcıya yapıştırmayı ve çalıştırmayı deneyin. `{tenant}`Azure AD B2C kiracınızın adıyla değiştirin. `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`Daha önce kiracınıza kaydettiğiniz uygulamanın uygulama kimliğiyle değiştirin. `{policy}`Kiracınızda oluşturduğunuz bir ilkenin adıyla değiştirin (örneğin,) `b2c_1_sign_in` .

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parametre | Gerekli | Açıklama |
| --------- | -------- | ----------- |
|Kiracı| Yes | Azure AD B2C kiracınızın adı|
|ilkesinin| Yes| Çalıştırılacak Kullanıcı akışı. Azure AD B2C kiracınızda oluşturduğunuz Kullanıcı akışının adını belirtin. Örneğin: `b2c_1_sign_in` , `b2c_1_sign_up` , veya `b2c_1_edit_profile` . |
| client_id | Yes | [Azure Portal](https://portal.azure.com/) uygulamanıza atanan uygulama kimliği. |
| response_type | Yes | `id_token`OpenID Connect oturum açma için içermelidir. Yanıt türünü de içerebilir `token` . Kullanıyorsanız, uygulamanız yetkilendirme uç noktası `token` için ikinci bir istek yapmadan yetkilendirme uç noktasından hemen bir erişim belirteci alabilir.  `token`Yanıt türünü kullanırsanız, `scope` parametresi belirtecinin hangi kaynağa verilmeyeceğini belirten bir kapsam içermelidir. |
| redirect_uri | No | Uygulamanızın kimlik doğrulama yanıtlarının gönderilebileceği ve alınabileceği, uygulamanızın yeniden yönlendirme URI 'SI. Portalın, URL kodlamalı olması dışında, portalda kaydettiğiniz yeniden yönlendirme URI 'lerinden biriyle tam olarak eşleşmesi gerekir. |
| response_mode | No | Elde edilen belirteci uygulamanıza geri göndermek için kullanılacak yöntemi belirtir.  Örtük akışlar için kullanın `fragment` . |
| scope | Yes | Kapsamların boşlukla ayrılmış listesi. Tek bir kapsam değeri, Azure AD 'ye, İstenen izinlerin her ikisi de belirtir. `openid`Kapsam, kullanıcıya oturum açma ve kimlik belirteçleri biçimindeki Kullanıcı hakkında veri edinme iznini gösterir. `offline_access`Kapsam, Web Apps için isteğe bağlıdır. Uygulamanızın kaynaklara uzun süreli erişim için yenileme belirteci gerektiğini gösterir. |
| state | No | İstekte, belirteç yanıtında döndürülen bir değer. Kullanmak istediğiniz herhangi bir içerik dizesi olabilir. Genellikle, siteler arası istek sahteciliği saldırıları engellemek için rastgele oluşturulan, benzersiz bir değer kullanılır. Durum Ayrıca, kullanıcının uygulamadaki durumuyla ilgili bilgileri, açık oldukları sayfa gibi kimlik doğrulama isteği yapılmadan önce kodlamak için de kullanılır. |
| nonce | Yes | İstek olarak ortaya çıkan KIMLIK belirtecine dahil edilen isteğe (uygulama tarafından oluşturulan) dahil bir değer. Daha sonra uygulama, belirteç yeniden yürütme saldırılarını azaltmak için bu değeri doğrulayabilirler. Genellikle değer, isteğin kaynağını belirlemek için kullanılabilecek rastgele, benzersiz bir dizedir. |
| isteme | No | Gerekli Kullanıcı etkileşimi türü. Şu anda geçerli olan tek değer `login` . Bu parametre, kullanıcıyı bu istek üzerine kimlik bilgilerini girmeye zorlar. Çoklu oturum açma etkili olmaz. |

Bu noktada, kullanıcıdan ilkenin iş akışını tamamlaması istenir. Kullanıcının Kullanıcı adı ve parolasını girmesi, bir sosyal kimlik ile oturum açması, dizin için kayıt veya başka birçok adım olması gerekebilir. Kullanıcı eylemleri, Kullanıcı akışının nasıl tanımlandığına bağlıdır.

Kullanıcı Kullanıcı akışını tamamladıktan sonra, Azure AD, için kullandığınız değerde uygulamanıza bir yanıt döndürür `redirect_uri` . Parametresinde belirtilen yöntemini kullanır `response_mode` . Yanıt, yürütülen Kullanıcı akışından bağımsız olarak her kullanıcı eylemi senaryosu için tam olarak aynıdır.

### <a name="successful-response"></a>Başarılı yanıt
' I kullanan başarılı bir `response_mode=fragment` Yanıt `response_type=id_token+token` , okunabilirliği için satır sonları ile aşağıdaki gibi görünür:

```http
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parametre | Açıklama |
| --------- | ----------- |
| access_token | Uygulamanın istediği erişim belirteci. |
| token_type | Belirteç türü değeri. Azure AD 'nin desteklediği tek tür taşıyıcı. |
| expires_in | Erişim belirtecinin geçerli olduğu sürenin uzunluğu (saniye cinsinden). |
| scope | Belirtecin geçerli olduğu kapsamlar. Ayrıca, daha sonra kullanmak üzere belirteçleri önbelleğe almak için kapsamları kullanabilirsiniz. |
| id_token | Uygulamanın istediği KIMLIK belirteci. KIMLIK belirtecini, kullanıcının kimliğini doğrulamak ve kullanıcıyla oturum başlatmak için kullanabilirsiniz. KIMLIK belirteçleri ve içerikleri hakkında daha fazla bilgi için [Azure AD B2C belirteç başvurusuna](tokens-overview.md)bakın. |
| state | İsteğe bir `state` parametre dahil ise, yanıtta aynı değer görünmelidir. Uygulamanın, `state` istek ve yanıt değerlerinin özdeş olduğunu doğrulaması gerekir. |

### <a name="error-response"></a>Hata yanıtı
Ayrıca, uygulamanın uygun şekilde işleyebilmesi için yeniden yönlendirme URI 'sine de hata yanıtları gönderilebilir:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametre | Açıklama |
| --------- | ----------- |
| error | Oluşan hata türlerini sınıflandırmak için kullanılan kod. |
| error_description | Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |
| state | İsteğe bir `state` parametre dahil ise, yanıtta aynı değer görünmelidir. Uygulamanın, `state` istek ve yanıt değerlerinin özdeş olduğunu doğrulaması gerekir.|

## <a name="validate-the-id-token"></a>KIMLIK belirtecini doğrulama

KIMLIK belirtecinin alınması, kullanıcının kimliğini doğrulamak için yeterli değil. KIMLIK belirtecinin imzasını doğrulayın ve uygulamanızın gereksinimlerine göre belirteçteki talepleri doğrulayın. Azure AD B2C, belirteçleri imzalamak ve bunların geçerli olduğunu doğrulamak için [JSON Web belirteçleri (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) ve ortak anahtar şifrelemesi kullanır.

Birçok açık kaynak kitaplığı, kullanmayı tercih ettiğiniz dile bağlı olarak JWTs 'yi doğrulamak için kullanılabilir. Kendi doğrulama mantığınızı uygulamak yerine, kullanılabilir açık kaynaklı kitaplıkları araştırmayı düşünün. Bu kitaplıkları düzgün bir şekilde kullanmayı öğrenmenize yardımcı olması için bu makaledeki bilgileri kullanabilirsiniz.

Azure AD B2C bir OpenID Connect meta veri uç noktası vardır. Bir uygulama, çalışma zamanında Azure AD B2C hakkındaki bilgileri getirmek için uç noktayı kullanabilir. Bu bilgiler uç noktaları, belirteç içerikleri ve belirteç imzalama anahtarlarını içerir. Azure AD B2C kiracınızdaki her Kullanıcı akışı için bir JSON meta veri belgesi vardır. Örneğin, fabrikamb2c.onmicrosoft.com kiracısındaki b2c_1_sign_in Kullanıcı akışı için meta veri belgesi şurada bulunur:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Bu yapılandırma belgesinin özelliklerinden biri `jwks_uri` . Aynı kullanıcı akışının değeri şöyle olacaktır:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Bir KIMLIK belirtecini imzalamak için kullanılan Kullanıcı akışının (ve meta verilerin nereden getirileceği) belirlenmesi için iki seçeneğiniz vardır. İlk olarak, Kullanıcı akış adı içindeki talebe dahil edilir `acr` `id_token` . KIMLIK belirtecinden talepleri ayrıştırmaya ilişkin bilgi için [Azure AD B2C belirteç başvurusuna](tokens-overview.md)bakın. Diğer bir seçenek de, isteği keserken parametre değerindeki Kullanıcı akışını kodlayacaktır `state` . Ardından, `state` hangi Kullanıcı akışının kullanıldığını belirleyen parametresinin kodunu çözün. Her iki yöntem de geçerlidir.

Meta veri belgesini OpenID Connect meta veri uç noktasından aldıktan sonra, KIMLIK belirtecinin imzasını doğrulamak için RSA-256 ortak anahtarlarını (bu uç noktada bulunur) kullanabilirsiniz. Bu uç noktada, her biri bir tarafından tanımlanan herhangi bir zamanda listelenmiş birden fazla anahtar olabilir `kid` . Üst bilgisi `id_token` de bir talep içerir `kid` . Bu, KIMLIK belirtecini imzalamak için bu anahtarların hangisinin kullanıldığını belirtir. [Belirteçleri doğrulama](tokens-overview.md)hakkında bilgi edinmek dahil daha fazla bilgi için [Azure AD B2C belirteç başvurusuna](tokens-overview.md)bakın.
<!--TODO: Improve the information on this-->

KIMLIK belirtecinin imzasını doğruladıktan sonra, birkaç talep doğrulama gerektirir. Örnek:

* Belirteç yeniden `nonce` yürütme saldırılarını önleme talebini doğrulayın. Değeri, oturum açma isteğinde belirtidikleriniz olmalıdır.
* `aud`Kimlik belirtecinin uygulamanız için verildiğinden emin olmak için talebi doğrulayın. Değeri uygulamanızın uygulama KIMLIĞI olmalıdır.
* `iat` `exp` Kimlik belirtecinin sona ermediğinden emin olmak için ve taleplerini doğrulayın.

Gerçekleştirmeniz gereken birkaç doğrulama daha vardır ve [OpenID Connect Core belirtiminde](https://openid.net/specs/openid-connect-core-1_0.html)ayrıntılı olarak açıklanmıştır. Senaryonuza bağlı olarak ek talepler de doğrulamak isteyebilirsiniz. Bazı ortak doğrulamalar şunları içerir:

* Kullanıcı veya kuruluşun uygulamaya kaydolmasını sağlama.
* Kullanıcının uygun yetkilendirme ve ayrıcalıklara sahip olduğundan emin olma.
* Azure AD Multi-Factor Authentication kullanma gibi belirli bir kimlik doğrulama gücünün meydana geldiğinden emin olma.

Bir KIMLIK belirtecindeki talepler hakkında daha fazla bilgi için [Azure AD B2C belirteç başvurusuna](tokens-overview.md)bakın.

KIMLIK belirtecini doğruladıktan sonra kullanıcıyla bir oturum başlatabilirsiniz. Uygulamanızda, Kullanıcı hakkında bilgi edinmek için KIMLIK belirtecindeki talepleri kullanın. Bu bilgiler, görüntüleme, kayıtlar, yetkilendirme vb. için kullanılabilir.

## <a name="get-access-tokens"></a>Erişim belirteçleri al
Web uygulamalarınızın yapması gereken tek şey Kullanıcı akışlarını yürütmek ise, sonraki birkaç bölümü atlayabilirsiniz. Aşağıdaki bölümlerde yer alan bilgiler, yalnızca bir Web API 'sine kimliği doğrulanmış çağrılar yapması gereken ve Azure AD B2C tarafından korunan Web uygulamaları için geçerlidir.

Kullanıcıyı tek sayfalı uygulamanıza imzaladığınıza göre, Azure AD tarafından güvenliği sağlanmış Web API 'Lerini çağırmaya yönelik erişim belirteçleri alabilirsiniz. Yanıt türünü kullanarak bir belirteç almış olsanız bile `token` , yeniden oturum açmak için kullanıcıyı yeniden yönlendirmeksizin ek kaynaklara yönelik belirteçleri almak üzere bu yöntemi kullanabilirsiniz.

Tipik bir Web uygulaması akışında uç noktaya bir istek yaparsınız `/token` . Ancak, uç nokta CORS isteklerini desteklemez, bu nedenle bir yenileme belirteci almak için AJAX çağrılarının yapılması bir seçenek değildir. Bunun yerine, diğer Web API 'Lerine yönelik yeni belirteçler almak için, bir gizli HTML iframe öğesinde örtük akışı kullanabilirsiniz. Aşağıda, okunabilirliği için satır sonları içeren bir örnek verilmiştir:

```http
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
```

| Parametre | Gerekli mi? | Description |
| --- | --- | --- |
|Kiracı| Gerekli | Azure AD B2C kiracınızın adı|
ilkesinin| Gerekli| Çalıştırılacak Kullanıcı akışı. Azure AD B2C kiracınızda oluşturduğunuz Kullanıcı akışının adını belirtin. Örneğin: `b2c_1_sign_in` , `b2c_1_sign_up` , veya `b2c_1_edit_profile` . |
| client_id |Gerekli |[Azure Portal](https://portal.azure.com)uygulamanıza atanan uygulama kimliği. |
| response_type |Gerekli |`id_token`OpenID Connect oturum açma için içermelidir.  Yanıt türünü de içerebilir `token` . Burada kullanıyorsanız, uygulamanız yetkilendirme uç noktası `token` için ikinci bir istek yapmadan yetkilendirme uç noktasından hemen bir erişim belirteci alabilir. `token`Yanıt türünü kullanırsanız, `scope` parametresi belirtecinin hangi kaynağa verilmeyeceğini belirten bir kapsam içermelidir. |
| redirect_uri |Önerilen |Uygulamanızın kimlik doğrulama yanıtlarının gönderilebileceği ve alınabileceği, uygulamanızın yeniden yönlendirme URI 'SI. Portalın, URL kodlamalı olması dışında, portala kaydettiğiniz yeniden yönlendirme URI 'lerinden biriyle tam olarak eşleşmesi gerekir. |
| scope |Gerekli |Kapsamların boşlukla ayrılmış listesi.  Belirteçleri almak için, istenen kaynak için ihtiyaç duyduğunuz tüm kapsamları dahil edin. |
| response_mode |Önerilen |Elde edilen belirteci uygulamanıza geri göndermek için kullanılan yöntemi belirtir. Örtük akış için kullanın `fragment` . Diğer iki mod belirtilebilir, `query` `form_post` ancak örtük akışta çalışmaz. |
| state |Önerilen |Belirteç yanıtında döndürülen isteğe eklenen bir değer.  Kullanmak istediğiniz herhangi bir içerik dizesi olabilir.  Genellikle, siteler arası istek sahteciliği saldırıları engellemek için rastgele oluşturulan, benzersiz bir değer kullanılır.  Durum Ayrıca, kimlik doğrulama isteği gerçekleştirilmeden önce kullanıcının uygulamasındaki durumu hakkında bilgi kodlamak için de kullanılır. Örneğin, kullanıcının sayfası veya görünümü. |
| nonce |Gerekli |Talep olarak elde edilen KIMLIK belirtecine dahil edilen, uygulama tarafından oluşturulan, isteğe dahil edilen bir değer.  Daha sonra uygulama, belirteç yeniden yürütme saldırılarını azaltmak için bu değeri doğrulayabilirler. Genellikle değer, isteğin kaynağını tanımlayan rastgele, benzersiz bir dizedir. |
| isteme |Gerekli |Gizli bir IFRAME 'de belirteçleri yenilemek ve almak için, `prompt=none` iframe 'nin oturum açma sayfasında takılı olmadığından emin olmak için kullanın ve hemen döndürür. |
| login_hint |Gerekli |Gizli bir iframe içindeki belirteçleri yenilemek ve almak için, kullanıcının belirli bir zamanda sahip olabileceği birden çok oturumu ayırt etmek için bu ipucuna kullanıcının Kullanıcı adını ekleyin. Talebi kullanarak daha önceki bir oturum açma işleminden Kullanıcı adını ayıklayabilirsiniz `preferred_username` ( `profile` talebi almak için kapsam gereklidir `preferred_username` ). |
| domain_hint |Gerekli |`consumers` veya `organizations` olabilir.  Gizli bir IFRAME içindeki belirteçleri yenilemek ve almak için, `domain_hint` istekteki değeri ekleyin.  `tid`Hangi değerin kullanılacağını öğrenmek için daha önceki bir oturum açma kimlik belirtecinden talebi ayıklayın ( `profile` talebi almak için kapsam gereklidir `tid` ). `tid`Talep değeri ise `9188040d-6c67-4c5b-b112-36a304b66dad` , kullanın `domain_hint=consumers` .  Aksi takdirde, kullanın `domain_hint=organizations` . |

`prompt=none`Parametresi ayarlanarak bu istek hemen başarılı veya başarısız olur ve uygulamanıza geri döner.  Belirtilen yeniden yönlendirme URI 'sindeki, parametresinde belirtilen yöntemi kullanarak uygulamanıza başarılı bir yanıt gönderilir `response_mode` .

### <a name="successful-response"></a>Başarılı yanıt
Kullanarak başarılı bir yanıt `response_mode=fragment` Şu örneğe benzer şekilde görünür:

```http
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parametre | Açıklama |
| --- | --- |
| access_token |Uygulamanın istediği belirteç. |
| token_type |Belirteç türü her zaman taşıyıcı olur. |
| state |İsteğe bir `state` parametre dahil ise, yanıtta aynı değer görünmelidir. Uygulamanın, `state` istek ve yanıt değerlerinin özdeş olduğunu doğrulaması gerekir. |
| expires_in |Erişim belirtecinin geçerli olduğu süre (saniye cinsinden). |
| scope |Erişim belirtecinin geçerli olduğu kapsamlar. |

### <a name="error-response"></a>Hata yanıtı
Ayrıca, uygulamanın uygun şekilde işleyebilmesi için yeniden yönlendirme URI 'sine de hata yanıtları gönderilebilir.  İçin `prompt=none` beklenen bir hata şu örneğe benzer şekilde görünür:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parametre | Açıklama |
| --- | --- |
| error |Oluşan hata türlerini sınıflandırmak için kullanılabilen bir hata kodu dizesi. Ayrıca, hatalara yanıt vermek için dizesini de kullanabilirsiniz. |
| error_description |Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |

İframe isteğinde bu hatayı alırsanız, Kullanıcı yeni bir belirteci almak için etkileşimli olarak yeniden oturum açması gerekir.

## <a name="refresh-tokens"></a>Belirteçleri Yenile
KIMLIK belirteçleri ve erişim belirteçlerinin her ikisi de kısa bir süre sonra sona erer. Bu belirteçleri düzenli aralıklarla yenilemek için uygulamanızın hazırlanması gerekir.  Her iki tür belirteci yenilemek için, `prompt=none` Azure AD adımlarını denetlemek için parametresini kullanarak daha önceki bir örnekte kullandığımız aynı gizli iframe isteğini gerçekleştirin.  Yeni bir değer almak için `id_token` `response_type=id_token` ve `scope=openid` , ve parametresini kullandığınızdan emin olun `nonce` .

## <a name="send-a-sign-out-request"></a>Oturum kapatma isteği gönder
Kullanıcıyı uygulamadan dışarı imzalamak istediğinizde oturumu kapatmak için kullanıcıyı Azure AD 'ye yönlendirin. Kullanıcıyı yeniden yönlendirmezseniz, Azure AD ile geçerli bir çoklu oturum açma oturumu olduğundan, kimlik bilgilerini tekrar girmeden uygulamanıza yeniden kimlik doğrulaması yapabiliyor olabilirler.

Kullanıcıyı, `end_session_endpoint` [kimlik belirtecini doğrulama](#validate-the-id-token)bölümünde açıklanan OpenID Connect meta veri belgesinde listelenen öğesine yeniden yönlendirebilirsiniz. Örnek:

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametre | Gerekli | Açıklama |
| --------- | -------- | ----------- |
| Kiracı | Yes | Azure AD B2C kiracınızın adı |
| ilkesinin | Yes | Kullanıcıyı uygulamanızda imzalamak için kullanmak istediğiniz kullanıcı akışı. |
| post_logout_redirect_uri | No | Başarılı oturum kapatıldıktan sonra kullanıcının yeniden yönlendirilmesi gereken URL. Dahil değilse, kullanıcıya genel bir ileti gösterir Azure AD B2C. |
| state | No | İsteğe bir `state` parametre dahil ise, yanıtta aynı değer görünmelidir. Uygulamanın, `state` istek ve yanıt değerlerinin özdeş olduğunu doğrulaması gerekir. |


> [!NOTE]
> Kullanıcıyı ' a yönlendirmek, `end_session_endpoint` Azure AD B2C ile kullanıcının çoklu oturum açma durumunu temizler. Ancak, kullanıcının Kullanıcı sosyal kimlik sağlayıcısı oturumundan oturum açmasını engellemez. Kullanıcı sonraki oturum açma sırasında aynı kimlik sağlayıcısını seçerse, kullanıcının kimlik bilgilerini girmeden yeniden kimlik doğrulaması yapılır. Bir Kullanıcı Azure AD B2C uygulamanızın oturumunu kapatmak isterse, örneğin Facebook hesabının oturumunu tamamen kapatmak istedikleri anlamına gelmez. Ancak, yerel hesaplar için kullanıcının oturumu doğru şekilde sona erer.
>

## <a name="next-steps"></a>Sonraki adımlar

### <a name="code-sample-azure-ad-b2c-with-microsoft-authentication-library-for-javascript"></a>Kod örneği: JavaScript için Microsoft kimlik doğrulama kitaplığı ile Azure AD B2C

[Azure AD B2C için msal.js ile oluşturulan tek sayfalı uygulama][github-msal-js-example] (GitHub)

GitHub 'daki Bu örnek, [msal.js][github-msal-js] ve açılır stil kimlik doğrulaması kullanılarak oluşturulmuş basit bir web uygulamasında Azure AD B2C başlamanıza yardımcı olmaya yöneliktir.

<!-- Links - EXTERNAL -->
[github-msal-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
[github-msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
