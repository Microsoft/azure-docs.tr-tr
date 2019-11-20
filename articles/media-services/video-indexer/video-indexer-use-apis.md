---
title: Video Indexer API'sini kullanma
titleSuffix: Azure Media Services
description: Azure Media Services Video Indexer API 'SI ile çalışmaya başlama öğreticisi.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: e1fcfcd9e6bcb6108573f032d81f10ccc2be1e57
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839632"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Öğretici: Video Indexer API'sini kullanma

Video Indexer, Microsoft tarafından sunulan çeşitli ses ve video yapay zeka (AI) teknolojilerini tek bir tümleşik hizmette birleştirerek geliştirmeyi daha kolay hale getirir. API 'Ler, geliştiricilerin ölçek, küresel erişim, kullanılabilirlik ve bulut platformlarının güvenilirliğini öğrenmek zorunda kalmadan medya AI teknolojilerine odaklanmasını sağlamak üzere tasarlanmıştır. API 'Leri kullanarak dosyalarınızı karşıya yükleyebilir, ayrıntılı video öngörüleri alabilir, eklenebilir Öngörüler ve oynatıcı Pencere öğelerinin URL 'Lerini alabilir ve daha fazlasını yapabilirsiniz.

Video Indexer bir hesap oluştururken, ücretsiz bir deneme hesabı (belirli bir sayıda dizin oluşturma dakikası aldığınız) veya ücretli bir seçenek (Kota ile sınırlı olmamak üzere) seçebilirsiniz. Ücretsiz deneme sürümü sayesinde, Video Indexer Web sitesi kullanıcılarına en fazla 600 dakikalık ücretsiz dizin oluşturma ve API kullanıcılarına 2400 dakikalık ücretsiz dizin oluşturma olanağı sağlar. Ücretli bir seçenekle, [Azure aboneliğinize ve Azure Media Services hesabına bağlı](connect-to-azure.md)bir video Indexer hesabı oluşturursunuz. Dizin oluşturma faaliyeti yapılan dakika sayısının yanı sıra Azure Media Services hesabıyla ilgili ücretler için ödeme yaparsınız.

Bu makalede geliştiricilerin [Video Indexer API’sinden](https://api-portal.videoindexer.ai/) nasıl yararlanabileceği açıklanmaktadır.

## <a name="subscribe-to-the-api"></a>API’ye abone olma

1. [Video Indexer Geliştirici Portalı](https://api-portal.videoindexer.ai/)’nda oturum açın.
    
    ![Video Indexer Geliştirici Portalında oturum açın](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * Video Indexer için kaydolurken kullandığınız sağlayıcıyı kullanmanız gerekir.
   > * Kişisel Google ve Microsoft (Outlook/Live) hesapları yalnızca deneme hesapları için kullanılabilir. Azure'a bağlı hesaplar için Azure Active Directory gerekir.
   > * Her e-posta için yalnızca bir etkin hesap olabilir. Bir Kullanıcı, Google için user@gmail.com ile LinkedIn ve üzeri için user@gmail.com oturum açmaya çalışırsa, ikincisi, kullanıcının zaten var olduğunu söyleyen bir hata sayfası görüntüler.

2. Abone olun.

    [Ürünler](https://api-portal.videoindexer.ai/products) sekmesini seçin. Sonra yetkilendirme ve abone ol ' u seçin.
    
    ![Video Indexer geliştirici portalındaki ürünler sekmesi](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Yeni kullanıcılar otomatik olarak Yetkilendirme’ye abone edilir.
    
    Abone olduktan sonra aboneliğinizi ve birincil ve ikincil anahtarlarınızı görebilirsiniz. Anahtarlar korunmalıdır. Anahtarlar yalnızca sunucu kodunuz tarafından kullanılmalıdır. İstemci tarafında (. js,. html vb.) kullanılamayacak.

    ![Video Indexer geliştirici portalındaki abonelik ve anahtarlar](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> Video Indexer kullanıcısı, tek bir abonelik anahtarını kullanarak birden çok Video Indexer hesabına bağlanabilir. Daha sonra bu Video Indexer hesaplarını farklı Media Services hesaplarına bağlayabilirsiniz.

## <a name="obtain-access-token-using-the-authorization-api"></a>Yetkilendirme API'sini kullanarak erişim belirtecini alma

Yetkilendirme API 'sine abone olduktan sonra erişim belirteçleri elde edebilirsiniz. Bu erişim belirteçleri, İşlemler API’sinde kimlik doğrulamak için kullanılır.

İşlemler API'sine yapılan her çağrı, çağrının yetkilendirme kapsamına uyan bir erişim belirteci ile ilişkilendirilmelidir.

- Kullanıcı düzeyi: Kullanıcı düzeyi erişim belirteçleri, **Kullanıcı** düzeyinde işlem gerçekleştirmenize olanak tanır. Örnek: ilişkili hesaplar alma.
- Hesap düzeyi: hesap düzeyi erişim belirteçleri, **Hesap** düzeyinde veya **video** düzeyinde işlemler gerçekleştirmenize olanak tanır. Örneğin, videoyu karşıya yükleyin, tüm videoları listeleyin, video öngörüleri alın ve bu şekilde devam edin.
- Video düzeyi: video düzeyi erişim belirteçleri, belirli bir **video**üzerinde işlemler gerçekleştirmenize olanak tanır. Örneğin, video öngörüleri edinin, resim yazıları indirin, pencere öğelerini alın vb.

Bu belirteçlerin salt okunurdur veya **AllowEdit = true/false**belirterek düzenlenmesine izin verip vermeyeceklerini kontrol edebilirsiniz.

Çoğu sunucu-sunucu senaryosunda, büyük olasılıkla **Hesap** işlemlerini ve **video** işlemlerini kapsadığından aynı **Hesap** belirtecini kullanacaksınız. Ancak, Video Indexer için istemci tarafı çağrıları yapmayı planlıyorsanız (örneğin, JavaScript 'ten), istemcilerin tüm hesaba erişimini engellemek için bir **video** erişim belirteci kullanmak istersiniz. Ayrıca, istemci kodu Video Indexer Katıştırırken (örneğin, **öngörüleri al pencere öğesini** veya **Player pencere öğesini al**' ı kullanarak), bir **video** erişim belirteci sağlamanız gerekir.

İşleri kolaylaştırmak için **Yetkilendirme** API’si > **GetAccounts**’u kullanarak ilk önce bir kullanıcı belirteci almaya gerek kalmadan hesaplarınızı alabilirsiniz. Ayrıca, hesapları geçerli belirteçlerle almayı da isteyebilirsiniz. Böylece hesap belirteci almak için yapılacak ek bir çağrıyı atlayabilirsiniz.

Erişim belirteçlerinin süresi 1 saatte dolar. İşlemler API'sini kullanmadan önce erişim belirtecinizin geçerli olduğundan emin olun. Süresi dolarsa, yeni bir erişim belirteci almak için yetkilendirme API 'sini yeniden çağırın.

API ile tümleştirmeye başlamaya hazırsınız. [Her bir Video Indexer REST API’nin ayrıntılı açıklamasına](https://api-portal.videoindexer.ai/) bakabilirsiniz.

## <a name="account-id"></a>Hesap Kimliği

Hesap Kimliği parametresi, işleme yönelik tüm API çağrıları için gereklidir. Hesap Kimliği, aşağıdaki yollardan biriyle alınabilen bir GUID'dir:

* Hesap Kimliğini almak için **Video Indexer web sitesini** kullanın:

    1. [Video Indexer](https://www.videoindexer.ai/) web sitesine gidip oturum açın.
    2. **Ayarlar** sayfasına gidin.
    3. Hesap kimliğini kopyalayın.

        ![Video Indexer ayarları ve hesap KIMLIĞI](./media/video-indexer-use-apis/account-id.png)

* Hesap Kimliğini programlı olarak almak için **Video Indexer Geliştirici Portalı**’nı kullanın.

    [Hesap Al](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) API’sini kullanın.

    > [!TIP]
    > `generateAccessTokens=true` parametresini tanımlayarak hesaplar için erişim belirteçleri oluşturabilirsiniz.

* Hesap Kimliğini hesabınızdaki bir yürütücü sayfasının URL'sinden alın.

    Video izlerken kimlik, `accounts` bölümünden sonra ve `videos` bölümünden önce görünür.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Öneriler

Bu bölümde, Video Indexer API kullanırken işinize yarayacak bazı öneriler sunulmaktadır.

- Bir videoyu karşıya yüklemeyi planlıyorsanız dosyayı bazı genel ağ konumlarına (örneğin, OneDrive) yerleştirmeniz önerilir. Videonun bağlantısını alın ve URL'yi karşıya dosya yükleme parametresi olarak kullanın.

    Video Indexer’a sağlanan URL bir medya dosyasına (ses veya video) yönlendirmelidir. OneDrive tarafından oluşturulan bağlantıların bazıları, dosyayı içeren bir HTML sayfası içindir. URL için kolay bir doğrulama bir tarayıcıya yapıştırmaktır — dosya indirilbaşlarsa, büyük olasılıkla iyi bir URL olur. Tarayıcı bazı görselleştirmeleri işliyorsa, büyük olasılıkla bir dosyanın bağlantısı değildir ve bir HTML sayfasına değildir.

- Belirtilen video için video içgörüleri alan API’yi çağırdığınızda yanıt içeriği olarak ayrıntılı bir JSON çıktısını alırsınız. [Döndürülen JSON hakkındaki ayrıntıları bu konuda görebilirsiniz](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Kod örneği

Aşağıdaki C# kod parçacığı, tüm Video Indexer API'lerinin kullanımını bir arada göstermektedir.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="see-also"></a>Ayrıca bkz.

- [Video Indexer’a genel bakış](video-indexer-overview.md)
- [Bölgeler](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Sonraki adımlar

[JSON çıktısının ayrıntılarını inceleyin](video-indexer-output-json-v2.md).
