---
title: Azure Cosmos DB zaman noktası geri yükleme özelliği hakkında sık sorulan sorular.
description: Bu makalede, sürekli yedekleme modu kullanılarak elde edilen Azure Cosmos DB zaman noktası geri yükleme özelliği hakkında sık sorulan sorular listelenmektedir.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1cf94964f420f7a7d4fc0f6ba0b77813b3e75787
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393233"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Azure Cosmos DB zaman noktası geri yükleme özelliği (Önizleme) hakkında sık sorulan sorular
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB için zaman noktası geri yükleme özelliği (sürekli yedekleme modu) Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bu makalede, sürekli yedekleme modu kullanılarak elde edilen Azure Cosmos DB zaman noktası geri yükleme işlevselliği (Önizleme) hakkında sık sorulan sorular listelenmektedir.

## <a name="how-much-time-does-it-takes-to-restore"></a>Ne kadar zaman geri yükleme sürüyor?
Geri yükleme süresi verilerinizin boyutuna bağlıdır.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>Geri yükleme saatini yerel saate gönderebilir miyim?
Geri yükleme, veritabanları veya kapsayıcılar gibi önemli kaynakların o anda var olup olmadığına bağlı olarak gerçekleşmeyebilir. Saati girip, belirli bir süre için seçili veritabanına veya kapsayıcıya bakarak emin olabilirsiniz. Geri yüklenecek kaynak yok görürseniz geri yükleme işlemi çalışmaz.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>Bir hesabın geri yüklenip yüklenmediğinde nasıl izleyebilirim?
Restore komutunu gönderdikten ve aynı sayfada bekledikten sonra, işlem tamamlandıktan sonra durum çubuğunda başarıyla geri yüklenen hesap iletisi görüntülenir. Ayrıca, geri yüklenen hesabı arayabilir ve [Geri yüklenmekte olan hesabın durumunu izleyebilirsiniz](continuous-backup-restore-portal.md#track-restore-status). Geri yükleme devam *ederken, hesabın durumu oluşturulacak, geri* yükleme işlemi tamamlandıktan sonra hesap durumu *çevrimiçi* olarak değişir.

Benzer şekilde, PowerShell ve CLı için aşağıdaki gibi komutu yürüterek geri yükleme işleminin ilerlemesini izleyebilirsiniz `az cosmosdb show` :

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

Hesap çevrimiçi olduğunda provisioningState *başarılı* olarak gösterilir.

```json
{
"virtualNetworkRules": [],
"writeLocations" : [
{
    "documentEndpoint": "https://<accountname>.documents.azure.com:443/", 
    "failoverpriority": 0,
    "id": "accountName" ,
    "isZoneRedundant" : false, 
    "locationName": "West US 2", 
    "provisioningState": "Succeeded"
}
]
}
```

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>Bir hesabın başka bir hesaptan geri yüklenip yüklenmediğini nasıl öğrenebilirim?
Komutunu çalıştırın `az cosmosdb show` , çıktıda özelliğinin değerini görebilirsiniz `createMode` . Değer **geri yükle** olarak ayarlandıysa. hesabın başka bir hesaptan geri yüklendiğini belirtir. `restoreParameters`Özelliği `restoreSource` , kaynak hesap kimliği olan gibi daha ayrıntılı ayrıntılara sahiptir. Parametresindeki son GUID, `restoreSource` kaynak hesabın InstanceId 'sidir.

Örneğin, aşağıdaki çıktıda, kaynak hesabın örnek KIMLIĞI *7b4bb-f6a0-430e-ade1-638d781830cc* ' dir

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>Paylaşılan bir üretilen iş veritabanını veya bir kapsayıcıyı paylaşılan bir üretilen iş veritabanı içinde geri yüklerken ne olur?
Tüm paylaşılan üretilen iş veritabanı geri yüklendi. Geri yükleme için paylaşılan bir üretilen iş veritabanında kapsayıcıların bir alt kümesini seçemezsiniz.

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>Hesap tanımında InstanceId 'nin kullanımı nedir?
Belirli bir zamanda, Azure Cosmos DB hesabının `accountName` özelliği, etkin durumdayken genel olarak benzersizdir. Ancak, hesap silindikten sonra, aynı ada sahip başka bir hesap oluşturulabilir ve bu nedenle "accountName" bir hesap örneğini tanımlamak için artık yeterli değildir. 

ID veya bir `instanceId` Hesap örneğinin özelliğidir ve geri yükleme için aynı ada sahip olmaları durumunda birden fazla hesapta (canlı ve silinmiş) ayırt etmek için kullanılır. Veya komutlarını çalıştırarak örnek KIMLIĞINI alabilirsiniz `Get-AzCosmosDBRestorableDatabaseAccount`  `az cosmosdb restorable-database-account` . Ad özniteliği değeri "InstanceId" değerini gösterir.

## <a name="next-steps"></a>Sonraki adımlar

* [Sürekli yedekleme](continuous-backup-restore-introduction.md) modu nedir?
* [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)veya [Azure Resource Manager](continuous-backup-restore-template.md)kullanarak sürekli yedeklemeyi yapılandırın ve yönetin.
* Sürekli yedekleme moduyla verileri geri yüklemek için gereken [Izinleri yönetin](continuous-backup-restore-permissions.md) .
* [Sürekli yedekleme modunun kaynak modeli](continuous-backup-restore-resource-model.md)