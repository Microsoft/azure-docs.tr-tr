---
title: StorSimple 8000 serisi cihaz için uyarıları görüntüleyin ve yönetin
description: StorSimple uyarı koşullarını ve önem derecesini, uyarı bildirimlerinin nasıl yapılandırılacağını ve bildirimleri yönetmek için StorSimple Device Manager hizmetinin nasıl kullanılacağını açıklar.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 36ed87a75d590a8647e5347b3e90e6f5159dc6b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016854"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>StorSimple uyarılarını görüntülemek ve yönetmek için StorSimple Device Manager hizmetini kullanın

## <a name="overview"></a>Genel Bakış

StorSimple Device Manager hizmetindeki **Uyarılar** dikey penceresi, StorSimple cihazı ile ilgili uyarıları gerçek zamanlı olarak gözden geçirmeniz ve temizlemeniz için bir yol sağlar. Bu dikey pencerede, StorSimple cihazlarınızın sistem durumu sorunlarını ve genel Microsoft Azure StorSimple çözümünü merkezi olarak izleyebilirsiniz.

Bu öğreticide, yaygın uyarı koşulları, uyarı önem düzeyleri ve uyarı bildirimlerinin nasıl yapılandırılacağı açıklanmaktadır. Ayrıca, belirli bir uyarıyı hızlıca bulmanıza ve uygun şekilde yanıt vermenize olanak sağlayan uyarı hızlı başvuru tabloları da dahildir.

![Uyarılar sayfası](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Ortak uyarı koşulları

StorSimple cihazınız, çeşitli koşullara yanıt olarak uyarılar oluşturur. En yaygın uyarı koşulları türleri şunlardır:

* **Donanım sorunları** : Bu uyarılar, donanımınızın sistem durumunu bildirir. Bellenim yükseltmelerinde, bir ağ arabiriminde sorunlar varsa veya veri sürücülerinizden biriyle ilgili bir sorun olup olmadığını bilmeniz gerekir.
* **Bağlantı sorunları** : Bu uyarılar, verileri aktarma konusunda zorluk oluşması durumunda oluşur. Azure depolama hesabına veri aktarımı sırasında veya cihazlar ile StorSimple Device Manager hizmeti arasında bağlantı olmaması nedeniyle iletişim sorunları oluşabilir. Çok sayıda hata noktası olduğundan, iletişim sorunları düzeltilemeyecek bir kısmı. Daha gelişmiş sorun giderme işlemlerine devam etmeden önce ağ bağlantısı ve Internet erişiminin kullanılabilir olduğunu her zaman ilk önce doğrulamanız gerekir. Sorun giderme konusunda yardım için [Test-Connection cmdlet Ile sorun giderme](storsimple-8000-troubleshoot-deployment.md)bölümüne gidin.
* **Performans sorunları** : Bu uyarılar, sisteminiz ağır bir yük altında olduğunda olduğu gibi en iyi şekilde gerçekleştirimediğinde oluşur.

Ayrıca, güvenlik, güncelleştirmeler veya iş hatalarıyla ilgili uyarıları görebilirsiniz.

## <a name="alert-severity-levels"></a>Uyarı önem düzeyleri

Uyarılar, uyarı durumunun sahip olacağı etkiye ve uyarıya yanıt gereksinimine bağlı olarak farklı önem düzeylerine sahiptir. Önem dereceleri şunlardır:

* **Kritik** – bu uyarı, sisteminizin başarılı performansını etkileyen bir koşula yanıt olarak yapılır. StorSimple hizmetinin kesilmediğinden emin olmak için eylem gerekir.
* **Uyarı** – bu durum çözümlenmezse kritik hale gelebilir. Durumu araştırmalı ve sorunu temizlemek için gereken tüm işlemleri gerçekleştirmeniz gerekir.
* **Bilgi** – bu uyarı, sisteminizi izlemek ve yönetmek için yararlı olabilecek bilgiler içerir.

## <a name="configure-alert-settings"></a>Uyarı ayarlarını yapılandırma

StorSimple cihazlarınızın her biri için uyarı koşullarının e-postayla bildirilmesini isteyip istemediğinizi seçebilirsiniz. Buna ek olarak, diğer **e-posta alıcıları** kutusuna e-posta adreslerini girerek diğer uyarı bildirimi alıcılarını noktalı virgülle ayırarak tanımlayabilirsiniz.

> [!NOTE]
> Cihaz başına en fazla 20 e-posta adresi girebilirsiniz.

Bir cihaz için e-posta bildirimini etkinleştirdikten sonra, bildirim listesinin üyeleri kritik bir uyarı oluştuğunda bir e-posta iletisi alır. Mesajlar *StorSimple-Alerts-noreply \@ mail.windowsazure.com* 'dan gönderilir ve uyarı koşulunu tanımlarlar. Alıcılar e-posta bildirim listesinden kaldırmak için **abonelik kaldırma** ' ya tıklayabilir.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Bir cihaz için uyarıların e-posta bildirimini etkinleştirmek için
1. StorSimple Cihaz Yöneticisi hizmetinize gidin. Cihazların listesinden, yapılandırmak istediğiniz cihazı seçin ve tıklatın.
2. Cihaz için **Ayarlar**  >  **genel** ' e gidin.

   ![Uyarılar dikey penceresi](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. **Genel ayarlar** dikey penceresinde, **Uyarı ayarları** ' na gidin ve şunları ayarlayın:
   
   1. **E-posta bildirimi gönder** alanında **Evet**' i seçin.
   2. **E-posta hizmeti yöneticileri** alanında, hizmet yöneticisinin ve tüm ortak yöneticilerin uyarı bildirimlerini almasını sağlamak için **Evet** ' i seçin.
   3. **Diğer e-posta alıcıları** alanına, uyarı bildirimlerini alması gereken diğer tüm alıcıların e-posta adreslerini girin. Adları *birisi \@ Somewhere.com* biçiminde girin. E-posta adreslerini ayırmak için noktalı virgül kullanın. Cihaz başına en fazla 20 e-posta adresi yapılandırabilirsiniz. 
      
3. Sınama e-postası bildirimi göndermek için **Test e-postası gönder ' e** tıklayın. StorSimple Device Manager hizmeti, test bildirimini ileten durum iletilerini görüntüler.

    ![Uyarı ayarları](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Sınama e-postası gönderildiğinde bir bildirim görürsünüz. 
   
    ![Uyarı sınama bildirimi e-postası gönderildi](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Test bildirim iletisi gönderilemezse, StorSimple Device Manager hizmetinde uygun bir hata iletisi görüntülenir. Birkaç dakika bekleyin ve ardından test bildirim iletinizi yeniden göndermeye çalışın. 

5. Yapılandırmayı tamamladıktan sonra **Kaydet**' e tıklayın. Onayınız istendiğinde **Evet**’e tıklayın.

     ![Uyarı sınama bildirimi e-postası gönderildi 2](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Uyarıları görüntüleme ve izleme

StorSimple Device Manager hizmeti Özet dikey penceresi, cihazlarınızda önem düzeyine göre düzenlenmiş, cihazlarınızdaki uyarı sayısına hızlı bir bakış sağlar.

![Uyarılar panosu](./media/storsimple-8000-manage-alerts/device-summary4.png)

Önem düzeyine tıkladığınızda **Uyarılar** dikey penceresi açılır. Sonuçlar yalnızca bu önem düzeyiyle eşleşen uyarıları içerir.

Listedeki bir uyarıya tıkladığınızda, uyarının en son ne zaman bildirildiği, cihazdaki uyarının yinelenme sayısı ve uyarıyı çözümlemek için önerilen eylem dahil olmak üzere uyarı için ek ayrıntılar sağlanmaktadır. Bu bir donanım uyarısıdır, donanım bileşenini de belirler.

![Donanım uyarısı örneği](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Bilgileri Microsoft Desteği göndermeniz gerekiyorsa, uyarı ayrıntılarını bir metin dosyasına kopyalayabilirsiniz. Öneriyi uyguladıktan ve Şirket içindeki uyarı koşulunu çözümledikten sonra, **Uyarılar** dikey penceresinde uyarıyı seçip **Temizle**' ye tıklayarak cihazdan uyarıyı temizlemeniz gerekir. Birden çok uyarıyı temizlemek için, her bir uyarıyı seçin, **Uyarı** sütunu dışında herhangi bir sütuna tıklayın ve sonra temizlenecek tüm uyarıları seçtikten sonra **Temizle** ' ye tıklayın. Sorun çözüldüğünde veya sistem uyarıyı yeni bilgilerle güncelleştirdiğinde bazı uyarıların otomatik olarak temizlendiğini unutmayın.

**Temizle**' ye tıkladığınızda, uyarı ve sorunu çözmek için gerçekleştirdiğiniz adımlar hakkında açıklama sağlama fırsatına sahip olursunuz. Yeni bilgilerle başka bir olay tetikleniyorsa, bazı olaylar sistem tarafından temizlenir. Bu durumda, aşağıdaki iletiyi görürsünüz.

![Uyarı iletisini temizle](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Uyarıları sıralama ve gözden geçirme

Raporları raporlarda çalıştırmayı daha verimli bulabilirsiniz, böylece bunları gruplar halinde gözden geçirebilir ve temizleyebilirsiniz. Ayrıca, **Uyarılar** dikey penceresinde en fazla 250 uyarı görüntülenebilir. Bu uyarı sayısını aştıysanız, varsayılan görünümde tüm uyarılar gösterilmez. Hangi uyarıların görüntülendiğini özelleştirmek için aşağıdaki alanları birleştirebilirsiniz:

* **Durum** – **etkin** ya da **temizlenmiş** uyarıları görüntüleyebilirsiniz. Etkin uyarıların hala sisteminizde tetiklenmesi, temizlenmiş uyarıların bir yönetici tarafından el ile temizlenmesi veya sistem uyarı koşulunu yeni bilgilerle güncelleştirmesi nedeniyle program aracılığıyla temizlenmiş olması gerekir.
* **Önem derecesi** – tüm önem düzeyleri (kritik, uyarı, bilgi) veya yalnızca kritik uyarılar gibi belirli bir önem derecesindeki uyarıları görüntüleyebilirsiniz.
* **Kaynak** : tüm kaynaklardaki uyarıları görüntüleyebilir veya uyarıları hizmetten ya da tüm cihazlardan gelen olanlarla sınırlayabilirsiniz.
* **Zaman aralığı** : **Kimden** ve **bitiş** tarihleri ve zaman damgaları belirterek, ilgilendiğiniz zaman dilimi sırasında uyarılara bakabilirsiniz.

![Uyarı listesi](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Uyarılar hızlı başvuru

Aşağıdaki tablolarda karşılaşabileceğiniz Microsoft Azure StorSimple uyarılarından bazıları ve kullanılabilir olduğunda ek bilgi ve öneriler listelenmektedir. StorSimple cihaz uyarıları aşağıdaki kategorilerden birine girer:

* [Bulut bağlantısı uyarıları](#cloud-connectivity-alerts)
* [Küme uyarıları](#cluster-alerts)
* [Olağanüstü durum kurtarma uyarıları](#disaster-recovery-alerts)
* [Donanım uyarıları](#hardware-alerts)
* [İş hatası uyarıları](#job-failure-alerts)
* [Yerel olarak sabitlenmiş birim uyarıları](#locally-pinned-volume-alerts)
* [Ağ uyarıları](#networking-alerts)
* [Performans uyarıları](#performance-alerts)
* [Güvenlik uyarıları](#security-alerts)
* [Destek paketi uyarıları](#support-package-alerts)
* [Kasa ortamı uyarıları](#enclosure-environment-alerts)

### <a name="cloud-connectivity-alerts"></a>Bulut bağlantısı uyarıları

| Uyarı metni | Olay | Daha fazla bilgi/Önerilen Eylemler |
|:--- |:--- |:--- |
| > <*bulut kimlik bilgisi adı* bağlantısı oluşturulamıyor. |Depolama hesabına bağlanılamıyor. |Aygıtınızla ilgili bir bağlantı sorunu olabilir. `Test-HcsmConnection`Sorunu tanımlamak ve onarmak için lütfen cihazınızdaki StorSimple Için Windows PowerShell arabiriminden cmdlet 'i çalıştırın. Ayarlar doğruysa, sorun, uyarının oluşturulduğu depolama hesabının kimlik bilgileri ile olabilir. Bu durumda, `Test-HcsStorageAccountCredential` çözebilmeniz gereken sorunlar olup olmadığını öğrenmek için cmdlet 'ini kullanın.<ul><li>Ağ ayarlarınızı denetleyin.</li><li>Depolama hesabınızın kimlik bilgilerini denetleyin.</li></ul> |
| Son <*sayı*> dakika boyunca cihazınızdan sinyal almadık. |Cihaza bağlanılamıyor. |Cihazınızda bir bağlantı sorunu var gibi görünüyor. `Test-HcsmConnection`Sorunu belirleyip onarmak için lütfen cihazınızdaki StorSimple Için Windows PowerShell arabiriminden cmdlet 'i kullanın veya ağ yöneticinize başvurun. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>Bulut bağlantısı başarısız olduğunda StorSimple davranışı

Üretimde çalışan StorSimple cihazım için bulut bağlantısı başarısız olursa ne olur?

StorSimple üretim cihazınızda bulut bağlantısı başarısız olursa cihazınızın durumuna bağlı olarak aşağıdakiler oluşabilir:

* **Cihazınızdaki yerel veriler için**: bir süre boyunca kesinti olmayacaktır ve okuma sunulmak üzere devam edecektir. Ancak, bekleyen IOs sayısı arttıkça ve bir sınırı aşarsa, okumalar başarısız olacak şekilde başlayabilir.

    Cihazınızdaki veri miktarına bağlı olarak, bulut bağlantısında kesintiye uğradıktan sonraki ilk birkaç saat için yazma işlemleri de devam edecektir. Daha sonra yazma işlemleri yavaşlar ve bu süre sonunda bulut bağlantısı birkaç saat boyunca kesintiye uğradığında işlem başarısız olur. (Cihazda buluta gönderilecek veriler için geçici depolama alanı vardır. Bu alan, veriler gönderildiğinde temizlenir. Bağlantı başarısız olursa, bu depolama alanındaki veriler buluta itilecektir ve GÇ başarısız olur.)
* **Buluttaki veriler için**: çoğu bulut bağlantı hatası için bir hata döndürülür. Bağlantı geri yüklendikten sonra, Kullanıcı birimi çevrimiçi duruma getirmek zorunda kalmadan IOs sürdürülür. Nadir örneklerde, birimi Azure portal çevrimiçi olarak geri getirmek için Kullanıcı müdahalesi gerekli olabilir.
* **Devam eden bulut anlık görüntüleri**: işlem 4-5 saat içinde birkaç kez yeniden denenir ve bağlantı geri yüklenemediğinde, bulut anlık görüntüleri başarısız olur.

### <a name="cluster-alerts"></a>Küme uyarıları

| Uyarı metni | Olay | Daha fazla bilgi/Önerilen Eylemler |
|:--- |:--- |:--- |
| Cihaz,> *Cihaz adı* <yük devretdi. |Cihaz bakım modunda. |Bakım modundan girme veya çıkış nedeniyle cihaz yük devretmede başarısız oldu. Bu normaldir ve hiçbir eylemde bulunmanız gerekmez. Bu uyarıyı kabul ettikten sonra uyarılar sayfasından temizleyin. |
| Cihaz,> *Cihaz adı* <yük devretdi. |Cihaz üretici yazılımı veya yazılımı az önce güncelleştirildi. |Bir güncelleştirme nedeniyle küme yük devretmesi vardı. Bu normaldir ve hiçbir eylemde bulunmanız gerekmez. Bu uyarıyı kabul ettikten sonra uyarılar sayfasından temizleyin. |
| Cihaz,> *Cihaz adı* <yük devretdi. |Denetleyici kapatıldı veya yeniden başlatıldı. |Etkin denetleyici bir yönetici tarafından kapatıldığı veya yeniden başlatıldığından cihaz yük devretmedi. Eylem gerekmiyor. Bu uyarıyı kabul ettikten sonra uyarılar sayfasından temizleyin. |
| Cihaz,> *Cihaz adı* <yük devretdi. |Planlı Yük devretme. |Bunun planlanmış bir yük devretme olduğunu doğrulayın. Uygun eylemi gerçekleştirdikten sonra uyarılar sayfasından bu uyarıyı temizleyin. |
| Cihaz,> *Cihaz adı* <yük devretdi. |Planlanmamış yük devretme. |StorSimple, planlanmamış yük devretmeler üzerinden otomatik olarak kurtarmak için oluşturulmuştur. Bu uyarılardan çok sayıda görürseniz, Microsoft Desteği başvurun. |
| Cihaz,> *Cihaz adı* <yük devretdi. |Diğer/bilinmeyen neden. |Bu uyarılardan çok sayıda görürseniz, Microsoft Desteği başvurun. Sorun çözümlendikten sonra uyarılar sayfasından bu uyarıyı temizleyin. |
| Kritik bir cihaz hizmeti durumu başarısız olarak bildiriyor. |DataPath hizmet hatası. |Yardım için Microsoft Desteği başvurun. |
| Ağ arabirimi için sanal IP adresi <*Data #*>, durumu başarısız olarak bildiriyor. |Diğer/bilinmeyen neden. |Bazen geçici koşullar Bu uyarıların oluşmasına neden olabilir. Bu durumda, bu uyarı bir süre sonra otomatik olarak temizlenir. Sorun devam ederse, Microsoft Desteği başvurun. |
| Ağ arabirimi için sanal IP adresi <*Data #*>, durumu başarısız olarak bildiriyor. |Arabirim adı: ağ üzerinde yinelenen bir IP adresi algılandığından <*Data #*> IP adresi `<IP address>` çevrimiçi duruma getirilemiyor. |Yinelenen IP adresinin ağdan kaldırıldığından emin olun veya arabirimi farklı bir IP adresiyle yeniden yapılandırın. |

### <a name="disaster-recovery-alerts"></a>Olağanüstü durum kurtarma uyarıları

| Uyarı metni | Olay | Daha fazla bilgi/Önerilen Eylemler |
|:--- |:--- |:--- |
| Kurtarma işlemleri bu hizmetin tüm ayarlarını geri yükleyemedi. Cihaz yapılandırma verileri bazı cihazlar için tutarsız bir durumda. |Olağanüstü durum kurtarma sonrasında veri tutarsızlığı algılandı. |Hizmette şifrelenen veriler, cihazdaki ile eşitlenmez. <cihaz *adı*> storsimple Device Manager, eşitleme işlemini başlatacak şekilde yetkilendirin. `Restore-HcsmEncryptedServiceData`Güvenlik profilini geri yüklemek için bu cmdlet 'e giriş olarak eski parolayı sağlamak üzere StorSimple Için Windows PowerShell arabirimini kullanın <*cihaz adı*> cmdlet 'ini çalıştırın. Ardından, `Invoke-HcsmServiceDataEncryptionKeyChange` hizmet veri şifreleme anahtarını güncelleştirmek için cmdlet 'ini çalıştırın. Uygun eylemi gerçekleştirdikten sonra uyarılar sayfasından bu uyarıyı temizleyin. |

### <a name="hardware-alerts"></a>Donanım uyarıları

| Uyarı metni | Olay | Daha fazla bilgi/Önerilen Eylemler |
|:--- |:--- |:--- |
| <*BILEŞEN kimliği*>, <*durum*> olarak durumu raporlar. | |Bazen geçici koşullar Bu uyarıların oluşmasına neden olabilir. Bu durumda, bu uyarı bir süre sonra otomatik olarak temizlenir. Sorun devam ederse, Microsoft Desteği başvurun. |
| Pasif denetleyici hatalı çalışıyor. |Pasif (ikincil) denetleyici çalışmıyor. |Cihazınız çalışıyor, ancak Denetleyicilerinizden biri arızalı. Bu denetleyiciyi yeniden başlatmayı deneyin. Sorun çözümlenmezse Microsoft Desteği başvurun. |

### <a name="job-failure-alerts"></a>İş hatası uyarıları

| Uyarı metni | Olay | Daha fazla bilgi/Önerilen Eylemler |
|:--- |:--- |:--- |
| <*kaynak birim grubu kimliği*> yedeklemesi başarısız oldu. |Yedekleme işi başarısız oldu. |Bağlantı sorunları yedekleme işleminin başarıyla tamamlanmasını engelliyor olabilir. Herhangi bir bağlantı sorunu yoksa, en fazla yedekleme sayısına ulaşmış olabilirsiniz. Artık gerekmeyen yedekleri silin ve işlemi yeniden deneyin. Uygun eylemi gerçekleştirdikten sonra uyarılar sayfasından bu uyarıyı temizleyin. |
| <*kaynak yedekleme öğesi kimliklerinin* <*hedef birim seri numaralarına*> kopyası başarısız>. |Kopyalama işi başarısız oldu. |Yedeklemenin hala geçerli olduğunu doğrulamak için yedekleme listesini yenileyin. Yedekleme geçerliyse, bulut bağlantısı sorunları kopyalama işleminin başarıyla tamamlanmasını engelliyor olabilir. Herhangi bir bağlantı sorunu yoksa, depolama sınırına ulaşmış olabilirsiniz. Artık gerekmeyen yedekleri silin ve işlemi yeniden deneyin. Sorunu çözmek için uygun eylemi gerçekleştirdikten sonra uyarılar sayfasından bu uyarıyı temizleyin. |
| <*kaynak yedekleme öğesi kimliklerinin*> geri yüklenmesi başarısız oldu. |Geri yükleme işi başarısız oldu. |Yedeklemenin hala geçerli olduğunu doğrulamak için yedekleme listesini yenileyin. Yedekleme geçerliyse, bulut bağlantısı sorunları geri yükleme işleminin başarıyla tamamlanmasını engelliyor olabilir. Herhangi bir bağlantı sorunu yoksa, depolama sınırına ulaşmış olabilirsiniz. Artık gerekmeyen yedekleri silin ve işlemi yeniden deneyin. Sorunu çözmek için uygun eylemi gerçekleştirdikten sonra uyarılar sayfasından bu uyarıyı temizleyin. |

### <a name="locally-pinned-volume-alerts"></a>Yerel olarak sabitlenmiş birim uyarıları

| Uyarı metni | Olay | Daha fazla bilgi/Önerilen Eylemler |
|:--- |:--- |:--- |
| <*birim adı*> yerel birimi oluşturulamadı. |Birim oluşturma işi başarısız oldu. <Hatalı *hata koduna karşılık gelen hata iletisi*>. |Bağlantı sorunları, alan oluşturma işleminin başarıyla tamamlanmasını engelliyor olabilir. Yerel olarak sabitlenmiş birimler bol alanla sağlandı ve alan oluşturma işlemi, katmanlı birimlerin buluta atılanmasını içerir. Herhangi bir bağlantı sorunu yoksa, cihazdaki yerel alanı tükenmiş olabilirsiniz. Bu işlemi yeniden denemeden önce cihazda boş alan olup olmadığını saptayın. |
| <*birim adının*> yerel birim genişletmesi başarısız oldu. |Birim değiştirme işi, *başarısız hata kodu> karşılık gelen <hata iletisi* nedeniyle başarısız oldu. |Bağlantı sorunları, birim genişletme işleminin başarıyla tamamlanmasını engelliyor olabilir. Yerel olarak sabitlenmiş birimler bol alanla sağlandı ve mevcut alanı genişletme işlemi, katmanlı birimlerin buluta atılanmasını içerir. Herhangi bir bağlantı sorunu yoksa, cihazdaki yerel alanı tükenmiş olabilirsiniz. Bu işlemi yeniden denemeden önce cihazda boş alan olup olmadığını saptayın. |
| Birim *adı*> birimi <dönüştürülemedi. |Birim türünü yerel olarak sabitlenmiş olarak katmanlı olarak dönüştürmek için birim dönüştürme işi başarısız oldu. |Yerel olarak iliştirilmiş türden birimin katmanlı olarak dönüştürülmesi tamamlanamadı. İşlemin başarıyla tamamlanmasını engelleyen bir bağlantı sorunu olmadığından emin olun. Bağlantı sorunlarını gidermek için [Test-HcsmConnection cmdlet Ile sorun giderme](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet)bölümüne gidin.<br>Yerel olarak sabitlenmiş birimdeki verilerden bazıları dönüştürme sırasında buluta taşdığından, özgün yerel olarak sabitlenmiş birim artık katmanlı bir birim olarak işaretlendi. Elde edilen katmanlı birim, gelecekteki yerel birimlerde geri kazanılanamaz, cihazdaki yerel alanı hala işgal etmektedir.<br>Tüm bağlantı sorunlarını çözün, uyarıyı temizleyin ve tüm verilerin yerel olarak yeniden kullanılabilir hale getirildiğinden emin olmak için bu birimi özgün yerel olarak sabitlenmiş birim türüne dönüştürün. |
| Birim *adı*> birimi <dönüştürülemedi. |Birim türünü katmanlıdan yerel olarak sabitlenmiş olarak dönüştürmek için birim dönüştürme işi başarısız oldu. |Birim türü katmanlı olan birimi yerel olarak sabitlenmiş olarak dönüştürme işlemi tamamlanamadı. İşlemin başarıyla tamamlanmasını engelleyen bir bağlantı sorunu olmadığından emin olun. Bağlantı sorunlarını gidermek için [Test-HcsmConnection cmdlet Ile sorun giderme](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet)bölümüne gidin.<br>Artık dönüştürme işleminin bir parçası olarak yerel olarak sabitlenmiş bir birim olarak işaretlenen orijinal katmanlı birim, bulutta yer alan verilere sahip olmaya devam ederken, bu birim için cihazdaki bol alanla sağlanan alan artık gelecekteki yerel birimlerde geri kazanılamıyor.<br>Tüm bağlantı sorunlarını çözün, uyarıyı temizleyin ve cihazdaki yerel alan bol alanla sağlanması için bu birimi özgün katmanlı birim türüne dönüştürün. |
| <*birim grubu adının* yerel anlık görüntüleri için yerel alan tüketimine yaklaşma> |Yedekleme ilkesi için yerel anlık görüntüler yakında boş alan tükeniyor ve ana bilgisayar yazma hatalarından kaçınmak için geçersiz kılınabilir. |Bu yedekleme ilkesi grubuyla ilişkili birimlerde yüksek veri dalgalanmasına neden olan sık kullanılan yerel anlık görüntüler, cihazdaki yerel alanın hızla tüketilmesine neden olur. Artık gerekli olmayan tüm yerel anlık görüntüleri silin. Ayrıca, bu yedekleme ilkesi için yerel anlık görüntü zamanlamalarınızı, daha az sıklıkta yerel anlık görüntüler olacak şekilde güncelleştirin ve bulut anlık görüntülerinin düzenli olarak alındığından emin olun. Bu eylemler alınmadığında, bu anlık görüntüler için yerel alan yakında tükenebilir ve sistem, ana bilgisayar yazmaları 'nın başarıyla işlenmeye devam etmesini sağlamak için otomatik olarak silinir. |
| <*birim grubu adı*> yerel anlık görüntüleri geçersiz kılındı. |<*birim grubu adı*> yerel anlık görüntüleri geçersiz kılındı ve cihazdaki yerel alanı aştıkları için silindi. |Bunun gelecekte tekrar olmamasını sağlamak için, bu yedekleme ilkesi için yerel anlık görüntü zamanlamalarını gözden geçirin ve artık gerekli olmayan tüm yerel anlık görüntüleri silin. Bu yedekleme ilkesi grubuyla ilişkili birimlerde yüksek veri dalgalanmasına neden olan sık kullanılan yerel anlık görüntüler, cihazdaki yerel alanın hızla tüketilmesine neden olabilir. |
| <*kaynak yedekleme öğesi kimliklerinin*> geri yüklenmesi başarısız oldu. |Geri yükleme işi başarısız oldu. |Bu yedekleme ilkesinde yerel olarak sabitlenmiş ve katmanlı birimler karışımı varsa, yedeklemenin hala geçerli olduğunu doğrulamak için yedekleme listesini yenileyin. Yedekleme geçerliyse, bulut bağlantısı sorunları geri yükleme işleminin başarıyla tamamlanmasını engelliyor olabilir. Bu anlık görüntü grubunun bir parçası olarak geri yüklenen yerel olarak sabitlenmiş birimlerde, tüm verileri cihaza indirilmez ve bu anlık görüntü grubunda katmanlı ve yerel olarak sabitlenmiş birimlerin bir karışımı varsa, birbirleriyle eşitlenmiş olmayacaktır. Geri yükleme işlemini başarıyla tamamlayabilmesi için konakta bu gruptaki birimleri çevrimdışına alın ve geri yükleme işlemini yeniden deneyin. Geri yükleme işlemi sırasında gerçekleştirilen birim verilerinde yapılan tüm değişikliklerin kaybolacağını unutmayın. |

### <a name="networking-alerts"></a>Ağ uyarıları

| Uyarı metni | Olay | Daha fazla bilgi/Önerilen Eylemler |
|:--- |:--- |:--- |
| StorSimple Hizmetleri başlatılamadı. |Veri yolu hatası |Sorun devam ederse Microsoft Desteği başvurun. |
| ' Data0 ' için yinelenen IP adresi algılandı. | |Sistem, ' 10.0.0.1 ' IP adresi için bir çakışma algıladı. Cihazdaki ' data0 ' ağ kaynağı *\<device1>* çevrimdışı. Bu IP adresinin bu ağdaki başka bir varlık tarafından kullanılmadığından emin olun. Ağ sorunlarını gidermek için [Get-NetAdapter cmdlet Ile sorun giderme](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet)bölümüne gidin. Bu sorunu çözmeye yardımcı olması için ağ yöneticinize başvurun. Sorun devam ederse Microsoft Desteği başvurun. |
| ' Data0 ' için IPv4 (veya IPv6) adresi çevrimdışı. | |' 10.0.0.1 ' IP adresine sahip ' data0 ' ağ kaynağı ve cihazdaki önek uzunluğu ' 22 ' *\<device1>* çevrimdışı. Bu arabirimin bağlı olduğu anahtar bağlantı noktalarının çalışır durumda olduğundan emin olun. Ağ sorunlarını gidermek için [Get-NetAdapter cmdlet Ile sorun giderme](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet)bölümüne gidin. |
| Kimlik doğrulama hizmetine bağlanılamadı. |Veri yolu hatası |Kimlik doğrulaması için kullanılan URL 'ye ulaşılamıyor. Güvenlik Duvarı kurallarınızın StorSimple cihazı için belirtilen URL düzenlerini içerdiğinden emin olun. Azure portal URL desenleri hakkında daha fazla bilgi için https: \/ /aka.MS/SS-8000-Network-reqs adresine gidin. Azure Kamu Bulutu kullanıyorsanız, https:/aka.ms/ss8000-gov-network-reqs içindeki URL düzenlerine gidin \/ .|

### <a name="performance-alerts"></a>Performans uyarıları

| Uyarı metni | Olay | Daha fazla bilgi/Önerilen Eylemler |
|:--- |:--- |:--- |
| Cihaz yükü <*eşiği*> aştı. |Beklenen yanıt süreleriyle daha yavaş. |Cihazınız ağır bir giriş/çıkış yükü altında kullanım bildiriyor. Bu, cihazınızın çalışmasına ve aynı zamanda çalışmamasına neden olabilir. Cihaza eklediğiniz iş yüklerini gözden geçirin ve başka bir cihaza taşınabilecek veya artık gerekli olmayan bir cihaz olup olmadığını saptayın.|
| StorSimple Hizmetleri başlatılamadı. |Veri yolu hatası |Sorun devam ederse Microsoft Desteği başvurun. |

### <a name="security-alerts"></a>Güvenlik uyarıları

| Uyarı metni | Olay | Daha fazla bilgi/Önerilen Eylemler |
|:--- |:--- |:--- |
| Microsoft Desteği oturum başladı. |Üçüncü tarafa erişilen destek oturumu. |Lütfen bu erişimin yetkilendirilmiş olduğunu onaylayın. Uygun eylemi gerçekleştirdikten sonra uyarılar sayfasından bu uyarıyı temizleyin. |
| <*öğesi*> parolasının süresi <> *süre* içinde dolacak. |Parola süre sonu yaklaşılıyor. |Parolanızı süresi dolmadan önce değiştirin. |
| <*Öğesı kimliği*> için güvenlik yapılandırma bilgileri eksik. | |Bu birim kapsayıcısı ile ilişkili birimler, StorSimple yapılandırmanızı çoğaltmak için kullanılamaz. Verilerinizin güvenle depolandığından emin olmak için birim kapsayıcısını ve birim kapsayıcısıyla ilişkili tüm birimleri silmenizi öneririz. Uygun eylemi gerçekleştirdikten sonra uyarılar sayfasından bu uyarıyı temizleyin. |
| <> <*Öğesı kimliği*> Için oturum açma girişimi *sayısı* başarısız oldu. |Birden çok başarısız oturum açma denemesi. |Cihazınız saldırıya açık veya yetkili bir Kullanıcı yanlış bir parolayla bağlanmaya çalışıyor olabilir.<ul><li>Yetkili kullanıcılarınıza başvurun ve bu denemelerin meşru bir kaynaktan olduğunu doğrulayın. Çok sayıda başarısız oturum açma denemesini görmeye devam ederseniz, uzaktan yönetimi devre dışı bırakmayı ve ağ yöneticinize başvurmayı düşünün. Uygun eylemi gerçekleştirdikten sonra uyarılar sayfasından bu uyarıyı temizleyin.</li><li>Snapshot Manager örneklerinizin doğru parolayla yapılandırılıp yapılandırılmadığını denetleyin. Uygun eylemi gerçekleştirdikten sonra uyarılar sayfasından bu uyarıyı temizleyin.</li></ul>Daha fazla bilgi için, [zaman aşımına uğradı bir cihaz parolasını değiştirme](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password)bölümüne gidin. |
| Hizmet veri şifreleme anahtarı değiştirilirken bir veya daha fazla başarısızlık oluştu. | |Hizmet veri şifreleme anahtarı değiştirilirken hatalarla karşılaşıldı. Hata koşullarını doğruladıktan sonra, `Invoke-HcsmServiceDataEncryptionKeyChange` hizmeti güncelleştirmek için cihazınızda StorSimple Için Windows PowerShell arabiriminden cmdlet 'ini çalıştırın. Bu sorun devam ederse Microsoft desteği 'ne başvurun. Sorunu çözdükten sonra, uyarılar sayfasından bu uyarıyı temizleyin. |

### <a name="support-package-alerts"></a>Destek paketi uyarıları

| Uyarı metni | Olay | Daha fazla bilgi/Önerilen Eylemler |
|:--- |:--- |:--- |
| Destek paketi oluşturulamadı. |StorSimple, paketi oluşturamadı. |Bu işlemi yeniden deneyin. Sorun devam ederse, Microsoft Desteği başvurun. Sorunu çözdükten sonra, uyarılar sayfasından bu uyarıyı temizleyin. |

### <a name="enclosure-environment-alerts"></a>Kasa ortamı uyarıları

| Uyarı metni | Olay | Daha fazla bilgi/Önerilen Eylemler |
|:--- |:--- |:--- |
| Donanım bileşeni çevresel sıcaklık algılayıcısı durumu başarısız olarak bildiriyor.  | Kutu türü: Ana kutu | Bu uyarı, StorSimple etrafındaki ortam, kabul edilebilir bir aralığın üzerinde olduğunda tetiklenir. Veri merkezindeki AC çıkış akışından alınan sıcaklığın veya Airflow 'un çevresel akışını kontrol edin. Sıcaklık normal olarak döndüğünde, bir süre geçtikten sonra uyarı otomatik olarak temizlenir. Sorun devam ederse, Microsoft desteğine başvurun.   |

## <a name="next-steps"></a>Sonraki adımlar

[StorSimple hataları ve cihaz dağıtımı sorunlarını giderme](storsimple-8000-troubleshoot-deployment.md)hakkında daha fazla bilgi edinin.
