---
title: Azure Media Services v3 API 'sindeki yüzeyleri bulma ve redaksiyonu | Microsoft Docs
description: Azure Media Services v3, bir video dosyası göndermenizi, yüzleri algılamanıza ve isteğe bağlı olarak tek bir Birleşik geçişte ya da düzenlenmesine izin veren iki aşamalı bir işlem aracılığıyla bunları bir araya getirmenizi (Bulanıklaştırma) uygulamanızı sağlayan bir yüz algılama ve redaksiyon (Bulanıklaştırma) ön ayarı sağlar. Bu makalede, v3 API 'sindeki yüz algılayıcısı ön ayarıyla yüz bulma ve redaksiyonun nasıl yapılacağı gösterilir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 4907a81fc8cb55499fa97f2b02a3e19e7117bbbc
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286394"
---
# <a name="find-and-redact-blur-faces-with-the-face-detector-preset"></a>Yüz algılayıcısı ön ayarıyla yüz bulma ve redaksiyonu (Bulanıklaştırma)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services v3 API 'SI, bulutta ölçeklenebilir yüz algılama ve redaksiyon (Bulanıklaştırma) sunan bir yüz algılayıcısı ön ayarı içerir. Yüz Redaksiyon, seçili kişilerin yüzlerini bulanıklaştırmak için videonuzu değiştirmenize olanak sağlar. Yüz redaksiyon hizmetini genel güvenlik ve haber medya senaryolarında kullanmak isteyebilirsiniz. Birden çok yüz içeren birkaç dakikalık bir çekimi, el ile redaksiyonda zaman alabilir, ancak bu önceden ayarlanmış olarak, yüz redaksiyon süreci yalnızca birkaç basit adım gerektirir.

Bu makale, **yüz algılayıcısı ön ayarıyla** ilgili ayrıntıları verir ve .net IÇIN Azure Media Services SDK ile nasıl kullanacağınızı gösterir.

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

## <a name="compliance-privacy-and-security"></a>Uyumluluk, gizlilik ve güvenlik
 
Önemli bir anımsatıcı olarak, Azure Media Services Analize ait tüm geçerli kanunlarla uyumlu olmanız gerekir. Azure Media Services veya başka bir Azure hizmetini, diğer kullanıcıların haklarını ihlal eden bir şekilde kullanmamalıdır. Biyometrik veriler de dahil olmak üzere herhangi bir videoyu, işleme ve depolama için Azure Media Services hizmetine yüklemeden önce, videodaki kişilerden gelen tüm uygun haklara sahip olmanız gerekir. Azure Media Services Azure bilişsel [Hizmetler koşulları](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)'nda uyumluluk, gizlilik ve güvenlik hakkında bilgi edinmek için. Microsoft 'un gizlilik yükümlülüklerini ve verilerinizi işleme için, Microsoft 'un [Gizlilik bildirimini](https://privacy.microsoft.com/PrivacyStatement), [çevrimiçi hizmet koşulları](https://www.microsoft.com/licensing/product-licensing/products) 'nı (OST) ve [veri Işleme eki](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) 'ni ("DPA") gözden geçirin. Veri saklama, silme/yok etme dahil olmak üzere daha fazla gizlilik bilgisi, OST ve [burada](../video-indexer/faq.md)bulunabilir. Azure Media Services kullanarak, bilişsel hizmetler koşulları, OST, DPA ve gizlilik bildirimiyle bağlanmayı kabul etmiş olursunuz.

## <a name="face-redaction-modes"></a>Yüz redaksiyon modları

Yüz Redaksiyon, her video çerçevesindeki yüzleri algılayarak ve yüz nesnesini aynı anda ileri ve geri doğru şekilde izlerken, aynı bireyin diğer açılardan de bulanıklaşır. Otomatik redaksiyon süreci karmaşıktır ve her zaman %100 ' ün garantisini her zaman bulanıklaştırmaz. Bu nedenle, önceden ayarlanmış olarak, dosyayı son bulanıklaştırma geçişi için göndermeden önce bir düzen aracılığıyla bulanıklaştırma 'nın kalitesini ve doğruluğunu artırmak üzere iki taramalı bir mod kullanılabilir. 

Tamamen otomatik **Birleşik** moda ek olarak, iki taramalı iş akışı, bir yüz kimlikleri listesi aracılığıyla bulanıklaştırmak istediğiniz yüzleri (veya Bulanıklaştırma değil) seçmenize olanak sağlar. Rastgele kare ayarlamaları yapmak için, Önayar ikinci pass girişi olarak JSON biçiminde bir meta veri dosyası kullanır. Bu iş akışı **analiz** ve **redakct** modlarına bölünür.

Ayrıca, tek bir işte her iki görevi çalıştıran tek bir geçişte yalnızca iki modu birleştirebilirsiniz; Bu mod **Birleşik** olarak adlandırılır.  Bu makalede örnek kod, örnek bir kaynak dosyasında Basitleştirilmiş tek Pass **Birleşik** modunun nasıl kullanılacağını gösterir.

### <a name="combined-mode"></a>Birleşik mod

Bu, JSON dosyasının el ile düzenlenmesine gerek kalmadan tek bir geçişte Redaksiyonu yapılmış bir MP4 video dosyası oluşturur. İş için varlık klasöründeki çıktı, seçilen bulanıklaştırma efektini kullanarak bulanık yüzeyleri içeren tek bir. mp4 dosyası olacaktır. Redaksiyon için en iyi sonuçları elde etmek için, **SourceResolution** olarak ayarlanmış çözüm özelliğini kullanın.

| Aşama | Dosya Adı | Notlar |
| --- | --- | --- |
| Giriş varlığı |"ignite-sample.mp4" |WMV, har veya MP4 biçimindeki video |
| Önceden ayarlanmış yapılandırma |Yüz algılayıcısı yapılandırması | **mod**: faceredactormode. Birleşik,  **blurtype**: blurtype. med, **Resolution**: analysisresolution. SourceResolution |
| Çıkış varlığı |"ignite-redacted.mp4 |Yüzler için Bulanıklaştırma efektini içeren video |

### <a name="analyze-mode"></a>Çözümleme modu

İki taramalı iş akışının **Çözümle** geçişi bir video girişi alır ve algılanan her bir yüzey için yüz konumları, yüz kimliği ve JPG görüntülerinin listesini IÇEREN bir JSON dosyası oluşturur. 

| Aşama | Dosya Adı | Notlar |
| --- | --- | --- |
| Giriş varlığı |"ignite-sample.mp4" |WMV, MPV veya MP4 biçimindeki video |
| Önceden ayarlanmış yapılandırma |Yüz algılayıcısı yapılandırması |**mod**: faceredactormode. Analyze, **çözüm**: analysisresolution. SourceResolution|
| Çıkış varlığı |Üzerinde ignite-sample_annotations.js |JSON biçimindeki yüz konumlarının ek açıklama verileri. Bu, bulanıklaştırma sınırlayıcı kutularını değiştirmek için Kullanıcı tarafından düzenlenebilir. Aşağıdaki örneğe bakın. |
| Çıkış varlığı |foo_thumb% 06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Algılanan her bir yüzün kırpılan bir jpg (sayının, yüzün LabelId 'sini gösterdiği) |

#### <a name="output-example"></a>Çıkış örneği

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>Redact (Bulanıklaştırma) modu

İş akışının ikinci geçişi, tek bir varlık içinde birleştirilmesi gereken daha fazla sayıda giriş alır.

Bu, bulanıklaştırmak üzere bir kimlik listesi, özgün video ve ek açıklama JSON 'u içerir. Bu mod, giriş videosunda bulanıklaştırma uygulamak için ek açıklamaları kullanır.

Analiz geçişinin çıktısı özgün videoyu içermez. Videonun, Redakct modu görevi için giriş varlığına yüklenmesi ve birincil dosya olarak seçilmesi gerekir.

| Aşama | Dosya Adı | Notlar |
| --- | --- | --- |
| Giriş varlığı |"ignite-sample.mp4" |WMV, MPV veya MP4 biçimindeki video. 1. adımdaki ile aynı video. |
| Giriş varlığı |"Üzerinde ignite-sample_annotations.js" |bulanık yüz değiştirmek istiyorsanız, isteğe bağlı değişiklikler ile birinci aşamada ek açıklama meta verileri dosyası. Bu, bir dış uygulama, kod veya metin düzenleyicisinde düzenlenmelidir. |
| Giriş varlığı | "ignite-sample_IDList.txt" (Isteğe bağlı) | Redaksiyonlar için isteğe bağlı yeni satıra ayrılmış yüz kimlikleri listesi. Boş bırakılırsa, kaynaktaki tüm yüzlerin bulanıklık uygulanmış olması gerekir. Belirli yüzeyleri bulanıklaştırma seçeneğini seçmeli olarak seçmek için listeyi kullanabilirsiniz. |
| Yüz algılayıcısı önayarı  |Önceden ayarlanmış yapılandırma  | **mod**: faceredactormode. Redact, **blurtype**: blurtype. med |
| Çıkış varlığı |"ignite-sample-redacted.mp4" |Ek açıklamalar temelinde uygulanan bulanıklık ile video |

#### <a name="example-output"></a>Örnek çıkış

Bu, bir ID seçili olan bir ıdlist öğesinden alınan çıktıdır.

Örnek foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>Bulanıklaştırma türleri

**Birleşik** veya **REDAKCT** modunda, JSON giriş yapılandırması aracılığıyla aralarından seçim yapabileceğiniz beş farklı bulanıklaştırma modu vardır: **düşük**, **Med**, **High**, **Box** ve **Black**. Varsayılan olarak **Med** kullanılır.

Aşağıdaki bulanıklaştırma türlerinin örneklerini bulabilirsiniz.


#### <a name="low"></a>Düşük

![Düşük çözünürlüklü bulanıklaştırma ayarı örneği.](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>Olduğundan

![Orta ölçekli çözünürlük bulanıklığı ayarı örneği.](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>Yüksek

![Yüksek çözünürlüklü bulanıklaştırma ayarı örneği.](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>Box

![Çıktındaki hata ayıklama sırasında kullanılacak kutu modu.](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>Siyahi

![Kara kutu modu, siyah kutular ile tüm yüzleri içerir.](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>Çıktı JSON dosyasının öğeleri

Redaksiyon MP, bir video çerçevesinde 64 adede kadar insan yüzü algılayan yüksek duyarlıklı bir konum algılama ve izleme sağlar. Yüz yüze yüzler en iyi sonuçları sağlar, kenar yüzeyleri ve küçük yüzler (24x24 pikselden küçük veya buna eşit) zor olur.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET örnek kodu

Aşağıdaki program, **Birleşik** tek geçişli redaksiyon modunun nasıl kullanılacağını göstermektedir:

- Bir varlık oluşturun ve kıymete bir medya dosyası yükleyin.
- Mode ve blurType ayarlarını kullanan yüz algılayıcısı ön ayarını yapılandırın.
- Yüz algılayıcısı ön ayarını kullanarak yeni bir dönüşüm oluşturma
- Çıktı Redaksiyonu yapılmış video dosyasını indirin.

## <a name="download-and-configure-the-sample"></a>Örneği indirin ve yapılandırın

Aşağıdaki komutu kullanarak, .NET örneğini içeren bir GitHub havuzunu makinenize kopyalayın:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

Örnek, [Faceredadctor](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor) klasöründe bulunur. İndirilen projenizde [appsettings.js](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json) açın. Değerleri, [API 'lere erişirken](./access-api-howto.md)aldığınız kimlik bilgileriyle değiştirin.

**Isteğe bağlı olarak** , deponun kökündeki **[Sample. env](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env)** dosyasını kopyalayabilir ve burada ayrıntıları doldurabilir ve bu dosyayı **. env** olarak yeniden adlandırabilirsiniz (önde gelen noktayı unutmayın.) böylece depodaki tüm örnek projelerde kullanılabilir.  Bu, her örnekteki dosya üzerinde doldurulmuş appsettings.jsolması gereğini ortadan kaldırır ve ayrıca kendi git hub 'ı kopyalanmış depolarınızda herhangi bir ayarı kontrol etmeyi önler.

#### <a name="examples"></a>Örnekler

Bu kod, **Birleşik** bir mod bulanıklığı Için **Facedetectorönayar** kurulumunu gösterir.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

Bu kod örneği, Önayar oluşturma sırasında bir dönüştürme nesnesine nasıl geçtiğini gösterir. Dönüşüm oluşturulduktan sonra, işler doğrudan buna gönderilebilir.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

