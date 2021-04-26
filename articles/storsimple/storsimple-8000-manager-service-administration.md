---
title: StorSimple Device Manager hizmet yönetimi | Microsoft Docs
description: Azure portal StorSimple Device Manager hizmetini kullanarak StorSimple cihazınızı nasıl yöneteceğinizi öğrenin.
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
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: 0e25d41fbf6a9683f178cbfd1b07fde1b6796509
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656830"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>StorSimple cihazınızı yönetmek için StorSimple Device Manager hizmetini kullanın

## <a name="overview"></a>Genel Bakış

Bu makalede, bu kullanıcı arabirimi aracılığıyla gerçekleştirilebilecek belirli iş akışlarına nasıl bağlanabileceği, kullanılabilir çeşitli seçenekler ve bu kullanıcı arabirimine giden bağlantılar dahil olmak üzere StorSimple Device Manager hizmet arabirimi açıklanmaktadır. Bu kılavuz her ikisi için de geçerlidir; StorSimple fiziksel cihazı ve bulut gereci.

Bu makaleyi okuduktan sonra şunları öğreneceksiniz:

* StorSimple Device Manager hizmetine bağlanma
* StorSimple cihazınızı StorSimple Device Manager hizmeti aracılığıyla yönetin

## <a name="connect-to-storsimple-device-manager-service"></a>StorSimple Device Manager hizmetine bağlanma

StorSimple Device Manager hizmeti Microsoft Azure ' de çalışır ve birden çok StorSimple cihaza bağlanır. Bu cihazları yönetmek için tarayıcıda çalışan Merkezi Microsoft Azure portal kullanırsınız. StorSimple Device Manager hizmetine bağlanmak için aşağıdakileri yapın.

#### <a name="to-connect-to-the-service"></a>Hizmete bağlanmak için
1. Öğesine gidin [https://portal.azure.com/](https://portal.azure.com/) .
2. Microsoft hesabı kimlik bilgilerinizi kullanarak Microsoft Azure portal oturum açın (bölmenin sağ üst kısmında bulunur).
3. StorSimple Device Manager hizmetine erişmek için sol gezinti bölmesini aşağı kaydırın.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>StorSimple Device Manager hizmetini kullanarak StorSimple cihazını yönetme

Aşağıdaki tabloda, StorSimple Device Manager hizmeti kullanıcı arabirimi içinde gerçekleştirilebilecek tüm ortak yönetim görevlerinin ve karmaşık iş akışlarının özeti gösterilmektedir. Bu görevler, başlatıldığı Kullanıcı arabirimi dikey pencerelerine göre düzenlenmiştir.

Her iş akışı hakkında daha fazla bilgi için, tabloda ilgili yordama tıklayın.

[!INCLUDE [StorSimple software warning](../../includes/storsimple-update-software-warning.md)]

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Device Manager iş akışları

| Bunu yapmak istiyorsanız... | Bu yordamı kullanın. |
| --- | --- |
| Hizmet oluşturma</br>Hizmet silme</br>Hizmet kayıt anahtarını al</br>Hizmet kayıt anahtarını yeniden üret |[StorSimple Device Manager hizmeti dağıtma](storsimple-8000-manage-service.md) |
| Etkinlik günlüklerini görüntüleme |[StorSimple Device Manager hizmeti özetini kullanın](storsimple-8000-service-dashboard.md) |
| Hizmet veri şifreleme anahtarını değiştirme</br>İşlem günlüklerini görüntüleme |[StorSimple Device Manager hizmeti panosunu kullanma](storsimple-8000-service-dashboard.md) |
| Cihazı devre dışı bırakma</br>Cihazı silme |[Bir cihazı devre dışı bırakma veya silme](storsimple-8000-deactivate-and-delete-device.md) |
| Olağanüstü durum kurtarma ve cihaz yük devretmesi hakkında bilgi edinin</br>Fiziksel bir cihaza yük devretme</br>Sanal cihaza yük devretme</br>İş sürekliliği olağanüstü durum kurtarma (BCDR) |[StorSimple cihazınız için yük devretme ve olağanüstü durum kurtarma](storsimple-8000-device-failover-disaster-recovery.md) |
| Bir birim için yedeklemeleri listeleme</br>Bir yedekleme kümesi seçin</br>Bir yedekleme kümesini silme |[Yedekleri yönetme](storsimple-8000-manage-backup-catalog.md) |
| Bir birimi kopyalama |[Bir birimi kopyalama](storsimple-8000-clone-volume-u2.md) |
| Bir yedekleme kümesini geri yükleme |[Bir yedekleme kümesini geri yükleme](storsimple-8000-restore-from-backup-set-u2.md) |
| Depolama hesapları hakkında</br>Depolama hesabı ekleme</br>Depolama hesabını düzenleme</br>Bir depolama hesabını silme</br>Depolama hesaplarının anahtar dönüşü |[Depolama hesaplarını yönetme](storsimple-8000-manage-storage-accounts.md) |
| Bant genişliği şablonları hakkında</br>Bant genişliği şablonu ekleme</br>Bant genişliği şablonunu düzenleme</br>Bant genişliği şablonunu silme</br>Varsayılan bir bant genişliği şablonu kullan</br>Belirli bir zamanda başlayan bir günlük bant genişliği şablonu oluşturun |[Bant genişliği şablonlarını yönetme](storsimple-8000-manage-bandwidth-templates.md) |
| Erişim denetimi kayıtları hakkında</br>Erişim denetimi kaydı oluşturma</br>Erişim denetimi kaydını düzenleme</br>Erişim denetimi kaydını silme |[Erişim denetim kayıtlarını yönetme](storsimple-8000-manage-acrs.md) |
| İş ayrıntılarını görüntüleme</br>Bir işi iptal etme |[İşleri yönetme](storsimple-8000-manage-jobs-u2.md) |
| Uyarı bildirimleri alma</br>Uyarıları yönetme</br>Uyarıları gözden geçirme |[StorSimple uyarılarını görüntüleyin ve yönetin](storsimple-8000-manage-alerts.md) |
| İzleme grafikleri oluşturma |[StorSimple cihazınızı izleme](./storsimple-8000-monitor-device.md) |
| Birim kapsayıcısı ekleme</br>Birim kapsayıcısını değiştirme</br>Birim kapsayıcısını silme |[Birim kapsayıcıları yönetme](storsimple-8000-manage-volume-containers.md) |
| Birim ekleme</br>Bir birimi değiştirme</br>Bir birimi çevrimdışı duruma getirme</br>Bir birimi silme</br>Bir birimi izleme |[Birimleri yönetme](storsimple-8000-manage-volumes-u2.md) |
| Cihaz ayarlarını değiştir</br>Zaman ayarlarını değiştir</br>DNS.md ayarlarını değiştir</br>Ağ arabirimlerini yapılandırma |[StorSimple cihazınız için cihaz yapılandırmasını değiştirme](storsimple-8000-modify-device-config.md) |
| Web proxy ayarlarını görüntüle |[Cihazınız için Web proxy 'yi yapılandırma](storsimple-8000-configure-web-proxy.md) |
| Cihaz yönetici parolasını değiştir</br>StorSimple Snapshot Manager parolasını değiştirme |[StorSimple parolalarını değiştirme](storsimple-8000-change-passwords.md) |
| Uzaktan yönetimi yapılandırma |[StorSimple cihazınıza uzaktan bağlanma](storsimple-8000-remote-connect.md) |
| Uyarı ayarlarını yapılandırma |[StorSimple uyarılarını görüntüleyin ve yönetin](storsimple-8000-manage-alerts.md) |
| StorSimple cihazınız için CHAP yapılandırma |[StorSimple cihazınız için CHAP yapılandırma](./storsimple-8000-configure-chap.md) |
| Yedekleme ilkesi ekleme</br>Zamanlama ekleme veya değiştirme</br>Yedekleme ilkesini silme</br>El ile yedekleme yapın</br>Birden çok birim ve zamanlamaya sahip özel bir yedekleme ilkesi oluşturma |[Yedekleme ilkelerini yönetme](storsimple-8000-manage-backup-policies-u2.md) |
| Cihaz denetleyicilerini durdur</br>Cihaz denetleyicilerini yeniden Başlat</br>Cihaz denetleyicilerini kapatma</br>Cihazınızı fabrika varsayılanlarına sıfırlayın</br>(Yukarıda yalnızca şirket içi cihaz için geçerlidir) |[StorSimple cihaz denetleyicisini yönetme](storsimple-8000-manage-device-controller.md) |
| StorSimple donanım bileşenleri hakkında bilgi edinin</br>Donanım durumunu izleme</br>(Yukarıda yalnızca şirket içi cihaz için geçerlidir) |[Donanım bileşenlerini izleme](storsimple-8000-monitor-hardware-status.md) |
| Destek paketi oluşturma |[Destek paketi oluşturma ve yönetme](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Yazılım güncelleştirmelerini yükler |[Cihazınızı güncelleştirme](storsimple-update-device.md) |

## <a name="next-steps"></a>Sonraki adımlar

StorSimple cihazınızın günlük işlemleri veya donanım bileşenlerinden herhangi biri ile ilgili herhangi bir sorunla karşılaşırsanız, aşağıdakilere bakın:

* [Tanılama aracını kullanarak sorun giderme](storsimple-8000-diagnostics.md)
* [StorSimple izleme göstergesi LED 'lerini kullanın](storsimple-monitoring-indicators.md)

Sorunları çözemezseniz ve bir hizmet isteği oluşturmanız gerekiyorsa, [iletişim Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md).