---
title: Bölümlenmemiş Azure Cosmos kapsayıcılarını bölümlenmiş kapsayıcılara geçirme
description: Var olan tüm bölümlenmemiş kapsayıcıları bölümlenmiş kapsayıcılara geçirmeyi öğrenin.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/25/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: edb6114406922d55c439ae7426a2be933bba4aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334098"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Bölümlendirilmemiş kapsayıcıları bölümlenmiş kapsayıcılara geçirme
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB, bölüm anahtarı olmayan kapsayıcılar oluşturmayı destekler. Şu anda, 2. x sürümüne eşit veya daha küçük bir sürüme sahip olan Azure CLı ve Azure Cosmos DB SDK 'Ları (.net, Java, NodeJs) kullanarak bölümlenmemiş kapsayıcılar oluşturabilirsiniz. Azure portal kullanarak bölümlenmemiş kapsayıcılar oluşturamazsınız. Ancak, bu tür bölümlenmemiş kapsayıcılar elastik değildir ve 10.000 RU/sn aktarım hızı sınırının sabit depolama kapasitesine sahiptir.

Bölümlenmemiş kapsayıcılar eskide kaldığından depolama alanını ve aktarım hızını ölçeklendirebilmek için mevcut bölümlenmemiş kapsayıcılarınızı bölümlenmiş kapsayıcılara geçirmeniz gerekir. Azure Cosmos DB, bölümlenmemiş kapsayıcılarınızı bölümlenmiş kapsayıcılara geçirmek için kullanabileceğiniz sistem tanımlı bir mekanizma sunar. Bu belgede mevcut bölümlenmemiş kapsayıcıların nasıl otomatik olarak bölümlenmiş kapsayıcılara geçirileceği anlatılmıştır. Otomatik geçiş özelliğinden faydalanabilmek için tüm dillerdeki SDK'ların V3 sürümünü kullanıyor olmanız gerekir.

> [!NOTE]
> Şu anda, bu belgede açıklanan adımları kullanarak MongoDB ve Gremlin API hesaplarını Azure Cosmos DB geçiremezsiniz.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Sistem tanımlı bölüm anahtarını kullanarak kapsayıcıyı geçirme

Azure Cosmos DB geçişi desteklemek için, `/_partitionkey` bölüm anahtarı olmayan tüm kapsayıcılarda adlı sistem tanımlı bir bölüm anahtarı sağlar. Kapsayıcılar geçirildikten sonra bölüm anahtarı tanımını değiştiremezsiniz. Örneğin, bölümlenmiş bir kapsayıcıya geçirilmiş bir kapsayıcının tanımı aşağıdaki gibi olacaktır:

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

Kapsayıcı geçirildikten sonra, `_partitionKey` özelliği belgenin diğer özellikleriyle birlikte doldurarak belgeler oluşturabilirsiniz. `_partitionKey`Özelliği, belgelerinizin bölüm anahtarını temsil eder.

Doğru bölüm anahtarının seçilmesi, sağlanan aktarım hızını en iyi şekilde kullanmak için önemlidir. Daha fazla bilgi için bkz. [bölüm anahtarını seçme](partitioning-overview.md) makalesi.

> [!NOTE]
> Yalnızca tüm dillerde SDK 'ların en son/v3 sürümünü kullanıyorsanız sistem tanımlı bölüm anahtarından yararlanabilirsiniz.

Aşağıdaki örnek, sistem tanımlı bölüm anahtarı ile bir belge oluşturmak için örnek bir kod gösterir ve belgeyi okur:

**Belgenin JSON temsili**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

Tüm örnek için bkz. [.NET örnekleri][1] GitHub deposu.
                      
## <a name="migrate-the-documents"></a>Belgeleri geçirme

Kapsayıcı tanımı bir bölüm anahtarı özelliği ile geliştirirken, kapsayıcıdaki belgeler otomatik olarak geçirilmez. Bu, sistem bölümü anahtar özelliği `/_partitionKey` yolunun varolan belgelere otomatik olarak eklenmeyeceği anlamına gelir. Bir bölüm anahtarı olmadan oluşturulmuş belgeleri okuyarak ve bunları belgelerdeki özelliği ile yeniden yazarak, varolan belgeleri yeniden bölümlemeniz gerekir `_partitionKey` .

## <a name="access-documents-that-dont-have-a-partition-key"></a>Bölüm anahtarı olmayan belgelere erişin

Uygulamalar, "PartitionKey. None" adlı özel sistem özelliğini kullanarak bölüm anahtarı olmayan mevcut belgelere erişebilir, bu, geçirilmeyen belgelerin değeridir. Tüm CRUD ve sorgu işlemlerinde bu özelliği kullanabilirsiniz. Aşağıdaki örnek, NonePartitionKey öğesinden tek bir belgeyi okumak için bir örnek gösterir. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Belgeleri yeniden bölümleme hakkında tüm örnek için bkz. [.net Samples][1] GitHub deposu. 

## <a name="compatibility-with-sdks"></a>SDK 'lar ile uyumluluk

V2. x. x ve v1. x. x gibi Azure Cosmos DB SDK 'ların daha eski sürümleri sistem tarafından tanımlanan bölüm anahtarı özelliğini desteklemez. Bu nedenle, eski bir SDK 'dan kapsayıcı tanımını okuduğunuzda, hiçbir bölüm anahtarı tanımı içermez ve bu kapsayıcılar tamamen daha önce olduğu gibi davranır. SDK 'ların eski sürümüyle oluşturulan uygulamalar hiçbir değişiklik yapılmadan bölümlenmemiş olarak çalışmaya devam eder. 

Geçirilen bir kapsayıcı SDK 'nın en son/v3 sürümü tarafından tüketilediyse ve sistem tanımlı bölüm anahtarını yeni belgeler içinde doldurmaya başlatırsanız, artık daha eski SDK 'lardan bu belgelere erişemezsiniz (okuma, güncelleştirme, silme, sorgulama).

## <a name="known-issues"></a>Bilinen sorunlar

**V3 SDK kullanılarak bölüm anahtarı olmadan eklenen öğelerin sayısını sorgulamak daha yüksek aktarım hızı tüketimine sahip olabilir**

V2 SDK kullanılarak eklenen öğeler için v3 SDK veya parametresi ile v3 SDK kullanılarak eklenen öğeler için sorgulama yaparsanız `PartitionKey.None` , Count sorgusu, `PartitionKey.None` parametre feedolar içinde sağlandıysa daha fazla ru/s tüketebilir. `PartitionKey.None`Bölüm anahtarı ile başka hiçbir öğe eklenmezseniz parametresini sağlamamız önerilir.

Bölüm anahtarı için farklı değerlerle yeni öğeler eklenirse, içinde uygun anahtarı geçirerek bu tür öğe sayılarını sorgulamak `FeedOptions` herhangi bir sorun olmayacaktır. Bölüm anahtarı ile yeni belgeler eklendikten sonra, bölüm anahtarı değeri olmadan yalnızca belge sayısını sorgulamanızı istiyorsanız, bu sorgu, normal bölümlenmiş koleksiyonlara benzer şekilde daha yüksek RU/s öğesine neden olabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB'de bölümleme](partitioning-overview.md)
* [Azure Cosmos DB'de İstek birimleri](request-units.md)
* [Kapsayıcı ve veritabanlarına aktarım hızı sağlama](set-throughput.md)
* [Azure Cosmos hesabıyla çalışma](./account-databases-containers-items.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration