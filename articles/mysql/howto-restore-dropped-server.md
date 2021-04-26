---
title: Bir MySQL için Azure veritabanı sunucusunu geri yükleme
description: Bu makalede, Azure portal kullanarak MySQL için Azure veritabanı 'nda silinen bir sunucunun nasıl geri yükleneceği açıklanmaktadır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/09/2020
ms.openlocfilehash: 5fc1ab1b3dfbc324668873749c143846c2015cd4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306288"
---
# <a name="restore-a-deleted-azure-database-for-mysql-server"></a>Bir MySQL için Azure veritabanı sunucusunu geri yükleme

Bir sunucu silindiğinde, veritabanı sunucusu yedeklemesi hizmette beş güne kadar tutulabilirler. Veritabanı yedeklemesine yalnızca sunucunun ilk olarak bulunduğu Azure aboneliğinden erişilebilir ve geri yüklenebilir. Silinen bir MySQL sunucusu kaynağını sunucu silme zamanından 5 gün içinde kurtarmak için aşağıdaki önerilen adımları izlenebilir. Önerilen adımlar yalnızca sunucu yedeklemesinin hala kullanılabildiği ve sistemden silinmediği durumlarda çalışır. 

## <a name="pre-requisites"></a>Ön koşullar
Silinen bir MySQL için Azure veritabanı sunucusunu geri yüklemek için şunlar gerekir:
- Özgün sunucuyu barındıran Azure abonelik adı
- Sunucunun oluşturulduğu konum

## <a name="steps-to-restore"></a>Geri yükleme adımları

1. Azure portal Izleme dikey penceresinde [etkinlik günlüğü](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) ' ne gidin. 

2. Etkinlik günlüğü ' nde, gösterilen **Filtre Ekle** ' ye tıklayın ve aşağıdaki filtreleri ayarlayın 

    - **Abonelik** = silinen sunucuyu barındıran aboneliğiniz
    - **Kaynak türü** = MySQL sunucuları Için Azure veritabanı (Microsoft. Dbformyısql/sunucular) 
    - **Operation** = MySQL sunucusunu Sil (Microsoft. Dbformyısql/Servers/Delete) 
 
     [![MySQL Server 'ı silme işleminde etkinlik günlüğü filtrelendi](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. MySQL Server olayını Sil olayına çift tıklayın ve JSON sekmesine tıklayın ve JSON çıktısında "RESOURCEID" ve "submissionTimestamp" özniteliklerini aklınızda edin. RESOURCEID şu biçimdedir:/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforMySQL/servers/deletedserver.
 
 4. [Sunucu REST API Oluştur sayfasına](/rest/api/mysql/servers/create) gidin ve yeşil renkle vurgulanmış olan "dene" sekmesine tıklayın ve Azure hesabınızla oturum açın.
 
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
   - **Kaynak türü** = MySQL sunucuları Için Azure veritabanı (Microsoft. Dbformyısql/sunucular) 
   - **İşlem** = MySQL Server oluşturmayı Güncelleştir

## <a name="next-steps"></a>Sonraki adımlar
- Bir sunucuyu beş gün içinde geri yüklemeye çalışıyorsanız ve daha önce açıklanan adımları doğru şekilde gerçekleştirdikten sonra yine de bir hata alıyorsanız, yardım için bir destek olayı açın. Silinen bir sunucuyu beş günden sonra geri yüklemeye çalışıyorsanız, yedekleme dosyası bulunamadığı için bir hata beklenmektedir. Bu senaryoda bir destek bileti açmayın. Yedekleme sistemden silinirse destek ekibi herhangi bir yardım sağlayamaz. 
- Sunucuların yanlışlıkla silinmesini engellemek için, [kaynak kilitlerini](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222)kullanmanızı kesinlikle öneririz.
