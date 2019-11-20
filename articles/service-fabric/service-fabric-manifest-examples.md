---
title: Azure Service Fabric uygulama bildirimi örnekleri
description: Service Fabric bir uygulama için uygulama ve hizmet bildirimi ayarlarını yapılandırmayı öğrenin.
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 969f15ecb45164e3b3f26c22f756a022e55782ed
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013187"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Service Fabric uygulama ve hizmet bildirimi örnekleri
Bu bölüm, uygulama ve hizmet bildirimlerinin örneklerini içerir. Bu örneklerde önemli senaryolar gösterilmemelidir, ancak kullanılabilir farklı ayarlar ve bunların nasıl kullanılacağı gösterilir. 

Aşağıda, gösterilen özelliklerin ve bir parçası oldukları örnek bildirimlerin bir dizini verilmiştir.

|Özellik|Bildirim|
|---|---|
|[Kaynak idaresi](service-fabric-resource-governance.md)|[Uygulama bildirimi Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [kapsayıcı uygulama bildirimi](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Bir hizmeti yerel yönetici hesabı olarak çalıştırma](service-fabric-application-runas-security.md)|[Uygulama bildirimini Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Tüm hizmet kodu paketlerine varsayılan ilke uygulama](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Uygulama bildirimini Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Kullanıcı ve grup sorumluları oluşturma](service-fabric-application-runas-security.md)|[Uygulama bildirimini Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|hizmet örnekleri arasında bir veri paketi paylaşma|[Uygulama bildirimini Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Hizmet uç noktalarını geçersiz kıl](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Uygulama bildirimini Reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Hizmet başlangıcında Betik Çalıştır](service-fabric-run-script-at-service-startup.md)|[VotingWeb hizmeti bildirimi](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[HTTPS uç noktası tanımlama](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[VotingWeb hizmeti bildirimi](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Bir yapılandırma paketi bildirin](service-fabric-application-and-service-manifests.md)|[VotingData hizmeti bildirimi](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Veri paketi bildirme](service-fabric-application-and-service-manifests.md)|[VotingData hizmeti bildirimi](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Ortam değişkenlerini geçersiz kıl](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Kapsayıcı uygulama bildirimi](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Kapsayıcı bağlantı noktasından konağa eşlemeyi yapılandırma](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Kapsayıcı uygulama bildirimi](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Kapsayıcı kayıt defteri kimlik doğrulamasını yapılandırma](service-fabric-get-started-containers.md#configure-container-repository-authentication)|[Kapsayıcı uygulama bildirimi](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Yalıtım modunu ayarla](service-fabric-get-started-containers.md#configure-isolation-mode)|[Kapsayıcı uygulama bildirimi](service-fabric-manifest-example-container-app.md#application-manifest)|
|[İşletim sistemi yapıya özgü kapsayıcı görüntülerini belirtme](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Kapsayıcı uygulama bildirimi](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Ortam değişkenlerini ayarlama](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Kapsayıcı FrontEndService hizmet bildirimi](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [kapsayıcı BackendService hizmeti bildirimi](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Uç nokta yapılandırma](service-fabric-get-started-containers.md#configure-communication)|[Kapsayıcı FrontEndService hizmet bildirimi](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [kapsayıcı BackendService hizmet bildirimi](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [votingdata hizmeti bildirimi](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|komutları kapsayıcıya geçirme|[Kapsayıcı FrontEndService hizmeti bildirimi](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Bir sertifikayı kapsayıcıya aktarma](service-fabric-securing-containers.md)|[Kapsayıcı FrontEndService hizmeti bildirimi](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Birim sürücüsünü yapılandırma](service-fabric-containers-volume-logging-drivers.md)|[Kapsayıcı BackEndService bildirimi](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

