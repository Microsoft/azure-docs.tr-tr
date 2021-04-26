---
title: Azure Otomasyonu için PowerShell Iş akışını öğrenin
description: Bu makalede, PowerShell Iş akışı ile PowerShell ile Otomasyon Runbook 'larına uygulanan kavramlar arasındaki farklar öğretilir.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b3beb8b3eda4dfabf9240aa328a24d5f855689b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833517"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>Azure Otomasyonu için PowerShell Iş akışını öğrenin

Azure Otomasyonu 'ndaki runbook 'lar Windows PowerShell iş akışları, Windows Workflow Foundation kullanan Windows PowerShell betikleri olarak uygulanır. İş akışı uzun süre çalışan görevleri gerçekleştiren veya birden çok aygıttaki veya yönetilen düğümlerdeki birden çok adımın koordinasyonu için gereken programlanmış, bağlantılı adımlardan oluşan bir dizidir. 

Bir iş akışı Windows PowerShell söz dizimi ile yazıldığı ve Windows PowerShell tarafından başlatıldığından, Windows Workflow Foundation tarafından işlenir. Bir iş akışının normal bir betik üzerinden avantajları, birden çok cihaza karşı bir eylemin eşzamanlı performansını ve hatalardan otomatik kurtarmasını içerir. 

> [!NOTE]
> PowerShell Iş akışı betiği, bir Windows PowerShell betiğine çok benzer ancak yeni bir kullanıcı için kafa karıştırıcı olabilecek bazı önemli farklılıklar içerir. Bu nedenle, runbook 'larınızı PowerShell Iş akışı kullanarak yalnızca [kontrol noktaları](#use-checkpoints-in-a-workflow)kullanmanız gerekiyorsa yazmanızı öneririz. 

Bu makaledeki konuların tüm ayrıntıları için bkz. [Windows PowerShell Iş akışını kullanmaya](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11))başlama.

## <a name="use-workflow-keyword"></a>Workflow anahtar sözcüğünü kullanma

PowerShell betiğini bir PowerShell iş akışına dönüştürmenin ilk adımı onu `Workflow` anahtar sözcüğüyle çevretir. Bir iş akışı, `Workflow` anahtar sözcüğüyle başlar ve ardından, parantez içine alınmış betiğin gövdesini izler. `Workflow`Aşağıdaki sözdiziminde gösterildiği gibi, iş akışının adı anahtar sözcüğünü izler:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

İş akışının adı, Otomasyon Runbook 'unun adıyla eşleşmelidir. Runbook içeri aktarıldıysa, dosya adının iş akışı adıyla eşleşmesi ve **. ps1** ile bitmesi gerekir.

İş akışına parametre eklemek için, `Param` anahtar sözcüğünü komut dosyasında olduğu gibi kullanın.

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>PowerShell Iş akışı kodu ve PowerShell betiği kodu arasındaki farkları öğrenin

PowerShell Iş akışı kodu, birkaç önemli değişiklik haricinde PowerShell betiği kodu ile neredeyse aynı şekilde görünür. Aşağıdaki bölümlerde, bir iş akışında çalışması için bir PowerShell betiğine yapmanız gereken değişiklikler açıklanır.

### <a name="activities"></a>Etkinlikler

Etkinlik, bir sırada gerçekleştirilen bir iş akışındaki belirli bir görevdir. Windows PowerShell Workflow birçok Windows PowerShell cmdlet'ini bir iş akışı içinde çalıştıklarında otomatik olarak etkinliklere dönüştürür. Runbook 'inizdeki bu cmdlet 'lerden birini belirttiğinizde, ilgili etkinlik Windows Workflow Foundation tarafından çalıştırılır. 

Bir cmdlet 'e karşılık gelen bir etkinlik yoksa, Windows PowerShell Iş akışı cmdlet 'i bir [InlineScript](#use-inlinescript) etkinliğinde otomatik olarak çalıştırır. Bazı cmdlet 'ler hariç tutulur ve bir InlineScript bloğuna açıkça eklemediğiniz takdirde bir iş akışında kullanılamaz. Daha fazla bilgi için bkz. [betik Iş akışlarında etkinlikleri kullanma](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11)).

İş akışı etkinlikleri çalışmalarını yapılandıran ortak parametreler kümesini paylaşır. Bkz. [about_WorkflowCommonParameters](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="positional-parameters"></a>Konumsal parametreler

Bir iş akışındaki etkinlikler ve cmdlet 'lerle konumsal parametreleri kullanamazsınız. Bu nedenle, parametre adlarını kullanmanız gerekir. Çalışan tüm hizmetleri alan aşağıdaki kodu göz önünde bulundurun:

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Bu kodu bir iş akışında çalıştırmayı denerseniz, `Parameter set cannot be resolved using the specified named parameters.` Bu sorunu düzeltmek için aşağıdaki örnekte gösterildiği gibi bir ileti alırsınız:

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Seri durumdan çıkarılan nesneler

İş akışlarında nesnelerin serisi kaldırılır, yani özellikleri hala kullanılabilir, ancak yöntemlerine uygulanmaz.  Örneğin, bir hizmeti nesnenin yöntemini kullanarak durduran aşağıdaki PowerShell kodunu göz önünde bulundurun `Stop` `Service` .

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Bunu bir iş akışında çalıştırmayı denerseniz, şunu bildiren bir hata alırsınız `Method invocation is not supported in a Windows PowerShell Workflow.`

Bir seçenek, bu iki kod satırını bir [InlineScript](#use-inlinescript) bloğunda sarmalıdır. Bu durumda, `Service` blok içindeki bir hizmet nesnesini temsil eder.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Başka bir seçenek de, varsa yöntemiyle aynı işlevselliğe sahip başka bir cmdlet kullanmaktır. Örneğimizde `Stop-Service` cmdlet, yöntemiyle aynı işlevselliği sağlar `Stop` ve bir iş akışı için aşağıdaki kodu kullanabilirsiniz.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>InlineScript kullanma

`InlineScript`Etkinlik, PowerShell iş akışı yerine bir veya daha fazla komutu geleneksel PowerShell betiği olarak çalıştırmanız gerektiğinde faydalıdır.  Bir iş akışındaki komutlar işlenmek üzere Windows Workflow Foundation'a gönderilirken, bir InlineScript bloğundaki komutlar Windows PowerShell tarafından işlenir.

InlineScript aşağıda gösterilen aşağıdaki sözdizimini kullanır.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Çıktıyı bir değişkene atayarak bir InlineScript öğesinden çıkış geri alabilirsiniz. Aşağıdaki örnek bir hizmeti durdurup hizmet adını verir.

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Değerleri bir InlineScript bloğuna geçirebilirsiniz, ancak **$using** kapsam değiştiricisini kullanmanız gerekir.  Aşağıdaki örnek, hizmet adının bir değişken tarafından sağlanması dışında, önceki örnekle aynıdır.

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

InlineScript etkinlikleri belirli iş akışlarında kritik olabilir, ancak iş akışı yapılarını desteklemez. Onları yalnızca aşağıdaki nedenlerden dolayı kullanmanız gerekir:

* Bir InlineScript bloğunun içinde [kontrol noktaları](#use-checkpoints-in-a-workflow) kullanamazsınız. Blok içinde bir hata oluşursa, bloğun başından önce sürdürülmesi gerekir.
* Bir InlineScript bloğu içinde [paralel yürütme](#use-parallel-processing) kullanamazsınız.
* InlineScript, InlineScript bloğunun tüm uzunluğu için Windows PowerShell oturumunu tutduğundan, iş akışının ölçeklenebilirliğini etkiler.

InlineScript kullanma hakkında daha fazla bilgi için bkz. [bir Iş akışında Windows PowerShell komutlarını çalıştırma](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11)) ve [about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript).

## <a name="use-parallel-processing"></a>Paralel işleme kullan

Windows PowerShell İş Akışlarının bir avantajı da bir komutlar kümesini tipik bir betikteki gibi sırayla yürütmek yerine paralel olarak gerçekleştirebilmesidir.

`Parallel`Aynı anda çalışan birden çok komut içeren bir betik bloğu oluşturmak için anahtar sözcüğünü kullanabilirsiniz. Bu, aşağıda gösterildiği gibi aşağıdaki sözdizimini kullanır. Bu durumda, Activity1 ve Activity2 aynı anda başlar. Activity3 yalnızca Activity1 ve Activity2 tamamlandıktan sonra başlar.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Örneğin, bir ağ hedefine birden çok dosya kopyalamak için aşağıdaki PowerShell komutlarını göz önünde bulundurun. Bir sonraki başlamadan önce bir dosyanın kopyalamayı tamamlaması gereken bu komutlar sırayla çalıştırılır.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Aşağıdaki iş akışı, aynı komutları aynı anda kopyalamaya başlamak için paralel olarak çalıştırır.  Yalnızca tüm kopyalandıktan sonra, görüntülenen tamamlanma iletisi görüntülenir.

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

Bir koleksiyondaki her öğe için komutları eşzamanlı olarak işlemek için `ForEach -Parallel` yapısını kullanabilirsiniz. Betik bloğundaki komutlar sırayla yürütülürken koleksiyondaki öğeler paralel olarak işlenir. Bu işlem aşağıda gösterilen sözdizimini kullanır. Bu durumda, Activity1 koleksiyondaki tüm öğeler için aynı anda başlar. Her öğe için Activity2, Activity1 tamamlandıktan sonra başlar. Activity3, yalnızca Activity1 ve Activity2 tüm öğeler için tamamlandıktan sonra başlar. `ThrottleLimit`Paralellik sınırlamak için parametresini kullanıyoruz. Çok yüksek bir, `ThrottleLimit` soruna neden olabilir. Parametresinin ideal değeri, `ThrottleLimit` ortamınızdaki birçok faktöre bağlıdır. Düşük bir değer ile başlayın ve belirli bir durumda çalışacak bir tane bulana kadar artan değerleri farklı şekilde deneyin.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Aşağıdaki örnek, dosyaları paralel olarak kopyalamaya yönelik bir önceki örneğe benzerdir.  Bu durumda, her dosya için kopyaladıktan sonra bir ileti görüntülenir.  Yalnızca tüm kopyalandıktan sonra, görüntülenen son tamamlanma iletisi görüntülenir.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Bu durum güvenilir olmayan sonuçlara izin vermek üzere gösterildiğinden, alt runbook 'ların paralel olarak çalıştırılmasını önermiyoruz. Alt runbook 'un çıkışı bazen gösterilmez ve bir alt runbook 'taki ayarlar diğer paralel alt runbook 'ları etkileyebilir. `VerbosePreference`, Ve gibi değişkenler `WarningPreference` alt runbook 'lara yayılmayabilir. Alt runbook bu değerleri değiştirirse, bu değerler, çağrıdan sonra düzgün şekilde geri yüklenmeyebilir.

## <a name="use-checkpoints-in-a-workflow"></a>Bir iş akışında kontrol noktaları kullanma

Denetim noktası, değişkenlerin geçerli değerlerini ve bu noktaya oluşturulan tüm çıktıları içeren iş akışının geçerli durumunun anlık görüntüsüdür. Bir iş akışı hatayla sona ererse veya askıya alınırsa, başlangıçta başlamak yerine, bir sonraki sefer çalıştırıldığında son denetim noktasından başlar. 

Etkinlik ile bir iş akışında denetim noktası ayarlayabilirsiniz `Checkpoint-Workflow` . Azure Otomasyonu, diğer runbook 'ların çalışmasına izin vermek için, üç saat boyunca çalışan herhangi bir runbook 'un kaldırılabileceği, [dengeli](automation-runbook-execution.md#fair-share)bir özellik adlı bir özelliğe sahiptir. Sonuç olarak, yüklenmeyen runbook yeniden yüklenir. Olduğunda, runbook 'ta gerçekleştirilen son denetim noktasından yürütmeyi sürdürür.

Runbook 'un sonunda tamamlanmasını garantilemek için, üç saatten az bir süre çalışan aralıklarla kontrol noktaları eklemeniz gerekir. Her çalıştırma sırasında yeni bir kontrol noktası eklenirse ve bir hata nedeniyle runbook üç saat sonra çıkarılirse, runbook süresiz olarak sürdürülür.

Aşağıdaki örnekte, Activity2 sonrasında bir özel durum oluşur ve iş akışının sonuna olur. İş akışı yeniden çalıştırıldığında, bu etkinlik son denetim noktası kümesinden hemen sonra olduğundan, Activity2 çalıştırılarak başlatılır.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Özel duruma neden olabilecek etkinliklerden sonra bir iş akışında kontrol noktaları ayarlayın ve iş akışı devam ettirirse yinelenmemelidir. Örneğin, iş akışınız bir sanal makine oluşturabilir. Sanal makineyi oluşturmak için komutlardan önce ve sonra bir denetim noktası ayarlayabilirsiniz. Oluşturma başarısız olursa, iş akışı yeniden başlatılırsa komutlar yinelenir. Oluşturma başarılı olduktan sonra iş akışı başarısız olursa, iş akışı sürdürüldüğünde sanal makine yeniden oluşturulmaz.

Aşağıdaki örnekte birden çok dosya bir ağ konumuna kopyalanır ve her dosyadan sonra bir denetim noktası ayarlanır.  Ağ konumu kaybolursa, iş akışı hata ile sona erer.  Yeniden başlatıldığında, son denetim noktasında sürdürülür. Yalnızca önceden kopyalanmış dosyalar atlanır.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

[Askıya alma Iş akışı](/powershell/module/psworkflow/about/about_suspend-workflow) etkinliğini veya son kontrol noktasından sonra Kullanıcı adı kimlik bilgileri kalıcı olmadığından, kimlik bilgilerini null olarak ayarlamanız ve ardından `Suspend-Workflow` veya denetim noktası çağrıldıktan sonra bunları varlık deposundan yeniden almanız gerekir.  Aksi takdirde, aşağıdaki hata iletisini alabilirsiniz: `The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

Aşağıdaki aynı kodda PowerShell Iş akışı runbook 'larınızda bu durumun nasıl işleneceği gösterilmektedir.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzAutomationCredential -Name "MyCredential"
    $null = Connect-AzAccount -Credential $Cred

    $VmsToCreate = Get-AzAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzVM -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzAutomationCredential -Name "MyCredential"
        $null = Connect-AzAccount -Credential $Cred
        }
}
```

> [!NOTE]
> Grafik olmayan PowerShell runbook 'ları için `Add-AzAccount` ve `Add-AzureRMAccount` [Connect-azaccount](/powershell/module/az.accounts/connect-azaccount)için diğer adlar. Bu cmdlet 'leri kullanabilir veya Otomasyon hesabınızdaki [modüllerinizi](automation-update-azure-modules.md) en son sürümlere güncelleştirebilirsiniz. Yeni bir Otomasyon hesabı oluşturmuş olsanız bile modüllerinizi güncelleştirmeniz gerekebilir. Hizmet sorumlusu ile yapılandırılmış bir farklı çalıştır hesabı kullanarak kimlik doğrulaması yapıyorsanız, bu cmdlet 'lerin kullanılması gerekli değildir.

Denetim noktalarıyla ilgili daha fazla bilgi için bkz. [Betik İş Akışına Denetim Noktaları Ekleme](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11)).

## <a name="next-steps"></a>Sonraki adımlar

* PowerShell Iş akışı runbook 'ları hakkında bilgi edinmek için bkz. [öğretici: PowerShell Iş akışı runbook 'U oluşturma](learn/automation-tutorial-runbook-textual.md).
