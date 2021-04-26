---
title: Azure Otomasyonu VM'leri çalışma saatleri dışında başlat/durdur genel bakış
description: Bu makalede, bir zamanlamaya göre VM 'Leri başlatan veya durduran ve Azure Izleyici günlüklerinden bunları proaktif olarak izleyen VM'leri çalışma saatleri dışında başlat/durdur özelliği açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b28367aa242d5fab71dc5046ff6188c634883f03
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834525"
---
# <a name="startstop-vms-during-off-hours-overview"></a>VM'leri çalışma saatleri dışında başlat/durdur genel bakış

VM'leri çalışma saatleri dışında başlat/durdur özelliği, etkin Azure VM 'Leri başlatır veya sonlandırır. Kullanıcı tanımlı zamanlamalarda makineleri başlatır veya sonlandırır, Azure Izleyici günlükleri aracılığıyla öngörüler sağlar ve [eylem gruplarını](../azure-monitor/alerts/action-groups.md)kullanarak isteğe bağlı e-postalar gönderir. Bu özellik çoğu senaryo için hem Azure Resource Manager hem de klasik VM 'lerde etkinleştirilebilir.

Bu özellik, VM 'Leri başlatmak için [Start-AzVm](/powershell/module/az.compute/start-azvm) cmdlet 'ini kullanır. VM 'Leri durdurmak için [stop-AzVM](/powershell/module/az.compute/stop-azvm) kullanır.

> [!NOTE]
> Runbook 'lar yeni Azure az Module cmdlet 'lerini kullanacak şekilde güncelleştirildiğinden Azurere ön ek diğer adını kullanır.

> [!NOTE]
> VM'leri çalışma saatleri dışında başlat/durdur, mevcut Azure modüllerinin en yeni sürümlerini destekleyecek şekilde güncelleştirilmiştir. Bu özelliğin Market 'te bulunan güncelleştirilmiş sürümü azurere modüllerini desteklemez, azurerm'den az modüllere geçirdik.

Özelliği, VM maliyetlerini iyileştirmek isteyen kullanıcılar için merkezileşmemiş düşük maliyetli bir Otomasyon seçeneği sağlar. Özelliğini kullanarak şunları yapabilirsiniz:

- [VM 'leri başlatıp durdurulacak şekilde zamanlayın](automation-solution-vm-management-config.md#schedule).
- [Azure etiketlerini kullanarak](automation-solution-vm-management-config.md#tags)VM 'leri, artan sırada başlatılacak ve duracak şekilde zamanlayın. Bu etkinlik klasik VM 'Ler için desteklenmez.
- VM 'Leri [düşük CPU kullanımına](automation-solution-vm-management-config.md#cpuutil)göre oto durdur.

Geçerli özellikle ilgili sınırlamalar aşağıda verilmiştir:

- Herhangi bir bölgedeki VM 'Leri yönetir, ancak yalnızca Azure Otomasyonu hesabınızla aynı abonelikte kullanılabilir.
- Azure ve Azure Kamu 'da, bir Log Analytics çalışma alanını, bir Azure Otomasyonu hesabını ve uyarıları destekleyen her bölgede kullanılabilir. Azure Kamu bölgeleri Şu anda e-posta işlevlerini desteklememektedir.

> [!NOTE]
> Bu sürümü yüklemeden önce, şu anda önizleme aşamasında olan [sonraki sürümü](https://github.com/microsoft/startstopv2-deployments)öğrenmek istiyoruz.  Bu yeni sürüm (v2), bununla aynı işlevleri sunar, ancak Azure 'daki yeni teknolojiden faydalanmak için tasarlanmıştır. Tek bir Başlat/Durdur örneğinden çoklu abonelik desteği gibi müşterilerden gelen, yaygın olarak istenen özelliklerden bazılarını ekler.

## <a name="prerequisites"></a>Önkoşullar

- Çalışma saatleri dışında VM 'Leri Başlat/Durdur özelliği için Runbook 'lar bir [Azure farklı çalıştır hesabıyla](./automation-security-overview.md#run-as-accounts)çalışır. Bu farklı çalıştır hesabı, kullanım süresini dolacak veya sıklıkla değiştirebilen bir parola yerine sertifika kimlik doğrulaması kullandığından, tercih edilen kimlik doğrulama yöntemidir.

- Sorgu ve analiz etmek için runbook iş günlüklerini ve iş akışını depolayan bir çalışma alanında, bir [Azure izleyici Log Analytics çalışma alanı](../azure-monitor/logs/design-logs-deployment.md) . Otomasyon hesabı yeni veya mevcut bir Log Analytics çalışma alanına bağlanabilir ve her iki kaynağın de aynı kaynak grubunda olması gerekir.

VM'leri çalışma saatleri dışında başlat/durdur özelliği için etkin VM 'lerle çalışmak üzere ayrı bir Otomasyon hesabı kullanmanızı öneririz. Azure modül sürümleri sıklıkla yükseltilir ve parametreleri değişebilir. Özelliği aynı temposunda yükseltilmez ve kullandığı cmdlet 'lerin daha yeni sürümleriyle çalışmayabilir. Güncelleştirilmiş modülleri üretim Otomasyonu hesabınıza aktarmadan önce, herhangi bir uyumluluk sorunu olmadığını doğrulamak için bunları bir test Otomasyonu hesabına aktarmanıza önerilir.

## <a name="permissions"></a>İzinler

VM'leri çalışma saatleri dışında başlat/durdur özelliği için sanal makineleri etkinleştirmek üzere bazı izinleriniz olmalıdır. İzinlerin, özelliğin önceden oluşturulmuş bir Otomasyon hesabı ve Log Analytics çalışma alanı kullanıp kullanmadığını veya yeni bir hesap ve çalışma alanı oluşturup kullanmadığına bağlı olarak farklılık vardır.

Abonelik üzerinde bir katkıda bulunsanız ve Azure Active Directory (AD) kiracınızda genel yönetici varsa izinleri yapılandırmanız gerekmez. Bu haklara sahip değilseniz veya özel bir rol yapılandırmanız gerekmiyorsa, aşağıda açıklanan izinlere sahip olduğunuzdan emin olun.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Önceden var olan Otomasyon hesabı ve Log Analytics çalışma alanı için izinler

Mevcut bir Otomasyon hesabı ve Log Analytics çalışma alanı kullanarak VM'leri çalışma saatleri dışında başlat/durdur özelliği için VM 'Leri etkinleştirmek üzere, kaynak grubu kapsamında aşağıdaki izinlere sahip olmanız gerekir. Roller hakkında daha fazla bilgi için bkz. [Azure özel roller](../role-based-access-control/custom-roles.md).

| İzin | Kapsam|
| --- | --- |
| Microsoft. Automation/automationAccounts/Read | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/değişkenler/yazma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/zamanlamalar/yazma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/Runbook/yazma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/bağlantılar/yazma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/sertifikalar/yazma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/modüller/yazma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/modüller/okuma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/Jobzamanlamalar/yazma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/Jobs/Write | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/Jobs/okuma | Kaynak Grubu |
| Microsoft. OperationsManagement/Solutions/Write | Kaynak Grubu |
| Microsoft. Operationalınsights/çalışma alanları/* | Kaynak Grubu |
| Microsoft. Insights/diagnosticSettings/Write | Kaynak Grubu |
| Microsoft. Insights/ActionGroups/Write | Kaynak Grubu |
| Microsoft. Insights/ActionGroups/Read | Kaynak Grubu |
| Microsoft. resources/abonelikler/resourceGroups/Read | Kaynak Grubu |
| Microsoft. resources/dağıtımlar/* | Kaynak Grubu |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Yeni Otomasyon hesabı ve yeni Log Analytics çalışma alanı izinleri

Yeni bir Otomasyon hesabı ve Log Analytics çalışma alanı kullanarak VM'leri çalışma saatleri dışında başlat/durdur özelliği için sanal makineleri etkinleştirebilirsiniz. Bu durumda, önceki bölümde tanımlanan izinlere ve bu bölümde tanımlanan izinlere ihtiyacınız vardır. Ayrıca aşağıdaki rolleri de gereklidir:

- Abonelikte Co-Administrator. Klasik VM 'Leri yönetecekdeyseniz, bu rol klasik farklı çalıştır hesabını oluşturmak için gereklidir. [Klasik farklı çalıştır hesapları](automation-create-standalone-account.md#create-a-classic-run-as-account) artık varsayılan olarak oluşturulmaz.
- [Azure AD](../active-directory/roles/permissions-reference.md) uygulama geliştirici rolünde üyelik. Farklı Çalıştır hesaplarını yapılandırma hakkında daha fazla bilgi için bkz. [Farklı Çalıştır hesaplarını yapılandırma izinleri](automation-security-overview.md#permissions).
- Abonelik üzerinde veya aşağıdaki izinlerle katkıda bulunan.

| İzin |Kapsam|
| --- | --- |
| Microsoft. Authorization/Işlemler/okuma | Abonelik|
| Microsoft. Authorization/Permissions/Read |Abonelik|
| Microsoft. Authorization/Roleatamalar/okuma | Abonelik |
| Microsoft.Authorization/roleAssignments/write | Abonelik |
| Microsoft. Authorization/Roleatamaları/silme | Abonelik |
| Microsoft. Automation/automationAccounts/Connections/Read | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/sertifikalar/okuma | Kaynak Grubu |
| Microsoft. Automation/automationAccounts/Write | Kaynak Grubu |
| Microsoft. Operationalınsights/çalışma alanları/yazma | Kaynak Grubu |

## <a name="components"></a>Bileşenler

VM'leri çalışma saatleri dışında başlat/durdur özelliği, önceden yapılandırılmış runbook 'ları, zamanlamaları ve Azure Izleyici günlükleri ile tümleştirmeyi içerir. Bu öğeleri, sanal makinelerinizin başlangıç ve kapatılmasını iş gereksinimlerinize uyacak şekilde uyarlamak için kullanabilirsiniz.

### <a name="runbooks"></a>Runbook'lar

Aşağıdaki tabloda, özelliğin Otomasyon hesabınıza dağıttığı runbook 'lar listelenmektedir. Runbook kodunda değişiklik yapmayın. Bunun yerine, yeni işlevsellik için kendi runbook 'unuzu yazın.

> [!IMPORTANT]
> **Alt öğesi** adına eklenmiş herhangi bir runbook 'u doğrudan çalıştırmayın.

Tüm üst runbook 'lar `WhatIf` parametresi içerir. True olarak ayarlandığında parametresi, runbook 'un parametre olmadan çalıştırıldığında aldığı tam davranışı ayrıntılandıran şekilde destekler ve doğru VM 'Lerin hedeflendiğine doğrular. Bir runbook yalnızca `WhatIf` parametre false olarak ayarlandığında tanımlı eylemlerini gerçekleştirir.

|Runbook | Parametreler | Description|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Üst runbook 'tan çağırılır. Bu runbook otomatik durdurma senaryosu için kaynak temelinde uyarı oluşturur.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: true veya false  | Hedeflenen abonelik veya kaynak gruplarındaki VM 'lerde Azure uyarı kuralları oluşturur veya güncelleştirir. <br> `VMList` , örneğin, bir VM 'lerin virgülle ayrılmış listesidir (boşluk olmadan) `vm1,vm2,vm3` .<br> `WhatIf` yürütme olmadan runbook mantığının doğrulanmasına izin vermez.|
|AutoStop_Disable | Yok | Otomatik durdurma uyarılarını ve Varsayılan zamanlamayı devre dışı bırakır.|
|AutoStop_VM_Child | WebHookData | Üst runbook 'tan çağırılır. Uyarı kuralları, klasik bir sanal makineyi durdurmak için bu runbook 'u çağırır.|
|AutoStop_VM_Child_ARM | WebHookData |Üst runbook 'tan çağırılır. Uyarı kuralları bir sanal makineyi durdurmak için bu runbook 'u çağırır.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Eylem: Başlat veya Durdur<br> VMList  | Cloud Services tarafından klasik VM grubunda Eylem Başlat veya durdur işlemini gerçekleştirir. |
|ScheduledStartStop_Child | VMName <br> Eylem: Başlat veya Durdur <br> ResourceGroupName | Üst runbook 'tan çağırılır. Zamanlanan durdurma için bir Başlat veya Durdur eylemi yürütür.|
|ScheduledStartStop_Child_Classic | VMName<br> Eylem: Başlat veya Durdur<br> ResourceGroupName | Üst runbook 'tan çağırılır. Klasik VM 'Ler için zamanlanmış durdurma için Başlat veya Durdur eylemini yürütür. |
|ScheduledStartStop_Parent | Eylem: Başlat veya Durdur <br>VMList <br> WhatIf: true veya false | Abonelikteki tüm VM 'Leri başlatır veya sonlandırır. Değişkenleri düzenleyin `External_Start_ResourceGroupNames` ve `External_Stop_ResourceGroupNames` yalnızca bu hedeflenmiş kaynak gruplarında yürütülecek şekilde yürütün. Ayrıca, değişkeni güncelleştirerek belirli VM 'Leri hariç bırakabilirsiniz `External_ExcludeVMNames` .|
|SequencedStartStop_Parent | Eylem: Başlat veya Durdur <br> WhatIf: true veya false<br>VMList| Başlat/Durdur etkinliğini sıralamak istediğiniz her VM üzerinde **sequencestart** ve **sequencestop** adlı Etiketler oluşturur. Bu etiket adları büyük/küçük harfe duyarlıdır. Etiketinin değeri pozitif tamsayılar, örneğin, `1,2,3` başlatmak veya durdurmak istediğiniz sıraya karşılık gelen bir liste olmalıdır. <br>**Note**: VM 'ler `External_Start_ResourceGroupNames` ,, `External_Stop_ResourceGroupNames` ve değişkenlerinde tanımlanan kaynak grupları içinde olmalıdır `External_ExcludeVMNames` . Eylemlerin etkili olabilmesi için uygun etiketlere sahip olmaları gerekir.|

### <a name="variables"></a>Değişkenler

Aşağıdaki tabloda, Otomasyon hesabınızda oluşturulan değişkenler listelenmektedir. Yalnızca ile önekli değişkenleri değiştirin `External` . Ön eki olan değişkenlerin değiştirilmesi, `Internal` istenmeyen etkilere neden olur.

> [!NOTE]
> VM adı ve kaynak grubuyla ilgili sınırlamalar büyük ölçüde değişken boyutunun bir sonucudur. Bkz. [Azure Otomasyonu 'Nda değişken varlıkları](./shared-resources/variables.md).

|Değişken | Açıklama|
|---------|------------|
|External_AutoStop_Condition | Bir uyarıyı tetiklemeden önce koşulu yapılandırmak için gereken koşullu işleç. Kabul edilebilir değerler `GreaterThan` ,,, `GreaterThanOrEqual` `LessThan` ve `LessThanOrEqual` .|
|External_AutoStop_Description | CPU yüzdesi eşiği aşarsa VM 'yi durdurma uyarısı.|
|External_AutoStop_Frequency | Kural için değerlendirme sıklığı. Bu parametre, girişi TimeSpan biçiminde kabul eder. Olası değerler 5 dakikadan 6 saate kadar sürer. |
|External_AutoStop_MetricName | Azure uyarı kuralının yapılandırıldığı performans ölçümünün adı.|
|External_AutoStop_Severity | Ölçüm uyarısının önem derecesi, 0 ile 4 arasında değişebilir. |
|External_AutoStop_Threshold | Değişkende belirtilen Azure uyarı kuralının eşiği `External_AutoStop_MetricName` . Yüzde değerleri 1 ile 100 arasındadır.|
|External_AutoStop_TimeAggregationOperator | Koşulu değerlendirmek için seçilen pencere boyutuna uygulanan zaman toplama işleci. Kabul edilebilir değerler `Average` ,,, `Minimum` `Maximum` `Total` ve `Last` .|
|External_AutoStop_TimeWindow | Azure 'un bir uyarı tetiklenmesi için seçili ölçümleri analiz eden pencerenin boyutu. Bu parametre, girişi TimeSpan biçiminde kabul eder. Olası değerler 5 dakikadan 6 saate kadar sürer.|
|External_EnableClassicVMs| Klasik VM 'Lerin özelliğin hedeflediği değeri belirten değer. Varsayılan değer true 'dur. Azure bulut çözümü sağlayıcısı (CSP) abonelikleri için bu değişkeni false olarak ayarlayın. Klasik VM 'Ler, [Klasik farklı çalıştır hesabı](automation-create-standalone-account.md#create-a-classic-run-as-account)gerektirir.|
|External_ExcludeVMNames | 140 VM 'Lerle sınırlı tutulacak VM adlarının virgülle ayrılmış listesi. Listeye 140 'den fazla VM eklerseniz, dışlama için belirtilen VM 'Ler istenmeden başlatılabilir veya durmuş olabilir.|
|External_Start_ResourceGroupNames | Başlatma eylemleri için hedeflenen bir veya daha fazla kaynak grubunun virgülle ayrılmış listesi.|
|External_Stop_ResourceGroupNames | Durdurma eylemlerini hedefleyen bir veya daha fazla kaynak grubunun virgülle ayrılmış listesi.|
|External_WaitTimeForVMRetrySeconds |**SequencedStartStop_Parent** runbook 'un VM 'lerde gerçekleştirilmesi için saniye cinsinden bekleme süresi. Bu değişken, runbook 'un bir sonraki eylemle devam etmeden önce belirtilen saniye sayısı için alt işlemleri beklemesine izin verir. En uzun bekleme süresi 10800 veya üç saattir. Varsayılan değer 2100 saniyedir.|
|Internal_AutomationAccountName | Otomasyon hesabının adını belirtir.|
|Internal_AutoSnooze_ARM_WebhookURI | VM 'Ler için oto durdurma senaryosuna çağrılan Web kancası URI 'SI.|
|Internal_AutoSnooze_WebhookUri | Klasik VM 'Ler için oto durdurma senaryosu adlı Web kancası URI 'SI.|
|Internal_AzureSubscriptionId | Azure abonelik KIMLIĞI.|
|Internal_ResourceGroupName | Otomasyon hesabı kaynak grubu adı.|

>[!NOTE]
>Değişken için `External_WaitTimeForVMRetryInSeconds` , varsayılan değer 600 ' den 2100 ' e güncelleştirilmiştir.

Tüm senaryolarda, ve değişkenleri, `External_Start_ResourceGroupNames`  `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** ve **ScheduledStartStop_Parent** runbook 'Ları Için virgülle ayrılmış VM listeleri hariç olmak üzere VM 'leri hedeflemek için gereklidir. Diğer bir deyişle, sanal makinelerinizin başlatma ve durdurma eylemlerinin gerçekleşmesi için hedef kaynak gruplarına ait olması gerekir. Mantığı Azure Ilkesine benzer şekilde çalışarak abonelik veya kaynak grubunu hedefleyebilir ve yeni oluşturulan VM 'Ler tarafından devralınan eylemlere sahip olabilirsiniz. Bu yaklaşım her VM için ayrı bir zamanlamanın korunmasını, sonra da her bir sanal makine için ayrı bir zamanlama olmasını önler.

### <a name="schedules"></a>Zamanlamalar

Aşağıdaki tabloda, Otomasyon hesabınızda oluşturulan varsayılan zamanlamaların her biri listelenmektedir. Bunları değiştirebilir veya kendi özel zamanlamalarınızı oluşturabilirsiniz. Varsayılan olarak, **Scheduled_StartVM** ve **Scheduled_StopVM** zamanlamaları hariç tüm zamanlamalar devre dışı bırakılır.

Tüm zamanlamaları etkinleştirmeyin, çünkü bu durum çakışan zamanlama eylemleri oluşturabilir. En iyi yöntem, hangi iyileştirmelerin yapmak istediğinizi belirlemektir ve bunları uygun şekilde değiştirebilirsiniz. Daha fazla açıklama için genel bakış bölümündeki örnek senaryolar bölümüne bakın.

|Zamanlama adı | Sıklık | Description|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8 saatte bir | **AutoStop_CreateAlert_Parent** runbook 'u her 8 saatte bir çalıştırır ve bu da, `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` ve değişkenlerinde VM tabanlı değerleri de durduruyor `External_ExcludeVMNames` . Alternatif olarak, parametresini kullanarak, VM 'lerin virgülle ayrılmış bir listesini belirtebilirsiniz `VMList` .|
|Scheduled_StopVM | Kullanıcı tanımlı, günlük | **ScheduledStopStart_Parent** runbook 'u `Stop` her gün belirtilen zamanda bir parametre ile çalıştırır. , Değişken varlıklar tarafından tanımlanan kuralları karşılayan tüm VM 'Leri otomatik olarak sonlandırır. **Zamanlanan-StartVM** ilgili zamanlamasını etkinleştirin.|
|Scheduled_StartVM | Kullanıcı tanımlı, günlük | **ScheduledStopStart_Parent** runbook 'u, `Start` belirtilen saatte her gün bir parametre değeri ile çalıştırır. , Değişken varlıklar tarafından tanımlanan kuralları karşılayan tüm VM 'Leri otomatik olarak başlatır. İlgili zamanlamayı **Zamanlanmış-StopVM**' i etkinleştirin.|
|Sequenced-StopVM | 1:00 (UTC), her Cuma | **Sequenced_StopStop_Parent** runbook 'U her Cuma için belirtilen zamanda bir parametre değeri ile çalıştırır `Stop` . Sıralı (artan), uygun değişkenler tarafından tanımlanan bir **SequenceStop** etiketine sahip tüm VM 'leri sonlandırır. Etiket değerleri ve varlık değişkenleri hakkında daha fazla bilgi için bkz. [runbook 'lar](#runbooks). **Sıralı-StartVM** ilgili zamanlamasını etkinleştirin.|
|Sequenced-StartVM | 1:00 PM (UTC), her Pazartesi | **SequencedStopStart_Parent** runbook 'U her Pazartesi için belirtilen zamanda bir parametre değeri ile çalıştırır `Start` . Sıralı (azalan), uygun değişkenlerle tanımlanan **Sequencestart** etiketiyle tüm VM 'leri başlatır. Etiket değerleri ve değişken varlıklar hakkında daha fazla bilgi için bkz. [runbook 'lar](#runbooks). İlgili zamanlamayı, **sıralı-StopVM**'yi etkinleştirin.|

## <a name="use-the-feature-with-classic-vms"></a>Klasik VM 'lerle özelliği kullanma

Klasik VM 'Ler için VM'leri çalışma saatleri dışında başlat/durdur özelliğini kullanıyorsanız, Otomasyon tüm VM 'lerinizi bulut hizmeti başına sırayla işler. VM 'Ler, farklı bulut hizmetlerinde paralel olarak hala işlenir. 

Özelliği klasik VM 'lerle birlikte kullanmak için, varsayılan olarak oluşturulmamış bir klasik Farklı Çalıştır hesabına ihtiyacınız vardır. Klasik farklı çalıştır hesabı oluşturma yönergeleri için bkz. [Klasik farklı çalıştır hesabı oluşturma](automation-create-standalone-account.md#create-a-classic-run-as-account).

Bulut hizmeti başına 20 ' den fazla VM varsa, bazı öneriler aşağıda verilmiştir:

* Üst runbook **ScheduledStartStop_Parent** birden çok zamanlama oluşturun ve zamanlamaya göre 20 VM 'yi belirtin.
* Zamanlama özelliklerinde, `VMList` VM adlarını virgülle ayrılmış bir liste olarak belirtmek için parametresini kullanın (boşluk yok).

Aksi takdirde, bu özelliğin Otomasyon işi üçten fazla saat çalışırsa, bu, dengeli olarak kaldırılmış veya [dengeli](automation-runbook-execution.md#fair-share) bir şekilde kaldırılır.

Azure CSP abonelikleri yalnızca Azure Resource Manager modelini destekler. Azure Resource Manager olmayan hizmetler programda yok. VM'leri çalışma saatleri dışında başlat/durdur özelliği çalıştırıldığında, klasik kaynakları yönetmek için cmdlet 'ler olduğundan hatalar alabilirsiniz. CSP hakkında daha fazla bilgi edinmek için bkz. [CSP aboneliklerinde kullanılabilir hizmetler](/azure/cloud-solution-provider/overview/azure-csp-available-services). CSP aboneliği kullanıyorsanız, dağıtımdan sonra [External_EnableClassicVMs](#variables) değişkenini false olarak ayarlamanız gerekir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-the-feature"></a>Özelliği görüntüleme

Etkin özelliğe erişmek için aşağıdaki mekanizmalardan birini kullanın:

* Otomasyon hesabınızdan **Ilgili kaynaklar** altında **sanal makineyi Başlat/Durdur** ' u seçin. VM 'yi Başlat/Durdur sayfasında, **sanal makine çözümlerini Başlat/Durdur** altında **çözümü Yönet** ' i seçin.

* Otomasyon hesabınıza bağlı Log Analytics çalışma alanına gidin. Çalışma alanını seçtikten sonra sol bölmeden **çözümler** ' i seçin. Çözümler sayfasında, listeden **Başlat-Durdur-VM [çalışma alanı]** öğesini seçin.  

Özelliği seçmek **Start-Stop-VM [çalışma alanı]** sayfasını görüntüler. Burada, **Startstopvm** kutucuğundaki bilgiler gibi önemli ayrıntıları gözden geçirebilirsiniz. Log Analytics çalışma alanınızda olduğu gibi, bu kutucuk, başlatılan ve başarıyla tamamlanmış olan özellik için Runbook işlerinin bir sayısını ve grafik temsilini görüntüler.

![Otomasyon Güncelleştirme Yönetimi sayfası](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Halka kutucuğuna tıklayarak iş kayıtlarının daha fazla analizini yapabilirsiniz. Panoda iş geçmişi ve önceden tanımlanmış günlük arama sorguları gösterilmektedir. Arama sorgularınıza göre arama yapmak için Log Analytics gelişmiş portala geçiş yapın.

## <a name="update-the-feature"></a>Özelliği güncelleştirme

VM'leri çalışma saatleri dışında başlat/durdur önceki bir sürümünü dağıttıysanız, güncelleştirilmiş bir yayını dağıtmadan önce hesabınızı hesabınızdan silin. [Özelliği kaldırmak](automation-solution-vm-management-remove.md#delete-the-feature) için adımları izleyin ve sonra [etkinleştirmek](automation-solution-vm-management-enable.md)için adımları uygulayın.

## <a name="next-steps"></a>Sonraki adımlar

Ortamınızdaki VM 'lerde özelliği etkinleştirmek için bkz. [enable VM'leri çalışma saatleri dışında Başlat/Durdur](automation-solution-vm-management-enable.md).
