---
title: Azure Media Services v3 ile açıklayıcı sesli parçalar sinyali | Microsoft Docs
description: Bu öğreticinin adımlarını izleyerek bir dosyayı karşıya yükleyin, videoyu kodlayın, açıklayıcı ses parçaları ekleyin ve içeriğinizi Media Services v3 ile akışın.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 09/25/2019
ms.author: juliako
ms.openlocfilehash: 0d8f88e6c2fe273efa969278146de67ba18eaecf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392195"
---
# <a name="signal-descriptive-audio-tracks"></a>Sinyal açıklayıcı ses parçaları

Görsel açıdan görme istemcilerinin, konuşmayı dinleyerek video kaydını izlemesini sağlamak için videonuza konuşma izi ekleyebilirsiniz. Media Services v3 'de, bildirim dosyasındaki ses izlemesine açıklama ekleyerek açıklayıcı ses izlemelerinin sinyalini görürsünüz.

Bu makalede, bir videoyu kodlama, çıktı varlığına açıklayıcı ses içeren bir salt ses MP4 dosyasını (AAC codec) karşıya yükleme ve açıklama sesini dahil etmek için. ISM dosyasını düzenleme işlemlerinin nasıl yapılacağı gösterilir.

## <a name="prerequisites"></a>Önkoşullar

- [Media Services hesabı oluşturun](create-account-cli-how-to.md).
- [Azure CLI Ile Access Azure Media Services API 'sindeki](access-api-cli-how-to.md) adımları izleyin ve kimlik bilgilerini kaydedin. API 'ye erişmek için bunları kullanmanız gerekir.
- [Dinamik paketlemeyi](dynamic-packaging-overview.md)gözden geçirin.
- [Karşıya yükleme, kodlama ve akış videoları](stream-files-tutorial-with-api.md) öğreticisini gözden geçirin.

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Bir giriş varlığı oluşturma ve içine yerel dosya yükleme 

**CreateInputAsset** işlevi yeni bir giriş [Varlığı](https://docs.microsoft.com/rest/api/media/assets) oluşturur ve içine belirtilen yerel video dosyasını yükler. Bu **varlık** , kodlama işinize giriş olarak kullanılır. Media Services v3 'de, bir **işin** girişi bir **varlık**olabilir ya da HTTPS URL 'leri aracılığıyla Media Services hesabınız için kullanılabilir hale getirebilmeniz gereken içerik olabilir. 

HTTPS URL 'sinden kodlama hakkında bilgi edinmek istiyorsanız [Bu makaleye](job-input-from-http-how-to.md) bakın.  

Media Services v3’te dosyaları karşıya yüklemek için Azure Depolama API’lerini kullanırsınız. Aşağıdaki .NET kod parçacığı bunun nasıl yapıldığını gösterir.

Aşağıdaki işlev şu eylemleri gerçekleştirir:

* Bir **varlık** oluşturur 
* [Depolama alanındaki varlığın kapsayıcısına](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container) yazılabilir bir [SAS URL 'si](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) alır
* SAS URL’sini kullanarak dosyayı depolamadaki kapsayıcıya yükler

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Oluşturulan giriş varlığının adını diğer yöntemlere geçirmeniz gerekiyorsa, `CreateInputAssetAsync` ' den döndürülen varlık nesnesindeki `Name` özelliğini kullandığınızdan emin olun. Örneğin, inputAsset.Name. 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Kodlama işinin sonucunu depolamak için bir çıkış varlığı oluşturma

Çıktı [Varlığı](https://docs.microsoft.com/rest/api/media/assets), kodlama işinizin sonucunu depolar. Aşağıdaki işlev bir çıkış varlığının nasıl oluşturulacağını gösterir.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Oluşturulan çıkış varlığının adını diğer yöntemlere geçirmeniz gerekiyorsa, `CreateIOutputAssetAsync` ' den döndürülen varlık nesnesindeki `Name` özelliğini kullandığınızdan emin olun. Örneğin, outputAsset.Name. 

Bu makale söz konusu olduğunda, `outputAsset.Name` değerini `SubmitJobAsync` ve `UploadAudioIntoOutputAsset` işlevlerine geçirin.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Karşıya yüklenen dosyayı kodlayan bir dönüşüm ve iş oluşturma

Media Services’te içerik kodlarken veya işlerken, kodlama ayarlarını bir tarif olarak ayarlamak yaygın bir modeldir. Daha sonra bu tarifi bir videoya uygulamak üzere bir **İş** gönderirsiniz. Her yeni video için yeni işler göndererek, bu tarifi kitaplığınızdaki tüm videolarınıza uygulayacaksanız. Media Services içinde tarif, **Dönüşüm** olarak adlandırılır. Daha fazla bilgi için [Dönüşümler ve İşler](transform-concept.md) konusuna bakın. Bu öğreticide açıklanan örnek, videoyu çeşitli iOS ve Android cihazlarına akışla aktarmak için kodlayan bir tarifi tanımlar. 

Aşağıdaki örnek, bir dönüşüm (yoksa, yoksa) oluşturur.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

Aşağıdaki işlev bir iş gönderir.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>İşin tamamlanmasını bekleyin

İşin tamamlanması biraz sürüyor ve tamamlandığında bildirim almak istiyorsunuz. İşin tamamlanmasını beklemek için Event Grid kullanmanızı öneririz.

İş, genellikle şu durumlardan geçer: **Zamanlanmış**, **sıraya alınmış**, **işleme**, **tamamlandı** (son durum). İş bir hatayla karşılaştıysa **Hata** durumunu alırsınız. İş iptal edilme sürecindeyse **İptal Ediliyor** ve **İptal Edildi** durumunu alırsınız.

Daha fazla bilgi için bkz. [Event Grid olaylarını işleme](reacting-to-media-services-events.md).

## <a name="upload-the-audio-only-mp4-file"></a>Yalnızca ses MP4 dosyasını karşıya yükle

Çıktı varlığına açıklayıcı ses içeren ek salt ses MP4 dosyasını (AAC codec) karşıya yükleyin.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

@No__t-0 işlevine yapılan çağrıya bir örnek aşağıda verilmiştir:

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>. ISM dosyasını düzenleme

Kodlama işiniz bittiğinde, çıkış varlığı kodlama işi tarafından oluşturulan dosyaları içerir. 

1. Azure portal, Media Services hesabınızla ilişkili depolama hesabına gidin. 
1. Çıkış varlığınızın adı ile kapsayıcıyı bulun. 
1. Kapsayıcıda. ISM dosyasını bulun ve **blobu Düzenle** ' ye tıklayın (sağ pencerede). 
1. Açıklayıcı ses içeren karşıya yüklenen salt-tek MP4 dosyası (AAC codec) hakkındaki bilgileri ekleyerek ve tamamlandığında **Kaydet** ' e basarak. ISM dosyasını düzenleyin.

    Açıklayıcı ses izlerini bildirmek için. ISM dosyasına "Erişilebilirlik" ve "rol" parametreleri eklemeniz gerekir. Ses açıklaması olarak bir ses izini bildirmek üzere bu parametreleri doğru bir şekilde ayarlamak sizin sorumluluğunuzdadır. Örneğin, aşağıdaki örnekte gösterildiği gibi, belirli bir ses izi için. ISM dosyasına `<param name="accessibility" value="description" />` ve `<param name="role" value="alternate" />` ekleyin.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>Akış Bulucu alma

Kodlama tamamlandıktan sonra sıradaki adım, çıktı Varlığındaki videoyu yürütmek için istemcilerin kullanımına sunmaktır. Bunu iki adımda gerçekleştirebilirsiniz: ilk olarak, bir [akış Bulucu](https://docs.microsoft.com/rest/api/media/streaminglocators)oluşturun ve ikinci olarak, istemcilerin kullanabileceği akış URL 'lerini oluşturun. 

**Akış Bulucu** oluşturma işlemine yayımlama denir. Varsayılan olarak, **akış Bulucu** , API çağrılarını yaptıktan hemen sonra geçerli olur ve isteğe bağlı başlangıç ve bitiş zamanlarını yapılandırmadıkça silinene kadar sürer. 

Bir [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) oluştururken istenen **StreamingPolicyName** değerini belirtmeniz gerekir. Bu örnekte, temiz (şifrelenmemiş) içeriğin akışını yapacağınız için önceden tanımlı temiz akış ilkesi **PredefinedStreamingPolicy.ClearStreamingOnly** kullanılır.

> [!IMPORTANT]
> Özel bir [akış ilkesi](https://docs.microsoft.com/rest/api/media/streamingpolicies)kullanırken, medya hizmeti hesabınız için sınırlı sayıda ilke kümesi tasarlamalı ve aynı şifreleme seçenekleri ve protokoller gerektiğinde bunları streamingbulucular için yeniden kullanmanız gerekir. Medya hizmeti hesabınızın akış Ilkesi girişi sayısı için bir kotası vardır. Her bir akış bulucu için yeni bir akış Ilkesi oluşturmamalısınız.

Aşağıdaki kod, benzersiz bir locatorName ile işlevi çağırdığınızı varsayar.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Bu konudaki örnek akışı ele alırken, aşamalı indirme yoluyla video teslim etmek için bir akış Bulucu oluşturmak için aynı çağrıyı kullanabilirsiniz.

### <a name="get-streaming-urls"></a>Akış URL'leri alma

Artık [akış bulucunun](https://docs.microsoft.com/rest/api/media/streaminglocators) oluşturulduğuna göre, **Getstreamingurls**Içinde gösterildiği gibi akış URL 'lerini alabilirsiniz. URL oluşturmak için, [akış uç noktası](https://docs.microsoft.com/rest/api/media/streamingendpoints) ana bilgisayar adını ve **akış Bulucu** yolunu birleştirmeniz gerekir. Bu örnekte, *varsayılan* **akış uç noktası** kullanılır. İlk olarak bir medya hizmeti hesabı oluşturduğunuzda, bu *varsayılan* **akış uç noktası** durdurulmuş durumda olacaktır, bu yüzden **Start**'ı çağırmanız gerekir.

> [!NOTE]
> Bu yöntemde, çıkış varlığı için **akış bulucuyu** oluştururken kullanılan locatorname öğesine ihtiyacınız vardır.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Azure Media Player ile test etme

Bu makalede, akışı test etmek için Azure Media Player kullanılmaktadır. 

> [!NOTE]
> Oynatıcı bir https sitesinde barındırılıyorsa, "https" URL’sini güncelleştirdiğinizden emin olun.

1. Bir web tarayıcısı açın ve [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) sayfasına gidin.
2. **URL:** kutusunda, uygulamanızdan ALDıĞıNıZ akış URL değerlerinden birini yapıştırın. 
 
     URL 'yi HLS, Dash veya kesintisiz biçimde yapıştırabilir ve Azure Media Player cihazınızda otomatik olarak kayıttan yürütmek için uygun bir akış protokolüne geçiş yapar.
3. **Oynatıcıyı Güncelleştir** düğmesine basın.

Azure Media Player, test için kullanılabilir, ancak üretim ortamında kullanılmamalıdır. 

## <a name="next-steps"></a>Sonraki adımlar

[Videoları analiz etme](analyze-videos-tutorial-with-api.md)
