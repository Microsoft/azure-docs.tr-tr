---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 71f18cf8448060385ea38be9b2719b1ed545c5d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545427"
---
> [!NOTE] 
> Bir birim kapsayıcısıyla ilişkili şifreleme ayarlarını ve depolama hesabı kimlik bilgilerini, oluşturulduktan sonra değiştiremezsiniz.

#### <a name="to-modify-a-volume-container"></a>Bir birim kapsayıcısını değiştirmek için

1. StorSimple Device Manager hizmetinize gidin ve sonra **yönetim > birim kapsayıcıları**' na gidin.

2. Birim kapsayıcılarının tablolu listesinden, değiştirmek istediğiniz birim kapsayıcısını seçin. **Cihazlar** sayfasında, cihazı seçin, çift tıklayın ve ardından **birim kapsayıcıları** sekmesine tıklayın.

3. Birim kapsayıcılarının tablosal listesinde, değiştirmek istediğiniz birim kapsayıcısını seçin. Açılan dikey pencerede, komut çubuğundan **Değiştir** ' e tıklayın.

    ![Birim kapsayıcısını Değiştir](./media/storsimple-8000-modify-volume-container/modify-volume-container-01.png)

4. **Birim kapsayıcısını Değiştir** dikey penceresinde aşağıdaki adımları uygulayın:
   
   1. Birim kapsayıcısıyla ilişkili ad, şifreleme anahtarı ve depolama hesabı, belirtileden sonra değiştirilemez. İlişkili bant genişliği ayarını değiştirin.<!--STEPS NEED WORK. Updated screen doesn't show alternative to Unlimited or subsequent steps if they customize bandwidth. Can we talk them through this (briefly)?-->
      
       ![Bant genişliği ayarını değiştir](./media/storsimple-8000-modify-volume-container/modify-volume-container-02.png)<!--New graphic based on: modify-volume-container-bw-setting.png-->

   1.  **Tamam**'a tıklayın.<!--If they choose Custom, do they still click OK, or are there more steps?-->

5. **Birim kapsayıcısını Değiştir** iletişim kutusunun sonraki sayfasında:<!--This step happens only if they choose Custom bandwidth? Are the steps similar to those in "Add volume container," step 3f, above?"-->
   
   1. Aşağı açılan listeden, mevcut bir bant genişliği şablonu seçin.
   1. Belirtilen bant genişliği şablonu için zamanlama ayarlarını gözden geçirin.
   1. **Kaydet** ' e tıklayın ve değişiklikleri onaylayın.
      
       ![Değişiklikleri Onayla](./media/storsimple-8000-modify-volume-container/modify-volume-container-03.png)

      **Birim kapsayıcıları** dikey penceresi değişiklikleri yansıtacak şekilde güncelleştirilir.
