---
title: Azure Otomasyonu ile VM 'Leri başlatma ve durdurma sorunlarını giderme
description: Bu makalede, Azure Otomasyonu 'nda VM 'Leri başlatma ve durdurma sorunlarını giderme hakkında bilgi sağlanır
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 860a47386b31403b6a3d41fc2473b1e1040889a7
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162036"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Çalışma saatleri dışında VM 'Leri Başlat/Durdur çözümü sorunlarını giderme

## <a name="deployment-failure"></a>Senaryo: sanal makine Başlat/Durdur çözümü düzgün şekilde dağıtılamadı

### <a name="issue"></a>Sorun

[Çalışma saatleri dışında VM 'Leri Başlat/Durdur çözümü](../automation-solution-vm-management.md)dağıtılırken, aşağıdaki hatalardan birini alırsınız:

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

1. Seçilen bölgede aynı ada sahip bir Otomasyon hesabı zaten var.
2. VM 'Leri Başlat/Durdur çözümünün dağıtımına izin veren bir ilke yerinde.
3. `Microsoft.OperationsManagement`, `Microsoft.Insights`veya `Microsoft.Automation` kaynak türleri kayıtlı değil.
4. Log Analytics çalışma alanınızın üzerinde bir kilidi vardır.
5. Eski bir Azurerd modülleri sürümüne veya Başlat/Durdur çözümüne sahipsiniz.

### <a name="resolution"></a>Çözünürlük

Sorununuz için olası çözümleri veya aranacak konumları görmek için aşağıdaki listeyi gözden geçirin:

1. Otomasyon hesaplarının farklı kaynak gruplarında olsalar bile, bir Azure bölgesi içinde benzersiz olması gerekir. Hedef bölgede mevcut Otomasyon hesaplarınızı kontrol edin.
2. Var olan bir ilke, VM çözümünün başlatılması için gereken bir kaynağı engeller. Azure portal ilke atamalarınız ' ne gidin ve bu kaynağın dağıtımına izin vermeyen bir ilke atamamı olup olmadığınızı denetleyin. Bu konuda daha fazla bilgi edinmek için bkz. [Requestdisallowedbypolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md).
3. Sanal makineyi Başlat/Durdur çözümünü dağıtmak için aboneliğinizin aşağıdaki Azure Kaynak ad alanlarına kayıtlı olması gerekir:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Sağlayıcıları kaydettirme sırasında hatalar hakkında daha fazla bilgi edinmek için bkz. [kaynak sağlayıcısı kaydı hatalarını giderme](../../azure-resource-manager/resource-manager-register-provider-errors.md) .
4. Log Analytics çalışma alanınızda bir kilit varsa, Azure portal çalışma alanınıza gidin ve kaynaktaki kilitleri kaldırın.
5. Yukarıdaki çözünürlükler sorununuzu çözmezse, Başlat/Durdur çözümünü yeniden dağıtmak için [çözümü güncelleştirme](../automation-solution-vm-management.md#update-the-solution) bölümündeki yönergeleri izleyin.

## <a name="all-vms-fail-to-startstop"></a>Senaryo: tüm VM 'Ler başlatılamadı/durdurulamıyor

### <a name="issue"></a>Sorun

VM 'yi Başlat/Durdur çözümünü yapılandırdınız, ancak yapılandırılan tüm VM 'Leri başlatmıyor veya durdurmayacak.

### <a name="cause"></a>Nedeni

Bu hata, aşağıdakilerden biri nedeniyle oluşabilir:

1. Zamanlama doğru yapılandırılmadı
2. RunAs hesabı doğru şekilde yapılandırılmamış olabilir
3. Bir runbook 'ta çalıştırma hataları olabilir
4. VM 'Ler dışarıda bırakılmış olabilir

### <a name="resolution"></a>Çözünürlük

Sorununuz için olası çözümleri veya aranacak konumları görmek için aşağıdaki listeyi gözden geçirin:

* Sanal makine Başlat/Durdur çözümü için bir zamanlamayı doğru şekilde yapılandırdığınızdan emin olun. Zamanlamayı yapılandırma hakkında bilgi edinmek için bkz. [zamanlamalar](../automation-schedules.md) makalesi.

* Herhangi bir hata bulmak için [iş akışlarını](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) denetleyin. Portalda Otomasyon hesabınıza gidin ve **Işlem Otomasyonu**altında **işler** ' i seçin. **İşler** sayfasında, aşağıdaki runbook 'lardan birindeki işleri arayın:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* [Runas hesabınızın](../manage-runas-account.md) başlatılmaya veya durdurulmaya denediğiniz VM 'ler için uygun izinlere sahip olduğunu doğrulayın. Bir kaynaktaki izinleri nasıl denetleyeceğinizi öğrenmek için bkz. [hızlı başlangıç: Azure Portal kullanarak bir kullanıcıya atanan rolleri görüntüleme](../../role-based-access-control/check-access.md). Farklı Çalıştır hesabı tarafından kullanılan hizmet sorumlusu için uygulama kimliğini sağlamanız gerekir. Bu değeri, Azure portal Otomasyon hesabınıza giderek, **Hesap ayarları** altında **Farklı Çalıştır hesapları** ' nı seçerek ve uygun farklı Çalıştır hesabına tıklayarak elde edebilirsiniz.

* VM 'Ler açıkça dışlandıklarında, sanal makineler başlatılamaz veya durdurulamaz. Çözümün dağıtıldığı Otomasyon hesabındaki **External_ExcludeVMNames** değişkeninde ayarlanan sanal makineler. Aşağıdaki örnek, bu değeri PowerShell ile nasıl sorgulayakullanabileceğinizi gösterir.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Senaryo: bazı sanal makinelerimin başlatılması veya durdurulması başarısız oluyor

### <a name="issue"></a>Sorun

VM 'yi Başlat/Durdur çözümünü yapılandırdınız, ancak yapılandırılmış VM 'lerin bazılarını başlatmıyor veya durdurmayacak.

### <a name="cause"></a>Nedeni

Bu hata, aşağıdakilerden biri nedeniyle oluşabilir:

1. Sıra senaryosu kullanılıyorsa, bir etiket eksik veya hatalı olabilir
2. VM dışlanıyor olabilir
3. RunAs hesabı VM 'de yeterli izinlere sahip olmayabilir
4. VM 'nin başlamasını veya durdurmasını durduran bir şey olabilir

### <a name="resolution"></a>Çözünürlük

Sorununuz için olası çözümleri veya aranacak konumları görmek için aşağıdaki listeyi gözden geçirin:

* Çalışma saatleri dışında VM Başlat/Durdur çözümünün [sıra senaryosunu](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) kullanırken, başlatmak veya durdurmak ISTEDIĞINIZ her VM 'nin doğru etikete sahip olduğundan emin olmanız gerekir. Başlatmak istediğiniz sanal makinelerin `sequencestart` etiketine sahip olduğundan ve durdurmak istediğiniz VM 'Lerin `sequencestop` etiketine sahip olduğundan emin olun. Her iki etiket de pozitif bir tamsayı değeri gerektirir. Etiketleri ve bunların değerlerini içeren tüm VM 'Leri aramak için aşağıdaki örneğe benzer bir sorgu kullanabilirsiniz.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* VM 'Ler açıkça dışlandıklarında, sanal makineler başlatılamaz veya durdurulamaz. Çözümün dağıtıldığı Otomasyon hesabındaki **External_ExcludeVMNames** değişkeninde ayarlanan sanal makineler. Aşağıdaki örnek, bu değeri PowerShell ile nasıl sorgulayakullanabileceğinizi gösterir.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* VM 'Leri başlatmak ve durdurmak için, Otomasyon hesabının RunAs hesabının VM 'de uygun izinlere sahip olması gerekir. Bir kaynaktaki izinleri nasıl denetleyeceğinizi öğrenmek için bkz. [hızlı başlangıç: Azure Portal kullanarak bir kullanıcıya atanan rolleri görüntüleme](../../role-based-access-control/check-access.md). Farklı Çalıştır hesabı tarafından kullanılan hizmet sorumlusu için uygulama kimliğini sağlamanız gerekir. Bu değeri, Azure portal Otomasyon hesabınıza giderek, **Hesap ayarları** altında **Farklı Çalıştır hesapları** ' nı seçerek ve uygun farklı Çalıştır hesabına tıklayarak elde edebilirsiniz.

* VM 'nin başlatma veya ayırmayı kaldırma konusunda bir sorun yaşıyorsanız, bu davranışa sanal makinenin bir sorunu neden olabilir. Bazı örnekler veya olası sorunlar, kapatmaya çalışırken bir güncelleştirme uygulanıyor, bir hizmetin askıda kalması ve daha fazlası). Günlüklerde hata olup olmadığını görmek için VM kaynağına gidin ve **etkinlik günlüklerine** bakın. Ayrıca, olay günlüklerinde hata olup olmadığını görmek için VM 'de oturum açma girişiminde bulunabilir. VM 'niz hakkında sorun giderme hakkında daha fazla bilgi için bkz. [Azure sanal makinelerinde sorun giderme](../../virtual-machines/troubleshooting/index.md)

* Herhangi bir hata bulmak için [iş akışlarını](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) denetleyin. Portalda Otomasyon hesabınıza gidin ve **Işlem Otomasyonu**altında **işler** ' i seçin.

## <a name="custom-runbook"></a>Senaryo: özel runbook sunucum sanal makinelerimi başlatamıyor veya durdurabilir

### <a name="issue"></a>Sorun

Özel bir runbook 'u yazdı veya PowerShell Galerisi bir tane indirdiğiniz için düzgün çalışmıyor.

### <a name="cause"></a>Nedeni

Hatanın nedeni birçok işlemlerden biri olabilir. Azure portal Otomasyon hesabınıza gidin ve **Işlem Otomasyonu**altında **işler** ' i seçin. **İşler** sayfasından, iş başarısızlıklarını görüntülemek için Runbook 'ınızdan işler ' i arayın.

### <a name="resolution"></a>Çözünürlük

Azure Otomasyonu 'nda VM 'Leri başlatmak ve durdurmak için [saatlerin dışında VM 'Leri Başlat/Durdur çözümünün](../automation-solution-vm-management.md) kullanılması önerilir. Bu çözüm Microsoft tarafından yazılmıştır. Özel runbook 'lar Microsoft tarafından desteklenmez. [Runbook sorun giderme](runbooks.md) makalesini ziyaret ederek özel runbook için bir çözüm bulabilirsiniz. Bu makalede, tüm türlerdeki runbook 'lar için genel rehberlik ve sorun giderme işlemleri sağlanmaktadır. Herhangi bir hata bulmak için [iş akışlarını](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) denetleyin. Portalda Otomasyon hesabınıza gidin ve **Işlem Otomasyonu**altında **işler** ' i seçin.

## <a name="dont-start-stop-in-sequence"></a>Senaryo: VM 'Ler doğru sırada başlamıyor veya durdurulmayacak

### <a name="issue"></a>Sorun

Çözümde yapılandırdığınız VM 'Ler doğru sırada başlatılmaz veya durduramaz.

### <a name="cause"></a>Nedeni

Bu, VM 'lerde hatalı etiketlemesinin oluşmasına neden olur.

### <a name="resolution"></a>Çözünürlük

Çözümün doğru yapılandırıldığından emin olmak için aşağıdaki adımları uygulayın.

1. Durumunuza bağlı olarak tüm sanal makinelerin başlatılmış veya durdurulmuş olduğundan emin olun `sequencestart` veya `sequencestop` etiketi vardır. Bu etiketlerin değeri olarak pozitif bir tamsayı olması gerekir. VM 'Ler, bu değere göre artan sırada işlenir.
2. Başlatılacak veya durdurulacak VM 'Ler için kaynak gruplarının, durumunuza bağlı olarak `External_Start_ResourceGroupNames` veya `External_Stop_ResourceGroupNames` değişkenlerinde olduğundan emin olun.
3. Değişikliklerinizi önizlemek için, `SequencedStartStop_Parent` runbook 'unu, WHATıF parametresi true olarak ayarlayarak test edin.

Daha ayrıntılı ve VM 'Leri sırayla başlatmak ve durdurmak için çözümün nasıl kullanılacağına ilişkin ek yönergeler için bkz. [sırasıyla VM 'Leri başlatma/durdurma](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="403"></a>Senaryo: başlatma/durdurma VM işi 403 yasaklanmış durumuyla başarısız oluyor

### <a name="issue"></a>Sorun

Çalışma saatleri dışında VM 'Leri Başlat/Durdur çözüm runbook 'ları için `403 forbidden` hatasıyla başarısız olan işleri bulabilirsiniz.

### <a name="cause"></a>Nedeni

Bu sorun, yanlış yapılandırılmış veya zaman aşımına uğramamış farklı çalıştır hesabından kaynaklanabilir. Ayrıca Otomasyon hesapları farklı çalıştır hesabı tarafından VM kaynaklarına yetersiz izinler nedeniyle olabilir.

### <a name="resolution"></a>Çözünürlük

Farklı Çalıştır hesabınızın doğru şekilde yapılandırıldığını denetlemek için Azure portal Otomasyon hesabınıza gidin ve **Hesap ayarları**altında **Farklı Çalıştır hesapları** ' nı seçin. Burada, farklı çalıştır hesapları yanlış yapılandırılmamışsa veya zaman aşımına erdiyse bu durumda farklı çalıştır hesaplarınızın durumu görüntülenir.

Farklı Çalıştır hesabınız [yanlış yapılandırılmış](../manage-runas-account.md#misconfiguration)Ise, farklı çalıştır hesabınızı silip yeniden oluşturmanız gerekir.

Farklı Çalıştır hesabınız için sertifikanın geçerliliği dolmuşsa, sertifikayı yenilemek için [otomatik olarak imzalanan sertifika yenilemesinde](../manage-runas-account.md#cert-renewal) listelenen adımları izleyin.

Soruna eksik izinler neden olmuş olabilir. Bir kaynaktaki izinleri nasıl denetleyeceğinizi öğrenmek için bkz. [hızlı başlangıç: Azure Portal kullanarak bir kullanıcıya atanan rolleri görüntüleme](../../role-based-access-control/check-access.md). Farklı Çalıştır hesabı tarafından kullanılan hizmet sorumlusu için uygulama kimliğini sağlamanız gerekir. Bu değeri, Azure portal Otomasyon hesabınıza giderek, **Hesap ayarları** altında **Farklı Çalıştır hesapları** ' nı seçerek ve uygun farklı Çalıştır hesabına tıklayarak elde edebilirsiniz.

## <a name="other"></a>Senaryo: sorunum yukarıda listelenmiyor

### <a name="issue"></a>Sorun

Bu sayfada listelenmeyen VM'leri çalışma saatleri dışında başlat/durdur çözümünü kullanırken bir sorun veya beklenmeyen bir sonuç yaşarsınız.

### <a name="cause"></a>Nedeni

Birçok kez hatanın nedeni çözümün eski ve güncel olmayan bir sürümü kullanılıyor olabilir.

> [!NOTE]
> VM'leri çalışma saatleri dışında başlat/durdur çözümü, çözümü dağıtırken Otomasyon hesabınıza içeri aktarılan Azure modülleri ile test edilmiştir. Çözüm şu anda Azure modülünün daha yeni sürümleriyle çalışmaz. Bu yalnızca VM'leri çalışma saatleri dışında başlat/durdur çözümünü çalıştırmak için kullandığınız Otomasyon hesabını etkiler. Azure [Otomasyonu 'nda Azure PowerShell modüllerini güncelleştirme](../automation-update-azure-modules.md) bölümünde açıklandığı gibi diğer otomasyon hesaplarınızda Azure modülünün daha yeni sürümlerini kullanmaya devam edebilirsiniz.

### <a name="resolution"></a>Çözünürlük

Birçok hatayı çözmek için çözümü kaldırmanız ve güncelleştirmeniz önerilir. Çözümü güncelleştirme hakkında bilgi edinmek için bkz. [saatlerde VM 'Leri Başlat/Durdur çözümünü güncelleştirme](../automation-solution-vm-management.md#update-the-solution). Ayrıca, herhangi bir hata aramak için [iş akışlarını](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) kontrol edebilirsiniz. Portalda Otomasyon hesabınıza gidin ve **Işlem Otomasyonu**altında **işler** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemediyseniz, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

* [Azure Forumları](https://azure.microsoft.com/support/forums/) aracılığıyla Azure uzmanlarından yanıtlar alın
* [@AzureSupport](https://twitter.com/azuresupport) hesabı ile bağlantı kurun. Bu resmi Microsoft Azure hesabı, müşteri deneyimini geliştirmek için Azure topluluğunu doğru kaynaklara ulaştırır: yanıtlar, destek ve uzmanlar.
* Daha fazla yardıma ihtiyacınız varsa, bir Azure destek olayı dosyası gönderebilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.
