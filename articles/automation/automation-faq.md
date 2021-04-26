---
title: Azure Otomasyonu SSS | Microsoft Docs
description: Bu makalede, Azure Otomasyonu hakkında sık sorulan sorulara yanıtlar verilmektedir.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9ab4ce9b8691f27fe392d2e3099677d45900d2e3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834237"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Otomasyonu sık sorulan sorular

Bu Microsoft SSS, Azure Otomasyonu hakkında sık sorulan soruların bir listesidir. Özellikleri hakkında başka sorularınız varsa, tartışma forumuna gidin ve sorularınızı gönderin. Bir soru sıkça sorulduğunda, hızlı ve kolay bir şekilde bulunması için bu makaleye ekleyeceğiz.

## <a name="update-management"></a>Güncelleştirme Yönetimi

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Beklenmeyen işletim sistemi düzeyinde yükseltmelere engel olabilir miyim?

Red Hat Enterprise Linux gibi bazı Linux değişkenlerde, işletim sistemi düzeyinde yükseltmeler paketler aracılığıyla gerçekleşebilir. Bu, işletim sistemi sürümü numarasının değiştiği Güncelleştirme Yönetimi çalışmasına yol açabilir. Güncelleştirme Yönetimi, bir yöneticinin bir Linux makinesinde yerel olarak kullandığı paketleri güncelleştirmek için aynı yöntemleri kullandığından, bu davranış bilerek yapılır.

İşletim sistemi sürümünün Güncelleştirme Yönetimi dağıtımlar aracılığıyla güncelleştirilmesini önlemek için **dışlama** özelliğini kullanın.

Red Hat Enterprise Linux, hariç tutulacak paket adı `redhat-release-server.x86_64` .

### <a name="why-arent-criticalsecurity-updates-applied"></a>Kritik/güvenlik güncelleştirmeleri neden uygulanmadı?

Güncelleştirmeleri bir Linux makinesine dağıtırken, güncelleştirme sınıflandırmalarını seçebilirsiniz. Bu seçenek, belirtilen ölçütlere uyan güncelleştirmeleri filtreler. Bu filtre, güncelleştirme dağıtıldığında makinede yerel olarak uygulanır.

Güncelleştirme Yönetimi bulutta güncelleştirme zenginleştirmesini gerçekleştirdiğinden, yerel makinede bu bilgilere sahip olmasa da, Güncelleştirme Yönetimi bir güvenlik etkisi olan bazı güncelleştirmelere bayrak atayabilirsiniz. Kritik güncelleştirmeleri bir Linux makinesine uygularsanız, bu makinede güvenlik etkisi varmış gibi işaretlenmemiş güncelleştirmeler olabilir ve bu nedenle uygulanmaz. Ancak, Güncelleştirme Yönetimi ilgili güncelleştirme hakkında ek bilgi içerdiğinden, bu makineyi hala uyumsuz olarak raporlayabilir.

Güncelleştirme sınıflandırmasına göre güncelleştirmelerin dağıtımı, CentOS 'in RTM sürümlerinde çalışmaz. CentOS güncelleştirmelerini doğru bir şekilde dağıtmak için, güncelleştirmelerin uygulandığından emin olmak için tüm sınıflandırmalar ' ı seçin. SUSE için, sınıflandırma olarak yalnızca **diğer güncelleştirmelerin** seçilmesi, zypper (Paket Yöneticisi) ile ilişkili olmaları durumunda başka bazı güvenlik güncelleştirmelerini de yükleyebilir veya öncelikle bağımlılıkları zorunludur. Bu davranış, zypper kısıtlamasıdır. Bazı durumlarda, güncelleştirme dağıtımını yeniden çalıştırmanız ve ardından güncelleştirme günlüğünden dağıtımı doğrulamanız gerekebilir.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Güncelleştirmeleri Azure kiracılarının tamamında dağıtabilir miyim?

Güncelleştirme Yönetimi başka bir Azure kiracı raporlamada düzeltme eki gerektiren makineleriniz varsa, bunları zamanlanmak için aşağıdaki geçici çözümü kullanmanız gerekir. [Yeni-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) cmdlet 'ini `ForUpdateConfiguration` bir zamanlama oluşturmak için belirtilen parametreyle birlikte kullanabilirsiniz. [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) cmdlet 'ini kullanabilir ve diğer Kiracıdaki makineleri `NonAzureComputer` parametresine geçirebilirsiniz. Aşağıdaki örnek bunun nasıl yapılacağını göstermektedir.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>İşlem otomasyonu-Python runbook 'ları

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Azure Otomasyonu 'nda hangi Python 3 sürümü destekleniyor?

Bulut işleri için Python 3,8 desteklenir. Kod farklı sürümler arasında uyumluysa 3. x sürümünden oluşan betikler ve paketler çalışabilir.

Windows karma runbook çalışanları üzerindeki karma işler için, kullanmak istediğiniz 3. x sürümünü yüklemeyi seçebilirsiniz. Linux hibrit Runbook Worker 'lar üzerinde karma işler için, DSC OMSConfig ve Linux karma çalışanını çalıştırmak üzere makinede yüklü Python 3 sürümüne bağımlıdır. Sürüm 3,6 ' i yüklemenizi öneririz; Ancak, Python 3 ' ün sürümleri arasındaki yöntem imzalarında veya sözleşmelerinde hiçbir değişiklik yoksa farklı sürümler de çalışır.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Python 2 ve Python 3 runbook 'ları aynı Otomasyon hesabında çalışıyor mu?

Evet, Python 2 ve Python 3 runbook 'ların aynı Otomasyon hesabında kullanılması için bir sınırlama yoktur.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Mevcut Python 2 runbook 'larını ve paketlerini Python 3 ' e geçirme planı nedir?

Azure Otomasyonu, Python 2 runbook 'larını ve paketlerini Python 3 ' e geçirmeyi planlamaz. Bu geçişi kendiniz gerçekleştirmeniz gerekecektir. Mevcut ve yeni Python 2 runbook 'ları ve paketleri çalışmaya devam edecektir.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Python 3 ortamında varsayılan olarak desteklenen paketler nelerdir?

Azure Package 4.0.0, Python 3 Otomasyon ortamında varsayılan olarak yüklenir. Varsayılan sürümü geçersiz kılmak için Azure paketinin daha yüksek bir sürümünü elle içeri aktarabilirsiniz.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>Python 2 paketine başvuran bir Python 3 runbook çalıştırdığımda veya bunun tersini yaparsanız ne olur?

Python 2 ve Python 3 farklı yürütme ortamlarına sahiptir. Python 2 runbook 'u çalışırken, Python 3 için yalnızca Python 2 paketleri içeri aktarılabilir ve benzerdir.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Python 2 ile Python 3 runbook 'ları ve paketleri birbirinden ayırt Nasıl yaparım?.

Python 3, Python 2 ve Python 3 runbook 'larını ayırt eden yeni bir runbook tanımıdır. Benzer şekilde, Python 3 paketleri için başka bir paket türü de sunulmuştur.

## <a name="next-steps"></a>Sonraki adımlar

Sorunuz burada yanıtlanmazsa, daha fazla soru ve yanıt için aşağıdaki kaynaklara başvurabilirsiniz.

- [Azure Otomasyonu](/answers/topics/azure-automation.html)
- [Geri Bildirim forumu](https://feedback.azure.com/forums/905242-update-management)
