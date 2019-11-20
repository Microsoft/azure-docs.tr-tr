---
title: Azure AD 'de imzalama anahtarı geçişi
description: Bu makalede Azure Active Directory için imzalama anahtarı aktarma en iyi yöntemleri açıklanmaktadır
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
ms.date: 10/20/2018
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f20a10f7c6f98b352e8a2d794fabc3b6b3b57319
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835301"
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Azure Active Directory 'da anahtar geçişi imzalanıyor
Bu makalede, güvenlik belirteçlerini imzalamak için Azure Active Directory (Azure AD) ' de kullanılan ortak anahtarlar hakkında bilmeniz gerekenler açıklanmaktadır. Bu anahtarların düzenli olarak bir süre içinde devredildiğini ve acil bir durumda bir acil durum için hemen geri alınabilir olduğunu unutmayın. Azure AD kullanan tüm uygulamalar, anahtar geçişi işlemini programlı bir şekilde işleyebilmelidir veya düzenli bir el ile geçiş işlemi oluşturabilir. Anahtarların nasıl çalıştığını, uygulamanıza yapılan geçişin etkisini nasıl değerlendirireceğini ve gerekirse anahtar rollover 'ı işlemek için düzenli el ile geçiş süreci oluşturmayı öğrenmek için okumaya devam edin.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Azure AD 'de imzalama anahtarlarına genel bakış
Azure AD, kendisini kullanan uygulamalar arasında güven sağlamak için endüstri standartlarına göre oluşturulmuş ortak anahtar şifrelemeyi kullanır. Pratik koşullarda, bu, aşağıdaki şekilde işe yarar: Azure AD, ortak ve özel anahtar çiftinden oluşan bir imzalama anahtarı kullanır. Bir kullanıcı kimlik doğrulaması için Azure AD kullanan bir uygulamada oturum açtığında, Azure AD Kullanıcı hakkında bilgi içeren bir güvenlik belirteci oluşturur. Bu belirteç, uygulamaya geri gönderilmeden önce özel anahtarı kullanılarak Azure AD tarafından imzalanır. Belirtecin geçerli olduğunu ve Azure AD 'den geldiğini doğrulamak için, uygulamanın, kiracının [OpenID Connect bulgu belgesinde](https://openid.net/specs/openid-connect-discovery-1_0.html) veya SAML/WS-besbir federasyonda bulunan Azure AD tarafından kullanıma sunulan ortak anahtarı kullanarak belirtecin imzasını doğrulaması gerekir [ meta veri belgesi](azure-ad-federation-metadata.md).

Güvenlik nedeniyle, Azure AD 'nin imzalama anahtarı düzenli olarak kaydedilir ve acil durumda bir acil durum durumunda hemen üzerinden alınabilir. Azure AD ile tümleştirilen herhangi bir uygulama, ne sıklıkta gerçekleşebileceğini önemli bir anahtar geçişi olayını işleyecek şekilde hazırlanmalıdır. Yoksa ve uygulamanız bir belirteçte imzayı doğrulamak için bir süre sonu anahtarını kullanmayı denerse, oturum açma isteği başarısız olur.

OpenID Connect bulgu belgesinde ve Federasyon meta verileri belgesinde her zaman bir geçerli anahtar mevcuttur. Uygulamanız, belgede belirtilen anahtarlardan herhangi birini kullanmaya hazırlanmalıdır, çünkü bir anahtar yakında alınabilir, diğeri de bunun yerini alır ve bu şekilde devam eder.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Uygulamanızın etkilenip etkilendiğine ve bunun ne yapacaklarınızın nasıl değerlendirilecektir
Uygulamanızın anahtar rollover 'ı nasıl işleyeceği, uygulamanın türü veya kimlik protokolünün ve kitaplığın kullanıldığı değişkenlere bağlıdır. Aşağıdaki bölümler, en yaygın uygulama türlerinin anahtar rollover tarafından etkilenip etkilenmediğini değerlendirir ve uygulamanın otomatik geçişi desteklemek veya anahtarı el ile güncelleştirmek için nasıl güncellenebileceğine ilişkin yönergeler sağlar.

* [Kaynaklara erişen yerel istemci uygulamaları](#nativeclient)
* [Kaynaklara erişen web uygulamaları/API 'Ler](#webclient)
* [Kaynakları koruyan ve Azure Uygulama Hizmetleri kullanılarak oluşturulan Web uygulamaları/API 'Leri](#appservices)
* [.NET OWIN OpenID Connect, WS-beslenir veya Windowsazureactivedirectoryyataerauthentication ara yazılımı kullanılarak kaynakları koruyan Web uygulamaları/API 'Leri](#owin)
* [.NET Core OpenID Connect veya Jwtyataerauthentication ara yazılımı kullanılarak kaynakları koruyan Web uygulamaları/API 'Leri](#owincore)
* [Node. js Passport ile kaynakları koruyan Web uygulamaları/API 'Ler-Azure-ad modülü](#passport)
* [Kaynakları koruyan ve Visual Studio 2015 veya üzeri ile oluşturulan Web uygulamaları/API 'Leri](#vs2015)
* [Kaynakları koruyan ve Visual Studio 2013 ile oluşturulan Web uygulamaları](#vs2013)
* Kaynakları koruyan ve Visual Studio 2013 ile oluşturulan Web API 'Leri
* [Kaynakları koruyan ve Visual Studio 2012 ile oluşturulan Web uygulamaları](#vs2012)
* [Windows Identity Foundation kullanarak kaynakları koruyan ve Visual Studio 2010, 2008 o ile oluşturulan Web uygulamaları](#vs2010)
* [Diğer kitaplıkları kullanan veya desteklenen protokollerden herhangi birini uygulayan Web uygulamaları/API 'Leri, kaynakları koruyan](#other)

Bu kılavuz için geçerli **değildir** :

* Azure AD Uygulama Galerisi 'nden (özel dahil) eklenen uygulamalar, imzalama anahtarlarının dikkate aldığı ayrı bir kılavuza sahiptir. [Daha fazla bilgi.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Uygulama proxy 'si aracılığıyla yayımlanan şirket içi uygulamalar, imzalama anahtarları hakkında endişelenmenize gerek kalmaz.

### <a name="nativeclient"></a>Kaynaklara erişen yerel istemci uygulamaları
Yalnızca kaynaklara erişen uygulamalar (ör. Microsoft Graph, Keykasası, Outlook API ve diğer Microsoft API 'Leri) genellikle yalnızca bir belirteç alıp kaynak sahibine geçiş yapın. Hiçbir kaynağı korumadıklarından, belirteci incelemeyin ve bu nedenle düzgün bir şekilde imzalandığından emin olmak zorunda değildir.

Masaüstü veya mobil tarafından bu kategoriye giren ve bu nedenle geçiş tarafından etkilenmeyenler yerel istemci uygulamaları.

### <a name="webclient"></a>Kaynaklara erişen web uygulamaları/API 'Ler
Yalnızca kaynaklara erişen uygulamalar (ör. Microsoft Graph, Keykasası, Outlook API ve diğer Microsoft API 'Leri) genellikle yalnızca bir belirteç alıp kaynak sahibine geçiş yapın. Hiçbir kaynağı korumadıklarından, belirteci incelemeyin ve bu nedenle düzgün bir şekilde imzalandığından emin olmak zorunda değildir.

Yalnızca uygulama akışını kullanan Web uygulamaları ve Web API 'Leri (istemci kimlik bilgileri/istemci sertifikası), bu kategoriye girer ve bu nedenle geçiş tarafından etkilenmemektedir.

### <a name="appservices"></a>Kaynakları koruyan ve Azure Uygulama Hizmetleri kullanılarak oluşturulan Web uygulamaları/API 'Leri
Azure Uygulama Hizmetleri ' kimlik doğrulama/yetkilendirme (EasyAuth) işlevselliğinde, anahtar geçişi otomatik olarak işlemek için gerekli mantık zaten var.

### <a name="owin"></a>.NET OWIN OpenID Connect, WS-beslenir veya Windowsazureactivedirectoryyataerauthentication ara yazılımı kullanılarak kaynakları koruyan Web uygulamaları/API 'Leri
Uygulamanız .NET OWIN OpenID Connect, WS-beslenir veya Windowsazureactivedirectoryyataerauthentication ara yazılımı kullanıyorsa, anahtar geçişi otomatik olarak işlemek için gerekli mantık zaten vardır.

Uygulamanızın Startup.cs veya Startup.Auth.cs ' de aşağıdaki kod parçacıklarını arayarak uygulamanızın bunlardan herhangi birini kullandığını doğrulayabilirsiniz.

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
     });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="owincore"></a>.NET Core OpenID Connect veya Jwtyataerauthentication ara yazılımı kullanılarak kaynakları koruyan Web uygulamaları/API 'Leri
Uygulamanız .NET Core OWIN OpenID Connect veya Jwtyataerauthentication ara yazılımı kullanıyorsa, anahtar geçişi otomatik olarak işlemek için gerekli mantık zaten vardır.

Uygulamanızın Startup.cs veya Startup.Auth.cs ' de aşağıdaki kod parçacıklarını arayarak uygulamanızın bunlardan herhangi birini kullandığını doğrulayabilirsiniz.

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="passport"></a>Node. js Passport ile kaynakları koruyan Web uygulamaları/API 'Ler-Azure-ad modülü
Uygulamanız Node. js Passport-ad modülünü kullanıyorsa, anahtar geçişi otomatik olarak işlemek için gerekli mantık zaten vardır.

Uygulamanızın App. js dosyasında aşağıdaki kod parçacığını arayarak uygulamanızın Passport-ad ' ınızı doğrulayabilirsiniz.

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Kaynakları koruyan ve Visual Studio 2015 veya üzeri ile oluşturulan Web uygulamaları/API 'Leri
Uygulamanız, Visual Studio 2015 veya sonraki sürümlerde bir Web uygulaması şablonu kullanılarak oluşturulduysa ve **değişiklik kimlik doğrulaması** menüsünden **Iş veya okul hesapları** ' nı seçtiyseniz, anahtar geçişi otomatik olarak işlemek için gerekli mantık zaten vardır. OWıN OpenID Connect ara yazılım ' na gömülü olan bu mantık, OpenID Connect bulgu belgesinden anahtarları alır ve önbelleğe alır ve bunları düzenli aralıklarla yeniler.

Çözümünüze el ile kimlik doğrulaması eklediyseniz, uygulamanız gerekli anahtar geçiş mantığına sahip olmayabilir. Kendiniz yazmanız veya [diğer kitaplıkları kullanarak Web uygulamalarında/API 'lerde bulunan adımları izlemeniz veya desteklenen protokollerden herhangi birini el ile](#other)uygulamanız gerekecektir.

### <a name="vs2013"></a>Kaynakları koruyan ve Visual Studio 2013 ile oluşturulan Web uygulamaları
Uygulamanız Visual Studio 2013 bir Web uygulaması şablonu kullanılarak oluşturulmuşsa ve **kimlik doğrulama** menüsünden **Kurumsal hesaplar** ' ı seçtiyseniz, anahtar geçişi otomatik olarak işlemek için gerekli mantık zaten vardır. Bu mantık, kuruluşunuzun benzersiz tanımlayıcısını ve imza anahtarı bilgilerini projeyle ilişkili iki veritabanı tablosu halinde depolar. Veritabanının bağlantı dizesini projenin Web. config dosyasında bulabilirsiniz.

Çözümünüze el ile kimlik doğrulaması eklediyseniz, uygulamanız gerekli anahtar geçiş mantığına sahip olmayabilir. Kendiniz yazmak veya adımları gerekecek [Web uygulamaları / diğer kitaplıkları'nı kullanarak veya el ile desteklenen protokoller hiçbirini uygulama API'leri](#other).

Aşağıdaki adımlar, mantığın uygulamanızda düzgün çalıştığını doğrulamanıza yardımcı olur.

1. Visual Studio 2013, çözümü açın ve sağ penceredeki **Sunucu Gezgini** sekmesine tıklayın.
2. **Veri bağlantıları**, **DefaultConnection**ve ardından **Tablolar**' ı genişletin. **Issuingauthoritykeys** tablosunu bulun, sağ tıklayın ve ardından **tablo verilerini göster**' e tıklayın.
3. **Issuingauthoritykeys** tablosunda, anahtar için parmak izi değerine karşılık gelen en az bir satır olacaktır. Tablodaki satırları silin.
4. **Kiracılar** tablosuna sağ tıklayın ve ardından **tablo verilerini göster**' e tıklayın.
5. **Kiracılar** tablosunda, benzersiz bir dizin kiracı tanımlayıcısına karşılık gelen en az bir satır olacaktır. Tablodaki satırları silin. Hem **kiracılar** tablosu hem de **ıssuingauthoritykeys** tablosundaki satırları silmezseniz, çalışma zamanında bir hata alırsınız.
6. Uygulamayı derleyin ve çalıştırın. Hesabınızda oturum açtıktan sonra, uygulamayı durdurabilirsiniz.
7. **Sunucu Gezgini** dönün ve **ıssuingauthoritykeys** ve **kiracılar** tablosundaki değerlere bakın. Federasyon meta veri belgesinden uygun bilgilerle otomatik olarak yeniden doldurulduğuna dikkat edin.

### <a name="vs2013"></a>Kaynakları koruyan ve Visual Studio 2013 ile oluşturulan Web API 'Leri
Visual Studio 2013 ' de Web API şablonu kullanarak bir Web API uygulaması oluşturduysanız ve sonra **değişiklik kimlik doğrulaması** menüsünden **Kurumsal hesaplar** ' ı seçtiyseniz, uygulamanızda gerekli mantığa sahipsiniz demektir.

Kimlik doğrulamasını el ile yapılandırdıysanız, kendi anahtar bilgilerini otomatik olarak güncelleştirmek üzere Web API 'nizi nasıl yapılandıracağınızı öğrenmek için aşağıdaki yönergeleri izleyin.

Aşağıdaki kod parçacığı, Federasyon meta veri belgesinden en son anahtarları nasıl alınacağını ve sonra belirteci doğrulamak için [JWT belirteci işleyicisini](https://msdn.microsoft.com/library/dn205065.aspx) nasıl kullanacağınızı gösterir. Kod parçacığı, Azure AD 'den gelecek belirteçleri doğrulamak için bir veritabanı, yapılandırma dosyası veya başka bir yerde olsun, anahtarı kalıcı hale getiren kendi önbelleğe alma mekanizmanızı kullanacağınızı varsayar.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Kaynakları koruyan ve Visual Studio 2012 ile oluşturulan Web uygulamaları
Uygulamanız Visual Studio 2012 ' de oluşturulduysa, büyük olasılıkla uygulamanızı yapılandırmak için kimlik ve erişim aracını kullanmışsınızdır. Bu, [doğrulama verenin ad kayıt defteri 'ni (VINR)](https://msdn.microsoft.com/library/dn205067.aspx)de kullanıyor olabilirsiniz. HAVR, güvenilir kimlik sağlayıcıları (Azure AD) ve bunlar tarafından verilen belirteçleri doğrulamak için kullanılan anahtarlarla ilgili bilgilerin korunmasından sorumludur. VINR Ayrıca dizininizle ilişkili en son Federasyon meta veri belgesini indirerek, yapılandırmanın en son belgeyle güncel olup olmadığını kontrol ederek bir Web. config dosyasında depolanan anahtar bilgilerini otomatik olarak güncelleştirmeyi kolaylaştırır ve uygulamayı yeni anahtarı gerektiği gibi kullanacak şekilde güncelleştirme.

Uygulamanızı Microsoft tarafından sağlanan kod örneklerinden veya İzlenecek yol belgelerinden birini kullanarak oluşturduysanız, anahtar aktarma mantığı projenize zaten dahil edilmiştir. Aşağıdaki kodun projenizde zaten var olduğunu fark edeceksiniz. Uygulamanızda zaten bu mantık yoksa, eklemek ve düzgün çalıştığını doğrulamak için aşağıdaki adımları izleyin.

1. **Çözüm Gezgini**, uygun proje için **System. IdentityModel** derlemesine bir başvuru ekleyin.
2. **Global.asax.cs** dosyasını açın ve aşağıdaki using yönergelerini ekleyin:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. **Global.asax.cs** dosyasına aşağıdaki yöntemi ekleyin:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. **Global.asax.cs** içindeki **Application_Start ()** yönteminde, belirtilen şekilde **refreshvalidationsettings ()** yöntemini çağırın:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Bu adımları izledikten sonra, uygulamanızın Web. config dosyası, en son anahtarlar dahil olmak üzere federasyon meta veri belgesinden en son bilgilerle güncelleştirilir. Bu güncelleştirme, uygulama havuzunuzun IIS 'de her geri dönüştürüldüğünde oluşur; Varsayılan olarak IIS, uygulamaları her 29 saatte bir geri dönüştürmek üzere ayarlanmıştır.

Anahtar aktarma mantığının çalıştığını doğrulamak için aşağıdaki adımları izleyin.

1. Uygulamanızın yukarıdaki kodu kullandığını doğruladıktan sonra, **Web. config** dosyasını açın ve özellikle aşağıdaki birkaç satırı arayarak  **\<ıssuernameregstreams >** bloğuna gidin:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. **Parmak izi Ekle="">ayarında,herhangibirkarakterifarklıbirkarakterledeğiştirerekparmakizideğerinideğiştirin.\<** **Web. config** dosyasını kaydedin.
3. Uygulamayı derleyin ve çalıştırın. Oturum açma işlemini tamamlayabilirseniz, uygulamanız dizininizin Federasyon meta veri belgesinden gerekli bilgileri indirerek anahtarı başarıyla güncelliyor. Oturum açarken sorun yaşıyorsanız, [Azure AD makalesini kullanarak Web uygulamanıza oturum açma eklemeyi](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) veya aşağıdaki kod örneğini indirerek ve inceleyerek uygulamanızdaki değişikliklerin doğru olduğundan emin olun: [Azure Active Directory Için çok kiracılı bulut uygulaması](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="vs2010"></a>.NET 3,5 için kaynakları koruyan ve Visual Studio 2008 ya da 2010 ve Windows Identity Foundation (WıF) v 1.0 ile oluşturulan Web uygulamaları
WıF v 1.0 üzerinde bir uygulama oluşturduysanız, yeni bir anahtar kullanmak için uygulamanızın yapılandırmasını otomatik olarak yenilemek üzere bir sağlanmayan mekanizma yoktur.

* *En kolay yol* WıF SDK ' da bulunan FedUtil aracı 'nı kullanın. Bu, en son meta veri belgesini alabilir ve yapılandırmanızı güncelleştirebilir.
* Uygulamanızı, sistem ad alanında yer alan en yeni WıF sürümünü içeren .NET 4,5 ' a güncelleştirin. Daha sonra, uygulama yapılandırmasının otomatik güncelleştirmelerini gerçekleştirmek için [doğrulama verenin adı kayıt defterini (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) kullanabilirsiniz.
* Bu kılavuz belgesinin sonundaki yönergelere göre el ile geçiş gerçekleştirin.

Yapılandırmanızı güncelleştirmek için FedUtil kullanma yönergeleri:

1. Visual Studio 2008 veya 2010 için geliştirme makinenizde WıF v 1.0 SDK 'nın yüklü olduğunu doğrulayın. Henüz yüklemediyseniz, [buradan indirebilirsiniz](https://www.microsoft.com/en-us/download/details.aspx?id=4451) .
2. Visual Studio 'da çözümü açın ve ardından geçerli projeye sağ tıklayıp **Federasyon meta verilerini Güncelleştir**' i seçin. Bu seçenek kullanılamıyorsa, FedUtil ve/veya WıF v 1.0 SDK yüklü değildir.
3. Komut isteminde, Federasyon meta verilerinizi güncelleştirmeye başlamak için **Güncelleştir** ' i seçin. Uygulamanın barındırıldığı sunucu ortamına erişiminiz varsa, isteğe bağlı olarak FedUtil 'nin [Otomatik meta veri güncelleştirme zamanlayıcısını](https://msdn.microsoft.com/library/ee517272.aspx)kullanabilirsiniz.
4. Güncelleştirme işlemini gerçekleştirmek için **son** ' a tıklayın.

### <a name="other"></a>Diğer kitaplıkları kullanan veya desteklenen protokollerden herhangi birini uygulayan Web uygulamaları/API 'Leri, kaynakları koruyan
Başka bir kitaplık kullanıyorsanız veya desteklenen protokollerden herhangi birini el ile uyguladıysanız, anahtarın OpenID Connect Discovery belgesinden veya Federasyon meta verilerinden alındığından emin olmak için kitaplığı veya uygulamanızı gözden geçirmeniz gerekir belgedeki. Bunu kontrol etmenin bir yolu, OpenID bulgu belgesine veya Federasyon meta veri belgesine yapılan çağrılar için kodunuzda veya kitaplığın kodunda bir arama yapmak.

Bu anahtar, uygulamanızda bir yere veya sabit kodlanmış olarak depolanırsa, bu kılavuz belgesinin sonundaki yönergelere göre el ile bir rollover gerçekleştirerek, anahtarı el ile alabilir ve uygun şekilde güncelleştirebilirsiniz. Azure AD 'nin geçiş temposunda arttığı veya bir acil durum nedeniyle daha fazla kesintiler ve yük devretme yapmaktan kaçınmak için uygulamanızı geliştirmenin, bu makaledeki yaklaşımlardan herhangi birini kullanarak **otomatik geçişi desteklemesini** öneririz. bant dışı geçiş.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Uygulamanızı, etkilenip etkilenmediğine yönelik test etme
Komut dosyalarını indirerek ve [Bu GitHub deposundaki](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) yönergeleri izleyerek uygulamanızın otomatik anahtar geçişi 'ni destekleyip desteklemediğini doğrulayabilirsiniz.

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Uygulamanız otomatik geçişi desteklemiyorsa el ile geçiş gerçekleştirme
Uygulamanız otomatik geçişi desteklemiyorsa , Azure AD 'nin imzalama anahtarlarını düzenli olarak izleyen ve buna uygun bir el ile geçiş gerçekleştiren bir işlem oluşturmanız gerekir. [Bu GitHub deposu](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) bunun nasıl yapılacağı hakkında betikler ve yönergeler içerir.

