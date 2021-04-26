---
title: Azure PowerShell örnekleri-Azure Cloud Services sıfırlama (genişletilmiş destek)
description: Bir Azure bulut hizmeti (genişletilmiş destek) dağıtımını sıfırlamaya yönelik örnek betikler
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 5c43d61b1e7cd98674eab4c6d857cc1114a06013
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99475328"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Azure bulut hizmetini sıfırlama (genişletilmiş destek) 
Bu örnekler, var olan bir Azure bulut hizmeti (genişletilmiş destek) dağıtımını sıfırlamanın çeşitli yollarını kapsar.

## <a name="reimage-role-instances-of-cloud-service"></a>Bulut hizmetinin rol örnekleri yeniden görüntüsü
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Bu komut, ContosOrg adlı kaynak grubuna ait olan ContosoCS adlı bulut hizmetinin ContosoFrontEnd_IN_0 ve ContosoBackEnd_IN_1 2 rol örneğini yeniden görüntüler.

## <a name="reimage-all-roles-of-cloud-service"></a>Tüm bulut hizmeti rollerini yeniden görüntü oluştur
```powershell
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Bu komut, ContosOrg adlı kaynak grubuna ait olan ContosoCS adlı bulut hizmeti 'nin tüm rol örneklerini yeniden görüntüler.

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Bulut hizmetinin tek bir rol örneğini yeniden görüntü oluşturma
```powershell
Invoke-AzCloudServiceRoleInstanceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0"
```
Bu komut, ContosOrg adlı kaynak grubuna ait olan ContosoCS adlı bulut hizmetinin ContosoFrontEnd_IN_0 adlı rol örneğini yeniden görüntüler.

## <a name="rebuild-role-instances-of-cloud-service"></a>Bulut hizmetinin rol örneklerini yeniden oluştur
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Bu komut, ContosOrg adlı kaynak grubuna ait ContosoCS adlı bulut hizmetinin ContosoFrontEnd_IN_0 ve ContosoBackEnd_IN_1 2 rol örneğini yeniden oluşturur.

## <a name="rebuild-all-roles-of-cloud-service"></a>Tüm bulut hizmeti rollerini yeniden derle
```powershell
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Bu komut, ContosOrg adlı kaynak grubuna ait ContosoCS adlı bulut hizmeti 'nin tüm rol örneklerini yeniden oluşturur.

## <a name="restart-role-instances-of-cloud-service"></a>Bulut hizmetinin rol örneklerini yeniden Başlat
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Bu komut, ContosOrg adlı kaynak grubuna ait ContosoCS adlı bulut hizmetinin ContosoFrontEnd_IN_0 ve ContosoBackEnd_IN_1 2 rol örneğini yeniden başlatır.

## <a name="restart-all-roles-of-cloud-service"></a>Tüm bulut hizmeti rollerini yeniden Başlat
```powershell
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Bu komut, ContosOrg adlı kaynak grubuna ait ContosoCS adlı bulut hizmeti 'nin tüm rol örneklerini yeniden başlatır.

## <a name="next-steps"></a>Sonraki adımlar

- Azure Cloud Services (genişletilmiş destek) hakkında daha fazla bilgi için bkz. [azure Cloud Services (genişletilmiş destek) genel bakış](overview.md).
- [Cloud Services (genişletilmiş destek) örnekleri deposunu](https://github.com/Azure-Samples/cloud-services-extended-support) ziyaret edin
