---
title: Azure Otomasyonu 'nda değişken varlıklar
description: Değişken varlıklar, Azure Otomasyonu 'ndaki tüm runbook 'lar ve DSC yapılandırmalarının kullanabildiği değerlerdir.  Bu makalede, değişkenlerin ayrıntıları ve hem metin hem de grafik yazarken bunlarla nasıl çalışılacağı açıklanmaktadır.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3fe008d20ab43636b59861bcc5a7914ba0fca17e
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910058"
---
# <a name="variable-assets-in-azure-automation"></a>Azure Otomasyonu 'nda değişken varlıklar

Değişken varlıklar, Otomasyon hesabınızdaki tüm runbook 'lar ve DSC yapılandırmalarının kullanabildiği değerlerdir. Azure portal, PowerShell içinden veya DSC yapılandırmasından yönetilebilecek. Otomasyon değişkenleri aşağıdaki senaryolarda kullanışlıdır:

- Birden çok runbook veya DSC yapılandırması arasında bir değer paylaşma.

- Aynı runbook veya DSC yapılandırmasından birden çok iş arasında bir değer paylaşma.

- Portaldan veya IP adları, belirli bir kaynak grubu, AD etki alanı adı ve daha fazlası gibi yaygın yapılandırma öğelerinin bir kümesi gibi runbook 'lar ya da DSC yapılandırmaları tarafından kullanılan PowerShell komut satırından bir değeri yönetin.  

Otomasyon değişkenleri kalıcı olduğundan, runbook veya DSC yapılandırması başarısız olsa bile bunlar kullanılabilir. Bu davranış, bir değerin daha sonra başka bir runbook tarafından kullanılmasına izin verir veya bir sonraki çalıştırılışında aynı runbook veya DSC yapılandırması tarafından kullanılır.

Bir değişken oluşturulduğunda, şifrelenmiş olarak depolanmasını belirtebilirsiniz. Şifrelenmiş değişkenler Azure Otomasyonu 'nda güvenli bir şekilde depolanır ve değeri Azure PowerShell modülünün bir parçası olarak gelen [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) cmdlet 'inden alınamaz. Şifrelenmiş bir değerin alınabileceği tek yolu bir runbook veya DSC yapılandırmasındaki **Get-AutomationVariable** etkinliğidir. Şifrelenmiş bir değişkeni şifrelenmeyen olarak değiştirmek istiyorsanız, değişkeni şifreli olarak silip yeniden oluşturmanız gerekir.

>[!NOTE]
>Azure Otomasyonu 'nda güvenli varlıklar, kimlik bilgileri, sertifikalar, bağlantılar ve şifrelenmiş değişkenler içerir. Bu varlıklar, her Otomasyon hesabı için oluşturulan benzersiz bir anahtar kullanılarak Azure Otomasyonu 'nda şifrelenir ve depolanır. Bu anahtar, sistem tarafından yönetilen bir Key Vault depolanır. Güvenli bir varlık depolamadan önce, anahtar Key Vault yüklenir ve ardından varlığı şifrelemek için kullanılır. Bu işlem Azure Otomasyonu tarafından yönetiliyor.

## <a name="variable-types"></a>Değişken türleri

Azure portal bir değişken oluşturduğunuzda, portalın değişken değerini girmeye uygun denetimi görüntülemesi için açılan listeden bir veri türü belirtmeniz gerekir. Değişken bu veri türüyle sınırlı değil. Farklı türde bir değer belirtmek istiyorsanız, değişkeni Windows PowerShell kullanarak ayarlamanız gerekir. **Tanımlı değil**' i belirtirseniz, değişkenin değeri **$null**olarak ayarlanır ve değeri [set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) cmdlet 'i veya **set-AutomationVariable** etkinliği ile ayarlamanız gerekir. Portalda karmaşık bir değişken türü için değer oluşturamaz veya değiştiremezsiniz, ancak Windows PowerShell kullanarak herhangi bir türde bir değer sağlayabilirsiniz. Karmaşık türler bir [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)olarak döndürülür.

Bir dizi veya Hashtable oluşturup değişkenine kaydederek birden çok değeri tek bir değişkende saklayabilirsiniz.

Otomasyon 'da kullanılabilen değişken türlerinin bir listesi aşağıda verilmiştir:

* Dize
* Integer
* DateTime
* Boole değeri
* Null

## <a name="azurerm-powershell-cmdlets"></a>Azurerd PowerShell cmdlet 'leri

Azurerd için aşağıdaki tablodaki cmdlet 'ler Windows PowerShell ile otomasyon kimlik bilgisi varlıkları oluşturmak ve yönetmek için kullanılır. Bunlar, Automation runbook 'larda ve DSC yapılandırmalarında kullanıma sunulan [Azurerd. Automation modülünün](/powershell/azure/overview)bir parçası olarak gelir.

| Cmdlet'ler | Açıklama |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Mevcut bir değişkenin değerini alır.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Yeni bir değişken oluşturur ve değerini ayarlar.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Varolan bir değişkeni kaldırır.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Mevcut bir değişken için değeri ayarlar.|

## <a name="activities"></a>Etkinlikler

Aşağıdaki tablodaki etkinlikler bir runbook ve DSC yapılandırmalarının değişkenlerine erişmek için kullanılır. Get-AzureRmAutomationVariable ve Get-AutomationVariable cmdlet 'leri arasındaki fark, bu belgenin başlangıcında yukarıda açıklanan şekilde belirlenir.

| Etkinlikler | Açıklama |
|:---|:---|
|Get-AutomationVariable|Mevcut bir değişkenin değerini alır.|
|Set-AutomationVariable|Mevcut bir değişken için değeri ayarlar.|

> [!NOTE]
> Runbook veya DSC yapılandırması arasındaki bağımlılıkları keşfetmenizi ve tasarım zamanında Otomasyon değişkenlerini karmaşık hale getirebileceğinden, bir runbook veya DSC yapılandırmasında **Get-AutomationVariable** öğesinin – name parametresinde değişken kullanmaktan kaçının.

Aşağıdaki tablodaki işlevler, bir Python2 runbook 'taki değişkenlere erişmek ve bunları almak için kullanılır.

|Python2 Işlevleri|Açıklama|
|:---|:---|
|automationassets.get_automation_variable|Mevcut bir değişkenin değerini alır. |
|automationassets.set_automation_variable|Mevcut bir değişken için değeri ayarlar. |

> [!NOTE]
> Varlık işlevlerine erişebilmek için Python runbook 'unun en üstündeki "automationvarlıklar" modülünü içeri aktarmanız gerekir.

## <a name="creating-a-new-automation-variable"></a>Yeni bir Otomasyon değişkeni oluşturma

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Azure portal ile yeni bir değişken oluşturmak için

1. Otomasyon hesabınızdan **varlıklar** kutucuğuna tıklayın ve ardından **varlıklar** dikey penceresinde **değişkenler**' i seçin.
2. **Değişkenler** kutucuğunda **değişken Ekle**' yi seçin.
3. **Yeni değişken** dikey penceresindeki seçenekleri doldurun ve **Oluştur** ' a tıklayarak yeni değişkeni Kaydet ' e tıklayın.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Windows PowerShell ile yeni bir değişken oluşturmak için

[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) cmdlet 'i yeni bir değişken oluşturur ve ilk değerini ayarlar. [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)kullanarak değeri alabilirsiniz. Değer basit bir tür ise, aynı tür döndürülür. Karmaşık bir tür ise, bir **PSCustomObject** döndürülür.

Aşağıdaki örnek komutlarda dize türünde bir değişkenin nasıl oluşturulduğu ve sonra değerinin nasıl döndürdüğü gösterilmektedir.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Aşağıdaki örnek komutlarda, karmaşık bir türe sahip bir değişken oluşturma ve ardından özelliklerini döndürme gösterilmektedir. Bu durumda, **Get-AzureRmVm** ' den bir sanal makine nesnesi kullanılır.

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Runbook veya DSC yapılandırmasında değişken kullanma

Bir PowerShell runbook 'u veya DSC yapılandırmasındaki bir otomasyon değişkeninin değerini ve **Get-AutomationVariable** değerini almak için **set-AutomationVariable** etkinliğini kullanın. İş akışı etkinliklerinden daha az verimlidir olduğundan, **set-azurermautomationvariable** veya **Get-azurermautomationvariable** cmdlet 'LERINI bir runbook veya DSC yapılandırmasında kullanmamalısınız. **Get-AzureRMAutomationVariable**ile güvenli değişkenlerin değerini de alamazsınız. Bir runbook veya DSC yapılandırması içinden yeni bir değişken oluşturmanın tek yolu [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) cmdlet 'ini kullanmaktır.

### <a name="textual-runbook-samples"></a>Metinsel runbook örnekleri

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Bir değişkenden basit bir değer ayarlama ve alma

Aşağıdaki örnek komutlar, metinsel bir runbook 'ta nasıl değişken ayarlanacağını ve alınacağını göstermektedir. Bu örnekte, *Numberofyinelemelerini* ve *NumberOfRunnings* adlı tamsayı türündeki değişkenlerin ve *samplemessage* adlı dize türünde bir değişkenin oluşturulduğu varsayılır.

```powershell
$NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Python2 içinde değişken ayarlama ve alma

Aşağıdaki örnek kod, bir değişkeni nasıl kullanacağınızı, bir değişkenin nasıl ayarlanacağını ve bir Python2 runbook 'unda var olmayan bir değişken için özel durum nasıl işleneceğini gösterir.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("non-existing variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Grafik runbook örnekleri

Grafik bir runbook 'ta, grafik düzenleyicinin Kitaplık bölmesinde değişkene sağ tıklayıp istediğiniz etkinliği seçerek **Get-AutomationVariable** veya **set-AutomationVariable** ' ı ekleyin.

![Tuvale değişken Ekle](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Bir değişkendeki değerleri ayarlama

Aşağıdaki görüntüde, bir değişken grafik runbook 'unda basit bir değerle güncelleştirilecek örnek etkinlikler gösterilmektedir. Bu örnekte, **Get-AzureRmVM** tek bir Azure sanal makinesini alır ve bilgisayar adı, bir dize türüne sahip var olan bir Otomasyon değişkenine kaydedilir. Çıktıda yalnızca tek bir nesneyi beklediğinizden, [bağlantının bir işlem hattı veya sıra](../automation-graphical-authoring-intro.md#links-and-workflow) olmasından bağımsız değildir.

![Basit değişken ayarla](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Sonraki Adımlar

- Grafik yazarken etkinlikleri birbirine bağlama hakkında daha fazla bilgi için bkz. [grafik yazarken bağlantılar](../automation-graphical-authoring-intro.md#links-and-workflow)
- Grafik runbook'ları kullanmaya başlamak için bkz. [İlk grafik runbook uygulamam](../automation-first-runbook-graphical.md)
