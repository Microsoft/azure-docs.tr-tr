---
title: Microsoft Azure 'da Perfinsıghts kullanma | Microsoft Docs
description: Windows VM performans sorunlarını gidermek için Perfınsıghts 'in nasıl kullanılacağını öğrenir.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 783b479dd3e5f429516799d7d3ea82f363cac2ec
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058169"
---
# <a name="how-to-use-perfinsights"></a>PerfInsights’ı kullanma

[Perfinsıghts](https://aka.ms/perfinsightsdownload) , tanılama verilerini toplayıp çözümleyen ve Azure 'da Windows sanal makine performans sorunlarını gidermeye yardımcı olacak bir rapor sunan bir kendi kendine yardım tanılama aracıdır. Perfinsıghts, sanal makinelerde tek başına bir araç olarak, [Azure sanal makineler Için Performans Tanılama](performance-diagnostics.md)kullanılarak veya [Azure Performans Tanılama VM Uzantısı](performance-diagnostics-vm-extension.md)' nı yükleyerek doğrudan portaldan çalıştırılabilir.

Sanal makinelerle ilgili performans sorunları yaşıyorsanız, desteğe başvurmadan önce bu aracı çalıştırın.

## <a name="supported-troubleshooting-scenarios"></a>Desteklenen sorun giderme senaryoları

Perfinsıghts çeşitli bilgi türlerini toplayıp analiz edebilir. Aşağıdaki bölümlerde yaygın senaryolar ele alınmaktadır.

### <a name="quick-performance-analysis"></a>Hızlı performans analizi

Bu senaryo, disk yapılandırmasını ve aşağıdakiler de dahil olmak üzere diğer önemli bilgileri toplar:

-   Olay günlükleri

-   Tüm gelen ve giden bağlantılar için ağ durumu

-   Ağ ve güvenlik duvarı yapılandırma ayarları

-   Şu anda sistemde çalışmakta olan tüm uygulamalar için görev listesi

-   Microsoft SQL Server veritabanı yapılandırma ayarları (VM, SQL Server çalıştıran bir sunucu olarak tanımlanmışsa)

-   Depolama güvenilirlik sayaçları

-   Önemli Windows düzeltmeleri

-   Yüklü filtre sürücüleri

Bu, sistemi etkilememelidir olmayan bir bilgi koleksiyonudur. 

>[!Note]
>Bu senaryo, aşağıdaki senaryolardan her birine otomatik olarak eklenir:

### <a name="benchmarking"></a>Karşılaştırmalı

Bu senaryo, VM 'ye bağlı tüm sürücüler için [DiskSpd](https://github.com/Microsoft/diskspd) kıyaslama TESTINI (ıOPS ve Mbps) çalıştırır. 

> [!Note]
> Bu senaryo, sistemi etkileyebilir ve canlı bir üretim sisteminde çalıştırılmamalıdır. Gerekirse, herhangi bir sorunu önlemek için bu senaryoyu adanmış bir bakım penceresinde çalıştırın. Bir izleme veya kıyaslama testinin neden olduğu artan bir iş yükü, sanal makinenizin performansını olumsuz yönde etkileyebilir.
>

### <a name="performance-analysis"></a>Performans Analizi

Bu senaryo, RuleEngineConfig. json dosyasında belirtilen sayaçları kullanarak bir [performans sayacı](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) izlemesi çalıştırır. VM, SQL Server çalıştıran bir sunucu olarak tanımlanmışsa, bir performans sayacı izlemesi çalıştırılır. Bu, RuleEngineConfig. json dosyasında bulunan sayaçlar kullanılarak yapılır. Bu senaryo, performans tanılama verilerini de içerir.

### <a name="azure-files-analysis"></a>Azure dosyaları Analizi

Bu senaryoda, bir ağ izleme ile birlikte özel bir performans sayacı yakalama çalıştırılır. Yakalama tüm sunucu Ileti bloğu (SMB) istemci paylaşımları sayaçlarını içerir. Yakalama 'nın parçası olan bazı anahtar SMB istemci paylaşımıyla ilgili performans sayaçları aşağıda verilmiştir:

| **Tür**     | **SMB istemci paylaşımları sayacı** |
|--------------|-------------------------------|
| IOPS         | Veri Isteği/sn             |
|              | Okuma Isteği/sn             |
|              | Yazma Isteği/sn            |
| Gecikme süresi      | Ort. sn/veri Isteği         |
|              | Ort. sn/okuma                 |
|              | Ort. sn/yazma                |
| GÇ boyutu      | Ort. Bayt/veri Isteği       |
|              | Ort. Bayt/okuma               |
|              | Ort. Bayt/yazma              |
| Aktarım hızı   | Veri bayt/sn                |
|              | Okuma Bayt/sn                |
|              | Yazma Bayt/sn               |
| Kuyruk Uzunluğu | Ort. Okuma sırası uzunluğu        |
|              | Ort. Yazma kuyruğu uzunluğu       |
|              | Ort. Veri kuyruğu uzunluğu        |

### <a name="advanced-performance-analysis"></a>Gelişmiş performans analizi

Gelişmiş bir performans analizi çalıştırdığınızda, paralel olarak çalıştırılacak izlemeleri seçersiniz. İsterseniz, tümünü çalıştırabilirsiniz (performans sayacı, XPerf, ağ ve StorPort).  

> [!Note]
> Bu senaryo, sistemi etkileyebilir ve canlı bir üretim sisteminde çalıştırılmamalıdır. Gerekirse, herhangi bir sorunu önlemek için bu senaryoyu adanmış bir bakım penceresinde çalıştırın. Bir izleme veya kıyaslama testinin neden olduğu artan bir iş yükü, sanal makinenizin performansını olumsuz yönde etkileyebilir.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Perfinsıghts tarafından ne tür bilgiler toplandı?

Windows VM, diskler veya depolama havuzları yapılandırması, performans sayaçları, Günlükler ve çeşitli izlemeler hakkında bilgiler toplanır. Bu, kullandığınız performans senaryosuna bağlıdır. Aşağıdaki tabloda ayrıntılar verilmektedir:

|Toplanan veriler                              |  |  | Performans senaryoları |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Hızlı performans analizi | Karşılaştırmalı | Performans Analizi | Azure dosyaları Analizi | Gelişmiş performans analizi |
| Olay günlüklerinden bilgi       | Evet                        | Evet                                | Evet                      | Evet                  | Evet                  |
| Sistem bilgileri                | Evet                        | Evet                                | Evet                      | Evet                  | Evet                  |
| Birim eşleme                        | Evet                        | Evet                                | Evet                      | Evet                  | Evet                  |
| Disk eşleme                          | Evet                        | Evet                                | Evet                      | Evet                  | Evet                  |
| Çalışan görevler                     | Evet                        | Evet                                | Evet                      | Evet                  | Evet                  |
| Depolama güvenilirlik sayaçları      | Evet                        | Evet                                | Evet                      | Evet                  | Evet                  |
| Depolama bilgileri               | Evet                        | Evet                                | Evet                      | Evet                  | Evet                  |
| Fsutil çıkışı                     | Evet                        | Evet                                | Evet                      | Evet                  | Evet                  |
| Filtre sürücüsü bilgileri                | Evet                        | Evet                                | Evet                      | Evet                  | Evet                  |
| Netstat çıkışı                    | Evet                        | Evet                                | Evet                      | Evet                  | Evet                  |
| Ağ yapılandırması             | Evet                        | Evet                                | Evet                      | Evet                  | Evet                  |
| Güvenlik duvarı yapılandırması            | Evet                        | Evet                                | Evet                      | Evet                  | Evet                  |
| SQL Server yapılandırması          | Evet                        | Evet                                | Evet                      | Evet                  | Evet                  |
| Performans Tanılama izlemeleri *  | Evet                        | Evet                                | Evet                      | Evet                  | Evet                  |
| Performans sayacı izleme * *      |                            |                                    | Evet                      |                      | Evet                  |
| SMB sayacı izleme * *              |                            |                                    |                          | Evet                  |                      |
| SQL Server sayacı izleme * *       |                            |                                    | Evet                      |                      | Evet                  |
| XPerf izleme                       |                            |                                    |                          |                      | Evet                  |
| StorPort izleme                    |                            |                                    |                          |                      | Evet                  |
| Ağ izleme                     |                            |                                    |                          | Evet                  | Evet                  |
| DiskSpd kıyaslama izlemesi * * *       |                            | Evet                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Performans Tanılama izlemesi (*)

Verilerin toplanması ve sürekli performans sorunlarını tanılamak için arka planda kural tabanlı bir altyapı çalıştırır. Şu kurallar Şu anda destekleniyor:

- HighCpuUsage kuralı: Yüksek CPU kullanım sürelerini algılar ve bu dönemler sırasında en fazla CPU kullanımı tüketicilerini gösterir.
- HighDiskUsage kuralı: Fiziksel disklerde yüksek disk kullanımı sürelerini algılar ve bu dönemler sırasında en iyi disk kullanımı tüketicilerini gösterir.
- HighResolutionDiskMetric kuralı: Her fiziksel disk için ıOPS, verimlilik ve 50 milisaniye başına g/ç gecikme süresi ölçümlerini gösterir. Disk azaltma sürelerini hızlı bir şekilde belirlemesine yardımcı olur.
- HighMemoryUsage kuralı: Yüksek bellek kullanım dönemlerini algılar ve bu dönemler sırasında en fazla bellek kullanım tüketicilerini gösterir.

> [!NOTE] 
> Şu anda, .NET Framework 4,5 veya sonraki sürümleri içeren Windows sürümleri desteklenir.

### <a name="performance-counter-trace-"></a>Performans sayacı izleme (* *)

Aşağıdaki performans sayaçlarını toplar:

- \ İşlem, \ işlemci, \ bellek, \Iş parçacığı, \Fiziksel disk ve \ LogicalDisk
- \Cache\Dirty sayfaları, \Cache\Lazy yazma boşaltmaları/sn, \Server\Pool disk belleğine alma, başarısızlık ve \Server\Pool disk belleği sorunları
- \Network Interface, \ıpv4 Interface \Datagram, \ıpv4 IPv6 \ datagramları, \TCPv4\Segments, \TCPv6\Segments, \Network bağdaştırıcısı, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \Udpv6\datagram, \TCPv4\Connection, \TCPv6\Connection gibi seçili sayaçlar. Ağ QoS ilkesi paketleri, \ Işlemci başına ağ arabirimi kartı etkinliği ve \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>SQL Server örnekleri için
- \SQL Server:Buffer Manager, \SQLServer:Resource Pool Stats, and \SQLServer:SQL Statistics\
- \SQLServer: kilitler, \SQLServer: genel, Istatistikler
- \SQLServer: erişim yöntemleri

#### <a name="for-azure-files"></a>Azure dosyaları için
\SMB Istemci paylaşımları

### <a name="diskspd-benchmark-trace-"></a>DiskSpd kıyaslama izlemesi (* * *)
DiskSpd g/ç iş yükü testleri (işletim sistemi diski [yazma] ve havuz sürücüleri [okuma/yazma])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>VM 'niz üzerinde Perfinsıghts aracını çalıştırın

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Aracı çalıştırmadan önce neleri bilmem gerekir? 

#### <a name="tool-requirements"></a>Araç gereksinimleri

-  Bu aracın, performans sorunu olan VM 'de çalıştırılması gerekir. 

-  Aşağıdaki işletim sistemleri desteklenir: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 ve Windows Server 2016; Windows 8.1 ve Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Aracı üretim VM 'lerinde çalıştırdığınızda olası sorunlar

-  The benchişaretleme senaryosu veya XPerf veya DiskSpd kullanacak şekilde yapılandırılmış "Gelişmiş performans analizi" senaryosu için, araç sanal makinenin performansını olumsuz etkileyebilir. Bu senaryolar canlı bir üretim ortamında çalıştırılmamalıdır.

-  DiskSpd kullanmak üzere yapılandırılmış olan benchişaretleme senaryosu veya "Gelişmiş performans analizi" senaryosunda, başka bir arka plan etkinliğinin g/ç iş yüküne engel olmadığından emin olun.

-  Varsayılan olarak, araç veri toplamak için geçici depolama sürücüsünü kullanır. İzleme daha uzun bir süre etkin kalırsa, toplanan veri miktarı ilgili olabilir. Bu, geçici diskte yer alan kullanılabilirliği azaltabilir ve bu nedenle bu sürücüye bağımlı olan tüm uygulamaları etkileyebilir.

### <a name="how-do-i-run-perfinsights"></a>Nasıl yaparım?, Perfinsıghts çalışıyor mu? 

[Azure Performans Tanılama VM Uzantısı](performance-diagnostics-vm-extension.md)'nı yükleyerek bir sanal makinede Perfinsıghts 'i çalıştırabilirsiniz. Ayrıca, tek başına bir araç olarak da çalıştırabilirsiniz. 

**Azure portal ile Perfinsıghts 'i yükleyip çalıştırın**

Bu seçenek hakkında daha fazla bilgi için bkz. [Azure Performans Tanılama VM uzantısını yüklemeyi](performance-diagnostics-vm-extension.md#install-the-extension).  

**Tek başına modda Perfinsıghts çalıştırma**

Perfinsıghts aracını çalıştırmak için aşağıdaki adımları izleyin:


1. [Perfinsıghts. zip](https://aka.ms/perfinsightsdownload)' i indirin.

2. Perfinsıghts. zip dosyasının engelini kaldırın. Bunu yapmak için, Perfinsıghts. zip dosyasına sağ tıklayın ve **Özellikler**' i seçin. **Genel** sekmesinde **Engellemeyi kaldır**' ı seçin ve ardından **Tamam**' ı seçin. Bu, aracın ek güvenlik istemleri olmadan çalışmasını sağlar.  

    ![Kilit kaldırma vurgulanmış olarak Perfinsıghts özelliklerinin ekran görüntüsü](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  Sıkıştırılmış Perfinsıghts. zip dosyasını geçici sürücünüze genişletin (varsayılan olarak bu genellikle D sürücüsüdür). 

4.  Windows komut istemi ' ni yönetici olarak açın ve ardından kullanılabilir komut satırı parametrelerini görüntülemek için Perfinsıghts. exe ' yi çalıştırın.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Perfinsıghts CommandLine çıktısının ekran görüntüsü](media/how-to-use-perfInsights/pi-commandline.png)
    
    Perfinsıghts senaryolarını çalıştırmaya yönelik temel sözdizimi şöyledir:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Aşağıdaki örneği kullanarak, 5 dakika boyunca performans analizi senaryosunu çalıştırın:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Aşağıdaki örneği kullanarak, 5 dakika boyunca XPerf ve performans sayacı izlemeleri ile gelişmiş senaryoyu çalıştırabilirsiniz:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    5 dakika boyunca performans analizi senaryosunu çalıştırmak ve sonuç ZIP dosyasını depolama hesabına yüklemek için aşağıdaki örneği kullanabilirsiniz:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    **/List** komutunu kullanarak tüm kullanılabilir senaryoları ve seçenekleri arayabilirsiniz:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Bir senaryoyu çalıştırmadan önce, Perfinsıghts kullanıcıdan tanılama bilgilerini paylaşmayı ve EULA 'Yı kabul etmesini kabul etmesini ister. Bu istemleri atlamak için **/AcceptDisclaimerAndShareDiagnostics** seçeneğini kullanın. 
    >
    >Microsoft ile birlikte etkin bir destek bileti varsa ve üzerinde çalıştığınız destek mühendisinin isteği başına Perfınsıghts çalıştırıyorsanız, **/SR** seçeneğini kullanarak destek bileti numarasını sağladığınızdan emin olun.
    >
    >Varsayılan olarak, Perfinsıghts, varsa kendisini en son sürüme güncellemeyi dener. Otomatik güncelleştirmeyi atlamak için **/Skipautoupdate** veya **/sau** parametresini kullanın.  
    >
    >Süre anahtarı **/d** belirtilmezse, Perfinsıghts, vmslow, azurefiles ve gelişmiş senaryoları çalıştırırken sorunu yeniden oluşturmanız istenir. 

İzlemeler veya işlemler tamamlandığında, yeni bir dosya, Perfinsıghts ile aynı klasörde görüntülenir. Dosya adı **\_performancediagnostics yyyy-aa-gg\_hh-mm-ss-fff. zip şeklindedir.** Bu dosyayı analiz için destek aracısına gönderebilir veya bulguları ve önerileri gözden geçirmek için raporu ZIP dosyası içinde açabilirsiniz.

## <a name="review-the-diagnostics-report"></a>Tanılama raporunu gözden geçirin

**\_Performancediagnostics yyyy-aa-gg\_hh-mm-ss-fff. zip** dosyasında, perfinsıghts 'in bulgularını ayrıntılarıyla gösteren bir HTML raporu bulabilirsiniz. Raporu gözden geçirmek için **performancediagnostics\_yyyy-aa-\_gg hh-mm-ss-fff. zip** dosyasını genişletin ve ardından **perfinsıghts Report. html** dosyasını açın.

**Bulguları** sekmesini seçin.

![](media/how-to-use-perfInsights/pi-finding-tab.png)
Perfinsıghtsraporunun![ekran görüntüsü ekran görüntüsü](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> Yüksek olarak sınıflandırılan bulgular, performans sorunlarına neden olabilecek bilinen sorunlardır. Orta olarak sınıflandırılan bulgular, performans sorunlarına neden olmayan en iyi olmayan yapılandırmayı temsil eder. Düşük olarak sınıflandırılan bulgular yalnızca bilgilendirici deyimler.

Tüm yüksek ve orta düzeyde bulguları ve bağlantıları gözden geçirin. Performansı en iyi duruma getirilmiş yapılandırmalara yönelik en iyi uygulamalar hakkında bilgi edinin.

### <a name="storage-tab"></a>Depolama sekmesi

**Bulguları** bölümü, depolama ile ilgili çeşitli bulguları ve önerileri görüntüler.

**Disk Haritası** ve **birim eşleme** bölümleri mantıksal birimlerin ve fiziksel disklerin birbirleriyle nasıl ilişkili olduğunu açıklamaktadır.

Fiziksel disk perspektifinde (disk eşlemesi), tablo, diskte çalışan tüm mantıksal birimleri gösterir. Aşağıdaki örnekte, **PhysicalDrive2** birden çok bölümde oluşturulan iki mantıksal birimi çalıştırır (J ve H):

![Disk sekmesinin ekran görüntüsü](media/how-to-use-perfInsights/pi-disk-tab.png)

Birim perspektifinde (birim eşlemesi), tablolar her mantıksal birimin altındaki tüm fiziksel diskleri gösterir. RAID/dinamik diskler için, bir mantıksal birimi birden fazla fiziksel diskte çalıştırabileceğini unutmayın. Aşağıdaki örnekte, *C:\\Mount* fiziksel diskler 2 ve 3 ' te *spanneddisk* olarak yapılandırılmış bir bağlama noktasıdır:

![Birim sekmesinin ekran görüntüsü](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>SQL sekmesi

Hedef VM SQL Server örnekleri barındırıyorsa, **SQL**adlı raporda ek bir sekme görürsünüz:

![SQL sekmesinin ekran görüntüsü](media/how-to-use-perfInsights/pi-sql-tab.png)

Bu bölüm, VM 'de barındırılan SQL Server örneklerinin her biri için bir **bulguları** sekmesi ve ek sekmeler içerir.

**Bulmalar** sekmesi, öneriler ile birlikte bulunan tüm SQL ile ilgili performans sorunlarının bir listesini içerir.

Aşağıdaki örnekte, **PhysicalDrive0** (C sürücüsü çalıştırılarak) görüntülenir. Bunun nedeni, hem **modeldev** hem de **modellog** dosyalarının C sürücüsünde konumlandırıldı ve farklı türlerdir (sırasıyla veri dosyası ve işlem günlüğü gibi).

![Günlük bilgilerinin ekran görüntüsü](media/how-to-use-perfInsights/pi-log-info.png)

Belirli SQL Server örneklerine yönelik sekmeler, seçilen örnekle ilgili temel bilgileri görüntüleyen bir genel bölüm içerir. Sekmeler Ayrıca ayarlar, konfigürasyonlar ve Kullanıcı seçenekleri de dahil olmak üzere gelişmiş bilgiler için ek bölümler içerir.

### <a name="diagnostic-tab"></a>Tanılama sekmesi
**Tanılama** sekmesi, Perfinsıghts 'ın çalışma süresi boyunca bilgisayardaki en iyi CPU, disk ve bellek tüketicileri hakkında bilgi içerir. Ayrıca, sistemin eksik olabileceği kritik düzeltme ekleri, görev listesi ve önemli sistem olayları hakkında bilgi bulabilirsiniz. 

## <a name="references-to-the-external-tools-used"></a>Kullanılan dış araçlara başvurular

### <a name="diskspd"></a>DiskSpd

DiskSpd, Microsoft 'tan bir depolama yük Oluşturucu ve performans testi aracıdır. Daha fazla bilgi için bkz. [DiskSpd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>XPerf

XPerf, Windows performans araç seti 'ndeki izlemeleri yakalamaya yönelik bir komut satırı aracıdır. Daha fazla bilgi için bkz. [Windows performans araç seti – XPerf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla gözden geçirmek için Microsoft Desteği tanılama günlüklerini ve raporları karşıya yükleyebilirsiniz. Destek, sorun giderme sürecine yardımcı olmak için Perfinsıghts tarafından oluşturulan çıktıyı iletmenize istekte bulunabilir.

Aşağıdaki ekran görüntüsünde, neler alabileceğinize benzer bir ileti gösterilmektedir:

![Microsoft Desteği örnek ileti ekran görüntüsü](media/how-to-use-perfInsights/pi-support-email.png)

Dosya aktarımı çalışma alanına erişmek için iletideki yönergeleri izleyin. Ek güvenlik için, ilk kullanımda parolanızı değiştirmeniz gerekir.

Oturum açtıktan sonra, perfinsıghts tarafından toplanan **\_performancediagnostics yyyy-mm-dd\_hh-mm-ss-fff. zip** dosyasını karşıya yüklemek için bir iletişim kutusu görürsünüz.

