---
title: Kullanıcı kaydı ve ürün aboneliği temsilcisi seçme
description: Azure API Management 'de bir üçüncü tarafa Kullanıcı kaydı ve ürün aboneliği temsilcisi seçme hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: 63ff91c6b4db351e5ec72973874466cff74432b5
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073457"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Kullanıcı kaydı ve ürün aboneliği temsilcisi seçme

Temsili, geliştirici portalındaki yerleşik işlevselliği kullanmanın aksine, geliştirici oturum açma/kaydolma ve ürün aboneliklerinizi işlemek için mevcut Web sitenizi kullanmanıza olanak sağlar. Bu, Web sitenizin kullanıcı verilerine sahip olması ve bu adımların doğrulanmasını özel bir şekilde gerçekleştirmesini sağlar.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>Geliştirici için temsilci seçme ve kaydolma

Geliştirici için oturum açın ve mevcut Web sitenize kaydolun, sitenizde özel bir temsilci uç noktası oluşturmanız gerekir. API Management geliştirici portalından başlatılan bu tür bir istek için giriş noktası olarak görev yapması gerekir.

Son iş akışı aşağıdaki gibi olacaktır:

1. Geliştirici API Management geliştirici portalındaki oturum açma veya kaydolma bağlantısına tıklasa
2. Tarayıcı, yetkilendirme uç noktasına yeniden yönlendirildi
3. Dönüş içinde temsil eden bir uç nokta, veya kullanıcıdan oturum açmasını veya kaydolmayı isteyen Kullanıcı ARABIRIMINI buraya yönlendirir.
4. Başarılı olduğunda Kullanıcı, başlattıkları API Management geliştirici portalı sayfasına yeniden yönlendirilir.

Başlamak için ilk olarak istekleri, yetkilendirme uç noktanız aracılığıyla yönlendirmek üzere ayarlama API Management. Azure portal, API Management kaynağınızın **güvenlik** araması yapın ve ardından **temsili** öğesine tıklayın. ' Oturum açma & kaydolma ' özelliğini etkinleştirmek için onay kutusuna tıklayın.

![Temsili sayfası][api-management-delegation-signin-up]

* Özel temsili uç noktanızın URL 'sinin ne olacağına karar verin ve bunu, **temsili uç nokta URL 'si** alanına girin. 
* Yetkilendirme kimlik doğrulaması anahtarı alanında, isteğin Azure API Management gerçekten geldiğinden emin olmak için, doğrulama için sağlanmış bir imzayı hesaplamak üzere kullanılacak bir gizli dizi girin. Sizin için rastgele bir anahtar oluşturmak API Management **oluşturmak için Oluştur** düğmesine tıklayabilirsiniz.

Şimdi, **Yetkilendirme uç noktası**oluşturmanız gerekir. Birkaç işlem yapması gerekebilir:

1. Aşağıdaki biçimde bir istek alın:
   
   > *http:\//www.yourwebsite.com/apimdelegation? Operation = SignIn & returnurl = {kaynak sayfanın URL 'si} & anahtar = {String} & SIG = {String}*
   > 
   > 
   
    Oturum açma/kaydolma çalışması için sorgu parametreleri:
   
   * **işlem**: ne tür bir temsilcinin olduğunu tanımlar; bu durumda yalnızca **oturum** açabilir
   * **ReturnUrl**: kullanıcının oturum açma veya kaydolma bağlantısında tıklattığı sayfanın URL 'si
   * **anahtar**: güvenlik karmasında işlem yapmak için kullanılan özel bir anahtar dizesi
   * **SIG**: kendi hesaplanmış karmaınızla karşılaştırmak için kullanılacak bir hesaplanan güvenlik karması
2. İsteğin Azure API Management geldiğini doğrulayın (isteğe bağlı, ancak güvenlik için önemle önerilir)
   
   * **ReturnUrl** ve **anahtar** sorgu PARAMETRELERINE göre BIR dizenin HMAC-SHA512 olur karmasını hesaplama ([aşağıda belirtilen örnek kod]):
     
     > HMAC (**anahtar** + ' \n ' + **ReturnUrl**)
     > 
     > 
   * Yukarıdaki hesaplanmış karmayı **SIG** sorgu parametresinin değeriyle karşılaştırın. İki karma eşleşiyorsa, sonraki adıma geçin, aksi takdirde isteği reddedin.
3. Oturum açma/kaydolma için bir istek aldığınızı doğrulayın: **işlem** sorgu parametresi "**SignIn**" olarak ayarlanacak.
4. Kullanıcıyı oturum açmak veya kaydolmak için kullanıcı ARABIRIMI ile sunun
5. Kullanıcı kaydolduktan sonra, API Management için bunlara karşılık gelen bir hesap oluşturmanız gerekir. API Management REST API [Kullanıcı Oluştur] . Bunu yaparken, kullanıcı KIMLIĞINI Kullanıcı deponuzdaki aynı değere veya izlediğiniz bir KIMLIĞE ayarlamış olduğunuzdan emin olun.
6. Kullanıcının kimliği başarıyla doğrulandığında:
   
   * API Management ile [Çoklu oturum açma (SSO) belirteci isteme] REST API
   * Yukarıdaki API çağrısından aldığınız SSO URL 'sine bir returnUrl sorgu parametresi ekleyin:
     
     > Örneğin, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * kullanıcıyı yukarıda üretilen URL 'ye yönlendir

**Oturum açma** işlemine ek olarak, önceki adımları izleyerek ve aşağıdaki işlemlerden birini kullanarak hesap yönetimi de gerçekleştirebilirsiniz:

* **Parola**
* **ChangeProfile**
* **CloseAccount**

Hesap yönetimi işlemleri için aşağıdaki sorgu parametrelerini geçirmeniz gerekir.

* **işlem**: ne tür bir temsili isteğin olduğunu tanımlar (ChangePassword, changeprofile veya closeaccount)
* **Kullanıcı**kimliği: yönetilecek HESABıN Kullanıcı kimliği
* **anahtar**: güvenlik karmasında işlem yapmak için kullanılan özel bir anahtar dizesi
* **SIG**: kendi hesaplanmış karmaınızla karşılaştırmak için kullanılacak bir hesaplanan güvenlik karması

## <a name="delegate-product-subscription"> </a>Ürün aboneliğine temsilci seçme
Ürün aboneliğine temsilci seçme, Kullanıcı oturum açma/kurma için de aynı şekilde çalışır. Son iş akışı aşağıdaki gibi olacaktır:

1. Geliştirici API Management Geliştirici Portalında bir ürün seçer ve abone ol düğmesine tıklamalar.
2. Tarayıcı, yetkilendirme uç noktasına yönlendirilir.
3. Yetkilendirme uç noktası gerekli ürün abonelik adımlarını gerçekleştirir. Adımları tasarlamak sizin için. Fatura bilgilerini istemek, ek sorular sormak veya yalnızca bilgileri depolamak ve herhangi bir kullanıcı eylemi gerektirmeksizin, başka bir sayfaya yönlendirmeyi de içerebilir.

İşlevselliği etkinleştirmek için, **temsilci seçme** sayfasında **ürün aboneliğine temsilci seç**' e tıklayın.

Ardından, yetkilendirme uç noktasının aşağıdaki eylemleri kullandığından emin olun:

1. Aşağıdaki biçimde bir istek alın:
   
   > *http:\//www.yourwebsite.com/apimdelegation? işlemi = {Operation} & ProductID = {abone ol} & UserID = {User yapma isteği} & anahtar = {String} & SIG = {String}*
   >
   
    Ürün abonelik durumu için sorgu parametreleri:
   
   * **işlem**: ne tür bir temsili isteğin olduğunu tanımlar. Ürün aboneliği istekleri için geçerli seçenekler şunlardır:
     * "Abone ol": kullanıcıyı sağlanan KIMLIĞE sahip belirli bir ürüne abone olma isteği (aşağıya bakın)
     * "Abonelikten çıkma": bir kullanıcının üründen aboneliğini kaldırma isteği
     * "Yenile": bir aboneliği yenileme isteği (örneğin, süresi dolacak)
   * **ProductID**: kullanıcının abone olması için ISTEDIĞI ürünün kimliği
   * **SubscriptionID**: *abonelik kaldırma* ve *yenileme* -ürün aboneliğinin kimliği
   * **UserID**: isteğin YAPıLDıĞı kullanıcının kimliği
   * **anahtar**: güvenlik karmasında işlem yapmak için kullanılan özel bir anahtar dizesi
   * **SIG**: kendi hesaplanmış karmaınızla karşılaştırmak için kullanılacak bir hesaplanan güvenlik karması

2. İsteğin Azure API Management geldiğini doğrulayın (isteğe bağlı, ancak güvenlik için önemle önerilir)
   
   * **ProductID**, **UserID**ve **anahtar** sorgu PARAMETRELERINE göre bir dizenin HMAC-SHA512 olur ' ı hesaplama:
     
     > HMAC (**anahtar** + ' \n ' + **ProductID** + ' \n ' + **Kullanıcı kimliği**)
     > 
     > 
   * Yukarıdaki hesaplanmış karmayı **SIG** sorgu parametresinin değeriyle karşılaştırın. İki karma eşleşiyorsa, sonraki adıma geçin, aksi takdirde isteği reddedin.
3. **İşlem** sırasında istenen işlem türüne göre ürün aboneliğini işleyin. Örneğin, faturalandırma, daha fazla soru vb.
4. Kullanıcı, sizin tarafınızdan ürüne başarıyla abone olurken, [abonelikler için REST API çağırma]kullanıcıyı API Management ürüne abone olun.

## <a name="delegate-example-code"></a> Örnek kod

Bu kod örnekleri şunları gösterir:

* Yayımcı portalının temsili ekranında ayarlanan *temsili doğrulama anahtarını*alın
* Daha sonra, geçirilen returnUrl 'nin geçerliliğini sağlamak için imzayı doğrulamak için kullanılan bir HMAC oluşturun.

Aynı kod, ProductID ve UserID için hafif değişiklikle birlikte da geçerlidir.

**C#returnUrl 'nin karmasını oluşturmak için kod**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**ReturnUrl 'nin karmasını oluşturmak için NodeJS kodu**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

## <a name="next-steps"></a>Sonraki adımlar
Temsili hakkında daha fazla bilgi için aşağıdaki videoya bakın:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[Çoklu oturum açma (SSO) belirteci isteme]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[Kullanıcı Oluştur]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[abonelikler için REST API çağırma]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[aşağıda belirtilen örnek kod]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
