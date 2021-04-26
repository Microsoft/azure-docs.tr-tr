---
title: Azure sanal makine depolama yapılandırmalarının SAP HANA | Microsoft Docs
description: SAP HANA dağıtılan VM için depolama önerileri.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure HANA, depolama Ultra disk, Premium Depolama
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/03/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0c0fbb1280fc2a7eaca1d97e7e016cf480873c8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666589"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure sanal makine depolama alanı yapılandırmaları

Azure, SAP HANA çalıştıran Azure sanal makineleri için uygun olan farklı türlerde depolama alanı sağlar. SAP HANA dağıtımlar listesi için kabul edilebilir **SAP HANA sertifikalı Azure Depolama türleri** : 

- Azure Premium SSD veya Premium Depolama 
- [Ultra disk](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Bu disk türleri hakkında bilgi edinmek için bkz. [SAP iş yükü Için Azure Depolama türleri](./planning-guide-storage.md) ve [bir disk türü seçme](../../disks-types.md)

Azure, Azure Standard ve Premium depolamada bulunan VHD 'ler için iki dağıtım yöntemi sunar. Azure blok depolama dağıtımları için [Azure yönetilen disk](https://azure.microsoft.com/services/managed-disks/) avantajlarından yararlanabilirsiniz. 

Depolama türlerinin bir listesi ve ıOPS ve depolama aktarım hızı içindeki SLA 'Lar için, [yönetilen diskler Için Azure belgelerini](https://azure.microsoft.com/pricing/details/managed-disks/)gözden geçirin.

> [!IMPORTANT]
> Seçilen Azure depolama türünden bağımsız olarak, bu depolama üzerinde kullanılan dosya sisteminin, belirli işletim sistemi ve DBMS için SAP tarafından desteklenmesi gerekir. [Sap destek notuna #2972496](https://launchpad.support.sap.com/#/notes/2972496) , SAP HANA dahil farklı işletim sistemleri ve veritabanları için desteklenen dosya sistemlerini listeler. Bu, tüm birimlerde geçerlidir SAP HANA her türlü görevi okumak ve yazmak için erişim sağlayabilir. Azure 'da SAP HANA için özel olarak NFS 'yi kullanmak, bu makalede daha sonra belirtilen şekilde NFS sürümlerinin ek kısıtlamaları için geçerlidir 


Farklı depolama türleri için en düşük SAP HANA sertifikalı koşullar şunlardır: 

- Azure Premium Storage- **/Hana/log** Azure [yazma Hızlandırıcısı](../../how-to-enable-write-accelerator.md)tarafından desteklenmelidir. **/Hana/Data** birimi Azure yazma hızlandırıcısı veya ultra disk olmadan Premium depolamaya yerleştirilebilecek
- En azından **/Hana/log** birimi Için Azure Ultra disk. **/Hana/Data** Volume, Azure yazma Hızlandırıcısı olmayan Premium depolamaya veya daha hızlı yeniden başlatma süreleriyle elde edilebilir.
- **NFS v 4.1** , **/Hana/log ve/Hana/Data** için Azure NetApp Files en üstünde bulunan birimleri. /Hana/Shared birimi NFS v3 veya NFS v 4.1 protokolünü kullanabilir

Bazı depolama türleri birleştirilebilir. Örneğin, **/Hana/Data** ' ı Premium depolamaya koymak mümkündür ve gereken düşük gecikme süresini elde etmek için **/Hana/log** , Ultra disk depolama alanına yerleştirilebilir. **/Hana/Data** için ANF 'yi temel alan bir birim kullanırsanız, **/Hana/log** birimi de ANF 'nin üzerindeki NFS 'yi temel alır. Birimlerden biri (/Hana/Data gibi) ve Azure Premium depolama ya da diğer birime yönelik Ultra disk ( **/Hana/log** gibi) IÇIN ANF üzerinde NFS kullanılması **desteklenmez**.

Şirket içi dünyada, genellikle g/ç alt sistemleri ve özellikleri hakkında dikkatli olmanız gerekiyordu. Bunun nedeni, Gereç satıcısının SAP HANA için en düşük depolama gereksinimlerinin karşılandığından emin olması gerekiyordu. Azure altyapısını kendiniz oluştururken, bu SAP tarafından verilen gereksinimlerin bazılarını bilmelisiniz. SAP 'nin öneren düşük aktarım hızı özelliklerinden bazıları şunlardır:

- 1 MB g/ç boyutları ile **/Hana/log** /250 MB/sn üzerinde okuma/yazma
- 16 MB ve 64 MB g/ç boyutları için **/Hana/Data** için en az 400 MB/sn etkinliğini okuyun
- 16 MB ve 64 MB g/ç boyutları ile **/Hana/Data** için en az 250 MB/sn etkinliği yazın

Yetersiz depolama gecikmesi DBMS sistemleri için kritik öneme sahip olduğu için, DBMS gibi SAP HANA, verileri bellekteki tut gibi. Depolama alanındaki kritik yol genellikle DBMS sistemlerinin işlem günlüğü yazmaları etrafında olur. Ayrıca, kilitlenme kurtarmasından sonra işlemi yazma veya bellek içi veri yükleme gibi işlemler önemli olabilir. Bu nedenle, **/Hana/Data** ve **/Hana/log** birimleri için Azure Premium Depolama, Ultra disk veya ANF 'den yararlanmak **zorunludur** . 


HANA için depolama yapılandırmanızı seçerken kullanabileceğiniz bazı temel ilkeler şöyle listelenebilir:

- [SAP iş yükü Için Azure depolama türlerini](./planning-guide-storage.md) temel alan depolama türüne karar verin ve [bir disk türü seçin](../../disks-types.md)
- VM için boyutlandırma veya karar verirken genel VM g/ç verimlilik ve ıOPS sınırları göz önünde bulundurun. Genel VM depolama aktarım hızı, [bellek için iyileştirilmiş sanal makine boyutlarında](../../sizes-memory.md) belgelenmiştir
- Depolama yapılandırmasına karar verirken, **/Hana/Data** Volume yapılandırmanızda VM 'nin Genel aktarım hızını aşmaya çalışın. Savepoints yazmak SAP HANA, g/ç agresif yayınlanıyor olabilir. Bir kayıt noktası yazarken **/Hana/Data** hacminin üretilen iş limitlerinin gönderimi kolayca mümkündür. **/Hana/Data** birimini oluşturan DISKLERINIZ, sanal makinenizin izin verdiğinden daha yüksek bir aktarım hızına sahip olursa, yazma noktası tarafından kullanılan aktarım hızı, yineleme günlüğü yazmaları için üretilen iş taleplerini kesintiye uğratan bir şekilde çalışabilir. Uygulama verimini etkileyebilecek bir durum


> [!IMPORTANT]
> Depolama yapılandırmalarına yönelik öneriler, ile başlamak için yönergeler olarak tasarlanmıştır. İş yükünü çalıştırma ve depolama kullanımı düzenlerini çözümleme, belirtilen tüm depolama bant genişliğini veya ıOPS 'yi kullanmıyorsanız emin olabilirsiniz. Depolama sırasında daha sonra yeniden boyutlandırmayı düşünebilirsiniz. Veya aksine, iş yükünüzün bu yapılandırmalarda önerilenden daha fazla depolama verimi olması gerekebilir. Sonuç olarak, daha fazla kapasite, ıOPS veya aktarım hızı dağıtmanız gerekebilir. Depolama kapasitesi gerekli, depolama gecikme süresi gerekli, depolama aktarım hızı ve ıOPS ve en az pahalı yapılandırma arasındaki geçiş alanında, Azure, sizin ve HANA iş yükünüz için doğru güvenliğin aşılmasına ve bu açığı fark etmek üzere farklı yeteneklere ve farklı fiyat noktalarına sahip yeterli sayıda farklı depolama türü sunar.


## <a name="stripe-sets-versus-sap-hana-data-volume-partitioning"></a>SAP HANA veri birimi bölümlemeye karşı dizili kümeler
Azure Premium depolamayı kullanarak, birden çok Azure diskinde **/Hana/Data** ve/veya **/Hana/log** birimini dizili en iyi fiyat/performans oranına sahip olabilirsiniz. Daha büyük disk birimlerini dağıtmak yerine, ıOPS veya üretilen iş verimini daha fazlasını sağlar. Şimdiye kadar bu, Linux 'un bir parçası olan LVM ve MDADDM birimi yöneticileriyle birlikte gerçekleştirildi. Diskleri dizme yöntemi, eski ve iyi bilinen bir yöntemdir. Bu şeritli birimler, ihtiyacınız olabilecek ıOPS veya işleme özelliklerine ulaştığından, bu Şeritli birimleri yönetme konusunda karmaşıklıkları ekler. Özellikle, birimlerin kapasiteye göre uzatılihtiyacı olan durumlarda. En azından **/Hana/Data** için SAP, birden çok Azure diskinde şeritleme ile aynı hedefi karşılayan alternatif bir yöntem sunmuştur. SAP HANA 2,0 SPS03 bu yana, HANA ındexserver, farklı Azure disklerinde bulunan birden çok HANA veri dosyasına g/ç etkinliğini şeritleyebiliyor. Bunun avantajı, farklı Azure disklerinde dizili bir birim oluşturma ve yönetme konusunda endişelenmeniz gerekmez. Veri birimi bölümlemenin SAP HANA işlevselliği bölümünde ayrıntılı olarak açıklanmıştır:

- [HANA Yönetici Kılavuzu](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog hakkında SAP HANA – veri birimlerini bölümleme](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP Note #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP Note #2700123](https://launchpad.support.sap.com/#/notes/2700123)

Ayrıntılar arasında okuma, bu işlevin kullanılmasıyla ilgili, birim Yöneticisi tabanlı dizili kümeler arasındaki karmaşıklıkları göz önüne alır. Ayrıca, HANA veri birimi bölümlemenin yalnızca Azure Premium depolama gibi Azure blok depolama için çalışmadığını fark edersiniz. Bu paylaşımların ıOPS veya işleme sınırlamaları olması durumunda, bu işlevselliği ve NFS paylaşımlarında Stripe ile de kullanabilirsiniz.  


## <a name="linux-io-scheduler-mode"></a>Linux g/ç zamanlayıcı modu
Linux 'ta birkaç farklı g/ç zamanlama modu vardır. Linux satıcıları ve SAP aracılığıyla sık görülen öneriler, disk birimleri için g/ç Zamanlayıcı modunu, **MQ-son** veya **Kyber** modundan, henüz SLES sapayarla profilleri tarafından yapılmadıysa **noop** (multiqueue) veya **hiçbiri için yok** (multiqueue) moduna yeniden yapılandırmadır. Ayrıntılara şu şekilde başvurulur: 

- [SAP Note #1984787](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP Note #2578899](https://launchpad.support.sap.com/#/notes/2578899) 
- [SLES 12 SP4'TE NoOp ayarı ile ilgili sorun](https://www.suse.com/support/kb/doc/?id=000019547)

Red hat üzerinde, farklı SAP uygulamaları için belirli ayar profilleri tarafından belirlenen ayarları bırakın.


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Azure n serisi sanal makineler için Premium Depolama ve Azure Yazma Hızlandırıcısı çözümleri
Azure Yazma Hızlandırıcısı, yalnızca Azure a serisi VM 'Leri için kullanılabilen bir işlevdir. Ad olduğu gibi, işlevselliğin amacı, Azure Premium depolamada yazma işlemleri için g/ç gecikmesini geliştirmedir. SAP HANA için, Yazma Hızlandırıcısı yalnızca **/Hana/log hacminde** kullanılması gerekir. Bu nedenle, **/Hana/Data** ve **/Hana/log** , Azure yazma Hızlandırıcısı yalnızca **/Hana/log** birimini desteklemeye yönelik ayrı birimlerdir. 

> [!IMPORTANT]
> Azure Premium Depolama kullanılırken, **/Hana/log** birimi için Azure [yazma Hızlandırıcısı](../../how-to-enable-write-accelerator.md) kullanımı zorunludur. Yazma Hızlandırıcısı Premium Depolama ve yalnızca d serisi ve Mv2-Series VM 'Ler için kullanılabilir. Yazma Hızlandırıcısı, Esv3 veya Edsv4 gibi diğer Azure VM aileleriyle birlikte çalışmıyor.

Aşağıdaki Azure Premium disklerine yönelik önbelleğe alma önerileri, şunun gibi SAP HANA için g/ç özelliklerinin olduğunu varsayar:

- HANA veri dosyalarında her türlü okuma iş yükü vardır. Özel durumlar, HANA örneğinin yeniden başlatıldıktan sonra veya veriler HANA 'ya yüklendiğinde büyük ölçekli g/ç 'dir. Veri dosyalarına karşı daha büyük bir okuma g/ç, HANA veritabanı yedeklemeleri olabilir. Sonuç olarak okuma önbelleği çoğu durumda çoğu zaman bu yana anlamlı değildir, tüm veri dosyası birimlerinin tamamen okunabilir olması gerekir. 
- Veri dosyalarına yönelik yazma, Hana işlemi ve Hana kilitlenme kurtarması temelinde artışlarıyla 'de yaşanır. İşlemi yazmak zaman uyumsuzdur ve herhangi bir Kullanıcı hareketini tutmaz. Kilitlenme kurtarması sırasında verilerin yazılması, sistemin tekrar hızlı yanıt vermesini sağlamak için performans açısından kritik öneme sahiptir. Ancak, kilitlenme kurtarması bunun yerine olağanüstü durumlar olmalıdır
- HANA yineleme dosyalarından her türlü okuma işlemi de vardır. Özel durumlar, işlem günlüğü yedeklemeleri, kilitlenme kurtarması veya bir HANA örneğinin yeniden başlatma aşamasında olan büyük g/ç aşamasıdır.  
- SAP HANA Yinele günlük dosyasında ana yük yazılır. İş yükünün yapısına bağlı olarak, 4 KB kadar küçük veya diğer durumlarda 1 MB veya daha fazla g/ç boyutunda g/ç olabilir. Yeniden yineleme SAP HANA için yazma gecikmesi performans açısından kritik öneme sahiptir.
- Tüm yazmamaların diskte güvenilir bir biçimde kalıcı olması gerekir

**Öneri: Bu gözlemlenen g/ç desenlerinin SAP HANA sonucu olarak, Azure Premium depolama kullanan farklı birimlerin önbelleğe alınması şöyle ayarlanmalıdır:**

- **/Hana/Data** -önbelleğe alma veya okuma önbelleği yok
- **/Hana/log** -önbelleğe alma-Azure yazma Hızlandırıcısı etkinleştirilmesi gereken, d ve Mv2-Series VM 'ler için özel durum 
- **/Hana/Shared** -okuma önbelleği
- **Işletim sistemi diski** -VM oluşturma sırasında Azure tarafından ayarlanan varsayılan önbelleğe alma işlemini değiştirmeyin


Çeşitli Azure Premium disklerinde dizili kümeler oluşturmak için LVM veya mdaddm kullanıyorsanız, dizili boyutları tanımlamanız gerekir. Bu boyutlar **/Hana/Data** ve **/Hana/log** arasında farklılık gösterir. **Öneri: bir öneri, öneriye göre kullanılacak şekilde belirlenir:**

- **/Hana/Data** IÇIN 256 KB
- **/Hana/log** IÇIN 64 KB

> [!NOTE]
> **/Hana/Data** için Şerit boyutu, daha güncel Linux sürümlerindeki müşteri deneyimlerini temel alarak 64 kb veya 128 kb Ile 256 KB arasında çağrı yapan önceki önerilerden değiştirilmiştir. 256 KB boyutundaki boyut biraz daha iyi performans sağlar. Ayrıca, daha büyük g/ç boyutları ile yeterli aktarım hızı sağlamak için **/Hana/log** ' ın 32 kb 'den 64 KB 'ye kadar olan önerilerini de değiştirdik.

> [!NOTE]
> Azure blok depolama, bir VHD 'nin üç görüntüsünü sakladığı için RAID birimlerini kullanarak artıklık düzeyi yapılandırmanız gerekmez. Azure Premium diskler içeren bir Stripe kümesinin kullanımı, yeterli ıOPS ve/veya g/ç üretilen işi sağlayan birimleri yapılandırmak içindir.

Bir dizi kümesi altındaki birçok Azure VHD birikimini, ıOPS ve depolama alanı işleme tarafında ackümülatif olur. Bu nedenle, 3 x P30 Azure Premium depolama diskinden bir Stripe kümesi yerleştirirseniz, tek bir Azure Premium Depolama P30 diskinin depolama aktarım hızını üç kez ve üç kez vermesi gerekir.

> [!IMPORTANT]
> Birden çok Azure Premium diskinde dizili kümeler oluşturmak için LVM veya mdaddm 'yi Volume Manager olarak kullanıyorsanız, üç SAP HANA FileSystems/Data,/log ve/Shared bir varsayılan veya kök birim grubuna yerleştirilmemelidir. Genellikle/Data,/log ve/sharediçin ayrı birim grupları oluşturmak üzere Linux satıcıları kılavuzunu izlemeniz önerilir.


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


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>Azure Premium Storage tabanlı üretim için önerilen depolama çözümü

> [!IMPORTANT]
> Azure n serisi sanal makineler için SAP HANA sertifikası, **/Hana/log** birimi Için yalnızca Azure yazma Hızlandırıcısı. Sonuç olarak, Azure n serisi sanal makinelerdeki dağıtım SAP HANA dağıtımları, **/Hana/log** birimi için Azure yazma hızlandırıcısı ile yapılandırılmalıdır.  

> [!NOTE]
> Azure Premium Depolama 'yı içeren senaryolarda, yapılandırmaya veri bloğu özelliklerini uygulamamız gerekir. Herhangi bir şekil veya form için depolama testi araçları kullanırken, [Azure Premium disk](../../disk-bursting.md) patlaması 'nın göz önünde bulundurularak çalışmasına dikkat edin. SAP HWCCT veya HCMT aracı aracılığıyla teslim edilen depolama testlerini çalıştırmak, testlerin bazıları birikerek topladığınız kredisi aşacağından tüm testlerin ölçütleri geçitireceğiz. Özellikle, tüm testler kesme olmadan sırayla çalışır.


> [!NOTE]
> Üretim senaryolarında, [ıAAS Için SAP BELGELERINDE](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)sap tarafından SAP HANA için belirli bir sanal makine türünün desteklenip desteklenmediğini kontrol edin.

**Öneri: üretim senaryolarında Azure Premium Depolama ile önerilen yapılandırma şöyle görünür:**

SAP **/Hana/Data** Volume yapılandırması:

| VM SKU | RAM | En çok, VM G/Ç<br /> Aktarım hızı | /Hana/Data | Sağlanan İşleme Hızı | Maksimum patlama performansı | IOPS | Veri bloğu ıOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14.000 |
| M32ls | 256 GiB | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14.000 |
| M64ls | 512 GiB | 1.000 MBps | 4 x P10 | 400 MBps | 680 MBps | 2.000 | 14.000 |
| M64s | 1.000 GiB | 1.000 MBps | 4 x P15 | 500 MBps | 680 MBps | 4.400 | 14.000 |
| M64ms | 1.750 GiB | 1.000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9.200 | 14.000 |  
| M128s | 2.000 GiB | 2.000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9.200| 14.000 | 
| M128ms | 3.800 GiB | 2.000 MBps | 4 x P30 | 800 MBps | gereksiz | 20.000 | gereksiz | 
| M208s_v2 | 2.850 GiB | 1.000 MBps | 4 x P30 | 800 MBps | gereksiz | 20.000| gereksiz | 
| M208ms_v2 | 5.700 GiB | 1.000 MBps | 4 x P40 | 1.000 MBps | gereksiz | 30.000 | gereksiz |
| M416s_v2 | 5.700 GiB | 2.000 MBps | 4 x P40 | 1.000 MBps | gereksiz | 30.000 | gereksiz |
| M416ms_v2 | 11.400 GiB | 2.000 MBps | 4 x P50 | 2.000 MBps | gereksiz | 30.000 | gereksiz |


**/Hana/log** birimi için. yapılandırma şöyle görünür:

| VM SKU | RAM | En çok, VM G/Ç<br /> Aktarım hızı | **/Hana/log** birimi | Sağlanan İşleme Hızı | Maksimum patlama performansı | IOPS | Veri bloğu ıOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1.500 | 10.500 | 
| M32ls | 256 GiB | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1.500 | 10.500 | 
| M64ls | 512 GiB | 1.000 MBps | 3 x P10 | 300 MBps | 510 MBps | 1.500 | 10.500 | 
| M64s | 1.000 GiB | 1.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 | 
| M64ms | 1.750 GiB | 1.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 |  
| M128s | 2.000 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500|  
| M128ms | 3.800 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 | 
| M208s_v2 | 2.850 GiB | 1.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 |  
| M208ms_v2 | 5.700 GiB | 1.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 |  
| M416s_v2 | 5.700 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 |  
| M416ms_v2 | 11.400 GiB | 2.000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3.300 | 10.500 | 


Diğer birimlerde yapılandırma şöyle görünür:

| VM SKU | RAM | En çok, VM G/Ç<br /> Aktarım hızı | /Hana/Shared | /root birimi | /usr/SAP |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MBps | 1 x P15 | 1 x P6 | 1 x P6 |
| M32ls | 256 GiB | 500 MBps |  1 x P15 | 1 x P6 | 1 x P6 |
| M64ls | 512 GiB | 1000 MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M64s | 1.000 GiB | 1.000 MBps | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms | 1.750 GiB | 1.000 MBps | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s | 2.000 GiB | 2.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms | 3.800 GiB | 2.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2.850 GiB | 1.000 MBps |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5.700 GiB | 1.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5.700 GiB | 2.000 MBps |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11.400 GiB | 2.000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 


Farklı önerilen birimler için depolama aktarım hızının, çalıştırmak istediğiniz iş yükünü karşılayıp karşılamadığını kontrol edin. İş yükü **/Hana/Data** ve **/Hana/log** için daha yüksek birimler gerektiriyorsa, Azure Premium Depolama VHD 'lerinin sayısını artırmanız gerekir. Listelenmiş olandan daha fazla VHD 'ye sahip bir birimi boyutlandırma, ıOPS 'yi ve g/ç verimini Azure sanal makine türü sınırları içinde arttırır.

Azure Yazma Hızlandırıcısı yalnızca [Azure yönetilen disklerle](https://azure.microsoft.com/services/managed-disks/)birlikte çalışmaktadır. Bu nedenle, en azından **/Hana/log** birimini oluşturan Azure Premium Depolama disklerinin yönetilen diskler olarak dağıtılması gerekir. [Yazma Hızlandırıcısı](../../how-to-enable-write-accelerator.md)makalesinde Azure yazma Hızlandırıcısı hakkında daha ayrıntılı yönergeler ve kısıtlamalar bulabilirsiniz.

Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series) ailesi ve [Edsv4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series)'Nin Hana sertifikalı VM 'leri için, **/Hana/Data** ve **/Hana/log** birimi için ANF gerekir. Ya da yalnızca **/Hana/log** birimi için Azure Premium depolama yerine Azure Ultra Disk Storage 'ı kullanmanız gerekir. Sonuç olarak, Azure Premium depolamada bulunan **/Hana/Data** birimi için yapılandırma şöyle görünebilir:

| VM SKU | RAM | En çok, VM G/Ç<br /> Aktarım hızı | /Hana/Data | Sağlanan İşleme Hızı | Maksimum patlama performansı | IOPS | Veri bloğu ıOPS |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MBps | 3 x P10 | 300 MBps | 510 MBps | 1.500 | 10.500 |
| E32ds_v4 | 256 GiB | 768 MBps | 3 x P10 |  300 MBps | 510 MBps | 1.500 | 10.500|
| E48ds_v4 | 384 GiB | 1.152 MBps | 3 x P15 |  375 MBps |510 MBps | 3.300  | 10.500 | 
| E64ds_v4 | 504 GiB | 1.200 MBps | 3 x P15 |  375 MBps | 510 MBps | 3.300 | 10.500 | 
| E64s_v3 | 432 GiB | 1.200 MB/s | 3 x P15 |  375 MBps | 510 MBps | 3.300 | 10.500 | 

Ultra disk üzerinde **/Hana/log** dahil diğer birimler için yapılandırma şu şekilde görünebilir:

| VM SKU | RAM | En çok, VM G/Ç<br /> Aktarım hızı | /Hana/log birimi | /Hana/log g/ç aktarım hızı | /Hana/log ıOPS | /Hana/Shared | /root birimi | /usr/SAP |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MBps | 80 GB | 250 MBps | 1.800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256 GiB | 768 MBps | 128 GB | 250 MBps | 1.800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384 GiB | 1.152 MBps | 192 GB | 250 MBps | 1.800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504 GiB | 1.200 MBps | 256 GB | 250 MBps | 1.800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432 GiB | 1.200 MBps | 220 GB | 250 MBps | 1.800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>SAP HANA için Azure Ultra disk depolama yapılandırması
Başka bir Azure depolama türüne [Azure Ultra disk](../../disks-types.md#ultra-disk)adı verilir. Şu ana kadar çok ve ultra disk sunulan Azure depolama arasındaki önemli fark, disk yeteneklerinin artık disk boyutuna bağlanmadığı bir isteklerdir. Müşteri olarak, bu özellikleri ultra disk için tanımlayabilirsiniz:

- 4 GiB ile 65.536 GiB arasında değişen bir disk boyutu
- IOPS, 100 ıOPS 'den 160K ıOPS 'ye (maksimum sanal makine türlerine göre değişir) göre Aralık
- 300 MB/sn 'den 2.000 MB/sn 'ye kadar depolama verimi

Ultra Disk size boyut, ıOPS ve disk aktarım hızı aralığınızı karşılayan tek bir disk tanımlama olanağı sağlar. IOP ve depolama verimlilik gereksinimlerini karşılayan birimler oluşturmak için Azure Premium Depolama 'nın en üstünde LVM veya MDADDM gibi mantıksal birim yöneticileri kullanmak yerine. Ultra disk ve Premium Depolama arasında bir yapılandırma karışımı çalıştırabilirsiniz. Sonuç olarak, Ultra disk kullanımını performans açısından kritik **/Hana/Data** ve **/Hana/log** birimlerine sınırlayabilir ve Azure Premium Depolama ile diğer birimleri kapsaymanıza olanak sağlayabilirsiniz.

Ultra disk 'in diğer avantajları Premium depolamaya kıyasla daha iyi okuma gecikmesi olabilir. Daha hızlı okuma gecikmesi, HANA başlangıç zamanlarını ve verilerin sonraki yükünü belleğe düşürmek istediğinizde avantajlara sahip olabilir. Ayrıca, HANA Savepoints yazarken Ultra disk depolamanın avantajları da keçeli olabilir. 

> [!NOTE]
> Ultra disk henüz tüm Azure bölgelerinde mevcut değildir ve henüz aşağıda listelenen tüm VM türlerini desteklemede. Ultra diskin kullanılabildiği ve hangi VM ailelerinin desteklendiği hakkında ayrıntılı bilgi için, [Azure 'da disk türlerinin kullanılabildiği](../../disks-types.md#ultra-disk)makaleye bakın.

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Saf Ultra disk yapılandırmasına sahip üretim tarafından önerilen depolama çözümü
Bu yapılandırmada, **/Hana/Data** ve **/Hana/log** birimlerini ayrı tutmanız gerekir. Önerilen değerler SAP 'nin SAP HANA ve depolama yapılandırmalarının VM türlerini, [SAP TDI depolaması teknik](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)penceresinde önerildiği şekilde onaylamak Için gereken KPI 'lerin dışına türetilir.

Öneriler genellikle bu makalenin önceki kısımlarında belirtilen SAP minimum gereksinimlerini aşır. Listelenen öneriler, SAP 'ye göre boyut önerileri ve farklı VM türlerinin sağladığı en fazla depolama hacmi arasında bir uzlaşmaya neden vardır.

> [!NOTE]
> Azure Ultra disk, bir diskin gigabayt kapasitesi başına en az 2 ıOPS zorlamaya zorlarken


| VM SKU | RAM | En çok, VM G/Ç<br /> Aktarım hızı | /Hana/Data birimi | /Hana/Data g/ç verimlilik | /Hana/Data ıOPS | /Hana/log birimi | /Hana/log g/ç aktarım hızı | /Hana/log ıOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/sn | 200 GB | 400 MBps | 2,500 | 80 GB | 250 MB | 1.800 |
| E32ds_v4 | 256 GiB | 768 MB/s | 300 GB | 400 MBps | 2,500 | 128 GB | 250 MBps | 1.800 |
| E48ds_v4 | 384 GiB | 1152 MB/s | 460 GB | 400 MBps | 3.000 | 192 GB | 250 MBps | 1.800 |
| E64ds_v4 | 504 GiB | 1200 MB/s | 610 GB | 400 MBps | 3.500 |  256 GB | 250 MBps | 1.800 |
| E64s_v3 | 432 GiB | 1.200 MB/s | 610 GB | 400 MBps | 3.500 | 220 GB | 250 MB | 1.800 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 2,500 | 96 GB | 250 MBps  | 1.800 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 2,500 | 256 GB | 250 MBps  | 1.800 |
| M64ls | 512 GiB | 1.000 MB/s | 620 GB | 400 MBps | 3.500 | 256 GB | 250 MBps  | 1.800 |
| M64s | 1.000 GiB | 1.000 MB/s |  1.200 GB | 600 MBps | 5.000 | 512 GB | 250 MBps  | 2,500 |
| M64ms | 1.750 GiB | 1.000 MB/s | 2.100 GB | 600 MBps | 5.000 | 512 GB | 250 MBps  | 2,500 |
| M128s | 2.000 GiB | 2.000 MB/s |2.400 GB | 750 MBps | 7.000 | 512 GB | 250 MBps  | 2,500 | 
| M128ms | 3.800 GiB | 2.000 MB/s | 4.800 GB | 750 MBps |9.600 | 512 GB | 250 MBps  | 2,500 | 
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 3.500 GB | 750 MBps | 7.000 | 512 GB | 250 MBps  | 2,500 | 
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 7.200 GB | 750 MBps | 14.400 | 512 GB | 250 MBps  | 2,500 | 
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 7.200 GB | 1.000 MBps | 14.400 | 512 GB | 400 MBps  | 4.000 | 
| M416ms_v2 | 11.400 GiB | 2.000 MB/s | 14.400 GB | 1.500 MBps | 28.800 | 512 GB | 400 MBps  | 4.000 |   

**Listelenen değerlerin başlangıç noktası olması amaçlanmıştır ve gerçek taleplere göre değerlendirilmesi gerekir.** Azure Ultra disk 'in avantajı, ıOPS ve aktarım hızı değerlerinin, VM 'yi kapatma veya sisteme uygulanan iş yükünü durdurma gerekmeden uyarlanabilme gereği elde edilebilir.   

> [!NOTE]
> Şimdiye kadar, Ultra disk depolaması olan depolama anlık görüntüleri kullanılabilir değildir. Bu, VM anlık görüntülerinin Azure Backup hizmetleriyle kullanımını engeller


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp Files NFS v 4.1 birimleri
HANA için ANF hakkında ayrıntılı bilgi için [Azure NetApp Files belge NFS v 4.1 birimlerini okuyun SAP HANA](./hana-vm-operations-netapp.md)




## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Azure Premium Depolama ile maliyet bilincine çözüm
Şimdiye kadar, bu belgede açıklanan Azure Premium depolama çözümü, [Azure n serisi sanal makineler için Premium Depolama ve azure yazma hızlandırıcısı ile Ilgili çözüm çözümleri](#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines) SAP HANA üretimde desteklenen senaryolara yöneliktir. Üretim destektable yapılandırmalarının özelliklerinden biri, SAP HANA verileri için birimlerin ayrılmasıdır ve günlüğü iki farklı birime geri kaydeder. Bu tür bir ayrımı nedeni, birimlerdeki iş yükü özelliklerinin farklı olması nedenidir. Bu, önerilen üretim yapılandırmalarına sahip olan, farklı türde önbelleğe alma veya hatta farklı Azure blok depolama türleri gerekli olabilir. Üretim dışı senaryolar için, üretim sistemlerine yönelik olarak gerçekleştirilen bazı önemli noktalar, üretim dışı olan daha düşük üretimden oluşan sistemlere uygulanmayabilir. Sonuç olarak, HANA verileri ve günlük birimi birleştirilebilir. Sonuç olarak, bazı küller ile sonuç olarak, üretim sistemleri için gerekli olan belirli aktarım hızını veya gecikmeli KPI 'Ları henüz karşılamıyor. Bu ortamlarda maliyetleri azaltmanın başka bir yönü de [Azure Standart SSD Storage](./planning-guide-storage.md#azure-standard-ssd-storage)kullanımı olabilir. Standart SSD veya Standart HDD Azure Storage ' ın seçilmesi,  [sanal makineler Için SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)makalesinde belgelendiği şekılde tek VM SLA 'larınız üzerinde etkili olduğunu unutmayın.

Bu tür yapılandırmalara daha az maliyetli bir alternatif şöyle görünebilir:


| VM SKU | RAM | En çok, VM G/Ç<br /> Aktarım hızı | /Hana/Data ve/Hana/log<br /> LVM veya MDADDM ile şeritli | /Hana/Shared | /root birimi | /usr/SAP | açıklamaları |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | 1 MS depolama gecikme süresi<sup>1</sup> ' den az olmaz |
| E16v3 | 128 GiB | 384 MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | Sanal makine türü HANA sertifikalı değil <br /> 1 MS depolama gecikme süresi<sup>1</sup> ' den az olmaz |
| M32ts | 192 GiB | 500 MB/s | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Birleşik veri ve günlük birimi için Yazma Hızlandırıcısı kullanmak, ıOPS oranını 5.000<sup>2</sup> olarak sınırlandırır |
| E20ds_v4 | 160 GiB | 480 MB/sn | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | 1 MS depolama gecikme süresi<sup>1</sup> ' den az olmaz |
| E32v3 | 256 GiB | 768 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Sanal makine türü HANA sertifikalı değil <br /> 1 MS depolama gecikme süresi<sup>1</sup> ' den az olmaz |
| E32ds_v4 | 256 GiB | 768 MBps | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 1 MS depolama gecikme süresi<sup>1</sup> ' den az olmaz |
| M32ls | 256 GiB | 500 MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | Birleşik veri ve günlük birimi için Yazma Hızlandırıcısı kullanmak, ıOPS oranını 5.000<sup>2</sup> olarak sınırlandırır |
| E48ds_v4 | 384 GiB | 1.152 MBps | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 1 MS depolama gecikme süresi<sup>1</sup> ' den az olmaz |
| E64v3 | 432 GiB | 1.200 MB/s | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 1 MS depolama gecikme süresi<sup>1</sup> ' den az olmaz |
| E64ds_v4 | 504 GiB | 1200 MB/s |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 1 MS depolama gecikme süresi<sup>1</sup> ' den az olmaz |
| M64ls | 512 GiB | 1.000 MB/s | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | Birleşik veri ve günlük birimi için Yazma Hızlandırıcısı kullanmak, ıOPS oranını 10.000<sup>2</sup> olarak sınırlandırır |
| M64s | 1.000 GiB | 1.000 MB/s | 7 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | Birleşik veri ve günlük birimi için Yazma Hızlandırıcısı kullanmak, ıOPS oranını 10.000<sup>2</sup> olarak sınırlandırır |
| M64ms | 1.750 GiB | 1.000 MB/s | 6 x P20 | 1 x E30 | 1 x E6 | 1 x E6 | Birleşik veri ve günlük birimi için Yazma Hızlandırıcısı kullanmak, ıOPS oranını 10.000<sup>2</sup> olarak sınırlandırır |
| M128s | 2.000 GiB | 2.000 MB/s |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | Birleşik veri ve günlük birimi için Yazma Hızlandırıcısı kullanmak, ıOPS oranını 20.000<sup>2</sup> olarak sınırlandırır |
| M208s_v2 | 2.850 GiB | 1.000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | Birleşik veri ve günlük birimi için Yazma Hızlandırıcısı kullanmak, ıOPS oranını 10.000<sup>2</sup> olarak sınırlandırır |
| M128ms | 3.800 GiB | 2.000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | Birleşik veri ve günlük birimi için Yazma Hızlandırıcısı kullanmak, ıOPS oranını 20.000<sup>2</sup> olarak sınırlandırır |
| M208ms_v2 | 5.700 GiB | 1.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | Birleşik veri ve günlük birimi için Yazma Hızlandırıcısı kullanmak, ıOPS oranını 10.000<sup>2</sup> olarak sınırlandırır |
| M416s_v2 | 5.700 GiB | 2.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | Birleşik veri ve günlük birimi için Yazma Hızlandırıcısı kullanmak, ıOPS oranını 20.000<sup>2</sup> olarak sınırlandırır |
| M416ms_v2 | 11400 GiB | 2.000 MB/s | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | Birleşik veri ve günlük birimi için Yazma Hızlandırıcısı kullanmak, ıOPS oranını 20.000<sup>2</sup> olarak sınırlandırır |


<sup>1</sup> [Azure yazma Hızlandırıcısı](../../how-to-enable-write-accelerator.md) , Ev4 ve Ev4 VM aileleri ile birlikte kullanılamaz. Azure Premium Depolama kullanmanın sonucu olarak, g/ç gecikmesi 1 MS 'den az olmaz

<sup>2</sup> VM ailesi [Azure yazma Hızlandırıcısı](../../how-to-enable-write-accelerator.md)destekler, ancak yazma hızlandırıcısının ıOPS sınırının disk yapılandırması IOPS özelliklerini sınırlayabileceği bir olasılık vardır

SAP HANA için veri ve günlük birimini birleştirme durumunda, şeritli birimi oluşturan diskler okuma önbelleği veya okuma/yazma önbelleği etkin olmamalıdır.

SAP ile sertifikalı olmayan ve bu nedenle [SAP HANA donanım dizininde](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)listelenmeyen sanal makine türleri vardır. Müşterilerin geri bildirimi, listelenen bazı sanal makine türlerinin bazı üretim dışı görevler için başarıyla kullanılmıştı.


## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için bkz.

- [Azure sanal makineleri Için yüksek kullanılabilirlik kılavuzu SAP HANA](./sap-hana-availability-overview.md).