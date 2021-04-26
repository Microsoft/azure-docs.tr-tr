---
title: StorSimple cihazınızı güncelleştirin | Microsoft Docs
description: Düzenli ve bakım modu güncelleştirmelerini ve düzeltmeleri yüklemek için StorSimple Güncelleştirme özelliğinin nasıl kullanılacağını açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: faf06775b78c5a7c90cea000ac0a1eb768107ef4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956849"
---
# <a name="update-your-storsimple-8000-series-device"></a>StorSimple 8000 serisi cihazınızı güncelleştirin
> [!NOTE]
> StorSimple için klasik portal kullanım dışıdır. StorSimple Cihaz Yöneticileriniz, yeni Azure portalına kullanımdan kaldırma zamanlamasına göre otomatik olarak taşınacaktır. Bu taşımayla ilgili bir e-posta ve portal bildirimi alacaksınız. Bu belge de yakında kullanımdan kaldırılacaktır. Taşıma hakkında tüm sorularınız için bkz. [SSS: Azure portalına taşıma](./index.yml).

## <a name="overview"></a>Genel Bakış
StorSimple güncelleştirmeleri Özellikleri, StorSimple cihazınızı kolayca güncel tutmanızı sağlar. Güncelleştirme türüne bağlı olarak, klasik Azure portalı aracılığıyla veya Windows PowerShell arabirimi aracılığıyla cihaza güncelleştirmeleri uygulayabilirsiniz. Bu öğretici, güncelleştirme türlerini ve bunların her birinin nasıl yükleneceğini açıklar.

İki tür cihaz güncelleştirmesini uygulayabilirsiniz: 

* Düzenli (veya normal mod) güncelleştirmeler
* Bakım modu güncelleştirmeleri

Klasik Azure portalı veya Windows PowerShell aracılığıyla düzenli güncelleştirmeleri yükleyebilirsiniz; Ancak, bakım modu güncelleştirmelerini yüklemek için Windows PowerShell kullanmanız gerekir. 

Her güncelleştirme türü, aşağıda ayrı olarak açıklanmıştır.

### <a name="regular-updates"></a>Normal güncelleştirmeler
Normal güncelleştirmeler cihaz normal moddayken yüklenebilen, kesintiye uğramayan güncelleştirmelerdir. Bu güncelleştirmeler, her cihaz denetleyicisine Microsoft Update Web sitesi aracılığıyla uygulanır. 

> [!IMPORTANT]
> Güncelleştirme işlemi sırasında bir denetleyici yük devretmesi meydana gelebilir. Ancak, bu sistem kullanılabilirliğini veya işlemini etkilemez.
> 
> 

* Klasik Azure portalı aracılığıyla düzenli güncelleştirmelerin nasıl yükleneceğine ilişkin ayrıntılar için bkz. [Klasik Azure portalı aracılığıyla düzenli güncelleştirmeleri yüklemek](#install-regular-updates-via-the-azure-classic-portal).
* Ayrıca, StorSimple için Windows PowerShell aracılığıyla düzenli güncelleştirmeler yükleyebilirsiniz. Ayrıntılar için bkz. [StorSimple için Windows PowerShell aracılığıyla düzenli güncelleştirmeleri yüklemeyi](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Bakım modu güncelleştirmeleri
Bakım modu güncelleştirmeleri, disk üretici yazılımı yükseltmeleri gibi karışıklığa neden olan güncelleştirmelerdir. Bu güncelleştirmeler cihazın bakım moduna girmesini gerektirir. Ayrıntılar için bkz. [2. Adım: bakım modunu girme](#step2). Bakım modu güncelleştirmelerini yüklemek için klasik Azure portalını kullanamazsınız. Bunun yerine StorSimple için Windows PowerShell kullanmanız gerekir. 

Bakım modu güncelleştirmelerinin nasıl yükleneceğine ilişkin ayrıntılar için bkz. [StorSimple için Windows PowerShell aracılığıyla bakım modu güncelleştirmelerini yüklemek](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Bakım modu güncelleştirmelerinin her denetleyiciye ayrı ayrı uygulanması gerekir. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Klasik Azure portalı aracılığıyla düzenli güncelleştirmeleri yükler
StorSimple cihazınıza güncelleştirme uygulamak için klasik Azure portalını kullanabilirsiniz.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>StorSimple için Windows PowerShell aracılığıyla düzenli güncelleştirmeleri yükler
Alternatif olarak, StorSimple için Windows PowerShell normal (normal mod) güncelleştirmeleri uygulamak için de kullanabilirsiniz.

> [!IMPORTANT]
> StorSimple için Windows PowerShell kullanarak düzenli güncelleştirmeleri yükleyebilseniz de, düzenli olarak klasik Azure portalı üzerinden düzenli güncelleştirmeler yüklemenizi öneririz. Güncelleştirme 1 ' den başlayarak, portaldan güncelleştirmeleri yüklemeden önce ön denetimler gerçekleştirilecek. Bu ön denetimler hatalara ve daha sorunsuz bir deneyim sağlamaya çalışır. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Bakım modu güncelleştirmelerini StorSimple için Windows PowerShell aracılığıyla yüklensin
StorSimple cihazınıza bakım modu güncelleştirmeleri uygulamak için StorSimple için Windows PowerShell kullanırsınız. Tüm g/ç istekleri bu modda duraklatıldı. Geçici olmayan rastgele erişim belleği (NVRAM) veya kümeleme hizmeti gibi hizmetler de durdurulur. Bu moddan çıktığınızda veya çıkarken her iki denetleyici de yeniden başlatılır. Bu moddan çıktığınızda, tüm hizmetler sürdürülür ve sağlıklı olmalıdır. (Bu işlem birkaç dakika sürebilir.)

Bakım modu güncelleştirmelerini uygulamanız gerekiyorsa, klasik Azure portalı aracılığıyla yüklenmesi gereken güncelleştirmeler olduğunu belirten bir uyarı alırsınız. Bu uyarı, güncelleştirmeleri yüklemek için StorSimple için Windows PowerShell kullanma yönergelerini içerir. Cihazınızı güncelleştirdikten sonra, cihazı normal moda değiştirmek için aynı yordamı kullanın. Adım adım yönergeler için bkz. [4. Adım: bakım modundan çıkma](#step4).

> [!IMPORTANT]
> * Bakım modunu girmeden önce, klasik Azure portalındaki **bakım** sayfasında **Donanım durumunu** denetleyerek her iki cihaz denetleyicisinin de sağlıklı olduğunu doğrulayın. Denetleyici sağlıklı değilse, sonraki adımlar için Microsoft Desteği başvurun. Daha fazla bilgi için, Iletişim Microsoft Desteği gidin. 
> * Bakım modundayken, güncelleştirmeyi önce bir denetleyicide, sonra da diğer denetleyiciye uygulamanız gerekir.
> 
> 

### <a name="step-1-connect-to-the-serial-console"></a>1. Adım: seri konsoluna bağlanma <a name="step1"></a>
İlk olarak, seri konsoluna erişmek için PuTTY gibi bir uygulama kullanın. Aşağıdaki yordamda, seri konsoluna bağlanmak için PuTTY 'nin nasıl kullanılacağı açıklanmaktadır.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode"></a>2. Adım: bakım modunu girin <a name="step2"></a>
Konsola bağlandıktan sonra, yüklenecek güncelleştirmeler olup olmadığını belirledikten sonra bunları yüklemek için bakım modunu girin.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates"></a>3. Adım: güncelleştirmelerinizi yüklemeyi <a name="step3"></a>
Ardından, güncelleştirmelerinizi yükledikten sonra.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode"></a>4. Adım: bakım modundan çıkma <a name="step4"></a>
Son olarak, bakım modundan çıkın.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Düzeltmeleri StorSimple için Windows PowerShell aracılığıyla yüklensin
Microsoft Azure StorSimple güncelleştirmelerinin aksine, düzeltmeler paylaşılan bir klasörden yüklenir. Güncelleştirmelerde olduğu gibi, iki tür düzeltme vardır: 

* Normal düzeltmeler 
* Bakım modu düzeltmeleri  

Aşağıdaki yordamlarda, düzenli ve bakım modu düzeltmelerini yüklemek için StorSimple için Windows PowerShell nasıl kullanılacağı açıklanmaktadır.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Cihazın fabrika sıfırlamasını yaparsanız güncelleştirmeler ne olur?
Bir cihaz fabrika ayarlarına sıfırlanırsa, tüm güncelleştirmeler kaybedilir. Fabrika sıfırlama cihazı kaydedildikten ve yapılandırıldıktan sonra, güncelleştirmeleri klasik Azure portalı ve/veya StorSimple için Windows PowerShell el ile yüklemeniz gerekir. Fabrika Sıfırlaması hakkında daha fazla bilgi için bkz. [cihazı fabrika varsayılan ayarlarına sıfırlama](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Sonraki adımlar
* [StorSimple cihazınızı yönetmek için StorSimple için Windows PowerShell kullanma](./storsimple-8000-windows-powershell-administration.md)hakkında daha fazla bilgi edinin.
* [StorSimple cihazınızı yönetmek Için StorSimple Yöneticisi hizmetini kullanma](./storsimple-8000-manager-service-administration.md)hakkında daha fazla bilgi edinin.