---
title: Microsoft Azure StorSimple 8100 cihazını yükler | Microsoft Docs
description: Yazılımı dağıtmadan ve yapılandırmadan önce StorSimple 8100 cihazınızın paketini açma, raf bağlama ve kablosunu açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 0b2301c1c9b846e2ea005f049c5aadb3d4634a81
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963592"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>StorSimple 8100 cihazınızın paketini açma, raf bağlama ve kablo oluşturma

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Genel Bakış
Microsoft Azure StorSimple 8100, tek bir kutu, rafa takılan bir aygıttır. Bu öğreticide, StorSimple cihazını yapılandırıp dağıtmadan önce StorSimple 8100 cihaz donanımının paketini açma, raf bağlama ve kablonun nasıl yapılacağı açıklanmaktadır.

## <a name="unpack-your-storsimple-8100-device"></a>StorSimple 8100 cihazınızın paketini açın
Aşağıdaki adımlarda, StorSimple 8100 depolama cihazınızın paketini açma hakkında net, ayrıntılı yönergeler sağlanmaktadır. Cihaz tek bir kutuda gönderilir.

### <a name="prepare-to-unpack-your-device"></a>Cihazınızın paketini açmaya hazırlanma
Cihazınızı paketten yapılandırmadan önce aşağıdaki bilgileri gözden geçirin.

![Uyarı simgesi](./media/storsimple-safety/IC740879.png)![ağır ağırlık simgesi](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **uyarısı!**

1. El ile idare ediyorsanız, kutunun ağırlığını yönetmek için iki kişiye sahip olduğunuzdan emin olun. Tam olarak yapılandırılmış bir kutu 32 kg 'a (70 lbs) ağırlık verebilir.
2. Kutuyu düz ve sabit bir yüzeye yerleştirin.

Ardından, cihazınızın paketini açmak için aşağıdaki adımları izleyin.

#### <a name="to-unpack-your-device"></a>Cihazınızın paketini açmak için
1. Kutuda ve ambalajda ezik, kesik, su hasarı veya gözle görülür herhangi bir hasar olup olmadığını kontrol edin. Kutu veya ambalajda ciddi hasar varsa kutuyu açmayın. Cihazın iyi çalışma sırasında olup olmadığını değerlendirmenize yardımcı olması için lütfen [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md) .
2. Kutuyu açın. Aşağıdaki görüntüde, StorSimple cihazınızın açılmış görünümü gösterilmektedir.
   
     ![Depolama cihazınızın paketini açın](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Depolama cihazınızın paketi açılmış görünümü**
   
   | Etiket | Açıklama |
   | --- | --- |
   |   1\. |Paketleme kutusu |
   |   2 |Alt fohar |
   |   3 |Cihaz |
   |   4 |En iyi fohar |
   |   5 |Donatı kutusu |
3. Kutuyu açtıktan sonra aşağıdakilerin bulunduğundan emin olun:
   
   * 1 tek muhafaza cihazı
   * 2 güç kablosu
   * 1 çapraz bağlantı Ethernet kablosu
   * 2 seri konsol kabloları
   * seri erişim için 1 seri-USB dönüştürücüsü
   * 1 yetkisiz prova T10 screwdriver
   * 10 GbE ağ arabirimleriyle kullanılmak üzere 4 QSFP-SFP + bağdaştırıcı
   * 1 raf bağlama kiti (bağlantı donanımı ile 2 taraf rayları)
   * Başlarken belgeleri
     
     Yukarıda listelenen öğelerden herhangi birini almazsanız [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md).

Bir sonraki adım cihazınızı rafa yerleştirmektir.

## <a name="rack-mount-your-storsimple-8100-device"></a>StorSimple 8100 cihazınızı raf bağlama
StorSimple 8100 depolama cihazınızı ön ve arka gönderilere sahip standart 19 inç bir rafa yüklemek için sonraki adımları izleyin. StorSimple 8100 cihazının tek bir birincil kasası vardır.

Yükleme, her biri aşağıdaki yordamlarda ele alınan birden çok adımdan oluşur.

> [!IMPORTANT]
> StorSimple cihazlarının uygun bir işlem için rafa bağlı olması gerekir.
> 
> 

### <a name="prepare-the-site"></a>Siteyi hazırlama
Cihaz, hem ön hem de arka gönderilere sahip standart 19 inç bir rafa yüklenmiş olmalıdır. Raf yüklemesine hazırlanmak için aşağıdaki yordamı kullanın.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Siteyi raf yüklemesine hazırlamak için
1. Cihazın düz, sabit ve dengeli bir çalışma yüzeyi (veya benzeri) üzerinde güvenli bir şekilde durduğundan emin olun.
2. Ayarlamayı planladığınız sitenin, bağımsız bir kaynaktan veya kesintisiz güç kaynağı (UPS) olan bir raf güç dağıtım biriminden (PDU) standart AC gücüne sahip olduğunu doğrulayın.
3. Cihazı bağlamak istediğiniz rafta bir 2U yuvasının bulunduğundan emin olun.

![Uyarı simgesi](./media/storsimple-safety/IC740879.png)![ağır ağırlık simgesi](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **uyarısı!**

Cihaz kurulumunu el ile işliyorsa ağırlığı yönetmek için iki kişiye sahip olduğunuzdan emin olun. Tam olarak yapılandırılmış bir kutu 32 kg 'a (70 lbs) ağırlık verebilir.

### <a name="rack-prerequisites"></a>Raf önkoşulları
8100 Kasası, ile standart 19 inçlik bir raf dolabında yüklenmek üzere tasarlanmıştır:

* Raf gönderisini postala, en düşük 27,84 cm derinlik.
* Cihaz için maksimum 32 kg ağırlığı
* 5 Pascal için maksimum arka baskı (0,5 mm su ölçer).

### <a name="rack-mounting-rail-kit"></a>Dolap bağlama demiryolu seti
Bir bağlama rayları kümesi, 19 inç raf dolabı ile kullanılmak üzere sağlanır. Büyük/üst sınır, maksimum kasa ağırlığını işleyecek şekilde sınanmıştır. Bu rayları, raf içinde alan kaybı olmadan birden çok Kasası yüklemeye de izin verir.

#### <a name="to-install-the-device-on-the-rails"></a>Cihazı raya 'ya yüklemek için
1. Bu adımı yalnızca, iç rayları cihazınızda yüklü değilse gerçekleştirin. Genellikle, iç rayların fabrikada yüklü olması gerekir. Raylar yüklü değilse, sol kil ve sağ-Kııl slaytlarını, kasa kasasının kenarlarına yüklersiniz. Bunlar her bir tarafta altı ölçüm screws kullanarak iliştirirler. Yön ile ilgili yardım için, kıl slaytları **LH – Front** ve **RH – Front**olarak işaretlenir ve kutunun arka tarafına doğru bir şekilde yapıştırılmış olan uçta bir uçtan uca gösterilir.<br/>
   
    ![Kasa Kasası 'na demiryolu kasalarını ekleme](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **İç demiryolu slaytlarını kutunun kenarlarına ekleme**
   
    Etiket | Açıklama
    ----- | -----------
    1\.     | A 3x4 düğme-baş vida
    2     | Kasa slaytları

2. Dış sol demiryolu ve dış sağ demiryolu derlemelerini raf dolabı dikey üyelerine iliştirin. Köşeli ayraçlar **LH**, **RH**ve **bu yana** doğru yönde size yol gösterecek şekilde işaretlenir.
3. Ray tertibatının ön ve arkasındaki ray pimlerini bulun. Kııl 'yi raf nakilleri arasına sığacak şekilde genişletin ve PIN 'leri ön ve arka rafa, dikey üye deliklere ekleyin. Demiryolu derlemesinin düzeyi olduğundan emin olun.
4. Kııl derlemesini raf dikey üyelerine güvenli hale getirmek için, belirtilen ölçüm screıws ' nin ikisini kullanın. Önünde ve diğeri arkada bir vida kullanın.
5. Diğer demiryolu derlemesi için bu adımları tekrarlayın.<br/>
   
     ![Kııl slaytlarını raf dolabına ekleme](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Dış demiryolu derlemelerini rafa ekleme**
   
   | Etiket | Açıklama |
   | --- | --- |
   |   1\. |Clamping vidalı |
   |   2 |Kare deliği ön dolap gönderisi vidalı |
   |   3 |Sol demiryolu ön konumu PIN 'leri |
   |   4 |Clamping vidalı |
   |   5 |Sol demiryolu arka konumu PIN 'leri |

### <a name="mounting-the-device-in-the-rack"></a>Cihazı rafa bağlama
Yeni yüklenen raf raylarını kullanarak cihazı rafa bağlamak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-mount-the-device"></a>Cihazı bağlamak için
1. Bir yardımcı ile, Kasası kaldırın ve raf raylarıyla hizalayın.
2. Cihazı, Raya 'ya dikkatle ekleyin ve ardından cihazı raf dolabına tamamen gönderin.<br/>
   
    ![Cihaz rafa ekleniyor](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Cihazı rafa bağlama**
3. Caps Free 'ı çekerek sol ve sağ ön flanş harflerini kaldırın. Flanş 'ın üst sınırı, flanş 'lere kolayca yaslar.
4. Her bir flanş, sol ve sağ aracılığıyla bir adet Phillips-Head vida yükleyerek, rafa ait kutunun güvenliğini sağlayın.
5. Bir konuma basarak ve onları yerinde yaslayarak, flanş Cap 'leri yükler.<br/>
   
     ![Flanş Caps 'i yükleme](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Flanş Cap 'i yükleme**
   
   | Etiket | Açıklama |
   | --- | --- |
   |   1\. |Kutu birleşme vidalı |

Bir sonraki adım, cihazınızı güç, ağ ve seri erişim için kablolayın.

## <a name="cable-your-storsimple-8100-device"></a>StorSimple 8100 cihazınızı bağlayın
Aşağıdaki yordamlarda, StorSimple 8100 cihazınızın güç, ağ ve seri bağlantılar için nasıl kablo olarak kullanılacağı açıklanmaktadır.

### <a name="prerequisites"></a>Önkoşullar
Cihazınızın kablolarını bağlamaya başlamadan önce şunlara ihtiyacınız olacaktır:

* Depolama cihazınız, tamamen açılmış ve rafa takılı.
* cihazınızla gelen 2 güç kablosu
* 2 güç dağıtımı birimlerine erişim (önerilir).
* Ağ kabloları
* Belirtilen seri kablolar
* Bilgisayarınızda yüklü uygun sürücüyle seri USB dönüştürücüsü (gerekirse)
* 10 GbE ağ arabirimleriyle kullanılmak üzere 4 QSFP-SFP + bağdaştırıcı sağladı
* [StorSimple cihazınızda 10 GbE ağ arabirimleri için desteklenen donanım](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Güç kabloları
Cihazınız yedekli güç ve soğutma modülleri (PCMs) içerir. Yüksek kullanılabilirlik sağlamak için her iki PCMs 'nin yüklü ve farklı güç kaynaklarına bağlanması gerekir.

Cihazınızın güç bağlantısını yapmak için aşağıdaki adımları uygulayın.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Ağ kablolaması
Cihazınız etkin bekleme bir yapılandırmadır: herhangi bir zamanda, bir denetleyici modülü etkin ve diğer denetleyici modülü bekleme modundayken tüm disk ve ağ işlemlerini işliyor. Bir denetleyici başarısız olursa, bekleme denetleyicisi hemen etkinleştirilir ve tüm disk ve ağ işlemlerine devam eder.

Bu yedekli denetleyici yük devretmesini desteklemek için, aşağıdaki adımlarda açıklandığı gibi cihaz ağınızı kablosunu yapmanız gerekir.

#### <a name="to-cable-for-network-connection"></a>Ağ bağlantısı için kablo
1. Cihazınız her denetleyicide altı ağ arabirimine sahiptir: dört 1 Gbps ve 2 10 Gbps Ethernet bağlantı noktası. Cihazınızın arka düzleminde çeşitli veri bağlantı noktalarını belirler.
   
    ![8100 cihazının geri düzlemi](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Veri bağlantı noktalarını gösteren cihazın geri dönmesi**
   
   | Etiket | Açıklama |
   | --- | --- |
   |   0,1,4,5 |1 GbE ağ arabirimleri |
   |   2,3 |10 GbE ağ arabirimleri |
   |   6 |Seri bağlantı noktaları |
2. Ağ kablolaması için aşağıdaki diyagrama bakın. (En düşük ağ yapılandırması düz mavi çizgilerle gösterilir. Yüksek kullanılabilirlik ve performans için gereken ek yapılandırma noktalı çizgilere göre gösterilir.)

    ![Ağ için 2U cihazınızı bağlayın](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Cihazınız için ağ kablolaması**

   |Etiket | Açıklama |
   |----- | ----------- |
   | A    | Internet erişimi olan LAN |
   | B    | Denetleyici 0 |
   | C    | PCM 0 |
   | D    | Denetleyici 1 |
   | E    | PCM 1 |
   | F, G | Konaklar |
   | 0-5  | Ağ arabirimleri |



Cihaz kablolaması sırasında, en düşük yapılandırma şunları gerektirir:

* Her denetleyicide bulut erişimi ve diğeri de Iscsı için bir tane olmak üzere en az iki ağ arabirimi bağlı. DATA 0 bağlantı noktası otomatik olarak etkinleştirilir ve cihazın seri konsolu aracılığıyla yapılandırılır. VERI 0 ' dan farklı olarak, başka bir veri bağlantı noktasının de klasik Azure portalı üzerinden yapılandırılması gerekir. Bu durumda, DATA 0 bağlantı noktasını birincil LAN 'a (Internet erişimi olan ağ) bağlayın. Diğer veri bağlantı noktaları, hedeflenen role bağlı olarak ağın SAN/Iscsı LAN (VLAN) kesimine bağlanabilir.
* Denetleyici yük devretmesi gerçekleşirse kullanılabilirliği sağlamak için aynı ağa bağlı her denetleyicideki özdeş arabirimler. Örneğin, denetleyicilerden biri için VERI 0 ve VERI 3 ' ü bağlamayı seçerseniz, ilgili VERILERI 0 ve VERI 3 ' ü diğer denetleyiciye bağlamanız gerekir.

Yüksek kullanılabilirlik ve performans için göz önünde bulundurun:

* Mümkün olduğunda, her denetleyicide bulut erişimi (1 GbE) ve başka bir Iscsı (10 GbE önerilir) için bir çift ağ arabirimi yapılandırın.
* Mümkün olduğunda, bir anahtar hatasına karşı kullanılabilirlik sağlamak için her denetleyicideki ağ arabirimlerini iki farklı anahtara bağlayın. Şekil, iki farklı anahtara bağlı her denetleyiciden 2 10 GbE ağ arabirimlerini, DATA 2 ve DATA 3 ' ü gösterir.

Daha fazla bilgi için, [StorSimple cihazınız Için yüksek kullanılabilirlik gereksinimleri](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple)altındaki **ağ arabirimlerine** bakın.

> [!NOTE]
> 10 GbE ağ arabirimlerinizde SFP + alıcı vericiler kullanıyorsanız, belirtilen QSFP-SFP + bağdaştırıcıları ' nı kullanın. Daha fazla bilgi için, [StorSimple cihazınızda 10 GbE ağ arabirimleri Için desteklenen donanımlar](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)bölümüne gidin.
> 
> 

### <a name="serial-port-cabling"></a>Seri bağlantı noktası kablolama
Seri bağlantı noktanızın kablosunu almak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-cable-for-serial-connection"></a>Seri bağlantı için kablo
1. Cihazınızın her denetleyicide bir wranal simgesiyle tanımlanan bir seri bağlantı noktası vardır. Cihazınızın arka düzleminde seri bağlantı noktalarını bulmak için [ağ kablolama](#network-cabling) bölümündeki çizime bakın.
2. Cihaz arkadüzüzde etkin denetleyiciyi belirler. Yanıp sönen mavi, denetleyicinin etkin olduğunu gösterir.
3. Belirtilen seri kablolarını (gerekirse, dizüstü bilgisayarınızın USB seri dönüştürücüsünü) kullanın ve konsolunuzu veya bilgisayarınızı (cihaza yönelik terminal öykünmesi ile) etkin denetleyicinin seri bağlantı noktasına bağlayın.
4. Bilgisayarınıza seri USB sürücüleri (cihazla birlikte) yükler.
5. Seri bağlantıyı aşağıdaki şekilde ayarlayın: 115.200 baud, 8 veri biti, 1 durdurma biti, eşlik yok ve akış denetimi None olarak ayarlanmadı.
6. Konsola ENTER tuşuna basarak bağlantının çalıştığını doğrulayın. Seri konsol menüsü görünmelidir.

> [!NOTE]
> **Işık yönetimi**: Cihaz, uzak bir veri merkezinde veya sınırlı erişimli bir bilgisayar odasına yüklendiğinde, her iki denetleyiciyle seri bağlantıların her zaman bir seri konsol anahtarına veya benzer bir donanıma bağlı olduğundan emin olun. Bu, ağ kesintileri veya beklenmeyen başarısızlıklar varsa bant dışı uzaktan denetim ve destek işlemlerine izin verir.
> 
> 

Cihazınız artık güç, ağ erişimi ve seri bağlantı için kablolu olarak sağlanır. Bir sonraki adım, yazılımı yapılandırmak ve cihazınızı dağıtmaktır.

## <a name="next-steps"></a>Sonraki adımlar
Şirket [Içi StorSimple cihazınızı dağıtmayı ve yapılandırmayı](storsimple-8000-deployment-walkthrough-u2.md)öğrenin.

