---
title: Bir algılama modeli belirleme-yüz
titleSuffix: Azure Cognitive Services
description: Bu makalede, Azure yüz uygulamanızla hangi yüz algılama modelinin kullanılacağını nasıl seçeceğiniz gösterilmektedir.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: yluiu
ms.custom: devx-track-csharp
ms.openlocfilehash: 72fd005ce44d116f86d9a0b4c0d1932e2e4facfb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102425779"
---
# <a name="specify-a-face-detection-model"></a>Yüz algılama modeli belirtme

Bu kılavuzda, Azure yüz hizmeti için bir yüz algılama modeli belirtme gösterilmektedir.

Yüz hizmeti, görüntülerde insan yüzeyleri üzerinde işlem gerçekleştirmek için makine öğrenimi modellerini kullanır. Müşteri geri bildirimlerine ve araştırmada ilerlemeleri temel alan modellerimizin doğruluğunu geliştirmeye devam ediyoruz ve bu geliştirmeleri model güncelleştirmeleri olarak sunduk. Geliştiriciler, yüz algılama modelinin hangi sürümünü kullanmak istediğinizi belirtme seçeneğine sahiptir; Kullanım durumlarına en uygun modeli seçebilirler.

Belirli yüz işlemlerinde yüz algılama modelini nasıl belirteceğinizi öğrenmek için okumaya devam edin. Yüz tanıma hizmeti bir yüzün görüntüsünü başka bir veri biçimine dönüştürdüğünde yüz algılamayı kullanır.

En son modeli kullanıp kullanmayacağınızı bilmiyorsanız, yeni modeli değerlendirmek ve sonuçları geçerli veri kümesi kullanarak karşılaştırmak için [farklı modelleri değerlendir](#evaluate-different-models) bölümüne atlayın.

## <a name="prerequisites"></a>Önkoşullar

AI yüz algılaması kavramı hakkında bilgi sahibi olmanız gerekir. Değilseniz, yüz algılama kavramsal Kılavuzu veya nasıl yapılır Kılavuzu ' na bakın.

* [Yüz algılama kavramları](../concepts/face-detection.md)
* [Görüntüdeki yüzeyleri algılama](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Belirtilen modelle yüzler Algıla

Yüz algılama, insan yüzlerinin sınırlama kutusu konumlarını bulur ve görsel çizgilerini tanımlar. Bu, yüzün özelliklerini ayıklar ve daha sonra [tanıma](../concepts/face-recognition.md) işlemlerinde kullanılmak üzere depolar.

[Yüz algılama] API 'sini kullandığınızda, parametresi ile model sürümünü atayabilirsiniz `detectionModel` . Kullanılabilir değerler şunlardır:

* `detection_01`
* `detection_02`
* `detection_03`

[Yüz algılama] REST API için bir istek URL 'si şöyle görünür:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

İstemci kitaplığını kullanıyorsanız, `detectionModel` uygun bir dizeye geçirerek değerini atayabilirsiniz. Atanmamış olarak bırakırsanız, API varsayılan model sürümünü ( `detection_01` ) kullanır. .NET istemci kitaplığı için aşağıdaki kod örneğine bakın.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_04", detectionModel: "detection_03");
```

## <a name="add-face-to-person-with-specified-model"></a>Belirtilen modele sahip kişiye yüz ekleyin

Yüz tanıma hizmeti bir görüntüden yüz verileri ayıklayabilir ve Person [kişi-yüz tanıma](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API 'si aracılığıyla bir **kişi** nesnesiyle ilişkilendirebilirler. Bu API çağrısında, algılama modelini [yüz algılamayla]aynı şekilde belirtebilirsiniz.

.NET istemci kitaplığı için aşağıdaki kod örneğine bakın.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_03" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_04");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_03");
```

Bu kod, KIMLIĞI olan bir **Persongroup** oluşturur ve bu gruba `mypersongroupid` bir **kişi** ekler. Ardından, modeli kullanarak bu **kişiye** bir yüz ekler `detection_03` . *DetectionModel* parametresini BELIRTMEZSENIZ, API varsayılan modeli kullanır `detection_01` .

> [!NOTE]
> Bir **kişi** nesnesindeki tüm yüzler için aynı algılama modelini kullanmanız gerekmez ve bir **kişi** nesnesiyle Karşılaştırılacak yeni yüzeyleri algılamada aynı algılama modelini kullanmanız gerekmez (örneğin, [yüz] tanıma API 'si).

## <a name="add-face-to-facelist-with-specified-model"></a>Belirtilen modelle çok yönlü listeye yüz ekleme

Ayrıca, mevcut bir çok **yönlü liste** nesnesine bir yüz eklediğinizde bir algılama modeli de belirtebilirsiniz. .NET istemci kitaplığı için aşağıdaki kod örneğine bakın.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_04");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_03");
```

Bu kod, adlı bir çok **yönlü liste** oluşturur `My face collection` ve modele sahip bir yüz ekler `detection_03` . *DetectionModel* parametresini BELIRTMEZSENIZ, API varsayılan modeli kullanır `detection_01` .

> [!NOTE]
> Bir çok **yönlü liste** nesnesindeki tüm yüzler için aynı algılama modelini kullanmanız gerekmez ve çok **yönlü bir liste** nesnesiyle Karşılaştırılacak yeni yüzeyleri algılamada aynı algılama modelini kullanmanız gerekmez.

## <a name="evaluate-different-models"></a>Farklı modelleri değerlendirin

Farklı yüz algılama modelleri farklı görevler için iyileştirilmiştir. Farklılıklara genel bakış için aşağıdaki tabloya bakın.

|**detection_01**  |**detection_02**  |**detection_03** 
|---------|---------|---|
|Tüm yüz algılama işlemleri için varsayılan seçenek. | 2019 Mayıs 'ta serbest bırakılır ve isteğe bağlı olarak tüm yüz algılama işlemlerinde kullanılabilir. |  Şubat 2021 ' de yayımlanmıştır ve isteğe bağlı olarak tüm yüz algılama işlemlerinde kullanılabilir.
|Küçük, yan görünüm veya bulanık yüz için en iyi duruma getirilmemiştir.  | Küçük, yan görünüm ve bulanık yüzler üzerinde geliştirilmiş doğruluk. | Daha küçük yüzler (64x64 piksel) ve döndürülmüş yüz yönleri dahil daha gelişmiş doğruluk.
|Algıla çağrısında belirtilerse ana yüzleri özniteliklerini (Head, Age, duygu vb.) döndürür. |  Yüz özniteliklerini döndürmez.     | Algıla çağrısında belirtilerse "çok yönlü maske" ve "Noseandmouthkapsanan" özniteliklerini döndürür.
|Algıla çağrısında belirtilerse yüz çizgilerini döndürür.   | Yüz yer işaretlerini döndürmez.  | Yüz yer işaretlerini döndürmez.

Algılama modellerinin performanslarını karşılaştırmanın en iyi yolu, bunları örnek bir veri kümesi üzerinde kullanmaktır. [Yüz tanıma] API 'sini çeşitli görüntülerde, özellikle de görülmesi zor olan çok sayıda yüzün veya yüzlerin her bir algılama modeli kullanılarak çağrılmasını öneririz. Her modelin döndürdüğü yüz sayısına dikkat edin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, farklı yüz API 'Leri ile kullanmak üzere algılama modelinin nasıl belirtildiklerini öğrendiniz. Sonra, yüz algılamayı kullanmaya başlamak için hızlı başlangıç izleyin.

* [Yüz .NET SDK 'Sı](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Yüz Python SDK 'Sı](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Yüz go SDK 'Sı](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[Yüz - Algılama]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Yüz - Belirleme]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc