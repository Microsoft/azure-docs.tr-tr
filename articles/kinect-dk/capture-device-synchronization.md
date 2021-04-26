---
title: Azure Kinect cihaz eşitlemesini yakala
description: Azure Kinect algılayıcı SDK 'sını kullanarak Azure Kinect yakalama cihazlarını eşitlemeyi öğrenin.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, algılayıcı, SDK, derinlik, RGB, dahili, dış, eşitleme, zincirleme zinciri, aşama boşluğu
ms.openlocfilehash: ce0c72d3d708d5696a9775b3885f278f0c23cac1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102034327"
---
# <a name="capture-azure-kinect-device-synchronization"></a>Azure Kinect cihaz eşitlemesini yakala

Azure Kinect donanımı, renk ve derinlik görüntülerinin yakalama süresini hizalayabilir. Aynı cihazdaki kameralar arasında hizalama **iç eşitdir**. Birden çok bağlı cihaz genelinde yakalama süresi hizalaması **dış eşitdir**. Mikrofon dizisi, renk ve derinlik kameralarından bağımsız olarak çalışır.

## <a name="device-internal-synchronization"></a>Cihaz iç eşitlemesi

Tek tek kameralar arasında görüntü yakalama, donanımda eşitlenir. Hem renk hem de derinlik algılayıcısındaki görüntüleri içeren her [k4a_capture_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__capture__t.html) , görüntülerin zaman damgaları donanımın işletim moduna göre hizalanır. Varsayılan olarak, bir yakalamanın görüntüleri, pozlama hizalanmış ortalamasıdır. Derinlik ve renk yakalamalarından oluşan göreli zamanlama, `depth_delay_off_color_usec` [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html)alanı kullanılarak ayarlanabilir.

## <a name="device-external-synchronization"></a>Cihaz dış eşitleme

Bkz. donanım kurulumu için [dış eşitlemeyi ayarlama](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices) .

Her bağlı cihaz için yazılım, **ana** veya **alt** modda çalışacak şekilde yapılandırılmalıdır. Bu ayar [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html)yapılandırılır.

Dış eşitleme kullanırken, zaman damgalarının doğru şekilde hizalanması için ana kameraların ana kadar her zaman başlatılması gerekir.

### <a name="subordinate-mode"></a>Alt mod

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_SUBORDINATE
```

### <a name="master-mode"></a>Ana mod

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_MASTER;
```

### <a name="retrieving-synchronization-jack-state"></a>Eşitleme jakı durumu alınıyor

Eşitleme giriş ve eşitleme çıkış jaklarına ait geçerli durumu programlı bir şekilde almak için [k4a_device_get_sync_jack](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0209ac87bfd055163677321b0304e962.html#ga0209ac87bfd055163677321b0304e962) işlevini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Artık cihaz eşitlemesini nasıl etkinleştireceğinizi ve yakaleyeceğinizi öğrenirsiniz. Ayrıca, nasıl kullanılacağını inceleyebilirsiniz 

>[!div class="nextstepaction"]
>[Azure Kinect algılayıcı SDK kaydı ve kayıttan yürütme API 'SI](record-playback-api.md)
