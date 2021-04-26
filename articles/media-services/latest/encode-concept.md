---
title: Video ve ses Media Services kodlama
description: Bu makalede, Azure Media Services ile video ve ses kodlama hakkında bilgi verilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: cd37175bd85e31ddc18c8267cdf01f7dc6249a0b
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491925"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Video ve ses Media Services kodlama

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services ' deki terim kodlaması, dijital video ve/veya ses içeren dosyaları bir standart biçimden diğerine dönüştürme işlemi için geçerlidir. (a), dosyaların boyutunu azaltarak ve/veya (b) çok sayıda cihaz ve uygulama ile uyumlu bir biçim üretir. Bu işlem, video sıkıştırması veya kodlama kodlaması olarak da adlandırılır. Kavramlarla ilgili daha fazla bilgi için [veri sıkıştırmaya](https://en.wikipedia.org/wiki/Data_compression) ve [kodlama ve dönüştürme nedir?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) bölümüne bakın.

Videolar genellikle, [aşamalı indirme](https://en.wikipedia.org/wiki/Progressive_download) ya da [Uyarlamalı bit hızı akışı](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)aracılığıyla cihazlara ve uygulamalara dağıtılır.

> [!IMPORTANT]
> Media Services, iptal edilen veya hatalı işler için faturalandırılmıyor. Örneğin, %50 ilerleme durumuna ulaşan ve iptal edilen bir iş, iş dakikası %50 ' de faturalandırılmaz. Yalnızca bitmiş işler için ücretlendirilirsiniz.

* Aşamalı indirme ile teslim etmek için Azure Media Services kullanarak bir dijital medya dosyasını (Mezzanine), [H.](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) ve codec bileşeniyle kodlanmış video ve [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) codec ile kodlanan ses içeren bir [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) dosyasına dönüştürebilirsiniz. Bu MP4 dosyası Depolama hesabınızdaki bir varlığa yazılır. Dosyayı doğrudan indirmek için Azure depolama API 'Lerini veya SDK 'Larını (örneğin, [depolama REST API](../../storage/common/storage-rest-api-auth.md) veya [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) kullanabilirsiniz. Depolama alanında belirli bir kapsayıcı adıyla çıkış varlığı oluşturduysanız, bu konumu kullanın. Aksi takdirde, [varlık kapsayıcısı URL 'lerini listelemek](/rest/api/media/assets/listcontainersas)için Media Services kullanabilirsiniz. 
* İçeriği Uyarlamalı bit hızı akışı ile teslim etmek üzere hazırlamak için, Mezzanine dosyasının birden çok bitücret (yüksek-düşük) ile kodlanmış olması gerekir. Kaliteyi düzgün şekilde geçişini sağlamak için, bit hızı düşürüldü, videonun çözümlenmesi düşürüldü. Bu, bir çözüm (bkz. [otomatik olarak üretilen Uyarlamalı bit hızı el](encode-autogen-bitrate-ladder.md)ile) adlı bir kodlama. Media Services kullanarak, Mezzanine dosyalarını birden çok bit hızında kodlamak için kullanabilirsiniz. Bunu yaparken, Depolama hesabınızdaki bir varlığa yazılan MP4 dosyaları ve ilişkili akış yapılandırma dosyaları kümesi alacaksınız. Daha sonra, videoyu [MPEG-Dash](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) ve [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming)gibi akış protokolleriyle teslim etmek Için Media Services ' de [dinamik paketleme](encode-dynamic-packaging-concept.md) özelliğini kullanabilirsiniz. Bu, bir [akış Bulucu](stream-streaming-locators-concept.md) oluşturmanız ve desteklenen protokollere karşılık gelen akış URL 'leri oluşturmanızı gerektirir. Bu, daha sonra özelliklerine göre cihazlara/uygulamalara devredilebilir.

Aşağıdaki diyagramda, dinamik paketleme ile isteğe bağlı kodlama için iş akışı gösterilmektedir.

![Dinamik paketleme ile isteğe bağlı kodlama için iş akışı](./media/encode-dynamic-packaging-concept/media-services-dynamic-packaging.svg)

Bu konu, içeriğinizi Media Services v3 ile nasıl kodlayabileceğine kılavuzluk sağlar.

## <a name="transforms-and-jobs"></a>Dönüşümler ve işler

Media Services v3 ile kodlamak için bir [dönüşüm](/rest/api/media/transforms) ve [iş](/rest/api/media/jobs)oluşturmanız gerekir. Dönüştürme, kodlama ayarlarınız ve çıktılar için bir tarif tanımlar; iş, tarif eden bir örneğidir. Daha fazla bilgi için [Dönüşümler ve İşler](transform-jobs-concept.md) konusuna bakın.

Media Services ile kodlarken, kodlayıcıların giriş medya dosyalarının nasıl işleneceğini söylemek için önayarları kullanın. V3 Media Services, dosyalarınızı kodlamak için standart kodlayıcı kullanırsınız. Örneğin, video çözünürlüğünü ve/veya kodlanmış içerikte istediğiniz ses kanalı sayısını belirtebilirsiniz.

Sektörde en iyi uygulamaları temel alan önerilen yerleşik ön ayarlardan biriyle hızlı bir şekilde çalışmaya başlayabilir veya belirli bir senaryoyu veya cihaz gereksinimlerinizi hedeflemek için özel bir önayar oluşturmayı tercih edebilirsiniz. Daha fazla bilgi için bkz. [özel dönüşümle kodlama](transform-custom-presets-how-to.md).

2019 Ocak 'tan başlayarak, MP4 dosyaları üretmek için standart kodlayıcıyla kodlarken, yeni bir. MPI dosyası oluşturulur ve çıkış varlığına eklenir. Bu MPı dosyası, [dinamik paketleme](encode-dynamic-packaging-concept.md) ve akış senaryoları için performansı iyileştirmeye yöneliktir.

> [!NOTE]
> MPı dosyasını değiştirmemeniz veya kaldırmanız ya da bu dosyanın var olan (veya olmayan) hizmetinize herhangi bir bağımlılığı almanız gerekmez.

### <a name="creating-job-input-from-an-https-url"></a>HTTPS URL 'sinden iş girişi oluşturma

Videolarınızı işlemek üzere Iş gönderdiğinizde, Media Services Giriş videosunu nerede bulacağınızı bildirmeniz gerekir. Seçeneklerden biri, bir HTTPS URL 'sini iş girişi olarak belirtmektir. Şu anda Media Services v3, HTTPS URL 'Leri üzerinden öbekli aktarım kodlamasını desteklemez.

#### <a name="examples"></a>Örnekler

* [.NET ile HTTPS URL 'sinden kodlama](stream-files-dotnet-quickstart.md)
* [REST ile HTTPS URL 'sinden kodlama](stream-files-tutorial-with-rest.md)
* [CLı ile HTTPS URL 'sinden kodlama](stream-files-cli-quickstart.md)
* [Node.jsile HTTPS URL 'sini kodla ](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Yerel bir dosyadan iş girişi oluşturma

Giriş videosu bir medya hizmeti varlığı olarak depolanabilir, bu durumda bir dosyayı temel alan bir giriş varlığı (yerel olarak veya Azure Blob depolama alanında depolanır) oluşturabilirsiniz.

#### <a name="examples"></a>Örnekler

[Yerleşik ön ayarları kullanarak yerel bir dosyayı kodla](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Alt kırpmayla iş girişi oluşturma

Bir videoyu kodlarken, kaynak dosyayı kırpmak veya kırpmak ve giriş videosunun yalnızca istenen kısmına sahip bir çıktı üretmek için de belirtebilirsiniz. Bu işlev, [Builtınstandardencoderönayar](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ön ayarları veya [Standardencoderönayar](/rest/api/media/transforms/createorupdate#standardencoderpreset) önayarları kullanılarak oluşturulan [dönüşümlerle](/rest/api/media/transforms) birlikte kullanılır.

İsteğe bağlı veya Canlı Arşiv (kaydedilmiş bir olay) için tek bir klibe sahip bir [iş](/rest/api/media/jobs/create) oluşturmayı belirtebilirsiniz. İş girişi bir varlık veya HTTPS URL 'SI olabilir.

> [!TIP]
> Videoyu yeniden kodlamadan videonuzun bir alt LIP akışını yapmak istiyorsanız, [dinamik paketlemede ön filtreleme bildirimleri](filters-dynamic-manifest-concept.md)kullanmayı göz önünde bulundurun.

#### <a name="examples"></a>Örnekler

Örneklere bakın:

* [.NET ile videoyu alt kırpın](transform-subclip-video-dotnet-how-to.md)
* [REST ile videoyu alt kırpın](transform-subclip-video-rest-how-to.md)

## <a name="built-in-presets"></a>Yerleşik ön ayarlar

Media Services aşağıdaki yerleşik kodlama önayarlarını destekler:  

### <a name="builtinstandardencoderpreset"></a>Builtınstandardencoderönayar

[Builtınstandencoderönayar](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) , giriş videosunu standart kodlayıcıyla kodlamak için yerleşik bir ön ayar ayarlamak üzere kullanılır.

Aşağıdaki yerleşik ön ayarlar şu anda desteklenmektedir:

- **Encodernamedönayar. AACGoodQualityAudio**: yalnızca 192 Kbps hızında kodlanmış tek bir MP4 dosyası üretir.
- **Encodernamedönayar. Uyarlantivestreaming** (önerilir): Bu, H. Ise Uyarlamalı bit hızı kodlamasını destekler. Daha fazla bilgi için bkz. [bit hızı merdiveni otomatik oluşturma](encode-autogen-bitrate-ladder.md).
- **Encodernamerönayar. H265AdaptiveStreaming** : uyarlamalı akış ön ayarıyla benzerdir, ancak HEVC (H. 265) codec bileşenini kullanır. H. 265 video ve stereo AAC ses içeren bir GOP hizalanmış MP4 dosyaları kümesi üretir. Giriş çözümlemesi, bit hızı ve kare hızına dayalı olarak bir bit hızı merdiveni otomatik olarak oluşturulur. Otomatik olarak oluşturulan önayar, giriş çözünürlüğünü hiçbir şekilde aşmaz. Örneğin, giriş 720p ise, çıkış en iyi 720p olarak kalır.
- **Encodernamedönayar. ContentAwareEncoding**: H. IBU içerik algılayan kodlama için önceden ayarlanmış bir ön ayar sunar. Herhangi bir giriş içeriği verildiğinde, hizmet en iyi katman sayısını ve uyarlamalı akış gönderimi için uygun bit hızı ve çözüm ayarlarını otomatik olarak saptamaya çalışır. Temel algoritmalar zaman içinde gelişmeye devam edecektir. Çıktı, video ve ses Aralanmış MP4 dosyaları içerir. Daha fazla bilgi için bkz. [içeriğe duyarlı kodlama](encode-content-aware-concept.md).
- **Encodernamedönayar. H265ContentAwareEncoding**: HEVC (H. 265) içerik kullanan kodlama için önceden ayarlanmış bir ayar sunar. İçeriğe duyarlı kodlama kullanarak bir GOP hizalanmış MP4 'leri kümesi üretir. Herhangi bir giriş içeriği verildiğinde, hizmet, giriş içeriğinin ilk hafif analizini yapar ve en iyi katman sayısını, uyarlamalı akışa göre teslim etmek için uygun bit hızını ve çözüm ayarlarını belirlemede sonuçları kullanır. Bu ön ayar özellikle düşük ve orta ölçekli karmaşıklık videoları için geçerlidir. burada çıktı dosyaları düşük bitoranlarda, ancak görüntüleyiciler için de iyi bir deneyim sunan bir kalitede olur. Çıktı, video ve ses Aralanmış MP4 dosyaları içerir.
  > [!NOTE]
  > Artık kullanım dışı olan **Contentawareencoding** değil ContentAwareEncodingExperimental ' i kullandığınızdan emin olun

- **Encodernamedönayar. H264MultipleBitrate1080p**: 6000 kbps 'den 400 Kbps 'e ve stereo AAC ses arasında değişen sekiz GOP hizalanmış MP4 dosyası kümesi oluşturur. Çözüm, 1080p ' de başlar ve 360p 'ye gider.
- **Encodernamedönayar. H264MultipleBitrate720p**: altı GOP hizalanmış MP4 dosyası kümesi oluşturur ve 3400 kbps ile 400 Kbps arasında ve stereo AAC sesde vardır. Çözüm 720p ' de başlar ve 360p 'ye gider.
- **Encodernamedönayar. H264MultipleBitrateSD**: beş adet GOP hizalanmış MP4 dosyası kümesi oluşturur ve 1600 kbps ila 400 Kbps ve stereo AAC sestir. Çözüm 480p 'de başlar ve 360p 'ye gider.
- **Encodernamedönayar. H264SingleBitrate1080p**: videonun, 6750 kbps ve 1080 piksellik bir resim yüksekliği ile KODLANDıĞı bir MP4 dosyası üretir ve stereo ses, 64 kbps 'de AAC-LC codec bileşeniyle kodlanır.
- **Encodernamedönayar. H264SingleBitrate720p**: videonun, 4500 kbps ve 720 piksellik bir resim yüksekliği ile KODLANDıĞı bir MP4 dosyası üretir ve stereo ses, 64 kbps 'de AAC-LC codec bileşeniyle kodlanır.
- **Encodernamedönayar. H264SingleBitrateSD**: videonun, 2200 kbps ve 480 piksellik bir resim yüksekliği ile KODLANDıĞı bir MP4 dosyası üretir ve stereo ses, 64 kbps 'de AAC-LC codec bileşeniyle kodlanır.
- **Encodernamedönayar. H265SingleBitrate720P**: videonun 1800 Kbps hızında ve 720 piksellik bir resim yüksekliğinde ve stereo ses, 128 kbps 'de AAC-LC codec bileşeniyle kodlandığı bir MP4 dosyası üretir.
- **Encodernamedönayar. H265SingleBitrate1080p**: videonun 3500 Kbps hızında ve 1080 piksellik bir resim yüksekliğinde ve stereo ses, 128 kbps 'de AAC-LC codec bileşeniyle kodlandığı bir MP4 dosyası üretir.
- **Encodernamedönayar. H265SingleBitrate4K**: videonun 9500 Kbps hızında ve 2160 piksellik bir resim yüksekliğinde ve stereo ses, 128 kbps 'de AAC-LC codec bileşeniyle kodlandığı bir MP4 dosyası üretir.

En güncel ön ayar listesini görmek için bkz. [videoları kodlamada kullanılacak yerleşik ön ayarlar](/rest/api/media/transforms/createorupdate#encodernamedpreset).

Önayarların nasıl kullanıldığını görmek için bkz. [karşıya yükleme, kodlama ve akış dosyaları](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>Standardencoderönayar

[Standardencoderönayar](/rest/api/media/transforms/createorupdate#standardencoderpreset) , giriş videosunu standart kodlayıcıyla kodlarken kullanılacak ayarları açıklar. Dönüşüm önayarlarını özelleştirirken bu ön ayarı kullanın.

#### <a name="considerations"></a>Dikkat edilmesi gerekenler

Özel ön ayarlar oluşturulurken aşağıdaki noktalar geçerlidir:

- AVC içerik üzerindeki yükseklik ve genişlik için tüm değerler dört katı olmalıdır.
- Azure Media Services v3 'de, tüm kodlama bit fiyatları bit/saniye cinsinden. Bu, birim olarak kilobit/saniye kullanan v2 API 'lerimiz olan ön ayarlardan farklıdır. Örneğin, v2 'deki bit hızı 128 (kilobit/saniye) olarak belirtilmişse, v3 'de 128000 (bit/saniye) olarak ayarlanır.

### <a name="customizing-presets"></a>Önayarları özelleştirme

Media Services, özel kodlama ihtiyaçlarınızı ve gereksinimlerinizi karşılamak için hazır ayarların tüm değerlerini özelleştirmeyi tam olarak destekler. Kodlayıcı önayarlarının nasıl özelleştirileceğini gösteren örnekler için aşağıdaki listeye bakın:

#### <a name="examples"></a>Örnekler

- [.NET ile önayarları özelleştirme](transform-custom-presets-how-to.md)
- [CLı ile önayarları özelleştirme](transform-custom-preset-cli-how-to.md)
- [Önayarları REST ile özelleştirme](transform-custom-preset-rest-how-to.md)


## <a name="preset-schema"></a>Önceden ayarlanmış şema

Media Services v3 'de, Önayarlar, API 'nin kendisinde kesin olarak belirlenmiş varlıklardır. Bu nesneler için "şema" tanımını [Open API belirtiminde (veya Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)bulabilirsiniz. Ayrıca, [REST API](/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset) (veya diğer Media Services v3 SDK başvuru belgelerinde) önceden ayarlanmış tanımları ( **standardencoderönayar** gibi) görüntüleyebilirsiniz.

## <a name="scaling-encoding-in-v3"></a>V3 'de ölçeklendirme kodlaması

Medya işlemeyi ölçeklendirmek için bkz. [CLI Ile ölçekleme](media-reserved-units-cli-how-to.md).
İçinde, API 'nin **2020-05-01** sürümü veya Azure Portal üzerinden oluşturulan hesaplar için, ölçekleme ve medya ayrılmış birimleri artık gerekli değildir. Ölçeklendirme, hizmet tarafından dahili olarak otomatik olarak işlenir.

## <a name="billing"></a>Faturalandırma

Media Services, iptal edilen veya hatalı işler için faturalandırılmıyor. Örneğin, %50 ilerleme durumuna ulaşan ve iptal edilen bir iş, iş dakikası %50 ' de faturalandırılmaz. Yalnızca bitmiş işler için ücretlendirilirsiniz.

Daha fazla bilgi için bkz. [Fiyatlandırma](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Soru sorun, geri bildirimde bulunun, güncelleştirmeleri al

Soru sormak, geri bildirimde bulunmak ve Media Services hakkında güncelleştirmeler almak için [Azure Media Services Community](media-services-community.md) makalesine göz atın.

## <a name="next-steps"></a>Sonraki adımlar

* [Media Services kullanarak karşıya yükleme, kodlama ve akış](stream-files-tutorial-with-api.md).
* [Yerleşik ön ayarları kullanarak BIR https URL 'Sinden kodlama](job-input-from-http-how-to.md).
* [Yerleşik ön ayarları kullanarak yerel bir dosya kodlayın](job-input-from-local-file-how-to.md).
* [Belirli bir senaryoyu veya cihaz gereksinimlerinizi hedeflemek için özel bir önayar oluşturun](transform-custom-presets-how-to.md).
