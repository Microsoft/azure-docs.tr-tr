---
title: Bir Azure Otomasyonu runbook bir e-posta Gönder
description: Bir runbook içindeki e göndermek için SendGrid kullanmayı öğrenin.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 07/15/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: ce05aadb53cc3ad24ed65ea139594010e1aef047
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234986"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Öğretici: Bir Azure Otomasyonu runbook bir e-posta Gönder

Bir runbook ile gelen e-posta gönderebilir [SendGrid](https://sendgrid.com/solutions) PowerShell kullanarak. Bu öğreticide depolanan bir API anahtarı kullanarak bir e-posta gönderen bir yeniden kullanılabilir runbook'unun nasıl oluşturulacağını gösterilecek [Azure KeyVault](/azure/key-vault/).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Bir Azure anahtar kasası oluşturma
> * SendGrid API anahtarınızı Keyvault'ta Store
> * API anahtarınızı alır ve bir e-posta gönderen bir runbook oluşturma

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gereklidir:

* Azure aboneliği: Henüz bir aboneliğiniz yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ya da [ücretsiz hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) için kaydolabilirsiniz.
* [SendGrid hesabı oluşturma](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Otomasyon hesabı](automation-offering-get-started.md) ile **Az** modülleri ve [farklı çalıştır bağlantısı](automation-create-runas-account.md), depolamak ve runbook yürütün.

## <a name="create-an-azure-keyvault"></a>Bir Azure anahtar kasası oluşturma

Aşağıdaki PowerShell betiğini kullanarak bir Azure anahtar kasası oluşturabilirsiniz. Değişken değerleri ortamınıza özgü değerlerle değiştirin. Ekli Azure Cloud Shell'i kullanmak <kbd>deneyin</kbd> kod bloğunun sağ üst köşesinde bulunan düğmesini. Ayrıca kopyalayabilir ve yerel olarak, varsa, kodu çalıştırmak [Azure PowerShell Modülü](/powershell/azure/install-az-ps) yerel makinenizde yüklü.

> [!NOTE]
> API anahtarınızı almak için bulunan adımları kullanın. [SendGrid API anahtarınızı bulma](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the KeyVault to the Automation RunAs account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Bir Azure anahtar kasası oluşturma ve bir gizli diğer yolları için bkz. [KeyVault hızlı Başlangıçlar](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Gerekli modülleri içeri aktarmak için Otomasyon hesabı

Bir runbook içindeki Azure KeyVault kullanmak için Otomasyon hesabınızda aşağıdaki modüller gerekir:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile).
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault).

Tıklayın <kbd>Azure Otomasyonu Dağıt</kbd> Azure Otomasyonu sekmesinde yükleme seçenekleri altında. Bu eylem, Azure portalını açar. İçeri aktarma sayfasında Otomasyon hesabınızı seçin ve <kbd>Tamam</kbd>.

Gerekli modülleri eklemek için ek yöntemleri için bkz [modülleri Al](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>E-posta göndermek için runbook oluşturma

Bir anahtar kasası oluşturulur ve SendGrid API anahtarınızı depolanan sonra API anahtarı almak ve bir e-posta runbook oluşturmak için zaman olur.

Bu runbook AzureRunAsConnection kullanan [Run As hesabı](automation-create-runas-account.md) Azure KeyVault gizli diziyi almak için Azure ile kimlik doğrulaması.

Adlı bir runbook oluşturmak için bu örneği kullanmak **gönderme GridMailMessage**. PowerShell komut dosyasını değiştirin ve farklı senaryolar için yeniden.

1. Azure Otomasyon hesabınıza gidin.
2. Altında **süreç otomasyonu**seçin **runbook'ları**.
3. Runbook'ların listenin en üstünde seçin **+ bir runbook Oluştur**.
4. Üzerinde **Runbook Ekle** want **gönderme GridMailMessage** runbook adı. Runbook türü için **PowerShell**. Ardından **Oluştur**’u seçin.
   ![Runbook oluşturma](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbook oluşturulur ve **PowerShell Runbook'unu Düzenle** sayfası açılır.
   ![Runbook'u düzenleme](./media/automation-send-email/automation-send-email-edit.png)
6. Aşağıdaki PowerShell örneği içine kopyalayın **Düzenle** sayfası. Emin `$VaultName` , KeyVault oluştururken belirttiğiniz adı.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Seçin **Yayımla** kaydedin ve runbook'u yayımlayamadı.

Runbook başarılı bir şekilde uyarak çalıştığını doğrulamak için altındaki adımları izleyebilirsiniz [bir runbook'u Test](manage-runbooks.md#test-a-runbook) veya [runbook başlatma](start-runbooks.md).
Test e-postanızı başlangıçta görmüyorsanız, denetle, **Önemsiz** ve **istenmeyen posta** klasörleri.

## <a name="clean-up"></a>Temizleme

İhtiyacınız kalmadıysa runbook'u silebilirsiniz. Bunu yapmak için runbook listesinden runbook'u seçip **Sil**'e tıklayın.

Anahtar kasasını kullanarak silme [Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps) cmdlet'i.

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Sonraki adımlar

* Oluşturma veya, bir runbook başlatma sorunları için bkz: [runbook'ları ile hatalarında sorun giderme](./troubleshoot/runbooks.md).
* Modüller Otomasyon hesabınızı güncelleştirmek için bkz: [Azure automation'da Azure PowerShell modüllerini güncelleştirme](automation-update-azure-modules.md)].
* Runbook yürütmesi izlemek için bkz: [Otomasyon iş durumunu ve iş akışları için Azure İzleyici günlüklerine iletmek](automation-manage-send-joblogs-log-analytics.md).
* Bir uyarı kullanarak bir runbook'u tetiklemek için bkz: [Azure Otomasyonu runbook'u tetiklemek için bir uyarı kullanın](automation-create-alert-triggered-runbook.md).
