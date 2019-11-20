---
title: Azure geçişi ile ilgili sık sorulan sorular
description: Azure geçişi ile ilgili yaygın ve sık sorulan soruları ele alınmaktadır
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: snehaa
ms.openlocfilehash: 1663e1d418610bcfa2ada28770944bfc8cbf908f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73480076"
---
# <a name="azure-migrate-common-questions"></a>Azure geçişi: sık sorulan sorular

Bu makalede, Azure geçişi hakkında sık sorulan sorular yanıtlanmaktadır. Bu makaleyi okuduktan sonra daha fazla sorgunuz varsa, bunları [Azure geçişi forumuna](https://aka.ms/AzureMigrateForum)gönderin.

## <a name="general"></a>Genel

### <a name="which-azure-geographies-are-supported"></a>Hangi Azure coğrafi lıkları desteklenir?

[VMware VM](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) ve [Hyper-V VM 'Leri](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)için Azure geçişi 'nin desteklenen coğrafi graflarını gözden geçirin.

### <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Azure geçişi ile Site Recovery arasındaki fark nedir?

Azure geçişi, şirket içi makinelerin ve iş yüklerinin Azure 'da bulunmasını, değerlendirmesini ve geçişini yönetmek ve izlemek için merkezi bir merkez sağlar. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) bir olağanüstü durum kurtarma çözümüdür. Azure geçişi: sunucu geçiş aracı, bazı şirket içi makinelerin geçiş ve geçiş geçişi için bazı arka uç Site Recovery işlevleri kullanır.

### <a name="how-do-i-delete-an-azure-migrate-project"></a>Nasıl yaparım? bir Azure geçişi projesi silinsin mi?

Bir projeyi silmek için [Bu yönergeleri](how-to-delete-project.md) izleyin. Azure geçişi projesindeki makineleri ve iş yüklerini keşfettiğiniz, değerlendirmekte ve geçirirken oluşturulan [kaynakları gözden geçirin](how-to-delete-project.md#created-resources) .


## <a name="azure-migrate-appliance"></a>Azure Geçişi gereci

### <a name="how-does-the-appliance-connect-to-azure"></a>Gereç Azure 'a nasıl bağlanır?

Bağlantı Internet üzerinden olabilir veya Azure ExpressRoute 'u ortak/Microsoft eşlemesi ile kullanabilirsiniz.

### <a name="what-network-connectivity-is-needed-for-azure-migrate-server-assessment-and-migration"></a>Azure geçişi sunucu değerlendirmesi ve geçişi için hangi ağ bağlantısı gerekir?

Azure geçişi için gereken URL 'Ler ve bağlantı noktaları hakkında bilgi edinmek için [VMware](migrate-support-matrix-vmware.md) ve [Hyper-V](migrate-support-matrix-hyper-v.md) destek matrislerini gözden geçirin.

### <a name="can-i-harden-the-appliance-vm-created-with-the-template"></a>Şablonla oluşturulan gereç sanal makinesini kullanabilir miyim?

Azure geçişi gereci için gereken iletişim ve güvenlik duvarı kurallarını olduğu sürece şablona (örneğin, virüsten koruma) bileşen ekleyebilirsiniz.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Azure geçişi gereci hangi verileri toplamıştır?

Gereç tarafından toplanan verileri aşağıdaki gibi gözden geçirin:
- VMware VM 'Leri için [performans verileri](migrate-appliance.md#collected-performance-data-vmware) ve [meta veriler](migrate-appliance.md#collected-metadata-vmware) .
- Hyper-V VM 'Leri için [performans verileri](migrate-appliance.md#collected-performance-data-hyper-v) ve [meta veriler](migrate-appliance.md#collected-metadata-hyper-v) .


### <a name="does-appliance-analysis-impact-performance"></a>Gereç Analizi etkisi performansı mı?

Azure, sanal makine performans verilerini ölçmek için şirket içi makineleri şirket içi makinelere sürekli olarak geçiş yaptı. Bu profil oluşturma, Hyper-V/ESXi Konakları veya vCenter Server üzerinde neredeyse hiçbir performans etkisi yoktur.

### <a name="where-and-how-long-is-collected-data-stored"></a>Toplanan veriler nerede ve ne kadar depolanıyor?

Azure geçişi gereci tarafından toplanan veriler, geçiş projesini oluştururken seçtiğiniz Azure konumunda depolanır. Veriler bir Microsoft aboneliğine güvenli bir şekilde depolanır ve Azure geçişi projesini sildiğinizde silinir.

Bağımlılık görselleştirmesi için toplanan veriler, Azure aboneliğinde oluşturulan bir Log Analytics çalışma alanında Birleşik Devletler depolanır. Bu veriler, aboneliğinizdeki Log Analytics çalışma alanını sildiğinizde silinir. Bağımlılık görselleştirmesi hakkında [daha fazla bilgi edinin](concepts-dependency-visualization.md) .

### <a name="what-volume-of-data-is-uploaded-during-continuous-profiling"></a>Sürekli profil oluşturma sırasında hangi veri hacmi karşıya yüklendi?

Azure geçişi 'ne gönderilen verilerin hacmi, birkaç parametreye bağlı olarak değişir. Birim hakkında fikir sahibi olmak için, 10 makine içeren bir Azure geçişi projesi (her biri bir disk ve bir NIC ile) günde 50 MB 'a gönderilir. Bu değer yaklaşık, NIC ve disklerin veri noktası sayısına göre değişir. Makine, NIC 'ler veya disk sayısı arttıkça, gönderilen verilerdeki artış doğrusal değildir.

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Veriler Rest 'de ve aktarımda mi şifrelendi?

Evet, her ikisi.
- Meta veriler, HTTPS üzerinden Internet üzerinden Azure geçişi hizmetine güvenli bir şekilde gönderilir.
- Meta veriler bir [Azure Cosmos veritabanı](../cosmos-db/database-encryption-at-rest.md) veritabanında ve [Azure Blob depolama](../storage/common/storage-service-encryption.md) alanında bir Microsoft aboneliğine depolanır. Meta veriler Rest 'de şifrelenir.
- Bağımlılık analizine yönelik veriler de aktarım sırasında şifrelenir (güvenli HTTPS). Bu, aboneliğinizdeki bir Log Analytics çalışma alanında depolanır. Ayrıca, Rest 'ten de şifrelenir.

### <a name="how-does-the-appliance-communicate-with-vcenter-server-and-azure-migrate"></a>Gereç vCenter Server ve Azure geçişi ile nasıl iletişim kurar?

1. Gereç, gereci ayarlarken verdiğiniz kimlik bilgilerini kullanarak vCenter Server (bağlantı noktası 443) öğesine bağlanır.
2. Gereç, vCenter Server tarafından yönetilen VM 'Lerle ilgili meta verileri toplamak için vCenter Server sorgulamak üzere VMware PowerCLI kullanır. VM 'Ler (çekirdek, bellek, diskler, NIC 'Ler) ve geçen aya ait her bir VM 'nin performans geçmişi hakkında yapılandırma verileri toplar.
3. Toplanan meta veriler daha sonra değerlendirme için Azure geçişi: Sunucu değerlendirmesi (HTTPS üzerinden Internet üzerinden) gönderilir.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>Aynı gereci birden çok vCenter Server örneğine bağlanabilir miyim?

Hayır. Bir gereç ve vCenter Server arasında bire bir eşleme vardır. Birden çok vCenter Server örneğinde VM 'Leri saptamak için birden çok gereç dağıtmanız gerekir.


### <a name="machine-size-changed-can-i-run-the-assessment-again"></a>Makine boyutu değişti. Değerlendirmeyi yeniden çalıştırabilir miyim?

Azure geçişi gereci, şirket içi ortam hakkındaki bilgileri sürekli olarak toplar. Ancak değerlendirme, şirket içi VM 'lerin bir zaman noktası anlık görüntüsüdür. Değerlendirmek istediğiniz bir VM 'deki ayarları değiştirirseniz, en son değişikliklerle değerlendirmesi güncelleştirmek için yeniden hesapla seçeneğini kullanın.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment"></a>Çok kiracılı bir ortamda bulma işlemini nasıl yapabilirim?

- VMware için, ortamınız kiracılar arasında paylaşılmışsa ve başka bir kiracının aboneliğindeki bir kiracının VM 'lerini öğrenmek istemiyorsanız, yalnızca keşfetmesini istediğiniz VM 'Lere erişebilen vCenter Server kimlik bilgileri oluşturun. Daha sonra, Azure geçiş gereci 'nda bulmaya başladığınızda bu kimlik bilgilerini kullanın.
- Hyper-V için bulma, Hyper-V ana bilgisayar kimlik bilgilerini kullanır. VM 'Ler aynı Hyper-V konağını paylaşıyorsa, şu anda bulmayı ayırmanın bir yolu yoktur.  

### <a name="how-many-vms-can-i-discover-with-a-single-appliance"></a>Tek bir gereç ile kaç VM keşfedebilirim?

En fazla 10.000 VMware VM ve tek bir geçiş gereci olan en fazla 5.000 Hyper-V VM 'Leri bulabilirsiniz. Şirket içi ortamınızda daha fazla makineniz varsa, [Hyper-V](scale-hyper-v-assessment.md) ve [VMware](scale-vmware-assessment.md) değerlendirmesini nasıl ölçeklentireceğinizi öğrenin.

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>Azure geçişi gerecini projeden silebilir miyim?

Şu anda projeden gereç silme desteklenmiyor.

- Gereci silmenin tek yolu, Gereç ile ilişkili Azure geçişi projesini içeren kaynak grubunu silmektir.
- Ancak, kaynak grubu silindiğinde diğer kayıtlı gereçler, bulunan envanter, değerlendirmeler ve kaynak grubundaki projeyle ilişkili diğer tüm Azure bileşenleri de silinir.

## <a name="azure-migrate-server-assessment"></a>Azure geçişi sunucu değerlendirmesi


### <a name="does-azure-migrate-need-vcenter-server-for-vmware-vm-discovery"></a>Azure geçişi, VMWare VM keşfi için vCenter Server gerekiyor mu?

Evet, Azure geçişi 'nin bir VMware ortamında bulmayı gerçekleştirmesi vCenter Server gerekir. VCenter Server tarafından yönetilmeyen ESXi konaklarının bulunmasını desteklemez.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure geçişi sunucu değerlendirmesi ve harıta araç seti arasındaki fark nedir?

Sunucu değerlendirmesi, geçiş hazırlığı ve Azure 'a geçiş için iş yüklerinin değerlendirmesi hakkında değerlendirme sağlar. [Microsoft Assessment and planlama (Map) araç seti](https://www.microsoft.com/download/details.aspx?id=7826) , Windows istemci/sunucu işletim sistemlerinin daha yeni sürümleri için geçiş planlaması ve yazılım kullanımı izleme dahil diğer görevlerle yardımcı olur. Bu senaryolar için harıta araç setini kullanmaya devam edin.

### <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Sunucu değerlendirmesi ve Site Recovery Dağıtım Planlayıcısı arasındaki fark nedir?

Sunucu değerlendirmesi bir geçiş planlama aracıdır. Site Recovery Dağıtım Planlayıcısı bir olağanüstü durum kurtarma planlama aracıdır. 

- **Azure 'da şirket içi geçiş planlama**: Şirket Içi sunucularınızı Azure 'a geçirmeyi planlıyorsanız, geçiş planlaması Için Sunucu değerlendirmesi kullanın. Şirket içi iş yüklerini değerlendirir ve geçiş yapmanıza yardımcı olacak yönergeler ve araçlar sağlar. Geçiş planı kurulduktan sonra, makineleri Azure 'a geçirmek için Azure geçiş sunucusu geçişi de dahil olmak üzere araçları kullanabilirsiniz.
- **Azure 'da olağanüstü durum kurtarmayı planlayın**: Şirket içinden Azure 'a olağanüstü durum kurtarma ayarlamayı Site Recovery için Site Recovery dağıtım planlayıcısı kullanın. Dağıtım Planlayıcısı, olağanüstü durum kurtarma amacıyla Şirket içi ortamınızın derinlemesine ve Site Recovery özgü bir değerlendirmesini sağlar. Çoğaltma ve yük devretme gibi olağanüstü durum kurtarma konusunda öneriler sağlar.

### <a name="does-azure-migrate-estimate-costs-for-the-enterprise-agreement-ea-program"></a>Azure, Kurumsal Anlaşma (EA) programı için tahmini maliyetleri mi geçirsin?

Azure geçişi sunucu değerlendirmesi Şu anda [Kurumsal Anlaşma program](https://azure.microsoft.com/offers/enterprise-agreement-support/)için maliyet tahminleri desteklememektedir. Geçici bir çözüm olarak, bir değerlendirme oluşturduğunuzda, **teklif**olarak **Kullandıkça Öde** ' yi belirtebilir ve indirim yüzdesini (aboneliğe uygulanabilir) değerlendirme özelliklerindeki **indirimle** el ile ekleyebilirsiniz:

  ![Değerlendirme özellikleri](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-and-performance-based-sizing"></a>Şirket içi ve performans tabanlı boyutlandırma arasındaki fark nedir?

Şirket içi olarak boyutlandırılması sayesinde Azure geçişi, değerlendirme için VM performans verilerini kabul etmez. Şirket içi yapılandırmaya bağlı olarak VM boyutlarını değerlendirir. Performans tabanlı boyutlandırma ile boyutlandırma, kullanım verilerini temel alır.

- Örneğin, bir şirket içi VM 'nin %50 CPU kullanımı ve %50 bellek kullanımında 4 çekirdeğe ve 8 GB belleğe sahip olması halinde aşağıdakiler olur:
    - Şirket içi boyutlandırma, dört çekirdeğe ve 8 GB belleğe sahip bir Azure VM SKU 'SU önerir.
    - Kullanım yüzdesi göz önünde bulundurulduğundan, performans tabanlı boyutlandırmanın iki çekirdeği ve 4 GB belleğe sahip bir VM SKU 'SU önerilir.

- Benzer şekilde, disk boyutlandırma iki değerlendirme özelliklerine bağlıdır: boyutlandırma ölçütleri ve depolama türü.
    - Boyutlandırma ölçütü performans tabanlıdır ve depolama türü otomatik ise, Azure geçişi, hedef disk türünü (Standart veya Premium) belirlediğinde, diskin ıOPS ve aktarım hızı değerlerini hesaba göre alır.
    - Boyutlandırma ölçütü performans tabanlıdır ve depolama türü Premium ise, Azure geçişi, şirket içi diskin boyutuna bağlı olarak bir Premium disk SKU 'SU önerir. Aynı mantık disk boyutlandırılmasına, boyutlandırma şirket içi olarak olduğunda ve depolama türü standart veya Premium olduğunda uygulanır.

### <a name="does-performance-history-and-utilization-percentile-impact-size-recommendations"></a>Performans geçmişi ve kullanım yüzdebirlik boyut önerilerini etkiler mi?

Bu özellikler yalnızca performans tabanlı boyutlandırma için geçerlidir.

- Azure geçişi, şirket içi makinelerin performans geçmişini toplar ve Azure 'da VM boyutunu ve disk türünü önermek için kullanır.
- Gereç, her 20 saniyede bir gerçek zamanlı kullanım verileri toplamak için şirket içi ortamı sürekli olarak profillerdir.
- Gereç, 20 saniyelik örnekleri kaydeder ve 15 dakikada bir tek bir veri noktası oluşturur.
- Veri noktasını oluşturmak için gereç tüm 20 saniyelik örneklerden tepe değeri seçer.
- Gereç bu veri noktasını Azure 'a gönderir.

Azure 'da performans süresi ve performans geçmişi yüzdelik değeri temelinde bir değerlendirme oluşturduğunuzda, Azure geçişi etkin kullanım değerini hesaplar ve boyutlandırmak için kullanır.

- Örneğin, performans süresini bir güne ve yüzdebirlik değerini 95 yüzdebirlik olarak ayarlarsanız Azure geçişi, son gün için toplayıcı tarafından gönderilen 15 dakikalık örnek noktalarını artan düzende sıralar ve 95. yüzdebirlik değerini etkin olarak seçer kullanımı.
- 95. yüzdebirlik değerinin kullanılması, aykırı değerlerin yoksayılmasını sağlar. 99. yüzdebirlik 'i kullanırsanız, dış değerler dahil edilebilir. Herhangi bir aykırı değer olmadan dönem için en yoğun kullanımı seçmek istiyorsanız, 99. yüzdebirlik ' u seçin.

## <a name="server-assessment---dependency-visualization"></a>Sunucu değerlendirmesi-bağımlılık görselleştirme


### <a name="what-is-dependency-visualization"></a>Bağımlılık görselleştirmesi nedir?

Daha fazla güvenle geçiş için VM gruplarını değerlendirmek üzere bağımlılık görselleştirmesini kullanın. Bağımlılık görselleştirmesi, değerlendirme çalıştırmadan önce makine bağımlılıklarını çapraz denetler. Bu, herhangi bir şeyin gerisinde olmamasını sağlamaya yardımcı olur ve bu nedenle Azure 'a geçiş yaparken beklenmedik kesintilerden kaçınmaya yardımcı olur. Azure geçişi, bağımlılık görselleştirmesini etkinleştirmek için Azure Izleyici 'de Hizmet Eşlemesi çözümünü kullanır. [daha fazla bilgi edinin](concepts-dependency-visualization.md).

> [!NOTE]
> Bağımlılık görselleştirmesi Azure Kamu 'da kullanılamaz.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>Bağımlılık görselleştirmesini kullanmak için ödeme yapmam gerekir mi?
Hayır. Azure Geçişi fiyatlandırması hakkında [daha fazla bilgi edinin](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Bağımlılık görselleştirmesi için bir şey yüklemem gerekiyor mu?

Bağımlılık görselleştirmesini kullanmak için, değerlendirmek istediğiniz her şirket içi makineye aracıları indirip yüklemeniz gerekir.

Her makineye aşağıdaki aracıları yüklemeniz gerekir:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Bağımlılık Aracısı](../azure-monitor/platform/agents-overview.md#dependency-agent).
- İnternet bağlantısı olmayan makineleriniz varsa Log Analytics ağ geçidini indirip yüklemeniz gerekir.

Bağımlılık görselleştirmesi kullanmadığınız takdirde bu aracılara ihtiyacınız yoktur.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Bağımlılık görselleştirmesi için mevcut bir çalışma alanını kullanabilir miyim?

Evet, var olan bir çalışma alanını geçiş projesine iliştirebilir ve bunu bağımlılık görselleştirmesi için kullanabilirsiniz. [Daha fazla bilgi edinin](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Bağımlılık görselleştirme raporunu dışarı aktarabilir miyim?

Hayır, bağımlılık görselleştirmesi verilemiyor. Bununla birlikte, Azure geçişi Hizmet Eşlemesi kullanır ve bağımlılıkları JSON biçiminde almak için [Hizmet Eşlemesi REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) de kullanabilirsiniz.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Microsoft Monitoring Agent (MMA) ve bağımlılık aracısının yüklenmesini nasıl otomatikleştirebilirim?

[Bağımlılık aracısını yüklemek için bu betiği](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)kullanın. Komut satırını veya Otomasyonu kullanarak [MMA 'yı yüklemek için bu yönergeleri](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) izleyin. MMA için [bu betiği](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)kullanın.

Betiklerin yanı sıra, aracıları dağıtmak için System Center Configuration Manager ve [ıntigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) gibi dağıtım araçlarını da kullanabilirsiniz.


### <a name="what-operating-systems-are-supported-by-mma"></a>MMA tarafından desteklenen işletim sistemleri nelerdir?

- [MMA tarafından desteklenen Windows işletim sistemlerinin](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)listesini görüntüleyin.
- [MMA tarafından desteklenen Linux işletim sistemlerinin](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)listesini görüntüleyin.

### <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Bir saatten uzun bir süre boyunca bağımlılıkları görselleştirebilir miyim?
Hayır. Bağımlılıkları bir saate kadar görselleştirebilirsiniz. Geçmiş olarak belirli bir tarihe kadar geri dönerek, bir ay olarak geri dönebilirsiniz, ancak görselleştirme için en uzun süre bir saat olabilir. Örneğin, dün bağımlılıklarını görüntülemek için bağımlılık eşlemesindeki süre süresini kullanabilirsiniz, ancak yalnızca bir saatlik pencerenin bağımlılıklarını görüntüleyebilirsiniz. Ancak, Azure Izleyici günlüklerini daha uzun bir süre boyunca [bağımlılık verilerini sorgulamak](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) için kullanabilirsiniz.

### <a name="can-i-use-dependency-visualization-for-groups-of-more-than-10-vms"></a>10 ' dan fazla VM grubu için bağımlılık görselleştirmesini kullanabilir miyim?
10 adede kadar VM içeren gruplar için [bağımlılıkları görselleştirebilirsiniz](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) . 10 ' dan fazla VM 'ye sahip bir grubunuz varsa, grubu daha küçük gruplara bölmeniz ve sonra bağımlılıkları görselleştirmenizi öneririz.

## <a name="azure-migrate-server-migration"></a>Azure geçiş sunucusu geçişi

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-site-recovery"></a>Azure geçişi sunucu geçişi ve Site Recovery arasındaki fark nedir?

- Şirket içi VMware VM 'lerinin aracısız geçişi, Azure geçişi sunucu geçişi içinde yerel bir yerdir.
- Hyper-V VM 'lerinin, fiziksel sunucuların ve aracı tabanlı VMware VM 'lerinin geçirilmesi için Azure geçişi sunucu geçişi Azure Site Recovery çoğaltma altyapısını kullanır.


## <a name="next-steps"></a>Sonraki adımlar
[Azure geçişi 'ne genel bakış](migrate-services-overview.md)konusunu okuyun.
