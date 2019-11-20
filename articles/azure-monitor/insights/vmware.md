---
title: Azure Izleyici 'de çözüm VMware İzleme | Microsoft Docs
description: VMware İzleme çözümünün günlükleri yönetmeye nasıl yardımcı olabileceğini ve ESXi konaklarının izlenmesini nasıl sağlayabileceğini öğrenin.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 05/04/2018
ms.openlocfilehash: dc453ad42312bb096aed1356d376b0906870a7b0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900602"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Azure Izleyici 'de VMware İzleme (kullanım dışı) çözümü

![VMware simgesi](./media/vmware/vmware-symbol.png)

> [!NOTE]
> VMware İzleme çözümü kullanım dışı bırakılmıştır.  Çözümü zaten yüklemiş olan müşteriler bu uygulamayı kullanmaya devam edebilir, ancak VMware İzleme hiçbir yeni çalışma alanına eklenemez.

Azure Izleyici 'deki VMware İzleme çözümü, büyük VMware günlükleri için merkezi bir günlüğe kaydetme ve izleme yaklaşımı oluşturmanıza yardımcı olan bir çözümdür. Bu makalede, çözümü kullanarak ESXi konaklarının tek bir konumda nasıl giderebileceğiniz, yakalanacağı ve yönetileceği açıklanmaktadır. Çözümle birlikte, tüm ESXi konaklarınız için ayrıntılı verileri tek bir konumda görebilirsiniz. ESXi ana bilgisayar günlükleri aracılığıyla sunulan VM ve ESXi konaklarının en iyi olay sayılarını, durumunu ve eğilimlerini görebilirsiniz. Merkezi ESXi konak günlüklerini görüntüleyip arayarak sorun giderebilirsiniz. Günlük arama sorgularını temel alan uyarılar da oluşturabilirsiniz.

Çözüm, Log Analytics aracısına sahip bir hedef VM 'ye veri göndermek için ESXi konağının yerel Syslog işlevini kullanır. Ancak, çözüm hedef VM 'de Syslog 'a dosya yazmaz. Log Analytics Aracısı 1514 numaralı bağlantı noktasını açar ve bunu dinler. Verileri aldıktan sonra, Log Analytics Aracısı verileri Azure Izleyici 'ye iter.

## <a name="install-and-configure-the-solution"></a>Çözümü yükleyip yapılandırma
Çözümü yüklemek ve yapılandırmak için aşağıdaki bilgileri kullanın.

* [İzleme çözümü yüklemesi](../insights/solutions.md#install-a-monitoring-solution)bölümünde açıklanan işlemi kullanarak aboneliğinize VMware izleme çözümünü ekleyin.

#### <a name="supported-vmware-esxi-hosts"></a>Desteklenen VMware ESXi Konakları
vSphere ESXi ana bilgisayarı 5,5, 6,0 ve 6,5

#### <a name="prepare-a-linux-server"></a>Linux sunucusu hazırlama
ESXi konaklarından tüm Syslog verilerini almak için bir Linux işletim sistemi VM 'si oluşturun. [Log Analytics Linux Aracısı](../learn/quick-collect-linux-computer.md) , tüm ESXi ana bilgisayar Syslog verileri için koleksiyon noktasıdır. Aşağıdaki örnekte olduğu gibi, günlükleri tek bir Linux sunucusuna iletmek için birden çok ESXi ana bilgisayarı kullanabilirsiniz.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![Syslog akışı](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Syslog koleksiyonunu yapılandırma
1. VSphere için Syslog iletmeyi ayarlayın. Syslog iletmeyi ayarlamaya yardımcı olacak ayrıntılı bilgiler için, bkz. [ESXi 5,0 ve üzeri için Syslog yapılandırma (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). **ESXi konak yapılandırması** > **yazılım** > **Gelişmiş ayarlar** > **Syslog**' a gidin.
   ![vspburconfig](./media/vmware/vsphere1.png)  
1. *Syslog. Global. logHost* alanında, Linux sunucunuzu ve *1514*numaralı bağlantı noktasını ekleyin. Örneğin, `tcp://hostname:1514` veya `tcp://123.456.789.101:1514`
1. Syslog için ESXi ana bilgisayar güvenlik duvarını açın. **ESXi ana bilgisayar yapılandırması** > **yazılım** > **güvenlik profili** > güvenlik **duvarı** ve açık **Özellikler**.  

    ![vsizefw](./media/vmware/vsphere2.png)  

    ![vsbu efwproperties](./media/vmware/vsphere3.png)  
1. Syslog 'ın düzgün şekilde ayarlandığını doğrulamak için vSphere konsolunu denetleyin. **1514** numaralı bağlantı noktasının yapılandırıldığını ESXi ana bilgisayarında doğrulayın.
1. Linux sunucusuna Linux için Log Analytics Aracısı indirin ve yükleyin. Daha fazla bilgi için bkz. [Linux için Log Analytics aracısına yönelik belgeler](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Linux için Log Analytics aracısı yüklendikten sonra,/etc/seçenek/Microsoft/omsagent/sysconf/omsagent.exe dizinine gidin ve vmware_esxi. conf dosyasını/etc/seçenek/Microsoft/omsagent/conf/omsagent.exe dizinine kopyalayın ve sahip/grubunu değiştirin ve dosya izinleri. Örnek:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. `sudo /opt/microsoft/omsagent/bin/service_control restart`çalıştırarak Linux için Log Analytics aracısını yeniden başlatın.
1. ESXi konağındaki `nc` komutunu kullanarak Linux sunucusu ile ESXi Konağı arasındaki bağlantıyı test edin. Örnek:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. Azure portal, `VMware_CL`için bir günlük sorgusu gerçekleştirin. Azure Izleyici Syslog verilerini toplarken Syslog biçimini korur. Portalda, *ana bilgisayar* adı ve *ProcessName*gibi bazı belirli alanlar yakalanır.  

    ![type](./media/vmware/type.png)  

    Görünüm günlüğü arama sonuçlarınız yukarıdaki görüntüyle benzerdir VMware İzleme çözüm panosunu kullanmak üzere ayarlanır.  

## <a name="vmware-data-collection-details"></a>VMware veri toplama ayrıntıları
VMware İzleme çözümü, etkinleştirdiğiniz Linux için Log Analytics aracılarını kullanarak ESXi konaklarından çeşitli performans ölçümlerini ve günlük verilerini toplar.

Aşağıdaki tabloda veri toplama yöntemleri ve verilerin toplanmasına ilişkin diğer ayrıntılar gösterilmektedir.

| platformunun | Linux için Log Analytics Aracısı | SCOM Aracısı | Azure Depolama | SCOM gerekli mi? | Yönetim grubu aracılığıyla gönderilen SCOM Aracısı verileri | toplama sıklığı |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |3 dakikada bir |

Aşağıdaki tabloda VMware İzleme çözümü tarafından toplanan veri alanı örnekleri gösterilmektedir:

| alan adı | açıklama |
| --- | --- |
| Device_s |VMware depolama cihazları |
| ESXIFailure_s |hata türleri |
| EventTime_t |olayın gerçekleştiği zaman |
| HostName_s |ESXi ana bilgisayar adı |
| Operation_s |VM oluşturma veya VM silme |
| ProcessName_s |Olay adı |
| ResourceId_s |VMware konağının adı |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |VMware SCSI durumu |
| SyslogMessage_s |Syslog verileri |
| UserName_s |VM 'yi oluşturan veya silen Kullanıcı |
| VMName_s |VM adı |
| Bilgisayar |Ana bilgisayar |
| TimeGenerated |verilerin oluşturulduğu saat |
| DataCenter_s |VMware veri merkezi |
| StorageLatency_s |depolama gecikmesi (MS) |

## <a name="vmware-monitoring-solution-overview"></a>VMware İzleme çözüme genel bakış
VMware kutucuğu Log Analytics çalışma alanınızda görünür. Hatalara ilişkin üst düzey bir görünüm sağlar. Kutucuğa tıkladığınızda bir Pano görünümüne gidebilirsiniz.

![tile (döşeme)](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Pano görünümünde gezinme
**VMware** Pano görünümünde, Blade 'leri şu şekilde düzenlenmiştir:

* Hata durumu sayısı
* Olay sayılarına göre popüler ana bilgisayar
* En yüksek olay sayıları
* Sanal makine etkinlikleri
* ESXi konak disk olayları

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Dikey pencereye özgü ayrıntılı bilgileri gösteren Log Analytics arama bölmesini açmak için herhangi bir dikey pencere ' ya tıklayın.

Buradan, belirli bir şeyi değiştirmek için günlük sorgusunu düzenleyebilirsiniz. Günlük sorguları oluşturma hakkında ayrıntılı bilgi için bkz. [Azure izleyici 'de günlük sorgularını kullanarak veri bulma](../log-query/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>ESXi ana bilgisayar olaylarını bul
Tek bir ESXi ana bilgisayarı, işlemlerine bağlı olarak birden çok günlük oluşturur. VMware İzleme çözümü onları merkezileştirir ve olay sayılarını özetler. Bu merkezi görünüm, hangi ESXi konağının yüksek hacimde olaylara sahip olduğunu ve ortamınızda en sık hangi olayların meydana geldiklerinizi anlamanıza yardımcı olur.

![olay](./media/vmware/events.png)

Bir ESXi konağına veya bir olay türüne tıklayarak daha fazla ayrıntıya gidebilirsiniz.

Bir ESXi ana bilgisayar adına tıkladığınızda, bu ESXi ana bilgisayarındaki bilgileri görüntüleyebilirsiniz. Olay türü ile sonuçları daraltmak istiyorsanız, arama sorgunuza `“ProcessName_s=EVENT TYPE”` ekleyin. Arama filtresinde **ProcessName** seçeneğini belirleyebilirsiniz. Bu, bilgileri sizin için daraltır.

![incelemek](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Yüksek VM etkinliklerini bulma
Bir sanal makine, herhangi bir ESXi konağında oluşturulup silinebilir. Yöneticinin bir ESXi konağının kaç VM oluşturduğunu belirlemesi yararlı olur. Bu durumda, performans ve kapasite planlamasının anlaşılmasına yardımcı olur. Ortamınızı yönetirken VM etkinlik olaylarının izlenmesi çok önemlidir.

![incelemek](./media/vmware/vmactivities1.png)

Ek ESXi konak VM oluşturma verilerini görmek isterseniz, bir ESXi ana bilgisayar adına tıklayın.

![incelemek](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Ortak günlük sorguları
Çözüm, yüksek depolama alanı, depolama gecikme süresi ve yol hatası gibi ESXi konaklarınızı yönetmenize yardımcı olabilecek diğer yararlı sorguları içerir.

![sorgu](./media/vmware/queries.png)


#### <a name="save-queries"></a>Sorguları Kaydet
Günlük sorgularını kaydetmek, Azure Izleyici 'de standart bir özelliktir ve faydalı bulduğunuz sorguları korumanıza yardımcı olabilir. Faydalı bulduğunuz bir sorgu oluşturduktan sonra **Sık Kullanılanlar**' a tıklayarak bunu kaydedin. Kayıtlı bir sorgu daha sonra kendi özel panolarınızı oluşturabileceğiniz [Pano](../learn/tutorial-logs-dashboards.md) sayfasından daha sonra kolayca yeniden kullanmanıza olanak tanır.

![DockerDashboardView](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Sorgulardan uyarı oluşturma
Sorgularınızı oluşturduktan sonra, belirli olaylar oluştuğunda sizi uyarmak için sorguları kullanmak isteyebilirsiniz. Uyarı oluşturma hakkında bilgi için bkz. [Log Analytics Uyarıları](../platform/alerts-overview.md) . Uyarı sorgularının ve diğer sorgu örneklerinin örnekleri için bkz. Log Analytics Web günlüğü gönderisini [kullanarak VMware 'Yi izleme](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) .

## <a name="frequently-asked-questions"></a>Sık sorulan sorular
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>ESXi ana bilgisayar ayarında ne yapmam gerekir? Geçerli ortammda hangi etkileri olacaktır?
Çözüm yerel ESXi ana bilgisayar Syslog iletme mekanizmasını kullanır. Günlükleri yakalamak için ESXi konağında ek Microsoft yazılımlarına gerek yoktur. Mevcut ortamınız üzerinde düşük bir etkiye sahip olmalıdır. Ancak, ESXI işlevselliği olan Syslog iletmeyi ayarlamanız gerekir.

### <a name="do-i-need-to-restart-my-esxi-host"></a>ESXi konumu yeniden başlatmem gerekir mi?
Hayır. Bu işlem için yeniden başlatma gerekmez. Bazen, vSphere Syslog öğesini düzgün bir şekilde güncelleştirmez. Böyle bir durumda ESXi konağında oturum açın ve Syslog 'yi yeniden yükleyin. Yine de, bu işlemin ortamınızda kesintiye uğramaması için konağı yeniden başlatmanız gerekmez.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Log Analytics gönderilen günlük verilerinin hacmini artırabilir veya azaltabilir miyim?
Evet. VSphere içindeki ESXi ana bilgisayar günlüğü düzeyi ayarlarını kullanabilirsiniz. Günlük toplama, *bilgi* düzeyine dayalıdır. Bu nedenle, VM oluşturmayı veya silmeyi denetlemek isterseniz, ana bilgisayar üzerinde *bilgi* düzeyini tutmanız gerekir. Daha fazla bilgi için bkz. [VMware bilgi tabanı](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Hostd neden Log Analytics veri sağlamıyor? Günlük ayarlarım Info olarak ayarlanır.
Syslog zaman damgası için bir ESXi ana bilgisayar hatası vardı. Daha fazla bilgi için bkz. [VMware bilgi tabanı](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Geçici çözümü uyguladıktan sonra, Hostd normal şekilde çalışır.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Syslog verilerini omsagent ile tek bir VM 'ye ileten birden çok ESXi ana bilgisayarı olabilir miyim?
Evet. Birden çok ESXi ana bilgisayarı, omsagent ile tek bir VM 'ye iletmenize sahip olabilirsiniz.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Log Analytics veri akışını neden göremiyorum?
Birden çok neden olabilir:

* ESXi ana bilgisayarı, omsagent çalıştıran VM 'ye doğru bir şekilde veri itimez. Test etmek için aşağıdaki adımları gerçekleştirin:

  1. Doğrulamak için, SSH kullanarak ESXi konağında oturum açın ve şu komutu çalıştırın: `nc -z ipaddressofVM 1514`

      Bu başarılı olmazsa, gelişmiş yapılandırmadaki vSphere ayarları muhtemelen doğru değildir. Syslog iletimi için ESXi konağını ayarlama hakkında bilgi için bkz. [Syslog toplamasını yapılandırma](#configure-syslog-collection) .
  1. Syslog bağlantı noktası bağlantısı başarılı olursa, ancak yine de herhangi bir veri görmezseniz, şu komutu çalıştırmak için SSH kullanarak ESXi ana bilgisayarında Syslog öğesini yeniden yükleyin: `esxcli system syslog reload`
* Log Analytics aracısına sahip VM doğru ayarlanmadı. Bunu test etmek için aşağıdaki adımları gerçekleştirin:

  1. Log Analytics 1514 numaralı bağlantı noktasını dinler. Açık olduğunu doğrulamak için şu komutu çalıştırın: `netstat -a | grep 1514`
  1. `1514/tcp` açık bağlantı noktasını görmeniz gerekir. Bunu yapmazsanız, omsagent 'ın doğru bir şekilde yüklendiğinden emin olun. Bağlantı noktası bilgilerini görmüyorsanız, VM 'de Syslog bağlantı noktası açık değildir.

    a. Log Analytics aracısının `ps -ef | grep oms`kullanarak çalıştığını doğrulayın. Çalışmıyorsa, komutunu çalıştırarak işlemi başlatın `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` dosyasını açın.

     c. Uygun Kullanıcı ve grup ayarının geçerli olduğunu ve şuna benzer olduğunu doğrulayın: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Dosya yoksa veya Kullanıcı ve Grup ayarı yanlışsa, [bir Linux sunucusunu hazırlarken](#prepare-a-linux-server)düzeltici işlem gerçekleştirin.

## <a name="next-steps"></a>Sonraki adımlar
* Ayrıntılı VMware konak verilerini görüntülemek için Log Analytics 'de [günlük sorgularını](../log-query/log-query-overview.md) kullanın.
* VMware konak verilerini gösteren [kendi panolarınızı oluşturun](../learn/tutorial-logs-dashboards.md) .
* Belirli VMware ana bilgisayar olayları gerçekleştiğinde [uyarı oluştur](../platform/alerts-overview.md) .
