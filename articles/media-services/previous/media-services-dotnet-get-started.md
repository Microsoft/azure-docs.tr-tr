---
title: .NET kullanarak isteğe bağlı içerik göndermeye başlama | Microsoft Belgeleri
description: Bu öğreticide, .NET’i kullanarak Azure Media Services ile isteğe bağlı bir içerik teslim uygulaması gerçekleştirilmesinin adımları açıklanmaktadır.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 11754e8e98e13cffefaf4a8c1fa08bc60d650105
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "69016558"
---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>.NET SDK kullanarak isteğe bağlı içerik göndermeye başlama  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

Bu öğretici, Azure Media Services .NET SDK'sı kullanarak Azure Media Services (AMS) uygulaması ile temel bir İsteğe Bağlı Video (VoD) içerik teslim hizmeti uygulamanın adımlarını açıklar.

## <a name="prerequisites"></a>Önkoşullar

Öğreticiyi tamamlamak için aşağıdakiler gereklidir:

* Bir Azure hesabı. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com/pricing/free-trial/).
* Bir Media Services hesabı. Bir Media Services hesabı oluşturmak için bkz. [Media Services hesabı oluşturma](media-services-portal-create-account.md).
* .NET framework 4.0 veya sonraki sürümü.
* Visual Studio.

Bu öğretici aşağıdaki görevleri içerir:

1. Akış uç noktalarını başlatın (Azure portalını kullanarak).
2. Visual Studio projesi oluşturun ve yapılandırın.
3. Media Services hesabına bağlanın.
2. Bir video dosyası yükleyin.
3. Kaynak dosyayı uyarlamalı bit hızlı bir MP4 dosyaları grubuna kodlayın.
4. Varlığı yayımlayın, akış ve aşamalı indirme URL’lerini alın.  
5. İçeriğinizi oynatın.

## <a name="overview"></a>Genel Bakış
Bu öğreticide, .NET için Azure Media Services (AMS) SDK’sını kullanarak bir İsteğe Bağlı Video (VoD) içerik teslim uygulaması gerçekleştirilmesinin adımları açıklanmaktadır.

Öğretici, temel Media Services iş akışını ve Media Services geliştirmek için gereken en genel programlama nesnelerini ve görevleri tanıtır. Öğreticiyi tamamladığınızda, yükleyip kodlayarak ardından indirdiğiniz örnek bir medya dosyasını akışla aktarabilecek veya aşamalı olarak indirebileceksiniz.

### <a name="ams-model"></a>AMS modeli

Aşağıdaki resimde Media Services OData modeliyle VoD uygulamaları geliştirirken en sık kullanılan nesnelerin bazıları gösterilmektedir.

Resmi tam boyutlu görüntülemek için tıklayın.  

<a href="./media/media-services-dotnet-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-dotnet-get-started/media-services-overview-object-model-small.png"></a> 

Modelin tamamını [buradan](https://media.windows.net/API/$metadata?api-version=2.15) görüntüleyebilirsiniz.  

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Azure portal ile akış uç noktalarını başlatma

Azure Media Services ile çalışırken en sık karşılaşılan senaryolardan biri bit hızı uyarlamalı akış iletmektir. Media Services, bu akış biçimlerinin her birinin önceden paketlenmiş sürümlerini depolamanıza gerek kalmadan, uyarlamalı bit hızı MP4 ile kodlanmış içeriğinizi Media Services tarafından desteklenen akış biçimlerinde (MPEG DASH, HLS, Kesintisiz Akış) tam vaktinde göndermenize olanak tanıyan dinamik paketleme özelliğine sahiptir.

>[!NOTE]
>AMS hesabınız oluşturulduğunda hesabınıza **Durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçerik akışını başlatmak ve dinamik paketleme ile dinamik şifrelemeden yararlanmak için içerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumda olması gerekir.

Akış uç noktasını başlatmak için aşağıdakileri yapın:

1. [Azure portal](https://portal.azure.com/)’da oturum açın.
2. Ayarlar penceresinde, Akış uç noktaları'na tıklayın.
3. Varsayılan akış uç noktasına tıklayın.

    VARSAYILAN AKIŞ UÇ NOKTASI AYRINTILARI penceresi görüntülenir.

4. Başlat simgesine tıklayın.
5. Yaptığınız değişiklikleri kaydetmek için Kaydet düğmesine tıklayın.

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio projesi oluşturup yapılandırma

1. Geliştirme ortamınızı kurun ve app.config dosyanızı [.NET ile Media Services geliştirme](media-services-dotnet-how-to-use.md) bölümünde açıklandığı gibi bağlantı bilgileriyle doldurun. 
2. Yeni bir klasör oluşturun (yerel sürücünüzün herhangi bir yerinde) ve kodlayıp akışla aktarmak veya aşamalı indirmek istediğiniz bir .mp4 dosyasını buraya kopyalayın. Bu örnekte "C:\VideoFiles" yolu kullanılmaktadır.

## <a name="connect-to-the-media-services-account"></a>Media Services hesabına bağlanma

.NET ile Media Services’i kullanırken, Media Services programlama görevlerinin çoğu için **CloudMediaContext** sınıfını kullanmalısınız: Media Services hesabına bağlanma; şu nesneleri oluşturma, güncelleştirme, silme ve bunlara erişme: varlıklar, varlık dosyaları, işler, erişim ilkeleri, bulucular vb.

Varsayılan program sınıfının üzerine aşağıdaki kodu yazın: Kod, bağlantı değerlerinin App.config dosyasından nasıl okunacağını ve Media Services’e bağlanmak amacıyla **CloudMediaContext** nesnesinin nasıl oluşturulacağını gösterir. Daha fazla bilgi için bkz. [Media Services API'sine bağlanma](media-services-use-aad-auth-to-access-ams-api.md).

Dosya adını ve yolunu medya dosyanıza göre güncelleştirmeyi unutmayın.

**Ana** işlev, bu bölümün sonraki kısımlarında açıklanacak olan yöntemleri çağırır.

> [!NOTE]
> Tüm işlevler için bu makalede bahsedilen tanımları ekleyene kadar derleme hatası alırsınız.

```csharp
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Add calls to methods defined in this section.
            // Make sure to update the file name and path to where you have your media file.
            IAsset inputAsset =
            UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

            IAsset encodedAsset =
            EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

            PublishAssetGetURLs(encodedAsset);
        }
        catch (Exception exception)
        {
            // Parse the XML error message in the Media Services response and create a new
            // exception with its content.
            exception = MediaServicesExceptionParser.Parse(exception);

            Console.Error.WriteLine(exception.Message);
        }
        finally
        {
            Console.ReadLine();
        }
        }
```

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Yeni varlık oluşturma ve video dosyası yükleme

Media Services’de, dijital dosyalar bir varlığa yüklenir (veya alınır). **Varlık** varlığı video, ses, görüntüler, küçük resim koleksiyonları, metin parçaları ve kapalı açıklamalı alt yazı dosyaları (ve bu dosyalar hakkındaki meta veriler) içerebilir.  Dosyalar yüklendiğinde, içeriğiniz sonraki işleme ve akışla aktarma faaliyetleri için güvenli bir şekilde bulutta depolanmış olur. Varlık içindeki dosyalara **Varlık Dosyaları** adı verilir.

Aşağıda tanımlanan **UploadFile** yöntemi, **CreateFromFile** yöntemini (.NET SDK Uzantılarında tanımlanmıştır) çağırır. **CreateFromFile**, belirtilen kaynak dosyasının yüklendiği yeni bir varlık oluşturur.

**CreateFromFile** yöntemi, aşağıdaki varlık oluşturma seçeneklerden birini belirtmenize olanak sağlayan **AssetCreationOptions**’ı alır:

* **Hiçbiri**: Şifreleme kullanılmaz. Varsayılan değer budur. Bu seçeneği kullandığınızda, içeriğinizin aktarım sırasında ve depolama alanında beklerken korunmadığını unutmayın.
  Aşamalı indirme kullanarak bir MP4 iletmeyi planlıyorsanız bu seçeneği kullanın.
* **StorageEncrypted**: Temiz içeriğinizi yerel olarak Gelişmiş Şifreleme Standardı (AES) 256 bit şifreleme kullanarak şifrelemek için bu seçeneği kullanın. Şifrelenen içerik ardından Azure Storage’a yüklenerek bekleme durumunda depolanır. Depolama Şifrelemesi ile korunan varlıklar, kodlamadan önce otomatik olarak şifrelenerek şifrelenmiş bir dosya sistemine yerleştirilir ve yeni bir çıktı varlığı şeklinde geri yüklenmeden önce isteğe bağlı olarak yeniden şifrelenir. Depolama Şifrelemesinin birincil kullanım nedeni, yüksek kaliteli girdi medya dosyalarınızın güvenliğini güçlü şifrelemeyle diskte bekleyen konumda sağlamak istediğiniz durumdur.
* **CommonEncryptionProtected**: Zaten Ortak Şifreleme veya PlayReady DRM ile şifrelenip korunan bir içerik (örneğin, PlayReady DRM ile korunan Kesintisiz Akış) yüklüyorsanız bu seçeneği kullanın.
* **EnvelopeEncryptionProtected**: AES ile şifrelenmiş HLS yüklüyorsanız bu seçeneği kullanın. Dosyaların Transform Manager tarafından kodlanmış ve şifrelenmiş olması gerektiğini unutmayın.

**CreateFromFile** yöntemi, dosyanın yükleme durumunu bildirmek üzere bir geri çağırma belirtmenize de olanak sağlar.

Aşağıdaki örnekte, varlık seçeneği olarak **Hiçbiri** belirliyoruz.

Program sınıfına aşağıdaki yöntemi ekleyin.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Kaynak dosyayı uyarlamalı bit hızlı bir MP4 dosyaları grubuna kodlama
Varlıklar Media Services’e alındıktan sonra medyaya, istemcilere teslim edilmeden önce kodlama, biçimini değiştirme, filigran ekleme ve benzeri işlemler uygulanabilir. Bu etkinlikler, yüksek performans ve kullanılabilirlik sağlamak için birden fazla arka plan rol örneğinde zamanlanır ve çalıştırılır. Bu etkinliklere İşler adı verilir ve her bir İş, Varlık dosyası üzerinde asıl işi yapan atomik Görevlerden oluşur.

Daha önce belirtildiği gibi, Azure Media Services ile çalışırken en sık karşılaşılan senaryolardan biri, istemcilerinize bit hızı uyarlamalı akış iletmektir. Media Services bir uyarlamalı bit hızı MP4 dosyası kümesini dinamik olarak şu biçimlerden birine paketleyebilir: HTTP Canlı Akışı (HLS), Kesintisiz Akış ve MPEG DASH.

Dinamik paketlemeden yararlanmak için, ara (kaynak) dosyanızı bit hızı uyarlamalı bir MP4 veya Kesintisiz Akış dosyaları kümesine kodlamanız veya dosyanın kodlamasını dönüştürmeniz gerekir.  

Aşağıdaki kod, bir kodlama işinin nasıl gönderileceğini gösterir. İş, ara dosyayı **Medya Kodlayıcı Standart**’ını kullanarak bir grup bit hızı uyarlamalı MP4’e kodlamasını dönüştüren bir görev içerir. Kod işi gönderir ve tamamlanana kadar bekler.

İş tamamlandığında varlığınızı akışla aktarabilir veya kodlama dönüştürmenin sonucu olarak oluşturulan MP4 dosyalarını aşamalı indirebilirsiniz.

Program sınıfına aşağıdaki yöntemi ekleyin.

```csharp
    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "Adaptive Streaming",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }
```

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Varlığı yayımlayıp akış ve aşamalı indirme URL’lerini alma

Bir varlığı akışla aktarmak veya indirmek için söz konusu varlığı önce bir bulucu oluşturarak “yayımlamak” gerekir. Bulucular varlıkta bulunan dosyalara erişim imkanı sağlar. Media Services iki tür bulucuyu destekler: Medya dosyalarını indirmek için kullanılan, medya akışını (örneğin, MPEG DASH, HLS veya Kesintisiz Akış) ve erişim Imzası (SAS) Konumlandırıcı için kullanılan OnDemandOrigin Konumlandırıcı.

### <a name="some-details-about-url-formats"></a>URL biçimleri hakkında bazı ayrıntılar

Bulucuları oluşturduktan sonra, dosyalarınızı akışla aktarmak veya indirmek için kullanılacak URL'leri oluşturabilirsiniz. Bu öğreticideki örnek, uygun tarayıcılara yapıştırabileceğiniz URL'ler oluşturur. Bu bölümde yalnızca farklı biçimlerin neye benzediğini gösteren kısa örnekler verilir.

#### <a name="a-streaming-url-for-mpeg-dash-has-the-following-format"></a>MPEG DASH’e ilişkin bir akış URL'si aşağıdaki biçime sahiptir:

{akış uç noktası adı-media services hesabı adı}.streaming.mediaservices.windows.net/{konum kimliği}/{dosya adı}.ism/Manifest **(format=mpd-time-csf)**

#### <a name="a-streaming-url-for-hls-has-the-following-format"></a>HLS’ye ilişkin bir akış URL'si aşağıdaki biçime sahiptir:

{akış uç noktası adı-media services hesabı adı}.streaming.mediaservices.windows.net/{konum kimliği}/{dosya adı}.ism/Manifest **(format=m3u8-aapl)**

#### <a name="a-streaming-url-for-smooth-streaming-has-the-following-format"></a>Kesintisiz Akışa ilişkin bir akış URL'si aşağıdaki biçime sahiptir:

{akış uç noktası adı-media services hesabı adı}.streaming.mediaservices.windows.net/{konum kimliği}/{dosya adı}.ism/Manifest


#### <a name="a-sas-url-used-to-download-files-has-the-following-format"></a>Dosya indirmek için kullanılan bir SAS URL'si aşağıdaki biçime sahiptir:

{blob kapsayıcısı adı}/{varlık adı}/{dosya adı}/{SAS imzası}

Media Services .NET SDK uzantıları, yayımlanan varlık için biçimlendirilmiş URL'ler döndüren kullanışlı yardımcı yöntemler sağlar.

Aşağıdaki kod, .NET SDK Uzantıları kullanarak bulucu oluşturur, akış ve aşamalı indirme URL'leri alır. Kod ayrıca yerel bir klasöre nasıl dosya indirileceğini gösterir.

Program sınıfına aşağıdaki yöntemi ekleyin.

```csharp
    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }
```

## <a name="test-by-playing-your-content"></a>İçeriğiniz oynatarak test etme

Önceki bölümde tanımlanan programı çalıştırdığınızda konsol penceresinde aşağıdakine benzer URL'ler görüntülenir.

Uyarlamalı akış URL'leri:

Kesintisiz Akış

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

Aşamalı indirme URL'leri (ses ve video).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Videonuzun akışını yapmak için URL'nizi [Azure Media Services Player](https://aka.ms/azuremediaplayer)'daki URL metin kutusuna yapıştırın.

Aşamalı indirmeyi test etmek için bir tarayıcıya (örneğin Internet Explorer, Chrome veya Safari) bir URL yapıştırın.

Daha fazla bilgi için aşağıdaki konulara bakın:

- [İçeriğinizi mevcut oynatıcılarda oynatma](media-services-playback-content-with-existing-players.md)
- [Video oynatıcı uygulamaları geliştirme](media-services-develop-video-players.md)
- [DASH.js ile MPEG-DASH Uyarlamalı Akış Videosunu bir HTML5 Uygulamasına ekleme](media-services-embed-mpeg-dash-in-html5.md)

## <a name="download-sample"></a>Örnek indirme
Aşağıdaki kod örneği, bu öğreticide oluşturduğunuz kodu içerir: [örnek](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="next-steps"></a>Sonraki Adımlar

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]



<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: https://go.microsoft.com/fwlink/?linkid=255386
[Portal]: https://portal.azure.com/
