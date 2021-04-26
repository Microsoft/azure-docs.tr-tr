---
title: 'Depolama: performans en iyi uygulamaları & yönergeleri'
description: ', Azure sanal makinesinde (VM) SQL Server performansını iyileştirmek için en iyi depolama uygulamaları ve yönergeleri sağlar.'
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 23e006c637285ad484e98b23b2a9f506156f519c
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389732"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Depolama: Azure VM 'lerinde SQL Server için en iyi performans uygulamaları
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu makalede, Azure sanal makinelerinde (VM) SQL Server performansını iyileştirmek için en iyi depolama uygulamaları ve yönergeleri sunulmaktadır.

Genellikle maliyetleri iyileştirmek ve performansı iyileştirmek arasında bir denge vardır. Bu performans en iyi yöntemler serisi, Azure sanal makinelerinde SQL Server için *en iyi* performansı elde etmek üzere odaklanır. İş yükünüz daha az güç alıyorsa, önerilen en iyi duruma getirme gerektirmeyebilirsiniz. Bu önerileri değerlendirdiğiniz için performans ihtiyaçlarınızı, maliyetlerinizi ve iş yükü desenlerini göz önünde bulundurun.

Daha fazla bilgi edinmek için bu serinin diğer makalelerine bakın: [performans denetim listesi](performance-guidelines-best-practices-checklist.md), [VM boyutu](performance-guidelines-best-practices-vm-size.md)ve [temel toplama](performance-guidelines-best-practices-collect-baseline.md). 

## <a name="checklist"></a>Denetim Listesi

Makalenin geri kalanının daha ayrıntılı bir şekilde ele aldığı depolama en iyi uygulamalarına kısa bir genel bakış için aşağıdaki denetim listesini gözden geçirin: 

- Disk türünü seçmeden önce, uygulamayı izleyin ve SQL Server Data, log ve tempdb dosyaları için [depolama bant genişliği ve gecikme süresi gereksinimlerini saptayın](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) . 
- Depolama performansını iyileştirmek için, kullanılabilir önbelleğe alınmamış en yüksek ıOPS 'yi planlayın ve [sanal makine ve disklerin kasasından](../../../virtual-machines/premium-storage-performance.md#throttling)kaçınırken veri okuma işlemleri için performans özelliği olarak veri önbelleğe alma özelliğini kullanın.
- Verileri, günlüğü ve tempdb dosyalarını ayrı sürücülere yerleştirin.
    - Veri sürücüsü için, önbellek desteğinin kullanılabilirliğini sağlamak üzere yalnızca [Premium P30 ve P40 disklerini](../../../virtual-machines/disks-types.md#premium-ssd) kullanın
    - [Premium P30-P80 disklerini](../../../virtual-machines/disks-types.md#premium-ssd) değerlendirirken kapasite ve test performansına yönelik günlük sürücüsü planı için maliyet karşılaştırması
      - Alt milisaniyelik depolama gecikmesi gerekliyse, işlem günlüğü için [Azure Ultra diskler](../../../virtual-machines/disks-types.md#ultra-disk) ' i kullanın. 
      - D serisi sanal makine dağıtımları için Azure Ultra disklerini kullanarak [yazma hızlandırıcıyı](../../../virtual-machines/how-to-enable-write-accelerator.md) göz önünde bulundurun.
    - En [](/sql/relational-databases/databases/tempdb-database) `D:\` iyi VM boyutunu seçtikten sonra, çoğu SQL Server iş yükü için tempdb 'yi yerel kısa ömürlü SSD sürücüsüne yerleştirin. 
      - Yerel sürücünün kapasitesi tempdb için yeterli değilse, VM 'yi boyutlandırmayı göz önünde bulundurun. Daha fazla bilgi için bkz. [veri dosyası önbelleğe alma ilkeleri](#data-file-caching-policies) .
- Hedef sanal makinenin ıOPS ve aktarım hızı sınırlarına kadar g/ç bant genişliğini artırmak için [depolama alanlarını](/windows-server/storage/storage-spaces/overview) kullanarak birden çok Azure veri diski Stripe.
- Veri dosyası diskleri için [konak önbelleğini](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) salt okunurdur olarak ayarlayın.
- Günlük dosyası diskleri için [konak önbelleğe almayı](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) hiçbiri olarak ayarlayın.
    - SQL Server dosyaları içeren disklerde okuma/yazma önbelleğini etkinleştirmeyin. 
    - Diskinizin önbellek ayarlarını değiştirmeden önce SQL Server hizmetini her zaman durdurun.
- Geliştirme ve test iş yükleri ve uzun süreli yedekleme Arşivi, standart depolama kullanmayı düşünün. Üretim iş yükleri için Standart HDD/SDD kullanılması önerilmez.
- [Kredi tabanlı disk](../../../virtual-machines/disk-bursting.md#credit-based-bursting) patlaması (P1-P20) yalnızca daha küçük geliştirme/test iş yükleri ve departman sistemleri için düşünülmelidir.
- Depolama hesabını SQL Server VM aynı bölgede sağlayın. 
- Azure coğrafi olarak yedekli depolamayı (coğrafi çoğaltma) devre dışı bırakın ve depolama hesabında LRS (yerel yedekli depolama) kullanın.
- Veri diskinizi, geçici `D:\` sürücü dışında (varsayılan 4 KB 'ye sahiptir) bir sürücüye yerleştirilmiş tüm veri dosyalarının 64 KB blok boyutunu (ayırma birim boyutu) kullanacak şekilde biçimlendirin. Azure Marketi aracılığıyla dağıtılan SQL Server VM 'Ler, depolama havuzu 64 KB olarak ayarlanan bir blok boyutu ve ayırma ile biçimlendirilen veri disklerinde gelir. 

Depolama denetim listesini başkalarıyla karşılaştırmak için, kapsamlı [performans en iyi yöntemleri denetim listelerine](performance-guidelines-best-practices-checklist.md)bakın. 

## <a name="overview"></a>Genel Bakış

Azure VM 'de SQL Server iş yükleri için en etkili yapılandırmayı bulmak üzere [iş uygulamanızın depolama performansını ölçerek](performance-guidelines-best-practices-collect-baseline.md#storage)başlayın. Depolama gereksinimleri bilindikten sonra, gerekli ıOPS 'yi ve uygun bellekten sanal çekirdek oranına sahip aktarım hızını destekleyen bir sanal makine seçin. 

İş yükünüz için yeterli depolama ölçeklenebilirliği ve işletmenizin kapasitesini ve performans gereksinimlerini karşılayan disklerin bir karışımını (genellikle bir depolama havuzunda) içeren bir VM boyutu seçin. 

Disk türü, hem diskte hem de en yüksek performans gereksinimlerinizin barındırıldığı dosya türüne bağlıdır.

> [!TIP]
> Azure portal üzerinden SQL Server VM sağlamak, depolama yapılandırma sürecinde size rehberlik etmenize yardımcı olur ve veri ve günlük dosyalarınız için ayrı depolama havuzları oluşturma, tempdb 'yi `D:\` sürücüye hedefleme ve en iyi önbellek ilkesini etkinleştirme gibi en iyi depolama uygulamalarını uygular. Depolamayı sağlama ve yapılandırma hakkında daha fazla bilgi için bkz. [SQL VM depolama yapılandırması](storage-configuration.md). 

## <a name="vm-disk-types"></a>VM disk türleri

Disklerinizin performans düzeyinde bir seçiminiz vardır. Temel depolama olarak kullanılabilen yönetilen disk türleri (performans özellikleri arttırılarak listelenmiştir) standart sabit disk sürücüleri (HDD), standart SSD 'Ler, Premium katı hal sürücüleri (SSD) ve ultra disklerdir. 

Diskin performansı kapasiteye göre artar, 4 GiB alan P1 ve 120 ıOPS gibi [Premium disk etiketlerine](../../../virtual-machines/disks-types.md#premium-ssd) göre gruplanmış olarak, 32 TİB depolama ve 20.000 IOPS ile P80. Premium Depolama bazı iş yükleri için okuma ve yazma performansını artırmaya yardımcı olan bir depolama önbelleğini destekler. Daha fazla bilgi için bkz. [yönetilen disklere genel bakış](../../../virtual-machines/managed-disks-overview.md). 

Azure VM 'de SQL Server için göz önünde bulundurmanız gereken üç ana [disk türü](../../../virtual-machines/managed-disks-overview.md#disk-roles) vardır. BIR işletim sistemi diski, geçici bir disk ve veri diskleriniz. İşletim sistemi sürücüsü `(C:\)` ve kısa ömürlü geçici sürücü üzerinde nelerin depolandığını dikkatle seçin `(D:\)` . 

### <a name="operating-system-disk"></a>İşletim sistemi diski

İşletim sistemi diski, bir işletim sisteminin çalışan bir sürümü olarak önyükleme yapılabilen ve bağlanan ve sürücü olarak etiketlenmiş bir VHD 'dir `C:\` . Bir Azure sanal makine oluşturduğunuzda, Platform işletim sistemi diski için VM 'ye en az bir disk ekler. `C:\`Sürücü, uygulama yüklemeleri ve dosya yapılandırması için varsayılan konumdur. 

Üretim SQL Server ortamları için, veri dosyaları, günlük dosyaları, hata günlükleri için işletim sistemi diskini kullanmayın. 

### <a name="temporary-disk"></a>Geçici disk

Birçok Azure sanal makinesi, geçici disk (sürücü olarak etiketlenir) adlı başka bir disk türü içerir `D:\` . Sanal makine serisine ve boyut boyutuna bağlı olarak, bu diskin kapasitesi farklılık gösterir. Geçici disk, disk depolamanın yeniden oluşturulduğu (içinde olduğu gibi, serbest bırakıldığı ve yeniden ayrıldığı), sanal makine yeniden başlatıldığında veya farklı bir konağa taşındığında (örneğin, [hizmet düzeltme](/troubleshoot/azure/virtual-machines/understand-vm-reboot)için), daha kısa ömürlü olur. 

Geçici depolama sürücüsü uzak depolama için kalıcı değil ve bu nedenle kullanıcı veritabanı dosyalarını, işlem günlüğü dosyalarını veya korunması gereken herhangi bir şeyi depolamamalıdır. 

`D:\`Yerel önbellek tüketimi bir sorun değilse, SQL Server iş yükleri için yerel GEÇICI SSD sürücüsüne tempdb yerleştirin. [Geçici diski](../../../virtual-machines/azure-vms-no-temp-disk.md) olmayan bir sanal makine kullanıyorsanız, tempdb 'yi kendi yalıtılmış diskine veya depolama havuzuna, önbelleğe alma ayarı salt okunurdur olarak ayarlamanız önerilir. Daha fazla bilgi için bkz. [tempdb veri önbelleğe alma ilkeleri](performance-guidelines-best-practices-storage.md#data-file-caching-policies).

### <a name="data-disks"></a>Veri diskleri

Veri diskleri, tek bir diskin sanal makineye sunabileceği kapasiteyi ve performansı aşmamak için genellikle [depolama havuzlarında](/windows-server/storage/storage-spaces/overview) oluşturulan uzak depolama disklerdir.

İş yükünüzün ıOPS, verimlilik ve kapasite gereksinimlerini karşılayan en az disk sayısını ekleyin. Yeniden boyutlandırmayı planladığınız en küçük sanal makinede bulunan en fazla veri diski sayısını aşmayın.

En iyi cins performans gereksinimlerine sağlanan veri disklerine veri ve günlük dosyaları yerleştirin. 

Veri diskinizi, geçici `D:\` sürücü dışında (varsayılan 4 KB 'ye sahiptir) bir sürücüye yerleştirilmiş tüm veri dosyaları için 64 KB ayırma birimi boyutu kullanacak şekilde biçimlendirin. Azure Marketi aracılığıyla dağıtılan SQL Server VM 'Ler, depolama havuzu boyutu 64 KB olarak ayarlanan ayırma birimi boyutu ve ayırma ile biçimlendirilen veri disklerinde gelir. 

## <a name="premium-disks"></a>Premium diskler

Üretim SQL Server iş yükleri için veri ve günlük dosyaları için Premium SSD diskleri kullanın. Premium SSD ıOPS ve bant genişliği, [disk boyutuna ve türüne](../../../virtual-machines/disks-types.md)göre değişir. 

Üretim iş yükleri için P30 ve/veya P40 disklerini SQL Server veri dosyalarını kullanarak önbelleğe alma desteğinin yanı sıra SQL Server işlem günlüğü dosyaları için P30 up P80 kullanın.  En iyi toplam sahip olma maliyeti için, veri ve günlük dosyaları için P30s (5000 ıOPS/200 MBPS) ile başlayın ve yalnızca sanal makine disk sayısını kontrol etmeniz gerektiğinde daha fazla kapasite seçin.

OLTP iş yükleri için, en yoğun zamanlarda ve `Disk Reads/sec`  +  performans sayaçlarındaki iş yüklerini kullanarak, disk başına (veya depolama havuzu) hedef IOPS 'yi performans gereksinimlerinize göre eşleştirin `Disk Writes/sec` . Veri ambarı ve raporlama iş yükleri için, iş yüklerini yoğun zamanlarda ve ' de kullanarak hedef aktarım hızını eşleştirin `Disk Read Bytes/sec`  +  `Disk Write Bytes/sec` . 

En iyi performansı elde etmek için depolama alanları 'nı kullanın, biri günlük dosyası (ler) ve diğer veri dosyaları için olmak üzere iki havuz yapılandırın. Disk şeritleme kullanmıyorsanız, ayrı sürücülerle eşleştirilmiş iki Premium SSD diski kullanın; burada bir sürücü, günlük dosyasını ve diğeri de verileri içerir.

Depolama havuzunuzun bir parçası olarak kullanılan, disk başına [sağlanan IOPS ve aktarım hızı](../../../virtual-machines/disks-types.md#premium-ssd) . Disklerin Birleşik ıOPS ve aktarım hızı özellikleri, sanal makinenin üretilen iş sınırlarına en yüksek yetenektir.

En iyi uygulama, ıOPS 'nin (ve aktarım hızı) ve kapasitenin en düşük gereksinimlerini karşılaırken mümkün olan en az sayıda diski kullanmaktır. Bununla birlikte, Fiyat ve performans dengelemesi, az sayıda büyük disk yerine çok sayıda küçük diskle daha iyi bir şekilde eğilimindedir.

### <a name="scaling-premium-disks"></a>Premium diskleri ölçeklendirme

Azure yönetilen diski ilk kez dağıtıldığında, bu diskin performans katmanı sağlanan disk boyutunu temel alır. Dağıtım sırasında performans katmanını belirleyin veya disk boyutunu değiştirmeden daha sonra değiştirin. Talep arttıkça, iş gereksinimlerinizi karşılamak için performans düzeyini artırabilirsiniz. 

Performans katmanını değiştirmek, yöneticilerin [disk](../../../virtual-machines/disk-bursting.md#credit-based-bursting)patlaması olmadan daha yüksek talebe göre hazırlık ve karşılamasını sağlar. 

Faturanızı depolama performans katmanını karşılayacak şekilde tasarlanan süre için daha yüksek performansı kullanın. Kapasiteyi arttırmadan performans gereksinimleriyle eşleşecek şekilde katmanı yükseltin. Ek performansa artık gerek kalmadığında Orijinal katmana geri dönün.

Bu düşük maliyetli ve performansın geçici genişletmesi, alışveriş, performans testi, eğitim olayları ve daha fazla performansın yalnızca kısa bir dönem için ihtiyaç duyduğu diğer kısa pencereler gibi hedeflenmiş olaylar için güçlü bir kullanım durumdur. 

Daha fazla bilgi için bkz. [yönetilen diskler Için performans katmanları](../../../virtual-machines/disks-change-performance.md). 

## <a name="azure-ultra-disk"></a>Azure Ultra disk

Düşük gecikme süresine sahip bir gecikme süresi varsa, SQL Server günlük sürücüsü için [Azure Ultra disk](../../../virtual-machines/disks-types.md#ultra-disk) kullanmayı veya g/ç gecikme süresine son derece duyarlı uygulamalar için de veri sürücüsünü kullanmayı düşünün. 

Kapasite ve ıOPS 'nin bağımsız olarak ölçeklenmesi için, Ultra disk yapılandırılabilir. Ultra disk yöneticileri, uygulama ihtiyaçlarına göre kapasite, ıOPS ve aktarım hızı gereksinimleriyle bir disk sağlayabilir. 

Ultra disk tüm VM serisinde desteklenmez ve bölge kullanılabilirliği, yedeklilik ve Azure Backup için destek gibi başka sınırlamalar vardır. Daha fazla bilgi için bkz. kısıtlamaların tam listesi için [Azure Ultra diskleri kullanma](../../../virtual-machines/disks-enable-ultra-ssd.md) . 

## <a name="standard-hdds-and-ssds"></a>Standart HDD 'Ler ve SSD 'Ler

[Standart HDD 'ler](../../../virtual-machines/disks-types.md#standard-hdd) ve SSD 'ler, değişen gecikme süreleri ve bant genişliğine sahiptir ve yalnızca geliştirme ve test iş yükleri için önerilir. Üretim iş yükleri Premium SSD 'Ler kullanmalıdır. Standart SSD (geliştirme/test senaryoları) kullanıyorsanız, [sanal makine boyutunuzu](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) desteklediği en fazla veri diski sayısını eklemek ve en iyi performans Için depolama alanları ile disk şeritleme kullanılması önerilir.

## <a name="caching"></a>Önbelleğe Alma

Premium Depolama önbelleğini destekleyen sanal makineler, bir sanal makinenin ıOPS ve aktarım hızı yeteneklerini genişletmek için Azure BlobCache veya Host Caching adlı ek bir özellikten yararlanabilir. Hem Premium Depolama hem de Premium depolama önbelleği için etkinleştirilen sanal makineler, depolama performansını geliştirmek için birlikte kullanılabilecek iki farklı depolama bant genişliği sınırlarına sahiptir.

Bir sanal makinenin önbelleğe alınmamış disk işleme limitlerini önbelleğe alma olmadan ıOPS ve MBps aktarım hızı. Önbelleğe alınan en fazla sınır, büyüme ve beklenmeyen en üst düzey adreslerine yardımcı olan okuma noktaları için ek bir arabellek sağlar.

Ek maliyet olmadan veri sürücüsüne yönelik okuma performansını önemli ölçüde artırmak için her seçenek desteklendiğinde Premium önbelleğe almayı etkinleştirin. 

Azure BlobCache 'e okuma ve yazma (önbelleğe alınan ıOPS ve aktarım hızı), sanal makinenin önbelleğe alınmamış ıOPS ve verimlilik sınırlarına göre sayılmaz.

> [!NOTE]
> Disk önbelleği 4 TiB ve daha büyük (P50 ve daha büyük) diskler için desteklenmez. Sanal makinenize birden çok disk iliştirilmişse, 4 TiB 'den küçük olan her bir disk önbelleğe almayı destekleyecektir. Daha fazla bilgi için bkz. [disk önbelleği](../../../virtual-machines/premium-storage-performance.md#disk-caching). 

### <a name="uncached-throughput"></a>Önbelleğe alınmamış aktarım hızı

Önbelleğe alınmamış maksimum disk ıOPS ve aktarım hızı, sanal makinenin işleyebileceği en yüksek uzak depolama sınırıdır. Bu sınır, sanal makinede tanımlanmıştır ve temel disk depolamanın bir sınırı değildir. Bu sınır, yerel g/ç 'ye geçici sürücü ( `D:\` sürücü) veya işletim sistemi sürücüsüne göre değil, VM 'ye uzaktan bağlı veri sürücülerine yönelik g/ç için geçerlidir.

Önbelleğe alınmamış ıOPS ve bir VM için kullanılabilir aktarım hızı miktarı, sanal makinenizin belgelerinde doğrulanabilir.

Örneğin, [d serisi](../../../virtual-machines/m-series.md) belgelerde, önbelleğe alınmamış disk aktarım hızı IÇIN Standard_M8ms VM için maksimum önbelleğe alınmamış üretilen iş hacmı 5000 ıops ve 125 MB/sn olduğunu gösterir. 

![D serisi önbelleğe alınmamış disk işleme belgelerini gösteren ekran görüntüsü.](./media/performance-guidelines-best-practices/m-series-table.png)

Benzer şekilde, Standard_M32ts önbelleğe alınmamış disk ıOPS ve 500 MB/sn önbelleğe alınmamış disk aktarım hızını destekle20.000 diğine bakabilirsiniz. Bu sınır, temel alınan Premium disk depolamadan bağımsız olarak sanal makine düzeyinde yönetilir.

Daha fazla bilgi için bkz. [önbelleğe alınmamış ve önbelleğe alınmış sınırlar](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits).


### <a name="cached-and-temp-storage-throughput"></a>Önbelleğe alınmış ve geçici depolama aktarım hızı

Önbelleğe alınan en fazla ve geçici depolama aktarım hızı sınırı, sanal makinedeki önbelleğe alınmamış aktarım hızı sınırının ayrı bir sınırsıdır. Azure BlobCache, sanal makine konağının Rastgele erişimli belleği ve yerel olarak bağlı SSD 'nin bir bileşiminden oluşur. `D:\`Sanal makine içindeki geçici sürücü (sürücü) bu yerel SSD üzerinde de barındırılır.

Önbelleğe alınan en fazla ve geçici depolama aktarım hızı sınırı, `D:\` **yalnızca** konak önbelleğe alma etkinse, yerel geçici sürücü (sürücü) ve Azure Blobcache 'e karşı g/ç 'yi yönetir. 

Premium depolamada önbelleğe alma etkinleştirildiğinde, sanal makineler, önbelleğe alınmamış VM ıOPS ve üretilen iş limitlerinin sınırlarının ötesine ölçeklendirebilir.  

Yalnızca belirli sanal makineler hem Premium Storage hem de Premium Depolama önbelleğini destekler (sanal makine belgelerinde doğrulanması gerekir). Örneğin, [d serisi](../../../virtual-machines/m-series.md) belgelerde hem Premium Depolama hem de Premium Depolama önbelleğinin desteklendiği belirtilir: 

![D serisi Premium Depolama desteğini gösteren ekran görüntüsü.](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

Önbelleğin sınırları, sanal makine boyutuna bağlı olarak değişir. Örneğin, Standard_M8ms VM 10000 önbelleğe alınmış disk ıOPS ve 1000 MBps önbelleğe alınmış disk aktarım 793 hızını destekler. Benzer şekilde, Standard_M32ts VM 40000 önbelleğe alınmış disk ıOPS ve 400 MBps önbelleğe alınmış disk aktarım 3174 hızını destekler. 

![D serisi önbelleğe alınmış disk işleme belgelerini gösteren ekran görüntüsü.](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

Mevcut bir VM 'de konak önbelleğini el ile etkinleştirebilirsiniz. Sanal makinenizin önbelleğe alma ilkesinde herhangi bir değişiklik yapılmadan önce tüm uygulama iş yüklerini ve SQL Server hizmetlerini durdurun. Sanal makine önbellek ayarlarından herhangi birinin değiştirilmesi, ayarlar uygulandıktan sonra hedef diskin ayrılmakta ve yeniden iliştirilmesi sonucunu vermez.

### <a name="data-file-caching-policies"></a>Veri dosyası önbelleğe alma ilkeleri

Depolama önbelleğe alma ilkeniz, sürücüde barındırılan SQL Server veri dosyalarının türüne göre farklılık gösterir. 

Aşağıdaki tabloda, SQL Server veri türüne göre önerilen önbelleğe alma ilkelerinin özeti verilmiştir: 

|SQL Server disk |Öneri |
|---------|---------|
| **Veri diski** | `Read-only`SQL Server veri dosyalarını barındıran diskler için önbelleğe almayı etkinleştirin. <br/> Önbellekten okuma, veri diskinden önbelleğe alınmamış okumalardan daha hızlı olacaktır. <br/> Önbelleğe alınmamış ıOPS ve aktarım hızı ile önbelleğe alınan ıOPS ve aktarım hızı, VM sınırları dahilinde sanal makineden kullanılabilen toplam olası performansı sağlar, ancak gerçek performans, iş yükünün önbelleği kullanma özelliğine (önbellek isabet oranı) göre değişir. <br/>|
|**İşlem günlüğü diski**|Önbellek ilkesini `None` , işlem günlüğünü barındıran diskler için olarak ayarlayın.  Işlem günlüğü diski için önbelleğe almayı etkinleştirmeye yönelik bir performans avantajı yoktur, ya da `Read-only` `Read/Write` günlük sürücüsünde etkinleştirilmiş veya önbelleğe alma özelliği, sürücüye yönelik yazma performansını düşürebilir ve veri sürücüsünde okuma için kullanılabilir önbellek miktarını azaltır.  |
|**İşletim SISTEMI diski** | Varsayılan önbelleğe alma ilkesi, `Read-only` `Read/write` işletim sistemi sürücüsü için veya olabilir. <br/> İşletim sistemi sürücüsünün önbelleğe alma düzeyinin değiştirilmesi önerilmez.  |
| **tempdb**| Kapasite nedeniyle daha kısa bir süre içinde tempdb yerleştirilemez `D:\` , daha büyük bir kısa ömürlü sürücü almak için sanal makineyi yeniden boyutlandırın ya da tempdb 'yi önbelleğe alma yapılandırılmış ayrı bir veri sürücüsüne yerleştirin `Read-only` . <br/> Sanal makine önbelleği ve kısa ömürlü sürücü her ikisi de yerel SSD 'yi kullanır, bu nedenle tempdb g/ç olarak boyutlandırılırken, kısa ömürlü sürücüde barındırıldığında, önbelleğe alınmış ıOPS ve üretilen iş sanal makine limitlerine göre sayılacak şekilde bu değeri aklınızda bulundurun.| 
| | | 


Daha fazla bilgi için bkz. [disk önbelleği](../../../virtual-machines/premium-storage-performance.md#disk-caching). 


## <a name="disk-striping"></a>Disk şeridi

Günlük dosyası ve tempdb dahil olmak üzere veri disklerinin sayısını öğrenmek için SQL veri dosyalarınız için gereken aktarım hızını ve bant genişliğini çözümleyin. Aktarım hızı ve bant genişliği sınırları VM boyutuna göre farklılık gösterir. Daha fazla bilgi için bkz. [VM boyutu](../../../virtual-machines/sizes.md)

Daha fazla işleme için ek veri diskleri ekleyin ve disk şeritleme kullanın. Örneğin, 12.000 ıOPS ve 180 MB/sn aktarım hızı gerektiren bir uygulama, 15.000 ıOPS ve 600 MB/sn aktarım hızı sağlamak için üç şeritli P30 disk kullanabilir. 

Disk şeritleme yapılandırmak için bkz. [disk şeridi](storage-configuration.md#disk-striping). 

## <a name="disk-capping"></a>Disk dönüşü 

Hem disk hem de sanal makine düzeyinde üretilen iş sınırları vardır. VM başına en fazla ıOPS sınırı ve disk başına farklılık gösterir ve birbirinden bağımsızdır.

Bu limitlerin ötesinde kaynakları kullanan uygulamalar kısıtlanacak (Ayrıca, camış olarak da bilinir). Uygulama gereksinimlerini karşılayan bir disk Stripe içinde bir sanal makine ve disk boyutu seçin ve sınırlamalar uygulanmaz. Daha az üretilen iş olması için, önbelleğe alma özelliğini kullanın veya uygulamayı ayarlayın.

Örneğin, 12.000 ıOPS ve 180 MB/s gerektiren bir uygulama şunları yapabilir: 
- Önbelleğe alınmamış maksimum disk işleme 20.000 ıOPS ve 500 MBps olan [Standard_M32ms](../../../virtual-machines/m-series.md) kullanın.
- 15.000 ıOPS ve 600 MB/sn aktarım hızı sunmak için üç P30 diski Stripe.
- [Standard_M16ms](../../../virtual-machines/m-series.md) bir sanal makine kullanın ve ana bilgisayar önbelleğe alma özelliğini kullanarak yerel önbelleği tüketir. 

Yüksek kullanımlarda ölçeği artırılabilecek şekilde yapılandırılan sanal makineler, en büyük VM boyutunu desteklemek için yeterli ıOPS ve aktarım hızı olan depolama alanı sağlamalıdır ve en küçük VM SKU 'SU tarafından desteklenen en yüksek sayıdan küçük veya ona eşit olan en fazla disk sayısını korurken.

Disk dönüşü hakkında daha fazla bilgi edinmek ve dönüşü önlemek için önbelleğe almayı kullanmak için bkz. [DISK GÇ dönüşü](../../../virtual-machines/disks-performance.md).

> [!NOTE] 
> Bazı disk, hala kullanıcılara tatmin edici performansa neden olabilir; iş için maliyeti ve performansı dengelemek üzere daha büyük bir VM 'ye yeniden boyutlandırmak yerine iş yüklerini ayarlayın ve koruyun. 


## <a name="write-acceleration"></a>Yazma hızlandırma

Yazma hızlandırma yalnızca, [a serisi](https://docs.microsoft.com/azure/virtual-machines/m-series) sanal makineler (VM) için kullanılabilen bir disk özelliğidir. Yazma hızlandırmasının amacı, yüksek hacimli görev açısından kritik OLTP iş yükleri veya veri ambarı ortamları nedeniyle tek basamaklı g/ç gecikme süresi gerektiğinde Azure Premium depolamada yapılan yazma işlemleri için g/ç gecikme süresini artırmaktır. 

Günlük dosyalarını barındıran sürücüye yazma gecikmesini geliştirmek için yazma hızlandırmasını kullanın. SQL Server veri dosyaları için yazma hızlandırma kullanmayın. 

Yazma Hızlandırıcısı diskler, sanal makineyle aynı ıOPS sınırını paylaşır. Eklenen diskler, bir VM için Yazma Hızlandırıcısı ıOPS sınırını aşamaz.  

Takip tablosu, sanal makine başına desteklenen veri disklerinin ve ıOPS 'nin sayısını özetler: 

| VM SKU  | Yazma Hızlandırıcısı disk sayısı  | VM başına Yazma Hızlandırıcısı disk ıOPS 'si  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2, M208s_v2  | 8  | 10000  |
| M64ms, M64ls, M64s  |  8 | 10000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms, M8s  | 1 | 1250 |

Yazma hızlandırmasının kullanılması için çeşitli kısıtlamalar vardır. Daha fazla bilgi için [yazma Hızlandırıcısı kullanırken kısıtlamalar](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator)bölümüne bakın.


### <a name="comparing-to-azure-ultra-disk"></a>Azure Ultra disk ile karşılaştırma

Yazma hızlandırma ve Azure Ultra diskler arasındaki en büyük fark, yazma hızlandırmasının yalnızca d serisi ve Azure Ultra diskler için kullanılabilir bir depolama seçeneği olduğu bir sanal makine özelliğidir. Yazma Ivmesi, sanal makine boyutuna bağlı olarak kendi sınırlamalarıyla, yazma için iyileştirilmiş bir önbelleğidir. Azure Ultra diskler, Azure sanal makineleri için düşük gecikmeli bir disk depolama seçeneğidir. 

Mümkünse, işlem günlüğü diski için Ultra diskler üzerinde yazma hızlandırma kullanın. Yazma hızlandırmasını desteklemeyen ancak işlem günlüğü için düşük gecikme gerektiren sanal makineler için Azure Ultra diskler ' i kullanın. 

## <a name="monitor-storage-performance"></a>Depolama performansını izleme

Depolama ihtiyaçlarını değerlendirmek ve depolamanın ne kadar iyi çalıştığını belirlemek için ne ölçecek ve bu göstergelerin ne anlama geldiğini anlamanız gerekir. 

[IOPS (saniye başına giriş/çıkış)](../../../virtual-machines/premium-storage-performance.md#iops) , uygulamanın depolama için saniye başına yapmakta olduğu isteklerin sayısıdır. Performans Izleme sayaçlarını ve kullanarak ıOPS ölçümünü ölçün `Disk Reads/sec` `Disk Writes/sec` . [OLTP (çevrimiçi işlem işleme)](/azure/architecture/data-guide/relational-data/online-transaction-processing) uygulamalarının en iyi performansı elde etmek için daha yüksek IOPS 'yi kullanabilmesi gerekir. Ödeme işleme sistemleri, çevrimiçi alışveriş ve perakende satış noktası sistemleri gibi uygulamalar, OLTP uygulamalarına örnek olarak verilebilir.

[Aktarım hızı](../../../virtual-machines/premium-storage-performance.md#throughput) , genellikle saniyede megabayt ile ölçülen, temeldeki depolamaya gönderilen veri birimidir. Performans Izleyicisi sayaçları ve ile aktarım hızını `Disk Read Bytes/sec` ölçün `Disk Write Bytes/sec` . [Veri](/azure/architecture/data-guide/relational-data/data-warehousing) AMBARı, IOPS üzerinden aktarım hızını en üst düzeye çıkarırken iyileştirilir Analiz, raporlama, ETL workstreams ve diğer iş zekası hedeflerine yönelik veri depoları gibi uygulamalar, veri ambarı uygulamalarına örnek olarak verilebilir.

G/ç birim boyutları, ıOPS ve aktarım hızı yeteneklerini daha küçük g/ç boyutları daha yüksek ıOPS ve daha büyük g/ç boyutları daha yüksek aktarım hızı olarak etkiler. SQL Server en iyi g/ç boyutunu otomatik olarak seçer. Hakkında daha fazla bilgi için bkz. [Uygulamalarınız IÇIN IOPS, üretilen iş ve gecikmeyi iyileştirme](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance). 

Sanal makine ve disk düzeyinin yanı sıra tüketimi ve AzureBlob önbelleğinin durumunu bulmak için değerli olan belirli Azure Izleyici ölçümleri vardır. İzleme çözümünüze ve Pano Azure portal eklenecek anahtar sayaçlarını belirlemek için bkz. [depolama kullanım ölçümleri](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics). 

> [!NOTE]
> Azure Izleyici Şu anda kısa ömürlü geçici sürücü için disk düzeyi ölçümleri sunmaz `(D:\)` . VM 'nin önbelleğe alınmış ıOPS yüzdesi ve tüketilen VM önbelleğe alınmış bant genişliği yüzdesi, hem kısa ömürlü geçici sürücü hem de `(D:\)` ana bilgisayar önbelleklemesi ÜZERINDE IOPS ve aktarım hızını yansıtır


## <a name="next-steps"></a>Sonraki adımlar

En iyi performans uygulamaları hakkında daha fazla bilgi edinmek için bu serideki diğer makalelere bakın:
- [Hızlı denetim listesi](performance-guidelines-best-practices-checklist.md)
- [VM boyutu](performance-guidelines-best-practices-vm-size.md)
- [Taban çizgisini topla](performance-guidelines-best-practices-collect-baseline.md)

En iyi güvenlik uygulamaları için bkz. [Azure sanal makinelerinde SQL Server Için güvenlik konuları](security-considerations-best-practices.md).

TPC-E ve TPC_C kıyaslamalar ile Azure VM 'lerinde SQL Server performansının ayrıntılı bir şekilde sınanması için bkz. Web günlüğü [OLTP performansını iyileştirme](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794).

[Azure sanal makinelerine genel bakış ' a SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)diğer SQL Server sanal makine makalelerini inceleyin. SQL Server sanal makineleri hakkında sorularınız olursa [Sık Sorulan Sorular](frequently-asked-questions-faq.md) bölümüne bakın.
