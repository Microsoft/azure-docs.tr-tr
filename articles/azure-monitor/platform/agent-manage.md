---
title: Azure Log Analytics aracısını yönetme | Microsoft Docs
description: Bu makalede, bir makineye dağıtılan Log Analytics Windows veya Linux aracısının yaşam döngüsü boyunca genellikle gerçekleştirdiğiniz farklı yönetim görevleri açıklanır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 06/14/2019
ms.openlocfilehash: 8dec91a3987aed978bb088d1aeab48a6fd0f9fb4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932795"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Windows ve Linux için Log Analytics aracısını yönetme ve sürdürme

Azure Izleyici 'de Log Analytics Windows veya Linux aracısının ilk dağıtımından sonra, aracıyı yeniden yapılandırmanız, yükseltmeniz veya yaşam döngüsünün kullanımdan kaldırılması aşamasına ulaştıysa bilgisayardan kaldırmanız gerekebilir. Bu rutin bakım görevlerini el ile veya Otomasyon aracılığıyla kolayca yönetebilirsiniz, bu da hem işlemsel hata hem de giderleri azaltır.

## <a name="upgrading-agent"></a>Aracı yükseltiliyor

Windows ve Linux için Log Analytics Aracısı en son sürüme el ile veya VM 'nin çalıştığı dağıtım senaryosuna ve ortamına bağlı olarak otomatik olarak yükseltilebilir. Aşağıdaki yöntemler aracıyı yükseltmek için kullanılabilir.

| Ortam | Yükleme yöntemi | Upgrade Yöntemi |
|--------|----------|-------------|
| Azure VM | Windows/Linux için Log Analytics Aracısı VM Uzantısı | *Otomatik* olarak, Azure Resource Manager şablonunuzu otomatik olarak varsayılan olarak yükseltilir. bu özelliği otomatik yükseltme özelliğini **yanlış**olarak ayarlayarak geri çevirebilirsiniz. |
| Özel Azure VM görüntüleri | Windows/Linux için Log Analytics aracısının el ile yüklenmesi | VM 'Lerin aracının en yeni sürümüne güncelleştirilmesi için Windows Installer paketi veya Linux kendiliğinden ayıklanan ve yüklenebilir kabuk betik paketi çalıştıran komut satırından gerçekleştirilmesi gerekir.|
| Azure dışı VM 'Ler | Windows/Linux için Log Analytics aracısının el ile yüklenmesi | VM 'Lerin aracının en yeni sürümüne güncelleştirilmesi için Windows Installer paketi veya Linux kendiliğinden ayıklanan ve yüklenebilir kabuk betik paketi çalıştıran komut satırından gerçekleştirilmesi gerekir. |

### <a name="upgrade-windows-agent"></a>Windows aracısını yükselt 

Bir Windows sanal makinesi üzerindeki aracıyı Log Analytics VM uzantısı kullanılarak yüklenmeyen en son sürüme güncelleştirmek için, komut Istemi, komut dosyası veya diğer otomasyon çözümünden veya MMASetup-\<platform\>. msi kurulum sihirbazını kullanarak komutunu çalıştırın.  

Aşağıdaki adımları gerçekleştirerek Log Analytics çalışma alanınızdan Windows aracısının en son sürümünü indirebilirsiniz.

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.

2. Azure portalında **Tüm hizmetler**’e tıklayın. Kaynak listesinde **Log Analytics** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Log Analytics çalışma alanlarını**seçin.

3. Log Analytics çalışma alanları listenizde, çalışma alanını seçin.

4. Log Analytics çalışma alanınızda **Gelişmiş ayarlar**' ı ve ardından **bağlı kaynaklar**' ı ve son olarak **Windows Server**' ı seçin.

5. Windows **sunucuları** sayfasında, Windows işletim sisteminin işlemci mimarisine bağlı olarak indirmek Için uygun **Windows aracısını indir** sürümünü seçin.

>[!NOTE]
>Windows için Log Analytics aracısının yükseltilmesi sırasında, raporlama yapılacak bir çalışma alanını yapılandırmayı veya yeniden yapılandırmayı desteklemez. Aracıyı yapılandırmak için, [bir çalışma alanı ekleme veya kaldırma](#adding-or-removing-a-workspace)altında listelenen desteklenen yöntemlerden birini izlemeniz gerekir.
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Kurulum Sihirbazı 'Nı kullanarak yükseltmek için

1. Yönetici haklarına sahip bir hesapla bilgisayarda oturum açın.

2. Kurulum sihirbazını başlatmak için **MMASetup-\<platform\>. exe** ' yi yürütün.

3. Kurulum sihirbazının ilk sayfasında, **İleri**' ye tıklayın.

4. **Microsoft Monitoring Agent kurulum** iletişim kutusunda lisans sözleşmesini kabul etmek için **kabul ediyorum ' a tıklayın.**

5. **Microsoft Monitoring Agent kurulum** Iletişim kutusunda **Yükselt**' e tıklayın. Durum sayfası yükseltmenin ilerlemesini görüntüler.

6. **Microsoft Monitoring Agent yapılandırması başarıyla tamamlandığında.** sayfası göründüğünde **son**' a tıklayın.

#### <a name="to-upgrade-from-the-command-line"></a>Komut satırından yükseltmek için

1. Yönetici haklarına sahip bir hesapla bilgisayarda oturum açın.

2. Aracı yükleme dosyalarını ayıklamak için, yükseltilmiş bir komut isteminden `MMASetup-<platform>.exe /c` ' ı çalıştırın ve dosyaları ayıklama yolunu sorar. Alternatif olarak, `MMASetup-<platform>.exe /c /t:<Full Path>` bağımsız değişkenlerini geçirerek yolu belirtebilirsiniz.

3. Şu komutu çalıştırın, burada D:\ , yükseltme günlük dosyasının konumudur.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Linux aracısını yükselt 

Önceki sürümlerden yükseltme (> 1.0.0-47) desteklenir. `--upgrade` komutuyla yüklemeyi gerçekleştirmek aracının tüm bileşenlerini en son sürüme yükseltir.

Aracıyı yükseltmek için aşağıdaki komutu çalıştırın.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Çalışma alanı ekleme veya kaldırma

### <a name="windows-agent"></a>Windows Aracısı
Bu bölümdeki adımlar, yalnızca Windows aracısını farklı bir çalışma alanına rapor verecek şekilde yeniden yapılandırmak ya da bir çalışma alanını yapılandırmadan kaldırmak istediğinizde gereklidir, ancak aracıyı birden fazla çalışma alanına raporlama için yapılandırmak istediğinizde (genellikle Çoklu barındırma olarak adlandırılır). Windows aracısının birden çok çalışma alanına rapor verecek şekilde yapılandırılması, yalnızca aracının ilk kurulumundan sonra ve aşağıda açıklanan yöntemler kullanılarak gerçekleştirilebilir.    

#### <a name="update-settings-from-control-panel"></a>Ayarları denetim masası 'ndan Güncelleştir

1. Yönetici haklarına sahip bir hesapla bilgisayarda oturum açın.

2. **Denetim Masası**'nı açın.

3. **Microsoft Monitoring Agent** öğesini seçin ve ardından **Azure Log Analytics** sekmesine tıklayın.

4. Bir çalışma alanını kaldırdıysanız, seçin ve ardından **Kaldır**' a tıklayın. Aracının raporlamayı durdurmasını istediğiniz başka bir çalışma alanı için bu adımı tekrarlayın.

5. Çalışma alanı eklenirse, **Ekle** ' ye tıklayın ve **Log Analytics çalışma alanı Ekle** iletişim kutusunda, çalışma alanı kimliğini ve çalışma alanı anahtarını (birincil anahtar) yapıştırın. Bilgisayarın Azure Kamu bulutundaki bir Log Analytics çalışma alanına rapor etmesi gerekiyorsa Azure Cloud açılır listesinden Azure ABD kamu ' u seçin.

6. Değişikliklerinizi kaydetmek için **Tamam**’a tıklayın.

#### <a name="remove-a-workspace-using-powershell"></a>PowerShell kullanarak çalışma alanını kaldırma

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>PowerShell kullanarak Azure Commercial 'e çalışma alanı ekleme

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>PowerShell kullanarak ABD kamu için Azure 'da bir çalışma alanı ekleme

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Aracıyı yüklemek veya yapılandırmak için daha önce komut satırını veya betiği kullandıysanız `EnableAzureOperationalInsights` `AddCloudWorkspace` ve `RemoveCloudWorkspace`ile değiştirilmiştir.
>

### <a name="linux-agent"></a>Linux Aracısı
Aşağıdaki adımlarda, farklı bir çalışma alanına kaydolmaya veya bir çalışma alanını yapılandırmadan kaldırmaya karar verirseniz Linux aracısının nasıl yeniden yapılandırılacağı gösterilmektedir.

1. Bir çalışma alanına kayıtlı olduğunu doğrulamak için şu komutu çalıştırın:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Aşağıdaki örneğe benzer bir durum döndürmelidir:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Durumun aynı zamanda aracının çalıştığını da gösterdiği, aksi takdirde aracıyı yeniden yapılandırmak için aşağıdaki adımlar başarıyla tamamlanmayacak.

2. Zaten bir çalışma alanıyla kaydedilmişse, aşağıdaki komutu çalıştırarak kayıtlı çalışma alanını kaldırın. Aksi takdirde, bir sonraki adımla devam edin.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Farklı bir çalışma alanıyla kaydolmak için aşağıdaki komutu çalıştırın:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Değişikliklerinizin geçerli olduğunu doğrulamak için şu komutu çalıştırın:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Aşağıdaki örneğe benzer bir durum döndürmelidir:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Değişikliklerin etkili olabilmesi için Aracı hizmetinin yeniden başlatılması gerekmez.

## <a name="update-proxy-settings"></a>Ara sunucu ayarlarını Güncelleştir
Aracıyı, dağıtımdan sonra bir ara sunucu veya [ağ geçidi Log Analytics](gateway.md) aracılığıyla hizmetle iletişim kuracak şekilde yapılandırmak için, bu görevi gerçekleştirmek için aşağıdaki yöntemlerden birini kullanın.

### <a name="windows-agent"></a>Windows Aracısı

#### <a name="update-settings-using-control-panel"></a>Denetim Masası 'nı kullanarak ayarları güncelleştirme

1. Yönetici haklarına sahip bir hesapla bilgisayarda oturum açın.

2. **Denetim Masası**'nı açın.

3. **Microsoft Monitoring Agent** öğesini seçin ve ardından **proxy ayarları** sekmesine tıklayın.

4. **Ara sunucu kullan**'a tıklayıp ara sunucunun veya ağ geçidinin URL'sini ve bağlantı noktası numarasını girin. Ara sunucunuz veya Log Analytics ağ geçidiniz kimlik doğrulaması gerektiriyorsa, kimlik doğrulaması yapmak için kullanıcı adını ve parolayı yazın, ardından **Tamam**'a tıklayın.

#### <a name="update-settings-using-powershell"></a>PowerShell kullanarak ayarları güncelleştirme

Aşağıdaki örnek PowerShell kodunu kopyalayın, ortamınıza özgü bilgilerle güncelleştirin ve bir PS1 dosya adı uzantısıyla kaydedin. Betiği Azure Izleyici 'de Log Analytics çalışma alanına doğrudan bağlanan her bilgisayarda çalıştırın.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

### <a name="linux-agent"></a>Linux Aracısı
Linux bilgisayarlarınızın bir ara sunucu veya Log Analytics ağ geçidi üzerinden iletişim kurması gerekiyorsa aşağıdaki adımları gerçekleştirin. Proxy yapılandırması değeri `[protocol://][user:password@]proxyhost[:port]` sözdizimine sahiptir. *proxyhost* özelliği, ara sunucunun tam etki adı alanı veya IP adresini kabul eder.

1. Aşağıdaki komutları çalıştırıp değerleri kendi ayarlarınıza göre değiştirerek `/etc/opt/microsoft/omsagent/proxy.conf` dosyasını düzenleyin.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
    ```

2. Aşağıdaki komutu çalıştırarak aracıyı yeniden başlatın:

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ```

## <a name="uninstall-agent"></a>Aracıyı kaldır
Komut satırı veya Kurulum Sihirbazı 'nı kullanarak Windows veya Linux Aracısı 'nı kaldırmak için aşağıdaki yordamlardan birini kullanın.

### <a name="windows-agent"></a>Windows Aracısı

#### <a name="uninstall-from-control-panel"></a>Denetim masasından kaldır
1. Yönetici haklarına sahip bir hesapla bilgisayarda oturum açın.

2. **Denetim Masası**'nda **Programlar ve Özellikler**' e tıklayın.

3. **Programlar ve Özellikler**' de **Microsoft Monitoring Agent**' a, **Kaldır**' a ve ardından **Evet**' e tıklayın.

>[!NOTE]
>Aracı Kurulum Sihirbazı ayrıca, Azure portal bir çalışma alanından indirileceği **MMASetup-\<platform\>. exe**' ye çift tıklayarak da çalıştırılabilir.

#### <a name="uninstall-from-the-command-line"></a>Komut satırından kaldır
Aracının indirilen dosyası, IExpress ile oluşturulan, kendi içinde bulunan bir yükleme paketidir. Aracının ve destekleyici dosyaların kurulum programı pakette bulunur ve aşağıdaki örnekte gösterilen komut satırı kullanılarak düzgün bir şekilde kaldırılması için ayıklanmalıdır.

1. Yönetici haklarına sahip bir hesapla bilgisayarda oturum açın.

2. Aracı yükleme dosyalarını ayıklamak için, yükseltilmiş bir komut isteminden `extract MMASetup-<platform>.exe` çalıştırın ve dosyaları ayıklama yolunu sorar. Alternatif olarak, `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`bağımsız değişkenleri geçirerek de yolu belirtebilirsiniz. IExpress tarafından desteklenen komut satırı anahtarları hakkında daha fazla bilgi için bkz. [IExpress Için komut satırı anahtarları](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) ve bu örneği gereksinimlerinize uyacak şekilde güncelleştirin.

3. İsteminde `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`yazın.

### <a name="linux-agent"></a>Linux Aracısı
Aracıyı kaldırmak için Linux bilgisayarında aşağıdaki komutu çalıştırın. *--temizleme* bağımsız değişkeni, aracıyı ve yapılandırmasını tamamen kaldırır.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Aracıyı bir Operations Manager yönetim grubuna bildirilecek şekilde yapılandırma

### <a name="windows-agent"></a>Windows Aracısı
Windows için Log Analytics aracısını bir System Center Operations Manager yönetim grubuna bildirilecek şekilde yapılandırmak için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Yönetici haklarına sahip bir hesapla bilgisayarda oturum açın.

2. **Denetim Masası**'nı açın.

3. **Microsoft Monitoring Agent** ' a ve ardından **Operations Manager** sekmesine tıklayın.

4. Operations Manager sunucularınızın Active Directory Tümleştirmesi varsa, **Yönetim grubu atamalarını AD DS otomatik olarak güncelleştir**' e tıklayın.

5. **Yönetim grubu Ekle** iletişim kutusunu açmak için **Ekle** ' ye tıklayın.

6. **Yönetim grubu adı** alanında, yönetim grubunuzun adını yazın.

7. **Birincil yönetim sunucusu** alanına birincil yönetim sunucusunun bilgisayar adını yazın.

8. **Yönetim sunucusu bağlantı noktası** ALANıNA, TCP bağlantı noktası numarasını yazın.

9. **Aracı eylem hesabı**altında, yerel sistem hesabını veya bir yerel etki alanı hesabını seçin.

10. **Tamam** ' **a tıklayarak yönetim grubu Ekle** iletişim kutusunu kapatın ve ardından **Tamam** ' a tıklayarak **Microsoft Monitoring Agent Özellikler** iletişim kutusunu kapatın.

### <a name="linux-agent"></a>Linux Aracısı
Linux için Log Analytics aracısını bir System Center Operations Manager yönetim grubuna bildirilecek şekilde yapılandırmak için aşağıdaki adımları gerçekleştirin.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Dosyayı düzenleme `/etc/opt/omi/conf/omiserver.conf`

2. `httpsport=` ile başlayan satırın 1270 numaralı bağlantı noktasını tanımladığından emin olun. Örneğin: `httpsport=1270`

3. OMı sunucusunu yeniden başlatın: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Sonraki adımlar

- Linux aracısını yüklerken veya yönetirken sorunlarla karşılaşırsanız [, Linux aracısının sorunlarını giderme](agent-linux-troubleshoot.md) konusunu gözden geçirin.

- Windows Agent 'ı yüklerken veya yönetirken sorunlarla karşılaşırsanız [Windows Agent sorunlarını giderme](agent-windows-troubleshoot.md) konusunu gözden geçirin.
