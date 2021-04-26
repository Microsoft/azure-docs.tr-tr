---
title: StorSimple 5000-7000 serisinde verileri 8000 serisi cihaza geçir | Microsoft Docs
description: StorSimple 5000-7000 serisindeki verileri 8000 serisi cihaza geçirme ve önkoşulları geçiş işlemi için ne olduğu hakkında bilgi edinin.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: 58b7b67842e9ba385b34ea4d8fdbcac190076218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99428175"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>StorSimple 5000-7000 serisinden verileri 8000 serisi cihaza geçirme

> [!IMPORTANT]
> - 31 Temmuz 2019 tarihinde, StorSimple 5000/7000 Serisi destek (EOS) durumunun sonuna ulaşacaktır. StorSimple 5000/7000 Serisi müşterilerinin belgede açıklanan alternatifden birine geçiş yapmanızı öneririz.
> - Geçiş Şu anda yardımlı bir işlemdir. StorSimple 5000-7000 Serisi cihazınızdan bir 8000 serisi cihaza veri geçirmeyi düşünüyorsanız, Microsoft Desteği ile geçiş zamanlamanız gerekir. Microsoft Desteği, daha sonra aboneliğinizi geçişe olanak tanır. Daha fazla bilgi için bkz. [destek bileti açma](storsimple-8000-contact-microsoft-support.md).
> - Hizmet isteğini kaydettikten sonra, geçiş planının yürütülmesi ve gerçekten geçişi başlatmak için birkaç hafta sürebilir.
> - Microsoft Desteği iletişime geçmeden önce, makalede belirtilen [geçiş önkoşullarını](#migration-prerequisites) gözden geçirmeyi ve tamamlamayı unutmayın.

## <a name="overview"></a>Genel Bakış

Bu makalede, StorSimple 5000-7000 Serisi müşterilerinin verilerini StorSimple 8000 serisi fiziksel cihaza veya bir 8010/8020 bulut gerecine geçirmeye izin veren geçiş özelliği açıklanır. Bu makalede ayrıca, 5000-7000 Serisi eski bir cihazdan 8000 serisi fiziksel veya bulut gerecine veri geçirmek için gereken adımlara yönelik indirilebilir adım adım yönergeler de bağlantı sağlar.

Bu makale, hem şirket içi 8000 serisi cihaz hem de StorSimple Cloud Appliance için geçerlidir.


## <a name="migration-feature-versus-host-side-migration"></a>Geçiş özelliği, konak tarafında geçişe karşı

Geçiş özelliğini kullanarak veya bir konak tarafı geçişi gerçekleştirerek verilerinizi taşıyabilirsiniz. Bu bölümde, uzmanları ve dezavantajları dahil olmak üzere her yöntemin özellikleri açıklanmaktadır. Verilerinizi geçirmek istediğiniz yöntemi anlamak için bu bilgileri kullanın.

Geçiş özelliği, 7000/5000 serisinden 8000 serisine bir olağanüstü durum kurtarma (DR) işlemini benzetir. Bu özellik, verileri 5000/7000 Serisi biçiminden Azure 'da 8000 seri biçimine geçirmenize olanak sağlar. Geçiş işlemi StorSimple geçiş aracı kullanılarak başlatılır. Araç, yedekleme meta verilerinin 8000 serisi cihaza indirilmesini ve dönüştürülmesini başlatır ve ardından en son yedeklemeyi kullanarak cihazdaki birimleri kullanıma sunar.

| Avantajlar                                                                                                                                     |Dezavantajlar                                                                                                                                                              |
|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Geçiş işlemi, 5000/7000 serisinde gerçekleştirilen yedeklemelerin geçmişini korur.                                               | Kullanıcılar verilere erişmeyi denediğinde, bu geçiş verileri Azure 'dan indirerek veri indirme maliyetlerini de indirir.                                     |
| Konak tarafında hiçbir veri geçirilmez.                                                                                                     | İşlemin başlatılması ve 8000 serisinde en son yedeklemenin ortaya çıkması arasında kesinti olması gerekir (Geçiş Aracı kullanılarak tahmin edilebilir). |
| Bu işlem, 8000 serisi cihazlarda tüm ilkeleri, bant genişliği şablonlarını, şifrelemeyi ve diğer ayarları korur.                      | Kullanıcı erişimi, yalnızca kullanıcılar tarafından erişilen verileri geri getirecek ve tüm veri kümesini yeniden sarmamayacak.                                                  |
| Bu işlem, Azure 'daki daha eski yedeklemelerin, üretimi etkilemeden zaman uyumsuz olarak dönüştürülmesi için ek süre gerektirir | Geçiş, yalnızca bir bulut yapılandırma düzeyinde yapılabilir.  Bir bulut yapılandırmasındaki tek tek birimler ayrı olarak geçirilemez                       |

Ana bilgisayar tarafı geçişi, 8000 serisinin bağımsız olarak ayarlanmasını ve verilerin 5000/7000 Serisi cihazdan 8000 serisi cihazına kopyalanmasını sağlar. Bu, verileri bir depolama cihazından diğerine geçirmeye eşdeğerdir. Diskpatron, Robocopy gibi çeşitli araçlar, verileri kopyalamak için kullanılır.

| Avantajlar                                                                                                                      |Dezavantajlar                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Geçiş, birim temelinde tek başına approached olabilir.                                               | Önceki yedeklemeler (5000/7000 serisinde alınan), 8000 serisi cihazda kullanılamayacak.                                                                                                       |
| Azure 'daki bir depolama hesabında verilerin birleştirilmesi için izin verir.                                                       | 8000 serisi üzerinde buluta ilk yedekleme, 8000 serisi üzerindeki tüm verilerin Azure 'a yedeklenmesi gerektiğinden daha uzun sürer.                                                                     |
| Başarılı bir geçiş sonrasında tüm veriler gereç üzerinde yereldir. Verilere erişirken gecikme süresi yok. | Azure depolama tüketimi, veriler 5000/7000 cihazdan silinene kadar artar.                                                                                                        |
|                                                                                                                           | 7000/5000 serisi cihazda büyük miktarda veri varsa, geçiş sırasında bu verilerin Azure 'dan indirilmesi gerekir ve Azure 'dan veri yükleme ile ilgili maliyet ve gecikme süreleri uygulanır |

Bu makale yalnızca 5000/7000 ' den 8000 ' e kadar seri cihaza geçiş özelliğine odaklanır. Konak tarafı geçişi hakkında daha fazla bilgi için [diğer depolama cihazlarından geçiş](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf)bölümüne gidin.

## <a name="migration-prerequisites"></a>Geçiş önkoşulları

Eski 5000 veya 7000 Serisi cihazınız ve 8000 serisi StorSimple cihazı için geçiş önkoşulları aşağıda verilmiştir.

> [!IMPORTANT]
> Microsoft Desteği bir hizmet isteği vermeden önce geçiş önkoşullarını gözden geçirin ve doldurun.

### <a name="for-the-50007000-series-device-source"></a>5000/7000 Serisi cihaz (kaynak) için

Geçişe başlamadan önce aşağıdakileri doğrulayın:

* 5000 veya 7000 Serisi kaynak cihazınız var; cihaz canlı veya aşağı olabilir.

    > [!IMPORTANT]
    > Geçiş işlemi boyunca bu cihaza seri erişiminizin olmasını öneririz. Herhangi bir cihaz sorunu olması gerekir, seri erişim sorun gidermeye yardımcı olabilir.

* 5000 veya 7000 Serisi kaynak cihazınız, yazılım sürümü v 2.1.1.518 veya üstünü çalıştırıyor. Önceki sürümler desteklenmez.
* 5000 veya 7000 serisinin çalıştırıldığı sürümü doğrulamak için, Web Kullanıcı arabiriminizdeki sağ üst köşeye bakın. Bu, cihazınızın çalıştığı yazılım sürümünü görüntülemelidir. Geçiş için, 5000 veya 7000 serinizin v 2.1.1.518 çalıştırıyor olması gerekir.

    ![Eski Cihazdaki yazılım sürümünü denetle](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Canlı cihazınız v 2.1.1.518 veya sonraki bir sürümü çalıştırıyorsa, lütfen sisteminizi gerekli en düşük sürüme yükseltin. Yükseltmeyi gerçekleştirmenize yardımcı olması için Microsoft Desteği çalışmanız gerekebilir.
    * V 2.1.1.518 çalıştırıyorsanız, kayıt defteri geri yükleme hatalarıyla ilgili herhangi bir bildirim olup olmadığını görmek için Web Kullanıcı arabirimi ' ne gidin. Kayıt defteri geri yükleme işlemi başarısız olduysa, kayıt defteri geri yükleme çalıştırın. Kayıt defterinizi geri yüklemenize yardımcı olması için Microsoft Desteği çalışmanız gerekebilir.
    * V 2.1.1.518 çalıştırmayan bir cihaz varsa, v 2.1.1.518 çalıştıran bir değiştirme cihazına yük devretme işlemi gerçekleştirin. Ayrıntılı yönergeler için 5000/7000 Serisi StorSimple cihazınızın DR bölümüne bakın.
    * Bulut anlık görüntüsü alarak cihazınızın verilerini yedekleyin.
    * Kaynak cihazda çalışan diğer tüm etkin yedekleme işlerini denetleyin. Bu, StorSimple veri koruma konsolu ana bilgisayarındaki işleri içerir. Geçerli işlerin tamamlanmasını bekleyin.


### <a name="for-the-8000-series-physical-device-target"></a>8000 serisi fiziksel cihaz (hedef) için

Geçişe başlamadan önce aşağıdakileri doğrulayın:

* Hedef 8000 serisi cihazınız kaydettirildi ve çalışıyor. Daha fazla bilgi için bkz. [StorSimple cihazınızı StorSimple Yöneticisi hizmeti Ile dağıtma](storsimple-8000-deployment-walkthrough-u2.md).
* 8000 serisi cihazınızda en son StorSimple 8000 serisi güncelleştirme 5 yüklüdür ve 6.3.9600.17845 veya üzeri sürümü çalışmaktadır. Cihazınızda en son güncelleştirmeler yüklü değilse, geçişe devam edebilmeniz için en son güncelleştirmeleri yüklemeniz gerekir. Daha fazla bilgi için bkz. [8000 serisi cihazınıza en son güncelleştirme nasıl yüklenir](storsimple-8000-install-update-5.md).
* Azure aboneliğiniz geçiş için etkinleştirildi. Aboneliğiniz etkinleştirilmemişse, geçiş için aboneliğinizi etkinleştirmek üzere Microsoft Desteği başvurun.

### <a name="for-the-80108020-cloud-appliance-target"></a>8010/8020 bulut gereci (hedef) için

Geçişe başlamadan önce aşağıdakileri doğrulayın:

* Hedef bulut gereciniz kaydettirildi ve çalışıyor. Daha fazla bilgi için bkz. [StorSimple Cloud Appliance dağıtma ve yönetme](storsimple-8000-cloud-appliance-u2.md).
* Bulut gereciniz en son StorSimple 8000 serisi güncelleştirme 5 yazılım sürümünü çalıştırıyor 6.3.9600.17845. Bulut gereciniz güncelleştirme 5 ' i çalıştırıyorsa, geçişe devam etmeden önce yeni bir güncelleştirme 5 bulut gereci oluşturun. Daha fazla bilgi için bkz. [8010/8020 bulut gereci oluşturma](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>StorSimple geçiş aracı 'nı çalıştıran bilgisayar için

StorSimple geçiş aracı, StorSimple 5000-7000 serisinden bir 8000 serisi cihaza veri geçirmenize olanak sağlayan bir UI tabanlı araçtır. StorSimple geçiş aracı 'nı yüklemek için, aşağıdaki gereksinimleri karşılayan bir bilgisayar kullanın.

Bilgisayar Internet bağlantısına sahiptir ve:

* Aşağıdaki işletim sistemini çalıştırıyor
    * Windows 10.
    * StorSimple geçiş aracı 'nı yüklemek için Windows Server 2012 R2 (veya üzeri).
* .NET 4.5.2 yüklendi.
* , Aracı yüklemek ve kullanmak için en az 5 GB boş alana sahiptir.

> [!TIP]
> StorSimple cihazınız bir Windows Server konağına bağlıysa, geçiş aracını Windows Server ana bilgisayarına yükleyebilirsiniz.

#### <a name="to-install-storsimple-migration-tool"></a>StorSimple geçiş aracı 'nı yüklemek için

Bilgisayarınıza StorSimple geçiş aracı 'nı yüklemek için aşağıdaki adımları gerçekleştirin.

1. _StorSimple8000SeriesMigrationTool_ klasörünü Windows bilgisayarınıza kopyalayın. Yazılımın kopyalandığı sürücünün yeterli alana sahip olduğundan emin olun.

    Klasöründe _StorSimple8000SeriesMigrationTool.exe.config_ araç yapılandırma dosyasını açın. Dosyanın kod parçacığı aşağıda verilmiştir.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Anahtarlara karşılık gelen değerleri düzenleyin ve ile değiştirin:

    * `UserName` – Azure portal oturum açmak için Kullanıcı adı.
    * `SubscriptionName and SubscriptionId` – Azure aboneliğinizin adı ve KIMLIĞI. StorSimple Device Manager hizmet giriş sayfanızda, **genel** altında **Özellikler**' e tıklayın. Hizmet ile ilişkili abonelik adı ve abonelik KIMLIĞINI kopyalayın.
    * `ResourceName` – StorSimple Device Manager hizmetinizin adı Azure portal. Hizmet özellikleri altında de gösterilir.
    * `ResourceGroup` – Azure portal StorSimple Device Manager hizmetinize ilişkin kaynak grubunun adı. Hizmet özellikleri altında de gösterilir.
    ![Hedef cihaz için hizmet özelliklerini denetle](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId` – Azure portal Azure Active Directory kiracı KIMLIĞI. Microsoft Azure 'da yönetici olarak oturum açın. Microsoft Azure portal **Azure Active Directory**' ye tıklayın. **Yönet**'in altında **Özellikler**’e tıklayın. Kiracı KIMLIĞI, **DIZIN kimliği** kutusunda gösterilir.
    ![Azure Active Directory için kiracı KIMLIĞINI denetle](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Yapılandırma dosyasına yapılan değişiklikleri kaydedin.
4.  Aracı başlatmak için _StorSimple8000SeriesMigrationTool.exe_ çalıştırın. Kimlik bilgileri istendiğinde Azure portal aboneliğinizle ilişkili kimlik bilgilerini sağlayın. 
5.  StorSimple geçiş aracı kullanıcı arabirimi görüntülenir.
  

## <a name="next-steps"></a>Sonraki adımlar
[StorSimple 5000-7000 serisinden bir 8000 serisi cihaza veri geçirme](https://databoxbuilds.blob.core.windows.net/storsimpledocs/MicrosoftAzureStorSimple_Migration_Guide_20171115.pdf)hakkında adım adım yönergeleri indirin.
