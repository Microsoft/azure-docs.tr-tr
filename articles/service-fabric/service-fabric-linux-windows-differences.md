---
title: "Linux ile Windows arasındaki Azure Service Fabric farkları | Microsoft Docs"
description: "Linux üzerindeki Azure Service Fabric Önizlemesi ile Windows üzerindeki Azure Service Fabric arasındaki farklar."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 5faf7dc0b544f6fe2f83565cc368e218c6df35af
ms.lasthandoff: 03/28/2017


---
# <a name="differences-between-service-fabric-on-linux-preview-and-windows-generally-available"></a>Linux (önizleme) ve Windows (genel kullanıma açık) üzerindeki Service Fabric arasındaki farklar

Linux üzerindeki Service Fabric önizleme aşamasında olduğundan, Windows’ta desteklenip Linux’ta desteklenmeyen bazı özellikler mevcuttur. Linux üzerindeki Service Fabric genel kullanıma sunulduğunda özellik kümeleri de eşit olacaktır.

* Güvenilir Koleksiyonlar (ve Güvenilir Durum Bilgisi Olan Hizmetler) Linux üzerinde desteklenmez.
* ReverseProxy, Linux üzerinde kullanılamaz.
* Tek başına yükleyici, Linux üzerinde kullanılamaz.
* Linux üzerinde bildirim dosyaları için XML şema doğrulaması yapılmaz. 
* Konsol yönlendirmesi, Linux üzerinde desteklenmez. 
* Hata Analizi Hizmeti (FAS), Linux üzerinde kullanılamaz.
* Azure Active Directory desteği, Linux üzerinde mevcut değildir.
* PowerShell komutlarının bazı CLI komutu eşdeğerleri mevcut değildir.
* Bir Linux kümesinde PowerShell komutlarının yalnızca bir alt kümesi çalıştırılabilir (sonraki bölümde ayrıntılı olarak verilmiştir).

>[!NOTE]
>Konsol yönlendirmesi, Windows’ta bile üretim kümelerinde desteklenmez.

Windows üzerinde kullanılan VisualStudio, PowerShell, VSTS ve ETW ile geliştirme araçları, Linux üzerinde kullanılan Yeoman, Eclipse, Jenkins ve LTTng’den farklıdır.

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Linux Service Fabric kümesinde çalışmayan PowerShell cmdlet'leri

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Cmd
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
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
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
* [Linux üzerinde Yeoman kullanarak ilk Service Fabric Java uygulamanızı oluşturma ve dağıtma](service-fabric-create-your-first-linux-application-with-java.md)
* [Linux üzerinde Eclipse için Service Fabric Eklentisi kullanarak ilk Service Fabric Java uygulamanızı oluşturma ve dağıtma](service-fabric-get-started-eclipse.md)
* [Linux üzerinde ilk CSharp uygulamanızı oluşturma](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Service Fabric uygulamalarınızı yönetmek için Azure CLI kullanma](service-fabric-azure-cli.md)

