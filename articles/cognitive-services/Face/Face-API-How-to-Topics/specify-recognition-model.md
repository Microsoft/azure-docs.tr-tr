---
title: Bir tanıma modeli belirtme-yüz
titleSuffix: Azure Cognitive Services
description: Bu makalede, Azure yüz uygulamanızla birlikte kullanılacak tanınma modelini nasıl seçeceğiniz gösterilmektedir.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: longl
ms.custom: devx-track-csharp
ms.openlocfilehash: 3d22f2fb0bb550d966cbc5e181882552514513b2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936589"
---
# <a name="specify-a-face-recognition-model"></a>Yüz tanıma modeli belirtme

Bu kılavuzda, Azure yüz hizmetini kullanarak yüz algılama, tanımlama ve benzerlik arama için bir yüz tanıma modeli belirtme konusu gösterilmektedir.

Yüz hizmeti, görüntülerde insan yüzeyleri üzerinde işlem gerçekleştirmek için makine öğrenimi modellerini kullanır. Müşteri geri bildirimlerine ve araştırmada ilerlemeleri temel alan modellerimizin doğruluğunu geliştirmeye devam ediyoruz ve bu geliştirmeleri model güncelleştirmeleri olarak sunduk. Geliştiriciler, yüz tanıma modeli 'nin hangi sürümünü kullanmak istediğinizi belirtme seçeneğine sahiptir; Kullanım durumlarına en uygun modeli seçebilirler.

Azure yüz hizmeti 'nin kullanılabilir dört tanıma modeli vardır. Modeller _recognition_01_ (yayımlanan 2017), _recognition_02_ (yayımlanan 2019) ve _recognition_03_ (yayımlanmış 2020), bu modellerle oluşturulan çok yönlü listeler veya **persongroup**'lar kullanan müşteriler için geriye dönük uyumluluk sağlamak amacıyla sürekli olarak desteklenmektedir. Bir çok **yönlü liste** veya **kişilik grubu** her zaman ile oluşturulduğu tanınma modelini kullanır ve eklendiklerinde yeni yüzler bu modelle ilişkilendirilir. Bu, oluşturulduktan sonra değiştirilemez ve müşterilerin karşılık gelen çok **yönlü liste** veya **persongroup** ile ilgili tanıma modelini kullanması gerekir.

Daha sonraki tanıma modellerine daha kolay geçiş yapabilirsiniz; Ancak, tercih ettiğiniz tanıma modeliyle yeni bir çok yönlü listeler ve kişiler oluşturmanız gerekecektir.

_Recognition_04_ modeli (yayımlanan 2021) Şu anda kullanılabilir en doğru modeldir. Yeni bir müşteriyseniz, bu modeli kullanmanızı öneririz. _Recognition_04_ , hem benzerlik karşılaştırmaları hem de kişi ile eşleşen karşılaştırmalar için iyileştirilmiş doğruluk sağlar. _Recognition_04_ , yüz kapamaları (Surgical maskeleri, N95 maskeleri, kumaş maskeleri) takan kullanıcılar için tanımayı geliştirir. Artık, kayıtlı bir kullanıcının yüz kapağı içerip içermediğini tespit etmek ve ardından en son _recognition_04_ modeliyle nerede olduklarını tanımak için en son _detection_03_ modelini kullanan güvenli ve sorunsuz kullanıcı deneyimleri oluşturabilirsiniz. Her bir modelin diğerlerinden bağımsız olarak çalıştığını ve bir model için bir güven eşiği kümesinin diğer tanıma modelleriyle karşılaştırılmadığını unutmayın.

Model çakışmalarını önleyerek, farklı yüz işlemlerinde seçili bir modeli nasıl belirteceğinizi öğrenmek için okumaya devam edin. İleri düzey bir kullanıcıysanız ve en son modele geçiş yapmanız gerekip gerekmediğini öğrenmek isterseniz, yeni modeli değerlendirmek ve sonuçları geçerli veri kümesi kullanarak karşılaştırmak için [farklı modelleri değerlendir](#evaluate-different-models) bölümüne atlayın.


## <a name="prerequisites"></a>Önkoşullar

AI yüz tanıma ve tanımlama kavramlarını tanımanız gerekir. Değilseniz, önce şu kılavuzlara bakın:

* [Yüz algılama kavramları](../concepts/face-detection.md)
* [Yüz tanıma kavramları](../concepts/face-recognition.md)
* [Görüntüdeki yüzeyleri algılama](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Belirtilen modelle yüzler Algıla

Yüz algılama, insan yüzlerini görsel olarak tanımlar ve bunların sınırlayıcı kutu konumlarını bulur. Ayrıca, yüzün özelliklerini ayıklar ve bu özellikleri, kimlik ' de kullanılmak üzere depolar. Bu bilgilerin tümü bir yüzün temsilini oluşturur.

Tanınma modeli, yüz özellikleri ayıklandığında kullanılır, bu nedenle algılama işlemini gerçekleştirirken bir model sürümü belirtebilirsiniz.

[Yüz algılama] API 'sini kullanırken, model sürümünü `recognitionModel` parametresiyle atayın. Kullanılabilir değerler şunlardır:
* recognition_01
* recognition_02
* recognition_03
* recognition_04


İsteğe bağlı olarak, _Recognıtionmodel_ 'in yanıt olarak döndürülüp döndürülmeyeceğini belirtmek Için _returnrecognıtionmodel_ parametresini (varsayılan **yanlış**) belirtebilirsiniz. Bu nedenle, [yüz algılama] REST API için bir istek URL 'si şöyle görünür:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

İstemci kitaplığını kullanıyorsanız, `recognitionModel` sürümünü temsil eden bir dize geçirerek değerini atayabilirsiniz. Atanmamış olarak bırakırsanız, varsayılan bir model sürümü `recognition_01` kullanılacaktır. .NET istemci kitaplığı için aşağıdaki kod örneğine bakın.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_01", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Belirtilen modelle yüzeyleri tanımla

Yüz tanıma hizmeti bir görüntüden yüz verileri çıkarabilir ve bunu bir **kişi** nesnesiyle (örneğin, [yüz tanıma](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API çağrısı aracılığıyla) Ilişkilendirebilir ve birden çok **kişi** nesnesi bir **persongroup**'ta birlikte depolanabilir. Daha sonra **Yeni bir yüz** , bir Person ( [yüz] tanıma çağrısıyla) ile karşılaştırılabilir ve bu gruptaki eşleşen kişi belirlenebilir.

Bir **persongroup** 'un tüm **kişiler** için benzersiz bir tanıma modeli olması gerekir ve bunu, `recognitionModel` grubu oluştururken ([Persongroup-Create] veya [largepersongroup-Create]) parametresini kullanarak belirtebilirsiniz. Bu parametreyi belirtmezseniz, özgün `recognition_01` model kullanılır. Bir grup her zaman ile oluşturulduğu tanıma modelini kullanır ve bu modele eklendiğinde yeni yüzler bu modelle ilişkilendirilir; Bu, bir grubun oluşturulduktan sonra değiştirilemez. Bir **persongroup** 'un hangi modele yapılandırıldığını görmek Için, _Returnrecognıtionmodel_ parametresi **true** olarak ayarlanmış olan [persongroup-Get] API 'sini kullanın.

.NET istemci kitaplığı için aşağıdaki kod örneğine bakın.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

Bu kodda, KIMLIĞI olan bir **Persongroup** `mypersongroupid` oluşturulur ve yüz özelliklerini ayıklamak için _recognition_02_ modelini kullanmak üzere ayarlanır.

Buna karşılık, bu **kişinin** karşılaştırılacağı yüzeyleri tespit etmek için kullanılacak modeli ( [yüz tanıma] API 'si aracılığıyla) belirlemeniz gerekir. Kullandığınız model, her zaman **Persongroup**'un yapılandırmasıyla tutarlı olmalıdır; Aksi takdirde, işlem uyumsuz modeller nedeniyle başarısız olur.

[Yüz] tanıma API 'sinde değişiklik yoktur; Yalnızca algılama sürümünde model sürümünü belirtmeniz gerekir.

## <a name="find-similar-faces-with-specified-model"></a>Belirtilen modelle benzer yüzler bulun

Ayrıca, benzerlik arama için bir tanıma modeli de belirtebilirsiniz. Çok yönlü liste oluşturma `recognitionModel` API 'si veya [Largeçok yönlü liste-oluştur]ile çok **yönlü** liste oluştururken model sürümünü atayabilirsiniz. [] Bu parametreyi belirtmezseniz, `recognition_01` model varsayılan olarak kullanılır. Çok **yönlü bir liste** , her zaman ile oluşturulduğu tanıma modelini kullanır ve listeye eklendiğinde yeni yüzler bu modelle ilişkilendirilir; Bu, oluşturulduktan sonra değiştirilemez. Çok **yönlü bir listenin** hangi modelde yapılandırıldığını görmek için, tam olarak, _Returnrecognıtionmodel_ parametresi **true** olarak ayarlanmış olan çok [yönlü liste-Al] API 'sini kullanın.

.NET istemci kitaplığı için aşağıdaki kod örneğine bakın.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_04");
```

Bu kod  `My face collection` , özellik ayıklama için _recognition_04_ modeli kullanılarak adlı bir çok yönlü liste oluşturur. Benzer yüzler için bu çok **yönlü listeyi** yeni algılanan bir yüze aradığınızda, bu yüz _recognition_04_ modeli kullanılarak algılanan ([yüz Algıla]) olmalıdır. Önceki bölümde olduğu gibi, modelin tutarlı olması gerekir.

Yüz, benzer bir API 'yi [bulun] ; Yalnızca algılama sürümünde model sürümünü belirtirsiniz.

## <a name="verify-faces-with-specified-model"></a>Belirtilen modelle yüzeyleri doğrula

Yüz doğrulama API 'si, iki [yüzün] aynı kişiye ait olup olmadığını denetler. Doğrulama API 'sinde, tanıma modelleriyle ilgili bir değişiklik yoktur, ancak yalnızca aynı modelde algılanan yüzeyleri karşılaştırabilirsiniz.

## <a name="evaluate-different-models"></a>Farklı modelleri değerlendirin

Kendi verilerinizde farklı tanıma modellerinin performanslarını karşılaştırmak isterseniz, şunları yapmanız gerekir:
1. Sırasıyla _recognition_01_, _recognition_02_, _Recognition_03_ ve _recognition_04_ kullanarak dört kişilik oluşturun.
1. Yüzleri algılamak ve bu dört **kişilik** Bu **kişilerin içindeki kişilere** kaydetmek için görüntü verilerinizi kullanın. 
1. PersonGroup-tren API 'sini kullanarak kişi gruplarınızı eğitme.
1. Yüz ile test edin ve tüm dört **Persongroup**'Lar üzerinde tanımlayarak sonuçları karşılaştırın.


Normalde bir güvenirlik eşiği (sıfır ile arasında bir değer, modelin bir yüzü belirlemek için ne kadar güvenli olacağını belirleyen bir değer) belirtirseniz, farklı modeller için farklı eşikler kullanmanız gerekebilir. Bir modelin eşiği, diğer ile paylaşılmak üzere tasarlanmamıştır ve aynı sonuçları üretmeyecektir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, farklı yüz hizmeti API 'Leri ile kullanmak üzere tanınma modelini nasıl belirtdiğinize öğrendiniz. Sonra, yüz algılamayı kullanmaya başlamak için hızlı başlangıç izleyin.

* [Yüz .NET SDK 'Sı](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Yüz Python SDK 'Sı](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Yüz go SDK 'Sı](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[Yüz - Algılama]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Yüz-benzerini bulun]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Yüz - Belirleme]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Yüz-doğrulama]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Oluşturma]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[Kişilik grubu-al]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Oluşturma]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[Çok yönlü liste-oluştur]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[Çok yönlü liste-Al]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[Largecelist-oluştur]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc