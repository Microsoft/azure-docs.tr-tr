---
title: Azure sanal makine depolama yapılandırmalarının SAP HANA | Microsoft Docs
description: SAP HANA dağıtılan VM için depolama önerileri.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1faf6e4c9124d494507a124013d5fd8588f4b41b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934912"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure sanal makine depolama alanı yapılandırmaları

Azure, SAP HANA çalıştıran Azure sanal makineleri için uygun olan farklı türlerde depolama alanı sağlar. SAP HANA dağıtımlar listesi için kabul edilebilir **SAP HANA sertifikalı Azure Depolama türleri** : 

- Azure Premium SSD  
- [Ultra disk](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Bu disk türleri hakkında bilgi edinmek için [disk türünü seçme](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types) makalesine bakın

Azure, Azure Standard ve Premium depolamada bulunan VHD 'ler için iki dağıtım yöntemi sunar. Genel senaryo izin veriyorsa, [Azure yönetilen disk](https://azure.microsoft.com/services/managed-disks/) dağıtımlarından yararlanın. 

Depolama türlerinin bir listesi ve ıOPS ve depolama aktarım hızı içindeki SLA 'Lar için, [yönetilen diskler Için Azure belgelerini](https://azure.microsoft.com/pricing/details/managed-disks/)gözden geçirin.

Farklı depolama türleri için en düşük SAP HANA sertifikalı koşullar şunlardır: 

- Azure Premium SSD-/Hana/log Azure [yazma Hızlandırıcısı](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)önbelleğe alınması gerekir. /Hana/Data birimi Azure Yazma Hızlandırıcısı veya ultra disk olmadan Premium SSD yerleştirilebilecek
- En azından/Hana/log birimi için Azure Ultra disk. /Hana/Data Volume Premium SSD Azure Yazma Hızlandırıcısı olmadan veya daha hızlı yeniden başlatma süreleri Ultra disk elde etmek için eklenebilir
- **NFS v 4.1** ,/Hana/log **ve** /Hana/Dataiçin Azure NetApp Files en üstünde bulunan birimleri. /Hana/Shared birimi NFS v3 veya NFS v 4.1 protokolünü kullanabilir. NFS v 4.1 protokolü/Hana/Data ve/Hana/günlük birimleri için zorunludur.

Bazı depolama türleri birleştirilebilir. Örneğin,/Hana/Data ' ı Premium depolamaya koymak mümkündür ve gereken düşük gecikme süresini elde etmek için/Hana/log Ultra disk depolama alanına yerleştirilebilir. Ancak, örneğin,/Hana/Data için NFS birimlerinin karıştırılması ve/Hana/log için diğer sertifikalı depolama türlerinden birini kullanmanız önerilmez

Şirket içi dünyada, genellikle g/ç alt sistemleri ve özellikleri hakkında dikkatli olmanız gerekiyordu. Bunun nedeni, Gereç satıcısının SAP HANA için en düşük depolama gereksinimlerinin karşılandığından emin olması gerekiyordu. Azure altyapısını kendiniz oluştururken, bu gereksinimlerin bazılarını bilmelisiniz. İstenen en düşük aktarım hızı özelliklerinden bazıları şunlar için gerekenler:

- 1 MB g/ç boyutlarıyla 250 MB/sn 'nin **/Hana/log** tarihinde okuma/yazma özelliğini etkinleştirin
- 16 MB ve 64 MB g/ç boyutları için **/Hana/Data** için en az 400 MB/sn okuma etkinliğini etkinleştirin
- 16 MB ve 64 MB g/ç boyutları ile **/Hana/Data** için en az 250 MB/sn yazma etkinliğini etkinleştirin

Yetersiz depolama gecikmesi DBMS sistemleri için kritik öneme sahip olduğu için, DBMS gibi SAP HANA, verileri bellekteki tut gibi. Depolama alanındaki kritik yol genellikle DBMS sistemlerinin işlem günlüğü yazmaları etrafında olur. Ayrıca, kilitlenme kurtarmasından sonra işlemi yazma veya bellek içi veri yükleme gibi işlemler önemli olabilir. Bu nedenle, **/Hana/Data** ve **/Hana/log** birimleri için Azure Premium disklerinin yararlanmak **zorunludur** . SAP tarafından istenen en düşük **/Hana/log** ve **/Hana/Data** aktarım hızını elde etmek Için, birden çok Azure Premium Depolama diski üzerinden MDADDM veya LVM kullanarak bir RAID 0 oluşturmanız gerekir. Ve RAID birimlerini **/Hana/Data** ve **/Hana/log** birimleri olarak kullanın. 

**Öneri: RAID 0 için şerit boyutları olarak kullanım önerisi:**

- **/Hana/Data** IÇIN 64 kb veya 128 KB
- **/Hana/log** IÇIN 32 KB

> [!NOTE]
> Azure Premium ve standart depolama, bir VHD 'nin üç görüntüsünü tutadığından, RAID birimlerini kullanarak artıklık düzeyi yapılandırmanız gerekmez. RAID birimi kullanımı, yalnızca yeterli g/ç üretilen işi sağlayan birimleri yapılandırmak içindir.

Bir RAID 'in altındaki birçok Azure VHD birikimini, ıOPS ve depolama alanı işleme tarafında ackümülatif ' dir. Bu nedenle, 3 x P30 Azure Premium Depolama diski üzerinden bir RAID 0 yerleştirirseniz, tek bir Azure Premium Depolama P30 diskinin depolama aktarım hızını üç kez ve üç kez vermesi gerekir.

Ayrıca, bir VM 'yi boyutlandırdığınızda veya karar verirken genel VM g/ç verimini göz önünde bulundurun. Genel VM depolama verimi, bu makalede bellek için [iyileştirilmiş sanal makine boyutları](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)bölümünde belgelenmiştir.

## <a name="linux-io-scheduler-mode"></a>Linux g/ç zamanlayıcı modu
Linux 'ta birkaç farklı g/ç zamanlama modu vardır. Linux satıcıları ve SAP aracılığıyla sık görülen öneriler, disk birimleri için g/ç Zamanlayıcı modunu, **CFQ** modundan **noop moduna yeniden yapılandırmadır** . [SAP Note #1984798](https://launchpad.support.sap.com/#/notes/1984787)ayrıntılara başvurulur. 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Azure n serisi sanal makineler için Premium Depolama ve Azure Yazma Hızlandırıcısı çözümleri
Azure Yazma Hızlandırıcısı, yalnızca Azure a serisi VM 'Leri için kullanılabilen bir işlevdir. Ad olduğu gibi, işlevselliğin amacı, Azure Premium depolamada yazma işlemleri için g/ç gecikmesini geliştirmedir. SAP HANA için, Yazma Hızlandırıcısı yalnızca **/Hana/log hacminde** kullanılması gerekir. Bu nedenle, **/Hana/Data** ve **/Hana/log** , Azure yazma Hızlandırıcısı yalnızca **/Hana/log** birimini desteklemeye yönelik ayrı birimlerdir. 

> [!IMPORTANT]
> Azure Premium Depolama kullanılırken, Azure [yazma Hızlandırıcısı](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) veya/Hana/log birimi kullanımı zorunludur. Yazma Hızlandırıcısı yalnızca Premium Depolama ve yalnızca d serisi ve Mv2 serisi VM 'Ler için kullanılabilir.

Aşağıdaki önbelleğe alma önerileri, şunun gibi SAP HANA için g/ç özelliklerinin olduğunu varsayar:

- HANA veri dosyalarında her türlü okuma iş yükü vardır. Özel durumlar, HANA örneğinin yeniden başlatıldıktan sonra veya veriler HANA 'ya yüklendiğinde büyük ölçekli g/ç 'dir. Veri dosyalarına karşı daha büyük bir okuma g/ç, HANA veritabanı yedeklemeleri olabilir. Sonuç olarak okuma önbelleği çoğu durumda çoğu zaman bu yana anlamlı değildir, tüm veri dosyası birimlerinin tamamen okunabilir olması gerekir.
- Veri dosyalarına yönelik yazma, Hana işlemi ve Hana kilitlenme kurtarması temelinde artışlarıyla 'de yaşanır. İşlemi yazmak zaman uyumsuzdur ve herhangi bir Kullanıcı hareketini tutmaz. Kilitlenme kurtarması sırasında verilerin yazılması, sistemin tekrar hızlı yanıt vermesini sağlamak için performans açısından kritik öneme sahiptir. Ancak, kilitlenme kurtarması bunun yerine olağanüstü durumlar olmalıdır
- HANA yineleme dosyalarından her türlü okuma işlemi de vardır. Özel durumlar, işlem günlüğü yedeklemeleri, kilitlenme kurtarması veya bir HANA örneğinin yeniden başlatma aşamasında olan büyük g/ç aşamasıdır.  
- SAP HANA Yinele günlük dosyasında ana yük yazılır. İş yükünün yapısına bağlı olarak, 4 KB kadar küçük veya diğer durumlarda 1 MB veya daha fazla g/ç boyutunda g/ç olabilir. Yeniden yineleme SAP HANA için yazma gecikmesi performans açısından kritik öneme sahiptir.
- Tüm yazmamaların diskte güvenilir bir biçimde kalıcı olması gerekir

**Öneri: Bu gözlemlenen g/ç desenlerinin SAP HANA sonucu olarak, Azure Premium depolama kullanan farklı birimlerin önbelleğe alınması şöyle ayarlanmalıdır:**

- **/Hana/Data** -önbelleğe alma yok
- **/Hana/log** -önbelleğe alma-yazma hızlandırıcısı, No. ve Mv2-Series için özel durum; burada, okuma önbelleği olmadan etkinleştirilmelidir. 
- **/Hana/Shared** -okuma önbelleği

### <a name="production-recommended-storage-solution"></a>Üretim için önerilen depolama çözümü

> [!IMPORTANT]
> Azure n serisi sanal makineler için SAP HANA sertifikası, **/Hana/log** birimi Için yalnızca Azure yazma Hızlandırıcısı. Sonuç olarak, Azure n serisi sanal makinelerdeki dağıtım SAP HANA dağıtımları, **/Hana/log** birimi için Azure yazma hızlandırıcısı ile yapılandırılmalıdır.  

> [!NOTE]
> Üretim senaryolarında, [ıAAS Için SAP BELGELERINDE](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)sap tarafından SAP HANA için belirli bir sanal makine türünün desteklenip desteklenmediğini kontrol edin.



**Öneri: üretim senaryolarında önerilen yapılandırma şöyle görünür:**

| VM SKU 'SU | RAM | En çok, VM G/Ç<br /> İşleme | /Hana/Data | /Hana/log | /Hana/Shared | /root birimi | /usr/SAP | Hana/yedekleme |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

M416xx_v2 VM türleri henüz Microsoft tarafından genel kullanıma açık hale getirilmez. Farklı önerilen birimler için depolama aktarım hızının, çalıştırmak istediğiniz iş yükünü karşılayıp karşılamadığını kontrol edin. İş yükü **/Hana/Data** ve **/Hana/log**için daha yüksek birimler gerektiriyorsa, Azure Premium Depolama VHD 'lerinin sayısını artırmanız gerekir. Listelenmiş olandan daha fazla VHD 'ye sahip bir birimi boyutlandırma, ıOPS 'yi ve g/ç verimini Azure sanal makine türü sınırları içinde arttırır.

Azure Yazma Hızlandırıcısı yalnızca [Azure yönetilen disklerle](https://azure.microsoft.com/services/managed-disks/)birlikte çalışmaktadır. Bu nedenle, en azından **/Hana/log** birimini oluşturan Azure Premium Depolama disklerinin yönetilen diskler olarak dağıtılması gerekir.

Azure Yazma Hızlandırıcısı tarafından desteklenebilir sanal makine başına Azure Premium Depolama VHD 'lerinin sınırları vardır. Geçerli sınırlar şunlardır:

- M128xx ve M416xx VM için 16 VHD
- M64xx ve M208xx VM için 8 VHD
- M32xx VM için 4 VHD

Azure Yazma Hızlandırıcısı 'in nasıl etkinleştirileceği hakkında daha ayrıntılı yönergeler [yazma Hızlandırıcısı](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)makalesinde bulunabilir.

Azure Yazma Hızlandırıcısı yönelik ayrıntılar ve kısıtlamalar aynı belgelerde bulunabilir.

**Öneri:/Hana/log birimini oluşturan diskler için Yazma Hızlandırıcısı kullanmanız gerekir**


### <a name="cost-conscious-azure-storage-configuration"></a>Maliyet bilincine sahip Azure depolama yapılandırması
Aşağıdaki tabloda, müşterilerin Azure VM 'lerinde SAP HANA barındırmak için de kullandığı VM türlerinin bir yapılandırması gösterilmektedir. SAP HANA için en düşük depolama ölçütlerine uymayan bazı sanal makine türleri olabilir veya SAP tarafından SAP HANA ile resmi olarak desteklenmez. Ancak şimdiye kadar bu VM 'Ler, üretim dışı senaryolar için iyi bir şekilde gerçekleştirdi. **/Hana/Data** ve **/Hana/log** , tek bir birimde birleştirilir. Sonuç olarak, Azure Yazma Hızlandırıcısı kullanımı ıOPS cinsinden bir sınırlama haline gelebilir.

> [!NOTE]
> SAP tarafından desteklenen senaryolar için, [ıAAS Için SAP BELGELERINDE](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)sap tarafından SAP HANA için belirli bir sanal makine türünün desteklenip desteklenmediğini kontrol edin.

> [!NOTE]
> Maliyet açısından önem veren bir çözüme yönelik önceki önerilerden bir değişiklik, [azure standart HDD disklerinden](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) daha iyi performans ve daha güvenilir [Azure Standart SSD disklerine](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) geçiş yapmak için kullanılır


| VM SKU 'SU | RAM | En çok, VM G/Ç<br /> İşleme | /Hana/Data ve/Hana/log<br /> LVM veya MDADDM ile şeritli | /Hana/Shared | /root birimi | /usr/SAP | Hana/yedekleme |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1\.200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2\.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1\.000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1\.000 GiB | 1\.000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1\.750 GiB | 1\.000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2\.000 GiB | 2\.000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3\.800 GiB | 2\.000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2\.000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


M416xx_v2 VM türleri henüz Microsoft tarafından genel kullanıma açık hale getirilmez. 3 x P20 daha küçük sanal makine türleri için önerilen diskler, [SAP TDI depolama teknik incelemesine](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)göre boşluk önerileriyle ilgili birimleri fazla boyuta göre daha fazla boyuta sahiptir. Ancak tabloda gösterildiği gibi seçim, SAP HANA için yeterli disk işleme sağlamak üzere yapılmıştır. İki kat daha fazla bellek birimini temsil eden yedeklemeleri korumak için boyutlandırılmış olan **/Hana/Backup** biriminde değişiklikler yapmanız gerekiyorsa, ayarlamayı ücretsiz olarak kullanabilirsiniz.   
Farklı önerilen birimler için depolama aktarım hızının, çalıştırmak istediğiniz iş yükünü karşılayıp karşılamadığını kontrol edin. İş yükü **/Hana/Data** ve **/Hana/log**için daha yüksek birimler gerektiriyorsa, Azure Premium Depolama VHD 'lerinin sayısını artırmanız gerekir. Listelenmiş olandan daha fazla VHD 'ye sahip bir birimi boyutlandırma, ıOPS 'yi ve g/ç verimini Azure sanal makine türü sınırları içinde arttırır. 

> [!NOTE]
> Yukarıdaki yapılandırmaların Azure [sanal makine tek VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) 'Sından bir Azure Premium Depolama ve Azure Standart depolama alanı karışımını kullandığından, bu yapılandırma avantajına sahip olmaz. Ancak, maliyetleri iyileştirmek için seçim seçilmiş. VM yapılandırmasını Azure tek VM SLA 'Sı ile uyumlu hale getirmek için Azure Standart depolama (SXX) olarak listelenen tüm diskler için Premium Depolama alanını seçmeniz gerekir.


> [!NOTE]
> Belirtilen disk yapılandırması önerileri, en düşük gereksinimleri SAP 'nin altyapı sağlayıcıları doğrultusunda ifade eder. Gerçek müşteri dağıtımları ve iş yükü senaryolarında, Bu önerilerin hala yeterli yetenekleri sağlamadığı durumlarla karşılaşıldı. Bu durum, bir müşterinin bir HANA yeniden başlatıldıktan sonra verilerin daha hızlı bir şekilde yeniden yüklenmesini gerektiren veya yedekleme yapılandırmalarının depolama için daha yüksek bant genişliğine sahip olduğu durumlar olabilir. Diğer durumlarda, 5000 ıOPS 'nin belirli iş yükü için yeterli olmadığı **/Hana/log** dahil değildir. Bu nedenle, bu önerileri bir başlangıç noktası olarak götürün ve iş yükünün gereksinimlerine göre uyarlayın.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>SAP HANA için Azure Ultra disk depolama yapılandırması
Microsoft, [Azure Ultra disk](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)adlı yeni bir Azure depolama türü kullanıma sunuyoruz. Şu ana kadar çok ve ultra disk sunulan Azure depolama arasındaki önemli fark, disk yeteneklerinin artık disk boyutuna bağlanmadığı bir isteklerdir. Müşteri olarak, bu özellikleri ultra disk için tanımlayabilirsiniz:

- 4 GiB ile 65.536 GiB arasında değişen bir disk boyutu
- IOPS, 100 ıOPS 'den 160K ıOPS 'ye (maksimum sanal makine türlerine göre değişir) göre Aralık
- 300 MB/sn 'den 2.000 MB/sn 'ye kadar depolama verimi

Ultra Disk size boyut, ıOPS ve disk aktarım hızı aralığınızı karşılayan tek bir disk tanımlama olanağı sağlar. IOP ve depolama verimlilik gereksinimlerini karşılayan birimler oluşturmak için Azure Premium Depolama 'nın en üstünde LVM veya MDADDM gibi mantıksal birim yöneticileri kullanmak yerine. Ultra disk ve Premium Depolama arasında bir yapılandırma karışımı çalıştırabilirsiniz. Sonuç olarak, Ultra disk kullanımını performans açısından kritik/Hana/Data ve/Hana/log birimlerine sınırlayabilir ve Azure Premium Depolama ile diğer birimleri kapsaymanıza olanak sağlayabilirsiniz.

Ultra disk 'in diğer avantajları Premium depolamaya kıyasla daha iyi okuma gecikmesi olabilir. Daha hızlı okuma gecikmesi, HANA başlangıç zamanlarını ve verilerin sonraki yükünü belleğe daha azaltmak istediğinizde avantajlara sahip olabilir. Ayrıca, HANA Savepoints yazarken Ultra disk depolamanın avantajları da keçeli olabilir. /Hana/Data için Premium Depolama disklerinin genellikle önbelleğe Yazma Hızlandırıcısı olmadığından, Premium depolamada bulunan/Hana/Data 'ya yönelik yazma gecikmesi, Ultra diskle karşılaştırıldığında daha yüksektir. Ultra disk ile yazılan kayıt noktası Ultra disk üzerinde daha iyi bir şekilde gerçekleştiriliyor olabilir.

> [!IMPORTANT]
> Ultra disk henüz tüm Azure bölgelerinde mevcut değildir ve henüz aşağıda listelenen tüm VM türlerini desteklemede. Ultra diskin kullanılabildiği ve hangi VM ailelerinin desteklendiği hakkında ayrıntılı bilgi için, [Azure 'da disk türlerinin kullanılabildiği](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)makaleye bakın.

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Saf Ultra disk yapılandırmasına sahip üretim tarafından önerilen depolama çözümü
Bu yapılandırmada,/Hana/Data ve/Hana/log birimlerini ayrı tutmanız gerekir. Önerilen değerler SAP 'nin SAP HANA ve depolama yapılandırmalarının VM türlerini, [SAP TDI depolaması teknik](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)penceresinde önerildiği şekilde onaylamak Için gereken KPI 'lerin dışına türetilir.

| VM SKU 'SU | RAM | En çok, VM G/Ç<br /> İşleme | /Hana/Data birimi | /Hana/Data g/ç verimlilik | /Hana/Data ıOPS | /Hana/log birimi | /Hana/log g/ç aktarım hızı | /Hana/log ıOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 600 GB | 700 MBps | 7\.500 | 512 GB | 500 MBps  | 2\.000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 400 MBps | 7\.500 | 256 GB | 250 MBps  | 2\.000 |
| M32ls | 256 GiB | 500 MB/s | 300 GB | 400 MBps | 7\.500 | 256 GB | 250 MBps  | 2\.000 |
| M64ls | 512 GiB | 1\.000 MB/s | 600 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2\.500 |
| M64s | 1\.000 GiB | 1\.000 MB/s |  1\.200 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2\.500 |
| M64ms | 1\.750 GiB | 1\.000 MB/s | 2\.100 GB | 600 MBps | 7\.500 | 512 GB | 400 MBps  | 2\.500 |
| M128s | 2\.000 GiB | 2\.000 MB/s |2\.400 GB | 1\.200 MBps |9\.000 | 512 GB | 800 MBps  | 3\.000 | 
| M128ms | 3\.800 GiB | 2\.000 MB/s | 4\.800 GB | 1200 MBps |9\.000 | 512 GB | 800 MBps  | 3\.000 | 
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 3\.500 GB | 1\.000 MBps | 9\.000 | 512 GB | 400 MBps  | 2\.500 | 
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 7\.200 GB | 1\.000 MBps | 9\.000 | 512 GB | 400 MBps  | 2\.500 | 
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 7\.200 GB | 1\.500 MBps | 9\.000 | 512 GB | 800 MBps  | 3\.000 | 
| M416ms_v2 | 11.400 GiB | 2\.000 MB/s | 14.400 GB | 1\.500 MBps | 9\.000 | 512 GB | 800 MBps  | 3\.000 |   

M416xx_v2 VM türleri henüz Microsoft tarafından genel kullanıma açık hale getirilmez. Listelenen değerlerin başlangıç noktası olması amaçlanmıştır ve gerçek taleplere göre değerlendirilmesi gerekir. Azure Ultra disk 'in avantajı, ıOPS ve aktarım hızı değerlerinin, VM 'yi kapatma veya sisteme uygulanan iş yükünü durdurma gerekmeden uyarlanabilme gereği elde edilebilir.   

> [!NOTE]
> Şimdiye kadar, Ultra disk depolaması olan depolama anlık görüntüleri kullanılabilir değildir. Bu, VM anlık görüntülerinin Azure Backup hizmetleriyle kullanımını engeller

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>Saf Ultra disk yapılandırmasına sahip üretim tarafından önerilen depolama çözümü
Bu yapılandırmada, aynı disk üzerinde/Hana/Data ve/Hana/log birimleri olursunuz. Önerilen değerler SAP 'nin SAP HANA ve depolama yapılandırmalarının VM türlerini, [SAP TDI depolaması teknik](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)penceresinde önerildiği şekilde onaylamak Için gereken KPI 'lerin dışına türetilir.

| VM SKU 'SU | RAM | En çok, VM G/Ç<br /> İşleme | /Hana/Data ve/log için birim | /Hana/Data ve günlük g/ç performansı | /Hana/Data ve log ıOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1\.200 MB/s | 1\.200 GB | 1\.200 MBps | 9\.500 | 
| M32ts | 192 GiB | 500 MB/s | 512 GB | 400 MBps | 9\.500 | 
| M32ls | 256 GiB | 500 MB/s | 600 GB | 400 MBps | 9\.500 | 
| M64ls | 512 GiB | 1\.000 MB/s | 1\.100 GB | 900 MBps | 10,000 | 
| M64s | 1\.000 GiB | 1\.000 MB/s |  1\.700 GB | 900 MBps | 10,000 | 
| M64ms | 1\.750 GiB | 1\.000 MB/s | 2\.600 GB | 900 MBps | 10,000 | 
| M128s | 2\.000 GiB | 2\.000 MB/s |2\.900 GB | 1\.800 MBps |12.000 | 
| M128ms | 3\.800 GiB | 2\.000 MB/s | 5\.300 GB | 1\.800 MBps |12.000 |  
| M208s_v2 | 2\.850 GiB | 1\.000 MB/s | 4\.000 GB | 900 MBps | 10,000 |  
| M208ms_v2 | 5\.700 GiB | 1\.000 MB/s | 7\.700 GB | 900 MBps | 10,000 | 
| M416s_v2 | 5\.700 GiB | 2\.000 MB/s | 7\.700 GB | 1, 800MBps | 12.000 |  
| M416ms_v2 | 11.400 GiB | 2\.000 MB/s | 15.000 GB | 1\.800 MBps | 12.000 |    

M416xx_v2 VM türleri henüz Microsoft tarafından genel kullanıma açık hale getirilmez. Listelenen değerlerin başlangıç noktası olması amaçlanmıştır ve gerçek taleplere göre değerlendirilmesi gerekir. Azure Ultra disk 'in avantajı, ıOPS ve aktarım hızı değerlerinin, VM 'yi kapatma veya sisteme uygulanan iş yükünü durdurma gerekmeden uyarlanabilme gereği elde edilebilir.  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp Files NFS v 4.1 birimleri
Azure NetApp Files/Hana/Shared,/Hana/Data ve/Hana/log birimleri için kullanılabilen yerel NFS paylaşımları sağlar. /Hana/Data ve/Hana/log birimleri için ANF tabanlı NFS paylaşımlarının kullanılması, v 4.1 NFS protokolünün kullanımını gerektirir. NFS Protokolü v3, paylaşımları ANF üzerinde dayandırırken/Hana/Data ve/Hana/log birimlerinin kullanımı için desteklenmez. 

> [!IMPORTANT]
> Azure NetApp Files uygulanan NFS v3 protokolünün/Hana/Data ve/Hana/log için kullanılması desteklenmez. NFS 4,1 kullanımı,/Hana/Data ve/Hana/log birimleri için bir işlev noktasından bir görünüm açısından zorunludur. Öte yandan,/Hana/Shared birimi için NFS v3 veya NFS v 4.1 protokolü, bir görünüm noktasından kullanılabilir.

### <a name="important-considerations"></a>Önemli konular
SAP NetWeaver ve SAP HANA için Azure NetApp Files düşünürken, aşağıdaki önemli noktalara dikkat edin:

- En düşük kapasite havuzu 4 TiB 'dir.  
- En küçük birim boyutu 100 GiB 'dir
- Azure NetApp Files ve Azure NetApp Files birimlerinin takılabileceği tüm sanal makineler aynı bölgedeki aynı Azure sanal ağında veya eşlenmiş [sanal ağlarda](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) olmalıdır.  
- Seçilen sanal ağ, Azure NetApp Files atanmış bir alt ağa sahip olmalıdır.
- Azure NetApp biriminin performansı, [Azure NetApp Files Için hizmet düzeyinde](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)belgelendiği gibi birim kotasının ve hizmet düzeyinin bir işlevidir. HANA Azure NetApp birimlerini boyutlandırdığınızda, elde edilen aktarım hızının HANA sistem gereksinimlerini karşıladığından emin olun.  
- Azure NetApp Files, [dışarı aktarma ilkesi](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)sunar: izin verilen istemcileri, erişim türünü (okuma & yazma, salt okuma, vb.) denetleyebilirsiniz. 
- Azure NetApp Files Özellik henüz bölge farkında değildir. Şu anda Azure NetApp Files özelliği bir Azure bölgesindeki tüm kullanılabilirlik bölgelerinde dağıtılmaz. Bazı Azure bölgelerindeki olası gecikme etkilerine yönelik etkileri göz önünde bulundurun.  
- Düşük gecikme süresi boyunca sanal makinelerin Azure NetApp depolama alanına yakın bir şekilde dağıtılmasını sağlamak önemlidir. SAP HANA iş yükleri için düşük gecikme süresi kritik öneme sahiptir. Sanal makinelerin ve Azure NetApp Files birimlerinin yakın bir yerde dağıtıldığından emin olmak için Microsoft temsilcinizle birlikte çalışın.  
- <b>SID</b>adm IÇIN Kullanıcı kimliği ve sanal makinelerdeki `sapsys` grup kimliği, Azure NetApp Files yapılandırmasıyla aynı olmalıdır. 

> [!IMPORTANT]
> SAP HANA iş yükleri için düşük gecikme süresi kritik öneme sahiptir. Sanal makinelerin ve Azure NetApp Files birimlerinin yakın bir yerde dağıtıldığından emin olmak için Microsoft temsilcinizle birlikte çalışın.  

> [!IMPORTANT]
> <b>SID</b>adm Kullanıcı kimliği ve sanal makine Ile Azure NetApp yapılandırması arasındaki `sapsys` IÇIN grup kimliği arasında bir uyumsuzluk varsa, sanal makinelere takılan Azure NetApp birimlerindeki dosyaların izinleri `nobody` olarak görüntülenecektir. Azure NetApp Files için [Yeni bir sistem](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) oluştururken, <b>SID</b>adm için doğru Kullanıcı kimliğini ve `sapsys` için grup kimliğini belirttiğinizden emin olun.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp Files HANA veritabanı için boyutlandırma

Azure NetApp biriminin performansı, [Azure NetApp Files Için hizmet düzeyi](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)bölümünde belgelendiği gibi, birim boyutu ve hizmet düzeyi işlevindedir. 

Azure 'da SAP altyapısını tasarlarken, en düşük işleme özelliklerine çeviren SAP tarafından bazı minimum depolama gereksinimlerinden haberdar olmanız gerekir:

- 1 MB g/ç boyutlarıyla 250 MB/sn 'nin/Hana/log tarihinde okuma/yazma özelliğini etkinleştirin  
- 16 MB ve 64 MB g/ç boyutları için/Hana/Data için en az 400 MB/sn okuma etkinliğini etkinleştirin  
- 16 MB ve 64 MB g/ç boyutları ile/Hana/Data için en az 250 MB/sn yazma etkinliğini etkinleştirin  

Birim kotasının 1 TiB başına [Azure NetApp Files verimlilik limitleri](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) şunlardır:
- Premium depolama katmanı-64 MIB/s  
- Ultra depolama katmanı-128 MIB/sn  

Veri ve günlük SAP minimum aktarım hızı gereksinimlerini karşılamak ve `/hana/shared` yönelik yönergelere göre, önerilen boyutlar şöyle görünür:

| Birim | Boyut<br /> Premium depolama katmanı | Boyut<br /> Ultra depolama katmanı | Desteklenen NFS Protokolü |
| --- | --- | --- |
| /Hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /Hana/Data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /Hana/Shared | 4 çalışan düğümü başına en fazla (512 GB, 1xRAM) | 4 çalışan düğümü başına en fazla (512 GB, 1xRAM) | V3 veya v 4.1 |

Bu makalede sunulan düzenin SAP HANA yapılandırması, Azure NetApp Files Ultra Storage katmanını kullanarak şöyle görünür:

| Birim | Boyut<br /> Ultra depolama katmanı | Desteklenen NFS Protokolü |
| --- | --- |
| /Hana/log/mnt00001 | 2 TiB | v 4.1 |
| /Hana/log/mnt00002 | 2 TiB | v 4.1 |
| /Hana/Data/mnt00001 | 3,2 TiB | v 4.1 |
| /Hana/Data/mnt00002 | 3,2 TiB | v 4.1 |
| /Hana/Shared | 2 TiB | V3 veya v 4.1 |

> [!NOTE]
> Burada belirtilen Azure NetApp Files boyutlandırma önerileri, altyapı sağlayıcıları doğrultusunda, SAP 'nin ifade edilen en düşük gereksinimlerini karşılayacak şekilde hedefleniyor. Gerçek müşteri dağıtımları ve iş yükü senaryolarında bu yeterli olmayabilir. Bu önerileri bir başlangıç noktası olarak kullanın ve belirli iş yükünüzün gereksinimlerine göre uyarlayın.  

> [!TIP]
> Birimleri `unmount`, sanal makineleri durdurmanız veya SAP HANA durdurmanız gerekmeden Azure NetApp Files birimleri dinamik olarak yeniden boyutlandırabilir. Bu, uygulamanızı hem beklenen hem de öngörülemeyen üretilen iş taleplerini karşılamak için esneklik sağlar.

Bir SAP HANA genişleme yapılandırması ile birlikte bekleyen bir yapılandırma, [Azure VM 'lerde bulunan ve SUSE Linux Enterprise Server Azure NetApp Files Ile Azure VM 'lerinde bekleme düğümüyle birlikte SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)olarak yayımlanmış.


## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için bkz.

- [Azure sanal makineleri Için yüksek kullanılabilirlik kılavuzu SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
