---
title: Azure 'da BareMetal Altyapısı nedir?
description: Azure 'da BareMetal altyapısına genel bakış sağlar.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: c0fd250a63ce93d3f8b62dfe76fe753c928801ce
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536889"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Azure 'da BareMetal Altyapısı nedir?

Microsoft Azure, işletmenizin ihtiyaçlarını karşılamak için çok çeşitli tümleşik bulut hizmetleriyle bir bulut altyapısı sunmaktadır. Ancak bazı durumlarda, sanallaştırma katmanı olmadan çıplak sunucularda hizmetleri çalıştırmanız gerekebilir. Kök erişiminizin olması ve işletim sistemi (OS) üzerinde denetiminiz olması gerekebilir. Bu tür bir gereksinimi karşılamak için Azure, çeşitli yüksek değerli ve görev açısından kritik uygulamalar için BareMetal altyapı sunar.

BareMetal altyapısı adanmış BareMetal örnekleri (işlem örnekleri), yüksek performanslı ve uygulamaya uygun depolama (NFS, ISCSı ve Fiber Kanal) ve yalıtılmış bir ortamda işleve özgü sanal LAN 'lar (VLAN 'Lar) kümesi haline getirilir. Depolama, genişleme kümeleri gibi özellikleri etkinleştirmek veya STONITH ile yüksek kullanılabilirlik çiftleri oluşturmak için BareMetal örnekleri arasında paylaşılabilir.
 
Bu ortamın Ayrıca Azure aboneliğinizde bir veya daha fazla Azure sanal ağı (VNet) üzerinde sanal makineler (VM) çalıştırıyorsanız erişebileceğiniz özel VLAN 'Lar de vardır. Tüm ortam, Azure aboneliğinizde bir kaynak grubu olarak temsil edilir.

BareMetal altyapısı, 2 soketli ila 24 soketli sunucu ve 1,5 TB ila 24 adet 1 arasında değişen bellek üzerinden 30 ' dan fazla SKU 'ya kadar 30 adede kadar SKU 'da sunulur Octane bellek ile büyük bir SKU kümesi de mevcuttur. Azure, bir hiper ölçekli bulutta en büyük çıplak örnek aralığını sunar.

## <a name="why-baremetal-infrastructure"></a>Neden BareMetal altyapısı?  

Kuruluştaki bazı merkezi iş yükleri, tipik bir sanallaştırılmış bulut ayarında çalışmak üzere tasarlanmamış teknolojilerden oluşur. Özel mimari, sertifikalı donanım veya normal olarak büyük boyutlarda olmaları gerekir. Bu teknolojiler en gelişmiş veri koruma ve iş sürekliliği özelliklerine sahip olsa da, bu özellikler sanallaştırılmış bulut için oluşturulmazlar. Gecikme süreleri, gürültülü komşular ve değişiklik yönetimi ve bakım etkinliği üzerinde daha fazla denetim gerektirir.

BareMetal altyapısı, bu tür uygulamalardan oluşan seçim kümesi için oluşturulur, sertifikalandırilmiştir ve test edilmiştir. Azure, bu tür çözümleri sunan ilk ilkiydi ve bu yana en büyük portföy ve en gelişmiş sistemlerle sonuçlanmıştır.

BareMetal altyapısı şu avantajları sunar: 

- Adanmış örnekler
- Özelleştirilmiş iş yükleri için sertifikalı donanım
    - SAP ( [SAP notuna #1928533](https://launchpad.support.sap.com/#/notes/1928533)bakın)
    - Oracle ( [Oracle belge kimliği #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Çıplak (işlem sanallaştırma yok)
- Azure 'da barındırılan uygulama VM 'Leri ile BareMetal örneklerine düşük gecikme süresi (0,35 MS)
- Tüm flash SSD ve NVMe
    - En fazla 1 PB/kiracı 
    - 1,2 milyon/kiracıya kadar ıOPS 
    - 40/100 GB ağ bant genişliği
    - NFS, ISCSı ve FC aracılığıyla erişilebilir
- Yedekli güç, güç kaynakları, NIC 'ler, bağlantı noktaları, WAN, depolama ve yönetim
- Hata üzerinde değişiklik için sık erişimli yedekler (yeniden yapılandırma gerekmeden)
- Müşteri ile eşgüdümlü Bakım pencereleri
- Uygulamayla uyumlu anlık görüntüler, arşivleme, yansıtma ve kopyalama


## <a name="baremetal-benefits"></a>BareMetal avantajları  

BareMetal altyapısı, kurumsal uygulamalarınızı çalıştırmak için sertifika gerektiren görev açısından kritik iş yükleri için tasarlanmıştır. BareMetal örnekleri yalnızca size ayrılmıştır ve işletim sistemine (OS) tam erişime (kök erişim) sahip olacaksınız. İşletim sistemini ve uygulama yüklemesini gereksinimlerinize göre yönetirsiniz. Güvenlik için, örnekleri internet bağlantısı olmadan Azure sanal ağınız (VNet) içinde sağlanır. Yalnızca sanal makineleriniz (VM) ve aynı katman 2 ağındaki diğer Azure hizmetlerinde çalışan hizmetler, BareMetal örnekleriniz ile iletişim kurabilir.  

BareMetal altyapısı şu avantajları sunar: 

- Özelleştirilmiş iş yükleri için sertifikalı donanım
- SAP ( [SAP notuna #1928533](https://launchpad.support.sap.com/#/notes/1928533)bakın)
- Oracle ( [Oracle belge kimliği #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Hiper yönetici olmayan BareMetal örneği, tek kiracılı sahiplik
- Azure 'da barındırılan uygulama VM 'Leri ile BareMetal örneklerine düşük gecikme süresi (0,35 MS)
- Tüm flash SSD ve NVMe desteği
- Veritabanı en fazla 1 PB/kiracı 
- 1,2 milyon/kiracıya kadar ıOPS 
- 50 GB ağ bant genişliği 

## <a name="sku-availability-in-azure-regions"></a>Azure bölgelerindeki SKU kullanılabilirliği

BareMetal altyapısı, özel iş yükleri için sertifikalı birden çok SKU sunar. İhtiyaçlarınızı karşılamak için iş yüküne özgü SKU 'Ları kullanın.

- Büyük örnekler: iki soketli ve dört soketli sistemler arasında.  
- Çok büyük örnekler: dört soketli ve Yirmi soketli sistemler arasında. 

Özelleştirilmiş iş yükleri için BareMetal altyapısı aşağıdaki Azure bölgelerinde kullanılabilir:
- West Europe
- Kuzey Avrupa
- Almanya Orta Batı * bölge desteği
- Doğu ABD 2 * bölge desteği
- Doğu ABD * bölge desteği
- Batı ABD * bölge desteği
- Batı ABD 2 * bölge desteği
- Orta Güney ABD

>[!NOTE]
>**Bölgeler desteği** , BareMetal örneklerinin yüksek dayanıklılık ve kullanılabilirlik için bölgeler arasında dağıtılabileceği bölge içindeki kullanılabilirlik bölgelerini ifade eder. Bu özellik, çok siteli etkin-etkin ölçekleme desteğini sunar.

## <a name="managing-baremetal-instances-in-azure"></a>Azure 'da BareMetal örnekleri yönetme 

Gereksinimlerinize bağlı olarak, BareMetal altyapısının uygulama topolojileri karmaşık olabilir. Paylaşılan veya ayrılmış depolama ve özelleştirilmiş LAN ve WAN bağlantıları ile bir veya daha fazla konumda birden çok örnek dağıtabilirsiniz. Bu nedenle, Azure, bir sağlama portalındaki alana bir CSA/GBB tarafından bu bilgilerin bir danışmanlığını yakalamasına olanak sağlar. 

Karemetal altyapınızın sağlandığı zamana göre, işletim sistemi, ağlar, depolama birimleri, bölgelerde bölgeler ve bölgelerde ve konumlar arasındaki WAN bağlantıları önceden yapılandırılmıştır. İşletim sistemi lisanslarınızı (KLG) kaydetmek, işletim sistemini yapılandırmak ve uygulama katmanını yüklemek için hazırsınız.

Tüm BareMetal kaynakları ve bunların durumlarını ve özniteliklerini Azure portal. Ayrıca, örnekleri işleyebilir ve hizmet isteklerini açabilir ve bilet buradan destekleyebilirsiniz. 

## <a name="operational-model"></a>İşletimsel model
BareMetal altyapısı ISO 27001, ISO 27017, SOC 1 ve SOC 2 uyumludur. Ayrıca, kendi lisansını getir (KLG) modelini kullanır: işletim sistemi, özel iş yükü ve üçüncü taraf uygulamalar.  

Kök erişim ve tam denetim elde edersiniz almaz, şu şekilde sorumluluk alacaksınız:
- Yedekleme ve kurtarma çözümlerini, yüksek kullanılabilirliği ve olağanüstü durum kurtarmayı tasarlama ve uygulama.
- İşletim sistemi ve üçüncü taraf yazılım için lisanslama, güvenlik ve destek.

Microsoft 'un sorumluluğundadır:
- Özel iş yükleri için donanım sağlama. 
- İşletim sistemini sağlama.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="BareMetal altyapı desteği modelinin diyagramı." border="false":::

## <a name="baremetal-instance-stamp"></a>BareMetal örnek damgası

BareMetal örnek damgası, aşağıdaki bileşenleri birleştirir:

- **Hesaplanıyor:** Gerekli bilgi işlem yeteneğini sağlayan ve özelleştirilmiş iş yükü için sertifikalı Intel Xeon işlemcilerin oluşturulmasını temel alan sunucular.

- **Ağ:** Birleşik yüksek hızlı bir ağ dokusunda bilgi işlem, depolama ve LAN bileşenleri birbirine bağlanır.

- **Depolama alanı:** Birleşik bir ağ dokusunda erişilen bir altyapı.

BareMetal damgasının çok kiracılı altyapısı içinde müşteriler yalıtılmış kiracılarda dağıtılır. Bir kiracı dağıttığınızda Azure kaydınızda bir Azure aboneliği adı alırsınız. Bu Azure aboneliği, BareMetal örnekleriniz için faturalandırılan bir aboneliğiniz.

>[!NOTE]
>Bir BareMetal örneği dağıtan bir müşteri, kiracıya yalıtılmıştır. Bir kiracı, diğer kiracılardan ağ, depolama ve işlem katmanında yalıtılmıştır. Farklı kiracılara atanan depolama ve işlem birimleri, Bunaremetal örneklerinde birbirini göremez veya birbirleriyle iletişim kuramaz.

## <a name="operating-system"></a>İşletim sistemi
BareMetal örneğinin sağlanması sırasında, makinelere yüklemek istediğiniz işletim sistemini seçebilirsiniz. 

>[!NOTE]
>Unutmayın, BareMetal altyapısı bir KLG modelidir.

Kullanılabilir Linux işletim sistemi sürümleri şunlardır:
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>Depolama
BareMetal altyapısı, yüksek düzeyde yedekli NFS depolama ve Fiber Kanal depolama alanı sağlar. Altyapı, SAP, SQL ve daha fazlası gibi kurumsal iş yükleri için derin tümleştirme sunar. Ayrıca uygulamayla tutarlı veri koruma ve veri yönetimi özellikleri sağlar. Self servis yönetim araçları, tek bir cam izleme bölmesiyle birlikte boş bir anlık görüntü, kopyalama ve ayrıntılı çoğaltma yetenekleri sunar. Altyapı, veri kullanılabilirliği ve iş sürekliliği ihtiyaçları için sıfır RPO ve RTO özellikleri sağlar. 

Depolama altyapısı şunları sunar:
- 4 x 100 GB yukarı bağlantılar.
- En fazla 32 GB Fiber Kanal uplink.
- Tüm flash SSD ve NVMe sürücüleri.
- Ultra düşük gecikme süresi ve yüksek aktarım hızı.
- En fazla 4 PB ham depolama alanını ölçeklendirir. 
- En çok 11.000.000 ıOPS.

Bu veri erişim protokolleri desteklenir: 
- iSCSI 
- NFS (v3 veya v4) 
- Fiber Kanal 
- FC üzerinde NVMe  

## <a name="networking"></a>Ağ
Azure ağ hizmetleri 'nin mimarisi, BareMetal örneklerinde özelleştirilmiş iş yüklerinin başarılı bir şekilde dağıtılması için önemli bir bileşendir. Bu, tüm BT sistemleri Azure 'da zaten yer alıyor olabilir. Azure, Azure 'u şirket içi yazılım dağıtımlarınıza bir sanal veri merkezi gibi görünür hale getirmek için size ağ teknolojisi sunar. BareMetal örnekleri için gereken Azure ağ işlevselliği şunları içerir:

- Azure sanal ağları, şirket içi ağ varlıklarınıza bağlanan Azure ExpressRoute bağlantı hattına bağlanır.
- Şirket içinde Azure 'a bağlanan ExpressRoute bağlantı hattının en az 1 Gbps veya daha yüksek bant genişliğine sahip olması gerekir.
- Azure 'da uzatılmış Active Directory ve DNS veya tamamen Azure 'da çalışır.

ExpressRoute, şirket içi ağınızı bir bağlantı sağlayıcısının yardımı ile özel bir bağlantı üzerinden Microsoft bulutuna genişletmenizi sağlar. Şirket içi konumunuz ve istediğiniz Azure bölgesi arasında uygun maliyetli veri aktarımı için **ExpressRoute yerel** ' i kullanabilirsiniz. Politik sınırlar genelinde bağlantıyı genişletmek için **ExpressRoute Premium**'u etkinleştirebilirsiniz. 

BareMetal örnekleri, Azure VNet sunucunuzun IP adresi aralığınızı içinde temin edilir.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Azure BareMetal altyapı diyagramının mimari diyagramı." lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

Gösterilen mimari üç bölüme ayrılmıştır:
- **Sol:** Farklı uygulamaları çalıştıran, iş ortağı veya Equinx gibi yerel uç yönlendiriciyle bağlanan müşteri şirket içi altyapısını gösterir. Daha fazla bilgi için bkz. [bağlantı sağlayıcıları ve konumlar: Azure ExpressRoute](../expressroute/expressroute-locations.md).
- **Ortala:** Azure Edge ağına bağlantısı sunan Azure aboneliğiniz kullanılarak sağlanan [ExpressRoute](../expressroute/expressroute-introduction.md) 'ı gösterir.
- **Sağ:** Azure IaaS ' i gösterir ve bu durumda, Azure sanal ağınız dahilinde sağlanan uygulamalarınızı barındırmak için VM 'lerin kullanılması.
- **Alt:** Düşük gecikme süresine sahip BareMetal bağlantısı için ExpressRoute [FastPath](../expressroute/about-fastpath.md) Ile etkinleştirilen ExpressRoute ağ geçidinizin kullanımını gösterir.   
   >[!TIP]
   >Bunu desteklemek için, ExpressRoute ağ geçidinizin UltraPerformance olmalıdır. Daha fazla bilgi için bkz. [ExpressRoute sanal ağ geçitleri hakkında](../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="next-steps"></a>Sonraki adımlar

Sonraki adım Azure portal aracılığıyla BareMetal örnekleriyle nasıl tanımlanacağına ve etkileşime gireceğini öğrenmektedir.

> [!div class="nextstepaction"]
> [Azure portal aracılığıyla BareMetal örnekleri yönetme](connect-baremetal-infrastructure.md)
