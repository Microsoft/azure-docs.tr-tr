---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: fd9b3b501d6efbe6a74d350a678494e8254dbb32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545409"
---
#### <a name="to-create-a-volume-container"></a>Birim kapsayıcısı oluşturmak için

1. StorSimple Cihaz Yöneticisi hizmetinize gidin ve **Cihazlar**’a tıklayın. Tablosal cihaz listesinden bir cihazı seçin ve tıklayın. 

    ![Birim kapsayıcısı dikey penceresi](./media/storsimple-8000-create-volume-container/create-volume-container-01.png)

2. Cihaz panosunda **+ Birim kapsayıcısı ekle**’ye tıklayın

    ![Birim kapsayıcısı dikey penceresi 2](./media/storsimple-8000-create-volume-container/create-volume-container-02.png)

3. **Birim kapsayıcısı ekle** dikey penceresinde:
   
   1. Cihaz otomatik olarak seçilir.
   2. Birim kapsayıcınıza bir **Ad** verin. Adı 3 ile 32 karakter arası uzunlukta olmalıdır. Birim kapsayıcısını bir kez oluşturduktan sonra yeniden adlandıramazsınız.
   3. Cihazdan buluta gönderilen verilerin şifrelenmesini etkinleştirmek için **Bulut Depolama Şifrelemesini Etkinleştir**’i seçin.
   4. 8 ile 32 karakter uzunlukta olan bir **Bulut Depolama Şifrelemesi Anahtarı** sağlayın ve onaylayın. Bu anahtar cihaz tarafından şifrelenmiş verilere erişmek için kullanılır.
   5. Bu birim kapsayıcısı ile ilişkilendirilecek bir **Depolama Hesabı** seçin. Mevcut bir depolama hesabını veya hizmet oluşturulduğu sırasında oluşturulan varsayılan hesabı seçebilirsiniz. Hizmet aboneliğine bağlanmayan depolama hesabını belirtmek için de **Yeni ekle** seçeneğini kullanabilirsiniz.
   6. Kullanılabilir tüm bant genişliğini tüketmek istiyorsanız **Bant genişliğini belirt** açılan listesinde **Sınırsız**’ı seçin. Bant genişliği denetimlerini görevlendirmek ve 1 Mb/sn ile 1000 Mb/sn arasında bir değer belirtmek için bu seçeneği **Özel** olarak da ayarlayabilirsiniz.
   
      Bant genişliği kullanım bilgileriniz varsa, **Bant genişliği şablonu seçin**’i belirterek zamanlama temelinde bant genişliği ayırabilirsiniz. Adım adım bir yordam için [Bant genişliği şablonu ekleyin](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template)’e gidin.

      ![Birim kapsayıcısı dikey penceresi 3](./media/storsimple-8000-create-volume-container/create-volume-container-06-b.png)<!--New graphic. Source: add-volume-container-bw-setting.-->

   7. **Oluştur**’a tıklayın.

        <!--![Volume container blade 4](./media/storsimple-8000-create-volume-container/create-volume-container-06.png)-->
   
       Birim kapsayıcısı başarıyla oluşturulduğunda size bildirilir.

       ![Birim kapsayıcısı oluşturma bildirimi](./media/storsimple-8000-create-volume-container/create-volume-container-08.png)

   Yeni oluşturulan birim kapsayıcısı, cihazınıza yönelik birim kapsayıcıları listesine eklenir.

   ![Birim kapsayıcısı ekle dikey penceresi](./media/storsimple-8000-create-volume-container/create-volume-container-09.png)
