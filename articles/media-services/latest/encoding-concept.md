---
title: Video ve ses Media Services kodlama
titleSuffix: Azure Media Services
description: Azure Media Services ile video ve ses kodlamasını öğrenin.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d3de307b1dadf302004fa9fd02c8cf23e36b3046
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73574272"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Video ve ses Media Services kodlama

Media Services ' deki terim kodlaması, dijital video ve/veya ses içeren dosyaları bir standart biçimden diğerine dönüştürme işlemi için geçerlidir, (a), dosyaların boyutunu azalttığından ve/veya (b), ile uyumlu bir biçim üretir çok çeşitli cihaz ve uygulama. Bu işlem, video sıkıştırması veya kodlama kodlaması olarak da adlandırılır. Kavramlarla ilgili daha fazla bilgi için [veri sıkıştırmaya](https://en.wikipedia.org/wiki/Data_compression) ve [kodlama ve dönüştürme nedir?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) bölümüne bakın.

Videolar genellikle, [aşamalı indirme](https://en.wikipedia.org/wiki/Progressive_download) ya da [Uyarlamalı bit hızı akışı](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)aracılığıyla cihazlara ve uygulamalara dağıtılır.

* Aşamalı indirme ile teslim etmek için Azure Media Services kullanarak bir dijital medya dosyasını (Mezzanine), [H.](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) ve codec bileşeniyle kodlanmış video ve [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) ile kodlanan ses içeren bir [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) dosyasına dönüştürebilirsiniz. bileşeni. Bu MP4 dosyası Depolama hesabınızdaki bir varlığa yazılır. Dosyayı doğrudan indirmek için Azure depolama API 'Lerini veya SDK 'Larını (örneğin, [depolama REST API](../../storage/common/storage-rest-api-auth.md) veya [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) kullanabilirsiniz. Depolama alanında belirli bir kapsayıcı adıyla çıkış varlığı oluşturduysanız, bu konumu kullanın. Aksi takdirde, [varlık kapsayıcısı URL 'lerini listelemek](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)için Media Services kullanabilirsiniz. 
* İçeriği Uyarlamalı bit hızı akışı ile teslim etmek üzere hazırlamak için, Mezzanine dosyasının birden çok bitücret (yüksek-düşük) ile kodlanmış olması gerekir. Kaliteyi düzgün şekilde geçişini sağlamak için, bit hızı düşürüldü, videonun çözümlenmesi düşürüldü. Bu, bir çözüm (bkz. [otomatik olarak üretilen Uyarlamalı bit hızı el](autogen-bitrate-ladder.md)ile) adlı bir kodlama. Media Services kullanarak, Mezzanine dosyalarını birden çok bit hızında kodlamak için kullanabilirsiniz. Bunu yaparken, Depolama hesabınızdaki bir varlığa yazılan MP4 dosyaları ve ilişkili akış yapılandırma dosyaları kümesi alacaksınız. Daha sonra, videoyu [MPEG-Dash](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) ve [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming)gibi akış protokolleriyle teslim etmek Için Media Services ' de [dinamik paketleme](dynamic-packaging-overview.md) özelliğini kullanabilirsiniz. Bu, bir [akış Bulucu](streaming-locators-concept.md) oluşturmanız ve desteklenen protokollere karşılık gelen akış URL 'leri oluşturmanızı gerektirir. Bu, daha sonra özelliklerine göre cihazlara/uygulamalara devredilebilir.

Aşağıdaki diyagramda, dinamik paketleme ile isteğe bağlı kodlama için iş akışı gösterilmektedir.

![Dinamik paketleme ile isteğe bağlı kodlama için iş akışı](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Bu konu, içeriğinizi Media Services v3 ile nasıl kodlayabileceğine kılavuzluk sağlar.

## <a name="transforms-and-jobs"></a>Dönüşümler ve işler

Media Services v3 ile kodlamak için bir [dönüşüm](https://docs.microsoft.com/rest/api/media/transforms) ve [iş](https://docs.microsoft.com/rest/api/media/jobs)oluşturmanız gerekir. Dönüştürme, kodlama ayarlarınız ve çıktılar için bir tarif tanımlar; iş, tarif eden bir örneğidir. Daha fazla bilgi için [Dönüşümler ve İşler](transforms-jobs-concept.md) konusuna bakın.

Media Services ile kodlarken, kodlayıcıların giriş medya dosyalarının nasıl işleneceğini söylemek için önayarları kullanın. Örneğin, video çözünürlüğünü ve/veya kodlanmış içerikte istediğiniz ses kanalı sayısını belirtebilirsiniz.

Sektörde en iyi uygulamaları temel alan önerilen yerleşik ön ayarlardan biriyle hızlı bir şekilde çalışmaya başlayabilir veya belirli bir senaryoyu veya cihaz gereksinimlerinizi hedeflemek için özel bir önayar oluşturmayı tercih edebilirsiniz. Daha fazla bilgi için bkz. [özel dönüşümle kodlama](customize-encoder-presets-how-to.md).

2019 Ocak 'tan başlayarak, MP4 dosyaları üretmek için Media Encoder Standard ile kodlarken, yeni bir. MPI dosyası oluşturulur ve çıkış varlığına eklenir. Bu MPı dosyası, [dinamik paketleme](dynamic-packaging-overview.md) ve akış senaryoları için performansı iyileştirmeye yöneliktir.

> [!NOTE]
> MPı dosyasını değiştirmemeniz veya kaldırmanız ya da bu dosyanın var olan (veya olmayan) hizmetinize herhangi bir bağımlılığı almanız gerekmez.

### <a name="creating-job-input-from-an-https-url"></a>HTTPS URL 'sinden iş girişi oluşturma

Videolarınızı işlemek üzere Iş gönderdiğinizde, Media Services Giriş videosunu nerede bulacağınızı bildirmeniz gerekir. Seçeneklerden biri, bir HTTPS URL 'sini iş girişi olarak belirtmektir. Şu anda Media Services v3, HTTPS URL 'Leri üzerinden öbekli aktarım kodlamasını desteklemez.

#### <a name="examples"></a>Örnekler

* [.NET ile HTTPS URL 'sinden kodlama](stream-files-dotnet-quickstart.md)
* [REST ile HTTPS URL 'sinden kodlama](stream-files-tutorial-with-rest.md)
* [CLı ile HTTPS URL 'sinden kodlama](stream-files-cli-quickstart.md)
* [Node. js ile HTTPS URL 'sinden kodlama](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Yerel bir dosyadan iş girişi oluşturma

Giriş videosu bir medya hizmeti varlığı olarak depolanabilir, bu durumda bir dosyayı temel alan bir giriş varlığı (yerel olarak veya Azure Blob depolama alanında depolanır) oluşturabilirsiniz.

#### <a name="examples"></a>Örnekler

[Yerleşik ön ayarları kullanarak yerel bir dosyayı kodla](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Alt kırpmayla iş girişi oluşturma

Bir videoyu kodlarken, kaynak dosyayı kırpmak veya kırpmak ve giriş videosunun yalnızca istenen kısmına sahip bir çıktı üretmek için de belirtebilirsiniz. Bu işlev, [Builtınstandardencoderönayar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ön ayarları veya [Standardencoderönayar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) önayarları kullanılarak oluşturulan [dönüşümlerle](https://docs.microsoft.com/rest/api/media/transforms) birlikte kullanılır.

İsteğe bağlı veya Canlı Arşiv (kaydedilmiş bir olay) için tek bir klibe sahip bir [iş](https://docs.microsoft.com/rest/api/media/jobs/create) oluşturmayı belirtebilirsiniz. İş girişi bir varlık veya HTTPS URL 'SI olabilir.

> [!TIP]
> Videoyu yeniden kodlamadan videonuzun bir alt LIP akışını yapmak istiyorsanız, [dinamik paketlemede ön filtreleme bildirimleri](filters-dynamic-manifest-overview.md)kullanmayı göz önünde bulundurun.

#### <a name="examples"></a>Örnekler

Örneklere bakın:

* [.NET ile videoyu alt kırpın](subclip-video-dotnet-howto.md)
* [REST ile videoyu alt kırpın](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Yerleşik ön ayarlar

Media Services aşağıdaki yerleşik kodlama önayarlarını destekler:  

### <a name="builtinstandardencoderpreset"></a>Builtınstandardencoderönayar

[Builtınstandencoderönayar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) , giriş videosunu standart kodlayıcıyla kodlamak için yerleşik bir ön ayar ayarlamak üzere kullanılır.

Şu ön ayarlar şu anda destekleniyor:

- **Encodernamedönayar. AACGoodQualityAudio**: yalnızca 192 Kbps hızında kodlanmış tek bir MP4 dosyası üretir.
- **Encodernamedönayar. Uyarlantivestreaming** (önerilir): daha fazla bilgi için bkz. [bit hızı el ile oluşturma](autogen-bitrate-ladder.md).
- **Encodernamedönayar. ContentAwareEncodingExperimental**: içeriğe duyarlı kodlama için deneysel önayar gösterir. Herhangi bir giriş içeriği verildiğinde, hizmet en iyi katman sayısını ve uyarlamalı akış gönderimi için uygun bit hızı ve çözüm ayarlarını otomatik olarak saptamaya çalışır. Temel algoritmalar zaman içinde gelişmeye devam edecektir. Çıktı, video ve ses Aralanmış MP4 dosyaları içerir. Daha fazla bilgi için bkz. [içerik kullanan kodlama Için deneysel önayar](cae-experimental.md).
- **Encodernamedönayar. H264MultipleBitrate1080p**: 6000 kbps 'den 400 Kbps 'e ve stereo AAC ses arasında değişen sekiz GOP hizalanmış MP4 dosyası kümesi oluşturur. Çözüm, 1080p ' de başlar ve 360p 'ye gider.
- **Encodernamedönayar. H264MultipleBitrate720p**: altı GOP hizalanmış MP4 dosyası kümesi oluşturur ve 3400 kbps ile 400 Kbps arasında ve stereo AAC sesde vardır. Çözüm 720p ' de başlar ve 360p 'ye gider.
- **Encodernamedönayar. H264MultipleBitrateSD**: beş adet GOP hizalanmış MP4 dosyası kümesi oluşturur ve 1600 kbps ila 400 Kbps ve stereo AAC sestir. Çözüm 480p 'de başlar ve 360p 'ye gider.
- **Encodernamedönayar. H264SingleBitrate1080p**: videonun, 6750 kbps ve 1080 piksellik bir resim yüksekliği ile KODLANDıĞı bir MP4 dosyası üretir ve stereo ses, 64 kbps 'de AAC-LC codec bileşeniyle kodlanır.
- **Encodernamedönayar. H264SingleBitrate720p**: videonun, 4500 kbps ve 720 piksellik bir resim yüksekliği ile KODLANDıĞı bir MP4 dosyası üretir ve stereo ses, 64 kbps 'de AAC-LC codec bileşeniyle kodlanır.
- **Encodernamedönayar. H264SingleBitrateSD**: videonun, 2200 kbps ve 480 piksellik bir resim yüksekliği ile KODLANDıĞı bir MP4 dosyası üretir ve stereo ses, 64 kbps 'de AAC-LC codec bileşeniyle kodlanır.

En güncel ön ayar listesini görmek için bkz. [videoları kodlamada kullanılacak yerleşik ön ayarlar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Önayarların nasıl kullanıldığını görmek için bkz. [karşıya yükleme, kodlama ve akış dosyaları](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>Standardencoderönayar

[Standardencoderönayar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) , giriş videosunu standart kodlayıcıyla kodlarken kullanılacak ayarları açıklar. Dönüşüm önayarlarını özelleştirirken bu ön ayarı kullanın.

#### <a name="considerations"></a>Dikkat edilmesi gerekenler

Özel ön ayarlar oluşturulurken aşağıdaki noktalar geçerlidir:

- AVC içerik üzerindeki yükseklik ve genişlik için tüm değerler dört katı olmalıdır.
- Azure Media Services v3 'de, tüm kodlama bit fiyatları bit/saniye cinsinden. Bu, birim olarak kilobit/saniye kullanan v2 API 'lerimiz olan ön ayarlardan farklıdır. Örneğin, v2 'deki bit hızı 128 (kilobit/saniye) olarak belirtilmişse, v3 'de 128000 (bit/saniye) olarak ayarlanır.

### <a name="customizing-presets"></a>Önayarları özelleştirme

Media Services, özel kodlama ihtiyaçlarınızı ve gereksinimlerinizi karşılamak için hazır ayarların tüm değerlerini özelleştirmeyi tam olarak destekler. Kodlayıcı önayarlarının nasıl özelleştirileceğini gösteren örnekler için aşağıdaki listeye bakın:

#### <a name="examples"></a>Örnekler

- [.NET ile önayarları özelleştirme](customize-encoder-presets-how-to.md)
- [CLı ile önayarları özelleştirme](custom-preset-cli-howto.md)
- [Önayarları REST ile özelleştirme](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Önceden ayarlanmış şema

Media Services v3 'de, Önayarlar, API 'nin kendisinde kesin olarak belirlenmiş varlıklardır. Bu nesneler için "şema" tanımını [Open API belirtiminde (veya Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)bulabilirsiniz. Ayrıca, [REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (veya diğer Media Services v3 SDK başvuru belgelerinde) önceden ayarlanmış tanımları ( **standardencoderönayar**gibi) görüntüleyebilirsiniz.

## <a name="scaling-encoding-in-v3"></a>V3 'de ölçeklendirme kodlaması

Medya işlemeyi ölçeklendirmek için bkz. [CLI Ile ölçekleme](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="next-steps"></a>Sonraki adımlar

* [Media Services kullanarak karşıya yükleme, kodlama ve akış](stream-files-tutorial-with-api.md).
* [Yerleşik ön ayarları kullanarak BIR https URL 'Sinden kodlama](job-input-from-http-how-to.md).
* [Yerleşik ön ayarları kullanarak yerel bir dosya kodlayın](job-input-from-local-file-how-to.md).
* [Belirli bir senaryoyu veya cihaz gereksinimlerinizi hedeflemek için özel bir önayar oluşturun](customize-encoder-presets-how-to.md).