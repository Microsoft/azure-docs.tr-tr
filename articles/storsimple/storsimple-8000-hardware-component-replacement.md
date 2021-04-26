---
title: StorSimple 8000 serisi donanım bileşeni değiştirme | Microsoft Docs
description: Bir StorSimple cihazının PCMs, pil, denetleyici modülleri, EBOD denetleyicilerini, disk sürücülerinizi ve kasalarını güvenle nasıl değiştireceğiniz açıklanır.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 12ab5a9598cc0222f5a3e64985be2e2ea9e7e2fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014865"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>StorSimple 8000 serisi cihazındaki bir donanım bileşenini değiştirme

## <a name="overview"></a>Genel Bakış
Donanım bileşeni değiştirme öğreticileri Microsoft Azure StorSimple 8000 serisi cihazınızın donanım bileşenlerini ve bunları kaldırmak ve değiştirmek için gereken adımları anlatmaktadır. Bu makalede güvenlik simgeleri açıklanmakta, ayrıntılı öğreticilere işaretçiler sağlanmıştır ve değiştirilebilen bileşenler listelenir.

> [!IMPORTANT]
> Herhangi bir StorSimple bileşenini kaldırmaya veya değiştirmeye çalışmadan önce, [Güvenlik simgesi kurallarını](#safety-icon-conventions) ve diğer [güvenlik önlemlerini](storsimple-8000-safety.md)gözden geçirdiğinizden emin olun.

### <a name="safety-icon-conventions"></a>Güvenlik simgesi kuralları

Aşağıdaki tabloda, bu öğreticilerde kullanılan güvenlik simgeleri açıklanmaktadır. Cihaz bileşenlerini kaldırma ve değiştirme adımlarını izleyerek bu güvenlik simgelerine yakın bir şekilde dikkat edin.

| Simge | Metin | Ek bilgiler |
|:--- |:--- |:--- |
| ![Uyarı simgesi](./media/storsimple-hardware-component-replacement/Warning.png) |**OLMA TEHLIKESI!** |Önedilmediği durumlarda, ölüm veya ciddi bir yaralama oluşmasına neden olan tehlikeli bir durum belirtir. Bu sinyal sözcüğü, en Extreme durumlarla sınırlıdır. |
| ![Uyarı simgesi](./media/storsimple-hardware-component-replacement/Warning.png) |**WARNING!** |Önedilmediği durumlarda, ölüm veya ciddi bir yaralama oluşmasına neden olabilecek tehlikeli bir durum belirtir. |
| ![Uyarı simgesi](./media/storsimple-hardware-component-replacement/Caution.png) |**DIKKATLI!** |Önedilmediği durumlarda küçük veya orta bir yaralanmaya neden olabilecek tehlikeli bir durum belirtir. |
| ![Bildirim simgesi](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**DEĞIŞTIRILEBILECEĞINI** |Önemli kabul edilen, ancak tehlike ile ilgili olmayan bilgileri gösterir. |
| ![Elektrik sarsıntı simgesi](./media/storsimple-hardware-component-replacement/Electric.png) |**Elektrik şok hasar** |Yüksek voltaj gösterir. |
| ![Ağır ağırlık simgesi](./media/storsimple-hardware-component-replacement/Weight.png) |**Ağır ağırlık** | |
| ![Kullanıcı hizmet verebilir parçası yok simgesi](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Hiçbir Kullanıcı hizmet konusu parçası yok** |Uygun şekilde Eğitilmediğiniz müddetçe bu erişimi kullanmayın. |
| ![Yönergeleri oku simgesi](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Önce tüm yönergeleri okuyun** | |
| ![İpucu rastlantı simgesi](./media/storsimple-hardware-component-replacement/TipHazard.png) |**İpucu hasar** | |

### <a name="before-you-begin"></a>Başlamadan önce

Bu öğreticide, cihazınız ve güvenlik Simgeleriniz hakkındaki güvenlik bilgilerini öğrenmeye çalışın. Tüm bilgiler için [, güvenli bir şekilde yüklemek ve StorSimple cihazınızı çalıştırmak](storsimple-8000-safety.md) sayfasına gidin. StorSimple cihazınızı oluşturmadan önce [güvenlik önlemlerinizi](storsimple-8000-safety.md#handling-precautions) gözden geçirdiğinizden emin olun.

Bir bileşeni değiştirmeyi denemeden önce aşağıdaki bilgileri göz önünde bulundurun.

![Uyarı simgesi ](./media/storsimple-hardware-component-replacement/Warning.png) ![ elektrik sarsıntı simgesi ](./media/storsimple-hardware-component-replacement/Electric.png) **uyarısı!**

* StorSimple cihazınızın modüllerini ve bileşenlerini işlerken, elektrostatik bir ücret veya antistatik bir işlem kullanarak kendinizi doğru şekilde yapın.
* Herhangi bir devresi dokunmayın. Bir devre ortaya çıkarabilecek bileşenleri işlerken sağlanan tutamaçları ve kılavuzlarını kullanın.

![Uyarı simgesi ](./media/storsimple-hardware-component-replacement/Warning.png) ![ bildirim simgesi ](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **bildirimi:**

Bir modülü değiştirdiğinizde, **hiçbir zaman kutunun arkada boş bir yuva bırakmayın**. Sorun parçasını kaldırmadan önce bir değiştirme veya boş modül alın.

## <a name="hardware-component-replacement-procedures"></a>Donanım bileşeni değiştirme yordamları

StorSimple 8000 serisi cihazınız, birincil ve/veya EBOD kasaları içindeki çeşitli eklenti modüllerinden oluşur. 8100, tek bir birincil muhafaza içerir, ancak 8600 birincil bir kutu ve EBOD kasası içeren çift bir muhafaza aygıtıdır.

Cihazınızdaki ana donanım bileşenleri aşağıdaki tablolarda özetlenmektedir. İlgili öğreticiye gitmek için **değiştirme yordamı** sütunundaki bağlantıya tıklayın.

| Bileşenler | # Var | Eklenti modülü | Değiştirme yordamı |
|:--- |:--- |:--- |:--- |
| Kasa |1 |No |[StorSimple cihazınızda kasayı değiştirme](storsimple-8000-chassis-replacement.md) |
| Birincil denetleyiciler |2 |Yes |[StorSimple cihazınızda bir denetleyici modülünü değiştirme](storsimple-8000-controller-replacement.md) |
| 764W güç ve soğutma modülleri (PCMs) |2 |Yes |[StorSimple cihazınızın güç ve soğutma modülünü değiştirme](storsimple-8000-power-cooling-module-replacement.md) |
| Yedekleme pili |2 |Yes |[StorSimple cihazınızın yedek pil modülünü değiştirme](storsimple-8000-battery-replacement.md) |
| Disk sürücüleri |12 |Yes |[StorSimple cihazınızda bir disk sürücüsünü değiştirme](storsimple-8000-disk-drive-replacement.md) |

**Tablo 1** Birincil kasada donanım bileşenleri

Birincil kutu ve EBOD Kasası, g/ç modülleriyle farklılık gösterir. Ayrıca, PCMs 'de farklı bir wattage vardır. Birincil kasadaki PCMs 764 W, ancak EBOD muhafazaları 580 W ' tır. Birincil kasadaki PCMs, yedekleme pil modülünü de içerir.

| Bileşenler | # Var | Eklenti modülü | Değiştirme yordamı |
|:--- |:--- |:--- |:--- |
| Kasa |1 |No |[StorSimple cihazınızda kasayı değiştirme](storsimple-8000-chassis-replacement.md) |
| EBOD denetleyicileri |2 |Yes |[StorSimple cihazınızda bir EBOD denetleyicisini değiştirme](storsimple-8000-ebod-controller-replacement.md) |
| 580W güç ve soğutma modülleri (PCMs) |2 |Yes |[StorSimple cihazınızın güç ve soğutma modülünü değiştirme](storsimple-8000-power-cooling-module-replacement.md) |
| Disk sürücüleri |12 |Yes |[StorSimple cihazınızda bir disk sürücüsünü değiştirme](storsimple-8000-disk-drive-replacement.md) |

**Tablo 2** EBOD muhafazasında donanım bileşenleri

Cihazdaki eklenti modülleri aşağıdaki ön ve arka diyagramlarda vurgulanır. Bu diyagramları, bir değiştirme gerekliyse çeşitli eklenti modüllerinin konumunu tespit etmek için kullanabilirsiniz. Ön diyagram, disk sürücüleri ve EBOD kasası ve birincil kutunun arka şemaları, eklenti modüllerini gösterir.

![Disk sürücüleri olan cihazın ön bölmesi](./media/storsimple-hardware-component-replacement/IC741028.png)

**Şekil 1** Cihazın önü

| Etiketle | Description |
|:--- |:--- |
| 0 - 11 |Disk sürücüleri (Toplam 12) |

Hem birincil kutu hem de EBOD Kasası, sürücü taşıyıcı modüllerdir. Kasa on iki 3,5 "disk sürücüsü 3 x 4 biçimde düzenlenmiştir.

![Cihaz birincil kutu modüllerinin geri düzlemi](./media/storsimple-hardware-component-replacement/IC740994.png)

**Şekil 2** Birincil Kasası geri

| Etiketle | Açıklama |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Denetleyici 0 |
| 4 |Denetleyici 1 |

![Cihaz EBOD Kasası eklenti modüllerinin geri düzlemi](./media/storsimple-hardware-component-replacement/IC769599.png)

**Şekil 3** EBOD Kasası geri

| Etiketle | Açıklama |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD denetleyicisi 0 |
| 4 |EBOD denetleyicisi 1 |

## <a name="field-replaceable-units"></a>Alan değiştirilebilir birimler

StorSimple cihazınız için aşağıdaki alan değiştirilebilir birimler (FRU) kullanılabilir:

* Kasa (tümleşik işlemler paneli dahil)
* 764 W AC PCM
* 580 W AC PCM
* Sürücü taşıyıcısı modülü ile sabit disk sürücüsü
* Denetleyici modülü
* EBOD denetleyici modülü
* Yedekleme Pil modülü
* Raf bağlama demiryolu seti

Bu değiştirme birimlerinden herhangi birini sıralamak için lütfen [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md) .

## <a name="next-steps"></a>Sonraki adımlar

StorSimple donanım bileşenini değiştirmeyi denemeden önce tüm [güvenlik bilgilerini](storsimple-8000-safety.md) gözden geçirin.
