---
title: VMware VM 'lerini geçirme aracısız Azure geçiş sunucusu geçişi
description: Azure geçişi ile VMware VM 'lerinin aracısız geçişini nasıl çalıştıracağınızı öğrenin.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: mvc
ms.openlocfilehash: a1d745c95b89efefabbd0b83061f9dcd9fe13911
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567127"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>VMware VM 'lerini Azure 'a geçirme (aracısız)

Bu makalede, [Azure geçişi: sunucu geçiş](migrate-services-overview.md#azure-migrate-server-migration-tool) Aracı kullanılarak aracısız geçiş ile şirket Içi VMware VM 'lerini Azure 'a nasıl geçireceğiniz gösterilmektedir. Ayrıca, aracı tabanlı geçiş kullanarak VMware VM 'Leri geçirebilirsiniz. Yöntemleri [karşılaştırın](server-migrate-overview.md#compare-migration-methods) .

Bu öğretici, VMware VM 'lerinin Azure 'a nasıl değerlendirileceğini ve geçirileceğini gösteren bir serideki üçüncü bir seridir. 

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

1. Geçiş için Azure ve VMware 'yi hazırlamak üzere [ilk öğreticiyi doldurun](./tutorial-discover-vmware.md) .
2. [VMware VM](./tutorial-assess-vmware-azure-vm.md) 'lerini Azure 'a geçirmeden önce değerlendirmek için ikinci öğreticiyi tamamlamanızı öneririz, ancak şunları yapmanız gerekmez. 
3. Zaten oluşturulmuş projeye git veya [Yeni bir proje oluştur](./create-manage-projects.md)
4. Azure hesabınız için izinleri doğrulama-Azure hesabınız, bir VM oluşturmak ve Azure yönetilen diskine yazmak için izinlere ihtiyaç duyuyor.

## <a name="set-up-the-azure-migrate-appliance"></a>Azure geçişi gereç 'yi ayarlama

Azure geçişi sunucu geçişi, VMware VM 'lerinin bulunması, değerlendirmesi ve aracısız geçirilmesi için kullanılan basit bir VMware VM gereci çalıştırır. [Değerlendirme öğreticisini](./tutorial-assess-vmware-azure-vm.md)izlerseniz, zaten gereci ayarlamış olursunuz. Yapmadıysanız, şu yöntemlerden birini kullanarak şimdi ayarlayın:

- **Ova şablonu**: indirilen bir ova şablonu kullanarak BIR VMware VM üzerinde [ayarlanır](how-to-set-up-appliance-vmware.md) .
- **Betik**: BIR VMware VM 'de veya fiziksel makinede bir PowerShell yükleyici betiği kullanılarak [ayarlanır](deploy-appliance-script.md) . Bu yöntem, bir OVA şablonu kullanarak bir VM ayarlayamıyorum veya Azure Kamu kullanıyorsanız kullanılmalıdır.

Gereci oluşturduktan sonra Azure geçişi 'ne bağlanıp bağlanamadıktan sonra sunucu değerlendirmesi yapın, ilk kez yapılandırın ve Azure geçişi projesine kaydedin.

## <a name="replicate-vms"></a>Sanal makineleri çoğaltma

Gereci ayarladıktan ve bulmayı tamamladıktan sonra, VMware VM 'lerinin Azure 'a çoğaltılmasını başlatabilirsiniz. 

- Aynı anda 500 çoğaltma gerçekleştirebilirsiniz.
- Portalda, geçiş için aynı anda en fazla 10 VM seçebilirsiniz. Daha fazla makine geçirmek için bunları 10 toplu iş gruplarına ekleyin.

Aşağıda belirtilen şekilde çoğaltmayı etkinleştirin:

1. Azure geçişi proje > **sunucularında** **Azure geçişi: sunucu geçişi**' nde **Çoğalt**' a tıklayın.

    ![Sanal makineleri çoğaltma](./media/tutorial-migrate-vmware/select-replicate.png)

2. **Çoğalt**, > **Kaynak ayarları** > **Makineleriniz sanallaştırıldı mı?** bölümünde **Evet, VMware vSphere** ile seçeneğini belirleyin.
3. **Şirket içi aletler** bölümünde, ayarladığınız Azure Geçişi aletinin adını seçip **Tamam** seçeneğini belirleyin. 

    ![Kaynak ayarları](./media/tutorial-migrate-vmware/source-settings.png)

4. **Sanal makineler** bölümünde çoğaltmak istediğiniz makineleri seçin. VM boyutlandırma ve disk türünü bir değerlendirmede uygulamak için bir **Azure geçişi değerlendirmesinde geçiş ayarlarını Içeri aktarma** bölümünde, **Evet**' i seçin ve VM grubunu ve değerlendirme adını seçin. Değerlendirme ayarlarını kullanmıyorsanız **Hayır**' ı seçin.
   
    ![Değerlendirme seçme](./media/tutorial-migrate-vmware/select-assessment.png)

5. **Sanal makineler**' de, geçirmek Istediğiniz VM 'leri seçin. Ardından Ileri ' ye tıklayın **: hedef ayarlar**.

    ![VM 'Leri seçin](./media/tutorial-migrate-vmware/select-vms.png)

6. **Hedef ayarlar**' da, abonelik ve hedef bölge ' yi seçin. Azure VM 'lerinin geçişten sonra bulunduğu kaynak grubunu belirtin.
7. **Sanal ağ** bölümünde, Azure VM 'lerinin geçişten sonra birleşme Azure VNET/alt ağını seçin.
8. **Kullanılabilirlik seçenekleri**' nde şunları seçin:
    -  Bölge içindeki belirli bir kullanılabilirlik bölgesine geçirilen makineyi sabitlemek için kullanılabilirlik alanı. Kullanılabilirlik Alanları arasında çok düğümlü bir uygulama katmanı oluşturan sunucuları dağıtmak için bu seçeneği kullanın. Bu seçeneği belirlerseniz, Işlem sekmesinde seçilen makinenin her biri için kullanılacak kullanılabilirlik alanını belirtmeniz gerekir. Bu seçenek yalnızca geçiş için seçilen hedef bölge Kullanılabilirlik Alanları destekliyorsa kullanılabilir
    -  Geçirilen makinenin bir kullanılabilirlik kümesine yerleştirileceği kullanılabilirlik kümesi. Bu seçeneği kullanabilmek için seçilen hedef kaynak grubunun bir veya daha fazla kullanılabilirlik kümesi olmalıdır.
    - Geçirilen makineler için bu kullanılabilirlik yapılandırmalarının herhangi birine ihtiyacınız yoksa, altyapı artıklığı gerekli değildir.
9. **Disk şifreleme türü**' nde şunları seçin:
    - Platform tarafından yönetilen anahtar ile bekleyen şifreleme
    - Müşteri tarafından yönetilen anahtarla bekleyen şifreleme
    - Platform tarafından yönetilen ve müşteri tarafından yönetilen anahtarlarla Çift şifreleme

   > [!NOTE]
   > VM 'Leri CMK ile çoğaltmak için hedef kaynak grubu altında [bir disk şifreleme kümesi oluşturmanız](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) gerekir. Bir disk şifreleme kümesi nesnesi yönetilen diskleri, SSE için kullanılacak CMK 'yi içeren bir Key Vault eşler.
  
10. **Azure Hibrit Avantajı**’nda:

    - Azure Hibrit Avantajı’nı uygulamak istemiyorsanız **Hayır**’ı seçin. Ardından **İleri**'ye tıklayın.
    - Etkin Yazılım Güvencesi veya Windows Server abonelikleri kapsamında olan Windows Server makineleriniz varsa ve avantajı geçirdiğiniz makinelere uygulamak istiyorsanız **Evet**’i seçin. Ardından **İleri**'ye tıklayın.

    ![Hedef ayarları](./media/tutorial-migrate-vmware/target-settings.png)

11. **İşlem** bölümünde, işlem ' de VM adı, boyutu, işletim sistemi disk türü ve kullanılabilirlik yapılandırmasını (önceki adımda seçildiyse) gözden geçirin. VM’ler [Azure gereksinimleriyle](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)uyumlu olmalıdır.

    - **VM boyutu**: değerlendirme önerilerini KULLANıYORSANıZ, VM boyutu açılan listesi önerilen boyutu gösterir. Aksi takdirde Azure Geçişi, Azure aboneliğindeki en yakın eşleşmeye göre bir boyut seçer. Alternatif olarak **Azure VM boyutu**’nda el ile bir boyut seçin. 
    - **Işletim sistemi diski**: VM için işletim sistemi (önyükleme) diskini belirtin. İşletim Sistemi diski, işletim sistemi önyükleyiciye ve yükleyiciye sahip disktir. 
    - **Kullanılabilirlik alanı**: kullanılacak kullanılabilirlik bölgesini belirtin.
    - **Kullanılabilirlik kümesi**: kullanılacak kullanılabilirlik kümesini belirtin.

    > [!NOTE]
    > Bir sanal makine kümesi için farklı bir kullanılabilirlik seçeneği seçmek istiyorsanız 1. adıma gidin ve tek bir sanal makine kümesi için çoğaltma başlattıktan sonra farklı kullanılabilirlik seçeneklerini belirleyerek adımları yineleyin.




12. **Diskler** bölümünde, VM disklerinin Azure’a çoğaltılıp çoğaltılmayacağını belirtin ve Azure’da disk türünü (Standart SSD/HDD veya premium yönetilen diskler) seçin. Ardından **İleri**'ye tıklayın.
   
    ![Ekran görüntüsü Çoğalt iletişim kutusunun diskler sekmesini gösterir.](./media/tutorial-migrate-vmware/disks.png)

13. **Çoğaltmayı gözden geçir ve başlat** bölümünde ayarları gözden geçirin ve sunuculara yönelik ilk çoğaltmayı başlatmak için **Çoğalt** üzerine tıklayın.

> [!NOTE]
> Çoğaltma ayarlarını yineleme başlamadan önce dilediğiniz zaman güncelleştirebilirsiniz (  >  **çoğaltılan makineleri** yönetin). Çoğaltma başladıktan sonra ayarları değiştiremezsiniz.

### <a name="provisioning-for-the-first-time"></a>İlk kez sağlama

Bu, projede çoğaltmakta olduğunuz ilk VM ise, sunucu geçişi bu kaynakları projeyle aynı kaynak grubunda otomatik olarak sağlar.

- **Service Bus**: sunucu geçişi, yüklemeye çoğaltma düzenleme iletileri göndermek için Service Bus 'ı kullanır.
- **Ağ geçidi depolama hesabı**: sunucu geçişi, çoğaltılan VM 'ler hakkında durum bilgilerini depolamak için ağ geçidi depolama hesabını kullanır.
- **Günlük depolama hesabı**: Azure geçişi gereci, VM 'ler için çoğaltma günlüklerini bir günlük depolama hesabına yükler. Azure geçişi, çoğaltma bilgilerini çoğaltma tarafından yönetilen disklere uygular.
- **Anahtar Kasası**: Azure geçişi gereci, hizmet veri yolu için bağlantı dizelerini yönetmek üzere anahtar kasasını ve Çoğaltmada kullanılan depolama hesapları için erişim anahtarlarını kullanır.

## <a name="track-and-monitor"></a>İzleme ve izleme

1. Portal bildirimlerinde iş durumunu izleyin.
2. **Azure geçişi: sunucu geçişi**'nde **sunucuları** çoğaltma ' ya tıklayarak çoğaltma durumunu izleyin.

     ![Çoğaltmayı izleme](./media/tutorial-migrate-vmware/replicating-servers.png)

Çoğaltma aşağıdaki gibi gerçekleşir:
- Çoğaltma Başlat işi başarıyla tamamlandığında, makineler ilk çoğaltmasını Azure 'a başlatır.
- İlk çoğaltma sırasında VM anlık görüntüsü oluşturulur. Anlık görüntüdeki disk verileri, Azure 'da çoğaltma ile yönetilen disklere çoğaltılır.
- İlk çoğaltma tamamlandıktan sonra Delta çoğaltma başlar. Şirket içi disklerde artımlı değişiklikler düzenli aralıklarla Azure 'daki çoğaltma disklerine çoğaltılır.

## <a name="run-a-test-migration"></a>Geçiş testi çalıştırma


Delta çoğaltma başladığında, Azure 'a tam geçiş çalıştırmadan önce VM 'Ler için bir test geçişi çalıştırabilirsiniz. Bunu geçirmeden önce her makine için en az bir kez yapmanızı öneririz.

- Bir test geçişi çalıştırmak, geçiş işleminin beklendiği gibi çalıştığını denetler ve bu işlem, çalışır durumda olan şirket içi makineleri etkilemeden, çoğaltmaya devam eder. 
- Test geçişi, çoğaltılan verileri kullanarak bir Azure VM oluşturarak geçişe benzetir (genellikle Azure aboneliğinizdeki bir üretim dışı VNet 'e geçiş yapar).
- Geçişi doğrulamak, uygulama testi gerçekleştirmek ve tam geçişten önce herhangi bir sorunu gidermek için çoğaltılan test Azure VM 'yi kullanabilirsiniz.

Test geçişini aşağıdaki şekilde yapın:


1. **Geçiş hedefleri**  >  **sunucuları**  >  **Azure geçişi: sunucu geçişi**' nde **geçirilen sunucuları test et**' e tıklayın.

     ![Geçirilen sunucuları test etme](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Test edilecek VM’yi sağ tıklayıp **Test geçişi** üzerine tıklayın.

    ![Test geçişi](./media/tutorial-migrate-vmware/test-migrate.png)

3. **Test Geçişi** bölümünde, Azure VM’nin geçişten sonra bulunacağı Azure VNet’i seçin. Üretim dışı bir VNet kullanmanızı öneririz.
4. **Test Geçişi** işlemi başlar. Portal bildirimlerinde işi izleyin.
5. Geçiş bittikten sonra geçirilen Azure VM’yi, Azure portalında **Sanal Makineler** bölümünde görüntüleyin. Makine adında **Testi** şeklinde bir son ek vardır.
6. Test tamamlandıktan sonra **Makineleri çoğaltma** bölümünde Azure VM’yi sağ tıklayıp **Test geçişini temizle** üzerine tıklayın.

    ![Geçişi temizleme](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>VM geçirme

Test geçişinin beklendiği gibi çalışıp çalışmadığını doğruladıktan sonra şirket içi makineleri geçirebilirsiniz.

1. Azure geçişi proje > **sunucuları**  >  **Azure geçişi: sunucu geçişi**' nde, **sunucuları çoğaltma**' ya tıklayın.

    ![Sunucuları çoğaltma](./media/tutorial-migrate-vmware/replicate-servers.png)

2. **Makineleri çoğaltma** bölümünde VM > **Geçir** üzerine sağ tıklayın.
3.   >  **Sanal makineleri Kapat ' a geçiş yapın ve veri kaybı olmadan planlı bir geçiş gerçekleştirin**, **Evet**  >  **Tamam**' ı seçin.
    - Azure Geçişi, varsayılan olarak şirket içi VM’yi kapatır ve son çoğaltmadan bu yana gerçekleşen tüm VM değişikliklerini eşitlemek için bir isteğe bağlı çoğaltma çalıştırır. Bu, veri kaybı olmamasını sağlar.
    - VM’yi kapatmak istemiyorsanız, **Hayır** seçeneğini belirleyin
4. VM için bir geçiş işlemi başlar. Azure bildirimlerinde işlemi izleyin.
5. İşlem bittikten sonra **Sanal Makineler** sayfasında VM’yi görüntüleyebilir ve yönetebilirsiniz.

## <a name="complete-the-migration"></a>Geçişi tamamlamayı

1. Geçiş yapıldıktan sonra, **çoğaltmayı durdurmak**> VM 'ye sağ tıklayın. Bu, şirket içi makine için çoğaltmayı durduruyor ve VM için çoğaltma durumu bilgilerini temizler.
2. Geçiş sırasında Windows VM 'Leri ve Linux için VM aracısını otomatik olarak yükledik. Linux VM Aracısı yüklemesinin düzgün şekilde çalışmasını sağlamak için makinede Linux OS varsa, geçirilen makinelerde Azure VM Linux Aracısı [gereksinimlerini](../virtual-machines/extensions/agent-linux.md#requirements) gözden geçirin. 
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
- Daha yüksek performans için:
    - Varsayılan olarak, veri diskleri, ana bilgisayar önbelleği "none" olarak ayarlanmış şekilde oluşturulur. Veri diski önbelleğe alma işlemini iş yükü gereksinimlerinize göre gözden geçirin ve ayarlayın. [Daha fazla bilgi edinin](../virtual-machines/premium-storage-performance.md#disk-caching).  
- Daha fazla güvenlik için:
    - Azure Güvenlik Merkezi ile gelen trafik erişimini kilitleme ve sınırlayın [-tam zamanında yönetim](../security-center/security-center-just-in-time.md).
    - [Ağ Güvenlik Grupları](../virtual-network/network-security-groups-overview.md) ile ağ trafiğini yönetim uç noktaları ile kısıtlayın.
    - [Azure Disk Şifrelemesi](../security/fundamentals/azure-disk-encryption-vms-vmss.md)’ni dağıtarak disklerin güvenliğinin sağlanmasına yardımcı olun ve verileri hırsızlık ve yetkisiz erişime karşı koruyun.
    - [IaaS kaynaklarının güvenliğini sağlama](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) hakkında daha fazla bilgi edinin ve [Azure Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/)’ni ziyaret edin.
- İzleme ve yönetim için:
-  [Azure Maliyet Yönetimi](../cost-management-billing/cloudyn/overview.md)’ni dağıtarak kaynak kullanımını ve harcamayı izleyin.


## <a name="next-steps"></a>Sonraki adımlar

Azure bulut benimseme çerçevesindeki [bulut geçiş yolculuğunu](/azure/architecture/cloud-adoption/getting-started/migrate) araştırın.