---
title: Azure Işlevleri için depolama konuları
description: Azure Işlevlerinin depolama gereksinimlerini ve depolanan verileri şifreleme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: 5faa85a4fac9fc0b8639f33c475283f4f043c627
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779264"
---
# <a name="storage-considerations-for-azure-functions"></a>Azure Işlevleri için depolama konuları

Bir işlev uygulaması örneği oluşturduğunuzda Azure Işlevleri bir Azure depolama hesabı gerektirir. Aşağıdaki depolama hizmetleri, işlev uygulamanız tarafından kullanılabilir:


|Depolama hizmeti  | İşlev kullanımı  |
|---------|---------|
| [Azure Blob Depolama](../storage/blobs/storage-blobs-introduction.md)     | Bağlama durumu ve işlev anahtarlarını koruyun.  <br/>Ayrıca [, dayanıklı işlevler görev hub 'ları](durable/durable-functions-task-hubs.md)tarafından kullanılır. |
| [Azure Dosyaları](../storage/files/storage-files-introduction.md)  | İşlev uygulaması kodunuzu bir [Tüketim planı](consumption-plan.md) ve [Premium planda](functions-premium-plan.md)depolamak ve çalıştırmak için kullanılan dosya paylaşma. |
| [Azure Kuyruk Depolama](../storage/queues/storage-queues-introduction.md)     | [Dayanıklı işlevler 'de görev hub 'ları](durable/durable-functions-task-hubs.md)tarafından kullanılır.   |
| [Azure Tablo Depolama](../storage/tables/table-storage-overview.md)  |  [Dayanıklı işlevler 'de görev hub 'ları](durable/durable-functions-task-hubs.md)tarafından kullanılır.       |

> [!IMPORTANT]
> Tüketim/Premium barındırma planını kullanırken, işlev kodunuz ve bağlama yapılandırma dosyalarınız ana depolama hesabındaki Azure dosya depolama alanında depolanır. Ana depolama hesabını sildiğinizde bu içerik silinir ve kurtarılamaz.

## <a name="storage-account-requirements"></a>Depolama hesabı gereksinimleri

Bir işlev uygulaması oluştururken blob, kuyruk ve tablo depolamayı destekleyen genel amaçlı bir Azure depolama hesabı oluşturmanız veya bağlamanız gerekir. Bunun nedeni, Işlevlerin Tetikleyicileri yönetme ve işlev yürütmelerinin günlüğe kaydedilmesini gibi işlemler için Azure Storage 'ı temel aldığından oluşur. Bazı depolama hesapları kuyrukları ve tabloları desteklemez. Bu hesaplar yalnızca BLOB depolama hesapları ve Azure Premium depolama içerir.

Depolama hesabı türleri hakkında daha fazla bilgi edinmek için bkz. [Azure Depolama Hizmetlerine Giriş](../storage/common/storage-introduction.md#core-storage-services). 

İşlev uygulamanızla mevcut bir depolama hesabını kullanabilirsiniz, ancak bu gereksinimleri karşıladığından emin olmanız gerekir. Bu depolama hesabı gereksinimlerini karşılamak için Azure portal, işlev uygulaması oluşturma akışının bir parçası olarak oluşturulan depolama hesapları garanti edilir. Portalda, bir işlev uygulaması oluştururken Mevcut bir depolama hesabı seçerken desteklenmeyen hesaplar filtrelenmez. Bu akışta yalnızca oluşturmakta olduğunuz işlev uygulamasıyla aynı bölgedeki mevcut depolama hesaplarını seçebilirsiniz. Daha fazla bilgi için bkz. [depolama hesabı konumu](#storage-account-location).

<!-- JH: Does using a Premium Storage account improve perf? -->

## <a name="storage-account-guidance"></a>Depolama hesabı Kılavuzu

Her işlev uygulamasının çalışması için bir depolama hesabının olması gerekir. Bu hesap silinirse, işlev uygulamanız çalışmaz. Depolama ile ilgili sorunları gidermek için bkz. [depolama ile ilgili sorunları giderme](functions-recover-storage-account.md). Aşağıdaki ek konular, işlev uygulamaları tarafından kullanılan depolama hesabı için geçerlidir.

### <a name="storage-account-location"></a>Depolama hesabı konumu

En iyi performansı elde etmek için, işlev uygulamanız aynı bölgedeki bir depolama hesabını kullanmalıdır, bu da gecikmeyi azaltır. Azure portal, bu en iyi yöntemi zorlar. Bir nedenden dolayı, işlev uygulamanızdan farklı bir bölgede bir depolama hesabı kullanmanız gerekiyorsa, işlev uygulamanızı portalın dışında oluşturmanız gerekir. 

### <a name="storage-account-connection-setting"></a>Depolama hesabı bağlantı ayarı

Depolama hesabı bağlantısı [AzureWebJobsStorage uygulama ayarında](./functions-app-settings.md#azurewebjobsstorage)tutulur. 

Depolama anahtarlarını yeniden oluşturduğunuzda depolama hesabı bağlantı dizesinin güncellenmesi gerekir. [Depolama anahtarı yönetimi hakkında buradan daha fazla bilgi edinin](../storage/common/storage-account-create.md).

### <a name="shared-storage-accounts"></a>Paylaşılan depolama hesapları

Birden çok işlevli uygulamanın herhangi bir sorun olmadan aynı depolama hesabını paylaşması mümkündür. Örneğin, Visual Studio 'da Azure Storage öykünücüsü 'nü kullanarak birden çok uygulama geliştirebilirsiniz. Bu durumda, öykünücü tek bir depolama hesabı gibi davranır. İşlev uygulamanız tarafından kullanılan depolama hesabı, uygulama verilerinizi depolamak için de kullanılabilir. Ancak, bu yaklaşım her zaman bir üretim ortamında iyi bir fikir değildir.

### <a name="optimize-storage-performance"></a>Depolama performansını iyileştirme

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

## <a name="storage-data-encryption"></a>Depolama veri şifrelemesi

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

### <a name="in-region-data-residency"></a>Bölge içi veri yerleşimi

Tüm müşteri verilerinin tek bir bölge içinde kalması gerektiğinde, işlev uygulamasıyla ilişkili depolama hesabı, [bölge içi yedeklerle](../storage/common/storage-redundancy.md)bir tane olmalıdır. Bölgesel olarak yedekli depolama hesabının da [Azure dayanıklı işlevler](./durable/durable-functions-perf-and-scale.md#storage-account-selection)ile kullanılması gerekir.

Diğer platform tarafından yönetilen müşteri verileri yalnızca iç yük dengeli bir App Service Ortamı (ASE) barındırırken bölge içinde depolanır. Daha fazla bilgi için bkz. [AI bölge artıklığı](../app-service/environment/zone-redundancy.md#in-region-data-residency).

## <a name="mount-file-shares"></a>Dosya paylaşımlarını bağlama

_Bu işlevsellik yalnızca Linux üzerinde çalışırken geçerlidir._ 

Mevcut Azure dosya paylaşımlarını Linux işlev uygulamalarınıza bağlayabilirsiniz. Linux işlev uygulamanıza bir paylaşma bağlayarak, işlevlerinizde mevcut makine öğrenimi modellerini veya diğer verileri kullanabilirsiniz. [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add)Komutunu, mevcut bir paylaşımın Linux işlev uygulamanıza bağlamak için kullanabilirsiniz. 

Bu komutta, `share-name` var olan Azure dosya paylaşımının adıdır ve `custom-id` işlev uygulamasına bağlandığında, bu paylaşımın benzersiz bir şekilde tanımlayan herhangi bir dize olabilir. Ayrıca, `mount-path` işlev uygulamanızda paylaşıma erişildiği yoldur. `mount-path` biçiminde olmalıdır `/dir-name` ve ile başlayamaz `/home` .

Tüm bir örnek için bkz. [Python işlev uygulaması oluşturma ve Azure dosya paylaşma bağlama](scripts/functions-cli-mount-files-storage-linux.md)içindeki betikler. 

Şu anda yalnızca bir `storage-type` , `AzureFiles` desteklenir. Yalnızca belirli bir işlev uygulamasına beş paylaşım bağlayabilirsiniz. Bir dosya paylaşımının bağlanması, en az 200-300ms veya depolama hesabı farklı bir bölgedeyse daha da fazla olan soğuk başlangıç süresini artırabilir.

Bağlı paylaşma, belirtilen işlev kodunuz için kullanılabilir `mount-path` . Örneğin, olduğunda `mount-path` `/path/to/mount` , aşağıdaki Python örneğinde olduğu gibi, hedef dizine dosya sistemi API 'leri ile erişebilirsiniz:

```python
import os
...

files_in_share = os.listdir("/path/to/mount")
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Işlevleri barındırma seçenekleri hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure İşlevleri’ni ölçeklendirme ve barındırma](functions-scale.md)
