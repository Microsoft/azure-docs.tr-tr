---
title: StorSimple cihazınız için güvenlik | Microsoft Docs
description: Güvenlik kurallarını, yönergeleri ve konuları açıklar ve StorSimple cihazınızı güvenle yüklemeyi ve çalıştırmayı açıklar.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: dae6d535-1ca2-4d2b-b221-6819043aa068
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: alkohli
ms.openlocfilehash: 4622a8575d7b6a38226ee3a980c05f143c128356
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68963529"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>StorSimple cihazınızı güvenle yükleyip çalıştırın

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

![Warning simge ](./media/storsimple-safety/IC740879.png)
 ![Read güvenlik bildirimi simgesi ](./media/storsimple-safety/IC740885.png) **okuma GÜVENLIĞI ve SISTEM durumu bilgileri**

Bu makaledeki Microsoft Azure StorSimple cihazınız için geçerli olan tüm güvenlik ve sistem durumu bilgilerini okuyun. Daha sonra başvurmak üzere StorSimple aygıtınızla birlikte gelen tüm yazdırılmış kılavuzlarınızı saklayın. Bu ürün için yönergeleri izlemeden ve düzgün şekilde ayarlanamaması ve bu ürüne yönelik doğru kurulum, kullanım ve bakım riski, ciddi bir sorun veya ölüm riskini artırabilir ya da cihaz ya da cihazlara zarar verebilir. [Bu kılavuzun indirilebilir bir sürümü](https://www.microsoft.com/download/details.aspx?id=44233) de mevcuttur.

## <a name="safety-icon-conventions"></a>Güvenlik simgesi kuralları
Microsoft Azure StorSimple cihazınızı kurarken ve çalıştırırken gözlenecek güvenlik önlemlerinizi gözden geçirdikten sonra bulacağınız simgeler aşağıda verilmiştir.

| Simg | Açıklama |
|:--- |:--- |
| ![Danger simge ](./media/storsimple-safety/IC740879.png) **Danger!** |Önedilmediği durumlarda, ölüm veya ciddi bir yaralama oluşmasına neden olan tehlikeli bir durum belirtir. Bu sinyal sözcüğü, en Extreme durumlarla sınırlı olacaktır. |
| ![Warning simge ](./media/storsimple-safety/IC740879.png) **uyarısı!** |Önedilmediği durumlarda, ölüm veya ciddi bir yaralama oluşmasına neden olabilecek tehlikeli bir durum belirtir. |
| ![Warning simge ](./media/storsimple-safety/IC740879.png) **dikkatli!** |Önedilmediği durumlarda küçük veya orta bir yaralanmaya neden olabilecek tehlikeli bir durum belirtir. |
| ![Notice simge ](./media/storsimple-safety/IC740881.png) **bildirimi:** |Önemli kabul edilen, ancak tehlike ile ilgili olmayan bilgileri gösterir. |
| ![Electrical sarsıntı simgesi ](./media/storsimple-safety/IC740882.png) **elektrik sarsıntı** |Yüksek voltaj |
| ![Heavy kalınlığı simgesi ](./media/storsimple-safety/IC740883.png) **ağır ağırlık** | |
| Kullanıcı serviceable **parçalarını ![No ](./media/storsimple-safety/IC740879.png) Kullanıcı** |Uygun şekilde Eğitilmediğiniz müddetçe bu erişimi kullanmayın. |
| ![Read güvenlik bildirimi simgesi](./media/storsimple-safety/IC740885.png)**önce tüm yönergeleri oku** | |
| ![Tip rastlantı simgesi ](./media/storsimple-safety/IC740886.png) **Ipucu rastlantı** | |

## <a name="handling-precautions"></a>Önlemleri işleme
![Warning simge ](./media/storsimple-safety/IC740879.png) ![Heavy ağırlık simgesi ](./media/storsimple-safety/IC740883.png) **Uyarı!** 

Yaralanma riskini azaltmak için:

* Tam olarak yapılandırılmış bir kutu 32 kg 'a (70 lbs) ağırlık verebilir; kendiniz de kaldırma denemeyin.
* Kasası taşımadan önce her zaman ağırlığı işlemek için iki kişinin kullanılabilir olduğundan emin olun. Bu ağırlığı kaldırmaya çalışan bir kişinin, sakatlama 'e dokunması gerektiğini unutmayın.
* Kapsamı, birimin arkada bulunan güç ve soğutma modüllerindeki (PCMs) tanıtıcılarla kaldırmayın. Bunlar ağırlığı alacak şekilde tasarlanmamıştır.

## <a name="connection-precautions"></a>Bağlantı önlemleri
![Warning simge ](./media/storsimple-safety/IC740879.png) sarsıntı simgesi ![Electrical ](./media/storsimple-safety/IC740882.png) **uyarısı!**

Yaralanma, elektrik sarsıntı veya ölüm olasılığını azaltmak için:

* Birden çok AC kaynağı tarafından güçlendirilmiştir, tüm yalıtımın tüm yalıtımının bağlantısını kesin.
* Birimi Taşımadan önce kalıcı olarak çıkarın veya herhangi bir şekilde hasar gördüğünü düşünün.
* Güç kaynağı kapakları ile güvenli bir elektrik dünya bağlantısı sağlayın. Kutunun grokinin, güç uygulamadan önce ulusal ve yerel gereksinimleri karşıladığından emin olun.
* Bir PCM 'den bir PCM kaldırılmadan önce güç bağlantısının her zaman bağlantısının kesildiğinden emin olun.
* Güç kaynağı kablosu ana bağlantı kesme aygıtı olduğu için, dış cihazların donanımların yakınında bulunduğundan ve kolayca erişilebilir olduğundan emin olun.

![Warning simge ](./media/storsimple-safety/IC740879.png) sarsıntı simgesi ![Electrical ](./media/storsimple-safety/IC740882.png) **uyarısı!**

Elektrik bağlantılarından aşırı Isıtma veya yangın olasılığını azaltmak için:

* Teknik belirtimde açıklanan gereksinimleri karşılamak için elektrik aşırı yüklemesi koruması ile uygun bir güç kaynağı sağlayın.
* Bifurta Power kablosu sayısı ("Y" müşteri adayları) kullanmayın.
* Geçerli güvenlik, egörev ve sıcaklık gereksinimleriyle uyum sağlamak için hiçbir kapsamaları kaldırılmalıdır ve tüm yuvaların eklenti modülleri veya sürücü boşlukları ile doldurulması gerekir.
* Ekipmanın üretici tarafından belirtilen bir şekilde kullanıldığından emin olun. Bu ekipman üretici tarafından belirtilmemiş bir şekilde kullanılıyorsa, ekipman tarafından sunulan koruma düşebilir.

![Notice simge ](./media/storsimple-safety/IC740881.png) **bildirimi:**

Ekipmanlarınızın doğru çalışması ve ürün hasarı engellemek için:

* Cihazın arkasında bulunan RJ45 bağlantı noktaları yalnızca Ethernet bağlantısı içindir. Bunların bir telekomünikasyon ağına bağlı olmaması gerekir.
* Cihazı, önden arkaya soğutma tasarımını barındırabilecek bir rafa yüklediğinizden emin olun.
* Tüm eklenti modülleri ve boş levhalar, sistem kasası 'nın bir parçasıdır. Bunlar yalnızca bir değiştirme hemen eklenebileceği zaman kaldırılmalıdır. Sistem tüm modüller veya boşluklar olmadan çalıştırılmamalıdır.

## <a name="rack-system-precautions"></a>Raf sistemi önlemleri
Cihazı bir raf dolabına bağladığınızda aşağıdaki güvenlik gereksinimlerinin göz önünde bulundurulmalıdır.

![Warning simge ](./media/storsimple-safety/IC740879.png) rastlantı simgesi ![Tip ](./media/storsimple-safety/IC740886.png) **uyarısı!**

Bir ucundan çok fazla yaralanma olasılığını azaltmak için:

* Raf tasarımı, yüklenen eklerin toplam ağırlığını desteklemelidir ve raf veya normal kullanım sırasında rafa veya bir şekilde kullanıma sunulmasını engellemek için uygun özellikleri sabitlemeye dahil edilmelidir.
* Bir raf yüklerken, bölmeyi yukarıdan aşağıya doğru ve boş olarak doldur.
* Rafa ortadan kaldırmak için bir seferde birden fazla kutusu her seferinde birden çok kez girmeyin.

![Warning simge ](./media/storsimple-safety/IC740879.png) sarsıntı simgesi ![Electrical ](./media/storsimple-safety/IC740882.png) **uyarısı!**

Yaralanma, elektrik sarsıntı veya ölüm olasılığını azaltmak için:

* Raf, güvenli bir elektrik dağıtım sistemine sahip olmalıdır. Bu, kutu için güncel koruma sağlamalıdır ve yüklenen toplam muhafaza sayısına göre aşırı yüklenmiş olmalıdır. Namelevha üzerinde gösterilen elektrik güç tüketimi derecelendirmesi gözlenmelidir.
* Elektrik dağıtım sistemi, raftaki her bir kutu için güvenilir bir zemin sağlamalıdır.
* Elektrik dağıtım sisteminin tasarımı, Tüm muhafazaların tüm güç kaynakları için geçerli olan toplam zemin sızıntısı olduğunu dikkate almalıdır. Her bir kasadaki her bir güç kaynağının, 60 Hz, 264 volt tarihinde en fazla 1,0 mA kadar geçerli olduğunu unutmayın. Raf, "yüksek LEAKAGE CURRENT" ile etiketleme gerektirebilir. Bir tedariği bağlamadan önce zemin (Dünya) bağlantısı gereklidir. "
* Kasaları, muhafazalarla yapılandırıldığında, UL 60950-1 ve ıEC 60950-1/EN 60950-1 güvenlik gereksinimlerini karşılamalıdır.

![Notice simge ](./media/storsimple-safety/IC740881.png) **bildirimi:**

Raf sisteminizin uygun soğutma için:

* Raf tasarımının 35 derece santigrat (95 derece Fahrenhayt) en yüksek muhafaza işletim çevresel sıcaklığını dikkate aldığından emin olun.
* Sistem, düşük basınç, arka arkaya yükleme (raf kapıları tarafından oluşturulan arka basınç ve 5 Pascal [0,5 mm su ölçer] ' ı aşmayacak şekilde) ile çalıştırılır.

## <a name="power-cooling-module-pcm-precautions"></a>Güç soğutma modülü (PCM) önlemleri
Cihaz iki PCMs ile çalışacak şekilde tasarlanmıştır. Her PCMs 'nin bir güç kaynağı ve çift eksenli bir fanı vardır. Kritik bir durum sırasında, Sistem normal işlemlere devam ederken bir güç kaynağı hatasına izin verir. İki PCMs (ve bu nedenle güç kaynakları) her zaman yüklenmelidir. Tek bir PCM, yedekli güç sağlamaz. Bu nedenle, bir PCM 'nin başarısız olması kapalı kalma süresine veya olası veri kaybına neden olabilir.

![Warning simge ](./media/storsimple-safety/IC740879.png) sarsıntı simgesi ![Electrical ](./media/storsimple-safety/IC740882.png) **uyarısı!**

Yaralanma, elektrik sarsıntı veya ölüm olasılığını azaltmak için:

* Kapakları, PCM 'den kaldırmayın. İçinde elektrik sarsıntısı bir tehlike vardır. PCM 'yi döndürmek ve bir değiştirme almak için [Microsoft desteği başvurun](storsimple-contact-microsoft-support.md).

![Notice simge ](./media/storsimple-safety/IC740881.png) **bildirimi:**

Ekipmanlarınızın doğru çalışması ve ürün hasarı engellemek için:

* Başarısız olan PCM 'yi 24 saat içinde değiştirmelisiniz. Değişiklik için bir PCM kaldırıldıktan sonra, kaldırma işleminin ardından 10 dakika içinde tamamlanması gerekir.
* Bir değiştirme hemen yüklenemediği takdirde PCM 'yi kaldırmayın. Kutunun tüm modüller yapılmadan işletilmemelidir.

## <a name="electrostatic-discharge-esd-precautions"></a>Elektrostatic Boşalma (ESD) önlemleri
![Notice simge ](./media/storsimple-safety/IC740881.png) **bildirimi:**

Aşağıdaki ESD ile ilgili önlemleri gözlemleyin.

* Uygun bir Antistatic bilek veya anormal strap 'yi yüklediğinizden ve denetdiğinizden emin olun.
* Modülleri ve bileşenleri işlerken tüm geleneksel ESD önlemlerini gözlemleyin.
* Arka düzlem bileşenleriyle ve modül bağlayıcılarıyla iletişim kullanmaktan kaçının.
* ESD hasar garantisi kapsamında değildir.

## <a name="battery-disposal-precautions"></a>Pil elden çıkarma önlemleri
Güç kaynağı, geçici, kısa süreli güç kesintileri sırasında belleğin içeriğini korumak için özel bir pil kullanır. Pil PCM 'de dağıtılır. Aşağıdaki bilgileri pil hakkında aklınızda tutun.

![Warning simge ](./media/storsimple-safety/IC740879.png) **uyarısı!**

Kısa parçalara, yangın, patlama, yaralanma veya ölüm riskini azaltmak için:

* Ulusal/bölgesel yönetmeliklere uygun olarak kullanılan pillerin atımı atılmaya.
* 60 derecenin üzerinde (140 derece Fahrenhayt) veya ıneylemsizlik 'ın üzerine derece ayırın, Crush veya ısı ayırın. PCM pili yalnızca sağlanan bir pil ile değiştirin. Başka bir pilin kullanılması, yangın veya patlama riskini sunabilir.
* Güç kaynağından kaldırılırsa, pillerde koruyucu bitiş üst sınırı kullanın.

![Notice simge ](./media/storsimple-safety/IC740881.png) **bildirimi:**

Pillere gönderim yaparken veya başka bir şekilde taşındığınızda, [https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx) adresinden erişilebilen IATA lityum pil Kılavuzu belgesini izleyin

Bu güvenlik bildirimlerini inceledikten sonra, bir sonraki adım cihazınızın paketini açmak, rafa eklemek ve kablosunu yapmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar
* 8100 bir cihaz için, [StorSimple 8100 cihazınızı yüklemeye](storsimple-8100-hardware-installation.md)gidin.
* 8600 bir cihaz için, [StorSimple 8600 cihazınızı yüklemeye](storsimple-8600-hardware-installation.md)gidin.

