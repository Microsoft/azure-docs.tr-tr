---
title: PowerShell 'de bir Service Fabric uygulamasını yükseltme
description: Azure PowerShell betik örneği-PowerShell kullanarak bir Azure Service Fabric uygulamasını yükseltme ve izleme.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 7b55dc6a400f936ac23b233e4c84a6b1aebf45cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784705"
---
# <a name="upgrade-a-service-fabric-application"></a>Service Fabric uygulamasını yükseltme

Bu örnek betik, çalışan bir Service Fabric uygulama örneğini 1.3.0 sürümüne yükseltir. Betik yeni uygulama paketini küme görüntü deposuna kopyalar, uygulama türünü kaydeder ve gereksiz uygulama paketini kaldırır.  Betik izlenen bir yükseltme başlatır ve yükseltme tamamlanana veya geri alınana kadar yükseltme durumunu sürekli olarak denetler. Parametreleri gereken şekilde özelleştirin. 

Gerekirse, [Service Fabric SDK’sı](../service-fabric-get-started.md) ile Service Fabric PowerShell modülünü yükleyin. 

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) | Service Fabric kümesindeki tüm uygulamaları veya belirli bir uygulamayı alır.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade) | Service Fabric uygulama yükseltmesinin durumunu alır. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) | Service Fabric kümesinde kayıtlı Service Fabric uygulama türlerini alır. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) | Service Fabric uygulama türünün kaydını siler.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) | Bir Service Fabric uygulama paketini görüntü deposuna kopyalar.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) | Service Fabric uygulama türünü kaydeder. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) | Service Fabric bir uygulamayı belirtilen uygulama türü sürümüne yükseltir. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage) | Service Fabric uygulama paketini görüntü deposundan kaldırır.|


## <a name="next-steps"></a>Sonraki adımlar

Service Fabric PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/service-fabric/overview).

Azure Service Fabric için ek Powershell örneklerine [Azure PowerShell örnekleri](../service-fabric-powershell-samples.md) içinden erişilebilir.
