---
title: Azure Güvenlik Duvarı Premium önizlemesine geçiş
description: Azure Güvenlik Duvarı standartlarından Azure Güvenlik Duvarı Premium önizlemesine geçiş yapmayı öğrenin.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: ffdcad33568af9955dbdf5aafb1b6ffe4a51681d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100549853"
---
# <a name="migrate-to-azure-firewall-premium-preview"></a>Azure Güvenlik Duvarı Premium önizlemesine geçiş

Azure Güvenlik Duvarı standardını Azure Güvenlik Duvarı Premium önizlemesine geçirebilmeniz için yeni Premium özelliklerden yararlanabilirsiniz. Azure Güvenlik Duvarı Premium Önizleme özellikleri hakkında daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Premium Önizleme özellikleri](premium-features.md).

Aşağıdaki iki örnek nasıl yapılacağını göstermektedir:
- Azure PowerShell kullanarak mevcut bir standart ilkeyi geçirme
- Mevcut bir standart güvenlik duvarını (klasik kurallarla birlikte) bir Premium ilkeyle Azure Güvenlik Duvarı Premium 'a geçirin.

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Azure PowerShell kullanarak mevcut bir ilkeyi geçirme

`Transform-Policy.ps1` , varolan bir standart ilkeden yeni bir Premium ilkesi oluşturan Azure PowerShell bir betiktir.

Standart güvenlik duvarı ilke KIMLIĞI verildiğinde, betik onu Premium bir Azure Güvenlik Duvarı ilkesine dönüştürür. Betik ilk olarak Azure hesabınıza bağlanır, ilkeyi çeker, çeşitli parametreleri dönüştürür/ekler ve ardından yeni bir Premium ilkesi yükler. Yeni Premium ilkesi olarak adlandırılmıştır `<previous_policy_name>_premium` .

Kullanım örneği:

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> Betik, tehdit zekası ayarlarını geçirmez. Devam etmeden önce bu ayarları ve el ile geçişi yapmanız gerekir.

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    # #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy 
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -ErrorAction SilentlyContinue
    if (($null -eq $networkModule) -or ($networkModule.Version -lt 4.5)){
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy
```

## <a name="migrate-an-existing-standard-firewall-using-the-azure-portal"></a>Azure portal kullanarak mevcut bir standart güvenlik duvarını geçirme

Bu örnekte, bir Premium ilkeyle standart güvenlik duvarının (Klasik kurallar) Azure Güvenlik Duvarı Premium 'a geçirilmesi için Azure portal nasıl kullanılacağı gösterilmektedir.

1. Azure portal, standart güvenlik duvarınızı seçin. **Genel bakış** sayfasında **güvenlik duvarı ilkesine geçir**' i seçin.

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="Güvenlik Duvarı ilkesine geçiş":::

1. **Güvenlik duvarına geçiş ilkesi** sayfasında, **gözden geçir + oluştur**' u seçin.
1. **Oluştur**’u seçin.

   Dağıtımın tamamlanabilmesi birkaç dakika sürer.
1. `Transform-Policy.ps1`Bu yeni standart ilkeyi bir Premium ilkeye dönüştürmek için [Azure PowerShell betiğini](#migrate-an-existing-policy-using-azure-powershell) kullanın.
1. Portalda standart güvenlik duvarı kaynağını seçin. 
1. **Otomasyon** altında **şablonu dışarı aktar**' ı seçin. Bu tarayıcı sekmesini açık tutun. Daha sonra geri döneceksiniz.
   > [!TIP]
   > Şablonu kaybetmemenizi sağlamak için tarayıcı sekmesinin kapalı veya yenilenmiş olması durumunda dosyayı indirip kaydedin.
1. Yeni bir tarayıcı sekmesi açın, Azure portal gidin ve güvenlik duvarınızı içeren kaynak grubunu açın.
1. Mevcut standart güvenlik duvarı örneğini silin.

   Bu işlemin tamamlanması birkaç dakika sürer.

1. Tarayıcı sekmesine, verilecek şablonla dönün.
1. **Dağıt**' ı seçin, ardından **özel dağıtım** sayfasında **Şablonu Düzenle**' yi seçin.
1. Şablon metnini düzenleyin:
   
   1. Kaynak altında, `Microsoft.Network/azureFirewalls` altında, `Properties` `sku` `tier` "standart" iken "Premium" olarak değiştirin.
   1. Şablon altında `Parameters` `defaultValue` , için şunu değiştirin `firewallPolicies_FirewallPolicy_,<your policy name>_externalid` :
      
       `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>"`

      Yeni değer:

      `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>_premium"`
1. **Kaydet**’i seçin.
1. **Gözden geçir + Oluştur**’u seçin.
1. **Oluştur**’u seçin.

Dağıtım tamamlandığında, artık tüm yeni Azure Güvenlik Duvarı Premium önizleme özelliklerini yapılandırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Güvenlik Duvarı Premium özellikleri hakkında daha fazla bilgi edinin](premium-features.md)