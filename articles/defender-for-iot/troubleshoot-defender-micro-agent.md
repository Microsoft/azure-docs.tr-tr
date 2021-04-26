---
title: Defender IoT mikro aracı sorunlarını giderme (Önizleme)
description: Beklenmeyen veya açıklanamayan hataları nasıl işleyeceğinizi öğrenin.
ms.date: 4/5/2021
ms.topic: reference
ms.openlocfilehash: 3d52c4093c01d7e449c68b1c8143249b51f7061a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011429"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Defender IoT mikro aracı sorunlarını giderme (Önizleme)

Beklenmeyen bir hata oluşursa, bu sorun giderme yöntemlerini sorunu çözme girişiminde kullanabilirsiniz. Gerektiğinde yardım için IoT ürün ekibine yönelik Azure Defender 'a da ulaşabilirsiniz.   

## <a name="service-status"></a>Hizmet durumu 

Hizmetin durumunu görüntülemek için: 

1. Aşağıdaki komutu çalıştırın

    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```

1. Hizmetin kararlı olduğundan ve işlemdeki çalışma süresinin uygun olduğundan emin olun `active` .

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="Etkin olduğunu ve çalışma süresinin uygun olup olmadığını denetleyerek hizmetinizin kararlı olduğundan emin olun.":::

Hizmet olarak listeleniyorsa `inactive` , hizmeti başlatmak için aşağıdaki komutu kullanın:

```azurecli
systemctl start defender-iot-micro-agent.service 
```

Hizmetin kilitlenmediğini, işlemin çalışma süresinin 2 dakikadan daha kısa olduğunu bilirsiniz. Bu sorunu çözmek için [günlükleri gözden geçirmeniz](#review-the-logs)gerekir.

## <a name="validate-micro-agent-root-privileges"></a>Mikro aracı kök ayrıcalıklarını doğrula

Defender IoT mikro Aracı hizmetinin kök ayrıcalıklarla çalıştığını doğrulamak için aşağıdaki komutu kullanın.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="IoT mikro aracı hizmeti için Defender 'ın kök ayrıcalıklarla çalıştığını doğrulayın.":::
## <a name="review-the-logs"></a>Günlükleri gözden geçirin 

Günlükleri gözden geçirmek için aşağıdaki komutu kullanın:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

### <a name="quick-log-review"></a>Hızlı günlük incelemesi

Mikro aracı çalıştırıldığında bir sorun oluşursa, mikro aracıyı geçici bir durumda çalıştırabilirsiniz. Bu, aşağıdaki komutu kullanarak günlükleri görüntülemenize olanak sağlar:

```azurecli
sudo systectl stop defender-iot-micro-agent
cd /var/defender_iot_micro_agent/
sudo ./defender_iot_micro_agent
```

## <a name="restart-the-service"></a>Hizmeti yeniden başlatın

Hizmeti yeniden başlatmak için aşağıdaki komutu kullanın: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Sonraki adımlar

[Özellik desteği ve emekliliğinizi](edge-security-module-deprecation.md)inceleyin.
