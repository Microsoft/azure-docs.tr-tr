---
title: SQL Server dağıtımı için Azure NetApp Files kullanmanın avantajları | Microsoft Docs
description: SQL Server dağıtımı için Azure NetApp Files kullanma hakkında ayrıntılı maliyet analizi performansı avantajlarını gösterir.
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
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: 46fe7570b7b9ea9446918d407dbe87596b8d0496
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863913"
---
#  <a name="benefits-of-using-azure-netapp-files-for-sql-server-deployment"></a>SQL Server dağıtımı için Azure NetApp Files kullanmanın avantajları

Azure NetApp Files, depolama çözümlerini engelle ile karşılaştırıldığında, toplam sahip olma maliyetinin (TCO) SQL Server azaltır.  Blok depolama ile sanal makineler, disk işlemleri için g/ç ve bant genişliği sınırlarına sahiptir, tek başına ağ bant genişliği limitleri yalnızca Azure NetApp Files karşı uygulanır.  Diğer bir deyişle, Azure NetApp Files VM düzeyindeki g/ç sınırları uygulanmaz. Bu g/ç sınırları olmadan, Azure NetApp Files bağlı daha küçük sanal makinelerde çalışan SQL Server, çok daha büyük sanal makinelerde çalışan SQL Server da gerçekleştirebilir. Örnek boyutlandırma, önceki fiyat etiketinin %25 ' i ile işlem maliyetlerini azaltır.  *İşlem maliyetlerini Azure NetApp Files azaltabilirsiniz.*  

Ancak, işlem maliyetleri SQL Server lisans maliyetleriyle karşılaştırıldığında küçüktür.  Microsoft SQL Server [lisanslama](https://download.microsoft.com/download/B/C/0/BC0B2EA7-D99D-42FB-9439-2C56880CAFF4/SQL_Server_2017_Licensing_Datasheet.pdf) fiziksel çekirdek sayısına bağlıdır. Bu nedenle, azalan örnek boyutu yazılım lisanslama için daha büyük bir maliyet tasarrufu sağlar. *Yazılım lisans maliyetlerini Azure NetApp Files azaltabilirsiniz.*

Depolama alanının maliyeti, veritabanının gerçek boyutuna bağlı olarak değişkendir. Seçili depolama alanı ne olursa olsun kapasitenin, yönetilen bir disk veya dosya paylaşımının olmasına bakılmaksızın maliyeti vardır.  Veritabanı boyutları arttıkça ve depolama maliyeti arttıkça, depolama, genel maliyeti etkileyen TCO artışına katkıda bulunur.  Bu nedenle, onaylama aşağıdaki gibi olarak ayarlanır: *Azure NetApp Files ile SQL Server dağıtım maliyetlerini azaltabilirsiniz.* 

Bu makalede SQL Server dağıtımı için Azure NetApp Files kullanma hakkında ayrıntılı maliyet analizi ve performans avantajları gösterilmektedir. Yalnızca daha küçük örneklerin değil, veritabanı çalışmasını sağlamak için yeterli CPU yoktur, *çoğu durumda, daha küçük örnekler, Azure NetApp Files nedeniyle daha büyük, disk tabanlı karşılıklarından daha fazla performansa sahiptir.* 

## <a name="detailed-cost-analysis"></a>Ayrıntılı maliyet analizi 

Bu bölümdeki iki grafik kümesi TCO örneğini göstermektedir.  En iyi fiyat kapasitesi performansına ulaşmak için, yönetilen disklerin sayısı ve türü, Azure NetApp Files hizmet düzeyi ve her senaryonun kapasitesi seçilmiştir.  Her grafik gruplandırılmış makinelerden oluşur (Azure NetApp Files ile D16, yönetilen diskle D64 ile karşılaştırıldığında) ve fiyatlar her makine türü için bölünür.  

İlk grafik kümesi, 1-TiB veritabanı boyutu kullanarak çözümün genel maliyetini gösterir. Bu, D16s_v3 D64, D8 ile D32 ve D16 D4 ile karşılaştırır. Her yapılandırma için öngörülen IOPS, yeşil veya sarı bir satırla belirtilir ve sağ taraftaki Y eksenine karşılık gelir.

[![1-TiB veritabanı boyutunu kullanarak çözümün genel maliyetini gösteren grafik. ](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png#lightbox)


İkinci grafik kümesi, 50-TiB veritabanı kullanan genel maliyeti gösterir. Karşılaştırmalar, diğer bir deyişle, Azure NetApp Files ile karşılaştırıldığında aynı şekilde D16 ile karşılaştırılır. 

[![50-TiB veritabanı boyutunu kullanarak genel maliyeti gösteren grafik. ](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png#lightbox)
 
## <a name="performance-and-lots-of-it"></a>Performans ve çok sayıda  

Önemli maliyet azaltma onayını sağlamak için çok fazla performans gerekir. Genel Azure envanteri destek 80.000 disk ıOPS ' deki en büyük örnek örnek olarak vardır. Tek bir Azure NetApp Files birimi 80.000 veritabanı ıOPS elde edebilir ve D16 gibi örnekler aynı şekilde tüketebilir. Normalde 25.600 disk ıOPS özelliğine sahip olan D16, D64 boyutunun %25 ' i olur.  D64s_v3, 80.000 disk ıOPS özelliğine sahiptir ve bu nedenle mükemmel bir üst düzey karşılaştırma noktası sunar.

D16s_v3, bir Azure NetApp Files birimini 80.000 veritabanı ıOPS 'ye verebilir. SQL Storage kıyaslama (SSB) sınama aracı tarafından kanıtlanmış olduğu gibi, D16 örneği, D64 örneğinden diske bu ulaşılabilir daha büyük bir iş yükü %125 ' i elde.  Araçla ilgili ayrıntılar için [SSB test aracı](#ssb-testing-tool) bölümüne bakın.

1-TiB çalışma kümesi boyutu ve %80 okuma, %20 güncelleştirme SQL Server iş yükü ve D örnek sınıfındaki çoğu örnek için performans özellikleri ölçülüyor; en çok, D2 ve D64 örneklerinin kendileri de test edilmesinden dışlanıyor. Daha önce, hızlandırılmış ağı desteklemediğinden ve karşılaştırma noktası olduğundan, ilki kalmadı. Sırasıyla D4s_v3, D8s_v3, D16s_v3 ve D32s_v3 sınırlarını anlamak için aşağıdaki grafiğe bakın.  Yönetilen disk depolama testleri grafikte gösterilmez. Karşılaştırma değerleri, D sınıfı örnek türü için doğrudan [Azure sanal makine sınırları tablosundan](../virtual-machines/dv3-dsv3-series.md) çizilir.

Azure NetApp Files, D sınıfındaki her bir örnek iki kez daha büyük örneklerin disk performansı özelliklerini karşılayabilir veya aşabilir.  *Azure NetApp Files ile yazılım lisans maliyetlerini önemli ölçüde azaltabilirsiniz.*  

* %75 CPU kullanımında D4, D16 'ın disk özelliklerine eşleşti.  
    * D16, 25.600 disk ıOPS ile sınırlıdır.  
* %75 CPU kullanımında D8, D32 'ın disk özelliklerine eşleşti.  
    * D32, 51.200 disk ıOPS ile sınırlıdır.  
* %55 CPU kullanımında D16, D64 'ın disk özelliklerine eşleşti.  
    * D64, 80.000 disk ıOPS ile sınırlıdır.  
* %15 CPU kullanımı D32, D64 'ın disk özellikleri de ile eşleşti.  
    * Yukarıda belirtilen D64 oranı 80.000 disk ıOPS ile sınırlıdır.  

### <a name="s3b-cpu-limits-test--performance-versus-processing-power"></a>S3B CPU sınırları test – performans ve işlem gücü karşılaştırması

Aşağıdaki diyagramda S3B CPU sınırları testi özetlenmektedir:

![Azure NetApp Files üzerinden tek örnekli SQL Server ortalama CPU yüzdesini gösteren diyagram.](../media/azure-netapp-files/solution-sql-server-single-instance-average-cpu.png)

Ölçeklenebilirlik yalnızca hikayenin bir parçasıdır. Diğer bölüm gecikmedir.  Daha küçük sanal makinelerin g/ç tarifelerinin çok daha yüksek olduğu bir şeydir. Bu, aşağıda gösterildiği gibi düşük tek basamaklı gecikme süreleriyle başka bir şeydir.  

* D4 yılının 26.000 IOPS, 2,3-ms gecikme süresiyle Azure NetApp Files karşı.  
* D8 yılının 51.000 IOPS, 2,0-ms gecikme süresiyle Azure NetApp Files karşı.  
* D16 yılının 88.000 IOPS, 2,8-ms gecikme süresiyle Azure NetApp Files karşı.
* D32 yılının 80.000 IOPS, 2,4-ms gecikme süresiyle Azure NetApp Files karşı.  

### <a name="s3b-per-instance-type-latency-results"></a>Örnek başına S3B türü gecikme sonuçları

Aşağıdaki diyagramda, Azure NetApp Files üzerinden tek örnekli SQL Server gecikmesi gösterilmektedir:

![Azure NetApp Files üzerinde tek örnekli SQL Server gecikmesini gösteren diyagram.](../media/azure-netapp-files/solution-sql-server-single-instance-latency.png)

## <a name="ssb-testing-tool"></a>SSB test aracı 
 
[TPC-E](http://www.tpc.org/tpce/) benchişaretleme Aracı, tasarım, *depolama* yerine *Hesaplama* , işleme görevi. Bu bölümde gösterilen test sonuçları, SQL depolama kıyaslaması (SSB) adlı bir stres testi aracına dayalıdır.  SQL Server depolama alanı kıyaslaması, [SLOB2 Oracle benchişaretleme aracına](https://kevinclosson.net/slob/)benzer bir OLTP iş yükünün benzetimini yapmak için büyük ölçekli SQL yürütmesini bir SQL Server veritabanına göre kullanabilir. 

SSB Aracı, diyor deyimlerini doğrudan Azure sanal makinesi içinde çalışan SQL Server veritabanına veren bir SELECT ve UPDATE kullanımlı iş yükü oluşturur.  Bu proje için SSB iş yükleri, 1 ' den 100 ' e kadar, Kullanıcı sayısı başına 15 dakikada 10 veya 12 Ara noktayla SQL Server.  Bu çalıştırmaların tüm performans ölçümleri, her senaryo için üç kez çalıştırılan yinelenebilirlik SSB için perfmon 'un görünüm noktasından yapılmıştır. 

Testlerin kendisi %80 SELECT ve %20 GÜNCELLEŞTIRME ekstresi olarak yapılandırılmıştır, bu nedenle 90% Random Read.  SSB 'nin oluşturduğu veritabanının kendisi 1000 GB idi. Bu, Kullanıcı tablosu başına 15 Kullanıcı tablosu ve 9.000.000 satırdan ve satır başına 8192 baytından oluşur. 

SSB kıyaslama açık kaynaklı bir araçtır.  [SQL Storage kıyaslama GitHub sayfasından](https://github.com/NetApp/SQL_Storage_Benchmark.git)ücretsiz olarak sunulmaktadır.  


## <a name="in-summary"></a>Özet olarak  

Azure NetApp Files ile, toplam sahip olma maliyetinizi önemli ölçüde azaltırken SQL Server performansını artırabilirsiniz. 

## <a name="next-steps"></a>Sonraki Adımlar

* [Azure NetApp Files için SMB birimi oluşturma](azure-netapp-files-create-volumes-smb.md) 
* [Azure NetApp Files kullanan çözüm mimarileri: SQL Server](azure-netapp-files-solution-architectures.md#sql-server) 

