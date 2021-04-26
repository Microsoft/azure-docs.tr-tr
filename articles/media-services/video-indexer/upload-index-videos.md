---
title: Video Indexer ile videoları karşıya yükleme ve dizinleme
titleSuffix: Azure Media Services
description: Bu konuda, API'lerı kullanarak Video Indexer ile videolarınızı karşıya yükleme ve dizinleme gösterilmektedir.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/04/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 90fca4342b1fe04adef97a1a4c1c2166ca7ec51e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532485"
---
# <a name="upload-and-index-your-videos"></a>Videolarınızı karşıya yükleme ve dizinleme  

Videonuz karşıya yüklendikten sonra Video Indexer (isteğe bağlı olarak) videoyu kodlar (makalede ele alınmıştır). Video Indexer hesabınızı oluştururken ücretsiz bir deneme hesabı (belirli sayıda ücretsiz dizin oluşturma dakikası elde edersiniz) veya ücretli bir seçenek (kota sınırlaması olmaz) arasından seçim yapabilirsiniz. Ücretsiz deneme kullanıldığında Video Indexer, web sitesi kullanıcılarına 600 dakikaya kadar ve API kullanıcılarına ise 2400 dakikaya kadar ücretsiz dizin oluşturma olanağı sunar. Ücretli seçenek kullanıldığında [Azure aboneliğinize ve bir Azure Media Services hesabına bağlı](connect-to-azure.md) bir Video Indexer hesabı oluşturulur. Dizin oluşturma için ödeme yaparsınız. daha fazla bilgi için bkz. [Media Services fiyatlandırması](https://azure.microsoft.com/pricing/details/media-services/).

Video Indexer API'siyle videoları karşıya yüklerken aşağıdaki karşıya yükleme seçeneklerini kullanabilirsiniz: 

* Videonuzu bir URL'den karşıya yükleyin (tercih edilir).
* video dosyasını istek gövdesinde bir bayt dizisi olarak gönderin,
* [Varlık kimliğini](../latest/assets-concept.md) sağlayarak mevcut Azure Media Services varlığını kullanın (yalnızca ücretli hesaplarda desteklenir).

Makalesinde, bu seçeneklerle videolarınızı karşıya yükleme ve dizin oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir:

* [Video Indexer web sitesi](#upload-and-index-a-video-using-the-video-indexer-website) 
* [Video Indexer API'leri](#upload-and-index-with-api)

## <a name="supported-file-formats-for-video-indexer"></a>Video Indexer için desteklenen dosya biçimleri

Video Indexer ile kullanabileceğiniz dosya biçimlerinin bir listesi için bkz. [Giriş kapsayıcı/dosya biçimleri](../latest/encode-media-encoder-standard-formats-reference.md) makalesi.

## <a name="video-files-storage"></a>Video dosyaları depolaması

- Ücretli Video Indexer hesabıyla, Azure aboneliğinize ve bir Azure Media Services hesabına bağlı bir Video Indexer hesabı oluşturursunuz. Daha fazla bilgi için bkz. [Azure 'a bağlı video Indexer hesabı oluşturma](connect-to-azure.md).
- Video dosyaları Azure Media Services tarafından Azure depolama 'da depolanır. Zaman sınırlaması yoktur.
- Video ve ses dosyalarınızı her zaman, Video Indexer tarafından ayıklanan meta verileri ve öngörüleri de silebilirsiniz. Video Indexer bir dosyayı sildikten sonra dosya ve meta verileri ve öngörüleri Video Indexer kalıcı olarak kaldırılır. Ancak, Azure depolama 'da kendi yedekleme çözümünüzü uyguladıysanız, dosya Azure depolama alanında kalır.
- Karşıya yükleme işlemi Video Indexer Web sitesini veya karşıya yükleme API 'sini kullanmayı ne olursa olsun, videonun kalıcılığı aynı olur.
   
## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a>Video Indexer Web sitesini kullanarak bir videoyu karşıya yükleme ve dizin oluşturma

> [!NOTE]
> Videonun adı 80 karakterden uzun olamaz.

1. [Video Indexer](https://www.videoindexer.ai/) web sitesinde oturum açın.
1. Karşıya video yüklemek için **Karşıya Yükle** düğme veya bağlantısına basın.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Karşıya yükle":::
1. Videonuz karşıya yüklendikten sonra Video Indexer videoyu dizinlemeye ve analiz etmeye başlar.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Karşıya yükleme ilerleme durumu":::
1. Video Indexer çözümlendikten sonra, videonuza bağlantı içeren bir e-posta alacaksınız ve videonuzda nelerin bulunmuştur ilgili kısa bir açıklama alırsınız. Örnek: kişiler, konular, OCR’ler.

## <a name="upload-and-index-with-api"></a>API ile karşıya yükleme ve Dizin

Videoları karşıya yüklemek ve bir URL 'ye göre dizinlemek için [video yükleme](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) API 'sini kullanın. Aşağıdaki kod örneği, bayt dizisinin nasıl karşıya yükleneceğini gösteren açıklamalı dışarı çıkan kodu içerir. 

### <a name="configurations-and-params"></a>Yapılandırmalar ve parametreler

Bu bölümde, isteğe bağlı parametrelerin bazıları ve ayarlanmalarının ne zaman gerekeceği açıklanmaktadır. En güncel params bilgileri için [karşıya yükleme videosu](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) API 'sine bakın.

#### <a name="externalid"></a>externalID 

Bu parametre, video ile ilişkilendirilecek bir kimlik belirtmenize olanak sağlar. Bu kimlik, dış "Video İçerik Yönetimi" (VCM) sistemiyle tümleştirmede kullanılabilir. Video Indexer portalında bulunan videolar, belirtilen dış kimlik kullanılarak aranabilir.

#### <a name="callbackurl"></a>callbackUrl

[!INCLUDE [callback url](./includes/callback-url.md)]

##### <a name="other-considerations"></a>Diğer önemli noktalar

- Video Indexer, özgün URL 'de belirtilen mevcut parametreleri döndürür.
- Belirtilen URL kodlanmalıdır.

#### <a name="indexingpreset"></a>indexingPreset

Ses veya video dosyanıza uygulamak istediğiniz AI grubunu tanımlamak için bu parametreyi kullanın. Bu parametre, dizinleme işlemini yapılandırmak için kullanılır. Aşağıdaki değerleri belirtebilirsiniz:

- `AudioOnly` – Yalnızca ses kullanarak öngörüleri dizine alın ve ayıklayın (video yok sayılıyor).
- `VideoOnly` -Yalnızca video kullanarak öngörüleri dizine alın ve ayıklayın (ses yok sayılıyor).
- `Default` – Hem ses hem de video kullanarak öngörüleri dizine alın ve ayıklayın.
- `DefaultWithNoiseReduction` – Ses akışına gürültü azaltma algoritmaları uygulanırken hem ses hem de videodan öngörüleri dizine alın ve ayıklayın.

    `DefaultWithNoiseReduction`Değer artık varsayılan önayar (kullanım dışı) ile eşlenir.
- `BasicAudio` -Yalnızca temel ses özellikleri (döküm, çeviri, biçim çıkış alt yazıları ve alt yazılar) dahil olmak üzere yalnızca ses (video yok sayılıyor) kullanarak öngörüleri dizine alın ve ayıklayın.
 - `AdvancedAudio` -Standart ses analizine ek olarak gelişmiş ses özellikleri (ses olayı algılama) dahil olmak üzere yalnızca ses kullanarak öngörüleri dizine alın ve ayıklayın.

> [!NOTE]
> Video Indexer, sesin en fazla iki parçasını içerir. Dosyada daha fazla ses parçası varsa, bunlar tek bir izleme olarak kabul edilir.<br/>
Parçaları ayrı olarak dizinlemek istiyorsanız ilgili ses dosyasını ayıklamanız ve olarak dizinetmeniz gerekir `AudioOnly` .

Fiyat, seçilen dizinleme seçeneğine bağlıdır. Daha fazla bilgi için [Media Services fiyatlandırmasına](https://azure.microsoft.com/pricing/details/media-services/)bakın.

#### <a name="priority"></a>Priority

Videoların önceliklerine göre Video Indexer dizini oluşturulur. Dizin önceliğini belirtmek için **Priority** parametresini kullanın. Şu değerler geçerlidir: **düşük**, **normal** (varsayılan) ve **yüksek**.

**Priority** parametresi yalnızca ücretli hesaplar için desteklenir.

#### <a name="streamingpreset"></a>streamingPreset

Videonuz karşıya yüklendikten sonra Video Indexer, isteğe bağlı olarak videoyu kodlar. Video Indexer, ardından dizinlemeye ve videoyu analiz etmeye geçer. Video Indexer analizi tamamladığında video kimliğini içeren bir bildirim alırsınız.  

[Videoyu karşıya yükleme](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) veya [Videoyu Yeniden Dizinleme](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) API'sini kullanırken isteğe bağlı parametrelerden biri de `streamingPreset` parametresidir. `streamingPreset` parametresini `Default`, `SingleBitrate` veya `AdaptiveBitrate` olarak ayarlarsanız kodlama işlemi tetiklenir. Dizinleme ve kodlama işleri tamamlandıktan sonra video yayımlanır. Böylece videonuzun akışını da yapabilirsiniz. Video akışı yapmak istediğiniz Akış Uç Noktası **Çalışıyor** durumunda olmalıdır.

Tekbit hızı için standart kodlayıcı maliyeti çıkış başına uygulanır. Video yüksekliği 720 ' e eşit veya daha büyükse, Video Indexer 1280x720 olarak kodlar. Aksi takdirde, 640x468 olarak.
Varsayılan ayar, [içerik duyarlı kodlanıyor](../latest/encode-content-aware-concept.md).

Dizinleme ve kodlama işlerini çalıştırmak için [Video Indexer hesabınıza bağlı Azure Media Services hesabı](connect-to-azure.md) Ayrılmış Birimler gerektirir. Daha fazla bilgi için bkz. [Medya İşlemeyi Ölçeklendirme](../previous/media-services-scale-media-processing-overview.md). Bunlar işlem gücü kullanımı yoğun işler olduğundan S3 türü birimlerin kullanılması önemle tavsiye edilir. Ayrılmış birim sayısı, paralel olarak çalıştırılabilecek en fazla iş sayısını tanımlar. Önerilen temel kullanım 10 S3 ayrılmış birimdir. 

Videonuzu yalnızca dizinlemek istiyorsanız ve kodlamayacaksanız `streamingPreset` parametresini `NoStreaming` olarak ayarlayın.

#### <a name="videourl"></a>videoUrl

Dizinlenecek video/ses dosyasının URL'si. URL bir medya dosyasına yönlendirmelidir (HTML sayfaları desteklenmez). Dosya, URI'nin parçası olarak sunulan bir erişim belirteci tarafından korunabilir ve dosyayı sunan uç noktanın güvenliği TLS 1.2 veya üzeri bir sürümle sağlanmalıdır. URL’nin kodlanması gerekir. 

`videoUrl` belirtilmezse Video Indexer dosyayı çok parçalı/form gövde içeriği olarak geçirmenizi bekler.

### <a name="code-sample"></a>Kod örneği

Aşağıdaki C# kod parçacığı, tüm Video Indexer API'lerinin kullanımını bir arada göstermektedir.

**Aşağıdaki kod örneğini çalıştırmaya yönelik yönergeler**

Bu kodu geliştirme platformunuza kopyaladıktan sonra iki parametre sağlamanız gerekir: API Management kimlik doğrulama anahtarı ve video URL 'SI.

* API anahtarı – API anahtarı, Video Indexer hesabınızda işlem gerçekleştirmek için bir erişim belirteci almanızı sağlayacak olan kişisel API Yönetimi abonelik anahtarınıza sahiptir. 

    API anahtarınızı almak için şu akışa gidin:

    * https://api-portal.videoindexer.ai/ sayfasına gidin
    * Oturum aç
    * **Ürünler**  ->  **Yetkilendirme**  ->  **Yetkilendirme aboneliğine** git
    * **Birincil anahtarı** Kopyala
* Video URL 'SI: endekslenecek video/ses dosyasının URL 'SI. URL bir medya dosyasına yönlendirmelidir (HTML sayfaları desteklenmez). Dosya, URI'nin parçası olarak sunulan bir erişim belirteci tarafından korunabilir ve dosyayı sunan uç noktanın güvenliği TLS 1.2 veya üzeri bir sürümle sağlanmalıdır. URL’nin kodlanması gerekir.

Kod örneğini başarıyla çalıştırmanın sonucu, sırasıyla karşıya yüklenen öngörüleri ve videoları incelemenize olanak sağlayacak bir öngörü pencere öğesi URL 'SI ve oynatıcı pencere öğesi URL 'SI içerir. 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```

### <a name="common-errors"></a>Sık karşılaşılan hatalar

Upload işlemi aşağıdaki tabloda listelenen durum kodlarını döndürebilir.

|Durum kodu|ErrorType (yanıt gövdesinde)|Açıklama|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|Bu video zaten aynı hesapta işleniyor.|
|400|VIDEO_ALREADY_FAILED|Bu videonun işlenmesi 2 saatten daha kısa bir süre önce aynı hesapta başarısız oldu. API istemcilerin videoyu yeniden yüklemek için en az 2 saat beklemesi gerekir.|
|429||Deneme hesaplarına dakika başına 5 karşıya yükleme izni verilir. Ücretli hesaplara dakika başına 50 yükleme izni verilir.|

## <a name="uploading-considerations-and-limitations"></a>Karşıya yükleme konusunda dikkat edilmesi gerekenler ve sınırlamalar
 
- Videonun adı 80 karakterden uzun olamaz.
- Videonuzu URL’ye dayalı olarak karşıya yüklerken (tercih edilir) uç noktanın güvenliği TLS 1.2 (veya üzeri) ile sağlanmalıdır.
- URL seçeneğiyle karşıya yükleme boyutu 30 GB ile sınırlıdır.
- İstek URL'si uzunluğu 6144 karakterle, sorgu dizesi URL'si uzunluğu ise 4096 karakterle sınırlıdır.
- Bayt dizisi seçeneğiyle karşıya yükleme boyutu 2 GB ile sınırlıdır.
- Bayt dizisi seçeneği 30 dakika sonra zaman aşımına uğrar.
- Param 'da belirtilen URL 'nin `videoURL` kodlanması gerekir.
- Indexing Media Services varlıklarında, URL'den dizin oluşturmayla aynı sınırlama geçerlidir.
- Video Indexer'ın tek dosya için maksimum süre sınırı 4 saattir.
- URL'nin erişilebilir olması gerekir (örneğin genel URL olabilir). 

    Bu bir özel URL'yse erişim belirteci istekte sağlanmalıdır.
- URL, sayfanın bağlantısı gibi bir Web sayfasına değil, geçerli bir medya dosyasına işaret etmek zorunda `www.youtube.com` .
- Ücretli bir hesapta dakikada 50 filme kadar ve deneme hesabında da dakikada 5 filme kadar karşıya yükleyebilirsiniz.

> [!Tip]
> .NET Framework 4.6.2 veya üzeri bir sürümünü kullanmanız önerilir. Eski .NET Framework sürümlerinde varsayılan olarak TLS 1.2 ayarı kullanılmaz.
>
> Eski .NET Framework sürümlerini kullanmanız gerekirse kodunuzda REST API çağrısı öncesine bir satır ekleyin:  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="next-steps"></a>Sonraki adımlar

[API tarafından üretilen Azure Video Indexer çıkışını inceleyin](video-indexer-output-json-v2.md)
