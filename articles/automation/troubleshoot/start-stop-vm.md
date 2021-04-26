---
title: Azure Otomasyonu VM'leri çalışma saatleri dışında başlat/durdur sorunlarını giderme
description: Bu makalede, VM'leri çalışma saatleri dışında başlat/durdur özelliğinin kullanımı sırasında oluşan sorunların nasıl giderileceği ve çözüleceği açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b03481b41e879acac4c3b193d72594454f650525
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831195"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>VM'leri çalışma saatleri dışında başlat/durdur sorunlarını giderme

Bu makalede, Azure Otomasyonu VM'leri çalışma saatleri dışında başlat/durdur özelliğini sanal makinelerinize dağıtırken ortaya çıkan sorunları giderme ve çözme hakkında bilgi sağlanır. 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Senaryo: VM'leri çalışma saatleri dışında başlat/durdur düzgün şekilde dağıtılamazsa

### <a name="issue"></a>Sorun

[VM'leri çalışma saatleri dışında Başlat/Durdur](../automation-solution-vm-management.md)dağıtırken, aşağıdaki hatalardan birini alırsınız:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Nedeni

Dağıtımlar aşağıdaki nedenlerden biri nedeniyle başarısız olabilir:

- Seçilen bölgede aynı ada sahip bir Otomasyon hesabı zaten var.
- İlke VM'leri çalışma saatleri dışında başlat/durdur dağıtımına izin vermez.
- `Microsoft.OperationsManagement`, `Microsoft.Insights` Veya `Microsoft.Automation` kaynak türü kayıtlı değil.
- Log Analytics çalışma alanınız kilitli.
- Azurermmodules veya VM'leri çalışma saatleri dışında başlat/durdur özelliğinin güncel olmayan bir sürümüne sahipsiniz.

### <a name="resolution"></a>Çözüm

Olası çözümler için aşağıdaki düzeltmeleri gözden geçirin:

* Otomasyon hesaplarının farklı kaynak gruplarında olsalar bile, bir Azure bölgesi içinde benzersiz olması gerekir. Hedef bölgede mevcut Otomasyon hesaplarınızı kontrol edin.
* Mevcut bir ilke, VM'leri çalışma saatleri dışında başlat/durdur dağıtılması için gereken bir kaynağı engeller. Azure portal ilke atamalarınız ' ne gidin ve bu kaynağın dağıtımına izin vermeyen bir ilke atamamı olup olmadığınızı denetleyin. Daha fazla bilgi için bkz. [Requestdisallowedbypolicy hatası](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* VM'leri çalışma saatleri dışında başlat/durdur dağıtmak için aboneliğinizin aşağıdaki Azure Kaynak ad alanlarına kayıtlı olması gerekir:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Sağlayıcıları kaydettiğinizde hatalar hakkında daha fazla bilgi edinmek için bkz. [kaynak sağlayıcısı kaydı hatalarını giderme](../../azure-resource-manager/templates/error-register-resource-provider.md).
* Log Analytics çalışma alanınızda bir kilit varsa, Azure portal çalışma alanınıza gidin ve kaynaktaki kilitleri kaldırın.
* Bu çözümler sorununuzu çözmezse, VM'leri çalışma saatleri dışında başlat/durdur yeniden dağıtmak için [özelliği Güncelleştir](../automation-solution-vm-management.md#update-the-feature) bölümündeki yönergeleri izleyin.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Senaryo: tüm VM 'Ler başlatılamadı veya durdurulamıyor

### <a name="issue"></a>Sorun

VM'leri çalışma saatleri dışında başlat/durdur yapılandırdınız, ancak tüm VM 'Leri başlatmıyor veya durdurmayacak.

### <a name="cause"></a>Nedeni

Bu hata, aşağıdakilerden biri nedeniyle oluşabilir:

- Bir zamanlama doğru yapılandırılmamış.
- Farklı Çalıştır hesabı doğru şekilde yapılandırılmamış olabilir.
- Bir runbook 'ta çalıştırma hataları olabilir.
- VM 'Ler dışarıda bırakılmış olabilir.

### <a name="resolution"></a>Çözüm

Olası çözümler için aşağıdaki listeyi gözden geçirin:

* VM'leri çalışma saatleri dışında başlat/durdur için bir zamanlamayı doğru şekilde yapılandırdığınızdan emin olun. Zamanlamayı yapılandırma hakkında bilgi edinmek için bkz. [zamanlamalar](../shared-resources/schedules.md).

* Herhangi bir hata bulmak için [iş akışlarını](../automation-runbook-execution.md#job-statuses) denetleyin. Aşağıdaki runbook 'lardan birindeki işleri arayın:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* [Farklı Çalıştır hesabınızın](../automation-security-overview.md#run-as-accounts) başlatmaya çalıştığınız veya durdurduğunuz VM 'ler için uygun izinlere sahip olduğunu doğrulayın. Bir kaynaktaki izinleri nasıl denetleyeceğinizi öğrenmek için bkz. [hızlı başlangıç: Azure Portal kullanarak bir kullanıcıya atanan rolleri görüntüleme](../../role-based-access-control/check-access.md). Farklı Çalıştır hesabı tarafından kullanılan hizmet sorumlusu için uygulama KIMLIĞINI sağlamanız gerekir. Azure portal Otomasyon hesabınıza giderek bu değeri alabilirsiniz. **Hesap ayarları**' nın altında **Farklı Çalıştır hesapları** ' nı seçin ve uygun farklı çalıştır hesabını seçin.

* VM 'Ler açıkça dışlandıklarında, sanal makineler başlatılmamış veya durdurulmuş olabilir. Dışlanan VM 'Ler, `External_ExcludeVMNames` özelliğin dağıtıldığı Otomasyon hesabındaki değişkende ayarlanır. Aşağıdaki örnek, bu değeri PowerShell ile nasıl sorgulayakullanabileceğinizi gösterir.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Senaryo: bazı sanal makinelerimin başlatılması veya durdurulması başarısız oluyor

### <a name="issue"></a>Sorun

VM'leri çalışma saatleri dışında başlat/durdur yapılandırdınız, ancak yapılandırılmış VM 'lerin bazılarını başlatmıyor veya durdurmayacak.

### <a name="cause"></a>Nedeni

Bu hata, aşağıdakilerden biri nedeniyle oluşabilir:

- Sıra senaryosunda, bir etiket eksik veya yanlış olabilir.
- VM dışlanıyor olabilir.
- Farklı Çalıştır hesabı VM 'de yeterli izinlere sahip olmayabilir.
- VM 'nin başlamasını veya durdurulmasını durduran bir sorun olabilir.

### <a name="resolution"></a>Çözüm

Olası çözümler için aşağıdaki listeyi gözden geçirin:

* VM'leri çalışma saatleri dışında başlat/durdur [dizi senaryosunu](../automation-solution-vm-management.md) kullandığınızda, başlatmak veya durdurmak ISTEDIĞINIZ her VM 'nin doğru etikete sahip olduğundan emin olmanız gerekir. Başlatmak istediğiniz sanal makinelerin `sequencestart` etiketine ve durdurmak Istediğiniz VM 'lere sahip olduğundan emin olun `sequencestop` . Her iki etiket de pozitif bir tamsayı değeri gerektirir. Etiketleri ve bunların değerlerini içeren tüm VM 'Leri aramak için aşağıdaki örneğe benzer bir sorgu kullanabilirsiniz.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* VM 'Ler açıkça dışlandıklarında, sanal makineler başlatılmamış veya durdurulmuş olabilir. Dışlanan VM 'Ler, `External_ExcludeVMNames` özelliğin dağıtıldığı Otomasyon hesabındaki değişkende ayarlanır. Aşağıdaki örnek, bu değeri PowerShell ile nasıl sorgulayakullanabileceğinizi gösterir.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* VM 'Leri başlatmak ve durdurmak için, Otomasyon hesabının farklı çalıştır hesabı VM 'de uygun izinlere sahip olmalıdır. Bir kaynaktaki izinleri nasıl denetleyeceğinizi öğrenmek için bkz. [hızlı başlangıç: Azure Portal kullanarak bir kullanıcıya atanan rolleri görüntüleme](../../role-based-access-control/check-access.md). Farklı Çalıştır hesabı tarafından kullanılan hizmet sorumlusu için uygulama KIMLIĞINI sağlamanız gerekir. Azure portal Otomasyon hesabınıza giderek bu değeri alabilirsiniz. **Hesap ayarları** altında **Farklı Çalıştır hesapları** ' nı seçin ve uygun farklı çalıştır hesabını seçin.
* VM 'de başlatma veya ayırmayı kaldırma konusunda bir sorun varsa, sanal makinenin kendisi üzerinde bir sorun olabilir. Örnekler, sanal makine kapanmaya çalışırken uygulanan bir güncelleştirmedir, askıda olan bir hizmet ve daha fazlasını sağlar. Günlüklerde hata olup olmadığını görmek için VM kaynağına gidin ve **etkinlik günlükleri** ' ne bakın. Ayrıca, olay günlüklerinde herhangi bir hata olup olmadığını görmek için VM 'de oturum açma girişiminde bulunabilir. VM 'nizin sorunlarını giderme hakkında daha fazla bilgi edinmek için bkz. [Azure sanal makinelerinde sorun giderme](/troubleshoot/azure/virtual-machines/welcome-virtual-machines).
* Herhangi bir hata bulmak için [iş akışlarını](../automation-runbook-execution.md#job-statuses) denetleyin. Portalda Otomasyon hesabınıza gidin ve **Işlem Otomasyonu** altında **işler** ' i seçin.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Senaryo: özel runbook sunucum sanal makinelerimi başlatamıyor veya durdurabilir

### <a name="issue"></a>Sorun

Özel bir runbook 'u yazdı veya PowerShell Galerisi bir tane indirdiğiniz için düzgün çalışmıyor.

### <a name="cause"></a>Nedeni

Hatanın pek çok nedeni olabilir. Azure portal Otomasyon hesabınıza gidin ve **Işlem Otomasyonu** altında **işler** ' i seçin. **İşler** sayfasından, iş başarısızlıklarını görüntülemek için Runbook 'ınızdan işler ' i arayın.

### <a name="resolution"></a>Çözüm

Şunları yapmanızı öneririz:

* Azure Otomasyonu 'nda VM 'Leri başlatmak ve durdurmak için [VM'leri çalışma saatleri dışında Başlat/Durdur](../automation-solution-vm-management.md) kullanın. 
* Microsoft 'un özel runbook 'ları desteklemediğini unutmayın. [Runbook sorunlarını gidermek](runbooks.md)için özel runbook 'ünüz için bir çözüm bulabilirsiniz. Herhangi bir hata bulmak için [iş akışlarını](../automation-runbook-execution.md#job-statuses) denetleyin. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Senaryo: VM 'Ler doğru sırada başlamıyor veya durdurulmayacak

### <a name="issue"></a>Sorun

Özellik için etkinleştirdiğiniz VM 'Ler doğru sırada başlatılmaz veya duramaz.

### <a name="cause"></a>Nedeni

Bu sorun, VM 'lerde hatalı etiketlemesinin oluşmasına neden olur.

### <a name="resolution"></a>Çözüm

Özelliğin doğru şekilde etkinleştirildiğinden emin olmak için şu adımları izleyin:

1. `sequencestart`Durumunuza bağlı olarak, başlatılacak veya durdurulmuş tüm VM 'lerin veya etiketine sahip olduğundan emin olun `sequencestop` . Bu etiketlerin değeri olarak pozitif bir tamsayı olması gerekir. VM 'Ler, bu değere göre artan sırada işlenir.
1. Başlatılacak veya durdurulacak VM 'Ler için kaynak gruplarının, `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` durumunuza bağlı olarak veya değişkenlerinde olduğundan emin olun.
1.  `WHATIF` Değişikliklerinizi önizlemek için parametresi doğru olarak ayarlanmış SequencedStartStop_Parent runbook 'unu yürüterek yaptığınız değişiklikleri test edin.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Senaryo: VM'leri çalışma saatleri dışında başlat/durdur işi 403 Yasak hata ile başarısız oluyor

### <a name="issue"></a>Sorun

`403 forbidden`VM'leri çalışma saatleri dışında Başlat/Durdur runbook 'lar için hata vererek başarısız olan işleri bulabilirsiniz.

### <a name="cause"></a>Nedeni

Bu sorun, yanlış yapılandırılmış veya zaman aşımına uğramamış farklı çalıştır hesabından kaynaklanabilir. Ayrıca, farklı çalıştır hesabı tarafından VM kaynaklarına yetersiz izinler de olabilir.

### <a name="resolution"></a>Çözüm

Farklı Çalıştır hesabınızın düzgün yapılandırıldığını doğrulamak için, Azure portal Otomasyon hesabınıza gidin ve **Hesap ayarları** altında **Farklı Çalıştır hesapları** ' nı seçin. Farklı Çalıştır hesabı yanlış yapılandırılmamışsa veya süre dolmuşsa, durum koşulu gösterir.

Farklı Çalıştır hesabınız yanlış yapılandırılmış ise, farklı çalıştır hesabınızı silin ve yeniden oluşturun. Daha fazla bilgi için bkz. [Azure Otomasyonu farklı çalıştır hesapları](../automation-security-overview.md#run-as-accounts).

Farklı Çalıştır hesabınız için sertifikanın geçerliliği dolmuşsa, sertifikayı yenilemek için [otomatik olarak imzalanan sertifika yenileme](../manage-runas-account.md#cert-renewal) bölümündeki adımları izleyin.

Eksik izinler varsa bkz. [hızlı başlangıç: Azure Portal kullanarak bir kullanıcıya atanan rolleri görüntüleme](../../role-based-access-control/check-access.md). Farklı Çalıştır hesabı tarafından kullanılan hizmet sorumlusu için uygulama KIMLIĞINI sağlamanız gerekir. Azure portal Otomasyon hesabınıza giderek bu değeri alabilirsiniz. **Hesap ayarları**' nın altında **Farklı Çalıştır hesapları** ' nı seçin ve uygun farklı çalıştır hesabını seçin.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Senaryo: sorunum burada listelenmiyor

### <a name="issue"></a>Sorun

Bu sayfada listelenmeyen VM'leri çalışma saatleri dışında başlat/durdur kullandığınızda bir sorun veya beklenmeyen bir sonuç yaşarsınız.

### <a name="cause"></a>Nedeni

Birçok kez hatanın nedeni, özelliğin eski ve güncel olmayan bir sürümü kullanılıyor olabilir.

> [!NOTE]
> VM'leri çalışma saatleri dışında başlat/durdur özelliği, özelliği sanal makinelere dağıtırken Otomasyon hesabınıza aktarılan Azure modülleriyle test edilmiştir. Özelliği şu anda Azure modülünün daha yeni sürümleriyle çalışmıyor. Bu kısıtlama yalnızca VM'leri çalışma saatleri dışında başlat/durdur çalıştırmak için kullandığınız Otomasyon hesabını etkiler. Diğer otomasyon hesaplarınızda Azure modülünün daha yeni sürümlerini [güncelleştirme Azure PowerShell modüller](../automation-update-azure-modules.md)' de açıklandığı gibi kullanabilirsiniz.

### <a name="resolution"></a>Çözüm

Birçok hatayı çözümlemek için VM'leri çalışma saatleri dışında başlat/durdur kaldırın ve [güncelleştirin](../automation-solution-vm-management.md#update-the-feature). Ayrıca, herhangi bir hata aramak için [iş akışlarını](../automation-runbook-execution.md#job-statuses) kontrol edebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu burada görmüyorsanız veya sorununuzu çözemezseniz, ek destek için aşağıdaki kanallardan birini deneyin:

* Azure [forumları](https://azure.microsoft.com/support/forums/)aracılığıyla Azure uzmanlarından yanıtlar alın.
* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabıyla bağlanın. Azure desteği, Azure Community 'yi yanıtlar, destek ve uzmanlar için bağlar.
* Azure destek olayı dosyası oluşturma. [Azure destek sitesine](https://azure.microsoft.com/support/options/)gidin ve **Destek Al**' ı seçin.
