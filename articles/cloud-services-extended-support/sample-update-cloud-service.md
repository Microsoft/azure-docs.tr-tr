---
title: Azure PowerShell örnekleri - Azure Cloud Service'i (genişletilmiş destek) güncelleştirme
description: Azure bulut hizmeti (genişletilmiş destek) dağıtımlarını güncelleştirmek için örnek betikler
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: fcb92ec3aa8f360107f0e7220415c57344c6d83a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98881477"
---
# <a name="update-an-azure-cloud-service-extended-support"></a>Azure Cloud Service'i (genişletilmiş destek) güncelleştirme

Bu örnekler, var olan bir Azure bulut hizmeti (genişletilmiş destek) dağıtımını güncelleştirmenin çeşitli yollarını kapsar.

## <a name="add-an-extension-to-existing-cloud-service"></a>Mevcut bulut hizmetine uzantı ekleme
```powershell
# Create RDP extension object
$rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name "RDPExtension" -Credential $credential -Expiration $expiration -TypeHandlerVersion "1.2.1"
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Add RDP extension to existing Cloud Service extension object
$cloudService.ExtensionProfileExtension = $cloudService.ExtensionProfileExtension + $rdpExtension
# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="remove-all-extensions-from-a-cloud-service"></a>Bulut hizmetinden tüm uzantıları kaldırma
```powershell
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Set extension to empty list
$cloudService.ExtensionProfileExtension = @()
# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="remove-the-remote-desktop-extension-from-cloud-service"></a>Uzak Masaüstü uzantısını bulut hizmetinden kaldır
```powershell
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"
# Remove extension by name RDPExtension
$cloudService.ExtensionProfileExtension = $cloudService.ExtensionProfileExtension | Where-Object { $_.Name -ne "RDPExtension" }
# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="scale-out--scale-in-role-instances"></a>Genişleme/ölçek genişletme rol örnekleri
```powershell
# Get existing Cloud Service
$cloudService = Get-AzCloudService -ResourceGroup "ContosOrg" -CloudServiceName "ContosoCS"

# Scale-out all role instance count by 1
$cloudService.RoleProfileRole | ForEach-Object {$_.SkuCapacity += 1}

# Scale-in ContosoFrontend role instance count by 1
$role = $cloudService.RoleProfileRole | Where-Object {$_.Name -eq "ContosoFrontend"}
 $role.SkuCapacity -= 1

# Update Cloud Service configuration as per the new role instance count
$cloudService.Configuration = $configuration

# Update Cloud Service
$cloudService | Update-AzCloudService
```

## <a name="next-steps"></a>Sonraki adımlar
Azure Cloud Services (genişletilmiş destek) hakkında daha fazla bilgi için bkz. [azure Cloud Services (genişletilmiş destek) genel bakış](overview.md).
- [Cloud Services (genişletilmiş destek) örnekleri deposunu](https://github.com/Azure-Samples/cloud-services-extended-support) ziyaret edin