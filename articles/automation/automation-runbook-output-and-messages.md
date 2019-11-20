---
title: Azure Otomasyonu 'nda Runbook çıkışı ve Iletileri
description: Azure Automation 'da runbook 'lardan çıkış ve hata iletilerinin nasıl oluşturulduğunu ve alınacağını açıklar.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: df0842635d13180951f8d79a598fe0708ca64b8a
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850533"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Azure Otomasyonu 'nda Runbook çıkışı ve iletileri

Çoğu Azure Otomasyonu runbook 'un bir çıktı biçimi vardır. Bu çıktı kullanıcıya veya başka bir runbook ile kullanmak istediğiniz karmaşık bir nesneye yönelik bir hata iletisi olabilir. Windows PowerShell, bir betikten veya iş akışından çıkış göndermek için [birden çok akış](/powershell/module/microsoft.powershell.core/about/about_redirection) sağlar. Azure Otomasyonu, bu akışların her biriyle farklı şekilde çalışır. Runbook oluştururken her birinin kullanımı için en iyi uygulamaları izlemeniz gerekir.

Aşağıdaki tabloda her bir akışın kısa bir açıklaması ve yayımlanan runbook 'lar için Azure portal ve [bir runbook test](automation-testing-runbook.md)edilirken davranışları verilmiştir. Sonraki bölümlerde, her akış hakkında daha fazla ayrıntı sağlanır.

| Akış | Açıklama | Yayımlanmış | Test |
|:--- |:--- |:--- |:--- |
| Çıkış |Diğer runbook'lar tarafından kullanılması amaçlanan nesneler. |İş geçmişine yazılır. |Test Çıkış Bölmesi'nde görüntülenir. |
| Uyarı |Kullanıcıya yönelik uyarı iletisi. |İş geçmişine yazılır. |Test Çıkış Bölmesi'nde görüntülenir. |
| Hata |Kullanıcıya yönelik hata iletisi. Bir özel durumun aksine, runbook varsayılan olarak bir hata iletisinden sonra devam eder. |İş geçmişine yazılır. |Test Çıkış Bölmesi'nde görüntülenir. |
| Ayrıntılı |Genel veya hata ayıklama bilgileri veren mesajlar. |Yalnızca runbook için ayrıntılı günlük kaydı açıksa iş geçmişine yazılır. |Yalnızca $VerbosePreference runbook 'ta devam etmek üzere ayarlandıysa, test çıkış bölmesinde görüntülenir. |
| İlerleme durumu |Runbook'taki her etkinlikten önce ve sonra otomatik oluşturulan kayıtlar. Runbook, etkileşimli bir kullanıcı için tasarlandıklarından kendi ilerleme kayıtlarını oluşturmayı denememelidir. |Yalnızca runbook için ilerleme durumu günlük kaydı açıksa iş geçmişine yazılır. |Test Çıkış Bölmesi'nde görüntülenmez. |
| Hata ayıklama |Etkileşimli bir kullanıcıya yönelik iletiler. Runbook 'larda kullanılmamalıdır. |İş geçmişine yazılmaz. |Test Çıkış Bölmesi'ne yazılmaz. |

## <a name="output-stream"></a>Çıkış akışı
Çıkış akışı, doğru çalıştığı zaman bir betik veya iş akışı tarafından oluşturulan nesnelerin çıkışı için tasarlanmıştır. Azure Otomasyonu 'nda bu akış öncelikle [Geçerli runbook 'u çağıran üst runbook 'lar](automation-child-runbooks.md)tarafından tüketilmesi amaçlanan nesneler için kullanılır. Üst runbook'tan [runbook'u satır içi olarak çağırdığınızda](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) , üst öğeye çıkış akışından verileri döndürür. Yalnızca runbook 'un başka bir runbook tarafından çağrılmayacağını biliyorsanız, çıkış akışını yalnızca genel bilgileri kullanıcıya geri bildirmek için kullanın. Ancak, en iyi uygulama olarak kullanıcıya genel bilgiler iletmek için [Verbose Stream](#verbose-stream) kullanmalısınız.

[Write-Output](https://technet.microsoft.com/library/hh849921.aspx) kullanarak veya nesneyi runbook'ta kendi satırına koyarak çıkış akışına veri yazabilirsiniz.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Bir işlevden çıkış

Runbook 'na dahil olan bir işlevde çıkış akışına yazdığınızda, çıkış runbook 'a geri geçirilir. Runbook bu çıkışı bir değişkene atarsa, çıkış akışına yazılmaz. İşlevin içinden diğer akışlara yazma, runbook için karşılık gelen akışa yazar.

Aşağıdaki örnek runbook 'u göz önünde bulundurun:

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Runbook işi için çıkış akışı:

```output
Output inside of function
Output outside of function
```

Runbook işi için ayrıntılı akış:

```output
Verbose outside of function
Verbose inside of function
```

Runbook 'u yayımladıktan ve başlamadan önce, ayrıntılı akış çıkışını almak için Runbook ayarlarında ayrıntılı günlüğü de açmalısınız.

### <a name="declaring-output-data-type"></a>Çıkış verisi türünü bildirme

Bir iş akışı [OutputType özniteliğini](https://technet.microsoft.com/library/hh847785.aspx)kullanarak çıkışının veri türünü belirtebilir. Bu özniteliğin çalışma zamanı sırasında bir etkisi yoktur, ancak tasarım zamanında runbook yazarına runbook'un beklenen çıkışı hakkında bir gösterge sağlar. Runbook 'lar için araç kümesi gelişmeye devam ettiğinden, çıkış veri türlerinin tasarım zamanında bildirilmesinin önemi önemli olarak artar. Sonuç olarak, bu bildirimi oluşturduğunuz tüm runbook 'lara eklemek en iyi uygulamadır.

Örnek çıkış türlerinin bir listesi aşağıda verilmiştir:

* System. String
* System.Int32
* System. Collections. Hashtable
* Microsoft. Azure. Commands. COMPUTE. modeller. PSVirtualMachine

Aşağıdaki örnek runbook bir dize nesnesi çıkışı yapar ve çıkış türü bildirimini içerir. Runbook'unuz belirli bir türde dizi çıkışı yapıyorsa, yine de o türdeki dizi yerine, türü belirtmelisiniz.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

Grafik veya grafik PowerShell Iş akışı runbook 'larında çıkış türü bildirmek için **giriş ve çıkış** menü seçeneğini seçebilir ve çıkış türünün adını yazabilirsiniz. Bir üst runbook 'tan başvuruda bulunarak kolayca tanımlanabilir olması için tam .NET sınıf adını kullanmanız önerilir. Bu, bu sınıfın tüm özelliklerini runbook 'taki Data Bus 'a sunar ve bunları koşullu mantık, günlüğe kaydetme ve Runbook 'taki diğer etkinlikler için değer olarak başvurmak üzere kullanırken çok fazla esneklik sağlar.<br> ![Runbook giriş ve çıkış seçeneği](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

Aşağıdaki örnekte, bu özelliği göstermek için iki grafik runbook 'unuz vardır. Modüler runbook tasarım modelini uygularsanız, farklı çalıştır hesabını kullanarak Azure ile kimlik doğrulamasını yöneten *kimlik doğrulama runbook şablonu* olarak hizmet veren bir runbook uygulamanız vardır. Genellikle belirli bir senaryoyu otomatikleştirmek için çekirdek mantığı gerçekleştirecek ikinci runbook, bu durumda *kimlik doğrulama runbook şablonunu* yürütmek ve sonuçları **Test** çıkış bölmenize görüntülemektir. Normal koşullarda, bu runbook 'un alt runbook 'tan gelen çıktıyı kullanan bir kaynağa karşı bir işlem yapması gerekir.

Aşağıda, **Authenticateto-Azure** runbook 'un temel mantığı verilmiştir.<br> ![Runbook şablonu örneği](media/automation-runbook-output-and-messages/runbook-authentication-template.png)kimliğini doğrulayın.

Bu, kimlik doğrulama profili özelliklerini döndüren *Microsoft. Azure. Commands. Profile. modeller. PSAzureContext*çıktı türünü içerir.<br> ![Runbook çıkış türü örneği](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Bu runbook basit olsa da, buradan çağırmak için bir yapılandırma öğesi vardır. Son etkinlik, **Write-output** cmdlet 'ini yürütüyor ve bu cmdlet için gerekli olan **InputObject** parametresi için bir PowerShell ifadesi kullanarak profil verilerini bir $ _ değişkenine yazar.

Bu örnekteki *Test-ChildOutputType*adlı ikinci runbook için, yalnızca iki etkinliğiniz vardır.<br> ![Örnek alt çıkış türü runbook 'U](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

İlk etkinlik **Authenticateto-Azure** runbook 'unu çağırır ve ikinci etkinlik, **etkinlik çıktısının** **veri kaynağıyla** **Write-Verbose** cmdlet 'ini ve **alan yolu** **için değer**  **Authenticateto-Azure** runbook 'un bağlam çıkışını belirten Context. Subscription. subscriptionName.<br> ![Write-Verbose cmdlet parametresi veri kaynağı](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Elde edilen çıktı, aboneliğin adıdır.<br> ![Test-ChildOutputType runbook sonuçları](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

> [!NOTE]
> **Giriş ve çıkış özellikleri** bölmesindeki **Çıkış türü** kutusuna bir değer girdikten sonra, girişiniz denetim tarafından tanınabilmesi için denetimin dışını tıklamalıdır.

## <a name="message-streams"></a>İleti akışları

Çıkış akışından farklı olarak, ileti akışlarının kullanıcıya bilgi iletmesi amaçlanır. Farklı bilgi türleri için birden çok ileti akışı vardır ve her biri Azure Otomasyonu tarafından farklı şekilde işlenir.

### <a name="warning-and-error-streams"></a>Uyarı ve hata akışları

Uyarı ve Hata akışlarının bir runbook'ta oluşan sorunları günlüğe kaydetmesi amaçlanır. Runbook yürütüldüğünde iş geçmişine yazılır ve bir runbook test edildiğinde Azure portal test çıkış bölmesine dahil edilir. Varsayılan olarak, runbook bir uyarı veya hatadan sonra yürütülmeye devam eder. İletiyi oluşturmadan önce runbook'ta bir [tercih değişkeni](#preference-variables) ayarlayarak bir uyarı veya hatada runbook'un askıya alınması gerektiğini belirtebilirsiniz. Örneğin, bir özel durum olduğu için bir runbook 'un hata üzerinde askıda kalmasına neden olmak için **$ErrorActionPreference** ' ı Durdur ' a ayarlayın.

[Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) veya [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) cmdlet'ini kullanarak bir uyarı veya hata iletisi oluşturun. Etkinlikler de bu akışlara yazabilir.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Verbose stream

Ayrıntılı ileti akışı, runbook işlemi hakkında genel bilgiler için kullanılır. [Hata ayıklama akışı](#debug-stream) bir runbook 'ta kullanılamadığından, hata ayıklama bilgileri için ayrıntılı iletiler kullanılmalıdır. Varsayılan olarak, yayımlanan runbook 'lardan ayrıntılı iletiler iş geçmişinde depolanmaz. Ayrıntılı iletileri depolamak için, yayımlanan runbook 'ları, Azure portal runbook 'un Yapılandır sekmesinde ayrıntılı kayıtları günlüğe kaydetmek üzere yapılandırın. Çoğu durumda, performans nedenleriyle runbook için ayrıntılı kayıtları günlüğe kaydetmeme varsayılan ayarını korumalısınız. Bu seçeneği yalnızca sorun gidermek veya bir runbook'ta hata ayıklamak için açın.

[Bir runbook 'u test](automation-testing-runbook.md)ederken, runbook ayrıntılı kayıtları günlüğe kaydetmek üzere yapılandırılmış bile olsa ayrıntılı iletiler görüntülenmez. [Bir runbook 'u test](automation-testing-runbook.md)ederken ayrıntılı iletileri göstermek için $VerbosePreference değişkenini devam edecek şekilde ayarlamanız gerekir. Bu değişken kümesiyle, ayrıntılı iletiler Azure portal test çıkış bölmesinde görüntülenir.

[Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) cmdlet'ini kullanarak bir ayrıntılı ileti oluşturun.

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Hata ayıklama akışı

Hata Ayıklama akışının etkileşimli kullanıcı tarafından kullanılması amaçlanır ve runbook'larda kullanılmamalıdır.

## <a name="progress-records"></a>İlerleme durumu kayıtlarını

Bir runbook 'u ilerleme kayıtlarını günlüğe kaydetmek üzere yapılandırırsanız (Azure portal runbook 'un Yapılandır sekmesinde), her etkinlik çalıştırılmadan önce ve sonra iş geçmişine bir kayıt yazılır. Çoğu durumda, performansı en üst düzeye çıkarmak için ilerleme durumu kayıtlarını günlüğe kaydetmeme varsayılan ayarını korumalısınız. Bu seçeneği yalnızca sorun gidermek veya bir runbook'ta hata ayıklamak için açın. Bir runbook'u test ederken, runbook ilerleme kaydı kayıtlarını günlüğe kaydetmek üzere yapılandırılmış bile olsa ilerleme durumu iletileri görüntülenmez.

Bu cmdlet etkileşimli bir kullanıcıyla kullanılmak üzere tasarlandığından, [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) cmdlet 'i runbook 'ta geçerli değildir.

## <a name="preference-variables"></a>Tercih değişkenleri

Windows PowerShell farklı çıkış akışlarına gönderilen verilerin nasıl yanıtlanacağını belirlemek için [tercih değişkenleri](https://technet.microsoft.com/library/hh847796.aspx) kullanır. Bir runbook 'ta farklı akışlara gönderilen verilere nasıl yanıt vereceğini denetlemek için bu değişkenleri ayarlayabilirsiniz.

Aşağıdaki tabloda runbook'larda kullanılabilecek tercih değişkenleriyle geçerli ve varsayılan değerleri listelenmektedir. Bu tablo yalnızca bir runbook 'ta geçerli olan değerleri içerir. Windows PowerShell 'de Azure Otomasyonu dışında kullanıldığında, tercih değişkenleri için ek değerler geçerlidir.

| Değişken | Varsayılan Değer | Geçerli Değerler |
|:--- |:--- |:--- |
| WarningPreference |Devam et |Durdur<br>Devam et<br>SilentlyContinue |
| ErrorActionPreference |Devam et |Durdur<br>Devam et<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Durdur<br>Devam et<br>SilentlyContinue |

Aşağıdaki tabloda runbook'larda geçerli olan tercih değişkeni değerleri için davranışlar listelenmektedir.

| Değer | Davranış |
|:--- |:--- |
| Devam et |İletiyi günlüğe kaydeder ve runbook'u yürütmeye devam eder. |
| SilentlyContinue |İletiyi günlüğe kaydetmeden runbook'u yürütmeye devam eder. Bu değer iletiyi yok saymakla aynı etkiye sahiptir. |
| Durdur |İletiyi günlüğe kaydeder ve runbook'u askıya alır. |

## <a name="runbook-output"></a>Runbook çıkışı ve iletileri alınıyor

### <a name="azure-portal"></a>Azure portal

Bir runbook işinin ayrıntılarını runbook 'un Işler sekmesinden Azure portal görüntüleyebilirsiniz. İşin özeti, giriş parametrelerini ve [Çıkış akışını](#output-stream) işle ilgili genel bilgilerin yanı sıra, gerçekleştiklerinde özel durumlar da görüntüler. Bu geçmiş, runbook ayrıntılı ve ilerleme kaydı kayıtlarını günlüğe kaydetmek üzere yapılandırıldıysa, [Çıkış akışından](#output-stream) ve [uyarı ve hata akışlarının](#warning-and-error-streams) yanı sıra [ayrıntılı akış](#verbose-stream) ve [ilerleme kaydı kayıtlarını](#progress-records) da içerir.

### <a name="windows-powershell"></a>Windows PowerShell

Windows PowerShell 'de, [Get-Azureautomationjoi put](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) cmdlet 'ini kullanarak bir runbook 'tan çıkışı ve iletileri alabilirsiniz. Bu cmdlet iş KIMLIĞINI gerektirir ve hangi akışın dönebileceği belirttiğiniz Stream adlı bir parametreye sahiptir. Bir işin tüm akışlarını döndürmek için **Tümü** değerini belirtebilirsiniz.

Aşağıdaki örnek, örnek bir runbook başlatır ve tamamlanmasını bekler. Tamamlandığında, çıkış akışı işten toplanır.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="graphical-authoring"></a>Grafik yazma

Grafik runbook 'lar için, ek günlüğe kaydetme, etkinlik düzeyinde izleme biçiminde kullanılabilir. İki izleme düzeyi vardır: Temel ve ayrıntılı. Temel izlemede, runbook 'taki her etkinliğin başlangıç ve bitiş zamanını ve etkinlik yeniden denemeleri ile ilgili bilgileri görebilirsiniz. Bazı örnekler, etkinliğin deneme sayısı ve başlangıç zamanı. Ayrıntılı izlemede, her etkinlik için temel izleme Plus giriş ve çıkış verilerini alırsınız. Şu anda izleme kayıtları ayrıntılı akış kullanılarak yazılır, bu nedenle izlemeyi etkinleştirdiğinizde ayrıntılı günlüğü etkinleştirmeniz gerekir. İzleme özelliği etkinken grafik runbook 'lar için ilerleme kayıtlarının günlüğe kaydını yapmanız gerekmez. Temel izleme aynı amaca hizmet eder ve daha bilgilendirici olur.

![Grafik yazma Iş akışları görünümü](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Grafik runbook 'lar için ayrıntılı günlük kaydını ve izlemeyi etkinleştirdiğinizde daha fazla bilgi edinmek için, üretim Iş akışları görünümünde daha fazla bilgi bulabilirsiniz. Bu ek bilgiler, runbook ile ilgili üretim sorunlarını gidermek için gereklidir ve bu nedenle, bunu yalnızca bu amaçla etkinleştirmeniz gerekir, bu da genel bir uygulama olarak değil. Izleme kayıtları özellikle çok fazla olabilir. Grafik runbook izleme ile, temel veya ayrıntılı izlemeyi yapılandırdığınıza bağlı olarak, etkinlik başına iki ile dört arasında kayıt alabilirsiniz. Sorun giderme için bir runbook 'un ilerlemesini izlemek üzere bu bilgilere ihtiyaç duymadığınız takdirde, Izlemenin kapatılmasını devam etmek isteyebilirsiniz.

**Etkinlik düzeyi izlemeyi etkinleştirmek için aşağıdaki adımları uygulayın:**

1. Azure portalında, Otomasyon hesabınızı açın.
2. **Işlem Otomasyonu**altında runbook 'ların listesini açmak Için **runbook 'lar** ' ı seçin.
3. Runbook 'lar sayfasında, runbook 'lardan bir grafik runbook 'u tıklatarak seçin.
4. **Ayarlar**altında **günlüğe kaydetme ve izleme**' ye tıklayın.
5. Günlüğe kaydetme ve Izleme sayfasında, ayrıntılı günlük kaydını etkinleştirmek için **Açık** ' a tıklayın ve etkinlik düzeyinde izleme altında, Izleme düzeyini **temel** veya **ayrıntılı** olarak, ihtiyacınız olan izleme düzeyine göre değiştirin.<br>

   ![Grafik yazma günlüğe kaydetme ve Izleme sayfası](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-monitor-logs"></a>Izleme günlüklerini Microsoft Azure

Otomasyon, Log Analytics çalışma alanınıza runbook iş durumu ve iş akışları gönderebilir. Azure Izleyici günlükleri ile şunları yapabilirsiniz

* Otomasyon işleriniz hakkında Öngörüler alın
* Runbook iş durumunuzu temel alarak bir e-posta veya uyarı tetikleyin (örneğin, başarısız veya askıya alındı)
* İş akışlarınız genelinde gelişmiş sorgular yazma
* Otomasyon hesaplarında işlerin ilişkilendirilmesi
* Zaman içinde iş geçmişinizi görselleştirin

İş verilerini toplamak, ilişkilendirmek ve üzerinde işlem yapmak üzere Azure Izleyici günlükleri ile tümleştirmeyi yapılandırma hakkında daha fazla bilgi için bkz. [Otomasyon 'Dan Azure izleyici günlüklerine iş durumu ve iş akışlarını iletme](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Sonraki adımlar

* Runbook yürütme, runbook işlerini izleme ve diğer teknik ayrıntılar hakkında daha fazla bilgi edinmek için bkz. [Runbook işi izleme](automation-runbook-execution.md)
* Alt runbook 'ları nasıl tasarlayıp kullanacağınızı anlamak için bkz. [Azure Automation 'Da alt runbook 'lar](automation-child-runbooks.md)
* Dil başvurusu ve öğrenme modülleri de dahil olmak üzere PowerShell hakkında daha fazla bilgi için [PowerShell belgelerine](/powershell/scripting/overview)bakın.
