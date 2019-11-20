---
title: Azure Otomasyonu 'nda runbook yürütmesi
description: Azure Otomasyonu 'ndaki bir runbook 'un nasıl işlendiği hakkındaki ayrıntıları açıklar.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2f8fa4c378ed394930a4018c58b99ed919cbc2c2
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73886960"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure Otomasyonu 'nda runbook yürütmesi

Azure Otomasyonu 'nda bir runbook başlattığınızda bir iş oluşturulur. Bir iş, runbook 'un tek bir yürütme örneğidir. Her işi çalıştırmak için bir Azure Otomasyonu çalışanı atanır. Çalışanlar birçok Azure hesabı tarafından paylaşıldığında, farklı Otomasyon hesaplarından gerçekleştirilen işler diğerinden yalıtılmıştır. İşiniz için hangi çalışan hizmetleri için istek olduğunu denetleyin. Tek bir runbook 'un tek seferde çalışan çok sayıda işi olabilir. Aynı Otomasyon hesabından gerçekleştirilen işlerin yürütme ortamı yeniden kullanılabilir. Aynı anda çalıştırdığınız daha fazla iş, aynı korumalı alana daha fazla gönderilebilir. Aynı korumalı alan işleminde çalışan işler birbirini etkileyebilir, bir örnek `Disconnect-AzureRMAccount` cmdlet 'ini çalıştırıyor olabilir. Bu cmdlet 'in çalıştırılması, paylaşılan korumalı alan işlemindeki her runbook işinin bağlantısını keser. Azure portal runbook 'ların listesini görüntülediğinizde, her runbook için başlatılan tüm işlerin durumunu listeler. Her runbook 'un durumunu izlemek için işlerin listesini görüntüleyebilirsiniz. İş günlükleri en fazla 30 gün boyunca depolanır. Farklı iş durumları [Iş durumlarının](#job-statuses)açıklaması için.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Aşağıdaki diyagramda [PowerShell runbook](automation-runbook-types.md#powershell-runbooks)'ları, [grafik runbook](automation-runbook-types.md#graphical-runbooks) 'Lar ve [PowerShell iş akışı runbook 'ları](automation-runbook-types.md#powershell-workflow-runbooks)için bir runbook işinin yaşam döngüsü gösterilmektedir.

![İş durumları-PowerShell Iş akışı](./media/automation-runbook-execution/job-statuses.png)

İşlerinizin Azure aboneliğinize bir bağlantı yaparak Azure kaynaklarınıza erişimi vardır. Yalnızca bu kaynaklara genel buluttan erişilebiliyorsa, veri merkezinizdeki kaynaklara erişimi vardır.

## <a name="where-to-run-your-runbooks"></a>Runbook 'larınızın çalıştırılacağı konum

Azure Otomasyonu 'ndaki runbook 'lar, Azure 'da veya [karma Runbook Worker](automation-hybrid-runbook-worker.md)üzerinde bir korumalı alan üzerinde çalışabilir. Korumalı alan, Azure 'da birden çok iş tarafından kullanılabilen paylaşılan bir ortamdır. Aynı korumalı alanı kullanan işler, korumalı alanın kaynak sınırlamalarına göre bağlanır. Karma runbook çalışanları, runbook 'ları doğrudan rolü barındıran bilgisayarda ve bu yerel kaynakları yönetmek için ortamdaki kaynaklara karşı çalıştırabilir. Runbook 'lar Azure Otomasyonu 'nda depolanır ve yönetilir ve ardından bir veya daha fazla atanmış bilgisayara gönderilir. Çoğu runbook 'lar Azure korumalı alanında kolayca çalıştırılabilir. Runbook 'unuzu yürütmek için bir Azure korumalı alanı üzerinden karma runbook seçmenin önerilme senaryosu vardır. Bazı örnek senaryolar listesi için aşağıdaki tabloya bakın:

|Görev|En iyi seçim|Notlar|
|---|---|---|
|Azure kaynaklarıyla tümleştirme|Azure korumalı alanı|Azure 'da barındırılan kimlik doğrulama daha basittir. Azure VM 'de karma runbook çalışanı kullanıyorsanız, [Azure kaynakları için Yönetilen kimlikler](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) kullanabilirsiniz|
|Azure kaynaklarını yönetmek için en iyi performans|Azure korumalı alanı|Betik aynı ortamda çalışır ve bu da daha az gecikme süresine sahiptir|
|İşlem maliyetlerini en aza indir|Azure korumalı alanı|İşlem yükü yok, VM 'ye gerek yok|
|Uzun süre çalışan betik|Karma Runbook Çalışanı|Azure korumalı alanlar, [kaynaklarla ilgili sınırlamalara](../azure-subscription-service-limits.md#automation-limits) sahiptir|
|Yerel hizmetlerle etkileşim kurma|Karma Runbook Çalışanı|Doğrudan konak makinesine erişebilir|
|Üçüncü taraf yazılım ve yürütülebilir dosyaları gerektir|Karma Runbook Çalışanı|İşletim sistemini yönetiyoruz ve yazılım yükleyebilir|
|Runbook ile bir dosyayı veya klasörü izleme|Karma Runbook Çalışanı|Karma Runbook Worker üzerinde [izleyici görevi](automation-watchers-tutorial.md) kullanma|
|Yoğun kaynak kullanımı betiği|Karma Runbook Çalışanı| Azure korumalı alanlar, [kaynaklarla ilgili sınırlamalara](../azure-subscription-service-limits.md#automation-limits) sahiptir|
|Belirli gereksinimlere sahip modülleri kullanma| Karma Runbook Çalışanı|Bazı örnekler şunlardır:</br> **WinSCP** -WinSCP. exe ' de bağımlılık </br> **Iısadministration** -IIS 'nin etkinleştirilmesini gerektirir|
|Yükleyici gerektiren modülü yükleme|Karma Runbook Çalışanı|Korumalı alan için modüller copable olmalıdır|
|4\.7.2 'ten farklı .NET Framework gerektiren runbook 'ları veya modülleri kullanma|Karma Runbook Çalışanı|Automation korumalı alanlar .NET Framework 4.7.2 sahiptir ve bunu yükseltmenin bir yolu yoktur|
|Yükseltme gerektiren betikler|Karma Runbook Çalışanı|Korumalı alanlar yükseltmeye izin vermez. Bunu çözümlemek için karma bir runbook worker kullanın ve yükseltme gerektiren komutu çalıştırırken UAC 'yi kapatabilir ve `Invoke-Command` kullanabilirsiniz|
|WMI erişimi gerektiren betikler|Karma Runbook Çalışanı|Bulutta korumalı alanlar üzerinde çalışan işlerin [WMI erişimi yok](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Runbook davranışı

Runbook 'lar, içinde tanımlanan mantığa göre yürütülür. Bir runbook kesintiye uğrarsa, runbook başlangıcında yeniden başlatılır. Bu davranış, geçici sorunlar varsa runbook 'ların yeniden başlatılmaları gerektiği şekilde yazılmasını gerektirir.

Azure korumalı alanında çalıştırılan bir runbook 'tan başlatılan PowerShell işleri tam dil modunda çalışmayabilir. PowerShell dil modları hakkında daha fazla bilgi için bkz. [PowerShell dil modları](/powershell/module/microsoft.powershell.core/about/about_language_modes). Azure Otomasyonu 'nda işlerle etkileşime geçme hakkında daha fazla bilgi için bkz. [PowerShell ile iş durumunu alma](#retrieving-job-status-using-powershell)

### <a name="creating-resources"></a>Kaynak oluşturma

Betiğinizin kaynakları oluşturması durumunda, yeniden oluşturmayı denemeden önce kaynağın zaten mevcut olup olmadığını denetlemeniz gerekir. Aşağıdaki örnekte temel bir örnek gösterilmektedir:

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependant-scripts"></a>Zamana bağımlı betikler

Runbook 'ları yazarken dikkatli bir dikkat edilmelidir. Daha önce belirtildiği gibi, runbook 'ların sağlam olmaları ve Runbook 'un yeniden başlatılmasına veya başarısız olmasına neden olabilecek geçici hataları işleyebilecekleri bir şekilde yazılması gerekir. Bir runbook başarısız olursa, yeniden denenir. Bir runbook normalde bir zaman kısıtlaması içinde çalışırsa, başlatma, kapatma ya da genişleme gibi işlemleri yalnızca belirli zamanlarda çalıştırmak için Runbook 'ta yürütme zamanının uygulanması için mantık uygulanmalıdır.

> [!NOTE]
> Azure korumalı alan işlemindeki yerel saat UTC zamanına ayarlanır. Runbook 'larınızda tarih ve saat hesaplamaları bunun göz önüne alınması gerekir.

### <a name="tracking-progress"></a>İlerlemeyi izleme

Runbook 'ların doğası gereği modüler olması iyi bir uygulamadır. Bu, runbook 'taki mantığı yeniden kullanılabilmesi ve kolayca yeniden başlayabileceği şekilde yapılandırmak anlamına gelir. Bir runbook 'ta ilerlemeyi izlemek, sorun oluşursa runbook 'taki mantığın doğru bir şekilde yürütüldüğünü sağlamak için iyi bir yoldur. Runbook 'un ilerlemesini izlemenin bazı olası yolları, depolama hesapları, veritabanı veya paylaşılan dosyalar gibi bir dış kaynak kullanmaktır. Durumu dışarıdan izleyerek runbook 'ınızdaki mantığı oluşturarak runbook 'un aldığı son eylemin durumunu kontrol edebilirsiniz. Ardından sonuçları temel alarak runbook 'taki belirli görevleri atlayın veya devam edin.

### <a name="prevent-concurrent-jobs"></a>Eş zamanlı işleri engelle

Bazı runbook 'lar aynı anda birden çok iş üzerinde çalışıyorsa Strangely davranabilir. Bu durumda, bir runbook 'un zaten çalışan bir işe sahip olup olmadığını denetlemek için mantık uygulamak önemlidir. Aşağıdaki örnekte, bu davranışı nasıl yapabiliriz gösteren temel bir örnek gösterilmektedir:

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>Birden fazla abonelik ile çalışma

Birden çok abonelikle ilgilenen runbook 'ları yazarken, runbook 'unuz, kimlik doğrulama bağlamınızın aynı korumalı alanda çalışan başka bir runbook 'tan alınmadığından emin olmak için [Disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) cmdlet 'ini kullanır. Daha sonra, `AzureRM` cmdlet 'lerinde `-AzureRmContext` parametresini kullanmanız ve uygun bağlamınızı geçirmeniz gerekir.

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Özel durum işleme

Betikler yazarken, özel durumları ve potansiyel aralıklı sorunları işleyebilmek önemlidir. Runbook 'larınızla ilgili özel durumları veya aralıklı sorunları işlemenin bazı farklı yolları aşağıda verilmiştir:

#### <a name="erroractionpreference"></a>$ErrorActionPreference

[$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) tercih değişkeni, PowerShell 'in Sonlandırıcı olmayan bir hataya nasıl yanıt vereceğini belirler. Hata sonlandırma `$ErrorActionPreference`bundan etkilenmez, her zaman sonlandırılır. `$ErrorActionPreference`kullanarak, `Get-ChildItem` cmdlet 'inden `PathNotFound` gibi Normalde Sonlandırılmamış bir hata runbook 'un tamamlanmasını durdurur. Aşağıdaki örnekte `$ErrorActionPreference`kullanımı gösterilmektedir. Komut dosyası durulacağı için son `Write-Output` satırı hiçbir şekilde yürütülmeyecektir.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Son catch 'i deneyin

[Deneme](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) hatalarını işlemenize yardımcı olması için PowerShell betiklerinizde catch 'i deneyin. Try catch 'i kullanarak belirli özel durumları veya genel özel durumları yakalayabilirsiniz. Catch ifadesinin hataları izlemek için kullanılması veya hatayı işlemeye çalışmak için kullanılması gerekir. Aşağıdaki örnek varolmayan bir dosyayı indirmeye çalışır. Bir özel durum oluşursa, son değer döndürülen `System.Net.WebException` özel durumu yakalar.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Yaratır

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) , sonlandırma hatası oluşturmak için kullanılabilir. Bu, bir runbook 'ta kendi mantığınızı tanımlarken yararlı olabilir. Komut dosyasını durduran belirli bir ölçüt karşılanıyorsa, betiği durdurmak için `throw` kullanabilirsiniz. Aşağıdaki örnek, `throw`kullanarak makine için gereken bir işlev parametresini gösterir.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Yürütülebilir dosyaları veya çağırma süreçlerini kullanma

Azure korumalı alanında çalışan runbook 'lar, işlemlerin (örneğin, bir. exe veya alt çalışma. Call) çağrılmasını desteklemez. Bunun nedeni, Azure sanal, kapsayıcılarda çalıştırılan ve temel alınan tüm API 'lere erişimi olmayan paylaşılan işlemlerdir. 3\. taraf yazılımı veya alt işlemlerin çağrılmasını gerektiren senaryolarda, runbook 'u [karma Runbook Worker](automation-hybrid-runbook-worker.md)üzerinde yürütmeniz önerilir.

### <a name="device-and-application-characteristics"></a>Cihaz ve uygulama özellikleri

Azure korumalı alanında çalıştırılan runbook işlerinin hiçbir cihaza veya uygulama özelliklerine erişimi yoktur. Windows üzerinde performans ölçümlerini sorgulamak için kullanılan en yaygın API, WMI 'dır. Bu ortak ölçümlerin bazıları bellek ve CPU kullanımlarıdır. Ancak, hangi API 'nin kullanılması önemlidir. Bulutta çalışan işlerin, cihaz ve uygulama özelliklerini tanımlamaya yönelik sektör standartları olan Genel Bilgi Modeli (CıM) üzerinde oluşturulmuş Microsoft Web tabanlı kuruluş yönetimi (WBEM) uygulamasına erişimi yoktur.

## <a name="job-statuses"></a>İş durumları

Aşağıdaki tabloda, bir iş için olası farklı durumlar açıklanmaktadır. PowerShell 'de iki tür hata, sonlandırma ve sonlandırma olmayan hatalar vardır. Hataların sonlandırılması, meydana gelir durumunda runbook durumunu **başarısız** olarak ayarlar. Sonlandırılmamış hatalar, komut dosyasının gerçekleştikten sonra bile devam etmesine izin verir. Sonlandırıcı olmayan bir hata örneği, mevcut olmayan bir yol ile `Get-ChildItem` cmdlet 'ini kullanmaktır. PowerShell yolun mevcut olmadığını, bir hata olduğunu ve sonraki klasöre devam ettiğini görür. Bu hata runbook durumunu **başarısız** olarak ayarlanmamış ve **tamamlandı**olarak işaretlenebilir. Bir runbook 'u Sonlandırıcı olmayan bir hata üzerinde durdurmaya zorlamak için cmdlet üzerinde `-ErrorAction Stop` kullanabilirsiniz.

| Durum | Açıklama |
|:--- |:--- |
| Tamamlandı |İş başarıyla tamamlandı. |
| Başarısız |[Grafik ve PowerShell Iş akışı runbook 'ları](automation-runbook-types.md)için Runbook derlenemedi. [PowerShell betiği runbook 'ları](automation-runbook-types.md)için Runbook başlatılamadı veya iş bir özel duruma sahipti. |
| Başarısız, kaynak bekleniyor |İş başarısız oldu çünkü bu, [dengeli](#fair-share) bir şekilde, her seferinde ve aynı denetim noktasından ya da runbook 'un başlangıcından her seferinde başlatılmış |
| Kuyruktan |İş, bir Otomasyon çalışanındaki kaynakların başlatılabilmesini sağlamak için kullanılabilir hale gelmesini bekliyor. |
| Başlatılıyor |İş bir çalışana atandı ve sistem tarafından başlatılıyor. |
| Etmek |Sistem askıya alındıktan sonra işi sürdürüyor. |
| Çalışıyor |İş çalışıyor. |
| Çalışıyor, kaynakları bekliyor |İş, [dengeli bir paylaşımın](#fair-share) sınırına ulaştığından kaldırıldı. Kısa süre içinde son denetim noktasından devam eder. |
| Durduruldu |İş tamamlanmadan önce Kullanıcı tarafından durduruldu. |
| Durduruluyor |Sistem işi durduruyor. |
| Alın |İş, Kullanıcı tarafından, sistem tarafından veya Runbook 'taki bir komut tarafından askıya alındı. Bir runbook 'ta bir denetim noktası yoksa, runbook 'un başından başlatılır. Bir denetim noktası varsa, yeniden başlayabilir ve son denetim noktasından sürdürebilirsiniz. Runbook yalnızca bir özel durum oluştuğunda sistem tarafından askıya alınır. Varsayılan olarak, ErrorActionPreference devam olarak ayarlanır, yani iş bir hata üzerinde çalışmaya devam **eder**. Bu tercih değişkeni **Durdur**olarak ayarlandıysa, iş bir hata üzerinde askıya alınır. Yalnızca [grafik ve PowerShell Iş akışı runbook 'ları](automation-runbook-types.md) için geçerlidir. |
| Askıya alma |Sistem, kullanıcının isteği sırasında işi askıya almaya çalışıyor. Runbook, askıya alınmadan önce bir sonraki denetim noktasına ulaşmalıdır. Son denetim noktasını zaten geçirtiyse, askıya alınmadan önce tamamlanır. Yalnızca [grafik ve PowerShell Iş akışı runbook 'ları](automation-runbook-types.md) için geçerlidir. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Azure portal iş durumunu görüntüleme

Tüm runbook işlerinin özetlenen durumunu görüntüleyebilir veya Azure portal belirli bir runbook işinin ayrıntılarına gidebilirsiniz. Ayrıca, runbook iş durumu ve iş akışlarını iletmek için Log Analytics çalışma alanınız ile tümleştirmeyi yapılandırabilirsiniz. Azure Izleyici günlükleriyle tümleştirme hakkında daha fazla bilgi için bkz. [Otomasyon 'Dan Azure izleyici günlüklerine iş durumu ve iş akışları iletme](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Otomasyon Runbook işleri Özeti

Seçtiğiniz Otomasyon hesabınızın sağında, **Iş istatistikleri** kutucuğunun altındaki tüm runbook işlerinin özetini görebilirsiniz.

![İş Istatistikleri kutucuğu](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Bu kutucuk, yürütülen tüm işlerin iş durumunun bir sayısını ve grafik temsilini görüntüler.

Kutucuğa tıkladığınızda, yürütülen tüm işlerin özetlenen listesini içeren **işler** sayfası sunulmaktadır. Bu sayfada durum, başlangıç zamanları ve tamamlanma süreleri gösterilmektedir.

![Otomasyon hesabı Işleri sayfası](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

İşleri **Filtrele** ' yi seçerek iş listesini filtreleyerek belirli bir runbook 'ta, iş durumunda veya açılan listeden ve arama yapılacak zaman aralığından filtre uygulayabilirsiniz.

![Iş durumunu filtrele](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Alternatif olarak, Otomasyon hesabınızdaki runbook **'lar** sayfasından söz konusu runbook 'u seçerek belirli bir runbook için iş Özeti ayrıntılarını görüntüleyebilirsiniz ve sonra **işler** kutucuğunu seçebilirsiniz. Bu eylem **işler** sayfasını gösterir ve buradan ayrıntılarını ve çıktısını görüntülemek için iş kaydına tıklayabilirsiniz.

![Otomasyon hesabı Işleri sayfası](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>İş Özeti

Belirli bir runbook için oluşturulmuş tüm işlerin listesini ve en son durumunu görüntüleyebilirsiniz. Bu listeyi iş durumuna ve iş için yapılan son değişikliğin tarih aralığına göre filtreleyebilirsiniz. Ayrıntılı bilgilerini ve çıktısını görüntülemek için bir işin adına tıklayın. İşin ayrıntılı görünümü, bu iş için sağlanmış olan runbook parametrelerinin değerlerini içerir.

Bir runbook işlerini görüntülemek için aşağıdaki adımları kullanabilirsiniz.

1. Azure portal **Otomasyon** ' u seçin ve ardından bir Otomasyon hesabının adını seçin.
2. Hub 'dan **runbook 'lar** ' ı seçin ve ardından **runbook 'lar** sayfasında listeden bir runbook seçin.
3. Seçili runbook 'un sayfasında **işler** kutucuğuna tıklayın.
4. Listedeki işlerden birine ve runbook iş ayrıntıları sayfasında, ayrıntılarını ve çıktısını görüntüleyebilirsiniz.

## <a name="retrieving-job-status-using-powershell"></a>PowerShell kullanarak iş durumunu alma

Bir runbook için oluşturulan işleri ve belirli bir işin ayrıntılarını almak için [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) komutunu kullanabilirsiniz. [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook)kullanarak PowerShell ile bir runbook başlatırsanız, sonuçta elde edilen işi döndürür. Bir işin çıkışını almak için [Get-Azurermautomationjoi put](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) komutunu kullanın.

Aşağıdaki örnek komutlar, örnek bir runbook 'un son işini alır ve durumunu, runbook parametreleri için belirtilen değerleri ve işten alınan çıktıyı görüntüler.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Aşağıdaki örnek, belirli bir işin çıkışını alır ve her bir kaydı döndürür. Kayıtlardan biri için bir özel durum olduğu durumda, özel durum değer yerine dışarı yazılır. Bu davranış, özel durumlar genellikle çıkış sırasında günlüğe kaydedilmeyeceğini etkileyebilecek ek bilgiler sağlayabileceği için yararlıdır.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>Etkinlik günlüğünden ayrıntıları al

Runbook 'u başlatan kişi veya hesap gibi diğer ayrıntılar, Otomasyon hesabı için etkinlik günlüğünden alınabilir. Aşağıdaki PowerShell örneği, son kullanıcıyı söz konusu runbook 'u çalıştırmak için sağlar:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>Dengeli bir paylaşma

Azure Otomasyonu, bulutta bulunan tüm runbook 'lar arasında kaynak paylaşmak için, üç saatten uzun bir süreyle çalışan işleri geçici olarak kaldırır veya sonlandırır. [PowerShell tabanlı runbook 'lar](automation-runbook-types.md#powershell-runbooks) ve [Python runbook 'ların](automation-runbook-types.md#python-runbooks) işleri durdurulur ve yeniden başlatılmaz ve iş durumu durdurulmuş olarak gösterilir.

Uzun süre çalışan görevler için [karma Runbook Worker](automation-hrw-run-runbooks.md#job-behavior)kullanılması önerilir. Karma runbook çalışanları, dengeli bir paylaşımdan sınırlı değildir ve bir runbook 'un ne kadar süreyle yürütülemediği konusunda bir sınırlama yoktur. Diğer iş sınırları hem Azure sanal [değerleri](../azure-subscription-service-limits.md#automation-limits) hem de karma runbook çalışanları için geçerlidir. Karma runbook çalışanları 3 saatlik bir paylaşılan sınır ile sınırlı olmasa da, runbook 'ların üzerinde çalıştığı, beklenmeyen yerel altyapı sorunlarından yeniden başlatma davranışlarını destekleyecek şekilde geliştirilebilmelidir.

Diğer bir seçenek de runbook 'u alt runbook 'ları kullanarak iyileştirmeniz olur. Runbook 'iniz birkaç veritabanında bir veritabanı işlemi gibi birkaç kaynak üzerinde aynı işlevle döngüye alıyorsa, bu işlevi bir [alt runbook](automation-child-runbooks.md) 'a taşıyabilir ve [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet 'i ile çağırabilirsiniz. Bu alt runbook 'ların her biri ayrı işlemlerde paralel olarak yürütülür. Bu davranış, üst runbook 'un tamamlanacağı toplam süreyi düşürür. Alt runbook tamamlandıktan sonra gerçekleştirilen işlemler varsa, her bir alt öğe için iş durumunu denetlemek üzere runbook 'inizdeki [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) cmdlet 'ini kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Otomasyonu 'nda bir runbook başlatmak için kullanılabilecek farklı yöntemler hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu 'nda runbook başlatma](automation-starting-a-runbook.md)
* Dil başvurusu ve öğrenme modülleri de dahil olmak üzere PowerShell hakkında daha fazla bilgi için [PowerShell belgelerine](https://docs.microsoft.com/powershell/scripting/overview)bakın.
