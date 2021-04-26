---
title: StorSimple Snapshot Manager yedekleme işleri | Microsoft Docs
description: Zamanlanan, çalışmakta olan ve tamamlanan yedekleme işlerini görüntülemek ve yönetmek için StorSimple Snapshot Manager MMC ek bileşeninin nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 3c26a84e32a17cba83b5ca895f146e561072fa62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998201"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Yedekleme işlerini görüntülemek ve yönetmek için StorSimple Snapshot Manager kullanma

## <a name="overview"></a>Genel Bakış
**Kapsam** bölmesindeki **Işler** düğümü, **Zamanlanmış**, **son 24 saati** ve etkileşimli olarak veya yapılandırılmış bir ilke ile **başlatılan yedekleme görevlerini** gösterir. 

Bu öğreticide, zamanlanan, en son ve çalışmakta olan yedekleme işleri hakkındaki bilgileri göstermek için **işler** düğümünü nasıl kullanabileceğiniz açıklanmaktadır. (İşlerin listesi ve ilgili bilgiler **sonuçlar** bölmesinde görünür.) Ayrıca, listelenen bir işe sağ tıklayıp kullanılabilir eylemleri listeleyen bir bağlam menüsü görebilirsiniz.

## <a name="view-scheduled-jobs"></a>Zamanlanan işleri görüntüleme
Zamanlanmış yedekleme işlerini görüntülemek için aşağıdaki yordamı kullanın.

#### <a name="to-view-scheduled-jobs"></a>Zamanlanan işleri görüntülemek için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın. 
2. **Kapsam** bölmesinde, **işler** düğümünü genişletin ve **zamanlanan**' ye tıklayın. **Sonuçlar** bölmesinde aşağıdaki bilgiler görüntülenir:
   
   * **Ad** : zamanlanmış anlık görüntünün adı
   * **Sonraki çalıştırma** : bir sonraki zamanlanmış anlık görüntünün tarihi ve saati
   * **Son çalıştırma** : en son zamanlanmış anlık görüntünün tarihi ve saati
     
     > [!NOTE]
     > Yalnızca bir kerelik anlık görüntüler için, bir **sonraki çalıştırma** ve **son çalıştırma** aynı olacaktır.
     
     ![Zamanlanmış yedekleme işleri](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Belirli bir iş üzerinde ek eylemler gerçekleştirmek için, **sonuçlar** bölmesinde iş adına sağ tıklayın ve menü seçeneklerinden seçim yapın.

## <a name="view-recent-jobs"></a>Son işleri görüntüle
Son 24 saat içinde tamamlanan yedekleme ve geri yükleme işlerini görüntülemek için aşağıdaki yordamı kullanın.

#### <a name="to-view-recent-jobs"></a>Son işleri görüntülemek için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde, **işler** düğümünü genişletin ve **son 24 saat**' e tıklayın. **Sonuçlar** bölmesinde, son 24 saat (en fazla 64 iş için) yedekleme işleri gösterilmektedir. Aşağıdaki bilgiler, belirttiğiniz **Görünüm** seçeneklerine bağlı olarak **sonuçlar** bölmesinde görünür:
   
   * **Name** : zamanlanmış anlık görüntünün adı.
   * **Started** : anlık görüntünün başladığı tarih ve saat.
   * **Durduruldu** : anlık görüntünün bittiği veya sonlandırıldığı tarih ve saat.
   * **Geçti** – **Başlangıç** ve **durdurulma** zamanları arasındaki süre miktarı.
   * **Durum** : son tamamlanan işin durumu. **Başarılı** , yedeklemenin başarıyla oluşturulduğunu gösteriyor. **Başarısız oldu** , işin başarıyla çalıştırılmadığını gösterir.
   * **Bilgi** : hatanın nedeni.
   * **Işlenen bayt (MB)** – işlenen birim grubundaki veri miktarı (MB cinsinden). 
     
     ![Son 24 saat içinde çalıştırılan işler](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Belirli bir iş üzerinde ek eylemler gerçekleştirmek için, **sonuçlar** bölmesinde iş adına sağ tıklayın ve menü seçeneklerinden seçim yapın.
   
    ![İşi silme](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Çalışmakta olan işleri görüntüle
Şu anda çalışmakta olan işleri görüntülemek için aşağıdaki yordamı kullanın.

#### <a name="to-view-currently-running-jobs"></a>Çalışmakta olan işleri görüntülemek için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde, **işler** düğümünü genişletin ve **çalışıyor**' a tıklayın. Belirttiğiniz **Görünüm** seçeneklerine bağlı olarak, **sonuçlar** bölmesinde aşağıdaki bilgiler görüntülenir:
   
   * **Name** : zamanlanmış anlık görüntünün adı.
   * **Started** : anlık görüntünün başladığı tarih ve saat.
   * **Checkpoint** : yedeklemenin geçerli eylemi.
   * **Durum** : tamamlanma yüzdesi.
   * **Geçen** : yedeklemenin başlamasından bu yana geçen süre. 
   * **Ortalama verimlilik (MB)** – işlenen toplam veri baytı, işlenmek üzere harcanan toplam süre (MB) ile oranıdır.
   * **Işlenen bayt (MB)** – işlenen toplam veri baytı (MB cinsinden).
   * **Yazılan bayt (MB)** – yazılan toplam veri baytı (MB cinsinden). Verilerin yanı sıra meta verileri de içerir ve bu nedenle genellikle Işlenen bayttan daha büyüktür.
     
     ![Şu anda çalışan işler](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Belirli bir iş üzerinde ek eylemler gerçekleştirmek için, **sonuçlar** bölmesinde iş adına sağ tıklayın ve menü seçeneklerinden seçim yapın.

## <a name="next-steps"></a>Sonraki adımlar
* StorSimple [çözümünüzü yönetmek Için storsimple Snapshot Manager](storsimple-snapshot-manager-admin.md)nasıl kullanacağınızı öğrenin.
* [StorSimple Snapshot Manager kullanarak yedekleme kataloğunu yönetme](storsimple-snapshot-manager-manage-backup-catalog.md)hakkında bilgi edinin.

