---
title: Azure Otomasyonu 'nda değişkenleri yönetme
description: Bu makalede runbook 'larda ve DSC yapılandırmalarında değişkenlerle nasıl çalışılacağı açıklanmaktadır.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9f1ace00356583dbb6102317e3d157fb58682710
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832588"
---
# <a name="manage-variables-in-azure-automation"></a>Azure Otomasyonu 'nda değişkenleri yönetme

Değişken varlıklar, Otomasyon hesabınızdaki tüm runbook 'lar ve DSC yapılandırmalarının kullanabildiği değerlerdir. Bunları, PowerShell 'den, runbook içinden veya bir DSC yapılandırmasında Azure portal yönetebilirsiniz.

Otomasyon değişkenleri aşağıdaki senaryolar için yararlıdır:

- Birden çok runbook veya DSC yapılandırması arasında bir değer paylaşma.

- Aynı runbook veya DSC yapılandırmasından birden çok iş arasında bir değer paylaşma.

- Portalda veya PowerShell komut satırından runbook veya DSC yapılandırmalarının kullandığı bir değeri yönetme. Örneğin, belirli bir VM adı listesi, belirli bir kaynak grubu, AD etki alanı adı ve daha fazlası gibi yaygın bir yapılandırma öğeleri kümesidir.  

Azure Otomasyonu değişkenleri sürdürür ve bir runbook ya da DSC yapılandırması başarısız olsa bile bunları kullanılabilir hale getirir. Bu davranış, bir runbook veya DSC yapılandırmasının, daha sonra başka bir runbook tarafından veya bir sonraki çalıştırılışında aynı runbook veya DSC yapılandırması tarafından kullanılan bir değer ayarlamasına olanak tanır.

Azure Otomasyonu, her şifreli değişkeni güvenli bir şekilde depolar. Bir değişken oluşturduğunuzda, Azure Otomasyonu tarafından güvenli bir varlık olarak şifrelemeyi ve depolamayı belirtebilirsiniz. Değişkeni oluşturduktan sonra, değişkeni yeniden oluşturmadan şifreleme durumunu değiştiremezsiniz. Önceden şifrelenmeyen hassas verileri depolayan Otomasyon hesabı değişkenleri varsa, bunları silmeniz ve şifrelenmiş değişkenler olarak yeniden oluşturmanız gerekir. Azure Güvenlik Merkezi 'nin önerisi, [Otomasyon hesabı değişkenlerinde](../../security-center/recommendations-reference.md#recs-compute)açıklandığı şekilde tüm Azure Otomasyonu değişkenlerini şifreleyebilmelidir. Bu güvenlik önerilerinden hariç tutmak istediğiniz şifrelenmemiş değişkenlere sahipseniz, bkz. muafiyet kuralı oluşturmak için [öneriler ve güvenli puandan bir kaynağı muaf](../../security-center/exempt-resource.md) tutma.

>[!NOTE]
>Azure Otomasyonu 'nda güvenli varlıklar, kimlik bilgileri, sertifikalar, bağlantılar ve şifrelenmiş değişkenler içerir. Bu varlıklar, her Otomasyon hesabı için oluşturulan benzersiz bir anahtar kullanılarak Azure Otomasyonu 'nda şifrelenir ve depolanır. Azure Otomasyonu, anahtarı sistem tarafından yönetilen Key Vault depolar. Otomasyon, güvenli bir varlık depolamadan önce anahtarı Key Vault 'den yükler ve ardından varlığı şifrelemek için kullanır.

## <a name="variable-types"></a>Değişken türleri

Azure portal bir değişken oluşturduğunuzda, portalın değişken değerini girmek için uygun denetimi görüntülemesi için açılan listeden bir veri türü belirtmeniz gerekir. Azure Otomasyonu 'nda bulunan değişken türleri şunlardır:

* Dize
* Tamsayı
* DateTime
* Boole
* Null

Değişken, belirtilen veri türüyle sınırlı değil. Farklı türde bir değer belirtmek istiyorsanız, değişkeni Windows PowerShell kullanarak ayarlamanız gerekir. Belirtirseniz `Not defined` , değişkenin değeri null olarak ayarlanır. Değeri [set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable) cmdlet 'i veya iç cmdlet ile ayarlamanız gerekir `Set-AutomationVariable` . `Set-AutomationVariable`Azure korumalı alan ortamında veya bir Windows hibrit Runbook Worker üzerinde çalışması amaçlanan runbook 'larınızda kullanın.

Karmaşık bir değişken türü için değer oluşturmak veya değiştirmek için Azure portal kullanamazsınız. Ancak, Windows PowerShell kullanarak herhangi bir türde bir değer sağlayabilirsiniz. Karmaşık türler üzerindeNewtonsoft.Jsolarak alınır [ . ](https://www.newtonsoft.com/json/help/html/N_Newtonsoft_Json_Linq.htm) PSObject türü [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)yerine karmaşık nesne türü Için LINQ. jproperty.

Bir dizi veya Hashtable oluşturup değişkenine kaydederek birden çok değeri tek bir değişkende saklayabilirsiniz.

>[!NOTE]
>VM adı değişkenleri en fazla 80 karakter olabilir. Kaynak grubu değişkenleri en fazla 90 karakter olabilir. Bkz. [Azure kaynakları Için adlandırma kuralları ve kısıtlamaları](../../azure-resource-manager/management/resource-name-rules.md).

## <a name="powershell-cmdlets-to-access-variables"></a>Değişkenlere erişmek için PowerShell cmdlet 'leri

Aşağıdaki tablodaki cmdlet 'ler, PowerShell ile otomasyon değişkenleri oluşturup yönetir. Bunlar [az modüllerin](modules.md#az-modules)bir parçası olarak sevk ederler.

| Cmdlet | Açıklama |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable) | Mevcut bir değişkenin değerini alır. Değer basit bir tür ise, aynı tür alınır. Karmaşık bir tür ise, bir `PSCustomObject` tür alınır. <sup>1</sup>|
|[New-AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable) | Yeni bir değişken oluşturur ve değerini ayarlar.|
|[Remove-AzAutomationVariable](/powershell/module/az.automation/remove-azautomationvariable)| Varolan bir değişkeni kaldırır.|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable)| Mevcut bir değişken için değeri ayarlar. |

<sup>1</sup> şifrelenen bir değişkenin değerini almak için bu cmdlet 'i kullanamazsınız. Bunu yapmanın tek yolu, `Get-AutomationVariable` bir runbook veya DSC yapılandırmasında iç cmdlet 'ini kullanmaktır. Örneğin, şifrelenmiş bir değişkenin değerini görmek için, değişkeni almak üzere bir runbook oluşturup çıkış akışına yazabilirsiniz:

```powershell
$encryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $encryptvar"
```

## <a name="internal-cmdlets-to-access-variables"></a>Değişkenlere erişmek için iç cmdlet 'ler

Aşağıdaki tablodaki iç cmdlet 'ler, runbook 'larınızda ve DSC yapılandırmalarında değişkenlere erişmek için kullanılır. Bu cmdlet 'ler genel modülle birlikte gelir `Orchestrator.AssetManagement.Cmdlets` . Daha fazla bilgi için bkz. [iç cmdlet 'ler](modules.md#internal-cmdlets).

| İç cmdlet | Description |
|:---|:---|
|`Get-AutomationVariable`|Mevcut bir değişkenin değerini alır.|
|`Set-AutomationVariable`|Mevcut bir değişken için değeri ayarlar.|

> [!NOTE]
> `Name` `Get-AutomationVariable` Bir RUNBOOK veya DSC yapılandırmasındaki cmdlet parametresindeki değişkenleri kullanmaktan kaçının. Bir değişkenin kullanımı, runbook 'lar ile otomasyon değişkenleri arasındaki bağımlılıkları tasarım zamanında bulmayı karmaşıklaştırır.

## <a name="python-functions-to-access-variables"></a>Değişkenlere erişmek için Python işlevleri

Aşağıdaki tablodaki işlevler, Python 2 ve 3 runbook 'ta değişkenlere erişmek için kullanılır. Python 3 runbook 'ları Şu anda önizleme aşamasındadır.

|Python Işlevleri|Description|
|:---|:---|
|`automationassets.get_automation_variable`|Mevcut bir değişkenin değerini alır. |
|`automationassets.set_automation_variable`|Mevcut bir değişken için değeri ayarlar. |

> [!NOTE]
> `automationassets`Varlık işlevlerine erişmek Için Python runbook 'unun en üstündeki modülünü içeri aktarmanız gerekir.

## <a name="create-and-get-a-variable"></a>Değişken oluşturma ve edinme

>[!NOTE]
>Bir değişkenin şifrelemesini kaldırmak istiyorsanız, değişkeni silmeniz ve şifrelenmemiş olarak yeniden oluşturmanız gerekir.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Azure portal kullanarak bir değişken oluşturun ve alın

1. Otomasyon hesabınızdan, sol taraftaki bölmede **paylaşılan kaynaklar**' ın altında **değişkenler** ' i seçin.
2. **Değişkenler** sayfasında, **değişken Ekle**' yi seçin.
3. **Yeni değişken sayfasındaki seçenekleri** doldurun ve sonra yeni değişkeni kaydetmek için **Oluştur** ' u seçin.

> [!NOTE]
> Şifrelenmiş bir değişken kaydedildikten sonra portalda görüntülenemez. Yalnızca güncelleştirilmiş olabilir.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Windows PowerShell 'de değişken oluşturma ve edinme

Runbook veya DSC yapılandırmanız `New-AzAutomationVariable` Yeni bir değişken oluşturmak ve ilk değerini ayarlamak için cmdlet 'ini kullanır. Değişken şifrelenirse, çağrının parametresini kullanması gerekir `Encrypted` . Komut dosyası, kullanarak değişkenin değerini alabilir `Get-AzAutomationVariable` .

>[!NOTE]
>Bir PowerShell betiği şifreli bir değer alamaz. Bunu yapmanın tek yolu, iç `Get-AutomationVariable` cmdlet 'ini kullanmaktır.

Aşağıdaki örnek, bir dize değişkeninin nasıl oluşturulacağını gösterir ve ardından değerini döndürür.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"
$variableValue = "My String"

New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
-AutomationAccountName "MyAutomationAccount" -Name 'MyStringVariable' `
-Encrypted $false -Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name 'MyStringVariable').Value
```

Aşağıdaki örnek, karmaşık bir türe sahip bir değişken oluşturma ve ardından özelliklerini alma işlemlerinin nasıl yapılacağını gösterir. Bu durumda, [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) öğesinden bir sanal makine nesnesi, özelliklerinin bir alt kümesini belirtmek için kullanılır.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"

$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" -Name "VM01" | Select Name, Location, Extensions
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" -AutomationAccountName "MyAutomationAccount" -Name "MyComplexVariable" -Encrypted $false -Value $vm

$vmValue = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name "MyComplexVariable"

$vmName = $vmValue.Value.Name
$vmTags = $vmValue.Value.Tags
```

## <a name="textual-runbook-examples"></a>Metinsel runbook örnekleri

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aşağıdaki örnek, metinsel bir runbook 'ta nasıl değişken ayarlanacağını ve alınacağını gösterir. Bu örnekte, **NumberOfIterations** ve **numberOfRunnings** adlı tamsayı değişkenlerinin oluşturulması ve **samplemessage** adlı bir dize değişkeni olduğunu varsaymaktadır.

```powershell
$rgName = "ResourceGroup01"
$accountName = "MyAutomationAccount"

$numberOfIterations = Get-AutomationVariable -Name "numberOfIterations"
$numberOfRunnings = Get-AutomationVariable -Name "numberOfRunnings"
$sampleMessage = Get-AutomationVariable -Name "sampleMessage"

Write-Output "Runbook has been run $numberOfRunnings times."

for ($i = 1; $i -le $numberOfIterations; $i++) {
    Write-Output "$i`: $sampleMessage"
}
Set-AutomationVariable -Name numberOfRunnings -Value ($numberOfRunnings += 1)
```

# <a name="python-2"></a>[Python 2](#tab/python2)

Aşağıdaki örnek, bir değişken alma, değişken ayarlama ve Python 2 runbook 'ta varolmayan bir değişken için özel durum işleme işlemlerinin nasıl yapılacağını gösterir.

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
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

# <a name="python-3"></a>[Python 3 karşılaştırması](#tab/python3)

Aşağıdaki örnek, bir değişken alma, değişken ayarlama ve Python 3 runbook (Önizleme) içinde varolmayan bir değişken için özel durum işleme işlemlerinin nasıl yapılacağını gösterir.

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
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print ("variable not found")
```

---

## <a name="graphical-runbook-examples"></a>Grafik runbook örnekleri

Grafik runbook 'unda, **Get-AutomationVariable** veya **set-AutomationVariable** iç cmdlet 'leri için etkinlik ekleyebilirsiniz. Grafik düzenleyicinin Kitaplık bölmesinde her bir değişkene sağ tıklayıp istediğiniz etkinliği seçmeniz yeterlidir.

![Tuvale değişken Ekle](../media/variables/runbook-variable-add-canvas.png)

Aşağıdaki görüntüde, bir değişken grafik runbook 'unda basit bir değerle güncelleştirilecek örnek etkinlikler gösterilmektedir. Bu örnekte, için etkinlik `Get-AzVM` tek bir Azure sanal makinesini alır ve bilgisayar adını var olan bir Otomasyon dizesi değişkenine kaydeder. Kodun yalnızca çıktıda tek bir nesne beklediği için [bağlantının bir ardışık düzen veya sıra](../automation-graphical-authoring-intro.md#use-links-for-workflow) olmasından bağımsız değildir.

![Basit değişken ayarla](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Sonraki adımlar

- Değişkenlere erişmek için kullanılan cmdlet 'ler hakkında daha fazla bilgi edinmek için bkz. [Azure Automation 'da modülleri yönetme](modules.md).

- Runbook 'lar hakkında genel bilgi için bkz. [Azure Otomasyonu 'Nda runbook yürütme](../automation-runbook-execution.md).

- DSC yapılandırmalarının ayrıntıları için bkz. [Azure Otomasyonu durum yapılandırmasına genel bakış](../automation-dsc-overview.md).
