---
title: StorSimple Cloud Appliance Güncelleştirme 3| Microsoft Docs
description: Microsoft Azure sanal ağında StorSimple Cloud Appliance oluşturmayı, dağıtmayı ve yönetmeyi öğrenin. (StorSimple Güncelleştirme 3 ve üstü için geçerlidir).
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2017
ms.author: alkohli
ms.openlocfilehash: 01ce952ea774ba852c83d0d6aa3fe38d5dfd677e
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965723"
---
# <a name="deploy-and-manage-a-storsimple-cloud-appliance-in-azure-update-3-and-later"></a>Azure’da StorSimple Cloud Appliance dağıtma ve yönetme (StorSimple Güncelleştirme 3 ve üstü)

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Genel Bakış

StorSimple 8000 Series Cloud Appliance, Microsoft Azure StorSimple çözümünüzle birlikte gelen ek bir özelliktir. StorSimple Cloud Appliance, Microsoft Azure sanal ağındaki bir sanal makinede çalışır ve bunu ana bilgisayarlarınızdaki verileri yedeklemek ve kopyalamak için kullanabilirsiniz.

Bu makalede, Azure’da StorSimple Cloud Appliance dağıtma ve yönetme işlemi adım adım açıklanmaktadır. Bu makaleyi okuduktan sonra şunları yapabilir olacaksınız:

* Bulut gerecinin fiziksel cihazdan farkını anlama.
* Bulut gerecini oluşturabilme ve yapılandırabilme.
* Bulut gerecine bağlanma.
* Bulut gereciyle çalışmayı öğrenme.

Bu öğretici, Güncelleştirme 3 ve üstü sürümleri çalıştıran tüm StorSimple Cloud Appliance’lar için geçerlidir.

#### <a name="cloud-appliance-model-comparison"></a>Bulut gereci modeli karşılaştırması

StorSimple Cloud Appliance, standart 8010 (önceden 1100 olarak biliniyordu) ve premium 8020 (Güncelleştirme 2’de sunulmuştur) olmak üzere iki model seçeneğiyle kullanıma sunulmuştur. Aşağıdaki tabloda iki modelin karşılaştırması sunulmuştur.

| Cihaz modeli | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **Maksimum kapasite** |30 TB |64 TB |
| **Azure VM** |Standard_A3 (4 çekirdek, 7 GB bellek)| Standard_DS3 (4 çekirdek, 14 GB bellek)|
| **Bölge kullanılabilirliği** |Tüm Azure bölgeleri |Premium Depolama ve DS3 Azure VM’lerini destekleyen Azure bölgeleri<br></br>Bölgenizde hem **Sanal Makineler > DS serisi** hem de **Depolama > Disk depolamanın** mevcut olup olmadığını görmek için [bu listeyi](https://azure.microsoft.com/regions/services/) kullanın. |
| **Depolama türü** |Yerel diskler için Azure Standard Storage kullanır.<br></br> [Standart Depolama hesabı oluşturmayı](../storage/common/storage-create-storage-account.md) öğrenin. |Yerel diskler için Azure Premium Depolama kullanır<sup>2</sup> <br></br> |
| **İş yükü kılavuzu** |Yedeklerden dosya alma öğe düzeyi |Bulut geliştirme ve test senaryoları <br></br>Düşük gecikme süreli ve daha yüksek performanslı iş yükleri<br></br>Olağanüstü durum kurtarma için ikincil cihaz |

<sup>1</sup> *Önceden 1100 olarak biliniyordu*.

<sup>2</sup> *Hem 8010 hem de 8020 bulut katmanı için Azure Standart Depolama kullanır. Tek fark cihazdaki yerel katmandadır*.

## <a name="how-the-cloud-appliance-differs-from-the-physical-device"></a>Bulut gerecinin fiziksel cihazdan farkı

StorSimple Cloud Appliance, Microsoft Azure Sanal Makinesi’nde tek düğümde çalışan StorSimple’ın yalnızca yazılım olan bir sürümüdür. Bulut gereci, fiziksel cihazınızın kullanılamadığı olağanüstü durum kurtarma senaryolarını destekler. Bulut gereci, yedeklemelerden öğe düzeyinde alma, şirket içi olağanüstü durum kurtarma ve bulut geliştirme ve test senaryolarında kullanılmaya uygundur.

#### <a name="differences-from-the-physical-device"></a>Fiziksel cihazdan farklar

Aşağıdaki tabloda StorSimple Cloud Appliance ile StorSimple fiziksel cihazı arasındaki bazı temel farklar gösterilmektedir.

|  | Fiziksel cihaz | Bulut gereci |
| --- | --- | --- |
| **Location** |Veri merkezinde yer alır. |Azure üzerinde çalışır. |
| **Ağ arabirimleri** |Altı ağ arabirimine sahiptir: Veri 5 üzerinden veri 0. |Yalnızca bir ağ arabirimine sahiptir: VERI 0. |
| **Kayıt** |İlk yapılandırma adımı sırasında kaydedilir. |Kayıt ayrı bir görevdir. |
| **Hizmeti verileri şifreleme anahtarı** |Fiziksel cihazda yeniden üretin ve ardından yeni anahtarla bulut gerecini güncelleştirin. |Bulut gerecinden yeniden üretemezsiniz. |
| **Desteklenen birim türleri** |Hem yerel olarak sabitlenmiş hem de katmanlı birimleri destekler. |Yalnızca katmanlı birimleri destekler. |

## <a name="prerequisites-for-the-cloud-appliance"></a>Bulut gereci için önkoşullar

Aşağıdaki bölümlerde, StorSimple Cloud Appliance’ınız için yapılandırma önkoşulları açıklanmaktadır. Bir bulut gereci dağıtmadan önce, bulut gereci kullanımında güvenlik açısından dikkat edilecek noktaları gözden geçirin.

[!INCLUDE [StorSimple Cloud Appliance security](../../includes/storsimple-8000-cloud-appliance-security.md)]

#### <a name="azure-requirements"></a>Azure gereksinimleri

Bulut gereci sağlamadan önce, Azure ortamınızda aşağıdaki hazırlıkları yapmanız gerekir:

* Veri merkezinizde bir StorSimple 8000 serisi fiziksel cihazının (model 8100 veya 8600) dağıtıldığından ve çalıştırıldığından emin olun. Bu cihazı StorSimple Cloud Appliance oluşturmayı planladığınız aynı StorSimple Cihaz Yöneticisi hizmetiyle kaydedin.
* Bulut gereci için, [Azure üzerinde bir sanal ağ yapılandırın](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Premium Storage kullanıyorsanız, Premium Storage’ı destekleyen bir Azure bölgesinde sanal ağ oluşturmanız gerekir. Premium Depolama bölgeleri, [Bölgeye Göre Azure Hizmetleri listesinde](https://azure.microsoft.com/regions/services/) Disk depolama satırına karşılık gelen bölgelerdir.
* Kendi DNS sunucu adınızı belirtmek yerine Azure tarafından sağlanan varsayılan DNS sunucusunu kullanmanızı öneririz. DNS sunucusu adınız geçerli değilse veya DNS sunucusu IP adreslerini doğru çözümleyemiyorsa, bulut gerecini oluşturma işlemi başarısız olur.
* Noktadan siteye ve siteden siteye isteğe bağlıdır, ancak gerekli değildir. İsterseniz, daha gelişmiş senaryolar için bu seçenekleri yapılandırabilirsiniz.
* Bulut gereci tarafından sunulan birimleri kullanabileceğiniz sanal ağda [Azure Sanal Makineleri](../virtual-machines/virtual-machines-windows-quick-create-portal.md) (barındırma sunucuları) oluşturabilirsiniz. Bu sunucular aşağıdaki gereksinimleri karşılamalıdır:

  * iSCSI Initiator yazılımı yüklü Windows veya Linux sanal makineleri olmalıdır.
  * Bulut gereciyle aynı sanal ağda çalışıyor olmalıdır.
  * Bulut gerecinin iç IP adresi üzerinden bulut gerecinin iSCSI hedefine bağlanabilir olmalıdır.
  * Aynı sanal ağda iSCSI ve bulut trafiği için desteği yapılandırdığınızdan emin olun.

#### <a name="storsimple-requirements"></a>StorSimple gereksinimleri

Bulut gereci oluşturmadan önce, StorSimple Cihaz Yöneticisi hizmetinize aşağıdaki güncelleştirmeleri uygulayın:

* Bulut gerecinizin barındırma sunucuları olacak sanal makineler için [erişim denetimi kayıtları](storsimple-8000-manage-acrs.md) ekleyin.
* Bulut gereciyle aynı bölgedeki bir [depolama hesabını](storsimple-8000-manage-storage-accounts.md#add-a-storage-account) kullanın. Farklı bölgelerdeki Depolama hesapları performansın düşmesine neden olabilir. Bulut gereciyle Standart veya Premium Depolama hesabı kullanabilirsiniz. [Standart depolama hesabı](../storage/common/storage-create-storage-account.md)oluşturma hakkında daha fazla bilgi.
* Bulut gereci oluşturma işlemi için, verileriniz için kullanılandan farklı bir depolama hesabı kullanın. Aynı depolama hesabı kullanmak performansın düşmesine neden olabilir.

Başlamadan önce aşağıdaki bilgilere sahip olduğunuzdan emin olun:

* Azure portalı hesabınıza erişim kimlik bilgileri.
* StorSimple Cihaz Yöneticisi hizmetine kayıtlı fiziksel cihazınızdan alınan hizmet veri şifreleme anahtarının bir kopyası.

## <a name="create-and-configure-the-cloud-appliance"></a>Bulut gerecini oluşturma ve yapılandırma

Bu yordamları gerçekleştirmeden önce, [Bulut gereci önkoşullarını](#prerequisites-for-the-cloud-appliance) karşıladığınızdan emin olun.

StorSimple Cloud Appliance oluşturmak için aşağıdaki adımları gerçekleştirin.

### <a name="step-1-create-a-cloud-appliance"></a>1\. adım: Bulut gereci oluşturma

StorSimple Cloud Appliance’ı oluşturmak için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [Create a cloud appliance](../../includes/storsimple-8000-create-cloud-appliance-u2.md)]

Bu adımda bulut gereci oluşturulamazsa İnternet bağlantınız olmayabilir. Daha fazla bilgi edinmek için bulut gereci oluştururken [İnternet bağlantısı sorunlarını giderme](#troubleshoot-internet-connectivity-errors) bölümüne gidin.

### <a name="step-2-configure-and-register-the-cloud-appliance"></a>2\. adım: Bulut gerecini yapılandırma ve kaydetme

Bu yordama başlamadan önce, hizmet veri şifreleme anahtarının bir kopyasına sahip olduğunuzdan emin olun. Hizmet veri şifreleme anahtarı, StorSimple Cihaz Yöneticisi hizmetine ilk StorSimple fiziksel cihazınızı kaydettiğinizde oluşturulur. Bu anahtarı güvenli bir konumda saklamanız söylenmişti. Bir hizmeti verilerini şifreleme anahtarının bir kopyası sizde yoksa, yardım için Microsoft Destek’e başvurmanız gerekir.

StorSimple Cloud Appliance’ınızı yapılandırmak ve kaydetmek için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [Configure and register a cloud appliance](../../includes/storsimple-8000-configure-register-cloud-appliance.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>3\. adım: Seçim Cihaz yapılandırma ayarlarını değiştir

Aşağıdaki bölümde, CHAP, StorSimple Snapshot Manager kullanmak ya da cihaz yöneticisi parolasını değiştirmek istiyorsanız, StorSimple Cloud Appliance için gereken cihaz yapılandırma ayarları açıklanmaktadır.

#### <a name="configure-the-chap-initiator"></a>CHAP başlatıcısını yapılandırma

Bu parametre bulut gerecinizin (hedef), birimlere erişmeye çalışan başlatıcılardan (sunucu) beklediği kimlik bilgilerini içerir. Başlatıcılar, bu kimlik doğrulaması sırasında cihazınıza kendilerini tanıtmak için CHAP kullanıcı adı ve CHAP parolası sağlar. Ayrıntılı adımlar için [Cihazınız için CHAP yapılandırma](storsimple-8000-configure-chap.md#unidirectional-or-one-way-authentication)’ya gidin.

#### <a name="configure-the-chap-target"></a>CHAP hedefini yapılandırma

Bu parametre, CHAP özellikli başlatıcı karşılıklı veya çift yönlü kimlik doğrulaması istediğinde bulut gerecinizin kullandığı kimlik bilgilerini içerir. Bulut gereciniz, bu kimlik doğrulama işlemi sırasında kendisini başlatıcıya tanıtmak için Ters CHAP kullanıcı adı ve Ters CHAP parolası kullanır.

> [!NOTE]
> CHAP hedefi ayarları genel ayarlardır. Bu ayarlar uygulandığında, bulut gerecine bağlı olan tüm birimler CHAP kimlik doğrulamasını kullanır.

Ayrıntılı adımlar için [Cihazınız için CHAP yapılandırma](storsimple-8000-configure-chap.md#bidirectional-or-mutual-authentication)’ya gidin.

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager parolasını yapılandırma

StorSimple Snapshot Manager yazılımı Windows ana bilgisayarınıza bulunur ve yöneticilerin yerel ve bulut anlık görüntüleri biçiminde StorSimple cihazınızın yedeklerini yönetmelerine olanak tanır.

> [!NOTE]
> Bulut gereci için, Windows ana bilgisayarınız bir Azure sanal makinesidir.

StorSimple Snapshot Manager’da bir cihazı yapılandırdığınız sırada, depolama cihazınızın kimlik doğrulaması için StorSimple cihazı IP adresi ve parolasını sağlamanız istenir. Ayrıntılı adımlar için [StorSimple Snapshot Manager parolasını yapılandırma](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)’ya gidin.

#### <a name="change-the-device-administrator-password"></a>Cihaz yöneticisi parolasını değiştirme

Bulut gerecine erişmek için Windows PowerShell arabirimini kullandığınızda, bir cihaz yöneticisi parolası girmeniz gerekir. Verilerinizin güvenliği açısından, bulut gerecinin kullanılabilmesi için önce bu parolayı değiştirmelisiniz. Ayrıntılı adımlar için [Cihaz yöneticisi parolasını yapılandırma](../storsimple/storsimple-8000-change-passwords.md#change-the-device-administrator-password)’ya gidin.

## <a name="connect-remotely-to-the-cloud-appliance"></a>Bulut gerecine uzaktan bağlanma

Windows PowerShell arabirimi üzerinden bulut gerecinize uzaktan erişim varsayılan olarak etkin değildir. Önce bulut gerecinde, sonra da bulut gerecine erişmek için kullanılan istemcide uzaktan yönetimi etkinleştirmeniz gerekir.

Aşağıdaki iki adımlı yordamda bulut gerecinize uzaktan nasıl bağlanabileceğiniz açıklanmıştır.

### <a name="step-1-configure-remote-management"></a>1\. adım: Uzaktan yönetimi yapılandırma

StorSimple Cloud Appliance’ınız için uzaktan yönetimi yapılandırmak üzere aşağıdaki adımları gerçekleştirin.

[!INCLUDE [Configure remote management via HTTP for cloud appliance](../../includes/storsimple-8000-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-cloud-appliance"></a>2\. adım: Bulut gerecine uzaktan erişin

Bulut gerecinde uzaktan yönetimi etkinleştirdikten sonra, aynı sanal ağ içindeki farklı bir sanal makineden gerece bağlanmak için Windows PowerShell uzaktan iletişimini kullanın. Örneğin, iSCSI’yı bağlamak için yapılandırdığınız ve kullandığınız ana bilgisayar sanal makinesinden bağlanabilirsiniz. Çoğu dağıtımda, bulut gerecine erişmek için kullanabileceğiniz ana bilgisayar sanal makinenize erişim için ortak bir uç nokta açarsınız.

> [!WARNING]
> **Gelişmiş güvenlik için, uç noktalara bağlanırken HTTPS kullanmanız ve ardından PowerShell uzak oturumunuz tamamladıktan sonra uç noktaları silmeniz önerilir.**

Bulut gereciniz için uzaktan iletişim ayarlamak amacıyla [StorSimple cihazınıza uzaktan bağlanma](storsimple-8000-remote-connect.md) yordamlarını izlemelisiniz.

## <a name="connect-directly-to-the-cloud-appliance"></a>Bulut gerecine doğrudan bağlanma

Bulut gerecine doğrudan da bağlanabilirsiniz. Bulut gerecine sanal ağ dışındaki veya Microsoft Azure ortamı dışındaki başka bir bilgisayardan doğrudan bağlanmak için ek uç noktalar oluşturmanız gerekir.

Bulut gerecinde ortak uç nokta oluşturmak için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [Create public endpoints on a cloud appliance](../../includes/storsimple-8000-create-public-endpoints-cloud-appliance.md)]

Bu uygulama sanla ağınızdaki ortak uç noktaların sayısını en aza indireceğinden, aynı sanal ağdaki başka bir sanal makineden bağlanmanızı öneririz. Bu durumda, Uzak Masaüstü oturumu aracılığıyla sanal makineye bağlanın ve yerel ağdaki başka bir Windows istemcisinde olduğu gibi, sanal makineyi kullanım için yapılandırın. Bağlantı noktası zaten biliniyor olduğundan, ortak bağlantı noktası numarasını eklemeniz gerekmez.

## <a name="get-private-ip-for-the-cloud-appliance"></a>Bulut gereci özel IP alma

Bulut gerecinin aynı sanal ağ içindeki konak sunucusuna bağlanması için bulut gerecinin iç veya özel IP adresi gerekir. Bulut gerecinin özel IP adresini almak için aşağıdaki adımları uygulayın

1. Bulut gereciniz için temel alınan sanal makineye gidin. Sanal makine, bulut gerecinizle aynı ada sahiptir. **Tüm kaynaklar**’a gidin, bulut gerecinin ve aboneliğin adını belirtin ve sanal makine türünü seçin. Sunulan sanal makinelerin listesinde, bulut gerecine karşılık gelen sanal makineyi seçin veya tıklayın.

     ![Bulut gereciniz için sanal makine seçme](./media/storsimple-8000-cloud-appliance-u2/sca-vm.png)

2. **Ayarlar > Ağ**’a gidin. Sağ bölmede bulut gerecinin özel IP adresini görüsünüz. Not alın.

    ![Bulut gereciniz için özel IP adresi alma](./media/storsimple-8000-cloud-appliance-u2/sca-private-ip-vm-networking.png)

## <a name="work-with-the-storsimple-cloud-appliance"></a>StorSimple Cloud Appliance ile çalışma

Artık StorSimple Cloud Appliance’ınızı oluşturduğunuza ve yapılandırdığınıza göre, bununla çalışmaya başlamaya hazırsınız. Bulut gereçlerinde, tıpkı fiziksel bir StorSimple cihazında olduğu gibi birim kapsayıcıları, birimler ve yedekleme ilkeleriyle çalışabilirsiniz. Aralarındaki tek fark, cihaz listenizden bulut gerecinizi seçtiğinizden emin olmanızın gerekmesidir. Bulut gerecine ilişkin çeşitli yönetim görevleri hakkındaki adım adım yordamlar için [StorSimple Cihaz Yöneticisi hizmetini kullanarak bulut gerecini yönetme](storsimple-8000-manager-service-administration.md) konusuna başvurun.

Aşağıdaki bölümlerde, bulut gereciyle çalışırken karşınıza çıkan farklılıklar açıklanmaktadır.

### <a name="maintain-a-storsimple-cloud-appliance"></a>StorSimple Cloud Appliance’ın bakımını yapma

Bu gereç yalnızca yazılım olan bir cihaz olduğundan, fiziksel cihaz ile karşılaştırıldığında bulut gereci çok daha az bakım gerektirir.

Bulut gerecini güncelleştiremezsiniz. Yeni bulut gereci oluşturmak için yazılımın en son sürümünü kullanın.


### <a name="storage-accounts-for-a-cloud-appliance"></a>Bulut gereci için depolama hesapları

StorSimple Cihaz Yöneticisi hizmeti, bulut gereci ve fiziksel cihaz tarafından kullanılmak üzere depolama hesapları oluşturulur. Depolama hesaplarınızı oluştururken kolay adda bir bölge tanımlayıcısı kullanmanızı öneririz. Bu, tüm sistem bileşenlerinde bölge tutarlılığının sağlanmasına yardımcı olur. Bir bulut gereci için, performans sorunlarını önlemek amacıyla tüm bileşenlerin aynı bölgede olması önemlidir.

Adım adım bir yordam için [depolama hesabı ekleme](storsimple-8000-manage-storage-accounts.md#add-a-storage-account)’ye gidin.

### <a name="deactivate-a-storsimple-cloud-appliance"></a>StorSimple Cloud Appliance’ı devre dışı bırakma

Bir bulut gerecini devre dışı bırakma eylemi, gereç sağlanırken oluşturulan sanal makineyi ve kaynakları siler. Bulut gereci devre dışı bırakıldıktan sonra, önceki durumuna geri yüklenemez. Bulut gerecini devre dışı bırakmadan önce, buna bağlı istemcileri ve ana bilgisayarları durdurduğunuzdan ya da sildiğinizden emin olun.

Bulut gerecini devre dışı bırakmak aşağıdaki eylemlere neden olur:

* Bulut gereci kaldırılır.
* Bulut gereci için oluşturulan işletim sistemi diski ve veri diskleri kaldırılır.
* Hazırlama sırasında oluşturulan barındırılan hizmet ve sanal ağ korunur. Bunları kullanmıyorsanız, bunları el ile silmeniz gerekir.
* Bulut gereci için oluşturulan bulut anlık görüntüleri korunur.

Adım adım yordam için [StorSimple cihazınızı devre dışı bırakma ve silme](storsimple-8000-deactivate-and-delete-device.md)’ye gidin.

StorSimple Cihaz Yöneticisi hizmet dikey penceresinde bulut gereci devre dışı olarak görünmeye başladıktan sonra, **Cihazlar** dikey penceresindeki cihaz listesinden bulut gerecini silebilirsiniz.

### <a name="start-stop-and-restart-a-cloud-appliance"></a>Bulut gerecini başlatma, durdurma ve yeniden başlatma
StorSimple fiziksel cihazının aksine, StorSimple Cloud Appliance’ta basılabilen bir güç açma veya güç kapatma düğmesi yoktur. Ancak, bulut gerecini durdurmanız ve yeniden başlatmanız gereken durumlar olabilir.

Bulut gerecini başlatma, durdurma ve yeniden başlatmanın en kolay yolu, Sanal Makineler hizmet dikey penceresini kullanmaktır. Sanal makine hizmetine gidin. VM listesinden bulut gerecinize karşılık gelen VM’yi (aynı ada sahiptir) bulun ve VM adına tıklayın. Bulut gereci oluşturulduktan sonra varsayılan olarak başlatıldığından, sanal makine dikey pencerenize baktığınızda gerecin durumu **Çalışıyor** şeklinde olur. Bir sanal makineyi istediğiniz zaman başlatabilir, durdurabilir veya yeniden başlatabilirsiniz.

[!INCLUDE [Stop and restart cloud appliance](../../includes/storsimple-8000-stop-restart-cloud-appliance.md)]

### <a name="reset-to-factory-defaults"></a>Fabrika ayarlarına sıfırlama
Bulut gerecinizi baştan başlatmak istediğinize karar verirseniz, gereci devre dışı bırakıp silmeniz ve ardından yeni bir gereç oluşturmanız yeterlidir.

## <a name="fail-over-to-the-cloud-appliance"></a>Bulut gerecine yük devretme
Olağanüstü durum kurtarma (DR), StorSimple Cloud Appliance’ın tasarlanma ana senaryolarından biridir. Bu senaryoda, fiziksel StorSimple cihazı veya veri merkezinin tamamı kullanılamayabilir. Neyse ki, işlemleri farklı bir konuma geri yüklemek için bulut gerecini kullanabilirsiniz. DR sırasında kaynak cihazdaki birim kapsayıcıların sahipliği değişir ve bulut gerecine aktarılır.

DR önkoşulları şunlardır:

* Bulut gereci oluşturulmalı ve yapılandırılmalı.
* Birim kapsayıcısındaki tüm birimler çevrimdışı olmalı.
* Yükünü devrettiğiniz birim kapsayıcısıyla ilişkili bir bulut anlık görüntüsü olmalı.

> [!NOTE]
> * Bulut gerecini DR için ikincil cihaz olarak kullanırken, 8010’nun 30 TB Standard Depolama ve 8020’nin 64 TB Premium Depolama içerdiğini unutmayın. DR senaryosu için daha yüksek kapasiteli olan 8020 bulut gereci daha uygun olabilir.

Adım adım bir yordam için [bulut gerecine yük devretme](storsimple-8000-device-failover-cloud-appliance.md) konusuna gidin.

## <a name="delete-the-cloud-appliance"></a>Bulut gerecini silme
Önceden bir StorSimple Cloud Appliance yapılandırmış ve kullanmış ancak şimdi bunun kullanımı için ücret tahakkuk etmesini durdurmak istiyorsanız, bulut gerecini durdurmanız gerekir. Bulut gereci durdurulduğunda VM serbest bırakılır. Bu eylem, aboneliğinizde ücret tahakkuk etmesini engeller. Ne var ki, işletim sistemi ve veri diskleri için uygulanan depolama ücretleri devam eder.

Tüm ücretleri durdurmak için bulut gerecini silmeniz gerekir. Bulut gereci tarafından oluşturulan yedekleri silmek için cihazı devre dışı bırakabilir veya silebilirsiniz. Daha fazla bilgi için bkz. [StorSimple cihazını devre dışı bırakma ve silme](storsimple-8000-deactivate-and-delete-device.md).

[!INCLUDE [Delete a cloud appliance](../../includes/storsimple-8000-delete-cloud-appliance.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>İnternet bağlantısı sorunlarını giderme
Bulut gereci oluşturulduğu sırada İnternet bağlantısı yoksa oluşturma adımı başarısız olur. İnternet bağlantısı hatalarında sorun gidermek için Azure portalında aşağıdaki adımları izleyin:

1. [Azure portalda Windows sanal makinesi oluşturun](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal). Bu sanal makine, bulut gerecinizle aynı depolama hesabı, sanal ağ ve alt ağı kullanmalıdır. Azure’da aynı depolama hesabını, sanal ağı ve alt ağı kullanan mevcut bir Windows Server ana bilgisayarı varsa, İnternet bağlantısı sorunlarını gidermek için onu da kullanabilirsiniz.
2. Önceki adımda oluşturduğunuz sanal makinede uzaktan oturum açın.
3. Sanal makinenin içinde bir komut penceresi açın (Win + R ve ardından `cmd` yazın).
4. Komut isteminde aşağıdaki cmd’yi çalıştırın.

    `nslookup windows.net`
5. `nslookup` başarısız olursa, İnternet bağlantısı sorunu bulut gerecinin StorSimple Cihaz Yöneticisi hizmetine kaydedilmesini önlüyordur.
6. Bulut gerecinin _windows.net_ gibi Azure sitelerine erişebildiğinden emin olmak için sanal ağınızda gerekli değişiklikleri yapın.

## <a name="next-steps"></a>Sonraki adımlar
* [StorSimple Cihaz Yöneticisi hizmetini kullanarak bulut gereci yönetme](storsimple-8000-manager-service-administration.md)yi öğrenin.
* [Bir yedeklemek kümesinden StorSimple birimini geri yükleme](storsimple-8000-restore-from-backup-set-u2.md)yi öğrenin.
