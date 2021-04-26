---
title: Azure 'da Oracle veritabanı tasarlama ve uygulama | Microsoft Docs
description: Azure ortamınızda bir Oracle veritabanı tasarlayın ve uygulayın.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/17/2020
ms.author: kegorman
ms.reviewer: tigorman
ms.openlocfilehash: 6e59d0065dfa74979bf3bbc72458bda516e3b641
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669986"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Azure 'da Oracle veritabanı tasarlama ve uygulama

## <a name="assumptions"></a>Varsayımlar

- Bir Oracle veritabanını Şirket içinden Azure 'a geçirmeyi planlıyorsunuz.
- Geçirmek istediğiniz Oracle Database için [Tanılama paketi](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) veya [otomatik iş yükü deposu](https://www.oracle.com/technetwork/database/manageability/info/other-manageability/wp-self-managing-database18c-4412450.pdf) var
- Oracle 'daki çeşitli ölçümleri kavramış olursunuz.
- Uygulama performansı ve platform kullanımının temel olarak anlaşılmış olursunuz.

## <a name="goals"></a>Hedefler

- Azure 'da Oracle dağıtımınızı en iyi hale getirmeyi öğrenin.
- Bir Azure ortamında Oracle veritabanı için performans ayarlama seçeneklerini gezin.
- Mimari ve avantajlar aracılığıyla fiziksel ayarlama sınırları, veritabanı kodunun mantıksal ayarı, (SQL) ve genel veritabanı tasarımı arasında net beklentileri vardır.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Şirket içi ve Azure uygulamaları arasındaki farklar 

Şirket içi uygulamaları Azure 'a geçirirken göz önünde bulundurmanız gereken bazı önemli noktalar aşağıda verilmiştir. 

Önemli bir farklılık, bir Azure uygulamasında VM 'Ler, diskler ve sanal ağlar gibi kaynakların diğer istemciler arasında paylaşılmasıdır. Ayrıca, kaynaklar gereksinimlere göre kısıtlanabilir. Azure, başarısız olmaya (MTBF) odaklanmak yerine, hatanın (MTTR) arızalanmaya yönelik daha fazla odaklanılmıştır.

Aşağıdaki tabloda, şirket içi uygulama ve bir Oracle veritabanının Azure uygulamasıyla ilgili bazı farklılıklar listelenmiştir.


|  | Şirket içi uygulama | Azure uygulama |
| --- | --- | --- |
| **Ağ** |LAN/WAN  |SDN (yazılım tanımlı ağ)|
| **Güvenlik grubu** |IP/bağlantı noktası kısıtlama araçları |[Ağ güvenlik grubu (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
| **Esnekliği** |MTBF (hataların arasındaki ortalama süre) |MTTR (ortalama kurtarma süresi)|
| **Planlı bakım** |Düzeltme eki uygulama/yükseltme|[Kullanılabilirlik kümeleri](/previous-versions/azure/virtual-machines/windows/infrastructure-example) (Azure tarafından yönetilen düzeltme eki/yükseltmeler) |
| **Kaynak** |Ayrılmış  |Diğer istemcilerle paylaşılıyor|
| **Bölgeler** |Veri merkezleri |[Bölge çiftleri](../../regions.md#region-pairs)|
| **Depolama** |SAN/fiziksel diskler |[Azure tarafından yönetilen depolama](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **Ölçeklendirme** |Dikey Ölçek |Yatay ölçeklendirme|


### <a name="requirements"></a>Gereksinimler

- Oracle 'ın çekirdek tarafından lisansladığı şekilde gerçek CPU kullanımını belirleme, vCPU gereksinimlerinin boyutlandırılması, maliyet tasarrufları için önemli bir alıştırma olabilir. 
- Veritabanı boyutunu, yedekleme depolama alanını ve büyüme oranını saptayın.
- Oracle statspack ve AWR raporlarına veya işletim sistemi düzeyi depolama izleme araçlarından temel alarak tahmin edebileceğiniz GÇ gereksinimlerini saptayın.

## <a name="configuration-options"></a>Yapılandırma seçenekleri

Bir Azure ortamında performansı artırmak için ayarlayabilmeniz gereken dört olası alan vardır:

- Sanal makine boyutu
- Ağ aktarım hızı
- Disk türleri ve yapılandırma
- Disk önbelleği ayarları

### <a name="generate-an-awr-report"></a>AWR raporu oluşturma

Mevcut bir Oracle Enterprise Edition veritabanınız varsa ve Azure 'a geçirmeyi planlıyorsanız, birkaç seçeneğiniz vardır. Oracle örnekleriniz için [Tanılama paketiniz](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) varsa, ÖLÇÜMLERI (IOPS, Mbps, Gibs vb.) almak için Oracle AWR raporunu çalıştırabilirsiniz. Tanılama paketi lisansı olmayan bu veritabanları veya standart bir sürüm veritabanı için, el ile anlık görüntüler toplandıktan sonra aynı önemli ölçümler bir statspack raporuyla toplanabilir.  Bu iki raporlama yöntemi arasındaki temel fark, AWR 'nin otomatik olarak toplanması ve veritabanı hakkında statspack 'in öncül raporlama seçeneğinden daha fazla bilgi sağlar.

AWR raporunuzu hem düzenli hem de yoğun iş yükleri sırasında çalıştırmayı düşünebilirsiniz; böylece karşılaştırabilirsiniz. Daha doğru iş yükünü toplamak için, bir haftadan oluşan genişletilmiş bir pencere raporunu ve 24 saatlik bir çalışmayı göz önünde bulundurun ve AWR 'nin rapordaki hesaplamaları kapsamında ortalamalar sunacağını unutmayın.  Bir veri merkezi geçişi için, üretim sistemlerinde boyutlandırma için rapor toplamayı ve Kullanıcı testi, test, geliştirme vb. için kullanılan kalan veritabanı kopyalarını tahmine göre (üretim, test ve üretim boyutu %50 ' e eşit) tahmin etmenizi öneririz.

Varsayılan olarak, AWR deposu 8 gün veri tutar ve saatlik aralıklarda anlık görüntü alır.  Bir AWR raporunu komut satırından çalıştırmak için, bir terminalden aşağıdakiler uygulanabilir:

```bash
$ sqlplus / as sysdba
SQL> @$ORACLE_HOME/rdbms/admin/awrrpt.sql;
```

### <a name="key-metrics"></a>Ana ölçümler

Rapor aşağıdaki bilgileri ister:
- Rapor türü: HTML veya metın (12,1 ' de HTML) ve metın biçiminden daha fazla bilgi sağlar.)
- Anlık görüntülerin görüntüleneceği gün sayısı (bir saatlik aralıklar için, bir haftalık rapor, anlık görüntü kimliklerinde 168 farklı olacaktır)
- Rapor penceresi için anlık görüntüyle başlayan TID.
- Rapor penceresi için anlık görüntüyle sona erme kimliği.
- AWR betiği tarafından oluşturulacak raporun adı.

AWR 'yi gerçek bir uygulama kümesinde (RAC) çalıştırıyorsanız, awrrpt. SQL yerine awrgrpt. SQL olur.  "G" raporu, tek bir rapordaki RAC veritabanındaki tüm düğümler için bir rapor oluşturur ve her RAC düğümünde bir tane çalıştırmak zorunda kalır.

AWR raporundan elde ettiğiniz ölçümler aşağıda verilmiştir:

- Veritabanı adı, örnek adı ve ana bilgisayar adı
- Veritabanı sürümü (Oracle tarafından desteklenebilirlik)
- CPU/çekirdek
- SGA/PGA, (ve 'nin boyutlandırılıp boyutlandırılmayacağını bilmenizi sağlamak için danışmanları)
- GB cinsinden toplam bellek
- CPU% meşgul
- DB CPU 'Ları
- IOPS (okuma/yazma)
- MBPs (okuma/yazma)
- Ağ aktarım hızı
- Ağ gecikmesi oranı (düşük/yüksek)
- En fazla bekleme olayları 
- Veritabanı için parametre ayarları
- Veritabanı RAC, sınava verileri, gelişmiş özellikler veya yapılandırma kullanımı

### <a name="virtual-machine-size"></a>Sanal makine boyutu

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. AWR raporundan CPU, bellek ve g/ç kullanımını temel alarak VM boyutunu tahmin edin

' A bakabileceğiniz bir şey, sistem performans sorunlarının nerede olduğunu belirten ilk beş zaman aşımına uğramış ön etkinlik olaydır.

Örneğin, aşağıdaki diyagramda günlük dosyası eşitleme en üstte. Bu, LGWR 'ın günlük arabelleğini yeniden yineleme günlük dosyasına yazmadan önce gerekli olan bekleme sayısını belirtir. Bu sonuçlar, daha iyi uygulanan depolama veya disklerin gerekli olduğunu gösterir. Ayrıca, diyagram CPU (çekirdek) sayısını ve bellek miktarını da gösterir.

![Tablonun en üstünde bulunan günlük dosyası eşitlemesini gösteren ekran görüntüsü.](./media/oracle-design/cpu_memory_info.png)

Aşağıdaki diyagramda, okuma ve yazma toplam g/ç sayısı gösterilmektedir. Rapor sırasında yazılan 59 GB okuma ve 247,3 GB vardı.

![Okuma ve yazma toplam g/ç sayısını gösteren ekran görüntüsü.](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. bir VM seçin

AWR raporundan topladığınız bilgilere bağlı olarak, bir sonraki adım gereksinimlerinize uyan benzer boyuttaki bir VM 'yi seçmek. Kullanılabilir VM 'lerin bir listesini [bellek için iyileştirilmiş](../../sizes-memory.md)makalesinde bulabilirsiniz.

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. ACU 'ya dayalı benzer bir VM Serisi ile sanal makine boyutlandırmasına ince ayar yapın

VM 'yi seçtikten sonra, VM için ACU 'ya dikkat edin. Gereksinimlerinize daha uygun olan ACU değerine göre farklı bir VM seçebilirsiniz. Daha fazla bilgi için bkz. [Azure işlem birimi](../../acu.md).

![ACU birimleri sayfasının ekran görüntüsü](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Ağ aktarım hızı

Aşağıdaki diyagramda, üretilen iş ve ıOPS arasındaki ilişki gösterilmektedir:

![Aktarım hızı ekran görüntüsü](./media/oracle-design/throughput.png)

Toplam ağ verimlilik, aşağıdaki bilgilere göre tahmin edilir:
- SQL * NET trafiği
- MBps x sunucu sayısı (Oracle Data Guard gibi giden akış)
- Uygulama çoğaltma gibi diğer faktörler

![SQL * NET aktarım hızı ekran görüntüsü](./media/oracle-design/sqlnet_info.png)

Ağ bant genişliği gereksinimlerinize göre, aralarından seçim yapabileceğiniz çeşitli ağ geçidi türleri vardır. Bunlar temel, VpnGw ve Azure ExpressRoute 'u içerir. Daha fazla bilgi için [VPN Gateway fiyatlandırması sayfasına](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h)bakın.

**Öneriler**

- Ağ gecikmesi, şirket içi dağıtıma kıyasla daha yüksektir. Ağ gidiş dönüşlerini düşürmek performansı önemli ölçüde iyileştirebilir.
- Gidiş-gelişleri azaltmak için, yüksek işlemlere sahip uygulamaları veya aynı sanal makinede "geveze" uygulamalarını birleştirin.
- Daha iyi ağ performansı için, [hızlandırılmış ağ](../../../virtual-network/create-vm-accelerated-networking-cli.md) Ile sanal makineleri kullanın.
- Belirli Linux dağıtımları için [kesme/EŞLEMEYI kaldır desteğini](/previous-versions/azure/virtual-machines/linux/configure-lvm#trimunmap-support)etkinleştirmeyi düşünün.
- [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) 'ı ayrı bir sanal makineye yükler.
- Çok büyük sayfalar Linux üzerinde varsayılan olarak etkinleştirilmemiştir. Büyük sayfaları etkinleştirmeyi ve Oracle DB ayarlamayı düşünün `use_large_pages = ONLY` . Bu, performansı artırmaya yardımcı olabilir. [Burada](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390) daha fazla bilgi bulabilirsiniz.

### <a name="disk-types-and-configurations"></a>Disk türleri ve yapılandırma

- *Varsayılan işletim sistemi diskleri*: Bu disk türleri kalıcı veriler ve önbelleğe alma sağlar. Başlangıçta işletim sistemi erişimi için iyileştirilir ve işlem veya veri ambarı (analitik) iş yükleri için tasarlanmamıştır.

- *Yönetilen diskler*: Azure, VM disklerinizde kullandığınız depolama hesaplarını yönetir. Disk türünü (Oracle iş yükleri için en sık Premium SSD) ve ihtiyacınız olan diskin boyutunu belirtirsiniz. Azure, diski sizin için oluşturur ve yönetir.  Premium SSD yönetilen disk yalnızca bellek için iyileştirilmiş ve özellikle tasarlanan VM Serisi için kullanılabilir. Belirli bir VM boyutunu seçtikten sonra, menü yalnızca söz konusu VM boyutunu temel alan kullanılabilir Premium Depolama SKU 'Larını gösterir.

![Yönetilen disk sayfasının ekran görüntüsü](./media/oracle-design/premium_disk01.png)

Depolama alanınızı bir VM 'de yapılandırdıktan sonra, bir veritabanı oluşturmadan önce diskleri test etmek isteyebilirsiniz. G/ç hızını gecikme süresi ve aktarım hızı açısından bilmek, VM 'Lerin gecikme süresiyle beklenen aktarım hızını desteklemesinin gerekip gerekmediğini belirlemenize yardımcı olabilir.

Uygulama yük testi için Oracle Orion, Sysbench, SLOB ve Fio gibi çeşitli araçlar vardır.

Bir Oracle veritabanını dağıttıktan sonra yük testini yeniden çalıştırın. Düzenli ve en yoğun iş yüklerinizi başlatın ve sonuçlar size ortamınızın temelini gösterir.  İş yükü testinde gerçekçi olması gerekir; VM 'de, gerçekteki sanal makine üzerinde çalıştırdıklarınız gibi hiçbir şey iş yükünü çalıştırmak mantıklı değildir.

Oracle yoğun bir şekilde yoğun bir şekilde yoğun bir veritabanıdır, depolama boyutu yerine ıOPS ücretine göre depolamanın boyutunun boyutlandırilmesi oldukça önemlidir. Örneğin, gerekli ıOPS 5.000 ise ancak yalnızca 200 GB gerekliyse, P30 sınıfı Premium diski, 200 GB 'tan fazla depolama alanı ile gelse de alabilir.

IOP oranı, AWR raporundan elde edilebilir. Bu, yineleme günlüğü, fiziksel okuma ve yazma oranına göre belirlenir.  Seçilen VM serisinin her zaman, iş yükünün g/ç talebini da işleyebilme olanağı olduğunu doğrulayın.  VM 'nin depolamadan daha düşük bir GÇ sınırı varsa, en yüksek sınır VM tarafından ayarlanır.

![AWR rapor sayfasının ekran görüntüsü](./media/oracle-design/awr_report.png)

Örneğin, yineleme boyutu 11,63 MB/sn 'ye eşit olan saniyede 12.200.000 bayttır.
IOPS 12.200.000/2.358 = 5.174.

G/ç gereksinimlerinden oluşan net bir resme sahip olduktan sonra, bu gereksinimleri karşılamak için en uygun sürücü birleşimini seçebilirsiniz.

**Öneriler**

- Veri alanı için, yönetilen depolama veya Oracle ASM kullanarak bir dizi diskte g/ç iş yükünü yayın.
- G/ç 'yi azaltmak için Oracle gelişmiş sıkıştırmayı kullanın (her iki veri ve dizinler için).
- Yinele günlüklerini, TEMP ve ayrı veri disklerindeki tablo alanlarını geri alın.
- Herhangi bir uygulama dosyasını varsayılan işletim sistemi disklerine yerleştirmeyin (/dev/sda). Bu diskler hızlı VM önyükleme süreleri için en iyi duruma getirilmemiştir ve uygulamanız için iyi performans sağlamabilirler.
- Premium depolamada, d serisi VM 'Ler kullanırken, yeniden yineleme diski üzerinde [yazma Hızlandırıcısı](../../how-to-enable-write-accelerator.md) etkinleştirin.
- Yineleme günlüklerini Ultra diske yüksek gecikme süresiyle taşımayı düşünün.

### <a name="disk-cache-settings"></a>Disk önbelleği ayarları

Konak önbelleğe alma için üç seçenek vardır ancak bir Oracle veritabanı için veritabanı iş yükü için yalnızca ReadOnly önbelleğe alma önerilir.  ReadWrite bir veri dosyası için önemli güvenlik açıkları ortaya çıkarabilir. Bu, bir veritabanı yazma hedefinin, bu bilgileri önbelleğe almak için veri tabanına kaydedesağlamaktır.

Bir dosya sisteminin veya uygulamanın aksine, bir veritabanı için konak önbelleğe alma önerisi *salt okunur* olur: tüm istekler sonraki kullanımlar için önbelleğe alınır. Tüm yazma işlemleri diske yazılmaya devam eder.

**Öneriler**

Aktarım hızını en üst düzeye çıkarmak için mümkün olduğunda ana bilgisayar önbelleği için **ReadOnly** ile başlamanız önerilir. Premium Depolama için, dosya sistemini **ReadOnly** seçenekleriyle bağladığınızda "engelleri" devre dışı bırakmanız gerektiğini aklınızda bulundurun. /Etc/fstab dosyasını UUID ile disklere güncelleştirin.

![ReadOnly ve None seçeneklerini gösteren yönetilen disk sayfasının ekran görüntüsü.](./media/oracle-design/premium_disk02.png)

- İşletim sistemi diskleri için varsayılan **okuma/yazma** önbelleğini kullanın ve Oracle Iş yükü VM 'leri IÇIN Premium SSD kullanın.  Ayrıca, takas için kullanılan birimin de Premium SSD üzerinde olduğundan emin olun.
- Tüm VERI dosyaları için önbelleğe alma için **ReadOnly** kullanın. Salt okunur önbelleğe alma yalnızca Premium yönetilen disk, P30 ve üzeri için kullanılabilir.  Salt okunur önbelleğe alma ile kullanılabilen bir 4095GiB birimi için bir sınır vardır.  Daha büyük bir ayırma, varsayılan olarak konak önbelleğe almayı devre dışı bırakır.

İş yükleri gün ve akşam arasında büyük ölçüde farklılık gösterir ve GÇ iş yükü bunu destekleyebileceğinden, burç içeren P1-P20 Premium SSD, gece, toplu iş yükleri veya sınırlı GÇ talepleri sırasında gereken performansı sağlayabilir.  

## <a name="security"></a>Güvenlik

Azure ortamınızı ayarladıktan ve yapılandırdıktan sonra, bir sonraki adım ağınızı güvenli hale getirmeye çalışır. Bazı öneriler aşağıda verilmiştir:

- *NSG ilkesi*: NSG, bir alt ağ veya NIC ile tanımlanabilir. Erişimi, hem güvenlik hem de uygulama güvenlik duvarları gibi şeyler için zorla yönlendirme için alt ağ düzeyinde denetlemek daha basittir.

- Sıçrama *kutusu*: daha güvenli erişim için Yöneticiler Uygulama hizmetine veya veritabanına doğrudan bağlanmamalıdır. Bir atlama kutusu, yönetici makinesi ile Azure kaynakları arasında bir medya olarak kullanılır.
![Sıçrama kutusu topolojisi sayfasının ekran görüntüsü](./media/oracle-design/jumpbox.png)

    Yönetici makinesi yalnızca atlama kutusuna IP kısıtlı erişim sunmalıdır. Sıçrama kutusunun uygulamaya ve veritabanına erişimi olmalıdır.

- *Özel ağ* (alt ağlar): uygulama hizmeti ve veritabanınızın ayrı alt ağlarda olması önerilir, bu sayede NSG ilkesi tarafından daha iyi denetim ayarlanabilir.


## <a name="additional-reading"></a>Ek okuma

- [Oracle ASM’yi yapılandırma](configure-oracle-asm.md)
- [Oracle Data Guard’ı yapılandırma](configure-oracle-dataguard.md)
- [Oracle altın kapısını yapılandırma](configure-oracle-golden-gate.md)
- [Oracle yedekleme ve kurtarma](./oracle-overview.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: yüksek oranda kullanılabilir VM 'Ler oluşturma](../../linux/create-cli-complete.md)
- [VM dağıtımı Azure CLı örneklerini keşfet](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
