---
title: Azure Otomasyonu durum yapılandırması sorunlarını giderme
description: Bu makalede, Azure Otomasyonu durum yapılandırması sorunlarını nasıl giderebileceğiniz ve giderebileceğiniz açıklanır.
services: automation
ms.subservice: ''
ms.date: 04/16/2019
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 91c4780981851b62027fecf18da2c3b78625f272
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831213"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>Azure Otomasyonu durum yapılandırması sorunlarını giderme

Bu makalede, Azure Otomasyonu durum yapılandırması 'nda yapılandırmaları derlerken veya dağıtırken ortaya çıkan sorunları giderme ve çözme hakkında bilgi sağlanır. Durum yapılandırma özelliği hakkında genel bilgi için bkz. [Azure Otomasyonu durum yapılandırmasına genel bakış](../automation-dsc-overview.md).

## <a name="diagnose-an-issue"></a>Bir sorunu Tanıla

Yapılandırma için bir derleme veya dağıtım hatası aldığınızda, sorunu tanılamanıza yardımcı olacak birkaç adım aşağıda verilmiştir.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. yapılandırmanızın yerel makinede başarıyla derlendiğinden emin olun

Azure Otomasyonu durum yapılandırması, PowerShell Istenen durum yapılandırması (DSC) üzerine kurulmuştur. DSC dilinin ve sözdiziminin belgelerini [POWERSHELL DSC docs](/powershell/scripting/overview)' da bulabilirsiniz.

Yerel makinenizde bir DSC yapılandırması derleyerek, şu gibi yaygın hataları bulabilir ve çözebilirsiniz:

   - Eksik modüller.
   - Sözdizimi hataları.
   - Mantık hataları.

### <a name="2-view-dsc-logs-on-your-node"></a>2. düğümünüz için DSC günlüklerini görüntüleyin

Yapılandırmanız başarıyla derlenir, ancak bir düğüme uygulandığında başarısız olursa DSC günlüklerinde ayrıntılı bilgileri bulabilirsiniz. Bu günlüklerin nerede bulunacağı hakkında daha fazla bilgi için bkz. [WHERE DSC olay günlükleri](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

[Xdscdiagnostics](https://github.com/PowerShell/xDscDiagnostics) modülü DSC günlüklerinden ayrıntılı bilgileri Ayrıştırmada size yardımcı olabilir. Desteğe başvurursanız, bu günlüklere sorununuzu tanımaları gerekir.

`xDscDiagnostics` [Kalıcı sürüm modülünü Install](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)' daki yönergeleri izleyerek modülü yerel makinenize yükleyebilirsiniz.

`xDscDiagnostics`Modülü Azure makinenize yüklemek Için [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand)komutunu kullanın. [WINDOWS sanal makinenizde Çalıştır komutuyla PowerShell betikleri çalıştırma](../../virtual-machines/windows/run-command.md)bölümündeki adımları Izleyerek Azure Portal **Çalıştır komutunu** da kullanabilirsiniz.

**Xdscdiagnostics** kullanımı hakkında bilgi için bkz. [DSC günlüklerini çözümlemek Için xdscdiagnostics kullanma](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Ayrıca bkz. [Xdscdiagnostics cmdlet 'leri](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. düğümlerin ve Otomasyon çalışma alanının gerekli modüller içerdiğinden emin olun

DSC, düğümde yüklü olan modüllere bağlıdır. Azure Otomasyonu durum yapılandırması 'nı kullandığınızda, [Içeri aktarma modülleri](../shared-resources/modules.md#import-modules)' nde bulunan adımları izleyerek, gerekli tüm modülleri Otomasyon hesabınıza alın. Yapılandırmaların belirli modül sürümlerine de bağımlılığı olabilir. Daha fazla bilgi için bkz. [Modül sorunlarını giderme](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Senaryo: özel karakterler içeren bir yapılandırma portaldan silinemiyor

### <a name="issue"></a>Sorun

Portaldan bir DSC yapılandırmasını silmeye çalıştığınızda, aşağıdaki hatayı görürsünüz:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Nedeni

Bu hata, çözülmesi planlanan geçici bir sorundur.

### <a name="resolution"></a>Çözüm

Yapılandırmayı silmek için [Remove-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration) cmdlet 'ini kullanın.

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Senaryo: DSC Aracısı kaydedilemedi

### <a name="issue"></a>Sorun

[Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) veya başka bir DSC cmdlet 'i olduğunda şu hatayı alırsınız:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>Nedeni

Bu hata, normalde bir güvenlik duvarının, bir proxy sunucusunun arkasında olduğu makinenin veya diğer ağ hatalarının oluşmasına neden olur.

### <a name="resolution"></a>Çözüm

Makinenizin DSC için uygun uç noktalara erişimi olduğunu doğrulayın ve yeniden deneyin. Gereken bağlantı noktaları ve adreslerin listesi için bkz. [ağ planlaması](../automation-dsc-overview.md#network-planning).

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Senaryo: durum raporları yanıt kodunu yetkisiz döndürür

### <a name="issue"></a>Sorun

Azure Otomasyonu durum yapılandırması olan bir düğümü kaydettiğinizde aşağıdaki hata iletilerinden birini alırsınız:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Nedeni

Bu sorun, hatalı veya geçerliliği olumsuz bir sertifika nedeniyle oluşur. Bkz. [bir düğümü yeniden kaydetme](../automation-dsc-onboarding.md#re-register-a-node).

Bu sorun, ***. Azure-Automation.net** erişimine izin verilmeyen bir ara sunucu yapılandırması nedeniyle de oluşabilir. Daha fazla bilgi için bkz. [özel ağların yapılandırması](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Çözüm

Hatalı DSC düğümünü yeniden kaydetmek için aşağıdaki adımları kullanın.

#### <a name="step-1-unregister-the-node"></a>1. Adım: düğümün kaydını silme

1. Azure Portal, **ana**  >  **Otomasyon hesapları** > (Otomasyon hesabınız) > **Durum Yapılandırması (DSC)** bölümüne gidin.
1. **Düğümler**' i seçin ve sorun olan düğümü seçin.
1. Düğümün kaydını silmek için **kaydı kaldır** ' ı seçin.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>2. Adım: DSC uzantısını düğümden kaldırma

1. Azure Portal, **ana**  >  **sanal makine** > (başarısız düğüm) > **uzantılarına** gidin.
1. PowerShell DSC Uzantısı olan **Microsoft. PowerShell. DSC**' yi seçin.
1. Uzantıyı kaldırmak için **Kaldır** ' ı seçin.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>3. Adım: tüm hatalı veya vadesi geçen sertifikaları düğümden kaldırma

Yükseltilmiş bir PowerShell isteminde başarısız olan düğümde şu komutları çalıştırın:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

#### <a name="step-4-reregister-the-failing-node"></a>4. Adım: başarısız düğümü yeniden kaydetme

1. Azure Portal, **ana**  >  **Otomasyon hesapları** > (Otomasyon hesabınız) > **Durum Yapılandırması (DSC)** bölümüne gidin.
1. **Düğüm** seçin.
1. **Add (Ekle)** seçeneğini belirleyin.
1. Hatalı düğümü seçin.
1. **Bağlan**' ı seçin ve istediğiniz seçenekleri belirleyin.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Senaryo: düğüm, "bulunamadı" hatası ile başarısız durumda

### <a name="issue"></a>Sorun

Düğüm, başarısız durumuna sahip bir rapor içeriyor ve şu hatayı içeriyor:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Nedeni

Bu hata genellikle düğüm bir yapılandırma adına atandığında oluşur. Örneğin, bir düğüm yapılandırma (MOF dosyası) adı yerine **ABC**, örneğin, **ABC. Web sunucusu**.

### <a name="resolution"></a>Çözüm

* Düğümü yapılandırma adı değil düğüm yapılandırma adı ile atadığınızdan emin olun.
* Azure portal veya PowerShell cmdlet 'i kullanarak bir düğüme düğüm yapılandırması atayabilirsiniz.

  * Azure Portal, **ana**  >  **Otomasyon hesapları** > (Otomasyon hesabınız) > **Durum Yapılandırması (DSC)** bölümüne gidin. Ardından bir düğüm seçin ve **düğüm yapılandırması ata**' yı seçin.
  * [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) cmdlet 'ini kullanın.

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Senaryo: bir yapılandırma derlendiğinde hiçbir düğüm yapılandırması (MOF dosyası) üretilmedi

### <a name="issue"></a>Sorun

DSC derleme işiniz şu hatayla askıya alınır:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Nedeni

`Node`DSC yapılandırmasındaki anahtar sözcüğü izleyen ifade olarak değerlendirilirse `$null` , hiçbir düğüm yapılandırması üretilmez.

### <a name="resolution"></a>Çözüm

Sorunu gidermek için aşağıdaki çözümlerden birini kullanın:

* `Node`Yapılandırma tanımındaki anahtar sözcüğünün yanındaki Ifadenin null olarak değerlendirilmediğinden emin olun.
* Yapılandırmayı derlerken [configurationData](../automation-dsc-compile.md) geçiriyorsanız, yapılandırmanın yapılandırma verilerinden beklediği değerleri geçirdiğinizden emin olun.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Senaryo: DSC düğüm raporu sürüyor durumunda takılmış olur

### <a name="issue"></a>Sorun

DSC Aracısı çıkışları:

```error
No instance found with given property values
```

### <a name="cause"></a>Nedeni

Windows Management Framework (WMF) sürümünüzü yükselttiniz ve bozulmuş Windows Yönetim Araçları (WMI) var.

### <a name="resolution"></a>Çözüm

[DSC bilinen sorunları ve sınırlamaları](/powershell/scripting/wmf/known-issues/known-issues-dsc)bölümündeki yönergeleri izleyin.

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Senaryo: DSC yapılandırmasında kimlik bilgileri kullanılamıyor

### <a name="issue"></a>Sorun

DSC derleme işiniz şu hatayla askıya alındı:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Nedeni

Yapılandırmada bir kimlik bilgisi kullandınız, ancak `ConfigurationData` `PSDscAllowPlainTextPassword` her düğüm yapılandırması için doğru olarak ayarlanacak doğru olarak sağlamadık.

### <a name="resolution"></a>Çözüm

`ConfigurationData` `PSDscAllowPlainTextPassword` Yapılandırmada belirtilen her düğüm yapılandırması için doğru olarak ayarlanması için doğru şekilde geçiş yaptığınızdan emin olun. Bkz. [Azure Otomasyonu durum YAPıLANDıRMASıNDA DSC yapılandırmalarını derleme](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Senaryo: bir makineyi DSC uzantısından etkinleştirirken "uzantı işleme hatası" hatası

### <a name="issue"></a>Sorun

Bir DSC Uzantısı kullanarak bir makineyi etkinleştirdiğinizde, şu hatayı içeren bir hata oluşur:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Nedeni

Bu hata genellikle, düğüme hizmette mevcut olmayan bir düğüm yapılandırma adı atandığında oluşur.

### <a name="resolution"></a>Çözüm

* Düğümü, hizmette adıyla tam olarak eşleşen bir adla atadığınızdan emin olun.
* Düğüm yapılandırma adını eklemeyi tercih edebilirsiniz; Bu, düğümü etkinleştirmeye, ancak düğüm yapılandırması atamamasına neden olur.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Senaryo: bir düğümü PowerShell kullanarak kaydederken "bir veya daha fazla hata oluştu" hatası

### <a name="issue"></a>Sorun

[Register-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode) veya [register-AzureRMAutomationDSCNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode)kullanarak bir düğümü kaydettiğinizde, şu hatayı alırsınız:

```error
One or more errors occurred.
```

### <a name="cause"></a>Nedeni

Bu hata, Otomasyon hesabı tarafından kullanılan bir düğümü ayrı bir abonelikte kaydetmeye çalıştığınızda oluşur.

### <a name="resolution"></a>Çözüm

Abonelikler arası düğümü ayrı bir bulut veya şirket içi için tanımlanmış gibi değerlendirin. Makineleri etkinleştirmek için şu seçeneklerden birini kullanarak düğümü kaydedin:

* Windows: [Şirket içinde veya Azure/AWS dışındaki bir bulutta bulunan fiziksel/sanal Windows makineleri](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines).
* Linux: [Şirket içinde veya Azure dışındaki bir bulutta bulunan fiziksel/sanal Linux makineler](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines).

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Senaryo: "sağlama başarısız oldu" hata iletisi

### <a name="issue"></a>Sorun

Bir düğümü kaydettiğinizde, şu hatayı görürsünüz:

```error
Provisioning has failed
```

### <a name="cause"></a>Nedeni

Bu ileti, düğüm ile Azure arasında bağlantı sorunu olduğunda oluşur.

### <a name="resolution"></a>Çözüm

Düğümünüz bir sanal özel ağ (VPN) içinde olup olmadığını veya Azure 'a bağlanan başka sorunlar olup olmadığını belirleme. Bkz. [özellik dağıtım sorunlarını giderme](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Senaryo: Linux 'ta bir yapılandırma uygulanırken genel hata ile başarısız oldu

### <a name="issue"></a>Sorun

Linux 'ta bir yapılandırma uyguladığınızda, şu hatayı içeren bir hata oluşur:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Nedeni

**/Tmp** konumu olarak ayarlandıysa `noexec` , geçerli DSC sürümü yapılandırmaların uygulanamadır.

### <a name="resolution"></a>Çözüm

`noexec`Seçeneği **/tmp** konumundan kaldırın.

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Senaryo: çakışan düğüm yapılandırma adları bozuk bir yayına neden olabilir

### <a name="issue"></a>Sorun

Birden çok düğüm yapılandırması oluşturmak için tek bir yapılandırma betiği kullandığınızda ve bazı düğüm yapılandırma adları diğer adlara ait alt kümeler olduğunda, derleme hizmeti yanlış yapılandırma atanmasına son verebilir. Bu sorun yalnızca, düğüm başına yapılandırma verileri olan yapılandırmalar oluşturmak için tek bir komut dosyası kullandığınızda ve yalnızca ad çakışması dizenin başlangıcında oluştuğunda oluşur. Cmdlet 'ler kullanılarak bir karma tablosu olarak geçirilen düğüm verilerine göre yapılandırmalar oluşturmak için kullanılan tek bir yapılandırma betiğiyle, düğüm verilerinde **Server** ve **1server** adlı sunucular de bulunur.

### <a name="cause"></a>Nedeni

Bu, derleme hizmeti ile ilgili bilinen bir sorundur.

### <a name="resolution"></a>Çözüm

En iyi geçici çözüm, yerel olarak veya bir CI/CD işlem hattının derlenmesi ve düğüm yapılandırması MOF dosyalarını doğrudan hizmete yüklemeniz. Hizmette derleme bir gereksinimle karşılaşırsanız, bir sonraki en iyi geçici çözüm, derlemede çakışma olmaması için derleme işlerinin bölünmesi olacaktır.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Senaryo: DSC yapılandırması karşıya yüklemesi sırasında ağ geçidi zaman aşımı hatası

#### <a name="issue"></a>Sorun

`GatewayTimeout`DSC yapılandırmasını karşıya yüklerken bir hata alırsınız. 

### <a name="cause"></a>Nedeni

Derlenmesi uzun süren DSC yapılandırmalarının bu hataya neden olması olabilir.

### <a name="resolution"></a>Çözüm

`ModuleName`Tüm [Import-dscresource](/powershell/scripting/dsc/configurations/import-dscresource) çağrılarının PARAMETRESINI açıkça ekleyerek DSC yapılandırlarınızın daha hızlı ayrıştırmasını sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu burada görmüyorsanız veya sorununuzu çözemezseniz, ek destek için aşağıdaki kanallardan birini deneyin:

* Azure [forumları](https://azure.microsoft.com/support/forums/)aracılığıyla Azure uzmanlarından yanıtlar alın.
* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabıyla bağlanın. Azure desteği, Azure Community 'yi yanıtlar, destek ve uzmanlar için bağlar.
* Azure destek olayı dosyası oluşturma. [Azure destek sitesine](https://azure.microsoft.com/support/options/)gidin ve **Destek Al**' ı seçin.
