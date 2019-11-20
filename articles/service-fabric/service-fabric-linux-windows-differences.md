---
title: Linux ve Windows arasındaki Azure Service Fabric farklılıkları | Microsoft Docs
description: Linux üzerinde Azure Service Fabric ve Windows üzerinde Azure Service Fabric arasındaki farklar.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 0010108a7f3f9341d28c0b2f146c2e2f421ce47d
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170578"
---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Linux ve Windows üzerinde Service Fabric arasındaki farklar

Windows üzerinde desteklenen ancak henüz Linux üzerinde olmayan bazı özellikler vardır. Sonuç olarak, özellik kümeleri eşlik edilir ve her sürümde bu özellik boşluğu küçülür. Kullanılabilir en son sürümler arasında aşağıdaki farklar vardır.

* Envoy (ters proxy) Linux 'ta önizlemededir
* Linux için tek başına yükleyici, Linux üzerinde henüz kullanılamaz
* Konsol yeniden yönlendirme (Linux veya Windows üretim kümelerinde desteklenmez)
* Linux üzerinde hata analizi hizmeti (FAS)
* Service Fabric Services için DNS hizmeti (Linux üzerinde kapsayıcılar için DNS hizmeti desteklenir)
* Belirli PowerShell komutlarının CLı komutu eşdeğerleri (çoğunlukla tek başına kümeler için geçerlidir)
* [Günlük uygulamasındaki ölçeklenebilirliği etkileyebilecek farklılıklar](service-fabric-concepts-scalability.md#choosing-a-platform)

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Linux Service Fabric kümesinde çalışmayan PowerShell cmdlet 'leri

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-Servicefabricpartitionquorumkaybetme
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-Servicefabricımatorecontent
* Get-ServiceFabricChaosReport
* Get-Servicefabricnodeınlationprogress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-Servicefabricpartitionquorumkaybetme
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Onayla-ServiceFabricRepairTask
* Tam-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>Sonraki adımlar
* [Linux üzerinde geliştirme ortamınızı hazırlama](service-fabric-get-started-linux.md)
* [OSX üzerinde geliştirme ortamınızı hazırlama](service-fabric-get-started-mac.md)
* [Yeumman kullanarak Linux üzerinde ilk Service Fabric Java uygulamanızı oluşturma ve dağıtma](service-fabric-create-your-first-linux-application-with-java.md)
* [Service Fabric Çakışan Küreler Service Fabric eklentisini kullanarak Linux üzerinde ilk Java uygulamanızı oluşturma ve dağıtma](service-fabric-get-started-eclipse.md)
* [Linux üzerinde ilk CSharp uygulamanızı oluşturma](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Uygulamalarınızı yönetmek için Service Fabric CLı kullanın](service-fabric-application-lifecycle-sfctl.md)
