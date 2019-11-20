---
title: Güncelleştirme Yönetimi hata giderme sorunları
description: Güncelleştirme Yönetimi sorunları nasıl giderebileceğinizi öğrenin.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 2aebcf05cbc818997943ed3bab19fb1fd8a83592
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786056"
---
# <a name="troubleshooting-issues-with-update-management"></a>Güncelleştirme Yönetimi sorunlarını giderme

Bu makalede, Güncelleştirme Yönetimi kullandığınızda karşılaşabileceğiniz sorunlara yönelik çözümler ele alınmaktadır.

Temeldeki sorunu belirlemede karma çalışan Aracısı için bir aracı sorun giderici vardır. Sorun giderici hakkında daha fazla bilgi için bkz. [Güncelleştirme Aracısı sorunlarını giderme](update-agent-issues.md). Diğer tüm sorunlar için aşağıdaki sorun giderme kılavuzunu kullanın.

Çözümü bir sanal makineye (VM) ekleme çalışırken sorunlarla karşılaşırsanız, olay KIMLIĞI 4502 ve olay ayrıntıları içeren olaylar için yerel makinedeki **uygulama ve hizmet günlükleri** altındaki **Operations Manager** günlüğünü kontrol edin. **Microsoft. EnterpriseManagement. HealthService. AzureAutomation. Hybridadgent**.

Aşağıdaki bölümde, belirli hata iletileri ve her biri için olası çözümler vurgulanmaktadır. Diğer ekleme sorunları için bkz. [çözüm ekleme sorunlarını giderme](onboarding.md).

## <a name="nologs"></a>Senaryo: makineler altında portalda gösterilmez Güncelleştirme Yönetimi

### <a name="issue"></a>Sorun

Aşağıdaki belirtilerle karşılaşırsınız:

* Makineniz, bir sanal makinenin Güncelleştirme Yönetimi görünümünden **yapılandırılmamış** .

* Azure Otomasyonu hesabınızın Güncelleştirme Yönetimi görünümünde makineleriniz eksik.

* **Uyumluluk**altında **değerlendirilmeyen** olarak gösteren makineleriniz var. Ancak, karma Runbook Worker için Azure Izleyici günlüklerinde sinyal verilerini görürsünüz, ancak Güncelleştirme Yönetimi için değildir.

### <a name="cause"></a>Nedeni

Bu soruna yerel yapılandırma sorunları veya yanlış yapılandırılmış kapsam yapılandırması neden olabilir.

Karma runbook çalışanını yeniden kaydetmeniz ve yeniden yüklemeniz gerekebilir.

Çalışma alanınızda ulaşılan ve daha fazla veri depolamayı engelleyen bir kota tanımlamış olabilirsiniz.

### <a name="resolution"></a>Çözünürlük

* İşletim sistemine bağlı olarak [Windows](update-agent-issues.md#troubleshoot-offline) veya [Linux](update-agent-issues-linux.md#troubleshoot-offline)için sorun gidericiyi çalıştırın.

* Makinenizin doğru çalışma alanına bildirimde bulunduğundan emin olun. Bu yönü doğrulamaya ilişkin yönergeler için bkz. [Log Analytics için aracı bağlantısını doğrulayın](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Ayrıca, bu çalışma alanının Azure Otomasyonu hesabınıza bağlı olduğundan emin olun. Onaylamak için Otomasyon hesabınıza gidin ve **Ilgili kaynaklar**altında **bağlantılı çalışma alanı** ' nı seçin.

* Makinelerin Log Analytics çalışma alanınızda göründüğünden emin olun. Aşağıdaki sorguyu Otomasyon hesabınıza bağlı Log Analytics çalışma alanında çalıştırın:

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  Makinenizi sorgu sonuçlarında görmüyorsanız, kısa bir süre önce bir yerel yapılandırma sorunu olduğu anlamına gelir ve [aracıyı yeniden yüklemeniz](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)gerekir. Makineniz sorgu sonuçlarında görünüyorsa, bu listedeki bir sonraki madde işaretli öğede belirtilen kapsam yapılandırmasını doğrulamanız gerekir.

* Kapsam yapılandırma sorunlarını denetleyin. [Kapsam yapılandırması](../automation-onboard-solutions-from-automation-account.md#scope-configuration) , hangi makinelerin çözüm için yapılandırıldığını belirler. Makinenizin çalışma alanınızda gösterilmesi, ancak **güncelleştirme yönetimi** portalında yoksa, makineleri hedeflemek için kapsam yapılandırmasını yapılandırmanız gerekecektir. Bunun nasıl yapılacağını öğrenmek için bkz. [çalışma alanındaki makineler](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace)ekleme.

* Çalışma alanınızda aşağıdaki sorguyu çalıştırın:

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  Bir `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` sonucu alırsanız, çalışma alanınızda erişilen ve verilerin kaydedilmesini durduran bir kota vardır. Çalışma alanınızda, **kullanım ve tahmini maliyetler**  > **veri hacmi yönetimi** ' ne gidin ve kotayı denetleyin veya kaldırın.

* Bu adımlar sorununuzu gidermezse Windows karma [Runbook Worker 'ı dağıtma](../automation-windows-hrw-install.md) bölümündeki adımları izleyerek Windows Için karma çalışanı yeniden yükleyin. Veya Linux için [bir Linux karma runbook çalışanı dağıtın](../automation-linux-hrw-install.md).

## <a name="rp-register"></a>Senaryo: abonelikler için Otomasyon kaynak sağlayıcısı kaydedilemiyor

### <a name="issue"></a>Sorun

Otomasyon hesabınızdaki çözümlerle çalışırken aşağıdaki hatayla karşılaşırsınız:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Nedeni

Otomasyon kaynak sağlayıcısı abonelikte kayıtlı değil.

### <a name="resolution"></a>Çözünürlük

Otomasyon kaynak sağlayıcısını kaydetmek için Azure portal aşağıdaki adımları izleyin:

1. Portalın altındaki Azure hizmeti listesinde, **tüm hizmetler**' i seçin ve ardından Genel hizmet grubundaki **abonelikler** ' i seçin.
2. Aboneliğinizi seçin.
3. **Ayarlar**altında **kaynak sağlayıcıları**' nı seçin.
4. Kaynak sağlayıcıları listesinden, **Microsoft. Automation** kaynak sağlayıcısının kayıtlı olduğunu doğrulayın.
5. Listede yoksa, [kaynak sağlayıcısı kaydı hatalarını giderme](/azure/azure-resource-manager/resource-manager-register-provider-errors)adımlarını izleyerek **Microsoft. Automation** sağlayıcısını kaydedin.

## <a name="components-enabled-not-working"></a>Senaryo: Güncelleştirme Yönetimi çözümünün bileşenleri etkinleştirildi ve şimdi bu sanal makine yapılandırılıyor

### <a name="issue"></a>Sorun

Daha sonra bir sanal makinede 15 dakika sonra şu iletiyi görmeye devam edersiniz:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Nedeni

Bu hata, aşağıdaki nedenlerden dolayı oluşabilir:

- Otomasyon hesabıyla iletişim engelleniyor.
- Eklendi olan VM, yüklü Microsoft Monitoring Agent (MMA) ile Sysprep uygulanmamış bir kopyalanmış makineden gelmiş olabilir.

### <a name="resolution"></a>Çözünürlük

1. Güncelleştirme Yönetimi çalışması için hangi adreslere ve bağlantı noktalarına izin verileceğini öğrenmek için [ağ planlamasına](../automation-hybrid-runbook-worker.md#network-planning) gidin.
2. Kopyalanmış bir görüntü kullanıyorsanız:
   1. Log Analytics çalışma alanınızda, sanal makineyi, `MicrosoftDefaultScopeConfig-Updates` kapsam yapılandırması için kaydedilen aramadan kaldırın. Kayıtlı aramalar, çalışma alanınızda **genel** altında bulunabilir.
   2. `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force` öğesini çalıştırın.
   3. @No__t_1 yeniden başlatmak için `Restart-Service HealthService` çalıştırın. Bu, anahtarı yeniden oluşturur ve yeni bir UUID oluşturur.
   4. Bu yaklaşım işe yaramazsa, önce görüntüde Sysprep 'i çalıştırın ve ardından MMA 'yı yüklemeniz gerekir.

## <a name="multi-tenant"></a>Senaryo: başka bir Azure kiracısındaki makineler için bir güncelleştirme dağıtımı oluştururken bağlantılı bir abonelik hatası alıyorsunuz

### <a name="issue"></a>Sorun

Başka bir Azure kiracısındaki makineler için bir güncelleştirme dağıtımı oluşturmaya çalıştığınızda aşağıdaki hatayla karşılaşırsınız:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Nedeni

Bu hata, bir güncelleştirme dağıtımında yer alan başka bir kiracıda Azure VM 'Leri olan bir güncelleştirme dağıtımı oluşturduğunuzda oluşur.

### <a name="resolution"></a>Çözünürlük

Zamanlanmış bu öğeleri almak için aşağıdaki geçici çözümü kullanın. Bir zamanlama oluşturmak için [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet 'ini `-ForUpdate` anahtarıyla birlikte kullanabilirsiniz. Ardından [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) cmdlet 'ini kullanın ve diğer Kiracıdaki makineleri `-NonAzureComputer` parametresine geçirin. Aşağıdaki örnek bunun nasıl yapılacağını göstermektedir:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>Senaryo: açıklanamayan yeniden başlatmalar

### <a name="issue"></a>Sorun

**Yeniden başlatma denetimi** seçeneğini **hiçbir şekilde yeniden başlatmaya**ayarlamış olsanız bile, güncelleştirmeler yüklendikten sonra makineler yeniden başlatılıyor.

### <a name="cause"></a>Nedeni

Windows Update, herhangi biri yeniden başlatma davranışını değiştirebilecek çeşitli kayıt defteri anahtarları tarafından değiştirilebilir.

### <a name="resolution"></a>Çözünürlük

Makinelerinizin düzgün yapılandırıldığından emin olmak için [yeniden başlatmayı yönetmek üzere kullanılan](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kayıt defteri ve kayıt defteri anahtarlarını [düzenleyerek otomatik güncelleştirmeleri yapılandırma](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) altında listelenen kayıt defteri anahtarlarını gözden geçirin.

## <a name="failed-to-start"></a>Senaryo: bir güncelleştirme dağıtımında makine "başlatılamadı" olarak gösteriliyor

### <a name="issue"></a>Sorun

**Bir makine başlatılamadı durumunu gösterir** . Makinenin belirli ayrıntılarını görüntülediğinizde, aşağıdaki hatayı görürsünüz:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Nedeni

Bu hatanın oluşmasının nedeni aşağıdakilerden biri olabilir:

* Makine artık yok.
* Makine kapalı ve ulaşılamaz durumda.
* Makine bir ağ bağlantısı sorununa sahiptir ve bu nedenle makinedeki karma çalışana ulaşılamıyor.
* MMA 'nın Sourcecomputerıd 'sini değiştiren bir güncelleştirmesi vardı.
* Bir Otomasyon hesabında 2.000 eşzamanlı iş sınırına ulaşırsanız güncelleştirme çalışması kısıtlanıyor. Her dağıtım bir iş olarak değerlendirilir ve bir güncelleştirme dağıtımındaki her makine iş olarak sayılır. Otomasyon hesabınızda çalışmakta olan diğer herhangi bir Otomasyon işi veya güncelleştirme dağıtımı, eşzamanlı iş sınırına doğru sayılır.

### <a name="resolution"></a>Çözünürlük

Uygun olduğunda, güncelleştirme dağıtımlarınız için [dinamik grupları](../automation-update-management-groups.md) kullanın. Ayrıca

* Makinenin hala var olduğunu ve erişilebilir olduğunu doğrulayın. Mevcut değilse, dağıtımınızı düzenleyin ve makineyi kaldırın.
* Güncelleştirme Yönetimi için gereken bağlantı noktaları ve adreslerin listesi için [ağ planlama](../automation-update-management.md#ports) bölümüne bakın ve sonra makinenizin bu gereksinimleri karşıladığını doğrulayın.
* Ortamınızdaki `SourceComputerId` değişmiş olan makineleri bulmak için Log Analytics aşağıdaki sorguyu çalıştırın. Aynı `Computer` değerine sahip bilgisayarları, ancak farklı bir `SourceComputerId` değeri olup olmadığına bakın. 

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```
   Etkilenen makineleri bulduktan sonra, bu makineleri hedefleyen güncelleştirme dağıtımlarını düzenleyin ve ardından `SourceComputerId` doğru değeri yansıtması için bunları kaldırın ve yeniden ekleyin.

## <a name="updates-nodeployment"></a>Senaryo: güncelleştirmeler dağıtım olmadan yüklenir

### <a name="issue"></a>Sorun

Bir Windows makinesini Güncelleştirme Yönetimi kaydettiğinizde, bir dağıtım olmadan yüklenen güncelleştirmeleri görürsünüz.

### <a name="cause"></a>Nedeni

Windows 'da, güncelleştirmeler kullanılabilir duruma geldiğinde otomatik olarak yüklenir. Bu davranış, makineye dağıtılacak bir güncelleştirme zamanalmadıysanız karışıklığına neden olabilir.

### <a name="resolution"></a>Çözünürlük

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` kayıt defteri anahtarı varsayılan olarak 4: **otomatik indir ve yükle**ayarlarına sahiptir.

Güncelleştirme Yönetimi istemciler için, bu anahtarı 3: **Otomatik İndir ancak otomatik olarak yükle**' ye ayarlamayı öneririz.

Daha fazla bilgi için bkz. [otomatik güncelleştirmeleri yapılandırma](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="machine-already-registered"></a>Senaryo: makine farklı bir hesaba zaten kayıtlı

### <a name="issue"></a>Sorun

Aşağıdaki hata iletisini alırsınız:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Nedeni

Makine, Güncelleştirme Yönetimi için başka bir çalışma alanına zaten eklendi.

### <a name="resolution"></a>Çözünürlük

1. Makinenin doğru çalışma alanına rapor olduğundan emin olmak için, makineler bölümündeki adımları izleyin [güncelleştirme yönetimi altında portalda görünmez](#nologs) .
2. [Karma runbook grubunu silerek](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group)makinedeki eski yapıtları temizleyin ve sonra yeniden deneyin.

## <a name="machine-unable-to-communicate"></a>Senaryo: makine hizmetle iletişim kuramıyor

### <a name="issue"></a>Sorun

Aşağıdaki hata iletilerinden birini alırsınız:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

### <a name="cause"></a>Nedeni

Bir proxy, ağ geçidi veya güvenlik duvarı ağ iletişimini engelliyor olabilir.

### <a name="resolution"></a>Çözünürlük

Ağınızı gözden geçirin ve uygun bağlantı noktalarına ve adreslere izin verildiğinden emin olun. Güncelleştirme Yönetimi ve karma runbook çalışanları için gereken bağlantı noktalarının ve adreslerin listesi için bkz. [ağ gereksinimleri](../automation-hybrid-runbook-worker.md#network-planning) .

## <a name="unable-to-create-selfsigned-cert"></a>Senaryo: otomatik olarak imzalanan sertifika oluşturulamıyor

### <a name="issue"></a>Sorun

Aşağıdaki hata iletilerinden birini alırsınız:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Nedeni

Karma Runbook Worker otomatik olarak imzalanan bir sertifika oluşturamadı.

### <a name="resolution"></a>Çözünürlük

Sistem hesabının **C:\programdata\microsoft\crypto\rsa** klasörüne okuma erişimi olduğunu doğrulayın ve yeniden deneyin.

## <a name="mw-exceeded"></a>Senaryo: zamanlanan güncelleştirme bir MaintenanceWindowExceeded hatasıyla başarısız oldu

### <a name="issue"></a>Sorun

Güncelleştirmeler için varsayılan bakım penceresi 120 dakikadır. Bakım penceresini en fazla 6 saat veya 360 dakika olarak artırabilirsiniz.

### <a name="resolution"></a>Çözünürlük

Başarısız olan zamanlanmış güncelleştirme dağıtımlarını düzenleyin ve bakım penceresini arttırın.

Bakım pencereleri hakkında daha fazla bilgi için bkz. [Install Updates](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="hresult"></a>Senaryo: makine "değerlendirilmedi" olarak gösterilir ve bir HResult özel durumu gösterir

### <a name="issue"></a>Sorun

* **Uyumluluk**altında **değerlendirilmeyen** olarak görünen makineleriniz vardır ve bunun altında bir özel durum iletisi görürsünüz.
* Değerlendirilmeyen olarak gösteren makineleriniz vardır.
* Portalda bir HRESULT hata kodu görürsünüz.

### <a name="cause"></a>Nedeni

Güncelleştirme Aracısı (Windows üzerindeki Windows Update Aracısı; bir Linux dağıtımı için Paket Yöneticisi) doğru yapılandırılmamış. Güncelleştirme Yönetimi, gereken güncelleştirmeleri, düzeltme ekinin durumunu ve dağıtılan düzeltme eklerinin sonuçlarını sağlamak için makinenin güncelleştirme aracısına bağımlıdır. Bu bilgiler olmadan, Güncelleştirme Yönetimi gereken veya yüklenen düzeltme eklerine düzgün şekilde bildiremiyor.

### <a name="resolution"></a>Çözünürlük

Güncelleştirmeleri makinede yerel olarak gerçekleştirmeyi deneyin. Bu başarısız olursa, genellikle güncelleştirme aracısında bir yapılandırma hatası olduğu anlamına gelir.

Bu sorun genellikle ağ yapılandırması ve güvenlik duvarı sorunlarından kaynaklanır. Aşağıdaki işlemi deneyin:

* Linux için, paket deponuzdaki ağ uç noktasına ulaşabildiğinizden emin olmak için uygun belgeleri denetleyin.
* Windows için, [güncelleştirmeler intranet uç noktasından (WSUS/SCCM) indirmeyen güncelleştirmeler](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)bölümünde listelenen aracı yapılandırmanızı denetleyin.
  * Makineler Windows Update için yapılandırılmışsa, [http/proxy ile Ilgili sorunlar](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)bölümünde açıklanan uç noktalara ulaşabildiğinizden emin olun.
  * Makineler Windows Server Update Services (WSUS) için yapılandırılmışsa, [WUServer kayıt defteri anahtarı](/windows/deployment/update/waas-wu-settings)tarafından yapılandırılan WSUS sunucusuna erişebildiğinizden emin olun.

Bir HRESULT görürseniz, tüm özel durum iletisini görmek için kırmızı renkte görüntülenecek özel duruma çift tıklayın. Olası çözümler veya önerilen eylemler için aşağıdaki tabloyu gözden geçirin:

|Özel durum  |Çözüm veya eylem  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Özel durumun nedeni hakkında ek ayrıntılar bulmak için [Windows Update hata kodu listesinde](https://support.microsoft.com/help/938205/windows-update-error-code-list) ilgili hata kodunu arayın.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Bunlar ağ bağlantısı sorunlarını gösterir. Makinenizin Güncelleştirme Yönetimi için ağ bağlantısı olduğundan emin olun. Gerekli bağlantı noktalarının ve adreslerin listesi için [ağ planlama](../automation-update-management.md#ports) bölümüne bakın.        |
|`0x8024001E`| Hizmet veya sistem kapatıldığından güncelleştirme işlemi tamamlanmadı.|
|`0x8024002E`| Windows Update hizmeti devre dışı bırakıldı.|
|`0x8024402C`     | Bir WSUS sunucusu kullanıyorsanız, `WUServer` ve `WUStatusServer` kayıt defteri değerlerinin `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` kayıt defteri anahtarı altında doğru WSUS sunucusunu belirtdiğinizden emin olun.        |
|`0x80072EE2`|Bir ağ bağlantısı sorunu veya yapılandırılmış bir WSUS sunucusuyla iletişim kurulurken bir sorun var. WSUS ayarlarını denetleyin ve hizmetin istemciden erişilebilir olduğundan emin olun.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Windows Update hizmeti 'nin (wuauserv) çalıştığından ve devre dışı bırakılmadığından emin olun.        |
|Diğer tüm genel durumlar     | Olası çözümler için internet 'te bir arama çalıştırın ve yerel BT desteğiniz ile çalışın.         |

Windowsupdate. log dosyasını gözden geçirmek olası nedenleri belirlemenize de yardımcı olabilir. Günlüğü okuma hakkında daha fazla bilgi için bkz. [windowsupdate. log dosyasını okuma](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Ayrıca, makinede Windows Update sorunları denetlemek için [Windows Update sorun gidericiyi](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) indirebilir ve çalıştırabilirsiniz.

> [!NOTE]
> [Windows Update sorun giderici](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) belgeleri Windows istemcilerinde kullanılması gerektiğini gösterir, ancak Windows Server 'da da kullanılabilir.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Senaryo: güncelleştirme çalıştırması "başarısız" durumu (Linux) döndürüyor

### <a name="issue"></a>Sorun

Bir güncelleştirme çalıştırması başlatılır, ancak çalıştırma sırasında hatalarla karşılaşır.

### <a name="cause"></a>Nedeni

Olası nedenler:

* Paket Yöneticisi sağlam değil.
* Güncelleştirme Aracısı (Windows için WUA, Linux için özel paket yöneticisi) yanlış yapılandırılmış.
* Belirli paketler, bulut tabanlı düzeltme eki uygulama ile müdahale ediyor.
* Makineye ulaşılamıyor.
* Güncelleştirmeler çözümlenmeyen bağımlılıklara sahipti.

### <a name="resolution"></a>Çözünürlük

Bir güncelleştirme çalıştırması sırasında başarılı olduktan sonra bir başarısızlık oluşursa, çalıştırmada etkilenen makinenin [iş çıktısını kontrol](../manage-update-multi.md#view-results-of-an-update-deployment) edin. Çözebileceğinizi ve üzerinde işlem yapmanız gereken makinelerinizden belirli hata iletileri bulabilirsiniz. Güncelleştirme Yönetimi, paket yöneticisinin başarılı güncelleştirme dağıtımları için sağlıklı olmasını gerektirir.

İş başarısız olmadan hemen önce belirli düzeltme ekleri, paketler veya güncelleştirmeler görülemezse, bunları bir sonraki güncelleştirme dağıtımından [dışlanmasını](../automation-tutorial-update-management.md#schedule-an-update-deployment) deneyebilirsiniz. Windows Update günlük bilgilerini toplamak için bkz. [Windows Update günlük dosyaları](/windows/deployment/update/windows-update-logs).

Düzeltme eki uygulama sorununu çözemezseniz, sonraki güncelleştirme dağıtımı başlamadan *önce* aşağıdaki günlük dosyasının bir kopyasını oluşturun ve sorun giderme amacıyla koruyun:

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Düzeltme ekleri yüklenmedi

### <a name="machines-dont-install-updates"></a>Makineler güncelleştirmeleri yüklemez

* Güncelleştirmeleri doğrudan makinede çalıştırmayı deneyin. Makine güncelleştirmeleri uygulayamıyorum, [sorun giderme kılavuzunda olası hataların listesine](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult)bakın.
* Güncelleştirmeler yerel olarak çalışıyorsa, [GÜNCELLEŞTIRME YÖNETIMI VM 'Yi kaldırma](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources)konusunda yer alan kılavuzu izleyerek aracıyı kaldırıp yeniden yüklemeyi deneyin.

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Güncelleştirmelerin kullanılabildiğini biliyorum, ancak makinelerimde kullanılabilir olarak gösterilmiyorum

* Bu durum genellikle makineler WSUS veya System Center Configuration Manager (SCCM) güncelleştirmelerini almak üzere yapılandırılmışsa ancak WSUS ve SCCM güncelleştirmeleri onaylamamışsa oluşur.
* [Bu makalenin "kayıt defterini düzenleyerek otomatik güncelleştirmeleri yapılandırma" bölümündeki kayıt defteri anahtarlarına UseWUServer kayıt defteri anahtarını çapraz](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)olarak bağlayarak, makinelerin WSUS ve SCCM için yapılandırılıp yapılandırılmadığını kontrol edebilirsiniz.
* Güncelleştirmeler WSUS 'de onaylanmamışsa, bunlar yüklenmez. Aşağıdaki sorguyu çalıştırarak, Log Analytics ' de onaylanmamış güncelleştirmeleri denetleyebilirsiniz:

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Güncelleştirmeler yüklendi olarak gösteriliyor, ancak makinamda bulamıyorum

* Güncelleştirmeler genellikle başka güncelleştirmelerle değiştirilir. Daha fazla bilgi için, Windows Update sorun giderme kılavuzunda [güncelleştirme 'nin yerini almıştır](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) .

### <a name="installing-updates-by-classification-on-linux"></a>Linux'da güncelleştirmeleri sınıflandırmaya göre yükleme

* Güncelleştirmeleri Linux'a sınıflandırmaya göre ("Kritik ve güvenlik güncelleştirmeleri") yüklemek, özellikle CentOS için önemli uyarılara tabidir. Bu sınırlamalar [güncelleştirme yönetimi genel bakış sayfasında](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)belgelenmiştir.

### <a name="kb2267602-is-consistently-missing"></a>KB2267602, sürekli olarak eksik

* KB2267602, [Windows Defender tanım güncelleştirmesidir](https://www.microsoft.com/wdsi/definitions). Günlük olarak güncelleştirilir.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemezseniz, ek destek için aşağıdaki kanallardan birini deneyin:

* Azure [forumları](https://azure.microsoft.com/support/forums/)aracılığıyla Azure uzmanlarından yanıtlar alın.
* Azure Community 'yi doğru kaynaklara bağlayarak müşteri deneyimini iyileştirmeye yönelik [@AzureSupport](https://twitter.com/azuresupport), resmi Microsoft Azure hesabı ile bağlanın: yanıtlar, destek ve uzmanlar.
* Azure destek olayı dosyası oluşturma. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.
