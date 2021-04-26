---
title: StorSimple 8000 serisi yedekleme ilkelerini yönetme | Microsoft Docs
description: StorSimple Device Manager hizmetini kullanarak bir StorSimple 8000 serisi cihazında el ile yedeklemeler, yedekleme zamanlamaları ve yedekleme bekletme oluşturmak ve yönetmek için nasıl kullanabileceğinizi açıklar.
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
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 9ec2bbef88ed185c3927676ad2596269783d1850
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85515626"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Yedekleme ilkelerini yönetmek için Azure portal StorSimple Device Manager hizmetini kullanma


## <a name="overview"></a>Genel Bakış

Bu öğreticide, StorSimple Device Manager hizmeti **yedekleme ilkesi** dikey penceresinin, StorSimple birimleriniz için Yedekleme süreçlerini ve yedekleme bekletmesini denetlemek üzere nasıl kullanılacağı açıklanmaktadır. Ayrıca, el ile yedeklemenin nasıl tamamlandığını da açıklar.

Bir birimi yedeklerken, yerel bir anlık görüntü veya bulut anlık görüntüsü oluşturmayı tercih edebilirsiniz. Yerel olarak sabitlenmiş bir birimi yedekliyorsanız, bir bulut anlık görüntüsü belirtmenizi öneririz. Çok sayıda dalgalanma sahip bir veri kümesiyle bağlanmış yerel olarak sabitlenmiş bir birimin çok sayıda yerel anlık görüntüsünü almak, yerel alandan hızlı bir şekilde çalışma yapmak için gereken bir durumla sonuçlanır. Yerel anlık görüntüleri al seçeneğini belirlerseniz, en son durumu yedeklemek için daha az günlük anlık görüntü kullanmanızı, bunları bir gün boyunca saklamayı ve sonra silmeyi öneririz.

Yerel olarak sabitlenmiş bir birimin bulut anlık görüntüsünü aldığınızda, yalnızca değiştirilen verileri buluta kopyalar, burada yinelenenleri kaldırılmış ve sıkıştırılır.

## <a name="the-backup-policy-blade"></a>Yedekleme ilkesi dikey penceresi

StorSimple cihazınız için **yedekleme ilkesi** dikey penceresi, yedekleme ilkelerini yönetmenizi ve yerel ve bulut anlık görüntülerini zamanlamanıza izin verir. Yedekleme ilkeleri, bir birim koleksiyonu için yedekleme zamanlamalarını ve yedekleme bekletmesini yapılandırmak üzere kullanılır. Yedekleme ilkeleri, birden fazla birimin aynı anda anlık görüntüsünü alma olanağı sağlar. Bu, bir yedekleme ilkesi tarafından oluşturulan yedeklemelerin kilitlenmeyle tutarlı kopyalar olacağı anlamına gelir.

Tablo listesi yedekleme ilkeleri, mevcut yedekleme ilkelerini aşağıdaki alanlardan bir veya daha fazlasına göre filtrelemenize de olanak tanır:

* **İlke adı** – ilkeyle ilişkili ad. Farklı ilke türleri şunlardır:

  * Kullanıcı tarafından açıkça oluşturulan zamanlanmış ilkeler.
  * İlk olarak StorSimple Snapshot Manager oluşturulan içeri aktarılan ilkeler. Bunlar, ilkelerin içeri aktarıldığı StorSimple Snapshot Manager konağını tanımlayan bir etikettir.

  > [!NOTE]
  > Toplu oluşturma sırasında otomatik veya varsayılan yedekleme ilkeleri artık etkin değildir.

* **Son başarılı yedekleme** – bu ilkeyle gerçekleştirilen son başarılı yedeklemenin tarihi ve saati.

* **Sonraki yedekleme** : Bu ilke tarafından başlatılacak bir sonraki zamanlanmış yedeklemenin tarihi ve saati.

* **Birimler** : ilkeyle ilişkili birimler. Yedekleme ilkesiyle ilişkili tüm birimler, yedeklemeler oluşturulduğunda birlikte gruplandırılır.

* **Zamanlamalar** : yedekleme ilkesiyle ilişkili zamanlamaların sayısı.

Yedekleme ilkeleri için gerçekleştirebileceğiniz sık kullanılan işlemler şunlardır:

* Yedekleme ilkesi ekleme
* Zamanlama ekleme veya değiştirme
* Birim ekleme veya kaldırma
* Yedekleme ilkesini silme
* El ile yedekleme yapın

## <a name="add-a-backup-policy"></a>Yedekleme ilkesi ekleme

Yedeklemelerinizi otomatik olarak zamanlamak için bir yedekleme ilkesi ekleyin. Bir birimi ilk oluşturduğunuzda, biriminiz ile ilişkili bir varsayılan yedekleme ilkesi yoktur. Birim verilerini korumak için bir yedekleme ilkesi eklemeniz ve atamanız gerekir.

StorSimple cihazınız için bir yedekleme ilkesi eklemek üzere Azure portal aşağıdaki adımları gerçekleştirin. İlkeyi ekledikten sonra bir zamanlama tanımlayabilirsiniz (bkz. [bir zamanlama ekleme veya değiştirme](#add-or-modify-a-schedule)).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Zamanlama ekleme veya değiştirme

StorSimple cihazınızda var olan bir yedekleme ilkesine eklenmiş bir zamanlama ekleyebilir veya düzenleyebilirsiniz. Bir zamanlamayı eklemek veya değiştirmek için Azure portal aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Birim ekleme veya kaldırma

StorSimple cihazınızda bir yedekleme ilkesine atanan bir birim ekleyebilir veya kaldırabilirsiniz. Birim eklemek veya kaldırmak için Azure portal aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Yedekleme ilkesini silme

StorSimple cihazınızda bir yedekleme ilkesini silmek için Azure portal aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>El ile yedekleme yapın

Tek bir birim için isteğe bağlı (el ile) yedekleme oluşturmak için Azure portal aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Sonraki adımlar

StorSimple [cihazınızı yönetmek Için storsimple Device Manager hizmetini kullanma](storsimple-8000-manager-service-administration.md)hakkında daha fazla bilgi edinin.

