---
title: Bırakılmış bir PostgreSQL sunucusu için Azure veritabanını geri yükleme
description: Bu makalede, Azure portal kullanarak, PostgreSQL için Azure veritabanı 'nda bırakılan bir sunucunun nasıl geri yükleneceği açıklanmaktadır.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 5b5bb9fd6e3d34fc4a6b0ae90a2cd76fc84e9ce1
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366530"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>Bırakılmış bir PostgreSQL sunucusu için Azure veritabanını geri yükleme

Sunucu bırakıldığında, veritabanı sunucusu yedeklemesi hizmette beş güne kadar tutulabilirler. Veritabanı yedeklemesine yalnızca sunucunun ilk olarak bulunduğu Azure aboneliğinden erişilebilir ve geri yüklenebilir. Sunucu silme zamanından itibaren, bırakılan bir PostgreSQL sunucusu kaynağını 5 gün içinde kurtarmak için aşağıdaki önerilen adımları izleyebilirsiniz. Önerilen adımlar yalnızca sunucu yedeklemesinin hala kullanılabildiği ve sistemden silinmediği durumlarda çalışır. 

## <a name="pre-requisites"></a>Ön koşullar
Bırakılan bir PostgreSQL sunucusu için Azure veritabanı 'nı geri yüklemek için şunlar gerekir:
- Özgün sunucuyu barındıran Azure abonelik adı
- Sunucunun oluşturulduğu konum

## <a name="steps-to-restore"></a>Geri yükleme adımları

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade)gidin. **Azure izleyici** hizmetini seçip **etkinlik günlüğü**' nü seçin.

2. Etkinlik günlüğü ' nde, gösterilen **Filtre Ekle** ' ye tıklayın ve aşağıdakiler için aşağıdaki filtreleri ayarlayın

    - **Abonelik** = silinen sunucuyu barındıran aboneliğiniz
    - **Kaynak türü** = PostgreSQL Için Azure veritabanı sunucuları (Microsoft. DBforPostgreSQL/Servers)
    - **Operation** = PostgreSQL sunucusunu Sil (Microsoft. DBforPostgreSQL/Servers/Delete)
 
    ![Etkinlik günlüğü, PostgreSQL için filtre uygulandı sunucu işlemi](./media/howto-restore-dropped-server/activity-log-azure.png)

3. **PostgreSQL sunucusunu Sil** olayını seçin, ardından **JSON sekmesini** seçin. `resourceId` `submissionTimestamp` JSON çıkışındaki ve özniteliklerini kopyalayın. RESOURCEID şu biçimdedir: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` .


 4. PostgreSQL [sunucu oluşturma REST API sayfasına](/rest/api/PostgreSQL/servers/create) gidin ve yeşil renkte vurgulanmış olan **deneyin** sekmesini seçin. Azure hesabınızla oturum açın.

 5. Önceki adımda yakalanan RESOURCEID özniteliği JSON değerine bağlı olarak **Resourcegroupname**, **ServerName** (silinen sunucu adı), **SubscriptionID** özelliklerini belirtin. Aşağıdaki görüntüde gösterildiği gibi, api sürümü özelliği önceden doldurulur ve olduğu gibi bırakılabilir.

    ![REST API kullanarak sunucu oluşturma](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. Istek gövdesi bölümünde aşağıda ilerleyin ve "bırakılan sunucu konumu" (ör. merkezileştirme, EastUS vb.), "submissionTimestamp" ve "RESOURCEID" değerlerini değiştirerek aşağıdakini yapıştırın. "Restorepoinıntime" için, komutun hata içermediğinden emin olmak için "submissionTimestamp" değerini **15 dakika** olarak belirtin.
    
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

    Örneğin, geçerli saat 2020-11-02T23:59:59.0000000 Z ise, zaman içinde en az 15 dakika önceki geri yükleme noktasını, 2020-11-02T23:44:59.0000000 Z olarak kullanmanızı öneririz.

    > [!Important]
    > Sunucu bırakıldıktan beş gün sonra zaman sınırı vardır. Beş günden sonra yedekleme dosyası bulunamadığı için bir hata beklenir.
    
7. 201 veya 202 yanıt kodunu görürseniz geri yükleme isteği başarıyla gönderilir. 

    Sunucu oluşturma, özgün sunucuda sağlanan veritabanı boyutuna ve işlem kaynaklarına bağlı olarak zaman alabilir. Geri yükleme durumu, filtreleme yoluyla etkinlik günlüğünden izlenebilir 
   - **Abonelik** = aboneliğiniz
   - **Kaynak türü** = PostgreSQL Için Azure veritabanı sunucuları (Microsoft. DBforPostgreSQL/Servers) 
   - **İşlem** = güncelleştirme PostgreSQL sunucu oluşturma

## <a name="next-steps"></a>Sonraki adımlar
- Bir sunucuyu beş gün içinde geri yüklemeye çalışıyorsanız ve daha önce açıklanan adımları doğru şekilde gerçekleştirdikten sonra yine de bir hata alıyorsanız, yardım için bir destek olayı açın. Bırakılan bir sunucuyu beş günden sonra geri yüklemeye çalışıyorsanız, yedekleme dosyası bulunamadığı için bir hata beklenecektir. Bu senaryoda bir destek bileti açmayın. Yedekleme sistemden silinirse destek ekibi herhangi bir yardım sağlayamaz. 
- Sunucuların yanlışlıkla silinmesini engellemek için, [kaynak kilitlerini](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222)kullanmanızı kesinlikle öneririz.
