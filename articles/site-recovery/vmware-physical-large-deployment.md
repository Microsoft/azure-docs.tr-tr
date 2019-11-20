---
title: Azure Site Recovery VMware/fiziksel olağanüstü durum kurtarmayı ölçeklendirin
description: Çok sayıda şirket içi VMware VM veya Azure Site Recovery ile fiziksel sunucu için Azure 'da olağanüstü durum kurtarmayı ayarlamayı öğrenin.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: e08c7d5f794611a92688e931f35da7482c04407f
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082214"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>VMware VM 'Leri/fiziksel sunucular için ölçekte olağanüstü durum kurtarmayı ayarlama

Bu makalede, [Azure Site Recovery](site-recovery-overview.md) hizmetini kullanarak şirket Içi VMware VM 'lerinin veya üretim ortamınızdaki fiziksel sunucuların büyük sayıları (> 1000) için Azure 'da olağanüstü durum kurtarmanın nasıl ayarlanacağı açıklanır.


## <a name="define-your-bcdr-strategy"></a>BCDR stratejinizi tanımlama

İş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejinizin bir parçası olarak, iş uygulamalarınız ve iş yükleriniz için kurtarma noktası hedeflerini (RPOs) ve kurtarma süresi hedeflerini (RTOs) tanımlarsınız. RTO, devam eden sorunları önlemek için bir iş uygulamasının veya işlemin geri yüklenmesi ve kullanılabilir olması gereken süre ve hizmet düzeyi sayısını ölçer.
- Site Recovery, VMware VM 'Leri ve fiziksel sunucular için sürekli çoğaltma ve RTO için [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) sağlar.
- VMware VM 'Leri için büyük ölçekli olağanüstü durum kurtarma ve ihtiyacınız olan Azure kaynaklarını farklı şekilde planlarken, kapasite hesaplamaları için kullanılacak bir RTO değeri belirtebilirsiniz.


## <a name="best-practices"></a>En iyi uygulamalar

Büyük ölçekli olağanüstü durum kurtarma için bazı genel en iyi uygulamalar. Bu en iyi uygulamalar, belgenin sonraki bölümlerinde daha ayrıntılı bir şekilde ele alınmıştır.

- **Hedef gereksinimleri belirleme**: olağanüstü durum kurtarmayı ayarlamadan önce Azure 'da kapasite ve kaynak gereksinimlerini tahmin edin.
- **Site Recovery bileşenleri Için plan**yapın: tahmini kapasitenizi karşılamak için gereken Site Recovery bileşenlerini (yapılandırma sunucusu, işlem sunucuları) öğrenin.
- **Bir veya daha fazla genişleme işlem sunucusu ayarlama**: yapılandırma sunucusunda varsayılan olarak çalışan işlem sunucusunu kullanmayın. 
- **En son güncelleştirmeleri çalıştırın**: Site Recovery Team Site Recovery bileşenlerinin düzenli olarak yeni sürümlerini yayınlar ve en son sürümleri çalıştırdığınızdan emin olun. Bu konuda size yardımcı olmak için güncelleştirmeler için [yenilikleri](site-recovery-whats-new.md) izleyin ve güncelleştirmeleri yayınlarlar [ve yükler](service-updates-how-to.md) .
- **İzleme**proaktif: olağanüstü durum kurtarma 'yı ve çalışır duruma getirmek için, çoğaltılan makinelerin durumunu ve sağlığını ve altyapı kaynaklarını önceden izlemeniz gerekir.
- **Olağanüstü durum kurtarma ayrıntılarında**: olağanüstü durum kurtarma detaylarını düzenli olarak çalıştırmalısınız. Bu, üretim ortamınızda etkilenmez, ancak Azure 'a yük devretmenin gerektiğinde beklendiği gibi çalışmasını sağlamaya yardımcı olur.



## <a name="gather-capacity-planning-information"></a>Kapasite planlama bilgilerini toplayın

Hedef (Azure) kapasite gereksinimlerinizi değerlendirmenize ve tahmin etmenize yardımcı olmak için şirket içi ortamınız hakkında bilgi toplayın.
- VMware için, bunu yapmak üzere VMware VM 'Leri için Dağıtım Planlayıcısı çalıştırın.
- Fiziksel sunucular için bilgileri el ile toplayın.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>VMware VM 'Leri için Dağıtım Planlayıcısı çalıştırma

Dağıtım Planlayıcısı, VMware şirket içi ortamınız hakkında bilgi toplamanıza yardımcı olur.

- VM 'leriniz için normal karmaşıklığı temsil eden bir dönemde Dağıtım Planlayıcısı çalıştırın. Bu, daha doğru tahminler ve öneriler oluşturur.
- Planner, çalıştığı sunucudan üretilen işi hesaplarken, bu, yapılandırma sunucusu makinesinde Dağıtım Planlayıcısı çalıştırmanızı öneririz. Verimlilik ölçme hakkında [daha fazla bilgi edinin](site-recovery-vmware-deployment-planner-run.md#get-throughput) .
- Henüz ayarlanmış bir yapılandırma sunucusu yoksa:
    - Site Recovery bileşenlere [genel bakış alın](vmware-physical-azure-config-process-server-overview.md) .
    - Üzerinde Dağıtım Planlayıcısı çalıştırmak için [bir yapılandırma sunucusu ayarlayın](vmware-azure-deploy-configuration-server.md).

Ardından, planlayıcısı aşağıdaki gibi çalıştırın:

1. Dağıtım Planlayıcısı [hakkında bilgi edinin](site-recovery-deployment-planner.md) . Portaldan en son sürümü indirebilir veya [doğrudan indirebilirsiniz](https://aka.ms/asr-deployment-planner).
2. Dağıtım Planlayıcısı için [önkoşulları](site-recovery-deployment-planner.md#prerequisites) ve [en son güncelleştirmeleri](site-recovery-deployment-planner-history.md) gözden geçirin ve aracı [indirip ayıklayın](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) .
3. Yapılandırma sunucusunda [dağıtım planlayıcısı çalıştırın](site-recovery-vmware-deployment-planner-run.md) .
4. Tahminleri ve önerileri özetlemek için [bir rapor oluşturun](site-recovery-vmware-deployment-planner-run.md#generate-report) .
5. [Rapor önerilerini](site-recovery-vmware-deployment-planner-analyze-report.md) ve [maliyet tahminleri](site-recovery-vmware-deployment-planner-cost-estimation.md)' ni çözümleyin.

>[!NOTE]
> Araç varsayılan olarak, en fazla 1000 VM için rapor profili ve rapor oluşturacak şekilde yapılandırılmıştır. ASRDeploymentPlanner. exe. config dosyasındaki MaxVMsSupported anahtar değerini artırarak bu sınırı değiştirebilirsiniz.

## <a name="plan-target-azure-requirements-and-capacity"></a>Hedef (Azure) gereksinimlerini ve kapasitesini planlayın

Toplanan tahminleri ve önerileri kullanarak hedef kaynakları ve kapasiteyi planlayabilirsiniz. VMware VM 'Leri için Dağıtım Planlayıcısı çalıştırdıysanız, size yardımcı olmak için bir dizi [rapor önerisi](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) kullanabilirsiniz.

- **Uyumlu VM 'ler**: Azure 'a olağanüstü durum kurtarmaya yönelik VM 'lerin sayısını belirlemek için bu numarayı kullanın. Ağ bant genişliği ve Azure çekirdekleri ile ilgili öneriler bu sayıyı temel alır.
- **Gerekli ağ bant genişliği**: uyumlu VM 'lerin Delta çoğaltması için gereken bant genişliğini aklınızda bulabilirsiniz. 
    - Planlayıcısı çalıştırdığınızda, istenen RPO süresini dakikalar içinde belirtirsiniz. Öneriler, bu RPO %100 ve zamanın %90 ' ünü karşılamak için gereken bant genişliğini gösterir. 
    - Ağ bant genişliği önerileri, planlayıcıda önerilen toplam yapılandırma sunucusu ve işlem sunucusu sayısı için gereken bant genişliğini dikkate alır.
- **Gerekli Azure çekirdekleri**: uyumlu sanal makinelerin sayısına bağlı olarak, hedef Azure bölgesinde ihtiyacınız olan çekirdek sayısını göz önünde bulabilirsiniz. Yeterli çekirdekler yoksa, yük devretme Site Recovery, gerekli Azure sanal makinelerini oluşturamayacak.
- **ÖNERILEN VM toplu iş boyutu**: önerilen toplu iş boyutu, toplu işlemin ilk çoğaltmasını 72 saat içinde varsayılan olarak bitirebilme, %100 ' i bir RPO 'ya göre değil. Saat değeri değiştirilebilir.

Azure kaynakları, ağ bant genişliği ve VM toplu işlem planlaması için bu önerileri kullanabilirsiniz.

## <a name="plan-azure-subscriptions-and-quotas"></a>Azure abonelikleri ve kotaları planlayın

Hedef abonelikteki kullanılabilir kotaların yük devretmeyi işlemek için yeterli olduğundan emin olmak istiyoruz.

**Görev** | **Ayrıntılar** | **Eylem**
--- | --- | ---
**Çekirdekleri denetleyin** | Kullanılabilir kotanın çekirdekleri, yük devretme sırasında toplam hedef sayısına eşit veya daha fazla değilse yük devretme başarısız olur. | VMware VM 'Leri için, hedef abonelikte Dağıtım Planlayıcısı temel öneriyi karşılamak üzere yeterince çekirdeğe sahip olup olmadığınızı denetleyin.<br/><br/> Fiziksel sunucular için Azure çekirdekleri el ile tahminleri karşıladığından emin olun.<br/><br/> Kotaları denetlemek için, Azure portal > **abonelikte**, **kullanım + kotalar**' a tıklayın.<br/><br/> Kotaları artırma hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) .
**Yük devretme sınırlarını denetle** | Yük devretme sayısı Site Recovery yük devretme sınırlarını aşamaz. |  Yük devretme işlemleri sınırları aşarsa, abonelikler ekleyebilir, birden fazla aboneliğe yük devreedebilir veya bir abonelik için kotayı artırabilirsiniz. 


### <a name="failover-limits"></a>Yük devretme sınırları

Sınırlar, bir saat içinde Site Recovery tarafından desteklenen yük devretme sayısını belirtir ve makine başına üç disk kabul edilir.

Uyumluluk ne anlama geliyor? Azure VM 'yi başlatmak için, Azure 'un önyükleme başlatma durumunda bazı sürücülerin olması ve DHCP gibi hizmetlerin otomatik olarak başlayacak şekilde ayarlanması gerekir.
- Uyumlu olan makineler bu ayarlara zaten sahip olur.
- Windows çalıştıran makinelerde uyumluluğu önceden denetleyebilir ve gerekirse uyumlu hale getirebilirsiniz. [Daha fazla bilgi edinin](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Linux makineleri yalnızca yük devretme sırasında uyumlu hale getirilir.

**Makine Azure ile uyumlu mı?** | **Azure VM sınırları (yönetilen disk yük devretmesi)**
--- | --- 
Yes | 2000
Hayır | 1000

- Sınırlar, aboneliğin hedef bölgesinde en az diğer işlerin devam ettiğini varsayar.
- Bazı Azure bölgeleri daha küçüktür ve biraz daha düşük sınırlara sahip olabilir.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Altyapı ve VM bağlantısı planlaması

Azure 'a yük devretmeden sonra, iş yüklerinizin şirket içinde olduğu gibi çalışması ve kullanıcıların Azure VM 'lerinde çalışan iş yüklerine erişmesini sağlamak için gerekli olacaktır.

- Active Directory veya DNS şirket içi altyapınızı Azure 'a devretmek hakkında [daha fazla bilgi edinin](site-recovery-active-directory.md#test-failover-considerations) .
- Yük devretmeden sonra Azure VM 'lerine bağlanmaya hazırlanma hakkında [daha fazla bilgi edinin](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) .



## <a name="plan-for-source-capacity-and-requirements"></a>Kaynak kapasitesini ve gereksinimleri planlayın

Kapasite gereksinimlerini karşılamak için yeterli yapılandırma sunucularınız ve genişleme işlem sunucularınız olması önemlidir. Büyük ölçekli dağıtımınıza başladığınızda, tek bir yapılandırma sunucusu ve tek bir genişleme işlem sunucusu ile çalışmaya başlayın. Önceden tanımlanmış sınırlara ulaştığınızda, ek sunucular ekleyin.

>[!NOTE]
> VMware VM 'Leri için Dağıtım Planlayıcısı, ihtiyacınız olan yapılandırma ve işlem sunucuları hakkında bazı öneriler sağlar. Aşağıdaki yordamlarda yer alan tabloları Dağıtım Planlayıcısı önerinin yerine kullanmanızı öneririz. 


## <a name="set-up-a-configuration-server"></a>Yapılandırma sunucusu ayarlama
 
Yapılandırma sunucusu kapasitesi, veri dalgalanması hızına göre değil, çoğaltılan makine sayısından etkilenir. Ek yapılandırma sunucuları gerekip gerekmediğini anlamak için, bu tanımlı VM sınırlarını kullanın.

**CPU** | **Bellek** | **Önbellek diski** | **Çoğaltılan makine sınırı**
 --- | --- | --- | ---
8 vCPU<br> 2 yuva * 4 çekirdek @ 2,5 GHz | 16 GB | 600 GB | 550 adede kadar makine<br> Her makinede her bir makinenin üç 100 GB diski olduğunu varsayar.

- Bu sınırlar, OVF şablonu kullanılarak ayarlanan bir yapılandırma sunucusunu temel alır.
- Sınırlar, yapılandırma sunucusunda varsayılan olarak çalışan işlem sunucusunu kullanmadığınız varsayılır.

Yeni bir yapılandırma sunucusu eklemeniz gerekiyorsa aşağıdaki yönergeleri izleyin:

- Bir OVF şablonu kullanarak VMware VM olağanüstü durum kurtarma için [bir yapılandırma sunucusu ayarlayın](vmware-azure-deploy-configuration-server.md) .
- Fiziksel sunucular için veya OVF şablonu kullanmıyorum VMware dağıtımları için el ile [bir yapılandırma sunucusu ayarlayın](physical-azure-set-up-source.md) .

Bir yapılandırma sunucusu ayarlarken şunları unutmayın:

- Bir yapılandırma sunucusu ayarlarken, bu, kurulum sonrasında değiştirilmemesi gerektiğinden, içinde bulunduğu abonelik ve kasayı göz önünde bulundurmanız önemlidir. Kasayı değiştirmeniz gerekiyorsa, yapılandırma sunucusunun kasadan ilişkisini kaldırmak ve yeniden kaydetmeniz gerekir. Bu, kasadaki VM 'lerin çoğaltılmasını durduruyor.
- Birden çok ağ bağdaştırıcısı ile bir yapılandırma sunucusu kurmak isterseniz, bunu ayarlama sırasında yapmanız gerekir. Bu, yapılandırma sunucusunu kasaya kaydettikten sonra bunu yapamazsınız.

## <a name="set-up-a-process-server"></a>İşlem sunucusu ayarlama

İşlem sunucusu kapasitesi, çoğaltma için etkinleştirilen makinelerin sayısına göre değil, veri dalgalanma hızlarından etkilenir.

- Büyük dağıtımlarda, her zaman en az bir genişleme işlem sunucusu olmalıdır.
- Ek sunuculara ihtiyacınız olup olmadığını anlamak için aşağıdaki tabloyu kullanın.
- En yüksek özelliği olan bir sunucu eklemenizi öneririz. 


**CPU** | **Bellek** | **Önbellek diski** | **Karmaşıklık oranı**
 --- | --- | --- | --- 
12 vCPU<br> 2 yuva * 6 çekirdek @ 2,5 GHz | 24 GB | 1 GB | 2 TB 'a kadar gün

İşlem sunucusunu aşağıdaki şekilde ayarlayın:

1. [Önkoşulları](vmware-azure-set-up-process-server-scale.md#prerequisites)gözden geçirin.
2. Sunucuyu [portala](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)veya [komut satırından](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line)yükler.
3. Çoğaltılan makineleri yeni sunucuyu kullanacak şekilde yapılandırın. Zaten çoğaltma makineleriniz varsa:
    - Tüm işlem sunucusu iş yükünü yeni işlem sunucusuna [taşıyabilirsiniz](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) .
    - Alternatif olarak, belirli VM 'Leri yeni işlem sunucusuna [taşıyabilirsiniz](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) .



## <a name="enable-large-scale-replication"></a>Büyük ölçekli çoğaltmayı etkinleştir

Kapasiteyi planlayıp ve gerekli bileşen ve altyapıyı dağıttıktan sonra, çok sayıda VM için çoğaltmayı etkinleştirin.

1. Makineleri toplu işlerle sıralayın. Bir toplu iş içindeki VM 'Ler için çoğaltmayı etkinleştirin ve sonra bir sonraki toplu işe geçiş yapın.

    - VMware VM 'Leri için Dağıtım Planlayıcısı raporunda [ÖNERILEN VM toplu iş boyutunu](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) kullanabilirsiniz.
    - Fiziksel makineler için, benzer boyut ve veri miktarına ve kullanılabilir ağ aktarım hızına sahip makinelere göre toplu işlemleri tanımlamanızı öneririz. Amacı, ilk çoğaltmasını aynı süre içinde bitirmesi muhtemel olan toplu makinelere yönelik olarak tasarlanmıştır.
    
2. Bir makineye yönelik disk dalgalanması yüksekse veya dağıtım planlayıcısının sınırlarını aşarsa, çoğaltma için ihtiyaç duymayan kritik olmayan dosyaları (örneğin, günlük dökümleri veya geçici dosyalar) makinede taşıyabilirsiniz. VMware VM 'Leri için, bu dosyaları ayrı bir diske taşıyabilir ve sonra [Bu diski](vmware-azure-exclude-disk.md) çoğaltmayla hariç bırakabilirsiniz.
3. Çoğaltmayı etkinleştirmeden önce, makinelerin [çoğaltma gereksinimlerini](vmware-physical-azure-support-matrix.md#replicated-machines)karşıladığından emin olun.
4. [VMware VM 'leri](vmware-azure-set-up-replication.md#create-a-policy) veya [fiziksel sunucular](physical-azure-disaster-recovery.md#create-a-replication-policy)için bir çoğaltma ilkesi yapılandırın.
5. [VMware VM 'leri](vmware-azure-enable-replication.md) veya [fiziksel sunucular](physical-azure-disaster-recovery.md#enable-replication)için çoğaltmayı etkinleştirin. Bu, seçilen makineler için ilk çoğaltmayı kapatır.

## <a name="monitor-your-deployment"></a>Dağıtımınızı izleme

İlk VM toplu işi için çoğaltmayı başlattıktan sonra, dağıtımınızı şu şekilde izlemeye başlayın:  

1. Çoğaltılan makinelerin sistem durumunu izlemek için bir olağanüstü durum kurtarma Yöneticisi atayın.
2. Çoğaltılan öğeler ve altyapı için [olayları izleyin](site-recovery-monitor-and-troubleshoot.md) .
3. Genişleme işlem sunucularınızın [sistem durumunu izleyin](vmware-physical-azure-monitor-process-server.md) .
4. Daha kolay izleme amacıyla olaylara yönelik [e-posta bildirimleri](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) almak için kaydolun.
5. Her şeyin beklendiği gibi çalıştığından emin olmak için düzenli [olağanüstü durum kurtarma detaylarını](site-recovery-test-failover-to-azure.md)yürütün.


## <a name="plan-for-large-scale-failovers"></a>Büyük ölçekli yük devretme işlemleri için plan yapın

Olağanüstü bir durumda, çok sayıda makine/iş yükünü Azure 'a devretmek gerekebilir. Bu olay türü için aşağıdaki gibi hazırlanın.

Yük devretme için önceden aşağıdaki şekilde hazırlanabilirsiniz:

- İş yüklerinizin yük devretmeden sonra kullanılabilmesi ve kullanıcıların Azure VM 'lerine erişebilmesi için [altyapınızı ve sanal makinelerinizi hazırlayın](#plan-infrastructure-and-vm-connectivity) .
- Bu belgede daha önce geçen [Yük devretme sınırlarına](#failover-limits) göz önünde edin. Yük devretmeleri Bu limitlerin içinde olduğundan emin olun.
- Düzenli [olağanüstü durum kurtarma detaylarını](site-recovery-test-failover-to-azure.md)çalıştırın. Detaydan yardım:
    - Yük devretmeden önce dağıtımınızda boşluklar bulun.
    - Uygulamalarınızın uçtan uca RTO tahminini yapın.
    - İş yükleriniz için uçtan uca RPO 'yu tahmin edin.
    - IP adres aralığı çakışmalarını belirler.
    - Ayrıntıya gitme işlemleri yaparken, detaylara yönelik üretim ağlarını kullanmanıza, üretim ve test ağlarında aynı alt ağ adlarını kullanmaktan kaçınmanızı ve her ayrıntıdan sonra yük devretme testini temizleyeöneririz.

Büyük ölçekli yük devretme çalıştırmak için şunları öneririz:

1. İş yükü yük devretmesi için kurtarma planları oluşturun.
    - Her kurtarma planı, 50 adede kadar makinenin yük devretmesini tetikleyebilir.
    - Kurtarma planları hakkında [daha fazla bilgi edinin](recovery-plan-overview.md) .
2. Azure 'daki tüm el ile görevleri otomatikleştirmek için kurtarma planlarına Azure Otomasyonu runbook betikleri ekleyin. Tipik görevler yük dengeleyicileri yapılandırmayı, DNS 'yi güncellemeyi içerir. [Daha fazla bilgi](site-recovery-runbook-automation.md)
2. Yük devretmeden önce, Windows makinelerini Azure ortamıyla uyumlu olacak şekilde hazırlayın. Uyumlu olan makineler için [Yük devretme limitleri](#plan-azure-subscriptions-and-quotas) daha yüksektir. Runbook 'lar hakkında [daha fazla bilgi edinin](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) .
4.  Bir kurtarma planıyla birlikte [Start-AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell cmdlet 'i ile yük devretmeyi tetikleyin.



## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [İzleyici Site Recovery](site-recovery-monitor-and-troubleshoot.md)
