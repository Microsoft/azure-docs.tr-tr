---
title: Aracı tabanlı Azure geçişi sunucu geçişine sahip VMware VM 'lerini geçirme
description: Azure geçişi ile VMware VM 'lerinin aracı tabanlı geçişini çalıştırmayı öğrenin.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: 60b58f7cf67a22e019ff186e4e1811ff5b001d84
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714461"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>VMware VM 'lerini Azure 'a geçirme (aracı tabanlı)

Bu makalede, şirket içi VMware VM 'lerini, [Azure geçişi: sunucu geçiş](migrate-services-overview.md#azure-migrate-server-migration-tool) aracı ile aracı tabanlı geçişle kullanarak Azure 'a nasıl geçirebileceğiniz gösterilmektedir.  Ayrıca, aynı zamanda aracısız geçiş kullanarak VMware VM 'Leri geçirebilirsiniz. Yöntemleri [karşılaştırın](server-migrate-overview.md#compare-migration-methods) .


 Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Azure 'u Azure geçişi ile çalışacak şekilde hazırlayın.
> * Aracı tabanlı geçişe hazırlanın. Azure geçişi 'nin geçiş için makineleri bulabilmesi için bir VMware hesabı ayarlayın. Mobility hizmeti aracısının geçirmek istediğiniz makinelere yükleyebilmesi için bir hesap ayarlayın ve bir makineyi çoğaltma gereci olarak davranacak şekilde hazırlayın.
> * Azure geçişi: sunucu geçiş aracı ekleme
> * Çoğaltma gerecini ayarlayın.
> * VM 'Leri çoğaltın.
> * Her şeyin beklendiği gibi çalıştığından emin olmak için bir test geçişi çalıştırın.
> * Azure 'a tam geçiş gerçekleştirin.

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir, böylece bir kavram kanıtı hızlı bir şekilde ayarlayabilmenizi sağlayabilirsiniz. Öğreticiler mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce, VMware Aracısı tabanlı geçiş mimarisini [gözden geçirin](./agent-based-migration-architecture.md) .

## <a name="prepare-azure"></a>Azure’u hazırlama

Azure 'u aracı tabanlı geçişe hazırlamak için tablodaki görevleri doldurun.

**Görev** | **Ayrıntılar**
--- | ---
**Azure Geçişi projesi oluşturma** | Azure hesabınızın bir proje oluşturmak için katkıda bulunan veya sahip izinlerinin olması gerekir.
**Azure hesabı izinlerini doğrulama** | Azure hesabınızın bir VM oluşturmak ve Azure yönetilen diskine yazmak için izinleri olması gerekir.
**Azure ağı ayarlama** | Azure VM 'lerinin geçişten sonra katılalacağı bir ağ kurun.

### <a name="assign-permissions-to-create-project"></a>Proje oluşturmak için izin atama
Azure geçişi projeniz yoksa, bir tane oluşturmak için izinleri doğrulayın.


1. Azure portalında ilgili aboneliği açın ve **Erişim denetimi (IAM)** öğesini seçin.
2. **Erişimi denetle**' de ilgili hesabı bulun ve izinleri görüntülemek için tıklatın.
3. **Katkıda** bulunan veya **sahip** izninizin olduğunu doğrulayın.

    - Ücretsiz Azure hesabı oluşturduysanız aboneliğinizin sahibi siz olursunuz.
    - Aboneliğin sahibi siz değilseniz sahibiyle iletişime geçerek gerekli rolün atanmasını sağlayın.
    
### <a name="assign-azure-account-permissions"></a>Azure hesabı izinleri atama

Şu izinlere sahip olmanız için, sanal makine katılımcısı rolünü hesaba atayın:

- Seçilen kaynak grubunda sanal makine oluşturma.
- Seçilen sanal ağda sanal makine oluşturma.
- Azure yönetilen diskine yazın. 


### <a name="set-up-an-azure-network"></a>Azure ağı ayarlama

[Bir Azure ağı kurun](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Şirket içi makineler Azure yönetilen disklere çoğaltılır. Geçiş için Azure 'a yük devretmek, Azure VM 'Leri bu yönetilen disklerden oluşturulur ve ayarladığınız Azure ağı 'na eklenir.

## <a name="prepare-for-migration"></a>Geçiş için hazırlanma

Destek gereksinimlerini ve izinlerini doğrulayın ve bir çoğaltma gereci dağıtmaya hazırlanın. 

### <a name="prepare-an-account-to-discover-vms"></a>VM 'Leri bulmaya yönelik bir hesap hazırlayın

Azure geçişi sunucusu geçişi, geçirmek istediğiniz VM 'Leri bulması için VMware sunucularına erişmesi gerekir. Hesabı aşağıdaki gibi oluşturun:

1. Ayrılmış bir hesap kullanmak için, rolü vCenter düzeyinde oluşturun. Role **Azure_Migrate** gibi bir ad verin.
2. Role aşağıdaki tabloda özetlenen izinleri atayın.
3. vCenter sunucusu veya vSphere ana bilgisayarında bir kullanıcı oluşturun. Rolü kullanıcıya atayın.

#### <a name="vmware-account-permissions"></a>VMware hesap izinleri

**Görev** | **Rol/İzinler** | **Ayrıntılar**
--- | --- | ---
**VM bulma** | En az bir salt okunur kullanıcı<br/><br/> Veri Merkezi nesnesi –> Alt Nesneye Yay, role=Read-only | Kullanıcı veri merkezi düzeyinde atandı ve bu veri merkezindeki tüm nesnelere erişimi var.<br/><br/> Erişimi kısıtlamak için, **alt nesneye yay** ile **hiçbir erişim** rolünü alt nesnelere (vSphere Konakları, veri depoları, VM 'ler ve ağlar) atayın.
**Çoğaltma** |  Gerekli izinlere sahip bir rol (Azure_Site_Recovery) oluşturup rolü VMware kullanıcısı veya grubuna atayın<br/><br/> Veri Merkezi nesnesi –> Alt Nesneye Yay, role=Azure_Site_Recovery<br/><br/> Veri deposu -> Alan ayırma, veri deposuna göz atma, düşük düzeyli dosya işlemleri, dosyayı kaldırma, sanal makine dosyalarını güncelleştirme<br/><br/> Ağ -> Ağ ataması<br/><br/> Kaynak -> VM’yi kaynak havuzuna atama, kapalı VM’yi geçirme, açık VM’yi geçirme<br/><br/> Görevler -> Görev oluşturma, görevi güncelleştirme<br/><br/> Sanal makine -> Yapılandırma<br/><br/> Sanal makine -> Etkileşim -> soruyu yanıtlama, cihaz bağlantısı, CD ortamını yapılandırma, disket ortamını yapılandırma, kapatma, açma, VMware araçlarını yükleme<br/><br/> Sanal makine -> Envanter -> Oluşturma, kaydetme, kaydı kaldırma<br/><br/> Sanal makine -> Sağlama -> Sanal makine indirmeye izin verme, Sanal makine dosyalarını karşıya yüklemeye izin verme<br/><br/> Sanal makine -> Anlık görüntüler -> Anlık görüntüleri kaldırma | Kullanıcı veri merkezi düzeyinde atandı ve bu veri merkezindeki tüm nesnelere erişimi var.<br/><br/> Erişimi kısıtlamak için, **alt nesneye yay** ile **hiçbir erişim** rolünü alt nesnelere (vSphere Konakları, veri depoları, VM 'ler ve ağlar) atayın.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Bir hesabı Mobility hizmeti yüklemesi için hazırlama

Çoğaltmak istediğiniz makinelerde Mobility hizmeti yüklü olmalıdır.

- Azure geçişi çoğaltma gereci, bir makine için çoğaltmayı etkinleştirdiğinizde bu hizmetin göndererek yüklemesini gerçekleştirebilir veya el ile veya yükleme araçlarını kullanarak yükleyebilirsiniz.
- Bu öğreticide, Mobility hizmetini göndererek yükleme yoluyla yükleyeceğiz.
- Anında yükleme için, Azure geçişi sunucu geçişinin sanal makineye erişmek için kullanabileceği bir hesap hazırlamanız gerekir. Bu hesap, Mobility hizmetini el ile yüklememeniz durumunda yalnızca gönderme yüklemesi için kullanılır.

Hesabı aşağıdaki gibi hazırlayın:

1. VM üzerinde yükleme izinleri ile bir etki alanı veya yerel hesap hazırlayın.
2. Windows VM 'Ler için, bir etki alanı hesabı kullanmıyorsanız, kayıt defterinde bir değere sahip **LocalAccountTokenFilterPolicy** DWORD girişini ekleyerek yerel makinede uzak kullanıcı erişim denetimini devre dışı bırakın **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. Linux VM 'Ler için kaynak Linux sunucusunda bir kök hesap hazırlayın.


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Çoğaltma gereci için bir makine hazırlama

Gereç, makineleri Azure 'a çoğaltma için kullanılır. Gereç, bu bileşenleri barındıran tek, yüksek oranda kullanılabilir ve şirket içi VMware VM 'dir:

- **Yapılandırma sunucusu**: yapılandırma sunucusu, şirket Içi ve Azure arasındaki iletişimleri koordine eder ve veri çoğaltmasını yönetir.
- **İşlem sunucusu**: işlem sunucusu bir çoğaltma ağ geçidi olarak davranır. Çoğaltma verilerini alır; önbelleğe alma, sıkıştırma ve şifreleme ile en iyi duruma getirir ve Azure 'da bir önbellek depolama hesabına gönderir. İşlem sunucusu Ayrıca, çoğaltmak istediğiniz VM 'Lere Mobility hizmeti Aracısı 'nı da yükleyip şirket içi VMware VM 'lerinin otomatik olarak bulunmasını gerçekleştirir.

Gereç için aşağıdaki gibi hazırlanın:

- [Gereç gereksinimlerini gözden geçirin](migrate-replication-appliance.md#appliance-requirements). Genellikle, indirilen bir OVA dosyasını kullanarak çoğaltma gereci bir VMware VM 'si ayarlarsınız. Şablon, tüm gereksinimlere uyan bir gereç oluşturur.
- MySQL 'in gereç üzerinde yüklü olması gerekir. Yükleme yöntemlerini [gözden geçirin](migrate-replication-appliance.md#mysql-installation) .
- [Ortak bulut URL 'lerini](migrate-replication-appliance.md#url-access)ve gereç makinesinin erişmesi gereken [Azure Kamu URL 'lerini](migrate-replication-appliance.md#azure-government-url-access) gözden geçirin.
- Çoğaltma gereç makinesinin erişmesi gereken [bağlantı noktalarını gözden geçirin](migrate-replication-appliance.md#port-access) .



### <a name="check-vmware-requirements"></a>VMware gereksinimlerini denetleme

VMware sunucularının ve VM 'lerinin Azure 'a geçiş gereksinimleriyle uyumlu olduğundan emin olun. 

1. VMware sunucusu gereksinimlerini [doğrulayın](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based).
2. [Doğrula](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) Geçiş için VM gereksinimleri.
3. Azure ayarlarını doğrulayın. Azure 'a çoğaltılan şirket içi VM 'Ler, [Azure VM gereksinimleriyle](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)uyumlu olmalıdır.
4. Azure 'a geçirmeden önce VM 'lerde gereken bazı değişiklikler vardır.
    - Geçişe başlamadan önce bu değişiklikleri yapmak önemlidir. Değişikliği yapmadan önce VM 'yi geçirirseniz, VM Azure 'da önyüklenemeyebilir.
    - Yapmanız gereken [Windows](prepare-for-migration.md#windows-machines) ve [Linux](prepare-for-migration.md#linux-machines) değişikliklerini gözden geçirin.

> [!NOTE]
> Azure geçişi sunucu geçişi ile aracı tabanlı geçiş, Azure Site Recovery hizmetinin özelliklerine dayalıdır. Bazı gereksinimler Site Recovery belgelerine bağlantı verebilir.

## <a name="set-up-the-replication-appliance"></a>Çoğaltma gereç ayarı

Bu yordamda, gerecin indirilen bir açık sanallaştırma uygulaması (OVA) şablonuyla nasıl ayarlanacağı açıklanmaktadır. Bu yöntemi kullanamıyoruz, gereci [bir komut dosyası kullanarak](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance)ayarlayabilirsiniz. 

### <a name="download-the-replication-appliance-template"></a>Çoğaltma gereç şablonunu indirin

Şablonu şu şekilde indirin:

1. Azure geçişi projesinde, **geçiş hedefleri** altındaki **sunucular** ' a tıklayın.
2. **Azure geçişi-sunucular**  >  **Azure geçişi: sunucu geçişi** bölümünde **bul**' a tıklayın.

    ![VM'leri bulma](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. Makinelerde **bulunan makinelerde**  >  **makineler sanallaştırılmış mı?**, **VMware vSphere hiper yöneticiyle Evet**' e tıklayın.
4. **Nasıl geçiş** yapmak istiyorsunuz? bölümünde, **aracı tabanlı çoğaltmayı kullanma**' yı seçin.
5. **Hedef bölge**' de, makineleri geçirmek istediğiniz Azure bölgesini seçin.
6. **Geçiş için hedef bölgenin bölge adı olduğunu onaylayın**' i seçin.
7. **Kaynak oluştur**' a tıklayın. Bu, arka planda bir Azure Site Recovery Kasası oluşturur. Bu düğmeye tıkladıktan sonra bu proje için hedef bölgeyi değiştiremezsiniz ve sonraki tüm geçişler bu bölgeye yöneliktir.

    ![Kurtarma Hizmetleri kasası oluşturma](./media/tutorial-migrate-vmware-agent/create-resources.png)  

    > [!NOTE]
    > Oluşturulan Azure geçişi projesi için bağlantı yöntemi olarak özel uç nokta ' ı seçtiyseniz, kurtarma hizmetleri kasası da özel uç nokta bağlantısı için yapılandırılır. Çoğaltma gerecinden özel uç noktalara ulaşılabildiğinden emin olun: [ **daha fazla bilgi**](how-to-use-azure-migrate-with-private-endpoints.md#troubleshoot-network-connectivity)


8. **Yeni bir çoğaltma gereci yüklemek** istiyor musunuz?, **çoğaltma gereci yüklensin**' i seçin.
9. **İndir**’e tıklayın. Bu, bir OVF şablonu indirir.
    ![OVA 'yı indir](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Kaynak grubunun ve kurtarma hizmetleri kasasının adını aklınızda edin. Bu, Gereç dağıtımı sırasında gereklidir.


### <a name="import-the-template-in-vmware"></a>VMware’de şablonu içeri aktarma

OVF şablonunu indirdikten sonra, Windows Server 2016 çalıştıran bir VMware VM 'de çoğaltma uygulaması oluşturmak için onu VMware 'e içeri aktarırsınız.

1. VMware vSphere Istemcisiyle VMware vCenter sunucusunda veya vSphere ESXi konağında oturum açın.
2. **Dosya** menüsünde **OVF Şablonunu Dağıt** seçeneğini belirleyerek **OVF Şablonu Dağıtma Sihirbazı**’nı başlatın. 
3. **Kaynak Seç**' de indirilen ovf 'nin konumunu girin.
4. **Ayrıntıları gözden geçirin**' de **İleri**' yi seçin.
5. **Ad ve klasör seçin** ' de, yapılandırma ' yı **seçin** ve varsayılan ayarları kabul edin.
6. **Depolama Seç**' de  >  **sanal disk biçimi** Seç ' ın en iyi performans için, **kalın sağlama Eager sıfırlandı** seçeneğini belirleyin
7. Sihirbaz sayfalarının geri kalan kısmında varsayılan ayarları kabul edin.
8. **Tamamlanmaya hazır**' te, VM 'yi varsayılan ayarlarla ayarlamak için, **dağıtım bittikten sonra güç açma**' yı seçin  >  .

   > [!TIP]
   > Ek bir NIC eklemek istiyorsanız, dağıtım bittikten sonra açma   >  **işini** kaldırın. Varsayılan olarak, şablon tek bir NIC içerir. Dağıtımdan sonra daha fazla NIC ekleyebilirsiniz.

### <a name="start-appliance-setup"></a>Gereç kurulumunu Başlat

1. VMware vSphere Istemci konsolunda VM 'yi açın. VM’de Windows Server 2016 yükleme deneyimi önyüklemesi yapılır.
2. Lisans sözleşmesini kabul edin ve bir yönetici parolası girin.
3. Yükleme tamamlandıktan sonra, yönetici parolasını kullanarak VM 'de yönetici olarak oturum açın. İlk kez oturum açtığınızda, çoğaltma gereç Kurulum Aracı (Azure Site Recovery yapılandırma aracı) birkaç saniye içinde başlar.
5. Gereci sunucu geçişine kaydetmek için kullanılacak bir ad girin. Ardından **İleri**'ye tıklayın.
6. Araç, VM’nin Azure bağlanıp bağlanamadığını denetler. Bağlantı kurulduktan sonra Azure aboneliğinizde oturum açmak için **Oturum aç** seçeneğini belirleyin.
7. Aracın gereci tanımlamak için bir Azure AD uygulaması kaydını tamamlamasını bekleyin. Gereç yeniden başlatılır.
1. Makinede tekrar oturum açın. Birkaç saniye içinde, Yapılandırma Sunucusu Yönetim Sihirbazı otomatik olarak başlar.

### <a name="register-the-replication-appliance"></a>Çoğaltma gereç kaydını kaydetme

Çoğaltma gerecini ayarlamayı ve kaydetmeyi tamamlayın.

1. Gereç kurulumunda, **Kurulum bağlantısı**' nı seçin.
2. Çoğaltma gerecinin VM keşfi için kullandığı NIC 'yi (varsayılan olarak yalnızca bir NIC) seçin ve Mobility hizmetinin kaynak makinelerde anında yüklemesini yapın.
3. Çoğaltma gerecinin Azure ile bağlantı için kullandığı NIC 'ı seçin. Sonra **Kaydet**'i seçin. Yapılandırıldıktan sonra bu ayarı değiştiremezsiniz.
4. Gereç bir proxy sunucusunun arkasında bulunuyorsa, proxy ayarlarını belirtmeniz gerekir.
    - Proxy adını veya olarak belirtin **http://ip-address** **http://FQDN** . HTTPS proxy sunucuları desteklenmez.
5. Abonelik, kaynak grupları ve kasa ayrıntıları istendiğinde, Gereç şablonunu indirdiğinizde not ettiğiniz ayrıntıları ekleyin.
6. **Üçüncü taraf yazılımı yükleyin** bölümünde lisans sözleşmesini kabul edin. MySQL Server’ı yüklemek için **İndir ve Yükle** seçeneğini belirleyin.
7. **VMware PowerCLI’yi Yükle** seçeneğini belirleyin. Bunu yapmadan önce tüm tarayıcı pencerelerinin kapalı olduğundan emin olun. Daha sonra **Devam** seçeneğini belirleyin.
8. **Gereç yapılandırmasını doğrulama** bölümünde ön koşullar, siz devam etmeden önce doğrulanır.
9. **vCenter Server/vSphere ESXi sunucusu yapılandırma** bölümünde, çoğaltmak istediğiniz VM’lerin bulunduğu vCenter sunucusunun veya vSphere konağının FQDN’sini ya da IP adresini girin. Sunucunun dinleme gerçekleştirdiği bağlantı noktasını girin. Kasadaki VMware sunucusu için kullanılacak bir kolay ad girin.
10. VMware bulma için [oluşturduğunuz](#prepare-an-account-to-discover-vms) hesabın kimlik bilgilerini girin. **Ekle**  >  **devam et**' i seçin.
11. **Sanal makine kimlik bilgilerini Yapılandır**' da, VM 'ler için çoğaltmayı etkinleştirdiğinizde Mobility hizmetinin anında yüklenmesi için [oluşturduğunuz](#prepare-an-account-for-mobility-service-installation) kimlik bilgilerini girin.  
    - Windows makinelerinde hesap için, çoğaltmak istediğiniz makinelerde yerel yönetici ayrıcalıkları gerekir.
    - Linux’ta kök hesap için bilgileri sağlayın.
12. Kaydı tamamlamak için **Yapılandırmayı son haline getir** seçeneğini belirleyin.


Çoğaltma gereci kaydedildikten sonra, Azure geçişi sunucu değerlendirmesi, belirtilen ayarları kullanarak VMware sunucularına bağlanır ve VM 'Leri bulur. Keşfedilen   >  **öğeleri** Yönet bölümünde **diğer** sekmesinde bulunan VM 'leri görüntüleyebilirsiniz.



## <a name="replicate-vms"></a>Sanal makineleri çoğaltma

Geçiş için VM 'Leri seçin.

> [!NOTE]
> Portalda, çoğaltma için aynı anda en fazla 10 makine seçebilirsiniz. Daha fazla yineleme yapmanız gerekiyorsa, bunları 10 ' un toplu işleri halinde gruplayın.

1. Azure geçişi proje > **sunucularında** **Azure geçişi: sunucu geçişi**' nde **Çoğalt**' a tıklayın.

    ![Azure geçişi 'nde sunucular ekranının ekran görüntüsü. Çoğaltma düğmesi Azure geçişi: geçiş araçları altında sunucu geçişi ' nde seçilidir.](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. **Çoğalt**, > **Kaynak ayarları** > **Makineleriniz sanallaştırıldı mı?** bölümünde **Evet, VMware vSphere** ile seçeneğini belirleyin.
3. **Şirket içi gereç** bölümünde, ayarladığınız Azure geçiş gerecinin adını seçin.
4. **VCenter Server**'Da, VM 'Leri yöneten vCenter sunucusunun adını veya VM 'Lerin barındırıldığı vSphere sunucusunu belirtin.
5. **Işlem sunucusu**' nda, çoğaltma gerecinin adını seçin.
6. **Konuk kimlik bilgileri**' nde, Mobility hizmetinin göndererek yüklenmesi IÇIN kullanılacak VM yönetici hesabını belirtin. Ileri ' ye tıklayın **: sanal makineler**.

    ![Çoğaltma ekranındaki kaynak ayarları sekmesinin ekran görüntüsü. Konuk kimlik bilgileri alanı vurgulanır ve değer VM-admin-Account olarak ayarlanır.](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. **Sanal makineler**' de çoğaltmak istediğiniz makineleri seçin.

    - VM’ler için bir değerlendirme çalıştırırsanız değerlendirme sonuçlarından VM boyutlandırma ve disk türü (premium/standart) önerilerini uygulayabilirsiniz. Bunu yapmak için **Azure Geçişi değerlendirmesinden geçiş ayarları içe aktarılsın mı?** bölümünde **Evet**’i seçin.
    - Bir değerlendirme çalıştırmadıysanız veya değerlendirme ayarlarını kullanmak istemiyorsanız **Hayır**’ı seçin.
    - Değerlendirmeyi kullanmayı seçtiyseniz VM grubunu ve değerlendirme adını belirleyin.
8. **Kullanılabilirlik seçenekleri**' nde şunları seçin:
    -  Bölge içindeki belirli bir kullanılabilirlik bölgesine geçirilen makineyi sabitlemek için kullanılabilirlik alanı. Kullanılabilirlik Alanları arasında çok düğümlü bir uygulama katmanı oluşturan sunucuları dağıtmak için bu seçeneği kullanın. Bu seçeneği belirlerseniz, Işlem sekmesinde seçilen makinenin her biri için kullanılacak kullanılabilirlik alanını belirtmeniz gerekir. Bu seçenek yalnızca geçiş için seçilen hedef bölge Kullanılabilirlik Alanları destekliyorsa kullanılabilir
    -  Geçirilen makinenin bir kullanılabilirlik kümesine yerleştirileceği kullanılabilirlik kümesi. Bu seçeneği kullanabilmek için seçilen hedef kaynak grubunun bir veya daha fazla kullanılabilirlik kümesi olmalıdır.
    - Geçirilen makineler için bu kullanılabilirlik yapılandırmalarının herhangi birine ihtiyacınız yoksa, altyapı artıklığı gerekli değildir.
9. Geçirmek istediğiniz her VM 'yi denetleyin. Ardından Ileri ' ye tıklayın **: hedef ayarlar**.
10. **Hedef ayarları**’nda aboneliği ve geçiş yapacağınız hedef bölgeyi seçin. Daha sonra Azure VM’lerinin geçişten sonra bulunacağı kaynak grubunu belirtin.
11. **Sanal Ağ**’da Azure VM’lerinin geçişten sonra katılacağı Azure sanal ağını/alt ağını seçin.  
12. **Önbellek depolama hesabı**' nda, proje için otomatik olarak oluşturulan önbellek depolama hesabını kullanmak için varsayılan seçeneği değiştirmeyin. Çoğaltma için önbellek depolama hesabı olarak kullanmak üzere farklı bir depolama hesabı belirtmek istiyorsanız açılan listesini kullanın. <br/> 
    > [!NOTE]
    >
    > - Azure geçişi projesi için bağlantı yöntemi olarak özel uç nokta ' ı seçtiyseniz, kurtarma hizmetleri kasasının önbellek depolama hesabına erişimine izin verin. [**Daha fazla bilgi edinin**](how-to-use-azure-migrate-with-private-endpoints.md#grant-access-permissions-to-the-recovery-services-vault)
    > - Özel eşleme ile ExpressRoute kullanarak çoğaltmak için önbellek depolama hesabı için özel bir uç nokta oluşturun. [**Daha fazla bilgi edinin**](how-to-use-azure-migrate-with-private-endpoints.md#create-a-private-endpoint-for-the-storage-account-optional) 
13. **Kullanılabilirlik seçenekleri**' nde şunları seçin:
    -  Bölge içindeki belirli bir kullanılabilirlik bölgesine geçirilen makineyi sabitlemek için kullanılabilirlik alanı. Kullanılabilirlik Alanları arasında çok düğümlü bir uygulama katmanı oluşturan sunucuları dağıtmak için bu seçeneği kullanın. Bu seçeneği belirlerseniz, Işlem sekmesinde seçilen makinenin her biri için kullanılacak kullanılabilirlik alanını belirtmeniz gerekir. Bu seçenek yalnızca geçiş için seçilen hedef bölge Kullanılabilirlik Alanları destekliyorsa kullanılabilir
    -  Geçirilen makinenin bir kullanılabilirlik kümesine yerleştirileceği kullanılabilirlik kümesi. Bu seçeneği kullanabilmek için seçilen hedef kaynak grubunun bir veya daha fazla kullanılabilirlik kümesi olmalıdır.
    - Geçirilen makineler için bu kullanılabilirlik yapılandırmalarının herhangi birine ihtiyacınız yoksa, altyapı artıklığı gerekli değildir.
14. **Disk şifreleme türü**' nde şunları seçin:
    - Platform tarafından yönetilen anahtar ile bekleyen şifreleme
    - Müşteri tarafından yönetilen anahtarla bekleyen şifreleme
    - Platform tarafından yönetilen ve müşteri tarafından yönetilen anahtarlarla Çift şifreleme

   > [!NOTE]
   > VM 'Leri CMK ile çoğaltmak için hedef kaynak grubu altında [bir disk şifreleme kümesi oluşturmanız](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) gerekir. Bir disk şifreleme kümesi nesnesi yönetilen diskleri, SSE için kullanılacak CMK 'yi içeren bir Key Vault eşler.
  
15. **Azure Hibrit Avantajı**’nda:

    - Azure Hibrit Avantajı’nı uygulamak istemiyorsanız **Hayır**’ı seçin. Ardından **İleri**'ye tıklayın.
    - Etkin Yazılım Güvencesi veya Windows Server abonelikleri kapsamında olan Windows Server makineleriniz varsa ve avantajı geçirdiğiniz makinelere uygulamak istiyorsanız **Evet**’i seçin. Ardından **İleri**'ye tıklayın.

    ![Hedef ayarları](./media/tutorial-migrate-vmware/target-settings.png)

16. **İşlem** bölümünde VM adı, boyutu, işletim sistemi disk türü ve kullanılabilirlik yapılandırmasını (önceki adımda seçildiyse) gözden geçirin. VM’ler [Azure gereksinimleriyle](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)uyumlu olmalıdır.

   - **VM boyutu**: değerlendirme önerilerini KULLANıYORSANıZ, VM boyutu açılan listesi önerilen boyutu gösterir. Aksi takdirde Azure Geçişi, Azure aboneliğindeki en yakın eşleşmeye göre bir boyut seçer. Alternatif olarak **Azure VM boyutu**’nda el ile bir boyut seçin. 
    - **Işletim sistemi diski**: VM için işletim sistemi (önyükleme) diskini belirtin. İşletim Sistemi diski, işletim sistemi önyükleyiciye ve yükleyiciye sahip disktir. 
    - **Kullanılabilirlik alanı**: kullanılacak kullanılabilirlik bölgesini belirtin.
    - **Kullanılabilirlik kümesi**: kullanılacak kullanılabilirlik kümesini belirtin.

17. **Diskler**' de, VM disklerinin Azure 'da çoğaltılıp çoğaltılmayacağını belirtin ve Azure 'da disk türünü (Standart SSD/HDD veya Premium yönetilen diskler) seçin. Ardından **İleri**'ye tıklayın.
    - Diskleri çoğaltmadan çıkarabilirsiniz.
    - Diskleri çıkarırsanız bu diskler geçişten sonra Azure VM’de bulunmaz. 

18. **Çoğaltmayı gözden geçir ve başlat** bölümünde ayarları gözden geçirin ve sunuculara yönelik ilk çoğaltmayı başlatmak için **Çoğalt** üzerine tıklayın.

> [!NOTE]
> Çoğaltma ayarlarını, çoğaltma başlamadan önce dilediğiniz zaman güncelleştirebilirsiniz,   >  **çoğaltılan makineleri** yönetin. Çoğaltma başladıktan sonra ayarlar değiştirilemez.


## <a name="track-and-monitor"></a>İzleme ve izleme

1. Portal bildirimlerinde iş durumunu izleyin. 

    ![İşi izle](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. Çoğaltma durumunu izlemek için Azure geçişi 'nde **sunucuları çoğaltma** **: sunucu geçişi**' ne tıklayın.

    ![Çoğaltmayı izleme](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

Çoğaltma aşağıdaki gibi gerçekleşir:
- Çoğaltma Başlat işi başarıyla tamamlandığında, makineler ilk çoğaltmasını Azure 'a başlatır.
- İlk çoğaltma tamamlandıktan sonra Delta çoğaltma başlar. Şirket içi disklerde artımlı değişiklikler düzenli aralıklarla Azure 'daki çoğaltma disklerine çoğaltılır.


## <a name="run-a-test-migration"></a>Geçiş testi çalıştırma


Delta çoğaltma başladığında, Azure 'a tam geçiş çalıştırmadan önce VM 'Ler için bir test geçişi çalıştırabilirsiniz. Bunu geçirmeden önce her makine için en az bir kez yapmanızı öneririz.

- Bir test geçişi çalıştırmak, geçiş işleminin beklendiği gibi çalıştığını denetler ve bu işlem, çalışır durumda olan şirket içi makineleri etkilemeden, çoğaltmaya devam eder. 
- Test geçişi, çoğaltılan verileri kullanarak bir Azure VM oluşturarak geçişe benzetir (genellikle Azure aboneliğinizdeki bir üretim dışı VNet 'e geçiş yapar).
- Geçişi doğrulamak, uygulama testi gerçekleştirmek ve tam geçişten önce herhangi bir sorunu gidermek için çoğaltılan test Azure VM 'yi kullanabilirsiniz.

Test geçişini aşağıdaki şekilde yapın:


1. **Geçiş hedefleri**  >  **sunucuları**  >  **Azure geçişi: sunucu geçişi**' nde **geçirilen sunucuları test et**' e tıklayın.

     ![Geçirilen sunucuları test etme](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Test edilecek VM’yi sağ tıklayıp **Test geçişi** üzerine tıklayın.

    ![Test geçişi](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. **Test Geçişi** bölümünde, Azure VM’nin geçişten sonra bulunacağı Azure VNet’i seçin. Üretim dışı bir VNet kullanmanızı öneririz.
4. **Test Geçişi** işlemi başlar. Portal bildirimlerinde işi izleyin.
5. Geçiş bittikten sonra geçirilen Azure VM’yi, Azure portalında **Sanal Makineler** bölümünde görüntüleyin. Makine adında **Testi** şeklinde bir son ek vardır.
6. Test tamamlandıktan sonra **Makineleri çoğaltma** bölümünde Azure VM’yi sağ tıklayıp **Test geçişini temizle** üzerine tıklayın.

    ![Geçişi temizleme](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>VM geçirme

Test geçişinin beklendiği gibi çalışıp çalışmadığını doğruladıktan sonra şirket içi makineleri geçirebilirsiniz.

1. Azure geçişi proje > **sunucuları**  >  **Azure geçişi: sunucu geçişi**' nde, **sunucuları çoğaltma**' ya tıklayın.

    ![Sunucuları çoğaltma](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. **Makineleri çoğaltma** bölümünde VM > **Geçir** üzerine sağ tıklayın.
3.   >  **Sanal makineleri Kapat ' a geçiş yapın ve veri kaybı olmadan planlı bir geçiş gerçekleştirin**, **Evet**  >  **Tamam**' ı seçin.
    - Varsayılan olarak Azure geçişi, en düşük veri kaybını sağlamak için şirket içi VM 'yi kapatır. 
    - VM’yi kapatmak istemiyorsanız, **Hayır** seçeneğini belirleyin
4. VM için bir geçiş işlemi başlar. Azure bildirimlerinde işlemi izleyin.
5. İşlem bittikten sonra **Sanal Makineler** sayfasında VM’yi görüntüleyebilir ve yönetebilirsiniz.

## <a name="complete-the-migration"></a>Geçişi tamamlamayı

1. Geçiş yapıldıktan sonra, **geçişi durdurmak**> VM 'ye sağ tıklayın. Bu, şunları yapar:
    - Şirket içi makine için çoğaltmayı sonlandırır.
    - Makineyi Azure geçişi: sunucu geçişi içindeki **çoğaltma sunucusu** sayısından kaldırır.
    - VM için çoğaltma durumu bilgilerini temizler.
2. Geçirilen makinelere Azure VM [Windows](../virtual-machines/extensions/agent-windows.md) veya [Linux](../virtual-machines/extensions/agent-linux.md) Aracısı 'nı yükler.
3. Veritabanı bağlantısı dizelerini ve web sunucusu yapılandırmalarını güncelleştirme gibi herhangi bir geçiş sonrası uygulama ayarı gerçekleştirin.
4. Geçirilen uygulamada son uygulama ve geçiş kabul testi gerçekleştirme işlemi şimdi Azure’da çalıştırılmaktadır.
5. Geçirilen Azure VM örneğine giden trafiği kesin.
6. Yerel sanal makine envanterinizden şirket içi sanal makineleri kaldırın.
7. Yerel yedeklemelerden şirket içi sanal makineleri kaldırın.
8. Azure sanal makinelerinin yeni konumunu ve IP adresini göstermek için herhangi bir iç belgeyi güncelleştirin. 

## <a name="post-migration-best-practices"></a>Geçiş sonrası en iyi uygulamalar

- Şirket içi
    - Uygulama trafiğini, geçirilen Azure sanal makinesi örneğinde çalıştırılan uygulamaya taşıyın.
    - Yerel sanal makine envanterinizden şirket içi sanal makineleri kaldırın.
    - Yerel yedeklemelerden şirket içi sanal makineleri kaldırın.
    - Azure sanal makinelerinin yeni konumunu ve IP adresini göstermek için herhangi bir iç belgeyi güncelleştirin.
- Geçişten sonra Azure VM ayarlarını ince ayar:
    - [Azure VM aracısı](../virtual-machines/extensions/agent-windows.md), Azure Yapı Denetleyicisi ile sanal makine etkileşimini yönetir. Azure Backup, Site Recovery ve Azure Güvenliği gibi bazı Azure hizmetleri için gereklidir. VM 'Leri aracı tabanlı geçişle geçirirken, Mobility hizmeti yükleyicisi Windows makinelerine Azure VM Aracısı 'nı yüklüyor. Linux VM 'lerde, geçişten sonra aracıyı yüklemenizi öneririz.
    - Geçiş işleminden sonra Mobility hizmetini Azure VM 'den el ile kaldırın.
    - Geçişten sonra VMware araçlarını el ile kaldırın.
- Azure 'da:
    - Veritabanı bağlantısı dizelerini ve web sunucusu yapılandırmalarını güncelleştirme gibi herhangi bir geçiş sonrası uygulama ayarı gerçekleştirin.
    - Geçirilen uygulamada son uygulama ve geçiş kabul testi gerçekleştirme işlemi şimdi Azure’da çalıştırılmaktadır.
- İş sürekliliği/olağanüstü durum kurtarma
    - Azure Backup hizmetini kullanarak Azure sanal makinelerini yedekleyip verileri güvende tutun. [Daha fazla bilgi edinin](../backup/quick-backup-vm-portal.md).
    - Site Recovery ile Azure sanal makinelerini ikincil bölgeye çoğaltarak iş yüklerinin çalışmaya devam etmesini ve sürekli kullanılabilir olmasını sağlayın. [Daha fazla bilgi edinin](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Daha fazla güvenlik için:
    - Azure Güvenlik Merkezi ile gelen trafik erişimini kilitleme ve sınırlayın [-tam zamanında yönetim](../security-center/security-center-just-in-time.md).
    - [Ağ Güvenlik Grupları](../virtual-network/network-security-groups-overview.md) ile ağ trafiğini yönetim uç noktaları ile kısıtlayın.
    - [Azure Disk Şifrelemesi](../security/fundamentals/azure-disk-encryption-vms-vmss.md)’ni dağıtarak disklerin güvenliğinin sağlanmasına yardımcı olun ve verileri hırsızlık ve yetkisiz erişime karşı koruyun.
    - [IaaS kaynaklarının güvenliğini sağlama](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) hakkında daha fazla bilgi edinin ve [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)’ni ziyaret edin.
- İzleme ve yönetim için:
    - [Azure Maliyet Yönetimi](../cost-management-billing/cloudyn/overview.md)’ni dağıtarak kaynak kullanımını ve harcamayı izleyin.




 ## <a name="next-steps"></a>Sonraki adımlar

Azure bulut benimseme çerçevesindeki [bulut geçiş yolculuğunu](/azure/architecture/cloud-adoption/getting-started/migrate) araştırın.