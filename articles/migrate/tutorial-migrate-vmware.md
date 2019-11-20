---
title: Aracısız Azure geçişi sunucu geçişine sahip şirket içi VMware VM 'lerini Azure 'a geçirme | Microsoft Docs
description: Azure geçişi kullanılarak şirket içi VMware VM 'lerinin Azure 'a nasıl gerçekleştirileceğini ve aracısız geçirilmesini açıklar.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: ec7d10a1d9fec43c149c068fb408211db76d19eb
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309371"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>VMware VM 'lerini Azure 'a geçirme (aracısız)

Bu makalede, Azure geçişi sunucu geçiş aracı ile aracısız geçiş kullanarak şirket içi VMware VM 'lerini Azure 'a nasıl geçireceğiniz gösterilmektedir.

[Azure geçişi](migrate-services-overview.md) , şirket içi uygulamalarınızı ve iş yüklerinizi ve AWS/GCP sanal makine örneklerini Azure 'a bulmayı, değerlendirmeyi ve geçirmeyi izlemek için bir merkezi Merkez sağlar. Hub, değerlendirme ve geçiş için Azure geçiş araçları ve ayrıca üçüncü taraf bağımsız yazılım satıcısı (ISV) teklifleri sağlar.

Bu öğretici, Azure geçiş sunucusu değerlendirmesini ve geçişini kullanarak VMware VM 'lerini Azure 'a nasıl değerlendirileceğini ve geçireceğinizi gösteren bir serinin üçüncü bir dizisidir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * VM 'Leri geçiş için hazırlayın.
> * Azure geçiş sunucusu geçiş aracı 'nı ekleyin.
> * Geçirmek istediğiniz VM 'Leri bulun.
> * VM 'Leri çoğaltmaya başlayın.
> * Her şeyin beklendiği gibi çalıştığından emin olmak için bir test geçişi çalıştırın.
> * Tam VM geçişini çalıştırın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.

## <a name="migration-methods"></a>Geçiş yöntemleri

Azure geçişi sunucu geçiş aracını kullanarak VMware VM 'lerini Azure 'a geçirebilirsiniz. Bu araç, VMware VM geçişi için birkaç seçenek sunar:

- Aracısız çoğaltma kullanılarak geçiş. VM 'Leri bunlara hiçbir şey yüklemeye gerek kalmadan geçirin.
- Çoğaltma için bir aracı ile geçiş. Çoğaltma için VM 'ye bir aracı yükler.

Aracısız veya aracı tabanlı geçiş kullanmak isteyip istemediğinize karar vermek için şu makaleleri gözden geçirin:

- Aracısız geçişin [nasıl çalıştığını öğrenin](server-migrate-overview.md) ve [geçiş yöntemlerini karşılaştırın](server-migrate-overview.md#compare-migration-methods).
- Aracı tabanlı yöntemi kullanmak istiyorsanız [Bu makaleyi okuyun](tutorial-migrate-vmware-agent.md) .

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce karşılamanız gereken ön koşullar şunlardır:

1. VMware geçiş mimarisini [anlayın](migrate-architecture.md) .
2. Geçiş için Azure ve VMware 'yi ayarlamak üzere bu serideki [ilk öğreticiyi doldurun](tutorial-prepare-vmware.md) . Özellikle, bu öğreticide şunları yapmanız gerekir:
    - [Azure](tutorial-prepare-vmware.md#prepare-azure) 'u geçiş için hazırlayın.
    - [Şirket içi ortamı](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) geçiş için hazırlayın.
    
3. VMware VM 'lerini Azure 'a geçirmeden önce Azure geçişi sunucu değerlendirmesi ile değerlendirmeyi denemeniz önerilir. Değerlendirmeyi ayarlamak için bu serideki [ikinci öğreticiyi doldurun](tutorial-assess-vmware.md) . VM 'Leri değerlendirmek istemiyorsanız bu öğreticiyi atlayabilirsiniz. Bir değerlendirme denemeniz önerilir, ancak geçiş yapmayı denemeden önce bir değerlendirme çalıştırmanız gerekmez.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Azure geçiş sunucusu geçiş aracını ekleme

VMware VM 'lerini değerlendirmek için ikinci öğreticiyi izmediyseniz, bir Azure geçişi projesi ayarlama ve Azure geçişi sunucusu geçiş aracını seçme [yönergelerini izlemeniz](how-to-add-tool-first-time.md) gerekir. 

İkinci öğreticiyi takip ediyorsanız ve zaten bir Azure geçişi projesi ayarlandıysa, Azure geçiş sunucusu geçiş aracını şu şekilde ekleyin:

1. Azure geçişi projesinde **Genel Bakış ' a**tıklayın. 
2. **Bul, değerlendir ve geçiş sunucuları**' nda, **sunucuları değerlendir ve geçir**' e tıklayın.

     ![Sunucuları değerlendirin ve geçirin](./media/tutorial-migrate-vmware/assess-migrate.png)

3. **Geçiş araçları**' nda, geçişe **hazır olduğunuzda bir geçiş aracı eklemek Için buraya tıklayın ' ı**seçin.

    ![Araç seçin](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Araçlar listesinde Azure geçişi ' ni **seçin: Sunucu geçişi** > **ekleme aracı**

    ![Sunucu geçiş aracı](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Azure geçişi gereç 'yi ayarlama

Azure geçişi sunucu geçişi, hafif bir VMware VM gereci çalıştırır. Gereç, VM bulma işlemini gerçekleştirir ve VM meta verilerini ve performans verilerini Azure geçişi sunucu geçişine iletir. Aynı gereç, Azure geçişi sunucu değerlendirmesi aracı tarafından da kullanılır.

VMware VM 'lerini değerlendirmek için ikinci öğreticiyi izlediyseniz, bu öğreticide zaten gereci ayarlamış olursunuz. Bu öğreticiyi izlemeden, gereci şimdi ayarlamanız gerekir. Bunu yapmak için şunları yapın: 

- Bir OVA şablon dosyasını indirip vCenter Server içe aktarın.
- Gereci oluşturun ve Azure geçişi sunucu değerlendirmesi 'ne bağlanıp bağlanamadığından emin olun. 
- Gereci ilk kez yapılandırın ve Azure geçişi projesi ile kaydedin.

Gereci ayarlamak için [Bu makaledeki](how-to-set-up-appliance-vmware.md) yönergeleri izleyin.


## <a name="prepare-vms-for-migration"></a>VM 'Leri geçiş için hazırlama

Azure geçişi, VM 'Lerin Azure 'a geçirilmesini sağlamak için bazı VM değişiklikleri gerektirir.

- Bazı işletim sistemleri için Azure geçişi bu değişiklikleri otomatik olarak yapar. [Daha fazla bilgi edinin](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)
- Bu işletim sistemlerinden birine sahip olmayan bir VM 'yi geçiriyorsanız, sanal makineyi hazırlamak için yönergeleri izleyin.
- Geçişe başlamadan önce bu değişiklikleri yapmak önemlidir. Değişikliği yapmadan önce VM 'yi geçirirseniz, VM Azure 'da önyüklenemeyebilir.
- VM için çoğaltma etkinleştirildikten sonra şirket içi VM 'lerde yaptığınız yapılandırma değişiklikleri Azure 'a çoğaltılır. Değişikliklerin çoğaltılmasını sağlamak için, geçiş yaptığınız kurtarma noktasının şirket içinde yapılandırma değişikliklerinin gerçekleştirildiği zamandan daha geç olduğundan emin olun.


### <a name="prepare-windows-server-vms"></a>Windows Server VM 'lerini hazırlama

**Eylem** | **Ayrıntılar** | **Yönergelerin**
--- | --- | ---
Azure VM 'deki Windows birimlerinin, şirket içi VM ile aynı sürücü harfi atamalarını kullandığından emin olun. | SAN ilkesini çevrimiçi olarak yapılandırın. | 1. VM 'de bir yönetici hesabıyla oturum açın ve bir komut penceresi açın.<br/> 2. DiskPart yardımcı programını çalıştırmak için **DiskPart** yazın.<br/> 3. **San ilkesi = OnlineAll** yazın<br/> 4. DiskPart 'Tan çıkmak için exit yazın ve komut istemi ' ni kapatın.
Azure VM için Azure seri erişim konsolunu etkinleştirme | Bu sorun gidermeye yardımcı olur. VM 'yi yeniden başlatmanız gerekmez. Azure VM, disk görüntüsü kullanılarak önyüklenir ve bu, yeni VM için bir yeniden başlatmaya eşdeğerdir. | ' İ etkinleştirmek için [Bu yönergeleri](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) izleyin.
Hyper-V Konuk tümleştirmesi 'ni yükler | Windows Server 2003 çalıştıran makineleri geçiriyorsanız, Hyper-V Konuk Tümleştirme Hizmetleri 'ni VM işletim sistemine yükleyebilirsiniz. | [Daha fazla bilgi edinin](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Uzak Masaüstü | SANAL makinede Uzak Masaüstü 'Nü etkinleştirin ve Windows Güvenlik Duvarı 'nın herhangi bir ağ profili üzerinde uzak masaüstü erişimini engellemediğinden emin olun. | [Daha fazla bilgi edinin](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Linux VM 'lerini hazırlama

**Eylem** | **Ayrıntılar** 
--- | --- | ---
Hyper-V Linux Tümleştirme Hizmetleri 'ni yükler | Linux dağıtımların çoğu yeni sürümü bu varsayılan olarak eklenmiştir.
Linux init görüntüsünü, gerekli Hyper-V sürücülerini içerecek şekilde yeniden oluşturun | Bu, VM 'nin Azure 'da önyüklenmesini sağlar ve yalnızca bazı dağıtımlarda gerekli olur.
Azure seri konsol günlüğünü etkinleştirme | Bu sorun gidermeye yardımcı olur. VM 'yi yeniden başlatmanız gerekmez. Azure VM, disk görüntüsü kullanılarak önyüklenir ve bu, yeni VM için bir yeniden başlatmaya eşdeğerdir.<br/> ' İ etkinleştirmek için [Bu yönergeleri](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) izleyin.
Cihaz eşleme dosyasını güncelleştir | Kalıcı cihaz tanımlayıcılarını kullanmak için cihaz adı olan cihaz eşleme dosyasını birim ilişkilendirmeleriyle Güncelleştir
Fstab girdilerini Güncelleştir | Kalıcı birim tanımlayıcılarını kullanmak için girişleri güncelleştirin.
Uıdev kuralını kaldır | Arabirim adlarını ayrılmış olan tüm udev kurallarını MAC adresine göre kaldırın vb.
Ağ arabirimlerini Güncelleştir | Ağ arabirimlerini DHCP tabanlı IP adresini alacak şekilde güncelleştirin.
SSH 'yi etkinleştirme | SSH 'nin etkinleştirildiğinden ve SSHD hizmetinin yeniden başlatma sırasında otomatik olarak başlayacak şekilde ayarlandığından emin olun.<br/> Gelen SSH bağlantı isteklerinin işletim sistemi güvenlik duvarı veya komut dosyası kuralları tarafından engellenmediğinden emin olun.

Azure 'da bir Linux sanal makinesi çalıştırmaya yönelik bu adımları açıklayan [Bu makaleyi izleyin](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) ve popüler Linux dağıtımlarından bazılarına yönelik yönergeleri dahil edin.  


## <a name="replicate-vms"></a>Sanal makineleri çoğaltma

Bulma işlemi tamamlandığında VMware VM’lerini Azure’a çoğaltabilirsiniz. 

> [!NOTE]
> En fazla 10 makineyi birbirine çoğaltabilirsiniz. Daha fazla çoğaltma yapmanız gerekiyorsa bunları aynı anda 10 ' da çoğaltın. Aracısız geçiş için 100 ' e kadar eşzamanlı çoğaltma gerçekleştirebilirsiniz.

1. Azure Geçişi projesi > **Sunucular**, **Azure Geçişi: Sunucu Geçişi** bölümünde **Çoğalt**’a tıklayın.

    ![Sanal makineleri çoğaltma](./media/tutorial-migrate-vmware/select-replicate.png)

2. **Çoğalt**, > **Kaynak ayarları** > **Makineleriniz sanallaştırıldı mı?** bölümünde **Evet, VMware vSphere** ile seçeneğini belirleyin.
3. **Şirket içi aletler** bölümünde, ayarladığınız Azure Geçişi aletinin adını seçip **Tamam** seçeneğini belirleyin. 

    ![Kaynak ayarları](./media/tutorial-migrate-vmware/source-settings.png)

    - Bu adım, öğreticiyi tamamladığınızda daha önce bir gereç ayarlamış olduğunuz varsayılır.
    - Bir gereç ayarlamadıysanız, [Bu makaledeki](how-to-set-up-appliance-vmware.md)yönergeleri izleyin.

4. **Sanal makineler** bölümünde çoğaltmak istediğiniz makineleri seçin.
    - VM’ler için bir değerlendirme çalıştırırsanız değerlendirme sonuçlarından VM boyutlandırma ve disk türü (premium/standart) önerilerini uygulayabilirsiniz. Bunu yapmak için **Azure Geçişi değerlendirmesinden geçiş ayarları içe aktarılsın mı?** bölümünde **Evet**’i seçin.
    - Bir değerlendirme çalıştırmadıysanız veya değerlendirme ayarlarını kullanmak istemiyorsanız **Hayır**’ı seçin.
    - Değerlendirmeyi kullanmayı seçtiyseniz VM grubunu ve değerlendirme adını belirleyin.

    ![Değerlendirme seçme](./media/tutorial-migrate-vmware/select-assessment.png)

5. VM’leri **Sanal makineler** bölümünde gerektiği şekilde arayın ve geçirmek istediğiniz her bir VM’yi seçin. Ardından **İleri: Hedef ayarları** seçeneğine tıklayın.

    ![VM 'Leri seçin](./media/tutorial-migrate-vmware/select-vms.png)

6. **Hedef ayarları**’nda aboneliği ve geçiş yapacağınız hedef bölgeyi seçin. Daha sonra Azure VM’lerinin geçişten sonra bulunacağı kaynak grubunu belirtin. **Sanal Ağ**’da Azure VM’lerinin geçişten sonra katılacağı Azure sanal ağını/alt ağını seçin.
7. **Azure Hibrit Avantajı**’nda:

    - Azure Hibrit Avantajı’nı uygulamak istemiyorsanız **Hayır**’ı seçin. Ardından **İleri**'ye tıklayın.
    - Etkin Yazılım Güvencesi veya Windows Server abonelikleri kapsamında olan Windows Server makineleriniz varsa ve avantajı geçirdiğiniz makinelere uygulamak istiyorsanız **Evet**’i seçin. Ardından **İleri**'ye tıklayın.

    ![Hedef ayarları](./media/tutorial-migrate-vmware/target-settings.png)

8. **İşlem** bölümünde VM adını, boyutunu, İşletim Sistemi disk türünü ve kullanılabilirlik kümesini gözden geçirin. VM’ler [Azure gereksinimleriyle](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)uyumlu olmalıdır.

    - **VM boyutu**: Değerlendirme önerilerini kullanıyorsanız VM boyutu açılan listesi, önerilen boyutu içerir. Aksi takdirde Azure Geçişi, Azure aboneliğindeki en yakın eşleşmeye göre bir boyut seçer. Alternatif olarak **Azure VM boyutu** ’nda el ile bir boyut seçin. 
    - **İşletim Sistemi diski**: VM için İşletim Sistemi (önyükleme) diskini belirtin. İşletim Sistemi diski, işletim sistemi önyükleyiciye ve yükleyiciye sahip disktir. 
    - **Kullanılabilirlik kümesi**: VM’nin geçişten sonra bir Azure kullanılabilirlik kümesinde bulunması gerekiyorsa kümeyi belirtin. Küme, geçiş için belirttiğiniz hedef kaynak grubunda olmalıdır.

    ![VM işlem ayarları](./media/tutorial-migrate-vmware/compute-settings.png)

9. **Diskler** bölümünde, VM disklerinin Azure’a çoğaltılıp çoğaltılmayacağını belirtin ve Azure’da disk türünü (Standart SSD/HDD veya premium yönetilen diskler) seçin. Ardından **İleri**'ye tıklayın.
    - Diskleri çoğaltmadan çıkarabilirsiniz.
    - Diskleri çıkarırsanız bu diskler geçişten sonra Azure VM’de bulunmaz. 

    ![Diskler](./media/tutorial-migrate-vmware/disks.png)

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
- İlk çoğaltma sırasında VM anlık görüntüsü oluşturulur. Anlık görüntüdeki disk verileri, Azure 'da çoğaltma ile yönetilen disklere çoğaltılır.
- İlk çoğaltma tamamlandıktan sonra Delta çoğaltma başlar. Şirket içi disklerde artımlı değişiklikler düzenli aralıklarla Azure 'daki çoğaltma disklerine çoğaltılır.

Portal bildirimlerinde iş durumunu izleyebilirsiniz.

Çoğaltma durumunu, **Azure geçişi 'nde** sunucuları **çoğaltma ' ya tıklayarak izleyebilirsiniz: Sunucu geçişi**.
![Çoğaltmayı izleme](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Geçiş testi çalıştırma


Delta çoğaltma başladığında, Azure 'a tam geçiş çalıştırmadan önce VM 'Ler için bir test geçişi çalıştırabilirsiniz. Bunu geçirmeden önce her makine için en az bir kez yapmanızı öneririz.

- Bir test geçişi çalıştırmak, geçiş işleminin beklendiği gibi çalıştığını denetler ve bu işlem, çalışır durumda olan şirket içi makineleri etkilemeden, çoğaltmaya devam eder. 
- Test geçişi, çoğaltılan verileri kullanarak bir Azure VM oluşturarak geçişe benzetir (genellikle Azure aboneliğinizdeki bir üretim dışı VNet 'e geçiş yapar).
- Geçişi doğrulamak, uygulama testi gerçekleştirmek ve tam geçişten önce herhangi bir sorunu gidermek için çoğaltılan test Azure VM 'yi kullanabilirsiniz.

Test geçişini aşağıdaki şekilde yapın:


1. **Geçiş hedefleri** > **sunucularında** > **Azuregeçişi: Sunucu Geçişi** bölümünde **Geçirilen sunucuları test et** üzerine tıklayın.

     ![Geçirilen sunucuları test etme](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Test edilecek VM’yi sağ tıklayıp **Test geçişi** üzerine tıklayın.

    ![Test geçişi](./media/tutorial-migrate-vmware/test-migrate.png)

3. **Test Geçişi** bölümünde, Azure VM’nin geçişten sonra bulunacağı Azure VNet’i seçin. Üretim dışı bir VNet kullanmanızı öneririz.
4. **Test Geçişi** işlemi başlar. Portal bildirimlerinde işi izleyin.
5. Geçiş bittikten sonra geçirilen Azure VM’yi, Azure portalında **Sanal Makineler** bölümünde görüntüleyin. Makine adında **Testi** şeklinde bir son ek vardır.
6. Test tamamlandıktan sonra **Makineleri çoğaltma** bölümünde Azure VM’yi sağ tıklayıp **Test geçişini temizle** üzerine tıklayın.

    ![Geçişi temizleme](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>VM’leri geçirme

Test geçişinin beklendiği gibi çalışıp çalışmadığını doğruladıktan sonra şirket içi makineleri geçirebilirsiniz.

1. Azure Geçişi projesi > **Sunucular** > **Azure Geçişi: Sunucu Geçişi** bölümünde, **Sunucuları çoğaltma** üzerine tıklayın.

    ![Sunucuları çoğaltma](./media/tutorial-migrate-vmware/replicate-servers.png)

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
