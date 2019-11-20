---
title: Azure cdn'de web içeriğinin kullanım süresini yönetme | Microsoft Docs
description: Azure CDN, Azure Web Apps/Cloud Services, ASP.NET veya IIS içeriğinin kullanım süresini yönetme konusunda bilgi edinin.
services: cdn
documentationcenter: .NET
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: magattus
ms.openlocfilehash: a701c332659181081184906a73826b7137d8c49c
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593712"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Azure cdn'de web içeriğinin kullanım süresini yönetme
> [!div class="op_single_selector"]
> * [Azure web içeriği](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Depolama](cdn-manage-expiration-of-blob-content.md)
> 

Genel olarak erişilebilen kaynak web sunucusundan dosyaları sona erdiğinde, yaşam süresi (TTL) kadar Azure Content Delivery Network (CDN) önbelleğe alınabilir. TTL değeri tarafından belirlenir `Cache-Control` kaynak sunucusundan gelen HTTP yanıt üst bilgisi. Bu makalede nasıl ayarlanacağını `Cache-Control` Microsoft Azure App Service, Azure Cloud Services, ASP.NET uygulamaları ve her biri benzer şekilde yapılandırılmış Internet Information Services (IIS) siteleri, Web Apps özelliği için üstbilgiler. Ayarlayabileceğiniz `Cache-Control` üstbilgi yapılandırma dosyaları kullanılarak veya program aracılığıyla. 

Önbellek ayarları Azure portalından ayarlayarak da kontrol edebilirsiniz [CDN önbelleğe alma kuralları](cdn-caching-rules.md). Oluşturduğunuz bir veya daha fazla önbelleğe alma kuralları ve kendi önbelleğe alma davranışını ayarlayın **geçersiz kılma** veya **önbelleği atla**, bu makalede ele alınan kaynak tarafından sağlanan önbelleğe alma ayarları göz ardı edilir. Genel önbelleğe alma kavramları hakkında daha fazla bilgi için bkz. [önbelleğe alma nasıl işler](cdn-how-caching-works.md).

> [!TIP]
> Dosya hiçbir TTL ayarlamak seçebilirsiniz. Bu durumda, Azure CDN, otomatik olarak önbelleğe alma kuralları Azure portalında ayarladığınız sürece varsayılan TTL yedi gün geçerlidir. Bu varsayılan TTL yalnızca genel web teslimatı iyileştirmeler için geçerlidir. Büyük dosya iyileştirmeler için varsayılan TTL bir gündür ve en iyi duruma getirme akış medya için varsayılan TTL bir yıldır.
> 
> Azure CDN dosyaları ve diğer kaynaklara erişimi hızlandırma işleyişi hakkında daha fazla bilgi için bkz. [Azure Content Delivery Network'e genel bakış](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>CDN önbelleğe alma kuralları kullanarak Cache-Control üst bilgileri ayarlama
Bir web sunucusunun ayarlamak için tercih edilen yöntem `Cache-Control` başlığıdır Azure portalında önbelleğe alma kuralları kullanılacak. CDN önbelleğe alma kuralları hakkında daha fazla bilgi için bkz. [denetimi Azure CDN önbelleğe alma kuralları ile önbelleğe alma davranışını](cdn-caching-rules.md).

> [!NOTE] 
> Önbelleğe alma kuralları, yalnızca kullanılabilir **verizon'dan Azure CDN standart** ve **akamai'den Azure CDN standart** profilleri. İçin **verizon'dan Azure CDN Premium** profilleri kullanmalıdır [Azure CDN kurallar altyapısı](cdn-rules-engine.md) içinde **Yönet** benzer işlevselliği için portal.

**CDN önbelleğe alma kuralları sayfasına gitmek için**:

1. Azure portalında bir CDN profili seçin ve ardından web sunucusu için uç nokta seçin.

1. Ayarların altındaki sol bölmede **Önbelleğe alma kuralları**’nı seçin.

   ![CDN önbelleğe alma kuralları düğmesi](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   **Önbelleğe alma kuralları** sayfası görüntülenir.

   ![CDN önbelleğe alma sayfası](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Genel önbelleğe alma kuralları kullanarak bir web sunucusunun Cache-Control üst bilgilerini ayarlamak için:**

1. Altında **genel önbelleğe alma kuralları**ayarlayın **sorgu dizesini önbelleğe alma davranışı** için **sorgu dizelerini Yoksay** ayarlayıp **önbelleğe alma davranışını** için **Geçersiz kılma**.
      
1. İçin **önbellek sona erme süresi**, 3600 içinde girin **saniye** kutusu veya 1'de **saat** kutusu. 

   ![CDN genel önbelleğe alma kuralları örneği](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Bu genel önbelleğe alma kuralı, bir önbellek süresi bir saat ayarlar ve uç noktaya yönelik tüm istekler etkiler. Tüm geçersiz kılmaları `Cache-Control` veya `Expires` bitiş noktası tarafından belirtilen kaynak sunucu tarafından gönderilen HTTP üstbilgileri.   

1. **Kaydet**’i seçin.

**Özel önbelleğe alma kuralları kullanarak bir web sunucusu dosyanın Cache-Control üst bilgilerini ayarlamak için:**

1. Altında **özel önbelleğe alma kuralları**, iki eşleşme koşul oluşturun:

     a. İlk eşleşme koşulu için **eşleşen koşul** için **yolu** girin `/webfolder1/*` için **eşleşecek değer**. Ayarlama **önbelleğe alma davranışını** için **geçersiz kılma** ve 4'te girin **saat** kutusu.

     b. İkinci eşleşme koşulu için **eşleşen koşul** için **yolu** girin `/webfolder1/file1.txt` için **eşleşecek değer**. Ayarlama **önbelleğe alma davranışını** için **geçersiz kılma** ve 2'de girin **saat** kutusu.

    ![CDN özel önbelleğe alma kuralları örneği](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    İlk özel önbelleğe alma kuralı bir önbellek süresi klasördeki tüm dosyaları için dört saatlik ayarlar `/webfolder1` , uç noktası tarafından belirtilen kaynak sunucusunda klasör. İlk kural ikinci kuralı geçersiz kılar `file1.txt` yalnızca dosya ve ayarlar bir önbellek süresi iki saattir.

1. **Kaydet**’i seçin.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Yapılandırma dosyalarını kullanarak Cache-Control üst bilgileri ayarlama
Görüntüleri ve stil sayfalarını gibi statik içerikler için güncelleştirme sıklığını değiştirerek denetleyebilirsiniz **applicationHost.config** veya **Web.config** web uygulamanız için yapılandırma dosyaları. Ayarlanacak `Cache-Control` kullanım içeriğiniz için üst bilgi `<system.webServer>/<staticContent>/<clientCache>` ya da dosyasında öğe.

### <a name="using-applicationhostconfig-files"></a>ApplicationHost.config dosyalarını kullanma
**ApplicationHost.config** dosya, IIS yapılandırma sistemi, kök dosyasıdır. Yapılandırma ayarlarında bir **ApplicationHost.config** dosya sitedeki tüm uygulamaları etkiler ancak herhangi bir ayarları tarafından geçersiz kılınır **Web.config** mevcut dosyalar için bir web uygulaması.

### <a name="using-webconfig-files"></a>Web.config dosyalarını kullanma
İle bir **Web.config** dosyası, tüm web uygulamanızı veya web uygulamanızda belirli bir dizin davranışını özelleştirebilirsiniz. Genellikle, en az bir sahip **Web.config** web uygulamanızın kök klasöründe bir dosya. Her **Web.config** dosyası, en alt düzeyde bir başkası tarafından geçersiz kılınır sürece belirli bir klasörde yapılandırma ayarlarını her şey bu klasöre ve alt klasörlerinde etkileyen **Web.config** dosya. 

Örneğin, ayarlayabileceğiniz bir `<clientCache>` öğesinde bir **Web.config** web uygulamanız üç gün için tüm statik içeriği önbelleğe almak için web uygulamanızın kök klasöründe bir dosya. De ekleyebilirsiniz bir **Web.config** daha değişken içeriğe sahip klasörde bir dosya (örneğin, `\frequent`) ve kendi `<clientCache>` öğesi bir alt klasörün içeriği altı saat boyunca önbelleğe almak için. Tüm bu içeriğe net sonucudur web sitesi için herhangi bir içerik hariç üç gün boyunca önbellekte `\frequent` yalnızca altı saat için önbelleğe alınan dizin.  

Aşağıdaki XML yapılandırma dosyası örneği nasıl ayarlanacağını gösterir `<clientCache>` öğesi üç gün yaş üst sınırını belirtmek için:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Kullanılacak **cacheControlMaxAge** öznitelik değerini ayarlamalısınız **cacheControlMode** özniteliğini `UseMaxAge`. Bu ayar HTTP üst bilgi ve yönerge neden `Cache-Control: max-age=<nnn>`, yanıta eklenecek. Timespan değeri biçimi **cacheControlMaxAge** özniteliği `<days>.<hours>:<min>:<sec>`. Değeri saniye dönüştürülür ve değeri olarak kullanılan `Cache-Control` `max-age` yönergesi. Hakkında daha fazla bilgi için `<clientCache>` öğesi bkz [istemci önbellek \<clientCache >](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Cache-Control üst bilgileri programlı olarak ayarlama
ASP.NET uygulamaları için programlı olarak ayarlayarak CDN önbelleğe alma davranışını denetleyen **HttpResponse.Cache** .NET API özelliği. Hakkında bilgi için **HttpResponse.Cache** özelliğine bakın [HttpResponse.Cache özelliği](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) ve [HttpCachePolicy sınıfı](/dotnet/api/system.web.httpcachepolicy).  

Program aracılığıyla önbellek uygulama içeriği için ASP.NET, aşağıdaki adımları izleyin:
   1. İçerik ayarlayarak gibi önbelleğe kaydedilemeyen işaretli olduğunu doğrulayın `HttpCacheability` için `Public`. 
   1. Aşağıdakilerden birini çağırarak bir önbellek Doğrulayıcı ayarlamak `HttpCachePolicy` yöntemleri:
      - Çağrı `SetLastModified` bir zaman damgası değeri ayarlamak için `Last-Modified` başlığı.
      - Çağrı `SetETag` için bir değer ayarlamak için `ETag` başlığı.
   1. İsteğe bağlı olarak, çağırarak bir önbellek sona erme zamanı belirtin `SetExpires` için bir değer ayarlamak için `Expires` başlığı. Aksi takdirde, bu belgede daha önce açıklanan varsayılan önbellek buluşsal yöntemleri uygulayın.

Örneğin, bir saat için içerik önbelleği için aşağıdaki C# kodu ekleyin:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Cache-Control üst bilgisi test etme
Web içeriğinize TTL ayarlarını kolayca doğrulayabilirsiniz. Tarayıcınızın ile [Geliştirici Araçları](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), web içeriği test `Cache-Control` yanıtı üstbilgisi. Gibi bir araç kullanabilirsiniz **wget**, [Postman](https://www.getpostman.com/), veya [Fiddler](https://www.telerik.com/fiddler) yanıt üstbilgileri incelemek üzere.

## <a name="next-steps"></a>Sonraki Adımlar
* [Ayrıntıları okuyun **clientCache** öğesi](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Belgelerini okuyun **HttpResponse.Cache** özelliği](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Belgelerini okuyun **HttpCachePolicy sınıfı**](/dotnet/api/system.web.httpcachepolicy)  
* [Önbelleğe alma kavramları hakkında bilgi edinin](cdn-how-caching-works.md)
