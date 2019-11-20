---
title: Sorun giderme-Azure Otomasyonu karma runbook çalışanları
description: Bu makalede, Azure Otomasyonu karma runbook çalışanları sorunlarını giderme hakkında bilgi sağlanır
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 02/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 39cf6126f6212b6e83f1974dae7aaab0038e69c6
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240993"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Karma runbook çalışanları sorunlarını giderme

Bu makale, karma runbook çalışanları ile ilgili sorun giderme hakkında bilgi sağlar.

## <a name="general"></a>Genel

Karma Runbook Worker, çalışan, runbook işlerini alacak ve durumu raporlamak üzere otomasyon hesabınızla iletişim kurmak için bir aracıya bağımlıdır. Windows için bu aracı Microsoft Monitoring Agent. Linux için Linux için OMS Aracısı.

### <a name="runbook-execution-fails"></a>Senaryon Runbook yürütmesi başarısız oluyor

#### <a name="issue"></a>Sorun

Runbook yürütmesi başarısız olur ve şu hatayı alırsınız:

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Runbook 'unuzu üç kez yürütmeyi denediğinde kısa süre sonra askıya alınır. Runbook 'un tamamlanmasını kesintiye uğratan durumlar vardır. Bu durumda, ilgili hata iletisi size nedenini bildiren herhangi bir ek bilgi içermeyebilir.

#### <a name="cause"></a>Nedeni

Olası olası nedenler şunlardır:

* Runbook 'lar yerel kaynaklarla kimlik doğrulayamıyorum

* Karma çalışanı bir ara sunucu veya güvenlik duvarının arkasında

* Runbook 'lar yerel kaynaklarla kimlik doğrulayamıyorum

* Karma Runbook Worker özelliğini çalıştıracak şekilde yapılandırılan bilgisayar, en düşük donanım gereksinimlerini karşılamıyor.

#### <a name="resolution"></a>Çözüm

443 numaralı bağlantı noktasında, bilgisayarın *. azure-automation.net 'ye giden erişimi olduğunu doğrulayın.

Karma runbook çalışanı çalıştıran bilgisayarlar, bu özelliği barındırmak üzere yapılandırılmadan önce en düşük donanım gereksinimlerini karşılamalıdır. Runbook 'lar ve kullandıkları arka plan işlemi sistemin aşırı kullanılmasına ve runbook iş gecikmelerine veya zaman aşımına neden olmasına neden olabilir.

Karma Runbook Worker özelliğinin çalışacağı bilgisayarın en düşük donanım gereksinimlerini karşıladığını onaylayın. Varsa, karma Runbook Worker işlemlerinin ve pencerelerinin performansı arasındaki bağıntıyı öğrenmek için CPU ve bellek kullanımını izleyin. Bellek veya CPU baskısı varsa, bu durum kaynakları yükseltme gereksinimini gösterebilir. Ayrıca, iş yükü beklentilerinin bir artış belirtmesi durumunda en düşük gereksinimleri destekleyebilen ve ölçeklendirilen farklı bir işlem kaynağı seçebilirsiniz.

# *[4294967295] kodlu Win32 Işlemine çıkıldı*açıklaması ile ilgili bir olay için **Microsoft-SMA** olay günlüğü 'nü denetleyin. Bu hatanın nedeni, runbook 'larınızda kimlik doğrulaması yapılandırmadınız veya karma çalışan grubu için farklı çalıştır kimlik bilgilerini belirtmezsiniz. Runbook 'larınız için kimlik doğrulamasını doğru şekilde yapılandırdığınızdan emin olmak için [runbook izinlerini](../automation-hrw-run-runbooks.md#runbook-permissions) gözden geçirin.

### <a name="no-cert-found"></a>Senaryon Karma Runbook Worker 'daki sertifika deposunda sertifika bulunamadı

#### <a name="issue"></a>Sorun

Karma Runbook Worker üzerinde çalışan bir runbook şu hata iletisiyle başarısız oluyor:

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Nedeni

Bu hata, farklı çalıştır hesabı sertifikasının bulunmadığı bir karma runbook çalışanında çalışan bir runbook 'ta [Farklı Çalıştır hesabını](../manage-runas-account.md) kullanmaya çalıştığınızda oluşur. Karma runbook çalışanları varsayılan olarak, farklı çalıştır hesabının düzgün çalışması için gerekli olan sertifika varlığını yerel olarak içermez.

#### <a name="resolution"></a>Çözüm

Karma runbook çalışanınız bir Azure sanal makinesi ise bunun yerine [Azure kaynakları Için Yönetilen kimlikler](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) kullanabilirsiniz. Bu senaryo, kimlik doğrulamasını basitleştirecek farklı çalıştır hesabı yerine Azure VM 'nin yönetilen kimliğini kullanarak Azure kaynaklarında kimlik doğrulaması yapmanıza olanak sağlar. Karma Runbook Worker şirket içi bir makinedir ve farklı çalıştır hesabı sertifikasını makineye yüklemeniz gerekir. Sertifikayı yüklemeyi öğrenmek için, [Export-Runascercertificate Meditohybridworker](../automation-hrw-run-runbooks.md#runas-script) runbook 'unu çalıştırma adımlarına bakın.

## <a name="linux"></a>Linux

Linux hibrit Runbook Worker, çalışan, runbook işlerini alacak ve durumu raporlamak üzere otomasyon hesabınızla iletişim kurmak için Linux için OMS Aracısı bağımlıdır. Çalışan kaydı başarısız olursa, hatanın olası nedenleri aşağıda verilmiştir:

### <a name="oms-agent-not-running"></a>Senaryon Linux için OMS Aracısı çalışmıyor

#### <a name="issue"></a>Sorun

Linux için OMS Aracısı çalışmıyor

#### <a name="cause"></a>Nedeni

Linux için OMS Aracısı çalışmıyorsa, Linux hibrit Runbook Worker 'ın Azure Otomasyonu ile iletişim kurmasını engeller. Aracı çeşitli nedenlerle çalışmıyor olabilir.

#### <a name="resolution"></a>Çözüm

 Aşağıdaki komutu girerek aracının çalıştığını doğrulayın: `ps -ef | grep python`. Aşağıdakine benzer bir çıktı görmeniz gerekir, Python, **nxautomation** Kullanıcı hesabı ile işlenir. Güncelleştirme Yönetimi veya Azure Otomasyonu çözümleri etkinleştirilmemişse, aşağıdaki işlemlerden hiçbiri çalışmıyor.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Aşağıdaki listede bir Linux karma Runbook Worker için başlatılan süreçler gösterilmektedir. Bunlar `/var/opt/microsoft/omsagent/state/automationworker/` dizinde bulunur.


* **OMS. conf** -bu değer çalışan Yöneticisi işlemidir. Doğrudan DSC 'den başlatılır.

* **Worker. conf** -bu Işlem, otomatik kayıtlı karma çalışan sürecdir, çalışan Yöneticisi tarafından başlatılır. Bu işlem, Güncelleştirme Yönetimi tarafından kullanılır ve Kullanıcı için saydamdır. Güncelleştirme Yönetimi çözümü makinede etkinleştirilmemişse bu işlem yoktur.

* **diy/Worker. conf** -bu Işlem, diy karma çalışan işlemidir. Karma Runbook Worker 'daki Kullanıcı runbook 'larını yürütmek için DIY karma çalışan işlemi kullanılır. Bu, yalnızca farklı bir yapılandırma kullanan anahtar ayrıntıdaki otomatik kayıtlı karma çalışan işleminden farklıdır. Bu işlem, Azure Otomasyonu çözümü devre dışı bırakılmışsa ve DIY Linux karma çalışanı kayıtlı değilse mevcut değildir.

Linux için OMS Aracısı çalışmıyorsa, hizmeti başlatmak için şu komutu çalıştırın: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Senaryon Belirtilen sınıf yok

Şu hatayı görürseniz: **Belirtilen sınıf yok..** `/var/opt/microsoft/omsconfig/omsconfig.log` bu durumda Linux için OMS Aracısı güncelleştirilmesi gerekir. OMS aracısını yeniden yüklemek için aşağıdaki komutu çalıştırın:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows hibrit Runbook Worker, çalışan, runbook işlerini alacak ve durumu raporlamak üzere otomasyon hesabınızla iletişim kurmak için Microsoft Monitoring Agent bağımlıdır. Çalışan kaydı başarısız olursa, hatanın olası nedenleri aşağıda verilmiştir:

### <a name="mma-not-running"></a>Senaryon Microsoft Monitoring Agent çalışmıyor

#### <a name="issue"></a>Sorun

`healthservice` Hizmet karma Runbook Worker makinesinde çalışmıyor.

#### <a name="cause"></a>Nedeni

Microsoft Monitoring Agent Windows hizmeti çalışmıyorsa, bu durum karma Runbook Worker 'ın Azure Otomasyonu ile iletişim kurmasını engeller.

#### <a name="resolution"></a>Çözüm

PowerShell 'de aşağıdaki komutu girerek aracının çalıştığını doğrulayın: `Get-Service healthservice`. Hizmet durdurulmuşsa, hizmeti başlatmak için PowerShell 'de aşağıdaki komutu girin: `Start-Service healthservice`.

### <a name="event-4502"></a>Operations Manager günlüğünde olay 4502

#### <a name="issue"></a>Sorun

**Uygulama ve hizmetler Logs\Operations Manager** olay günlüğünde, aşağıdaki açıklamayla **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. Hybridavgent** Içeren olay 4502 ve eventmessage ' u görürsünüz: *\<WSID\>. OMS.opinsights.Azure.com hizmeti tarafından sunulan sertifika, Microsoft Hizmetleri için kullanılan bir sertifika yetkilisi tarafından verilmemiş. Lütfen, TLS/SSL iletişimini engelleyen bir proxy çalıştırıp çalıştırmadığını görmek için ağ yöneticinize başvurun. KB3126513 makalesinde bağlantı sorunlarıyla ilgili ek sorun giderme bilgileri bulunur.*

#### <a name="cause"></a>Nedeni

Bu sorunun nedeni, proxy 'niz veya ağ güvenlik duvarınız Microsoft Azure ile iletişimi engelliyor olabilir. 443 bağlantı noktalarında, bilgisayarın *. azure-automation.net 'ye giden erişimi olduğunu doğrulayın.

#### <a name="resolution"></a>Çözüm

Günlükler, C:\ProgramData\Microsoft\System Center\orchestrator\7.2\sma\sandboxeson konumundaki her karma çalışan üzerinde yerel olarak depolanır. **Uygulama ve hizmetler Logs\Microsoft-SMA\Operations** ve **uygulama ve hizmetler logs\operations Manager** olay günlüğünde bir bağlantı veya diğer sorunu belirten bir uyarı veya hata olayı olup olmadığını kontrol edebilirsiniz. rol, normal işlemler altında Azure Otomasyonu veya sorunu için eklemeyi etkiler.

[Runbook çıkışı ve iletileri](../automation-runbook-output-and-messages.md) , yalnızca bulutta çalışan runbook işleri gibi karma çalışanların Azure Otomasyonu 'na gönderilir. Ayrıca ayrıntılı ve Ilerleme akışlarını diğer runbook 'larda yaptığınız şekilde de etkinleştirebilirsiniz.

### <a name="corrupt-cache"></a>Karma Runbook Worker raporlama değil

#### <a name="issue"></a>Sorun

Karma Runbook Worker makineniz çalışıyor ancak çalışma alanındaki makinenin sinyal verilerini görmüyor olabilirsiniz.

Aşağıdaki örnek sorguda, bir çalışma alanındaki makineler ve bunların son sinyali gösterilmektedir:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Nedeni

Bu sorun, karma Runbook Worker 'daki bozuk bir önbellekten kaynaklanıyor olabilir.

#### <a name="resolution"></a>Çözüm

Bu sorunu çözmek için karma runbook çalışanında oturum açın ve aşağıdaki betiği çalıştırın. Bu betik Microsoft Monitoring Agent sonlandırır, önbelleğini kaldırır ve hizmeti yeniden başlatır. Bu eylem, karma runbook çalışanını Azure Otomasyonu 'ndan yapılandırmasını yeniden indirmeye zorlar.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>Senaryon Karma Runbook Worker ekleyemezsiniz

#### <a name="issue"></a>Sorun

`Add-HybridRunbookWorker` Cmdlet 'ini kullanarak karma Runbook Worker eklemeye çalışırken aşağıdaki iletiyi alırsınız.

```error
Machine is already registered
```

#### <a name="cause"></a>Nedeni

Bu, makinenin farklı bir Otomasyon hesabıyla zaten kayıtlı olması veya bir makineden kaldırıldıktan sonra karma Runbook Worker 'ı yeniden eklemeye çalışırsanız meydana gelir.

#### <a name="resolution"></a>Çözüm

Bu sorunu çözmek için, aşağıdaki kayıt defteri anahtarını kaldırın ve yeniden başlatın `HealthService` ve `Add-HybridRunbookWorker` cmdlet 'i yeniden deneyin:

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* [Azure Forumları](https://azure.microsoft.com/support/forums/) aracılığıyla Azure uzmanlarından yanıtlar alın
* [@AzureSupport](https://twitter.com/azuresupport) hesabı ile bağlantı kurun. Bu resmi Microsoft Azure hesabı, müşteri deneyimini geliştirmek için Azure topluluğunu doğru kaynaklara ulaştırır: yanıtlar, destek ve uzmanlar.
* Daha fazla yardıma ihtiyacınız varsa, bir Azure destek olayı dosyası gönderebilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.

