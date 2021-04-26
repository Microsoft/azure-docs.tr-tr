---
title: Birden çok Azure Kinect DK cihazını eşitler
description: Bu makalede, çok cihazlı eşitlemenin avantajları ve eşitlenecek cihazların nasıl ayarlanacağı açıklanır.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: Azure, Kinect, özellikler, donanım, DK, yetenekler, derinlik, renk, RGB, IMU, dizi, derinlik, çoklu, eşitleme
ms.openlocfilehash: eabf77896777f39efcfd61adb3040bca8642716e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102039964"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>Birden çok Azure Kinect DK cihazını eşitler

Her Azure Kinect DK cihazı, birden çok cihazı birbirine bağlamak için kullanabileceğiniz 3,5-mm eşitleme bağlantı noktalarını (**eşitleme** ve **eşitleme**) içerir. Cihazları bağlandıktan sonra, yazılımınız aralarında tetikleyici zamanlamasını koordine edebilir. 

Bu makale, cihazları bağlama ve eşitlemeye nasıl olanak sağlar.

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>Birden çok Azure Kinect DK cihazı kullanmanın avantajları

Aşağıdakiler de dahil olmak üzere birden çok Azure Kinect DK aygıtı kullanmanın birçok nedeni vardır:

- Okları doldur. Azure Kinect DK veri dönüştürmeleri tek bir görüntü oluştursa da, iki kamera (derinlik ve RGB) aslında küçük bir uzaklığa sahiptir. Bu fark, olası sonuçlar sağlar. Bir ön plan nesnesi, bir cihazdaki iki kameradan biri için arka plan nesnesinin bir kısmının görünümünü engellediğinde bir occluson oluşur. Ortaya çıkan renkli görüntüde, ön plan nesnesi bir gölgeyi bir gölge olarak saçmış gibi görünüyor.  
   Örneğin, aşağıdaki diyagramda, sol taraftaki kamera "P2" gri pikselini görür. Ancak, beyaz ön plan nesnesi sağ tarafındaki kameranın IR ışını engeller. Sağ taraftaki kameranın "P2" için hiçbir verisi yok.  
   ![Diyagramda, Engellenenler ile aynı noktaya yönlendirilmiş iki kamera gösterilmektedir.](./media/occlusion.png)  
   Daha fazla eşitlenmiş cihaz, occlued verileri sağlayabilir.
- Nesneleri üç boyutta tarayın.
- Etkin kare hızını 30 kareden (FPS) büyük bir değere yükseltin.
- Aynı sahneye ait, hepsi de pozlama merkezinin 100 mikrosaniye cinsinden hizalanan birden çok 4K renk görüntüsünü yakalayın &mu; .
- Alan içindeki kamera kapsamını artırın.

## <a name="plan-your-multi-device-configuration"></a>Çoklu cihaz yapılandırmanızı planlayın

Başlamadan önce [Azure Kinect dk donanım belirtimlerini](hardware-specification.md) ve [Azure Kinect dk derinlik kamerasını](depth-camera.md)gözden geçirdiğinizden emin olun.

> [!NOTE]  
> Dış plastik kapağı kaldırarak eşitleme ve eşitleme jakları kullanıma sunun.

### <a name="select-a-device-configuration"></a>Bir cihaz yapılandırması seçin

Cihaz yapılandırmanız için aşağıdaki yaklaşımlardan birini kullanabilirsiniz:

- Zincirleme **yapılandırma**. Bir ana cihazı ve en fazla sekiz alt cihazı eşitler.  
   ![Azure Kinect DK cihazlarının bir bağlantı zinciri yapılandırmasında nasıl bağlanacağını gösteren diyagram.](./media/multicam-sync-daisychain.png)
- **Yıldız yapılandırması**. Bir ana cihazı ve en fazla iki alt cihazı eşitler.  
   ![Bir yıldız yapılandırmasında birden çok Azure DK cihazı ayarlamayı gösteren diyagram.](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>Dış eşitleme tetikleyicisi kullanma

Her iki yapılandırmada de ana cihaz, bağımlı cihazlar için tetikleme sinyali sağlar. Ancak, eşitleme tetikleyicisi için özel bir dış kaynak kullanabilirsiniz. Örneğin, bu seçeneği, görüntü yakalamalarını diğer ekipmanlarla senkronize etmek için kullanabilirsiniz. Zincir yapılandırmasında veya yıldız yapılandırmasında, dış tetikleyici kaynağı ana cihaza bağlanır.

Dış tetikleyici kaynağınız ana cihazla aynı şekilde çalışır olmalıdır. Aşağıdaki özelliklere sahip bir eşitleme sinyali teslim etmelidir:

- Etkin yüksek
- Darbe genişliği: 8 ' den büyük &mu;
- 5 V TTL/CMOS
- En fazla itici kapasitesi: 8 milimetre AMPS (mA)
- Sıklık desteği: tam olarak 30 FPS, 15 FPS ve 5 FPS (renk Kamerası ana VSYNC sinyalinin sıklığı)

Tetikleyici kaynağı, 3,5-mm ses kablosu kullanarak bağlantı noktasında ana cihaz **eşitlemesine** sinyali teslim etmelidir. Bir stereo ya da mono kablosu kullanabilirsiniz. Azure Kinect, ses kablosu bağlayıcısının tüm sıvayıp ve halkalarını bir arada ve BT 'ye göre kısaltaşır. Aşağıdaki diyagramda gösterildiği gibi, cihaz yalnızca bağlayıcı ipucundan eşitleme sinyalini alır.

![Dış tetikleyici sinyali için kablo yapılandırması](./media/resources/camera-trigger-signal.jpg)

Dış ekipmanla çalışma hakkında daha fazla bilgi için bkz. [dış eşitlenmiş cihazlarla Azure Kinect Kaydedicisini kullanma](record-external-synchronized-units.md)

> [!NOTE]  
> Sync, RGB kameranın VSync. Tüm cihazların zaman damgaları sıfıra ayarlanır ve sayılır. Microsoft, eşitleme Pulse en küçük ve en büyük genişliğini nitelememiş ve bir Azure Kinect DK 'den eşitleme tarafından oluşturulan Pulse bir izleme işlemi yapmanızı öneririz.

### <a name="plan-your-camera-settings-and-software-configuration"></a>Kamera ayarlarınızı ve yazılım yapılandırmanızı planlayın

Kameraları denetlemek ve görüntü verilerini kullanmak üzere yazılımınızı ayarlama hakkında daha fazla bilgi için bkz. [Azure Kinect ALGıLAYıCı SDK](about-sensor-sdk.md).

Bu bölümde, eşitlenen cihazları etkileyen (ancak tek cihazları değil) çeşitli faktörler açıklanmaktadır. Yazılımınız bu faktörleri göz önünde bulundurmalıdır.

#### <a name="exposure-considerations"></a>Pozlama konuları
Her bir cihazın kesin zamanlamasını denetlemek isterseniz, el ile pozlama ayarı kullanmanızı öneririz. Otomatik poz ayarı altında, her renk kamerası gerçek pozlamayı dinamik olarak değiştirebilir. Pozlama zamanlamayı etkilediği için, bu değişiklikler kameraları daha hızlı bir şekilde eşitlenmemiş şekilde gönderir.

Görüntü yakalama döngüsünde, aynı pozlama ayarını sürekli olarak ayarlamayı önleyin. Gerektiğinde API 'YI yalnızca bir kez çağırın.

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>Birden çok derinlik kamera arasında Girişim Önleme

Birden çok derinlik Kamerası, görünümün çakışan alanlarını görüntülikteyse, her kameranın kendi ilişkili Laser 'i görüntülemesi gerekir. Balıklarını 'in birbiriyle kesintiye uğramasını engellemek için kamera yakalamaları, 160 μs veya daha fazla bir diğerinden daha fazla olmalıdır.

Her derinlik Kamerası yakalaması için lazer, dokuz kez döner ve her seferinde yalnızca 125 s için etkindir &mu; . Daha sonra, &mu; &mu; işlem moduna bağlı olarak, lazer, 14505 s veya 23905 s için boşta kalır. Bu davranış, fark hesaplamasının başlangıç noktasının 125 olduğu anlamına gelir &mu; .

Ayrıca, kamera saati ile cihaz üretici yazılımı saati arasındaki farklar 160 s olan en düşük sapmayı artırır &mu; . Yapılandırmanız için daha kesin bir konum hesaplamak üzere, kullanmakta olduğunuz derinlik modunu unutmayın ve [derinlik algılayıcısı ham zamanlama tablosuna](hardware-specification.md#depth-sensor-raw-timing)bakın. Bu tablodaki verileri kullanarak, aşağıdaki denklemi kullanarak en düşük sapmayı (her kameranın pozlama süresi) hesaplayabilirsiniz:

> *Pozlama süresi* = (*IR darbelerler* &times; *Pulse width*) + (*boşta kalma süreleri* &times; *boşta kalma süresi*)

160 s 'nin bir sapmasını kullandığınızda &mu; , diğer balıklarını boşta kaldığında her bir lazer açık olacak şekilde en fazla dokuz ek derinlik kamerada yapılandırabilirsiniz.

Yazılımınıza, ```depth_delay_off_color_usec``` ```subordinate_delay_off_master_usec``` her IR Laser 'nin kendi 160 &mu; s penceresinde veya farklı bir görünüm alanına sahip olduğundan emin olmak için veya kullanın.

> [!NOTE]  
> Gerçek Pulse genişliği 125abd, ancak biraz daha kolay bir şekilde sunmamız durumunda. Örnek olarak NFOV yok alınıyor, her 125US Pulse, 1450us boşta ile izlenir. Bu yukarı alma-(9 x 125) + (8 x 1450)-12.8 MS 'nin etkilenme süresini verir. 2 Cihazın açığa çıkmasına neden olan Closet, ilk kameranın ilk boşta kalma süresine düşecek ikinci kameranın ilk Pulse olması olabilir. Birinci ve ikinci kameralar arasındaki gecikme 125US (bir Pulse genişliği) kadar az olabilir, ancak bu nedenle 160u bu şekilde biraz daha önerilir. Verilen 160US, en fazla 10 kameradan oluşan pozlama dönemlerini ayırmada kullanabilirsiniz.

## <a name="prepare-your-devices-and-other-hardware"></a>Cihazlarınızı ve diğer donanımınızı hazırlayın

Birden çok Azure Kinect DK cihazına ek olarak, derlemek istediğiniz yapılandırmayı desteklemek için ek konak bilgisayarları ve başka donanımlar almanız gerekebilir. Kuruluma başlamadan önce tüm cihazların ve donanımların kullanılabilir olduğundan emin olmak için bu bölümdeki bilgileri kullanın.

### <a name="azure-kinect-dk-devices"></a>Azure Kinect DK cihazları

Eşitlemesini istediğiniz her Azure Kinect DK aygıtı için aşağıdakileri yapın:

- Cihazda en son bellenim 'un yüklü olduğundan emin olun. Cihazlarınızı güncelleştirme hakkında daha fazla bilgi için [Azure Kinect dk bellenimini güncelleştirme](update-device-firmware.md)bölümüne gidin. 
- Eşitleme bağlantı noktalarını açığa çıkarmak için cihaz kapağını kaldırın.
- Her bir cihazın seri numarasını aklınızda edin. Bu numarayı kurulum sürecinin ilerleyen kısımlarında kullanacaksınız.

### <a name="host-computers"></a>Konak bilgisayarlar

Genellikle, her Azure Kinect DK kendi ana bilgisayarını kullanır. Cihazı nasıl kullandığınıza ve USB bağlantısı üzerinden ne kadar veri aktarıldığını bağlı olarak, adanmış bir ana bilgisayar denetleyicisi kullanabilirsiniz. 

Azure Kinect algılayıcı SDK 'sının her bir ana bilgisayarda yüklü olduğundan emin olun. Algılayıcı SDK 'nın nasıl yükleneceği hakkında daha fazla bilgi için [hızlı başlangıç: Azure Kinect dk 'Yi ayarlama](set-up-azure-kinect-dk.md)sayfasına gidin. 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Linux bilgisayarları: Ubuntu üzerinde USB belleği

Varsayılan olarak, Linux tabanlı ana bilgisayar bilgisayarları USB denetleyicilerini işlemek için yalnızca 16 MB çekirdek belleği ayırır. Bu miktar genellikle tek bir Azure Kinect DK desteklemek için yeterlidir. Ancak, birden çok cihazı desteklemek için, USB denetleyicinin daha fazla belleğe sahip olması gerekir. Belleği artırmak için aşağıdaki adımları izleyin:

1. Düzenle/**etc/varsayılan/grub**.
1. Aşağıdaki satırı bulun:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   Bu satırı kullanarak değiştirin:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > Bu komutlar USB belleğini 32 MB olarak ayarlar. Bu, varsayılan değerin iki katı olan örnek bir ayardır. Çözümünüz için uygun olan çok daha büyük bir değer belirleyebilirsiniz.
1. **Sudo Update-grub** çalıştırın.
1. Bilgisayarı yeniden başlatın.

### <a name="cables"></a>Kablolar

Cihazları birbirine ve ana bilgisayarlara bağlamak için, 3,5-mm erkek erkek-erkek kablolarını (3,5-mm ses kablosu olarak da bilinir) kullanmanız gerekir. Kablolar 10 metreden uzun olmalıdır ve stereo veya mono olabilir.

Sahip olmanız gereken kablo sayısı, kullandığınız cihaz sayısına ve ayrıca belirli cihaz yapılandırmasına bağlıdır. Azure Kinect DK kutusunda kablolar yoktur. Bunları ayrı satın almalısınız.

Cihazları yıldız yapılandırmasında bağladığınızda, bir kulaklık Bölümlendirici de olmalıdır.

## <a name="connect-your-devices"></a>Cihazlarınızı bağlama

**Azure Kinect DK cihazlarını bir bağlantı zinciri yapılandırmasında bağlamak için**

1. Her Azure Kinect DK 'yi güce bağlayın.
1. Her cihazı ana bilgisayar bilgisayarına bağlayın. 
1. Ana cihaz olacak bir cihaz seçin ve bir 3,5-mm ses kablosunu, **eşitleme çıkış** bağlantı noktasına takın.
1. Kablonun diğer sonunu, ilk alt cihazın bağlantı noktasındaki **eşitlemeye** bağlayın.
1. Başka bir cihazı bağlamak için, ilk alt cihazın **eşitleme çıkış** bağlantı noktasına ve sonraki cihazın bağlantı noktasındaki **eşitleme** ' ye başka bir kablo takın.
1. Tüm cihazlar bağlanana kadar önceki adımı tekrarlayın. Son cihazda yalnızca bir kablo bağlantısı olmalıdır. **Eşitlenmiş** bağlantı noktası boş olmalıdır.

**Azure Kinect DK cihazlarını bir yıldız yapılandırmasında bağlamak için**

1. Her Azure Kinect DK 'yi güce bağlayın.
1. Her cihazı ana bilgisayar bilgisayarına bağlayın. 
1. Ana cihaz olmak üzere bir cihaz seçin ve kulaklık bölücü 'un tek sonunu **eşitleme çıkış** bağlantı noktasına bağlayın.
1. 3,5-mm ses kablolarını, kulaklık Bölümlendiricinin "bölünmüş" uçlarına bağlayın.
1. Her kablonun diğer sonunu, bağımlı cihazlardan birinin bağlantı noktasındaki **eşitlemeye** bağlayın.

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>Cihazların bağlı ve iletişim kurduğunu doğrulama

Cihazların doğru şekilde bağlandığını doğrulamak için [Azure Kinect görüntüleyicisini](azure-kinect-viewer.md)kullanın. Her bir alt cihazı ana cihazla birlikte sınamak için gereken şekilde bu yordamı tekrarlayın

> [!IMPORTANT]  
> Bu yordam için, her bir Azure Kinect DK 'nin seri numarasını bilmeniz gerekir.

1. Azure Kinect görüntüleyicisinin iki örneğini açın.
1. **Cihazı aç** altında, test etmek istediğiniz alt cihazın seri numarasını seçin.  
   ![Cihazı aç](./media/open-devices.png)
   > [!IMPORTANT]  
   > Tüm cihazlar arasında tam görüntü yakalama hizalaması almak için, ana cihazı en son başlatmanız gerekir.  
1. **Dış eşitleme** altında **Sub**' ı seçin.  
   ![Alt kamera başlangıcı](./media/sub-device-start.png)
1.  **Başlat**'ı seçin.  
    > [!NOTE]  
    > Bu bir alt cihaz olduğundan, Azure Kinect Görüntüleyicisi cihaz başladıktan sonra bir görüntü görüntülemez. Alt cihaz ana cihazdan bir eşitleme sinyali alıncaya kadar görüntü gösterilmez.
1. Alt cihaz başlatıldıktan sonra, ana cihazı açmak için Azure Kinect Görüntüleyicisi 'nin diğer örneğini kullanın.
1. **Dış eşitleme** altında, **ana**' yı seçin.
1. **Başlat**'ı seçin.

Ana Azure Kinect cihazı başlatıldığında, her iki Azure Kinect Görüntüleyici örneği de görüntüleri görüntülemelidir.

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>Cihazları eşitlenmiş küme olarak ayarlama

Cihazların doğru iletişim kurduğunu doğruladıktan sonra, bunları tek bir etki alanında görüntü oluşturmaya hazırsınız demektir.

Tek bir cihazda, derinlik ve RGB kameralarının birlikte çalışması için fabrikada ayarlanmış olması gerekir. Ancak, birden çok cihazın birlikte çalışması gerektiğinde, görüntüleri işlemek için kullanmak istediğiniz kameranın etki alanına bir görüntünün nasıl dönüştürülmesinin belirlenmesi için bunların ayarlanması gerekir.

Çapraz ayarlama cihazları için birden çok seçenek vardır. Microsoft, OpenCV yöntemini kullanan [GitHub yeşil ekran kodu örneğini](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen)sağlar. Bu kod örneği için Readme dosyası, cihazları ayarlamaya yönelik daha fazla ayrıntı ve yönergeler sağlar.

Ayarlama hakkında daha fazla bilgi için bkz. [Azure Kinect ayarlama Işlevlerini kullanma](use-calibration-functions.md).

## <a name="next-steps"></a>Sonraki adımlar

Eşitlenmiş cihazları ayarladıktan sonra, nasıl kullanacağınızı de öğrenebilirsiniz
> [!div class="nextstepaction"]
> [Azure Kinect algılayıcı SDK kaydı ve kayıttan yürütme API 'SI](record-playback-api.md)

## <a name="related-topics"></a>İlgili konular

- [Azure Kinect algılayıcı SDK hakkında](about-sensor-sdk.md)
- [Azure Kinect DK donanım belirtimleri](hardware-specification.md) 
- [Hızlı başlangıç: Azure Kinect DK 'yi ayarlama](set-up-azure-kinect-dk.md) 
- [Azure Kinect DK bellenimini güncelleştirme](update-device-firmware.md) 
- [Azure Kinect DK’yi sıfırlama](reset-azure-kinect-dk.md) 
- [Azure Kinect Görüntüleyicisi](azure-kinect-viewer.md) 
