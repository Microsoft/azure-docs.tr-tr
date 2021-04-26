---
title: StorSimple Snapshot Manager birim grupları | Microsoft Docs
description: Toplu grupları oluşturmak ve yönetmek için StorSimple Snapshot Manager MMC ek bileşeninin nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: df2d74701e21c3773d96564f1b06d80ddb9fce9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98209228"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Toplu grupları oluşturmak ve yönetmek için StorSimple Snapshot Manager kullanma
## <a name="overview"></a>Genel Bakış
Birim gruplarına birimler atamak, birim grubu hakkındaki bilgileri görüntülemek, yedeklemeleri zamanlamak ve birim gruplarını düzenlemek için **kapsam** bölmesindeki **Birim grupları** düğümünü kullanabilirsiniz.

Birim grupları, yedeklemelerin uygulamayla tutarlı olmasını sağlamak için kullanılan ilgili birimlerin havuzlarıdır. Daha fazla bilgi için bkz. [birimler ve birim grupları](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) ve [Windows birim gölge kopyası hizmeti ile tümleştirme](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Bir birim grubundaki tüm birimlerin tek bir bulut hizmeti sağlayıcısından gelmesi gerekir.
> * Birim gruplarını yapılandırırken, Küme Paylaşılan birimlerini (CSV) ve CSV 'leri aynı birim grubunda karıştırmayın. StorSimple Snapshot Manager, aynı anlık görüntüde CSVs ve CSV olmayan bir karışımı desteklemez.

![Birim grupları düğümü](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Şekil 1: StorSimple Snapshot Manager birim grupları düğümü** 

Bu öğreticide, StorSimple Snapshot Manager kullanarak şunları yapabilirsiniz:

* Birim gruplarınız hakkındaki bilgileri görüntüleme
* Birim grubu oluşturma
* Birim grubunu yedekleme
* Birim grubunu düzenleme
* Birim grubunu silme

Bu eylemlerin tümü, **Eylemler** bölmesinde de kullanılabilir.

## <a name="view-volume-groups"></a>Birim gruplarını görüntüle
**Birim grupları** düğümüne tıklarsanız, **sonuçlar** bölmesi yaptığınız sütun seçimlerine bağlı olarak her bir birim grubuyla ilgili aşağıdaki bilgileri gösterir. ( **Sonuçlar** bölmesindeki sütunlar yapılandırılabilir. **Birimler** düğümüne sağ tıklayın, **Görünüm**' ü seçin ve ardından **sütun Ekle/Kaldır**' ı seçin.)

| Sonuç sütunu | Açıklama |
|:--- |:--- |
| Ad |**Ad** sütunu, birim grubunun adını içerir. |
| Uygulama |**Uygulamalar** sütununda, Windows konakta yüklü olan ve ÇALıŞMAKTA olan VSS yazıcılarının sayısı gösterilir. |
| Seçili |**Seçili** sütun, birim grubunda bulunan birim sayısını gösterir. Sıfır (0), birim grubundaki birimlerle hiçbir uygulamanın ilişkilendirilmediğini belirtir. |
| Alındıysa |**Içeri aktarılan** sütun, içeri aktarılan birim sayısını gösterir. **True** olarak ayarlandığında, bu sütun bir birim grubunun Azure Portal içeri aktarıldığını ve storsimple Snapshot Manager içinde oluşturulmadığını gösterir. |

> [!NOTE]
> StorSimple Snapshot Manager birim grupları ayrıca Azure portal **yedekleme ilkeleri** sekmesinde görüntülenir.
> 
> 

## <a name="create-a-volume-group"></a>Birim grubu oluşturma
Bir birim grubu oluşturmak için aşağıdaki yordamı kullanın.

#### <a name="to-create-a-volume-group"></a>Birim grubu oluşturmak için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde, **Birim grupları**' na sağ tıklayın ve ardından **birim grubu oluştur**' a tıklayın.
   
    ![Birim grubu oluştur](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    **Birim grubu oluştur** iletişim kutusu görüntülenir.
   
    ![Birim grubu oluştur iletişim kutusu](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Aşağıdaki bilgileri girin:
   
   1. **Ad** kutusuna yeni birim grubu için benzersiz bir ad yazın.
   2. **Uygulamalar** kutusunda, birim grubuna ekleyeceğiniz birimlerle ilişkili uygulamalar ' ı seçin.
      
       **Uygulamalar** kutusu yalnızca StorSimple birimlerini kullanan ve VSS yazıcılarının kendileri için etkinleştirildiği uygulamaları listeler. Bir VSS yazıcısı yalnızca yazıcının farkında olduğu tüm birimler StorSimple birimleridir etkindir. Uygulamalar kutusu boşsa, Azure StorSimple birimlerini kullanan ve desteklenen VSS yazıcılarını içeren hiçbir uygulama yüklenir. (Şu anda Azure StorSimple, Microsoft Exchange ve SQL Server destekler.) VSS yazıcıları hakkında daha fazla bilgi için bkz. [Windows birim gölge kopyası hizmeti Ile tümleştirme](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Bir uygulama seçerseniz, onunla ilişkili tüm birimler otomatik olarak seçilir. Buna karşılık, belirli bir uygulamayla ilişkili birimleri seçerseniz uygulama, **uygulamalar** kutusunda otomatik olarak seçilir. 
   3. **Birimler** kutusunda, birim grubuna eklemek Için StorSimple birimleri ' ni seçin. 
      
      * Tek veya birden çok bölüm içeren birimler dahil edebilirsiniz. (Birden çok bölüm birimi dinamik diskler veya birden çok bölümlü temel diskler olabilir.) Birden çok bölüm içeren bir birim tek bir birim olarak değerlendirilir. Sonuç olarak, bölümlerden yalnızca birini bir birim grubuna eklerseniz, diğer tüm bölümler aynı anda o birim grubuna otomatik olarak eklenir. Bir birim grubuna birden çok bölüm birimi ekledikten sonra, birden çok bölüm birimi tek bir birim olarak değerlendirilmeye devam eder.
      * Bunlara herhangi bir birim atamaktan, boş birim grupları oluşturabilirsiniz. 
      * Küme Paylaşılan birimlerini (CSV) ve CSV 'yi, aynı birim grubunda karıştırmayın. StorSimple Snapshot Manager, aynı anlık görüntüde CSV birimlerinin ve CSV olmayan birimlerin bir karışımını desteklemez.
4. Birim grubunu kaydetmek için **Tamam** ' ı tıklatın.

## <a name="back-up-a-volume-group"></a>Birim grubunu yedekleme
Bir birim grubunu yedeklemek için aşağıdaki yordamı kullanın.

#### <a name="to-back-up-a-volume-group"></a>Bir birim grubunu yedeklemek için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde, **Birim grupları** düğümünü genişletin, bir birim grubu adına sağ tıklayın ve ardından **yedeklemeyi al**' a tıklayın.
   
    ![Birim grubunu hemen Yedekle](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. **Yedeği al** Iletişim kutusunda **Yerel anlık görüntü** veya **bulut anlık görüntüsü**' nü seçin ve ardından **Oluştur**' a tıklayın.
   
    ![Yedekleme Al iletişim kutusu](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Yedeğin çalıştığını doğrulamak için **işler** düğümünü genişletin ve ardından **çalışıyor**' a tıklayın. Yedeklemenin listelenmesi gerekir.
5. Tamamlanan anlık görüntüyü görüntülemek için, **Yedekleme kataloğu** düğümünü genişletin, birim grubu adını genişletin ve ardından **Yerel anlık görüntü** veya **bulut anlık görüntüsü**' ne tıklayın. Yedekleme başarıyla tamamlandıysa listelenecektir.

## <a name="edit-a-volume-group"></a>Birim grubunu düzenleme
Bir birim grubunu düzenlemek için aşağıdaki yordamı kullanın.

#### <a name="to-edit-a-volume-group"></a>Bir birim grubunu düzenlemek için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde, **Birim grupları** düğümünü genişletin, bir birim grubu adına sağ tıklayın ve ardından **Düzenle**' ye tıklayın.
3. **Birim grubu oluştur** iletişim kutusu görüntülenir. **Ad**, **uygulama** ve **birim** girdilerini değiştirebilirsiniz.
4. Değişikliklerinizi kaydetmek için **Tamam**’a tıklayın.

## <a name="delete-a-volume-group"></a>Birim grubunu silme
Bir birim grubunu silmek için aşağıdaki yordamı kullanın. 

> [!WARNING]
> Bu, birim grubuyla ilişkili tüm yedeklemeleri de siler.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Bir birim grubunu silmek için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde, **Birim grupları** düğümünü genişletin, bir birim grubu adına sağ tıklayın ve ardından **Sil**' e tıklayın.
3. **Birim grubunu sil** iletişim kutusu görüntülenir. Metin kutusuna **onaylayın** yazın ve ardından **Tamam**' a tıklayın.
   
    Silinen birim grubu, **sonuçlar** bölmesindeki listeden ve bu birim grubuyla ilişkili tüm yedeklemeler yedekleme kataloğundan silinir.

## <a name="next-steps"></a>Sonraki adımlar
* StorSimple [çözümünüzü yönetmek Için storsimple Snapshot Manager](storsimple-snapshot-manager-admin.md)nasıl kullanacağınızı öğrenin.
* [StorSimple Snapshot Manager kullanarak yedekleme ilkeleri oluşturma ve yönetme](storsimple-snapshot-manager-manage-backup-policies.md)hakkında bilgi edinin.

