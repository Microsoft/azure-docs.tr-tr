---
title: StorSimple 8000 serisi cihaz denetleyicilerini yönetme | Microsoft Docs
description: StorSimple cihaz denetleyicilerinizi durdurmayı, yeniden başlatmayı, kapatmayı veya sıfırlamayı öğrenin.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 090797549cc61aa27945114e5ef8b666226b66e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94957002"
---
# <a name="manage-your-storsimple-device-controllers"></a>StorSimple cihaz denetleyicilerinizi yönetin

## <a name="overview"></a>Genel Bakış

Bu öğreticide, StorSimple cihaz denetleyicilerinizde gerçekleştirilebilecek farklı işlemler açıklanmaktadır. StorSimple cihazınızdaki denetleyiciler, etkin-Pasif bir yapılandırmada gereksiz (eş) denetleyicileridir. Belirli bir zamanda yalnızca bir denetleyici etkindir ve tüm disk ve ağ işlemlerini işliyor. Diğer denetleyici pasif bir modda. Etkin denetleyici başarısız olursa, pasif denetleyici otomatik olarak etkin hale gelir.

Bu öğreticide, cihaz denetleyicilerini yönetmek için adım adım yönergeler yer almaktadır:

* StorSimple Device Manager hizmetinde cihazınızın **denetleyiciler** dikey penceresi.
* StorSimple için Windows PowerShell.

StorSimple Device Manager hizmeti aracılığıyla cihaz denetleyicilerini yönetmenizi öneririz. Bir eylem yalnızca StorSimple için Windows PowerShell kullanılarak gerçekleştirilemiyorsa, öğretici bunu bir yere getirir.

Bu öğreticiyi okuduktan sonra şunları yapabilirsiniz:

* StorSimple cihaz denetleyicisini yeniden başlatma veya kapatma
* StorSimple cihazını kapatma
* StorSimple cihazınızı fabrika varsayılanlarına sıfırlayın

## <a name="restart-or-shut-down-a-single-controller"></a>Tek bir denetleyiciyi yeniden başlatma veya kapatma
Normal sistem işleminin bir parçası olarak denetleyicinin yeniden başlatılması veya kapatılmasını gerekli değildir. Tek bir cihaz denetleyicisi için kapalı işlemler yalnızca, başarısız bir cihaz donanım bileşeninin değişiklik gerektirdiği durumlarda ortaktır. Bir denetleyici yeniden başlatması, performansının aşırı bellek kullanımından veya hatalı çalışan bir denetleyiciden etkilediği bir durumda da gerekebilir. Ayrıca, değiştirilen denetleyiciyi etkinleştirmek ve test etmek istiyorsanız, başarılı bir denetleyici değişikliği sonrasında bir denetleyiciyi yeniden başlatmanız gerekebilir.

Pasif denetleyicinin kullanılabilir olduğu varsayılırsa, cihazın yeniden başlatılması bağlı başlatıcılarda kesintiye neden olmaz. Pasif denetleyici kullanılabilir değilse veya kapatılmışsa, etkin denetleyicinin yeniden başlatılması hizmet kesintisine ve kapalı kalma süresine yol açabilir.

> [!IMPORTANT]
> * **Çalışan bir denetleyici asla fiziksel olarak kaldırılmamalıdır, çünkü bu durum yedekliliğe ve kapalı kalma süresinin artmasına neden olur.**
> * Aşağıdaki yordam yalnızca StorSimple fiziksel cihazı için geçerlidir. StorSimple Cloud Appliance başlatma, durdurma ve yeniden başlatma hakkında daha fazla bilgi için bkz. [bulut gereci Ile çalışma](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance).

StorSimple Device Manager hizmeti veya StorSimple için Windows PowerShell Azure portal aracılığıyla tek bir cihaz denetleyicisini yeniden başlatabilir veya kapatabilirsiniz.

Cihaz denetleyicilerinizi Azure portal yönetmek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Azure portal bir denetleyiciyi yeniden başlatmak veya kapatmak için
1. StorSimple Device Manager hizmetinizde **cihazlar**' a gidin. Cihaz listesinden cihazınızı seçin. 

    ![Cihaz seçin](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. **Ayarlar > denetleyicileri**' ne gidin.
   
    ![StorSimple cihaz denetleyicilerinin sağlıklı olduğunu doğrulayın](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. **Denetleyiciler** dikey penceresinde, cihazlarınızın her iki denetleyicisinin durumunun **sağlıklı** olduğunu doğrulayın. Bir denetleyici seçin, sağ tıklayın ve ardından **Yeniden Başlat** veya **Kapat**' ı seçin.

    ![Yeniden Başlat veya StorSimple cihaz denetleyicilerini Kapat ' ı seçin](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Denetleyiciyi yeniden başlatmak veya kapatmak için bir iş oluşturulur ve varsa geçerli uyarılarla karşılaşırsınız. Yeniden başlatma veya kapatmaları izlemek için, **service > etkinlik günlükleri** ' ne gidin ve ardından hizmetinize özgü parametrelere göre filtreleyin. Bir denetleyici kapatıldıysa, denetleyiciyi açmak için güç düğmesini göndermeniz gerekir.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>StorSimple için Windows PowerShell bir denetleyiciyi yeniden başlatmak veya kapatmak için
StorSimple için Windows PowerShell StorSimple cihazınızda tek bir denetleyiciyi kapatmak veya yeniden başlatmak için aşağıdaki adımları gerçekleştirin.

1. Cihaza seri konsol veya uzak bir bilgisayardan bir Telnet oturumu aracılığıyla erişin. Controller 0 veya Controller 1 ' e bağlanmak için, [cihaz seri konsoluna bağlanmak Için PuTTY kullanma](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)bölümündeki adımları izleyin.
2. Seri konsol menüsünde, seçenek 1 ' i seçin, **tam erişim Ile oturum açın**.
3. Başlık iletisinde, bağlandığınız denetleyiciyi (denetleyici 0 veya denetleyici 1) ve etkin veya pasif (bekleme) denetleyicisi olduğunu bir yere getirin.
   
   * Tek bir denetleyiciyi kapatmak için, isteminde şunu yazın:
     
       `Stop-HcsController`
     
       Bu, bağlandığınız denetleyiciyi kapatır. Etkin denetleyiciyi durdurursanız, cihaz pasif denetleyiciye devreder.

   * Bir denetleyiciyi yeniden başlatmak için, isteminde şunu yazın:
     
       `Restart-HcsController`
     
       Bu işlem bağlandığınız denetleyiciyi yeniden başlatır. Etkin denetleyiciyi yeniden başlatırsanız, yeniden başlatmadan önce pasif denetleyiciye devreder.

## <a name="shut-down-a-storsimple-device"></a>StorSimple cihazını kapatma

Bu bölümde, çalışan veya başarısız bir StorSimple cihazını uzak bir bilgisayardan nasıl kapatılabilen açıklanmaktadır. Cihaz denetleyicileri kapatıldıktan sonra bir cihaz kapalıdır. Cihaz fiziksel olarak taşındığında veya hizmet dışına çıktığında bir cihaz kapatma işlemi yapılır.

> [!IMPORTANT]
> Cihazı kapatmadan önce cihaz bileşenlerinin sistem durumunu kontrol edin. Cihazınıza gidin ve ardından **ayarlar > donanım sistem durumu**' na tıklayın. **Durum ve donanım durumu** dikey penceresinde, tüm bileşenlerin LED durumunun yeşil olduğunu doğrulayın. Yalnızca sağlıklı bir cihazın yeşil durumu vardır. Cihazınız hatalı çalışan bir bileşenin yerini alacak şekilde kapatılmakta ise ilgili bileşenler için başarısız (kırmızı) veya düşürülmüş (sarı) bir durum görürsünüz.


#### <a name="to-shut-down-a-storsimple-device"></a>StorSimple cihazını kapatmak için

1. Cihazınızdaki pasif denetleyiciyi tanımlamak ve kapatmak için [yeniden başlatma veya bir denetleyiciyi kapatma](#restart-or-shut-down-a-single-controller) yordamını kullanın. Bu işlemi Azure portal veya StorSimple için Windows PowerShell ' de yapabilirsiniz.
2. Etkin denetleyiciyi kapatmak için yukarıdaki adımı yineleyin.
3. Artık cihazın arka düzlemine bakmanız gerekir. İki denetleyici tamamen kapatıldıktan sonra, denetleyicilerde her iki durumda da durum LED 'Leri yanıp sönen kırmızı olmalıdır. Şu anda cihazı tam olarak kapatmanız gerekiyorsa güç ve soğutma modüllerindeki (PCMs) güç anahtarlarını kapalı konuma çevirin. Bu, cihazı kapatmalıdır.

## <a name="reset-the-device-to-factory-default-settings"></a>Cihazı varsayılan fabrika ayarlarına sıfırlama

> [!IMPORTANT]
> Cihazınızı fabrika varsayılan ayarlarına sıfırlamanız gerekiyorsa Microsoft Desteği başvurun. Aşağıda açıklanan yordam yalnızca Microsoft Desteği ile birlikte kullanılmalıdır.

Bu yordamda, StorSimple için Windows PowerShell kullanılarak Microsoft Azure StorSimple cihazınızın fabrika varsayılan ayarlarına nasıl sıfırlanacağı açıklanır.
Bir cihazı sıfırlamak, tüm verileri ve ayarları varsayılan olarak tüm kümeden kaldırır.

Microsoft Azure StorSimple cihazınızı fabrika varsayılan ayarlarına sıfırlamak için aşağıdaki adımları gerçekleştirin:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Cihazı StorSimple için Windows PowerShell varsayılan ayarlarına sıfırlamak için
1. Cihaza seri konsol üzerinden erişin. **Etkin** denetleyiciye bağlı olduğunuzdan emin olmak için başlık iletisini kontrol edin.
2. Seri konsol menüsünde, seçenek 1 ' i seçin, **tam erişim Ile oturum açın**.
3. Komut isteminde, tüm verileri, meta verileri ve denetleyici ayarlarını kaldırarak kümeyi tümüyle sıfırlamak için aşağıdaki komutu yazın:
   
    `Reset-HcsFactoryDefault`
   
    Tek bir denetleyiciyi sıfırlamak için, parametresiyle  [Reset-HcsFactoryDefault](/previous-versions/windows/powershell-scripting/dn688132(v=wps.630)) cmdlet 'ini kullanın `-scope` .)
   
    Sistem birden çok kez yeniden başlatılacak. Sıfırlama başarıyla tamamlandığında size bildirilir. Sistem modeline bağlı olarak, 8100 bir cihaz için 45-60 dakika sürebilir ve bu işlemi tamamlaması 8600 için 60-90.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Cihaz denetleyicilerini yönetme ile ilgili sorular ve yanıtlar
Bu bölümde, StorSimple cihaz denetleyicilerini yönetme hakkında sık sorulan sorulardan bazılarını özetliyoruz.

**Ç.** Cihazımın her ikisi de sağlıklı ve açık durumda olduğunda ve etkin denetleyiciyi yeniden başlattıktan veya kapatdığımda ne olur?

**A.** Cihazınızdaki denetleyicilerin her ikisi de sağlıklı ve açık ise sizden onay istenir. Şunları seçebilirsiniz:

* **Etkin denetleyiciyi yeniden başlatma** – etkin bir denetleyiciyi yeniden başlatmanın, cihazın pasif denetleyiciye yük devretmeye neden olduğunu size bildirilir. Denetleyici yeniden başlatılır.
* **Etkin bir denetleyiciyi kapatma** : etkin bir denetleyicinin kapandığı için kapalı kalma süresi ile ilgili bildirim alırsınız. Ayrıca, denetleyiciyi açmak için cihazdaki güç düğmesine gönderim yapmanız gerekir.

**Ç.** Cihazımın pasif denetleyicisi kullanılamıyorsa veya devre dışı bırakıldıysa ve etkin denetleyiciyi yeniden başlattıktan veya kapatdığımda ne olur?

**A.** Cihazınızdaki pasif denetleyici kullanılamıyorsa veya kapatılmışsa ve şunları yapabilirsiniz:

* **Etkin denetleyiciyi yeniden başlatın** : işleme devam etmek, hizmette geçici bir kesinti oluşmasına neden olur ve sizden onay istenir.
* **Etkin bir denetleyiciyi kapatma** – işlem kapalı kalma süresi ile sonuçlanmasına devam eden bilgilendirilirsiniz. Ayrıca, cihazı açmak için bir veya her iki denetleyicinin güç düğmesini de göndermeniz gerekir. Onaylamanız istenir.

**Ç.** Denetleyicinin yeniden başlatılması veya kapatılmasını ne zaman devam etmez?

**A.** Bir denetleyicinin yeniden başlatılması veya kapatılması şu durumlarda başarısız olabilir:

* Bir cihaz güncelleştirmesi devam ediyor.
* Bir denetleyici yeniden başlatması zaten devam ediyor.
* Bir denetleyici kapatması zaten devam ediyor.

**Ç.** Bir denetleyicinin yeniden başlatılıp kapatıldığına nasıl emin olabilirsiniz?

**A.** Denetleyici dikey penceresinde denetleyici durumunu kontrol edebilirsiniz. Denetleyici durumu, bir denetleyicinin yeniden başlatma veya kapatma sürecinde olup olmadığını gösterir. Ayrıca, denetleyicinin yeniden başlatılması veya kapatılması durumunda **Uyarılar** dikey penceresi bir bilgilendirme uyarısı içerir. Denetleyici yeniden başlatma ve kapatılma işlemleri de etkinlik günlüklerine kaydedilir. Etkinlik günlükleri hakkında daha fazla bilgi için [etkinlik günlüklerini görüntüleme](storsimple-8000-service-dashboard.md#view-the-activity-logs)sayfasına gidin.

**Ç.** Denetleyici yük devretmesinin sonucu olarak g/ç 'nin herhangi bir etkisi var mı?

**A.** Başlatıcı ve etkin denetleyici arasındaki TCP bağlantıları, denetleyici yük devretmesinin bir sonucu olarak sıfırlanır, ancak pasif denetleyici işlemi kabul eder. Bu işlemin kursu sırasında başlatıcılar ve cihaz arasındaki g/ç etkinliğinde geçici (30 saniyeden az) bir etkinlik olabilir.

**Ç.** Nasıl yaparım?, kapatıldıktan ve kaldırıldıktan sonra denetleyicim hizmete geri dönsin mi?

**A.** Bir denetleyiciyi hizmete döndürmek için, [StorSimple cihazınızda bir denetleyici modülünü değiştirme](storsimple-8000-controller-replacement.md)bölümünde açıklandığı gibi gövdeye eklemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar
* StorSimple cihaz denetleyicilerinizde Bu öğreticide listelenen yordamları kullanarak çözemayacağınızı sorun yaşarsanız [Microsoft desteği başvurun](storsimple-8000-contact-microsoft-support.md).
* StorSimple Device Manager hizmetini kullanma hakkında daha fazla bilgi edinmek için StorSimple [Device Manager hizmetini kullanarak StorSimple cihazınızı yönetin](storsimple-8000-manager-service-administration.md)sayfasına gidin.