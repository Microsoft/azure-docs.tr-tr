---
title: "Örnek: büyük ölçekli özelliği kullanın-Yüz Tanıma API'si"
titleSuffix: Azure Cognitive Services
description: Bu kılavuz, var olan kişi grubundan ve çok yönlü nesnelerden LargePersonGroup ve Largeçok yönlü liste nesnelerine nasıl ölçekleneceye ilişkin bir makaledir.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/01/2019
ms.author: sbowles
ms.openlocfilehash: 976baaef11251715218ecea71986f08ec5f72996
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73743735"
---
# <a name="example-use-the-large-scale-feature"></a>Örnek: büyük ölçekli özelliği kullanın

Bu kılavuz, var olan kişi grubundan ve çok yönlü nesneleri sırasıyla LargePersonGroup ve Largeçok yönlü liste nesnelerine ölçeklendirmeye yönelik gelişmiş bir makaledir. Bu kılavuzda geçiş işlemi gösterilir. PersonGroup ve çok yönlü liste nesneleri, [eğitme](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4) işlemi ve yüz tanıma işlevleri ile temel bir benzerlik olduğunu varsayar. Bu konular hakkında daha fazla bilgi edinmek için, [yüz tanıma](../concepts/face-recognition.md) kavramsal Kılavuzu ' na bakın.

LargePersonGroup ve Largecelist, toplu olarak büyük ölçekli işlemler olarak adlandırılır. LargePersonGroup, her biri en fazla 248 yüzü olan en fazla 1.000.000 kişi içerebilir. Largecelıst, en fazla 1.000.000 yüz içerebilir. Büyük ölçekli işlemler geleneksel kişiler ve çok yönlü listesine benzerdir ancak yeni mimari nedeniyle bazı farklılıklar vardır. 

Örnekler, Azure bilişsel Hizmetler Yüz Tanıma API'si istemci kitaplığı kullanılarak yazılır C# .

> [!NOTE]
> Büyük ölçekte kimlik ve bulgulara benzeyen yüz arama performansını etkinleştirmek için, Largecelist ve LargePersonGroup 'u önceden işlemek üzere bir eğitme işlemi tanıtın. Eğitim süresi, gerçek kapasiteye göre saniyeler arasında bir saat kadar farklılık gösterir. Eğitim döneminde, daha önce başarılı bir eğitim işlemi yapıldıktan sonra kimlik ve Bulgubenzeri bir şekilde gerçekleştirilmesi mümkündür. Büyük ölçekli eğitimlere yeni bir post geçişi tamamlanana kadar, yeni eklenen kişilerin ve yüzlerin sonuç içinde görünmesidir.

## <a name="step-1-initialize-the-client-object"></a>1\. Adım: istemci nesnesini başlatma

Yüz Tanıma API'si istemci kitaplığını kullandığınızda, abonelik anahtarı ve abonelik uç noktası FaceClient sınıfının Oluşturucusu aracılığıyla geçirilir. Örneğin:

```csharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionEndpoint = "https://westus.api.cognitive.microsoft.com";
private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });
faceClient.Endpoint = SubscriptionEndpoint
```

Abonelik anahtarını karşılık gelen uç noktayla almak için Azure portal Azure Marketi 'ne gidin.
Daha fazla bilgi için bkz. [abonelikler](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="step-2-code-migration"></a>2\. Adım: kod geçişi

Bu bölüm, kişi veya çok yönlü liste uygulamasının LargePersonGroup veya Largeçok yönlü listesine nasıl geçirileceğiyle odaklanır. LargePersonGroup veya Largecelist, tasarım ve iç uygulamadaki PersonGroup veya çok yönlü listesinden farklılık gösterdiğinde, API arabirimleri geriye dönük uyumluluk için de benzerdir.

Veri geçişi desteklenmiyor. Bunun yerine LargePersonGroup veya Largeçok yönlü listesini yeniden oluşturursunuz.

### <a name="migrate-a-persongroup-to-a-largepersongroup"></a>Bir kişilik grubunu bir LargePersonGroup 'a geçirme

Bir PersonGroup 'tan LargePersonGroup 'a geçiş basittir. Bunlar, tam olarak aynı grup düzeyi işlemleri paylaşır.

PersonGroup veya Person ile ilgili uygulama için, yalnızca API yolları veya SDK sınıfı/modülünü LargePersonGroup ve LargePersonGroup Person olarak değiştirmeniz gerekir.

Tüm yüzleri ve kişileri PersonGroup 'tan yeni LargePersonGroup 'a ekleyin. Daha fazla bilgi için bkz. [yüz ekleme](how-to-add-faces.md).

### <a name="migrate-a-facelist-to-a-largefacelist"></a>Çok yönlü bir listeyi Largeçok yönlü bir listeye geçirme

| FaceList API’leri | LargeFaceList API’leri |
|:---:|:---:|
| Oluşturma | Oluşturma |
| Sil | Sil |
| Al | Al |
| Liste | Liste |
| Güncelleştirme | Güncelleştirme |
| - | Eğitim |
| - | Eğitim Durumunu Alma |

Yukarıdaki tabloda, FaceList ile LargeFaceList arasındaki liste düzeyinde işlemlerin karşılaştırması yer almaktadır. Gösterildiği gibi Largecelist, çok yönlü liste ile karşılaştırıldığında yeni işlemler, eğitme ve eğitim durumunu alır. Largeçok yönlü listesi eğitimi, [Findbenzer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) işlemin bir önkoşuludur. Çok yönlü liste için eğitim gerekli değildir. Aşağıdaki kod parçacığı, Largeçok yönlü listesinin eğitiminin bekleneceği bir yardımcı işlevdir:

```csharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceClient.LargeTrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await faceClient.LargeFaceList.TrainAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

Daha önce, ek yüzleri ve aşağıda gösterilene benzer şekilde, çok yönlü listenin tipik bir kullanımı aşağıda verilmiştir:

```csharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.FaceList.CreateAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.FaceList.AddFaceFromStreamAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

Bunu Largeçok yönlü listesine geçirirken, şunlar olur:

```csharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
faceClient.LargeFaceList.CreateAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await faceClient.LargeFaceList.AddFaceFromStreamAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = faceClient.Face.DetectWithStreamAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await faceClient.Face.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Daha önce gösterildiği gibi, veri yönetimi ve Bulgubenzeri Bölüm neredeyse aynıdır. Tek istisna, yeni bir ön işleme eğitme işleminin, Findbenzer çalışmadan önce Largecelist dosyasında tamamlanmasının gerekmektedir.

## <a name="step-3-train-suggestions"></a>3\. Adım: önerileri eğitme

Eğitme işlemi, [bulgulara benzer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) ve [tanımlamayı](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)hızsa da, eğitim süresi özellikle büyük ölçekte olduğunda, daha fazla zaman vardır. Farklı ölçeklerde tahmini eğitim süresi aşağıdaki tabloda listelenmiştir.

| Yüzler veya kişiler için ölçeklendirin | Tahmini eğitim süresi |
|:---:|:---:|
| 1000 | 1-2 sn |
| 10,000 | 5-10 sn |
| 100.000 | 1-2 dk |
| 1\.000.000 | 10-30 dk |

Büyük ölçekli özelliği daha iyi kullanmak için aşağıdaki stratejileri öneririz.

### <a name="step-31-customize-time-interval"></a>Adım 3,1: Saat aralığını özelleştirme

`TrainLargeFaceList()`gösterildiği gibi, sonsuz eğitim durum denetimi sürecini geciktirmek için milisaniye cinsinden bir zaman aralığı vardır. Daha fazla yüz içeren LargeFaceList için, büyük bir aralık kullanıldığında çağrı sayıları ve maliyeti azaltılır. Largeçok yönlü listesinin beklenen kapasitesine göre zaman aralığını özelleştirin.

Aynı strateji LargePersonGroup için de geçerlidir. Örneğin, 1.000.000 kişi ile bir LargePersonGroup 'u eğitedığınızda `timeIntervalInMilliseconds`, 1 dakikalık bir Aralık olan 60.000 olabilir.

### <a name="step-32-small-scale-buffer"></a>Adım 3,2: küçük ölçekli arabellek

LargePersonGroup veya largeçok yönlü listesindeki kişiler veya yüzler yalnızca eğitim alındıktan sonra aranabilir. Dinamik bir senaryoda yeni kişiler veya yüzler sürekli olarak eklenir ve anında aranabilir olması gerekir, ancak eğitim istenen şekilde daha uzun sürebilir. 

Bu sorunu azaltmak için, yalnızca yeni eklenen girişler için arabellek olarak fazladan bir küçük ölçekli LargePersonGroup veya Largecelist kullanın. Bu arabellek, daha küçük bir boyut nedeniyle eğitme süresini daha kısa sürer. Bu geçici arabellekte anında arama özelliği çalışmalıdır. Ana öğreticiyle bir sparser aralığı üzerinde çalıştırarak ana LargePersonGroup veya Largecesıya listesindeki eğitimle birlikte bu arabelleği kullanın. Örnekler gece ortasında ve günlük olarak gösterilir.

Örnek bir iş akışı:

1. Ana koleksiyon olan bir ana LargePersonGroup veya Largeçok yönlü listesi oluşturun. Arabellek koleksiyonu olan bir buffer LargePersonGroup veya Largeçok yönlü listesi oluşturun. Arabellek koleksiyonu yalnızca yeni eklenen kişiler veya yüzler için geçerlidir.
1. Hem ana koleksiyona hem de arabellek koleksiyonuna yeni kişiler veya yüzler ekleyin.
1. Yalnızca yeni eklenen girdilerin geçerli olduğundan emin olmak için arabellek koleksiyonunu kısa bir zaman aralığıyla eğitme.
1. Hem ana koleksiyona hem de arabellek koleksiyonuna karşı bir çağrı kimliği veya Findbenzerdir. Sonuçları birleştirin.
1. Arabellek toplama boyutu bir eşiğe veya Sistem boşta kalma süresine arttıkça, yeni bir arabellek koleksiyonu oluşturun. Ana koleksiyonda eğitme işlemini tetikleyin.
1. Ana koleksiyonda eğitme işlemi tamamlandıktan sonra eski arabellek koleksiyonunu silin.

### <a name="step-33-standalone-training"></a>Adım 3,3: tek başına eğitim

Oldukça uzun bir gecikme kabul edilebilir ise, yeni verileri ekledikten sonra eğitme işlemini tetiklemeniz gerekmez. Bunun yerine Eğitim işlemi, ana mantıktan ayrılabilir ve düzenli olarak tetiklenebilir. Bu strateji, kabul edilebilir gecikme süresine sahip dinamik senaryolar için uygundur. Bu, tren sıklığını daha fazla azaltmak için statik senaryolara uygulanabilir.

`TrainLargeFaceList`benzer bir `TrainLargePersonGroup` işlevi olduğunu varsayalım. `System.Timers` ' de [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) sınıfını çağırarak LargePersonGroup 'ta tek başına eğitimin tipik bir uygulamasıdır:

```csharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    faceClient.LargePersonGroup.CreateAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Set up standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1-minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1-hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces, and identification, except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Veri yönetimi ve tanımlama ile ilgili uygulamalar hakkında daha fazla bilgi için bkz. bir görüntüdeki [yüzler ekleme](how-to-add-faces.md) ve [yüzeyleri tanımlama](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a>Özet

Bu kılavuzda, var olan PersonGroup veya çok yönlü liste kodunun veri değil, LargePersonGroup veya Largeçok yönlü listesine nasıl geçirileceğiyle öğrendiniz:

- LargePersonGroup ve Largecelist, Largecegroup veya cese listesi gibi çalışarak, largeçok yönlü listesi için tren işlemi gereklidir.
- Büyük ölçekli veri kümeleri için dinamik veri güncelleştirmesine yönelik doğru eğitme stratejisini alın.

## <a name="next-steps"></a>Sonraki adımlar

Bir kişilik grubuna yüz ekleme veya bir kişilik grubu üzerinde belirleme işlemini yürütme hakkında bilgi edinmek için nasıl yapılır kılavuzunu izleyin.

- [Yüz Ekle](how-to-add-faces.md)
- [Görüntüdeki yüzeyleri tanımla](HowtoIdentifyFacesinImage.md)
