---
title: Azure Cosmos DB içinde sürekli yedeklemeyi ve zaman içinde geri yüklemeyi yapılandırmak için Azure PowerShell kullanın.
description: Azure PowerShell kullanarak sürekli yedekleme ve verileri geri yükleme ile bir hesap sağlamayı öğrenin.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 5261075a82eaefd91cbedd2dd2fe08cb1e0a20b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381843"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-powershell"></a>Sürekli yedekleme ve zaman noktası geri yükleme (Önizleme) ile yapılandırma ve yönetme-Azure PowerShell kullanma
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB için zaman noktası geri yükleme özelliği (sürekli yedekleme modu) Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB zaman içinde geri yükleme özelliği (Önizleme), bir kapsayıcı içindeki yanlışlıkla yapılan bir değişikliği kurtarmanıza, silinen bir hesabı, veritabanını veya kapsayıcıyı geri yüklemeyi veya herhangi bir bölgeye geri yüklemeyi (yedeklemelerin varolduğu) sağlar. Sürekli yedekleme modu, son 30 gün içinde herhangi bir zaman noktasına geri yükleme yapmanıza olanak sağlar.

Bu makalede, Azure PowerShell kullanarak sürekli yedekleme ve verileri geri yükleme ile bir hesabın nasıl sağlanacağı açıklanır.

## <a name="install-azure-powershell"></a><a id="install-powershell"></a>Azure PowerShell'i yükleme

1. `Az.CosmosDB`Zaman içinde nokta geri yükleme ile ilgili komutları içeren önizleme modülünü yüklemek için Azure PowerShell ' den aşağıdaki komutu çalıştırın:

   ```azurepowershell
   Install-Module -Name Az.CosmosDB -AllowPrerelease
   ```

1. Modülleri yükledikten sonra kullanarak Azure 'da oturum açın

   ```azurepowershell
   Connect-AzAccount
   ```

1. Aşağıdaki komutla belirli bir abonelik seçin:

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Sürekli yedekleme ile bir SQL API hesabı sağlama

Sürekli yedekleme ile bir hesap sağlamak için `-BackupPolicyType Continuous` normal sağlama komutuyla birlikte bir bağımsız değişken ekleyin.

Aşağıdaki cmdlet, `pitracct2` *myrg* kaynak grubu altında *Batı ABD* bölgede sürekli yedekleme ilkesiyle oluşturulan tek bir bölge yazma hesabına örnektir:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct2" `
  -ApiKind "Sql"
      
```

## <a name="provision-a-mongodb-api-account-with-continuous-backup"></a><a id="provision-mongodb-api"></a>Sürekli yedekleme ile MongoDB API hesabı sağlama

Aşağıdaki cmdlet, *myrg* kaynak grubu altında *Batı ABD* bölgede oluşturulan sürekli yedekleme hesabı *pitracct2* örneğidir:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct3" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="trigger-a-restore-operation"></a><a id="trigger-restore"></a>Geri yükleme işlemi tetikleyin

Aşağıdaki cmdlet, hedef hesap, kaynak hesap, konum, kaynak grubu ve zaman damgası kullanarak restore komutuyla geri yükleme işleminin tetiklenmesi için bir örnektir:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName <resourceGroupName> `
  -TargetDatabaseAccountName <restored-account-name> `
  -SourceDatabaseAccountName <sourceDatabaseAccountName> `
  -RestoreTimestampInUtc <UTC time> `
  -Location <Azure Region Name>

```

**Örnek 1:** Tüm hesap geri yükleniyor:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**Örnek 2:** Belirli koleksiyonlar ve veritabanları geri yükleniyor. Bu örnek, myCol1, myCol2 from myDB1 ve tüm veritabanı myDB2 içindeki koleksiyonları geri yükler, bunun altındaki tüm kapsayıcıları içerir.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB1" -CollectionName "myCol1", "myCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>SQL API için geri yüklenebilen kaynakları listeleme

Sabit Listesi cmdlet 'leri, çeşitli zaman damgalarına geri yükleme için kullanılabilen kaynakları keşfetmenize yardımcı olur. Ayrıca, geri yüklenebilen hesap, veritabanı ve kapsayıcı kaynaklarında önemli olayların bir akışını da sağlar.

**Geçerli abonelikte geri yüklenmiş tüm hesapları listeleyin**

`Get-AzCosmosDBRestorableDatabaseAccount`Geçerli abonelikte geri yüklenebildiğinden tüm hesapları listelemek için PowerShell komutunu çalıştırın.

Yanıt, geri yüklenen ve geri yüklenebilecekleri bölgeleri içeren tüm veritabanı hesaplarını (hem canlı hem de silinmiş) içerir.

```console
{
    "accountName": "sampleaccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "locationName": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  },
```

`CreationTime`Ya da `DeletionTime` hesabı için olduğu gibi, bölge için de bir `CreationTime` veya vardır `DeletionTime` . Bu süreler, bu bölgeye geri yüklenecek doğru bölgeyi ve geçerli bir zaman aralığını seçmenizi sağlar.

**Canlı bir veritabanı hesabındaki tüm SQL veritabanlarının sürümlerini listeleyin**

Veritabanlarının tüm sürümlerinin listelenmesi, veritabanının mevcut zamanının bilinmediği bir senaryoda doğru veritabanını seçmenize olanak sağlar.

Veritabanlarının tüm sürümlerini listelemek için aşağıdaki PowerShell komutunu çalıştırın. Bu komut yalnızca canlı hesaplarla birlikte kullanılabilir. `DatabaseAccountInstanceId`Ve parametreleri, `LocationName` `name` `location` cmdlet 'inin yanıtı içindeki ve özelliklerinden elde edilir `Get-AzCosmosDBRestorableDatabaseAccount` . `DatabaseAccountInstanceId`Öznitelik, `instanceId` Geri yüklenmekte olan kaynak veritabanı hesabının özelliğine başvurur:


```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -LocationName "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

**Canlı bir veritabanı hesabındaki bir veritabanının tüm SQL kapsayıcıları sürümlerini listeleyin.**

Tüm SQL kapsayıcıları sürümlerini listelemek için aşağıdaki komutu kullanın. Bu komut yalnızca canlı hesaplarla birlikte kullanılabilir. `DatabaseRid`Parametresi, `ResourceId` geri yüklemek istediğiniz veritabanının bir veritabanıdır. Bu, `ownerResourceid` cmdlet 'inin yanıtında bulunan özniteliğin değeridir `Get-AzCosmosdbSqlRestorableDatabase` . Yanıt Ayrıca bu veritabanının içindeki tüm kapsayıcılar üzerinde gerçekleştirilen işlemlerin bir listesini içerir.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"

```

**Belirli bir zaman damgasında geri yüklenebilir veritabanlarını veya kapsayıcıları bulun**

Belirli bir zaman damgasında geri yüklenebilinen veritabanlarının veya kapsayıcıların listesini almak için aşağıdaki komutu kullanın. Bu komut yalnızca canlı hesaplarla birlikte kullanılabilir.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

## <a name="enumerate-restorable-resources-for-mongodb"></a><a id="enumerate-mongodb-api"></a>MongoDB için geri yüklenebilen kaynakları listeleme

Aşağıda açıklanan listeleme komutları, çeşitli zaman damgalarına geri yükleme için kullanılabilen kaynakları keşfetmenize yardımcı olur. Ayrıca, geri yüklenebilen hesap, veritabanı ve kapsayıcı kaynaklarında önemli olayların bir akışını da sağlar. Bu komutlar yalnızca canlı hesaplar için çalışır ve SQL API komutlarına benzerdir, ancak `MongoDB` yerine komut adında `sql` .

**Canlı bir veritabanı hesabındaki tüm MongoDB veritabanlarının sürümlerini listeleyin**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US"

```

**Canlı bir veritabanı hesabındaki tüm MongoDB koleksiyonlarının sürümlerini listeleyin**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"
```

**Belirli bir zaman damgasına ve bölgeye geri yüklemek için kullanılabilen bir MongoDB veritabanı hesabının tüm kaynaklarını listeleyin**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure CLI](continuous-backup-restore-command-line.md), [Kaynak Yöneticisi](continuous-backup-restore-template.md)veya [Azure Portal](continuous-backup-restore-portal.md)kullanarak sürekli yedeklemeyi yapılandırın ve yönetin.
* [Sürekli yedekleme modunun kaynak modeli](continuous-backup-restore-resource-model.md)
* Sürekli yedekleme moduyla verileri geri yüklemek için gereken [Izinleri yönetin](continuous-backup-restore-permissions.md) .
