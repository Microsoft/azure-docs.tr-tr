---
title: "StorSimple sanal cihazını Güncelleştirme 2 | Microsoft Docs"
description: "Microsoft Azure sanal ağında StorSimple sanal cihazı oluşturmayı, dağıtmayı ve yönetmeyi öğrenin. (StorSimple Güncelleştirme 2 için geçerlidir)."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: f37752a5-cd0c-479b-bef2-ac2c724bcc37
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2017
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 48d9d8ae97eb763932dd6a59a7df01ae92c92eff
ms.lasthandoff: 03/24/2017


---
# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>Azure’da StorSimple sanal cihazını dağıtma ve yönetme
## <a name="overview"></a>Genel Bakış
StorSimple 8000 serisi sanal cihaz Microsoft Azure StorSimple çözümünüzle birlikte gelen ek bir yetenektir. StorSimple sanal cihazı Microsoft Azure sanal ağındaki bir sanal makinede çalışır ve bunu ana bilgisayarlarınızdaki verileri yedeklemek ve kopyalamak için kullanabilirsiniz. Bu öğretici Azure’da bir sanal cihaz yönetmeyi ve dağıtmayı açıklar ve yazılım sürümü Güncelleştirme 2'yi veya düşük sürümünü çalıştıran tüm sanal cihazlar için geçerlidir.

#### <a name="virtual-device-model-comparison"></a>Sanal cihaz modeli karşılaştırması
StorSimple sanal cihazı, standart 8010 (önceden 1100 olarak biliniyordu) ve premium 8020 (Güncelleştirme 2’de sunulmuştur) olmak üzere iki model seçeneğiyle kullanıma sunulmuştur. Aşağıdaki tabloda iki model karşılaştırılmıştır.

| Cihaz modeli | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **Maksimum kapasite** |30 TB |64 TB |
| **Azure VM** |Standard_A3 (4 çekirdek, 7 GB bellek) |Standard_DS3 (4 çekirdek, 14 GB bellek) |
| **Sürüm uyumluluğu** |Güncelleştirme 2 ya da üst sürümü öncesini çalıştıran sürümler |Güncelleştirme 2 ya da üst sürümünü çalıştıran sürümler |
| **Bölge kullanılabilirliği** |Tüm Azure bölgeleri |Premium Depolamayı destekleyen tüm Azure bölgeleri<br></br> Premium depolama bölgeleri, [Bölgeye Göre Azure Hizmetleri](https://azure.microsoft.com/en-us/regions/services) listesinde *Disk depolama* satırına karşılık gelen bölgelerdir. |
| **Depolama türü** |Yerel diskler için Azure Standard Storage kullanır.<br></br> [Standart Depolama hesabı oluşturmayı](../storage/storage-create-storage-account.md) öğrenin. |Yerel diskler için Azure Premium Depolama kullanır<sup>2</sup> <br></br>[Premium Storage hesabı oluşturmayı](../storage/storage-premium-storage.md) öğrenin. |
| **İş yükü kılavuzu** |Yedeklerden dosya alma öğe düzeyi |Bulut geliştirme ve test senaryoları, düşük gecikme, daha yüksek performans iş yükleri <br></br>Olağanüstü durum kurtarma için ikincil cihaz |

<sup>1</sup> *Önceden 1100 olarak biliniyordu*.

<sup>2</sup> *Hem 8010 hem de 8020 bulut katmanı için Azure Standart Depolama kullanır. Tek fark cihazdaki yerel katmandadır*.

Bu makalede, Azure’da bir StorSimple sanal cihazı dağıtma işlemi adım adım açıklanmaktadır. Bu makaleyi okuduktan sonra şunları yapabilir olacaksınız:

* Sanal cihazın fiziksel cihazdan farkını anlama.
* Sanal cihaz oluşturabilme ve yapılandırabilme.
* Sanal cihaza bağlanma
* Sanal cihazla çalışmayı öğrenme.

Bu öğretici Güncelleştirme 2 ve üst sürümünü çalıştıran tüm StorSimple sanal cihazları için geçerlidir.

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>Sanal cihazın fiziksel cihazdan farkı
StorSimple sanal cihazı, Microsoft Azure Sanal Makinesi’nde tek düğümde çalışan StorSimple’ın yalnızca yazılım olan bir sürümüdür. Sanal cihaz, fiziksel cihazınızın kullanılamadığı kurtarma senaryolarını destekler ve yedekler, şirket içi olağanüstü durum kurtarma ve bulut geliştirme ve test senaryolarından öğe düzeyinde alma için uygundur.

#### <a name="differences-from-the-physical-device"></a>Fiziksel cihazdan farklar
Aşağıdaki tabloda StorSimple sanal cihazı ve StorSimple fiziksel cihazı arasındaki bazı temel farklar gösterilmektedir.

|  | Fiziksel cihaz | Sanal cihaz |
| --- | --- | --- |
| **Konum** |Veri merkezinde yer alır. |Azure üzerinde çalışır. |
| **Ağ arabirimleri** |Altı ağ arabirimi bulunur: VERİ 0’dan VERİ 5’e. |Yalnızca bir ağ arabirimi bulunur: VERİ 0 |
| **Kayıt** |Yapılandırma adımı sırasında kaydedilir. |Kayıt ayrı bir görevdir. |
| **Hizmeti verileri şifreleme anahtarı** |Fiziksel cihazda yeniden oluşturun ve ardından yeni anahtarla sanal cihazı güncelleştirin. |Sanal cihazdan yeniden oluşturamazsınız. |

## <a name="prerequisites-for-the-virtual-device"></a>Sanal cihaz için önkoşullar
Aşağıdaki bölümlerde, StorSimple sanal cihazınız için yapılandırma önkoşulları açıklanmaktadır. Sanal cihazı dağıtmadan önce, [sanal cihaz kullanımıyla ilgili güvenlik konularını](storsimple-security.md#storsimple-virtual-device-security) gözden geçirin.

#### <a name="azure-requirements"></a>Azure gereksinimleri
Sanal cihaz sağlamadan önce, Azure ortamınızda aşağıdaki hazırlıkları yapmanız gerekir:

* Sanal cihaz için, [Azure üzerinde bir sanal ağ yapılandırın](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Premium Storage kullanıyorsanız, Premium Storage’ı destekleyen bir Azure bölgesinde sanal ağ oluşturmanız gerekir. Premium depolama bölgeleri, [Bölgeye Göre Azure Hizmetleri](https://azure.microsoft.com/en-us/regions/services) listesinde *Disk depolama* satırına karşılık gelen bölgelerdir.
* Kendi DNS sunucu adınızı belirtmek yerine Azure tarafından sağlanan varsayılan DNS sunucusunu kullanmanız önerilir. DNS sunucusu adınız geçerli değilse veya DNS sunucusu IP adreslerini doğru çözümleyemiyorsa, sanal cihaz oluşturma başarısız olur.
* Noktadan siteye ve siteden siteye isteğe bağlıdır, ancak gerekli değildir. İsterseniz, daha gelişmiş senaryolar için bu seçenekleri yapılandırabilirsiniz.
* Sanal cihaz tarafından sunulan birimleri kullanabileceğiniz sanal ağda [Azure Sanal Makineleri](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (barındırma sunucuları) oluşturabilirsiniz. Bu sunucular aşağıdaki gereksinimleri karşılamalıdır:                             

  * iSCSI Initiator yazılımı yüklü Windows veya Linux sanal makineleri olmalıdır.
  * Sanal cihazla aynı sanal ağda çalışıyor olmalıdır.
  * Sanal cihazın iç IP adresi üzerinden sanal cihazın iSCSI hedefine bağlanabilir olmalıdır.
* Aynı sanal ağda iSCSI ve bulut trafiği için desteği yapılandırdığınızdan emin olun.

#### <a name="storsimple-requirements"></a>StorSimple gereksinimleri
Sanal cihaz oluşturmadan önce, Azure StorSimple hizmetinize aşağıdaki güncelleştirmeleri uygulayın:

* Sanal cihazınız için barındırma sunucuları olacak sanal makineler için [erişim denetimi kayıtları](storsimple-manage-acrs.md) ekleyin.
* Sanal cihazla aynı bölgede bir [depolama hesabı](storsimple-manage-storage-accounts.md#add-a-storage-account) kullanın. Farklı bölgelerdeki Depolama hesapları performansın düşmesine neden olabilir. Sanal cihazla Standart veya Premium Storage hesabı kullanabilirsiniz. [Standard Storage hesabı](../storage/storage-create-storage-account.md) ya da [Premium Storage hesabı](../storage/storage-premium-storage.md) oluşturma hakkında daha fazla bilgi
* Verileriniz için oluşturduğunuzdan sanal cihaz oluşturma için farklı bir depolama hesabı kullanın. Aynı depolama hesabı kullanmak performansın düşmesine neden olabilir.

Başlamadan önce aşağıdaki bilgilere sahip olduğunuzdan emin olun:

* Klasik Azure portalı hesabınıza erişim kimlik bilgileri.
* Fiziksel cihazınızdan alınan veri şifreleme anahtarının bir kopyası.

## <a name="create-and-configure-the-virtual-device"></a>Sanal cihaz oluşturma ve yapılandırma
Bu yordamları gerçekleştirmeden önce, [sanal cihaz önkoşullarını](#prerequisites-for-the-virtual-device) karşıladığınızdan emin olun.

Sanal ağ oluşturduktan, StorSimple Yöneticisi hizmeti yapılandırdıktan ve fiziksel StorSimple cihazınızı hizmete kaydettikten sonra, StorSimple sanal cihazı oluşturmak amacıyla aşağıdaki adımları kullanabilirsiniz.

### <a name="step-1-create-a-virtual-device"></a>1. Adım: Sanal cihaz oluşturma
StorSimple sanal cihazı oluşturmak için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

Bu adımda sanal cihaz oluşturulamazsa İnternet bağlantınız olmayabilir. Daha fazla bilgi için sanal cihaz oluştururken [İnternet bağlantısı sorunlarını giderme](#troubleshoot-internet-connectivity-errors) bölümüne gidin.

### <a name="step-2-configure-and-register-the-virtual-device"></a>2. Adım: Sanal cihaz yapılandırma ve kaydetme
Bu yordama başlamadan önce, hizmet verileri şifreleme anahtarının bir kopyasına sahip olduğunuzdan emin olun. Hizmet verileri şifreleme anahtarı, ilk StorSimple cihazınızı yapılandırdığınız sırada sizden güvenli bir konumda saklamanız istendiğinde oluşturulmuştur. Bir hizmeti verilerini şifreleme anahtarının bir kopyası sizde yoksa, yardım için Microsoft Destek’e başvurmanız gerekir.

StorSimple sanal cihazınızı yapılandırmak ve kaydetmek için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>3. Adım: (İsteğe bağlı) Cihaz yapılandırma ayarlarını değiştirme
Aşağıdaki bölümde, CHAP, StorSimple Snapshot Manager kullanmak ya da Cihaz Yöneticisi parolasını değiştirmek istiyorsanız, StorSimple sanal cihazı için gereken cihaz yapılandırma ayarları açıklanmaktadır.

#### <a name="configure-the-chap-initiator"></a>CHAP başlatıcısını yapılandırma
Bu parametre sanal cihazınızın (hedef), birimlere erişmeye çalışan başlatıcılardan (sunucu) beklediği kimlik bilgilerini içerir. Başlatıcılar, bu kimlik doğrulaması sırasında cihazınıza kendilerini tanıtacak CHAP kullanıcı adı ve CHAP parolası sağlar. Ayrıntılı adımlar için [Cihazınız için CHAP yapılandırma](storsimple-configure-chap.md#unidirectional-or-one-way-authentication)’ya gidin.

#### <a name="configure-the-chap-target"></a>CHAP hedefini yapılandırma
Bu parametre CHAP etkin başlatıcı karşılıklı veya çift yönlü kimlik doğrulama istediğinde sanal cihazınızın kullandığı kimlik bilgilerini içerir. Sanal cihazınız, bu kimlik doğrulama işlemi sırasında kendisini başlatıcıya tanıtmak için Ters CHAP kullanıcı adı ve Ters CHAP parolası kullanır. CHAP hedefi ayarlarının genel ayarlar olduğunu unutmayın. Bunlar uygulandığında, depolama sanal depolama cihazına bağlı olan tüm birimler CHAP kimlik doğrulamasını kullanır. Ayrıntılı adımlar için [Cihazınız için CHAP yapılandırma](storsimple-configure-chap.md#bidirectional-or-mutual-authentication)’ya gidin.

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager parolasını yapılandırma
StorSimple Snapshot Manager yazılımı Windows ana bilgisayarınıza bulunur ve yöneticilerin yerel ve bulut anlık görüntüleri biçiminde StorSimple cihazınızın yedeklerini yönetmelerine olanak tanır.

> [!NOTE]
> Sanal cihaz için, Windows ana bilgisayarınız bir Azure sanal makinesidir.
>
>

StorSimple Snapshot Manager’da bir cihazı yapılandırma sırasında, depolama cihazınızın kimlik doğrulaması için StorSimple cihazı IP adresi ve parolasını sağlamanız istenir. Ayrıntılı adımlar için [StorSimple Snapshot Manager parolasını yapılandırma](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)’ya gidin.

#### <a name="change-the-device-administrator-password"></a>Cihaz yöneticisi parolasını değiştirme
Sanal cihaza erişmek için Windows PowerShell arabirimini kullandığınızda, bir cihaz yöneticisi parolası girmeniz gerekir. Verilerinizin güvenliği için, sanal cihazın kullanılabilmesi amacıyla önce bu parolayı değiştirmeniz gereklidir. Ayrıntılı adımlar için [Cihaz yöneticisi parolasını yapılandırma](storsimple-change-passwords.md#change-the-device-administrator-password)’ya gidin.

## <a name="connect-remotely-to-the-virtual-device"></a>Sanal cihaza uzaktan bağlanma
Windows PowerShell arabirimi üzerinden sanal cihazınıza uzaktan erişim varsayılan olarak etkin değildir. Önce sanal cihazda uzaktan erişimi etkinleştirmeli ve ardından bunu, sanal cihazınıza erişim için kullanılacak istemcide etkinleştirmelisiniz.

Uzaktan bağlanmaya ilişkin iki adımlı işlem aşağıda ayrıntılı olarak verilmiştir.

### <a name="step-1-configure-remote-management"></a>1. Adım: Uzaktan yönetimi yapılandırma
StorSimple sanal cihazınız için uzaktan yönetimi yapılandırmak üzere aşağıdaki adımları gerçekleştirin.

[!INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>2. Adım: Sanal cihaza uzaktan erişim
StorSimple cihaz yapılandırma sayfasında uzaktan yönetimi etkinleştirdikten sonra, aynı sanal ağdaki başka bir sanal makineden sanal cihaza bağlanmak için Windows PowerShell uzaktan iletişimi kullanabilirsiniz; örneğin, iSCSI’ye bağlanmak için yapılandırdığınız ve kullandığınız ana bilgisayar sanal makinesinden bağlanabilirsiniz. Çoğu dağıtımda, sanal cihaza erişmek için kullanabileceğiniz ana bilgisayar sanal makinenize erişim için halihazırda bir ortak uç nokta açmış olacaksınız.

> [!WARNING]
> **Gelişmiş güvenlik için, uç noktalara bağlanırken HTTPS kullanmanız ve ardından PowerShell uzak oturumunuz tamamladıktan sonra uç noktaları silmeniz önerilir.**
>
>

Sanal cihazınızı için uzaktan iletişim ayarlamak amacıyla [StorSimple cihazınıza uzaktan bağlanma](storsimple-remote-connect.md) yordamlarını izlemelisiniz.

## <a name="connect-directly-to-the-virtual-device"></a>Sanal cihaza doğrudan bağlanma
Sanal cihaza doğrudan da bağlanabilirsiniz. Sanal cihaza sanal ağ dışındaki veya Microsoft Azure ortamı dışındaki başka bir bilgisayardan doğrudan bağlanmak istiyorsanız, aşağıdaki yordamda açıklandığı şekilde ek uç noktalar oluşturmanız gerekir.

Sanal cihazda ortak uç nokta oluşturmak için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

Bu uygulama sanla ağınızdaki ortak uç noktaların sayısını en aza indireceğinden, aynı sanal ağdaki başka bir sanal makineden bağlanmanızı öneririz. Bu yöntemi kullandığınızda, Uzak Masaüstü oturumu aracılığıyla sanal makineye bağlanın ve yerel ağdaki başka bir Windows istemcisinde olduğu gibi, sanal makineyi kullanım için yapılandırın. Bağlantı noktası zaten biliniyor olacağından, ortak bağlantı noktası numarası eklemeniz gerekmez.

## <a name="work-with-the-storsimple-virtual-device"></a>StorSimple sanal cihazıyla çalışma
Artık StorSimple sanal cihazınızı oluşturduğunuza ve yapılandırdığınıza göre, bununla çalışmaya başlamaya hazırsınız. Fiziksel StorSimple cihazında çalıştığınız gibi bir sanal cihazdaki birim kapsayıcıları, birimler ve yedekleme ilkeleri ile çalışabilirsiniz; tek fark cihaz listenizden sanal cihazı seçtiğinizden emin olmanızdır. Sanal cihaza ilişkin çeşitli yönetim görevleri hakkında adım adım yordamlar için, [Sanal cihazı yönetmek için StorSimple Yöneticisi hizmetini kullanma](storsimple-manager-service-administration.md)’ya başvurun.

Aşağıdaki bölümlerde, sanal cihazla çalışırken karşınıza çıkacak farklılıklar açıklanmaktadır.

### <a name="maintain-a-storsimple-virtual-device"></a>StorSimple sanal cihazına bakım yapma
Bu, yalnızca yazılım olan bir cihaz olduğundan, fiziksel cihaza ilişkin bakımla karşılaştırıldığında sanal cihaza ilişkin bakım minimumdur. Aşağıdaki seçenekleriniz vardır:

* **Yazılım güncelleştirmeleri** – Tüm güncelleştirme durum iletileriyle birlikte, yazılımın güncelleştirildiği son tarihi görüntüleyebilirsiniz. Yeni güncelleştirmeleri denetlemek istiyorsanız, el ile tarama gerçekleştirmek için sayfanın alt kısmındaki **Güncelleştirmeleri tara** düğmesini kullanabilirsiniz. Güncelleştirmeler varsa, yüklemek için **Güncelleştirmeleri Yükle**’ye tıklayın. Sanal cihazda tek bir arabirim olduğundan, bu güncelleştirmeler uygulandığında ufak hizmet kesintisi olacağı anlamına gelir. Sanal cihaz, yayımlanan güncelleştirmeleri uygulamak için kapanır e yeniden başlatılır (gerekliyse). Adım adım yordam için, [cihazınızı güncelleştirme](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal)ye gidin.
* **Destek paketi** – Sanal cihazınızla ilgili sorunlarda Microsoft Destek’e yardımcı olmak için bir destek paketi oluşturabilir ve karşıya yükleyebilirsiniz. Adım adım bir yordam için [destek paketi oluşturma ve yönetme](storsimple-create-manage-support-package.md)’ye gidin.

### <a name="storage-accounts-for-a-virtual-device"></a>Sanal cihaz için depolama hesapları
Depolama hesapları StorSimple Yöneticisi hizmeti, sanal cihaz ve fiziksel cihaz tarafından kullanılmak üzere oluşturulmuştur. Depolama hesaplarınızı oluşturduğunuzda,bölgenin tüm sistem bileşenlerinde tutarlı olmasına yardımcı olmak amacıyla kolay bir adla bölge tanımlayıcısı kullanmanızı öneririz. Sanal bir cihaz için, performans sorunlarını önlemek amacıyla tüm bileşenlerin aynı bölgede olması önemlidir.

Adım adım bir yordam için [depolama hesabı ekleme](storsimple-manage-storage-accounts.md#add-a-storage-account)’ye gidin.

### <a name="deactivate-a-storsimple-virtual-device"></a>StorSimple sanal cihazını devre dışı bırakma
Sanal cihazı devre dışı bırakmak, hazırlandığında oluşturulan sanal makineyi ve kaynakları siler. Sanal cihaz devre dışı bırakıldıktan sonra, önceki durumuna geri yüklenemez. Sanal cihazı devre dışı bırakmadan önce, buna bağlı istemcileri ve ana bilgisayarları durdurduğunuzdan ya da sildiğinizden emin olun.

Sanal cihazı devre dışı bırakmak aşağıdaki eylemlere neden olur:

* Sanal cihaz kaldırılır.
* Sanal cihaz için oluşturulan işletim sistemi diski ve veri diskleri kaldırılır.
* Hazırlama sırasında oluşturulan barındırılan hizmet ve sanal ağ korunur. Bunları kullanmıyorsanız, bunları el ile silmeniz gerekir.
* Sanal cihaz için oluşturulan bulut anlık görüntüleri korunur.

Adım adım yordam için [StorSimple cihazınızı devre dışı bırakma ve silme](storsimple-deactivate-and-delete-device.md)’ye gidin.

Sanal cihaz StorSimple Yöneticisi hizmet sayfasında devre dışı olarak görünmeye başladıktan sonra, sanal cihazı **Cihazlar** sayfasındaki cihaz listesinden silebilirsiniz.

### <a name="start-stop-and-restart-a-virtual-device"></a>Sanal cihazı başlatma, durdurma ve yeniden başlatma
StorSimple fiziksel cihazının aksine, StorSimple sanal cihazında basmak için güç açık veya güç kapalı düğmesi yoktur. Ancak, sanal cihazı durdurmanız ve yeniden başlatmanız gereken durumlar olabilir. Örneğin, bazı güncelleştirmeler güncelleştirme işlemini tamamlamak için sanal makinenin yeniden başlatılmasını gerektirebilir. Sanal cihazı başlatma, durdurma ve yeniden başlatmanın en kolay yolu, Sanal Makineleri Yönetme Konsolu’nu kullanmaktır.

Yönetim Konsolu’na baktığınızda, oluşturulduktan sonra varsayılan olarak başlatıldığında, sanal cihaz durumu **çalışıyor** şeklindedir. Bir sanal makineyi istediğiniz zaman başlatabilir, durdurabilir veya yeniden başlatabilirsiniz.

[!INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>Fabrika ayarlarına sıfırlama
Yalnızca sanal cihazınızı baştan başlatmak istediğinize karar verirseniz, devre dışı bırakmanız ve silmeniz ve ardından yeni bir tane oluşturmanız yeterlidir. Fiziksel cihazınız sıfırlandığında olduğu gibi, yeni sanal cihazınızda yüklü güncelleştirme bulunmaz; bu nedenle, kullanmadan önce güncelleştirmeleri denetlediğinizden emin olun.

## <a name="fail-over-to-the-virtual-device"></a>Sanal cihaza yük devretme
Olağanüstü Durum Kurtarma (DR), StorSimple sanal cihazın tasarlanma ana senaryolarından biridir. Bu senaryoda, fiziksel StorSimple cihazı veya veri merkezinin tamamı kullanılamayabilir. Neyse ki, işlemleri farklı bir konuma geri yüklemek için sanal cihazı kullanabilirsiniz. DR sırasında kaynak cihazdaki birim kapsayıcıları sahipliği değiştirir ve sanal cihaza aktarılır. DR için önkoşullar, sanal cihazın oluşturulması ve yapılandırılması, birim kapsayıcısındaki tüm birimlerin çevrimdışına alınması ve birim kapsayıcısının ilişkili bir bulut anlık görüntüsüne sahip olmasıdır.

> [!NOTE]
> * Sanal cihazı DR için ikincil cihaz olarak kullanırken, 8010’nun 30 TB Standard Storage ve 8020’nin 64 TB Premium Storage içerdiğini unutmayın. Daha yüksek kapasiteli 8020 sanal cihazı bir DR senaryosuna daha uygun olabilir.
> * Güncelleştirme 2 çalıştıran bir cihazdan Güncelleştirme 1 yazılımı öncesini çalıştıran bir cihaza yük devredemez ya da kopyalama yapamazsınız. Ancak Güncelleştirme 2 çalıştıran bir cihazdan Güncelleştirme 1 (1.1 veya 1.2) çalıştıran bir cihaza yük devredebilirsiniz.
>
>

Adım adım bir yordam için [sanal bir cihaza yük devretme](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device)’ye gidin.

## <a name="shut-down-or-delete-the-virtual-device"></a>Sanal cihazı kapatma ya da silme
Önceden bir StorSimple sanal cihazı yapılandırmış ve kullanmış ancak şimdi bunun kullanımı için ücret tahakkuk etmesini durdurmak istiyorsanız, sanal cihazı kapatmanız yeterlidir. Sanal cihazı kapatmak, bunun depolamadaki işletim sistemini ya da veri disklerini silmez. Aboneliğinize tahakkuk eden ücretleri durdurur, ancak işletim sistemi ve veri diskleri için depolama ücretleri devam eder.

Sanal cihazı siler veya kapatırsanız, StorSimple Yönetici hizmetinin Cihazlar sayfasında **Çevrimdışı** olarak görünür. Sanal cihaz tarafından oluşturulan yedekleri de silmek isterseniz, cihazı devre dışı bırakmayı ya da silmeyi seçebilirsiniz. Daha fazla bilgi için bkz. [StorSimple cihazını devre dışı bırakma ve silme](storsimple-deactivate-and-delete-device.md).

[!INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[!INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>İnternet bağlantısı sorunlarını giderme
Sanal cihaz oluştururken İnternet bağlantısı yoksa oluşturma adımı başarısız olur. Hatanın İnternet bağlantısından kaynaklanması durumunda sorun gidermek için klasik Azure portalında aşağıdaki adımları gerçekleştirin:

1. Azure’da bir Windows server 2012 sanal makinesi oluşturun. Bu sanal makine, sanal cihazınızla aynı depolama hesabı, sanal ağ ve alt ağı kullanmalıdır. Azure’da aynı depolama hesabını, sanal ağı ve alt ağı kullanan bir Windows Server ana bilgisayarınız varsa, İnternet bağlantısı sorunlarını gidermek için onu da kullanabilirsiniz.
2. Önceki adımda oluşturduğunuz sanal makinede uzaktan oturum açın.
3. Sanal makinenin içinde bir komut penceresi açın (Win + R ve ardından `cmd` yazın).
4. Komut isteminde aşağıdaki cmd’yi çalıştırın.

    `nslookup windows.net`
5. `nslookup` başarısız olursa İnternet bağlantısı sorunu sanal cihazın StorSimple Yöneticisi hizmetine kaydedilmesini önlüyordur.
6. Sanal cihazın "windows.net" gibi Azure sitelerine erişebildiğinden emin olmak için sanal ağınızda gerekli değişiklikleri yapın.

## <a name="next-steps"></a>Sonraki adımlar
* [Sanal cihazı yönetmek için StorSimple Yöneticisi hizmetini kullanma](storsimple-manager-service-administration.md)yı öğrenin.
* [Bir yedeklemek kümesinden StorSimple birimini geri yükleme](storsimple-restore-from-backup-set.md)yi öğrenin.

