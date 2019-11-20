---
title: Azure 'da Oracle veritabanı tasarlama ve uygulama | Microsoft Docs
description: Azure ortamınızda bir Oracle veritabanı tasarlayın ve uygulayın.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: c2c2d1a9affe13d485bfeef52c781ed259b53bc8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100123"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Azure’da Oracle veritabanı tasarlama ve dağıtma

## <a name="assumptions"></a>Varsayımlar

- Bir Oracle veritabanını Şirket içinden Azure 'a geçirmeyi planlıyorsunuz.
- Geçirmek istediğiniz Oracle Database için [Tanılama paketi](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm)
- Oracle AWR raporlarında çeşitli ölçümleri kavramış olursunuz.
- Uygulama performansı ve platform kullanımının temel olarak anlaşılmış olursunuz.

## <a name="goals"></a>Hedefleri

- Azure 'da Oracle dağıtımınızı en iyi hale getirmeyi öğrenin.
- Bir Azure ortamında Oracle veritabanı için performans ayarlama seçeneklerini gezin.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>Şirket içi ve Azure uygulamaları arasındaki farklar 

Şirket içi uygulamaları Azure 'a geçirirken göz önünde bulundurmanız gereken bazı önemli noktalar aşağıda verilmiştir. 

Önemli bir farklılık, bir Azure uygulamasında VM 'Ler, diskler ve sanal ağlar gibi kaynakların diğer istemciler arasında paylaşılmasıdır. Ayrıca, kaynaklar gereksinimlere göre kısıtlanabilir. Azure, başarısız olmaya (MTBF) odaklanmak yerine, hatanın (MTTR) arızalanmaya yönelik daha fazla odaklanılmıştır.

Aşağıdaki tabloda, şirket içi uygulama ve bir Oracle veritabanının Azure uygulamasıyla ilgili bazı farklılıklar listelenmiştir.

> 
> |  | **Şirket içi uygulama** | **Azure uygulama** |
> | --- | --- | --- |
> | **Ağ** |LAN/WAN  |SDN (yazılım tanımlı ağ)|
> | **Güvenlik grubu** |IP/bağlantı noktası kısıtlama araçları |[Ağ güvenlik grubu (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Esnekliği** |MTBF (hataların arasındaki ortalama süre) |MTTR (ortalama kurtarma süresi)|
> | **Planlı bakım** |Düzeltme eki uygulama/yükseltme|[Kullanılabilirlik kümeleri](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (Azure tarafından yönetilen düzeltme eki/yükseltmeler) |
> | **Kaynak** |Adanmış  |Diğer istemcilerle paylaşılıyor|
> | **Bölgeler** |Veri merkezleri |[Bölge çiftleri](https://docs.microsoft.com/azure/virtual-machines/windows/regions#region-pairs)|
> | **Depolama** |SAN/fiziksel diskler |[Azure tarafından yönetilen depolama](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Ölçeklendirme** |Dikey Ölçek |Yatay ölçeklendirme|


### <a name="requirements"></a>Gereksinimler

- Veritabanı boyutunu ve büyüme oranını belirleme.
- Oracle AWR raporlarına veya diğer ağ izleme araçlarına göre tahmin edebileceğiniz ıOPS gereksinimlerini saptayın.

## <a name="configuration-options"></a>Yapılandırma seçenekleri

Bir Azure ortamında performansı artırmak için ayarlayabilmeniz gereken dört olası alan vardır:

- Sanal makine boyutu
- Ağ aktarım hızı
- Disk türleri ve yapılandırma
- Disk önbelleği ayarları

### <a name="generate-an-awr-report"></a>AWR raporu oluşturma

Mevcut bir Oracle veritabanınız varsa ve Azure 'a geçirmeyi planlıyorsanız, birkaç seçeneğiniz vardır. Oracle örnekleriniz için [Tanılama paketiniz](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) varsa, ÖLÇÜMLERI (IOPS, Mbps, Gibs vb.) almak için Oracle AWR raporunu çalıştırabilirsiniz. Ardından, topladığınız ölçümlere göre VM 'yi seçin. Ya da benzer bilgiler almak için altyapı ekibinize başvurabilirsiniz.

AWR raporunuzu hem düzenli hem de yoğun iş yükleri sırasında çalıştırmayı düşünebilirsiniz; böylece karşılaştırabilirsiniz. Bu raporlara bağlı olarak, VM 'Leri ortalama iş yüküne veya en yüksek iş yüküne göre boyut olarak kullanabilirsiniz.

Aşağıda, bir AWR raporunun nasıl üreticeğine bir örnek verilmiştir (geçerli yüklemelerinizin bir tane varsa, Oracle Enterprise Manager 'ı kullanarak AWR raporlarınızı oluşturma):

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Anahtar ölçümleri

AWR raporundan elde ettiğiniz ölçümler aşağıda verilmiştir:

- Toplam çekirdek sayısı
- CPU saat hızı
- GB cinsinden toplam bellek
- CPU kullanımı
- En yoğun veri aktarımı hızı
- G/ç değişikliklerinin oranı (okuma/yazma)
- Yeniden yineleme hızı (MBPs)
- Ağ aktarım hızı
- Ağ gecikmesi oranı (düşük/yüksek)
- GB cinsinden veritabanı boyutu
- SQL * NET from/to Client aracılığıyla alınan baytlar

### <a name="virtual-machine-size"></a>Sanal makine boyutu

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. AWR raporundan CPU, bellek ve g/ç kullanımını temel alan VM boyutunu tahmin etme

' A bakabileceğiniz bir şey, sistem performans sorunlarının nerede olduğunu belirten ilk beş zaman aşımına uğramış ön etkinlik olaydır.

Örneğin, aşağıdaki diyagramda günlük dosyası eşitleme en üstte. Bu, LGWR 'ın günlük arabelleğini yeniden yineleme günlük dosyasına yazmadan önce gerekli olan bekleme sayısını belirtir. Bu sonuçlar, daha iyi uygulanan depolama veya disklerin gerekli olduğunu gösterir. Ayrıca, diyagram CPU (çekirdek) sayısını ve bellek miktarını da gösterir.

![AWR rapor sayfasının ekran görüntüsü](./media/oracle-design/cpu_memory_info.png)

Aşağıdaki diyagramda, okuma ve yazma toplam g/ç sayısı gösterilmektedir. Rapor sırasında yazılan 59 GB okuma ve 247,3 GB vardı.

![AWR rapor sayfasının ekran görüntüsü](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. VM seçin

AWR raporundan topladığınız bilgilere bağlı olarak, bir sonraki adım gereksinimlerinize uyan benzer boyuttaki bir VM 'yi seçmek. Kullanılabilir VM 'lerin bir listesini [bellek için iyileştirilmiş](../../linux/sizes-memory.md)makalesinde bulabilirsiniz.

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. ACU 'ya dayalı benzer bir VM Serisi ile sanal makine boyutlandırmasına ince ayar yapma

VM 'yi seçtikten sonra, VM için ACU 'ya dikkat edin. Gereksinimlerinize daha uygun olan ACU değerine göre farklı bir VM seçebilirsiniz. Daha fazla bilgi için bkz. [Azure işlem birimi](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

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
- Daha iyi ağ performansı için, [hızlandırılmış ağ](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) Ile sanal makineleri kullanın.
- Bazı Linux Dağları için [kesme/EŞLEMEYI kaldır desteğini](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm#trimunmap-support)etkinleştirmeyi düşünün.
- [Oracle Enterprise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) 'ı ayrı bir sanal makineye yükler.
- Çok büyük sayfalar Linux üzerinde varsayılan olarak etkinleştirilmemiştir. Büyük sayfaları etkinleştirmeyi ve Oracle DB ayarlamayı `use_large_pages = ONLY` düşünün. Bu, performansı artırmaya yardımcı olabilir. [Burada](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390)daha fazla bilgi bulabilirsiniz.

### <a name="disk-types-and-configurations"></a>Disk türleri ve yapılandırma

- *Varsayılan işletim sistemi diskleri*: Bu disk türleri kalıcı veriler ve önbelleğe alma sağlar. Başlangıçta işletim sistemi erişimi için iyileştirilir ve işlem veya veri ambarı (analitik) iş yükleri için tasarlanmamıştır.

- *Yönetilmeyen diskler*: Bu disk türleriyle, VM disklerinizde karşılık gelen sanal sabit disk (VHD) dosyalarını depolayan depolama hesaplarını yönetirsiniz. VHD dosyaları, Azure depolama hesaplarında sayfa Blobları olarak depolanır.

- *Yönetilen diskler*: Azure, VM disklerinizde kullandığınız depolama hesaplarını yönetir. Disk türünü (Premium veya standart) ve ihtiyacınız olan diskin boyutunu belirtirsiniz. Azure, diski sizin için oluşturur ve yönetir.

- *Premium depolama diskleri*: Bu disk türleri üretim iş yükleri için idealdir. Premium Depolama, DS, DSv2, GS ve F serisi VM 'Ler gibi belirli boyut serisi sanal makinelere eklenebilecek VM disklerini destekler. Premium disk farklı boyutlarda gelir ve 32 GB ile 4.096 GB arasında değişen diskler arasında seçim yapabilirsiniz. Her disk boyutunun kendi performans belirtimleri vardır. Uygulama gereksinimlerinize bağlı olarak, sanal makinenize bir veya daha fazla disk ekleyebilirsiniz.

Portaldan yeni bir yönetilen disk oluşturduğunuzda, kullanmak istediğiniz disk türü için **hesap türünü** seçebilirsiniz. Kullanılabilir disklerin tümünün açılan menüde gösterildiğine dikkat edin. Belirli bir VM boyutunu seçtikten sonra, menü yalnızca söz konusu VM boyutunu temel alan kullanılabilir Premium Depolama SKU 'Larını gösterir.

![Yönetilen disk sayfasının ekran görüntüsü](./media/oracle-design/premium_disk01.png)

Depolama alanınızı bir VM 'de yapılandırdıktan sonra, bir veritabanı oluşturmadan önce diskleri test etmek isteyebilirsiniz. G/ç hızını gecikme süresi ve aktarım hızı açısından bilmek, VM 'Lerin gecikme süresiyle beklenen aktarım hızını desteklemesinin gerekip gerekmediğini belirlemenize yardımcı olabilir.

Uygulama yük testi için Oracle Orion, Sysbench ve Fio gibi çeşitli araçlar vardır.

Bir Oracle veritabanını dağıttıktan sonra yük testini yeniden çalıştırın. Düzenli ve en yoğun iş yüklerinizi başlatın ve sonuçlar size ortamınızın temelini gösterir.

Depolama boyutu yerine ıOPS ücretine göre depolamanın boyutunun oluşturulması daha önemli olabilir. Örneğin, gerekli ıOPS 5.000 ise ancak yalnızca 200 GB gerekliyse, P30 sınıfı Premium diski, 200 GB 'tan fazla depolama alanı ile gelse de alabilir.

IOP oranı, AWR raporundan elde edilebilir. Bu, yineleme günlüğü, fiziksel okuma ve yazma oranına göre belirlenir.

![AWR rapor sayfasının ekran görüntüsü](./media/oracle-design/awr_report.png)

Örneğin, yineleme boyutu 11,63 MB/sn 'ye eşit olan saniyede 12.200.000 bayttır.
IOPS 12.200.000/2.358 = 5.174.

G/ç gereksinimlerinden oluşan net bir resme sahip olduktan sonra, bu gereksinimleri karşılamak için en uygun sürücü birleşimini seçebilirsiniz.

**Öneriler**

- Veri alanı için, yönetilen depolama veya Oracle ASM kullanarak bir dizi diskte g/ç iş yükünü yayın.
- Okuma yoğunluğu ve yazma yoğunluklu işlemler için g/ç blok boyutu arttıkça, daha fazla veri diski ekleyin.
- Büyük sıralı süreçler için blok boyutunu artırın.
- G/ç 'yi (hem veri hem de dizinler için) azaltmak için veri sıkıştırmayı kullanın.
- Yineleme günlüklerini, sistemi ve temps 'yi ayırın ve ayrı veri disklerinde TS 'yi geri alın.
- Herhangi bir uygulama dosyasını varsayılan işletim sistemi disklerine yerleştirmeyin (/dev/sda). Bu diskler hızlı VM önyükleme süreleri için en iyi duruma getirilmemiştir ve uygulamanız için iyi performans sağlamabilirler.
- Premium depolamada, d serisi VM 'Ler kullanırken, yeniden yineleme diski üzerinde [yazma Hızlandırıcısı](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) etkinleştirin.

### <a name="disk-cache-settings"></a>Disk önbelleği ayarları

Konak önbelleğe alma için üç seçenek vardır:

- *Salt okunur*: Tüm istekler sonraki kullanımlar için önbelleğe alınır. Tüm yazma işlemleri doğrudan Azure Blob depolama alanına kalıcıdır.

- *ReadWrite*: Bu bir "salt okuma" algoritmasıdır. Okuma ve yazma işlemleri, gelecekteki okumalar için önbelleğe alınır. Yazısız yazma işlemleri, önce yerel önbellekte kalıcı hale getirilir. Ayrıca, hafif iş yükleri için en düşük disk gecikme süresini de sağlar. Gerekli verileri kalıcı olarak işlemeyen bir uygulamayla ReadWrite önbelleği kullanmak, VM kilitlenirse veri kaybına yol açabilir.

- *Hiçbiri* (devre dışı): Bu seçeneği kullanarak önbelleği atlayabilirsiniz. Tüm veriler diske aktarılır ve Azure depolama 'da kalıcı hale getirilir. Bu yöntem, g/ç yoğunluklu iş yükleri için en yüksek g/ç hızını sağlar. Ayrıca "işlem maliyeti" göz önünde bulundurmanız gerekir.

**Öneriler**

Aktarım hızını en üst düzeye çıkarmak için, konak önbelleğe alma için **none** ile başlamanız önerilir. Premium Depolama için, dosya sistemini **ReadOnly** veya **none** seçenekleriyle bağladığınızda "engelleri" devre dışı bırakmanız gerektiğini aklınızda bulundurun. /Etc/fstab dosyasını UUID ile disklere güncelleştirin.

![Yönetilen disk sayfasının ekran görüntüsü](./media/oracle-design/premium_disk02.png)

- İşletim sistemi diskleri için varsayılan **okuma/yazma** önbelleği kullanın.
- SISTEM, GEÇICI ve GERI alma için, önbelleğe alma için **none** kullanın.
- VERILER için, önbelleğe alma için **hiçbiri** kullanın. Ancak veritabanınız salt okunurdur veya okuma yoğunluğu varsa, **salt okuma** önbelleği kullanın.

Veri diski ayarınız kaydedildikten sonra, işletim sistemi düzeyinde sürücüyü çıkarmadığınız ve sonra değişikliği yaptıktan sonra yeniden bağlanmadığınız takdirde konak önbellek ayarını değiştiremezsiniz.

## <a name="security"></a>Güvenlik

Azure ortamınızı ayarladıktan ve yapılandırdıktan sonra, bir sonraki adım ağınızı güvenli hale getirmeye çalışır. Bazı öneriler aşağıda verilmiştir:

- *NSG ilkesi*: NSG, bir alt ağ veya NIC ile tanımlanabilir. Erişimi, hem güvenlik hem de uygulama güvenlik duvarları gibi şeyler için zorla yönlendirme için alt ağ düzeyinde denetlemek daha basittir.

- Sıçrama *kutusu*: Daha güvenli erişim için Yöneticiler Uygulama hizmetine veya veritabanına doğrudan bağlanmamalıdır. Bir atlama kutusu, yönetici makinesi ile Azure kaynakları arasında bir medya olarak kullanılır.
![Sıçrama kutusu topolojisi sayfasının ekran görüntüsü](./media/oracle-design/jumpbox.png)

    Yönetici makinesi yalnızca atlama kutusuna IP kısıtlı erişim sunmalıdır. Sıçrama kutusunun uygulamaya ve veritabanına erişimi olmalıdır.

- *Özel ağ* (alt ağlar): Uygulama hizmeti ve veritabanınızın ayrı alt ağlarda olması önerilir, bu sayede NSG ilkesi tarafından daha iyi denetim ayarlanabilir.


## <a name="additional-reading"></a>Ek okuma

- [Oracle ASM’yi yapılandırma](configure-oracle-asm.md)
- [Oracle Data Guard 'ı yapılandırma](configure-oracle-dataguard.md)
- [Oracle altın kapısını yapılandırma](configure-oracle-golden-gate.md)
- [Oracle yedekleme ve kurtarma](oracle-backup-recovery.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Yüksek oranda kullanılabilir VM 'Ler oluşturma](../../linux/create-cli-complete.md)
- [VM dağıtımı Azure CLı örneklerini keşfet](../../linux/cli-samples.md)
