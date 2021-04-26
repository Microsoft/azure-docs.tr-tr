---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 23ce17844a0113f63931c6ece7d36bfefedc2de5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100551875"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Bir StorSimple yedekleme ilkesi eklemek için

1. StorSimple cihazınıza gidin ve **Yedekleme ilkesi**’ne tıklayın.

2. **Yedekleme ilkesi** dikey penceresinde komut çubuğundan **+ İlke ekle**’ye tıklayın.
   
    ![Yedekleme ilkesi ekleme](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-01.png)

3. **Yedekleme ilkesi oluştur** dikey penceresinde, aşağıdaki adımları uygulayın:
   
   1. **Cihazı seçin** alanı, seçtiğiniz cihaza göre otomatik olarak doldurulur.
   
   2. 3 ile 150 arasında karakter içeren bir yedekleme **ilkesi adı** belirtin. İlke oluşturulduktan sonra ilkeyi yeniden adlandıramazsınız.
       
   3. Bu yedekleme ilkesine birim atamak için **Birim ekle**’yi seçin ve ardından birimlerin tablosal listesinde, bu yedekleme ilkesine bir veya daha fazla birim atamak için onay kutularına tıklayın.

       ![Yedekleme ilkesi ekleme 2](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-02.png)<!--Replacement screen source: create-backup-policy-addvolumes.png-->

   4. Bu yedekleme ilkesi için bir zamanlama tanımlamak için **İlk zamanlama**’ya tıklayın ve aşağıdaki parametreleri değiştirin:<!--Do the substeps remain the same? Can they follow without a screenshot?-->

       ![Yedekleme ilkesi ekleme 3](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-03.png)<!--Replacement screen source: create-backup-policy-first-schedule.png-->

       1. **Anlık görüntü türü** için **Bulut** veya **Yerel**’i seçin.

       2. Yedekleme sıklığını belirtin (bir sayı belirtip açılan listeden **Gün** veya **Hafta** öğesini seçin).

       3. Bir elde tutma zamanlaması girin.

       4. Yedekleme ilkesinin başlayacağı saat ve tarihi girin.

       5. Zamanlamayı tanımlamak için **Tamam**’a tıklayın.

   5. Yedekleme ilkesi oluşturmak için **Oluştur**’a tıklayın.
   
   6. Yedekleme ilkesi oluşturulduğunda size bildirilir. Yeni eklenen ilke, **Yedekleme İlkeleri** dikey penceresinin tablosal görünümünde gösterilir.

       ![Yedekleme ilkesi ekleme 5](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-07.png)
