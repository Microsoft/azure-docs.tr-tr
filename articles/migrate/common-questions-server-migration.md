---
title: Azure geçişi sunucu geçişi hakkında sık sorulan sorular
description: Makineleri geçirmek için Azure geçişi sunucu geçişini kullanma hakkında sık sorulan sorulara yanıtlar alın.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: 63c7f226dcd99ec8040f2078ce12be0fe3c594df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99548823"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure geçişi sunucu geçişi: sık sorulan sorular

Bu makalede, Azure geçişi: sunucu geçiş aracı hakkında sık sorulan sorular yanıtlanmaktadır. Başka sorularınız varsa şu kaynakları kontrol edin:

- Azure geçişi hakkında [genel sorular](resources-faq.md)
- [Azure geçişi](common-questions-appliance.md) gereci hakkında sorular
- [Bulma, değerlendirme ve bağımlılık görselleştirmesiyle](common-questions-discovery-assessment.md) ilgili sorular
- [Azure geçişi forumundaki](https://aka.ms/AzureMigrateForum) soruların yanıtlarını alın

## <a name="does-azure-migrate-convert-uefi-based-machines-to-bios-based-machines-and-migrate-them-to-azure-as-azure-generation-1-vms"></a>Azure geçişi, UEFı tabanlı makineleri BIOS tabanlı makinelere dönüştürüp Azure 1. nesil VM 'Ler olarak Azure 'a geçiremidir?
Azure geçişi: sunucu geçiş aracı, UEFı tabanlı tüm makineleri Azure 2. nesil VM 'Ler olarak Azure 'a geçirir. Artık UEFı tabanlı VM 'lerin BIOS tabanlı VM 'lere dönüştürülmesini desteklemiyoruz. Tüm BIOS tabanlı makinelerin yalnızca Azure 1. nesil VM 'Ler olarak Azure 'a geçirildiğini unutmayın.

## <a name="how-can-i-migrate-uefi-based-machines-to-azure-as-azure-generation-1-vms"></a>UEFı tabanlı makineleri Azure 1. nesil VM 'Ler olarak Azure 'a nasıl geçirebilirim?
Azure geçişi: sunucu geçiş aracı, UEFı tabanlı makineleri Azure 2. nesil VM 'Ler olarak Azure 'a geçirir. Bunları Azure 1. nesil VM 'lerine geçirmek istiyorsanız, çoğaltmayı başlatmadan önce önyükleme türünü BIOS 'a dönüştürün ve Azure 'a geçiş yapmak için Azure geçişi: sunucu geçiş aracını kullanın.
 
## <a name="which-operating-systems-are-supported-for-migration-of-uefi-based-machines-to-azure"></a>UEFı tabanlı makinelerin Azure 'a geçirilmesi için hangi işletim sistemleri desteklenir?

| **UEFı tabanlı makineler için desteklenen işletim sistemleri** | **Aracısız VMware 'den Azure 'a**                                                                                                             | **Azure 'dan aracısız Hyper-V** | **Azure 'da aracı tabanlı VMware, fiziksel ve diğer bulutlar** |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| Windows Server 2019, 2016, 2012 R2, 2012                | E                                                                                                                                         | E                              | E                                                          |
| Windows 10 Pro, Windows 10 Enterprise                   | E                                                                                                                                         | E                              | E                                                          |
| SUSE Linux Enterprise Server 15 SP1                     | E                                                                                                                                         | E                              | E                                                          |
| SUSE Linux Enterprise Server 12 SP4                     | E                                                                                                                                         | E                              | E                                                          |
| Ubuntu Server 16,04, 18,04, 19,04, 19,10                | E                                                                                                                                         | E                              | E                                                          |
| RHEL 8,1, 8,0, 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x        | Y<br>                 _RHEL 8. x [el ile hazırlık](./prepare-for-migration.md#linux-machines) gerektiriyor_   | E                              | E                                                          |
| Sent OS 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 6. x               | Y<br>_Sent OS 8. x [el ile hazırlık](./prepare-for-migration.md#linux-machines) gerektirir_ | E                              | E                                                          |
| Oracle Linux 7,7, 7,7-CI                                |  E                                                                                                                                        | E                              | E                                                          |

## <a name="can-i-use-the-recovery-services-vault-created-by-azure-migrate-for-disaster-recovery-scenarios"></a>Olağanüstü durum kurtarma senaryoları için Azure geçişi tarafından oluşturulan Kurtarma Hizmetleri kasasını kullanabilir miyim?
Olağanüstü durum kurtarma senaryoları için Azure geçişi tarafından oluşturulan Kurtarma Hizmetleri kasasının kullanılmasını önermiyoruz. Bunun yapılması, Azure geçişi 'nde çoğaltma başarısızlıklarını başlatmaya neden olabilir. 

## <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>Aracı tabanlı geçişler için çoğaltma gerecini nereye yüklemeliyim?

Çoğaltma gereci adanmış bir makineye yüklenmelidir. Çoğaltma gereci, çoğaltmak istediğiniz bir kaynak makineye veya daha önce yüklemiş olduğunuz Azure geçişi bulma ve değerlendirme gerecine yüklenmelidir. Daha fazla ayrıntı için [öğreticiyi](./tutorial-migrate-physical-virtual-machines.md) izleyin.

## <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>AWS EC2 örneklerimi Azure 'a nasıl geçirebilirim?

AWS EC2 örneklerinizi Azure 'a eklemek, değerlendirmek ve geçirmek için bu [makaleyi](./tutorial-migrate-aws-virtual-machines.md) gözden geçirin.

## <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>Amazon Linux Işletim sistemini çalıştıran AWS VM 'Leri geçirebilir miyim?

Amazon Linux işletim sistemi yalnızca AWS 'de desteklendiğinden, Amazon Linux çalıştıran sanal makineler olduğu gibi geçirilemez.
Amazon Linux üzerinde çalışan iş yüklerini geçirmek için Azure 'da bir CentOS/RHEL VM 'yi açabilir ve ilgili iş yükü geçişi yaklaşımını kullanarak AWS Linux makinesinde çalışan iş yükünü geçirebilirsiniz. Örneğin, iş yüküne bağlı olarak, geçişe yardımcı olacak iş yüküne özgü araçlar (Web sunucuları durumunda veritabanları veya dağıtım araçları gibi) olabilir.

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Azure geçişi ile geçiş için hangi coğrafi dosyalar desteklenir?

[Genel](migrate-support-matrix.md#supported-geographies-public-cloud) ve [kamu bulutları](migrate-support-matrix.md#supported-geographies-azure-government) için desteklenen coğrafyaları inceleyin.

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>Aynı Azure geçişi projesini birden çok bölgeye geçirmek için kullanabilir miyim?

Bir Azure geçişi projesinde birden çok bölge için değerlendirmeler oluşturabileceğiniz gibi, tek bir Azure geçişi projesi yalnızca bir Azure bölgesine sunucu geçirmek için kullanılabilir. Geçiş yapmanız gereken her bölge için ek Azure geçişi projesi oluşturabilirsiniz.

- Aracısız VMware geçişleri için, ilk çoğaltmayı etkinleştirdikten sonra hedef bölge kilitlenir.
- Aracı tabanlı geçişler (VMware, fiziksel sunucular ve diğer bulutlardan sunucular) için, çoğaltma gereci ayarlanırken portalda "kaynak oluştur" düğmesine tıklandığında hedef bölge kilitlenir.
- Aracısız Hyper-V geçişleri için, Hyper-V çoğaltma sağlayıcısını ayarlarken portalda "kaynak oluştur" düğmesine tıklandığında hedef bölge kilitlenir.

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>Aynı Azure geçişi projesini birden çok aboneliğe geçirmek için kullanabilir miyim? 

Evet, bir Azure geçişi projesi için aynı hedef bölgede birden çok aboneliğe geçiş yapabilirsiniz. Bir makine veya makine kümesi için çoğaltmayı etkinleştirirken hedef aboneliği seçebilirsiniz. Hedef bölge, aracısız VMware geçişleri için ilk çoğaltma sonrasında ve aracı tabanlı geçişler ve aracısız Hyper-V geçişleri için çoğaltma gereci ve Hyper-V sağlayıcısı yüklemesi sırasında kilitlidir.

## <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>Azure geçişi: sunucu geçişi ' nde geçiş seçenekleri nelerdir?

Azure geçişi: sunucu geçiş aracı, kaynak sunucularınızın/sanal makinelerinizin Azure 'dan aracısız geçiş ve aracı tabanlı geçişe geçişini gerçekleştirmek için iki seçenek sunar.

Seçili geçiş seçeneği ne olursa olsun, Azure geçişi kullanarak bir sunucuyu geçirmeye yönelik ilk adım: sunucu geçişi, sunucu için çoğaltmayı etkinleştirmektir. Bu, VM/sunucu verilerinizin ilk çoğaltmasını Azure 'a uygular. İlk çoğaltma tamamlandıktan sonra, artımlı verileri Azure 'a geçirmek için devam eden bir çoğaltma (devam eden Delta eşitleme) oluşturulur. İşlem Delta eşitleme aşamasına ulaştıktan sonra dilediğiniz zaman Azure 'a geçiş yapabilirsiniz.  

Geçiş seçeneğinde karar verirken göz önünde bulundurmanız gereken bazı noktalar aşağıda verilmiştir.

**Aracısız geçişler** , geçirilmekte olan kaynak VM 'lere/sunuculara herhangi bir yazılımın (aracıların) dağıtılmasını gerektirmez. Aracısız seçeneği, sanallaştırma sağlayıcısı tarafından sunulan işlevlerle tümleştirerek çoğaltmayı düzenler.
Aracısız çoğaltma seçenekleri [VMware VM](./tutorial-migrate-vmware.md) 'Leri ve [Hyper-V VM 'leri](./tutorial-migrate-hyper-v.md)için kullanılabilir.

**Aracı tabanlı geçişler** , geçirilecek kaynak VM 'lere/makinelere Azure geçiş yazılımının (aracıların) yüklenmesini gerektirir. Aracı tabanlı seçenek, çoğaltma işlevselliğine yönelik sanallaştırma platformuna bağlı değildir ve bu nedenle, x86/x64 mimarisi çalıştıran herhangi bir sunucu ve aracı tabanlı çoğaltma yöntemi tarafından desteklenen bir işletim sistemi sürümü ile kullanılabilir.

Aracı tabanlı geçiş seçeneği, [VMware VM](./tutorial-migrate-vmware-agent.md)'leri, [Hyper-V VM 'leri](./tutorial-migrate-physical-virtual-machines.md), [fiziksel sunucular](./tutorial-migrate-physical-virtual-machines.md), [AWS üzerinde](./tutorial-migrate-aws-virtual-machines.md)çalışan VM 'ler, GCP üzerinde çalışan sanal makineler veya farklı bir sanallaştırma sağlayıcısında çalışan VM 'ler için kullanılabilir. Aracı tabanlı geçiş, makinelerinizi geçiş amacına göre fiziksel sunucu olarak değerlendirir.

Aracısız geçiş, desteklenen senaryolar (VMWare ve Hyper-V) için aracı tabanlı çoğaltma seçeneklerine ek kolaylık ve kolaylık sağlarken, aşağıdaki kullanım örnekleri için aracı tabanlı senaryoyu kullanmayı düşünebilirsiniz:

- IOPS kısıtlanmış ortam: aracısız çoğaltma, anlık görüntüler kullanır ve depolama ıOPS/bant genişliği kullanır. Ortamınızda depolamada/ıOPS üzerinde kısıtlamalar varsa, aracı tabanlı geçiş yöntemini öneririz.
- VCenter Server yoksa, VMware VM 'lerinizi fiziksel sunucu olarak kabul edebilir ve aracı tabanlı geçiş iş akışını kullanabilirsiniz.

Daha fazla bilgi edinmek için, VMware geçişleri için geçiş seçeneklerini karşılaştırmak üzere bu [makaleyi](./server-migrate-overview.md) gözden geçirin.

## <a name="how-does-agentless-migration-work"></a>Aracısız geçiş nasıl çalışır?

Azure geçişi: sunucu geçişi, VMware sanal makinelerinin ve Windows veya Linux çalıştıran Hyper-V sanal makinelerinin geçirilmesi için aracısız çoğaltma seçenekleri sağlar. Araç ayrıca, fiziksel sunucuları geçirmek için kullanılabilen Windows ve Linux sunucuları için, VMware, Hyper-V, AWS, GCP, vb. üzerinde de x86/x64 sanal makineleri için ek bir aracı tabanlı çoğaltma seçeneği sağlar. Aracı tabanlı çoğaltma seçeneği, geçirilmekte olan sunucu/sanal makinede aracı yazılımının yüklenmesini gerektirir, ancak aracısız seçeneğinde hiçbir yazılım sanal makinelere yüklenemeyecektir ve bu sayede aracı tabanlı çoğaltma seçeneği üzerinde ek kolaylık ve kolaylık sağlar.

Aracısız çoğaltma seçeneği, sanallaştırma sağlayıcısı (VMware, Hyper-V) tarafından sunulan mekanizmalar kullanılarak işe yarar. VMware sanal makineleri söz konusu olduğunda aracısız çoğaltma mekanizması, sanal makine disklerinden verileri çoğaltmak için VMware anlık görüntülerini ve VMware değiştirilmiş blok izleme teknolojisini kullanır. Bu mekanizma birçok yedekleme ürünü tarafından kullanılan bir ile benzerdir. Hyper-V sanal makineleri söz konusu olduğunda, aracısız çoğaltma mekanizması sanal makine disklerinden verileri çoğaltmak için VM anlık görüntülerini ve Hyper-V çoğaltmasının değişiklik izleme özelliğini kullanır.

Bir sanal makine için çoğaltma yapılandırıldığında, önce ilk çoğaltma aşamasından geçer. İlk çoğaltma sırasında, bir VM anlık görüntüsü alınır ve anlık görüntü disklerinden verilerin tam kopyası aboneliğinizdeki yönetilen disklere çoğaltılır. Sanal makine için ilk çoğaltma işlemi tamamlandıktan sonra, çoğaltma işlemi artımlı çoğaltma (Delta çoğaltma) aşamasına geçiş yapar. Artımlı çoğaltma aşamasında, son tamamlanan çoğaltma döngüsünden bu yana gerçekleşen veri değişiklikleri düzenli aralıklarla çoğaltılır ve çoğaltma tarafından yönetilen disklere uygulandıktan sonra çoğaltma, VM 'deki değişikliklerle eşitlenmiş şekilde devam ediyor. VMware sanal makineleri söz konusu olduğunda, çoğaltma döngüleri arasındaki değişiklikleri izlemek için VMware değiştirilmiş blok izleme teknolojisi kullanılır. Çoğaltma döngüsünün başlangıcında, geçerli anlık görüntü ile son başarıyla çoğaltılan anlık görüntü arasındaki değişiklikleri almak için bir VM anlık görüntüsü alınır ve değiştirilmiş blok izleme kullanılır. Bu şekilde, sanal makine için çoğaltmayı eşitlenmiş halde tutmak üzere yalnızca son tamamlanan çoğaltma döngüsünün çoğaltılmasından bu yana değiştirilen verilerin çoğaltılması gerekir. Her çoğaltma döngüsünün sonunda, anlık görüntü serbest bırakılır ve sanal makine için anlık görüntü birleştirme gerçekleştirilir. Benzer şekilde, Hyper-V sanal makineleri söz konusu olduğunda, ardışık çoğaltma döngüleri arasındaki değişiklikleri izlemek için Hyper-V çoğaltma değişikliği izleme motoru kullanılır.
Çoğaltma işlemini çoğaltılan bir sanal makinede gerçekleştirdiğinizde, şirket içi sanal makineyi kapatıp, sıfır veri kaybı sağlamak için bir son artımlı çoğaltma gerçekleştirebilirsiniz. Geçiş seçeneğini gerçekleştirirken, sanal makineye karşılık gelen çoğaltma tarafından yönetilen diskler, Azure 'da sanal makine oluşturmak için kullanılır.

Başlamak için, [VMware aracısız geçişi](./tutorial-migrate-vmware.md) ve [Hyper-V aracısız geçiş](./tutorial-migrate-hyper-v.md) öğreticilerine bakın.

## <a name="how-does-agent-based-migration-work"></a>Aracı tabanlı geçiş nasıl çalışır?

VMware sanal makineleri ve Hyper-V sanal makineleri için aracısız geçiş seçeneklerine ek olarak, sunucu geçiş aracı fiziksel sunucularda çalışan Windows ve Linux sunucularını geçirmek ya da VMware, Hyper-V, AWS, Google Cloud Platform vb. üzerinde x86/x64 sanal makineleri olarak çalıştırmak için aracı tabanlı bir geçiş seçeneği sağlar.

Aracı tabanlı geçiş yöntemi, sunucu verilerini Azure 'a çoğaltmak için geçirilmekte olan sunucuda yüklü aracı yazılımını kullanır. Çoğaltma işlemi, aracının çoğaltma verileri çoğaltma gereci veya yapılandırma sunucusu (veya genişleme Işlem sunucusu) adlı ayrılmış bir çoğaltma sunucusuna aktardığı bir yük boşaltma mimarisi kullanır. Aracı tabanlı geçiş seçeneğinin nasıl çalıştığı hakkında [daha fazla bilgi edinin](./agent-based-migration-architecture.md) . 

Note: çoğaltma gereci Azure geçişi bulma gerecinden farklıdır ve ayrı/ayrılmış bir makineye yüklenmelidir.

## <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>Nasıl yaparım? geçişlerim için bant genişliği gereksinimini ölçer mi?

Azure 'a veri çoğaltma için kullanılan bant genişliği, bir dizi etkene bağlıdır ve şirket içi Azure geçişi gerecinin verileri okuyup Azure 'a çoğaltmasının ne kadar hızlı olduğunu gösteren bir işlevdir. Çoğaltma iki aşamaya sahiptir: ilk çoğaltma ve Delta çoğaltma.

Bir VM için çoğaltma başladığında, disklerin tam kopyalarının çoğaltılacağı bir ilk çoğaltma çevrimi meydana gelir. İlk çoğaltma tamamlandıktan sonra, artımlı çoğaltma döngüleri (Delta döngüleri), önceki çoğaltma döngüsünden bu yana gerçekleşen tüm değişiklikleri aktarmak için düzenli olarak zamanlanır.

### <a name="agentless-vmware-vm-migration"></a>Aracısız VMware VM geçişi

İlk çoğaltmanın tamamlanmasını istediğiniz dalga ve zaman içinde taşınması gereken veri hacmine bağlı olarak bant genişliği gereksinimini ortadan kaldırabilirsiniz (ideal olarak, ilk çoğaltmanın gerçek pencere üzerinde en az 3-4 gün önce tamamlanmasını ve pencere sırasında kapalı kalma süresini en az bir kez devam ettirmek için) yeterli zaman vermesini istemeniz gerekir.

Aşağıdaki formülü kullanarak aracısız VMware VM geçişi için gereken bant genişliğini veya süreyi tahmin edebilirsiniz:

Çoğaltma için ilk çoğaltma süresi = {disk boyutu (veya varsa kullanılan boyutu) * 0,7 (%30 sıkıştırma ortalama – koruyucu tahmin varsayılıyor)}/bant genişliği çoğaltma için kullanılabilir.

### <a name="agent-based-vmware-vm-migration"></a>Aracı tabanlı VMware VM geçişi

Aracı tabanlı bir çoğaltma yöntemi için, dağıtım planlayıcısı ortamın veri dalgalanma göre profilini oluşturup gerekli bant genişliği gereksinimini tahmin etmenize yardımcı olabilir. Daha fazla bilgi edinmek için bu [makaleyi](./agent-based-migration-architecture.md#plan-vmware-deployment)görüntüleyin. 

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Aracısız VMware çoğaltması için Azure geçişi aracı 'nı kullanarak çoğaltma Nasıl yaparım? kısıtlaması yapılsın mı?  

NetQosPolicy kullanarak kısıtlama yapabilirsiniz. Örnek:

NetQosPolicy içinde kullanılacak AppNamePrefix "GatewayWindowsService.exe" dir. Bunun gibi bir ilke oluşturarak gerecden gelen çoğaltma trafiğini kısıtlamak için Azure geçişi gereci üzerinde bir ilke oluşturabilirsiniz:

New-NetQosPolicy-adı "kısıtlanacak"-AppPathNameMatchCondition "GatewayWindowsService.exe"-kısıtlar Lerateactionbitspersecond 1 MB

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>Veriler şirket içi ortamdan Azure 'a nasıl iletilir? İletimden önce şifrelendi mı?

Aracısız çoğaltma durumunda Azure geçişi gereci verileri sıkıştırır ve karşıya yüklemeden önce şifreler. Veriler, https üzerinden güvenli bir iletişim kanalı üzerinden iletilir ve TLS 1,2 veya sonraki bir sürümü kullanır. Ayrıca, Azure depolama, verilerinizi buluta kalıcı hale geldiğinde otomatik olarak şifreler (bekleyen şifreleme).  

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Dalgalanma oranı aracısız çoğaltmayı nasıl etkiler?

Aracısız çoğaltma veride veri katdığından, *karmaşıklık* *oranı karmaşıklık hızından* daha önemlidir. Bir dosya tekrar yazıldığında ve yeniden yazıldığında, oran çok etkiye sahip değildir. Ancak, diğer her kesimin yazıldığı bir model sonraki döngüde yüksek dalgalanma neden olur. Aktardığımız veri miktarını en aza indirdiğimiz için, bir sonraki döngüyü zamanlamadan önce verilerin mümkün olduğunca katlarına izin veririz.

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Çoğaltma döngüsünü ne sıklıkta planlıyor?

Sonraki çoğaltma döngüsünü zamanlamaya yönelik formül (önceki bir dönem/2) veya bir saat, hangisi daha yüksek.

Örneğin, bir VM bir Delta çevrimi için dört saat sürüyorsa, sonraki zaman bir sonraki saat içinde değil, iki saat içinde zamanlanır. İlk yineleme döngüsünün hemen zamanlandığı zaman, işlem ilk çoğaltmadan hemen sonra farklıdır.

## <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>Nasıl yaparım? VMware/Hyper-V üzerinde çalışan Windows Server 2003 ' i Azure 'a geçirin.

[Windows Server 2003 genişletilmiş desteği](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) 14 Temmuz 2015 tarihinde sona erdi.  Azure destek ekibi, Azure 'da Windows Server 2003 ' i çalıştırmaya yönelik sorunları gidermeye yardımcı olmaya devam etmektedir. Ancak, bu destek, işletim sistemi düzeyinde sorun giderme veya düzeltme eki gerektirmeyen sorunlarla sınırlıdır.
Uygulamalarınızı Windows Server 'ın daha yeni bir sürümünü çalıştıran Azure örneklerine geçirmek, Azure bulutunun esnekliğini ve güvenilirliğini etkili bir şekilde kullandığınızdan emin olmak için önerilen yaklaşımdır.

Bununla birlikte, Windows Server 2003 ' ı Azure 'a geçirmeyi hala tercih ediyorsanız, Windows Server 'ın VMware veya Hyper-V ' d e çalışan bir VM olması durumunda Azure geçişi: sunucu geçiş aracı 'nı kullanarak Windows Server [2003 makinelerinizi geçişe hazırlamak](./prepare-windows-server-2003-migration.md)için bu makaleyi gözden geçirin.

## <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>Test geçişi ve geçiş işlemleri arasındaki fark nedir?

Test geçişi, gerçek geçişten önce geçişleri test etmek ve doğrulamak için bir yol sağlar. Test geçişi, Azure 'da bir korumalı alan ortamında VM 'lerin çoğaltılmasının test kopyalarını oluşturmanıza izin vererek işe yarar. Korumalı alan ortamı, belirttiğiniz bir test sanal ağı tarafından kaldırılıyor. Test geçiş işlemi, yalıtılmış bir korumalı alan ortamında kopyalanmış bir kopyada testler gerçekleştirmenize izin vererek, kaynak üzerinde çalışmaya devam eden uygulamalar kesintiye uğratılmamış. Geçişi doğrulamak, uygulama testi gerçekleştirmek ve gerçek geçişten önce herhangi bir sorunu gidermek için gerektiğinde birden çok test gerçekleştirebilirsiniz.

## <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>Windows Server 2008 ve 2008 R2 geçişten sonra Azure 'da desteklenecek mi?

Şirket içi Windows Server 2008 ve 2008 R2 sunucularınızı Azure sanal makinelerine geçirebilir ve destek tarihlerinin sonuna kadar, sanal makineyi çalıştırmanın maliyetinin üzerinde ek ücret ödemeden, üç yıl boyunca genişletilmiş güvenlik güncelleştirmeleri edinebilirsiniz. Windows Server 2008 ve 2008 R2 iş yüklerinizi geçirmek için Azure geçişi: sunucu geçiş aracı 'nı kullanabilirsiniz.

## <a name="is-there-a-rollback-option-for-azure-migrate"></a>Azure geçişi için bir geri alma seçeneği var mı?

Azure 'da uygulama işlevselliği ve performansınızı doğrulamak için test geçiş seçeneğini kullanabilirsiniz. Herhangi bir sayıda test geçişi gerçekleştirebilirsiniz ve test geçiş işlemi aracılığıyla güven kurulduktan sonra son geçişi yürütebilirler. Bir test geçişi, devam eden ve gerçek geçişi gerçekleştirene kadar çoğaltmaya devam eden şirket içi makineyi etkilemez. Test geçişi sırasında herhangi bir hata oluşursa, son geçişi ertelemeyi ve kaynak VM/sunucunuzu çalışır durumda tutmayı ve Azure 'a çoğaltmayı tercih edebilirsiniz. Hataları çözdükten sonra son geçişi yeniden deneyebilirsiniz.  
Note: Azure 'a son geçiş gerçekleştirdikten sonra şirket içi kaynak makine kapatıldıktan sonra, Azure 'dan şirket içi ortamınıza geri alma işlemi gerçekleştiremezsiniz.

## <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>Test geçişleri için kullanılacak sanal ağı ve alt ağı seçebilir miyim?

Test geçişleri için bir sanal ağ seçebilirsiniz. Alt ağ aşağıdaki mantığa göre otomatik olarak seçilir:

- Çoğaltma etkinleştirilirken bir hedef alt ağ (varsayılan dışında) bir giriş olarak belirtilmişse, Azure geçişi, test geçişi için seçilen sanal ağda aynı ada sahip bir alt ağ kullanarak öncelik verir.
- Aynı ada sahip alt ağ bulunamazsa Azure geçişi, bir ağ geçidi/Application Gateway/Güvenlik Duvarı/savunma alt ağı olmayan alfabetik olarak kullanılabilir ilk alt ağı seçer.

## <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>Sunucu için test geçiş düğmesi neden devre dışı bırakıldı?

Test geçiş düğmesi aşağıdaki senaryolarda devre dışı durumda olabilir:

- VM için bir ilk çoğaltma (IR) tamamlanana kadar bir test geçişine başlayamazsınız. IR işlemi tamamlanana kadar test geçiş düğmesi devre dışı bırakılır. VM 'niz bir Delta eşitleme aşamasıdır bir test geçişi gerçekleştirebilirsiniz.
- Test geçişi zaten tamamlanmışsa düğme devre dışı bırakılabilir, ancak bu VM için bir test geçişi temizliği gerçekleştirilmedi. Bir test geçişi temizliği gerçekleştirin ve işlemi yeniden deneyin.

## <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>Test geçişimi temizleyediğimde ne olur?

Test geçişi, çoğaltılan verileri kullanarak bir test Azure VM oluşturarak gerçek geçişin benzetimini yapar. Sunucu, bir "-test" sonekiyle çoğaltılan verilerin bir zaman içindeki kopyası olan hedef kaynak grubuna (çoğaltma etkinleştirilirken seçilir) dağıtılır. Test geçişleri sunucu işlevselliğini doğrulamaya yöneliktir, böylece geçiş sonrası sorunları en aza indirilir. Test geçişi, testi sonrası testi temizlenmemiş ise, test sanal makinesi Azure 'da çalışmaya devam edecektir ve ücretlendirilecektir. Bir test geçişi sonrası temizleme işlemini temizlemek için sunucu geçiş aracında çoğaltma makineleri görünümüne gidin ve makinede ' test geçişini Temizle ' eylemini kullanın.

## <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>Active Directory etki alanı denetleyicilerini Azure geçişi 'ni kullanarak geçirebilir miyim?

Sunucu geçiş aracı, uygulamanın belirsiz olduğu ve çoğu uygulama için çalışmaktadır. Sunucu geçiş aracını kullanarak bir sunucuyu geçirdiğinizde, sunucuda yüklü olan tüm uygulamalar onunla birlikte geçirilir. Ancak bazı uygulamalarda, sunucu geçişi dışındaki alternatif geçiş yöntemleri geçişe daha uygun olabilir.  Active Directory için, şirket içi sitenin Azure ortamınıza bağlandığı karma ortamlarda, Azure 'a ek etki alanı denetleyicileri ekleyerek ve Active Directory çoğaltma ayarlayarak dizininizi Azure 'a genişletebilirsiniz. Azure 'da kendi etki alanı denetleyicilerini gerektiren yalıtılmış bir ortama geçiriyorsanız (veya bir korumalı alan ortamında uygulamaları test edebilirsiniz), sunucu geçiş aracını kullanarak sunucuları geçirebilirsiniz.

## <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>Geçişten sonra çoğaltmayı durdurmazsanız ne olur?

Çoğaltmayı durdurduğunuzda Azure geçişi: sunucu geçiş aracı, çoğaltma için oluşturulan abonelikte yönetilen diskleri temizler. Bir geçişten sonra çoğaltmayı durdurmazsanız, bu disklere yönelik ücret almaya devam edersiniz. Çoğaltmayı durdur, zaten geçirilmiş makinelere bağlı diskleri etkilemez.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>VMware VM 'lerini geçirmek için VMware vCenter 'a ihtiyacım var mı?

VMware Aracısı tabanlı veya aracısız geçiş kullanarak [VMware VM 'lerini geçirmek](server-migrate-overview.md) Için, sanal makinelerin bulunduğu ESXi konaklarının vCenter Server tarafından yönetilmesi gerekir. VCenter Server yoksa, VMware VM 'lerini fiziksel sunucu olarak geçirerek geçirebilirsiniz. [Daha fazla bilgi edinin](migrate-support-matrix-physical-migration.md).

## <a name="can-i-upgrade-my-os-while-migrating"></a>Geçiş sırasında işletim sistemini yükseltebilir miyim?

Azure geçişi: sunucu geçiş aracı, şu anda yalnızca benzer geçişleri destekler. Araç, geçiş sırasında işletim sistemi sürümünün yükseltilmesini desteklemez. Geçirilen makine, kaynak makineyle aynı işletim sistemine sahip olacaktır.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>VCenter Server VM 'Leri bulmaya yönelik iki (veya daha fazla) gereç dağıttım. Ancak, VM 'Leri geçirmeye çalıştığımda yalnızca gereçlerden birine karşılık gelen VM 'Leri görüyorum.

Ayarlanacak birden çok alet varsa, sağlanan vCenter hesaplarındaki sanal makineler arasında çakışma olmaması gerekir. Böyle bir çakışmayla bulma işlemi desteklenmeyen bir senaryodur.

## <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>Nasıl yaparım? sanal makinenizin başarıyla geçirilip geçirilmediğini bilir mi?

VM 'nizi/sunucunuzu başarıyla geçirdikten sonra sanal makineler sayfasından VM 'yi görüntüleyebilir ve yönetebilirsiniz. Doğrulamak için geçirilen VM 'ye bağlanın.
Alternatif olarak, geçişin başarıyla tamamlanıp tamamlanmadığını denetlemek için, işlemin ' Iş durumu ' nu gözden geçirebilirsiniz. Herhangi bir hata görürseniz, bunları çözümleyin ve geçiş işlemini yeniden deneyin.

## <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>Geçiş sırasında birden fazla kaynak VM 'yi tek bir VM 'de birleştirebilir miyim?

Azure geçiş sunucusu geçiş özellikleri şu anda benzer geçişler için desteklenir. Geçiş kapsamında sunucu birleştirmeyi veya işletim sistemini yükseltmeyi desteklemiyoruz. 

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Aracısız çoğaltma, VMware sunucularını nasıl etkiler?

Aracısız çoğaltma VMware vCenter Server ve VMware ESXi konaklarında bazı performans etkilerine neden olur. Aracısız çoğaltma anlık görüntüler kullandığından, depolama üzerinde ıOPS 'yi tüketir, bu nedenle bazı ıOPS depolama bant genişliği gereklidir. Ortamınızda depolama veya IOPS üzerinde kısıtlamalar varsa aracısız çoğaltmanın kullanılması önerilmez.


## <a name="next-steps"></a>Sonraki adımlar

[Azure geçişi 'ne genel bakış](migrate-services-overview.md)konusunu okuyun.
