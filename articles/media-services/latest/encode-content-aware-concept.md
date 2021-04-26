---
title: İçerik duyarlı kodlama için önceden ayarlanmış
description: Bu makalede, Microsoft Azure Media Services v3 'de içeriğe duyarlı kodlama ele alınmaktadır.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 8b48c6b0ef84458fa54692994d8e295d25114dd8
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112161"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Belirli bir çözüm için en iyi bit hızı değerini bulmak için içerik algılayan kodlama ön ayarını kullanın

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

İçeriği [Uyarlamalı bit hızı akışı](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)ile teslim etmek üzere hazırlamak için videonun birden çok bit hızında (yüksek-düşük) kodlanması gerekir. Bu, bit hızı düşürüldü, bu da videonun çözümü olduğundan, performansı düzgün bir şekilde azalmasını sağlar. Bu tür bir çoklu bit hızı kodlaması, bir çözüm ve bit fiyatları tablosu olarak adlandırılan, Media Services [yerleşik kodlama önayarları](/rest/api/media/transforms/createorupdate#encodernamedpreset)' na bakın.

İşlemekte olduğunuz içeriği bilmeniz ve kodlama merdiveni ' ni tek tek videonun karmaşıklığına göre özelleştirmeniz/ayarlamanız gerekir. Her çözünürlükte, kalitedeki artışın Perceptive olmadığı bir bit hızı vardır: kodlayıcı bu en iyi bit hızı değerinde çalışır. Bir sonraki iyileştirme düzeyi, içeriğe göre çözümlerin seçmesidir. Örneğin, bir PowerPoint sunusunun videosu, 720p 'in altına gitmesinin avantajına sahip değildir. Diğer bir deyişle, Kodlayıcıdaki her bir görüntü için ayarları iyileştirmek üzere kodlayıcı eklenebilir. 

Microsoft 'un [uyarlamalı akış](encode-autogen-bitrate-ladder.md) önceden ayarı, kaynak videoların kalite ve çözünürlüğündeki değişkenlik sorununa kısmen yöneliktir. Müşterilerimiz, bazı 1080p, 720p ve daha az sayıda SD ve daha düşük çözünürlükte içerik karışımına sahiptir. Ayrıca, tüm kaynak içerikleri film veya TV Studios 'den yüksek kaliteli mezzanines değildir. Uyarlamalı akış önceden ayarı, bit hızı el merdivenine, giriş Mezzanine 'nin çözünürlüğü veya Ortalama bit hızını aşmamasını sağlayarak bu sorunları giderir. Ancak, bu önayar çözünürlük ve bit hızı dışında kaynak özelliklerini incelemez.

## <a name="the-content-aware-encoding"></a>İçeriğe duyarlı kodlama

İçerik kullanan kodlama önceden ayarı, "Uyarlamalı bit hızı akışı" mekanizmasını genişleterek, kodlayıcının belirli bir çözüm için en iyi bit hızı değerini, ancak kapsamlı hesaplama Analizi gerektirmeksizin araymasına imkan tanıyan özel mantık ekleyerek. Bu önayar bir GOP hizalanmış MP4 'leri kümesi oluşturur. Herhangi bir giriş içeriği verildiğinde, hizmet, giriş içeriğinin ilk hafif analizini yapar ve en iyi katman sayısını, uyarlamalı akışa göre teslim etmek için uygun bit hızını ve çözüm ayarlarını belirlemede sonuçları kullanır. Bu ön ayar özellikle düşük ve orta ölçekli karmaşıklık videoları için geçerlidir. burada, çıkış dosyalarının uyarlamalı akış önayarıyla, ancak yine de görüntüleyicilere iyi bir deneyim sunan bir kaliteden daha düşük bitoranlarda olması gerekir. Çıktı, video ve ses Aralanmış ile MP4 dosyaları içerir

Aşağıdaki örnek grafiklerde [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) ve [vmaf](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)gibi kalite ölçümlerini kullanarak karşılaştırma gösterilmektedir. Kaynak, kodlayıcılarını stres amacıyla tasarlanan filmlerden ve TV gösterlerinden gelen yüksek karmaşıklık görüntüleriyle kısa küçük resimleri birleştirerek oluşturulmuştur. Tanım olarak, bu ön ayar içerikten içeriğe farklılık gösteren sonuçlar üretir. Ayrıca, bazı içerikler için bit hızı veya kalite açısından önemli bir azalma olmadığı anlamına gelir.

![PSNR kullanan hız deformasyonu (RD) eğrisi](media/encode-content-aware-concept/msrv1.png)

**Şekil 1: yüksek karmaşıklık kaynağı için PSNR ölçümünü kullanarak oran deformasyonu (RD) eğrisi**

![VMAF kullanan hız deformasyonu (RD) eğrisi](media/encode-content-aware-concept/msrv2.png)

**Şekil 2: yüksek karmaşıklık kaynağı için VMAF ölçümünü kullanarak oran deformasyonu (RD) eğrisi**

Aşağıda, bir kaynak içeriği kategorisinin sonuçları verilmiştir ve bu, kodlayıcının girişin düşük kaliteli olduğunu (düşük bit hızı nedeniyle birçok sıkıştırma yapıtı) belirleyebilmesini sağlar. İçerik algılayan ön ayarda, kodlayıcının, çok düşük bir bit hızında yalnızca bir çıkış katmanı üretmesine karar ettiğini unutmayın. bu sayede, çoğu istemci, akışı etkilemeden oynatabilir.

![PSNR kullanan RD eğrisi](media/encode-content-aware-concept/msrv3.png)

**Şekil 3: düşük kaliteli giriş için PSNR kullanan RD eğrisi (1080p 'de)**

![VMAF kullanan RD eğrisi](media/encode-content-aware-concept/msrv4.png)

**Şekil 4: düşük kaliteli giriş için VMAF kullanan RD eğrisi (1080p 'de)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>İçerik algılayan kodlama ön ayarını kullanma 

Bu önayarı kullanan dönüşümler aşağıdaki gibi oluşturulabilir. 

Dönüştürme çıkışları kullanan öğreticiler için [sonraki adımlar](#next-steps) bölümüne bakın. Çıkış varlığı, MPEG-DASH ve HLS gibi protokollerde Media Services akış uç noktalarından (öğreticilerde gösterildiği gibi) teslim edilebilir.

> [!NOTE]
> **Contentawareencoding** ön ayarını kullandığınızdan emin olun ContentAwareEncodingExperimental.

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> Ön ayarı kullanan kodlama işleri, `ContentAwareEncoding` Çıkış dakikalarına göre faturalandırılır. 
  
## <a name="next-steps"></a>Sonraki adımlar

* [Öğretici: Media Services v3 ile videoları karşıya yükleme, kodlama ve akışla](stream-files-tutorial-with-api.md)
* [Öğretici: Uzak dosya tabanlı URL kodlama ve video akışı yapma - REST](stream-files-tutorial-with-rest.md)
* [Öğretici: URL 'yi temel alarak uzak bir dosyayı kodlayın ve video CLı 'yı akışa koyun](stream-files-cli-quickstart.md)
* [Öğretici: URL 'yi temel alarak uzak bir dosyayı kodlayın ve videoyu akışa sunun-.NET](stream-files-dotnet-quickstart.md)
* [Öğretici: URL 'yi temel alarak uzak bir dosyayı kodlayın ve videoyu akışa Node.js](stream-files-nodejs-quickstart.md)
