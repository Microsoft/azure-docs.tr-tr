---
title: Microsoft Azure StorSimple Manager Sanal dizi yönetimi | Microsoft Docs
description: Azure portal StorSimple Device Manager hizmetini kullanarak StorSimple şirket içi sanal dizinizi yönetmeyi öğrenin.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: cb5b6b1a2d890fe49d78da17d549c5676557b971
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654258"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>StorSimple Sanal dizinizi yönetmek için StorSimple Device Manager hizmetini kullanın
![işlem akışını ayarla](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Genel Bakış
Bu makalede, bağlantı kurmak ve kullanılabilir çeşitli seçenekler dahil olmak üzere StorSimple Device Manager hizmet arabirimi açıklanmakta ve bu kullanıcı arabirimi aracılığıyla gerçekleştirilebilecek belirli iş akışlarının bağlantıları sağlanmaktadır.

Bu makaleyi okuduktan sonra şunları yapabilirsiniz:

* StorSimple Device Manager hizmetine bağlanma
* StorSimple Device Manager Kullanıcı arabiriminde gezin
* StorSimple Sanal dizinizi StorSimple Device Manager hizmeti aracılığıyla yönetin

> [!NOTE]
> StorSimple 8000 serisi cihaz için kullanılabilen yönetim seçeneklerini görüntülemek için, StorSimple [cihazınızı yönetmek Için StorSimple Yöneticisi hizmetini kullanma](./storsimple-8000-manager-service-administration.md)bölümüne gidin.

## <a name="connect-to-the-storsimple-device-manager-service"></a>StorSimple Device Manager hizmetine bağlanma
StorSimple Device Manager hizmeti Microsoft Azure ' de çalışır ve birden çok StorSimple Sanal dizilerine bağlanır. Bu cihazları yönetmek için tarayıcıda çalışan Merkezi Microsoft Azure portal kullanırsınız. StorSimple Device Manager hizmetine bağlanmak için aşağıdakileri yapın.

#### <a name="to-connect-to-the-service"></a>Hizmete bağlanmak için
1. Adresine gidin [https://portal.azure.com](https://portal.azure.com) .
2. Microsoft hesabı kimlik bilgilerinizi kullanarak Microsoft Azure portal oturum açın (bölmenin sağ üst kısmında bulunur).
3. Belirli bir abonelikteki tüm cihaz yöneticlerinizi görüntülemek için StorSimple cihaz yöneticilerinde ' filtre ' > ' Filter ' bölümüne gidin.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Yönetim görevlerini gerçekleştirmek için StorSimple Device Manager hizmetini kullanın
Aşağıdaki tabloda, StorSimple Device Manager hizmeti Özeti dikey penceresinde gerçekleştirilebilecek tüm ortak yönetim görevlerinin ve karmaşık iş akışlarının özeti gösterilmektedir. Bu görevler, başlatıldığı dikey pencerelere göre düzenlenmiştir.

Her iş akışı hakkında daha fazla bilgi için, tabloda ilgili yordama tıklayın.

[!INCLUDE [StorSimple software warning](../../includes/storsimple-update-software-warning.md)]

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Device Manager iş akışları
| Bunu yapmak istiyorsanız... | Bu yordamı kullanın |
| --- | --- |
| Hizmet oluşturma</br>Hizmet silme</br>Hizmet kayıt anahtarı alma</br>Hizmet kayıt anahtarını yeniden üret |[StorSimple Device Manager hizmetini dağıtma](storsimple-virtual-array-manage-service.md) |
| Etkinlik günlüklerini görüntüleme |[StorSimple hizmet özetini kullanın](storsimple-virtual-array-service-summary.md) |
| Sanal diziyi devre dışı bırak</br>Sanal diziyi silme |[Sanal diziyi devre dışı bırakma veya silme](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Olağanüstü durum kurtarma ve cihaz yük devretme</br>Yük devretme önkoşulları</br>İş sürekliliği olağanüstü durum kurtarma (BCDR)</br>Olağanüstü durum kurtarma sırasında hatalar |[StorSimple Sanal diziniz için olağanüstü durum kurtarma ve cihaz yük devretmesi](storsimple-virtual-array-failover-dr.md) |
| Paylaşımları ve birimleri yedekleme</br>El ile yedekleme yapın</br>Yedekleme zamanlamasını değiştirme</br>Mevcut yedeklemeleri görüntüle |[StorSimple Sanal dizinizi yedekleyin](storsimple-virtual-array-backup.md) |
| Yedekleme kümesinde kopya paylaşımları</br>Yedekleme kümesinden kopya birimleri</br>Öğe düzeyinde kurtarma (yalnızca dosya sunucusu) |[StorSimple Sanal dizininizin bir yedeklemesinden kopyalayın](storsimple-virtual-array-clone.md) |
| Depolama hesapları hakkında</br>Depolama hesabı ekleme</br>Depolama hesabını düzenleme</br>Bir depolama hesabını silme |[StorSimple Sanal dizisi için depolama hesaplarını yönetme](storsimple-virtual-array-manage-storage-accounts.md) |
| Erişim denetimi kayıtları hakkında</br>Erişim denetimi kaydı ekleme veya değiştirme </br>Erişim denetimi kaydını silme |[StorSimple Sanal dizisi için erişim denetim kayıtlarını yönetme](storsimple-virtual-array-manage-acrs.md) |
| İş ayrıntılarını görüntüleme |[StorSimple Sanal dizisi işlerini yönetin](storsimple-virtual-array-manage-jobs.md) |
| Uyarı ayarlarını yapılandırma</br>Uyarı bildirimleri alma</br>Uyarıları yönetme</br>Uyarıları gözden geçirme |[StorSimple Sanal dizisi için uyarıları görüntüleyin ve yönetin](storsimple-virtual-array-manage-alerts.md) |
| Cihaz yönetici parolasını değiştirme |[StorSimple Sanal dizisi cihaz yönetici parolasını değiştirme](storsimple-virtual-array-change-device-admin-password.md) |
| Yazılım güncelleştirmelerini yükler |[Sanal dizinizi güncelleştirme](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Aşağıdaki görevler için [Yerel Web Kullanıcı arabirimini](storsimple-ova-web-ui-admin.md) kullanmanız gerekir:
> 
> * [Hizmet veri şifreleme anahtarını alma](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Destek paketi oluşturma](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Sanal diziyi durdurma ve yeniden başlatma](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
Web Kullanıcı arabirimi ve nasıl kullanılacağı hakkında bilgi için, StorSimple [sanal dizinizi yönetmek üzere StorSimple Web Kullanıcı arabirimini kullanma](storsimple-ova-web-ui-admin.md)bölümüne gidin.