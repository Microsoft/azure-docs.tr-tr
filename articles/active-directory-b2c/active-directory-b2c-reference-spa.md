---
title: Örtük akış Azure Active Directory B2C kullanarak tek sayfalı oturum açma
description: Azure Active Directory B2C ile OAuth 2,0 örtük akışını kullanarak tek sayfalı oturum açmayı nasıl ekleyeceğinizi öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ab8c8a582b90976ada20b1e970c9e9648d14b2a9
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596448"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 'de OAuth 2,0 örtük akışını kullanarak tek sayfalı oturum açma

Birçok modern uygulamanın, birincil olarak JavaScript 'te yazılmış tek sayfalı bir uygulama ön ucu vardır. Uygulama genellikle tepki, angular veya Vue. js gibi bir çerçeve kullanılarak yazılır. Birincil olarak bir tarayıcıda çalışan tek sayfalı uygulamalar ve diğer JavaScript uygulamaları, kimlik doğrulaması için bazı ek güçlüklere sahiptir:

- Bu uygulamaların güvenlik özellikleri geleneksel sunucu tabanlı Web uygulamalarından farklıdır.
- Birçok yetkilendirme sunucusu ve kimlik sağlayıcısı, çıkış noktaları arası kaynak paylaşımı (CORS) isteklerini desteklemez.
- Tam sayfa tarayıcı yeniden yönlendirmeleri, uygulamanın dışında yeniden yönlendirilir ve Kullanıcı deneyimine sahip olabilir.

Bu uygulamaları desteklemek için Azure Active Directory B2C (Azure AD B2C) OAuth 2,0 örtük akışını kullanır. OAuth 2,0 yetkilendirmesi dolaylı verme akışı, [oauth 2,0 belirtiminin 4,2 bölümünde](https://tools.ietf.org/html/rfc6749)açıklanmaktadır. Örtük akışta, uygulama herhangi bir sunucudan sunucuya Exchange olmadan belirteçleri doğrudan Azure Active Directory (Azure AD) yetkilendirme uç noktasından alır. Tüm kimlik doğrulama mantığı ve oturum işleme, tam olarak JavaScript istemcisinde veya bir sayfa yeniden yönlendirme ya da bir açılır kutu ile yapılır.

Azure AD B2C, standart OAuth 2,0 örtük akışını basit kimlik doğrulamasından ve yetkilendirmeye genişletir. Azure AD B2C, [ilke parametresini](active-directory-b2c-reference-policies.md)tanıtır. İlke parametresiyle, uygulamanıza kaydolma, oturum açma ve profil yönetimi Kullanıcı akışları gibi ilkeler eklemek için OAuth 2,0 kullanabilirsiniz. Bu makaledeki örnek HTTP isteklerinde, **{Tenant}. onmicrosoft. com** örnek olarak kullanılır. @No__t_0, varsa kiracınızın adıyla değiştirin ve ayrıca bir Kullanıcı akışı oluşturmuş olursunuz.

Örtük oturum açma akışı aşağıdaki şekilde görünür. Her adım makalede daha sonra ayrıntılı olarak açıklanmaktadır.

![OpenID Connect örtük akışını gösteren kulvar stili diyagram](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Kimlik doğrulama isteklerini gönder

Web uygulamanızın kullanıcı kimliğini doğrulaması ve bir Kullanıcı akışı çalıştırması gerektiğinde, kullanıcıyı `/authorize` uç noktasına yönlendirebilir. Kullanıcı, Kullanıcı akışına bağlı olarak eylem gerçekleştirir.

Bu istekte istemci, `scope` parametresindeki kullanıcıdan ve Kullanıcı akışının çalışması için gereken izinleri gösterir. İsteğin nasıl çalıştığına ilişkin bir fikir almak için, isteği bir tarayıcıya yapıştırmayı ve çalıştırmayı deneyin. @No__t_0, Azure AD B2C kiracınızın adıyla değiştirin. @No__t_0, daha önce kiracınızda kaydettiğiniz uygulamanın uygulama KIMLIĞIYLE değiştirin. @No__t_0, kiracınızda oluşturduğunuz bir ilkenin adıyla değiştirin, örneğin `b2c_1_sign_in`.

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parametre | Gereklidir | Açıklama |
| --------- | -------- | ----------- |
|Kiracı| Yes | Azure AD B2C kiracınızın adı|
|ilkesinin| Yes| Çalıştırılacak Kullanıcı akışı. Azure AD B2C kiracınızda oluşturduğunuz Kullanıcı akışının adını belirtin. Örneğin: `b2c_1_sign_in`, `b2c_1_sign_up` veya `b2c_1_edit_profile`. |
| client_id | Yes | [Azure Portal](https://portal.azure.com/) uygulamanıza atanan uygulama kimliği. |
| response_type | Yes | OpenID Connect oturum açma `id_token` içermelidir. Ayrıca `token` yanıt türünü de içerebilir. @No__t_0 kullanıyorsanız, uygulamanız yetkilendirme uç noktası için ikinci bir istek yapmadan yetkilendirme uç noktasından hemen bir erişim belirteci alabilir.  @No__t_0 yanıt türünü kullanırsanız, `scope` parametresi, belirtecinin hangi kaynağa verilmeyeceğini belirten bir kapsam içermelidir. |
| isteğinde | Hayır | Uygulamanızın kimlik doğrulama yanıtlarının gönderilebileceği ve alınabileceği, uygulamanızın yeniden yönlendirme URI 'SI. Portalın, URL kodlamalı olması dışında, portalda kaydettiğiniz yeniden yönlendirme URI 'lerinden biriyle tam olarak eşleşmesi gerekir. |
| response_mode | Hayır | Elde edilen belirteci uygulamanıza geri göndermek için kullanılacak yöntemi belirtir.  Örtük akışlar için `fragment` kullanın. |
| scope | Yes | Kapsamların boşlukla ayrılmış listesi. Tek bir kapsam değeri, Azure AD 'ye, İstenen izinlerin her ikisi de belirtir. @No__t_0 kapsam, kullanıcıya oturum açma ve KIMLIK belirteçleri biçiminde kullanıcı hakkında veri edinme iznini gösterir. @No__t_0 kapsamı Web Apps için isteğe bağlıdır. Uygulamanızın kaynaklara uzun süreli erişim için yenileme belirteci gerektiğini gösterir. |
| durum | Hayır | İstekte, belirteç yanıtında döndürülen bir değer. Kullanmak istediğiniz herhangi bir içerik dizesi olabilir. Genellikle, siteler arası istek sahteciliği saldırıları engellemek için rastgele oluşturulan, benzersiz bir değer kullanılır. Durum Ayrıca, kullanıcının uygulamadaki durumuyla ilgili bilgileri, açık oldukları sayfa gibi kimlik doğrulama isteği yapılmadan önce kodlamak için de kullanılır. |
| nonce | Yes | İstek olarak ortaya çıkan KIMLIK belirtecine dahil edilen isteğe (uygulama tarafından oluşturulan) dahil bir değer. Daha sonra uygulama, belirteç yeniden yürütme saldırılarını azaltmak için bu değeri doğrulayabilirler. Genellikle değer, isteğin kaynağını belirlemek için kullanılabilecek rastgele, benzersiz bir dizedir. |
| isteme | Hayır | Gerekli Kullanıcı etkileşimi türü. Şu anda geçerli olan tek değer `login`. Bu parametre, kullanıcıyı bu istek üzerine kimlik bilgilerini girmeye zorlar. Çoklu oturum açma etkili olmaz. |

Bu noktada, kullanıcıdan ilkenin iş akışını tamamlaması istenir. Kullanıcının Kullanıcı adı ve parolasını girmesi, bir sosyal kimlik ile oturum açması, dizin için kayıt veya başka birçok adım olması gerekebilir. Kullanıcı eylemleri, Kullanıcı akışının nasıl tanımlandığına bağlıdır.

Kullanıcı Kullanıcı akışını tamamladıktan sonra, Azure AD `redirect_uri` için kullandığınız değerde uygulamanıza bir yanıt döndürür. @No__t_0 parametresinde belirtilen yöntemini kullanır. Yanıt, yürütülen Kullanıcı akışından bağımsız olarak her kullanıcı eylemi senaryosu için tam olarak aynıdır.

### <a name="successful-response"></a>Başarılı yanıt
@No__t_0 ve `response_type=id_token+token` kullanan başarılı bir yanıt, okunabilirliği için satır sonları ile aşağıdaki gibi görünür:

```HTTP
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
| id_token | Uygulamanın istediği KIMLIK belirteci. KIMLIK belirtecini, kullanıcının kimliğini doğrulamak ve kullanıcıyla oturum başlatmak için kullanabilirsiniz. KIMLIK belirteçleri ve içerikleri hakkında daha fazla bilgi için [Azure AD B2C belirteç başvurusuna](active-directory-b2c-reference-tokens.md)bakın. |
| durum | İsteğe bir `state` parametresi dahil edilir, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta `state` değerlerinin özdeş olduğunu doğrulamalıdır. |

### <a name="error-response"></a>Hata yanıtı
Ayrıca, uygulamanın uygun şekilde işleyebilmesi için yeniden yönlendirme URI 'sine de hata yanıtları gönderilebilir:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametre | Açıklama |
| --------- | ----------- |
| error | Oluşan hata türlerini sınıflandırmak için kullanılan kod. |
| error_description | Kimlik doğrulama hatasının temel nedenini belirlemenize yardımcı olabilecek belirli bir hata iletisi. |
| durum | İsteğe bir `state` parametresi dahil edilir, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta `state` değerlerinin özdeş olduğunu doğrulamalıdır.|

## <a name="validate-the-id-token"></a>KIMLIK belirtecini doğrulama

KIMLIK belirtecinin alınması, kullanıcının kimliğini doğrulamak için yeterli değil. KIMLIK belirtecinin imzasını doğrulayın ve uygulamanızın gereksinimlerine göre belirteçteki talepleri doğrulayın. Azure AD B2C, belirteçleri imzalamak ve bunların geçerli olduğunu doğrulamak için [JSON Web belirteçleri (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) ve ortak anahtar şifrelemesi kullanır.

Birçok açık kaynak kitaplığı, kullanmayı tercih ettiğiniz dile bağlı olarak JWTs 'yi doğrulamak için kullanılabilir. Kendi doğrulama mantığınızı uygulamak yerine, kullanılabilir açık kaynaklı kitaplıkları araştırmayı düşünün. Bu kitaplıkları düzgün bir şekilde kullanmayı öğrenmenize yardımcı olması için bu makaledeki bilgileri kullanabilirsiniz.

Azure AD B2C bir OpenID Connect meta veri uç noktası vardır. Bir uygulama, çalışma zamanında Azure AD B2C hakkındaki bilgileri getirmek için uç noktayı kullanabilir. Bu bilgiler uç noktaları, belirteç içerikleri ve belirteç imzalama anahtarlarını içerir. Azure AD B2C kiracınızdaki her Kullanıcı akışı için bir JSON meta veri belgesi vardır. Örneğin, fabrikamb2c.onmicrosoft.com kiracısındaki b2c_1_sign_in Kullanıcı akışının meta veri belgesi şu konumda bulunur:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Bu yapılandırma belgesinin özelliklerinden biri `jwks_uri`. Aynı kullanıcı akışının değeri şöyle olacaktır:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Bir KIMLIK belirtecini imzalamak için kullanılan Kullanıcı akışının (ve meta verilerin nereden getirileceği) belirlenmesi için iki seçeneğiniz vardır. İlk olarak, Kullanıcı akış adı `id_token` `acr` talebine dahil edilir. KIMLIK belirtecinden talepleri ayrıştırmaya ilişkin bilgi için [Azure AD B2C belirteç başvurusuna](active-directory-b2c-reference-tokens.md)bakın. Diğer bir seçenek de, isteği keserken `state` parametresi değerindeki Kullanıcı akışını kodlayacaktır. Sonra, hangi Kullanıcı akışının kullanıldığını öğrenmek için `state` parametresinin kodunu çözün. Her iki yöntem de geçerlidir.

Meta veri belgesini OpenID Connect meta veri uç noktasından aldıktan sonra, KIMLIK belirtecinin imzasını doğrulamak için RSA-256 ortak anahtarlarını (bu uç noktada bulunur) kullanabilirsiniz. Bu uç noktada, her biri `kid` tarafından tanımlanan herhangi bir zamanda birden fazla anahtar listelenmiş olabilir. @No__t_0 üstbilgisi ayrıca bir `kid` talebi içerir. Bu, KIMLIK belirtecini imzalamak için bu anahtarların hangisinin kullanıldığını belirtir. [Belirteçleri doğrulama](active-directory-b2c-reference-tokens.md)hakkında bilgi edinmek dahil daha fazla bilgi için [Azure AD B2C belirteç başvurusuna](active-directory-b2c-reference-tokens.md)bakın.
<!--TODO: Improve the information on this-->

KIMLIK belirtecinin imzasını doğruladıktan sonra, birkaç talep doğrulama gerektirir. Örnek:

* Belirteç yeniden yürütme saldırılarını engellemek için `nonce` talebini doğrulayın. Değeri, oturum açma isteğinde belirtidikleriniz olmalıdır.
* KIMLIK belirtecinin uygulamanız için verildiğinden emin olmak için `aud` talebini doğrulayın. Değeri uygulamanızın uygulama KIMLIĞI olmalıdır.
* KIMLIK belirtecinin sona ermediğinden emin olmak için `iat` ve `exp` taleplerini doğrulayın.

Gerçekleştirmeniz gereken birkaç doğrulama daha vardır ve [OpenID Connect Core belirtiminde](https://openid.net/specs/openid-connect-core-1_0.html)ayrıntılı olarak açıklanmıştır. Senaryonuza bağlı olarak ek talepler de doğrulamak isteyebilirsiniz. Bazı ortak doğrulamalar şunları içerir:

* Kullanıcı veya kuruluşun uygulamaya kaydolmasını sağlama.
* Kullanıcının uygun yetkilendirme ve ayrıcalıklara sahip olduğundan emin olma.
* Azure Multi-Factor Authentication kullanarak gibi belirli bir kimlik doğrulama gücünün meydana geldiğinden emin olma.

Bir KIMLIK belirtecindeki talepler hakkında daha fazla bilgi için [Azure AD B2C belirteç başvurusuna](active-directory-b2c-reference-tokens.md)bakın.

KIMLIK belirtecini doğruladıktan sonra kullanıcıyla bir oturum başlatabilirsiniz. Uygulamanızda, Kullanıcı hakkında bilgi edinmek için KIMLIK belirtecindeki talepleri kullanın. Bu bilgiler, görüntüleme, kayıtlar, yetkilendirme vb. için kullanılabilir.

## <a name="get-access-tokens"></a>Erişim belirteçleri al
Web uygulamalarınızın yapması gereken tek şey Kullanıcı akışlarını yürütmek ise, sonraki birkaç bölümü atlayabilirsiniz. Aşağıdaki bölümlerde yer alan bilgiler, yalnızca bir Web API 'sine kimliği doğrulanmış çağrılar yapması gereken ve Azure AD B2C tarafından korunan Web uygulamaları için geçerlidir.

Kullanıcıyı tek sayfalı uygulamanıza imzaladığınıza göre, Azure AD tarafından güvenliği sağlanmış Web API 'Lerini çağırmaya yönelik erişim belirteçleri alabilirsiniz. @No__t_0 yanıt türünü kullanarak zaten bir belirteç almış olsanız bile, kullanıcıyı yeniden oturum açmadan ek kaynaklara yönelik belirteçleri almak için bu yöntemi kullanabilirsiniz.

Tipik bir Web uygulaması akışında `/token` uç noktasına bir istek yaparsınız. Ancak, uç nokta CORS isteklerini desteklemez, bu nedenle bir yenileme belirteci almak için AJAX çağrılarının yapılması bir seçenek değildir. Bunun yerine, diğer Web API 'Lerine yönelik yeni belirteçler almak için, bir gizli HTML iframe öğesinde örtük akışı kullanabilirsiniz. Aşağıda, okunabilirliği için satır sonları içeren bir örnek verilmiştir:

```HTTP
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

| Parametre | Gerekli mi? | Açıklama |
| --- | --- | --- |
|Kiracı| Gereklidir | Azure AD B2C kiracınızın adı|
ilkesinin| Gereklidir| Çalıştırılacak Kullanıcı akışı. Azure AD B2C kiracınızda oluşturduğunuz Kullanıcı akışının adını belirtin. Örneğin: `b2c_1_sign_in`, `b2c_1_sign_up` veya `b2c_1_edit_profile`. |
| client_id |Gereklidir |[Azure Portal](https://portal.azure.com)uygulamanıza atanan uygulama kimliği. |
| response_type |Gereklidir |OpenID Connect oturum açma `id_token` içermelidir.  @No__t_0 yanıt türünü de içerebilir. Burada `token` kullanıyorsanız, uygulamanız yetkilendirme uç noktası için ikinci bir istek yapmadan yetkilendirme uç noktasından hemen bir erişim belirteci alabilir. @No__t_0 yanıt türünü kullanırsanız, `scope` parametresi, belirtecinin hangi kaynağa verilmeyeceğini belirten bir kapsam içermelidir. |
| isteğinde |Önerilen |Uygulamanızın kimlik doğrulama yanıtlarının gönderilebileceği ve alınabileceği, uygulamanızın yeniden yönlendirme URI 'SI. Portalın, URL kodlamalı olması dışında, portala kaydettiğiniz yeniden yönlendirme URI 'lerinden biriyle tam olarak eşleşmesi gerekir. |
| scope |Gereklidir |Kapsamların boşlukla ayrılmış listesi.  Belirteçleri almak için, istenen kaynak için ihtiyaç duyduğunuz tüm kapsamları dahil edin. |
| response_mode |Önerilen |Elde edilen belirteci uygulamanıza geri göndermek için kullanılan yöntemi belirtir. Örtük akış için `fragment` kullanın. Diğer iki mod belirtilebilir, `query` ve `form_post`, ancak örtük akışta çalışmıyor. |
| durum |Önerilen |Belirteç yanıtında döndürülen isteğe eklenen bir değer.  Kullanmak istediğiniz herhangi bir içerik dizesi olabilir.  Genellikle, siteler arası istek sahteciliği saldırıları engellemek için rastgele oluşturulan, benzersiz bir değer kullanılır.  Durum Ayrıca, kimlik doğrulama isteği gerçekleştirilmeden önce kullanıcının uygulamasındaki durumu hakkında bilgi kodlamak için de kullanılır. Örneğin, kullanıcının sayfası veya görünümü. |
| nonce |Gereklidir |Talep olarak elde edilen KIMLIK belirtecine dahil edilen, uygulama tarafından oluşturulan, isteğe dahil edilen bir değer.  Daha sonra uygulama, belirteç yeniden yürütme saldırılarını azaltmak için bu değeri doğrulayabilirler. Genellikle değer, isteğin kaynağını tanımlayan rastgele, benzersiz bir dizedir. |
| isteme |Gereklidir |Gizli bir IFRAME 'de belirteçleri yenilemek ve almak için, iframe 'nin oturum açma sayfasında takılı olmadığından emin olmak için `prompt=none` kullanın ve hemen geri döner. |
| login_hint |Gereklidir |Gizli bir iframe içindeki belirteçleri yenilemek ve almak için, kullanıcının belirli bir zamanda sahip olabileceği birden çok oturumu ayırt etmek için bu ipucuna kullanıcının Kullanıcı adını ekleyin. @No__t_0 talebini kullanarak daha önceki bir oturum açma işleminden Kullanıcı adını ayıklayabilirsiniz (`preferred_username` talebi almak için `profile` kapsamı gerekir). |
| domain_hint |Gereklidir |`consumers` veya `organizations` olabilir.  Gizli bir IFRAME içindeki belirteçleri yenilemek ve almak için, isteğe `domain_hint` değerini ekleyin.  Hangi değerin kullanılacağını (`tid` talebi almak için `profile` kapsamı gereklidir) öğrenmek için daha önceki bir oturum açma KIMLIK belirtecinden `tid` talebini ayıklayın. @No__t_0 talep değeri `9188040d-6c67-4c5b-b112-36a304b66dad`, `domain_hint=consumers` kullanın.  Aksi takdirde `domain_hint=organizations` kullanın. |

@No__t_0 parametresini ayarlayarak, bu istek hemen başarılı olur ya da başarısız olur ve uygulamanıza geri döner.  @No__t_0 parametresinde belirtilen yöntemi kullanılarak, belirtilen yeniden yönlendirme URI 'sindeki uygulamanıza başarılı bir yanıt gönderilir.

### <a name="successful-response"></a>Başarılı yanıt
@No__t_0 kullanarak başarılı bir yanıt Şu örneğe benzer şekilde görünür:

```HTTP
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
| durum |İsteğe bir `state` parametresi dahil edilir, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta `state` değerlerinin özdeş olduğunu doğrulamalıdır. |
| expires_in |Erişim belirtecinin geçerli olduğu süre (saniye cinsinden). |
| scope |Erişim belirtecinin geçerli olduğu kapsamlar. |

### <a name="error-response"></a>Hata yanıtı
Ayrıca, uygulamanın uygun şekilde işleyebilmesi için yeniden yönlendirme URI 'sine de hata yanıtları gönderilebilir.  @No__t_0 için beklenen bir hata şu örneğe benzer şekilde görünür:

```HTTP
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
KIMLIK belirteçleri ve erişim belirteçlerinin her ikisi de kısa bir süre sonra sona erer. Bu belirteçleri düzenli aralıklarla yenilemek için uygulamanızın hazırlanması gerekir.  Her iki tür belirteci yenilemek için, Azure AD adımlarını denetlemek üzere `prompt=none` parametresini kullanarak daha önceki bir örnekte kullandığımız aynı gizli iframe isteğini gerçekleştirin.  Yeni bir `id_token` değeri almak için `response_type=id_token` ve `scope=openid` ve bir `nonce` parametresi kullandığınızdan emin olun.

## <a name="send-a-sign-out-request"></a>Oturum kapatma isteği gönder
Kullanıcıyı uygulamadan dışarı imzalamak istediğinizde oturumu kapatmak için kullanıcıyı Azure AD 'ye yönlendirin. Kullanıcıyı yeniden yönlendirmezseniz, Azure AD ile geçerli bir çoklu oturum açma oturumu olduğundan, kimlik bilgilerini tekrar girmeden uygulamanıza yeniden kimlik doğrulaması yapabiliyor olabilirler.

Kullanıcıyı [kimlik belirtecini doğrulama](#validate-the-id-token)bölümünde açıklanan OpenID Connect meta veri belgesinde listelenen `end_session_endpoint` yeniden yönlendirebilirsiniz. Örnek:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parametre | Gereklidir | Açıklama |
| --------- | -------- | ----------- |
| Kiracı | Yes | Azure AD B2C kiracınızın adı |
| ilkesinin | Yes | Kullanıcıyı uygulamanızda imzalamak için kullanmak istediğiniz kullanıcı akışı. |
| post_logout_redirect_uri | Hayır | Başarılı oturum kapatıldıktan sonra kullanıcının yeniden yönlendirilmesi gereken URL. Dahil değilse, kullanıcıya genel bir ileti gösterir Azure AD B2C. |
| durum | Hayır | İsteğe bir `state` parametresi dahil edilir, yanıtta aynı değer görünmelidir. Uygulama, istek ve yanıtta `state` değerlerinin özdeş olduğunu doğrulamalıdır. |


> [!NOTE]
> Kullanıcıyı `end_session_endpoint` yönlendirerek kullanıcının çoklu oturum açma durumunu Azure AD B2C ile temizler. Ancak, kullanıcının Kullanıcı sosyal kimlik sağlayıcısı oturumundan oturum açmasını engellemez. Kullanıcı sonraki oturum açma sırasında aynı kimlik sağlayıcısını seçerse, kullanıcının kimlik bilgilerini girmeden yeniden kimlik doğrulaması yapılır. Bir Kullanıcı Azure AD B2C uygulamanızın oturumunu kapatmak isterse, örneğin Facebook hesabının oturumunu tamamen kapatmak istedikleri anlamına gelmez. Ancak, yerel hesaplar için kullanıcının oturumu doğru şekilde sona erer.
>

## <a name="next-steps"></a>Sonraki adımlar

### <a name="code-sample-hellojs-with-azure-ad-b2c"></a>Kod örneği: Azure AD B2C ile Hello. js

Azure AD B2C (GitHub) [ile Hello. js üzerinde oluşturulan tek sayfalı uygulama][github-hello-js-example]

GitHub 'daki Bu örnek, [Hello. js][github-hello-js] üzerinde oluşturulmuş basit bir web uygulamasında Azure AD B2C başlamanıza ve açılır stil kimlik doğrulamasını kullanmaya başlamanıza yardımcı olmaya yöneliktir.

<!-- Links - EXTERNAL -->
[github-hello-js-example]: https://github.com/azure-ad-b2c/apps/tree/master/spa/javascript-hellojs-singlepageapp-popup
[github-hello-js]: https://github.com/MrSwitch/hello.js
