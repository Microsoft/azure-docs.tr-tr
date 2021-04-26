---
title: MariaDB sunucusu için silinen bir Azure veritabanını geri yükleme
description: Bu makalede, Azure portal kullanarak MariaDB için Azure veritabanı 'nda silinen bir sunucunun nasıl geri yükleneceği açıklanmaktadır.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 4/12/2021
ms.openlocfilehash: 7cf2ce61239c7f2320f7b789acbb8f340e6de471
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315652"
---
# <a name="restore-a-deleted-azure-database-for-mariadb-server"></a>MariaDB sunucusu için silinen bir Azure veritabanını geri yükleme

Bir sunucu silindiğinde, veritabanı sunucusu yedeklemesi hizmette beş güne kadar tutulabilirler. Veritabanı yedeklemesine yalnızca sunucunun ilk olarak bulunduğu Azure aboneliğinden erişilebilir ve geri yüklenebilir. Silinen bir MariaDB sunucusu kaynağını, sunucu silme zamanından 5 gün içinde kurtarmak için aşağıdaki önerilen adımları izlenebilir. Önerilen adımlar yalnızca sunucu yedeklemesinin hala kullanılabildiği ve sistemden silinmediği durumlarda çalışır. 

## <a name="pre-requisites"></a>Ön koşullar
MariaDB sunucusu için silinen bir Azure veritabanı 'nı geri yüklemek için şunlar gerekir:
- Özgün sunucuyu barındıran Azure abonelik adı
- Sunucunun oluşturulduğu konum

## <a name="steps-to-restore"></a>Geri yükleme adımları

1. Azure portal Izleme dikey penceresinde [etkinlik günlüğü](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) ' ne gidin. 

2. Etkinlik günlüğü ' nde, gösterilen **Filtre Ekle** ' ye tıklayın ve aşağıdaki filtreleri ayarlayın 

    - **Abonelik** = silinen sunucuyu barındıran aboneliğiniz
    - **Kaynak türü** = MariaDB sunucuları Için Azure veritabanı (Microsoft. Dbformarıdb/sunucular) 
    - **İşlem** = MariaDB sunucusunu Sil (Microsoft. Dbformarıdb/Servers/Delete) 
 
     [![MariaDB sunucusu için etkinlik günlüğü filtreleme işlemi](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. MariaDB sunucusu olayını Sil ' e çift tıklayın ve JSON sekmesine tıklayıp JSON çıktısında "RESOURCEID" ve "submissionTimestamp" özniteliklerini aklınızda edin. RESOURCEID şu biçimdedir:/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBForMariaDB/servers/deletedserver.
 
 4. [Sunucu REST API Oluştur sayfasına](/rest/api/mariadb/servers/create) gidin ve yeşil renkle vurgulanmış olan "dene" sekmesine tıklayın ve Azure hesabınızla oturum açın.
 
 5. 3. adımda yakalanan RESOURCEID özniteliğinden türetilmiş resourceGroupName, serverName (silinen sunucu adı), SubscriptionID,, API-Version, görüntüde gösterildiği gibi önceden doldurulurken.
 
     [![REST API kullanarak sunucu oluşturma](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. Istek gövdesi bölümünde aşağıya kaydırın ve aşağıdakileri yapıştırın:
     
    ```json
    {
        "location": "Dropped Server Location",  
        "properties": 
            {
                "restorePointInTime": "submissionTimestamp - 15 minutes",
                "createMode": "PointInTimeRestore",
                "sourceServerId": "resourceId"
            }
    }
    ```

7. Yukarıdaki istek gövdesinde aşağıdaki değerleri değiştirin:
   * Silinen sunucunun ilk olarak oluşturulduğu Azure bölgesi ile "sunucu konumu bırakıldı"
   * "submissionTimestamp" ve "RESOURCEID" değeri 3. adımda yakalandı. 
   * "Restorepoinıntime" için, komutun hata içermediğinden emin olmak için "submissionTimestamp" değerini **15 dakika** olarak belirtin.

8. 201 veya 202 yanıt kodunu görürseniz geri yükleme isteği başarıyla gönderilir. 

9. Sunucu oluşturma, özgün sunucuda sağlanan veritabanı boyutuna ve işlem kaynaklarına bağlı olarak zaman alabilir. Geri yükleme durumu, filtreleme yoluyla etkinlik günlüğünden izlenebilir 
   - **Abonelik** = aboneliğiniz
   - **Kaynak türü** = MariaDB sunucuları Için Azure veritabanı (Microsoft. Dbformarıdb/sunucular) 
   - **Operation** = güncelleştirme MariaDB sunucu oluşturma

## <a name="next-steps"></a>Sonraki adımlar
- Bir sunucuyu beş gün içinde geri yüklemeye çalışıyorsanız ve daha önce açıklanan adımları doğru şekilde gerçekleştirdikten sonra yine de bir hata alıyorsanız, yardım için bir destek olayı açın. Silinen bir sunucuyu beş günden sonra geri yüklemeye çalışıyorsanız, yedekleme dosyası bulunamadığı için bir hata beklenmektedir. Bu senaryoda bir destek bileti açmayın. Yedekleme sistemden silinirse destek ekibi herhangi bir yardım sağlayamaz. 
- Sunucuların yanlışlıkla silinmesini engellemek için, [kaynak kilitlerini](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222)kullanmanızı kesinlikle öneririz.
