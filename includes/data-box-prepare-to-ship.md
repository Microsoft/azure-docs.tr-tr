---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/25/2019
ms.author: alkohli
ms.openlocfilehash: 1d52117440028c75b249f469f2b3576c2ab1c5c5
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "67189035"
---
Son adım cihazı göndermeye hazırlamaktır. Bu adımda tüm cihaz paylaşımları çevrimdışı duruma getirilir. Bu işlem başlatıldıktan sonra paylaşımlara erişilemez.

> [!IMPORTANT]
> Azure adlandırma kurallarıyla uyumlu olmayan verileri işaretlediği için göndermeye hazırlama adımı gereklidir. Bu adımın atlanması, uyumsuz veriler nedeniyle olası karşıya veri yükleme hatalarıyla sonuçlanabilir.

1. **Göndermeye hazırlama** sayfasına gidip **Hazırlamayı başlat**'a tıklayın. Varsayılan olarak, veriler kopyalanırken sağlama toplamları hesaplanır. Göndermeye hazırlama işlemi, sağlama toplamı hesaplamasını tamamlar ve dosya listesini ( *-BOM dosyaları*) oluşturur. Sağlama toplamı hesaplaması, verilerinizin boyutuna bağlı olarak birkaç saat veya birkaç gün sürebilir. 
   
    ![Göndermeye hazırlama 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Herhangi bir nedenle cihaz hazırlamayı durdurmak isterseniz **Hazırlamayı durdur**'a tıklayın. Daha sonra göndermeye hazırlama işlemine kaldığınız yerden devam edebilirsiniz.
        
    ![Göndermeye hazırlama 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. Göndermeye hazırlama işlemi başlar ve cihaz paylaşımları çevrimdışı olur. Cihaz hazır olduktan sonra gönderim etiketini indirmek için bir anımsatıcı görürsünüz.

    ![Gönderim etiketi indirme anımsatıcısı](media/data-box-prepare-to-ship/download-shipping-label-reminder.png)

3. Cihaz hazırlığı tamamlandıktan sonra cihaz durumu *Göndermeye hazır* olarak değişir ve cihaz kilitlenir.
        
    ![Göndermeye hazırlama 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Cihaza daha fazla veri kopyalamak istiyorsanız cihazın kilidini açabilir, daha fazla veri kopyalayabilir ve göndermeye hazırlamayı tekrar çalıştırabilirsiniz.

    Bu adımda hatalar oluşursa, hata günlüğünü indirmeniz ve hataları çözmeniz gerekecektir. Hatalar çözümlendikten sonra **Göndermeye hazırlama** işlemini çalıştırın.

4. Göndermeye hazırlama işlemi başarıyla tamamlandıktan sonra (hata olmadan), bu işlemde kopyalanan dosyaların listesini (bildirim olarak da bilinir) indirin. Daha sonra bu listeyi kullanarak Azure'a yüklenen dosyaları doğrulayabilirsiniz. Daha fazla bilgi için bkz. [Göndermeye hazırlama sırasında BOM dosyalarını inceleme](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Göndermeye hazırlama 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

5. Cihazı kapatın. **Kapat veya yeniden başlat** sayfasına gidip **Kapat**'a tıklayın. Onayınız istendiğinde devam etmek için **Tamam**'a tıklayın.

6. Kabloları sökün. Bir sonraki adım cihazı Microsoft'a göndermektir.
