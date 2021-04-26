---
title: MongoDB için Azure Cosmos DB API 'sindeki verileri yönetmek için MongoDB uzantı komutları
description: Bu makalede MongoDB için Azure Cosmos DB API 'sinde depolanan verileri yönetmek üzere MongoDB uzantı komutlarının nasıl kullanılacağı açıklanır.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: chrande
ms.custom: devx-track-js
ms.openlocfilehash: deba6696eb71287902fa3970ed2d83d0b09ac08d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658495"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API 'sinde depolanan verileri yönetmek için MongoDB uzantı komutlarını kullanma 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Aşağıdaki belge, MongoDB için Azure Cosmos DB API 'sine özgü özel eylem komutlarını içerir. Bu komutlar [Azure Cosmos DB kapasite modeline](account-databases-containers-items.md)özgü veritabanı kaynaklarını oluşturmak ve almak için kullanılabilir.

MongoDB için Azure Cosmos DB API 'sini kullanarak, MongoDB uygulamanızda yatırımlarınızı korurken küresel dağıtım, otomatik parçalama, yüksek kullanılabilirlik, gecikme süreleri, otomatik, bekleyen şifreleme, yedeklemeler ve çok daha fazlası gibi avantajların Cosmos DB avantajlarından yararlanabilirsiniz. Açık kaynaklı [MongoDB istemci sürücülerinden](https://docs.mongodb.org/ecosystem/drivers)herhangi birini kullanarak MongoDB için Azure Cosmos DB API 'si ile iletişim kurabilirsiniz. Azure Cosmos DB MongoDB API 'SI, mevcut istemci sürücülerinin [MongoDB kablo protokolüne](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)bağlanarak kullanımını sağlar.

## <a name="mongodb-protocol-support"></a>MongoDB protokol desteği

MongoDB için Azure Cosmos DB API 'SI, MongoDB sunucu sürümü 4,0, 3,6 ve 3,2 ile uyumludur. Daha fazla bilgi için bkz. [4,0](mongodb-feature-support-40.md), [3,6](mongodb-feature-support-36.md)ve [3,2](mongodb-feature-support.md) makalelerinde desteklenen özellikler ve söz dizimi. 

Aşağıdaki uzantı komutları, veritabanı istekleri aracılığıyla Azure Cosmos DB özel kaynakları oluşturma ve değiştirme olanağı sağlar:

* [Veritabanı oluştur](#create-database)
* [Veritabanını güncelleştir](#update-database)
* [Veritabanını al](#get-database)
* [Koleksiyon Oluştur](#create-collection)
* [Koleksiyonu Güncelleştir](#update-collection)
* [Koleksiyonu al](#get-collection)

## <a name="create-database"></a><a id="create-database"></a> Veritabanı oluştur

Veritabanı uzantısı Oluştur komutu yeni bir MongoDB veritabanı oluşturur. Veritabanı adı, komut tarafından ayarlanan veritabanı bağlamından kullanılabilir `use database` . Aşağıdaki tabloda komut içindeki parametreler açıklanmaktadır:

|**Alan**|**Tür** |**Açıklama** |
|---------|---------|---------|
| `customAction`   |  `string`  |   Özel komutun adı, "CreateDatabase" olmalıdır.      |
| `offerThroughput` | `int`  | Veritabanında ayarladığınız üretilen iş hacmi. Bu parametre isteğe bağlıdır. |
| `autoScaleSettings` | `Object` | [Otomatik ölçeklendirme modu](provision-throughput-autoscale.md)için gereklidir. Bu nesne, otomatik ölçeklendirme kapasitesi moduyla ilişkili ayarları içerir. `maxThroughput`Koleksiyonun dinamik olarak artırılabileceği en yüksek Istek birimi miktarını açıklayan değeri ayarlayabilirsiniz. |

### <a name="output"></a>Çıktı

Komut başarılı olursa, şu yanıtı döndürür:

```javascript
{ "ok" : 1 }
```

Çıktıda parametreler için özel komutun [varsayılan çıktısına](#default-output) bakın.

### <a name="examples"></a>Örnekler

#### <a name="create-a-database"></a>Veritabanı oluşturma

Tüm varsayılan değerleri kullanan adlı bir veritabanı oluşturmak için `"test"` aşağıdaki komutu kullanın:

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

Bu komut veritabanı düzeyinde üretilen iş olmadan bir veritabanı oluşturur. Bu, bu veritabanı içindeki koleksiyonların, kullanmanız gereken aktarım hızı miktarını belirtmesi gerektiği anlamına gelir.

#### <a name="create-a-database-with-throughput"></a>Aktarım hızı ile veritabanı oluşturma

Adlı bir veritabanı oluşturmak `"test"` ve 1000 ru 'nin [veritabanı düzeyinde](set-throughput.md#set-throughput-on-a-database) sağlanan verimini belirtmek için aşağıdaki komutu kullanın:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

Bu, bir veritabanı oluşturur ve buna bir aktarım hızı ayarlar. Koleksiyonlar [belirli bir üretilen iş düzeyiyle](set-throughput.md#set-throughput-on-a-database-and-a-container)oluşturulmamışsa, bu veritabanı içindeki tüm koleksiyonlar küme aktarım hızını paylaşır.

#### <a name="create-a-database-with-autoscale-throughput"></a>Otomatik ölçeklendirme işleme ile veritabanı oluşturma

Adlı bir veritabanı oluşturmak `"test"` ve [veritabanı DÜZEYINDE](set-throughput.md#set-throughput-on-a-database)20.000 ru/sn 'nin en yüksek bir otomatik ölçeklendirme aktarım hızını belirtmek için aşağıdaki komutu kullanın:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a> Veritabanını güncelleştir

Veritabanı uzantısını Güncelleştir komutu, belirtilen veritabanıyla ilişkili özellikleri güncelleştirir. Veritabanınızı sağlanan aktarım hızına otomatik ölçeklendirme ve tam tersi olarak değiştirme yalnızca Azure portalında desteklenir. Aşağıdaki tabloda komut içindeki parametreler açıklanmaktadır:

|**Alan**|**Tür** |**Açıklama** |
|---------|---------|---------|
| `customAction`    |    `string`     |   Özel komutun adı. "UpdateDatabase" olmalıdır.      |
|  `offerThroughput`   |  `int`       |     Veritabanı [veritabanı düzeyinde aktarım hızı](set-throughput.md#set-throughput-on-a-database) kullanıyorsa veritabanında ayarlamak istediğiniz yeni sağlanan aktarım hızı  |
| `autoScaleSettings` | `Object` | [Otomatik ölçeklendirme modu](provision-throughput-autoscale.md)için gereklidir. Bu nesne, otomatik ölçeklendirme kapasitesi moduyla ilişkili ayarları içerir. `maxThroughput`Veritabanının dinamik olarak artırılabileceği en yüksek Istek birimi miktarını açıklayan değeri ayarlayabilirsiniz. |

Bu komut, oturum bağlamında belirtilen veritabanını kullanır. Bu, komutunda kullandığınız veritabanıdır `use <database>` . Şu anda, veritabanı adı bu komut kullanılarak değiştirilemez.

### <a name="output"></a>Çıktı

Komut başarılı olursa, şu yanıtı döndürür:

```javascript
{ "ok" : 1 }
```

Çıktıda parametreler için özel komutun [varsayılan çıktısına](#default-output) bakın.

### <a name="examples"></a>Örnekler

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>Bir veritabanıyla ilişkili sağlanan aktarım hızını güncelleştirme

Adı 1200 ru olan bir veritabanının sağlanan verimini güncelleştirmek için `"test"` aşağıdaki komutu kullanın:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>Bir veritabanıyla ilişkili otomatik ölçeklendirme verimini güncelleştirme

Adı 20.000 ru olan bir veritabanının sağlanan verimini güncelleştirmek `"test"` veya bir [Otomatik ölçeklendirme işleme düzeyine](provision-throughput-autoscale.md)dönüştürmek için aşağıdaki komutu kullanın:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a> Veritabanını al

Veritabanı uzantısını Al komutu, veritabanı nesnesini döndürür. Veritabanı adı, komutun yürütüldüğü veritabanı bağlamından kullanılır.

```javascript
{
  customAction: "GetDatabase"
}
```

Aşağıdaki tabloda komut içindeki parametreler açıklanmaktadır:


|**Alan**|**Tür** |**Açıklama** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Özel komutun adı. "GetDatabase" olmalıdır|
        
### <a name="output"></a>Çıktı

Komut başarılı olursa, yanıt aşağıdaki alanları içeren bir belge içerir:

|**Alan**|**Tür** |**Açıklama** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Yanıtın durumu. 1 = = başarılı. 0 = = hata.      |
| `database`    |    `string`        |   Veritabanının adı.      |
|   `provisionedThroughput`  |    `int`      |    Veritabanı [el ile veritabanı düzeyinde aktarım hızı](set-throughput.md#set-throughput-on-a-database) kullanıyorsa veritabanında ayarlanan üretilen iş hacmi     |
| `autoScaleSettings` | `Object` | Bu nesne, [Otomatik ölçeklendirme modunu](provision-throughput-autoscale.md)kullanıyorsa veritabanıyla ilişkili kapasite parametrelerini içerir. `maxThroughput`Değer, veritabanının dinamik olarak artırılacakları en yüksek Istek birimi miktarını tanımlar. |

Komut başarısız olursa, varsayılan bir özel komut yanıtı döndürülür. Çıktıda parametreler için özel komutun [varsayılan çıktısına](#default-output) bakın.

### <a name="examples"></a>Örnekler

#### <a name="get-the-database"></a>Veritabanını al

Adlı bir veritabanının veritabanı nesnesini almak için `"test"` aşağıdaki komutu kullanın:

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

Veritabanında ilişkili bir işleme yoksa, çıkış şöyle olacaktır:

```javascript
{ "database" : "test", "ok" : 1 }
```

Veritabanında kendisiyle ilişkili [veritabanı düzeyinde bir el ile işleme](set-throughput.md#set-throughput-on-a-database) varsa, çıktıda şu `provisionedThroughput` değerler gösterilir:

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

Veritabanında kendisiyle ilişkili [veritabanı düzeyinde bir otomatik ölçeklendirme üretilen işi](provision-throughput-autoscale.md) varsa, çıktı, `provisionedThroughput` veritabanı IÇIN en düşük ru/sn 'yi ve `autoScaleSettings` `maxThroughput` veritabanı için en fazla ru/sn 'yi açıklayan öğesini de içeren nesnesini gösterir.

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
                "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="create-collection"></a><a id="create-collection"></a> Koleksiyon Oluştur

Koleksiyon uzantısı Oluştur komutu yeni bir MongoDB koleksiyonu oluşturur. Veritabanı adı, komut tarafından ayarlanan veritabanları bağlamında kullanılır `use database` . CreateCollection komutunun biçimi aşağıdaki gibidir:

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  // Replace the line below with "autoScaleSettings: { maxThroughput: (int) }" to use Autoscale instead of Provisioned Throughput. Fill the required Autoscale max throughput setting.
  offerThroughput: (int) // Provisioned Throughput enabled with required throughput amount set
}
```

Aşağıdaki tabloda komut içindeki parametreler açıklanmaktadır:

| **Alan** | **Tür** | **Gerekli** | **Açıklama** |
|---------|---------|---------|---------|
| `customAction` | `string` | Gerekli | Özel komutun adı. "CreateCollection" olmalıdır.|
| `collection` | `string` | Gerekli | Koleksiyonun adı. Özel karakterlere veya boşluklara izin verilmez.|
| `offerThroughput` | `int` | İsteğe Bağlı | Veritabanında ayarlanacak üretilen iş hacmi. Bu parametre sağlanmazsa, varsayılan olarak en az 400 RU/sn olur. * 10.000 RU/sn 'nin ötesinde üretilen iş miktarını belirtmek için `shardKey` parametresi gereklidir.|
| `shardKey` | `string` | Büyük aktarım hızı olan koleksiyonlar için gereklidir | Parçalı koleksiyon için parça anahtarının yolu. İçinde 10.000 RU/sn 'den fazla ayarlarsanız bu parametre gereklidir `offerThroughput` .  Belirtilmişse, ekli tüm belgeler bu anahtar ve değeri gerektirir. |
| `autoScaleSettings` | `Object` | [Otomatik ölçeklendirme modu](provision-throughput-autoscale.md) için gerekli | Bu nesne, otomatik ölçeklendirme kapasitesi moduyla ilişkili ayarları içerir. `maxThroughput`Koleksiyonun dinamik olarak artırılabileceği en yüksek Istek birimi miktarını açıklayan değeri ayarlayabilirsiniz. |

### <a name="output"></a>Çıktı

Varsayılan özel komut yanıtını döndürür. Çıktıda parametreler için özel komutun [varsayılan çıktısına](#default-output) bakın.

### <a name="examples"></a>Örnekler

#### <a name="create-a-collection-with-the-minimum-configuration"></a>En düşük yapılandırmayla bir koleksiyon oluşturma

Adı ve varsayılan değerleri olan yeni bir koleksiyon oluşturmak için `"testCollection"` aşağıdaki komutu kullanın: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

Bu, 400RU/s ile yeni bir sabit, parçalama, koleksiyon ve `_id` otomatik olarak oluşturulan alanın bir diziniyle sonuçlanır. Bu tür bir yapılandırma, işlev aracılığıyla yeni koleksiyonlar oluştururken de geçerlidir `insert()` . Örnek: 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>Parçalı olmayan bir koleksiyon oluşturma

1000 ru 'nın adı ve sağlanmış iş hızına sahip, parçalı olmayan bir koleksiyon oluşturmak için `"testCollection"` aşağıdaki komutu kullanın: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

Parça anahtarı belirtmeye gerek kalmadan, 10.000 RU/sn 'ye kadar bir koleksiyon oluşturabilirsiniz `offerThroughput` . Daha büyük aktarım hızına sahip koleksiyonlar için sonraki bölüme göz atın.

#### <a name="create-a-sharded-collection"></a>Parçalı koleksiyon oluşturma

`"testCollection"`11.000 ru 'nın adı ve sağlanan aktarım hızı ve `shardkey` "a. b" özelliği ile parçalı bir koleksiyon oluşturmak için aşağıdaki komutu kullanın:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

Bu komut artık `shardKey` içinde belirtilen 10.000 ru/sn 'den fazla olan parametresi gerektirir `offerThroughput` .

#### <a name="create-an-unsharded-autoscale-collection"></a>Parçalı olmayan bir otomatik ölçeklendirme koleksiyonu oluşturma

`'testCollection'` [Otomatik ölçeklendirme üretilen iş KAPASITESINI](provision-throughput-autoscale.md) 4.000 ru/sn olarak ayarlanan adlı, parçalı olmayan bir koleksiyon oluşturmak için aşağıdaki komutu kullanın:

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

Değer için, parça `autoScaleSettings.maxThroughput` anahtarı olmadan 4.000 ru/s ile 10.000 ru/s arasında bir Aralık belirtebilirsiniz. Daha yüksek otomatik ölçeklendirme verimlilik için parametresini belirtmeniz gerekir `shardKey` .

#### <a name="create-a-sharded-autoscale-collection"></a>Parçalı bir otomatik ölçeklendirme koleksiyonu oluşturma

Adında bir parça anahtarı ile adlı parçalı bir koleksiyon oluşturmak `'testCollection'` `'a.b'` ve [Otomatik ölçeklendirme üretilen Iş kapasitesini](provision-throughput-autoscale.md) 20.000 ru/sn olarak ayarlamak için aşağıdaki komutu kullanın:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a> Koleksiyonu Güncelleştir

Koleksiyon uzantısını Güncelleştir komutu, belirtilen koleksiyonla ilişkili özellikleri güncelleştirir. Koleksiyonunuzu sağlanan aktarım hızına göre otomatik ölçeklendirme ve tam tersi olarak değiştirme yalnızca Azure portalında desteklenir.

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  // Replace the line below with "autoScaleSettings: { maxThroughput: (int) }" if using Autoscale instead of Provisioned Throughput. Fill the required Autoscale max throughput setting. Changing between Autoscale and Provisioned throughput is only supported in the Azure Portal.
  offerThroughput: (int) // Provisioned Throughput enabled with required throughput amount set
}
```

Aşağıdaki tabloda komut içindeki parametreler açıklanmaktadır:

|**Alan**|**Tür** |**Açıklama** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Özel komutun adı. "UpdateCollection" olması gerekir.      |
|  `collection`   |   `string`      |   Koleksiyonun adı.       |
| `offerThroughput` | `int` |   Koleksiyonda ayarlanacak sağlanan aktarım hızı.|
| `autoScaleSettings` | `Object` | [Otomatik ölçeklendirme modu](provision-throughput-autoscale.md)için gereklidir. Bu nesne, otomatik ölçeklendirme kapasitesi moduyla ilişkili ayarları içerir. `maxThroughput`Değer, koleksiyonun dinamik olarak artırılacakları en yüksek Istek birimi sayısını açıklar. |

## <a name="output"></a>Çıktı

Varsayılan özel komut yanıtını döndürür. Çıktıda parametreler için özel komutun [varsayılan çıktısına](#default-output) bakın.

### <a name="examples"></a>Örnekler

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>Bir koleksiyonla ilişkili sağlanan aktarım hızını güncelleştirme

Adı 1200 ru olan bir koleksiyonun sağlanan verimini güncelleştirmek için `"testCollection"` aşağıdaki komutu kullanın:

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a> Koleksiyonu al

Koleksiyonu al özel komutu koleksiyon nesnesini döndürür.

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

Aşağıdaki tabloda komut içindeki parametreler açıklanmaktadır:


|**Alan**|**Tür** |**Açıklama** |
|---------|---------|---------|
| `customAction`    |   `string`      |   Özel komutun adı. "GetCollection" olmalıdır.      |
| `collection`    |    `string`     |    Koleksiyonun adı.     |

### <a name="output"></a>Çıktı

Komut başarılı olursa, yanıt aşağıdaki alanları içeren bir belge içerir


|**Alan**|**Tür** |**Açıklama** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Yanıtın durumu. 1 = = başarılı. 0 = = hata.      |
| `database`    |    `string`     |   Veritabanının adı.      |
| `collection`    |    `string`     |    Koleksiyonun adı.     |
|  `shardKeyDefinition`   |   `document`      |  Parçalı anahtar olarak kullanılan dizin belirtim belgesi. Bu, isteğe bağlı bir yanıt parametresidir.       |
|  `provisionedThroughput`   |   `int`      |    Koleksiyonda ayarlanacak sağlanan aktarım hızı. Bu, isteğe bağlı bir yanıt parametresidir.     |
| `autoScaleSettings` | `Object` | Bu nesne, [Otomatik ölçeklendirme modunu](provision-throughput-autoscale.md)kullanıyorsa veritabanıyla ilişkili kapasite parametrelerini içerir. `maxThroughput`Değer, koleksiyonun dinamik olarak artırılacakları en yüksek Istek birimi sayısını açıklar. |

Komut başarısız olursa, varsayılan bir özel komut yanıtı döndürülür. Çıktıda parametreler için özel komutun [varsayılan çıktısına](#default-output) bakın.

### <a name="examples"></a>Örnekler

#### <a name="get-the-collection"></a>Koleksiyonu al

Adlı bir koleksiyon için koleksiyon nesnesini almak için `"testCollection"` aşağıdaki komutu kullanın:

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

Koleksiyonda ilişkili bir üretilen iş kapasitesi varsa, bu değer dahil olur `provisionedThroughput` ve çıktı şöyle olacaktır:

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

Koleksiyonda ilişkili bir otomatik ölçeklendirme işleme varsa, bu `autoScaleSettings` `maxThroughput` parametre, koleksiyonun dinamik olarak arttırabileceği en yüksek aktarım hızını tanımlayan parametresine sahip olur. Ayrıca, `provisionedThroughput` koleksiyonda istek yoksa, bu koleksiyonun azaltılacağı en düşük aktarım hızını tanımlayan değeri de içerir: 

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 1000,
        "autoScaleSettings" : {
            "maxThroughput" : 10000
        },
        "ok" : 1
}
```

Koleksiyon [veritabanı düzeyinde aktarım hızını](set-throughput.md#set-throughput-on-a-database), otomatik ölçeklendirme modunda veya el ile paylaşıyorsanız, çıkış şöyle olur:

```javascript
{ "database" : "test", "collection" : "testCollection", "ok" : 1 }
```

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
            "maxThroughput" : 20000
        },
        "ok" : 1
}
```


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a> Özel bir komutun varsayılan çıkışı

Belirtilmemişse, özel bir yanıt aşağıdaki alanlara sahip bir belge içerir:

|**Alan**|**Tür** |**Açıklama** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Yanıtın durumu. 1 = = başarılı. 0 = = hata.      |
| `code`    |   `int`      |   Yalnızca komut başarısız olduğunda (örneğin, ok = = 0) döndürülür. MongoDB hata kodunu içerir. Bu, isteğe bağlı bir yanıt parametresidir.      |
|  `errMsg`   |  `string`      |    Yalnızca komut başarısız olduğunda (örneğin, ok = = 0) döndürülür. Kullanıcı dostu bir hata iletisi içerir. Bu, isteğe bağlı bir yanıt parametresidir.      |

Örnek:

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>Sonraki adımlar

Sonra aşağıdaki Azure Cosmos DB kavramları öğrenmek için devam edebilirsiniz: 

* [Azure Cosmos DB’de dizin oluşturma](../cosmos-db/index-policy.md)
* [Azure Cosmos DB verileri yaşam süresi ile otomatik olarak sona erer](../cosmos-db/time-to-live.md)
