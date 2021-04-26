---
title: 'Temeli topla: performans en iyi yöntemleri & yönergeleri'
description: Azure sanal makinesinde (VM) SQL Server performansını iyileştirmek için bir performans temeli toplama adımları sağlar.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1909d5fd5e3c02f104c73acb515740cb6ff65f6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572566"
---
# <a name="collect-baseline-performance-best-practices-for-sql-server-on-azure-vm"></a>Temeli topla: Azure VM 'de SQL Server için en iyi performans uygulamaları
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu makalede, Azure sanal makinelerinde (VM 'Ler) SQL Server performansını en iyi hale getirmeye yönelik bir dizi en iyi yöntem ve kılavuz olarak bir performans temeli toplamaya yönelik bilgiler sağlanmaktadır.

Genellikle maliyetleri iyileştirmek ve performansı iyileştirmek arasında bir denge vardır. Bu performans en iyi yöntemler serisi, Azure sanal makinelerinde SQL Server için *en iyi* performansı elde etmek üzere odaklanır. İş yükünüz daha az güç alıyorsa, önerilen en iyi duruma getirme gerektirmeyebilirsiniz. Bu önerileri değerlendirdiğiniz için performans ihtiyaçlarınızı, maliyetlerinizi ve iş yükü desenlerini göz önünde bulundurun.

## <a name="overview"></a>Genel Bakış

Öngörülü bir yaklaşım için, PerfMon/LogMan kullanarak performans sayaçlarını toplayın ve kaynak ortamının genel baskılarına ve olası performans sorunlarını daha iyi anlamak için SQL Server bekleme istatistiklerini yakalayın. 

[Uygulama performansı denetim listesini](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)takip eden en yoğun zamanlarda kaynak Iş yükünün CPU, bellek, [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), [verimlilik](../../../virtual-machines/premium-storage-performance.md#throughput)ve [gecikmesini](../../../virtual-machines/premium-storage-performance.md#latency) toplayarak başlayın. 

Tipik iş gününde iş yükleri gibi yoğun saatlerde veri toplayın, ayrıca günlük işleme ve hafta sonu ETL iş yükleri gibi diğer yüksek yük işlemleri de elde edin. Çeyrek sonu işleme gibi genellikle yoğun iş yükleri için kaynaklarınızın ölçeğini ölçeklendirmeniz ve iş yükü tamamlandığında ölçeklendirilmesi gerekir. 

İş yükünüzün performans gereksinimlerine ölçeklenebilen [VM boyutunu](../../../virtual-machines/sizes-memory.md) seçmek için performans analizini kullanın.


## <a name="storage"></a>Depolama

SQL Server performansı büyük ölçüde g/ç alt sistemine bağlıdır ve depolama performansı ıOPS ve aktarım hızı ile ölçülür. Veritabanınız fiziksel belleğe uygun olmadığı için SQL Server sürekli olarak veritabanı sayfalarını arabellek havuzunda içine ve dışına çıkarır. SQL Server için veri dosyaları farklı şekilde değerlendirilmelidir. Günlük dosyalarına erişim, tempdb dahil olmak üzere veri dosyalarının rasgele olarak erişildiği bir işlemin geri alınması gerektiği durumlar haricinde sıralıdır. Yavaş bir g/ç alt sistemidir varsa, kullanıcılarınız yavaş yanıt süreleri ve zaman aşımları nedeniyle tamamlanmamış görevler gibi performans sorunlarıyla karşılaşabilir. 

Azure Marketi Sanal makineleri, varsayılan olarak veri dosyalarından ayrı bir fiziksel diskte bulunan günlük dosyalarına sahiptir. Tempdb veri dosyalarının sayısı ve boyutu en iyi uygulamaları karşılar ve bu, kısa ömürlü sürücüyü hedeflenmiştir `D:\` . 

Aşağıdaki PerfMon sayaçları, SQL Server gereken GÇ aktarım hızını doğrulamaya yardımcı olabilir: 
* **\Logicaldisk\disk Okuma/sn** (Okuma IOPS)
* **\Logicaldisk\disk yazma/sn** (IOPS yaz) 
* **\Logicaldisk\disk okuma bayt/sn** (veri, günlük ve tempdb dosyaları için okuma işleme gereksinimleri)
* **\Logicaldisk\disk yazma bayt/sn** (veri, günlük ve tempdb dosyaları için yazma işleme gereksinimleri)

En yoğun düzeylerde ıOPS ve aktarım hızı gereksinimlerini kullanarak ölçülerinizin kapasitesiyle eşleşen VM boyutlarını değerlendirin. 

İş yükünüz 20K okuma ıOPS ve 10.000 yazma ıOPS gerektiriyorsa, depolama alanları kullanılarak P30 2 disk şeritli şekilde, E16s_v3 (en fazla 32K önbelleğe alınmış ve 25600 önbelleğe alınmış ıOPS ile) veya M16_s (en fazla 20 KB önbelleğe alınmış ve 10.000 ön belleğe sahip ıOPS ile) seçeneğini belirleyebilirsiniz. 

VM 'Ler, ıOPS ve aktarım hızı için farklı ölçek sınırlarına sahip olduğu için iş yükünün hem aktarım hızını hem de ıOPS gereksinimlerini anladığınızdan emin olun.

## <a name="memory"></a>Bellek

Ayrıca, işletim sistemi tarafından kullanılan dış belleği ve SQL Server dahili olarak kullanılan belleği izleyin. Her iki bileşenin de basıncını belirlenmesi, sanal makinelerin boyutlandırmasından ve ayarlamaya yönelik fırsatları belirlemesine yardımcı olur. 

Aşağıdaki PerfMon sayaçları, bir SQL Server sanal makinenin bellek durumunu doğrulamaya yardımcı olabilir: 
* [\Bellek\kullanılabilir MBayt](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer: bellek Manager\Target sunucu belleği (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: bellek Manager\Total sunucu belleği (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer Manager\Lazy yazma/sn](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer: buffer Manager\Page Life erkeklerin](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

## <a name="compute"></a>İşlem

Azure 'da işlem, Şirket içinden farklı yönetilir. Şirket içi sunucular, yönetim ek yükü ve yeni donanım edinme maliyeti nedeniyle yükseltme olmadan son birkaç yıla göre oluşturulmuştur. Sanallaştırma bu sorunlardan bazılarını azaltır, ancak kaynak tüketimine yapılan önemli değişiklikler tüm fiziksel ortamın yeniden dengelenmesi gereken anlamına gelir. 

Bu, farklı bir donanım serisi üzerinde yeni bir sanal makinenin, hatta farklı bir bölgede bile elde edilmesi kolay bir şekilde Azure 'da bir sınama değildir. 

Azure 'da, sanal makine kaynaklarından çok daha fazla yararlanmak istiyorsunuz, bu nedenle Azure sanal makineleri, iş yükünü etkilemeden ortalama CPU 'YU mümkün olduğunca yüksek tutmaya yönelik olarak yapılandırılmalıdır. 

Aşağıdaki PerfMon sayaçları, bir SQL Server sanal makinenin işlem durumunu doğrulamaya yardımcı olabilir:
* **\İşlemci bilgileri (_Total) \% Işlemci zamanı**
* **\ Process (sqlservr) \% Işlemci zamanı**

> [!NOTE] 
> İdeal olarak, %90 ' ün üzerinde en üst düzeye sahip olan ancak sürekli bir süre için %80 ' a 100 ulaşmadan, işlem sürenizin% ' ı kullanmayı hedefmeye çalışın. Temelde, yalnızca uygulama için gereken işlemi sağlamak ve iş gerektirdiğinde ölçeği yukarı veya aşağı doğru şekilde planlamak isteyeceksiniz. 


## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinmek için bu serideki diğer makalelere göz atın:
- [Hızlı denetim listesi](performance-guidelines-best-practices-checklist.md)
- [VM boyutu](performance-guidelines-best-practices-vm-size.md)
- [Depolama](performance-guidelines-best-practices-storage.md)


En iyi güvenlik uygulamaları için bkz. [Azure sanal makinelerinde SQL Server Için güvenlik konuları](security-considerations-best-practices.md).

[Azure sanal makinelerine genel bakış ' a SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)diğer SQL Server sanal makine makalelerini inceleyin. SQL Server sanal makineleri hakkında sorularınız olursa [Sık Sorulan Sorular](frequently-asked-questions-faq.md) bölümüne bakın.
