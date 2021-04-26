---
title: Bulut çözümü sağlayıcısı programı için StorSimple Sanal dizisi dağıtma
description: CSP iş ortağının mevcut bir müşteriye nasıl müşteri veya yeni bir abonelik ekleyebileceğinizi öğrenin ve ardından CSP 'de StorSimple Sanal dizisi dağıtmak için bir hizmet oluşturabilirsiniz.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: dc0cf718ee51e23fb749bdf57d5344977de009d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021869"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Bulut çözümü sağlayıcısı programı için StorSimple Sanal dizisi dağıtma

## <a name="overview"></a>Genel Bakış

StorSimple Sanal dizisi, müşterileri için bulut çözümü sağlayıcısı (CSP) iş ortakları tarafından dağıtılabilir. CSP iş ortağı, StorSimple Device Manager hizmeti oluşturabilir. Bu hizmet daha sonra StorSimple Sanal dizisini ve ilişkili paylaşımları, birimleri ve yedeklemeleri dağıtmak ve yönetmek için kullanılabilir.

Bu makalede, CSP iş ortağının mevcut bir müşteriye bir müşteri veya yeni abonelik ekleme ve ardından CSP 'de StorSimple Sanal dizisi dağıtmak üzere bir hizmet oluşturma işlemlerinin nasıl yapılacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce aşağıdakileri doğrulayın:

- CSP programına kaydolmuş olursunuz.
- Geçerli [Iş Ortağı Merkezi](https://partnercenter.microsoft.com/) oturum açma kimlik bilgileriniz var. Kimlik bilgileri, yeni müşteriler eklemek, müşteri aramak veya Iş ortağı panosundan bir müşteri hesabına gitmek için Iş ortağı portalında oturum açmanızı sağlar. CSP, Azure portal müşteri adına StorSimple Yöneticisi olarak çalışabilir.
                             
## <a name="add-a-customer"></a>Müşteri ekleme

Bir müşteri eklerseniz, otomatik olarak bir abonelik oluşturulur. Bir müşteri eklemek (ve otomatik olarak bir abonelik oluşturmak) için Iş ortağı portalında aşağıdaki adımları gerçekleştirin.

1. [Iş Ortağı Merkezi](https://partnercenter.microsoft.com/) ' ne gıdın ve CSP kimlik bilgilerinizi kullanarak oturum açın. **Pano**' ya tıklayın.

     ![Iş Ortağı Merkezi 'nde Pano](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. Sol bölmede **müşteriler**' e tıklayın. Sağ bölmede, **Müşteri Ekle**' ye tıklayın. Müşterinin ayrıntılarını girin. Bir müşteri aboneliği oluşturmak için **İleri: abonelikler** ' e tıklayın.

    ![Müşteri Ekle](./media/storsimple-partner-csp-deploy/image2.png)

3.  Teklif **Microsoft Azure** seçin. Sayfanın alt kısmına ilerleyin ve **gözden geçir**' e tıklayın.

    ![Abonelik bilgilerini gözden geçirme](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Bilgileri gözden geçirin ve **Gönder**' e tıklayın.

    ![Abonelik gönder](./media/storsimple-partner-csp-deploy/image4.png)

5. Daha sonra başvurmak üzere onay bilgilerini kaydedin.

    ![Kaydetmeyi Onayla](./media/storsimple-partner-csp-deploy/image5.png)

6. Yeni eklediğiniz müşteriyi bulun veya buraya gidin. Ayrıntıların detayına gitmek için **Şirket adına** tıklayın.

    ![Müşteriyi arayın](./media/storsimple-partner-csp-deploy/image6.png)  

7. Sol bölmede **hizmet yönetimi**' ni seçin. Sağ bölmede, **Hizmetleri Yönet** altında, müşteriniz Için bir Azure Yöneticisi olarak oturum açmak üzere **Microsoft Azure yönetim portalı** ' ye tıklayın.

    ![Azure portalında oturum açın](./media/storsimple-partner-csp-deploy/image9.png)

8. StorSimple Device Manager oluşturmak için **+ Yeni** ' ye tıklayıp **StorSimple sanal cihaz serisini** arayın veya bu seriyi bulun. Daha fazla bilgi için, [StorSimple Device Manager hizmeti dağıtma](storsimple-virtual-array-manage-service.md)konusuna gidin.

    ![StorSimple Device Manager hizmeti oluşturma](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Abonelik ekleme

Bazı örneklerde, mevcut bir müşteriniz olabilir ve bir abonelik eklemeniz gerekir. Mevcut bir müşteriye abonelik eklemek için Iş ortağı portalında aşağıdaki adımları gerçekleştirin.

1. [Iş Ortağı Merkezi](https://partnercenter.microsoft.com/) ' ne gıdın ve CSP kimlik bilgilerinizi kullanarak oturum açın. **Pano**' ya tıklayın.

     ![Iş Ortağı Merkezi 'nde Pano](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. Sol bölmede **müşteriler**' e tıklayın. Abonelik eklemek istediğiniz müşteriyi bulun veya buraya gidin. ![ ](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) Müşterinizin şirket adına ait satırı genişletmek için onay simgesini Genişlet simgesine tıklayın. Ayrıntılar ' da, **Abonelik Ekle**' ye tıklayın.

    ![Müşteriler](./media/storsimple-partner-csp-deploy/image10.png)

3. Abonelikteki **en üstteki teklifler** için **Microsoft Azure** işaretleyin ve **Gönder**' e tıklayın. Bu yeni bir abonelik oluşturur.

    ![Yeni Abonelik Ekle](./media/storsimple-partner-csp-deploy/image11.png)

6. Yeni bir abonelik oluşturulduktan sonra, **müşteriler** sayfasına dönmek için **<--** sol bölmedeki müşteriler ' e tıklayın. Henüz bir abonelik oluşturduğunuz müşteriyi arayın. Ayrıntıların detayına gitmek için **Şirket adına** tıklayın.

    ![Müşteriyi arayın](./media/storsimple-partner-csp-deploy/image6.png)  

7. Sol bölmede **hizmet yönetimi**' ni seçin. Sağ bölmede, **Hizmetleri Yönet** altında, müşteriniz Için bir Azure Yöneticisi olarak oturum açmak üzere **Microsoft Azure yönetim portalı** ' ye tıklayın.

    ![Azure portalında oturum açın](./media/storsimple-partner-csp-deploy/image9.png)

8. StorSimple Device Manager oluşturmak için **+ Yeni** ' ye tıklayıp **StorSimple sanal cihaz serisini** arayın veya bu seriyi bulun. Daha fazla bilgi için, [StorSimple Device Manager hizmeti dağıtma](storsimple-virtual-array-manage-service.md)konusuna gidin.

    ![StorSimple Device Manager hizmeti oluşturma](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Sonraki adımlar

- CSP 'de StorSimple hakkında daha fazla sorunuz varsa, [CSP: sık sorulan sorular](storsimple-partner-csp-faq.md)sayfasına gidin.
- StorSimple 'ı dağıtmaya hazırsanız, [StorSimple 'ı CSP 'de dağıtma](storsimple-partner-csp-deploy.md)bölümüne gidin.
