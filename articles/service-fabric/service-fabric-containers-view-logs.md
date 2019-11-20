---
title: Azure Service Fabric kapsayıcılar günlüklerini görüntüleme | Microsoft Docs
description: Service Fabric Explorer kullanılarak çalışan bir Service Fabric kapsayıcı Hizmetleri için kapsayıcı günlüklerinin nasıl görüntüleneceğini açıklar.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: atsenthi
ms.openlocfilehash: fd1787318e8573183293ddd832a11cf8cfe09cf2
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832614"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Service Fabric kapsayıcı hizmeti için günlükleri görüntüleme
Azure Service Fabric bir kapsayıcı Orchestrator ve hem [Linux hem de Windows kapsayıcılarını](service-fabric-containers-overview.md)destekler.  Bu makalede, sorunları tanılamanıza ve giderebilmeniz için çalışan bir kapsayıcı hizmetinin veya ölü kapsayıcının kapsayıcı günlüklerinin nasıl görüntüleneceği açıklanır.

## <a name="access-the-logs-of-a-running-container"></a>Çalışan bir kapsayıcının günlüklerine erişin
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)kullanılarak kapsayıcı günlüklerine erişilebilir.  Bir Web tarayıcısında, `http://mycluster.region.cloudapp.azure.com:19080/Explorer`giderek kümenin yönetim uç noktasındaki Service Fabric Explorer açın.  

Kapsayıcı günlükleri, kapsayıcı hizmeti örneğinin üzerinde çalıştığı küme düğümünde bulunur. Örnek olarak, [Linux oylama örnek uygulamasının](service-fabric-quickstart-containers-linux.md)Web ön uç kapsayıcısının günlüklerini alın. Ağaç görünümünde **küme**>**uygulamalar**>**votingtype**>**Fabric:/oylama/azuiptal tefront**' ı genişletin.  Sonra bölümü genişletin (Bu örnekte d1aa737e-f22a-e347-be16-eec90be24bc1) ve kapsayıcının *_lnxvm_0*küme düğümünde çalıştığını görün.

Ağaç görünümünde *_lnxvm_0* **düğümünde kod paketini bulun>** **_Lnxvm_0**>**Fabric:/oylama**>**azuiptal tfrontpkg**>**kod paketleri** **>kodu.**  Ardından kapsayıcı günlüklerini göstermek için **kapsayıcı günlükleri** seçeneğini belirleyin.

![Service Fabric platformu][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Ölü veya kilitlenen kapsayıcının günlüklerine erişin
V 6.2 ' den başlayarak, [REST API 'leri](/rest/api/servicefabric/sfclient-index) veya [Service Fabric CLI (sfctl)](service-fabric-cli.md) komutlarını kullanarak ölü veya kilitlenen bir kapsayıcı için günlükleri de getirebilirsiniz.

### <a name="set-container-retention-policy"></a>Kapsayıcı bekletme ilkesi ayarlama
Service Fabric (6.1 veya üzeri sürümler), kapsayıcı başlatma hatalarının tanılanmasına yardımcı olmak için sonlandırılan veya başlatılamayan kapsayıcıların bekletilmesini destekler. Bu ilke, aşağıdaki kod parçacığında gösterildiği gibi **ApplicationManifest.xml** dosyasında ayarlanabilir:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

**ContainersRetentionCount** ayarı, başarısız olduğunda bekletilecek kapsayıcı sayısını belirtir. Negatif bir değer belirtilirse başarısız olan tüm kapsayıcılar bekletilir. **ContainersRetentionCount** özniteliği belirtilmediğinde hiçbir kapsayıcı korunmaz. **ContainersRetentionCount** özniteliği, kullanıcıların test ve üretim kümeleri için farklı değerler belirtebilmesi amacıyla Uygulama Parametrelerini destekler. Kapsayıcı hizmetinin diğer düğümlere taşınmasını önlemek için bu özellikler kullanılırken kapsayıcı hizmetinin belirli bir düğümü hedeflemesini sağlamak için yerleştirme kısıtlamaları kullanın. Bu özellik kullanılarak bekletilen tüm kapsayıcılar el ile kaldırılmalıdır.

**Runınteractıve** ayarı docker 'ın `--interactive` ve `tty` [bayraklarına](https://docs.docker.com/engine/reference/commandline/run/#options)karşılık gelir. Bu ayar bildirim dosyasında true olarak ayarlandığında, kapsayıcıyı başlatmak için bu bayraklar kullanılır.  

### <a name="rest"></a>REST
Kilitlenen bir kapsayıcının günlüklerini almak için [düğümde dağıtılan kapsayıcı günlüklerini al](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) işlemini kullanın. Kapsayıcının üzerinde çalıştığı düğümün adını, uygulama adını, hizmet bildirimi adını ve kod paketi adını belirtin.  `&Previous=true`belirtin. Yanıt, kod paketi örneğinin ölü kapsayıcısının kapsayıcı günlüklerini içerecektir.

İstek URI 'SI aşağıdaki biçime sahiptir:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Örnek istek:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 yanıt gövdesi:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Çökmeyle ilgili bir kapsayıcı için günlükleri getirmek üzere [sfctl hizmeti Get-Container-logs](service-fabric-sfctl-service.md) komutunu kullanın.  Kapsayıcının üzerinde çalıştığı düğümün adını, uygulama adını, hizmet bildirimi adını ve kod paketi adını belirtin. `--previous` bayrağını belirtin.  Yanıt, kod paketi örneğinin ölü kapsayıcısının kapsayıcı günlüklerini içerecektir.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Yanıt:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Sonraki adımlar
- [Linux kapsayıcı uygulaması oluşturma öğreticisi](service-fabric-tutorial-create-container-images.md)aracılığıyla çalışın.
- [Service Fabric ve kapsayıcılar](service-fabric-containers-overview.md) hakkında daha fazla bilgi edinin

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
