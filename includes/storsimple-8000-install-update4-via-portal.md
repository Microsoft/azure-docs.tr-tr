---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c3cfab78ab4bcc92b3e65b5e4fa04a29bf688de0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93376266"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Azure portalından bir güncelleştirmeyi yüklemek için

1. StorSimple hizmet sayfasında cihazınızı seçin.

    ![Cihaz Seç](./media/storsimple-8000-install-update4-via-portal/update1.png)

2. **Cihaz ayarları**  >  **cihaz güncelleştirmeleri**' ne gidin.

    ![Cihaz güncelleştirmeleri ' ne tıklayın](./media/storsimple-8000-install-update4-via-portal/update2.png)

2. Yeni güncelleştirmeler varsa bir bildirim görüntülenir. Alternatif olarak, **cihaz güncelleştirmeleri** dikey penceresinde **Güncelleştirmeleri Tara**' ya tıklayın. Kullanılabilir güncelleştirmeleri taramak için bir iş oluşturulur. İş başarıyla tamamlandığında size bildirilir.

    ![Cihaz güncelleştirmeleri 2 ' ye tıklayın](./media/storsimple-8000-install-update4-via-portal/update3.png)

3. Bir güncelleştirmeyi cihazınıza uygulamadan önce sürüm notlarını gözden geçirmeniz önerilir. Güncelleştirmeleri uygulamak için **güncelleştirmeleri yüklensin**' e tıklayın. **Düzenli güncelleştirmeleri Onayla** dikey penceresinde, güncelleştirmeleri uygulamadan önce tamamlanacak önkoşulları gözden geçirin. Cihazı güncelleştirmeye hazırız olduğunu göstermek için onay kutusunu işaretleyin ve ardından **Install**' a tıklayın.

    ![Cihaz güncelleştirmeleri 3 ' e tıklayın](./media/storsimple-8000-install-update4-via-portal/update4.png)

6. Bir dizi önkoşul denetimi başlatılır. Bu denetimler şunlardır:
   
   * Her iki cihaz denetleyicisinin de sağlıklı ve çevrimiçi olduğunu doğrulamaya yönelik **denetleyici durumu denetimleri**.
   * StorSimple cihazınızdaki tüm donanım bileşenlerinin sağlıklı olduğunu doğrulamaya yönelik **donanım bileşeni durum denetimleri**.
   * DATA 0’ın cihazınızda etkin olduğunu doğrulamaya yönelik **DATA 0 denetimleri**. Bu arabirim etkin değilse etkinleştirmeniz ve sonra yeniden denemeniz gerekir.

     Güncelleştirme indirilir ve yalnızca tüm denetimler başarıyla tamamlanırsa yüklenir. Denetimler devam ederken size bildirilir. Ön denetimler başarısız olursa, hata nedenleri ile birlikte sağlanacaktır. Bu sorunları çözün ve işlemi yeniden deneyin. Bu sorunları kendi başınıza çözemiyorsanız Microsoft Desteği’ne başvurmanız gerekebilir.

7. Ön denetimler başarıyla tamamlandıktan sonra bir güncelleştirme işi oluşturulur. Güncelleştirme işi başarıyla oluşturulduğunda size bildirilir.
   
    ![Güncelleştirme işi oluşturma](./media/storsimple-8000-install-update4-via-portal/update6.png)
   
    Bundan sonra güncelleştirme, cihazınıza uygulanır.

9. Güncelleştirmenin tamamlanması birkaç saat sürer. Güncelleştirme işini seçin ve **Ayrıntılar**’a tıklayarak dilediğiniz zaman işin ayrıntılarını görüntüleyin.

    ![Güncelleştirme işi oluşturma 2](./media/storsimple-8000-install-update4-via-portal/update8.png)

     Ayrıca, güncelleştirme işinin ilerlemesini **cihaz ayarları > işlerle** izleyebilirsiniz. **İşler** dikey penceresinde, güncelleştirme ilerlemesini görebilirsiniz.

     ![Güncelleştirme işi oluşturma 3](./media/storsimple-8000-install-update4-via-portal/update7.png)

10. İş tamamlandıktan sonra cihaz **güncelleştirmeleri > cihaz ayarları**' na gidin. Yazılım sürümü şimdi güncellenmelidir.

   ![Güncelleştirme işi oluşturma 4](./media/storsimple-8000-install-update4-via-portal/update9.png)

