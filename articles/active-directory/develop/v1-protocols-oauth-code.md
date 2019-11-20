---
title: Azure AD 'de OAuth 2,0 yetkilendirme kodu akışını anlayın
description: Bu makalede, Azure Active Directory ve OAuth 2,0 kullanarak kiracınızdaki Web uygulamalarına ve Web API 'Lerine erişim yetkisi vermek için HTTP iletilerinin nasıl kullanılacağı açıklanır.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 051565d984196edce0404b12677cf27de9006f29
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175204"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>OAuth 2.0 kod verme akışını kullanarak Azure Active Directory web uygulamalarına erişimi yetkilendirme

> [!NOTE]
>  Sunucuya çağırmayı planladığınız kaynağı söylemezseniz, sunucu bu kaynak için koşullu erişim ilkelerini tetiklemez. Bu nedenle MFA tetikleyicisine sahip olmak için, URL 'nize bir kaynak eklemeniz gerekir. 
>

Azure Active Directory (Azure AD), Azure AD kiracınızdaki Web uygulamalarına ve Web API 'Lerine erişim yetkisi verme olanağı sağlamak için OAuth 2,0 kullanır. Bu kılavuz dilden bağımsızdır ve [Açık kaynaklı kitaplıklarımızın](active-directory-authentication-libraries.md)HIÇBIRINI kullanmadan http iletilerinin nasıl gönderileceğini ve alınacağını açıklar.

OAuth 2,0 yetkilendirme kodu akışı, [oauth 2,0 belirtiminin 4,1 bölümünde](https://tools.ietf.org/html/rfc6749#section-4.1)açıklanmaktadır. Web uygulamaları ve yerel olarak yüklenen uygulamalar dahil olmak üzere çoğu uygulama türünde kimlik doğrulama ve yetkilendirme gerçekleştirmek için kullanılır.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>OAuth 2,0 yetkilendirme akışı

Yüksek düzeyde, bir uygulamanın tüm yetkilendirme akışı şuna benzer bir bit:

![OAuth kimlik doğrulama kod akışı](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Yetkilendirme kodu iste

Yetkilendirme kodu akışı, kullanıcıyı `/authorize` uç noktasına yönlendiren istemciyle başlar. Bu istekte istemci, kullanıcıdan almaları gereken izinleri gösterir. Azure portal **Uygulama kayıtları > uç noktaları** ' nı seçerek kiracınızın OAuth 2,0 yetkilendirme uç noktasını alabilirsiniz.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parametre |  | Açıklama |
| --- | --- | --- |
| Kiracı |Gerekli |İsteğin yolundaki `{tenant}` değeri, uygulamada kimlerin oturum açmasını denetlemek için kullanılabilir. İzin verilen değerler, kiracı tanımlayıcılarıdır; Örneğin, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` veya `contoso.onmicrosoft.com` ya da kiracıdan bağımsız belirteçler için `common` |
| client_id |Gerekli |Azure AD ile kaydettiğinizde uygulamanıza atanan uygulama KIMLIĞI. Bunu Azure portalında bulabilirsiniz. Hizmetler kenar çubuğunda **Azure Active Directory** ' a tıklayın, **uygulama kayıtları**' a tıklayın ve uygulamayı seçin. |
| response_type |Gerekli |, Yetkilendirme kodu akışı için `code` içermelidir. |
| isteğinde |Önerilen |Uygulamanızın kimlik doğrulama yanıtlarının gönderilebileceği ve alınabileceği, uygulamanızın redirect_uri 'ı. Portalın, URL kodlamalı olması dışında, portala kaydettiğiniz redirect_uris biriyle tam olarak eşleşmesi gerekir. Yerel & Mobile Apps için `urn:ietf:wg:oauth:2.0:oob` varsayılan değerini kullanmanız gerekir. |
| response_mode |Seçim |Elde edilen belirteci uygulamanıza geri göndermek için kullanılması gereken yöntemi belirtir. `query`, `fragment`veya `form_post`olabilir. `query`, yeniden yönlendirme URI 'niz üzerinde bir sorgu dizesi parametresi olarak kodu sağlar. Örtük akışı kullanarak bir KIMLIK belirteci isteğinde bulunduğsanız, [OpenID belirtiminde](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)belirtilen şekilde `query` kullanamazsınız. Yalnızca kod isteğinde bulunduğsanız `query`, `fragment`veya `form_post`kullanabilirsiniz. `form_post` yeniden yönlendirme URI 'nize kodu içeren bir GÖNDERI yürütür. Varsayılan değer bir kod akışı için `query`.  |
| durum |Önerilen |İstekte bulunan, belirteç yanıtında de döndürülen bir değer. Rastgele oluşturulan benzersiz bir değer genellikle [siteler arası istek sahteciliği saldırılarını önlemek](https://tools.ietf.org/html/rfc6749#section-10.12)için kullanılır. Durum Ayrıca, kullanıcının uygulamadaki durumu hakkında bilgi kodlamak için kullanılır; Örneğin, bulunan sayfa veya görünüm gibi kimlik doğrulama isteği gerçekleştirilmeden önce. |
| Kaynak | Önerilen |Hedef Web API 'sinin (güvenli kaynak) uygulama KIMLIĞI URI 'SI. Uygulama KIMLIĞI URI 'sini bulmak için Azure portalında **Azure Active Directory**' a tıklayın, **uygulama kayıtları**' na tıklayın, uygulamanın **Ayarlar** sayfasını açın ve ardından **Özellikler**' e tıklayın. Ayrıca, `https://graph.microsoft.com` gibi bir dış kaynak da olabilir. Bu, yetkilendirme ya da belirteç isteklerinden birinde gereklidir. Daha az kimlik doğrulama isteminin, kullanıcıdan onay aldığından emin olmak için bunu yetkilendirme isteğine yerleştirdiğinden emin olmak için. |
| scope | **LIP** | V1 Azure AD uygulamaları için kapsamlar, Azure portalında uygulamalar **ayarları**altında, **gerekli izinler**altında statik olarak yapılandırılmalıdır. |
| isteme |Seçim |Gerekli kullanıcı etkileşiminin türünü belirtin.<p> Geçerli değerler şunlardır: <p> *oturum açma*: kullanıcıdan yeniden kimlik doğrulaması yapması istenir. <p> *select_account*: kullanıcıdan bir hesap seçmesi ve çoklu oturum açmayı kesintiye uğratma istenir. Kullanıcı, mevcut bir oturum açmış hesabı seçebilir, hatırlanan bir hesap için kimlik bilgilerini girebilir veya tamamen farklı bir hesap kullanmayı tercih edebilir. <p> *onay*: Kullanıcı izni verildi, ancak güncelleştirilmesi gerekiyor. Kullanıcıdan onay girmesi istenir. <p> *admin_consent*: bir yöneticinin kuruluşlarındaki tüm kullanıcılar adına onay girmeleri istenir |
| login_hint |Seçim |Kullanıcı adının bir süre önce bilinerek Kullanıcı için oturum açma sayfasının Kullanıcı adı/e-posta adresi alanını önceden doldurmanız için kullanılabilir. Genellikle uygulamalar bu parametreyi yeniden oluşturma sırasında kullanır ve `preferred_username` talebini kullanarak önceki bir oturum açma işleminden zaten Kullanıcı adını ayıklamış olur. |
| domain_hint |Seçim |Kullanıcının oturum açmak için kullanması gereken kiracı veya etki alanı hakkında bir ipucu sağlar. Domain_hint değeri, kiracının kayıtlı bir etki alanıdır. Kiracı şirket içi bir dizine federe ise, AAD belirtilen kiracı federasyon sunucusuna yeniden yönlendirir. |
| code_challenge_method | Önerilen    | `code_challenge` parametresi için `code_verifier` kodlamak için kullanılan yöntem. `plain` veya `S256`biri olabilir. Dışlanmazsa, `code_challenge` dahil edildikçe `code_challenge` düz metin olarak kabul edilir. Azure AAD v 1.0 hem `plain` hem de `S256` destekler. Daha fazla bilgi için bkz. [Pkce RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | Önerilen    | Yerel veya genel bir istemciden kod değişimi (PKCE) için kanıt anahtarı aracılığıyla yetkilendirme kodu yetkisini güvenli hale getirmek için kullanılır. `code_challenge_method` varsa gereklidir. Daha fazla bilgi için bkz. [Pkce RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Kullanıcı bir kuruluşun parçasıysa, kuruluşun yöneticisi kullanıcının adına izin verebilir veya reddedebilir veya kullanıcının izin vermesini sağlayabilir. Kullanıcıya yalnızca yönetici izin veriyorsa izin verilir.
>
>

Bu noktada, kullanıcıdan Azure portalında uygulama tarafından istenen izinlerin kimlik bilgilerini ve onayını girmesi istenir. Kullanıcı kimlik doğrulamasından ve onay vermiş olduktan sonra, Azure AD, kod ile talebinizdeki `redirect_uri` adreste uygulamanıza bir yanıt gönderir.

### <a name="successful-response"></a>Başarılı yanıt
Başarılı bir yanıt şöyle görünebilir:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parametre | Açıklama |
| --- | --- |
| admin_consent |Bir yönetici bir izin isteği istemine kabul etmişse değer true 'dur. |
| Kodudur |Uygulamanın istediği yetkilendirme kodu. Uygulama, hedef kaynak için bir erişim belirteci istemek üzere yetkilendirme kodunu kullanabilir. |
| session_state |Geçerli Kullanıcı oturumunu tanımlayan benzersiz bir değer. Bu değer bir GUID 'dir, ancak incelenmesi gerekmeden geçirilen donuk bir değer olarak değerlendirilmelidir. |
| durum |İsteğe bir durum parametresi dahil edilir, yanıtta aynı değer görünmelidir. Bu, uygulamanın istek ve yanıt içindeki durum değerlerinin yanıtı kullanmadan önce özdeş olduğunu doğrulaması için iyi bir uygulamadır. Bu, istemciye karşı [siteler arası Istek forgery (CSRF) saldırılarını](https://tools.ietf.org/html/rfc6749#section-10.12) algılamaya yardımcı olur. |

### <a name="error-response"></a>Hata yanıtı
Ayrıca, uygulamanın bunları uygun şekilde işleyebilmesi için `redirect_uri` hata yanıtları da gönderilebilir.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parametre | Açıklama |
| --- | --- |
| error |[OAuth 2,0 yetkilendirme çerçevesinin](https://tools.ietf.org/html/rfc6749)5,2 bölümünde tanımlanan bir hata kodu değeri. Sonraki tabloda, Azure AD 'nin döndürdüğü hata kodları açıklanmaktadır. |
| error_description |Hatanın daha ayrıntılı bir açıklaması. Bu ileti, son kullanıcı kullanımı için tasarlanmamıştır. |
| durum |Durum değeri, istekte gönderilen ve siteler arası istek sahteciliği (CSRF) saldırılarını önlemeye yönelik yanıtta döndürülen rastgele oluşturulmuş bir yeniden kullanılabilir olmayan değerdir. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Yetkilendirme uç noktası hataları için hata kodları
Aşağıdaki tabloda hata yanıtının `error` parametresinde döndürülebilecek çeşitli hata kodları açıklanmaktadır.

| Hata kodu | Açıklama | İstemci eylemi |
| --- | --- | --- |
| invalid_request |Eksik gerekli bir parametre gibi protokol hatası. |İsteği onarın ve yeniden gönderin. Bu bir geliştirme hatasıdır ve genellikle ilk test sırasında yakalanır. |
| unauthorized_client |İstemci uygulamasının bir yetkilendirme kodu istemesine izin verilmiyor. |Bu durum genellikle istemci uygulaması Azure AD 'ye kaydedilmediğinde veya kullanıcının Azure AD kiracısına eklenmediğinde oluşur. Uygulama kullanıcıya uygulamayı yükleme ve Azure AD 'ye ekleme yönergesini isteyebilir. |
| access_denied |Kaynak sahibi reddedildi onayı |İstemci uygulaması, kullanıcıya Kullanıcı tarafından bağlanmadığı takdirde devam edemediği konusunda bildirimde bulunabilir. |
| unsupported_response_type |Yetkilendirme sunucusu istekteki yanıt türünü desteklemiyor. |İsteği onarın ve yeniden gönderin. Bu bir geliştirme hatasıdır ve genellikle ilk test sırasında yakalanır. |
| server_error |Sunucu beklenmeyen bir hatayla karşılaştı. |İsteği yeniden deneyin. Bu hatalar geçici koşullardan kaynaklanabilir. İstemci uygulaması, isteği geçici bir hata nedeniyle geciktirildiği kullanıcıya açıklayabilir. |
| temporarily_unavailable |Sunucu, isteği işlemek için geçici olarak çok meşgul. |İsteği yeniden deneyin. İstemci uygulaması, yanıtı, geçici bir durum nedeniyle geciktiğinde kullanıcıya açıklayabilir. |
| invalid_resource |Hedef kaynak geçersiz, çünkü mevcut değil, Azure AD bu dosyayı bulamıyor veya doğru şekilde yapılandırılmamış. |Bu, varsa kaynağın kiracıda yapılandırılmamış olduğunu gösterir. Uygulama kullanıcıya uygulamayı yükleme ve Azure AD 'ye ekleme yönergesini isteyebilir. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Erişim belirteci istemek için yetkilendirme kodunu kullanma
Artık bir yetkilendirme kodu edindiniz ve Kullanıcı tarafından izin verildiğinden, `/token` uç noktasına bir POST isteği göndererek istenen kaynağa erişim belirtecinin kodunu kullanabilirsiniz:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parametre |  | Açıklama |
| --- | --- | --- |
| Kiracı |Gerekli |İsteğin yolundaki `{tenant}` değeri, uygulamada kimlerin oturum açmasını denetlemek için kullanılabilir. İzin verilen değerler, kiracı tanımlayıcılarıdır; Örneğin, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` veya `contoso.onmicrosoft.com` ya da kiracıdan bağımsız belirteçler için `common` |
| client_id |Gerekli |Azure AD ile kaydettiğinizde uygulamanıza atanan uygulama kimliği. Bunu Azure portal bulabilirsiniz. Uygulama kimliği, uygulama kaydının ayarlarında görüntülenir. |
| grant_type |Gerekli |Yetkilendirme kodu akışı için `authorization_code` olmalıdır. |
| Kodudur |Gerekli |Önceki bölümde aldığınız `authorization_code` |
| isteğinde |Gerekli | İstemci uygulamasına kayıtlı bir `redirect_uri`. |
| client_secret |Web uygulamaları için gerekli, ortak istemcilerde izin verilmiyor |**Anahtarlar**altındaki uygulamanız Için Azure portalında oluşturduğunuz uygulama gizli anahtarı. Client_secrets cihazlarda güvenilir bir şekilde depolanamadığı için yerel bir uygulamada (ortak istemci) kullanılamaz. `client_secret` sunucu tarafında güvenli bir şekilde depolayabilme özelliğine sahip Web uygulamaları ve Web API 'Leri (tüm gizli istemciler) için gereklidir. Client_secret gönderilmeden önce URL kodlamalı olmalıdır. |
| Kaynak | Önerilen |Hedef Web API 'sinin (güvenli kaynak) uygulama KIMLIĞI URI 'SI. Uygulama KIMLIĞI URI 'sini bulmak için Azure portalında **Azure Active Directory**' a tıklayın, **uygulama kayıtları**' na tıklayın, uygulamanın **Ayarlar** sayfasını açın ve ardından **Özellikler**' e tıklayın. Ayrıca, `https://graph.microsoft.com` gibi bir dış kaynak da olabilir. Bu, yetkilendirme ya da belirteç isteklerinden birinde gereklidir. Daha az kimlik doğrulama isteminin, kullanıcıdan onay aldığından emin olmak için bunu yetkilendirme isteğine yerleştirdiğinden emin olmak için. Hem yetkilendirme isteğinde hem de belirteç isteğinde, kaynak ' parametrelerinin eşleşmesi gerekir. | 
| code_verifier | Seçim | Authorization_code elde etmek için kullanılan aynı code_verifier. Yetkilendirme kodu verme isteğinde PKCE kullanılmışsa gereklidir. Daha fazla bilgi için bkz. [Pkce RFC](https://tools.ietf.org/html/rfc7636)   |

Uygulama KIMLIĞI URI 'sini bulmak için Azure portalında **Azure Active Directory**' a tıklayın, **uygulama kayıtları**' na tıklayın, uygulamanın **Ayarlar** sayfasını açın ve ardından **Özellikler**' e tıklayın.

### <a name="successful-response"></a>Başarılı yanıt
Azure AD, başarılı bir yanıt üzerine bir [erişim belirteci](access-tokens.md) döndürür. İstemci uygulamasından gelen ağ çağrılarını ve bunlarla ilişkili gecikme süresini en aza indirmek için, istemci uygulaması OAuth 2,0 yanıtında belirtilen belirteç ömrü için erişim belirteçlerini önbelleğe almalıdır. Belirteç ömrünü öğrenmek için `expires_in` veya `expires_on` parametre değerlerini kullanın.

Bir Web API 'SI kaynağı bir `invalid_token` hata kodu döndürürse, bu, kaynağın belirtecin dolduğunu belirlediğini gösteriyor olabilir. İstemci ve kaynak saat zamanları farklıysa ("zaman sapması" olarak bilinir), istemci önbelleğinden belirteç temizlenmeden önce kaynak belirtecin süresinin dolabileceğini düşünemez. Bu durumda, hala hesaplanan ömrü içinde olsa bile, önbellekteki belirteci temizleyin.

Başarılı bir yanıt şöyle görünebilir:

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| Parametre | Açıklama |
| --- | --- |
| access_token |İstenen erişim belirteci.  Bu, opak bir dizedir; kaynağın alma beklediği görünüme bağlıdır ve istemcinin istemci tarafından görünmesi için tasarlanmamıştır. Uygulama, bir Web API 'SI gibi güvenli kaynak üzerinde kimlik doğrulaması yapmak için bu belirteci kullanabilir. |
| token_type |Belirteç türü değerini gösterir. Azure AD 'nin desteklediği tek tür taşıyıcı. Taşıyıcı belirteçleri hakkında daha fazla bilgi için bkz [. OAuth 2.0 yetkilendirme çerçevesi: taşıyıcı belirteç kullanımı (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Erişim belirtecinin geçerli olduğu süre (saniye cinsinden). |
| expires_on |Erişim belirtecinin süre sonu. Tarih, 1970-01-01T0:0: 0Z UTC 'den sona erme zamanına kadar saniye sayısı olarak gösterilir. Bu değer, önbelleğe alınmış belirteçlerin ömrünü belirlemede kullanılır. |
| Kaynak |Web API 'sinin (güvenli kaynak) uygulama KIMLIĞI URI 'SI. |
| scope |İstemci uygulamasına kimliğe bürünme izinleri verildi. Varsayılan izin `user_impersonation`. Güvenli kaynağın sahibi Azure AD 'ye ek değerler kaydedebilir. |
| refresh_token |Bir OAuth 2,0 yenileme belirteci. Uygulama, geçerli erişim belirtecinin süresi dolduktan sonra ek erişim belirteçleri almak için bu belirteci kullanabilir. Belirteçleri yenileme uzun süreli olduğundan, uzun süre boyunca kaynaklara erişimi sürdürmek için kullanılabilir. |
| id_token |Bir [kimlik belirtecini](id-tokens.md)temsil eden işaretsiz bir JSON Web token (JWT). Uygulama, oturum açan kullanıcı hakkında bilgi istemek için bu belirtecin segmentlerinin kodunu çözer base64Url. Uygulama değerleri önbelleğe alabilir ve görüntüleyebilir, ancak herhangi bir yetkilendirme veya güvenlik sınırları için bu değere dayanmamalıdır. |

JSON Web belirteçleri hakkında daha fazla bilgi için bkz. [JWT IETF taslak belirtimi](https://go.microsoft.com/fwlink/?LinkId=392344).   `id_tokens`hakkında daha fazla bilgi edinmek için, bkz. [v 1.0 OpenID Connect Flow](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Hata yanıtı
Belirteç verme uç noktası hataları, istemci, belirteç verme uç noktasını doğrudan çağırdığı için HTTP hata kodlarıdır. HTTP durum koduna ek olarak, Azure AD belirteç verme uç noktası, hatayı tanımlayan nesneler içeren bir JSON belgesi de döndürür.

Örnek hata yanıtı şuna benzeyebilir:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parametre | Açıklama |
| --- | --- |
| error |Oluşan hata türlerini sınıflandırmak için kullanılabilen ve hatalara yanıt vermek için kullanılabilen bir hata kodu dizesi. |
| error_description |Bir geliştiricinin kimlik doğrulama hatasının kök nedenini belirlemesine yardımcı olabilecek belirli bir hata iletisi. |
| error_codes |Tanılamada yardımcı olabilecek STS 'ye özgü hata kodlarının bir listesi. |
| timestamp |Hatanın oluştuğu zaman. |
| trace_id |Tanılamada yardımcı olabilecek istek için benzersiz bir tanımlayıcı. |
| correlation_id |İsteğe ait, bileşenler genelinde tanılamada yardımcı olabilecek benzersiz bir tanımlayıcı. |

#### <a name="http-status-codes"></a>HTTP durum kodları
Aşağıdaki tabloda, belirteç verme uç noktasının döndürdüğü HTTP durum kodları listelenmektedir. Bazı durumlarda, yanıtı anlatmak için hata kodu yeterlidir, ancak hatalar varsa, eşlik eden JSON belgesini ayrıştırarak hata kodunu inceleyebilirsiniz.

| HTTP Kodu | Açıklama |
| --- | --- |
| 400 |Varsayılan HTTP kodu. Çoğu durumda kullanılır ve genellikle hatalı oluşturulmuş bir istek nedeniyle olur. İsteği onarın ve yeniden gönderin. |
| 401 |Kimlik doğrulama başarısız oldu. Örneğin, istekte client_secret parametresi eksik. |
| 403 |Yetkilendirme başarısız oldu. Örneğin, kullanıcının kaynağa erişim izni yok. |
| 500 |Hizmette bir iç hata oluştu. İsteği yeniden deneyin. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Belirteç uç noktası hataları için hata kodları
| Hata kodu | Açıklama | İstemci eylemi |
| --- | --- | --- |
| invalid_request |Eksik gerekli bir parametre gibi protokol hatası. |İsteği onarın ve yeniden gönderin |
| invalid_grant |Yetkilendirme kodu geçersiz veya süresi doldu. |`/authorize` uç noktasına yeni bir istek deneyin |
| unauthorized_client |Kimliği doğrulanmış istemcinin bu yetkilendirme verme türünü kullanma yetkisi yok. |Bu durum genellikle istemci uygulaması Azure AD 'ye kaydedilmediğinde veya kullanıcının Azure AD kiracısına eklenmediğinde oluşur. Uygulama kullanıcıya uygulamayı yükleme ve Azure AD 'ye ekleme yönergesini isteyebilir. |
| invalid_client |İstemci kimlik doğrulaması başarısız oldu. |İstemci kimlik bilgileri geçerli değil. Bu uygulamayı onarmak için, uygulama Yöneticisi kimlik bilgilerini güncelleştirir. |
| unsupported_grant_type |Yetkilendirme sunucusu yetkilendirme verme türünü desteklemiyor. |İstekteki izin türünü değiştirin. Bu tür bir hata yalnızca geliştirme sırasında ve ilk test sırasında algılanarak gerçekleştirilmelidir. |
| invalid_resource |Hedef kaynak geçersiz, çünkü mevcut değil, Azure AD bu dosyayı bulamıyor veya doğru şekilde yapılandırılmamış. |Bu, varsa kaynağın kiracıda yapılandırılmamış olduğunu gösterir. Uygulama kullanıcıya uygulamayı yükleme ve Azure AD 'ye ekleme yönergesini isteyebilir. |
| interaction_required |İstek, Kullanıcı etkileşimi gerektirir. Örneğin, ek bir kimlik doğrulama adımı gereklidir. | Etkileşimli olmayan bir istek yerine, aynı kaynak için etkileşimli bir yetkilendirme isteğiyle yeniden deneyin. |
| temporarily_unavailable |Sunucu, isteği işlemek için geçici olarak çok meşgul. |İsteği yeniden deneyin. İstemci uygulaması, yanıtı, geçici bir durum nedeniyle geciktiğinde kullanıcıya açıklayabilir. |

## <a name="use-the-access-token-to-access-the-resource"></a>Kaynağa erişmek için erişim belirtecini kullanma
Bir `access_token`başarıyla edindiniz, bu belirteci Web API 'Lerine yönelik isteklerde kullanarak, `Authorization` üstbilgisine dahil edebilirsiniz. [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) belirtiminde, korumalı kaynaklara erışmek için http isteklerinde taşıyıcı belirteçlerinin nasıl kullanılacağı açıklanmaktadır.

### <a name="sample-request"></a>Örnek istek
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Hata yanıtı
RFC 6750 sorun HTTP durum kodlarını uygulayan güvenli kaynaklar. İstek kimlik doğrulama kimlik bilgilerini içermiyorsa veya belirteç eksikse, yanıt bir `WWW-Authenticate` üst bilgisi içerir. Bir istek başarısız olduğunda, kaynak sunucusu HTTP durum kodu ve bir hata kodu ile yanıt verir.

İstemci isteği taşıyıcı belirtecini içermiyorsa, aşağıdaki başarısız bir yanıt örneğidir:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Hata parametreleri
| Parametre | Açıklama |
| --- | --- |
| authorization_uri |Yetkilendirme sunucusunun URI 'SI (fiziksel uç noktası). Bu değer, bir bulma uç noktasından sunucu hakkında daha fazla bilgi almak için bir arama anahtarı olarak da kullanılır. <p><p> İstemcinin, yetkilendirme sunucusunun güvenilir olduğunu doğrulaması gerekir. Kaynak Azure AD tarafından korunduğunda, URL 'nin https://login.microsoftonline.com veya Azure AD 'nin desteklediği başka bir ana bilgisayar adı ile başladığını doğrulamak yeterlidir. Kiracıya özgü bir kaynak her zaman kiracıya özgü bir yetkilendirme URI 'SI döndürmelidir. |
| error |[OAuth 2,0 yetkilendirme çerçevesinin](https://tools.ietf.org/html/rfc6749)5,2 bölümünde tanımlanan bir hata kodu değeri. |
| error_description |Hatanın daha ayrıntılı bir açıklaması. Bu ileti, son kullanıcı kullanımı için tasarlanmamıştır. |
| resource_id |Kaynağın benzersiz tanımlayıcısını döndürür. İstemci uygulaması, kaynak için bir belirteç istediğinde `resource` parametresinin değeri olarak bu tanımlayıcıyı kullanabilir. <p><p> İstemci uygulamasının bu değeri doğrulaması önemlidir, aksi takdirde kötü amaçlı bir hizmet ayrıcalık **yükselmesi** saldırısı yapabilir <p><p> Bir saldırının önlenmesi için önerilen strateji, `resource_id` erişilmekte olan Web API URL 'siyle aynı olduğunu doğrulamadır. Örneğin, https://service.contoso.com/data erişilmesi durumunda `resource_id` https://service.contoso.com/ olabilir. Kimliği doğrulamak için güvenilir bir alternatif yol olmadıkça, istemci uygulamanın temel URL ile başlamayan bir `resource_id` reddetmesi gerekir. |

#### <a name="bearer-scheme-error-codes"></a>Taşıyıcı şeması hata kodları
RFC 6750 belirtimi, yanıtta WWW-Authenticate üst bilgisini ve taşıyıcı şemasını kullanan kaynaklar için aşağıdaki hataları tanımlar.

| HTTP durum kodu | Hata kodu | Açıklama | İstemci eylemi |
| --- | --- | --- | --- |
| 400 |invalid_request |İstek düzgün biçimlendirilmemiş. Örneğin, bir parametre eksik veya iki kez aynı parametreyi kullanıyor olabilir. |Hatayı düzeltip isteği yeniden deneyin. Bu tür bir hata yalnızca geliştirme sırasında ve başlangıç testinde algılanarak gerçekleştirilmelidir. |
| 401 |invalid_token |Erişim belirteci eksik, geçersiz veya iptal edildi. Error_description parametresinin değeri ek ayrıntı sağlar. |Yetkilendirme sunucusundan yeni bir belirteç isteyin. Yeni belirteç başarısız olursa beklenmeyen bir hata oluştu. Kullanıcıya bir hata iletisi gönderin ve rastgele gecikmelerden sonra yeniden deneyin. |
| 403 |insufficient_scope |Erişim belirteci, kaynağa erişmek için gereken kimliğe bürünme izinlerini içermiyor. |Yetkilendirme uç noktasına yeni bir yetkilendirme isteği gönderin. Yanıt kapsam parametresini içeriyorsa, kaynak isteğindeki kapsam değerini kullanın. |
| 403 |insufficient_access |Belirtecin konusu kaynağa erişmek için gerekli izinlere sahip değil. |Kullanıcıdan farklı bir hesap kullanmasını veya belirtilen kaynağa izin istemesini iste. |

## <a name="refreshing-the-access-tokens"></a>Erişim belirteçleri yenileniyor

Erişim belirteçleri kısa süreli olduğundan, kaynaklara erişmeye devam etmek için süreleri dolduktan sonra yenilenmelidir. `/token` uç noktasına başka bir `POST` isteği göndererek `access_token` yenileyebilirsiniz, ancak bu kez `code`yerine `refresh_token` sağlar.  Yenileme belirteçleri, istemcinize erişim izni verilen tüm kaynaklar için geçerlidir. bu nedenle, `resource=https://graph.microsoft.com` için bir istekte verilen yenileme belirteci, `resource=https://contoso.com/api` için yeni bir erişim belirteci istemek üzere kullanılabilir. 

Yenileme belirteçlerinin belirtilen ömürleri yok. Genellikle, yenileme belirteçlerinin yaşam süreleri nispeten uzundur. Ancak, bazı durumlarda belirteçleri yenileme süre sonu, iptal etme veya istenen eylem için yeterli ayrıcalıklara sahip değil. Uygulamanızın belirteç verme uç noktası tarafından döndürülen hataları beklemesi ve işlemesi gerekir.

Yenileme belirteci hatası ile bir yanıt aldığınızda, geçerli yenileme belirtecini atın ve yeni bir yetkilendirme kodu veya erişim belirteci isteyin. Özellikle, yetkilendirme kodu verme akışında bir yenileme belirteci kullanırken, `interaction_required` veya `invalid_grant` hata kodlarıyla bir yanıt alırsanız yenileme belirtecini atın ve yeni bir yetkilendirme kodu isteyin.

Bir yenileme belirteci kullanarak yeni bir erişim belirteci almak için **kiracıya özgü** uç noktaya yönelik örnek bir istek ( **ortak** uç nokta da kullanabilirsiniz) şöyle görünür:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### <a name="successful-response"></a>Başarılı yanıt
Başarılı bir belirteç yanıtı şöyle görünür:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| Parametre | Açıklama |
| --- | --- |
| token_type |Belirteç türü. Yalnızca **taşıyıcı**değeri desteklenir. |
| expires_in |Belirtecin saniye cinsinden kalan süresi. Tipik bir değer 3600 ' dir (bir saat). |
| expires_on |Belirtecin süresinin dolacağı tarih ve saat. Tarih, 1970-01-01T0:0: 0Z UTC 'den sona erme zamanına kadar saniye sayısı olarak gösterilir. |
| Kaynak |Erişim belirtecinin erişim için kullanılabileceği güvenli kaynağı tanımlar. |
| scope |Yerel istemci uygulamasına kimliğe bürünme izinleri verildi. Varsayılan izin **user_impersonation**' dir. Hedef kaynağın sahibi Azure AD 'ye alternatif değerler kaydedebilir. |
| access_token |İstenen yeni erişim belirteci. |
| refresh_token |Bu yanıttaki bir süre sona erdiğinde yeni erişim belirteçleri istemek için kullanılabilen yeni bir OAuth 2,0 refresh_token. |

### <a name="error-response"></a>Hata yanıtı
Örnek hata yanıtı şuna benzeyebilir:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parametre | Açıklama |
| --- | --- |
| error |Oluşan hata türlerini sınıflandırmak için kullanılabilen ve hatalara yanıt vermek için kullanılabilen bir hata kodu dizesi. |
| error_description |Bir geliştiricinin kimlik doğrulama hatasının kök nedenini belirlemesine yardımcı olabilecek belirli bir hata iletisi. |
| error_codes |Tanılamada yardımcı olabilecek STS 'ye özgü hata kodlarının bir listesi. |
| timestamp |Hatanın oluştuğu zaman. |
| trace_id |Tanılamada yardımcı olabilecek istek için benzersiz bir tanımlayıcı. |
| correlation_id |İsteğe ait, bileşenler genelinde tanılamada yardımcı olabilecek benzersiz bir tanımlayıcı. |

Hata kodlarının ve önerilen istemci eyleminin açıklaması için bkz. [belirteç uç noktası hataları Için hata kodları](#error-codes-for-token-endpoint-errors).

## <a name="next-steps"></a>Sonraki adımlar
Azure AD v 1.0 uç noktası ve Web uygulamalarınıza ve Web API 'Lerine kimlik doğrulama ve yetkilendirme ekleme hakkında daha fazla bilgi edinmek için bkz. [örnek uygulamalar](sample-v1-code.md).
