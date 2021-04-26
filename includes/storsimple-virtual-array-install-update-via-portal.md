---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: df572b88cf8a67163b28ec87154dcea01646b9a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95555739"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Azure portalı aracılığıyla güncelleştirmeleri yüklemek için

1. StorSimple Cihaz Yöneticinize gidip **Cihazlar**’ı seçin. Hizmetinize bağlı cihazların listesinden güncelleştirmek istediğiniz cihazı seçip tıklayın. 

    ![Son olarak, MySSDevManager Device Manager vurgulanır ve seçilir, cihazlar vurgulanır ve seçilir ve cihaz MYSSIS1103 vurgulanır ve seçilir.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate1m.png) 

2. **Ayarlar** dikey penceresinde **Cihaz güncelleştirmeleri**'ne tıklayın. 

    ![MYSSIS1103 hakkında bilgi görüntülenir. Ayarlar ' da, cihaz güncelleştirmeleri vurgulanır.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate2m.png)  

3. Yazılım güncelleştirmeleri varsa bir ileti görürsünüz. Güncelleştirmeleri denetlemek için **Tara**’ya da tıklayabilirsiniz.

    ![Cihaz güncelleştirmeleri bölmesinde tarama düğmesi vurgulanır. Dört satırlık bir ileti vardır: son tarama/taranmamış/yazılım sürümü/10.0.10280.0.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate3m.png)

    Tarama başlayıp başarıyla tamamlandığında size bildirilir.

    ![Bildirim "cihaz için güncelleştirmelerin taranması Myssıs 1103.2:12 PM/işlemi başarıyla tamamladı" diyor.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate5m.png)

4. Güncelleştirmeleri tarandıktan sonra **Güncelleştirmeleri indir**’e tıklayın. 

    ![Cihaz güncelleştirmeleri bölmesi "yeni güncelleştirmeler var" ve "son tarama/11/3/2016 2:12 PM/yazılım sürümü/10.0.10280.0" diyor. Sürüm vurgulanır.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate6m.png)

5. **Yeni güncelleştirmeler** dikey penceresinde güncelleştirmeler indirildikten sonra bilgileri gözden geçirin; yüklemeyi onaylamanız gerekir. **Tamam**'a tıklayın.

    ![Yeni güncelleştirmeler bölmesi "Güncelleştirmeler indirildikten sonra, yüklemeyi onaylamanız gerekecektir" ifadesini görürsünüz. Tamam düğmesi vurgulanır.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate7m.png)

6. Karşıya yükleme başlayıp başarıyla tamamlandığında size bildirilir.

     !["Cihaz, Mysssıs için güncelleştirmeleri Indirme... 2:13 PM ".](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate8m.png)

5. **Cihaz güncelleştirmeleri** dikey penceresinde **Yükle**’ye tıklayın.

     ![Cihaz güncelleştirmeleri bölmesinde "güncelleştirmeleri yüklemeyi Onayla" iletisi görüntülenir. Install düğmesi vurgulanır.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate11m.png)   

6. **Yeni güncelleştirmeler** dikey penceresinde güncelleştirmenin kesintiye uğratacağı konusunda uyarı alırsınız. Sanal dizi tek düğümlü bir cihaz olduğundan, cihaz güncelleştirildikten sonra yeniden başlatılır. Bu işlem devam eden G/Ç işlemlerini kesintiye uğratır. Güncelleştirmeleri yüklemek için **Tamam**’a tıklayın. 

    ![Yeni güncelleştirmeler bölmesindeki ileti "Bu güncelleştirmeler yüklenirken cihazınız kapalı kalma süresine sahip olacaktır". Tamam düğmesi vurgulanır.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate12m.png) 

7. Yükleme işi başladığında size bildirilir. 

    ![Bildirim "Device MYSSIS1103 için güncelleştirmeleri yükleme" diyor. 2:15 PM.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate13m.png)

8.  Yükleme işi başarıyla tamamlandıktan sonra yüklemeyi izlemek için **Cihaz güncelleştirmeleri** dikey penceresindeki **İşi Görüntüle** bağlantısına tıklayın. 

    ![Cihaz güncelleştirmeleri bölmesinde bağlantı etiketleyen "Güncelleştirmeler yükleniyor. Işi görüntüle "vurgulanacaktır.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate15m.png)

    Bu işlem sizi **Güncelleştirmeleri Yükle** dikey penceresine götürür. İşle ilgili ayrıntılı bilgileri burada görüntüleyebilirsiniz.

    ![Güncelleştirmeleri yükleme bölmesinde cihaz, durum, süre, başlangıç saati ve bitiş zamanı gibi yükleme bilgileri bulunur.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate16m.png)

9. Güncelleştirmeler başarıyla yüklendikten sonra Cihaz güncelleştirmeleri dikey penceresinde bununla ilgili bir ileti görürsünüz. Yazılım sürümü de **10.0.10288.0** olarak değişir. 

    ![Cihaz güncelleştirmeleri bölmesi, "cihazınızın güncel olduğunu" gösterir ve yazılım sürümü (10.0.10288.0) sağlar.](../includes/media/storsimple-virtual-array-install-update-via-portal/azupdate17m.png)
