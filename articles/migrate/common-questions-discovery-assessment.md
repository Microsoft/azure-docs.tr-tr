---
title: Azure geçişi 'nde bulma, değerlendirme ve bağımlılık analizi ile ilgili sorular
description: Azure geçişi 'nde bulma, değerlendirme ve bağımlılık analizi hakkında sık sorulan soruların yanıtlarını alın.
author: rashijoshi
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: c10963d28e0d2ecee73150e8b5af89cee96d28b2
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077015"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Keşif, değerlendirme ve bağımlılık analizi-genel sorular

Bu makalede, Azure geçişi 'nde bulma, değerlendirme ve bağımlılık analizleri hakkında sık sorulan sorular yanıtlanmaktadır. Başka sorularınız varsa şu kaynakları kontrol edin:

- Azure geçişi hakkında [genel sorular](resources-faq.md)
- [Azure geçişi](common-questions-appliance.md) gereci hakkında sorular
- [Sunucu geçişi](common-questions-server-migration.md) hakkında sorular
- [Azure geçişi forumundaki](https://aka.ms/AzureMigrateForum) soruların yanıtlarını alın


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Azure geçişi ile bulma ve değerlendirme için hangi coğrafi dosyalar desteklenir?

[Genel](migrate-support-matrix.md#supported-geographies-public-cloud) ve [kamu bulutları](migrate-support-matrix.md#supported-geographies-azure-government) için desteklenen coğrafyaları inceleyin.


## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Bir gereç ile kaç sunucu keşfedebilirim?

VMware ortamından en fazla 10.000 sunucusu, Hyper-V ortamından en fazla 5.000 sunucuya ve tek bir gereç kullanarak en çok 1000 fiziksel sunucuya kadar bulabilirsiniz. Daha fazla sunucunuz varsa, [Hyper-V değerlendirmesi ölçekleme](scale-hyper-v-assessment.md), [bir VMware değerlendirmesi ölçekleme](scale-vmware-assessment.md)veya [fiziksel sunucu değerlendirmesini ölçeklendirme](scale-physical-assessment.md)hakkında bilgi edinin.

## <a name="how-do-i-choose-the-assessment-type"></a>Değerlendirme türünü nasıl seçebilirim?

- Şirket içi [VMware](how-to-set-up-appliance-vmware.md) ve [Hyper-V](how-to-set-up-appliance-hyper-v.md) ortamınızdan SUNUCULARı değerlendirmek istediğinizde **Azure VM değerlendirmelerini** ve Azure VM 'lerine geçiş için [fiziksel sunucuları](how-to-set-up-appliance-physical.md) kullanın. [Daha Fazla Bilgi](concepts-assessment-calculation.md)

- Azure SQL veritabanı veya Azure SQL yönetilen örneği 'ne geçiş için, VMware ortamınızdan şirket içi SQL Server değerlendirmek istediğinizde, değerlendirme türü **Azure SQL** ' i kullanın. [Daha Fazla Bilgi](concepts-assessment-calculation.md)

- Bu değerlendirme türünü kullanarak [Azure VMware çözümüne (AVS)](../azure-vmware/introduction.md) geçiş için şirket Içi [VMware VM](how-to-set-up-appliance-vmware.md) 'Lerinizi değerlendirmek istediğinizde **Azure VMware çözümü (AVS)** değerlendirmelerini kullanın. [Daha fazla bilgi edinin](concepts-azure-vmware-solution-assessment-calculation.md)

- Her iki tür değerlendirmeyi de çalıştırmak için yalnızca VMware makinelerini içeren ortak bir grup kullanabilirsiniz. Azure Geçişi'nde AVS değerlendirmelerini ilk kez çalıştırıyorsanız, yeni bir VMware makineleri grubu oluşturmanızın önerildiğini aklınızda bulundurun.
 

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Azure VM 'mdeki ve/veya AVS değerlendirmesi raporundaki bazı/tüm sunucular için performans verileri neden eksik?

"Performans tabanlı" değerlendirme için, Azure geçiş gereci şirket içi sunucular için performans verilerini toplayamazsa, değerlendirme raporu dışarı aktarma ' PercentageOfCoresUtilizedMissing ' veya ' PercentageOfMemoryUtilizedMissing ' diyor. Lütfen şunu denetleyin:

- Değerlendirme oluşturduğunuz süre boyunca sunucular açık ise
- Yalnızca bellek sayaçları eksikse ve Hyper-V ortamındaki sunucuları değerlendirmeye çalışıyorsanız. Bu senaryoda, lütfen sunucularda dinamik belleği etkinleştirin ve en son değişiklikleri yansıtacak şekilde değerlendirmesi ' yeniden hesaplayın '. Gereç, Hyper-V ortamındaki yarı sunucular için bellek kullanım değerlerini yalnızca sunucuda dinamik bellek etkinken toplayabilir.

- Tüm performans sayaçları eksikse, 443 (HTTPS) bağlantı noktalarında giden bağlantılara izin verildiğinden emin olun.

    > [!Note]
    > Performans sayaçlarından herhangi biri eksikse, Azure geçişi: Sunucu değerlendirmesi, şirket içinde ayrılmış çekirdeğe/belleğe geri döner ve buna uygun bir VM boyutu önerir.


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Azure SQL değerlendirmem içindeki bazı/tüm SQL örnekleri/veritabanları için performans verileri neden eksik?

Performans verilerinin toplandığından emin olmak için lütfen şunu denetleyin:

- Değerlendirme oluşturduğunuz süre boyunca SQL Server 'lar açık ise
- Azure geçişi 'nde SQL aracısının bağlantı durumu ' Connected ' ise ve son sinyali kontrol ederseniz 
- Azure geçişi 'nin tüm SQL örnekleri için bağlantı durumu, bulunan SQL örneği dikey penceresinde ' bağlı ' ise
- Performans sayaçlarının hepsi eksikse 443 numaralı bağlantı noktası üzerinden giden bağlantılara (HTTPS) izin verildiğinden emin olun

Performans sayaçlarından herhangi biri eksikse, Azure SQL değerlendirmesi Bu örnek/veritabanı için en küçük Azure SQL yapılandırmasını önerir.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Değerlendirmemin güvenilirlik derecesi neden düşük?

"Performans tabanlı" değerlendirmeler için güvenilirlik derecesi, değerlendirmeyi hesaplarken gereken [kullanılabilir veri noktaları](./concepts-assessment-calculation.md#ratings) yüzdesi temelinde hesaplanır. Değerlendirme güvenilirlik derecesinin düşük olmasının nedenlerini aşağıda bulunabilirsiniz:

- Değerlendirmeyi oluşturduğunuz süre boyunca ortamınızın profilini oluşturmadınız. Örneğin değerlendirmeyi bir hafta olarak ayarlanmış performans süresiyle oluşturuyorsanız, toplanacak tüm veri noktalarının keşfini başlattıktan sonra en az bir hafta beklemeniz gerekir. Süre bekleyemez, lütfen performans süresini daha küçük bir süre değiştirin ve değerlendirmeyi **yeniden hesaplayın** .
 
- Değerlendirme, değerlendirme süresi içinde sunucuların bir bölümü veya tümü için performans verilerini toplayamıyor. Yüksek güvenilirlikli bir derecelendirme için lütfen şunları doğrulayın: 
    - Değerlendirme süresi boyunca sunucular açık
    - 443 bağlantı noktalarında giden bağlantılara izin verilir
    - Hyper-V sunucuları için dinamik bellek etkin 
    - Azure geçişi 'ndeki aracıların bağlantı durumu ' Connected ' ve son sinyali denetle
    - Azure SQL değerlendirmelerinde, tüm SQL örnekleri için Azure geçişi bağlantı durumu bulunan SQL örneği dikey penceresinde "bağlandı" olarak ayarlanır

    Güvenilirlik derecelendirmesindeki en son değişiklikleri yansıtacak şekilde değerlendirmeyi **Yeniden Hesaplayın**.

- Azure VM ve AVS değerlendirmelerinde, bulma başlatıldıktan sonra birkaç sunucu oluşturulmuştur. Örneğin, son bir ayın performans geçmişi için bir değerlendirme oluşturuyorsanız ancak ortamda yalnızca bir hafta önce birkaç sunucu oluşturuluyorsa. Bu durumda, yeni sunucular için performans verileri sürenin tamamına uygun olmayacaktır ve güvenirlik derecelendirmesi düşük olacaktır. [Daha fazla bilgi edinin](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- Azure SQL değerlendirmelerinde, bulma başlatıldıktan sonra birkaç SQL örneği veya veritabanı oluşturuldu. Örneğin, son bir ayın performans geçmişi için bir değerlendirme oluşturuyorsanız ancak ortamda yalnızca bir hafta önce birkaç SQL örneği veya veritabanı oluşturuluyorsa. Bu durumda, yeni sunucular için performans verileri sürenin tamamına uygun olmayacaktır ve güvenirlik derecelendirmesi düşük olacaktır. [Daha fazla bilgi edinin](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="-the-number-of-azure-vm-or-avs-assessments-on-the-discovery-and-assessment-tool-are-incorrect"></a>Bulma ve değerlendirme aracında Azure VM veya AVS değerlendirmelerinin sayısı > hatalı
 Bunu düzeltmek için, Tüm değerlendirmelere gitmek ve Azure VM veya AVS değerlendirmesini yeniden hesaplamak için toplam değerlendirme sayısına tıklayın. Bulma ve değerlendirme aracı daha sonra bu değerlendirme türü için doğru sayıyı gösterir.


## <a name="i-want-to-try-out-the-new-azure-sql-assessment"></a>Yeni Azure SQL değerlendirmesi 'ni denemek istiyorum
VMware ortamınızda çalışan SQL Server örnekleri ve veritabanlarının keşfi ve değerlendirmesi artık önizlemededir. [Bu öğreticiyi](tutorial-discover-vmware.md)kullanmaya başlayın. Bu özelliği mevcut bir projede denemek istiyorsanız, lütfen bu makaledeki [önkoşulları](how-to-discover-sql-existing-project.md) tamamladığınızdan emin olun.

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-sql-assessment"></a>Azure SQL değerlendirmesi oluştururken bazı sunucuları göremiyorum

- Azure SQL değerlendirmesi yalnızca SQL örneklerinin bulunduğu, çalışan sunucularda yapılabilir. Değerlendirmek istediğiniz sunucuları ve SQL örneklerini göremiyorsanız bulma işleminin tamamlanması için bir süre bekleyin ve ardından değerlendirmeyi oluşturun. 
- Değerlendirme oluştururken daha önce oluşturulmuş bir grubu göremiyorsanız, lütfen VMware olmayan sunucu veya bir SQL örneği olmayan herhangi bir sunucuyu gruptan kaldırın.
- Azure Geçişi'nde Azure SQL değerlendirmelerini ilk kez çalıştırıyorsanız, yeni bir sunucu grubu oluşturmanız önerilir.

## <a name="i-want-to-understand-how-was-the-readiness-for-my-instance-computed"></a>Örnek için nasıl hazırlık yapıldığını anlamak istiyorum?
SQL örneklerinizin hazır olma durumu, hedeflenen Azure SQL dağıtım türü (Azure SQL Veritabanı veya Azure SQL Yönetilen Örneği) ile bir özellik uyumluluğu denetimi yapıldıktan sonra hesaplanmıştır. [Daha fazla bilgi edinin](./concepts-azure-sql-assessment-calculation.md#calculate-readiness)

## <a name="why-is-the-readiness-for-all-my-sql-instances-marked-as-unknown"></a>Neden tüm SQL örneklerim bilinmiyor olarak işaretlendi?
Bulma işlemi son zamanlarda başlatılmışsa ve hala devam ediyorsa, bazı veya tüm SQL örneklerinin hazır olduğunu bilinmiyor olarak görebilirsiniz. Aletin ortam profilini oluşturması için bir süre beklemeniz ve ardından değerlendirmeyi yeniden hesaplamanız önerilir.
SQL bulma her 24 saatte bir gerçekleştirilir ve en son yapılandırma değişikliklerinin yansıtması için bir güne kadar beklemeniz gerekebilir. 

## <a name="why-is-the-readiness-for-some-of-my-sql-instances-marked-as-unknown"></a>SQL örneklerinden bazıları bilinmiyor olarak işaretlenme neden bilinmiyor?
Bu durum şu durumlarda oluşabilir: 
- Bulma hala devam ediyor. Aletin ortam profilini oluşturması için bir süre beklemeniz ve ardından değerlendirmeyi yeniden hesaplamanız önerilir.
- Hatalar ve bildirimler dikey penceresinde düzeltmeniz gereken bazı bulma sorunları var.

SQL bulma her 24 saatte bir gerçekleştirilir ve en son yapılandırma değişikliklerinin yansıtması için bir güne kadar beklemeniz gerekebilir.

## <a name="my-assessment-is-in-outdated-state"></a>Değerlendirmem Süresi geçmiş durumda

### <a name="azure-vmavs-assessment"></a>Azure VM/AVS değerlendirmesi
Değerlendirilen bir gruptaki sunucularda şirket içi değişiklikler varsa, değerlendirme süresi geçmiş olarak işaretlenir. Aşağıdaki özelliklerde bir veya daha fazla değişiklik olduğundan, bir değerlendirme "güncel değil" olarak işaretlenebilir:
- İşlemci çekirdekleri sayısı
- Ayrılan bellek
- Önyükleme türü veya bellenim
- İşletim sistemi adı, sürümü ve mimarisi
- Disk sayısı
- Ağ bağdaştırıcısı sayısı
- Disk boyutu değişikliği (GB ayrılmış)
- NIC özellikleri güncelleştirmesi. Örnek: MAC adresi değişiklikleri, IP adresi ekleme vb.

Değerlendirmede yapılan en son değişiklikleri yansıtmak için lütfen değerlendirmeyi **yeniden hesaplayın** .

### <a name="azure-sql-assessment"></a>Azure SQL değerlendirmesi
Değerlendirilen gruptaki şirket içi SQL örneklerinde ve veritabanlarında değişiklikler yapıldıysa, değerlendirme **süresi geçmiş** olarak işaretlenir:
- Sunucuda SQL örneği eklendi veya kaldırıldı
- SQL örneğinde SQL veritabanı eklendi veya kaldırıldı
- SQL örneğindeki toplam veritabanı boyutu %20'den fazla değiştirildi
- İşlemci çekirdekleri ve/veya ayrılan bellek sayısı değişikliği

Değerlendirmede yapılan en son değişiklikleri yansıtmak için lütfen değerlendirmeyi **yeniden hesaplayın** .

## <a name="why-was-i-recommended-a-particular-target-deployment-type"></a>Bana neden belirli bir hedef dağıtım türü önerildi?
Azure Geçişi, SQL örneğinizle uyumlu olan belirli bir Azure SQL dağıtım türünü önerir. Microsoft tarafından önerilen hedefe geçiş yapılması genel olarak geçiş çalışmalarınızı azaltır. SQL örneğinizin ve yönettiği veritabanlarının performans özellikleri dikkate alındıktan sonra bu Azure SQL yapılandırması (SKU) önerildi. Birden çok Azure SQL yapılandırması uygunsa, maliyeti en uygun olanı öneririz. [Daha fazla bilgi edinin](./concepts-azure-sql-assessment-calculation.md#calculate-sizing)

## <a name="what-deployment-target-should-i-choose-if-my-sql-instance-is-ready-for-azure-sql-db-and-azure-sql-mi"></a>SQL örneğim Azure SQL Veritabanı ve Azure SQL Yönetilen Örneği için hazırsa hangi dağıtım hedefini seçmeliyim? 
Örneğiniz hem Azure SQL Veritabanı hem de Azure SQL Yönetilen Örneği için hazırsa, Azure SQL yapılandırmasının tahmini maliyetinin daha düşük olduğu dağıtım türünün hedeflenmesini öneririz.

## <a name="why-is-my-instance-marked-as-potentially-ready-for-azure-vm-in-my-azure-sql-assessment"></a>Örneğim Azure SQL değerlendirmem 'nda Azure VM için neden büyük olasılıkla kullanılamıyor olarak işaretlendi?
Değerlendirme özelliklerinde seçilen hedef dağıtım türü **Önerilen** olduğunda ve SQL örneği Azure SQL Veritabanı ve Azure SQL Yönetilen Örneği için hazır olmadığında bu durum oluşabilir. Kullanıcının, örneğin çalıştırıldığı Sunucunun Azure VM'ye geçmeye hazır olup olmadığını öğrenmek için Azure geçişinde değerlendirme türü **Azure VM** olan bir değerlendirme oluşturması önerilir.
Kullanıcının, örnek olarak çalışan sunucunun bir Azure VM 'ye geçirmeye hazırlanın olup olmadığını öğrenmek için Azure **VM** olarak değerlendirme türü Ile Azure geçişi ile bir değerlendirme oluşturulması önerilir:
- Azure geçişi 'ndeki Azure VM değerlendirmelerinde Şu anda yükseltme-bir-kaydırma uygulanır ve Azure sanal makinesinde SQL örnekleri ve veritabanlarını çalıştırmaya yönelik belirli performans ölçümlerini düşünmeyecektir. 
- Sunucuda Azure VM değerlendirmesi çalıştırdığınızda, önerilen boyut ve maliyet tahminleri sunucuda çalıştırılan ve Sunucu Geçişi aracı kullanılarak bir Azure sanal makinesine geçirilebilecek tüm örnekleri kapsar. Geçiş yapmadan önce Azure sanal makinelerindeki SQL Server için [performans yönergelerini gözden geçirin](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).

## <a name="i-cant-see-some-databases-in-my-assessment-even-though-the-instance-is-part-of-the-assessment"></a>Örnek değerlendirmenin bir parçası olsa da bazı veritabanlarını değerlendirmemde göremiyorum

Azure SQL değerlendirmesi yalnızca çevrimiçi durumdaki veritabanlarını içerir. Veritabanı başka bir durumdaysa, değerlendirme bu tür veritabanları için hazırlık, boyutlandırma ve maliyet hesaplamasını yoksayar. Bu tür veritabanlarını değerlendirmek istiyorsanız lütfen veritabanının durumunu değiştirin ve değerlendirmeyi bir süre sonra yeniden hesaplayın.

## <a name="i-want-to-compare-costs-for-running-my-sql-instances-on-azure-vm-vs-azure-sql-databaseazure-sql-managed-instance"></a>Azure VM Ile Azure SQL veritabanı/Azure SQL yönetilen örneği üzerinde SQL örneklerimi çalıştırmanın maliyetlerini karşılaştırmak istiyorum

**Azure SQL** değerlendirmesinde kullanılan aynı grup üzerinde türü **Azure VM** olan bir değerlendirme oluşturabilirsiniz. Ardından bu iki raporu yan yana koyup karşılaştırabilirsiniz. Öte yandan Azure Geçişi'nde Azure VM değerlendirmeleri şu anda lift-and-shift ile taşımaya odaklanmıştır ve Azure sanal makinesinde SQL örneklerini ve veritabanlarını çalıştırmaya yönelik belirli performans ölçümlerini dikkate almaz. Sunucuda Azure VM değerlendirmesi çalıştırdığınızda, önerilen boyut ve maliyet tahminleri sunucuda çalıştırılan ve Sunucu Geçişi aracı kullanılarak bir Azure sanal makinesine geçirilebilecek tüm örnekleri kapsar. Geçiş yapmadan önce Azure sanal makinelerindeki SQL Server için [performans yönergelerini gözden geçirin](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).

## <a name="the-storage-cost-in-my-azure-sql-assessment-is-zero"></a>Azure SQL değerlendirmem 'nda depolama maliyeti sıfır
Azure SQL yönetilen örneği için, ilk 32 GB/örnek/ay depolaması için bir depolama maliyeti eklenmez ve daha fazla depolama maliyeti, GB 'lik artışlarla depolama için eklenir. [Daha Fazla Bilgi](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Azure VMware çözümü (AVS) değerlendirmesi oluştururken bazı grupları göremiyorum

- AVS değerlendirmesi yalnızca VMware makineleri içeren gruplarda yapılabilir. AVS değerlendirmesi yapmayı amaçlıyorsanız VMware dışı tüm makineleri gruptan kaldırın.
- Azure Geçişi'nde AVS değerlendirmelerini ilk kez çalıştırıyorsanız, yeni bir VMware makineleri grubu oluşturmanız önerilir.

## <a name="i-cant-see-some-vm-types-and-sizes-in-azure-government"></a>Azure Kamu 'da bazı VM türlerini ve boyutlarını göremiyorum

Değerlendirme ve geçiş için desteklenen VM türleri ve boyutları, Azure Kamu konumunda kullanılabilirliğine bağlıdır. Azure Kamu 'da VM türlerini [gözden geçirebilir ve karşılaştırabilirsiniz](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) .

## <a name="the-size-of-my-server-changed-can-i-run-an-assessment-again"></a>Sunucu boyutu değişti. Bir değerlendirmeyi yeniden çalıştırabilir miyim?

Azure geçişi gereci, şirket içi ortam hakkındaki bilgileri sürekli olarak toplar.  Değerlendirme, şirket içi sunucuların bir zaman noktası anlık görüntüsüdür. Değerlendirmek istediğiniz bir sunucuda ayarları değiştirirseniz, en son değişikliklerle değerlendirmesi güncelleştirmek için yeniden hesapla seçeneğini kullanın.

## <a name="how-do-i-discover-servers-in-a-multitenant-environment"></a>Nasıl yaparım? çok kiracılı bir ortamda sunucu keşfet mi?

- **VMware**: bir ortam kiracılar arasında paylaşılmışsa ve başka bir kiracının aboneliğindeki bir kiracının sunucularını öğrenmek istemiyorsanız, yalnızca keşfetmesini istediğiniz sunuculara erişebilen VMware vCenter Server kimlik bilgileri oluşturun. Daha sonra, Azure geçiş gereci 'nda bulmaya başladığınızda bu kimlik bilgilerini kullanın.
- **Hyper-v**: bulma Işlemi, Hyper-v ana bilgisayar kimlik bilgilerini kullanır. Sunucular aynı Hyper-V konağını paylaşıyorsa, şu anda bulmayı ayırmanın bir yolu yoktur.  

## <a name="do-i-need-vcenter-server"></a>VCenter Server ihtiyacım var mı?

Evet, Azure geçişi, bulma işlemini gerçekleştirmek için bir VMware ortamında vCenter Server gerektirir. Azure geçişi, vCenter Server tarafından yönetilmeyen ESXi konaklarının bulunmasını desteklemez.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Azure VM değerlendirmesinde boyutlandırma seçenekleri nelerdir?

Azure geçişi, şirket içi olarak boyutlandırılması için sunucu performansı verilerini değerlendirme için kabul etmez. Azure değerlendirir VM boyutlarını şirket içi yapılandırmaya göre geçirin. Performans tabanlı boyutlandırma ile boyutlandırma, kullanım verilerini temel alır.

Örneğin, bir şirket içi sunucuda %50 CPU kullanımı ve %50 bellek kullanımı için dört çekirdek ve 8 GB bellek varsa:
- Şirket içi boyutlandırma, dört çekirdeğe ve 8 GB belleğe sahip bir Azure VM SKU 'SU önerir.
- Performans tabanlı boyutlandırma, kullanım yüzdesi kabul edildiği için iki çekirdeğe ve 4 GB belleğe sahip bir VM SKU 'SU önermenizi sağlayacak.

Benzer şekilde, disk boyutlandırma, boyutlandırma ölçütlerine ve depolama türüne bağlıdır:
- Boyutlandırma ölçütü performans tabanlıdır ve depolama türü otomatik ise, Azure geçişi, hedef disk türünü (Standart veya Premium) belirlediğinde, diskin ıOPS ve aktarım hızı değerlerini hesaba göre alır.
- Boyutlandırma ölçütü performans tabanlıdır ve depolama türü Premium ise, Azure geçişi, şirket içi diskin boyutuna bağlı olarak bir Premium disk SKU 'SU önerir. Aynı mantık, boyutlandırma şirket içinde olduğunda ve depolama türü standart veya Premium olduğunda disk boyutlandırılmasına uygulanır.

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>Performans geçmişi ve kullanımı bir Azure VM değerlendirmesinde boyutlandırmayı etkiler mi?

Evet, performans geçmişi ve kullanımı bir Azure VM değerlendirmesinde boyutlandırmayı etkiler.

### <a name="performance-history"></a>Performans geçmişi

Yalnızca performans tabanlı boyutlandırma için Azure geçişi, şirket içi makinelerin performans geçmişini toplar ve ardından Azure 'da VM boyutunu ve disk türünü önermek için kullanır:

1. Gereç, her 20 saniyede bir gerçek zamanlı kullanım verilerini toplamak için şirket içi ortamı sürekli olarak profillerdir.
2. Gereç, toplanan 20 saniyelik örnekleri kaydeder ve bunları 15 dakikada bir tek bir veri noktası oluşturmak için kullanır.
3. Veri noktasını oluşturmak için gereç tüm 20 saniyelik örneklerden tepe değeri seçer.
4. Gereç, veri noktasını Azure 'a gönderir.

### <a name="utilization"></a>Kullanım

Azure 'da bir değerlendirme oluşturduğunuzda, performans süresine ve ayarlanan performans geçmişi yüzdebirlik değerine bağlı olarak, Azure geçişi etkin kullanım değerini hesaplar ve ardından boyutlandırmak için kullanır.

Örneğin, performans süresini bir güne ve yüzdebirlik değerini 95. yüzdebirlik olarak ayarlarsanız, Azure geçişi, son gün için toplayıcı tarafından gönderilen 15 dakikalık örnek noktalarını artan düzende sıralar. Etkin kullanım olarak 95. yüzdebirlik değerini seçer.

95. yüzdebirlik değerinin kullanılması, aykırı değerlerin yoksayılmasını sağlar. Azure geçirilmesi 99. yüzdebirlik ' i kullanıyorsa, dış değerler dahil edilebilir. Tüm aykırı değerleri içermeyen dönemin en yoğun kullanımını seçmek için Azure geçişi ' ni 99. yüzdebirlik 'i kullanacak şekilde ayarlayın.


## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>İçeri aktarma tabanlı değerlendirmeler, bulgu kaynağı olan değerlendirmelere gereç olarak nasıl farklıdır?

İçeri aktarma tabanlı Azure VM değerlendirmeleri, bir CSV dosyası kullanarak Azure geçişi 'ne aktarılan makinelerle oluşturulan değerlendirmelerdir. İçeri aktarmanın yalnızca dört alanı zorunludur: sunucu adı, çekirdekler, bellek ve işletim sistemi. Dikkat edilmesi gereken bazı noktalar şunlardır: 
 - Hazırlık ölçütleri, önyükleme türü parametresindeki içeri aktarma tabanlı değerlendirmelere daha az sıkıdır. Önyükleme türü sağlanmazsa, makinenin BIOS önyükleme türü olduğu ve makinenin **koşullu** olarak işaretli olmadığı varsayılır. Bulgu kaynağı bir gereç olarak bulunan değerlendirmelerinde, önyükleme türü eksikse hazır olma durumu **koşullu** olarak işaretlenir. Hazırlık hesaplamasında bu fark, kullanıcıların içeri aktarma tabanlı değerlendirmeler tamamlandığında geçiş planlamasının erken aşamalarında makinelerde tüm bilgilere sahip olmaması olabilir. 
 - Performans tabanlı içeri aktarma değerlendirmeleri, doğru boyutlandırma hesaplamaları için Kullanıcı tarafından sunulan kullanım değerini kullanır. Kullanım değeri Kullanıcı tarafından sağlandığı için, değerlendirme özelliklerinde **performans geçmişi** ve **yüzdebirlik kullanım** seçenekleri devre dışıdır. Bulgu kaynağı bir gereç olarak bulunan değerlendirmelerinde, seçilen yüzdebirlik değeri gereç tarafından toplanan performans verilerinden çekilir.

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>İçeri aktarma tabanlı AVS değerlendirmesi bilinmeyen olarak işaretlenmiş olan geçiş aracı neden bilinmiyor?

Bir CSV dosyası aracılığıyla içeri aktarılan makineler için, bir AVS değerlendirmesinde varsayılan geçiş aracı bilinmez. Ancak, VMware makinelerinde, VMware karma bulut uzantısı (HCX) çözümünün kullanılması önerilir. [Daha Fazla Bilgi Edinin](../azure-vmware/tutorial-deploy-vmware-hcx.md).


## <a name="what-is-dependency-visualization"></a>Bağımlılık görselleştirmesi nedir?

Bağımlılık görselleştirmesi, daha fazla güvenle geçirilecek sunucu gruplarını değerlendirmenize yardımcı olabilir. Bağımlılık görselleştirmesi, değerlendirme çalıştırmadan önce makine bağımlılıklarını çapraz denetler. Bu, herhangi bir şeyin gerisinde kalmasını sağlamaya yardımcı olur ve Azure 'a geçiş yaparken beklenmedik kesintilerden kaçınmaya yardımcı olur. Azure geçişi, bağımlılık görselleştirmesini etkinleştirmek için Azure Izleyici 'de Hizmet Eşlemesi çözümünü kullanır. [Daha fazla bilgi edinin](concepts-dependency-visualization.md).

> [!NOTE]
> Aracı tabanlı bağımlılık analizi, Azure Kamu 'da kullanılamaz. Aracısız bağımlılık analizini kullanabilirsiniz

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Aracı tabanlı ve aracısız arasındaki fark nedir?

Aracısız görselleştirme ve aracı tabanlı görselleştirme arasındaki farklılıklar tabloda özetlenmiştir.

**Gereksinim** | **Aracısız** | **Aracı tabanlı**
--- | --- | ---
Destek | Bu seçenek şu anda önizleme aşamasındadır ve yalnızca VMware ortamındaki sunucular için kullanılabilir. Desteklenen işletim sistemlerini [gözden geçirin](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) . | Genel kullanılabilirlik (GA).
Aracı | Çapraz denetlemek istediğiniz makinelere aracı yüklemeye gerek yoktur. | Çözümlemek istediğiniz her şirket içi makineye yüklenecek aracılar: [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)ve [bağımlılık Aracısı](../azure-monitor/agents/agents-overview.md#dependency-agent). 
Önkoşullar | Önkoşulları ve dağıtım gereksinimlerini [gözden geçirin](concepts-dependency-visualization.md#agentless-analysis) . | Önkoşulları ve dağıtım gereksinimlerini [gözden geçirin](concepts-dependency-visualization.md#agent-based-analysis) .
Log Analytics | Gerekli değildir. | Azure geçişi, bağımlılık görselleştirmesi için [Azure izleyici günlüklerinde](../azure-monitor/logs/log-query-overview.md) [hizmet eşlemesi](../azure-monitor/vm/service-map.md) çözümünü kullanır. [Daha fazla bilgi edinin](concepts-dependency-visualization.md#agent-based-analysis).
Nasıl çalışır? | Bağımlılık görselleştirmesi için etkinleştirilen makinelerde TCP bağlantı verilerini yakalar. Bulmadan sonra, verileri beş dakikalık aralıklarla toplar. | Bir makineye yüklü Hizmet Eşlemesi aracılar, her bir işlem için TCP işlemleri ve gelen/giden bağlantılarla ilgili verileri toplar.
Veriler | Kaynak makine sunucu adı, işlem, uygulama adı.<br/><br/> Hedef makine sunucu adı, işlem, uygulama adı ve bağlantı noktası. | Kaynak makine sunucu adı, işlem, uygulama adı.<br/><br/> Hedef makine sunucu adı, işlem, uygulama adı ve bağlantı noktası.<br/><br/> Bağlantı sayısı, gecikme süresi ve veri aktarımı bilgilerinin toplanması ve Log Analytics sorguları için kullanılabilir olması. 
Görselleştirme | Tek bir sunucunun bağımlılık eşlemesi, bir saat ile 30 güne kadar bir süre içinde görüntülenebilir. | Tek bir sunucunun bağımlılık eşlemesi.<br/><br/> Eşleme, yalnızca bir saat boyunca görüntülenebilir.<br/><br/> Bir sunucu grubunun bağımlılık eşlemesi.<br/><br/> Harita görünümünden bir gruptaki sunucuları ekleyin ve kaldırın.
Veri dışarı aktarma | Son 30 günlük veri, CSV biçiminde indirilebilir. | Veriler, Log Analytics ile sorgulanabilir.


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>Aracısız bağımlılık analizine yönelik gereci dağıtmem gerekir mi?

Evet, [Azure geçişi](migrate-appliance.md) gerecinin dağıtılması gerekir.

## <a name="do-i-pay-for-dependency-visualization"></a>Bağımlılık görselleştirmesi için ücret ödemem gerekiyor mu?

Hayır. [Azure geçişi fiyatlandırması](https://azure.microsoft.com/pricing/details/azure-migrate/)hakkında daha fazla bilgi edinin.

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Aracı tabanlı bağımlılık görselleştirmesi için ne yükleyebilirim?

Aracı tabanlı bağımlılık görselleştirmesini kullanmak için, değerlendirmek istediğiniz her şirket içi makineye aracıları indirip yükleyin:

- [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)
- [Bağımlılık aracısı](../azure-monitor/agents/agents-overview.md#dependency-agent)
- İnternet bağlantısı olmayan makineleriniz varsa Log Analytics ağ geçidini indirip yükleyin.

Bu aracılara yalnızca aracı tabanlı bağımlılık görselleştirmesi kullanıyorsanız ihtiyacınız vardır.

## <a name="can-i-use-an-existing-workspace"></a>Mevcut bir çalışma alanını kullanabilir miyim?

Evet, aracı tabanlı bağımlılık görselleştirmesi için, var olan bir çalışma alanını geçiş projesine iliştirebilir ve bunu bağımlılık görselleştirmesi için kullanabilirsiniz. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Bağımlılık görselleştirme raporunu dışarı aktarabilir miyim?

Hayır, aracı tabanlı görselleştirmede bağımlılık görselleştirme raporu verilemez. Bununla birlikte, Azure geçişi Hizmet Eşlemesi kullanır ve bağımlılıkları JSON biçiminde almak için [Hizmet Eşlemesi REST API](/rest/api/servicemap/machines/listconnections) de kullanabilirsiniz.

## <a name="can-i-automate-agent-installation"></a>Aracı yüklemesini otomatikleştirebilirim miyim?

Aracı tabanlı bağımlılık görselleştirmesi için:

- [Bağımlılık aracısını yüklemek için bir komut dosyası](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent)kullanın.
- MMA için [komut satırını veya Otomasyonu kullanın](../azure-monitor/agents/log-analytics-agent.md#installation-options)veya bir [komut dosyası](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)kullanın.
- Betiklerin yanı sıra, aracıları dağıtmak için Microsoft uç nokta Configuration Manager ve [ıntigua](https://www.intigua.com/intigua-for-azure-migration) gibi dağıtım araçlarını kullanabilirsiniz.

## <a name="what-operating-systems-does-mma-support"></a>MMA hangi işletim sistemlerini destekler?

- [MMA 'nın desteklediği Windows işletim sistemlerinin](../azure-monitor/agents/log-analytics-agent.md#installation-options)listesini görüntüleyin.
- [MMA 'nın desteklediği Linux işletim sistemlerinin](../azure-monitor/agents/log-analytics-agent.md#installation-options)listesini görüntüleyin.

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Bağımlılıkları birden fazla saat için görselleştirebilir miyim?

Aracı tabanlı görselleştirme için, bağımlılıkları bir saate kadar görselleştirebilirsiniz. Geçmişte belirli bir tarihe kadar bir ay kadar geri dönebilirsiniz, ancak görselleştirme için en uzun süre bir saat olabilir. Örneğin, dün bağımlılıklarını görüntülemek için bağımlılık eşlemesindeki süre süresini kullanabilirsiniz, ancak yalnızca bir saatlik pencerenin bağımlılıklarını görüntüleyebilirsiniz. Ancak, daha uzun bir süre boyunca [bağımlılık verilerini sorgulamak](./how-to-create-group-machine-dependencies.md) Için Azure izleyici günlüklerini kullanabilirsiniz.

Aracısız görselleştirme için, tek bir sunucunun bağımlılık haritasını bir saat ile 30 gün arasında bir süre izleyebilirsiniz.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-servers"></a>10 ' dan fazla sunucu grubu için bağımlılıkları görselleştirebilir miyim?

10 adede kadar sunucusuna sahip gruplar için [bağımlılıkları görselleştirebilirsiniz](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) . 10 ' dan fazla sunucusu olan bir grubunuz varsa, grubu daha küçük gruplara bölmeniz ve sonra bağımlılıkları görselleştirmenizi öneririz.

## <a name="next-steps"></a>Sonraki adımlar

[Azure geçişi 'ne genel bakış](migrate-services-overview.md)konusunu okuyun.