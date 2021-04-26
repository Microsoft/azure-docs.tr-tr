---
title: Azure Otomasyonu 'nda sertifikaları yönetme
description: Bu makalede runbook 'ların ve DSC yapılandırmalarının erişimine yönelik sertifikalarla nasıl çalışılacağı açıklanmaktadır.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 788f15cd1edad228e695e6e87f5b630b8e4fdf55
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834453"
---
# <a name="manage-certificates-in-azure-automation"></a>Azure Otomasyonu 'nda sertifikaları yönetme

Azure Otomasyonu, Azure Resource Manager kaynakları için [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) cmdlet 'ini kullanarak, sertifikaları runbook 'LAR ve DSC yapılandırmalarına göre güvenli bir şekilde depolar. Güvenli sertifika depolama, kimlik doğrulaması için sertifikaları kullanan runbook 'ları ve DSC yapılandırması oluşturmanızı veya bunları Azure ya da üçüncü taraf kaynaklarına eklemenizi sağlar.

>[!NOTE]
>Azure Otomasyonu 'nda güvenli varlıklar, kimlik bilgileri, sertifikalar, bağlantılar ve şifrelenmiş değişkenler içerir. Bu varlıklar, her Otomasyon hesabı için oluşturulan benzersiz bir anahtar kullanılarak şifrelenir ve otomasyon 'da depolanır. Otomasyon, anahtarı sistem tarafından yönetilen Key Vault hizmetinde depolar. Güvenli bir varlık depolamadan önce, otomasyon anahtarı Key Vault ' den yükler ve ardından varlığı şifrelemek için onu kullanır.

## <a name="powershell-cmdlets-to-access-certificates"></a>Sertifikalara erişim için PowerShell cmdlet 'leri

Aşağıdaki tablodaki cmdlet 'ler, PowerShell ile otomasyon sertifikaları oluşturur ve yönetir. Bunlar [az modüllerin](modules.md#az-modules)bir parçası olarak sevk ederler.

|Cmdlet |Açıklama|
| --- | ---|
|[Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate)|Runbook veya DSC yapılandırmasında kullanılacak sertifikayla ilgili bilgileri alır. Sertifikayı yalnızca iç `Get-AutomationCertificate` cmdlet 'ini kullanarak alabilirsiniz.|
|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)|Otomasyon 'da yeni bir sertifika oluşturur.|
|[Remove-AzAutomationCertificate](/powershell/module/Az.Automation/Remove-AzAutomationCertificate)|Bir sertifikayı otomatikleştirmeden kaldırır.|
|[Set-AzAutomationCertificate](/powershell/module/Az.Automation/Set-AzAutomationCertificate)|Sertifika dosyasını karşıya yükleme ve bir **. pfx** dosyası için parolayı ayarlama dahil olmak üzere var olan bir sertifikanın özelliklerini ayarlar.|

[Add-Azurecercertificateate](/powershell/module/servicemanagement/azure.service/add-azurecertificate) cmdlet 'i, belirtilen bulut hizmeti için bir hizmet sertifikası yüklemek için de kullanılabilir.

## <a name="internal-cmdlets-to-access-certificates"></a>Sertifikalara erişim için iç cmdlet 'ler

Aşağıdaki tablodaki iç cmdlet, runbook 'larınızda sertifikalara erişmek için kullanılır. Bu cmdlet, genel modülle birlikte gelir `Orchestrator.AssetManagement.Cmdlets` . Daha fazla bilgi için bkz. [iç cmdlet 'ler](modules.md#internal-cmdlets).

| İç cmdlet | Description |
|:---|:---|
|`Get-AutomationCertificate`|Runbook veya DSC yapılandırmasında kullanmak için bir sertifika alır. [System. Security. Cryptography. X509Certificates. X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) nesnesi döndürür.|

> [!NOTE]
> `Name` `Get-AutomationCertificate` Bir RUNBOOK veya DSC yapılandırmasında, içindeki değişkenleri kullanmaktan kaçının. Bu tür değişkenler, tasarım zamanında runbook 'lar ya da DSC yapılandırması ile otomasyon değişkenleri arasındaki bağımlılıkları bulmayı karmaşıklaştırır.

## <a name="python-functions-to-access-certificates"></a>Sertifikalara erişim için Python işlevleri

Bir Python 2 ve 3 runbook 'daki sertifikalara erişmek için aşağıdaki tablodaki işlevini kullanın. Python 3 runbook 'ları Şu anda önizleme aşamasındadır.

| İşlev | Açıklama |
|:---|:---|
| `automationassets.get_automation_certificate` | Bir sertifika varlığı hakkındaki bilgileri alır. |

> [!NOTE]
> `automationassets`Varlık işlevlerine erişmek Için Python runbook 'ınızın başlangıcında modülünü içeri aktarmanız gerekir.

## <a name="create-a-new-certificate"></a>Yeni bir sertifika oluştur

Yeni bir sertifika oluşturduğunuzda, Automation 'a bir. cer veya. pfx dosyası yüklersiniz. Sertifikayı dışa aktarılabilir olarak işaretlerseniz, bunu Otomasyon sertifika deposundan aktarabilirsiniz. Dışarı aktarılabilir değilse, yalnızca runbook veya DSC yapılandırmasında oturum açmak için kullanılabilir. Otomasyon, sertifikanın **Microsoft tarafından GELIŞTIRILMIŞ RSA ve AES şifreleme sağlayıcısına** sahip olmasını gerektirir.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Azure portal yeni bir sertifika oluşturun

1. Otomasyon hesabınızdan, sol taraftaki bölmede **paylaşılan kaynak** altında **Sertifikalar** ' ı seçin.
1. **Sertifikalar** sayfasında, **sertifika ekle**' yi seçin.
1. **Ad** alanına sertifika için bir ad yazın.
1. Bir **. cer** veya **. pfx** dosyasına gitmek Için, **sertifika dosyasını karşıya yükle** altında **bir dosya seç**' i seçin. Bir **. pfx** dosyası seçerseniz, bir parola belirtin ve verilemeyeceğini belirtin.
1. Yeni sertifika varlığını kaydetmek için **Oluştur** ' u seçin.

### <a name="create-a-new-certificate-with-powershell"></a>PowerShell ile yeni bir sertifika oluşturma

Aşağıdaki örnekte, yeni bir Otomasyon sertifikasının nasıl oluşturulduğu ve dışarı aktarılabilir olarak işaretlenme gösterilmiştir. Bu örnek, var olan bir **. pfx** dosyasını içeri aktarır.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath -Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Kaynak Yöneticisi şablonuyla yeni bir sertifika oluşturma

Aşağıdaki örnek, PowerShell aracılığıyla Kaynak Yöneticisi şablonu kullanarak Otomasyon hesabınıza nasıl bir sertifikanın dağıtılacağını göstermektedir:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path and filename>'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>' #A name of your choosing
$ResourceGroupName = '<resource group name>' #The one that holds your automation account
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName $ResourceGroupName -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>Sertifika Al

Bir sertifikayı almak için iç `Get-AutomationCertificate` cmdlet 'ini kullanın. [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) cmdlet 'ini kullanarak sertifika varlığı hakkında bilgi döndürüyor, ancak sertifikanın kendisi değil.

### <a name="textual-runbook-examples"></a>Metinsel runbook örnekleri

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aşağıdaki örnek, bir runbook 'ta bir bulut hizmetine nasıl sertifika ekleneceğini gösterir. Bu örnekte, parola şifrelenmiş bir Otomasyon değişkeninden alınır.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

# <a name="python-2"></a>[Python 2](#tab/python2)

Aşağıdaki örnekte, Python 2 runbook 'larında sertifikalara nasıl erişebileceğiniz gösterilmektedir.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

# <a name="python-3"></a>[Python 3 karşılaştırması](#tab/python3)

Aşağıdaki örnekte, Python 3 runbook 'larında (Önizleme) sertifikalara nasıl erişebileceğiniz gösterilmektedir.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print (cert)
```

---

### <a name="graphical-runbook-example"></a>Grafik runbook örneği

`Get-AutomationCertificate`Kitaplık bölmesinde sertifikaya sağ tıklayıp **tuvale Ekle**' yi seçerek bir grafik runbook 'una iç cmdlet için bir etkinlik ekleyin.

![Tuvale sertifika ekleme ekranının ekran görüntüsü](../media/certificates/automation-certificate-add-to-canvas.png)

Aşağıdaki görüntüde bir grafik runbook 'unda sertifika kullanmanın bir örneği gösterilmektedir.

![Grafik yazma örneğini örnek ekran görüntüsü](../media/certificates/graphical-runbook-add-certificate.png)

## <a name="next-steps"></a>Sonraki adımlar

* Sertifikalara erişmek için kullanılan cmdlet 'ler hakkında daha fazla bilgi için bkz. [Azure Automation 'da modülleri yönetme](modules.md).
* Runbook 'lar hakkında genel bilgi için bkz. [Azure Otomasyonu 'Nda runbook yürütme](../automation-runbook-execution.md).
* DSC yapılandırmalarının ayrıntıları için bkz. [Azure Otomasyonu durum yapılandırmasına genel bakış](../automation-dsc-overview.md).
