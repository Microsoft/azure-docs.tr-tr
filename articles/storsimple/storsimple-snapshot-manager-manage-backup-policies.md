---
title: StorSimple Snapshot Manager yedekleme ilkeleri | Microsoft Docs
description: Zamanlanmış yedeklemeleri denetleyen yedekleme ilkelerini oluşturmak ve yönetmek için StorSimple Snapshot Manager MMC ek bileşeninin nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 745aeb9844f1abad075d34b34735563200ea618b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90054951"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Yedekleme ilkeleri oluşturmak ve yönetmek için StorSimple Snapshot Manager kullanma
## <a name="overview"></a>Genel Bakış
Yedekleme ilkesi birim verilerini yerel olarak veya bulutta yedeklemeye yönelik bir zamanlama oluşturur. Bir yedekleme ilkesi oluşturduğunuzda, bir bekletme ilkesi de belirtebilirsiniz. (En fazla 64 anlık görüntü tutabilirsiniz.) Yedekleme ilkeleri hakkında daha fazla bilgi için bkz. [StorSimple 8000 serisinde yedekleme türleri: karma bulut çözümü](storsimple-overview.md). [](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies)

Bu öğreticide, aşağıdaki işlemlerin nasıl yapılacağı açıklanmaktadır:

* Yedekleme ilkesi oluşturma
* Yedekleme ilkesini düzenleme
* Yedekleme ilkesini silme

## <a name="create-a-backup-policy"></a>Yedekleme ilkesi oluşturma
Yeni bir yedekleme ilkesi oluşturmak için aşağıdaki yordamı kullanın.

#### <a name="to-create-a-backup-policy"></a>Bir yedekleme ilkesi oluşturmak için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde, **yedekleme ilkeleri**' ne sağ tıklayın ve **yedekleme İlkesi Oluştur**' a tıklayın.

    ![Yedekleme ilkesi oluşturma](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    **Ilke oluştur** iletişim kutusu görüntülenir.

    ![Ilke oluşturma-Genel sekmesi](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. **Genel** sekmesinde, aşağıdaki bilgileri doldurun:

   1. **Ad** metin kutusuna ilke için bir ad yazın.
   2. **Birim grubu** metin kutusuna ilkeyle ilişkili birim grubunun adını yazın.
   3. **Yerel anlık görüntü** veya **bulut anlık görüntüsü** seçin.
   4. Tutulacak anlık görüntü sayısını seçin. **Tümü**' nü seçerseniz, 64 anlık görüntü korunur (maksimum).
4. **Zamanlama** sekmesine tıklayın.

    ![Ilke oluşturma-Zamanlama sekmesi](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. **Zamanla** sekmesinde, aşağıdaki bilgileri doldurun:

   1. Sonraki yedeklemeyi zamanlamak için **Etkinleştir** onay kutusuna tıklayın.
   2. **Ayarlar** altında **bir kez**, **günlük**, **haftalık** veya **aylık**' ı seçin.
   3. **Başlat** metin kutusunda Takvim simgesine tıklayın ve bir başlangıç tarihi seçin.
   4. **Gelişmiş ayarlar** altında, isteğe bağlı yineleme zamanlamaları ve bitiş tarihi ayarlayabilirsiniz.
   5. **Tamam**'a tıklayın.

Bir yedekleme ilkesi oluşturduktan sonra, **sonuçlar** bölmesinde aşağıdaki bilgiler görüntülenir:

* **Ad** : yedekleme ilkesinin adı.
* **Tür** – yerel anlık görüntü veya bulut anlık görüntüsü.
* **Birim grubu** – ilkeyle ilişkili birim grubu.
* **Bekletme** : korunan anlık görüntü sayısı; maksimum değer 64 ' dir.
* **Oluşturuldu** : Bu ilkenin oluşturulduğu tarih.
* **Enabled** – ilkenin Şu anda etkin olup olmadığını belirtir: **true** , geçerli olduğunu gösterir; **False** , etkin olmadığını gösterir.

## <a name="edit-a-backup-policy"></a>Yedekleme ilkesini düzenleme
Mevcut bir yedekleme ilkesini düzenlemek için aşağıdaki yordamı kullanın.

#### <a name="to-edit-a-backup-policy"></a>Bir yedekleme ilkesini düzenlemek için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde, **yedekleme ilkeleri** düğümüne tıklayın. Tüm yedekleme ilkeleri **sonuçlar** bölmesinde görünür.
3. Düzenlemek istediğiniz ilkeye sağ tıklayın ve ardından **Düzenle**' ye tıklayın.

    ![Yedekleme ilkesini düzenleme](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. **Ilke oluştur** penceresi göründüğünde, değişikliklerinizi girin ve ardından **Tamam**' a tıklayın.

## <a name="delete-a-backup-policy"></a>Yedekleme ilkesini silme
Bir yedekleme ilkesini silmek için aşağıdaki yordamı kullanın.

#### <a name="to-delete-a-backup-policy"></a>Bir yedekleme ilkesini silmek için
1. StorSimple Snapshot Manager başlatmak için masaüstü simgesine tıklayın.
2. **Kapsam** bölmesinde, **yedekleme ilkeleri** düğümüne tıklayın. Tüm yedekleme ilkeleri **sonuçlar** bölmesinde görünür.
3. Silmek istediğiniz yedekleme ilkesini sağ tıklatın ve ardından **Sil**' e tıklayın.
4. Onay iletisi göründüğünde **Evet**' e tıklayın.

    ![Yedekleme ilkesi onayını silme](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Sonraki adımlar
* StorSimple [çözümünüzü yönetmek Için storsimple Snapshot Manager](storsimple-snapshot-manager-admin.md)nasıl kullanacağınızı öğrenin.
* [StorSimple Snapshot Manager kullanarak yedekleme işlerini görüntüleme ve yönetme](storsimple-snapshot-manager-manage-backup-jobs.md)hakkında bilgi edinin.
