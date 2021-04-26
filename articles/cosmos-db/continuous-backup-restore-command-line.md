---
title: Azure Cosmos DB ' de sürekli yedeklemeyi ve zaman içinde geri yüklemeyi yapılandırmak için Azure CLı 'yi kullanın.
description: Azure CLı kullanarak sürekli yedekleme ve verileri geri yükleme ile bir hesap sağlamayı öğrenin.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9ea71dae746ac423e7b17b6235b4d5cd3e143cd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377338"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-cli"></a>Sürekli yedekleme ve zaman noktası geri yükleme (Önizleme) yapılandırma ve yönetme-Azure CLı kullanma
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB için zaman noktası geri yükleme özelliği (sürekli yedekleme modu) Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB zaman içinde geri yükleme özelliği (Önizleme), bir kapsayıcı içindeki yanlışlıkla yapılan bir değişikliği kurtarmanıza, silinen bir hesabı, veritabanını veya kapsayıcıyı geri yüklemeyi veya herhangi bir bölgeye geri yüklemeyi (yedeklemelerin varolduğu) sağlar. Sürekli yedekleme modu, son 30 gün içinde herhangi bir zaman noktasına geri yükleme yapmanıza olanak sağlar.

Bu makalede, Azure CLı kullanarak sürekli yedekleme ve verileri geri yükleme ile bir hesabın nasıl sağlanacağı açıklanır.

## <a name="install-azure-cli"></a><a id="install"></a>Azure CLı 'yı yükler

1. Azure CLı 'nın en son sürümünü yükler

   * [Azure CLI](/cli/azure/install-azure-cli) 'nın en son sürümünü 2.17.1 sürümünden daha yüksek bir sürüme yükler.
   * CLı 'yi zaten yüklediyseniz, `az upgrade` en son sürüme güncelleştirmek için komutunu çalıştırın. Bu komut yalnızca 2,11 'den yüksek CLı sürümü ile çalışır. Önceki bir sürümünüz varsa, en son sürümü yüklemek için yukarıdaki bağlantıyı kullanın.

1. `cosmosdb-preview`CLI uzantısını yükler.

   * Süre içinde geri yükleme komutları uzantı altında kullanılabilir `cosmosdb-preview` .
   * Aşağıdaki komutu çalıştırarak bu uzantıyı yükleyebilirsiniz: `az extension add --name cosmosdb-preview`
   * Aşağıdaki komutu çalıştırarak bu uzantıyı kaldırabilirsiniz: `az extension remove --name cosmosdb-preview`

1. Oturum açın ve aboneliğinizi seçin

   * Komutuyla Azure hesabınızda oturum açın `az login` .
   * Gerekli aboneliği kullan komutunu seçin `az account set -s <subscriptionguid>` .

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Sürekli yedekleme ile bir SQL API hesabı sağlama

Sürekli yedekleme ile bir SQL API hesabı sağlamak için, `--backup-policy-type Continuous` normal sağlama komutuyla birlikte ek bir bağımsız değişken geçirilmelidir. Aşağıdaki komut, `pitracct2` *myrg* kaynak grubu altında *Batı ABD* bölgesinde oluşturulan sürekli yedekleme ilkesiyle birlikte adlandırılan tek bir bölge yazma hesabına örnektir:

```azurecli-interactive

az cosmosdb create \
  --name pitracct2 \
  --resource-group myrg \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-an-azure-cosmos-db-api-for-mongodb-account-with-continuous-backup"></a><a id="provision-mongo-api"></a>Sürekli yedekleme ile MongoDB hesabı için Azure Cosmos DB API sağlama

Aşağıdaki komut, `pitracct3` *myrg* kaynak grubu altında *Batı ABD* bölgeyi oluşturan sürekli yedekleme ilkesiyle adlandırılan tek bir bölge yazma hesabının bir örneğini gösterir:

```azurecli-interactive

az cosmosdb create \
  --name pitracct3 \
  --kind MongoDB \
  --resource-group myrg \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore"></a>CLı ile geri yükleme işlemi tetikleyin

Geri yüklemeyi tetiklemenin en kolay yolu, geri yükleme komutunu hedef hesap, kaynak hesap, konum, kaynak grubu, zaman damgası (UTC) ve isteğe bağlı olarak veritabanı ve kapsayıcı adlarını vererek kullanmaktır. Geri yükleme işleminin tetiklenmesi için bazı örnekler aşağıda verilmiştir:

1. Mevcut bir hesaptan geri yükleyerek yeni bir Azure Cosmos DB hesabı oluşturun.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. Mevcut bir veritabanı hesabından yalnızca seçili veritabanlarını ve kapsayıcıları geri yükleyerek yeni bir Azure Cosmos DB hesabı oluşturun.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=collection1 collection2 \
    --databases-to-restore name=MyDB2 collections=collection3 collection4

   ```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>SQL API için geri yüklenebilen kaynakları listeleme

Aşağıda açıklanan listeleme komutları, çeşitli zaman damgalarına geri yükleme için kullanılabilen kaynakları keşfetmenize yardımcı olur. Ayrıca, geri yüklenebilen hesap, veritabanı ve kapsayıcı kaynaklarında önemli olayların bir akışını da sağlar.

**Geçerli abonelikte geri yüklenmiş tüm hesapları listeleyin**

Geçerli abonelikte geri yüklenebildiğinden tüm hesapları listelemek için aşağıdaki CLı komutunu çalıştırın

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "pitrbb"
```

Yanıt, geri yüklenen ve geri yüklenebilecekleri bölgeleri içeren tüm veritabanı hesaplarını (hem canlı hem de silinmiş) içerir:

```json
{
    "accountName": "pitrbb",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

`CreationTime`Ya da `DeletionTime` hesabı için olduğu gibi, bölge için de bir `CreationTime` veya vardır `DeletionTime` . Bu süreler, bu bölgeye geri yüklenecek doğru bölgeyi ve geçerli bir zaman aralığını seçmenizi sağlar.

**Canlı bir veritabanı hesabındaki tüm veritabanlarının sürümlerini listeleyin**

Veritabanlarının tüm sürümlerinin listelenmesi, veritabanının mevcut zamanının bilinmediği bir senaryoda doğru veritabanını seçmenize olanak sağlar.

Veritabanlarının tüm sürümlerini listelemek için aşağıdaki CLı komutunu çalıştırın. Bu komut yalnızca canlı hesaplarla birlikte kullanılabilir. `instanceId`Ve parametreleri, `location` `name` `location` komutu yanıtı içindeki ve özelliklerinden elde edilir `az cosmosdb restorable-database-account list` . InstanceId özniteliği Ayrıca, geri yüklenmekte olan kaynak veritabanı hesabının bir özelliğidir:

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

Bu komut çıktısı artık bir veritabanının oluşturulup silindiğini gösterir.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

**Canlı bir veritabanı hesabındaki bir veritabanının tüm SQL kapsayıcıları sürümlerini listeleyin**

Tüm SQL kapsayıcıları sürümlerini listelemek için aşağıdaki komutu kullanın. Bu komut yalnızca canlı hesaplarla birlikte kullanılabilir. `databaseRid`Parametresi, `ResourceId` geri yüklemek istediğiniz veritabanının bir veritabanıdır. `ownerResourceid`Komutun yanıtında bulunan özniteliğin değeridir `az cosmosdb sql restorable-database list` .

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

Bu komut çıktısı, bu veritabanının içindeki tüm kapsayıcılar üzerinde gerçekleştirilen işlemlerin listesini içerir:

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

**Belirli bir zaman damgasında geri yüklenebilir veritabanlarını veya kapsayıcıları bulun**

Belirli bir zaman damgasında geri yüklenebilinen veritabanlarının veya kapsayıcıların listesini almak için aşağıdaki komutu kullanın. Bu komut yalnızca canlı hesaplarla birlikte kullanılabilir.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10 T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

## <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>MongoDB API hesabı için geri yüklenebilen kaynakları numaralandır

Aşağıda açıklanan listeleme komutları, çeşitli zaman damgalarına geri yükleme için kullanılabilen kaynakları keşfetmenize yardımcı olur. Ayrıca, geri yüklenebilen hesap, veritabanı ve kapsayıcı kaynaklarında önemli olayların bir akışını da sağlar. SQL API 'SI gibi, `az cosmosdb` komutunu, ancak yerine parametresiyle birlikte kullanabilirsiniz `mongodb` `sql` . Bu komutlar yalnızca canlı hesaplar için çalışır.

**Canlı bir veritabanı hesabındaki tüm MongoDB veritabanlarının sürümlerini listeleyin**

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

**Canlı bir veritabanı hesabındaki tüm MongoDB koleksiyonlarının sürümlerini listeleyin**

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "<InstanceID>" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

**Belirli bir zaman damgasına ve bölgeye geri yüklemek için kullanılabilen bir MongoDB veritabanı hesabının tüm kaynaklarını listeleyin**

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md)veya [Azure Resource Manager](continuous-backup-restore-template.md)kullanarak sürekli yedeklemeyi yapılandırın ve yönetin.
* [Sürekli yedekleme modunun kaynak modeli](continuous-backup-restore-resource-model.md)
* Sürekli yedekleme moduyla verileri geri yüklemek için gereken [Izinleri yönetin](continuous-backup-restore-permissions.md) .
