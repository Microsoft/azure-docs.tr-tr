---
title: StorSimple cihaz modunu değiştirme | Microsoft Docs
description: StorSimple cihaz modlarını açıklar ve cihaz modunu değiştirmek için StorSimple için Windows PowerShell kullanmayı açıklar.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: c7b0ea489c1d70ab86d677aad666ea6728fa76b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017092"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>StorSimple cihazınızda cihaz modunu değiştirme

Bu makalede, StorSimple cihazınızın çalışabilecebileceği çeşitli modların kısa bir açıklaması sağlanmaktadır. StorSimple cihazınız üç modda çalışabilir: normal, bakım ve kurtarma.

Bu makaleyi okuduktan sonra şunları bilirsiniz:

* StorSimple cihaz modları
* StorSimple cihazının hangi modda olduğunu anlamak
* Normalin içinden bakım moduna geçme ve *tam tersi*

Yukarıdaki yönetim görevleri yalnızca StorSimple cihazınızın Windows PowerShell arabirimi aracılığıyla gerçekleştirilebilir.

## <a name="about-storsimple-device-modes"></a>StorSimple cihaz modları hakkında

StorSimple cihazınız normal, bakım veya kurtarma modunda çalışabilir. Bu modların her biri aşağıda kısaca açıklanmıştır.

### <a name="normal-mode"></a>Normal mod

Bu, tam olarak yapılandırılmış bir StorSimple cihazı için normal işletimsel mod olarak tanımlanır. Varsayılan olarak, cihazınızın normal modda olması gerekir.

### <a name="maintenance-mode"></a>Bakım modu

Bazen StorSimple cihazının bakım moduna yerleştirilmesi gerekebilir. Bu mod, cihazda bakım gerçekleştirmenize ve disk üretici yazılımı ile ilgili olanlar gibi kesintiye uğratan güncelleştirmeler yüklemenize olanak tanır.

Sistemi bakım moduna yalnızca StorSimple için Windows PowerShell aracılığıyla yerleştirebilirsiniz. Tüm g/ç istekleri bu modda duraklatıldı. Geçici olmayan rastgele erişim belleği (NVRAM) veya kümeleme hizmeti gibi hizmetler de durdurulur. Bu moddan çıktığınızda veya çıkarken her iki denetleyici de yeniden başlatılır. Bakım modundan çıktığınızda, tüm hizmetler sürdürülür ve sağlıklı olmalıdır. Bu birkaç dakika sürebilir.

> [!NOTE]
> **Bakım modu yalnızca düzgün çalışan bir cihazda desteklenir. Denetleyicilerin bir veya her ikisinin de çalışmadığı bir cihazda desteklenmez.**


### <a name="recovery-mode"></a>Kurtarma modu

Kurtarma modu, "ağ desteğiyle Windows güvenli mod" olarak açıklanabilir. Kurtarma modu Microsoft Desteği ekibine geçirir ve sistem üzerinde tanılama gerçekleştirmesine izin verir. Kurtarma modunun birincil hedefi, sistem günlüklerini almak için kullanılır.

Sisteminiz kurtarma moduna geçtiğinde, sonraki adımlar için Microsoft Desteği başvurmanız gerekir. Daha fazla bilgi için, [iletişim Microsoft desteği](storsimple-8000-contact-microsoft-support.md)gidin.

> [!NOTE]
> **Cihazı kurtarma moduna yerleştirebilirsiniz. Cihaz hatalı durumdaysa kurtarma modu, cihazı Microsoft Desteği personelinin inceleyeceği bir duruma almaya çalışır.**

## <a name="determine-storsimple-device-mode"></a>StorSimple cihaz modunu belirleme

#### <a name="to-determine-the-current-device-mode"></a>Geçerli cihaz modunu belirleme

1. [Cihaz seri konsoluna bağlanmak Için PuTTY kullanma](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)bölümündeki adımları izleyerek cihaz seri konsolunda oturum açın.
2. Cihazın seri konsol menüsündeki Başlık iletisine bakın. Bu ileti, cihazın bakım veya kurtarma modunda olup olmadığını açıkça gösterir. İleti, sistem moduyla ilgili belirli bilgileri içermiyorsa, cihaz normal moddadır.

## <a name="change-the-storsimple-device-mode"></a>StorSimple cihaz modunu değiştirme

Bakım yapmak veya bakım modu güncelleştirmelerini yüklemek için StorSimple cihazını bakım moduna (normal moddan) yerleştirebilirsiniz. Bakım modunu girmek veya çıkmak için aşağıdaki yordamları gerçekleştirin.

> [!IMPORTANT]
> Bakım modunu girmeden önce, Azure portal cihaz **ayarlarına > cihaz ayarlarına** erişerek her iki cihaz denetleyicisinin sağlıklı olduğunu doğrulayın. Denetleyicilerin biri veya her ikisi iyi durumda değilse, sonraki adımlar için Microsoft Desteği başvurun. Daha fazla bilgi için, [iletişim Microsoft desteği](storsimple-8000-contact-microsoft-support.md)gidin.
 

#### <a name="to-enter-maintenance-mode"></a>Bakım moduna girmek için

1. [Cihaz seri konsoluna bağlanmak Için PuTTY kullanma](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)bölümündeki adımları izleyerek cihaz seri konsolunda oturum açın.
2. Seri konsol menüsünde, seçenek 1 ' i seçin, **tam erişim Ile oturum açın**. İstendiğinde, **Cihaz Yöneticisi parolasını** belirtin. Varsayılan parola: `Password1` .
3. Komut istemine şunu yazın: 
   
    `Enter-HcsMaintenanceMode`
4. Bakım modunun tüm g/ç isteklerini kesintiye uğratabileceğini ve Azure portal bağlantıyı kullandığını bildiren bir uyarı iletisi görürsünüz ve sizden onay istenir. Bakım moduna girmek için **Y** yazın.
5. Her iki denetleyici de yeniden başlatılır. Yeniden başlatma tamamlandığında, seri konsol başlığı cihazın bakım modunda olduğunu gösterir. Örnek çıktı aşağıda gösterilmiştir.

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>Bakım modundan çıkmak için

1. Cihaz seri konsolunda oturum açın. Cihazınızın bakım modunda olduğunu belirten başlık iletisinden emin olun.
2. Komut istemine şunları yazın:
   
    `Exit-HcsMaintenanceMode`
3. Bir uyarı iletisi ve bir onay iletisi görüntülenir. Bakım modundan çıkmak için **Y** yazın.
4. Her iki denetleyici de yeniden başlatılır. Yeniden başlatma tamamlandığında seri konsol başlığı, cihazın normal modda olduğunu gösterir. Örnek çıktı aşağıda gösterilmiştir.

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>Sonraki adımlar

StorSimple cihazınızda [normal ve bakım modu güncelleştirmelerini nasıl uygulayacağınızı](storsimple-update-device.md) öğrenin.

