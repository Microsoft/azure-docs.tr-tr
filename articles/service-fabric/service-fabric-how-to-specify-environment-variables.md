---
title: Azure Service Fabric Services için ortam değişkenlerini belirtme | Microsoft Docs
description: Service Fabric 'de uygulamalar için ortam değişkenlerinin nasıl kullanılacağını gösterir
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: df9b199c24301016b9f9da8a8dec52129bbf94bd
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703537"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Service Fabric içindeki hizmetler için ortam değişkenlerini belirtme

Bu makalede, Service Fabric bir hizmet veya kapsayıcı için ortam değişkenlerini nasıl belirtebileceğiniz gösterilmektedir.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>Hizmetler için ortam değişkenlerini belirtme yordamı

Bu örnekte, bir kapsayıcı için bir ortam değişkeni ayarlarsınız. Makalede, zaten bir uygulama ve hizmet bildiriminiz olduğunu varsaymaktadır.

1. ServiceManifest. xml dosyasını açın.
2. @No__t-0 öğesinde, her ortam değişkeni için yeni bir `EnvironmentVariables` öğesi ve bir `EnvironmentVariable` öğesi ekleyin.

    ```xml
    <CodePackage Name="MyCode" Version="CodeVersion1">
            ...
            <EnvironmentVariables>
                  <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
                  <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentVariables>
    </CodePackage>
    ```

   Ortam değişkenleri uygulama bildiriminde geçersiz kılınabilir.

3. Uygulama bildiriminde ortam değişkenlerini geçersiz kılmak için `EnvironmentOverrides` öğesini kullanın.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="specifying-environment-variables-dynamically-using-docker-compose"></a>Docker Compose kullanarak ortam değişkenlerini dinamik olarak belirtme

Service Fabric, [dağıtım için Docker Compose kullanma](service-fabric-docker-compose.md#supported-compose-directives)yeteneğini destekler. Oluşturma dosyaları, kabuktan ortam değişkenlerini kaynak olarak içerebilir. Bu davranış, istenen ortam değerlerini dinamik olarak değiştirmek için kullanılabilir:

```yml
environment:
  - "hostname:${hostname}"
```

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede ele alınan temel kavramlardan bazıları hakkında daha fazla bilgi edinmek için bkz. [birden çok ortam için uygulamaları yönetme makaleleri](service-fabric-manage-multiple-environment-app-configuration.md).

Visual Studio 'da kullanılabilen diğer uygulama yönetimi özellikleri hakkında daha fazla bilgi için bkz. [Visual Studio 'da Service Fabric uygulamalarınızı yönetme](service-fabric-manage-application-in-visual-studio.md).
