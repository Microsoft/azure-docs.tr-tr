---
title: Azure NetApp Files için SMB performansı hakkında SSS | Microsoft Docs
description: Azure NetApp Files için SMB performansı hakkında sık sorulan soruları yanıtlar.
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
ms.openlocfilehash: 1ec58b056bd610773500c8ace1fb12d268b980e0
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726727"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Azure NetApp Files için SMB performansı hakkında SSS

Bu makalede, Azure NetApp Files yönelik SMB performansı en iyi uygulamaları hakkında sık sorulan sorular (SSS) yanıtlanmaktadır.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>SMB paylaşımlarında SMB çok kanallı etkin mi? 

Evet, çok kanallı SMB varsayılan olarak etkindir ve bir değişiklik, Ocak 2020 ' de erken olarak gerçekleştirilir. Mevcut SMB birimlerinde önceden geçen tüm SMB paylaşımları özelliği etkinleştirilmiştir ve yeni oluşturulan tüm birimlerde özelliği oluşturma sırasında de etkinleştirilir. 

Çok kanallı SMB işlevselliğinin avantajlarından yararlanmak için, özellik etkinleştirmeden önce kurulan tüm SMB bağlantıları sıfırlanmalıdır. Sıfırlamak için SMB paylaşımının bağlantısını kesebilir ve yeniden bağlanabilirsiniz.

## <a name="is-rss-supported"></a>RSS destekleniyor mu?

Evet, Azure NetApp Files alma tarafı ölçeklendirmeyi (RSS) destekler.

SMB çok kanallı özelliği etkinken, bir SMB3 istemcisi, tek RSS özellikli bir ağ arabirimi kartı (NIC) üzerinden Azure NetApp Files SMB sunucusuna birden fazla TCP bağlantısı kurar. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Hangi Windows sürümleri çok kanallı SMB 'yi destekliyor?

En iyi performansı etkinleştirmek için Windows 2012 ' den itibaren çok kanallı SMB 'yi destekliyordu.  Ayrıntılar için bkz. SMB [çok kanallı ve çok KANALLı SMB temelleri](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0) [dağıtma](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) . 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Azure Sanal makinem RSS 'yi destekliyor mu?

Azure sanal makinenizin NIC 'lerinin RSS 'yi desteklemesini sağlamak için komutu `Get-SmbClientNetworkInterface` aşağıdaki gibi çalıştırın ve alanı kontrol edin `RSS Capable` : 

![Azure sanal makinesi için RSS çıkışını gösteren ekran görüntüsü.](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp Files doğrudan erişimli SMB 'yi destekliyor mu?

Hayır, Azure NetApp Files doğrudan erişimli SMB desteklemez. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Çok kanallı SMB avantajı nedir? 

Çok kanallı SMB özelliği, bir SMB3 istemcisinin tek bir ağ arabirimi kartı (NIC) veya birden fazla NIC üzerinden bağlantı havuzu kurmasını ve bunları tek bir SMB oturumu için istekleri gönderecek şekilde kullanmasını sağlar. Buna karşılık, tasarım, SMB1 ve SMB2, istemcinin bir bağlantı kurmasını ve belirli bir oturum için tüm SMB trafiğini bu bağlantı üzerinden göndermesini gerektirir. Bu tek bağlantı, tek bir istemciden elde edilebilir genel protokol performansını sınırlandırır.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>SMB için istemcimde birden çok NIC yapılandırmam gerekir mi?

Hayır. SMB istemcisi, SMB sunucusu tarafından döndürülen NIC sayısıyla eşleştirecektir.  Her depolama birimine bir ve yalnızca bir depolama uç noktasından erişilebilir.  Bu, belirli bir SMB ilişkisi için yalnızca bir NIC 'nin kullanılacağı anlamına gelir.  

Aşağıdaki çıktıda gösterildiği gibi `Get-SmbClientNetworkInterace` , sanal makinenin 2 ağ arabirimi vardır--15 ve 12.  Aşağıdaki komutta gösterildiği gibi `Get-SmbMultichannelConnection` , ıkı RSS özellıklı NIC olsa da, yalnızca SMB paylaşımıyla bağlantılı olarak arabirim 12 kullanılır; arabirim 15 kullanımda değildir.

![RSS özellikli NIC 'ler için çıktıyı gösteren screeshot.](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Azure 'da NIC ekibi oluşturma destekleniyor mu?

NIC ekibi oluşturma, Azure 'da desteklenmez. Azure sanal makinelerinde birden çok ağ arabirimi desteklense de, fiziksel bir yapı yerine bir mantıksal temsil eder. Bu nedenle, hata toleransı vermez.  Ayrıca, bir Azure sanal makinesi için kullanılabilen bant genişliği, tek bir ağ arabirimi değil, makinenin kendisi için hesaplanır.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Çok kanallı SMB gibi performans nedir?

Aşağıdaki testler ve grafiklerde, tek örnekli iş yükleri üzerinde çok kanallı SMB 'nin gücü gösterilmektedir.

### <a name="random-io"></a>Rastgele g/ç  

İstemcide çok kanallı SMB devre dışı bırakıldığında, FIO ve 40 GiB çalışma kümesi kullanılarak saf 4 KiB okuma ve yazma testleri gerçekleştirildi.  SMB paylaşımının her bir test arasında ayrılması,,,,,,,,,,,,,,,,,,,,,, `1` ,,, `4` , `8` `16` `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` Testler, varsayılan ayarının `4` g/ç yoğunluklu iş yükleri için yeterli olduğunu gösterir; ' ı arttırın `8` ve göz `16` ardı edilebilir etkiye sahip olur. 

Bu komut, `netstat -na | findstr 445` ile arasında ve arasında yapılan artışlarla daha fazla bağlantı kurulmasını sağlar `1` `4` `8` `16` .  Her test sırasında her bir sınama sırasında SMB için dört CPU çekirdeği tam olarak kullanıldı `Per Processor Network Activity Cycles` (Bu makaleye dahil değildir.)

![Çok kanallı SMB ile rastgele g/ç karşılaştırması gösteren grafik.](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Azure sanal makinesi, SMB (veya NFS) depolama g/ç sınırlarını etkilemez.  Aşağıdaki grafikte gösterildiği gibi, D32ds örnek türünün önbelleğe alınmış depolama ıOPS için 308.000 sınırlı bir oranı ve önbelleğe alınmamış depolama ıOPS için 51.200 vardır.  Ancak, yukarıdaki grafik SMB üzerinde önemli ölçüde g/ç gösterir.

![Rastgele g/ç karşılaştırma testini gösteren grafik.](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>Sıralı GÇ 

Daha önce açıklanan rastgele g/ç testlerine benzer testler, 64-KiB sıralı g/ç ile gerçekleştirildi. Her RSS ağ arabirimi başına istemci bağlantı sayısı arttıkça 4 ' ün üzerinde artım rastgele g/ç üzerinde belirgin bir etkiye sahip olsa da, aynı sıra sıralı g/ç için geçerli değildir. Aşağıdaki grafikte gösterildiği gibi, her artış okuma aktarım hızı ile ilgili bir artış ile ilişkilendirilir. Azure tarafından her örnek türü/boyutu için yerleştirilmiş olan ağ bant genişliği kısıtlamaları nedeniyle yazma performansı düz kaldı. 

![Üretilen iş testi karşılaştırmayı gösteren grafik.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure, her bir sanal makine türü/boyutu için ağ oranı sınırlarını koyar. Hız limiti yalnızca giden trafiğe uygulanır. Bir sanal makinede bulunan NIC 'lerin sayısı, makinenin kullanabildiği toplam bant genişliği miktarına uygun değildir.  Örneğin, D32ds örnek türü, 16.000 Mbps (2.000 MiB/sn) ağ sınırına sahiptir.  Yukarıdaki sıralı grafik gösterdiği gibi sınır, giden trafiği (yazma) etkiler ancak çok kanallı okumaları etkilemez.

![Sıralı g/ç karşılaştırma testini gösteren grafik.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="what-performance-is-expected-with-a-single-instance-with-a-1-tb-dataset"></a>1 TB 'lık veri kümesiyle tek bir örnekle hangi performansı bekleniyor?

Okuma/yazma karışımları ile iş yükleri hakkında daha ayrıntılı bilgi sağlamak için aşağıdaki iki grafik, 1 TB 'LıK bir veri kümesiyle ve çok kanallı SMB ile 4 TB 'lik tek bir ultra hizmet düzeyi bulut 50 hacminin performansını gösterir. Ağ bant genişliğinin () tam kullanımını sağlamak için en iyi ıodepth 16 ve esnek GÇ (FIO) parametreleri kullanılmıştır `numjobs=16` .

Aşağıdaki grafik, 4k rastgele g/ç 'nin sonuçlarını tek bir VM örneğiyle ve okuma/yazma karışımından %10 aralıklarla gösterir:

![Windows 2019 Standard _D32ds_v4 4K rastgele GÇ testi gösteren grafik.](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

Aşağıdaki grafikte, sıralı g/ç için sonuçlar gösterilmektedir:

![Windows 2019 Standard _D32ds_v4 64K sıralı işleme gösteren grafik.](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="what-performance-is-expected-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>1 TB 'lık bir veri kümesiyle 5 sanal makine kullanılarak ölçeklendirilirken hangi performans beklenir?

5 VM içeren bu sınamalar, her bir işlemle kendi dosyasına yazılırken aynı test ortamını tek VM ile kullanır.

Aşağıdaki grafik rastgele g/ç için sonuçları gösterir:

![Windows 2019 Standard _D32ds_v4 4K 5-örnek randio GÇ testi gösteren grafik.](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

Aşağıdaki grafikte, sıralı g/ç için sonuçlar gösterilmektedir:

![Windows 2019 Standard _D32ds_v4 64K 5 örnek sıralı aktarım hızını gösteren grafik.](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-do-you-monitor-hyper-v-ethernet-adapters-and-ensure-that-you-maximize-network-capacity"></a>Hyper-V Ethernet bağdaştırıcılarını nasıl izleyebilir ve ağ kapasitesini en üst düzeye çıkarmanızı sağlayabilirsiniz.  

FIO ile test ederken kullanılan bir strateji ayarlanmanı `numjobs=16` . Bu durumda, Microsoft Hyper-V ağ bağdaştırıcısını en üst düzeye çıkarmak için her bir işi 16 özel örneğe çatalın.

**Performans İzleyicisi ' ni > ağ arabirimi > Microsoft Hyper-V ağ bağdaştırıcısına sayaç eklemek >**, Windows Performans İzleyicisi 'ndeki bağdaştırıcıların her birinde etkinlik olup olmadığını kontrol edebilirsiniz.

![Performans Izleyicisi ekleme sayacı arabirimini gösteren ekran görüntüsü.](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

Birimlerinizde veri trafiği çalışmaya başladıktan sonra, bağdaştırıcılarınızı Windows performans Izleyicisi 'nde izleyebilirsiniz. Bu 16 sanal bağdaştırıcıların tümünü kullanmıyorsanız, ağ bant genişliği kapasitenizi en üst düzeye çıkarmayın olabilirsiniz.

![Performans Izleyicisi çıkışını gösteren ekran görüntüsü.](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="is-accelerated-networking-recommended"></a>Hızlandırılmış ağ önerilir mi?

En yüksek performans için mümkün olan yerlerde [hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-powershell.md) yapılandırmanız önerilir. Aşağıdaki noktaları göz önünde bulundurun:  

* Azure portal, bu özelliği destekleyen sanal makineler için varsayılan olarak hızlandırılmış ağa izin verebilir.  Ancak, anormal ve benzer yapılandırma araçları gibi diğer dağıtım yöntemleri de olmayabilir.  Hızlandırılmış ağı etkinleştirme hatası, makinenin performansını hobble sağlayabilir.  
* Bir sanal makinenin ağ arabiriminde hızlandırılmış ağ etkinleştirilmemişse, örnek türü veya boyutu için destek olmaması gerekir, daha büyük örnek türleriyle devre dışı kalır. Bu durumlarda el ile müdahale etmeniz gerekir.

## <a name="are-jumbo-frames-supported"></a>Jumbo çerçeveleri destekleniyor mu?

Azure sanal makinelerinde jumbo çerçeveleri desteklenmez.

## <a name="is-smb-signing-supported"></a>SMB Imzalama destekleniyor mu? 

SMB protokolü, dosya ve yazıcı paylaşımı ve uzak Windows yönetimi gibi diğer ağ işlemleri için temel sağlar. Geçiş sırasında SMB paketlerini değiştiren bağlantıyı izinsiz izleme saldırıları engellemek için SMB protokolü, SMB paketlerinin dijital imzalanmasını destekler. 

SMB Imzalama, Azure NetApp Files tarafından desteklenen tüm SMB protokol sürümleri için desteklenir. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>SMB Imzalanmasının performans etkisi nedir?  

SMB Imzalama, SMB performansının üzerine bir çok değerli etkiye sahiptir. Performans düşüşünün diğer olası nedenleri arasında, her bir paketin dijital imzalanması, aşağıdaki Perfmon çıktısı olarak ek istemci tarafı CPU kullanır. Bu durumda, SMB Imzalama da dahil olmak üzere SMB 'den sorumlu olan çekirdek 0 görünür.  Önceki bölümde çok kanallı sıralı okuma üretilen iş numaralarıyla bir karşılaştırma SMB Imzasının, 875MiB/sn 'den yaklaşık 250MiB/sn 'ye kadar olan genel aktarım hızını azalttığını gösterir. 

![SMB Imzalama performansı etkisini gösteren grafik.](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)

## <a name="what-is-the-anticipated-impact-of-smb-encryption-on-client-workloads"></a>İstemci iş yükleri üzerinde SMB şifrelemenin beklenen etkisi nedir?

Bkz. [SMB şifreleme SSS](azure-netapp-files-faqs.md#smb_encryption_impact).

## <a name="next-steps"></a>Sonraki adımlar  

- [Azure NetApp Files hakkında SSS](azure-netapp-files-faqs.md)
- Azure NetApp Files ile SMB dosya paylaşımları kullanma hakkında [SMB Iş yükleri için Azure NetApp Files: yönetilen kurumsal dosya paylaşımları](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) ' na bakın.