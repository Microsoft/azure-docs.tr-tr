---
title: Azure Otomasyonu 'nda bağlantıları yönetme
description: Bu makalede, Azure Otomasyonu bağlantılarının dış hizmetler veya uygulamalarla nasıl yönetileceği ve Runbook 'larda bunlarla nasıl çalışacakları açıklanır.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: has-adal-ref, devx-track-azurepowershell
ms.openlocfilehash: 4b5983d6ea4ea9065d1292a2c5ee0c094cea093b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834903"
---
# <a name="manage-connections-in-azure-automation"></a>Azure Otomasyonu 'nda bağlantıları yönetme

Bir Azure Otomasyonu bağlantı varlığı aşağıda listelenen bilgileri içerir. Bu bilgi, bir runbook veya DSC yapılandırmasından bir dış hizmete veya uygulamaya bağlanmak için gereklidir. 

* Kullanıcı adı ve parola gibi kimlik doğrulaması için gereken bilgiler
* URL veya bağlantı noktası gibi bağlantı bilgileri

Bağlantı varlığı, belirli bir uygulamaya bağlanmak için tüm özellikleri birlikte tutar, bu da birden çok değişken oluşturulmasını gereksiz hale getirir. Bir bağlantının değerlerini tek bir yerde düzenleyebilir ve bir bağlantı adını tek bir parametrede runbook veya DSC yapılandırmasına geçirebilirsiniz. Runbook veya yapılandırma, iç cmdlet 'ini kullanarak bir bağlantının özelliklerine erişir `Get-AutomationConnection` .

Bir bağlantı oluşturduğunuzda bir bağlantı türü belirtmeniz gerekir. Bağlantı türü bir özellikler kümesini tanımlayan bir şablondur. Meta veri dosyası ile tümleştirme modülünü kullanarak Azure Otomasyonu 'na bir bağlantı türü ekleyebilirsiniz. Tümleştirme modülü bir bağlantı türü içeriyorsa ve otomasyon hesabınıza içeri aktarıldıysa, [Azure Otomasyonu API 'si](/previous-versions/azure/reference/mt163818(v=azure.100)) kullanılarak bir bağlantı türü oluşturmak da mümkündür. 

>[!NOTE]
>Azure Otomasyonu 'nda güvenli varlıklar, kimlik bilgileri, sertifikalar, bağlantılar ve şifrelenmiş değişkenler içerir. Bu varlıklar, her Otomasyon hesabı için oluşturulan benzersiz bir anahtar kullanılarak Azure Otomasyonu 'nda şifrelenir ve depolanır. Azure Otomasyonu, anahtarı sistem tarafından yönetilen Key Vault depolar. Otomasyon, güvenli bir varlık depolamadan önce anahtarı Key Vault 'den yükler ve ardından varlığı şifrelemek için kullanır. 

## <a name="connection-types"></a>Bağlantı türleri

Azure Otomasyonu aşağıdaki yerleşik bağlantı türlerini kullanılabilir hale getirir:

* `Azure` -Klasik kaynakları yönetmek için kullanılan bir bağlantıyı temsil eder.
* `AzureServicePrincipal` -Azure farklı çalıştır hesabı tarafından kullanılan bir bağlantıyı temsil eder.
* `AzureClassicCertificate` -Klasik Azure farklı çalıştır hesabı tarafından kullanılan bir bağlantıyı temsil eder.

Çoğu durumda, bir [Farklı Çalıştır hesabı](automation-security-overview.md)oluşturduğunuzda oluşturulduğundan bir bağlantı kaynağı oluşturmanız gerekmez.

## <a name="powershell-cmdlets-to-access-connections"></a>Bağlantılara erişim için PowerShell cmdlet 'leri

Aşağıdaki tablodaki cmdlet 'ler, PowerShell ile otomasyon bağlantıları oluşturur ve yönetir. Bunlar [az modüllerin](shared-resources/modules.md#az-modules)bir parçası olarak sevk ederler.

|Cmdlet|Açıklama|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection)|Bir bağlantı hakkındaki bilgileri alır.|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)|Yeni bir bağlantı oluşturur.|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection)|Varolan bir bağlantıyı kaldırır.|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue)|Mevcut bir bağlantı için belirli bir alanın değerini ayarlar.|

## <a name="internal-cmdlets-to-access-connections"></a>Bağlantılara erişim için iç cmdlet 'ler

Aşağıdaki tablodaki iç cmdlet, runbook 'larınızda ve DSC yapılandırmalarında bağlantılara erişmek için kullanılır. Bu cmdlet, genel modülle birlikte gelir `Orchestrator.AssetManagement.Cmdlets` . Daha fazla bilgi için bkz. [iç cmdlet 'ler](shared-resources/modules.md#internal-cmdlets).

|İç cmdlet|Description|
|---|---|
|`Get-AutomationConnection` | Bağlantıdaki farklı alanların değerlerini alır ve bunları bir [karma tablosu](/powershell/module/microsoft.powershell.core/about/about_hash_tables)olarak döndürür. Daha sonra bu Hashtable 'ı runbook veya DSC yapılandırmasındaki uygun komutlarla kullanabilirsiniz.|

>[!NOTE]
>Parametresi ile değişkenleri kullanmaktan kaçının `Name` `Get-AutomationConnection` . Bu durumda değişkenlerin kullanılması, runbook 'ları veya DSC yapılandırması ile bağlantı varlıkları arasındaki bağımlılıkları tasarım zamanında karmaşıklaştırır.

## <a name="python-functions-to-access-connections"></a>Bağlantılara erişim için Python işlevleri

Aşağıdaki tablodaki işlev, Python 2 ve 3 runbook 'taki bağlantılara erişmek için kullanılır. Python 3 runbook 'ları Şu anda önizleme aşamasındadır.

| İşlev | Açıklama |
|:---|:---|
| `automationassets.get_automation_connection` | Bir bağlantı alır. Bağlantının özelliklerine sahip bir sözlük döndürür. |

> [!NOTE]
> `automationassets`Varlık işlevlerine erişmek Için Python runbook 'unun en üstündeki modülünü içeri aktarmanız gerekir.

## <a name="create-a-new-connection"></a>Yeni bağlantı oluşturma

### <a name="create-a-new-connection-with-the-azure-portal"></a>Azure portal yeni bir bağlantı oluşturun

Azure portal yeni bir bağlantı oluşturmak için:

1. Otomasyon hesabınızdan, **paylaşılan kaynaklar** altındaki **Bağlantılar** ' a tıklayın.
2. Bağlantılar sayfasında **+ bağlantı ekle** ' ye tıklayın.
4. Yeni bağlantı bölmesindeki **tür** alanında, oluşturulacak bağlantı türünü seçin. Seçenekleriniz `Azure` , ve ' dir `AzureServicePrincipal` `AzureClassicCertificate` . 
5. Form, seçtiğiniz bağlantı türü için özellikleri gösterir. Formu doldurun ve yeni bağlantıyı kaydetmek için **Oluştur** ' a tıklayın.

### <a name="create-a-new-connection-with-windows-powershell"></a>Windows PowerShell ile yeni bir bağlantı oluşturma

Cmdlet 'ini kullanarak Windows PowerShell ile yeni bir bağlantı oluşturun `New-AzAutomationConnection` . Bu cmdlet, `ConnectionFieldValues` bağlantı türü tarafından tanımlanan her bir özellik için değerleri tanımlayan bir Hashtable bekleyen bir parametreye sahiptir.

Yeni bir bağlantı varlığı oluşturmak için portaldan farklı çalıştır hesabı oluşturmaya alternatif olarak aşağıdaki örnek komutları kullanabilirsiniz.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Otomasyon hesabınızı oluşturduğunuzda, `AzureServicePrincipal` bağlantı varlığını oluşturmak için bağlantı türü ile birlikte, varsayılan olarak çeşitli genel modüller vardır `AzureRunAsConnection` . Farklı bir kimlik doğrulama yöntemiyle bir hizmete veya uygulamaya bağlanmak için yeni bir bağlantı varlığı oluşturmaya çalışırsanız, bağlantı türü Otomasyon hesabınızda zaten tanımlanmadığı için işlem başarısız olur. Özel bir modül için kendi bağlantı türünü oluşturma hakkında daha fazla bilgi için bkz. [bağlantı türü ekleme](#add-a-connection-type).

## <a name="add-a-connection-type"></a>Bağlantı türü ekleme

Runbook veya DSC yapılandırmanız bir dış hizmete bağlanıyorsa, bir [özel modülde](shared-resources/modules.md#custom-modules) tümleştirme modülü olarak adlandırılan bir bağlantı türü tanımlamanız gerekir. Bu modül, bağlantı türü özelliklerini belirten bir meta veri dosyası içerir ve sıkıştırılmış **. zip** dosyanızın modül klasöründe bulunan **&lt; ModuleName &gt;-Automation.js'** de adlandırılır. Bu dosya, modülün temsil ettiği sisteme veya hizmete bağlanmak için gereken bir bağlantının alanlarını içerir. Bu dosyayı kullanarak, bağlantı türü için alan adlarını, veri türlerini, şifreleme durumunu ve isteğe bağlı durumu ayarlayabilirsiniz. 

Aşağıdaki örnek, adlı bir özel bağlantı türü için Kullanıcı adı ve parola özelliklerini tanımlayan **. JSON** dosya biçimindeki bir şablondur `MyModuleConnection` :

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Runbook veya DSC yapılandırmasında bağlantı alın

İç cmdlet ile runbook veya DSC yapılandırmasında bir bağlantı alın `Get-AutomationConnection` . Bu cmdlet, `Get-AzAutomationConnection` bağlantı hakkında bilgi yerine bağlantı değerlerini aldığı için cmdlet üzerinden tercih edilir.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aşağıdaki örnek, runbook 'inizdeki Azure Resource Manager kaynaklarla kimlik doğrulamak için farklı çalıştır hesabını nasıl kullanacağınızı gösterir. Sertifika tabanlı hizmet sorumlusuna başvuran farklı çalıştır hesabını temsil eden bir bağlantı varlığı kullanır.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

# <a name="python"></a>[Python](#tab/python2)

Aşağıdaki örnek, bir Python 2 ve 3 runbook 'ta farklı çalıştır bağlantısını kullanarak nasıl kimlik doğrulaması yapılacağını gösterir.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resource manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

---

### <a name="graphical-runbook-examples"></a>Grafik runbook örnekleri

`Get-AutomationConnection`Bir grafik runbook 'unu iç cmdlet için bir etkinlik ekleyebilirsiniz. Grafik düzenleyicinin Kitaplık bölmesinde bağlantıya sağ tıklayın ve **tuvale Ekle**' yi seçin.

![tuvale Ekle](media/automation-connections/connection-add-canvas.png)

Aşağıdaki görüntüde bir grafik runbook 'unda bağlantı nesnesi kullanma örneği gösterilmektedir. Bu örnek `Constant value` `Get RunAs Connection` , kimlik doğrulaması için bir bağlantı nesnesi kullanan etkinliğin veri kümesini kullanır. Parametre kümesi tek bir nesne beklediği için burada bir işlem [hattı bağlantısı](automation-graphical-authoring-intro.md#use-links-for-workflow) kullanılır `ServicePrincipalCertificate` .

![bağlantıları al](media/automation-connections/automation-get-connection-object.png)

## <a name="next-steps"></a>Sonraki adımlar

* Bağlantılara erişmek için kullanılan cmdlet 'ler hakkında daha fazla bilgi edinmek için bkz. [Azure Automation 'da modülleri yönetme](shared-resources/modules.md).
* Runbook 'lar hakkında genel bilgi için bkz. [Azure Otomasyonu 'Nda runbook yürütme](automation-runbook-execution.md).
* DSC yapılandırmalarının ayrıntıları için bkz. [durum yapılandırmasına genel bakış](automation-dsc-overview.md).
