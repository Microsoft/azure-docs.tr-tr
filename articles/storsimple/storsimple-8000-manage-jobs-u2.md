---
title: StorSimple 8000 serisi için işleri görüntüleme ve yönetme | Microsoft Docs
description: StorSimple Device Manager hizmet Işleri dikey penceresini ve en son, geçerli ve zamanlanmış yedekleme işlerini izlemek için nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: dc630021cbf93074119d4feedcb3a09fae1453e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91949437"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>İşleri görüntülemek ve yönetmek için StorSimple Device Manager hizmetini kullanın (güncelleştirme 3 ve üstü)

## <a name="overview"></a>Genel Bakış
**İşler** dikey penceresi, storsimple Device Manager hizmetinize bağlı cihazlarda başlatılan işleri görüntülemek ve yönetmek için tek bir merkezi Portal sağlar. Birden çok cihaz için zamanlanmış, çalışan, tamamlanmış, iptal edilmiş ve başarısız işleri görüntüleyebilirsiniz. Sonuçlar tablolu biçimde görüntülenir.

![İşler dikey penceresi](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

İlgilendiğiniz işleri, şu gibi alanlarda filtreleyerek hızlı bir şekilde bulabilirsiniz:

* **Durum** – işler devam ediyor, başarılı, iptal edilebilir, başarısız, iptal edilebilir veya hatalarla başarılı olabilir.
* **Zaman aralığı** : işler tarih ve saat aralığına göre filtrelenebilir. Aralıklar 1 saat, son 24 saat, son 7 gün, son 30 gün, geçmiş yıl veya özel tarih.
* **Tür** – iş türü zamanlanmış yedekleme, el ile yedekleme, yedekleme, yedekleme, birim kopyalama, birim kapsayıcılarının yükünü devretme, yerel olarak sabitlenmiş birim oluşturma, birim değiştirme, güncelleştirme yükleme, destek günlükleri toplama ve bulut gereci oluşturma olabilir.
* **Cihazlar** – işler hizmetinize bağlı belirli bir cihazda başlatılır.
  
Filtrelenmiş işler daha sonra aşağıdaki özniteliklere göre tablo halinde ayarlanır:
  
* **Ad** – zamanlanmış yedekleme, el ile yedekleme, geri yükleme yedekleme, birim kopyalama, birim kapsayıcılarının yükünü devretme, yerel olarak sabitlenmiş birim oluşturma, birim değiştirme, güncelleştirme yükleme, destek günlükleri toplama veya bulut gereci oluşturma.
* **Durum** – çalışıyor, tamamlandı, iptal edildi, başarısız, iptal edildi veya hatalarla tamamlandı.
* **Varlık** – işler bir birimle, bir yedekleme ilkesiyle veya bir cihazla ilişkilendirilebilir. Örneğin, bir kopyalama işi bir birimle ilişkilendirilir, ancak zamanlanan bir yedekleme işi bir yedekleme ilkesiyle ilişkilendirilir. Bir cihaz işi, olağanüstü durum kurtarma (DR) veya geri yükleme işlemi sonucu olarak oluşturulur.
* **Device** : işin başlatıldığı cihazın adı.
* **Başlangıç tarihi** – işin başlatıldığı zaman.
* **Süre** : işi tamamlaması için gereken süre.

İşlerin listesi, her 30 saniyede bir yenilenir.

Bu sayfada işle ilgili aşağıdaki işlemleri gerçekleştirebilirsiniz:

* İş ayrıntılarını görüntüleme
* Bir işi iptal etme

## <a name="view-job-details"></a>İş ayrıntılarını görüntüleme
Herhangi bir işin ayrıntılarını görüntülemek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-view-job-details"></a>İş ayrıntılarını görüntülemek için
1. StorSimple Device Manager hizmetinize gidin ve ardından **işler**' e tıklayın.

2. **İşler** dikey penceresinde ilgilendiğiniz iş (ler) i uygun filtrelerle bir sorgu çalıştırarak görüntüleyin. Tamamlanmış, çalışan veya iptal edilmiş işleri arayabilirsiniz.

    ![İş dikey penceresi 2](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Bir işi seçin ve tıklayın.

    ![İş dikey penceresi 3](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. İş ayrıntıları dikey penceresinde durumu, ayrıntıları, zaman istatistiklerini ve veri istatistiklerini görüntüleyebilirsiniz.
   
    ![İş ayrıntıları](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Bir işi iptal etme
Çalışan bir işi iptal etmek için aşağıdaki adımları gerçekleştirin.

> [!NOTE]
> Birim türünü değiştirmek veya bir birimi genişletmek için bir birimi değiştirme gibi bazı işler iptal edilemez.


### <a name="to-cancel-a-job"></a>Bir işi iptal etmek için
1. **İşler** sayfasında, uygun filtrelerle bir sorgu çalıştırarak iptal etmek istediğiniz çalışan işleri görüntüleyin. İşi seçin.

2. Seçili işe sağ tıklayıp bağlam menüsünü çağırın ve **iptal**' e tıklayın.

    ![İş ayrıntıları 2](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Onayınız istendiğinde **Evet**’e tıklayın. Bu iş iptal edildi.

## <a name="next-steps"></a>Sonraki adımlar
* [StorSimple yedekleme ilkelerinizi yönetmeyi](storsimple-8000-manage-backup-policies-u2.md)öğrenin.
* StorSimple [cihazınızı yönetmek Için storsimple Device Manager hizmetini nasıl kullanacağınızı](storsimple-8000-manager-service-administration.md)öğrenin.

