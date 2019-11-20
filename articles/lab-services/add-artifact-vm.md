---
title: Azure DevTest Labs bir sanal makineye yapıt ekleme | Microsoft Docs
description: Azure DevTest Labs bir laboratuvarda bir sanal makineye yapıt ekleme hakkında bilgi edinin
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 27fec279582d845972b87ac635c87c16c239924e
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73901321"
---
# <a name="add-an-artifact-to-a-vm"></a>VM’ye yapıt ekleme
Bir VM oluştururken, var olan yapıtları buna ekleyebilirsiniz. Bu yapıtlar, [ortak DevTest Labs git deposundan](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) veya kendi git deponuzdan olabilir. Bu makalede, Azure portal yapılarını ve Azure PowerShell kullanarak nasıl ekleyeceğiniz gösterilmektedir. 

Azure DevTest Labs *yapıtlar* , Windows PowerShell betikleri çalıştırma, Bash komutlarını çalıştırma ve yazılım yükleme gıbı, VM sağlandığında gerçekleştirilen *eylemleri* belirtmenize olanak tanır. Yapıt *parametreleri* , belirli senaryonuz için yapıtı özelleştirmenize olanak sağlar.

Özel yapıtlar oluşturma hakkında bilgi edinmek için bkz. [özel yapıtlar oluşturma](devtest-lab-artifact-author.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Azure portalı kullanma 
1. [Azure portalında](https://go.microsoft.com/fwlink/p/?LinkID=525040) oturum açın.
1. **Tüm hizmetler**' i seçin ve ardından listeden **DevTest Labs** ' i seçin.
1. Laboratuvarlar listesinden, çalışmak istediğiniz VM 'yi içeren Laboratuvarı seçin.  
1. **Sanal makinelerimi**seçin.
1. İstediğiniz VM 'yi seçin.
1. **Yapıtları Yönet**' i seçin. 
1. **Yapıtları Uygula**' yı seçin.
1. **Yapıtları Uygula** BÖLMESINDE, VM 'ye eklemek istediğiniz yapıtı seçin.
1. **Yapıt Ekle** bölmesinde gerekli parametre değerlerini ve ihtiyacınız olan tüm isteğe bağlı parametreleri girin.  
1. Yapıtı eklemek için **Ekle** ' yi seçin ve **yapıtları Uygula** bölmesine dönün.
1. VM 'niz için gereken yapıtları eklemeye devam edin.
1. Yapılarınızı ekledikten sonra [yapıtların çalıştırıldığı sırayı değiştirebilirsiniz](#change-the-order-in-which-artifacts-are-run). Ayrıca, [bir yapıtı görüntülemek veya değiştirmek](#view-or-modify-an-artifact)için geri dönebilirsiniz.
1. Yapıtları eklemeyi tamamladığınızda **Uygula** ' yı seçin.

### <a name="change-the-order-in-which-artifacts-are-run"></a>Yapıtların çalıştırıldığı sırayı değiştirme
Varsayılan olarak, yapıtların eylemleri VM 'ye eklendikleri sırada yürütülür. Aşağıdaki adımlarda yapıtların çalıştırıldığı sıranın nasıl değiştirileceği gösterilmektedir.

1. **Yapıtları Uygula** bölmesinin en üstünde, VM 'ye eklenmiş yapıların sayısını belirten bağlantıyı seçin.
   
    ![VM 'ye eklenen yapıt sayısı](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **Seçilen yapılar** bölmesinde, yapıtları istediğiniz sırada sürükleyin ve bırakın. Yapıtı sürüklerken sorun yaşıyorsanız, yapıtın sol tarafından sürüklediğinizden emin olun. 
1. Tamamladığınızda **Tamam**’ı seçin.  

### <a name="view-or-modify-an-artifact"></a>Yapıtı görüntüleme veya değiştirme
Aşağıdaki adımlarda, bir yapının parametrelerinin nasıl görüntüleneceği veya değiştirileceği gösterilmektedir:

1. **Yapıtları Uygula** bölmesinin en üstünde, VM 'ye eklenmiş yapıların sayısını belirten bağlantıyı seçin.
   
    ![VM 'ye eklenen yapıt sayısı](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. **Seçilen yapılar** bölmesinde, görüntülemek veya düzenlemek istediğiniz yapıtı seçin.  
1. **Yapıt Ekle** bölmesinde, gerekli değişiklikleri yapın ve **Tamam** ' ı seçerek **yapıt Ekle** bölmesini kapatın.
1. **Seçilen yapılar** bölmesini kapatmak için **Tamam ' ı** seçin.

## <a name="use-powershell"></a>PowerShell kullanma
Aşağıdaki betik belirtilen yapıyı belirtilen VM 'ye uygular. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) komutu, işlemi gerçekleştiren bir işlemdir.  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>Sonraki adımlar
Yapıtlar hakkında aşağıdaki makalelere bakın:

- [Laboratuvarınız için zorunlu yapıtlar belirtin](devtest-lab-mandatory-artifacts.md)
- [Özel yapıtlar oluşturma](devtest-lab-artifact-author.md)
- [Laboratuvara yapıt deposu ekleme](devtest-lab-artifact-author.md)
- [Yapıt hatalarını tanılama](devtest-lab-troubleshoot-artifact-failure.md)
