---
title: Kullanıcı akışlarına API bağlayıcıları ekleme (Önizleme)
description: Bir Kullanıcı akışında kullanılacak bir API bağlayıcısını yapılandırın.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 03/24/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: 86e9b13ce56e1924b0e24a7f4971da18620617de
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043640"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow-preview"></a>Kaydolma Kullanıcı akışına API Bağlayıcısı ekleme (Önizleme)

> [!IMPORTANT]
> Kaydolma için API bağlayıcıları, Azure AD B2C genel önizleme özelliğidir. Önizlemeler hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bir [API bağlayıcısını](api-connectors-overview.md)kullanmak IÇIN önce API bağlayıcısını oluşturun ve ardından Kullanıcı akışında etkinleştirin.

## <a name="create-an-api-connector"></a>API Bağlayıcısı oluşturma

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. **Azure hizmetleri** altında **Azure AD B2C**' yi seçin.
4. **API bağlayıcıları (Önizleme)** öğesini seçin ve ardından **yeni API Bağlayıcısı**' nı seçin.

   ![Yeni API Bağlayıcısı ekleme](./media/add-api-connector/api-connector-new.png)

5. Çağrı için bir görünen ad belirtin. Örneğin, **Kullanıcı bilgilerini doğrulayın**.
6. API çağrısının **uç nokta URL 'sini** sağlayın.
7. **Kimlik doğrulama türünü** SEÇIN ve API 'nizi çağırmak için kimlik doğrulama bilgilerini yapılandırın. API 'nizin güvenliğini sağlama seçenekleri için aşağıdaki bölüme bakın.

    ![API bağlayıcısını yapılandırma](./media/add-api-connector/api-connector-config.png)

8. **Kaydet**’i seçin.

## <a name="securing-the-api-endpoint"></a>API uç noktası güvenliğini sağlama
API uç noktanızı, HTTP temel kimlik doğrulaması veya HTTPS istemci sertifikası kimlik doğrulaması (Önizleme) kullanarak koruyabilirsiniz. Her iki durumda da, API uç noktanızı çağırırken Azure AD B2C kullanacağı kimlik bilgilerini sağlarsınız. API uç noktanız daha sonra kimlik bilgilerini denetler ve yetkilendirme kararları gerçekleştirir.

### <a name="http-basic-authentication"></a>HTTP temel kimlik doğrulaması
HTTP temel kimlik doğrulaması, [RFC 2617](https://tools.ietf.org/html/rfc2617)' de tanımlanmıştır. Azure AD B2C, üst bilgiyle istemci kimlik bilgileri (ve) ile bir HTTP isteği gönderir `username` `password` `Authorization` . Kimlik bilgileri Base64 kodlamalı dize olarak biçimlendirilir `username:password` . Daha sonra API 'niz, bir API çağrısının engellenip engellenmeyeceğini tespit etmek için bu değerleri denetler.

### <a name="https-client-certificate-authentication-preview"></a>HTTPS istemci sertifikası kimlik doğrulaması (Önizleme)

> [!IMPORTANT]
> Bu işlevsellik önizlemededir ve hizmet düzeyi anlaşmadan sağlanır. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

İstemci sertifikası kimlik doğrulaması, istemcinin kimliğini kanıtlamak için sunucuya bir istemci sertifikası sağladığı karşılıklı sertifika tabanlı bir kimlik doğrulama yöntemidir. Bu durumda Azure AD B2C, API Bağlayıcısı yapılandırmasının bir parçası olarak karşıya yüklediğiniz sertifikayı kullanır. Bu, SSL el sıkışmasının bir parçası olarak gerçekleşir. Daha sonra API hizmetiniz, erişimi yalnızca uygun sertifikalara sahip olan hizmetlere sınırlayabilir. İstemci sertifikası bir PKCS12 (PFX) X. 509.440 dijital sertifikasıdır. Üretim ortamlarında, bir sertifika yetkilisi tarafından imzalanması gerekir. 

Bir sertifika oluşturmak için, imzalanmış sertifikalara yönelik sertifika veren sağlayıcılarıyla otomatik olarak imzalanan sertifikalara ve tümleştirmelere yönelik seçeneklere sahip [Azure Key Vault](../key-vault/certificates/create-certificate.md)kullanabilirsiniz. Önerilen ayarlar şunlardır:
- **Konu**: `CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **Içerik türü**: `PKCS #12`
- **Yaşam süresi Acton türü**: `Email all contacts at a given percentage lifetime` veya `Email all contacts a given number of days before expiry`
- **Anahtar türü**: `RSA`
- **Anahtar boyutu**: `2048`
- **Dışarı aktarılabilir özel anahtar**: `Yes` (PFX dosyasını verebilmek için)

Daha sonra [sertifikayı dışarı aktarabilirsiniz](../key-vault/certificates/how-to-export-certificate.md). Alternatif olarak, otomatik olarak imzalanan bir sertifika oluşturmak için PowerShell 'in [New-SelfSignedCertificate cmdlet 'ini](../active-directory-b2c/secure-rest-api.md#prepare-a-self-signed-certificate-optional) kullanabilirsiniz.

Bir sertifikanız olduktan sonra, bunu API Bağlayıcısı yapılandırmasının bir parçası olarak yükleyebilirsiniz. Parolanın yalnızca bir parola ile korunan sertifika dosyaları için gerekli olduğunu unutmayın.

API 'nizin, API uç noktalarını korumak için gönderilen istemci sertifikalarına göre yetkilendirmeyi uygulaması gerekir. Azure App Service ve Azure Işlevleri için bkz. *API kodınızdan sertifikayı* etkinleştirme ve doğrulama hakkında bilgi edinmek için bkz. [TLS karşılıklı kimlik doğrulamasını yapılandırma](../app-service/app-service-web-configure-tls-mutual-auth.md) .  Ayrıca, ilke ifadelerini kullanarak istenen değerlere karşı [istemci sertifikası özelliklerini denetlemek](
../api-management/api-management-howto-mutual-certificates-for-clients.md)  için Azure API Management de kullanabilirsiniz.

Sertifikanızın kullanım süreleri dolduğunda anımsatıcı uyarılarını ayarlamanız önerilir. Yeni bir sertifika oluşturmanız ve yukarıdaki adımları yinelemeniz gerekecektir. Yeni sertifika dağıtıldığında API hizmetiniz, eski ve yeni sertifikaları kabul etmeye geçici olarak devam edebilir. Var olan bir API bağlayıcısına yeni bir sertifika yüklemek için **API bağlayıcıları** altında API bağlayıcısını seçin ve **yeni sertifikayı karşıya yükle**' ye tıklayın. Süresi dolmayan ve geçmiş en son karşıya yüklenen sertifika, Azure Active Directory tarafından otomatik olarak kullanılacak.

### <a name="api-key"></a>API Anahtarı
Bazı hizmetler, geliştirme sırasında HTTP uç noktalarınıza erişimi belirsizetmek için bir "API anahtarı" mekanizması kullanır. [Azure işlevleri](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)için, bunu `code` **uç nokta URL 'sine** sorgu parametresi olarak ekleyerek yapabilirsiniz. Örneğin, `https://contoso.azurewebsites.net/api/endpoint` <b>`?code=0123456789`</b> ). 

Bu, yalnızca üretimde kullanılması gereken bir mekanizma değildir. Bu nedenle, temel veya sertifika kimlik doğrulaması için yapılandırma her zaman gereklidir. Geliştirme amacıyla herhangi bir kimlik doğrulama yöntemi uygulamak istemiyorsanız (önerilmez), temel kimlik doğrulaması ' nı seçebilir ve için geçici değerleri kullanabilir `username` ve API `password` 'nizin YETKILENDIRMEYI uygularken API 'niz göz ardı edilebilir.

## <a name="the-request-sent-to-your-api"></a>API 'nize gönderilen istek
Bir API Bağlayıcısı, bir JSON gövdesinde anahtar-değer çiftleri olarak Kullanıcı öznitelikleri (' talepler ') gönderen bir **http post** isteği olarak yürütülür. Öznitelikler, [Microsoft Graph](/graph/api/resources/user#properties) Kullanıcı özelliklerine benzer şekilde serileştirilir. 

**Örnek istek**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

İstekte yalnızca Kullanıcı özellikleri ve **Azure AD B2C**  >  **Kullanıcı öznitelikleri** deneyiminde listelenen özel öznitelikler kullanılabilir.

Özel öznitelikler dizindeki **extension_ \<extensions-app-id> _CustomAttribute**  biçiminde bulunur. API 'nizin bu aynı serileştirilmiş biçimde talepler alması beklenir. Özel öznitelikler hakkında daha fazla bilgi için, bkz. [Azure AD B2C özel öznitelikleri tanımlama](user-flow-custom-attributes.md).

Ayrıca, **Kullanıcı arabirimi yerel ayarları (' ui_locales ')** talebi tüm isteklerde varsayılan olarak gönderilir. Bu, bir kullanıcının kendi cihazında yapılandırılan, uluslararası yanıtları döndürmek için API tarafından kullanılabilecek olan yerel ayarları sağlar.

> [!IMPORTANT]
> Bir talebin API uç noktası çağrıldığında bir değeri yoksa, talep API 'ye gönderilmez. API 'niz istek içinde olmayan bir talebi açıkça denetlemek ve işlemek için tasarlanmalıdır.

> [!TIP] 
> [**kimlikler (' kimlikler ')**](/graph/api/resources/objectidentity) ve **e-posta adresi (' e-posta ')** talepleri, kiracınızda bir hesabı olmadan önce BIR kullanıcıyı tanımlamak için API 'niz tarafından kullanılabilir. 

## <a name="enable-the-api-connector-in-a-user-flow"></a>Kullanıcı akışında API bağlayıcısını etkinleştirme

Kaydolma Kullanıcı akışına bir API Bağlayıcısı eklemek için bu adımları izleyin.

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. **Azure hizmetleri** altında **Azure AD B2C**' yi seçin.
4. **Kullanıcı akışları**' nı seçin ve ardından API bağlayıcısını eklemek istediğiniz kullanıcı akışını seçin.
5. **API bağlayıcıları**' nı seçin ve ardından Kullanıcı akışında aşağıdaki adımlarda ÇAĞıRMAK istediğiniz API uç noktalarını seçin:

   - **Bir kimlik sağlayıcısıyla oturum açtıktan sonra**
   - **Kullanıcı oluşturmadan önce**

   ![Kullanıcı akışına API ekleme](./media/add-api-connector/api-connectors-user-flow-select.png)

6. **Kaydet**’i seçin.

## <a name="after-signing-in-with-an-identity-provider"></a>Bir kimlik sağlayıcısıyla oturum açtıktan sonra

Kaydolma işleminde bu adımdaki bir API Bağlayıcısı, Kullanıcı kimlik sağlayıcısıyla kimlik doğrulamasından sonra (Google, Facebook, & Azure AD) hemen çağrılır. Bu adım, kullanıcı özniteliklerinin toplanması için kullanıcıya sunulan form olan ***öznitelik koleksiyonu sayfasından*** önce gelir. Bir kullanıcı yerel hesapla kayıt alıyorsa bu adım çağrılmaz.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Bu adımda API 'ye gönderilen örnek isteği
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

API 'ye gönderilen tam talepler, kimlik sağlayıcısı tarafından hangi bilgilerin sağlandığını bağlıdır. ' e-posta ' her zaman gönderilir.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Bu adımda Web API 'sinden beklenen yanıt türleri

Web API 'SI, bir Kullanıcı akışı sırasında Azure AD 'den bir HTTP isteği aldığında, bu yanıtları döndürebilir:

- Devamlılık yanıtı
- Engelleme yanıtı

#### <a name="continuation-response"></a>Devamlılık yanıtı

Devamlılık yanıtı, Kullanıcı akışının sonraki adıma devam etmesi gerektiğini belirtir: öznitelik koleksiyonu sayfası.

Devamlılık yanıtında, API talepleri döndürebilir. API tarafından bir talep döndürülürse talep şunları yapar:

- Öznitelik koleksiyonu sayfasındaki giriş alanını önceden doldurur.

[Devamlılık yanıtı](#example-of-a-continuation-response)örneğine bakın.

#### <a name="blocking-response"></a>Engelleme yanıtı

Engelleme yanıtı Kullanıcı akışından çıkar. Kullanıcıya bir engelleme sayfası görüntüleyerek Kullanıcı akışının devamlılığını durdurmak için özellikle API tarafından verilebilir. Engelleme sayfası `userMessage` API tarafından sunulan ' i görüntüler.

[Engelleme yanıtı](#example-of-a-blocking-response)örneğine bakın.

## <a name="before-creating-the-user"></a>Kullanıcı oluşturmadan önce

Kaydolma işleminde bu adımda bulunan bir API Bağlayıcısı, varsa öznitelik toplama sayfasından sonra çağrılır. Bu adım, Kullanıcı hesabı oluşturulmadan önce her zaman çağrılır.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Bu adımda API 'ye gönderilen örnek isteği

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
API 'ye gönderilen tam talepler, kullanıcıdan hangi bilgilerin toplandığına veya kimlik sağlayıcısı tarafından sağlandığına bağlıdır.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Bu adımda Web API 'sinden beklenen yanıt türleri

Web API 'SI, bir Kullanıcı akışı sırasında Azure AD 'den bir HTTP isteği aldığında, bu yanıtları döndürebilir:

- Devamlılık yanıtı
- Engelleme yanıtı
- Doğrulama yanıtı

#### <a name="continuation-response"></a>Devamlılık yanıtı

Devamlılık yanıtı, Kullanıcı akışının bir sonraki adıma devam etmesi gerektiğini gösterir: kullanıcıyı dizinde oluşturun.

Devamlılık yanıtında, API talepleri döndürebilir. API tarafından bir talep döndürülürse talep şunları yapar:

- Öznitelik koleksiyonu sayfasından talebe zaten atanmış olan tüm değerleri geçersiz kılar.

[Devamlılık yanıtı](#example-of-a-continuation-response)örneğine bakın.

#### <a name="blocking-response"></a>Engelleme yanıtı
Engelleme yanıtı Kullanıcı akışından çıkar. Kullanıcıya bir engelleme sayfası görüntüleyerek Kullanıcı akışının devamlılığını durdurmak için özellikle API tarafından verilebilir. Engelleme sayfası `userMessage` API tarafından sunulan ' i görüntüler.

[Engelleme yanıtı](#example-of-a-blocking-response)örneğine bakın.

### <a name="validation-error-response"></a>Doğrulama-hata yanıtı
 API bir doğrulama hata yanıtıyla yanıt verdiğinde, Kullanıcı akışı öznitelik toplama sayfasında kalır ve `userMessage` kullanıcıya görüntülenir. Kullanıcı daha sonra formu düzenleyip yeniden gönderebilir. Bu tür bir yanıt, giriş doğrulaması için kullanılabilir.

[Doğrulama hata yanıtı](#example-of-a-validation-error-response)örneğine bakın.

## <a name="example-responses"></a>Örnek yanıtlar

### <a name="example-of-a-continuation-response"></a>Devamlılık yanıtı örneği

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parametre                                          | Tür              | Gerekli | Açıklama                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| eylem                                             | Dize            | Yes      | Değer olmalıdır `Continue` .                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | No       | Döndürülen değerler, bir kullanıcıdan toplanan değerlerin üzerine yazabilir. Ayrıca, bir **uygulama talebi** olarak seçilirse belirtece de döndürülebilir.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | Talebin içermesi gerekmez `_<extensions-app-id>_` . Döndürülen değerler, bir kullanıcıdan toplanan değerlerin üzerine yazabilir. Ayrıca, bir **uygulama talebi** olarak seçilirse belirtece de döndürülebilir.  |

### <a name="example-of-a-blocking-response"></a>Engelleme yanıtı örneği

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| Parametre   | Tür   | Gerekli | Açıklama                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| sürüm     | Dize | Yes      | API sürümü.                                                    |
| eylem      | Dize | Yes      | Değer olmalıdır `ShowBlockPage`                                              |
| userMessage | Dize | Yes      | Kullanıcıya görüntülenecek ileti.                                            |

**Engelleyici bir Yanıt ile son kullanıcı deneyimi**

![Örnek engelleme sayfası](./media/add-api-connector/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Doğrulama hatası yanıt örneği

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code."
}
```

| Parametre   | Tür    | Gerekli | Açıklama                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| sürüm     | Dize  | Yes      | API 'nizin sürümü.                                                    |
| eylem      | Dize  | Yes      | Değer olmalıdır `ValidationError` .                                           |
| durum      | Tamsayı/dize | Yes      | Değer olmalı `400` veya `"400"` bir ValidationError yanıtı için.  |
| userMessage | Dize  | Yes      | Kullanıcıya görüntülenecek ileti.                                            |

> [!NOTE]
> Yanıt gövdesinde "durum" değerine ek olarak HTTP durum kodu "400" olmalıdır.

**Doğrulama hatası yanıtıyla Son Kullanıcı deneyimi**

![Örnek doğrulama sayfası](./media/add-api-connector/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>En iyi uygulamalar ve sorun giderme

### <a name="using-serverless-cloud-functions"></a>Sunucusuz bulut işlevlerini kullanma
Azure Işlevlerinde HTTP Tetikleyicileri gibi sunucusuz işlevler, API Bağlayıcısı ile kullanmak üzere API uç noktaları oluşturma basit bir yol sağlar. [Örneğin](code-samples.md#api-connectors), doğrulama mantığını gerçekleştirmek ve belirli e-posta etki alanları için oturum açma işlemleri kısıtlamak gibi sunucusuz bulut işlevini kullanabilirsiniz. Sunucusuz bulut işlevi ayrıca daha karmaşık senaryolar için diğer Web API 'Lerini, Kullanıcı depolarını ve diğer bulut hizmetlerini çağırıp çağırabilir.

### <a name="best-practices"></a>En iyi uygulamalar
Aşağıdakileri doğrulayın:
* API 'niz, yukarıda özetlenen API isteği ve yanıt sözleşmelerini takip eden bir. 
* API bağlayıcısının **uç nokta URL 'si** doğru API uç noktasını işaret eder.
* API 'niz alınan taleplerin null değerlerini açıkça denetler.
* Akıcı bir kullanıcı deneyimi sağlamak için API 'niz mümkün olduğunca çabuk yanıt verir.
    * Sunucusuz bir işlev veya ölçeklenebilir Web hizmeti kullanıyorsanız, API 'YI "uyanık" veya "normal" olarak tutan bir barındırma planı kullanın. Üretimde. Azure Işlevleri için [Premium planı](../azure-functions/functions-scale.md) kullanmanız önerilir
 

### <a name="use-logging"></a>Günlüğe kaydetmeyi kullanma
Genel olarak, API 'nizi beklenmedik hata kodları, özel durumlar ve düşük performans için izlemek üzere [Application Insights](../azure-functions/functions-monitoring.md)gıbı Web API hizmetiniz tarafından etkinleştirilen günlük araçlarını kullanmak yararlı olacaktır.
* HTTP 200 veya 400 olmayan HTTP durum kodlarını izleyin.
* 401 veya 403 HTTP durum kodu genellikle kimlik doğrulamalarınız ile ilgili bir sorun olduğunu gösterir. API 'nin kimlik doğrulama katmanını ve API Bağlayıcısı 'nda karşılık gelen yapılandırmayı iki kez kontrol edin.
* Gerekirse geliştirmede daha Agresif günlük düzeyi (örn. "Trace" veya "Debug") kullanın.
* API 'nizi uzun yanıt süreleri için izleyin.

## <a name="next-steps"></a>Sonraki adımlar
- [Örneklerimizi](code-samples.md#api-connectors)kullanmaya başlayın.
