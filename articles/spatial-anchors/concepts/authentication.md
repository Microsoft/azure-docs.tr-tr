---
title: Kimlik doğrulaması ve yetkilendirme
description: Bir uygulama veya hizmetin Azure uzamsal bağlayıcıların kimliğini doğrulayabileceği çeşitli yollar ve uzamsal Tutturucuların erişimini geçit halinde denetleyebilmeniz gereken denetim düzeylerini öğrenin.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 01065f9ac26599d26d6e2a6979eae1e559a82854
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97722972"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Azure uzamsal Tutturucuların kimlik doğrulaması ve yetkilendirmesi

Bu makalede, uygulamanızın veya Web hizmetinizin Azure uzamsal bağlayıcılarının kimliğini doğrulayabilmeniz için çeşitli yollar öğreneceksiniz. Ayrıca, uzamsal bağlayıcı hesaplarınıza erişimi denetlemek için Azure Active Directory (Azure AD) içinde Azure rol tabanlı erişim denetimi (Azure RBAC) kullanma yolları hakkında bilgi edineceksiniz.

## <a name="overview"></a>Genel Bakış

![Azure uzamsal Tutturucuların kimlik doğrulamasına genel bakış gösteren diyagram.](./media/spatial-anchors-authentication-overview.png)

Belirli bir Azure uzamsal çıpası hesabına erişmek için, istemcilerin öncelikle Azure Karma Gerçeklik güvenlik belirteci hizmeti 'nden (STS) bir erişim belirteci alması gerekir. STS 'den edinilen belirteçlerin ömrü 24 saattir. Bu kişiler, uzamsal bağlayıcı hizmetlerinin hesapta yetkilendirme kararları almak için kullandığı ve yalnızca yetkili sorumluların hesaba erişebildiğinden emin olmak için kullandıkları bilgileri içerirler.

Erişim belirteçleri, Azure AD tarafından verilen hesap anahtarları veya belirteçler için Exchange 'de elde edilebilir.

Hesap anahtarları, Azure uzamsal bağlayıcı hizmetini kullanmaya hızlıca başlamanıza olanak tanır. Ancak uygulamanızı üretime dağıtmadan önce Azure AD kimlik doğrulamasını kullanmak için uygulamanızı güncelleştirmeniz önerilir.

Azure AD kimlik doğrulama belirteçlerini iki şekilde edinebilirsiniz:

- Kurumsal bir uygulama oluşturuyorsanız ve şirketiniz kimlik sistemi olarak Azure AD kullanıyorsa, uygulamanızda Kullanıcı tabanlı Azure AD kimlik doğrulamasını kullanabilirsiniz. Daha sonra mevcut Azure AD güvenlik gruplarınızı kullanarak uzamsal bağlayıcı hesaplarınıza erişim izni verirsiniz. Ayrıca, kuruluşunuzdaki kullanıcılara doğrudan erişim izni verebilirsiniz.
- Aksi takdirde, Azure AD belirteçlerini uygulamanızı destekleyen bir Web hizmetinden edinmenizi öneririz. Bu yöntemi üretim uygulamaları için öneririz, çünkü istemci uygulamanızda Azure uzamsal bağlantılarına erişim için kimlik bilgilerini gömmenizi önleyebilirsiniz.

## <a name="account-keys"></a>Hesap anahtarları

Başlamak için en kolay yol, Azure uzamsal bağlayıcı hesabınıza erişim için hesap anahtarlarını kullanmaktır. Azure portal hesap anahtarlarınızı alabilirsiniz. Hesabınıza gidin ve **anahtarlar** sekmesini seçin:

![Vurgulanan birincil anahtar için Kopyala düğmesi ile anahtarlar sekmesini gösteren ekran görüntüsü.](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

İki anahtar mevcuttur. Her ikisi de uzamsal bağlayıcı hesabına erişim için aynı anda geçerlidir. Hesaba erişmek için kullandığınız anahtarı düzenli olarak güncelleştirmeniz önerilir. İki ayrı geçerli anahtarın olması, bu güncelleştirmelerin kesinti olmadan yapılmasını mümkün değildir. Birincil anahtarı ve ikincil anahtarı alternatif olarak güncelleştirmeniz yeterlidir.

SDK, hesap anahtarları aracılığıyla kimlik doğrulaması için yerleşik desteğe sahiptir. Yalnızca `AccountKey` nesneniz üzerinde özelliğini ayarlamanız gerekir `cloudSession` :

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Bu özelliği ayarladıktan sonra SDK, bir erişim belirtecinin hesap anahtarının değişimini ve uygulamanız için gerekli belirteçlerin önbelleğe alınmasını işler.

> [!WARNING]
> Yalnızca geliştirme/prototip yazma sırasında, hızlı ekleme için hesap anahtarlarını kullanmanızı öneririz. Uygulamanızı, içindeki eklenmiş bir hesap anahtarıyla üretime göndermek için önerilmez. Bunun yerine, daha sonra açıklanan Kullanıcı tabanlı veya hizmet tabanlı Azure AD kimlik doğrulaması yaklaşımlarını kullanın.

## <a name="azure-ad-user-authentication"></a>Azure AD Kullanıcı kimlik doğrulaması

Azure Active Directory kullanıcıları hedefleyen uygulamalar için, Kullanıcı için bir Azure AD belirteci kullanmanızı öneririz. Bu belirteci [msal](../../active-directory/develop/msal-overview.md)kullanarak elde edebilirsiniz. [Bir uygulamayı kaydetmeye yönelik hızlı başlangıç](../../active-directory/develop/quickstart-register-app.md)içindeki adımları izleyin ve şunları yapın:

**Azure portal**
1.    Uygulamanızı Azure AD 'ye yerel bir uygulama olarak kaydedin. Kayıt kapsamında, uygulamanızın çok kiracılı olup olmayacağını belirlemeniz gerekir. Ayrıca, uygulamanız için izin verilen yeniden yönlendirme URL 'Lerini sağlamanız gerekir.
1.  **API izinleri** sekmesine gidin.
2.  **Izin Ekle**' yi seçin.
    1.  **Kuruluşumun kullandığı API** 'Lerde **karma gerçeklik kaynak sağlayıcısını** seçin.
    2.  **Temsilci izinleri** seçin.
    3.  **Mixedreality** altında **mixedreality. SignIn** ' ı seçin.
    4.  **Izin Ekle**' yi seçin.
3.  **Yönetici Izni ver**' i seçin.

2. Uygulamanıza veya kullanıcılarınıza kaynağınızın erişimini verin:
   1.    Azure portal uzamsal bağlayıcılarınızın kaynağına gidin.
   2.    **Erişim denetimi (IAM)** sekmesine gidin.
   3.    **Rol ataması ekle**’yi seçin.
   1.    [Bir rol seçin](#azure-role-based-access-control).
   2.    **Seç** kutusunda, erişim atamak istediğiniz kullanıcıların, grupların ve/veya uygulamaların adlarını girin.
   3.    **Kaydet**’i seçin.

**Kodunuzda**
1.    MSAL ' de **ISTEMCI kimliği** ve **redirecturı** PARAMETRELERI için kendi Azure AD uygulamanızın uygulama KIMLIĞI ve yeniden yönlendirme URI 'sini kullandığınızdan emin olun.
2.    Kiracı bilgilerini ayarla:
        1.    Uygulamanız **yalnızca Kuruluşumu** destekliyorsa, bu DEĞERI **kiracı kimliğiniz** veya **kiracı adınızla** değiştirin. Örneğin, contoso.microsoft.com.
        2.    Uygulamanız **herhangi bir kuruluş dizinindeki hesapları** destekliyorsa, bu değeri **kuruluşlar** ile değiştirin.
        3.    Uygulamanız **tüm Microsoft hesabı kullanıcıları** destekliyorsa, bu değeri **ortak** ile değiştirin.
3.    Belirteç isteğiniz içinde, **kapsamı** **" `https://sts.<account-domain>//.default` "** olarak ayarlayın. burada, `<account-domain>` Azure uzamsal bağlayıcı hesabınızın **hesap etki alanı** ile değiştirilmiştir. Doğu ABD 2 hesabı etki alanındaki bir Azure uzamsal bağlayıcı hesabı için örnek kapsam **" `https://sts.mixedreality.azure.com//.default` "** dir. Bu kapsam, Azure AD 'ye, uygulamanızın karma gerçeklik güvenlik belirteci hizmeti (STS) için bir belirteç istediğini gösterir.

Bu adımları tamamladıktan sonra, uygulamanız MSAL bir Azure AD belirteci ile elde edebilmelidir. Bu Azure AD belirtecini, `authenticationToken` bulut oturumu yapılandırma nesneniz üzerinde olarak ayarlayabilirsiniz:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD hizmeti kimlik doğrulaması

Azure uzamsal çıpası kullanan uygulamaları üretime dağıtmak için, kimlik doğrulama isteklerini Broker 'lara sahip bir arka uç hizmeti kullanmanızı öneririz. İşleme genel bir bakış aşağıda verilmiştir:

![Azure uzamsal Tutturucuların kimlik doğrulamasına genel bakış sağlayan diyagram.](./media/spatial-anchors-aad-authentication.png)

Burada, uygulamanızın arka uç hizmetinde kimlik doğrulaması yapmak için kendi mekanizmasını kullandığı varsayılır. (Örneğin, bir Microsoft hesabı, PlayFab, Facebook, Google ID veya özel Kullanıcı adı ve parola.)  Kullanıcılarınızın arka uç hizmetinize kimlik doğrulamasından geçtikten sonra, bu hizmet bir Azure AD belirtecini alabilir, Azure uzamsal Tutturucuların erişim belirteci için Exchange 'e geri döndürebilir ve istemci uygulamanıza geri geri dönebilir.

Azure AD erişim belirteci [msal](../../active-directory/develop/msal-overview.md)aracılığıyla alınır. [Uygulamayı kaydetme hızlı](../../active-directory/develop/quickstart-register-app.md)başlangıcı bölümündeki adımları izleyerek şunları yapın:

**Azure portal**
1.    Uygulamanızı Azure AD 'ye kaydedin:
        1.    Azure portal **Azure Active Directory**' i seçin ve **uygulama kayıtları**' yı seçin.
        2.    **Yeni kayıt** seçeneğini belirleyin.
        3.    Uygulamanızın adını girin, uygulama türü olarak **Web uygulaması/API** ' yi seçin ve hizmetinizin kimlik doğrulama URL 'sini girin. **Oluştur**’u seçin.
2.    Uygulamada, **Ayarlar**' ı seçin ve ardından **Sertifikalar ve gizlilikler** sekmesini seçin. Yeni bir istemci parolası oluşturun, bir süre seçin ve ardından **Ekle**' yi seçin. Gizli anahtar değerini kaydettiğinizden emin olun. Web hizmetinizin koduna dahil etmeniz gerekir.
3.    Uygulamanıza ve/veya kullanıcılarınızın kaynağına erişmesine izin verin:
        1.    Azure portal uzamsal bağlayıcılarınızın kaynağına gidin.
        2.    **Erişim denetimi (IAM)** sekmesine gidin.
        3.    **Rol ataması ekle**’yi seçin.
        4.    [Bir rol seçin](#azure-role-based-access-control).
        5.    **Seç** kutusunda, erişim atamak istediğiniz uygulamaların adını veya adlarını girin. Uygulamanızın kullanıcılarının uzamsal bağlayıcı hesabına karşı farklı rollere sahip olmasını istiyorsanız, Azure AD 'de birden çok uygulamayı kaydedin ve her birine ayrı bir rol atayın. Ardından, kullanıcılarınız için doğru rolü kullanmak üzere yetkilendirme mantığınızı uygulayın.

              > [!NOTE]
              > **Rol ataması Ekle** bölmesinde, **erişim ata**' da **Azure AD Kullanıcı, Grup veya hizmet sorumlusu**' nı seçin.

        6.    **Kaydet**’i seçin.

**Kodunuzda**

>[!NOTE]
> [Uzamsal bağlayıcı örnek uygulamalarının](https://github.com/Azure/azure-spatial-anchors-samples)bir parçası olarak kullanılabilen [hizmet örneğini](https://github.com/Azure/azure-spatial-anchors-samples/tree/master/Sharing/SharingServiceSample) kullanabilirsiniz.

1.    MSAL ' de **ISTEMCI kimliği**, **gizli anahtar** ve **redirecturı** parametreleri olarak kendi Azure AD uygulamanızın uygulama kimliği, uygulama gizli anahtarı ve yeniden yönlendirme URI 'sini kullandığınızdan emin olun.
2.    Kiracı KIMLIĞINI MSAL ' deki **yetkili** parametresinde kendı Azure AD kiracı kimliğiniz olarak ayarlayın.
3.    Belirteç isteğiniz içinde, **kapsamı** **" `https://sts.<account-domain>//.default` "** olarak ayarlayın. burada, `<account-domain>` Azure uzamsal bağlayıcı hesabınızın **hesap etki alanı** ile değiştirilmiştir. Doğu ABD 2 hesabı etki alanındaki bir Azure uzamsal bağlayıcı hesabı için örnek kapsam **" `https://sts.mixedreality.azure.com//.default` "** dir.

Bu adımları tamamladıktan sonra, arka uç hizmetiniz bir Azure AD belirtecini alabilir. Daha sonra, istemciye geri dönecektir için bunu bir MR belirteci için değiş tokuş edebilir. Bir MR belirtecini almak için Azure AD belirtecinin kullanılması bir REST çağrısıyla yapılır. Örnek bir çağrı aşağıda verilmiştir:

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Yetkilendirme üst bilgisi şu şekilde biçimlendirilir: `Bearer <Azure_AD_token>`

Yanıt, MR belirtecini düz metin olarak içerir.

Daha sonra bu MR belirteci istemciye döndürülür. İstemci uygulamanız daha sonra bunu, bulut oturumu yapılandırmasında erişim belirteci olarak ayarlayabilir:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="azure-role-based-access-control"></a>Azure rol tabanlı erişim denetimi

Hizmetinizin uygulamalarına, hizmetlerine veya Azure AD kullanıcılarına verilen erişim düzeyini denetlemenize yardımcı olması için, bu önceden mevcut rolleri Azure uzamsal bağlayıcı hesaplarınıza göre gerektiği gibi atayabilirsiniz:

- **Uzamsal bağlayıcı hesap sahibi**. Bu role sahip uygulamalar veya kullanıcılar uzamsal bağlayıcı oluşturabilir, bunları sorgulayabilir ve silebilir. Hesap anahtarlarını kullanarak hesabınızda kimlik doğrulaması yaptığınızda, uzamsal bağlayıcı hesabı sahibi rolü kimliği doğrulanmış sorumluya atanır.
- **Uzamsal bağlayıcı hesabı katılımcısı**. Bu role sahip uygulamalar veya kullanıcılar kendileri için uzamsal bağlayıcı ve sorgu oluşturabilir, ancak bunları silemez.
- **Uzamsal bağlayıcı hesap okuyucu**. Bu role sahip uygulamalar veya kullanıcılar yalnızca uzamsal bağlantıları sorgulayabilir. Yenilerini oluşturamazlar, var olanları silemez veya meta verileri güncelleştiremez. Bu rol genellikle, bazı kullanıcıların ortamı seçmekte olduğu ancak diğerlerinin ortamda daha önce yer aldığı bağlantıları geri çekebileceği uygulamalar için kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

Azure uzamsal bağlayıcılarla ilk uygulamanızı oluşturun:

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
