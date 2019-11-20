---
title: Azure Log Analytics Linux Aracısı sorunlarını giderme | Microsoft Docs
description: Azure Izleyici 'de Linux için Log Analytics aracısında, en sık karşılaşılan sorunların belirtilerini, nedenlerini ve çözümlemesini tanıtın.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/13/2018
ms.openlocfilehash: 5b828f62d5a8c7c518f3d28e92f52aac3b77f65c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932809"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Linux için Log Analytics aracısında sorun giderme 

Bu makalede, Azure Izleyici 'de Linux için Log Analytics aracısında karşılaşabileceğiniz sorunları gidermeye yönelik yardım ve bunları çözmek için olası çözümler sunulmaktadır.

Bu adımlardan hiçbiri sizin için çalışmazsa, aşağıdaki destek kanalları da kullanılabilir:

* Premier destek avantajlarına sahip müşteriler, [Premier](https://premier.microsoft.com/)ile bir destek isteği açabilir.
* Azure destek sözleşmeleri olan müşteriler [Azure Portal](https://manage.windowsazure.com/?getsupport=true)bir destek talebi açabilir.
* OMI sorunlarını, [OMI sorun giderme kılavuzuyla](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md)tanılayın.
* Bir [GitHub sorunu](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)dosyası.
* Gönderilen fikirleri ve hataları [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) veya yeni bir dosya dosyasını gözden geçirmek Için Log Analytics geri bildirim sayfasını ziyaret edin.  

## <a name="important-log-locations-and-log-collector-tool"></a>Önemli günlük konumları ve günlük Toplayıcı aracı

 Dosya | Yol
 ---- | -----
 Linux günlük dosyası için Log Analytics Aracısı | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Log Analytics Aracısı yapılandırma günlük dosyası | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Sorun gidermeye veya bir GitHub sorunu göndermeden önce önemli günlükleri almak için günlük Toplayıcı aracımızı kullanmanızı öneririz. Araç hakkında daha fazla bilgi edinebilirsiniz ve [buradan](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)çalıştırabilirsiniz.

## <a name="important-configuration-files"></a>Önemli yapılandırma dosyaları

 Kategori | Dosya konumu
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf` veya `/etc/rsyslog.conf` veya `/etc/rsyslog.d/95-omsagent.conf`
 Performans, Nagios, Zabbix, Log Analytics çıktı ve genel aracı | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Ek yapılandırmalar | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Koleksiyon, veri menüsünden yapılandırılmışsa, çalışma alanınızın Azure portal [Gelişmiş ayarları Log Analytics](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) , performans sayaçları ve Syslog için yapılandırma dosyalarını düzenlemenin üzerine yazılır. Tüm aracıların yapılandırmasını devre dışı bırakmak için Log Analytics **Gelişmiş ayarlarından** koleksiyonu devre dışı bırakın ya da tek bir aracı için aşağıdakileri çalıştırın:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Yükleme hata kodları

| Hata kodu | Anlamı |
| --- | --- |
| NOT_DEFINED | Gerekli bağımlılıklar yüklü olmadığından auoms auditd eklentisi yüklenmeyecek | Auoms yüklemesi başarısız oldu, paket auditd 'yi yükleme. |
| 2 | Kabuk paketi için geçersiz seçenek belirtildi. Kullanım için `sudo sh ./omsagent-*.universal*.sh --help` Çalıştır |
| 3 | Kabuk paketine hiçbir seçenek sağlanmaz. Kullanım için `sudo sh ./omsagent-*.universal*.sh --help` çalıştırın. |
| 4 | Geçersiz paket türü veya geçersiz proxy ayarları; omsagent-*RPM*. sh PAKETLERI yalnızca RPM tabanlı sistemlere yüklenebilir ve omsagent-*Deb*. sh paketleri yalnızca, detem tabanlı sistemlere yüklenebilir. [En son sürümden](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux)evrensel yükleyiciyi kullanmanız önerilir. Ayrıca, proxy ayarlarınızı doğrulamak için gözden geçirin. |
| 5 | Kabuk paketi kök olarak yürütülmelidir veya ekleme sırasında 403 hatası döndürüldü. `sudo`kullanarak komutunuz çalıştırın. |
| 6 | Geçersiz Paket mimarisi veya ekleme sırasında 200 hatası döndürüldü. omsagent-*x64.sh paketleri yalnızca 64 bitlik sistemlere yüklenebilir ve omsagent-* x86.sh paketleri yalnızca 32 bit sistemlere yüklenebilir. [En son sürümden](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest)mimariniz için doğru paketi indirin. |
| 17 | OMS paketi yüklemesi başarısız oldu. Kök hatası için komut çıktısına bakın. |
| 19 | OMı paketi yüklemesi başarısız oldu. Kök hatası için komut çıktısına bakın. |
| 20 | SCX paketinin yüklemesi başarısız oldu. Kök hatası için komut çıktısına bakın. |
| 21 | Sağlayıcı setlerinin yüklemesi başarısız oldu. Kök hatası için komut çıktısına bakın. |
| 22 | Paketlenmiş paket yüklenemedi. Kök hatası için komut çıktısına bakın |
| 23 | SCX veya OMı paketi zaten yüklü. Kabuk paketini yüklemek için `--install` yerine `--upgrade` kullanın. |
| 30 | İç paket hatası. Çıkışındaki ayrıntılarla ilgili bir [GitHub sorunu verin](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 55 | Desteklenmeyen OpenSSL sürümü veya Azure Izleyici 'ye bağlanılamıyor ya da dpkg, kilitli veya eksik bir kıvrımlı programdır. |
| 61 | Python ctypes kitaplığı eksik. Python ctypes kitaplığını veya paketini (Python-ctypes) yükler. |
| 62 | Bataklık programı eksik, bataklık 'yı yüklemelisiniz. |
| 63 | Oluşturulmuş program eksik, yüklensin. |
| 64 | Eksik kıvrımlı program, kıvrımlı 'yı yükler. |
| 65 | GPG programı eksik, GPG 'yi yükler. |

## <a name="onboarding-error-codes"></a>Ekleme hata kodları

| Hata kodu | Anlamı |
| --- | --- |
| 2 | Omsadmin betiğine geçersiz seçenek belirtildi. Kullanım için `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` çalıştırın. |
| 3 | Omsadmin betiğine geçersiz yapılandırma belirtildi. Kullanım için `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` çalıştırın. |
| 4 | Omsadmin betiğine geçersiz proxy belirtildi. Proxy 'yi doğrulayın ve [BIR http proxy 'si kullanmaya yönelik belgelerimizi](log-analytics-agent.md#network-firewall-requirements)görüntüleyin. |
| 5 | Azure Izleyici 'den 403 HTTP hatası alındı. Ayrıntılar için omsadmin betiğinin tam çıktısına bakın. |
| 6 | Azure Izleyici 'den 200 olmayan HTTP hatası alındı. Ayrıntılar için omsadmin betiğinin tam çıktısına bakın. |
| 7 | Azure Izleyici ile bağlantı kurulamıyor. Ayrıntılar için omsadmin betiğinin tam çıktısına bakın. |
| 8 | Log Analytics çalışma alanına ekleme hatası. Ayrıntılar için omsadmin betiğinin tam çıktısına bakın. |
| 30 | İç betik hatası. Çıkışındaki ayrıntılarla ilgili bir [GitHub sorunu verin](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 31 | Aracı KIMLIĞI oluşturulurken hata oluştu. Çıkışındaki ayrıntılarla ilgili bir [GitHub sorunu verin](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 32 | Sertifikalar oluşturulurken hata oluştu. Ayrıntılar için omsadmin betiğinin tam çıktısına bakın. |
| 33 | Omsconfig için metaconfiguration oluşturulurken hata oluştu. Çıkışındaki ayrıntılarla ilgili bir [GitHub sorunu verin](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 34 | Metaconfiguration oluşturma betiği yok. `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`ile ekleme işlemini yeniden deneyin. |

## <a name="enable-debug-logging"></a>Hata ayıklama günlüğünü etkinleştir
### <a name="oms-output-plugin-debug"></a>OMS çıkış eklentisi hata ayıklaması
 Floentd, eklentiler ve çıktılar için farklı günlük düzeyleri belirtmenize olanak sağlayan, eklentiye özgü günlük düzeyleri sağlar. OMS çıkışı için farklı bir günlük düzeyi belirtmek üzere `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`adresindeki genel aracı yapılandırmasını düzenleyin.  

 OMS çıkış eklentisinde, yapılandırma dosyasının sonundan önce, `info` `log_level` özelliğini `debug`olarak değiştirin:

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

Hata ayıklama günlüğü, Azure Izleyici 'ye türe, veri öğelerinin sayısına ve gönderilmek üzere harcanan zamana göre ayrılmış toplu karşıya yüklemeleri görmenizi sağlar:

*Örnek hata ayıklama etkin günlüğü:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Ayrıntılı çıkış
OMS çıkış eklentisini kullanmak yerine, veri öğelerini doğrudan `stdout`, Linux için Log Analytics Aracısı günlük dosyasında görünür hale getirebilirsiniz.

`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`genel aracı yapılandırma dosyasında, her satırın önüne bir `#` ekleyerek OMS çıkış eklentisine açıklama ekleyin: Log Analytics

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Çıkış eklentisinin altında, her satırın önündeki `#` kaldırarak aşağıdaki bölümün açıklamasını kaldırın:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Sorun: proxy ile Azure Izleyici arasında bağlantı kurulamıyor

### <a name="probable-causes"></a>Olası nedenler
* Ekleme sırasında belirtilen ara sunucu yanlıştı
* Azure Izleyici ve Azure Otomasyonu hizmet uç noktaları, veri merkezinizde beyaz listede değil 

### <a name="resolution"></a>Çözünürlük
1. `-v` etkin seçeneği ile aşağıdaki komutu kullanarak, Linux için Log Analytics aracısıyla Azure Izleyici 'ye yeniden giriş yapın. Proxy aracılığıyla Azure Izleyici 'ye bağlanan aracının ayrıntılı çıkışının yapılmasına izin verir. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Aracıyı bir ara sunucu üzerinden iletişim kurmak üzere doğru şekilde yapılandırdığınızdan emin olmak için [proxy ayarlarını güncelleştirme](agent-manage.md#update-proxy-settings) bölümüne bakın.    
* Aşağıdaki Azure Izleyici uç noktalarının beyaz listelenmiş olduğunu iki kez denetleyin:

    |Aracı Kaynağı| Bağlantı Noktaları | Yön |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Bağlantı noktası 443| Gelen ve giden |  
    |*.oms.opinsights.azure.com | Bağlantı noktası 443| Gelen ve giden |  
    |*.blob.core.windows.net | Bağlantı noktası 443| Gelen ve giden |  
    |*.azure-automation.net | Bağlantı noktası 443| Gelen ve giden | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Sorun: ekleme girişimi sırasında 403 hatası alıyorsunuz

### <a name="probable-causes"></a>Olası nedenler
* Linux sunucusunda tarih ve saat yanlış 
* Kullanılan çalışma alanı KIMLIĞI ve çalışma alanı anahtarı doğru değil

### <a name="resolution"></a>Çözünürlük

1. Komut tarihi ile Linux sunucunuzdaki saati denetleyin. Süre geçerli zamandan +/-15 dakika ise, ekleme başarısız olur. Bu güncelleştirmeyi düzeltmek için Linux sunucunuzun tarihi ve/veya saat dilimi. 
2. Linux için Log Analytics aracısının en son sürümünü yüklediğinizden emin olun.  En yeni sürüm, ekleme başarısızlığının ne zaman eğriliği olduğunu size bildirir.
3. Bu makalenin önceki kısımlarında yer aldığı yükleme yönergelerinden sonra doğru çalışma alanı KIMLIĞI ve çalışma alanı anahtarı kullanılarak yeniden oluşturuluyor.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Sorun: kayıt sonrasında günlük dosyasında bir 500 ve 404 hatası görürsünüz
Bu, Linux verilerinin ilk kez karşıya yüklenirken Log Analytics bir çalışma alanına gerçekleşen bilinen bir sorundur. Bu, gönderilen verileri veya hizmet deneyimini etkilemez.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Sorun: omıagent 'ı %100 CPU 'SU ile görüyorsunuz

### <a name="probable-causes"></a>Olası nedenler
NSS-pek paketi [v 1.0.3 -5. EL7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) ' de bir gerileme, önemli bir performans sorununa neden oldu. Bu, Redhat/CentOS 7. x dağıtımları içinde çok fazla şey görüyor. Bu sorun hakkında daha fazla bilgi edinmek için, aşağıdaki belgelere bakın: hata [1667121 performans gerileme, Libböcek](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

Performansla ilgili hatalar her zaman gerçekleşmez ve yeniden oluşturulması çok zordur. Omıagent ile ilgili bir sorunla karşılaşırsanız, belirli bir eşiği aşdığınızda omıagent yığın izlemesini toplayacak olan betik omiHighCPUDiagnostics.sh kullanmanız gerekir.

1. Betiği indir <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Tanılamayı %30 saat sonra %30 CPU eşiğine göre Çalıştır <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Omiagent_trace dosyasında Callstack dökülecektir, çok sayıda kıvrımlı ve NSS işlev çağrısı fark ederseniz aşağıdaki çözüm adımlarını izleyin.

### <a name="resolution-step-by-step"></a>Çözüm (adım adım)

1. NSS-pek paketini [v 1.0.3 -5. EL7 _ 6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html)sürümüne yükseltin. <br/>
`sudo yum upgrade nss-pem`

2. NSS-PEI yükseltme için uygun değilse (genellikle CentOS üzerinde gerçekleşir), daha sonra 7.29.0-46 olarak kıvma düşürme olur. "Yıum Güncelleştir" i çalıştırırsanız, 7.29.0-51 ' e yükseltilecektir ve sorun yeniden gerçekleşir. <br/>
`sudo yum downgrade curl libcurl`

3. OMı 'yi yeniden Başlat: <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Sorun: Azure portal herhangi bir veri görmüyorsunuz

### <a name="probable-causes"></a>Olası nedenler

- Azure Izleyici 'ye ekleme başarısız oldu
- Azure Izleyici bağlantısı engellendi
- Linux verileri için Log Analytics Aracısı yedeklenir

### <a name="resolution"></a>Çözünürlük
1. Aşağıdaki dosyanın mevcut olup olmadığını denetleyerek, ekleme Azure Izleyici 'nin başarılı olup olmadığını denetleyin: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. `omsadmin.sh` komut satırı yönergelerini kullanarak yeniden ekleme
3. Proxy kullanılıyorsa, daha önce sağlanmış olan proxy çözümleme adımlarına bakın.
4. Bazı durumlarda, Linux için Log Analytics Aracısı hizmetle iletişim kuramadığınızda, aracıdaki veriler, 50 MB olan tam arabellek boyutuna göre sıraya alınır. Aracı şu komut çalıştırılarak yeniden başlatılmalıdır: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Bu sorun, aracı sürümü 1.1.0-28 ve üzeri sürümlerde düzeltilmiştir.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Sorun: iletilen syslog iletilerini görmüyorsunuz 

### <a name="probable-causes"></a>Olası nedenler
* Linux sunucusuna uygulanan yapılandırma, gönderilen tesisler ve/veya günlük düzeylerinin toplamaya izin vermiyor
* Syslog, Linux sunucusuna doğru iletilmiyor
* Saniye başına iletilen ileti sayısı, Linux 'un işlemesi için Log Analytics aracısının temel yapılandırması için çok büyük

### <a name="resolution"></a>Çözünürlük
* Syslog için Log Analytics çalışma alanındaki yapılandırmanın tüm tesisler ve doğru günlük düzeylerine sahip olduğunu doğrulayın. [Azure Portal Syslog koleksiyonunu yapılandırma '](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal) yı gözden geçirin
* Yerel Syslog mesajlaşma Daemon 'ları (`rsyslog`, `syslog-ng`) iletilen iletileri alabildiğini doğrulama
* İletilerin engellenmediğinden emin olmak için Syslog sunucusunda güvenlik duvarı ayarlarını denetleyin
* `logger` komutunu kullanarak Log Analytics Syslog iletisinin benzetimini yapın
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Sorun: omsagent günlük dosyasında zaten kullanımda olan bir adres alıyorsunuz
Omsagent. log içinde `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` görüyorsanız.

### <a name="probable-causes"></a>Olası nedenler
Bu hata, Linux Tanılama uzantısının (LAD) Log Analytics Linux VM uzantısıyla yan yana yüklendiğini ve bu bağlantı noktası, syslog veri toplama için omsagent olarak aynı bağlantı noktasını kullanıyor olduğunu gösterir.

### <a name="resolution"></a>Çözünürlük
1. Kök olarak, aşağıdaki komutları yürütün (25224 bir örnektir ve ortamınızda LAD tarafından kullanılan farklı bir bağlantı noktası numarası görmeniz olasıdır):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Ardından doğru `rsyslogd` veya `syslog_ng` yapılandırma dosyasını düzenlemeniz ve 25229 numaralı bağlantı noktasına yazmak üzere LAD ile ilgili yapılandırmayı değiştirmeniz gerekir.

2. VM `rsyslogd`çalışıyorsa, değiştirilecek dosya: `/etc/rsyslog.d/95-omsagent.conf` (varsa, değilse `/etc/rsyslog`). VM `syslog_ng`çalışıyorsa, değiştirilecek dosya: `/etc/syslog-ng/syslog-ng.conf`.
3. Omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`yeniden başlatın.
4. Syslog hizmetini yeniden başlatın.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Sorun: Temizleme seçeneğini kullanarak omsagent 'ı kaldıramıyorsunuz

### <a name="probable-causes"></a>Olası nedenler

* Linux Tanılama uzantısı yüklendi
* Linux Tanılama uzantısı yüklendi ve kaldırıldı, ancak MDSD tarafından kullanılan omsagent hakkında bir hata görürsünüz ve kaldırılamaz.

### <a name="resolution"></a>Çözünürlük
1. Linux Tanılama uzantısını (LAD) kaldırın.
2. Linux Tanılama uzantısı dosyalarını şu konumda mevcutsa makineden kaldırın: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` ve `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Sorun: herhangi bir Nagios verisi bulunmayan verileri göremezsiniz 

### <a name="probable-causes"></a>Olası nedenler
* Omsagent kullanıcısının Nagios günlük dosyasından okuma izni yok
* Nagios kaynağı ve filtresi, omsagent. conf dosyasından açıklama kaldırmadı

### <a name="resolution"></a>Çözünürlük
1. Aşağıdaki [yönergeleri](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts)Izleyerek Nagıos dosyasından okumak için omsagent kullanıcısı ekleyin.
2. Linux genel yapılandırma dosyası için Log Analytics Aracısı `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`adresinde, Nagios kaynak ve filtresinin **her ikisinin de** açıklama kaldırmamakta olduğundan emin olun.

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Sorun: herhangi bir Linux verisi görmüyorsunuz 

### <a name="probable-causes"></a>Olası nedenler
* Azure Izleyici 'ye ekleme başarısız oldu
* Azure Izleyici bağlantısı engellendi
* Sanal makine yeniden başlatıldı
* OMı paketi, Linux paketi için Log Analytics Aracısı tarafından yüklenmiş olana kıyasla daha yeni bir sürüme el ile yükseltildi
* `omsconfig.log` günlük dosyasında DSC kaynak günlükleri *sınıfı bulunamadı* hatası
* Verilerin Log Analytics Aracısı yedeklendi
* DSC günlükleri *geçerli yapılandırması yok. Bir yapılandırma dosyası belirtmek ve önce geçerli bir yapılandırma oluşturmak için-Path parametresiyle birlikte start-DscConfiguration komutunu yürütün.* `omsconfig.log` günlük dosyasında, ancak `PerformRequiredConfigurationChecks` işlemler hakkında bir günlük iletisi yok.

### <a name="resolution"></a>Çözünürlük
1. Auditd paketi gibi tüm bağımlılıkları yükler.
2. Aşağıdaki dosyanın mevcut olup olmadığını denetleyerek Azure Izleyici 'ye ekleme işleminin başarılı olup olmadığını denetleyin: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.  Aksi takdirde, omsadmin.sh komut satırı [yönergeleri](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)kullanılarak yeniden ekleyin.
4. Proxy kullanılıyorsa, yukarıdaki proxy sorun giderme adımlarını kontrol edin.
5. Bazı Azure dağıtım sistemlerinde OMID OMı sunucu arka plan programı, sanal makine yeniden başlatıldıktan sonra başlatılmaz. Bu, denetim, ChangeTracking veya UpdateManagement çözümüyle ilgili verileri görmeyecektir. Geçici çözüm, `sudo /opt/omi/bin/service_control restart`çalıştırarak OMI sunucusunu el ile başlatsın.
6. OMı paketi daha yeni bir sürüme el ile yükseltildikten sonra, Log Analytics aracısının çalışmaya devam etmesi için el ile yeniden başlatılması gerekir. Bu adım, OMı sunucusunun yükseltildikten sonra otomatik olarak başlamadığından dolayı bazı kaldırmalar için gereklidir. OMı 'yi yeniden başlatmak için `sudo /opt/omi/bin/service_control restart` çalıştırın.
7. Omsconfig. log dosyasında DSC kaynak *sınıfı bulunamadı* hatası görürseniz, `sudo /opt/omi/bin/service_control restart`çalıştırın.
8. Bazı durumlarda, Linux için Log Analytics Aracısı Azure Izleyici ile iletişim kuramadığı zaman aracıdaki veriler tam arabellek boyutuna yedeklenir: 50 MB. Aşağıdaki komut `/opt/microsoft/omsagent/bin/service_control restart`çalıştırılarak aracının yeniden başlatılması gerekir.

    >[!NOTE]
    >Bu sorun aracı sürümü 1.1.0-28 veya üzeri sürümlerde düzeltildi
    >

* `omsconfig.log` günlük dosyası `PerformRequiredConfigurationChecks` işlemlerinin sistemde düzenli olarak çalıştığını belirtmezse, cron işi/hizmeti ile ilgili bir sorun olabilir. `/etc/cron.d/OMSConsistencyInvoker`altında cron işinin bulunduğundan emin olun. Gerekirse cron işini oluşturmak için aşağıdaki komutları çalıştırın:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Ayrıca, cron hizmetinin çalıştığından emin olun. Bu hizmetin durumunu denetlemek için debir, Ubuntu, SUSE veya `service crond status` RHEL, CentOS Oracle Linux ile `service cron status` kullanabilirsiniz. Hizmet yoksa, ikili dosyaları yükleyebilir ve aşağıdakileri kullanarak hizmeti başlatabilirsiniz:

    **Ubuntu/dene**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Sorun: Syslog veya Linux performans sayaçları için portaldan koleksiyon yapılandırılırken ayarlar uygulanmaz

### <a name="probable-causes"></a>Olası nedenler
* Linux için Log Analytics Aracısı en son yapılandırmayı çekmedi
* Portalda değiştirilen ayarlar uygulanmadı

### <a name="resolution"></a>Çözünürlük
**Arka plan:** `omsconfig`, beş dakikada bir yeni Portal-tarafı yapılandırma için arama yapan Linux yapılandırma aracısına yönelik Log Analytics aracısıdır. Bu yapılandırma daha sonra/etc/seçenek/Microsoft/omsagent/conf/omsagent.exe konumunda bulunan Linux yapılandırma dosyaları için Log Analytics aracısına uygulanır.

* Bazı durumlarda, Linux yapılandırma Aracısı için Log Analytics Aracısı, en son yapılandırmanın uygulanmadığı için Portal yapılandırma hizmeti ile iletişim kuramayabilir.
  1. `dpkg --list omsconfig` veya `rpm -qi omsconfig`çalıştırarak `omsconfig` aracısının yüklü olduğundan emin olun.  Yüklü değilse, Linux için Log Analytics aracısının en son sürümünü yeniden yükleyin.

  2. `omsconfig` aracısının Azure Izleyici ile iletişim kurabildiğini denetleyin `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`aşağıdaki komutu çalıştırın. Bu komut, syslog ayarları, Linux performans sayaçları ve özel Günlükler dahil olmak üzere, aracının hizmetten aldığı yapılandırmayı döndürür. Bu komut başarısız olursa, aşağıdaki komutu `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`çalıştırın. Bu komut omsmsconfig aracısının Azure Izleyici ile konuştuğunu ve en son yapılandırmayı almasına zorlar.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Sorun: herhangi bir özel günlük verisi görmüyorsunuz 

### <a name="probable-causes"></a>Olası nedenler
* Azure Izleyici 'ye ekleme başarısız oldu.
* Bu ayar **Linux sunucularıma aşağıdaki yapılandırmayı uygular** .
* omsmsconfig, hizmetten en son özel günlük yapılandırmasını almadı.
* Linux Kullanıcı `omsagent` Log Analytics Aracısı, izinler nedeniyle özel günlüğe erişemiyor veya bulunamadı.  Aşağıdaki hataları görebilirsiniz:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Linux sürümü için Log Analytics aracısında düzeltilen yarış durumu ile ilgili bilinen sorun 1.1.0-,

### <a name="resolution"></a>Çözünürlük
1. Şu dosyanın mevcut olup olmadığını kontrol ederek Azure Izleyici 'ye ekleme işleminin başarılı olduğunu doğrulayın: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`. Aksi takdirde şunlardan birini yapın:  

  1. Omsadmin.sh komut satırı [yönergeleri](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)kullanılarak yeniden ekleme.
  2. Azure portal **Gelişmiş ayarlar** altında, **aşağıdaki yapılandırmayı Linux sunucularıma Uygula** ayarının etkinleştirildiğinden emin olun.  

2. `omsconfig` aracısının Azure Izleyici ile iletişim kurabildiğini denetleyin `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`aşağıdaki komutu çalıştırın.  Bu komut, syslog ayarları, Linux performans sayaçları ve özel Günlükler dahil olmak üzere, aracının hizmetten aldığı yapılandırmayı döndürür. Bu komut başarısız olursa, aşağıdaki komutu `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`çalıştırın. Bu komut omsmsconfig aracısının Azure Izleyici ile konuştuğunu ve en son yapılandırmayı almasına zorlar.

**Arka plan:** Ayrıcalıklı kullanıcı `root`olarak çalışan Linux için Log Analytics Aracısı yerine, aracı `omsagent` Kullanıcı olarak çalışır. Çoğu durumda, belirli dosyaların okunamadığı için bu kullanıcıya açık izin verilmelidir. `omsagent` kullanıcıya izin vermek için aşağıdaki komutları çalıştırın:

1. `omsagent` kullanıcıyı belirli bir gruba ekleyin `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Gerekli dosyaya evrensel okuma erişimi verin `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Linux sürümü için 1.1.0-, öncesi Log Analytics aracısına sahip bir yarış durumu ile ilgili bilinen bir sorun vardır. En son aracıya güncelleştirdikten sonra, `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`çıkış eklentisinin en son sürümünü almak için aşağıdaki komutu çalıştırın.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Sorun: yeni bir çalışma alanına yeniden ekleme yapmaya çalışıyorsunuz
Bir aracıyı yeni bir çalışma alanına yeniden eklemeyi denediğinizde, Log Analytics Aracısı yapılandırmasının yeniden ekleme işleminden önce temizlenmesi gerekir. Aracıdan eski yapılandırmayı temizlemek için, kabuk paketini `--purge` ile çalıştırın

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Veya

```
sudo sh ./onboard_agent.sh --purge
```

`--purge` seçeneğini kullanarak yeniden eklemeye devam edebilirsiniz

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Azure portal Log Analytics Aracısı uzantısı hatalı bir durumla işaretlendi: sağlama başarısız oldu

### <a name="probable-causes"></a>Olası nedenler
* Log Analytics Aracısı işletim sisteminden kaldırıldı
* Log Analytics aracı hizmeti çalışmıyor, devre dışı veya yapılandırılmamış

### <a name="resolution"></a>Çözünürlük 
Sorunu gidermek için aşağıdaki adımları gerçekleştirin.
1. Uzantıyı Azure portal kaldır.
2. [Yönergeleri](../../azure-monitor/learn/quick-collect-linux-computer.md)izleyerek aracıyı yükler.
3. Şu komutu çalıştırarak aracıyı yeniden başlatın: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Birkaç dakika bekleyin ve sağlama durumu, **sağlama başarılı**olarak değişir.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Sorun: isteğe bağlı Log Analytics aracı yükseltmesi

### <a name="probable-causes"></a>Olası nedenler

Konaktaki Log Analytics aracı paketleri güncel değildir.

### <a name="resolution"></a>Çözünürlük 
Sorunu gidermek için aşağıdaki adımları gerçekleştirin.

1. [Sayfada](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/)en son sürümü denetleyin.
2. Yükleme betiğini indir (örnek sürüm olarak 1.4.2-124):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. `sudo sh ./omsagent-*.universal.x64.sh --upgrade`yürüterek paketleri yükseltin.
