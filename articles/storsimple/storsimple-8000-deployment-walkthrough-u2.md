---
title: Azure portalında StorSimple 8000 serisi cihazınızı dağıtma | Microsoft Docs
description: Güncelleştirme 3 ve üzerini çalıştıran StorSimple 8000 serisi cihazını ve StorSimple Cihaz Yöneticisi hizmetini dağıtmak için uygulanacak adımları ve en iyi yöntemleri açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/23/2018
ms.author: alkohli
ms.openlocfilehash: 1f44690de1f38e3d337072cc7c974887eb0e31cc
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965894"
---
# <a name="deploy-your-on-premises-storsimple-device-update-3-and-later"></a>Şirket içi StorSimple cihazınızı (Güncelleştirme 3 ve üzeri) dağıtma

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Genel Bakış
Microsoft Azure StorSimple cihaz dağıtımına hoş geldiniz. Bu dağıtım öğreticileri, StorSimple 8000 Serisi Güncelleştirme 3 ve üzeri için geçerlidir. Bu öğretici dizisi, StorSimple cihazınız için yapılandırma denetim listesi, yapılandırma önkoşulları ve ayrıntılı yapılandırma adımlarını içerir.

Bu öğreticilerdeki bilgiler, güvenlik önlemlerini gözden geçirdiğinizi ve StorSimple cihazınızı kutusundan çıkardığınızı, yerleştirdiğinizi ve kablolarını taktığınızı varsayar. Bu görevleri henüz gerçekleştirmediyseniz, [güvenlik önlemlerini](storsimple-8000-safety.md) inceleyerek başlayın. Cihazınızı açmak, rafa monte etmek ve kablo bağlantısını yapmak için cihaza özel yönergeleri izleyin.

* [8100 model cihazınızı kutusundan çıkarma, rafa takma ve kablolarını bağlama](storsimple-8100-hardware-installation.md)
* [8600 model cihazınızı kutusundan çıkarma, rafa takma ve kablolarını bağlama](storsimple-8600-hardware-installation.md)

Kurulum ve yapılandırma işlemini tamamlamak için yönetici ayrıcalıkları gerekir. Başlamadan önce yapılandırma denetim listesini gözden geçirmenizi öneririz. Dağıtım ve yapılandırma işleminin tamamlanması biraz zaman alabilir.

> [!NOTE]
> Microsoft Azure Web sitesinde yayınlanan StorSimple dağıtım bilgileri yalnızca StorSimple 8000 serisi cihazlar için geçerlidir. 7000 serisi cihazlar hakkında tam bilgi için şu adresi ziyaret edin: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). 7000 serisi dağıtım bilgileri için bkz. [StorSimple Sistemi Hızlı Başlangıç Kılavuzu](http://onlinehelp.storsimple.com/111_Appliance/). 


## <a name="deployment-steps"></a>Dağıtım adımları
StorSimple cihazınızı yapılandırmak ve StorSimple Cihaz Yöneticisi hizmetine bağlamak için gerekli adımları gerçekleştirin. Gerekli adımlara ek olarak, dağıtım sırasında ihtiyacınız olabilecek isteğe bağlı adım ve yordamlar vardır. Bu adım adım dağıtım yönergelerinde isteğe bağlı adımların her birini ne zaman gerçekleştirmeniz gerektiği belirtilmiştir.

| Adım | Açıklama |
| --- | --- |
| **ÖNKOŞULLAR** |Dağıtım için bu önkoşullar tamamlanmalıdır. |
| [Dağıtım yapılandırma denetim listesi](#deployment-configuration-checklist) |Dağıtımdan önce ve dağıtım sırasında bilgi toplamak ve bilgileri kaydetmek için bu denetim listesini kullanın. |
| [Dağıtım önkoşulları](#deployment-prerequisites) |Bunlar, ortamın dağıtım için hazır olduğunu doğrular. |
|  | |
| **ADIM ADIM DAĞITIM** |Bu adımlar, StorSimple cihazınızı üretim ortamına dağıtmak için gereklidir. |
| [1. Adım: Yeni bir hizmet oluşturun](#step-1-create-a-new-service) |StorSimple cihazınız için bulut yönetimi ve depolamayı ayarlayın. *Başka StorSimple cihazlar için bir hizmetiniz varsa, bu adımı atlayın*. |
| [2. Adım: Hizmet kayıt anahtarını al](#step-2-get-the-service-registration-key) |StorSimple cihazınızı kaydetmek ve yönetim hizmetine bağlamak için bu anahtarı kullanın. |
| [Adım 3: StorSimple için Windows PowerShell aracılığıyla cihazı yapılandırma ve kaydetme](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |Kurulumu yönetim hizmetini kullanarak tamamlamak için cihazı ağınıza bağlayın ve Azure’a kaydedin. |
| [4. Adım: En düşük cihaz kurulumunu tamamlar](#step-4-complete-minimum-device-setup)</br>[En iyi uygulama: StorSimple cihazınızı güncelleştirin](#scan-for-and-apply-updates) |Depolama alanı sağlamak için, yönetim hizmetini kullanarak cihaz kurulumunu tamamlayın ve etkinleştirin. |
| [5. Adım: Birim kapsayıcısı oluşturma](#step-5-create-a-volume-container) |Birimleri sağlamak için bir kapsayıcı oluşturun. Birim kapsayıcısı, kapsadığı tüm birimler için depolama hesabı, bant genişliği ve şifreleme ayarlarını içerir. |
| [Adım 6: Birim oluşturma](#step-6-create-a-volume) |Sunucularınız için StorSimple cihazında depolama birimleri sağlayın. |
| [Adım 7: Bir birimi bağlama, başlatma ve biçimlendirme](#step-7-mount-initialize-and-format-a-volume)</br>[Seçim MPIO yapılandırma](storsimple-8000-configure-mpio-windows-server.md) |Sunucularınızı cihaz tarafından sağlanan iSCSI depolama alanına bağlayın. İsteğe bağlı olarak, sunucularınızın bağlantı, ağ ve arabirim hatalarından etkilenmemesini sağlamak için MPIO’yu yapılandırın. |
| [8. Adım: Bir yedekleme yapın](#step-8-take-a-backup) |Verilerinizi korumak için yedekleme ilkenizi ayarlayın |
|  | |
| **DİĞER YORDAMLAR** |Çözümünüzü dağıtırken bu yordamlara başvurmanız gerekebilir. |
| [Hizmet için yeni bir depolama hesabı yapılandırın](#configure-a-new-storage-account-for-the-service) | |
| [Cihaz seri konsoluna bağlanmak için PuTTY kullanın](#use-putty-to-connect-to-the-device-serial-console) | |
| [Bir Windows Server konağının IQN’ini alın](#get-the-iqn-of-a-windows-server-host) | |
| [El ile yedekleme oluşturun](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>Dağıtım yapılandırma denetim listesi
Cihazınızı dağıtmadan önce, StorSimple cihazınızda yazılımı yapılandırmak amacıyla bilgi toplamanız gerekir. Bu bilgilerin bir bölümünü önceden hazırlamak, ortamınızda StorSimple cihazını dağıtma işlemini kolaylaştırmaya yardımcı olur. Cihazınızı dağıtırken yapılandırma ayrıntılarını not etmek için bu denetim listesini indirin ve kullanın.

* [StorSimple dağıtım yapılandırma denetim listesini indirme](https://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>Dağıtım önkoşulları
Aşağıdaki bölümlerde, StorSimple Cihaz Yöneticisi hizmetiniz ve StorSimple cihazınız için yapılandırma önkoşulları açıklanmaktadır.

### <a name="for-the-storsimple-device-manager-service"></a>StorSimple Cihaz Yöneticisi hizmeti için
Başlamadan önce aşağıdakilerden emin olun:

* Erişim kimlik bilgilerine sahip bir Microsoft hesabınız var.
* Erişim kimlik bilgilerine sahip bir Microsoft Azure Storage hesabınız var.
* Microsoft Azure aboneliğiniz StorSimple Cihaz Yöneticisi hizmeti için etkinleştirildi. Aboneliğinizin [Kurumsal Anlaşma](https://azure.microsoft.com/pricing/enterprise-agreement/) aracılığıyla satın alınmış olması gerekir.
* PuTTY gibi bir terminal öykünme yazılımına erişiminiz var.

### <a name="for-the-device-in-the-datacenter"></a>Veri merkezindeki cihaz için
Cihazınızı yapılandırmadan önce, Cihazınızın tam olarak açılmış, bir rafa monte edilmiş ve güç, ağ ve seri erişim için kablolar aşağıdaki şekilde tam olarak bağlanmış olduğundan emin olun:

* [8100 model cihazınızı kutusundan çıkarma, rafa takma ve kablolarını bağlama](storsimple-8100-hardware-installation.md)
* [8600 model cihazınızı kutusundan çıkarma, rafa takma ve kablolarını bağlama](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Veri merkezindeki ağ için
Başlamadan önce aşağıdakilerden emin olun:

* Veri merkezi güvenlik duvarınızdaki bağlantı noktaları iSCSI ve bulut trafiğine izin vermek için [StorSimple cihazınız için ağ gereksinimleri](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device) bölümünde belirtildiği şekilde açık.

## <a name="step-by-step-deployment"></a>Adım adım dağıtım
StorSimple cihazınızı veri merkezinde dağıtmak için aşağıdaki adım adım yönergeleri kullanın.

## <a name="step-1-create-a-new-service"></a>1\. adım: Yeni hizmet oluşturma
Bir StorSimple Cihaz Yöneticisi hizmeti birden çok StorSimple cihazını yönetebilir. StorSimple Cihaz Yöneticisi hizmetinin bir örneğini oluşturmak için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]

> [!IMPORTANT]
> Hizmetinizle birlikte bir depolama hesabının otomatik olarak oluşturulmasını etkinleştirmediyseniz, bir hizmeti başarıyla oluşturduktan sonra en az bir depolama hesabı oluşturmanız gerekir. Bir birim kapsayıcısı oluşturduğunuzda bu depolama hesabı kullanılır.
>
> * Otomatik olarak bir depolama hesabı oluşturmadıysanız, ayrıntılı yönergeler için [Hizmet için yeni bir depolama hesabı yapılandırma](#configure-a-new-storage-account-for-the-service) bölümüne gidin.
> * Bir depolama hesabının otomatik olarak oluşturulmasını etkinleştirdiyseniz 2. [adıma gidin: Hizmet kayıt anahtarını](#step-2-get-the-service-registration-key)alın.


## <a name="step-2-get-the-service-registration-key"></a>2\. adım: Hizmet kayıt anahtarı alma
StorSimple Cihaz Yöneticisi hizmeti çalışır duruma geldikten sonra, hizmet kayıt anahtarını almanız gerekir. Bu anahtar StorSimple cihazınızı kaydetmek ve hizmete bağlamak için kullanılır.

Azure portalında aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>3\. adım: StorSimple için Windows PowerShell aracılığıyla cihazı yapılandırma ve kaydetme
Aşağıdaki yordamda açıklandığı gibi StorSimple cihazınızın ilk kurulumu tamamlamak üzere StorSimple için Windows PowerShell kullanın. Bu adımı tamamlamak için bir terminal öykünme yazılımı kullanmanız gerekir. Daha fazla bilgi için bkz. [Cihaz seri konsoluna bağlanmak için PuTTY kullanma](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-8000-configure-and-register-device-u2](../../includes/storsimple-8000-configure-and-register-device-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>4\. Adım: Minimum cihaz kurulumunu tamamlayın
StorSimple cihazınız için en düşük cihaz yapılandırması için aşağıdakileri yapmanız gerekir: 

* Cihazınız için kolay bir ad sağlayın.
* Cihazın saat dilimini ayarlayın.
* İki denetleyiciye de sabit IP adresleri atayın.

En düşük cihaz kurulumunu tamamlamak için Azure portalında aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

Minimum cihaz ayarını tamamladıktan sonra [en son güncelleştirmeleri taramak ve uygulamak](#scan-for-and-apply-updates) önerilir.

## <a name="step-5-create-a-volume-container"></a>5\. Adım: Birim kapsayıcısı oluşturun
Birim kapsayıcısı, kapsadığı tüm birimler için depolama hesabı, bant genişliği ve şifreleme ayarlarını içerir. StorSimple cihazınızda birimleri sağlamaya başlamadan önce bir birim kapsayıcısı oluşturmanız gerekir.

Birim kapsayıcısı oluşturmak için Azure portalında aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>6\. Adım: Birim oluştur
Bir birim kapsayıcısı oluşturduktan sonra, sunucularınız için StorSimple cihazında bir depolama birimi sağlayabilirsiniz. Birim oluşturmak için Azure portalında aşağıdaki adımları gerçekleştirin.

> [!IMPORTANT]
> StorSimple Cihaz Yöneticisi hem ölçülü hem de tam kaynak kullanan birimler oluşturabilir. Ancak kısmen sağlanan birimler oluşturamazsınız.


[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>7\. Adım: Bir birimi bağlayın, başlatın ve biçimlendirin
Aşağıdaki adımlar, Windows Server konağınızda gerçekleştirilir.

> [!IMPORTANT]
> * StorSimple çözümünüzün yüksek oranda kullanılabilirliğini sağlamak için, iSCSI’ı yapılandırmadan önce konak sunucularınızda (isteğe bağlı) MPIO’yu yapılandırmanızı öneririz. Konak sunucuları üzerinde MPIO yapılandırması sunucuların bağlantı, ağ veya arabirim hatalarına dayanabileceğinden emin olunmasını sağlar.
> * Windows Server konağında MPIO ve iSCSI yükleme ve yapılandırma yönergeleri için [StorSimple cihazınız için MPIO yapılandırma](storsimple-8000-configure-mpio-windows-server.md) bölümüne gidin. Bu adımlar StorSimple birimlerini bağlamayı, başlatmayı ve biçimlendirmeyi de içerir.
> * Bir Linux konağında MPIO ve iSCSI yükleme ve yapılandırma yönergeleri için [StorSimple Linux konağınız için MPIO yapılandırma](storsimple-configure-mpio-on-linux.md) bölümüne gidin.

MPIO yapılandırmamaya karar verirseniz, bir Windows Server konağında StorSimple birimlerinizi bağlamak, başlatmak ve biçimlendirmek için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-8000-mount-initialize-format-volume](../../includes/storsimple-8000-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>8\. Adım: Bir yedekleme yapın
Yedeklemeler, birimlerin zaman noktası korumasını sağlar ve geri yükleme sürelerini azaltırken kurtarılabilirliği iyileştirir. StorSimple cihazınızda iki tür yedekleme oluşturabilirsiniz: yerel anlık görüntüler ve bulut anlık görüntüleri. Bu yedekleme türlerinin her biri **Zamanlanmış** veya **El ile** olabilir.

Zamanlanmış yedekleme oluşturmak için Azure portalında aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

İstediğiniz zaman el ile yedekleme oluşturabilirsiniz. Yordamlar için, [El ile yedekleme oluşturun](#create-a-manual-backup) bölümüne gidin.

Cihaz yapılandırmasını tamamladınız.

## <a name="configure-a-new-storage-account-for-the-service"></a>Hizmet için yeni bir depolama hesabı yapılandırın
Bu yalnızca hizmetinizle bir depolama hesabının otomatik olarak oluşturulmasını etkinleştirmediyseniz gerçekleştirmeniz gereken isteğe bağlı bir adımdır. StorSimple birim kapsayıcısı oluşturmak için bir Microsoft Azure Storage hesabı gereklidir.

Farklı bir bölgede bir Azure Storage hesabı oluşturmanız gerekiyorsa, adım adım yönergeler için bkz. [Azure Storage hesapları hakkında](../storage/common/storage-create-storage-account.md).

Azure portalındaki **StorSimple Cihaz Yöneticisi hizmeti** sayfasında aşağıdaki adımları gerçekleştirin.

[!INCLUDE [storsimple-8000-configure-new-storage-account-u2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Cihaz seri konsoluna bağlanmak için PuTTY kullanın
StorSimple için Windows PowerShell’e bağlanmak için PuTTY gibi bir terminal öykünme yazılımı kullanmanız gerekir. Cihaza seri konsolu aracılığıyla doğrudan veya uzak bir bilgisayardan telnet oturumu açarak eriştiğinizde PuTTY kullanabilirsiniz.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Güncelleştirmeleri tarama ve güncelleştirmeleri uygulama
Cihazınızın güncelleştirilmesi birkaç saat sürebilir. En son güncelleştirmeyi yüklemenin ayrıntılı adımları için [Güncelleştirme 5'i yükleme](storsimple-8000-install-update-5.md) bölümüne gidin.


## <a name="get-the-iqn-of-a-windows-server-host"></a>Bir Windows Server konağının IQN’ini alın
Windows Server® 2012 çalıştıran bir Windows konağının iSCSI Tam Adını (IQN) almak için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>El ile yedekleme oluşturun
StorSimple cihazınızda tek bir birim için bir isteğe bağlı el ile yedekleme oluşturmak üzere Azure portalında aşağıdaki adımları gerçekleştirin.

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="view-the-pinout-diagram-for-serial-cable-for-storsimple"></a>StorSimple için seri kablo bağlantı diyagramını görüntüleyin
Aşağıdaki bağlantı diyagramı StorSimple seri konsol kablosu için kullanılabilir.

Burada DB9 dişi bağlayıcı P1 ve 3,5 mm bağlayıcı P2’dir.

![StorSimple seri konsol kablosu için bağlantı diyagramı 1](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram1.png)

Aşağıdaki diyagramda gösterildiği gibi, stereo jakın ucu PIN 3 RX, ortası PIN 2 TX ve tabanı PIN 1 GND olarak değerlendirilir.

![StorSimple seri konsol kablosu için bağlantı diyagramı 2](./media/storsimple-8000-deployment-walkthrough-u2/pinout-diagram2.png)



## <a name="next-steps"></a>Sonraki adımlar
* [StorSimple Cloud Appliance’ı yapılandırma](storsimple-8000-cloud-appliance-u2.md).
* [StorSimple cihazınızı yönetmek için, StorSimple Cihaz Yöneticisi hizmetini kullanın](storsimple-8000-manager-service-administration.md).

