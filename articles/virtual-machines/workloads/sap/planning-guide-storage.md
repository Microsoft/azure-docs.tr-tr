---
title: SAP iş yükü için Azure Depolama türleri
description: SAP iş yükleri için Azure depolama türlerini planlama
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/08/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ecd33549536323658a7116d7d5c311eaaec98487
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107302956"
---
# <a name="azure-storage-types-for-sap-workload"></a>SAP iş yükü için Azure Depolama türleri
Azure 'da yetenekler, verimlilik, gecikme ve fiyatlara göre büyük ölçüde farklı depolama türlerine sahiptir. Bazı depolama türleri veya SAP senaryolarında sınırlı kullanılabilir değildir. Ancak, birkaç Azure depolama türü, belirli SAP iş yükü senaryoları için uygundur veya iyileştirilmiştir. Özellikle SAP HANA için, bazı Azure Depolama türleri SAP HANA kullanım için sertifikalandıralındı. Bu belgede, farklı depolama türlerini inceleyeceğiz ve SAP iş yükleri ve SAP bileşenleriyle yeteneklerini ve kullanılabilirliğini anladık.

Bu makale boyunca kullanılan birimler hakkında açıklama. Genel bulut satıcıları, Minb ([Gibibyte](https://en.wikipedia.org/wiki/Gibibyte)) veya TİB (gigabayt veya terabayt yerine boyut birimleri olarak[tebibyte](https://en.wikipedia.org/wiki/Tebibyte) ) kullanacak şekilde taşınır. Bu nedenle, tüm Azure belgeleri ve prize bu birimleri kullanıyor.  Belge boyunca, bu boyut birimlerine MIB, GiB ve TiB birimlerinin özel olarak başvuruyoruz. MB, GB ve TB ile plan yapmanız gerekebilir. Bu nedenle, 250 MiB/sn aktarım hızı yerine 400 MIB/sn aktarım hızı için büyüklük yapmanız gerekiyorsa hesaplamalarda bazı küçük farklılıklara dikkat edin.

## <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Depolama dayanıklılık

Standart HDD, Standart SSD, Azure Premium Storage ve ultra disk Microsoft Azure depolaması, temel VHD 'yi (işletim sistemi ile) ve VM 'ye bağlı veri disklerini veya VHD 'leri üç farklı depolama düğümü üzerinde üç kopyaya tutar. Bir depolama düğümü hatası durumunda başka bir çoğaltmaya yük devretmek ve yeni bir çoğaltmanın dengeli olması saydam olur. Bu yedekliliğe ilişkin bir sonuç olarak, birden çok Azure diskinde her türlü depolama artıklık katmanını kullanmak gerekli **değildir** . Bu olgu yerel yedekli depolama (LRS) olarak adlandırılır. LRS, Azure 'da bu depolama türleri için varsayılandır. [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) , diğer yerel Azure depolama alanı Ile aynı SLA 'ları elde etmek için yeterli artıklık sağlar.

Azure [depolama çoğaltma](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) makalesinde, Azure 'un sunabileceği farklı depolama türleri için uygulanan makalede açıklanan daha fazla artıklık yöntemi vardır. 

Ayrıca, farklı Azure Depolama türlerinin [sanal makineler Için SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)'da yayınlanan tek VM kullanılabilirliği SLA 'larını etkilediğini aklınızda bulundurun.

### <a name="azure-managed-disks"></a>Azure yönetilen diskler

Yönetilen diskler, Azure depolama hesaplarında depolanan VHD 'ler yerine kullanılabilecek Azure Resource Manager bir kaynak türüdür. Yönetilen diskler, bağlı oldukları sanal makinenin [kullanılabilirlik kümesi] [sanal makineler-Yönet-kullanılabilirliği] ile otomatik olarak hizalanır ve bu nedenle sanal makinenizin ve sanal makinede çalışan hizmetlerin kullanılabilirliğini arttırır. Daha fazla bilgi için [genel bakış makalesini](../../managed-disks-overview.md)okuyun.

Dayanıklılık ile ilgili bu örnek, yönetilen disklerin avantajlarını gösterir:

- SAP sisteminize yönelik iki DBMS sanal makinelerinizi bir Azure kullanılabilirlik kümesi 'nde dağıtmakta olursunuz 
- Azure VM 'Leri dağıttığında, işletim sistemi görüntüsüne sahip disk farklı bir depolama kümesine yerleştirilir. Bu, her iki VM 'nin tek bir Azure Storage kümesi tarafından etkilenmemesini önler
- Veritabanınızın veri ve günlük dosyalarını depolamak için bu VM 'lere atadığınız yeni yönetilen diskler oluştururken, iki VM için bu yeni diskler ayrı depolama kümelerinde da dağıtılır. bu nedenle, ilk VM 'nin hiç bir diski ikinci VM diskleriyle depolama kümelerini paylaşmamalıdır

Yönetilen diskler olmadan dağıtım müşteri tanımlı depolama hesaplarında, disk ayırma rastgele olur ve VM 'Lerin bir AvSet içinde dayanıklılık amacıyla dağıtılmasının farkında olmaz.

> [!NOTE]
> Bu nedenle, yönetilen diskler aracılığıyla özel olarak kullanılabilen bazı diğer iyileştirmeler ve bu nedenle, diskleri için Azure blok depolama kullanan yeni VM dağıtımları (Azure NetApp Files hariç tüm Azure depolama), temel VHD/işletim sistemi diskleri için Azure yönetilen diskleri, SAP veritabanı dosyalarını içeren veri disklerini kullanmak için gereklidir. VM 'Leri kullanılabilirlik kümesi aracılığıyla, Kullanılabilirlik Alanları veya kümelerden ve bölgelerden bağımsız olarak dağıtıp dağıtamıyorsanız bağımsız. Yedeklemeleri depolamak amacıyla kullanılan disklerin, yönetilen diskler olması gerekmez.

> [!NOTE]
> Azure yönetilen diskler yalnızca yerel artıklık (LRS) sağlar. 


## <a name="storage-scenarios-with-sap-workloads"></a>SAP iş yükleri ile depolama senaryoları
Kalıcı depolama, Azure 'da dağıttığınız yığının çeşitli bileşenlerinde SAP iş yükünde gereklidir. Bu senaryolar listesi en az şunun gibi:

- İşletim sistemini ve o diske yüklediğiniz diğer yazılımları tutan sanal makinenizin temel VHD 'sini kalıcı yapın. Bu disk/VHD, sanal makinenizin köküdür. Üzerinde yapılan değişikliklerin kalıcı olması gerekir. Bu nedenle, bir sonraki sefer VM 'yi durdurup yeniden başlattığınızda, hala mevcut olan tüm değişiklikler mevcut olmaya devam eder. Özellikle de VM 'nin, Azure tarafından, başlangıçta çalıştırıldığı bilgisayardan başka bir konağa dağıtıldığı durumlarda
- Kalıcı veri diskleri. Bu diskler, uygulamasındaki uygulama verilerini depolamak için eklediğiniz VHD 'lardır. Bu uygulama verileri bir veritabanı, yedekleme dosyaları veya yazılım yüklemelerinin veri ve günlük/yineleme dosyalarını olabilir. , İşletim sistemini tutan taban VHD 'nizin ötesinde herhangi bir disk anlamına gelir
- NetWeaver veya S/4HANA için genel aktarım dizininizi içeren dosya paylaşımları veya paylaşılan diskler. Bu paylaşımların içeriği birden çok VM 'de çalışan yazılım tarafından tüketilebilir veya yüksek kullanılabilirlik yük devretme kümesi senaryoları oluşturmak için kullanılır
- /Sapmnt dizini veya benzer EDI işlemlerine yönelik ortak dosya paylaşımları. Bu paylaşımların içeriği birden çok VM 'de çalışan yazılım tarafından tüketilebilir veya yüksek kullanılabilirlik yük devretme kümesi senaryoları oluşturmak için kullanılır

Sonraki birkaç bölümde, farklı Azure Depolama türleri ve SAP iş yükünün kullanılabilirliği, yukarıdaki dört senaryoya uygun olarak ele alınmıştır. Farklı Azure Depolama türlerinin nasıl kullanılması gerektiği konusunda genel bir kategori, [Azure 'da bulunan disk türleri nelerdir](../../disks-types.md)makalesinde açıklanmaktadır. SAP iş yükü için farklı Azure depolama türlerini kullanmaya yönelik öneriler, farklı şekilde değil.

SAP NetWeaver/4HANA için Azure depolama türlerinde destek kısıtlamaları için, SAP HANA sertifikalı ve desteklenen Azure Depolama türleri için [sap destek dekontunu 2015553](https://launchpad.support.sap.com/#/notes/2015553) okuyun ve [Azure sanal makine depolama yapılandırması SAP HANA](./hana-vm-operations-storage.md)makalesini okuyun.

Farklı Azure depolama türlerini açıklayan bölümler, SAP tarafından desteklenen depolama alanını kullanan kısıtlamalar ve olanaklar hakkında daha fazla arka plan sağlar. 

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>SAP Storage senaryoları için depolama önerileri
Ayrıntılara geçmeden önce belgenin başlangıcında bulunan Özet ve önerileri sunuyoruz. Ancak, belirli Azure Depolama türlerinin ayrıntıları belgenin bu bölümüne göre yapılır. Bir tablodaki SAP Storage senaryolarına yönelik depolama önerilerini özetleme, şöyle görünür:

| Kullanım senaryosu | Standart HDD | Standart SSD | Premium Depolama | Ultra disk | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| İşletim sistemi diski | uygun değil |  kısıtlı uygun (üretim dışı) | Önerilen | mümkün değil | mümkün değil |
| Genel aktarım dizini | desteklenmiyor | desteklenmiyor | Önerilen | Önerilen | Önerilen |
| /sapmnt | uygun değil | kısıtlı uygun (üretim dışı) | Önerilen | Önerilen | Önerilen |
| DBMS veri birimi SAP HANA d/Mv2 VM aileleri | desteklenmiyor | desteklenmiyor | Önerilen | Önerilen | Önerilen<sup>2</sup> |
| DBMS günlük birimi SAP HANA d/Mv2 VM aileleri | desteklenmiyor | desteklenmiyor | Önerilen<sup>1</sup> | Önerilen | Önerilen<sup>2</sup> | 
| DBMS veri birimi SAP HANA Esv3/Edsv4 VM aileleri | desteklenmiyor | desteklenmiyor | Önerilen | Önerilen | Önerilen<sup>2</sup> |
| DBMS günlük birimi SAP HANA Esv3/Edsv4 VM aileleri | desteklenmiyor | desteklenmiyor | desteklenmiyor | Önerilen | Önerilen<sup>2</sup> | 
| DBMS veri birimi HANA olmayan | desteklenmiyor | kısıtlı uygun (üretim dışı) | Önerilen | Önerilen | desteklenmiyor |
| DBMS günlük birimi HANA olmayan p/Mv2 VM aileleri | desteklenmiyor | kısıtlı uygun (üretim dışı) | Önerilen<sup>1</sup> | Önerilen | desteklenmiyor |
| DBMS günlük birimi HANA olmayan/Mv2 VM aileleri | desteklenmiyor | kısıtlı uygun (üretim dışı) | Orta ölçekli iş yüküne uygun | Önerilen | desteklenmiyor |


<sup>1</sup> [Azure yazma Hızlandırıcısı](../../how-to-enable-write-accelerator.md) for log/Mv2 VM aileleri ile günlük/yineleme günlüğü birimleri <sup>2</sup> ' nin kullanımıyla birlikte, ANF kullanarak/Hana/Data ve/Hana/log ' in ANF üzerinde olması gerekir 

Farklı depolama türleri listesinden bekleneceğiniz özellikler şöyle olabilir:

| Kullanım senaryosu | Standart HDD | Standart SSD | Premium Depolama | Ultra disk | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Verimlilik/ıOPS SLA 'Sı | hayır | hayır | evet | evet | evet |
| Gecikme süresi okumaları | yüksek | Orta-yüksek | zayıf | alt milisaniyelik | alt milisaniyelik |
| Gecikme süresi yazmaları | yüksek | Orta-yüksek  | düşük (alt milisaniyelik<sup>1</sup>) | alt milisaniyelik | alt milisaniyelik |
| HANA destekleniyor | hayır | hayır | Evet<sup>1</sup> | evet | evet |
| Olası disk anlık görüntüleri | evet | evet | evet | hayır | evet |
| Kullanılabilirlik kümeleri kullanılırken farklı depolama kümelerinde disklerin ayrılması | yönetilen diskler aracılığıyla | yönetilen diskler aracılığıyla | yönetilen diskler aracılığıyla | kullanılabilirlik kümeleri aracılığıyla dağıtılan VM 'Ler ile disk türü desteklenmiyor | <sup>3</sup> yok |
| Kullanılabilirlik Alanları hizalı | evet | evet | evet | evet | Microsoft 'un katılımını gerektirir |
| Zikzak yedekliliği | yönetilen diskler için değil | yönetilen diskler için değil | yönetilen diskler için değil | hayır | hayır |
| Coğrafi artıklık | yönetilen diskler için değil | yönetilen diskler için değil | hayır | hayır | hayır |


günlük/Mv2 günlük birimlerine yönelik olarak d/VM aileleri için [Azure yazma Hızlandırıcısı](../../how-to-enable-write-accelerator.md) kullanımı ile <sup>1</sup>

<sup>2</sup> maliyet, sağlanan IOPS ve aktarım hızına bağlıdır

<sup>3</sup> farklı ANF kapasite havuzlarının oluşturulması, kapasite havuzlarının farklı depolama birimlerine dağıtılmasını garantilemez


> [!IMPORTANT]
> Azure NetApp Files (ANF) kullanarak 1 milisaniyelik g/ç gecikme süresi elde etmek için, sanal makinelerinize doğru yerleşimi ve ANF tabanlı NFS paylaşımlarını düzenlemek üzere Microsoft ile çalışmanız gerekir. Şimdiye kadar, sanal makine tarafından dağıtılan ve ANF üzerinde barındırılan NFS birimleri arasında otomatik bir yakınlık sağlayan hiçbir mekanizma yoktur. Farklı Azure bölgelerinin farklı kurulumu verildiğinde, sanal makine ve NFS paylaşımının yakınlık halinde ayrılmadığından, eklenen ağ gecikmesi 1 milisaniyenin ötesinde g/ç gecikmesini gönderebilir.


> [!IMPORTANT]
> Şu anda sunulan Azure blok depolama tabanlı yönetilen disklerin hiçbiri veya Azure NetApp Files herhangi bir bölgesel veya coğrafi artıklık sunmamaktadır. Sonuç olarak, yüksek kullanılabilirlik ve olağanüstü durum kurtarma mimarlarınızın bu yönetilen diskler, NFS veya SMB paylaşımları için herhangi bir Azure yerel depolama çoğaltma türüne bağlı olmadığından emin olmanız gerekir.


## <a name="azure-premium-storage"></a>Azure Premium Depolama
Azure Premium SSD depolaması, şunları sağlamak için hedefle tanıtılmıştır:

* Düşük g/ç gecikme süresi
* IOPS ve aktarım hızı için SLA 'Lar
* G/ç gecikmede daha az değişkenlik

Bu tür bir depolama, DBMS iş yüklerini, düşük tek basamaklı milisaniyelik gecikme süresi gerektiren depolama trafiğini ve Azure Premium Depolama söz konusu disklerde saklanan gerçek veri hacmi değildir ve bu nedenle, disk içinde depolanan verilerin miktarından bağımsız olarak bu tür bir diskin boyut kategorisini hedefler. Ayrıca, [Premium SSD](../../disks-types.md#premium-ssd)makalesinde gösterilen boyut kategorilerine doğrudan eşlenmemiş Premium depolamada diskler oluşturabilirsiniz. Ekibinizle şu makaleye sahiptir:

- Depolama, aralıklar halinde düzenlenir. Örneğin, 513 GiB ile 1024 GiB kapasitesi arasındaki bir disk aynı özellikleri ve aynı aylık maliyetleri paylaşır
- GiB başına ıOPS, boyut kategorilerinde doğrusal olarak izlenmiyor. 32 GiB 'nin altındaki daha küçük diskler, GiB başına ıOPS ücretlerinden daha fazladır. 32 gib 'den 1024 GiB 'ye kadar olan diskler için gib başına ıOPS oranı GiB başına 4-5 ıOPS arasındadır. 32.767 GiB 'ye kadar büyük diskler için, GiB başına ıOPS ücreti 1 ' in altına gidiyor
- Bu depolama alanı için g/ç üretilen işi disk kategorisinin boyutuyla doğrusal değil. 65 GiB ve 128 GiB kapasitesi arasındaki kategori gibi daha küçük diskler için üretilen iş, 780KB/GiB ' dir. Bir 32.767 GiB diski gibi Extreme büyük diskler için üretilen iş, 28KB/GiB 'nin etrafında
- IOPS ve üretilen iş SLA 'Ları, diskin kapasitesi değişmeksizin değiştirilemez


SAP iş yükünün yetenek matrisi şöyle görünür:

| Özellik| Yorum| Notlar/bağlantılar | 
| --- | --- | --- | 
| İşletim sistemi temel VHD | uçtan | Tüm sistemler |
| Veri diski | uçtan | Tüm sistemler- [SAP HANA için özel](../../how-to-enable-write-accelerator.md) |
| SAP Genel aktarım dizini | EVET | [Desteklenir](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | uçtan | Tüm sistemler |
| Yedekleme depolama alanı | uçtan | yedeklemelerin kısa vadeli depolaması için |
| Paylaşımlar/paylaşılan disk | kullanılamaz | Azure Premium dosyaları veya üçüncü taraf gerekir |
| Dayanıklılık | LRS | Diskler için GRS veya ZRS yok |
| Gecikme süresi | düşük-orta | - |
| ıOPS SLA 'Sı | EVET | - |
| IOPS doğrusal-kapasiteye | köşeli ayraçlar içinde yarı doğrusal  | [Yönetilen disk fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Disk başına maksimum ıOPS | 20.000 [disk boyutuna bağımlı](https://azure.microsoft.com/pricing/details/managed-disks/) | [VM sınırlarını](../../sizes.md) de göz önünde bulundurun |
| Verimlilik SLA 'Sı | EVET | - |
| Doğrusal olarak kapasiteye işleme | köşeli ayraçlar içinde yarı doğrusal | [Yönetilen disk fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA sertifikalı | EVET | [SAP HANA için özel](../../how-to-enable-write-accelerator.md) |
| Olası disk anlık görüntüleri | EVET | - |
| Azure Backup VM anlık görüntülerini mümkün | EVET | [yazma Hızlandırıcısı](../../how-to-enable-write-accelerator.md) önbelleğe alınan diskler hariç  |
| Maliyetler | UZUNLUKTA | - |

Azure Premium Depolama, Azure Premium Depolama ile sunulan yaygın önbelleğe alma türleriyle SAP HANA depolama gecikme KPI 'lerini yerine getirmez. SAP HANA günlük yazma işlemleri için depolama gecikme KPI 'Larını karşılamak için, [yazma Hızlandırıcısı etkinleştirme](../../how-to-enable-write-accelerator.md)makalesinde açıklandığı gibi Azure yazma Hızlandırıcısı önbelleğe alma kullanmanız gerekir. Azure Yazma Hızlandırıcısı, işlem günlüğü yazmaları ve kayıt günlüğü yazmaları için diğer tüm DBMS sistemleri avantajlarına sahiptir. Bu nedenle, tüm SAP DBMS dağıtımları genelinde kullanılması önerilir. SAP HANA için Azure Yazma Hızlandırıcısı Azure Premium Depolama ile birlikte kullanımı zorunludur.



**Özet:** Azure Premium Depolama, SAP iş yükü için önerilen Azure depolama türlerinden biridir. Bu öneri üretim dışı ve üretim sistemleri için geçerlidir. Azure Premium Depolama, veritabanı iş yüklerini işlemeye uygundur. Azure Yazma Hızlandırıcısı kullanımı, Azure Premium disklerinde önemli ölçüde yazma gecikmesini artırmaktır. Ancak, yüksek ıOPS ve aktarım hızı fiyatları olan DBMS sistemlerinde, tek bir tarafta istenen kapasiteyi sağlayan dizili kümeler oluşturmak için veya Linux 'ta Windows depolama alanları veya mantıksal birim yöneticileri gibi işlevleri kullanmanız gerekir, ancak aynı zamanda gerekli ıOPS veya en iyi maliyet verimliliği olan işlem hacmi.


### <a name="azure-burst-functionality-for-premium-storage"></a>Premium Depolama için Azure patlama işlevselliği
Kapasiteye göre 512 GiB 'ye eşit veya daha küçük Azure Premium depolama diskleri için patlama işlevselliği sunulur. Disk patlaması 'nın nasıl çalıştığına tam olarak, [disk buralma](../../disk-bursting.md)makalesinde açıklanmaktadır. Makaleyi okurken, ıOPS ve aktarım hızı için g/ç iş yükünüzün, disklerin nominal ıOPS ve aktarım hızı altında olduğu durumlarda (nominal aktarım hızı ile ilgili ayrıntılar için bkz. [yönetilen disk fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks/)) tahakkuk etme kavramını anlamış olursunuz. Geçerli kullanımınız ile diskin nominal değerleri arasındaki ıOPS ve aktarım hızı Delta değerini tahakkuk eteceğiz. Artışlarıyla, en fazla 30 dakika ile sınırlıdır.

Bu veri bloğu işlevinin planlanabilecek ideal durumlar, büyük olasılıkla farklı DBMS için veri dosyaları içeren birimler veya diskler olacaktır. Özellikle küçük ve orta ölçekli sistemlerle bu birimlere karşı beklenen g/ç iş yükü, şöyle görünmelidir:

- Veriler ideal olarak bellekte önbelleğe alındığından veya HANA söz konusu olduğunda, tam olarak bellekte olması gerektiğinden, düşük ila orta okuma iş yükü
- Veritabanı denetim noktaları veya düzenli olarak verilen işlemi tarafından tetiklenen yazma burları
- Yedeklemelerin depolama anlık görüntüleri aracılığıyla Yürütülmeyen durumlarda sürekli bir akışta okuyan yedekleme iş yükü
- SAP HANA için, bir örnek yeniden başlatıldıktan sonra verilerin belleğe yüklenmesi

Özellikle iş yükünüzün yalnızca saniye başına birkaç yüz işlemi işlediği daha küçük DBMS sistemlerinde, bu tür bir veri bloğu işlevselliği, işlemi depolayan veya günlüğü yinelemek için gereken disk veya birimlere da anlam verebilir. Bu tür bir diskte veya birimlerde beklenen iş yükü şu şekilde görünür:

- Uygulama tarafından verilen her işleme bir g/ç işlemi tetikleyebilmesi için iş yüküne ve iş yükünün yapısına bağlı olan diske normal yazma işlemleri
- Dizin oluşturma veya yeniden oluşturma gibi işletimsel görevler için üretilen iş yükündeki daha yüksek iş yükü
- İşlem günlüğü gerçekleştirirken veya günlük yedeklerini yinelemek için artışlarıyla 'yi okuyun


## <a name="azure-ultra-disk"></a>Azure Ultra disk
Azure ultra diskler Azure IaaS VM'leri içi yüksek işleme hızı, yüksek IOPS ve tutarlı olarak düşük gecikme süresi getirir. Ultra disklerin bazı ek avantajları, sanal makinelerinizi (VM) yeniden başlatmanıza gerek kalmadan, iş yükleriniz ile birlikte, diskin ıOPS ve aktarım hızını dinamik olarak değiştirme imkanını içerir. Ultra diskler, SAP DBMS iş yükü gibi veri kullanımı yoğun iş yükleri için uygundur. Ultra diskler yalnızca veri diskleri olarak kullanılabilir ve işletim sistemini depolayan temel VHD diski olarak kullanılamaz. Temel VHD diski olarak Azure Premium depolama kullanımını öneririz.

Bir ultra disk oluştururken, şunları tanımlayabilmeniz için üç boyutun olması gerekir:

- Diskin kapasitesi. Aralıklar 4 GiB 'den 65.536 GiB 'ye kadar
- Disk için sağlanan ıOPS. Diskin kapasitesine göre farklı en büyük değerler geçerlidir. Daha fazla ayrıntı için [Ultra disk](../../disks-types.md#ultra-disk) makalesini okuyun
- Sağlanan depolama bant genişliği. Farklı en yüksek bant genişliği, diskin kapasitesine bağlı olarak geçerlidir. Daha fazla ayrıntı için [Ultra disk](../../disks-types.md#ultra-disk) makalesini okuyun

Tek bir diskin maliyeti, belirli diskler için ayrı olarak tanımlayabileceğiniz üç boyuta göre belirlenir. 


SAP iş yükünün yetenek matrisi şöyle görünür:

| Özellik| Yorum| Notlar/bağlantılar | 
| --- | --- | --- | 
| İşletim sistemi temel VHD | çalışmıyor | - |
| Veri diski | uçtan | Tüm sistemler  |
| SAP Genel aktarım dizini | EVET | [Desteklenir](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | uçtan | Tüm sistemler |
| Yedekleme depolama alanı | uçtan | yedeklemelerin kısa vadeli depolaması için |
| Paylaşımlar/paylaşılan disk | kullanılamaz | Üçüncü taraf gerekir |
| Dayanıklılık | LRS | Diskler için GRS veya ZRS yok |
| Gecikme süresi | çok düşük | - |
| ıOPS SLA 'Sı | EVET | - |
| IOPS doğrusal-kapasiteye | köşeli ayraçlar içinde yarı doğrusal  | [Yönetilen disk fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Disk başına maksimum ıOPS | 1.200 160.000 | disk kapasitesinden bağımsız |
| Verimlilik SLA 'Sı | EVET | - |
| Doğrusal olarak kapasiteye işleme | köşeli ayraçlar içinde yarı doğrusal | [Yönetilen disk fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA sertifikalı | EVET | - |
| Olası disk anlık görüntüleri | NO | - |
| Azure Backup VM anlık görüntülerini mümkün | NO | - |
| Maliyetler | Premium depolamadan daha yüksek | - |



**Özet:** Azure Ultra diskler, her türlü SAP iş yükü için düşük gecikme süresine sahip uygun bir depolardır. Şimdiye kadar, Ultra disk yalnızca Kullanılabilirlik Alanları (zonal dağıtım) ile dağıtılan VM 'lerle birlikte kullanılabilir. Bu noktada, Ultra disk depolama anlık görüntülerini destekmiyor. Diğer tüm depolamanın tersine, temel VHD diski için Ultra disk kullanılamaz. Ultra disk, g/ç iş yükünün büyük ölçüde dalgalandığı ve dağıtılan depolama aktarım hızını veya ıOPS 'yi maksimum bant genişliği ve ıOPS kullanımı yerine depolama iş yükü desenlerine uyarlamak istediğiniz durumlarda idealdir.


## <a name="azure-netapp-files-anf"></a>Azure NetApp dosyaları (ANF)
[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) , Azure Native NFS ve SMB paylaşımlarını yüksek performanslı bir şekilde gerçekleştirmek için Microsoft ve NetApp ile hedefe yönelik bir ortak işlemin sonucudur. Bu vurgu, DBMS dağıtım senaryolarını etkinleştiren yüksek bant genişliği ve düşük gecikmeli depolama sağlamak ve zaman içinde, Azure aracılığıyla NetApp depolamanın tipik işletimsel işlevlerini olanaklı kılmaktadır. NFS/SMB paylaşımları, depolama aktarım hızını ve fiyata ayırt eden üç farklı hizmet düzeyinde sunulur. Hizmet düzeyleri [Azure NetApp Files makale hizmet düzeylerinde](../../../azure-netapp-files/azure-netapp-files-service-levels.md)belgelenmiştir. Farklı SAP iş yükü türleri için aşağıdaki hizmet düzeyleri önerilir:

- SAP DBMS iş yükü: performans, ideal Ultra
- SAPMNT paylaşma: performans, ideal Ultra
- Genel aktarım dizini: performans, ideal Ultra

> [!NOTE]
> Minimum sağlama boyutu, kapasite havuzu olarak adlandırılan 4 TiB birimidir. Daha sonra bu kapasite havuzundan birimler oluşturursunuz. Öte yandan, oluşturabileceğiniz en küçük birim 100 GiB 'dir. Bir kapasite havuzunu TiB adımlarında genişletebilirsiniz. Fiyatlandırma için [Azure NetApp Files ücretlendirme](https://azure.microsoft.com/pricing/details/netapp/) makalesine bakın

ANF depolama Şu anda çeşitli SAP iş yükü senaryolarında desteklenmektedir:

- SAP 'nin Genel aktarım dizini için SMB veya NFS paylaşımları sağlama
- Yüksek kullanılabilirlik senaryolarında sapmnt paylaşma bölümünde belgelendiği gibi:
    - [SAP uygulamaları için Azure NetApp Files (SMB) ile Windows üzerinde Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik](./high-availability-guide-windows-netapp-files-smb.md)
    - [SAP uygulamaları için Azure NetApp Files SUSE Linux Enterprise Server üzerindeki Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik](./high-availability-guide-suse-netapp-files.md)
    - [SAP NetWeaver için Azure sanal makineler yüksek kullanılabilirlik, SAP uygulamaları için Azure NetApp Files Red Hat Enterprise Linux](./high-availability-guide-rhel-netapp-files.md)
- [Azure sanal makine depolama yapılandırması SAP HANA](./hana-vm-operations-storage.md) makalesinde belgelendiği gibi,/Hana/Data ve/Hana/Shared BIRIMLERINE yönelik NFS v 4.1 paylaşımlarını ve/veya NFS v 4.1 veya NFS v3 birimlerini kullanan SAP HANA dağıtımlar
- Oracle verileri için [Dnfs](https://docs.oracle.com/en/database/oracle/oracle-database/19/ntdbi/creating-an-oracle-database-on-direct-nfs.html#GUID-2A0CCBAB-9335-45A8-B8E3-7E8C4B889DEA) kullanan Oracle Linux konuk işletim sistemindeki Oracle dağıtımları ve günlük birimlerini yineleyin. Daha fazla ayrıntı [Için Azure sanal makineler Oracle DBMS dağıtımı for SAP iş yükü](./dbms_guide_oracle.md) makalesine ulaşabilirsiniz

> [!NOTE]
> Azure NetApp Files tabanlı NFS veya SMB paylaşımları için başka bir DBMS iş yükü desteklenmez. Güncelleştirmeler ve değişiklikler, bu değişiklik yapacaklıyordu.

Zaten Azure Premium depolamada olduğu gibi, üretilen iş için en az sayıda sayı üzerinde kalmak gerektiğinde, GB başına sabit veya doğrusal bir işleme boyutu bir sorun olabilir. Bunun gibi SAP HANA için büyük/küçük harf. ANF ile, bu sorun Azure Premium diskinden daha fazla olabilir. Azure Premium disk söz konusu olduğunda, GiB 'ye göre görece yüksek aktarım hızı ve bunların genelinde dizili ve daha düşük kapasitede daha yüksek aktarım hızı sunan birkaç küçük disk alabilirsiniz. Bu tür bir dizme, ANF üzerinde barındırılan NFS veya SMB paylaşımları için çalışmaz. Bu kısıtlama, şunun gibi fazla kapasitenin dağıtımına neden oldu:

- Örneğin, ANF üzerinde barındırılan bir NFS biriminde 250 MiB/sn aktarım hızı elde etmek için, Ultra hizmet düzeyinin 1,95 TiB kapasitesini dağıtmanız gerekir. 
- 400 MIB/sn elde etmek için 3,125 TiB kapasitesini dağıtmanız gerekir. Ancak, birim için ihtiyaç duyduğunuz aktarım hızını elde etmek üzere kapasitenin aşırı sağlanması gerekebilir. Bu aşırı kapasite sağlama, daha küçük HANA örneklerinin fiyatlandırmasını etkiler. 
- SAP/sapmnt dizini için ANF 'nin üzerinde NFS kullanma alanında, genellikle Azure NetApp Files tarafından zorlanan 100 GiB 150 'ye kadar olan minimum kapasiteye sahip olursunuz. Ancak müşteri deneyimi, 12,8 MIB/sn 'nin (Ultra hizmet düzeyi kullanılarak) ilgili üretilen iş hızının yeterince olmamasından ve SAP sisteminin kararlılığı üzerinde olumsuz bir etkiye sahip olabileceğini gösterdi. Bu gibi durumlarda, müşteriler/sapmnt biriminin hacmini artırarak sorunları önleyerek bu birime daha fazla verimlilik sağlanır.

SAP iş yükünün yetenek matrisi şöyle görünür:

| Özellik| Yorum| Notlar/bağlantılar | 
| --- | --- | --- | 
| İşletim sistemi temel VHD | çalışmıyor | - |
| Veri diski | uçtan | Yalnızca SAP HANA  |
| SAP Genel aktarım dizini | EVET | SMB ve NFS |
| SAP sapmnt | uçtan | Tüm sistemler SMB (yalnızca Windows) veya NFS (yalnızca Linux) |
| Yedekleme depolama alanı | uçtan | - |
| Paylaşımlar/paylaşılan disk | EVET | SMB 3,0, NFS v3 ve NFS v 4.1 |
| Dayanıklılık | LRS | Diskler için GRS veya ZRS yok |
| Gecikme süresi | çok düşük | - |
| ıOPS SLA 'Sı | EVET | - |
| IOPS doğrusal-kapasiteye | Kesinlikle doğrusal  | [Hizmet düzeyine](../../../azure-netapp-files/azure-netapp-files-service-levels.md) bağımlı |
| Verimlilik SLA 'Sı | EVET | - |
| Doğrusal olarak kapasiteye işleme | köşeli ayraçlar içinde yarı doğrusal | [Hizmet düzeyine](../../../azure-netapp-files/azure-netapp-files-service-levels.md) bağımlı |
| HANA sertifikalı | EVET | - |
| Olası disk anlık görüntüleri | EVET | - |
| Azure Backup VM anlık görüntülerini mümkün | NO | - |
| Maliyetler | Premium depolamadan daha yüksek | - |


ANF depolamanın ek yerleşik işlevleri:

- Birimin anlık görüntülerini gerçekleştirme yeteneği
- Anlık görüntülerden ANF birimlerinin kopyalanması
- Birimleri anlık görüntülerden geri yükle (yaslama geri çevir)

**Özet**: Azure NetApp FILES, NFS ve SMB birimleri veya paylaşımları dağıtmaya izin veren, Hana sertifikalı düşük gecikmeli bir depodır. Depolama, birimin GiB kapasitesi başına doğrusal bir şekilde farklı verimlilik ve ıOPS sağlayan üç farklı hizmet düzeyiyle gelir. ANF depolaması, bekleyen bir düğümle SAP HANA genişleme senaryolarına dağıtım yapmak için etkinleştiriliyor. Depolama alanı,/sapmnt veya SAP Genel aktarım dizini için gerektiğinde dosya paylaşımları sağlamaya uygundur. ANF depolama, yerel NetApp işlevselliği olarak kullanılabilen işlev kullanılabilirliğiyle birlikte gelir.  



## <a name="azure-standard-ssd-storage"></a>Azure Standart SSD depolaması
Azure Standart HDD depolaması ile karşılaştırıldığında, Azure Standart SSD depolama, daha iyi kullanılabilirlik, tutarlılık, güvenilirlik ve gecikme sağlar. Düşük ıOPS düzeylerinde tutarlı performans gerektiren iş yükleri için optimize edilmiştir. Bu depolama, düşük ıOPS ve verimlilik taleplerine sahip olan, üretim dışı SAP sistemleri için kullanılan en düşük depolama alanı. SAP iş yükünün yetenek matrisi şöyle görünür:

| Özellik| Yorum| Notlar/bağlantılar | 
| --- | --- | --- | 
| İşletim sistemi temel VHD | kısıtlı uygun | Üretim dışı sistemler |
| Veri diski | kısıtlı uygun | düşük ıOPS ve gecikme süresi taleplerini içeren bazı üretim dışı sistemler |
| SAP Genel aktarım dizini | NO | [Desteklenmiyor](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | kısıtlı uygun | Üretim dışı sistemler |
| Yedekleme depolama alanı | uçtan | - |
| Paylaşımlar/paylaşılan disk | kullanılamaz | Üçüncü taraf gerekir |
| Dayanıklılık | LRS, GRS | Diskler için kullanılabilir ZRS yok |
| Gecikme süresi | yüksek | SAP Küresel aktarım dizini veya üretim sistemleri için çok yüksek |
| ıOPS SLA 'Sı | NO | - |
| Disk başına maksimum ıOPS | 500 | Diskin boyutundan bağımsız |
| Verimlilik SLA 'Sı | NO | - |
| HANA sertifikalı | NO | - |
| Olası disk anlık görüntüleri | EVET | - |
| Azure Backup VM anlık görüntülerini mümkün | EVET | - |
| Maliyetler | LOW | - |



**Özet:** Azure Standart SSD depolaması, temel VHD için üretim dışı VM 'Ler için en düşük önerinin yanı sıra göreli gecikme süresine sahip ve/veya düşük ıOPS ve/veya düşük ıOPS ve üretilen iş ücretleri Bu Azure depolama türü artık SAP Küresel aktarım dizinini barındırmak için desteklenmiyor. 



## <a name="azure-standard-hdd-storage"></a>Azure Standart HDD depolaması
Azure Standart HDD depolama, Azure altyapısı 2014 yılında SAP NetWeaver iş yükü için sertifikalı olduğunda tek depolama türüdür. 2014 yılında Azure sanal makineleri, depolama aktarım hızı az ve düşüktür. Bu nedenle, bu depolama türü yalnızca taleplerine devam edebilir. Depolama, gecikme süresi duyarlı olmayan iş yükleri için idealdir ve bu da SAP alanında deneyim yaşar. Azure VM 'lerinin arttırılarak ve bu VM 'Lerin arttığı iş yükü arttıkça, bu depolama türü artık SAP senaryolarıyla kullanım için kabul edilmez. SAP iş yükünün yetenek matrisi şöyle görünür:

| Özellik| Yorum| Notlar/bağlantılar | 
| --- | --- | --- | 
| İşletim sistemi temel VHD | uygun değil | - |
| Veri diski | uygun değil | - |
| SAP Genel aktarım dizini | NO | [Desteklenmiyor](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | NO | Desteklenmez |
| Yedekleme depolama alanı | uçtan | - |
| Paylaşımlar/paylaşılan disk | kullanılamaz | Azure dosyaları veya üçüncü taraf gerekir |
| Dayanıklılık | LRS, GRS | Diskler için kullanılabilir ZRS yok |
| Gecikme süresi | yüksek | DBMS kullanımı, SAP Global aktarım dizini veya sapmnt/saploc için çok yüksek |
| ıOPS SLA 'Sı | NO | - |
| Disk başına maksimum ıOPS | 500 | Diskin boyutundan bağımsız |
| Verimlilik SLA 'Sı | NO | - |
| HANA sertifikalı | NO | - |
| Olası disk anlık görüntüleri | EVET | - |
| Azure Backup VM anlık görüntülerini mümkün | EVET | - |
| Maliyetler | LOW | - |



**Özet:** Standart HDD, yalnızca SAP yedeklemelerini depolamak için kullanılması gereken bir Azure depolama türüdür. Burada verileri aramak için kullanılan kullanımdan kaldırılan sistemler gibi, etkin olmayan sistemler için yalnızca temel VHD olarak kullanılmalıdır. Ancak etkin bir geliştirme, QA veya üretim VM 'Leri bu depolama alanını temel almalıdır. Ya da bu depolama üzerinde barındırılmakta olan veritabanı dosyaları


## <a name="azure-vm-limits-in-storage-traffic"></a>Depolama trafiğinden Azure VM sınırları
Şirket içi senaryolara karşı, seçtiğiniz tek VM türü, elde ettiğiniz depolama bant genişliği için önemli bir rol oynar. Farklı depolama türleri için şunları göz önünde bulundurmanız gerekir:

| Depolama türü| Linux | Windows | Yorumlar |
| --- | --- | --- | --- |
| Standart HDD | [Azure 'da Linux VM 'Leri için Boyutlar](../../sizes.md) | [Azure 'da Windows VM 'Leri için Boyutlar](../../sizes.md) | Orta veya büyük VM 'lerin depolama sınırlarına dokunmaya çok zor olabilir |
| Standart SSD | [Azure 'da Linux VM 'Leri için Boyutlar](../../sizes.md) | [Azure 'da Windows VM 'Leri için Boyutlar](../../sizes.md) | Orta veya büyük VM 'lerin depolama sınırlarına dokunmaya çok zor olabilir |
| Premium Depolama | [Azure 'da Linux VM 'Leri için Boyutlar](../../sizes.md) | [Azure 'da Windows VM 'Leri için Boyutlar](../../sizes.md) | Depolama yapılandırmasıyla daha kolay isabet ıOPS veya depolama üretilen iş VM sınırları |
| Ultra disk depolaması | [Azure 'da Linux VM 'Leri için Boyutlar](../../sizes.md) | [Azure 'da Windows VM 'Leri için Boyutlar](../../sizes.md) | Depolama yapılandırmasıyla daha kolay isabet ıOPS veya depolama üretilen iş VM sınırları |
| Azure NetApp Files | [Azure 'da Linux VM 'Leri için Boyutlar](../../sizes.md) | [Azure 'da Windows VM 'Leri için Boyutlar](../../sizes.md) | Depolama trafiği, depolama bant genişliği değil, ağ verimlilik bant genişliği kullanıyor! |

Sınırlamalar olarak Şunlara göz önünde olabilirsiniz:

- Daha küçük olan VM, ekleyebileceğiniz daha az disk olur. Bu, ANF için geçerlidir. NFS veya SMB paylaşımlarını bağlayabileceğinizden, iliştirilebilecek paylaşılan birim sayısı sınırı ile karşılaşmazsınız
- VM 'Ler, Premium depolama diskleri ve ultra disklerle kolayca aşılacak g/ç aktarım hızı ve ıOPS sınırlarına sahiptir
- ANF ile, paylaşılan birimlere giden trafik, VM 'nin ağ bant genişliğini tükettiği ve depolama bant genişliği değil
- Çift basamaklı TiB kapasitesi alanında büyük NFS birimleri sayesinde, tek bir VM 'ye erişen bir birime erişen aktarım hızı, paylaşılan birimle etkileşime geçen tek bir oturum için Linux sınırlarına göre Plato 'ya gidiyor. 

Azure VM 'lerini bir SAP sisteminin yaşam döngüsünde ayarlarken, yeni ve daha büyük VM türünün ıOPS ve depolama verimlilik sınırlarını değerlendirmelisiniz. Bazı durumlarda, depolama yapılandırmasını Azure VM 'nin yeni özelliklerine ayarlamak da mantıklıdır. 


## <a name="striping-or-not-striping"></a>Şeritleme veya şeridi yok
Birden çok Azure diskinin daha büyük bir birimde bir dizi kümesi oluşturulması, tek tek disklerin ıOPS ve üretilen iş verimini tek bir birimde birikmesini sağlar. Yalnızca Azure Standart depolama ve Azure Premium Depolama için kullanılır. Aktarım hızını ve ıOPS 'yi bir diskin kapasitesinden bağımsız olarak yapılandırabileceğiniz Azure Ultra disk, Stripe kümelerinin kullanımını gerektirmez. NFS veya SMB tabanlı paylaşılan birimler şeritli olamaz. Azure Premium Depolama verimlilik ve ıOPS 'nin doğrusal olmayan doğası nedeniyle, büyük tek Azure Premium Depolama disklerinden aynı ıOPS ve aktarım hızı ile daha küçük kapasite sağlayabilirsiniz. Bu, Azure Premium Depolama kullanarak daha düşük maliyetli verimlilik veya ıOPS elde etme yöntemidir. Örneğin, iki P15 Premium Depolama diski arasında dizme size şu şekilde bir aktarım hızı sağlar: 

- 250 MIB/sn. Bu tür bir birimde 512 GiB kapasitesi vardır. Saniyede 250 MIB üretilen işi sağlayan tek bir diske sahip olmak istiyorsanız, 2 TiB kapasiteye sahip bir P40 disk seçmeniz gerekir. 
- 400 MIB/sn dört P10 Premium depolama diskini şeritleyerek Toplam 512 GiB kapasitesine sahip. Saniyede en az 500 MIB işleme içeren tek bir diske sahip olmak isterseniz, 8 TiB ile bir P60 Premium Depolama diski seçmeniz gerekir. Premium depolamanın maliyeti kapasiteye göre doğrusal olduğundan, dizme kullanarak maliyet tasarrufu sağlayabilirsiniz.

Bazı kuralların dizme üzerinde izlenmesi gerekir:

- Azure depolama, verileri gereksiz halde tutduğundan, sanal makine tarafından yapılandırılan depolama alanı kullanılmamalıdır
- Şerit kümesinin uygulandığı diskler, aynı boyutta olması gerekir

Birden çok daha küçük disk arasında dizme, Azure Premium Depolama kullanarak iyi bir fiyat/performans oranı elde etmenin en iyi yoludur. Şeritte ek dağıtım ve yönetim ek yükü olduğu anlaşıldı.

Belirli Stripe boyut önerileri için, [Azure sanal makine depolama yapılandırmalarının SAP HANA](./hana-vm-operations-storage.md)gıbı farklı DBMS belgelerini okuyun.




## <a name="next-steps"></a>Sonraki adımlar
Makaleleri okuyun:

- [SAP iş yükü için Azure sanal makineler DBMS dağıtımına yönelik konular](./dbms_guide_general.md)
- [SAP HANA Azure sanal makine depolama alanı yapılandırmaları](./hana-vm-operations-storage.md)
