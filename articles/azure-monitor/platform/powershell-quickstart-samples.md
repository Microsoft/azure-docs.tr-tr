---
title: Azure Izleyici PowerShell hızlı başlangıç örnekleri
description: Otomatik ölçeklendirme, uyarılar, Web kancaları ve etkinlik günlükleri arama gibi Azure Izleyici özelliklerine erişmek için PowerShell 'i kullanın.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 2/14/2018
ms.openlocfilehash: d1aa4b4e2d72f10ca73616bc7e69b0d02f13a501
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72551843"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Azure Izleyici PowerShell hızlı başlangıç örnekleri
Bu makalede, Azure Izleyici özelliklerine erişmenize yardımcı olacak örnek PowerShell komutları gösterilmektedir.

> [!NOTE]
> Azure Izleyici, 25 Eylül 2016 ' e kadar "Azure Insights" adı verilen yeni addır. Ancak, ad alanları ve bu nedenle aşağıdaki komutlar yine de "Öngörüler" sözcüğünü içerir.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>PowerShell 'i ayarlama
Henüz yapmadıysanız, PowerShell 'i bilgisayarınızda çalışacak şekilde ayarlayın. Daha fazla bilgi için bkz. [PowerShell 'ı yüklemek ve yapılandırmak](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Bu makaledeki örnekler
Makalesindeki örneklerde Azure Izleyici cmdlet 'lerini nasıl kullanabileceğiniz gösterilmektedir. Ayrıca Azure izleyici [(Öngörüler) cmdlet 'Lerinde](https://docs.microsoft.com/powershell/module/az.applicationinsights)Azure izleyici PowerShell cmdlet 'lerinin tüm listesini inceleyebilirsiniz.

## <a name="sign-in-and-use-subscriptions"></a>Oturum açın ve abonelikleri kullanın
İlk olarak, Azure aboneliğinizde oturum açın.

```powershell
Connect-AzAccount
```

Bir oturum açma ekranı görürsünüz. Hesabınızda oturum açtıktan sonra Tenantıd ve varsayılan abonelik KIMLIĞI görüntülenir. Tüm Azure cmdlet 'leri varsayılan aboneliğiniz bağlamında çalışır. Erişiminiz olan aboneliklerin listesini görüntülemek için aşağıdaki komutu kullanın:

```powershell
Get-AzSubscription
```

Çalışma bağlamını görmek için (komutlarınızın hangi abonelikle çalıştırılmakta olduğunu), aşağıdaki komutu kullanın:

```powershell
Get-AzContext
```
Çalışma bağlamını farklı bir aboneliğe dönüştürmek için aşağıdaki komutu kullanın:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Abonelik için etkinlik günlüğünü alma
[Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) cmdlet 'ini kullanın.  Yaygın olarak kullanılan bazı örnekler aşağıda verilmiştir. Etkinlik günlüğü, son 90 gün işlem içerir. Bu saatten önceki tarihleri kullanmak bir hata mesajı elde ediyor.  

Aşağıdaki komutlarda ne zaman kullanılacağını doğrulamak için geçerli tarih/saat ne olduğunu öğrenin:
```powershell
Get-Date
```

Bu saatten/tarihten itibaren günlük girişlerini al:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Bir zaman/tarih aralığı arasındaki günlük girişlerini al:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Belirli bir kaynak grubundaki günlük girdilerini al:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Belirli bir kaynak sağlayıcısından bir zaman/tarih aralığı arasında günlük girişleri al:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Belirli bir çağıran tüm günlük girişlerini al:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Aşağıdaki komut, etkinlik günlüğünden son 1000 olayı alır:

```powershell
Get-AzLog -MaxRecord 10
```

`Get-AzLog` diğer birçok parametreyi destekler. Daha fazla bilgi için `Get-AzLog` başvurusuna bakın.

> [!NOTE]
> `Get-AzLog` yalnızca 15 gün geçmiş bir geçmişi sağlar. **-MaxRecords** parametresini kullanmak, son N olayı, 15 günden daha fazla sorgulamanızı sağlar. 15 günden eski olaylara erişmek için REST API veya SDK 'yı kullanın (C# Örneğin, SDK 'yı kullanarak). **StartTime**'i eklemezseniz, varsayılan değer **bitişsaati** eksi bir saattir. **Bitişsaati**eklemezseniz, varsayılan değer geçerli süredir. Her zaman UTC 'de.
> 
> 

## <a name="retrieve-alerts-history"></a>Uyarı geçmişini al
Tüm uyarı olaylarını görüntülemek için aşağıdaki örnekleri kullanarak Azure Resource Manager günlüklerini sorgulayabilirsiniz.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Belirli bir uyarı kuralının geçmişini görüntülemek için, uyarı kuralının kaynak KIMLIĞI ' ni geçirerek `Get-AzAlertHistory` cmdlet 'ini kullanabilirsiniz.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

@No__t_0 cmdlet 'i çeşitli parametreleri destekler. Daha fazla bilgi için bkz. [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Uyarı kuralları hakkında bilgi alma
Aşağıdaki komutların tümü, "montest" adlı bir kaynak grubu üzerinde çalışır.

Uyarı kuralının tüm özelliklerini görüntüleyin:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Bir kaynak grubundaki tüm uyarıları al:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

Hedef kaynak için ayarlanan tüm uyarı kurallarını alın. Örneğin, bir VM 'de ayarlanan tüm uyarı kuralları.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` diğer parametreleri destekler. Daha fazla bilgi için bkz. [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) .

## <a name="create-metric-alerts"></a>Ölçüm uyarıları oluşturma
Bir uyarı kuralı oluşturmak, güncelleştirmek veya devre dışı bırakmak için `Add-AlertRule` cmdlet 'ini kullanabilirsiniz.

Sırasıyla `New-AzAlertRuleEmail` ve `New-AzAlertRuleWebhook` kullanarak e-posta ve Web kancası özellikleri oluşturabilirsiniz. Uyarı kuralı cmdlet 'inde, bu özellikleri uyarı kuralının **Eylemler** özelliğine eylem olarak atayın.

Aşağıdaki tabloda, ölçüm kullanılarak bir uyarı oluşturmak için kullanılan parametreler ve değerler açıklanmaktadır.

| parametresinin | değer |
| --- | --- |
| Adı |simpletestdiskwrite |
| Bu uyarı kuralının konumu |Doğu ABD |
| adlı yönetilen örnek, |montest |
| Targetresourceıd |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| Oluşturulan uyarının MetricName |\Fiziksel disk (_Total) \Disk yazma/sn. Tam ölçüm adlarını alma hakkında `Get-MetricDefinitions` cmdlet 'ini inceleyin |
| işlecinde |GreaterThan |
| Eşik değeri (Bu ölçümün içindeki Count/SEC) |1 |
| WindowSize (SS: DD: ss biçimi) |00:05:00 |
| toplayıcı (Bu örnekte ortalama sayı kullanan ölçüm istatistiği) |Ortalama |
| özel e-postalar (dize dizisi) |' foo@example.com ', ' bar@example.com ' |
| sahipler, katkıda bulunanlar ve okuyucular için e-posta gönderin |-SendToServiceOwners |

E-posta eylemi oluşturma

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Web kancası eylemi oluşturma

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Klasik bir VM 'de CPU% Ölçümü üzerinde uyarı kuralı oluştur

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Uyarı kuralını alma

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Verilen özellikler için zaten bir uyarı kuralı varsa, uyarı Ekle cmdlet 'i de kuralı güncelleştirir. Bir uyarı kuralını devre dışı bırakmak için **-DisableRule**parametresini ekleyin.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Uyarılar için kullanılabilir ölçümlerin bir listesini alın
Belirli bir kaynağın tüm ölçümlerinin listesini görüntülemek için `Get-AzMetricDefinition` cmdlet 'ini kullanabilirsiniz.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

Aşağıdaki örnek, için ölçüm adı ve birim içeren bir tablo oluşturur.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

@No__t_0 için kullanılabilir seçeneklerin tam listesi [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx)' de bulunabilir.

## <a name="create-and-manage-activity-log-alerts"></a>Etkinlik günlüğü uyarıları oluşturma ve yönetme
Bir etkinlik günlüğü uyarısı ayarlamak için `Set-AzActivityLogAlert` cmdlet 'ini kullanabilirsiniz. Bir etkinlik günlüğü uyarısı, koşullarınızı öncelikle koşulların bir sözlüğü olarak tanımlamanızı ve sonra bu koşulları kullanan bir uyarı oluşturmanızı gerektirir.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

Ek Web kancası özellikleri isteğe bağlıdır. @No__t_0 kullanarak bir etkinlik günlüğü uyarısının içeriğini geri alabilirsiniz.

## <a name="create-and-manage-autoscale-settings"></a>Otomatik ölçeklendirme ayarları oluşturma ve yönetme
Bir kaynak (Web uygulaması, VM, bulut hizmeti veya sanal makine ölçek kümesi) için yapılandırılmış yalnızca bir otomatik ölçeklendirme ayarı olabilir.
Ancak, her otomatik ölçeklendirme ayarının birden çok profili olabilir. Örneğin, bir performans tabanlı ölçek profili için bir tane ve bir zamanlama tabanlı profil için ikinci bir tane. Her profilde, üzerinde birden fazla kural yapılandırılabilir. Otomatik ölçeklendirme hakkında daha fazla bilgi için bkz. [bir uygulamayı otomatik ölçeklendirme](../../cloud-services/cloud-services-how-to-scale-portal.md).

Kullanılacak adımlar şunlardır:

1. Kural oluştur.
2. Daha önce oluşturduğunuz kuralları profillerde eşleyerek profil oluşturun.
3. İsteğe bağlı: Web kancasını ve e-posta özelliklerini yapılandırarak otomatik ölçeklendirme bildirimleri oluşturun.
4. Önceki adımlarda oluşturduğunuz profilleri ve bildirimleri eşleyerek hedef kaynakta bir ada sahip bir otomatik ölçeklendirme ayarı oluşturun.

Aşağıdaki örneklerde, CPU kullanım ölçümünü kullanarak bir Windows işletim sistemi için bir sanal makine ölçek kümesi için otomatik ölçeklendirme ayarını nasıl oluşturacağınız gösterilmektedir.

İlk olarak, bir örnek sayısı artmış olacak şekilde genişletmek için bir kural oluşturun.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Sonra, örnek sayısı azalmasını içeren, ölçeklendirmek için bir kural oluşturun.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Ardından, kurallar için bir profil oluşturun.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Web kancası özelliği oluştur.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Daha önce oluşturduğunuz e-posta ve Web kancası dahil, otomatik ölçeklendirme ayarı için bildirim özelliğini oluşturun.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Son olarak, daha önce oluşturduğunuz profili eklemek için otomatik ölçeklendirme ayarını oluşturun. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Otomatik ölçeklendirme ayarlarını yönetme hakkında daha fazla bilgi için bkz. [Get-otomatik Scalesetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Otomatik ölçeklendirme geçmişi
Aşağıdaki örnek, son otomatik ölçeklendirme ve uyarı olaylarını nasıl görüntüleyekullanabileceğinizi gösterir. Otomatik ölçeklendirme geçmişini görüntülemek için etkinlik günlüğü aramasını kullanın.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Otomatik ölçeklendirme geçmişini almak için `Get-AzAutoScaleHistory` cmdlet 'ini kullanabilirsiniz.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Daha fazla bilgi için bkz. [Get-oto Scalehistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Otomatik ölçeklendirme ayarının ayrıntılarını görüntüleme
Otomatik ölçeklendirme ayarı hakkında daha fazla bilgi almak için `Get-Autoscalesetting` cmdlet 'ini kullanabilirsiniz.

Aşağıdaki örnek, ' myrg1 ' kaynak grubundaki tüm otomatik ölçeklendirme ayarları hakkındaki ayrıntıları gösterir.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

Aşağıdaki örnek, ' myrg1 ' kaynak grubundaki tüm otomatik ölçeklendirme ayarları ve özellikle ' hayal Calevmsssetting ' adlı otomatik ölçeklendirme ayarı hakkındaki ayrıntıları gösterir.

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Otomatik ölçeklendirme ayarını kaldırma
Bir otomatik ölçeklendirme ayarını silmek için `Remove-Autoscalesetting` cmdlet 'ini kullanabilirsiniz.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Etkinlik günlüğü için günlük profillerini yönetme
Bir *günlük profili* oluşturabilir ve etkinlik günlüğünden verileri bir depolama hesabına aktarabilir ve veri saklama alanını yapılandırabilirsiniz. İsteğe bağlı olarak, verileri olay hub 'ınıza da akışla aktarabilirsiniz. Bu özellik şu anda önizleme aşamasındadır ve her abonelik için yalnızca bir günlük profili oluşturabilirsiniz. Günlük profillerini oluşturmak ve yönetmek için şu cmdlet 'leri geçerli aboneliğiniz ile birlikte kullanabilirsiniz. Ayrıca, belirli bir aboneliği seçebilirsiniz. PowerShell varsayılan olarak geçerli aboneliğe izin verse de, `Set-AzContext` kullanarak her zaman değiştirebilirsiniz. Bu abonelik içindeki herhangi bir depolama hesabına veya Olay Hub 'ına veri yönlendirmek için etkinlik günlüğü yapılandırabilirsiniz. Veriler blob dosyası olarak JSON biçiminde yazılır.

### <a name="get-a-log-profile"></a>Günlük profili al
Mevcut günlük profillerinizi getirmek için `Get-AzLogProfile` cmdlet 'ini kullanın.

### <a name="add-a-log-profile-without-data-retention"></a>Veri saklama olmadan bir günlük profili ekleme
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Günlük profilini kaldırma
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Veri saklama ile bir günlük profili ekleme
**-Retentionındays** özelliğini, verilerin korunduğu pozitif bir tamsayı olarak gün sayısıyla belirtebilirsiniz.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Bekletme ve EventHub ile günlük profili ekleme
Verilerinizi depolama hesabına yönlendirmeye ek olarak, bir olay hub 'ına da akış gönderebilirsiniz. Bu önizleme sürümünde depolama hesabı yapılandırması zorunludur, ancak olay hub 'ı yapılandırması isteğe bağlıdır.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Tanılama günlüklerini yapılandırma
Birçok Azure hizmeti, aşağıdakilerden birini veya birkaçını yapabiliriz ek Günlükler ve telemetri sağlar: 
 - verileri Azure depolama hesabınıza kaydetmek üzere yapılandırılmış olmalıdır
 - Event Hubs gönderildi
 - Log Analytics çalışma alanına gönderilir. 

İşlem yalnızca bir kaynak düzeyinde gerçekleştirilebilir. Depolama hesabı veya Olay Hub 'ı, tanılama ayarının yapılandırıldığı hedef kaynakla aynı bölgede bulunmalıdır.

### <a name="get-diagnostic-setting"></a>Tanılama ayarını al
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Tanılama ayarını devre dışı bırak

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Tanılama ayarını bekletme olmadan etkinleştir

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Tanılama ayarını bekletme ile etkinleştir

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Belirli bir günlük kategorisi için bekletme ile tanılama ayarını etkinleştir

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Event Hubs için tanılama ayarını etkinleştir

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Log Analytics için tanılama ayarını etkinleştir

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Çalışma alanı kimliği özelliğinin, çalışma alanının *kaynak kimliğini* aldığını unutmayın. Aşağıdaki komutu kullanarak Log Analytics çalışma alanınızın kaynak KIMLIĞINI elde edebilirsiniz:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Bu komutlar, birden çok hedefe veri göndermek için birleştirilebilir.

