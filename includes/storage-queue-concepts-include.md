---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 08/21/2020
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 45df30f4f1444b6148af9f3c7d47b94909ccef3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027342"
---
## <a name="what-is-queue-storage"></a>Kuyruk depolama nedir?

Azure Kuyruk depolama, HTTP veya HTTPS kullanan kimlik doğrulaması yapılmış çağrılar aracılığıyla dünyanın her yerinden erişilebilen çok sayıda iletinin depolanması için bir hizmettir. Tek bir kuyruk iletisinin boyutu 64 KB’ye kadar olabilir ve bir kuyrukta, depolama hesabının toplam kapasite sınırına kadar milyonlarca ileti bulunabilir. Kuyruk depolama genellikle zaman uyumsuz olarak işlenecek iş biriktirme listesi oluşturmak için kullanılır.

## <a name="queue-service-concepts"></a>Kuyruk hizmeti kavramlar

Azure Kuyruk hizmeti aşağıdaki bileşenleri içerir:

![Azure Kuyruk hizmeti bileşenleri](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Depolama hesabı:** Tüm Azure depolama erişimi bir depolama hesabı üzerinden yapılır. Depolama hesapları hakkında daha fazla bilgi için bkz. [depolama hesabına genel bakış](../articles/storage/common/storage-account-overview.md).
* **Kuyruk:** Kuyrukta bir dizi ileti vardır. Tüm iletiler bir kuyrukta olmalıdır. Kuyruk adının tamamen küçük harfli olması gerektiğini unutmayın. Kuyrukların adlandırılması hakkında daha fazla bilgi için bkz. [Kuyrukları ve Meta Verileri Adlandırma](/rest/api/storageservices/Naming-Queues-and-Metadata).
* **İleti:** İleti, biçimi ne olursa olsun en çok 64 KB büyüklüktedir. Bir iletinin kuyrukta kalabileceği en uzun süre 7 gündür. Sürüm 2017-07-29 veya üzeri için, en fazla yaşam süresi herhangi bir pozitif sayı veya iletinin süresinin dolmadığını belirten-1 olabilir. Bu parametre atlanırsa, varsayılan yaşam süresi yedi gündür.
* **URL biçimi:** Kuyruklar şu URL biçimi kullanılarak adreslenebilir: http:// `<storage account>` . Queue.Core.Windows.net/`<queue>`

    Aşağıdaki URL diyagramdaki bir kuyruğun adresini belirtir:

    `http://myaccount.queue.core.windows.net/incoming-orders`