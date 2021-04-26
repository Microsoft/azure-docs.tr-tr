---
title: Azure Otomasyonu 'nda grafik runbook 'ları yazma
description: Bu makalede, kod ile çalışmasız bir grafik runbook 'unun nasıl yazılacağı anlatılır.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 88492d914b710c7a738dd6d7f501e22d490065b6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833823"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>Azure Otomasyonu 'nda grafik runbook 'ları yazma

Azure Otomasyonu 'ndaki tüm runbook 'lar Windows PowerShell iş akışlarıdır. Grafik runbook 'ları ve grafik PowerShell Iş akışı runbook 'ları, Otomasyon çalışanlarının çalıştırdığı ancak görüntüleyemeyeceğiniz veya değiştiremeyeceğiniz PowerShell kodu oluşturur. Grafik runbook 'unu grafik PowerShell Iş akışı runbook 'una dönüştürebilir ve bunun tersini yapabilirsiniz. Ancak, bu runbook 'ları bir metinsel runbook 'a dönüştüremezsiniz. Ayrıca, Otomasyon grafik Düzenleyicisi bir metinsel runbook 'u içeri aktaramazsınız.

Grafik yazma, temel Windows PowerShell veya PowerShell Iş akışı kodunun karmaşıklıkları olmadan Azure Otomasyonu için Runbook 'lar oluşturmanızı sağlar. Bir cmdlet ve Runbook kitaplığından tuvale etkinlik ekleyebilir, bunları birbirine bağlayabilir ve bir iş akışı oluşturacak şekilde yapılandırabilirsiniz. System Center Orchestrator veya Service Management Automation (SMA) ile çalıştıysanız grafik yazma hakkında bilgi sahibi olmanız gerekir. Bu makalede bir grafik runbook 'u oluşturmaya başlamak için ihtiyacınız olan kavramlara giriş sunulmaktadır.

## <a name="overview-of-graphical-editor"></a>Grafik düzenleyiciye genel bakış

Grafik bir runbook oluşturarak veya düzenleyerek grafik düzenleyiciyi Azure portal açabilirsiniz.

![Grafik çalışma alanı](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

Aşağıdaki bölümlerde, grafik düzenleyicideki denetimler açıklanır.

### <a name="canvas-control"></a>Tuval denetimi

Tuval denetimi, runbook 'unuzu tasarlamanızı sağlar. Kitaplık denetimindeki düğümlerden etkinlikleri runbook 'a ekleyebilir ve bunları runbook mantığını tanımlamak için bağlantılarla birlikte bağlayabilirsiniz. Tuvalin en altında, yakınlaştırmanızı ve küçültme sağlayan denetimler vardır.

### <a name="library-control"></a>Kitaplık denetimi

Kitaplık denetimi, runbook uygulamanıza eklenecek [etkinlikleri](#use-activities) seçmenizi sağlar. Bunları başka etkinliklere bağlayabileceğiniz tuvale eklersiniz. Kitaplık denetimi, aşağıdaki tabloda tanımlanan bölümleri içerir.

| Section | Description |
|:--- |:--- |
| Cmdlet’ler |Runbook 'da kullanılabilen tüm cmdlet 'ler. Cmdlet 'ler modüle göre düzenlenir. Otomasyon hesabınızda yüklediğiniz tüm modüller kullanılabilir. |
| Runbook'lar |Otomasyon hesabınızdaki runbook 'lar. Bu runbook 'ları alt runbook 'lar olarak kullanılacak tuvale ekleyebilirsiniz. Yalnızca düzenlenmekte olan runbook ile aynı çekirdek türündeki runbook 'lar gösterilir. Grafik runbook 'lar için yalnızca PowerShell tabanlı runbook 'lar gösterilir. Grafik PowerShell Iş akışı runbook 'ları için yalnızca PowerShell Iş akışı tabanlı runbook 'lar gösterilir. |
| Varlıklar |Otomasyon hesabınızdaki, runbook 'larınızda kullanabileceğiniz [Otomasyon varlıkları](/previous-versions/azure/dn939988(v=azure.100)) . Bir runbook 'a varlık eklemek, seçili varlığı alan bir iş akışı etkinliği ekler. Değişken varlıklar söz konusu olduğunda, değişkeni almak için bir etkinlik eklenip eklenmeyeceğini veya değişkeni ayarlamayı seçebilirsiniz. |
| Runbook Denetimi |Geçerli runbook 'larınız için kullanılabilen etkinlikleri denetleyin. Birleşim etkinliği birden çok giriş alır ve iş akışına devam etmeden önce tüm tamamlanana kadar bekler. Bir kod etkinliği, grafik runbook türüne bağlı olarak bir veya daha fazla PowerShell veya PowerShell Iş akışı kodu çalıştırır. Bu etkinliği, özel kod veya diğer etkinliklerle elde edilmesi zor olan işlevler için kullanabilirsiniz. |

### <a name="configuration-control"></a>Yapılandırma denetimi

Yapılandırma denetimi, tuvalde seçilen bir nesne için ayrıntı sağlamanıza olanak sağlar. Bu denetimde kullanılabilen özellikler, seçilen nesne türüne bağlıdır. Yapılandırma denetiminde bir seçenek belirlediğinizde daha fazla bilgi sağlamak için ek dikey pencereler açılır.

### <a name="test-control"></a>Test denetimi

Grafik Düzenleyicisi ilk başlatıldığında test denetimi görüntülenmez. Grafik runbook 'unu etkileşimli olarak test ettiğinizde açılır.

## <a name="use-activities"></a>Kullanım etkinlikleri

Etkinlikler bir runbook 'un yapı taşlarıdır. Bir etkinlik bir PowerShell cmdlet 'i, alt runbook veya bir iş akışı olabilir. Kitaplık denetiminde sağ tıklayıp **tuvale Ekle**' yi seçerek runbook 'a bir etkinlik ekleyebilirsiniz. Daha sonra istediğiniz tuvalde dilediğiniz yere yerleştirmek için aktiviteyi tıklatıp sürükleyebilirsiniz. Tuvaldeki etkinliğin konumu runbook 'un çalışmasını etkilemez. Runbook 'unuzu, işlemini görselleştirmek için en uygun bulduğunuz şekilde yerleştirebilirsiniz.

![Tuvale Ekle](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Yapılandırma dikey penceresinde özelliklerini ve parametrelerini yapılandırmak için tuvalde bir etkinlik seçin. Etkinliğin etiketini açıklayıcı bulduğunuz bir adla değiştirebilirsiniz. Runbook hala özgün cmdlet 'i çalıştırıyor. Yalnızca grafik düzenleyicinin kullandığı görünen adı değiştiriyorsunuz. Etiketin runbook içinde benzersiz olması gerektiğini unutmayın.

### <a name="parameter-sets"></a>Parametre kümeleri

Bir parametre kümesi, belirli bir cmdlet için değerleri kabul eden zorunlu ve isteğe bağlı parametreleri tanımlar. Tüm cmdlet 'ler en az bir parametre kümesine sahiptir ve bazılarında birkaç küme vardır. Bir cmdlet birden çok parametre kümesine sahipse, parametreleri yapılandırmadan önce kullanılacak birini seçmeniz gerekir. Bir etkinlik tarafından kullanılan parametre kümesini **parametre kümesi** seçip başka bir küme seçerek değiştirebilirsiniz. Bu durumda, önceden yapılandırdığınız herhangi bir parametre değeri kaybedilir.

Aşağıdaki örnekte, [Get-AzVM](/powershell/module/az.compute/get-azvm) cmdlet 'inin üç parametre kümesi vardır. Örnek, bir kaynak grubundaki tüm sanal makineleri döndürmek için **Listvirtualmachineınresourcegroupparamset** adlı tek bir isteğe bağlı parametre içeren bir kümesi kullanır. Örnek, döndürülecek sanal makineyi belirtmek için **Getvirtualmachineınresourcegroupparamset** parametre kümesini de kullanır. Bu küme, iki zorunlu parametreye ve bir isteğe bağlı parametreye sahiptir.

![Parametre kümesi](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Parametre değerleri

Bir parametre için bir değer belirttiğinizde, değerin nasıl belirtilceğini belirlemek için bir veri kaynağı seçersiniz. Belirli bir parametre için kullanılabilen veri kaynakları, bu parametre için geçerli değerlere bağlıdır. Örneğin, null değerlere izin verilmeyen bir parametre için null kullanılabilir bir seçenek değildir.

| Veri Kaynağı | Description |
|:--- |:--- |
| Sabit değer |Parametre için bir değer yazın. Bu veri kaynağı yalnızca şu veri türleri için kullanılabilir: Int32, Int64, String, Boolean, DateTime, anahtar. |
| Etkinlik çıkışı |İş akışındaki geçerli etkinlikten önce gelen bir etkinliğin çıkışını kullanın. Tüm geçerli etkinlikler listelenir. Parametre değeri için yalnızca çıktıyı üreten etkinliği kullanın. Etkinlik birden fazla özelliğe sahip bir nesne çıktıbulunursa, etkinlik seçildikten sonra belirli bir özelliğin adını yazabilirsiniz. |
| Runbook girişi |Etkinlik parametresi için giriş olarak bir runbook girişi seçin. |
| Değişken varlık |Giriş olarak bir Otomasyon değişkeni seçin. |
| Kimlik bilgisi varlığı |Giriş olarak bir Otomasyon kimlik bilgisi seçin. |
| Sertifika varlığı |Giriş olarak bir Otomasyon sertifikası seçin. |
| Bağlantı varlığı |Giriş olarak bir Otomasyon bağlantısı seçin. |
| PowerShell ifadesi |Basit bir [PowerShell ifadesi](#work-with-powershell-expressions)belirtin. İfade etkinlikten önce değerlendirilir ve sonuç parametre değeri için kullanılır. Bir etkinliğin veya Runbook giriş parametresinin çıktısına başvurmak için değişkenleri kullanabilirsiniz. |
| Yapılandırılmadı |Daha önce yapılandırılmış olan tüm değerleri temizleyin. |

#### <a name="optional-additional-parameters"></a>İsteğe bağlı ek parametreler

Tüm cmdlet 'ler ek parametreler sağlama seçeneğine sahiptir. Bunlar PowerShell ortak parametreleridir veya diğer özel parametrelerdir. Grafik Düzenleyicisi, PowerShell söz dizimini kullanarak parametreler sağlayabileceğiniz bir metin kutusu görüntüler. Örneğin, ortak parametresini kullanmak için `Verbose` , belirtmeniz gerekir `-Verbose:$True` .

### <a name="retry-activity"></a>Yeniden deneme etkinliği

Bir etkinlik için yeniden deneme işlevselliği, belirli bir koşul karşılanana kadar birden çok kez çalışmasına izin verir. Bu özelliği birden çok kez çalışması gereken etkinlikler için kullanabilirsiniz, hataya açıktır, başarılı olması için birden fazla girişim gerektirebilir veya geçerli veriler için etkinliğin çıktı bilgilerini test edebilirsiniz.

Bir etkinlik için yeniden denemeyi etkinleştirdiğinizde bir gecikme ve koşul belirleyebilirsiniz. Gecikme süresi, runbook 'un etkinliği yeniden çalıştırmadan önce bekleyeceği süredir (saniye veya dakika cinsinden ölçülür). Bir gecikme belirtmezseniz, etkinlik tamamlandıktan hemen sonra tekrar çalışır.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="Yeniden deneme özelliği etkinleştirme ayarlarının ekran görüntüsü.":::

Yeniden deneme koşulu, etkinlik her çalıştıktan sonra değerlendirilen bir PowerShell deyimidir. İfade true olarak çözümlenirse etkinlik yeniden çalışır. İfade false olarak çözümlenirse etkinlik yeniden çalıştırılmaz ve Runbook bir sonraki etkinliğe gider.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="Bu koşul doğru alana ve yeniden deneme koşulunda kullanılabilecek PowerShell ifadelerine örneklere kadar yeniden denemeyi gösteren ekran görüntüsü.":::

Yeniden deneme koşulu, `RetryData` etkinlik yeniden denemeleri hakkında bilgilere erişim sağlayan adlı bir değişken kullanabilir. Bu değişken aşağıdaki tablodaki özelliklere sahiptir:

| Özellik | Açıklama |
|:--- |:--- |
| `NumberOfAttempts` |Etkinliğin kaç kez çalıştırıldığı. |
| `Output` |Etkinliğin son çalıştırağından çıktı. |
| `TotalDuration` |Etkinlik ilk kez başlatıldığından bu yana zaman geçti. |
| `StartedAt` |Etkinliğin ilk başlatıldığı zaman (UTC biçiminde). |

Etkinlik yeniden deneme koşullarının örnekleri aşağıda verilmiştir.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

Bir etkinlik için yeniden deneme koşulu yapılandırdıktan sonra, etkinlik size anımsatacak iki görsel ipucu içerir. Etkinlik, etkinliğin yapılandırmasını gözden geçirdikten sonra etkinlik içinde gösterilir.

![Etkinlik yeniden deneme görsel göstergeleri](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>İş akışı betik denetimi

Bir iş akışı betik denetimi, yazılan grafik runbook 'un türüne bağlı olarak PowerShell veya PowerShell Iş akışı betiğini kabul eden özel bir etkinliktir. Bu denetim, diğer yollarla kullanılamayacak işlevselliği sağlar. Parametreleri kabul edemez, ancak etkinlik çıktısı ve Runbook giriş parametreleri için değişkenleri kullanabilir. Etkinliğin tüm çıktıları DataBus 'e eklenir. Bir özel durum giden bağlantısı olmayan çıktıdır ve bu durumda çıkış runbook 'un çıktısına eklenir.

Örneğin, aşağıdaki kod adlı bir runbook giriş değişkenini kullanarak tarih hesaplamaları gerçekleştirir `NumberOfDays` . Daha sonra, runbook 'taki izleyen etkinlikler tarafından kullanılacak çıktı olarak hesaplanmış bir tarih saat değeri gönderir.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>İş akışı için bağlantıları kullanma

Grafik runbook 'taki bir bağlantı iki etkinliği birbirine bağlar. Kaynak etkinlikten hedef etkinliğe işaret eden bir ok olarak tuvalde görüntülenir. Etkinlikler, kaynak etkinlik tamamlandıktan sonra başlangıç hedef etkinliği ile ok yönünde çalışır.

### <a name="create-a-link"></a>Bağlantı oluştur

Kaynak etkinliğini seçip şeklin altındaki daireye tıklayarak iki etkinlik arasında bir bağlantı oluşturabilirsiniz. Oku hedef etkinlik ve yayına sürükleyin.

![Bağlantı oluştur](media/automation-graphical-authoring-intro/create-link-options.png)

Yapılandırma dikey penceresinde özelliklerini yapılandırmak için bağlantıyı seçin. Özellikler, aşağıdaki tabloda açıklanan bağlantı türünü içerir.

| Bağlantı türü | Description |
|:--- |:--- |
| İşlem Hattı |Hedef etkinlik, kaynak etkinlikten her nesne çıktısı için bir kez çalışır. Kaynak etkinlik hiçbir çıkış içermiyorsa, hedef etkinlik çalışmaz. Kaynak etkinlikten alınan çıkış bir nesne olarak kullanılabilir. |
| Sequence |Hedef etkinlik, kaynak etkinlikten çıkış aldığında yalnızca bir kez çalışır. Kaynak etkinlikten alınan çıkış, nesne dizisi olarak kullanılabilir. |

### <a name="start-runbook-activity"></a>Runbook 'u Başlat etkinliği

Grafik runbook, gelen bağlantısı olmayan etkinliklerle başlar. Genellikle runbook için başlangıç etkinliği görevi gören yalnızca bir etkinlik vardır. Birden çok etkinliğin gelen bir bağlantısı yoksa, runbook bunları paralel olarak çalıştırarak başlatılır. Her tamamlandıkça diğer etkinlikleri çalıştırmak için bağlantıları izler.

### <a name="specify-link-conditions"></a>Bağlantı koşullarını belirtme

Bir bağlantı üzerinde bir koşul belirttiğinizde, hedef etkinlik yalnızca koşul doğru olarak çözümlenirse çalışır. Genellikle `ActivityOutput` kaynak etkinlikten çıktıyı almak için bir koşuldaki bir değişkeni kullanırsınız.

İşlem hattı bağlantısı için tek bir nesne için koşul belirtmeniz gerekir. Runbook, kaynak etkinliğin her bir nesne çıkışı için koşulu değerlendirir. Ardından, koşulu karşılayan her bir nesne için hedef etkinliğini çalıştırır. Örneğin, kaynak etkinliğiyle `Get-AzVM` , yalnızca grup1 adlı kaynak grubundaki sanal makineleri almak için koşullu bir işlem hattı bağlantısı için aşağıdaki sözdizimini kullanabilirsiniz.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Bir sıra bağlantısı için Runbook, kaynak etkinlikten tüm nesneleri içeren tek bir dizi döndürüldüğünden koşulu yalnızca bir kez değerlendirir. Bu nedenle, runbook bir işlem hattı bağlantısıyla aynı şekilde filtrelemeye yönelik bir sıra bağlantısı kullanamaz. Sıra bağlantısı, bir sonraki etkinliğin çalıştırılıp çalıştırılmadığını belirleyebilir.

Örneğin, **Başlangıç VM** runbook 'ımızda aşağıdaki etkinlik kümesini alın:

![Dizileri olan koşullu bağlantı](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Runbook, giriş parametrelerinin değerlerini doğrulayan `VMName` ve `ResourceGroupName` yapılacak uygun eylemi belirleyecek üç farklı dizi bağlantısı kullanır. Olası eylemler tek bir VM başlatır, kaynak grubundaki tüm VM 'Leri başlatır veya bir abonelikteki tüm VM 'Leri başlatır. Ve arasındaki sıra bağlantısı için `Connect to Azure` `Get single VM` koşul mantığı şöyledir:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

Koşullu bir bağlantı kullandığınızda, kaynak etkinlikten söz konusu daldaki diğer etkinliklere sunulan veriler koşula göre filtrelenir. Bir etkinlik, kaynak birden çok bağlantıya ise, her dalda etkinliklere sunulan veriler, bu dala bağlanan bağlantıdaki koşula bağlıdır.

Örneğin, `Start-AzVM` aşağıdaki runbook 'taki etkinlik tüm sanal makineleri başlatır. İki koşullu bağlantıya sahiptir. İlk koşullu bağlantı, `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` etkinlik başarıyla tamamlanırsa filtre uygulamak için ifadeyi kullanır `Start-AzVM` . İkinci koşullu bağlantı, `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` `Start-AzVm` etkinliğin sanal makineyi başlatamazsa filtrelemek için ifadeyi kullanır.

![Koşullu bağlantı örneği](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

İlk bağlantıyı izleyen ve etkinlik çıkışını kullanan herhangi bir etkinlik `Get-AzureVM` yalnızca çalıştırıldığı sırada başlatılan sanal makineleri alır `Get-AzureVM` . İkinci bağlantıyı izleyen herhangi bir etkinlik yalnızca çalıştırıldığında durdurulmuş olan sanal makineleri alır `Get-AzureVM` . Üçüncü bağlantıyı izleyen herhangi bir etkinlik, çalışma durumlarına bakılmaksızın tüm sanal makineleri alır.

### <a name="use-junctions"></a>Merkezleriyle kullanma

Birleşim, tüm gelen dallar tamamlanana kadar bekleyen özel bir etkinliktir. Bu, runbook 'un birden çok etkinliği paralel olarak çalıştırmasına ve taşınmadan önce tümünün tamamlandığından emin olmanıza olanak tanır.

Bir birleşimin sınırsız sayıda gelen bağlantısı olabilir, ancak bu bağlantılardan yalnızca biri işlem hattı olabilir. Gelen dizi bağlantılarının sayısı sınırlı değil. Birleşimi birden çok gelen işlem hattı bağlantısı ile oluşturabilir ve Runbook 'u kaydedebilirsiniz, ancak çalıştırıldığında başarısız olur.

Aşağıdaki örnek, bir sanal makine kümesi başlatan bir runbook 'un parçası olan bir runbook 'un aynı anda bu makinelere uygulanacak düzeltme eklerini de indiriyor. Runbook devam etmeden önce her iki işlemin de tamamlandığından emin olmak için bir birleşim kullanır.

![Birleşim](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>Döngülerle çalışma

Bir hedef etkinlik, kaynak etkinliğine veya sonunda kaynağına geri bağlanan başka bir etkinliğe bağlantı geldiğinde bir döngüyle biçimlendirilir. Grafik yazma Şu anda döngüleri desteklemiyor. Runbook 'üzde bir döngüyle karşılaşırsanız, doğru şekilde kaydedilir ancak çalıştırıldığında bir hata alır.

![Aralıklı](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>Etkinlikler arasında veri paylaşma

Giden bağlantı ile bir etkinliğin çıkış aldığı tüm veriler, runbook için verir 'e yazılır. Runbook 'taki tüm etkinlikler parametre değerlerini doldurmak veya betik koduna eklemek için verir üzerindeki verileri kullanabilir. Etkinlik, iş akışındaki önceki etkinliklerin çıktısına erişebilir.

Verilerin verir 'a nasıl yazıldığı, etkinliğin bağlantı türüne bağlıdır. İşlem hattı bağlantısı için veri, birden fazla nesne olarak çıktı olur. Sıralı bir bağlantı için, veriler dizi olarak çıktı olur. Yalnızca bir değer varsa, tek öğeli dizi olarak çıktı olur.

Runbook 'larınızın DataBus üzerindeki verilere erişmesi için iki yolu vardır: 
* Etkinlik çıkış veri kaynağı kullanın.
* PowerShell ifadesi veri kaynağı kullanın.

İlk mekanizma, başka bir etkinliğin parametresini doldurmak için bir etkinlik çıkış veri kaynağı kullanır. Çıktı bir nesnedir, runbook tek bir özellik belirtebilir.

![Etkinlik çıkışı](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

İkinci veri erişim mekanizması, `ActivityOutput` aşağıda gösterilen söz dizimini kullanarak bir etkinliğin çıkışını bir PowerShell ifadesi veri kaynağında veya bir iş akışı betiği etkinliğinde bir değişkenle alır. Çıktı bir nesnedir, runbook 'niz tek bir özellik belirtebilir.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>Kontrol noktaları kullan

Herhangi bir etkinlikte **denetim noktası runbook** 'u seçerek bir grafik PowerShell iş akışı runbook 'unda [denetim noktaları](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) belirleyebilirsiniz. Bu, etkinlik çalıştıktan sonra bir denetim noktasının ayarlanmış olmasına neden olur.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Kontrol noktaları yalnızca grafik PowerShell Iş akışı runbook 'larında etkinleştirilir ve grafik runbook 'larda kullanılamaz. Runbook, Azure cmdlet 'lerini kullanıyorsa, etkinlik ile tüm denetim noktası etkinliklerini izlemelidir `Connect-AzAccount` . Bağlantı işlemi, runbook 'un askıya alınması ve farklı bir çalışan üzerinde bu denetim noktasından yeniden başlatılması gereken durumlarda kullanılır.

## <a name="handle-runbook-input"></a>Runbook girişini işle

Bir runbook, geçerli diğeri alt olarak kullanılıyorsa, runbook 'u Azure portal aracılığıyla veya başka bir runbook 'tan Başlatan kullanıcıdan giriş yapılmasını gerektirir. Örneğin, bir sanal makine oluşturan bir runbook için, kullanıcının runbook başladığında sanal makine adı ve diğer özellikler gibi bilgileri sağlaması gerekebilir.

Runbook bir veya daha fazla giriş parametresi tanımlayarak girişi kabul eder. Kullanıcı, runbook her başlatıldığında bu parametrelerin değerlerini sağlar. Kullanıcı Azure portal kullanarak runbook 'u başlattığında, kullanıcıdan runbook tarafından desteklenen her giriş parametresi için değer sağlaması istenir.

Runbook 'unuzu yazarken, runbook araç çubuğunda **giriş ve çıkış** ' a tıklayarak giriş parametrelerine erişebilirsiniz. Bu, var olan bir giriş parametresini düzenleyebileceğiniz veya **giriş Ekle**' ye tıklayarak yeni bir tane oluşturabileceğiniz giriş ve çıkış denetimini açar.

![Girdi ekleme](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Her giriş parametresi aşağıdaki tablodaki Özellikler tarafından tanımlanır:

| Özellik | Açıklama |
|:--- |:--- |
| Ad | Gereklidir. Parametrenin adı. Ad, runbook içinde benzersiz olmalıdır. Bir harfle başlamalı ve yalnızca harf, rakam ve alt çizgi içermelidir. Ad boşluk içeremez. |
| Description |İsteğe bağlı. Giriş parametresinin amacının açıklaması. |
| Tür | İsteğe bağlı. Parametre değeri için beklenen veri türü. Azure portal, giriş isterken her bir parametre için veri türü için uygun bir denetim sağlar. Desteklenen parametre türleri String, Int32, Int64, Decimal, Boolean, DateTime ve Object. Bir veri türü seçilmezse, varsayılan olarak dize olur.|
| Zorunlu | İsteğe bağlı. Parametresi için bir değer sağlanması gerektiğini belirten ayar. `yes`' I seçerseniz, runbook başlatıldığında bir değer sağlanmalıdır. Seçeneğini belirlerseniz `no` , runbook başlatıldığında bir değer gerekli değildir ve varsayılan bir değer kullanılabilir. Varsayılan bir değeri tanımlanmış olmayan her zorunlu parametre için bir değer sağlamazsanız runbook başlatılamaz. |
| Varsayılan değer | İsteğe bağlı. Bir parametre için, runbook başlatıldığında bir parametre geçirilmemişse kullanılan değer. Varsayılan bir değer ayarlamak için öğesini seçin `Custom` . `None`Herhangi bir varsayılan değer sağlamak istemiyorsanız seçin. |

## <a name="handle-runbook-output"></a>Runbook çıkışını işle

Grafik yazma, [runbook 'un çıkışına](./automation-runbook-output-and-messages.md)giden bağlantısı olmayan herhangi bir etkinlik tarafından oluşturulan verileri kaydeder. Çıktı runbook işiyle birlikte kaydedilir ve Runbook bir alt öğe olarak kullanıldığında üst runbook 'ta kullanılabilir.

## <a name="work-with-powershell-expressions"></a>PowerShell ifadelerle çalışma

Grafik yazmanın avantajlarından biri, en az PowerShell bilgisine sahip bir runbook oluşturmanıza olanak sağlar. Şu anda, bazı [parametre değerlerini](#use-activities) doldurmak ve [bağlantı koşullarını](#use-links-for-workflow)ayarlamak için bir PowerShell 'i bilmeniz gerekir. Bu bölüm, PowerShell ifadelerine hızlı bir giriş sağlar. PowerShell 'in tüm ayrıntıları, [Windows PowerShell Ile komut dosyası oluşturma](/powershell/scripting/overview)sırasında kullanılabilir.

### <a name="use-a-powershell-expression-as-a-data-source"></a>Veri kaynağı olarak bir PowerShell ifadesi kullanma

Bir [etkinlik parametresinin](#use-activities) değerini PowerShell kodunun sonuçlarıyla doldurmak için bir veri kaynağı olarak bir PowerShell ifadesi kullanabilirsiniz. İfade, basit bir işlevi veya bazı karmaşık mantığı gerçekleştiren birden çok satırı gerçekleştiren tek satırlık bir kod olabilir. Bir değişkene atanmamış bir komuttan gelen tüm çıktılar parametre değerine çıkış olur.

Örneğin, aşağıdaki komut geçerli tarihi verir.

```powershell-interactive
Get-Date
```

Sonraki kod parçacığı, geçerli tarihten bir dize oluşturur ve onu bir değişkene atar. Kod, değişkenin içeriğini çıkışa gönderir.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Aşağıdaki komutlar geçerli tarihi değerlendirir ve geçerli günün hafta sonu veya hafta içi olduğunu gösteren bir dize döndürür.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>Etkinlik çıkışını kullan

Runbook 'inizdeki önceki bir etkinliğin çıktısını kullanmak için, `ActivityOutput` değişkeni aşağıdaki söz dizimiyle kullanın.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Örneğin, bir sanal makine adı gerektiren bir özelliği olan bir etkinliğiniz olabilir. Bu durumda, runbook 'u aşağıdaki ifadeyi kullanabilir.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Özelliği yalnızca bir ad yerine sanal makine nesnesi gerektiriyorsa, runbook aşağıdaki söz dizimini kullanarak tüm nesneyi döndürür.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Runbook, etkinliğin çıkışını aşağıdaki gibi daha karmaşık bir ifadede kullanabilir. Bu ifade metni sanal makine adına birleştirir.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>Değerleri Karşılaştır

Değerleri karşılaştırmak veya bir değerin belirtilen bir Düzenle eşleşip eşleşmediğini anlamak için [karşılaştırma işleçlerini](/powershell/module/microsoft.powershell.core/about/about_comparison_operators) kullanın. Karşılaştırma, true veya false değerini döndürür.

Örneğin, aşağıdaki koşul adlı bir etkinlikten sanal makinenin `Get-AzureVM` Şu anda durdurulmuş olup olmadığını belirler.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState -eq "Stopped"
```

Aşağıdaki koşul, aynı sanal makinenin durdurulmuş dışında bir durumda olup olmadığını belirler.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState -ne "Stopped"
```

Veya gibi [mantıksal bir işleç](/powershell/module/microsoft.powershell.core/about/about_logical_operators)kullanarak runbook 'inizdeki birden çok koşulu birleştirebilirsiniz `-and` `-or` . Örneğin, aşağıdaki koşul, önceki örnekteki sanal makinenin durdurulmuş veya durduruluyor durumunda olup olmadığını denetler.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState -eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState -eq "Stopping")
```

### <a name="use-hashtables"></a>Diyez tabloları kullanma

[Hashtables](/powershell/module/microsoft.powershell.core/about/about_hash_tables) , bir değer kümesi döndürmek için yararlı olan ad-değer çiftleridir. Ayrıca, sözlük olarak adlandırılan bir karma tablosu da görebilirsiniz. Belirli etkinliklerin özellikleri basit bir değer yerine bir karma değer bekler.

Aşağıdaki sözdizimini kullanarak bir Hashtable oluşturun. Herhangi bir sayıda girdi içerebilir, ancak her biri bir ad ve değer tarafından tanımlanır.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Örneğin, aşağıdaki ifade bir internet araması için bir değer Hashtable bekleyen bir etkinlik parametresi için veri kaynağı olarak kullanılmak üzere bir karma tablosu oluşturur.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

Aşağıdaki örnek, bir Hashtable 'ı doldurmak için çağrılan bir etkinliğin çıktısını kullanır `Get Twitter Connection` .

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>Azure kaynaklarında kimlik doğrulama

Azure Otomasyonu 'ndaki runbook 'lar Azure kaynaklarını yöneten Azure 'da kimlik doğrulaması gerektirir. Hizmet sorumlusu olarak da adlandırılan [Farklı Çalıştır hesabı](./automation-security-overview.md), bir Otomasyon Runbook 'unun aboneliğinizdeki Azure Resource Manager kaynaklarına erişmek için kullandığı varsayılan mekanizmadır. Bu işlevselliği, `AzureRunAsConnection` PowerShell [Get-AutomationConnection](/system-center/sma/manage-global-assets) cmdlet 'ini kullanan bağlantı varlığını tuvale ekleyerek bir grafik runbook 'a ekleyebilirsiniz. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'ini de ekleyebilirsiniz. Bu senaryo aşağıdaki örnekte gösterilmiştir.

![Farklı çalıştır kimlik doğrulama etkinlikleri](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

`Get Run As Connection`Etkinliği, veya `Get-AutomationConnection` adlı sabit değerli bir veri kaynağıyla yapılandırılır `AzureRunAsConnection` .

![Farklı Çalıştır bağlantı yapılandırması](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Sonraki etkinlik, `Connect-AzAccount` runbook 'ta kullanılmak üzere kimliği doğrulanmış farklı çalıştır hesabını ekler.

![Connect-AzAccount parametre kümesi](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>PowerShell runbook 'ları için `Add-AzAccount` ve `Add-AzureRMAccount` diğer adlardır `Connect-AzAccount` . Bu diğer adların grafik runbook 'larınız için kullanılamayacağını unutmayın. Grafik runbook 'u yalnızca kendisi kullanabilir `Connect-AzAccount` .

**ApplicationId**, **CERTIFICATETHUMBPRINT** ve **tenantıd** parametre alanları için, etkinlik birden fazla özelliğe sahip bir nesne çıkardığından, alan yolu için özelliğin adını belirtin. Aksi takdirde, runbook çalıştırıldığında kimlik doğrulamaya çalışırken başarısız olur. Bu, runbook 'unuzu farklı çalıştır hesabıyla doğrulamak için en azından ihtiyacınız olan şeydir.

Bazı aboneler, Azure klasik dağıtımını veya Azure Resource Manager kaynaklarını yönetmek için bir [Azure AD Kullanıcı hesabı](./shared-resources/credentials.md) kullanarak bir Otomasyon hesabı oluşturur. Bu aboneler için geriye dönük uyumluluk sağlamak için, runbook 'inizde kullanılacak kimlik doğrulama mekanizması, `Add-AzureAccount` bir [kimlik bilgisi](./shared-resources/credentials.md)varlığına sahip olan cmdlet 'dir. Varlık, Azure hesabına erişimi olan bir Active Directory kullanıcısını temsil eder.

Tuvale bir kimlik bilgisi varlığı ekleyerek ve ardından `Add-AzureAccount` girişinin kimlik bilgisi varlığını kullanan bir etkinliğin ardından grafik runbook 'ınız için bu işlevselliği etkinleştirebilirsiniz. Aşağıdaki örneğe bakın.

![Kimlik doğrulama etkinlikleri](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook 'un başlangıcında ve her denetim noktasından sonra kimlik doğrulaması gerekir. Bu nedenle, herhangi bir `Add-AzureAccount` etkinlikten sonra bir etkinlik kullanmanız gerekir `Checkpoint-Workflow` . Ek bir kimlik bilgisi etkinliği kullanmanız gerekmez.

![Etkinlik çıkışı](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>Grafik runbook 'unu dışarı aktarma

Yalnızca bir grafik runbook 'un yayımlanan sürümünü dışarı aktarabilirsiniz. Runbook henüz yayımlanmadıysa **dışa aktarma** düğmesi devre dışıdır. **Dışarı aktar** düğmesine tıkladığınızda, runbook yerel bilgisayarınıza indirilir. Dosyanın adı, bir **. graphrunbook** uzantısıyla Runbook adı ile eşleşir.

## <a name="import-a-graphical-runbook"></a>Grafik runbook 'unu içeri aktarma

Bir runbook eklerken **Içeri aktar** seçeneğini belirleyerek bir grafik veya grafik PowerShell iş akışı runbook dosyasını içeri aktarabilirsiniz. İçeri aktarılacak dosyayı seçtiğinizde, aynı adı koruyabilir veya yeni bir tane sağlayabilirsiniz. **Runbook türü** alanı, dosyanın seçili değerlendirir sonra runbook türünü görüntüler. Doğru olmayan farklı bir tür seçerek grafik Düzenleyicisi, olası çakışmaların olduğunu belirten bir ileti görüntüler ve dönüştürme sırasında sözdizimi hataları olabilir.

![Runbook 'u içeri aktar](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>Grafik runbook 'unu test etme

Azure Otomasyonu 'ndaki her grafik runbook 'un bir taslak sürümü ve yayımlanmış bir sürümü vardır. Yalnızca yayımlanan sürümü çalıştırabilirsiniz, ancak yalnızca taslak sürümünü düzenleyebilirsiniz. Yayımlanan sürüm Taslak sürümdeki herhangi bir değişiklikten etkilenmez. Taslak sürümü kullanıma hazırsa, onu yayımlarsınız ve bu, geçerli yayımlanmış sürümün taslak sürümünüzle üzerine yazar.

Yayımlanan sürümden değişmeden bırakarak Azure portal runbook 'un taslak sürümünü test edebilirsiniz. Alternatif olarak, runbook 'un herhangi bir sürümü değiştirmeden önce düzgün çalıştığını doğrulayabilmeniz için, yeni bir runbook 'u yayımlanmadan önce test edebilirsiniz. Bir runbook 'un testi, taslak sürümünü yürütür ve gerçekleştirdiği eylemlerin tamamlanmasını sağlar. İş geçmişi oluşturulmaz, ancak test çıkış bölmesi çıktıyı görüntüler.

Düzen için Runbook 'u açarak ve ardından **Test bölmesi**' ne tıklayarak grafik runbook 'larınızın test denetimini açın. Test denetimi giriş parametrelerine sorar ve **Başlat**' a tıklayarak runbook 'u başlatabilirsiniz.

## <a name="publish-a-graphical-runbook"></a>Grafik runbook 'unu yayımlama

Düzenlenecek runbook 'u açarak ve ardından **Yayımla**' ya tıklayarak bir grafik runbook 'unu yayımlayın. Runbook için olası durumlar şunlardır:

* Yeni--runbook henüz yayımlanmadı. 
* Yayımlandı--runbook yayımlandı.
* Düzenle--runbook yayımlandıktan sonra düzenlendi ve taslak ve yayımlanan sürümler farklı.

![Runbook durumları](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Runbook 'un yayımlanmış sürümüne dönmek için seçeneğiniz vardır. Bu işlem, runbook 'un son Yayımlanma sonrasında yapılan tüm değişiklikleri atar. Runbook 'un taslak sürümünü yayınlanan sürümle değiştirir.

## <a name="next-steps"></a>Sonraki adımlar

* Grafik runbook 'larını kullanmaya başlamak için bkz. [öğretici: grafik runbook 'U oluşturma](learn/automation-tutorial-runbook-graphical.md).
* Runbook türleri ve bunların avantajları ve sınırlamaları hakkında daha fazla bilgi edinmek için bkz. [Azure Otomasyonu runbook türleri](automation-runbook-types.md).
* Otomasyon farklı çalıştır hesabını kullanarak kimlik doğrulaması yapılacağını anlamak için bkz. [Farklı Çalıştır hesabı](automation-security-overview.md#run-as-account).
* PowerShell cmdlet başvurusu için bkz. [az. Automation](/powershell/module/az.automation/#automation).
