---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67188406"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Bakım modu güncelleştirmelerini StorSimple için Windows PowerShell aracılığıyla yüklemek için
1. Henüz yapmadıysanız, cihaz seri konsoluna erişin ve 1 seçeneğini belirleyin, **tam erişimle oturum açın**. 
2. Parolayı yazın. Varsayılan parola **Parola1**' dir.
3. Komut istemine şunları yazın:
   
     `Get-HcsUpdateAvailability` 
4. Güncelleştirmeler varsa ve güncelleştirmelerin karışıklığa veya kesintiye uğramamış olup olmadığı hakkında bilgi verilecektir. Kesintiye uğratan güncelleştirmeler uygulamak için cihazı bakım moduna almanız gerekir. Yönergeler için bkz. [2. Adım: bakım modunu girin](../articles/storsimple/storsimple-update-device.md#step2) .
5. Cihazınız bakım modundayken, komut isteminde şunu yazın: `Start-HcsUpdate`
6. Onayınız istenir. Güncelleştirmeleri doğruladıktan sonra, şu anda eriştiğiniz denetleyiciye yüklenir. Güncelleştirmeler yüklendikten sonra denetleyici yeniden başlatılır. 
7. Güncelleştirmelerin durumunu izleyin. Şunu yazın:
   
    `Get-HcsUpdateStatus`
   
    `RunInProgress`İse `True` güncelleştirme hala devam etmektedir. `RunInProgress`İse `False` , güncelleştirmenin tamamlandığını gösterir.  
8. Güncelleştirme geçerli denetleyiciye yüklendiğinde ve yeniden başlatıldığında, diğer denetleyiciye bağlanın ve 1 ile 6 arasındaki adımları gerçekleştirin.
9. Her iki denetleyici güncelleştirildikten sonra bakım modundan çıkın. Yönergeler için bkz. [4. Adım: bakım modundan çıkma](../articles/storsimple/storsimple-update-device.md#step4) .

