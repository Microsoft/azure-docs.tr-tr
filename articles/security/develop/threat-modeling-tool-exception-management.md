---
title: Özel durum yönetimi-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Threat Modeling Tool özel durum yönetimi hakkında bilgi edinin. Bkz. risk azaltma bilgileri ve kod örneklerini görüntüleme.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 41532e554623c47e9728c6ccab92d99500e42021
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517436"
---
# <a name="security-frame-exception-management--mitigations"></a>Güvenlik çerçevesi: özel durum yönetimi | Karşı 
| Ürün/hizmet | Makale |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF-yapılandırma dosyasına serviceDebug düğümünü eklemeyin](#servicedebug)</li><li>[WCF-yapılandırma dosyasına serviceMetadata düğümünü dahil etme](#servicemetadata)</li></ul> |
| **Web API** | <ul><li>[ASP.NET Web API 'sinde uygun özel durum işlemenin yapıldığından emin olun](#exception)</li></ul> |
| **Web uygulaması** | <ul><li>[Hata iletilerinde güvenlik ayrıntılarını gösterme](#messages)</li><li>[Varsayılan hata işleme sayfasını Uygula](#default)</li><li>[IIS 'de dağıtım yöntemini perakende olarak ayarla](#deployment)</li><li>[Özel durumların güvenle başarısız olması gerekir](#fail)</li></ul> |

## <a name="wcf--do-not-include-servicedebug-node-in-configuration-file"></a><a id="servicedebug"></a>WCF-yapılandırma dosyasına serviceDebug düğümünü eklemeyin

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | WCF | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel, NET Framework 3 |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [fortify Krallığı](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Adımlar** | Windows Communication Framework (WCF) Hizmetleri, hata ayıklama bilgilerini açığa çıkarmak için yapılandırılabilir. Hata ayıklama bilgileri üretim ortamlarında kullanılmamalıdır. `<serviceDebug>`Etiketi, hata ayıklama bilgisi özelliğinin BIR WCF hizmeti için etkinleştirilip etkinleştirilmeyeceğini tanımlar. IncludeExceptionDetailInFaults özniteliği true olarak ayarlanırsa, uygulamadaki özel durum bilgileri istemcilere döndürülür. Saldırganlar, uygulama tarafından kullanılan çerçeve, veritabanı veya diğer kaynaklara hedeflenmiş bağlama saldırılarına karşı, çıkış hata ayıklamadan elde ettikleri ek bilgilerden yararlanabilir. |

### <a name="example"></a>Örnek
Aşağıdaki yapılandırma dosyası `<serviceDebug>` etiketini içerir: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Hizmette hata ayıklama bilgilerini devre dışı bırakın. Bu, `<serviceDebug>` etiketi uygulamanızın yapılandırma dosyasından kaldırarak gerçekleştirilebilir. 

## <a name="wcf--do-not-include-servicemetadata-node-in-configuration-file"></a><a id="servicemetadata"></a>WCF-yapılandırma dosyasına serviceMetadata düğümünü dahil etme

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | WCF | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Genel, NET Framework 3 |
| **Başvurular**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [fortify Krallığı](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Adımlar** | Bir hizmetle ilgili olarak genel olarak kullanıma sunulmaları, saldırganlar tarafından hizmetten nasıl yararlanabileceğinize dair değerli bilgiler sağlayabilir. `<serviceMetadata>`Etiketi, meta veri yayımlama özelliğini sunar. Hizmet meta verileri, herkese açık olmaması gereken hassas bilgiler içerebilir. En azından, güvenilen kullanıcıların meta verilere erişmesine izin verin ve gereksiz bilgilerin açığa çıkmadığından emin olun. Daha iyi ancak meta verileri yayımlama özelliğini tamamen devre dışı bırakın. Güvenli bir WCF yapılandırması `<serviceMetadata>` etiketini içermez. |

## <a name="ensure-that-proper-exception-handling-is-done-in-aspnet-web-api"></a><a id="exception"></a>ASP.NET Web API 'sinde uygun özel durum işlemenin yapıldığından emin olun

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web API | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | MVC 5, MVC 6 |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [ASP.NET Web API 'Sinde özel durum işleme](https://www.asp.net/web-api/overview/error-handling/exception-handling), [ASP.NET Web API 'de model doğrulama](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Adımlar** | Varsayılan olarak, ASP.NET Web API 'sindeki en yakalanamayan özel durumlar durum kodu ile bir HTTP yanıtına çevrilir `500, Internal Server Error`|

### <a name="example"></a>Örnek
API tarafından döndürülen durum kodunu denetlemek için `HttpResponseException` aşağıda gösterildiği gibi kullanılabilir: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>Örnek
Özel durum yanıtında daha fazla denetim için, `HttpResponseMessage` sınıfı aşağıda gösterildiği gibi kullanılabilir: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
Türünde olmayan işlenmemiş özel durumları yakalamak için `HttpResponseException` özel durum filtreleri kullanılabilir. Arabirimi uygulayan özel durum filtreleri `System.Web.Http.Filters.IExceptionFilter` . Bir özel durum filtresi yazmanın en basit yolu, sınıfından türetmektir `System.Web.Http.Filters.ExceptionFilterAttribute` ve OnException metodunu geçersiz kılar. 

### <a name="example"></a>Örnek
`NotImplementedException`Özel DURUMLARı http durum koduna dönüştüren bir filtre aşağıda verilmiştir `501, Not Implemented` : 
```csharp
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

Web API özel durum filtresini kaydetmek için çeşitli yollar vardır:
- Eyleme göre
- Denetleyiciye göre
- Görünüm

### <a name="example"></a>Örnek
Filtreyi belirli bir eyleme uygulamak için, filtreyi eyleme bir öznitelik olarak ekleyin: 
```csharp
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>Örnek
Filtreyi bir içindeki tüm eylemlere uygulamak için `controller` , filtre bir özniteliği olarak `controller` sınıfa ekleyin: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Örnek
Filtreyi tüm Web API denetleyicilerine Global olarak uygulamak için, bir filtrenin bir örneğini `GlobalConfiguration.Configuration.Filters` koleksiyona ekleyin. Bu koleksiyondaki özel durum filtreleri Web API denetleyicisi işlemleri için geçerlidir. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Örnek
Model doğrulaması için, model durumu aşağıda gösterildiği gibi CreateErrorResponse metoduna geçirilebilir: 
```csharp
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

ASP.NET Web API 'sinde olağanüstü işlem ve model doğrulama hakkında ek ayrıntılar için başvurular bölümündeki bağlantıları denetleyin 

## <a name="do-not-expose-security-details-in-error-messages"></a><a id="messages"></a>Hata iletilerinde güvenlik ayrıntılarını gösterme

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | Yok  |
| **Adımlar** | <p>Genel hata iletileri, hassas uygulama verileri dahil etmeden doğrudan kullanıcıya sağlanır. Hassas verilere örnek olarak şunlar verilebilir:</p><ul><li>Sunucu adları</li><li>Bağlantı dizeleri</li><li>Kullanıcı</li><li>Parolalar</li><li>SQL yordamları</li><li>Dinamik SQL hatalarının ayrıntıları</li><li>Yığın izleme ve kod satırları</li><li>Bellekte depolanan değişkenler</li><li>Sürücü ve klasör konumları</li><li>Uygulama yüklemesi noktaları</li><li>Konak yapılandırma ayarları</li><li>Diğer iç uygulama ayrıntıları</li></ul><p>Bir uygulamadaki tüm hataları yakalamak ve genel hata iletileri sağlamak ve ayrıca IIS içinde özel hataları etkinleştirmek, bilgilerin açığa çıkmasını önlemeye yardımcı olur. Diğer hata işleme mimarilerinin yanı sıra SQL Server veritabanı ve .NET özel durum işleme, uygulamanızın profilini oluşturulan kötü niyetli bir kullanıcı için özellikle ayrıntılıdır ve son derece yararlıdır. .NET özel durum sınıfından türetilmiş bir sınıfın içeriğini doğrudan görüntülemediğinizden ve beklenmeyen bir özel durumun yanlışlıkla doğrudan kullanıcıya çıkarılmaması için uygun özel durum işleme sahip olduğunuzdan emin olun.</p><ul><li>Genel hata iletilerini doğrudan özel durum/hata iletisinde bulunan özel ayrıntıları soyutlamak için doğrudan kullanıcıya sağlayın</li><li>.NET özel durum sınıfının içeriğini doğrudan kullanıcıya gösterme</li><li>Tüm hata iletilerini yakalama ve kullanıcıya uygulama istemcisine gönderilen genel bir hata iletisi ile ilgili bilgi verme</li><li>Özel durum sınıfının içeriğini doğrudan kullanıcıya, özellikle de dönüş değerine `.ToString()` veya ileti ya da StackTrace özelliklerine ilişkin değerleri kullanıma sunmayın. Bu bilgileri güvenli bir şekilde günlüğe kaydedin ve kullanıcıya daha fazla zararsız iletisi görüntüleyin</li></ul>|

## <a name="implement-default-error-handling-page"></a><a id="default"></a>Varsayılan hata işleme sayfasını Uygula

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [ASP.NET Hata Sayfası Ayarlarını Düzenle İletişim Kutusu](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd569096(v=ws.10)) |
| **Adımlar** | <p>Bir ASP.NET uygulaması başarısız olduğunda ve bir HTTP/1. x 500 Iç sunucu hatasına neden olduğunda veya bir özellik yapılandırması (örneğin, Istek filtrelemesi) bir sayfanın görüntülenmesini engelliyorsa bir hata iletisi oluşturulur. Yöneticiler, uygulamanın istemciye kolay bir ileti görüntülemesini, istemciye yönelik ayrıntılı hata iletisini veya yalnızca localhost 'a yönelik ayrıntılı hata iletisini seçmesini seçebilir. `<customErrors>`web.config etiketinde üç mod vardır:</p><ul><li>**Açık:** Özel hataların etkinleştirildiğini belirtir. Hiçbir defaultRedirect özniteliği belirtilmemişse, kullanıcılar genel bir hata görür. Özel hatalar uzak istemcilere ve yerel konağa gösterilir</li><li>**Kapalı:** Özel hataların devre dışı bırakıldığını belirtir. Ayrıntılı ASP.NET hataları uzak istemcilere ve yerel konağa gösterilir</li><li>**Yalnızca RemoteOnly:** Özel hataların yalnızca uzak istemcilere gösterildiğini ve ASP.NET hatalarının yerel ana bilgisayara gösterildiğini belirtir. Bu varsayılan değerdir</li></ul><p>`web.config`Uygulama/site için dosyasını açın ve etiketin ya da tanımlı olduğundan emin olun `<customErrors mode="RemoteOnly" />` `<customErrors mode="On" />` .</p>|

## <a name="set-deployment-method-to-retail-in-iis"></a><a id="deployment"></a>IIS 'de dağıtım yöntemini perakende olarak ayarla

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Dağıtım |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [Deployment öğesi (ASP.NET Settings şeması)](/previous-versions/dotnet/netframework-2.0/ms228298(v=vs.80)) |
| **Adımlar** | <p>`<deployment retail>`Anahtar, ÜRETIM IIS sunucuları tarafından kullanılmak üzere tasarlanmıştır. Bu anahtar, uygulamanın bir sayfada izleme çıktısı oluşturma, son kullanıcılara ayrıntılı hata iletilerini görüntüleme ve hata ayıklama anahtarını devre dışı bırakma özelliğini devre dışı bırakarak uygulamaların en iyi olası performans ve en az olası güvenlik bilgisi sızıntısı ile çalışmasına yardımcı olmak için kullanılır.</p><p>Çoğu zaman, başarısız istek izleme ve hata ayıklama gibi geliştirici odaklı olan anahtarlar ve seçenekler etkin geliştirme sırasında etkinleştirilir. Herhangi bir üretim sunucusundaki dağıtım yönteminin perakende olarak ayarlanması önerilir. machine.config dosyasını açın ve `<deployment retail="true" />` true olarak ayarlandığından emin olun.</p>|

## <a name="exceptions-should-fail-safely"></a><a id="fail"></a>Özel durumların güvenle başarısız olması gerekir

| Başlık                   | Ayrıntılar      |
| ----------------------- | ------------ |
| **Bileşen**               | Web Uygulaması | 
| **SDL aşaması**               | Derleme |  
| **İlgili teknolojiler** | Genel |
| **Öznitelikler**              | Yok  |
| **Başvurular**              | [Güvenli bir şekilde başarısız](https://owasp.org/www-community/Fail_securely) |
| **Adımlar** | Uygulamanın güvenli bir şekilde başarısız olması gerekir. Belirli bir kararın hangi kararı yaptığını temel alarak Boole değeri döndüren herhangi bir yöntem, özel durum bloğunun dikkatle oluşturulmasını sağlamalıdır. Özel durum bloğu çok daha sorunsuz yazıldığında, hangi güvenlik sorunlarından dolayı katdığı önemli birçok mantıksal hata vardır.|

### <a name="example"></a>Örnek
```csharp
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
Yukarıdaki yöntem, bazı özel durumlar oluşursa her zaman true olarak döndürülür. Son Kullanıcı hatalı biçimlendirilmiş bir URL sağlıyorsa, tarayıcı buna uyar, ancak `Uri()` Oluşturucu bunu yapmaz; bu, bir özel durum oluşturur ve kurban geçerli ancak hatalı BIÇIMLENDIRILMIŞ URL 'ye alınır.