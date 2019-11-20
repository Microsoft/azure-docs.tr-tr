---
title: Visual Studio 'da bağlı hizmetlerle Bing Haber Arama API'si bağlanın veC#
titleSuffix: Azure Cognitive Services
description: Bing Haber Arama hizmetini kullanarak uygulamaların ve hizmetlerin web ölçeğindeki reklamsız bir arama motorunun gücünden faydalanmasını sağlayabilirsiniz.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: ghogen
ms.openlocfilehash: 0a8a4f69c94f217cb40146b022adf2b2d56c8d42
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111503"
---
# <a name="tutorial-connect-to-bing-news-search-api-with-connected-services-in-visual-studio-and-c"></a>Öğretici: Visual Studio 'da bağlı hizmetlerle Bing Haber Arama API'si bağlanın veC#

Bing Haber Arama hizmetini kullanarak uygulamaların ve hizmetlerin web ölçeğindeki reklamsız bir arama motorunun gücünden faydalanmasını sağlayabilirsiniz. Bing Haber Arama, Bilişsel Hizmetler kapsamında sunulan arama hizmetlerinden biridir.

Bu makalede Bing Haber Arama hizmeti için Visual Studio Bağlı Hizmet özelliğini kullanma hakkında ayrıntılı bilgiler verilmektedir. Özellik, Bilişsel Hizmetler uzantısının yüklendiği Visual Studio 2017 15.7 ve sonraki sürümlerde mevcuttur.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bir aboneliğiniz yoksa [ücretsiz hesap](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz.
- Web geliştirme iş yükü yüklü olan Visual Studio 2019. [Şimdi indir](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Projenize Bing Haber Arama API'si desteği ekleme

1. MyWebApplication adlı yeni bir ASP.NET Core web projesi oluşturun. Tüm varsayılan ayarlarla birlikte **Web Uygulaması (Model-Görünüm-Denetleyici)** proje şablonunu kullanın. Projeye MyWebApplication adını vermek önemlidir; bu nedenle kodu projeye kopyaladığınızda ad alanı eşleştirilir. 

1. **Çözüm Gezgini**’nde **Ekle** > **Bağlı Hizmet** seçeneklerini belirleyin.
   Projenize ekleyebileceğiniz hizmetlerle birlikte Bağlı Hizmet sayfası görüntülenir.

   ![Bağlı Hizmet Ekle menü öğesinin ekran görüntüsü](../media/vs-common/Connected-Service-Menu.PNG)

1. Kullanılabilir hizmetler menüsünde **Uygulamalarınıza Akıllı Arama Özelliği Ekleyin**'i seçin.

   ![Bağlı hizmetler listesinin ekran görüntüsü](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Visual Studio’da oturum açtıysanız ve hesabınızla ilişkili bir Azure aboneliğiniz varsa, aboneliklerinizi içeren bir açılır listenin yer aldığı bir sayfa görüntülenir. Kullanmak istediğiniz aboneliği seçin ve Bing Haber Arama API'si için bir ad belirleyin. İsterseniz **Düzenle**'yi seçerek otomatik olarak oluşturulan adı değiştirebilirsiniz.

   ![Abonelik ve ad alanlarının ekran görüntüsü](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Kaynak grubunu ve fiyatlandırma katmanını seçin.

   ![Kaynak grubu ve fiyatlandırma katmanı alanlarının ekran görüntüsü](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   Fiyatlandırma katmanları hakkında daha fazla bilgiye ihtiyacınız varsa **Fiyatlandırmayı gözden geçir**'i seçin.

1. **Ekle**’yi seçerek, Bağlı Hizmet için destek ekleyin.
   Visual Studio; Bing Haber Arama API'si bağlantısını desteklemek üzere NuGet paketlerini, yapılandırma dosyası girdilerini ve diğer değişiklikleri eklemek için projenizi değiştirir. Çıkış, projenizde olup bitenlerin kaydını gösterir. Aşağıdakine benzer bir şey görmeniz gerekir:

   ```output
   [5/4/2018 12:41:21.084 PM] Adding Intelligent Search to the project.
   [5/4/2018 12:41:21.271 PM] Creating new Intelligent Search...
   [5/4/2018 12:41:24.128 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.ImageSearch' version 1.2.0...
   [5/4/2018 12:41:24.135 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.NewsSearch' version 1.2.0...
   [5/4/2018 12:41:24.154 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.WebSearch' version 1.2.0...
   [5/4/2018 12:41:24.168 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.CustomSearch' version 1.2.0...
   [5/4/2018 12:41:24.187 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.VideoSearch' version 1.2.0...
   [5/4/2018 12:42:07.287 PM] Retrieving keys...
   [5/4/2018 12:42:07.741 PM] Updating appsettings.json setting: 'ServiceKey' = 'c271412f3e4c4e1dacc7c4145fa0572a'
   [5/4/2018 12:42:07.745 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://api.cognitive.microsoft.com/bing/v7.0'
   [5/4/2018 12:42:07.749 PM] Updating appsettings.json setting: 'Name' = 'WebApplicationCore-Search_IntelligentSearch'
   [5/4/2018 12:42:10.217 PM] Successfully added Intelligent Search to the project.
   ```

   appsettings.json dosyası artık aşağıdaki yeni ayarlara sahiptir:

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Bing Haber Arama API'sini kullanarak web sayfasına arama işlevi ekleme

Projenize Bing Haber Arama API'si desteğini eklediniz. API'yi kullanarak web sayfasına akıllı arama özelliği eklemek için aşağıdaki adımları izleyin.

1. *Startup.cs* dosyasının `ConfigureServices` metoduna `IServiceCollection.AddSingleton` çağrısı ekleyin. Bu işlem temel ayarları içeren yapılandırma nesnesini projenizdeki kodun kullanımına sunar.
 
   ```csharp
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddMvc();
           services.AddSingleton<IConfiguration>(Configuration);
       }
   ```


1. **Models** klasörünün adına *BingNewsModel.cs* adlı yeni bir sınıf dosyası ekleyin. Projenize farklı bir ad verdiyseniz MyWebApplication yerine o adı yazın. İçeriğini aşağıdaki kodla değiştirin:
 
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace MyWebApplication.Models
    {
        public class BingNewsModel
        {
            public News SearchResult { get; set; } 
            public string SearchText { get; set; }
        }
    }
    ```

   Bu model, Bing Haber Arama hizmeti çağrısının sonuçlarını depolamak için kullanılır.
 
1. **Denetleyiciler** klasörüne *IntelligentSearchController.cs* adlı yeni bir sınıf dosyası ekleyin. İçeriğini aşağıdaki kodla değiştirin:

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using MyWebApplication.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    using Microsoft.Extensions.Configuration;
    
    namespace MyWebApplication.Controllers
    {
        // A controller to handle News Search requests
        public class IntelligentSearchController : Controller
        {
            private IConfiguration configuration;
  
            // Set up the configuration that contains the keys
            // (from the appsettings.json file)
            // that you will use to access the service  
            public IntelligentSearchController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }

            // Call the Bing News Search API and put the result in the model object.    
            public async Task<IActionResult> BingSearchResult(BingNewsModel model)
            {
                if (!string.IsNullOrWhiteSpace(model.SearchText))
                { 
                    INewsSearchAPI client = this.GetNewsSearchClient(new MyHandler());
                    model.SearchResult = await client.News.SearchAsync(model.SearchText);
                }
                return View(model);
            }
    
            // Forward requests to the Search endpoint to the BingSearchResult method
            [HttpPost("Search")]
            public IActionResult Search(BingNewsModel model)
            {
                return RedirectToAction("BingSearchResult", model);
            }

            // Get the search client object
            private INewsSearchAPI GetNewsSearchClient(DelegatingHandler handler)
            {
                string key =
                   configuration.GetSection("CognitiveServices")["IntelligentSearch:ServiceKey"];
    
                INewsSearchAPI client = new NewsSearchAPI(
                   new ApiKeyServiceClientCredentials(key), handlers: handler);
    
                return client;
            }
        }
    }
   ```

   Bu kodda oluşturucu, anahtarlarınızı içeren yapılandırma nesnesini ayarlar. `Search` yolunun metodu yalnızca `BingSearchResult` işlevinin yeniden yönlendirmesidir. Bu öğe `GetNewsSearchClient` metodunu çağırarak `NewsSearchAPI` istemci nesnesini alır.  `NewsSearchAPI` istemci nesnesinde `SearchAsync` metodu bulunur ve hizmeti çağırarak sonuçları az önce oluşturduğunuz `SearchResult` modeline döndürür. 

1. Yukarıdaki kodda başvurulan `MyHandler` sınıfını ekleyin. Bu işlem arama hizmetine yapılan zaman uyumsuz çağrıyı `DelegatingHandler` temel sınıfına devreder.

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Threading;

    class MyHandler : DelegatingHandler
    {
        protected async override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Call the inner handler.
            var response = await base.SendAsync(request, cancellationToken);
            
            return response;
        }
    }
   ```

1. Arama gönderme ve sonuçları görüntüleme desteği eklemek için **Görünümler** klasöründe **IntelligentSearch** adlı yeni bir klasör oluşturun. Bu yeni klasöre *BingSearchResult.cshtml* adlı görünümü ekleyin. Aşağıdaki kodu kopyalayın:

    ```cshtml
    @using System
    @model MyWebApplication.Models.BingNewsModel
    
    @{
        ViewData["Title"] = "BingSearchResult";
    }
    
    <h2>Search News</h2>
    
    <div class="row">
        <section>
            <form asp-controller="IntelligentSearch" asp-action="Search" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width ="90%">
                    <tr>
                        <td>
                            <input type="text" name="SearchText" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Search</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    <h2>Search Result</h2=
    <table>
    @if (!string.IsNullOrEmpty(Model.SearchText)) {
        foreach (var item in Model.SearchResult.Value) {
        <tr>
            <td rowspan="2" width="90">
               <img src=@item?.Image?.Thumbnail?.ContentUrl width="80" height="80" />
            </td>
            <td><a href=@item.Url>@item.Name</a></td>
        </tr>   
        <tr>
            <td>@item.Description</td>
        </tr>
        <tr height="10">
            <td/><td/>
         </tr>
        } }
     </table>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    ```

1. Web uygulamasını yerel ortamda başlatın, oluşturduğunuz sayfanın URL'sini (/IntelligentSearch/BingSearchResult) girin ve Search (Ara) düğmesini kullanarak bir arama isteği gönderin.

   ![Bing Haber Arama sonuçlarının ekran görüntüsü](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubuna ihtiyacınız kalmadığında silebilirsiniz. Böylece bilişsel hizmet ve ilgili kaynaklar silinir. Kaynak grubunu portal aracılığıyla silmek için:

1. Portalın üst kısmındaki arama kutusuna kaynak grubunuzun adını girin. Silmek istediğiniz kaynak grubunu seçin.
2. **Kaynak grubunu sil**'i seçin.
3. **Kaynak Grubunun Adını Yazın** kutusuna kaynak grubunun adını yazın ve **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bing Haber Arama API'si hakkında daha fazla bilgi edinmek için bkz. [Bing Haber Arama nedir?](index.yml).
