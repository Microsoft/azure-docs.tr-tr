---
title: Linux bilgisayarlarına Log Analytics aracısını yükleme
description: Bu makalede, diğer bulutlarda veya şirket içinde barındırılan Linux bilgisayarların Linux için Log Analytics aracısıyla Azure Izleyici 'ye nasıl bağlanacağı açıklanır.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 23597804a34a9bc409db179010569024aa472016
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725935"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Linux bilgisayarlarına Log Analytics aracısını yükleme
Bu makalede, aşağıdaki yöntemleri kullanarak Linux bilgisayarlarına Log Analytics aracısını yükleme hakkında ayrıntılı bilgi verilmektedir:

* GitHub 'da barındırılan [bir sarmalayıcı betiği kullanarak Linux için aracıyı yükler](#install-the-agent-using-wrapper-script) . Bu, bilgisayarın Internet bağlantısı olduğunda (doğrudan veya bir proxy sunucusu aracılığıyla) aracıyı yüklemek ve yükseltmek için önerilen yöntemdir.
* Aracıyı [el ile indirip yükleyin](#install-the-agent-manually) . Bu, Linux bilgisayarın Internet erişimi olmadığında ve [Log Analytics ağ geçidi](./gateway.md)aracılığıyla Azure Izleyici veya Azure Otomasyonu ile iletişim kurdukları durumlarda gereklidir. 

>[!IMPORTANT]
> Bu makalede açıklanan yükleme yöntemleri genellikle şirket içi veya diğer bulutlardaki sanal makineler için kullanılır. Azure sanal makineleri için kullanabileceğiniz daha verimli seçenekler için bkz. [yükleme seçenekleri](./log-analytics-agent.md#installation-options) .



## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Log Analytics Aracısı tarafından desteklenen Linux dağıtımların listesi için bkz. [Azure izleyici aracılarına genel bakış](agents-overview.md#supported-operating-systems) .

>[!NOTE]
>OpenSSL 1.1.0 yalnızca x86_x64 platformlarında desteklenir (64-bit) ve OpenSSL, 1. x ' den önceki bir platformda desteklenmez.

>[!NOTE]
>Kapsayıcılarda Log Analytics Linux aracısının çalıştırılması desteklenmez. Kapsayıcıları izlemeniz gerekiyorsa, lütfen Docker konakları veya Kubernetes için [kapsayıcı öngörüleri](../containers/container-insights-overview.md) Için [kapsayıcı izleme çözümünü](../containers/containers.md) kullanın.

2018 Ağustos 'Tan sonra yayınlanan sürümlerle başlayarak, destek modelimiz için aşağıdaki değişiklikleri yapıyoruz:  

* İstemci değil yalnızca sunucu sürümleri desteklenir.  
* Azure Linux tarafından onaylanan bazı [destekler](../../virtual-machines/linux/endorsed-distros.md)için odak desteği. Azure Linux tarafından onaylama ve Log Analytics Linux Aracısı için desteklenmekte olan yeni bir delinler/sürüm arasında bazı gecikme olabileceğini unutmayın.
* Listelenen her ana sürüm için tüm küçük yayınlar desteklenir.
* Üreticisinin destek sonu tarihini geçen sürümler desteklenmez.
* Yalnızca VM görüntülerini destekler; Hatta, resmi olmayan yayımcıların görüntülerinin türevi olan kapsayıcılar desteklenmez.
* Yeni bir AMı sürümü desteklenmez.  
* Yalnızca OpenSSL 1. x 'i varsayılan olarak çalıştıran sürümler desteklenir.

>[!NOTE]
>Şu anda desteklenmeyen ve destek modelimize hizalanmayan bir veya daha fazla sürümü kullanıyorsanız, bu depoyu çatalla, Microsoft destek 'in aracılı aracı sürümleriyle ilgili yardım sağlamayamayacak olduğunu bildiren bu depoyu çatallandırmanızı öneririz.

### <a name="python-requirement"></a>Python gereksinimi

Aracı sürümü 1.13.27 'den başlayarak, Linux Aracısı hem Python 2 hem de 3 ' ü destekleyecektir. Her zaman en son aracıyı kullanmanızı öneririz. 

Aracının eski bir sürümünü kullanıyorsanız, sanal makinenin varsayılan olarak Python 2 ' yi kullanması gerekir. Sanal makineniz, varsayılan olarak Python 2 ' yi içermeyen bir demi kullanıyorsa, bunu kurmanız gerekir. Aşağıdaki örnek komutlar farklı distros üzerinde Python 2 ' ye yüklenir.

 - Red hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, debir: `apt-get install -y python2`
 - SUSE `zypper install -y python2`

Python2 yürütülebilir dosyası *Python*'un diğer adı olmalıdır. Aşağıda, bu diğer adı ayarlamak için kullanabileceğiniz bir yöntem verilmiştir:

1. Mevcut diğer adları kaldırmak için aşağıdaki komutu çalıştırın.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Diğer adı oluşturmak için aşağıdaki komutu çalıştırın.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>Desteklenen Linux sağlamlaştırma
OMS aracısında Linux için sınırlı özelleştirme ve sağlamlaştırma desteği vardır.

Aşağıdakiler şu anda desteklenmektedir: 
- FIPS

Aşağıdakiler desteklenmez:
- CI
- SELINUX

CIS ve SELINUX sağlamlaştırma desteği, [Azure Izleme Aracısı](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview)için planlanmaktadır. Daha fazla sağlamlaştırma ve özelleştirme yöntemleri, OMS Aracısı için desteklenmez veya planlanmaz.  

## <a name="agent-prerequisites"></a>Aracı önkoşulları

Aşağıdaki tabloda, aracının yükleneceği [desteklenen Linux destekleri](#supported-operating-systems) için gereken paketler vurgulanmıştır.

|Gerekli paket |Description |En düşük sürüm |
|-----------------|------------|----------------|
|GLIBC |    GNU C Kitaplığı | 2.5-12 
|Openssl    | OpenSSL kitaplıkları | 1.0. x veya 1.1. x |
|Curl | Web istemcisini kıvır | 7.15.5 |
|Python | | 2,7 veya 3.6 +
|Python-ctypes | | 
|PAM | Eklenebilir Kimlik Doğrulaması Modülleri | | 

>[!NOTE]
>Syslog iletilerini toplamak için rsyslog veya Syslog-ng gerekir. Red Hat Enterprise Linux, CentOS ve Oracle Linux sürümünün (sysklog) sürüm 5 ' te bulunan varsayılan Syslog Daemon, Syslog olay koleksiyonu için desteklenmez. Bu dağıtımların bu sürümünden Syslog verileri toplamak için rsyslog arka plan programı yüklenmeli ve sysklog ' ı değiştirecek şekilde yapılandırılmalıdır.

## <a name="network-requirements"></a>Ağ gereksinimleri
Linux aracısının ağ gereksinimleri için bkz. [Log Analytics aracısına genel bakış](./log-analytics-agent.md#network-requirements) .

## <a name="agent-install-package"></a>Aracı yüklemesi paketi

Linux için Log Analytics Aracısı birden çok paketten oluşur. Yayın dosyası, Shell paketi şu parametreyle çalıştırılarak kullanılabilir olan aşağıdaki paketleri içerir `--extract` :

**Paket** | **Sürüm** | **Açıklama**
----------- | ----------- | --------------
omsagent | 1.13.9 | Linux için Log Analytics Aracısı
omsconfig | 1.1.1 | Log Analytics Aracısı için yapılandırma Aracısı
OMI | 1.6.4 | Açık yönetim altyapısı (OMı)-basit bir CıM sunucusu. *OMı 'nın hizmetin çalışması için gerekli bir cron işini çalıştırmak için kök erişimi gerektirdiğini unutmayın.*
SCX | 1.6.4 | İşletim sistemi performans ölçümleri için OMı CıM sağlayıcıları
Apache-cimprov | 1.0.1 | OMı için Apache HTTP sunucu performansı izleme sağlayıcısı. Yalnızca Apache HTTP sunucusu algılanırsa yüklenir.
MySQL-cimprov | 1.0.1 | OMı için MySQL Server performans izleme sağlayıcısı. Yalnızca MySQL/MariaDB sunucusu algılanırsa yüklenir.
Docker-cimprov | 1.0.0 | OMı için Docker sağlayıcısı. Yalnızca Docker algılanırsa yüklenir.

### <a name="agent-installation-details"></a>Aracı Yükleme ayrıntıları

Linux paketleri için Log Analytics aracısını yükledikten sonra, aşağıdaki ek sistem genelinde yapılandırma değişiklikleri uygulanır. Omsagent paketi kaldırıldığında bu yapıtlar kaldırılır.

* Ayrıcalıklı olmayan bir Kullanıcı adlı: `omsagent` oluşturulur. Daemon bu kimlik bilgisi altında çalışır. 
* İçinde bir sudoers *içerme* dosyası oluşturulur `/etc/sudoers.d/omsagent` . Bu `omsagent` , syslog ve omsagent Daemon 'ları 'ı yeniden başlatmayı yetkilendirir. Sudo *ekleme* yönergeleri, sudo 'ın yüklü sürümünde desteklenmiyorsa, bu girişlerin üzerine yazılır `/etc/sudoers` .
* Syslog yapılandırması bir olay alt kümesini aracıya iletecek şekilde değiştirilir. Daha fazla bilgi için bkz. [Syslog veri toplamayı yapılandırma](data-sources-syslog.md).

İzlenen bir Linux bilgisayarında aracı olarak listelenir `omsagent` . `omsconfig` , Linux yapılandırma aracısına yönelik Log Analytics aracısıdır ve her 5 dakikada bir yeni Portal tarafı yapılandırmasını arar. Yeni ve güncelleştirilmiş yapılandırma konumunda bulunan aracı yapılandırma dosyalarına uygulanır `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="install-the-agent-using-wrapper-script"></a>Sarmalayıcı betiği kullanarak aracıyı yükler

Aşağıdaki adımlar, GitHub 'da barındırılan aracıyı indirmek ve aracıyı yüklemek için doğrudan veya bir proxy sunucusu üzerinden iletişim kurabilen Linux bilgisayarları için sarmalayıcı betiği kullanarak Azure ve Azure Kamu Bulutu 'nda Log Analytics için aracının kurulumunu yapılandırır.  

Linux bilgisayarınızın Log Analytics için bir proxy sunucusu üzerinden iletişim kurması gerekiyorsa, bu yapılandırma komut satırında dahil ederek belirlenebilir `-p [protocol://][user:password@]proxyhost[:port]` . *Protocol* özelliği `http` , veya kabul eder `https` ve *proxyhost* özelliği proxy sunucusunun tam etki alanı adını veya IP adresini kabul eder. 

Örnek: `https://proxy01.contoso.com:30443`

Her iki durumda da kimlik doğrulaması gerekliyse, Kullanıcı adını ve parolayı belirtmeniz gerekir. Örnek: `https://user01:password@proxy01.contoso.com:30443`

1. Linux bilgisayarı bir Log Analytics çalışma alanına bağlanacak şekilde yapılandırmak için, çalışma alanı KIMLIĞI ve birincil anahtar sağlamak üzere aşağıdaki komutu çalıştırın. Bu komut aracıyı indirir, sağlama toplamını doğrular ve aracıyı yükler.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Aşağıdaki komut proxy `-p` parametresini ve kimlik doğrulaması proxy sunucunuz için gerekliyse örnek sözdizimini içerir:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Linux bilgisayarı Azure Kamu Bulutu 'nda Log Analytics çalışma alanına bağlanacak şekilde yapılandırmak için, daha önce kopyalanmış çalışma alanı KIMLIĞI ve birincil anahtar sağlamak üzere aşağıdaki komutu çalıştırın. Bu komut aracıyı indirir, sağlama toplamını doğrular ve aracıyı yükler. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Aşağıdaki komut proxy `-p` parametresini ve kimlik doğrulaması proxy sunucunuz için gerekliyse örnek sözdizimini içerir:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Aşağıdaki komutu çalıştırarak aracıyı yeniden başlatın: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## <a name="install-the-agent-manually"></a>Aracıyı el ile yükleme

Linux için Log Analytics Aracısı kendiliğinden ayıklanan ve yüklenebilir bir kabuk betik paketinde sunulmaktadır. Bu paket, aracı bileşenlerinden her biri için Demcı ve RPM paketleri içerir ve tek tek paketleri almak için doğrudan veya ayıklanamaz. X64 için bir paket ve x86 mimarileri için bir paket sağlanır. 

> [!NOTE]
> Azure VM 'lerinde, Linux için [azure log ANALYTICS VM uzantısını](../../virtual-machines/extensions/oms-linux.md) kullanarak aracıyı bunlara yüklemenizi öneririz. 

1. SCP/SFTP kullanarak uygun paketi (x64 veya x86) Linux sanal makinenize veya fiziksel bilgisayarınıza [indirin](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) ve aktarın.

2. Bağımsız değişkenini kullanarak paketi yükler `--install` . Yükleme sırasında bir Log Analytics çalışma alanına eklemek için, `-w <WorkspaceID>` `-s <workspaceKey>` daha önce kopyalanmış olan ve parametreleri sağlayın.

    >[!NOTE]
    >`--upgrade`Linux için System Center Operations Manager Aracısı zaten yüklüyse, OMI, SCX, omsmsconfig veya daha eski sürümleri gibi bağımlı paketler yüklüyse bağımsız değişkenini kullanmanız gerekir. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Linux aracısını, bir Log Analytics ağ geçidi aracılığıyla bir Log Analytics çalışma alanına yüklemek ve bağlanmak üzere yapılandırmak için, proxy, çalışma alanı KIMLIĞI ve çalışma alanı anahtarı parametrelerini sağlayan aşağıdaki komutu çalıştırın. Bu yapılandırma, öğesini ekleyerek komut satırında belirtilebilir `-p [protocol://][user:password@]proxyhost[:port]` . *Proxyhost* özelliği, Log Analytics ağ geçidi sunucusunun tam etki alanı adını veya IP adresini kabul eder.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Kimlik doğrulaması gerekliyse, Kullanıcı adını ve parolayı belirtmeniz gerekir. Örnek: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Linux bilgisayarı Azure Kamu bulutundaki bir Log Analytics çalışma alanına bağlanacak şekilde yapılandırmak için, daha önce kopyalanmış çalışma alanı KIMLIĞI ve birincil anahtarı sağlamak üzere aşağıdaki komutu çalıştırın.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Aracı paketlerini yüklemek ve daha sonra belirli bir Log Analytics çalışma alanına raporlamak üzere yapılandırmak istiyorsanız aşağıdaki komutu çalıştırın:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Aracıyı yüklemeden Aracı paketlerini paketten ayıklamak istiyorsanız aşağıdaki komutu çalıştırın:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>Önceki sürümden yükseltme

Önceki sürümden yükseltme, 1.0.0-47 sürümünden başlayarak her sürümde desteklenir. `--upgrade`Aracının tüm bileşenlerini en son sürüme yükseltmek için parametresiyle birlikte yüklemeyi gerçekleştirin.

## <a name="cache-information"></a>Önbellek bilgileri
Linux için Log Analytics aracısındaki veriler, Azure Izleyici 'ye gönderilmeden önce *% STATE_DIR_WS%/out_oms_common*. Buffer * konumundaki yerel makinede önbelleğe alınır. Özel günlük verilerinin *% STATE_DIR_WS%/out_oms_blob*. Buffer * içinde ara belleğe alınmış olması. Yol bazı [çözümler ve veri türleri](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=)için farklı olabilir.

Aracı 20 saniyede bir karşıya yüklemeye çalışır. Başarısız olursa, bu işlem başarılı olana kadar üstel bir zaman uzunluğu artırır: ikinci denemeden önce 30 saniye, üçüncü, 120 saniyeden önce 60 saniye... Bu nedenle, yeniden denemeler arasında başarıyla bağlanana kadar en fazla 16 dakikalık bir süre sürer. Aracı, belirli bir veri öbeğinin sonuna kadar, bir sonrakine geçmeden önce 6 kez yeniden dener. Bu, aracı başarıyla yeniden karşıya yükleyeünceye kadar devam eder. Bu, verilerin atılmadan yaklaşık 30 dakikaya kadar arabelleğe alınmayacağı anlamına gelir.

Varsayılan önbellek boyutu 10 MB 'tır, ancak [omsagent. conf dosyasında](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf)değiştirilebilir.


## <a name="next-steps"></a>Sonraki adımlar

- Aracıyı sanal makineden yeniden yapılandırma, yükseltme veya kaldırma hakkında bilgi edinmek için [Windows ve Linux için Log Analytics aracısının yönetimini ve güvenliğini](agent-manage.md) gözden geçirin.

- Aracıyı yüklerken veya yönetirken sorunlarla karşılaşırsanız [, Linux aracısının sorunlarını giderme](agent-linux-troubleshoot.md) konusunu gözden geçirin.
