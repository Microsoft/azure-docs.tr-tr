---
title: Azure Site Recovery kullanarak Azure Stack VM 'Leri Azure 'a çoğaltma | Microsoft Docs
description: Azure Site Recovery hizmetiyle Azure Stack VM 'Ler için Azure 'da olağanüstü durum kurtarmayı ayarlamayı öğrenin.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 300b239de74c7d21e03eb0c3b059663b5074964c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728394"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Azure Stack VM'lerini Azure'a çoğaltma

Bu makalede, [Azure Site Recovery hizmetini](site-recovery-overview.md)kullanarak olağanüstü durum kurtarma Azure Stack VM 'lerinin Azure 'a nasıl ayarlanacağı gösterilmektedir.

Site Recovery iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejinize katkıda bulunur. Hizmet, sanal makine iş yüklerinizin beklenen durumlarda kullanılabilir kalmasını ve beklenmeyen kesintiler meydana gelmesini sağlar.

- Site Recovery, VM 'lerin Azure depolama 'ya çoğaltılmasını düzenler ve yönetir.
- Birincil sitenizde bir kesinti oluştuğunda, Azure 'a yük devretmek için Site Recovery kullanırsınız.
- Yük devretmede, Azure VM 'Leri depolanan VM verilerinden oluşturulur ve kullanıcılar bu Azure VM 'lerinde çalışan iş yüklerine erişmeye devam edebilir.
- Her şey tekrar çalışır duruma geldiğinde Azure sanal makinelerini birincil sitenize geri yükleyebilir ve Azure Storage 'a çoğaltmayı yeniden başlatabilirsiniz.


Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * **1. Adım: Azure Stack VM 'lerini çoğaltma Için hazırlayın**. VM 'Lerin Site Recovery gereksinimleriyle uyumlu olup olmadığını denetleyin ve Site Recovery Mobility hizmetinin yüklenmesine hazırlanın. Bu hizmet, çoğaltmak istediğiniz her sanal makineye yüklenir.
> * **2. Adım: bir kurtarma hizmetleri Kasası ayarlama**. Site Recovery için bir kasa ayarlayın ve ne çoğaltmak istediğinizi belirtin. Site Recovery bileşenleri ve eylemleri kasada yapılandırılır ve yönetilir.
> * **3. Adım: kaynak çoğaltma ortamını ayarlama**. Bir Site Recovery yapılandırma sunucusu ayarlayın. Yapılandırma sunucusu, Site Recovery gereken tüm bileşenleri çalıştıran tek bir Azure Stack sanal sunucusudur. Yapılandırma sunucusunu ayarladıktan sonra, kasayı kasaya kaydedersiniz.
> * **4. Adım: hedef çoğaltma ortamını ayarlama**. Azure hesabınızı ve kullanmak istediğiniz Azure Depolama hesabını ve ağını seçin. Çoğaltma sırasında VM verileri Azure depolama 'ya kopyalanır. Yük devretmeden sonra Azure VM 'Leri belirtilen ağa birleştirilir.
> * **5. Adım: çoğaltmayı etkinleştirme**. Çoğaltma ayarlarını yapılandırın ve VM 'Ler için çoğaltmayı etkinleştirin. Çoğaltma etkinleştirildiğinde Mobility hizmeti bir VM 'ye yüklenir. Site Recovery VM 'nin ilk çoğaltmasını gerçekleştirir ve devam eden çoğaltma başlar.
> * **6. Adım: olağanüstü durum kurtarma detaya gitme**: çoğaltma çalışır duruma geçtikten sonra, bir detaya gitme çalıştırarak yük devretmenin beklendiği gibi çalıştığını doğrularsınız. Detaya gitmeyi başlatmak için Site Recovery bir yük devretme testi çalıştırırsınız. Yük devretme testi üretim ortamınızı etkilemez.

Bu adımlar tamamlandığında, Azure 'da ve gerektiğinde tam yük devretme çalıştırabilirsiniz.

## <a name="architecture"></a>Mimari

![Diyagramda, kiracı abonelikleriyle ilişkili bulutlarda iki kiracı için kurtarma hizmetleri kasalarının her ikisi de ortak bir Azure Stack altyapısında gösterilmektedir.](./media/azure-stack-site-recovery/architecture.png)

**Konum** | **Bileşen** |**Ayrıntılar**
--- | --- | ---
**Yapılandırma Sunucusu** | Tek bir Azure Stack VM üzerinde çalışır. | Her abonelikte bir yapılandırma sunucusu sanal makinesi ayarlarsınız. Bu VM aşağıdaki Site Recovery bileşenleri çalıştırır:<br/><br/> -Yapılandırma sunucusu: şirket içi ve Azure arasındaki iletişimleri koordine eder ve veri çoğaltmasını yönetir. -İşlem sunucusu: çoğaltma ağ geçidi olarak davranır. Çoğaltma verilerini alır, önbelleğe alma, sıkıştırma ve şifreleme ile iyileştirir; ve bunu Azure depolama 'ya gönderir.<br/><br/> Çoğaltmak istediğiniz VM 'Ler aşağıda belirtilen sınırları aşarsa, ayrı bir tek başına işlem sunucusu ayarlayabilirsiniz. [Daha fazla bilgi edinin](vmware-azure-set-up-process-server-scale.md).
**Mobility hizmeti** | Çoğaltmak istediğiniz her sanal makineye yüklenir. | Bu makaledeki adımlarda, çoğaltma etkinleştirildiğinde Mobility hizmetinin bir VM 'ye otomatik olarak yüklenmesi için bir hesap hazırlarız. Hizmeti otomatik olarak yüklemek istemiyorsanız kullanabileceğiniz çeşitli yöntemler vardır. [Daha fazla bilgi edinin](vmware-azure-install-mobility-service.md).
**Azure** | Azure 'da bir kurtarma hizmetleri Kasası, bir depolama hesabı ve bir sanal ağ gerekir. |  Çoğaltılan veriler depolama hesabında depolanır. Yük devretme gerçekleştiğinde Azure sanal makineleri Azure ağa eklenir.


Çoğaltma, aşağıdaki gibi çalışmaktadır:

1. Kasada, çoğaltma kaynağını ve hedefini belirtin, yapılandırma sunucusunu ayarlayın, bir çoğaltma ilkesi oluşturur ve çoğaltmayı etkinleştirin.
2. Mobility hizmeti makineye yüklendi (anında yüklemeyi kullandıysanız) ve makineler çoğaltma ilkesine uygun olarak çoğaltmaya başlar.
3. Sunucu verilerinin ilk kopyası Azure Storage 'a çoğaltılır.
4. İlk çoğaltma tamamlandıktan sonra, Delta değişikliklerinin Azure 'a çoğaltılması başlar. Bir makine için izlenen değişiklikler bir .hrl dosyasında saklanır.
5. Yapılandırma sunucusu, Azure ile çoğaltma yönetimini düzenler (bağlantı noktası HTTPS 443 giden).
6. İşlem sunucusu kaynak makinelerden veri alır, bunları iyileştirir ve şifreler ve Azure Storage 'a (bağlantı noktası 443 giden) gönderir.
7. Çoğaltılan makineler, çoğaltma yönetimi için yapılandırma sunucusu (bağlantı noktası HTTPS 443 gelen) ile iletişim kurar. Makineler, çoğaltma verilerini işlem sunucusuna gönderir (bağlantı noktası HTTPS 9443 gelen-değiştirilebilir).
8. Trafik İnternet üzerinden Azure depolama genel uç noktalarına çoğaltılır. Alternatif olarak, Azure ExpressRoute genel eşliğini kullanabilirsiniz. Trafiğin siteden siteye bir VPN aracılığıyla şirket içi bir siteden Azure’a çoğaltılması desteklenmez.

## <a name="prerequisites"></a>Önkoşullar

Bu senaryoyu ayarlamanız için gerekenler aşağıda verilmiştir.

**Gereksinim** | **Ayrıntılar**
--- | ---
**Azure abonelik hesabı** | Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.
**Azure hesabı izinleri** | Kullandığınız Azure hesabı için izinler gerekir:<br/><br/> -Kurtarma hizmeti Kasası oluşturma<br/><br/> -Senaryo için kullandığınız kaynak grubunda ve sanal ağda bir sanal makine oluşturun<br/><br/> -Belirttiğiniz depolama hesabına yazın<br/><br/> Şunlara dikkat edin:<br/><br/> -Bir hesap oluşturursanız, aboneliğinizin yöneticisi olursunuz ve tüm eylemleri gerçekleştirebilirsiniz.<br/><br/> -Mevcut bir aboneliği kullanıyorsanız ve yönetici değilseniz, size sahip veya katkıda bulunan izinleri atamak için yönetici ile çalışmanız gerekir.<br/><br/> -Daha ayrıntılı izinlere ihtiyacınız varsa [Bu makaleye](site-recovery-role-based-linked-access-control.md)bakın.
**Azure Stack VM** | Kiracı aboneliğinde, Site Recovery yapılandırma sunucusu olarak dağıtılacak bir Azure Stack VM olması gerekir.


### <a name="prerequisites-for-the-configuration-server"></a>Yapılandırma sunucusu önkoşulları

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]



## <a name="step-1-prepare-azure-stack-vms"></a>1. Adım: Azure Stack VM 'Leri hazırlama

### <a name="verify-the-operating-system"></a>İşletim sistemini doğrulama

VM 'Lerin tabloda özetlenen işletim sistemlerinden birini çalıştırdığından emin olun.


**İşletim sistemi** | **Ayrıntılar**
--- | ---
**64 bit Windows** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (SP1)
**CentOS** | 5,2-5,11, 6,1 6,9, 7,0 7,3
**Ubuntu** | 14,04 LTS Server, 16,04 LTS Server. [Desteklenen çekirdekleri](vmware-physical-azure-support-matrix.md#ubuntu-kernel-versions) gözden geçirme

### <a name="prepare-for-mobility-service-installation"></a>Mobility hizmeti yüklemesine hazırlanma

Çoğaltmak istediğiniz her sanal makinenin Mobility hizmetinin yüklü olması gerekir. Çoğaltma etkinleştirildiğinde, işlem sunucusunun sanal makineye hizmeti otomatik olarak yüklemesi için VM ayarlarını doğrulayın.

#### <a name="windows-machines"></a>Windows makineleri

- Çoğaltmayı etkinleştirmek istediğiniz VM ile ağ bağlantısına ve işlem sunucusunu çalıştıran makineye (varsayılan olarak bu yapılandırma sunucusu VM 'dir) ağ bağlantısı gerekir.
- Çoğaltmayı etkinleştirdiğiniz makinede yönetici haklarına sahip bir hesaba (etki alanı veya yerel) sahip olmanız gerekir.
    - Site Recovery ayarlarken bu hesabı belirtirsiniz. Sonra işlem sunucusu, çoğaltma etkinleştirildiğinde Mobility hizmetini yüklemek için bu hesabı kullanır.
    - Bu hesap yalnızca anında yükleme için Site Recovery tarafından ve Mobility hizmetini güncelleştirmek için kullanılır.
    - Bir etki alanı hesabı kullanmıyorsanız, sanal makinede uzak kullanıcı erişim denetimini devre dışı bırakmanız gerekir:
        - Kayıt defterinde HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System altında **LocalAccountTokenFilterPolicy** DWORD değerini oluşturun.
        - Değeri 1 olarak ayarlayın.
        - Bunu yapmak için komut isteminde şunu yazın: **reg ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System/V LocalAccountTokenFilterPolicy/t REG_DWORD/d 1**.
- Çoğaltmak istediğiniz VM 'deki Windows güvenlik duvarında dosya ve yazıcı paylaşımına ve WMI 'ye izin verin.
    - Bunu yapmak için, **WF. msc** ' yi çalıştırarak Windows Güvenlik Duvarı konsolunu açın. **Gelen kuralları**  >  **Yeni kural**' a sağ tıklayın. **Önceden tanımlanmış**' i seçin ve listeden **dosya ve yazıcı paylaşımı** ' nı seçin. Sihirbazı tamamladıktan sonra bağlantı > **sona ermesini** sağlamak için seçin.
    - Etki alanı bilgisayarları için, bunu yapmak için bir GPO kullanabilirsiniz.


#### <a name="linux-machines"></a>Linux makineleri

- Linux bilgisayarı ile işlem sunucusu arasında ağ bağlantısı olduğundan emin olun.
- Çoğaltmayı etkinleştirdiğiniz makinede, kaynak Linux sunucusunda bir kök kullanıcı olan bir hesaba ihtiyacınız vardır:
    - Site Recovery ayarlarken bu hesabı belirtirsiniz. Sonra işlem sunucusu, çoğaltma etkinleştirildiğinde Mobility hizmetini yüklemek için bu hesabı kullanır.
    - Bu hesap yalnızca anında yükleme için Site Recovery tarafından ve Mobility hizmetini güncelleştirmek için kullanılır.
- Kaynak Linux sunucusundaki /etc/hosts dosyasında, yerel ana bilgisayar adını tüm ağ bağdaştırıcıları ile ilişkili IP adreslerine eşleyen girişler olduğunu denetleyin.
- Çoğaltmak istediğiniz bilgisayara en son openssh, openssh-server, openssl paketlerini yükleyin.
- Secure Shell’in (SSH) etkin olduğundan ve bağlantı noktası 22’de çalıştırıldığından emin olun.
- SFTP alt sistemi ve parola ile kimlik doğrulamasını sshd_config dosyasında etkinleştirin:
    1. Bunu yapmak için kök olarak oturum açın.
    2. /Etc/ssh/sshd_config dosyasında, **Passwordaduthentication** ile başlayan satırı bulun. Satırı açıklama durumundan çıkarın ve değerini **yes** (evet) olarak değiştirin.
    3. **Subsystem** ile başlayan satırı bulun ve açıklama durumundan çıkarın.

        ![Linux Mobility hizmeti](./media/azure-stack-site-recovery/linux-mobility.png)

    4. sshd hizmetini yeniden başlatın.


### <a name="note-the-vm-private-ip-address"></a>VM özel IP adresini aklınızda

Çoğaltmak istediğiniz her makine için IP adresini bulun:

1. Azure Stack portalında VM 'ye tıklayın.
2. **Kaynak** menüsünde, **ağ arabirimleri**' ne tıklayın.
3. Özel IP adresi ' ni aklınızda edin.

    ![Özel IP adresi](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>2. Adım: kasa oluşturma ve çoğaltma hedefi seçme

1. Azure Portal **kaynak**  >  **yönetimi araçları**  >  **yedeklemesi ve Site Recovery** oluştur ' u seçin.
2. **Ad** alanına kasayı tanımlamak için kolay bir ad girin.
3. **Kaynak grubu**' nda, bir kaynak grubu oluşturun veya seçin. **ContosoRG** kullandık.
4. **Konum** alanına Azure bölgesini girin. **Batı Avrupa** kullanacağız.
5. Panodan kasaya hızlıca erişmek için **panoya sabitle**  >  **Oluştur**' u seçin.

   ![Yeni kasa oluştur](./media/azure-stack-site-recovery/new-vault-settings.png)

   Yeni kasa **Pano**  >  **tüm kaynaklar**' da ve ana **Kurtarma Hizmetleri kasaları** sayfasında görüntülenir.

### <a name="select-a-replication-goal"></a>Çoğaltma hedefi seçme

1. **Kurtarma Hizmetleri** kasalarında bir kasa adı belirtin >. **Contosovmkasasını** kullanıyoruz.
2. **Başlarken** bölümünde Site Recovery’yi seçin. Daha sonra **Altyapıyı Hazırlama**’yı seçin.
3. Makinelerinizin bulunduğu **koruma hedefi** bölümünde  >   **Şirket içi**' ı seçin.
4. **Makinelerinizi nereye çoğaltmak istiyorsunuz** bölümünde **Azure’a** seçeneğini belirleyin.
5. **Makinelerinizde sanallaştırılmış**, **sanallaştırılmış değil**' i seçin. Ardından **Tamam**’ı seçin.

    ![Koruma hedefi](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>3. Adım: kaynak ortamını ayarlama

Yapılandırma sunucusu makinesini ayarlayın, kasaya kaydedin ve çoğaltmak istediğiniz makineleri bulun.

1. **Altyapı kaynağını hazırla**' ya tıklayın  >  .
2. **Kaynağı hazırla** bölümünde **+ Yapılandırma Sunucusu**’na tıklayın.

    ![+ Yapılandırma sunucusu iletişim kutusunun şu iletiyle birlikte "bir tane kurmak için yukarıdaki komut çubuğunda + yapılandırma sunucusu ' na tıklayın...](./media/azure-stack-site-recovery/plus-config-srv.png)

3. **Sunucu Ekle**' de, **sunucu türü**' nde **Configuration Server** ' ın göründüğünden emin olun.
5. Site Recovery Birleşik kurulum yükleme dosyasını indirin.
6. Kasa kayıt anahtarını indirin. Birleşik kurulumu çalıştırdığınızda kayıt anahtarı gereklidir. Anahtar, oluşturulduktan sonra beş gün boyunca geçerlidir.

    ![Sunucu türü yapılandırma sunucusu olarak ayarlanan Sunucu Ekle iletişim kutusunun ekran görüntüsü ve kasa kayıt anahtarını Indir düğmesi vurgulanır.](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Azure Site Recovery Birleşik kurulum 'U Çalıştır

Yapılandırma sunucusunu yüklemek ve kaydetmek için, yapılandırma sunucusu için kullanmak istediğiniz VM 'ye bir RDP bağlantısı yapın ve Birleşik kurulum 'U çalıştırın.

Başlamadan önce, başlamadan önce saatin sanal makinede [bir saat sunucusuyla eşitlendiğinden](/windows-server/networking/windows-time-service/windows-time-service-top) emin olun. Süre, yerel saatten beş dakikadan fazla olursa yükleme başarısız olur.

Şimdi yapılandırma sunucusunu yükleyebilirsiniz:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Yapılandırma sunucusu da komut satırından yüklenebilir. [Daha fazla bilgi edinin](physical-manage-configuration-server.md#install-from-the-command-line).
>
> Hesap adının portalda görünmesi 15 dakika veya daha fazla sürebilir. Hemen güncelleştirmek için **yapılandırma sunucuları**  >  **_sunucu adı_*_ > _* Refresh Server**' ı seçin.

## <a name="step-4-set-up-the-target-environment"></a>4. Adım: hedef ortamı ayarlama

Hedef kaynaklarını seçin ve doğrulayın.

1. **Altyapıyı hazırla**  >  **hedefi**' nde, kullanmak istediğiniz Azure aboneliğini seçin.
2. Hedef dağıtım modelini belirtin.
3. Site Recovery, bir veya birden çok uyumlu Azure depolama hesabınızın ve ağınızın olup olmadığını denetler. Bu bilgi bulamazsa, Sihirbazı tamamlayabilmeniz için en az bir depolama hesabı ve sanal ağ oluşturmanız gerekir.


## <a name="step-5-enable-replication"></a>5. Adım: Çoğaltmayı etkinleştirme

### <a name="create-a-replication-policy"></a>Çoğaltma ilkesi oluşturma

1. **Altyapı**  >  **çoğaltma ayarlarını** hazırla ' ya tıklayın.
2. **Çoğaltma ilkesi oluştur** bölümünde bir ilke adı belirtin.
3. **RPO eşiği** bölümünde kurtarma noktası hedefi (RPO) sınırını belirtin.
    - Çoğaltılan veriler için kurtarma noktaları, zaman kümesine uygun olarak oluşturulur.
    - Bu ayar, sürekli olan çoğaltmayı etkilemez. Yalnızca bir kurtarma noktası oluşturulmadan eşik sınırına ulaşılırsa bir uyarı yayınlar.
4. **Kurtarma noktası bekletme** bölümünde, her kurtarma noktasının ne kadar süreyle tutulacağını belirtin. Çoğaltılan VM 'Ler belirtilen zaman penceresinde herhangi bir noktaya kurtarılabilir.
5. Uygulamayla **tutarlı anlık görüntü sıklığı**' nda, uygulamayla tutarlı anlık görüntülerin ne sıklıkla oluşturulacağını belirtin.

    - Uygulamayla tutarlı bir anlık görüntü, VM içindeki uygulama verilerinin zaman içinde bir noktadaki anlık görüntüsüdür.
    - Birim Gölge Kopyası Hizmeti (VSS), anlık görüntü çekilirken VM üzerindeki uygulamaların tutarlı bir durumda olmasını sağlar.
6. İlkeyi oluşturmak için **Tamam**’ı seçin.


### <a name="confirm-deployment-planning"></a>Dağıtım planlamasını onaylama

Hemen bu adımı atlayabilirsiniz. **Dağıtım planlama** açılan listesinde Evet ' e tıklayın **, bunu yapmadım**.



### <a name="enable-replication"></a>Çoğaltmayı etkinleştirme

[1. Adım: hazırlama makinesi](#step-1-prepare-azure-stack-vms)' nde tüm görevleri tamamladığınızdan emin olun. Ardından çoğaltmayı aşağıdaki şekilde etkinleştirin:

1. **Uygulama kaynağını Çoğalt**' ı seçin  >  .
2. **Kaynak** bölümünde yapılandırma sunucusunu seçin.
3. **Makine türü**' nde **fiziksel makineler**' i seçin.
4. İşlem sunucusunu (yapılandırma sunucusu) seçin. Daha sonra, **Tamam**'a tıklayın.
5. **Hedef** bölümünde, yük devretmeden sonra VM 'leri oluşturmak istediğiniz aboneliği ve kaynak grubunu seçin. Yük devredilen VM 'Ler için kullanmak istediğiniz dağıtım modelini seçin.
6. Çoğaltılan verileri depolamak istediğiniz Azure Depolama hesabını seçin.
7. Yük devretme işleminden sonra oluşturulan Azure VM’lerin bağlandığı Azure ağını ve alt ağını seçin.
8. Koruma için seçtiğiniz tüm makinelere ağ ayarını uygulamak için **Seçili makineler için şimdi yapılandırı** seçin. Her makine için ayrı ayrı Azure ağı seçmek istiyorsanız, **daha sonra Yapılandır** ' ı seçin.
9. **Fiziksel makinelerde** **+ fiziksel makine**' ye tıklayın. Çoğaltmak istediğiniz her makinenin adını, IP adresini ve işletim sistemi türünü belirtin.

    - Makinenin iç IP adresini kullanın.
    - Genel IP adresini belirtirseniz, çoğaltma beklendiği gibi çalışmayabilir.

10. **Özellikler**  >  **Özellikleri Yapılandır** bölümünde, işlem sunucusunun, Mobility hizmetini makinede otomatik olarak yüklemek için kullanacağı hesabı seçin.
11. Çoğaltma **ayarları**  >  **çoğaltma ayarlarını yapılandır** bölümünde doğru çoğaltma ilkesinin seçili olup olmadığını denetleyin.
12. **Çoğaltmayı Etkinleştir**’e tıklayın.
13.  **Ayarlar**  >  **işleri**  >  **Site Recovery işler**' de korumayı etkinleştirme işinin ilerlemesini izleyin. **Korumayı Sonlandır** işi çalıştırıldıktan sonra makine yük devretmeye hazırız.

> [!NOTE]
> Bir VM için çoğaltma etkinleştirildiğinde Site Recovery, Mobility Hizmeti’ni yükler.
>
> Değişikliklerin geçerli olması ve portalda görüntülenmesi 15 dakika veya daha uzun sürebilir.
>
> Eklediğiniz VM 'leri izlemek için **yapılandırma sunucularındaki**  >  **son iletişim adresindeki** VM 'ler için son keşfedilen zamanı kontrol edin. Zamanlanan bulma işlemini beklemeden VM’leri eklemek için yapılandırma sunucusunu vurgulayın (seçmeyin) ve **Yenile**’yi seçin.


## <a name="step-6-run-a-disaster-recovery-drill"></a>6. Adım: olağanüstü durum kurtarma ayrıntısı çalıştırma

Her şeyin beklendiği gibi çalıştığından emin olmak için Azure 'a yük devretme testi çalıştırırsınız. Bu yük devretme üretim ortamınızı etkilemez.

### <a name="verify-machine-properties"></a>Makine özelliklerini doğrulama

Yük devretme testi çalıştırmadan önce makine özelliklerini doğrulayın ve [Azure gereksinimleriyle](vmware-physical-azure-support-matrix.md#azure-vm-requirements)uyumlu olduğundan emin olun. Özellikleri aşağıdaki şekilde görüntüleyebilir ve değiştirebilirsiniz:

1. **Korunan Öğeler**’de **Çoğaltılan Öğeler** > VM seçeneğine tıklayın.
2. **Çoğaltılan öğe** bölmesinde VM bilgileri ile sistem durumunun bir özeti ve kullanılabilen son kurtarma noktaları yer alır. Daha fazla ayrıntı görüntülemek için **Özellikler**’e tıklayın.
3. **İşlem ve ağ** bölümünde, ayarları gerektiği gibi değiştirin.

    - Azure VM adını, kaynak grubunu, hedef boyutunu, [kullanılabilirlik kümesini](../virtual-machines/windows/tutorial-availability-sets.md)ve yönetilen disk ayarlarını değiştirebilirsiniz.
    - Ayrıca, ağ ayarlarını görüntüleyebilir ve değiştirebilirsiniz. Bunlar, yük devretmeden sonra Azure VM 'nin katıldığı ağ/alt ağı ve VM 'ye atanacak IP adresini içerir.
1. **Diskler**' de, VM 'deki işletim sistemi ve veri diskleri hakkındaki bilgileri görüntüleyin.


### <a name="run-a-test-failover"></a>Yük devretme testi çalıştırma

Yük devretme testi çalıştırdığınızda şunlar olur:

1. Yük devretme için gerekli tüm koşulların karşılandığından emin olmak için bir önkoşul denetimi çalıştırılır.
2. Yük devretme, belirtilen kurtarma noktasını kullanarak verileri işler:
    - **En son işlenen**: makine, Site Recovery tarafından işlenen en son kurtarma noktasına devreder. Zaman damgası gösterilir. Bu seçenekte, verileri işlemek için zaman harcanmadığından düşük bir RTO (kurtarma süresi hedefi) sağlanır.
    - **Uygulamayla tutarlı olan en son**: makine, uygulamayla tutarlı en son kurtarma noktasına devreder.
    - **Özel**: yük devretme için kullanılan kurtarma noktasını seçin.

3. İşlenen veriler kullanılarak bir Azure VM oluşturulur.
4. Yük devretme testi, detaya gitme sırasında oluşturulan Azure VM 'Leri otomatik olarak temizleyebilir.

Bir VM için yük devretme testini aşağıdaki şekilde çalıştırın:

1. **Ayarlar**  >  **çoğaltılan öğeler**' de VM > **+ Yük devretme testi**' ne tıklayın.
2. Bu izlenecek yol için, **en son işlenen** kurtarma noktasını kullanmayı seçeceğiz.
3. **Yük devretme testi** bölümünde, hedef Azure ağını seçin.
4. Yük devretmeyi başlatmak için **Tamam**'a tıklayın.
5. Özelliklerini açmak için VM 'ye tıklayarak ilerlemeyi izleyin. Ya da, *kasa adı* ayarları   >    >  **işler**  > **Site Recovery işler**' de yük devretme testi işi ' ne tıklayın.
6. Yük devretme bittikten sonra, çoğaltma Azure VM, Azure portalı > **Sanal Makineler** bölümünde görünür. VM 'nin uygun boyutta olduğundan, doğru ağa bağlı olduğundan ve çalıştığından emin olun.
7. Şimdi Azure’da çoğaltılan sanal makineye bağlanabiliyor olmanız gerekir. [Daha fazla bilgi edinin](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
8. Yük devretme testi sırasında oluşturulan Azure sanal makinelerini silmek için, VM’de **Yük devretme testini temizle**’ye tıklayın. **Notlar**' da, test yük devretmesi ile ilgili tüm gözlemlerinizi kaydedin.

## <a name="fail-over-and-fail-back"></a>Yük devretme ve ilk duruma döndürme

Çoğaltmayı ayarladıktan ve her şeyin çalıştığından emin olmak için bir detaya gitme gerçekleştirdikten sonra, gerektiğinde Azure 'a makinelerin yükünü devreder.

Yük devretmeyi çalıştırmadan önce, yük devretmeden sonra Azure 'da makineye bağlanmak istiyorsanız, ardından başlamadan önce [bağlanmaya hazırlanın](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) .

Ardından yük devretmeyi aşağıdaki gibi çalıştırın:


1. **Ayarlar**  >  **çoğaltılan öğeler** bölümünde, **Yük devretme**> makineye tıklayın.
2. Kullanmak istediğiniz kurtarma noktasını seçin.
3. **Yük devretme testi** bölümünde, hedef Azure ağını seçin.
4. **Yük devretmeyi başlatmadan önce makineyi kapatın** seçeneğini belirleyin. Bu ayar ile, Site Recovery yük devretmeyi başlatmadan önce kaynak makineyi kapatmaya çalışır. Ancak, yük devretme, kapatılma başarısız olsa bile devam eder.
5. Yük devretmeyi başlatmak için **Tamam**'a tıklayın. **İşler** sayfasında yük devretme ilerlemesini izleyebilirsiniz.
6. Yük devretme bittikten sonra, çoğaltma Azure VM, Azure portalı > **Sanal Makineler** bölümünde görünür. Yük devretmeden sonra bağlanmayı hazırladıysanız, VM 'nin uygun boyutta olduğundan, doğru ağa bağlı olduğundan ve çalıştığından emin olun.
7. VM doğrulandıktan sonra, yük devretmeyi tamamlamak için **Yürüt** ' e tıklayın. Bu, kullanılabilir tüm kurtarma noktalarını siler.

> [!WARNING]
> Devam eden yük devretme işlemini iptal etmeyin: Yük devretme başlatılmadan önce VM çoğaltma durdurulur. Devam eden bir yük devretme işlemini iptal ederseniz yük devretme durdurulur, ancak VM yeniden çoğaltılamaz.


### <a name="fail-back-to-azure-stack"></a>Azure Stack yeniden başarısız oldu

Birincil siteniz tekrar çalışır duruma geldiğinde Azure 'dan Azure Stack 'e geri dönebilirsiniz. Bunu yapmak için [burada](/azure-stack/operator/site-recovery-failback)listelenen adımları izleyin.

## <a name="conclusion"></a>Sonuç

Bu makalede Azure Stack VM 'Leri Azure 'a çoğalttık. Çoğaltma yerinde olduğunda, Azure 'a yük devretmenin beklendiği gibi çalıştığından emin olmak için bir olağanüstü durum kurtarma detayına göz ıyoruz. Makale Ayrıca, Azure 'a tam yük devretme çalıştırmaya ve Azure Stack geri yüklemeye yönelik adımları da içerir.

## <a name="next-steps"></a>Sonraki adımlar

Yeniden başarısız olduktan sonra, sanal makineyi yeniden koruyabilir ve bunu yapmak Için tekrar Azure 'a çoğaltmaya başlayabilirsiniz. Bu makaledeki adımları tekrarlayın.