---
title: Yüz verilerinizi abonelikler arasında geçirme-yüz
titleSuffix: Azure Cognitive Services
description: Bu kılavuzda, depolanan yüz verilerinizi bir yüz aboneliğinden diğerine nasıl geçirebileceğiniz gösterilmektedir.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: nitinme
ms.custom: devx-track-csharp
ms.openlocfilehash: ea2a127ffbf966b024322ab5a908466dec1cc8a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935643"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Yüz verilerinizi farklı bir yüz aboneliğine geçirin

Bu kılavuzda, farklı bir Azure bilişsel hizmetler aboneliğine sahip kayıtlı bir PersonGroup nesnesi gibi yüz verilerinin, yüzlü bir Azure bilişsel hizmet aboneliğine nasıl taşınacağı gösterilmektedir. Verileri taşımak için anlık görüntü özelliğini kullanın. Bu şekilde, işlemlerinizi taşırken veya genişlettiğinizde bir PersonGroup veya çok yönlü liste nesnesi oluşturup eğmenize gerek kalmadan kaçınabilirsiniz. Örneğin, belki de ücretsiz bir abonelikle bir PersonGroup nesnesi oluşturdunuz ve şimdi bunu ücretli aboneliğinize geçirmek istiyorsunuz. Ya da büyük bir kurumsal işlem için farklı bölgelerdeki abonelikler arasında yüz verileri eşitlemeniz gerekebilir.

Aynı geçiş stratejisi LargePersonGroup ve Largecelist nesneleri için de geçerlidir. Bu kılavuzdaki kavramlarla ilgili bilgi sahibi değilseniz, [yüz tanıma kavramları](../concepts/face-recognition.md) kılavuzundaki tanımları bölümüne bakın. Bu kılavuzda, C# ile yüz .NET istemci kitaplığı kullanılmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki öğeler gereklidir:

- Biri mevcut verileri ve diğeri geçirilecek iki yüz abonelik anahtarı. Yüz hizmetine abone olmak ve anahtarınızı almak için bilişsel [Hizmetler hesabı oluşturma](../../cognitive-services-apis-create-account.md)' daki yönergeleri izleyin.
- Hedef aboneliğe karşılık gelen yüz abonelik KIMLIĞI dizesi. Bunu bulmak için Azure portal **genel bakış** ' ı seçin. 
- [Visual Studio 2015 veya 2017](https://www.visualstudio.com/downloads/)'nin herhangi bir sürümü.

## <a name="create-the-visual-studio-project"></a>Visual Studio projesini oluşturma

Bu kılavuz, yüz veri geçişini çalıştırmak için basit bir konsol uygulaması kullanır. Tam bir uygulama için GitHub 'daki [yüz anlık görüntü örneğine](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) bakın.

1. Visual Studio 'da yeni bir konsol uygulaması .NET Framework projesi oluşturun. Bunu çok **yönlü Apisnapshotsample** olarak adlandırın.
1. Gereken NuGet paketlerini alın. Çözüm Gezgini projenize sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin. **Tarayıcı** sekmesini seçin ve **ön sürümü dahil et**' i seçin. Aşağıdaki paketi bulup yükler:
    - [Microsoft. Azure. Biliveservices. Vision. Face 2.3.0-Önizleme](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Yüz istemcileri oluşturma

*Program. cs*' deki **ana** yöntemde, kaynak ve hedef abonelikleriniz için iki [faceclient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) örneği oluşturun. Bu örnek, hedef olarak Doğu Asya bölgesindeki bir yüz aboneliğini kaynak ve Batı ABD bir abonelik kullanır. Bu örnek, verilerin bir Azure bölgesinden diğerine nasıl geçirileceğini gösterir. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Kaynak ve hedef abonelikleriniz için abonelik anahtarı değerlerini ve uç nokta URL 'Lerini girin.


## <a name="prepare-a-persongroup-for-migration"></a>Bir kişilik grubunu geçiş için hazırlama

Hedef aboneliğe geçirmek için kaynak aboneliğinizdeki PersonGroup 'un KIMLIĞINE sahip olmanız gerekir. [Persongroupoperationsextensions. ListAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync) yöntemini kullanarak, persongroup nesnelerinizin bir listesini alın. Ardından, [Persongroup. Persongroupıd](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) özelliğini alın. Bu işlem, sahip olduğunuz PersonGroup nesnelerine göre farklılık yapar. Bu kılavuzda, kaynak kişinin KIMLIĞI ' nde depolanır `personGroupId` .

> [!NOTE]
> [Örnek kod](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) , geçirilecek yeni bir persongroup oluşturur ve bunları ister. Çoğu durumda, kullanmak için zaten bir PersonGroup grubunuz olmalıdır.

## <a name="take-a-snapshot-of-a-persongroup"></a>Bir PersonGroup 'un anlık görüntüsünü alın

Anlık görüntü, belirli yüz veri türleri için geçici bir uzak depodır. Verileri bir abonelikten diğerine kopyalamak için Pano türü olarak çalışır. İlk olarak, kaynak abonelikteki verilerin bir anlık görüntüsünü alın. Ardından, hedef abonelikte yeni bir veri nesnesine uygularsınız.

Kaynak aboneliğin FaceClient örneğini kullanarak PersonGroup 'un anlık görüntüsünü alın. PersonID ve hedef aboneliğin KIMLIĞIYLE birlikte bulunan [Takeasync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync) kullanın. Birden çok hedef aboneliğiniz varsa, bunları üçüncü parametreye dizi girdileri olarak ekleyin.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Anlık görüntü alma ve uygulama işlemi, kaynak veya hedef kişiler veya çok yönlü listeler için düzenli olarak yapılan çağrıları kesintiye uğratmaz. Örneğin, çok [yönlü liste yönetimi çağrıları](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations) veya [Persongroup eğitme](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations) çağrısı gibi kaynak nesneyi değiştiren eşzamanlı çağrılar yapmayın. Anlık görüntü işlemi, bu işlemlerden önce veya sonra çalışabilir ya da hatalarla karşılaşabilirler.

## <a name="retrieve-the-snapshot-id"></a>Anlık görüntü KIMLIĞINI alma

Anlık görüntü almak için kullanılan yöntem zaman uyumsuzdur, bu yüzden tamamlanmasını beklemeniz gerekir. Anlık görüntü işlemleri iptal edilemez. Bu kodda, `WaitForOperation` yöntemi zaman uyumsuz çağrıyı izler. Her 100 ms durumunu denetler. İşlem bittikten sonra, alanı ayrıştırarak bir işlem KIMLIĞI alın `OperationLocation` . 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Tipik bir `OperationLocation` değer şöyle görünür:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

`WaitForOperation`Yardımcı yöntemi şöyledir:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

İşlem durumu görüntülendikten sonra `Succeeded` , `ResourceLocation` döndürülen OperationStatus örneğinin alanını ayrıştırarak anlık görüntü kimliğini alın.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Tipik bir `resourceLocation` değer şöyle görünür:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Hedef aboneliğe anlık görüntü uygulama

Ardından, rastgele oluşturulmuş bir KIMLIK kullanarak hedef abonelikte yeni PersonGroup grubunu oluşturun. Ardından, anlık görüntüyü bu kişilik grubuna uygulamak için hedef aboneliğin FaceClient örneğini kullanın. Anlık görüntü KIMLIĞINI ve yeni PersonGroup KIMLIĞINI geçirin.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Anlık görüntü nesnesi yalnızca 48 saat için geçerlidir. Yalnızca kısa bir süre sonra veri geçişi için kullanmak istiyorsanız bir anlık görüntü alın.

Anlık görüntü uygulama isteği başka bir işlem KIMLIĞI döndürüyor. Bu KIMLIĞI almak için `OperationLocation` döndürülen applySnapshotResult örneğinin alanını ayrıştırın. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Anlık görüntü uygulama işlemi de zaman uyumsuzdur, bu nedenle daha sonra `WaitForOperation` işlemin bitmesini beklemek için kullanın.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Veri geçişini test etme

Anlık görüntüyü uyguladıktan sonra, hedef abonelikteki yeni bir kişi grubu orijinal yüz verileriyle doldurulur. Varsayılan olarak, eğitim sonuçları da kopyalanır. Yeni PersonGroup, yeniden eğitimlere gerek kalmadan yüz tanıma çağrılarına hazırlanmaya yöneliktir.

Veri geçişini test etmek için aşağıdaki işlemleri çalıştırın ve yazdırdıkları sonuçları konsola karşılaştırın:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the PersonGroup before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Aşağıdaki yardımcı yöntemleri kullanın:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("PersonGroup:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

Artık hedef abonelikte yeni PersonGroup grubunu kullanabilirsiniz. 

Hedef kişilik grubunu daha sonra yeniden güncelleştirmek için anlık görüntüyü almak üzere yeni bir kişilik grubu oluşturun. Bunu yapmak için, bu kılavuzdaki adımları izleyin. Tek bir PersonGroup nesnesi yalnızca bir kez bir anlık görüntü uygulanmasını sağlayabilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yüz verilerini geçirmeyi tamamladıktan sonra, anlık görüntü nesnesini el ile silin.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Sonraki adımlar

Ardından, ilgili API başvuru belgelerine bakın, anlık görüntü özelliğini kullanan örnek bir uygulamayı keşfedebilir veya burada bahsedilen diğer API işlemlerini kullanmaya başlamak için nasıl yapılır kılavuzunu izleyin:

- [Anlık görüntü başvuru belgeleri (.NET SDK)](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations)
- [Yüz anlık görüntü örneği](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Yüz ekleme](how-to-add-faces.md)
- [Bir görüntüdeki yüzleri algılama](HowtoDetectFacesinImage.md)
