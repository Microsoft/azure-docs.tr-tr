---
title: Depolama hesabı kapsayıcısı kurtarma
description: Depolama hesabı kapsayıcısı kurtarma
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
ms.openlocfilehash: f095bdfe7bbb5777a2ad2aabb3bda92d0974457d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693585"
---
# <a name="storage-account-container-recovery"></a>Depolama hesabı kapsayıcısı kurtarma

Azure depolama, otomatik çoğaltmalar aracılığıyla veri dayanıklılığı sağlar. Ancak, bu, yanlışlıkla veya kötü amaçlı olarak, uygulama kodunun veya kullanıcıların verileri bozmasını engellemez. Uygulama veya Kullanıcı hatası durumunda verilerin aslına uygunluğunu korumak, verileri bir denetim günlüğü ile ikincil bir depolama konumuna kopyalama gibi daha gelişmiş teknikler gerektirir.

## <a name="checking-azure-storage-account-type"></a>Azure depolama hesabı türü denetleniyor

1. [Azure portalına](https://portal.azure.com/) gidin.

2. Depolama hesabınızı bulun.

3. **Genel bakış** bölümünde, **çoğaltmayı**kontrol edin.

   ![Görüntü](media/storage-account-container-recovery/1.png)

4. Çoğaltma türü **GRS/RA-GRS**ise, garanti olmadan hesap kapsayıcısı kurtarma mümkündür. Diğer tüm çoğaltma türleri için bu mümkün değildir.

5. Aşağıdaki bilgileri toplayın ve Microsoft Desteği bir destek isteği dosyasına koyun.

   * Depolama hesabı adı:
   * Kapsayıcı adı:
   * Silinme zamanı:

   Aşağıdaki tabloda, çoğaltma stratejisine bağlı olarak depolama hesabı kapsayıcısı kurtarma kapsamına genel bakış sunulmaktadır.

   |Içerik türü|LRS|ZRS|GRS|RA-GRS| 
   |---|---|---|---|---|
   |Depolama kapsayıcısı|Hayır|Hayır|Evet|Evet| 

   * Depolama hesabı kapsayıcısını ve garantisi olmadan geri yüklemeyi deneyebilirsiniz. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>Kurtarma işleminin başarılı olması için yapmanız gerekenler

* Lütfen kapsayıcıyı (aynı ada sahip) yeniden oluşturmayın.  
* Kapsayıcıyı zaten yeniden oluşturduysanız, kurtarma için bir destek isteği yapmadan önce kapsayıcıyı silmeniz gerekir.

## <a name="steps-to-prevent-this-in-the-future"></a>Gelecekte bunu engellemeye yönelik adımlar

1. Bundan sonra bunu önlemek için, en önerilen özelliği, [geçici silme](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)' dır.

2. [Anlık görüntü](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob) özelliğini de öneriyoruz.
 
## <a name="next-steps"></a>Sonraki adımlar

Bu özellik için iki örnek kod aşağıda verilmiştir:

  * [.NET 'te blob anlık görüntüsü oluşturma ve yönetme](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [PowerShell ile blob anlık görüntülerini kullanma](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

