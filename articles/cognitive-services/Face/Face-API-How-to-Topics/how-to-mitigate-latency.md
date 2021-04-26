---
title: Yüz hizmeti kullanılırken gecikme süresini azaltma
titleSuffix: Azure Cognitive Services
description: Yüz hizmetini kullanırken gecikme süresini nasıl azaltacağınızı öğrenin.
services: cognitive-services
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.openlocfilehash: a306883573387a2a5c20a53c7015c6dbd3eddf65
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878677"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>Nasıl yapılır: yüz hizmetini kullanırken gecikme süresini azaltma

Yüz hizmetini kullanırken gecikme süresiyle karşılaşabilirsiniz. Gecikme süresi bir ağ üzerinden iletişim kurarken oluşan her türlü gecikmeye başvurur. Genel olarak, gecikme süresinin olası nedenleri şunlardır:
- Her paketin kaynaktan hedefe aktarılması gereken fiziksel uzaklık.
- İletim ortamının sorunları.
- Yönlendirici veya anahtardaki hatalar, iletim yolunda.
- Virüsten koruma uygulamaları, güvenlik duvarları ve paketleri incelemeye yönelik diğer güvenlik mekanizmaları için gereken süre.
- İstemci veya sunucu uygulamalarında düzgün olmayan bir durumda.

Bu konu, Azure bilişsel hizmetler 'i kullanmaya özgü gecikme süresinin olası nedenleri ve bu nedenlerin nasıl azaltılacağını ele almaktadır.

> [!NOTE]
> Azure bilişsel hizmetler, gecikmeyle ilgili hiçbir Hizmet Düzeyi Sözleşmesi (SLA) sağlamaz.

## <a name="possible-causes-of-latency"></a>Gecikme süresinin olası nedenleri

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Bilişsel hizmet ile uzak URL arasındaki yavaş bağlantı

Bazı Azure bilişsel hizmetler, sağladığınız uzak bir URL 'den veri elde eden yöntemler sağlar. Örneğin, yüz hizmetinin [DetectWithUrlAsync yöntemini](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) çağırdığınızda, hizmetin yüzleri algılamaya çalıştığı bir görüntünün URL 'sini belirtebilirsiniz.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

Yüz hizmeti 'nin görüntüyü uzak sunucudan indirmesi gerekir. Yüz hizmetten uzak sunucuya bağlantı yavaşsa, algılama yönteminin yanıt süresini etkiler.

Bunu azaltmak için [görüntüyü Azure Premium BLOB depolama alanında depolamayı](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet)göz önünde bulundurun. Örnek:

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>Büyük karşıya yükleme boyutu

Bazı Azure bilişsel hizmetler, karşıya yüklediğiniz bir dosyadan veri elde eden yöntemler sağlar. Örneğin, yüz hizmetinin [Detectwithstreamasync yöntemini](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) çağırdığınızda, hizmetin yüzeyleri algılamaya çalıştığı bir görüntüyü karşıya yükleyebilirsiniz.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

Karşıya yüklenecek dosya büyükse, `DetectWithStreamAsync` aşağıdaki nedenlerden dolayı yöntemin yanıt süresini etkiler:
- Dosyayı karşıya yüklemek daha uzun sürer.
- Dosyayı dosya boyutuna göre işlemek için hizmeti daha uzun sürer.

Karşı
- [Görüntüyü Azure Premium blob depolamada depolamayı](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet)göz önünde bulundurun. Örnek:
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- Daha küçük bir dosyayı karşıya yüklemeyi düşünün.
    - Yüz tanıma için [giriş verileriyle](../concepts/face-detection.md#input-data) ilgili yönergeleri ve yüz [tanıma için giriş verilerini](../concepts/face-recognition.md#input-data)görüntüleyin.
    - Yüz algılama için, algılama modeli kullanılırken `DetectionModel.Detection01` , görüntü dosyası boyutunu azaltmak işleme hızını artırır. Algılama modeli kullanılırken, görüntü dosyası `DetectionModel.Detection02` boyutunu azaltmak yalnızca görüntü dosyası 1920x1080 ' den küçük olduğunda işleme hızını artırır.
    - Yüz tanıma için, yüz boyutunu 200x200 piksel olarak düşürmek, tanınma modelinin doğruluğunu etkilemez.
    - Ve yöntemlerinin performansı, `DetectWithUrlAsync` `DetectWithStreamAsync` görüntüde kaç yüzün olduğuna de bağlıdır. Yüz tanıma hizmeti bir görüntü için 100 yüz alabilir. Yüzler, büyük ve küçük olan yüz dikdörtgen boyutuna göre sıralanır.
    - Birden çok hizmet yöntemini çağırmanız gerekiyorsa, uygulama tasarımınız için izin veriyorsa bunları paralel olarak çağırmayı göz önünde bulundurun. Örneğin, bir yüz karşılaştırması gerçekleştirmek üzere iki görüntüde yüzleri algılamanıza gerek varsa:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>İşlem kaynağınız ve yüz hizmeti arasında yavaş bağlantı

Bilgisayarınızın yüz hizmetine yavaş bağlantısı varsa, hizmet yöntemlerinin yanıt süresini etkiler.

Karşı
- Yüz aboneliğinizi oluştururken, uygulamanızın barındırıldığı konuma en yakın bölgeyi seçtiğinizden emin olun.
- Birden çok hizmet yöntemini çağırmanız gerekiyorsa, uygulama tasarımınız için izin veriyorsa bunları paralel olarak çağırmayı göz önünde bulundurun. Bir örnek için önceki bölüme bakın.
- Kullanıcı deneyimini daha uzun süre gecikmediğinde, API çağrısını yeniden denemeden önce bir zaman aşımı eşiği (örn. en fazla 5s) seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, yüz hizmetini kullanırken gecikme süresinin nasıl azaltılacağını öğrendiniz. Daha sonra, var olan PersonGroup ve çok yönlü liste nesnelerinden, sırasıyla LargePersonGroup ve Largecelist nesnelerine nasıl ölçeklentireceğinizi öğrenin.

> [!div class="nextstepaction"]
> [Örnek: büyük ölçekli özelliği kullanın](how-to-use-large-scale.md)

## <a name="related-topics"></a>İlgili konular

- [Başvuru belgeleri (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Başvuru belgeleri (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
