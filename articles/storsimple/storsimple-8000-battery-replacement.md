---
title: Microsoft Azure StorSimple 8000 serisi cihazda pili değiştirme
description: StorSimple cihazınızda yedekleme pil modülünü kaldırma, değiştirme ve koruma işlemlerinin nasıl yapılacağını açıklar.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 17a6a07d117557cdee23634bd211a14cf90d49e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023780"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>StorSimple cihazınızın yedek pil modülünü değiştirme

## <a name="overview"></a>Genel Bakış
Microsoft Azure StorSimple cihazınızdaki birincil muhafaza gücü ve soğutma modülü (PCM), ek bir pil paketine sahiptir. Bu paket, birincil kasada AC gücü kaybı olursa StorSimple cihazının verileri kaydedebilmesi için güç sağlar. Bu pil paketi *Yedekleme Pil modülü* olarak adlandırılır. Yedekleme pili modülü yalnızca StorSimple cihazınızdaki birincil kasa için mevcuttur (EBOD Kasası bir yedekleme pil modülü içermez).

Bu öğreticide, aşağıdaki işlemlerin nasıl yapılacağı açıklanmaktadır:

* Yedekleme Pil modülünü kaldırma
* Yeni bir yedekleme pil modülü yükler
* Yedekleme Pil modülünü koruyun

> [!IMPORTANT]
> Bir yedekleme pil modülünü kaldırmadan ve değiştirmeden önce, [StorSimple donanım bileşeni değiştirme 'ye giriş](storsimple-8000-hardware-component-replacement.md)bölümündeki güvenlik bilgilerini gözden geçirin.


## <a name="remove-the-backup-battery-module"></a>Yedekleme Pil modülünü kaldırma
StorSimple cihazınız için yedekleme pil modülü, alan tarafından değiştirilebilen bir birimdir. PCM 'e yüklenmeden önce, pil modülünün özgün paketinizde depolanması gerekir. Yedekleme pilini kaldırmak için aşağıdaki adımları gerçekleştirin.

#### <a name="to-remove-the-backup-battery-module"></a>Yedekleme Pil modülünü kaldırmak için
1. Azure portal, StorSimple Device Manager hizmeti dikey pencerenize gidin. **Cihazlar** ' a gidin ve cihaz listesinden cihazınızı seçin.   >  **Donanım durumunu** izlemek için gidin. **Paylaşılan bileşenler** altında pilin durumuna bakın.
2. Pilin başarısız olduğu PCM 'yi belirler. Şekil 1 ' de StorSimple cihazının geri gösterilmektedir.
   
    ![Cihaz birincil kutu modüllerinin geri düzlemi](./media/storsimple-battery-replacement/IC740994.png)
   
    **Şekil 1** PCM ve denetleyici modüllerini gösteren birincil cihazın geri dönmesi
   
   | Etiketle | Açıklama |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Denetleyici 0 |
   | 4 |Denetleyici 1 |
   
    Şekil 2 ' de gösterilen sayı 3 ' te gösterildiği gibi, **Pil hatasına** KARŞıLıK gelen PCM 0 ' daki izleme göstergesi açık olmalıdır.
   
    ![Cihaz PCM Izleme göstergesi LED 'Leri arka düzlemi](./media/storsimple-battery-replacement/IC740992.png)
   
    **Şekil 2** İzleme göstergesi LED 'lerini gösteren PCM 'nin geri dönmesi
   
   | Etiketle | Açıklama |
   |:--- |:--- |
   | 1 |AC güç hatası |
   | 2 |Fan hatası |
   | 3 |Pil hatası |
   | 4 |PCM TAMAM |
   | 5 |DC güç hatası |
   | 6 |Pil sağlıklı |
3. PCM 'yi başarısız bir pille kaldırmak için, [PCM 'Yi kaldırma](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm)bölümündeki adımları izleyin.
4. PCM kaldırıldıktan sonra, aşağıdaki şekilde gösterildiği gibi pil modülü tanıtıcısını kaldırın ve döndürün ve pili kaldırmak için çekin.
   
    ![PCM 'Den Pil kaldırma](./media/storsimple-battery-replacement/IC741019.png)
   
    **Şekil 3** PCM 'den Pil kaldırma
5. Modülü alan tarafından değiştirilebilen birim paketlemeye yerleştirin.
6. Doğru bakım ve işleme için kusurlu birimi Microsoft 'a döndürün.

## <a name="install-a-new-backup-battery-module"></a>Yeni bir yedekleme pil modülü yükler
Değiştirme pili modülünü, StorSimple cihazınızın birincil kasasında PCM 'ye yüklemek için aşağıdaki adımları uygulayın.

#### <a name="to-install-the-battery-module"></a>Pil modülünü yüklemek için
1. Yedekleme Pil modülünü PCM 'de doğru yöne yerleştirin.
2. Bağlayıcıyı oturmak için, pil modülünün tüm yolunu azaltın.
3. [StorSimple cihazınızda güç ve soğutma modülünü değiştirme](storsimple-8000-power-cooling-module-replacement.md)konusundaki yönergeleri izleyerek birincil MUHAFAZADAKI PCM 'yi değiştirin.
4. Değiştirme tamamlandıktan sonra cihazınıza gidin ve   >  Azure Portal **Donanım durumunu** İzle ' ye gidin. Yüklemenin başarılı olduğundan emin olmak için pilin durumunu doğrulayın. Yeşil bir durum, pilin sağlıklı olduğunu gösterir.

## <a name="maintain-the-backup-battery-module"></a>Yedekleme Pil modülünü koruyun
StorSimple cihazınızda, yedekleme pil modülü güç kaybı olayı sırasında denetleyiciye güç sağlar. StorSimple cihazının, denetimli bir şekilde kapatmadan önce kritik verileri kaydetmesine izin verir. PCMs 'de tamamen ücretlendirilen iki pille, sistem art arda iki kayıp olayını işleyebilir.

Azure portal, **izleyici** dikey penceresindeki **donanım sistem durumu** , pilin arızalı mi yoksa yaşam süresi mi yaklaşdığını gösterir. Pil durumu, **paylaşılan bileşenler** altında **PCM 0** veya **PCM 1 ' de pille** bir şekilde gösterilir. Bu dikey pencere, son kullanım süresi boyunca **düşürülmüş** bir durum gösterir ve kullanım ömrü boyunca **başarısız** olur.

> [!NOTE]
> Yalnızca ücretlendirililmesi gerektiğinde, pilin **başarısız** olduğunu raporlayabilir.


**Düşürülmüş** durum görünürse, aşağıdaki eylem kursu önerilir:

* Sistem yakın bir güç kaybına neden olmuş olabilir veya piller düzenli olarak bakım yaşıyor olabilir. Devam etmeden önce sistemi 12 saat boyunca gözlemleyin.
  
  * Çalışan denetleyiciler ve PCMs 'Ler ile 12 saatlik sürekli bağlantı sonrasında durum hala **düşerse** , pilin değişmesi gerekir. Lütfen yedek bir yedekleme pil modülü için [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md) .
  * Durum 12 saat sonra Tamam olursa, pil çalışır durumdadır ve yalnızca bir bakım ücreti gerekir.
* AC gücünün ilişkili bir kaybı yoksa ve PCM açık ve AC gücüne bağlıysa, pilin değişmesi gerekir. Yedek bir yedekleme pil modülünü sıralamak için [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md) .

> [!IMPORTANT]
> Ulusal ve bölgesel düzenlemelere göre başarısız pili atın.

## <a name="next-steps"></a>Sonraki adımlar
[StorSimple donanım bileşeni değiştirme](storsimple-8000-hardware-component-replacement.md)hakkında daha fazla bilgi edinin.

