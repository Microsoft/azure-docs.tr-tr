---
title: Azure Otomasyonu'nda kimlik bilgilerini yönetme
description: Bu makalede, kimlik bilgileri varlıklarının nasıl oluşturulduğu ve bir runbook veya DSC yapılandırmasında nasıl kullanılacağı açıklanır.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6220a44e952aa4d9856ac5fc2077d254103d4a2c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834291"
---
# <a name="manage-credentials-in-azure-automation"></a>Azure Otomasyonu'nda kimlik bilgilerini yönetme

Otomasyon kimlik bilgileri varlığı, Kullanıcı adı ve parola gibi güvenlik kimlik bilgilerini içeren bir nesne barındırır. Runbook 'lar ve DSC yapılandırması, kimlik doğrulaması için bir [PSCredential](/dotnet/api/system.management.automation.pscredential) nesnesini kabul eden cmdlet 'leri kullanır. Alternatif olarak, `PSCredential` kimlik doğrulaması gerektiren bir uygulama veya hizmete sağlamak üzere nesnenin Kullanıcı adını ve parolasını ayıklayabilirler.

>[!NOTE]
>Azure Otomasyonu 'nda güvenli varlıklar, kimlik bilgileri, sertifikalar, bağlantılar ve şifrelenmiş değişkenler içerir. Bu varlıklar, her Otomasyon hesabı için oluşturulan benzersiz bir anahtar kullanılarak Azure Otomasyonu 'nda şifrelenir ve depolanır. Azure Otomasyonu, anahtarı sistem tarafından yönetilen Key Vault depolar. Otomasyon, güvenli bir varlık depolamadan önce anahtarı Key Vault 'den yükler ve ardından varlığı şifrelemek için kullanır. 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>Kimlik bilgilerine erişmek için kullanılan PowerShell cmdlet 'leri

Aşağıdaki tablodaki cmdlet 'ler, PowerShell ile otomasyon kimlik bilgilerini oluşturur ve yönetir. Bunlar [az modüllerin](modules.md#az-modules)bir parçası olarak sevk ederler.

| Cmdlet | Açıklama |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential) |Kimlik bilgisiyle ilgili meta verileri içeren bir [Credentialınfo](/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo) nesnesi alır. Cmdlet `PSCredential` nesnenin kendisini almaz.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential) |Yeni bir Otomasyon kimlik bilgisi oluşturur. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential) |Otomasyon kimlik bilgisini kaldırır. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential) |Mevcut bir Otomasyon kimlik bilgisinin özelliklerini ayarlar. |

## <a name="other-cmdlets-used-to-access-credentials"></a>Kimlik bilgilerine erişmek için kullanılan diğer cmdlet 'ler

Aşağıdaki tablodaki cmdlet 'ler, runbook 'larınızda ve DSC yapılandırmalarında kimlik bilgilerine erişmek için kullanılır. 

| Cmdlet | Açıklama |
|:--- |:--- |
| `Get-AutomationPSCredential` |`PSCredential`Runbook veya DSC yapılandırmasında kullanılacak bir nesne alır. Çoğu kez, ikincinin yalnızca kimlik bilgisi bilgilerini aldığı için cmdlet yerine bu [iç cmdlet](modules.md#internal-cmdlets) 'i kullanmanız gerekir `Get-AzAutomationCredential` . Bu bilgiler normalde başka bir cmdlet 'e geçmek için yararlı değildir. |
| [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) |Kullanıcı adı ve parola isteminde bir kimlik bilgisi alır. Bu cmdlet, varsayılan Microsoft. PowerShell. Security modülünün bir parçasıdır. Bkz. [varsayılan modüller](modules.md#default-modules).|
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) | Bir kimlik bilgisi varlığı oluşturur. Bu cmdlet, varsayılan Azure modülünün bir parçasıdır. Bkz. [varsayılan modüller](modules.md#default-modules).|

`PSCredential`Kodunuzda nesneleri almak için modülünü içeri aktarmanız gerekir `Orchestrator.AssetManagement.Cmdlets` . Daha fazla bilgi için bkz. [Azure Automation 'da modülleri yönetme](modules.md).

```powershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Parametresindeki değişkenleri kullanmaktan kaçının `Name` `Get-AutomationPSCredential` . Kullanımları runbook 'lar ya da DSC yapılandırması ile kimlik bilgisi varlıkları arasındaki bağımlılıkları tasarım zamanında karmaşıklaştırabilir.

## <a name="python-functions-that-access-credentials"></a>Kimlik bilgilerine erişen Python işlevleri

Aşağıdaki tablodaki işlev, Python 2 ve 3 runbook 'daki kimlik bilgilerine erişmek için kullanılır. Python 3 runbook 'ları Şu anda önizleme aşamasındadır.

| İşlev | Açıklama |
|:---|:---|
| `automationassets.get_automation_credential` | Bir kimlik bilgisi varlığı hakkındaki bilgileri alır. |

> [!NOTE]
> `automationassets`Varlık işlevlerine erişmek Için Python runbook 'ınızın en üstündeki modülü içeri aktarın.

## <a name="create-a-new-credential-asset"></a>Yeni bir kimlik bilgisi varlığı oluştur

Azure portal kullanarak veya Windows PowerShell kullanarak yeni bir kimlik bilgisi varlığı oluşturabilirsiniz.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Azure portal yeni bir kimlik bilgisi varlığı oluşturun

1. Otomasyon hesabınızdan, sol taraftaki bölmede, **paylaşılan kaynaklar** altında **kimlik bilgileri** ' ni seçin.
2. **Kimlik bilgileri** sayfasında **kimlik bilgisi ekle**' yi seçin.
3. Yeni kimlik bilgisi bölmesinde, adlandırma standartlarınızı izleyerek uygun bir kimlik bilgisi adı girin.
4. **Kullanıcı adı** ALANıNA erişim Kimliğinizi yazın.
5. Her iki parola alanı için de gizli erişim anahtarınızı girin.

    ![Yeni kimlik bilgisi oluştur](../media/credentials/credential-create.png)

6. Multi-Factor Authentication kutusu işaretliyse, işaretini kaldırın.
7. Yeni kimlik bilgisi varlığını kaydetmek için **Oluştur** ' a tıklayın.

> [!NOTE]
> Azure Otomasyonu, çok faktörlü kimlik doğrulaması kullanan kullanıcı hesaplarını desteklemez.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Windows PowerShell ile yeni bir kimlik bilgisi varlığı oluşturma

Aşağıdaki örnek, yeni bir Otomasyon kimlik bilgisi varlığının nasıl oluşturulacağını göstermektedir. `PSCredential`Önce ad ve parolayla bir nesne oluşturulur ve ardından kimlik bilgisi varlığını oluşturmak için kullanılır. Bunun yerine, `Get-Credential` kullanıcıdan bir ad ve parola yazıp yazmayabilmeniz için cmdlet 'ini kullanabilirsiniz.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>Kimlik bilgisi varlığı al

Bir runbook veya DSC yapılandırması, iç cmdlet ile bir kimlik bilgisi varlığı alır `Get-AutomationPSCredential` . Bu cmdlet, `PSCredential` kimlik bilgisi gerektiren bir cmdlet ile kullanabileceğiniz bir nesnesi alır. Ayrıca, tek tek kullanılacak kimlik bilgisi nesnesinin özelliklerini de alabilirsiniz. Nesnenin Kullanıcı adı ve güvenli parola özellikleri vardır. 

> [!NOTE]
> `Get-AzAutomationCredential`Cmdlet 'i, `PSCredential` kimlik doğrulaması için kullanılabilecek bir nesne almaz. Yalnızca kimlik bilgileri hakkında bilgi sağlar. Bir runbook 'ta kimlik bilgisi kullanmanız gerekiyorsa, bunu kullanarak bir nesne olarak almanız gerekir `PSCredential` `Get-AutomationPSCredential` .

Alternatif olarak, bir parolanın güvenli olmayan bir sürümünü temsil eden bir [NetworkCredential](/dotnet/api/system.net.networkcredential) nesnesi almak Için [getnetworkcredential](/dotnet/api/system.management.automation.pscredential.getnetworkcredential) yöntemini kullanabilirsiniz.

### <a name="textual-runbook-example"></a>Metinsel runbook örneği

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aşağıdaki örnek, bir runbook 'ta PowerShell kimlik bilgisinin nasıl kullanılacağını göstermektedir. Kimlik bilgisini alır ve Kullanıcı adını ve parolasını değişkenlere atar.

```powershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)ile Azure 'da kimlik doğrulaması yapmak için kimlik bilgisi de kullanabilirsiniz. Çoğu durumda, bir [Farklı Çalıştır hesabı](../automation-security-overview.md#run-as-accounts) kullanmalı ve [Get-azautomationconnection](../automation-connections.md)ile bağlantıyı almanız gerekir.

```powershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$securePassword)

Connect-AzAccount -Credential $myPsCred
```

# <a name="python-2"></a>[Python 2](#tab/python2)

Aşağıdaki örnekte, Python 2 runbook 'larında kimlik bilgilerine erişme örneği gösterilmektedir.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

# <a name="python-3"></a>[Python 3 karşılaştırması](#tab/python3)

Aşağıdaki örnekte, Python 3 runbook 'larında (Önizleme) kimlik bilgilerine erişme örneği gösterilmektedir.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print (cred["username"])
print (cred["password"])
```

---

### <a name="graphical-runbook-example"></a>Grafik runbook örneği

Grafik `Get-AutomationPSCredential` Düzenleyicinin Kitaplık bölmesinde kimlik bilgisine sağ tıklayıp **tuvale Ekle**' yi seçerek bir grafik runbook 'una iç cmdlet için bir etkinlik ekleyebilirsiniz.

![Tuvale kimlik bilgisi cmdlet 'i Ekle](../media/credentials/credential-add-canvas.png)

Aşağıdaki görüntüde bir grafik runbook 'unda kimlik bilgisi kullanmanın bir örneği gösterilmektedir. Bu durumda kimlik bilgileri, Azure ['da kimlik doğrulaması yapmak için Azure Otomasyonu 'nda Azure ad kullanma](../automation-use-azure-ad.md)bölümünde açıklandığı gibi, bir runbook için Azure kaynaklarına yönelik kimlik doğrulaması sağlar. İlk etkinlik, Azure aboneliğine erişimi olan kimlik bilgisini alır. Daha sonra hesap bağlantısı etkinliği bu kimlik bilgisini, bundan sonra gelen etkinlikler için kimlik doğrulaması sağlamak üzere kullanır. Tek bir nesne beklediği için burada bir işlem [hattı bağlantısı](../automation-graphical-authoring-intro.md#use-links-for-workflow) kullanılır `Get-AutomationPSCredential` .  

![İşlem hattı bağlantısı olan kimlik bilgisi iş akışı örneği](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>DSC yapılandırmasında kimlik bilgilerini kullanma

Azure Otomasyonu 'ndaki DSC yapılandırması, kullanarak kimlik bilgisi varlıkları ile çalışabileceği sürece `Get-AutomationPSCredential` , kimlik bilgisi varlıklarını parametreler aracılığıyla da geçirebilir. Daha fazla bilgi için bkz. [Azure Automation DSC yapılandırma derleme](../automation-dsc-compile.md#credential-assets).

## <a name="next-steps"></a>Sonraki adımlar

* Sertifikalara erişmek için kullanılan cmdlet 'ler hakkında daha fazla bilgi için bkz. [Azure Automation 'da modülleri yönetme](modules.md).
* Runbook 'lar hakkında genel bilgi için bkz. [Azure Otomasyonu 'Nda runbook yürütme](../automation-runbook-execution.md).
* DSC yapılandırmalarının ayrıntıları için bkz. [Azure Otomasyonu durum yapılandırmasına genel bakış](../automation-dsc-overview.md).
