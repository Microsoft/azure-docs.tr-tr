---
title: StorSimple 8000 serisi cihazında VERI 0 ayarlarını değiştirme | Microsoft Docs
description: StorSimple cihazınızda DATA 0 ağ arabirimini yeniden yapılandırmak için StorSimple için Windows PowerShell nasıl kullanacağınızı öğrenin.
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
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 85d7114f419266124d0d23368b24700af025758a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961048"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>StorSimple 8000 serisi cihazınızda DATA 0 ağ arabirimi ayarlarını değiştirin

## <a name="overview"></a>Genel Bakış

Microsoft Azure StorSimple cihazınız, 0 ' dan fazla VERI 5 ' e kadar olan altı ağ arabirimine sahiptir. DATA 0 arabirimi her zaman Windows PowerShell arabirimi veya seri konsol aracılığıyla yapılandırılır ve otomatik olarak bulut etkindir. VERI 0 ağ arabirimini Azure portal aracılığıyla yapılandıramayacağınızı unutmayın.

DATA 0 arabirimi ilk olarak StorSimple cihazının ilk dağıtımı sırasında Kurulum Sihirbazı aracılığıyla yapılandırılır. Cihaz işlemsel moddayken, VERI 0 ayarlarını yeniden yapılandırmanız gerekebilir. Bu öğretici, VERI 0 ağ ayarlarını, her ikisi de StorSimple için Windows PowerShell değiştirmek için iki yöntem sunar.

Bu öğreticiyi okuduktan sonra şunları yapabilirsiniz:

* VERI 0 Ağ ayarını Kurulum Sihirbazı aracılığıyla değiştirme
* Cmdlet 'i aracılığıyla VERI 0 ağ ayarlarını değiştirme `Set-HcsNetInterface`

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>VERI 0 ağ ayarlarını Kurulum Sihirbazı aracılığıyla değiştirme
StorSimple cihazınızın Windows PowerShell arabirimine bağlanıp bir Kurulum Sihirbazı oturumu başlatarak VERI 0 ağ ayarlarını yeniden yapılandırabilirsiniz. VERI 0 ayarlarını değiştirmek için aşağıdaki adımları gerçekleştirin:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>VERI 0 ağ ayarlarını Kurulum Sihirbazı aracılığıyla değiştirmek için
1. Seri konsol menüsünde, seçenek 1 ' i seçin, **tam erişim Ile oturum açın**. İstendiğinde, **Cihaz Yöneticisi parolasını** belirtin. Varsayılan parola `Password1` .
2. Komut istemine şunları yazın:
   
    `Invoke-HcsSetupWizard`
3. Cihazınızın VERI 0 arabirimini yapılandırmaya yardımcı olmak üzere bir Kurulum Sihirbazı görüntülenir. IP adresi, ağ geçidi ve ağ maskesi için yeni değerler sağlayın.

> [!NOTE]
> Sabit denetleyiciler IP 'Lerinin, Azure portal StorSimple cihazının **ağ ayarları** dikey penceresi aracılığıyla yeniden yapılandırılması gerekir. Daha fazla bilgi için [ağ arabirimlerini değiştirme](storsimple-8000-modify-device-config.md#modify-network-interfaces)bölümüne gidin.

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Set-HcsNetInterface cmdlet 'ini kullanarak VERILERI 0 ağ ayarlarını değiştirme
DATA 0 ağ arabirimini yeniden yapılandırmak için alternatif bir yol, `Set-HcsNetInterface` cmdlet 'i kullanmaktır. Cmdlet 'i, StorSimple cihazınızın Windows PowerShell arabiriminden yürütülür. Bu yordamı kullanırken, denetleyici sabit IP 'Leri burada da yapılandırılabilir. VERI 0 ayarlarını değiştirmek için aşağıdaki adımları gerçekleştirin: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Set-HcsNetInterface cmdlet 'i aracılığıyla VERI 0 ağ ayarlarını değiştirmek için
1. Seri konsol menüsünde, seçenek 1 ' i seçin, **tam erişim Ile oturum açın**. İstendiğinde, Cihaz Yöneticisi parolasını belirtin. Varsayılan parola `Password1` .
2. Komut istemine şunları yazın:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    Açılı ayraçlar içinde, VERI 0 için aşağıdaki değerleri yazın:
   
   * IPv4 adresi
   * IPv4 ağ geçidi
   * IPv4 alt ağ maskesi
   * Denetleyici 0 için sabit IPv4 adresi
   * Denetleyici 1 için sabit IPv4 adresi
     
     Bu cmdlet 'in kullanımı hakkında daha fazla bilgi için [StorSimple için Windows PowerShell cmdlet başvurusuna](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630))gidin.

## <a name="next-steps"></a>Sonraki adımlar
* VERI 0 ' dan farklı ağ arabirimlerini yapılandırmak için [Azure Portal ağ ayarlarını yapılandırma](storsimple-8000-modify-device-config.md)' yı kullanabilirsiniz. 
* Ağ arabirimlerinizi yapılandırırken herhangi bir sorunla karşılaşırsanız [dağıtım sorunlarını giderme](./storsimple-8000-troubleshoot-deployment.md)bölümüne bakın.