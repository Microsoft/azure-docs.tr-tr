---
title: "Öğretici: Visual Studio 'da bağlı hizmetlerle Metin Analizi hizmetine bağlanma"
titleSuffix: Azure Cognitive Services
description: Bu makalede ve beraberindeki makalelerde, Metin Analizi Hizmeti için Visual Studio Bağlı Hizmet özelliğinin kullanımına ilişkin ayrıntılar sağlanmaktadır.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: aahi
ms.openlocfilehash: b094a6917892dfff58c49435de4dc42551be19df
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837201"
---
# <a name="tutorial-connect-to-the-text-analytics-service-with-connected-services-in-visual-studio"></a>Öğretici: Visual Studio 'da bağlı hizmetlerle Metin Analizi hizmetine bağlanma

Metin Analizi Hizmeti’ni kullanarak görsel verileri kategorilere ayırıp işlemenin yanı sıra hizmetlerinizi oluşturmanıza yardımcı olan makine yardımlı görüntü denetimi gerçekleştirmek için zengin bilgileri ayıklayabilirsiniz.

Bu makalede ve beraberindeki makalelerde, Metin Analizi Hizmeti için Visual Studio Bağlı Hizmet özelliğinin kullanımına ilişkin ayrıntılar sağlanmaktadır. Özelliği, bilişsel hizmetler uzantısı yüklü olan Visual Studio 2019 veya üzeri sürümlerde kullanılabilir.

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliği. Bir aboneliğiniz yoksa [ücretsiz hesap](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz.
- Web geliştirme iş yükü yüklü olan Visual Studio 2019. [Şimdi indir](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Metin Analizi Hizmeti için projenize destek ekleme

1. TextAnalyticsDemo adlı yeni bir ASP.NET Core web projesi oluşturun. Tüm varsayılan ayarlarla birlikte Web Uygulaması (Model-Görünüm-Denetleyici) proje şablonunu kullanın. Projeye MyWebApplication adını vermek önemlidir; bu nedenle kodu projeye kopyaladığınızda ad alanı eşleştirilir.  Bu makaledeki örnek, MVC kullanır, ancak Metin Analizi bağlı hizmetini herhangi bir ASP.NET proje türüyle kullanabilirsiniz.

1. **Çözüm Gezgini**’nde **Bağlı Hizmet** öğesine çift tıklayın.
   Projenize ekleyebileceğiniz hizmetlerle birlikte Bağlı Hizmet sayfası görüntülenir.

   ![Çözüm Gezgini’nde Bağlı Hizmet’in ekran görüntüsü](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. Kullanılabilir hizmetler menüsünde **Metin Analizi ile Yaklaşımı Değerlendirme** seçeneğini belirleyin.

   ![Bağlı Hizmetler ekranının ekran görüntüsü](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Visual Studio’da oturum açtıysanız ve hesabınızla ilişkili bir Azure aboneliğiniz varsa, aboneliklerinizi içeren bir açılır listenin yer aldığı bir sayfa görüntülenir.

   ![Metin Analizi Bağlı Hizmeti ekranının ekran görüntüsü](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. Kullanmak istediğiniz aboneliği seçin ve sonra Metin Analizi Hizmeti için bir ad seçin veya otomatik olarak oluşturulan adı değiştirmek için **Düzenle** bağlantısını seçin, kaynak grubunu ve Fiyatlandırma Katmanını seçin.

   ![Kaynak grubu ve fiyatlandırma katmanı alanlarının ekran görüntüsü](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   Fiyatlandırma katmanları ile ilgili ayrıntılar için bağlantıyı izleyin.

1. **Ekle**’yi seçerek, Bağlı Hizmet için destek ekleyin.
   Visual Studio; Metin Analizi Hizmeti bağlantısını desteklemek üzere NuGet paketlerini, yapılandırma dosyası girdilerini ve diğer değişiklikleri eklemek için projenizi değiştirir. **Çıkış Penceresi**’nde projenizde olup bitenlerin günlüğü gösterilir. Çıktın aşağıdaki gibi görünmesi gerekir:

   ```output
    [6/1/2018 3:04:02.347 PM] Adding Text Analytics to the project.
    [6/1/2018 3:04:02.906 PM] Creating new Text Analytics...
    [6/1/2018 3:04:06.314 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Language' version 1.0.0-preview...
    [6/1/2018 3:04:56.759 PM] Retrieving keys...
    [6/1/2018 3:04:57.822 PM] Updating appsettings.json setting: 'ServiceKey' = '<service key>'
    [6/1/2018 3:04:57.827 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.1'
    [6/1/2018 3:04:57.832 PM] Updating appsettings.json setting: 'Name' = 'TextAnalyticsDemo'
    [6/1/2018 3:05:01.840 PM] Successfully added Text Analytics to the project.
    ```
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>Metin örneğinin dilini algılamak için Metin Analizi Hizmeti’ni kullanın.

1. Startup.cs’de deyimleri kullanarak aşağıdakileri ekleyin.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. Bir yapılandırma alanı ekleyin ve Başlangıç sınıfında yapılandırma alanını başlatan bir oluşturucu ekleyerek programınızda Yapılandırmayı etkinleştirin.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. `DemoTextAnalyzeController` adlı *denetleyiciler* klasörüne bir sınıf dosyası ekleyin ve içeriğini aşağıdaki kodla değiştirin:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Extensions.Configuration;
    using TextAnalyticsDemo.Models;
    
    namespace TextAnalyticsDemo.Controllers
    {
        public class DemoTextAnalyzeController : Controller
        {
            private IConfiguration configuration;
    
            public DemoTextAnalyzeController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }
    
            public IActionResult TextAnalyzeResult(TextAnalyzeModel model)
            {
    
                if (!string.IsNullOrWhiteSpace(model.TextStr))
                {
                    ITextAnalyticsAPI client = this.GetTextAnalyzeClient(new MyHandler());
                    model.AnalyzeResult = client.DetectLanguage(
                        new BatchInput(
                            new List<Input>()
                            {
                                new Input("id",model.TextStr)
                            }));
                }
                return View(model);
            }
    
            [HttpPost("Analyze")]
            public IActionResult Analyze(TextAnalyzeModel model)
            {
                return RedirectToAction("TextAnalyzeResult", model);
            }
    
            // Using the ServiceKey from the configuration file,
            // get an instance of the Text Analytics client.
            private ITextAnalyticsAPI GetTextAnalyzeClient(DelegatingHandler handler)
            {
                string key = configuration.GetSection("CognitiveServices")["TextAnalytics:ServiceKey"];
    
                ITextAnalyticsAPI client = new TextAnalyticsAPI( handlers: handler);
                client.SubscriptionKey = key;
                client.AzureRegion = AzureRegions.Westus;
    
                return client;
            }
        }
    }
    ```
    
    Kod, Metin Analizi API'si çağrıları yapmak için istemci nesnesini ve belirli bir metinde DetectLanguage öğesini çağıran bir istek işleyicisini almak için `GetTextAnalyzeClient` içerir.

1. Önceki kod tarafından kullanılan MyHandler yardımcı sınıfını ekleyin.

    ```csharp
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

1. *Modeller* klasöründe, model için bir sınıf ekleyin.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    
    namespace Demo.Models
    {
        public class TextAnalyzeModel
        {
            public string TextStr { get; set; }
    
            public LanguageBatchResult AnalyzeResult { get; set; }
    
            public SentimentBatchResult AnalyzeResult2 { get; set; }
        }
    }
    ```

1. Çözümlenmiş metni, belirlenen dili ve analizdeki güvenirlik düzeyini temsil eden puanı göstermek için bir Görünüm ekleyin. Bunu yapmak için **Görünümler** klasörüne sağ tıklayın, **Ekle**’yi ve sonra **Görüntüle**’yi seçin. Görüntülenen iletişim kutusunda _TextAnalyzeResult_ adını sağlayın, _Görünümler_ klasörüne **TextAnalyzeResult.cshtml** adlı yeni bir dosya eklemek için varsayılanları kabul edin ve buna aşağıdaki içerikleri kopyalayın:
    
    ```cshtml
    @using System
    @model Demo.Models.TextAnalyzeModel
    
    @{
        ViewData["Title"] = "TextAnalyzeResult";
    }
    
    <h2>Text Language</h2>
    
    <div class="row">
        <section>
            <form asp-controller="DemoTextAnalyze" asp-action="Analyze" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width="90%">
                    <tr>
                        <td>
                            <input type="text" name="TextStr" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Analyze</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    
    <h2>Result</h2>
    <div>
        <dl class="dl-horizontal">
            <dt>
                Text :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.TextStr)
            </dd>
            <dt>
                Language Name :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Name)
            </dd>
            <dt>
                Score :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Score)
            </dd>
        </dl>
    </div>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    
    ```
 
1. Örneği yerel olarak derleyin ve çalıştırın. Bir metin girin ve Metin Analizi’nin hangi dili algıladığına bakın.
   
## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse kaynak grubunu silin. Böylece bilişsel hizmet ve ilgili kaynaklar silinir. Kaynak grubunu portal aracılığıyla silmek için:

1. Portalın üst kısmındaki Arama kutusuna kaynak grubunuzun adını girin. Bu öğreticide kullanılan kaynak grubunu arama sonuçlarında gördüğünüzde seçin.
2. **Kaynak grubunu sil**'i seçin.
3. **KAYNAK GRUBU ADINI YAZIN:** kutusuna kaynak grubunun adını yazın ve **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

[Metin Analizi Hizmet Belgeleri](index.yml)’ni okuyarak Metin Analizi Hizmeti hakkında daha fazla bilgi edinin.
