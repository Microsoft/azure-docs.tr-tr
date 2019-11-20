---
title: Oturum yönetimi-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Threat Modeling Tool kullanıma sunulan tehditler için azaltmaları
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 5d9dc1595e3cc812ba060d958b6e981867500ae2
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161501"
---
# <a name="security-frame-session-management"></a>Güvenlik çerçevesi: oturum yönetimi
| Ürün/hizmet | Makale |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Azure AD kullanırken ADAL yöntemlerini kullanarak uygun oturumu kapatma uygulama](#logout-adal)</li></ul> |
| IoT cihazı | <ul><li>[Oluşturulan SaS belirteçleri için sonlu yaşam sürelerini kullanın](#finite-tokens)</li></ul> |
| **Azure belge DB** | <ul><li>[Oluşturulan kaynak belirteçleri için en düşük belirteç ömrünü kullanın](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[ADFS kullanırken WsFederation yöntemlerini kullanarak uygun oturumu kapatma uygulama](#wsfederation-logout)</li></ul> |
| **Kimlik sunucusu** | <ul><li>[Kimlik sunucusu kullanılırken uygun oturumu kapatma uygula](#proper-logout)</li></ul> |
| **Web uygulaması** | <ul><li>[HTTPS üzerinden kullanılabilen uygulamalar güvenli tanımlama bilgileri kullanmalıdır](#https-secure-cookies)</li><li>[Tüm HTTP tabanlı uygulamalar yalnızca tanımlama bilgisi tanımı için http belirtmelidir](#cookie-definition)</li><li>[ASP.NET Web sayfalarındaki siteler arası Istek forgery (CSRF) saldırılarına karşı azaltma](#csrf-asp)</li><li>[Etkin olmama ömrü için oturum ayarlama](#inactivity-lifetime)</li><li>[Uygulamadan doğru oturumu kapatma uygulama](#proper-app-logout)</li></ul> |
| **Web API** | <ul><li>[ASP.NET Web API 'Lerinde siteler arası Istek forgery (CSRF) saldırılarına karşı azaltma](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>Azure AD kullanırken ADAL yöntemlerini kullanarak uygun oturumu kapatma uygulama

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure AD | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | Uygulama Azure AD tarafından verilen erişim belirtecini kullanıyorsa, oturum kapatma olay işleyicisi şunu çağırmalıdır |

### <a name="example"></a>Örnek
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Örnek
Ayrıca Session. Abandon () yöntemini çağırarak kullanıcının oturumunu yok etmeniz gerekir. Aşağıdaki yöntem Kullanıcı oturumu kapatma 'nın güvenli uygulamasını gösterir:
```csharp
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a id="finite-tokens"></a>Oluşturulan SaS belirteçleri için sonlu yaşam sürelerini kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | IoT cihazı | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | Azure IoT Hub kimlik doğrulaması için oluşturulan SaS belirteçleri sonlu bir süre sonu süresine sahip olmalıdır. Belirteçlerin tehlikeye düşmesi durumunda yeniden oynatılabilecek süreyi sınırlamak için SaS belirteci yaşam sürelerini en düşük bir süreye tutun.|

## <a id="resource-tokens"></a>Oluşturulan kaynak belirteçleri için en düşük belirteç ömrünü kullanın

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Azure belge DB | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | Kaynak belirtecinin TimeSpan değerini gereken minimum değere küçültün. Kaynak belirteçleri varsayılan geçerli bir TimeSpan değeri olan 1 saattir.|

## <a id="wsfederation-logout"></a>ADFS kullanırken WsFederation yöntemlerini kullanarak uygun oturumu kapatma uygulama

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | ADFS | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | Uygulama ADFS tarafından verilen STS belirtecini kullanıyorsa, oturum kapatma olay işleyicisi kullanıcının oturumunu kapatmak için WSFederationAuthenticationModule. FederatedSignOut () metodunu çağırmalıdır. Ayrıca geçerli oturum yok edilmelidir ve oturum belirteci değeri sıfırlanmalı ve null olmalıdır.|

### <a name="example"></a>Örnek
```csharp
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a id="proper-logout"></a>Kimlik sunucusu kullanılırken uygun oturumu kapatma uygula

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Kimlik sunucusu | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [IdentityServer3-Federal oturum kapatma](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Adımlar** | IdentityServer, dış kimlik sağlayıcılarıyla federasyona ekleme yeteneğini destekler. Kullanıcı, bir yukarı akış kimlik sağlayıcısı oturumunu kapattığında, kullanılan protokole bağlı olarak, Kullanıcı oturumu kapattığında bir bildirim almak mümkün olabilir. Bu, IdentityServer 'ın istemcilerine bir kullanıcı tarafından imzalanabilmesi için bildirim almasına izin verir. Uygulama ayrıntıları için başvurular bölümündeki belgeleri denetleyin.|

## <a id="https-secure-cookies"></a>HTTPS üzerinden kullanılabilen uygulamalar güvenli tanımlama bilgileri kullanmalıdır

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | EnvironmentType-Onprea |
| **Başvur**              | [httpCookies öğesi (ASP.NET Settings şeması)](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie. Secure özelliği](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Adımlar** | Tanımlama bilgilerine normalde yalnızca kapsam oldukları etki alanı erişilebilir. Ne yazık ki, "etki alanı" tanımı protokol içermez, bu nedenle HTTPS üzerinden oluşturulan tanımlama bilgilerine HTTP üzerinden erişilebilir. "Secure" özniteliği tarayıcıya, tanımlama bilgisinin yalnızca HTTPS üzerinden kullanılabilir hale getirilmesinin gerektiğini gösterir. HTTPS üzerinden ayarlanmış tüm tanımlama bilgilerinin **güvenli** özniteliğini kullandığından emin olun. Bu gereksinim, requireSSL özniteliği true olarak ayarlanarak Web. config dosyasında zorlanabilir. Herhangi bir ek kod değişikliği yapmanıza gerek kalmadan tüm geçerli ve gelecekteki tanımlama bilgileri için **güvenli** özniteliği zorlayacağından tercih edilen yaklaşım tercih edilir.|

### <a name="example"></a>Örnek
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
Bu ayar, uygulamaya erişmek için HTTP kullanılmasına rağmen zorlanır. Uygulamaya erişmek için HTTP kullanılıyorsa, tanımlama bilgileri güvenli özniteliğiyle ayarlandığı ve tarayıcı bunları uygulamaya geri gönderemediği için ayar uygulamayı keser.

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Web Forms, MVC5 |
| **Özelliklerine**              | EnvironmentType-Onprea |
| **Başvur**              | Yok  |
| **Adımlar** | Web uygulaması bağlı olan taraf ve IDP ADFS sunucusu olduğunda Feeruth belirtecinin Secure özniteliği, Web. config ' in `system.identityModel.services` bölümünde requireSSL özelliği true olarak ayarlanarak yapılandırılabilir:|

### <a name="example"></a>Örnek
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a id="cookie-definition"></a>Tüm HTTP tabanlı uygulamalar yalnızca tanımlama bilgisi tanımı için http belirtmelidir

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [Güvenli tanımlama bilgisi özniteliği](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Adımlar** | Bir siteler arası betik oluşturma (XSS) saldırısında bilgilerin açığa çıkması riskini azaltmak için, tanımlama bilgilerine sunulan ve tüm büyük tarayıcılarda desteklenen yeni bir öznitelik-httpOnly-. Özniteliği, bir tanımlama bilgisinin betik aracılığıyla erişilebilir olmadığı belirtir. Yalnızca HttpOnly tanımlama bilgilerini kullanarak bir Web uygulaması, tanımlama bilgisinde yer alan hassas bilgilerin betik aracılığıyla çalınıp saldırganın Web sitesine gönderilmesi olasılığını azaltır. |

### <a name="example"></a>Örnek
Tanımlama bilgilerini kullanan tüm HTTP tabanlı uygulamalar, Web. config dosyasında aşağıdaki yapılandırmayı uygulayarak tanımlama bilgisi tanımında HttpOnly belirtmelidir:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Web Forms |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [FormsAuthentication. RequireSSL özelliği](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Adımlar** | RequireSSL özelliği değeri, yapılandırma öğesinin requireSSL özniteliği kullanılarak bir ASP.NET uygulamasının yapılandırma dosyasında ayarlanır. ASP.NET uygulamanız için Web. config dosyasında, SSL (Güvenli Yuva Katmanı) ' nin, requireSSL özniteliğini ayarlayarak, Forms-Authentication tanımlama bilgisini sunucuya döndürmesini isteyip istemediğinizi belirtebilirsiniz.|

### <a name="example"></a>Örnek 
Aşağıdaki kod örneği, Web. config dosyasındaki requireSSL özniteliğini ayarlar.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | MVC5 |
| **Özelliklerine**              | EnvironmentType-Onprea |
| **Başvur**              | [Windows Identity Foundation (WıF) yapılandırması – Bölüm II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Adımlar** | Feeruth tanımlama bilgileri için httpOnly özniteliğini ayarlamak için, hideFromCsript öznitelik değeri true olarak ayarlanmalıdır. |

### <a name="example"></a>Örnek
Aşağıdaki yapılandırma doğru yapılandırmayı gösterir:
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a id="csrf-asp"></a>ASP.NET Web sayfalarındaki siteler arası Istek forgery (CSRF) saldırılarına karşı azaltma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | Siteler arası istek güvenliği (CSRF veya XSRF), bir saldırganın bir Web sitesinde farklı bir kullanıcının kurulu oturumunun güvenlik bağlamında eylemler gerçekleştirebileceği bir saldırı türüdür. Hedef Web sitesi, alınan isteğin kimliğini doğrulamak için oturum tanımlama bilgilerini kullanıyorsa, içerik değişiklik veya silme amacını alır. Saldırgan, kullanıcının zaten oturum açtığı bir güvenlik açığı bulunan siteden bir komut içeren bir URL 'YI yüklemek için farklı bir kullanıcının tarayıcısına sahip olması ile bu güvenlik açığından yararlanabilir. Saldırgan, güvenlik açığı bulunan sunucudan bir kaynak yükleyen farklı bir Web sitesi barındırarak veya bir bağlantıyı tıklatmalarını sağlayan farklı bir Web sitesi barındırmakla ilgili birçok yol vardır. Sunucu istemciye ek bir belirteç gönderirse saldırı engellenebilir, istemcinin bu belirteci gelecekteki tüm isteklere eklemesi gerekir ve gelecekteki tüm isteklerin geçerli oturumla ilgili bir belirteç (örneğin, ASP.NET kullanarak) dahil edileceğini doğrular. Antiforger, Token veya ViewState. |

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | MVC5, MVC6 |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [ASP.NET MVC ve Web sayfalarında XSRF/CSRF engellemesi](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Adımlar** | Anti-CSRF ve ASP.NET MVC formları-görünümlerde `AntiForgeryToken` yardımcı yöntemini kullanın; forma bir `Html.AntiForgeryToken()` koyun, örneğin,|

### <a name="example"></a>Örnek
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Örnek
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Örnek
Aynı zamanda, HTML. Antiforgeritoken (), ziyaretçiye, yukarıda gösterilen rastgele gizli değer ile aynı değere sahip __Requestdoğrulamaları Icationtoken adlı bir tanımlama bilgisi verir. Ardından, gelen bir form gönderisini doğrulamak için, hedef eylem yöntemine [Validateantiforgeryıtoken] filtresini ekleyin. Örnek:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Bunu denetleyen yetkilendirme filtresi:
* Gelen istek, __Requestdoğrulamaları ıationtoken adlı bir tanımlama bilgisine sahip
* Gelen istekte __Requestdoğrulamaları ıationtoken adlı bir `Request.Form` girdisi var
* Bu tanımlama bilgisi ve `Request.Form` değerleri, tümünün iyi olduğu varsayıldığında, istek normal olarak ilerler. Aksi halde, "gerekli bir Anti-forgery belirteci sağlanmadı veya geçersizdi" iletisiyle bir yetkilendirme hatası oluştu. 

### <a name="example"></a>Örnek
Anti-CSRF ve AJAX: bir AJAX isteği HTML form verileri değil JSON verilerini gönderebileceğinden, form belirteci AJAX istekleri için bir sorun olabilir. Tek bir çözüm, belirteçleri özel bir HTTP üst bilgisinde göndermektir. Aşağıdaki kod belirteçleri oluşturmak için Razor söz dizimi kullanır ve ardından belirteçleri bir AJAX isteğine ekler. 
```csharp
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Örnek
İsteği tamamladığınızda, belirteçleri istek başlığından ayıklayın. Ardından, belirteçleri doğrulamak için AntiForgery. Validate metodunu çağırın. Belirteçler geçerli değilse Validate yöntemi bir özel durum oluşturur.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Web Forms |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [Web saldırılarına karşı ASP.NET yerleşik özelliklerinden yararlanın](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Adımlar** | WebForm tabanlı uygulamalarda CSRF saldırıları, ViewStateUserKey, her Kullanıcı-Kullanıcı KIMLIĞI veya daha iyi bir oturum KIMLIĞI için değişiklik gösteren rastgele bir dizeye ayarlanarak azaltılabilir. Bir dizi teknik ve sosyal nedenler için oturum kimliği, tahmin edilemeyen, zaman aşımına uğradığından ve Kullanıcı başına temelinde farklılık gösterdiği için çok daha iyidir.|

### <a name="example"></a>Örnek
Tüm sayfalarınızda olması gereken kod aşağıda verilmiştir:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>Etkin olmama ömrü için oturum ayarlama

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [HttpSessionState. Timeout özelliği](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Adımlar** | Oturum zaman aşımı, bir Kullanıcı bir Aralık (Web sunucusu tarafından tanımlanan) sırasında bir Web sitesi üzerinde herhangi bir işlem gerçekleştirmezse gerçekleşen olayı temsil eder. Sunucu tarafında olay, Kullanıcı oturumunun durumunu ' geçersiz ' olarak değiştirin (örneğin, "artık kullanılmıyor") ve Web sunucusuna bunu yok saymasını bildirin (içerdiği tüm verileri silme). Aşağıdaki kod örneği, Web. config dosyasında zaman aşımı oturum özniteliğini 15 dakikaya ayarlar.|

### <a name="example"></a>Örnek
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a id="threat-detection"></a>Azure SQL 'de tehdit algılamayı etkinleştirme

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Web Forms |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [Authentication için Forms öğesi (ASP.NET Settings şeması)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Adımlar** | Form kimlik doğrulama bileti tanımlama bilgisi zaman aşımını 15 dakikaya ayarlayın|

### <a name="example"></a>Örnek
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Web Forms, MVC5 |
| **Özelliklerine**              | EnvironmentType-Onprea |
| **Başvur**              | [Asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Adımlar** | Web uygulaması bağlı olan taraf ve ADFS ise, kimlik doğrulama tanımlama bilgilerinin yaşam süresi-Feeruth belirteçleri-Web. config dosyasında aşağıdaki yapılandırmayla ayarlanabilir:|

### <a name="example"></a>Örnek
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>Örnek
Ayrıca, ADFS sunucusunda aşağıdaki PowerShell komutu çalıştırılarak ADFS tarafından verilen SAML talep belirtecinin ömrü 15 dakikaya ayarlanmalıdır:
```csharp
Set-ADFSRelyingPartyTrust -TargetName "<RelyingPartyWebApp>" -ClaimsProviderName @("Active Directory") -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>Uygulamadan doğru oturumu kapatma uygulama

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | Kullanıcı oturumu Kapat düğmesine bastığında uygulamada doğru oturumu kapatma işlemini gerçekleştirin. Oturum kapatma sonrasında, uygulama kullanıcının oturumunu yok eder ve ayrıca oturum tanımlama bilgisi değerini sıfırlayıp null değer vererek kimlik doğrulama tanımlama bilgisi değerini sıfırlayıp null değeri yok eder. Ayrıca, birden çok oturum tek bir kullanıcı kimliğine bağlı olduğunda, zaman aşımı veya oturumu kapatma sırasında sunucu tarafında toplu olarak sonlandırılmalıdır. Son olarak, her sayfada oturum kapatma işlevinin kullanılabilir olduğundan emin olun. |

## <a id="csrf-api"></a>ASP.NET Web API 'Lerinde siteler arası Istek forgery (CSRF) saldırılarına karşı azaltma

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web API’si | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | Yorlar |
| **Özelliklerine**              | Yok  |
| **Başvur**              | Yok  |
| **Adımlar** | Siteler arası istek güvenliği (CSRF veya XSRF), bir saldırganın bir Web sitesinde farklı bir kullanıcının kurulu oturumunun güvenlik bağlamında eylemler gerçekleştirebileceği bir saldırı türüdür. Hedef Web sitesi, alınan isteğin kimliğini doğrulamak için oturum tanımlama bilgilerini kullanıyorsa, içerik değişiklik veya silme amacını alır. Saldırgan, kullanıcının zaten oturum açtığı bir güvenlik açığı bulunan siteden bir komut içeren bir URL 'YI yüklemek için farklı bir kullanıcının tarayıcısına sahip olması ile bu güvenlik açığından yararlanabilir. Saldırgan, güvenlik açığı bulunan sunucudan bir kaynak yükleyen farklı bir Web sitesi barındırarak veya bir bağlantıyı tıklatmalarını sağlayan farklı bir Web sitesi barındırmakla ilgili birçok yol vardır. Sunucu istemciye ek bir belirteç gönderirse saldırı engellenebilir, istemcinin bu belirteci gelecekteki tüm isteklere eklemesi gerekir ve gelecekteki tüm isteklerin geçerli oturumla ilgili bir belirteç (örneğin, ASP.NET kullanarak) dahil edileceğini doğrular. Antiforger, Token veya ViewState. |

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web API’si | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | MVC5, MVC6 |
| **Özelliklerine**              | Yok  |
| **Başvur**              | [ASP.NET Web API 'sindeki siteler arası Istek forgery (CSRF) saldırılarını önleme](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Adımlar** | Anti-CSRF ve AJAX: bir AJAX isteği HTML form verileri değil JSON verilerini gönderebileceğinden, form belirteci AJAX istekleri için bir sorun olabilir. Tek bir çözüm, belirteçleri özel bir HTTP üst bilgisinde göndermektir. Aşağıdaki kod belirteçleri oluşturmak için Razor söz dizimi kullanır ve ardından belirteçleri bir AJAX isteğine ekler. |

### <a name="example"></a>Örnek
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Örnek
İsteği tamamladığınızda, belirteçleri istek başlığından ayıklayın. Ardından, belirteçleri doğrulamak için AntiForgery. Validate metodunu çağırın. Belirteçler geçerli değilse Validate yöntemi bir özel durum oluşturur.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>Örnek
Anti-CSRF ve ASP.NET MVC formları-görünümlerde Antiforgeritoken yardımcı yöntemini kullanın; forma bir HTML. Antiforgeryıtoken () koyun, örneğin
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Örnek
Yukarıdaki örnekte aşağıdakine benzer bir durum çıktı verilmiştir:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Örnek
Aynı zamanda, HTML. Antiforgeritoken (), ziyaretçiye, yukarıda gösterilen rastgele gizli değer ile aynı değere sahip __Requestdoğrulamaları Icationtoken adlı bir tanımlama bilgisi verir. Ardından, gelen bir form gönderisini doğrulamak için, hedef eylem yöntemine [Validateantiforgeryıtoken] filtresini ekleyin. Örnek:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Bunu denetleyen yetkilendirme filtresi:
* Gelen istek, __Requestdoğrulamaları ıationtoken adlı bir tanımlama bilgisine sahip
* Gelen istekte __Requestdoğrulamaları ıationtoken adlı bir `Request.Form` girdisi var
* Bu tanımlama bilgisi ve `Request.Form` değerleri, tümünün iyi olduğu varsayıldığında, istek normal olarak ilerler. Aksi halde, "gerekli bir Anti-forgery belirteci sağlanmadı veya geçersizdi" iletisiyle bir yetkilendirme hatası oluştu.

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web API’si | 
| **SDL aşaması**               | Yapı |  
| **İlgili teknolojiler** | MVC5, MVC6 |
| **Özelliklerine**              | Kimlik sağlayıcısı-ADFS, kimlik sağlayıcısı-Azure AD |
| **Başvur**              | [ASP.NET Web API 2,2 ' de bireysel hesaplarla ve yerel oturum açmayla bir Web API 'sinin güvenliğini sağlama](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Adımlar** | Web API 'sinin OAuth 2,0 kullanılarak güvenliği varsa, yetkilendirme isteği üstbilgisinde bir taşıyıcı belirteci bekler ve yalnızca belirteç geçerliyse isteğe erişim izni verir. Tanımlama bilgisi tabanlı kimlik doğrulamasından farklı olarak, tarayıcılar taşıyıcı belirteçlerini isteklere iliştirmez. İstekte bulunan istemcinin, istek üstbilgisine doğrudan taşıyıcı belirtecini eklemesi gerekir. Bu nedenle, OAuth 2,0 kullanılarak korunan ASP.NET Web API 'Leri için, taşıyıcı belirteçleri CSRF saldırılarına karşı savunma olarak değerlendirilir. Uygulamanın MVC bölümü Forms kimlik doğrulaması (yani, tanımlama bilgileri kullanır) kullanıyorsa, güvenlik yumuşatma simgelerinin MVC web uygulaması tarafından kullanılması gerektiğini lütfen unutmayın. |

### <a name="example"></a>Örnek
Web API 'sinin tanımlama bilgilerinde değil yalnızca taşıyıcı belirteçlerine güvenilmesi için bilgilendirilmesi yeterlidir. Bu, `WebApiConfig.Register` yönteminde aşağıdaki yapılandırma tarafından yapılabilir:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

SuppressDefaultHostAuthentication yöntemi, Web API 'sine, istek Web API ardışık düzenine, IIS veya OWıN ara yazılım tarafından ulaşmadan önce gerçekleşen herhangi bir kimlik doğrulamasını yok saymasını söyler. Bu şekilde, Web API 'sini yalnızca taşıyıcı belirteçleri kullanarak kimlik doğrulaması yapacak şekilde kısıtlayabiliriz.
