---
title: Azure Otomasyonu karma Runbook Worker sorunlarını giderme
description: Bu makalede, Azure Otomasyonu karma runbook çalışanları ile ortaya çıkan sorunları giderme ve çözme işlemleri açıklanır.
services: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 906e38b28015bd70cf1c97ba9323094d64a12c94
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830853"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>Karma Runbook Çalışanı ile ilgili sorunları giderme

Bu makalede, Azure Otomasyonu karma runbook çalışanları ile ilgili sorun giderme ve sorunları çözme hakkında bilgi sağlanır. Genel bilgi için bkz. [karma Runbook Worker genel bakış](../automation-hybrid-runbook-worker.md).

## <a name="general"></a>Genel

Karma Runbook Worker, çalışan, runbook işlerini alacak ve rapor durumunu kaydetmek için Azure Otomasyonu hesabınızla iletişim kurmak üzere bir aracıya bağımlıdır. Windows için bu aracı Windows için Log Analytics aracısıdır. Linux için bu, Linux için Log Analytics aracısıdır.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Senaryo: runbook yürütmesi başarısız oluyor

#### <a name="issue"></a>Sorun

Runbook yürütmesi başarısız olur ve aşağıdaki hata iletisini alırsınız:

`The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times.`

Runbook 'unuzu üç kez yürütmeyi denediğinde kısa süre sonra askıya alınır. Runbook 'un tamamlanmasını kesintiye uğratan durumlar vardır. İlgili hata iletisi herhangi bir ek bilgi içermeyebilir.

#### <a name="cause"></a>Nedeni

Olası nedenler şunlardır:

* Runbook 'lar yerel kaynaklarla kimlik doğrulaması yapamaz.
* Karma çalışanı bir ara sunucu veya güvenlik duvarının arkasında.
* Karma runbook çalışanını çalıştırmak için yapılandırılan bilgisayar, en düşük donanım gereksinimlerini karşılamıyor.

#### <a name="resolution"></a>Çözüm

443 numaralı bağlantı noktasında bilgisayarın **\* . Azure-Automation.net** 'e giden erişimi olduğunu doğrulayın.

Karma runbook çalışanı çalıştıran bilgisayarların, çalışan bu özelliği barındıracak şekilde yapılandırmadan önce en düşük donanım gereksinimlerini karşılaması gerekir. Runbook 'lar ve kullandıkları arka plan işlemi, sistemin aşırı kullanılmasına ve runbook iş gecikmeleri veya zaman aşımları oluşmasına neden olabilir.

Karma Runbook Worker özelliğinin çalıştırılacağı bilgisayarın en düşük donanım gereksinimlerini karşıladığını onaylayın. Varsa, karma Runbook Worker işlemlerinin ve pencerelerinin performansı arasındaki bağıntıyı öğrenmek için CPU ve bellek kullanımını izleyin. Herhangi bir bellek veya CPU baskısı, kaynakları yükseltme gereksinimini gösterebilir. Ayrıca, iş yükü beklentilerinin gerekli olduğunu belirtmesi durumunda en düşük gereksinimleri ve ölçeği destekleyen farklı bir bilgi işlem kaynağı da seçebilirsiniz.

Açıklamasıyla ilgili bir olay için **Microsoft-SMA** olay günlüğü ' ne bakın `Win32 Process Exited with code [4294967295]` . Bu hatanın nedeni, runbook 'larınızda kimlik doğrulamasını yapılandırmadığınıza veya karma Runbook Worker grubu için farklı çalıştır kimlik bilgilerini belirtmemiş olmanız olabilir. Runbook 'larınız için kimlik doğrulamasını doğru şekilde yapılandırdığınızdan emin olmak için [bir karma runbook çalışanındaki runbook 'Ların çalıştırıldığı](../automation-hrw-run-runbooks.md) runbook izinlerini gözden geçirin.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Senaryo: karma runbook çalışanında olay 15011

#### <a name="issue"></a>Sorun

Karma Runbook Worker, bir sorgu sonucunun geçerli olmadığını belirten Event 15011 alır. Çalışan, [SignalR sunucusuyla](/aspnet/core/signalr/introduction)bir bağlantı açmayı denediğinde aşağıdaki hata görüntülenir.

`[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
`

#### <a name="cause"></a>Nedeni

Karma Runbook Worker, örneğin Güncelleştirme Yönetimi için otomatik özellik dağıtımı için doğru şekilde yapılandırılmadı. Dağıtım, VM 'yi Log Analytics çalışma alanına bağlayan bir bölüm içerir. PowerShell betiği, abonelik içinde sağlanan ada sahip çalışma alanını arar. Bu durumda, Log Analytics çalışma alanı farklı bir abonelikte bulunur. Betik, çalışma alanını bulamıyor ve bir tane oluşturmaya çalışıyor, ancak ad zaten alınmış. Sonuç olarak, dağıtım başarısız olur.

#### <a name="resolution"></a>Çözüm

Bu sorunu çözmek için iki seçeneğiniz vardır:

* Başka bir abonelikteki Log Analytics çalışma alanını aramak için PowerShell betiğini değiştirin. Bu, gelecekte birçok karma Runbook Worker makinesi dağıtmayı planlıyorsanız kullanmak için iyi bir çözümtir.

* Çalışan makineyi bir Orchestrator korumalı alanında çalışacak şekilde el ile yapılandırın. Ardından, işlevleri test etmek için çalışan üzerinde Azure Otomasyonu hesabında oluşturulmuş bir runbook 'u çalıştırın.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Senaryo: Windows Azure VM 'Leri karma çalışanı grubundan otomatik olarak bırakılır

#### <a name="issue"></a>Sorun

Çalışan makine uzun süredir kapalıyken karma Runbook Worker veya VM 'lerini göremezsiniz.

#### <a name="cause"></a>Nedeni

Karma Runbook Worker makinesi, 30 günden daha uzun bir süre boyunca Azure Otomasyonu 'nu Pingdi. Sonuç olarak, Otomasyon karma Runbook Worker grubunu veya sistem çalışan grubunu temizlemiştir. 

#### <a name="resolution"></a>Çözüm

Çalışan makinesini başlatın ve ardından Azure Otomasyonu ile yeniden yükleyin. Runbook ortamının nasıl yükleneceğine ve Azure Otomasyonu 'na nasıl bağlanacağınız hakkında yönergeler için bkz. [Windows karma Runbook Worker dağıtımı](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Senaryo: karma runbook çalışanındaki sertifika deposunda sertifika bulunamadı

#### <a name="issue"></a>Sorun

Karma Runbook Worker üzerinde çalışan bir runbook şu hata iletisiyle başarısız oluyor:

`Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000`  
`At line:3 char:1`  
`+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...`  
`+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~`  
`    + CategoryInfo          : CloseError: (:) [Connect-AzAccount],ArgumentException`  
`    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand`

#### <a name="cause"></a>Nedeni

Bu hata, farklı çalıştır hesabı sertifikasının bulunmadığı bir karma runbook çalışanında çalışan bir runbook 'ta [Farklı Çalıştır hesabını](../automation-security-overview.md#run-as-accounts) kullanmayı denediğinizde oluşur. Karma runbook çalışanları varsayılan olarak sertifika varlığını yerel olarak içermez. Farklı Çalıştır hesabı, bu varlığın düzgün şekilde çalışmasını gerektirir.

#### <a name="resolution"></a>Çözüm

Karma runbook çalışanınız bir Azure sanal makinesi ise bunun yerine [yönetilen kimliklerle runbook kimlik doğrulamasını](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities) kullanabilirsiniz. Bu senaryo, farklı çalıştır hesabı yerine Azure VM 'nin yönetilen kimliğini kullanarak Azure kaynaklarında kimlik doğrulaması yapma olanağı sunarak kimlik doğrulamasını basitleştirir. Karma Runbook Worker şirket içi bir makinedir ve farklı çalıştır hesabı sertifikasını makineye yüklemeniz gerekir. Sertifikayı yüklemeyi öğrenmek için [bir karma runbook çalışanında runbook 'Ları çalıştırma](../automation-hrw-run-runbooks.md)içindeki PowerShell runbook **Export-Runascercertificateatetohybridworker** ' ı çalıştırma adımlarına bakın.

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Senaryo: karma Runbook Worker kaydı sırasında hata 403

#### <a name="issue"></a>Sorun

Çalışan ilk kayıt aşaması başarısız olur ve şu hatayı alırsınız (403):

`Forbidden: You don't have permission to access / on this server.`

#### <a name="cause"></a>Nedeni

Olası nedenler şunlardır:

* Aracının ayarlarında yanlış yazılmış bir çalışma alanı KIMLIĞI veya çalışma alanı anahtarı (birincil) var. 
* Karma Runbook Worker, bir hesap bağlama hatasına neden olan yapılandırmayı indiremez. Azure makinelerde Özellikler etkinleştirdiğinde, yalnızca bir Log Analytics çalışma alanı ve bir Otomasyon hesabı bağlamak için yalnızca belirli bölgeleri destekler. Bilgisayarda yanlış bir tarih veya saat kümesi de mümkündür. Süre, geçerli zamandan itibaren +/-15 dakika ise, özellik dağıtımı başarısız olur.

#### <a name="resolution"></a>Çözüm

##### <a name="mistyped-workspace-id-or-key"></a>Yanlış yazılmış çalışma alanı KIMLIĞI veya anahtarı
Aracının çalışma alanı KIMLIĞI veya çalışma alanı anahtarının yanlış yazılmış olup olmadığını doğrulamak için bkz. [bir çalışma alanı ekleme veya kaldırma-](../../azure-monitor/platform/agent-manage.md#windows-agent) Windows Aracısı için Windows Aracısı veya Linux Aracısı için [çalışma alanı-Linux Aracısı Ekleme veya kaldırma](../../azure-monitor/platform/agent-manage.md#linux-agent) . Azure portal tam dizeyi seçtiğinizden emin olun ve dikkatlice kopyalayıp yapıştırın.

##### <a name="configuration-not-downloaded"></a>Yapılandırma indirilmedi

Log Analytics çalışma alanınız ve otomasyon hesabınızın bağlı bir bölgede olması gerekir. Desteklenen bölgelerin listesi için bkz. [Azure Otomasyonu ve Log Analytics çalışma alanı eşlemeleri](../how-to/region-mappings.md).

Ayrıca, bilgisayarınızın tarih veya saat dilimini güncelleştirmeniz gerekebilir. Özel bir zaman aralığı seçerseniz, aralığın UTC 'de olduğundan emin olun, bu da yerel saat diliminizden farklı olabilir.

### <a name="scenario-set-azstorageblobcontent-fails-on-a-hybrid-runbook-worker"></a><a name="set-azstorageblobcontent-execution-fails"></a>Senaryo: karma Runbook Worker üzerinde Set-AzStorageBlobContent başarısız oluyor 

#### <a name="issue"></a>Sorun

Runbook yürütülmeye çalıştığında başarısız olur `Set-AzStorageBlobContent` ve aşağıdaki hata iletisini alırsınız:

`Set-AzStorageBlobContent : Failed to open file xxxxxxxxxxxxxxxx: Illegal characters in path`

#### <a name="cause"></a>Nedeni

 Bu hata, UNC yolları ekleyen çağrıların uzun dosya adı davranışının oluşmasına neden olur `[System.IO.Path]::GetFullPath()` .

#### <a name="resolution"></a>Çözüm

Geçici bir çözüm olarak, aşağıdaki içerikle adlı bir yapılandırma dosyası oluşturabilirsiniz `OrchestratorSandbox.exe.config` :

```azurecli
<configuration>
  <runtime>
    <AppContextSwitchOverrides value="Switch.System.IO.UseLegacyPathHandling=false" />
  </runtime>
</configuration>
```

Bu dosyayı yürütülebilir dosyayla aynı klasöre yerleştirin `OrchestratorSandbox.exe` . Örneğin,

`%ProgramFiles%\Microsoft Monitoring Agent\Agent\AzureAutomation\7.3.702.0\HybridAgent`

>[!Note]
> Aracıyı yükseltirseniz, bu yapılandırma dosyası silinir ve yeniden oluşturulması gerekir.

## <a name="linux"></a>Linux

Linux hibrit Runbook Worker, çalışan, runbook işlerini alacak ve durumu raporlamak üzere otomasyon hesabınızla iletişim kurmak için [Linux 'un Log Analytics aracısına](../../azure-monitor/agents/log-analytics-agent.md) bağımlıdır. Çalışan kaydı başarısız olursa, hatanın olası nedenleri aşağıda verilmiştir.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>Senaryo: Linux karma Runbook Worker, bir runbook imzalanırken parola istemi alır

#### <a name="issue"></a>Sorun

`sudo`Linux karma Runbook Worker için komutu çalıştırmak, parola için beklenmeyen bir istem alır.

#### <a name="cause"></a>Nedeni

Linux için Log Analytics aracısına ait **nxautomationuser** hesabı, **sudoers** dosyasında doğru şekilde yapılandırılmamış. Karma Runbook Worker 'ın, Linux Runbook Worker 'daki runbook 'ları imzalayabilmesi için hesap izinlerinin ve diğer verilerin uygun yapılandırmasına ihtiyacı vardır.

#### <a name="resolution"></a>Çözüm

* Karma runbook çalışanının makinede GnuPG (GPG) yürütülebilirine sahip olduğundan emin olun.

* **Sudoers** dosyasındaki **nxautomationuser** hesabının yapılandırmasını doğrulayın. Bkz. [runbook 'Ları karma Runbook Worker üzerinde çalıştırma](../automation-hrw-run-runbooks.md).

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Senaryo: Linux için Log Analytics Aracısı çalışmıyor

#### <a name="issue"></a>Sorun

Linux için Log Analytics Aracısı çalışmıyor.

#### <a name="cause"></a>Nedeni

Aracı çalışmıyorsa, Linux hibrit Runbook Worker 'ın Azure Otomasyonu ile iletişim kurmasını engeller. Aracı çeşitli nedenlerle çalışmıyor olabilir.

#### <a name="resolution"></a>Çözüm

 Komutu girerek aracının çalıştığını doğrulayın `ps -ef | grep python` . Aşağıdakine benzer bir çıktı görmeniz gerekir. Python, **nxautomation** kullanıcı hesabıyla işlenir. Azure Otomasyonu özelliği etkinleştirilmemişse, aşağıdaki işlemlerden hiçbiri çalışmıyor.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Aşağıdaki listede bir Linux karma Runbook Worker için başlatılan süreçler gösterilmektedir. Bunlar tamamen/var/seçenek/Microsoft/omsagent/State/automationworker/dizininde bulunur.

* **OMS. conf**: Worker Manager işlemi. Doğrudan DSC 'den başlatılır.
* **Worker. conf**: otomatik kaydedilmiş karma çalışan işlemi. Çalışan Yöneticisi tarafından başlatılır. Bu işlem, Güncelleştirme Yönetimi tarafından kullanılır ve Kullanıcı için saydamdır. Makinede Güncelleştirme Yönetimi etkinleştirilmemişse bu işlem yoktur.
* **diy/Worker. conf**: diy karma çalışan işlemi. Karma Runbook Worker 'daki Kullanıcı runbook 'larını yürütmek için DIY karma çalışan işlemi kullanılır. Bu, yalnızca farklı bir yapılandırma kullandığı anahtar ayrıntısıyla otomatik kaydedilmiş karma çalışan işleminden farklıdır. Bu işlem, Azure Otomasyonu devre dışıysa ve DIY Linux hibrit Worker kayıtlı değilse yoktur.

Aracı çalışmıyorsa, hizmeti başlatmak için şu komutu çalıştırın: `sudo /opt/microsoft/omsagent/bin/service_control restart` .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Senaryo: belirtilen sınıf yok

`The specified class does not exist..` **/Var/seçenek/Microsoft/omsconfig/omsconfig.log** dosyasında hata Iletisini görürseniz, Linux için Log Analytics aracısının güncelleştirilmesi gerekir. Aracıyı yeniden yüklemek için aşağıdaki komutu çalıştırın.

```Bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows hibrit Runbook Worker, çalışan, runbook işlerini alacak ve durumu raporlamak üzere otomasyon hesabınızla iletişim kurması için [Windows 'un Log Analytics aracısına](../../azure-monitor/agents/log-analytics-agent.md) bağımlıdır. Çalışan kaydı başarısız olursa, bu bölümde bazı olası nedenler bulunur.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Senaryo: Windows için Log Analytics Aracısı çalışmıyor

#### <a name="issue"></a>Sorun

`healthservice`Karma Runbook Worker makinesinde çalışmıyor.

#### <a name="cause"></a>Nedeni

Windows hizmeti için Log Analytics çalışmıyorsa, karma Runbook Worker Azure Otomasyonu ile iletişim kuramaz.

#### <a name="resolution"></a>Çözüm

PowerShell 'de aşağıdaki komutu girerek aracının çalıştığını doğrulayın: `Get-Service healthservice` . Hizmet durdurulmuşsa, hizmeti başlatmak için PowerShell 'de aşağıdaki komutu girin: `Start-Service healthservice` .

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>Senaryo: Operations Manager günlüğünde olay 4502

#### <a name="issue"></a>Sorun

**Uygulama ve hizmetler Logs\Operations Manager** olay günlüğünde, olay 4502 ve aşağıdaki açıklamayla içeren bir olay iletisi görürsünüz `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` :<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Nedeni

Bu sorunun nedeni, proxy 'niz veya ağ güvenlik duvarınız Microsoft Azure ile iletişimi engelliyor olabilir. 443 numaralı bağlantı noktasında bilgisayarın **\* . Azure-Automation.net** 'e giden erişimi olduğunu doğrulayın.

#### <a name="resolution"></a>Çözüm

Günlükler, C:\ProgramData\Microsoft\System Center\orchestrator\7.2\sma\sandboxeson konumundaki her karma çalışan üzerinde yerel olarak depolanır. **Uygulama ve hizmetler Logs\Microsoft-SMA\Operations** ve **uygulama ve hizmetler logs\operations Manager** olay günlüklerinde bir uyarı veya hata olayı olup olmadığını doğrulayabilirsiniz. Bu Günlükler, rolün Azure Otomasyonu 'nu etkinleştirmeyi etkileyen bir bağlantı veya diğer tür sorunu veya normal işlemlerde karşılaşılan bir sorunu gösterir. Log Analytics aracısıyla ilgili sorunları gidermeye yönelik ek yardım için bkz. [Log Analytics Windows Agent ile ilgili sorunları giderme](../../azure-monitor/agents/agent-windows-troubleshoot.md).

Hibrit çalışanlar, bulutta çalışan runbook işlerinin çıkış ve mesaj gönderme gibi runbook [çıkışlarını ve Iletileri](../automation-runbook-output-and-messages.md) Azure Otomasyonu 'na gönderir. Ayrıntılı ve Ilerleme akışlarını runbook 'lar için yaptığınız gibi etkinleştirebilirsiniz.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-microsoft-365-through-proxy"></a>Senaryo: Orchestrator.Sandbox.exe proxy üzerinden Microsoft 365 bağlanamaz

#### <a name="issue"></a>Sorun

Windows karma Runbook Worker üzerinde çalışan bir betik, Orchestrator korumalı alanında Microsoft 365 beklendiği gibi bağlanamaz. Betik, bağlantı için [Connect-MsolService](/powershell/module/msonline/connect-msolservice) ' i kullanıyor. 

Proxy 'yi ve atlama listesini ayarlamak için **Orchestrator.Sandbox.exe.config** ayarlarsanız, korumalı alan hala düzgün şekilde bağlanmaz. Aynı ara sunucu ve atlama listesi ayarlarına sahip bir **Powershell_ise.exe.config** dosyası, beklendiği gibi çalışıyor gibi çalışır. Service Management Automation (SMA) günlükleri ve PowerShell günlükleri ara sunucu ile ilgili herhangi bir bilgi sağlamaz.

#### <a name="cause"></a>Nedeni

Sunucu üzerindeki Active Directory Federasyon Hizmetleri (AD FS) (AD FS) bağlantısı proxy 'yi atlayamaz. Bir PowerShell korumalı alanının, oturum açmış kullanıcı olarak çalıştığını unutmayın. Ancak, bir Orchestrator korumalı alanı yoğun bir şekilde özelleştirilir ve **Orchestrator.Sandbox.exe.config** dosya ayarlarını yok sayabilir. Makine veya Log Analytics Aracısı proxy ayarlarını işlemek için özel koda sahiptir, ancak diğer özel proxy ayarlarını işlemeye yönelik değildir. 

#### <a name="resolution"></a>Çözüm

Komut dosyanızı PowerShell cmdlet 'leri için MSOnline modülü yerine Azure Active Directory modüllerini kullanacak şekilde geçirerek Orchestrator korumalı alanı için sorunu çözebilirsiniz. Daha fazla bilgi için bkz. [Orchestrator 'Dan Azure Otomasyonu 'na (Beta) geçiş](../automation-orchestrator-migration.md).

MSOnline modülü cmdlet 'lerini kullanmaya devam etmek istiyorsanız, komut dosyanızı [Invoke-komutunu](/powershell/module/microsoft.powershell.core/invoke-command)kullanacak şekilde değiştirin. Ve parametreleri için değerleri `ComputerName` belirtin `Credential` . 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Bu kod değişikliği, belirtilen kimlik bilgileri bağlamında tamamen yeni bir PowerShell oturumu başlatır. Trafiğin, etkin kullanıcının kimliğini doğrulayan bir ara sunucu üzerinden akmasını sağlamalıdır.

>[!NOTE]
>Bu çözüm, korumalı alan yapılandırma dosyasını işlemeyi gereksiz hale getirir. Yapılandırma dosyasını betiğiyle birlikte çalışırken başarılı olsanız bile, karma Runbook Worker aracısının her güncelleştirildiği her seferinde dosya silinir.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Senaryo: karma Runbook Worker raporlama değil

#### <a name="issue"></a>Sorun

Karma Runbook Worker makineniz çalışıyor, ancak çalışma alanında makine için sinyal verisi görmüyor.

Aşağıdaki örnek sorguda, bir çalışma alanındaki makineler ve bunların son sinyali gösterilmektedir:

```kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Nedeni

Bu sorun, karma Runbook Worker 'daki bozuk bir önbellekten kaynaklanıyor olabilir.

#### <a name="resolution"></a>Çözüm

Bu sorunu çözmek için karma runbook çalışanında oturum açın ve aşağıdaki betiği çalıştırın. Bu betik, Windows için Log Analytics aracısını durduruyor, önbelleğini kaldırır ve hizmeti yeniden başlatır. Bu eylem, karma runbook çalışanını Azure Otomasyonu 'ndan yapılandırmasını yeniden indirmeye zorlar.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-windows-hybrid-runbook-worker"></a><a name="already-registered"></a>Senaryo: bir Windows karma Runbook Worker ekleyemezsiniz

#### <a name="issue"></a>Sorun

Cmdlet 'ini kullanarak bir karma Runbook Worker eklemeye çalıştığınızda aşağıdaki iletiyi alırsınız `Add-HybridRunbookWorker` :

`Machine is already registered`

#### <a name="cause"></a>Nedeni

Bu sorun, makinenin farklı bir Otomasyon hesabıyla zaten kayıtlı olması veya bir makineden kaldırıldıktan sonra karma Runbook Worker 'ı yeniden eklemeye çalışırsanız meydana gelir.

#### <a name="resolution"></a>Çözüm

Bu sorunu çözmek için, aşağıdaki kayıt defteri anahtarını kaldırın, yeniden başlatın `HealthService` ve `Add-HybridRunbookWorker` cmdlet 'i yeniden deneyin.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

### <a name="scenario-you-cant-add-a-linux-hybrid-runbook-worker"></a><a name="already-registered"></a>Senaryo: Linux karma Runbook Worker ekleyemezsiniz

#### <a name="issue"></a>Sorun

Python betiğini kullanarak bir karma Runbook Worker eklemeye çalıştığınızda şu iletiyi alırsınız `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` :

`Unable to register, an existing worker was found. Please deregister any existing worker and try again.`

Ayrıca, Python betiği kullanılarak karma Runbook Worker kaydı silinmeye çalışılıyor `sudo python /opt/microsoft/omsconfig/.../onboarding.py --deregister` :

`Failed to deregister worker. [response_status=404]`

#### <a name="cause"></a>Nedeni

Bu sorun, makine farklı bir Otomasyon hesabıyla kayıtlıysa, Azure hibrit çalışan grubu silinmişse veya karma runbook çalışanını bir makineden kaldırdıktan sonra yeniden eklemeye çalışırsanız meydana gelebilir.

#### <a name="resolution"></a>Çözüm

Bu sorunu çözmek için:

1. Aracıyı kaldırın `sudo sh onboard_agent.sh --purge` .

1. Şu komutları çalıştırın:

   ```
   sudo mv -f /home/nxautomation/state/worker.conf /home/nxautomation/state/worker.conf_old
   sudo mv -f /home/nxautomation/state/worker_diy.crt /home/nxautomation/state/worker_diy.crt_old
   sudo mv -f /home/nxautomation/state/worker_diy.key /home/nxautomation/state/worker_diy.key_old
   ```

1. Aracıyı yeniden ekleyin `sudo sh onboard_agent.sh -w <workspace id> -s <workspace key> -d opinsights.azure.com` .

1. Klasörün `/opt/microsoft/omsconfig/modules/nxOMSAutomationWorker` doldurulmasını bekleyin.

1. `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register`Python betiğini yeniden deneyin.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu burada görmüyorsanız veya sorununuzu çözemezseniz, ek destek için aşağıdaki kanallardan birini deneyin:

* Azure [forumları](https://azure.microsoft.com/support/forums/)aracılığıyla Azure uzmanlarından yanıtlar alın.
* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabıyla bağlanın. Azure desteği, Azure Community 'yi yanıtlar, destek ve uzmanlar için bağlar.
* Azure destek olayı dosyası oluşturma. [Azure destek sitesine](https://azure.microsoft.com/support/options/)gidin ve **Destek Al**' ı seçin.
