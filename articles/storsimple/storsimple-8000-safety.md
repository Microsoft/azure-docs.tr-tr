---
title: '& StorSimple 8000 serisi cihazı güvenle çalıştırın'
description: Güvenlik kurallarını, yönergeleri ve konuları açıklar ve StorSimple cihazınızı güvenle yüklemeyi ve çalıştırmayı açıklar.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 3adbd292d42fd6fd2a0e149e103c17968a6f084b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94950763"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>StorSimple cihazınızı güvenle yükleyip çalıştırın
![Uyarı simgesi ](./media/storsimple-safety/IC740879.png)
 ![ okuma güvenlik uyarısı SIMGESI ](./media/storsimple-safety/IC740885.png) **güvenlik ve durum bilgilerini oku**

Bu makaledeki Microsoft Azure StorSimple cihazınız için geçerli olan tüm güvenlik ve sistem durumu bilgilerini okuyun. Daha sonra başvurmak üzere StorSimple aygıtınızla birlikte gelen tüm yazdırılmış kılavuzlarınızı saklayın. Bu ürün için yönergeleri izlemeden ve düzgün şekilde ayarlanamaması ve bu ürüne yönelik doğru kurulum, kullanım ve bakım riski, ciddi bir sorun veya ölüm riskini artırabilir ya da cihaz ya da cihazlara zarar verebilir. [Bu kılavuzun indirilebilir bir sürümü](https://www.microsoft.com/download/details.aspx?id=44233) de mevcuttur.

## <a name="safety-icon-conventions"></a>Güvenlik simgesi kuralları
Microsoft Azure StorSimple cihazınızı kurarken ve çalıştırırken gözlenecek güvenlik önlemlerinizi gözden geçirdikten sonra bulacağınız simgeler aşağıda verilmiştir.

| Simge | Description |
|:--- |:--- |
| ![Danger simgesi ](./media/storsimple-safety/IC740879.png) **tehlike!** |Önedilmediği durumlarda, ölüm veya ciddi bir yaralama oluşmasına neden olan tehlikeli bir durum belirtir. Bu sinyal sözcüğü, en Extreme durumlarla sınırlı olacaktır. |
| ![Uyarı simgesi ](./media/storsimple-safety/IC740879.png) **uyarısı!** |Önedilmediği durumlarda, ölüm veya ciddi bir yaralama oluşmasına neden olabilecek tehlikeli bir durum belirtir. |
| ![Uyarı simgesi ](./media/storsimple-safety/IC740879.png) **dikkatli!** |Önedilmediği durumlarda küçük veya orta bir yaralanmaya neden olabilecek tehlikeli bir durum belirtir. |
| ![Bildirim simgesi ](./media/storsimple-safety/IC740881.png) **bildirimi:** |Önemli kabul edilen, ancak tehlike ile ilgili olmayan bilgileri gösterir. |
| ![Elektrik sarsıntısı simgesi ](./media/storsimple-safety/IC740882.png) **elektrik şok hasar** |Yüksek voltaj |
| ![Ağır kalınlık simgesi ](./media/storsimple-safety/IC740883.png) **ağır ağırlık** | |
| ![Kullanıcı tarafından ](./media/storsimple-safety/IC740879.png)  hizmet içermeyen parçalar simgesi yok |Uygun şekilde Eğitilmediğiniz müddetçe bu erişimi kullanmayın. |
| ![Güvenlik bildirimi okuma simgesi](./media/storsimple-safety/IC740885.png)**ilk olarak tüm yönergeleri oku** | |
| ![İpucu hasar simgesi ](./media/storsimple-safety/IC740886.png) **Ipucu rastlantı** | |

## <a name="handling-precautions"></a>Önlemleri işleme
![Uyarı simgesi ](./media/storsimple-safety/IC740879.png) ![ ağır ağırlık simgesi ](./media/storsimple-safety/IC740883.png) **uyarısı!** 

Yaralanma riskini azaltmak için:

* Tam olarak yapılandırılmış bir kutu 32 kg 'a (70 lbs) ağırlık verebilir; kendiniz de kaldırma denemeyin.
* Kasası taşımadan önce her zaman ağırlığı işlemek için iki kişinin kullanılabilir olduğundan emin olun. Bu ağırlığı kaldırmaya çalışan bir kişinin, sakatlama 'e dokunması gerektiğini unutmayın.
* Kapsamı, birimin arkada bulunan güç ve soğutma modüllerindeki (PCMs) tanıtıcılarla kaldırmayın. Bunlar ağırlığı alacak şekilde tasarlanmamıştır.

## <a name="connection-precautions"></a>Bağlantı önlemleri
![Uyarı simgesi ](./media/storsimple-safety/IC740879.png) ![ elektrik sarsıntı simgesi ](./media/storsimple-safety/IC740882.png) **uyarısı!**

Yaralanma, elektrik sarsıntı veya ölüm olasılığını azaltmak için:

* Birden çok AC kaynağı tarafından güçlendirilmiştir, tüm yalıtımın tüm yalıtımının bağlantısını kesin.
* Birimi Taşımadan önce kalıcı olarak çıkarın veya herhangi bir şekilde hasar gördüğünü düşünün.
* Güç kaynağı kapakları ile güvenli bir elektrik dünya bağlantısı sağlayın. Kutunun grokinin, güç uygulamadan önce ulusal ve yerel gereksinimleri karşıladığından emin olun.
* Bir PCM 'den bir PCM kaldırılmadan önce güç bağlantısının her zaman bağlantısının kesildiğinden emin olun.
* Güç kaynağı kablosu ana bağlantı kesme aygıtı olduğu için, dış cihazların donanımların yakınında bulunduğundan ve kolayca erişilebilir olduğundan emin olun.

![Uyarı simgesi ](./media/storsimple-safety/IC740879.png) ![ elektrik sarsıntı simgesi ](./media/storsimple-safety/IC740882.png) **uyarısı!**

Elektrik bağlantılarından aşırı Isıtma veya yangın olasılığını azaltmak için:

* Teknik belirtimde açıklanan gereksinimleri karşılamak için elektrik aşırı yüklemesi koruması ile uygun bir güç kaynağı sağlayın.
* Bifurta Power kablosu sayısı ("Y" müşteri adayları) kullanmayın.
* Geçerli güvenlik, egörev ve sıcaklık gereksinimleriyle uyum sağlamak için hiçbir kapsamaları kaldırılmalıdır ve tüm yuvaların eklenti modülleri veya sürücü boşlukları ile doldurulması gerekir.
* Ekipmanın üretici tarafından belirtilen bir şekilde kullanıldığından emin olun. Bu ekipman üretici tarafından belirtilmemiş bir şekilde kullanılıyorsa, ekipman tarafından sunulan koruma düşebilir.

![Bildirim simgesi ](./media/storsimple-safety/IC740881.png) **bildirimi:**

Ekipmanlarınızın doğru çalışması ve ürün hasarı engellemek için:

* Cihazın arkasında bulunan RJ45 bağlantı noktaları yalnızca Ethernet bağlantısı içindir. Bunların bir telekomünikasyon ağına bağlı olmaması gerekir.
* Cihazı, önden arkaya soğutma tasarımını barındırabilecek bir rafa yüklediğinizden emin olun.
* Tüm eklenti modülleri ve boş levhalar, sistem kasası 'nın bir parçasıdır. Bunlar yalnızca bir değiştirme hemen eklenebileceği zaman kaldırılmalıdır. Sistem tüm modüller veya boşluklar olmadan çalıştırılmamalıdır.

## <a name="rack-system-precautions"></a>Raf sistemi önlemleri
Cihazı bir raf dolabına bağladığınızda aşağıdaki güvenlik gereksinimlerinin göz önünde bulundurulmalıdır.

![Uyarı simgesi ](./media/storsimple-safety/IC740879.png) ![ Ipucu hasar simgesi ](./media/storsimple-safety/IC740886.png) **uyarısı!**

Bir ucundan çok fazla yaralanma olasılığını azaltmak için:

* Raf tasarımı, yüklenen eklerin toplam ağırlığını desteklemelidir ve raf veya normal kullanım sırasında rafa veya bir şekilde kullanıma sunulmasını engellemek için uygun özellikleri sabitlemeye dahil edilmelidir.
* Bir raf yüklerken, bölmeyi yukarıdan aşağıya doğru ve boş olarak doldur.
* Rafa ortadan kaldırmak için bir seferde birden fazla kutusu her seferinde birden çok kez girmeyin.

![Uyarı simgesi ](./media/storsimple-safety/IC740879.png) ![ elektrik sarsıntı simgesi ](./media/storsimple-safety/IC740882.png) **uyarısı!**

Yaralanma, elektrik sarsıntı veya ölüm olasılığını azaltmak için:

* Raf, güvenli bir elektrik dağıtım sistemine sahip olmalıdır. Bu, kutu için güncel koruma sağlamalıdır ve yüklenen toplam muhafaza sayısına göre aşırı yüklenmiş olmalıdır. Namelevha üzerinde gösterilen elektrik güç tüketimi derecelendirmesi gözlenmelidir.
* Elektrik dağıtım sistemi, raftaki her bir kutu için güvenilir bir zemin sağlamalıdır.
* Elektrik dağıtım sisteminin tasarımı, Tüm muhafazaların tüm güç kaynakları için geçerli olan toplam zemin sızıntısı olduğunu dikkate almalıdır. Her bir kasadaki her bir güç kaynağının, 60 Hz, 264 volt tarihinde en fazla 1,0 mA kadar geçerli olduğunu unutmayın. Raf, "yüksek LEAKAGE CURRENT" ile etiketleme gerektirebilir. Bir tedariği bağlamadan önce zemin (Dünya) bağlantısı gereklidir. "
* Kasaları, muhafazalarla yapılandırıldığında, UL 60950-1 ve ıEC 60950-1/EN 60950-1 güvenlik gereksinimlerini karşılamalıdır.

![Bildirim simgesi ](./media/storsimple-safety/IC740881.png) **bildirimi:**

Raf sisteminizin uygun soğutma için:

* Raf tasarımının 35 derece santigrat (95 derece Fahrenhayt) en yüksek muhafaza işletim çevresel sıcaklığını dikkate aldığından emin olun. Raf sisteminin soğuk olduğu yere sahip olun ve veri merkezindeki AC askısından yeterli Airflow olup olmadığını kontrol edin.
* Sistem, düşük basınç, arka arkaya yükleme (raf kapıları tarafından oluşturulan arka basınç ve 5 Pascal [0,5 mm su ölçer] ' ı aşmayacak şekilde) ile çalıştırılır.

## <a name="power-cooling-module-pcm-precautions"></a>Güç soğutma modülü (PCM) önlemleri

Cihaz iki PCMs ile çalışacak şekilde tasarlanmıştır. Her PCMs 'nin bir güç kaynağı ve çift eksenli bir fanı vardır. Kritik bir durum sırasında, Sistem normal işlemlere devam ederken bir güç kaynağı hatasına izin verir. İki PCMs (ve bu nedenle güç kaynakları) her zaman yüklenmelidir. Tek bir PCM, yedekli güç sağlamaz. Bu nedenle, bir PCM 'nin başarısız olması kapalı kalma süresine veya olası veri kaybına neden olabilir.

![Uyarı simgesi ](./media/storsimple-safety/IC740879.png) ![ elektrik sarsıntı simgesi ](./media/storsimple-safety/IC740882.png) **uyarısı!**

Yaralanma, elektrik sarsıntı veya ölüm olasılığını azaltmak için:

* Kapakları, PCM 'den kaldırmayın. İçinde elektrik sarsıntısı bir tehlike vardır. PCM 'yi döndürmek ve bir değiştirme almak için [Microsoft desteği başvurun](./storsimple-8000-contact-microsoft-support.md).

![Bildirim simgesi ](./media/storsimple-safety/IC740881.png) **bildirimi:**

Ekipmanlarınızın doğru çalışması ve ürün hasarı engellemek için:

* Başarısız olan PCM 'yi 24 saat içinde değiştirmelisiniz. Değişiklik için bir PCM kaldırıldıktan sonra, kaldırma işleminin ardından 10 dakika içinde tamamlanması gerekir.
* Bir değiştirme hemen yüklenemediği takdirde PCM 'yi kaldırmayın. Kutunun tüm modüller yapılmadan işletilmemelidir.

## <a name="electrostatic-discharge-esd-precautions"></a>Elektrostatic Boşalma (ESD) önlemleri

![Bildirim simgesi ](./media/storsimple-safety/IC740881.png) **bildirimi:**

Aşağıdaki ESD ile ilgili önlemleri gözlemleyin.

* Uygun bir Antistatic bilek veya anormal strap 'yi yüklediğinizden ve denetdiğinizden emin olun.
* Modülleri ve bileşenleri işlerken tüm geleneksel ESD önlemlerini gözlemleyin.
* Arka düzlem bileşenleriyle ve modül bağlayıcılarıyla iletişim kullanmaktan kaçının.
* ESD hasar garantisi kapsamında değildir.

## <a name="battery-disposal-precautions"></a>Pil elden çıkarma önlemleri

Güç kaynağı, geçici, kısa süreli güç kesintileri sırasında belleğin içeriğini korumak için özel bir pil kullanır. Pil PCM 'de dağıtılır. Aşağıdaki bilgileri pil hakkında aklınızda tutun.

![Uyarı simgesi ](./media/storsimple-safety/IC740879.png) **uyarısı!**

Kısa parçalara, yangın, patlama, yaralanma veya ölüm riskini azaltmak için:

* Ulusal/bölgesel yönetmeliklere uygun olarak kullanılan pillerin atımı atılmaya.
* 60 derecenin üzerinde (140 derece Fahrenhayt) veya ıneylemsizlik 'ın üzerine derece ayırın, Crush veya ısı ayırın. PCM pili yalnızca sağlanan bir pil ile değiştirin. Başka bir pilin kullanılması, yangın veya patlama riskini sunabilir.
* Güç kaynağından kaldırılırsa, pillerde koruyucu bitiş üst sınırı kullanın.

![Bildirim simgesi ](./media/storsimple-safety/IC740881.png) **bildirimi:**

Pillere gönderim yaparken veya başka bir şekilde taşındığınızda, şu adreste bulunan ıATA lityum pil Kılavuzu belgesini izleyin: [https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Bu güvenlik bildirimlerini inceledikten sonra, bir sonraki adım cihazınızın paketini açmak, rafa eklemek ve kablosunu yapmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* 8100 bir cihaz için, [StorSimple 8100 cihazınızı yüklemeye](storsimple-8100-hardware-installation.md)gidin.
* 8600 bir cihaz için, [StorSimple 8600 cihazınızı yüklemeye](storsimple-8600-hardware-installation.md)gidin.