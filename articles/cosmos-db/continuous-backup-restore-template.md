---
title: Azure Cosmos DB ' de sürekli yedeklemeyi ve zaman içinde geri yüklemeyi yapılandırmak için ARM şablonunu kullanın.
description: Azure Resource Manager şablonları kullanarak sürekli yedekleme ve verileri geri yükleme ile bir hesap sağlamayı öğrenin.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 4abfdd0209bd9f13fb7bd902b27a53f65156da2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381826"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-resource-manager-templates"></a>Sürekli yedekleme ve zaman noktası geri yükleme (Önizleme) ile yapılandırma ve yönetme-Azure Resource Manager şablonları kullanma
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB için zaman noktası geri yükleme özelliği (sürekli yedekleme modu) Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB zaman içinde geri yükleme özelliği (Önizleme), bir kapsayıcı içindeki yanlışlıkla yapılan bir değişikliği kurtarmanıza, silinen bir hesabı, veritabanını veya kapsayıcıyı geri yüklemeyi veya herhangi bir bölgeye geri yüklemeyi (yedeklemelerin varolduğu) sağlar. Sürekli yedekleme modu, son 30 gün içinde herhangi bir zaman noktasına geri yükleme yapmanıza olanak sağlar.

Bu makalede, Azure Resource Manager şablonları kullanarak sürekli yedekleme ve verileri geri yükleme ile bir hesabın nasıl sağlanacağı açıklanır.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Sürekli yedekleme ile hesap sağlama

Azure Cosmos DB bir hesabı sürekli mod ile dağıtmak için Azure Resource Manager şablonları kullanabilirsiniz. Bir hesap sağlamak için şablonu tanımlarken, `backupPolicy` Aşağıdaki örnekte gösterildiği gibi parametresini ekleyin:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

Daha sonra Azure PowerShell veya CLı kullanarak şablonu dağıtın. Aşağıdaki örnek, bir CLı komutuyla şablonun nasıl dağıtılacağını göstermektedir:

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore"></a>Kaynak Yöneticisi şablonunu kullanarak geri yükleme

Kaynak Yöneticisi şablonu kullanarak da bir hesabı geri yükleyebilirsiniz. Şablonu tanımlarken aşağıdaki parametreleri içerir:

* `createMode` *Geri yüklenecek* parametreyi ayarla
* `restoreParameters`' İ tanımlayın, `restoreSource` değerin `az cosmosdb restorable-database-account list` kaynak hesabınıza ait komutun çıktısından ayıklandığını unutmayın. Hesap adınızın örnek KIMLIĞI özniteliği geri yüklemeyi yapmak için kullanılır.
* `restoreMode`Parametresini *Poinıntime* olarak ayarlayın ve `restoreTimestampInUtc` değeri yapılandırın.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

Daha sonra Azure PowerShell veya CLı kullanarak şablonu dağıtın. Aşağıdaki örnek, bir CLı komutuyla şablonun nasıl dağıtılacağını göstermektedir:  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure CLI](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-command-line.md)veya [Azure Portal](continuous-backup-restore-portal.md)kullanarak sürekli yedeklemeyi yapılandırın ve yönetin.
* [Sürekli yedekleme modunun kaynak modeli](continuous-backup-restore-resource-model.md)
* Sürekli yedekleme moduyla verileri geri yüklemek için gereken [Izinleri yönetin](continuous-backup-restore-permissions.md) .