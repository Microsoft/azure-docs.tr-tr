---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 5e923fdf560692c645c8a69e7e26d13f69d6920c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93375979"
---
### <a name="to-take-a-backup"></a>Yedek almak için

1. StorSimple Cihaz Yöneticisi hizmetinize gidin. Tablosal cihaz listesinden cihazınızı seçmek için tıklayın ve **Tüm ayarlar**’a tıklayın. **Ayarlar** dikey penceresinde **Ayarlar > Yönet > Yedekleme ilkesi**’ne gidin.

    ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu1.png)

2. **Yedekleme ilkesi** dikey penceresinde **+ İlke ekle**’ye tıklayın.

    ![Yedekleme ekleme-ilke 2](./media/storsimple-8000-take-backup/step8takebu2.png)

3. **Yedekleme ilkesi oluştur** dikey penceresinde, yedekleme ilkenize 3 - 150 arası karakterden oluşan bir ad verin.

4. Yedeği alınacak birimleri seçin. Birden fazla birim seçerseniz, kilitlenmeyle tutarlı yedek oluşturmak için bu birimler birlikte gruplandırılır.

    ![Yedekleme ekleme-ilke 3](./media/storsimple-8000-take-backup/step8takebu4.png)

5. **İlk zamanlamayı ekle** dikey penceresinde:

    1. Yedekleme türünü seçin. Daha hızlı geri yükleme gerçekleştirebilmek için **Yerel** anlık görüntüyü seçin. Veri dayanıklılığı için **Bulut** anlık görüntüsünü seçin.
    2. Yedekleme sıklığını dakika, saat, gün veya hafta cinsinden belirtin.
    3. Elde tutma süresini seçin. Elde tutma seçimleri yedekleme sıklığına bağlıdır. Örneğin, günlük ilkesi için elde tutma İlkesi hafta cinsinden belirtilebilirken, aylık ilkesi için elde tutma ay cinsindendir.
    4. Yedekleme ilkesinin başlangıç saatini ve tarihini seçin.
    5. Yedekleme ilkesini oluşturmak için **Tamam**’a tıklayın.

        ![Yedekleme ekleme-ilke 4](./media/storsimple-8000-take-backup/step8takebu5.png) 

6. Yedekleme ilkesi oluşturma işlemini başlatmak için **Oluştur**’a tıklayın. Yedekleme ilkesi başarıyla oluşturulduğunda size bildirilir. Yedekleme ilkeleri listesi de güncelleştirilir.
      
      ![Yedekleme ekleme-ilke 5](./media/storsimple-8000-take-backup/step8takebu9.png)
      
      Artık, birim verilerinizin zamanlanmış yedeklerini oluşturan bir yedekleme ilkesine sahipsiniz.




