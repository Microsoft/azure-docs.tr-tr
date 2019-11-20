---
title: Azure Değişiklik İzleme sorunlarını giderme
description: Bu makale, sorun giderme hakkında bilgi sağlar Değişiklik İzleme
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: bobbytreed
ms.author: robreed
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: beb0b89bdbf143c89a83c0813313a8bbda7235d4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564851"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Değişiklik İzleme ve Envanter sorunlarını giderme

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Senaryon Değişiklik İzleme kayıtları Windows makineleri için gösterilmiyor

#### <a name="issue"></a>Sorun

Değişiklik İzleme için eklendi olan Windows makineler için herhangi bir envanter veya Değişiklik İzleme sonucu görmezsiniz.

#### <a name="cause"></a>Nedeni

Bu hata aşağıdaki nedenlerden kaynaklanıyor olabilir:

1. **Microsoft Monitoring Agent** çalışmıyor
2. Otomasyon hesabına geri iletişim engelleniyor.
3. Değişiklik İzleme için yönetim paketleri indirilmez.
4. Eklendi olan VM, Microsoft Monitoring Agent yüklü olarak Sysprep uygulanmamış bir kopyalanmış makineden gelmiş olabilir.

#### <a name="resolution"></a>Çözüm

1. Makinede **Microsoft Monitoring Agent** (HealthService. exe) çalıştığını doğrulayın.
1. Makinedeki **Olay Görüntüleyicisi** denetleyin ve içindeki sözcüğe `changetracking` sahip tüm olayları arayın.
1. Değişiklik İzleme çalışması için hangi adreslere ve bağlantı noktalarına izin verileceğini öğrenmek için [ağ planlaması](../automation-hybrid-runbook-worker.md#network-planning) ' nı ziyaret edin.
1. Aşağıdaki Değişiklik İzleme ve stok yönetim paketlerinin yerel olarak mevcut olduğunu doğrulayın:
    * Microsoft. ıntelligencepacks. ChangeTrackingDirectAgent. *
    * Microsoft. ıntelligencepacks. ınventorychangetracking. *
    * Microsoft. ıntelligencepacks. Singletonınventorycollection. *
1. Kopyalanmış bir görüntü kullanılıyorsa, önce görüntüyü Sysprep yapın ve Microsoft Monitoring Agent aracısını yüklemeden sonra yükler.

Bu çözümler sorununuzu gidermezse ve desteğe başvurursanız, aracıdaki tanılamayı toplamak için aşağıdaki komutları çalıştırabilirsiniz

Aracı makinede, adresine `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` gidin ve aşağıdaki komutları çalıştırın:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Varsayılan olarak hata izleme etkindir, önceki örnekte olduğu gibi ayrıntılı hata iletilerini etkinleştirmek istiyorsanız parametresini kullanın `VER` . Bilgi izlemeleri için çağrılırken `INF` `StartTracing.cmd`kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* [Azure Forumları](https://azure.microsoft.com/support/forums/) aracılığıyla Azure uzmanlarından yanıtlar alın
* [@AzureSupport](https://twitter.com/azuresupport) hesabı ile bağlantı kurun. Bu resmi Microsoft Azure hesabı, müşteri deneyimini geliştirmek için Azure topluluğunu doğru kaynaklara ulaştırır: yanıtlar, destek ve uzmanlar.
* Daha fazla yardıma ihtiyacınız varsa, bir Azure destek olayı dosyası gönderebilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.
