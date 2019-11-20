---
title: Windows 'da Azure Service Fabric hizmetlerinizi kapsayıkatın
description: Windows 'da Service Fabric Reliable Services ve Reliable Actors hizmetlerinizi nasıl kapsayıleyeceğinizi öğrenin.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: anmolah
editor: roroutra
ms.assetid: 0b41efb3-4063-4600-89f5-b077ea81fa3a
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/23/2018
ms.author: anmola
ms.openlocfilehash: 0cb48a2272ce854005f9f3db5b6a9abf62cc7015
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599195"
---
# <a name="containerize-your-service-fabric-reliable-services-and-reliable-actors-on-windows"></a>Windows üzerinde Service Fabric Reliable Services ve Reliable Actors kapsayıcı

Service Fabric, mikro hizmetler Service Fabric (Reliable Services ve güvenilir aktör tabanlı hizmetler) Kapsayıcılı hizmetleri destekler. Daha fazla bilgi için bkz. [Service Fabric kapsayıcıları](service-fabric-containers-overview.md).

Bu belge, hizmetinizi bir Windows kapsayıcısı içinde çalıştırmaya yönelik rehberlik sağlar.

> [!NOTE]
> Şu anda bu özellik yalnızca Windows için geçerlidir. Kapsayıcıları çalıştırmak için kümenin Windows Server 2016 kapsayıcılarla çalışıyor olması gerekir.

## <a name="steps-to-containerize-your-service-fabric-application"></a>Service Fabric uygulamanızı Kapsayıcılı hale getirme adımları

1. Service Fabric uygulamanızı Visual Studio 'da açın.

2. [SFBinaryLoader.cs](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/code/SFBinaryLoaderForContainers/SFBinaryLoader.cs) sınıfını projenize ekleyin. Bu sınıftaki kod, bir kapsayıcı içinde çalışırken uygulamanızın içinde Service Fabric çalışma zamanı ikililerini doğru bir şekilde yüklemek için yardımcı olur.

3. Kapsayıcılı yapmak istediğiniz her kod paketi için program giriş noktasında Yükleyiciyi başlatın. Aşağıdaki kod parçacığında gösterilen statik oluşturucuyu program giriş noktası dosyanıza ekleyin.

   ```csharp
   namespace MyApplication
   {
      internal static class Program
      {
          static Program()
          {
              SFBinaryLoader.Initialize();
          }

          /// <summary>
          /// This is the entry point of the service host process.
          /// </summary>
          private static void Main()
          {
   ```

4. Projenizi derleyin ve [paketleyin](service-fabric-package-apps.md#Package-App) . Bir paket oluşturup oluşturmak için Çözüm Gezgini ' de uygulama projesine sağ tıklayın ve **paket** komutunu seçin.

5. Kapsayıcılı yapmanız gereken her kod paketi için [Createdockerpackage. ps1](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/scripts/CodePackageToDockerPackage/CreateDockerPackage.ps1)PowerShell betiğini çalıştırın. Kullanım aşağıdaki gibidir:

    Tam .NET
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $applicationExeName = 'Name of the Code package executable.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -ApplicationExeName $applicationExeName
      ```
    .NET Core
      ```powershell
        $codePackagePath = 'Path to the code package to containerize.'
        $dockerPackageOutputDirectoryPath = 'Output path for the generated docker folder.'
        $dotnetCoreDllName = 'Name of the Code package dotnet Core Dll.'
        CreateDockerPackage.ps1 -CodePackageDirectoryPath $codePackagePath -DockerPackageOutputDirectoryPath $dockerPackageOutputDirectoryPath -DotnetCoreDllName $dotnetCoreDllName
      ```
      Betik, $dockerPackageOutputDirectoryPath 'da Docker yapıtlarına sahip bir klasör oluşturur. Oluşturulan dockerfile 'ı herhangi bir `expose` bağlantı noktası olarak değiştirin, kurulum betiklerini çalıştırın ve bu şekilde devam edin. gereksinimlerinize göre.

6. Sonraki adımda, Docker kapsayıcı [](service-fabric-get-started-containers.md#Push-Containers) paketinizi [derleyip](service-fabric-get-started-containers.md#Build-Containers) deponuza göndermeniz gerekir.

7. Container Image, depo bilgileriniz, kayıt defteri kimlik doğrulaması ve bağlantı noktası arasında eşleme eklemek için ApplicationManifest. xml ve ServiceManifest. xml dosyasını değiştirin. Bildirimleri değiştirmek için bkz. [Azure Service Fabric kapsayıcı uygulaması oluşturma](service-fabric-get-started-containers.md). Hizmet bildirimindeki kod paketi tanımının karşılık gelen kapsayıcı görüntüsüyle değiştirilmeleri gerekir. Giriş noktasını ContainerHost türüne değiştirdiğinizden emin olun.

   ```xml
   <!-- Code package is your service executable. -->
   <CodePackage Name="Code" Version="1.0.0">
   <EntryPoint>
    <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
    <ContainerHost>
      <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
    </ContainerHost>
   </EntryPoint>
   <!-- Pass environment variables to your container: -->
   </CodePackage>
   ```

8. Çoğaltıcınıza ve hizmet uç noktanıza yönelik bağlantı noktasından konağa eşleme ekleyin. Bu bağlantı noktalarının her ikisi de Service Fabric tarafından çalışma zamanında atandıklarından, eşleme için atanmış bağlantı noktasını kullanmak üzere ContainerPort sıfır olarak ayarlanır.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="0" EndpointRef="ServiceEndpoint"/>
    <PortBinding ContainerPort="0" EndpointRef="ReplicatorEndpoint"/>
   </ContainerHostPolicies>
   </Policies>
   ```

9. Kapsayıcı yalıtımı modunu yapılandırma için bkz. [yalıtım modunu yapılandırma]( https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-containers#configure-isolation-mode). Windows, kapsayıcılar için iki yalıtım modunu destekler: İşlem ve Hyper-V. Aşağıdaki kod parçacıklarında, uygulama bildirim dosyasında yalıtım modunun nasıl belirtildiği gösterilmektedir.

   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```
   ```xml
   <Policies>
   <ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
   ...
   </ContainerHostPolicies>
   </Policies>
   ```

> [!NOTE] 
> Varsayılan olarak Service Fabric uygulamalar, uygulamaya özgü istekleri kabul eden bir uç nokta biçiminde Service Fabric çalışma zamanına erişebilir. Uygulama güvenilmeyen kod barındırdığınızda bu erişimi devre dışı bırakmayı düşünün. Daha fazla bilgi için lütfen [Service Fabric en iyi güvenlik uygulamaları](service-fabric-best-practices-security.md#platform-isolation)bölümüne bakın. Service Fabric çalışma zamanına erişimi devre dışı bırakmak için aşağıdaki şekilde, içeri aktarılan hizmet bildirimine karşılık gelen uygulama bildiriminin Ilkeler bölümüne aşağıdaki ayarı ekleyin:
>
```xml
  <Policies>
      <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
  </Policies>
```
>

10. Bu uygulamayı test etmek için, sürüm 5,7 veya üstünü çalıştıran bir kümeye dağıtmanız gerekir. 6,1 veya daha düşük bir çalışma zamanı sürümü için bu önizleme özelliğini etkinleştirmek üzere küme ayarlarını düzenleyip güncelleştirmeniz gerekir. Daha sonra gösterilen ayarı eklemek için bu [makaledeki](service-fabric-cluster-fabric-settings.md) adımları izleyin.
    ```
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "FabricContainerAppsEnabled",
            "value": "true"
          }
        ]
      }
    ```

11. Daha sonra düzenlenen uygulama paketini bu kümeye [dağıtın](service-fabric-deploy-remove-applications.md) .

Artık kümenizi çalıştıran bir Kapsayıcılı Service Fabric uygulamanız olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar
* [Service Fabric’te kapsayıcı](service-fabric-get-started-containers.md) çalıştırma hakkında daha fazla bilgi edinin.
* Service Fabric [uygulama yaşam döngüsü](service-fabric-application-lifecycle.md) hakkında bilgi edinin.
