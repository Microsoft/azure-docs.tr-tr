---
title: Bir tanıma modeli belirtme-Yüz Tanıma API'si
titleSuffix: Azure Cognitive Services
description: Bu makalede, Azure Yüz Tanıma API'si uygulamanızla hangi tanıma modelinin kullanılacağını nasıl seçeceğiniz gösterilmektedir.
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: 23c54a69f709ec97d895ed5965841e43ebdc560c
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306556"
---
# <a name="specify-a-face-recognition-model"></a>Yüz tanıma modeli belirtme

Bu kılavuzda, Azure Yüz Tanıma API'si kullanarak yüz algılama, tanımlama ve benzerlik araması için bir yüz tanıma modeli belirtme konusu gösterilmektedir.

Yüz Tanıma API'si, görüntülerde insan yüzeyleri üzerinde işlem gerçekleştirmek için makine öğrenimi modellerini kullanır. Müşteri geri bildirimlerine ve araştırmada ilerlemeleri temel alan modellerimizin doğruluğunu geliştirmeye devam ediyoruz ve bu geliştirmeleri model güncelleştirmeleri olarak sunduk. Geliştiriciler, yüz tanıma modeli 'nin hangi sürümünü kullanmak istediğinizi belirtme seçeneğine sahiptir; Kullanım durumlarına en uygun modeli seçebilirler.

Yeni bir Kullanıcı kullanıyorsanız en son modeli kullanmanızı öneririz. Model çakışmalarını önlarken farklı yüz işlemlerinde nasıl belirteceğinizi öğrenmek için okumaya devam edin. İleri düzey bir kullanıcıysanız ve en son modele geçiş yapmanız gerekip gerekmediğine emin değilseniz, yeni modeli değerlendirmek ve sonuçları geçerli veri kümesi kullanarak karşılaştırmak için [farklı modelleri değerlendir](#evaluate-different-models) bölümüne atlayın.

## <a name="prerequisites"></a>Önkoşullar

AI yüz tanıma ve tanımlama kavramlarını tanımanız gerekir. Değilseniz, önce bu nasıl yapılır kılavuzlarını inceleyin:

* [Görüntüdeki yüzeyleri algılama](HowtoDetectFacesinImage.md)
* [Görüntüdeki yüzeyleri belirleme](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Belirtilen modelle yüzler Algıla

Yüz algılama, insan yüzlerini görsel olarak tanımlar ve bunların sınırlayıcı kutu konumlarını bulur. Ayrıca, yüzün özelliklerini ayıklar ve bu özellikleri, kimlik ' de kullanılmak üzere depolar. Bu bilgilerin tümü bir yüzün temsilini oluşturur.

Tanınma modeli, yüz özellikleri ayıklandığında kullanılır, bu nedenle algılama işlemini gerçekleştirirken bir model sürümü belirtebilirsiniz.

[Yüz algılama] API 'sini kullanırken, model sürümünü `recognitionModel` parametresiyle atayın. Kullanılabilir değerler şunlardır:

* `recognition_01`
* `recognition_02`

İsteğe bağlı olarak, _Recognıtionmodel_ 'in yanıt olarak döndürülüp döndürülmeyeceğini belirtmek Için _returnrecognıtionmodel_ parametresini (varsayılan **yanlış**) belirtebilirsiniz. Bu nedenle, [yüz algılama] REST API için bir istek URL 'si şöyle görünür:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

İstemci kitaplığını kullanıyorsanız, sürümünü temsil eden bir dize geçirerek değerini `recognitionModel` atayabilirsiniz.
Atanmamış olarak bırakırsanız, varsayılan model sürümü (_recognition_01_) kullanılacaktır. .NET istemci kitaplığı için aşağıdaki kod örneğine bakın.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Belirtilen modelle yüzeyleri tanımla

Yüz Tanıma API'si bir görüntüden yüz verileri çıkarabilir ve bir **kişi** nesnesiyle (örneğin, [yüz tanıma](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API çağrısı aracılığıyla) Ilişkilendirebilir ve birden çok **kişi** nesnesi bir **persongroup**'ta birlikte depolanabilir. Daha sonra **Yeni bir yüz** , bir Person ( [Yüz - Belirleme] tanıma çağrısıyla) ile karşılaştırılabilir ve bu gruptaki eşleşen kişi belirlenebilir.

Bir **persongroup** 'un tüm **kişiler**için benzersiz bir tanıma modeli olması gerekir ve `recognitionModel` bunu, grubu oluştururken ([PersonGroup - Oluşturma] veya [LargePersonGroup - Oluşturma]) parametresini kullanarak belirtebilirsiniz. Bu parametreyi belirtmezseniz, özgün `recognition_01` model kullanılır. Bir grup her zaman ile oluşturulduğu tanıma modelini kullanır ve bu modele eklendiğinde yeni yüzler bu modelle ilişkilendirilir; Bu, bir grubun oluşturulduktan sonra değiştirilemez. Bir **persongroup** 'un hangi modele yapılandırıldığını görmek Için, _Returnrecognıtionmodel_ parametresi **true**olarak ayarlanmış olan [Kişilik grubu-al] API 'sini kullanın.

.NET istemci kitaplığı için aşağıdaki kod örneğine bakın.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

Bu kodda, kimliği `mypersongroupid` olan bir **persongroup** oluşturulur ve yüz özelliklerini ayıklamak için _recognition_02_ modelini kullanmak üzere ayarlanır.

Buna karşılık, bu **kişinin** karşılaştırılacağı yüzeyleri tespit etmek için kullanılacak modeli ( [Yüz algılama] API 'si aracılığıyla) belirlemeniz gerekir. Kullandığınız model, her zaman **Persongroup**'un yapılandırmasıyla tutarlı olmalıdır; Aksi takdirde, işlem uyumsuz modeller nedeniyle başarısız olur.

[Yüz - Belirleme] tanıma API 'sinde değişiklik yoktur; Yalnızca algılama sürümünde model sürümünü belirtmeniz gerekir.

## <a name="find-similar-faces-with-specified-model"></a>Belirtilen modelle benzer yüzler bulun

Ayrıca, benzerlik arama için bir tanıma modeli de belirtebilirsiniz. `recognitionModel` Calıst [Çok yönlü liste-oluştur] API veya [Largecelist-oluştur]ile yüz listesini oluştururken ile model sürümünü atayabilirsiniz. Bu parametreyi belirtmezseniz, özgün `recognition_01` model kullanılır. Yüz listesi, her zaman ile oluşturulduğu tanıma modelini kullanır ve bunlara eklendiğinde yeni yüzler bu modelle ilişkilendirilecektir; Bu, oluşturulduktan sonra değiştirilemez. Yüz listesinin hangi modelde yapılandırıldığını görmek için, şu şekilde, _Returnrecognıtionmodel_ parametresi **true**olarak ayarlanmış olan çok [Çok yönlü liste-Al] API 'sini kullanın.

.NET istemci kitaplığı için aşağıdaki kod örneğine bakın.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

Bu kod, özellik ayıklama için `My face collection` _recognition_02_ modeli kullanılarak adlı bir yüz listesi oluşturur. Bu yüz listesini, benzer yüzler için yeni bir algılanan bir yüze aradığınızda, _recognition_02_ modeli kullanılarak bu yüzün algılanan ([Yüz algılama]) olması gerekir. Önceki bölümde olduğu gibi, modelin tutarlı olması gerekir.

Yüz, benzer bir API 'yi [Yüz-benzerini bulun] ; Yalnızca algılama sürümünde model sürümünü belirtirsiniz.

## <a name="verify-faces-with-specified-model"></a>Belirtilen modelle yüzeyleri doğrula

Yüz doğrulama API 'si, iki [Yüz-doğrulama] aynı kişiye ait olup olmadığını denetler. Doğrulama API 'sinde, tanıma modelleriyle ilgili bir değişiklik yoktur, ancak yalnızca aynı modelde algılanan yüzeyleri karşılaştırabilirsiniz. Bu nedenle, iki yüzün veya `recognition_01` `recognition_02`kullanılarak algılanabilmesi gerekir.

## <a name="evaluate-different-models"></a>Farklı modelleri değerlendirin

Verilerinizde _recognition_01_ ve _recognition_02_ modellerinin performanslarını karşılaştırmak istiyorsanız şunları yapmanız gerekir:

1. Sırasıyla _recognition_01_ ve _Recognition_02_ ile iki **persongroup**oluşturun.
1. Yüzleri algılamak ve bu iki **Personb grubu**için eğitim işlemini tetiklemek ve **Person** [PersonGroup - Eğitme] API 'si ile eğitim sürecini tetiklemek için görüntü verilerinizi kullanın.
1. Yüz ile test edin [Yüz - Belirleme] hem **persongroup**'lar üzerinde hem de sonuçları karşılaştırın.

Normalde bir güvenirlik eşiği (sıfır ile arasında bir değer, modelin bir yüzü belirlemek için ne kadar güvenli olacağını belirleyen bir değer) belirtirseniz, farklı modeller için farklı eşikler kullanmanız gerekebilir. Bir modelin eşiği, diğer ile paylaşılmak üzere tasarlanmamıştır ve aynı sonuçları üretmeyecektir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, farklı yüz hizmeti API 'Leri ile kullanmak üzere tanınma modelini nasıl belirtdiğinize öğrendiniz. Sonra, yüz algılamayı kullanmaya başlamak için hızlı başlangıç izleyin.

* [Yüz .NET SDK 'Sı](../Quickstarts/csharp-sdk.md)
* [Yüz Python SDK 'Sı](../Quickstarts/python-sdk.md)

[Yüz algılama]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Yüz-benzerini bulun]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Yüz - Belirleme]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Yüz-doğrulama]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Oluşturma]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[Kişilik grubu-al]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Eğitme]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Oluşturma]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[Çok yönlü liste-oluştur]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[Çok yönlü liste-Al]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[Largecelist-oluştur]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
