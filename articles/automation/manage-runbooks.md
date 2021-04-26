---
title: Azure Otomasyonu 'nda runbook 'ları yönetme
description: Bu makalede, Azure Otomasyonu 'nda runbook 'ların nasıl yönetileceği açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 02/24/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a172189d8b52a80fc50e7d8c882859f7855aeca8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830097"
---
# <a name="manage-runbooks-in-azure-automation"></a>Azure Otomasyonu 'nda runbook 'ları yönetme

Yeni bir tane oluşturarak veya var olan bir dosyayı ya da [runbook galerisinden](automation-runbook-gallery.md)bir runbook 'u içeri aktararak, Azure Otomasyonu 'na bir runbook ekleyebilirsiniz. Bu makalede, bir dosyadan içeri aktarılmış bir runbook 'u yönetmeye yönelik bilgiler sağlanmaktadır. [Azure Otomasyonu Için runbook ve modül galerilerinde](automation-runbook-gallery.md)topluluk runbook 'larına ve modüllerine erişmenin tüm ayrıntılarını bulabilirsiniz.

## <a name="create-a-runbook"></a>Runbook oluşturma

Azure portal veya Windows PowerShell kullanarak Azure Otomasyonu 'nda yeni bir runbook oluşturun. Runbook oluşturulduktan sonra, içindeki bilgileri kullanarak düzenleyebilirsiniz.

* [Azure Otomasyonu 'nda metin runbook 'unu düzenleme](automation-edit-textual-runbook.md)
* [Otomasyon Runbook 'ları için anahtar Windows PowerShell Iş akışı kavramlarını öğrenin](automation-powershell-workflow.md)
* [Azure Otomasyonu 'nda Python 2 paketlerini yönetme](python-packages.md)
* [Azure Otomasyonu 'nda Python 3 paketlerini yönetme (Önizleme)](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Azure portal runbook oluşturma

1. Azure portalında, Otomasyon hesabınızı açın.
2. Hub 'dan runbook listesini açmak için **Işlem Otomasyonu** altında **runbook 'lar** ' ı seçin.
3. **Runbook oluştur ' a** tıklayın.
4. Runbook için bir ad girin ve [türünü](automation-runbook-types.md)seçin. Runbook adı bir harfle başlamalıdır ve harfler, rakamlar, alt çizgiler ve kısa çizgiler içerebilir.
5. Runbook 'u oluşturmak için **Oluştur** ' a tıklayın ve düzenleyiciyi açın.

### <a name="create-a-runbook-with-powershell"></a>PowerShell ile runbook oluşturma

Boş bir runbook oluşturmak için [New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) cmdlet 'ini kullanın. `Type`İçin tanımlanan runbook türlerinden birini belirtmek için parametresini kullanın `New-AzAutomationRunbook` .

Aşağıdaki örnek, yeni bir boş runbook oluşturmayı gösterir.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'NewRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
}
New-AzAutomationRunbook @params
```

## <a name="import-a-runbook"></a>Runbook 'u içeri aktarma

Kendi runbook 'unuzu oluşturmak için bir PowerShell veya PowerShell Iş akışı (**. ps1**) betiğini, bir grafik runbook 'unu (. graphrunbook) veya Python 2 ya da Python 3 betiğini (**.****çıgrafı**) içeri aktarabilirsiniz. İçeri aktarma sırasında oluşturulan [runbook 'un türünü](automation-runbook-types.md) belirtmeniz gerekir ve aşağıdaki noktaları dikkate alın.

* Bir [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) 'Una veya [PowerShell iş akışı runbook](automation-runbook-types.md#powershell-workflow-runbooks)'unda iş akışı içermeyen bir **. ps1** dosyasını içeri aktarabilirsiniz. Bir PowerShell Iş akışı runbook 'una içeri aktarırsanız, bir iş akışına dönüştürülür. Bu durumda, yapılan değişiklikleri anlatmak için Runbook 'a açıklamalar dahildir.

* PowerShell iş akışı [runbook 'una](automation-runbook-types.md#powershell-workflow-runbooks)yalnızca bir PowerShell iş akışı içeren bir **. ps1** dosyasını içeri aktarabilirsiniz. Dosya birden çok PowerShell iş akışı içeriyorsa içeri aktarma başarısız olur. Her bir iş akışını kendi dosyasına kaydetmeli ve ayrı olarak içeri aktarmanız gerekir.

* PowerShell betik altyapısı bunu tanıyamadığı için PowerShell Iş akışını içeren bir **. ps1** dosyasını [PowerShell runbook 'una](automation-runbook-types.md#powershell-runbooks)içeri aktarmayın.

* Yalnızca bir **. graphrunbook** dosyasını yeni bir [grafik runbook 'una](automation-runbook-types.md#graphical-runbooks)aktarın.

### <a name="import-a-runbook-from-the-azure-portal"></a>Azure portal runbook 'u içeri aktarma

Bir betik dosyasını Azure Otomasyonu 'na aktarmak için aşağıdaki yordamı kullanabilirsiniz.

> [!NOTE]
> Portalı kullanarak bir PowerShell Iş akışı runbook 'unda yalnızca bir **. ps1** dosyasını içeri aktarabilirsiniz.

1. Azure portalında, Otomasyon hesabınızı açın.
2. Runbook 'ların listesini açmak için **Işlem Otomasyonu** altında **runbook 'lar** ' ı seçin.
3. **Runbook 'U Içeri Aktar ' a** tıklayın.
4. **Runbook dosyası** ' na tıklayın ve içeri aktarılacak dosyayı seçin.
5. **Ad** alanı etkinleştirilirse, runbook adını değiştirme seçeneğiniz vardır. Ad bir harfle başlamalıdır ve harfler, rakamlar, alt çizgiler ve kısa çizgiler içerebilir.
6. [Runbook türü](automation-runbook-types.md) otomatik olarak seçilir, ancak ilgili kısıtlamaları hesaba aldıktan sonra türü değiştirebilirsiniz.
7. **Oluştur**’a tıklayın. Yeni runbook, Otomasyon hesabı için Runbook 'lar listesinde görüntülenir.
8. Çalıştırmak için [runbook 'u yayımlamanız](#publish-a-runbook) gerekir.

> [!NOTE]
> Grafik runbook 'unu içeri aktardıktan sonra, başka bir türe dönüştürebilirsiniz. Ancak, grafik bir runbook 'u metin temelli bir runbook 'a dönüştüremezsiniz.

### <a name="import-a-runbook-with-windows-powershell"></a>Windows PowerShell ile bir runbook 'u içeri aktarma

Bir betik dosyasını taslak runbook olarak içeri aktarmak için [Import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) cmdlet 'ini kullanın. Runbook zaten mevcutsa, parametresini cmdlet ile kullanmadığınız müddetçe içeri aktarma başarısız olur `Force` .

Aşağıdaki örnek bir runbook 'a bir betik dosyasının nasıl içeri aktarılacağını gösterir.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'Sample_TestRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
    Path                  = 'C:\Runbooks\Sample_TestRunbook.ps1'
}
Import-AzAutomationRunbook @params
```

## <a name="handle-resources"></a>Kaynakları işle

Runbook 'ağınız bir [kaynak](automation-runbook-execution.md#resources)oluşturursa, betiği oluşturmayı denemeden önce kaynağın zaten mevcut olup olmadığını kontrol etmelidir. Temel bir örnek aşağıda verilmiştir.

```powershell
$vmName = 'WindowsVM1'
$rgName = 'MyResourceGroup'
$myCred = Get-AutomationPSCredential 'MyCredential'

$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $rgName
if (-not $vmExists) {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $rgName -Credential $myCred
} else {
    Write-Output "VM $vmName already exists, skipping"
}
```

## <a name="retrieve-details-from-activity-log"></a>Etkinlik günlüğünden ayrıntıları alma

Otomasyon hesabı için [etkinlik günlüğünden](automation-runbook-execution.md#activity-logging) runbook 'u başlatan kişi veya hesap gibi runbook ayrıntılarını alabilirsiniz. Aşağıdaki PowerShell örneği, son kullanıcının belirtilen runbook 'u çalıştırmasını sağlar.

```powershell-interactive
$SubID = '00000000-0000-0000-0000-000000000000'
$AutoRgName = 'MyResourceGroup'
$aaName = 'MyAutomationAccount'
$RunbookName = 'MyRunbook'
$StartTime = (Get-Date).AddDays(-1)

$params = @{
    ResourceGroupName = $AutoRgName
    StartTime         = $StartTime
}
$JobActivityLogs = (Get-AzLog @params).Where( { $_.Authorization.Action -eq 'Microsoft.Automation/automationAccounts/jobs/write' })

$JobInfo = @{}
foreach ($log in $JobActivityLogs) {
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($null -eq $JobInfo[$log.SubmissionTimestamp] -and $JobResource.Properties.Runbook.Name -eq $RunbookName) {
        # Get runbook
        $jobParams = @{
            ResourceGroupName     = $AutoRgName
            AutomationAccountName = $aaName
            Id                    = $JobResource.Properties.JobId
        }
        $Runbook = Get-AzAutomationJob @jobParams | Where-Object RunbookName -EQ $RunbookName

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | Sort-Object Key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>İlerleme izleme

Runbook 'larınızın daha kolay bir şekilde yeniden kullanılabilmesi ve yeniden başlatılması için, runbook 'larınızı doğası gereği modüler olarak yazmak iyi bir uygulamadır. Bir runbook 'ta ilerlemeyi izlemek, sorunlar varsa runbook mantığının doğru şekilde yürütüldüğünü sağlar.

Bir runbook 'un ilerlemesini depolama hesabı, veritabanı veya paylaşılan dosyalar gibi bir dış kaynak kullanarak izleyebilirsiniz. İlk olarak gerçekleştirilen son eylemin durumunu denetlemek için Runbook 'ınızdaki mantığı oluşturun. Daha sonra, denetim sonuçlarına göre, mantıksal runbook 'taki belirli görevleri atlayabilir veya devam edebilir.

## <a name="prevent-concurrent-jobs"></a>Eş zamanlı işleri engelle

Bazı runbook 'lar aynı anda birden çok iş üzerinde çalışıyorsa Strangely davranır. Bu durumda, bir runbook 'un zaten çalışmakta olan bir işin olup olmadığını belirlemede Logic uygulaması için önem taşır. Temel bir örnek aşağıda verilmiştir.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
$cnParams = @{
    ServicePrincipal      = $true
    Tenant                = $connection.TenantId
    ApplicationId         = $connection.ApplicationId
    CertificateThumbprint = $connection.CertificateThumbprint
}
Connect-AzAccount @cnParams
$AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs.Where( { $_.Status -eq 'Running' })).count

if (($jobs.Status -contains 'Running' -and $runningCount -gt 1 ) -or ($jobs.Status -eq 'New')) {
    # Exit code
    Write-Output "Runbook [$runbookName] is already running"
    exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Zamana bağlı bir betikte geçici hataları işleme

Runbook 'larınızın, yeniden başlatılmasına veya başarısız olmasına neden olabilecek geçici hatalar da dahil olmak üzere sağlam ve [hataları](automation-runbook-execution.md#errors)işleyebilme yeteneğine sahip olması gerekir. Bir runbook başarısız olursa, Azure Otomasyonu bunu yeniden dener.

Runbook 'larınız normalde bir zaman kısıtlaması içinde çalışıyorsa, yürütme süresini denetlemek için komut dosyasının Logic uygular. Bu denetim, yalnızca belirli zamanlarda başlatma, kapatma veya genişleme gibi işlemlerin çalıştırılmasını sağlar.

> [!NOTE]
> Azure korumalı alan işlemindeki yerel saat UTC olarak ayarlanır. Runbook 'larınızda tarih ve saat hesaplamaları bu olguyu göz önünde bulundurmanız gerekir.

## <a name="work-with-multiple-subscriptions"></a>Birden fazla abonelik ile çalışma

Runbook 'larınızın [aboneliklerle](automation-runbook-execution.md#subscriptions)çalışabilebilmesi gerekir. Örneğin, birden çok aboneliği işlemek için Runbook [devre dışı bırak-AzContextAutosave](/powershell/module/Az.Accounts/Disable-AzContextAutosave) cmdlet 'ini kullanır. Bu cmdlet, kimlik doğrulama bağlamının aynı korumalı alanda çalışan başka bir runbook 'tan alınmamasını sağlar. Runbook aynı zamanda `Get-AzContext` cmdlet 'ini kullanarak geçerli oturumun bağlamını alır ve değişkenine atar `$AzureContext` .

```powershell
Disable-AzContextAutosave -Scope Process

$connection = Get-AutomationConnection -Name AzureRunAsConnection
$cnParams = @{
    ServicePrincipal      = $true
    Tenant                = $connection.TenantId
    ApplicationId         = $connection.ApplicationId
    CertificateThumbprint = $connection.CertificateThumbprint
}
Connect-AzAccount @cnParams

$ChildRunbookName = 'ChildRunbookDemo'
$aaName = 'MyAutomationAccount'
$rgName = 'MyResourceGroup'

$startParams = @{
    ResourceGroupName     = $rgName
    AutomationAccountName = $aaName
    Name                  = $ChildRunbookName
    DefaultProfile        = $AzureContext
}
Start-AzAutomationRunbook @startParams
```

## <a name="work-with-a-custom-script"></a>Özel bir komut dosyasıyla çalışma

> [!NOTE]
> Normalde Log Analytics Aracısı yüklü olan konakta özel komut dosyaları ve Runbook 'lar çalıştıramazsınız.

Özel bir komut dosyası kullanmak için:

1. Bir Otomasyon hesabı oluşturun ve [katkıda bulunan rolü](automation-role-based-access-control.md)alın.
2. [Hesabı Azure çalışma alanına bağlayın](../security-center/security-center-enable-data-collection.md).
3. [Karma Runbook Worker](automation-hybrid-runbook-worker.md), [güncelleştirme yönetimi](./update-management/overview.md)veya başka bir Otomasyon özelliğini etkinleştirin. 
4. Bir Linux makinesinde, yüksek izinlere sahip olmanız gerekir. [İmza denetimlerini](automation-linux-hrw-install.md#turn-off-signature-validation)kapatmak için oturum açın.

## <a name="test-a-runbook"></a>Bir runbook'u test etme

Bir runbook 'u test ettiğinizde [taslak sürümü](#publish-a-runbook) yürütülür ve gerçekleştirdiği tüm işlemler tamamlanır. İş geçmişi oluşturulmaz, ancak [Çıkış](automation-runbook-output-and-messages.md#use-the-output-stream) ve [uyarı ve hata](automation-runbook-output-and-messages.md#working-with-message-streams) akışları test çıkış bölmesinde görüntülenir. [Ayrıntılı akışa](automation-runbook-output-and-messages.md#write-output-to-verbose-stream) Iletiler yalnızca [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) değişkeni olarak ayarlandıysa çıkış bölmesinde görüntülenir `Continue` .

Taslak sürümü çalıştırılsa da runbook hala normal şekilde yürütülür ve ortamdaki kaynaklara karşı herhangi bir eylem gerçekleştirir. Bu nedenle, yalnızca üretim dışı kaynaklarda runbook 'ları test etmelisiniz.

Her [runbook türünü](automation-runbook-types.md) test etme yordamı aynıdır. Azure portal metin Düzenleyicisi ile grafik Düzenleyicisi arasındaki sınamada fark yoktur.

1. Runbook 'un taslak sürümünü [metin düzenleyicisinde](automation-edit-textual-runbook.md) veya [grafik düzenleyicide](automation-graphical-authoring-intro.md)açın.
1. Test sayfasını açmak için **Test** ' e tıklayın.
1. Runbook 'ta parametreler varsa, bunlar sol bölmede listelenir ve burada test için kullanılacak değerleri sağlayabilirsiniz.
1. Bir [karma runbook çalışanı](automation-hybrid-runbook-worker.md)üzerinde testi çalıştırmak Istiyorsanız, **çalışma ayarlarını** **karma çalışan** olarak değiştirin ve hedef grubun adını seçin.  Aksi takdirde, testi bulutta çalıştırmak için varsayılan **Azure** 'u saklayın.
1. Testi başlatmak için **Başlat** ' a tıklayın.
1. Test edilirken bir [PowerShell Iş akışını](automation-runbook-types.md#powershell-workflow-runbooks) veya [grafik](automation-runbook-types.md#graphical-runbooks) runbook 'u durdurmak veya askıya almak için çıkış bölmesi altındaki düğmeleri kullanabilirsiniz. Bir runbook'u askıya aldığınızda, askıya alınmadan önce geçerli etkinliği tamamlar. Runbook askıya alındığında, durdurabilir veya yeniden başlatabilirsiniz.
1. Çıkış bölmesinde runbook 'tan çıktıyı inceleyin.

## <a name="publish-a-runbook"></a>Runbook yayımlama

Yeni bir runbook oluşturduğunuzda veya içeri aktardığınızda, çalıştırmadan önce onu yayımlamanız gerekir. Azure Otomasyonu 'ndaki her runbook 'un bir taslak sürümü ve yayımlanmış bir sürümü vardır. Yalnızca Yayımlanan sürüm çalıştırılabilir ve yalnızca Taslak sürüm düzenlenebilir. Yayımlanan sürüm Taslak sürümdeki herhangi bir değişiklikten etkilenmez. Taslak sürümü kullanılabilir hale getirilmeli hale geldiğinde, geçerli yayımlanmış sürümün üzerine bir taslak sürümü yazarak onu yayımlayın.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Azure portal runbook yayımlama

1. Runbook 'u Azure portal açın.
2. **Düzenle**’ye tıklayın.
3. Doğrulama iletisine yanıt olarak **Yayımla** ' ya ve ardından **Evet** ' e tıklayın.

### <a name="publish-a-runbook-using-powershell"></a>PowerShell kullanarak runbook yayımlama

Runbook 'unuzu yayımlamak için [Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) cmdlet 'ini kullanın. 

```azurepowershell-interactive
$aaName = "MyAutomationAccount"
$RunbookName = "Sample_TestRunbook"
$rgName = "MyResourceGroup"

$publishParams = @{
    AutomationAccountName = $aaName
    ResourceGroupName     = $rgName
    Name                  = $RunbookName
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Azure portalda runbook zamanlama

Runbook 'u yayımlandığında, işlem için zamanlayabilirsiniz:

1. Runbook 'u Azure portal açın.
2. **Kaynaklar** altında **zamanlamalar** ' ı seçin.
3. **Zamanlama Ekle ' yi** seçin.
4. Runbook 'U zamanla bölmesinde, **runbook 'una bir zamanlama bağla**' yı seçin.
5. Zamanlama bölmesinde **Yeni bir zamanlama oluştur ' a** tıklayın.
6. Yeni zamanlama bölmesine bir ad, açıklama ve diğer parametreleri girin.
7. Zamanlama oluşturulduktan sonra, vurgulayın ve **Tamam**' a tıklayın. Artık runbook 'ıza bağlanmalıdır.
8. Runbook 'un durumunu bildirmek için posta kutunuzda bir e-posta arayın.

## <a name="obtain-job-statuses"></a>İş durumlarını al

### <a name="view-statuses-in-the-azure-portal"></a>Azure portal durumları görüntüleme

Azure Automation 'da iş işleme ayrıntıları [işler](automation-runbook-execution.md#jobs)' de verilmiştir. Runbook işlerinizi görmeyi hazırsanız Azure portal kullanın ve otomasyon hesabınıza erişin. Sağ tarafta, **Iş istatistikleri**' nde tüm runbook işlerinin özetini görebilirsiniz.

![İş Istatistikleri kutucuğu](./media/manage-runbooks/automation-account-job-status-summary.png)

Özet, yürütülen her iş için iş durumunun bir sayısını ve grafik temsilini görüntüler.

Kutucuğa tıkladığınızda, yürütülen tüm işlerin özetlenen listesini içeren Işler sayfası sunulmaktadır. Bu sayfada her iş için durum, Runbook adı, başlangıç saati ve tamamlanma zamanı gösterilir.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="Işler sayfasının ekran görüntüsü.":::

İş listesini **filtre işleri**' ni seçerek filtreleyebilirsiniz. Belirli bir runbook 'u, iş durumunu veya açılan listeden bir seçimi filtreleyin ve arama için zaman aralığını belirtin.

![İş durumunu filtrele](./media/manage-runbooks/automation-account-jobs-filter.png)

Alternatif olarak, belirli bir runbook için iş Özeti ayrıntılarını Otomasyon hesabınızdaki runbook 'Lar sayfasından seçip **işler**' i seçerek görüntüleyebilirsiniz. Bu eylem Işler sayfasını gösterir. Buradan, ayrıntılarını ve çıktısını görüntülemek için bir iş kaydına tıklayabilirsiniz.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="Hatalar düğmesinin vurgulandığı Işler sayfasının ekran görüntüsü.":::

### <a name="retrieve-job-statuses-using-powershell"></a>PowerShell kullanarak iş durumlarını alma

Bir runbook için oluşturulan işleri ve belirli bir işin ayrıntılarını almak için [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) cmdlet 'ini kullanın. Kullanarak bir runbook başlatırsanız `Start-AzAutomationRunbook` , sonuçta elde edilen işi döndürür. İş çıktısını almak için [Get-Azautomationjoi put](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) komutunu kullanın.

Aşağıdaki örnek, örnek bir runbook 'un son işini alır ve durumunu, runbook parametreleri için belirtilen değerleri ve iş çıktısını görüntüler.

```azurepowershell-interactive
$getJobParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Runbookname           = 'Test-Runbook'
}
$job = (Get-AzAutomationJob @getJobParams | Sort-Object LastModifiedDate -Desc)[0]
$job | Select-Object JobId, Status, JobParameters

$getOutputParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Id                    = $job.JobId
    Stream                = 'Output'
}
Get-AzAutomationJobOutput @getOutputParams
```

Aşağıdaki örnek, belirli bir işin çıkışını alır ve her bir kaydı döndürür. Kayıtlardan biri için bir [özel durum](automation-runbook-execution.md#exceptions) varsa, komut dosyası değeri yerine özel durumu yazar. Bu davranış, özel durumlar çıkış sırasında normalde günlüğe kaydedilmeyeceğini daha fazla bilgi sağlayamadığı için yararlıdır.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Stream                = 'Any'
}
$output = Get-AzAutomationJobOutput @params

foreach ($item in $output) {
    $jobOutParams = @{
        AutomationAccountName = 'MyAutomationAccount'
        ResourceGroupName     = 'MyResourceGroup'
        Id                    = $item.StreamRecordId
    }
    $fullRecord = Get-AzAutomationJobOutputRecord @jobOutParams

    if ($fullRecord.Type -eq 'Error') {
        $fullRecord.Value.Exception
    } else {
        $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

* Runbook yönetiminin ayrıntılarını öğrenmek için bkz. [Azure Otomasyonu 'Nda runbook yürütme](automation-runbook-execution.md).
* PowerShell runbook 'unu hazırlamak için bkz. [Azure Automation 'da metin runbook 'Larını düzenleme](automation-edit-textual-runbook.md).
* Runbook yürütmeyle ilgili sorunları gidermek için bkz. [runbook sorunlarını giderme](troubleshoot/runbooks.md).
