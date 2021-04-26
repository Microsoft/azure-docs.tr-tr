---
title: Azure kuyruk depolama 'ya giriş-Azure depolama
description: Çok sayıda iletiyi depolamaya yönelik bir hizmet olan Azure kuyruk depolama 'ya giriş konusuna bakın. Kuyruk depolama hizmeti bir URL biçimi, depolama hesabı, kuyruk ve ileti içerir.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 03/18/2020
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 32ce5af5371047ae814602e9118f622ee036bd9c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276221"
---
# <a name="what-is-azure-queue-storage"></a>Azure Kuyruk Depolama nedir?

Azure Kuyruk Depolama, çok sayıda iletiyi depolamaya yönelik bir hizmettir. HTTP veya HTTPS kullanarak kimliği doğrulanmış çağrılar aracılığıyla dünyanın her yerinden iletilere erişirsiniz. Kuyruk iletisi boyutu 64 KB 'ye kadar olabilir. Bir kuyruk, depolama hesabının toplam kapasite sınırına kadar milyonlarca ileti içerebilir. Kuyruklar genellikle zaman uyumsuz olarak işlenecek iş biriktirme listesi oluşturmak için kullanılır.

## <a name="queue-storage-concepts"></a>Kuyruk depolama kavramları

Kuyruk depolama aşağıdaki bileşenleri içerir:

![Bir depolama hesabı, kuyruklar ve iletiler arasındaki ilişkiyi gösteren diyagram.](./media/storage-queues-introduction/queue1.png)

- **URL biçimi:** Kuyruklar şu URL biçimi kullanılarak adreslenebilir:

  `https://<storage account>.queue.core.windows.net/<queue>`

  Aşağıdaki URL diyagramdaki bir kuyruğun adresini belirtir:

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Depolama hesabı:** Tüm Azure depolama erişimi bir depolama hesabı üzerinden yapılır. Depolama hesabı kapasitesi hakkında daha fazla bilgi için bkz. [Standart depolama hesapları Için ölçeklenebilirlik ve performans hedefleri](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

- **Kuyruk:** Kuyrukta bir dizi ileti vardır. Sıra **adı tamamen küçük harfle yazılmalıdır.** Sıraları adlandırma hakkında daha fazla bilgi için bkz. [ad kuyrukları ve meta verileri adlandırma](/rest/api/storageservices/naming-queues-and-metadata).

- **İleti:** İleti, biçimi ne olursa olsun en çok 64 KB büyüklüktedir. Sürüm 2017-07-29 ' den önce, izin verilen maksimum yaşam süresi yedi gündür. Sürüm 2017-07-29 veya üzeri için, en fazla yaşam süresi herhangi bir pozitif sayı veya iletinin süresinin dolmadığını belirten-1 olabilir. Bu parametre atlanırsa, varsayılan yaşam süresi yedi gündür.

## <a name="next-steps"></a>Sonraki adımlar

- [Depolama hesabı oluşturma](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [.NET kullanarak kuyruk depolamayı kullanmaya başlama](storage-dotnet-how-to-use-queues.md)
- [Java kullanarak kuyruk depolamayı kullanmaya başlama](storage-java-how-to-use-queue-storage.md)
- [Python kullanarak kuyruk depolamayı kullanmaya başlama](storage-python-how-to-use-queue-storage.md)
- [Node.jskullanarak kuyruk depolamayı kullanmaya başlama ](storage-nodejs-how-to-use-queues.md)
