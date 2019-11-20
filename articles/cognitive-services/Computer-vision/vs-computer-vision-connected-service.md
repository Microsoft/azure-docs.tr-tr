---
title: Visual Studio bağlı hizmeti-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Visual Studio bağlı hizmet özelliğini kullanarak bir ASP.NET Core Web uygulamasından Görüntü İşleme API'si bağlama.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ghogen
ms.custom: seodec18
ms.openlocfilehash: e4308f98b6e547acd4adfb62ab78c0517247d905
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177084"
---
# <a name="use-connected-services-in-visual-studio-to-connect-to-the-computer-vision-api"></a>Görüntü İşleme API'si bağlanmak için Visual Studio 'da bağlı hizmetleri kullanın

Bu makale ve yardımcı makaleler, bilişsel hizmetler Görüntü İşleme API'si için Visual Studio bağlı hizmet özelliğini kullanma hakkında ayrıntılı bilgi sağlar. Özelliği, bilişsel hizmetler uzantısı yüklü olan Visual Studio 2017 15,7 veya üzeri sürümlerde kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

- Bir Azure aboneliği. Bir [hesabınız yoksa ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/)için kaydolabilirsiniz.
- **Web geliştirme** iş yükü yüklüyken Visual Studio 2017 sürüm 15,7 veya üzeri. [Şimdi indirin](https://visualstudio.microsoft.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-cognitive-services-computer-vision-api"></a>Bilişsel hizmetler için projenize destek ekleyin Görüntü İşleme API'si

1. Yeni bir ASP.NET Core Web projesi oluşturun. Boş proje şablonunu kullanın. 

1. **Çözüm Gezgini**, @no__t 2 ' ye**bağlı hizmet** **Ekle**' yi seçin.
   Bağlı hizmet sayfası, projenize ekleyebileceğiniz hizmetlerle birlikte görüntülenir.

   ![Visual Studio projesinde menü sağ tıklama: > bağlı hizmet ekle](../media/vs-common/Connected-Service-Menu.PNG)

1. Kullanılabilir hizmetler menüsünde bilişsel **hizmetler görüntü işleme API'si**' yi seçin.

   ![Bağlı hizmetler menüsü: görüntüleri çözümle... Seviyelendirilmiş](./media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-0.PNG)

   Visual Studio 'da oturum açtıysanız ve hesabınızla ilişkili bir Azure aboneliğiniz varsa, aboneliklerinizle bir açılan liste içeren bir sayfa görüntülenir.

   ![Abonelik açılan menüsü vurgulanmış Görüntü İşleme API'si pencere](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-1.PNG)

1. Kullanmak istediğiniz aboneliği seçin ve ardından Görüntü İşleme API'si için bir ad seçin veya otomatik olarak oluşturulan adı değiştirmek için Düzenle bağlantısını seçin, kaynak grubunu ve fiyatlandırma katmanını seçin.

   ![Bağlı hizmet ayrıntılarını Düzenle](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-2.PNG)

   Fiyatlandırma katmanlarında Ayrıntılar için bağlantıyı izleyin.

1. Bağlı hizmet için desteklenen eklemek için Ekle ' yi seçin.
   Visual Studio, NuGet paketlerini, yapılandırma dosyası girişlerini ve Görüntü İşleme API'si bir bağlantıyı destekleyecek diğer değişiklikleri eklemek için projenizi değiştirir. Çıkış Penceresi, projenizde neler olduğunu gösteren günlüğü gösterir. Aşağıdakine benzer bir şey görmeniz gerekir:

   ```output
   [4/26/2018 5:15:31.664 PM] Adding Computer Vision API to the project.
   [4/26/2018 5:15:32.084 PM] Creating new ComputerVision...
   [4/26/2018 5:15:32.153 PM] Creating new Resource Group...
   [4/26/2018 5:15:40.286 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Vision.ComputerVision' version 2.1.0.
   [4/26/2018 5:15:44.117 PM] Retrieving keys...
   [4/26/2018 5:15:45.602 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceKey=<service key>
   [4/26/2018 5:15:45.606 PM] Changing appsettings.json setting: ComputerVisionAPI_ServiceEndPoint=https://australiaeast.api.cognitive.microsoft.com/vision/v2.1
   [4/26/2018 5:15:45.609 PM] Changing appsettings.json setting: ComputerVisionAPI_Name=WebApplication-Core-ComputerVision_ComputerVisionAPI
   [4/26/2018 5:15:46.747 PM] Successfully added Computer Vision API to the project.
   ```
 
## <a name="use-the-computer-vision-api-to-detect-attributes-of-an-image"></a>Bir görüntünün özniteliklerini algılamak için Görüntü İşleme API'si kullanma

1. Startup.cs ' ye aşağıdaki using deyimlerini ekleyin.
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Bir yapılandırma alanı ekleyin ve programınızda yapılandırmayı etkinleştirmek için `Startup` sınıfındaki yapılandırma alanını Başlatan bir Oluşturucu ekleyin.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Projenizdeki Wwwroot klasöründe bir görüntüler klasörü ekleyin ve Wwwroot klasörünüze bir görüntü dosyası ekleyin. Örnek olarak, bu [görüntü işleme API'si sayfasındaki](https://azure.microsoft.com/services/cognitive-services/computer-vision/)görüntülerden birini kullanabilirsiniz. Görüntülerden birine sağ tıklayın, yerel sabit sürücünüze kaydedin, ardından Çözüm Gezgini, görüntüler klasörüne sağ tıklayın ve sonra da @no__t**var olan öğe** **Ekle**' yi seçerek projenize ekleyin. Projeniz Çözüm Gezgini şöyle görünmelidir: 
  
   ![Bir resim dosyası seçiliyken Çözüm Gezgini görünümünün ekran görüntüsü](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-3.PNG) 

1. Görüntü dosyasına sağ tıklayın, Özellikler ' i seçin ve **daha sonra Kopyala**' yı seçin. 

   ![görüntü özellikleri penceresi; Daha yeniyse, çıkış dizinine Kopyala ayarla](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-5.PNG) 
 
1. Görüntü İşleme API'si erişmek ve bir görüntüyü sınamak için Configure metodunu aşağıdaki kodla değiştirin.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        // TODO: Change this to your image's path on your site. 
        string imagePath = @"images/subway.png";

        // Enable static files such as image files. 
        app.UseStaticFiles();

        string visionApiKey = this.configuration["ComputerVisionAPI_ServiceKey"];
        string visionApiEndPoint = this.configuration["ComputerVisionAPI_ServiceEndPoint"];

        HttpClient client = new HttpClient();

        // Request headers.
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", visionApiKey);

        // Request parameters. A third optional parameter is "details".
        string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

        // Assemble the URI for the REST API Call.
        string uri = visionApiEndPoint + "/analyze" + "?" + requestParameters;

        HttpResponseMessage response;

        // Request body. Posts an image you've added to your site's images folder. 
        var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
        byte[] byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

        string contentString = string.Empty;
        using (ByteArrayContent content = new ByteArrayContent(byteData))
        {
            // This example uses content type "application/octet-stream".
            // The other content types you can use are "application/json" and "multipart/form-data".
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

            // Execute the REST API call.
            response = client.PostAsync(uri, content).Result;

            // Get the JSON response.
            contentString = response.Content.ReadAsStringAsync().Result;
        }

        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("<h1>Cognitive Services Demo</h1>");
            await context.Response.WriteAsync($"<p><b>Test Image:</b></p>");
            await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
            await context.Response.WriteAsync($"<p><b>Computer Vision API results:</b></p>");
            await context.Response.WriteAsync("<p>");
            await context.Response.WriteAsync(JsonPrettyPrint(contentString));
            await context.Response.WriteAsync("<p>");
        });
    }
   ```

    Buradaki kod, Görüntü İşleme REST API çağrısı için ikili içerik olarak URI ve görüntüyle bir HTTP isteği oluşturur.

1. Getımageasbytearray ve JsonPrettyPrint yardımcı işlevlerini ekleyin.

   ```csharp
    /// <summary>
    /// Returns the contents of the specified file as a byte array.
    /// </summary>
    /// <param name="imageFilePath">The image file to read.</param>
    /// <returns>The byte array of the image data.</returns>
    static byte[] GetImageAsByteArray(string imageFilePath)
    {
        FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
        BinaryReader binaryReader = new BinaryReader(fileStream);
        return binaryReader.ReadBytes((int)fileStream.Length);
    }

    /// <summary>
    /// Formats the given JSON string by adding line breaks and indents.
    /// </summary>
    /// <param name="json">The raw JSON string to format.</param>
    /// <returns>The formatted JSON string.</returns>
    static string JsonPrettyPrint(string json)
    {
        if (string.IsNullOrEmpty(json))
            return string.Empty;

        json = json.Replace(Environment.NewLine, "").Replace("\t", "");

        string INDENT_STRING = "    ";
        var indent = 0;
        var quoted = false;
        var sb = new StringBuilder();
        for (var i = 0; i < json.Length; i++)
        {
            var ch = json[i];
            switch (ch)
            {
                case '{':
                case '[':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case '}':
                case ']':
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    sb.Append(ch);
                    break;
                case '"':
                    sb.Append(ch);
                    bool escaped = false;
                    var index = i;
                    while (index > 0 && json[--index] == '\\')
                        escaped = !escaped;
                    if (!escaped)
                        quoted = !quoted;
                    break;
                case ',':
                    sb.Append(ch);
                    if (!quoted)
                    {
                        sb.AppendLine();
                    }
                    break;
                case ':':
                    sb.Append(ch);
                    if (!quoted)
                        sb.Append(" ");
                    break;
                default:
                    sb.Append(ch);
                    break;
            }
        }
        return sb.ToString();
    }
   ```

1. Web uygulamasını çalıştırın ve görüntinizdeki Görüntü İşleme API'si bulunan öğeleri görüntüleyin.

   ![Görüntü İşleme API'si resim ve biçimlendirilen sonuçlar](media/vs-computer-vision-connected-service/Cog-Vision-Connected-Service-4.PNG)  

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu silin. Bu, bilişsel hizmeti ve ilgili kaynakları siler. Portal üzerinden kaynak grubunu silmek için:

1. Portalın üst kısmındaki arama kutusuna kaynak grubunuzun adını girin. Bu hızlı başlangıçta arama sonuçlarında kullanılan kaynak grubunu gördüğünüzde, seçin.
2. **Kaynak grubunu sil**' i seçin.
3. **Kaynak grubu adını yazın:** kutusuna kaynak grubunun adını yazın ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

[Görüntü işleme API'si belgelerini](Home.md)okuyarak görüntü işleme API'si hakkında daha fazla bilgi edinin.
