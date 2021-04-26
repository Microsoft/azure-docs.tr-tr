---
title: Tek birimde Azure NetApp Files Oracle veritabanı performansı | Microsoft Docs
description: Oracle veritabanı 'nda Azure NetApp Files tek bir birimin performans testi sonuçlarını açıklar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: c6cdf2f6dada0aa4dea2f70f18237b7ee39e3ea1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91571521"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>Azure NetApp Files tek birimlerinde Oracle veritabanı performansı

Bu makalede, bulutta Oracle hakkında aşağıdaki konular ele alınmaktadır. Bu konular bir veritabanı yöneticisine, bulut mimaruna veya depolama alanı mimarilerine ilişkin belirli bir ilgi olabilir:   

* Çevrimiçi işlem işleme (OLTP) iş yükünü (çoğunlukla rastgele g/ç) veya çevrimiçi analitik işlem (OLAP) iş yükünü (çoğunlukla sıralı g/ç) barındırdığınızda, performans nasıl görünür?   
* Normal Linux çekirdek NFS (kNFS) istemcisiyle Oracle 'ın kendi doğrudan NFS istemcisi arasındaki performans farkı nedir?
* Bant genişliği düşünüldüğünde, tek bir Azure NetApp Files biriminin performansı yeterlidir mi?

## <a name="testing-environment-and-components"></a>Ortamı ve bileşenleri test etme

Aşağıdaki diyagramda test için kullanılan ortam gösterilmektedir. Tutarlılık ve kolaylık sağlaması için, test öğelerinin tüm öğelerini dağıtmak üzere anormal bir PlayBook kullanılmıştı.

![Oracle test ortamı](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>Sanal makine yapılandırması

Testler, sanal makine için aşağıdaki kurulumu kullandı:
* İşletim Sistemi:   
    RedHat Enterprise Linux 7,8 (WLE-ora01)
* Örnek türleri:   
    Test sırasında iki model kullanılmıştır – D32s_v3 ve D64s_v3
* Ağ arabirimi sayısı:   
    Bir (1) alt ağa yerleştirildi 3  
* Disklerinden   
    Oracle ikilileri ve işletim sistemi tek bir Premium diske yerleştirildi

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Azure NetApp Files yapılandırması
Testler aşağıdaki Azure NetApp Files yapılandırmalarını kullandı:   

* Kapasite havuzu boyutu:  
    Havuzun çeşitli boyutları yapılandırıldı: 4 TiB, 8 TiB, 16 TiB, 32 TiB 
* Hizmet düzeyi:  
    Ultra (128 MIB/sn, ayrılan birim kapasitesi başına 1 TiB)
* Dörtten  
    Bir ve iki toplu test değerlendirildi

### <a name="workload-generator"></a>İş yükü Oluşturucu 

Test edilen iş yükü SLOB 2.5.4.2 tarafından kullanıldı. SLOB (küçük Oracle kıyaslaması), g/ç alt sistemini bir SGA arabellekli fiziksel g/ç iş yüküne göre stres ve test etmek için tasarlanan Oracle alanındaki iyi bilinen bir iş yükü üreticisidir.  

SLOB 2.5.4.2, eklenebilir veritabanını (PDB) desteklemez. Bu nedenle, `setup.sh` ve `runit.sh` BETIKLERINE pdb desteği eklemek için bir değişiklik eklenmiştir.  

Testlerde kullanılan SLOB değişkenleri aşağıdaki bölümlerde açıklanmıştır.

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>İş yükü 80% SELECT, %20 GÜNCELLEŞTIRME | Rastgele g/ç – `slob.conf` değişkenler   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>İş yükü 100% SELECT | Sıralı g/ç – `slob.conf` değişkenler

`UPDATE_PCT=0`   
`SCAN_PCT=100`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

### <a name="database"></a>Veritabanı

Testler için kullanılan Oracle sürümü Oracle Database Enterprise Edition 19.3.0.0.

Oracle parametreleri aşağıdaki gibidir:  
* `sga_max_size`: 4096P
* `sga_target`: 4096
* `db_writer_processes`: 12
* `awr_pdb_autoflush_enabled`: true
* `filesystemio_options`: SETALL
* `log_buffer`: 134217728

SLOB veritabanı için bir PDB oluşturuldu.

Aşağıdaki diyagramda, dört SLOB Kullanıcı şeması barındırmak için oluşturulan 600 GB boyutundaki (20 veri dosyası, 30 GB), PERFIO adlı tablo alanı gösterilmektedir. Her Kullanıcı şeması, boyutu 125 GB idi.

![Oracle veritabanı](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>Performans ölçümleri

Amaç, GÇ performansını uygulamanın yaşadığı şekilde bildirmektir. Bu nedenle, bu makaledeki tüm diyagramlar Oracle veritabanı tarafından otomatik Iş yükü deposu (AWR) raporları aracılığıyla raporlanan ölçümleri kullanır. Diyagramlarda kullanılan ölçümler aşağıdaki gibidir:   

* **Ortalama GÇ Isteği sayısı/sn**   
    Yük Profili bölümünden ortalama okuma GÇ Isteği/sn ve ortalama yazma GÇ Isteği/sn toplamına karşılık gelir
* **Ortalama GÇ MB/sn**   
    Yük Profili bölümünden ortalama okuma GÇ MB/sn ve ortalama yazma GÇ MB/sn toplamına karşılık gelir
* **Ortalama okuma gecikmesi**   
    Mikrosaniyede "db dosyası sıralı okuma" Oracle bekleme olayının ortalama gecikme süresine karşılık gelir
* **İş parçacığı sayısı/şema**   
    Kullanıcı şeması başına SLOB iş parçacığı sayısına karşılık gelir

## <a name="performance-measurement-results"></a>Performans ölçümü sonuçları  

Bu bölümde, performans ölçümünün sonuçları açıklanmaktadır.

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Linux kNFS Istemcisi ve Oracle Direct NFS karşılaştırması

Bu senaryo bir Azure VM Standard_D32s_v3 (Intel E5-2673 v4 @ 2,30 GHz) üzerinde çalışıyor. İş yükü %75 SEÇIM ve %25 GÜNCELLEŞTIRME, genellikle rastgele g/ç ve veritabanı arabelleğinin yaklaşık% 7,5 ' i ile 

Aşağıdaki diyagramda gösterildiği gibi, Oracle DNFS istemcisi, normal Linux kNFS Istemcisinden daha fazla iş için 2.8 x 'e sahiptir:  

![Linux kNFS Istemcisi, Oracle Direct NFS ile karşılaştırılır](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

Aşağıdaki diyagramda okuma işlemleri için gecikme süresi eğrisi gösterilmektedir. Bu bağlamda, kNFS istemcisinin performans sorunu, istemci ile NFS sunucusu (Azure NetApp Files birimi) arasında kurulan tek NFS TCP yuvası bağlantısıdır.  

![Linux kNFS Istemcisi, Oracle Direct NFS gecikme eğrisi ile karşılaştırılır](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

DNFS istemcisi, yüzlerce TCP yuvası bağlantısı oluşturma özelliği nedeniyle paralellik özelliğinden yararlanarak daha fazla GÇ isteği/sn gönderim yapabiliyor. [Azure NetApp Files yapılandırma](#anf_config)bölümünde açıklandığı gibi, ayrılan her ek kapasite, ek bir 128MiB/s bant genişliğine izin verir. DNFS, 8-TiB kapasite seçimi tarafından uygulanan sınır olan 1 GiB/sn aktarım hızı üzerinden kullanıma hazır. Daha fazla kapasite verildiğinde, daha fazla üretilen iş daha fazla.

Aktarım hızı yalnızca bir tane göz önünde bulundurulmalıdır. Kullanıcı deneyimine yönelik birincil etki olan başka bir değerlendirme gecikmedir. Aşağıdaki diyagramda gösterildiği gibi, gecikme artışlarında kNFS ile DNFS ile daha hızlı bir şekilde beklenilebilir. 

![Linux kNFS Istemcisi, Oracle doğrudan NFS okuma gecikmesi ile karşılaştırılır](../media/azure-netapp-files/performance-oracle-read-latency.png)  

Histogramlar, veritabanı gecikmeleri hakkında harika öngörüler sağlar. Aşağıdaki diyagramda, en yüksek eşzamanlılık veri noktasında (32 iş parçacığı/şema) DNFS kullanılırken, kayıtlı "db dosyası sıralı okuma" açısına ait tüm bir görünüm verilmiştir. Aşağıdaki diyagramda gösterildiği gibi, tüm okuma işlemlerinin %47 ' i 512 mikrosaniye ve 1000 mikrosaniye arasında kabul edildi, ancak tüm okuma işlemlerinin %90 ' i 2 ms 'nin altında bir gecikme süresine sunuldu.

![Linux kNFS Istemcisi Oracle doğrudan NFS histoklarını karşılaştırma](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

Sonuç olarak, DNFS 'nin NFS 'deki bir Oracle veritabanı örneğinin performansını iyileştirmek için bir arada olması gerekir.

### <a name="single-volume-performance-limits"></a>Tek birim performans sınırları

Bu bölümde, rastgele g/ç ve sıralı g/ç içeren tek bir birimin performans sınırları açıklanmaktadır. 

#### <a name="random-io"></a>Rastgele g/ç

DNFS, 8 TB 'lik Azure NetApp Files performans kotası tarafından sağlanmaktan çok daha fazla bant genişliği tüketiyor. Azure NetApp Files birim kapasitesini, anlık bir değişiklik olan 16 TiB ile artırarak, birim bant genişliği miktarı 1024 MIB/sn 'den, 2X ve 2048 MiB/sn 'ye kadar artar. 

Aşağıdaki diyagramda %80 seçim ve %20 güncelleştirme iş yükünün bir yapılandırması ve veritabanı arabellek isabet oranı %8 ' i gösterilmektedir. SLOB, saniyede 200.000 adet NFS g/ç isteği için tek bir birimi de sürücüne sağlayabiliyor. Her işlemin 8 kıb boyutunda olduğu düşünüldüğünde, test edilen sistem ~ 200.000 GÇ isteği/sn veya 1600 MIB/s teslim edemedi.
 
![Oracle DNFS aktarım hızı](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

Aşağıdaki okuma gecikmesi eğrisi diyagramı, okuma üretilen işi arttıkça, gecikme süresinin 1 MS çizgisinin altında düzgün bir şekilde arttığı ve yaklaşık 165.000 ortalama okuma GÇ isteği/sn ' nin ortalama okuma gecikmesi, ~ 1,3 MS ile ilgili olduğunu gösterir.  Bu değer, Azure bulutundaki neredeyse tüm diğer teknolojilerde g/ç oranı unachievable için inanılmaz bir gecikme değeridir. 

![Oracle DNFS gecikme eğrisi](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>Sıralı g/ç  

Aşağıdaki diyagramda gösterildiği gibi, tüm g/ç 'ler, bir RMAN yedeklemesini veya tam bir tablo taramasını göz önünde bulundurarak, örneğin, ulaştıkları kadar bant genişliği gerektiren iş yükleri gibi bir şekilde rastgele değildir.  Daha önce açıklandığı gibi, ancak birim 32 TiB olarak yeniden boyutlandırıldığı için aynı yapılandırmayı kullanarak, aşağıdaki diyagramda tek bir Oracle DB örneğinin yukarı doğru 3.900 MB/s aktarım hızını ve Azure NetApp Files birimin 32 TB (128 MB/s * 32 = 4096 MB/s) performans kotasına çok yakın şekilde depolayabileceği gösterilmektedir.

![Oracle DNFS g/ç](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

Özet olarak, Azure NetApp Files Oracle veritabanlarınızı buluta almanıza yardımcı olur. Veritabanı talep edildiğinde performansı sağlar. Birim kotayı istediğiniz zaman dinamik olarak ve kesintiye uğramamış olarak yeniden boyutlandırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure NetApp Files için performans kıyaslamaları test önerileri](azure-netapp-files-performance-metrics-volumes.md)
- [Linux için performans karşılaştırmaları](performance-benchmarks-linux.md)