---
title: StorSimple Sanal dizisi işlerini görüntüleyin ve yönetin | Microsoft Docs
description: StorSimple Device Manager hizmet Işleri sayfasını ve StorSimple Sanal dizisi için en son ve geçerli işleri izlemek üzere nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: d806d8a04dc1bd8547808d20c77bfec310f7dd06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95992988"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>StorSimple Sanal dizisine yönelik işleri görüntülemek için StorSimple Device Manager hizmetini kullanın
## <a name="overview"></a>Genel Bakış
**İşler** dikey penceresi, storsimple Device Manager hizmetinize bağlı sanal dizilerde başlatılan işleri görüntülemek ve yönetmek için tek bir merkezi Portal sağlar. Birden çok sanal cihaz için çalışan, tamamlanan ve başarısız işleri görüntüleyebilirsiniz. Sonuçlar tablolu biçimde görüntülenir.

![İşler dikey penceresi](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

İlgilendiğiniz işleri, şu gibi alanlarda filtreleyerek hızlı bir şekilde bulabilirsiniz:

* **Zaman aralığı** : işler tarih ve saat aralığına göre filtrelenebilir.
* **Cihazlar** – işler hizmetinize bağlı belirli bir cihazda başlatılır. Filtrelenmiş işler, daha sonra aşağıdaki özniteliklere göre tablo olarak ayarlanır:
  
  * **Ad** : Iş adı **Tümü**, **yedekleme**, **kopyalama**, **Yük devretme**, **güncelleştirmeleri indirme** veya **güncelleştirme yükleme** olabilir.
  * **Durum** – işlerin **hepsi**, **devam ediyor**, **başarılı** veya **başarısız** veya **iptal edilmiş** olabilir.
  * **Varlık** – işler bir birimle, paylaşımda veya cihazla ilişkilendirilebilir.
  * **Device** : işin başlatıldığı cihazın adı.
  * **Başlangıç tarihi** – işin başlatıldığı zaman.
  * **Duration** : işin çalıştırıldığı süre.
* **Durum** : tüm, çalışan, tamamlanmış veya başarısız işleri arayabilirsiniz.
* **İş türü** : iş türü tümü, yedekleme, geri yükleme, yük devretme, güncelleştirmeleri indirme veya güncelleştirme yükleme olabilir.

İşlerin listesi, her 30 saniyede bir yenilenir.

## <a name="view-job-details"></a>İş ayrıntılarını görüntüleme
Herhangi bir işin ayrıntılarını görüntülemek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-view-job-details"></a>İş ayrıntılarını görüntülemek için
1. **İşler** dikey penceresinde ilgilendiğiniz iş (ler) i uygun filtrelerle bir sorgu çalıştırarak görüntüleyin. Tamamlanmış veya çalışan işler için arama yapabilirsiniz.
2. Tablosal iş listesinden bir iş seçin.
   
    ![İş dikey penceresi](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Sayfanın alt kısmındaki **Ayrıntılar**' a tıklayın.
4. **Ayrıntılar** iletişim kutusunda durumu, ayrıntıları ve zaman istatistiklerini görüntüleyebilirsiniz. Aşağıdaki çizimde, **yedekleme Işi ayrıntıları** iletişim kutusunun bir örneği gösterilmektedir.
   
    ![İş ayrıntıları](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Hiper yöneticide sanal makine duraklatıldığında iş sorunları
StorSimple Sanal diziniz üzerinde devam eden bir iş olduğunda ve cihaz (Hiper yöneticide sağlanan sanal makine) 15 dakikadan daha uzun bir süre durakladığında, iş başarısız olur. Bunun nedeni, StorSimple Sanal diziniz zaman Microsoft Azure zamanından eşitlenmemiş. 

Şu hatayı görürsünüz: "cihazınızın saati, 15 dakikadan uzun bir süre Microsoft Azure eşitlenmemiş. Hiper yöneticinin ve cihaz sürelerinin bir NTP sunucusu ile eşitlendiğinden emin olun. Hiçbir bağlantı sorunu olmadığını doğrulayın. Bağlantı sorunlarını gidermek için sanal cihazınızın yerel Web kullanıcı arabiriminden tanılama testlerini çalıştırın. "

Bu arızalar yedekleme, geri yükleme, güncelleştirme ve yük devretme işleri için geçerlidir. Sanal makineniz Hyper-V ' d a sağlanmışsa, makine en sonunda, hiper yöneticiyle zaman eşitler. Bu durumda, işinizi yeniden başlatabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
[StorSimple Sanal dizinizi yönetmek için yerel Web Kullanıcı arabirimini nasıl kullanacağınızı öğrenin](storsimple-ova-web-ui-admin.md).

