---
title: Aracı tabanlı Azure geçişi sunucu geçişi ile şirket içi VMware VM 'lerini Azure 'a geçirme | Microsoft Docs
description: Bu makalede, Azure geçiş sunucusu geçişi ile şirket içi makinelerin Azure 'a yönelik aracı tabanlı geçişinin nasıl gerçekleştirileceği açıklanır.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 07e91abc1130505abc84f6687be7edd04522fa76
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720181"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>VMware VM 'lerini Azure 'a geçirme (aracı tabanlı)

Bu makalede, Azure geçişi sunucusu geçiş aracı ile aracı tabanlı geçiş kullanarak şirket içi VMware VM 'lerini Azure 'a nasıl geçirebileceğiniz gösterilmektedir.

[Azure geçişi](migrate-services-overview.md) , şirket içi uygulamalarınızı ve iş yüklerinizi ve AWS/GCP sanal makine örneklerini Azure 'a bulmayı, değerlendirmeyi ve geçirmeyi izlemek için bir merkezi Merkez sağlar. Hub, değerlendirme ve geçiş için Azure geçiş araçları ve ayrıca üçüncü taraf bağımsız yazılım satıcısı (ISV) teklifleri sağlar.


Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Kaynak ortamı ayarlayın ve aracı tabanlı geçiş için bir Azure geçişi çoğaltma gereci dağıtın.
> * Geçiş için hedef ortamı ayarlayın.
> * Çoğaltma ilkesi ayarlayın.
> * Çoğaltmayı etkinleştirin.
> * Her şeyin beklendiği gibi çalıştığından emin olmak için bir test geçişi çalıştırın.
> * Azure 'a tam geçiş gerçekleştirin.

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir, böylece bir kavram kanıtı hızlı bir şekilde ayarlayabilmenizi sağlayabilirsiniz. Öğreticiler mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Ayrıntılı yönergeler için, VMware değerlendirmesi ve geçiş için nasıl yapılır konusunu gözden geçirin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce

VM 'Leri Azure 'a geçirmeden önce Azure geçişi sunucu değerlendirmesi ile VMware VM değerlendirmesi yapmayı öneririz. Bir değerlendirmeyi aşağıdaki şekilde ayarlayın:

1. [Azure ve VMware](tutorial-prepare-vmware.md) 'yi değerlendirme için hazırlamak üzere öğreticiyi izleyin.
2. Daha sonra, değerlendirme için bir Azure geçişi gereci ayarlamak ve VM 'Leri bulup değerlendirmek için [Bu öğreticiyi](tutorial-assess-vmware.md) izleyin.


Bir değerlendirmeyi denemenizi öneririz, ancak VM 'Leri geçirmeden önce bir değerlendirme çalıştırmanız gerekmez.

## <a name="migration-methods"></a>Geçiş yöntemleri

Azure geçişi sunucu geçiş aracını kullanarak VMware VM 'lerini Azure 'a geçirebilirsiniz. Bu araç, VMware VM geçişi için birkaç seçenek sunar:

- Aracısız çoğaltma. VM 'Leri bunlara hiçbir şey yüklemeye gerek kalmadan geçirin.
- Aracı tabanlı geçiş. ya da çoğaltma. Çoğaltma için VM 'ye bir aracı (Mobility Hizmetleri Aracısı) yükler.

Aracısız veya aracı tabanlı geçiş kullanmak isteyip istemediğinize karar vermek için şu makaleleri gözden geçirin:

- VMware geçiş seçenekleri [hakkında bilgi edinin](server-migrate-overview.md) .
- [Geçiş yöntemlerini karşılaştırın](server-migrate-overview.md#compare-migration-methods).
- Aracısız geçişi denemek için [Bu makaleyi izleyin](tutorial-migrate-vmware.md) .



## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiye başlamadan önce karşılamanız gereken ön koşullar şunlardır:

1. VMware geçiş mimarisini [gözden geçirin](migrate-architecture.md) .
2. Azure hesabınızın sanal makine katılımcısı rolüne atandığından emin olun, böylece şu izinlere sahip olursunuz:

    - Seçilen kaynak grubunda sanal makine oluşturma.
    - Seçilen sanal ağda sanal makine oluşturma.
    - Azure yönetilen diskine yazın. 

3. [Bir Azure ağı kurun](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Şirket içi makineler Azure yönetilen disklere çoğaltılır. Geçiş için Azure 'a yük devretmek, Azure VM 'Leri bu yönetilen disklerden oluşturulur ve geçişi ayarlarken belirttiğiniz bir Azure ağı 'na eklenir.


## <a name="prepare-azure"></a>Azure’u hazırlama

Azure geçişi sunucu değerlendirmesi ile zaten bir değerlendirme çalıştırırsanız, bu adımları zaten tamamlamış olduğundan bu bölümdeki yönergeleri atlayabilirsiniz. 

Bir değerlendirme çalıştırmadığınız takdirde Azure geçişi sunucu geçişi ile geçirebilmeniz için önce Azure izinleri ayarlamanız gerekir.

- **Proje oluşturma**: Azure hesabınız, Azure geçişi projesi oluşturmak için izinlere ihtiyaç duyuyor. 
- **Azure geçişi çoğaltma gereci kaydetme**: çoğaltma gereci Azure hesabınızda bir Azure Active Directory uygulaması oluşturur ve kaydeder. Bunun için izinleri temsilci seçmeniz gerekir.
- **Oluşturma Key Vault**: Azure geçişi, Azure geçişi sunucu geçişini kullanarak VMware VM 'lerini geçirmek için kaynak grubunda bir Key Vault oluşturur ve erişim anahtarlarını aboneliğinizdeki çoğaltma depolama hesabına yönetebilir. Kasayı oluşturmak için Azure geçişi projesinin bulunduğu kaynak grubunda rol atama izinlerine sahip olmanız gerekir. 


### <a name="assign-permissions-to-create-project"></a>Proje oluşturmak için izin atama

1. Azure portal aboneliğini açın ve **erişim denetimi (IAM)** seçeneğini belirleyin.
2. **Erişimi denetle**' de ilgili hesabı bulun ve izinleri görüntülemek için tıklatın.
3. **Katkıda bulunan** veya **sahip** izinlerinizin olması gerekir.
    - Henüz ücretsiz bir Azure hesabı oluşturduysanız, aboneliğinizin sahibi olursunuz.
    - Abonelik sahibi değilseniz, rolü atamak için sahip ile çalışın.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Çoğaltma gerecini kaydetmek için izin atama

Aracı tabanlı geçiş için, hesabınızda bir Azure AD uygulaması oluşturmak ve kaydetmek üzere Azure 'a geçiş sunucusu geçişini devretmek için temsilci izinleri. Aşağıdaki yöntemlerden birini kullanarak izinleri atayabilirsiniz:

- Kiracı/Genel yönetici, Kiracıdaki kullanıcılara Azure AD uygulamaları oluşturmak ve kaydolmak için izin verebilir.
- Kiracı/Genel yönetici, uygulama geliştirici rolünü (izinleri olan) hesaba atayabilir.

Buna dikkat edin:

- Uygulamalar, yukarıda açıklananlar dışında başka bir abonelik üzerinde başka erişim izinlerine sahip değildir.
- Yalnızca yeni bir çoğaltma gereci kaydettiğinizde bu izinlere ihtiyacınız vardır. Çoğaltma gereci kurulduktan sonra izinleri kaldırabilirsiniz. 


#### <a name="grant-account-permissions"></a>Hesap izinleri verme

Kiracı/Genel yönetici, izinleri aşağıdaki gibi verebilir

1. Azure AD 'de, kiracı/genel yönetici **Azure Active Directory** > **Kullanıcılar** > **Kullanıcı ayarları**' na gitmelidir.
2. Yönetici **uygulama kayıtları** **Evet**olarak ayarlanmalıdır.

    ![Azure AD izinleri](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Bu, hassas olmayan bir varsayılan ayardır. [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Uygulama geliştirici rolü atama 

Kiracı/Genel yönetici, uygulama geliştirici rolünü bir hesaba atayabilir. [Daha fazla bilgi edinin](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Key Vault oluşturmak için izin atama

Azure geçişi projesinin bulunduğu kaynak grubunda rol atama izinlerini aşağıdaki gibi atayın:

1. Azure portal içindeki kaynak grubunda, **erişim denetimi (IAM)** seçeneğini belirleyin.
2. **Erişimi denetle**' de ilgili hesabı bulun ve izinleri görüntülemek için tıklatın. Sahip (veya **katkıda bulunan** ve **Kullanıcı erişimi Yöneticisi**) izinlerine **sahip** olmanız gerekir.
3. Gerekli izinleriniz yoksa, bunları kaynak grubu sahibinden isteyin. 


## <a name="prepare-on-premises-vmware"></a>Şirket içi VMware’leri hazırlama

### <a name="prepare-an-account-for-automatic-discovery"></a>Otomatik bulma için bir hesap hazırlama

Azure geçişi sunucu geçişi, VMware sunucularına şu şekilde erişim gerektirir:

- VM'leri otomatik olarak bulma. En az bir salt okunur hesap gereklidir.
- Çoğaltma, yük devretme ve yeniden çalıştırmayı yönetme. Diskleri oluşturma ve kaldırma ve VM’leri çalıştırma gibi işlemleri gerçekleştirebilen bir hesabınızın olması gerekir.

Hesabı aşağıdaki gibi oluşturun:

1. Ayrılmış bir hesap kullanmak için, rolü vCenter düzeyinde oluşturun. Role **Azure_Site_Recovery** gibi bir ad verin.
2. Role aşağıdaki tabloda özetlenen izinleri atayın.
3. vCenter sunucusu veya vSphere ana bilgisayarında bir kullanıcı oluşturun. Rolü kullanıcıya atayın.

#### <a name="vmware-account-permissions"></a>VMware hesap izinleri

**Görev** | **Rol/İzinler** | **Ayrıntılar**
--- | --- | ---
**VM bulma** | En az bir salt okunur kullanıcı<br/><br/> Veri Merkezi nesnesi –> Alt Nesneye Yay, role=Read-only | Kullanıcı veri merkezi düzeyinde atandı ve bu veri merkezindeki tüm nesnelere erişimi var.<br/><br/> Erişimi kısıtlamak için, **Alt öğeye yay** nesnesi ile **Erişim yok** rolünü alt nesnelere (vSphere ana bilgisayarları, veri depoları, VM’ler ve ağlar) atayın.
**Tam çoğaltma, yük devretme, yeniden çalışma** |  Gerekli izinlere sahip bir rol (Azure_Site_Recovery) oluşturup rolü VMware kullanıcısı veya grubuna atayın<br/><br/> Veri Merkezi nesnesi –> Alt Nesneye Yay, role=Azure_Site_Recovery<br/><br/> Veri deposu -> Alan ayırma, veri deposuna göz atma, düşük düzeyli dosya işlemleri, dosyayı kaldırma, sanal makine dosyalarını güncelleştirme<br/><br/> Ağ -> Ağ ataması<br/><br/> Kaynak -> VM’yi kaynak havuzuna atama, kapalı VM’yi geçirme, açık VM’yi geçirme<br/><br/> Görevler -> Görev oluşturma, görevi güncelleştirme<br/><br/> Sanal makine -> Yapılandırma<br/><br/> Sanal makine -> Etkileşim -> soruyu yanıtlama, cihaz bağlantısı, CD ortamını yapılandırma, disket ortamını yapılandırma, kapatma, açma, VMware araçlarını yükleme<br/><br/> Sanal makine -> Envanter -> Oluşturma, kaydetme, kaydı kaldırma<br/><br/> Sanal makine -> Sağlama -> Sanal makine indirmeye izin verme, Sanal makine dosyalarını karşıya yüklemeye izin verme<br/><br/> Sanal makine -> Anlık görüntüler -> Anlık görüntüleri kaldırma | Kullanıcı veri merkezi düzeyinde atandı ve bu veri merkezindeki tüm nesnelere erişimi var.<br/><br/> Erişimi kısıtlamak için, **Alt öğeye yay** nesnesi ile **Erişim yok** rolünü alt nesnelere (vSphere ana bilgisayarları, veri depoları, VM’ler ve ağlar) atayın.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Bir hesabı Mobility hizmeti yüklemesi için hazırlama

Çoğaltmak istediğiniz makinelerde Mobility hizmeti yüklü olmalıdır.

- Azure geçişi çoğaltma gereci, bir makine için çoğaltmayı etkinleştirdiğinizde bu hizmetin göndererek yüklemesini gerçekleştirebilir veya el ile veya yükleme araçlarını kullanarak yükleyebilirsiniz.
- Bu öğreticide, Mobility hizmetini göndererek yükleme yoluyla yükleyeceğiz.
- Anında yükleme için, Azure geçişi sunucu geçişinin sanal makineye erişmek için kullanabileceği bir hesap hazırlamanız gerekir.

Hesabı aşağıdaki gibi hazırlayın:

1. VM üzerinde yükleme izinleri ile bir etki alanı veya yerel hesap hazırlayın.
2. Windows VM 'Ler için, bir etki alanı hesabı kullanmıyorsanız, kayıt defterinde bir değere sahip **LocalAccountTokenFilterPolicy**DWORD girdisini ekleyerek yerel makinede uzak kullanıcı erişim denetimini devre dışı bırakın, **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\ altında Windows\currentversion\policies\sistemi**
3. Linux VM 'Ler için kaynak Linux sunucusunda bir kök hesap hazırlayın.


### <a name="check-vmware-requirements"></a>VMware gereksinimlerini denetleme

VMware sunucularının ve VM 'lerinin Azure 'a geçiş gereksinimleriyle uyumlu olduğundan emin olun. 


> [!NOTE]
> Azure geçişi sunucu geçişi ile aracı tabanlı geçiş, Azure Site Recovery hizmetinin özelliklerine dayalıdır. Bazı gereksinimler Site Recovery belgelerine bağlantı verebilir.

1. VMware sunucusu gereksinimlerini [doğrulayın](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements).
2. [Doğrula](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) Geçiş için VM desteği gereksinimleri.
3. VM ayarlarını doğrulayın. Azure 'a çoğaltılan şirket içi VM 'Ler, [Azure VM gereksinimleriyle](migrate-support-matrix-vmware.md#azure-vm-requirements)uyumlu olmalıdır.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Azure geçiş sunucusu geçiş aracını ekleme

VMware VM 'lerini değerlendirmek için öğreticiyi takip etmediyseniz, bir Azure geçişi projesi ayarlayın ve ardından Azure geçiş sunucusu geçiş aracı 'nı ekleyin:

1. Azure portalı > **Tüm hizmetler** bölümünde **Azure Geçişi**’ni arayın.
2. **Hizmetler** altında **Azure Geçişi**’ni seçin.

    ![Azure geçişi 'ni ayarlama](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. **Genel Bakış** bölümünde **Sunucuları değerlendir ve geçir**’e tıklayın.
4. **Sunucuları bul, değerlendir ve geçir**altında, **sunucuları değerlendir ve geçir**' e tıklayın.

    ![Sunucuları bulma ve değerlendirme](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. **Sunucuları bul, değerlendir ve geçir** bölümünde **Araç ekle**’ye tıklayın.
2. **Projeyi geçir** bölümünde Azure aboneliğinizi seçin ve henüz yapmadıysanız bir kaynak grubu oluşturun.
3. **Proje ayrıntıları**' nda projeyi oluşturmak istediğiniz proje adını ve coğrafi konumu belirtin ve **İleri** ' ye tıklayın.

    ![Azure geçişi projesi oluşturma](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    Bu coğrafi bölgelerde bir Azure geçişi projesi oluşturabilirsiniz.

    **Coğrafya** | **Bölge**
    --- | ---
    Asya | Güneydoğu Asya
    Avrupa | Kuzey Avrupa veya Batı Avrupa
    Amerika Birleşik Devletleri | Doğu ABD veya Orta Batı ABD

    Proje için belirtilen coğrafya yalnızca şirket içi VM’lerden toplanan meta verileri depolamak için kullanılır. Gerçek geçiş için herhangi bir hedef bölge seçebilirsiniz.
4. **Değerlendirme Seç aracında**, şimdi > **için değerlendirme aracı eklemeyi atla** ' yı seçin.
5. **Geçiş aracı Seç**bölümünde **Azure geçişi: sunucu geçişi** > **İleri**' yi seçin.
6. **İnceleme ve araç ekle**' de ayarları gözden geçirin ve **araç ekle** ' ye tıklayın.
7. Araç eklendikten sonra, Azure geçişi Proje > **sunucularında** **geçiş araçları** > görüntülenir.

## <a name="set-up-the-replication-appliance"></a>Çoğaltma gereç ayarı

Geçişin ilk adımı, çoğaltma gerecini ayarlamaya yönelik. Çoğaltma gereci, şu bileşenleri barındıran tek, yüksek oranda kullanılabilir, şirket içi bir VMware VM 'dir:

- **Yapılandırma sunucusu**: yapılandırma sunucusu, şirket Içi ve Azure arasındaki iletişimleri koordine eder ve veri çoğaltmasını yönetir.
- **İşlem sunucusu**: işlem sunucusu bir çoğaltma ağ geçidi olarak davranır. Çoğaltma verilerini alır; önbelleğe alma, sıkıştırma ve şifreleme ile en iyi duruma getirir ve Azure 'da bir önbellek depolama hesabına gönderir. İşlem sunucusu Ayrıca, çoğaltmak istediğiniz VM 'Lere Mobility hizmeti Aracısı 'nı da yükleyip şirket içi VMware VM 'lerinin otomatik olarak bulunmasını gerçekleştirir.


Çoğaltma gerecini ayarlamak için hazırlanan bir açık sanallaştırma uygulaması (OVA) şablonu indirirler. Şablonu VMware 'ye içeri aktarır ve çoğaltma gereci sanal makinesini oluşturursunuz. 

### <a name="download-the-replication-appliance-template"></a>Çoğaltma gereç şablonunu indirin

Şablonu şu şekilde indirin:

1. Azure geçişi projesinde, **geçiş hedefleri**altındaki **sunucular** ' a tıklayın.
2. **Azure geçişi-sunucular** > **Azure geçişi: sunucu geçişi**' nde **bul**' a tıklayın.

    ![VM'leri bulma](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. **Makineleri bul** > **Makineleriniz sanallaştırıldı mı?** bölümünde **Evet, VMware vSphere hiper yöneticisi ile** seçeneğini belirleyin.
4. **Nasıl geçiş**yapmak istiyorsunuz? bölümünde, **aracı tabanlı çoğaltmayı kullanma**' yı seçin.
5. **Hedef bölge**' de, makineleri geçirmek istediğiniz Azure bölgesini seçin.
6. **Geçiş için hedef bölgenin bölge adı olduğunu onaylayın**' i seçin.
7. **Kaynak oluştur**' a tıklayın. Bu, arka planda bir Azure Site Recovery Kasası oluşturur.
    - Bu düğmeye tıkladıktan sonra bu proje için hedef bölgeyi değiştiremezsiniz.
    - Sonraki tüm geçişler bu bölgedir.

    ![Kurtarma Hizmetleri kasası oluşturma](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. **Yeni bir çoğaltma gereci yüklemek**istiyor musunuz?, **çoğaltma gereci yüklensin**' i seçin.
9. Çoğaltma gereç indirmek için **İndir**' e tıklayın. Bu, gereci çalıştıran yeni bir VMware VM oluşturmak için kullandığınız bir OVF şablonunu indirir.
    OVA](./media/tutorial-migrate-vmware-agent/download-ova.png) Indirme ![
10. Kaynak grubunun ve kurtarma hizmetleri kasasının adını aklınızda edin. Bu, Gereç dağıtımı sırasında gereklidir.


### <a name="import-the-template-in-vmware"></a>VMware’de şablonu içeri aktarma

OVF şablonunu indirdikten sonra, Windows Server 2016 çalıştıran bir VMware VM 'de çoğaltma uygulaması oluşturmak için onu VMware 'e içeri aktarırsınız.

1. VMWare vSphere İstemcisi ile VMware vCenter sunucusunda veya vSphere ESXi konağında oturum açın.
2. **Dosya** menüsünde **OVF Şablonunu Dağıt** seçeneğini belirleyerek **OVF Şablonu Dağıtma Sihirbazı**’nı başlatın. 
3. **Kaynak Seç**' de indirilen ovf 'nin konumunu girin.
4. **Ayrıntıları gözden geçirin**' de **İleri**' yi seçin.
5. **Ad ve klasör seçin** ' de, yapılandırma ' yı **seçin**ve varsayılan ayarları kabul edin.
6. **Depolama > seçin** ' de **sanal disk biçimi seçin**, En Iyi performans Için, **kalın sağlama Eager sıfırlandı**seçeneğini belirleyin.
7. Sihirbaz sayfalarının geri kalan kısmında varsayılan ayarları kabul edin.
8. **Tamamlanmaya hazır**' te, VM 'yi varsayılan ayarlarla ayarlamak için dağıtım > **son**' u seçerek **Güç açma** ' yı seçin.

   > [!TIP]
   > Bir NIC daha eklemek istiyorsanız **Dağıtımdan sonra aç** > **Son** seçeneğinin işaretini kaldırın. Varsayılan olarak, şablon tek bir NIC içerir. Dağıtımdan sonra daha fazla NIC ekleyebilirsiniz.

### <a name="kick-off-replication-appliance-setup"></a>Çoğaltma gereç kurulumunu başlatma

1. VMWare vSphere Client konsolundan VM’yi açın.
2. VM’de Windows Server 2016 yükleme deneyimi önyüklemesi yapılır. Lisans sözleşmesini kabul edin ve bir yönetici parolası girin.
3. Yükleme tamamlandıktan sonra, yönetici parolasını kullanarak VM 'de yönetici olarak oturum açın.
4. İlk kez oturum açtığınızda, çoğaltma gereç Kurulum Aracı (Azure Site Recovery yapılandırma aracı) birkaç saniye içinde başlar.
5. Gereci Azure geçişi sunucu geçişine kaydederek kullanılacak bir ad girin. Ardından **İleri**'ye tıklayın.
6. Araç, VM’nin Azure bağlanıp bağlanamadığını denetler. Bağlantı kurulduktan sonra Azure aboneliğinizde oturum açmak için **Oturum aç** seçeneğini belirleyin.
7. Aracın gereci tanımlamak için bir Azure AD uygulaması kaydını tamamlamasını bekleyin. Gereç yeniden başlatılır.
1. Makinede tekrar oturum açın. Birkaç saniye içinde, Yapılandırma Sunucusu Yönetim Sihirbazı otomatik olarak başlar.

### <a name="register-the-replication-appliance"></a>Çoğaltma gereç kaydını kaydetme

Çoğaltma gerecini ayarlamayı ve kaydetmeyi tamamlayın.

1. Yapılandırma sunucusu Yönetim Sihirbazı 'nda, **Kurulum bağlantısı**' nı seçin.
2. Çoğaltma gerecinin VM keşfi için kullandığı NIC 'yi (varsayılan olarak yalnızca bir NIC) seçin ve Mobility hizmetinin kaynak makinelerde anında yüklemesini yapın.
3. Çoğaltma gerecinin Azure ile bağlantı için kullandığı NIC 'ı seçin. Daha sonra **Kaydet**’e tıklayın. Yapılandırıldıktan sonra bu ayarı değiştiremezsiniz.
4. Gereç bir proxy sunucusunun arkasında bulunuyorsa, proxy ayarlarını belirtmeniz gerekir.
    - Proxy adını **http://ip-address** veya **http://FQDN** olarak belirtin. HTTPS proxy sunucuları desteklenmez.
5. Abonelik, kaynak grupları ve kasa ayrıntıları istendiğinde, Gereç şablonunu indirdiğinizde not ettiğiniz ayrıntıları ekleyin.
6. **Üçüncü taraf yazılımı yükleyin** bölümünde lisans sözleşmesini kabul edin. MySQL Server’ı yüklemek için **İndir ve Yükle** seçeneğini belirleyin.
7. **VMware PowerCLI’yi Yükle** seçeneğini belirleyin. Bunu yapmadan önce tüm tarayıcı pencerelerinin kapalı olduğundan emin olun. Daha sonra **Devam** seçeneğini belirleyin.
8. **Gereç yapılandırmasını doğrulama** bölümünde ön koşullar, siz devam etmeden önce doğrulanır.
9. **vCenter Server/vSphere ESXi sunucusu yapılandırma** bölümünde, çoğaltmak istediğiniz VM’lerin bulunduğu vCenter sunucusunun veya vSphere konağının FQDN’sini ya da IP adresini girin. Sunucunun dinleme gerçekleştirdiği bağlantı noktasını girin. Kasadaki VMware sunucusu için kullanılacak bir kolay ad girin.
10. VMware bulma için [oluşturduğunuz](#prepare-an-account-for-automatic-discovery) hesabın kimlik bilgilerini girin. **Ekle** > **devam et**' i seçin.
11. **Sanal makine kimlik bilgilerini Yapılandır**' da, VM 'ler için çoğaltmayı etkinleştirdiğinizde Mobility hizmetinin anında yüklenmesi için [oluşturduğunuz](#prepare-an-account-for-mobility-service-installation) kimlik bilgilerini girin.  
    - Windows makinelerinde hesap için, çoğaltmak istediğiniz makinelerde yerel yönetici ayrıcalıkları gerekir.
    - Linux’ta kök hesap için bilgileri sağlayın.
12. Kaydı tamamlamak için **Yapılandırmayı son haline getir** seçeneğini belirleyin.


Çoğaltma gereci kaydedildikten sonra, Azure geçişi sunucu değerlendirmesi, belirtilen ayarları kullanarak VMware sunucularına bağlanır ve VM 'Leri bulur. Bulunan VM 'Leri, **diğer** sekmede > **bulunan öğeleri** **Yönet** bölümünde görüntüleyebilirsiniz.


## <a name="replicate-vms"></a>Sanal makineleri çoğaltma

Şimdi, geçiş için VM 'Ler ' i seçin.

> [!NOTE]
> En fazla 10 makineyi birbirine çoğaltabilirsiniz. Daha fazla çoğaltma yapmanız gerekiyorsa bunları aynı anda 10 ' da çoğaltın.

1. Azure geçişi Proje > **sunucularında** **Azure geçişi: sunucu geçişi**' nde **Çoğalt**' a tıklayın.

    ![Sanal makineleri çoğaltma](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. **Çoğalt**, > **Kaynak ayarları** > **Makineleriniz sanallaştırıldı mı?** bölümünde **Evet, VMware vSphere** ile seçeneğini belirleyin.
3. **Şirket içi gereç**bölümünde, ayarladığınız Azure geçiş gerecinin adını seçin.
4. **VCenter Server**'Da, VM 'Leri yöneten vCenter sunucusunun adını veya VM 'Lerin barındırıldığı vSphere sunucusunu belirtin.
5. **Işlem sunucusu**' nda, çoğaltma gerecinin adını seçin.
6. **Konuk kimlik bilgileri**' nde, Mobility hizmetinin göndererek yüklenmesi IÇIN kullanılacak VM yönetici hesabını belirtin. Ileri ' ye tıklayın **: sanal makineler**.

    ![Sanal makineleri çoğaltma](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. **Sanal makineler**' de çoğaltmak istediğiniz makineleri seçin.

    - VM’ler için bir değerlendirme çalıştırırsanız değerlendirme sonuçlarından VM boyutlandırma ve disk türü (premium/standart) önerilerini uygulayabilirsiniz. Bunu yapmak için **Azure Geçişi değerlendirmesinden geçiş ayarları içe aktarılsın mı?** bölümünde **Evet**’i seçin.
    - Bir değerlendirme çalıştırmadıysanız veya değerlendirme ayarlarını kullanmak istemiyorsanız **Hayır**’ı seçin.
    - Değerlendirmeyi kullanmayı seçtiyseniz VM grubunu ve değerlendirme adını belirleyin.

8. Geçirmek istediğiniz her VM 'yi denetleyin. Ardından Ileri ' ye tıklayın **: hedef ayarlar**.
9. **Hedef ayarları**’nda aboneliği ve geçiş yapacağınız hedef bölgeyi seçin. Daha sonra Azure VM’lerinin geçişten sonra bulunacağı kaynak grubunu belirtin.
10. **Sanal Ağ**’da Azure VM’lerinin geçişten sonra katılacağı Azure sanal ağını/alt ağını seçin.
11. **Azure Hibrit Avantajı**’nda:

    - Azure Hibrit Avantajı’nı uygulamak istemiyorsanız **Hayır**’ı seçin. Ardından **İleri**'ye tıklayın.
    - Etkin Yazılım Güvencesi veya Windows Server abonelikleri kapsamında olan Windows Server makineleriniz varsa ve avantajı geçirdiğiniz makinelere uygulamak istiyorsanız **Evet**’i seçin. Ardından **İleri**'ye tıklayın.

12. **İşlem** bölümünde VM adını, boyutunu, İşletim Sistemi disk türünü ve kullanılabilirlik kümesini gözden geçirin. VM’ler [Azure gereksinimleriyle](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)uyumlu olmalıdır.

    - **VM boyutu**: değerlendirme önerilerini KULLANıYORSANıZ, VM boyutu açılan listesi önerilen boyutu içerir. Aksi takdirde Azure Geçişi, Azure aboneliğindeki en yakın eşleşmeye göre bir boyut seçer. Alternatif olarak **Azure VM boyutu** ’nda el ile bir boyut seçin. 
    - **Işletim sistemi diski**: VM için işletim sistemi (önyükleme) diskini belirtin. İşletim Sistemi diski, işletim sistemi önyükleyiciye ve yükleyiciye sahip disktir. 
    - **Kullanılabilirlik kümesi**: sanal makinenin geçişten sonra bir Azure kullanılabilirlik kümesinde olması gerekiyorsa, kümeyi belirtin. Küme, geçiş için belirttiğiniz hedef kaynak grubunda olmalıdır.

13. **Diskler**' de, VM disklerinin Azure 'da çoğaltılıp çoğaltılmayacağını belirtin ve Azure 'da disk türünü (Standart SSD/HDD veya Premium yönetilen diskler) seçin. Ardından **İleri**'ye tıklayın.
    - Diskleri çoğaltmadan çıkarabilirsiniz.
    - Diskleri çıkarırsanız bu diskler geçişten sonra Azure VM’de bulunmaz. 

14. **Çoğaltmayı gözden geçir ve başlat** bölümünde ayarları gözden geçirin ve sunuculara yönelik ilk çoğaltmayı başlatmak için **Çoğalt** üzerine tıklayın.

> [!NOTE]
> Çoğaltma ayarlarını, çoğaltma başlamadan önce dilediğiniz zaman güncelleştirebilir, > çoğaltma **makinelerini** **yönetebilirsiniz** . Çoğaltma başladıktan sonra ayarlar değiştirilemez.




## <a name="track-and-monitor"></a>İzleme ve izleme

- **Çoğalt** ' a tıkladığınızda çoğaltma Başlat işi başlar. 
- Çoğaltma Başlat işi başarıyla tamamlandığında, makineler ilk çoğaltmasını Azure 'a başlatır.
- İlk çoğaltma tamamlandıktan sonra Delta çoğaltma başlar. Şirket içi disklerde artımlı değişiklikler düzenli aralıklarla Azure 'daki çoğaltma disklerine çoğaltılır.


Portal bildirimlerinde iş durumunu izleyebilirsiniz.

![İşi izle](./media/tutorial-migrate-vmware-agent/jobs.png)

Çoğaltma durumunu, **Azure geçişi: sunucu geçişi**' nde **sunucuları** çoğaltma ' ya tıklayarak izleyebilirsiniz.
![Izleyicisi çoğaltma](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Geçiş testi çalıştırma


Delta çoğaltma başladığında, Azure 'a tam geçiş çalıştırmadan önce VM 'Ler için bir test geçişi çalıştırabilirsiniz. Bunu geçirmeden önce her makine için en az bir kez yapmanızı öneririz.

- Bir test geçişi çalıştırmak, geçiş işleminin beklendiği gibi çalıştığını denetler ve bu işlem, çalışır durumda olan şirket içi makineleri etkilemeden, çoğaltmaya devam eder. 
- Test geçişi, çoğaltılan verileri kullanarak bir Azure VM oluşturarak geçişe benzetir (genellikle Azure aboneliğinizdeki bir üretim dışı VNet 'e geçiş yapar).
- Geçişi doğrulamak, uygulama testi gerçekleştirmek ve tam geçişten önce herhangi bir sorunu gidermek için çoğaltılan test Azure VM 'yi kullanabilirsiniz.

Test geçişini aşağıdaki şekilde yapın:


1. **Geçiş hedeflerde** > **sunucuları** > **Azure geçişi: sunucu geçişi**' nde **geçirilen sunucuları test et**' e tıklayın.

     ![Geçirilen sunucuları test etme](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Test edilecek VM’yi sağ tıklayıp **Test geçişi** üzerine tıklayın.

    ![Test geçişi](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. **Test Geçişi** bölümünde, Azure VM’nin geçişten sonra bulunacağı Azure VNet’i seçin. Üretim dışı bir VNet kullanmanızı öneririz.
4. **Test Geçişi** işlemi başlar. Portal bildirimlerinde işi izleyin.
5. Geçiş bittikten sonra geçirilen Azure VM’yi, Azure portalında **Sanal Makineler** bölümünde görüntüleyin. Makine adında **Testi** şeklinde bir son ek vardır.
6. Test tamamlandıktan sonra **Makineleri çoğaltma** bölümünde Azure VM’yi sağ tıklayıp **Test geçişini temizle** üzerine tıklayın.

    ![Geçişi temizleme](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>VM’leri geçirme

Test geçişinin beklendiği gibi çalışıp çalışmadığını doğruladıktan sonra şirket içi makineleri geçirebilirsiniz.

1. Azure geçişi Proje > **sunucularında** **Azure geçişi: sunucu geçişi** > **sunucuları çoğaltma**' ya tıklayın.

    ![Sunucuları çoğaltma](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. **Makineleri çoğaltma** bölümünde VM > **Geçir** üzerine sağ tıklayın.
3. **Geçir** > **Sanal makineleri kapat ve veri kaybı olmadan planlı geçiş yap** bölümünde **Evet** > **Tamam** seçeneğini belirleyin.
    - Azure Geçişi, varsayılan olarak şirket içi VM’yi kapatır ve son çoğaltmadan bu yana gerçekleşen tüm VM değişikliklerini eşitlemek için bir isteğe bağlı çoğaltma çalıştırır. Bu, veri kaybı olmamasını sağlar.
    - VM’yi kapatmak istemiyorsanız, **Hayır** seçeneğini belirleyin
4. VM için bir geçiş işlemi başlar. Azure bildirimlerinde işlemi izleyin.
5. İşlem bittikten sonra **Sanal Makineler** sayfasında VM’yi görüntüleyebilir ve yönetebilirsiniz.

## <a name="complete-the-migration"></a>Geçişi tamamlama

1. Geçiş yapıldıktan sonra, **geçişi durdurmak**> VM 'ye sağ tıklayın. Bu, şirket içi makine için çoğaltmayı durduruyor ve VM için çoğaltma durumu bilgilerini temizler.
2. Geçirilen makinelere Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) veya [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) Aracısı 'nı yükler.
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
    - Azure Güvenlik Merkezi ile gelen trafik erişimini kilitleme ve sınırlayın [-tam zamanında yönetim](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - [Ağ Güvenlik Grupları](https://docs.microsoft.com/azure/virtual-network/security-overview) ile ağ trafiğini yönetim uç noktaları ile kısıtlayın.
    - [Azure Disk Şifrelemesi](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview)’ni dağıtarak disklerin güvenliğinin sağlanmasına yardımcı olun ve verileri hırsızlık ve yetkisiz erişime karşı koruyun.
    - [IaaS kaynaklarının güvenliğini sağlama](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) hakkında daha fazla bilgi edinin ve [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)’ni ziyaret edin.
- İzleme ve yönetim için:
    - [Azure Maliyet Yönetimi](https://docs.microsoft.com/azure/cost-management/overview)’ni dağıtarak kaynak kullanımını ve harcamayı izleyin.




 ## <a name="next-steps"></a>Sonraki adımlar

Azure bulut benimseme çerçevesindeki [bulut geçiş yolculuğunu](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) araştırın.
