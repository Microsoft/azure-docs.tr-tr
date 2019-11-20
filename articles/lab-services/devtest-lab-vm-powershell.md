---
title: Azure PowerShell ile DevTest Labs 'de sanal makine oluşturma | Microsoft Docs
description: Azure PowerShell ile sanal makineler oluşturmak ve yönetmek için Azure DevTest Labs nasıl kullanacağınızı öğrenin.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 1a6938bd541e316dbe9f333c670c382faab6ad21
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854259"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Azure PowerShell kullanarak DevTest Labs ile sanal makine oluşturma
Bu makalede Azure PowerShell kullanarak Azure DevTest Labs sanal makine oluşturma gösterilmektedir. Azure DevTest Labs bir laboratuvarda sanal makine oluşturulmasını otomatikleştirmek için PowerShell betikleri kullanabilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar
Başlamadan önce:

- Bu makaledeki betiği veya komutları test etmek için mevcut bir laboratuvarı kullanmak istemiyorsanız [bir laboratuvar oluşturun](devtest-lab-create-lab.md) . 
- [Azure PowerShell yükleyip](/powershell/azure/install-az-ps?view=azps-1.7.0) Azure portal tümleştirilmiş Azure Cloud Shell kullanın. 

## <a name="powershell-script"></a>PowerShell betiği
Bu bölümdeki örnek komut dosyası [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) cmdlet 'ini kullanır.  Bu cmdlet, laboratuvarın kaynak kimliğini, gerçekleştirilecek eylemin adını (`createEnvironment`) ve bu eylemi gerçekleştirmek için gereken parametreleri alır. Parametreler, tüm sanal makine açıklaması özelliklerini içeren bir karma tablodur. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

Yukarıdaki betikteki sanal makine özellikleri, işletim sistemi olarak Windows Server 2016 DataCenter ile bir sanal makine oluşturmamıza olanak tanır. Her sanal makine türü için bu özellikler biraz farklı olacaktır. [Sanal makineyi tanımla](#define-virtual-machine) bölümü, bu betikte hangi özelliklerin kullanılacağını nasıl belirleyecağınızı gösterir.

Aşağıdaki komut, bir dosya adında kaydedilen betiği çalıştırmaya ilişkin bir örnek sağlar: Create-LabVirtualMachine. ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Sanal makineyi tanımla
Bu bölümde, oluşturmak istediğiniz bir sanal makine türüne özgü özelliklerin nasıl alınacağı gösterilmektedir. 

### <a name="use-azure-portal"></a>Azure portalı kullanma
Azure portal bir VM oluştururken Azure Resource Manager şablonu oluşturabilirsiniz. VM oluşturma işlemini gerçekleştirmeniz gerekmez. Yalnızca şablonu görene kadar adımları izleyin. Bu, önceden oluşturulmuş bir laboratuvar VM 'si yoksa, gerekli JSON açıklamasını almanın en iyi yoludur. 

1. [Azure portalına](https://portal.azure.com) gidin.
2. Sol gezinti menüsünde **tüm hizmetler** ' i seçin.
3. Hizmetler listesinden **DevTest Labs** 'i arayın ve seçin. 
4. **DevTest Labs** sayfasında laboratuvarınızı laboratuvar listesinden seçin.
5. Laboratuvarınızın giriş sayfasında, araç çubuğunda **+ Ekle** ' yi seçin. 
6. VM için bir **temel görüntü** seçin. 
7. **Gönder** düğmesinin üstündeki sayfanın altındaki **Otomasyon seçeneklerini** belirleyin. 
8. Sanal makineyi oluşturmak için **Azure Resource Manager şablonunu** görürsünüz. 
9. **Kaynaklar** bölümündeki JSON segmenti, daha önce seçtiğiniz görüntü türünün tanımına sahiptir. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

Bu örnekte, bir Azure pazar yeri görüntüsünün tanımını nasıl kullanacağınızı görürsünüz. Aynı şekilde özel bir görüntünün, bir formülün veya ortamın tanımını alabilirsiniz. Sanal makine için gereken yapıtları ekleyin ve gerekli tüm gelişmiş ayarları ayarlayın. Gerekli alanlar için değerleri ve tüm isteğe bağlı alanları **Otomasyon seçenekleri** düğmesini seçmeden önce sağlamaktan sonra.

### <a name="use-azure-rest-api"></a>Azure REST API kullanma
Aşağıdaki yordam REST API kullanarak bir görüntünün özelliklerini almak için adımlar sağlar: Bu adımlar yalnızca laboratuvardaki mevcut bir VM için çalışır. 

1. [Sanal makineler-liste](/rest/api/dtl/virtualmachines/list) sayfasına gidin, **deneyin** düğmesini seçin. 
2. **Azure aboneliğinizi** seçin.
3. **Laboratuvar için kaynak grubunu**girin.
4. **Laboratuvarın adını**girin. 
5. **Çalıştır**'ı seçin.
6. VM 'nin oluşturulduğu **görüntünün özelliklerini** görürsünüz. 

## <a name="set-expiration-date"></a>Sona erme tarihi ayarla
Eğitim, tanıtımlar ve denemeler gibi senaryolarda, sanal makineler oluşturmak ve bunları sabit bir süreden sonra otomatik olarak silmek isteyebilirsiniz, böylece gereksiz maliyetlere tabi kalmazsınız. PowerShell kullanarak, örnek [PowerShell betiği](#powershell-script) bölümünde gösterildiği gibi, bir VM için bir sona erme tarihi ayarlayabilirsiniz.

Laboratuvardaki tüm mevcut VM 'Ler için sona erme tarihi ayarlayan örnek bir PowerShell betiği aşağıda verilmiştir:

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki içeriğe bakın: [Azure DevTest Labs için Azure PowerShell belgeleri](/powershell/module/az.devtestlabs/)
