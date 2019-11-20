---
title: Azure Server geçişi ile şirket içi Hyper-V VM 'lerini Azure 'a geçirme | Microsoft Docs
description: Bu makalede, şirket içi Hyper-V VM 'lerinin Azure 'a geçiş sunucusu geçişi ile Azure 'a nasıl geçirileceği açıklanır.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 755bb6d019418cf9dae22ebf7ee6a3c94af3c750
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309441"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Hyper-V VM’lerini Azure’a Geçirin 

Bu makalede, Azure geçişi sunucu geçiş aracı ile aracısız geçiş kullanarak şirket içi Hyper-V VM 'lerini Azure 'a nasıl geçirebileceğiniz gösterilmektedir.

[Azure geçişi](migrate-services-overview.md) , şirket içi uygulamalarınızı, iş yüklerinizi ve özel/genel bulut VM 'lerini Azure 'a bulmayı, değerlendirmeyi ve geçirmeyi izlemek için bir merkezi Merkez sağlar. Hub, değerlendirme ve geçiş için Azure geçiş araçları ve ayrıca üçüncü taraf bağımsız yazılım satıcısı (ISV) teklifleri sağlar.

Bu öğretici, Azure geçiş sunucusu değerlendirmesini ve geçişini kullanarak Hyper-V ' y i değerlendirmek ve Azure 'a geçirmek için kullanılan bir serinin üçüncü bir seridir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:


> [!div class="checklist"]
> * Azure 'u ve şirket içi Hyper-V ortamınızı hazırlayın
> * Kaynak ortamı ayarlayın ve bir çoğaltma gereci dağıtın.
> * Hedef environ'yi ayarlayın.
> * Çoğaltmayı etkinleştirin.
> * Her şeyin beklendiği gibi çalıştığından emin olmak için bir test geçişi çalıştırın.
> * Azure 'a tam geçiş gerçekleştirin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce karşılamanız gereken ön koşullar şunlardır:

1. Hyper-V geçiş mimarisini [gözden geçirin](migrate-architecture.md) .
2. Geçiş için Azure ve Hyper-V ayarlamak üzere bu serideki [ilk öğreticiyi doldurun](tutorial-prepare-hyper-v.md) . İlk öğreticide şunları yapabilirsiniz:
    - [Azure](tutorial-prepare-hyper-v.md#prepare-azure) 'u geçiş için hazırlayın.
    - [Şirket içi ortamı](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) geçiş için hazırlayın.
3. Hyper-V sanal makinelerini, Azure geçişi sunucu değerlendirmesini kullanarak Azure 'a geçirmeden önce değerlendirmeyi denemenizi öneririz. Bunu yapmak için, bu serinin [ikinci öğreticisini doldurun](tutorial-assess-hyper-v.md) . Bir değerlendirmeyi denemenizi öneririz, ancak VM 'Leri geçirmeden önce bir değerlendirme çalıştırmanız gerekmez.
4. Azure hesabınızın sanal makine katılımcısı rolüne atandığından emin olun, böylece şu izinlere sahip olursunuz:

    - Seçilen kaynak grubunda sanal makine oluşturma.
    - Seçilen sanal ağda sanal makine oluşturma.
    - Azure yönetilen diskine yazın.   
5. [Bir Azure ağı kurun](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Azure 'a geçiş yaptığınızda, oluşturulan Azure VM 'Leri, geçişi ayarlarken belirttiğiniz bir Azure ağına birleştirilir.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Azure geçiş sunucusu geçiş aracını ekleme

Hyper-V VM 'lerini değerlendirmek için ikinci öğreticiyi izmediyseniz, bir Azure geçişi projesi ayarlamak için [Bu yönergeleri izlemeniz](how-to-add-tool-first-time.md) ve Azure geçişi sunucusu geçiş aracını projeye eklemeniz gerekir.

İkinci öğreticiyi takip ediyorsanız ve zaten bir Azure geçişi proje kurulumu varsa, Azure geçişi sunucusu geçiş aracını şu şekilde ekleyin:

1. Azure geçişi projesinde **Genel Bakış ' a**tıklayın. 
2. **Bul, değerlendir ve geçiş sunucuları**' nda, **sunucuları değerlendir ve geçir**' e tıklayın.
3. **Geçiş araçları**' nda, geçişe **hazır olduğunuzda bir geçiş aracı eklemek Için buraya tıklayın ' ı**seçin.

    ![Araç seçin](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Araçlar listesinde Azure geçişi ' ni **seçin: Sunucu geçişi** > **ekleme aracı**

    ![Sunucu geçiş aracı](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Azure geçişi gereç 'yi ayarlama

Azure geçişi sunucu geçişi basit bir Hyper-V sanal makine gereci çalıştırır.

- Gereç, VM bulma işlemini gerçekleştirir ve VM meta verilerini ve performans verilerini Azure geçişi sunucu geçişine iletir.
- Aynı gereç, Azure geçişi sunucu değerlendirmesi aracı tarafından da kullanılır.

Gereci ayarlamak için:
- Hyper-V VM 'lerini değerlendirmek için ikinci öğreticiyi izlediyseniz, bu öğreticide zaten gereci ayarlamış olursunuz.
- Bu öğreticiyi izlemeden, gereci şimdi ayarlamanız gerekir. Bunu yapmak için şunları yapın: 

    - Azure portal sıkıştırılmış bir Hyper-V VHD 'sini indirin.
    - Gereci oluşturun ve Azure geçişi sunucu değerlendirmesi 'ne bağlanıp bağlanamadığından emin olun. 
    - Gereci ilk kez yapılandırın ve Azure geçişi projesi ile kaydedin.

    Gereci ayarlamak için [Bu makaledeki](how-to-set-up-appliance-hyper-v.md) yönergeleri izleyin.

## <a name="prepare-hyper-v-hosts"></a>Hyper-V konakları hazırlama

1. Azure geçişi Proje > **sunucularında** **Azure geçişi: Sunucu geçişi**için **bul**' a tıklayın.
2. Makinelerde bulunan makineler**sanallaştırılmış mı?** , **Hyper-V ile Evet '** i seçin. > 
3. **Hedef bölge**' de, makineleri geçirmek istediğiniz Azure bölgesini seçin.
6. **Geçiş için hedef bölgenin bölge adı olduğunu onaylayın**' i seçin.
7. **Kaynak oluştur**' a tıklayın. Bu, arka planda bir Azure Site Recovery Kasası oluşturur.
    - Azure geçişi sunucu geçişi ile geçiş zaten ayarladıysanız, kaynaklar daha önce ayarlandığı için bu seçenek görünmez.
    - Bu düğmeye tıkladıktan sonra bu proje için hedef bölgeyi değiştiremezsiniz.
    - Sonraki tüm geçişler bu bölgedir.
    
8. **Hyper-v ana bilgisayar sunucularını hazırlama**bölümünde, Hyper-v çoğaltma sağlayıcısını ve kayıt anahtarı dosyasını indirin.
    - Kayıt anahtarı, Hyper-V konağını Azure geçişi sunucu geçişine kaydetmek için gereklidir.
    - Anahtar, oluşturulduktan sonra beş gün boyunca geçerlidir.

    ![Sağlayıcıyı ve anahtarı indir](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Sağlayıcı kurulum dosyasını ve kayıt anahtarı dosyasını, çoğaltmak istediğiniz VM 'Leri çalıştıran her Hyper-V konağına (veya küme düğümüne) kopyalayın.
5. Sonraki yordamda açıklandığı gibi, her konakta sağlayıcı kurulum dosyasını çalıştırın.
6. Sağlayıcıyı konaklara yükledikten sonra, **bulma makineler**bölümünde **kaydı Sonlandır**' ı tıklatın.

    ![Kaydı Sonlandır](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Kayıt tamamlandıktan sonra, bulunan VM 'Lerin Azure geçişi sunucu geçişi 'nde görünene kadar 15 dakika sürebilir. VM 'Ler keşfedildiğinde, **bulunan sunucular** sayılır.

![Bulunan sunucular](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Hyper-V konaklarının kaydını yapın

İndirilen kurulum dosyasını (AzureSiteRecoveryProvider. exe) ilgili her Hyper-V konağına yükleme.

1. Sağlayıcı kurulum dosyasını her bir konakta veya küme düğümünde çalıştırın.
2. Sağlayıcı kurulum sihirbazında **Microsoft Update**>, sağlayıcı güncelleştirmelerini denetlemek için Microsoft Update kullanmayı tercih edin.
3. **Yükleme**bölümünde, sağlayıcı ve aracı için varsayılan yükleme konumunu kabul edin ve **yükleme**' yi seçin.
4. Yükleme sonrasında, Kayıt Sihirbazı 'nda **kasa ayarları**>, **Araştır**' ı seçin ve **anahtar dosyasında**, indirdiğiniz kasa anahtarı dosyasını seçin.
5. **Ara sunucu ayarları**' nda, konakta çalışan sağlayıcının İnternet 'e nasıl bağlandığını belirtin.
    - Gereç bir proxy sunucusunun arkasında bulunuyorsa, proxy ayarlarını belirtmeniz gerekir.
    - Proxy adını veya **http://ip-address** **http://FQDN** olarak belirtin. HTTPS proxy sunucuları desteklenmez.
   

6. Sağlayıcının [gereken URL 'lere](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access)ulaşabildiğinizden emin olun.
7. **Kayıt**bölümünde, ana bilgisayar kaydedildikten sonra **son**' a tıklayın.

## <a name="replicate-hyper-v-vms"></a>Hyper-V VM 'lerini çoğaltma

Bulma işlemi tamamlandıktan sonra, Hyper-V VM 'lerinin Azure 'a çoğaltılmasını başlatabilirsiniz.

> [!NOTE]
> En fazla 10 makineyi birbirine çoğaltabilirsiniz. Daha fazla çoğaltma yapmanız gerekiyorsa bunları aynı anda 10 ' da çoğaltın.

1. Azure Geçişi projesi > **Sunucular**, **Azure Geçişi: Sunucu Geçişi** bölümünde **Çoğalt**’a tıklayın.
2. > **Kaynak ayarları** >  **Çoğalt**,**makineleriniz sanallaştırılmış mı?** , **Hyper-V ile Evet '** i seçin. Ardından **İleri: Sanal makineler**.
3. **Sanal makineler** bölümünde çoğaltmak istediğiniz makineleri seçin.
    - VM’ler için bir değerlendirme çalıştırırsanız değerlendirme sonuçlarından VM boyutlandırma ve disk türü (premium/standart) önerilerini uygulayabilirsiniz. Bunu yapmak için **Azure Geçişi değerlendirmesinden geçiş ayarları içe aktarılsın mı?** bölümünde **Evet**’i seçin.
    - Bir değerlendirme çalıştırmadıysanız veya değerlendirme ayarlarını kullanmak istemiyorsanız **Hayır**’ı seçin.
    - Değerlendirmeyi kullanmayı seçtiyseniz VM grubunu ve değerlendirme adını belirleyin.

        ![Değerlendirme seçme](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. VM’leri **Sanal makineler** bölümünde gerektiği şekilde arayın ve geçirmek istediğiniz her bir VM’yi seçin. Ardından İleri ' **ye tıklayın: Hedef ayarları** seçeneğine tıklayın.

    ![VM 'Leri seçin](./media/tutorial-migrate-hyper-v/select-vms.png)

5. **Hedef ayarlar**' da geçireceğiniz hedef bölgeyi, aboneliğinizi ve Azure VM 'lerinin geçişten sonra bulunacağı kaynak grubunu seçin.
7. **Çoğaltma depolama hesabı**' nda, çoğaltılan verilerin Azure 'Da depolanacağı Azure Storage hesabını seçin.
8. **Sanal ağ**' ı seçin ve Azure VM 'lerinin geçişten sonra katılacağını Azure VNET/subnet ' i seçin.
9. **Azure Hibrit Avantajı**’nda:

    - Azure Hibrit Avantajı’nı uygulamak istemiyorsanız **Hayır**’ı seçin. Ardından **İleri**'ye tıklayın.
    - Etkin Yazılım Güvencesi veya Windows Server abonelikleri kapsamında olan Windows Server makineleriniz varsa ve avantajı geçirdiğiniz makinelere uygulamak istiyorsanız **Evet**’i seçin. Ardından **İleri**'ye tıklayın.

    ![Hedef ayarları](./media/tutorial-migrate-hyper-v/target-settings.png)

10. **İşlem** bölümünde VM adını, boyutunu, İşletim Sistemi disk türünü ve kullanılabilirlik kümesini gözden geçirin. VM’ler [Azure gereksinimleriyle](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)uyumlu olmalıdır.

    - **VM boyutu**: Değerlendirme önerilerini kullanıyorsanız VM boyutu açılan listesi, önerilen boyutu içerir. Aksi takdirde Azure Geçişi, Azure aboneliğindeki en yakın eşleşmeye göre bir boyut seçer. Alternatif olarak **Azure VM boyutu** ’nda el ile bir boyut seçin. 
    - **İşletim Sistemi diski**: VM için İşletim Sistemi (önyükleme) diskini belirtin. İşletim Sistemi diski, işletim sistemi önyükleyiciye ve yükleyiciye sahip disktir. 
    - **Kullanılabilirlik kümesi**: VM’nin geçişten sonra bir Azure kullanılabilirlik kümesinde bulunması gerekiyorsa kümeyi belirtin. Küme, geçiş için belirttiğiniz hedef kaynak grubunda olmalıdır.

    ![VM işlem ayarları](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. **Diskler** bölümünde, VM disklerinin Azure’a çoğaltılıp çoğaltılmayacağını belirtin ve Azure’da disk türünü (Standart SSD/HDD veya premium yönetilen diskler) seçin. Ardından **İleri**'ye tıklayın.
    - Diskleri çoğaltmadan çıkarabilirsiniz.
    - Diskleri çıkarırsanız bu diskler geçişten sonra Azure VM’de bulunmaz. 

    ![Diskler](./media/tutorial-migrate-hyper-v/disks.png)

10. **Çoğaltmayı gözden geçir ve başlat** bölümünde ayarları gözden geçirin ve sunuculara yönelik ilk çoğaltmayı başlatmak için **Çoğalt** üzerine tıklayın.

> [!NOTE]
> Çoğaltma başlamadan önce **makineleri** > **Çoğaltmayı Yönet** bölümünde çoğaltma ayarlarını dilediğiniz zaman güncelleştirebilirsiniz. Çoğaltma başladıktan sonra ayarlar değiştirilemez.

### <a name="provisioning-for-the-first-time"></a>İlk kez sağlama

Azure geçişi projesinde çoğaltmakta olduğunuz ilk sanal makine ise, Azure geçişi sunucu geçişi, bu kaynakları projeyle aynı kaynak grubunda otomatik olarak sağlar.

- **Hizmet veri yolu**: Azure geçişi sunucu geçişi, hizmet veri yolunu kullanarak, yüklemeye çoğaltma düzenleme iletileri gönderir.
- **Ağ geçidi depolama hesabı**: Sunucu geçişi, çoğaltılan VM 'Ler hakkında durum bilgilerini depolamak için ağ geçidi depolama hesabını kullanır.
- **Günlük depolama hesabı**: Azure geçişi gereci, VM 'Ler için çoğaltma günlüklerini bir günlük depolama hesabına yükler. Azure geçişi, çoğaltma bilgilerini çoğaltma tarafından yönetilen disklere uygular.
- **Anahtar Kasası**: Azure geçişi gereci, hizmet veri yolu için bağlantı dizelerini yönetmek için anahtar kasasını ve Çoğaltmada kullanılan depolama hesapları için erişim anahtarlarını kullanır. Hazırlandığınızda, anahtar kasasının depolama hesabına erişmesi için gereken izinleri ayarlamış olmanız gerekir. [Bu Izinleri gözden geçirin](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>İzleme ve izleme


- **Çoğalt** ' a tıkladığınızda çoğaltma Başlat işi başlar. 
- Çoğaltma Başlat işi başarıyla tamamlandığında, makineler ilk çoğaltmasını Azure 'a başlatır.
- İlk çoğaltma tamamlandıktan sonra Delta çoğaltma başlar. Şirket içi disklerde artımlı değişiklikler düzenli aralıklarla Azure 'a çoğaltılır.

Portal bildirimlerinde iş durumunu izleyebilirsiniz.

Çoğaltma durumunu, **Azure geçişi 'nde** sunucuları **çoğaltma ' ya tıklayarak izleyebilirsiniz: Sunucu geçişi**.
![Çoğaltmayı izleme](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Geçiş testi çalıştırma


Delta çoğaltma başladığında, Azure 'a tam geçiş çalıştırmadan önce VM 'Ler için bir test geçişi çalıştırabilirsiniz. Bunu geçirmeden önce her makine için en az bir kez yapmanızı öneririz.

- Bir test geçişi çalıştırmak, geçiş işleminin beklendiği gibi çalıştığını denetler ve bu işlem, çalışır durumda olan şirket içi makineleri etkilemeden, çoğaltmaya devam eder. 
- Test geçişi, çoğaltılan verileri kullanarak bir Azure VM oluşturarak geçişe benzetir (genellikle Azure aboneliğinizdeki bir üretim dışı VNet 'e geçiş yapar).
- Geçişi doğrulamak, uygulama testi gerçekleştirmek ve tam geçişten önce herhangi bir sorunu gidermek için çoğaltılan test Azure VM 'yi kullanabilirsiniz.

Test geçişini aşağıdaki şekilde yapın:


1. **Geçiş hedefleri** > **sunucularında** > **Azuregeçişi: Sunucu Geçişi** bölümünde **Geçirilen sunucuları test et** üzerine tıklayın.

     ![Geçirilen sunucuları test etme](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Test edilecek VM’yi sağ tıklayıp **Test geçişi** üzerine tıklayın.

    ![Test geçişi](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. **Test Geçişi** bölümünde, Azure VM’nin geçişten sonra bulunacağı Azure VNet’i seçin. Üretim dışı bir VNet kullanmanızı öneririz.
4. **Test Geçişi** işlemi başlar. Portal bildirimlerinde işi izleyin.
5. Geçiş bittikten sonra geçirilen Azure VM’yi, Azure portalında **Sanal Makineler** bölümünde görüntüleyin. Makine adında **Testi** şeklinde bir son ek vardır.
6. Test tamamlandıktan sonra **Makineleri çoğaltma** bölümünde Azure VM’yi sağ tıklayıp **Test geçişini temizle** üzerine tıklayın.

    ![Geçişi temizleme](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>VM’leri geçirme

Test geçişinin beklendiği gibi çalışıp çalışmadığını doğruladıktan sonra şirket içi makineleri geçirebilirsiniz.

1. Azure Geçişi projesi > **Sunucular** > **Azure Geçişi: Sunucu Geçişi** bölümünde, **Sunucuları çoğaltma** üzerine tıklayın.

    ![Sunucuları çoğaltma](./media/tutorial-migrate-hyper-v/replicate-servers.png)

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

- Daha fazla esneklik için:
    - Azure Backup hizmetini kullanarak Azure sanal makinelerini yedekleyip verileri güvende tutun. [Daha fazla bilgi edinin](../backup/quick-backup-vm-portal.md).
    - Site Recovery ile Azure sanal makinelerini ikincil bölgeye çoğaltarak iş yüklerinin çalışmaya devam etmesini ve sürekli kullanılabilir olmasını sağlayın. [Daha fazla bilgi edinin](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Daha fazla güvenlik için:
    - Azure Güvenlik Merkezi ile gelen trafik erişimini kilitleme ve sınırlayın [-tam zamanında yönetim](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - [Ağ Güvenlik Grupları](https://docs.microsoft.com/azure/virtual-network/security-overview) ile ağ trafiğini yönetim uç noktaları ile kısıtlayın.
    - [Azure Disk Şifrelemesi](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview)’ni dağıtarak disklerin güvenliğinin sağlanmasına yardımcı olun ve verileri hırsızlık ve yetkisiz erişime karşı koruyun.
    - [IaaS kaynaklarının güvenliğini sağlama](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) hakkında daha fazla bilgi edinin ve [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)’ni ziyaret edin.
- İzleme ve yönetim için:
-  [Azure Maliyet Yönetimi](https://docs.microsoft.com/azure/cost-management/overview)’ni dağıtarak kaynak kullanımını ve harcamayı izleyin.


## <a name="next-steps"></a>Sonraki adımlar

Azure bulut benimseme çerçevesindeki [bulut geçiş yolculuğunu](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) araştırın.
