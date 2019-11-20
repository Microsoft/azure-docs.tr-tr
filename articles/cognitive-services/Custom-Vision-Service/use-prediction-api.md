---
title: Görüntüleri sınıflandırıcı ile programlama yoluyla test etmek için tahmin uç noktası kullanın Özel Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Özel Görüntü İşleme sınıflandırıcınızla programlama yoluyla görüntüleri test etmek için API’nin nasıl kullanılacağını öğrenin.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: anroth
ms.openlocfilehash: 50325b75280160a3fefa5b5487df29a25e53bddd
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966936"
---
# <a name="use-your-model-with-the-prediction-api"></a>Tahmin API 'SI ile modelinizi kullanma

Modelinize eğtikten sonra yansımaları, tahmin API uç noktasına göndererek programlama yoluyla test edebilirsiniz.

> [!NOTE]
> Bu belgede, Tahmin API’sine görüntü göndermek için C# kullanımı gösterilmektedir. Daha fazla bilgi ve örnek için bkz. [tahmin API 'si başvurusu](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="publish-your-trained-iteration"></a>Eğitilen yinelelerinizi yayımlayın

[Özel Görüntü İşleme web sayfasından](https://customvision.ai) projenizi ve __Performans__ sekmesini seçin.

Tahmin API 'sine görüntü göndermek için öncelikle, __Yayımla__ ' yı seçerek ve yayımlanan yineleme için bir ad belirterek, yinelemeyi tahmin etmek üzere yayımlamanız gerekir. Bu, modelinizi Özel Görüntü İşleme Azure kaynağınızın tahmin API 'SI için erişilebilir hale getirir.

![Performans sekmesi, Yayınla düğmesini çevreleyen kırmızı bir dikdörtgenle gösterilir.](./media/use-prediction-api/unpublished-iteration.png)

Modelinize başarıyla yayımlandıktan sonra, sol taraftaki kenar çubuğundan yineleinizin yanında "yayımlandı" etiketini görürsünüz ve bu adın adı yinelemenin açıklamasında görüntülenir.

![Performans sekmesi, yayımlanan etiketi çevreleyen kırmızı bir dikdörtgen ve yayımlanan yinelemenin adı ile gösterilir.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>URL ve tahmin anahtarını alma

Modeliniz yayımlandıktan sonra, __tahmin URL 'sini__seçerek gerekli bilgileri alabilirsiniz. Bu, tahmin __URL 'si__ ve __tahmin anahtarı__da dahil olmak üzere, tahmin API 'si kullanımıyla ilgili bilgileri içeren bir iletişim kutusu açar.

![Performans sekmesi, tahmin URL 'SI düğmesini çevreleyen kırmızı bir dikdörtgenle gösterilir.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Performans sekmesi, bir görüntü dosyası ve tahmin anahtarı değeri kullanımı için tahmin URL 'SI değerini çevreleyen kırmızı bir dikdörtgenle gösterilir.](./media/use-prediction-api/prediction-api-info.png)


Bu kılavuzda, bir yerel görüntü kullanacaksınız, bu nedenle geçici bir konuma **bir görüntü dosyanız** varsa, URL 'yi kopyalayın. İlgili __tahmin anahtarı__ değerini de kopyalayın.

## <a name="create-the-application"></a>Uygulama oluşturma

1. Visual Studio 'da yeni C# bir konsol uygulaması oluşturun.

1. __Program.cs__ dosyasının gövdesi olarak aşağıdaki kodu kullanın.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. Aşağıdaki bilgileri değiştirin:
   * `namespace` Alanı projenizin adına ayarlayın.
   * Yer tutucusunu `<Your prediction key>` , daha önce aldığınız anahtar değeriyle değiştirin.
   * Yer tutucusunu `<Your prediction URL>` , daha önce aldığınız URL ile değiştirin.

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı çalıştırdığınızda, konsolunda bir görüntü dosyasının yolunu girmeniz istenir. Görüntü daha sonra tahmin API 'sine gönderilir ve tahmin sonuçları JSON biçimli bir dize olarak döndürülür. Aşağıda örnek bir yanıt verilmiştir.

```json
{
    "Id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "Project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "Iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "Created":"2019-03-20T16:47:31.322Z",
    "Predictions":[
        {"TagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","TagName":"cat", "Probability":1.0},
        {"TagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","TagName":"dog", "Probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, özel görüntü sınıflandırıcıya/algılayıcısının görüntülerini nasıl göndereleceğini ve C# SDK ile programlı bir şekilde yanıt almanızı öğrendiniz. Daha sonra, ile C#uçtan uca senaryoları nasıl tamamlayacağınızı veya farklı bır DIL SDK 'sını kullanmaya başlamanızı öğrenin.

* [Hızlı başlangıç: .NET SDK](csharp-tutorial.md)
* [Hızlı Başlangıç: Python SDK 'Sı](python-tutorial.md)
* [Hızlı Başlangıç: Java SDK 'Sı](java-tutorial.md)
* [Hızlı Başlangıç: Düğüm SDK 'Sı](node-tutorial.md)
* [Hızlı Başlangıç: SDK git](go-tutorial.md)
