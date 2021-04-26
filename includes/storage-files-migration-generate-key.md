---
title: include dosyası
description: include dosyası
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a142de62f1ab7046d47c29753863b16864d97536
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075628"
---
Hizmet veri şifreleme anahtarları, StorSimple Yöneticisi hizmetinizden StorSimple cihazına gönderilen depolama hesabı kimlik bilgileri gibi gizli müşteri verilerini şifrelemek için kullanılır. BT kuruluşunuzda depolama cihazlarında bir anahtar döndürme ilkesi varsa, bu anahtarları düzenli aralıklarla değiştirmeniz gerekir. Anahtar değişikliği işlemi, StorSimple Yöneticisi hizmeti tarafından yönetilen tek bir cihaz veya birden çok cihaz olmasına bağlı olarak biraz farklı olabilir. Daha fazla bilgi için, [StorSimple güvenlik ve veri koruma](../articles/storsimple/storsimple-8000-security.md)bölümüne gidin.

Hizmet veri şifreleme anahtarını değiştirmek 3 adımlı bir işlemdir:

1. Azure Resource Manager için Windows PowerShell betiklerini kullanarak hizmet veri şifreleme anahtarını değiştirmeye yönelik bir cihaz yetkilendirin.
2. StorSimple için Windows PowerShell kullanarak, hizmet veri şifreleme anahtarı değişikliğini başlatın.
3. Birden fazla StorSimple cihazınız varsa, diğer cihazlarda hizmet veri şifreleme anahtarını güncelleştirin.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>1. Adım: bir cihaza hizmet veri şifreleme anahtarını değiştirme yetkisi vermek için Windows PowerShell betiği kullanma
Genellikle, Cihaz Yöneticisi hizmet yöneticisinin hizmet veri şifreleme anahtarlarını değiştirmek için bir cihazı yetkilendirmesini ister. Hizmet Yöneticisi, daha sonra cihazı anahtarı değiştirecek şekilde yetkilendirecek.

Bu adım Azure Resource Manager tabanlı betik kullanılarak gerçekleştirilir. Hizmet Yöneticisi yetkilendirilebilecek bir cihaz seçebilir. Daha sonra cihaz, hizmet veri şifreleme anahtarı değiştirme işlemini başlatmaya yetkilidir. 

Betiği kullanma hakkında daha fazla bilgi için [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1) gidin.

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Hizmet veri şifreleme anahtarlarını değiştirmek için hangi cihazlara yetki atanabilir?
Bir cihazın hizmet veri şifreleme anahtarı değişikliklerini başlatma yetkisine sahip olabilmesi için aşağıdaki ölçütleri karşılaması gerekir:

* Hizmet veri şifreleme anahtarı değişiklik yetkilendirmesi için uygun olması için cihazın çevrimiçi olması gerekir.
* Anahtar değişikliği başlatılmamış 30 dakika sonra aynı cihazı yetkilendirebilirsiniz.
* Anahtar değişikliğinin önceden yetkilendirilmiş cihaz tarafından başlatılmadığından farklı bir cihaza yetki verebilirsiniz. Yeni cihaz yetkilendirildikten sonra, eski cihaz değişikliği başlatamıyor.
* Hizmet veri şifreleme anahtarı geçişi devam ederken bir cihazı yetkilendiremezsiniz.
* Bir cihazı, hizmete kayıtlı cihazların bazıları, diğer kullanıcılar olmadığında şifrelemeyi geri alındığında yetkilendirebilirsiniz. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>2. Adım: hizmet veri şifreleme anahtarı değişikliğini başlatmak için StorSimple için Windows PowerShell kullanma
Bu adım yetkili StorSimple cihazında StorSimple için Windows PowerShell arabiriminde gerçekleştirilir.

> [!NOTE]
> Anahtar geçişi tamamlanana kadar StorSimple Manager hizmetinizin Azure portal hiçbir işlem gerçekleştirilemez.


Windows PowerShell arabirimine bağlanmak için cihaz seri konsolunu kullanıyorsanız aşağıdaki adımları gerçekleştirin.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Hizmet veri şifreleme anahtarı değişikliğini başlatmak için
1. Tam erişimle oturum açmak için 1 seçeneğini belirleyin.
2. Komut istemine şunları yazın:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Cmdlet başarıyla tamamlandıktan sonra yeni bir hizmet veri şifreleme anahtarı alacaksınız. Bu işlemin 3. adımında kullanılmak üzere bu anahtarı kopyalayın ve kaydedin. Bu anahtar, StorSimple Yöneticisi hizmetine kayıtlı tüm kalan cihazları güncelleştirmek için kullanılacaktır.
   
   > [!NOTE]
   > Bu işlem, StorSimple cihazını yetkilendirmek için dört saat içinde başlatılmalıdır.
   > 
   > 
   
   Bu yeni anahtar daha sonra hizmete kaydedilen tüm cihazlara gönderilmek üzere hizmete gönderilir. Daha sonra hizmet panosunda bir uyarı görüntülenir. Hizmet, kayıtlı cihazlardaki tüm işlemleri devre dışı bırakacak ve cihaz yöneticisinin diğer cihazlarda hizmet veri şifreleme anahtarını güncelleştirmesi gerekir. Ancak, g/ç (buluta veri gönderen konaklar) kesintiye uğramaz.
   
   Hizmetinize kayıtlı tek bir cihazınız varsa, geçiş işlemi tamamlanır ve sonraki adımı atlayabilirsiniz. Hizmetinize kayıtlı birden çok cihazınız varsa adım 3 ' e geçin.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>3. Adım: diğer StorSimple cihazlarında hizmet veri şifreleme anahtarını güncelleştirme
StorSimple Yöneticisi hizmetinize kayıtlı birden çok cihazınız varsa, bu adımların StorSimple cihazınızın Windows PowerShell arabiriminde gerçekleştirilmesi gerekir. 2. adımda elde ettiğiniz anahtarın, StorSimple Yöneticisi hizmetine kayıtlı olan tüm kalan StorSimple cihazını güncelleştirmek için kullanılması gerekir.

Cihazınızda hizmet veri şifrelemesini güncelleştirmek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Fiziksel cihazlarda hizmet veri şifreleme anahtarını güncelleştirmek için
1. Konsola bağlanmak için StorSimple için Windows PowerShell kullanın. Tam erişimle oturum açmak için 1 seçeneğini belirleyin.
2. Komut isteminde şunu yazın:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. 2. adımda elde ettiğiniz hizmet veri şifreleme anahtarını sağlayın [: hizmet verileri şifreleme anahtarı değişikliğini başlatmak için StorSimple için Windows PowerShell kullanın](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Tüm 8010/8020 bulut gereçlerinde hizmet veri şifreleme anahtarını güncelleştirmek için
1. [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell betiğini indirip kurun. 
2. PowerShell 'i açın ve komut isteminde şunu yazın:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Bu betik, hizmet veri şifreleme anahtarının, cihaz yöneticisi altındaki tüm 8010/8020 bulut gereçlerinde ayarlanmış olmasını sağlayacaktır.