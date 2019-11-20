---
title: Microsoft Azure 'da Perfinsıghts Linux kullanımı | Microsoft Docs
description: Linux VM performans sorunlarını gidermek için Perfınsıghts 'in nasıl kullanılacağını öğrenir.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: 19b2fcaed2c80d4ca52ada9f9f0898479e73bcf2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080525"
---
# <a name="how-to-use-perfinsights"></a>PerfInsights’ı kullanma

[Perfinsıghts Linux](https://aka.ms/perfinsightslinuxdownload) , tanılama verilerini toplayıp analiz eden bir kendi kendine bir tanılama aracıdır ve Azure 'da Linux sanal makine performans sorunlarını gidermenize yardımcı olacak bir rapor sağlar. Perfinsıghts, desteklenen sanal makinelerde tek başına bir araç olarak veya [Azure sanal makineleri Için performans tanılamayı](performance-diagnostics.md)kullanarak doğrudan portaldan çalıştırılabilir.

Sanal makinelerle ilgili performans sorunları yaşıyorsanız, desteğe başvurmadan önce bu aracı çalıştırın.

## <a name="supported-troubleshooting-scenarios"></a>Desteklenen sorun giderme senaryoları

Perfinsıghts çeşitli bilgi türlerini toplayıp analiz edebilir. Aşağıdaki bölümlerde yaygın senaryolar ele alınmaktadır.

### <a name="quick-performance-analysis"></a>Hızlı performans analizi

Bu senaryo, sanal makinenizin depolama ve donanım yapılandırması, aşağıdakiler dahil çeşitli Günlükler gibi temel bilgileri toplar:

- İşletim sistemi bilgileri

- PCI cihaz bilgileri

- Genel Konuk işletim sistemi günlükleri

- Yapılandırma dosyaları

- Depolama bilgileri

- Azure sanal makine yapılandırması ( [azure Instance Metadata Service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)kullanılarak toplanır)

- Çalışan işlemlerin, diskin, belleğin ve CPU kullanımının listesi

- Ağ bilgileri

Bu, sistemi etkilememelidir olmayan bir bilgi koleksiyonudur.

>[!Note]
>Hızlı performans Analizi senaryosu aşağıdaki senaryolardan her birine otomatik olarak eklenir:

### <a name="performance-analysis"></a>Performans Analizi

Bu senaryo, hızlı performans çözümlemesine benzer, ancak tanılama bilgilerinin daha uzun süre yakalanmasını sağlar.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Perfinsıghts tarafından ne tür bilgiler toplanır

Linux sanal makinesi, işletim sistemi, blok cihazları, yüksek kaynak tüketicileri, yapılandırma ve çeşitli Günlükler hakkında bilgiler toplanır. Daha fazla ayrıntı aşağıda verilmiştir:

- İşletim sistemi
  - Linux dağıtımı ve sürümü
  - Çekirdek bilgileri
  - Sürücü bilgileri

- Donanım
  - PCI aygıtları [`*`]

- Süreçler ve bellek
  - İşlem listesi (görev adı, kullanılan bellek, açılan dosyalar)
  - Toplam, kullanılabilir ve boş fiziksel bellek
  - Toplam, kullanılabilir ve serbest takas belleği
  - CPU 'nun oluşturulması ve 5 saniyelik bir aralıktaki CPU kullanımını işleme
  - İşlem belleği kullanımı için 5 saniyelik aralıkta profil oluşturma

- Ağ  
  - Bağdaştırıcılar istatistikleriyle ağ bağdaştırıcılarının listesi
  - Ağ yönlendirme tablosu
  - Açık bağlantı noktaları ve durum

- Depolama
  - Cihaz listesini engelle
  - Bölüm listesi
  - Bağlama noktaları listesi
  - MDADDM birim bilgileri
  - LVM birimi bilgileri
  - 5 saniyelik aralıkta tüm disklerde yakalama profili oluşturuluyor

- Günlükler
  - /var/log/Messages
  - /var/log/Syslog
  - /var/log/Kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/Cloud-Init.log
  - /var/log/Cloud-init-output.log
  - /var/logtecu-Manager.log
  - /var/log/waagent.log
  - /var/log/Azure/[uzantı klasörü]/\*günlük\*
  - /var/seçenek/Microsoft/omsconfig/omsconfig.log
  - /var/seçenek/Microsoft/omsagent/log/omsagent.log
  - /etc/waagentnconfig
  - Son beş güne ait journalctl çıkışı

- [Azure sanal makine örneği meta verileri](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] PCI bilgileri henüz DEMIN ve SLES dağıtımlarında toplanmadı

## <a name="run-the-perfinsights-linux-on-your-vm"></a>VM 'niz üzerinde Perfinsıghts Linux 'u çalıştırın

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Aracı çalıştırmadan önce neleri bilmem gerekir

#### <a name="tool-requirements"></a>Araç gereksinimleri

- Bu aracın, performans sorunu olan VM 'de çalıştırılması gerekir.
- Python 2,7, VM 'de yüklü olmalıdır

- Aşağıdaki dağıtımlar Şu anda destekleniyor:

    | Dağıtım               | Version                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux sunucusu        | 6,10 [`*`], 7,3, 7,6, 7,5 (Oracle-Database-Ee 13,8 Market Image)|
    | CentOS                     | 6,5 [`*`], 7,6                                    |
    | RHEL                       | 7,2, 7,5, 8,0 [`*`]                               |
    | Ubuntu                     | 14.04, 16.04 18.04                               |
    | Debian                     | 8, 9, 10 [`*`]                                    |
    | SLES                       | 12 SP4 [`*`]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] Lütfen [bilinen sorunlar](#known-issues) bölümüne bakın

### <a name="known-issues"></a>Bilinen sorunlar

- RHEL 8 için Python varsayılan olarak yüklü değildir. Perfinsıghts Linux 'u çalıştırmak için, önce Python 2,7 ' i yüklemeniz gerekir

- Konuk Aracısı bilgileri toplama, CentOS 6. x üzerinde başarısız olabilir

- PCI cihaz bilgileri, DEMIN tabanlı dağıtımlarda toplanmaz

- LVM bilgileri bazı dağıtımlarla kısmen toplanır

### <a name="how-do-i-run-perfinsights"></a>Nasıl yaparım? çalışan perfinsı

Azure portal 'den Azure Performans Tanılama 'Yı yükleyerek bir sanal makinede Perfinsıghts 'i çalıştırabilirsiniz. Ayrıca, tek başına bir araç olarak da çalıştırabilirsiniz.

>[!Note]
>Perfinsıghts yalnızca verileri toplayıp analiz eder. Sistemde herhangi bir değişiklik yapmaz.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Azure portal ile Perfinsıghts 'i yükleyip çalıştırın

Bu seçenek hakkında daha fazla bilgi için bkz. [Azure Performans Tanılama](performance-diagnostics.md).  

#### <a name="run-perfinsights-in-standalone-mode"></a>Tek başına modda Perfinsıghts çalıştırma

Perfinsıghts aracını çalıştırmak için aşağıdaki adımları izleyin:

1. [Perfinsıghts. tar. gz](https://aka.ms/perfinsightslinuxdownload) dosyasını sanal makinenizde bir klasöre indirin ve terminalden aşağıdaki komutları kullanarak içeriği ayıklayın.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Dosyasını içeren `perfinsights.py` klasöre gidin ve ardından kullanılabilir komut satırı parametrelerini görüntülemek `perfinsights.py` için çalıştırın.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Perfinsıghts Linux CommandLine çıkışının ekran görüntüsü](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    Perfinsıghts senaryolarını çalıştırmaya yönelik temel sözdizimi şöyledir:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    Hızlı performans analizi senaryosunu 1 dakika boyunca çalıştırmak ve/t MP/output klasörü altında sonuçları oluşturmak için aşağıdaki örneği kullanabilirsiniz:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    5 dakika boyunca performans analizi senaryosunu çalıştırmak ve sonuç tar Ball 'ı depolama hesabına yüklemek için aşağıdaki örneği kullanabilirsiniz:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Bir senaryoyu çalıştırmadan önce, Perfinsıghts kullanıcıdan tanılama bilgilerini paylaşmayı ve EULA 'Yı kabul etmesini kabul etmesini ister. Bu istemleri atlamak için **-a veya--Accept-Disclaimer-ve-Share-Diagnostic** seçeneğini kullanın.
    >
    >Microsoft ile etkin bir destek biletine sahipseniz ve üzerinde çalıştığınız destek mühendisinin isteği başına Perfinsıghts çalıştırıyorsanız, **-s veya--support-Request** seçeneğini kullanarak destek bileti numarasını sağladığınızdan emin olun.

Çalıştırma tamamlandığında, hiçbir çıkış klasörü belirtilmediği sürece yeni bir tar dosyası Perfinsıghts ile aynı klasörde görünür. Dosyanın adı **\_performancediagnostics yyyy-\_aa-gg hh-mm-ss-fff. tar. gz şeklindedir.** Bu dosyayı analiz için destek aracısına gönderebilir veya bulguları ve önerileri gözden geçirmek için raporu dosya içinde açabilirsiniz.

## <a name="review-the-diagnostics-report"></a>Tanılama raporunu gözden geçirin

**\_Performancediagnostics yyyy-\_aa-gg hh-mm-ss-fff. tar. gz** dosyasında, perfinsıghts 'in bulgularını ayrıntılarıyla gösteren bir HTML raporu bulabilirsiniz. Raporu gözden geçirmek için **performancediagnostics\_yyyy-aa-gg\_hh-mm-ss-fff. tar. gz** dosyasını genişletin ve ardından **perfinsıghts Report. html** dosyasını açın.

### <a name="overview-tab"></a>Genel Bakış sekmesi

**Genel bakış** sekmesi temel çalıştırma ayrıntılarını ve sanal makine bilgilerini sağlar. **Bulmalar** sekmesi, Perfinsıghts raporunun tüm farklı bölümlerinden önerilerin özetini görüntüler.

![Perfinsıghts raporunun ekran görüntüsü](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Perfinsıghts raporunun ekran görüntüsü](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> Yüksek olarak sınıflandırılan bulgular, performans sorunlarına neden olabilecek bilinen sorunlardır. Orta olarak sınıflandırılan bulgular, performans sorunlarına neden olmayan en iyi olmayan yapılandırmayı temsil eder. Düşük olarak sınıflandırılan bulgular yalnızca bilgilendirici deyimler.

Tüm yüksek ve orta düzeyde bulguları ve bağlantıları gözden geçirin. Performansı en iyi duruma getirilmiş yapılandırmalara yönelik en iyi uygulamalar hakkında bilgi edinin.

### <a name="cpu-tab"></a>CPU sekmesi

**CPU** sekmesi, SISTEM genelinde CPU tüketimi hakkında, Perfinsıghts çalıştırması sırasında bilgi sağlar. Yüksek CPU kullanım süreleri ve en uzun çalışan CPU tüketicileri hakkında bilgi, CPU ile ilgili yüksek sorunların giderilmesi için yararlı olacaktır.

![Perfinsıghts rapor CPU sekmesinin ekran görüntüsü](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Depolama sekmesi

**Bulguları** bölümü, depolama ile ilgili çeşitli bulguları ve önerileri görüntüler.

**Blok cihazlar** ve **bölümler**, **LVM**ve **mdaddm** sekmeleri gibi ilgili diğer bölümler, blok cihazların nasıl yapılandırıldığını ve birbirleriyle ilgili olduğunu açıklamaktadır.

![Depolama sekmesinin ekran görüntüsü](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![MDADDM sekmesinin ekran görüntüsü](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Linux sekmesi

**Linux** SEKMESI, sanal makinenizde çalışan donanım ve işletim sistemi hakkında bilgiler içerir. Ayrıntılar, çalışan işlemlerin ve konuk Aracı, PCI, CPU, sürücüler ve LIS sürücüleriyle ilgili bilgilerin bir listesini içerir.

![Linux sekmesinin ekran görüntüsü](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla gözden geçirmek için Microsoft Desteği tanılama günlüklerini ve raporları karşıya yükleyebilirsiniz. Microsoft Desteği personeli ile çalışırken, sorun giderme sürecine yardımcı olmak için Perfinsıghts tarafından oluşturulan çıktıyı iletmenize istekte bulunabilir.

Aşağıdaki ekran görüntüsünde, neler alabileceğinize benzer bir ileti gösterilmektedir:

![Microsoft Desteği örnek ileti ekran görüntüsü](media/how-to-use-perfinsights-linux/support-email.png)

Dosya aktarımı çalışma alanına erişmek için iletideki yönergeleri izleyin. Ek güvenlik için, ilk kullanımda parolanızı değiştirmeniz gerekir.

Oturum açtıktan sonra, perfinsıghts tarafından toplanan **\_performancediagnostics yyyy-\_aa-gg hh-mm-ss-fff. tar. gz** dosyasını karşıya yüklemek için bir iletişim kutusu görürsünüz.
