---
title: CEF verilerini Azure Sentinel 'e bağlamak için günlük ileticisini dağıtın | Microsoft Docs
description: CEF verilerini Azure Sentinel 'e bağlamak için aracıyı dağıtmayı öğrenin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2021
ms.author: yelevin
ms.openlocfilehash: ee28837d3e687d78b645a1ab18a9add1f8e57fcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771269"
---
# <a name="step-1-deploy-the-log-forwarder"></a>1. Adım: günlük ileticisini dağıtma


Bu adımda, günlükleri güvenlik çözümünüzden Azure Sentinel çalışma alanınıza ileten Linux makinesini belirlersiniz ve yapılandıracaksınız. Bu makine, şirket içi ortamınızda, Azure VM 'de veya başka bir buluttaki bir sanal makinede bulunan fiziksel veya sanal bir makine olabilir. Belirtilen bağlantıyı kullanarak, belirlenen makinede aşağıdaki görevleri gerçekleştiren bir komut dosyası çalıştırırsınız:

- , Linux için Log Analytics aracısını (OMS Aracısı olarak da bilinir) yükleyerek aşağıdaki amaçlarla yapılandırır:
    - TCP bağlantı noktası 25226 ' deki yerleşik Linux Syslog arka plan programından CEF iletilerini dinleme
    - Bunların ayrıştırılabildiği ve zenginleştirdiği Azure Sentinel çalışma alanınıza TLS üzerinden güvenli bir şekilde gönderme

- Yerleşik Linux Syslog cinini (rsyslog. d/syslog-ng) aşağıdaki amaçlarla yapılandırır:
    - TCP bağlantı noktası 514 ' deki güvenlik çözümlerinizde syslog iletilerini dinleme
    - TCP bağlantı noktası 25226 kullanarak yalnızca, CEF olarak tanımladığı iletileri localhost üzerinde Log Analytics aracısına iletme
 
## <a name="prerequisites"></a>Önkoşullar

- Belirlenen Linux makinenizde yükseltilmiş izinleriniz (sudo) olmalıdır.

- Linux makinesinde **python 2,7** veya **3** ' ün yüklü olması gerekir.<br>`python -version`Denetlemek için komutunu kullanın.

- Log Analytics aracısını yüklemeden önce Linux makinenin herhangi bir Azure çalışma alanına bağlı olmaması gerekir.

- Linux makinenizin en az **4 CPU çekirdeği ve 8 GB RAM** olması gerekir.

    > [!NOTE]
    > - **Rsyslog** Daemon kullanan tek bir günlük iletici makinesi, toplanan **saniyede 8500 olay kadar (EPS)** desteklenen kapasiteye sahiptir.

- Bu işlemin bir noktasında çalışma alanı KIMLIĞI ve çalışma alanı birincil anahtarına ihtiyacınız olabilir. Onları çalışma alanı kaynağında, **aracılar yönetimi** altında bulabilirsiniz.

## <a name="run-the-deployment-script"></a>Dağıtım betiğini çalıştırma
 
1. Azure Sentinel gezinti menüsünde, **veri bağlayıcıları**' na tıklayın. Bağlayıcılar listesinden **ortak olay biçimi (CEF)** kutucuğuna ve ardından sağ alt köşedeki **bağlayıcı sayfası aç** düğmesine tıklayın. 

1. 1,2 altında, **CEF toplayıcısını Linux makinesine yüklemek** için aşağıdaki betiği Çalıştır altında belirtilen bağlantıyı kopyalayın **ve CEF toplayıcısı 'nı yüklemek ve uygulamak için aşağıdaki** METINDEN (çalışma alanı kimliğini ve birincil anahtarı yer tutucuları yerine uygulayarak) uygulayın:

    ```bash
    sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]
    ```

1. Betik çalışırken, herhangi bir hata veya uyarı iletisi aldığınızdan emin olmak için kontrol edin.
    - *Bilgisayar* alanının eşlemesiyle bir sorunu düzeltmek için bir komut çalıştırmanızı yönlendiren bir ileti alabilirsiniz. Ayrıntılar için [dağıtım betiğinin](#mapping-command) açıklamasına bakın.

1. 2. [Adım: CEF iletilerini iletmek için Güvenlik çözümünüzü yapılandırma](connect-cef-solution-config.md).


> [!NOTE]
> **Hem düz Syslog *hem* de CEF iletilerini iletmek için aynı makineyi kullanma**
>
> Bu günlük iletici makinesini, [syslog iletilerini](connect-syslog.md) ve CEF 'yi iletmek için kullanmayı planlıyorsanız, olayların Syslog ve CommonSecurityLog tablolarına çoğaltılmasını önlemek için:
>
> 1. CEF biçiminde ileticiye günlük gönderen her kaynak makinede, CEF iletilerini göndermek için kullanılan tesisleri kaldırmak için Syslog yapılandırma dosyasını düzenlemeniz gerekir. Bu şekilde, CEF 'de gönderilen tesisler Syslog 'da da gönderilmeyecektir. Bunun nasıl yapılacağı hakkında ayrıntılı yönergeler için bkz. [Linux aracısında Syslog yapılandırma](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent) .
>
> 1. Aracının Azure Sentinel 'de Syslog yapılandırmasıyla eşitlenmesini devre dışı bırakmak için bu makinelerde aşağıdaki komutu çalıştırmanız gerekir. Bu, önceki adımda yaptığınız yapılandırma değişikliğinin üzerine yazılmamasını sağlar.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

> [!NOTE]
> **TimeGenerated alanının kaynağını değiştirme**
>
> - Varsayılan olarak, Log Analytics Aracısı şemadaki *TimeGenerated* alanını, aracının Syslog arka plan programından olayı aldığı zamana göre doldurur. Sonuç olarak kaynak sistemde olayın oluşturulma zamanı Azure Sentinel'de kaydedilmez.
>
> - Ancak, komut dosyasını indirecek ve çalıştıracak aşağıdaki komutu çalıştırabilirsiniz `TimeGenerated.py` . Bu betik, Log Analytics aracısını aracı tarafından alındığı zaman yerine, *TimeGenerated* alanını olayın kaynak sistemindeki özgün zamanına göre doldurmak üzere yapılandırır.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="deployment-script-explained"></a>Dağıtım betiği açıklanmıştı

Aşağıda dağıtım betiğinin eylemlerinin komut satırı açıklaması verilmiştir.

Uygun açıklamayı görmek için bir Syslog Daemon seçin.

# <a name="rsyslog-daemon"></a>[rsyslog Daemon](#tab/rsyslog)

1. **Log Analytics aracısını indirme ve yükleme:**

    - Log Analytics (OMS) Linux aracısının yükleme betiğini indirir.

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Log Analytics aracısını yükleme.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **25226 bağlantı noktasını dinlemek için Log Analytics aracı yapılandırmasını ayarlama ve CEF iletilerini Azure Sentinel 'e iletme:**

    - Log Analytics Agent GitHub deposundan yapılandırmayı indirir.

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Syslog Daemon 'ı yapılandırma:**

    - Syslog yapılandırma dosyasını kullanarak TCP iletişimi için 514 bağlantı noktasını açar `/etc/rsyslog.conf` .

    - , Syslog Daemon dizinine özel bir yapılandırma dosyası ekleyerek CEF iletilerini TCP bağlantı noktası 25226 ' deki Log Analytics aracısına iletmek için arka plan programını yapılandırır `security-config-omsagent.conf` `/etc/rsyslog.d/` .

        `security-config-omsagent.conf`Dosyanın içeriği:

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **Syslog Daemon ve Log Analytics Aracısı yeniden başlatılıyor:**

    - Rsyslog arka plan programını yeniden başlatır.
    
        ```bash
        service rsyslog restart
        ```

    - Log Analytics aracısını yeniden başlatır.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. ***Bilgisayar* alanının beklenen şekilde eşlenmesi doğrulanıyor:**

    - Syslog kaynağındaki *bilgisayar* alanının, aşağıdaki komutu kullanarak Log Analytics aracısında düzgün şekilde eşlenmesini sağlar: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Eşlemeyle ilgili bir sorun varsa, komut dosyası **aşağıdaki komutu el ile çalıştırmanızı** yönlendiren bir hata iletisi üretir (yer tutucunun yerine çalışma alanı kimliğini uygulama). Komut doğru eşlemeyi güvence altına alacak ve aracıyı yeniden başlatacaktır.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[Syslog-ng Daemon](#tab/syslogng)

1. **Log Analytics aracısını indirme ve yükleme:**

    - Log Analytics (OMS) Linux aracısının yükleme betiğini indirir.

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Log Analytics aracısını yükleme.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **25226 bağlantı noktasını dinlemek için Log Analytics aracı yapılandırmasını ayarlama ve CEF iletilerini Azure Sentinel 'e iletme:**

    - Log Analytics Agent GitHub deposundan yapılandırmayı indirir.

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Syslog Daemon 'ı yapılandırma:**

    - Syslog yapılandırma dosyasını kullanarak TCP iletişimi için 514 bağlantı noktasını açar `/etc/syslog-ng/syslog-ng.conf` .

    - , Syslog Daemon dizinine özel bir yapılandırma dosyası ekleyerek CEF iletilerini TCP bağlantı noktası 25226 ' deki Log Analytics aracısına iletmek için arka plan programını yapılandırır `security-config-omsagent.conf` `/etc/syslog-ng/conf.d/` .

        `security-config-omsagent.conf`Dosyanın içeriği:

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Syslog Daemon ve Log Analytics Aracısı yeniden başlatılıyor:**

    - Syslog-ng cinini yeniden başlatır.
    
        ```bash
        service syslog-ng restart
        ```

    - Log Analytics aracısını yeniden başlatır.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. ***Bilgisayar* alanının beklenen şekilde eşlenmesi doğrulanıyor:**

    - Syslog kaynağındaki *bilgisayar* alanının, aşağıdaki komutu kullanarak Log Analytics aracısında düzgün şekilde eşlenmesini sağlar: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Eşlemeyle ilgili bir sorun varsa, komut dosyası **aşağıdaki komutu el ile çalıştırmanızı** yönlendiren bir hata iletisi üretir (yer tutucunun yerine çalışma alanı kimliğini uygulama). Komut doğru eşlemeyi güvence altına alacak ve aracıyı yeniden başlatacaktır.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```
---

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede, CEF gereçlerini Azure Sentinel 'e bağlamak için Log Analytics aracısının nasıl dağıtılacağını öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [CEF ve CommonSecurityLog alan eşlemesi](cef-name-mapping.md)hakkında bilgi edinin.
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](./tutorial-detect-threats-built-in.md)başlayın.
