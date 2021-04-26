---
title: Azure Sentinel 'e bağlantıyı doğrulama | Microsoft Docs
description: CEF iletilerinin Azure Sentinel 'e iletildiğinden emin olmak için güvenlik çözümünüzün bağlantısını doğrulayın.
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
ms.openlocfilehash: 3ce83de7f876bbd67120bf511d29860b71cd2227
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771286"
---
# <a name="step-3-validate-connectivity"></a>3. Adım: bağlantıyı doğrulama

Günlük ileticinizi dağıttıktan sonra (adım 1 ' de) ve Güvenlik çözümünüzü BT CEF iletileri gönderecek şekilde yapılandırdıysanız (adım 2 ' de), güvenlik çözümünüz ile Azure Sentinel arasındaki bağlantıyı doğrulamak için bu yönergeleri izleyin. 

## <a name="prerequisites"></a>Önkoşullar

- Günlük ileticisi makinenizde yükseltilmiş izinleriniz (sudo) olmalıdır.

- Günlük ileticisi makinenizde **python 2,7** veya **3** ' ün yüklü olması gerekir.<br>
`python –version`Denetlemek için komutunu kullanın.

- Bu işlemin bir noktasında çalışma alanı KIMLIĞI ve çalışma alanı birincil anahtarına ihtiyacınız olabilir. Onları çalışma alanı kaynağında, **aracılar yönetimi** altında bulabilirsiniz.

## <a name="how-to-validate-connectivity"></a>Bağlantıyı doğrulama

1. Azure Sentinel gezinti menüsünde **Günlükler**' i açın. Güvenlik çözümünüzdeki günlükleri alıp almadığınızı görmek için **Commonsecuritylog** şemasını kullanarak bir sorgu çalıştırın.<br>
Günlüklerinizin **Log Analytics** görünmeye başlaması için 20 dakika sürecağına dikkat edin. 

1. Sorgudan herhangi bir sonuç görmüyorsanız, olayların güvenlik çözümünden oluşturulduğunu doğrulayın veya bir süre oluşturmayı deneyin ve belirlediğiniz Syslog iletici makinesine iletildiğini doğrulayın. 

1. Güvenlik çözümünüz, günlük ileticisi ve Azure Sentinel arasındaki bağlantıyı denetlemek için günlük ileticisinde (yer tutucunun yerine çalışma alanı KIMLIĞINI uygulama) aşağıdaki betiği çalıştırın. Bu betik, arka plan programının doğru şekilde yapılandırıldığını ve arka plan programı ile Log Analytics Aracısı arasındaki iletişimi engellemediğini kontrol eder. Ayrıca, uçtan uca bağlantıyı denetlemek için ' TestCommonEventFormat ' adlı sahte iletiler gönderir. <br>

    ```bash
    sudo wget -O cef_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]
    ```

   - ***Bilgisayar* alanının eşlemesiyle** bir sorunu düzeltmek için bir komut çalıştırmanızı yönlendiren bir ileti alabilirsiniz. Ayrıntılar için [doğrulama betiğinin](#mapping-command) açıklamasına bakın.

    - **CISCO ASA güvenlik duvarı günlüklerinin ayrıştırılmasıyla** ilgili bir sorunu düzeltmek için bir komut çalıştırmanızı yönlendiren bir ileti alabilirsiniz. Ayrıntılar için [doğrulama betiğinin](#parsing-command) açıklamasına bakın.

## <a name="validation-script-explained"></a>Doğrulama betiği açıklanmıştı

Doğrulama betiği aşağıdaki denetimleri gerçekleştirir:

# <a name="rsyslog-daemon"></a>[rsyslog Daemon](#tab/rsyslog)

1. Dosyanın<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    var ve geçerli.

1. Dosyanın aşağıdaki metni içerip içermediğini denetler:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Aşağıdaki komut kullanılarak Cisco ASA güvenlik duvarı olayları için ayrıştırma 'nin beklenen şekilde yapılandırılıp yapılandırılmadığını denetler: 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Ayrıştırma ile ilgili bir sorun varsa, komut dosyası **aşağıdaki komutu el ile çalıştırmanızı** yönlendiren bir hata iletisi üretir (yer tutucunun yerine çalışma alanı kimliğini uygulama). Komut doğru ayrıştırmanın ve aracının yeniden başlatılmasına emin olur.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Syslog kaynağındaki *bilgisayar* alanının, aşağıdaki komutu kullanarak Log Analytics aracısında düzgün şekilde eşlendiğini denetler: 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Eşlemeyle ilgili bir sorun varsa, komut dosyası **aşağıdaki komutu el ile çalıştırmanızı** yönlendiren bir hata iletisi üretir (yer tutucunun yerine çalışma alanı kimliğini uygulama). Komut doğru eşlemeyi güvence altına alacak ve aracıyı yeniden başlatacaktır.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Makinede ağ trafiğini (örneğin, bir ana bilgisayar güvenlik duvarı) engelliyor olabilecek herhangi bir güvenlik geliştirmesi olup olmadığını denetler.

1. Syslog Daemon (rsyslog), TCP bağlantı noktası 25226 ' deki Log Analytics aracısına (CEF olarak tanımlanır) ileti göndermek için düzgün şekilde yapılandırılıp yapılandırılmadığını denetler:

    - Yapılandırma dosyası: `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. Syslog Daemon 'u ve Log Analytics aracısını yeniden başlatır:

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Gerekli bağlantıların kurulu olduğunu denetler: veri almak için TCP 514, Syslog Daemon ve Log Analytics Aracısı arasındaki dahili iletişim için TCP 25226:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Syslog arka plan programının 514 numaralı bağlantı noktasında veri aldığını ve aracının 25226 numaralı bağlantı noktasında veri aldığını denetler:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. SAHTE verileri, localhost üzerinde 514 numaralı bağlantı noktasına gönderir. Bu veriler, aşağıdaki sorguyu çalıştırarak Azure Sentinel çalışma alanında observable olmalıdır:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[Syslog-ng Daemon](#tab/syslogng)

1. Dosyanın<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    var ve geçerli.

1. Dosyanın aşağıdaki metni içerip içermediğini denetler:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Aşağıdaki komut kullanılarak Cisco ASA güvenlik duvarı olayları için ayrıştırma 'nin beklenen şekilde yapılandırılıp yapılandırılmadığını denetler: 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>Ayrıştırma ile ilgili bir sorun varsa, komut dosyası **aşağıdaki komutu el ile çalıştırmanızı** yönlendiren bir hata iletisi üretir (yer tutucunun yerine çalışma alanı kimliğini uygulama). Komut doğru ayrıştırmanın ve aracının yeniden başlatılmasına emin olur.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Syslog kaynağındaki *bilgisayar* alanının, aşağıdaki komutu kullanarak Log Analytics aracısında düzgün şekilde eşlendiğini denetler: 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>Eşlemeyle ilgili bir sorun varsa, komut dosyası **aşağıdaki komutu el ile çalıştırmanızı** yönlendiren bir hata iletisi üretir (yer tutucunun yerine çalışma alanı kimliğini uygulama). Komut doğru eşlemeyi güvence altına alacak ve aracıyı yeniden başlatacaktır.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. Makinede ağ trafiğini (örneğin, bir ana bilgisayar güvenlik duvarı) engelliyor olabilecek herhangi bir güvenlik geliştirmesi olup olmadığını denetler.

1. Syslog Daemon (syslog-ng), TCP bağlantı noktası 25226 ' deki Log Analytics aracısına CEF (Regex kullanılarak) olarak tanımladığı iletileri göndermek için düzgün şekilde yapılandırıldığını denetler:

    - Yapılandırma dosyası: `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. Syslog Daemon 'u ve Log Analytics aracısını yeniden başlatır:

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Gerekli bağlantıların kurulu olduğunu denetler: veri almak için TCP 514, Syslog Daemon ve Log Analytics Aracısı arasındaki dahili iletişim için TCP 25226:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Syslog arka plan programının 514 numaralı bağlantı noktasında veri aldığını ve aracının 25226 numaralı bağlantı noktasında veri aldığını denetler:

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. SAHTE verileri, localhost üzerinde 514 numaralı bağlantı noktasına gönderir. Bu veriler, aşağıdaki sorguyu çalıştırarak Azure Sentinel çalışma alanında observable olmalıdır:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="next-steps"></a>Sonraki adımlar

Bu belgede CEF gereçlerini Azure Sentinel 'e bağlamayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [CEF ve CommonSecurityLog alan eşlemesi](cef-name-mapping.md)hakkında bilgi edinin.
- [Verilerinize nasıl görünürlük alabileceğinizi ve olası tehditleri](quickstart-get-visibility.md)öğrenin.
- [Azure Sentinel ile tehditleri algılamaya](./tutorial-detect-threats-built-in.md)başlayın.
- Verilerinizi izlemek için [çalışma kitaplarını kullanın](tutorial-monitor-your-data.md) .
