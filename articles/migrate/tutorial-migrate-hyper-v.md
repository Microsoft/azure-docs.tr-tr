---
title: Azure Server geçişi ile Hyper-V VM 'lerini Azure 'a geçirme
description: Azure Server geçişi ile şirket içi Hyper-V VM 'lerini Azure 'a geçirmeyi öğrenin
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 0072ce81fc619c39770eba52e24dc5a0c57280a6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604585"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Hyper-V VM’lerini Azure’a geçirme 

Bu makalede, [Azure geçişi: sunucu geçiş](migrate-services-overview.md#azure-migrate-server-migration-tool) aracı ile şirket içi Hyper-V VM 'lerini Azure 'a nasıl geçirebileceğiniz gösterilmektedir.

Bu öğretici, makinelerin Azure 'a nasıl değerlendirileceğini ve geçirileceğini gösteren bir serideki üçüncü bir seridir. 

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir, böylece bir kavram kanıtı hızlı bir şekilde ayarlayabilmenizi sağlayabilirsiniz. Öğreticiler mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. 

 Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure geçişi: sunucu geçiş aracı 'nı ekleyin.
> * Geçirmek istediğiniz VM 'Leri bulun.
> * VM 'Leri çoğaltmaya başlayın.
> * Her şeyin beklendiği gibi çalıştığından emin olmak için bir test geçişi çalıştırın.
> * Tam VM geçişini çalıştırın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.


## <a name="prerequisites"></a>Önkoşullar


Bu öğreticiye başlamadan önce karşılamanız gereken ön koşullar şunlardır:

1. Hyper-V geçiş mimarisini [gözden geçirin](hyper-v-migration-architecture.md) .
2. [İnceleme](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) Geçiş için Hyper-V konak gereksinimleri ve Hyper-V konaklarının ve kümelerinin VM geçişi için erişmesi gereken Azure URL 'Leri.
3. Azure 'a geçirmek istediğiniz Hyper-V VM 'lerinin gereksinimlerini [gözden geçirin](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) .
4. [Hyper-V VM](tutorial-assess-hyper-v.md) 'lerini Azure 'a geçirmeden önce değerlendirmenizi öneririz, ancak şunları yapmanız gerekmez.
5. Zaten oluşturulmuş projeye git veya [Yeni bir proje oluştur](./create-manage-projects.md)
6. Azure hesabınız için izinleri doğrulama-Azure hesabınız, bir VM oluşturmak ve Azure yönetilen diskine yazmak için izinlere ihtiyaç duyuyor.

## <a name="download-and-install-the-provider"></a>Sağlayıcıyı indirme ve yükleme

Hyper-V VM 'lerini geçirmek için Azure geçişi: sunucu geçişi, Hyper-V konaklarına veya küme düğümlerine yazılım sağlayıcıları (Microsoft Azure Site Recovery sağlayıcı ve Microsoft Azure kurtarma hizmeti Aracısı) yüklüyor. Hyper-V geçişi için [Azure geçişi](migrate-appliance.md) gerecinin kullanılmadığını unutmayın.

1. Azure geçişi proje > **sunucularında** **Azure geçişi: sunucu geçişi**' nde **bul**' a tıklayın.
2. Makinelerde **bulunan makineler**  >  **sanallaştırılmış mı?**, **Hyper-V ile Evet '** i seçin.
3. **Hedef bölge**' de, makineleri geçirmek istediğiniz Azure bölgesini seçin.
6. **Geçiş için hedef bölgenin bölge adı olduğunu onaylayın**' i seçin.
7. **Kaynak oluştur**' a tıklayın. Bu, arka planda bir Azure Site Recovery Kasası oluşturur.
    - Azure geçişi sunucu geçişi ile geçiş zaten ayarladıysanız, kaynaklar daha önce ayarlandığı için bu seçenek görünmez.
    - Bu düğmeye tıkladıktan sonra bu proje için hedef bölgeyi değiştiremezsiniz.
    - Sonraki tüm geçişler bu bölgedir.
    
8. **Hyper-v ana bilgisayar sunucularını hazırlama** bölümünde, Hyper-v çoğaltma sağlayıcısını ve kayıt anahtarı dosyasını indirin.
    - Kayıt anahtarı, Hyper-V konağını Azure geçişi sunucu geçişine kaydetmek için gereklidir.
    - Anahtar, oluşturulduktan sonra beş gün boyunca geçerlidir.

    ![Sağlayıcıyı ve anahtarı indir](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Sağlayıcı kurulum dosyasını ve kayıt anahtarı dosyasını, çoğaltmak istediğiniz VM 'Leri çalıştıran her Hyper-V konağına (veya küme düğümüne) kopyalayın.
5. Sonraki yordamda açıklandığı gibi, her konakta sağlayıcı kurulum dosyasını çalıştırın.
6. Sağlayıcıyı konaklara yükledikten sonra, **bulma makineler** bölümünde **kaydı Sonlandır**' ı tıklatın.

    ![Kaydı Sonlandır](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Kayıt tamamlandıktan sonra, bulunan VM 'Lerin Azure geçişi sunucu geçişi 'nde görünene kadar 15 dakika sürebilir. VM 'Ler keşfedildiğinde, **bulunan sunucular** sayılır.

![Bulunan sunucular](./media/tutorial-migrate-hyper-v/discovered-servers.png)


## <a name="replicate-hyper-v-vms"></a>Hyper-V VM 'lerini çoğaltma

Bulma işlemi tamamlandıktan sonra, Hyper-V VM 'lerinin Azure 'a çoğaltılmasını başlatabilirsiniz.

> [!NOTE]
> En fazla 10 makineyi birbirine çoğaltabilirsiniz. Daha fazla çoğaltma yapmanız gerekiyorsa bunları aynı anda 10 ' da çoğaltın.

1. Azure geçişi proje > **sunucularında** **Azure geçişi: sunucu geçişi**' nde **Çoğalt**' a tıklayın.
2. > **kaynak ayarları** **Çoğalt**,  >  **makineleriniz sanallaştırılmış mı?**, **Hyper-V ile Evet '** i seçin. Ileri ' ye tıklayın **: sanal makineler**.
3. **Sanal makineler** bölümünde çoğaltmak istediğiniz makineleri seçin.
    - VM’ler için bir değerlendirme çalıştırırsanız değerlendirme sonuçlarından VM boyutlandırma ve disk türü (premium/standart) önerilerini uygulayabilirsiniz. Bunu yapmak için **Azure Geçişi değerlendirmesinden geçiş ayarları içe aktarılsın mı?** bölümünde **Evet**’i seçin.
    - Bir değerlendirme çalıştırmadıysanız veya değerlendirme ayarlarını kullanmak istemiyorsanız **Hayır**’ı seçin.
    - Değerlendirmeyi kullanmayı seçtiyseniz VM grubunu ve değerlendirme adını belirleyin.

        ![Değerlendirme seçme](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. VM’leri **Sanal makineler** bölümünde gerektiği şekilde arayın ve geçirmek istediğiniz her bir VM’yi seçin. Ardından Ileri ' ye tıklayın **: hedef ayarlar**.

    ![VM 'Leri seçin](./media/tutorial-migrate-hyper-v/select-vms.png)

5. **Hedef ayarlar**' da geçireceğiniz hedef bölgeyi, aboneliğinizi ve Azure VM 'lerinin geçişten sonra bulunacağı kaynak grubunu seçin.
7. **Çoğaltma depolama hesabı**' nda, çoğaltılan verilerin Azure 'Da depolanacağı Azure Storage hesabını seçin.
8. **Sanal ağ**' ı seçin ve Azure VM 'lerinin geçişten sonra katılacağını Azure VNET/subnet ' i seçin.
9. **Kullanılabilirlik seçenekleri**' nde şunları seçin:
    -  Bölge içindeki belirli bir kullanılabilirlik bölgesine geçirilen makineyi sabitlemek için kullanılabilirlik alanı. Kullanılabilirlik Alanları arasında çok düğümlü bir uygulama katmanı oluşturan sunucuları dağıtmak için bu seçeneği kullanın. Bu seçeneği belirlerseniz, Işlem sekmesinde seçilen makinenin her biri için kullanılacak kullanılabilirlik alanını belirtmeniz gerekir. Bu seçenek yalnızca geçiş için seçilen hedef bölge Kullanılabilirlik Alanları destekliyorsa kullanılabilir
    -  Geçirilen makinenin bir kullanılabilirlik kümesine yerleştirileceği kullanılabilirlik kümesi. Bu seçeneği kullanabilmek için seçilen hedef kaynak grubunun bir veya daha fazla kullanılabilirlik kümesi olmalıdır.
    - Geçirilen makineler için bu kullanılabilirlik yapılandırmalarının herhangi birine ihtiyacınız yoksa, altyapı artıklığı gerekli değildir.
10. **Azure Hibrit Avantajı**’nda:

    - Azure Hibrit Avantajı’nı uygulamak istemiyorsanız **Hayır**’ı seçin. Ardından **İleri**' ye tıklayın.
    - Etkin Yazılım Güvencesi veya Windows Server abonelikleri kapsamında olan Windows Server makineleriniz varsa ve avantajı geçirdiğiniz makinelere uygulamak istiyorsanız **Evet**’i seçin. Ardından **İleri**'ye tıklayın.

    ![Hedef ayarları](./media/tutorial-migrate-hyper-v/target-settings.png)

11. **İşlem** bölümünde VM adı, boyutu, işletim sistemi disk türü ve kullanılabilirlik yapılandırmasını (önceki adımda seçildiyse) gözden geçirin. VM’ler [Azure gereksinimleriyle](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)uyumlu olmalıdır.

    - **VM boyutu**: değerlendirme önerilerini KULLANıYORSANıZ, VM boyutu açılan listesi önerilen boyutu içerir. Aksi takdirde Azure Geçişi, Azure aboneliğindeki en yakın eşleşmeye göre bir boyut seçer. Alternatif olarak **Azure VM boyutu**’nda el ile bir boyut seçin. 
    - **Işletim sistemi diski**: VM için işletim sistemi (önyükleme) diskini belirtin. İşletim Sistemi diski, işletim sistemi önyükleyiciye ve yükleyiciye sahip disktir. 
    - **Kullanılabilirlik kümesi**: sanal makinenin geçişten sonra bir Azure kullanılabilirlik kümesinde olması gerekiyorsa, kümeyi belirtin. Küme, geçiş için belirttiğiniz hedef kaynak grubunda olmalıdır.

    ![VM işlem ayarları](./media/tutorial-migrate-hyper-v/compute-settings.png)

12. **Diskler**' de Azure 'a ÇOĞALTıLMASı gereken VM disklerini belirtin. Ardından **İleri**'ye tıklayın.
    - Diskleri çoğaltmadan çıkarabilirsiniz.
    - Diskleri çıkarırsanız bu diskler geçişten sonra Azure VM’de bulunmaz. 

    ![Ekran görüntüsü Çoğalt iletişim kutusunun diskler sekmesini gösterir.](./media/tutorial-migrate-hyper-v/disks.png)

13. **Çoğaltmayı gözden geçir ve başlat** bölümünde ayarları gözden geçirin ve sunuculara yönelik ilk çoğaltmayı başlatmak için **Çoğalt** üzerine tıklayın.

> [!NOTE]
> Çoğaltma ayarlarını, çoğaltma başlamadan önce istediğiniz zaman güncelleştirebilirsiniz, bu da çoğaltmayı **yönetme** bölümünde  >  . Çoğaltma başladıktan sonra ayarlar değiştirilemez.

## <a name="provision-for-the-first-time"></a>İlk kez sağlama

Azure geçişi projesinde çoğaltmakta olduğunuz ilk VM ise Azure geçişi: sunucu geçişi, bu kaynakları projeyle aynı kaynak grubunda otomatik olarak sağlar.
- **Önbellek depolama hesabı**: Hyper-V konaklarında yüklü olan Azure Site Recovery sağlayıcı yazılımı, aboneliğinizdeki depolama hesabına (önbellek depolama hesabı veya günlük depolama hesabı olarak bilinir) çoğaltma Için yapılandırılmış VM 'ler için çoğaltma verilerini yükler. Azure geçişi hizmeti daha sonra karşıya yüklenen çoğaltma verilerini depolama hesabından VM 'ye karşılık gelen çoğaltma ile yönetilen disklere kopyalar. Bir VM için çoğaltma yapılandırılırken önbellek depolama hesabının belirtilmesi gerekir ve çoğaltma, projede ilk kez yapılandırıldığında Azure geçişi projesi için otomatik olarak bir tane oluşturur.

## <a name="track-and-monitor"></a>İzleme ve izleme


- **Çoğalt** ' a tıkladığınızda çoğaltma Başlat işi başlar. 
- Çoğaltma Başlat işi başarıyla tamamlandığında, makineler ilk çoğaltmasını Azure 'a başlatır.
- İlk çoğaltma tamamlandıktan sonra Delta çoğaltma başlar. Şirket içi disklerde artımlı değişiklikler düzenli aralıklarla Azure 'a çoğaltılır.

Portal bildirimlerinde iş durumunu izleyebilirsiniz.

Çoğaltma durumunu, **Azure geçişi: sunucu geçişi**' nde **sunucuları** çoğaltma ' ya tıklayarak izleyebilirsiniz.
![Çoğaltmayı izleme](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Geçiş testi çalıştırma


Delta çoğaltma başladığında, Azure 'a tam geçiş çalıştırmadan önce VM 'Ler için bir test geçişi çalıştırabilirsiniz. Bunu geçirmeden önce her makine için en az bir kez yapmanızı öneririz.

- Bir test geçişi çalıştırmak, geçiş işleminin beklendiği gibi çalıştığını denetler ve bu işlem, çalışır durumda olan şirket içi makineleri etkilemeden, çoğaltmaya devam eder. 
- Test geçişi, çoğaltılan verileri kullanarak bir Azure VM oluşturarak geçişe benzetir (genellikle Azure aboneliğinizdeki üretim dışı bir Azure VNet 'e geçiş yapar).
- Geçişi doğrulamak, uygulama testi gerçekleştirmek ve tam geçişten önce herhangi bir sorunu gidermek için çoğaltılan test Azure VM 'yi kullanabilirsiniz.

Test geçişini aşağıdaki şekilde yapın:


1. **Geçiş hedefleri**  >  **sunucuları**  >  **Azure geçişi: sunucu geçişi**' nde **geçirilen sunucuları test et**' e tıklayın.

     ![Geçirilen sunucuları test etme](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Test edilecek VM’yi sağ tıklayıp **Test geçişi** üzerine tıklayın.

    ![Test geçişi](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. **Test geçişi**' nde geçişten sonra Azure VM 'Nin bulunduğu Azure sanal ağını seçin. Üretim dışı bir sanal ağ kullanmanızı öneririz.
4. **Test Geçişi** işlemi başlar. Portal bildirimlerinde işi izleyin.
5. Geçiş bittikten sonra geçirilen Azure VM’yi, Azure portalında **Sanal Makineler** bölümünde görüntüleyin. Makine adında **Testi** şeklinde bir son ek vardır.
6. Test tamamlandıktan sonra **Makineleri çoğaltma** bölümünde Azure VM’yi sağ tıklayıp **Test geçişini temizle** üzerine tıklayın.

    ![Geçişi temizleme](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>VM geçirme

Test geçişinin beklendiği gibi çalışıp çalışmadığını doğruladıktan sonra şirket içi makineleri geçirebilirsiniz.

1. Azure geçişi proje > **sunucuları**  >  **Azure geçişi: sunucu geçişi**' nde, **sunucuları çoğaltma**' ya tıklayın.

    ![Sunucuları çoğaltma](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. **Makineleri çoğaltma** bölümünde VM > **Geçir** üzerine sağ tıklayın.
3.   >  **Sanal makineleri Kapat ' a geçiş yapın ve veri kaybı olmadan planlı bir geçiş gerçekleştirin**, **Evet**  >  **Tamam**' ı seçin.
    - Azure Geçişi, varsayılan olarak şirket içi VM’yi kapatır ve son çoğaltmadan bu yana gerçekleşen tüm VM değişikliklerini eşitlemek için bir isteğe bağlı çoğaltma çalıştırır. Bu, veri kaybı olmamasını sağlar.
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

- Daha fazla esneklik için:
    - Azure Backup hizmetini kullanarak Azure sanal makinelerini yedekleyip verileri güvende tutun. [Daha fazla bilgi edinin](../backup/quick-backup-vm-portal.md).
    - Site Recovery ile Azure sanal makinelerini ikincil bölgeye çoğaltarak iş yüklerinin çalışmaya devam etmesini ve sürekli kullanılabilir olmasını sağlayın. [Daha fazla bilgi edinin](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Daha fazla güvenlik için:
    - Azure Güvenlik Merkezi ile gelen trafik erişimini kilitleme ve sınırlayın [-tam zamanında yönetim](../security-center/security-center-just-in-time.md).
    - [Ağ Güvenlik Grupları](../virtual-network/network-security-groups-overview.md) ile ağ trafiğini yönetim uç noktaları ile kısıtlayın.
    - [Azure Disk Şifrelemesi](../security/fundamentals/azure-disk-encryption-vms-vmss.md)’ni dağıtarak disklerin güvenliğinin sağlanmasına yardımcı olun ve verileri hırsızlık ve yetkisiz erişime karşı koruyun.
    - [IaaS kaynaklarının güvenliğini sağlama](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) hakkında daha fazla bilgi edinin ve [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)’ni ziyaret edin.
- İzleme ve yönetim için:
-  [Azure Maliyet Yönetimi](../cost-management-billing/cloudyn/overview.md)’ni dağıtarak kaynak kullanımını ve harcamayı izleyin.


## <a name="next-steps"></a>Sonraki adımlar

Azure bulut benimseme çerçevesindeki [bulut geçiş yolculuğunu](/azure/architecture/cloud-adoption/getting-started/migrate) araştırın.