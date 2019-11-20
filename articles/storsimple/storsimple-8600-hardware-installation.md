---
title: Microsoft Azure StorSimple 8600 cihazını yükler | Microsoft Docs
description: Yazılımı dağıtmadan ve yapılandırmadan önce StorSimple 8600 cihazınızın paketini açma, raf bağlama ve kablosunu açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 571c8b729408d7b9e805b875baccfd9ac8d6b9be
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965278"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>StorSimple 8600 cihazınızın paketini açma, raf bağlama ve kablo oluşturma

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Genel Bakış
Microsoft Azure StorSimple 8600, çift bir muhafaza aygıtıdır ve birincil ve EBOD Kasası oluşur. Bu öğreticide, StorSimple yazılımını yapılandırmadan önce StorSimple 8600 cihaz donanımının paketini açma, raf bağlama ve kablonun nasıl yapılacağı açıklanmaktadır.

## <a name="unpack-your-storsimple-8600-device"></a>StorSimple 8600 cihazınızın paketini açın
Aşağıdaki adımlarda, StorSimple 8600 depolama cihazınızın paketini açmak için net, ayrıntılı yönergeler sağlanmaktadır. Bu cihaz, biri birincil kutu, diğeri de EBOD Kasası için olmak üzere iki kutuya gönderilir. Bu iki kutu daha sonra tek bir kutuya yerleştirilir.

### <a name="prepare-to-unpack-your-device"></a>Cihazınızın paketini açmaya hazırlanma
Cihazınızı paketten yapılandırmadan önce aşağıdaki bilgileri gözden geçirin.

![Uyarı simgesi](./media/storsimple-safety/IC740879.png)![ağır ağırlık simgesi](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **uyarısı!**

1. Cihazı el ile işliyorsa, cihazın ağırlığını yönetmek için kullanılabilecek iki kişiye sahip olduğunuzdan emin olun. Tam olarak yapılandırılmış bir kutu 32 kg 'a (70 lbs) ağırlık verebilir.
2. Kutuyu düz ve sabit bir yüzeye yerleştirin.

Ardından, cihazınızın paketini açmak için aşağıdaki adımları izleyin.

#### <a name="to-unpack-your-device"></a>Cihazınızın paketini açmak için
1. Kutuda ve ambalajda ezik, kesik, su hasarı veya gözle görülür herhangi bir hasar olup olmadığını kontrol edin. Kutu veya ambalajda ciddi hasar varsa kutuyu açmayın. Cihazın iyi çalışma sırasında olup olmadığını değerlendirmenize yardımcı olması için lütfen [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md) .
2. Dış kutusunu açın ve ardından birincil ve EBOD kasaları ile ilgili iki kutuyu basılı götürün. Artık birincil ve EBOD kasaları paketini açabilirsiniz. Aşağıdaki şekilde, bir muhafazadan birinin açılmış görünümü gösterilmektedir.
   
    ![Depolama cihazınızın paketini açın](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Depolama cihazınızın paketi açılmış görünümü**
   
   | Etiket | Açıklama |
   | --- | --- |
   |   1\. |Paketleme kutusu |
   |   2 |SAS kabloları (aksesuarlar ve kablolar tepsisinde) |
   |   3 |Alt fohar |
   |   4 |Cihaz |
   |   5 |En iyi fohar |
   |   6 |Donatı kutusu |
3. İki kutuyu paketten kaldırdıktan sonra şunları yaptığınızdan emin olun:
   
   * 1 birincil kutu (birincil kasa ve EBOD Kasası iki ayrı kutuda bulunur)
   * 1 EBOD Kasası
   * 4 güç kablosu, her kutuda 2
   * 2 SAS kablosu (birincil Kasası EBOD Kasası 'na bağlamak için)
   * 1 çapraz bağlantı Ethernet kablosu
   * 2 seri konsol kabloları
   * seri erişim için 1 seri-USB dönüştürücüsü
   * 10 GbE ağ arabirimleriyle kullanılmak üzere 4 QSFP-SFP + bağdaştırıcı
   * 2 raf bağlama setleri (donanım bağlama ile 4 taraf Ray, birincil kasa ve EBOD Kasası için 2), her kutuda 1
   * Başlarken belgeleri
     
     Yukarıda listelenen öğelerden herhangi birini almazsanız [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md).  

Bir sonraki adım cihazınızı rafa yerleştirmektir.

## <a name="rack-mount-your-storsimple-8600-device"></a>StorSimple 8600 cihazınızı raf bağlama
StorSimple 8600 depolama cihazınızı ön ve arka gönderilere sahip standart 19 inç bir rafa yüklemek için sonraki adımları izleyin. Bu cihaz iki muhafazada gelir: birincil kutu ve EBOD Kasası. Bunların her ikisinin de rafa bağlı olması gerekir.

Yükleme, her biri aşağıdaki yordamlarda ele alınan birden çok adımdan oluşur.

> [!IMPORTANT]
> StorSimple cihazlarının uygun bir işlem için rafa bağlı olması gerekir.
> 
> 

### <a name="site-preparation"></a>Site hazırlığı
Kasaları hem ön hem de arka gönderilere sahip standart 19 inç bir rafa yüklenmiş olmalıdır. Raf yüklemesine hazırlanmak için aşağıdaki yordamı kullanın.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Siteyi raf yüklemesine hazırlamak için
1. Birincil ve EBOD kasaları düz, kararlı ve düzeyi bir iş yüzeyi (veya benzeri) üzerinde güvenli bir şekilde dinlendiğinden emin olun.
2. Ayarlamayı planladığınız sitenin, bağımsız bir kaynaktan veya kesintisiz güç kaynağı (UPS) olan bir raf güç dağıtım biriminden (PDU) standart AC gücüne sahip olduğunu doğrulayın.
3. Kasaları bağlamak istediğiniz rafta bir 4U (2 X 2U) yuvasının bulunduğundan emin olun.

![Uyarı simgesi](./media/storsimple-safety/IC740879.png)![ağır ağırlık simgesi](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **uyarısı!**

 Cihaz kurulumunu el ile işliyorsa ağırlığı yönetmek için iki kişiye sahip olduğunuzdan emin olun. Tam olarak yapılandırılmış bir kutu 32 kg 'a (70 lbs) ağırlık verebilir.

### <a name="rack-prerequisites"></a>Raf önkoşulları
Kasaları, ile standart 19 inçlik bir raf dolabında yüklenmek üzere tasarlanmıştır:

* Raf gönderisini postala 27,84 cm arasındaki en düşük derinlik
* Cihaz için maksimum 32 kg ağırlığı
* 5 Pascal maksimum geri baskısı (0,5 mm su ölçer)

### <a name="rack-mounting-rail-kit"></a>Dolap bağlama demiryolu seti
Bir montaj rayına kümesi, 19 inç raf dolabı ile kullanım için sağlanacaktır. Büyük/üst sınır, maksimum kasa ağırlığını işleyecek şekilde sınanmıştır. Bu rayları, raf içinde alan kaybı olmadan birden çok Kasası yüklemeye de izin verir. Önce EBOD Kasası 'nı yükler.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>EBOD Kasası 'nı raya 'ya yüklemek için
1. Bu adımı yalnızca, iç rayları cihazınızda yüklü değilse gerçekleştirin. Genellikle, iç rayların fabrikada yüklü olması gerekir. Raylar yüklü değilse, sol kil ve sağ-Kııl slaytlarını, kasa kasasının kenarlarına yüklersiniz. Bunlar her bir tarafta altı ölçüm screws kullanarak iliştirirler. Yön ile ilgili yardım için, kıl slaytları **LH – Front** ve **RH – Front**olarak işaretlenir ve kutunun arka tarafına doğru bir şekilde yapıştırılmış olan uçta bir uçtan uca gösterilir.
   
    ![Kasa Kasası 'na demiryolu kasalarını ekleme](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Kıl slaytlarını kutunun kenarlarına ekleme**
   
   | Etiket | Açıklama |
   | --- | --- |
   |  1\. |A 3x4 düğme-baş vida |
   |  2 |Kasa slaytları |
2. Sol demiryolu ve sağ demiryolu derlemelerini raf dolabı dikey üyelerine iliştirin. Köşeli ayraçlar **LH**, **RH**ve **bu yana** doğru yönde size yol gösterecek şekilde işaretlenir.
3. Ray tertibatının ön ve arkasındaki ray pimlerini bulun. Kııl 'yi raf nakilleri arasına sığacak şekilde genişletin ve iğneleri ön ve arka dolap dikey üye delikleri içine ekleyin. Demiryolu derlemesinin düzeyi olduğundan emin olun.
4. Belirtilen ölçüm screcılarından ikisini kullanarak, Kııl derlemesini raf dikey üyelerine güvenli hale getirin. Önünde ve diğeri arkada bir vida kullanın.
5. Diğer demiryolu derlemesi için bu adımları tekrarlayın.
   
     ![Kııl slaytlarını raf dolabına ekleme](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **VAIL derlemelerini rafa ekleme**
   
   | Etiket | Açıklama |
   | --- | --- |
   |   1\. |Clamping vidalı |
   |   2 |Kare deliği ön dolap gönderisi vidalı |
   |   3 |Sol ön demiryolu konumu PIN 'leri |
   |   4 |Clamping vidalı |
   |   5 |Sol arka demiryolu konumu PIN 'leri |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Raftaki EBOD Muhafazası bağlama
Yeni yüklenen raf raylarını kullanarak, raftaki EBOD Muhafazası bağlamak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-mount-the-ebod-enclosure"></a>EBOD Kasası bağlamak için
1. Bir yardımcı ile, Kasası kaldırın ve raf raylarıyla hizalayın.
2. Kutunun rayına dikkatle ekleyin ve ardından tamamen raf dolabına gönderin.
   
    ![Cihaz rafa ekleniyor](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Kasası rafa bağlama**
3. Caps Free 'ı çekerek sol ve sağ ön flanş harflerini kaldırın. Flanş 'ın üst sınırı, flanş 'lere kolayca yaslar.
4. Her bir flanş, sol ve sağ aracılığıyla bir adet Phillips-Head vida yükleyerek, bir kutunun rafla güvenliğini sağlayın.
5. Flanş Caps ' i konuma basarak ve yerlerine yaslayarak ekleyin.
   
     ![Flanş Caps 'i yükleme](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Flanş Cap 'i yükleme**
   
   | Etiket | Açıklama |
   | --- | --- |
   |   1\. |Kutu birleşme vidalı |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Birincil Kasası rafa bağlama
EBOD Kasası bağlamayı tamamladıktan sonra, birincil Kasası aynı adımları izleyerek bağlamanız gerekecektir.

> [!NOTE]
> * Rafa, birincil kutu ve EBOD Kasası arasında birkaç boş yuva olması mümkündür.
> * Birincil Kasası EBOD Kasası 'na bağlamak için, belirtilen 2m SAS kablosunu kullanın.
> * Baş birimin göreli yerleşimi için EBOD birimine kısıtlama yoktur. Bu nedenle, birincil kutu üst yuvaya ve aşağıdaki EBOD kutusuna yerleştirilebilir veya bunun tersini yapabilir.
> 
> 

Bir sonraki adım, cihazınızı güç, ağ ve seri erişim için kablolayın.

## <a name="cable-your-storsimple-8600-device"></a>StorSimple 8600 cihazınızı bağlayın
Aşağıdaki yordamlarda, StorSimple 8600 cihazınızın güç, ağ ve seri bağlantılar için nasıl kablo olarak kullanılacağı açıklanmaktadır.

### <a name="prerequisites"></a>Önkoşullar
Cihazınızı kablolu çalışmaya başlamadan önce şunları yapmanız gerekir:

* Birincil kasası ve EBOD Kasası, tamamen paketi açılmış
* cihazınızla birlikte gelen 4 güç kablosu (birincil ve EBOD Kasası için 2)
* EBOD Kasası 'nı birincil muhafaza bağlamak için cihazla birlikte sunulan 2 SAS kablosu
* 2 güç dağıtımı birimlerine (PDU) erişim (önerilir)
* Ağ kabloları
* Belirtilen seri kablolar
* Bilgisayarınızda yüklü uygun sürücüyle seri USB dönüştürücüsü (gerekirse)
* 10 GbE ağ arabirimleriyle kullanılmak üzere 4 QSFP-SFP + bağdaştırıcı sağladı
* [StorSimple cihazınızda 10 GbE ağ arabirimleri için desteklenen donanım](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS ve güç kabloları
Cihazınızın hem birincil Kasası hem de bir EBOD kasası vardır. Bu, birimlerin seri bağlantılı SCSI (SAS) bağlantısı ve gücü için bir araya gruplanmalıdır.

Bu cihazı ilk kez ayarlarken, önce SAS kablolaması için adımları gerçekleştirin ve ardından güç kabloları adımlarını doldurun.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Ağ kablolaması
Cihazınız etkin bekleme özellikli bir yapılandırmada: herhangi bir zamanda, bir denetleyici modülü etkin ve diğer denetleyici modülü bekleme modundayken tüm disk ve ağ işlemlerini işliyor. Bir denetleyici hatası oluşursa, bekleme denetleyicisi hemen etkinleştirilir ve tüm disk ve ağ işlemlerine devam eder.

Bu yedekli denetleyici yük devretmesini desteklemek için, aşağıdaki adımlarda gösterildiği gibi cihaz ağınızı kablosunu yapmanız gerekir.

#### <a name="to-cable-for-network-connection"></a>Ağ bağlantısı için kablo
1. Cihazınız her denetleyicide altı ağ arabirimine sahiptir: dört adet 1 Gbps ve 2 10 Gbps Ethernet bağlantı noktası. Cihazınızın arka düzleminde veri bağlantı noktalarını belirlemek için aşağıdaki çizime bakın.
   
     ![8600 cihazının geri düzlemi](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Veri bağlantı noktalarını gösteren cihazınızın geri dönmesi**
   
   | Etiket | Açıklama |
   | --- | --- |
   |   0,1,4,5 |1 GbE ağ arabirimleri |
   |   2,3 |10 GbE ağ arabirimleri |
   |   6 |Seri bağlantı noktaları |
2. Ağ kablolaması için aşağıdaki diyagrama bakın. (En düşük ağ yapılandırması düz mavi çizgilerle gösterilir. Yüksek kullanılabilirlik ve performans için gereken ek yapılandırma noktalı çizgilere göre gösterilir.)

![4U cihazınızı ağ için kablo ile](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Cihazınız için ağ kablolaması**

| Etiket | Açıklama |
| --- | --- |
| A |Internet erişimi olan LAN |
| B |Denetleyici 0 |
| C |PCM 0 |
| D |Denetleyici 1 |
| E |PCM 1 |
| C |EBOD denetleyicisi 0 |
| G |EBOD denetleyicisi 1 |
| H, I |Konaklar (örneğin, dosya sunucuları) |
| 0-5 |Ağ arabirimleri |
| 6 |Birincil kutu |
| 7 |EBOD Kasası |

Cihaz kablolaması sırasında, en düşük yapılandırma şunları gerektirir:

* Her denetleyicide bulut erişimi ve diğeri de Iscsı için bir tane olmak üzere en az iki ağ arabirimi bağlı. DATA 0 bağlantı noktası otomatik olarak etkinleştirilir ve cihazın seri konsolu aracılığıyla yapılandırılır. VERI 0 ' dan farklı olarak, başka bir veri bağlantı noktasının de klasik Azure portalı üzerinden yapılandırılması gerekir. Bu durumda, DATA 0 bağlantı noktasını birincil LAN 'a (Internet erişimi olan ağ) bağlayın. Diğer veri bağlantı noktaları, hedeflenen role bağlı olarak ağın SAN/Iscsı LAN (VLAN) kesimine bağlanabilir.
* Denetleyici yük devretmesi gerçekleşirse kullanılabilirliği sağlamak için aynı ağa bağlı her denetleyicideki özdeş arabirimler. Örneğin, denetleyicilerden biri için VERI 0 ve VERI 3 ' ü bağlamayı seçerseniz, ilgili VERILERI 0 ve VERI 3 ' ü diğer denetleyiciye bağlamanız gerekir.

Yüksek kullanılabilirlik ve performans için göz önünde bulundurun:

* Mümkün olduğunda, her denetleyicide bulut erişimi (1 GbE) ve başka bir Iscsı (10 GbE önerilir) için bir çift ağ arabirimi yapılandırın.
* Mümkün olduğunda, bir anahtar hatasına karşı kullanılabilirlik sağlamak için her denetleyicideki ağ arabirimlerini iki farklı anahtara bağlayın. Şekil, iki farklı anahtara bağlı her denetleyiciden 2 10 GbE ağ arabirimlerini, DATA 2 ve DATA 3 ' ü gösterir. Daha fazla bilgi için, [StorSimple cihazınız Için yüksek kullanılabilirlik gereksinimleri](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple)altındaki **ağ arabirimlerine** bakın.

> [!NOTE]
> 10 GbE ağ arabirimlerinizde SFP + alıcı vericiler kullanıyorsanız, belirtilen QSFP-SFP + bağdaştırıcıları kullanın. Daha fazla bilgi için, [StorSimple cihazınızda 10 GbE ağ arabirimleri Için desteklenen donanımlar](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)bölümüne gidin.
> 
> 

### <a name="serial-port-cabling"></a>Seri bağlantı noktası kablolama
Seri bağlantı noktanızın kablosunu almak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-cable-for-serial-connection"></a>Seri bağlantı için kablo
1. Cihazınızın her denetleyicide bir wranal simgesiyle tanımlanan bir seri bağlantı noktası vardır. Seri bağlantı noktalarını bulmak için, cihazınızın arkasında bulunan veri bağlantı noktalarını gösteren çizime bakın.
2. Cihaz arkadüzüzde etkin denetleyiciyi belirler. Yanıp sönen mavi, denetleyicinin etkin olduğunu gösterir.
3. Belirtilen seri kablosunu (gerekirse, dizüstü bilgisayarınızın USB seri dönüştürücüsünü) kullanın ve konsolunuzu veya bilgisayarınızı (cihaza yönelik terminal öykünmesi ile) etkin denetleyicinin seri bağlantı noktasına bağlayın.
4. Bilgisayarınıza seri USB sürücüleri (cihazla birlikte) yükler.
5. Seri bağlantıyı aşağıdaki şekilde ayarlayın:
   
   * 115.200 baud
   * 8 veri bitleri
   * 1 durdurma biti
   * Eşlik yok
   * Akış denetimi **none** olarak ayarlandı
6. Konsola ENTER tuşuna basarak bağlantının çalıştığını doğrulayın. Seri konsol menüsü görünmelidir.

> [!NOTE]
> **Işık yönetimi:** Cihaz, uzak bir veri merkezinde veya sınırlı erişimli bir bilgisayar odasına yüklendiğinde, her iki denetleyiciyle seri bağlantıların her zaman bir seri konsol anahtarına veya benzer bir donanıma bağlı olduğundan emin olun. Bu, ağ kesintisi veya beklenmeyen hatalara karşı bant dışı uzaktan denetim ve destek işlemlerine izin verir.
> 
> 

Cihazınızın güç, ağ erişimi ve seri bağlantı için kablo bağlantısını tamamladınız. Bir sonraki adım, cihazınızdaki yazılımı yapılandırmak için kullanılır.

## <a name="next-steps"></a>Sonraki adımlar
Artık şirket [Içi StorSimple cihazınızı dağıtmaya ve yapılandırmaya](storsimple-8000-deployment-walkthrough-u2.md)hazırsınız.

