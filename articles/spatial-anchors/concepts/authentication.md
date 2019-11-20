---
title: Azure uzamsal bağlantılarına yönelik kimlik doğrulama ve yetkilendirme | Microsoft Docs
description: Bir uygulama veya hizmetin Azure uzamsal bağlayıcıların kimliğini doğrulayabileceği çeşitli yollar ve Azure uzamsal Tutturucuların erişimini geçit halinde denetleyebilmeniz gereken denetim düzeylerini öğrenin.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 850748462f0273f2dfb1522d900ce9f1b2156d2a
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517061"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Azure uzamsal Tutturucuların kimlik doğrulaması ve yetkilendirmesi

Bu bölümde, uygulamanızın veya Web hizmetinizin Azure uzamsal bağlayıcılarının kimliğini doğrulamak için kullanabileceğiniz çeşitli yollar ve Azure Directory 'de (Azure AD) rol tabanlı Access Control kullanarak uzamsal Tutturucuların hesaplarına erişimi denetleme yolları ele alınacaktır.  

## <a name="overview"></a>Genel Bakış

![Azure uzamsal Tutturucuların kimlik doğrulamasına genel bakış](./media/spatial-anchors-authentication-overview.png)

Belirli bir Azure uzamsal çıpası hesabına erişmek için, istemcilerin öncelikle Azure Karma Gerçeklik güvenlik belirteci hizmeti 'nden (STS) bir erişim belirteci alması gerekir. STS 'den 24 saat içinde alınan belirteçler ve hesap üzerinde yetkilendirme kararları almak için uzamsal bağlayıcı hizmetleri bilgilerini içerir ve yalnızca yetkili sorumluların bu hesaba erişebildiğinden emin olun. 

Erişim belirteçleri, hesap anahtarlarından ya da Azure AD tarafından verilen belirteçlerden Exchange 'de elde edilebilir. 

Hesap anahtarları, Azure uzamsal bağlayıcı hizmetini kullanmaya hızlı bir şekilde başlamanızı sağlar; Ancak, uygulamanızı üretime dağıtmadan önce Azure AD tabanlı kimlik doğrulaması kullanmak için uygulamanızı güncelleştirmeniz önerilir. 

Azure AD kimlik doğrulaması belirteçleri iki şekilde elde edilebilir:

- Kurumsal bir uygulama oluşturuyorsanız ve şirketiniz kimlik sistemi olarak Azure AD kullanıyorsa, uygulamanızda Kullanıcı tabanlı Azure AD kimlik doğrulamasını kullanabilir ve mevcut Azure AD güvenlik gruplarınızı kullanarak uzamsal bağlayıcı hesaplarınıza erişim verebilirsiniz veya doğrudan kuruluşunuzdaki kullanıcılara. 
- Aksi takdirde, uygulamanızı destekleyen bir Web hizmetinden Azure AD belirteçlerini edinmeniz önerilir. Destekleyici Web hizmeti kullanmak, üretim uygulamaları için önerilen kimlik doğrulama yöntemidir. Bu, istemci uygulamanızda Azure uzamsal Tutturucuların kimlik bilgilerinin gömülmesini önler. 

## <a name="account-keys"></a>Hesap anahtarları

Azure uzamsal Çıpaları hesabınıza erişim için hesap anahtarlarının kullanılması, kullanmaya başlamanın en kolay yoludur. Hesap anahtarlarınızı Azure portal bulacaksınız. Hesabınıza gidin ve "anahtarlar" sekmesini seçin.

![Azure uzamsal Tutturucuların kimlik doğrulamasına genel bakış](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Her ikisi de uzamsal bağlayıcı hesabına erişim için aynı anda geçerli olan iki anahtar kullanılabilir hale getirilir. Hesaba erişmek için kullandığınız anahtarı düzenli olarak güncelleştirmeniz önerilir; iki ayrı geçerli anahtarın olması, bu tür güncelleştirmeleri kapalı kalma süresi olmadan etkinleştirir; yalnızca birincil anahtarı ve ikincil anahtarı güncelleştirmeniz gerekir. 

SDK, hesap anahtarları ile kimlik doğrulaması için yerleşik desteğe sahiptir; yalnızca cloudSession nesneniz üzerinde AccountKey özelliğini ayarlamanız gerekir. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrttabcppwinrt"></a>[C++Wınrt](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Bu işlem yapıldıktan sonra SDK, bir erişim belirtecinin hesap anahtarının değişimini ve uygulamanız için gerekli belirteçlerin önbelleğe alınmasını idare eder. 

> [!WARNING] 
> Hesap anahtarlarının kullanımı hızlı bir şekilde taslak için önerilir, ancak yalnızca geliştirme/prototip oluşturma sırasında önerilir. Uygulamanızı, içindeki bir ekli hesap anahtarını kullanarak üretime göndermemelidir ve bunun yerine, ileri listelenen kullanıcı tabanlı veya hizmet tabanlı Azure AD kimlik doğrulama yaklaşımlarını kullanın.

## <a name="azure-ad-user-authentication"></a>Azure AD Kullanıcı kimlik doğrulaması

Azure Active Directory kullanıcıları hedefleyen uygulamalar için önerilen yaklaşım, Kullanıcı için bir Azure AD belirteci kullanmaktır ve bu, aşağıdaki belgelerde açıklandığı gibi adal kitaplığını kullanarak elde edebilirsiniz: [https://docs.microsoft.com/azure/active-directory/develop/v1-overview](../../active-directory/develop/v1-overview.md); listelenen adımları izlemelisiniz "hızlı başlatır" altında şunları içerir:

1. Azure portal yapılandırma
    1.  Uygulamanızı Azure AD 'ye **yerel uygulama**olarak kaydedin. Kayıt kapsamında, uygulamanızın çok kiracılı olup olmayacağını belirlemeniz ve uygulamanız için izin verilen yeniden yönlendirme URL 'Lerini sağlamanız gerekir.  
    2.  Uygulamanıza veya kullanıcılarınıza kaynağınızın erişimini verin: 
        1.  Azure portal içindeki uzamsal bağlayıcılarınızın kaynağına gidin
        2.  **Erişim denetimi (IAM)** sekmesine geçiş yap
        3.  **Rol ataması Ekle** ' ye basın
            1.  [Rol seçin](#role-based-access-control)
            2.  **Seç** alanında, erişim atamak istediğiniz kullanıcı, Grup veya uygulama (lar) ı ve uygulamanın adını girin. 
            3.  **Kaydet**'e basın.
2. Kodunuzda:
    1.  ADAL 'da **ISTEMCI kimliği** ve **redirecturi** PARAMETRELERI olarak kendi Azure AD uygulamanızın **uygulama kimliği** ve **yeniden yönlendirme URI** 'sini kullandığınızdan emin olun
    2.  Kiracı bilgilerini ayarla:
        1.  Uygulamanız **yalnızca Kuruluşumu**destekliyorsa, bu DEĞERI **kiracı kimliğiniz** veya **kiracı adınızla** değiştirin (örneğin, contoso.Microsoft.com)
        2.  Uygulamanız **herhangi bir kuruluş dizinindeki hesapları**destekliyorsa, bu değeri **kuruluşlar** ile değiştirin
        3.  Uygulamanız **tüm Microsoft hesabı kullanıcıları**destekliyorsa, bu değeri **ortak** ile değiştirin
    3.  Belirteç isteğiniz üzerinde **kaynağı** "https://sts.mixedreality.azure.com" olarak ayarlayın. Bu "kaynak", Azure AD 'ye, uygulamanızın Azure uzamsal bağlayıcı hizmeti için bir belirteç istediğini gösterir.  

Bu şekilde, uygulamanız ADAL bir Azure AD belirteci ile elde edilebilir. Bu Azure AD belirtecini, bulut oturumu yapılandırma nesneniz üzerinde **Authenticationtoken** olarak ayarlayabilirsiniz. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++Wınrt](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD hizmeti kimlik doğrulaması

Azure uzamsal Bağlayıcılarından üretime yönelik uygulamaları dağıtmak için önerilen seçenek, kimlik doğrulama isteklerini Broker 'ın bulunduğu bir arka uç hizmetten faydalanır. Genel şema, bu diyagramda açıklandığı gibi olmalıdır:

![Azure uzamsal Tutturucuların kimlik doğrulamasına genel bakış](./media/spatial-anchors-aad-authentication.png)

Burada, uygulamanızın kendi mekanizmasını kullandığı varsayılır (örneğin: Microsoft hesabı, PlayFab, Facebook, Google ID, Özel Kullanıcı adı/parola vb.) arka uç hizmetinde kimlik doğrulaması yapmak için. Kullanıcılarınızın arka uç hizmetinize kimlik doğrulaması yapıldıktan sonra, bu hizmet bir Azure AD belirtecini alabilir, Azure uzamsal Tutturucuların erişim belirteci için Exchange 'e geri dönebilir ve bunu istemci uygulamanıza geri döndürebilir.

Azure AD erişim belirteci, adal kitaplığı kullanılarak aşağıdaki belgelerde açıklandığı şekilde alınır: [https://docs.microsoft.com/azure/active-directory/develop/v1-overview](../../active-directory/develop/v1-overview.md); "hızlı başlatır" altında listelenen adımları izlemeniz gerekir, ancak şunları içerir:

1.  Azure portal yapılandırma:
    1.  Uygulamanızı Azure AD 'ye kaydedin:
        1.  Azure portal ' de **Azure Active Directory**' a gidin ve **uygulama kayıtları** ' nı seçin.
        2.  **Yeni uygulama kaydı** seçin
        3.  Uygulamanızın adını girin, uygulama türü olarak **Web uygulaması/API** ' yi seçin ve hizmetinizin kimlik doğrulama URL 'sini girin. Sonra **Oluştur**' a basın.
        4.  Bu uygulamada, **Ayarlar**' a, sonra da **anahtarlar** sekmesini seçin. Anahtarınızın adını girin, bir süre seçin ve **Kaydet**' i tıklayın. Web hizmetinizin koduna dahil etmeniz gerekeceğinden, bu sırada görüntülenen anahtar değerini kaydettiğinizden emin olun.
    2.  Uygulamanıza ve/veya kullanıcılarınızın kaynağına erişmesine izin verin:
        1.  Azure portal içindeki uzamsal bağlayıcılarınızın kaynağına gidin
        2.  **Erişim denetimi (IAM)** sekmesine geçiş yap
        3.  **Rol ataması Ekle** ' ye basın
        1.  [Rol seçin](#role-based-access-control)
        2.  **Seç** alanına, oluşturduğunuz ve erişim atamak istediğiniz uygulama (lar) ın adını girin. Uygulamanızın kullanıcılarının uzamsal bağlayıcı hesabına karşı farklı rollere sahip olmasını istiyorsanız, Azure AD 'de birden çok uygulamayı kaydetmeniz ve ayrı bir role atamanız gerekir. Ardından, kullanıcılarınız için doğru rolü kullanmak üzere yetkilendirme mantığınızı uygulayın.  
    3.  **Kaydet**'e basın.
2.  Kodunuzda (Note: GitHub ' da bulunan hizmet örneğini kullanabilirsiniz):
    1.  ADAL 'da istemci KIMLIĞI, gizli anahtar ve Redirecturı parametreleri olarak kendi Azure AD uygulamanızın uygulama KIMLIĞI, uygulama gizli anahtarı ve yeniden yönlendirme URI 'sini kullandığınızdan emin olun
    2.  Kiracı KIMLIĞINI, ADAL 'daki yetkili parametresindeki kendi AAAzure ADD kiracı KIMLIĞINIZ olarak ayarlayın
    3.  Belirteç isteğiniz üzerinde **kaynağı** "https://sts.mixedreality.azure.com" olarak ayarlayın 

Bu şekilde, arka uç hizmetiniz bir Azure AD belirteci alabilir. Daha sonra, istemciye geri dönecektir için bunu bir MR belirteci için değiş tokuş edebilir. Bir MR belirtecini almak için Azure AD belirtecinin kullanılması bir REST çağrısıyla yapılır. Örnek bir çağrı aşağıda verilmiştir:

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Yetkilendirme üstbilgisinin şu şekilde biçimlendirildiği konum:`Bearer <accoundId>:<accountKey>`

Ve yanıt, MR belirtecini düz metin olarak içerir.
 
Daha sonra bu MR belirteci istemciye döndürülür. İstemci uygulamanız daha sonra bunu, bulut oturumu yapılandırmasında erişim belirteci olarak ayarlayabilir.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objctabobjc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swifttabswift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndktabcpp"></a>[C++NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrttabcppwinrt"></a>[C++Wınrt](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Rol tabanlı erişim denetimi

Hizmetinizin uygulamalara, hizmetlerine veya Azure AD kullanıcılarına verilen erişim düzeyini denetlemeye yardımcı olmak için, Azure uzamsal bağlayıcı hesaplarınıza göre gereken şekilde atamanız için aşağıdaki roller oluşturulmuştur:

- **Uzamsal Tutturucuların hesap sahibi**: Bu role sahip uygulamalar veya kullanıcılar uzamsal bağlayıcı oluşturabilir, bunları sorgulayabilir ve silebilir. Hesap anahtarlarını kullanarak hesabınızda kimlik doğrulaması yaptığınızda, **uzamsal bağlayıcı hesabı sahibi** rolü kimliği doğrulanmış sorumluya atanır. 
- **Uzamsal Tutturucuların hesabı katılımcısı**: Bu role sahip uygulamalar veya kullanıcılar uzamsal bağlayıcı oluşturabilir, bunları sorgulayabilir, ancak bunları silemez. 
- **Uzamsal Tutturucuların hesap okuyucusu**: Bu role sahip uygulamalar veya kullanıcılar yalnızca uzamsal bağlayıcıları sorgulayabilir, ancak yenilerini oluşturamaz, varolanları silebilir veya uzamsal bağlayıcılarında meta verileri güncelleştiremez. Bu genellikle bazı kullanıcıların ortamı seçtirilen uygulamalar için kullanılır, diğerleri ise yalnızca daha önce bu ortama yerleştirilmiş olan bağlantıları geri çekebilirler.

## <a name="next-steps"></a>Sonraki adımlar

Azure uzamsal bağlayıcılarla ilk uygulamanızı oluşturun.

> [!div class="nextstepaction"]
> ['Yi](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
