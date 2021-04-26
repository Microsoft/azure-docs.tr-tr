---
title: Kodlama geçiş kılavuzu
description: Bu makale, Azure Media Services V2 'den v3 'e geçiş yaparken size yardımcı olacak senaryo tabanlı kılavuzların kodlamasını sağlar.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: a01571f4a1f852deb84b7f20d61b8048e8000790
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490106"
---
# <a name="encoding-scenario-based-migration-guidance"></a>Senaryo tabanlı geçiş kılavuzunu kodlama

![Geçiş Kılavuzu logosu](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![geçiş adımları 2](./media/migration-guide/steps-4.svg)

Bu makale, Azure Media Services V2 'den v3 'e geçiş yaparken size yardımcı olacak senaryo tabanlı kılavuzların kodlamasını sağlar.

## <a name="prerequisites"></a>Önkoşullar

Kodlama iş akışınızı değiştirmeye geçmeden önce, depolamanın yönetilme yöntemiyle farkları anlamanız önerilir.  AMS v3 'de, Azure depolama API 'SI, Media Services hesabınızla ilişkili depolama hesaplarını yönetmek için kullanılır.

> [!NOTE]
> V2 'de oluşturulan işler ve görevler, bir dönüşümle ilişkilendirilmediği için v3 'de gösterilmez. Öneri, v3 dönüştürmelerini ve işlerine geçiş yapmak.

## <a name="encoding-workflow-comparison"></a>Kodlama iş akışı karşılaştırması

V2 ve v3 için kodlama iş akışlarının görsel bir karşılaştırması için aşağıdaki akış çizelgeleriyle bakacağız birkaç dakikanızı yararlanın.

### <a name="v2-encoding-workflow"></a>V2 kodlama iş akışı

Daha büyük bir sürümü görmek için aşağıdaki görüntüye tıklayın.

[![V2 ](./media/migration-guide/V2-pretty.svg) için iş akışını kodlama ](./media/migration-guide/V2-pretty.svg#lightbox)

1. Kurulum
    1. Bir varlık oluşturun veya mevcut varlığı kullanın. Yeni bir varlık kullanılıyorsa, bu varlığa içerik yükleyin. Mevcut bir varlık kullanılıyorsa, varlık içinde zaten mevcut olan dosyaları kodlamış olmanız gerekir.
    2. Aşağıdaki öğelerin değerlerini alın:
        - Medya işlemcisi KIMLIĞI veya nesnesi
        - Kullanmak istediğiniz kodlayıcının kodlayıcı dizesi (adı)
        - Yeni varlığın varlık KIMLIĞI veya mevcut varlığın varlık KIMLIĞI
    3. İzleme için, bir iş veya görev düzeyi bildirim aboneliği ya da bir SDK olay işleyicisi oluşturun
2. Görevi veya görevleri içeren işi oluşturun. Her görev yukarıdaki öğeleri ve şunları içermelidir:
    - Bir çıkış varlığının oluşturulması gereken yönerge.  Çıktı varlığı sistem tarafından oluşturulur.
    - Çıkış varlığı için isteğe bağlı ad
3. İşi gönder.
4. İşi izleyin.

### <a name="v3-encoding-workflow"></a>V3 kodlama iş akışı

[![V3 için iş akışını kodlama](./media/migration-guide/V3-pretty.svg)](./media/migration-guide/V3-pretty.svg#lightbox)

1. Kurulum
    1. Bir varlık oluşturun veya mevcut bir varlığı kullanın. Yeni bir varlık kullanılıyorsa, bu varlığa içerik yükleyin. Mevcut bir varlık kullanılıyorsa, varlık içinde zaten mevcut olan dosyaları kodlamış olmanız gerekir. *Bu varlığa daha fazla içerik yüklememelisiniz.*
    1. Çıkış varlığı oluşturun.  Çıkış varlığı, kodlanmış dosyaların ve giriş ve çıkış meta verilerinin depolanacağı yerdir.
    1. Dönüştürme için değerleri Al:
        - Standart kodlayıcı önceden ayarı
        - AMS kaynak grubu
        - AMS hesap adı
    1. Dönüştürmeyi oluşturun veya var olan bir dönüşümü kullanın.  Dönüşümler yeniden kullanılabilir. İşi göndermek istediğiniz her seferinde yeni bir dönüşüm oluşturmak gerekli değildir.
1. Bir iş oluşturma
    1. İş için aşağıdaki öğelerin değerlerini alın:
        - Dönüştürme adı
        - Varlık için SAS URL 'SI, dosya paylaşımınızın HTTPs kaynak yolu veya dosyaların yerel yolu için temel URI. `JobInputAsset`Ayrıca, giriş olarak bir varlık adı da kullanabilir.
        - Dosya adı (ler)
        - Çıkış varlıkları
        - Bir kaynak grubu
        - AMS hesap adı  
1. İşinizi izlemek için [Event Grid](monitoring/monitor-events-portal-how-to.md) kullanın.
1. İşi gönder.

## <a name="custom-presets-from-v2-to-v3-encoding"></a>V2 'den v3 kodlamaya özel ön ayarlar

V2 kodunuz özel bir ön ayarla standart kodlayıcı olarak adlandırıldıysanız, bir işi göndermeden önce özel standart kodlayıcı önceden ayarıyla yeni bir dönüşüm oluşturmanız gerekir.

Özel önayarlar artık JSON ve XML tabanlı değildir. [Açık API (Swagger)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) belgelerinde tanımlanan özel önceden ayarlanmış şemayı takıp eden JSON 'da önceden ayarlarınızı yeniden oluşturun.

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>Bir kodlama işinden giriş ve çıkış meta veri dosyaları

V2 'de XML giriş ve çıkış meta verileri dosyaları, bir kodlama işinin sonucu olarak üretilir. V3 'de meta veri biçimi XML olarak JSON olarak değiştirilmiştir. Meta veriler hakkında daha fazla bilgi için bkz. [giriş meta verileri](input-metadata-schema.md) ve [Çıkış meta verileri](output-metadata-schema.md).

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>Premium Encoder ile V3 Standart Kodlayıcısı veya iş ortağı tabanlı çözümler

V2 API 'SI artık Premium Encoder 'ı desteklemiyor. Daha önce HEVC kodlaması için iş akışı tabanlı Premium Kodlayıcısı kullandıysanız, HEVC kodlama desteğiyle yeni v3 [Standart kodlayıcıya](encode-media-encoder-standard-formats-reference.md) geçiş yapılmalıdır.

Premium Encoder 'ın gelişmiş iş akışı özelliklerine ihtiyacınız varsa, [Imagine Communications](https://imaginecommunications.com), [Telestream](https://www.telestream.net)veya [Bitmovin](https://bitmovin.com)'dan bir Azure gelişmiş kodlama iş ortağı çözümü kullanmaya başlamanız önerilir.

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>HTTPS barındırılan URL 'lerde olan girişler içeren işler

Artık, Azure depolama 'da depolanan dosyalardan, yerel olarak depolanan veya [http (S) iş girişi desteği](job-input-from-http-how-to.md)kullanılarak gerçekleştirilen dosyalardaki işleri gönderebilirsiniz.

İşleri göndermeden önce Azure Blob dosyalarından dosyaları boş varlıklara kopyalamak için iş akışlarını daha önce kullandıysanız, Azure Blob depolama alanındaki bir SAS URL 'sini doğrudan işe geçirerek iş akışınızı basitleştirebileceksiniz.

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>Dizin Oluşturucu V1 ses dökümü yeni AudioAnalyzer "temel modu"

V2 API 'sinde Dizin Oluşturucu v1 işlemcisini kullanan müşteriler için, `AudioAnalyzer` bir işi göndermeden önce [Temel modda](transform-create-basic-audio-how-to.md) yeni ' yi çağıran bir dönüşüm oluşturmanız gerekir.

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>Kodlama, dönüşümler ve işler kavramları, öğreticiler ve nasıl yapılır kılavuzlarından

### <a name="concepts"></a>Kavramlar

- [Video ve ses Media Services kodlama](encode-concept.md)
- [Standart kodlayıcı biçimleri ve codec bileşenleri](encode-media-encoder-standard-formats-reference.md)
- [Otomatik olarak oluşturulmuş bir bit hızı ile kodlama](encode-autogen-bitrate-ladder.md)
- [Belirli bir çözüm için en iyi bit hızı değerini bulmak için içerik algılayan kodlama ön ayarını kullanın](encode-content-aware-concept.md)
- [Medya ayrılmış birimleri](concept-media-reserved-units.md)
- [Giriş meta verileri](input-metadata-schema.md)
- [Çıkış meta verileri](output-metadata-schema.md)
- [Media Services v3 'de dinamik paketleme: ses codec bileşenleri](encode-dynamic-packaging-concept.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>Öğreticiler

- [Öğretici: URL 'yi temel alarak uzak bir dosyayı kodlayın ve videoyu akışa sunun-.NET](stream-files-dotnet-quickstart.md)
- [Öğretici: Media Services v3 ile videoları karşıya yükleme, kodlama ve akışla](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>Nasıl yapılır kılavuzları

- [HTTPS URL 'sinden iş girişi oluşturma](job-input-from-http-how-to.md)
- [Yerel dosyadan iş girişi oluşturma](job-input-from-local-file-how-to.md)
- [Temel ses dönüşümü oluşturma](transform-create-basic-audio-how-to.md)
- .NET ile
  - [Özel bir dönüşümle kodlama-.NET](transform-custom-presets-how-to.md)
  - [Media Encoder Standard birlikte bir kaplama oluşturma](transform-create-overlay-how-to.md)
  - [.NET ile kodlayıcı standardını kullanarak küçük resimleri oluşturma](transform-generate-thumbnails-dotnet-how-to.md)
- Azure CLI ile
  - [Özel bir dönüşümle kodlama-Azure CLı](transform-custom-preset-cli-how-to.md)
- REST ile
  - [Özel bir dönüşümle kodlama-REST](transform-custom-preset-rest-how-to.md)
  - [REST ile kodlayıcı standardını kullanarak küçük resimleri oluşturma](transform-generate-thumbnails-rest-how-to.md)
- [Media Services .NET ile kodlarken bir videoyu alt kırpın](transform-subclip-video-dotnet-how-to.md)
- [Media Services REST ile kodlarken bir videoyu alt kırpın](transform-subclip-video-rest-how-to.md)

## <a name="samples"></a>Örnekler

Ayrıca [kod örneklerinde v2 ve v3 kodunu da karşılaştırabilirsiniz](migrate-v-2-v-3-migration-samples.md).
