---
title: Azure Otomasyonu PowerShell runbook’unda Azure Resource Manager şablonu dağıtma
description: Bu makalede, bir PowerShell runbook 'tan Azure Storage 'da depolanan bir Azure Resource Manager şablonunun nasıl dağıtılacağı açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
keywords: PowerShell, runbook, JSON, Azure Otomasyonu
ms.openlocfilehash: 20dbf9f9bbf97ed0c24ea3a525c56c7cde2db428
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834849"
---
# <a name="deploy-an-azure-resource-manager-template-in-a-powershell-runbook"></a>PowerShell runbook 'unda Azure Resource Manager şablonu dağıtma

Azure Kaynak [yönetimi şablonu](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)kullanarak Azure kaynağı dağıtan bir [Azure Otomasyonu PowerShell runbook 'u](./learn/automation-tutorial-runbook-textual-powershell.md) yazabilirsiniz. Şablonlar, Azure kaynaklarınızın dağıtımını otomatik hale getirmek için Azure Otomasyonu 'nu kullanmanıza olanak sağlar. Kaynak Yöneticisi şablonlarınızı Azure depolama gibi merkezi ve güvenli bir konumda tutabilirsiniz.

Bu makalede, yeni bir Azure depolama hesabı dağıtmak için [Azure Storage](../storage/common/storage-introduction.md) 'da depolanan kaynak yöneticisi şablonunu kullanan bir PowerShell runbook 'u oluşturacağız.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Henüz bir aboneliğiniz yoksa [MSDN abone avantajlarınızı etkinleştirebilir](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ya da [ücretsiz hesap için kaydolabilirsiniz](https://azure.microsoft.com/free/).
* Runbook’u tutacak ve Azure kaynaklarında kimlik doğrulamasını yapacak bir [Automation hesabı](./automation-security-overview.md). Bu hesabın sanal makineyi başlatma ve durdurma izni olmalıdır.
* Kaynak Yöneticisi şablonunun kaydedileceği [Azure depolama hesabı](../storage/common/storage-account-create.md) .
* Azure PowerShell yerel bir makineye yüklendi. Azure PowerShell alma hakkında bilgi için bkz. [Azure PowerShell modülünü yüklemek](/powershell/azure/install-az-ps) .

## <a name="create-the-resource-manager-template"></a>Resource Manager şablonu oluşturma

Bu örnekte, yeni bir Azure depolama hesabı dağıtan bir Kaynak Yöneticisi şablonu kullanıyoruz.

Bir metin düzenleyicisinde, aşağıdaki metni kopyalayın:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Dosyayı **TemplateTest.js** olarak yerel olarak kaydedin.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Kaynak Yöneticisi şablonunu Azure Storage 'a kaydetme

Artık PowerShell 'i kullanarak bir Azure depolama dosya paylaşımının oluşturulmasını ve dosyadaki **TemplateTest.js** karşıya yüklenmesini istiyoruz. Dosya paylaşımının nasıl oluşturulacağı ve Azure portal bir dosyanın nasıl yükleneceği hakkında yönergeler için bkz. [Windows 'Da Azure dosya depolama ile çalışmaya başlama](../storage/files/storage-dotnet-how-to-use-files.md).

Yerel makinenizde PowerShell 'i başlatın ve bir dosya paylaşma oluşturup Kaynak Yöneticisi şablonunu bu dosya paylaşımında karşıya yüklemek için aşağıdaki komutları çalıştırın.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>PowerShell runbook betiğini oluşturma

Şimdi Azure Storage 'dan dosya **TemplateTest.js** alan ve yeni bir Azure depolama hesabı oluşturmak için şablonu dağıtan bir PowerShell betiği oluşturacağız.

Bir metin düzenleyicisinde şu metni yapıştırın:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)

# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
```

Dosyayı **DeployTemplate.ps1** olarak yerel olarak kaydedin.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Runbook 'u içeri aktarın ve Azure Otomasyonu hesabınıza yayımlayın

Artık PowerShell 'i kullanarak runbook 'u Azure Otomasyonu hesabınıza içeri aktarıp runbook 'u yayımlamanız gerekir. Azure portal runbook 'u içeri aktarma ve yayımlama hakkında daha fazla bilgi için bkz. [Azure Otomasyonu 'nda runbook 'Ları yönetme](manage-runbooks.md).

Otomasyon hesabınıza bir PowerShell runbook 'u olarak **DeployTemplate.ps1** aktarmak Için aşağıdaki PowerShell komutlarını çalıştırın:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Runbook’u başlatma

Şimdi [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet 'ini çağırarak runbook 'u başlatacağız. Azure portal runbook 'u başlatma hakkında daha fazla bilgi için bkz. [Azure Otomasyonu 'nda runbook başlatma](./start-runbooks.md).

PowerShell konsolunda aşağıdaki komutları çalıştırın:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json'
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Runbook çalıştıktan sonra, iş nesnesinin özellik değerini alarak durumunu kontrol edebilirsiniz `$job.Status` .

Runbook Kaynak Yöneticisi şablonunu alır ve yeni bir Azure depolama hesabı dağıtmak için onu kullanır. Aşağıdaki komutu çalıştırarak yeni depolama hesabının oluşturulduğunu görebilirsiniz:

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>Sonraki adımlar

* Kaynak Yöneticisi şablonları hakkında daha fazla bilgi için bkz. [Azure Resource Manager genel bakış](../azure-resource-manager/management/overview.md).
* Azure depolama 'yı kullanmaya başlamak için bkz. [Azure depolama 'Ya giriş](../storage/common/storage-introduction.md).
* Diğer yararlı Azure Otomasyonu runbook 'larını bulmak için bkz. [Azure Otomasyonu 'nda runbook 'ları ve modülleri kullanma](automation-runbook-gallery.md).
* PowerShell cmdlet başvurusu için bkz. [az. Automation](/powershell/module/az.automation#automation).
