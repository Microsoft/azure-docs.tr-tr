---
title: Azure 'da Güncelleştirme Yönetimi dağıtımınızda ön betikleri ve komut dosyalarını yönetme
description: Bu makalede, güncelleştirme dağıtımları için betiklerin ve son betiklerin nasıl yapılandırılacağı ve yönetileceği açıklanmaktadır.
services: automation
ms.subservice: update-management
ms.date: 03/08/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51067095b7ebb33da61908b1424752b481668f5f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830817"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Ön betikleri ve son betikleri yönetme

Ön betikler ve son betikler, Azure Otomasyonu hesabınızda (ön görev öncesi) ve sonrasında (görev sonrası) bir güncelleştirme dağıtımı üzerinden çalıştırılacak runbook 'lardır. Ön betikler ve son betikler yerel olarak değil, Azure bağlamında çalışır. Ön betikler güncelleştirme dağıtımının başlangıcında çalışır. Son betikler, dağıtımın sonunda ve yapılandırılan tüm yeniden başlatmalar sonrasında çalışır.

## <a name="pre-script-and-post-script-requirements"></a>Betik öncesi ve betik sonrası gereksinimler

Bir runbook 'un bir ön betik veya komut dosyası olarak kullanılabilmesi için, uygulamayı Otomasyon hesabınıza aktarmanız ve [runbook 'u yayımlamanız](../manage-runbooks.md#publish-a-runbook)gerekir.

Şu anda yalnızca PowerShell ve Python 2 runbook 'ları, ön/son betik olarak desteklenmektedir. Python 3, grafik, PowerShell Iş akışı, grafik PowerShell Iş akışı gibi diğer runbook türleri şu anda ön/Post betikleri olarak desteklenmemektedir.

## <a name="pre-script-and-post-script-parameters"></a>Betik öncesi ve betik sonrası parametreler

Betikleri ön betikler ve betikleri yapılandırdığınızda, bir runbook 'u zamanlama gibi parametreleri geçirebilirsiniz. Parametreler, güncelleştirme dağıtımı oluşturma sırasında tanımlanmıştır. Betiklerin ve son betiklerin ön betikleri aşağıdaki türleri destekler:

* Char
* bayt
* 'tir
* kalacağını
* Kategori
* sunuculu
* Çift
* Hem
* dizisinde

Ön betik ve betik sonrası runbook parametreleri Boolean, nesne veya dizi türlerini desteklemez. Bu değerler runbook 'ların başarısız olmasına neden olur. 

Başka bir nesne türüne ihtiyacınız varsa, runbook 'ta kendi mantığınızla başka bir türe çevirebilirsiniz.

Standart runbook parametrelerinizin yanı sıra `SoftwareUpdateConfigurationRunContext` parametresi (JSON dizesi türü) sağlanır. Parametresini ön betikte veya betik sonrası runbook 'ınızla tanımlarsanız, güncelleştirme dağıtımı tarafından otomatik olarak geçirilir. Parametresi, [Softwareupdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration)tarafından döndürülen bilgilerin bir alt kümesi olan güncelleştirme dağıtımı hakkında bilgiler içerir. Aşağıdaki bölümler ilişkili özellikleri tanımlar.

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext özellikleri

|Özellik  |Açıklama  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Yazılım güncelleştirme yapılandırmasının adı.        |
|Softwareupdateconfigurationrunıd     | Çalıştırmanın benzersiz KIMLIĞI.        |
|SoftwareUpdateConfigurationSettings     | Yazılım güncelleştirme yapılandırmasıyla ilgili özellikler koleksiyonu.         |
|SoftwareUpdateConfigurationSettings. operatingSystem     | Güncelleştirme dağıtımı için hedeflenen işletim sistemleri.         |
|SoftwareUpdateConfigurationSettings. Duration     | Güncelleştirme dağıtımının en uzun süresi, `PT[n]H[n]M[n]S` ISO8601 başına olarak çalışır; bakım penceresi de denir.          |
|SoftwareUpdateConfigurationSettings. Windows     | Windows bilgisayarlarıyla ilgili özellikler koleksiyonu.         |
|SoftwareUpdateConfigurationSettings. Windows. excludedKbNumbers     | Güncelleştirme dağıtımından dışlanan KBs 'lerin bir listesi.        |
|SoftwareUpdateConfigurationSettings. Windows. ıncludedupdatesınıflandırmaları     | Güncelleştirme dağıtımı için seçilen güncelleştirme sınıflandırmaları.        |
|SoftwareUpdateConfigurationSettings. Windows. rebootSetting     | Güncelleştirme dağıtımı için yeniden başlatma ayarları.        |
|azureVirtualMachines     | Güncelleştirme dağıtımındaki Azure VM 'Leri için Resourceıds listesi.        |
|nonAzureComputerNames|Güncelleştirme dağıtımında, Azure olmayan bilgisayarların FQDN 'lerinin listesi.|

Aşağıdaki örnek, **Softwareupdateconfigurationruncontext** parametresine GEÇIRILEN bir JSON dizesidir:

```json
"SoftwareUpdateConfigurationRunContext": {
    "SoftwareUpdateConfigurationName": "sampleConfiguration",
    "SoftwareUpdateConfigurationRunId": "00000000-0000-0000-0000-000000000000",
    "SoftwareUpdateConfigurationSettings": {
      "operatingSystem": "Windows",
      "duration": "PT2H0M",
      "windows": {
        "excludedKbNumbers": [
          "168934",
          "168973"
        ],
        "includedUpdateClassifications": "Critical",
        "rebootSetting": "IfRequired"
      },
      "azureVirtualMachines": [
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
      ],
      "nonAzureComputerNames": [
        "box1.contoso.com",
        "box2.contoso.com"
      ]
    }
  }
```

Tüm özelliklere sahip tam bir örnek şurada bulunabilir: [ada göre yazılım güncelleştirme yapılandırmasını al](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> `SoftwareUpdateConfigurationRunContext`Nesne, makineler için yinelenen girdiler içerebilir. Bu, ön betiklerin ve betiklerin aynı makinede birden fazla kez çalışmasına neden olabilir. Bu davranışı geçici olarak çözmek için, `Sort-Object -Unique` yalnızca BENZERSIZ VM adlarını seçmek üzere kullanın.

## <a name="use-a-pre-script-or-post-script-in-a-deployment"></a>Bir dağıtımda ön betik veya komut dosyası kullanma

Bir güncelleştirme dağıtımında bir ön betik veya bir komut dosyası kullanmak için, bir güncelleştirme dağıtımı oluşturarak başlayın. **Ön betikler + betikleri sonrası**' u seçin. Bu eylem, **betikleri ön betikleri Seç + betikleri sonrası** sayfasını açar.

![Betikleri seçin](./media/pre-post-scripts/select-scripts.png)

Kullanmak istediğiniz betiği seçin. Bu örnekte, **updatemanagement-TurnOnVms** runbook 'unu kullanırız. Runbook 'u seçtiğinizde, **betiği Yapılandır** sayfası açılır. **Ön betik**' i seçin ve ardından **Tamam**' ı seçin.

**Updatemanagement-TurnOffVms** betiği için bu işlemi tekrarlayın. Ancak **komut dosyası türünü** seçtiğinizde, **betiği gönder**' i seçin.

**Seçili öğeler** bölümü artık betiklerinizin seçili olduğunu gösteriyor. Birisi bir ön betiktir ve diğeri bir komut dosyası olur:

![Seçili öğeler](./media/pre-post-scripts/selected-items.png)

Güncelleştirme dağıtımınızı yapılandırmayı tamamlama.

Güncelleştirme dağıtımınız tamamlandığında, sonuçları görüntülemek için **güncelleştirme dağıtımları** ' na gidebilirsiniz. Görebileceğiniz gibi, durum, ön betik ve son betik için verilmiştir:

![Sonuçları Güncelleştir](./media/pre-post-scripts/update-results.png)

Güncelleştirme dağıtımı çalıştırmasını seçtiğinizde, betiklerin ve son betiklerin ek ayrıntıları gösterilir. Çalıştırma sırasında betik kaynağına bir bağlantı sağlanır.

![Dağıtım çalıştırma sonuçları](./media/pre-post-scripts/deployment-run.png)

## <a name="stop-a-deployment"></a>Dağıtımı durdur

Bir ön koda dayalı bir dağıtımı durdurmak isterseniz, bir özel durum [oluşturmanız gerekir.](../automation-runbook-execution.md#throw) Bunu yapmazsanız dağıtım ve son betik çalışmaya devam edecektir. Aşağıdaki kod parçacığı, PowerShell kullanarak özel durum oluşturmayı gösterir.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

Python 2 ' de, özel durum işleme bir [TRY](https://www.python-course.eu/exception_handling.php) bloğunda yönetilir.

## <a name="interact-with-machines"></a>Makinelerle etkileşim kurma

Ön betiklerin ve son betiklerin, doğrudan dağıtımınızdaki makinelere değil, Otomasyon hesabınızda runbook 'lar olarak çalışır. Ön görevler ve görevler, Azure bağlamında da çalışır ve Azure olmayan makinelere erişemez. Aşağıdaki bölümlerde, Azure VM 'Leri veya Azure dışı makineler olsun, makinelerle doğrudan nasıl etkileşim kurabileceği gösterilmektedir.

### <a name="interact-with-azure-machines"></a>Azure makinelerle etkileşim kurma

Ön görevler ve son görevler, runbook 'lar olarak çalışır ve dağıtımınızda Azure sanal makinelerinizdeki yerel olarak çalışmaz. Azure VM 'larınızla etkileşim kurmak için aşağıdaki öğelere sahip olmanız gerekir:

* Farklı Çalıştır hesabı
* Çalıştırmak istediğiniz runbook

Azure makinelerle etkileşim kurmak için [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) cmdlet 'Ini kullanarak Azure VM 'larınızla etkileşim kurun. Bunu nasıl yapacağınızı gösteren bir örnek için, [Çalıştır komutuyla güncelleştirme yönetimi-Betiği Çalıştır komutuyla](https://github.com/azureautomation/update-management-run-script-with-run-command)runbook örneği ' ne bakın.

### <a name="interact-with-non-azure-machines"></a>Azure olmayan makinelerle etkileşim kurma

Ön görevler ve son görevler Azure bağlamında çalışır ve Azure olmayan makinelere erişemez. Azure olmayan makinelerle etkileşim kurmak için aşağıdaki öğelere sahip olmanız gerekir:

* Farklı Çalıştır hesabı
* Makinede yüklü karma Runbook Worker
* Yerel olarak çalıştırmak istediğiniz runbook
* Üst runbook

Azure olmayan makinelerle etkileşim kurmak için Azure bağlamında bir üst runbook çalıştırılır. Bu runbook, [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet 'i ile bir alt runbook 'u çağırır. `RunOn`Parametresini belirtmeniz ve betiğin üzerinde çalışacağı karma Runbook Worker adını sağlamanız gerekir. [Betiği yerel olarak çalıştır güncelleştirme yönetimi](https://github.com/azureautomation/update-management-run-script-locally)runbook örneğine bakın.

## <a name="abort-patch-deployment"></a>Düzeltme Eki dağıtımını durdur

Ön betik bir hata döndürürse, dağıtımınızı iptal etmek isteyebilirsiniz. Bunu yapmak için, komut dosyasında hata oluşturabilecek herhangi bir mantık için bir hata [oluşturmanız gerekir.](/powershell/module/microsoft.powershell.core/about/about_throw)

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

Python 2 ' de, belirli bir koşul oluştuğunda bir hata oluşturmak istiyorsanız, bir [Raise](https://docs.python.org/2.7/reference/simple_stmts.html#the-raise-statement) ifadesini kullanın.

```python
If (<My custom error logic>)
   raise Exception('Something happened.')
```

## <a name="samples"></a>Örnekler

Ön betiklerin ve son betiklerin örnekleri [Azure Automation GitHub kuruluşunda](https://github.com/azureautomation) ve [PowerShell Galerisi](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)bulunabilir ya da Azure Portal aracılığıyla içeri aktarabilirsiniz. Bunu yapmak için, Otomasyon hesabınızda, **Işlem Otomasyonu** altında **runbook 'lar Galerisi**' ni seçin. Filtre için **güncelleştirme yönetimi** kullanın.

![Galeri listesi](./media/pre-post-scripts/runbook-gallery.png)

Ya da aşağıdaki listede gösterildiği gibi bunları kendi komut dosyası adlarıyla arayabilirsiniz:

* Güncelleştirme Yönetimi-VM 'Leri açma
* Güncelleştirme Yönetimi-VM 'Leri kapatma
* Güncelleştirme Yönetimi betiği yerel olarak çalıştır
* Ön/son betiklerin Güncelleştirme Yönetimi şablonu
* Güncelleştirme Yönetimi-Betiği Çalıştır komutuyla Çalıştır

> [!IMPORTANT]
> Runbook 'ları içeri aktardıktan sonra, kullanılmadan önce bunları yayımlamanız gerekir. Bunu yapmak için Otomasyon hesabınızda runbook 'u bulun, **Düzenle**' yi seçin ve ardından **Yayımla**' yı seçin.

Örnekler, aşağıdaki örnekte tanımlanan temel şablona dayalıdır. Bu şablon, komut dosyaları ve son betikler ile kullanmak üzere kendi runbook 'unuzu oluşturmak için kullanılabilir. Azure ile kimlik doğrulaması yapmak ve parametreyi işlemek için gerekli mantık `SoftwareUpdateConfigurationRunContext` dahildir.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccount -Name $runId -Value "" -Encrypted $false
#Set value of variable
Set-AutomationVariable -Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> Grafik olmayan PowerShell runbook 'ları için `Add-AzAccount` ve `Add-AzureRMAccount` [Connect-azaccount](/powershell/module/az.accounts/connect-azaccount)için diğer adlar. Bu cmdlet 'leri kullanabilir veya Otomasyon hesabınızdaki [modüllerinizi](../automation-update-azure-modules.md) en son sürümlere güncelleştirebilirsiniz. Yeni bir Otomasyon hesabı oluşturmuş olsanız bile modüllerinizi güncelleştirmeniz gerekebilir.

## <a name="next-steps"></a>Sonraki adımlar

Güncelleştirme yönetimi hakkında daha fazla bilgi için bkz. [VM 'niz için güncelleştirmeleri ve düzeltme eklerini yönetme](manage-updates-for-vm.md).
