---
title: Yerel Azure Service Fabric kümesi kurulumlarınızın sorunlarını giderme
description: Bu makalede, yerel geliştirme kümenizin sorunlarını gidermeye yönelik bir dizi öneri ele alınmaktadır
ms.topic: conceptual
ms.date: 02/23/2018
ms.openlocfilehash: 20948cd1626c02d73fb6e9ef096b552bbab627fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575916"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Yerel geliştirme kümesi kurulumunuzda sorun giderme
Yerel Azure Service Fabric geliştirme kümeniz ile etkileşim kurarken bir sorunla karşılaşırsanız, olası çözümler için aşağıdaki önerileri gözden geçirin.

## <a name="cluster-setup-failures"></a>Küme kurulum sorunları
### <a name="cannot-clean-up-service-fabric-logs"></a>Service Fabric günlükleri temizlenemiyor
#### <a name="problem"></a>Sorun
DevClusterSetup betiğini çalıştırırken şu hatayı görürsünüz:

```output
Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
At line:1 char:1 + .\DevClusterSetup.ps1
+ ~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
+ FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1
```

#### <a name="solution"></a>Çözüm
Geçerli PowerShell penceresini kapatın ve yönetici olarak yeni bir PowerShell penceresi açın. Artık betiği başarıyla çalıştırabilirsiniz.

## <a name="cluster-connection-failures"></a>Küme bağlantısı sorunları

### <a name="type-initialization-exception"></a>Tür başlatma özel durumu
#### <a name="problem"></a>Sorun
PowerShell 'de kümeye bağlanırken, System. Fabric. Common. AppTrace için hata TypeInitializationException görürsünüz.

#### <a name="solution"></a>Çözüm
Yol değişkeniniz yükleme sırasında doğru şekilde ayarlanmadı. Windows oturumunu kapatın ve yeniden oturum açın. Bu, yolunuza yenilenir.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Küme bağlantısı "nesne kapalı" ile başarısız oluyor
#### <a name="problem"></a>Sorun
Connect-ServiceFabricCluster çağrısı aşağıdakine benzer bir hata ile başarısız olur:

```output
Connect-ServiceFabricCluster : The object is closed.
At line:1 char:1
+ Connect-ServiceFabricCluster
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
+ FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster
```

#### <a name="solution"></a>Çözüm
Geçerli PowerShell penceresini kapatın ve yönetici olarak yeni bir PowerShell penceresi açın.

### <a name="fabric-connection-denied-exception"></a>Fabric bağlantısı reddedildi özel durumu
#### <a name="problem"></a>Sorun
Visual Studio 'da hata ayıklarken, bir FabricConnectionDeniedException hatası alırsınız.

#### <a name="solution"></a>Çözüm
Bu hata genellikle bir hizmet ana bilgisayarı işlemini el ile başlatmaya çalıştığınızda oluşur.

Çözümünüzde başlangıç projeleri olarak ayarlanmış bir hizmet projesi olmadığından emin olun. Yalnızca Service Fabric uygulama projelerinin başlangıç projeleri olarak ayarlanması gerekir.

> [!TIP]
> Kurulum sonrasında, yerel kümeniz anormal davranmaya başlarsa, Yerel Küme Yöneticisi sistem tepsisi uygulamasını kullanarak sıfırlayabilirsiniz. Bu, var olan kümeyi kaldırır ve yenisini ayarlar. Dağıtılan tüm uygulamaların ve ilişkili verilerin kaldırıldığını unutmayın.
> 
> 

## <a name="next-steps"></a>Sonraki adımlar
* [Sistem durumu raporları ile kümenizi anlayın ve sorun giderin](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Service Fabric Explorer ile kümenizi görselleştirme](service-fabric-visualizing-your-cluster.md)

