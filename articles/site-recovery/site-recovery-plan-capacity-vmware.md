---
title: Azure Site Recovery ile VMware olağanüstü durum kurtarma için kapasite planlayın
description: Bu makale, Azure Site Recovery kullanarak VMware VM 'lerinin olağanüstü durum kurtarmasını ayarlarken kapasiteyi ve ölçeklendirmeyi planlamaya yardımcı olabilir.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 467c70a722b8a243be6ac2826188a4ba3459aa06
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961369"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Azure 'da VMware olağanüstü durum kurtarma için kapasiteyi ve ölçeklendirmeyi planlayın

[Azure Site Recovery](site-recovery-overview.md)kullanarak şirket Içi VMware VM 'lerini ve fiziksel sunucuları Azure 'a çoğaltırken kapasiteyi ve ölçeklendirmeyi planlamak için bu makaleyi kullanın.

## <a name="how-do-i-start-capacity-planning"></a>Nasıl yaparım? kapasite planlaması başlatılsın mı?

Azure Site Recovery altyapı gereksinimleri hakkında bilgi edinmek için, VMware çoğaltması için [Azure Site Recovery dağıtım planlayıcısı](https://aka.ms/asr-deployment-planner-doc) çalıştırarak çoğaltma ortamınız hakkında bilgi toplayın. Daha fazla bilgi için bkz. [VMware Için Azure 'a Site Recovery dağıtım planlayıcısı hakkında](site-recovery-deployment-planner.md). 

Site Recovery Dağıtım Planlayıcısı, uyumlu ve uyumsuz VM 'Ler, VM başına disk ve disk başına veri dalgalanması hakkında tüm bilgileri içeren bir rapor sağlar. Araç ayrıca hedef RPO ve başarılı çoğaltma ve yük devretme testi için gereken Azure altyapısını karşılamak için ağ bant genişliği gereksinimlerini özetler.

## <a name="capacity-considerations"></a>Kapasite konuları

Bileşen | Ayrıntılar
--- | ---
**Çoğaltma** | **Günlük değişiklik oranı üst sınırı**: korunan bir makine yalnızca bir işlem sunucusu kullanabilir. Tek bir işlem sunucusu günlük 2 TB'a kadar olan değişiklik oranını işleyebilir. Bu nedenle, 2 TB, korunan bir makine için desteklenen günlük veri değişim oranı üst sınırıdır.<br /><br /> **En yüksek aktarım hızı**: çoğaltılan bir makine, Azure 'daki bir depolama hesabına ait olabilir. Standart bir Azure depolama hesabı, saniye başına en fazla 20.000 isteği işleyebilir. Saniye başına giriş/çıkış işlemi sayısını (ıOPS) bir kaynak makine üzerinden 20.000 olarak sınırlandırmanıza önerilir. Örneğin, beş diski olan bir kaynak makineniz varsa ve her disk kaynak makinede 120 ıOPS (8 K boyutunda) oluşturursa, kaynak makine, 500 olan her disk için Azure ıOPS sınırının içindedir. (Gereken depolama hesabı sayısı, 20.000 ile bölünen toplam kaynak makine ıOPS 'ye eşittir.)
**Yapılandırma sunucusu** | Yapılandırma sunucusu, korunan makinelerde çalışan tüm iş yükleri genelinde günlük değişiklik hızı kapasitesini işleyebilmelidir. Yapılandırma makinesi, verileri sürekli olarak Azure Storage 'a çoğaltmak için yeterli bant genişliğine sahip olmalıdır.<br /><br /> En iyi uygulama, yapılandırma sunucusunu korumak istediğiniz makinelerle aynı ağa ve LAN kesimine yerleştirkullanmaktır. Yapılandırma sunucusunu farklı bir ağa yerleştirebilirsiniz ancak korumak istediğiniz makineler katman 3 ağ görünürlüğüne sahip olmalıdır.<br /><br /> Yapılandırma sunucusu için boyut önerileri, aşağıdaki bölümdeki tabloda özetlenmiştir.
**İşlem sunucusu** | İlk işlem sunucusu, yapılandırma sunucusunda varsayılan olarak yüklenir. Ortamınızı ölçeklendirmek için ek işlem sunucuları dağıtabilirsiniz. <br /><br /> İşlem sunucusu korumalı makinelerden çoğaltma verilerini alır. İşlem sunucusu, önbelleğe alma, sıkıştırma ve şifrelemeyi kullanarak verileri iyileştirir. Daha sonra, işlem sunucusu verileri Azure 'a gönderir. İşlem sunucusu makinesi bu görevleri gerçekleştirmek için yeterli kaynağa sahip olmalıdır.<br /><br /> İşlem sunucusu, disk tabanlı bir önbellek kullanır. Ağ tıkanıklık veya kesinti oluşursa depolanan veri değişikliklerini işlemek için 600 GB veya daha fazla ayrı bir önbellek diski kullanın.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Yapılandırma sunucusu ve yerleşik işlem sunucusu için boyut önerileri

İş yükünü korumak için oluşturulmuş bir işlem sunucusunu kullanan bir yapılandırma sunucusu, aşağıdaki yapılandırmalara göre 200 sanal makineyi işleyebilir:

CPU | Bellek | Önbellek diski boyutu | Veri değişim oranı | Korumalı makineler
--- | --- | --- | --- | ---
8 vCPU (2 yuva * 4 çekirdek \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB veya daha az | 100 'den daha az makine çoğaltmak için kullanın.
12 vCPU (2 yuva * 6 çekirdek \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB ila 1 TB | 100 ' a 150 makine çoğaltmak için kullanın.
16 vCPU (2 yuva * 8 çekirdek \@ 2,5 GHz) | 32 GB | 1 TB | > 1 TB-2 TB | 151 ' a 200 makine çoğaltmak için kullanın.
[Ovf şablonu](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)kullanarak başka bir yapılandırma sunucusu dağıtın. | | | | 200 'den fazla makine çoğaltıyorsanız yeni bir yapılandırma sunucusu dağıtın.
Başka bir [işlem sunucusu](vmware-azure-set-up-process-server-scale.md#download-installation-file)dağıtın. | | | > 2 TB| Günlük toplam veri değişim oranı 2 TB 'den büyükse yeni bir genişleme işlem sunucusu dağıtın.

Şu yapılandırmalarda:

* Her kaynak makinede üç 100 GB disk vardır.
* Önbellek disk ölçümleri için RAID 10 ile 10 K RPM sekiz paylaşılan erişim imza sürücüsünün depolanmasını destekliyoruz.

## <a name="size-recommendations-for-the-process-server"></a>İşlem sunucusu için boyut önerileri

İşlem sunucusu, Azure Site Recovery veri çoğaltmayı işleyen bileşendir. Günlük değişiklik oranı 2 TB 'den büyükse, çoğaltma yükünü işlemek için genişleme işlem sunucuları eklemeniz gerekir. Ölçeği genişletmek için şunları yapabilirsiniz:

* Bir [ovf şablonu](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)kullanarak dağıtarak yapılandırma sunucularının sayısını artırın. Örneğin, iki yapılandırma sunucusu kullanarak en fazla 400 makineyi koruyabilirsiniz.
* [Genişleme işlem sunucuları](vmware-azure-set-up-process-server-scale.md#download-installation-file)ekleyin. Yapılandırma sunucusu (veya buna ek olarak) yerine çoğaltma trafiğini işlemek için genişleme işlem sunucularını kullanın.

Aşağıdaki tabloda bu senaryo açıklanmaktadır:

* Bir genişleme işlem sunucusu ayarlarsınız.
* Korumalı sanal makineleri genişleme işlem sunucusunu kullanacak şekilde yapılandırdınız.
* Her korunan kaynak makinenin her biri 100 GB 'lik üç diski vardır.

Ek işlem sunucusu | Önbellek diski boyutu | Veri değişim oranı | Korumalı makineler
--- | --- | --- | ---
4 vCPU (2 yuva * 2 çekirdek \@ 2,5 GHz), 8 GB bellek | 300 GB | 250 GB veya daha az | 85 veya daha az makine çoğaltmak için kullanın.
8 vCPU (2 yuva * 4 çekirdek \@ 2,5 GHz), 12 GB bellek | 600 GB | 251 GB ila 1 TB | 86 ' a 150 makine çoğaltmak için kullanın.
12 vCPU (2 yuva * 6 çekirdek \@ 2,5 GHz) 24 GB bellek | 1 TB | > 1 TB-2 TB | 151 ' a 225 makine çoğaltmak için kullanın.

Sunucularınızı ölçeklendirmeniz, genişleme veya genişleme modeli için tercihlerinize bağlıdır. Ölçeği genişletmek için, birkaç yüksek kaliteli yapılandırma sunucusu ve işlem sunucusu dağıtın. Ölçeği genişletmek için, daha az kaynağa sahip daha fazla sunucu dağıtın. Örneğin, günlük toplam veri değişim oranı 1,5 TB olan 200 makineleri korumak istiyorsanız aşağıdaki eylemlerden birini yapabilirsiniz:

* Tek bir işlem sunucusu (16 vCPU, 24 GB RAM) ayarlayın.
* İki işlem sunucusu ayarlayın (2 x 8 vCPU, 2 * 12 GB RAM).

## <a name="control-network-bandwidth"></a>Ağ bant genişliğini denetleme

Çoğaltma (ilk çoğaltma ve sonra Delta) için gereken bant genişliğini hesaplamak üzere [Site Recovery dağıtım planlayıcısı](site-recovery-deployment-planner.md) kullandıktan sonra, çoğaltma için kullanılan bant genişliği miktarını denetlemek için kullanabileceğiniz birkaç seçeneğiniz vardır:

* **Bant genişliğini kısıtlama**: Azure 'a çoğaltılan VMware trafiği belirli bir işlem sunucusundan geçer. İşlem sunucusu olarak çalışan makinelerde bant genişliğini azaltmayı seçebilirsiniz.
* **Etki genişliğini etkiler**: birkaç kayıt defteri anahtarını kullanarak, çoğaltma için kullanılan bant genişliğini etkileyebilirsiniz:
  * **HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** kayıt defteri değeri, bir diskin veri aktarımı (başlangıç veya değişim çoğaltması) için kullanılan iş parçacıklarının sayısını belirtir. Daha yüksek bir değer, çoğaltma için kullanılan ağ bant genişliğini artırır.
  * **HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** kayıt defteri değeri, yeniden çalışma sırasında veri aktarımı için kullanılan iş parçacıklarının sayısını belirtir.

### <a name="throttle-bandwidth"></a>Bant genişliğini kısıtlama

1. İşlem sunucusu olarak kullandığınız makinede Azure Backup MMC ek bileşenini açın. Varsayılan olarak, masaüstünde veya şu klasörde yedekleme için bir kısayol bulunur: C:\Program Files\Microsoft Azure kurtarma hizmetleri servisleri \ bin.
2. Ek bileşende **Özellikleri Değiştir**' i seçin.

    ![Özellikleri değiştirmek için Azure Backup MMC ek bileşeni seçeneğinin ekran görüntüsü](./media/site-recovery-vmware-to-azure/throttle1.png)
3. **Daraltma** sekmesinde, **yedekleme işlemleri için internet bant genişliği kullanımını azaltmayı etkinleştir**' i seçin. Çalışma ve çalışılmayan saatlerin sınırlarını ayarlayın. Geçerli aralıklar 512 kbps ile 1.023 Mbps arasındadır.

    ![Azure Backup özellikleri iletişim kutusunun ekran görüntüsü](./media/site-recovery-vmware-to-azure/throttle2.png)

Ayrıca, azaltma ayarı için [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) cmdlet'ini de kullanabilirsiniz. Bir örneği aşağıda verilmiştir:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting - NoThrottle** hiçbir azaltmanın gerekli olmadığını gösterir.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>VM için ağ bant genişliğini değiştirme

1. VM kayıt defterinde **HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows Azure backup\replication hedefine gidin**' a gidin.
   * Çoğaltılan bir diskteki bant genişliği trafiğini değiştirmek için, **UploadThreadsPerVM**değerini değiştirin. Mevcut değilse anahtarı oluşturun.
   * Azure 'dan yeniden çalışma trafiği için bant genişliğini değiştirmek üzere **DownloadThreadsPerVM**değerini değiştirin.
2. Her anahtar için varsayılan değer **4**' dir. "Fazla sağlanan" bir ağda, bu kayıt defteri anahtarlarının varsayılan değerlerinin değiştirilmesi gerekir. Kullanabileceğiniz en büyük değer **32**' dir. Değeri iyileştirmek için trafiği izleyin.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>500 'den fazla VM 'yi korumak için Site Recovery altyapısını ayarlama

Site Recovery altyapısını ayarlamadan önce, aşağıdaki faktörleri ölçmek için ortama erişin: uyumlu sanal makineler, günlük veri değişim oranı, Site Recovery ulaşmak istediğiniz RPO için gereken ağ bant genişliği gerekli olan bileşenler ve ilk çoğaltmayı tamamlaması için geçen süre. Gerekli bilgileri toplamak için aşağıdaki adımları izleyin:

1. Bu parametreleri ölçmek için ortamınızda Site Recovery Dağıtım Planlayıcısı çalıştırın. Yardımcı yönergeler için bkz. [VMware için Site Recovery dağıtım planlayıcısı hakkında](site-recovery-deployment-planner.md).
2. [Yapılandırma sunucusu için boyut önerilerini](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server)karşılayan bir yapılandırma sunucusu dağıtın. Üretim iş yükünüz 650 sanal makineyi aşarsa, başka bir yapılandırma sunucusu dağıtın.
3. Ölçülen günlük veri değişim hızına göre, [Boyut yönergeleri](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)yardımıyla [genişleme işlem sunucuları](vmware-azure-set-up-process-server-scale.md#download-installation-file) dağıtın.
4. Bir disk sanal makinesi için veri değişikliği oranının 2 MBps değerini aşmaması bekleniyorsa, Premium yönetilen diskler kullandığınızdan emin olun. Belirli bir süre için Dağıtım Planlayıcısı çalıştırmaları Site Recovery. Veri değişikliği hızdaki en yüksek düzeyde, raporda yakalanmayabilir.
5. [Ağ bant genişliğini](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) , ulaşmak istediğiniz RPO 'Ya göre ayarlayın.
6. Altyapı ayarlandığında, iş yükünüz için olağanüstü durum kurtarmayı etkinleştirin. Nasıl yapılacağını öğrenmek için bkz. [VMware için kaynak ortamı Azure 'a çoğaltma Için ayarlama](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Ek işlem sunucularını dağıtma

Dağıtımınızı 200 kaynak makineden daha fazla ölçeklendirebilir veya 2 TB 'den fazla toplam günlük değişim oranı varsa, trafik birimini işlemek için işlem sunucuları eklemeniz gerekir. Bir genişleme işlem sunucusu ne zaman ayarlandığında [işlem sunucusu uyarıları](vmware-physical-azure-monitor-process-server.md#process-server-alerts) sağlamak için 9,24 sürümündeki ürünü geliştirdik. Yeni kaynak makineleri korumak veya [yükü dengelemek](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)için [işlem sunucusunu ayarlayın](vmware-azure-set-up-process-server-scale.md) .

### <a name="migrate-machines-to-use-the-new-process-server"></a>Yeni işlem sunucusunu kullanmak için makineleri geçirme

1. **Site Recovery sunucuları** > **ayarları** ' nı seçin. Yapılandırma sunucusunu seçin ve ardından **işlem sunucuları**' nı genişletin.

    ![Işlem sunucusu iletişim kutusunun ekran görüntüsü](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Kullanımda olan işlem sunucusuna sağ tıklayın ve ardından **Değiştir**' i seçin.

    ![Yapılandırma sunucusu iletişim kutusunun ekran görüntüsü](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. **Hedef işlem sunucusunu seçin**bölümünde, kullanmak istediğiniz yeni işlem sunucusunu seçin. Ardından, sunucunun işleyeceği sanal makineleri seçin. Sunucu hakkında bilgi almak için bilgi simgesini seçin. Yük kararları almanıza yardımcı olmak için, seçilen her bir sanal makineyi yeni işlem sunucusuna çoğaltmak üzere gereken ortalama alan gösterilir. Yeni işlem sunucusuna Çoğaltmaya başlamak için onay işaretini seçin.

## <a name="deploy-additional-master-target-servers"></a>Ek ana hedef sunucuları dağıtma

Aşağıdaki senaryolarda, birden fazla ana hedef sunucusu gereklidir:

*   Linux tabanlı bir sanal makineyi korumak istiyorsunuz.
*   Yapılandırma sunucusundaki kullanılabilir ana hedef sunucunun, sanal makinenin veri deposuna erişimi yok.
*   Ana hedef sunucusundaki toplam disk sayısı (sunucudaki yerel disk sayısı artı korunacak disk sayısı) 60 diskten fazla.

Linux tabanlı bir sanal makine için ana hedef sunucu ekleme hakkında bilgi edinmek için bkz. yeniden [çalışma Için Linux ana hedef sunucusu yüklemek](vmware-azure-install-linux-master-target.md).

Windows tabanlı bir sanal makine için ana hedef sunucu eklemek için:

1. **Kurtarma Hizmetleri kasası** > **Site Recovery altyapı** > **yapılandırma sunucularına**gidin.
2. Gerekli yapılandırma sunucusunu seçin ve ardından **ana hedef sunucusu**' nu seçin.

    ![Ana hedef sunucu Ekle düğmesini gösteren ekran görüntüsü](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Birleşik kurulum dosyasını indirin ve ardından ana hedef sunucuyu ayarlamak için dosyayı sanal makinede çalıştırın.
4. **Ana hedef yüklemeyi** > **İleri**' yi seçin.

    ![Ana hedef Install seçeneğini seçen ekran görüntüsü](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Varsayılan yükleme konumunu seçin ve ardından **yükleme**' yi seçin.

     ![Varsayılan yükleme konumunu gösteren ekran görüntüsü](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Ana hedefi yapılandırma sunucusuna kaydetmek için, **yapılandırmaya devam et**' i seçin.

    ![Yapılandırmaya Ilerle düğmesine gösteren ekran görüntüsü](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Yapılandırma sunucusunun IP adresini girin ve parolayı girin. Bir parola oluşturmayı öğrenmek için bkz. [yapılandırma sunucusu parolası oluşturma](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Yapılandırma sunucusu için IP adresinin ve parolanın nereye girdiğinin gösterildiği ekran görüntüsü](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. **Kaydol**’u seçin. Kayıt tamamlandığında **son**' u seçin.

Kayıt başarıyla tamamlandığında, sunucu, **Kurtarma Hizmetleri kasasındaki** Azure portal, **Site Recovery altyapı** > **yapılandırma sunucuları** > , yapılandırma sunucusu.

 > [!NOTE]
 > [Windows için ana hedef sunucu Birleşik kurulum dosyasının](https://aka.ms/latestmobsvc)en son sürümünü indirin.

## <a name="next-steps"></a>Sonraki adımlar

[Site Recovery dağıtım planlayıcısı](https://aka.ms/asr-deployment-planner)indirin ve çalıştırın.
