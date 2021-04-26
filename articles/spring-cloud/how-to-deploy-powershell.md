---
title: Azure PowerShell ile Azure yay bulutu nasıl dağıtılır
description: Azure PowerShell ile Azure yay bulutu nasıl dağıtılır
author: bmitchell287
ms.author: brendm
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cb320a37818084f2fbcad22a3cc992655b19c3d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878745"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>Azure PowerShell ile Azure Spring Cloud dağıtma

Bu makalede [az. SpringCloud](/powershell/module/Az.SpringCloud) PowerShell modülünü kullanarak Azure Spring Cloud 'ın bir örneğini nasıl oluşturabileceğiniz açıklanmaktadır.

## <a name="requirements"></a>Gereksinimler

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > **Az. SpringCloud** PowerShell modülü önizlemedeyken, cmdlet 'ini kullanarak ayrı olarak yüklemelisiniz `Install-Module` . Bu PowerShell modülü genel kullanıma sunulduktan sonra, gelecekteki Az PowerShell modülü sürümlerinin bir parçası haline gelecek ve Azure Cloud Shell içinden varsayılan olarak sağlanacaktır.

  ```azurepowershell-interactive
  Install-Module -Name Az.SpringCloud
  ```

* Birden çok Azure aboneliğiniz varsa, kaynakların faturalandırılması gereken uygun aboneliği seçin. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 'ini kullanarak belirli bir abonelik seçin.

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 'ini kullanarak bir [Azure Kaynak grubu](../azure-resource-manager/management/overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek, belirtilen ada ve belirtilen konuma sahip bir kaynak grubu oluşturur.

```azurepowershell-interactive
New-AzResourceGroup -Name <resource group name> -Location eastus
```

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>Azure yay bulutu 'nın yeni bir örneğini sağlayın

Azure Spring Cloud 'ın yeni bir örneğini oluşturmak için [New-AzSpringCloud](/powershell/module/az.springcloud/new-azspringcloud) cmdlet 'ini kullanırsınız. Aşağıdaki örnek, daha önce oluşturulan kaynak grubunda belirtilen ada sahip bir Azure yay bulut hizmeti oluşturur.

```azurepowershell-interactive
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location eastus
```

## <a name="create-a-new-azure-spring-cloud-app"></a>Yeni bir Azure yay bulutu uygulaması oluşturun

Yeni bir uygulama oluşturmak için [New-AzSpringCloudApp](/powershell/module/az.springcloud/new-azspringcloudapp) cmdlet 'ini kullanırsınız. Aşağıdaki örnek adlı bir Azure Spring Cloud uygulaması oluşturur `gateway` .

```azurepowershell-interactive
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>Yeni bir Azure yay bulutu dağıtımı oluşturun

Yeni bir dağıtım oluşturmak için [New-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/new-azspringcloudappdeployment) cmdlet 'ini kullanırsınız. Aşağıdaki örnek, uygulama için adlı bir Azure yay bulutu dağıtımı oluşturur `default` `gateway` .

```azurepowershell-interactive
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>Azure yay bulut hizmeti edinme

Azure Spring bulut hizmetini ve özelliklerini almak için [Get-AzSpringCloud](/powershell/module/az.springcloud/get-azspringcloud) cmdlet 'ini kullanırsınız. Aşağıdaki örnek, belirtilen Azure yay bulutu hizmeti hakkındaki bilgileri alır.

```azurepowershell-interactive
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>Azure yay bulut uygulaması edinme

Azure Spring Cloud uygulamasını ve özelliklerini almak için [Get-AzSpringCloudApp](/powershell/module/az.springcloud/get-azspringcloudapp) cmdlet 'ini kullanırsınız. Aşağıdaki örnek `gateway` yay bulutu uygulaması hakkındaki bilgileri alır.

```azurepowershell-interactive
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>Azure yay bulutu dağıtımı edinme

Azure Spring bulut dağıtımını ve özelliklerini almak için [Get-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/get-azspringcloudappdeployment) cmdlet 'ini kullanırsınız. Aşağıdaki örnek `default` yay bulutu dağıtımı hakkında bilgi alır.

```azurepowershell-interactive
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede oluşturulan kaynaklar gerekmiyorsa, aşağıdaki örnekleri çalıştırarak bunları silebilirsiniz.

### <a name="delete-an-azure-spring-cloud-deployment"></a>Azure yay bulutu dağıtımını silme

Azure yay bulutu dağıtımını kaldırmak için [Remove-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/remove-azspringcloudappdeployment) cmdlet 'ini kullanırsınız. Aşağıdaki örnek, `default` belirtilen hizmet ve uygulama için adlı bir Azure yay bulutu uygulaması dağıtımını siler.

```azurepowershell-interactive
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>Azure Spring Cloud uygulamasını silme

Bir yay bulutu uygulamasını kaldırmak için [Remove-AzSpringCloudApp](/powershell/module/Az.SpringCloud/remove-azspringcloudapp) cmdlet 'ini kullanırsınız. Aşağıdaki örnek, `gateway` belirtilen hizmet ve kaynak grubundaki uygulamayı siler.

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>Azure yay bulut hizmetini silme

Azure Spring Cloud Service 'i kaldırmak için [Remove-AzSpringCloud](/powershell/module/Az.SpringCloud/remove-azspringcloud) cmdlet 'ini kullanırsınız. Aşağıdaki örnek, belirtilen Azure Spring Cloud Service 'i siler.

```azurepowershell
Remove-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

### <a name="delete-the-resource-group"></a>Kaynak grubunu silme

> [!CAUTION]
> Aşağıdaki örnek, belirtilen kaynak grubunu ve içinde yer alan tüm kaynakları siler.
> Bu makalenin kapsamı dışındaki kaynaklar belirtilen kaynak grubunda mevcutsa, bunlar da silinir.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource group name>
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure yay bulut geliştirici kaynakları](spring-cloud-resources.md).
