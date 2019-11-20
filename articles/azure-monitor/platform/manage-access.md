---
title: Azure Izleyici 'de Log Analytics çalışma alanlarını yönetme | Microsoft Docs
description: Kaynak, çalışma alanı veya tablo düzeyi izinleri kullanarak Azure Izleyici 'deki bir Log Analytics çalışma alanında depolanan verilere erişimi yönetebilirsiniz. Bu makalede nasıl tamamlanacağı açıklanır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/22/2019
ms.openlocfilehash: 890e2fb06b9194bba49b94eae4b8ea3f0bfed1d7
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932179"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Azure Izleyici 'de günlük verilerine ve çalışma alanlarına erişimi yönetme

Azure Izleyici, [günlük](data-platform-logs.md) verilerini bir Log Analytics çalışma alanında depolar. Çalışma alanı, veri ve yapılandırma bilgilerini içeren bir kapsayıcıdır. Günlük verilerine erişimi yönetmek için, çalışma alanınız ile ilgili çeşitli yönetim görevlerini gerçekleştirirsiniz.

Bu makalede, ' ye erişim verme dahil olmak üzere günlüklere erişimi yönetme ve bunları içeren çalışma alanlarını yönetme konuları açıklanmaktadır: 

* Çalışma alanı izinlerini kullanarak çalışma alanı.
* Azure rol tabanlı erişim denetimi (RBAC) kullanarak belirli kaynaklardan günlük verilerine erişmesi gereken kullanıcılar.
* Azure RBAC kullanarak çalışma alanındaki belirli bir tablodaki günlük verilerine erişmesi gereken kullanıcılar.

## <a name="configure-access-control-mode"></a>Erişim denetimi modunu yapılandırma

Bir çalışma alanında yapılandırılan erişim denetimi modunu Azure portal veya Azure PowerShell ile görüntüleyebilirsiniz.  Bu ayarı, aşağıdaki desteklenen yöntemlerden birini kullanarak değiştirebilirsiniz:

* Azure portalı

* Azure PowerShell

* Azure Resource Manager şablonu

### <a name="from-the-azure-portal"></a>Azure portal

**Log Analytics çalışma** alanı menüsündeki çalışma alanının **genel bakış** sayfasında geçerli çalışma alanı erişim denetimi modunu görüntüleyebilirsiniz.

![Çalışma alanı erişim denetimi modunu görüntüle](media/manage-access/view-access-control-mode.png)

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
1. Azure portal, çalışma alanınız > Log Analytics çalışma alanları ' nı seçin.

Bu ayarı, çalışma alanının **Özellikler** sayfasından değiştirebilirsiniz. Çalışma alanını yapılandırma izniniz yoksa ayarı değiştirmek devre dışı bırakılır.

![Çalışma alanı erişim modunu değiştir](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>PowerShell'i kullanma

Abonelikteki tüm çalışma alanları için erişim denetimi modunu incelemek üzere aşağıdaki komutu kullanın:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

Çıkışın aşağıdakine benzer olması gerekir:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

`False` değeri, çalışma alanının çalışma alanı bağlamı erişim moduyla yapılandırıldığı anlamına gelir.  `True` değeri, çalışma alanının kaynak bağlamı erişim moduyla yapılandırıldığı anlamına gelir.

> [!NOTE]
> Bir çalışma alanı Boole değeri olmadan döndürülürse ve boşsa, bu da `False` değerinin sonuçlarıyla eşleşir.
>

Belirli bir çalışma alanı için erişim denetimi modunu kaynak bağlamı iznine ayarlamak için aşağıdaki betiği kullanın:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Abonelikteki tüm çalışma alanları için erişim denetimi modunu kaynak bağlamı iznine ayarlamak için aşağıdaki betiği kullanın:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="using-a-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanma

Azure Resource Manager şablonunda erişim modunu yapılandırmak için, çalışma alanında **Enablelogaccessusingonlyresourcepermissions** Özellik bayrağını aşağıdaki değerlerden birine ayarlayın.

* **yanlış**: çalışma alanını çalışma alanı bağlamı izinlerine ayarlayın. Bayrak ayarlanmamışsa bu varsayılan ayardır.
* **doğru**: çalışma alanını Kaynak bağlamı izinlerine ayarlayın.

## <a name="manage-access-using-workspace-permissions"></a>Çalışma alanı izinlerini kullanarak erişimi yönetme

Her çalışma alanı kendisiyle ilişkilendirilmiş birden çok hesap içerebilir ve her hesabın birden fazla çalışma alanına erişimi olabilir. Erişim, [Azure rol tabanlı erişim](../../role-based-access-control/role-assignments-portal.md)kullanılarak yönetilir.

Şu etkinlikler de Azure izinleri gerektirir:

|Eylem |Gereken Azure İzni |Notlar |
|-------|-------------------------|------|
| İzleme çözümlerini ekleme ve kaldırma | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Bu izinlerin kaynak grubu veya abonelik düzeyinde verilmiş olması gerekir. |
| Fiyatlandırma katmanını değiştirme | `Microsoft.OperationalInsights/workspaces/*/write` | |
| *Backup* ve *Site Recovery* çözüm kutucuklarındaki verileri görüntüleme | Yönetici / Ortak yönetici | Klasik dağıtım modeli kullanılarak dağıtılan kaynaklara erişir |
| Azure portalında bir çalışma alanı oluşturma | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Çalışma alanı temel özelliklerini görüntüleyin ve portalda çalışma alanı Dikey penceresini girin | `Microsoft.OperationalInsights/workspaces/read` ||
| Tüm arabirimleri kullanarak günlükleri sorgula | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Sorguları kullanarak tüm günlük türlerine erişin | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Belirli bir günlük tablosuna erişme | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Bu çalışma alanına Günlükler gönderilmesine izin vermek için çalışma alanı anahtarlarını okuyun | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Azure izinlerini kullanarak erişimi yönetme

Azure izinlerini kullanarak Log Analytics çalışma alanına izin vermek için, [Azure abonelik kaynaklarınıza erişimi yönetmek için rol atamalarını kullanma](../../role-based-access-control/role-assignments-portal.md) bölümündeki adımları izleyin. Örneğin özel roller için bkz. [özel roller örneği](#custom-role-examples)

Azure 'da Log Analytics çalışma alanları için iki yerleşik kullanıcı rolü vardır:

* Log Analytics Okuyucusu
* Log Analytics Katkıda Bulunan

*Log Analytics Okuyucusu* rolünün üyeleri aşağıdakileri yapabilir:

* Tüm izleme verilerini görüntüleme ve arama
* Tüm Azure kaynakları üzerinde Azure tanılama yapılandırmasını görüntüleme dahil olmak üzere, izleme ayarlarını görüntüleyin.

Log Analytics okuyucu rolü aşağıdaki Azure eylemlerini içerir:

| Tür    | İzin | Açıklama |
| ------- | ---------- | ----------- |
| Eylem | `*/read`   | Tüm Azure kaynaklarını ve kaynak yapılandırmalarını görüntüleyebilme. Aşağıdakileri görüntülemeyi içerir: <br> Sanal makine uzantısı durumu <br> Kaynaklarda Azure tanılamalarının yapılandırması <br> Tüm kaynakların tüm özellikleri ve ayarları. <br> Çalışma alanları için, tam Kısıtlanmamış izinlerin çalışma alanı ayarlarını okumasını ve verilerin üzerinde sorgu gerçekleştirmesini sağlar. Daha ayrıntılı seçeneklere bakın. |
| Eylem | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Kullanım dışı, kullanıcılara atanması gerekmez. |
| Eylem | `Microsoft.OperationalInsights/workspaces/search/action` | Kullanım dışı, kullanıcılara atanması gerekmez. |
| Eylem | `Microsoft.Support/*` | Destek olayları açma özelliği |
|Eylem Dışı | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Veri toplama API 'sini kullanmak ve aracıları yüklemek için gerekli çalışma alanı anahtarını okumayı engeller. Bu, kullanıcının çalışma alanına yeni kaynak eklemesini önler |

*Log Analytics Katkıda Bulunan* rolünün üyeleri aşağıdakileri yapabilir:

* *Log Analytics okuyucu rolünün*tüm ayrıcalıklarını içerir ve bu da kullanıcının tüm izleme verilerini okumasına izin verir
* Otomasyon hesapları oluşturma ve yapılandırma
* Yönetim çözümlerini ekleme ve kaldırma

    > [!NOTE]
    > Son iki eylemi başarılı bir şekilde gerçekleştirmek için bu iznin kaynak grubu veya abonelik düzeyinde verilmesi gerekir.

* Depolama hesabı anahtarlarını oku
* Azure depolama 'daki günlüklerin koleksiyonunu yapılandırma
* Azure kaynaklarına ilişkin izleme ayarlarını düzenleme:
  * VM'lere VM uzantısı ekleme
  * Tüm Azure kaynaklarında Azure tanılamayı yapılandırma

> [!NOTE]
> Özelliği, bir sanal makine üzerinde tam denetim kazanmak için bir sanal makineye sanal makine uzantısı eklemek için kullanabilirsiniz.

Log Analytics katkıda bulunan rolü aşağıdaki Azure eylemlerini içerir:

| İzin | Açıklama |
| ---------- | ----------- |
| `*/read`     | Tüm kaynakların ve kaynak yapılandırmalarının görüntülenmesine imkan sağlar. Aşağıdakileri görüntülemeyi içerir: <br> Sanal makine uzantısı durumu <br> Kaynaklarda Azure tanılamalarının yapılandırması <br> Tüm kaynakların tüm özellikleri ve ayarları. <br> Çalışma alanları için, tam Kısıtlanmamış izinlerin çalışma alanı ayarını okumasına ve verilerde sorgu gerçekleştirmesine izin verir. Daha ayrıntılı seçeneklere bakın. |
| `Microsoft.Automation/automationAccounts/*` | Runbook'ları ekleme ve düzenleme dahil olmak üzere Azure Otomasyonu hesapları oluşturma ve yapılandırma olanağı |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Linux uzantısı için OMS aracısı ve Microsoft Monitoring Agent gibi sanal makine uzantılarını ekleme, güncelleştirme ve kaldırma |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Depolama hesabı anahtarını görüntüleyin. Log Analytics’i Azure depolama hesaplarındaki günlükleri okuyacak şekilde yapılandırmak için gereklidir |
| `Microsoft.Insights/alertRules/*` | Uyarı kurallarını ekleme, güncelleştirme ve kaldırma |
| `Microsoft.Insights/diagnosticSettings/*` | Azure kaynaklarında tanılama ayarlarını ekleme, güncelleştirme ve kaldırma |
| `Microsoft.OperationalInsights/*` | Log Analytics çalışma alanları için yapılandırma ekleyin, güncelleştirin ve kaldırın. Çalışma alanı gelişmiş ayarlarını düzenlemek için kullanıcının `Microsoft.OperationalInsights/workspaces/write` olması gerekir. |
| `Microsoft.OperationsManagement/*` | Yönetim çözümlerini ekleme ve kaldırma |
| `Microsoft.Resources/deployments/*` | Dağıtımları oluşturma ve silme. Çözümleri, çalışma alanlarını ve otomasyon hesaplarını eklemek ve kaldırmak için gereklidir |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Dağıtımları oluşturma ve silme. Çözümleri, çalışma alanlarını ve otomasyon hesaplarını eklemek ve kaldırmak için gereklidir |

Kullanıcıları bir kullanıcı rolüne eklemek ve bu rolden kaldırmak için `Microsoft.Authorization/*/Delete` ve `Microsoft.Authorization/*/Write` izinleri gereklidir.

Bu rolleri, kullanıcılara farklı kapsamlarda erişim vermek için kullanın:

* Abonelik - Abonelikteki tüm çalışma alanlarına erişim
* Kaynak grubu - Kaynak grubundaki tüm çalışma alanına erişim
* Kaynak - Yalnızca belirtilen çalışma alanına erişim

Doğru erişim denetimini güvence altına almak için kaynak düzeyinde (çalışma alanı) atamalar gerçekleştirmenizi öneririz. Gereken özel izinlere sahip rolleri oluşturmak için [özel rolleri](../../role-based-access-control/custom-roles.md) kullanın.

### <a name="resource-permissions"></a>Kaynak izinleri

Kullanıcılar, kaynak bağlamı erişimi kullanarak bir çalışma alanından günlükleri sorgularsa, kaynak üzerinde aşağıdaki izinlere sahip olurlar:

| İzin | Açıklama |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Örnekler:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Kaynak için tüm günlük verilerini görüntüleme olanağı.  |
| `Microsoft.Insights/diagnosticSettings/write` | Tanılama ayarını bu kaynak için günlükleri ayarlamaya izin verecek şekilde yapılandırma özelliği. |

`/read` izni genellikle yerleşik [okuyucu](../../role-based-access-control/built-in-roles.md#reader) ve [katkıda bulunan](../../role-based-access-control/built-in-roles.md#contributor) rolleri gibi _\*/Read veya_ _\*_ izinleri içeren bir rolden verilir. Belirli eylemler veya adanmış yerleşik roller içeren özel roller bu izni içermeyebilir.

Farklı tablolar için farklı erişim denetimi oluşturmak isterseniz, aşağıdaki [tablo başına erişim denetimi tanımlama](#table-level-rbac) bölümüne bakın.

## <a name="custom-role-examples"></a>Özel rol örnekleri

1. Bir kullanıcıya, kaynaklarından günlük verileri erişimi vermek için aşağıdakileri yapın:

    * Çalışma alanı erişim denetimi modunu **çalışma alanı veya kaynak izinleri kullanacak** şekilde yapılandırma

    * Kullanıcılara kaynaklarına `*/read` veya `Microsoft.Insights/logs/*/read` izinleri verin. Çalışma alanında [Log Analytics okuyucu](../../role-based-access-control/built-in-roles.md#reader) rolü zaten atanırsa, bu yeterli olur.

2. Bir kullanıcıya, kaynaklarından günlük verilerine erişim vermek ve kaynaklarını çalışma alanına Günlükler gönderecek şekilde yapılandırmak için aşağıdakileri yapın:

    * Çalışma alanı erişim denetimi modunu **çalışma alanı veya kaynak izinleri kullanacak** şekilde yapılandırma

    * Çalışma alanında kullanıcılara şu izinleri verin: `Microsoft.OperationalInsights/workspaces/read` ve `Microsoft.OperationalInsights/workspaces/sharedKeys/action`. Kullanıcılar, bu izinlerle hiçbir çalışma alanı düzeyi sorgu gerçekleştiremez. Bunlar yalnızca çalışma alanını numaralandırırlar ve Tanılama ayarları veya aracı yapılandırması için bir hedef olarak kullanabilir.

    * Kullanıcılara kaynakları için aşağıdaki izinleri verin: `Microsoft.Insights/logs/*/read` ve `Microsoft.Insights/diagnosticSettings/write`. [Log Analytics katkıda](../../role-based-access-control/built-in-roles.md#contributor) bulunan rolü zaten atanmışsa, okuyucu rolüne atanır veya bu kaynakta `*/read` izinleri verildiyse yeterli olur.

3. Güvenlik olaylarını okuyup veri gönderebilmek zorunda kalmadan, kaynaklarından günlük verilerine erişim vermek için aşağıdakileri yapın:

    * Çalışma alanı erişim denetimi modunu **çalışma alanı veya kaynak izinleri kullanacak** şekilde yapılandırma

    * Kullanıcılara kaynakları için aşağıdaki izinleri verin: `Microsoft.Insights/logs/*/read`.

    * Kullanıcıların SecurityEvent türünü okumasını engellemek için aşağıdaki eylemi ekleyin: `Microsoft.Insights/logs/SecurityEvent/read`. Olmayan eylem, okuma izni sağlayan eylem ile aynı özel rolde yer almaktadır (`Microsoft.Insights/logs/*/read`). Kullanıcı bu kaynağa veya abonelik ya da kaynak grubuna atanmış başka bir rolden okuma eylemini içeriyorsa, tüm günlük türlerini okuyabilirler. Bu, örneğin, okuyucu veya katkıda bulunan rolüyle `*/read`, varsa de geçerlidir.

4. Bir kullanıcıya, kaynaklarından günlük verileri için erişim izni vermek ve tüm Azure AD oturum açma ve Güncelleştirme Yönetimi çözüm günlüğü verilerini çalışma alanından okumak için aşağıdakileri yapın:

    * Çalışma alanı erişim denetimi modunu **çalışma alanı veya kaynak izinleri kullanacak** şekilde yapılandırma

    * Çalışma alanında kullanıcılara aşağıdaki izinleri verin: 

        * `Microsoft.OperationalInsights/workspaces/read` – kullanım, çalışma alanını numaralandırmak ve Azure portal çalışma alanı Dikey penceresini açmak için gereklidir
        * `Microsoft.OperationalInsights/workspaces/query/read` – sorguları yürütebilmesi için her kullanıcı için gereklidir
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read` – Azure AD oturum açma günlüklerini okuyabilmek için
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` – Güncelleştirme Yönetimi çözüm günlüklerini okuyabilmek için
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` – Güncelleştirme Yönetimi çözüm günlüklerini okuyabilmek için
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read` – güncelleştirme yönetimi günlüklerini okuyabilmek için
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` – Güncelleştirme Yönetimi çözümü kullanabiliyor olması gerekir
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` – Güncelleştirme Yönetimi çözümü kullanabiliyor olması gerekir

    * Kullanıcılara kaynakları için aşağıdaki izinleri verin: `*/read`, okuyucu rolüne atandı veya `Microsoft.Insights/logs/*/read`. 

## <a name="table-level-rbac"></a>Tablo düzeyi RBAC

**Tablo DÜZEYI RBAC** , diğer izinlerin yanı sıra bir Log Analytics çalışma alanındaki verilere daha ayrıntılı denetim tanımlamanızı sağlar. Bu denetim, yalnızca belirli bir kullanıcı kümesi için erişilebilen belirli veri türlerini tanımlamanızı sağlar.

Çalışma alanındaki belirli [tablolara](../log-query/logs-structure.md) erişim izni vermek ya da erişimi reddetmek için [Azure özel rolleriyle](../../role-based-access-control/custom-roles.md) tablo erişim denetimi uygulayabilirsiniz. Bu roller, kullanıcının [erişim modundan](design-logs-deployment.md#access-mode)bağımsız olarak, çalışma alanı bağlamı veya kaynak bağlamı [erişim denetimi modlarıyla](design-logs-deployment.md#access-control-mode) çalışma alanlarına uygulanır.

Tablo erişim denetimine erişimi tanımlamak için aşağıdaki eylemlerle [özel bir rol](../../role-based-access-control/custom-roles.md) oluşturun.

* Bir tabloya erişim vermek için, rol tanımının **Eylemler** bölümüne ekleyin.
* Bir tabloya erişimi reddetmek için, rol tanımının **NotActions** bölümüne ekleyin.
* Tüm tabloları belirtmek için * kullanın.

Örneğin, _sinyal_ ve _AzureActivity_ tablolarına erişimi olan bir rol oluşturmak için aşağıdaki eylemleri kullanarak özel bir rol oluşturun:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Yalnızca _Securitybaseline_ 'e erişimi olan ve başka tablolar olmadan bir rol oluşturmak için aşağıdaki eylemleri kullanarak özel bir rol oluşturun:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
"NotActions":  [
    "Microsoft.OperationalInsights/workspaces/query/*/read"
],
```

### <a name="custom-logs"></a>Özel günlükler

 Özel Günlükler ve HTTP Veri Toplayıcı API 'SI gibi veri kaynaklarından özel günlükler oluşturulur. Günlük türünü belirlemenin en kolay yolu, [günlük şemasında özel Günlükler](../log-query/get-started-portal.md#understand-the-schema)altında listelenen tabloları denetlemede olur.

 Şu anda tek tek özel günlüklere erişim izni veremez veya vermeyebilirsiniz, ancak tüm özel günlüklere erişim verebilir veya erişimi reddedebilirsiniz. Tüm özel günlüklere erişimi olan bir rol oluşturmak için aşağıdaki eylemleri kullanarak özel bir rol oluşturun:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```

### <a name="considerations"></a>Dikkat edilmesi gerekenler

* Bir kullanıcıya standart okuyucu veya _\*/okuma_ eylemi içeren katkıda bulunan rollerle genel okuma izni verildiyse, tablo başına erişim denetimini geçersiz kılar ve tüm günlük verilerine erişim sağlar.
* Bir kullanıcıya tablo başına erişim verildiyse ancak başka izinler yoksa, API 'den günlük verilerine erişebilecek ancak Azure portal. Azure portal erişim sağlamak için, temel rolü olarak Log Analytics okuyucu kullanın.
* Aboneliğin yöneticileri, diğer izin ayarlarından bağımsız olarak tüm veri türlerine erişebilir.
* Çalışma alanı sahipleri, tablo başına erişim denetimi için diğer kullanıcılar gibi davranır.
* Bireysel kullanıcılar yerine, atamaların sayısını azaltmak için güvenlik gruplarına roller atanmasını öneririz. Bu Ayrıca, erişimi yapılandırmak ve doğrulamak için mevcut Grup Yönetimi araçlarını kullanmanıza yardımcı olur.

## <a name="next-steps"></a>Sonraki adımlar

* Veri merkezinizdeki veya diğer bulut Ortamınızdaki bilgisayarlardan veri toplamak için [Log Analytics aracısına genel bakış](../../azure-monitor/platform/log-analytics-agent.md) konusuna bakın.

* Azure VM 'lerinden veri toplamayı yapılandırmak için bkz. [Azure sanal makineler hakkında veri toplama](../../azure-monitor/learn/quick-collect-azurevm.md) .
