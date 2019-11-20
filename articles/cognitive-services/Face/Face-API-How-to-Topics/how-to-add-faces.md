---
title: "Örnek: bir kişilik grubuna yüz ekleme-Yüz Tanıma API'si"
titleSuffix: Azure Cognitive Services
description: Bu kılavuzda, Azure bilişsel Hizmetler Yüz Tanıma API'si bir PersonGroup nesnesine çok sayıda kişi ve yüz ekleme gösterilmektedir.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 2f8a6272b02aea5948be79ddf72d105c4f72bb33
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744251"
---
# <a name="add-faces-to-a-persongroup"></a>Bir kişilik grubuna yüz ekleme

Bu kılavuzda, bir PersonGroup nesnesine çok sayıda kişi ve yüz ekleme gösterilmektedir. Aynı strateji LargePersonGroup, çok yönlü liste ve Largecelist nesneleri için de geçerlidir. Bu örnek, Azure bilişsel Hizmetler Yüz Tanıma API'si .NET istemci kitaplığı kullanılarak dilinde yazılmıştır C# .

## <a name="step-1-initialization"></a>1\. Adım: Başlatma

Aşağıdaki kod birkaç değişken bildirir ve yüz ekleme isteklerini zamanlamak için bir yardımcı işlevi uygular:

- `PersonCount`, toplam kişi sayısıdır.
- `CallLimitPerSecond`, abonelik katmanına göre saniyedeki maksimum çağrı sayısıdır.
- `_timeStampQueue`, istek zaman damgalarını kaydetmek için kullanılan bir Kuyruktur.
- `await WaitCallLimitPerSecondAsync()` sonraki isteği göndermek için geçerli olana kadar bekler.

```csharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>2\. Adım: API çağrısını yetkilendirme

Bir istemci kitaplığı kullandığınızda, abonelik anahtarınızı **Faceclient** sınıfının oluşturucusuna geçirmeniz gerekir. Örneğin:

```csharp
private readonly IFaceClient faceClient = new FaceClient(
    new ApiKeyServiceClientCredentials("<SubscriptionKey>"),
    new System.Net.Http.DelegatingHandler[] { });
```

Abonelik anahtarını almak için Azure portal Azure Marketi 'ne gidin. Daha fazla bilgi için bkz. [abonelikler](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>3\. Adım: PersonGroup oluşturma

Kişileri kaydetmek için "MyPersonGroup" adlı bir PersonGroup oluşturulur.
Genel doğrulama sağlamak için istek süresi, `_timeStampQueue` hedefinde kuyruğa alınır.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceClient.LargePersonGroup.CreateAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>4\. Adım: PersonGroup için kişileri oluşturma

Kişiler eşzamanlı olarak oluşturulur ve çağrı sınırını aşmamak için `await WaitCallLimitPerSecondAsync()` de uygulanır.

```csharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceClient.PersonGroupPerson.CreateAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>5\. Adım: Kişilere yüz ekleme

Farklı kişilere eklenen yüzler eşzamanlı olarak işlenir. Belirli bir kişiye eklenen yüzler sırayla işlenir.
Yeniden `await WaitCallLimitPerSecondAsync()`, istek sıklığının sınırlama kapsamında olduğundan emin olmak için çağrılır.

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Özet

Bu kılavuzda, çok sayıda kişi ve yüz içeren bir PersonGroup oluşturma işlemini öğrendiniz. Bazı anımsatıcılar:

- Bu strateji Ayrıca çok yönlü listeler ve LargePersonGroups için de geçerlidir.
- Farklı çok yönlü listelere veya LargePersonGroups içindeki kişilere yüz ekleme veya silme, eşzamanlı olarak işlenir.
- Bir LargePersonGroup içindeki belirli bir çok yönlü liste veya kişiye yüz ekleme veya silme işlemi sırayla yapılır.
- Kolaylık olması için, bu kılavuzda olası bir özel durum nasıl ele alınacağını göz ardı edilir. Daha fazla sağlamlık geliştirmek istiyorsanız, doğru yeniden deneme ilkesini uygulayın.

Aşağıdaki özellikler açıklanmıştı ve gösterilmiştir:

- [Persongroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) API ' i kullanarak kişilik grupları oluşturun.
- Kişiler [-oluşturma](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c) API 'Sini kullanarak kişi oluşturun.
- [Persongroup kişi-yüz tanıma](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API 'sini kullanarak kişilere yüz ekleyin.

## <a name="related-topics"></a>İlgili konular

- [Görüntüdeki yüzeyleri tanımla](HowtoIdentifyFacesinImage.md)
- [Görüntüdeki yüzeyleri algılama](HowtoDetectFacesinImage.md)
- [Büyük ölçekli özelliği kullanın](how-to-use-large-scale.md)
