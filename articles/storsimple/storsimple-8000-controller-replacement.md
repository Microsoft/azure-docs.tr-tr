---
title: StorSimple 8000 serisi cihaz denetleyicisini değiştirme | Microsoft Docs
description: StorSimple 8000 serisi cihazındaki bir veya iki denetleyici modülünü kaldırmayı ve değiştirmeyi açıklar.
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
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 9d8b75c48da2bb13d843258ead378d3e849da951
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514088"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>StorSimple cihazınızda bir denetleyici modülünü değiştirme
## <a name="overview"></a>Genel Bakış
Bu öğreticide, StorSimple cihazında bir veya her iki denetleyici modülünü kaldırma ve değiştirme işlemleri açıklanmaktadır. Ayrıca, tek ve çift denetleyici değiştirme senaryolarında temel alınan mantığı da açıklar.

> [!NOTE]
> Bir denetleyici değişikliği yapmadan önce, denetleyici bellenimini her zaman en son sürüme güncelleştirmenizi öneririz.
> 
> StorSimple cihazınızda hasar oluşmasını engellemek için, LED 'ler aşağıdakilerden biri olarak gösterilene kadar denetleyiciyi çıkarmayın:
> 
> * Tüm ışıklar kapalı.
> * LED 3, ![ yeşil onay simgesi ](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png) ve ![ kırmızı çapraz simgesi ](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) yanıp sönülüyor, 0 ve LED 7 **Açık**.


Aşağıdaki tabloda, desteklenen denetleyici değiştirme senaryoları gösterilmektedir.

| Case (Olay) | Değiştirme senaryosu | Uygulanabilir yordam |
|:--- |:--- |:--- |
| 1 |Bir denetleyici başarısız durumunda, diğer denetleyici sağlıklı ve etkin olur. |Tek [bir denetleyici değişikliği arkasındaki mantığı](#single-controller-replacement-logic)ve [değiştirme adımlarını](#single-controller-replacement-steps)açıklayan [tek denetleyicideki değiştirme](#replace-a-single-controller). |
| 2 |Her iki denetleyici de başarısız oldu ve değişiklik gerektiriyor. Kasa, diskler ve disk Kasası sağlıklı. |İki denetleyicide değişiklik ve [değiştirme adımlarının](#dual-controller-replacement-steps) [arkasındaki mantığı](#dual-controller-replacement-logic)açıklayan [çift denetleyici değiştirme](#replace-both-controllers). |
| 3 |Aynı cihazdan veya farklı cihazlardan olan denetleyiciler takas edilir. Kasa, diskler ve disk kasaları sağlıklı. |Yuva uyumsuzluğu uyarı iletisi görüntülenir. |
| 4 |Bir denetleyici eksik ve diğer denetleyici başarısız oluyor. |İki denetleyicide değişiklik ve [değiştirme adımlarının](#dual-controller-replacement-steps) [arkasındaki mantığı](#dual-controller-replacement-logic)açıklayan [çift denetleyici değiştirme](#replace-both-controllers). |
| 5 |Bir veya iki denetleyici başarısız oldu. Cihaza seri konsol veya Windows PowerShell uzaktan iletişimi üzerinden erişemezsiniz. |El ile denetleyici değiştirme yordamı için [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md) . |
| 6 |Denetleyicilerin farklı bir derleme sürümü var ve bu durum şu nedenlerle olabilir:<ul><li>Denetleyicilerin farklı bir yazılım sürümü vardır.</li><li>Denetleyicilerin farklı bir bellenim sürümü vardır.</li></ul> |Denetleyici yazılım sürümleri farklıysa, değiştirme mantığı bunu algılar ve değiştirme denetleyicisindeki yazılım sürümünü güncelleştirir.<br><br>Denetleyici üretici yazılımı sürümleri farklıysa ve eski bellenim sürümü otomatik olarak **yükseltilemeyen** , Azure Portal bir uyarı iletisi görüntülenir. Güncelleştirmeleri taramalı ve bellenim güncelleştirmelerini yüklemelisiniz.</br></br>Denetleyici üretici yazılımı sürümleri farklıysa ve eski bellenim sürümü otomatik olarak yükseltilecektir, denetleyici değiştirme mantığı bunu algılar ve denetleyici başladıktan sonra, bellenim otomatik olarak güncelleştirilir. |

Başarısız olduysa bir denetleyici modülünü kaldırmanız gerekir. Denetleyici modüllerinin biri veya her ikisi başarısız olabilir, bu da tek bir denetleyici değiştirme veya çift denetleyici değişikliği oluşmasına neden olabilir. Değiştirme yordamları ve bunların arkasındaki mantık için aşağıdakilere bakın:

* [Tek bir denetleyiciyi değiştirme](#replace-a-single-controller)
* [Her iki denetleyicisi de değiştir](#replace-both-controllers)
* [Denetleyiciyi kaldırma](#remove-a-controller)
* [Denetleyici ekleme](#insert-a-controller)
* [Cihazınızda etkin denetleyiciyi tanımla](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Denetleyiciyi kaldırmadan ve değiştirmeden önce, [StorSimple donanım bileşeni değiştirme](storsimple-8000-hardware-component-replacement.md)içindeki güvenlik bilgilerini gözden geçirin.
> 
> 

## <a name="replace-a-single-controller"></a>Tek bir denetleyiciyi değiştirme
Microsoft Azure StorSimple cihazdaki iki denetleyiciden biri başarısız olduğunda, hatalı çalışan veya eksik olduğunda, tek bir denetleyiciyi değiştirmeniz gerekir.

### <a name="single-controller-replacement-logic"></a>Tek denetleyicideki değiştirme mantığı
Tek denetleyicide değiştirme sırasında, önce başarısız olan denetleyiciyi kaldırmalısınız. (Cihazdaki kalan denetleyici etkin denetleyicdir.) Değiştirme denetleyicisi eklediğinizde aşağıdaki eylemler gerçekleşir:

1. Değiştirme denetleyicisi, StorSimple cihazından hemen iletişim kurmaya başlar.
2. Etkin denetleyicinin sanal sabit diskinin (VHD) anlık görüntüsü değiştirme denetleyicisinde kopyalanır.
3. Anlık görüntü, değiştirme denetleyicisi bu VHD 'den başladığında, bir bekleme denetleyicisi olarak tanınabilmesi için değiştirilir.
4. Değişiklikler tamamlandığında, değiştirme denetleyicisi bekleme denetleyicisi olarak başlatılır.
5. Her iki denetleyici çalışırken, küme çevrimiçi duruma gelir.

### <a name="single-controller-replacement-steps"></a>Tek denetleyicideki değiştirme adımları
Microsoft Azure StorSimple cihazınızdaki denetleyicilerden biri başarısız olursa aşağıdaki adımları izleyin. (Diğer denetleyicinin etkin ve çalışıyor olması gerekir. Her iki denetleyici de başarısız olur veya arızası olursa, [çift denetleyici değiştirme adımları](#dual-controller-replacement-steps)bölümüne gidin.)

> [!NOTE]
> Denetleyicinin yeniden başlatılması ve tamamen kurtarması için tek denetleyici değiştirme yordamından 30 ila 45 dakika sürebilir. Tüm yordamın, kabloların eklenmesi dahil toplam süresi yaklaşık 2 saattir.


#### <a name="to-remove-a-single-failed-controller-module"></a>Tek bir başarısız denetleyici modülünü kaldırmak için
1. Azure portal, StorSimple Device Manager hizmetine gidin, **cihazlar**' a tıklayın ve ardından izlemek istediğiniz cihazın adına tıklayın.
2. **> donanım durumunu izle**' ye gidin. Denetleyicinin 0 veya denetleyici 1 durumu kırmızı olmalıdır, bu da bir hatayı gösterir.
   
   > [!NOTE]
   > Tek denetleyicideki başarısız denetleyici her zaman bir bekleme denetleyicisidir.
   
3. Başarısız denetleyici modülünü bulmak için Şekil 1 ' i ve aşağıdaki tabloyu kullanın.
   
    ![Cihaz birincil kutu modüllerinin geri düzlemi](./media/storsimple-controller-replacement/IC740994.png)
   
    **Şekil 1** StorSimple cihazının geri dönmesi
   
   | Etiketle | Açıklama |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Denetleyici 0 |
   | 4 |Denetleyici 1 |
4. Başarısız olan denetleyicide, tüm bağlı ağ kablolarını veri bağlantı noktalarından kaldırın. 8600 modeli kullanıyorsanız, denetleyiciyi EBOD denetleyicisine bağlayan SAS kablolarını da kaldırın.
5. Başarısız olan denetleyiciyi kaldırmak için [denetleyiciyi kaldırma](#remove-a-controller) bölümündeki adımları izleyin.
6. Fabrika değişimini, başarısız denetleyicinin kaldırıldığı aynı yuvaya takın. Bu, tek denetleyici değiştirme mantığını tetikler. Daha fazla bilgi için bkz. [tek denetleyici değiştirme mantığı](#single-controller-replacement-logic).
7. Tek denetleyici değiştirme mantığı arka planda ilerlerken, kabloları yeniden bağlayın. Tüm kablolarını, değiştirmeden önce bağlı oldukları şekilde bağlama işlemini gerçekleştirin.
8. Denetleyici yeniden başlatıldıktan sonra, denetleyicinin sağlıklı bir duruma geri olduğunu ve bekleme modunda olduğunu doğrulamak için Azure portal **Denetleyici durumunu** ve **küme durumunu** kontrol edin.

> [!NOTE]
> Cihazı seri konsol aracılığıyla izliyorsanız, denetleyici değiştirme yordamından kurtarılırken birden çok yeniden başlatma görebilirsiniz. Seri konsol menüsü sunulursa, değiştirmenin tamamlandığını anlarsınız. Menü, denetleyicinin değiştirilmesini başlatmak üzere iki saat içinde görünmezse, lütfen [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md).
>
> Güncelleştirme 4 ' ü başlattıktan sonra, `Get-HCSControllerReplacementStatus` Denetleyici değiştirme işleminin durumunu izlemek için aygıtın Windows PowerShell arabirimindeki cmdlet 'ini de kullanabilirsiniz.
> 

## <a name="replace-both-controllers"></a>Her iki denetleyicisi de değiştir
Microsoft Azure StorSimple cihazdaki her iki denetleyici de başarısız olduğunda, hatalı çalışan veya eksik olduğunda her iki denetleyicisi de değiştirmeniz gerekir. 

### <a name="dual-controller-replacement-logic"></a>Çift denetleyici değiştirme mantığı
İki denetleyicide değiştirme sırasında, önce başarısız olan denetleyicileri kaldırmalı ve ardından değiştirmeler eklersiniz. İki değiştirme denetleyicisi takıldığında, aşağıdaki eylemler gerçekleşir:

1. Yuva 0 ' daki değiştirme denetleyicisi aşağıdakileri denetler:
   
   1. BT yazılımının ve yazılımın güncel sürümlerini kullanıyor mu?
   2. Kümenin bir parçası mi?
   3. Eş denetleyici çalışıyor ve kümelenmiş mi?
      
      Bu koşullardan hiçbiri doğru değilse, denetleyici en son günlük yedeği arar (sürücüdeki **Domdepolamada** bulunur). Denetleyici, VHD 'nin en son anlık görüntüsünü yedekten kopyalar.
2. Yuva 0 ' daki denetleyici görüntünün kendisi için anlık görüntüyü kullanır.
3. Bu arada, yuva 1 ' deki denetleyici, denetleyici 0 ' ı, görüntüleme ve başlatma işleminin tamamlanmasını bekler.
4. Denetleyici 0 başladıktan sonra, denetleyici 1, denetleyici 0 tarafından oluşturulan kümeyi algılar ve bu, tek denetleyici değiştirme mantığını tetikler. Daha fazla bilgi için bkz. [tek denetleyici değiştirme mantığı](#single-controller-replacement-logic).
5. Daha sonra, her iki denetleyici de çalışır ve küme çevrimiçi olarak gelir.

> [!IMPORTANT]
> İki denetleyici değişimini izleyerek StorSimple cihazı yapılandırıldıktan sonra, cihazın el ile yedeklenmesini yapmanız gerekir. Günlük cihaz yapılandırma yedeklemeleri, 24 saat geçtikten sonra tetiklenmez. Cihazınızın el ile yedeklenmesini sağlamak için [Microsoft desteği](storsimple-8000-contact-microsoft-support.md) ile çalışın.


### <a name="dual-controller-replacement-steps"></a>Çift denetleyici değiştirme adımları
Bu iş akışı, Microsoft Azure StorSimple cihazındaki denetleyicilerin her ikisi başarısız olduğunda gereklidir. Bu, soğutma sisteminin çalışmayı durdurduğu bir veri merkezinde gerçekleşebilir ve sonuç olarak, her iki denetleyici de kısa bir süre içinde başarısız olur. StorSimple cihazının kapalı veya açık olmasına bağlı olarak ve 8600 ya da 8100 modeli kullanıp kullanmayacağınızı, farklı bir adım kümesi yapmanız gerekir.

> [!IMPORTANT]
> Denetleyicinin yeniden başlatılması ve bir çift denetleyici değiştirme yordamından tamamen kurtarılması için 45 dakika ila 1 saat sürebilir. Tüm yordamın toplam süresi, kabloları ekleme da dahil olmak üzere yaklaşık 2,5 saattir.

#### <a name="to-replace-both-controller-modules"></a>Her iki denetleyici modülünü değiştirmek için
1. Cihaz kapalıysa, bu adımı atlayın ve sonraki adıma geçin. Cihaz açıksa, cihazı kapatın.
   
   1. 8600 modeli kullanıyorsanız, önce birincil Kasası kapatın ve ardından EBOD kasasının oturumunu kapatın.
   2. Cihaz tamamen kapanıncaya kadar bekleyin. Cihazın arkasında bulunan tüm LED 'ler kapalı olacaktır.
2. Veri bağlantı noktalarına bağlı olan tüm ağ kablolarını kaldırın. 8600 modeli kullanıyorsanız, birincil Kasası EBOD Kasası 'na bağlayan SAS kablolarını de kaldırın.
3. StorSimple cihazından her iki denetleyicisi de kaldırın. Daha fazla bilgi için bkz. [denetleyiciyi kaldırma](#remove-a-controller).
4. İlk olarak denetleyici 0 için fabrika yerini ekleyin ve ardından denetleyici 1 ' i takın. Daha fazla bilgi için bkz. [denetleyici ekleme](#insert-a-controller). Bu, çift denetleyici değiştirme mantığını tetikler. Daha fazla bilgi için bkz. [çift denetleyici değiştirme mantığı](#dual-controller-replacement-logic).
5. Denetleyici değiştirme mantığı arka planda ilerlerken, kabloları yeniden bağlayın. Tüm kablolarını, değiştirmeden önce bağlı oldukları şekilde bağlama işlemini gerçekleştirin. [Storsimple 8100 cihazınızı yükledikten](storsimple-8100-hardware-installation.md) veya [StorSimple 8600 cihazınızı yüklemenize](storsimple-8600-hardware-installation.md)yönelik cihazınızı kablo bölümünde modelinize yönelik ayrıntılı yönergelere bakın.
6. StorSimple cihazını açın. 8600 modeli kullanıyorsanız:
   
   1. İlk olarak EBOD Kasası 'nın açık olduğundan emin olun.
   2. EBOD Kasası çalışmaya başlamadan bekleyin.
   3. Birincil Kasası açın.
   4. İlk denetleyici yeniden başlatıldıktan ve sağlıklı bir durumda olduktan sonra, sistem çalışır.
      
      > [!NOTE]
      > Cihazı seri konsol aracılığıyla izliyorsanız, denetleyici değiştirme yordamından kurtarılırken birden çok yeniden başlatma görebilirsiniz. Seri konsol menüsü göründüğünde, değiştirmenin tamamlandığını anlarsınız. Menü, denetleyicinin değiştirilmesini başlatırken 2,5 saat içinde görünmezse, lütfen [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Denetleyiciyi kaldırma
StorSimple cihazınızdan hatalı bir denetleyici modülünü kaldırmak için aşağıdaki yordamı kullanın.

> [!NOTE]
> Aşağıdaki çizimler denetleyici 0 ' dır. Denetleyici 1 için bunlar tersine çevrilir.


#### <a name="to-remove-a-controller-module"></a>Bir denetleyici modülünü kaldırmak için
1. Parmak izi ve üzüzlü arasında modül mandalı.
2. Parmak izi ve bir araya getirme, denetleyici mandalı yayınlanması için bir araya gelir.
   
    ![Denetleyici mandalı bırakılıyor](./media/storsimple-controller-replacement/IC741047.png)
   
    **Şekil 2** Denetleyici mandalı bırakılıyor
3. Denetleyiciyi kasadan dışarı kaydır için mandalı bir tanıtıcı olarak kullanın.
   
    ![Kayan denetleyici kasa dışı](./media/storsimple-controller-replacement/IC741048.png)
   
    **Şekil 3** Denetleyiciyi kasadan yukarı kaydırma

## <a name="insert-a-controller"></a>Denetleyici ekleme
StorSimple cihazınızdan hatalı bir modülü kaldırdıktan sonra fabrika tarafından sağlanan bir denetleyici modülünü yüklemek için aşağıdaki yordamı kullanın.

#### <a name="to-install-a-controller-module"></a>Denetleyici modülünü yüklemek için
1. Arabirim bağlayıcılarına zarar olup olmadığını kontrol edin. Bağlayıcı PIN 'lerinin herhangi biri hasar görmüşse veya Bükülü ise modülü yüklemeyin.
2. Mandal tamamen serbest bırakıldığında denetleyici modülünü kasaya kaydırın.
   
    ![Denetleyiciyi kasaya kaydırma](./media/storsimple-controller-replacement/IC741053.png)
   
    **Şekil 4** , Kasaya kayan denetleyici
3. Denetleyici modülü takıldığında, denetleyici modülünü kasaya göndermeye devam ederken mandalı kapatmaya başlayın. Mandal, denetleyiciyi bir yere yönlendirecek şekilde yönlendirecektir.
   
    ![Denetleyici mandalı kapatılıyor](./media/storsimple-controller-replacement/IC741054.png)
   
    **Şekil 5** Denetleyici mandalı kapatılıyor
4. Mandal bir yere yaslantığında işiniz bitti. **Tamam** ışığı artık açık olmalıdır.
   
   > [!NOTE]
   > Denetleyicinin ve etkinleştirilmesi için 5 dakikaya kadar zaman alabilir.
  
5. Değiştirme işleminin başarılı olduğunu doğrulamak için, Azure Portal cihazınıza gidin ve ardından   >  **Donanım durumunu** İzle ' ye gidin ve hem denetleyicinin 0 ve denetleyici 1 ' in sağlıklı olduğundan emin olun (durum yeşil).

## <a name="identify-the-active-controller-on-your-device"></a>Cihazınızda etkin denetleyiciyi tanımla
Bir StorSimple cihazında etkin denetleyiciyi bulmanızı gerektiren, ilk kez cihaz kaydı veya denetleyici değiştirme gibi birçok durum vardır. Etkin denetleyici tüm disk üretici yazılımı ve ağ işlemlerini işler. Etkin denetleyiciyi tanımlamak için aşağıdaki yöntemlerden herhangi birini kullanabilirsiniz:

* [Etkin denetleyiciyi tanımlamak için Azure portal kullanın](#use-the-azure-portal-to-identify-the-active-controller)
* [Etkin denetleyiciyi tanımlamak için StorSimple için Windows PowerShell kullanın](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Etkin denetleyiciyi belirlemek için fiziksel cihazı denetleyin](#check-the-physical-device-to-identify-the-active-controller)

Bu yordamların her biri, ileri ' ye açıklanmıştır.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Etkin denetleyiciyi tanımlamak için Azure portal kullanın
Azure Portal cihazınıza gidip   >  **donanım sistem durumunu** izleyin ve **denetleyiciler** bölümüne gidin. Burada, hangi denetleyicinin etkin olduğunu doğrulayabilirsiniz.

![Azure portal 'de etkin denetleyiciyi tanımla](./media/storsimple-controller-replacement/IC752072.png)

**Şekil 6** Etkin denetleyiciyi gösteren Azure portal

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Etkin denetleyiciyi tanımlamak için StorSimple için Windows PowerShell kullanın
Cihaza seri konsol üzerinden eriştiğinizde bir başlık iletisi görüntülenir. Başlık iletisi, model, ad, yüklü yazılım sürümü ve eriştiğiniz denetleyicinin durumu gibi temel cihaz bilgilerini içerir. Aşağıdaki görüntüde bir başlık iletisi örneği gösterilmektedir:

![Seri başlık iletisi](./media/storsimple-controller-replacement/IC741098.png)

**Şekil 7** Denetleyici 0 etkin olarak gösteren başlık iletisi

Bağlı olduğunuz denetleyicinin etkin veya pasif olduğunu anlamak için başlık iletisini kullanabilirsiniz.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Etkin denetleyiciyi belirlemek için fiziksel cihazı denetleyin
Cihazınızdaki etkin denetleyiciyi belirlemek için, birincil kutunun arkasında bulunan DATA 5 bağlantı noktasının üzerinde mavi renkli bir yol bulun.

Bu LED 'in yanıp sönmesi durumunda, denetleyici etkindir ve diğer denetleyici bekleme modundadır. Aşağıdaki diyagramı ve tabloyu bir yardım olarak kullanın.

![Veri bağlantı noktalarıyla cihaz birincil Kasası arka düzlemi](./media/storsimple-controller-replacement/IC741055.png)

**Şekil 8** Veri bağlantı noktaları ve izleme LED 'Leri ile birincil Kasası geri

| Etiketle | Description |
|:--- |:--- |
| 1-6 |VERI 0 – 5 ağ bağlantı noktası |
| 7 |Mavi ışığı |

## <a name="next-steps"></a>Sonraki adımlar
[StorSimple donanım bileşeni değiştirme](storsimple-8000-hardware-component-replacement.md)hakkında daha fazla bilgi edinin.

