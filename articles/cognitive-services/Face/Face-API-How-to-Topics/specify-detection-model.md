---
title: Algılama modeli belirtme-Yüz Tanıma API'si
titleSuffix: Azure Cognitive Services
description: Bu makalede, Azure Yüz Tanıma API'si uygulamanızla hangi yüz algılama modelinin kullanılacağını nasıl seçeceğiniz gösterilmektedir.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 4306a918d56240bfe038100124b3c2b94964cebc
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306675"
---
# <a name="specify-a-face-detection-model"></a>Yüz algılama modeli belirtme

Bu kılavuzda, Azure Yüz Tanıma API'si için bir yüz algılama modeli belirtme gösterilmektedir.

Yüz Tanıma API'si, görüntülerde insan yüzeyleri üzerinde işlem gerçekleştirmek için makine öğrenimi modellerini kullanır. Müşteri geri bildirimlerine ve araştırmada ilerlemeleri temel alan modellerimizin doğruluğunu geliştirmeye devam ediyoruz ve bu geliştirmeleri model güncelleştirmeleri olarak sunduk. Geliştiriciler, yüz algılama modelinin hangi sürümünü kullanmak istediğinizi belirtme seçeneğine sahiptir; Kullanım durumlarına en uygun modeli seçebilirler.

Belirli yüz işlemlerinde yüz algılama modelini nasıl belirteceğinizi öğrenmek için okumaya devam edin. Yüz Tanıma API'si, bir yüzün görüntüsünü başka bir veri biçimine dönüştürdüğünde yüz algılamayı kullanır.

En son modeli kullanıp kullanmayacağınızı bilmiyorsanız, yeni modeli değerlendirmek ve sonuçları geçerli veri kümesi kullanarak karşılaştırmak için [farklı modelleri değerlendir](#evaluate-different-models) bölümüne atlayın.

## <a name="prerequisites"></a>Önkoşullar

AI yüz algılaması kavramı hakkında bilgi sahibi olmanız gerekir. Değilseniz, yüz algılama kavramsal Kılavuzu veya nasıl yapılır Kılavuzu ' na bakın.

* [Yüz algılama kavramları](../concepts/face-detection.md)
* [Görüntüdeki yüzeyleri algılama](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Belirtilen modelle yüzler Algıla

Yüz algılama, insan yüzlerinin sınırlama kutusu konumlarını bulur ve görsel çizgilerini tanımlar. Bu, yüzün özelliklerini ayıklar ve daha sonra [tanıma](../concepts/face-recognition.md) işlemlerinde kullanılmak üzere depolar.

[Yüz algılama] API 'sini kullandığınızda, `detectionModel` parametresi ile model sürümünü atayabilirsiniz. Kullanılabilir değerler şunlardır:

* `detection_01`
* `detection_02`

[Yüz algılama] REST API için bir istek URL 'si şöyle görünür:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

İstemci kitaplığını kullanıyorsanız, uygun bir dizeye geçirerek değerini `detectionModel` atayabilirsiniz. Atanmamış olarak bırakırsanız, API varsayılan model sürümünü (`detection_01`) kullanır. .NET istemci kitaplığı için aşağıdaki kod örneğine bakın.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>Belirtilen modele sahip kişiye yüz ekleyin

Yüz Tanıma API'si, bir görüntüden yüz verileri ayıklayabilir ve Person [kişi-yüz tanıma](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API 'si aracılığıyla bir **kişi** nesnesiyle ilişkilendirebilirler. Bu API çağrısında, algılama modelini [Yüz algılama]aynı şekilde belirtebilirsiniz.

.NET istemci kitaplığı için aşağıdaki kod örneğine bakın.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

Bu kod, kimliği `mypersongroupid` olan bir **persongroup** oluşturur ve bu gruba bir **kişi** ekler. Ardından, `detection_02` modeli kullanarak bu **kişiye** bir yüz ekler. *DetectionModel* parametresini BELIRTMEZSENIZ, API varsayılan modeli `detection_01`kullanır.

> [!NOTE]
> Bir **kişi** nesnesindeki tüm yüzler için aynı algılama modelini kullanmanız gerekmez ve bir **kişi** nesnesiyle Karşılaştırılacak yeni yüzeyleri algılamada aynı algılama modelini kullanmanız gerekmez (örneğin, [Yüz - Belirleme] tanıma API 'si).

## <a name="add-face-to-facelist-with-specified-model"></a>Belirtilen modelle çok yönlü listeye yüz ekleme

Ayrıca, mevcut bir çok **yönlü liste** nesnesine bir yüz eklediğinizde bir algılama modeli de belirtebilirsiniz. .NET istemci kitaplığı için aşağıdaki kod örneğine bakın.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

Bu kod, `My face collection` `detection_02` adlı bir çok **yönlü liste** oluşturur ve modele sahip bir yüz ekler. *DetectionModel* parametresini BELIRTMEZSENIZ, API varsayılan modeli `detection_01`kullanır.

> [!NOTE]
> Bir çok **yönlü liste** nesnesindeki tüm yüzler için aynı algılama modelini kullanmanız gerekmez ve çok **yönlü bir liste** nesnesiyle Karşılaştırılacak yeni yüzeyleri algılamada aynı algılama modelini kullanmanız gerekmez.

## <a name="evaluate-different-models"></a>Farklı modelleri değerlendirin

Farklı yüz algılama modelleri farklı görevler için iyileştirilmiştir. Farklılıklara genel bakış için aşağıdaki tabloya bakın.

|**detection_01**  |**detection_02**  |
|---------|---------|
|Tüm yüz algılama işlemleri için varsayılan seçenek. | 2019 Mayıs 'ta serbest bırakılır ve isteğe bağlı olarak tüm yüz algılama işlemlerinde kullanılabilir.
|Küçük, yan görünüm veya bulanık yüz için en iyi duruma getirilmemiştir.  | Küçük, yan görünüm ve bulanık yüzler üzerinde geliştirilmiş doğruluk. |
|Algıla çağrısında belirtilerse yüz özniteliklerini (Head, Age, duygu vb.) döndürür. |  Yüz özniteliklerini döndürmez.     |
|Algıla çağrısında belirtilerse yüz çizgilerini döndürür.   | Yüz yer işaretlerini döndürmez.  |

`detection_01` Ve`detection_02` modellerinin performanslarını karşılaştırmak için en iyi yol, bunları örnek bir veri kümesi üzerinde kullanmaktır. [Yüz algılama] API 'sini çeşitli görüntülerde, özellikle de görülmesi zor olan çok sayıda yüzün veya yüzlerin her bir algılama modeli kullanılarak çağrılmasını öneririz. Her modelin döndürdüğü yüz sayısına dikkat edin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, farklı yüz API 'Leri ile kullanmak üzere algılama modelinin nasıl belirtildiklerini öğrendiniz. Sonra, yüz algılamayı kullanmaya başlamak için hızlı başlangıç izleyin.

* [Yüz .NET SDK 'Sı](../Quickstarts/csharp-sdk.md)
* [Yüz Python SDK 'Sı](../Quickstarts/python-sdk.md)

[Yüz algılama]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
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
