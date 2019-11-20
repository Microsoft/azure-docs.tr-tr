---
title: Media Services v3 ile karşıya yükleme, kodlama ve akışla
titleSuffix: Azure Media Services
description: Azure Media Services v3 ile bir dosyayı karşıya yüklemeyi, videoyu kodlamayı ve akış içeriğini gösteren öğretici.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/22/2019
ms.author: juliako
ms.openlocfilehash: f8ff3dc71727abf9e276cccc951c4d1143f4200d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583108"
---
# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>Öğretici: Media Services v3 ile videoları karşıya yükleme, kodlama ve akışla

> [!NOTE]
> Bu öğretici [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) örnekleri kullanıyor olsa da, genel adımlar [REST API](https://docs.microsoft.com/rest/api/media/liveevents), [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)veya desteklenen diğer [SDK](media-services-apis-overview.md#sdks)'lar için aynıdır.

Azure Media Services, medya dosyalarınızı çok çeşitli tarayıcılarda ve cihazlarda oynatacak biçimlere kodlamanızı sağlar. Örneğin, içeriğinizi Apple'ın HLS veya MPEG DASH biçimlerinde akışla göndermek isteyebilirsiniz. Akışla göndermeden önce yüksek kaliteli dijital medya dosyanızı kodlamanız gerekir. Kodlama konusunda yardım için bkz. [kodlama kavramı](encoding-concept.md). Bu öğretici yerel video dosyasını karşıya yükler ve karşıya yüklenen dosyayı kodlar. Ayrıca, bir HTTPS URL 'SI aracılığıyla erişilebilir hale gelen içeriği de kodlayabilirsiniz. Daha fazla bilgi için bkz. [HTTP(s) URL'sinde iş girişi oluşturma](job-input-from-http-how-to.md).

![Azure Media Player ile video oynatma](./media/stream-files-tutorial-with-api/final-video.png)

Bu öğretici şunların nasıl yapıldığını gösterir:

> [!div class="checklist"]
> * Konusunda açıklanan örnek uygulamayı indirin.
> * Karşıya yükleyen, kodlayan ve akışların kodunu inceleyin.
> * Uygulamayı çalıştırın.
> * Akış URL 'sini test edin.
> * Kaynakları temizleyin.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Ön koşullar

- Visual Studio yüklü değilse, [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)' i edinebilirsiniz.
- [Media Services hesabı oluşturun](create-account-cli-how-to.md).<br/>Kaynak grubu adı ve Media Services hesap adı için kullandığınız değerleri anımsadığınızdan emin olun.
- [Azure CLI Ile Access Azure Media Services API 'sindeki](access-api-cli-how-to.md) adımları izleyin ve kimlik bilgilerini kaydedin. API 'ye erişmek için bunları kullanmanız gerekir.

## <a name="download-and-set-up-the-sample"></a>Örneği indirin ve ayarlayın

Aşağıdaki komutu kullanarak, akışı .NET örneği olan bir GitHub deposunu makinenize kopyalayın:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

Örnek, [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles) klasöründe yer alır.

İndirilen projenizde [appSettings. JSON](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) ' yı açın. Değerleri, [API 'lere eriştiğiniz](access-api-cli-how-to.md)kimlik bilgileriyle değiştirin.

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>Karşıya yüklenen, kodlanan ve akışı yapılan kodu inceleme

Bu bölümde, [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) projesinin *Program.cs* dosyasında tanımlı işlevler incelenmektedir.

Örnek aşağıdaki eylemleri gerçekleştirir:

1. Yeni bir **dönüşüm** oluşturur (ilk olarak, belirtilen dönüştürmenin var olup olmadığını denetler).
2. Kodlama **işinin**çıkışı olarak kullanılan bir çıktı **varlığı** oluşturur.
3. Bir giriş **varlığı** oluşturun ve belirtilen yerel video dosyasını bu dosyaya yükler. Varlık, işin girişi olarak kullanılır.
4. Oluşturulan girişi ve çıktıyı kullanarak kodlama işini gönderir.
5. İşin durumunu denetler.
6. Bir **akış Bulucu**oluşturur.
7. Akış URL'leri oluşturur.

### <a name="a-idstart_using_dotnet-start-using-media-services-apis-with-net-sdk"></a>.NET SDK ile Media Services API 'Leri kullanmaya başlamak <a id="start_using_dotnet" />

.NET ile Media Services API’lerini kullanmaya başlamak için bir **AzureMediaServicesClient** nesnesi oluşturmanız gerekir. Nesneyi oluşturmak için, istemcinin Azure AD 'yi kullanarak Azure 'a bağlanması için gereken kimlik bilgilerini sağlamanız gerekir. Makalenin başlangıcında kopyaladığınız kodda, **GetCredentialsAsync** işlevi, yerel yapılandırma dosyasında sağlanan kimlik bilgilerini temel alarak ServiceClientCredentials nesnesi oluşturur.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Bir giriş varlığı oluşturma ve içine yerel dosya yükleme

**CreateInputAsset** işlevi yeni bir giriş [Varlığı](https://docs.microsoft.com/rest/api/media/assets) oluşturur ve içine belirtilen yerel video dosyasını yükler. Bu **varlık** , kodlama işinize giriş olarak kullanılır. Media Services v3 'de, bir **işin** GIRIŞI, https URL 'leri aracılığıyla Media Services hesabınız için kullanılabilir hale getirebileceği bir **varlık** ya da içerik olabilir. HTTPS URL 'sinden kodlama hakkında bilgi edinmek için [Bu](job-input-from-http-how-to.md) makaleye bakın.

Media Services v3’te dosyaları karşıya yüklemek için Azure Depolama API’lerini kullanırsınız. Aşağıdaki .NET kod parçacığı bunun nasıl yapıldığını gösterir.

Aşağıdaki işlev şu eylemleri gerçekleştirir:

* Bir **varlık**oluşturur.
* [Depolama alanındaki varlığın kapsayıcısına](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)yazılabilir bir [SAS URL 'si](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) alır.
* SAS URL 'sini kullanarak dosyayı depolama alanındaki kapsayıcıya yükler.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Bir işin sonucunu depolamak için çıktı varlığı oluşturma

Çıktı [Varlığı](https://docs.microsoft.com/rest/api/media/assets), kodlama işinizin sonucunu depolar. Proje, bu çıktı varlığının sonuçlarını "output" klasörüne indiren **DownloadResults** işlevini tanımlar, böylece elinizde neyin olduğunu görebilirsiniz.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Bir Dönüşüm ve karşıya yüklenen dosyayı kodlayan İş oluşturma

Media Services içeriği kodlarken veya işlerken, kodlama ayarlarını tarif olarak ayarlamak için ortak bir modeldir. Daha sonra bu tarifi bir videoya uygulamak üzere bir **İş** gönderirsiniz. Her yeni video için yeni işler göndererek, bu tarifi kitaplığınızdaki tüm videolara uygulayacağız. Media Services bir tarif, **dönüşüm**olarak adlandırılır. Daha fazla bilgi için [Dönüşümler ve İşler](transform-concept.md) konusuna bakın. Bu öğreticide açıklanan örnek, videoyu çeşitli iOS ve Android cihazlarına akışla aktarmak için kodlayan bir tarifi tanımlar.

#### <a name="transform"></a>Dönüşüm

Yeni bir [Dönüşüm](https://docs.microsoft.com/rest/api/media/transforms) örneği oluştururken çıktı olarak neyi üretmesi istediğinizi belirtmeniz gerekir. Gerekli parametre, aşağıdaki kodda gösterildiği gibi bir **TransformOutput** nesnesidir. Her **TransformOutput** bir **Ön ayar** içerir. **Ön ayar**, video ve/veya ses işleme işlemlerinin istenen **TransformOutput** nesnesini oluşturmak üzere kullanılacak adım adım yönergelerini açıklar. Bu makalede açıklanan örnek, **AdaptiveStreaming** adlı yerleşik bir Ön Ayar kullanır. Ön Ayar, giriş çözünürlüğü ve bit hızını temel alarak, giriş videosunu otomatik olarak oluşturulan bir bit hızı basamağına (bit hızı-çözünürlük çiftleri) kodlar ve her bir bit hızı-çözünürlük çiftine karşılık gelen H.264 video ve AAC sesi ile ISO MP4 dosyaları üretir. Bu Ön Ayar hakkında bilgi için bkz. [otomatik oluşturulan bit hızı basamağı](autogen-bitrate-ladder.md).

Yerleşik bir EncoderNamedPreset ön ayarını veya özel ön ayarları kullanabilirsiniz. Daha fazla bilgi için bkz. [Kodlayıcı önayarlarını özelleştirme](customize-encoder-presets-how-to.md).

Bir [Dönüşüm](https://docs.microsoft.com/rest/api/media/transforms) oluştururken ilk olarak aşağıdaki kodda gösterildiği gibi **Get** yöntemi ile bir dönüşümün zaten var olup olmadığını denetlemeniz gerekir. Media Services v3’te varlıklar üzerindeki **Get** yöntemleri, varlığın mevcut olmaması durumunda **null** değerini döndürür (büyük/küçük harfe duyarlı ad denetimi).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>İş

Yukarıda bahsedildiği gibi [Transform](https://docs.microsoft.com/rest/api/media/transforms) nesnesi tarif, [Job](https://docs.microsoft.com/rest/api/media/jobs) ise bu **Transform** nesnesini belirli bir giriş videosu veya ses içeriğine uygulamak için Media Services’e gönderilen gerçek istektir. **İş** giriş videosunun konumu ve çıktının konumu gibi bilgileri belirtir.

Bu örnekte giriş videosu, yerel makinenizden yüklenmiştir. HTTPS URL 'sinden kodlama hakkında bilgi edinmek istiyorsanız [Bu](job-input-from-http-how-to.md) makaleye bakın.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>İşin tamamlanmasını bekleyin

İşin tamamlanması biraz sürüyor ve tamamlandığında bildirim almak istiyorsunuz. Aşağıdaki kod örneği, [İş](https://docs.microsoft.com/rest/api/media/jobs)’in durumu için hizmette nasıl yoklama yapılacağını gösterir. Yoklama, olası gecikme nedeniyle üretim uygulamaları için önerilen en iyi uygulamadır. Yoklama, bir hesap üzerinde gereğinden fazla kullanılırsa kısıtlanabilir. Geliştiricilerin onun yerine Event Grid kullanmalıdır.

Event Grid yüksek kullanılabilirlik, tutarlı performans ve dinamik ölçek için tasarlanmıştır. Event Grid ile uygulamalarınız neredeyse tüm Azure hizmetleri ve özel kaynaklardan gelen olayları takip edip bu olaylara yanıt verebilir. Basit, HTTP tabanlı reaktif olay işleme özelliği, olayların akıllı filtrelenmesi ve yönlendirilmesi sayesinde etkili çözümler oluşturmanıza yardımcı olur.  Bkz. [Olayları özel bir web uç noktasına yönlendirme](job-state-events-cli-how-to.md).

**İş** genellik şu aşamalardan geçer: **Zamanlandı**, **Kuyruğa Alındı**, **İşleniyor**, **Tamamlandı** (son aşama). İş bir hatayla karşılaştıysa **Hata** durumunu alırsınız. İş iptal edilme süreciiyorsa, tamamlandığında iptal edip **Iptal etmiş** olursunuz **.**

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>İş hata kodları

Bkz. [hata kodları](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="get-a-streaming-locator"></a>Akış Bulucu alma

Kodlama tamamlandıktan sonra sıradaki adım, çıktı Varlığındaki videoyu yürütmek için istemcilerin kullanımına sunmaktır. Bunu iki adımda kullanılabilir hale getirebilirsiniz: ilk olarak, bir [akış Bulucu](https://docs.microsoft.com/rest/api/media/streaminglocators)oluşturun ve ikinci olarak, istemcilerin kullanabileceği akış URL 'lerini oluşturun.

**Akış Bulucu** oluşturma işlemine yayımlama denir. Varsayılan olarak, **akış Bulucu** , API çağrılarını yaptıktan hemen sonra geçerlidir ve isteğe bağlı başlangıç ve bitiş zamanlarını yapılandırmadığınız sürece silinene kadar sürer.

Bir [Streaminglocator](https://docs.microsoft.com/rest/api/media/streaminglocators)oluştururken, Istenen **Streamingpolicyname**' i belirtmeniz gerekir. Bu örnekte, açık akış ilkesinin (**Predefinedstreamingpolicy. ClearStreamingOnly**) kullanılması için,-Clear (veya şifrelenmemiş olmayan içerik) akışı oluşturursunuz.

> [!IMPORTANT]
> Özel bir [akış ilkesi](https://docs.microsoft.com/rest/api/media/streamingpolicies)kullanırken, medya hizmeti hesabınız için sınırlı sayıda ilke kümesi tasarlamalı ve aynı şifreleme seçenekleri ve protokoller gerektiğinde bunları streamingbulucular için yeniden kullanmanız gerekir. Medya hizmeti hesabınızın akış Ilkesi girişi sayısı için bir kotası vardır. Her bir akış bulucu için yeni bir akış Ilkesi oluşturmamalısınız.

Aşağıdaki kod, işlevi benzersiz bir locatorName ile çağırmanın olduğunu varsayar.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Bu konudaki örnek akışı ele alırken, aşamalı indirme yoluyla video teslim etmek için bir akış Bulucu oluşturmak için aynı çağrıyı kullanabilirsiniz.

### <a name="get-streaming-urls"></a>Akış URL'leri alma

Artık [akış bulucunun](https://docs.microsoft.com/rest/api/media/streaminglocators) oluşturulduğuna göre, **Getstreamingurls**Içinde gösterildiği gibi akış URL 'lerini alabilirsiniz. URL oluşturmak için, [akış uç noktası](https://docs.microsoft.com/rest/api/media/streamingendpoints) ana bilgisayar adını ve **akış Bulucu** yolunu birleştirmeniz gerekir. Bu örnekte, *varsayılan* **akış uç noktası** kullanılır. İlk olarak bir medya hizmeti hesabı oluşturduğunuzda, bu *varsayılan* **akış uç noktası** durdurulmuş durumda olacaktır, bu yüzden **Start**'ı çağırmanız gerekir.

> [!NOTE]
> Bu yöntemde, çıkış varlığı için **akış bulucuyu** oluştururken kullanılan locatorname öğesine ihtiyacınız vardır.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Media Services hesabınızdaki kaynakları temizleme

Genellikle, yeniden kullanmayı planladığınız nesneler hariç her şeyi temizlemeniz gerekir (genellikle dönüşümleri yeniden kullanacaksınız ve Streamingkonumlandırıcı, vb.). Deneme sonrasında hesabınızın temiz olmasını istiyorsanız, kullanmayı planlamadığınız kaynakları silin. Örneğin, aşağıdaki kod Işleri siler:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Örnek uygulamayı çalıştırma

1. *Encodeandstreamfiles* uygulamasını çalıştırmak için CTRL + F5 tuşlarına basın.
2. Akış URL’lerinden birini konsoldan kopyalayın.

Bu örnekte, farklı protokolleri kullanarak videoyu kayıttan yürütmek için kullanılabilen URL’ler gösterilir:

![Media Services akışı videosu için URL 'Leri gösteren örnek çıkış](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>Akış URL’sini test etme

Bu makalede, akışı test etmek için Azure Media Player kullanılmaktadır.

> [!NOTE]
> Oynatıcı bir https sitesinde barındırılıyorsa, "https" URL’sini güncelleştirdiğinizden emin olun.

1. Bir web tarayıcısı açın ve [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) sayfasına gidin.
2. **URL:** kutusunda, uygulamayı çalıştırdığınızda ALDıĞıNıZ akış URL değerlerinden birini yapıştırın.
3. **Oynatıcıyı Güncelleştir**' i seçin.

Azure Media Player, test için kullanılabilir ancak üretim ortamında kullanılmamalıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturduğunuz Media Services ve depolama hesapları dahil olmak üzere, kaynak grubunuzdaki kaynaklardan herhangi birine artık ihtiyacınız yoksa kaynak grubunu silebilirsiniz.

Aşağıdaki CLı komutunu yürütün:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Çoklu iş parçacığı kullanımı

Azure Media Services v3 SDK 'Ları iş parçacığı açısından güvenli değildir. Çok iş parçacıklı bir uygulama geliştirirken, iş parçacığı başına yeni bir Azudüzeltici Istemci nesnesi oluşturmanız ve kullanmanız gerekir.

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="next-steps"></a>Sonraki adımlar

Videonuzu karşıya yükleme, kodlama ve akışla aktarma hakkında bilgi edindiğinize göre aşağıdaki makaleye bakabilirsiniz: 

> [!div class="nextstepaction"]
> [Videoları analiz etme](analyze-videos-tutorial-with-api.md)
