---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 400c5f0b2281679c3ec913e94643ffec6082d9e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100552421"
---
#### <a name="to-create-a-volume"></a>Birim oluşturmak için
1. **Cihazlar** dikey penceresindeki tablosal cihaz listesinden cihazınızı seçin. **+ Birim ekle**’ye tıklayın.<!--Note to reviewer. No screen updates. Had to rename screens to be able to correct a formatting error.-->

    ![Yeni birim ekleme](./media/storsimple-8000-create-volume-u2/create-volume-01.png)

2. **Birim ekle** dikey penceresinde:
   
   1. **Cihazı seçin** alanı otomatik olarak geçerli cihazınızla doldurulur.

   2. Açılan listeden, birim eklemeniz gereken birim kapsayıcısını seçin. 

   3. Biriminiz için bir **Ad** yazın. Birimi bir kez oluşturduktan sonra yeniden adlandıramazsınız.

   4. Açılan listede biriminizin **Tür**’ünü seçin. Yerel garantilerin, düşük gecikme sürelerinin ve yüksek performansın gerektiği iş yükleri için **Yerel olarak sabitlenmiş** bir birim seçin. Diğer tüm veriler için **Katmanlı** bir birim seçin. Bu birimi arşiv verileri için kullanıyorsanız **Bu birimi daha az sıklıkta erişilen arşiv verileri için kullanın**’ı işaretleyin.
      
       Katmanlı birim ölçülü sayıda kaynak kullanarak sağlanır ve hızlı oluşturulabilir. Arşiv verileri için hedeflenen katmanlı birime yönelik olarak **Bu birimi daha az sıklıkta erişilen arşiv verileri için kullan**’ın seçilmesi, biriminizle ilgili yinelenenleri kaldırma öbek boyutunu 512 KB olarak değiştirir. Bu alan işaretli değilse, ilgili katmanlı birim 64 KB öbek boyutu kullanır. Daha büyük bir yinelenenleri kaldırma öbek boyutu cihazın büyük arşiv verilerinin buluta aktarımını hızlandırmasını sağlar.
       
       Yerel olarak sabitlenmiş bir birim sıkı hazırlanmıştır ve birimdeki birincil verilerin cihazda yerel kalmasını ve buluta dağıtılmamasını sağlar.  Yerel olarak sabitlenmiş bir birim oluşturursanız cihaz, istenen boyutta birimi sağlamak için yerel katmanlardaki kullanılabilir alanı denetler. Yerel olarak sabitlenmiş birim oluşturma işlemi var olan verileri cihazdan buluta dağılmasını kapsayabilir ve birim oluşturmak için geçen süre uzun olabilir. Toplam süre hazırlanan birimin boyutuna, kullanılabilir ağ bant genişliğine ve cihazınızdaki verilere bağlıdır.

   5. Biriminiz için **Sağlanan Kapasite**’yi belirtin. Seçili birim türünü temel alan kullanılabilir kapasiteyi not edin. Belirtilen birim boyutu kullanılabilir alanı aşmamalıdır.
      
       8100 cihazında 8,5 TB'a kadar yerel olarak sabitlenmiş birim ya da 200 TB'a kadar katmanlı birim sağlayabilirsiniz. Daha büyük olan 8600 cihazında 22,5 TB'a kadar yerel olarak sabitlenmiş birim ya da 500 TB'a kadar katmanlı birim sağlayabilirsiniz. Katmanlı birimlerin çalışma kümesini barındırmak için cihazda yerel alan gerektiğinden, yerel olarak sabitlenmiş birimlerin oluşturulması, katmanlı birimlerin sağlanması için kullanılabilecek alanı etkiler. Bu nedenle, yerel olarak sabitlenmiş birim oluşturursanız, katmanlı birimlerin oluşturulması için gereken boş alan azalır. Benzer şekilde, katmanlı birim oluşturulduysa, yerel olarak sabitlenmiş birimlerin oluşturulması için kullanılabilir alan azalır.
      
       8100 cihazınızda 8,5 TB boyutunda (izin verilen en yüksek boyut) yerel olarak sabitlenmiş bir birim sağlarsanız, cihazdaki kullanılabilir yerel alanın tümünü kullanmış olursunuz. Katmanlı birimin çalışan kümesinin barındıracak cihazda yerel alan olmadığından, bu noktadan sonra herhangi bir katmanlı birim oluşturamazsınız. Var olan katmanlı birimler kullanılabilir alanı de etkiler. Örneğin, zaten 106 TB boyutunda katmanlı birimlerin bulunduğu bir 8100 cihazınız varsa, yerel olarak sabitlenmiş birimlerin kullanabileceği yalnızca 4 TB’lık alan kalır.
 
      1. **Bağlı konaklar** alanında oka tıklayın. 

         ![Bağlı konaklar](./media/storsimple-8000-create-volume-u2/create-volume-02.png)

      1. **Bağlı konaklar** dikey penceresinde mevcut bir ACR’yi seçin veya aşağıdaki adımları gerçekleştirerek yeni ACR ekleyin:

         1. ACR’nize bir **Ad** verin.
         2. **iSCSI Başlatıcısı Adı** altında, Windows konağınızın iSCSI Tam Adını (IQN) girin. IQN’niz yoksa [Windows Server konağının IQN’sini al](#get-the-iqn-of-a-windows-server-host)’a gidin.

      1. **Oluştur**’a tıklayın. Belirtilen ayarlarla bir birim oluşturulur.

         ![Oluştur’a tıklayın](./media/storsimple-8000-create-volume-u2/create-volume-03.png)

         > [!NOTE]
         > Burada oluşturduğunuz birimin korunmadığını unutmayın. Zamanlanan yedeklemeler almak için yedekleme ilkeleri oluşturmanız ve ilkeleri bu birimle ilişkilendirmeniz gerekir. 

