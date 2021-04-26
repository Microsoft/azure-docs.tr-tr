---
title: Azure Media Indexer ile medya dosyalarını dizine alma
description: Azure Media Indexer, medya dosyalarınızın içeriğini aranabilir hale getirmenizi ve kapalı açıklamalı alt yazı ve anahtar sözcükler için tam metin dökümü oluşturmanıza olanak sağlar. Bu konuda Media Indexer nasıl kullanılacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 68b72cca319772fa52829c6b95237e15f083a2e7
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962671"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Azure Media Indexer ile medya dosyalarını dizine alma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> Müşterilerin Dizin Oluşturucu v1 ve Dizin Oluşturucu v2, [Media Services v3 Audioanalön ayarlı temel modunu](../latest/analyze-video-audio-files-concept.md)kullanarak geçiş yapmanız önerilir. [Azure Media Indexer](media-services-index-content.md) medya işlemcisi ve [Azure Media Indexer 2 Preview](./legacy-components.md) medya işlemcileri kullanımdan kaldırılıyor. Kullanımdan kaldırma tarihleri için, bu [eski bileşenler](legacy-components.md) konusuna bakın.

Azure Media Indexer, medya dosyalarınızın içeriğini aranabilir hale getirmenizi ve kapalı açıklamalı alt yazı ve anahtar sözcükler için tam metin dökümü oluşturmanıza olanak sağlar. Bir toplu işte bir medya dosyası veya birden çok medya dosyası işleyebilirsiniz.  

İçerik dizin oluştururken, temiz konuşma içeren medya dosyalarını (arka plan müziği, gürültü, efekt veya mikrofon hisleri olmadan) kullandığınızdan emin olun. Uygun içeriğe örnek olarak şunlar verilebilir: kayıtlı toplantılar, seminerler veya sunular. Şu içerikler dizin oluşturmak için uygun olmayabilir: Filmler, TV programları, karışık ses ve ses efektleriyle herhangi bir şey, arka plan gürültüsü olan kötü kaydedilmiş içerik (hisler).

Bir dizin oluşturma işi aşağıdaki çıktıları oluşturabilir:

* Şu biçimlerdeki kapalı açıklamalı altyazı dosyaları: **Ttml** ve **WEBVTT**.
  
    Kapalı açıklamalı altyazı dosyaları, kaynak videodaki konuşmanın ne kadar tanındığını temel alarak bir dizin oluşturma işine işaret eden, recognizme adlı bir etiket içerir.  Kullanılabilirlik için çıkış dosyalarını ekran için, tanınan bir değer kullanabilirsiniz. Düşük puan, ses kalitesi nedeniyle yetersiz dizin oluşturma sonuçları anlamına gelir.
* Anahtar sözcük dosyası (XML).

Bu makalede, **bir varlığı dizine** eklemek ve **birden çok dosyayı indekslemek** için dizin oluşturma işlerinin nasıl oluşturulacağı gösterilmektedir.

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Dizinleme görevleri için yapılandırma ve bildirim dosyalarını kullanma
Bir görev yapılandırması kullanarak, dizin oluşturma görevleriniz için daha fazla ayrıntı belirtebilirsiniz. Örneğin, medya dosyanız için hangi meta verileri kullanacağınızı belirtebilirsiniz. Bu meta veriler, dil motoru tarafından sözlüğünü genişletmek için kullanılır ve konuşma tanıma doğruluğunu büyük ölçüde geliştirir.  İstediğiniz çıkış dosyalarınızı da belirtebilirsiniz.

Ayrıca, bir bildirim dosyası kullanarak birden çok medya dosyasını aynı anda işleyebilirsiniz.

Daha fazla bilgi için bkz. [Azure Media Indexer Için görev önayar](./legacy-components.md).

## <a name="index-an-asset"></a>Varlık dizini oluşturma
Aşağıdaki yöntem bir ortam dosyasını varlık olarak yükler ve varlığı dizine eklemek için bir iş oluşturur.

Hiçbir yapılandırma dosyası belirtilmemişse, medya dosyasının tüm varsayılan ayarlarla dizini oluşturulur.

```csharp
    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
```

<!-- __ -->
### <a name="output-files"></a><a id="output_files"></a>Çıkış dosyaları
Varsayılan olarak, bir dizin oluşturma işi aşağıdaki çıkış dosyalarını oluşturur. Dosyalar ilk çıkış varlığı içinde depolanır.

Birden fazla giriş medyası dosyası olduğunda, Dizin Oluşturucu, ' JobResult.txt ' adlı iş çıktıları için bir bildirim dosyası oluşturur. Her giriş medya dosyası için, elde edilen TTML, WebVTT ve anahtar sözcük dosyaları sırayla numaralandırılır ve "alias" kullanılarak adlandırılır.

| Dosya adı | Description |
| --- | --- |
| **Inputfilename. ttml**<br/>**Inputfilename. VTT** |TTML ve WebVTT biçimlerinde kapalı açıklamalı altyazı (CC) dosyaları.<br/><br/>Ses ve video dosyalarını işitme engelli kişiler için erişilebilir hale getirmek için kullanılabilirler.<br/><br/>Kapalı açıklamalı altyazı dosyaları, kaynak videodaki konuşmanın ne kadar tanındığını temel alarak bir dizin oluşturma işi gösteren <b>Recognizbilme</b> adlı bir etiket içerir.  Kullanılabilirlik için çıkış dosyalarını ekran için, <b>tanınan</b> bir değer kullanabilirsiniz. Düşük puan, ses kalitesi nedeniyle yetersiz dizin oluşturma sonuçları anlamına gelir. |
| **InputFileName.kw.xml<br/> InputFileName.info** |Anahtar sözcük ve bilgi dosyaları. <br/><br/>Anahtar sözcük dosyası, sıklık ve konum bilgileri ile konuşma içeriğinden ayıklanan anahtar sözcükleri içeren bir XML dosyasıdır. <br/><br/>Bilgi dosyası, tanınan her terim hakkında ayrıntılı bilgiler içeren bir düz metin dosyasıdır. İlk satır özeldir ve recognizme Puanını içerir. Sonraki her satır, şu verilerin sekmeyle ayrılmış bir listesidir: başlangıç zamanı, bitiş zamanı, sözcük/tümcecik, güven. Süreler saniye cinsinden verilir ve güven 0-1 ' dan bir sayı olarak verilir. <br/><br/>Örnek satır: "1,20 1,45 Word 0,67" <br/><br/>Bu dosyalar, konuşma analizlerini gerçekleştirmek için veya Bing, Google ya da Microsoft SharePoint gibi arama altyapılarına açık hale getirmek veya daha fazla ilgili reklamları sunmak için kullanılması gibi çeşitli amaçlarla kullanılabilir. |
| **JobResult.txt** |Çıkış bildirimi, yalnızca birden çok dosya dizinlenirken bulunur ve aşağıdaki bilgileri içerir:<br/><br/><table border="1"><tr><th>InputFile</th><th>Diğer ad</th><th>Ortam uzunluğu</th><th>Hata</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Tüm giriş medyası dosyaları başarıyla dizinlenmezse, dizin oluşturma işi 4000 hata koduyla başarısız olur. Daha fazla bilgi için bkz. [hata kodları](#error_codes).

## <a name="index-multiple-files"></a>Birden çok dosya dizini oluştur
Aşağıdaki yöntem, birden çok medya dosyasını bir varlık olarak yükler ve bir toplu işteki tüm bu dosyaları dizine almak için bir iş oluşturur.

". Lst" uzantısına sahip bir bildirim dosyası oluşturulup varlığa karşıya yüklenir. Bildirim dosyası tüm varlık dosyalarının listesini içerir. Daha fazla bilgi için bkz. [Azure Media Indexer Için görev önayar](./legacy-components.md).

```csharp
    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }
```

### <a name="partially-succeeded-job"></a>Kısmen başarılı Iş
Tüm giriş medyası dosyaları başarıyla dizinlenmez, dizin oluşturma işi 4000 hata koduyla başarısız olur. Daha fazla bilgi için bkz. [hata kodları](#error_codes).

Aynı çıkışlar (başarılı işler olarak) oluşturulur. Hata sütunu değerlerine göre hangi giriş dosyalarının başarısız olduğunu öğrenmek için çıkış bildirimi dosyasına başvurabilirsiniz. Başarısız olan giriş dosyaları için, elde edilen TTML, WebVTT ve anahtar sözcük dosyaları oluşturulmaz.

### <a name="task-preset-for-azure-media-indexer"></a><a id="preset"></a> Azure Media Indexer için görev önayarı
Azure Media Indexer işleme, görevin yanı sıra isteğe bağlı bir görev ön ayarı sağlayarak özelleştirilebilir.  Bu yapılandırma XML biçimi aşağıda açıklanmıştır.

| Name | Gerektirme | Description |
| --- | --- | --- |
| **girişinin** |yanlış |Dizin eklemek istediğiniz varlık dosyaları.</p><p>Azure Media Indexer, şu medya dosyası biçimlerini destekler: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Dosya adlarını **giriş** öğesinin **ad** veya **liste** özniteliğinde (aşağıda gösterildiği gibi) belirtebilirsiniz. Hangi varlık dosyasının dizine alınmayı belirtmezseniz, birincil dosya çekilir. Birincil varlık dosyası ayarlanmamışsa, giriş varlığının ilk dosyası dizine alınır.</p><p>Varlık dosya adını açıkça belirtmek için şunu yapın:<br/>`<input name="TestFile.wmv">`<br/><br/>Aynı zamanda birden çok varlık dosyasını aynı anda dizinde (en fazla 10 dosya). Bunu yapmak için:<br/><br/><ol class="ordered"><li><p>Bir metin dosyası (bildirim dosyası) oluşturun ve bir. lst uzantısı verin. </p></li><li><p>Bu bildirim dosyasına giriş varlığınızın tüm varlık dosya adlarının listesini ekleyin. </p></li><li><p>Bildirim dosyasını varlığa ekleyin (karşıya yükleyin).  </p></li><li><p>Girişin liste özniteliğinde bildirim dosyasının adını belirtin.<br/>`<input list="input.lst">`</li></ol><br/><br/>Note: bildirim dosyasına 10 ' dan fazla dosya eklerseniz, dizin oluşturma işi 2006 hata koduyla başarısız olur. |
| **veriyi** |yanlış |Sözlük uyarlama için kullanılan belirtilen varlık dosyalarının meta verileri.  Uygun isimler gibi standart olmayan sözlük sözcüklerini tanımak için dizin oluşturucunun hazırlanması yararlı olur.<br/>`<metadata key="..." value="..."/>` <br/><br/>Önceden tanımlanmış **anahtarlar** için **değerler** sağlayabilirsiniz. Şu anda aşağıdaki anahtarlar desteklenir:<br/><br/>"title" ve "Description"-iş için dil modelinin ince ayar ve konuşma tanıma doğruluğunu iyileştirecek sözlük uyarlaması için kullanılır.  Temel Internet değerleri, dizin oluşturma göreviniz süresince iç sözlüğü genişletmek için içeriği kullanarak bağlamsal olarak ilgili metin belgelerini bulmak için arama yapar.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **özelliklerinde** <br/><br/> Sürüm 1,2 ' ye eklenmiştir. Şu anda desteklenen tek özellik konuşma tanıma ("ASR") ' dir. |yanlış |Konuşma tanıma özelliği aşağıdaki ayarlar anahtarlarına sahiptir:<table><tr><th><p>Anahtar</p></th>        <th><p>Description</p></th><th><p>Örnek değer</p></th></tr><tr><td><p>Dil</p></td><td><p>Çoklu ortam dosyasında tanınmak için doğal dil.</p></td><td><p>İngilizce, Ispanyolca</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>istenen çıkış resim yazısı biçimlerinin noktalı virgülle ayrılmış listesi (varsa)</p></td><td><p>ttml; WEBVTT</p></td></tr><tr><td><p></p></td><td><p> </p></td><td><p>Değeri Yanlýþ</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>Anahtar sözcüğünün XML dosyası gerekip gerekmediğini belirten bir Boole bayrağı.</p></td><td><p>Değeri Yanlýþ. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>Tam açıklamalı alt yazıların zorlanıp zorlanmayacağını belirten bir Boole bayrağı (güven düzeyinden bağımsız olarak).  </p><p>Varsayılan değer false şeklindedir; bu durumda %50 ' den az güvenilirlik düzeyi olan kelimeler ve deyimler son açıklamalı alt yazı çıktılarından çıkarılır ve üç nokta ("...") ile değiştirilmiştir.  Üç nokta üst yazı kalitesi denetimi ve denetimi için faydalıdır.</p></td><td><p>Değeri Yanlýþ. </p></td></tr></table> |

### <a name="error-codes"></a><a id="error_codes"></a>Hata kodları
Bir hata durumunda, Azure Media Indexer aşağıdaki hata kodlarından birini yeniden raporlemelidir:

| Kod | Name | Olası nedenler |
| --- | --- | --- |
| 2000 |Geçersiz yapılandırma |Geçersiz yapılandırma |
| 2001 |Geçersiz giriş varlıkları |Eksik giriş varlıkları veya boş varlık. |
| 2002 |Geçersiz bildirim |Bildirim boş veya bildirim geçersiz öğeler içeriyor. |
| 2003 |Medya dosyası indirilemedi |Bildirim dosyasında geçersiz URL. |
| 2004 |Desteklenmeyen protokol |Medya URL 'SI protokolü desteklenmiyor. |
| 2005 |Desteklenmeyen dosya türü |Giriş medya dosyası türü desteklenmiyor. |
| 2006 |Çok fazla giriş dosyası |Giriş bildiriminde 10 ' dan fazla dosya vardır. |
| 3000 |Medya dosyasının kodu çözülemedi |Desteklenmeyen medya codec bileşeni <br/>veya<br/> Bozuk medya dosyası <br/>veya<br/> Giriş medyasında ses akışı yok. |
| 4000 |Toplu dizin oluşturma kısmen başarılı oldu |Bazı giriş medya dosyalarından dizin oluşturulamadı. Daha fazla bilgi için bkz. <a href="#output_files">çıkış dosyaları</a>. |
| other |İç hatalar |Lütfen destek ekibine başvurun. indexer@microsoft.com |

## <a name="supported-languages"></a><a id="supported_languages"></a>Desteklenen diller
Şu anda Ingilizce ve Ispanyolca dilleri desteklenmektedir.  

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>İlgili bağlantılar
[Azure Media Services Analytics genel bakışı](./legacy-components.md)

[Azure Media Indexer 2 Preview ile medya dosyalarını dizine alma](./legacy-components.md)