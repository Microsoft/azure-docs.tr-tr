---
title: StorSimple 'da erişim denetim kayıtlarını yönetme | Microsoft Docs
description: StorSimple cihazındaki bir birime hangi ana bilgisayarların bağlanacağını belirleyen erişim denetim kayıtlarının (ACRs) nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 15c35fb314af27b1ced129a12f752d0a2794e0f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91949913"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Erişim denetim kayıtlarını yönetmek için StorSimple Yöneticisi hizmetini kullanma

## <a name="overview"></a>Genel Bakış
Erişim denetimi kayıtları (ACRs), StorSimple cihazındaki bir birime hangi konaklara bağlanabileceği belirtmenize izin verir. ACRs 'ler belirli bir birime ayarlanır ve konaklara ait Iscsı nitelenmiş adlarını (IQNs) içerir. Bir konak bir birime bağlanmaya çalıştığında, cihaz, ıQN adı için bu birimle ilişkili ACR 'yi denetler ve bir eşleşme varsa bağlantı oluşturulur. StorSimple Device Manager hizmeti dikey pencerenizin **yapılandırma** bölümündeki erişim denetim kayıtları, konaklara karşılık gelen tüm erişim denetim kayıtlarını görüntüler.

Bu öğreticide, aşağıdaki genel ACR ile ilgili görevler açıklanmaktadır:

* Erişim denetimi kaydı ekleme
* Erişim denetimi kaydını düzenleme
* Erişim denetimi kaydını silme

> [!IMPORTANT]
> * Bir birime ACR atarken, bu birimi bozabileceğinden, birimin birden fazla kümelenmemiş konak tarafından eşzamanlı olarak erişilmediğinden emin olmanız gerekir.
> * Bir birimden bir ACR silinirken, silme işlemi okuma/yazma kesintiye neden olabileceğinden, karşılık gelen konağın birime erişmediğinden emin olun.

## <a name="get-the-iqn"></a>IQN 'yi al

Windows Server 2012 çalıştıran bir Windows konağının ıQN 'sini almak için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Erişim denetimi kaydı ekleme
StorSimple Device Manager hizmeti dikey penceresindeki **yapılandırma** bölümünü, ACRS eklemek için kullanabilirsiniz. Genellikle, bir ACR 'yi bir birimle ilişkilendireceğiniz olur.

Bir ACR eklemek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-add-an-acr"></a>ACR eklemek için

1. StorSimple Device Manager hizmetinize gidin, hizmet adına çift tıklayın ve ardından **yapılandırma** bölümünde **erişim denetim kayıtları**' na tıklayın.
2. **Erişim denetimi kayıtları** dikey penceresinde **+ ACR Ekle**' ye tıklayın.

    ![ACR Ekle 'ye tıklayın](./media/storsimple-8000-manage-acrs/createacr1.png)

3. **ACR Ekle** dikey penceresinde aşağıdaki adımları uygulayın:

    1. ACR 'niz için bir ad sağlayın.
    
    2. **Iscsı Başlatıcısı adı (IQN)** altında Windows Server konağının IQN adını sağlayın.

    3. ACR 'yi oluşturmak için **Ekle** ' ye tıklayın.

        ![Ekle 2 ' ye tıklayın.](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Yeni eklenen ACR, ACRs 'nin tablosal listesinde görüntülenir.

    ![Ekle 3 ' e tıklayın](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Erişim denetimi kaydını düzenleme
ACRs 'yi düzenlemek için StorSimple Device Manager hizmeti dikey penceresindeki **yapılandırma** bölümünü kullanın.

> [!NOTE]
> Yalnızca şu anda kullanımda olmayan ACRs 'leri değiştirmeniz önerilir. Kullanılmakta olan bir birimle ilişkili bir ACR 'yi düzenlemek için öncelikle birimi çevrimdışına almanız gerekir.

Bir ACR 'yi düzenlemek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-edit-an-access-control-record"></a>Bir erişim denetimi kaydını düzenlemek için
1.  StorSimple Device Manager hizmetinize gidin, hizmet adına çift tıklayın ve ardından **yapılandırma** bölümünde **erişim denetim kayıtları**' na tıklayın.

    ![Erişim denetimi kayıtlarına git](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Erişim denetimi kayıtlarının tablosal listesinde, ' a tıklayın ve değiştirmek istediğiniz ACR 'yi seçin.

    ![Erişim denetim kayıtlarını Düzenle](./media/storsimple-8000-manage-acrs/editacr1.png)

3. **Erişim denetimi kaydını Düzenle** dikey penceresinde, başka bir konağa karşılık gelen farklı bir IQN sağlayın.

    ![Erişim denetimi kayıtlarını Düzenle 2](./media/storsimple-8000-manage-acrs/editacr2.png)

4. **Kaydet**’e tıklayın. Onayınız istendiğinde **Evet**’e tıklayın. 

    ![Erişim denetimi kayıtlarını düzenleme 3](./media/storsimple-8000-manage-acrs/editacr3.png)

5. ACR güncelleştirildiği zaman size bildirilir. Tablo listesi ayrıca değişikliği yansıtacak şekilde güncelleştirilir.

   
## <a name="delete-an-access-control-record"></a>Erişim denetimi kaydını silme
StorSimple Device Manager hizmeti dikey penceresindeki **yapılandırma** bölümünü kullanarak ACRS 'yi silebilirsiniz.

> [!NOTE]
> Yalnızca şu anda kullanımda olmayan ACRs 'leri silebilirsiniz. Kullanılmakta olan bir birimle ilişkili bir ACR 'yi silmek için öncelikle birimi çevrimdışına almanız gerekir.

Bir erişim denetimi kaydını silmek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-delete-an-access-control-record"></a>Bir erişim denetimi kaydını silmek için
1.  StorSimple Device Manager hizmetinize gidin, hizmet adına çift tıklayın ve ardından **yapılandırma** bölümünde **erişim denetim kayıtları**' na tıklayın.

    ![Access Control kayıtlarına git 1B](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Erişim denetimi kayıtlarının tablosal listesinde, öğesini tıklatın ve silmek istediğiniz ACR 'yi seçin.

    ![Erişim denetimi kayıtlarına git 2](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Bağlam menüsünü çağırmak için sağ tıklayın ve **Sil**' i seçin.

    ![Access Control kayıtlarına git 3](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Onay istendiğinde, bilgileri gözden geçirin ve ardından **Sil**' e tıklayın.

    ![Access Control kayıtlarına gidin 4](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Silme işlemi tamamlandığında size bildirilir. Tablosal listesi, silme işlemini yansıtacak şekilde güncelleştirilir.

    ![Access Control kayıtlarına git 5](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Sonraki adımlar
* [StorSimple birimlerini yönetme](storsimple-8000-manage-volumes-u2.md)hakkında daha fazla bilgi edinin.
* [StorSimple cihazınızı yönetmek Için StorSimple Yöneticisi hizmetini kullanma](storsimple-8000-manager-service-administration.md)hakkında daha fazla bilgi edinin.

