---
title: "Öğretici: Yüz tanıma API'siC#"
titleSuffix: Azure Cognitive Services
description: Bilişsel hizmetler yüz tanıma API'si bir resimdeki yüz özelliklerinin algılamak için kullanan bir Windows uygulaması oluşturun.
services: cognitive-services
author: ghogen
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: ghogen
ms.openlocfilehash: 7907a79289149d9e165dd6df0c09bee596e624e2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606808"
---
# <a name="connecting-to-cognitive-services-face-api-by-using-connected-services-in-visual-studio"></a>Visual Studio’da Bağlı Hizmetler’i kullanarak Bilişsel Hizmetler Yüz Tanıma API’sine bağlanma

Bilişsel Hizmetler Yüz Tanıma API’sini kullanarak, fotoğraflardaki yüzleri algılayabilir, analiz edebilir, düzenleyebilir ve etiketleyebilirsiniz.

Bu makalede ve beraberindeki destek makalelerinde, Bilişsel Hizmetler Yüz Tanıma API’si için Visual Studio Bağlı Hizmet özelliğinin kullanımına ilişkin ayrıntılar sağlanmaktadır. Özellik, Bilişsel Hizmetler uzantısının yüklendiği Visual Studio 2017 15.7 ve sonraki sürümlerde mevcuttur.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bir aboneliğiniz yoksa [ücretsiz hesap](https://azure.microsoft.com/pricing/free-trial/) için kaydolabilirsiniz.
- Visual Studio 2017 sürüm 15.7 veya üzeri sürümler **Web geliştirme** iş yükü yüklenmiş. [Şimdi indir](https://www.visualstudio.com/downloads/).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="create-a-project-and-add-support-for-cognitive-services-face-api"></a>Proje oluşturma ve Bilişsel hizmetler Yüz Tanıma API’si için destek ekleme

1. Yeni bir ASP.NET Core web projesi oluşturun. Boş proje şablonunu kullanın. 

1. **Çözüm Gezgini**’nde **Ekle** > **Bağlı Hizmet** seçeneklerini belirleyin.
   Projenize ekleyebileceğiniz hizmetlerle birlikte Bağlı Hizmet sayfası görüntülenir.

   ![Bağlı Hizmet Ekle menü öğesi](./media/vs-face-connected-service/Connected-Service-Menu.PNG)

1. Kullanılabilir hizmetler menüsünde **Bilişsel Hizmetler Yüz Tanıma API**’sini seçin.

   ![Bağlanılacak hizmeti seçme](./media/vs-face-connected-service/Cog-Face-Connected-Service-0.PNG)

   Visual Studio’da oturum açtıysanız ve hesabınızla ilişkili bir Azure aboneliğiniz varsa, aboneliklerinizi içeren bir açılır listenin yer aldığı bir sayfa görüntülenir.

   ![Aboneliğinizi seçme](media/vs-face-connected-service/Cog-Face-Connected-Service-1.PNG)

1. Kullanmak istediğiniz aboneliği seçin ve sonra Yüz Tanıma API’si için bir ad seçin veya otomatik olarak oluşturulan adı değiştirmek için Düzenle bağlantısını seçin, kaynak grubunu ve Fiyatlandırma Katmanını seçin.

   ![Bağlı hizmet ayrıntılarını düzenleme](media/vs-face-connected-service/Cog-Face-Connected-Service-2.PNG)

   Fiyatlandırma katmanları ile ilgili ayrıntılar için bağlantıyı izleyin.

1. Ekle’yi seçerek, Bağlı Hizmet için destek ekleyin.
   Visual Studio; Yüz Tanıma API’si bağlantısını desteklemek üzere NuGet paketlerini, yapılandırma dosyası girdilerini ve diğer değişiklikleri eklemek için projenizi değiştirir.

## <a name="use-the-face-api-to-detect-attributes-of-faces-in-an-image"></a>Bir görüntüdeki yüzlerin özniteliklerini algılamak için Yüz Tanıma API’sini kullanma

1. Startup.cs’de deyimleri kullanarak aşağıdakileri ekleyin:
 
   ```csharp
   using System.IO;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   using System.Net.Http;
   using System.Net.Http.Headers;
   ```
 
1. Bir yapılandırma alanı ekleyin ve Başlangıç sınıfında yapılandırma alanını başlatan bir oluşturucu ekleyerek programınızda Yapılandırmayı etkinleştirin.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Projenizdeki wwwroot klasörüne bir görüntüler klasörü ekleyin ve wwwroot klasörünüz için bir görüntü dosyası ekleyin. Örneğin, bu [Yüz Tanıma API’si sayfasındaki](https://azure.microsoft.com/services/cognitive-services/face/) görüntülerden birini kullanabilirsiniz. Sağ görüntülerden birini tıklayın, Çözüm Gezgini'nde sonra yerel sabit sürücünüze kaydedin, görüntüleri klasörü sağ tıklatın ve seçin **Ekle** > **var olan öğe** projenize eklemek için. Projeniz Çözüm Gezgini’nde aşağıdakine benzer şekilde görünmelidir:
 
   ![görüntü dosyasını içeren görüntüler klasörü](media/vs-face-connected-service/Cog-Face-Connected-Service-6.PNG)

1. Görüntü dosyasına sağ tıklayın, Özellikler’i seçin ve ardından **Yeniyse kopyala** seçeneğini belirleyin.

   ![Yeniyse kopyala](media/vs-face-connected-service/Cog-Face-Connected-Service-5.PNG)
 
1. Yüz Tanıma API’sine erişmek ve bir görüntüyü test etmek için aşağıdaki kod ile Yapılandırma yöntemini değiştirin. imagePath dizesini, yüzünüzün görüntüsü için doğru yol ve dosya adı ile değiştirin.

   ```csharp
    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            // TODO: Change this to your image's path on your site.
            string imagePath = @"images/face1.png";

            // Enable static files such as image files.
            app.UseStaticFiles();

            string faceApiKey = this.configuration["FaceAPI_ServiceKey"];
            string faceApiEndPoint = this.configuration["FaceAPI_ServiceEndPoint"];

            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", faceApiKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";

            // Assemble the URI for the REST API Call.
            string uri = faceApiEndPoint + "/detect?" + requestParameters;

            // Request body. Posts an image you've added to your site's images folder.
            var fileInfo = env.WebRootFileProvider.GetFileInfo(imagePath);
            var byteData = GetImageAsByteArray(fileInfo.PhysicalPath);

            string contentStringFace = string.Empty;
            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                var response = client.PostAsync(uri, content).Result;

                // Get the JSON response.
                contentStringFace = response.Content.ReadAsStringAsync().Result;
            }

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.Run(async (context) =>
            {
                await context.Response.WriteAsync($"<p><b>Face Image:</b></p>");
                await context.Response.WriteAsync($"<div><img src=\"" + imagePath + "\" /></div>");
                await context.Response.WriteAsync($"<p><b>Face detection API results:</b></p>");
                await context.Response.WriteAsync("<p>");
                await context.Response.WriteAsync(JsonPrettyPrint(contentStringFace));
                await context.Response.WriteAsync("<p>");
            });
        }
   ```
    Bu adımda kod bağlı hizmet eklendiğinde, eklenen anahtar kullanılarak yüz REST API çağrısı ile bir HTTP isteği oluşturur.

1. GetImageAsByteArray ve JsonPrettyPrint yardımcı işlevlerini ekleyin.

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

1. Web uygulamasını çalıştırın ve görüntüde hangi Yüz Tanıma API’sinin bulunduğunu görün.
 
   ![Yüz Tanıma API’si görüntü ve biçimlendirilmiş sonuçlar](media/vs-face-connected-service/Cog-Face-Connected-Service-4.PNG)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse kaynak grubunu silin. Böylece bilişsel hizmet ve ilgili kaynaklar silinir. Kaynak grubunu portal aracılığıyla silmek için:

1. Portalın üst kısmındaki Arama kutusuna kaynak grubunuzun adını girin. Bu hızlı başlangıçta kullanılan kaynak grubunu arama sonuçlarında gördüğünüzde seçin.
1. **Kaynak grubunu sil**'i seçin.
1. **KAYNAK GRUBU ADINI YAZIN:** kutusuna kaynak grubunun adını yazın ve **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

[Yüz Tanıma API’si Belgeleri](Overview.md)’ni okuyarak Yüz Tanıma API’si hakkında daha fazla bilgi edinin.
