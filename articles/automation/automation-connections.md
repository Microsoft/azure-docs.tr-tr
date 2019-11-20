---
title: Azure Otomasyonu 'nda bağlantı varlıkları
description: Azure Otomasyonu 'ndaki bağlantı varlıkları, bir runbook veya DSC yapılandırmasından bir dış hizmete veya uygulamaya bağlanmak için gereken bilgileri içerir. Bu makalede, bağlantıların ayrıntıları ve hem metin hem de grafik yazarken bunlarla nasıl çalışılacağı açıklanmaktadır.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 01/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 90b2234607ad120c43e241fe4ae5222fe285803e
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001653"
---
# <a name="connection-assets-in-azure-automation"></a>Azure Otomasyonu 'nda bağlantı varlıkları

Bir Otomasyon bağlantı varlığı, bir runbook veya DSC yapılandırmasından bir dış hizmete veya uygulamaya bağlanmak için gereken bilgileri içerir. Bu, URL veya bağlantı noktası gibi bağlantı bilgilerine ek olarak Kullanıcı adı ve parola gibi kimlik doğrulaması için gereken bilgileri içerebilir. Bir bağlantının değeri, birden çok değişken oluşturma aksine, tek bir varlık içindeki belirli bir uygulamaya bağlanmak için tüm özelliklerin tutulmasını sağlar. Kullanıcı bir bağlantının değerlerini tek bir yerde düzenleyebilir ve bir bağlantı adını tek bir parametrede runbook veya DSC yapılandırmasına geçirebilirsiniz. Bir bağlantının özelliklerine runbook veya DSC yapılandırmasından **Get-AutomationConnection** etkinliğiyle erişilebilir.

Bir bağlantı oluşturduğunuzda bir *bağlantı türü*belirtmeniz gerekir. Bağlantı türü bir özellikler kümesini tanımlayan bir şablondur. Bağlantı, bağlantı türünde tanımlanan her bir özellik için değerleri tanımlar. Tümleştirme modülü bir bağlantı türü içeriyorsa ve otomasyon hesabınıza içeri aktarıldıysa, bağlantı türleri tümleştirme modüllerinde Azure Otomasyonu 'na eklenir veya [Azure Otomasyonu API 'si](/previous-versions/azure/reference/mt163818(v=azure.100)) ile oluşturulur. Aksi takdirde, bir Otomasyon bağlantı türü belirtmek için bir meta veri dosyası oluşturmanız gerekir.  Bu sorun hakkında daha fazla bilgi için bkz. [tümleştirme modülleri](automation-integration-modules.md).

>[!NOTE]
>Azure Otomasyonu 'nda güvenli varlıklar, kimlik bilgileri, sertifikalar, bağlantılar ve şifrelenmiş değişkenler içerir. Bu varlıklar, her Otomasyon hesabı için oluşturulan benzersiz bir anahtar kullanılarak Azure Otomasyonu 'nda şifrelenir ve depolanır. Bu anahtar, sistem tarafından yönetilen bir Key Vault depolanır. Güvenli bir varlık depolamadan önce, anahtar Key Vault yüklenir ve ardından varlığı şifrelemek için kullanılır. Bu işlem Azure Otomasyonu tarafından yönetiliyor.

## <a name="connection-types"></a>Bağlantı türleri

Azure Otomasyonu 'nda kullanılabilen üç tür yerleşik bağlantı vardır:

* **Azure** -Bu bağlantı, klasik kaynakları yönetmek için kullanılabilir.
* **AzureClassicCertificate** -Bu bağlantı **AzureClassicRunAs** hesabı tarafından kullanılır.
* **AzureServicePrincipal** -Bu bağlantı **Azurerunas** hesabı tarafından kullanılır.

Çoğu durumda, bir [runas hesabı](manage-runas-account.md)oluşturduğunuzda oluşturulduğu sırada bir bağlantı kaynağı oluşturmanız gerekmez.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell cmdlet 'Leri

Aşağıdaki tablodaki cmdlet 'ler Windows PowerShell ile otomasyon bağlantıları oluşturmak ve yönetmek için kullanılır. Bunlar, Automation runbook 'larda ve DSC yapılandırmalarında kullanılabilecek [Azure PowerShell modülünün](/powershell/azure/overview) bir parçası olarak gönderilir.

|Cmdlet|Açıklama|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Bir bağlantı alır. Bağlantı alanlarının değerlerine sahip bir karma tablo içerir.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Yeni bir bağlantı oluşturur.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Var olan bir bağlantıyı kaldırın.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Mevcut bir bağlantı için belirli bir alanın değerini ayarlar.|

## <a name="activities"></a>Olaylar

Aşağıdaki tablodaki etkinlikler bir runbook veya DSC yapılandırmasındaki bağlantılara erişmek için kullanılır.

|Olaylar|Açıklama|
|---|---|
|[-AutomationConnection 'ı al](/powershell/module/servicemanagement/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Kullanmak için bir bağlantı alır. Bağlantının özelliklerine sahip bir karma tablo döndürür.|

>[!NOTE]
>Bu, runbook 'lar ya da DSC yapılandırmalarının ve tasarım zamanında bağlantı varlıklarının bağımlılıklarını karmaşık hale getirebileceğinden, **Get-AutomationConnection** 'ın – Name parametresiyle değişkenler kullanmaktan kaçının.


## <a name="python2-functions"></a>Python2 işlevleri
Aşağıdaki tablodaki işlev, bir Python2 runbook 'daki bağlantılara erişmek için kullanılır.

| İşlev | Açıklama |
|:---|:---|
| automationvarlıklarının. Get _automation_connection | Bir bağlantı alır. Bağlantının özelliklerine sahip bir sözlük döndürür. |

> [!NOTE]
> Varlık işlevlerine erişebilmek için Python runbook 'unun en üstündeki "automationvarlıklar" modülünü içeri aktarmanız gerekir.

## <a name="creating-a-new-connection"></a>Yeni bağlantı oluşturma

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Azure portal ile yeni bir bağlantı oluşturmak için

1. Otomasyon hesabınızdan **varlıklar bölümüne tıklayarak** **varlıklar** dikey penceresini açın.
2. **Bağlantılar dikey penceresini** açmak için **Bağlantılar** bölümüne tıklayın.
3. Dikey pencerenin üst kısmındaki **bağlantı ekle** ' ye tıklayın.
4. **Tür** açılır listesinde, oluşturmak istediğiniz bağlantı türünü seçin. Form, bu belirli tür için özellikler sunacaktır.
5. Formu doldurun ve yeni bağlantıyı kaydetmek için **Oluştur** ' a tıklayın.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Windows PowerShell ile yeni bir bağlantı oluşturmak için

[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) cmdlet 'Ini kullanarak Windows PowerShell ile yeni bir bağlantı oluşturun. Bu cmdlet, bağlantı türü tarafından tanımlanan her bir özellik için değerleri tanımlayan bir [karma tablo](https://technet.microsoft.com/library/hh847780.aspx) bekleyen **connectionfieldvalues** adlı bir parametreye sahiptir.

Hizmet sorumlusunu kullanarak runbook 'ların kimliğini doğrulamak için Otomasyon [Farklı Çalıştır hesabı](automation-sec-configure-azure-runas-account.md) hakkında bilginiz varsa, portaldan farklı çalıştır hesabı oluşturmak için alternatif olarak belirtilen PowerShell betiği, ' ı kullanarak yeni bir bağlantı varlığı oluşturur. Aşağıdaki örnek komutlar.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Otomasyon hesabınızı oluştururken bağlantı varlığı oluşturmak için komut dosyasını kullanabilirsiniz. Bu, **AzureServicePrincipal** bağlantı türü ile birlikte varsayılan olarak çeşitli genel modüller içerir.  **AzureRunAsConnection** bağlantı varlığı.  Farklı bir kimlik doğrulama yöntemiyle bir hizmet veya uygulamaya bağlanmak üzere yeni bir bağlantı varlığı oluşturmaya çalıştığınızda, bağlantı türü Otomasyon hesabınızda zaten tanımlanmadığı için bu işlem başarısız olur.  [PowerShell Galerisi](https://www.powershellgallery.com)özel veya modülünüz için kendi bağlantı türü oluşturma hakkında daha fazla bilgi için bkz. [tümleştirme modülleri](automation-integration-modules.md)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Runbook veya DSC yapılandırmasında bağlantı kullanma

**Get-AutomationConnection** cmdlet 'ini kullanarak RUNBOOK veya DSC yapılandırmasında bir bağlantı alırsınız.  [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) etkinliğini kullanamazsınız.  Bu etkinlik, bağlantıdaki farklı alanların değerlerini alır ve daha sonra runbook veya DSC yapılandırmasındaki uygun komutlarla kullanılabilecek bir [karma tablo](https://go.microsoft.com/fwlink/?LinkID=324844) olarak döndürür.

### <a name="textual-runbook-sample"></a>Metinsel runbook örneği

Aşağıdaki örnek komutlarda, runbook 'larınızda Azure Resource Manager kaynaklarla kimlik doğrulamak için daha önce bahsedilen farklı çalıştır hesabının nasıl kullanılacağı gösterilmektedir.  Kimlik bilgilerine değil, sertifika tabanlı hizmet sorumlusuna başvuran farklı çalıştır hesabını temsil eden bağlantı varlığını kullanır.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-azurermaccount** artık **Connect-azurermaccount**için bir diğer addır. Kitaplık öğelerinizi ararken, **Connect-AzureRMAccount**' u görmüyorsanız **Add-azurermaccount**komutunu kullanabilir veya bir Otomasyon hesabınızda modüllerinizi güncelleştirebilirsiniz.

### <a name="graphical-runbook-samples"></a>Grafik runbook örnekleri

Grafik düzenleyicinin Kitaplık bölmesinde bağlantıya sağ tıklayıp **tuvale Ekle**' yi seçerek bir grafik runbook 'una **Get-AutomationConnection** etkinliği eklersiniz.

![tuvale Ekle](media/automation-connections/connection-add-canvas.png)

Aşağıdaki görüntüde bir grafik runbook 'unda bağlantı kullanmanın bir örneği gösterilmektedir.  Bu, bir metinsel runbook ile farklı çalıştır hesabını kullanarak kimlik doğrulaması yapmak için yukarıda gösterilen örnektir.  Bu örnek, kimlik doğrulaması için bir bağlantı nesnesi kullanan **runas bağlantısını al** etkinliğinin **sabit değer** veri kümesini kullanır.  ServicePrincipalCertificate parametre kümesi tek bir nesne beklediği için burada bir işlem [hattı bağlantısı](automation-graphical-authoring-intro.md#links-and-workflow) kullanılır.

![bağlantıları al](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Python2 runbook örneği
Aşağıdaki örnek, bir Python2 runbook 'unda farklı çalıştır bağlantısını kullanarak nasıl kimlik doğrulaması yapılacağını gösterir.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resoruce manager """
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

## <a name="next-steps"></a>Sonraki adımlar

- Runbook 'larınızda Logic Flow 'un nasıl yönlendirileceğini ve kontrol olduğunu anlamak için [grafik yazarken bağlantıları](automation-graphical-authoring-intro.md#links-and-workflow) gözden geçirin.

- Azure Otomasyonu 'nun PowerShell modüllerinin kullanımı ve Azure Otomasyonu 'nda tümleştirme modülleri olarak çalışmak üzere kendi PowerShell modüllerinizi oluşturmak için en iyi uygulamalar hakkında daha fazla bilgi edinmek için bkz. [tümleştirme modülleri](automation-integration-modules.md).

