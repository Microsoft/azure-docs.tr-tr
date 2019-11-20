---
title: "Örnek: Gerçek zamanlı video analizi-Yüz Tanıma API'si"
titleSuffix: Azure Cognitive Services
description: Canlı video akışından alınan karelerde gerçek zamanlıya yakın analiz gerçekleştirmek için Yüz Tanıma API’sini kullanın.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: e2166354fb45d24e117156e917f4da726ee8406f
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114339"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Örnek: Gerçek Zamanlı Videoları Analiz Etme

Bu kılavuzda, canlı video akışından alınan karelerde nasıl gerçek zamanlıya yakın analiz gerçekleştirileceği gösterilmektedir. Böyle bir sistemdeki temel bileşenler şunlardır:

- Video kaynağından kareleri alma
- Hangi karelerin analiz edileceğini seçme
- Bu kareleri API’ye gönderme
- API çağrısından döndürülen her analiz sonucunu kullanma

Bu örnekler C# dilinde yazılmıştır ve kod, buradaki GitHub’da bulunabilir: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Yaklaşım

Video akışlarında gerçek zamanlıya yakın analiz çalıştırma sorununu çözmenin birçok yolu vardır. Gelişmişlik düzeyini artırma konusunda üç yaklaşımı açıklayarak başlayacağız.

### <a name="a-simple-approach"></a>Basit Bir Yaklaşım

Neredeyse gerçek zamanlı analiz sistemi için en basit tasarım sonsuz bir döngüdür, her yineleme bir çerçeveyi kendisi analiz eder ve sonucu kullanır:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Analizimiz hafif bir istemci algoritmasından oluştuysa bu yaklaşım uygun olacaktır. Ancak, bulutta analiz gerçekleştiğinde, ilgili gecikme bir API çağrısının birkaç saniye sürebileceği anlamına gelir. Bu süre boyunca görüntüleri yakalıyoruz ve iş parçacığumuz aslında hiçbir şey yapmaz. Maksimum kare hızımız, API çağrılarının gecikme süresiyle sınırlıdır.

### <a name="parallelizing-api-calls"></a>API Çağrılarını Paralelleştirme

Basit bir tek iş parçacıklı döngü, hafif istemci tarafı algoritma için mantıklı olsa da, bulut API çağrılarında yer alan gecikme süresine tam uymaz. Bu sorunun çözümü, uzun süre çalıştırılan API çağrılarının kare ele geçirme ile paralel şekilde yürütülmesine izin verilmesidir. C# dilinde, Görev tabanlı paralellik kullanarak bunu elde edebiliriz; örneğin:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () => 
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

Bu kod her bir analizi ayrı bir Görevde başlatır ve biz yeni kareleri ele geçirirken görev arka planda çalıştırılabilir. Bu yöntemde, bir API çağrısının döndürülmesini beklerken ana iş parçacığını engelliyoruz, ancak sunulan basit sürümün bazı garantilerini kaybettik. Paralel olarak birden çok API çağrısı gerçekleşebilir ve sonuçlar yanlış sırada döndürülür. Bu Ayrıca, işlev iş parçacığı açısından güvenli değilse tehlikeli olabilecek çok sayıda iş parçacığının ConsumeResult () işlevini aynı anda girmesine neden olabilir. Son olarak bu basit kod, oluşturulan Görevleri takip etmez, bu nedenle özel durumlar sessizce kaybolur. Bu nedenle, son adım analiz görevlerini izleyen bir "tüketici" iş parçacığı eklemektir, özel durumlar yükseltir, uzun süreli görevleri sonlandırır ve sonuçların doğru sırada tüketildiğinden emin olunması gerekir.

### <a name="a-producer-consumer-design"></a>Üretici-Tüketici Tasarımı

Son “üretici-tüketici” sistemimizde, önceki sonsuz döngümüze benzer görünen bir üretici iş parçacığımız vardır. Ancak üretici, kullanılabilir olduğu anda analiz sonuçlarını kullanmak yerine görevleri takip etmek için bir kuyruğa koyar.

```csharp
// Queue that will contain the API call tasks. 
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();
     
// Producer thread. 
while (true)
{
    // Grab a frame. 
    Frame f = GrabFrame();
 
    // Decide whether to analyze the frame. 
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread. 
        var analysisTask = Task.Run(async () => 
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }
        
        // Push the task onto the queue. 
        taskQueue.Add(analysisTask);
    }
}
```

Ayrıca, görevleri sıra dışında alan bir tüketici iş parçacığı sunuyoruz, bunların tamamlanmasını bekler ve sonucu görüntüler ya da oluşturulan özel durumu oluşturur. Kuyruğu kullanarak, sistemin maksimum kare hızını sınırlamadan sonuçların birer birer doğru sırayla kullanılmasını garanti edebiliriz.

```csharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
    var output = await analysisTask;
     
    // Consume the exception or result. 
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>Çözümü uygulama

### <a name="getting-started"></a>Başlarken

Uygulamanızı mümkün olduğunca hızlı bir şekilde çalışır duruma getirmek için yukarıda açıklanan sistemin esnek bir uygulamasını kullanacaksınız. Koda erişmek için [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis) sayfasına gidin.

Kitaplık, bir Web kamerasından video çerçevelerini işlemek için yukarıda ele alınan üretici-tüketici sistemini uygulayan Framekavrayıcı sınıfını içerir. Kullanıcı, API çağrısının tam biçimini belirtebilir ve sınıf, çağıran kodun yeni bir çerçeve edinildiği veya yeni bir analiz sonucunun kullanılabildiği bir şekilde bilmesini sağlamak için olayları kullanır.

Bazı olasılıkları göstermek için, kitaplığı kullanan iki örnek uygulama vardır. Birincisi basit bir konsol uygulamasıdır ve bunun basitleştirilmiş bir sürümü aşağıda verilmiştir. Varsayılan web kamerasından kareleri ele geçirir ve yüz algılama için Yüz Tanıma API’sine bunları gönderir.

```csharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face API Client. Insert your Face API key here.
            private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

İkinci örnek uygulama biraz daha ilginçtir ve video karelerinde hangi API’yi çağıracağınızı seçmenize olanak sağlar. Sol kısımda uygulama, canlı videonun bir önizlemesini gösterir, sağ kısımdaysa ilgili karenin üzerine yerleştirilmiş şekilde en son API sonucunu gösterir.

Çoğu modda, soldaki canlı video ile sağdaki görselleştirilmiş analiz arasında görünür bir gecikme olacaktır. Bu gecikme, API çağrısı yapmak için geçen süredir. Tek bir istisna, bilişsel hizmetler 'e herhangi bir görüntü göndermeden önce, istemci bilgisayarda OpenCV 'yi kullanarak yüz algılamayı gerçekleştiren "EmotionsWithClientFaceDetect" modudur. Bu şekilde, algılanan yüzü hemen görselleştirebiliriz ve sonra API çağrısı geri döndüğünde emokları güncelleştirebilirsiniz. Bu, istemcinin bazı basit işlemler gerçekleştirebileceği ve Bilişsel Hizmetler API'si gerektiğinde daha gelişmiş analizler ile genişlebildiği bir "hibrit" yaklaşımına bir örnektir.

![Video analiz etme](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Kod temelinizle tümleştirme

Bu örneği kullanmaya başlamak için şu adımları izleyin:

1. [Abonelikler](https://azure.microsoft.com/try/cognitive-services/)’den Görüntü İşleme API’leri için API anahtarlarını alın. Video karesi analizi için geçerli API’ler şunlardır:
    - [Görüntü İşleme API’si](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Duygu Tanıma API'si](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Yüz Tanıma API’si](https://docs.microsoft.com/azure/cognitive-services/face/overview)

2. [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub deposunu kopyalayın

3. Visual Studio 2015 ' de örneği açın ve örnek uygulamaları derleyin ve çalıştırın:
    - BasicConsoleSample için Yüz Tanıma API'si anahtarı doğrudan [basicconsolesample/program. cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs)içinde sabit olarak kodlanmıştır.
    - LiveCameraSample için anahtarlar, uygulamanın Ayarlar bölmesine girilmelidir. Oturumlarda kullanıcı verileri olarak kalıcı duruma getirilir.
        

Tümleştirmeye hazırsanız, **kendi projelerinizden VideoFrameAnalyzer kitaplığı 'na başvurun.** 

## <a name="summary"></a>Özet

Bu kılavuzda, yüz, Görüntü İşleme ve Emotion API 'Leri kullanarak canlı video akışları üzerinde neredeyse gerçek zamanlı analizler çalıştırmayı ve örnek kodumuzu kullanmaya başlamak için nasıl kullanılacağını öğrendiniz. Azure bilişsel [Hizmetler kayıt sayfasında](https://azure.microsoft.com/try/cognitive-services/), ücretsiz API anahtarlarıyla uygulamanızı oluşturmaya başlayabilirsiniz. 

 [UserVoice sitemizdeki](https://cognitive.uservoice.com/) [GitHub deposunda](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) veya daha geniş API geri bildirimleri için geri bildirim ve öneriler sağlamayı ücretsiz olarak hissetmekten çekinmeyin.

## <a name="related-topics"></a>İlgili Konular
- [Görüntüdeki Yüzleri Belirleme](HowtoIdentifyFacesinImage.md)
- [Görüntüdeki Yüzleri Algılama](HowtoDetectFacesinImage.md)
